---
title: 'Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core'
description: In diesem Tutorial werden wichtige Themen eingeführt, um Grundkenntnisse der Entwicklung von ASP.NET Core-Web-Apps, die Entity Framework Core verwenden, zu erweitern.
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054384"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core

Im vorherigen Tutorial haben Sie die „Tabelle pro Hierarchie“-Vererbung implementiert. In diesem Tutorial werden verschiedene Themen eingeführt, die beim Entwickeln komplexerer ASP.NET Core-Webanwendungen nützlich sein können, die Entity Framework Core verwenden.

In diesem Tutorial:

> [!div class="checklist"]
> * Durchführen unformatierter SQL-Abfragen
> * Aufrufen einer Abfrage zum Zurückgeben von Entitäten
> * Aufrufen einer Abfrage zum Zurückgeben von Typen
> * Abrufen einer Aktualisierungsabfrage
> * Untersuchen von SQL-Abfragen
> * Erstellen einer Abstraktionsschicht
> * Informationen zur automatischen Änderungserkennung
> * Informationen zum EF Core-Quellcode und zu Entwicklungsplänen
> * Informationen zum Verwenden dynamischer LINQs zum Vereinfachen des Codes

## <a name="prerequisites"></a>Voraussetzungen

* [Implementieren von Vererbung](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Durchführen unformatierter SQL-Abfragen

Einer der Vorteile von Entity Framework ist die Tatsache, dass vermieden wird, den Code zu eng an eine bestimmte Methode zum Speichern von Daten zu binden. Dies geschieht, indem SQL-Abfragen und -Befehle für Sie generiert werden. Somit müssen Sie sie nicht selbst schreiben. Aber es gibt außergewöhnliche Szenarios, für die Sie bestimmte SQL-Abfragen ausführen müssen, die Sie manuell erstellt haben. Bei diesen Szenarios enthält die erste API des Entity Framework Code Methoden, mit denen Sie SQL-Befehle direkt an die Datenbank übergeben können. In EF Core 1.0 verfügen Sie über die folgenden Optionen:

* Verwenden Sie die `DbSet.FromSql`-Methode für Abfragen, die Entitätstypen zurückgeben. Die Typen der zurückgegebenen Objekte müssen den Erwartungen des `DbSet`-Objekts entsprechen, und sie werden automatisch vom Datenbankkontext nachverfolgt, außer wenn Sie die [Überwachung deaktivieren](crud.md#no-tracking-queries).

* Verwenden Sie den `Database.ExecuteSqlCommand` für Nichtabfragebefehle.

Wenn Sie eine Abfrage ausführen müssen, die Typen zurückgibt, die keine Entitäten sind, können Sie mit der von EF bereitgestellten Datenbankverbindung ADO.NET verwenden. Die zurückgegebenen Daten werden nicht vom Datenbankkontext nachverfolgt, auch wenn Sie diese Methode zum Abrufen von Entitätstypen verwenden.

Dies trifft immer zu, wenn Sie SQL-Befehle in einer Webanwendung ausführen. Treffen Sie deshalb Vorsichtsmaßnahmen, um Ihre Website vor Angriffen durch Einschleusung von SQL-Befehlen zu schützen. Verwenden Sie dazu parametrisierte Abfragen, um sicherzustellen, dass Zeichenfolgen, die von einer Webseite übermittelt werden, nicht als SQL-Befehle interpretiert werden können. In diesem Tutorial verwenden Sie parametrisierte Abfragen beim Integrieren von Benutzereingaben in eine Abfrage.

## <a name="call-a-query-to-return-entities"></a>Aufrufen einer Abfrage zum Zurückgeben von Entitäten

Die `DbSet<TEntity>`-Klasse stellt eine Methode zur Verfügung, die Sie verwenden können, um eine Abfrage auszuführen, die eine Entität des Typs `TEntity` zurückgibt. Ändern Sie den Code in der `Details`-Methode des Abteilungscontrollers, und Sie werden sehen, wie sie funktioniert.

Ersetzen Sie, wie im folgenden hervorgehobenen Code gezeigt, in *DepartmentsController.cs* in der `Details`-Methode den Code, der eine Abteilung mit einem `FromSql`-Methodenaufruf abruft:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Wählen Sie die Registerkarte **Departments** (Abteilungen) und dann **Details** für eine der Abteilungen aus. So können Sie überprüfen, ob der neue Code korrekt funktioniert.

![Fakultätsdetails](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Aufrufen einer Abfrage zum Zurückgeben von Typen

Sie haben zuvor ein Statistikraster für Studenten für die Infoseite erstellt, das die Anzahl der Studenten für jedes Anmeldedatum zeigt. Sie haben die Daten aus der Entitätssammlung für Studenten (`_context.Students`) und LINQ verwendet, um die Ergebnisse in eine Liste von `EnrollmentDateGroup`-Ansichtsmodellobjekten zu projizieren. Angenommen, Sie möchten die SQL selbst schreiben, anstatt LINQ zu verwenden. Hierzu müssen Sie eine SQL-Abfrage ausführen, die etwas anderes als Entitätsobjekte zurückgibt. In EF Core 1.0 gibt es die Möglichkeit, ADO.NET-Code zu schreiben und die Datenbankverbindung von EF abzurufen.

Ersetzen Sie in *HomeController.cs* die `About`-Methode durch den folgenden Code:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Fügen Sie eine Using-Anweisung hinzu:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Führen Sie die Anwendung aus. Wechseln Sie zur Infoseite. Sie zeigt die gleichen Daten wie zuvor.

![Infoseite](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Abrufen einer Aktualisierungsabfrage

Nehmen wir an, dass Administratoren der Contoso University globale Änderungen in der Datenbank durchführen möchten, z.B. die Anzahl der Credits für jeden Kurs ändern. Wenn die Universität über eine große Anzahl an Kursen verfügt, wäre es ineffizient, sie alle als Entitäten abzurufen und separat zu ändern. In diesem Abschnitt implementieren Sie eine Webseite, die es dem Benutzer ermöglicht, einen Faktor anzugeben, nach dem die Anzahl der Credits für alle Kurse geändert werden kann. Sie werden die Änderung durch die Ausführung einer SQL UPDATE-Anweisung durchführen. Die Webseite wird wie die folgende Abbildung aussehen:

![Seite zum Aktualisieren der Credits für Kurse](advanced/_static/update-credits.png)

Fügen Sie in *CoursesController.cs* UpdateCourseCredits-Methoden für HttpGet und HttpPost hinzu:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Wenn der Controller eine HttpGet-Anforderung verarbeitet, wird nichts in `ViewData["RowsAffected"]` zurückgegeben, die Ansicht zeigt wie in der vorherigen Abbildung dargestellt ein leeres Textfeld und eine „Absenden“-Schaltfläche.

Wenn auf die Schaltfläche **Aktualisieren** geklickt wird, wird die HttpPost-Methode aufgerufen, und der Multiplikator hat den Wert in das Textfeld eingegeben. Der Code führt dann das SQL aus, das Kurse aktualisiert und die Anzahl der betroffenen Zeilen an die Ansicht in `ViewData` zurückgibt. Wenn die Ansicht einen `RowsAffected`-Wert erhält, zeigt sie die Anzahl der aktualisierten Zeilen an.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Ansichten/Kurse*, und klicken Sie anschließend auf **Hinzufügen > Neues Element**.

Klicken Sie im Dialogfeld **Neues Element hinzufügen** unter **Installiert** im linken Bereich auf **ASP.NET Core**, klicken Sie auf **RazorRazor-Ansicht**, und nennen Sie die neue Ansicht *UpdateCourseCredits.cshtml*.

Ersetzen Sie in *Views/Courses/UpdateCourseCredits.cshtml* den Vorlagencode durch den folgenden Code:

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

Führen Sie die `UpdateCourseCredits`-Methode aus, indem Sie die Registerkarte **Courses** (Kurse) auswählen, und dann „/UpdateCourseCredits“ am Ende der URL in die Adressleiste des Browsers einfügen (z.B.: `http://localhost:5813/Courses/UpdateCourseCredits`). Geben Sie eine Zahl in das Textfeld ein:

![Seite zum Aktualisieren der Credits für Kurse](advanced/_static/update-credits.png)

Klicken Sie auf **Aktualisieren**. Die Anzahl der betroffenen Zeilen wird angezeigt:

![Seite zum Aktualisieren der Credits für Kurse, betroffene Zeilen](advanced/_static/update-credits-rows-affected.png)

Klicken Sie auf **Zurück zur Liste**, um die Kursliste mit der geänderte Anzahl von Credits anzuzeigen.

Beachten Sie, dass der Produktionscode sicherstellen würde, dass Aktualisierungen immer zu gültigen Daten führen. Der hier dargestellte vereinfachte Code könnte die Anzahl der Credits so verändern, dass sie Zahlen größer als fünf ergeben. (Die `Credits`-Eigenschaft verfügt über ein `[Range(0, 5)]`-Attribut.) Die Updateabfrage würde funktionieren, aber die ungültigen Daten könnten zu unerwarteten Ergebnissen in anderen Teilen des Systems führen, die davon ausgehen, dass die Anzahl der Credits fünf oder weniger beträgt.

Weitere Informationen zu unformatierten SQL-Abfragen finden Sie unter [Unformatierte SQL-Abfragen](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>Untersuchen von SQL-Abfragen

Manchmal ist es hilfreich, die tatsächlichen SQL-Abfragen anzuzeigen, die an die Datenbank gesendet werden. Die integrierte Protokollfunktion für ASP.NET Core wird automatisch von EF Core verwendet, um Protokolle zu schreiben, die SQL für Abfragen und Updates enthalten. In diesem Abschnitt sehen Sie einige Beispiele für die SQL-Protokollierung.

Öffnen Sie *StudentsController.cs*, und legen Sie in der `Details`-Methode einen Haltepunkt auf der `if (student == null)`-Anweisung fest.

Führen Sie die Anwendung im Debugmodus aus. Wechseln Sie zur Detailseite für Studenten.

Wechseln Sie zum **Ausgabe**-Fenster, zeigen Sie die Debugausgabe an, und Sie sehen die Abfrage:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Sie sehen hier etwas, das Sie möglicherweise überrascht: SQL wählt bis zu zwei Zeilen (`TOP(2)`) aus der Personentabelle aus. Die `SingleOrDefaultAsync`-Methode wird nicht nach einer Zeile auf dem Server aufgelöst. Erläuterung:

* Wenn die Abfrage mehrere Zeilen zurückgibt, gibt die Methode 0 (null) zurück.
* Um zu bestimmen, ob die Abfrage mehrere Zeilen zurückgeben würde, muss EF überprüfen, ob mindestens zwei zurückgegeben werden.

Beachten Sie, dass Sie nicht den Debugmodus verwenden und an einem Haltepunkt anhalten müssen, um die Protokollierungsausgabe im **Ausgabe**-Fenster anzuzeigen. Es ist einfach praktisch, die Protokollierung an dem Punkt zu anzuhalten, an dem Sie die Ausgabe ansehen möchten. Wenn Sie dies nicht tun, wird die Protokollierung fortgesetzt, und Sie müssen scrollen, bis Sie die für Sie interessanten Stellen finden.

## <a name="create-an-abstraction-layer"></a>Erstellen einer Abstraktionsschicht

Viele Entwickler schreiben Code, um das Repository- und Arbeitseinheitsmuster als Wrapper um den Code zu implementieren, der mit dem Entity Framework arbeitet. Diese Muster sollen eine Abstraktionsebene zwischen der Datenzugriffsebene und den Geschäftslogikebene einer Anwendung erstellen. Die Implementierung dieser Muster unterstützt die Isolation Ihrer Anwendung vor Änderungen im Datenspeicher und kann automatisierte Komponententests oder eine testgesteuerte Entwicklung (Test-Driven Development, TDD) erleichtern. Das Schreiben von zusätzlichem Code zum Implementieren dieser Muster ist jedoch nicht immer die beste Wahl für Anwendungen, die EF verwenden. Dies hat mehrere Gründe:

* Die EF-Kontextklasse isoliert Ihren Code selbst vor datenspeicherspezifischem Code.

* Die EF-Kontextklasse kann als Arbeitseinheitsklasse für Updates der Datenbank fungieren, die Sie mithilfe von EF ausführen.

* EF enthält Features zur TDD-Implementierung ohne Repository-Code zu schreiben.

Weitere Informationen zur Implementierung der Repository- und Arbeitseinheitsmuster finden Sie in der [Version Entity Framework 5 dieser Tutorialreihe](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).

Entity Framework Core implementiert einen speicherinternen Datenbankanbieter, der für Tests verwendet werden kann. Weitere Informationen finden Sie unter [Test with InMemory (Testen mit InMemory)](/ef/core/miscellaneous/testing/in-memory).

## <a name="automatic-change-detection"></a>Automatische Änderungserkennung

Entity Framework bestimmt wie eine Entität geändert wurde (und welche Updates an die Datenbank gesendet werden müssen), indem die aktuellen Werte einer Entität mit den ursprünglichen Werten verglichen werden. Die ursprünglichen Werte werden gespeichert, wenn die Entität abgefragt oder angefügt wird. Einige der Methoden, die automatisch eine Änderungserkennung durchführen, sind die folgenden:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Entries

Wenn Sie eine große Anzahl von Entitäten überwachen und eine dieser Methoden oft in einer Schleife aufrufen, erhalten Sie möglicherweise erhebliche Leistungssteigerungen durch vorübergehendes Deaktivieren der automatischen Änderungserkennung mithilfe der `ChangeTracker.AutoDetectChangesEnabled`-Eigenschaft. Zum Beispiel:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core-Quellcode und Entwicklungspläne

Die Quelle von Entity Framework Core befindet sich unter [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore). Das EF Core-Repository enthält über Nacht erstellte Builds, Problemverfolgung, Featurespezifikationen, Notizen der Designbesprechungen und [die Roadmap für künftige Entwicklungen](https://github.com/dotnet/efcore/wiki/Roadmap). Sie können Fehler finden oder protokollieren und beitragen.

Obwohl der Quellcode Open Source ist, wird Entity Framework Core als ein Microsoft-Produkt vollständig unterstützt. Das Microsoft Entity Framework-Team überprüft, welche Beiträge akzeptiert werden. Es testet alle Codeänderungen, um die Qualität jedes Release zu garantieren.

## <a name="reverse-engineer-from-existing-database"></a>Reverse Engineering aus der bestehenden Datenbank

Verwenden Sie zum Zurückentwickeln (Reverse Engineering) eines Datenmodells, einschließlich der Entitätsklassen aus einer vorhandenen Datenbank, den Befehl [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext). Lesen Sie das [Tutorial Erste Schritte](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Verwenden dynamischer LINQs zum Vereinfachen des Codes

Das [dritte Tutorial dieser Reihe](sort-filter-page.md) zeigt, wie Sie LINQ-Code schreiben, indem Sie eine Hartcodierung der Spaltennamen in einer `switch`-Anweisung durchführen. Mit zwei Spalten zur Auswahl funktioniert dies hervorragend, aber wenn Sie viele Spalten zur Verfügung haben, könnte der Code ausführlich werden. Zur Behebung dieses Problems können Sie die `EF.Property`-Methode verwenden, um den Namen der Eigenschaft als Zeichenfolge anzugeben. Ersetzen Sie die `Index`-Methode im `StudentsController` durch den folgenden Code, um diesen Ansatz auszuprobieren.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Danksagungen

Tom Dykstra und Rick Anderson (Twitter @RickAndMSFT) haben dieses Tutorial verfasst. Rowan Miller, Diego Vega und andere Mitglieder des Entity Framework-Teams haben uns bei Codereviews und der Behebung von Problemen unterstützt, die aufgetreten waren, während wir den Code für dieses Tutorial geschrieben haben. John Parente und Paul Goldman arbeiteten an der Aktualisierung des Tutorials für ASP.NET Core 2.2.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Problembehandlung bei häufigen Fehlern

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll wird von einem anderen Prozess verwendet

Fehlermeldung:

> Kann nicht geöffnet werden „... bin\Debug\netcoreapp1.0\ContosoUniversity.dll“ zum Schreiben: „Der Prozess kann nicht auf die Datei „... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll“ zugreifen, da sie von einem anderen Prozess verwendet wird.

Projektmappe:

Beenden Sie die Website in IIS Express. Wechseln Sie zur Windows-Taskleiste, suchen Sie IIS Express, klicken Sie mit der rechten Maustaste auf das Symbol, wählen Sie den Standort Contoso University aus, und klicken Sie dann auf **Website beenden**.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Gerüstete Migration ohne Code in Up-und Down-Methoden

Mögliche Ursache:

EF-CLI-Befehle schließen und speichern die Codedateien nicht automatisch. Wenn Sie nicht gespeicherte Änderungen haben, wenn Sie den `migrations add`-Befehl ausführen, wird EF Ihre Änderungen nicht finden.

Projektmappe:

Führen Sie den `migrations remove`-Befehl aus, speichern Sie Ihre Codeänderungen, führen Sie den `migrations add`-Befehl erneut aus.

### <a name="errors-while-running-database-update"></a>Fehler beim Ausführen von Datenbankupdates

Es ist möglich, dass andere Fehler auftreten, wenn Schemaänderungen in einer Datenbank durchgeführt werden, die vorhandene Daten enthält. Wenn Sie Migrationsfehler erhalten, die Sie nicht beheben können, können Sie den Datenbanknamen in der Verbindungszeichenfolge ändern oder die Datenbank löschen. Mit einer neuen Datenbank gibt es keine zu migrierenden Daten, und der Update-Database-Befehl wird wahrscheinlich ohne Fehler abgeschlossen werden.

Der einfachste Ansatz besteht in der Neubenennung der Datenbank in *appsettings.json* . Das nächste Mal, wenn Sie `database update` ausführen, wird eine neue Datenbank erstellt.

Zum Löschen einer Datenbank im SSOX, klicken Sie mit der rechten Maustaste auf die Datenbank. Klicken Sie auf **Löschen**, wählen Sie dann im Dialogfeld **Datenbank löschen** **Bestehende Verbindungen schließen** aus, und klicken Sie auf **OK**.

Führen Sie zum Löschen einer Datenbank mithilfe der CLI den `database drop`-CLI-Befehl aus:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>Fehler beim Bestimmen der SQL Server-Instanz

Fehlermeldung:

> Ein netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server. Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden. Stellen Sie sicher, dass der Instanzname richtig und SQL Server so konfiguriert ist, das Remoteverbindungen zulässig sind. (Anbieter: SQL-Netzwerkschnittstellen, Fehler: 26: Fehler beim Suchen des angegebenen Servers/der angegebenen Instanz)

Projektmappe:

Überprüfen Sie die Verbindungszeichenfolge. Wenn Sie die Datenbankdatei manuell gelöscht haben, ändern Sie den Namen der Datenbank in der Konstruktionszeichenfolge, um mit einer neuen Datenbank zu beginnen.

## <a name="get-the-code"></a>Abrufen des Codes

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen zu EF Core finden Sie in der [Dokumentation zu Entity Framework Core](/ef/core). Zu diesem Thema ist auch ein Buch verfügbar: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action)

Weitere Informationen zum Bereitstellen einer Webanwendung finden Sie unter <xref:host-and-deploy/index>.

Weitere Informationen zu anderen Themen im Zusammenhang mit ASP.NET Core MVC, wie beispielsweise Authentifizierung und Autorisierung, finden Sie unter <xref:index>.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben Sie unformatierte SQL-Abfragen durchgeführt
> * Haben Sie eine Abfrage zum Zurückgeben von Entitäten aufgerufen
> * Haben Sie eine Abfrage zum Zurückgeben anderer Typen aufgerufen
> * Haben Sie eine Aktualisierungsabfrage aufgerufen
> * Haben Sie SQL-Abfragen untersucht
> * Haben Sie eine Abstraktionsschicht erstellt
> * Haben Sie Informationen zur automatischen Änderungserkennung erhalten
> * Haben Sie Informationen zum EF Core-Quellcode und zu Entwicklungsplänen erhalten
> * Haben Sie Informationen zum Verwenden dynamischer LINQs zum Vereinfachen des Codes erhalten

Dies schließt die verschiedenen Tutorials zur Verwendung von Entity Framework Core in einer ASP.NET Core MVC-Anwendung ab. Diese Serie hat mit einer neuen Datenbank gearbeitet; eine Alternative ist es, ein Modell aus einer vorhandenen Datenbank zurückzuentwickeln.

> [!div class="nextstepaction"]
> [Tutorial: Erste Schritte mit EF Core in ASP.NET Core mit einer neuen Datenbank](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
