---
title: Erstellen von Taghilfsprogrammen in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen von Taghilfsprogrammen in ASP.NET Core
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 306416db3d9ae0219f859c3cf459eb08a5b778cf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060923"
---
# <a name="author-tag-helpers-in-aspnet-core"></a><span data-ttu-id="64fe8-103">Erstellen von Taghilfsprogrammen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="64fe8-103">Author Tag Helpers in ASP.NET Core</span></span>

<span data-ttu-id="64fe8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64fe8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64fe8-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="64fe8-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started-with-tag-helpers"></a><span data-ttu-id="64fe8-106">Erste Schritte mit Taghilfsprogrammen</span><span class="sxs-lookup"><span data-stu-id="64fe8-106">Get started with Tag Helpers</span></span>

<span data-ttu-id="64fe8-107">Dieses Tutorial soll als Einführung in das Programmieren von Taghilfsprogrammen dienen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-107">This tutorial provides an introduction to programming Tag Helpers.</span></span> <span data-ttu-id="64fe8-108">Im Artikel [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](intro.md) werden die Vorteile von Taghilfsprogrammen beschrieben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-108">[Introduction to Tag Helpers](intro.md) describes the benefits that Tag Helpers provide.</span></span>

<span data-ttu-id="64fe8-109">Bei einem Taghilfsprogramm handelt es sich um eine Klasse, die die `ITagHelper`-Schnittstelle implementiert.</span><span class="sxs-lookup"><span data-stu-id="64fe8-109">A tag helper is any class that implements the `ITagHelper` interface.</span></span> <span data-ttu-id="64fe8-110">Wenn Sie hingegen ein Taghilfsprogramm erstellen, stellen Sie in der Regel eine Ableitung von `TagHelper` her, wodurch Sie auf die `Process`-Methode zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="64fe8-110">However, when you author a tag helper, you generally derive from `TagHelper`, doing so gives you access to the `Process` method.</span></span>

1. <span data-ttu-id="64fe8-111">Erstellen Sie ein neues ASP.NET Core-Projekt mit dem Namen **AuthoringTagHelpers** .</span><span class="sxs-lookup"><span data-stu-id="64fe8-111">Create a new ASP.NET Core project called **AuthoringTagHelpers** .</span></span> <span data-ttu-id="64fe8-112">Für dieses Projekt benötigen Sie keine Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="64fe8-112">You won't need authentication for this project.</span></span>

1. <span data-ttu-id="64fe8-113">Erstellen Sie einen Ordner mit dem Namen *TagHelpers* , in dem die Taghilfsprogramme gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-113">Create a folder to hold the Tag Helpers called *TagHelpers* .</span></span> <span data-ttu-id="64fe8-114">Dieser *TagHelpers* Ordner ist *nicht* zwingend erforderlich, allerdings handelt es sich dabei um eine praktische Konvention.</span><span class="sxs-lookup"><span data-stu-id="64fe8-114">The *TagHelpers* folder is *not* required, but it's a reasonable convention.</span></span> <span data-ttu-id="64fe8-115">Im Folgenden werden Beispiele zum Schreiben einfacher Taghilfsprogramme beschrieben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-115">Now let's get started writing some simple tag helpers.</span></span>

## <a name="a-minimal-tag-helper"></a><span data-ttu-id="64fe8-116">Ein Taghilfsprogramm mit den mindestens erforderlichen Elementen</span><span class="sxs-lookup"><span data-stu-id="64fe8-116">A minimal Tag Helper</span></span>

<span data-ttu-id="64fe8-117">In diesem Beispiel wird erläutert, wie Sie ein Taghilfsprogramm schreiben, das ein Update für ein E-Mail-Tag ausführt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-117">In this section, you write a tag helper that updates an email tag.</span></span> <span data-ttu-id="64fe8-118">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="64fe8-118">For example:</span></span>

```html
<email>Support</email>
```

<span data-ttu-id="64fe8-119">Der Server verwendet das Taghilfsprogramm für E-Mails, um dieses Markup in Folgendes zu konvertieren:</span><span class="sxs-lookup"><span data-stu-id="64fe8-119">The server will use our email tag helper to convert that markup into the following:</span></span>

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

<span data-ttu-id="64fe8-120">Dabei handelt es sich um ein Anchor-Tag, das daraus einen E-Mail-Link erstellt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-120">That is, an anchor tag that makes this an email link.</span></span> <span data-ttu-id="64fe8-121">Dieses Hilfsprogramm kann sich als nützlich erweisen, wenn Sie eine Blog-Engine schreiben und eine E-Mail an die Marketingabteilung, den Support oder andere Kontakte senden möchten, die alle derselben Domäne angehören.</span><span class="sxs-lookup"><span data-stu-id="64fe8-121">You might want to do this if you are writing a blog engine and need it to send email for marketing, support, and other contacts, all to the same domain.</span></span>

1. <span data-ttu-id="64fe8-122">Fügen Sie dem Ordner *TagHelpers* folgende `EmailTagHelper`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-122">Add the following `EmailTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * <span data-ttu-id="64fe8-123">Taghilfsprogramme verwenden Namenskonventionen, die auf die Elemente des Stammklassennamens ausgerichtet sind. Das Element *TagHelper* ist in dem Klassennamen allerdings nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="64fe8-123">Tag helpers use a naming convention that targets elements of the root class name (minus the *TagHelper* portion of the class name).</span></span> <span data-ttu-id="64fe8-124">In diesem Beispiel lautet der Stammname von \**EmailTagHelper\*\*\*email* , damit das `<email>`-Tag als Ziel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-124">In this example, the root name of **EmailTagHelper** is *email* , so the `<email>` tag will be targeted.</span></span> <span data-ttu-id="64fe8-125">Diese Namenskonvention sollte für die meisten Taghilfsprogramme funktionieren. Nachfolgend finden Sie eine Erläuterung dazu, wie Sie diese außer Kraft setzen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-125">This naming convention should work for most tag helpers, later on I'll show how to override it.</span></span>

   * <span data-ttu-id="64fe8-126">Die `EmailTagHelper`-Klasse wird von `TagHelper` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="64fe8-126">The `EmailTagHelper` class derives from `TagHelper`.</span></span> <span data-ttu-id="64fe8-127">Über die `TagHelper`-Klasse werden Methoden und Eigenschaften für das Schreiben von Taghilfsprogrammen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-127">The `TagHelper` class provides methods and properties for writing Tag Helpers.</span></span>

   * <span data-ttu-id="64fe8-128">Die überschriebene `Process`-Methode steuert die Funktionen des Taghilfsprogramms bei der Ausführung.</span><span class="sxs-lookup"><span data-stu-id="64fe8-128">The overridden `Process` method controls what the tag helper does when executed.</span></span> <span data-ttu-id="64fe8-129">Außerdem stellt die `TagHelper`-Klasse eine asynchrone Version (`ProcessAsync`) mit denselben Parametern bereit.</span><span class="sxs-lookup"><span data-stu-id="64fe8-129">The `TagHelper` class also provides an asynchronous version (`ProcessAsync`) with the same parameters.</span></span>

   * <span data-ttu-id="64fe8-130">Der Kontextparameter von `Process` (und `ProcessAsync`) enthält Informationen, die der Ausführung des aktuellen HTML-Tags zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-130">The context parameter to `Process` (and `ProcessAsync`) contains information associated with the execution of the current HTML tag.</span></span>

   * <span data-ttu-id="64fe8-131">Der Ausgabeparameter von `Process` (und `ProcessAsync`) enthält ein zustandsbehaftetes HTML-Element, das für die Originalquelle steht, die zum Generieren eines HTML-Tags und von Inhalten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-131">The output parameter to `Process` (and `ProcessAsync`) contains a stateful HTML element representative of the original source used to generate an HTML tag and content.</span></span>

   * <span data-ttu-id="64fe8-132">Der Klassenname in diesem Beispiel enthält ein **TagHelper** -Suffix, das *nicht* erforderlich ist, aber als bewährter Bestandteil angesehen wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-132">Our class name has a suffix of **TagHelper** , which is *not* required, but it's considered a best practice convention.</span></span> <span data-ttu-id="64fe8-133">Sie können die Klasse wie folgt deklarieren:</span><span class="sxs-lookup"><span data-stu-id="64fe8-133">You could declare the class as:</span></span>

   ```csharp
   public class Email : TagHelper
   ```

1. <span data-ttu-id="64fe8-134">`EmailTagHelper` Razor Fügen Sie der `addTagHelper` *views/_ViewImports. cshtml-* Datei die-Direktive hinzu, um die Klasse für alle Sichten verfügbar zu machen:</span><span class="sxs-lookup"><span data-stu-id="64fe8-134">To make the `EmailTagHelper` class available to all our Razor views, add the `addTagHelper` directive to the *Views/_ViewImports.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   <span data-ttu-id="64fe8-135">Im obenstehenden Code wird die Platzhaltersyntax verwendet, um alle Taghilfsprogramme anzugeben, die in der Assembly verfügbar sein sollen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-135">The code above uses the wildcard syntax to specify all the tag helpers in our assembly will be available.</span></span> <span data-ttu-id="64fe8-136">In der ersten Zeichenfolge nach `@addTagHelper` wird angegeben, welches Taghilfsprogramm geladen werden soll (Verwenden Sie für alle Taghilfsprogramme „\*“), und die zweite Zeichenfolge „AuthoringTagHelpers“ gibt die Assembly an, in der sich das Taghilfsprogramm befindet.</span><span class="sxs-lookup"><span data-stu-id="64fe8-136">The first string after `@addTagHelper` specifies the tag helper to load (Use "\*" for all tag helpers), and the second string "AuthoringTagHelpers" specifies the assembly the tag helper is in.</span></span> <span data-ttu-id="64fe8-137">Beachten Sie außerdem, dass die zweite Zeile die ASP.net Core MVC-taghilfsprogramme mithilfe der Platzhalter Syntax bringt (diese Hilfsprogramme werden unter [Einführung in taghilfsprogramme](intro.md)erläutert). Dies ist die- `@addTagHelper` Direktive, die das taghilfsprogramm für die Ansicht verfügbar macht Razor .</span><span class="sxs-lookup"><span data-stu-id="64fe8-137">Also, note that the second line brings in the ASP.NET Core MVC tag helpers using the wildcard syntax (those helpers are discussed in [Introduction to Tag Helpers](intro.md).) It's the `@addTagHelper` directive that makes the tag helper available to the Razor view.</span></span> <span data-ttu-id="64fe8-138">Stattdessen können Sie auch wie folgt den vollqualifizierten Namen eines Taghilfsprogramms eingeben:</span><span class="sxs-lookup"><span data-stu-id="64fe8-138">Alternatively, you can provide the fully qualified name (FQN) of a tag helper as shown below:</span></span>

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

<span data-ttu-id="64fe8-139">Wenn Sie einer Ansicht über einen vollqualifizierten Namen ein Taghilfsprogramm hinzufügen möchten, müssen Sie zunächst den Namen (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) und dann den **Assemblynamen** ( *AuthoringTagHelpers* , nicht notwendigerweise `namespace`) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-139">To add a tag helper to a view using a FQN, you first add the FQN (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`), and then the **assembly name** ( *AuthoringTagHelpers* , not necessarily the `namespace`).</span></span> <span data-ttu-id="64fe8-140">Die meisten Entwickler verwenden am liebsten die Platzhaltersyntax.</span><span class="sxs-lookup"><span data-stu-id="64fe8-140">Most developers will prefer to use the wildcard syntax.</span></span> <span data-ttu-id="64fe8-141">Unter [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](intro.md) finden Sie mehr Details zum Hinzufügen und Entfernen von Taghilfsprogrammen, zur Hierarchie und zur Platzhaltersyntax.</span><span class="sxs-lookup"><span data-stu-id="64fe8-141">[Introduction to Tag Helpers](intro.md) goes into detail on tag helper adding, removing, hierarchy, and wildcard syntax.</span></span>

1. <span data-ttu-id="64fe8-142">Aktualisieren Sie das Markup mit den folgenden Änderungen in der Datei *Views/Home/Contact.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="64fe8-142">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="64fe8-143">Führen Sie die App aus, und verwenden Sie einen beliebigen Browser, um die HTML-Quelle abzurufen, damit Sie überprüfen können, ob die E-Mail-Tags durch Anchor-Markups wie `<a>Support</a>` ersetzt wurden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-143">Run the app and use your favorite browser to view the HTML source so you can verify that the email tags are replaced with anchor markup (For example, `<a>Support</a>`).</span></span> <span data-ttu-id="64fe8-144">*Support* und *Marketing* werden als Links gerendert. Allerdings besitzen sie kein `href`-Attribut, das sie funktionsfähig macht.</span><span class="sxs-lookup"><span data-stu-id="64fe8-144">*Support* and *Marketing* are rendered as a links, but they don't have an `href` attribute to make them functional.</span></span> <span data-ttu-id="64fe8-145">Dies soll im nächsten Abschnitt behoben werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-145">We'll fix that in the next section.</span></span>

## <a name="setattribute-and-setcontent"></a><span data-ttu-id="64fe8-146">„SetAttribute“ und „SetContent“</span><span class="sxs-lookup"><span data-stu-id="64fe8-146">SetAttribute and SetContent</span></span>

<span data-ttu-id="64fe8-147">In diesem Abschnitt wird erläutert, wie Sie ein Update für `EmailTagHelper` ausführen, sodass ein gültiges Anchor-Tag für die E-Mail erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-147">In this section, we'll update the `EmailTagHelper` so that it will create a valid anchor tag for email.</span></span> <span data-ttu-id="64fe8-148">Wir aktualisieren Sie, um Informationen aus einer Razor Ansicht (in Form eines- `mail-to` Attributs) zu übernehmen und diese beim Erstellen des Ankers zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-148">We'll update it to take information from a Razor view (in the form of a `mail-to` attribute) and use that in generating the anchor.</span></span>

<span data-ttu-id="64fe8-149">Führen Sie über folgenden Code ein Update für die `EmailTagHelper`-Klasse aus:</span><span class="sxs-lookup"><span data-stu-id="64fe8-149">Update the `EmailTagHelper` class with the following:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* <span data-ttu-id="64fe8-150">Namen von Klassen und Eigenschaften für Taghilfsprogramme, die in Pascal-Schreibweise angegeben sind, werden in [Kebab-Schreibweise](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) übersetzt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-150">Pascal-cased class and property names for tag helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="64fe8-151">Wenn Sie daher das `MailTo`-Attribut verwenden möchten, müssen Sie das entsprechende `<email mail-to="value"/>`-Äquivalent auswählen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-151">Therefore, to use the `MailTo` attribute, you'll use `<email mail-to="value"/>` equivalent.</span></span>

* <span data-ttu-id="64fe8-152">Mit der letzten Zeile wird der Inhalt dieses Taghilfsprogramms fertig gestellt, das alle mindestens erforderlichen Elemente enthält.</span><span class="sxs-lookup"><span data-stu-id="64fe8-152">The last line sets the completed content for our minimally functional tag helper.</span></span>

* <span data-ttu-id="64fe8-153">In der markierten Zeile wird die Syntax zum Hinzufügen von Attributen dargestellt:</span><span class="sxs-lookup"><span data-stu-id="64fe8-153">The highlighted line shows the syntax for adding attributes:</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

<span data-ttu-id="64fe8-154">Dieser Ansatz funktioniert für das Attribut „href“, wenn es zu diesem Zeitpunkt noch nicht in der Attributsammlung enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="64fe8-154">That approach works for the attribute "href" as long as it doesn't currently exist in the attributes collection.</span></span> <span data-ttu-id="64fe8-155">Außerdem können Sie die `output.Attributes.Add`-Methode verwenden, um ein Attribut des Taghilfsprogramms am Ende der Tagattributsammlung hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-155">You can also use the `output.Attributes.Add` method to add a tag helper attribute to the end of the collection of tag attributes.</span></span>

1. <span data-ttu-id="64fe8-156">Aktualisieren Sie das Markup mit den folgenden Änderungen in der Datei *Views/Home/Contact.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="64fe8-156">Update the markup in the *Views/Home/Contact.cshtml* file with these changes:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. <span data-ttu-id="64fe8-157">Führen Sie die App aus, und überprüfen Sie, ob die richtigen Links generiert werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-157">Run the app and verify that it generates the correct links.</span></span>

<a name="self-closing"></a>

   > [!NOTE]
   > <span data-ttu-id="64fe8-158">Wenn das E-Mail-Tag selbstschließend sein soll (`<email mail-to="Rick" />`), ist auch die endgültige Ausgabe selbstschließend.</span><span class="sxs-lookup"><span data-stu-id="64fe8-158">If you were to write the email tag self-closing (`<email mail-to="Rick" />`), the final output would also be self-closing.</span></span> <span data-ttu-id="64fe8-159">Um die Funktion zum Schreiben des Tags nur mit einem Starttag (`<email mail-to="Rick">`) zu aktivieren, müssen Sie die Klasse wie folgt markieren:</span><span class="sxs-lookup"><span data-stu-id="64fe8-159">To enable the ability to write the tag with only a start tag (`<email mail-to="Rick">`) you must mark the class with the following:</span></span>
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   <span data-ttu-id="64fe8-160">Bei einem selbstschließenden E-Mail-Taghilfsprogramm sieht die Ausgabe wie folgt aus `<a href="mailto:Rick@contoso.com" />`.</span><span class="sxs-lookup"><span data-stu-id="64fe8-160">With a self-closing email tag helper, the output would be `<a href="mailto:Rick@contoso.com" />`.</span></span> <span data-ttu-id="64fe8-161">Bei selbstschließenden Anchor-Tags handelt es sich nicht um gültige HTML-Elemente. Daher sollten Sie diese nicht erstellen. Erstellen Sie stattdessen ein selbstschließendes Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="64fe8-161">Self-closing anchor tags are not valid HTML, so you wouldn't want to create one, but you might want to create a tag helper that's self-closing.</span></span> <span data-ttu-id="64fe8-162">Taghilfsprogramme legen den Typ der `TagMode`-Eigenschaft fest, nachdem ein Tag gelesen wurde.</span><span class="sxs-lookup"><span data-stu-id="64fe8-162">Tag helpers set the type of the `TagMode` property after reading a tag.</span></span>

### <a name="processasync"></a><span data-ttu-id="64fe8-163">ProcessAsync</span><span class="sxs-lookup"><span data-stu-id="64fe8-163">ProcessAsync</span></span>

<span data-ttu-id="64fe8-164">In diesem Abschnitt wird beschrieben, wie Sie ein asynchrones E-Mail-Hilfsprogramm schreiben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-164">In this section, we'll write an asynchronous email helper.</span></span>

1. <span data-ttu-id="64fe8-165">Ersetzen Sie die Klasse `EmailTagHelper` durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="64fe8-165">Replace the `EmailTagHelper` class with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   <span data-ttu-id="64fe8-166">**Hinweise:**</span><span class="sxs-lookup"><span data-stu-id="64fe8-166">**Notes:**</span></span>

   * <span data-ttu-id="64fe8-167">Diese Version verwendet die asynchrone `ProcessAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="64fe8-167">This version uses the asynchronous `ProcessAsync` method.</span></span> <span data-ttu-id="64fe8-168">Die asynchrone `GetChildContentAsync`-Methode gibt `Task` mit `TagHelperContent` zurück.</span><span class="sxs-lookup"><span data-stu-id="64fe8-168">The asynchronous `GetChildContentAsync` returns a `Task` containing the `TagHelperContent`.</span></span>

   * <span data-ttu-id="64fe8-169">Verwenden Sie den Parameter `output`, um Inhalte des HTML-Elements abzurufen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-169">Use the `output` parameter to get contents of the HTML element.</span></span>

1. <span data-ttu-id="64fe8-170">Nehmen Sie folgende Änderung an der *Views/Home/Contact.cshtml* -Datei vor, damit das Taghilfsprogramm die Ziel-E-Mail abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="64fe8-170">Make the following change to the *Views/Home/Contact.cshtml* file so the tag helper can get the target email.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. <span data-ttu-id="64fe8-171">Führen Sie die App aus, und überprüfen Sie, ob gültige E-Mail-Links generiert werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-171">Run the app and verify that it generates valid email links.</span></span>

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a><span data-ttu-id="64fe8-172">„RemoveAll“, „PreContent.SetHtmlContent“ und „PostContent.SetHtmlContent“</span><span class="sxs-lookup"><span data-stu-id="64fe8-172">RemoveAll, PreContent.SetHtmlContent and PostContent.SetHtmlContent</span></span>

1. <span data-ttu-id="64fe8-173">Fügen Sie dem Ordner *TagHelpers* folgende `BoldTagHelper`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-173">Add the following `BoldTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * <span data-ttu-id="64fe8-174">Das `[HtmlTargetElement]`-Attribut übergibt einen Attributparameter, der angibt, dass diesem ein HTML-Element mit dem HTML-Attribut „bold“ zugeordnet werden kann. Anschließend wird die Überschreibungsmethode `Process` in der Klasse ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-174">The `[HtmlTargetElement]` attribute passes an attribute parameter that specifies that any HTML element that contains an HTML attribute named "bold" will match, and the `Process` override method in the class will run.</span></span> <span data-ttu-id="64fe8-175">In diesem Beispiel entfernt die Methode `Process` das Attribut „bold“ und umschließt das enthaltene Markup mit `<strong></strong>`.</span><span class="sxs-lookup"><span data-stu-id="64fe8-175">In our sample, the `Process` method removes the "bold" attribute and surrounds the containing markup with `<strong></strong>`.</span></span>

   * <span data-ttu-id="64fe8-176">Da der bestehende Taginhalt nicht ersetzt werden soll, müssen Sie das Starttag `<strong>` über die Methode `PreContent.SetHtmlContent` und das Endtag `</strong>` über die Methode `PostContent.SetHtmlContent` schreiben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-176">Because you don't want to replace the existing tag content, you must write the opening `<strong>` tag with the `PreContent.SetHtmlContent` method and the closing `</strong>` tag with the `PostContent.SetHtmlContent` method.</span></span>

1. <span data-ttu-id="64fe8-177">Verändern Sie die Ansicht *About.cshtml* , damit diese den Attributwert `bold` enthält.</span><span class="sxs-lookup"><span data-stu-id="64fe8-177">Modify the *About.cshtml* view to contain a `bold` attribute value.</span></span> <span data-ttu-id="64fe8-178">Der fertige Code wird nachfolgend dargestellt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-178">The completed code is shown below.</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. <span data-ttu-id="64fe8-179">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="64fe8-179">Run the app.</span></span> <span data-ttu-id="64fe8-180">Sie können einen beliebigen Browser verwenden, um die Quelle zu untersuchen und das Markup zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-180">You can use your favorite browser to inspect the source and verify the markup.</span></span>

   <span data-ttu-id="64fe8-181">Das oben genannte `[HtmlTargetElement]`-Attribut gilt nur für HTML-Markups mit dem Attributnamen „bold“.</span><span class="sxs-lookup"><span data-stu-id="64fe8-181">The `[HtmlTargetElement]` attribute above only targets HTML markup that provides an attribute name of "bold".</span></span> <span data-ttu-id="64fe8-182">Das `<bold>`-Element wurde vom Taghilfsprogramm nicht verändert.</span><span class="sxs-lookup"><span data-stu-id="64fe8-182">The `<bold>` element wasn't modified by the tag helper.</span></span>

1. <span data-ttu-id="64fe8-183">Kommentieren Sie die Attributzeile `[HtmlTargetElement]` aus, damit standardmäßig `<bold>`-Tags angezielt werden, also HTML-Markups im Format `<bold>`.</span><span class="sxs-lookup"><span data-stu-id="64fe8-183">Comment out the `[HtmlTargetElement]` attribute line and it will default to targeting `<bold>` tags, that is, HTML markup of the form `<bold>`.</span></span> <span data-ttu-id="64fe8-184">Beachten Sie, dass die Standardnamenskonvention dem Klassennamen **Bold** TagHelper den `<bold>`-Tags entspricht.</span><span class="sxs-lookup"><span data-stu-id="64fe8-184">Remember, the default naming convention will match the class name **Bold** TagHelper to `<bold>` tags.</span></span>

1. <span data-ttu-id="64fe8-185">Führen Sie die App aus, und überprüfen Sie, ob das `<bold>`-Tag vom Taghilfsprogramm verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-185">Run the app and verify that the `<bold>` tag is processed by the tag helper.</span></span>

<span data-ttu-id="64fe8-186">Wenn Sie eine Klasse durch mehrere `[HtmlTargetElement]`-Attribute ergänzen, wird ein logischer ODER-Operator der Ziele erstellt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-186">Decorating a class with multiple `[HtmlTargetElement]` attributes results in a logical-OR of the targets.</span></span> <span data-ttu-id="64fe8-187">Wenn Sie z.B. den nachfolgenden Code verwenden, wird ein „bold“-Tag oder ein „bold“-Attribut zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="64fe8-187">For example, using the code below, a bold tag or a bold attribute will match.</span></span>

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

<span data-ttu-id="64fe8-188">Wenn mehrere Attribute derselben Anweisung hinzugefügt werden, behandelt die Runtime diese als logisches UND.</span><span class="sxs-lookup"><span data-stu-id="64fe8-188">When multiple attributes are added to the same statement, the runtime treats them as a logical-AND.</span></span> <span data-ttu-id="64fe8-189">Beispielsweise muss im nachfolgenden Code ein HTML-Element den Namen „bold“ mit einem Attribut namens „bold“ (`<bold bold />`) enthalten sein, damit dieses zugeordnet werden kann.</span><span class="sxs-lookup"><span data-stu-id="64fe8-189">For example, in the code below, an HTML element must be named "bold" with an attribute named "bold" (`<bold bold />`) to match.</span></span>

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

<span data-ttu-id="64fe8-190">Außerdem können Sie `[HtmlTargetElement]` verwenden, um den Namen des angezielten Elements zu ändern.</span><span class="sxs-lookup"><span data-stu-id="64fe8-190">You can also use the `[HtmlTargetElement]` to change the name of the targeted element.</span></span> <span data-ttu-id="64fe8-191">Wenn Sie z.B. möchten, dass `BoldTagHelper``<MyBold>`-Tags anzielt, müssen Sie die folgenden Attribute verwenden:</span><span class="sxs-lookup"><span data-stu-id="64fe8-191">For example if you wanted the `BoldTagHelper` to target `<MyBold>` tags, you would use the following attribute:</span></span>

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a><span data-ttu-id="64fe8-192">Übergeben eines Modells an das Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="64fe8-192">Pass a model to a Tag Helper</span></span>

1. <span data-ttu-id="64fe8-193">Fügen Sie einen *Models* -Ordner hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-193">Add a *Models* folder.</span></span>

1. <span data-ttu-id="64fe8-194">Fügen Sie dem Ordner *Models* die folgende `WebsiteContext`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="64fe8-194">Add the following `WebsiteContext` class to the *Models* folder:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. <span data-ttu-id="64fe8-195">Fügen Sie dem Ordner *TagHelpers* folgende `WebsiteInformationTagHelper`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-195">Add the following `WebsiteInformationTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * <span data-ttu-id="64fe8-196">Wie obenstehend erwähnt, übersetzen Taghilfsprogramme C#-Klassennamen und -Eigenschaften in der Pascal-Schreibweise für Taghilfsprogramme in [Kebab-Schreibweise](https://wiki.c2.com/?KebabCase).</span><span class="sxs-lookup"><span data-stu-id="64fe8-196">As mentioned previously, tag helpers translates Pascal-cased C# class names and properties for tag helpers into [kebab case](https://wiki.c2.com/?KebabCase).</span></span> <span data-ttu-id="64fe8-197">Um in zu verwenden `WebsiteInformationTagHelper` Razor , schreiben Sie daher `<website-information />` .</span><span class="sxs-lookup"><span data-stu-id="64fe8-197">Therefore, to use the `WebsiteInformationTagHelper` in Razor, you'll write `<website-information />`.</span></span>

   * <span data-ttu-id="64fe8-198">Das Zielelement wird mit dem `[HtmlTargetElement]`-Attribut nicht explizit identifiziert, sodass standardmäßig `website-information` angezielt wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-198">You are not explicitly identifying the target element with the `[HtmlTargetElement]` attribute, so the default of `website-information` will be targeted.</span></span> <span data-ttu-id="64fe8-199">Wenn Sie das folgende Attribut angewendet haben (beachten Sie, dass es sich hierbei nicht um Kebab Case handelt, sondern das Attribut dem Klassennamen zugeordnet wird):</span><span class="sxs-lookup"><span data-stu-id="64fe8-199">If you applied the following attribute (note it's not kebab case but matches the class name):</span></span>

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   <span data-ttu-id="64fe8-200">Das Tag `<website-information />` in Kebab-Schreibweise wird dann nicht zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="64fe8-200">The kebab case tag `<website-information />` wouldn't match.</span></span> <span data-ttu-id="64fe8-201">Wenn Sie das `[HtmlTargetElement]`-Attribut verwenden, müssen Sie wie nachfolgend dargestellt Kebab Case verwenden:</span><span class="sxs-lookup"><span data-stu-id="64fe8-201">If you want use the `[HtmlTargetElement]` attribute, you would use kebab case as shown below:</span></span>

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * <span data-ttu-id="64fe8-202">Selbstschließende Elemente haben keinen Inhalt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-202">Elements that are self-closing have no content.</span></span> <span data-ttu-id="64fe8-203">In diesem Beispiel verwendet das Razor Markup ein selbstschließendes Tag, aber das taghilfsprogramm erstellt ein [Abschnitts](https://www.w3.org/TR/html5/sections.html#the-section-element) Element (das nicht selbst geschlossen wird und Sie Inhalt innerhalb des `section` Elements schreiben).</span><span class="sxs-lookup"><span data-stu-id="64fe8-203">For this example, the Razor markup will use a self-closing tag, but the tag helper will be creating a [section](https://www.w3.org/TR/html5/sections.html#the-section-element) element (which isn't self-closing and you are writing content inside the `section` element).</span></span> <span data-ttu-id="64fe8-204">Aus diesem Grund müssen Sie `TagMode` auf `StartTagAndEndTag` festlegen, um Ausgaben zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-204">Therefore, you need to set `TagMode` to `StartTagAndEndTag` to write output.</span></span> <span data-ttu-id="64fe8-205">Stattdessen können Sie auch die Zeileneinstellung `TagMode` auskommentieren und Markups mit einem Endtag schreiben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-205">Alternatively, you can comment out the line setting `TagMode` and write markup with a closing tag.</span></span> <span data-ttu-id="64fe8-206">(Ein Beispielmarkup wird in einem nachfolgenden Abschnitt dieses Tutorials zur Verfügung gestellt.)</span><span class="sxs-lookup"><span data-stu-id="64fe8-206">(Example markup is provided later in this tutorial.)</span></span>

   * <span data-ttu-id="64fe8-207">Das Dollarzeichen (`$`) in der folgenden Zeile verwendet eine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span><span class="sxs-lookup"><span data-stu-id="64fe8-207">The `$` (dollar sign) in the following line uses an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings):</span></span>

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. <span data-ttu-id="64fe8-208">Fügen Sie der *About.cshtml* -Ansicht das folgende Markup hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-208">Add the following markup to the *About.cshtml* view.</span></span> <span data-ttu-id="64fe8-209">Das markierte Markup zeigt die Websiteinformationen an.</span><span class="sxs-lookup"><span data-stu-id="64fe8-209">The highlighted markup displays the web site information.</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > <span data-ttu-id="64fe8-210">Im Razor unten gezeigten Markup:</span><span class="sxs-lookup"><span data-stu-id="64fe8-210">In the Razor markup shown below:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > <span data-ttu-id="64fe8-211">Razor weiß, `info` dass es sich bei dem Attribut um eine Klasse, nicht um eine Zeichenfolge handelt und Sie c#-Code schreiben möchten.</span><span class="sxs-lookup"><span data-stu-id="64fe8-211">Razor knows the `info` attribute is a class, not a string, and you want to write C# code.</span></span> <span data-ttu-id="64fe8-212">Alle Attribute von Taghilfsprogrammen, die keine Zeichenfolgen darstellen, sollten ohne das Zeichen `@` geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-212">Any non-string tag helper attribute should be written without the `@` character.</span></span>

1. <span data-ttu-id="64fe8-213">Führen Sie die App aus, und navigieren Sie zur Ansicht „Info“, um die Websiteinformationen zu sehen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-213">Run the app, and navigate to the About view to see the web site information.</span></span>

   > [!NOTE]
   > <span data-ttu-id="64fe8-214">Sie können das folgende Markup mit dem Endtag verwenden und die Zeile mit `TagMode.StartTagAndEndTag` aus dem Taghilfsprogramm entfernen:</span><span class="sxs-lookup"><span data-stu-id="64fe8-214">You can use the following markup with a closing tag and remove the line with `TagMode.StartTagAndEndTag` in the tag helper:</span></span>
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a><span data-ttu-id="64fe8-215">Taghilfsprogramm für Bedingungen</span><span class="sxs-lookup"><span data-stu-id="64fe8-215">Condition Tag Helper</span></span>

<span data-ttu-id="64fe8-216">Das Taghilfsprogramm für Bedingungen rendert Ausgaben, wenn ein TRUE-Wert zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-216">The condition tag helper renders output when passed a true value.</span></span>

1. <span data-ttu-id="64fe8-217">Fügen Sie dem Ordner *TagHelpers* folgende `ConditionTagHelper`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-217">Add the following `ConditionTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. <span data-ttu-id="64fe8-218">Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:</span><span class="sxs-lookup"><span data-stu-id="64fe8-218">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. <span data-ttu-id="64fe8-219">Ersetzen Sie die `Index`-Methode im `Home`-Controller durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="64fe8-219">Replace the `Index` method in the `Home` controller with the following code:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. <span data-ttu-id="64fe8-220">Führen Sie die App aus, und navigieren Sie zur Homepage.</span><span class="sxs-lookup"><span data-stu-id="64fe8-220">Run the app and browse to the home page.</span></span> <span data-ttu-id="64fe8-221">Das Markup im bedingten `div`-Element wird nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="64fe8-221">The markup in the conditional `div` won't be rendered.</span></span> <span data-ttu-id="64fe8-222">Fügen Sie die Abfragezeichenfolge `?approved=true` an die URL an (z.B. `http://localhost:1235/Home/Index?approved=true`).</span><span class="sxs-lookup"><span data-stu-id="64fe8-222">Append the query string `?approved=true` to the URL (for example, `http://localhost:1235/Home/Index?approved=true`).</span></span> <span data-ttu-id="64fe8-223">`approved` ist auf TRUE festgelegt, und das bedingte Markup wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-223">`approved` is set to true and the conditional markup will be displayed.</span></span>

> [!NOTE]
> <span data-ttu-id="64fe8-224">Verwenden Sie den [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operator, um das anzuzielende Attribut wie beim „bold“-Taghilfsprogramm anzugeben, anstatt eine Zeichenfolge anzugeben:</span><span class="sxs-lookup"><span data-stu-id="64fe8-224">Use the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator to specify the attribute to target rather than specifying a string as you did with the bold tag helper:</span></span>
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> <span data-ttu-id="64fe8-225">Der [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operator schützt den Code, wenn dieser umgestaltet wird, also wenn z.B. der Name in `RedCondition` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-225">The [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator will protect the code should it ever be refactored (we might want to change the name to `RedCondition`).</span></span>

### <a name="avoid-tag-helper-conflicts"></a><span data-ttu-id="64fe8-226">Vermeiden von Konflikten mit dem Taghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="64fe8-226">Avoid Tag Helper conflicts</span></span>

<span data-ttu-id="64fe8-227">In diesem Abschnitt wird erläutert, wie Sie zwei Taghilfsprogramme für automatische Verlinkungen schreiben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-227">In this section, you write a pair of auto-linking tag helpers.</span></span> <span data-ttu-id="64fe8-228">Das erste ersetzt Markups mit einer URL, die mit HTTP beginnt, durch ein HTML-Anchor-Tag mit derselben URL, sodass ein Link zur der URL zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-228">The first will replace markup containing a URL starting with HTTP to an HTML anchor tag containing the same URL (and thus yielding a link to the URL).</span></span> <span data-ttu-id="64fe8-229">Das zweite führt dieselbe Aktion aus, allerdings für URLs, die mit WWW beginnen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-229">The second will do the same for a URL starting with WWW.</span></span>

<span data-ttu-id="64fe8-230">Da dieses beiden Hilfsprogramme sehr ähnlich funktionieren und Sie sie möglicherweise in der Zukunft umgestalten möchten, werden diese in derselben Datei gespeichert.</span><span class="sxs-lookup"><span data-stu-id="64fe8-230">Because these two helpers are closely related and you may refactor them in the future, we'll keep them in the same file.</span></span>

1. <span data-ttu-id="64fe8-231">Fügen Sie dem Ordner *TagHelpers* folgende `AutoLinkerHttpTagHelper`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="64fe8-231">Add the following `AutoLinkerHttpTagHelper` class to the *TagHelpers* folder.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   ><span data-ttu-id="64fe8-232">Die `AutoLinkerHttpTagHelper`-Klasse zielt `p`-Elemente an und verwendet [RegEx](/dotnet/standard/base-types/regular-expression-language-quick-reference), um den Anchor zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-232">The `AutoLinkerHttpTagHelper` class targets `p` elements and uses [Regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) to create the anchor.</span></span>

1. <span data-ttu-id="64fe8-233">Fügen Sie das folgende Markup an das Ende der Datei *Views/Home/Contact.cshtml* an:</span><span class="sxs-lookup"><span data-stu-id="64fe8-233">Add the following markup to the end of the *Views/Home/Contact.cshtml* file:</span></span>

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. <span data-ttu-id="64fe8-234">Führen Sie die App aus, und überprüfen Sie, ob das Taghilfsprogramm den Anchor richtig rendert.</span><span class="sxs-lookup"><span data-stu-id="64fe8-234">Run the app and verify that the tag helper renders the anchor correctly.</span></span>

1. <span data-ttu-id="64fe8-235">Führen Sie ein Update für die `AutoLinker`-Klasse aus, um `AutoLinkerWwwTagHelper` einzufügen, wodurch WWW-Text in ein Anchor-Tag konvertiert wird, das ebenfalls den ursprünglichen WWW-Text enthält.</span><span class="sxs-lookup"><span data-stu-id="64fe8-235">Update the `AutoLinker` class to include the `AutoLinkerWwwTagHelper` which will convert www text to an anchor tag that also contains the original www text.</span></span> <span data-ttu-id="64fe8-236">Der aktualisierte Code wird im folgenden Beispiel markiert:</span><span class="sxs-lookup"><span data-stu-id="64fe8-236">The updated code is highlighted below:</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. <span data-ttu-id="64fe8-237">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="64fe8-237">Run the app.</span></span> <span data-ttu-id="64fe8-238">Beachten Sie, dass der WWW-Text als Link gerendert wird, der HTTP-Text hingegen nicht.</span><span class="sxs-lookup"><span data-stu-id="64fe8-238">Notice the www text is rendered as a link but the HTTP text isn't.</span></span> <span data-ttu-id="64fe8-239">Wenn Sie in beide Klassen einen Haltepunkt einfügen, wird die HTTP-Hilfsprogrammklasse zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-239">If you put a break point in both classes, you can see that the HTTP tag helper class runs first.</span></span> <span data-ttu-id="64fe8-240">Das Problem dabei ist, dass die Ausgabe des Taghilfsprogramms zwischengespeichert wird, und wenn das WWW-Hilfsprogramm ausgeführt wird, wird die zwischengespeicherte Ausgabe des HTTP-Hilfsprogramms überschrieben.</span><span class="sxs-lookup"><span data-stu-id="64fe8-240">The problem is that the tag helper output is cached, and when the WWW tag helper is run, it overwrites the cached output from the HTTP tag helper.</span></span> <span data-ttu-id="64fe8-241">Nachfolgend wird erläutert, wie Sie die Reihenfolge kontrollieren, in der Taghilfsprogramme ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-241">Later in the tutorial we'll see how to control the order that tag helpers run in.</span></span> <span data-ttu-id="64fe8-242">Codefehler werden wie folgt behoben:</span><span class="sxs-lookup"><span data-stu-id="64fe8-242">We'll fix the code with the following:</span></span>

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > <span data-ttu-id="64fe8-243">In der ersten Version der Taghilfsprogramme für automatische Verlinkungen konnten Sie den Inhalt des Ziels über folgenden Code abrufen:</span><span class="sxs-lookup"><span data-stu-id="64fe8-243">In the first edition of the auto-linking tag helpers, you got the content of the target with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > <span data-ttu-id="64fe8-244">Das bedeutet, Sie rufen `GetChildContentAsync` über das `TagHelperOutput`-Objekt ab, das in der `ProcessAsync`-Methode übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="64fe8-244">That is, you call `GetChildContentAsync` using the `TagHelperOutput` passed into the `ProcessAsync` method.</span></span> <span data-ttu-id="64fe8-245">Wie zuvor bereits erwähnt, gewinnt das letzte Taghilfsprogramm, das ausgeführt wird, da die Ausgabe zwischengespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-245">As mentioned previously, because the output is cached, the last tag helper to run wins.</span></span> <span data-ttu-id="64fe8-246">Sie haben dieses Problem mithilfe des folgenden Codes behoben:</span><span class="sxs-lookup"><span data-stu-id="64fe8-246">You fixed that problem with the following code:</span></span>
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > <span data-ttu-id="64fe8-247">Der obenstehende Code prüft, ob der Inhalt verändert wurde. Wenn das der Fall ist, ruft er den Inhalt vom Ausgabepuffer ab.</span><span class="sxs-lookup"><span data-stu-id="64fe8-247">The code above checks to see if the content has been modified, and if it has, it gets the content from the output buffer.</span></span>

1. <span data-ttu-id="64fe8-248">Führen Sie die App aus, und überprüfen Sie, ob die beiden Links wie gewünscht funktionieren.</span><span class="sxs-lookup"><span data-stu-id="64fe8-248">Run the app and verify that the two links work as expected.</span></span> <span data-ttu-id="64fe8-249">Dabei kann es oft sein, dass es scheint, als würde das Taghilfsprogramm für die automatische Verlinkung einwandfrei funktionieren, obwohl ein verstecktes Problem besteht.</span><span class="sxs-lookup"><span data-stu-id="64fe8-249">While it might appear our auto linker tag helper is correct and complete, it has a subtle problem.</span></span> <span data-ttu-id="64fe8-250">Wenn das WWW-Taghilfsprogramm zuerst ausgeführt wird, werden die WWW-Links fehlerhaft erstellt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-250">If the WWW tag helper runs first, the www links won't be correct.</span></span> <span data-ttu-id="64fe8-251">Aktualisieren Sie den Code, indem Sie die Überladung `Order` hinzufügen, um die Reihenfolge zu kontrollieren, in der das Tag ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-251">Update the code by adding the `Order` overload to control the order that the tag runs in.</span></span> <span data-ttu-id="64fe8-252">Die Eigenschaft `Order` bestimmt die Ausführungsreihenfolge, die im Zusammenhang mit anderen Taghilfsprogrammen steht, die auf dasselbe Element ausgerichtet sind.</span><span class="sxs-lookup"><span data-stu-id="64fe8-252">The `Order` property determines the execution order relative to other tag helpers targeting the same element.</span></span> <span data-ttu-id="64fe8-253">Der Wert der Standardreihenfolge ist 0 (null), und Instanzen mit niedrigeren Werten werden zuerst ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="64fe8-253">The default order value is zero and instances with lower values are executed first.</span></span>

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   <span data-ttu-id="64fe8-254">Mit dem vorhergehenden Code wird sichergestellt, dass das HTTP-Taghilfsprogramm vor dem WWW-Taghilfsprogramm ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="64fe8-254">The preceding code guarantees that the HTTP tag helper runs before the WWW tag helper.</span></span> <span data-ttu-id="64fe8-255">Ändern Sie `Order` in `MaxValue`, und überprüfen Sie, ob das für das WWW-Tag generierte Markup falsch ist.</span><span class="sxs-lookup"><span data-stu-id="64fe8-255">Change `Order` to `MaxValue` and verify that the markup generated for the WWW tag is incorrect.</span></span>

## <a name="inspect-and-retrieve-child-content"></a><span data-ttu-id="64fe8-256">Überprüfen und Abrufen von untergeordnetem Inhalt</span><span class="sxs-lookup"><span data-stu-id="64fe8-256">Inspect and retrieve child content</span></span>

<span data-ttu-id="64fe8-257">Die Taghilfsprogramme stellen mehrere Eigenschaften zum Abrufen von Inhalt zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="64fe8-257">The tag helpers provide several properties to retrieve content.</span></span>

* <span data-ttu-id="64fe8-258">Das Ergebnis von `GetChildContentAsync` kann an `output.Content` angefügt werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-258">The result of `GetChildContentAsync` can be appended to `output.Content`.</span></span>
* <span data-ttu-id="64fe8-259">Sie können das Ergebnis von `GetChildContentAsync` mit `GetContent` prüfen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-259">You can inspect the result of `GetChildContentAsync` with `GetContent`.</span></span>
* <span data-ttu-id="64fe8-260">Wenn Sie `output.Content` ändern, wird der TagHelper-Text nicht ausgeführt oder gerendert, solange Sie nicht wie im Beispiel zur automatischen Verlinkung `GetChildContentAsync` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="64fe8-260">If you modify `output.Content`, the TagHelper body won't be executed or rendered unless you call `GetChildContentAsync` as in our auto-linker sample:</span></span>

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* <span data-ttu-id="64fe8-261">Bei mehreren Aufrufen von `GetChildContentAsync` wird derselbe Wert zurückgegeben, und der `TagHelper`-Text wird nicht erneut ausgeführt, solange Sie keinen FALSE-Parameter übergeben, der darauf hindeutet, dass die zwischengespeicherten Ergebnisse nicht verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="64fe8-261">Multiple calls to `GetChildContentAsync` returns the same value and doesn't re-execute the `TagHelper` body unless you pass in a false parameter indicating not to use the cached result.</span></span>

## <a name="load-minified-partial-view-taghelper"></a><span data-ttu-id="64fe8-262">TagHelper für das Laden minimierter Teilansichten</span><span class="sxs-lookup"><span data-stu-id="64fe8-262">Load minified partial view TagHelper</span></span>

<span data-ttu-id="64fe8-263">In Produktionsumgebungen lässt sich die Leistung steigern, indem minimierte Teilansichten geladen werden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-263">In production environments, performance can be improved by loading minified partial views.</span></span> <span data-ttu-id="64fe8-264">Um von minimierten Teilansichten in der Produktion zu profitieren, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="64fe8-264">To take advantage of minified partial view in production:</span></span>

* <span data-ttu-id="64fe8-265">Erstellen Sie einen Präbuildprozess, der Teilansichten minimiert, und richten Sie den Prozess ein.</span><span class="sxs-lookup"><span data-stu-id="64fe8-265">Create/set up a pre-build process that minifies partial views.</span></span>
* <span data-ttu-id="64fe8-266">Verwenden Sie den folgenden Code, um minimierte Teilansichten in Nicht-Entwicklungsumgebungen zu laden.</span><span class="sxs-lookup"><span data-stu-id="64fe8-266">Use the following code to load  minified partial views in non-development environments.</span></span>

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
