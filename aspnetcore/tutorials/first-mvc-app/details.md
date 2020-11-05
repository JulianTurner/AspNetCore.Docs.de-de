---
title: 'Teil 10: Untersuchen der Methoden „Details“ und „Delete“ einer ASP.NET Core-App'
author: rick-anderson
description: Dies ist Teil 10 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
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
uid: tutorials/first-mvc-app/details
ms.openlocfilehash: 9ceb6c3c8a6622d6e203641c2ce97a483d3d4325
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050744"
---
# <a name="part-10-examine-the-details-and-delete-methods-of-an-aspnet-core-app"></a><span data-ttu-id="e8a86-103">Teil 10: Untersuchen der Methoden „Details“ und „Delete“ einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="e8a86-103">Part 10, examine the Details and Delete methods of an ASP.NET Core app</span></span>

<span data-ttu-id="e8a86-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e8a86-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e8a86-105">Öffnen Sie den „Movie“-Controller, und untersuchen Sie die `Details`-Methode.</span><span class="sxs-lookup"><span data-stu-id="e8a86-105">Open the Movie controller and examine the `Details` method:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_details)]

<span data-ttu-id="e8a86-106">Die MVC-Gerüstbau-Engine, die diese Aktionsmethode erstellt hat, fügt einen Kommentar mit einer HTTP-Anforderung hinzu, die die Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="e8a86-106">The MVC scaffolding engine that created this action method adds a comment showing an HTTP request that invokes the method.</span></span> <span data-ttu-id="e8a86-107">In diesem Fall ist dies eine GET-Anforderung mit drei URL-Segmenten: dem Controller `Movies`, der Methode `Details` und dem Wert von `id`.</span><span class="sxs-lookup"><span data-stu-id="e8a86-107">In this case it's a GET request with three URL segments, the `Movies` controller, the `Details` method, and an `id` value.</span></span> <span data-ttu-id="e8a86-108">Wie bereits erwähnt, werden diese Segmente in *Startup.cs* definiert.</span><span class="sxs-lookup"><span data-stu-id="e8a86-108">Recall these segments are defined in *Startup.cs*.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie3/Startup.cs?highlight=5&name=snippet_1)]

<span data-ttu-id="e8a86-109">EF erleichtert das Suchen nach Daten mithilfe der `FirstOrDefaultAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="e8a86-109">EF makes it easy to search for data using the `FirstOrDefaultAsync` method.</span></span> <span data-ttu-id="e8a86-110">Ein wichtiges in die Methode integriertes Sicherheitsmerkmal ist, dass der Code überprüft, ob die Suchmethode einen Film gefunden hat, bevor sie versucht, etwas damit zu tun.</span><span class="sxs-lookup"><span data-stu-id="e8a86-110">An important security feature built into the method is that the code verifies that the search method has found a movie before it tries to do anything with it.</span></span> <span data-ttu-id="e8a86-111">Ein Hacker kann beispielsweise Fehler in die Website einschleusen, indem die von den Links erstellte URL von `http://localhost:{PORT}/Movies/Details/1` in etwas wie `http://localhost:{PORT}/Movies/Details/12345` geändert wird (oder einen anderen Wert, der keinen tatsächlichen Film darstellt).</span><span class="sxs-lookup"><span data-stu-id="e8a86-111">For example, a hacker could introduce errors into the site by changing the URL created by the links from `http://localhost:{PORT}/Movies/Details/1` to something like  `http://localhost:{PORT}/Movies/Details/12345` (or some other value that doesn't represent an actual movie).</span></span> <span data-ttu-id="e8a86-112">Wenn Sie nicht nach einem NULL-Movie gesucht haben, löst die App keine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="e8a86-112">If you didn't check for a null movie, the app would throw an exception.</span></span>

<span data-ttu-id="e8a86-113">Untersuchen Sie die Methoden `Delete` und `DeleteConfirmed`.</span><span class="sxs-lookup"><span data-stu-id="e8a86-113">Examine the `Delete` and `DeleteConfirmed` methods.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Controllers/MoviesController.cs?name=snippet_delete)]

<span data-ttu-id="e8a86-114">Beachten Sie, dass die `HTTP GET Delete`-Methode den angegebenen Film nicht löscht, sondern eine Ansicht des Films zurückgibt, in der Sie den Löschvorgang (HttpPost) auslösen können.</span><span class="sxs-lookup"><span data-stu-id="e8a86-114">Note that the `HTTP GET Delete` method doesn't delete the specified movie, it returns a view of the movie where you can submit (HttpPost) the deletion.</span></span> <span data-ttu-id="e8a86-115">Das Ausführen eines Löschvorgangs als Reaktion auf eine GET-Anforderung (oder eigentlich eines Bearbeitungs-, Erstellungs- oder sonstigen Vorgangs, der Daten ändern) stellt eine Sicherheitslücke dar.</span><span class="sxs-lookup"><span data-stu-id="e8a86-115">Performing a delete operation in response to a GET request (or for that matter, performing an edit operation, create operation, or any other operation that changes data) opens up a security hole.</span></span>

<span data-ttu-id="e8a86-116">Die `[HttpPost]`-Methode, die die Daten löscht, heißt `DeleteConfirmed`, um der HTTP-POST-Methode eine eindeutige Signatur bzw. einen eindeutigen Namen zu geben.</span><span class="sxs-lookup"><span data-stu-id="e8a86-116">The `[HttpPost]` method that deletes the data is named `DeleteConfirmed` to give the HTTP POST method a unique signature or name.</span></span> <span data-ttu-id="e8a86-117">Die beiden Methodensignaturen werden nachstehend gezeigt:</span><span class="sxs-lookup"><span data-stu-id="e8a86-117">The two method signatures are shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete2)]

[!code-csharp[](start-mvc/sample/MvcMovie/Controllers/MoviesController.cs?name=snippet_delete3)]

<span data-ttu-id="e8a86-118">Die Common Language Runtime (CLR) erfordert überladene Methoden, um eine eindeutige Parametersignatur zu erhalten (selber Methodenname, aber unterschiedliche Liste von Parametern).</span><span class="sxs-lookup"><span data-stu-id="e8a86-118">The common language runtime (CLR) requires overloaded methods to have a unique parameter signature (same method name but different list of parameters).</span></span> <span data-ttu-id="e8a86-119">Hier benötigen Sie jedoch zwei `Delete`-Methoden (eine für GET und eine für POST), die beide die gleiche Parametersignatur haben.</span><span class="sxs-lookup"><span data-stu-id="e8a86-119">However, here you need two `Delete` methods -- one for GET and one for POST -- that both have the same parameter signature.</span></span> <span data-ttu-id="e8a86-120">(Beide müssen eine einzelne ganze Zahl als Parameter akzeptieren.)</span><span class="sxs-lookup"><span data-stu-id="e8a86-120">(They both need to accept a single integer as a parameter.)</span></span>

<span data-ttu-id="e8a86-121">Es gibt zwei Ansätze zur Lösung dieses Problems. Eine besteht darin, die Methoden unterschiedlich zu benennen.</span><span class="sxs-lookup"><span data-stu-id="e8a86-121">There are two approaches to this problem, one is to give the methods different names.</span></span> <span data-ttu-id="e8a86-122">Dafür das der Gerüstbaumechanismus im vorherigen Beispiel gesorgt.</span><span class="sxs-lookup"><span data-stu-id="e8a86-122">That's what the scaffolding mechanism did in the preceding example.</span></span> <span data-ttu-id="e8a86-123">Dies bringt jedoch ein kleines Problem mit sich: ASP.NET ordnet Segmente einer URL anhand des Namens zu Aktionsmethoden zu. Wenn Sie die Methode umbenennen sollten, ist das Routing normalerweise nicht in der Lage, diese Methode zu finden.</span><span class="sxs-lookup"><span data-stu-id="e8a86-123">However, this introduces a small problem: ASP.NET maps segments of a URL to action methods by name, and if you rename a method, routing normally wouldn't be able to find that method.</span></span> <span data-ttu-id="e8a86-124">Die Lösung besteht (wie im Beispiel) im Hinzufügen des `ActionName("Delete")`-Attributs zur `DeleteConfirmed`-Methode.</span><span class="sxs-lookup"><span data-stu-id="e8a86-124">The solution is what you see in the example, which is to add the `ActionName("Delete")` attribute to the `DeleteConfirmed` method.</span></span> <span data-ttu-id="e8a86-125">Dieses Attribut führt die Zuordnung für das Routingsystem durch, sodass eine URL, die „/Delete/“ für eine POST-Anforderung enthält, die `DeleteConfirmed`-Methode findet.</span><span class="sxs-lookup"><span data-stu-id="e8a86-125">That attribute performs mapping for the routing system so that a URL that includes /Delete/ for a POST request will find the `DeleteConfirmed` method.</span></span>

<span data-ttu-id="e8a86-126">Eine weitere gängige Behelfslösung für Methoden mit identischen Namen und Signaturen ist das künstliche Ändern der POST-Methode durch Hinzufügen eines zusätzlichen (nicht verwendeten) Parameters.</span><span class="sxs-lookup"><span data-stu-id="e8a86-126">Another common work around for methods that have identical names and signatures is to artificially change the signature of the POST method to include an extra (unused) parameter.</span></span> <span data-ttu-id="e8a86-127">Siehe dazu die vorherige POST-Anforderung, der wir den `notUsed`-Parameter hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="e8a86-127">That's what we did in a previous post when we added the `notUsed` parameter.</span></span> <span data-ttu-id="e8a86-128">Sie können dasselbe hier für die `[HttpPost] Delete`-Methode ausführen:</span><span class="sxs-lookup"><span data-stu-id="e8a86-128">You could do the same thing here for the `[HttpPost] Delete` method:</span></span>

```csharp
// POST: Movies/Delete/6
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Delete(int id, bool notUsed)
```

### <a name="publish-to-azure"></a><span data-ttu-id="e8a86-129">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="e8a86-129">Publish to Azure</span></span>

<span data-ttu-id="e8a86-130">Informationen zum Bereitstellen in Azure finden Sie unter [Tutorial: Erstellen einer ASP.NET Core- und SQL-Datenbank-App in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="e8a86-130">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core and SQL Database app in Azure App Service](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e8a86-131">Vorherige</span><span class="sxs-lookup"><span data-stu-id="e8a86-131">Previous</span></span>](validation.md)
