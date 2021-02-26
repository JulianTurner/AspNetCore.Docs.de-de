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
ms.openlocfilehash: 852c26f89b229d89797d9ce6fce2983319defe9c
ms.sourcegitcommit: 422e8444b9f5cedc373be5efe8032822db54fcaf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101101145"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="9be02-103">Teil 7: Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9be02-103">Part 7, add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="9be02-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9be02-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9be02-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9be02-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9be02-106">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="9be02-106">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9be02-107">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9be02-107">Add a new field to the model.</span></span>
* <span data-ttu-id="9be02-108">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="9be02-108">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9be02-109">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="9be02-109">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9be02-110">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9be02-110">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9be02-111">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-111">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9be02-112">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="9be02-112">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9be02-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="9be02-113">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="9be02-114">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-114">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="9be02-115">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="9be02-115">Build the app.</span></span>

1. <span data-ttu-id="9be02-116">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-116">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie50/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="9be02-117">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="9be02-117">Update the following pages:</span></span>
   1. <span data-ttu-id="9be02-118">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-118">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="9be02-119">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="9be02-119">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="9be02-120">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-120">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9be02-121">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="9be02-121">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9be02-122">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="9be02-122">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9be02-123">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="9be02-123">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9be02-124">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9be02-124">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9be02-125">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="9be02-125">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9be02-126">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-126">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9be02-127">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="9be02-127">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9be02-128">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="9be02-128">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9be02-129">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="9be02-129">Don't use this approach on a production database!</span></span> <span data-ttu-id="9be02-130">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="9be02-130">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9be02-131">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9be02-131">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9be02-132">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="9be02-132">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9be02-133">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="9be02-133">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9be02-134">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9be02-134">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9be02-135">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="9be02-135">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9be02-136">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9be02-136">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9be02-137">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="9be02-137">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9be02-138">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9be02-138">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9be02-139">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="9be02-139">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9be02-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9be02-140">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9be02-141">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="9be02-141">Add a migration for the rating field</span></span>

1. <span data-ttu-id="9be02-142">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-142">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="9be02-143">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9be02-143">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="9be02-144">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9be02-144">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9be02-145">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="9be02-145">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9be02-146">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="9be02-146">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9be02-147">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9be02-147">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9be02-148">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9be02-148">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9be02-149">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="9be02-149">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9be02-150">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-150">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9be02-151">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="9be02-151">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9be02-152">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-152">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9be02-153">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="9be02-153">To delete the database in SSOX:</span></span>

1. <span data-ttu-id="9be02-154">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-154">Select the database in SSOX.</span></span>
1. <span data-ttu-id="9be02-155">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-155">Right-click on the database, and select **Delete**.</span></span>
1. <span data-ttu-id="9be02-156">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="9be02-156">Check **Close existing connections**.</span></span>
1. <span data-ttu-id="9be02-157">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="9be02-157">Select **OK**.</span></span>
1. <span data-ttu-id="9be02-158">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="9be02-158">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

   ```powershell
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9be02-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9be02-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9be02-160">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="9be02-160">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9be02-161">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="9be02-161">For this tutorial, you use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9be02-162">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="9be02-162">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9be02-163">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="9be02-163">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9be02-164">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be02-164">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9be02-165">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="9be02-165">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9be02-166">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="9be02-166">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9be02-167">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="9be02-167">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9be02-168">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="9be02-168">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9be02-169">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="9be02-169">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9be02-170">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-170">Creating a new table.</span></span>
>* <span data-ttu-id="9be02-171">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-171">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9be02-172">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-172">Dropping the old table.</span></span>
>* <span data-ttu-id="9be02-173">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-173">Renaming the new table.</span></span>
>
><span data-ttu-id="9be02-174">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="9be02-174">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9be02-175">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="9be02-175">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9be02-176">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="9be02-176">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9be02-177">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="9be02-177">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9be02-178">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="9be02-178">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="9be02-179">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="9be02-179">Delete the migration folder.</span></span>  

1. <span data-ttu-id="9be02-180">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-180">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="9be02-181">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="9be02-181">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9be02-182">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="9be02-182">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9be02-183">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9be02-183">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9be02-184">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9be02-184">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

<span data-ttu-id="9be02-185">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9be02-185">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9be02-186">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="9be02-186">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9be02-187">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9be02-187">Add a new field to the model.</span></span>
* <span data-ttu-id="9be02-188">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="9be02-188">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9be02-189">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="9be02-189">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9be02-190">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9be02-190">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9be02-191">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-191">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9be02-192">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="9be02-192">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9be02-193">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="9be02-193">Adding a Rating Property to the Movie Model</span></span>

1. <span data-ttu-id="9be02-194">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-194">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

   [!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

1. <span data-ttu-id="9be02-195">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="9be02-195">Build the app.</span></span>

1. <span data-ttu-id="9be02-196">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-196">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

   <a name="addrat"></a>

   [!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

1. <span data-ttu-id="9be02-197">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="9be02-197">Update the following pages:</span></span>
   1. <span data-ttu-id="9be02-198">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-198">Add the `Rating` field to the Delete and Details pages.</span></span>
   1. <span data-ttu-id="9be02-199">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="9be02-199">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
   1. <span data-ttu-id="9be02-200">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-200">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9be02-201">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="9be02-201">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9be02-202">Wenn Sie die App ohne eine Aktualisierung der Datenbank ausführen, wird eine `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="9be02-202">Running the app without an update to the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9be02-203">Die `SqlException`-Ausnahme wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="9be02-203">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9be02-204">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9be02-204">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9be02-205">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="9be02-205">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9be02-206">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-206">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9be02-207">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="9be02-207">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9be02-208">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="9be02-208">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9be02-209">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="9be02-209">Don't use this approach on a production database!</span></span> <span data-ttu-id="9be02-210">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="9be02-210">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9be02-211">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9be02-211">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9be02-212">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="9be02-212">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9be02-213">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="9be02-213">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9be02-214">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9be02-214">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9be02-215">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="9be02-215">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9be02-216">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9be02-216">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9be02-217">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="9be02-217">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9be02-218">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9be02-218">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9be02-219">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="9be02-219">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9be02-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9be02-220">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9be02-221">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="9be02-221">Add a migration for the rating field</span></span>

1. <span data-ttu-id="9be02-222">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-222">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
2. <span data-ttu-id="9be02-223">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9be02-223">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Rating
   Update-Database
   ```

<span data-ttu-id="9be02-224">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9be02-224">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9be02-225">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="9be02-225">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9be02-226">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="9be02-226">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9be02-227">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9be02-227">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9be02-228">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9be02-228">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9be02-229">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden und vorhandene Daten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="9be02-229">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9be02-230">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-230">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9be02-231">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="9be02-231">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9be02-232">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-232">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9be02-233">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="9be02-233">To delete the database in SSOX:</span></span>

* <span data-ttu-id="9be02-234">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-234">Select the database in SSOX.</span></span>
* <span data-ttu-id="9be02-235">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-235">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="9be02-236">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="9be02-236">Check **Close existing connections**.</span></span>
* <span data-ttu-id="9be02-237">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="9be02-237">Select **OK**.</span></span>
* <span data-ttu-id="9be02-238">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="9be02-238">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9be02-239">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9be02-239">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9be02-240">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="9be02-240">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9be02-241">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="9be02-241">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9be02-242">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="9be02-242">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9be02-243">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="9be02-243">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9be02-244">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be02-244">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9be02-245">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="9be02-245">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9be02-246">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="9be02-246">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9be02-247">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="9be02-247">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9be02-248">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="9be02-248">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9be02-249">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="9be02-249">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9be02-250">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-250">Creating a new table.</span></span>
>* <span data-ttu-id="9be02-251">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-251">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9be02-252">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-252">Dropping the old table.</span></span>
>* <span data-ttu-id="9be02-253">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-253">Renaming the new table.</span></span>
>
><span data-ttu-id="9be02-254">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="9be02-254">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9be02-255">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="9be02-255">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9be02-256">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="9be02-256">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9be02-257">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="9be02-257">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9be02-258">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="9be02-258">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

1. <span data-ttu-id="9be02-259">Löschen Sie den Ordner „Migration“.</span><span class="sxs-lookup"><span data-stu-id="9be02-259">Delete the migration folder.</span></span>  

1. <span data-ttu-id="9be02-260">Verwenden Sie die folgenden Befehle, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-260">Use the following commands to recreate the database.</span></span>

   ```dotnetcli
   dotnet ef database drop
   dotnet ef migrations add InitialCreate
   dotnet ef database update
   ```

---

<span data-ttu-id="9be02-261">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="9be02-261">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9be02-262">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="9be02-262">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9be02-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9be02-263">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9be02-264">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9be02-264">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9be02-265">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9be02-265">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9be02-266">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="9be02-266">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="9be02-267">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9be02-267">Add a new field to the model.</span></span>
* <span data-ttu-id="9be02-268">Die neue Änderung am Feldschema wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="9be02-268">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="9be02-269">Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="9be02-269">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="9be02-270">Es fügt der Datenbank die Tabelle [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="9be02-270">Adds an [`__EFMigrationsHistory`](/ef/core/managing-schemas/migrations/history-table) table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="9be02-271">Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-271">If the model classes aren't in sync with the database, EF throws an exception.</span></span>

<span data-ttu-id="9be02-272">Durch die automatische Überprüfung, ob Schema und Modell synchron sind, können Probleme aufgrund inkonsistenter Datenbankencodes leichter ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="9be02-272">Automatic verification that the schema and model are in sync makes it easier to find inconsistent database code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="9be02-273">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="9be02-273">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="9be02-274">Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-274">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="9be02-275">Erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="9be02-275">Build the app.</span></span>

<span data-ttu-id="9be02-276">Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="9be02-276">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="9be02-277">Aktualisieren Sie die folgenden Seiten:</span><span class="sxs-lookup"><span data-stu-id="9be02-277">Update the following pages:</span></span>

* <span data-ttu-id="9be02-278">Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-278">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="9be02-279">Aktualisieren Sie die Datei [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.</span><span class="sxs-lookup"><span data-stu-id="9be02-279">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="9be02-280">Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-280">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="9be02-281">Die App funktioniert erst, nachdem die Datenbank so aktualisiert wurde, dass sie das neue Feld enthält.</span><span class="sxs-lookup"><span data-stu-id="9be02-281">The app won't work until the database is updated to include the new field.</span></span> <span data-ttu-id="9be02-282">Wenn die App jetzt ausgeführt wird, löst sie eine `SqlException` aus:</span><span class="sxs-lookup"><span data-stu-id="9be02-282">If the app is run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="9be02-283">Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="9be02-283">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="9be02-284">Die Datenbanktabelle enthält nicht die Spalte `Rating`.</span><span class="sxs-lookup"><span data-stu-id="9be02-284">There's no `Rating` column in the database table.</span></span>

<span data-ttu-id="9be02-285">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="9be02-285">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="9be02-286">Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-286">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="9be02-287">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="9be02-287">This approach is convenient early in the development cycle, it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="9be02-288">Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen.</span><span class="sxs-lookup"><span data-stu-id="9be02-288">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="9be02-289">Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="9be02-289">Don't use this approach on a production database!</span></span> <span data-ttu-id="9be02-290">Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.</span><span class="sxs-lookup"><span data-stu-id="9be02-290">Dropping the database on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="9be02-291">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="9be02-291">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="9be02-292">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="9be02-292">The advantage of this approach is to keep the data.</span></span> <span data-ttu-id="9be02-293">Sie können diese Änderung entweder manuell oder durch Erstellen eines Skripts zur Änderung der Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="9be02-293">Make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="9be02-294">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="9be02-294">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="9be02-295">Verwenden Sie für dieses Tutorial Code First-Migrationen.</span><span class="sxs-lookup"><span data-stu-id="9be02-295">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="9be02-296">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9be02-296">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="9be02-297">Eine Beispieländerung ist nachstehend gezeigt. Nehmen Sie diese Änderung jedoch für jeden `new Movie`-Block vor.</span><span class="sxs-lookup"><span data-stu-id="9be02-297">A sample change is shown below, but make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="9be02-298">Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span><span class="sxs-lookup"><span data-stu-id="9be02-298">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="9be02-299">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="9be02-299">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9be02-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9be02-300">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="9be02-301">Hinzufügen einer Migration für das Bewertungsfeld</span><span class="sxs-lookup"><span data-stu-id="9be02-301">Add a migration for the rating field</span></span>

<span data-ttu-id="9be02-302">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-302">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="9be02-303">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9be02-303">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="9be02-304">Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:</span><span class="sxs-lookup"><span data-stu-id="9be02-304">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="9be02-305">Das Modell `Movie` mit dem Schema der Datenbank `Movie` vergleichen.</span><span class="sxs-lookup"><span data-stu-id="9be02-305">Compare the `Movie` model with the `Movie` database schema.</span></span>
* <span data-ttu-id="9be02-306">Erstellen von Code, um das Schema der Datenbank in das neue Modell zu migrieren</span><span class="sxs-lookup"><span data-stu-id="9be02-306">Create code to migrate the database schema to the new model.</span></span>

<span data-ttu-id="9be02-307">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9be02-307">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="9be02-308">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9be02-308">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="9be02-309">Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="9be02-309">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="9be02-310">Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="9be02-310">If you delete all the records in the database, the initializer will seed the database and include the `Rating` field.</span></span> <span data-ttu-id="9be02-311">Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.</span><span class="sxs-lookup"><span data-stu-id="9be02-311">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="9be02-312">Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-312">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9be02-313">So löschen Sie die Datenbank in SSOX</span><span class="sxs-lookup"><span data-stu-id="9be02-313">To delete the database in SSOX:</span></span>

* <span data-ttu-id="9be02-314">Wählen Sie die Datenbank in SSOX aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-314">Select the database in SSOX.</span></span>
* <span data-ttu-id="9be02-315">Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Löschen** aus.</span><span class="sxs-lookup"><span data-stu-id="9be02-315">Right-click on the database, and select **Delete**.</span></span>
* <span data-ttu-id="9be02-316">Aktivieren Sie **Vorhandene Verbindungen schließen**.</span><span class="sxs-lookup"><span data-stu-id="9be02-316">Check **Close existing connections**.</span></span>
* <span data-ttu-id="9be02-317">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="9be02-317">Select **OK**.</span></span>
* <span data-ttu-id="9be02-318">Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):</span><span class="sxs-lookup"><span data-stu-id="9be02-318">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9be02-319">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9be02-319">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="9be02-320">Löschen und Neuerstellen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="9be02-320">Drop and re-create the database</span></span>

> [!NOTE]
> <span data-ttu-id="9be02-321">Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*.</span><span class="sxs-lookup"><span data-stu-id="9be02-321">For this tutorial you, use the Entity Framework Core *migrations* feature where possible.</span></span> <span data-ttu-id="9be02-322">Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="9be02-322">Migrations updates the database schema to match changes in the data model.</span></span> <span data-ttu-id="9be02-323">Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt.</span><span class="sxs-lookup"><span data-stu-id="9be02-323">However, migrations can only do the kinds of changes that the EF Core provider supports, and the SQLite provider's capabilities are limited.</span></span> <span data-ttu-id="9be02-324">So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9be02-324">For example, adding a column is supported, but removing or changing a column is not supported.</span></span> <span data-ttu-id="9be02-325">Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="9be02-325">If a migration is created to remove or change a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="9be02-326">Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen.</span><span class="sxs-lookup"><span data-stu-id="9be02-326">Due to these limitations, this tutorial doesn't use migrations for SQLite schema changes.</span></span> <span data-ttu-id="9be02-327">Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.</span><span class="sxs-lookup"><span data-stu-id="9be02-327">Instead, when the schema changes, you drop and re-create the database.</span></span>
>
><span data-ttu-id="9be02-328">Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert.</span><span class="sxs-lookup"><span data-stu-id="9be02-328">The workaround for the SQLite limitations is to manually write migrations code to perform a table rebuild when something in the table changes.</span></span> <span data-ttu-id="9be02-329">Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="9be02-329">A table rebuild involves:</span></span>
>
>* <span data-ttu-id="9be02-330">Erstellen einer neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-330">Creating a new table.</span></span>
>* <span data-ttu-id="9be02-331">Kopieren von Daten aus der alten Tabelle in die neue Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-331">Copying data from the old table to the new table.</span></span>
>* <span data-ttu-id="9be02-332">Löschen der alten Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-332">Dropping the old table.</span></span>
>* <span data-ttu-id="9be02-333">Umbenennen der neuen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9be02-333">Renaming the new table.</span></span>
>
><span data-ttu-id="9be02-334">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="9be02-334">For more information, see the following resources:</span></span>
>
> * [<span data-ttu-id="9be02-335">SQLite EF Core-Datenbank-Anbieter-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="9be02-335">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="9be02-336">Anpassen des Migrationscodes</span><span class="sxs-lookup"><span data-stu-id="9be02-336">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="9be02-337">Datenseeding</span><span class="sxs-lookup"><span data-stu-id="9be02-337">Data seeding</span></span>](/ef/core/modeling/data-seeding)
> * [<span data-ttu-id="9be02-338">SQLite ALTER TABLE-Anweisung</span><span class="sxs-lookup"><span data-stu-id="9be02-338">SQLite ALTER TABLE statement</span></span>](https://sqlite.org/lang_altertable.html)

<span data-ttu-id="9be02-339">Löschen Sie die Datenbank, und verwenden Sie Migrationen, um die Datenbank erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9be02-339">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="9be02-340">Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*).</span><span class="sxs-lookup"><span data-stu-id="9be02-340">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="9be02-341">Führen Sie dann den `ef database update`-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9be02-341">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="9be02-342">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="9be02-342">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="9be02-343">Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.</span><span class="sxs-lookup"><span data-stu-id="9be02-343">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9be02-344">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9be02-344">Additional resources</span></span>

* [<span data-ttu-id="9be02-345">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="9be02-345">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="9be02-346">[Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="9be02-346">[Previous: Add Search](xref:tutorials/razor-pages/search)
[Next: Add Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end