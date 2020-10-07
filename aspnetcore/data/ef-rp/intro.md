---
title: Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8
author: rick-anderson
description: Informationen zum Erstellen einer Razor Pages-App mit Entity Framework Core
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424280"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor Pages mit Entity Framework Core in ASP.NET Core – Tutorial 1 bis 8

Von [Tom Dykstra](https://github.com/tdykstra) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird. In den Tutorials wird eine Website für eine fiktive Contoso University erstellt. Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten. In diesem Tutorial wird der Code-First-Ansatz verwendet. Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a>Datenbank-Engines

Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.

In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.

Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.

## <a name="the-sample-app"></a>Die Beispiel-App

Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität. Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen. Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen. Das Tutorial konzentriert sich auf die Verwendung von EF Core mit ASP.NET Core und nicht auf die Anpassung der Benutzeroberfläche.

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a>Erstellen des Web-App-Projekts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Geben Sie dem Projekt den Namen *ContosoUniversity*. Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.
* Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 5.0** und anschließend **Webanwendung** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.
* Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *Pages/Shared/_Layout.cshtml* ein: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Mit der Layoutdatei werden die Kopfzeile, die Fußzeile und das Menü der Website festgelegt. Durch den vorangehenden Code werden folgende Änderungen vorgenommen:

* Jedes Vorkommen von „ContosoUniversity“ wird in „Contoso University“ geändert. Diese Begriffskombination kommt dreimal vor.
* Die Menüeinträge **Home** und **Privacy** werden gelöscht.
* Es werden Einträge für **About**, **Students**, **Courses**, **Instructors** und **Departmens** hinzugefügt.

Ersetzen Sie den Inhalt der Datei *Pages/Index.cshtml* durch den folgenden Code:

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

Der obige Code ersetzt den Text über ASP.NET Core durch Text über diese App.

Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.

## <a name="the-data-model"></a>Das Datenmodell

In den folgenden Abschnitten wird ein Datenmodell erstellt:

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.

## <a name="the-student-entity"></a>Die Entität „Student“

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* Erstellen Sie im Projektordner den Ordner *Models*. 

* Erstellen Sie *Models/Student.cs* mit folgendem Code:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht. Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel. Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`. Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).

Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships). Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind. In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen. Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten. 

In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält. Angenommen, eine Student-Zeile weist die ID=1 auf. Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf. StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle. 

Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt. Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`. Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.

## <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`. Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich. In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren. Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.

Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`. Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/). Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben. Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist. Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben. Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.

## <a name="the-course-entity"></a>Die Entität „Course“

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

Erstellen Sie *Models/Course.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.

Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.

## <a name="scaffold-student-pages"></a>Gerüstbau der Student-Seiten

In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:

* Eine EF Core `DbContext`-Klasse. „context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert. Diese Klasse wird von der <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>-Klasse abgeleitet.
* Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Erstellen Sie einen Ordner *Pages/Students*.
* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.
* Im Dialogfeld **Add New Scaffold Item** (Neues Gerüstelement hinzufügen):
  * Wählen Sie auf der linken Registerkarte **Installiert > Häufig > Razor-Seiten** aus.
  * Wählen Sie **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen** aus.
* Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:
  * Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.
  * Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.
    * Ändern Sie den Datenkontextnamen so, dass er auf `SchoolContext` anstatt auf `ContosoUniversityContext` endet. Der aktualisierte Kontextname lautet `ContosoUniversity.Data.SchoolContext`.
   * Wählen Sie **Hinzufügen** aus.

Die folgenden Pakete werden automatisch installiert:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich. Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.

* Erstellen Sie einen Ordner *Pages/Students*.

* Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.

  **Unter Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  **Unter macOS oder Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

Wenn der vorherige Schritt zu einem Fehler führt, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.

Der Gerüstbauprozess:

* Erstellt Razor Pages im Ordner *Pages/Students*:
  * *Create.cshtml* und *Create.cshtml.cs*
  * *Create.cshtml* und *Delete.cshtml.cs*
  * *Details.cshtml* und *Details.cshtml.cs*
  * *Edit.cshtml* und *Edit.cshtml.cs*
  * *Index.cshtml* und *Index.cshtml.cs*
* Erstellt *Data/SchoolContext.cs*.
* Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.
* Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.

## <a name="database-connection-string"></a>Datenbankverbindungszeichenfolge

Das Gerüstbautool generiert eine Verbindungszeichenfolge in der Datei *appsettings.json*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an:

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist. Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Kürzen Sie die SQLite-Verbindungszeichenfolge auf *CU.db*:

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualisieren der Datenbankkontextklasse

Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert. Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet. Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind. In diesem Projekt heißt die Klasse `SchoolContext`.

Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Der obige Code ändert den Singular `DbSet<Student> Student` in den Plural `DbSet<Student> Students`. Nehmen Sie eine globale Änderung vor, damit der Code der Razor-Seite mit dem neuen Namen `DBSet` übereinstimmt: `_context.Student.`
in `_context.Students.`

Es gibt 8 Vorkommen.

Da eine Entitätenmenge mehrere Entitäten enthält, bevorzugen viele Entwickler den Plural für `DBSet`-Eigenschaftennamen.

Der hervorgehobene Code:

* Erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge. Das heißt für Entity Framework Core:
  * Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.
  * Entitäten entsprechen Zeilen in Tabellen.
* Ruft <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>. `OnModelCreating`:
  * Wird aufgerufen, wenn `SchoolContext` initialisiert wurde, bevor jedoch das Modell gesperrt und zum Initialisieren des Kontexts verwendet wurde.
  * Ist erforderlich, da die `Student`-Entität später im Tutorial Verweise auf die anderen Entitäten enthält.
  <!-- Review, OnModelCreating needs review -->

Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste (z. B. `SchoolContext`) werden per Abhängigkeitsinjektion während des App-Starts registriert. Komponenten, die diese Dienste erfordern (z. B. Razor-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.

Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Die folgenden hervorgehobenen Zeilen wurden beim Gerüstbau hinzugefügt:

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Stellen Sie sicher, dass der beim Gerüstbau hinzugefügte Code `UseSqlite` aufruft.

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

Weitere Informationen zur Verwendung einer Produktionsdatenbank finden Sie unter [Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production).

---

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.

### <a name="add-the-database-exception-filter"></a>Hinzufügen des Filters für die Datenbankausnahme

Fügen Sie `AddDatabaseDeveloperPageExceptionFilter` in `ConfigureServices` hinzu, wie im folgenden Code dargestellt:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

Fügen Sie das NuGet-Paket [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) hinzu.

Geben Sie in der PMC den folgenden Befehl ein, um das NuGet-Paket hinzuzufügen:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

Das NuGet-Paket `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` stellt ASP.NET Core-Middleware für Entity Framework Core-Fehlerseiten bereit. Diese Middleware hilft bei der Erkennung und Diagnose von Fehlern bei Entity Framework Core-Migrationen.

## <a name="create-the-database"></a>Erstellen der Datenbank

Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist. Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt. `EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:

* Löschen der Datenbank. Alle vorhandenen Daten gehen verloren.
* Ändern des Datenmodells. Beispielsweise Hinzufügen eines `EmailAddress`-Felds.
* Führen Sie die App aus.
* `EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.

Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen. Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten. Wenn dies der Fall ist, verwenden Sie Migrationen.

Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen. Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.

### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus.
* Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.
* Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Die `EnsureCreated`-Methode erstellt eine leere Datenbank. In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.

Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Der Code überprüft, ob Studenten in der Datenbank vorhanden sind. Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt. Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.

Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:

```powershell
Drop-Database -Confirm
```

Antworten Sie mit `Y`, um die Datenbank zu löschen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.

---

* Starten Sie die App neu.
* Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.

## <a name="view-the-database"></a>Zeigen Sie die Datenbank an

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).
* Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus. Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.
* Erweitern Sie den Knoten **Tabellen**.
* Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.
* Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde. Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.

---

## <a name="asynchronous-code"></a>Asynchroner Code

Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.

Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht. Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.

Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:
  * Er soll Rückrufe für Teile des Methodentexts generieren.
  * Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.
* Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.
* Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt. Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird. Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.
* Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.

Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:

* Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen. Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`. Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.
* Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.
* Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a>Überlegungen zur Leistung

Im Allgemeinen sollte eine Webseite keine beliebige Anzahl von Zeilen laden. Eine Abfrage sollte Paging oder ein Verfahren zum Einschränken verwenden. Die vorherige Abfrage könnte z. B. `Take` verwenden, um die Anzahl zurückgegebener Zeilen einzuschränken:

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

Durch das Auflisten einer großen Tabelle in einer Sicht kann eine teilweise konstruierte HTTP 200-Antwort zurückgegeben werden, wenn während des Durchlaufens eine Datenbankausnahme auftritt.

Der Standardwert von <xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> ist 1.024. Der folgende Code legt `MaxModelBindingCollectionSize` fest.

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

Das Paging wird später im Tutorial beschrieben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="step-by-step"]
> [Nächstes Tutorial](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Dies ist das erste einer Reihe von Tutorials, die zeigen, wie Entity Framework (EF) Core für eine [Razor Pages-App mit ASP.NET Core](xref:razor-pages/index) verwendet wird. In den Tutorials wird eine Website für eine fiktive Contoso University erstellt. Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten. In diesem Tutorial wird der Code-First-Ansatz verwendet. Informationen zum Durcharbeiten dieses Tutorials mit dem Database-First-Ansatz finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie noch nicht mit Razor Pages vertraut sind, arbeiten Sie zuerst die Tutorialreihe [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) durch, bevor Sie mit diesem Tutorial beginnen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Datenbank-Engines

Die Visual Studio-Anweisungen verwenden [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), eine Version von SQL Server Express, die nur unter Windows ausgeführt werden kann.

In den Visual Studio Code-Anweisungen wird [SQLite](https://www.sqlite.org/) verwendet, eine plattformübergreifende Datenbank-Engine.

Wenn Sie SQLite verwenden möchten, laden Sie ein Drittanbietertool zum Verwalten und Anzeigen einer SQLite-Datenbank (z.B. [DB Browser für SQLite](https://sqlitebrowser.org/)) herunter, und installieren Sie es.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, vergleichen Sie Ihren den Code mit dem [vollständigen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples). Eine gute Möglichkeit, Hilfe zu erhalten, besteht darin, eine Frage auf StackOverflow.com zu stellen und dabei das [ASP.NET Core-Tag](https://stackoverflow.com/questions/tagged/asp.net-core) oder das [EF Core-Tag](https://stackoverflow.com/questions/tagged/entity-framework-core) zu verwenden.

## <a name="the-sample-app"></a>Die Beispiel-App

Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität. Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen. Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.

![Indexseite „Studenten“](intro/_static/students-index30.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit30.png)

Der Benutzeroberflächenstil dieser Website basiert auf den integrierten Projektvorlagen. Das Tutorial konzentriert sich auf die Verwendung von EF Core und nicht auf die Anpassung der Benutzeroberfläche.

Folgen Sie dem Link am oberen Rand der Seite, um den Quellcode für das vollständige Projekt abzurufen. Der Ordner *cu30* enthält den Code für die ASP.NET Core 3.0-Version des Tutorials. Dateien, die den Status des Codes für die Tutorials 1 bis 7 widerspiegeln, finden Sie im Ordner *cu30snapshots*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:

* Erstellen Sie das Projekt.
* Führen Sie folgenden Befehl in der Paket-Manager-Konsole aus:

  ```powershell
  Update-Database
  ```

* Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

So führen Sie die APP nach dem Herunterladen des vollständigen Projekts aus:

* Löschen Sie *ContosoUniversity.csproj*, und benennen Sie *ContosoUniversitySQLite.csproj* in *ContosoUniversity.csproj* um.
* Kommentieren Sie in *Program.cs* `#define Startup` aus, sodass `StartupSQLite` verwendet wird.
* Löschen Sie *appSettings.json*, und benennen Sie *appSettingsSQLite.json* in *appSettings.json* um.
* Löschen Sie den Ordner *Migrations*, und benennen Sie *MigrationsSQL* in *Migrations* um.
* Führen Sie eine globale Suche nach `#if SQLiteVersion` aus, und entfernen Sie `#if SQLiteVersion` sowie die zugehörige `#endif`-Anweisung.
* Erstellen Sie das Projekt.
* Führen Sie an der Eingabeaufforderung im Projektordner die folgenden Befehle aus:

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* Führen Sie in Ihrem SQLite-Tool die folgende SQL-Anweisung aus:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* Führen Sie das Projekt aus, um das Seeding der Datenbank auszuführen.

---

## <a name="create-the-web-app-project"></a>Erstellen des Web-App-Projekts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Wählen Sie **ASP.NET Core-Webanwendung** aus.
* Geben Sie dem Projekt den Namen *ContosoUniversity*. Es ist wichtig, genau diesen Namen unter Beachtung von Groß-/Kleinschreibung zu verwenden, sodass die Namespaces beim Kopieren und Einfügen von Code übereinstimmen.
* Wählen Sie in den Dropdownlisten **.NET Core** und **ASP.NET Core 3.0** und anschließend **Webanwendung** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Navigieren Sie in einem Terminal zu dem Ordner, in dem der Projektordner erstellt werden soll.

* Führen Sie die folgenden Befehle aus, um ein Razor Pages-Projekt zu erstellen und per `cd` in den neuen Projektordner zu wechseln:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Richten Sie die Kopfzeile, die Fußzeile und das Menü der Website durch Aktualisieren von *Pages/Shared/_Layout.cshtml* ein:

* Ändern Sie jedes „ContosoUniversity“ in „Contoso University“. Diese Begriffskombination kommt dreimal vor.

* Löschen Sie die Menüeinträge **Home** (Start) und **Privacy** (Datenschutz), und fügen Sie Einträge für **About** (Info), **Students** (Studenten), **Courses** (Kurse), **Instructors** (Dozenten) und **Departments** (Fachbereiche) hinzu.

Die Änderungen werden hervorgehoben.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET Core durch Text zu dieser App:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Führen Sie die App aus, um sicherzustellen, dass die Startseite angezeigt wird.

## <a name="the-data-model"></a>Das Datenmodell

In den folgenden Abschnitten wird ein Datenmodell erstellt:

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

Ein Student kann für beliebig viele Kurse angemeldet sein, und für jeden Kurs kann eine beliebige Anzahl von Studenten angemeldet sein.

## <a name="the-student-entity"></a>Die Entität „Student“

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

* Erstellen Sie im Projektordner den Ordner *Models*.
* Erstellen Sie *Models/Student.cs* mit folgendem Code:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die diesem Kurs entspricht. Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel. Der alternative, automatisch erkannte Name für den Primärschlüssel der Klasse `Student` lautet also `StudentID`. Weitere Informationen finden Sie unter [EF Core – Schlüssel](/ef/core/modeling/keys?tabs=data-annotations).

Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships). Navigationseigenschaften enthalten andere Entitäten, die dieser Entität zugehörig sind. In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student`-Entität alle `Enrollment`-Entitäten, die mit diesem Studenten in Zusammenhang stehen. Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei Enrollment-Entitäten. 

In der-Datenbank ist eine Enrollment-Zeile mit einer Student-Zeile verknüpft, wenn die StudentID-Spalte den ID-Wert des Studenten enthält. Angenommen, eine Student-Zeile weist die ID=1 auf. Zugehörige Enrollment-Zeilen weisen die StudentID = 1 auf. StudentID ist ein *Fremdschlüssel* in der Enrollment-Tabelle. 

Die `Enrollments`-Eigenschaft wird als `ICollection<Enrollment>` definiert, da es möglicherweise mehrere zugehörige Enrollment-Entitäten gibt. Sie können andere Sammlungstypen verwenden, z.B. `List<Enrollment>` oder `HashSet<Enrollment>`. Wenn `ICollection<Enrollment>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<Enrollment>`-Auflistung.

## <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

Erstellen Sie *Models/Enrollment.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Die `EnrollmentID`-Eigenschaft ist der Primärschlüssel. Diese Entität verwendet das `classnameID`-Muster anstelle nur der `ID`. Wählen Sie für ein Produktionsdatenmodell ein Muster aus, und verwenden Sie es einheitlich. In diesem Tutorial werden beide Muster verwendet, um zu veranschaulichen, dass beide funktionieren. Wenn Sie `ID` ohne `classname` verwenden, ist es einfacher, einige Arten von Datenmodelländerungen zu implementieren.

Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`. Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft [NULL-Werte zulässt](/dotnet/csharp/programming-guide/nullable-types/). Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null): Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben. Beispielsweise `StudentID` der Fremdschlüssel für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist. Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben. Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.

## <a name="the-course-entity"></a>Die Entität „Course“

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

Erstellen Sie *Models/Course.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.

Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.

## <a name="scaffold-student-pages"></a>Gerüstbau der Student-Seiten

In diesem Abschnitt verwenden Sie das ASP.Net Core-Gerüstbautool, um Folgendes zu generieren:

* Eine EF Core *context*-Klasse. „context“ ist die Hauptklasse, die die Entity Framework-Funktionen für ein angegebenes Datenmodell koordiniert. Diese Klasse wird von der `Microsoft.EntityFrameworkCore.DbContext`-Klasse abgeleitet.
* Razor Pages-Instanzen, die CRUD-Vorgänge (Create, Read, Update, Delete) für die `Student`-Entität verarbeiten.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Erstellen Sie einen Ordner *Students* im Ordner *Pages*.
* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.
* Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.
* Gehen Sie im Dialogfeld **Razor Pages mit Entity Framework (CRUD) hinzufügen** folgendermaßen vor:
  * Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.
  * Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.
  * Ändern Sie den Datenkontextnamen aus *ContosoUniversity.Models.ContosoUniversityContext* in *ContosoUniversity.Data.SchoolContext*.
  * Wählen Sie **Hinzufügen** aus.

Die folgenden Pakete werden automatisch installiert:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Führen Sie die folgenden Befehle der .NET Core-CLI aus, um erforderliche NuGet-Pakete zu installieren:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Das Paket Microsoft.VisualStudio.Web.CodeGeneration.Design ist für den Gerüstbau erforderlich. Obwohl die App SQL Server nicht verwendet, benötigt das Gerüstbautool das SQL Server-Paket.

* Erstellen Sie einen Ordner *Pages/Students*.

* Führen Sie den folgenden Befehl zum Installieren des [Gerüstbautools aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator) aus.

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Führen Sie folgende Befehle aus, um das Gerüst für Student-Seiten zu erstellen.

  **Unter Windows**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **Unter macOS oder Linux**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Wenn Sie ein Problem mit dem vorherigen Schritt haben, erstellen Sie das Projekt, und wiederholen Sie den Gerüstbauschritt.

Der Gerüstbauprozess:

* Erstellt Razor Pages im Ordner *Pages/Students*:
  * *Create.cshtml* und *Create.cshtml.cs*
  * *Create.cshtml* und *Delete.cshtml.cs*
  * *Details.cshtml* und *Details.cshtml.cs*
  * *Edit.cshtml* und *Edit.cshtml.cs*
  * *Index.cshtml* und *Index.cshtml.cs*
* Erstellt *Data/SchoolContext.cs*.
* Fügt der Abhängigkeitsinjektion in *Startup.cs* den Kontext hinzu.
* Fügt der Datei *appsettings.json* eine Datenbankverbindungszeichenfolge hinzu.

## <a name="database-connection-string"></a>Datenbankverbindungszeichenfolge

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In der Datei *appsettings.json* wird die Verbindungszeichenfolge [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) angegeben.

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist. Standardmäßig erstellt LocalDB *MDF*-Dateien im Verzeichnis `C:/Users/<user>`.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Ändern Sie die Verbindungszeichenfolge, um auf eine SQLite-Datenbankdatei namens *CU.db* zu verweisen:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Aktualisieren der Datenbankkontextklasse

Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert. Der Kontext wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet. Der Kontext gibt an, welche Entitäten im Datenmodell enthalten sind. In diesem Projekt heißt die Klasse `SchoolContext`.

Aktualisieren Sie *Data/SchoolContext.cs* mit folgendem Code:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge. Das heißt für Entity Framework Core:

* Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.
* Entitäten entsprechen Zeilen in Tabellen.

Da eine Entitätenmenge mehrere Entitäten enthält, sollten die DBSet-Eigenschaften Namen im Plural tragen. Da das Gerüstbautool ein `Student`-DBSet erstellt hat, ändert dieser Schritt den Namen in den Plural `Students`. 

Damit der Razor Pages-Code mit dem neuen DBSet-Namen übereinstimmt, ändern Sie `_context.Student` im gesamten Projekt global in `_context.Students`.  Es gibt 8 Vorkommen.

Erstellen Sie das Projekt, um sich zu vergewissern, dass keine Compilerfehler vorhanden sind.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.

Das Gerüstbautool hat die context-Klasse automatisch beim Dependency Injection-Container registriert.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* In `ConfigureServices` wurden die hervorgehobenen Zeilen vom Gerüstbau hinzugefügt:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Stellen Sie in `ConfigureServices` sicher, dass der durch den Gerüstbau hinzugefügte Code `UseSqlite` aufruft.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.

## <a name="create-the-database"></a>Erstellen der Datenbank

Aktualisieren Sie *Program.cs*, um die Datenbank zu erstellen, wenn diese nicht vorhanden ist:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Die [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)-Methode führt keine Aktion aus, wenn eine Datenbank für den Kontext vorhanden ist. Wenn keine Datenbank vorhanden ist, werden die Datenbank und das Schema erstellt. `EnsureCreated` aktiviert den folgenden Workflow für die Verarbeitung von Datenmodelländerungen:

* Löschen der Datenbank. Alle vorhandenen Daten gehen verloren.
* Ändern des Datenmodells. Beispielsweise Hinzufügen eines `EmailAddress`-Felds.
* Führen Sie die App aus.
* `EnsureCreated` erstellt eine Datenbank mit dem neuen Schema.

Dieser Workflow funktioniert früh in der Entwicklungsphase gut, wenn sich das Schema rasch weiterentwickelt, solange Sie keine Daten beibehalten müssen. Anders verhält es sich, wenn es darum geht, Daten, die in die Datenbank eingegeben wurden, beizubehalten. Wenn dies der Fall ist, verwenden Sie Migrationen.

Später in der Tutorialserie löschen Sie die Datenbank, die von `EnsureCreated` erstellt wurde, und verwenden stattdessen Migrationen. Eine Datenbank, die von `EnsureCreated` erstellt wird, kann nicht mithilfe von Migrationen aktualisiert werden.

### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus.
* Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.
* Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).

## <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Die `EnsureCreated`-Methode erstellt eine leere Datenbank. In diesem Abschnitt wird Code hinzugefügt, der die Datenbank mit Testdaten auffüllt.

Erstellen Sie *Data/DbInitializer.cs* mit dem folgenden Code:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Der Code überprüft, ob Studenten in der Datenbank vorhanden sind. Wenn keine Studenten vorhanden sind, werden der Datenbank Testdaten hinzugefügt. Testdaten werden in Arrays anstelle von `List<T>`-Sammlungen erstellt, um die Leistung zu optimieren.

* Ersetzen Sie in *Program.cs* den `EnsureCreated`-Aufruf durch einen `DbInitializer.Initialize`-Aufruf:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.

---

* Starten Sie die App neu.

* Wählen Sie die Seite „Students“ aus, um die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde.

## <a name="view-the-database"></a>Zeigen Sie die Datenbank an

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).
* Wählen Sie in SSOX **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** aus. Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert.
* Erweitern Sie den Knoten **Tabellen**.
* Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.
* Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Code anzeigen**, um die Zuordnung des `Student`-Modells zum `Student`-Tabellenschema anzuzeigen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Verwenden Sie das SQLite-Tool, um das Datenbankschema und die Daten anzuzeigen, mit denen das Seeding ausgeführt wurde. Die Datenbankdatei trägt den Namen *CU.db* und befindet sich im Projektordner.

---

## <a name="asynchronous-code"></a>Asynchroner Code

Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.

Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht. Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.

Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:
  * Er soll Rückrufe für Teile des Methodentexts generieren.
  * Er soll das [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)-Objekt erstellen, das zurückgegeben wird.
* Der Rückgabetyp `Task<T>` stellt die derzeit ausgeführten Aufgaben dar.
* Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt. Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird. Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.
* Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.

Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:

* Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen. Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`. Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.
* Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.
* Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="step-by-step"]
> [Nächstes Tutorial](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Die Beispiel-Web-App der Contoso University veranschaulicht, wie Sie mit Entity Framework Core (EF Core) und ASP.NET Core eine Razor Pages-App erstellen können.

Bei der Beispiel-App handelt es sich um eine Website für die fiktive Contoso University. Sie enthält Funktionen wie die Zulassung von Studenten, die Erstellung von Kursen und Aufgaben von Dozenten. Dies ist die erste Seite eines mehrseitigen Tutorials, in dem die Erstellung der Beispiel-App der Contoso University erläutert wird.

[Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Anweisungen zum Download.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

Kenntnisse zu [Razor Pages](xref:razor-pages/index). Einsteiger sollten den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start) lesen, bevor sie mit dieser Tutorialreihe beginnen.

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie auf ein Problem stoßen, das Sie nicht lösen können, sollten Sie versuchen, Ihren Code mit dem [abgeschlossenen Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) zu vergleichen. Sie können auch Hilfe erhalten, indem Sie eine Frage zu [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) oder [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) auf [stackoverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) stellen.

## <a name="the-contoso-university-web-app"></a>Die Web-App der Contoso University

Bei der App, die mithilfe dieser Tutorials erstellt werden soll, handelt es sich um eine einfache Website einer Universität.

Benutzer können Informationen zu den Studenten, Kursen und Dozenten abrufen. Im Folgenden sind einige Anzeigen dargestellt, die mithilfe dieses Tutorials erstellt werden.

![Indexseite „Studenten“](intro/_static/students-index.png)

![Bearbeitungsseite für Studenten](intro/_static/student-edit.png)

Der Benutzeroberflächenstil dieser Website ähnelt den durch die integrierten Vorlagen generierten Seiten. In diesem Tutorial geht es um EF Core mit Razor Pages, nicht um die Benutzeroberfläche.

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a>Erstellen der Razor Pages-Web-App „ContosoUniversity“

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.
* Erstellen Sie eine neue ASP.NET Core-Webanwendung. Geben Sie dem Projekt den Namen **ContosoUniversity**. Es ist wichtig, dass Sie dem Projekt *ContosoUniversity* zu nennen, sodass die Namespaces übereinstimmen, wenn der Code kopiert und eingefügt wird.
* Wählen Sie in der Dropdownliste **ASP.NET Core 2.1** und anschließend **Webanwendung** aus.

Bilder zu den vorherigen Schritten finden Sie unter [Erstellen einer Razor Pages-Web-App](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Führen Sie die App aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Einrichten des Websitestils

Sie können das Websitemenü, das Layout und die Startseite über einige Änderungen einrichten. Aktualisieren Sie *Pages/Shared/_Layout.cshtml* mit folgenden Änderungen:

* Ändern Sie jedes „ContosoUniversity“ in „Contoso University“. Diese Begriffskombination kommt dreimal vor.

* Fügen Sie Menüeinträge für **Studenten**, **Kurse**, **Dozenten** und **Abteilungen** hinzu, und löschen Sie den Menüeintrag **Kontakt**.

Die Änderungen werden hervorgehoben. (Es wird *nicht* das gesamte Markup angezeigt.)

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

Ersetzen Sie in *Pages/Index.cshtml* die Inhalte der Datei durch den folgenden Code. Dadurch ersetzen Sie den Text zu ASP.NET und MVC durch Text zu dieser App:

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Erstellen des Datenmodells

Erstellen Sie Entitätsklassen für die Contoso University-App. Beginnen Sie mit dem folgenden drei Entitäten:

![Datenmodelldiagramm zur Kursanmeldung für Studenten](intro/_static/data-model-diagram.png)

Es besteht eine 1:n-Beziehung zwischen der `Student`- und der `Enrollment`-Entität. Es besteht eine 1:n-Beziehung zwischen der `Course`- und der `Enrollment`-Entität. Jeder Student kann sich für eine beliebige Anzahl von Kursen anmelden. Für jeden Kurs kann sich eine beliebige Anzahl von Studenten anmelden.

In den folgenden Abschnitten wird für jede dieser Entitäten eine Klasse erstellt.

### <a name="the-student-entity"></a>Die Entität „Student“

![Entitätsdiagramm „Student“](intro/_static/student-entity.png)

Erstellen Sie einen Ordner *Models* (Modelle). Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine Klassendatei mit dem Namen *Student.cs*:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Die `ID`-Eigenschaft fungiert als Primärschlüsselspalte der Datenbanktabelle, die dieser Klasse entspricht. Standardmäßig interpretiert Entity Framework Core eine Eigenschaft mit dem Namen `ID` oder `classnameID` als Primärschlüssel. In `classnameID` ist `classname` der Name der Klasse. Der Primärschlüssel, der alternativ automatisch erkannt wurde, ist im vorherigen Beispiel `StudentID`.

Die `Enrollments`-Eigenschaft ist eine [Navigationseigenschaft](/ef/core/modeling/relationships). Navigationseigenschaften werden mit anderen Entitäten verknüpft, die dieser Entität zugehörig sind. In diesem Fall enthält die `Enrollments`-Eigenschaft einer `Student entity` all diese `Enrollment`-Entitäten, die mit diesem `Student` in Zusammenhang stehen. Wenn es z.B. für eine „Student“-Zeile in der Datenbank zwei zugehörige „Enrollment“-Zeilen gibt, enthält die Navigationseigenschaft `Enrollments` zwei `Enrollment`-Entitäten. Bei einer zugehörigen `Enrollment`-Zeile handelt es sich um eine Zeile, die den Wert des Primärschlüssels des Studenten in der `StudentID`-Spalte enthält. Nehmen Sie z.B. an, dass es für den Studenten mit ID=1 zwei Zeilen in der `Enrollment`-Tabelle gibt. Die `Enrollment`-Tabelle verfügt über zwei Zeilen mit `StudentID`=1. Bei `StudentID` handelt es sich um einen Fremdschlüssel in der `Enrollment`-Tabelle, der den Studenten in der `Student`-Tabelle angibt.

Wenn in einer Navigationseigenschaft mehrere Entitäten gespeichert werden können, muss es sich um einen Listentyp wie `ICollection<T>` handeln. `ICollection<T>` oder ein Typ wie `List<T>` oder `HashSet<T>` können angegeben werden. Wenn `ICollection<T>` verwendet wird, erstellt Entity Framework Core standardmäßig eine `HashSet<T>`-Auflistung. Navigationseigenschaften, die mehrere Entitäten enthalten, entstehen auf der Grundlage von m:n- oder 1:n-Beziehungen.

### <a name="the-enrollment-entity"></a>Die Entität „Enrollment“

![Entitätsdiagramm „Enrollment“](intro/_static/enrollment-entity.png)

Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Enrollment.cs*-Datei:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Bei der `EnrollmentID`-Eigenschaft handelt es sich um den Primärschlüssel. Diese Entität verwendet genau wie die `Student`-Entität das `classnameID`-Muster anstelle von `ID`. In der Regel wählen Entwickler ein Muster aus und verwenden dieses im gesamten Datenmodell. In einem der nächsten Tutorials wird erläutert, wie Sie eine ID ohne Klassennamen verwenden, um die Vererbung einfacher in das Datenmodell zu implementieren.

Bei der `Grade`-Eigenschaft handelt es sich um eine `enum`. Das Fragezeichen nach der `Grade`-Typdeklaration gibt an, dass die `Grade`-Eigenschaft NULL-Werte zulässt. Eine Grade-Eigenschaft mit dem Wert NULL unterscheidet sich von einer Grade-Eigenschaft mit dem Wert 0 (null). Der Wert NULL bedeutet, dass keine Grade-Eigenschaft bekannt ist oder noch keine zugewiesen wurde.

Bei der `StudentID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Student` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Student`-Entität zugewiesen. Das bedeutet, dass die Eigenschaft genau eine `Student`-Entität enthält. Die `Student`-Entität unterscheidet sich von der `Student.Enrollments`-Navigationseigenschaft, die mehrere `Enrollment`-Entitäten enthält.

Bei der `CourseID`-Eigenschaft handelt es sich um einen Fremdschlüssel, und `Course` ist die entsprechende Navigationseigenschaft. Die `Enrollment`-Entität wird einer `Course`-Entität zugeordnet.

Entity Framework Core interpretiert Eigenschaften als Fremdschlüssel, wenn diese den Namen `<navigation property name><primary key property name>` haben. Beispielsweise `StudentID` für die `Student`-Navigationseigenschaft, da `ID` der Primärschlüssel der `Student`-Entität ist. Fremdschlüsseleigenschaften können ebenfalls den Namen `<primary key property name>` haben. Beispielsweise `CourseID`, da `CourseID` der Primärschlüssel der `Course`-Entität ist.

### <a name="the-course-entity"></a>Die Entität „Course“

![Entitätsdiagramm „Course“](intro/_static/course-entity.png)

Erstellen Sie mit dem folgenden Code im Ordner *Models* (Modelle) eine *Course.cs*-Datei:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Die `Enrollments`-Eigenschaft ist eine Navigationseigenschaft. `Course`-Entitäten können sich auf jede beliebige Anzahl von `Enrollment`-Entitäten beziehen.

Das `DatabaseGenerated`-Attribut lässt es zu, dass die App den Primärschlüssel angibt, sodass die Datenbank diesen nicht generieren muss.

## <a name="scaffold-the-student-model"></a>Erstellen des Gerüsts für das Studentenmodell

In diesem Abschnitt wird das Gerüst für das Studentenmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Studentenmodell erstellt.

* Erstellen Sie das Projekt.
* Erstellen Sie den Ordner *Pages/Students*.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Pages/Students*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.
* Wählen Sie im Dialogfeld **Gerüst hinzufügen** den Eintrag **Razor Pages mit Entity Framework (CRUD)** > **Hinzufügen** aus.

Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):

* Wählen Sie im Dropdownmenü **Modellklasse** **Student (ContosoUniversity.Models)** aus.
* Wählen Sie in der Zeile **Datenkontextklasse** das Pluszeichen ( **+** ) aus, und ändern Sie den generierten Namen in **ContosoUniversity.Models.SchoolContext**.
* Wählen Sie im Dropdownmenü **Datenkontextklasse** **ContosoUniversity.Models.SchoolContext** aus.
* Wählen Sie **Hinzufügen** aus.

![CRUD-Dialogfeld](intro/_static/s1.png)

Wenn Sie Probleme mit dem vorherigen Schritt haben, finden Sie weitere Informationen unter [Erstellen des Gerüsts für das Filmmodell](xref:tutorials/razor-pages/model#scaffold-the-movie-model).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgende Befehle aus, um das Gerüst für das Studentenmodell zu erstellen.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Der Gerüstprozess hat folgende Dateien erstellt und geändert:

### <a name="files-created"></a>Erstellte Dateien

* *Pages/Students/Create.cshtml.cs* ( bzw. /Delete, /Details, /Edit, /Index).
* *Data/SchoolContext.cs*

### <a name="file-updates"></a>Dateiupdates

* *Startup.cs*: Die Änderungen an dieser Datei werden im nächsten Abschnitt ausführlich erläutert.
* *appsettings.json*: Die Verbindungszeichenfolge, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird, wurde hinzugefügt.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste (z.B. der Datenbankkontext Entity Framework Core) werden über Dependency Injection beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.

Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem Dependency Injection-Container registriert.

Untersuchen Sie die Methode `ConfigureServices` in *Startup.cs*. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json*-Datei.

## <a name="update-main"></a>Aktualisieren der Main-Methode

Ändern Sie in der *Program.cs*-Datei die `Main`-Methode, um die folgenden Vorgänge auszuführen:

* Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.
* Rufen Sie [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) auf.
* Löschen Sie den Kontext, wenn die `EnsureCreated`-Methode abgeschlossen ist.

Der folgende Code zeigt die aktualisierte *Program.cs*-Datei.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` stellt sicher, dass die Datenbank für den Kontext vorhanden ist. Wenn sie vorhanden ist, werden keine Aktionen durchgeführt. Wenn sie nicht vorhanden ist, werden die Datenbank und alle Schemas erstellt. `EnsureCreated` verwendet keine Migrationen, um die Datenbank zu erstellen. Eine Datenbank, die mit `EnsureCreated` erstellt wird, kann später nicht mithilfe von Migrationen aktualisiert werden.

`EnsureCreated` wird beim Starten der App aufgerufen, wodurch der folgende Workflow ermöglicht wird:

* Löschen Sie die Datenbank.
* Ändern Sie das Datenbankschema, also fügen Sie z.B. ein `EmailAddress`-Feld hinzu.
* Führen Sie die App aus.
* Über `EnsureCreated` wird eine Datenbank mit der `EmailAddress`-Spalte erstellt.

`EnsureCreated` eignet sich am Anfang der Entwicklung, wenn das Schema schnell weiterentwickelt wird. Im Verlauf des Tutorials wird die Datenbank gelöscht, und Migrationen werden verwendet.

### <a name="test-the-app"></a>Testen der App

Führen Sie die App aus, und akzeptieren Sie die cookierichtlinie. Diese App bewahrt keine personenbezogenen Informationen auf. Weitere Informationen zur cookierichtlinie finden Sie auf der Seite [EU General Data Protection Regulation (GDPR) support (Unterstützung für die Datenschutz-Grundverordnung (DSGVO) der EU)](xref:security/gdpr).

* Klicken Sie auf den Link **Students** (Studenten) und anschließend auf **Neu erstellen**.
* Testen Sie die Links „Edit“ (Bearbeiten), „Details“ und „Delete“ (Löschen).

## <a name="examine-the-schoolcontext-db-context"></a>Untersuchen des Datenbankkontexts „SchoolContext“

Bei der Datenbankkontextklasse handelt es sich um die Hauptklasse, die die Entity Framework Core-Funktionen für ein angegebenes Datenmodell koordiniert. Der Datenkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind. In diesem Projekt heißt die Klasse `SchoolContext`.

Aktualisieren Sie *SchoolContext.cs* mit folgendem Code:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Der hervorgehobene Code erstellt eine [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für jede Entitätenmenge. Das heißt für Entity Framework Core:

* Entitätenmengen entsprechen in der Regel einer Datenbanktabelle.
* Entitäten entsprechen Zeilen in Tabellen.

`DbSet<Enrollment>` und `DbSet<Course>` können ausgelassen werden. Diese sind implizit in Entity Framework Core enthalten, da die `Student`-Entität auf die `Enrollment`-Entität verweist, und die `Enrollment`-Entität auf die `Course`-Entität verweist. Behalten Sie für dieses Tutorial `DbSet<Enrollment>` und `DbSet<Course>` im `SchoolContext`-Kontext.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Die Verbindungszeichenfolge gibt [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) an. LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die zwar für die Anwendungsentwicklung, aber nicht für den Produktionseinsatz bestimmt ist. LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt. Standardmäßig erstellt LocalDB *.mdf*-Datenbankdateien im `C:/Users/<user>`-Verzeichnis.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Hinzufügen von Code zum Initialisieren der Datenbank mithilfe von Testdaten

Entity Framework Core erstellt eine leere Datenbank. In diesem Abschnitt wird eine `Initialize`-Methode geschrieben, um diese mit Testdaten aufzufüllen.

Erstellen Sie im Ordner *Data* eine neuen Klassendatei mit dem Namen *DbInitializer.cs*, und fügen Sie den folgenden Code hinzu:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Hinweis: Der vorherige Code verwendet `Models` für den Namespace (`namespace ContosoUniversity.Models`) statt `Data`. `Models` entspricht dem vom Gerüst generierten Code. Weitere Informationen finden Sie in diesem [GitHub-Gerüstbau-Problem](https://github.com/aspnet/Scaffolding/issues/822).

Der Code überprüft, ob Studenten in der Datenbank enthalten sind. Wenn keine Studenten in der Datenbank enthalten sind, wird diese mit Testdaten initialisiert. Testdaten werden in Arrays anstelle von `List<T>`-Auflistungen geladen, um die Leistung zu optimieren.

Die `EnsureCreated`-Methode erstellt automatisch die Datenbank für den Datenbankkontext. Wenn die Datenbank vorhanden ist, wird `EnsureCreated` zurückgegeben, ohne Änderungen an der Datenbank vorzunehmen.

Ändern Sie in *Program.cs* die `Main`-Methode, um `Initialize` aufzurufen:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Beenden Sie die App, falls sie gerade ausgeführt wird, und führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (Package Manager Console, PMC) aus:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Beenden Sie die App, wenn Sie ausgeführt wird, und löschen Sie die Datei *CU.db*.

---

## <a name="view-the-db"></a>Abrufen der Datenbank

Der Datenbankname wird anhand des Kontextnamens, den Sie zuvor angegeben haben, plus Bindestrich und GUID generiert. Daher lautet der Name der Datenbank „SchoolContext-{GUID}“. Die GUID ist für jeden Benutzer unterschiedlich.
Öffnen Sie über das Menü **Ansicht** im Visual Studio **SQL Server-Objekt-Explorer** (SSOX).
Klicken Sie im SSOX auf **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}** .

Erweitern Sie den Knoten **Tabellen**.

Klicken Sie mit der rechten Maustaste auf die Tabelle **Student**, und klicken Sie auf **Daten anzeigen**, um die erstellten Spalten und die in die Tabelle eingefügten Zeilen aufzurufen.

## <a name="asynchronous-code"></a>Asynchroner Code

Die asynchrone Programmierung ist der Standardmodus für ASP.NET Core und EF Core.

Der Webserver verfügt nur über eine begrenzte Anzahl von Threads. Daher werden bei hoher Auslastung möglicherweise alle verfügbaren Threads gleichzeitig verwendet. Wenn dies der Fall ist, kann der Server keine neuen Anforderungen verarbeiten, bis die Threads wieder freigegeben werden. Wenn synchroner Code verwendet wird, kann es sein, dass zwar viele Threads belegt sind, diese aber keine Vorgänge ausführen, da sie auf den Abschluss der E/A-Vorgänge warten. Wenn asynchroner Code verwendet wird, werden Threads für den Server freigegeben, wenn diese nur auf den Abschluss der E/A-Vorgänge warten, damit andere Anforderungen verarbeitet werden können. Das bedeutet, dass es durch asynchronen Code ermöglicht wird, Serverressourcen effizienter zu nutzen, und der Server kann ohne Verzögerungen eine größere Menge von Datenverkehr verarbeiten.

Zur Laufzeit bedeutet die Verwendung von asynchronem Code allerdings, dass ein wenig mehr Aufwand entsteht. Für Situationen mit wenig Datenverkehr haben diese Leistungseinbußen keine negativen Folgen. Wenn es jedoch eine große Menge an Datenverkehr gibt, ist eine potentielle Verbesserung der Leistung von Bedeutung.

Im folgenden Code führen das [async](/dotnet/csharp/language-reference/keywords/async)-Schlüsselwort, der `Task<T>`-Rückgabewert, das `await`-Schlüsselwort und die `ToListAsync`-Methode dazu, dass der Code asynchron ausgeführt wird.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Das `async`-Schlüsselwort gibt dem Compiler folgende Anweisungen:
  * Er soll Rückrufe für Teile des Methodentexts generieren.
  * Er soll automatisch das [Task](/dotnet/api/system.threading.tasks.task)-Objekt erstellen, das zurückgegeben wird. Weitere Informationen finden Sie unter [Aufgabenrückgabetyp](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Der implizite Rückgabetyp `Task` steht für die derzeit ausgeführte Arbeit.
* Das `await`-Schlüsselwort hat zur Folge, dass der Compiler die Methode in zwei Teile unterteilt. Der erste Teil endet mit dem Vorgang, der auf asynchrone Weise gestartet wird. Der zweite Teil wird in eine Rückrufmethode übertragen, die aufgerufen wird, wenn der Vorgang abgeschlossen wird.
* Bei `ToListAsync` handelt es sich um die asynchrone Version der `ToList`-Erweiterungsmethode.

Behalten Sie Folgendes im Hinterkopf, wenn Sie asynchronen Code schreiben, der Entity Framework Core verwendet:

* Es werden nur Anweisungen auf asynchrone Weise ausgeführt, die Abfragen oder Befehle auslösen, die an die Datenbank gesendet werden sollen. Dazu gehören `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync` und `SaveChangesAsync`. Anweisungen wie `var students = context.Students.Where(s => s.LastName == "Davolio")`, die nur eine `IQueryable`-Instanz ändern, sind davon ausgeschlossen.
* Entity Framework Core-Kontexte sind nicht threadsicher. Versuchen Sie daher nicht, mehrere Vorgänge gleichzeitig auszuführen.
* Wenn Sie von den Leistungsvorteilen durch asynchronen Code profitieren möchten, überprüfen Sie, ob Bibliothekspakete (z.B. zum Paging) asynchronen Code verwenden, wenn sie Entity Framework Core-Methoden aufrufen, die Abfragen an die Datenbank senden.

Weitere Informationen zur asynchronen Programmierung in .NET finden Sie unter [Async (Übersicht)](/dotnet/standard/async) und [Asynchrone Programmierung mit Async und Await (C#)](/dotnet/csharp/programming-guide/concepts/async/).

Im nächsten Tutorial erfahren Sie mehr über die CRUD-Vorgänge (Create, Read, Update, Delete = Erstellen, Lesen, Aktualisieren, Löschen).



## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dieses Tutorial auf YouTube](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Nächste](xref:data/ef-rp/crud)

::: moniker-end
