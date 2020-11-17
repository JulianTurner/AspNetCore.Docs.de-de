---
title: 'Teil 2: Hinzufügen eines Modells'
author: rick-anderson
description: Dies ist Teil 2 der Tutorialreihe zu Razor Pages. In diesem Abschnitt werden Modellklassen hinzugefügt.
ms.author: riande
ms.date: 11/11/2020
no-loc:
- Index
- Create
- Delete
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
ms.openlocfilehash: 6244ac8798fb470a88802389961968fb52bd3c0a
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550678"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="9d67f-104">Teil 2: Hinzufügen eines Modells zu einer Razor Pages-App in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9d67f-104">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="9d67f-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9d67f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9d67f-106">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer Datenbank hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-106">In this section, classes are added for managing movies in a database.</span></span> <span data-ttu-id="9d67f-107">Die Modellklassen der App verwenden [Entity Framework Core](/ef/core) (EF Core), um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-107">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9d67f-108">EF Core ist ein objektrelationaler Mapper (O/RM), der den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="9d67f-108">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span> <span data-ttu-id="9d67f-109">Sie schreiben zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9d67f-109">You write the model classes first, and EF Core creates the database.</span></span>

<span data-ttu-id="9d67f-110">Die Modellklassen werden als POCO-Klassen (von „**P** lain-**O** ld **C** LR **O** bjects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-110">The model classes are known as POCO classes (from "**P** lain-**O** ld **C** LR **O** bjects") because they don't have a dependency on EF Core.</span></span> <span data-ttu-id="9d67f-111">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="9d67f-111">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d67f-112">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d67f-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d67f-113">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="9d67f-113">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-114">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d67f-115">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt *RazorPagesMovie*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-115">In **Solution Explorer**, right-click the *RazorPagesMovie* project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d67f-116">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-116">Name the folder *Models*.</span></span>
1. <span data-ttu-id="9d67f-117">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-117">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d67f-118">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-118">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d67f-119">Nennen Sie die Klasse *Movie*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-119">Name the class *Movie*.</span></span>
1. <span data-ttu-id="9d67f-120">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-120">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-121">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-121">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-122">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-122">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-123">`[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-123">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-124">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-124">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-125">Der Benutzer muss keine Zeitinformationen in das Datumsfeld eingeben.</span><span class="sxs-lookup"><span data-stu-id="9d67f-125">The user isn't required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-126">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-126">Only the date is displayed, not time information.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="9d67f-128">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-128">Add a folder named *Models*.</span></span>
1. <span data-ttu-id="9d67f-129">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-129">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d67f-130">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-130">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-131">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-131">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-132">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-132">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-133">`[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-133">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-134">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-134">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-135">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-135">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-136">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-136">Only the date is displayed, not time information.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d67f-137">Hinzufügen von NuGet-Paketen und EF-Tools</span><span class="sxs-lookup"><span data-stu-id="9d67f-137">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d67f-138">Hinzufügen einer Datenbankkontext-Klasse</span><span class="sxs-lookup"><span data-stu-id="9d67f-138">Add a database context class</span></span>

1. <span data-ttu-id="9d67f-139">Erstellen Sie im Projekt *RazorPagesMovie* einen Ordner mit dem Namen *Data*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-139">In the *RazorPagesMovie* project, create a folder named *Data*.</span></span>
1. <span data-ttu-id="9d67f-140">Fügen Sie im Ordner *Data* eine Datei mit dem Namen *RazorPagesMovieContext.cs* mit folgendem Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-140">In the *Data* folder, add a file named *RazorPagesMovieContext.cs* with the following code:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   <span data-ttu-id="9d67f-141">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-141">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d67f-142">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-142">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d67f-143">Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-143">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d67f-144">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="9d67f-144">Add a database connection string</span></span>

<span data-ttu-id="9d67f-145">Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-145">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d67f-146">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-146">Register the database context</span></span>

1. <span data-ttu-id="9d67f-147">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="9d67f-147">Add the following `using` statements at the top of *Startup.cs*:</span></span>

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. <span data-ttu-id="9d67f-148">Registrieren Sie den Datenbankkontext beim Container [Dependency Injection](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="9d67f-148">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-149">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-149">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d67f-150">Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt *RazorPagesMovie*, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-150">In the **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
1. <span data-ttu-id="9d67f-151">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Modelle*, und wählen Sie dann **Hinzufügen** > **Neue Datei...** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-151">Control-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
1. <span data-ttu-id="9d67f-152">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-152">In the **New File** dialog:</span></span>
   1. <span data-ttu-id="9d67f-153">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-153">Select **General** in the left pane.</span></span>
   1. <span data-ttu-id="9d67f-154">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-154">Select **Empty Class** in the center pane.</span></span>
   1. <span data-ttu-id="9d67f-155">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-155">Name the class **Movie** and select **New**.</span></span>

1. <span data-ttu-id="9d67f-156">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-156">Add the following properties to the `Movie` class:</span></span>

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-157">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-157">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-158">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-158">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-159">`[DataType(DataType.Date)]`: Das Attribut [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-159">`[DataType(DataType.Date)]`: The [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-160">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-160">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-161">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-161">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-162">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-162">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9d67f-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-163">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9d67f-164">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-164">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d67f-165">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="9d67f-165">Scaffold the movie model</span></span>

<span data-ttu-id="9d67f-166">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-166">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d67f-167">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-167">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-168">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-168">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9d67f-169">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-169">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9d67f-170">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-170">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9d67f-171">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-171">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9d67f-172">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-172">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/5/sca.png)

1. <span data-ttu-id="9d67f-174">Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-174">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

1. <span data-ttu-id="9d67f-176">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-176">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9d67f-177">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-177">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
   1. <span data-ttu-id="9d67f-178">Wählen Sie in der Zeile **Datenkontextklasse** das **+** -Zeichen (Pluszeichen) aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-178">In the **Data context class** row, select the **+** (plus) sign.</span></span>
      1. <span data-ttu-id="9d67f-179">Im Dialogfeld **Datenkontext hinzufügen** wird der Klassenname *RazorPagesMovie.Data.RazorPagesMovieContext* generiert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-179">In the **Add Data Context** dialog, the class name *RazorPagesMovie.Data.RazorPagesMovieContext* is generated.</span></span>
   1. <span data-ttu-id="9d67f-180">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-180">Select **Add**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

<span data-ttu-id="9d67f-182">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-182">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-183">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-183">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d67f-184">Öffnen Sie eine Befehlsshell im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="9d67f-184">Open a command shell to the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9d67f-185">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-185">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d67f-186">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-186">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d67f-187">In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-187">The following table details the ASP.NET Core code generator options.</span></span>

| <span data-ttu-id="9d67f-188">Option</span><span class="sxs-lookup"><span data-stu-id="9d67f-188">Option</span></span>               | <span data-ttu-id="9d67f-189">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9d67f-189">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d67f-190">Der Name des Modells.</span><span class="sxs-lookup"><span data-stu-id="9d67f-190">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d67f-191">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9d67f-191">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d67f-192">Verwendung des Standardlayouts.</span><span class="sxs-lookup"><span data-stu-id="9d67f-192">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d67f-193">Der relative Ausgabeordnerpfad zur Erstellung der Ansichten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-193">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d67f-194">Fügt `_ValidationScriptsPartial` den Seiten „Edit“ und Create hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-194">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d67f-195">Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:</span><span class="sxs-lookup"><span data-stu-id="9d67f-195">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d67f-196">Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d67f-196">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d67f-197">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="9d67f-197">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d67f-198">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="9d67f-198">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d67f-199">Der folgende Code zeigt das Einfügen von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-199">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9d67f-200">`IWebHostEnvironment` wird eingefügt, sodass die App SQLite in der Entwicklungs- und SQL Server der Produktionsumgebung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="9d67f-200">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-201">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-201">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="9d67f-202">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-202">Create a *Pages/Movies* folder:</span></span>
   1. <span data-ttu-id="9d67f-203">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages*. Wählen Sie **Hinzufügen** > **Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-203">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
   1. <span data-ttu-id="9d67f-204">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-204">Name the folder *Movies*.</span></span>

1. <span data-ttu-id="9d67f-205">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages/Movies*, und wählen Sie **Hinzufügen** > **Neuer Gerüstbau...** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-205">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

1. <span data-ttu-id="9d67f-207">Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-207">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

1. <span data-ttu-id="9d67f-209">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-209">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
   1. <span data-ttu-id="9d67f-210">Nennen Sie in der Zeile **Zu verwendende DbContext-Klasse:** die Klasse *RazorPagesMovie.Data.RazorPagesMovieContext*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-210">In the **DbContext Class to use:** row, name the class *RazorPagesMovie.Data.RazorPagesMovieContext*.</span></span>
   1. <span data-ttu-id="9d67f-211">Wählen Sie **Fertig stellen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-211">Select **Finish**.</span></span>

   ![Abbildung der vorherigen Anweisungen.](model/_static/5/arpMac.png)

<span data-ttu-id="9d67f-213">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-213">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d67f-214">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="9d67f-214">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d67f-215">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="9d67f-215">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d67f-216">Der folgende Code zeigt das Einfügen von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-216">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup`.</span></span> <span data-ttu-id="9d67f-217">`IWebHostEnvironment` wird eingefügt, sodass die App SQLite in der Entwicklungs- und SQL Server der Produktionsumgebung verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="9d67f-217">`IWebHostEnvironment` is injected so the app can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9d67f-218">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="9d67f-218">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-220">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-220">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d67f-221">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-221">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d67f-222">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-222">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d67f-223">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="9d67f-223">Updated</span></span>

* <span data-ttu-id="9d67f-224">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-224">*Startup.cs*</span></span>

<span data-ttu-id="9d67f-225">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-225">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-226">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-226">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9d67f-227">Der Gerüstbauprozess erstellt die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-227">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9d67f-228">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-228">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9d67f-229">Die erstellten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-229">The created files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-230">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-230">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d67f-231">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-231">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d67f-232">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-232">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d67f-233">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-233">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d67f-234">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="9d67f-234">Updated</span></span>

* <span data-ttu-id="9d67f-235">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-235">*Startup.cs*</span></span>

<span data-ttu-id="9d67f-236">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-236">The created and updated files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="no-loccreate-the-initial-database-schema-using-efs-migration-feature"></a><span data-ttu-id="9d67f-237">Create (erstellen) Sie das anfängliche Datenbankschema mithilfe des EF-Features „Migrationen“</span><span class="sxs-lookup"><span data-stu-id="9d67f-237">Create the initial database schema using EF's migration feature</span></span>

<span data-ttu-id="9d67f-238">Das Feature „Migrationen“ in Entity Framework Core ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9d67f-238">The migrations feature in Entity Framework Core provides a way to:</span></span>

* <span data-ttu-id="9d67f-239">Create (erstellen) des anfänglichen Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="9d67f-239">Create the initial database schema.</span></span>
* <span data-ttu-id="9d67f-240">Schrittweises Aktualisieren des Datenbankschemas, um es mit dem Datenmodell der Anwendung synchron zu halten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-240">Incrementally update the database schema to keep it in sync with the application's data model.</span></span>  <span data-ttu-id="9d67f-241">In der Datenbank vorhandene Daten werden beibehalten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-241">Existing data in the database is preserved.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-243">In diesem Abschnitt wird das Fenster **Paket-Manager-Konsole** (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="9d67f-243">In this section, the **Package Manager Console** (PMC) window is used to:</span></span>

* <span data-ttu-id="9d67f-244">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-244">Add an initial migration.</span></span>
* <span data-ttu-id="9d67f-245">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="9d67f-245">Update the database with the initial migration.</span></span>

1. <span data-ttu-id="9d67f-246">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-246">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

   ![PMC-Menü](model/_static/5/pmc.png)

1. <span data-ttu-id="9d67f-248">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9d67f-248">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-249">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-249">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* <span data-ttu-id="9d67f-250">Führen Sie die folgenden .NET-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-250">Run the following .NET CLI commands:</span></span>

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

<span data-ttu-id="9d67f-251">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9d67f-251">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d67f-252">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-252">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d67f-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="9d67f-253">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d67f-254">Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-254">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9d67f-255">Mit dem Befehl `migrations` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-255">The `migrations` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d67f-256">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="9d67f-256">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9d67f-257">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-257">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9d67f-258">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-258">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9d67f-259">Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-259">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9d67f-260">In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-260">In this case, `update` runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-261">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d67f-262">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-262">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d67f-263">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-263">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d67f-264">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-264">Services, such as the EF Core database context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d67f-265">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-265">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d67f-266">Der Konstruktorcode, der eine Datenbankkontextinstanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-266">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d67f-267">Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-267">The scaffolding tool automatically created a database context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d67f-268">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-268">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d67f-269">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-269">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9d67f-270">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="9d67f-270">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d67f-271">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9d67f-271">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d67f-272">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9d67f-272">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d67f-273">Der vorangehende Code erstellt eine [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-273">The preceding code creates a [DbSet\<Movie>](xref:Microsoft.EntityFrameworkCore.DbSet%601) property for the entity set.</span></span> <span data-ttu-id="9d67f-274">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-274">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d67f-275">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-275">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d67f-276">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-276">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d67f-277">Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9d67f-277">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-278">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-278">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d67f-279">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-279">Examine the `Up` method.</span></span>

---

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="9d67f-280">Testen der App</span><span class="sxs-lookup"><span data-stu-id="9d67f-280">Test the app</span></span>

1. <span data-ttu-id="9d67f-281">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9d67f-281">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

   <span data-ttu-id="9d67f-282">Möglicherweise wird eine Fehlermeldung wie diese angezeigt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-282">If you receive the following error:</span></span>

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   <span data-ttu-id="9d67f-283">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="9d67f-283">You missed the [migrations step](#pmc).</span></span>

1. <span data-ttu-id="9d67f-284">Testen Sie den Link **Create** .</span><span class="sxs-lookup"><span data-stu-id="9d67f-284">Test the **Create** link.</span></span>

   ![Seite Create](model/_static/conan5.png)

   > [!NOTE]
   > <span data-ttu-id="9d67f-286">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="9d67f-286">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d67f-287">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-287">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d67f-288">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9d67f-288">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

1. <span data-ttu-id="9d67f-289">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="9d67f-289">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9d67f-290">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-290">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d67f-291">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9d67f-291">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d67f-292">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d67f-292">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="9d67f-293">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-293">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="9d67f-294">Die Modellklassen der App verwenden [Entity Framework Core](/ef/core) (EF Core), um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-294">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="9d67f-295">EF Core ist ein objektrelationaler Mapper (O/RM), der den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="9d67f-295">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="9d67f-296">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-296">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9d67f-297">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="9d67f-297">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d67f-298">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d67f-298">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d67f-299">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="9d67f-299">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-300">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-300">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-301">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-301">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d67f-302">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-302">Name the folder *Models*.</span></span>

<span data-ttu-id="9d67f-303">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-303">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d67f-304">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-304">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d67f-305">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-305">Name the class **Movie**.</span></span>

<span data-ttu-id="9d67f-306">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-306">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-307">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-307">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-308">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-308">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-309">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-309">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-310">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-310">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-311">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-311">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-312">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-312">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d67f-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-313">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-314">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-314">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9d67f-315">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-315">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9d67f-316">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-316">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d67f-317">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-317">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-318">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-318">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-319">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-319">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-320">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-320">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-321">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-321">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-322">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-322">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-323">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-323">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d67f-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-324">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d67f-325">Hinzufügen von NuGet-Paketen und EF-Tools</span><span class="sxs-lookup"><span data-stu-id="9d67f-325">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d67f-326">Hinzufügen einer Datenbankkontext-Klasse</span><span class="sxs-lookup"><span data-stu-id="9d67f-326">Add a database context class</span></span>

* <span data-ttu-id="9d67f-327">Erstellen Sie im Projekt *RazorPagesMovie* einen neuen einen Ordner mit dem Namen *Data*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-327">In the *RazorPagesMovie* project, create a new folder named *Data*.</span></span>
* <span data-ttu-id="9d67f-328">Fügen Sie dem Ordner *Data* (Daten) die folgende `RazorPagesMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-328">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d67f-329">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-329">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d67f-330">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-330">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d67f-331">Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-331">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d67f-332">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="9d67f-332">Add a database connection string</span></span>

<span data-ttu-id="9d67f-333">Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-333">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d67f-334">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-334">Register the database context</span></span>

<span data-ttu-id="9d67f-335">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="9d67f-335">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d67f-336">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-336">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-337">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-337">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9d67f-338">Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt **RazorPagesMovie**, und wählen Sie dann **Hinzufügen** > **Neuer Ordner...** aus. Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-338">In the **Solution Tool Window**, control-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="9d67f-339">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei...** .</span><span class="sxs-lookup"><span data-stu-id="9d67f-339">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="9d67f-340">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-340">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9d67f-341">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-341">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9d67f-342">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-342">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9d67f-343">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-343">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9d67f-344">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-344">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-345">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-345">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-346">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-346">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-347">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-347">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-348">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-348">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-349">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-349">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-350">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-350">Only the date is displayed, not time information.</span></span>

---

<span data-ttu-id="9d67f-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-351">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<span data-ttu-id="9d67f-352">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-352">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d67f-353">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="9d67f-353">Scaffold the movie model</span></span>

<span data-ttu-id="9d67f-354">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-354">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d67f-355">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-355">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-356">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-356">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-357">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-357">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d67f-358">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-358">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d67f-359">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-359">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d67f-360">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-360">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="9d67f-362">Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-362">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="9d67f-364">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-364">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d67f-365">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-365">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d67f-366">Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und ändern Sie den generierten Namen von RazorPagesMovie.**Models**.RazorPagesMovieContext in RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9d67f-366">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9d67f-367">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9d67f-367">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9d67f-368">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="9d67f-368">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9d67f-369">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-369">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/3/arp.png)

<span data-ttu-id="9d67f-371">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-371">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-372">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-372">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d67f-373">Öffnen Sie ein Befehlsfenster im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="9d67f-373">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9d67f-374">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-374">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d67f-375">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-375">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d67f-376">In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-376">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9d67f-377">Option</span><span class="sxs-lookup"><span data-stu-id="9d67f-377">Option</span></span>               | <span data-ttu-id="9d67f-378">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9d67f-378">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d67f-379">Der Name des Modells.</span><span class="sxs-lookup"><span data-stu-id="9d67f-379">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d67f-380">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9d67f-380">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d67f-381">Verwendung des Standardlayouts.</span><span class="sxs-lookup"><span data-stu-id="9d67f-381">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d67f-382">Der relative Ausgabeordnerpfad zur Erstellung der Ansichten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-382">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d67f-383">Fügt `_ValidationScriptsPartial` den Seiten „Edit“ und Create hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-383">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d67f-384">Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:</span><span class="sxs-lookup"><span data-stu-id="9d67f-384">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d67f-385">Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d67f-385">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d67f-386">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="9d67f-386">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d67f-387">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="9d67f-387">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d67f-388">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="9d67f-388">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9d67f-389">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="9d67f-389">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-390">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-390">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d67f-391">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-391">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d67f-392">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-392">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d67f-393">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-393">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d67f-394">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies* > **Hinzufügen** > **Neuer Gerüstbau...** .</span><span class="sxs-lookup"><span data-stu-id="9d67f-394">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="9d67f-396">Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-396">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9d67f-398">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-398">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d67f-399">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus, oder geben Sie diesen ein.</span><span class="sxs-lookup"><span data-stu-id="9d67f-399">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d67f-400">Geben Sie in der Zeile **Datenkontextklasse** den Namen der neuen Klasse ein: RazorPagesMovie.**Data**.RazorPagesMovieContext.</span><span class="sxs-lookup"><span data-stu-id="9d67f-400">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="9d67f-401">[Dieses Änderung](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9d67f-401">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="9d67f-402">Sie erstellt die Datenbank-Kontextklasse mit dem korrekten Namespace.</span><span class="sxs-lookup"><span data-stu-id="9d67f-402">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="9d67f-403">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-403">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="9d67f-405">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-405">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="9d67f-406">Hinzufügen von EF-Tools</span><span class="sxs-lookup"><span data-stu-id="9d67f-406">Add EF tools</span></span>

<span data-ttu-id="9d67f-407">Führen Sie den folgenden .NET Core-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-407">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="9d67f-408">Der vorangehende Befehl fügt die Entity Framework Core-Tools für die .NET Core-CLI hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-408">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span> <span data-ttu-id="9d67f-409">Weitere Informationen finden Sie unter [Referenz zu Entity Framework Core-Tools: .NET Core-CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9d67f-409">For more information, see [Entity Framework Core tools reference - .NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="9d67f-410">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="9d67f-410">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="9d67f-411">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="9d67f-411">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="9d67f-412">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="9d67f-412">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="9d67f-413">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="9d67f-413">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a><span data-ttu-id="9d67f-414">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="9d67f-414">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-415">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-415">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-416">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-416">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d67f-417">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-417">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d67f-418">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-418">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d67f-419">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="9d67f-419">Updated</span></span>

* <span data-ttu-id="9d67f-420">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-420">*Startup.cs*</span></span>

<span data-ttu-id="9d67f-421">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-421">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-422">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-422">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d67f-423">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-423">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="9d67f-424">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-424">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d67f-425">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-425">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="9d67f-426">Aktualisiert</span><span class="sxs-lookup"><span data-stu-id="9d67f-426">Updated</span></span>

* <span data-ttu-id="9d67f-427">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-427">*Startup.cs*</span></span>

<span data-ttu-id="9d67f-428">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-428">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-429">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-429">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9d67f-430">Der Gerüstbauprozess erstellt die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-430">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="9d67f-431">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-431">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="9d67f-432">Die erstellten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-432">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9d67f-433">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="9d67f-433">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-434">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-434">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-435">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="9d67f-435">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9d67f-436">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-436">Add an initial migration.</span></span>
* <span data-ttu-id="9d67f-437">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="9d67f-437">Update the database with the initial migration.</span></span>

<span data-ttu-id="9d67f-438">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-438">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9d67f-440">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9d67f-440">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-441">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-441">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9d67f-442">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-442">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9d67f-443">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9d67f-443">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d67f-444">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-444">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d67f-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="9d67f-445">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d67f-446">Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-446">Ignore the warning, as it will be addressed in a later step.</span></span>

<span data-ttu-id="9d67f-447">Mit dem Migrationsbefehl wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-447">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d67f-448">Das Schema basiert auf dem Modell, das in `DbContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="9d67f-448">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="9d67f-449">Das Argument `InitialCreate` wird verwendet, um die Migrationen zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-449">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="9d67f-450">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-450">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="9d67f-451">Der `update`-Befehl führt die `Up`-Methode in Migrationen aus, die nicht angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-451">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="9d67f-452">In diesem Fall führt `update` die `Up`-Methode in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-452">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-453">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-453">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d67f-454">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-454">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d67f-455">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-455">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d67f-456">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-456">Services, such as the EF Core database context context, are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d67f-457">Komponenten, die diese Dienste erfordern (z. B. Razor-Seiten), werden diese Dienste über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-457">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d67f-458">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-458">The constructor code that gets a database context context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d67f-459">Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-459">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d67f-460">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-460">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d67f-461">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-461">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="9d67f-462">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="9d67f-462">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d67f-463">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9d67f-463">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d67f-464">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9d67f-464">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d67f-465">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-465">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9d67f-466">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-466">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d67f-467">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-467">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d67f-468">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-468">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d67f-469">Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9d67f-469">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-470">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d67f-471">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-471">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9d67f-472">Testen der App</span><span class="sxs-lookup"><span data-stu-id="9d67f-472">Test the app</span></span>

* <span data-ttu-id="9d67f-473">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9d67f-473">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9d67f-474">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="9d67f-474">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9d67f-475">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="9d67f-475">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9d67f-476">Testen Sie den Link **Create** .</span><span class="sxs-lookup"><span data-stu-id="9d67f-476">Test the **Create** link.</span></span>

  ![Seite Create](model/_static/conan5.png)

  > [!NOTE]
  > <span data-ttu-id="9d67f-478">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="9d67f-478">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d67f-479">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-479">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d67f-480">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9d67f-480">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9d67f-481">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="9d67f-481">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9d67f-482">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-482">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d67f-483">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9d67f-483">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d67f-484">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d67f-484">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9d67f-485">In diesem Abschnitt werden Klassen für die Verwaltung von Filmen in einer plattformübergreifenden [SQLite-Datenbank](https://www.sqlite.org/index.html) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-485">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="9d67f-486">Apps, die aus einer ASP.NET Core-Vorlage erstellt werden, verwenden eine SQLite-Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9d67f-486">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="9d67f-487">Die Modellklassen der App werden mit [Entity Framework Core (EF Core)](/ef/core) ([SQLite-EF Core-Datenbankanbieter](/ef/core/providers/sqlite)) verwendet, um mit der Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-487">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="9d67f-488">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriff vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="9d67f-488">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="9d67f-489">Die Modellklassen werden als POCO-Klassen (von „Plain-Old CLR Objects“) bezeichnet, da sie keinerlei Abhängigkeit von EF Core aufweisen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-489">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="9d67f-490">Sie definieren die Eigenschaften einer Datei, die in der Datenbank gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="9d67f-490">They define the properties of the data that are stored in the database.</span></span>

<span data-ttu-id="9d67f-491">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9d67f-491">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="add-a-data-model"></a><span data-ttu-id="9d67f-492">Hinzufügen eines Datenmodells</span><span class="sxs-lookup"><span data-stu-id="9d67f-492">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-493">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-494">Klicken Sie mit der rechten Maustaste auf das Projekt **RazorPagesMovie** und dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-494">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="9d67f-495">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-495">Name the folder *Models*.</span></span>

<span data-ttu-id="9d67f-496">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-496">Right-click the *Models* folder.</span></span> <span data-ttu-id="9d67f-497">Wählen Sie **Hinzufügen** > **Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-497">Select **Add** > **Class**.</span></span> <span data-ttu-id="9d67f-498">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-498">Name the class **Movie**.</span></span>

<span data-ttu-id="9d67f-499">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-499">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-500">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-500">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-501">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-501">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-502">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-502">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-503">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-503">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-504">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-504">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-505">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-505">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d67f-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-506">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-507">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-507">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9d67f-508">Fügen Sie einen Ordner namens *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-508">Add a folder named *Models*.</span></span>
* <span data-ttu-id="9d67f-509">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-509">Add a class to the *Models* folder named *Movie.cs*.</span></span>

<span data-ttu-id="9d67f-510">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-510">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-511">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-511">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-512">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-512">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-513">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-513">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-514">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-514">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-515">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-515">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-516">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-516">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d67f-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-517">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a><span data-ttu-id="9d67f-518">Hinzufügen von NuGet-Paketen und EF-Tools</span><span class="sxs-lookup"><span data-stu-id="9d67f-518">Add NuGet packages and EF tools</span></span>

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a><span data-ttu-id="9d67f-519">Hinzufügen einer Datenbankkontext-Klasse</span><span class="sxs-lookup"><span data-stu-id="9d67f-519">Add a database context class</span></span>

<span data-ttu-id="9d67f-520">Erstellen Sie im Projekt RazorPagesMovie einen neuen einen Ordner mit dem Namen *Data*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-520">In the RazorPagesMovie project, create a new folder named *Data*.</span></span> 
<span data-ttu-id="9d67f-521">Fügen Sie dem Ordner *Data* (Daten) die folgende `RazorPagesMovieContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-521">Add the following `RazorPagesMovieContext` class to the *Data* folder:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d67f-522">Der vorangehende Code erstellt eine `DbSet`-Eigenschaft für die Entitätenmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-522">The preceding code creates a `DbSet` property for the entity set.</span></span> <span data-ttu-id="9d67f-523">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle, und eine Entität entspricht einer Zeile in einer Tabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-523">In Entity Framework terminology, an entity set typically corresponds to a database table, and an entity corresponds to a row in the table.</span></span> <span data-ttu-id="9d67f-524">Der Code wird erst kompiliert, wenn Abhängigkeiten in einem späteren Schritt hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-524">The code won't compile until dependencies are added in a later step.</span></span>

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a><span data-ttu-id="9d67f-525">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="9d67f-525">Add a database connection string</span></span>

<span data-ttu-id="9d67f-526">Fügen Sie zur Datei *appsettings.json* wie im folgenden hervorgehobenen Code dargestellt eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-526">Add a connection string to the *appsettings.json* file as shown in the following highlighted code:</span></span>

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a><span data-ttu-id="9d67f-527">Hinzufügen der erforderlichen NuGet-Pakete</span><span class="sxs-lookup"><span data-stu-id="9d67f-527">Add required NuGet packages</span></span>

<span data-ttu-id="9d67f-528">Führen Sie den folgenden .NET Core-CLI-Befehl aus, um dem Projekt „SQLite“ und „CodeGeneration.Design“ hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="9d67f-528">Run the following .NET Core CLI command to add SQLite and CodeGeneration.Design to the project:</span></span>

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

<span data-ttu-id="9d67f-529">Das `Microsoft.VisualStudio.Web.CodeGeneration.Design`-Paket wird für den Gerüstbau benötigt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-529">The `Microsoft.VisualStudio.Web.CodeGeneration.Design` package is required for scaffolding.</span></span>

<a name="reg"></a>

### <a name="register-the-database-context"></a><span data-ttu-id="9d67f-530">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-530">Register the database context</span></span>

<span data-ttu-id="9d67f-531">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="9d67f-531">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="9d67f-532">Registrieren Sie den Datenbankkontext mit dem [Dependency Injection-Container](xref:fundamentals/dependency-injection) in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-532">Register the database context with the [dependency injection](xref:fundamentals/dependency-injection) container in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

<span data-ttu-id="9d67f-533">Erstellen Sie das Projekt zur Fehlerüberprüfung.</span><span class="sxs-lookup"><span data-stu-id="9d67f-533">Build the project as a check for errors.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-534">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-534">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="9d67f-535">Klicken Sie im Fenster **Projektmappenpad** bei gedrückter CTRL-Taste auf das Projekt *RazorPagesMovie*, und wählen Sie dann **Hinzufügen**  >  **Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-535">In **Solution Tool Window**, control-click the *RazorPagesMovie* project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="9d67f-536">Geben Sie dem Ordner den Namen *Modelle*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-536">Name the folder *Models*.</span></span>
* <span data-ttu-id="9d67f-537">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Modelle*, und klicken Sie auf **Hinzufügen** > **Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-537">Control-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="9d67f-538">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-538">In the **New File** dialog:</span></span>

  * <span data-ttu-id="9d67f-539">Klicken Sie im linken Bereich auf **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-539">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="9d67f-540">Klicken Sie im mittleren Bereich auf **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-540">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="9d67f-541">Geben Sie der Klasse den Namen **Film**, und wählen sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-541">Name the class **Movie** and select **New**.</span></span>

<span data-ttu-id="9d67f-542">Fügen Sie der Klasse `Movie` die folgenden Eigenschaften hinzu:</span><span class="sxs-lookup"><span data-stu-id="9d67f-542">Add the following properties to the `Movie` class:</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

<span data-ttu-id="9d67f-543">Die `Movie`-Klasse enthält:</span><span class="sxs-lookup"><span data-stu-id="9d67f-543">The `Movie` class contains:</span></span>

* <span data-ttu-id="9d67f-544">Die Datenbank benötigt das Feld `ID` für den primären Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="9d67f-544">The `ID` field is required by the database for the primary key.</span></span>
* <span data-ttu-id="9d67f-545">`[DataType(DataType.Date)]`: Das [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute)-Attribut gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="9d67f-545">`[DataType(DataType.Date)]`: The [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) attribute specifies the type of the data (`Date`).</span></span> <span data-ttu-id="9d67f-546">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="9d67f-546">With this attribute:</span></span>

  * <span data-ttu-id="9d67f-547">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-547">The user is not required to enter time information in the date field.</span></span>
  * <span data-ttu-id="9d67f-548">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-548">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="9d67f-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-549">[DataAnnotations](xref:System.ComponentModel.DataAnnotations) are covered in a later tutorial.</span></span>

---

<span data-ttu-id="9d67f-550">Erstellen Sie das Projekt, um sicherzustellen, dass keine Kompilierungsfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-550">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="9d67f-551">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="9d67f-551">Scaffold the movie model</span></span>

<span data-ttu-id="9d67f-552">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-552">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="9d67f-553">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-553">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-554">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-554">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-555">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-555">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d67f-556">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages*. Klicken Sie dann auf **Hinzufügen** > **Neuer Ordner**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-556">Right-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d67f-557">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-557">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d67f-558">Klicken Sie mit der rechten Maustaste auf den Ordner *Pages/Movies*. Klicken Sie dann auf **Hinzufügen** > **Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-558">Right-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/sca.png)

<span data-ttu-id="9d67f-560">Klicken Sie im Dialogfeld **Gerüst hinzufügen** auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-560">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffold.png)

<span data-ttu-id="9d67f-562">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-562">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="9d67f-563">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film (RazorPagesMovie.Models)** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-563">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="9d67f-564">Klicken Sie in der Zeile **Datenkontextklasse** auf das Pluszeichen **+** , und akzeptieren Sie den generierten Namen **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-564">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9d67f-565">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-565">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arp.png)

<span data-ttu-id="9d67f-567">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-567">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="9d67f-568">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9d67f-568">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="9d67f-569">Öffnen Sie ein Befehlsfenster im Projektverzeichnis, das die Dateien *Program.cs*, *Startup.cs* sowie *CSPROJ*-Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="9d67f-569">Open a command window in the project directory, which contains the *Program.cs*, *Startup.cs*, and *.csproj* files.</span></span>

* <span data-ttu-id="9d67f-570">**Für Windows**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-570">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="9d67f-571">**Für MacOS und Linux**: Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-571">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> <span data-ttu-id="9d67f-572">In der folgenden Tabelle sind die Optionen des ASP.NET Core-Codegenerators detailliert aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-572">The following table details the ASP.NET Core code generator options:</span></span>

| <span data-ttu-id="9d67f-573">Option</span><span class="sxs-lookup"><span data-stu-id="9d67f-573">Option</span></span>               | <span data-ttu-id="9d67f-574">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="9d67f-574">Description</span></span>|
| ----------------- | ------------ |
| `-m`  | <span data-ttu-id="9d67f-575">Der Name des Modells.</span><span class="sxs-lookup"><span data-stu-id="9d67f-575">The name of the model.</span></span> |
| `-dc`  | <span data-ttu-id="9d67f-576">Die `DbContext`-Klasse, die verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="9d67f-576">The `DbContext` class to use.</span></span> |
| `-udl` | <span data-ttu-id="9d67f-577">Verwendung des Standardlayouts.</span><span class="sxs-lookup"><span data-stu-id="9d67f-577">Use the default layout.</span></span> |
| `-outDir` | <span data-ttu-id="9d67f-578">Der relative Ausgabeordnerpfad zur Erstellung der Ansichten.</span><span class="sxs-lookup"><span data-stu-id="9d67f-578">The relative output folder path to create the views.</span></span> |
| `--referenceScriptLibraries` | <span data-ttu-id="9d67f-579">Fügt `_ValidationScriptsPartial` den Seiten „Edit“ und Create hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-579">Adds `_ValidationScriptsPartial` to Edit and Create pages</span></span> |

<span data-ttu-id="9d67f-580">Über die Option `-h` können Sie Hilfe zum Befehl `aspnet-codegenerator razorpage` erhalten:</span><span class="sxs-lookup"><span data-stu-id="9d67f-580">Use the `-h` option to get help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

<span data-ttu-id="9d67f-581">Weitere Informationen finden Sie unter [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="9d67f-581">For more information, see [dotnet-aspnet-codegenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-582">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-582">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="9d67f-583">Create (erstellen) Sie den Ordner *Pages/Movies*:</span><span class="sxs-lookup"><span data-stu-id="9d67f-583">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="9d67f-584">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages*. Wählen Sie **Hinzufügen** > **Neuer Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-584">Control-click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="9d67f-585">Nennen Sie den Ordner *Movies*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-585">Name the folder *Movies*.</span></span>

<span data-ttu-id="9d67f-586">Klicken Sie bei gedrückter CTRL-TASTE auf den Ordner *Pages/Movies*, und wählen Sie **Hinzufügen** > **Neues Gerüstelement** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-586">Control-click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/scaMac.png)

<span data-ttu-id="9d67f-588">Klicken Sie im Dialogfeld **Add New Scaffolding** (Neuen Gerüstbau hinzufügen) auf **Razor-Seiten mithilfe des Entity Frameworks (CRUD)** > **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-588">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/add_scaffoldMac.png)

<span data-ttu-id="9d67f-590">Vervollständigen Sie das Dialogfeld **Add Razor Pages using Entity Framework (CRUD)** (Razor-Seiten mithilfe des Entity Frameworks (CRUD) hinzufügen):</span><span class="sxs-lookup"><span data-stu-id="9d67f-590">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="9d67f-591">Wählen Sie in der Dropdownliste **Modellklasse** den Eintrag **Film** aus, oder geben Sie ihn ein.</span><span class="sxs-lookup"><span data-stu-id="9d67f-591">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="9d67f-592">Geben Sie **RazorPagesMovieContext** in der Zeile **Datenkontextklasse** ein, und wählen Sie den Eintrag aus. Dadurch wird eine neue Datenbankkontext-Klasse mit dem richtigen Namespace erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-592">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new database context class with the correct namespace.</span></span> <span data-ttu-id="9d67f-593">In diesem Fall ist das **RazorPagesMovie.Models.RazorPagesMovieContext**.</span><span class="sxs-lookup"><span data-stu-id="9d67f-593">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="9d67f-594">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-594">Select **Add**.</span></span>

![Abbildung der vorherigen Anweisungen.](model/_static/arpMac.png)

<span data-ttu-id="9d67f-596">Die Datei *appsettings.json* wird mit der Verbindungszeichenfolge aktualisiert, die zum Herstellen einer Verbindung mit einer lokalen Datenbank verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-596">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="9d67f-597">Der Gerüstprozess erstellt und ändert folgende Dateien:</span><span class="sxs-lookup"><span data-stu-id="9d67f-597">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="9d67f-598">Erstellte Dateien</span><span class="sxs-lookup"><span data-stu-id="9d67f-598">Files created</span></span>

* <span data-ttu-id="9d67f-599">*Pages/Movies*: Create, Delete, Details, Edit und Index.</span><span class="sxs-lookup"><span data-stu-id="9d67f-599">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="9d67f-600">*Data/RazorPagesMovieContext.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-600">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="9d67f-601">Datei aktualisiert</span><span class="sxs-lookup"><span data-stu-id="9d67f-601">File updated</span></span>

* <span data-ttu-id="9d67f-602">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="9d67f-602">*Startup.cs*</span></span>

<span data-ttu-id="9d67f-603">Die erstellten und aktualisierten Daten werden im nächsten Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-603">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="9d67f-604">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="9d67f-604">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-605">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-605">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9d67f-606">In diesem Abschnitt wird die Paket-Manager-Konsole (Package Manager Console, PMC) für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="9d67f-606">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="9d67f-607">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="9d67f-607">Add an initial migration.</span></span>
* <span data-ttu-id="9d67f-608">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="9d67f-608">Update the database with the initial migration.</span></span>

<span data-ttu-id="9d67f-609">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-609">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC-Menü](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="9d67f-611">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="9d67f-611">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="9d67f-612">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-612">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="9d67f-613">Das Schema basiert auf dem Modell in `DbContext` in der Datei *RazorPagesMovieContext.cs*.</span><span class="sxs-lookup"><span data-stu-id="9d67f-613">The schema is based on the model specified in the `DbContext`, in the *RazorPagesMovieContext.cs* file.</span></span> <span data-ttu-id="9d67f-614">Das Argument `InitialCreate` wird verwendet, um die Migration zu benennen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-614">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="9d67f-615">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-615">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="9d67f-616">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="9d67f-616">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="9d67f-617">Der Befehl `Update-Database` führt die Methode `Up`-in der Datei *Migrations/\<time-stamp>_InitialCreate.cs* aus.</span><span class="sxs-lookup"><span data-stu-id="9d67f-617">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9d67f-618">Die Methode `Up` erstellt die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="9d67f-618">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-619">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-619">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="9d67f-620">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="9d67f-620">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="9d67f-621">Mit den vorherigen Befehlen wird die folgende Warnung erstellt: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="9d67f-621">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="9d67f-622">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-622">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="9d67f-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'." („Für die Spalte „Price“ mit Dezimalwerten beim Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.")</span><span class="sxs-lookup"><span data-stu-id="9d67f-623">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="9d67f-624">Ignorieren Sie die Warnung, da sie in einem späteren Schritt behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="9d67f-624">Ignore the warning, as it will be addressed in a later step.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="9d67f-625">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9d67f-625">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="9d67f-626">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="9d67f-626">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="9d67f-627">ASP.NET Core wird mit [Dependency Injection](xref:fundamentals/dependency-injection) erstellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-627">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9d67f-628">Dienste wie der EF Core-Datenbankkontext werden über Abhängigkeitsinjektion (Dependency Injection) beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-628">Services (such as the EF Core database context context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="9d67f-629">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9d67f-629">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="9d67f-630">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-630">The constructor code that gets a database context contextB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="9d67f-631">Das Tool für den Gerüstbau hat automatisch einen Datenbankkontext erstellt und diesen beim Dependency Injection-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="9d67f-631">The scaffolding tool automatically created a database context context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="9d67f-632">Untersuchen Sie die Methode `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-632">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="9d67f-633">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="9d67f-633">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="9d67f-634">Der `RazorPagesMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="9d67f-634">The `RazorPagesMovieContext` coordinates EF Core functionality, such as Create, Read, Update, and Delete, for the `Movie` model.</span></span> <span data-ttu-id="9d67f-635">Der Datenkontext (`RazorPagesMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="9d67f-635">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](xref:Microsoft.EntityFrameworkCore.DbContext).</span></span> <span data-ttu-id="9d67f-636">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="9d67f-636">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="9d67f-637">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="9d67f-637">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="9d67f-638">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="9d67f-638">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="9d67f-639">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-639">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="9d67f-640">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="9d67f-640">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) object.</span></span> <span data-ttu-id="9d67f-641">Für die lokale Entwicklung liest das [-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der Datei *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="9d67f-641">For local development, the [Configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="9d67f-642">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="9d67f-642">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="9d67f-643">Untersuchen Sie die Methode `Up`.</span><span class="sxs-lookup"><span data-stu-id="9d67f-643">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="9d67f-644">Testen der App</span><span class="sxs-lookup"><span data-stu-id="9d67f-644">Test the app</span></span>

* <span data-ttu-id="9d67f-645">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="9d67f-645">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="9d67f-646">Wenn Sie eine Fehlermeldung erhalten, müssen Sie Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="9d67f-646">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="9d67f-647">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="9d67f-647">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="9d67f-648">Testen Sie den Link **Create** .</span><span class="sxs-lookup"><span data-stu-id="9d67f-648">Test the **Create** link.</span></span>

  ![Seite Create](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="9d67f-650">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="9d67f-650">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="9d67f-651">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-651">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="9d67f-652">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="9d67f-652">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="9d67f-653">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="9d67f-653">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="9d67f-654">Im nächsten Tutorial finden Sie Erläuterungen zu den Dateien, die durch den Gerüstbau erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9d67f-654">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9d67f-655">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9d67f-655">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9d67f-656">[Zurück: Erste Schritte](xref:tutorials/razor-pages/razor-pages-start)
> [Weiter: Gerüstbau mit Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="9d67f-656">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
