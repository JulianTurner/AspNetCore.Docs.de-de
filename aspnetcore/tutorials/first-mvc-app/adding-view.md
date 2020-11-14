---
title: 'Teil 3: Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 3 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 8/04/2019
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
uid: tutorials/first-mvc-app/adding-view
ms.openlocfilehash: 56f39643ccd69c6508148374033208eb3b2f25ab
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422716"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="f0ead-103">Teil 3: Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="f0ead-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="f0ead-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f0ead-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0ead-105">In diesem Abschnitt ändern Sie die `HelloWorldController`-Klasse so, dass [Razor](xref:mvc/views/razor)-Ansichtsdateien verwendet werden, um den Prozess des Generierens von HTML-Antworten für einen Client sauber zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="f0ead-105">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="f0ead-106">Sie erstellen mithilfe von Razor eine Ansichtsvorlagendatei.</span><span class="sxs-lookup"><span data-stu-id="f0ead-106">You create a view template file using Razor.</span></span> <span data-ttu-id="f0ead-107">Razor-basierte Ansichtsvorlagen haben die Dateinamenerweiterung *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-107">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="f0ead-108">Sie bieten eine elegante Möglichkeit zum Erstellen einer HTML-Ausgabe mit C#.</span><span class="sxs-lookup"><span data-stu-id="f0ead-108">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="f0ead-109">Derzeit gibt die `Index`-Methode eine Zeichenfolge mit der Meldung zurück, die in der Controllerklasse hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="f0ead-109">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="f0ead-110">Ersetzen Sie in der `HelloWorldController`-Klasse die `Index`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f0ead-110">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="f0ead-111">Der vorangehende Code ruft die Methode<xref:Microsoft.AspNetCore.Mvc.Controller.View*> des Controllers auf.</span><span class="sxs-lookup"><span data-stu-id="f0ead-111">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="f0ead-112">Er verwendet eine Ansichtsvorlage zum Generieren einer HTML-Antwort.</span><span class="sxs-lookup"><span data-stu-id="f0ead-112">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="f0ead-113">Controllermethoden (auch *Aktionsmethoden* genannt), wie z. B. die `Index`-Methode oben, geben in der Regel ein <xref:Microsoft.AspNetCore.Mvc.IActionResult> (oder eine von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleitete Klasse) und keinen Typ wie `string` zurück.</span><span class="sxs-lookup"><span data-stu-id="f0ead-113">Controller methods (also known as *action methods* ), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="f0ead-114">Hinzufügen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="f0ead-114">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f0ead-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0ead-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0ead-116">Klicken Sie mit der rechten Maustaste auf den Ordner *Views* , und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-116">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="f0ead-117">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld* , und klicken Sie dann **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-117">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="f0ead-118">Im Dialogfeld **Neues Element hinzufügen: MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="f0ead-118">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="f0ead-119">Geben Sie in das Suchfeld rechts oben *Ansicht* ein.</span><span class="sxs-lookup"><span data-stu-id="f0ead-119">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="f0ead-120">Klicken Sie auf **Razor-Ansicht**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-120">Select **Razor View**</span></span>

  * <span data-ttu-id="f0ead-121">Übernehmen Sie den im Feld **Name** stehenden Wert *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-121">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="f0ead-122">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-122">Select **Add**</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f0ead-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0ead-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f0ead-125">Fügen Sie die Ansicht `Index` für `HelloWorldController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-125">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="f0ead-126">Fügen Sie einen neuen Ordner namens *Views/HelloWorld* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-126">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="f0ead-127">Fügen Sie dem Ordner *Views/HelloWorld* eine neue Datei namens *Index.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-127">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f0ead-128">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f0ead-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f0ead-129">Klicken Sie mit der rechten Maustaste auf den Ordner *Views* , und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-129">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="f0ead-130">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld* , und klicken Sie dann **Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-130">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="f0ead-131">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="f0ead-131">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f0ead-132">Wählen Sie im linken Bereich die Option **ASP .NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-132">Select **ASP .NET Core** in the left pane.</span></span>
  * <span data-ttu-id="f0ead-133">Wählen Sie im Hauptbereich die Option **MVC-Ansichtsseite** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-133">Select **MVC View Page** in the center pane.</span></span>
  * <span data-ttu-id="f0ead-134">Geben Sie in das Feld **Name** die Zeichenfolge *Index* ein.</span><span class="sxs-lookup"><span data-stu-id="f0ead-134">Type *Index* in the **Name** box.</span></span>
  * <span data-ttu-id="f0ead-135">Wählen Sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-135">Select **New**.</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="f0ead-137">Ersetzen Sie den Inhalt der Razor-Ansichtsdatei *Views/HelloWorld/Index.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f0ead-137">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="f0ead-138">Navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-138">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="f0ead-139">Die `Index`-Methode im `HelloWorldController` hatte nicht viel zu tun. Sie diente zum Ausführen der Anweisung `return View();`, die angab, dass die Methode eine Ansichtsvorlagendatei zum Rendern einer Antwort im Browser verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="f0ead-139">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="f0ead-140">Da kein Name für eine Ansichtsvorlagendatei angegeben wurde, verwendet MVC standardmäßig die Standardansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="f0ead-140">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="f0ead-141">Die Standardansichtsdatei hat den gleichen Namen wie die Methode (`Index`), sodass die Ansichtsvorlage in */Views/HelloWorld/Index.cshtml* verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-141">The default view file has the same name as the method (`Index`), so the view template in */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="f0ead-142">Die folgende Abbildung zeigt die Zeichenfolge "Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="f0ead-142">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="f0ead-143">die in der Ansicht hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="f0ead-143">hard-coded in the view.</span></span>

![Browserfenster](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="f0ead-145">Ändern von Ansichten und Layoutseiten</span><span class="sxs-lookup"><span data-stu-id="f0ead-145">Change views and layout pages</span></span>

<span data-ttu-id="f0ead-146">Wählen Sie die Menülinks aus ( **MvcMovie** , **Home** , **Privacy** ).</span><span class="sxs-lookup"><span data-stu-id="f0ead-146">Select the menu links ( **MvcMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="f0ead-147">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-147">Each page shows the same menu layout.</span></span> <span data-ttu-id="f0ead-148">Das Menülayout wird mithilfe der Datei *Views/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-148">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f0ead-149">Öffnen Sie die Datei *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-149">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f0ead-150">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-150">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="f0ead-151">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-151">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f0ead-152">`RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-152">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f0ead-153">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Views/Home/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-153">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="f0ead-154">Ändern des Titels, der Fußzeile und Menülinks in der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="f0ead-154">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="f0ead-155">Ersetzen Sie den Inhalt der Datei *Views/Shared/_Layout.cshtml* durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="f0ead-155">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="f0ead-156">Die Änderungen werden hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="f0ead-156">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]
::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f0ead-157">Im oben stehenden Markup wurden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="f0ead-157">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="f0ead-158">`MvcMovie` wurde dreimal in `Movie App` geändert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-158">3 occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="f0ead-159">Das Anchor-Element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` wurde in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` geändert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-159">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="f0ead-160">Im obigen Markup wurde das [Anchor-Tag-Hilfsprogramm-Attribut](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) von `asp-area=""` und der Attributwert ausgelassen, da für diese App keine [Bereiche](xref:mvc/controllers/areas) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-160">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="f0ead-161">**Hinweis:** Der `Movies`-Controller wurde nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-161">**Note** : The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="f0ead-162">An diesem Punkt ist der `Movie App`-Link nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="f0ead-162">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="f0ead-163">Speichern Sie Ihre Änderungen, und wählen den **Privacy** -Link aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-163">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="f0ead-164">Wie Sie sehen, wird auf der Browserregisterkarte der Titel **Privacy Policy - Movie App** anstelle des Titels **Privacy Policy - Mvc Movie** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f0ead-164">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie** :</span></span>

![Registerkarte „Privacy“](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="f0ead-166">Wählen Sie den Link **Home** aus, und beachten Sie, dass im Text für den Titel und den Anker ebenfalls **Movie App** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-166">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="f0ead-167">Wir haben also eine Änderung des Texts und Titels in der Layoutvorlage einmalig vorgenommen, die von der gesamten Website übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-167">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="f0ead-168">Untersuchen Sie die Datei *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f0ead-168">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="f0ead-169">Die Datei *Views/_ViewStart.cshtml* fügt jeder Ansicht die Datei *Views/Shared/_Layout.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-169">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="f0ead-170">Die `Layout`-Eigenschaft kann verwendet werden, um eine andere Layoutansicht festzulegen, oder legen Sie sie auf `null` fest, damit keine Layoutdatei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-170">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="f0ead-171">Ändern Sie den Titel und das `<h2>`-Element der Ansichtsdatei *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f0ead-171">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="f0ead-172">Der Titel und das `<h2>`-Element sind geringfügig anders, sodass Sie sehen können, welcher Codeabschnitt die Anzeige ändert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-172">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="f0ead-173">`ViewData["Title"] = "Movie List";` im Code oben legt die `Title`-Eigenschaft des Wörterbuchs `ViewData`auf „Movie List“ fest.</span><span class="sxs-lookup"><span data-stu-id="f0ead-173">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="f0ead-174">Die `Title`-Eigenschaft wird im HTML-Element `<title>` der Layoutseite verwendet:</span><span class="sxs-lookup"><span data-stu-id="f0ead-174">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="f0ead-175">Speichern Sie die Änderung, und navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-175">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="f0ead-176">Sie sehen, dass sich der Browsertitel, die primäre Überschrift und die sekundären Überschriften geändert haben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-176">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="f0ead-177">(Wenn die Änderungen nicht im Browser angezeigt werden, zeigen Sie ggf. zwischengespeicherten Inhalt an.</span><span class="sxs-lookup"><span data-stu-id="f0ead-177">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="f0ead-178">Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.) Der Titel des Browsers wird mithilfe des Elements `ViewData["Title"]` erstellt, das wir in der Ansichtsvorlange *Index.cshtml* festgelegt haben, und des zusätzlichen Elements „- Movie App“, das in der Layoutdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f0ead-178">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="f0ead-179">Der Inhalt der Ansichtsvorlage *Index.cshtml* wird mit dem der Ansichtsvorlage *Views/Shared/_Layout.cshtml* zusammengeführt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-179">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="f0ead-180">Eine einzelne HTML-Antwort wird an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="f0ead-180">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="f0ead-181">Layoutvorlagen erleichtern es Ihnen, Änderungen an allen Seiten einer App vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="f0ead-181">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="f0ead-182">Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="f0ead-182">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Ansicht mit Filmliste](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="f0ead-184">Unser kleiner Beitrag zu den „Daten“ (in diesem Fall die Meldung "Hello from our View Template!")</span><span class="sxs-lookup"><span data-stu-id="f0ead-184">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="f0ead-185">ist jedoch hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-185">message) is hard-coded, though.</span></span> <span data-ttu-id="f0ead-186">Die MVC-Anwendung weist ein „V“ (View [Ansicht]) auf, und Sie verfügen über ein „C“ (Controller), aber noch nicht über ein „M“ (Modell).</span><span class="sxs-lookup"><span data-stu-id="f0ead-186">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="f0ead-187">Übergeben von Daten vom Controller an die Ansicht</span><span class="sxs-lookup"><span data-stu-id="f0ead-187">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="f0ead-188">Controlleraktionen werden als Antwort auf eine eingehende URL-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="f0ead-188">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="f0ead-189">Eine Controllerklasse ist der Ort, an dem der Code geschrieben wird, der die eingehenden Browseranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f0ead-189">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="f0ead-190">Der Controller ruft Daten aus einer Datenquelle ab und entscheidet, welche Art von Antwort an den Browser zurückgesendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-190">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="f0ead-191">Ansichtsvorlagen können von einem Controller zum Generieren und Formatieren einer HTML-Antwort an den Browser verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-191">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="f0ead-192">Controller sind für die Bereitstellung der benötigten Daten zuständig, damit eine Ansichtsvorlage eine Antwort liefern kann.</span><span class="sxs-lookup"><span data-stu-id="f0ead-192">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="f0ead-193">Eine bewährte Methode: Ansichtsvorlagen sollten **keine** Geschäftslogik ausführen bzw. nicht direkt mit einer Datenbank interagieren.</span><span class="sxs-lookup"><span data-stu-id="f0ead-193">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="f0ead-194">Eine Ansichtsvorlage sollte stattdessen nur mit den Daten arbeiten, die ihr vom Controller bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-194">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="f0ead-195">Durch Beibehaltung dieser Bereichstrennung bleibt der Code übersichtlich, testfähig und verwaltbar.</span><span class="sxs-lookup"><span data-stu-id="f0ead-195">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="f0ead-196">Derzeit verwendet die `Welcome`-Methode in der `HelloWorldController`-Klasse die Parameter `name` und `ID` und gibt anschließend die Werte direkt an den Browser aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-196">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="f0ead-197">Anstatt den Controller diese Antwort als Zeichenfolge rendern zu lassen, passen Sie den Controller so an, dass er eine Ansichtsvorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f0ead-197">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="f0ead-198">Die Ansichtsvorlage erstellt eine dynamische Antwort, was bedeutet, dass entsprechende Datenbestandteile vom Controller an die Ansicht übergeben werden müssen, damit eine Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-198">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="f0ead-199">Lassen Sie hierzu den Controller die dynamischen Daten (Parameter), die die Ansichtsvorlage benötigt, in einem `ViewData`-Wörterbuch ablegen, auf das die Ansichtsvorlage zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="f0ead-199">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="f0ead-200">Ändern Sie in *HelloWorldController.cs* die `Welcome`-Methode so, dass die Werte `Message` und `NumTimes` dem Wörterbuch `ViewData` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-200">In *HelloWorldController.cs* , change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="f0ead-201">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, was bedeutet, dass jeder beliebige Typ verwendet werden kann. Das `ViewData`-Objekt hat erst dann definierte Eigenschaften, wenn Sie ihm etwas hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-201">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="f0ead-202">Das [MVC-Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter (`name` und `numTimes`) aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-202">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="f0ead-203">Die vollständige Datei *HelloWorldController.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f0ead-203">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="f0ead-204">Das Wörterbuchobjekt `ViewData` enthält Daten, die an die Ansicht übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-204">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="f0ead-205">Erstellen Sie die Ansichtsvorlage für die Begrüßung namens *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-205">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="f0ead-206">Erstellen Sie eine Schleife in der Ansichtsvorlage *Welcome.cshtml* , die „Hello“ `NumTimes` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-206">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="f0ead-207">Ersetzen Sie den Inhalt von *Views/HelloWorld/Welcome.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f0ead-207">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="f0ead-208">Speichern Sie die Änderungen, und navigieren Sie zur folgenden URL:</span><span class="sxs-lookup"><span data-stu-id="f0ead-208">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="f0ead-209">Daten werden der URL entnommen und mithilfe der [MVC-Modellbindung](xref:mvc/models/model-binding) an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-209">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="f0ead-210">Der Controller packt die Daten in einem `ViewData`-Wörterbuch und übergibt das Objekt an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="f0ead-210">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="f0ead-211">Die Ansicht rendert dann die Daten im HTML-Format im Browser.</span><span class="sxs-lookup"><span data-stu-id="f0ead-211">The view then renders the data as HTML to the browser.</span></span>

![Die Ansicht „Privacy“ mit der Beschriftung „Welcome“ und der viermal gezeigten Wortfolge „Hello Rick“](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="f0ead-213">Im obigen Beispiel wurde das Wörterbuch `ViewData` verwendet, um Daten vom Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-213">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="f0ead-214">Später in diesem Tutorial wird ein Ansichtsmodell verwendet, um Daten von einem Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-214">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="f0ead-215">Der Ansatz mit dem Ansichtsmodell für das Übergeben von Daten ist im Allgemeinen dem Ansatz mit dem Wörterbuch `ViewData` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="f0ead-215">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="f0ead-216">Weitere Informationen finden Sie im Artikel [When to use ViewBag, ViewData, or TempData (Verwendung von ViewBag, ViewData oder TempData)](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="f0ead-216">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="f0ead-217">Im nächsten Tutorial wird eine Filmdatenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-217">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f0ead-218">[Zurück](adding-controller.md)
> [Weiter](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="f0ead-218">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f0ead-219">In diesem Abschnitt ändern Sie die `HelloWorldController`-Klasse so, dass [Razor](xref:mvc/views/razor)-Ansichtsdateien verwendet werden, um den Prozess des Generierens von HTML-Antworten für einen Client sauber zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="f0ead-219">In this section you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="f0ead-220">Sie erstellen mithilfe von Razor eine Ansichtsvorlagendatei.</span><span class="sxs-lookup"><span data-stu-id="f0ead-220">You create a view template file using Razor.</span></span> <span data-ttu-id="f0ead-221">Razor-basierte Ansichtsvorlagen haben die Dateinamenerweiterung *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-221">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="f0ead-222">Sie bieten eine elegante Möglichkeit zum Erstellen einer HTML-Ausgabe mit C#.</span><span class="sxs-lookup"><span data-stu-id="f0ead-222">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="f0ead-223">Derzeit gibt die `Index`-Methode eine Zeichenfolge mit der Meldung zurück, die in der Controllerklasse hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="f0ead-223">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="f0ead-224">Ersetzen Sie in der `HelloWorldController`-Klasse die `Index`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="f0ead-224">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="f0ead-225">Der vorangehende Code ruft die Methode<xref:Microsoft.AspNetCore.Mvc.Controller.View*> des Controllers auf.</span><span class="sxs-lookup"><span data-stu-id="f0ead-225">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="f0ead-226">Er verwendet eine Ansichtsvorlage zum Generieren einer HTML-Antwort.</span><span class="sxs-lookup"><span data-stu-id="f0ead-226">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="f0ead-227">Controllermethoden (auch *Aktionsmethoden* genannt), wie z. B. die `Index`-Methode oben, geben in der Regel ein <xref:Microsoft.AspNetCore.Mvc.IActionResult> (oder eine von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleitete Klasse) und keinen Typ wie `string` zurück.</span><span class="sxs-lookup"><span data-stu-id="f0ead-227">Controller methods (also known as *action methods* ), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="f0ead-228">Hinzufügen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="f0ead-228">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f0ead-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f0ead-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f0ead-230">Klicken Sie mit der rechten Maustaste auf den Ordner *Views* , und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-230">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="f0ead-231">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld* , und klicken Sie dann **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-231">Right click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="f0ead-232">Im Dialogfeld **Neues Element hinzufügen: MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="f0ead-232">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="f0ead-233">Geben Sie in das Suchfeld rechts oben *Ansicht* ein.</span><span class="sxs-lookup"><span data-stu-id="f0ead-233">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="f0ead-234">Klicken Sie auf **Razor-Ansicht**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-234">Select **Razor View**</span></span>

  * <span data-ttu-id="f0ead-235">Übernehmen Sie den im Feld **Name** stehenden Wert *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-235">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="f0ead-236">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-236">Select **Add**</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="f0ead-238">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="f0ead-238">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="f0ead-239">Fügen Sie die Ansicht `Index` für `HelloWorldController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-239">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="f0ead-240">Fügen Sie einen neuen Ordner namens *Views/HelloWorld* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-240">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="f0ead-241">Fügen Sie dem Ordner *Views/HelloWorld* eine neue Datei namens *Index.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-241">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f0ead-242">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="f0ead-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="f0ead-243">Klicken Sie mit der rechten Maustaste auf den Ordner *Views* , und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-243">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="f0ead-244">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld* , und klicken Sie dann **Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="f0ead-244">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="f0ead-245">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="f0ead-245">In the **New File** dialog:</span></span>

  * <span data-ttu-id="f0ead-246">Wählen Sie im linken Bereich **Web** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-246">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="f0ead-247">Wählen Sie im mittleren Bereich **Leere HTML-Datei** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-247">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="f0ead-248">Geben Sie *Index.cshtml* in das Feld **Name** ein.</span><span class="sxs-lookup"><span data-stu-id="f0ead-248">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="f0ead-249">Wählen Sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-249">Select **New**.</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="f0ead-251">Ersetzen Sie den Inhalt der Razor-Ansichtsdatei *Views/HelloWorld/Index.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f0ead-251">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="f0ead-252">Navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-252">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="f0ead-253">Die `Index`-Methode im `HelloWorldController` hatte nicht viel zu tun. Sie diente zum Ausführen der Anweisung `return View();`, die angab, dass die Methode eine Ansichtsvorlagendatei zum Rendern einer Antwort im Browser verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="f0ead-253">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="f0ead-254">Da kein Name für eine Ansichtsvorlagendatei angegeben wurde, verwendet MVC standardmäßig die Standardansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="f0ead-254">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="f0ead-255">Die Standardansichtsdatei hat den gleichen Namen wie die Methode (`Index`), sodass er in */Views/HelloWorld/Index.cshtml* verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-255">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="f0ead-256">Die folgende Abbildung zeigt die Zeichenfolge "Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="f0ead-256">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="f0ead-257">die in der Ansicht hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="f0ead-257">hard-coded in the view.</span></span>

![Browserfenster](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="f0ead-259">Ändern von Ansichten und Layoutseiten</span><span class="sxs-lookup"><span data-stu-id="f0ead-259">Change views and layout pages</span></span>

<span data-ttu-id="f0ead-260">Wählen Sie die Menülinks aus ( **MvcMovie** , **Home** , **Privacy** ).</span><span class="sxs-lookup"><span data-stu-id="f0ead-260">Select the menu links ( **MvcMovie** , **Home** , and **Privacy** ).</span></span> <span data-ttu-id="f0ead-261">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-261">Each page shows the same menu layout.</span></span> <span data-ttu-id="f0ead-262">Das Menülayout wird mithilfe der Datei *Views/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-262">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="f0ead-263">Öffnen Sie die Datei *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-263">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="f0ead-264">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-264">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="f0ead-265">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-265">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="f0ead-266">`RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-266">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="f0ead-267">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Views/Home/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-267">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="f0ead-268">Ändern des Titels, der Fußzeile und Menülinks in der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="f0ead-268">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="f0ead-269">Ändern Sie in den Elementen „Titel“ und „Fußzeile“ `MvcMovie` in `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-269">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="f0ead-270">Ändern Sie das Ankerlement `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-270">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="f0ead-271">Im folgenden Markup sind die Änderungen hervorgehoben dargestellt:</span><span class="sxs-lookup"><span data-stu-id="f0ead-271">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="f0ead-272">Im obigen Markup wurde das [Anchor-Tag-Hilfsprogramm-Attribut](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) von `asp-area` ausgelassen, da für diese App keine [Bereiche](xref:mvc/controllers/areas) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-272">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="f0ead-273">**Hinweis:** Der `Movies`-Controller wurde nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-273">**Note** : The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="f0ead-274">An diesem Punkt ist der `Movie App`-Link nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="f0ead-274">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="f0ead-275">Speichern Sie Ihre Änderungen, und wählen den **Privacy** -Link aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-275">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="f0ead-276">Wie Sie sehen, wird auf der Browserregisterkarte der Titel **Privacy Policy - Movie App** anstelle des Titels **Privacy Policy - Mvc Movie** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="f0ead-276">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie** :</span></span>

![Registerkarte „Privacy“](~/tutorials/first-mvc-app/adding-view/_static/about2.png)

<span data-ttu-id="f0ead-278">Wählen Sie den Link **Home** aus, und beachten Sie, dass im Text für den Titel und den Anker ebenfalls **Movie App** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-278">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="f0ead-279">Wir haben also eine Änderung des Texts und Titels in der Layoutvorlage einmalig vorgenommen, die von der gesamten Website übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-279">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="f0ead-280">Untersuchen Sie die Datei *Views/_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f0ead-280">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="f0ead-281">Die Datei *Views/_ViewStart.cshtml* fügt jeder Ansicht die Datei *Views/Shared/_Layout.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-281">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="f0ead-282">Die `Layout`-Eigenschaft kann verwendet werden, um eine andere Layoutansicht festzulegen, oder legen Sie sie auf `null` fest, damit keine Layoutdatei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-282">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="f0ead-283">Ändern Sie den Titel und das `<h2>`-Element der Ansichtsdatei *Views/HelloWorld/Index.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="f0ead-283">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="f0ead-284">Der Titel und das `<h2>`-Element sind geringfügig anders, sodass Sie sehen können, welcher Codeabschnitt die Anzeige ändert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-284">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="f0ead-285">`ViewData["Title"] = "Movie List";` im Code oben legt die `Title`-Eigenschaft des Wörterbuchs `ViewData`auf „Movie List“ fest.</span><span class="sxs-lookup"><span data-stu-id="f0ead-285">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="f0ead-286">Die `Title`-Eigenschaft wird im HTML-Element `<title>` der Layoutseite verwendet:</span><span class="sxs-lookup"><span data-stu-id="f0ead-286">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="f0ead-287">Speichern Sie die Änderung, und navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="f0ead-287">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="f0ead-288">Sie sehen, dass sich der Browsertitel, die primäre Überschrift und die sekundären Überschriften geändert haben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-288">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="f0ead-289">(Wenn die Änderungen nicht im Browser angezeigt werden, zeigen Sie ggf. zwischengespeicherten Inhalt an.</span><span class="sxs-lookup"><span data-stu-id="f0ead-289">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="f0ead-290">Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.) Der Titel des Browsers wird mithilfe des Elements `ViewData["Title"]` erstellt, das wir in der Ansichtsvorlange *Index.cshtml* festgelegt haben, und des zusätzlichen Elements „- Movie App“, das in der Layoutdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f0ead-290">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="f0ead-291">Beachten Sie außerdem, wie der Inhalt der Ansichtsvorlage *Index.cshtml* mit der Ansichtsvorlage *Views/Shared/_Layout.cshtml* zusammengeführt und eine einzelne HTML-Antwort an den Browser gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="f0ead-291">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="f0ead-292">Layoutvorlagen erleichtern ungemein das Vornehmen von Änderungen an allen Seiten in Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f0ead-292">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="f0ead-293">Weitere Informationen dazu finden Sie unter [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="f0ead-293">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Ansicht mit Filmliste](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="f0ead-295">Unser kleiner Beitrag zu den „Daten“ (in diesem Fall die Meldung "Hello from our View Template!")</span><span class="sxs-lookup"><span data-stu-id="f0ead-295">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="f0ead-296">ist jedoch hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="f0ead-296">message) is hard-coded, though.</span></span> <span data-ttu-id="f0ead-297">Die MVC-Anwendung weist ein „V“ (View [Ansicht]) auf, und Sie verfügen über ein „C“ (Controller), aber noch nicht über ein „M“ (Modell).</span><span class="sxs-lookup"><span data-stu-id="f0ead-297">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="f0ead-298">Übergeben von Daten vom Controller an die Ansicht</span><span class="sxs-lookup"><span data-stu-id="f0ead-298">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="f0ead-299">Controlleraktionen werden als Antwort auf eine eingehende URL-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="f0ead-299">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="f0ead-300">Eine Controllerklasse ist der Ort, an dem der Code geschrieben wird, der die eingehenden Browseranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f0ead-300">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="f0ead-301">Der Controller ruft Daten aus einer Datenquelle ab und entscheidet, welche Art von Antwort an den Browser zurückgesendet wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-301">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="f0ead-302">Ansichtsvorlagen können von einem Controller zum Generieren und Formatieren einer HTML-Antwort an den Browser verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-302">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="f0ead-303">Controller sind für die Bereitstellung der benötigten Daten zuständig, damit eine Ansichtsvorlage eine Antwort liefern kann.</span><span class="sxs-lookup"><span data-stu-id="f0ead-303">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="f0ead-304">Eine bewährte Methode: Ansichtsvorlagen sollten **keine** Geschäftslogik ausführen bzw. nicht direkt mit einer Datenbank interagieren.</span><span class="sxs-lookup"><span data-stu-id="f0ead-304">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="f0ead-305">Eine Ansichtsvorlage sollte stattdessen nur mit den Daten arbeiten, die ihr vom Controller bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-305">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="f0ead-306">Durch Beibehaltung dieser Bereichstrennung bleibt der Code übersichtlich, testfähig und verwaltbar.</span><span class="sxs-lookup"><span data-stu-id="f0ead-306">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="f0ead-307">Derzeit verwendet die `Welcome`-Methode in der `HelloWorldController`-Klasse die Parameter `name` und `ID` und gibt anschließend die Werte direkt an den Browser aus.</span><span class="sxs-lookup"><span data-stu-id="f0ead-307">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="f0ead-308">Anstatt den Controller diese Antwort als Zeichenfolge rendern zu lassen, passen Sie den Controller so an, dass er eine Ansichtsvorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="f0ead-308">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="f0ead-309">Die Ansichtsvorlage erstellt eine dynamische Antwort, was bedeutet, dass entsprechende Datenbestandteile vom Controller an die Ansicht übergeben werden müssen, damit eine Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="f0ead-309">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="f0ead-310">Lassen Sie hierzu den Controller die dynamischen Daten (Parameter), die die Ansichtsvorlage benötigt, in einem `ViewData`-Wörterbuch ablegen, auf das die Ansichtsvorlage zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="f0ead-310">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="f0ead-311">Ändern Sie in *HelloWorldController.cs* die `Welcome`-Methode so, dass die Werte `Message` und `NumTimes` dem Wörterbuch `ViewData` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-311">In *HelloWorldController.cs* , change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="f0ead-312">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, was bedeutet, dass jeder beliebige Typ verwendet werden kann. Das `ViewData`-Objekt hat erst dann definierte Eigenschaften, wenn Sie ihm etwas hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-312">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="f0ead-313">Das [MVC-Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter (`name` und `numTimes`) aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="f0ead-313">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="f0ead-314">Die vollständige Datei *HelloWorldController.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="f0ead-314">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="f0ead-315">Das Wörterbuchobjekt `ViewData` enthält Daten, die an die Ansicht übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="f0ead-315">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="f0ead-316">Erstellen Sie die Ansichtsvorlage für die Begrüßung namens *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="f0ead-316">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="f0ead-317">Erstellen Sie eine Schleife in der Ansichtsvorlage *Welcome.cshtml* , die „Hello“ `NumTimes` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-317">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="f0ead-318">Ersetzen Sie den Inhalt von *Views/HelloWorld/Welcome.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f0ead-318">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="f0ead-319">Speichern Sie die Änderungen, und navigieren Sie zur folgenden URL:</span><span class="sxs-lookup"><span data-stu-id="f0ead-319">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="f0ead-320">Daten werden der URL entnommen und mithilfe der [MVC-Modellbindung](xref:mvc/models/model-binding) an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-320">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="f0ead-321">Der Controller packt die Daten in einem `ViewData`-Wörterbuch und übergibt das Objekt an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="f0ead-321">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="f0ead-322">Die Ansicht rendert dann die Daten im HTML-Format im Browser.</span><span class="sxs-lookup"><span data-stu-id="f0ead-322">The view then renders the data as HTML to the browser.</span></span>

![Die Ansicht „Privacy“ mit der Beschriftung „Welcome“ und der viermal gezeigten Wortfolge „Hello Rick“](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="f0ead-324">Im obigen Beispiel wurde das Wörterbuch `ViewData` verwendet, um Daten vom Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-324">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="f0ead-325">Später in diesem Tutorial wird ein Ansichtsmodell verwendet, um Daten von einem Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f0ead-325">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="f0ead-326">Der Ansatz mit dem Ansichtsmodell für das Übergeben von Daten ist im Allgemeinen dem Ansatz mit dem Wörterbuch `ViewData` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="f0ead-326">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="f0ead-327">Weitere Informationen finden Sie im Artikel [When to use ViewBag, ViewData, or TempData (Verwendung von ViewBag, ViewData oder TempData)](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="f0ead-327">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="f0ead-328">Im nächsten Tutorial wird eine Filmdatenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="f0ead-328">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="f0ead-329">[Zurück](adding-controller.md)
> [Weiter](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="f0ead-329">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
