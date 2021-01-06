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
ms.openlocfilehash: bfda45afeea67a11ad775996d94a06125df08bc6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854586"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="749e4-103">Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="749e4-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="749e4-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="749e4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="749e4-105">In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="749e4-106">Diese Klassen bilden den „**M** odell“-Teil der **M** VC-App.</span><span class="sxs-lookup"><span data-stu-id="749e4-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="749e4-107">Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="749e4-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="749e4-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="749e4-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="749e4-109">Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P** lain **O** ld **C** LR **O** bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen.</span><span class="sxs-lookup"><span data-stu-id="749e4-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="749e4-110">Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="749e4-111">In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="749e4-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="749e4-112">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="749e4-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-114">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="749e4-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="749e4-115">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="749e4-117">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="749e4-119">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="749e4-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="749e4-120">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="749e4-121">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="749e4-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="749e4-122">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="749e4-123">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="749e4-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="749e4-124">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="749e4-124">With this attribute:</span></span>

* <span data-ttu-id="749e4-125">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="749e4-126">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="749e4-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="749e4-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="749e4-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="749e4-128">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="749e4-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-130">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="749e4-132">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="749e4-133">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="749e4-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="749e4-134">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="749e4-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="749e4-135">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="749e4-138">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="749e4-139">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="749e4-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="749e4-140">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="749e4-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="749e4-142">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="749e4-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="749e4-143">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="749e4-143">Press the **Ok** button.</span></span>

<span data-ttu-id="749e4-144">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="749e4-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="749e4-145">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="749e4-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="749e4-146">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="749e4-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="749e4-147">Führen Sie den folgenden .NET-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="749e4-148">Durch den vorangehenden Befehl wird das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="749e4-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="749e4-149">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="749e4-149">Create a database context class</span></span>

<span data-ttu-id="749e4-150">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="749e4-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="749e4-151">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="749e4-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="749e4-152">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="749e4-152">Create a *Data* folder.</span></span>

<span data-ttu-id="749e4-153">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="749e4-154">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="749e4-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="749e4-155">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="749e4-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="749e4-156">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="749e4-157">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="749e4-157">Register the database context</span></span>

<span data-ttu-id="749e4-158">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="749e4-159">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="749e4-160">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="749e4-161">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="749e4-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="749e4-162">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="749e4-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="749e4-163">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="749e4-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="749e4-164">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-166">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="749e4-167">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="749e4-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="749e4-168">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="749e4-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="749e4-169">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="749e4-169">Add a database connection string</span></span>

<span data-ttu-id="749e4-170">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-172">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

<span data-ttu-id="749e4-173">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="749e4-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="749e4-174">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="749e4-174">Scaffold movie pages</span></span>

<span data-ttu-id="749e4-175">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-177">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="749e4-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="749e4-179">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="749e4-181">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="749e4-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="749e4-182">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="749e4-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="749e4-183">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="749e4-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc5.png)

* <span data-ttu-id="749e4-185">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="749e4-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="749e4-186">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="749e4-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="749e4-187">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-187">Select **Add**</span></span>

<span data-ttu-id="749e4-188">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="749e4-188">Visual Studio creates:</span></span>

* <span data-ttu-id="749e4-189">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="749e4-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="749e4-190">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="749e4-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="749e4-191">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="749e4-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="749e4-193">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="749e4-194">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="749e4-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="749e4-195">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="749e4-197">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="749e4-198">Exportieren Sie den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="749e4-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="749e4-199">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="749e4-200">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="749e4-201">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="749e4-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="749e4-202">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="749e4-202">Initial migration</span></span>

<span data-ttu-id="749e4-203">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="749e4-204">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="749e4-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-206">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="749e4-207">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="749e4-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="749e4-208">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="749e4-209">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="749e4-210">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="749e4-211">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="749e4-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="749e4-212">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="749e4-213">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="749e4-214">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="749e4-215">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="749e4-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="749e4-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="749e4-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="749e4-217">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="749e4-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="749e4-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="749e4-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="749e4-219">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="749e4-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-220">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="749e4-221">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="749e4-222">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="749e4-223">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="749e4-224">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="749e4-225">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="749e4-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="749e4-226">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="749e4-227">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="749e4-228">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="749e4-229">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="749e4-229">The InitialCreate class</span></span>

<span data-ttu-id="749e4-230">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="749e4-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="749e4-231">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="749e4-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="749e4-232">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="749e4-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="749e4-233">Testen der App</span><span class="sxs-lookup"><span data-stu-id="749e4-233">Test the app</span></span>

* <span data-ttu-id="749e4-234">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="749e4-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="749e4-235">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="749e4-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-237">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="749e4-238">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="749e4-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="749e4-239">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="749e4-239">Test the **Create** page.</span></span> <span data-ttu-id="749e4-240">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="749e4-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="749e4-241">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="749e4-242">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="749e4-243">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="749e4-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="749e4-244">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="749e4-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="749e4-245">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="749e4-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-247">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="749e4-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="749e4-248">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="749e4-249">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="749e4-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-250">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="749e4-251">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="749e4-252">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="749e4-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="749e4-253">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="749e4-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="749e4-254">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="749e4-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="749e4-255">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="749e4-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="749e4-256">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="749e4-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="749e4-257">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="749e4-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="749e4-258">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="749e4-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="749e4-259">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="749e4-260">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="749e4-261">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="749e4-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="749e4-262">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="749e4-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="749e4-263">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="749e4-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="749e4-264">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="749e4-265">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="749e4-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="749e4-266">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="749e4-267">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="749e4-268">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="749e4-269">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="749e4-270">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="749e4-271">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="749e4-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="749e4-272">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="749e4-273">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="749e4-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="749e4-274">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="749e4-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="749e4-275">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="749e4-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="749e4-276">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="749e4-277">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="749e4-278">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="749e4-279">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="749e4-280">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="749e4-281">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="749e4-282">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="749e4-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="749e4-283">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="749e4-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="749e4-284">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="749e4-285">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="749e4-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="749e4-286">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="749e4-287">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="749e4-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749e4-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="749e4-288">Additional resources</span></span>

* [<span data-ttu-id="749e4-289">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="749e4-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="749e4-290">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="749e4-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="749e4-291">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="749e4-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="749e4-292">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="749e4-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-294">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="749e4-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="749e4-295">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="749e4-297">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-298">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="749e4-299">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="749e4-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="749e4-300">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="749e4-301">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="749e4-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="749e4-302">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="749e4-303">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="749e4-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="749e4-304">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="749e4-304">With this attribute:</span></span>

* <span data-ttu-id="749e4-305">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="749e4-306">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="749e4-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="749e4-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="749e4-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="749e4-308">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="749e4-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-310">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="749e4-312">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="749e4-313">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="749e4-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="749e4-314">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="749e4-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="749e4-315">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-317">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="749e4-318">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="749e4-319">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="749e4-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="749e4-320">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="749e4-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="749e4-322">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="749e4-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="749e4-323">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="749e4-323">Press the **Ok** button.</span></span>

<span data-ttu-id="749e4-324">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="749e4-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="749e4-325">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="749e4-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="749e4-326">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="749e4-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="749e4-327">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="749e4-327">Create a database context class</span></span>

<span data-ttu-id="749e4-328">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="749e4-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="749e4-329">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="749e4-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="749e4-330">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="749e4-330">Create a *Data* folder.</span></span>

<span data-ttu-id="749e4-331">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="749e4-332">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="749e4-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="749e4-333">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="749e4-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="749e4-334">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="749e4-335">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="749e4-335">Register the database context</span></span>

<span data-ttu-id="749e4-336">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="749e4-337">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="749e4-338">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="749e4-339">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="749e4-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="749e4-340">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="749e4-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="749e4-341">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="749e4-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="749e4-342">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-344">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="749e4-345">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="749e4-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="749e4-346">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="749e4-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="749e4-347">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="749e4-347">Add a database connection string</span></span>

<span data-ttu-id="749e4-348">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="749e4-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-350">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="749e4-351">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="749e4-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="749e4-352">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="749e4-352">Scaffold movie pages</span></span>

<span data-ttu-id="749e4-353">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-355">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="749e4-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="749e4-357">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="749e4-359">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="749e4-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="749e4-360">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="749e4-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="749e4-361">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="749e4-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* <span data-ttu-id="749e4-363">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="749e4-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="749e4-364">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="749e4-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="749e4-365">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-365">Select **Add**</span></span>

<span data-ttu-id="749e4-366">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="749e4-366">Visual Studio creates:</span></span>

* <span data-ttu-id="749e4-367">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="749e4-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="749e4-368">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="749e4-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="749e4-369">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="749e4-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="749e4-371">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="749e4-372">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="749e4-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="749e4-373">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-374">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="749e4-375">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="749e4-376">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="749e4-377">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="749e4-378">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="749e4-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="749e4-379">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="749e4-379">Initial migration</span></span>

<span data-ttu-id="749e4-380">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="749e4-381">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="749e4-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-383">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="749e4-384">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="749e4-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="749e4-385">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="749e4-386">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="749e4-387">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="749e4-388">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="749e4-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="749e4-389">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="749e4-390">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="749e4-391">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="749e4-392">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="749e4-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="749e4-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="749e4-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="749e4-394">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="749e4-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="749e4-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="749e4-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="749e4-396">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="749e4-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-397">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="749e4-398">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="749e4-399">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="749e4-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="749e4-400">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="749e4-401">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="749e4-402">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="749e4-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="749e4-403">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="749e4-404">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="749e4-405">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="749e4-406">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="749e4-406">The InitialCreate class</span></span>

<span data-ttu-id="749e4-407">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="749e4-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="749e4-408">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="749e4-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="749e4-409">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="749e4-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="749e4-410">Testen der App</span><span class="sxs-lookup"><span data-stu-id="749e4-410">Test the app</span></span>

* <span data-ttu-id="749e4-411">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="749e4-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="749e4-412">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="749e4-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-414">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="749e4-415">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="749e4-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="749e4-416">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="749e4-416">Test the **Create** page.</span></span> <span data-ttu-id="749e4-417">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="749e4-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="749e4-418">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="749e4-419">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="749e4-420">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="749e4-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="749e4-421">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="749e4-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="749e4-422">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="749e4-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-424">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="749e4-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="749e4-425">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="749e4-426">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="749e4-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-427">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="749e4-428">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="749e4-429">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="749e4-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="749e4-430">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="749e4-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="749e4-431">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="749e4-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="749e4-432">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="749e4-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="749e4-433">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="749e4-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="749e4-434">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="749e4-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="749e4-435">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="749e4-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="749e4-436">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="749e4-437">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="749e4-438">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="749e4-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="749e4-439">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="749e4-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="749e4-440">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="749e4-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="749e4-441">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="749e4-442">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="749e4-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="749e4-443">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="749e4-444">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="749e4-445">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="749e4-446">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="749e4-447">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="749e4-448">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="749e4-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="749e4-449">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="749e4-450">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="749e4-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="749e4-451">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="749e4-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="749e4-452">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="749e4-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="749e4-453">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="749e4-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="749e4-454">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="749e4-455">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="749e4-456">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="749e4-457">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="749e4-458">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="749e4-459">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="749e4-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="749e4-460">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="749e4-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="749e4-461">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="749e4-462">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="749e4-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="749e4-463">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="749e4-464">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="749e4-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749e4-465">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="749e4-465">Additional resources</span></span>

* [<span data-ttu-id="749e4-466">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="749e4-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="749e4-467">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="749e4-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="749e4-468">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="749e4-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="749e4-469">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="749e4-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-471">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="749e4-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="749e4-472">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="749e4-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-473">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="749e4-474">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="749e4-475">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="749e4-475">Scaffold the movie model</span></span>

<span data-ttu-id="749e4-476">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="749e4-477">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-479">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="749e4-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="749e4-481">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="749e4-483">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="749e4-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="749e4-484">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="749e4-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="749e4-485">**Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* <span data-ttu-id="749e4-487">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="749e4-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="749e4-488">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="749e4-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="749e4-489">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-489">Select **Add**</span></span>

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

<span data-ttu-id="749e4-491">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="749e4-491">Visual Studio creates:</span></span>

* <span data-ttu-id="749e4-492">Eine Entity Framework Core-[Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="749e4-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="749e4-493">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="749e4-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="749e4-494">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="749e4-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="749e4-495">Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="749e4-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="749e4-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="749e4-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="749e4-497">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="749e4-498">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="749e4-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="749e4-499">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="749e4-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="749e4-500">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="749e4-501">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="749e4-502">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="749e4-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="749e4-503">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="749e4-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="749e4-504">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="749e4-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="749e4-505">Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:</span><span class="sxs-lookup"><span data-stu-id="749e4-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-507">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="749e4-508">Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="749e4-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="749e4-509">Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.</span><span class="sxs-lookup"><span data-stu-id="749e4-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="749e4-510">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="749e4-510">Initial migration</span></span>

<span data-ttu-id="749e4-511">In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="749e4-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="749e4-512">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="749e4-512">Add an initial migration.</span></span>
* <span data-ttu-id="749e4-513">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="749e4-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="749e4-515">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="749e4-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="749e4-517">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="749e4-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="749e4-518">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="749e4-519">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="749e4-520">Das `Initial`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="749e4-521">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="749e4-522">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="749e4-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="749e4-523">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-524">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="749e4-525">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="749e4-526">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="749e4-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="749e4-527">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="749e4-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="749e4-528">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="749e4-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="749e4-529">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="749e4-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="749e4-530">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="749e4-531">Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="749e4-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="749e4-532">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="749e4-533">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="749e4-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="749e4-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="749e4-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="749e4-535">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="749e4-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="749e4-536">Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="749e4-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="749e4-537">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="749e4-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="749e4-538">Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="749e4-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="749e4-539">Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="749e4-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="749e4-540">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="749e4-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="749e4-541">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="749e4-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="749e4-542">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="749e4-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="749e4-543">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="749e4-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="749e4-544">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="749e4-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="749e4-545">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="749e4-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="749e4-546">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="749e4-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="749e4-547">Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="749e4-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="749e4-548">Testen der App</span><span class="sxs-lookup"><span data-stu-id="749e4-548">Test the app</span></span>

* <span data-ttu-id="749e4-549">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="749e4-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="749e4-550">Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="749e4-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="749e4-551">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="749e4-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="749e4-552">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="749e4-552">Test the **Create** link.</span></span> <span data-ttu-id="749e4-553">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="749e4-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="749e4-554">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="749e4-555">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="749e4-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="749e4-556">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="749e4-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="749e4-557">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="749e4-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="749e4-558">Überprüfen Sie die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="749e4-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="749e4-559">Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="749e4-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="749e4-560">`services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.</span><span class="sxs-lookup"><span data-stu-id="749e4-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="749e4-561">`=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="749e4-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="749e4-562">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="749e4-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="749e4-563">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="749e4-564">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="749e4-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="749e4-565">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="749e4-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="749e4-566">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="749e4-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="749e4-567">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="749e4-568">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="749e4-569">Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="749e4-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="749e4-570">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="749e4-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="749e4-571">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="749e4-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="749e4-572">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="749e4-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="749e4-573">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="749e4-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="749e4-574">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="749e4-575">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="749e4-576">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="749e4-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="749e4-577">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="749e4-578">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="749e4-579">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="749e4-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="749e4-580">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="749e4-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="749e4-581">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="749e4-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="749e4-582">Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="749e4-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="749e4-583">Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="749e4-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="749e4-584">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir.</span><span class="sxs-lookup"><span data-stu-id="749e4-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="749e4-585">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="749e4-586">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="749e4-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="749e4-587">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="749e4-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="749e4-588">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="749e4-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="749e4-589">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="749e4-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="749e4-590">Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="749e4-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="749e4-591">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="749e4-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="749e4-592">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="749e4-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="749e4-593">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="749e4-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="749e4-594">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:</span><span class="sxs-lookup"><span data-stu-id="749e4-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="749e4-595">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="749e4-595">Additional resources</span></span>

* [<span data-ttu-id="749e4-596">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="749e4-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="749e4-597">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="749e4-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="749e4-598">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit einer Datenbank](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="749e4-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
