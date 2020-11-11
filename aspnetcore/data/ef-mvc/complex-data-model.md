---
title: 'Tutorial: Erstellen eines komplexen Datenmodells: ASP.NET MVC mit EF Core'
description: In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie Regeln zur Formatierung, Validierung und Zuordnung angeben.
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
uid: data/ef-mvc/complex-data-model
ms.openlocfilehash: cee9e9eb4c5435f3f63f7d1d04f131d88effe9f6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054475"
---
# <a name="tutorial-create-a-complex-data-model---aspnet-mvc-with-ef-core"></a>Tutorial: Erstellen eines komplexen Datenmodells: ASP.NET MVC mit EF Core

In den vorherigen Tutorials haben Sie mit einem einfachen Datenmodell gearbeitet, das aus drei Entitäten bestand. In diesem Tutorial fügen Sie weitere Entitäten und Beziehungen hinzu und passen das Datenmodell an, indem Sie die Regeln zur Formatierung, Validierung und Datenbankzuordnung angeben.

Wenn Sie dies erledigt haben, bilden die Entitätsklassen ein vollständiges Datenmodell, das in der folgenden Abbildung dargestellt wird:

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Anpassen des Datenmodells
> * Ändern der Entität „Student“
> * Erstellen der Entität „Instructor“
> * Erstellen der Entität „OfficeAssignment“
> * Ändern der Entität „Course“
> * Erstellen der Entität „Department“
> * Ändern der Entität „Enrollment“
> * Aktualisieren des Datenbankkontexts
> * Füllen der Datenbank mit Testdaten
> * Hinzufügen einer Migration
> * Ändern der Verbindungszeichenfolge
> * Aktualisieren der Datenbank

## <a name="prerequisites"></a>Voraussetzungen

* [Verwenden von EF Core-Migrationen](migrations.md)

## <a name="customize-the-data-model"></a>Anpassen des Datenmodells

In diesem Abschnitt wird das Anpassen des Datenmodells mithilfe von Attributen erläutert, die Regeln zur Formatierung, Validierung und Datenbankzuordnung angeben. In den folgenden Abschnitten erstellen Sie dann das vollständige Datenmodell „School“, indem Sie Attribute zu den bereits erstellten Klassen hinzufügen und neue Klassen für die verbleibenden Entitätstypen im Modell erstellen.

### <a name="the-datatype-attribute"></a>Das DataType-Attribut

Für die Anmeldedaten von Studenten zeigen alle Webseiten derzeit die Zeit und das Datum an, obwohl für dieses Feld nur das Datum relevant ist. Indem Sie Attribute für die Datenanmerkung verwenden, können Sie eine Codeänderungen vornehmen, durch die das Anzeigeformat in jeder Ansicht korrigiert wird, in der die Daten angezeigt werden. Sie können dies beispielhaft testen, indem Sie ein Attribut zur `EnrollmentDate`-Eigenschaft der `Student`-Klasse hinzufügen.

Fügen Sie in *Models/Student.cs* eine `using`-Anweisung für den `System.ComponentModel.DataAnnotations`-Namespace hinzu, und fügen Sie wie im folgenden Beispiel dargestellt die Attribute `DataType` und `DisplayFormat` zur `EnrollmentDate`-Eigenschaft hinzu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_DataType&highlight=3,12-13)]

Das `DataType`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist. In diesem Fall soll nur das Datum verfolgt werden, nicht das Datum und die Zeit. Die `DataType`-Enumeration stellt viele Datentypen bereit, z.B. „Date“, „Time“, „PhoneNumber“, „Currency“, „EmailAddress“ usw. Das `DataType`-Attribut kann der Anwendung auch ermöglichen, typspezifische Features bereitzustellen. Beispielsweise kann ein `mailto:`-Link für `DataType.EmailAddress` erstellt werden, und eine Datumsauswahl kann in Browsern mit Unterstützung für HTML5 für `DataType.Date` bereitgestellt werden. Das `DataType`-Attribut gibt `data-`-Attribute (ausgesprochen „Datadash“) von HTML5 aus, die von HTML5-Browsern genutzt werden können. Die `DataType`-Attribute bieten keine Validierung.

`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird. Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf der CultureInfo-Klasse des Servers angezeigt.

Das `DisplayFormat`-Attribut dient zum expliziten Angeben des Datumsformats:

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung auch angewendet werden soll, wenn der Wert in einem Textfeld zur Bearbeitung angezeigt wird. (Dies könnte für einige Felder nützlich sein, z.B. soll für Währungsangaben das Währungssymbol nicht im Textfeld für die Bearbeitung enthalten sein.)

Sie können das `DisplayFormat`-Attribut eigenständig verwenden, meistens empfiehlt es sich jedoch, ebenfalls das `DataType`-Attribut zu verwenden. Das `DataType`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm. Es bietet folgende Vorteile, die `DisplayFormat` nicht bietet:

* Der Browser kann HTML5-Features aktivieren (z.B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links, einigen clientseitigen Eingabevalidierungen usw.).

* Standardmäßig rendert der Browser Daten mit dem ordnungsgemäßen auf Ihrem Gebietsschema basierenden Format.

Weitere Informationen finden Sie in der [Dokumentation zum \<input>-Taghilfsprogramm](../../mvc/views/working-with-forms.md#the-input-tag-helper).

Führen Sie die App aus, und wechseln Sie zur Indexseite „Studenten“, um festzustellen, dass beim Registrierungsdatum nicht mehr die Uhrzeit angezeigt wird. Dies gilt für jede Ansicht, die das Modell „Student“ verwendet.

![Indexseite „Studenten“, die Datumsangaben ohne Uhrzeit anzeigt](complex-data-model/_static/dates-no-times.png)

### <a name="the-stringlength-attribute"></a>Das StringLength-Attribut

Sie können ebenfalls Regeln für die Datenvalidierung und Meldungen für Validierungsfehler mithilfe von Attributen angeben. Das `StringLength`-Attribut legt die maximale Länge in der Datenbank fest und stellt die clientseitige und serverseitige Validierung für ASP.NET Core MVC bereit. Sie können ebenfalls die mindestens erforderliche Zeichenfolgenlänge in diesem Attribut angeben, dieser Wert hat jedoch keine Auswirkung auf das Datenbankschema.

Angenommen, Sie möchten sicherstellen, dass Benutzer nicht mehr als 50 Zeichen für einen Namen eingeben. Fügen Sie zum Hinzufügen dieser Einschränkung wie im folgenden Beispiel dargestellt `StringLength`-Attribute zu den `LastName`- und `FirstMidName`-Eigenschaften hinzu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_StringLength&highlight=10,12)]

Das `StringLength`-Attribut verhindert nicht, dass ein Benutzer einen Leerraum als Namen eingibt. Sie können das `RegularExpression`-Attribut verwenden, um Beschränkungen auf die Eingabe anzuwenden. Folgender Code erfordert beispielsweise, dass das erste Zeichen ein Großbuchstabe sein muss und die restlichen Zeichen alphabetisch sein müssen:

```csharp
[RegularExpression(@"^[A-Z]+[a-zA-Z]*$")]
```

Das `MaxLength`-Attribut stellt ähnliche Funktionalitäten wie das `StringLength`-Attribut bereit, ermöglicht jedoch keine clientseitige Validierung.

Das Datenbankmodell hat sich nun auf eine Weise geändert, die eine Änderung des Datenbankschemas erfordert. Verwenden Sie Migrationen, um das Schema ohne Verlust der Daten zu aktualisieren, die Sie mithilfe der Benutzeroberfläche der Anwendung zur Datenbank hinzugefügt haben.

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt. Öffnen Sie anschließend das Befehlsfenster im Projektordner, und geben Sie folgenden Befehl ein:

```dotnetcli
dotnet ef migrations add MaxLengthOnNames
```

```dotnetcli
dotnet ef database update
```

Der `migrations add`-Befehl gibt eine Warnung aus, dass es zu Datenverlust kommen kann, da die maximale Länge durch die Änderung um zwei Spalten verkürzt wurde.  Durch Migrationen wird eine Datei namens *\<timeStamp>_MaxLengthOnNames.cs* erstellt. Diese Datei enthält Code in der `Up`-Methode, durch den die Datenbank dem aktuellen Datenmodell entsprechend aktualisiert wird. Der Code wurde durch den `database update`-Befehl ausgeführt.

Der Zeitstempel, der dem Namen der Migrationsdatei vorangestellt ist, wird von Entity Framework verwendet, um die Migrationen zu sortieren. Sie können mehrere Migrationen erstellen, bevor Sie den Befehl „update-database“ ausführen. Anschließend werden alle Migrationen in der Reihenfolge angewendet, in der Sie erstellt wurden.

Führen Sie die App aus, wählen Sie die Registerkarte **Students** aus, klicken Sie dann auf **Neu erstellen** , und geben Sie einen beliebigen Namen ein, der länger als 50 Zeichen ist. Die Anwendung sollte Sie daran hindern. 

### <a name="the-column-attribute"></a>Das Column-Attribut

Sie können ebenfalls Attribute verwenden, um zu steuern, wie Ihre Klassen und Eigenschaften der Datenbank zugeordnet werden. Angenommen, Sie haben den Namen `FirstMidName` für das Feld „first-name“ verwendet, da das Feld ebenfalls einen Zweitnamen enthalten kann. Sie möchten jedoch, dass die Datenbankspalte in `FirstName` umbenannt wird, damit Benutzer, die Ad-hob-Abfragen für die Datenbank schreiben, an diesen Namen gewöhnt sind. Sie können das `Column`-Attribut verwenden, um diese Zuordnung durchzuführen.

Das `Column`-Attribut gibt an, dass bei der Erstellung der Datenbank die Spalte des `Student`-Elements, die der `FirstMidName`-Eigenschaft zugeordnet ist, den Namen `FirstName` erhält. Wenn Ihr Code also auf `Student.FirstMidName` verweist, stammen die Daten aus der `FirstName`-Spalte der `Student`-Tabelle oder werden in dieser aktualisiert. Wenn Sie keine Spaltennamen angeben, erhalten diese den gleichen Namen wie die Eigenschaft.

Fügen Sie in der Datei *Student.cs* eine `using`-Anweisung für `System.ComponentModel.DataAnnotations.Schema` hinzu, und fügen Sie wie im folgenden hervorgehobenen Code dargestellt das Attribut für den Spaltennamen zur `FirstMidName`-Eigenschaft hinzu:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Column&highlight=4,14)]

Durch das Hinzufügen des `Column`-Attributs wird das Modell geändert, das `SchoolContext` unterstützt, sodass keine Übereinstimmung mit der Datenbank vorliegt.

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt. Öffnen Sie anschließend das Befehlsfenster im Projektordner, und geben Sie folgenden Befehl ein, um eine weitere Migration zu erstellen:

```dotnetcli
dotnet ef migrations add ColumnFirstName
```

```dotnetcli
dotnet ef database update
```

Öffnen Sie im **SQL Server-Objekt-Explorer** den Tabellen-Designer „Student“, indem Sie auf die Tabelle **Student** doppelklicken.

![Tabelle „Students“ im SSOX nach der Migration](complex-data-model/_static/ssox-after-migration.png)

Bevor Sie die ersten beiden Migrationen angewendet haben, wiesen die Namensspalten den Typ „nvarchar(MAX)“ auf. Diese besitzen nun den Typ „nvarchar(50)“, und der Spaltenname hat sich von „FirstMidName“ in „FirstName“ geändert.

> [!Note]
> Wenn Sie vor dem Erstellen aller Entitätsklassen in den folgenden Abschnitten versuchen, zu kompilieren, werden möglicherweise Compilerfehler angezeigt.

## <a name="changes-to-student-entity"></a>Ändern der Entität „Student“

![Entität „Student“](complex-data-model/_static/student-entity.png)

Ersetzen Sie in *Models/Student.cs* den Code, den Sie zuvor hinzugefügt haben, durch folgenden Code. Die Änderungen werden hervorgehoben.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_BeforeInheritance&highlight=11,13,15,18,22,24-31)]

### <a name="the-required-attribute"></a>Das Attribut „Required“

Durch das `Required`-Attribut werden die Name-Eigenschaften zu Pflichtfeldern. Das `Required`-Attribut ist für nicht auf NULL festlegbare Typen wie Werttypen (DateTime, int, double, float usw.) nicht erforderlich. Typen, die nicht auf NULL festgelegt werden können, werden automatisch als Pflichtfelder behandelt.

Das `Required`-Attribut muss mit `MinimumLength` verwendet werden, damit die `MinimumLength` erzwungen wird.

```csharp
[Display(Name = "Last Name")]
[Required]
[StringLength(50, MinimumLength=2)]
public string LastName { get; set; }
```

### <a name="the-display-attribute"></a>Das Display-Attribut

Das `Display`-Attribut gibt an, dass die Beschriftung der Textfelder in jeder Instanz „First Name“, „Last Name“, „Full Name“ und „Enrollment Date“ lauten soll, statt dem Eigenschaftsnamen zu entsprechen (bei dem die Wörter nicht durch Leerräume getrennt werden).

### <a name="the-fullname-calculated-property"></a>Die berechnete FullName-Eigenschaft

Bei `FullName` handelt es sich um eine berechnete Eigenschaft, die einen Wert zurückgibt, der durch das Verketten von zwei weiteren Eigenschaften erstellt wird. Daher verfügt diese nur über einen get-Accessor, und keine `FullName`-Spalte wird in der Datenbank generiert.

## <a name="create-instructor-entity"></a>Erstellen der Entität „Instructor“

![Instructor-Entität](complex-data-model/_static/instructor-entity.png)

Erstellen Sie *Models/Instructor.cs* , indem Sie den Vorlagencode durch folgenden Code ersetzen:

[!code-csharp[](intro/samples/cu/Models/Instructor.cs?name=snippet_BeforeInheritance)]

Beachten Sie, dass einige Eigenschaften in den Entitäten „Student“ und „Instructor“ identisch sind. Im folgenden Tutorial [Implementing Inheritance (Implementierung von Vererbung)](inheritance.md) gestalten Sie diesen Code um, um die Redundanzen zu entfernen.

Sie können mehrere Attribute in einer Zeile einfügen, also können Sie das `HireDate`-Attribut folgendermaßen schreiben:

```csharp
[DataType(DataType.Date),Display(Name = "Hire Date"),DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
```

### <a name="the-courseassignments-and-officeassignment-navigation-properties"></a>Die Navigationseigenschaften „CourseAssignments“ und „OfficeAssignment“

Bei den Eigenschaften `CourseAssignments` und `OfficeAssignment` handelt es sich um Navigationseigenschaften.

Da ein Dozent eine beliebige Anzahl von Kursen geben kann, wird `CourseAssignments` als Auflistung definiert.

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

Wenn eine Navigationseigenschaft mehrere Entitäten enthalten kann, muss deren Typ aus einer Liste bestehen, in der Einträge hinzugefügt, gelöscht und aktualisiert werden können.  Sie können `ICollection<T>` oder einen Typ wie `List<T>` oder `HashSet<T>` angeben. Wenn Sie `ICollection<T>` angeben, erstellt Entity Framework standardmäßig eine `HashSet<T>`-Auflistung.

Warum es sich dabei um `CourseAssignment`-Entitäten handelt, wird im Folgenden im Abschnitt zu m:n-Beziehungen erläutert.

Die Geschäftsregeln der Contoso University besagen, dass ein Dozent maximal ein Büro besitzen kann. Die `OfficeAssignment`-Eigenschaft enthält also eine einzige OfficeAssignment-Entität (diese kann NULL sein, wenn kein Büro zugewiesen ist).

```csharp
public OfficeAssignment OfficeAssignment { get; set; }
```

## <a name="create-officeassignment-entity"></a>Erstellen der Entität „OfficeAssignment“

![Entität „OfficeAssignment“](complex-data-model/_static/officeassignment-entity.png)

Erstellen Sie *Models/OfficeAssignment.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu/Models/OfficeAssignment.cs)]

### <a name="the-key-attribute"></a>Das Key-Attribut

Es gibt eine 1:0..1-Beziehung zwischen den Entitäten „Instructor“ und „OfficeAssignment“. Eine Bürozuweisung ist nur in Beziehung zu dem Dozenten vorhanden, dem sie zugewiesen ist. Daher ist deren Primärschlüssel auch der Fremdschlüssel für die Entität „Instructor“. Entity Framework erkennt „InstructorID“ jedoch nicht automatisch als Primärschlüssel dieser Entität an, da dessen Name nicht der Namenskonvention „ID“ oder „classnameID“ folgt. Deshalb wird das `Key`-Attribut verwendet, um „InstructorID“ als Schlüssel zu erkennen:

```csharp
[Key]
public int InstructorID { get; set; }
```

Sie können ebenfalls das `Key`-Attribut verwenden, wenn die Entität einen eigenen primären Schlüssel besitzt, Sie der Eigenschaft jedoch einen anderen Namen als „classnameID“ oder „ID“ geben möchten.

Standardmäßig behandelt Entity Framework den Schlüssel nicht als datenbankgeneriert, da die Spalte für eine identifizierende Beziehung vorgesehen ist.

### <a name="the-instructor-navigation-property"></a>Die Navigationseigenschaft „Instructor“

Die Entität „Instructor“ verfügt über eine auf NULL festlegbare `OfficeAssignment`-Navigationseigenschaft (da einem Dozenten möglicherweise kein Büro zugewiesen ist). Die OfficeAssignment-Entität verfügt über eine nicht auf NULL festlegbare `Instructor`-Navigationseigenschaft (da eine Bürozuweisung nicht ohne einen Dozenten vorhanden sein kann – `InstructorID` ist nicht auf NULL festlegbar). Wenn die Entität „Instructor“ mit der Entität „OfficeAssignment“ verknüpft ist, besitzt jede Entität in den Navigationseigenschaften einen Verweis auf die andere.

Sie können ein `[Required]`-Attribut zur Navigationseigenschaft „Instructor“ hinzufügen, um anzugeben, dass ein zugehöriger Dozent vorhanden sein muss. Dies ist jedoch nicht notwendig, da der Fremdschlüssel `InstructorID` (bei dem es sich ebenfalls um den Schlüssel für diese Tabelle handelt) nicht auf NULL festlegbar ist.

## <a name="modify-course-entity"></a>Ändern der Entität „Course“

![Entität „Course“](complex-data-model/_static/course-entity.png)

Ersetzen Sie in *Models/Course.cs* den Code, den Sie zuvor hinzugefügt haben, durch folgenden Code. Die Änderungen werden hervorgehoben.

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Final&highlight=2,10,13,16,19,21,23)]

Die Entität „Course“ besitzt die Fremdschlüsseleigenschaft `DepartmentID`, die auf die zugehörige Entität „Department“ zeigt und die Navigationseigenschaft `Department` besitzt.

Entity Framework erfordert nicht, dass Sie eine Fremdschlüsseleigenschaft zu Ihrem Datenmodell hinzufügen, wenn eine Navigationseigenschaft für eine verknüpfte Entität vorhanden ist.  Entity Framework erstellt an den erforderlichen Stellen automatisch Fremdschlüssel in der Datenbank und erstellt [Schatteneigenschaften](/ef/core/modeling/shadow-properties) für diese. Durch Fremdschlüssel im Datenmodell können Updates einfacher und effizienter durchgeführt werden. Wenn Sie beispielsweise die Entität „Course“ zum Bearbeiten abrufen, ist die Entität „Department“ NULL, wenn Sie diese nicht laden. Wenn Sie die Entität „Course“ also aktualisieren, müssen Sie ebenfalls die Entität „Department“ abrufen. Wenn die Fremdschlüsseleigenschaft `DepartmentID` im Datenmodell vorhanden ist, müssen Sie die Entität „Department“ vor dem Update nicht abrufen.

### <a name="the-databasegenerated-attribute"></a>Das Attribut „DatabaseGenerated“

Das `DatabaseGenerated`-Attribut mit dem `None`-Parameter der `CourseID`-Eigenschaft gibt an, dass Primärschlüsselwerte vom Benutzer bereitgestellt und nicht von der Datenbank generiert werden.

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
[Display(Name = "Number")]
public int CourseID { get; set; }
```

Standardmäßig geht Entity Framework davon aus, dass Primärschlüsselwerte von der Datenbank generiert werden. Das ist in den meisten Fällen erwünscht. Für Course-Entitäten verwenden Sie jedoch eine benutzerdefinierte Kursnummer, z.B. eine 1000er-Reihe für eine Abteilung, eine 2000er-Reihe für eine andere usw.

Das `DatabaseGenerated`-Attribut kann ebenfalls verwendet werden, um Standardwerte zu generieren. Im Fall von Datenbankspalten wird es verwendet, um das Datum zu erfassen, an dem eine Zeile erstellt oder aktualisiert wurde.  Weitere Informationen finden Sie unter [Generated Properties (Generierte Eigenschaften)](/ef/core/modeling/generated-properties).

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften in der Entität „Course“ stellen folgende Beziehungen dar:

Ein Kurs ist einer Abteilung zugeordnet, sodass es aus den oben genannten Gründen eine `DepartmentID`-Fremdschlüsseleigenschaft und eine `Department`-Navigationseigenschaft gibt.

```csharp
public int DepartmentID { get; set; }
public Department Department { get; set; }
```

In einem Kurs können beliebig viele Studenten angemeldet sein, darum stellt die `Enrollments`-Navigationseigenschaft eine Auflistung dar:

```csharp
public ICollection<Enrollment> Enrollments { get; set; }
```

Ein Kurs kann von mehreren Dozenten unterrichtet werden, darum stellt die `CourseAssignments`-Navigationseigenschaft eine Auflistung dar (der Typ `CourseAssignment` wird [später](#many-to-many-relationships) erklärt):

```csharp
public ICollection<CourseAssignment> CourseAssignments { get; set; }
```

## <a name="create-department-entity"></a>Erstellen der Entität „Department“

![Entität „Department“](complex-data-model/_static/department-entity.png)

Erstellen Sie *Models/Department.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Begin)]

### <a name="the-column-attribute"></a>Das Column-Attribut

Zuvor haben Sie das `Column`-Attribut verwendet, um die Zuordnung des Spaltennamens zu ändern. Im Code für die Entität „Department“ wird das `Column`-Attribut verwendet, um die SQL-Datentypzuordnung zu ändern, sodass die Spalte mithilfe des SQL Server-Typs „money“ in der Datenbank definiert wird:

```csharp
[Column(TypeName="money")]
public decimal Budget { get; set; }
```

Die Spaltenzuordnung ist im Allgemeinen nicht erforderlich, da Entity Framework den geeigneten SQL Server-Datentyp auf dem CLR-Typ basierend auswählt, den Sie für die Eigenschaft definieren. Der CLR-Typ `decimal` wird dem SQL Server-Typ `decimal` zugeordnet. In diesem Fall wissen Sie jedoch, dass die Spalte Währungsbeträge enthält. Hierfür ist der Datentyp „money“ besser geeignet.

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:

Eine Abteilung kann einen Administrator besitzen oder nicht, und bei einem Administrator handelt es sich immer um einen Dozenten. Deshalb wird die `InstructorID`-Eigenschaft als Fremdschlüssel zur Entität „Instructor“ hinzugefügt, und ein Fragezeichen wird nach der Typbezeichnung `int` hinzugefügt, um die Eigenschaft als auf NULL festlegbar zu markieren. Die Navigationseigenschaft heißt `Administrator`, enthält jedoch eine Instructor-Entität:

```csharp
public int? InstructorID { get; set; }
public Instructor Administrator { get; set; }
```

Eine Abteilung kann viele Kurse aufweisen, darum gibt es die Navigationseigenschaft „Courses“:

```csharp
public ICollection<Course> Courses { get; set; }
```

> [!NOTE]
> Gemäß der Konvention aktiviert Entity Framework das kaskadierende Delete für nicht auf NULL festlegbare Fremdschlüssel und für m:n-Beziehungen. Dies kann zu zirkulären Regeln für kaskadierende Deletes führen, wodurch eine Ausnahme ausgelöst wird, wenn Sie versuchen, eine Migration hinzuzufügen. Wenn Sie beispielsweise die Eigenschaft „Department.InstructorID“ nicht als auf NULL festlegbar definiert haben, würde EF eine Regeln für kaskadierende Deletes konfigurieren, um die Abteilung zu löschen, wenn Sie den Kursleiter löschen. Dies ist aber nicht das, was Sie wollen. Wenn Ihre Geschäftsregeln erfordern, dass die `InstructorID`-Eigenschaft nicht auf NULL festlegbar ist, müssen Sie folgende Fluent-API-Anweisung verwenden, um kaskadierende Deletes für die Eigenschaft zu deaktivieren:
>
> ```csharp
> modelBuilder.Entity<Department>()
>    .HasOne(d => d.Administrator)
>    .WithMany()
>    .OnDelete(DeleteBehavior.Restrict)
> ```

## <a name="modify-enrollment-entity"></a>Ändern der Entität „Enrollment“

![Entität „Enrollment“](complex-data-model/_static/enrollment-entity.png)

Ersetzen Sie in *Models/Enrollment.cs* den Code, den Sie zuvor hinzugefügt haben, durch folgenden Code:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Final&highlight=1-2,16)]

### <a name="foreign-key-and-navigation-properties"></a>Fremdschlüssel- und Navigationseigenschaften

Die Fremdschlüssel- und Navigationseigenschaften stellen folgende Beziehungen dar:

Ein Anmeldungsdatensatz gilt für einen einzelnen Kurs, sodass es eine `CourseID`-Fremdschlüsseleigenschaft und eine `Course`-Navigationseigenschaft gibt:

```csharp
public int CourseID { get; set; }
public Course Course { get; set; }
```

Ein Anmeldungsdatensatz gilt für einen einzelnen Studenten, sodass es eine `StudentID`-Fremdschlüsseleigenschaft und eine `Student`-Navigationseigenschaft gibt:

```csharp
public int StudentID { get; set; }
public Student Student { get; set; }
```

## <a name="many-to-many-relationships"></a>m:n-Beziehungen

Es besteht eine m:n-Beziehung zwischen den Entitäten „Student“ und „Course“, und die Entitätsfunktionen „Enrollment“ liegen als m:n-Jointabelle *mit Nutzlast* in der Datenbank vor. „Mit Nutzlast“ bedeutet, dass die Tabelle „Enrollment“ außer den Fremdschlüsseln für die verknüpften Tabellen (in diesem Fall ein Primärschlüssel und eine Grade-Eigenschaft) zusätzliche Daten enthält.

Die folgende Abbildung stellt dar, wie diese Beziehungen in einem Entitätsdiagramm aussehen. (Dieses Diagramm wurde mithilfe der Entity Framework Power Tools für Entity Framework 6.x erstellt. Das Erstellen des Diagramms ist nicht Teil des Tutorials, sondern wird hier nur zur Veranschaulichung verwendet.)

![m:n-Beziehung zwischen „Student“ und „Course“](complex-data-model/_static/student-course.png)

Jede Beziehung weist an einem Ende „1“ und am anderen Ende „*“ auf, wodurch eine 1:n-Beziehung dargestellt wird.

Wenn in der Tabelle „Enrollment“ nicht die Information „Grade“ enthalten wäre, müsste diese nur die beiden Fremdschlüssel „CourseID“ und „StudentID“ enthalten. In diesem Fall würde es sich um eine m:n-Jointabelle ohne Nutzlast (oder um eine reine Jointabelle) in der Datenbank handeln. Die Entitäten „Instructor“ und „Course“ weisen diese Art von m:n-Beziehung auf. Als Nächstes sollten Sie also eine Entitätsklasse erstellen, die als Jointabelle ohne Nutzlast fungiert.

(Entity Framework 6.x unterstützt implizite Jointabellen für m:n-Beziehungen, Entity Framework Core jedoch nicht. Weitere Informationen finden Sie in der [Diskussion im GitHub-Repository für Entity Framework Core](https://github.com/aspnet/EntityFramework/issues/1368).)

## <a name="the-courseassignment-entity"></a>Die Entität „CourseAssignment“

![Entität „CourseAssignment“](complex-data-model/_static/courseassignment-entity.png)

Erstellen Sie *Models/CourseAssignment.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu/Models/CourseAssignment.cs)]

### <a name="join-entity-names"></a>Namen von Joinentitäten

Eine Jointabelle ist in der Datenbank für die m:n-Beziehung zwischen „Instructor“ und „Course“ erforderlich. Diese muss über eine Entitätenmenge dargestellt werden. Es ist üblich, eine Joinentität `EntityName1EntityName2` zu benennen, was in diesem Fall `CourseInstructor` entsprechen würde. Es wird jedoch empfohlen, einen Namen auszuwählen, der die Beziehung beschreibt. Datenmodelle fangen einfach an und werden dann größer. Mit der Zeit werden Joins ohne Nutzlast Nutzlasten hinzugefügt. Wenn Sie mit einem aussagekräftigen Entitätsnamen beginnen, müssen Sie diesen später nicht ändern. Im Idealfall verfügt die Joinentität über einen eigenen, nachvollziehbaren Namen (der möglicherweise aus nur einem Wort besteht) in der Geschäftsdomäne. „Books“ und „Customers“ könnten beispielsweise über „Ratings“ verknüpft werden. Für diese Beziehung ist `CourseAssignment` besser als `CourseInstructor` geeignet.

### <a name="composite-key"></a>Zusammengesetzte Schlüssel

Da die Fremdschlüssel nicht auf NULL festlegbar sind und zusammen jede Zeile der Tabelle eindeutig identifizieren, ist kein separater Primärschlüssel erforderlich. Die Eigenschaften *InstructorID* und *CourseID* fungieren als zusammengesetzter Primärschlüssel. Die einzige Möglichkeit zum Identifizieren von zusammengesetzten Primärschlüsseln für Entity Framework besteht in der Verwendung der *Fluent-API*. Dies kann nicht mithilfe von Attributen durchgeführt werden. Weitere Informationen zum Konfigurieren von zusammengesetzten Primärschlüsseln finden Sie im nächsten Abschnitt.

Durch den zusammengesetzten Schlüssel wird sichergestellt, dass nicht mehrere Zeilen für denselben Dozenten und Kurs vorhanden sein können, obwohl es mehrere Zeilen für einen Kurs und mehrere Zeilen für einen Dozenten gibt. Die Joinentität `Enrollment` definiert ihren eigenen Primärschlüssel, dadurch sind Duplikate dieser Art möglich. Sie können einen eindeutigen Index zu den Feldern mit Fremdschlüsseln hinzufügen, um solche Duplikate zu verhindern, oder `Enrollment` mit einem zusammengesetzten Primärschlüssel konfigurieren, der `CourseAssignment` ähnelt. Weitere Informationen finden Sie unter [Indizes](/ef/core/modeling/indexes).

## <a name="update-the-database-context"></a>Aktualisieren des Datenbankkontexts

Fügen Sie folgenden hervorgehobenen Code zur Datei *Data/SchoolContext.cs* hinzu:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_BeforeInheritance&highlight=15-18,25-31)]

Durch diesen Code werden neue Entitäten hinzugefügt, und der zusammengesetzte Primärschlüssel der Entität „CourseAssignment“ wird konfiguriert.

## <a name="about-a-fluent-api-alternative"></a>Informationen zu einer Fluent-API-Alternative

Der Code in der `OnModelCreating`-Methode der `DbContext`-Klasse verwendet die *Fluent-API* zum Konfigurieren des Verhaltens von Entity Framework. Die API wird als „Fluent“ bezeichnet, da sie häufig durch das Verketten mehrerer Methodenaufrufe zu einer einzigen Anweisung verwendet wird. Ein Beispiel hierfür finden Sie in der [EF Core documentation (Dokumentation zu Entity Framework)](/ef/core/modeling/#use-fluent-api-to-configure-a-model):

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>()
        .Property(b => b.Url)
        .IsRequired();
}
```

In diesem Tutorial verwendet Sie die Fluent-API nur für Datenbankzuordnungen, die nicht mithilfe von Attributen durchgeführt werden können. Sie können die Fluent-API jedoch ebenfalls verwenden, um den Großteil der Regeln für die Formatierung, Validierung und Zuordnung anzugeben, die Sie mithilfe von Attributen festlegen können. Manche Attribute, z.B. `MinimumLength`, können nicht mit der Fluent-API angewendet werden. Wie bereits erwähnt, ändert `MinimumLength` das Schema nicht, sondern wendet lediglich eine client- und serverseitige Validierungsregel an.

Einige Entwickler bevorzugen die exklusive Verwendung der Fluent-API, um ihre Entitätsklassen „rein“ zu halten. Sie können Attribute und die Fluent-API verwenden, wenn Sie möchten. Einige Anpassungen können nur mithilfe der Fluent-API vorgenommen werden. Im Allgemeinen wird jedoch empfohlen, einen der beiden Ansätze auszuwählen und diesen so konsistent wie möglich zu verwenden. Wenn Sie beide verwenden, beachten Sie, dass Attribute durch die Fluent-API überschrieben werden, wenn ein Konflikt vorliegt.

Weitere Informationen zu Attributen und Fluent-API im Vergleich finden Sie unter [Methods of configuration (Konfigurationsmethoden)](/ef/core/modeling/).

## <a name="entity-diagram-showing-relationships"></a>Entitätsdiagramm mit angezeigten Beziehungen

Die folgende Abbildung stellt das Diagramm dar, das von Entity Framework Power Tools für das vollständige Modell „School“ erstellt wird.

![Entitätsdiagramm](complex-data-model/_static/diagram.png)

Neben den Linien für 1:n-Beziehungen (1:\*) werden die Linien für „Eins-bis-Null-oder Eins“-Beziehungen (1:0..1) zwischen den Entitäten „Instructor“ und „OfficeAssignment“ und die Linien für 0..1:n-Beziehungen (0..1:*) zwischen den Entitäten „Instructor“ und „Department“ dargestellt.

## <a name="seed-database-with-test-data"></a>Füllen der Datenbank mit Testdaten

Ersetzen Sie den Code in der Datei *Data/DbInitializer.cs* durch folgenden Code, um Startwertdaten für die neu erstellten Entitäten bereitzustellen.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Final)]

Wie im ersten Tutorial dargestellt werden durch diesen Code überwiegend neue Entitätsobjekte dargestellt und für die Tests erforderliche Beispieldaten in Eigenschaften geladen. Achten Sie darauf, wie die m:n-Beziehungen verarbeitet werden: Der Code erstellt Beziehungen, indem Entitäten in den Joinentitätenmengen `Enrollments` und `CourseAssignment` erstellt werden.

## <a name="add-a-migration"></a>Hinzufügen einer Migration

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt. Öffnen Sie dann das Befehlsfenster im Projektordner, und geben Sie den Befehl `migrations add` ein (verwenden Sie den Befehl „update-database“ noch nicht):

```dotnetcli
dotnet ef migrations add ComplexDataModel
```

Sie erhalten eine Warnung über möglichen Datenverlust.

```text
An operation was scaffolded that may result in the loss of data. Please review the migration for accuracy.
Done. To undo this action, use 'ef migrations remove'
```

Wenn Sie versucht haben, den Befehl `database update` zu diesem Zeitpunkt auszuführen (führen Sie diesen noch nicht aus), erhalten Sie folgende Fehlermeldung:

> Die ALTER TABLE-Anweisung steht in Konflikt mit der FOREIGN KEY-Einschränkung „FK_dbo.Course_dbo.Department_DepartmentID“. Der Konflikt trat in der „ContosoUniversity“-Datenbank, Tabelle „dbo.Department“, Spalte „DepartmentID“ auf.

In einigen Fällen müssen Sie beim Ausführen von Migrationen mit vorhandenen Daten Stub-Daten in die Datenbank einfügen, um die Fremdschlüsseleinschränkungen zu erfüllen. Der generierte Code in der `Up`-Methode fügt den nicht auf NULL festlegbaren Fremdschlüssel „DepartmentID“ zur Tabelle „Course“ hinzu. Wenn beim Ausführen des Codes bereits Zeilen in der Tabelle „Course“ vorhanden sind, schlägt der `AddColumn`-Vorgang fehl, da SQL Server keinen Wert in eine Spalte einfügen kann, die nicht NULL sein darf. In diesem Tutorial führen Sie die Migration auf eine neue Datenbank durch. In einer Produktionsanwendung müssten Sie jedoch dafür sorgen, dass die Migration vorhandene Daten verarbeiten kann. Ein Beispiel hierfür finden Sie in den folgenden Anweisungen.

Damit diese Migration mit vorhandenen Daten funktioniert, müssen Sie den Code ändern, damit dieser der neuen Spalte einen Standardwert zuweist, und eine Stub-Abteilung namens „Temp“ erstellen, die als Standardabteilung fungiert. Folglich sind alle vorhandenen Course-Zeilen mit der Abteilung „Temp“ verknüpft, nachdem die Methode `Up` ausgeführt wurde.

* Öffnen Sie die Datei *{timestamp}_ComplexDataModel.cs*.

* Kommentieren Sie die Codezeile aus, die die Spalte „DepartmentID“ zur Tabelle „Course“ hinzufügt.

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CommentOut&highlight=9-13)]

* Fügen Sie folgenden hervorgehobenen Code nach dem Code hinzu, der die Tabelle „Department“ erstellt:

  [!code-csharp[](intro/samples/cu/Migrations/20170215234014_ComplexDataModel.cs?name=snippet_CreateDefaultValue&highlight=22-32)]

In einer Produktionsanwendung würden Sie Code oder Skripts schreiben, um Department-Zeilen hinzuzufügen und die Course-Zeilen mit den neuen Department-Zeilen zu verknüpfen. Danach benötigen Sie die Abteilung „Temp“ und den Standardwert für die Spalte „Course.DepartmentID“ nicht mehr.

Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt.

## <a name="change-the-connection-string"></a>Ändern der Verbindungszeichenfolge

Sie haben nun neuen Code zur `DbInitializer`-Klasse hinzugefügt, der Startwertdaten für neue Entitäten zu einer leeren Datenbank hinzufügt. Ändern Sie den Namen der Datenbank in der Verbindungszeichenfolge in *appsettings.json* in „ContosoUniversity3“ oder in einen anderen Namen, der auf Ihrem Computer bisher nicht verwendet wird, damit Entity Framework eine neue, leere Datenbank erstellt.

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=ContosoUniversity3;Trusted_Connection=True;MultipleActiveResultSets=true"
  },
```

Speichern Sie die Änderungen in *appsettings.json* .

> [!NOTE]
> Alternativ zum Ändern des Datenbanknamens können Sie die Datenbank auch löschen. Verwenden Sie den **SQL Server-Objekt-Explorer** (SSOX) oder den CLI-Befehl `database drop`:
>
> ```dotnetcli
> dotnet ef database drop
> ```

## <a name="update-the-database"></a>Aktualisieren der Datenbank

Nachdem Sie den Datenbanknamen geändert oder die Datenbank gelöscht haben, führen Sie den Befehl `database update` im Befehlsfenster aus, um die Migrationen auszuführen.

```dotnetcli
dotnet ef database update
```

Führen Sie die App aus, damit die `DbInitializer.Initialize`-Methode ausgeführt wird und die neue Datenbank auffüllt.

Öffnen Sie die Datenbank wie zuvor im SSOX, und erweitern Sie den Knoten **Tabellen** , um festzustellen, dass alle Tabellen erstellt wurden. (Wenn Sie SSOX immer noch geöffnet haben, klicken Sie auf die Schaltfläche **Aktualisieren**.)

![Tabellen im SSOX](complex-data-model/_static/ssox-tables.png)

Führen Sie die App aus, um den Initialisierungscode auszulösen, der das Seeding für die Datenbank ausführt.

Klicken Sie mit der rechten Maustaste auf die Tabelle **CourseAssignment** , und klicken Sie auf **Daten anzeigen** , um zu überprüfen, dass diese Daten enthält.

![CourseAssignment-Daten im SSOX](complex-data-model/_static/ssox-ci-data.png)

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Datenmodell wurde angepasst
> * Entität „Student“ wurde geändert
> * Entität „Instructor“ wurde erstellt
> * Entität „OfficeAssignment“ wurde erstellt
> * Entität „Course“ wurde geändert
> * Entität „Department“ wurde erstellt
> * Entität „Enrollment“ wurde geändert
> * Datenbankkontext wurde aktualisiert
> * Datenbank wurde mit Testdaten gefüllt
> * Migration wurde hinzugefügt
> * Verbindungszeichenfolge wurde geändert
> * Datenbank wurde aktualisiert

Fahren Sie mit dem nächsten Tutorial fort, um weitere Informationen über den Zugriff auf verwandte Daten zu erhalten.

> [!div class="nextstepaction"]
> [Weiter: Zugriff auf verwandte Daten](read-related-data.md)
