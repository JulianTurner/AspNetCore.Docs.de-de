---
title: 'Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zu Razor Pages und dem Entity Framework.
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
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
uid: data/ef-rp/read-related-data
ms.openlocfilehash: e52e4aefc18b84f85bea28a9724894eed50ca54a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061066"
---
# <a name="part-6-no-locrazor-pages-with-ef-core-in-aspnet-core---read-related-data"></a><span data-ttu-id="48c66-103">Teil 6: Razor Pages mit EF Core in ASP.NET Core – Lesen relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="48c66-103">Part 6, Razor Pages with EF Core in ASP.NET Core - Read Related Data</span></span>

<span data-ttu-id="48c66-104">Von [Tom Dykstra](https://github.com/tdykstra), [Jon P. Smith](https://twitter.com/thereformedprog) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48c66-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48c66-105">Dieses Tutorial zeigt, wie verwandte Daten gelesen und angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-105">This tutorial shows how to read and display related data.</span></span> <span data-ttu-id="48c66-106">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-106">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="48c66-107">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="48c66-107">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)

## <a name="eager-explicit-and-lazy-loading"></a><span data-ttu-id="48c66-110">Explizites Laden, Eager Loading und Lazy Loading</span><span class="sxs-lookup"><span data-stu-id="48c66-110">Eager, explicit, and lazy loading</span></span>

<span data-ttu-id="48c66-111">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="48c66-111">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="48c66-112">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-112">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="48c66-113">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="48c66-113">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="48c66-114">Wenn eine Entität gelesen wird, werden ihre zugehörigen Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-114">When an entity is read, its related data is retrieved.</span></span> <span data-ttu-id="48c66-115">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="48c66-115">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="48c66-116">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-116">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="48c66-117">Das Ausgeben mehrerer Abfragen kann effizienter sein als eine einzelne große Abfrage.</span><span class="sxs-lookup"><span data-stu-id="48c66-117">Issuing multiple queries can be more efficient than a giant single query.</span></span> <span data-ttu-id="48c66-118">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-118">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="48c66-120">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="48c66-120">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="48c66-121">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="48c66-121">One query for the main query</span></span> 
  * <span data-ttu-id="48c66-122">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="48c66-122">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="48c66-123">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="48c66-123">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="48c66-124">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch mit Daten auffüllt.</span><span class="sxs-lookup"><span data-stu-id="48c66-124">"Fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="48c66-125">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="48c66-125">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="48c66-127">**Hinweis**: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="48c66-127">**Note:** EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="48c66-128">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="48c66-128">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="48c66-129">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-129">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="48c66-130">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-130">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="48c66-131">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-131">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="48c66-132">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-132">Explicit loading with separate queries results in multiple queries sent to the database.</span></span> <span data-ttu-id="48c66-133">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="48c66-133">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="48c66-134">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-134">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="48c66-135">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="48c66-135">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="48c66-137">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-137">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="48c66-138">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-138">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="48c66-139">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-139">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="48c66-140">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-140">A query is sent to the database each time a navigation property is accessed for the first time.</span></span> <span data-ttu-id="48c66-141">Lazy Loading kann die Leistung beeinträchtigen, z. B. wenn Entwickler N+1-Muster verwenden, ein übergeordnetes Element laden und über untergeordnete Elemente aufzählen.</span><span class="sxs-lookup"><span data-stu-id="48c66-141">Lazy loading can hurt performance, for example when developers use N+1 patterns, loading a parent and enumerating through children.</span></span>

## <a name="create-course-pages"></a><span data-ttu-id="48c66-142">Erstellen von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="48c66-142">Create Course pages</span></span>

<span data-ttu-id="48c66-143">Die `Course`-Entität enthält eine Navigationseigenschaft, die die zugehörige `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="48c66-143">The `Course` entity includes a navigation property that contains the related `Department` entity.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<span data-ttu-id="48c66-145">So zeigen Sie den Namen des zugewiesenen Fachbereichs für einen Kurs an:</span><span class="sxs-lookup"><span data-stu-id="48c66-145">To display the name of the assigned department for a course:</span></span>

* <span data-ttu-id="48c66-146">Laden Sie die zugehörige `Department`-Entität in die `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="48c66-146">Load the related `Department` entity into the `Course.Department` navigation property.</span></span>
* <span data-ttu-id="48c66-147">Rufen Sie den Namen aus der `Name`-Eigenschaft der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="48c66-147">Get the name from the `Department` entity's `Name` property.</span></span>

<a name="scaffold"></a>

### <a name="scaffold-course-pages"></a><span data-ttu-id="48c66-148">Gerüstbau von Course-Seiten</span><span class="sxs-lookup"><span data-stu-id="48c66-148">Scaffold Course pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48c66-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48c66-149">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48c66-150">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="48c66-150">Follow the instructions in [Scaffold Student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="48c66-151">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="48c66-151">Create a *Pages/Courses* folder.</span></span>
  * <span data-ttu-id="48c66-152">Verwenden Sie `Course` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="48c66-152">Use `Course` for the model class.</span></span>
  * <span data-ttu-id="48c66-153">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-153">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48c66-154">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48c66-154">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="48c66-155">Erstellen Sie einen Ordner *Pages/Courses*.</span><span class="sxs-lookup"><span data-stu-id="48c66-155">Create a *Pages/Courses* folder.</span></span>

* <span data-ttu-id="48c66-156">Führen Sie den folgenden Befehl aus, um das Gerüst für Course-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-156">Run the following command to scaffold the Course pages.</span></span>

  <span data-ttu-id="48c66-157">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="48c66-157">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

  <span data-ttu-id="48c66-158">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="48c66-158">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages/Courses --referenceScriptLibraries
  ```

---

* <span data-ttu-id="48c66-159">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-159">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="48c66-160">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="48c66-160">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="48c66-161">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="48c66-161">The `Include` method specifies eager loading.</span></span>

* <span data-ttu-id="48c66-162">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="48c66-162">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="48c66-163">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-163">The department column displays the `DepartmentID`, which isn't useful.</span></span>

### <a name="display-the-department-name"></a><span data-ttu-id="48c66-164">Anzeigen des Fachbereichnamens</span><span class="sxs-lookup"><span data-stu-id="48c66-164">Display the department name</span></span>

<span data-ttu-id="48c66-165">Aktualisieren Sie „Pages/Courses/Index.cshtml.cs“ mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-165">Update Pages/Courses/Index.cshtml.cs with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Courses/Index.cshtml.cs?highlight=18,22,24)]

<span data-ttu-id="48c66-166">Der vorstehende Code ändert die `Course`-Eigenschaft in `Courses` und fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-166">The preceding code changes the `Course` property to `Courses` and adds `AsNoTracking`.</span></span> <span data-ttu-id="48c66-167">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-167">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="48c66-168">Die Entitäten müssen nicht nachverfolgt werden, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-168">The entities don't need to be tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="48c66-169">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="48c66-169">Update *Pages/Courses/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Courses/Index.cshtml?highlight=5,8,16-18,20,23,26,32,35-37,45)]

<span data-ttu-id="48c66-170">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="48c66-170">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="48c66-171">Der `Course`-Eigenschaftenname wurde in `Courses` geändert.</span><span class="sxs-lookup"><span data-stu-id="48c66-171">Changed the `Course` property name to `Courses`.</span></span>
* <span data-ttu-id="48c66-172">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="48c66-172">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="48c66-173">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="48c66-173">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="48c66-174">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="48c66-174">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="48c66-175">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-175">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="48c66-176">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="48c66-176">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="48c66-177">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="48c66-177">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index30.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="48c66-179">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="48c66-179">Loading related data with Select</span></span>

<span data-ttu-id="48c66-180">Die `OnGetAsync`-Methode lädt zugehörige Daten mit der `Include`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-180">The `OnGetAsync` method loads related data with the `Include` method.</span></span> <span data-ttu-id="48c66-181">Die `Select`-Methode ist eine Alternative, die nur die zugehörigen benötigten Daten lädt.</span><span class="sxs-lookup"><span data-stu-id="48c66-181">The `Select` method is an alternative that loads only the related data needed.</span></span> <span data-ttu-id="48c66-182">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-182">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="48c66-183">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="48c66-183">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="48c66-184">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="48c66-184">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=6)]

<span data-ttu-id="48c66-185">Der voranstehende Code gibt keine Entitätstypen zurück, weshalb keine Nachverfolgung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="48c66-185">The preceding code doesn't return any entity types, therefore no tracking is done.</span></span> <span data-ttu-id="48c66-186">Weitere Informationen zur EF-Nachverfolgung finden Sie unter [Nachverfolgungsabfragen im Vergleich zu Abfragen ohne Nachverfolgung](/ef/core/querying/tracking).</span><span class="sxs-lookup"><span data-stu-id="48c66-186">For more information about the EF tracking, see [Tracking vs. No-Tracking Queries](/ef/core/querying/tracking).</span></span>

<span data-ttu-id="48c66-187">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="48c66-187">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="48c66-188">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="48c66-188">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu30snapshots/6-related/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-instructor-pages"></a><span data-ttu-id="48c66-189">Erstellen von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="48c66-189">Create Instructor pages</span></span>

<span data-ttu-id="48c66-190">In diesem Abschnitt erfolgt der Gerüstbau von Instructor-Seiten, und der Index-Seite des Dozenten werden zugehörige Courses und Enrollments hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-190">This section scaffolds Instructor pages and adds related Courses and Enrollments to the Instructors Index page.</span></span>

<a name="IP"></a>
<span data-ttu-id="48c66-191">![Indexseite „Dozenten“](read-related-data/_static/instructors-index30.png)</span><span class="sxs-lookup"><span data-stu-id="48c66-191">![Instructors Index page](read-related-data/_static/instructors-index30.png)</span></span>

<span data-ttu-id="48c66-192">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="48c66-192">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="48c66-193">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="48c66-193">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="48c66-194">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-194">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="48c66-195">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-195">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="48c66-196">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="48c66-196">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="48c66-197">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-197">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="48c66-198">Wenn der Benutzer einen Dozenten auswählt, werden zugehörige `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-198">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="48c66-199">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-199">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="48c66-200">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-200">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="48c66-201">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-201">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="48c66-202">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="48c66-202">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="48c66-203">Wenn der Benutzer einen Kurs auswählt, werden zugehörige Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-203">When the user selects a course, related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="48c66-204">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-204">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="48c66-205">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-205">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model"></a><span data-ttu-id="48c66-206">Erstellen von Anzeigemodellen</span><span class="sxs-lookup"><span data-stu-id="48c66-206">Create a view model</span></span>

<span data-ttu-id="48c66-207">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-207">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="48c66-208">Ein Ansichtsmodell ist erforderlich, das drei Eigenschaften enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-208">A view model is needed that includes three properties representing the three tables.</span></span>

<span data-ttu-id="48c66-209">Erstellen *SchoolViewModels/InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-209">Create *SchoolViewModels/InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-instructor-pages"></a><span data-ttu-id="48c66-210">Gerüstbau von Instructor-Seiten</span><span class="sxs-lookup"><span data-stu-id="48c66-210">Scaffold Instructor pages</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48c66-211">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48c66-211">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="48c66-212">Befolgen Sie die Anweisungen unter [Gerüstbau der Student-Seiten](xref:data/ef-rp/intro#scaffold-student-pages) mit den folgenden Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="48c66-212">Follow the instructions in [Scaffold the student pages](xref:data/ef-rp/intro#scaffold-student-pages) with the following exceptions:</span></span>

  * <span data-ttu-id="48c66-213">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="48c66-213">Create a *Pages/Instructors* folder.</span></span>
  * <span data-ttu-id="48c66-214">Verwenden Sie `Instructor` als Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="48c66-214">Use `Instructor` for the model class.</span></span>
  * <span data-ttu-id="48c66-215">Verwenden Sie die vorhandene Kontextklasse, anstatt eine neue Klasse zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-215">Use the existing context class instead of creating a new one.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48c66-216">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48c66-216">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="48c66-217">Erstellen Sie einen Ordner *Pages/Instructors*.</span><span class="sxs-lookup"><span data-stu-id="48c66-217">Create a *Pages/Instructors* folder.</span></span>

* <span data-ttu-id="48c66-218">Führen Sie den folgenden Befehl aus, um das Gerüst für Instructor-Seiten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-218">Run the following command to scaffold the Instructor pages.</span></span>

  <span data-ttu-id="48c66-219">**Unter Windows:**</span><span class="sxs-lookup"><span data-stu-id="48c66-219">**On Windows:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

  <span data-ttu-id="48c66-220">**Unter Linux oder macOS:**</span><span class="sxs-lookup"><span data-stu-id="48c66-220">**On Linux or macOS:**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages/Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="48c66-221">Führen Sie die App aus, und navigieren Sie zur Seite Instructors, um zu sehen, wie die Gerüstbauseite aussieht, bevor Sie sie aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="48c66-221">To see what the scaffolded page looks like before you update it, run the app and navigate to the Instructors page.</span></span>

<span data-ttu-id="48c66-222">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-222">Update *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,19-53)]

<span data-ttu-id="48c66-223">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="48c66-223">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="48c66-224">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="48c66-224">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading)]

<span data-ttu-id="48c66-225">Der Code gibt Eager Loading für die folgenden Navigationseigenschaften an:</span><span class="sxs-lookup"><span data-stu-id="48c66-225">The code specifies eager loading for the following navigation properties:</span></span>

* `Instructor.OfficeAssignment`
* `Instructor.CourseAssignments`
  * `CourseAssignments.Course`
    * `Course.Department`
    * `Course.Enrollments`
      * `Enrollment.Student`

<span data-ttu-id="48c66-226">Beachten Sie die Wiederholung der `Include`- und `ThenInclude`-Methoden für `CourseAssignments` und `Course`.</span><span class="sxs-lookup"><span data-stu-id="48c66-226">Notice the repetition of `Include` and `ThenInclude` methods for `CourseAssignments` and `Course`.</span></span> <span data-ttu-id="48c66-227">Diese Wiederholung ist erforderlich, um Eager Loading für zwei Navigationseigenschaften der `Course`-Entität anzugeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-227">This repetition is necessary to specify eager loading for two navigation properties of the `Course` entity.</span></span>

<span data-ttu-id="48c66-228">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="48c66-228">The following code executes when an instructor is selected (`id != null`).</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectInstructor)]

<span data-ttu-id="48c66-229">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-229">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="48c66-230">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="48c66-230">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="48c66-231">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="48c66-231">The `Where` method returns a collection.</span></span> <span data-ttu-id="48c66-232">In diesem Fall wählt der Filter jedoch eine einzelne Entität aus, sodass die `Single`-Methode aufgerufen wird, um die Sammlung in eine einzelne `Instructor`-Entität zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="48c66-232">But in this case, the filter will select a single entity, so the `Single` method is called to convert the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="48c66-233">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="48c66-233">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="48c66-234">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="48c66-234">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="48c66-236">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="48c66-236">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="48c66-237">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-237">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="48c66-238">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-238">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span>

<span data-ttu-id="48c66-239">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="48c66-239">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_SelectCourse)]

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="48c66-240">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="48c66-240">Update the instructors Index page</span></span>

<span data-ttu-id="48c66-241">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="48c66-241">Update *Pages/Instructors/Index.cshtml* with the following code.</span></span>

[!code-cshtml[](intro/samples/cu30/Pages/Instructors/Index.cshtml?highlight=1,5,8,16-21,25-32,43-57,67-102,104-126)]

<span data-ttu-id="48c66-242">Durch den vorangehenden Code werden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="48c66-242">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="48c66-243">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="48c66-243">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="48c66-244">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="48c66-244">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="48c66-245">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="48c66-245">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="48c66-246">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="48c66-246">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `https://localhost:5001/Instructors?id=2`

  <span data-ttu-id="48c66-247">Wenn die Seitenanweisung `@page "{id:int?}"` ist, lautet die URL folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="48c66-247">When the page directive is `@page "{id:int?}"`, the URL is:</span></span>

  `https://localhost:5001/Instructors/2`

* <span data-ttu-id="48c66-248">Fügt eine **Office**-Spalte hinzu, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-248">Adds an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="48c66-249">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="48c66-249">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="48c66-250">Fügt eine **Courses**-Spalte hinzu, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-250">Adds a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="48c66-251">Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="48c66-251">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="48c66-252">Fügt Code hinzu, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten und Kurses hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-252">Adds code that dynamically adds `class="success"` to the `tr` element of the selected instructor and course.</span></span> <span data-ttu-id="48c66-253">Hiermit wird mit einer Bootstrapklasse eine Hintergrundfarbe für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-253">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="48c66-254">Fügt einen neuen Link mit der Bezeichnung **Select** hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-254">Adds a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="48c66-255">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="48c66-255">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

* <span data-ttu-id="48c66-256">Fügt eine Tabelle mit Kursen für den ausgewählten Dozenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-256">Adds a table of courses for the selected Instructor.</span></span>

* <span data-ttu-id="48c66-257">Fügt eine Tabelle mit Studentenregistrierungen für den ausgewählten Kurs hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-257">Adds a table of student enrollments for the selected course.</span></span>

<span data-ttu-id="48c66-258">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="48c66-258">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="48c66-259">Wenn `OfficeAssignment` NULL ist, wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-259">If `OfficeAssignment` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="48c66-260">Klicken Sie auf den Link **Select** für einen Dozenten.</span><span class="sxs-lookup"><span data-stu-id="48c66-260">Click on the **Select** link for an instructor.</span></span> <span data-ttu-id="48c66-261">Der Zeilenstil ändert sich, und Kurse, die diesem Dozenten zugewiesen sind, werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-261">The row style changes and courses assigned to that instructor are displayed.</span></span>

<span data-ttu-id="48c66-262">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="48c66-262">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index30.png)

## <a name="using-single"></a><span data-ttu-id="48c66-264">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="48c66-264">Using Single</span></span>

<span data-ttu-id="48c66-265">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="48c66-265">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="48c66-266">Die Verwendung von `Single` mit einer Where-Bedingung ist eine Frage der persönlichen Präferenz.</span><span class="sxs-lookup"><span data-stu-id="48c66-266">Use of `Single` with a Where condition is a matter of personal preference.</span></span> <span data-ttu-id="48c66-267">Sie bietet keine Vorteile gegenüber der Verwendung der `Where`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-267">It provides no benefits over using the `Where` method.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="48c66-268">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="48c66-268">Explicit loading</span></span>

<span data-ttu-id="48c66-269">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="48c66-269">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu30snapshots/6-related/Pages/Instructors/Index1.cshtml.cs?name=snippet_EagerLoading&highlight=6-9)]

<span data-ttu-id="48c66-270">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="48c66-270">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="48c66-271">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-271">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="48c66-272">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="48c66-272">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="48c66-273">Aktualisieren Sie *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="48c66-273">Update *Pages/Instructors/Index.cshtml.cs* with the following code.</span></span>

[!code-csharp[](intro/samples/cu30/Pages/Instructors/Index.cshtml.cs?highlight=31-35,52-56)]

<span data-ttu-id="48c66-274">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="48c66-274">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="48c66-275">Wenn ein Kurs ausgewählt ist, ruft der explizit geladene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="48c66-275">If a course is selected, the explicit loading code retrieves:</span></span>

* <span data-ttu-id="48c66-276">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="48c66-276">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="48c66-277">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="48c66-277">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="48c66-278">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="48c66-278">Notice that the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="48c66-279">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-279">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="48c66-280">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="48c66-280">Test the app.</span></span> <span data-ttu-id="48c66-281">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="48c66-281">From a user's perspective, the app behaves identically to the previous version.</span></span>

## <a name="next-steps"></a><span data-ttu-id="48c66-282">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="48c66-282">Next steps</span></span>

<span data-ttu-id="48c66-283">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-283">The next tutorial shows how to update related data.</span></span>

>[!div class="step-by-step"]
><span data-ttu-id="48c66-284">[Vorheriges Tutorial](xref:data/ef-rp/complex-data-model)
>[Nächstes Tutorial](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="48c66-284">[Previous tutorial](xref:data/ef-rp/complex-data-model)
[Next tutorial](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="48c66-285">In diesem Tutorial werden verwandte Daten gelesen und angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-285">In this tutorial, related data is read and displayed.</span></span> <span data-ttu-id="48c66-286">Verwandte Daten sind Daten, die von EF Core in die Navigationseigenschaften geladen werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-286">Related data is data that EF Core loads into navigation properties.</span></span>

<span data-ttu-id="48c66-287">Wenn nicht zu lösende Probleme auftreten, laden Sie die [fertige App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) herunter, oder zeigen Sie diese an.</span><span class="sxs-lookup"><span data-stu-id="48c66-287">If you run into problems you can't solve, [download or view the completed app.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)</span></span> <span data-ttu-id="48c66-288">[Anweisungen zum Download.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="48c66-288">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

<span data-ttu-id="48c66-289">Die folgenden Abbildungen zeigen die abgeschlossenen Seiten für dieses Tutorial:</span><span class="sxs-lookup"><span data-stu-id="48c66-289">The following illustrations show the completed pages for this tutorial:</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

## <a name="eager-explicit-and-lazy-loading-of-related-data"></a><span data-ttu-id="48c66-292">Explizites, Eager und Lazy Loading verwandter Daten</span><span class="sxs-lookup"><span data-stu-id="48c66-292">Eager, explicit, and lazy Loading of related data</span></span>

<span data-ttu-id="48c66-293">Es gibt mehrere Möglichkeiten, mit denen EF Core verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="48c66-293">There are several ways that EF Core can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="48c66-294">[Eager Loading (vorzeitiges Laden)](/ef/core/querying/related-data#eager-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-294">[Eager loading](/ef/core/querying/related-data#eager-loading).</span></span> <span data-ttu-id="48c66-295">Beim Eager Loading lädt eine Abfrage für einen Entitätstyp auch verwandte Entitäten.</span><span class="sxs-lookup"><span data-stu-id="48c66-295">Eager loading is when a query for one type of entity also loads related entities.</span></span> <span data-ttu-id="48c66-296">Wenn die Entität gelesen wird, werden ihre verwandten Daten abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-296">When the entity is read, its related data is retrieved.</span></span> <span data-ttu-id="48c66-297">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="48c66-297">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="48c66-298">EF Core wird mehrere Abfragen für einige Typen von Eager Loading ausgeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-298">EF Core will issue multiple queries for some types of eager loading.</span></span> <span data-ttu-id="48c66-299">Die Ausgabe mehrerer Abfragen kann effizienter sein, als dies bei einigen Abfragen in EF6 der Fall war. Dort war nur eine einzelne Abfrage vorhanden.</span><span class="sxs-lookup"><span data-stu-id="48c66-299">Issuing multiple queries can be more efficient than was the case for some queries in EF6 where there was a single query.</span></span> <span data-ttu-id="48c66-300">Eager Loading wird mit den `Include`- und `ThenInclude`-Methoden angegeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-300">Eager loading is specified with the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)
 
  <span data-ttu-id="48c66-302">Eager Loading sendet mehrere Abfragen, wenn eine Sammlungsnavigation enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="48c66-302">Eager loading sends multiple queries when a collection navigation is included:</span></span>

  * <span data-ttu-id="48c66-303">Eine Abfrage für die Hauptabfrage</span><span class="sxs-lookup"><span data-stu-id="48c66-303">One query for the main query</span></span> 
  * <span data-ttu-id="48c66-304">Eine Abfrage für jeden „Sammlungsrand“ in der Ladestruktur.</span><span class="sxs-lookup"><span data-stu-id="48c66-304">One query for each collection "edge" in the load tree.</span></span>

* <span data-ttu-id="48c66-305">Beispiel für separate Abfragen mit `Load`: Die Daten können in separaten Abfragen abgerufen werden. EF Core korrigiert die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="48c66-305">Separate queries with `Load`: The data can be retrieved in separate queries, and EF Core "fixes up" the navigation properties.</span></span> <span data-ttu-id="48c66-306">„Korrigieren“ bedeutet, dass EF Core die Navigationseigenschaften automatisch füllt.</span><span class="sxs-lookup"><span data-stu-id="48c66-306">"fixes up" means that EF Core automatically populates the navigation properties.</span></span> <span data-ttu-id="48c66-307">Separate Abfragen mit `Load` ähneln mehr dem expliziten Laden als dem Eager Loading.</span><span class="sxs-lookup"><span data-stu-id="48c66-307">Separate queries with `Load` is more like explicit loading than eager loading.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

  <span data-ttu-id="48c66-309">Hinweis: EF Core korrigiert automatisch Navigationseigenschaften für alle anderen Entitäten, die zuvor in die Kontextinstanz geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="48c66-309">Note: EF Core automatically fixes up navigation properties to any other entities that were previously loaded into the context instance.</span></span> <span data-ttu-id="48c66-310">Auch wenn die Daten für eine Navigationseigenschaft *nicht* explizit eingeschlossen sind, kann die Eigenschaft immer noch aufgefüllt werden, wenn einige oder alle verwandten Entitäten zuvor geladen wurden.</span><span class="sxs-lookup"><span data-stu-id="48c66-310">Even if the data for a navigation property is *not* explicitly included, the property may still be populated if some or all of the related entities were previously loaded.</span></span>

* <span data-ttu-id="48c66-311">[Explizites Laden](/ef/core/querying/related-data#explicit-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-311">[Explicit loading](/ef/core/querying/related-data#explicit-loading).</span></span> <span data-ttu-id="48c66-312">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-312">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="48c66-313">Es muss Code geschrieben werden, um die verwandten Daten bei Bedarf abzurufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-313">Code must be written to retrieve the related data when it's needed.</span></span> <span data-ttu-id="48c66-314">Explizites Laden mit separaten Abfragen führt zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-314">Explicit loading with separate queries results in multiple queries sent to the DB.</span></span> <span data-ttu-id="48c66-315">Mit explizitem Laden gibt der Code die zu ladenden Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="48c66-315">With explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="48c66-316">Verwenden Sie für explizites Laden die `Load`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-316">Use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="48c66-317">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="48c66-317">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="48c66-319">[Lazy Loading (verzögertes Laden)](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-319">[Lazy loading](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="48c66-320">[Lazy Loading wurde in Version 2.1 zu EF Core hinzugefügt](/ef/core/querying/related-data#lazy-loading).</span><span class="sxs-lookup"><span data-stu-id="48c66-320">[Lazy loading was added to EF Core in version 2.1](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="48c66-321">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-321">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="48c66-322">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-322">The first time a navigation property is accessed, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="48c66-323">Wenn zum ersten Mal auf eine Navigationseigenschaft zugegriffen wird, wird jedes Mal eine Abfrage an die Datenbank geschickt.</span><span class="sxs-lookup"><span data-stu-id="48c66-323">A query is sent to the DB each time a navigation property is accessed for the first time.</span></span>

* <span data-ttu-id="48c66-324">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="48c66-324">The `Select` operator loads only the related data needed.</span></span>

## <a name="create-a-course-page-that-displays-department-name"></a><span data-ttu-id="48c66-325">Erstellen einer Kursseite, die den Abteilungsnamen anzeigt</span><span class="sxs-lookup"><span data-stu-id="48c66-325">Create a Course page that displays department name</span></span>

<span data-ttu-id="48c66-326">Die Course-Entität enthält eine Navigationseigenschaft, welche die `Department`-Entität enthält.</span><span class="sxs-lookup"><span data-stu-id="48c66-326">The Course entity includes a navigation property that contains the `Department` entity.</span></span> <span data-ttu-id="48c66-327">Die `Department`-Entität enthält die Abteilung, der der Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-327">The `Department` entity contains the department that the course is assigned to.</span></span>

<span data-ttu-id="48c66-328">So zeigen Sie den Namen der zugewiesenen Abteilung in einer Kursliste an:</span><span class="sxs-lookup"><span data-stu-id="48c66-328">To display the name of the assigned department in a list of courses:</span></span>

* <span data-ttu-id="48c66-329">Rufen Sie `Name`-Eigenschaft aus der `Department`-Entität ab.</span><span class="sxs-lookup"><span data-stu-id="48c66-329">Get the `Name` property from the `Department` entity.</span></span>
* <span data-ttu-id="48c66-330">Die `Department`-Entität stammt aus der `Course.Department`-Navigationseigenschaft.</span><span class="sxs-lookup"><span data-stu-id="48c66-330">The `Department` entity comes from the `Course.Department` navigation property.</span></span>

![Course.Department](read-related-data/_static/dep-crs.png)

<a name="scaffold"></a>

### <a name="scaffold-the-course-model"></a><span data-ttu-id="48c66-332">Erstellen des Gerüsts für das Kursmodell</span><span class="sxs-lookup"><span data-stu-id="48c66-332">Scaffold the Course model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48c66-333">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48c66-333">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="48c66-334">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Course` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="48c66-334">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Course` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48c66-335">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48c66-335">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="48c66-336">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="48c66-336">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Course -dc SchoolContext -udl -outDir Pages\Courses --referenceScriptLibraries
  ```

---

<span data-ttu-id="48c66-337">Der vorherige Befehl erstellt ein Gerüst für das `Course`-Modell.</span><span class="sxs-lookup"><span data-stu-id="48c66-337">The preceding command scaffolds the `Course` model.</span></span> <span data-ttu-id="48c66-338">Öffnen Sie das Projekt in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="48c66-338">Open the project in Visual Studio.</span></span>

<span data-ttu-id="48c66-339">Öffnen Sie *Pages/Courses/Index.cshtml.cs*. Untersuchen Sie die `OnGetAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="48c66-339">Open *Pages/Courses/Index.cshtml.cs* and examine the `OnGetAsync` method.</span></span> <span data-ttu-id="48c66-340">Die Gerüstbauengine gibt Eager Loading für die `Department`-Navigationseigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="48c66-340">The scaffolding engine specified eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="48c66-341">Die `Include`-Methode gibt Eager Loading an.</span><span class="sxs-lookup"><span data-stu-id="48c66-341">The `Include` method specifies eager loading.</span></span>

<span data-ttu-id="48c66-342">Führen Sie die Anwendung aus, und klicken Sie auf den Link **Kurse**.</span><span class="sxs-lookup"><span data-stu-id="48c66-342">Run the app and select the **Courses** link.</span></span> <span data-ttu-id="48c66-343">Die Abteilungsspalte zeigt die `DepartmentID` an, die nicht hilfreich ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-343">The department column displays the `DepartmentID`, which isn't useful.</span></span>

<span data-ttu-id="48c66-344">Aktualisieren Sie die `OnGetAsync`-Methode mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-344">Update the `OnGetAsync` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="48c66-345">Der vorangehende Code fügt `AsNoTracking` hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-345">The preceding code adds `AsNoTracking`.</span></span> <span data-ttu-id="48c66-346">`AsNoTracking` verbessert die Leistung, da die zurückgegebenen Entitäten nicht nachverfolgt werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-346">`AsNoTracking` improves performance because the entities returned are not tracked.</span></span> <span data-ttu-id="48c66-347">Die Entitäten werden nicht nachverfolgt, da sie nicht im aktuellen Kontext aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-347">The entities are not tracked because they're not updated in the current context.</span></span>

<span data-ttu-id="48c66-348">Aktualisieren Sie *Pages/Courses/Index.cshtml* mit dem folgenden hervorgehobenen Markup:</span><span class="sxs-lookup"><span data-stu-id="48c66-348">Update *Pages/Courses/Index.cshtml* with the following highlighted markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="48c66-349">Die folgenden Änderungen wurden am Codegerüst vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="48c66-349">The following changes have been made to the scaffolded code:</span></span>

* <span data-ttu-id="48c66-350">Die Überschrift wurde von „Index“ in „Kurse“ geändert.</span><span class="sxs-lookup"><span data-stu-id="48c66-350">Changed the heading from Index to Courses.</span></span>
* <span data-ttu-id="48c66-351">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="48c66-351">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="48c66-352">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="48c66-352">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="48c66-353">Allerdings hat der Primärschlüssel in diesem Fall jedoch Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="48c66-353">However, in this case the primary key is meaningful.</span></span>
* <span data-ttu-id="48c66-354">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-354">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="48c66-355">Der Code zeigt die `Name`-Eigenschaft der `Department`-Entität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="48c66-355">The code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="48c66-356">Führen Sie die Anwendung aus. Wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="48c66-356">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

<a name="select"></a>

### <a name="loading-related-data-with-select"></a><span data-ttu-id="48c66-358">Laden verwandter Daten mit „Select“</span><span class="sxs-lookup"><span data-stu-id="48c66-358">Loading related data with Select</span></span>

<span data-ttu-id="48c66-359">Die `OnGetAsync`-Methode lädt verwandte Daten mit der `Include`-Methode:</span><span class="sxs-lookup"><span data-stu-id="48c66-359">The `OnGetAsync` method loads related data with the `Include` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/Index.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="48c66-360">Der `Select`-Operator lädt nur die erforderlichen verwandten Daten.</span><span class="sxs-lookup"><span data-stu-id="48c66-360">The `Select` operator loads only the related data needed.</span></span> <span data-ttu-id="48c66-361">Für die einzelnen Elemente wie `Department.Name` wird ein INNER JOIN von SQL verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-361">For single items, like the `Department.Name` it uses a SQL INNER JOIN.</span></span> <span data-ttu-id="48c66-362">Für Sammlungen wird ein anderer Zugriff auf die Datenbank verwendet, aber Gleiches gilt für den `Include`-Operator für Sammlungen.</span><span class="sxs-lookup"><span data-stu-id="48c66-362">For collections, it uses another database access, but so does the `Include` operator on collections.</span></span>

<span data-ttu-id="48c66-363">Der folgende Code lädt verwandte Daten mit der `Select`-Methode:</span><span class="sxs-lookup"><span data-stu-id="48c66-363">The following code loads related data with the `Select` method:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs?name=snippet_RevisedIndexMethod&highlight=4)]

<span data-ttu-id="48c66-364">Die `CourseViewModel`:</span><span class="sxs-lookup"><span data-stu-id="48c66-364">The `CourseViewModel`:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/CourseViewModel.cs?name=snippet)]

<span data-ttu-id="48c66-365">Ein vollständiges Beispiel finden Sie unter [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) und [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs).</span><span class="sxs-lookup"><span data-stu-id="48c66-365">See [IndexSelect.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml) and [IndexSelect.cshtml.cs](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu/Pages/Courses/IndexSelect.cshtml.cs) for a complete example.</span></span>

## <a name="create-an-instructors-page-that-shows-courses-and-enrollments"></a><span data-ttu-id="48c66-366">Erstellen einer Dozentenseite, die Kurse und Registrierungen anzeigt</span><span class="sxs-lookup"><span data-stu-id="48c66-366">Create an Instructors page that shows Courses and Enrollments</span></span>

<span data-ttu-id="48c66-367">In diesem Abschnitt wird die Dozentenseite erstellt.</span><span class="sxs-lookup"><span data-stu-id="48c66-367">In this section, the Instructors page is created.</span></span>

<a name="IP"></a>
<span data-ttu-id="48c66-368">![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)</span><span class="sxs-lookup"><span data-stu-id="48c66-368">![Instructors Index page](read-related-data/_static/instructors-index.png)</span></span>

<span data-ttu-id="48c66-369">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="48c66-369">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="48c66-370">Die Liste der Dozenten zeigt verwandte Daten aus der `OfficeAssignment`-Entität (Office in der vorherigen Abbildung).</span><span class="sxs-lookup"><span data-stu-id="48c66-370">The list of instructors displays related data from the `OfficeAssignment` entity (Office in the preceding image).</span></span> <span data-ttu-id="48c66-371">Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-371">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="48c66-372">Eager Loading wird für die `OfficeAssignment`-Entitäten verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-372">Eager loading is used for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="48c66-373">Eager Loading ist in der Regel effizienter, wenn die verwandten Daten angezeigt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="48c66-373">Eager loading is typically more efficient when the related data needs to be displayed.</span></span> <span data-ttu-id="48c66-374">In diesem Fall werden die Office-Zuweisungen für die Dozenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-374">In this case, office assignments for the instructors are displayed.</span></span>
* <span data-ttu-id="48c66-375">Wenn der Benutzer einen Dozenten auswählt (Harui in der vorherigen Abbildung), werden verwandte `Course`-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-375">When the user selects an instructor (Harui in the preceding image), related `Course` entities are displayed.</span></span> <span data-ttu-id="48c66-376">Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-376">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="48c66-377">Für die `Course`-Entitäten und ihre verwandten `Department`-Entitäten wird das Eager Loading verwendet.</span><span class="sxs-lookup"><span data-stu-id="48c66-377">Eager loading is used for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="48c66-378">In diesem Fall können separate Abfragen effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-378">In this case, separate queries might be more efficient because only courses for the selected instructor are needed.</span></span> <span data-ttu-id="48c66-379">Dieses Beispiel zeigt, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="48c66-379">This example shows how to use eager loading for navigation properties in entities that are in navigation properties.</span></span>
* <span data-ttu-id="48c66-380">Wenn der Benutzer einen Kurs auswählt (Chemie in der vorherigen Abbildung), werden verwandte Daten aus der `Enrollments`-Entität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-380">When the user selects a course (Chemistry in the preceding image), related data from the `Enrollments` entity is displayed.</span></span> <span data-ttu-id="48c66-381">In der vorherigen Abbildung sind der Name des Studenten und die Note angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-381">In the preceding image, student name and grade are displayed.</span></span> <span data-ttu-id="48c66-382">Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="48c66-382">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="48c66-383">Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="48c66-383">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="48c66-384">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-384">The instructors page shows data from three different tables.</span></span> <span data-ttu-id="48c66-385">Es wird ein Ansichtsmodell erstellt, das die drei Entitäten enthält, die die drei Tabellen darstellen.</span><span class="sxs-lookup"><span data-stu-id="48c66-385">A view model is created that includes the three entities representing the three tables.</span></span>

<span data-ttu-id="48c66-386">Erstellen Sie im Ordner *SchoolViewModels* die Datei *InstructorIndexData.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-386">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="scaffold-the-instructor-model"></a><span data-ttu-id="48c66-387">Gerüstbau für das Dozentenmodell</span><span class="sxs-lookup"><span data-stu-id="48c66-387">Scaffold the Instructor model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="48c66-388">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="48c66-388">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="48c66-389">Führen Sie die Schritte unter [Erstellen des Gerüsts für das Studentenmodell](xref:data/ef-rp/intro#scaffold-the-student-model) aus, und verwenden Sie `Instructor` für die Modellklasse.</span><span class="sxs-lookup"><span data-stu-id="48c66-389">Follow the instructions in [Scaffold the student model](xref:data/ef-rp/intro#scaffold-the-student-model) and use `Instructor` for the model class.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="48c66-390">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="48c66-390">Visual Studio Code</span></span>](#tab/visual-studio-code)

 <span data-ttu-id="48c66-391">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="48c66-391">Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Instructor -dc SchoolContext -udl -outDir Pages\Instructors --referenceScriptLibraries
  ```

---

<span data-ttu-id="48c66-392">Der vorherige Befehl erstellt ein Gerüst für das `Instructor`-Modell.</span><span class="sxs-lookup"><span data-stu-id="48c66-392">The preceding command scaffolds the `Instructor` model.</span></span> 
<span data-ttu-id="48c66-393">Führen Sie die Anwendung aus, und navigieren Sie zur Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="48c66-393">Run the app and navigate to the instructors page.</span></span>

<span data-ttu-id="48c66-394">Ersetzen Sie *Pages/Instructors/Index.cshtml.cs* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-394">Replace *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_all&highlight=2,18-99)]

<span data-ttu-id="48c66-395">Die `OnGetAsync`-Methode akzeptiert optional Routendaten für die ID des ausgewählten Dozenten.</span><span class="sxs-lookup"><span data-stu-id="48c66-395">The `OnGetAsync` method accepts optional route data for the ID of the selected instructor.</span></span>

<span data-ttu-id="48c66-396">Untersuchen Sie die Abfrage in der Datei *Pages/Instructors/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="48c66-396">Examine the query in the *Pages/Instructors/Index.cshtml.cs* file:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index1.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="48c66-397">Die Abfrage enthält zwei Dinge:</span><span class="sxs-lookup"><span data-stu-id="48c66-397">The query has two includes:</span></span>

* <span data-ttu-id="48c66-398">`OfficeAssignment`: In der [Dozentenansicht](#IP) angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-398">`OfficeAssignment`: Displayed in the [instructors view](#IP).</span></span>
* <span data-ttu-id="48c66-399">`CourseAssignments`: Welche Kurse gegeben werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-399">`CourseAssignments`: Which brings in the courses taught.</span></span>

### <a name="update-the-instructors-index-page"></a><span data-ttu-id="48c66-400">Aktualisieren der Indexseite „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="48c66-400">Update the instructors Index page</span></span>

<span data-ttu-id="48c66-401">Aktualisieren Sie *Pages/Instructors/Index.cshtml* mit folgendem Markup:</span><span class="sxs-lookup"><span data-stu-id="48c66-401">Update *Pages/Instructors/Index.cshtml* with the following markup:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=1-65&highlight=1,5,8,16-21,25-32,43-57)]

<span data-ttu-id="48c66-402">Das oben stehende Markup führt die folgenden Änderungen durch:</span><span class="sxs-lookup"><span data-stu-id="48c66-402">The preceding markup makes the following changes:</span></span>

* <span data-ttu-id="48c66-403">Aktualisiert die `page`-Anweisung von `@page` auf `@page "{id:int?}"`.</span><span class="sxs-lookup"><span data-stu-id="48c66-403">Updates the `page` directive from `@page` to `@page "{id:int?}"`.</span></span> <span data-ttu-id="48c66-404">`"{id:int?}"` ist eine Routenvorlage.</span><span class="sxs-lookup"><span data-stu-id="48c66-404">`"{id:int?}"` is a route template.</span></span> <span data-ttu-id="48c66-405">Die Routenvorlage ändert ganzzahlige Abfragezeichenfolgen in der URL in Routendaten.</span><span class="sxs-lookup"><span data-stu-id="48c66-405">The route template changes integer query strings in the URL to route data.</span></span> <span data-ttu-id="48c66-406">Klicken Sie beispielsweise auf den Link **Auswählen** für einen Dozenten, wenn nur die `@page`-Anweisung eine URL wie die folgende erzeugt:</span><span class="sxs-lookup"><span data-stu-id="48c66-406">For example, clicking on the **Select** link for an instructor with only the `@page` directive produces a URL like the following:</span></span>

  `http://localhost:1234/Instructors?id=2`

  <span data-ttu-id="48c66-407">Wenn die Seitenanweisung `@page "{id:int?}"` ist, sieht die vorherige URL wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="48c66-407">When the page directive is `@page "{id:int?}"`, the previous URL is:</span></span>

  `http://localhost:1234/Instructors/2`

* <span data-ttu-id="48c66-408">Der Seitentitel lautet **Dozenten**.</span><span class="sxs-lookup"><span data-stu-id="48c66-408">Page title is **Instructors**.</span></span>
* <span data-ttu-id="48c66-409">Es wurde eine **Office**-Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich 0 (null) ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-409">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="48c66-410">Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="48c66-410">Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="48c66-411">Es wurde eine **Kurse**-Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-411">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="48c66-412">Weitere Informationen zu dieser Razor-Syntax finden Sie unter [Expliziter Zeilenübergang](xref:mvc/views/razor#explicit-line-transition).</span><span class="sxs-lookup"><span data-stu-id="48c66-412">See [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) for more about this razor syntax.</span></span>

* <span data-ttu-id="48c66-413">Es wurde Code hinzugefügt, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-413">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="48c66-414">Hiermit wird eine Hintergrundfarbe mit einer Bootstrapklasse für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-414">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.CourseID == Model.CourseID)
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="48c66-415">Es wurde ein neuer Link mit der Bezeichnung **Auswählen** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="48c66-415">Added a new hyperlink labeled **Select**.</span></span> <span data-ttu-id="48c66-416">Dieser Link sendet die ID des ausgewählten Dozenten an die `Index`-Methode und legt die Hintergrundfarbe fest.</span><span class="sxs-lookup"><span data-stu-id="48c66-416">This link sends the selected instructor's ID to the `Index` method and sets a background color.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="48c66-417">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt den `Location` (Office) aus der verwandten `OfficeAssignment`-Entität an.</span><span class="sxs-lookup"><span data-stu-id="48c66-417">Run the app and select the **Instructors** tab. The page displays the `Location` (office) from the related `OfficeAssignment` entity.</span></span> <span data-ttu-id="48c66-418">Wenn OfficeAssignment gleich ist 0 (null), wird eine leere Tabellenzelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="48c66-418">If OfficeAssignment\` is null, an empty table cell is displayed.</span></span>

<span data-ttu-id="48c66-419">Klicken Sie auf den Link **Auswählen**.</span><span class="sxs-lookup"><span data-stu-id="48c66-419">Click on the **Select** link.</span></span> <span data-ttu-id="48c66-420">Der Zeilenstil verändert sich.</span><span class="sxs-lookup"><span data-stu-id="48c66-420">The row style changes.</span></span>

### <a name="add-courses-taught-by-selected-instructor"></a><span data-ttu-id="48c66-421">Hinzufügen von Kursen eines ausgewählten Dozenten</span><span class="sxs-lookup"><span data-stu-id="48c66-421">Add courses taught by selected instructor</span></span>

<span data-ttu-id="48c66-422">Aktualisieren Sie die `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-422">Update the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_OnGetAsync&highlight=1,8,16-999)]

<span data-ttu-id="48c66-423">Fügen Sie `public int CourseID { get; set; }` hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-423">Add `public int CourseID { get; set; }`</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_1&highlight=12)]

<span data-ttu-id="48c66-424">Überprüfen Sie die aktualisierte Abfrage:</span><span class="sxs-lookup"><span data-stu-id="48c66-424">Examine the updated query:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ThenInclude)]

<span data-ttu-id="48c66-425">Die vorhergehende Abfrage fügt die `Department`-Entitäten hinzu.</span><span class="sxs-lookup"><span data-stu-id="48c66-425">The preceding query adds the `Department` entities.</span></span>

<span data-ttu-id="48c66-426">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wird (`id != null`).</span><span class="sxs-lookup"><span data-stu-id="48c66-426">The following code executes when an instructor is selected (`id != null`).</span></span> <span data-ttu-id="48c66-427">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="48c66-427">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="48c66-428">Die `Courses`-Eigenschaft des Ansichtsmodells wird mit den `Course`-Entitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="48c66-428">The view model's `Courses` property is loaded with the `Course` entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_ID)]

<span data-ttu-id="48c66-429">Die `Where`-Methode gibt eine Sammlung zurück.</span><span class="sxs-lookup"><span data-stu-id="48c66-429">The `Where` method returns a collection.</span></span> <span data-ttu-id="48c66-430">In der vorherigen `Where`-Methode wird nur eine einzige `Instructor`-Entität zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-430">In the preceding `Where` method, only a single `Instructor` entity is returned.</span></span> <span data-ttu-id="48c66-431">Die `Single`-Methode konvertiert die Sammlung in eine einzelne `Instructor`-Entität.</span><span class="sxs-lookup"><span data-stu-id="48c66-431">The `Single` method converts the collection into a single `Instructor` entity.</span></span> <span data-ttu-id="48c66-432">Die `Instructor`-Entität ermöglicht Zugriff auf die `CourseAssignments`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="48c66-432">The `Instructor` entity provides access to the `CourseAssignments` property.</span></span> <span data-ttu-id="48c66-433">`CourseAssignments` ermöglicht Zugriff auf die verwandten `Course`-Entitäten.</span><span class="sxs-lookup"><span data-stu-id="48c66-433">`CourseAssignments` provides access to the related `Course` entities.</span></span>

![Dozent:Kurse m:N](complex-data-model/_static/courseassignment.png)

<span data-ttu-id="48c66-435">Die `Single`-Methode wird für eine Sammlung verwendet, wenn die Sammlung nur ein Element aufweist.</span><span class="sxs-lookup"><span data-stu-id="48c66-435">The `Single` method is used on a collection when the collection has only one item.</span></span> <span data-ttu-id="48c66-436">Die `Single`-Methode löst eine Ausnahme aus, wenn die Sammlung leer ist oder mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-436">The `Single` method throws an exception if the collection is empty or if there's more than one item.</span></span> <span data-ttu-id="48c66-437">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall 0 (null)) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="48c66-437">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="48c66-438">Für eine leere Sammlung wird `SingleOrDefault` verwendet:</span><span class="sxs-lookup"><span data-stu-id="48c66-438">Using `SingleOrDefault` on an empty collection:</span></span>

* <span data-ttu-id="48c66-439">Löst eine Ausnahme aus (auf der Suche nach einer `Courses`-Eigenschaft eines Nullverweises).</span><span class="sxs-lookup"><span data-stu-id="48c66-439">Results in an exception (from trying to find a `Courses` property on a null reference).</span></span>
* <span data-ttu-id="48c66-440">Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben.</span><span class="sxs-lookup"><span data-stu-id="48c66-440">The exception message would less clearly indicate the cause of the problem.</span></span>

<span data-ttu-id="48c66-441">Wenn ein Kurs ausgewählt ist, füllt der folgende Code die `Enrollments`-Eigenschaft des Ansichtsmodells:</span><span class="sxs-lookup"><span data-stu-id="48c66-441">The following code populates the view model's `Enrollments` property when a course is selected:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index2.cshtml.cs?name=snippet_courseID)]

<span data-ttu-id="48c66-442">Fügen Sie das folgende Markup zum Ende der Razor-Seite *Pages/Instructors/Index.cshtml* hinzu:</span><span class="sxs-lookup"><span data-stu-id="48c66-442">Add the following markup to the end of the *Pages/Instructors/Index.cshtml* Razor Page:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=60-102&highlight=7-999)]

<span data-ttu-id="48c66-443">Das vorhergehende Markup zeigt eine Liste der Kurse eines bestimmten Dozenten an, wenn einer ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="48c66-443">The preceding markup displays a list of courses related to an instructor when an instructor is selected.</span></span>

<span data-ttu-id="48c66-444">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="48c66-444">Test the app.</span></span> <span data-ttu-id="48c66-445">Klicken Sie auf den Link **Auswählen** auf der Dozentenseite.</span><span class="sxs-lookup"><span data-stu-id="48c66-445">Click on a **Select** link on the instructors page.</span></span>

### <a name="show-student-data"></a><span data-ttu-id="48c66-446">Anzeigen der Studentendaten</span><span class="sxs-lookup"><span data-stu-id="48c66-446">Show student data</span></span>

<span data-ttu-id="48c66-447">In diesem Abschnitt wird die Anwendung aktualisiert, um die Studentendaten für einen ausgewählten Kurs anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="48c66-447">In this section, the app is updated to show the student data for a selected course.</span></span>

<span data-ttu-id="48c66-448">Aktualisieren Sie die Abfrage in der `OnGetAsync`-Methode in *Pages/Instructors/Index.cshtml.cs* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-448">Update the query in the `OnGetAsync` method in *Pages/Instructors/Index.cshtml.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="48c66-449">Aktualisieren Sie *Pages/Instructors/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="48c66-449">Update *Pages/Instructors/Index.cshtml*.</span></span> <span data-ttu-id="48c66-450">Fügen Sie dem Dateiende das folgende Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="48c66-450">Add the following markup to the end of the file:</span></span>

[!code-cshtml[](intro/samples/cu/Pages/Instructors/IndexRRD.cshtml?range=103-)]

<span data-ttu-id="48c66-451">Das vorhergehende Markup zeigt eine Liste der Studenten, die im ausgewählten Kurs registriert sind.</span><span class="sxs-lookup"><span data-stu-id="48c66-451">The preceding markup displays a list of the students who are enrolled in the selected course.</span></span>

<span data-ttu-id="48c66-452">Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus.</span><span class="sxs-lookup"><span data-stu-id="48c66-452">Refresh the page and select an instructor.</span></span> <span data-ttu-id="48c66-453">Wählen Sie einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="48c66-453">Select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="using-single"></a><span data-ttu-id="48c66-455">Verwenden von „Single“</span><span class="sxs-lookup"><span data-stu-id="48c66-455">Using Single</span></span>

<span data-ttu-id="48c66-456">Die `Single`-Methode kann die `Where`-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="48c66-456">The `Single` method can pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexSingle.cshtml.cs?name=snippet_single&highlight=21-22,30-31)]

<span data-ttu-id="48c66-457">Der vorangehende `Single`-Ansatz bietet keine Vorteile gegenüber `Where`.</span><span class="sxs-lookup"><span data-stu-id="48c66-457">The preceding `Single` approach provides no benefits over using `Where`.</span></span> <span data-ttu-id="48c66-458">Einige Entwickler bevorzugen einfach den `Single`-Ansatz.</span><span class="sxs-lookup"><span data-stu-id="48c66-458">Some developers prefer the `Single` approach style.</span></span>

## <a name="explicit-loading"></a><span data-ttu-id="48c66-459">Explizites Laden</span><span class="sxs-lookup"><span data-stu-id="48c66-459">Explicit loading</span></span>

<span data-ttu-id="48c66-460">Der aktuelle Code gibt Eager Loading für `Enrollments` und `Students` an:</span><span class="sxs-lookup"><span data-stu-id="48c66-460">The current code specifies eager loading for `Enrollments` and `Students`:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/Index.cshtml.cs?name=snippet_ThenInclude&highlight=6-9)]

<span data-ttu-id="48c66-461">Angenommen, Benutzer möchten Registrierungen für einen Kurs nur selten anzeigen lassen.</span><span class="sxs-lookup"><span data-stu-id="48c66-461">Suppose users rarely want to see enrollments in a course.</span></span> <span data-ttu-id="48c66-462">In diesem Fall wäre es eine Optimierung, die Registrierungsdaten nur dann zu laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-462">In that case, an optimization would be to only load the enrollment data if it's requested.</span></span> <span data-ttu-id="48c66-463">In diesem Abschnitt wird die `OnGetAsync` aktualisiert, um das explizite Laden von `Enrollments` und `Students` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="48c66-463">In this section, the `OnGetAsync` is updated to use explicit loading of `Enrollments` and `Students`.</span></span>

<span data-ttu-id="48c66-464">Aktualisieren Sie `OnGetAsync` mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="48c66-464">Update the `OnGetAsync` with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Pages/Instructors/IndexXp.cshtml.cs?name=snippet_OnGetAsync&highlight=9-13,29-35)]

<span data-ttu-id="48c66-465">Der vorangehende Code löscht die *ThenInclude*-Methodenaufrufe für Registrierung und Studentendaten.</span><span class="sxs-lookup"><span data-stu-id="48c66-465">The preceding code drops the *ThenInclude* method calls for enrollment and student data.</span></span> <span data-ttu-id="48c66-466">Wenn ein Kurs ausgewählt ist, ruft der hervorgehobene Code Folgendes ab:</span><span class="sxs-lookup"><span data-stu-id="48c66-466">If a course is selected, the highlighted code retrieves:</span></span>

* <span data-ttu-id="48c66-467">Die `Enrollment`-Entitäten für den ausgewählten Kurs.</span><span class="sxs-lookup"><span data-stu-id="48c66-467">The `Enrollment` entities for the selected course.</span></span>
* <span data-ttu-id="48c66-468">Die `Student`-Entitäten für jede `Enrollment`.</span><span class="sxs-lookup"><span data-stu-id="48c66-468">The `Student` entities for each `Enrollment`.</span></span>

<span data-ttu-id="48c66-469">Beachten Sie, dass der vorherige Code `.AsNoTracking()` auskommentiert.</span><span class="sxs-lookup"><span data-stu-id="48c66-469">Notice the preceding code comments out `.AsNoTracking()`.</span></span> <span data-ttu-id="48c66-470">Navigationseigenschaften können nur für nachverfolgte Entitäten explizit geladen werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-470">Navigation properties can only be explicitly loaded for tracked entities.</span></span>

<span data-ttu-id="48c66-471">Testen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="48c66-471">Test the app.</span></span> <span data-ttu-id="48c66-472">Aus Benutzersicht verhält sich die Anwendung identisch mit der vorherigen Version.</span><span class="sxs-lookup"><span data-stu-id="48c66-472">From a users perspective, the app behaves identically to the previous version.</span></span>

<span data-ttu-id="48c66-473">Das nächste Tutorial zeigt, wie verwandte Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="48c66-473">The next tutorial shows how to update related data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="48c66-474">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="48c66-474">Additional resources</span></span>

* [<span data-ttu-id="48c66-475">Dieses Tutorial auf YouTube (Teil 1)</span><span class="sxs-lookup"><span data-stu-id="48c66-475">YouTube version of this tutorial (part1)</span></span>](https://www.youtube.com/watch?v=PzKimUDmrvE)
* [<span data-ttu-id="48c66-476">Dieses Tutorial auf YouTube (Teil 2)</span><span class="sxs-lookup"><span data-stu-id="48c66-476">YouTube version of this tutorial (part2)</span></span>](https://www.youtube.com/watch?v=xvDDrIHv5ko)

>[!div class="step-by-step"]
><span data-ttu-id="48c66-477">[Zurück](xref:data/ef-rp/complex-data-model)
>[Weiter](xref:data/ef-rp/update-related-data)</span><span class="sxs-lookup"><span data-stu-id="48c66-477">[Previous](xref:data/ef-rp/complex-data-model)
[Next](xref:data/ef-rp/update-related-data)</span></span>

::: moniker-end
