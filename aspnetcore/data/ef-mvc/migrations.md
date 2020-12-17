---
title: 'Teil 5 des Tutorials: Anwenden von Migrationen auf das Contoso University-Beispiel'
description: Teil 5 der Contoso University-Tutorialreihe Verwenden Sie das EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen in einer ASP.NET Core MVC-App.
author: rick-anderson
ms.author: riande
ms.custom: contperf-fy21q2
ms.date: 11/13/2020
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: 7c8f562bcf0b7e2672f2f1ac244e0d9278e4c204
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485926"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="0ad0f-104">Tutorial: Teil 5, Anwenden von Migrationen auf das Contoso University-Beispiel</span><span class="sxs-lookup"><span data-stu-id="0ad0f-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="0ad0f-105">In diesem Tutorial verwenden Sie zunächst die EF Core-Migrationsfeature für die Verwaltung von Datenmodelländerungen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="0ad0f-106">In späteren Tutorials fügen Sie weitere Migrationen hinzu, wenn Sie das Datenmodell ändern.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="0ad0f-107">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="0ad0f-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0ad0f-108">Erfahren Sie mehr über Migrationen</span><span class="sxs-lookup"><span data-stu-id="0ad0f-108">Learn about migrations</span></span>
> * <span data-ttu-id="0ad0f-109">Erstellen einer ursprünglichen Migration</span><span class="sxs-lookup"><span data-stu-id="0ad0f-109">Create an initial migration</span></span>
> * <span data-ttu-id="0ad0f-110">Untersuchen Sie Up- und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="0ad0f-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="0ad0f-111">Erfahren Sie mehr über die Datenmodell-Momentaufnahme</span><span class="sxs-lookup"><span data-stu-id="0ad0f-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="0ad0f-112">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="0ad0f-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0ad0f-113">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="0ad0f-113">Prerequisites</span></span>

* [<span data-ttu-id="0ad0f-114">Sortieren, Filtern und Auslagern</span><span class="sxs-lookup"><span data-stu-id="0ad0f-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="0ad0f-115">Informationen zu Migrationen</span><span class="sxs-lookup"><span data-stu-id="0ad0f-115">About migrations</span></span>

<span data-ttu-id="0ad0f-116">Wenn Sie eine neue Anwendung entwickeln, ändert sich Ihr Datenmodell häufig. Jedes Mal, wenn das Datenmodell geändert wird, ist es nicht mehr synchron mit der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="0ad0f-117">Sie haben zu Beginn dieser Tutorials Entity Framework für die Erstellung der Datenbank konfiguriert, sofern diese nicht vorhanden war.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="0ad0f-118">Anschließend können Sie bei jeder Datenmodelländerung (beim Hinzufügen, Entfernen oder Ändern von Entitätsklassen oder beim Ändern Ihrer DbContext-Klasse) die Datenbank löschen. EF erstellt dann eine neue Datenbank, die dem Modell entspricht, und startet diese mit Testdaten.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="0ad0f-119">Diese Methode, bei der die Datenbank mit dem Datenmodell synchron gehalten wird, funktioniert so lange, bis Sie die Anwendung für die Produktion bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="0ad0f-120">Wenn sich die Anwendung in der Produktion befindet, speichert sie in der Regel die Daten, die Sie weiter verwenden möchten, da nicht bei jeder Änderung, wie z.B. dem Hinzufügen einer neuen Spalte, alle Daten verloren gehen sollen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="0ad0f-121">Mit der EF Core-Migrationsfeature wird dieses Problem gelöst, indem EF das Datenbankschema aktualisiert, statt eine neue Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="0ad0f-122">Für die Arbeit mit Migrationen können Sie die **Paket-Manager-Konsole** (Package Manager Console, PMC) oder die CLI verwenden.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="0ad0f-123">In diesen Tutorials wird die Verwendungsweise von CLI-Befehlen erläutert.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="0ad0f-124">Informationen zur PMC finden Sie am [Ende dieses Tutorials](#pmc).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="0ad0f-125">Löschen der Datenbank</span><span class="sxs-lookup"><span data-stu-id="0ad0f-125">Drop the database</span></span>

<span data-ttu-id="0ad0f-126">Installieren Sie EF Core Tools als [globales Tool](/ef/core/miscellaneous/cli/dotnet), und löschen Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="0ad0f-126">Install EF Core tools as a [global tool](/ef/core/miscellaneous/cli/dotnet) and delete the database:</span></span>

 ```dotnetcli
 dotnet tool install --global dotnet-ef
 dotnet ef database drop
 ```

<span data-ttu-id="0ad0f-127">Im folgenden Abschnitt wird erläutert, wie CLI-Befehle ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-127">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="0ad0f-128">Erstellen einer ursprünglichen Migration</span><span class="sxs-lookup"><span data-stu-id="0ad0f-128">Create an initial migration</span></span>

<span data-ttu-id="0ad0f-129">Speichern Sie Ihre Änderungen, und erstellen Sie das Projekt.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-129">Save your changes and build the project.</span></span> <span data-ttu-id="0ad0f-130">Öffnen Sie anschließend ein Befehlsfenster, und navigieren Sie zu dem Projektordner.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-130">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="0ad0f-131">Sie können diesen Vorgang auf folgende Weise beschleunigen:</span><span class="sxs-lookup"><span data-stu-id="0ad0f-131">Here's a quick way to do that:</span></span>

* <span data-ttu-id="0ad0f-132">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie aus dem Kontextmenü die Option **Ordner in Datei-Explorer öffnen** aus.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-132">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Menüelement „In Datei-Explorer öffnen“](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="0ad0f-134">Geben Sie in die Adressleiste „cmd“ ein, und drücken Sie die EINGABETASTE.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-134">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Öffnen des Befehlsfensters](migrations/_static/open-command-window.png)

<span data-ttu-id="0ad0f-136">Geben Sie im Befehlsfenster folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="0ad0f-136">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="0ad0f-137">Mit den obigen Befehlen wird eine ähnliche Ausgabe wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0ad0f-137">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="0ad0f-138">Wenn Ihnen die Fehlermeldung *Auf die Datei „ContosoUniversity.dll“ kann nicht zugegriffen werden, da sie von einem anderen Prozess verwendet wird.* angezeigt wird, suchen Sie in der Windows-Taskleiste nach dem Symbol „IIS Express“, klicken Sie mit der rechten Maustaste darauf, und klicken Sie anschließend auf **ContosoUniversity > Website beenden**.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-138">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="0ad0f-139">Untersuchen Sie Up- und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="0ad0f-139">Examine Up and Down methods</span></span>

<span data-ttu-id="0ad0f-140">Wenn Sie den Befehl `migrations add` ausgeführt haben, hat EF den Code generiert, mit dem die Datenbank von Grund auf neu erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-140">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="0ad0f-141">Dieser Code befindet sich im Ordner *Migrationen* in der Datei *\<timestamp>_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-141">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="0ad0f-142">Mit der Methode `Up` der `InitialCreate`-Klasse werden die Datenbanktabellen erstellt, die den Datenmodellentitäten entsprechen, und mit der Methode `Down` werden die Datenbanktabellen gelöscht (siehe folgendes Beispiel).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-142">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="0ad0f-143">Die Migrationsfunktion ruft die Methode `Up` auf, um die Datenmodelländerungen für eine Migration zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-143">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="0ad0f-144">Wenn Sie einen Befehl eingeben, um ein Rollback für das Update auszuführen, ruft die Migrationsfunktion die Methode `Down` auf.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-144">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="0ad0f-145">Dieser Code ist für die ursprüngliche Migration vorgesehen, die bei der Eingabe des Befehls `migrations add InitialCreate` erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-145">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="0ad0f-146">Der Parameter für den Migrationsnamen (in dem Beispiel „InitialCreate“) wird für den Dateinamen verwendet und kann einen beliebigen Namen haben.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-146">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="0ad0f-147">Es wird empfohlen, ein Wort oder einen Ausdruck auszuwählen, durch das bzw. den der Hintergrund der Migration widergespiegelt wird.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-147">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="0ad0f-148">Sie können einer späteren Migration beispielsweise den Namen „AddDepartmentTable“ geben.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-148">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="0ad0f-149">Wenn Sie die ursprüngliche Migration erstellt haben, als die Datenbank bereits vorhanden war, wird der Code für die Datenbankerstellung zwar generiert, allerdings muss er nicht ausgeführt werden, da die Datenbank bereits dem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-149">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="0ad0f-150">Wenn Sie die App in einer anderen Umgebung bereitstellen, in der die Datenbank noch nicht vorhanden ist, wird dieser Code ausgeführt, um Ihre Datenbank zu erstellen. Daher sollte dieser zunächst getestet werden.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-150">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="0ad0f-151">Aus diesem Grund haben Sie den Namen der Datenbank zuvor in der Verbindungszeichenfolge geändert – damit eine Datenbank bei Migrationen von Grund auf neu erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-151">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="0ad0f-152">Die Momentaufnahme des Datenmodells</span><span class="sxs-lookup"><span data-stu-id="0ad0f-152">The data model snapshot</span></span>

<span data-ttu-id="0ad0f-153">Die Migrationsfunktion erstellt unter *Migrations/SchoolContextModelSnapshot.cs* eine *Momentaufnahme* des aktuellen Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-153">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="0ad0f-154">Wenn Sie eine Migration hinzufügen, bestimmt EF die vorgenommenen Änderungen, indem das Datenmodell mit der Momentaufnahmedatei verglichen wird.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-154">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="0ad0f-155">Verwenden Sie den Befehl [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove), um eine Migration zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-155">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="0ad0f-156">`dotnet ef migrations remove` löscht die Migration und stellt sicher, dass die Momentaufnahme ordnungsgemäß zurückgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-156">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="0ad0f-157">Wenn `dotnet ef migrations remove` fehlschlägt, verwenden Sie `dotnet ef migrations remove -v`, um weitere Informationen zu diesem Fehler zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-157">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="0ad0f-158">Weitere Informationen dazu, wie die Momentaufnahmedatei verwendet wird, finden Sie unter [EF Core Migrations in Team Environments (EF Core-Migrationen in Teamumgebungen)](/ef/core/managing-schemas/migrations/teams).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-158">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="0ad0f-159">Anwenden der Migration</span><span class="sxs-lookup"><span data-stu-id="0ad0f-159">Apply the migration</span></span>

<span data-ttu-id="0ad0f-160">Geben Sie folgenden Befehl in das Befehlsfenster ein, um die Datenbank mit den darin enthaltenen Tabellen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-160">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="0ad0f-161">Die Ausgabe des Befehls ähnelt der des Befehls `migrations add`. Der Unterschied besteht darin, dass Ihnen Protokolle für die SQL-Befehle angezeigt werden, mit denen die Datenbank eingerichtet wird.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-161">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="0ad0f-162">In der folgenden Beispielausgabe werden die meisten Protokolle ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-162">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="0ad0f-163">Wenn diese Detailebene in Protokollnachrichten nicht angezeigt werden soll, können Sie die Protokollebene in der Datei *appsettings.Development.json* ändern.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-163">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="0ad0f-164">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-164">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

<span data-ttu-id="0ad0f-165">Verwenden Sie den **SQL Server-Objekt-Explorer**, um die Datenbank wie im ersten Tutorial zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-165">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="0ad0f-166">Sie werden feststellen, dass die Tabelle „\_\_EFMigrationsHistory“ hinzugefügt wurde, in der nachverfolgt wird, welche Migrationen auf die Datenbank angewendet worden sind.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-166">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="0ad0f-167">Wenn Sie die Daten in dieser Tabelle anzeigen, ist eine Zeile für die erste Migration zu sehen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-167">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="0ad0f-168">(Im letzten Protokoll im vorgehenden Beispiel der CLI-Ausgabe wird die INSERT-Anweisung angezeigt, mit der diese Zeile erstellt wird.)</span><span class="sxs-lookup"><span data-stu-id="0ad0f-168">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="0ad0f-169">Führen Sie die Anwendung aus, um zu überprüfen, ob alles wie gewohnt funktioniert.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-169">Run the application to verify that everything still works the same as before.</span></span>

![Indexseite „Studenten“](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="0ad0f-171">Vergleich von CLI und PMC</span><span class="sxs-lookup"><span data-stu-id="0ad0f-171">Compare CLI and PMC</span></span>

<span data-ttu-id="0ad0f-172">Die EF-Tools für die Verwaltung von Migrationen sind über .NET Core-CLI-Befehle oder über PowerShell-Cmdlets im Visual Studio-Fenster **Paket-Manager-Console** (PMC) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-172">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="0ad0f-173">In diesem Tutorial wird die Verwendungsweise der CLI erläutert. Sie können aber auch die PMC verwenden, wenn Sie diese bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-173">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="0ad0f-174">Die EF-Befehle für die PMC-Befehle sind im Paket [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) enthalten.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-174">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="0ad0f-175">Dieses Paket ist im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten, weshalb Sie keinen Paketverweis hinzufügen müssen, wenn Ihre App über einen Paketverweis für `Microsoft.AspNetCore.App` verfügt.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-175">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="0ad0f-176">**Wichtig:** Dieses Paket ist nicht mit dem Paket identisch, das Sie für die CLI durch Bearbeitung der *CSPROJ*-Datei installiert haben.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-176">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="0ad0f-177">Der Name dieses Pakets endet mit `Tools`, im Gegensatz zum Namen des CLI-Pakets, der mit `Tools.DotNet` endet.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-177">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="0ad0f-178">Weitere Informationen zu CLI-Befehlen finden Sie unter [.NET Core-CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-178">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="0ad0f-179">Weitere Informationen zu den PMC-Befehlen finden Sie unter [Paket-Manager-Konsole (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-179">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="0ad0f-180">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="0ad0f-180">Get the code</span></span>

[<span data-ttu-id="0ad0f-181">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="0ad0f-181">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="0ad0f-182">Nächster Schritt</span><span class="sxs-lookup"><span data-stu-id="0ad0f-182">Next step</span></span>

<span data-ttu-id="0ad0f-183">Fahren Sie mit dem nächsten Tutorial fort, um fortgeschrittenere Themen zum Erweitern des Datenmodells kennenzulernen.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-183">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="0ad0f-184">Dabei werden Sie weitere Migrationen erstellen und anwenden.</span><span class="sxs-lookup"><span data-stu-id="0ad0f-184">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="0ad0f-185">ASP.NET Core MVC mit Entity Framework Core: Datenmodell (5 von 10)</span><span class="sxs-lookup"><span data-stu-id="0ad0f-185">Create and apply additional migrations</span></span>](complex-data-model.md)
