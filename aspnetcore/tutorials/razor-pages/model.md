---
title: 'Teil 2: Hinzufügen eines Modells'
author: rick-anderson
description: Dies ist Teil 2 der Tutorialreihe zu Razor Pages. In diesem Abschnitt werden Modellklassen hinzugefügt.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485991"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Teil 2: Hinzufügen eines Modells zu einer Razor Pages-App in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer Datenbank hinzugefügt. Die Modellklassen der App verwenden [Entity Framework Core](/ef/core) (EF Core), um mit der Datenbank zu arbeiten. EF Core ist ein objektrelationaler Mapper (O/RM), der den Datenzugriff vereinfacht. Sie schreiben zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.

Die Modellklassen werden als POCO-Klassen (von „**P** lain-**O** ld **C** LR **O** bjects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen. Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Hinzufügen eines Datenmodells

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt *RazorPagesMovie*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.
1. Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*. Wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse *Movie*.
1. Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Der Benutzer muss keine Zeitinformationen in das Datumsfeld eingeben.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Fügen Sie einen Ordner namens *Modelle* hinzu.
1. Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Hinzufügen von NuGet-Paketen und EF-Tools

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Hinzufügen einer Datenbankkontext-Klasse

1. Erstellen Sie im Projekt *RazorPagesMovie* einen Ordner mit dem Namen *Data*.
1. Fügen Sie im Ordner *Data* eine Datei mit dem Namen *RazorPagesMovieContext.cs* mit folgendem Code hinzu:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle. Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

1. Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Registrieren Sie den Datenbankkontext beim Container [Dependency Injection](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt *RazorPagesMovie*, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.
1. Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Modelle*, und wählen Sie dann **Hinzufügen** > **Neue Datei...** aus.
1. Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:
   1. Klicken Sie im linken Bereich auf **Allgemein**.
   1. Klicken Sie im mittleren Bereich auf **Leere Klasse**.
   1. Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.

1. Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.

## <a name="scaffold-the-movie-model"></a>Erstellen des Gerüsts für das Filmmodell

In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Erstellen Sie den Ordner *Pages/Movies*:
   1. Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.
   1. Nennen Sie den Ordner *Movies*.

1. Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.

   ![Abbildung der vorherigen Anweisungen.](model/_static/5/sca.png)

1. Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.

   ![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

1. Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):
   1. Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.
   1. Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.
      1. Im Dialogfeld **Datenkontext hinzufügen** wird der Klassenname *RazorPagesMovie.Data.RazorPagesMovieContext* generiert.
   1. Wählen Sie **Hinzufügen** aus.

   ![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Öffnen Sie eine Befehlsshell im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.

* **Für Windows**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt.

| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
| `-m`  | Der Name des Modells. |
| `-dc`  | Die `DbContext`-Klasse, die verwendet werden soll. |
| `-udl` | Verwendung des Standardlayouts. |
| `-outDir` | Der relative Ausgabeordnerpfad zur Erstellung der Ansichten. |
| `--referenceScriptLibraries` | Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu. |

Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Einfügen von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup`. `IWebHostEnvironment` wird eingefügt, sodass die App SQLite in der Entwicklungs- und SQL Server der Produktionsumgebung verwenden kann.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Erstellen Sie den Ordner *Pages/Movies*:
   1. Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages*. Wählen Sie **Hinzufügen** > **Neuer Ordner** aus.
   1. Nennen Sie den Ordner *Movies*.

1. Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages/Movies*, und wählen Sie **Hinzufügen** > **Neuer Gerüstbau...** aus.

   ![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

1. Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Weiter**.

   ![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

1. Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):
   1. Nennen Sie in der Zeile **Zu verwendende DbContext-Klasse:** die Klasse *RazorPagesMovie.Data.RazorPagesMovieContext*.
   1. Wählen Sie **Fertig stellen** aus.

   ![Abbildung der vorherigen Anweisungen.](model/_static/5/arpMac.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Einfügen von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup`. `IWebHostEnvironment` wird eingefügt, sodass die App SQLite in der Entwicklungs- und SQL Server der Produktionsumgebung verwenden kann.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Erstellte Dateien

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Der Gerüstprozess erstellt und ändert folgende Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualisiert

* *Startup.cs*

Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Der Gerüstbauprozess erstellt die folgenden Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“.

Die erstellten Daten werden im nächsten Abschnitt erläutert.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Der Gerüstprozess erstellt und ändert folgende Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualisiert

* *Startup.cs*

Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>Erstellen des anfängliche, Datenbankschema mithilfe des EF-Features „Migrationen“

Das Feature „Migrationen“ in Entity Framework Core ermöglicht Folgendes:

* Erstellen des anfänglichen Datenbankschemas
* Schrittweises Aktualisieren des Datenbankschemas, um es mit dem Datenmodell der Anwendung synchron zu halten.  In der Datenbank vorhandene Daten werden beibehalten.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In diesem Abschnitt wird das Fenster **Paket-Manager-Konsole** (Package Manager Console, PMC) für Folgendes verwendet:

* Fügen Sie eine anfängliche Migration hinzu.
* Aktualisieren Sie die Datenbank mit der anfänglichen Migration.

1. Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

   ![PMC-Menü](model/_static/5/pmc.png)

1. Geben Sie in der PMC die folgenden Befehle ein:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Führen Sie die folgenden .NET-CLI-Befehle aus:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")

Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.

Mit dem Befehl `migrations` wird Code generiert, um das anfängliche Datenbankschema zu erstellen. Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist. Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.

Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden. In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.

Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.

Untersuchen Sie die Methode `Startup.ConfigureServices`. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update und Delete) für das `Movie`-Modell. Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Untersuchen Sie die Methode `Up`.

---

<a name="test"></a>

## <a name="test-the-app"></a>Testen der App

1. Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).

   Möglicherweise wird eine Fehlermeldung wie diese angezeigt:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   Sie haben den [Migrationsschritt](#pmc) verpasst.

1. Testen Sie den Link **Create** (Erstellen).

   ![Seite „Create“](model/_static/conan5.png)

   > [!NOTE]
   > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

1. Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

In diesem Abschnitt werden Klassen für die Verwaltung von Filmen hinzugefügt. Die Modellklassen der App verwenden [Entity Framework Core](/ef/core) (EF Core), um mit der Datenbank zu arbeiten. EF Core ist ein objektrelationaler Mapper (O/RM), der den Datenzugriff vereinfacht.

Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen. Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Hinzufügen eines Datenmodells

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*. Wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse **Movie**.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Fügen Sie einen Ordner namens *Modelle* hinzu.
* Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Hinzufügen von NuGet-Paketen und EF-Tools

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Hinzufügen einer Datenbankkontext-Klasse

* Erstellen Sie im Projekt *RazorPagesMovie* einen neuen einen Ordner mit dem Namen *Data*.
* Fügen Sie dem Ordner *Data* (Daten) die folgende `RazorPagesMovieContext`-Klasse hinzu:

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle. Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt **RazorPagesMovie**, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.
* Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei...** .
* Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:

  * Klicken Sie im linken Bereich auf **Allgemein**.
  * Klicken Sie im mittleren Bereich auf **Leere Klasse**.
  * Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

---

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.

## <a name="scaffold-the-movie-model"></a>Erstellen des Gerüsts für das Filmmodell

In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie den Ordner *Pages/Movies*:

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.
* Nennen Sie den Ordner *Movies*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):

* Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.
* Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und ändern Sie den generierten Namen von RazorPagesMovie.**Models**.RazorPagesMovieContext in RazorPagesMovie.**Data**.RazorPagesMovieContext. [Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich. Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.
* Wählen Sie **Hinzufügen** aus.

![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.

* **Für Windows**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt:

| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
| `-m`  | Der Name des Modells. |
| `-dc`  | Die `DbContext`-Klasse, die verwendet werden soll. |
| `-udl` | Verwendung des Standardlayouts. |
| `-outDir` | Der relative Ausgabeordnerpfad zur Erstellung der Ansichten. |
| `--referenceScriptLibraries` | Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu. |

Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup. `IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Erstellen Sie den Ordner *Pages/Movies*:

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.
* Nennen Sie den Ordner *Movies*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neuer Gerüstbau...** .

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Weiter**.

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):

* Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus, oder geben Sie diesen ein.
* Geben Sie in der Zeile **Datenkontextklasse** den Namen der neuen Klasse ein: RazorPagesMovie.**Data**.RazorPagesMovieContext. [Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich. Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.
* Wählen Sie **Hinzufügen** aus.

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

### <a name="add-ef-tools"></a>Hinzufügen von EF-Tools

Führen Sie den folgenden .NET Core-CLI-Befehl aus:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Der vorangehende Befehl fügt die Entity Framework Core-Tools für die .NET Core-CLI hinzu. Weitere Informationen finden Sie unter [Referenz zu Entity Framework Core-Tools: .NET Core-CLI](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup. `IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Erstellte Dateien

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Der Gerüstprozess erstellt und ändert folgende Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualisiert

* *Startup.cs*

Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Der Gerüstprozess erstellt und ändert folgende Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“
* *Data/RazorPagesMovieContext.cs*

### <a name="updated"></a>Aktualisiert

* *Startup.cs*

Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Der Gerüstbauprozess erstellt die folgenden Dateien:

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“

Die erstellten Daten werden im nächsten Abschnitt erläutert.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>Anfängliche Migration

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:

* Fügen Sie eine anfängliche Migration hinzu.
* Aktualisieren Sie die Datenbank mit der anfänglichen Migration.

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")

Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.

Mit dem Migrationsbefehl wird Code generiert, um das anfängliche Datenbankschema zu erstellen. Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist. Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.

Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden. In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.

Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.

Untersuchen Sie die Methode `Startup.ConfigureServices`. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update und Delete) für das `Movie`-Modell. Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Untersuchen Sie die Methode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).

Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Sie haben den [Migrationsschritt](#pmc) verpasst.

* Testen Sie den Link **Create** (Erstellen).

  ![Seite „Create“](model/_static/conan5.png)

  > [!NOTE]
  > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt. Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank. Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten. EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.

Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen. Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Hinzufügen eines Datenmodells

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**. Geben Sie dem Ordner den Namen *Modelle*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*. Wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die Klasse **Movie**.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Fügen Sie einen Ordner namens *Modelle* hinzu.
* Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>Hinzufügen von NuGet-Paketen und EF-Tools

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Hinzufügen einer Datenbankkontext-Klasse

Erstellen Sie im Projekt RazorPagesMovie einen neuen einen Ordner mit dem Namen *Data*. 
Fügen Sie dem Ordner *Data* (Daten) die folgende `RazorPagesMovieContext`-Klasse hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle. Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Hinzufügen der erforderlichen NuGet-Pakete

Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Erstellen Sie das Projekt zur Fehlerüberprüfung.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt *RazorPagesMovie*, und wählen Sie dann **Hinzufügen**  >  **Neuer Ordner** aus. Geben Sie dem Ordner den Namen *Modelle*.
* Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.
* Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:

  * Klicken Sie im linken Bereich auf **Allgemein**.
  * Klicken Sie im mittleren Bereich auf **Leere Klasse**.
  * Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.

Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

Die `Movie`-Klasse enthält:

* Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.
* `[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

  * Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
  * Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.

---

Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.

## <a name="scaffold-the-movie-model"></a>Erstellen des Gerüsts für das Filmmodell

In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Erstellen Sie den Ordner *Pages/Movies*:

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.
* Nennen Sie den Ordner *Movies*.

Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.
* Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Wählen Sie **Hinzufügen** aus.

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.

* **Für Windows**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt:

| Option               | BESCHREIBUNG|
| ----------------- | ------------ |
| `-m`  | Der Name des Modells. |
| `-dc`  | Die `DbContext`-Klasse, die verwendet werden soll. |
| `-udl` | Verwendung des Standardlayouts. |
| `-outDir` | Der relative Ausgabeordnerpfad zur Erstellung der Ansichten. |
| `--referenceScriptLibraries` | Fügt `_ValidationScriptsPartial` zu den Seiten „Create“ und „Edit“ hinzu. |

Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Erstellen Sie den Ordner *Pages/Movies*:

* Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages*. Wählen Sie **Hinzufügen** > **Neuer Ordner** aus.
* Nennen Sie den Ordner *Movies*.

Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages/Movies*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):

* Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film** aus, oder geben Sie ihn ein.
* Geben Sie **RazorPagesMovieContext** in der Zeile **Datenkontextklasse** ein, und wählen Sie den Eintrag aus. Dadurch wird eine neue Datenbankkontext-Klasse mit dem richtigen Namespace erstellt. In diesem Fall ist das **RazorPagesMovie.Models.RazorPagesMovieContext**.
* Wählen Sie **Hinzufügen** aus.

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.

---

Der Gerüstprozess erstellt und ändert folgende Dateien:

### <a name="files-created"></a>Erstellte Dateien

* *Pages/Movies*: „Create“, „Delete“, „Details“, „Edit“ und „Index“
* *Data/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Datei aktualisiert

* *Startup.cs*

Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.

<a name="pmc"></a>

## <a name="initial-migration"></a>Anfängliche Migration

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:

* Fügen Sie eine anfängliche Migration hinzu.
* Aktualisieren Sie die Datenbank mit der anfänglichen Migration.

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration Initial
Update-Database
```

Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen. Das Schema basiert auf dem Modell in `DbContext` in der Datei *RazorPagesMovieContext.cs*. Das Argument `InitialCreate` wird verwendet, um die Migration zu benennen. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt. Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.

Der Befehl `Update-Database` führt die Methode `Up` in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus. Die Methode `Up` erstellt die Datenbank.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")

Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt. Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.

Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.

Untersuchen Sie die Methode `Startup.ConfigureServices`. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update und Delete) für das `Movie`-Modell. Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Untersuchen Sie die Methode `Up`.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).

Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Sie haben den [Migrationsschritt](#pmc) verpasst.

* Testen Sie den Link **Create** (Erstellen).

  ![Seite „Create“](model/_static/conan.png)

  > [!NOTE]
  > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

> [!div class="step-by-step"]
> [Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)

::: moniker-end
