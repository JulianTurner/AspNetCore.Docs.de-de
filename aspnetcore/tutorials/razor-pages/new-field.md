---
title: 'Teil 7: Hinzufügen eines neuen Felds'
author: rick-anderson
description: Dies ist Teil 7 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.custom: mvc, contperf-fy21q2
ms.date: 09/28/2020
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
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 2961fcaa8bbb93527eb80f5aa7b18ef04761fb50
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486160"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="d08af-103">Teil 7: Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d08af-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="d08af-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d08af-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d08af-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d08af-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d08af-106">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="d08af-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d08af-107">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d08af-107">Add a new field to the model.</span></span>
* <span data-ttu-id="d08af-108">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="d08af-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d08af-109">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="d08af-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d08af-110">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d08af-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d08af-111">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="d08af-112">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d08af-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d08af-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="d08af-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="d08af-114">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="d08af-115">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="d08af-115">Build the app.</span></span>

1. <span data-ttu-id="d08af-116">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="d08af-117">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="d08af-117">Update the following pages:</span></span>
   1. <span data-ttu-id="d08af-118">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="d08af-119">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="d08af-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="d08af-120">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d08af-121">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="d08af-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="d08af-122">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="d08af-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d08af-123">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="d08af-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d08af-124">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="d08af-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="d08af-125">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="d08af-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d08af-126">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d08af-127">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="d08af-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d08af-128">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="d08af-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d08af-129">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="d08af-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="d08af-130">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="d08af-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d08af-131">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d08af-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d08af-132">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="d08af-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="d08af-133">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="d08af-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d08af-134">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d08af-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d08af-135">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d08af-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d08af-136">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d08af-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d08af-137">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="d08af-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d08af-138">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="d08af-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d08af-139">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="d08af-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d08af-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d08af-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d08af-141">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="d08af-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="d08af-142">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="d08af-143">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d08af-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="d08af-144">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="d08af-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d08af-145">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="d08af-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="d08af-146">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="d08af-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="d08af-147">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="d08af-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d08af-148">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d08af-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d08af-149">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="d08af-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d08af-150">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="d08af-151">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="d08af-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d08af-152">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d08af-153">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="d08af-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="d08af-154">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="d08af-155">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="d08af-156">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="d08af-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="d08af-157">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="d08af-157">Select **OK**.</span></span>
1. <span data-ttu-id="d08af-158">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="d08af-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d08af-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d08af-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d08af-160">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="d08af-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="d08af-161">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="d08af-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="d08af-162">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="d08af-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="d08af-163">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="d08af-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="d08af-164">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d08af-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="d08af-165">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="d08af-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="d08af-166">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="d08af-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="d08af-167">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="d08af-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="d08af-168">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="d08af-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="d08af-169">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="d08af-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="d08af-170">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-170">Creating a new table.</span></span>
>* <span data-ttu-id="d08af-171">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="d08af-172">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-172">Dropping the old table.</span></span>
>* <span data-ttu-id="d08af-173">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-173">Renaming the new table.</span></span>
>
><span data-ttu-id="d08af-174">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="d08af-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="d08af-175">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="d08af-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="d08af-176">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="d08af-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="d08af-177">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="d08af-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="d08af-178">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="d08af-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="d08af-179">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="d08af-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="d08af-180">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="d08af-181">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="d08af-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d08af-182">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="d08af-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d08af-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d08af-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d08af-184">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d08af-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="d08af-185">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d08af-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d08af-186">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="d08af-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d08af-187">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d08af-187">Add a new field to the model.</span></span>
* <span data-ttu-id="d08af-188">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="d08af-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d08af-189">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="d08af-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d08af-190">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d08af-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d08af-191">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="d08af-192">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d08af-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d08af-193">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="d08af-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="d08af-194">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="d08af-195">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="d08af-195">Build the app.</span></span>

1. <span data-ttu-id="d08af-196">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="d08af-197">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="d08af-197">Update the following pages:</span></span>
   1. <span data-ttu-id="d08af-198">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="d08af-199">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="d08af-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="d08af-200">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d08af-201">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="d08af-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="d08af-202">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="d08af-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d08af-203">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="d08af-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d08af-204">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="d08af-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="d08af-205">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="d08af-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d08af-206">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d08af-207">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="d08af-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d08af-208">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="d08af-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d08af-209">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="d08af-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="d08af-210">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="d08af-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d08af-211">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d08af-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d08af-212">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="d08af-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="d08af-213">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="d08af-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d08af-214">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d08af-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d08af-215">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d08af-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d08af-216">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d08af-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d08af-217">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="d08af-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d08af-218">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="d08af-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d08af-219">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="d08af-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d08af-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d08af-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d08af-221">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="d08af-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="d08af-222">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="d08af-223">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d08af-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="d08af-224">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="d08af-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d08af-225">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="d08af-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="d08af-226">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="d08af-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="d08af-227">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="d08af-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d08af-228">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d08af-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d08af-229">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="d08af-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d08af-230">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="d08af-231">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="d08af-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d08af-232">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d08af-233">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="d08af-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d08af-234">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="d08af-235">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="d08af-236">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="d08af-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d08af-237">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="d08af-237">Select **OK**.</span></span>
* <span data-ttu-id="d08af-238">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="d08af-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d08af-239">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d08af-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d08af-240">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="d08af-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="d08af-241">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="d08af-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="d08af-242">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="d08af-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="d08af-243">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="d08af-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="d08af-244">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d08af-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="d08af-245">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="d08af-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="d08af-246">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="d08af-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="d08af-247">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="d08af-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="d08af-248">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="d08af-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="d08af-249">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="d08af-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="d08af-250">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-250">Creating a new table.</span></span>
>* <span data-ttu-id="d08af-251">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="d08af-252">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-252">Dropping the old table.</span></span>
>* <span data-ttu-id="d08af-253">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-253">Renaming the new table.</span></span>
>
><span data-ttu-id="d08af-254">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="d08af-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="d08af-255">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="d08af-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="d08af-256">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="d08af-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="d08af-257">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="d08af-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="d08af-258">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="d08af-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="d08af-259">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="d08af-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="d08af-260">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="d08af-261">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="d08af-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d08af-262">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="d08af-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d08af-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d08af-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d08af-264">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d08af-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d08af-265">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="d08af-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d08af-266">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="d08af-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d08af-267">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d08af-267">Add a new field to the model.</span></span>
* <span data-ttu-id="d08af-268">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="d08af-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d08af-269">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="d08af-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d08af-270">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="d08af-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d08af-271">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="d08af-272">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="d08af-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d08af-273">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="d08af-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d08af-274">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d08af-275">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="d08af-275">Build the app.</span></span>

<span data-ttu-id="d08af-276">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d08af-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="d08af-277">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="d08af-277">Update the following pages:</span></span>

* <span data-ttu-id="d08af-278">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d08af-279">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="d08af-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d08af-280">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d08af-281">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="d08af-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="d08af-282">Wenn die App jetzt ausgeführt wird, löst sie eine `SqlException` aus:</span><span class="sxs-lookup"><span data-stu-id="d08af-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d08af-283">Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="d08af-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d08af-284">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="d08af-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="d08af-285">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="d08af-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d08af-286">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d08af-287">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="d08af-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d08af-288">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="d08af-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d08af-289">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="d08af-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="d08af-290">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="d08af-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d08af-291">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d08af-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d08af-292">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="d08af-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="d08af-293">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="d08af-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d08af-294">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="d08af-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d08af-295">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="d08af-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d08af-296">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="d08af-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d08af-297">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="d08af-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d08af-298">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="d08af-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d08af-299">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="d08af-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d08af-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d08af-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d08af-301">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="d08af-301">Add a migration for the rating field</span></span>

<span data-ttu-id="d08af-302">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d08af-303">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d08af-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d08af-304">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="d08af-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d08af-305">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="d08af-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="d08af-306">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="d08af-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="d08af-307">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="d08af-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d08af-308">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d08af-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d08af-309">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="d08af-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d08af-310">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d08af-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="d08af-311">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="d08af-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d08af-312">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d08af-313">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="d08af-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d08af-314">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="d08af-315">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="d08af-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="d08af-316">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="d08af-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d08af-317">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="d08af-317">Select **OK**.</span></span>
* <span data-ttu-id="d08af-318">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="d08af-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d08af-319">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d08af-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="d08af-320">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="d08af-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="d08af-321">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="d08af-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="d08af-322">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="d08af-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="d08af-323">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="d08af-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="d08af-324">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d08af-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="d08af-325">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="d08af-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="d08af-326">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="d08af-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="d08af-327">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="d08af-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="d08af-328">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="d08af-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="d08af-329">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="d08af-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="d08af-330">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-330">Creating a new table.</span></span>
>* <span data-ttu-id="d08af-331">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="d08af-332">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-332">Dropping the old table.</span></span>
>* <span data-ttu-id="d08af-333">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d08af-333">Renaming the new table.</span></span>
>
><span data-ttu-id="d08af-334">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="d08af-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="d08af-335">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="d08af-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="d08af-336">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="d08af-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="d08af-337">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="d08af-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="d08af-338">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="d08af-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="d08af-339">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d08af-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d08af-340">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="d08af-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d08af-341">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d08af-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="d08af-342">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="d08af-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d08af-343">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="d08af-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d08af-344">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d08af-344">Additional resources</span></span>

* [<span data-ttu-id="d08af-345">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="d08af-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d08af-346">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d08af-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
