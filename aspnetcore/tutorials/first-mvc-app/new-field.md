---
title: 'Teil 8: Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: 84e344aabc9171020c0117d55eaf1a95e6b768db
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422534"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="befcc-103">Teil 8: Hinzufügen eines neuen Felds zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="befcc-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="befcc-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="befcc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="befcc-105">In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:</span><span class="sxs-lookup"><span data-stu-id="befcc-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="befcc-106">Dem Modell wird ein neues Feld hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="befcc-106">Add a new field to the model.</span></span>
* <span data-ttu-id="befcc-107">Das neue Feld wird in die Datenbank migriert.</span><span class="sxs-lookup"><span data-stu-id="befcc-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="befcc-108">Wird EF Code First verwendet, um eine Datenbank automatisch zu erstellen, geht Code First wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="befcc-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="befcc-109">Es fügt der Datenbank eine Tabelle hinzu, um das Schema der Datenbank nachzuverfolgen.</span><span class="sxs-lookup"><span data-stu-id="befcc-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="befcc-110">Es überprüft, ob die Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="befcc-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="befcc-111">Wenn sie nicht synchron sind, löst EF eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="befcc-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="befcc-112">Dies erleichtert die Suche nach Problemen mit inkonsistenten Datenbanken bzw. inkonsistentem Code.</span><span class="sxs-lookup"><span data-stu-id="befcc-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="befcc-113">Hinzufügen einer Rating-Eigenschaft zum Movie-Modell</span><span class="sxs-lookup"><span data-stu-id="befcc-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="befcc-114">Fügen Sie eine `Rating`-Eigenschaft zu *Models/Movie.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="befcc-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?name=snippet)]

<span data-ttu-id="befcc-115">Erstellen der App</span><span class="sxs-lookup"><span data-stu-id="befcc-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="befcc-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="befcc-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="befcc-117">Strg+Umschalt+B</span><span class="sxs-lookup"><span data-stu-id="befcc-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="befcc-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="befcc-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="befcc-119">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="befcc-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="befcc-120">Befehl ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="befcc-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="befcc-121">Da Sie der `Movie`-Klasse ein neues Feld hinzugefügt haben, müssen Sie die Eigenschaftenbindungsliste aktualisieren, damit diese neue Eigenschaft eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="befcc-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="befcc-122">Aktualisieren Sie in *MoviesController.cs* das `[Bind]`-Attribut für die Aktionsmethoden `Create` und `Edit` so, dass die `Rating`-Eigenschaft eingeschlossen wird:</span><span class="sxs-lookup"><span data-stu-id="befcc-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="befcc-123">Aktualisieren Sie die Ansichtsvorlagen, um die neue `Rating`-Eigenschaft in der Browseransicht anzuzeigen, zu erstellen und zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="befcc-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="befcc-124">Bearbeiten Sie die Datei */Views/Movies/Index.cshtml* , und fügen Sie das Feld `Rating` hinzu:</span><span class="sxs-lookup"><span data-stu-id="befcc-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Movies/Index.cshtml?highlight=28-49&range=12-51)]

::: moniker-end

<span data-ttu-id="befcc-125">Aktualisieren Sie die Datei */Views/Movies/Create.cshtml* mit dem Feld `Rating`.</span><span class="sxs-lookup"><span data-stu-id="befcc-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="befcc-126">Visual Studio / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="befcc-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="befcc-127">Sie können die vorherige „Formulargruppe“ kopieren und einfügen und IntelliSense die Felder aktualisieren lassen.</span><span class="sxs-lookup"><span data-stu-id="befcc-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="befcc-128">IntelliSense nutzt [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="befcc-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![Der Entwickler hat den Buchstaben R als Attributwert von „asp-for“ im zweiten „label“-Element der Ansicht eingegeben.](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="befcc-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="befcc-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="befcc-133">Aktualisieren Sie die übrigen Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="befcc-133">Update the remaining templates.</span></span>

<span data-ttu-id="befcc-134">Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="befcc-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="befcc-135">Eine Beispieländerung wird nachstehend gezeigt, aber Sie sollten diese Änderung für jedes `new Movie`-Element vornehmen.</span><span class="sxs-lookup"><span data-stu-id="befcc-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="befcc-136">Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="befcc-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="befcc-137">Wird sie jetzt ausgeführt, wird die folgende `SqlException` ausgelöst:</span><span class="sxs-lookup"><span data-stu-id="befcc-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="befcc-138">Dieser Fehler tritt auf, weil die aktualisierte Modellklasse „Movie“ sich vom Schema der Tabelle „Movie“ der vorhandenen Datenbank unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="befcc-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="befcc-139">(Die Datenbanktabelle enthält nicht die Spalte `Rating`.)</span><span class="sxs-lookup"><span data-stu-id="befcc-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="befcc-140">Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:</span><span class="sxs-lookup"><span data-stu-id="befcc-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="befcc-141">Lassen Sie die Datenbank von Entity Framework automatisch löschen und basierend auf dem neuen Modellklassenschema neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="befcc-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="befcc-142">Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch, wenn die aktive Entwicklung in einer Testdatenbank erfolgt. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell weiterzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="befcc-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="befcc-143">Der Nachteil ist jedoch, dass in der Datenbank vorhandene Daten verloren gehen. Deshalb eignet sich dieser Ansatz nicht für eine Produktionsdatenbank!</span><span class="sxs-lookup"><span data-stu-id="befcc-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="befcc-144">Das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="befcc-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="befcc-145">Dies ist ein guter Ansatz für die frühe Entwicklung und wenn SQLite verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="befcc-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="befcc-146">Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="befcc-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="befcc-147">Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten.</span><span class="sxs-lookup"><span data-stu-id="befcc-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="befcc-148">Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.</span><span class="sxs-lookup"><span data-stu-id="befcc-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="befcc-149">Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="befcc-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="befcc-150">Für dieses Tutorial wird Code First-Migrationen verwendet.</span><span class="sxs-lookup"><span data-stu-id="befcc-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="befcc-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="befcc-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="befcc-152">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="befcc-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC-Menü](adding-model/_static/pmc.png)

<span data-ttu-id="befcc-154">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="befcc-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="befcc-155">Der Befehl `Add-Migration` weist das Migrationsframework an, das aktuelle `Movie`-Modell mit dem aktuellen `Movie`-Datenbankschema zu untersuchen und den notwendigen Code zu erstellen, um die Datenbank in das neue Modell zu migrieren.</span><span class="sxs-lookup"><span data-stu-id="befcc-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="befcc-156">Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen.</span><span class="sxs-lookup"><span data-stu-id="befcc-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="befcc-157">Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="befcc-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="befcc-158">Werden alle Datensätze aus der Datenbank gelöscht, führt die initialize-Methode ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu.</span><span class="sxs-lookup"><span data-stu-id="befcc-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="befcc-159">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="befcc-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="befcc-160">Löschen Sie die Datenbank und die vorherige Migration, und verwenden Sie Migrationsvorgänge, um die Datenbank erneut zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="befcc-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="befcc-161">`dotnet ef migrations remove` entfernt die letzte Migration.</span><span class="sxs-lookup"><span data-stu-id="befcc-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="befcc-162">Wenn mehrere Migrationsvorgänge vorhanden ist, löschen Sie den Migrationsordner.</span><span class="sxs-lookup"><span data-stu-id="befcc-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="befcc-163">Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen, bearbeiten und anzeigen können.</span><span class="sxs-lookup"><span data-stu-id="befcc-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="befcc-164">[Zurück](search.md)
> [Weiter](validation.md)</span><span class="sxs-lookup"><span data-stu-id="befcc-164">[Previous](search.md)
[Next](validation.md)</span></span>
