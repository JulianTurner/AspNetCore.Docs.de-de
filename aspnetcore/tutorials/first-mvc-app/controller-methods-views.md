---
title: 'Teil 6: Controllermethoden und Ansichten in ASP.NET Core'
author: rick-anderson
description: Dies ist Teil 6 der Tutorialreihe zum Hinzufügen eines Modells zu einer ASP.NET Core MVC-App.
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
uid: tutorials/first-mvc-app/controller-methods-views
ms.openlocfilehash: b4850821317b6907452793ef09194844c90c0137
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050770"
---
# <a name="part-6-controller-methods-and-views-in-aspnet-core"></a><span data-ttu-id="e7277-103">Teil 6: Controllermethoden und Ansichten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e7277-103">Part 6, controller methods and views in ASP.NET Core</span></span>

<span data-ttu-id="e7277-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e7277-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e7277-105">Für den Anfang ist die Movie-App schon recht ansprechend, doch es gibt Raum für Verbesserungen. Zum Beispiel sollte **ReleaseDate** zwei Wörter sein.</span><span class="sxs-lookup"><span data-stu-id="e7277-105">We have a good start to the movie app, but the presentation isn't ideal, for example, **ReleaseDate** should be two words.</span></span>

![Indexansicht: „ReleaseDate“ ist ein Wort (ohne Leerzeichen), und bei jedem Veröffentlichungsdatum wird die Uhrzeit 12: 00 Uhr angezeigt](working-with-sql/_static/m55.png)

<span data-ttu-id="e7277-107">Öffnen Sie die Datei *Models/Movie.cs* , und fügen Sie die nachfolgend gezeigten markierten Zeilen hinzu:</span><span class="sxs-lookup"><span data-stu-id="e7277-107">Open the *Models/Movie.cs* file and add the highlighted lines shown below:</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie22/Models/MovieDateFixed.cs?name=snippet_1&highlight=2,3,12-13,17)]

<span data-ttu-id="e7277-108">Im nächsten Tutorial behandeln wir [Datenanmerkungen](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6).</span><span class="sxs-lookup"><span data-stu-id="e7277-108">We cover [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) in the next tutorial.</span></span> <span data-ttu-id="e7277-109">Das [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata)-Attribut gibt an, was für den Namen eines Felds angezeigt werden soll (in diesem Fall „Release Date“ anstatt „ReleaseDate“).</span><span class="sxs-lookup"><span data-stu-id="e7277-109">The [Display](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.metadata.displaymetadata) attribute specifies what to display for the name of a field (in this case "Release Date" instead of "ReleaseDate").</span></span> <span data-ttu-id="e7277-110">Das [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter)-Attribut gibt den Typ der Daten (Datum) an, damit die im Feld gespeicherten Informationen zur Uhrzeit nicht angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e7277-110">The [DataType](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.internal.datatypeattributeadapter) attribute specifies the type of the data (Date), so the time information stored in the field isn't displayed.</span></span>

<span data-ttu-id="e7277-111">Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="e7277-111">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="e7277-112">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="e7277-112">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="e7277-113">Navigieren Sie zum `Movies`-Controller, und halten Sie den Mauszeiger über einen **Bearbeiten** -Link, um die Ziel-URL zu sehen.</span><span class="sxs-lookup"><span data-stu-id="e7277-113">Browse to the `Movies` controller and hold the mouse pointer over an **Edit** link to see the target URL.</span></span>

![Browserfenster mit Maus über dem Link „Bearbeiten“ und der angezeigten Link-URL von https://localhost:5001/Movies/Edit/5](~/tutorials/first-mvc-app/controller-methods-views/_static/edit7.png)

<span data-ttu-id="e7277-115">Die Links **Bearbeiten** , **Details** und **Löschen** werden mithilfe des MVC Core-Hilfsprogramms für Ankertags in der Datei *Views/Movies/Index.cshtml* generiert.</span><span class="sxs-lookup"><span data-stu-id="e7277-115">The **Edit** , **Details** , and **Delete** links are generated by the Core MVC Anchor Tag Helper in the *Views/Movies/Index.cshtml* file.</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/IndexOriginal.cshtml?highlight=1-3&range=46-50)]

<span data-ttu-id="e7277-116">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in :::no-loc(Razor):::-Dateien.</span><span class="sxs-lookup"><span data-stu-id="e7277-116">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="e7277-117">Im obigen Code generiert `AnchorTagHelper` dynamisch den HTML-`href`-Attributwert aus der Controlleraktionsmethode und der Routen-ID. Verwenden Sie in Ihrem bevorzugten Browser **Quelltext anzeigen** oder die Entwicklertools, um das generierte Markup zu untersuchen.</span><span class="sxs-lookup"><span data-stu-id="e7277-117">In the code above, the `AnchorTagHelper` dynamically generates the HTML `href` attribute value from the controller action method and route id. You use **View Source** from your favorite browser or use the developer tools to examine the generated markup.</span></span> <span data-ttu-id="e7277-118">Ein Teil des generierten HTML-Codes wird unten gezeigt:</span><span class="sxs-lookup"><span data-stu-id="e7277-118">A portion of the generated HTML is shown below:</span></span>

```html
 <td>
    <a href="/Movies/Edit/4"> Edit </a> |
    <a href="/Movies/Details/4"> Details </a> |
    <a href="/Movies/Delete/4"> Delete </a>
</td>
```

<span data-ttu-id="e7277-119">Erinnern Sie sich an das Format für das [Routing](xref:mvc/controllers/routing) in der Datei *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="e7277-119">Recall the format for [routing](xref:mvc/controllers/routing) set in the *Startup.cs* file:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_1&highlight=5)]

<span data-ttu-id="e7277-120">ASP.NET Core übersetzt `https://localhost:5001/Movies/Edit/4` in eine Anforderung an die `Edit`-Aktionsmethode des `Movies`-Controllers mit dem `Id`-Parameter 4.</span><span class="sxs-lookup"><span data-stu-id="e7277-120">ASP.NET Core translates `https://localhost:5001/Movies/Edit/4` into a request to the `Edit` action method of the `Movies` controller with the parameter `Id` of 4.</span></span> <span data-ttu-id="e7277-121">(Controllermethoden werden auch als Aktionsmethoden bezeichnet.)</span><span class="sxs-lookup"><span data-stu-id="e7277-121">(Controller methods are also known as action methods.)</span></span>

<span data-ttu-id="e7277-122">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) sind eines der am häufigsten verwendeten neuen Features in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="e7277-122">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are one of the most popular new features in ASP.NET Core.</span></span> <span data-ttu-id="e7277-123">Weitere Informationen finden Sie unter [Zusätzliche Ressourcen](#additional-resources).</span><span class="sxs-lookup"><span data-stu-id="e7277-123">For more information, see [Additional resources](#additional-resources).</span></span>

<a name="get-post"></a>

<span data-ttu-id="e7277-124">Öffnen Sie den `Movies`-Controller, und untersuchen Sie die beiden `Edit`-Aktionsmethoden.</span><span class="sxs-lookup"><span data-stu-id="e7277-124">Open the `Movies` controller and examine the two `Edit` action methods.</span></span> <span data-ttu-id="e7277-125">Der folgende Code zeigt die `HTTP GET Edit`-Methode, die den Film abruft und das Bearbeitungsformular ausfüllt, das von der :::no-loc(Razor):::-Datei *Edit.cshtml* generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e7277-125">The following code shows the `HTTP GET Edit` method, which fetches the movie and populates the edit form generated by the *Edit.cshtml* :::no-loc(Razor)::: file.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="e7277-126">Der folgende Code zeigt die `HTTP POST Edit`-Methode, die die bereitgestellten Filmwerte verarbeitet:</span><span class="sxs-lookup"><span data-stu-id="e7277-126">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="e7277-127">Der folgende Code zeigt die `HTTP POST Edit`-Methode, die die bereitgestellten Filmwerte verarbeitet:</span><span class="sxs-lookup"><span data-stu-id="e7277-127">The following code shows the `HTTP POST Edit` method, which processes the posted movie values:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="e7277-128">Das `[Bind]`-Attribut ist eine Möglichkeit zum Schutz vor [zu vielen Angaben](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span><span class="sxs-lookup"><span data-stu-id="e7277-128">The `[Bind]` attribute is one way to protect against [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost).</span></span> <span data-ttu-id="e7277-129">Sie sollten nur Eigenschaften in das `[Bind]`-Attribut aufnehmen, die Sie ändern möchten.</span><span class="sxs-lookup"><span data-stu-id="e7277-129">You should only include properties in the `[Bind]` attribute that you want to change.</span></span> <span data-ttu-id="e7277-130">Weitere Informationen finden Sie unter [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application) (Schützen Ihres Controllers vor zu vielen Angaben).</span><span class="sxs-lookup"><span data-stu-id="e7277-130">For more information, see [Protect your controller from over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application).</span></span> <span data-ttu-id="e7277-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) bietet eine alternative Methode, um zu viele Angaben zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="e7277-131">[ViewModels](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/) provide an alternative approach to prevent over-posting.</span></span>

<span data-ttu-id="e7277-132">Beachten Sie, dass der zweiten `Edit`-Aktionsmethode das `[HttpPost]`-Attribut vorangestellt ist.</span><span class="sxs-lookup"><span data-stu-id="e7277-132">Notice the second `Edit` action method is preceded by the `[HttpPost]` attribute.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2&highlight=1)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2&highlight=4)]

::: moniker-end

<span data-ttu-id="e7277-133">Das `HttpPost`-Attribut gibt an, dass diese `Edit`-Methode *nur* für `POST`-Anforderungen aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="e7277-133">The `HttpPost` attribute specifies that this `Edit` method can be invoked *only* for `POST` requests.</span></span> <span data-ttu-id="e7277-134">Sie könnten das `[HttpGet]`-Attribut auf die erste Bearbeitungsmethode anwenden, aber dies ist nicht erforderlich, da `[HttpGet]` der Standardwert ist.</span><span class="sxs-lookup"><span data-stu-id="e7277-134">You could apply the `[HttpGet]` attribute to the first edit method, but that's not necessary because `[HttpGet]` is the default.</span></span>

<span data-ttu-id="e7277-135">Das `ValidateAntiForgeryToken`-Attribut wird verwendet, um [die Fälschung einer Anforderung zu verhindern](xref:security/anti-request-forgery). Es wird einem Fälschungssicherheitstoken zugeordnet, das in der Datei für die Bearbeitungsansicht ( *Views/Movies/Edit.cshtml* ) generiert wird.</span><span class="sxs-lookup"><span data-stu-id="e7277-135">The `ValidateAntiForgeryToken` attribute is used to [prevent forgery of a request](xref:security/anti-request-forgery) and is paired up with an anti-forgery token generated in the edit view file ( *Views/Movies/Edit.cshtml* ).</span></span> <span data-ttu-id="e7277-136">Die Datei für die Bearbeitungsansicht generiert das Fälschungssicherheitstoken mit dem [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms).</span><span class="sxs-lookup"><span data-stu-id="e7277-136">The edit view file generates the anti-forgery token with the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Movies/Edit.cshtml?range=9)]

<span data-ttu-id="e7277-137">Das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert ein ausgeblendetes Fälschungssicherheitstoken, das mit dem von `[ValidateAntiForgeryToken]` generierten Fälschungssicherheitstoken in der `Edit`-Methode des Movies-Controllers übereinstimmen muss.</span><span class="sxs-lookup"><span data-stu-id="e7277-137">The [Form Tag Helper](xref:mvc/views/working-with-forms) generates a hidden anti-forgery token that must match the `[ValidateAntiForgeryToken]` generated anti-forgery token in the `Edit` method of the Movies controller.</span></span> <span data-ttu-id="e7277-138">Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.</span><span class="sxs-lookup"><span data-stu-id="e7277-138">For more information, see <xref:security/anti-request-forgery>.</span></span>

<span data-ttu-id="e7277-139">Die `HttpGet Edit`-Methode verwendet den `ID`-Parameter eines Films, sucht den Film mit der `FindAsync`-Methode von Entity Framework, und gibt den ausgewählten Film an die Bearbeitungsansicht zurück.</span><span class="sxs-lookup"><span data-stu-id="e7277-139">The `HttpGet Edit` method takes the movie `ID` parameter, looks up the movie using the Entity Framework `FindAsync` method, and returns the selected movie to the Edit view.</span></span> <span data-ttu-id="e7277-140">Wenn ein Film nicht gefunden werden kann, wird `NotFound` (HTTP 404) zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="e7277-140">If a movie cannot be found, `NotFound` (HTTP 404) is returned.</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit1)]

<span data-ttu-id="e7277-141">Als das Gerüstsystem die Bearbeitungsansicht erstellt hat, wurde die `Movie`-Klasse überprüft und Code zum Rendern der `<label>`- und `<input>`-Elemente für jede Eigenschaft der Klasse erstellt.</span><span class="sxs-lookup"><span data-stu-id="e7277-141">When the scaffolding system created the Edit view, it examined the `Movie` class and created code to render `<label>` and `<input>` elements for each property of the class.</span></span> <span data-ttu-id="e7277-142">Das folgende Beispiel zeigt die Bearbeitungsansicht, die vom Visual Studio-Gerüstsystem generiert wurde:</span><span class="sxs-lookup"><span data-stu-id="e7277-142">The following example shows the Edit view that was generated by the Visual Studio scaffolding system:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/EditOriginal.cshtml)]

<span data-ttu-id="e7277-143">Beachten Sie, dass die Ansichtsvorlage eine `@model MvcMovie.Models.Movie`-Anweisung am Anfang der Datei aufweist.</span><span class="sxs-lookup"><span data-stu-id="e7277-143">Notice how the view template has a `@model MvcMovie.Models.Movie` statement at the top of the file.</span></span> <span data-ttu-id="e7277-144">`@model MvcMovie.Models.Movie` gibt an, dass die Ansicht erwartet, dass das Modell für die Ansichtsvorlage den Typ `Movie` hat.</span><span class="sxs-lookup"><span data-stu-id="e7277-144">`@model MvcMovie.Models.Movie` specifies that the view expects the model for the view template to be of type `Movie`.</span></span>

<span data-ttu-id="e7277-145">Der Gerüstcode verwendet mehrere Methoden von Taghilfsprogrammen, um das HTML-Markup zu optimieren.</span><span class="sxs-lookup"><span data-stu-id="e7277-145">The scaffolded code uses several Tag Helper methods to streamline the HTML markup.</span></span> <span data-ttu-id="e7277-146">Das [Hilfsprogramm für Bezeichnungstags](xref:mvc/views/working-with-forms) zeigt den Namen des Felds an („Title“, „ReleaseDate“, „Genre“ oder „Price“).</span><span class="sxs-lookup"><span data-stu-id="e7277-146">The [Label Tag Helper](xref:mvc/views/working-with-forms) displays the name of the field ("Title", "ReleaseDate", "Genre", or "Price").</span></span> <span data-ttu-id="e7277-147">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) rendert ein HTML-`<input>`-Element.</span><span class="sxs-lookup"><span data-stu-id="e7277-147">The [Input Tag Helper](xref:mvc/views/working-with-forms) renders an HTML `<input>` element.</span></span> <span data-ttu-id="e7277-148">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms) zeigt Validierungsmeldungen an, die dieser Eigenschaft zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="e7277-148">The [Validation Tag Helper](xref:mvc/views/working-with-forms) displays any validation messages associated with that property.</span></span>

<span data-ttu-id="e7277-149">Führen Sie die Anwendung aus, und navigieren Sie zur `/Movies`-URL.</span><span class="sxs-lookup"><span data-stu-id="e7277-149">Run the application and navigate to the `/Movies` URL.</span></span> <span data-ttu-id="e7277-150">Klicken Sie auf einen Link **Bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="e7277-150">Click an **Edit** link.</span></span> <span data-ttu-id="e7277-151">Zeigen Sie im Browser den Quelltext für die Seite an.</span><span class="sxs-lookup"><span data-stu-id="e7277-151">In the browser, view the source for the page.</span></span> <span data-ttu-id="e7277-152">Der generierte HTML-Code für das `<form>`-Element wird unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e7277-152">The generated HTML for the `<form>` element is shown below.</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Views/Shared/edit_view_source.html?highlight=1,6,10,17,24,28)]

<span data-ttu-id="e7277-153">Die `<input>`-Elemente sind in einem `HTML <form>`-Element enthalten, dessen `action`-Attribut so festgelegt ist, dass Beiträge an die URL `/Movies/Edit/id` gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="e7277-153">The `<input>` elements are in an `HTML <form>` element whose `action` attribute is set to post to the `/Movies/Edit/id` URL.</span></span> <span data-ttu-id="e7277-154">Die Formulardaten werden an den Server gesendet, wenn auf die Schaltfläche `Save` geklickt wird.</span><span class="sxs-lookup"><span data-stu-id="e7277-154">The form data will be posted to the server when the `Save` button is clicked.</span></span> <span data-ttu-id="e7277-155">Die letzte Zeile vor dem schließenden `</form>`-Element zeigt das ausgeblendete [XSRF](xref:security/anti-request-forgery)-Token, das durch das [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e7277-155">The last line before the closing `</form>` element shows the hidden [XSRF](xref:security/anti-request-forgery) token generated by the [Form Tag Helper](xref:mvc/views/working-with-forms).</span></span>

## <a name="processing-the-post-request"></a><span data-ttu-id="e7277-156">Verarbeiten der POST-Anforderung</span><span class="sxs-lookup"><span data-stu-id="e7277-156">Processing the POST Request</span></span>

<span data-ttu-id="e7277-157">Die folgende Liste zeigt die `[HttpPost]`-Version der `Edit`-Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="e7277-157">The following listing shows the `[HttpPost]` version of the `Edit` action method.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie21/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie/Controllers/MC1.cs?name=snippet_edit2)]

::: moniker-end

<span data-ttu-id="e7277-158">Das `[ValidateAntiForgeryToken]`-Attribut überprüft das ausgeblendete [XSRF](xref:security/anti-request-forgery)-Token, das vom Generator für Fälschungssicherheitstoken im [Hilfsprogramm für Formulartags](xref:mvc/views/working-with-forms) generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e7277-158">The `[ValidateAntiForgeryToken]` attribute validates the hidden [XSRF](xref:security/anti-request-forgery) token generated by the anti-forgery token generator in the [Form Tag Helper](xref:mvc/views/working-with-forms)</span></span>

<span data-ttu-id="e7277-159">Das [Modellbindungssystem](xref:mvc/models/model-binding) verwendet die übermittelten Formularwerte und erstellt ein `Movie`-Objekt, das als `movie`-Parameter übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="e7277-159">The [model binding](xref:mvc/models/model-binding) system takes the posted form values and creates a `Movie` object that's passed as the `movie` parameter.</span></span> <span data-ttu-id="e7277-160">Die `ModelState.IsValid`-Eigenschaft stellt sicher, dass die im Formular übermittelten Daten verwendet werden können, um ein `Movie`-Objekt zu ändern (zu bearbeiten oder zu aktualisieren).</span><span class="sxs-lookup"><span data-stu-id="e7277-160">The `ModelState.IsValid` property verifies that the data submitted in the form can be used to modify (edit or update) a `Movie` object.</span></span> <span data-ttu-id="e7277-161">Wenn die Daten gültig sind, werden sie gespeichert.</span><span class="sxs-lookup"><span data-stu-id="e7277-161">If the data is valid, it's saved.</span></span> <span data-ttu-id="e7277-162">Die aktualisierten (bearbeiteten) Filmdaten werden in der Datenbank gespeichert, indem die `SaveChangesAsync`-Methode des Datenbankkontexts aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e7277-162">The updated (edited) movie data is saved to the database by calling the `SaveChangesAsync` method of database context.</span></span> <span data-ttu-id="e7277-163">Nach dem Speichern der Daten leitet der Code den Benutzer an die `Index`-Aktionsmethode der `MoviesController`-Klasse weiter, die die Filmsammlung einschließlich der gerade vorgenommenen Änderungen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="e7277-163">After saving the data, the code redirects the user to the `Index` action method of the `MoviesController` class, which displays the movie collection, including the changes just made.</span></span>

<span data-ttu-id="e7277-164">Bevor das Formular an den Server gesendet wird, überprüft die clientseitige Validierung alle Validierungsregeln der Felder.</span><span class="sxs-lookup"><span data-stu-id="e7277-164">Before the form is posted to the server, client-side validation checks any validation rules on the fields.</span></span> <span data-ttu-id="e7277-165">Sind Validierungsfehler vorhanden, wird eine Fehlermeldung angezeigt, und das Formular wird nicht gesendet.</span><span class="sxs-lookup"><span data-stu-id="e7277-165">If there are any validation errors, an error message is displayed and the form isn't posted.</span></span> <span data-ttu-id="e7277-166">Wenn JavaScript deaktiviert ist, erfolgt keine clientseitige Validierung. Der Server erkennt jedoch, dass die bereitgestellten Werte nicht gültig sind, und die Formularwerte werden wieder mit Fehlermeldungen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e7277-166">If JavaScript is disabled, you won't have client-side validation but the server will detect the posted values that are not valid, and the form values will be redisplayed with error messages.</span></span> <span data-ttu-id="e7277-167">Später in diesem Tutorial untersuchen wir die [Modellvalidierung](xref:mvc/models/validation) im Detail.</span><span class="sxs-lookup"><span data-stu-id="e7277-167">Later in the tutorial we examine [Model Validation](xref:mvc/models/validation) in more detail.</span></span> <span data-ttu-id="e7277-168">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms) in der Ansichtsvorlage *Views/Movies/Edit.cshtml* übernimmt die Anzeige der entsprechenden Fehlermeldungen.</span><span class="sxs-lookup"><span data-stu-id="e7277-168">The [Validation Tag Helper](xref:mvc/views/working-with-forms) in the *Views/Movies/Edit.cshtml* view template takes care of displaying appropriate error messages.</span></span>

![Ansicht bearbeiten: Eine Ausnahme für einen falschen Wert für den Preis von „abc“ gibt an, dass das Feld für den Preis eine Zahl sein muss.](~/tutorials/first-mvc-app/controller-methods-views/_static/val.png)

<span data-ttu-id="e7277-171">Alle `HttpGet`-Methoden im Movie-Controller folgen einem ähnlichen Muster.</span><span class="sxs-lookup"><span data-stu-id="e7277-171">All the `HttpGet` methods in the movie controller follow a similar pattern.</span></span> <span data-ttu-id="e7277-172">Sie erhalten ein Movie-Objekt (oder eine Liste von Objekten bei `Index`), und übergeben das Objekt (Modell) an die Ansicht.</span><span class="sxs-lookup"><span data-stu-id="e7277-172">They get a movie object (or list of objects, in the case of `Index`), and pass the object (model) to the view.</span></span> <span data-ttu-id="e7277-173">Die `Create`-Methode übergibt ein leeres Movie-Objekt an die Ansicht `Create`.</span><span class="sxs-lookup"><span data-stu-id="e7277-173">The `Create` method passes an empty movie object to the `Create` view.</span></span> <span data-ttu-id="e7277-174">Alle Methoden, die Daten erstellen, bearbeiten, löschen oder in beliebiger Weise ändern, nutzen dazu die `[HttpPost]`-Überladung der Methode.</span><span class="sxs-lookup"><span data-stu-id="e7277-174">All the methods that create, edit, delete, or otherwise modify data do so in the `[HttpPost]` overload of the method.</span></span> <span data-ttu-id="e7277-175">Das Ändern von Daten in einer `HTTP GET`-Methode ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="e7277-175">Modifying data in an `HTTP GET` method is a security risk.</span></span> <span data-ttu-id="e7277-176">Das Ändern von Daten in einer `HTTP GET`-Methode verstößt auch gegen bewährte HTTP-Methoden und das architektonische [REST](http://rest.elkstein.org/)-Muster, das angibt, dass die GET-Anforderungen nicht den Status Ihrer Anwendung ändern dürfen.</span><span class="sxs-lookup"><span data-stu-id="e7277-176">Modifying data in an `HTTP GET` method also violates HTTP best practices and the architectural [REST](http://rest.elkstein.org/) pattern, which specifies that GET requests shouldn't change the state of your application.</span></span> <span data-ttu-id="e7277-177">Die Durchführung eines GET-Vorgangs sollte also eine sichere Operation sein, die keine Nebenwirkungen hat und die permanenten Daten nicht ändert.</span><span class="sxs-lookup"><span data-stu-id="e7277-177">In other words, performing a GET operation should be a safe operation that has no side effects and doesn't modify your persisted data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e7277-178">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e7277-178">Additional resources</span></span>

* [<span data-ttu-id="e7277-179">Globalisierung und Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="e7277-179">Globalization and localization</span></span>](xref:fundamentals/localization)
* [<span data-ttu-id="e7277-180">Einführung in Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="e7277-180">Introduction to Tag Helpers</span></span>](xref:mvc/views/tag-helpers/intro)
* [<span data-ttu-id="e7277-181">Erstellen von Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="e7277-181">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* <xref:security/anti-request-forgery>
* <span data-ttu-id="e7277-182">Schützen Sie Ihre Domänencontroller vor [zu vielen Angaben](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span><span class="sxs-lookup"><span data-stu-id="e7277-182">Protect your controller from [over-posting](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application)</span></span>
* [<span data-ttu-id="e7277-183">ViewModels</span><span class="sxs-lookup"><span data-stu-id="e7277-183">ViewModels</span></span>](https://rachelappel.com/use-viewmodels-to-manage-data-amp-organize-code-in-asp-net-mvc-applications/)
* [<span data-ttu-id="e7277-184">Hilfsprogramm für Formulartags</span><span class="sxs-lookup"><span data-stu-id="e7277-184">Form Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="e7277-185">Hilfsprogramm für Eingabetags</span><span class="sxs-lookup"><span data-stu-id="e7277-185">Input Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="e7277-186">Hilfsprogramm für Bezeichnungstags</span><span class="sxs-lookup"><span data-stu-id="e7277-186">Label Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="e7277-187">Hilfsprogramm für Auswahltags</span><span class="sxs-lookup"><span data-stu-id="e7277-187">Select Tag Helper</span></span>](xref:mvc/views/working-with-forms)
* [<span data-ttu-id="e7277-188">Hilfsprogramm für Validierungstags</span><span class="sxs-lookup"><span data-stu-id="e7277-188">Validation Tag Helper</span></span>](xref:mvc/views/working-with-forms)

> [!div class="step-by-step"]
> <span data-ttu-id="e7277-189">[Zurück](working-with-sql.md)
> [Weiter](search.md)</span><span class="sxs-lookup"><span data-stu-id="e7277-189">[Previous](working-with-sql.md)
[Next](search.md)</span></span>  
