---
title: 'Teil 4: Razor Pages mit EF Core in ASP.NET Core – Migrationen'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
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
uid: data/ef-rp/migrations
ms.openlocfilehash: e6d1b9f041e892aaa37840c28fdb3153bf098b0d
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061105"
---
# <a name="part-4-no-locrazor-pages-with-ef-core-migrations-in-aspnet-core"></a><span data-ttu-id="64f67-103">Teil 4: Razor Pages mit EF Core-Migrationen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64f67-103">Part 4, Razor Pages with EF Core migrations in ASP.NET Core</span></span>

<span data-ttu-id="64f67-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64f67-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64f67-105">In diesem Tutorial wird das EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="64f67-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="64f67-106">Wenn eine neue App entwickelt wird, ändert sich das Datenmodell häufig.</span><span class="sxs-lookup"><span data-stu-id="64f67-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="64f67-107">Nach jeder Modelländerung ist das Modell nicht mehr synchron mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="64f67-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="64f67-108">In dieser Tutorialreihe wurde zunächst Entity Framework für die Erstellung der Datenbank konfiguriert, falls diese nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="64f67-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="64f67-109">Jedes Mal, wenn sich das Datenmodell ändert, müssen Sie die Datenbank löschen.</span><span class="sxs-lookup"><span data-stu-id="64f67-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="64f67-110">Wenn die App das nächste Mal ausgeführt wird, wird durch den Aufruf von `EnsureCreated` die Datenbank erneut entsprechend dem neuen Datenmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="64f67-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="64f67-111">Die `DbInitializer`-Klasse wird dann ausgeführt, um das Seeding der neuen Datenbank durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="64f67-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="64f67-112">Dieser Ansatz, bei dem die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert gut, bis Sie die App für die Produktion bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="64f67-113">Wenn die App in der Produktionsumgebung ausgeführt wird, speichert sie in der Regel Daten, die verwaltet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="64f67-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="64f67-114">Wenn eine Änderung vorgenommen wird (z.B. eine neue Spalte hinzugefügt wird), kann die App nicht jedes Mal mit einer Testdatenbank starten.</span><span class="sxs-lookup"><span data-stu-id="64f67-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="64f67-115">Mit dem EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF Core das Datenbankschema aktualisiert, anstatt eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="64f67-116">Anstatt die Datenbank bei Änderungen des Datenmodell zu löschen und neu zu erstellen, erfolgt bei der Migration eine Aktualisierung des Schemas und die Beibehaltung vorhandener Daten.</span><span class="sxs-lookup"><span data-stu-id="64f67-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="64f67-117">Löschen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="64f67-117">Drop the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64f67-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64f67-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64f67-119">Verwenden Sie **SQL Server-Objekt-Explorer** (SSOX), um die Datenbank zu löschen, oder führen Sie den folgenden Befehl in der **Paket-Manager-Konsole** (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="64f67-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64f67-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="64f67-121">Führen Sie in der Eingabeaufforderung den folgenden Befehl aus, um die EF CLI zu installieren:</span><span class="sxs-lookup"><span data-stu-id="64f67-121">Run the following command at a command prompt to install the EF CLI:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="64f67-122">Navigieren Sie in der Eingabeaufforderung zum Projektordner.</span><span class="sxs-lookup"><span data-stu-id="64f67-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="64f67-123">Der Projektordner enthält die Datei *ContosoUniversity.csproj*.</span><span class="sxs-lookup"><span data-stu-id="64f67-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="64f67-124">Löschen Sie die Datei *CU.db*, oder führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="64f67-125">Erstellen einer ursprünglichen Migration</span><span class="sxs-lookup"><span data-stu-id="64f67-125">Create an initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64f67-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64f67-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64f67-127">Führen Sie die folgenden Befehle in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="64f67-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64f67-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64f67-129">Stellen Sie sicher, dass sich die Eingabeaufforderung im Projektordner befindet, und führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="64f67-130">Up- und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="64f67-130">Up and Down methods</span></span>

<span data-ttu-id="64f67-131">Der EF Core-Befehl `migrations add` hat Code generiert, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="64f67-132">Dieser Migrationscode ist in der Datei *Migrations\<timestamp>_InitialCreate.cs* enthalten.</span><span class="sxs-lookup"><span data-stu-id="64f67-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="64f67-133">Die Methode `Up` der Klasse `InitialCreate` erstellt die Datenbanktabellen, die den Datenmodell-Entitätenmengen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="64f67-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="64f67-134">Die Methode `Down` löscht diese, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="64f67-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="64f67-135">Der vorangehende Code ist für die ursprüngliche Migration bestimmt.</span><span class="sxs-lookup"><span data-stu-id="64f67-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="64f67-136">Der Code:</span><span class="sxs-lookup"><span data-stu-id="64f67-136">The code:</span></span>

* <span data-ttu-id="64f67-137">Wurde durch den `migrations add InitialCreate`-Befehl generiert.</span><span class="sxs-lookup"><span data-stu-id="64f67-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="64f67-138">Wird durch den `database update`-Befehl ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="64f67-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="64f67-139">Erstellt eine Datenbank für das Datenmodell, das durch die Datenbankkontextklasse angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="64f67-140">Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="64f67-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="64f67-141">Der Migrationsname kann ein beliebiger gültiger Dateiname sein.</span><span class="sxs-lookup"><span data-stu-id="64f67-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="64f67-142">Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="64f67-143">Eine Migration, bei der eine Tabelle mit Fachbereichen hinzugefügt wurde, könnte beispielsweise als „AddDepartmentTable“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="64f67-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="64f67-144">Die Migrationsverlaufstabelle</span><span class="sxs-lookup"><span data-stu-id="64f67-144">The migrations history table</span></span>

* <span data-ttu-id="64f67-145">Verwenden Sie SSOX oder das SQLite-Tool, um die Datenbank zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="64f67-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="64f67-146">Beachten Sie die zusätzliche Tabelle`__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="64f67-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="64f67-147">In der Tabelle `__EFMigrationsHistory` wird nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="64f67-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="64f67-148">Zeigen Sie die Daten in der Tabelle `__EFMigrationsHistory` an.</span><span class="sxs-lookup"><span data-stu-id="64f67-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="64f67-149">Es wird eine Zeile für die erste Migration angezeigt.</span><span class="sxs-lookup"><span data-stu-id="64f67-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="64f67-150">Die Momentaufnahme des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="64f67-150">The data model snapshot</span></span>

<span data-ttu-id="64f67-151">Bei der Migration wird in *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenmodells erstellt.</span><span class="sxs-lookup"><span data-stu-id="64f67-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="64f67-152">Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das aktuelle Datenmodell mit der Momentaufnahmedatei verglichen wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="64f67-153">Da die Momentaufnahmedatei den Status des Datenmodells nachverfolgt, können Sie eine Migration nicht löschen, indem Sie die Datei `<timestamp>_<migrationname>.cs` löschen.</span><span class="sxs-lookup"><span data-stu-id="64f67-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="64f67-154">Zum Zurücksetzen der neuesten Migration müssen Sie den Befehl `migrations remove` verwenden.</span><span class="sxs-lookup"><span data-stu-id="64f67-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="64f67-155">Dieser Befehl löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="64f67-156">Weitere Informationen finden Sie unter [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="64f67-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="64f67-157">Entfernen von EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="64f67-157">Remove EnsureCreated</span></span>

<span data-ttu-id="64f67-158">Diese Tutorialreihe hat mit der Verwendung von `EnsureCreated` begonnen.</span><span class="sxs-lookup"><span data-stu-id="64f67-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="64f67-159">`EnsureCreated` erstellt keine Migrationsverlaufstabelle und kann daher nicht mit Migrationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="64f67-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="64f67-160">EnsureCreated dient zum Testen oder schnellen Erstellen von Prototypen, wenn die Datenbank häufig gelöscht und neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="64f67-161">Ab diesem Zeitpunkt werden die Tutorials Migrationen verwenden.</span><span class="sxs-lookup"><span data-stu-id="64f67-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="64f67-162">Kommentieren Sie in *Data/DBInitializer.cs* die folgende Zeile aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="64f67-163">Führen Sie die App aus, und stellen Sie sicher, dass für die Datenbank ein Seeding durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="64f67-164">Anwenden von Migrationen in der Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="64f67-164">Applying migrations in production</span></span>

<span data-ttu-id="64f67-165">Es wird empfohlen, dass Produktions-Apps [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart **nicht** aufrufen.</span><span class="sxs-lookup"><span data-stu-id="64f67-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="64f67-166">`Migrate` sollte nicht aus einer APP aufgerufen werden, die in einer Serverfarm bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="64f67-167">Wenn die App auf mehrere Serverinstanzen horizontal hochskaliert wird, ist es schwierig sicherzustellen, dass Datenbankschemaaktualisierungen nicht von mehreren Servern erfolgen oder mit Lese-/Schreibzugriffen einen Konflikt verursachen.</span><span class="sxs-lookup"><span data-stu-id="64f67-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="64f67-168">Die Datenbankmigration sollte im Rahmen der Bereitstellung und auf kontrollierte Weise erfolgen.</span><span class="sxs-lookup"><span data-stu-id="64f67-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="64f67-169">Zu den Ansätzen für die Migration von Produktionsdatenbanken zählen die folgenden:</span><span class="sxs-lookup"><span data-stu-id="64f67-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="64f67-170">Verwendung von Migrationen zur Erstellung von SQL-Skripts und Verwendung der SQL-Skripts bei der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="64f67-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="64f67-171">Ausführen von `dotnet ef database update` über eine kontrollierte Umgebung.</span><span class="sxs-lookup"><span data-stu-id="64f67-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="64f67-172">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="64f67-172">Troubleshooting</span></span>

<span data-ttu-id="64f67-173">Wenn die App SQL Server LocalDB verwendet und die folgende Ausnahme angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="64f67-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="64f67-174">Die Lösung kann die Ausführung von `dotnet ef database update` an einer Eingabeaufforderung sein.</span><span class="sxs-lookup"><span data-stu-id="64f67-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="64f67-175">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="64f67-175">Additional resources</span></span>

* <span data-ttu-id="64f67-176">[EF Core-CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="64f67-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="64f67-177">Paket-Manager-Konsole (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="64f67-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="64f67-178">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="64f67-178">Next steps</span></span>

<span data-ttu-id="64f67-179">Im nächsten Tutorial wird das Datenmodell erstellt, und es werden Entitätseigenschaften und neue Entitäten hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="64f67-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="64f67-180">[Vorheriges Tutorial](xref:data/ef-rp/sort-filter-page)
> [Nächstes Tutorial](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="64f67-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="64f67-181">In diesem Tutorial wird das EF Core-Migrationsfeature zur Verwaltung von Datenmodelländerungen verwendet.</span><span class="sxs-lookup"><span data-stu-id="64f67-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="64f67-182">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter.</span><span class="sxs-lookup"><span data-stu-id="64f67-182">If you run into problems you can't solve, download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="64f67-183">Wenn eine neue App entwickelt wird, ändert sich das Datenmodell häufig.</span><span class="sxs-lookup"><span data-stu-id="64f67-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="64f67-184">Nach jeder Modelländerung ist das Modell nicht mehr synchron mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="64f67-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="64f67-185">In diesem Tutorial wurde zunächst Entity Framework für die Erstellung der Datenbank konfiguriert, falls diese nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="64f67-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="64f67-186">Bei jeder Datenmodelländerung:</span><span class="sxs-lookup"><span data-stu-id="64f67-186">Each time the data model changes:</span></span>

* <span data-ttu-id="64f67-187">Wird die Datenbank gelöscht.</span><span class="sxs-lookup"><span data-stu-id="64f67-187">The DB is dropped.</span></span>
* <span data-ttu-id="64f67-188">Erstellt EF eine neue, dem Modell entsprechende Datenbank.</span><span class="sxs-lookup"><span data-stu-id="64f67-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="64f67-189">Füllt die App die Datenbank mit Testdaten auf.</span><span class="sxs-lookup"><span data-stu-id="64f67-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="64f67-190">Dieser Ansatz, bei dem die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die App für die Produktion bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="64f67-191">Wenn die App in der Produktionsumgebung ausgeführt wird, speichert sie in der Regel Daten, die verwaltet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="64f67-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="64f67-192">Jedes Mal, wenn eine Änderung vorgenommen wird (z.B. wenn eine neue Spalte hinzugefügt wird), kann die App nicht mit einer Testdatenbank starten.</span><span class="sxs-lookup"><span data-stu-id="64f67-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="64f67-193">Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF Core das Datenbankschema aktualisiert, anstatt eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="64f67-194">Statt die Datenbank bei den Datenmodelländerungen zu löschen und neu zu erstellen, wird bei der Migration eine Aktualisierung des Schemas und eine Beibehaltung vorhandener Daten angestrebt.</span><span class="sxs-lookup"><span data-stu-id="64f67-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="64f67-195">Löschen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="64f67-195">Drop the database</span></span>

<span data-ttu-id="64f67-196">Verwenden Sie den **SQL Server-Objekt-Explorer** (SSOX) oder den Befehl `database drop`:</span><span class="sxs-lookup"><span data-stu-id="64f67-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64f67-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64f67-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64f67-198">Führen Sie folgenden Befehl in der **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="64f67-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```powershell
Drop-Database
```

<span data-ttu-id="64f67-199">Führen Sie `Get-Help about_EntityFrameworkCore` über die Paket-Manager-Konsole aus, um Hilfeinformationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="64f67-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64f67-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64f67-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="64f67-201">Öffnen Sie ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="64f67-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="64f67-202">Der Projektordner enthält die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="64f67-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="64f67-203">Geben Sie im Befehlsfenster Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="64f67-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="64f67-204">Erstellen einer ursprünglichen Migration und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="64f67-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="64f67-205">Erstellen Sie das Projekt und die erste Migration.</span><span class="sxs-lookup"><span data-stu-id="64f67-205">Build the project and create the first migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64f67-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64f67-206">Visual Studio</span></span>](#tab/visual-studio)

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="64f67-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64f67-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="64f67-208">Überprüfen der Methoden „Up“ und „Down“</span><span class="sxs-lookup"><span data-stu-id="64f67-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="64f67-209">Der EF Core-Befehl `migrations add` hat Code generiert, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="64f67-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="64f67-210">Dieser Migrationscode ist in der Datei *Migrations\<timestamp>_InitialCreate.cs* enthalten.</span><span class="sxs-lookup"><span data-stu-id="64f67-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="64f67-211">Die Methode `Up` der Klasse `InitialCreate` erstellt die Datenbanktabellen, die den Datenmodellentitätenmengen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="64f67-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="64f67-212">Die Methode `Down` löscht diese, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="64f67-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="64f67-213">Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="64f67-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="64f67-214">Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.</span><span class="sxs-lookup"><span data-stu-id="64f67-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="64f67-215">Der vorangehende Code ist für die ursprüngliche Migration bestimmt.</span><span class="sxs-lookup"><span data-stu-id="64f67-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="64f67-216">Dieser Code wurde bei der Ausführung des Befehls `migrations add InitialCreate` erstellt.</span><span class="sxs-lookup"><span data-stu-id="64f67-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="64f67-217">Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet.</span><span class="sxs-lookup"><span data-stu-id="64f67-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="64f67-218">Der Migrationsname kann ein beliebiger gültiger Dateiname sein.</span><span class="sxs-lookup"><span data-stu-id="64f67-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="64f67-219">Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="64f67-220">Eine Migration, bei der eine Tabelle mit Fachbereichen hinzugefügt wurde, könnte beispielsweise als „AddDepartmentTable“ bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="64f67-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="64f67-221">Wenn die ursprüngliche Migration erstellt wird und die Datenbank vorhanden ist:</span><span class="sxs-lookup"><span data-stu-id="64f67-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="64f67-222">Wird der Code für die Datenbankerstellung generiert.</span><span class="sxs-lookup"><span data-stu-id="64f67-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="64f67-223">Muss der Code für die Datenbankerstellung nicht ausgeführt werden, da die Datenbank bereits mit dem Datenmodell übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="64f67-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="64f67-224">Wenn der Code für die Datenbankerstellung ausgeführt wird, werden keine Änderungen vorgenommen, da die Datenbank bereits mit dem Datenmodell übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="64f67-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="64f67-225">Wenn die App in einer neuen Umgebung bereitgestellt wird, muss der Code für die Datenbankerstellung ausgeführt werden, damit die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="64f67-226">Die Datenbank wurde zuvor gelöscht und ist daher nicht vorhanden, darum erstellt die Migration eine neue Datenbank.</span><span class="sxs-lookup"><span data-stu-id="64f67-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="64f67-227">Die Momentaufnahme des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="64f67-227">The data model snapshot</span></span>

<span data-ttu-id="64f67-228">Die Migration erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="64f67-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="64f67-229">Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="64f67-230">Verwenden Sie folgenden Befehl, um eine Migration zu löschen:</span><span class="sxs-lookup"><span data-stu-id="64f67-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64f67-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64f67-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64f67-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="64f67-232">Remove-Migration</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="64f67-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="64f67-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="64f67-234">Weitere Informationen finden Sie unter [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span><span class="sxs-lookup"><span data-stu-id="64f67-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="64f67-235">Der Befehl „Remove-Migration“ löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="64f67-236">Entfernen von EnsureCreated und Testen der App</span><span class="sxs-lookup"><span data-stu-id="64f67-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="64f67-237">Zu einem frühen Entwicklungszeitpunkt wurde `EnsureCreated` verwendet.</span><span class="sxs-lookup"><span data-stu-id="64f67-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="64f67-238">In diesem Tutorial werden Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="64f67-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="64f67-239">Der Befehl `EnsureCreated` weist folgende Einschränkungen auf:</span><span class="sxs-lookup"><span data-stu-id="64f67-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="64f67-240">Er umgeht Migrationen und erstellt die Datenbank und das Schema.</span><span class="sxs-lookup"><span data-stu-id="64f67-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="64f67-241">Er erstellt keine Migrationstabelle.</span><span class="sxs-lookup"><span data-stu-id="64f67-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="64f67-242">Er kann *nicht* mit Migrationen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="64f67-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="64f67-243">Er dient zum Testen oder schnellen Erstellen von Prototypen an Positionen, an denen die Datenbank häufig gelöscht und neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="64f67-244">Entfernen Sie `EnsureCreated`:</span><span class="sxs-lookup"><span data-stu-id="64f67-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="64f67-245">Führen Sie die App aus, und stellen Sie sicher, dass für die Datenbank ein Seeding durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="64f67-246">Untersuchen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="64f67-246">Inspect the database</span></span>

<span data-ttu-id="64f67-247">Verwenden Sie den **SQL Server-Objekt-Explorer** zur Untersuchung der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="64f67-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="64f67-248">Beachten Sie die zusätzliche Tabelle`__EFMigrationsHistory`.</span><span class="sxs-lookup"><span data-stu-id="64f67-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="64f67-249">In der Tabelle `__EFMigrationsHistory` wird nachverfolgt, welche Migrationen auf die Datenbank angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="64f67-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="64f67-250">Wenn Sie die Daten in der Tabelle `__EFMigrationsHistory` anzeigen, wird eine Zeile für die erste Migration angezeigt.</span><span class="sxs-lookup"><span data-stu-id="64f67-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="64f67-251">Im letzten Protokoll im vorherigen Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="64f67-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="64f67-252">Führen Sie die App aus, und überprüfen Sie, ob alles funktioniert.</span><span class="sxs-lookup"><span data-stu-id="64f67-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="64f67-253">Anwenden von Migrationen in der Produktionsumgebung</span><span class="sxs-lookup"><span data-stu-id="64f67-253">Applying migrations in production</span></span>

<span data-ttu-id="64f67-254">Es wird empfohlen, dass Produktions-Apps [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) beim Anwendungsstart **nicht** aufrufen.</span><span class="sxs-lookup"><span data-stu-id="64f67-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="64f67-255">`Migrate` sollte in der Serverfarm nicht über eine App aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="64f67-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="64f67-256">Beispielsweise wenn die App über eine Cloud mit horizontaler Skalierung bereitgestellt wurde (mehrere Instanzen der App werden ausgeführt).</span><span class="sxs-lookup"><span data-stu-id="64f67-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="64f67-257">Die Datenbankmigration sollte im Rahmen der Bereitstellung und auf kontrollierte Weise erfolgen.</span><span class="sxs-lookup"><span data-stu-id="64f67-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="64f67-258">Zu den Ansätzen für die Migration von Produktionsdatenbanken zählen die folgenden:</span><span class="sxs-lookup"><span data-stu-id="64f67-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="64f67-259">Verwendung von Migrationen zur Erstellung von SQL-Skripts und Verwendung der SQL-Skripts bei der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="64f67-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="64f67-260">Ausführen von `dotnet ef database update` über eine kontrollierte Umgebung.</span><span class="sxs-lookup"><span data-stu-id="64f67-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="64f67-261">EF Core kann über die Tabelle `__MigrationsHistory` sehen, ob Migrationen ausgeführt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="64f67-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="64f67-262">Wenn die Datenbank auf dem neuesten Stand ist, wird keine Migration ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="64f67-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="64f67-263">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="64f67-263">Troubleshooting</span></span>

<span data-ttu-id="64f67-264">Laden Sie die [fertige App](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations) herunter.</span><span class="sxs-lookup"><span data-stu-id="64f67-264">Download the [completed app](
https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="64f67-265">Die App generiert folgende Ausnahme:</span><span class="sxs-lookup"><span data-stu-id="64f67-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="64f67-266">Projektmappe: Ausführen von `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="64f67-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="64f67-267">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="64f67-267">Additional resources</span></span>

* [<span data-ttu-id="64f67-268">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="64f67-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="64f67-269">[.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet)</span><span class="sxs-lookup"><span data-stu-id="64f67-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="64f67-270">Paket-Manager-Konsole (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="64f67-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="64f67-271">[Zurück](xref:data/ef-rp/sort-filter-page)
> [Weiter](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="64f67-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

