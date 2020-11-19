---
title: 'Teil 8: Hinzufügen der Validierung'
author: rick-anderson
description: Dies ist Teil 8 der Tutorialreihe zu Razor Pages.
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- Create
- Delete
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
uid: tutorials/razor-pages/validation
ms.openlocfilehash: d69ab3452f4f15e916049e5c772a20fe9f9fac65
ms.sourcegitcommit: 1ea3f23bec63e96ffc3a927992f30a5fc0de3ff9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94570223"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="71ead-103">Dies ist Teil 8 der Tutorialreihe zu Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="71ead-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="71ead-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="71ead-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="71ead-105">In diesem Abschnitt wird dem Modell `Movie` Validierungslogik hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="71ead-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="71ead-106">Die Validierungsregeln werden erzwungen, wenn ein Benutzer einen Film erstellt oder bearbeitet.</span><span class="sxs-lookup"><span data-stu-id="71ead-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="71ead-107">Validierung</span><span class="sxs-lookup"><span data-stu-id="71ead-107">Validation</span></span>

<span data-ttu-id="71ead-108">Ein wesentlicher Grundsatz der Softwareentwicklung heißt [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) (**D** on't **R** epeat **Y** ourself, dt. Wiederholen Sie sich nicht).</span><span class="sxs-lookup"><span data-stu-id="71ead-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="71ead-109">Razor Pages ist für Entwicklungsaufgaben gedacht, bei denen die Funktionalität einmal angegeben und für die gesamte App übernommen wird.</span><span class="sxs-lookup"><span data-stu-id="71ead-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="71ead-110">DRY kann Sie bei Folgendem unterstützen:</span><span class="sxs-lookup"><span data-stu-id="71ead-110">DRY can help:</span></span>

* <span data-ttu-id="71ead-111">Sie können die Codemenge in einer App reduzieren.</span><span class="sxs-lookup"><span data-stu-id="71ead-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="71ead-112">Der Code wird weniger fehleranfällig und lässt sich leichter testen und verwalten.</span><span class="sxs-lookup"><span data-stu-id="71ead-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="71ead-113">Die von Razor Pages und Entity Framework gebotene Unterstützung der Validierung ist ein gutes Beispiel des DRY-Prinzips:</span><span class="sxs-lookup"><span data-stu-id="71ead-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="71ead-114">Validierungsregeln werden an zentraler Stelle (in der Modellklasse) deklarativ angegeben.</span><span class="sxs-lookup"><span data-stu-id="71ead-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="71ead-115">Regeln werden überall in der App erzwungen.</span><span class="sxs-lookup"><span data-stu-id="71ead-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="71ead-116">Hinzufügen von Validierungsregeln zum Modell „Movie“</span><span class="sxs-lookup"><span data-stu-id="71ead-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="71ead-117">Der Namespace `DataAnnotations` bietet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="71ead-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="71ead-118">Eine Gruppe integrierter Validierungsattribute, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="71ead-119">Formatierungsattribute wie `[DataType]`, die bei der Formatierung helfen und keine Validierung bieten.</span><span class="sxs-lookup"><span data-stu-id="71ead-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="71ead-120">Aktualisieren Sie die `Movie`-Klasse, um die integrierten Validierungsattribute `[Required]`, `[StringLength]`, `[RegularExpression]` und `[Range]` zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="71ead-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="71ead-121">Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:</span><span class="sxs-lookup"><span data-stu-id="71ead-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="71ead-122">Die Attribute `[Required]` und `[MinimumLength]` geben an, dass eine Eigenschaft über einen Wert verfügen muss.</span><span class="sxs-lookup"><span data-stu-id="71ead-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="71ead-123">Nichts hindert einen Benutzer an der Eingabe von Leerzeichen, um diese Validierung zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="71ead-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="71ead-124">Das Attribut `[RegularExpression]` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="71ead-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="71ead-125">Im vorherigen Code, `Genre`:</span><span class="sxs-lookup"><span data-stu-id="71ead-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="71ead-126">Es dürfen nur Buchstaben enthalten sein.</span><span class="sxs-lookup"><span data-stu-id="71ead-126">Must only use letters.</span></span>
  * <span data-ttu-id="71ead-127">Der erste Buchstabe muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="71ead-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="71ead-128">Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.</span><span class="sxs-lookup"><span data-stu-id="71ead-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="71ead-129">Der `RegularExpression` `Rating`:</span><span class="sxs-lookup"><span data-stu-id="71ead-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="71ead-130">Das erste Zeichen muss ein Großbuchstabe sein.</span><span class="sxs-lookup"><span data-stu-id="71ead-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="71ead-131">Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig.</span><span class="sxs-lookup"><span data-stu-id="71ead-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="71ead-132">„PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „`Genre`“.</span><span class="sxs-lookup"><span data-stu-id="71ead-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="71ead-133">Das `[Range]`-Attribut schränkt einen Wert auf einen bestimmten Bereich ein.</span><span class="sxs-lookup"><span data-stu-id="71ead-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="71ead-134">Mit dem Attribut `[StringLength]` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die Mindestlänge festlegt werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="71ead-135">Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="71ead-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="71ead-136">Die oben genannten Validierungsregeln dienen der Veranschaulichung und sind für ein Produktionssystem nicht optimal.</span><span class="sxs-lookup"><span data-stu-id="71ead-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="71ead-137">Beispielsweise verhindert die vorherige Regel die Eingabe eines Films mit nur zwei Zeichen und lässt keine Sonderzeichen in `Genre` zu.</span><span class="sxs-lookup"><span data-stu-id="71ead-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="71ead-138">Die automatische Erzwingung von Validierungsregeln durch ASP.NET Core dient folgenden Zwecken:</span><span class="sxs-lookup"><span data-stu-id="71ead-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="71ead-139">Hilft, die App stabiler zu machen.</span><span class="sxs-lookup"><span data-stu-id="71ead-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="71ead-140">Reduziert die Wahrscheinlichkeit, dass ungültige Daten in der Datenbank gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="71ead-141">Benutzeroberfläche für Validierungsfehler in Razor Pages</span><span class="sxs-lookup"><span data-stu-id="71ead-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="71ead-142">Führen Sie die App aus, und navigieren Sie zu „Pages/Movies“.</span><span class="sxs-lookup"><span data-stu-id="71ead-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="71ead-143">Wählen Sie den Link **Neu Create (erstellen)** aus.</span><span class="sxs-lookup"><span data-stu-id="71ead-143">Select the **Create New** link.</span></span> <span data-ttu-id="71ead-144">Füllen Sie das Formular mit einigen ungültigen Werten aus.</span><span class="sxs-lookup"><span data-stu-id="71ead-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="71ead-145">Wenn die clientseitige jQuery-Validierung den Fehler erkennt, wird eine Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71ead-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="71ead-147">Wie Sie sehen, hat das Formular in allen Feldern mit einem ungültigen Wert automatisch eine Validierungsfehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71ead-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="71ead-148">Die Fehlermeldungen werden sowohl auf Clientseite (mithilfe von JavaScript und jQuery) als auch auf Serverseite erzwungen (wenn ein Benutzer JavaScript deaktiviert hat).</span><span class="sxs-lookup"><span data-stu-id="71ead-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="71ead-149">Ein entscheidender Vorteil ist, dass **keine** Codeänderungen auf den Seiten Create oder „Bearbeiten“ erforderlich waren.</span><span class="sxs-lookup"><span data-stu-id="71ead-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="71ead-150">Nach Anwenden von Datenanmerkungen auf das Modell wurde die Benutzeroberflächenvalidierung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="71ead-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="71ead-151">Die in diesem Tutorial erstellten Razor Pages haben die Validierungsregeln automatisch übernommen (mithilfe der Validierungsattribute für die Eigenschaften der Modellklasse `Movie`).</span><span class="sxs-lookup"><span data-stu-id="71ead-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="71ead-152">Testen Sie die Validierung mithilfe der Seite „Bearbeiten“. Es erfolgt dieselbe Validierung.</span><span class="sxs-lookup"><span data-stu-id="71ead-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="71ead-153">Die Formulardaten werden erst an den Server zurückgesendet, wenn auf Clientseite keine Validierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="71ead-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="71ead-154">Überprüfen Sie mithilfe von mindestens einem der folgenden Ansätze, ob keine Formulardaten bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="71ead-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="71ead-155">Setzen Sie einen Haltepunkt in der `OnPostAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="71ead-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="71ead-156">Senden Sie das Formular, indem Sie **Create** oder **Speichern** auswählen.</span><span class="sxs-lookup"><span data-stu-id="71ead-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="71ead-157">Der Haltepunkt wird niemals erreicht.</span><span class="sxs-lookup"><span data-stu-id="71ead-157">The break point is never hit.</span></span>
* <span data-ttu-id="71ead-158">Verwenden Sie das [Tool Fiddler](https://www.telerik.com/fiddler).</span><span class="sxs-lookup"><span data-stu-id="71ead-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="71ead-159">Verwenden Sie die Browserentwicklungstools zum Überwachen des Netzwerkdatenverkehrs.</span><span class="sxs-lookup"><span data-stu-id="71ead-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="71ead-160">Serverseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="71ead-160">Server-side validation</span></span>

<span data-ttu-id="71ead-161">Wenn JavaScript im Browser deaktiviert ist, erfolgt beim Senden des Formulars mit Fehlern eine Bereitstellung auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="71ead-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="71ead-162">Testen Sie optional die serverseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="71ead-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="71ead-163">Deaktivieren Sie JavaScript im Browser.</span><span class="sxs-lookup"><span data-stu-id="71ead-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="71ead-164">JavaScript kann in den Entwicklertools im Browser deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="71ead-165">Wenn JavaScript nicht im Browser deaktiviert werden kann, probieren Sie einen anderen Browser.</span><span class="sxs-lookup"><span data-stu-id="71ead-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="71ead-166">Setzen Sie auf der Seite Create oder „Bearbeiten“ in der `OnPostAsync`-Methode einen Haltepunkt.</span><span class="sxs-lookup"><span data-stu-id="71ead-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="71ead-167">Senden Sie ein Formular mit ungültigen Daten.</span><span class="sxs-lookup"><span data-stu-id="71ead-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="71ead-168">Überprüfen Sie, ob der Modellstatus ungültig ist:</span><span class="sxs-lookup"><span data-stu-id="71ead-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="71ead-169">Alternativ können Sie [die clientseitige Validierung auf dem Server deaktivieren](xref:mvc/models/validation#disable-client-side-validation).</span><span class="sxs-lookup"><span data-stu-id="71ead-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="71ead-170">Der folgende Code zeigt einen Teil der Seite *Create.cshtml*, deren Gerüst Sie zuvor im Tutorial erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="71ead-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="71ead-171">Sie wird auf den Seiten Create und „Bearbeiten“ für Folgendes verwendet:</span><span class="sxs-lookup"><span data-stu-id="71ead-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="71ead-172">Anzeigen des Ausgangsformulars</span><span class="sxs-lookup"><span data-stu-id="71ead-172">Display the initial form.</span></span>
* <span data-ttu-id="71ead-173">Erneutes Anzeigen des Formulars bei einem Fehler</span><span class="sxs-lookup"><span data-stu-id="71ead-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="71ead-174">Das [Hilfsprogramm für Eingabetags](xref:mvc/views/working-with-forms) verwendet die Attribute von [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) und generiert HTML-Attribute, die auf der Clientseite für die jQuery-Validierung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="71ead-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="71ead-175">Das [Hilfsprogramm für Validierungstags](xref:mvc/views/working-with-forms#the-validation-tag-helpers) zeigt Validierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="71ead-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="71ead-176">Weitere Informationen finden Sie unter [Validierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="71ead-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="71ead-177">Die Seiten Create und „Bearbeiten“ weisen keine Validierungsregeln auf.</span><span class="sxs-lookup"><span data-stu-id="71ead-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="71ead-178">Die Validierungsregeln und Fehlerzeichenfolgen werden nur in der `Movie`-Klasse angegeben.</span><span class="sxs-lookup"><span data-stu-id="71ead-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="71ead-179">Diese Validierungsregeln gelten automatisch für Razor-Seiten, die das Modell `Movie` bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="71ead-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="71ead-180">Wenn Validierungslogik geändert werden muss, erfolgt dies nur im Modell.</span><span class="sxs-lookup"><span data-stu-id="71ead-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="71ead-181">Die Validierung erfolgt in der gesamten Anwendung einheitlich (Validierungslogik ist zentral definiert).</span><span class="sxs-lookup"><span data-stu-id="71ead-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="71ead-182">Die zentrale Validierung unterstützt sauberen Code und erleichtert dessen Verwaltung und Aktualisierung.</span><span class="sxs-lookup"><span data-stu-id="71ead-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="71ead-183">Verwenden von DataType-Attributen</span><span class="sxs-lookup"><span data-stu-id="71ead-183">Use DataType Attributes</span></span>

<span data-ttu-id="71ead-184">Untersuchen Sie die Klasse `Movie`.</span><span class="sxs-lookup"><span data-stu-id="71ead-184">Examine the `Movie` class.</span></span> <span data-ttu-id="71ead-185">Der Namespace `System.ComponentModel.DataAnnotations` stellt zusätzlich zu der integrierten Gruppe von Validierungsattributen Formatierungsattribute bereit.</span><span class="sxs-lookup"><span data-stu-id="71ead-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="71ead-186">Das `[DataType]`-Attribut wird nur auf die Eigenschaften `ReleaseDate` und `Price` angewendet.</span><span class="sxs-lookup"><span data-stu-id="71ead-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="71ead-187">Die `[DataType]`-Attribute stellen Folgendes bereit:</span><span class="sxs-lookup"><span data-stu-id="71ead-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="71ead-188">Hinweise für die Ansichts-Engine zum Formatieren der Daten</span><span class="sxs-lookup"><span data-stu-id="71ead-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="71ead-189">Attribute wie `<a>` für URLs und `<a href="mailto:EmailAddress.com">` für E-Mail</span><span class="sxs-lookup"><span data-stu-id="71ead-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="71ead-190">Verwenden Sie das `[RegularExpression]`-Attribut, um das Format der Daten zu validieren.</span><span class="sxs-lookup"><span data-stu-id="71ead-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="71ead-191">Das `[DataType]`-Attribut wird verwendet, um einen Datentyp anzugeben, der spezifischer als der datenbankinterne Typ ist.</span><span class="sxs-lookup"><span data-stu-id="71ead-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="71ead-192">`[DataType]`-Attribute sind keine Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="71ead-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="71ead-193">In der Beispielanwendung wird nur das Datum ohne Uhrzeit angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71ead-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="71ead-194">Die `DataType`-Enumeration stellt viele Datentypen bereit, u. a. `Date`, `Time`, `PhoneNumber`, `Currency` und `EmailAddress`.</span><span class="sxs-lookup"><span data-stu-id="71ead-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="71ead-195">Die `[DataType]`-Attribute:</span><span class="sxs-lookup"><span data-stu-id="71ead-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="71ead-196">Können der Anwendung ermöglichen, typspezifische Features bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="71ead-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="71ead-197">Ein Link des Typs `mailto:` kann beispielsweise für `DataType.EmailAddress` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="71ead-198">Können in Browsern mit HTML5-Unterstützung die Datumsauswahl `DataType.Date` bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="71ead-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="71ead-199">Können HTML5-Attribute des Typs `data-` (ausgesprochen „Datadash“) ausgeben, die HTML5-Browser nutzen.</span><span class="sxs-lookup"><span data-stu-id="71ead-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="71ead-200">Bieten **keine** Validierung.</span><span class="sxs-lookup"><span data-stu-id="71ead-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="71ead-201">`DataType.Date` gibt nicht das Format des Datums an, das angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="71ead-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="71ead-202">Standardmäßig wird das Datenfeld gemäß den Standardformaten basierend auf `CultureInfo` des Servers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="71ead-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="71ead-203">Die Datenanmerkung `[Column(TypeName = "decimal(18, 2)")]` ist erforderlich, damit Entity Framework Core `Price` ordnungsgemäß einer Währung in der Datenbank zuordnen kann.</span><span class="sxs-lookup"><span data-stu-id="71ead-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="71ead-204">Weitere Informationen finden Sie unter [Datentypen](/ef/core/modeling/relational/data-types).</span><span class="sxs-lookup"><span data-stu-id="71ead-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="71ead-205">Das `[DisplayFormat]`-Attribut dient zum expliziten Angeben des Datumsformats:</span><span class="sxs-lookup"><span data-stu-id="71ead-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="71ead-206">Die Einstellung `ApplyFormatInEditMode` gibt an, dass die Formatierung angewendet wird, wenn der Wert zur Bearbeitung angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="71ead-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="71ead-207">Dieses Verhalten kann für einige Felder unerwünscht sein.</span><span class="sxs-lookup"><span data-stu-id="71ead-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="71ead-208">In Währungswerten soll beispielsweise wahrscheinlich nicht das Währungssymbol auf der Bearbeitungsoberfläche gezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="71ead-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="71ead-209">Das `[DisplayFormat]`-Attribut kann eigenständig verwendet werden, doch meist empfiehlt es sich, das `[DataType]`-Attribut zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="71ead-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="71ead-210">Das `[DataType]`-Attribut übermittelt die Semantik der Daten im Gegensatz zu deren Rendern auf einem Bildschirm.</span><span class="sxs-lookup"><span data-stu-id="71ead-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="71ead-211">Das `[DataType]`-Attribut bietet folgende Vorteile, die mit `[DisplayFormat]` nicht verfügbar sind:</span><span class="sxs-lookup"><span data-stu-id="71ead-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="71ead-212">Der Browser kann HTML5-Features aktivieren, z. B. zum Anzeigen eines Kalendersteuerelements, des dem Gebietsschema entsprechenden Währungssymbols, von E-Mail-Links usw.</span><span class="sxs-lookup"><span data-stu-id="71ead-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="71ead-213">Standardmäßig rendert der Browser Daten mit dem auf seinem Gebietsschema basierenden richtigen Format.</span><span class="sxs-lookup"><span data-stu-id="71ead-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="71ead-214">Das `[DataType]`-Attribut kann dem ASP.NET Core-Framework ermöglichen, die richtige Feldvorlage zum Rendern der Daten auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="71ead-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="71ead-215">`DisplayFormat`, falls eigenständig genutzt, verwendet die Zeichenfolgenvorlage.</span><span class="sxs-lookup"><span data-stu-id="71ead-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="71ead-216">**Hinweis:** Die jQuery-Validierung funktioniert nicht mit den Attributen `[Range]` und `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="71ead-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="71ead-217">Bei folgendem Code wird z.B. stets ein clientseitiger Validierungsfehler angezeigt, auch wenn sich das Datum im angegebenen Bereich befindet:</span><span class="sxs-lookup"><span data-stu-id="71ead-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="71ead-218">Es ist eine bewährte Methode, keine festen Datumsangaben in Ihren Modellen zu kompilieren, weshalb vom Einsatz des `[Range]`-Attributs und von `DateTime` abgeraten wird.</span><span class="sxs-lookup"><span data-stu-id="71ead-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="71ead-219">Verwenden Sie den Abschnitt [Configuration](xref:fundamentals/configuration/index) für Datumsbereiche und andere Werte, die häufig geändert werden, anstatt sie im Code anzugeben.</span><span class="sxs-lookup"><span data-stu-id="71ead-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="71ead-220">Der folgende Code zeigt die Kombination von Attributen in einer Zeile:</span><span class="sxs-lookup"><span data-stu-id="71ead-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="71ead-221">Unter [Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) werden erweiterte EF Core-Vorgänge mit Razor Pages erläutert.</span><span class="sxs-lookup"><span data-stu-id="71ead-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="71ead-222">Anwenden von Migrationen</span><span class="sxs-lookup"><span data-stu-id="71ead-222">Apply migrations</span></span>

<span data-ttu-id="71ead-223">Durch die Anwendung von „DataAnnotations“ auf die Klasse wird das Schema geändert.</span><span class="sxs-lookup"><span data-stu-id="71ead-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="71ead-224">Beispielsweise ergeben sich durch die Anwendung von DataAnnotations auf das `Title`-Feld folgende Änderungen:</span><span class="sxs-lookup"><span data-stu-id="71ead-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="71ead-225">Die Zeichen werden auf 60 begrenzt.</span><span class="sxs-lookup"><span data-stu-id="71ead-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="71ead-226">Ein `null`-Wert ist unzulässig.</span><span class="sxs-lookup"><span data-stu-id="71ead-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="71ead-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71ead-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="71ead-228">Die `Movie`-Tabelle verfügt zurzeit über das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="71ead-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="71ead-229">Die vorstehenden Schemaänderungen bewirken nicht, dass EF eine Ausnahme auslöst.</span><span class="sxs-lookup"><span data-stu-id="71ead-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="71ead-230">Erstellen Sie jedoch eine Migration, damit das Schema mit dem Modell konsistent ist.</span><span class="sxs-lookup"><span data-stu-id="71ead-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="71ead-231">Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.</span><span class="sxs-lookup"><span data-stu-id="71ead-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="71ead-232">Geben Sie in der PMC die folgenden Befehle ein:</span><span class="sxs-lookup"><span data-stu-id="71ead-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="71ead-233">`Update-Database` führt die `Up`-Methoden der `New_DataAnnotations`-Klasse aus.</span><span class="sxs-lookup"><span data-stu-id="71ead-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="71ead-234">Untersuchen Sie die `Up`-Methode.</span><span class="sxs-lookup"><span data-stu-id="71ead-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="71ead-235">Die aktualisierte `Movie`-Tabelle hat das folgende Schema:</span><span class="sxs-lookup"><span data-stu-id="71ead-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="71ead-236">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="71ead-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="71ead-237">Für SQLite sind keine Migrationen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="71ead-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="71ead-238">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="71ead-238">Publish to Azure</span></span>

<span data-ttu-id="71ead-239">Informationen zum Bereitstellen in Azure finden Sie unter [Tutorial: Erstellen einer ASP.NET Core-App in Azure mit SQL-Datenbank](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span><span class="sxs-lookup"><span data-stu-id="71ead-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="71ead-240">Vielen Dank für Ihr Interesse an dieser Einführung in Razor Pages.</span><span class="sxs-lookup"><span data-stu-id="71ead-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="71ead-241">[Erste Schritte mit Razor Pages und EF Core](xref:data/ef-rp/intro) ist ein ausgezeichneter Anschlussartikel an dieses Tutorial.</span><span class="sxs-lookup"><span data-stu-id="71ead-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71ead-242">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71ead-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="71ead-243">Zurück: Hinzufügen eines neuen Felds</span><span class="sxs-lookup"><span data-stu-id="71ead-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
