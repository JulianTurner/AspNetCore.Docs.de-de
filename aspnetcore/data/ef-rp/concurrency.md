---
title: 'Teil 8: Razor Pages mit EF Core in ASP.NET Core – Parallelität'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
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
uid: data/ef-rp/concurrency
ms.openlocfilehash: 573a509041bfb34faf50a227c451824db03f92ee
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93053994"
---
# <a name="part-8-no-locrazor-pages-with-ef-core-in-aspnet-core---concurrency"></a>Teil 8: Razor Pages mit EF Core in ASP.NET Core – Parallelität

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra) und [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren.

## <a name="concurrency-conflicts"></a>Nebenläufigkeitskonflikte

Ein Nebenläufigkeitskonflikt tritt auf, wenn:

* Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.
* Ein anderer Benutzer aktualisiert dieselbe Entität, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.

Wenn Parallelitätserkennung nicht aktiviert ist, überschreibt die letzte Aktualisierung der Datenbank die Änderungen des anderen Benutzers. Wenn dieses Risiko akzeptabel ist, können die Kosten für die Programmierung für Parallelitätserkennung den Vorteil überwiegen.

### <a name="pessimistic-concurrency-locking"></a>Pessimistische Parallelität (Sperren)

Eine Möglichkeit, Parallelitätskonflikte zu vermeiden, ist die Verwendung von Datenbanksperren. Man bezeichnet dies als pessimistische Parallelität. Bevor die App eine Datenbankzeile liest, die aktualisiert werden soll, wird eine Sperre angefordert. Nachdem eine Zeile für den Aktualisierungszugriff gesperrt wurde, dürfen keine anderen Benutzer die Zeile sperren, bis die erste Sperre freigegeben wird.

Das Verwalten von Sperren hat Nachteile. Die Programmierung kann komplex sein und Leistungsprobleme verursachen, wenn sich die Anzahl der Benutzer erhöht. Entity Framework Core enthält keine integrierte Unterstützung, und in diesem Tutorial wird nicht gezeigt, wie die Implementierung erfolgt.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten. Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget30.png)

Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.

![Ändern des Startdatums in 2013](concurrency/_static/change-date30.png)

Benutzer1 klickt zuerst auf **Speichern** und sieht, dass die Änderung wirksam wird, da der Browser die Indexseite mit 0 (null) als Budgetbetrag anzeigt.

Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt. Was anschließend geschieht, ist abhängig davon, wie Sie Parallelitätskonflikte handhaben:

* Sie können nachverfolgen, welche Eigenschaft ein Benutzer geändert hat und nur die entsprechenden Spalten in der Datenbank aktualisieren.

  In diesem Szenario sollten keine Daten verloren gehen. Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert. Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2. Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können. Dieser Ansatz bietet einige Nachteile:
 
  * Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.
  * Ist in einer Web-App in der Regel nicht praktisch. Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen. Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.
  * Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.

* Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.

  Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt. Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt. (Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.

* Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden. In der Regel würde die App:

  * Eine Fehlermeldung anzeigen
  * Den aktuellen Datenstatus anzeigen
  * Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden

  Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt. (Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt). Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.

## <a name="conflict-detection-in-ef-core"></a>Konflikterkennung in EF Core

EF Core löst `DbConcurrencyException`-Ausnahmen aus, wenn Konflikte erkannt werden. Das Datenmodell muss konfiguriert werden, um die Konflikterkennung zu aktivieren. Optionen für das Aktivieren der Konflikterkennung schließen Folgendes ein:

* Konfigurieren Sie EF Core so, dass die ursprünglichen Werte von Spalten, die als [Parallelitätstoken](/ef/core/modeling/concurrency) in der Where-Klausel der Update- und Delete-Befehle enthalten sind, einbezogen werden.

  Wenn `SaveChanges` aufgerufen wird, sucht die Where-Klausel nach den ursprünglichen Werten von Eigenschaften, die als Anmerkung mit dem <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute>-Attribut versehen wurden. Die Update-Anweisung findet keine zu aktualisierende Zeile, wenn sich eine der Parallelitätstokeneigenschaften geändert hat, seit die Zeile zum ersten Mal gelesen wurde. EF Core interpretiert dies als einen Parallelitätskonflikt. Bei Datenbanktabellen mit vielen Spalten kann dieser Ansatz zu sehr großen Where-Klauseln führen, was wiederum dazu führen kann, dass eine große Anzahl von Zuständen erforderlich ist. Deshalb wird dieser Ansatz in der Regel nicht empfohlen, und ist nicht die Methode, die in diesem Tutorial verwendet wird.

* Fügen Sie eine Änderungsverfolgungsspalte in die Datenbanktabelle ein, die verwendet werden kann, um zu bestimmen, wenn eine Änderung an einer Zeile vorgenommen wurde.

  In einer SQL Server-Datenbank ist `rowversion` der Datentyp der Änderungsverfolgungsspalte. Der Wert `rowversion` ist eine sequenzielle Zahl, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird. In einem Update- oder Delete-Befehl enthält die Where-Klausel den ursprünglichen Wert der Änderungsverfolgungsspalte (die ursprüngliche Zeilenversionsnummer). Wenn die zu aktualisierende Zeile von einem anderen Benutzer geändert wurde, unterscheidet sich der Wert in der Spalte `rowversion` vom ursprünglichen Wert. In diesem Fall kann die Update- oder Delete-Anweisung die zu aktualisierende Zeile aufgrund der Where-Klausel nicht finden. EF Core löst eine Parallelitätsausnahme aus, wenn keine Zeilen von einem Update- oder Delete-Befehl betroffen sind.

## <a name="add-a-tracking-property"></a>Fügen Sie eine Nachverfolgungseigenschaft hinzu

Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

Das <xref:System.ComponentModel.DataAnnotations.TimestampAttribute>-Attribut identifiziert die Spalte als Parallelitätsnachverfolgungsspalte. Die Fluent-API ist eine alternative Methode, um die Nachverfolgungseigenschaft anzugeben:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Für eine SQL Server-Datenbank wird das `[Timestamp]`-Attribut für eine Entitätseigenschaft als Bytearray definiert:

* Bewirkt, dass die Spalte in DELETE- und UPDATE WHERE-Klauseln eingeschlossen wird.
* Legt den Spaltentyp in der Datenbank auf [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) fest.

Die Datenbank generiert eine sequenzielle Zeilenversionsnummer, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird. In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Zeilenversionswert. Wenn sich die aktualisierte Zeile seit ihrem Abruf geändert hat, geschieht Folgendes:

* Der Wert der aktuellen Zeilenversion stimmt nicht mit dem abgerufenen Wert überein.
* Der `Update`- oder `Delete`-Befehl findet keine Zeile, da die `Where`-Klausel nach dem abgerufenen Zeilenversionswert sucht.
* Es wird eine `DbUpdateConcurrencyException` ausgelöst.

Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an. Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.

Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück. Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Für eine SQLite-Datenbank wird das `[Timestamp]`-Attribut für eine Entitätseigenschaft als Bytearray definiert:

* Bewirkt, dass die Spalte in DELETE- und UPDATE WHERE-Klauseln eingeschlossen wird.
* Wird einem BLOB-Spaltentyp zugeordnet.

Datenbank löst eine Aktualisierung der RowVersion-Spalte mit einem neuen zufälligen Bytearray aus, wenn eine Zeile aktualisiert wird. In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert der RowVersion-Spalte. Wenn sich die aktualisierte Zeile seit ihrem Abruf geändert hat, geschieht Folgendes:

* Der Wert der aktuellen Zeilenversion stimmt nicht mit dem abgerufenen Wert überein.
* Der `Update`- oder `Delete`-Befehl findet keine Zeile, da die `Where`-Klausel nach dem ursprünglichen Zeilenversionswert sucht.
* Es wird eine `DbUpdateConcurrencyException` ausgelöst.

---

### <a name="update-the-database"></a>Aktualisieren der Datenbank

Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.

Erstellen Sie das Projekt. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Führen Sie den folgenden Befehl in der PMC aus:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Führen Sie die folgenden Befehle über ein Terminal aus:

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

Dieser Befehl:

* Erstellt die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs*.
* Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt. Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Führen Sie den folgenden Befehl in der PMC aus:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Öffnen Sie die Datei `Migrations/<timestamp>_RowVersion.cs`, und fügen Sie den hervorgehobenen Code hinzu:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Der vorangehende Code:

  * Aktualisiert vorhandene Zeilen mit zufälligen Blobwerten.
  * Fügt Datenbanktrigger hinzu, die die RowVersion-Spalte auf einen zufälligen Blobwert festlegen, wenn eine Zeile aktualisiert wird.

* Führen Sie die folgenden Befehle über ein Terminal aus:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Gerüstbau der Department-Seiten

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:

* Erstellen Sie einen Ordner *Pages/Departments*.  
* Verwenden Sie `Department` als Modellklasse.
  * Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Erstellen Sie einen Ordner *Pages/Departments*.

* Führen Sie den folgenden Befehl aus, um das Gerüst für Department-Seiten zu erstellen.

  **Unter Windows:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Unter Linux oder macOS:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Erstellen Sie das Projekt.

## <a name="update-the-index-page"></a>Aktualisieren der Index-Seite

Das Gerüstbautool hat eine `RowVersion`-Spalte für die Index-Seite erstellt, dieses Feld würde jedoch in einer Produktions-App nicht angezeigt werden. In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis für die Funktionsweise der Parallelitätsverarbeitung bekommen. Das letzte Byte ist nicht zwingend eindeutig.

Aktualisieren Sie die Seite *Pages\Departments\Index.cshtml*:

* Ersetzen Sie „Index“ durch „Abteilungen“.
* Ändern Sie den Code, der `RowVersion` enthält, um nur das letzte Byte des Bytearrays anzuzeigen.
* Ersetzen Sie „FirstMidName“durch „FullName“.

Der folgende Code zeigt die aktualisierte Seite an:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Aktualisieren des Seitenbearbeitungsmodells

Aktualisieren Sie die Datei *Pages\Departments\Edit.cshtml.cs* mithilfe des folgenden Codes:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> wird mit dem `rowVersion`-Wert aus der Entität aktualisiert, als diese in der `OnGet`-Methode abgerufen wurde. EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert. Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

Im hervorgehobenen Code oben:

* Ist der Wert in `Department.RowVersion` der Inhalt der Entität, als diese ursprünglich in der Get-Anforderung für die Edit-Seite abgerufen wurde. Der Wert wird für die `OnPost`-Methode durch ein ausgeblendetes Feld auf der Razor-Seite bereitgestellt, das die zu bearbeitende Entität anzeigt. Der Wert des ausgeblendeten Felds wird vom Modellbinder in `Department.RowVersion` kopiert.
* `OriginalValue` verwendet EF Core in der Where-Klausel. Bevor die hervorgehobene Codezeile ausgeführt wird, enthält `OriginalValue` den Wert, der in der Datenbank enthalten war, als `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde. Dieser kann sich möglicherweise von dem auf der Edit-Seite angezeigten Wert unterscheiden.
* Der hervorgehobene Code stellt sicher, dass EF Core den ursprünglichen `RowVersion`-Wert aus der angezeigten `Department`-Entität in der Where-Klausel der SQL UPDATE-Anweisung verwendet.

Wenn ein Parallelitätsfehler auftritt, ruft der folgende hervorgehobene Code die Clientwerte (die Werte, die für diese Methode bereitgestellt werden) und die Datenbankwerte ab.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die für `OnPostAsync` bereitgestellt wurden:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt. In der Razor-Seite hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.

### <a name="update-the-edit-page"></a>Aktualisieren der Seite „Bearbeiten“

Aktualisieren Sie die Datei *Pages/Departments/Edit.cshtml* mithilfe des folgenden Codes:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Der vorangehende Code:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit ein Postback-Ereignis den Wert bindet.
* Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.
* Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“

Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.
* Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-130.png)

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Ändern Sie ein anderes Feld in der zweiten Registerkarte.

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-230.png)

Klicken Sie auf **Speichern**. Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error30.png)

In diesem Browserfenster sollte nicht das Namensfeld geändert werden. Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein. Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.

Klicken Sie erneut auf **Speichern**. Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert. Die gespeicherten Werte werden auf der Indexseite angezeigt.

## <a name="update-the-delete-page-model"></a>Aktualisieren des Modells für die Seite „Löschen“

Aktualisieren Sie die Datei *Pages/Departments/Delete.cshtml.cs* mithilfe des folgenden Codes:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde. Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde. Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`. Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:

* Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.
* Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.
* Wird `OnGetAsync` mit `concurrencyError` aufgerufen.

### <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

Durch den vorangehenden Code werden folgende Änderungen vorgenommen:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Eine Fehlermeldung wird hinzugefügt.
* „FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.
* `RowVersion` wird geändert, um das letzte Byte anzuzeigen.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit ein Postback-Ereignis den Wert bindet.

### <a name="test-concurrency-conflicts"></a>Testen Sie auf Parallelitätskonflikte

Erstellen Sie einen Fachbereich zum Testen.

Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.
* Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt. Durch Klicken auf **Löschen** wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)](/ef/core/modeling/concurrency)
* [Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)](/ef/core/saving/concurrency)
* [Debuggen von ASP.NET Core 2.x-Quellcode](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Nächste Schritte

Dies ist das letzte Tutorial der Serie. Weitere Themen werden in der [MVC-Version dieser Tutorialreihe](xref:data/ef-mvc/index) behandelt.

> [!div class="step-by-step"]
> [Vorheriges Tutorial](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Dieses Tutorial zeigt, wie Sie Konflikte behandeln, wenn mehrere Benutzer gleichzeitig dieselbe Entität aktualisieren. Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an. [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="concurrency-conflicts"></a>Nebenläufigkeitskonflikte

Ein Nebenläufigkeitskonflikt tritt auf, wenn:

* Ein Benutzer zur Bearbeitungsseite für eine Entität navigiert.
* Ein anderer Benutzer dieselbe Entität aktualisiert, bevor die Änderung des ersten Benutzers in die Datenbank geschrieben wird.

Wenn die Parallelitätserkennung nicht aktiviert ist, während gleichzeitige Updates ausgeführt werden, geschieht Folgendes:

* Das letzte Update gilt. Das bedeutet, dass die neuesten zu aktualisierenden Werte in der Datenbank gespeichert werden.
* Das erste der aktuellen Updates geht verloren.

### <a name="optimistic-concurrency"></a>Optimistische Nebenläufigkeit

Optimistische Nebenläufigkeit lässt Nebenläufigkeitskonflikte zu und reagiert entsprechend, wenn diese auftreten. Beispielsweise besucht Benutzer1 die Bearbeitungsseite des Fachbereichs und ändert das Budget für den englischen Fachbereich von 350.000 $ in 0 $.

![Ändern des Budgets in 0 (null)](concurrency/_static/change-budget.png)

Bevor Benutzer1 auf **Speichern** klickt, besucht Benutzer2 dieselbe Seite und ändert das Feld „Startdatum“ von 9.1.2007 in 9.1.2013.

![Ändern des Startdatums in 2013](concurrency/_static/change-date.png)

Benutzer1 klickt zuerst auf **Speichern** und sieht die Änderungen, wenn im Browser die Indexseite angezeigt wird.

![Budget in 0 (null) geändert](concurrency/_static/budget-zero.png)

Benutzer2 klickt auf einer Bearbeitungsseite auf **Speichern**, die weiterhin ein Budget von 350.000 $ anzeigt. Was daraufhin geschieht ist abhängig davon, wie Sie Nebenläufigkeitskonflikte handhaben.

Die optimistische Nebenläufigkeit umfasst die folgenden Optionen:

* Sie können Nachverfolgen, welche Eigenschaft ein Benutzer geändert hat, und nur die entsprechenden Spalten in der Datenbank aktualisieren.

  In diesem Szenario sollten keine Daten verloren gehen. Von den beiden Benutzern wurden unterschiedliche Eigenschaften aktualisiert. Das nächste Mal, wenn eine Person den englischen Fachbereich durchsucht, sieht diese die Änderungen von Benutzer1 und Benutzer2. Diese Methode der Aktualisierung kann die Anzahl von Konflikten reduzieren, die zu Datenverlust führen können. Dieser Ansatz:
 
  * Kann Datenverlust nicht verhindern, wenn konkurrierende Änderungen an der gleichen Eigenschaft gemacht werden.
  * Ist in einer Web-App in der Regel nicht praktisch. Erfordert, dass der maßgebliche Zustand beibehalten wird, um alle abgerufenen Werte und neuen Werte nachzuverfolgen. Das Verwalten von großen Datenmengen kann den Zustand der App-Leistung beeinträchtigen.
  * Kann die Anwendungskomplexität im Vergleich zur Parallelitätsermittlung für eine Entität erhöhen.

* Sie können zulassen, dass die Änderungen von Benutzer2 die Änderungen von Benutzer1 überschreiben.

  Das nächste Mal, wenn jemand den englischen Fachbereich durchsucht, wird das Datum 9.1.2013 und der wiederhergestellte Wert von 350.000 $ angezeigt. Dieses Ansatz wird *Client gewinnt*- oder *Last in Wins*-Szenario (Letzter gewinnt) genannt. (Alle Werte des Clients haben Vorrang vor dem Datenspeicher.) Wenn Sie keine Codierung für die Parallelitätsbehandlung durchführen, wird automatisch das „Client gewinnt“-Szenario ausgeführt.

* Sie können verhindern, dass die Änderungen von Benutzer2 in die Datenbank aufgenommen werden. In der Regel würde die App:

  * Eine Fehlermeldung anzeigen
  * Den aktuellen Datenstatus anzeigen
  * Dem Benutzer ermöglichen, die Änderungen erneut anzuwenden

  Dieses Szenario wird *Store Wins* (Speicher gewinnt) genannt. (Die Werte des Datenspeichers haben Vorrang gegenüber den Werten, die vom Client gesendet werden). In diesem Tutorial implementieren Sie das Szenario „Store Wins“ (Speicher gewinnt). Diese Methode stellt sicher, dass keine Änderungen überschrieben werden, ohne dass ein Benutzer darüber benachrichtigt wird.

## <a name="handling-concurrency"></a>Behandlung von Parallelität 

Wenn eine Eigenschaft als ein [Parallelitätstoken](/ef/core/modeling/concurrency) konfiguriert ist:

* Stellt EF Core sicher, dass die Eigenschaft nicht geändert wurde, nachdem sie abgerufen wurde. Die Überprüfung findet statt, wenn [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) oder [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) aufgerufen wird.
* Wenn die Eigenschaft geändert wurde, nachdem sie abgerufen wurde, wird eine [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception) ausgelöst. 

Das Datenbank- und Datenmodell müssen konfiguriert sein, um das Auslösen von `DbUpdateConcurrencyException` zu unterstützen.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Erkennen von Nebenläufigkeitskonflikten mit Eigenschaften

Nebenläufigkeitskonflikte können auf der Eigenschaftenebene über das [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute)-Attribut erkannt werden. Das Attribut kann auf mehrere Eigenschaften für das Modell angewendet werden. Weitere Informationen finden Sie unter [Datenanmerkungen-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Das Attribut `[ConcurrencyCheck]` wird in diesem Tutorial nicht verwendet.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Erkennen von Nebenläufigkeitskonflikten mit einer Zeile

Um Nebenläufigkeitskonflikte zu erkennen, wird dem Modell eine [Rowversion](/sql/t-sql/data-types/rowversion-transact-sql)-Nachverfolgungsspalte (Zeilenversion) hinzugefügt.  `rowversion` :

* Ist SQL Server-spezifisch. Andere Datenbanken enthalten möglicherweise keine ähnlichen Features.
* Wird verwendet, um zu bestimmen, dass eine Entität, seit dem Abruf aus der Datenbank, nicht geändert wurde. 

Die Datenbank generiert eine sequenzielle Anzahl von `rowversion`, die jedes Mal erhöht wird, wenn die Zeile aktualisiert wird. In einem `Update`- oder `Delete`-Befehl enthält die `Where`-Klausel den abgerufenen Wert von `rowversion`. Wenn sich die aktualisierte Zeile geändert hat, geschieht Folgendes:

* `rowversion` entspricht nicht dem abgerufenen Wert.
* Die Befehle `Update` oder `Delete` finden keine Zeile, da die `Where`-Klausel die abgerufene `rowversion` enthält.
* Es wird eine `DbUpdateConcurrencyException` ausgelöst.

In EF Core wird eine Parallelitätsausnahme ausgelöst, wenn keine Zeilen durch einen `Update`- oder `Delete`-Befehl aktualisiert werden.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Hinzufügen einer Nachverfolgungseigenschaft zur Entität „Department“

Fügen Sie der Datei *Models/Department.cs* eine Nachverfolgungseigenschaft namens „RowVersion“ hinzu:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Das [Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute)-Attribut gibt an, dass diese Spalte in der `Where`-Klausel der Befehle `Update` und `Delete` enthalten ist. Das Attribut wird `Timestamp` genannt, weil vorherige Versionen von SQL Server einen SQL-`timestamp`-Datentyp verwendet haben, bevor dieser durch SQL-`rowversion` ersetzt wurde.

Die Fluent-API kann auch die Nachverfolgungseigenschaft angeben:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Der folgende Code zeigt einen Teil von dem T-SQL an, das EF Core generiert, wenn der Name des Fachbereichs aktualisiert wird:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Das vorherige markierte Codebeispiel zeigt die `WHERE`-Klausel mit `RowVersion` an. Wenn die Datenbank `RowVersion` nicht dem `RowVersion`-Parameter (`@p2`) entspricht, werden keine Zeilen aktualisiert.

Der folgende hervorgehobene Code stellt das T-SQL dar, das genau überprüft, ob eine Zeile aktualisiert wurde:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) gibt die Anzahl der von der letzten Anweisung betroffenen Zeilen zurück. Wenn keine Zeilen aktualisiert werden, löst EF Core eine `DbUpdateConcurrencyException` aus.

Sie können das von EF Core generierte T-SQL im Ausgabefenster von Visual Studio sehen.

### <a name="update-the-db"></a>Aktualisieren der Datenbank

Das Hinzufügen der `RowVersion`-Eigenschaft ändert das Datenbankmodell, das eine Migration erfordert.

Erstellen Sie das Projekt. Geben Sie Folgendes in ein Befehlsfenster ein:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Die obenstehenden Befehle haben folgende Konsequenzen:

* Die Migrationsdatei *Migrations/{Zeitstempel}_RowVersion.cs* wird hinzugefügt.
* Es wird ein Update für die Datei *Migrations/SchoolContextModelSnapshot.cs* ausgeführt. Über dieses Update wird der `BuildModel`-Methode der folgende hervorgehobene Code hinzugefügt:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* Migrationen werden durchgeführt, um die Datenbank zu aktualisieren.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Erstellen des Gerüsts für das Abteilungsmodell

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-student-pages) aus, und verwenden Sie `Department` für die Modellklasse.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Der vorherige Befehl erstellt ein Gerüst für das `Department`-Modell. Öffnen Sie das Projekt in Visual Studio.

Erstellen Sie das Projekt.

### <a name="update-the-departments-index-page"></a>Aktualisieren der Indexseite für Abteilungen

Die Gerüstbauengine hat eine `RowVersion`-Spalte auf der Indexseite erstellt, jedoch sollte dieses Feld nicht angezeigt werden. In diesem Tutorial wird das letzte Byte der `RowVersion` angezeigt, damit Sie ein besseres Verständnis über die Parallelität erlangen. Das letzte Byte ist nicht zwingend eindeutig. Eine echte App würde `RowVersion` oder das letzte Byte von `RowVersion` nicht anzeigen.

Aktualisieren der Indexseite:

* Ersetzen Sie „Index“ durch „Abteilungen“.
* Ersetzen Sie das Markup mit `RowVersion` durch das letzten Byte von `RowVersion`.
* Ersetzen Sie „FirstMidName“durch „FullName“.

Das folgende Markup zeigt die aktualisierte Seite an:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Aktualisieren des Seitenbearbeitungsmodells

Aktualisieren Sie die Datei *Pages\Departments\Edit.cshtml.cs* mithilfe des folgenden Codes:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Um ein Nebenläufigkeitsproblem zu erkennen, wird die [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue)-Eigenschaft mit dem `rowVersion`-Wert aus der Entität aktualisiert, aus der dieser abgerufen wurde. EF Core generiert einen SQL UPDATE-Befehl mit einer WHERE-Klausel mit dem ursprünglichen `RowVersion`-Wert. Wenn keine Zeilen durch den UPDATE-Befehl betroffen sind (keine Zeile enthält den ursprünglichen `RowVersion`-Wert), wird eine `DbUpdateConcurrencyException`-Ausnahme ausgelöst.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

Im obenstehenden Code wird der Wert `Department.RowVersion` zurückgegeben, sobald die Entität abgerufen wurde. `OriginalValue` ist der Wert in der Datenbank, wenn `FirstOrDefaultAsync` in dieser Methode aufgerufen wurde.

Der folgende Code ruft die Clientwerte (die für diese Methode bereitgestellten Werte) und die Datenbankwerte ab:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Der folgende Code fügt eine benutzerdefinierte Fehlermeldung für jede Spalte ein, deren Datenbankwerte sich von jenen unterscheiden, die auf `OnPostAsync` bereitgestellt wurden:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Der folgende hervorgehobene Code legt den `RowVersion`-Wert auf den neuen Wert fest, der aus der Datenbank abgerufen wurde. Das nächste Mal, wenn der Benutzer auf **Speichern** klickt, werden nur Parallelitätsfehler abgefangen, die seit der letzten Anzeige der Bearbeitungsseite aufgetreten sind.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Die Anweisung `ModelState.Remove` ist erforderlich, da `ModelState` über den alten `RowVersion`-Wert verfügt. In der Razor-Seite hat der Wert `ModelState` Vorrang vor den Modelleigenschaftswerten, wenn beide vorhanden sind.

## <a name="update-the-edit-page"></a>Aktualisieren der Seite „Bearbeiten“

Aktualisieren Sie *Pages/Courses/Edit.cshtml* mithilfe des folgenden Markups:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Das obenstehende Markup:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.
* Zeigt das letzte Byte von `RowVersion` zu Debugzwecken an.
* Ersetzt `ViewData` durch den stark typisierten `InstructorNameSL`.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Testen von Nebenläufigkeitskonflikten mit der Seite „Bearbeiten“

Öffnen Sie für den englischen Fachbereich zwei Browserinstanzen der Seite „Bearbeiten“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Bearbeiten** für den englischen Fachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.
* Klicken Sie in der ersten Registerkarte auf den **Bearbeiten**-Link für den englischen Fachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie den Namen in der ersten Registerkarte, und klicken Sie auf **Speichern**.

![Seite 1 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-1.png)

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Ändern Sie ein anderes Feld in der zweiten Registerkarte.

![Seite 2 „Abteilung bearbeiten“ nach der Änderung](concurrency/_static/edit-after-change-2.png)

Klicken Sie auf **Speichern**. Es werden Fehlermeldungen für alle Felder angezeigt, die nicht mit den Datenbankwerten übereinstimmen:

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/edit-error.png)

In diesem Browserfenster sollte nicht das Namensfeld geändert werden. Kopieren Sie den aktuellen Wert (Sprachen), und fügen Sie ihn in das Namensfeld ein. Wechseln Sie durch Drücken der TAB-Taste zum nächsten Feld. Im Rahmen der clientseitigen Überprüfung wird die Fehlermeldung entfernt.

![Fehlermeldung auf der Seite „Abteilung bearbeiten“](concurrency/_static/cv.png)

Klicken Sie erneut auf **Speichern**. Der Wert, den Sie auf der zweiten Registerkarte eingegeben haben, wird gespeichert. Die gespeicherten Werte werden auf der Indexseite angezeigt.

## <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie das Seitenmodell „Löschen“ mit dem folgenden Code:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Die Seite „Löschen“ erkennt Nebenläufigkeitskonflikte, wenn die Entität geändert wurde, nachdem sie abgerufen wurde. Bei `Department.RowVersion` handelt es sich um die Zeilenversion, nachdem die Entität abgerufen wurde. Wenn EF Core den SQL-DELETE-Befehl erstellt, umfasst dieser eine WHERE-Klausel mit `RowVersion`. Wenn die Ergebnisse des SQL-DELETE-Befehls 0 (null) betroffene Zeilen ergeben:

* Stimmt die `RowVersion` im SQL-DELETE-Befehl nicht mit `RowVersion` in der Datenbank überein.
* Wird eine DbUpdateConcurrencyException-Ausnahme ausgelöst.
* Wird `OnGetAsync` mit `concurrencyError` aufgerufen.

### <a name="update-the-delete-page"></a>Aktualisieren der Seite „Delete“ (Löschen)

Aktualisieren Sie die *Pages\Departments\Delete.cshtml*-Datei mithilfe des folgenden Codes:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Durch den vorangehenden Code werden folgende Änderungen vorgenommen:

* Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int}"`.
* Eine Fehlermeldung wird hinzugefügt.
* „FirstMidName“ wird durch „FullName“ im Feld **Administrator** ersetzt.
* `RowVersion` wird geändert, um das letzte Byte anzuzeigen.
* Fügt eine ausgeblendete Zeilenversion hinzu. `RowVersion` muss hinzugefügt werden, damit über ein Postback-Ereignis der Wert gebunden werden kann.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Überprüfen von Nebenläufigkeitskonflikten mit der Seite „Löschen“

Erstellen Sie einen Fachbereich zum Testen.

Öffnen Sie im Testfachbereich zwei Browserinstanzen zum „Löschen“:

* Führen Sie die Anwendung aus, und wählen Sie „Abteilungen“ aus.
* Klicken Sie mit der rechten Maustaste auf den Link **Löschen** für den Testfachbereich, und klicken Sie auf **In neuer Registerkarte öffnen**.
* Klicken Sie auf den Link **Bearbeiten** für den Testfachbereich.

Beide Registerkarten zeigen die gleichen Informationen an.

Ändern Sie das Budget in der ersten Registerkarte, und klicken Sie auf **Speichern**.

Der Browser zeigt die Indexseite mit dem geänderten Wert und dem aktualisierten RowVersion-Indikator an. Beachten Sie, dass der aktualisierte RowVersion-Indikator beim zweiten Postback-Ereignis auf der anderen Registerkarte angezeigt wird.

Löschen Sie den Testfachbereich aus der zweiten Registerkarte. Ein Parallelitätsfehler wird mit den aktuellen Werten aus der Datenbank angezeigt. Durch Klicken auf **Löschen** wird die Entität gelöscht, es sei denn, `RowVersion` wurde aktualisiert.

Informationen zum Vererben eines Datenmodells finden Sie unter [Vererbung](xref:data/ef-mvc/inheritance).

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Concurrency Tokens in EF Core (Parallelitätstoken in EF Core)](/ef/core/modeling/concurrency)
* [Handle concurrency in EF Core (Handhabung von Parallelität in EF Core)](/ef/core/saving/concurrency)
* [Dieses Tutorial auf YouTube (Behandlung von Parallelitätskonflikten)](https://youtu.be/EosxHTFgYps)
* [Dieses Tutorial auf YouTube (Teil 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Dieses Tutorial auf YouTube (Teil 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Vorherige](xref:data/ef-rp/update-related-data)

::: moniker-end

