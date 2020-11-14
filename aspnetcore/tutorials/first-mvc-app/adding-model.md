---
title: 'Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 4 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: fa1d79bed56f17afe69697a7e24ec200e6a0ab22
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422759"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="aa72e-103">Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="aa72e-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="aa72e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="aa72e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="aa72e-105">In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="aa72e-106">Diese Klassen bilden den „ **M** odell“-Teil der **M** VC-App.</span><span class="sxs-lookup"><span data-stu-id="aa72e-106">These classes will be the " **M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="aa72e-107">Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="aa72e-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="aa72e-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="aa72e-109">Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P** lain **O** ld **C** LR **O** bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="aa72e-110">Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="aa72e-111">In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="aa72e-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="aa72e-112">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-114">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="aa72e-115">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aa72e-117">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa72e-119">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="aa72e-120">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="aa72e-121">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="aa72e-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="aa72e-122">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="aa72e-123">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="aa72e-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="aa72e-124">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="aa72e-124">With this attribute:</span></span>

* <span data-ttu-id="aa72e-125">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="aa72e-126">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="aa72e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="aa72e-128">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="aa72e-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-130">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="aa72e-132">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="aa72e-133">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="aa72e-134">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="aa72e-135">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa72e-138">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="aa72e-139">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE** , um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="aa72e-140">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="aa72e-142">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="aa72e-143">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-143">Press the **Ok** button.</span></span>

<span data-ttu-id="aa72e-144">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="aa72e-145">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="aa72e-146">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="aa72e-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="aa72e-147">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-147">Create a database context class</span></span>

<span data-ttu-id="aa72e-148">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="aa72e-148">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="aa72e-149">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-149">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="aa72e-150">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-150">Create a *Data* folder.</span></span>

<span data-ttu-id="aa72e-151">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-151">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="aa72e-152">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="aa72e-152">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="aa72e-153">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="aa72e-153">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="aa72e-154">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-154">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="aa72e-155">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="aa72e-155">Register the database context</span></span>

<span data-ttu-id="aa72e-156">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-156">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aa72e-157">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-157">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="aa72e-158">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-158">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aa72e-159">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-159">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="aa72e-160">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="aa72e-160">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="aa72e-161">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="aa72e-161">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="aa72e-162">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-162">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-163">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-164">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-164">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="aa72e-165">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-165">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aa72e-166">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="aa72e-166">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="aa72e-167">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="aa72e-167">Add a database connection string</span></span>

<span data-ttu-id="aa72e-168">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-168">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-169">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-170">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-170">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="aa72e-171">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-171">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="aa72e-172">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="aa72e-172">Scaffold movie pages</span></span>

<span data-ttu-id="aa72e-173">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-173">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-174">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-175">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-175">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="aa72e-177">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-177">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="aa72e-179">Bearbeiten Sie das Dialogfeld **Controller hinzufügen** :</span><span class="sxs-lookup"><span data-stu-id="aa72e-179">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="aa72e-180">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="aa72e-180">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="aa72e-181">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="aa72e-181">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc5.png)

* <span data-ttu-id="aa72e-183">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-183">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="aa72e-184">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-184">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="aa72e-185">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-185">Select **Add**</span></span>

<span data-ttu-id="aa72e-186">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aa72e-186">Visual Studio creates:</span></span>

* <span data-ttu-id="aa72e-187">Einen Movies-Controller ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-187">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="aa72e-188">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-188">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="aa72e-189">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-189">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-190">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="aa72e-191">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-191">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="aa72e-192">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="aa72e-192">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="aa72e-193">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-193">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-194">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aa72e-195">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-195">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="aa72e-196">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-196">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="aa72e-197">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-197">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="aa72e-198">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-198">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="aa72e-199">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="aa72e-199">Initial migration</span></span>

<span data-ttu-id="aa72e-200">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-200">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="aa72e-201">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="aa72e-201">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-202">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-203">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-203">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="aa72e-204">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="aa72e-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="aa72e-205">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-205">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="aa72e-206">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-206">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="aa72e-207">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-207">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="aa72e-208">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="aa72e-208">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="aa72e-209">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-209">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="aa72e-210">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-210">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="aa72e-211">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-211">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="aa72e-212">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="aa72e-212">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="aa72e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="aa72e-213">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="aa72e-214">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-214">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="aa72e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="aa72e-215">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="aa72e-216">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-216">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-217">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-217">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="aa72e-218">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-218">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="aa72e-219">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-219">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="aa72e-220">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-220">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="aa72e-221">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-221">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="aa72e-222">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="aa72e-222">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="aa72e-223">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs* ) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-223">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="aa72e-224">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-224">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="aa72e-225">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-225">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="aa72e-226">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-226">The InitialCreate class</span></span>

<span data-ttu-id="aa72e-227">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-227">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="aa72e-228">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="aa72e-228">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="aa72e-229">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="aa72e-229">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="aa72e-230">Testen der App</span><span class="sxs-lookup"><span data-stu-id="aa72e-230">Test the app</span></span>

* <span data-ttu-id="aa72e-231">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-231">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="aa72e-232">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-232">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-233">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-234">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-234">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="aa72e-235">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-235">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="aa72e-236">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-236">Test the **Create** page.</span></span> <span data-ttu-id="aa72e-237">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="aa72e-237">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="aa72e-238">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-238">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="aa72e-239">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-239">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="aa72e-240">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="aa72e-240">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="aa72e-241">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-241">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="aa72e-242">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="aa72e-242">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-243">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-244">Öffnen Sie die Datei *Controllers/MoviesController.cs* , und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="aa72e-244">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="aa72e-245">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-245">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="aa72e-246">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-246">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-247">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-247">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="aa72e-248">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="aa72e-249">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="aa72e-250">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="aa72e-250">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="aa72e-251">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-251">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="aa72e-252">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="aa72e-252">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="aa72e-253">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="aa72e-253">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="aa72e-254">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="aa72e-254">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="aa72e-255">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="aa72e-255">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="aa72e-256">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-256">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="aa72e-257">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-257">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="aa72e-258">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-258">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="aa72e-259">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-259">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="aa72e-260">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-260">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="aa72e-261">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-261">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="aa72e-262">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="aa72e-262">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="aa72e-263">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-263">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="aa72e-264">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-264">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="aa72e-265">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-265">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="aa72e-266">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-266">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="aa72e-267">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-267">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="aa72e-268">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-268">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="aa72e-269">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-269">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="aa72e-270">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-270">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="aa72e-271">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-271">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="aa72e-272">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="aa72e-272">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="aa72e-273">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-273">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="aa72e-274">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-274">The `Model` object is strongly typed.</span></span> <span data-ttu-id="aa72e-275">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-275">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="aa72e-276">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-276">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="aa72e-277">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-277">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="aa72e-278">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-278">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="aa72e-279">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="aa72e-279">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="aa72e-280">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-280">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="aa72e-281">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-281">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="aa72e-282">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-282">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="aa72e-283">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-283">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="aa72e-284">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="aa72e-284">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aa72e-285">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aa72e-285">Additional resources</span></span>

* [<span data-ttu-id="aa72e-286">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="aa72e-286">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="aa72e-287">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="aa72e-287">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="aa72e-288">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="aa72e-288">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="aa72e-289">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-289">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-290">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-290">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-291">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-291">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="aa72e-292">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-292">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-293">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-293">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aa72e-294">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-294">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-295">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-295">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa72e-296">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-296">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="aa72e-297">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-297">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="aa72e-298">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="aa72e-298">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="aa72e-299">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-299">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="aa72e-300">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="aa72e-300">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="aa72e-301">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="aa72e-301">With this attribute:</span></span>

* <span data-ttu-id="aa72e-302">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-302">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="aa72e-303">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-303">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="aa72e-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-304">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="aa72e-305">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="aa72e-305">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-306">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-306">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-307">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-307">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="aa72e-309">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-309">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="aa72e-310">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-310">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="aa72e-311">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-311">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="aa72e-312">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-312">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-313">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-313">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-314">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-314">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="aa72e-315">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-315">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="aa72e-316">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE** , um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-316">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="aa72e-317">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-317">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="aa72e-319">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-319">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="aa72e-320">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-320">Press the **Ok** button.</span></span>

<span data-ttu-id="aa72e-321">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-321">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="aa72e-322">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-322">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="aa72e-323">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="aa72e-323">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="aa72e-324">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-324">Create a database context class</span></span>

<span data-ttu-id="aa72e-325">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="aa72e-325">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="aa72e-326">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-326">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="aa72e-327">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-327">Create a *Data* folder.</span></span>

<span data-ttu-id="aa72e-328">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-328">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="aa72e-329">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="aa72e-329">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="aa72e-330">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="aa72e-330">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="aa72e-331">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-331">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="aa72e-332">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="aa72e-332">Register the database context</span></span>

<span data-ttu-id="aa72e-333">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-333">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aa72e-334">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-334">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="aa72e-335">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-335">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aa72e-336">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-336">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="aa72e-337">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="aa72e-337">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="aa72e-338">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="aa72e-338">Add the following `using` statements at the top of *Startup.cs* :</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="aa72e-339">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-339">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-340">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-340">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-341">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-341">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="aa72e-342">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-342">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aa72e-343">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="aa72e-343">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="aa72e-344">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="aa72e-344">Add a database connection string</span></span>

<span data-ttu-id="aa72e-345">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa72e-345">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-346">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-346">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-347">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-347">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="aa72e-348">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-348">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="aa72e-349">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="aa72e-349">Scaffold movie pages</span></span>

<span data-ttu-id="aa72e-350">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-350">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-351">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-351">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-352">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-352">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="aa72e-354">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-354">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="aa72e-356">Bearbeiten Sie das Dialogfeld **Controller hinzufügen** :</span><span class="sxs-lookup"><span data-stu-id="aa72e-356">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="aa72e-357">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="aa72e-357">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="aa72e-358">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="aa72e-358">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* <span data-ttu-id="aa72e-360">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-360">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="aa72e-361">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-361">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="aa72e-362">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-362">Select **Add**</span></span>

<span data-ttu-id="aa72e-363">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aa72e-363">Visual Studio creates:</span></span>

* <span data-ttu-id="aa72e-364">Einen Movies-Controller ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-364">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="aa72e-365">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-365">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="aa72e-366">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-366">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-367">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-367">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="aa72e-368">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-368">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="aa72e-369">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="aa72e-369">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="aa72e-370">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-370">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-371">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-371">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aa72e-372">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-372">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="aa72e-373">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="aa72e-374">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-374">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="aa72e-375">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-375">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="aa72e-376">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="aa72e-376">Initial migration</span></span>

<span data-ttu-id="aa72e-377">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-377">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="aa72e-378">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="aa72e-378">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-379">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-380">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-380">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="aa72e-381">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="aa72e-381">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="aa72e-382">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-382">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="aa72e-383">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-383">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="aa72e-384">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-384">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="aa72e-385">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="aa72e-385">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="aa72e-386">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-386">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="aa72e-387">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-387">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="aa72e-388">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-388">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="aa72e-389">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="aa72e-389">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="aa72e-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="aa72e-390">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="aa72e-391">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-391">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="aa72e-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="aa72e-392">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="aa72e-393">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-393">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-394">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-394">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="aa72e-395">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-395">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="aa72e-396">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-396">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="aa72e-397">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-397">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="aa72e-398">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-398">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="aa72e-399">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="aa72e-399">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="aa72e-400">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs* ) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-400">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="aa72e-401">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-401">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="aa72e-402">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-402">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="aa72e-403">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-403">The InitialCreate class</span></span>

<span data-ttu-id="aa72e-404">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-404">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="aa72e-405">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="aa72e-405">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="aa72e-406">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="aa72e-406">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="aa72e-407">Testen der App</span><span class="sxs-lookup"><span data-stu-id="aa72e-407">Test the app</span></span>

* <span data-ttu-id="aa72e-408">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-408">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="aa72e-409">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-409">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-410">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-410">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-411">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-411">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="aa72e-412">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-412">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="aa72e-413">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-413">Test the **Create** page.</span></span> <span data-ttu-id="aa72e-414">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="aa72e-414">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="aa72e-415">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-415">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="aa72e-416">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-416">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="aa72e-417">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="aa72e-417">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="aa72e-418">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-418">Test the **Edit** , **Details** , and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="aa72e-419">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="aa72e-419">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-420">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-420">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-421">Öffnen Sie die Datei *Controllers/MoviesController.cs* , und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="aa72e-421">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="aa72e-422">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-422">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="aa72e-423">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-423">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-424">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-424">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="aa72e-425">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="aa72e-426">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="aa72e-427">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="aa72e-427">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="aa72e-428">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-428">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="aa72e-429">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="aa72e-429">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="aa72e-430">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="aa72e-430">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="aa72e-431">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="aa72e-431">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="aa72e-432">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="aa72e-432">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="aa72e-433">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-433">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="aa72e-434">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-434">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="aa72e-435">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-435">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="aa72e-436">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-436">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="aa72e-437">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-437">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="aa72e-438">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-438">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="aa72e-439">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="aa72e-439">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="aa72e-440">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-440">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="aa72e-441">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-441">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="aa72e-442">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-442">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="aa72e-443">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-443">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="aa72e-444">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-444">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="aa72e-445">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-445">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="aa72e-446">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-446">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="aa72e-447">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-447">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="aa72e-448">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-448">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="aa72e-449">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="aa72e-449">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="aa72e-450">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="aa72e-450">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="aa72e-451">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-451">The `Model` object is strongly typed.</span></span> <span data-ttu-id="aa72e-452">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-452">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="aa72e-453">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-453">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="aa72e-454">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-454">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="aa72e-455">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-455">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="aa72e-456">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="aa72e-456">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="aa72e-457">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-457">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="aa72e-458">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-458">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="aa72e-459">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-459">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="aa72e-460">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-460">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="aa72e-461">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="aa72e-461">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aa72e-462">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aa72e-462">Additional resources</span></span>

* [<span data-ttu-id="aa72e-463">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="aa72e-463">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="aa72e-464">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="aa72e-464">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="aa72e-465">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="aa72e-465">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="aa72e-466">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="aa72e-466">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-467">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-467">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-468">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-468">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="aa72e-469">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-469">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-470">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-470">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="aa72e-471">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-471">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="aa72e-472">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="aa72e-472">Scaffold the movie model</span></span>

<span data-ttu-id="aa72e-473">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-473">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="aa72e-474">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-474">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-475">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-475">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-476">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="aa72e-476">In **Solution Explorer** , right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="aa72e-478">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-478">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="aa72e-480">Bearbeiten Sie das Dialogfeld **Controller hinzufügen** :</span><span class="sxs-lookup"><span data-stu-id="aa72e-480">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="aa72e-481">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="aa72e-481">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="aa72e-482">**Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-482">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* <span data-ttu-id="aa72e-484">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-484">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="aa72e-485">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="aa72e-485">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="aa72e-486">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-486">Select **Add**</span></span>

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

<span data-ttu-id="aa72e-488">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="aa72e-488">Visual Studio creates:</span></span>

* <span data-ttu-id="aa72e-489">Eine Entity Framework Core- [Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-489">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) ( *Data/MvcMovieContext.cs* )</span></span>
* <span data-ttu-id="aa72e-490">Einen Movies-Controller ( *Controllers/MoviesController.cs* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-490">A movies controller ( *Controllers/MoviesController.cs* )</span></span>
* <span data-ttu-id="aa72e-491">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ ( *Views/Movies/\*.cshtml* )</span><span class="sxs-lookup"><span data-stu-id="aa72e-491">Razor view files for Create, Delete, Details, Edit, and Index pages ( *Views/Movies/\*.cshtml* )</span></span>

<span data-ttu-id="aa72e-492">Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-492">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="aa72e-493">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa72e-493">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="aa72e-494">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-494">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="aa72e-495">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="aa72e-495">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="aa72e-496">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="aa72e-496">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="aa72e-497">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-497">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-498">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-498">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aa72e-499">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs* , *Startup.cs* , und *CSPROJ* ).</span><span class="sxs-lookup"><span data-stu-id="aa72e-499">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="aa72e-500">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="aa72e-500">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="aa72e-501">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa72e-501">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="aa72e-502">Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:</span><span class="sxs-lookup"><span data-stu-id="aa72e-502">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-503">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-503">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-504">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-504">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="aa72e-505">Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="aa72e-505">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="aa72e-506">Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-506">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="aa72e-507">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="aa72e-507">Initial migration</span></span>

<span data-ttu-id="aa72e-508">In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-508">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="aa72e-509">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="aa72e-509">Add an initial migration.</span></span>
* <span data-ttu-id="aa72e-510">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="aa72e-510">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-511">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-511">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="aa72e-512">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="aa72e-512">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="aa72e-514">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="aa72e-514">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="aa72e-515">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-515">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="aa72e-516">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-516">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="aa72e-517">Das `Initial`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-517">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="aa72e-518">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-518">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="aa72e-519">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="aa72e-519">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="aa72e-520">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-520">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-521">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-521">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="aa72e-522">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-522">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="aa72e-523">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs* ) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="aa72e-523">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="aa72e-524">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="aa72e-524">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="aa72e-525">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-525">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="aa72e-526">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="aa72e-526">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="aa72e-527">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-527">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="aa72e-528">Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-528">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="aa72e-529">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-529">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="aa72e-530">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-530">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aa72e-531">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa72e-531">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="aa72e-532">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-532">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="aa72e-533">Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="aa72e-533">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="aa72e-534">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-534">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="aa72e-535">Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="aa72e-535">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="aa72e-536">Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-536">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="aa72e-537">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="aa72e-537">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="aa72e-538">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="aa72e-538">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="aa72e-539">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="aa72e-539">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="aa72e-540">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-540">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="aa72e-541">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-541">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="aa72e-542">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="aa72e-542">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aa72e-543">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa72e-543">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="aa72e-544">Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-544">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="aa72e-545">Testen der App</span><span class="sxs-lookup"><span data-stu-id="aa72e-545">Test the app</span></span>

* <span data-ttu-id="aa72e-546">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="aa72e-546">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="aa72e-547">Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-547">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="aa72e-548">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="aa72e-548">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="aa72e-549">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-549">Test the **Create** link.</span></span> <span data-ttu-id="aa72e-550">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="aa72e-550">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="aa72e-551">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-551">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="aa72e-552">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-552">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="aa72e-553">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="aa72e-553">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="aa72e-554">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="aa72e-554">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="aa72e-555">Überprüfen Sie die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="aa72e-555">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="aa72e-556">Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="aa72e-556">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="aa72e-557">`services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.</span><span class="sxs-lookup"><span data-stu-id="aa72e-557">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="aa72e-558">`=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="aa72e-558">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="aa72e-559">Öffnen Sie die Datei *Controllers/MoviesController.cs* , und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="aa72e-559">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="aa72e-560">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-560">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="aa72e-561">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-561">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="aa72e-562">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="aa72e-562">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="aa72e-563">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="aa72e-563">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="aa72e-564">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-564">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="aa72e-565">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-565">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="aa72e-566">Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="aa72e-566">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="aa72e-567">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="aa72e-567">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="aa72e-568">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-568">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="aa72e-569">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="aa72e-569">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="aa72e-570">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="aa72e-570">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="aa72e-571">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-571">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="aa72e-572">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-572">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="aa72e-573">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="aa72e-573">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="aa72e-574">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-574">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="aa72e-575">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-575">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="aa72e-576">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="aa72e-576">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="aa72e-577">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="aa72e-577">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="aa72e-578">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="aa72e-578">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="aa72e-579">Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="aa72e-579">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="aa72e-580">Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-580">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="aa72e-581">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir.</span><span class="sxs-lookup"><span data-stu-id="aa72e-581">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="aa72e-582">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-582">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="aa72e-583">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-583">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="aa72e-584">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="aa72e-584">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="aa72e-585">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="aa72e-585">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="aa72e-586">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="aa72e-586">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="aa72e-587">Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-587">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="aa72e-588">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="aa72e-588">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="aa72e-589">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="aa72e-589">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="aa72e-590">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="aa72e-590">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="aa72e-591">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:</span><span class="sxs-lookup"><span data-stu-id="aa72e-591">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="aa72e-592">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aa72e-592">Additional resources</span></span>

* [<span data-ttu-id="aa72e-593">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="aa72e-593">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="aa72e-594">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="aa72e-594">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="aa72e-595">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit einer Datenbank](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="aa72e-595">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
