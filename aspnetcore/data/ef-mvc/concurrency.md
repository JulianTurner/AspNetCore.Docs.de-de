---
title: 'Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core'
description: In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.
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
uid: data/ef-mvc/concurrency
ms.openlocfilehash: d476c836e8d497ca1291992dda38da1fc9f59ed2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054371"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Tutorial: Behandeln der Parallelität: ASP.NET MVC mit EF Core

In den vorherigen Tutorials haben Sie gelernt, wie Sie Daten aktualisieren. In diesem Tutorial wird gezeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.

Sie erstellen Webseiten, die mit der Fachbereichsentität arbeiten und behandeln Parallelitätsfehler. In der nachfolgenden Abbildung sehen Sie die Seiten „Bearbeiten“ und „Löschen“, einschließlich einiger Meldungen, die angezeigt werden, wenn ein Parallelitätskonflikt auftritt.

![Seite „Fachbereich bearbeiten“](concurrency/_static/edit-error.png)

![Seite „Fachbereich löschen“](concurrency/_static/delete-error.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Erhalten Sie Informationen über Parallelitätskonflikte
> * Fügen Sie eine Nachverfolgungseigenschaft hinzu
> * Erstellen Sie Abteilungscontroller und -ansichten
> * Aktualisieren Sie die Ansicht „Index“
> * Aktualisieren Sie „Bearbeiten“-Methoden
> * Aktualisieren Sie die Ansicht „Bearbeiten“
> * Testen Sie auf Parallelitätskonflikte
> * Aktualisieren der Seite „Delete“ (Löschen)
> * Aktualisieren der Ansichten „Details“ und „Erstellen“

## <a name="prerequisites"></a>Voraussetzungen

* [Aktualisieren relevanter Daten](update-related-data.md)

## <a name="concurrency-conflicts"></a>Nebenläufigkeitskonflikte

Ein Parallelitätskonflikt tritt auf, wenn ein Benutzer die Daten einer Entität anzeigt, um diese zu bearbeiten, und ein anderer Benutzer eben diese Entitätsdaten aktualisiert, bevor die Änderungen des ersten Benutzers in die Datenbank geschrieben wurden. Wenn Sie die Erkennung solcher Konflikte nicht aktivieren, überschreibt das letzte Update der Datenbank die Änderungen des anderen Benutzers. In vielen Anwendungen ist dieses Risiko akzeptabel: Wenn es nur wenige Benutzer bzw. wenige Updates gibt, oder wenn es nicht schlimm ist, dass Änderungen überschrieben werden können, ist es den Aufwand, für die Parallelität zu programmieren, möglicherweise nicht wert. In diesem Fall müssen Sie für die Anwendung keine Behandlung von Nebenläufigkeitskonflikten konfigurieren.

### <a name="pessimistic-concurrency-locking"></a>Pessimistische Parallelität (Sperren)

Wenn Ihre Anwendung versehentliche Datenverluste in Parallelitätsszenarios verhindern muss, ist die Verwendung von Datenbanksperren eine Möglichkeit. Man bezeichnet dies als pessimistische Parallelität. Bevor Sie zum Beispiel eine Zeile aus einer Datenbank lesen, fordern Sie eine Sperre für den schreibgeschützten Zugriff oder den Aktualisierungszugriff an. Wenn Sie eine Zeile für den Aktualisierungszugriff sperren, kann kein anderer Benutzer diese Zeile für den schreibgeschützten Zugriff oder den Aktualisierungszugriff sperren, da er eine Kopie der Daten erhalten würde, die gerade geändert werden. Wenn Sie eine Zeile für den schreibgeschützten Zugriff sperren, können andere diese Zeile ebenfalls für den schreibgeschützten Zugriff sperren, aber nicht für den Aktualisierungszugriff.

Das Verwalten von Sperren hat Nachteile. Es kann komplex sein, sie zu programmieren. Dies erfordert erhebliche Datenbankverwaltungsressourcen und kann mit steigender Anzahl der Benutzer einer Anwendung zu Leistungsproblemen führen. Aus diesen Gründen unterstützen nicht alle Datenbankverwaltungssysteme die pessimistische Parallelität. Entity Framework Core enthält keine integrierte Unterstützung, und in diesem Tutorial wird nicht gezeigt, wie Sie sie implementieren.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Die optimistische Parallelität ist die Alternative zur pessimistischen Parallelität. Die Verwendung der optimistischen Parallelität bedeutet, Nebenläufigkeitskonflikte zu erlauben und entsprechend zu reagieren, wenn diese auftreten. Benutzer1 besucht z.B. die Seite „Abteilung bearbeiten“ und ändert das Budget für die englische Abteilung von $350.000,00 in $0,00.

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn der Browser die Indexseite anzeigt.

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

Dann klickt Benutzer2 auf einer Bearbeitungsseite, die weiterhin ein Budget von $350.000,00 angibt, auf **Speichern**. Was daraufhin geschieht, ist abhängig davon, wie Sie Nebenläufigkeitskonflikte behandeln.

Einige der Optionen schließen Folgendes ein:

* Sie können nachverfolgen, welche Eigenschaft ein Benutzer geändert hat und nur die entsprechenden Spalten in der Datenbank aktualisieren.

     Im Beispielszenario würden keine Daten verloren gehen, da verschiedene Eigenschaften von zwei Benutzern aktualisiert wurden. Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, wird sie die Änderungen von Benutzer1 und Benutzer2 sehen – das Startdatum 1.9.2013 und ein Budget von 0 Dollar. Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlusten führen können. Sie kann Datenverluste jedoch nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft einer Entität vorgenommen werden. Ob Entity Framework auf diese Weise funktioniert, hängt davon ab, wie Sie Ihren Aktualisierungscode implementieren. Oft ist dies in Webanwendungen nicht praktikabel, da es erforderlich sein kann, viele Zustände zu verwalten, um alle ursprünglichen Eigenschaftswerte einer Entität und die neuen Werte im Auge zu behalten. Das Verwalten vieler Zuständen kann sich auf die Leistung der Anwendung auswirken, da es entweder Serverressourcen beansprucht oder in der Webseite selbst (z. B. in ausgeblendeten Feldern) oder in einem cookie enthalten sein muss.

* Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.

     Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 1.9.2013 und der wiederhergestellte Wert $350.000,00 angezeigt. Das ist entweder ein *Client gewinnt* - oder ein *Letzter Schreiber gewinnt* -Szenario. (Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wie in der Einführung dieses Abschnitts beschrieben, geschieht dies automatisch, wenn Sie für die Behandlung der Parallelität keinen Code schreiben.

* Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden.

     In der Regel würden Sie eine Fehlermeldung ausgeben, ihm den aktuellen Zustand der Daten anzeigen und ihm erlauben, seine Änderungen erneut anzuwenden, sofern er dies immer noch machen möchte. Dieses Szenario wird *Speicher gewinnt* genannt. (Die Werte des Datenspeichers haben Vorrang vor den Werten, die vom Client gesendet werden.) In diesem Tutorial implementieren Sie das Szenario „Speicher gewinnt“. Diese Methode stellt sicher, dass keine Änderung überschrieben wird, ohne dass ein Benutzer darüber benachrichtigt wird.

### <a name="detecting-concurrency-conflicts"></a>Erkennen von Nebenläufigkeitskonflikten

Sie können Konflikte auflösen, indem Sie die `DbConcurrencyException`-Ausnahmen behandeln, die vom Entity Framework ausgelöst werden. Entity Framework muss dazu in der Lage sein, Konflikte zu erkennen, damit es weiß, wann diese Ausnahmen ausgelöst werden sollen. Aus diesem Grund müssen Sie die Datenbank und das Datenmodell entsprechend konfigurieren. Einige der Optionen für das Aktivieren der Konflikterkennung schließen Folgendes ein:

* Fügen Sie eine Änderungsverfolgungsspalte in die Datenbanktabelle ein, die verwendet werden kann, um zu bestimmen, wenn eine Änderung an einer Zeile vorgenommen wurde. Anschließend können Sie Entity Framework so konfigurieren, dass die Spalte in der Where-Klausel eines SQL-Updates oder eines Delete-Befehls enthalten ist.

     In der Regel ist `rowversion` der Datentyp der Änderungsverfolgungsspalte. Der Wert `rowversion` ist eine sequenzielle Zahl, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird. In einem Update- oder Delete-Befehl enthält die Where-Klausel den ursprünglichen Wert der Änderungsverfolgungsspalte (die ursprüngliche Zeilenversion). Wenn die Zeile, die aktualisiert wird, durch einen anderen Benutzer geändert wurde, unterscheidet sich der Wert in der Spalte `rowversion` vom ursprünglichen Wert, sodass die Anweisungen „Update“ oder „Delete“ die Zeile, die aktualisiert werden soll, aufgrund der Where-Klausel nicht finden können. Wenn Entity Framework feststellt, dass das Update oder der Delete-Befehl keine Zeilen aktualisiert hat (d.h., wenn die Anzahl der betroffenen Zeilen 0 (null) ist), wird dies als Parallelitätskonflikt interpretiert.

* Konfigurieren Sie Entity Framework so, dass die ursprünglichen Werte jeder Spalte in der Tabelle in den Where-Klauseln der Update- und Delete-Befehle enthalten sind.

     Wie in der ersten Option gibt die Where-Klausel keine Zeile zum Aktualisieren zurück, wenn etwas in der Zeile geändert wurde, da die Zeile zuerst gelesen wurde, was vom Entity Framework als Parallelitätskonflikt interpretiert wird. Bei Datenbanktabellen mit vielen Spalten kann dieser Ansatz zu sehr großen Where-Klauseln führen, was wiederum dazu führen kann, dass Sie eine große Anzahl von Zuständen verwalten müssen. Wie bereits erwähnt, kann das Verwalten großer Mengen von Zuständen die Anwendungsleistung beeinträchtigen. Deshalb wird dieser Ansatz in der Regel nicht empfohlen, und ist nicht die Methode, die in diesem Tutorial verwendet wird.

     Wenn Sie diesen Ansatz für die Parallelität implementieren wollen, müssen Sie alle nicht primären Schlüsseleigenschaften in der Entität markieren, für die Sie die Parallelität nachverfolgen wollen, indem Sie ihnen das Attribut `ConcurrencyCheck` hinzufügen. Diese Änderung ermöglicht dem Entity Framework, alle Spalten in der SQL-Where-Klausel der Update- und Delete-Anweisungen einzubeziehen.

Im weiteren Verlauf dieses Tutorials fügen Sie der Fachbereichsentität die Änderungsverfolgungseigenschaft `rowversion` hinzu, erstellen einen Controller und Ansichten und überprüfen, ob alles ordnungsgemäß funktioniert.

## <a name="add-a-tracking-property"></a>Fügen Sie eine Nachverfolgungseigenschaft hinzu

Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Das Attribut `Timestamp` gibt an, dass diese Spalte in die Where-Klausel der Befehle „Update“ und „Delete“ einbezogen wird, die an die Datenbank gesendet werden. Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor er durch SQL-`rowversion` ersetzt wurde. Der .NET-Typ für `rowversion` ist ein Bytearray.

Wenn Sie die Verwendung der Fluent-API bevorzugen, können Sie die Methode `IsConcurrencyToken` (in *Data/SchoolContext.cs* ) verwenden, um die Änderungsverfolgungseigenschaft anzugeben, wie im folgenden Beispiel dargestellt wird:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Durch das Hinzufügen einer Eigenschaft ändern Sie das Datenbankmodell, daher müssen Sie eine weitere Migration durchführen.

Speichern Sie ihre Änderungen, erstellen Sie das Projekt, und geben Sie dann die folgenden Befehle in das Befehlsfenster ein:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Erstellen Sie Abteilungscontroller und -ansichten

Erstellen Sie einen Abteilungscontroller und Ansichten, wie Sie es vorher bereits für Studenten, Kurse und Dozenten getan haben.

![Erstellen des Fachbereichs](concurrency/_static/add-departments-controller.png)

Ändern Sie in der Datei *DepartmentsController.cs* „FirstMidName“ an jeder Stelle in „FullName“, damit die Dropdownliste für Abteilungsadministratoren den vollen Namen des Dozenten enthält und nicht nur den Nachnamen.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Aktualisieren Sie die Ansicht „Index“

Die Engine für den Gerüstbau hat eine RowVersion-Spalte in der Indexansicht erstellt, dieses Feld soll jedoch nicht angezeigt werden.

Ersetzen Sie den Code in der Datei *Views/Departments/Index.cshtml* durch folgenden Code:

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Damit wird die Überschrift in „Abteilungen“ geändert, die Spalte „RowVersion“ gelöscht und der vollständige Name des Administrators wird anstelle des Vornamens angezeigt.

## <a name="update-edit-methods"></a>Aktualisieren Sie „Bearbeiten“-Methoden

Fügen Sie in den HttpGet-Methoden `Edit` und `Details``AsNoTracking` hinzu. Fügen Sie in der HttpGet-Methode `Edit` für den Administrator Eager Loading hinzu.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

Ersetzen Sie den vorhandenen Code für die HttpPost-Methode `Edit` durch folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Der Code versucht zunächst die Abteilung zu lesen, die aktualisiert werden soll. Wenn die Methode `FirstOrDefaultAsync` NULL zurückgibt, wurde die Abteilung von einem anderen Benutzer gelöscht. In diesem Fall verwendet der Code die bereitgestellten Formularwerte zum Erstellen einer Abteilungsentität, damit die Seite „Bearbeiten“ mit einer Fehlermeldung erneut angezeigt werden kann. Alternativ müssen Sie die Abteilungsentität nicht erneut erstellen, wenn Sie nur eine Fehlermeldung anzeigen, ohne die Abteilungsfelder erneut anzuzeigen.

Die Ansicht speichert den ursprünglichen Wert von `RowVersion` in einem ausgeblendeten Feld, und diese Methode erhält diesen Wert über den Parameter `rowVersion`. Bevor Sie `SaveChanges` aufrufen, müssen Sie diesen ursprünglichen Eigenschaftswert von `RowVersion` in die Auflistung `OriginalValues` für die Entität einfügen.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Wenn Entity Framework dann den SQL-Befehl „Update“ erstellt, enthält dieser Befehl eine Where-Klausel, die nach einer Zeile mit dem ursprünglichen Wert `RowVersion` sucht. Wenn keine Zeile durch den Befehl „Update“ betroffen ist (keine Zeile enthält den ursprünglichen Wert `RowVersion`), löst Entity Framework die Ausnahme `DbUpdateConcurrencyException` aus.

Der Code im Catch-Block für diese Ausnahme ruft die betroffene Abteilungsentität ab, die die aktualisierten Werte der Eigenschaft `Entries` auf dem Ausnahmeobjekt enthält.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Die Auflistung `Entries` hat nur ein `EntityEntry`-Objekt.  Sie können dieses Objekt verwenden, um die aktuellen Datenbankwerte und die neuen Werte abzurufen, die von dem Benutzer eingegeben wurden.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Der Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte mit Datenbankwerten hinzu, die von den Werten abweichen, die der Benutzer auf der Seite „Bearbeiten“ eingegeben hat (zugunsten der Übersichtlichkeit wird hier nur ein Feld angezeigt).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Schließlich legt der Code den Wert `RowVersion` von `departmentToUpdate` auf den neuen Wert fest, der aus der Datenbank abgerufen wurde. Dieser neue `RowVersion`-Wert wird in dem ausgeblendeten Feld gespeichert, wenn die Seite „Bearbeiten“ erneut angezeigt wird. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die nach dem erneuten Anzeigen der Seite „Bearbeiten“ aufgetreten sind.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt. In der Ansicht hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.

## <a name="update-edit-view"></a>Aktualisieren Sie die Ansicht „Bearbeiten“

Nehmen Sie folgende Änderungen in der Datei *Views/Departments/Edit.cshtml* vor:

* Fügen Sie ein ausgeblendetes Feld zum Speichern des Eigenschaftswerts `RowVersion`, direkt nach dem ausgeblendeten Feld für die Eigenschaft `DepartmentID` hinzu.

* Fügen Sie der Dropdownliste die Option „Select Administrator“ hinzu.

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Testen Sie auf Parallelitätskonflikte

Führen Sie die App aus, und navigieren Sie zur Indexseite „Abteilungen“. Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für die englische Abteilung und wählen **In neuer Registerkarte öffnen** aus, klicken Sie dann auf den Link **Bearbeiten** für die englische Abteilung. Beide Registerkarten zeigen nun die gleichen Informationen im Browser an.

Ändern Sie ein Feld in der ersten Registerkarte, und klicken Sie auf **Speichern**.

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

Der Browser zeigt die Indexseite mit dem geänderten Wert an.

Ändern Sie ein Feld in der zweiten Registerkarte.

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

Klicken Sie auf **Speichern**. Folgende Fehlermeldung wird angezeigt:

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

Klicken Sie erneut auf **Speichern**. Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert. Die gespeicherten Werte werden Ihnen auf der Indexseite angezeigt.

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Bei der Seite „Löschen“ entdeckt Entity Framework Nebenläufigkeitskonflikte, die durch die Bearbeitung einer Abteilung ausgelöst wurden, auf ähnliche Weise. Wenn die HttpGet-Methode `Delete` die Bestätigungsansicht anzeigt, enthält diese Ansicht den ursprünglichen Wert von `RowVersion` in einem ausgeblendeten Feld. Dieser Wert steht dann der HttpPost-Methode `Delete` zur Verfügung, die aufgerufen wird, wenn der Benutzer das Löschen bestätigt. Wenn Entity Framework den SQL-Befehl „Delete“ erstellt, enthält dieser Befehl eine Where-Klausel mit dem ursprünglichen Wert `RowVersion`. Wenn der Befehl in 0 (null) betroffenen Zeilen resultiert (d.h. die Zeile wurde geändert, nachdem die Bestätigungsseite „Löschen“ angezeigt wurde), wird eine Parallelitätsausnahme ausgelöst, und die HttpGet-Methode `Delete` wird mit einem auf TRUE festgelegten Fehlerflag aufgerufen, um die Bestätigungsseite erneut mit einer Fehlermeldung anzuzeigen. Es ist auch möglich, dass 0 (null) Zeilen betroffen sind, weil die Zeile von einem anderen Benutzer gelöscht wurde. In diesem Fall wird keine Fehlermeldung angezeigt.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Aktualisieren der Delete-Methoden im Abteilungscontroller

Ersetzen Sie in der Datei *DepartmentsController.cs* die HttpGet-Methode `Delete` durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Die Methode akzeptiert einen optionalen Parameter, der angibt, ob die Seite nach einem Parallelitätsfehler erneut angezeigt wird. Wenn dieses Flag auf TRUE festgelegt ist, und die angegebene Abteilung nicht mehr vorhanden ist, wurde sie von einem anderen Benutzer gelöscht. In diesem Fall leitet der Code an eine Indexseite weiter.  Wenn dieses Flag auf TRUE festgelegt ist, und die Abteilung vorhanden ist, wurde sie von einem anderen Benutzer geändert. In diesem Fall sendet der Code mithilfe von `ViewData` eine Fehlermeldung an die Ansicht.

Ersetzen Sie den Code in der HttpPost-Methode `Delete` (namens `DeleteConfirmed`) durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

In dem eingerüsteten Code, den Sie soeben ersetzt haben, akzeptiert diese Methode nur eine Datensatz-ID:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Diesen Parameter haben Sie in eine Abteilungsentitätsinstanz geändert, die durch die Modellbindung erstellt wurde. Dadurch erhält Entity Framework neben dem Datensatzschlüssel auch Zugriff auf den Eigenschaftswert „RowVersion“.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Ebenfalls haben Sie den Namen der Aktionsmethode `DeleteConfirmed` auf `Delete` geändert. Der eingerüstete Code verwendet den Namen `DeleteConfirmed`, um der HttpPost-Methode eine eindeutige Signatur zuzuweisen. (Die CLR erfordert überladene Methoden, um verschiedene Methodenparameter zu enthalten.) Da die Signaturen nun eindeutig sind, können Sie weiterhin die MVC-Konventionen verwenden, und den gleichen Namen für die HttpPost- und HttpGet-Delete-Methoden verwenden.

Wenn die Abteilung bereits gelöscht ist, gibt die Methode `AnyAsync` FALSE zurück, und die Anwendung kehrt zu der Indexmethode zurück.

Wenn ein Parallelitätsfehler abgefangen wird, zeigt der Code erneut die Bestätigungsseite „Löschen“ an, und stellt ein Flag bereit, das angibt, dass eine Fehlermeldung für die Parallelität angezeigt werden soll.

### <a name="update-the-delete-view"></a>Aktualisieren der Ansicht „Löschen“

Ersetzen Sie den eingerüsteten Code in der Datei *Views/Departments/Delete.cshtml* durch den folgenden Code, der ein Feld für die Fehlermeldung und ausgeblendete Felder für die Eigenschaften „DepartmentID“ und „RowVersion“ hinzufügt. Die Änderungen werden hervorgehoben.

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Dadurch werden folgende Änderungen vorgenommen:

* Fügt eine Fehlermeldung zwischen den Überschriften `h2` und `h3` hinzu.

* Ersetzt „FirstMidName“ durch „FullName“ im Feld **Administrator**.

* Entfernt das Feld „RowVersion“.

* Fügt der Eigenschaft `RowVersion` ein ausgeblendetes Feld zu.

Führen Sie die App aus, und navigieren Sie zur Indexseite „Abteilungen“. Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für die englische Abteilung, und wählen Sie **In neuer Registerkarte öffnen** aus. Klicken Sie in der ersten Registerkarte dann auf den Link **Bearbeiten** für die englische Abteilung.

Ändern Sie im ersten Fenster einen der Werte, und klicken Sie auf **Speichern** :

![Die Seite „Abteilung bearbeiten“ nach der Änderung vor dem Löschen](concurrency/_static/edit-after-change-for-delete.png)

Klicken Sie in der zweiten Registerkarte auf **Löschen**. Ihnen wird eine Fehlermeldung zur Parallelität angezeigt, und die Abteilungswerte werden mit den aktuellen Werten der Datenbank aktualisiert.

![Die Bestätigungsseite „Abteilung löschen“ mit Parallelitätsfehler](concurrency/_static/delete-error.png)

Wenn Sie erneut auf **Löschen** klicken, werden Sie auf die Indexseite weitergeleitet, die anzeigt, dass die Abteilung gelöscht wurde.

## <a name="update-details-and-create-views"></a>Aktualisieren der Ansichten „Details“ und „Erstellen“

Optional können Sie den eingerüsteten Code in den Ansichten „Details“ und „Erstellen“ bereinigen.

Ersetzen Sie den Code in der Datei *Views/Departments/Details.cshtml* , um die Spalte „RowVersion“ zu löschen und den vollständigen Namen des Administrators anzuzeigen.

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Ersetzen Sie den Code in der Datei *Views/Departments/Create.cshtml* , um der Dropdownliste eine Select-Option hinzuzufügen.

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

 Weitere Informationen zum Behandeln der Parallelität in EF Core finden Sie unter [Concurrency conflicts (Nebenläufigkeitskonflikte)](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie Informationen über Parallelitätskonflikte erhalten
> * Haben Sie eine Nachverfolgungseigenschaft hinzugefügt
> * Haben Sie Abteilungscontroller und Ansichten erstellt
> * Haben Sie die Ansicht „Index“ aktualisiert
> * Haben Sie „Bearbeiten“-Methoden aktualisiert
> * Haben Sie die Ansicht „Bearbeiten“ aktualisiert
> * Haben Sie auf Parallelitätskonflikte getestet
> * Haben Sie die Seite „Löschen“ aktualisiert
> * Haben Sie die Ansichten „Details“ und „Erstellen“ aktualisiert

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie die TPH-Vererbung (TPH = Table per Hierarchy, Tabelle pro Hierarchie) für die Entitäten „Instructor“ und „Student“ implementieren.

> [!div class="nextstepaction"]
> [Weiter: Tutorial: Implementieren von Vererbung: ASP.NET MVC mit EF Core](inheritance.md)
