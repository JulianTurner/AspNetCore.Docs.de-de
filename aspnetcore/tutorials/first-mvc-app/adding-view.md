---
title: 'Teil 3: Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App'
author: rick-anderson
description: Dies ist Teil 3 der Tutorialreihe zu ASP.NET Core MVC.
ms.author: riande
ms.date: 01/28/2021
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
uid: tutorials/first-mvc-app/adding-view
ms.custom: contperf-fy21q3
ms.openlocfilehash: 1542e44fcc6d0ae22fb1a759ea3a3ed1d866cbc7
ms.sourcegitcommit: 19a004ff2be73876a9ef0f1ac44d0331849ad159
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804568"
---
# <a name="part-3-add-a-view-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d723a-103">Teil 3: Hinzufügen einer Ansicht zu einer ASP.NET Core MVC-App</span><span class="sxs-lookup"><span data-stu-id="d723a-103">Part 3, add a view to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d723a-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d723a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d723a-105">In diesem Abschnitt ändern Sie die Klasse `HelloWorldController`, damit [Razor](xref:mvc/views/razor)-Ansichtsdateien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-105">In this section, you modify the `HelloWorldController` class to use [Razor](xref:mvc/views/razor) view files.</span></span> <span data-ttu-id="d723a-106">Dadurch wird die Generierung von HTML-Antworten an einen Client vollständig gekapselt.</span><span class="sxs-lookup"><span data-stu-id="d723a-106">This cleanly encapsulates the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d723a-107">Ansichtsvorlagen werden mithilfe von Razor erstellt.</span><span class="sxs-lookup"><span data-stu-id="d723a-107">View templates are created using Razor.</span></span> <span data-ttu-id="d723a-108">Razor-basierte Ansichtsvorlagen...</span><span class="sxs-lookup"><span data-stu-id="d723a-108">Razor-based view templates:</span></span>

* <span data-ttu-id="d723a-109">weisen die Erweiterung *.cshtml* auf.</span><span class="sxs-lookup"><span data-stu-id="d723a-109">Have a *.cshtml* file extension.</span></span>
* <span data-ttu-id="d723a-110">sind eine gute Möglichkeit, HTML-Ausgaben mit C# zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d723a-110">Provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d723a-111">Derzeit gibt die Methode `Index` eine Zeichenfolge mit einer Meldung in der Controllerklasse zurück.</span><span class="sxs-lookup"><span data-stu-id="d723a-111">Currently the `Index` method returns a string with a message in the controller class.</span></span> <span data-ttu-id="d723a-112">Ersetzen Sie in der `HelloWorldController`-Klasse die `Index`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d723a-112">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d723a-113">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d723a-113">The preceding code:</span></span>

* <span data-ttu-id="d723a-114">ruft die Methode <xref:Microsoft.AspNetCore.Mvc.Controller.View*> des Controllers auf.</span><span class="sxs-lookup"><span data-stu-id="d723a-114">Calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span>
* <span data-ttu-id="d723a-115">verwendet eine Ansichtsvorlage, um eine HTML-Antwort zu generieren.</span><span class="sxs-lookup"><span data-stu-id="d723a-115">Uses a view template to generate an HTML response.</span></span>

<span data-ttu-id="d723a-116">Controllermethoden...</span><span class="sxs-lookup"><span data-stu-id="d723a-116">Controller methods:</span></span>

* <span data-ttu-id="d723a-117">werden als *Aktionsmethoden* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="d723a-117">Are referred to as *action methods*.</span></span>  <span data-ttu-id="d723a-118">Ein Beispiel hierfür ist die Aktionsmethode `Index` im vorangehenden Code.</span><span class="sxs-lookup"><span data-stu-id="d723a-118">For example, the `Index` action method in the preceding code.</span></span>
* <span data-ttu-id="d723a-119">geben in der Regel <xref:Microsoft.AspNetCore.Mvc.IActionResult> oder eine von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleitete Klasse zurück, keinen Typ wie `string`.</span><span class="sxs-lookup"><span data-stu-id="d723a-119">Generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>, not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d723a-120">Hinzufügen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="d723a-120">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d723a-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d723a-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d723a-122">Klicken Sie mit der rechten Maustaste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d723a-122">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="d723a-123">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="d723a-123">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

<span data-ttu-id="d723a-124">Im Dialogfeld **Neues Element hinzufügen: MvcMovie**:</span><span class="sxs-lookup"><span data-stu-id="d723a-124">In the **Add New Item - MvcMovie** dialog:</span></span>

* <span data-ttu-id="d723a-125">Geben Sie in das Suchfeld rechts oben *Ansicht* ein.</span><span class="sxs-lookup"><span data-stu-id="d723a-125">In the search box in the upper-right, enter *view*</span></span>
* <span data-ttu-id="d723a-126">Klicken Sie auf **Razor-Ansicht**.</span><span class="sxs-lookup"><span data-stu-id="d723a-126">Select **Razor View**</span></span>
* <span data-ttu-id="d723a-127">Übernehmen Sie den im Feld **Name** stehenden Wert *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-127">Keep the **Name** box value, *Index.cshtml*.</span></span>
* <span data-ttu-id="d723a-128">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-128">Select **Add**</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view50.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d723a-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d723a-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d723a-131">Fügen Sie die Ansicht `Index` für `HelloWorldController` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d723a-131">Add an `Index` view for the `HelloWorldController`:</span></span>

* <span data-ttu-id="d723a-132">Fügen Sie einen neuen Ordner namens *Views/HelloWorld* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-132">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d723a-133">Fügen Sie dem Ordner *Views/HelloWorld* eine neue Datei namens *Index.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-133">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d723a-134">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d723a-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d723a-135">Klicken Sie bei gedrückter STRG-Taste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d723a-135">Control-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

<span data-ttu-id="d723a-136">Klicken Sie bei gedrückter STRG-Taste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="d723a-136">Control-click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>

<span data-ttu-id="d723a-137">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="d723a-137">In the **New File** dialog:</span></span>

* <span data-ttu-id="d723a-138">Wählen Sie im linken Bereich die Option **ASP.NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-138">Select **ASP.NET Core** in the left pane.</span></span>
* <span data-ttu-id="d723a-139">Klicken Sie im mittleren Bereich auf **Razor-Ansicht: Leer**.</span><span class="sxs-lookup"><span data-stu-id="d723a-139">Select **Razor View - Empty** in the center pane.</span></span>
* <span data-ttu-id="d723a-140">Geben Sie in das Feld **Name** die Zeichenfolge *Index* ein.</span><span class="sxs-lookup"><span data-stu-id="d723a-140">Type *Index* in the **Name** box.</span></span>
* <span data-ttu-id="d723a-141">Wählen Sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-141">Select **New**.</span></span>

  ![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view_macVSM8.9.png)

---

<span data-ttu-id="d723a-143">Ersetzen Sie den Inhalt der Razor-Ansichtsdatei *Views/HelloWorld/Index.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d723a-143">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d723a-144">Navigieren Sie zu `https://localhost:{PORT}/HelloWorld`:</span><span class="sxs-lookup"><span data-stu-id="d723a-144">Navigate to `https://localhost:{PORT}/HelloWorld`:</span></span>

* <span data-ttu-id="d723a-145">Die `Index`-Methode in `HelloWorldController` hat die Anweisung `return View();` ausgeführt, die angab, dass die Methode eine Ansichtsvorlagendatei zum Rendern einer Antwort an den Browser verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="d723a-145">The `Index` method in the `HelloWorldController` ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span>
* <span data-ttu-id="d723a-146">Da kein Name für eine Ansichtsvorlagendatei angegeben wurde, verwendet MVC die Standardansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="d723a-146">A view template file name wasn't specified, so MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d723a-147">Wenn kein Name für die Ansichtsdatei angegeben wird, wird die Standardansicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-147">When the view file name isn't specified, the default view is returned.</span></span> <span data-ttu-id="d723a-148">Die Standardansicht trägt den gleichen Namen wie die Aktionsmethode (in diesem Beispiel `Index`).</span><span class="sxs-lookup"><span data-stu-id="d723a-148">The default view has the same name as the action method, `Index` in this example.</span></span> <span data-ttu-id="d723a-149">Die Ansichtsvorlage */Views/HelloWorld/Index.cshtml* wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="d723a-149">The view template */Views/HelloWorld/Index.cshtml* is used.</span></span>
* <span data-ttu-id="d723a-150">Die folgende Abbildung zeigt die Zeichenfolge „Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="d723a-150">The following image shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d723a-151">die in der Ansicht hartcodiert ist:</span><span class="sxs-lookup"><span data-stu-id="d723a-151">hard-coded in the view:</span></span>

  ![Browserfenster](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d723a-153">Ändern von Ansichten und Layoutseiten</span><span class="sxs-lookup"><span data-stu-id="d723a-153">Change views and layout pages</span></span>

<span data-ttu-id="d723a-154">Wählen Sie die Menülinks **MvcMovie**, **Home** und **Privacy** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-154">Select the menu links **MvcMovie**, **Home**, and **Privacy**.</span></span> <span data-ttu-id="d723a-155">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-155">Each page shows the same menu layout.</span></span> <span data-ttu-id="d723a-156">Das Menülayout wird mithilfe der Datei *Views/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-156">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d723a-157">Öffnen Sie die Datei *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-157">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d723a-158">Mit [Layoutvorlagen](xref:mvc/views/layout) ist Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="d723a-158">[Layout](xref:mvc/views/layout) templates allows:</span></span>

* <span data-ttu-id="d723a-159">Angeben des HTML-Containerlayouts einer Website in einer Datei</span><span class="sxs-lookup"><span data-stu-id="d723a-159">Specifying the HTML container layout of a site in one place.</span></span>
* <span data-ttu-id="d723a-160">Anwenden des HTML-Containerlayouts auf mehrere Seiten der Website</span><span class="sxs-lookup"><span data-stu-id="d723a-160">Applying the HTML container layout across multiple pages in the site.</span></span>

<span data-ttu-id="d723a-161">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="d723a-161">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d723a-162">`RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-162">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d723a-163">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Views/Home/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="d723a-163">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d723a-164">Ändern des Titels, der Fußzeile und Menülinks in der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="d723a-164">Change the title, footer, and menu link in the layout file</span></span>

<span data-ttu-id="d723a-165">Ersetzen Sie den Inhalt der Datei *Views/Shared/_Layout.cshtml* durch das folgende Markup.</span><span class="sxs-lookup"><span data-stu-id="d723a-165">Replace the content of the *Views/Shared/_Layout.cshtml* file with the following markup.</span></span> <span data-ttu-id="d723a-166">Die Änderungen werden hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="d723a-166">The changes are highlighted:</span></span>
::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie5/Views/Shared/_Layout.cshtml?highlight=6,14,40)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d723a-167">Im oben stehenden Markup wurden folgende Änderungen vorgenommen:</span><span class="sxs-lookup"><span data-stu-id="d723a-167">The preceding markup made the following changes:</span></span>

* <span data-ttu-id="d723a-168">`MvcMovie` wurde dreimal in `Movie App` geändert.</span><span class="sxs-lookup"><span data-stu-id="d723a-168">Three occurrences of `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d723a-169">Das Anchor-Element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` wurde in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>` geändert.</span><span class="sxs-lookup"><span data-stu-id="d723a-169">The anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d723a-170">Im obigen Markup wurden in `asp-area=""` das [Ankerattribut des Taghilfsprogramms](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) und der Attributwert ausgelassen, da für diese App keine [Bereiche](xref:mvc/controllers/areas) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-170">In the preceding markup, the `asp-area=""` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) and attribute value was omitted because this app isn't using [Areas](xref:mvc/controllers/areas).</span></span>

<span data-ttu-id="d723a-171">**Hinweis:** Der Controller `Movies` wurde nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-171">**Note**: The `Movies` controller hasn't been implemented.</span></span> <span data-ttu-id="d723a-172">An diesem Punkt ist der Link `Movie App` nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="d723a-172">At this point, the `Movie App` link isn't functional.</span></span>

<span data-ttu-id="d723a-173">Speichern Sie Ihre Änderungen, und wählen den Link **Privacy** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-173">Save the changes and select the **Privacy** link.</span></span> <span data-ttu-id="d723a-174">Wie Sie sehen, wird auf der Browserregisterkarte der Titel **Privacy Policy - Movie App** anstelle des Titels **Privacy Policy - Mvc Movie** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d723a-174">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Registerkarte „Privacy“](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="d723a-176">Klicken Sie auf den Link **Home** (Startseite).</span><span class="sxs-lookup"><span data-stu-id="d723a-176">Select the **Home** link.</span></span>

<span data-ttu-id="d723a-177">Sie sehen, dass der Titel und der Ankertext **Movie App** anzeigen.</span><span class="sxs-lookup"><span data-stu-id="d723a-177">Notice that the title and anchor text display **Movie App**.</span></span> <span data-ttu-id="d723a-178">Die Änderungen wurden in der Layoutvorlage einmalig vorgenommen, aber der neue Linktext und der neue Titel werden auf allen Seiten der Website angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-178">The changes were made once in the layout template and all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d723a-179">Untersuchen Sie die Datei *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d723a-179">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d723a-180">Die Datei *Views/_ViewStart.cshtml* fügt jeder Ansicht die Datei *Views/Shared/_Layout.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-180">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d723a-181">Die `Layout`-Eigenschaft kann verwendet werden, um eine andere Layoutansicht festzulegen, oder legen Sie sie auf `null` fest, damit keine Layoutdatei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-181">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d723a-182">Öffnen Sie die Ansichtsdatei *Views/HelloWorld/Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-182">Open the *Views/HelloWorld/Index.cshtml* view file.</span></span>

<span data-ttu-id="d723a-183">Ändern Sie den Titel und das Element `<h2>` wie im folgenden Codeausschnitt hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="d723a-183">Change the title and `<h2>` element as highlighted in the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d723a-184">Der Titel und das Element `<h2>` unterscheiden sich, damit eindeutig ist, welcher Codebestandteil sich auf die Anzeige auswirkt.</span><span class="sxs-lookup"><span data-stu-id="d723a-184">The title and `<h2>` element are slightly different so it's clear which part of the code changes the display.</span></span>

<span data-ttu-id="d723a-185">`ViewData["Title"] = "Movie List";` im Code oben legt die `Title`-Eigenschaft des Wörterbuchs `ViewData`auf „Movie List“ fest.</span><span class="sxs-lookup"><span data-stu-id="d723a-185">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d723a-186">Die `Title`-Eigenschaft wird im HTML-Element `<title>` der Layoutseite verwendet:</span><span class="sxs-lookup"><span data-stu-id="d723a-186">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="d723a-187">Speichern Sie die Änderung, und navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="d723a-187">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span>

<span data-ttu-id="d723a-188">Die folgenden Elemente wurden geändert:</span><span class="sxs-lookup"><span data-stu-id="d723a-188">Notice that the following have changed:</span></span>

* <span data-ttu-id="d723a-189">Browsertitel</span><span class="sxs-lookup"><span data-stu-id="d723a-189">Browser title.</span></span>
* <span data-ttu-id="d723a-190">Primäre Überschrift</span><span class="sxs-lookup"><span data-stu-id="d723a-190">Primary heading.</span></span>
* <span data-ttu-id="d723a-191">Sekundäre Überschriften</span><span class="sxs-lookup"><span data-stu-id="d723a-191">Secondary headings.</span></span>

<span data-ttu-id="d723a-192">Wenn die Änderungen im Browser nicht angezeigt werden, wird möglicherweise zwischengespeicherter Inhalt angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-192">If there are no changes in the browser, it could be cached content that is being viewed.</span></span> <span data-ttu-id="d723a-193">Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="d723a-193">Press Ctrl+F5 in the browser to force the response from the server to be loaded.</span></span> <span data-ttu-id="d723a-194">Der Titel des Browsers wird mithilfe des Elements `ViewData["Title"]` erstellt, das wir in der Ansichtsvorlange *Index.cshtml* festgelegt haben, und des zusätzlichen Elements „- Movie App“, das in der Layoutdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="d723a-194">The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d723a-195">Der Inhalt der Ansichtsvorlage *Index.cshtml* wird mit dem der Ansichtsvorlage *Views/Shared/_Layout.cshtml* zusammengeführt.</span><span class="sxs-lookup"><span data-stu-id="d723a-195">The content in the *Index.cshtml* view template is merged with the *Views/Shared/_Layout.cshtml* view template.</span></span> <span data-ttu-id="d723a-196">Eine einzelne HTML-Antwort wird an den Browser gesendet.</span><span class="sxs-lookup"><span data-stu-id="d723a-196">A single HTML response is sent to the browser.</span></span> <span data-ttu-id="d723a-197">Layoutvorlagen erleichtern es Ihnen, Änderungen an allen Seiten einer App vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="d723a-197">Layout templates make it easy to make changes that apply across all of the pages in an app.</span></span> <span data-ttu-id="d723a-198">Weitere Informationen finden Sie unter [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d723a-198">To learn more, see [Layout](xref:mvc/views/layout).</span></span>

![Ansicht mit Filmliste](~/tutorials/first-mvc-app/adding-view/_static/hell50.png)

<span data-ttu-id="d723a-200">Die Daten, also die Meldung „Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="d723a-200">The small bit of "data", the "Hello from our View Template!"</span></span> <span data-ttu-id="d723a-201">sind jedoch hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-201">message, is hard-coded however.</span></span> <span data-ttu-id="d723a-202">Die MVC-Anwendung besitzt bereits eine Ansicht (V, View) und einen Controller (C), aber noch kein Modell (M).</span><span class="sxs-lookup"><span data-stu-id="d723a-202">The MVC application has a "V" (view), a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d723a-203">Übergeben von Daten vom Controller an die Ansicht</span><span class="sxs-lookup"><span data-stu-id="d723a-203">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d723a-204">Controlleraktionen werden als Antwort auf eine eingehende URL-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d723a-204">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d723a-205">Eine Controllerklasse ist der Ort, an dem der Code geschrieben wird, der die eingehenden Browseranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d723a-205">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d723a-206">Der Controller ruft Daten aus einer Datenquelle ab und entscheidet, welche Art von Antwort an den Browser zurückgesendet wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-206">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d723a-207">Ansichtsvorlagen können von einem Controller zum Generieren und Formatieren einer HTML-Antwort an den Browser verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-207">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d723a-208">Controller sind für die Bereitstellung der benötigten Daten zuständig, damit eine Ansichtsvorlage eine Antwort liefern kann.</span><span class="sxs-lookup"><span data-stu-id="d723a-208">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span>

<span data-ttu-id="d723a-209">Für Folgendes sind Ansichtsvorlagen **nicht** vorgesehen:</span><span class="sxs-lookup"><span data-stu-id="d723a-209">View templates should **not**:</span></span>

* <span data-ttu-id="d723a-210">Geschäftslogik</span><span class="sxs-lookup"><span data-stu-id="d723a-210">Do business logic</span></span>
* <span data-ttu-id="d723a-211">Direkte Interaktion mit einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="d723a-211">Interact with a database directly.</span></span>

<span data-ttu-id="d723a-212">Eine Ansichtsvorlage sollte nur mit den Daten arbeiten, die ihr vom Controller bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-212">A view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d723a-213">Durch diese Aufgabenteilung (Separation of Concerns) bleibt der Code:</span><span class="sxs-lookup"><span data-stu-id="d723a-213">Maintaining this "separation of concerns" helps keep the code:</span></span>

* <span data-ttu-id="d723a-214">Übersichtlich</span><span class="sxs-lookup"><span data-stu-id="d723a-214">Clean.</span></span>
* <span data-ttu-id="d723a-215">Testbar</span><span class="sxs-lookup"><span data-stu-id="d723a-215">Testable.</span></span>
* <span data-ttu-id="d723a-216">Verwaltbar</span><span class="sxs-lookup"><span data-stu-id="d723a-216">Maintainable.</span></span>

<span data-ttu-id="d723a-217">Derzeit verwendet die `Welcome`-Methode in der `HelloWorldController`-Klasse die Parameter `name` und `ID` und gibt anschließend die Werte direkt an den Browser aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-217">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span>

<span data-ttu-id="d723a-218">Anstatt den Controller diese Antwort als Zeichenfolge rendern zu lassen, passen Sie den Controller so an, dass er eine Ansichtsvorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="d723a-218">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d723a-219">Die Ansichtsvorlage generiert eine dynamische Antwort. Das bedeutet, dass entsprechende Daten vom Controller an die Ansicht übergeben werden müssen, damit eine Antwort generiert wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-219">The view template generates a dynamic response, which means that appropriate data must be passed from the controller to the view to generate the response.</span></span> <span data-ttu-id="d723a-220">Weisen Sie hierzu den Controller an, die dynamischen Daten (Parameter), die die Ansichtsvorlage benötigt, in einem `ViewData`-Wörterbuch abzulegen.</span><span class="sxs-lookup"><span data-stu-id="d723a-220">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary.</span></span> <span data-ttu-id="d723a-221">Die Ansichtsvorlage kann dann auf die dynamischen Daten zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d723a-221">The view template can then access the dynamic data.</span></span>

<span data-ttu-id="d723a-222">Ändern Sie in *HelloWorldController.cs* die `Welcome`-Methode so, dass die Werte `Message` und `NumTimes` dem Wörterbuch `ViewData` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-222">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span>

<span data-ttu-id="d723a-223">Das `ViewData`-Wörterbuch ist ein dynamisches Objekt. Das bedeutet, dass ein beliebiger Typ verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="d723a-223">The `ViewData` dictionary is a dynamic object, which means any type can be used.</span></span> <span data-ttu-id="d723a-224">Für das Objekt `ViewData` sind keine Eigenschaften definiert, bis etwas hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-224">The `ViewData` object has no defined properties until something is added.</span></span> <span data-ttu-id="d723a-225">Das MVC-[Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter `name` und `numTimes` aus der Abfragezeichenfolge den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="d723a-225">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters `name` and `numTimes` from the query string to parameters in the method.</span></span> <span data-ttu-id="d723a-226">Die vollständige Klasse `HelloWorldController`:</span><span class="sxs-lookup"><span data-stu-id="d723a-226">The complete `HelloWorldController`:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5&highlight=13-19)]

<span data-ttu-id="d723a-227">Das Wörterbuchobjekt `ViewData` enthält Daten, die an die Ansicht übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-227">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d723a-228">Erstellen Sie die Ansichtsvorlage für die Begrüßung namens *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-228">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d723a-229">Erstellen Sie eine Schleife in der Ansichtsvorlage *Welcome.cshtml*, die „Hello“ `NumTimes` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-229">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d723a-230">Ersetzen Sie den Inhalt von *Views/HelloWorld/Welcome.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d723a-230">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d723a-231">Speichern Sie die Änderungen, und navigieren Sie zur folgenden URL:</span><span class="sxs-lookup"><span data-stu-id="d723a-231">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d723a-232">Daten werden der URL entnommen und mithilfe der [MVC-Modellbindung](xref:mvc/models/model-binding) an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-232">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="d723a-233">Der Controller packt die Daten in einem `ViewData`-Wörterbuch und übergibt das Objekt an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d723a-233">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d723a-234">Die Ansicht rendert dann die Daten im HTML-Format im Browser.</span><span class="sxs-lookup"><span data-stu-id="d723a-234">The view then renders the data as HTML to the browser.</span></span>

![Die Ansicht „Privacy“ mit der Beschriftung „Welcome“ und der viermal gezeigten Wortfolge „Hello Rick“](~/tutorials/first-mvc-app/adding-view/_static/rick2_50.png)

<span data-ttu-id="d723a-236">Im vorherigen Beispiel wurde das Wörterbuch `ViewData` verwendet, um Daten vom Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-236">In the preceding sample, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d723a-237">Später in diesem Tutorial wird ein Ansichtsmodell verwendet, um Daten von einem Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-237">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d723a-238">Der Ansatz mit dem Ansichtsmodell für das Übergeben von Daten ist dem Ansatz mit dem Wörterbuch `ViewData` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="d723a-238">The view model approach to passing data is preferred over the `ViewData` dictionary approach.</span></span>

<span data-ttu-id="d723a-239">Im nächsten Tutorial wird eine Filmdatenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="d723a-239">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d723a-240">[Zurück: Hinzufügen eines Controllers](adding-controller.md)
> [Weiter: Hinzufügen eines Modells](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d723a-240">[Previous: Add a Controller](adding-controller.md)
[Next: Add a Model](adding-model.md)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d723a-241">In diesem Abschnitt wird die `HelloWorldController`-Klasse so geändert, dass [Razor](xref:mvc/views/razor)-Ansichtsdateien verwendet werden, um das Generieren von HTML-Antworten an einen Client vollständig zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="d723a-241">In this section, the `HelloWorldController` class is modified to use [Razor](xref:mvc/views/razor) view files to cleanly encapsulate the process of generating HTML responses to a client.</span></span>

<span data-ttu-id="d723a-242">Sie erstellen mithilfe von Razor eine Ansichtsvorlagendatei.</span><span class="sxs-lookup"><span data-stu-id="d723a-242">You create a view template file using Razor.</span></span> <span data-ttu-id="d723a-243">Razor-basierte Ansichtsvorlagen haben die Dateinamenerweiterung *.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-243">Razor-based view templates have a *.cshtml* file extension.</span></span> <span data-ttu-id="d723a-244">Sie bieten eine elegante Möglichkeit zum Erstellen einer HTML-Ausgabe mit C#.</span><span class="sxs-lookup"><span data-stu-id="d723a-244">They provide an elegant way to create HTML output with C#.</span></span>

<span data-ttu-id="d723a-245">Derzeit gibt die `Index`-Methode eine Zeichenfolge mit der Meldung zurück, die in der Controllerklasse hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="d723a-245">Currently the `Index` method returns a string with a message that's hard-coded in the controller class.</span></span> <span data-ttu-id="d723a-246">Ersetzen Sie in der `HelloWorldController`-Klasse die `Index`-Methode durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="d723a-246">In the `HelloWorldController` class, replace the `Index` method with the following code:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_4)]

<span data-ttu-id="d723a-247">Der vorangehende Code ruft die Methode<xref:Microsoft.AspNetCore.Mvc.Controller.View*> des Controllers auf.</span><span class="sxs-lookup"><span data-stu-id="d723a-247">The preceding code calls the controller's <xref:Microsoft.AspNetCore.Mvc.Controller.View*> method.</span></span> <span data-ttu-id="d723a-248">Er verwendet eine Ansichtsvorlage zum Generieren einer HTML-Antwort.</span><span class="sxs-lookup"><span data-stu-id="d723a-248">It uses a view template to generate an HTML response.</span></span> <span data-ttu-id="d723a-249">Controllermethoden (auch *Aktionsmethoden* genannt), wie z. B. die `Index`-Methode oben, geben in der Regel ein <xref:Microsoft.AspNetCore.Mvc.IActionResult> (oder eine von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleitete Klasse) und keinen Typ wie `string` zurück.</span><span class="sxs-lookup"><span data-stu-id="d723a-249">Controller methods (also known as *action methods*), such as the `Index` method above, generally return an <xref:Microsoft.AspNetCore.Mvc.IActionResult> (or a class derived from <xref:Microsoft.AspNetCore.Mvc.ActionResult>), not a type like `string`.</span></span>

## <a name="add-a-view"></a><span data-ttu-id="d723a-250">Hinzufügen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="d723a-250">Add a view</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d723a-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d723a-251">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d723a-252">Klicken Sie mit der rechten Maustaste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d723a-252">Right-click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>

* <span data-ttu-id="d723a-253">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neues Element**.</span><span class="sxs-lookup"><span data-stu-id="d723a-253">Right-click on the *Views/HelloWorld* folder, and then **Add > New Item**.</span></span>

* <span data-ttu-id="d723a-254">Im Dialogfeld **Neues Element hinzufügen: MvcMovie**</span><span class="sxs-lookup"><span data-stu-id="d723a-254">In the **Add New Item - MvcMovie** dialog</span></span>

  * <span data-ttu-id="d723a-255">Geben Sie in das Suchfeld rechts oben *Ansicht* ein.</span><span class="sxs-lookup"><span data-stu-id="d723a-255">In the search box in the upper-right, enter *view*</span></span>

  * <span data-ttu-id="d723a-256">Klicken Sie auf **Razor-Ansicht**.</span><span class="sxs-lookup"><span data-stu-id="d723a-256">Select **Razor View**</span></span>

  * <span data-ttu-id="d723a-257">Übernehmen Sie den im Feld **Name** stehenden Wert *Index.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-257">Keep the **Name** box value, *Index.cshtml*.</span></span>

  * <span data-ttu-id="d723a-258">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-258">Select **Add**</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d723a-260">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d723a-260">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d723a-261">Fügen Sie die Ansicht `Index` für `HelloWorldController` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-261">Add an `Index` view for the `HelloWorldController`.</span></span>

* <span data-ttu-id="d723a-262">Fügen Sie einen neuen Ordner namens *Views/HelloWorld* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-262">Add a new folder named *Views/HelloWorld*.</span></span>
* <span data-ttu-id="d723a-263">Fügen Sie dem Ordner *Views/HelloWorld* eine neue Datei namens *Index.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-263">Add a new file to the *Views/HelloWorld* folder name *Index.cshtml*.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d723a-264">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="d723a-264">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d723a-265">Klicken Sie mit der rechten Maustaste auf den Ordner *Views*, und klicken Sie dann **Hinzufügen > Neuer Ordner**. Nennen Sie den Ordner *HelloWorld*.</span><span class="sxs-lookup"><span data-stu-id="d723a-265">Right click on the *Views* folder, and then **Add > New Folder** and name the folder *HelloWorld*.</span></span>
* <span data-ttu-id="d723a-266">Klicken Sie mit der rechten Maustaste auf den Ordner *Views/HelloWorld*, und klicken Sie dann **Hinzufügen > Neue Datei**.</span><span class="sxs-lookup"><span data-stu-id="d723a-266">Right click on the *Views/HelloWorld* folder, and then **Add > New File**.</span></span>
* <span data-ttu-id="d723a-267">Führen Sie im Dialogfeld **Neue Datei** folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="d723a-267">In the **New File** dialog:</span></span>

  * <span data-ttu-id="d723a-268">Wählen Sie im linken Bereich **Web** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-268">Select **Web** in the left pane.</span></span>
  * <span data-ttu-id="d723a-269">Wählen Sie im mittleren Bereich **Leere HTML-Datei** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-269">Select **Empty HTML file** in the center pane.</span></span>
  * <span data-ttu-id="d723a-270">Geben Sie *Index.cshtml* in das Feld **Name** ein.</span><span class="sxs-lookup"><span data-stu-id="d723a-270">Type *Index.cshtml* in the **Name** box.</span></span>
  * <span data-ttu-id="d723a-271">Wählen Sie **Neu** aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-271">Select **New**.</span></span>

![Dialogfeld „Neues Element hinzufügen“](adding-view/_static/add_view_mac.png)

---

<span data-ttu-id="d723a-273">Ersetzen Sie den Inhalt der Razor-Ansichtsdatei *Views/HelloWorld/Index.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d723a-273">Replace the contents of the *Views/HelloWorld/Index.cshtml* Razor view file with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/HelloWorld/Index1.cshtml?highlight=7)]

<span data-ttu-id="d723a-274">Navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="d723a-274">Navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d723a-275">Die `Index`-Methode im `HelloWorldController` hatte nicht viel zu tun. Sie diente zum Ausführen der Anweisung `return View();`, die angab, dass die Methode eine Ansichtsvorlagendatei zum Rendern einer Antwort im Browser verwenden sollte.</span><span class="sxs-lookup"><span data-stu-id="d723a-275">The `Index` method in the `HelloWorldController` didn't do much; it ran the statement `return View();`, which specified that the method should use a view template file to render a response to the browser.</span></span> <span data-ttu-id="d723a-276">Da kein Name für eine Ansichtsvorlagendatei angegeben wurde, verwendet MVC standardmäßig die Standardansichtsdatei.</span><span class="sxs-lookup"><span data-stu-id="d723a-276">Because a view template file name wasn't specified, MVC defaulted to using the default view file.</span></span> <span data-ttu-id="d723a-277">Die Standardansichtsdatei hat den gleichen Namen wie die Methode (`Index`), sodass er in */Views/HelloWorld/Index.cshtml* verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-277">The default view file has the same name as the method (`Index`), so in the */Views/HelloWorld/Index.cshtml* is used.</span></span> <span data-ttu-id="d723a-278">Die folgende Abbildung zeigt die Zeichenfolge "Hello from our View Template!“,</span><span class="sxs-lookup"><span data-stu-id="d723a-278">The image below shows the string "Hello from our View Template!"</span></span> <span data-ttu-id="d723a-279">die in der Ansicht hartcodiert ist.</span><span class="sxs-lookup"><span data-stu-id="d723a-279">hard-coded in the view.</span></span>

![Browserfenster](~/tutorials/first-mvc-app/adding-view/_static/hell_template.png)

## <a name="change-views-and-layout-pages"></a><span data-ttu-id="d723a-281">Ändern von Ansichten und Layoutseiten</span><span class="sxs-lookup"><span data-stu-id="d723a-281">Change views and layout pages</span></span>

<span data-ttu-id="d723a-282">Wählen Sie die Menülinks aus (**MvcMovie**, **Home**, **Privacy**).</span><span class="sxs-lookup"><span data-stu-id="d723a-282">Select the menu links (**MvcMovie**, **Home**, and **Privacy**).</span></span> <span data-ttu-id="d723a-283">Auf jeder Seite wird dasselbe Menülayout gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-283">Each page shows the same menu layout.</span></span> <span data-ttu-id="d723a-284">Das Menülayout wird mithilfe der Datei *Views/Shared/_Layout.cshtml* implementiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-284">The menu layout is implemented in the *Views/Shared/_Layout.cshtml* file.</span></span> <span data-ttu-id="d723a-285">Öffnen Sie die Datei *Views/Shared/_Layout.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-285">Open the *Views/Shared/_Layout.cshtml* file.</span></span>

<span data-ttu-id="d723a-286">[Layout](xref:mvc/views/layout)-Vorlagen ermöglichen Ihnen, das HTML-Containerlayout Ihrer Website zentral anzugeben und dann auf mehrere Seiten Ihrer Website anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="d723a-286">[Layout](xref:mvc/views/layout) templates allow you to specify the HTML container layout of your site in one place and then apply it across multiple pages in your site.</span></span> <span data-ttu-id="d723a-287">Suchen Sie die Zeile `@RenderBody()`.</span><span class="sxs-lookup"><span data-stu-id="d723a-287">Find the `@RenderBody()` line.</span></span> <span data-ttu-id="d723a-288">`RenderBody` ist ein Platzhalter, bei dem alle ansichtsspezifischen Seiten, die Sie erstellen, von der Layoutseite *umschlossen* angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-288">`RenderBody` is a placeholder where all the view-specific pages you create show up, *wrapped* in the layout page.</span></span> <span data-ttu-id="d723a-289">Wenn Sie beispielsweise den Link **Privacy** auswählen, wird die Ansicht **Views/Home/Privacy.cshtml** in der `RenderBody`-Methode gerendert.</span><span class="sxs-lookup"><span data-stu-id="d723a-289">For example, if you select the **Privacy** link, the **Views/Home/Privacy.cshtml** view is rendered inside the `RenderBody` method.</span></span>

## <a name="change-the-title-footer-and-menu-link-in-the-layout-file"></a><span data-ttu-id="d723a-290">Ändern des Titels, der Fußzeile und Menülinks in der Layoutdatei</span><span class="sxs-lookup"><span data-stu-id="d723a-290">Change the title, footer, and menu link in the layout file</span></span>

* <span data-ttu-id="d723a-291">Ändern Sie in den Elementen „Titel“ und „Fußzeile“ `MvcMovie` in `Movie App`.</span><span class="sxs-lookup"><span data-stu-id="d723a-291">In the title and footer elements, change `MvcMovie` to `Movie App`.</span></span>
* <span data-ttu-id="d723a-292">Ändern Sie das Ankerlement `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` in `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span><span class="sxs-lookup"><span data-stu-id="d723a-292">Change the anchor element `<a class="navbar-brand" asp-area="" asp-controller="Home" asp-action="Index">MvcMovie</a>` to `<a class="navbar-brand" asp-controller="Movies" asp-action="Index">Movie App</a>`.</span></span>

<span data-ttu-id="d723a-293">Im folgenden Markup sind die Änderungen hervorgehoben dargestellt:</span><span class="sxs-lookup"><span data-stu-id="d723a-293">The following markup shows the highlighted changes:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Shared/_Layout.cshtml?highlight=6,24,51)]

<span data-ttu-id="d723a-294">Im obigen Markup wurde das [Anchor-Tag-Hilfsprogramm-Attribut](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) von `asp-area` ausgelassen, da für diese App keine [Bereiche](xref:mvc/controllers/areas) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-294">In the preceding markup, the `asp-area` [anchor Tag Helper attribute](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) was omitted because this app is not using [Areas](xref:mvc/controllers/areas).</span></span>

<!-- Routing has changed in 2.2, it's going to the last route.
>[!WARNING]
> We haven't implemented the `Movies` controller yet, so if you click the `Movie App` link, you get a 404 (Not found) error.
-->

<span data-ttu-id="d723a-295">**Hinweis:** Der `Movies`-Controller wurde nicht implementiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-295">**Note**: The `Movies` controller has not been implemented.</span></span> <span data-ttu-id="d723a-296">An diesem Punkt ist der `Movie App`-Link nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="d723a-296">At this point, the `Movie App` link is not functional.</span></span>

<span data-ttu-id="d723a-297">Speichern Sie Ihre Änderungen, und wählen den **Privacy**-Link aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-297">Save your changes and select the **Privacy** link.</span></span> <span data-ttu-id="d723a-298">Wie Sie sehen, wird auf der Browserregisterkarte der Titel **Privacy Policy - Movie App** anstelle des Titels **Privacy Policy - Mvc Movie** angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d723a-298">Notice how the title on the browser tab displays **Privacy Policy - Movie App** instead of **Privacy Policy - Mvc Movie**:</span></span>

![Registerkarte „Privacy“](~/tutorials/first-mvc-app/adding-view/_static/privacy50.png)

<span data-ttu-id="d723a-300">Wählen Sie den Link **Home** aus, und beachten Sie, dass im Text für den Titel und den Anker ebenfalls **Movie App** angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-300">Select the **Home** link and notice that the title and anchor text also display **Movie App**.</span></span> <span data-ttu-id="d723a-301">Wir haben also eine Änderung des Texts und Titels in der Layoutvorlage einmalig vorgenommen, die von der gesamten Website übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-301">We were able to make the change once in the layout template and have all pages on the site reflect the new link text and new title.</span></span>

<span data-ttu-id="d723a-302">Untersuchen Sie die Datei *Views/_ViewStart.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d723a-302">Examine the *Views/_ViewStart.cshtml* file:</span></span>

```cshtml
@{
    Layout = "_Layout";
}
```

<span data-ttu-id="d723a-303">Die Datei *Views/_ViewStart.cshtml* fügt jeder Ansicht die Datei *Views/Shared/_Layout.cshtml* hinzu.</span><span class="sxs-lookup"><span data-stu-id="d723a-303">The *Views/_ViewStart.cshtml* file brings in the *Views/Shared/_Layout.cshtml* file to each view.</span></span> <span data-ttu-id="d723a-304">Die `Layout`-Eigenschaft kann verwendet werden, um eine andere Layoutansicht festzulegen, oder legen Sie sie auf `null` fest, damit keine Layoutdatei verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-304">The `Layout` property can be used to set a different layout view, or set it to `null` so no layout file will be used.</span></span>

<span data-ttu-id="d723a-305">Ändern Sie den Titel und das `<h2>`-Element der Ansichtsdatei *Views/HelloWorld/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d723a-305">Change the title and `<h2>` element of the *Views/HelloWorld/Index.cshtml* view file:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Index2.cshtml?highlight=2,5)]

<span data-ttu-id="d723a-306">Der Titel und das `<h2>`-Element sind geringfügig anders, sodass Sie sehen können, welcher Codeabschnitt die Anzeige ändert.</span><span class="sxs-lookup"><span data-stu-id="d723a-306">The title and `<h2>` element are slightly different so you can see which bit of code changes the display.</span></span>

<span data-ttu-id="d723a-307">`ViewData["Title"] = "Movie List";` im Code oben legt die `Title`-Eigenschaft des Wörterbuchs `ViewData`auf „Movie List“ fest.</span><span class="sxs-lookup"><span data-stu-id="d723a-307">`ViewData["Title"] = "Movie List";` in the code above sets the `Title` property of the `ViewData` dictionary to "Movie List".</span></span> <span data-ttu-id="d723a-308">Die `Title`-Eigenschaft wird im HTML-Element `<title>` der Layoutseite verwendet:</span><span class="sxs-lookup"><span data-stu-id="d723a-308">The `Title` property is used in the `<title>` HTML element in the layout page:</span></span>

```cshtml
<title>@ViewData["Title"] - Movie App</title>
```

<span data-ttu-id="d723a-309">Speichern Sie die Änderung, und navigieren Sie zu `https://localhost:{PORT}/HelloWorld`.</span><span class="sxs-lookup"><span data-stu-id="d723a-309">Save the change and navigate to `https://localhost:{PORT}/HelloWorld`.</span></span> <span data-ttu-id="d723a-310">Sie sehen, dass sich der Browsertitel, die primäre Überschrift und die sekundären Überschriften geändert haben.</span><span class="sxs-lookup"><span data-stu-id="d723a-310">Notice that the browser title, the primary heading, and the secondary headings have changed.</span></span> <span data-ttu-id="d723a-311">(Wenn die Änderungen nicht im Browser angezeigt werden, zeigen Sie ggf. zwischengespeicherten Inhalt an.</span><span class="sxs-lookup"><span data-stu-id="d723a-311">(If you don't see changes in the browser, you might be viewing cached content.</span></span> <span data-ttu-id="d723a-312">Drücken Sie in Ihrem Browser STRG+F5, um das Laden der Antwort vom Server zu erzwingen.) Der Titel des Browsers wird mithilfe des Elements `ViewData["Title"]` erstellt, das wir in der Ansichtsvorlange *Index.cshtml* festgelegt haben, und des zusätzlichen Elements „- Movie App“, das in der Layoutdatei hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="d723a-312">Press Ctrl+F5 in your browser to force the response from the server to be loaded.) The browser title is created with `ViewData["Title"]` we set in the *Index.cshtml* view template and the additional "- Movie App" added in the layout file.</span></span>

<span data-ttu-id="d723a-313">Beachten Sie außerdem, wie der Inhalt der Ansichtsvorlage *Index.cshtml* mit der Ansichtsvorlage *Views/Shared/_Layout.cshtml* zusammengeführt und eine einzelne HTML-Antwort an den Browser gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d723a-313">Also notice how the content in the *Index.cshtml* view template was merged with the *Views/Shared/_Layout.cshtml* view template and a single HTML response was sent to the browser.</span></span> <span data-ttu-id="d723a-314">Layoutvorlagen erleichtern ungemein das Vornehmen von Änderungen an allen Seiten in Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="d723a-314">Layout templates make it really easy to make changes that apply across all of the pages in your application.</span></span> <span data-ttu-id="d723a-315">Weitere Informationen dazu finden Sie unter [Layout](xref:mvc/views/layout).</span><span class="sxs-lookup"><span data-stu-id="d723a-315">To learn more see [Layout](xref:mvc/views/layout).</span></span>

![Ansicht mit Filmliste](~/tutorials/first-mvc-app/adding-view/_static/hell3.png)

<span data-ttu-id="d723a-317">Unser kleiner Beitrag zu den „Daten“ (in diesem Fall die Meldung "Hello from our View Template!")</span><span class="sxs-lookup"><span data-stu-id="d723a-317">Our little bit of "data" (in this case the "Hello from our View Template!"</span></span> <span data-ttu-id="d723a-318">ist jedoch hartcodiert.</span><span class="sxs-lookup"><span data-stu-id="d723a-318">message) is hard-coded, though.</span></span> <span data-ttu-id="d723a-319">Die MVC-Anwendung weist ein „V“ (View [Ansicht]) auf, und Sie verfügen über ein „C“ (Controller), aber noch nicht über ein „M“ (Modell).</span><span class="sxs-lookup"><span data-stu-id="d723a-319">The MVC application has a "V" (view) and you've got a "C" (controller), but no "M" (model) yet.</span></span>

## <a name="passing-data-from-the-controller-to-the-view"></a><span data-ttu-id="d723a-320">Übergeben von Daten vom Controller an die Ansicht</span><span class="sxs-lookup"><span data-stu-id="d723a-320">Passing Data from the Controller to the View</span></span>

<span data-ttu-id="d723a-321">Controlleraktionen werden als Antwort auf eine eingehende URL-Anforderung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="d723a-321">Controller actions are invoked in response to an incoming URL request.</span></span> <span data-ttu-id="d723a-322">Eine Controllerklasse ist der Ort, an dem der Code geschrieben wird, der die eingehenden Browseranforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="d723a-322">A controller class is where the code is written that handles the incoming browser requests.</span></span> <span data-ttu-id="d723a-323">Der Controller ruft Daten aus einer Datenquelle ab und entscheidet, welche Art von Antwort an den Browser zurückgesendet wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-323">The controller retrieves data from a data source and decides what type of response to send back to the browser.</span></span> <span data-ttu-id="d723a-324">Ansichtsvorlagen können von einem Controller zum Generieren und Formatieren einer HTML-Antwort an den Browser verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-324">View templates can be used from a controller to generate and format an HTML response to the browser.</span></span>

<span data-ttu-id="d723a-325">Controller sind für die Bereitstellung der benötigten Daten zuständig, damit eine Ansichtsvorlage eine Antwort liefern kann.</span><span class="sxs-lookup"><span data-stu-id="d723a-325">Controllers are responsible for providing the data required in order for a view template to render a response.</span></span> <span data-ttu-id="d723a-326">Eine bewährte Methode: Ansichtsvorlagen sollten **keine** Geschäftslogik ausführen bzw. nicht direkt mit einer Datenbank interagieren.</span><span class="sxs-lookup"><span data-stu-id="d723a-326">A best practice: View templates should **not** perform business logic or interact with a database directly.</span></span> <span data-ttu-id="d723a-327">Eine Ansichtsvorlage sollte stattdessen nur mit den Daten arbeiten, die ihr vom Controller bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-327">Rather, a view template should work only with the data that's provided to it by the controller.</span></span> <span data-ttu-id="d723a-328">Durch Beibehaltung dieser Bereichstrennung bleibt der Code übersichtlich, testfähig und verwaltbar.</span><span class="sxs-lookup"><span data-stu-id="d723a-328">Maintaining this "separation of concerns" helps keep the code clean, testable, and maintainable.</span></span>

<span data-ttu-id="d723a-329">Derzeit verwendet die `Welcome`-Methode in der `HelloWorldController`-Klasse die Parameter `name` und `ID` und gibt anschließend die Werte direkt an den Browser aus.</span><span class="sxs-lookup"><span data-stu-id="d723a-329">Currently, the `Welcome` method in the `HelloWorldController` class takes a `name` and a `ID` parameter and then outputs the values directly to the browser.</span></span> <span data-ttu-id="d723a-330">Anstatt den Controller diese Antwort als Zeichenfolge rendern zu lassen, passen Sie den Controller so an, dass er eine Ansichtsvorlage verwendet.</span><span class="sxs-lookup"><span data-stu-id="d723a-330">Rather than have the controller render this response as a string, change the controller to use a view template instead.</span></span> <span data-ttu-id="d723a-331">Die Ansichtsvorlage erstellt eine dynamische Antwort, was bedeutet, dass entsprechende Datenbestandteile vom Controller an die Ansicht übergeben werden müssen, damit eine Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d723a-331">The view template generates a dynamic response, which means that appropriate bits of data must be passed from the controller to the view in order to generate the response.</span></span> <span data-ttu-id="d723a-332">Lassen Sie hierzu den Controller die dynamischen Daten (Parameter), die die Ansichtsvorlage benötigt, in einem `ViewData`-Wörterbuch ablegen, auf das die Ansichtsvorlage zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="d723a-332">Do this by having the controller put the dynamic data (parameters) that the view template needs in a `ViewData` dictionary that the view template can then access.</span></span>

<span data-ttu-id="d723a-333">Ändern Sie in *HelloWorldController.cs* die `Welcome`-Methode so, dass die Werte `Message` und `NumTimes` dem Wörterbuch `ViewData` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-333">In *HelloWorldController.cs*, change the `Welcome` method to add a `Message` and `NumTimes` value to the `ViewData` dictionary.</span></span> <span data-ttu-id="d723a-334">Das Wörterbuch `ViewData` ist ein dynamisches Objekt, was bedeutet, dass jeder beliebige Typ verwendet werden kann. Das `ViewData`-Objekt hat erst dann definierte Eigenschaften, wenn Sie ihm etwas hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="d723a-334">The `ViewData` dictionary is a dynamic object, which means any type can be used; the `ViewData` object has no defined properties until you put something inside it.</span></span> <span data-ttu-id="d723a-335">Das [MVC-Modellbindungssystem](xref:mvc/models/model-binding) ordnet automatisch die benannten Parameter (`name` und `numTimes`) aus der Abfragezeichenfolge auf der Adressleiste den Parametern der Methode zu.</span><span class="sxs-lookup"><span data-stu-id="d723a-335">The [MVC model binding system](xref:mvc/models/model-binding) automatically maps the named parameters (`name` and `numTimes`) from the query string in the address bar to parameters in your method.</span></span> <span data-ttu-id="d723a-336">Die vollständige Datei *HelloWorldController.cs* sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="d723a-336">The complete *HelloWorldController.cs* file looks like this:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/HelloWorldController.cs?name=snippet_5)]

<span data-ttu-id="d723a-337">Das Wörterbuchobjekt `ViewData` enthält Daten, die an die Ansicht übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="d723a-337">The `ViewData` dictionary object contains data that will be passed to the view.</span></span>

<span data-ttu-id="d723a-338">Erstellen Sie die Ansichtsvorlage für die Begrüßung namens *Views/HelloWorld/Welcome.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="d723a-338">Create a Welcome view template named *Views/HelloWorld/Welcome.cshtml*.</span></span>

<span data-ttu-id="d723a-339">Erstellen Sie eine Schleife in der Ansichtsvorlage *Welcome.cshtml*, die „Hello“ `NumTimes` anzeigt.</span><span class="sxs-lookup"><span data-stu-id="d723a-339">You'll create a loop in the *Welcome.cshtml* view template that displays "Hello" `NumTimes`.</span></span> <span data-ttu-id="d723a-340">Ersetzen Sie den Inhalt von *Views/HelloWorld/Welcome.cshtml* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="d723a-340">Replace the contents of *Views/HelloWorld/Welcome.cshtml* with the following:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/HelloWorld/Welcome.cshtml)]

<span data-ttu-id="d723a-341">Speichern Sie die Änderungen, und navigieren Sie zur folgenden URL:</span><span class="sxs-lookup"><span data-stu-id="d723a-341">Save your changes and browse to the following URL:</span></span>

`https://localhost:{PORT}/HelloWorld/Welcome?name=Rick&numtimes=4`

<span data-ttu-id="d723a-342">Daten werden der URL entnommen und mithilfe der [MVC-Modellbindung](xref:mvc/models/model-binding) an den Controller übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-342">Data is taken from the URL and passed to the controller using the [MVC model binder](xref:mvc/models/model-binding) .</span></span> <span data-ttu-id="d723a-343">Der Controller packt die Daten in einem `ViewData`-Wörterbuch und übergibt das Objekt an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d723a-343">The controller packages the data into a `ViewData` dictionary and passes that object to the view.</span></span> <span data-ttu-id="d723a-344">Die Ansicht rendert dann die Daten im HTML-Format im Browser.</span><span class="sxs-lookup"><span data-stu-id="d723a-344">The view then renders the data as HTML to the browser.</span></span>

![Die Ansicht „Privacy“ mit der Beschriftung „Welcome“ und der viermal gezeigten Wortfolge „Hello Rick“](~/tutorials/first-mvc-app/adding-view/_static/rick2.png)

<span data-ttu-id="d723a-346">Im obigen Beispiel wurde das Wörterbuch `ViewData` verwendet, um Daten vom Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-346">In the sample above, the `ViewData` dictionary was used to pass data from the controller to a view.</span></span> <span data-ttu-id="d723a-347">Später in diesem Tutorial wird ein Ansichtsmodell verwendet, um Daten von einem Controller an eine Ansicht zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d723a-347">Later in the tutorial, a view model is used to pass data from a controller to a view.</span></span> <span data-ttu-id="d723a-348">Der Ansatz mit dem Ansichtsmodell für das Übergeben von Daten ist im Allgemeinen dem Ansatz mit dem Wörterbuch `ViewData` vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="d723a-348">The view model approach to passing data is generally much preferred over the `ViewData` dictionary approach.</span></span> <span data-ttu-id="d723a-349">Weitere Informationen finden Sie im Artikel [When to use ViewBag, ViewData, or TempData (Verwendung von ViewBag, ViewData oder TempData)](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/).</span><span class="sxs-lookup"><span data-stu-id="d723a-349">See [When to use ViewBag, ViewData, or TempData](https://www.rachelappel.com/when-to-use-viewbag-viewdata-or-tempdata-in-asp-net-mvc-3-applications/) for more information.</span></span>

<span data-ttu-id="d723a-350">Im nächsten Tutorial wird eine Filmdatenbank erstellt.</span><span class="sxs-lookup"><span data-stu-id="d723a-350">In the next tutorial, a database of movies is created.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d723a-351">[Zurück](adding-controller.md)
> [Weiter](adding-model.md)</span><span class="sxs-lookup"><span data-stu-id="d723a-351">[Previous](adding-controller.md)
[Next](adding-model.md)</span></span>

::: moniker-end
