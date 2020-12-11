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
ms.openlocfilehash: d99d5bfd84c8c7e7d0c479964211591be434330c
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855520"
---
# <a name="part-4-add-a-model-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d8e9b-103">Teil 4: Hinzufügen eines Modells zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="d8e9b-103">Part 4, add a model to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d8e9b-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="d8e9b-105">In diesem Abschnitt fügen Sie die Klassen für die Verwaltung von Filmen in einer Datenbank hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-105">In this section, you add classes for managing movies in a database.</span></span> <span data-ttu-id="d8e9b-106">Diese Klassen bilden den „**M** odell“-Teil der **M** VC-App.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-106">These classes will be the "**M** odel" part of the **M** VC app.</span></span>

<span data-ttu-id="d8e9b-107">Verwenden Sie diese Klassen mit [Entity Framework Core](/ef/core) (EF Core), um mit einer Datenbank zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-107">You use these classes with [Entity Framework Core](/ef/core) (EF Core) to work with a database.</span></span> <span data-ttu-id="d8e9b-108">EF Core ist ein ORM-Framework (Objektrelationales Mapping, ORM), das den Datenzugriffscode vereinfacht, den Sie schreiben müssen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-108">EF Core is an object-relational mapping (ORM) framework that simplifies the data access code that you have to write.</span></span>

<span data-ttu-id="d8e9b-109">Die Modellklassen, die Sie erstellen, werden als POCO-Klassen (von **P** lain **O** ld **C** LR **O** bjects) bezeichnet, da sie nicht über eine Abhängigkeit von EF Core verfügen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-109">The model classes you create are known as POCO classes (from **P** lain **O** ld **C** LR **O** bjects) because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="d8e9b-110">Sie definieren lediglich die Eigenschaften der Daten, die in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-110">They just define the properties of the data that will be stored in the database.</span></span>

<span data-ttu-id="d8e9b-111">In diesem Tutorial schreiben Sie zuerst die Modellklassen. Anschließend erstellt EF Core die Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-111">In this tutorial, you write the model classes first, and EF Core creates the database.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d8e9b-112">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-112">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-114">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-114">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d8e9b-115">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-115">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8e9b-117">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-117">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8e9b-119">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-119">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="d8e9b-120">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-120">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="d8e9b-121">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-121">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="d8e9b-122">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-122">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="d8e9b-123">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-123">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d8e9b-124">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-124">With this attribute:</span></span>

* <span data-ttu-id="d8e9b-125">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-125">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="d8e9b-126">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-126">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d8e9b-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-127">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="d8e9b-128">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="d8e9b-128">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-130">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-130">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d8e9b-132">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-132">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="d8e9b-133">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-133">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="d8e9b-134">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-134">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="d8e9b-135">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-135">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-137">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-137">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8e9b-138">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-138">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="d8e9b-139">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-139">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="d8e9b-140">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-140">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="d8e9b-142">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-142">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="d8e9b-143">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-143">Press the **Ok** button.</span></span>

<span data-ttu-id="d8e9b-144">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-144">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="d8e9b-145">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-145">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="d8e9b-146">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-146">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

<span data-ttu-id="d8e9b-147">Führen Sie den folgenden .NET-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-147">Run the following .NET CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-aspnet-codegenerator
```

<span data-ttu-id="d8e9b-148">Durch den vorangehenden Befehl wird das [Gerüstbautool „aspnet-codegenerator“](xref:fundamentals/tools/dotnet-aspnet-codegenerator) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-148">The preceding command adds the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="d8e9b-149">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-149">Create a database context class</span></span>

<span data-ttu-id="d8e9b-150">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-150">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="d8e9b-151">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-151">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="d8e9b-152">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-152">Create a *Data* folder.</span></span>

<span data-ttu-id="d8e9b-153">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-153">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="d8e9b-154">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-154">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d8e9b-155">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-155">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d8e9b-156">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-156">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="d8e9b-157">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d8e9b-157">Register the database context</span></span>

<span data-ttu-id="d8e9b-158">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-158">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8e9b-159">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-159">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="d8e9b-160">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-160">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8e9b-161">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-161">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="d8e9b-162">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-162">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="d8e9b-163">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-163">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d8e9b-164">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-164">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-165">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-166">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-166">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

<span data-ttu-id="d8e9b-167">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-167">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8e9b-168">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-168">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="d8e9b-169">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8e9b-169">Add a database connection string</span></span>

<span data-ttu-id="d8e9b-170">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-170">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-171">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-171">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-11)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-172">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-172">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-11)]

---

<span data-ttu-id="d8e9b-173">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-173">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="d8e9b-174">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="d8e9b-174">Scaffold movie pages</span></span>

<span data-ttu-id="d8e9b-175">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-175">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-176">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-177">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-177">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="d8e9b-179">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-179">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d8e9b-181">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-181">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d8e9b-182">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="d8e9b-182">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d8e9b-183">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="d8e9b-183">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc5.png)

* <span data-ttu-id="d8e9b-185">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-185">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d8e9b-186">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-186">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d8e9b-187">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-187">Select **Add**</span></span>

<span data-ttu-id="d8e9b-188">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-188">Visual Studio creates:</span></span>

* <span data-ttu-id="d8e9b-189">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-189">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d8e9b-190">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-190">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d8e9b-191">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-191">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-192">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-192">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="d8e9b-193">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-193">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d8e9b-194">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-194">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d8e9b-195">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-195">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-196">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-196">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8e9b-197">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-197">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d8e9b-198">Exportieren Sie den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-198">Export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d8e9b-199">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-199">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="d8e9b-200">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-200">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="d8e9b-201">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-201">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="d8e9b-202">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="d8e9b-202">Initial migration</span></span>

<span data-ttu-id="d8e9b-203">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-203">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="d8e9b-204">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-204">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-205">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-205">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-206">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-206">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="d8e9b-207">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-207">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="d8e9b-208">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-208">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d8e9b-209">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-209">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-210">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-210">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d8e9b-211">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-211">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d8e9b-212">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-212">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="d8e9b-213">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-213">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d8e9b-214">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-214">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="d8e9b-215">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-215">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="d8e9b-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-216">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d8e9b-217">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-217">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d8e9b-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-218">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="d8e9b-219">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-219">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-220">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d8e9b-221">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-221">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="d8e9b-222">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-222">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d8e9b-223">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-223">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-224">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-224">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d8e9b-225">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-225">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d8e9b-226">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-226">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="d8e9b-227">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-227">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d8e9b-228">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-228">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="d8e9b-229">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-229">The InitialCreate class</span></span>

<span data-ttu-id="d8e9b-230">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-230">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="d8e9b-231">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-231">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="d8e9b-232">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-232">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d8e9b-233">Testen der App</span><span class="sxs-lookup"><span data-stu-id="d8e9b-233">Test the app</span></span>

* <span data-ttu-id="d8e9b-234">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-234">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="d8e9b-235">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-235">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-236">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-236">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-237">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-237">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="d8e9b-238">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-238">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="d8e9b-239">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-239">Test the **Create** page.</span></span> <span data-ttu-id="d8e9b-240">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-240">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d8e9b-241">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-241">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d8e9b-242">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-242">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d8e9b-243">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-243">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d8e9b-244">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-244">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="d8e9b-245">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="d8e9b-245">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-247">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-247">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d8e9b-248">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-248">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d8e9b-249">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-249">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-250">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-250">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d8e9b-251">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-251">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d8e9b-252">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-252">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d8e9b-253">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="d8e9b-253">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d8e9b-254">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-254">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d8e9b-255">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-255">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d8e9b-256">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-256">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d8e9b-257">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="d8e9b-257">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d8e9b-258">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-258">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d8e9b-259">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-259">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d8e9b-260">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-260">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d8e9b-261">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-261">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="d8e9b-262">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-262">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="d8e9b-263">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-263">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d8e9b-264">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-264">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d8e9b-265">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-265">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d8e9b-266">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-266">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d8e9b-267">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-267">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d8e9b-268">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-268">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d8e9b-269">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-269">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d8e9b-270">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-270">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d8e9b-271">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-271">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d8e9b-272">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-272">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="d8e9b-273">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-273">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d8e9b-274">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-274">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="d8e9b-275">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-275">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d8e9b-276">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-276">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="d8e9b-277">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-277">The `Model` object is strongly typed.</span></span> <span data-ttu-id="d8e9b-278">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-278">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d8e9b-279">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-279">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d8e9b-280">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-280">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d8e9b-281">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-281">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d8e9b-282">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-282">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d8e9b-283">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-283">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d8e9b-284">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-284">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d8e9b-285">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-285">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d8e9b-286">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-286">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d8e9b-287">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-287">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8e9b-288">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8e9b-288">Additional resources</span></span>

* [<span data-ttu-id="d8e9b-289">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="d8e9b-289">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d8e9b-290">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="d8e9b-290">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d8e9b-291">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-291">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d8e9b-292">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-292">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-293">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-293">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-294">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-294">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d8e9b-295">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-295">Name the file *Movie.cs*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-296">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-296">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d8e9b-297">Fügen Sie dem Ordner *Models* eine Datei mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-297">Add a file named *Movie.cs* to the *Models* folder.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-298">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-298">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8e9b-299">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle* > **Hinzufügen** > **Neue Klasse** > **Leere Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-299">Right-click the *Models* folder > **Add** > **New Class** > **Empty Class**.</span></span> <span data-ttu-id="d8e9b-300">Nennen Sie die Datei *Movie.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-300">Name the file *Movie.cs*.</span></span>

---

<span data-ttu-id="d8e9b-301">Aktualisieren Sie die Datei *Movie.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-301">Update the *Movie.cs* file with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

<span data-ttu-id="d8e9b-302">Die `Movie`-Klasse enthält ein `Id`-Feld, das von der Datenbank für den Primärschlüssel benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-302">The `Movie` class contains an `Id` field, which is required by the database for the primary key.</span></span>

<span data-ttu-id="d8e9b-303">Das <xref:System.ComponentModel.DataAnnotations.DataType>-Attribut für `ReleaseDate` gibt den Typ der Daten (`Date`) an.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-303">The <xref:System.ComponentModel.DataAnnotations.DataType> attribute on `ReleaseDate` specifies the type of the data (`Date`).</span></span> <span data-ttu-id="d8e9b-304">Mit diesem Attribut:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-304">With this attribute:</span></span>

* <span data-ttu-id="d8e9b-305">Es ist nicht erforderlich, dass der Benutzer Zeitinformationen in das Datumsfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-305">The user is not required to enter time information in the date field.</span></span>
* <span data-ttu-id="d8e9b-306">Nur das Datum wird angezeigt, keine Zeitinformationen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-306">Only the date is displayed, not time information.</span></span>

<span data-ttu-id="d8e9b-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) werden in einem späteren Tutorial behandelt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-307">[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) are covered in a later tutorial.</span></span>

## <a name="add-nuget-packages"></a><span data-ttu-id="d8e9b-308">Hinzufügen von NuGet-Paketen</span><span class="sxs-lookup"><span data-stu-id="d8e9b-308">Add NuGet packages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-309">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-309">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-310">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-310">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="d8e9b-312">Führen Sie den folgenden Befehl in der PMC aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-312">In the PMC, run the following command:</span></span>

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="d8e9b-313">Mit dem vorangehenden Befehl wird der EF Core SQL Server-Anbieter hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-313">The preceding command adds the EF Core SQL Server provider.</span></span> <span data-ttu-id="d8e9b-314">Das Anbieterpaket installiert das EF Core-Paket als Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-314">The provider package installs the EF Core package as a dependency.</span></span> <span data-ttu-id="d8e9b-315">Weitere Pakete werden im weiteren Verlauf des Tutorials automatisch im Gerüstbauschritt installiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-315">Additional packages are installed automatically in the scaffolding step later in the tutorial.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-317">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-317">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d8e9b-318">Wählen Sie im Menü **Projekt** die Option **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-318">From the **Project** menu, select **Manage NuGet Packages**.</span></span>

<span data-ttu-id="d8e9b-319">Geben Sie in das Feld **Suchen** in der oberen rechten Ecke `Microsoft.EntityFrameworkCore.SQLite` ein, und drücken Sie die **EINGABETASTE**, um zu suchen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-319">In the **Search** field in the upper right, enter `Microsoft.EntityFrameworkCore.SQLite` and press the **Return** key to search.</span></span> <span data-ttu-id="d8e9b-320">Wählen Sie das entsprechende NuGet-Paket aus, und klicken Sie auf die Schaltfläche **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-320">Select the matching NuGet package and press the **Add Package** button.</span></span>

![NuGet-Paket für Entity Framework Core hinzufügen](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

<span data-ttu-id="d8e9b-322">Das Dialogfeld **Projekte auswählen** wird angezeigt, und das Projekt `MvcMovie` ist ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-322">The **Select Projects** dialog will be displayed, with the `MvcMovie` project selected.</span></span> <span data-ttu-id="d8e9b-323">Klicken Sie auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-323">Press the **Ok** button.</span></span>

<span data-ttu-id="d8e9b-324">Ein Dialogfeld **Zustimmen zur Lizenz** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-324">A **License Acceptance** dialog will be displayed.</span></span> <span data-ttu-id="d8e9b-325">Überprüfen Sie die Lizenzen nach Wunsch, und klicken Sie dann auf die Schaltfläche **Akzeptieren**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-325">Review the licenses as desired, then click the **Accept** button.</span></span>

<span data-ttu-id="d8e9b-326">Wiederholen Sie die Schritte oben, um die folgenden NuGet-Pakete zu installieren:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-326">Repeat the above steps to install the following NuGet packages:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a><span data-ttu-id="d8e9b-327">Erstellen einer Datenbankkontextklasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-327">Create a database context class</span></span>

<span data-ttu-id="d8e9b-328">Eine Datenbankkontextklasse ist erforderlich, um EF Core-Funktionalität (Erstellen, Lesen, Aktualisieren, Löschen) für das `Movie`-Modell zu koordinieren.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-328">A database context class is needed to coordinate EF Core functionality (Create, Read, Update, Delete) for the `Movie` model.</span></span> <span data-ttu-id="d8e9b-329">Der Datenbankkontext wird von [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet und gibt die Entitäten an, die im Datenmodell enthalten sein sollen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-329">The database context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and specifies the entities to include in the data model.</span></span>

<span data-ttu-id="d8e9b-330">Erstellen Sie einen Ordner *Data*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-330">Create a *Data* folder.</span></span>

<span data-ttu-id="d8e9b-331">Fügen Sie eine Datei *Data/MvcMovieContext.cs* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-331">Add a *Data/MvcMovieContext.cs* file with the following code:</span></span> 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

<span data-ttu-id="d8e9b-332">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-332">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d8e9b-333">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-333">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d8e9b-334">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-334">An entity corresponds to a row in the table.</span></span>

<a name="reg"></a>

## <a name="register-the-database-context"></a><span data-ttu-id="d8e9b-335">Registrieren des Datenbankkontexts</span><span class="sxs-lookup"><span data-stu-id="d8e9b-335">Register the database context</span></span>

<span data-ttu-id="d8e9b-336">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-336">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8e9b-337">Dienste (z.B. der EF Core-Datenbankkontext) müssen über DI beim Anwendungsstart registriert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-337">Services (such as the EF Core DB context) must be registered with DI during application startup.</span></span> <span data-ttu-id="d8e9b-338">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-338">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8e9b-339">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-339">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span> <span data-ttu-id="d8e9b-340">In diesem Abschnitt registrieren Sie den Datenbankkontext mit dem DI-Container.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-340">In this section, you register the database context with the DI container.</span></span>

<span data-ttu-id="d8e9b-341">Fügen Sie am Anfang der Datei *Startup.cs* die folgenden `using`-Anweisungen ein.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-341">Add the following `using` statements at the top of *Startup.cs*:</span></span>

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

<span data-ttu-id="d8e9b-342">Fügen Sie folgenden hervorgehobenen Code in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-342">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-343">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-343">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-344">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-344">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

<span data-ttu-id="d8e9b-345">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8e9b-346">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a><span data-ttu-id="d8e9b-347">Hinzufügen einer Datenbank-Verbindungszeichenfolge</span><span class="sxs-lookup"><span data-stu-id="d8e9b-347">Add a database connection string</span></span>

<span data-ttu-id="d8e9b-348">Fügen Sie in der *appsettings.json* -Datei eine Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-348">Add a connection string to the *appsettings.json* file:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-349">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-349">Visual Studio</span></span>](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-13)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-350">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-350">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-13)]

---

<span data-ttu-id="d8e9b-351">Erstellen Sie das Projekt, um zu ermitteln, ob Compilerfehler vorliegen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-351">Build the project as a check for compiler errors.</span></span>

## <a name="scaffold-movie-pages"></a><span data-ttu-id="d8e9b-352">Gerüstbau der Movie-Seiten</span><span class="sxs-lookup"><span data-stu-id="d8e9b-352">Scaffold movie pages</span></span>

<span data-ttu-id="d8e9b-353">Verwenden Sie das Tool für den Gerüstbau, um CRUD-Seiten („Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen)) für das Movie-Modell zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-353">Use the scaffolding tool to produce Create, Read, Update, and Delete (CRUD) pages for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-354">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-354">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-355">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-355">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="d8e9b-357">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-357">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d8e9b-359">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-359">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d8e9b-360">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="d8e9b-360">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d8e9b-361">**Datenkontextklasse:** *MvcMovieContext (MvcMovie.Data)*</span><span class="sxs-lookup"><span data-stu-id="d8e9b-361">**Data context class:** *MvcMovieContext (MvcMovie.Data)*</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc3.png)

* <span data-ttu-id="d8e9b-363">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-363">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d8e9b-364">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-364">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d8e9b-365">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-365">Select **Add**</span></span>

<span data-ttu-id="d8e9b-366">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-366">Visual Studio creates:</span></span>

* <span data-ttu-id="d8e9b-367">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-367">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d8e9b-368">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-368">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d8e9b-369">Die automatische Erstellung dieser Dateien wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-369">The automatic creation of these files is known as *scaffolding*.</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-370">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-370">Visual Studio Code</span></span>](#tab/visual-studio-code) 

* <span data-ttu-id="d8e9b-371">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-371">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d8e9b-372">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-372">On Linux, export the scaffold tool path:</span></span>

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d8e9b-373">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-373">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-374">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-374">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8e9b-375">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-375">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="d8e9b-376">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-376">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

<span data-ttu-id="d8e9b-377">Sie können die Gerüstbauseiten noch nicht verwenden, da die Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-377">You can't use the scaffolded pages yet because the database doesn't exist.</span></span> <span data-ttu-id="d8e9b-378">Wenn Sie die App ausführen und auf den Link **Movie App** klicken, erhalten Sie eine Fehlermeldung des Typs *Datenbank kann nicht geöffnet werden* oder *Keine solche Tabelle vorhanden: Movie*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-378">If you run the app and click on the **Movie App** link, you get a *Cannot open database* or *no such table: Movie* error message.</span></span>

<a name="migration"></a>

## <a name="initial-migration"></a><span data-ttu-id="d8e9b-379">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="d8e9b-379">Initial migration</span></span>

<span data-ttu-id="d8e9b-380">Verwenden Sie das Feature [Migrations](xref:data/ef-mvc/migrations) von EF Core, um die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-380">Use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to create the database.</span></span> <span data-ttu-id="d8e9b-381">Migrations sind eine Reihe von Tools, mit denen Sie eine Datenbank erstellen und aktualisieren können, damit die Ihrem Datenmodell entspricht.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-381">Migrations is a set of tools that let you create and update a database to match your data model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-382">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-382">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-383">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-383">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

<span data-ttu-id="d8e9b-384">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-384">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

* <span data-ttu-id="d8e9b-385">`Add-Migration InitialCreate`: Generiert die Migrationsdatei *Migrations/{timestamp}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-385">`Add-Migration InitialCreate`: Generates a *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d8e9b-386">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-386">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-387">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-387">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d8e9b-388">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-388">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d8e9b-389">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-389">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span>

* <span data-ttu-id="d8e9b-390">`Update-Database`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-390">`Update-Database`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d8e9b-391">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-391">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

  <span data-ttu-id="d8e9b-392">Der Befehl zum Aktualisieren der Datenbank generiert folgende Warnung:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-392">The database update command generates the following warning:</span></span> 

  > <span data-ttu-id="d8e9b-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-393">No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="d8e9b-394">Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-394">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="d8e9b-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'. (Für die Spalte „Price“ mit Dezimalwerten für den Entitätstyp „Movie“ wurde kein Typ angegeben. Dadurch werden Werte automatisch abgeschnitten, falls diese nicht der Standardgenauigkeit und -skalierung entsprechen. Geben Sie den Spaltentyp von SQL-Server explizit an, der durch „ForHasColumnType()“ sämtliche Werte unterstützen kann.)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-395">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.</span></span>

  <span data-ttu-id="d8e9b-396">Sie können diese Warnung ignorieren, sie wird in einem späteren Tutorial behoben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-396">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-397">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-397">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d8e9b-398">Führen Sie die folgenden .NET Core-CLI-Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-398">Run the following .NET Core CLI commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* <span data-ttu-id="d8e9b-399">`ef migrations add InitialCreate`: Generiert eine Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-399">`ef migrations add InitialCreate`: Generates an *Migrations/{timestamp}_InitialCreate.cs* migration file.</span></span> <span data-ttu-id="d8e9b-400">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-400">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-401">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-401">Any name can be used, but by convention, a name is selected that describes the migration.</span></span> <span data-ttu-id="d8e9b-402">Da dies die erste Migration ist, enthält die generierte Klasse Code zum Erstellen des Datenbankschemas.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-402">Because this is the first migration, the generated class contains code to create the database schema.</span></span> <span data-ttu-id="d8e9b-403">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-403">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span>

* <span data-ttu-id="d8e9b-404">`ef database update`: Aktualisiert die Datenbank auf die neueste Migration, die der vorherige Befehl erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-404">`ef database update`: Updates the database to the latest migration, which the previous command created.</span></span> <span data-ttu-id="d8e9b-405">Der Befehl führt die `Up`-Methode in der Datei *Migrations/{zeitstempel}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-405">This command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

---

### <a name="the-initialcreate-class"></a><span data-ttu-id="d8e9b-406">Die InitialCreate-Klasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-406">The InitialCreate class</span></span>

<span data-ttu-id="d8e9b-407">Untersuchen Sie die Migrationsdatei *Migrations/{zeitstempel}_InitialCreate.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-407">Examine the *Migrations/{timestamp}_InitialCreate.cs* migration file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

<span data-ttu-id="d8e9b-408">Die `Up`-Methode erstellt die Movie-Tabelle und konfiguriert `Id` als Primärschlüssel.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-408">The `Up` method creates the Movie table and configures `Id` as the primary key.</span></span> <span data-ttu-id="d8e9b-409">Die `Down`-Methode macht die von der `Up`-Migration vorgenommenen Schemaänderungen rückgängig.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-409">The `Down` method reverts the schema changes made by the `Up` migration.</span></span>

<a name="test"></a>

## <a name="test-the-app"></a><span data-ttu-id="d8e9b-410">Testen der App</span><span class="sxs-lookup"><span data-stu-id="d8e9b-410">Test the app</span></span>

* <span data-ttu-id="d8e9b-411">Führen Sie die APP aus, und klicken Sie auf den Link **Movie App**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-411">Run the app and click the **Movie App** link.</span></span>

  <span data-ttu-id="d8e9b-412">Wenn eine Ausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-412">If you get an exception similar to one of the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-413">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-413">Visual Studio</span></span>](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-414">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-414">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  <span data-ttu-id="d8e9b-415">Sie haben möglicherweise den [Migrationsschritt](#migration) ausgelassen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-415">You probably missed the [migrations step](#migration).</span></span>

* <span data-ttu-id="d8e9b-416">Testen Sie die Seite **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-416">Test the **Create** page.</span></span> <span data-ttu-id="d8e9b-417">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-417">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d8e9b-418">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-418">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d8e9b-419">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-419">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d8e9b-420">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-420">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d8e9b-421">Testen Sie die Seiten **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-421">Test the **Edit**, **Details**, and **Delete** pages.</span></span>

## <a name="dependency-injection-in-the-controller"></a><span data-ttu-id="d8e9b-422">Abhängigkeitsinjektion im Controller</span><span class="sxs-lookup"><span data-stu-id="d8e9b-422">Dependency injection in the controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-423">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-423">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-424">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-424">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d8e9b-425">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-425">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d8e9b-426">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-426">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-427">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-427">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d8e9b-428">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-428">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d8e9b-429">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-429">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

### <a name="use-sqlite-for-development-sql-server-for-production"></a><span data-ttu-id="d8e9b-430">Verwenden von SQLite für die Entwicklung und von SQL Server für die Produktion</span><span class="sxs-lookup"><span data-stu-id="d8e9b-430">Use SQLite for development, SQL Server for production</span></span>

<span data-ttu-id="d8e9b-431">Wenn SQLite ausgewählt wurde, ist der von der Vorlage generierte Code für die Entwicklung bereit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-431">When SQLite is selected, the template generated code is ready for development.</span></span> <span data-ttu-id="d8e9b-432">Der folgende Code zeigt das Injizieren von <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in Startup.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-432">The following code shows how to inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into Startup.</span></span> <span data-ttu-id="d8e9b-433">`IWebHostEnvironment` wird eingefügt, sodass `ConfigureServices` SQLite bei der Entwicklung und SQL Server für die Produktion verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-433">`IWebHostEnvironment` is injected so `ConfigureServices` can use SQLite in development and SQL Server in production.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d8e9b-434">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="d8e9b-434">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d8e9b-435">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-435">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d8e9b-436">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-436">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d8e9b-437">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-437">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d8e9b-438">Dieser stark typisierte Ansatz ermöglicht eine Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-438">This strongly typed approach enables compile time code checking.</span></span> <span data-ttu-id="d8e9b-439">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-439">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views.</span></span>

<span data-ttu-id="d8e9b-440">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-440">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d8e9b-441">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-441">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d8e9b-442">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-442">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d8e9b-443">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-443">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d8e9b-444">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-444">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d8e9b-445">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-445">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d8e9b-446">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-446">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d8e9b-447">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-447">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d8e9b-448">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-448">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d8e9b-449">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-449">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
```

<span data-ttu-id="d8e9b-450">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-450">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d8e9b-451">Die `@model`-Anweisung am Anfang der Ansichtsdatei gibt den Typ des Objekts an, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-451">The `@model` statement at the top of the view file specifies the type of object that the view expects.</span></span> <span data-ttu-id="d8e9b-452">Beim Erstellen des Movie-Controllers wurde die folgende `@model`-Anweisung eingeschlossen:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-452">When the movie controller was created, the following `@model` statement was included:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d8e9b-453">Diese `@model`-Anweisung ermöglicht den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-453">This `@model` directive allows access to the movie that the controller passed to the view.</span></span> <span data-ttu-id="d8e9b-454">Das `Model`-Objekt ist stark typisiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-454">The `Model` object is strongly typed.</span></span> <span data-ttu-id="d8e9b-455">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-455">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d8e9b-456">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-456">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d8e9b-457">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-457">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d8e9b-458">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-458">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d8e9b-459">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-459">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d8e9b-460">Als der Movies-Controller erstellt wurde, hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-460">When the movies controller was created, scaffolding included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d8e9b-461">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-461">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d8e9b-462">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-462">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d8e9b-463">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-463">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d8e9b-464">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-464">Among other benefits, this means that you get compile time checking of the code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8e9b-465">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8e9b-465">Additional resources</span></span>

* [<span data-ttu-id="d8e9b-466">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="d8e9b-466">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d8e9b-467">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="d8e9b-467">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d8e9b-468">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit SQL](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-468">[Previous Adding a View](adding-view.md)
[Next Working with SQL](working-with-sql.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a><span data-ttu-id="d8e9b-469">Hinzufügen einer Datenmodellklasse</span><span class="sxs-lookup"><span data-stu-id="d8e9b-469">Add a data model class</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-470">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-470">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-471">Klicken Sie mit der rechten Maustaste auf den Ordner *Modelle*>**Hinzufügen** > **Klasse**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-471">Right-click the *Models* folder > **Add** > **Class**.</span></span> <span data-ttu-id="d8e9b-472">Nennen Sie die Klasse **Movie**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-472">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-473">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-473">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="d8e9b-474">Fügen Sie zum Ordner *Modelle* eine Klasse mit dem Namen *Movie.cs* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-474">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="d8e9b-475">Erstellen des Gerüsts für das Filmmodell</span><span class="sxs-lookup"><span data-stu-id="d8e9b-475">Scaffold the movie model</span></span>

<span data-ttu-id="d8e9b-476">In diesem Abschnitt wird das Gerüst für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-476">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="d8e9b-477">Mit dem Tool für den Gerüstbau werden Seiten für die Vorgänge „Create“ (Erstellen), „Read“ (Lesen), „Update“ (Aktualisieren) und „Delete“ (Löschen), kurz CRUD-Vorgänge, für das Filmmodell erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-477">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-478">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-478">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-479">Klicken Sie im *Projektmappen-Explorer* mit der rechten Maustaste auf den Ordner **Controllers** und anschließend auf **Hinzufügen > Neues Gerüstelement**.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-479">In **Solution Explorer**, right-click the *Controllers* folder **> Add > New Scaffolded Item**.</span></span>

![Screenshot für oben genannten Schritt](adding-model/_static/add_controller21.png)

<span data-ttu-id="d8e9b-481">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten unter Verwendung von Entity Framework > Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-481">In the **Add Scaffold** dialog, select **MVC Controller with views, using Entity Framework > Add**.</span></span>

![Dialogfeld „Gerüst hinzufügen“](adding-model/_static/add_scaffold21.png)

<span data-ttu-id="d8e9b-483">Bearbeiten Sie das Dialogfeld **Controller hinzufügen**:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-483">Complete the **Add Controller** dialog:</span></span>

* <span data-ttu-id="d8e9b-484">**Modellklasse:** *Movie (MvcMovie.Models)*</span><span class="sxs-lookup"><span data-stu-id="d8e9b-484">**Model class:** *Movie (MvcMovie.Models)*</span></span>
* <span data-ttu-id="d8e9b-485">**Datenkontextklasse:** Wählen Sie das Symbol **+** aus, und fügen Sie den Standardtyp **MvcMovie.Models.MvcMovieContext** hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-485">**Data context class:** Select the **+** icon and add the default **MvcMovie.Models.MvcMovieContext**</span></span>

![Datenkontext hinzufügen](adding-model/_static/dc.png)

* <span data-ttu-id="d8e9b-487">**Ansichten:** Belassen Sie den Standardwert der einzelnen Optionen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-487">**Views:** Keep the default of each option checked</span></span>
* <span data-ttu-id="d8e9b-488">**Controllername:** Übernehmen Sie den Standardnamen *MoviesController*.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-488">**Controller name:** Keep the default *MoviesController*</span></span>
* <span data-ttu-id="d8e9b-489">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-489">Select **Add**</span></span>

![Dialogfeld „Controller hinzufügen“](adding-model/_static/add_controller2.png)

<span data-ttu-id="d8e9b-491">Visual Studio erstellt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-491">Visual Studio creates:</span></span>

* <span data-ttu-id="d8e9b-492">Eine Entity Framework Core-[Datenbankkontext-Klasse](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-492">An Entity Framework Core [database context class](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)</span></span>
* <span data-ttu-id="d8e9b-493">Einen Movies-Controller (*Controllers/MoviesController.cs*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-493">A movies controller (*Controllers/MoviesController.cs*)</span></span>
* <span data-ttu-id="d8e9b-494">Razor-Ansichtsdateien für die Seiten „Erstellen“, „Löschen“, „Details“ „Bearbeiten“ und „Index“ (*Views/Movies/\*.cshtml*)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-494">Razor view files for Create, Delete, Details, Edit, and Index pages (*Views/Movies/\*.cshtml*)</span></span>

<span data-ttu-id="d8e9b-495">Die automatische Erstellung des Datenbankkontexts und der [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Aktionsmethoden (create, read, update and delete – Erstellen, Lesen, Aktualisieren und Löschen) und Ansichten wird als *Gerüstbau* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-495">The automatic creation of the database context and [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (create, read, update, and delete) action methods and views is known as *scaffolding*.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d8e9b-496">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d8e9b-496">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="d8e9b-497">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-497">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="d8e9b-498">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-498">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="d8e9b-499">Exportieren Sie unter Linux den Pfad des Gerüstbautools:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-499">On Linux, export the scaffold tool path:</span></span>

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* <span data-ttu-id="d8e9b-500">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-500">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-501">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-501">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d8e9b-502">Öffnen Sie ein Befehlsfenster im Projektverzeichnis (das Verzeichnis mit den Dateien *Program.cs*, *Startup.cs*, und *CSPROJ*).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-502">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="d8e9b-503">Installieren Sie das Gerüstbautool:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-503">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="d8e9b-504">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-504">Run the following command:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

<span data-ttu-id="d8e9b-505">Wenn Sie die App ausführen und auf den Link **Mvc Movie** klicken, erhalten Sie eine Fehlermeldung wie die Folgende:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-505">If you run the app and click on the **Mvc Movie** link, you get an error similar to the following:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-506">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-506">Visual Studio</span></span>](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-507">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-507">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

<span data-ttu-id="d8e9b-508">Sie müssen die Datenbank erstellen und verwenden dazu das EF Core-Feature [Migrationen](xref:data/ef-mvc/migrations).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-508">You need to create the database, and you use the EF Core [Migrations](xref:data/ef-mvc/migrations) feature to do that.</span></span> <span data-ttu-id="d8e9b-509">Mit „Migrationen“ können Sie eine Datenbank erstellen, die mit Ihrem Datenmodell übereinstimmt, und das Datenbankschema aktualisieren, wenn sich Ihr Datenmodell ändert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-509">Migrations lets you create a database that matches your data model and update the database schema when your data model changes.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="d8e9b-510">Anfängliche Migration</span><span class="sxs-lookup"><span data-stu-id="d8e9b-510">Initial migration</span></span>

<span data-ttu-id="d8e9b-511">In diesem Abschnitt werden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-511">In this section, the following tasks are completed:</span></span>

* <span data-ttu-id="d8e9b-512">Fügen Sie eine anfängliche Migration hinzu.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-512">Add an initial migration.</span></span>
* <span data-ttu-id="d8e9b-513">Aktualisieren Sie die Datenbank mit der anfänglichen Migration.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-513">Update the database with the initial migration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-514">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-514">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d8e9b-515">Wählen Sie im Menü **Tools** **NuGet-Paket-Manager** > **Paket-Manager-Konsole** (PMC) aus.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-515">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console** (PMC).</span></span>

   ![PMC-Menü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. <span data-ttu-id="d8e9b-517">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-517">In the PMC, enter the following commands:</span></span>

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   <span data-ttu-id="d8e9b-518">Mit dem Befehl `Add-Migration` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-518">The `Add-Migration` command generates code to create the initial database schema.</span></span>

   <span data-ttu-id="d8e9b-519">Das Datenbankschema basiert auf dem Modell, das in der Klasse `MvcMovieContext` angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-519">The database schema is based on the model specified in the `MvcMovieContext` class.</span></span> <span data-ttu-id="d8e9b-520">Das `Initial`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-520">The `Initial` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-521">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name verwendet, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-521">Any name can be used, but by convention, a name that describes the migration is used.</span></span> <span data-ttu-id="d8e9b-522">Weitere Informationen finden Sie unter <xref:data/ef-mvc/migrations>.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-522">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

   <span data-ttu-id="d8e9b-523">Mit dem Befehl `Update-Database` führen Sie die Methode `Up` in der Datei *Migrations/{time-stamp}_InitialCreate.cs* aus, mit der die Datenbank erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-523">The `Update-Database` command runs the `Up` method in the *Migrations/{time-stamp}_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-524">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-524">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

<span data-ttu-id="d8e9b-525">Mit dem Befehl `ef migrations add InitialCreate` wird Code generiert, um das anfängliche Datenbankschema zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-525">The `ef migrations add InitialCreate` command generates code to create the initial database schema.</span></span>

<span data-ttu-id="d8e9b-526">Das Datenbankschema basiert auf dem Modell, das in der `MvcMovieContext`-Klasse (in der Datei *Data/MvcMovieContext.cs*) angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-526">The database schema is based on the model specified in the `MvcMovieContext` class (in the *Data/MvcMovieContext.cs* file).</span></span> <span data-ttu-id="d8e9b-527">Das `InitialCreate`-Argument ist der Migrationsname.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-527">The `InitialCreate` argument is the migration name.</span></span> <span data-ttu-id="d8e9b-528">Es kann jeder Name verwendet werden, aber per Konvention wird ein Name ausgewählt, der die Migration beschreibt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-528">Any name can be used, but by convention, a name is selected that describes the migration.</span></span>

---

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="d8e9b-529">Überprüfen des mit Dependency Injection registrierten Kontexts</span><span class="sxs-lookup"><span data-stu-id="d8e9b-529">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="d8e9b-530">ASP.NET Core wird mit [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-530">ASP.NET Core is built with [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8e9b-531">Dienste (z.B. der EF Core-Datenbankkontext) werden über DI beim Anwendungsstart registriert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-531">Services (such as the EF Core DB context) are registered with DI during application startup.</span></span> <span data-ttu-id="d8e9b-532">Komponenten, die diese Dienste erfordern (z. B. Razor Pages), werden von diesen Diensten über Konstruktorparameter bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-532">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="d8e9b-533">Der Konstruktorcode, der eine Datenbankkontext-Instanz abruft, wird später in diesem Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-533">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d8e9b-534">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d8e9b-534">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d8e9b-535">Das Gerüstbautool hat automatisch einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-535">The scaffolding tool automatically created a DB context and registered it with the DI container.</span></span>

<span data-ttu-id="d8e9b-536">Untersuchen Sie die folgende `Startup.ConfigureServices`-Methode.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-536">Examine the following `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="d8e9b-537">Die hervorgehobene Zeile wurde vom Gerüst hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-537">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

<span data-ttu-id="d8e9b-538">Der `MvcMovieContext` koordiniert die EF Core-Funktionen (Create, Read, Update, Delete usw.) für das `Movie`-Modell.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-538">The `MvcMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="d8e9b-539">Der Datenkontext (`MvcMovieContext`) wird aus [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-539">The data context (`MvcMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="d8e9b-540">Der Datenkontext gibt an, welche Entitäten im Datenmodell enthalten sind:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-540">The data context specifies which entities are included in the data model:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

<span data-ttu-id="d8e9b-541">Der vorangehende Code erstellt eine [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1)-Eigenschaft für die Entitätsmenge.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-541">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="d8e9b-542">In der Terminologie von Entity Framework entspricht eine Entitätenmenge in der Regel einer Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-542">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="d8e9b-543">Entitäten entsprechen Zeilen in Tabellen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-543">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="d8e9b-544">Der Name der Verbindungszeichenfolge wird an den Kontext übergeben, indem Sie eine Methode auf einem [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions)-Objekt aufrufen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-544">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="d8e9b-545">Für die lokale Entwicklung liest das [ASP.NET Core-Konfigurationssystem](xref:fundamentals/configuration/index) die Verbindungszeichenfolge aus der *appsettings.json* -Datei.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-545">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="d8e9b-546">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d8e9b-546">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="d8e9b-547">Sie haben einen Datenbankkontext erstellt und diesen mit dem DI-Container registriert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-547">You created a DB context and registered it with the DI container.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="d8e9b-548">Testen der App</span><span class="sxs-lookup"><span data-stu-id="d8e9b-548">Test the app</span></span>

* <span data-ttu-id="d8e9b-549">Führen Sie die App aus, und fügen Sie `/Movies` an die URL im Browser an (`http://localhost:port/movies`).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-549">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="d8e9b-550">Wenn eine Datenbankausnahme auftritt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-550">If you get a database exception similar to the following:</span></span>

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="d8e9b-551">Sie haben den [Migrationsschritt](#pmc) verpasst.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-551">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="d8e9b-552">Testen Sie den Link **Create** (Erstellen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-552">Test the **Create** link.</span></span> <span data-ttu-id="d8e9b-553">Geben Sie die Daten ein, und senden Sie sie.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-553">Enter and submit data.</span></span>

  > [!NOTE]
  > <span data-ttu-id="d8e9b-554">Sie können unter Umständen in das Feld `Price` keine Kommas als Dezimaltrennzeichen eingeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-554">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="d8e9b-555">Zur Unterstützung der [jQuery-Validierung](https://jqueryvalidation.org/) für Gebietsschemas mit einer anderen Sprache als Englisch, in denen ein Komma („,“) als Dezimaltrennzeichen verwendet wird, und für Datums- und Uhrzeitformate, die nicht dem US-englischen Format entsprechen, muss die App globalisiert werden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-555">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="d8e9b-556">Globalisierungsanweisungen finden Sie unter [GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-556">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="d8e9b-557">Testen Sie die Links **Edit** (Bearbeiten), **Details** und **Delete** (Löschen).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-557">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="d8e9b-558">Überprüfen Sie die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-558">Examine the `Startup` class:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

<span data-ttu-id="d8e9b-559">Der oberhalb hervorgehobene Code zeigt den Filmdatenbankkontext, der dem [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)-Container hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-559">The preceding highlighted code shows the movie database context being added to the [Dependency Injection](xref:fundamentals/dependency-injection) container:</span></span>

* <span data-ttu-id="d8e9b-560">`services.AddDbContext<MvcMovieContext>(options =>` gibt die zu verwendende Datenbank und die Verbindungszeichenfolge an.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-560">`services.AddDbContext<MvcMovieContext>(options =>` specifies the database to use and the connection string.</span></span>
* <span data-ttu-id="d8e9b-561">`=>` ist ein [Lambdaoperator](/dotnet/articles/csharp/language-reference/operators/lambda-operator).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-561">`=>` is a [lambda operator](/dotnet/articles/csharp/language-reference/operators/lambda-operator)</span></span>

<span data-ttu-id="d8e9b-562">Öffnen Sie die Datei *Controllers/MoviesController.cs*, und überprüfen Sie den Konstruktor:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-562">Open the *Controllers/MoviesController.cs* file and examine the constructor:</span></span>

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

<span data-ttu-id="d8e9b-563">Der Konstruktor verwendet die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zum Einfügen des Datenbankkontexts (`MvcMovieContext`) in den Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-563">The constructor uses [Dependency Injection](xref:fundamentals/dependency-injection) to inject the database context (`MvcMovieContext`) into the controller.</span></span> <span data-ttu-id="d8e9b-564">Der Datenbankkontext wird in den einzelnen [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)-Methoden im Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-564">The database context is used in each of the [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) methods in the controller.</span></span>

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a><span data-ttu-id="d8e9b-565">Stark typisierte Modelle und das Schlüsselwort @model</span><span class="sxs-lookup"><span data-stu-id="d8e9b-565">Strongly typed models and the @model keyword</span></span>

<span data-ttu-id="d8e9b-566">Weiter oben in diesem Tutorial haben Sie gesehen, wie ein Controller mithilfe des Wörterbuchs `ViewData` Daten oder Objekte an eine Ansicht übergeben kann.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-566">Earlier in this tutorial, you saw how a controller can pass data or objects to a view using the `ViewData` dictionary.</span></span> <span data-ttu-id="d8e9b-567">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, das eine praktische Möglichkeit zur späten Bindung bietet, um Informationen an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-567">The `ViewData` dictionary is a dynamic object that provides a convenient late-bound way to pass information to a view.</span></span>

<span data-ttu-id="d8e9b-568">MVC bietet außerdem die Möglichkeit, stark typisierte Modellobjekte an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-568">MVC also provides the ability to pass strongly typed model objects to a view.</span></span> <span data-ttu-id="d8e9b-569">Dieser stark typisierte Ansatz ermöglicht eine bessere Überprüfung Ihres Codes zur Kompilierzeit.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-569">This strongly typed approach enables better compile time checking of your code.</span></span> <span data-ttu-id="d8e9b-570">Der Gerüstbaumechnismus hat diesen Ansatz (d.h. das Übergeben eines stark typisierten Modells) mit der `MoviesController`-Klasse und Ansichten genutzt, als die Methoden und Ansichten erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-570">The scaffolding mechanism used this approach (that is, passing a strongly typed model) with the `MoviesController` class and views when it created the methods and views.</span></span>

<span data-ttu-id="d8e9b-571">Untersuchen Sie die generierte `Details`-Methode in der Datei *Controllers/MoviesController.cs*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-571">Examine the generated `Details` method in the *Controllers/MoviesController.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

<span data-ttu-id="d8e9b-572">Der `id`-Parameter wird im Allgemeinen als Routendaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-572">The `id` parameter is generally passed as route data.</span></span> <span data-ttu-id="d8e9b-573">`https://localhost:5001/movies/details/1` legt beispielsweise Folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-573">For example `https://localhost:5001/movies/details/1` sets:</span></span>

* <span data-ttu-id="d8e9b-574">Den Controller auf den Controller `movies` (das erste URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-574">The controller to the `movies` controller (the first URL segment).</span></span>
* <span data-ttu-id="d8e9b-575">Die Aktion auf `details` (das zweite URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-575">The action to `details` (the second URL segment).</span></span>
* <span data-ttu-id="d8e9b-576">Die ID auf 1 (das letzte URL-Segment).</span><span class="sxs-lookup"><span data-stu-id="d8e9b-576">The id to 1 (the last URL segment).</span></span>

<span data-ttu-id="d8e9b-577">Sie können die `id` auch mithilfe einer Abfragezeichenfolge übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-577">You can also pass in the `id` with a query string as follows:</span></span>

`https://localhost:5001/movies/details?id=1`

<span data-ttu-id="d8e9b-578">Der `id`-Parameter wird als [Nullable-Typ](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) für den Fall definiert, dass kein ID-Wert angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-578">The `id` parameter is defined as a [nullable type](/dotnet/csharp/programming-guide/nullable-types/index) (`int?`) in case an ID value isn't provided.</span></span>

<span data-ttu-id="d8e9b-579">Ein [Lambdaausdruck](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) wird an `FirstOrDefaultAsync` übergeben, um Filmentitäten auszuwählen, die mit den Routendaten oder dem Wert der Abfragezeichenfolge übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-579">A [lambda expression](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) is passed in to `FirstOrDefaultAsync` to select movie entities that match the route data or query string value.</span></span>

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

<span data-ttu-id="d8e9b-580">Wenn ein Film gefunden wird, wird eine Instanz des `Movie`-Modells an die Ansicht `Details` übergeben:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-580">If a movie is found, an instance of the `Movie` model is passed to the `Details` view:</span></span>

```csharp
return View(movie);
   ```

<span data-ttu-id="d8e9b-581">Untersuchen Sie den Inhalt der Datei *Views/Movies/Details.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-581">Examine the contents of the *Views/Movies/Details.cshtml* file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

<span data-ttu-id="d8e9b-582">Durch Einschließen einer `@model`-Anweisung am Anfang der Ansichtsdatei können Sie den Typ des Objekts angeben, den die Ansicht erwartet.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-582">By including a `@model` statement at the top of the view file, you can specify the type of object that the view expects.</span></span> <span data-ttu-id="d8e9b-583">Beim Erstellen des „Movie“-Controllers wurde die folgende `@model`-Anweisung automatisch am Anfang der Datei *Details.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-583">When you created the movie controller, the following `@model` statement was automatically included at the top of the *Details.cshtml* file:</span></span>

```cshtml
@model MvcMovie.Models.Movie
```

<span data-ttu-id="d8e9b-584">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf den Film, den der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wir.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-584">This `@model` directive allows you to access the movie that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d8e9b-585">In der Ansicht *Details.cshtml* übergibt der Code jedes Filmfeld an die HTML-Hilfsprogramme `DisplayNameFor` und `DisplayFor` mit dem stark typisierten `Model`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-585">For example, in the *Details.cshtml* view, the code passes each movie field to the `DisplayNameFor` and `DisplayFor` HTML Helpers with the strongly typed `Model` object.</span></span> <span data-ttu-id="d8e9b-586">Die Methoden `Create` und `Edit` und Ansichten übergeben außerdem ein `Movie`-Modelobjekt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-586">The `Create` and `Edit` methods and views also pass a `Movie` model object.</span></span>

<span data-ttu-id="d8e9b-587">Überprüfen Sie die Datei *Index.cshtml* und die `Index`-Methode im „Movies“-Controller.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-587">Examine the *Index.cshtml* view and the `Index` method in the Movies controller.</span></span> <span data-ttu-id="d8e9b-588">Beachten Sie, wie der Code beim Aufrufen der `View`-Methode ein `List`-Objekt erstellt.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-588">Notice how the code creates a `List` object when it calls the `View` method.</span></span> <span data-ttu-id="d8e9b-589">Der Code übergibt diese Liste `Movies` aus der Aktionsmethode `Index` an die Ansicht:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-589">The code passes this `Movies` list from the `Index` action method to the view:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

<span data-ttu-id="d8e9b-590">Beim Erstellen des „Movies“-Controllers hat der Gerüstbau automatisch die folgende `@model`-Anweisung am Anfang der Datei *Index.cshtml* hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-590">When you created the movies controller, scaffolding automatically included the following `@model` statement at the top of the *Index.cshtml* file:</span></span>

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

<span data-ttu-id="d8e9b-591">Diese `@model`-Direktive ermöglicht Ihnen den Zugriff auf die Liste der Filme, die der Controller an die Ansicht übergeben hat, indem ein stark typisiertes `Model`-Objekt verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-591">The `@model` directive allows you to access the list of movies that the controller passed to the view by using a `Model` object that's strongly typed.</span></span> <span data-ttu-id="d8e9b-592">In der Ansicht *Index.cshtml* durchläuft der Code z.B. die Filme mithilfe einer `foreach`-Anweisung für das stark typisierte `Model`-Objekt:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-592">For example, in the *Index.cshtml* view, the code loops through the movies with a `foreach` statement over the strongly typed `Model` object:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

<span data-ttu-id="d8e9b-593">Da das `Model`-Objekt stark typisiert ist (als ein `IEnumerable<Movie>`-Objekt), wird jedes Element in der Schleife als `Movie` typisiert.</span><span class="sxs-lookup"><span data-stu-id="d8e9b-593">Because the `Model` object is strongly typed (as an `IEnumerable<Movie>` object), each item in the loop is typed as `Movie`.</span></span> <span data-ttu-id="d8e9b-594">Neben anderen Vorteilen bedeutet dies, dass Sie eine Überprüfung des Codes zur Kompilierzeit erhalten:</span><span class="sxs-lookup"><span data-stu-id="d8e9b-594">Among other benefits, this means that you get compile time checking of the code:</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8e9b-595">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d8e9b-595">Additional resources</span></span>

* [<span data-ttu-id="d8e9b-596">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="d8e9b-596">Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="d8e9b-597">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="d8e9b-597">Globalization and localization</span></span>](xref:fundamentals/localization)

> [!div class="step-by-step"]
> <span data-ttu-id="d8e9b-598">[Vorheriges Tutorial: Hinzufügen einer Ansicht](adding-view.md)
> [Nächstes Tutorial: Arbeiten mit einer Datenbank](working-with-sql.md)</span><span class="sxs-lookup"><span data-stu-id="d8e9b-598">[Previous Adding a View](adding-view.md)
[Next Working with a database](working-with-sql.md)</span></span>

::: moniker-end
