---
title: 'Teil 7: Hinzufügen der Suche zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 7 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 12/13/2018
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
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: 657072803f59feb99de8b31ddb3a6433d832aa30
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059623"
---
# <a name="part-7-add-search-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="cf298-103">Teil 7: Hinzufügen der Suche zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="cf298-103">Part 7, add search to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="cf298-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cf298-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cf298-105">In diesem Abschnitt fügen Sie die Suchfunktion zur Aktionsmethode `Index` hinzu, mit der Sie Filme nach *Genre* oder *Name* suchen können.</span><span class="sxs-lookup"><span data-stu-id="cf298-105">In this section, you add search capability to the `Index` action method that lets you search movies by *genre* or *name*.</span></span>

<span data-ttu-id="cf298-106">Aktualisieren Sie die `Index`-Methode in *Controllers/MoviesController.cs* mit folgendem Code:</span><span class="sxs-lookup"><span data-stu-id="cf298-106">Update the `Index` method found inside *Controllers/MoviesController.cs* with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_1stSearch)]

<span data-ttu-id="cf298-107">Die erste Zeile der Aktionsmethode `Index` erstellt eine [LINQ](/dotnet/standard/using-linq)-Abfrage zum Auswählen der Filme:</span><span class="sxs-lookup"><span data-stu-id="cf298-107">The first line of the `Index` action method creates a [LINQ](/dotnet/standard/using-linq) query to select the movies:</span></span>

```csharp
var movies = from m in _context.Movie
             select m;
```

<span data-ttu-id="cf298-108">Die Abfrage wird an dieser Stelle *nur* definiert und **nicht** auf die Datenbank angewendet.</span><span class="sxs-lookup"><span data-stu-id="cf298-108">The query is *only* defined at this point, it has **not** been run against the database.</span></span>

<span data-ttu-id="cf298-109">Wenn der `searchString`-Parameter eine Zeichenfolge enthält, wird die Filmabfrage so geändert, dass nach dem Wert der Suchzeichenfolge gefiltert wird:</span><span class="sxs-lookup"><span data-stu-id="cf298-109">If the `searchString` parameter contains a string, the movies query is modified to filter on the value of the search string:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_SearchNull2)]

<span data-ttu-id="cf298-110">Der Code `s => s.Title.Contains()` oben ist ein [Lambdaausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span><span class="sxs-lookup"><span data-stu-id="cf298-110">The `s => s.Title.Contains()` code above is a [Lambda Expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="cf298-111">Lambdas werden in methodenbasierten [LINQ](/dotnet/standard/using-linq)-Abfragen als Argumente für standardmäßige Abfrageoperatormethoden wie die [Where](/dotnet/api/system.linq.enumerable.where)-Methode oder `Contains` verwendet (siehe den vorangehenden Code).</span><span class="sxs-lookup"><span data-stu-id="cf298-111">Lambdas are used in method-based [LINQ](/dotnet/standard/using-linq) queries as arguments to standard query operator methods such as the [Where](/dotnet/api/system.linq.enumerable.where) method or `Contains` (used in the code above).</span></span> <span data-ttu-id="cf298-112">LINQ-Abfragen werden nicht ausgeführt, wenn sie definiert oder durch Aufrufen einer Methode geändert werden (z. B. `Where`, `Contains` oder `OrderBy`).</span><span class="sxs-lookup"><span data-stu-id="cf298-112">LINQ queries are not executed when they're defined or when they're modified by calling a method such as `Where`, `Contains`, or `OrderBy`.</span></span> <span data-ttu-id="cf298-113">Stattdessen wird die Ausführung der Abfrage verzögert.</span><span class="sxs-lookup"><span data-stu-id="cf298-113">Rather, query execution is deferred.</span></span>  <span data-ttu-id="cf298-114">Dies bedeutet, dass die Auswertung eines Ausdrucks so lange hinausgezögert wird, bis dessen realisierter Wert tatsächlich durchlaufen oder die `ToListAsync`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="cf298-114">That means that the evaluation of an expression is delayed until its realized value is actually iterated over or the `ToListAsync` method is called.</span></span> <span data-ttu-id="cf298-115">Weitere Informationen zur verzögerten Abfrageausführung finden Sie unter [Abfrageausführung](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span><span class="sxs-lookup"><span data-stu-id="cf298-115">For more information about deferred query execution, see [Query Execution](/dotnet/framework/data/adonet/ef/language-reference/query-execution).</span></span>

<span data-ttu-id="cf298-116">Hinweis: Die [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains)-Methode wird in der Datenbank und nicht im oben gezeigten C#-Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cf298-116">Note: The [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) method is run on the database, not in the c# code shown above.</span></span> <span data-ttu-id="cf298-117">Die Groß-/Kleinschreibung in der Abfrage hängt von der Datenbank und Sortierung ab.</span><span class="sxs-lookup"><span data-stu-id="cf298-117">The case sensitivity on the query depends on the database and the collation.</span></span> <span data-ttu-id="cf298-118">In SQL Server wird [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) zu [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql) zugeordnet, das Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="cf298-118">On SQL Server, [Contains](/dotnet/api/system.data.objects.dataclasses.entitycollection-1.contains) maps to [SQL LIKE](/sql/t-sql/language-elements/like-transact-sql), which is case insensitive.</span></span> <span data-ttu-id="cf298-119">In SQLite wird bei der Standardsortierung Groß-/Kleinschreibung beachtet.</span><span class="sxs-lookup"><span data-stu-id="cf298-119">In SQLite, with the default collation, it's case sensitive.</span></span>

<span data-ttu-id="cf298-120">Navigieren Sie zu `/Movies/Index`.</span><span class="sxs-lookup"><span data-stu-id="cf298-120">Navigate to `/Movies/Index`.</span></span> <span data-ttu-id="cf298-121">Fügen Sie eine Abfragezeichenfolge wie `?searchString=Ghost` an die URL an.</span><span class="sxs-lookup"><span data-stu-id="cf298-121">Append a query string such as `?searchString=Ghost` to the URL.</span></span> <span data-ttu-id="cf298-122">Die gefilterten Filme werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="cf298-122">The filtered movies are displayed.</span></span>

![Indexansicht](~/tutorials/first-mvc-app/search/_static/ghost.png)

<span data-ttu-id="cf298-124">Wenn Sie die Signatur der `Index`-Methode so ändern, dass sie einen Parameter mit dem Namen `id` hat, entspricht der Parameter `id` dem optionalen Platzhalter `{id}` für die Standardrouten, die in *Startup.cs* festgelegt sind.</span><span class="sxs-lookup"><span data-stu-id="cf298-124">If you change the signature of the `Index` method to have a parameter named `id`, the `id` parameter will match the optional `{id}` placeholder for the default routes set in *Startup.cs*.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="cf298-125">Ändern Sie den Parameter in `id` und alle Vorkommen von `searchString` in `id`.</span><span class="sxs-lookup"><span data-stu-id="cf298-125">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

<span data-ttu-id="cf298-126">Die vorherige `Index`-Methode:</span><span class="sxs-lookup"><span data-stu-id="cf298-126">The previous `Index` method:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="cf298-127">Die aktualisierte `Index`-Methode mit `id`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="cf298-127">The updated `Index` method with `id` parameter:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_SearchID)]

<span data-ttu-id="cf298-128">Sie können nun den Suchtitel als Routendaten (ein URL-Segment) anstatt als Wert einer Abfragezeichenfolge übergeben.</span><span class="sxs-lookup"><span data-stu-id="cf298-128">You can now pass the search title as route data (a URL segment) instead of as a query string value.</span></span>

![Indexansicht mit dem der URL hinzugefügten Wort „ghost“ und einer zurückgegebenen Filmliste mit zwei Filmen: Ghostbusters und Ghostbusters 2](~/tutorials/first-mvc-app/search/_static/g2.png)

<span data-ttu-id="cf298-130">Sie können jedoch von den Benutzern nicht erwarten, dass sie jedes Mal die URL ändern, wenn sie nach einem Film suchen möchten.</span><span class="sxs-lookup"><span data-stu-id="cf298-130">However, you can't expect users to modify the URL every time they want to search for a movie.</span></span> <span data-ttu-id="cf298-131">Deshalb fügen Sie nun Benutzeroberflächenelemente zum besseren Filtern von Filmen hinzu.</span><span class="sxs-lookup"><span data-stu-id="cf298-131">So now you'll add UI elements to help them filter movies.</span></span> <span data-ttu-id="cf298-132">Wenn Sie die Signatur der `Index`-Methode geändert haben, um das Übergeben des routengebundenen Parameters `ID` zu testen, ändern Sie sie erneut so, dass sie einen Parameter namens `searchString` verwendet:</span><span class="sxs-lookup"><span data-stu-id="cf298-132">If you changed the signature of the `Index` method to test how to pass the route-bound `ID` parameter, change it back so that it takes a parameter named `searchString`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1,6,8&name=snippet_1stSearch)]

<span data-ttu-id="cf298-133">Öffnen Sie die Datei *Views/Movies/Index.cshtml* , und fügen Sie das hervorgehobene `<form>`-Markup hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf298-133">Open the *Views/Movies/Index.cshtml* file, and add the `<form>` markup highlighted below:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexForm1.cshtml?highlight=10-16&range=4-21)]

<span data-ttu-id="cf298-134">Das HTML-Tag `<form>` nutzt das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms). Wenn Sie nun das Formular senden, wird die Filterzeichenfolge an die Aktion `Index` des „movies“-Controllers übermittelt.</span><span class="sxs-lookup"><span data-stu-id="cf298-134">The HTML `<form>` tag uses the [Form Tag Helper](xref:mvc/views/working-with-forms), so when you submit the form, the filter string is posted to the `Index` action of the movies controller.</span></span> <span data-ttu-id="cf298-135">Speichern Sie Ihre Änderungen, und testen Sie dann den Filter.</span><span class="sxs-lookup"><span data-stu-id="cf298-135">Save your changes and then test the filter.</span></span>

![Indexansicht mit dem in das Filtertextfeld eingegebenen Wort „ghost“](~/tutorials/first-mvc-app/search/_static/filter.png)

<span data-ttu-id="cf298-137">Entgegen Ihrer Erwartung gibt es keine `[HttpPost]`-Überladung der `Index`-Methode.</span><span class="sxs-lookup"><span data-stu-id="cf298-137">There's no `[HttpPost]` overload of the `Index` method as you might expect.</span></span> <span data-ttu-id="cf298-138">Diese ist nicht erforderlich, da die Methode nicht den Status der App ändert, sondern bloß Daten filtert.</span><span class="sxs-lookup"><span data-stu-id="cf298-138">You don't need it, because the method isn't changing the state of the app, just filtering data.</span></span>

<span data-ttu-id="cf298-139">Sie können die folgende `[HttpPost] Index`-Methode hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="cf298-139">You could add the following `[HttpPost] Index` method.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?highlight=1&name=snippet_SearchPost)]

<span data-ttu-id="cf298-140">Der Parameter `notUsed` dient zum Erstellen einer Überladung für die `Index`-Methode.</span><span class="sxs-lookup"><span data-stu-id="cf298-140">The `notUsed` parameter is used to create an overload for the `Index` method.</span></span> <span data-ttu-id="cf298-141">Damit beschäftigen wir uns später im Tutorial.</span><span class="sxs-lookup"><span data-stu-id="cf298-141">We'll talk about that later in the tutorial.</span></span>

<span data-ttu-id="cf298-142">Wenn Sie diese Methode hinzufügen, entspricht die aufrufende Aktionsinstanz der `[HttpPost] Index`-Methode, und die `[HttpPost] Index`-Methode wird wie in der folgenden Abbildung gezeigt ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="cf298-142">If you add this method, the action invoker would match the `[HttpPost] Index` method, and the `[HttpPost] Index` method would run as shown in the image below.</span></span>

![Browserfenster mit der Antwort der Anwendung von „Von HttpPost-Index“: Filter für „ghost“](~/tutorials/first-mvc-app/search/_static/fo.png)

<span data-ttu-id="cf298-144">Doch selbst wenn Sie diese `[HttpPost]`-Version der `Index`-Methode hinzufügen, gibt es eine Einschränkung für die gesamte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="cf298-144">However, even if you add this `[HttpPost]` version of the `Index` method, there's a limitation in how this has all been implemented.</span></span> <span data-ttu-id="cf298-145">Stellen Sie sich vor, Sie möchten eine bestimmte Suche als Favorit speichern oder einen Link an Freunde senden, auf den diese klicken können, um dieselbe gefilterte Liste von Filmen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="cf298-145">Imagine that you want to bookmark a particular search or you want to send a link to friends that they can click in order to see the same filtered list of movies.</span></span> <span data-ttu-id="cf298-146">Beachten Sie, dass die URL der HTTP POST-Anforderung identisch mit der URL der GET-Anforderung (localhost:{PORT}/Movies/Index) ist. Es sind keine Suchinformationen in der URL vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cf298-146">Notice that the URL for the HTTP POST request is the same as the URL for the GET request (localhost:{PORT}/Movies/Index) -- there's no search information in the URL.</span></span> <span data-ttu-id="cf298-147">Die Informationen in der Suchzeichenfolge werden an den Server als [Formularfeldwert](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data) gesendet.</span><span class="sxs-lookup"><span data-stu-id="cf298-147">The search string information is sent to the server as a [form field value](https://developer.mozilla.org/docs/Learn/HTML/Forms/Sending_and_retrieving_form_data).</span></span> <span data-ttu-id="cf298-148">Sie können dies mit den Entwicklertools für den Browser oder dem exzellenten Tool [Fiddler](https://www.telerik.com/fiddler) überprüfen.</span><span class="sxs-lookup"><span data-stu-id="cf298-148">You can verify that with the browser Developer tools or the excellent [Fiddler tool](https://www.telerik.com/fiddler).</span></span> <span data-ttu-id="cf298-149">Die folgende Abbildung zeigt die Entwicklertools für den Browser Chrome:</span><span class="sxs-lookup"><span data-stu-id="cf298-149">The image below shows the Chrome browser Developer tools:</span></span>

![Registerkarte „Netzwerk“ der Entwicklertools in Microsoft Edge mit einem Anforderungstext mit dem „searchString“-Wert „ghost“](~/tutorials/first-mvc-app/search/_static/f12_rb.png)

<span data-ttu-id="cf298-151">Sie können den Suchparameter und das [XSRF](xref:security/anti-request-forgery)-Token im Anforderungstext erkennen.</span><span class="sxs-lookup"><span data-stu-id="cf298-151">You can see the search parameter and [XSRF](xref:security/anti-request-forgery) token in the request body.</span></span> <span data-ttu-id="cf298-152">Wie im vorherigen Tutorial erwähnt, generiert das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) ein [XSRF](xref:security/anti-request-forgery)-Fälschungssicherheitstoken.</span><span class="sxs-lookup"><span data-stu-id="cf298-152">Note, as mentioned in the previous tutorial, the [Form Tag Helper](xref:mvc/views/working-with-forms) generates an [XSRF](xref:security/anti-request-forgery) anti-forgery token.</span></span> <span data-ttu-id="cf298-153">Da wir keine Daten ändern, müssen wir nicht das Token in der Controllermethode validieren.</span><span class="sxs-lookup"><span data-stu-id="cf298-153">We're not modifying data, so we don't need to validate the token in the controller method.</span></span>

<span data-ttu-id="cf298-154">Da sich der Suchparameter im Anforderungstext und nicht in der URL befindet, können Sie diese Suchinformationen nicht als Favorit speichern oder mit anderen teilen.</span><span class="sxs-lookup"><span data-stu-id="cf298-154">Because the search parameter is in the request body and not the URL, you can't capture that search information to bookmark or share with others.</span></span> <span data-ttu-id="cf298-155">Beheben Sie dieses Problem, indem Sie angeben, dass eine `HTTP GET`-Anforderung verwendet werden soll, die sich in der Datei *Views/Movies/Index.cshtml* befindet.</span><span class="sxs-lookup"><span data-stu-id="cf298-155">Fix this by specifying the request should be `HTTP GET` found in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGet.cshtml?highlight=12&range=1-23)]

<span data-ttu-id="cf298-156">Wenn Sie nun eine Suche übermitteln, enthält die URL die Suchabfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="cf298-156">Now when you submit a search, the URL contains the search query string.</span></span> <span data-ttu-id="cf298-157">Das Suchen wird auch an Aktionsmethode `HttpGet Index` übertragen, auch wenn Sie eine `HttpPost Index`-Methode haben.</span><span class="sxs-lookup"><span data-stu-id="cf298-157">Searching will also go to the `HttpGet Index` action method, even if you have a `HttpPost Index` method.</span></span>

![Browserfenster mit „searchString=ghost“ in der URL und den zurückgegebenen Filmen Ghostbusters und Ghostbusters 2, die das Wort „Ghost“ enthalten](~/tutorials/first-mvc-app/search/_static/search_get.png)

<span data-ttu-id="cf298-159">Das folgende Markup zeigt die Änderung am Tag `form`:</span><span class="sxs-lookup"><span data-stu-id="cf298-159">The following markup shows the change to the `form` tag:</span></span>

```cshtml
<form asp-controller="Movies" asp-action="Index" method="get">
```

## <a name="add-search-by-genre"></a><span data-ttu-id="cf298-160">Hinzufügen der Suche nach Genre</span><span class="sxs-lookup"><span data-stu-id="cf298-160">Add Search by genre</span></span>

<span data-ttu-id="cf298-161">Fügen Sie dem Ordner *Models* die folgende `MovieGenreViewModel`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="cf298-161">Add the following `MovieGenreViewModel` class to the *Models* folder:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Models/MovieGenreViewModel.cs)]

<span data-ttu-id="cf298-162">Das Ansichtsmodell „movie-genre“ enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cf298-162">The movie-genre view model will contain:</span></span>

* <span data-ttu-id="cf298-163">Eine Liste von Filmen.</span><span class="sxs-lookup"><span data-stu-id="cf298-163">A list of movies.</span></span>
* <span data-ttu-id="cf298-164">Ein `SelectList`-Element mit der Liste der Genres.</span><span class="sxs-lookup"><span data-stu-id="cf298-164">A `SelectList` containing the list of genres.</span></span> <span data-ttu-id="cf298-165">Dies ermöglicht dem Benutzer, ein Genre in der Liste auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="cf298-165">This allows the user to select a genre from the list.</span></span>
* <span data-ttu-id="cf298-166">Ein `MovieGenre`-Element, das das ausgewählte Genre enthält.</span><span class="sxs-lookup"><span data-stu-id="cf298-166">`MovieGenre`, which contains the selected genre.</span></span>
* <span data-ttu-id="cf298-167">`SearchString`, die den Text enthält, den Benutzer in das Suchtextfeld eingeben.</span><span class="sxs-lookup"><span data-stu-id="cf298-167">`SearchString`, which contains the text users enter in the search text box.</span></span>

<span data-ttu-id="cf298-168">Ersetzen Sie die `Index`-Methode in `MoviesController.cs` durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="cf298-168">Replace the `Index` method in `MoviesController.cs` with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_SearchGenre)]

<span data-ttu-id="cf298-169">Der folgende Code ist eine `LINQ`-Abfrage, die alle Genres aus der Datenbank abruft.</span><span class="sxs-lookup"><span data-stu-id="cf298-169">The following code is a `LINQ` query that retrieves all the genres from the database.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_LINQ)]

<span data-ttu-id="cf298-170">Das `SelectList`-Element von Genres wird durch Projizieren der unterschiedlichen Genres erstellt (wir möchten nicht, dass unsere Auswahlliste doppelte Genres enthält).</span><span class="sxs-lookup"><span data-stu-id="cf298-170">The `SelectList` of genres is created by projecting the distinct genres (we don't want our select list to have duplicate genres).</span></span>

<span data-ttu-id="cf298-171">Wenn der Benutzer nach dem Element sucht, wird der Wert für die Suche im Suchfeld beibehalten.</span><span class="sxs-lookup"><span data-stu-id="cf298-171">When the user searches for the item, the search value is retained in the search box.</span></span>

## <a name="add-search-by-genre-to-the-index-view"></a><span data-ttu-id="cf298-172">Hinzufügen der Suche nach Genre zur Indexansicht</span><span class="sxs-lookup"><span data-stu-id="cf298-172">Add search by genre to the Index view</span></span>

<span data-ttu-id="cf298-173">Aktualisieren Sie `Index.cshtml` in *Views/Movies/* folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="cf298-173">Update `Index.cshtml` found in *Views/Movies/* as follows:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexFormGenreNoRating.cshtml?highlight=1,15,16,17,19,28,31,34,37,43)]

<span data-ttu-id="cf298-174">Überprüfen Sie den Lambdaausdruck, der im folgenden HTML-Hilfsprogramm verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="cf298-174">Examine the lambda expression used in the following HTML Helper:</span></span>

`@Html.DisplayNameFor(model => model.Movies[0].Title)`

<span data-ttu-id="cf298-175">Das HTML-Hilfsprogramm `DisplayNameFor` im vorangehenden Code überprüft die Eigenschaft `Title`, auf die im Lambdaausdruck verwiesen wird, um den Anzeigenamen zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="cf298-175">In the preceding code, the `DisplayNameFor` HTML Helper inspects the `Title` property referenced in the lambda expression to determine the display name.</span></span> <span data-ttu-id="cf298-176">Da der Lambda-Ausdruck überprüft statt ausgewertet wird, erhalten Sie keine Zugriffsverletzung, wenn `model`, `model.Movies`, `model.Movies[0]` oder `null` leer sind.</span><span class="sxs-lookup"><span data-stu-id="cf298-176">Since the lambda expression is inspected rather than evaluated, you don't receive an access violation when `model`, `model.Movies`, or `model.Movies[0]` are `null` or empty.</span></span> <span data-ttu-id="cf298-177">Wenn der Lambdaausdruck ausgewertet wird, (z.B. mit `@Html.DisplayFor(modelItem => item.Title)`), werden die Eigenschaftswerte ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="cf298-177">When the lambda expression is evaluated (for example, `@Html.DisplayFor(modelItem => item.Title)`), the model's property values are evaluated.</span></span>

<span data-ttu-id="cf298-178">Testen Sie die App mit einer Suche nach Genre, Filmtitel und beidem:</span><span class="sxs-lookup"><span data-stu-id="cf298-178">Test the app by searching by genre, by movie title, and by both:</span></span>

![Browser-Fenster mit den Ergebnissen von https://localhost:5001/Movies?MovieGenre=Comedy&SearchString=2](~/tutorials/first-mvc-app/search/_static/s2.png)

> [!div class="step-by-step"]
> <span data-ttu-id="cf298-180">[Zurück](controller-methods-views.md)
> [Weiter](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="cf298-180">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>
