---
title: 'Teil 2: Hinzufügen eines Controllers zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 2 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 01/23/2021
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
uid: tutorials/first-mvc-app/adding-controller
ms.custom: contperf-fy21q3
ms.openlocfilehash: 47bb9b96bd5565a3a67f3cbdf9a4b6bc1f987447
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975260"
---
# <a name="part-2-add-a-controller-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="94a65-103">Teil 2: Hinzufügen eines Controllers zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="94a65-103">Part 2, add a controller to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="94a65-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94a65-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="94a65-105">Das Architekturmodell Model-View-Controller (MVC) trennt eine App in drei Hauptkomponenten: **M** odel (Modell), **V** iew (Ansicht) und **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="94a65-105">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="94a65-106">Das MVC-Muster hilft beim Erstellen von Apps, die einfacher zu testen und zu aktualisieren sind als herkömmliche monolithische Apps.</span><span class="sxs-lookup"><span data-stu-id="94a65-106">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span>

<span data-ttu-id="94a65-107">MVC-basierte Apps enthalten Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-107">MVC-based apps contain:</span></span>

* <span data-ttu-id="94a65-108">**M** odelle: Klassen, die die Daten der App darstellen.</span><span class="sxs-lookup"><span data-stu-id="94a65-108">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="94a65-109">Die Modellklassen verwenden Validierungslogik zum Erzwingen von Geschäftsregeln für diese Daten.</span><span class="sxs-lookup"><span data-stu-id="94a65-109">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="94a65-110">In der Regel wird der Modellstatus von Modellobjekten in einer Datenbank abgerufen und gespeichert.</span><span class="sxs-lookup"><span data-stu-id="94a65-110">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="94a65-111">In diesem Tutorial ruft ein `Movie`-Modell Daten aus einer Datenbank ab, stellt sie der Ansicht bereit und aktualisiert sie.</span><span class="sxs-lookup"><span data-stu-id="94a65-111">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="94a65-112">Aktualisierte Daten werden in eine Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="94a65-112">Updated data is written to a database.</span></span>
* <span data-ttu-id="94a65-113">**V** iews (Ansichten): Ansichten sind die Komponenten, die die Benutzeroberfläche der App anzeigen.</span><span class="sxs-lookup"><span data-stu-id="94a65-113">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="94a65-114">Im Allgemeinen werden die Modelldaten auf dieser Benutzeroberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94a65-114">Generally, this UI displays the model data.</span></span>
* <span data-ttu-id="94a65-115">**C** ontroller: Klassen, die Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="94a65-115">**C** ontrollers: Classes that:</span></span>
  * <span data-ttu-id="94a65-116">Verarbeiten von Browseranforderungen</span><span class="sxs-lookup"><span data-stu-id="94a65-116">Handle browser requests.</span></span>
  * <span data-ttu-id="94a65-117">Abrufen von Modelldaten</span><span class="sxs-lookup"><span data-stu-id="94a65-117">Retrieve model data.</span></span>
  * <span data-ttu-id="94a65-118">Aufrufen von Ansichtsvorlagen, die eine Antwort zurückgeben</span><span class="sxs-lookup"><span data-stu-id="94a65-118">Call view templates that return a response.</span></span>

<span data-ttu-id="94a65-119">In einer MVC-App zeigt die Ansicht nur Informationen an.</span><span class="sxs-lookup"><span data-stu-id="94a65-119">In an MVC app, the view only displays information.</span></span> <span data-ttu-id="94a65-120">Der Controller verarbeitet Benutzereingaben und Interaktionen und reagiert darauf.</span><span class="sxs-lookup"><span data-stu-id="94a65-120">The controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="94a65-121">Er verarbeitet z. B. URL-Segmente und Werte von Abfragezeichenfolgen und übergibt diese Werte an das Modell.</span><span class="sxs-lookup"><span data-stu-id="94a65-121">For example, the controller handles URL segments and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="94a65-122">Das Modell kann diese Werte nutzen, um die Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="94a65-122">The model might use these values to query the database.</span></span> <span data-ttu-id="94a65-123">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="94a65-123">For example:</span></span>

* <span data-ttu-id="94a65-124">`Https://localhost:5001/Home/Privacy` gibt den Controller `Home` und die Aktion `Privacy` an.</span><span class="sxs-lookup"><span data-stu-id="94a65-124">`Https://localhost:5001/Home/Privacy`: specifies the `Home`  controller  and the `Privacy` action.</span></span>
* <span data-ttu-id="94a65-125">`Https://localhost:5001/Movies/Edit/5` ist eine Anforderung zum Bearbeiten des Films mit der ID=5 mithilfe des Controllers `Movies` und der Aktion `Edit`, die später in diesem Tutorial ausführlich erläutert werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-125">`Https://localhost:5001/Movies/Edit/5`: is a request to edit the movie with ID=5 using the `Movies` controller and the `Edit` action, which are detailed later in the tutorial.</span></span>

<span data-ttu-id="94a65-126">Routendaten werden weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="94a65-126">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="94a65-127">Das Architekturmuster MVC unterteilt Apps in drei Hauptkomponentengruppen: Modelle (Models), Ansichten (Views) und Controller (Controllers).</span><span class="sxs-lookup"><span data-stu-id="94a65-127">The MVC architectural pattern separates an app into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="94a65-128">Dieses Muster hilft dabei, eine Trennung von Aspekten zu erreichen. Die Benutzeroberflächenlogik gehört hierbei zu den Ansichten.</span><span class="sxs-lookup"><span data-stu-id="94a65-128">This pattern helps to achieve separation of concerns: The UI logic belongs in the view.</span></span> <span data-ttu-id="94a65-129">Die Eingabelogik gehört zum Controller.</span><span class="sxs-lookup"><span data-stu-id="94a65-129">Input logic belongs in the controller.</span></span> <span data-ttu-id="94a65-130">Die Geschäftslogik gehört zum Modell.</span><span class="sxs-lookup"><span data-stu-id="94a65-130">Business logic belongs in the model.</span></span> <span data-ttu-id="94a65-131">Diese Trennung hilft beim Umgang mit Komplexität beim Entwickeln von Apps, da separat an den einzelnen Aspekten der Implementierung gearbeitet werden kann, ohne dabei den Code eines anderen Aspekts zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="94a65-131">This separation helps manage complexity when building an app, because it enables work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="94a65-132">Sie können beispielsweise am Code der Ansicht unabhängig vom Code für die Geschäftslogik arbeiten.</span><span class="sxs-lookup"><span data-stu-id="94a65-132">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="94a65-133">Diese Konzepte werden in dieser Tutorialreihe am Beispiel der Entwicklung einer Film-App vorgestellt und gezeigt.</span><span class="sxs-lookup"><span data-stu-id="94a65-133">These concepts are introduced and demonstrated in this tutorial series while building a movie app.</span></span> <span data-ttu-id="94a65-134">Das MVC-Projekt enthält Ordner für die *Controller* und *Views* (Ansichten).</span><span class="sxs-lookup"><span data-stu-id="94a65-134">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="94a65-135">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="94a65-135">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a65-136">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a65-136">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94a65-137">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Controller**.</span><span class="sxs-lookup"><span data-stu-id="94a65-137">In the **Solution Explorer**, right-click **Controllers > Add > Controller**.</span></span>

![Projektmappen-Explorer, Klick mit der rechten Maustaste auf Controller > Hinzufügen > Controller](~/tutorials/first-mvc-app/adding-controller/_static/add_controllercopyVS19v16.9.png)

<span data-ttu-id="94a65-139">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller – leer** aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-139">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**.</span></span>

![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app/adding-controller/_static/acCopyVS19v16.9.png)

<span data-ttu-id="94a65-141">Geben Sie im **Dialogfeld „Neues Element hinzufügen: MvcMovie“** **HelloWorldController.cs** ein, und klicken Sie auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="94a65-141">In the **Add New Item - MvcMovie dialog**, enter **HelloWorldController.cs** and select **Add**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a65-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a65-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94a65-143">Wählen Sie das **EXPLORER**-Symbol aus, klicken Sie dann bei gedrückter STRG-TASTE bzw. mit der rechten Maustaste auf **Controller > Neue Datei**, und geben Sie der neuen Datei den Namen *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="94a65-143">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

![Kontextmenü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_fileVSC1.51.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a65-145">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a65-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a65-146">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="94a65-146">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Kontextmenü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="94a65-148">Wählen Sie **ASP.NET Core** und **-Controllerklasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-148">Select **ASP.NET Core** and **Controller Class**.</span></span>

<span data-ttu-id="94a65-149">Nennen Sie den Controller **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="94a65-149">Name the controller **HelloWorldController**.</span></span>

![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="94a65-151">Ersetzen Sie den Inhalt von *Controllers/HelloWorldController.cs* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-151">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="94a65-152">Jede `public`-Methode in einem Controller kann als HTTP-Endpunkt aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-152">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="94a65-153">Im obigen Beispiel geben beide Methoden eine Zeichenfolge zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-153">In the sample above, both methods return a string.</span></span> <span data-ttu-id="94a65-154">Beachten Sie die Kommentare vor jeder Methode.</span><span class="sxs-lookup"><span data-stu-id="94a65-154">Note the comments preceding each method.</span></span>

<span data-ttu-id="94a65-155">Für HTTP-Endpunkte gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-155">An HTTP endpoint:</span></span>

* <span data-ttu-id="94a65-156">Sie sind als Ziel verwendbare URLs in der Webanwendung, z. B.: `https://localhost:5001/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="94a65-156">Is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`.</span></span>
* <span data-ttu-id="94a65-157">Sie kombinieren:</span><span class="sxs-lookup"><span data-stu-id="94a65-157">Combines:</span></span>
  * <span data-ttu-id="94a65-158">Das verwendete Protokoll: `HTTPS`</span><span class="sxs-lookup"><span data-stu-id="94a65-158">The protocol used: `HTTPS`.</span></span>
  * <span data-ttu-id="94a65-159">Die Netzwerkadresse des Webservers, einschließlich des TCP-Ports: `localhost:5001`</span><span class="sxs-lookup"><span data-stu-id="94a65-159">The network location of the web server, including the TCP port: `localhost:5001`.</span></span>
  * <span data-ttu-id="94a65-160">Den Ziel-URI: `HelloWorld`</span><span class="sxs-lookup"><span data-stu-id="94a65-160">The target URI: `HelloWorld`.</span></span>

<span data-ttu-id="94a65-161">Der erste Kommentar besagt, dass es sich dabei um eine [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET)-Methode handelt, die durch Anfügen von `/HelloWorld/` an die Basis-URL aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-161">The first comment states this is an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods/GET) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span>

<span data-ttu-id="94a65-162">Der zweite Kommentar gibt eine [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods)-Methode an, die aufgerufen wird, indem `/HelloWorld/Welcome/` an die URL angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-162">The second comment specifies an [HTTP GET](https://developer.mozilla.org/docs/Web/HTTP/Methods) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="94a65-163">Im weiteren Verlauf des Tutorials wird die Gerüstbau-Engine verwendet, um `HTTP POST`-Methoden zu generieren, die Daten aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="94a65-163">Later on in the tutorial, the scaffolding engine is used to generate `HTTP POST` methods, which update data.</span></span>

<span data-ttu-id="94a65-164">Führen Sie die App ohne den Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-164">Run the app without the debugger.</span></span>

<span data-ttu-id="94a65-165">Fügen Sie „HelloWorld“ an den Pfad in der Adressleiste an.</span><span class="sxs-lookup"><span data-stu-id="94a65-165">Append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="94a65-166">Die `Index`-Methode gibt eine Zeichenfolge zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-166">The `Index` method returns a string.</span></span>

![Browserfenster mit der App-Antwort: „This is my default action...“ (Dies ist meine Standardaktion)](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="94a65-168">MVC ruft Controllerklassen und die darin enthaltenen Aktionsmethoden abhängig von der eingehenden URL auf.</span><span class="sxs-lookup"><span data-stu-id="94a65-168">MVC invokes controller classes, and the action methods within them, depending on the incoming URL.</span></span> <span data-ttu-id="94a65-169">Die von MVC verwendete [URL-Standardroutinglogik](xref:mvc/controllers/routing) verwendet ein Format wie dieses, um den aufzurufenden Code zu bestimmen:</span><span class="sxs-lookup"><span data-stu-id="94a65-169">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC, uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="94a65-170">Das Routingformat ist in der `Configure`-Methode in der Datei *Startup.cs* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="94a65-170">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="94a65-171">Wenn Sie zu der App navigieren und keine URL-Segmente angeben, werden standardmäßig der Controller „Home“ und die Methode „Index“ verwendet, die in der oben hervorgehobenen Vorlagenzeile angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-171">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>  <span data-ttu-id="94a65-172">Für die obigen URL-Segmente gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-172">In the preceding URL segments:</span></span>

* <span data-ttu-id="94a65-173">Das erste URL-Segment bestimmt die auszuführende Controllerklasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-173">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="94a65-174">Daher wird `localhost:5001/HelloWorld` der **HelloWorld**-Controllerklasse zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="94a65-174">So `localhost:5001/HelloWorld` maps to the **HelloWorld** Controller class.</span></span>
* <span data-ttu-id="94a65-175">Der zweite Teil des URL-Segments bestimmt die Aktionsmethode für die Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-175">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="94a65-176">`localhost:5001/HelloWorld/Index` bewirkt also das Ausführen der `Index`-Methode der `HelloWorldController`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-176">So `localhost:5001/HelloWorld/Index` causes the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="94a65-177">Beachten Sie, dass Sie nur zu `localhost:5001/HelloWorld` navigieren mussten und die `Index`-Methode standardmäßig aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="94a65-177">Notice that you only had to browse to `localhost:5001/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="94a65-178">`Index` ist die Standardmethode, die für einen Controller aufgerufen wird, wenn der Methodenname nicht explizit angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-178">`Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span>
* <span data-ttu-id="94a65-179">Der dritte Teil des URL-Segments (`id`) ist für Routendaten.</span><span class="sxs-lookup"><span data-stu-id="94a65-179">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="94a65-180">Routendaten werden weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="94a65-180">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="94a65-181">Navigieren Sie zu `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="94a65-181">Browse to: `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="94a65-182">Ersetzen Sie dabei `{PORT}` durch Ihre Portnummer.</span><span class="sxs-lookup"><span data-stu-id="94a65-182">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="94a65-183">Die `Welcome`-Methode wird ausgeführt und gibt die Zeichenfolge `This is the Welcome action method...` zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-183">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="94a65-184">Bei dieser URL ist `HelloWorld` der Controller und `Welcome` die Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="94a65-184">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="94a65-185">Sie haben den Teil `[Parameters]` der URL noch nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a65-185">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Browserfenster mit der Anwendungsantwort „This is the Welcome action method“](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="94a65-187">Ändern Sie den Code so, dass Parameterinformationen von der URL an den Controller übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-187">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="94a65-188">Beispielsweise `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="94a65-188">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span>

<span data-ttu-id="94a65-189">Ändern Sie `Welcome`-Methode so, dass zwei Parameter, wie im folgenden Code gezeigt, einbezogen werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-189">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="94a65-190">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a65-190">The preceding code:</span></span>

* <span data-ttu-id="94a65-191">Verwendet das C#-Feature „optional-parameter“, um anzugeben, dass der `numTimes`-Parameter standardmäßig 1 ist, wenn kein anderer Wert für diesen Parameter übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-191">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span>
* <span data-ttu-id="94a65-192">Verwendet `HtmlEncoder.Default.Encode`, um die App vor schädlichen Eingaben (z. B. über JavaScript) zu schützen</span><span class="sxs-lookup"><span data-stu-id="94a65-192">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input, such as through JavaScript.</span></span>
* <span data-ttu-id="94a65-193">Verwendet [interpolierte Zeichenfolgen](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="94a65-193">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span>

<span data-ttu-id="94a65-194">Führen Sie die App aus, und navigieren Sie zu `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="94a65-194">Run the app and browse to: `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="94a65-195">Ersetzen Sie dabei `{PORT}` durch Ihre Portnummer.</span><span class="sxs-lookup"><span data-stu-id="94a65-195">Replace `{PORT}` with your port number.</span></span>

<span data-ttu-id="94a65-196">Probieren Sie in der URL verschiedene Werte für `name` und `numtimes` aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-196">Try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="94a65-197">Das MVC-[Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter aus der Abfragezeichenfolge den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="94a65-197">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string to parameters in the method.</span></span> <span data-ttu-id="94a65-198">Weitere Informationen finden Sie unter [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="94a65-198">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Browserfenster mit der Anwendungsantwort Hello Rick, NumTimes is\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="94a65-200">Für die obige Abbildung gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-200">In the previous image:</span></span>

* <span data-ttu-id="94a65-201">Das URL-Segment `Parameters` wird nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a65-201">The URL segment `Parameters` isn't used.</span></span>
* <span data-ttu-id="94a65-202">Die Parameter `name` und `numTimes` werden in der [Abfragezeichenfolge](https://wikipedia.org/wiki/Query_string) übergeben.</span><span class="sxs-lookup"><span data-stu-id="94a65-202">The `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span>
* <span data-ttu-id="94a65-203">Das Fragezeichen (`?`) in der obigen URL ist ein Trennzeichen, danach folgt die Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="94a65-203">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span>
* <span data-ttu-id="94a65-204">Das Zeichen `&` trennt Feld-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="94a65-204">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="94a65-205">Ersetzen Sie die `Welcome`-Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a65-205">Replace the `Welcome` method with the following code:</span></span>

  [!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="94a65-206">Führen Sie die App aus, und geben Sie die folgende URL ein: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="94a65-206">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="94a65-207">Für die obige URL gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-207">In the preceding URL:</span></span>

* <span data-ttu-id="94a65-208">Das dritte URL-Segment stimmte mit dem Routenparameter `id` überein.</span><span class="sxs-lookup"><span data-stu-id="94a65-208">The third URL segment matched the route parameter `id`.</span></span> 
* <span data-ttu-id="94a65-209">Die `Welcome`-Methode enthält den Parameter `id`, der mit der URL-Vorlage in der `MapControllerRoute`-Methode übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="94a65-209">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="94a65-210">Das nachfolgende `?` (in `id?`) gibt an, dass der Parameter `id` optional ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-210">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Startup.cs?name=snippet_route&highlight=5)]

<span data-ttu-id="94a65-211">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="94a65-211">In the preceding example:</span></span>

* <span data-ttu-id="94a65-212">Das dritte URL-Segment stimmte mit dem Routenparameter `id` überein.</span><span class="sxs-lookup"><span data-stu-id="94a65-212">The third URL segment matched the route parameter `id`.</span></span>
* <span data-ttu-id="94a65-213">Die `Welcome`-Methode enthält den Parameter `id`, der mit der URL-Vorlage in der `MapControllerRoute`-Methode übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="94a65-213">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapControllerRoute` method.</span></span>
* <span data-ttu-id="94a65-214">Das nachfolgende `?` (in `id?`) gibt an, dass der Parameter `id` optional ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-214">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="94a65-215">[Vorheriger Artikel: Erste Schritte](start-mvc.md)
> [Nächster Artikel: Hinzufügen einer Ansicht](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="94a65-215">[Previous: Get Started](start-mvc.md)
[Next: Add a View](adding-view.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="94a65-216">Das Architekturmodell Model-View-Controller (MVC) trennt eine App in drei Hauptkomponenten: **M** odel (Modell), **V** iew (Ansicht) und **C** ontroller.</span><span class="sxs-lookup"><span data-stu-id="94a65-216">The Model-View-Controller (MVC) architectural pattern separates an app into three main components: **M** odel, **V** iew, and **C** ontroller.</span></span> <span data-ttu-id="94a65-217">Das MVC-Muster hilft beim Erstellen von Apps, die einfacher zu testen und zu aktualisieren sind als herkömmliche monolithische Apps.</span><span class="sxs-lookup"><span data-stu-id="94a65-217">The MVC pattern helps you create apps that are more testable and easier to update than traditional monolithic apps.</span></span> <span data-ttu-id="94a65-218">MVC-basierte Apps enthalten Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-218">MVC-based apps contain:</span></span>

* <span data-ttu-id="94a65-219">**M** odelle: Klassen, die die Daten der App darstellen.</span><span class="sxs-lookup"><span data-stu-id="94a65-219">**M** odels: Classes that represent the data of the app.</span></span> <span data-ttu-id="94a65-220">Die Modellklassen verwenden Validierungslogik zum Erzwingen von Geschäftsregeln für diese Daten.</span><span class="sxs-lookup"><span data-stu-id="94a65-220">The model classes use validation logic to enforce business rules for that data.</span></span> <span data-ttu-id="94a65-221">In der Regel wird der Modellstatus von Modellobjekten in einer Datenbank abgerufen und gespeichert.</span><span class="sxs-lookup"><span data-stu-id="94a65-221">Typically, model objects retrieve and store model state in a database.</span></span> <span data-ttu-id="94a65-222">In diesem Tutorial ruft ein `Movie`-Modell Daten aus einer Datenbank ab, stellt sie der Ansicht bereit und aktualisiert sie.</span><span class="sxs-lookup"><span data-stu-id="94a65-222">In this tutorial, a `Movie` model retrieves movie data from a database, provides it to the view or updates it.</span></span> <span data-ttu-id="94a65-223">Aktualisierte Daten werden in eine Datenbank geschrieben.</span><span class="sxs-lookup"><span data-stu-id="94a65-223">Updated data is written to a database.</span></span>

* <span data-ttu-id="94a65-224">**V** iews (Ansichten): Ansichten sind die Komponenten, die die Benutzeroberfläche der App anzeigen.</span><span class="sxs-lookup"><span data-stu-id="94a65-224">**V** iews: Views are the components that display the app's user interface (UI).</span></span> <span data-ttu-id="94a65-225">Im Allgemeinen werden die Modelldaten auf dieser Benutzeroberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="94a65-225">Generally, this UI displays the model data.</span></span>

* <span data-ttu-id="94a65-226">**C** ontroller: Klassen, die Browseranforderungen verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="94a65-226">**C** ontrollers: Classes that handle browser requests.</span></span> <span data-ttu-id="94a65-227">Sie rufen Modelldaten ab und rufen Ansichtsvorlagen auf, die eine Antwort zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="94a65-227">They retrieve model data and call view templates that return a response.</span></span> <span data-ttu-id="94a65-228">In einer MVC-Anwendung zeigt die Ansicht nur Informationen. Benutzereingaben und -interaktionen werden vom Controller beantwortet und verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="94a65-228">In an MVC app, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="94a65-229">Der Controller verarbeitet beispielsweise Routendaten und Werte von Abfragezeichenfolgen, die an das Modell übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-229">For example, the controller handles route data and query-string values, and passes these values to the model.</span></span> <span data-ttu-id="94a65-230">Das Modell kann diese Werte nutzen, um die Datenbank abzufragen.</span><span class="sxs-lookup"><span data-stu-id="94a65-230">The model might use these values to query the database.</span></span> <span data-ttu-id="94a65-231">Beispiel: `https://localhost:5001/Home/About` hat Routendaten von `Home` (Controller) und `About` (für den Controller „Home“ aufzurufende Aktionsmethode).</span><span class="sxs-lookup"><span data-stu-id="94a65-231">For example, `https://localhost:5001/Home/About` has route data of `Home` (the controller) and `About` (the action method to call on the home controller).</span></span> <span data-ttu-id="94a65-232">`https://localhost:5001/Movies/Edit/5` ist eine Anforderung zum Bearbeiten des Films mit der ID 5 mithilfe des „movie“-Controllers.</span><span class="sxs-lookup"><span data-stu-id="94a65-232">`https://localhost:5001/Movies/Edit/5` is a request to edit the movie with ID=5 using the movie controller.</span></span> <span data-ttu-id="94a65-233">Routendaten werden weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="94a65-233">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="94a65-234">Das MVC-Muster hilft Ihnen beim Erstellen von Apps, die deren verschiedenen Aspekte (Eingabelogik, Geschäftslogik und Benutzeroberflächenlogik) trennt und zugleich eine lose Kopplung zwischen diesen Elementen bietet.</span><span class="sxs-lookup"><span data-stu-id="94a65-234">The MVC pattern helps you create apps that separate the different aspects of the app (input logic, business logic, and UI logic), while providing a loose coupling between these elements.</span></span> <span data-ttu-id="94a65-235">Das Muster gibt an, wo sich jede Art von Logik in der App befinden soll.</span><span class="sxs-lookup"><span data-stu-id="94a65-235">The pattern specifies where each kind of logic should be located in the app.</span></span> <span data-ttu-id="94a65-236">Die Benutzeroberflächenlogik ist Teil der Ansicht (view).</span><span class="sxs-lookup"><span data-stu-id="94a65-236">The UI logic belongs in the view.</span></span> <span data-ttu-id="94a65-237">Die Eingabelogik gehört zum Controller.</span><span class="sxs-lookup"><span data-stu-id="94a65-237">Input logic belongs in the controller.</span></span> <span data-ttu-id="94a65-238">Die Geschäftslogik gehört zum Modell.</span><span class="sxs-lookup"><span data-stu-id="94a65-238">Business logic belongs in the model.</span></span> <span data-ttu-id="94a65-239">Diese Trennung ermöglicht Ihnen das Bewältigen von Komplexität beim Erstellen einer App, da Sie zu einem Zeitpunkt an einem Aspekt der Implementierung arbeiten können, ohne den Code eines anderen zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="94a65-239">This separation helps you manage complexity when you build an app, because it enables you to work on one aspect of the implementation at a time without impacting the code of another.</span></span> <span data-ttu-id="94a65-240">Sie können beispielsweise am Code der Ansicht unabhängig vom Code für die Geschäftslogik arbeiten.</span><span class="sxs-lookup"><span data-stu-id="94a65-240">For example, you can work on the view code without depending on the business logic code.</span></span>

<span data-ttu-id="94a65-241">Wir behandeln diese Konzepte in dieser Tutorialreihe und zeigen Ihnen, wie Sie sie zum Erstellen einer Film-App nutzen.</span><span class="sxs-lookup"><span data-stu-id="94a65-241">We cover these concepts in this tutorial series and show you how to use them to build a movie app.</span></span> <span data-ttu-id="94a65-242">Das MVC-Projekt enthält Ordner für die *Controller* und *Views* (Ansichten).</span><span class="sxs-lookup"><span data-stu-id="94a65-242">The MVC project contains folders for the *Controllers* and *Views*.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="94a65-243">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="94a65-243">Add a controller</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94a65-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94a65-244">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94a65-245">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Controller**.</span><span class="sxs-lookup"><span data-stu-id="94a65-245">In **Solution Explorer**, right-click **Controllers > Add > Controller**</span></span>

  ![Kontextmenü](~/tutorials/first-mvc-app/adding-controller/_static/add_controller.png)

* <span data-ttu-id="94a65-247">Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller - leer** aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-247">In the **Add Scaffold** dialog box, select **MVC Controller - Empty**</span></span>

  ![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app/adding-controller/_static/ac.png)

* <span data-ttu-id="94a65-249">Geben Sie im **Dialogfeld zum Hinzufügen eines leeren MVC-Controllers**  den Namen **HelloWorldController** ein, und wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-249">In the **Add Empty MVC Controller dialog**, enter **HelloWorldController** and select **ADD**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94a65-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94a65-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94a65-251">Wählen Sie das **EXPLORER**-Symbol aus, klicken Sie dann bei gedrückter STRG-TASTE bzw. mit der rechten Maustaste auf **Controller > Neue Datei**, und geben Sie der neuen Datei den Namen *HelloWorldController.cs*.</span><span class="sxs-lookup"><span data-stu-id="94a65-251">Select the **EXPLORER** icon and then control-click (right-click) **Controllers > New File** and name the new file *HelloWorldController.cs*.</span></span>

  ![Kontextmenü](~/tutorials/first-mvc-app-xplat/adding-controller/_static/new_file.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94a65-253">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="94a65-253">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94a65-254">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller > Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="94a65-254">In **Solution Explorer**, right-click **Controllers > Add > New File**.</span></span>

![Kontextmenü](~/tutorials/first-mvc-app-mac/adding-controller/_static/add_controller.png)

<span data-ttu-id="94a65-256">Wählen Sie **ASP.NET Core** und **MVC-Controller-Klasse** aus.</span><span class="sxs-lookup"><span data-stu-id="94a65-256">Select **ASP.NET Core** and **MVC Controller Class**.</span></span>

<span data-ttu-id="94a65-257">Nennen Sie den Controller **HelloWorldController**.</span><span class="sxs-lookup"><span data-stu-id="94a65-257">Name the controller **HelloWorldController**.</span></span>

![Hinzufügen und Benennen des MVC-Controllers](~/tutorials/first-mvc-app-mac/adding-controller/_static/ac.png)

---

<span data-ttu-id="94a65-259">Ersetzen Sie den Inhalt von *Controllers/HelloWorldController.cs* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="94a65-259">Replace the contents of *Controllers/HelloWorldController.cs* with the following:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_1)]

<span data-ttu-id="94a65-260">Jede `public`-Methode in einem Controller kann als HTTP-Endpunkt aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-260">Every `public` method in a controller is callable as an HTTP endpoint.</span></span> <span data-ttu-id="94a65-261">Im obigen Beispiel geben beide Methoden eine Zeichenfolge zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-261">In the sample above, both methods return a string.</span></span> <span data-ttu-id="94a65-262">Beachten Sie die Kommentare vor jeder Methode.</span><span class="sxs-lookup"><span data-stu-id="94a65-262">Note the comments preceding each method.</span></span>

<span data-ttu-id="94a65-263">Ein HTTP-Endpunkt ist eine Ziel-URL in der Webanwendung, wie z.B. `https://localhost:5001/HelloWorld`, und kombiniert das verwendete Protokoll `HTTPS`, die Netzwerkadresse des Webservers (einschließlich TCP-Port) `localhost:5001` und den Ziel-URI `HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="94a65-263">An HTTP endpoint is a targetable URL in the web application, such as `https://localhost:5001/HelloWorld`, and combines the protocol used: `HTTPS`, the network location of the web server (including the TCP port): `localhost:5001` and the target URI `HelloWorld`.</span></span>

<span data-ttu-id="94a65-264">Der erste Kommentar besagt, dass es sich dabei um eine [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp)-Methode handelt, die durch Anfügen von `/HelloWorld/` an die Basis-URL aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-264">The first comment states this is an [HTTP GET](https://www.w3schools.com/tags/ref_httpmethods.asp) method that's invoked by appending `/HelloWorld/` to the base URL.</span></span> <span data-ttu-id="94a65-265">Der zweite Kommentar gibt eine [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html)-Methode an, die aufgerufen wird, indem `/HelloWorld/Welcome/` an die URL angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-265">The second comment specifies an [HTTP GET](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html) method that's invoked by appending `/HelloWorld/Welcome/` to the URL.</span></span> <span data-ttu-id="94a65-266">Im weiteren Verlauf des Tutorials wird die Gerüstbau-Engine verwendet, um `HTTP POST`-Methoden zu erstellen, mit denen Daten aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-266">Later on in the tutorial the scaffolding engine is used to generate `HTTP POST` methods which update data.</span></span>

<span data-ttu-id="94a65-267">Führen Sie die App im Nicht-Debugmodus aus, und fügen Sie „HelloWorld“ an den Pfad in der Adressleiste an.</span><span class="sxs-lookup"><span data-stu-id="94a65-267">Run the app in non-debug mode and append "HelloWorld" to the path in the address bar.</span></span> <span data-ttu-id="94a65-268">Die `Index`-Methode gibt eine Zeichenfolge zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-268">The `Index` method returns a string.</span></span>

![Browserfenster mit der Anwendungsantwort „This is my default action“](~/tutorials/first-mvc-app/adding-controller/_static/hell1.png)

<span data-ttu-id="94a65-270">MVC ruft Controllerklassen (und darin enthaltene Aktionsmethoden) abhängig von der eingehenden URL auf.</span><span class="sxs-lookup"><span data-stu-id="94a65-270">MVC invokes controller classes (and the action methods within them) depending on the incoming URL.</span></span> <span data-ttu-id="94a65-271">Die von MVC verwendete standardmäßige [URL-Routinglogik](xref:mvc/controllers/routing) befolgt ein Format wie dieses, um den aufzurufenden Code zu bestimmen:</span><span class="sxs-lookup"><span data-stu-id="94a65-271">The default [URL routing logic](xref:mvc/controllers/routing) used by MVC uses a format like this to determine what code to invoke:</span></span>

`/[Controller]/[ActionName]/[Parameters]`

<span data-ttu-id="94a65-272">Das Routingformat ist in der `Configure`-Methode in der Datei *Startup.cs* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="94a65-272">The routing format is set in the `Configure` method in *Startup.cs* file.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<!-- 
Add link to explain lambda.
Remove link for simplified tutorial.
-->

<span data-ttu-id="94a65-273">Wenn Sie zu der App navigieren und keine URL-Segmente angeben, werden standardmäßig der Controller „Home“ und die Methode „Index“ verwendet, die in der oben hervorgehobenen Vorlagenzeile angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-273">When you browse to the app and don't supply any URL segments, it defaults to the "Home" controller and the "Index" method specified in the template line highlighted above.</span></span>

<span data-ttu-id="94a65-274">Das erste URL-Segment bestimmt die auszuführende Controllerklasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-274">The first URL segment determines the controller class to run.</span></span> <span data-ttu-id="94a65-275">Daher wird `localhost:{PORT}/HelloWorld` der `HelloWorldController`-Klasse zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="94a65-275">So `localhost:{PORT}/HelloWorld` maps to the `HelloWorldController` class.</span></span> <span data-ttu-id="94a65-276">Der zweite Teil des URL-Segments bestimmt die Aktionsmethode für die Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-276">The second part of the URL segment determines the action method on the class.</span></span> <span data-ttu-id="94a65-277">Daher bewirkt `localhost:{PORT}/HelloWorld/Index` das Ausführen der `Index`-Methode der `HelloWorldController`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="94a65-277">So `localhost:{PORT}/HelloWorld/Index` would cause the `Index` method of the `HelloWorldController` class to run.</span></span> <span data-ttu-id="94a65-278">Beachten Sie, dass Sie nur zu `localhost:{PORT}/HelloWorld` navigieren mussten und die `Index`-Methode standardmäßig aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="94a65-278">Notice that you only had to browse to `localhost:{PORT}/HelloWorld` and the `Index` method was called by default.</span></span> <span data-ttu-id="94a65-279">Der Grund hierfür ist, dass `Index` die Standardmethode ist, die für einen Controller aufgerufen wird, wenn der Methodenname nicht explizit angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-279">This is because `Index` is the default method that will be called on a controller if a method name isn't explicitly specified.</span></span> <span data-ttu-id="94a65-280">Der dritte Teil des URL-Segments (`id`) ist für Routendaten.</span><span class="sxs-lookup"><span data-stu-id="94a65-280">The third part of the URL segment ( `id`) is for route data.</span></span> <span data-ttu-id="94a65-281">Routendaten werden weiter unten im Tutorial erläutert.</span><span class="sxs-lookup"><span data-stu-id="94a65-281">Route data is explained later in the tutorial.</span></span>

<span data-ttu-id="94a65-282">Wechseln Sie zu `https://localhost:{PORT}/HelloWorld/Welcome`.</span><span class="sxs-lookup"><span data-stu-id="94a65-282">Browse to `https://localhost:{PORT}/HelloWorld/Welcome`.</span></span> <span data-ttu-id="94a65-283">Die `Welcome`-Methode wird ausgeführt und gibt die Zeichenfolge `This is the Welcome action method...` zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-283">The `Welcome` method runs and returns the string `This is the Welcome action method...`.</span></span> <span data-ttu-id="94a65-284">Bei dieser URL ist `HelloWorld` der Controller und `Welcome` die Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="94a65-284">For this URL, the controller is `HelloWorld` and `Welcome` is the action method.</span></span> <span data-ttu-id="94a65-285">Sie haben den Teil `[Parameters]` der URL noch nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="94a65-285">You haven't used the `[Parameters]` part of the URL yet.</span></span>

![Browserfenster mit der Anwendungsantwort „This is the Welcome action method“](~/tutorials/first-mvc-app/adding-controller/_static/welcome.png)

<span data-ttu-id="94a65-287">Ändern Sie den Code so, dass Parameterinformationen von der URL an den Controller übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-287">Modify the code to pass some parameter information from the URL to the controller.</span></span> <span data-ttu-id="94a65-288">Beispielsweise `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span><span class="sxs-lookup"><span data-stu-id="94a65-288">For example, `/HelloWorld/Welcome?name=Rick&numtimes=4`.</span></span> <span data-ttu-id="94a65-289">Ändern Sie `Welcome`-Methode so, dass zwei Parameter, wie im folgenden Code gezeigt, einbezogen werden.</span><span class="sxs-lookup"><span data-stu-id="94a65-289">Change the `Welcome` method to include two parameters as shown in the following code.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_2)]

<span data-ttu-id="94a65-290">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="94a65-290">The preceding code:</span></span>

* <span data-ttu-id="94a65-291">Verwendet das C#-Feature „optional-parameter“, um anzugeben, dass der `numTimes`-Parameter standardmäßig 1 ist, wenn kein anderer Wert für diesen Parameter übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="94a65-291">Uses the C# optional-parameter feature to indicate that the `numTimes` parameter defaults to 1 if no value is passed for that parameter.</span></span> <!-- remove for simplified -->
* <span data-ttu-id="94a65-292">Verwendet `HtmlEncoder.Default.Encode`, um die App vor schädlicher Eingaben (über JavaScript) zu schützen.</span><span class="sxs-lookup"><span data-stu-id="94a65-292">Uses `HtmlEncoder.Default.Encode` to protect the app from malicious input (namely JavaScript).</span></span>
* <span data-ttu-id="94a65-293">Verwendet [interpolierte Zeichenfolgen](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span><span class="sxs-lookup"><span data-stu-id="94a65-293">Uses [Interpolated Strings](/dotnet/articles/csharp/language-reference/keywords/interpolated-strings) in `$"Hello {name}, NumTimes is: {numTimes}"`.</span></span> <!-- remove for simplified -->

<span data-ttu-id="94a65-294">Führen Sie die App aus, und navigieren Sie zu:</span><span class="sxs-lookup"><span data-stu-id="94a65-294">Run the app and browse to:</span></span>

   `https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="94a65-295">(Ersetzen Sie `{PORT}` durch Ihre Portnummer.) Sie können für `name` und `numtimes` in der URL verschiedene Werte ausprobieren.</span><span class="sxs-lookup"><span data-stu-id="94a65-295">(Replace `{PORT}` with your port number.) You can try different values for `name` and `numtimes` in the URL.</span></span> <span data-ttu-id="94a65-296">Das MVC-[Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="94a65-296">The MVC [model binding](xref:mvc/models/model-binding) system automatically maps the named parameters from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="94a65-297">Weitere Informationen finden Sie unter [Modellbindung](xref:mvc/models/model-binding).</span><span class="sxs-lookup"><span data-stu-id="94a65-297">See [Model Binding](xref:mvc/models/model-binding) for more information.</span></span>

![Browserfenster mit der Anwendungsantwort Hello Rick, NumTimes is\: 4](~/tutorials/first-mvc-app/adding-controller/_static/rick4.png)

<span data-ttu-id="94a65-299">In der obigen Abbildung wird das URL-Segment (`Parameters`) nicht verwendet, und die Parameter `name` und `numTimes` werden in der [Abfragezeichenfolge](https://wikipedia.org/wiki/Query_string) übergeben.</span><span class="sxs-lookup"><span data-stu-id="94a65-299">In the image above, the URL segment (`Parameters`) isn't used, the `name` and `numTimes` parameters are passed in the [query string](https://wikipedia.org/wiki/Query_string).</span></span> <span data-ttu-id="94a65-300">Das Fragezeichen (`?`) in der obigen URL ist ein Trennzeichen, danach folgt die Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="94a65-300">The `?` (question mark) in the above URL is a separator, and the query string follows.</span></span> <span data-ttu-id="94a65-301">Das Zeichen `&` trennt Feld-Wert-Paare.</span><span class="sxs-lookup"><span data-stu-id="94a65-301">The `&` character separates field-value pairs.</span></span>

<span data-ttu-id="94a65-302">Ersetzen Sie die `Welcome`-Methode durch folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="94a65-302">Replace the `Welcome` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_3)]

<span data-ttu-id="94a65-303">Führen Sie die App aus, und geben Sie die folgende URL ein: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span><span class="sxs-lookup"><span data-stu-id="94a65-303">Run the app and enter the following URL: `https://localhost:{PORT}/HelloWorld/Welcome/3?name=Rick`</span></span>

<span data-ttu-id="94a65-304">Dieses Mal hat das dritte URL-Segment mit dem Routenparameter `id` übereingestimmt.</span><span class="sxs-lookup"><span data-stu-id="94a65-304">This time the third URL segment matched the route parameter `id`.</span></span> <span data-ttu-id="94a65-305">Die `Welcome`-Methode enthält den Parameter `id`, der mit der URL-Vorlage in der `MapRoute`-Methode übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="94a65-305">The `Welcome` method contains a parameter `id` that matched the URL template in the `MapRoute` method.</span></span> <span data-ttu-id="94a65-306">Das nachfolgende `?` (in `id?`) gibt an, dass der Parameter `id` optional ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-306">The trailing `?` (in `id?`) indicates the `id` parameter is optional.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="94a65-307">In diesen Beispielen hat der Controller den „VC“-Teil von MVC übernommen, d. h. die Aufgaben von „View“ (Ansicht) und „Controller“.</span><span class="sxs-lookup"><span data-stu-id="94a65-307">In these examples the controller has been doing the "VC" portion of MVC - that is, the view and controller work.</span></span> <span data-ttu-id="94a65-308">Der Controller gibt direkt HTML zurück.</span><span class="sxs-lookup"><span data-stu-id="94a65-308">The controller is returning HTML directly.</span></span> <span data-ttu-id="94a65-309">Im Allgemeinen sollen Controller nicht direkt HTML zurückgeben, da dies sehr aufwändig zu programmieren und pflegen ist.</span><span class="sxs-lookup"><span data-stu-id="94a65-309">Generally you don't want controllers returning HTML directly, since that becomes very cumbersome to code and maintain.</span></span> <span data-ttu-id="94a65-310">Stattdessen verwenden Sie in der Regel eine separate Razor-Ansichtsvorlagendatei, um die HTML-Antwort zu generieren.</span><span class="sxs-lookup"><span data-stu-id="94a65-310">Instead you typically use a separate Razor view template file to help generate the HTML response.</span></span> <span data-ttu-id="94a65-311">Dies lernen Sie im nächsten Tutorial.</span><span class="sxs-lookup"><span data-stu-id="94a65-311">You do that in the next tutorial.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="94a65-312">[Zurück](start-mvc.md)
> [Weiter](adding-view.md)</span><span class="sxs-lookup"><span data-stu-id="94a65-312">[Previous](start-mvc.md)
[Next](adding-view.md)</span></span>

::: moniker-end
