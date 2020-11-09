---
title: 'Tutorial: Lesen verwandter Daten: ASP.NET Core MVC mit EF Core'
description: In diesem Tutorial lesen Sie verwandte Daten und zeigen sie an – d.h., die Daten, die Entity Framework in Navigationseigenschaften lädt.
author: rick-anderson
ms.author: riande
ms.date: 09/28/2019
ms.topic: tutorial
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
uid: data/ef-mvc/read-related-data
ms.openlocfilehash: 610a9e9b0007fb468ea9cdae6fadd2e756de4290
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054058"
---
# <a name="tutorial-read-related-data---aspnet-mvc-with-ef-core"></a><span data-ttu-id="f58a8-103">Tutorial: Lesen verwandter Daten: ASP.NET Core MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="f58a8-103">Tutorial: Read related data - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="f58a8-104">Im vorherigen Tutorial haben Sie das Schuldatenmodell abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-104">In the previous tutorial, you completed the School data model.</span></span> <span data-ttu-id="f58a8-105">In diesem Tutorial lesen Sie verwandte Daten und zeigen sie an – d.h., die Daten, die Entity Framework in Navigationseigenschaften lädt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-105">In this tutorial, you'll read and display related data -- that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="f58a8-106">Die folgenden Abbildungen zeigen die Seiten, mit den Sie arbeiten werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-106">The following illustrations show the pages that you'll work with.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

<span data-ttu-id="f58a8-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="f58a8-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f58a8-110">Erfahren Sie, wie verwandte Daten geladen werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-110">Learn how to load related data</span></span>
> * <span data-ttu-id="f58a8-111">Erstellen Sie eine Seite „Kurse“</span><span class="sxs-lookup"><span data-stu-id="f58a8-111">Create a Courses page</span></span>
> * <span data-ttu-id="f58a8-112">Erstellen Sie eine Seite „Instructors“</span><span class="sxs-lookup"><span data-stu-id="f58a8-112">Create an Instructors page</span></span>
> * <span data-ttu-id="f58a8-113">Erfahren Sie mehr über das explizite Laden</span><span class="sxs-lookup"><span data-stu-id="f58a8-113">Learn about explicit loading</span></span>

## <a name="prerequisites"></a><span data-ttu-id="f58a8-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="f58a8-114">Prerequisites</span></span>

* [<span data-ttu-id="f58a8-115">Erstellen eines komplexen Datenmodells</span><span class="sxs-lookup"><span data-stu-id="f58a8-115">Create a complex data model</span></span>](complex-data-model.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="f58a8-116">Erfahren Sie, wie verwandte Daten geladen werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-116">Learn how to load related data</span></span>

<span data-ttu-id="f58a8-117">Es gibt mehrere Möglichkeiten, mit denen Software für objektrelationales Mapping (ORM), z.B. Entity Framework, verwandte Daten in die Navigationseigenschaften einer Entität laden kann:</span><span class="sxs-lookup"><span data-stu-id="f58a8-117">There are several ways that Object-Relational Mapping (ORM) software such as Entity Framework can load related data into the navigation properties of an entity:</span></span>

* <span data-ttu-id="f58a8-118">Eager Loading (vorzeitiges Laden).</span><span class="sxs-lookup"><span data-stu-id="f58a8-118">Eager loading.</span></span> <span data-ttu-id="f58a8-119">Wenn die Entität gelesen wird, werden ihre verwandten Daten mit ihr abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-119">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="f58a8-120">Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="f58a8-120">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="f58a8-121">Sie geben Eager Loading in Entity Framework Core mit den `Include`- und `ThenInclude`-Methoden an.</span><span class="sxs-lookup"><span data-stu-id="f58a8-121">You specify eager loading in Entity Framework Core by using the `Include` and `ThenInclude` methods.</span></span>

  ![Beispiel für Eager Loading](read-related-data/_static/eager-loading.png)

  <span data-ttu-id="f58a8-123">Sie können einige der Daten in separaten Abfragen abrufen und EF „korrigiert“ die Navigationseigenschaften.</span><span class="sxs-lookup"><span data-stu-id="f58a8-123">You can retrieve some of the data in separate queries, and EF "fixes up" the navigation properties.</span></span>  <span data-ttu-id="f58a8-124">D.h., dass EF automatisch die separat abgerufenen Entitäten in die Navigationseigenschaften der zuvor abgerufenen Entitäten hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-124">That is, EF automatically adds the separately retrieved entities where they belong in navigation properties of previously retrieved entities.</span></span> <span data-ttu-id="f58a8-125">Für die Abfrage, die verwandte Daten abruft, können Sie die `Load`-Methode verwenden, anstelle einer Methode, die eine Liste oder ein Objekt wie `ToList` oder `Single` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-125">For the query that retrieves related data, you can use the `Load` method instead of a method that returns a list or object, such as `ToList` or `Single`.</span></span>

  ![Beispiel für separate Abfragen](read-related-data/_static/separate-queries.png)

* <span data-ttu-id="f58a8-127">Explizites Laden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-127">Explicit loading.</span></span> <span data-ttu-id="f58a8-128">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-128">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="f58a8-129">Sie schreiben Code, der die verwandten Daten abruft, wenn erforderlich.</span><span class="sxs-lookup"><span data-stu-id="f58a8-129">You write code that retrieves the related data if it's needed.</span></span> <span data-ttu-id="f58a8-130">So wie im Fall von Eager Loading mit separaten Abfragen führt explizites Laden zu mehreren Abfragen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-130">As in the case of eager loading with separate queries, explicit loading results in multiple queries sent to the database.</span></span> <span data-ttu-id="f58a8-131">Der Unterschied liegt darin, dass beim expliziten Laden der Code die zu ladenden Navigationseigenschaften angibt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-131">The difference is that with explicit loading, the code specifies the navigation properties to be loaded.</span></span> <span data-ttu-id="f58a8-132">In Entity Framework Core 1.1 können Sie die `Load`-Methode verwenden, um das explizite Laden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-132">In Entity Framework Core 1.1 you can use the `Load` method to do explicit loading.</span></span> <span data-ttu-id="f58a8-133">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f58a8-133">For example:</span></span>

  ![Beispiel für explizites Laden](read-related-data/_static/explicit-loading.png)

* <span data-ttu-id="f58a8-135">Lazy Loading (verzögertes Laden).</span><span class="sxs-lookup"><span data-stu-id="f58a8-135">Lazy loading.</span></span> <span data-ttu-id="f58a8-136">Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-136">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="f58a8-137">Wenn Sie jedoch zum ersten Mal versuchen, auf eine Navigationseigenschaft zuzugreifen, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-137">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="f58a8-138">Jedes Mal, wenn Sie zum ersten mal versuchen, Daten von einer Navigationseigenschaft abzurufen, wird eine Anfrage an die Datenbank gesendet.</span><span class="sxs-lookup"><span data-stu-id="f58a8-138">A query is sent to the database each time you try to get data from a navigation property for the first time.</span></span> <span data-ttu-id="f58a8-139">Entity Framework Core 1.0 unterstützt das Lazy Loading nicht.</span><span class="sxs-lookup"><span data-stu-id="f58a8-139">Entity Framework Core 1.0 doesn't support lazy loading.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="f58a8-140">Überlegungen zur Leistung</span><span class="sxs-lookup"><span data-stu-id="f58a8-140">Performance considerations</span></span>

<span data-ttu-id="f58a8-141">Wenn Sie wissen, dass Sie für jede abgerufene Entität verwandte Daten benötigen, bietet Eager Loading häufig die beste Leistung, da eine einzelne Abfrage, die an die Datenbank gesendet wird, in der Regel effizienter ist als separate Abfragen für jede abgerufene Entität.</span><span class="sxs-lookup"><span data-stu-id="f58a8-141">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="f58a8-142">Nehmen wir beispielsweise an, dass jede Abteilung zehn verwandte Kurse hat.</span><span class="sxs-lookup"><span data-stu-id="f58a8-142">For example, suppose that each department has ten related courses.</span></span> <span data-ttu-id="f58a8-143">Eager Loading aller verwandter Daten würde zu nur einer einzelnen (Join)-Abfrage und einem einzelnen Roundtrip zur Datenbank führen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-143">Eager loading of all related data would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="f58a8-144">Eine separate Abfrage für Kurse jeder Abteilung würde zu elf Roundtrips zur Datenbank führen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-144">A separate query for courses for each department would result in eleven round trips to the database.</span></span> <span data-ttu-id="f58a8-145">Die zusätzlichen Roundtrips zur Datenbank beeinträchtigen die Leistung besonders bei hoher Latenz.</span><span class="sxs-lookup"><span data-stu-id="f58a8-145">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="f58a8-146">Andererseits sind separate Abfragen in einigen Szenarios jedoch effizienter.</span><span class="sxs-lookup"><span data-stu-id="f58a8-146">On the other hand, in some scenarios separate queries is more efficient.</span></span> <span data-ttu-id="f58a8-147">Eager Loading aller verwandter Daten in einer Abfrage könnte eine sehr komplexe Verknüpfung generieren, die der SQL Server nicht effizient verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="f58a8-147">Eager loading of all related data in one query might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="f58a8-148">Oder angenommen, Sie benötigen nur Zugriff auf Navigationseigenschaften einer Entität, um auf eine Teilmenge der Reihe von Entitäten, die Sie verarbeiten, zuzugreifen. In diesem Fall können separate Abfragen möglicherweise besser ausgeführt werden, da Eager Loading aller Elemente im Vorfeld mehr Daten als benötigt abrufen würde.</span><span class="sxs-lookup"><span data-stu-id="f58a8-148">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, separate queries might perform better because eager loading of everything up front would retrieve more data than you need.</span></span> <span data-ttu-id="f58a8-149">Wenn die Leistung wichtig ist, empfiehlt es sich, die Leistung mit beiden Möglichkeiten zu testen, um die beste Wahl treffen zu können.</span><span class="sxs-lookup"><span data-stu-id="f58a8-149">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

## <a name="create-a-courses-page"></a><span data-ttu-id="f58a8-150">Erstellen Sie eine Seite „Kurse“</span><span class="sxs-lookup"><span data-stu-id="f58a8-150">Create a Courses page</span></span>

<span data-ttu-id="f58a8-151">Die Kursentität enthält eine Navigationseigenschaft, die die Abteilungsentität der Abteilung enthält, der der Kurs zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-151">The Course entity includes a navigation property that contains the Department entity of the department that the course is assigned to.</span></span> <span data-ttu-id="f58a8-152">Sie benötigen die Namenseigenschaft der Abteilungsentität in der `Course.Department`-Navigationseigenschaft, um den Namen der zugewiesenen Abteilung in einer Kursliste anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-152">To display the name of the assigned department in a list of courses, you need to get the Name property from the Department entity that's in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="f58a8-153">Erstellen Sie einen Controller mit dem Namen CoursesController für den Kursentitätstyp. Verwenden Sie dieselben Optionen für den **MVC-Controller mit Ansichten unter Verwendung des Entity Framework** -Gerüstbauers, die Sie zuvor für den Studentencontroller verwendet haben, wie in der folgenden Abbildung gezeigt:</span><span class="sxs-lookup"><span data-stu-id="f58a8-153">Create a controller named CoursesController for the Course entity type, using the same options for the **MVC Controller with views, using Entity Framework** scaffolder that you did earlier for the Students controller, as shown in the following illustration:</span></span>

![Hinzufügen eines Kursecontrollers](read-related-data/_static/add-courses-controller.png)

<span data-ttu-id="f58a8-155">Öffnen Sie *CoursesController.cs* , und untersuchen Sie die `Index`-Methode.</span><span class="sxs-lookup"><span data-stu-id="f58a8-155">Open *CoursesController.cs* and examine the `Index` method.</span></span> <span data-ttu-id="f58a8-156">Der automatische Gerüstbau hat mithilfe der `Include`-Methode ein Eager Loading für die `Department`-Navigationseigenschaft angegeben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-156">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="f58a8-157">Ersetzen Sie die `Index`-Methode durch den folgenden Code, der einen geeigneteren Namen für `IQueryable` verwendet, der Kursentitäten (`courses` anstelle von `schoolContext`) zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="f58a8-157">Replace the `Index` method with the following code that uses a more appropriate name for the `IQueryable` that returns Course entities (`courses` instead of `schoolContext`):</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_RevisedIndexMethod)]

<span data-ttu-id="f58a8-158">Öffnen Sie *Views/Courses/Index.cshtml*. Ersetzen Sie den Vorlagencode durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="f58a8-158">Open *Views/Courses/Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="f58a8-159">Die Änderungen werden hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="f58a8-159">The changes are highlighted:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/Index.cshtml?highlight=4,7,15-17,34-36,44)]

<span data-ttu-id="f58a8-160">Sie haben die folgenden Änderungen am eingerüsteten Code vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="f58a8-160">You've made the following changes to the scaffolded code:</span></span>

* <span data-ttu-id="f58a8-161">Die Überschrift wurde von „Index“ in „Kurse“ geändert.</span><span class="sxs-lookup"><span data-stu-id="f58a8-161">Changed the heading from Index to Courses.</span></span>

* <span data-ttu-id="f58a8-162">Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an.</span><span class="sxs-lookup"><span data-stu-id="f58a8-162">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="f58a8-163">Primärschlüssel werden nicht standardmäßig eingerüstet, da sie normalerweise ohne Bedeutung für Endbenutzer sind.</span><span class="sxs-lookup"><span data-stu-id="f58a8-163">By default, primary keys aren't scaffolded because normally they're meaningless to end users.</span></span> <span data-ttu-id="f58a8-164">Allerdings ist in diesem Fall der Primärschlüssel sinnvoll, und Sie möchten ihn anzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-164">However, in this case the primary key is meaningful and you want to show it.</span></span>

* <span data-ttu-id="f58a8-165">Die Spalte **Abteilung** wurde geändert, sodass sie jetzt den Namen der Abteilung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-165">Changed the **Department** column to display the department name.</span></span> <span data-ttu-id="f58a8-166">Der Code zeigt die `Name`-Eigenschaft der Abteilungsentität an, die in die `Department`-Navigationseigenschaft geladen wird:</span><span class="sxs-lookup"><span data-stu-id="f58a8-166">The code displays the `Name` property of the Department entity that's loaded into the `Department` navigation property:</span></span>

  ```html
  @Html.DisplayFor(modelItem => item.Department.Name)
  ```

<span data-ttu-id="f58a8-167">Führen Sie die Anwendung aus, und wählen Sie die Registerkarte **Kurse** aus, um die Liste mit den Abteilungsnamen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-167">Run the app and select the **Courses** tab to see the list with department names.</span></span>

![Indexseite „Kurse“](read-related-data/_static/courses-index.png)

## <a name="create-an-instructors-page"></a><span data-ttu-id="f58a8-169">Erstellen Sie eine Seite „Instructors“</span><span class="sxs-lookup"><span data-stu-id="f58a8-169">Create an Instructors page</span></span>

<span data-ttu-id="f58a8-170">In diesem Abschnitt müssen Sie einen Controller und eine Ansicht für die Instructor-Entität erstellen, um die Dozentenseite anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="f58a8-170">In this section, you'll create a controller and view for the Instructor entity in order to display the Instructors page:</span></span>

![Indexseite „Dozenten“](read-related-data/_static/instructors-index.png)

<span data-ttu-id="f58a8-172">Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f58a8-172">This page reads and displays related data in the following ways:</span></span>

* <span data-ttu-id="f58a8-173">Die Liste der Dozenten zeigt verwandte Daten aus der OfficeAssignment-Entität.</span><span class="sxs-lookup"><span data-stu-id="f58a8-173">The list of instructors displays related data from the OfficeAssignment entity.</span></span> <span data-ttu-id="f58a8-174">Die Instructor- und OfficeAssignment-Entitäten sind in einer 1:0..1-Beziehung.</span><span class="sxs-lookup"><span data-stu-id="f58a8-174">The Instructor and OfficeAssignment entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="f58a8-175">Sie werden Eager Loading für die OfficeAssignment-Entitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-175">You'll use eager loading for the OfficeAssignment entities.</span></span> <span data-ttu-id="f58a8-176">Wie zuvor erläutert, ist Eager Loading in der Regel effizienter, wenn Sie die verwandten Daten für alle abgerufenen Zeilen der primären Tabelle benötigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-176">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="f58a8-177">In diesem Fall sollten Sie die Office-Anweisungen für alle angezeigten Dozenten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-177">In this case, you want to display office assignments for all displayed instructors.</span></span>

* <span data-ttu-id="f58a8-178">Wenn der Benutzer einen Dozenten auswählt, werden verwandte Course-Entitäten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-178">When the user selects an instructor, related Course entities are displayed.</span></span> <span data-ttu-id="f58a8-179">Die Instructor- und Course-Entitäten stehen in einer m:n-Beziehung zueinander.</span><span class="sxs-lookup"><span data-stu-id="f58a8-179">The Instructor and Course entities are in a many-to-many relationship.</span></span> <span data-ttu-id="f58a8-180">Sie werden Eager Loading für die Kursentitäten und ihre zugehörigen Abteilungsentitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-180">You'll use eager loading for the Course entities and their related Department entities.</span></span> <span data-ttu-id="f58a8-181">In diesem Fall können separate Abfrage effizienter sein, da nur Kurse für den ausgewählten Dozenten benötigt werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-181">In this case, separate queries might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="f58a8-182">Dieses Beispiel zeigt jedoch, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f58a8-182">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>

* <span data-ttu-id="f58a8-183">Wenn der Benutzer einen Kurs auswählt, werden verwandte Daten aus der Registrierungsentität angezeigt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-183">When the user selects a course, related data from the Enrollments entity set is displayed.</span></span> <span data-ttu-id="f58a8-184">Die Kurs- und Registrierungsentitäten sind in einer 1:n-Beziehung.</span><span class="sxs-lookup"><span data-stu-id="f58a8-184">The Course and Enrollment entities are in a one-to-many relationship.</span></span> <span data-ttu-id="f58a8-185">Sie werden separate Abfragen für Registrierungsentitäten und ihre verwandten Studentenentitäten verwenden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-185">You'll use separate queries for Enrollment entities and their related Student entities.</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="f58a8-186">Erstellen eines Ansichtsmodells für die Indexansicht „Dozenten“</span><span class="sxs-lookup"><span data-stu-id="f58a8-186">Create a view model for the Instructor Index view</span></span>

<span data-ttu-id="f58a8-187">Die Dozentenseite zeigt Daten aus drei verschiedenen Tabellen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-187">The Instructors page shows data from three different tables.</span></span> <span data-ttu-id="f58a8-188">Aus diesem Grund erstellen Sie ein Ansichtsmodell, das drei Eigenschaften enthält. Jede enthält Daten für eine der Tabellen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-188">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="f58a8-189">Erstellen Sie im Ordner *SchoolViewModels* *InstructorIndexData.cs* , und ersetzen Sie den bestehenden Code durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f58a8-189">In the *SchoolViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Models/SchoolViewModels/InstructorIndexData.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="f58a8-190">Erstellen der Dozentencontroller und -ansichten</span><span class="sxs-lookup"><span data-stu-id="f58a8-190">Create the Instructor controller and views</span></span>

<span data-ttu-id="f58a8-191">Erstellen Sie einen Dozentencontroller mit EF-Lese-/Schreibaktionen, wie in der folgenden Abbildung gezeigt:</span><span class="sxs-lookup"><span data-stu-id="f58a8-191">Create an Instructors controller with EF read/write actions as shown in the following illustration:</span></span>

![Hinzufügen von Dozentencontrollern](read-related-data/_static/add-instructors-controller.png)

<span data-ttu-id="f58a8-193">Öffnen Sie *InstructorsController.cs*. Fügen Sie eine Using-Anweisung für den Namespace ViewModels hinzu:</span><span class="sxs-lookup"><span data-stu-id="f58a8-193">Open *InstructorsController.cs* and add a using statement for the ViewModels namespace:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_Using)]

<span data-ttu-id="f58a8-194">Ersetzen Sie die Indexmethode durch den folgenden Code, um Eager Loading verwandter Daten durchzuführen und ihn in das Ansichtsmodell einzufügen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-194">Replace the Index method with the following code to do eager loading of related data and put it in the view model.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_EagerLoading)]

<span data-ttu-id="f58a8-195">Die Methode akzeptiert optionale Routendaten (`id`) und einen Abfragezeichenfolgenparameter (`courseID`), die die ID-Werte des ausgewählten Dozenten und Kurses bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-195">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course.</span></span> <span data-ttu-id="f58a8-196">Die Parameter werden durch die **Auswählen** -Links auf der Seite bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-196">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="f58a8-197">Der Code erstellt zuerst eine Instanz des Ansichtsmodells und fügt die Dozentenliste ein.</span><span class="sxs-lookup"><span data-stu-id="f58a8-197">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="f58a8-198">Der Code gibt Eager Loading für die `Instructor.OfficeAssignment`- und `Instructor.CourseAssignments`-Navigationseigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="f58a8-198">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.CourseAssignments` navigation properties.</span></span> <span data-ttu-id="f58a8-199">Innerhalb der `CourseAssignments`-Eigenschaft wird die `Course`-Eigenschaft geladen, und innerhalb dieser werden die `Enrollments`- und `Department`-Eigenschaften geladen, und innerhalb jeder `Enrollment`-Entität wird die `Student`-Eigenschaft geladen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-199">Within the `CourseAssignments` property, the `Course` property is loaded, and within that, the `Enrollments` and `Department` properties are loaded, and within each `Enrollment` entity the `Student` property is loaded.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude)]

<span data-ttu-id="f58a8-200">Da die Ansicht immer die OfficeAssignment-Entität erfordert, ist es effizienter, sie in derselben Abfrage abzurufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-200">Since the view always requires the OfficeAssignment entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="f58a8-201">Kursentitäten sind erforderlich, wenn auf der Webseite ein Dozent ausgewählt ist. Somit ist eine einzelne Abfrage nur besser als mehrere Abfragen, wenn die Seite häufiger mit einem ausgewählten Kurs als ohne angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f58a8-201">Course entities are required when an instructor is selected in the web page, so a single query is better than multiple queries only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="f58a8-202">Der Code wiederholt `CourseAssignments` und `Course`, da Sie zwei Eigenschaften aus `Course` benötigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-202">The code repeats `CourseAssignments` and `Course` because you need two properties from `Course`.</span></span> <span data-ttu-id="f58a8-203">Die erste Zeichenfolge der `ThenInclude`-Abrufe erhält `CourseAssignment.Course`, `Course.Enrollments` und `Enrollment.Student`.</span><span class="sxs-lookup"><span data-stu-id="f58a8-203">The first string of `ThenInclude` calls gets `CourseAssignment.Course`, `Course.Enrollments`, and `Enrollment.Student`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=3-6)]

<span data-ttu-id="f58a8-204">An diesem Punkt im Code wäre eine andere `ThenInclude` für Navigationseigenschaften von `Student`, die Sie nicht benötigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-204">At that point in the code, another `ThenInclude` would be for navigation properties of `Student`, which you don't need.</span></span> <span data-ttu-id="f58a8-205">Aber ein Aufruf von `Include` beginnt mit `Instructor`-Eigenschaften neu. Daher müssen Sie den Vorgang erneut durchlaufen und `Course.Department` anstelle von `Course.Enrollments` angeben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-205">But calling `Include` starts over with `Instructor` properties, so you have to go through the chain again, this time specifying `Course.Department` instead of `Course.Enrollments`.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ThenInclude&highlight=7-9)]

<span data-ttu-id="f58a8-206">Der folgende Code wird ausgeführt, wenn ein Dozent ausgewählt wurde.</span><span class="sxs-lookup"><span data-stu-id="f58a8-206">The following code executes when an instructor was selected.</span></span> <span data-ttu-id="f58a8-207">Der ausgewählte Dozent wird aus der Liste der Dozenten im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-207">The selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="f58a8-208">Die `Courses`-Eigenschaft des Ansichtsmodells wird dann mit den Kursentitäten aus der `CourseAssignments`-Navigationseigenschaft dieses Dozenten geladen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-208">The view model's `Courses` property is then loaded with the Course entities from that instructor's `CourseAssignments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=56-62)]

<span data-ttu-id="f58a8-209">Die `Where`-Methode gibt eine Sammlung zurück. Aber in diesem Fall resultieren die an diese Methode übergebenen Kriterien nur in einer einzigen zurückgegebenen Instructor-Entität.</span><span class="sxs-lookup"><span data-stu-id="f58a8-209">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single Instructor entity being returned.</span></span> <span data-ttu-id="f58a8-210">Die `Single`-Methode konvertiert die Sammlung in eine einzelne Instructor-Entität, die Ihnen Zugriff auf die `CourseAssignments`-Eigenschaft dieser Entität gibt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-210">The `Single` method converts the collection into a single Instructor entity, which gives you access to that entity's `CourseAssignments` property.</span></span> <span data-ttu-id="f58a8-211">Die `CourseAssignments`-Eigenschaft enthält `CourseAssignment`-Entitäten, aus der Sie nur die verwandten `Course`-Entitäten benötigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-211">The `CourseAssignments` property contains `CourseAssignment` entities, from which you want only the related `Course` entities.</span></span>

<span data-ttu-id="f58a8-212">Verwenden Sie die `Single`-Methode für eine Sammlung, wenn Sie wissen, dass die Sammlung nur über ein Element verfügt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-212">You use the `Single` method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="f58a8-213">Die Single-Methode löst eine Ausnahme aus, wenn die Sammlung, an die übergeben wurde, leer ist, oder wenn mehr als ein Element vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-213">The Single method throws an exception if the collection passed to it's empty or if there's more than one item.</span></span> <span data-ttu-id="f58a8-214">Eine Alternative ist `SingleOrDefault`, womit ein Standardwert (in diesem Fall null) zurückgegeben wird, wenn die Sammlung leer ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-214">An alternative is `SingleOrDefault`, which returns a default value (null in this case) if the collection is empty.</span></span> <span data-ttu-id="f58a8-215">In diesem Fall würde jedoch trotzdem eine Ausnahme ausgelöst werden (da versucht wird, eine `Courses`-Eigenschaft auf einem Null-Verweis zu finden). Die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-215">However, in this case that would still result in an exception (from trying to find a `Courses` property on a null reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="f58a8-216">Wenn Sie die `Single`-Methode aufrufen, können Sie auch in die Where-Bedingung übergehen, anstatt die `Where`-Methode separat aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="f58a8-216">When you call the `Single` method, you can also pass in the Where condition instead of calling the `Where` method separately:</span></span>

```csharp
.Single(i => i.ID == id.Value)
```

<span data-ttu-id="f58a8-217">anstelle von:</span><span class="sxs-lookup"><span data-stu-id="f58a8-217">Instead of:</span></span>

```csharp
.Where(i => i.ID == id.Value).Single()
```

<span data-ttu-id="f58a8-218">Wenn ein Kurs ausgewählt wurde, wird der ausgewählte Kurs aus der Kursliste im Ansichtsmodell abgerufen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-218">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="f58a8-219">Die `Enrollments`-Eigenschaft des Ansichtsmodells wird dann mit den Registrierungsentitäten aus der `Enrollments`-Navigationseigenschaft dieses Kurses geladen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-219">Then the view model's `Enrollments` property is loaded with the Enrollment entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?range=64-69)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="f58a8-220">Ändern der Dozentenindexansicht</span><span class="sxs-lookup"><span data-stu-id="f58a8-220">Modify the Instructor Index view</span></span>

<span data-ttu-id="f58a8-221">Ersetzen Sie in *Views/Instructors/Index.cshtml* den Vorlagencode durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="f58a8-221">In *Views/Instructors/Index.cshtml* , replace the template code with the following code.</span></span> <span data-ttu-id="f58a8-222">Die Änderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-222">The changes are highlighted.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=1-64&highlight=1,3-7,15-19,24,26-31,41-54,56)]

<span data-ttu-id="f58a8-223">Sie haben die folgenden Änderungen am bestehenden Code vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="f58a8-223">You've made the following changes to the existing code:</span></span>

* <span data-ttu-id="f58a8-224">Die Modellklasse wurde zu `InstructorIndexData` geändert.</span><span class="sxs-lookup"><span data-stu-id="f58a8-224">Changed the model class to `InstructorIndexData`.</span></span>

* <span data-ttu-id="f58a8-225">Der Seitenname wurde von **Index** in **Dozenten** geändert.</span><span class="sxs-lookup"><span data-stu-id="f58a8-225">Changed the page title from **Index** to **Instructors**.</span></span>

* <span data-ttu-id="f58a8-226">Es wurde eine **Office** -Spalte hinzugefügt, die `item.OfficeAssignment.Location` nur anzeigt, wenn `item.OfficeAssignment` nicht gleich null ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-226">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` isn't null.</span></span> <span data-ttu-id="f58a8-227">(Da dies eine 1:0..1-Beziehung ist, gibt es möglicherweise keine verwandte OfficeAssignment-Entität.)</span><span class="sxs-lookup"><span data-stu-id="f58a8-227">(Because this is a one-to-zero-or-one relationship, there might not be a related OfficeAssignment entity.)</span></span>

  ```html
  @if (item.OfficeAssignment != null)
  {
      @item.OfficeAssignment.Location
  }
  ```

* <span data-ttu-id="f58a8-228">Es wurde eine **Kurse** -Spalte hinzugefügt, die die Kurse eines jeden Dozenten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-228">Added a **Courses** column that displays courses taught by each instructor.</span></span> <span data-ttu-id="f58a8-229">Weitere Informationen finden Sie im Abschnitt [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) (Expliziter Zeilenübergang) des Razor-Syntaxartikels.</span><span class="sxs-lookup"><span data-stu-id="f58a8-229">For more information, see the [Explicit line transition](xref:mvc/views/razor#explicit-line-transition) section of the Razor syntax article.</span></span>

* <span data-ttu-id="f58a8-230">Es wurde Code hinzugefügt, der `class="success"` dynamisch zum `tr`-Element des ausgewählten Dozenten hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-230">Added code that dynamically adds `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="f58a8-231">Hiermit wird mit einer Bootstrapklasse eine Hintergrundfarbe für die ausgewählte Zeile hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f58a8-231">This sets a background color for the selected row using a Bootstrap class.</span></span>

  ```html
  string selectedRow = "";
  if (item.ID == (int?)ViewData["InstructorID"])
  {
      selectedRow = "success";
  }
  <tr class="@selectedRow">
  ```

* <span data-ttu-id="f58a8-232">Es wurde ein neuer Link mit der Bezeichnung **Auswählen** unmittelbar vor den anderen Links in jeder Zeile hinzugefügt. Dies führt dazu, dass die ID des ausgewählten Dozenten an die `Index`-Methode gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="f58a8-232">Added a new hyperlink labeled **Select** immediately before the other links in each row, which causes the selected instructor's ID to be sent to the `Index` method.</span></span>

  ```html
  <a asp-action="Index" asp-route-id="@item.ID">Select</a> |
  ```

<span data-ttu-id="f58a8-233">Führen Sie die Anwendung aus. Klicken Sie auf die Registerkarte **Dozenten**. Die Seite zeigt die Eigenschaft für den Standort verwandter OfficeAssignment-Entitäten und eine leere Zelle an, wenn keine verwandte OfficeAssignment-Entität vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-233">Run the app and select the **Instructors** tab. The page displays the Location property of related OfficeAssignment entities and an empty table cell when there's no related OfficeAssignment entity.</span></span>

![Indexseite „Dozenten“, nichts ausgewählt](read-related-data/_static/instructors-index-no-selection.png)

<span data-ttu-id="f58a8-235">Fügen Sie in der Datei *Views/Instructors/Index.cshtml* nach dem Schließen des Tabellenelements (am Ende der Datei) den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="f58a8-235">In the *Views/Instructors/Index.cshtml* file, after the closing table element (at the end of the file), add the following code.</span></span> <span data-ttu-id="f58a8-236">Dieser Code zeigt eine Liste der Kurse an, die im Zusammenhang mit einem Dozenten stehen, wenn ein Dozent ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="f58a8-236">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=66-101)]

<span data-ttu-id="f58a8-237">Dieser Code liest die `Courses`-Eigenschaft des Ansichtsmodells, um eine Kursliste anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-237">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="f58a8-238">Er bietet außerdem einen **Auswählen** -Link, der die ID des ausgewählten Kurses an die `Index`-Aktionsmethode sendet.</span><span class="sxs-lookup"><span data-stu-id="f58a8-238">It also provides a **Select** hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="f58a8-239">Aktualisieren Sie die Seite. Wählen Sie einen Dozenten aus.</span><span class="sxs-lookup"><span data-stu-id="f58a8-239">Refresh the page and select an instructor.</span></span> <span data-ttu-id="f58a8-240">Jetzt sehen Sie ein Raster, das die dem Dozenten zugewiesenen Kurse anzeigt. Sie sehen auch den Namen der zugewiesenen Abteilung für jeden Kurs.</span><span class="sxs-lookup"><span data-stu-id="f58a8-240">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

![Indexseite „Dozenten“, Dozent ausgewählt](read-related-data/_static/instructors-index-instructor-selected.png)

<span data-ttu-id="f58a8-242">Fügen Sie den folgenden Code hinzu, nachdem Sie den Codeblock hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-242">After the code block you just added, add the following code.</span></span> <span data-ttu-id="f58a8-243">Dies zeigt eine Liste der Studenten an, die im Kurs registriert sind, wenn dieser Kurs ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="f58a8-243">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[](intro/samples/cu/Views/Instructors/Index1.cshtml?range=103-125)]

<span data-ttu-id="f58a8-244">Dieser Code liest die Registrierungseigenschaft des Ansichtsmodells, um eine Liste der in diesem Kurs registrierten Studenten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-244">This code reads the Enrollments property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="f58a8-245">Aktualisieren Sie die Seite erneut. Wählen Sie einen Dozenten aus.</span><span class="sxs-lookup"><span data-stu-id="f58a8-245">Refresh the page again and select an instructor.</span></span> <span data-ttu-id="f58a8-246">Wählen Sie dann einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.</span><span class="sxs-lookup"><span data-stu-id="f58a8-246">Then select a course to see the list of enrolled students and their grades.</span></span>

![Indexseite „Dozenten“, Dozent und Kurs ausgewählt](read-related-data/_static/instructors-index.png)

## <a name="about-explicit-loading"></a><span data-ttu-id="f58a8-248">Informationen zum expliziten Laden</span><span class="sxs-lookup"><span data-stu-id="f58a8-248">About explicit loading</span></span>

<span data-ttu-id="f58a8-249">Wenn Sie die Liste der Dozenten aus *InstructorsController.cs* abrufen, haben Sie Eager Loading für die `CourseAssignments`-Navigationseigenschaft angegeben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-249">When you retrieved the list of instructors in *InstructorsController.cs* , you specified eager loading for the `CourseAssignments` navigation property.</span></span>

<span data-ttu-id="f58a8-250">Angenommen, Sie haben erwartet, dass Benutzer nur selten Registrierungen für einen ausgewählten Dozenten und Kurs angezeigt haben möchten.</span><span class="sxs-lookup"><span data-stu-id="f58a8-250">Suppose you expected users to only rarely want to see enrollments in a selected instructor and course.</span></span> <span data-ttu-id="f58a8-251">In diesem Fall möchten Sie die Registrierungsdaten möglicherweise nur laden, wenn diese angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="f58a8-251">In that case, you might want to load the enrollment data only if it's requested.</span></span> <span data-ttu-id="f58a8-252">Ersetzen Sie die `Index`-Methode durch folgenden Code, um ein Beispiel für Eager Loading zu sehen. Dieser Code entfernt das Eager Loading für Registrierungen und lädt diese Eigenschaft explizit.</span><span class="sxs-lookup"><span data-stu-id="f58a8-252">To see an example of how to do explicit loading, replace the `Index` method with the following code, which removes eager loading for Enrollments and loads that property explicitly.</span></span> <span data-ttu-id="f58a8-253">Die Codeänderungen werden hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-253">The code changes are highlighted.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/InstructorsController.cs?name=snippet_ExplicitLoading&highlight=23-29)]

<span data-ttu-id="f58a8-254">Der neue Code löscht die *ThenInclude* -Methodenaufrufe für Registrierungsdaten aus dem Code, der Instructor-Entitäten abruft.</span><span class="sxs-lookup"><span data-stu-id="f58a8-254">The new code drops the *ThenInclude* method calls for enrollment data from the code that retrieves instructor entities.</span></span> <span data-ttu-id="f58a8-255">Außerdem wird `AsNoTracking` gelöscht.</span><span class="sxs-lookup"><span data-stu-id="f58a8-255">It also drops `AsNoTracking`.</span></span>  <span data-ttu-id="f58a8-256">Wenn ein Dozent und Kurs ausgewählt werden, ruft der hervorgehobene Code Registrierungsentitäten für den ausgewählten Kurs und Studentenentitäten für jede Registrierung ab.</span><span class="sxs-lookup"><span data-stu-id="f58a8-256">If an instructor and course are selected, the highlighted code retrieves Enrollment entities for the selected course, and Student entities for each Enrollment.</span></span>

<span data-ttu-id="f58a8-257">Führen Sie die Anwendung aus, navigieren Sie zur Dozentenindexseite, und Sie werden keinen Unterschied in der Anzeige auf der Seite bemerken, obwohl Sie die Abrufart für Daten geändert haben.</span><span class="sxs-lookup"><span data-stu-id="f58a8-257">Run the app, go to the Instructors Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="f58a8-258">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="f58a8-258">Get the code</span></span>

[<span data-ttu-id="f58a8-259">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="f58a8-259">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-steps"></a><span data-ttu-id="f58a8-260">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f58a8-260">Next steps</span></span>

<span data-ttu-id="f58a8-261">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="f58a8-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="f58a8-262">Haben Sie gelernt, wie verwandte Daten geladen werden</span><span class="sxs-lookup"><span data-stu-id="f58a8-262">Learned how to load related data</span></span>
> * <span data-ttu-id="f58a8-263">Wurde eine Seite „Kurse“ erstellt</span><span class="sxs-lookup"><span data-stu-id="f58a8-263">Created a Courses page</span></span>
> * <span data-ttu-id="f58a8-264">Wurde eine Seite „Instructors“ erstellt</span><span class="sxs-lookup"><span data-stu-id="f58a8-264">Created an Instructors page</span></span>
> * <span data-ttu-id="f58a8-265">Haben Sie mehr über das explizite Laden erfahren</span><span class="sxs-lookup"><span data-stu-id="f58a8-265">Learned about explicit loading</span></span>

<span data-ttu-id="f58a8-266">Fahren Sie mit dem nächsten Tutorial fort, um mehr über das Aktualisieren zugehöriger Daten zu erfahren.</span><span class="sxs-lookup"><span data-stu-id="f58a8-266">Advance to the next tutorial to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="f58a8-267">Aktualisieren relevanter Daten</span><span class="sxs-lookup"><span data-stu-id="f58a8-267">Update related data</span></span>](update-related-data.md)
