---
title: 'Teil 4: Arbeiten mit einer Datenbank'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 8c9d0d9c24e0ce81925ccde463bcf085531b665e
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024734"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="ff1d8-103">Dies ist Teil 4 der Tutorialreihe zu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-103">Part 4 of tutorial series on Razor Pages</span></span>

<span data-ttu-id="ff1d8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ff1d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="ff1d8-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ff1d8-106">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-106">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="ff1d8-107">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-107">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-108">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-109">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-109">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="ff1d8-110">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-110">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="ff1d8-111">Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-111">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-112">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff1d8-113">Die generierte Verbindungszeichenfolge ähnelt dem folgenden JSON-Code:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-113">The generated connection string is similar to the following JSON:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-114">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-114">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="ff1d8-115">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-115">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="ff1d8-116">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-116">For more information, see [Configuration](xref:fundamentals/configuration/index).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-117">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-117">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="ff1d8-118">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ff1d8-118">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ff1d8-119">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-119">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="ff1d8-120">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-120">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ff1d8-121">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:\Users\<user>\`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-121">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
1. <span data-ttu-id="ff1d8-122">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-122">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

   ![Menü Ansicht](sql/_static/5/ssox.png)

1. <span data-ttu-id="ff1d8-124">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-124">Right-click on the `Movie` table and select **View Designer**:</span></span>

   ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/5/design.png)

   ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

   <span data-ttu-id="ff1d8-127">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-127">Note the key icon next to `ID`.</span></span> <span data-ttu-id="ff1d8-128">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-128">By default, EF creates a property named `ID` for the primary key.</span></span>

1. <span data-ttu-id="ff1d8-129">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-129">Right-click on the `Movie` table and select **View Data**:</span></span>

   ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-131">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-131">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="ff1d8-132">SQLite</span><span class="sxs-lookup"><span data-stu-id="ff1d8-132">SQLite</span></span>

<span data-ttu-id="ff1d8-133">Auf der [SQLite](https://www.sqlite.org/)-Website ist zu lesen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-133">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="ff1d8-134">SQLite ist eine eigenständige, sehr zuverlässige, eingebettete, genehmigungsfreie SQL-Datenbank-Engine mit vollem Funktionsumfang.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-134">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="ff1d8-135">SQLite ist die weltweit am häufigsten verwendete Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-135">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="ff1d8-136">Es gibt viele Tools von Drittanbietern, die Sie herunterladen können, um eine SQLite-Datenbank zu verwalten und anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-136">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="ff1d8-137">Die folgende Abbildung stammt aus [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-137">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="ff1d8-138">Wenn Sie ein bestimmtes SQLite-Tool bevorzugen, geben Sie bitte in einem Kommentar dessen Vorteile an.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-138">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mit der Filmdatenbank](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="ff1d8-140">Für dieses Tutorial wird nach Möglichkeit das Entity Framework Core-Feature *Migrationen* verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-140">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="ff1d8-141">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-141">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="ff1d8-142">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-142">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="ff1d8-143">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-143">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="ff1d8-144">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-144">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="ff1d8-145">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-145">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="ff1d8-146">Wenn sich das Schema ändert, wird stattdessen die Datenbank gelöscht und neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-146">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="ff1d8-147">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-147">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="ff1d8-148">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-148">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="ff1d8-149">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-149">Creating a new table.</span></span>
>* <span data-ttu-id="ff1d8-150">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-150">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="ff1d8-151">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-151">Dropping the old table.</span></span>
>* <span data-ttu-id="ff1d8-152">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-152">Renaming the new table.</span></span>
>
><span data-ttu-id="ff1d8-153">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-153">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="ff1d8-154">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ff1d8-154">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="ff1d8-155">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="ff1d8-155">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="ff1d8-156">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="ff1d8-156">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="ff1d8-157">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="ff1d8-157">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="ff1d8-158">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="ff1d8-158">Seed the database</span></span>

<span data-ttu-id="ff1d8-159">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-159">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="ff1d8-160">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-160">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="ff1d8-161">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="ff1d8-161">Add the seed initializer</span></span>

<span data-ttu-id="ff1d8-162">Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-162">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

<span data-ttu-id="ff1d8-163">Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-163">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="ff1d8-164">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-164">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ff1d8-165">Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-165">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="ff1d8-166">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-166">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="ff1d8-167">Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-167">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="ff1d8-168">Die folgende Ausnahme tritt auf, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-168">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="ff1d8-169">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ff1d8-169">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-170">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-170">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="ff1d8-171">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-171">Delete all the records in the database.</span></span> <span data-ttu-id="ff1d8-172">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-172">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>

1. <span data-ttu-id="ff1d8-173">Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-173">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="ff1d8-174">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-174">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="ff1d8-175">Sie müssen IIS mit einer der folgenden Vorgehensweisen beenden und neu starten:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-175">Stop and restart IIS with any of the following approaches:</span></span>

   1. <span data-ttu-id="ff1d8-176">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-176">Right-click the IIS Express system tray icon in the notification area and select **Exit** or **Stop Site**:</span></span>

      ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Kontextmenü](sql/_static/stopIIS.png)

   1. <span data-ttu-id="ff1d8-179">Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-179">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
   1. <span data-ttu-id="ff1d8-180">Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-180">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-181">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-181">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ff1d8-182">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-182">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="ff1d8-183">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-183">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="ff1d8-184">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-184">The app shows the seeded data:</span></span>

![Im Browser geöffnete Movie-Anwendung mit Filmdaten](sql/_static/5/m55.png)

## <a name="additional-resources"></a><span data-ttu-id="ff1d8-186">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff1d8-186">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ff1d8-187">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="ff1d8-187">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="ff1d8-188">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-188">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ff1d8-189">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-189">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="ff1d8-190">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-190">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-191">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-192">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-192">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="ff1d8-193">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-193">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="ff1d8-194">Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-194">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-195">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-195">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff1d8-196">Die generierte Verbindungszeichenfolge sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-196">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-197">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-197">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="ff1d8-198">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-198">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="ff1d8-199">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-199">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-200">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="ff1d8-201">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ff1d8-201">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ff1d8-202">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-202">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="ff1d8-203">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-203">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ff1d8-204">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:\Users\<user>\`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-204">By default, LocalDB database creates `*.mdf` files in the `C:\Users\<user>\` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="ff1d8-205">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-205">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="ff1d8-207">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-207">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenüs](sql/_static/design.png)

  ![Im Designer geöffnete Tabellen „Movie“](sql/_static/dv.png)

<span data-ttu-id="ff1d8-210">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-210">Note the key icon next to `ID`.</span></span> <span data-ttu-id="ff1d8-211">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-211">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="ff1d8-212">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-212">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-214">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-214">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a><span data-ttu-id="ff1d8-215">SQLite</span><span class="sxs-lookup"><span data-stu-id="ff1d8-215">SQLite</span></span>

<span data-ttu-id="ff1d8-216">Auf der [SQLite](https://www.sqlite.org/)-Website ist zu lesen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-216">The [SQLite](https://www.sqlite.org/) website states:</span></span>

> <span data-ttu-id="ff1d8-217">SQLite ist eine eigenständige, sehr zuverlässige, eingebettete, genehmigungsfreie SQL-Datenbank-Engine mit vollem Funktionsumfang.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-217">SQLite is a self-contained, high-reliability, embedded, full-featured, public-domain, SQL database engine.</span></span> <span data-ttu-id="ff1d8-218">SQLite ist die weltweit am häufigsten verwendete Datenbank-Engine.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-218">SQLite is the most used database engine in the world.</span></span>

<span data-ttu-id="ff1d8-219">Es gibt viele Tools von Drittanbietern, die Sie herunterladen können, um eine SQLite-Datenbank zu verwalten und anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-219">There are many third-party tools you can download to manage and view a SQLite database.</span></span> <span data-ttu-id="ff1d8-220">Die folgende Abbildung stammt aus [DB Browser for SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-220">The image below is from [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span> <span data-ttu-id="ff1d8-221">Wenn Sie ein bestimmtes SQLite-Tool bevorzugen, geben Sie bitte in einem Kommentar dessen Vorteile an.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-221">If you have a favorite SQLite tool, leave a comment on what you like about it.</span></span>

![DB Browser for SQLite mit der Filmdatenbank](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> <span data-ttu-id="ff1d8-223">Für dieses Tutorial wird nach Möglichkeit das Entity Framework Core-Feature *Migrationen* verwendet.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-223">For this tutorial, the Entity Framework Core *migrations* feature is used where possible.</span></span> <span data-ttu-id="ff1d8-224">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-224">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="ff1d8-225">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-225">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="ff1d8-226">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-226">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="ff1d8-227">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-227">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="ff1d8-228">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-228">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="ff1d8-229">Wenn sich das Schema ändert, wird stattdessen die Datenbank gelöscht und neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-229">Instead, when the schema changes, the database is dropped and re-created.</span></span>
>
><span data-ttu-id="ff1d8-230">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-230">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="ff1d8-231">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-231">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="ff1d8-232">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-232">Creating a new table.</span></span>
>* <span data-ttu-id="ff1d8-233">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-233">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="ff1d8-234">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-234">Dropping the old table.</span></span>
>* <span data-ttu-id="ff1d8-235">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-235">Renaming the new table.</span></span>
>
><span data-ttu-id="ff1d8-236">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-236">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="ff1d8-237">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ff1d8-237">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="ff1d8-238">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="ff1d8-238">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="ff1d8-239">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="ff1d8-239">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="ff1d8-240">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="ff1d8-240">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a><span data-ttu-id="ff1d8-241">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="ff1d8-241">Seed the database</span></span>

<span data-ttu-id="ff1d8-242">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-242">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="ff1d8-243">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-243">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="ff1d8-244">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="ff1d8-244">Add the seed initializer</span></span>

<span data-ttu-id="ff1d8-245">Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-245">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

<span data-ttu-id="ff1d8-246">Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-246">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="ff1d8-247">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-247">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ff1d8-248">Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-248">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="ff1d8-249">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-249">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="ff1d8-250">Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-250">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="ff1d8-251">Die folgende Ausnahme tritt auf, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-251">The following exception occurs when `Update-Database` has not been run:</span></span>

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a><span data-ttu-id="ff1d8-252">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ff1d8-252">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-253">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff1d8-254">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-254">Delete all the records in the database.</span></span> <span data-ttu-id="ff1d8-255">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-255">Use the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox).</span></span>
* <span data-ttu-id="ff1d8-256">Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-256">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="ff1d8-257">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-257">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="ff1d8-258">Sie müssen IIS mit einer der folgenden Vorgehensweisen beenden und neu starten:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-258">Stop and restart IIS with any of the following approaches:</span></span>

  * <span data-ttu-id="ff1d8-259">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-259">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="ff1d8-262">Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-262">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="ff1d8-263">Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-263">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-264">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-264">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="ff1d8-265">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-265">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="ff1d8-266">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-266">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="ff1d8-267">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-267">The app shows the seeded data:</span></span>

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55https.png)

## <a name="additional-resources"></a><span data-ttu-id="ff1d8-269">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff1d8-269">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="ff1d8-270">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="ff1d8-270">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ff1d8-271">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-271">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ff1d8-272">Das `RazorPagesMovieContext`-Objekt übernimmt die Aufgabe der Herstellung der Verbindung mit der Datenbank und Zuordnung von `Movie`-Objekten zu Datensätzen in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-272">The `RazorPagesMovieContext` object handles the task of connecting to the database and mapping `Movie` objects to database records.</span></span> <span data-ttu-id="ff1d8-273">Der Datenbankkontext wird beim Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) in der `ConfigureServices`-Methode in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-273">The database context is registered with the [Dependency Injection](xref:fundamentals/dependency-injection) container in the `ConfigureServices` method in *Startup.cs*:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-274">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-275">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-275">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

<span data-ttu-id="ff1d8-276">Weitere Informationen über die in `ConfigureServices` verwendeten Methoden finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-276">For more information on the methods used in `ConfigureServices`, see:</span></span>

* <span data-ttu-id="ff1d8-277">[Unterstützung für die Datenschutz-Grundverordnung (DSGVO) in ASP.NET Core](xref:security/gdpr) für `CookiePolicyOptions`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-277">[EU General Data Protection Regulation (GDPR) support in ASP.NET Core](xref:security/gdpr) for `CookiePolicyOptions`.</span></span>
* [<span data-ttu-id="ff1d8-278">SetCompatibilityVersion</span><span class="sxs-lookup"><span data-stu-id="ff1d8-278">SetCompatibilityVersion</span></span>](xref:mvc/compatibility-version)

<span data-ttu-id="ff1d8-279">Das ASP.NET Core-[Konfigurationssystem](xref:fundamentals/configuration/index) liest den Schlüssel `ConnectionString`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-279">The ASP.NET Core [Configuration](xref:fundamentals/configuration/index) system reads the `ConnectionString` key.</span></span> <span data-ttu-id="ff1d8-280">Für die lokale Entwicklung wird für die Konfiguration die Verbindungszeichenfolge aus der Datei *appsettings.json* abgerufen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-280">For local development, configuration gets the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-281">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-281">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ff1d8-282">Die generierte Verbindungszeichenfolge sieht in etwa wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-282">The generated connection string will be similar to the following:</span></span>

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff1d8-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff1d8-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-284">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-284">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

<span data-ttu-id="ff1d8-285">Wenn die App auf einem Test- oder Produktionsserver bereitgestellt wird, kann eine Umgebungsvariable verwendet werden, um die Verbindungszeichenfolge auf einen echten Datenbankserver für Tests oder die Produktion festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-285">When the app is deployed to a test or production server, an environment variable can be used to set the connection string to a test or production database server.</span></span> <span data-ttu-id="ff1d8-286">Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-286">See [Configuration](xref:fundamentals/configuration/index) for more information.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-287">Visual Studio</span></span>](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a><span data-ttu-id="ff1d8-288">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ff1d8-288">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ff1d8-289">LocalDB ist eine Basisversion der SQL Server Express-Datenbank-Engine, die für die Programmentwicklung bestimmt ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-289">LocalDB is a lightweight version of the SQL Server Express database engine that's targeted for program development.</span></span> <span data-ttu-id="ff1d8-290">LocalDB wird bedarfsgesteuert gestartet und im Benutzermodus ausgeführt, sodass keine komplexe Konfiguration anfällt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-290">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ff1d8-291">Standardmäßig erstellt LocalDB `*.mdf`-Dateien im `C:/Users/<user/>`-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-291">By default, LocalDB database creates `*.mdf` files in the `C:/Users/<user/>` directory.</span></span>

<a name="ssox"></a>
* <span data-ttu-id="ff1d8-292">Öffnen Sie im Menü **Ansicht** den **SQL Server-Objekt-Explorer** (SSOX).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-292">From the **View** menu, open **SQL Server Object Explorer** (SSOX).</span></span>

  ![Menü Ansicht](sql/_static/ssox.png)

* <span data-ttu-id="ff1d8-294">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Designer anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-294">Right-click on the `Movie` table and select **View Designer**:</span></span>

  ![Für die Tabelle „Movie“ geöffnetes Kontextmenü](sql/_static/design.png)

  ![Im Designer geöffnete Tabelle „Movie“](sql/_static/dv.png)

<span data-ttu-id="ff1d8-297">Beachten Sie das Schlüsselsymbol neben `ID`.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-297">Note the key icon next to `ID`.</span></span> <span data-ttu-id="ff1d8-298">EF erstellt standardmäßig eine Eigenschaft namens `ID` für den Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-298">By default, EF creates a property named `ID` for the primary key.</span></span>

* <span data-ttu-id="ff1d8-299">Klicken Sie mit der rechten Maustaste auf die Tabelle `Movie`, und wählen Sie **Daten anzeigen** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-299">Right-click on the `Movie` table and select **View Data**:</span></span>

  ![Geöffnete Tabelle „Movie“ mit Tabellendaten](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff1d8-301">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff1d8-301">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-302">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-302">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a><span data-ttu-id="ff1d8-303">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="ff1d8-303">Seed the database</span></span>

<span data-ttu-id="ff1d8-304">Erstellen Sie im Ordner *Models* mit dem folgenden Code die neue Klasse `SeedData`:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-304">Create a new class named `SeedData` in the *Models* folder with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

<span data-ttu-id="ff1d8-305">Wenn in der Datenbank Filme vorhanden sind, wird der Initialisierer des Seedings zurückgegeben, und es werden keine Filme hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-305">If there are any movies in the database, the seed initializer returns and no movies are added.</span></span>

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a><span data-ttu-id="ff1d8-306">Hinzufügen des Initialisierers des Seedings</span><span class="sxs-lookup"><span data-stu-id="ff1d8-306">Add the seed initializer</span></span>

<span data-ttu-id="ff1d8-307">Ersetzen Sie den Inhalt der Datei *Program.cs* durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-307">Replace the contents of the *Program.cs* with the following code:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

<span data-ttu-id="ff1d8-308">Im vorherigen Code wurde die `Main`-Methode geändert, um Folgendes durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-308">In the previous code, the `Main` method has been modified to do the following:</span></span>

* <span data-ttu-id="ff1d8-309">Rufen Sie eine Datenbankkontextinstanz aus dem Dependency Injection-Container ab.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-309">Get a database context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ff1d8-310">Rufen Sie die `seedData.Initialize`-Methode auf, und übergeben Sie sie an die Instanz mit dem Datenbankkontext.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-310">Call the `seedData.Initialize` method, passing to it the database context instance.</span></span>
* <span data-ttu-id="ff1d8-311">Löschen Sie den Kontext, wenn die Seedmethode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-311">Dispose the context when the seed method completes.</span></span> <span data-ttu-id="ff1d8-312">Die [using-Anweisung](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) stellt sicher, dass der Kontext verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-312">The [using statement](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) ensures the context is disposed.</span></span>

<span data-ttu-id="ff1d8-313">Eine Produktions-App würde `Database.Migrate` nicht aufrufen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-313">A production app would not call `Database.Migrate`.</span></span> <span data-ttu-id="ff1d8-314">Es wird zum vorangehenden Code hinzugefügt, um die folgende Ausnahme zu verhindern, wenn `Update-Database` nicht ausgeführt wurde:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-314">It's added to the preceding code to prevent the following exception when `Update-Database` has not been run:</span></span>

<span data-ttu-id="ff1d8-315">SqlException: Die bei der Anmeldung angeforderte Datenbank „RazorPagesMovieContext-21“ kann nicht geöffnet werden.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-315">SqlException: Cannot open database "RazorPagesMovieContext-21" requested by the login.</span></span> <span data-ttu-id="ff1d8-316">Die Anmeldung ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-316">The login failed.</span></span>
<span data-ttu-id="ff1d8-317">Die Anmeldung des Benutzers „Benutzername“ ist fehlgeschlagen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-317">Login failed for user 'user name'.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ff1d8-318">Testen der App</span><span class="sxs-lookup"><span data-stu-id="ff1d8-318">Test the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ff1d8-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ff1d8-319">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ff1d8-320">Löschen Sie alle Datensätze in der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-320">Delete all the records in the database.</span></span> <span data-ttu-id="ff1d8-321">Dies ist über die Links „Löschen“ im Browser oder [SSOX](xref:tutorials/razor-pages/new-field#ssox) möglich.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-321">You can do this with the delete links in the browser or from [SSOX](xref:tutorials/razor-pages/new-field#ssox)</span></span>
* <span data-ttu-id="ff1d8-322">Zwingen Sie die App zur Initialisierung (rufen Sie dazu die Methoden in der `Startup`-Klasse auf), damit die Seed-Methode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-322">Force the app to initialize by calling the methods in the `Startup` class, so the seed method runs.</span></span> <span data-ttu-id="ff1d8-323">Um die Initialisierung zu erzwingen, muss IIS Express beendet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-323">To force initialization, IIS Express must be stopped and restarted.</span></span> <span data-ttu-id="ff1d8-324">Hierzu können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-324">You can do this with any of the following approaches:</span></span>

  * <span data-ttu-id="ff1d8-325">Klicken Sie auf der Taskleiste im Infobereich mit der rechten Maustaste auf das Symbol von IIS Express, und wählen Sie **Beenden** oder **Website beenden** aus:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-325">Right-click the IIS Express system tray icon in the notification area and tap **Exit** or **Stop Site**:</span></span>

    ![IIS Express-Symbol auf der Taskleiste](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Kontextmenü](sql/_static/stopIIS.png)

    * <span data-ttu-id="ff1d8-328">Wenn die App nicht im Debugmodus ausgeführt wird, drücken Sie <kbd>F5</kbd>, um sie im Debugmodus auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-328">If the app is running in non-debug mode, press <kbd>F5</kbd> to run in debug mode.</span></span>
    * <span data-ttu-id="ff1d8-329">Wenn die App im Debugmodus ausgeführt wird, beenden Sie den Debugger, und drücken Sie <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-329">If the app in debug mode, stop the debugger and press <kbd>F5</kbd>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ff1d8-330">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ff1d8-330">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ff1d8-331">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-331">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="ff1d8-332">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-332">Stop and start the app to seed the database.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="ff1d8-333">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="ff1d8-333">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="ff1d8-334">Löschen Sie alle Datensätze in der Datenbank (damit die Seed-Methode ausgeführt wird).</span><span class="sxs-lookup"><span data-stu-id="ff1d8-334">Delete all the records in the database, so the seed method will run.</span></span> <span data-ttu-id="ff1d8-335">Beenden und starten Sie die App, um das Seeding der Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-335">Stop and start the app to seed the database.</span></span>

---

<span data-ttu-id="ff1d8-336">Die App zeigt die per Seeding hinzugefügten Daten:</span><span class="sxs-lookup"><span data-stu-id="ff1d8-336">The app shows the seeded data:</span></span>

![In Chrome geöffnete Movie-Anwendung mit Filmdaten](sql/_static/m55https.png)

<span data-ttu-id="ff1d8-338">Im nächsten Tutorial wird die Präsentation der Daten bereinigt.</span><span class="sxs-lookup"><span data-stu-id="ff1d8-338">The next tutorial will clean up the presentation of the data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ff1d8-339">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ff1d8-339">Additional resources</span></span>

* [<span data-ttu-id="ff1d8-340">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="ff1d8-340">YouTube version of this tutorial</span></span>](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> <span data-ttu-id="ff1d8-341">[Zurück: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)
> [Weiter: Aktualisieren der Seiten](xref:tutorials/razor-pages/da1)</span><span class="sxs-lookup"><span data-stu-id="ff1d8-341">[Previous: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)
[Next: Update the pages](xref:tutorials/razor-pages/da1)</span></span>

::: moniker-end
