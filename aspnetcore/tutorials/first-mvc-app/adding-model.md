---
title: 'Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 11/16/2020
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
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 16cef6cc9e772f494515942072c2aaf58913ce91
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688448"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a>Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)

In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu. Diese Klassen bilden den „**M** odell“-Teil der **M** VC-App.

Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten. EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.

Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P** lain **O** ld **C** LR **O** bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen. Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.

In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Hinzufügen einer Datenmodellklasse

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**. Nennen Sie die Datei *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**. Nennen Sie die Datei *Movie.cs*.

---

Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.

Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

* Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
* Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.

## <a name="add-nuget-packages"></a>Hinzufügen von NuGet-Paketen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

Führen Sie den folgenden Befehl in der PMC aus:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt. Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit. Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.

Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen. Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt. Klicken Sie auf die Schaltfläche **OK**.

Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt. Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.

Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

Führen Sie den folgenden .NET-CLI-Befehl aus:

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

Durch den vorangehenden Befehl wird das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator) hinzugefügt.

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Erstellen einer Datenbankkontextklasse

Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren. Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.

Erstellen Sie einen Ordner *Data*.

Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt. Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert. In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.

## <a name="scaffold-movie-pages"></a>Gerüstbau der Movie-Seiten

Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:

* **Modellklasse:** *Movie (MvcMovie.Models)*
* **Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*

![Datenkontext hinzufügen](adding-model/_static/dc5.png)

* **Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.
* **Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.
* Wählen Sie **Hinzufügen** aus.

Visual Studio erstellt Folgendes:

* Einen Movies-Controller (*Controllers/MoviesController.cs*)
* Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)

Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).

* Exportieren Sie unter Linux den Pfad des Gerüstbautools:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist. Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.

<a name="migration"></a>

## <a name="initial-migration"></a>Anfängliche Migration

Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen. Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*. Das `InitialCreate`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt. Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas. Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.

* `Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat. Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

  Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung: 

  > No type was specified for the decimal column 'Price' on entity type 'Movie'. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)

  Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*. Das `InitialCreate`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt. Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas. Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.

* `ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat. Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

---

### <a name="the-initialcreate-class"></a>Die InitialCreate-Klasse

Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel. Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.

<a name="test"></a>

## <a name="test-the-app"></a>Testen der App

* Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.

  Wenn eine Ausnahme auftritt, die der folgenden ähnelt:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.

* Testen Sie die Seite **Create** (Erstellen). Geben Sie die Daten ein, und senden Sie sie.

  > [!NOTE]
  > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

## <a name="dependency-injection-in-the-controller"></a>Abhängigkeitsinjektion im Controller

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup. `IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Stark typisierte Modelle und das Schlüsselwort @model

Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann. Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.

MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben. Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit. Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.

Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben. `https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:

* Den Controller auf den Controller `movies` (das erste URL-Segment).
* Die Aktion auf `details` (das zweite URL-Segment).
* Die ID auf 1 (das letzte URL-Segment).

Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:

`https://localhost:5001/movies/details?id=1`

Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.

Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:

```csharp
return View(movie);
```

Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet. Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:

```cshtml
@model MvcMovie.Models.Movie
```

Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat. Das `Model`-Objekt ist stark typisiert. In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt. Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.

Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller. Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt. Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird. In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert. Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Globalisierung und Lokalisierung](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a>Hinzufügen einer Datenmodellklasse

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**. Nennen Sie die Datei *Movie.cs*.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**. Nennen Sie die Datei *Movie.cs*.

---

Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.

Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an. Mit diesem Attribut:

* Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.
* Nur das Datum wird angezeigt, keine Zeitinformationen.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.

## <a name="add-nuget-packages"></a>Hinzufügen von NuGet-Paketen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

Führen Sie den folgenden Befehl in der PMC aus:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt. Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit. Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.

Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen. Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt. Klicken Sie auf die Schaltfläche **OK**.

Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt. Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.

Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Erstellen einer Datenbankkontextklasse

Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren. Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.

Erstellen Sie einen Ordner *Data*.

Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Registrieren des Datenbankkontexts

ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt. Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert. In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.

Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Hinzufügen einer Datenbank-Verbindungszeichenfolge

Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.

## <a name="scaffold-movie-pages"></a>Gerüstbau der Movie-Seiten

Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:

* **Modellklasse:** *Movie (MvcMovie.Models)*
* **Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* **Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.
* **Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.
* Wählen Sie **Hinzufügen** aus.

Visual Studio erstellt Folgendes:

* Einen Movies-Controller (*Controllers/MoviesController.cs*)
* Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)

Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).

* Exportieren Sie unter Linux den Pfad des Gerüstbautools:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist. Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.

<a name="migration"></a>

## <a name="initial-migration"></a>Anfängliche Migration

Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen. Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.

Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*. Das `InitialCreate`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt. Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas. Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.

* `Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat. Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

  Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung: 

  > No type was specified for the decimal column 'Price' on entity type 'Movie'. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)

  Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*. Das `InitialCreate`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt. Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas. Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.

* `ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat. Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

---

### <a name="the-initialcreate-class"></a>Die InitialCreate-Klasse

Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel. Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.

<a name="test"></a>

## <a name="test-the-app"></a>Testen der App

* Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.

  Wenn eine Ausnahme auftritt, die der folgenden ähnelt:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.

* Testen Sie die Seite **Create** (Erstellen). Geben Sie die Daten ein, und senden Sie sie.

  > [!NOTE]
  > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

## <a name="dependency-injection-in-the-controller"></a>Abhängigkeitsinjektion im Controller

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion

Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit. Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup. `IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Stark typisierte Modelle und das Schlüsselwort @model

Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann. Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.

MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben. Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit. Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.

Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben. `https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:

* Den Controller auf den Controller `movies` (das erste URL-Segment).
* Die Aktion auf `details` (das zweite URL-Segment).
* Die ID auf 1 (das letzte URL-Segment).

Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:

`https://localhost:5001/movies/details?id=1`

Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.

Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:

```csharp
return View(movie);
```

Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet. Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:

```cshtml
@model MvcMovie.Models.Movie
```

Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat. Das `Model`-Objekt ist stark typisiert. In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt. Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.

Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller. Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt. Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird. In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert. Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Globalisierung und Lokalisierung](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Hinzufügen einer Datenmodellklasse

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**. Nennen Sie die Klasse **Movie**.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

* Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Erstellen des Gerüsts für das Filmmodell

In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt. Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:

* **Modellklasse:** *Movie (MvcMovie.Models)*
* **Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* **Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.
* **Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.
* Wählen Sie **Hinzufügen** aus.

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

Visual Studio erstellt Folgendes:

* Eine Entity Framework Core-[Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)
* Einen Movies-Controller (*Controllers/MoviesController.cs*)
* Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)

Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Installieren Sie das Gerüstbautool:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Exportieren Sie unter Linux den Pfad des Gerüstbautools:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).
* Installieren Sie das Gerüstbautool:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Führen Sie den folgenden Befehl aus:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations). Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.

<a name="pmc"></a>

## <a name="initial-migration"></a>Anfängliche Migration

In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:

* Fügen Sie eine anfängliche Migration hinzu.
* Aktualisieren Sie die Datenbank mit der anfänglichen Migration.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. Geben Sie in der PMC die folgenden Befehle ein:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.

   Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist. Das `Initial`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt. Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.

   Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.

Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist. Das `InitialCreate`-Argument ist der Migrationsname. Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Überprüfen des mit Dependency Injection registrierten Kontexts

ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt. Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert. Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt. Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.

Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode. Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell. Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet. Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge. In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle. Entitäten entsprechen Zeilen in Tabellen.

Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen. Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.

---

<a name="test"></a>

### <a name="test-the-app"></a>Testen der App

* Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).

Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

Sie haben den [Migrationsschritt](#pmc) verpasst.

* Testen Sie den Link **Create** (Erstellen). Geben Sie die Daten ein, und senden Sie sie.

  > [!NOTE]
  > Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben. Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden. Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).

* Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).

Überprüfen Sie die `Startup`-Klasse:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:

* `services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.
* `=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).

Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller. Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Stark typisierte Modelle und das Schlüsselwort @model

Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann. Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.

MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben. Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit. Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.

Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben. `https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:

* Den Controller auf den Controller `movies` (das erste URL-Segment).
* Die Aktion auf `details` (das zweite URL-Segment).
* Die ID auf 1 (das letzte URL-Segment).

Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:

`https://localhost:5001/movies/details?id=1`

Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.

Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:

```csharp
return View(movie);
   ```

Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet. Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:

```cshtml
@model MvcMovie.Models.Movie
```

Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir. In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt. Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.

Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller. Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt. Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird. In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert. Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro)
* [Globalisierung und Lokalisierung](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit einer Datenbank](working-with-sql.md)

::: moniker-end
