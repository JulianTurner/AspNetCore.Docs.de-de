---
title: 'Tutorial: Implementieren von CRUD-Funktionen – ASP.NET MVC mit EF Core'
description: In diesem Tutorial überprüfen und passen Sie CRUD-Code (Create, Read, Update, Delete) an, der durch den MVC-Gerüstbau automatisch für Ihre Controller und Ansichten erstellt wird.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2019
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
uid: data/ef-mvc/crud
ms.openlocfilehash: 043fe513f370cf63637733b66ca195e7887faab0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054293"
---
# <a name="tutorial-implement-crud-functionality---aspnet-mvc-with-ef-core"></a>Tutorial: Implementieren von CRUD-Funktionen – ASP.NET MVC mit EF Core

Im vorherigen Tutorial haben Sie eine MVC-Anwendung erstellt, die Entity Framework und SQL Server LocalDB verwendet, um Daten zu speichern und anzuzeigen. In diesem Tutorial überprüfen und passen Sie CRUD-Code (Create, Read, Update, Delete) an, der durch den MVC-Gerüstbau automatisch für Ihre Controller und Ansichten erstellt wird.

> [!NOTE]
> Es ist üblich, dass das Repositorymuster implementiert wird, um eine Abstraktionsebene zwischen Ihrem Controller und der Datenzugriffsebene zu erstellen. In diesen Tutorials werden keine Repositorys verwendet, um die Verwendung des Entity Frameworks einfach und zielorientiert zu erklären. Weitere Informationen über Repositorys mit EF finden Sie im [letzten Tutorial dieser Reihe](advanced.md).

In diesem Tutorial:

> [!div class="checklist"]
> * Anpassen der Seite „Details“
> * Aktualisieren der Seite „Erstellen“
> * Aktualisieren der Seite „Bearbeiten“
> * Aktualisieren der Seite „Delete“ (Löschen)
> * Schließen von Datenbankverbindungen

## <a name="prerequisites"></a>Voraussetzungen

* [Tutorial: Erste Schritte mit EF Core in einer ASP.NET Core MVC-Web-App](intro.md)

## <a name="customize-the-details-page"></a>Anpassen der Seite „Details“

Der eingerüstete Code der Indexseite „Students“ lässt die Eigenschaft `Enrollments` aus, da diese Eigenschaft eine Auflistung enthält. In der Seite **Details** zeigen Sie die Inhalte der Sammlung in einer HTML-Tabelle an.

Die Aktionsmethode für die Ansicht „Details“ in der Datei *Controllers/StudentsController.cs* verwendet die Methode `SingleOrDefaultAsync`, um eine einzelne Entität `Student` abzurufen. Fügen Sie Code hinzu, der die Methoden `Include`, `ThenInclude` und `AsNoTracking` aufruft, wie im folgenden hervorgehobenen Code gezeigt wird.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Details&highlight=8-12)]

Aufgrund der Methoden `Include` und `ThenInclude` lädt der Kontext die Navigationseigenschaft `Student.Enrollments` und in jeder Registrierung die Navigationseigenschaft `Enrollment.Course`.  Im Tutorial [Lesen verwandter Daten](read-related-data.md) erfahren Sie mehr über diese Methoden.

Die Methode `AsNoTracking` verbessert die Leistung in Szenarios, in denen zurückgegebene Entitäten nicht während dem aktuellen Kontext aktualisiert werden. Am Ende dieses Tutorials erfahren Sie mehr über `AsNoTracking`.

### <a name="route-data"></a>Routendaten

Der Schlüsselwert, der an die Methode `Details` weitergegeben wird, stammt von den *Routendaten*. Routendaten sind Daten, die die Modellbindung in einem Segment der URL gefunden hat. Die Standardroute gibt zum Beispiel Controller, Aktion und ID-Segmente an:

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Route&highlight=5)]

In der folgenden URL ordnet die Standardroute den Dozenten als den Controller, den Index als die Aktion und 1 als die ID ein. Dies sind Datenwerte für die Route.

```
http://localhost:1230/Instructor/Index/1?courseID=2021
```

Der letzte Teil der URL („?courseID=2021“) ist ein Abfragezeichenfolgenwert. Die Modellbindung gibt auch den ID-Wert an den Parameter `id` der Methode `Index` weiter, wenn Sie ihn als Abfragezeichenfolgenwert übergeben:

```
http://localhost:1230/Instructor/Index?id=1&CourseID=2021
```

In der Indexseite werden Link-URLs von Anweisungen eines Taghilfsprogramms in der Razor-Ansicht erstellt. Im folgenden Razor-Code entspricht der Parameter `id` der Standardroute, daher wird `id` den Routendaten hinzugefügt.

```html
<a asp-action="Edit" asp-route-id="@item.ID">Edit</a>
```

Wenn `item.ID` 6 entspricht, generiert dies den folgenden HTML-Code:

```html
<a href="/Students/Edit/6">Edit</a>
```

Im folgenden Razor-Code entspricht `studentID` nicht einem Parameter der Standardroute, daher wird sie als Abfragezeichenfolge hinzugefügt.

```html
<a asp-action="Edit" asp-route-studentID="@item.ID">Edit</a>
```

Wenn `item.ID` 6 entspricht, generiert dies den folgenden HTML-Code:

```html
<a href="/Students/Edit?studentID=6">Edit</a>
```

Weitere Informationen über Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

### <a name="add-enrollments-to-the-details-view"></a>Hinzufügen von Registrierungen in die Detailansicht

Öffnen Sie *Views/Students/Details.cshtml*. Alle Felder werden mithilfe der Hilfsprogramme `DisplayNameFor` und `DisplayFor` dargestellt, wie in folgendem Beispiel gezeigt wird:

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=13-18&highlight=2,5)]

Fügen Sie den folgenden Code nach dem letzten Feld und direkt vor dem Endtag `</dl>` ein, um eine Liste der Registrierungen anzuzeigen:

[!code-cshtml[](intro/samples/cu/Views/Students/Details.cshtml?range=31-52)]

Wenn der Codeeinzug nach dem Einfügen des Codes falsch ist, drücken Sie Strg + K + D, um diesen zu korrigieren.

Dieser Code durchläuft die Entitäten in der Navigationseigenschaft `Enrollments`. Für jede Registrierung werden der Kurstitel und die Klasse angezeigt. Der Kurstitel wird von der Entität „Course“ abgerufen, die in der Navigationseigenschaft `Course` der Entität „Enrollments“ gespeichert ist.

Führen Sie die App aus, wählen Sie die Registerkarte **Studenten** aus, und klicken Sie bei einem Studenten auf den Link **Details**. Die Liste der Kurse und Klassen für den ausgewählten Studenten wird angezeigt:

![Seite „Studentendetails“](crud/_static/student-details.png)

## <a name="update-the-create-page"></a>Aktualisieren der Seite „Create“ (Erstellen)

Bearbeiten Sie die HttpPost-Methode `Create` in *StudentsController.cs*, indem Sie einen Try-Catch-Block hinzufügen und die ID aus dem Attribut `Bind` entfernen.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=4,6-7,14-21)]

Dieser Code fügt die Entität „Student“ (Schüler) der Entitätenmenge „Students“ hinzu, die durch die ASP.NET Core MVC-Modellbindung erstellt wurde, und speichert die Änderungen in der Datenbank. (Die Modellbindung ist eine Funktionalität von ASP.NET Core MVC, die Ihnen die Arbeit mit Daten vereinfacht, die über ein Formular übermittelt wurden. Eine Modellbindung konvertiert übermittelte Formularwerte in CLR-Typen und übergibt diese als Parameter an die Aktionsmethode. In diesem Fall instanziiert die Modellbindung für Sie eine Entität „Student“, mithilfe von Eigenschaftswerten aus der Formularauflistung.)

Sie haben `ID` aus dem Attribut `Bind` entfernt, da ID der primäre Schlüsselwert ist, den SQL Server automatisch festlegt, wenn die Zeile eingefügt wird. Der ID-Wert wird nicht über Eingabe vom Benutzer festgelegt.

Abgesehen vom Attribut `Bind`, ist der Try-Catch-Block die einzige Änderung, die Sie am eingerüsteten Code vorgenommen haben. Wenn eine Ausnahme abgefangen wird, die von `DbUpdateException` abgeleitet wird, während die Änderungen gespeichert werden, wird eine generische Fehlermeldung angezeigt. `DbUpdateException`-Ausnahmen werden manchmal durch etwas außerhalb der Anwendung ausgelöst, und nicht durch einen Programmierfehler. Es wird empfohlen, dass der Benutzer es erneut versucht. Zwar wird es in diesem Beispiel nicht implementiert, aber eine qualitätsorientierte Produktionsanwendung würde die Ausnahme protokollieren. Weitere Informationen finden Sie im Abschnitt **Log for insight (Einblicke durch Protokollierung)** im Artikel [Monitoring and Telemetry (Building Real-World Cloud Apps with Azure) (Überwachung und Telemetrie (Erstellen von realitätsnahen Cloud-Apps mit Azure))](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).

Das Attribut `ValidateAntiForgeryToken` hilft dabei, Angriffe mit der Websiteübergreifenden Anforderungsfälschung (CSRF) zu verhindern. Der [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert das Token automatisch in die Ansicht und ist enthalten, wenn das Formular vom Benutzer gesendet wird. Das Token wird vom Attribut `ValidateAntiForgeryToken` überprüft. Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

<a id="overpost"></a>

### <a name="security-note-about-overposting"></a>Sicherheitshinweis zum Overposting

Das Attribut `Bind`, das der eingerüstete Code in der Methode `Create` enthält, ist eine Möglichkeit zum Schutz vor Overposting in Erstellungsszenarios. Nehmen wir beispielsweise an, die Entität „Student“ enthält die Eigenschaft `Secret`, die von dieser Webseite nicht festgelegt werden soll.

```csharp
public class Student
{
    public int ID { get; set; }
    public string LastName { get; set; }
    public string FirstMidName { get; set; }
    public DateTime EnrollmentDate { get; set; }
    public string Secret { get; set; }
}
```

Selbst wenn Sie kein `Secret`-Feld auf dieser Webseite haben, könnte ein Hacker ein Tool wie „Fiddler“ verwenden oder JavaScript-Code schreiben, um einen `Secret`-Formularwert bereitzustellen. Wenn das Attribut `Bind` nicht die Felder beschränkt, die die Modellbindung verwendet, wenn sie eine Studentinstanz erstellt, würde die Modellbindung den Formularwert `Secret` abrufen, und zum Erstellen der Entitätsinstanz „Student“ verwenden. Dann würde jeder beliebige Wert in Ihre Datenbank aktualisiert werden, den der Hacker für das Formularfeld `Secret` festlegt. Die folgende Abbildung zeigt das Fiddler-Tool beim Hinzufügen des Felds `Secret` (mit dem Wert „OverPost“) zu den bereitgestellten Formularwerten.

![Fiddler beim Hinzufügen des Felds „Secret“](crud/_static/fiddler.png)

Der Wert „OverPost“ würde dann erfolgreich der Eigenschaft `Secret` der eingefügten Zeile hinzugefügt werden, obwohl Sie nie beabsichtigt haben, dass die Webseite diese Eigenschaft festlegen kann.

Sie können das Overposting in Bearbeitungsszenarios verhindern, indem Sie die Entität zuerst aus der Datenbank lesen und dann `TryUpdateModel` aufrufen, und eine explizit erlaubte Eigenschaftenliste übergeben. Dies ist die Methode, die in diesen Tutorials verwendet wird.

Eine alternative Möglichkeit zum vermeiden von Overposting, die von vielen Entwicklern bevorzugt wird, ist das Verwenden von Ansichtsmodellen, anstelle von Entitätsklassen mit Modellbindung. Schließen Sie nur die Eigenschaften in dem Ansichtsmodell ein, die Sie aktualisieren möchten. Sobald die MVC-Modellbindung abgeschlossen ist, kopieren Sie die Ansichtsmodelleigenschaften in die Entitätsinstanz, optional unter Verwendung eines Tools wie AutoMapper. Wenden Sie `_context.Entry` auf die Entitätsinstanz an, um ihren Status auf `Unchanged` festzulegen, und legen Sie dann `Property("PropertyName").IsModified` in jeder Entitätseigenschaft, die im Ansichtsmodell enthalten ist, auf TRUE fest. Diese Methode funktioniert sowohl im Bearbeitungsszenario als auch im Erstellungsszenario.

### <a name="test-the-create-page"></a>Testen der Seite „Create“ (Erstellen)

Der Code in der Datei *Views/Students/Create.cshtml* verwendet die Taghilfsprogramme `label`, `input` und `span` (für Validierungsmeldungen) für jedes Feld.

Führen Sie die Anwendung aus, wählen Sie die Registerkarte **Students** aus und klicken auf **Neu erstellen**.

Geben Sie Namen und ein Datum ein. Versuchen Sie ein ungültiges Datum einzugeben, sofern Ihr Browser dies zulässt. (Manche Browser erzwingen die Verwendung einer Datumsauswahl.) Klicken Sie dann auf **Erstellen**, um die Fehlermeldung anzuzeigen.

![Validierungsfehler beim Datum](crud/_static/date-error.png)

Dies ist eine serverseitige Validierung, die Sie standardgemäß erhalten. In einem späteren Tutorial sehen Sie, wie Sie Attribute hinzufügen, die auch Code für die clientseitige Validierung generieren. Der folgende hervorgehobene Code zeigt die Überprüfung durch die Modellvalidierung in der Methode `Create`.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Create&highlight=8)]

Ändern Sie das Datum in einen gültigen Wert und klicken auf **Erstellen**, damit der neue Student auf der Seite **Index** angezeigt wird.

## <a name="update-the-edit-page"></a>Aktualisieren der Seite „Edit“ (Bearbeiten)

Die HttpGet-Methode `Edit` (ohne das Attribut `HttpPost`) in der Datei *StudentController.cs* verwendet die Methode `SingleOrDefaultAsync`, um die ausgewählte Studententität abzurufen, wie Sie bereits in der Methode `Details` gesehen haben. Sie müssen diese Methode nicht ändern.

### <a name="recommended-httppost-edit-code-read-and-update"></a>Empfohlener HttpPost-Edit-Code: Lesen und aktualisieren

Ersetzen Sie die HttpPost-Edit-Aktionsmethode durch folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ReadFirst)]

Diese Änderungen implementieren eine bewährte Sicherheitsmethode, um Overposting zu verhindern. Der Gerüstbauer hat ein `Bind`-Attribut generiert und die Entität hinzugefügt, die von der Modellbindung für die Entitätenmenge mit einem `Modified`-Flag erstellt wurde. Dieser Code ist für viele Szenarios nicht empfohlen, weil das Attribut `Bind` vorhandene Daten aus Feldern löscht, die nicht im Parameter `Include` aufgelistet sind.

Der neue Code liest die vorhandene Entität und ruft `TryUpdateModel` auf, um Felder in der abgerufenen Entität [basierend auf Benutzereingaben in den gesendeten Formulardaten](xref:mvc/models/model-binding) zu aktualisieren. Die automatische Änderungsnachverfolgung des Entity Frameworks legt das Flag `Modified` auf den Feldern fest, die durch die Formulareingabe geändert wurden. Wenn die Methode `SaveChanges` aufgerufen wird, erstellt Entity Framework SQL-Anweisungen, um die Datenbankzeile zu aktualisieren. Nebenläufigkeitskonflikte werden ignoriert, und nur die Tabellenspalten, die vom Benutzer aktualisiert wurden, werden in der Datenbank aktualisiert. (In einem späteren Tutorial lernen Sie, wie man Nebenläufigkeitskonflikte behandelt.)

Als Best Practice zum Verhindern von Overposting werden die Felder, die durch die Seite **Bearbeiten** aktualisierbar sein sollen, in den `TryUpdateModel`-Parametern deklariert. (Die leere Zeichenfolge vor der Liste der Felder in der Parameterliste ist für einen Präfix zur Verwendung mit den Namen der Formularfelder.) Derzeit sind keine zusätzlichen von Ihnen geschützten Felder vorhanden. Wenn Sie jedoch die Felder auflisten, die die Modellbindung binden soll, stellen Sie sicher, dass zukünftig hinzugefügte Felder automatisch geschützt sind, bis Sie sie explizit hier hinzufügen.

Aus diesen Änderungen resultiert, dass die Methodensignatur der HttpPost-Methode `Edit` identisch mit der HttpGet-Methode `Edit` ist. Daher haben Sie die Methode in `EditPost` umbenannt.

### <a name="alternative-httppost-edit-code-create-and-attach"></a>Alternativer HttpPost-Edit-Code: Erstellen und Anfügen

Der empfohlene HttpPost-Edit-Code stellt sicher, dass nur geänderte Spalten aktualisiert werden und behält Daten in Eigenschaften, die Sie nicht in der Modellbindung einschließen wollen. Dieser Read-First-Ansatz benötigt jedoch einen zusätzlichen Datenbank-Lesevorgang und kann zu komplexeren Code für die Behandlung von Nebenläufigkeitskonflikten führen. Alternativ können Sie dem EF-Kontext eine Entität anfügen, die von der Modellbindung erstellt wurde, und diese als geändert markieren. (Aktualisieren Sie ihr Projekt nicht mit diesem Code, er wird hier nur gezeigt, um eine optionale Vorgehensweise zu veranschaulichen.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_CreateAndAttach)]

Sie können diesen Ansatz verwenden, wenn die Benutzeroberfläche der Webseite alle Felder in der Entität enthält und diese aktualisieren kann.

Der eingerüstete Code verwendet einen Create-and-Attach-Ansatz (erstellen und anfügen), fängt aber nur `DbUpdateConcurrencyException`-Ausnahmen ab und gibt 404-Fehlermeldungen zurück.  Das gezeigte Beispiel fängt jegliche Ausnahmen zu Updates der Datenbank ab und zeigt eine Fehlermeldung an.

### <a name="entity-states"></a>Entitätsstatus

Der Datenbankkontext verfolgt, ob die Entitäten im Arbeitsspeicher mit ihren entsprechenden Zeilen in der Datenbank synchronisiert sind. Diese Information bestimmt, was passiert, wenn Sie die Methode `SaveChanges` aufrufen. Wenn Sie beispielsweise eine neue Entität an die Methode `Add` übergeben, wird der Status dieser Entität auf `Added` festgelegt. Wenn Sie dann die Methode `SaveChanges` aufrufen, erteilt der Datenbankkontext einen SQL-Befehl INSERT.

Eine Entität kann einen der folgenden Status aufweisen:

* `Added`. Die Entität ist noch nicht in der Datenbank vorhanden. Die Methode `SaveChanges` gibt eine INSERT-Anweisung aus.

* `Unchanged`. Die Methode `SaveChanges` muss nichts mit dieser Entität tun. Wenn Sie eine Entität aus der Datenbank lesen, beginnt die Entität mit diesem Status.

* `Modified`. Einige oder alle Eigenschaftswerte der Entität wurden geändert. Die Methode `SaveChanges` gibt eine UPDATE-Anweisung aus.

* `Deleted`. Die Entität wurde zum Löschen markiert. Die Methode `SaveChanges` gibt eine DELETE-Anweisung aus.

* `Detached`. Die Entität wird nicht vom Datenbankkontext nachverfolgt.

Statusänderungen werden in einer Desktop-App in der Regel automatisch festgelegt. Sie lesen eine Entität aus und nehmen Änderungen an ihren Eigenschaftswerten vor. Dadurch wird der Entitätsstatus automatisch auf `Modified` festgelegt. Wenn Sie dann `SaveChanges` aufrufen, generiert Entity Framework eine SQL UPDATE-Anweisung, die nur die aktuellen Eigenschaften aktualisiert, an denen Sie Änderungen vorgenommen haben.

In einer Web-App wird der `DbContext`, der zunächst eine Entität liest und seine zu bearbeitenden Daten anzeigt, verworfen, nachdem eine Seite gerendert wurde. Wenn die HttpPost-Aktionsmethode `Edit` einer Seite aufgerufen wird, wird eine neue Webanforderung mit einer neuen `DbContext`-Instanz gestellt. Wenn Sie die Entität erneut in diesem neuen Kontext lesen, simulieren Sie die Desktopverarbeitung.

Wenn Sie den zusätzlichen Lesevorgang jedoch nicht ausführen wollen, müssen Sie das Entitätsobjekt verwenden, das von der Modellbindung erstellt wurde.  Die einfachste Möglichkeit hierfür ist, den Entitätsstatus auf „geändert“ festzulegen, wie in dem zuvor gezeigten alternativen HttpPost-Edit-Code. Wenn Sie dann `SaveChanges` aufrufen, aktualisiert Entity Framework alle Spalten der Datenbankzeile, da der Kontext nicht wissen kann, welche Eigenschaften Sie geändert haben.

Der Code ist komplexer, wenn Sie den Read-First-Ansatz nicht nutzen wollen, gleichzeitig aber die SQL UPDATE-Anweisung nur zum Aktualisieren der Felder verwenden wollen, die tatsächlich vom Benutzer geändert wurden. Sie müssen die ursprünglichen Werte auf irgendeine Weise speichern (z.B. mithilfe von ausgeblendeten Feldern), damit sie verfügbar sind, wenn die HttpPost-Methode `Edit` aufgerufen wird. Dann können Sie eine Entität „Student“ mit den ursprünglichen Werten erstellen, die Methode `Attach` mit der Originalversion der Entität aufrufen, die Werte dieser Entität auf neue Werte aktualisieren und dann `SaveChanges` aufrufen.

### <a name="test-the-edit-page"></a>Testen der Seite „Edit“ (Bearbeiten)

Führen Sie die Anwendung aus, wählen Sie die Registerkarte **Students** aus, und klicken Sie dann auf den Link **Edit** (Bearbeiten).

![Bearbeitungsseite „Students“](crud/_static/student-edit.png)

Ändern Sie einige der Daten, und klicken Sie auf **Speichern**. Die Seite **Index** wird geöffnet, und die geänderten Daten werden angezeigt.

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Der Vorlagencode für die HttpGet-Methode `Delete` in der Datei *StudentController.cs* verwendet die Methode `SingleOrDefaultAsync`, um die ausgewählte Entität „Student“ abzurufen, wie Sie bereits in den Methoden zu den Details und dem Bearbeiten gesehen haben. Allerdings müssen Sie dieser Methode und der dazugehörigen Ansicht einige Funktionen hinzufügen, um eine benutzerdefinierte Fehlermeldung zu implementieren, wenn der Aufruf von `SaveChanges` fehlschlägt.

Wie Sie bereits bei den Vorgängen zum Aktualisieren und Erstellen gesehen haben, benötigen Löschvorgänge zwei Aktionsmethoden. Die Methode, die als Antwort zu einer GET-Anforderung aufgerufen wird, stellt eine Ansicht dar, die dem Benutzer ermöglicht, den Löschvorgang zu genehmigen oder abzubrechen. Wenn der Benutzer diesen Löschvorgang genehmigt, wird eine POST-Anforderung erstellt. Wenn dies geschieht, wird die HttpPost-Methode `Delete` aufgerufen, und dann führt diese Methode den Löschvorgang aus.

Fügen Sie der HttpPost-Methode `Delete` einen Try-Catch-Block hinzu, um jegliche Fehler zu behandeln, die auftreten können, wenn die Datenbank aktualisiert wird. Wenn ein Fehler auftritt, ruft die HttpPost-Delete-Methode die HttpGet-Delete-Methode auf und übergibt ihr einen Parameter, der angibt, dass ein Fehler aufgetreten ist. Die HttpGet-Delete-Methode zeigt die Bestätigungsseite mit der Fehlermeldung erneut an, damit der Benutzer eine weitere Möglichkeit erhält, den Vorgang erneut zu versuchen oder abzubrechen.

Ersetzen Sie die HttpGet-Aktionsmethode `Delete` mit folgendem Code für die Verwaltung der Fehlerberichtserstattung:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteGet&highlight=1,9,16-21)]

Dieser Code akzeptiert einen optionalen Parameter, der angibt, ob die Methode nach einem Fehler beim Speichern von Änderungen aufgerufen wurde. Dieser Parameter ist FALSE, wenn die HttpGet-Methode `Delete` aufgerufen wird, ohne dass vorher ein Fehler ausgelöst wurde. Wenn sie als Antwort auf einen Datenbankaktualisierungsfehler von der HttpPost-Methode `Delete` aufgerufen wird, ist der Parameter TRUE und eine Fehlermeldung wird an die Ansicht übergeben.

### <a name="the-read-first-approach-to-httppost-delete"></a>Der Read-First-Ansatz zu HttpPost-Delete

Ersetzen Sie die HttpPost-Aktionsmethode `Delete` (namens `DeleteConfirmed`) mit folgendem Code, der den tatsächlichen Löschvorgang ausführt und jegliche Datenbankaktualisierungsfehler abfängt:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithReadFirst&highlight=6-9,11-12,16-21)]

Dieser Code ruft die ausgewählte Entität ab und anschließend die Methode `Remove` auf, um den Status der Entität auf `Deleted` festzulegen. Wenn `SaveChanges` aufgerufen wird, wird der SQL-Befehl DELETE generiert.

### <a name="the-create-and-attach-approach-to-httppost-delete"></a>Der Create-and-Attach-Ansatz zu HttpPost-Delete

Wenn das Verbessern der Leistung in einer Anwendung mit hohem Volumen eine Priorität ist, können Sie eine überflüssige SQL-Abfrage durch das Instanziieren einer Entität „Student“ vermeiden, indem Sie nur einen primären Schlüsselwert verwenden und den Entitätsstatus auf `Deleted` festlegen. Das ist alles, was Entity Framework benötigt, um die Entität löschen zu können. (Fügen Sie diesen Code nicht in Ihr Projekt ein, er wird hier nur gezeigt, um eine alternative Vorgehensweise zu veranschaulichen.)

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DeleteWithoutReadFirst&highlight=7-8)]

Wenn die Entität zugehörige Daten besitzt, die auch gelöscht werden sollen, sollten Sie sicherstellen, dass das kaskadierende Delete in der Datenbank konfiguriert ist. Bei diesem Ansatz erkennt Entity Framework möglicherweise nicht, dass zugehörige Entitäten vorhanden sind, die gelöscht werden sollen.

### <a name="update-the-delete-view"></a>Aktualisieren der Ansicht „Löschen“

Fügen Sie in der Datei *Views/Student/Delete.cshtml* eine Fehlermeldung zwischen der Überschrift „h2“ und der Überschrift „h3“ eine Fehlermeldung ein, wie im folgenden Beispiel gezeigt wird:

[!code-cshtml[](intro/samples/cu/Views/Students/Delete.cshtml?range=7-9&highlight=2)]

Führen Sie die Anwendung aus, wählen Sie die Registerkarte **Students** aus und klicken auf den Link **Löschen**:

![Bestätigungsseite „Löschen“](crud/_static/student-delete.png)

Klicken Sie auf **Löschen**. Die Indexseite wird ohne den gelöschten Student angezeigt. (Ein Beispiel für den Fehlerbehandlungscode in Aktion im Tutorial zur Parallelität wird angezeigt.)

## <a name="close-database-connections"></a>Schließen von Datenbankverbindungen

Sobald Sie mit ihr fertig sind, muss die Kontextinstanz so bald wie möglich entfernt werden, um die Ressourcen der Datenbankverbindung freizugeben. Die in ASP.NET Core eingebaute [Dependency Injection](../../fundamentals/dependency-injection.md) erledigt diese Aufgabe für Sie.

Rufen Sie die [Erweiterungsmethode AddDbContext](https://github.com/aspnet/EntityFrameworkCore/blob/03bcb5122e3f577a84498545fcf130ba79a3d987/src/Microsoft.EntityFrameworkCore/EntityFrameworkServiceCollectionExtensions.cs) in der Datei *Startup.cs* auf, um die Klasse `DbContext` in dem ASP.NET Core DI-Container bereitzustellen. Diese Methode legt die Lebensdauer des Diensts standardgemäß auf `Scoped` fest. `Scoped` bedeutet, dass die Lebensdauer des Kontextobjekts mit der Lebensdauer der Webanforderung übereinstimmt, und die Methode `Dispose` wird am Ende der Webanforderung automatisch aufgerufen.

## <a name="handle-transactions"></a>Behandeln von Transaktionen

Standardgemäß implementiert Entity Framework implizit Transaktionen. In Szenarios, in denen Sie Änderungen an mehreren Zeilen oder Tabellen vornehmen und dann `SaveChanges` aufrufen, stellt Entity Framework automatisch sicher, dass alle Ihre Änderungen entweder fehlschlagen oder erfolgreich abgeschlossen werden. Wenn ein Fehler auftritt, nachdem einige der Änderungen durchgeführt wurden, werden diese Änderungen automatisch zurückgesetzt. Informationen zu Szenarios, die Sie genauer kontrollieren müssen (z.B. wenn Sie Vorgänge einfügen möchten, die außerhalb von Entity Framework in einer Transaktion ausgeführt werden), finden Sie unter [Transaktionen](/ef/core/saving/transactions).

## <a name="no-tracking-queries"></a>Abfragen ohne Nachverfolgung

Wenn ein Datenbankkontext Tabellenzeilen abruft und Entitätsobjekte erstellt, die diese darstellen, verfolgen sie standardgemäß, ob die Entitäten im Arbeitsspeicher mit dem Inhalt der Datenbank synchronisiert sind. Die Daten im Arbeitsspeicher fungieren als Cache und werden verwendet, wenn Sie eine Entität aktualisieren. Diese Zwischenspeicherung ist in Webanwendungen oft überflüssig, da Kontextinstanzen in der Regel kurzlebig sind (eine neue wird bei jeder Anforderung erstellt und gelöscht), und der Kontext, der eine Entität liest, wird in der Regel gelöscht, bevor diese Entität erneut verwendet wird.

Sie können die Nachverfolgung von Entitätsobjekten im Arbeitsspeicher deaktivieren, indem Sie die Methode `AsNoTracking` aufrufen. Typische Szenarios, in denen Sie das möglicherweise tun wollen, umfassen Folgendes:

* Während der Lebensdauer des Kontexts müssen Sie keine Entitäten aktualisieren, und EF muss [Navigationseigenschaften mit Entitäten, die durch separate Abfragen abgerufen wurden, nicht automatisch laden](read-related-data.md). Diese Bedingungen werden häufig in den HttpGet-Aktionsmethoden eines Controllers erfüllt.

* Sie führen eine Abfrage aus, die große Datenmengen abruft, und nur ein kleiner Teil dieser Daten wird aktualisiert. Bei großen Abfragen kann es effizienter sein, die Nachverfolgung zu deaktivieren, und zu einem späteren Zeitpunkt eine Abfrage für die wenigen Entitäten auszuführen, die aktualisiert werden müssen.

* Sie sollten eine Entität anfügen, um diese zu aktualisieren, jedoch haben Sie eben diese Entität bereits für einen anderen Zweck abgerufen. Da diese Entität bereits vom Datenbankkontext nachverfolgt wird, können Sie die zu ändernde Entität nicht anfügen. Ein Weg, diese Situation zu bewältigen, ist `AsNoTracking` auf der vorherigen Abfrage aufzurufen.

Weitere Informationen finden Sie unter [Tracking vs. No-Tracking (Mit Nachverfolgung gegen ohne Nachverfolgung)](/ef/core/querying/tracking).

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Die Seite „Details“ angepasst
> * Die Seite „Erstellen“ aktualisiert
> * Die Seite „Bearbeiten“ aktualisiert
> * Die Seite „Löschen“ aktualisiert
> * Datenbankverbindungen geschlossen

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die Funktionen der Seite **Index** erweitern, indem Sie das Sortieren, Filtern und Paging hinzufügen.

> [!div class="nextstepaction"]
> [Weiter: Tutorial: Hinzufügen von Sortieren, Filtern und Paging: ASP.NET MVC mit EF Core](sort-filter-page.md)
