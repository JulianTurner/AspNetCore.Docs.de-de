---
title: Taghilfsprogramme in ASP.NET Core
author: rick-anderson
description: Informationen zu Taghilfsprogrammen und deren Verwendung in ASP.NET Core
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 03/18/2019
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
uid: mvc/views/tag-helpers/intro
ms.openlocfilehash: 781365d99c6d36d8abaec9681128ba712db8cb88
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060663"
---
# <a name="tag-helpers-in-aspnet-core"></a><span data-ttu-id="29db5-103">Taghilfsprogramme in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29db5-103">Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="29db5-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="29db5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="what-are-tag-helpers"></a><span data-ttu-id="29db5-105">Informationen zu Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="29db5-105">What are Tag Helpers</span></span>

<span data-ttu-id="29db5-106">Taghilfsprogramme ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in :::no-loc(Razor):::-Dateien.</span><span class="sxs-lookup"><span data-stu-id="29db5-106">Tag Helpers enable server-side code to participate in creating and rendering HTML elements in :::no-loc(Razor)::: files.</span></span> <span data-ttu-id="29db5-107">Beispielsweise kann der integrierte `ImageTagHelper` eine Versionsnummer an den Bildnamen anfügen.</span><span class="sxs-lookup"><span data-stu-id="29db5-107">For example, the built-in `ImageTagHelper` can append a version number to the image name.</span></span> <span data-ttu-id="29db5-108">Bei jeder Änderung des Bilds generiert der Server eine neue eindeutige Version des Bilds, sodass Clients immer das aktuelle Bild (anstelle eines veralteten zwischengespeicherten Bilds) erhalten.</span><span class="sxs-lookup"><span data-stu-id="29db5-108">Whenever the image changes, the server generates a new unique version for the image, so clients are guaranteed to get the current image (instead of a stale cached image).</span></span> <span data-ttu-id="29db5-109">Für häufige Aufgaben wie das Erstellen von Formularen und Links sowie das Laden von Objekten gibt es zahlreiche integrierte Taghilfsprogramme. Weitere Taghilfsprogramme sind in öffentlichen GitHub-Repositorys und als NuGet-Pakete verfügbar.</span><span class="sxs-lookup"><span data-stu-id="29db5-109">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="29db5-110">Taghilfsprogramme werden in C# erstellt und sind für HTML-Elemente basierend auf dem Elementnamen, dem Attributnamen oder dem übergeordneten Tag konzipiert.</span><span class="sxs-lookup"><span data-stu-id="29db5-110">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="29db5-111">Beispielsweise kann der integrierte `LabelTagHelper` für das HTML-`<label>`-Element verwendet werden, wenn die `LabelTagHelper`-Attribute angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="29db5-111">For example, the built-in `LabelTagHelper` can target the HTML `<label>` element when the `LabelTagHelper` attributes are applied.</span></span> <span data-ttu-id="29db5-112">Wenn Sie mit [HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers)-Hilfsprogrammen vertraut sind, verringern taghilfsprogramme die expliziten Übergänge zwischen HTML und c# in :::no-loc(Razor)::: Sichten.</span><span class="sxs-lookup"><span data-stu-id="29db5-112">If you're familiar with [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers), Tag Helpers reduce the explicit transitions between HTML and C# in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="29db5-113">Häufig stellen HTML-Hilfsprogramme eine Alternative zu einem bestimmten Taghilfsprogramm dar. Allerdings ersetzen Taghilfsprogramme HTML-Hilfsprogramme nicht, und es gibt nicht für jedes HTML-Hilfsprogramm ein Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="29db5-113">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="29db5-114">Im Abschnitt [Taghilfsprogramme und HTML-Hilfsprogramme im Vergleich](#tag-helpers-compared-to-html-helpers) werden die Unterschiede detaillierter erläutert.</span><span class="sxs-lookup"><span data-stu-id="29db5-114">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span>

## <a name="what-tag-helpers-provide"></a><span data-ttu-id="29db5-115">Vorteile eines Taghilfsprogramms</span><span class="sxs-lookup"><span data-stu-id="29db5-115">What Tag Helpers provide</span></span>

<span data-ttu-id="29db5-116">**HTML-freundliche Entwicklungs Benutzerfreundlichkeit** Zum größten Teil :::no-loc(Razor)::: sieht Markup mit taghilfsprogrammen wie Standard-HTML aus.</span><span class="sxs-lookup"><span data-stu-id="29db5-116">**An HTML-friendly development experience** For the most part, :::no-loc(Razor)::: markup using Tag Helpers looks like standard HTML.</span></span> <span data-ttu-id="29db5-117">Front-End-Designer, die mit HTML/CSS/JavaScript vertraut sind, können :::no-loc(Razor)::: ohne Erlernen der c#- :::no-loc(Razor)::: Syntax bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="29db5-117">Front-end designers conversant with HTML/CSS/JavaScript can edit :::no-loc(Razor)::: without learning C# :::no-loc(Razor)::: syntax.</span></span>

<span data-ttu-id="29db5-118">**Eine umfangreiche IntelliSense-Umgebung zum Erstellen von HTML-und :::no-loc(Razor)::: Markup-** Funktionen ist ein starker Kontrast zu HTML-Hilfsprogrammen, der vorherige Ansatz für die serverseitige Erstellung von Markup in :::no-loc(Razor)::: Ansichten.</span><span class="sxs-lookup"><span data-stu-id="29db5-118">**A rich IntelliSense environment for creating HTML and :::no-loc(Razor)::: markup** This is in sharp contrast to HTML Helpers, the previous approach to server-side creation of markup in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="29db5-119">Im Abschnitt [Taghilfsprogramme und HTML-Hilfsprogramme im Vergleich](#tag-helpers-compared-to-html-helpers) werden die Unterschiede detaillierter erläutert.</span><span class="sxs-lookup"><span data-stu-id="29db5-119">[Tag Helpers compared to HTML Helpers](#tag-helpers-compared-to-html-helpers) explains the differences in more detail.</span></span> <span data-ttu-id="29db5-120">Im Abschnitt [IntelliSense-Unterstützung für Taghilfsprogramme](#intellisense-support-for-tag-helpers) wird die IntelliSense-Umgebung beschrieben.</span><span class="sxs-lookup"><span data-stu-id="29db5-120">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) explains the IntelliSense environment.</span></span> <span data-ttu-id="29db5-121">Sogar Entwickler, :::no-loc(Razor)::: die die c#-Syntax kennen, sind mit taghilfsprogrammen produktiver als das Schreiben von c#- :::no-loc(Razor)::: Markup.</span><span class="sxs-lookup"><span data-stu-id="29db5-121">Even developers experienced with :::no-loc(Razor)::: C# syntax are more productive using Tag Helpers than writing C# :::no-loc(Razor)::: markup.</span></span>

<span data-ttu-id="29db5-122">**Produktiveres Arbeiten und Erstellen von stabilerem, zuverlässigerem und verwaltbarem Code mithilfe von Informationen, die nur auf dem Server verfügbar sind** Beispielsweise galt in der Vergangenheit für das Aktualisieren von Bildern, dass auch der Name des Bildes geändert werden muss, wenn das Bild geändert wurde.</span><span class="sxs-lookup"><span data-stu-id="29db5-122">**A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server** For example, historically the mantra on updating images was to change the name of the image when you change the image.</span></span> <span data-ttu-id="29db5-123">Bilder sollten zur Verbesserung der Leistung immer zwischengespeichert werden, denn wenn Sie nicht den Namen des Bildes ändern, kann es sein, dass Clients veraltete Kopien erhalten.</span><span class="sxs-lookup"><span data-stu-id="29db5-123">Images should be aggressively cached for performance reasons, and unless you change the name of an image, you risk clients getting a stale copy.</span></span> <span data-ttu-id="29db5-124">In der Vergangenheit musste der Name des Bildes immer geändert werden, wenn dieses bearbeitet wurde, und jeder Verweis auf das Bild in der Web-App musste aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="29db5-124">Historically, after an image was edited, the name had to be changed and each reference to the image in the web app needed to be updated.</span></span> <span data-ttu-id="29db5-125">Dies ist nicht nur sehr arbeitsintensiv, sondern auch fehleranfällig (Sie könnten einen Verweis übersehen, versehentlich die falsche Zeichenfolge eingeben usw.) Das integrierte `ImageTagHelper` kann dies automatisch für Sie ausführen.</span><span class="sxs-lookup"><span data-stu-id="29db5-125">Not only is this very labor intensive, it's also error prone (you could miss a reference, accidentally enter the wrong string, etc.) The built-in `ImageTagHelper` can do this for you automatically.</span></span> <span data-ttu-id="29db5-126">Das `ImageTagHelper`-Taghilfsprogramm kann eine Versionsnummer an den Bildnamen anfügen. Das bedeutet, dass der Server bei jeder Änderung eine neue eindeutige Version für das Bild generiert.</span><span class="sxs-lookup"><span data-stu-id="29db5-126">The `ImageTagHelper` can append a version number to the image name, so whenever the image changes, the server automatically generates a new unique version for the image.</span></span> <span data-ttu-id="29db5-127">Clients erhalten dann immer das aktuelle Bild.</span><span class="sxs-lookup"><span data-stu-id="29db5-127">Clients are guaranteed to get the current image.</span></span> <span data-ttu-id="29db5-128">Die Verwendung des `ImageTagHelper` ist grundsätzlich kostenlos, bietet mehr Stabilität, und Sie sparen Zeit.</span><span class="sxs-lookup"><span data-stu-id="29db5-128">This robustness and labor savings comes essentially free by using the `ImageTagHelper`.</span></span>

<span data-ttu-id="29db5-129">Die meisten integrierten Taghilfsprogramme sind für HTML-Standardelemente konzipiert und stellen serverseitige Attribute für die jeweiligen Elemente bereit.</span><span class="sxs-lookup"><span data-stu-id="29db5-129">Most built-in Tag Helpers target standard HTML elements and provide server-side attributes for the element.</span></span> <span data-ttu-id="29db5-130">Das `<input>`-Element, das in vielen Ansichten im Ordner *Views/Accounts* (Ansichten/Konten) verwendet wird, enthält beispielsweise das `asp-for`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="29db5-130">For example, the `<input>` element used in many views in the *Views/Account* folder contains the `asp-for` attribute.</span></span> <span data-ttu-id="29db5-131">Dieses Attribut extrahiert den Namen der angegebenen Modelleigenschaft, und fügt diesen in die gerenderte HTML-Seite ein.</span><span class="sxs-lookup"><span data-stu-id="29db5-131">This attribute extracts the name of the specified model property into the rendered HTML.</span></span> <span data-ttu-id="29db5-132">Betrachten Sie eine :::no-loc(Razor)::: Ansicht mit dem folgenden Modell:</span><span class="sxs-lookup"><span data-stu-id="29db5-132">Consider a :::no-loc(Razor)::: view with the following model:</span></span>

```csharp
public class Movie
{
    public int ID { get; set; }
    public string Title { get; set; }
    public DateTime ReleaseDate { get; set; }
    public string Genre { get; set; }
    public decimal Price { get; set; }
}
```

<span data-ttu-id="29db5-133">Das folgende :::no-loc(Razor)::: Markup:</span><span class="sxs-lookup"><span data-stu-id="29db5-133">The following :::no-loc(Razor)::: markup:</span></span>

```cshtml
<label asp-for="Movie.Title"></label>
```

<span data-ttu-id="29db5-134">wird der folgende HTML-Code generiert:</span><span class="sxs-lookup"><span data-stu-id="29db5-134">Generates the following HTML:</span></span>

```html
<label for="Movie_Title">Title</label>
```

<span data-ttu-id="29db5-135">Das `asp-for`-Attribut wird von der `For`-Eigenschaft von [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="29db5-135">The `asp-for` attribute is made available by the `For` property in the [LabelTagHelper](/dotnet/api/microsoft.aspnetcore.mvc.taghelpers.labeltaghelper?view=aspnetcore-2.0).</span></span> <span data-ttu-id="29db5-136">Weitere Informationen finden Sie unter [Erstellen von Taghilfsprogrammen](xref:mvc/views/tag-helpers/authoring).</span><span class="sxs-lookup"><span data-stu-id="29db5-136">See [Author Tag Helpers](xref:mvc/views/tag-helpers/authoring) for more information.</span></span>

## <a name="managing-tag-helper-scope"></a><span data-ttu-id="29db5-137">Verwalten des Taghilfsprogrammbereichs</span><span class="sxs-lookup"><span data-stu-id="29db5-137">Managing Tag Helper scope</span></span>

<span data-ttu-id="29db5-138">Der Taghilfsprogrammbereich wird über eine Kombination aus `@addTagHelper`, `@removeTagHelper` und dem Deaktivierungszeichen „!“ gesteuert.</span><span class="sxs-lookup"><span data-stu-id="29db5-138">Tag Helpers scope is controlled by a combination of `@addTagHelper`, `@removeTagHelper`, and the "!" opt-out character.</span></span>

<a name="add-helper-label"></a>

### <a name="addtaghelper-makes-tag-helpers-available"></a><span data-ttu-id="29db5-139">`@addTagHelper` stellt Taghilfsprogramme zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="29db5-139">`@addTagHelper` makes Tag Helpers available</span></span>

<span data-ttu-id="29db5-140">Wenn Sie eine neue ASP.NET Core-Web-App mit dem Namen *AuthoringTagHelpers* erstellen, wird die folgende *Views/_ViewImports.cshtml* -Datei Ihrem Projekt hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="29db5-140">If you create a new ASP.NET Core web app named *AuthoringTagHelpers* , the following *Views/_ViewImports.cshtml* file will be added to your project:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=2&range=2-3)]

<span data-ttu-id="29db5-141">Über die `@addTagHelper`-Anweisung werden Taghilfsprogramme in der Ansicht zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="29db5-141">The `@addTagHelper` directive makes Tag Helpers available to the view.</span></span> <span data-ttu-id="29db5-142">In diesem Fall ist die Ansichts Datei *pages/_ViewImports. cshtml* , die standardmäßig von allen Dateien im Ordner " *pages* " und den Unterordnern geerbt wird. Bereitstellen von taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="29db5-142">In this case, the view file is *Pages/_ViewImports.cshtml* , which by default is inherited by all files in the *Pages* folder and subfolders; making Tag Helpers available.</span></span> <span data-ttu-id="29db5-143">Im obigen Code wird die Platzhalter Syntax (" \* ") verwendet, um anzugeben, dass alle taghilfsprogramme in der angegebenen Assembly ( *Microsoft. aspnetcore. MVC. taghilfsprogramme* ) für jede Ansichts Datei im Verzeichnis " *views* " oder Unterverzeichnis verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="29db5-143">The code above uses the wildcard syntax ("\*") to specify that all Tag Helpers in the specified assembly ( *Microsoft.AspNetCore.Mvc.TagHelpers* ) will be available to every view file in the *Views* directory or subdirectory.</span></span> <span data-ttu-id="29db5-144">Über den ersten Parameter nach `@addTagHelper` wird das Taghilfsprogramm geladen („\*“ wird für alle Taghilfsprogramme verwendet), und über den zweiten Parameter „Microsoft.AspNetCore.Mvc.TagHelpers“ wird die Assembly angegeben, die die Taghilfsprogramme enthält.</span><span class="sxs-lookup"><span data-stu-id="29db5-144">The first parameter after `@addTagHelper` specifies the Tag Helpers to load (we are using "\*" for all Tag Helpers), and the second parameter "Microsoft.AspNetCore.Mvc.TagHelpers" specifies the assembly containing the Tag Helpers.</span></span> <span data-ttu-id="29db5-145">Bei *Microsoft.AspNetCore.Mvc.TagHelpers* handelt es sich um die Assembly für die integrierten ASP.NET Core-Taghilfsprogramme.</span><span class="sxs-lookup"><span data-stu-id="29db5-145">*Microsoft.AspNetCore.Mvc.TagHelpers* is the assembly for the built-in ASP.NET Core Tag Helpers.</span></span>

<span data-ttu-id="29db5-146">Verwenden Sie folgenden Code, wenn Sie alle Taghilfsprogramme in diesem Projekt zur Verfügung stellen wollen. Dadurch wird eine Assembly mit dem Namen *AuthoringTagHelpers* erstellt:</span><span class="sxs-lookup"><span data-stu-id="29db5-146">To expose all of the Tag Helpers in this project (which creates an assembly named *AuthoringTagHelpers* ), you would use the following:</span></span>

[!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopy.cshtml?highlight=3)]

<span data-ttu-id="29db5-147">Wenn Ihr Projekt ein `EmailTagHelper`-Taghilfsprogramm mit einem Standardnamespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) verwendet, können Sie den vollqualifizierten Namen Ihres Taghilfsprogramms zur Verfügung stellen:</span><span class="sxs-lookup"><span data-stu-id="29db5-147">If your project contains an `EmailTagHelper` with the default namespace (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), you can provide the fully qualified name (FQN) of the Tag Helper:</span></span>

```cshtml
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<span data-ttu-id="29db5-148">Wenn Sie einer Ansicht über einen vollqualifizierten Namen ein Taghilfsprogramm hinzufügen möchten, müssen Sie zunächst diesen Namen (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) und dann den Assemblynamen ( *AuthoringTagHelpers* ) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="29db5-148">To add a Tag Helper to a view using an FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the assembly name ( *AuthoringTagHelpers* ).</span></span> <span data-ttu-id="29db5-149">Die meisten Entwickler verwenden am liebsten die Platzhaltersyntax („\*“).</span><span class="sxs-lookup"><span data-stu-id="29db5-149">Most developers prefer to use the  "\*" wildcard syntax.</span></span> <span data-ttu-id="29db5-150">Mithilfe der Platzhaltersyntax können Sie das Platzhalterzeichen „\*“ als Suffix in einem vollqualifizierten Namen einfügen.</span><span class="sxs-lookup"><span data-stu-id="29db5-150">The wildcard syntax allows you to insert the wildcard character "\*" as the suffix in an FQN.</span></span> <span data-ttu-id="29db5-151">Beispielsweise können Sie über die folgenden Anweisungen das `EmailTagHelper`-Hilfsprogramm integrieren:</span><span class="sxs-lookup"><span data-stu-id="29db5-151">For example, any of the following directives will bring in the `EmailTagHelper`:</span></span>

```cshtml
@addTagHelper AuthoringTagHelpers.TagHelpers.E*, AuthoringTagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.Email*, AuthoringTagHelpers
```

<span data-ttu-id="29db5-152">Wie bereits erwähnt `@addTagHelper` *_ViewImports* , ist das taghilfsprogramm für alle Ansichts Dateien im Verzeichnis " *views* " und in den Unterverzeichnissen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="29db5-152">As mentioned previously, adding the `@addTagHelper` directive to the *Views/_ViewImports.cshtml* file makes the Tag Helper available to all view files in the *Views* directory and subdirectories.</span></span> <span data-ttu-id="29db5-153">Sie können die `@addTagHelper`-Anweisung in bestimmten Ansichtsdateien verwenden, wenn Sie festlegen möchten, dass das Taghilfsprogramm nur für diese Ansichten verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="29db5-153">You can use the `@addTagHelper` directive in specific view files if you want to opt-in to exposing the Tag Helper to only those views.</span></span>

<a name="remove-razor-directives-label"></a>

### <a name="removetaghelper-removes-tag-helpers"></a><span data-ttu-id="29db5-154">Entfernen von Taghilfsprogrammen über `@removeTagHelper`</span><span class="sxs-lookup"><span data-stu-id="29db5-154">`@removeTagHelper` removes Tag Helpers</span></span>

<span data-ttu-id="29db5-155">Das `@removeTagHelper`-Taghilfsprogramm enthält dieselben beiden Parameter wie `@addTagHelper` und entfernt ein bereits zuvor hinzugefügtes Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="29db5-155">The `@removeTagHelper` has the same two parameters as `@addTagHelper`, and it removes a Tag Helper that was previously added.</span></span> <span data-ttu-id="29db5-156">Wenn z.B. der `@removeTagHelper` auf eine bestimmte Ansicht angewendet wird, wird das angegebene Taghilfsprogramm aus der Ansicht entfernt.</span><span class="sxs-lookup"><span data-stu-id="29db5-156">For example, `@removeTagHelper` applied to a specific view removes the specified Tag Helper from the view.</span></span> <span data-ttu-id="29db5-157">Wenn Sie das `@removeTagHelper`-Taghilfsprogramm in einem *Views/Folder/_ViewImports.cshtml* -Ordner verwenden, wird das festgelegte Taghilfsprogramm aus allen Ansichten im *Ordner* entfernt.</span><span class="sxs-lookup"><span data-stu-id="29db5-157">Using `@removeTagHelper` in a *Views/Folder/_ViewImports.cshtml* file removes the specified Tag Helper from all of the views in *Folder* .</span></span>

### <a name="controlling-tag-helper-scope-with-the-_viewimportscshtml-file"></a><span data-ttu-id="29db5-158">Steuern des Taghilfsprogrammbereichs mit der *_ViewImports.cshtml* -Datei</span><span class="sxs-lookup"><span data-stu-id="29db5-158">Controlling Tag Helper scope with the *_ViewImports.cshtml* file</span></span>

<span data-ttu-id="29db5-159">Sie können jedem Ordner eine *_ViewImports.cshtml* -Datei hinzufügen, und die Ansichtsengine wendet die Anweisungen aus dieser Datei und der *Views/_ViewImports.cshtml* -Datei an.</span><span class="sxs-lookup"><span data-stu-id="29db5-159">You can add a *_ViewImports.cshtml* to any view folder, and the view engine applies the directives from both that file and the *Views/_ViewImports.cshtml* file.</span></span> <span data-ttu-id="29db5-160">Wenn Sie für die *Startseiten* -Ansicht eine leere *Views/Home/_ViewImports.cshtml* -Ansicht hinzufügen, ändert sich nichts, da die *_ViewImports.cshtml* -Datei nur einen Zusatz darstellt.</span><span class="sxs-lookup"><span data-stu-id="29db5-160">If you added an empty *Views/Home/_ViewImports.cshtml* file for the *Home* views, there would be no change because the *_ViewImports.cshtml* file is additive.</span></span> <span data-ttu-id="29db5-161">Alle `@addTagHelper`-Anweisungen, die Sie der *Views/Home/_ViewImports.cshtml* -Datei hinzufügen (die nicht in der Standarddatei *Views/_ViewImports.cshtml* enthalten sind) machen diese Taghilfsprogramme nur für Ansichten im *Basis* -Ordner verfügbar.</span><span class="sxs-lookup"><span data-stu-id="29db5-161">Any `@addTagHelper` directives you add to the *Views/Home/_ViewImports.cshtml* file (that are not in the default *Views/_ViewImports.cshtml* file) would expose those Tag Helpers to views only in the *Home* folder.</span></span>

<a name="opt-out"></a>

### <a name="opting-out-of-individual-elements"></a><span data-ttu-id="29db5-162">Deaktivieren individueller Elemente</span><span class="sxs-lookup"><span data-stu-id="29db5-162">Opting out of individual elements</span></span>

<span data-ttu-id="29db5-163">Sie können Taghilfsprogramme auf Elementebene über das Deaktivierungszeichen „!“ für Taghilfsprogramme deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="29db5-163">You can disable a Tag Helper at the element level with the Tag Helper opt-out character ("!").</span></span> <span data-ttu-id="29db5-164">Beispielsweise wird die `Email`-Validierung in `<span>` über dieses Zeichen deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="29db5-164">For example, `Email` validation is disabled in the `<span>` with the Tag Helper opt-out character:</span></span>

```cshtml
<!span asp-validation-for="Email" class="text-danger"></!span>
```

<span data-ttu-id="29db5-165">Sie müssen dieses Zeichen auf das Start- und das Endtag anwenden.</span><span class="sxs-lookup"><span data-stu-id="29db5-165">You must apply the Tag Helper opt-out character to the opening and closing tag.</span></span> <span data-ttu-id="29db5-166">(Der Visual Studio-Editor fügt das Zeichen automatisch dem Endtag hinzu, wenn Sie es im Starttag verwendet haben.)</span><span class="sxs-lookup"><span data-stu-id="29db5-166">(The Visual Studio editor automatically adds the opt-out character to the closing tag when you add one to the opening tag).</span></span> <span data-ttu-id="29db5-167">Sobald Sie das Deaktivierungszeichen hinzugefügt haben, werden das Element und die Taghilfsprogrammattribute nicht mehr in unterschiedlichen Schriftarten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-167">After you add the opt-out character, the element and Tag Helper attributes are no longer displayed in a distinctive font.</span></span>

<a name="prefix-razor-directives-label"></a>

### <a name="using-taghelperprefix-to-make-tag-helper-usage-explicit"></a><span data-ttu-id="29db5-168">Verwenden von `@tagHelperPrefix`, um die Verwendung von Taghilfsprogrammen erforderlich zu machen</span><span class="sxs-lookup"><span data-stu-id="29db5-168">Using `@tagHelperPrefix` to make Tag Helper usage explicit</span></span>

<span data-ttu-id="29db5-169">Mithilfe der `@tagHelperPrefix`-Anweisung können Sie ein Tagpräfix angeben, um Unterstützung für Taghilfsprogramme zu aktivieren und ihre Verwendung explizit erforderlich zu machen.</span><span class="sxs-lookup"><span data-stu-id="29db5-169">The `@tagHelperPrefix` directive allows you to specify a tag prefix string to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> <span data-ttu-id="29db5-170">Beispielsweise können Sie das folgende Markup zu der *Views/_ViewImports.cshtml* -Datei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="29db5-170">For example, you could add the following markup to the *Views/_ViewImports.cshtml* file:</span></span>

```cshtml
@tagHelperPrefix th:
```

<span data-ttu-id="29db5-171">Im nachfolgend Codebild ist das Präfix des Taghilfsprogramms auf `th:` festgelegt, sodass nur die Elemente, die das Präfix `th:` verwenden, Taghilfsprogramme unterstützen (Elemente, für die Taghilfsprogramme aktiviert sind, werden in einer anderen Schriftart dargestellt).</span><span class="sxs-lookup"><span data-stu-id="29db5-171">In the code image below, the Tag Helper prefix is set to `th:`, so only those elements using the prefix `th:` support Tag Helpers (Tag Helper-enabled elements have a distinctive font).</span></span> <span data-ttu-id="29db5-172">Die Elemente `<label>` und `<input>` enthalten das Präfix des Taghilfsprogramms. Für sie sind Taghilfsprogramme aktiviert, für das Element `<span>` hingegen nicht.</span><span class="sxs-lookup"><span data-stu-id="29db5-172">The `<label>` and `<input>` elements have the Tag Helper prefix and are Tag Helper-enabled, while the `<span>` element doesn't.</span></span>

![image](intro/_static/thp.png)

<span data-ttu-id="29db5-174">Für `@addTagHelper` gelten dieselben Hierarchieregeln wie für `@tagHelperPrefix`.</span><span class="sxs-lookup"><span data-stu-id="29db5-174">The same hierarchy rules that apply to `@addTagHelper` also apply to `@tagHelperPrefix`.</span></span>

## <a name="self-closing-tag-helpers"></a><span data-ttu-id="29db5-175">Taghilfsprogramme als selbstschließende Tags</span><span class="sxs-lookup"><span data-stu-id="29db5-175">Self-closing Tag Helpers</span></span>

<span data-ttu-id="29db5-176">Viele Taghilfsprogramme können nicht als selbstschließende Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="29db5-176">Many Tag Helpers can't be used as self-closing tags.</span></span> <span data-ttu-id="29db5-177">Einige Taghilfsprogramme sind als selbstschließende Tags konzipiert.</span><span class="sxs-lookup"><span data-stu-id="29db5-177">Some Tag Helpers are designed to be self-closing tags.</span></span> <span data-ttu-id="29db5-178">Wenn Sie ein Taghilfsprogramm verwenden, das nicht als selbstschließendes Tag konzipiert ist, wird die gerenderte Ausgabe unterdrückt.</span><span class="sxs-lookup"><span data-stu-id="29db5-178">Using a Tag Helper that was not designed to be self-closing suppresses the rendered output.</span></span> <span data-ttu-id="29db5-179">Wenn Sie ein als selbstschließendes Tag konzipiertes Taghilfsprogramm verwenden, werden selbstschließende Tags in der gerenderten Ausgabe verwendet.</span><span class="sxs-lookup"><span data-stu-id="29db5-179">Self-closing a Tag Helper results in a self-closing tag in the rendered output.</span></span> <span data-ttu-id="29db5-180">Weitere Informationen finden Sie unter [Erstellen von Taghilfsprogrammen in ASP.NET Core](xref:mvc/views/tag-helpers/authoring) in [diesem Hinweis](xref:mvc/views/tag-helpers/authoring#self-closing).</span><span class="sxs-lookup"><span data-stu-id="29db5-180">For more information, see [this note](xref:mvc/views/tag-helpers/authoring#self-closing) in [Authoring Tag Helpers](xref:mvc/views/tag-helpers/authoring).</span></span>

## <a name="c-in-tag-helpers-attributedeclaration"></a><span data-ttu-id="29db5-181">C# in Attributen/der Deklaration von Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="29db5-181">C# in Tag Helpers attribute/declaration</span></span> 

<span data-ttu-id="29db5-182">Taghilfsprogramme lassen C# im Attribut des Elements oder im Tagdeklarationsbereich nicht zu.</span><span class="sxs-lookup"><span data-stu-id="29db5-182">Tag Helpers do not allow C# in the element's attribute or tag declaration area.</span></span> <span data-ttu-id="29db5-183">Beispielsweise ist der folgende Code ungültig:</span><span class="sxs-lookup"><span data-stu-id="29db5-183">For example, the following code is not valid:</span></span>

```cshtml
<input asp-for="LastName"  
       @(Model?.LicenseId == null ? "disabled" : string.Empty) />
```

<span data-ttu-id="29db5-184">Der vorangehende Code kann wie folgt geschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="29db5-184">The preceding code can be written as:</span></span>

```cshtml
<input asp-for="LastName" 
       disabled="@(Model?.LicenseId == null)" />
```

## <a name="intellisense-support-for-tag-helpers"></a><span data-ttu-id="29db5-185">IntelliSense-Unterstützung für Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="29db5-185">IntelliSense support for Tag Helpers</span></span>

<span data-ttu-id="29db5-186">Wenn Sie eine neue ASP.net Core-Web-App in Visual Studio erstellen, wird das nuget-Paket "Microsoft. aspnetcore. :::no-loc(Razor)::: .. Tools ".</span><span class="sxs-lookup"><span data-stu-id="29db5-186">When you create a new ASP.NET Core web app in Visual Studio, it adds the NuGet package "Microsoft.AspNetCore.:::no-loc(Razor):::.Tools".</span></span> <span data-ttu-id="29db5-187">Dabei handelt es sich um das Paket, das Taghilfsprogramme hinzufügt.</span><span class="sxs-lookup"><span data-stu-id="29db5-187">This is the package that adds Tag Helper tooling.</span></span>

<span data-ttu-id="29db5-188">Sie sollten ein HTML-`<label>`-Element schreiben.</span><span class="sxs-lookup"><span data-stu-id="29db5-188">Consider writing an HTML `<label>` element.</span></span> <span data-ttu-id="29db5-189">Wenn Sie `<l` im Visual Studio-Editor eingeben, zeigt IntelliSense passende Elemente an:</span><span class="sxs-lookup"><span data-stu-id="29db5-189">As soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="29db5-191">Sie erhalten nicht nur Hilfe für HTML, sondern es wird auch das Symbol „@" symbol with "“ mit <> darunter angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-191">Not only do you get HTML help, but the icon (the "@" symbol with "<>" under it).</span></span>

![image](intro/_static/tagSym.png)

<span data-ttu-id="29db5-193">Erkennt das Element als für Taghilfsprogramme konzipiert an.</span><span class="sxs-lookup"><span data-stu-id="29db5-193">identifies the element as targeted by Tag Helpers.</span></span> <span data-ttu-id="29db5-194">Für reine HTML-Elemente wie `fieldset` wird das Symbol „<>“ angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-194">Pure HTML elements (such as the `fieldset`) display the "<>" icon.</span></span>

<span data-ttu-id="29db5-195">Ein reines HTML-`<label>`-Tag zeigt das HTML-Tag (im Standardfarbdesign von Visual Studio) in braun, die Attribute in rot und die Attributwerte in blau an.</span><span class="sxs-lookup"><span data-stu-id="29db5-195">A pure HTML `<label>` tag displays the HTML tag (with the default Visual Studio color theme) in a brown font, the attributes in red, and the attribute values in blue.</span></span>

![image](intro/_static/LableHtmlTag.png)

<span data-ttu-id="29db5-197">Wenn Sie `<label` eingeben, listet IntelliSense die verfügbaren HTML/CSS-Attribute und die für Taghilfsprogramme konzipierten Attribute auf:</span><span class="sxs-lookup"><span data-stu-id="29db5-197">After you enter `<label`, IntelliSense lists the available HTML/CSS attributes and the Tag Helper-targeted attributes:</span></span>

![image](intro/_static/labelattr.png)

<span data-ttu-id="29db5-199">Aufgrund der Anweisungsvervollständigung von IntelliSense können Sie die TAB-Taste drücken, um die Anweisung mit dem ausgewählten Wert zu vervollständigen:</span><span class="sxs-lookup"><span data-stu-id="29db5-199">IntelliSense statement completion allows you to enter the tab key to complete the statement with the selected value:</span></span>

![image](intro/_static/stmtcomplete.png)

<span data-ttu-id="29db5-201">Wenn ein Taghilfsprogrammattribut eingegeben wird, ändern sich die Schriftarten des Tags und des Attributs.</span><span class="sxs-lookup"><span data-stu-id="29db5-201">As soon as a Tag Helper attribute is entered, the tag and attribute fonts change.</span></span> <span data-ttu-id="29db5-202">Wenn Sie das Standardfarbdesign von Visual Studio verwenden („Blau“ oder „Hell“), wird die Schrift in dunkellila angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-202">Using the default Visual Studio "Blue" or "Light" color theme, the font is bold purple.</span></span> <span data-ttu-id="29db5-203">Wenn Sie das Design „Dunkel“ verwenden, wird die Schrift in einem dunklen blaugrün angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-203">If you're using the "Dark" theme the font is bold teal.</span></span> <span data-ttu-id="29db5-204">Für die in diesem Artikel dargestellten Bilder wurde das Standarddesign verwendet.</span><span class="sxs-lookup"><span data-stu-id="29db5-204">The images in this document were taken using the default theme.</span></span>

![image](intro/_static/labelaspfor2.png)

<span data-ttu-id="29db5-206">Sie können die Visual Studio-Verknüpfung *CompleteWord* verwenden ( [standardmäßig](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) STRG+LEERTASTE in doppelten Anführungszeichen (""), und jetzt befinden Sie sich genauso wie in einer C#-Klasse in C#).</span><span class="sxs-lookup"><span data-stu-id="29db5-206">You can enter the Visual Studio *CompleteWord* shortcut (Ctrl +spacebar is the [default](/visualstudio/ide/default-keyboard-shortcuts-in-visual-studio) inside the double quotes (""), and you are now in C#, just like you would be in a C# class.</span></span> <span data-ttu-id="29db5-207">IntelliSense zeigt alle Methoden und Eigenschaften auf dem Seitenmodell an.</span><span class="sxs-lookup"><span data-stu-id="29db5-207">IntelliSense displays all the methods and properties on the page model.</span></span> <span data-ttu-id="29db5-208">Die Methoden und Eigenschaften sind verfügbar, weil der Eigenschaftentyp `ModelExpression` ist.</span><span class="sxs-lookup"><span data-stu-id="29db5-208">The methods and properties are available because the property type is `ModelExpression`.</span></span> <span data-ttu-id="29db5-209">Im nachfolgenden Beispiel wird die `Register`-Ansicht bearbeitet, damit das `RegisterViewModel` verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="29db5-209">In the image below, I'm editing the `Register` view, so the `RegisterViewModel` is available.</span></span>

![image](intro/_static/intellemail.png)

<span data-ttu-id="29db5-211">IntelliSense listet die Eigenschaften und Methoden auf, die für das Modell auf der Seite verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="29db5-211">IntelliSense lists the properties and methods available to the model on the page.</span></span> <span data-ttu-id="29db5-212">Mithilfe der umfassenden IntelliSense-Umgebung können Sie die CSS-Klasse auswählen:</span><span class="sxs-lookup"><span data-stu-id="29db5-212">The rich IntelliSense environment helps you select the CSS class:</span></span>

![image](intro/_static/iclass.png)

![image](intro/_static/intel3.png)

## <a name="tag-helpers-compared-to-html-helpers"></a><span data-ttu-id="29db5-215">Taghilfsprogramme und HTML-Hilfsprogramme im Vergleich</span><span class="sxs-lookup"><span data-stu-id="29db5-215">Tag Helpers compared to HTML Helpers</span></span>

<span data-ttu-id="29db5-216">Taghilfsprogramme werden an HTML-Elemente in :::no-loc(Razor)::: Sichten angehängt, während [HTML](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) -Hilfsprogramme als Methoden aufgerufen werden, die mit HTML in Sichten kombinierten sind :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="29db5-216">Tag Helpers attach to HTML elements in :::no-loc(Razor)::: views, while [HTML Helpers](https://stephenwalther.com/archive/2009/03/03/chapter-6-understanding-html-helpers) are invoked as methods interspersed with HTML in :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="29db5-217">Beachten Sie das folgende :::no-loc(Razor)::: Markup, das eine HTML-Bezeichnung mit der CSS-Klasse "Caption" erstellt:</span><span class="sxs-lookup"><span data-stu-id="29db5-217">Consider the following :::no-loc(Razor)::: markup, which creates an HTML label with the CSS class "caption":</span></span>

```cshtml
@Html.Label("FirstName", "First Name:", new {@class="caption"})
```

<span data-ttu-id="29db5-218">Das at ( `@` )-Symbol weist darauf hin, dass :::no-loc(Razor)::: es sich um den Anfang des Codes handelt</span><span class="sxs-lookup"><span data-stu-id="29db5-218">The at (`@`) symbol tells :::no-loc(Razor)::: this is the start of code.</span></span> <span data-ttu-id="29db5-219">Bei den nächsten beiden Parametern („FirstName“ und „First Name:“) handelt es sich um Zeichenfolgen. Daher kann [IntelliSense](/visualstudio/ide/using-intellisense) nicht helfen.</span><span class="sxs-lookup"><span data-stu-id="29db5-219">The next two parameters ("FirstName" and "First Name:") are strings, so [IntelliSense](/visualstudio/ide/using-intellisense) can't help.</span></span> <span data-ttu-id="29db5-220">Das letzte Argument:</span><span class="sxs-lookup"><span data-stu-id="29db5-220">The last argument:</span></span>

```cshtml
new {@class="caption"}
```

<span data-ttu-id="29db5-221">Dabei handelt es sich um ein anonymes Objekt, das verwendet wird, um Attribute darzustellen.</span><span class="sxs-lookup"><span data-stu-id="29db5-221">Is an anonymous object used to represent attributes.</span></span> <span data-ttu-id="29db5-222">Da es sich bei `class` um ein reserviertes Schlüsselwort in C# handelt, sollten Sie das `@`-Symbol verwenden, um C# zu zwingen, `@class=` als Symbol (Eigenschaftenname) zu interpretieren.</span><span class="sxs-lookup"><span data-stu-id="29db5-222">Because `class` is a reserved keyword in C#, you use the `@` symbol to force C# to interpret `@class=` as a symbol (property name).</span></span> <span data-ttu-id="29db5-223">An einen Front-End-Designer (jemand, der mit HTML/CSS/JavaScript und anderen Client Technologien vertraut ist, aber nicht mit c# und vertraut :::no-loc(Razor)::: ist), ist der größte Teil der Zeile fremd.</span><span class="sxs-lookup"><span data-stu-id="29db5-223">To a front-end designer (someone familiar with HTML/CSS/JavaScript and other client technologies but not familiar with C# and :::no-loc(Razor):::), most of the line is foreign.</span></span> <span data-ttu-id="29db5-224">Die gesamte Zeile muss ohne Hilfe von IntelliSense erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="29db5-224">The entire line must be authored with no help from IntelliSense.</span></span>

<span data-ttu-id="29db5-225">Wenn Sie das `LabelTagHelper`-Taghilfsprogramm verwenden, kann dasselbe Markup wie folgt geschrieben sein:</span><span class="sxs-lookup"><span data-stu-id="29db5-225">Using the `LabelTagHelper`, the same markup can be written as:</span></span>

```cshtml
<label class="caption" asp-for="FirstName"></label>
```

<span data-ttu-id="29db5-226">Wenn Sie die Taghilfsprogrammversion verwenden und `<l` im Visual Studio-Editor eingeben, zeigt IntelliSense passende Elemente an:</span><span class="sxs-lookup"><span data-stu-id="29db5-226">With the Tag Helper version, as soon as you enter `<l` in the Visual Studio editor, IntelliSense displays matching elements:</span></span>

![image](intro/_static/label.png)

<span data-ttu-id="29db5-228">Mithilfe von IntelliSense können Sie die gesamte Zeile schreiben.</span><span class="sxs-lookup"><span data-stu-id="29db5-228">IntelliSense helps you write the entire line.</span></span>

<span data-ttu-id="29db5-229">Die folgende Codeabbildung zeigt den Formular Teil der Ansicht *views/Account/Register. cshtml* , der :::no-loc(Razor)::: aus der in Visual Studio enthaltenen ASP.NET 4.5. x-MVC-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="29db5-229">The following code image shows the Form portion of the *Views/Account/Register.cshtml* :::no-loc(Razor)::: view generated from the ASP.NET 4.5.x MVC template included with Visual Studio.</span></span>

![image](intro/_static/regCS.png)

<span data-ttu-id="29db5-231">Der Visual Studio-Editor zeigt C#-Code vor grauem Hintergrund an.</span><span class="sxs-lookup"><span data-stu-id="29db5-231">The Visual Studio editor displays C# code with a grey background.</span></span> <span data-ttu-id="29db5-232">Z.B. wird das `AntiForgeryToken`-HTML-Hilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="29db5-232">For example, the `AntiForgeryToken` HTML Helper:</span></span>

```cshtml
@Html.AntiForgeryToken()
```

<span data-ttu-id="29db5-233">vor grauem Hintergrund angezeigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-233">is displayed with a grey background.</span></span> <span data-ttu-id="29db5-234">Ein Großteil des Markups in der Registeransicht ist in C# geschrieben.</span><span class="sxs-lookup"><span data-stu-id="29db5-234">Most of the markup in the Register view is C#.</span></span> <span data-ttu-id="29db5-235">Zum Vergleich wird in der folgenden Abbildung der entsprechende Ansatz unter Verwendung von Taghilfsprogrammen dargestellt:</span><span class="sxs-lookup"><span data-stu-id="29db5-235">Compare that to the equivalent approach using Tag Helpers:</span></span>

![image](intro/_static/regTH.png)

<span data-ttu-id="29db5-237">Das Markup ist viel deutlicher und kann einfacher gelesen, bearbeitet und verwaltet werden als im Ansatz über das HTML-Hilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="29db5-237">The markup is much cleaner and easier to read, edit, and maintain than the HTML Helpers approach.</span></span> <span data-ttu-id="29db5-238">Der C#-Code ist auf die mindestens erforderlichen Informationen begrenzt, die der Server benötigt.</span><span class="sxs-lookup"><span data-stu-id="29db5-238">The C# code is reduced to the minimum that the server needs to know about.</span></span> <span data-ttu-id="29db5-239">Der Visual Studio-Editor zeigt Markup an, das von einem Taghilfsprogramm in einer anderen Schriftart angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="29db5-239">The Visual Studio editor displays markup targeted by a Tag Helper in a distinctive font.</span></span>

<span data-ttu-id="29db5-240">Sehen Sie sich die *Email* -Gruppe an:</span><span class="sxs-lookup"><span data-stu-id="29db5-240">Consider the *Email* group:</span></span>

[!code-cshtml[](intro/sample/Register.cshtml?range=12-18)]

<span data-ttu-id="29db5-241">Alle asp-Attribute enthalten den Wert „Email“. „Email“ ist allerdings keine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="29db5-241">Each of the "asp-" attributes has a value of "Email", but "Email" isn't a string.</span></span> <span data-ttu-id="29db5-242">In diesem Kontext ist „Email“ die C#-Modellausdruckseigenschaft für das `RegisterViewModel`.</span><span class="sxs-lookup"><span data-stu-id="29db5-242">In this context, "Email" is the C# model expression property for the `RegisterViewModel`.</span></span>

<span data-ttu-id="29db5-243">Mithilfe des Visual Studio-Editors können Sie das **gesamte** Markup im Taghilfsprogrammansatz des Registerformulars schreiben. Visual Studio stellt hingegen für einen Großteil des Codes im HTML-Hilfsprogrammansatz keine Hilfe zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="29db5-243">The Visual Studio editor helps you write **all** of the markup in the Tag Helper approach of the register form, while Visual Studio provides no help for most of the code in the HTML Helpers approach.</span></span> <span data-ttu-id="29db5-244">Im Abschnitt [IntelliSense-Unterstützung für Tag-Hilfsprogramme](#intellisense-support-for-tag-helpers) finden Sie mehr Details zum Arbeiten mit Taghilfsprogrammen im Visual Studio-Editor.</span><span class="sxs-lookup"><span data-stu-id="29db5-244">[IntelliSense support for Tag Helpers](#intellisense-support-for-tag-helpers) goes into detail on working with Tag Helpers in the Visual Studio editor.</span></span>

## <a name="tag-helpers-compared-to-web-server-controls"></a><span data-ttu-id="29db5-245">Taghilfsprogramm zu Webserversteuerelementen im Vergleich</span><span class="sxs-lookup"><span data-stu-id="29db5-245">Tag Helpers compared to Web Server Controls</span></span>

* <span data-ttu-id="29db5-246">Taghilfsprogramme besitzen das Element nicht, dem sie zugeordnet sind. Stattdessen sind sie nur Teil des Rendervorgangs des Elements und des Inhalts.</span><span class="sxs-lookup"><span data-stu-id="29db5-246">Tag Helpers don't own the element they're associated with; they simply participate in the rendering of the element and content.</span></span> <span data-ttu-id="29db5-247">ASP.net <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> werden deklariert und auf einer Seite aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="29db5-247">ASP.NET <https://docs.microsoft.com/previous-versions/dotnet/netframework-3.0/7698y1f0(v=vs.85)> are declared and invoked on a page.</span></span>

* <span data-ttu-id="29db5-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> haben Sie einen nicht trivialen Lebenszyklus, der die Entwicklung und das Debuggen erschweren kann.</span><span class="sxs-lookup"><span data-stu-id="29db5-248"><https://docs.microsoft.com/previous-versions/zsyt68f1(v=vs.140)> have a non-trivial lifecycle that can make developing and debugging difficult.</span></span>

* <span data-ttu-id="29db5-249">Mithilfe von Webserversteuerelementen können Sie Funktionen zu den Dokumentobjektmodellelementen des Clients über Clientsteuerelemente hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="29db5-249">Web Server controls allow you to add functionality to the client Document Object Model (DOM) elements by using a client control.</span></span> <span data-ttu-id="29db5-250">Taghilfsprogramme verfügen nicht über Dokumentobjektmodelle.</span><span class="sxs-lookup"><span data-stu-id="29db5-250">Tag Helpers have no DOM.</span></span>

* <span data-ttu-id="29db5-251">Webserversteuerelemente umfassen die Browsererkennung nicht.</span><span class="sxs-lookup"><span data-stu-id="29db5-251">Web Server controls include automatic browser detection.</span></span> <span data-ttu-id="29db5-252">Taghilfsprogramme haben keine Kenntnisse über den Browser.</span><span class="sxs-lookup"><span data-stu-id="29db5-252">Tag Helpers have no knowledge of the browser.</span></span>

* <span data-ttu-id="29db5-253">Mehrere Taghilfsprogramme können gleichzeitig auf dasselbe Element wirken (weitere Informationen finden Sie unter [Avoiding Tag Helper conflicts (Vermeiden von Konflikten mit Taghilfsprogrammen)](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts)). Sie können hingegen in der Regel keine Webserversteuerelemente erstellen.</span><span class="sxs-lookup"><span data-stu-id="29db5-253">Multiple Tag Helpers can act on the same element (see [Avoiding Tag Helper conflicts](xref:mvc/views/tag-helpers/authoring#avoid-tag-helper-conflicts) ) while you typically can't compose Web Server controls.</span></span>

* <span data-ttu-id="29db5-254">Taghilfsprogramme können das Tag und den Inhalt von HTML-Elementen verändern, dem sie zugeordnet sind. Ansonsten nehmen Sie keine Änderungen an der Seite vor.</span><span class="sxs-lookup"><span data-stu-id="29db5-254">Tag Helpers can modify the tag and content of HTML elements that they're scoped to, but don't directly modify anything else on a page.</span></span> <span data-ttu-id="29db5-255">Der Funktionsbereich von Webserversteuerelementen ist weniger spezifisch. Sie können Aktionen ausführen, die andere Teile Ihrer Seite beeinflussen, wodurch Nebenwirkungen entstehen, die nicht vorgesehen sind.</span><span class="sxs-lookup"><span data-stu-id="29db5-255">Web Server controls have a less specific scope and can perform actions that affect other parts of your page; enabling unintended side effects.</span></span>

* <span data-ttu-id="29db5-256">Webserversteuerelemente verwenden Typkonverter, um Zeichenfolgen in Objekte zu konvertieren.</span><span class="sxs-lookup"><span data-stu-id="29db5-256">Web Server controls use type converters to convert strings into objects.</span></span> <span data-ttu-id="29db5-257">Mit Taghilfsprogrammen arbeiten Sie auf native Weise in C#, weshalb Sie keine Typkonvertierung durchführen müssen.</span><span class="sxs-lookup"><span data-stu-id="29db5-257">With Tag Helpers, you work natively in C#, so you don't need to do type conversion.</span></span>

* <span data-ttu-id="29db5-258">Webserversteuerelemente verwenden [System.ComponentModel](/dotnet/api/system.componentmodel), um das Verhalten von Komponenten und Steuerelementen zur Laufzeit und Entwurfszeit zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="29db5-258">Web Server controls use [System.ComponentModel](/dotnet/api/system.componentmodel) to implement the run-time and design-time behavior of components and controls.</span></span> <span data-ttu-id="29db5-259">`System.ComponentModel` enthält die Basisklassen und Schnittstellen zum Implementieren von Attributen und Typkonvertern, die Datenquellen binden und Komponenten lizenzieren.</span><span class="sxs-lookup"><span data-stu-id="29db5-259">`System.ComponentModel` includes the base classes and interfaces for implementing attributes and type converters, binding to data sources, and licensing components.</span></span> <span data-ttu-id="29db5-260">Im Gegensatz dazu stehen Taghilfsprogramme, die in der Regel von `TagHelper` abgeleitet sind. Die `TagHelper`-Basisklasse stellt nur zwei Methoden zur Verfügung: `Process` und `ProcessAsync`.</span><span class="sxs-lookup"><span data-stu-id="29db5-260">Contrast that to Tag Helpers, which typically derive from `TagHelper`, and the `TagHelper` base class exposes only two methods, `Process` and `ProcessAsync`.</span></span>

## <a name="customizing-the-tag-helper-element-font"></a><span data-ttu-id="29db5-261">Anpassen der Elementschriftart des Taghilfsprogramms</span><span class="sxs-lookup"><span data-stu-id="29db5-261">Customizing the Tag Helper element font</span></span>

<span data-ttu-id="29db5-262">Sie können die Schriftart und die **Farbgebung unter Extras**  >  **Optionen**  >  **Umgebung**  >  **Schriftarten und Farben** anpassen:</span><span class="sxs-lookup"><span data-stu-id="29db5-262">You can customize the font and colorization from **Tools** > **Options** > **Environment** > **Fonts and Colors** :</span></span>

![image](intro/_static/fontoptions2.png)

[!INCLUDE[](~/includes/built-in-TH.md)]

## <a name="additional-resources"></a><span data-ttu-id="29db5-264">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="29db5-264">Additional resources</span></span>

* [<span data-ttu-id="29db5-265">Erstellen von Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="29db5-265">Author Tag Helpers</span></span>](xref:mvc/views/tag-helpers/authoring)
* [<span data-ttu-id="29db5-266">Arbeiten mit Formularen</span><span class="sxs-lookup"><span data-stu-id="29db5-266">Working with Forms</span></span>](xref:mvc/views/working-with-forms)
* <span data-ttu-id="29db5-267">Auf der Seite [Beispiele für Taghilfsprogramme unter GitHub](https://github.com/dpaquette/TagHelperSamples) finden Sie Beispiele für Taghilfsprogramme, die Sie für die Arbeit mit [Bootstrap](https://getbootstrap.com/) verwenden können.</span><span class="sxs-lookup"><span data-stu-id="29db5-267">[TagHelperSamples on GitHub](https://github.com/dpaquette/TagHelperSamples) contains Tag Helper samples for working with [Bootstrap](https://getbootstrap.com/).</span></span>
