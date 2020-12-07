---
title: 'Teil 7: Hinzufügen eines neuen Felds'
author: rick-anderson
description: Dies ist Teil 7 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.custom: mvc
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
ms.openlocfilehash: 6b6856731c61957a9e23f76e2bc15befe56ea57d
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96420005"
---
# <a name="part-7-add-a-new-field-to-a-no-locrazor-page-in-aspnet-core"></a><span data-ttu-id="547a3-103">Teil 7: Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="547a3-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="547a3-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="547a3-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="547a3-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="547a3-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="547a3-106">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="547a3-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="547a3-107">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="547a3-107">Add a new field to the model.</span></span>
* <span data-ttu-id="547a3-108">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="547a3-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="547a3-109">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="547a3-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="547a3-110">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="547a3-110">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="547a3-111">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="547a3-112">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="547a3-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="547a3-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="547a3-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="547a3-114">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="547a3-115">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="547a3-115">Build the app.</span></span>

1. <span data-ttu-id="547a3-116">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="547a3-117">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="547a3-117">Update the following pages:</span></span>
   1. <span data-ttu-id="547a3-118">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="547a3-119">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="547a3-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="547a3-120">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="547a3-121">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="547a3-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="547a3-122">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="547a3-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="547a3-123">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="547a3-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="547a3-124">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="547a3-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="547a3-125">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="547a3-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="547a3-126">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="547a3-127">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="547a3-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="547a3-128">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="547a3-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="547a3-129">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="547a3-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="547a3-130">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="547a3-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="547a3-131">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="547a3-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="547a3-132">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="547a3-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="547a3-133">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="547a3-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="547a3-134">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="547a3-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="547a3-135">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="547a3-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="547a3-136">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="547a3-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="547a3-137">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="547a3-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="547a3-138">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="547a3-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="547a3-139">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="547a3-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="547a3-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="547a3-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="547a3-141">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="547a3-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="547a3-142">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="547a3-143">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="547a3-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="547a3-144">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="547a3-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="547a3-145">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="547a3-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="547a3-146">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="547a3-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="547a3-147">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="547a3-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="547a3-148">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="547a3-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="547a3-149">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="547a3-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="547a3-150">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="547a3-151">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="547a3-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="547a3-152">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="547a3-153">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="547a3-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="547a3-154">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="547a3-155">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="547a3-156">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="547a3-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="547a3-157">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="547a3-157">Select **OK**.</span></span>
1. <span data-ttu-id="547a3-158">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="547a3-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="547a3-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="547a3-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="547a3-160">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="547a3-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="547a3-161">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="547a3-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="547a3-162">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="547a3-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="547a3-163">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="547a3-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="547a3-164">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="547a3-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="547a3-165">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="547a3-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="547a3-166">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="547a3-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="547a3-167">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="547a3-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="547a3-168">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="547a3-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="547a3-169">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="547a3-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="547a3-170">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-170">Creating a new table.</span></span>
>* <span data-ttu-id="547a3-171">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="547a3-172">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-172">Dropping the old table.</span></span>
>* <span data-ttu-id="547a3-173">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-173">Renaming the new table.</span></span>
>
><span data-ttu-id="547a3-174">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="547a3-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="547a3-175">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="547a3-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="547a3-176">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="547a3-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="547a3-177">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="547a3-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="547a3-178">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="547a3-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="547a3-179">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="547a3-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="547a3-180">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="547a3-181">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="547a3-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="547a3-182">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="547a3-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="547a3-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="547a3-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="547a3-184">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="547a3-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="547a3-185">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="547a3-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="547a3-186">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="547a3-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="547a3-187">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="547a3-187">Add a new field to the model.</span></span>
* <span data-ttu-id="547a3-188">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="547a3-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="547a3-189">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="547a3-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="547a3-190">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="547a3-190">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="547a3-191">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="547a3-192">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="547a3-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="547a3-193">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="547a3-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="547a3-194">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="547a3-195">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="547a3-195">Build the app.</span></span>

1. <span data-ttu-id="547a3-196">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="547a3-197">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="547a3-197">Update the following pages:</span></span>
   1. <span data-ttu-id="547a3-198">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="547a3-199">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="547a3-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="547a3-200">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="547a3-201">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="547a3-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="547a3-202">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="547a3-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="547a3-203">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="547a3-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="547a3-204">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="547a3-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="547a3-205">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="547a3-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="547a3-206">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="547a3-207">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="547a3-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="547a3-208">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="547a3-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="547a3-209">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="547a3-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="547a3-210">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="547a3-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="547a3-211">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="547a3-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="547a3-212">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="547a3-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="547a3-213">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="547a3-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="547a3-214">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="547a3-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="547a3-215">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="547a3-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="547a3-216">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="547a3-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="547a3-217">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="547a3-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="547a3-218">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="547a3-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="547a3-219">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="547a3-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="547a3-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="547a3-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="547a3-221">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="547a3-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="547a3-222">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="547a3-223">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="547a3-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="547a3-224">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="547a3-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="547a3-225">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="547a3-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="547a3-226">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="547a3-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="547a3-227">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="547a3-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="547a3-228">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="547a3-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="547a3-229">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="547a3-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="547a3-230">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="547a3-231">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="547a3-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="547a3-232">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="547a3-233">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="547a3-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="547a3-234">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="547a3-235">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="547a3-236">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="547a3-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="547a3-237">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="547a3-237">Select **OK**.</span></span>
* <span data-ttu-id="547a3-238">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="547a3-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="547a3-239">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="547a3-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="547a3-240">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="547a3-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="547a3-241">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="547a3-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="547a3-242">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="547a3-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="547a3-243">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="547a3-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="547a3-244">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="547a3-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="547a3-245">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="547a3-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="547a3-246">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="547a3-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="547a3-247">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="547a3-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="547a3-248">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="547a3-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="547a3-249">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="547a3-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="547a3-250">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-250">Creating a new table.</span></span>
>* <span data-ttu-id="547a3-251">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="547a3-252">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-252">Dropping the old table.</span></span>
>* <span data-ttu-id="547a3-253">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-253">Renaming the new table.</span></span>
>
><span data-ttu-id="547a3-254">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="547a3-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="547a3-255">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="547a3-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="547a3-256">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="547a3-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="547a3-257">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="547a3-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="547a3-258">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="547a3-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="547a3-259">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="547a3-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="547a3-260">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="547a3-261">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="547a3-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="547a3-262">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="547a3-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="547a3-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="547a3-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="547a3-264">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="547a3-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="547a3-265">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="547a3-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="547a3-266">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="547a3-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="547a3-267">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="547a3-267">Add a new field to the model.</span></span>
* <span data-ttu-id="547a3-268">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="547a3-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="547a3-269">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="547a3-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="547a3-270">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="547a3-270">Adds an [`__EFMigrationsHistory`](https://docs.microsoft.com/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="547a3-271">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="547a3-272">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="547a3-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="547a3-273">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="547a3-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="547a3-274">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="547a3-275">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="547a3-275">Build the app.</span></span>

<span data-ttu-id="547a3-276">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="547a3-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="547a3-277">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="547a3-277">Update the following pages:</span></span>

* <span data-ttu-id="547a3-278">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="547a3-279">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="547a3-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="547a3-280">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="547a3-281">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="547a3-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="547a3-282">Wenn die App jetzt ausgeführt wird, löst sie eine `SqlException` aus:</span><span class="sxs-lookup"><span data-stu-id="547a3-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="547a3-283">Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="547a3-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="547a3-284">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="547a3-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="547a3-285">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="547a3-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="547a3-286">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="547a3-287">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="547a3-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="547a3-288">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="547a3-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="547a3-289">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="547a3-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="547a3-290">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="547a3-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="547a3-291">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="547a3-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="547a3-292">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="547a3-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="547a3-293">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="547a3-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="547a3-294">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="547a3-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="547a3-295">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="547a3-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="547a3-296">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="547a3-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="547a3-297">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="547a3-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="547a3-298">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="547a3-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="547a3-299">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="547a3-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="547a3-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="547a3-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="547a3-301">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="547a3-301">Add a migration for the rating field</span></span>

<span data-ttu-id="547a3-302">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="547a3-303">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="547a3-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="547a3-304">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="547a3-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="547a3-305">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="547a3-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="547a3-306">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="547a3-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="547a3-307">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="547a3-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="547a3-308">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="547a3-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="547a3-309">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="547a3-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="547a3-310">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="547a3-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="547a3-311">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="547a3-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="547a3-312">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="547a3-313">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="547a3-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="547a3-314">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="547a3-315">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="547a3-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="547a3-316">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="547a3-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="547a3-317">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="547a3-317">Select **OK**.</span></span>
* <span data-ttu-id="547a3-318">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="547a3-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="547a3-319">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="547a3-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="547a3-320">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="547a3-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="547a3-321">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="547a3-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="547a3-322">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="547a3-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="547a3-323">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="547a3-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="547a3-324">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="547a3-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="547a3-325">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="547a3-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="547a3-326">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="547a3-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="547a3-327">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="547a3-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="547a3-328">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="547a3-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="547a3-329">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="547a3-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="547a3-330">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-330">Creating a new table.</span></span>
>* <span data-ttu-id="547a3-331">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="547a3-332">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-332">Dropping the old table.</span></span>
>* <span data-ttu-id="547a3-333">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="547a3-333">Renaming the new table.</span></span>
>
><span data-ttu-id="547a3-334">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="547a3-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="547a3-335">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="547a3-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="547a3-336">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="547a3-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="547a3-337">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="547a3-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="547a3-338">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="547a3-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="547a3-339">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="547a3-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="547a3-340">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="547a3-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="547a3-341">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="547a3-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="547a3-342">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="547a3-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="547a3-343">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="547a3-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="547a3-344">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="547a3-344">Additional resources</span></span>

* [<span data-ttu-id="547a3-345">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="547a3-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="547a3-346">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="547a3-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
