---
title: 'Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core'
description: In diesem Tutorial werden wichtige Themen eingeführt, um Grundkenntnisse der Entwicklung von ASP.NET Core-Web-Apps, die Entity Framework Core verwenden, zu erweitern.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
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
uid: data/ef-mvc/advanced
ms.openlocfilehash: 386be395399bf4131e4b6c8cac8221f994e8b7c5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054384"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a><span data-ttu-id="06029-103">Tutorial: Erfahren Sie mehr über erweiterte Szenarien: ASP.NET MVC mit EF Core</span><span class="sxs-lookup"><span data-stu-id="06029-103">Tutorial: Learn about advanced scenarios - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="06029-104">Im vorherigen Tutorial haben Sie die „Tabelle pro Hierarchie“-Vererbung implementiert.</span><span class="sxs-lookup"><span data-stu-id="06029-104">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="06029-105">In diesem Tutorial werden verschiedene Themen eingeführt, die beim Entwickeln komplexerer ASP.NET Core-Webanwendungen nützlich sein können, die Entity Framework Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="06029-105">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

<span data-ttu-id="06029-106">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="06029-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="06029-107">Durchführen unformatierter SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="06029-107">Perform raw SQL queries</span></span>
> * <span data-ttu-id="06029-108">Aufrufen einer Abfrage zum Zurückgeben von Entitäten</span><span class="sxs-lookup"><span data-stu-id="06029-108">Call a query to return entities</span></span>
> * <span data-ttu-id="06029-109">Aufrufen einer Abfrage zum Zurückgeben von Typen</span><span class="sxs-lookup"><span data-stu-id="06029-109">Call a query to return other types</span></span>
> * <span data-ttu-id="06029-110">Abrufen einer Aktualisierungsabfrage</span><span class="sxs-lookup"><span data-stu-id="06029-110">Call an update query</span></span>
> * <span data-ttu-id="06029-111">Untersuchen von SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="06029-111">Examine SQL queries</span></span>
> * <span data-ttu-id="06029-112">Erstellen einer Abstraktionsschicht</span><span class="sxs-lookup"><span data-stu-id="06029-112">Create an abstraction layer</span></span>
> * <span data-ttu-id="06029-113">Informationen zur automatischen Änderungserkennung</span><span class="sxs-lookup"><span data-stu-id="06029-113">Learn about Automatic change detection</span></span>
> * <span data-ttu-id="06029-114">Informationen zum EF Core-Quellcode und zu Entwicklungsplänen</span><span class="sxs-lookup"><span data-stu-id="06029-114">Learn about EF Core source code and development plans</span></span>
> * <span data-ttu-id="06029-115">Informationen zum Verwenden dynamischer LINQs zum Vereinfachen des Codes</span><span class="sxs-lookup"><span data-stu-id="06029-115">Learn how to use dynamic LINQ to simplify code</span></span>

## <a name="prerequisites"></a><span data-ttu-id="06029-116">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="06029-116">Prerequisites</span></span>

* [<span data-ttu-id="06029-117">Implementieren von Vererbung</span><span class="sxs-lookup"><span data-stu-id="06029-117">Implement Inheritance</span></span>](inheritance.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="06029-118">Durchführen unformatierter SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="06029-118">Perform raw SQL queries</span></span>

<span data-ttu-id="06029-119">Einer der Vorteile von Entity Framework ist die Tatsache, dass vermieden wird, den Code zu eng an eine bestimmte Methode zum Speichern von Daten zu binden.</span><span class="sxs-lookup"><span data-stu-id="06029-119">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="06029-120">Dies geschieht, indem SQL-Abfragen und -Befehle für Sie generiert werden. Somit müssen Sie sie nicht selbst schreiben.</span><span class="sxs-lookup"><span data-stu-id="06029-120">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="06029-121">Aber es gibt außergewöhnliche Szenarios, für die Sie bestimmte SQL-Abfragen ausführen müssen, die Sie manuell erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="06029-121">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="06029-122">Bei diesen Szenarios enthält die erste API des Entity Framework Code Methoden, mit denen Sie SQL-Befehle direkt an die Datenbank übergeben können.</span><span class="sxs-lookup"><span data-stu-id="06029-122">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="06029-123">In EF Core 1.0 verfügen Sie über die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="06029-123">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="06029-124">Verwenden Sie die `DbSet.FromSql`-Methode für Abfragen, die Entitätstypen zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="06029-124">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="06029-125">Die Typen der zurückgegebenen Objekte müssen den Erwartungen des `DbSet`-Objekts entsprechen, und sie werden automatisch vom Datenbankkontext nachverfolgt, außer wenn Sie die [Überwachung deaktivieren](crud.md#no-tracking-queries).</span><span class="sxs-lookup"><span data-stu-id="06029-125">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="06029-126">Verwenden Sie den `Database.ExecuteSqlCommand` für Nichtabfragebefehle.</span><span class="sxs-lookup"><span data-stu-id="06029-126">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="06029-127">Wenn Sie eine Abfrage ausführen müssen, die Typen zurückgibt, die keine Entitäten sind, können Sie mit der von EF bereitgestellten Datenbankverbindung ADO.NET verwenden.</span><span class="sxs-lookup"><span data-stu-id="06029-127">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="06029-128">Die zurückgegebenen Daten werden nicht vom Datenbankkontext nachverfolgt, auch wenn Sie diese Methode zum Abrufen von Entitätstypen verwenden.</span><span class="sxs-lookup"><span data-stu-id="06029-128">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="06029-129">Dies trifft immer zu, wenn Sie SQL-Befehle in einer Webanwendung ausführen. Treffen Sie deshalb Vorsichtsmaßnahmen, um Ihre Website vor Angriffen durch Einschleusung von SQL-Befehlen zu schützen.</span><span class="sxs-lookup"><span data-stu-id="06029-129">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="06029-130">Verwenden Sie dazu parametrisierte Abfragen, um sicherzustellen, dass Zeichenfolgen, die von einer Webseite übermittelt werden, nicht als SQL-Befehle interpretiert werden können.</span><span class="sxs-lookup"><span data-stu-id="06029-130">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="06029-131">In diesem Tutorial verwenden Sie parametrisierte Abfragen beim Integrieren von Benutzereingaben in eine Abfrage.</span><span class="sxs-lookup"><span data-stu-id="06029-131">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-to-return-entities"></a><span data-ttu-id="06029-132">Aufrufen einer Abfrage zum Zurückgeben von Entitäten</span><span class="sxs-lookup"><span data-stu-id="06029-132">Call a query to return entities</span></span>

<span data-ttu-id="06029-133">Die `DbSet<TEntity>`-Klasse stellt eine Methode zur Verfügung, die Sie verwenden können, um eine Abfrage auszuführen, die eine Entität des Typs `TEntity` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="06029-133">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="06029-134">Ändern Sie den Code in der `Details`-Methode des Abteilungscontrollers, und Sie werden sehen, wie sie funktioniert.</span><span class="sxs-lookup"><span data-stu-id="06029-134">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="06029-135">Ersetzen Sie, wie im folgenden hervorgehobenen Code gezeigt, in *DepartmentsController.cs* in der `Details`-Methode den Code, der eine Abteilung mit einem `FromSql`-Methodenaufruf abruft:</span><span class="sxs-lookup"><span data-stu-id="06029-135">In *DepartmentsController.cs* , in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

<span data-ttu-id="06029-136">Wählen Sie die Registerkarte **Departments** (Abteilungen) und dann **Details** für eine der Abteilungen aus. So können Sie überprüfen, ob der neue Code korrekt funktioniert.</span><span class="sxs-lookup"><span data-stu-id="06029-136">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![Fakultätsdetails](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a><span data-ttu-id="06029-138">Aufrufen einer Abfrage zum Zurückgeben von Typen</span><span class="sxs-lookup"><span data-stu-id="06029-138">Call a query to return other types</span></span>

<span data-ttu-id="06029-139">Sie haben zuvor ein Statistikraster für Studenten für die Infoseite erstellt, das die Anzahl der Studenten für jedes Anmeldedatum zeigt.</span><span class="sxs-lookup"><span data-stu-id="06029-139">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="06029-140">Sie haben die Daten aus der Entitätssammlung für Studenten (`_context.Students`) und LINQ verwendet, um die Ergebnisse in eine Liste von `EnrollmentDateGroup`-Ansichtsmodellobjekten zu projizieren.</span><span class="sxs-lookup"><span data-stu-id="06029-140">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="06029-141">Angenommen, Sie möchten die SQL selbst schreiben, anstatt LINQ zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="06029-141">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="06029-142">Hierzu müssen Sie eine SQL-Abfrage ausführen, die etwas anderes als Entitätsobjekte zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="06029-142">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="06029-143">In EF Core 1.0 gibt es die Möglichkeit, ADO.NET-Code zu schreiben und die Datenbankverbindung von EF abzurufen.</span><span class="sxs-lookup"><span data-stu-id="06029-143">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="06029-144">Ersetzen Sie in *HomeController.cs* die `About`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="06029-144">In *HomeController.cs* , replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="06029-145">Fügen Sie eine Using-Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="06029-145">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="06029-146">Führen Sie die Anwendung aus. Wechseln Sie zur Infoseite.</span><span class="sxs-lookup"><span data-stu-id="06029-146">Run the app and go to the About page.</span></span> <span data-ttu-id="06029-147">Sie zeigt die gleichen Daten wie zuvor.</span><span class="sxs-lookup"><span data-stu-id="06029-147">It displays the same data it did before.</span></span>

![Infoseite](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="06029-149">Abrufen einer Aktualisierungsabfrage</span><span class="sxs-lookup"><span data-stu-id="06029-149">Call an update query</span></span>

<span data-ttu-id="06029-150">Nehmen wir an, dass Administratoren der Contoso University globale Änderungen in der Datenbank durchführen möchten, z.B. die Anzahl der Credits für jeden Kurs ändern.</span><span class="sxs-lookup"><span data-stu-id="06029-150">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="06029-151">Wenn die Universität über eine große Anzahl an Kursen verfügt, wäre es ineffizient, sie alle als Entitäten abzurufen und separat zu ändern.</span><span class="sxs-lookup"><span data-stu-id="06029-151">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="06029-152">In diesem Abschnitt implementieren Sie eine Webseite, die es dem Benutzer ermöglicht, einen Faktor anzugeben, nach dem die Anzahl der Credits für alle Kurse geändert werden kann. Sie werden die Änderung durch die Ausführung einer SQL UPDATE-Anweisung durchführen.</span><span class="sxs-lookup"><span data-stu-id="06029-152">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="06029-153">Die Webseite wird wie die folgende Abbildung aussehen:</span><span class="sxs-lookup"><span data-stu-id="06029-153">The web page will look like the following illustration:</span></span>

![Seite zum Aktualisieren der Credits für Kurse](advanced/_static/update-credits.png)

<span data-ttu-id="06029-155">Fügen Sie in *CoursesController.cs* UpdateCourseCredits-Methoden für HttpGet und HttpPost hinzu:</span><span class="sxs-lookup"><span data-stu-id="06029-155">In *CoursesController.cs* , add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="06029-156">Wenn der Controller eine HttpGet-Anforderung verarbeitet, wird nichts in `ViewData["RowsAffected"]` zurückgegeben, die Ansicht zeigt wie in der vorherigen Abbildung dargestellt ein leeres Textfeld und eine „Absenden“-Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="06029-156">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="06029-157">Wenn auf die Schaltfläche **Aktualisieren** geklickt wird, wird die HttpPost-Methode aufgerufen, und der Multiplikator hat den Wert in das Textfeld eingegeben.</span><span class="sxs-lookup"><span data-stu-id="06029-157">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="06029-158">Der Code führt dann das SQL aus, das Kurse aktualisiert und die Anzahl der betroffenen Zeilen an die Ansicht in `ViewData` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="06029-158">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="06029-159">Wenn die Ansicht einen `RowsAffected`-Wert erhält, zeigt sie die Anzahl der aktualisierten Zeilen an.</span><span class="sxs-lookup"><span data-stu-id="06029-159">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="06029-160">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Ansichten/Kurse* , und klicken Sie anschließend auf **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="06029-160">In **Solution Explorer** , right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="06029-161">Klicken Sie im Dialogfeld **Neues Element hinzufügen** unter **Installiert** im linken Bereich auf **ASP.NET Core** , klicken Sie auf **RazorRazor-Ansicht** , und nennen Sie die neue Ansicht *UpdateCourseCredits.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="06029-161">In the **Add New Item** dialog, click **ASP.NET Core** under **Installed** in the left pane, click **Razor View** , and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="06029-162">Ersetzen Sie in *Views/Courses/UpdateCourseCredits.cshtml* den Vorlagencode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="06029-162">In *Views/Courses/UpdateCourseCredits.cshtml* , replace the template code with the following code:</span></span>

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="06029-163">Führen Sie die `UpdateCourseCredits`-Methode aus, indem Sie die Registerkarte **Courses** (Kurse) auswählen, und dann „/UpdateCourseCredits“ am Ende der URL in die Adressleiste des Browsers einfügen (z.B.: `http://localhost:5813/Courses/UpdateCourseCredits`).</span><span class="sxs-lookup"><span data-stu-id="06029-163">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="06029-164">Geben Sie eine Zahl in das Textfeld ein:</span><span class="sxs-lookup"><span data-stu-id="06029-164">Enter a number in the text box:</span></span>

![Seite zum Aktualisieren der Credits für Kurse](advanced/_static/update-credits.png)

<span data-ttu-id="06029-166">Klicken Sie auf **Aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="06029-166">Click **Update**.</span></span> <span data-ttu-id="06029-167">Die Anzahl der betroffenen Zeilen wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="06029-167">You see the number of rows affected:</span></span>

![Seite zum Aktualisieren der Credits für Kurse, betroffene Zeilen](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="06029-169">Klicken Sie auf **Zurück zur Liste** , um die Kursliste mit der geänderte Anzahl von Credits anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="06029-169">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="06029-170">Beachten Sie, dass der Produktionscode sicherstellen würde, dass Aktualisierungen immer zu gültigen Daten führen.</span><span class="sxs-lookup"><span data-stu-id="06029-170">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="06029-171">Der hier dargestellte vereinfachte Code könnte die Anzahl der Credits so verändern, dass sie Zahlen größer als fünf ergeben.</span><span class="sxs-lookup"><span data-stu-id="06029-171">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="06029-172">(Die `Credits`-Eigenschaft verfügt über ein `[Range(0, 5)]`-Attribut.) Die Updateabfrage würde funktionieren, aber die ungültigen Daten könnten zu unerwarteten Ergebnissen in anderen Teilen des Systems führen, die davon ausgehen, dass die Anzahl der Credits fünf oder weniger beträgt.</span><span class="sxs-lookup"><span data-stu-id="06029-172">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="06029-173">Weitere Informationen zu unformatierten SQL-Abfragen finden Sie unter [Unformatierte SQL-Abfragen](/ef/core/querying/raw-sql).</span><span class="sxs-lookup"><span data-stu-id="06029-173">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-queries"></a><span data-ttu-id="06029-174">Untersuchen von SQL-Abfragen</span><span class="sxs-lookup"><span data-stu-id="06029-174">Examine SQL queries</span></span>

<span data-ttu-id="06029-175">Manchmal ist es hilfreich, die tatsächlichen SQL-Abfragen anzuzeigen, die an die Datenbank gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="06029-175">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="06029-176">Die integrierte Protokollfunktion für ASP.NET Core wird automatisch von EF Core verwendet, um Protokolle zu schreiben, die SQL für Abfragen und Updates enthalten.</span><span class="sxs-lookup"><span data-stu-id="06029-176">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="06029-177">In diesem Abschnitt sehen Sie einige Beispiele für die SQL-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="06029-177">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="06029-178">Öffnen Sie *StudentsController.cs* , und legen Sie in der `Details`-Methode einen Haltepunkt auf der `if (student == null)`-Anweisung fest.</span><span class="sxs-lookup"><span data-stu-id="06029-178">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="06029-179">Führen Sie die Anwendung im Debugmodus aus. Wechseln Sie zur Detailseite für Studenten.</span><span class="sxs-lookup"><span data-stu-id="06029-179">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="06029-180">Wechseln Sie zum **Ausgabe** -Fenster, zeigen Sie die Debugausgabe an, und Sie sehen die Abfrage:</span><span class="sxs-lookup"><span data-stu-id="06029-180">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="06029-181">Sie sehen hier etwas, das Sie möglicherweise überrascht: SQL wählt bis zu zwei Zeilen (`TOP(2)`) aus der Personentabelle aus.</span><span class="sxs-lookup"><span data-stu-id="06029-181">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="06029-182">Die `SingleOrDefaultAsync`-Methode wird nicht nach einer Zeile auf dem Server aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="06029-182">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="06029-183">Erläuterung:</span><span class="sxs-lookup"><span data-stu-id="06029-183">Here's why:</span></span>

* <span data-ttu-id="06029-184">Wenn die Abfrage mehrere Zeilen zurückgibt, gibt die Methode 0 (null) zurück.</span><span class="sxs-lookup"><span data-stu-id="06029-184">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="06029-185">Um zu bestimmen, ob die Abfrage mehrere Zeilen zurückgeben würde, muss EF überprüfen, ob mindestens zwei zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="06029-185">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="06029-186">Beachten Sie, dass Sie nicht den Debugmodus verwenden und an einem Haltepunkt anhalten müssen, um die Protokollierungsausgabe im **Ausgabe** -Fenster anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="06029-186">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="06029-187">Es ist einfach praktisch, die Protokollierung an dem Punkt zu anzuhalten, an dem Sie die Ausgabe ansehen möchten.</span><span class="sxs-lookup"><span data-stu-id="06029-187">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="06029-188">Wenn Sie dies nicht tun, wird die Protokollierung fortgesetzt, und Sie müssen scrollen, bis Sie die für Sie interessanten Stellen finden.</span><span class="sxs-lookup"><span data-stu-id="06029-188">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="06029-189">Erstellen einer Abstraktionsschicht</span><span class="sxs-lookup"><span data-stu-id="06029-189">Create an abstraction layer</span></span>

<span data-ttu-id="06029-190">Viele Entwickler schreiben Code, um das Repository- und Arbeitseinheitsmuster als Wrapper um den Code zu implementieren, der mit dem Entity Framework arbeitet.</span><span class="sxs-lookup"><span data-stu-id="06029-190">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="06029-191">Diese Muster sollen eine Abstraktionsebene zwischen der Datenzugriffsebene und den Geschäftslogikebene einer Anwendung erstellen.</span><span class="sxs-lookup"><span data-stu-id="06029-191">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="06029-192">Die Implementierung dieser Muster unterstützt die Isolation Ihrer Anwendung vor Änderungen im Datenspeicher und kann automatisierte Komponententests oder eine testgesteuerte Entwicklung (Test-Driven Development, TDD) erleichtern.</span><span class="sxs-lookup"><span data-stu-id="06029-192">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="06029-193">Das Schreiben von zusätzlichem Code zum Implementieren dieser Muster ist jedoch nicht immer die beste Wahl für Anwendungen, die EF verwenden. Dies hat mehrere Gründe:</span><span class="sxs-lookup"><span data-stu-id="06029-193">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="06029-194">Die EF-Kontextklasse isoliert Ihren Code selbst vor datenspeicherspezifischem Code.</span><span class="sxs-lookup"><span data-stu-id="06029-194">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="06029-195">Die EF-Kontextklasse kann als Arbeitseinheitsklasse für Updates der Datenbank fungieren, die Sie mithilfe von EF ausführen.</span><span class="sxs-lookup"><span data-stu-id="06029-195">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="06029-196">EF enthält Features zur TDD-Implementierung ohne Repository-Code zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="06029-196">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="06029-197">Weitere Informationen zur Implementierung der Repository- und Arbeitseinheitsmuster finden Sie in der [Version Entity Framework 5 dieser Tutorialreihe](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span><span class="sxs-lookup"><span data-stu-id="06029-197">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="06029-198">Entity Framework Core implementiert einen speicherinternen Datenbankanbieter, der für Tests verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="06029-198">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="06029-199">Weitere Informationen finden Sie unter [Test with InMemory (Testen mit InMemory)](/ef/core/miscellaneous/testing/in-memory).</span><span class="sxs-lookup"><span data-stu-id="06029-199">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="06029-200">Automatische Änderungserkennung</span><span class="sxs-lookup"><span data-stu-id="06029-200">Automatic change detection</span></span>

<span data-ttu-id="06029-201">Entity Framework bestimmt wie eine Entität geändert wurde (und welche Updates an die Datenbank gesendet werden müssen), indem die aktuellen Werte einer Entität mit den ursprünglichen Werten verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="06029-201">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="06029-202">Die ursprünglichen Werte werden gespeichert, wenn die Entität abgefragt oder angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="06029-202">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="06029-203">Einige der Methoden, die automatisch eine Änderungserkennung durchführen, sind die folgenden:</span><span class="sxs-lookup"><span data-stu-id="06029-203">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="06029-204">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="06029-204">DbContext.SaveChanges</span></span>

* <span data-ttu-id="06029-205">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="06029-205">DbContext.Entry</span></span>

* <span data-ttu-id="06029-206">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="06029-206">ChangeTracker.Entries</span></span>

<span data-ttu-id="06029-207">Wenn Sie eine große Anzahl von Entitäten überwachen und eine dieser Methoden oft in einer Schleife aufrufen, erhalten Sie möglicherweise erhebliche Leistungssteigerungen durch vorübergehendes Deaktivieren der automatischen Änderungserkennung mithilfe der `ChangeTracker.AutoDetectChangesEnabled`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="06029-207">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="06029-208">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="06029-208">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a><span data-ttu-id="06029-209">EF Core-Quellcode und Entwicklungspläne</span><span class="sxs-lookup"><span data-stu-id="06029-209">EF Core source code and development plans</span></span>

<span data-ttu-id="06029-210">Die Quelle von Entity Framework Core befindet sich unter [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="06029-210">The Entity Framework Core source is at [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore).</span></span> <span data-ttu-id="06029-211">Das EF Core-Repository enthält über Nacht erstellte Builds, Problemverfolgung, Featurespezifikationen, Notizen der Designbesprechungen und [die Roadmap für künftige Entwicklungen](https://github.com/dotnet/efcore/wiki/Roadmap).</span><span class="sxs-lookup"><span data-stu-id="06029-211">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/dotnet/efcore/wiki/Roadmap).</span></span> <span data-ttu-id="06029-212">Sie können Fehler finden oder protokollieren und beitragen.</span><span class="sxs-lookup"><span data-stu-id="06029-212">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="06029-213">Obwohl der Quellcode Open Source ist, wird Entity Framework Core als ein Microsoft-Produkt vollständig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="06029-213">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="06029-214">Das Microsoft Entity Framework-Team überprüft, welche Beiträge akzeptiert werden. Es testet alle Codeänderungen, um die Qualität jedes Release zu garantieren.</span><span class="sxs-lookup"><span data-stu-id="06029-214">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="06029-215">Reverse Engineering aus der bestehenden Datenbank</span><span class="sxs-lookup"><span data-stu-id="06029-215">Reverse engineer from existing database</span></span>

<span data-ttu-id="06029-216">Verwenden Sie zum Zurückentwickeln (Reverse Engineering) eines Datenmodells, einschließlich der Entitätsklassen aus einer vorhandenen Datenbank, den Befehl [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="06029-216">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="06029-217">Lesen Sie das [Tutorial Erste Schritte](/ef/core/get-started/aspnetcore/existing-db).</span><span class="sxs-lookup"><span data-stu-id="06029-217">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a><span data-ttu-id="06029-218">Verwenden dynamischer LINQs zum Vereinfachen des Codes</span><span class="sxs-lookup"><span data-stu-id="06029-218">Use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="06029-219">Das [dritte Tutorial dieser Reihe](sort-filter-page.md) zeigt, wie Sie LINQ-Code schreiben, indem Sie eine Hartcodierung der Spaltennamen in einer `switch`-Anweisung durchführen.</span><span class="sxs-lookup"><span data-stu-id="06029-219">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="06029-220">Mit zwei Spalten zur Auswahl funktioniert dies hervorragend, aber wenn Sie viele Spalten zur Verfügung haben, könnte der Code ausführlich werden.</span><span class="sxs-lookup"><span data-stu-id="06029-220">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="06029-221">Zur Behebung dieses Problems können Sie die `EF.Property`-Methode verwenden, um den Namen der Eigenschaft als Zeichenfolge anzugeben.</span><span class="sxs-lookup"><span data-stu-id="06029-221">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="06029-222">Ersetzen Sie die `Index`-Methode im `StudentsController` durch den folgenden Code, um diesen Ansatz auszuprobieren.</span><span class="sxs-lookup"><span data-stu-id="06029-222">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a><span data-ttu-id="06029-223">Danksagungen</span><span class="sxs-lookup"><span data-stu-id="06029-223">Acknowledgments</span></span>

<span data-ttu-id="06029-224">Tom Dykstra und Rick Anderson (Twitter @RickAndMSFT) haben dieses Tutorial verfasst.</span><span class="sxs-lookup"><span data-stu-id="06029-224">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="06029-225">Rowan Miller, Diego Vega und andere Mitglieder des Entity Framework-Teams haben uns bei Codereviews und der Behebung von Problemen unterstützt, die aufgetreten waren, während wir den Code für dieses Tutorial geschrieben haben.</span><span class="sxs-lookup"><span data-stu-id="06029-225">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span> <span data-ttu-id="06029-226">John Parente und Paul Goldman arbeiteten an der Aktualisierung des Tutorials für ASP.NET Core 2.2.</span><span class="sxs-lookup"><span data-stu-id="06029-226">John Parente and Paul Goldman worked on updating the tutorial for ASP.NET Core 2.2.</span></span>

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="06029-227">Problembehandlung bei häufigen Fehlern</span><span class="sxs-lookup"><span data-stu-id="06029-227">Troubleshoot common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="06029-228">ContosoUniversity.dll wird von einem anderen Prozess verwendet</span><span class="sxs-lookup"><span data-stu-id="06029-228">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="06029-229">Fehlermeldung:</span><span class="sxs-lookup"><span data-stu-id="06029-229">Error message:</span></span>

> <span data-ttu-id="06029-230">Kann nicht geöffnet werden „... bin\Debug\netcoreapp1.0\ContosoUniversity.dll“ zum Schreiben: „Der Prozess kann nicht auf die Datei „... \bin\Debug\netcoreapp1.0\ContosoUniversity.dll“ zugreifen, da sie von einem anderen Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="06029-230">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="06029-231">Projektmappe:</span><span class="sxs-lookup"><span data-stu-id="06029-231">Solution:</span></span>

<span data-ttu-id="06029-232">Beenden Sie die Website in IIS Express.</span><span class="sxs-lookup"><span data-stu-id="06029-232">Stop the site in IIS Express.</span></span> <span data-ttu-id="06029-233">Wechseln Sie zur Windows-Taskleiste, suchen Sie IIS Express, klicken Sie mit der rechten Maustaste auf das Symbol, wählen Sie den Standort Contoso University aus, und klicken Sie dann auf **Website beenden**.</span><span class="sxs-lookup"><span data-stu-id="06029-233">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="06029-234">Gerüstete Migration ohne Code in Up-und Down-Methoden</span><span class="sxs-lookup"><span data-stu-id="06029-234">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="06029-235">Mögliche Ursache:</span><span class="sxs-lookup"><span data-stu-id="06029-235">Possible cause:</span></span>

<span data-ttu-id="06029-236">EF-CLI-Befehle schließen und speichern die Codedateien nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="06029-236">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="06029-237">Wenn Sie nicht gespeicherte Änderungen haben, wenn Sie den `migrations add`-Befehl ausführen, wird EF Ihre Änderungen nicht finden.</span><span class="sxs-lookup"><span data-stu-id="06029-237">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="06029-238">Projektmappe:</span><span class="sxs-lookup"><span data-stu-id="06029-238">Solution:</span></span>

<span data-ttu-id="06029-239">Führen Sie den `migrations remove`-Befehl aus, speichern Sie Ihre Codeänderungen, führen Sie den `migrations add`-Befehl erneut aus.</span><span class="sxs-lookup"><span data-stu-id="06029-239">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="06029-240">Fehler beim Ausführen von Datenbankupdates</span><span class="sxs-lookup"><span data-stu-id="06029-240">Errors while running database update</span></span>

<span data-ttu-id="06029-241">Es ist möglich, dass andere Fehler auftreten, wenn Schemaänderungen in einer Datenbank durchgeführt werden, die vorhandene Daten enthält.</span><span class="sxs-lookup"><span data-stu-id="06029-241">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="06029-242">Wenn Sie Migrationsfehler erhalten, die Sie nicht beheben können, können Sie den Datenbanknamen in der Verbindungszeichenfolge ändern oder die Datenbank löschen.</span><span class="sxs-lookup"><span data-stu-id="06029-242">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="06029-243">Mit einer neuen Datenbank gibt es keine zu migrierenden Daten, und der Update-Database-Befehl wird wahrscheinlich ohne Fehler abgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="06029-243">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="06029-244">Der einfachste Ansatz besteht in der Neubenennung der Datenbank in *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="06029-244">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="06029-245">Das nächste Mal, wenn Sie `database update` ausführen, wird eine neue Datenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="06029-245">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="06029-246">Zum Löschen einer Datenbank im SSOX, klicken Sie mit der rechten Maustaste auf die Datenbank. Klicken Sie auf **Löschen** , wählen Sie dann im Dialogfeld **Datenbank löschen** **Bestehende Verbindungen schließen** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="06029-246">To delete a database in SSOX, right-click the database, click **Delete** , and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="06029-247">Führen Sie zum Löschen einer Datenbank mithilfe der CLI den `database drop`-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="06029-247">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="06029-248">Fehler beim Bestimmen der SQL Server-Instanz</span><span class="sxs-lookup"><span data-stu-id="06029-248">Error locating SQL Server instance</span></span>

<span data-ttu-id="06029-249">Fehlermeldung:</span><span class="sxs-lookup"><span data-stu-id="06029-249">Error Message:</span></span>

> <span data-ttu-id="06029-250">Ein netzwerkbezogener oder instanzspezifischer Fehler beim Herstellen einer Verbindung mit SQL Server.</span><span class="sxs-lookup"><span data-stu-id="06029-250">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="06029-251">Der Server wurde nicht gefunden, oder auf ihn kann nicht zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="06029-251">The server was not found or was not accessible.</span></span> <span data-ttu-id="06029-252">Stellen Sie sicher, dass der Instanzname richtig und SQL Server so konfiguriert ist, das Remoteverbindungen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="06029-252">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="06029-253">(Anbieter: SQL-Netzwerkschnittstellen, Fehler: 26: Fehler beim Suchen des angegebenen Servers/der angegebenen Instanz)</span><span class="sxs-lookup"><span data-stu-id="06029-253">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="06029-254">Projektmappe:</span><span class="sxs-lookup"><span data-stu-id="06029-254">Solution:</span></span>

<span data-ttu-id="06029-255">Überprüfen Sie die Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="06029-255">Check the connection string.</span></span> <span data-ttu-id="06029-256">Wenn Sie die Datenbankdatei manuell gelöscht haben, ändern Sie den Namen der Datenbank in der Konstruktionszeichenfolge, um mit einer neuen Datenbank zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="06029-256">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="06029-257">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="06029-257">Get the code</span></span>

[<span data-ttu-id="06029-258">Download or view the completed app (Herunterladen oder anzeigen der vollständigen App).</span><span class="sxs-lookup"><span data-stu-id="06029-258">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a><span data-ttu-id="06029-259">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="06029-259">Additional resources</span></span>

<span data-ttu-id="06029-260">Weitere Informationen zu EF Core finden Sie in der [Dokumentation zu Entity Framework Core](/ef/core).</span><span class="sxs-lookup"><span data-stu-id="06029-260">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="06029-261">Zu diesem Thema ist auch ein Buch verfügbar: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action)</span><span class="sxs-lookup"><span data-stu-id="06029-261">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="06029-262">Weitere Informationen zum Bereitstellen einer Webanwendung finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="06029-262">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="06029-263">Weitere Informationen zu anderen Themen im Zusammenhang mit ASP.NET Core MVC, wie beispielsweise Authentifizierung und Autorisierung, finden Sie unter <xref:index>.</span><span class="sxs-lookup"><span data-stu-id="06029-263">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="06029-264">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="06029-264">Next steps</span></span>

<span data-ttu-id="06029-265">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="06029-265">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="06029-266">Haben Sie unformatierte SQL-Abfragen durchgeführt</span><span class="sxs-lookup"><span data-stu-id="06029-266">Performed raw SQL queries</span></span>
> * <span data-ttu-id="06029-267">Haben Sie eine Abfrage zum Zurückgeben von Entitäten aufgerufen</span><span class="sxs-lookup"><span data-stu-id="06029-267">Called a query to return entities</span></span>
> * <span data-ttu-id="06029-268">Haben Sie eine Abfrage zum Zurückgeben anderer Typen aufgerufen</span><span class="sxs-lookup"><span data-stu-id="06029-268">Called a query to return other types</span></span>
> * <span data-ttu-id="06029-269">Haben Sie eine Aktualisierungsabfrage aufgerufen</span><span class="sxs-lookup"><span data-stu-id="06029-269">Called an update query</span></span>
> * <span data-ttu-id="06029-270">Haben Sie SQL-Abfragen untersucht</span><span class="sxs-lookup"><span data-stu-id="06029-270">Examined SQL queries</span></span>
> * <span data-ttu-id="06029-271">Haben Sie eine Abstraktionsschicht erstellt</span><span class="sxs-lookup"><span data-stu-id="06029-271">Created an abstraction layer</span></span>
> * <span data-ttu-id="06029-272">Haben Sie Informationen zur automatischen Änderungserkennung erhalten</span><span class="sxs-lookup"><span data-stu-id="06029-272">Learned about Automatic change detection</span></span>
> * <span data-ttu-id="06029-273">Haben Sie Informationen zum EF Core-Quellcode und zu Entwicklungsplänen erhalten</span><span class="sxs-lookup"><span data-stu-id="06029-273">Learned about EF Core source code and development plans</span></span>
> * <span data-ttu-id="06029-274">Haben Sie Informationen zum Verwenden dynamischer LINQs zum Vereinfachen des Codes erhalten</span><span class="sxs-lookup"><span data-stu-id="06029-274">Learned how to use dynamic LINQ to simplify code</span></span>

<span data-ttu-id="06029-275">Dies schließt die verschiedenen Tutorials zur Verwendung von Entity Framework Core in einer ASP.NET Core MVC-Anwendung ab.</span><span class="sxs-lookup"><span data-stu-id="06029-275">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span> <span data-ttu-id="06029-276">Diese Serie hat mit einer neuen Datenbank gearbeitet; eine Alternative ist es, ein Modell aus einer vorhandenen Datenbank zurückzuentwickeln.</span><span class="sxs-lookup"><span data-stu-id="06029-276">This series worked with a new database; an alternative is to  reverse engineer a model from an existing database.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="06029-277">Tutorial: Erste Schritte mit EF Core in ASP.NET Core mit einer neuen Datenbank</span><span class="sxs-lookup"><span data-stu-id="06029-277">Tutorial: EF Core with MVC, existing database</span></span>](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
