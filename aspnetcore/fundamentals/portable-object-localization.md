---
title: Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core
author: sebastienros
description: Dieser Artikel führt portable Objektdateien ein und erläutert die Schritte zu ihrer Verwendung in einer ASP.NET Core-Anwendung mit dem Framework Orchard Core.
ms.author: scaddie
ms.date: 09/26/2017
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
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 175614c426c564ce91068e18035ce05311432698
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689239"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="bf419-103">Konfigurieren der Lokalisierung portabler Objekte in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf419-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bf419-104">Von [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) und [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="bf419-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="bf419-105">Dieser Artikel erläutert die Schritte zur Verwendung von portablen Objektdateien (PO) in einer ASP.NET Core-Anwendung mit dem Framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="bf419-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="bf419-106">**Hinweis**: Orchard Core ist kein Microsoft-Produkt.</span><span class="sxs-lookup"><span data-stu-id="bf419-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="bf419-107">Microsoft bietet daher keinen Support für dieses Feature an.</span><span class="sxs-lookup"><span data-stu-id="bf419-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="bf419-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf419-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="bf419-109">Was ist eine PO-Datei?</span><span class="sxs-lookup"><span data-stu-id="bf419-109">What is a PO file?</span></span>

<span data-ttu-id="bf419-110">PO-Dateien werden als Textdateien verteilt, die übersetzte Zeichenfolgen für eine bestimmte Sprache enthalten.</span><span class="sxs-lookup"><span data-stu-id="bf419-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="bf419-111">Einige Vorteile der Verwendung von PO-Dateien im Vergleich zu *RESX*-Dateien sind:</span><span class="sxs-lookup"><span data-stu-id="bf419-111">Some advantages of using PO files instead of *.resx* files include:</span></span>
- <span data-ttu-id="bf419-112">PO-Dateien unterstützen Pluralisierung; *RESX*-Dateien unterstützen sie nicht.</span><span class="sxs-lookup"><span data-stu-id="bf419-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="bf419-113">PO-Dateien werden nicht wie *RESX*-Dateien kompiliert.</span><span class="sxs-lookup"><span data-stu-id="bf419-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="bf419-114">Daher sind spezialisierte Tools und die Buildschritte nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bf419-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="bf419-115">PO-Dateien funktionieren gut mit gemeinsamen Onlinebearbeitungstools.</span><span class="sxs-lookup"><span data-stu-id="bf419-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="bf419-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="bf419-116">Example</span></span>

<span data-ttu-id="bf419-117">Hier sehen Sie eine PO-Beispieldatei, die die Übersetzung für zwei Zeichenfolgen in Französisch, einschließlich der Pluralform einer der Zeichenfolgen enthält:</span><span class="sxs-lookup"><span data-stu-id="bf419-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="bf419-118">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="bf419-118">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="bf419-119">In diesem Beispiel wird die folgende Syntax verwendet:</span><span class="sxs-lookup"><span data-stu-id="bf419-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="bf419-120">`#:`: ein Kommentar, der den Kontext der zu übersetzenden Zeichenfolge angibt.</span><span class="sxs-lookup"><span data-stu-id="bf419-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="bf419-121">Die gleiche Zeichenfolge wird möglicherweise anders übersetzt, je nachdem, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf419-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="bf419-122">`msgid`: die nicht übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="bf419-123">`msgstr`: die übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="bf419-124">Wenn die Pluralisierung unterstützt wird, können mehrere Einträge definiert werden.</span><span class="sxs-lookup"><span data-stu-id="bf419-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="bf419-125">`msgid_plural`: die nicht übersetzte Zeichenfolge im Plural.</span><span class="sxs-lookup"><span data-stu-id="bf419-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="bf419-126">`msgstr[0]`: die übersetzte Zeichenfolge für den Fall 0.</span><span class="sxs-lookup"><span data-stu-id="bf419-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="bf419-127">`msgstr[N]`: die übersetzte Zeichenfolge für den Fall N.</span><span class="sxs-lookup"><span data-stu-id="bf419-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="bf419-128">Die PO-Dateispezifikation finden Sie [hier](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="bf419-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="bf419-129">Konfigurieren der PO-Dateiunterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf419-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="bf419-130">Dieses Beispiel beruht auf einer ASP.NET Core MVC-Anwendung, die aus einer Visual Studio 2017-Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="bf419-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="bf419-131">Verweisen auf das Paket</span><span class="sxs-lookup"><span data-stu-id="bf419-131">Referencing the package</span></span>

<span data-ttu-id="bf419-132">Fügen Sie einen Verweis auf das NuGet-Paket `OrchardCore.Localization.Core` hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf419-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="bf419-133">Die *CSPROJ*-Datei enthält nun eine Zeile, die der folgenden Zeile ähnelt (die Versionsnummer kann davon abweichen):</span><span class="sxs-lookup"><span data-stu-id="bf419-133">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="bf419-134">Registrieren des Diensts</span><span class="sxs-lookup"><span data-stu-id="bf419-134">Registering the service</span></span>

<span data-ttu-id="bf419-135">Fügen Sie die erforderlichen Dienste zur `ConfigureServices`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-135">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="bf419-136">Fügen Sie die erforderliche Middleware zur `Configure`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-136">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="bf419-137">Fügen Sie den folgenden Code zur Razor-Ansicht Ihrer Wahl hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf419-137">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="bf419-138">In diesem Beispiel wird *About.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-138">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="bf419-139">Eine `IViewLocalizer`-Instanz wird eingefügt und zum Übersetzen des Texts „Hello world!“ verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-139">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="bf419-140">Erstellen einer PO-Datei</span><span class="sxs-lookup"><span data-stu-id="bf419-140">Creating a PO file</span></span>

<span data-ttu-id="bf419-141">Erstellen Sie eine Datei mit dem Namen *\<culture code>.po* im Stammordner Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="bf419-141">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="bf419-142">In diesem Beispiel lautet der Dateiname *fr.po*, weil die französische Sprache verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="bf419-142">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="bf419-143">Diese Datei speichert die zu übersetzende Zeichenfolge sowie die französische Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-143">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="bf419-144">Übersetzungen stellen ihre übergeordnete Kultur wieder her, wenn erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bf419-144">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="bf419-145">In diesem Beispiel wird die *fr.po*-Datei verwendet, wenn die angeforderte Kultur `fr-FR` oder `fr-CA` ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-145">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="bf419-146">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="bf419-146">Testing the application</span></span>

<span data-ttu-id="bf419-147">Führen Sie Ihre Anwendung aus, und navigieren Sie zur URL`/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="bf419-147">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="bf419-148">Der Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="bf419-148">The text **Hello world!**</span></span> <span data-ttu-id="bf419-149">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf419-149">is displayed.</span></span>

<span data-ttu-id="bf419-150">Navigieren Sie zur URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="bf419-150">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="bf419-151">Der Text **Bonjour le Monde!**</span><span class="sxs-lookup"><span data-stu-id="bf419-151">The text **Bonjour le monde!**</span></span> <span data-ttu-id="bf419-152">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf419-152">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="bf419-153">Pluralisierung</span><span class="sxs-lookup"><span data-stu-id="bf419-153">Pluralization</span></span>

<span data-ttu-id="bf419-154">PO-Dateien unterstützen Pluralformen. Dies ist nützlich, wenn die gleiche Zeichenfolge aufgrund einer Kardinalität unterschiedlich übersetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="bf419-154">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="bf419-155">Diese Aufgabe ist komplizierter, da jede Sprache benutzerdefinierte Regeln festlegt, um auf der Grundlage von Kardinalität auszuwählen, welche Zeichenfolge zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-155">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="bf419-156">Das Orchard Localization-Paket stellt eine API zur Verfügung, um diese anderen Pluralformen automatisch aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="bf419-156">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="bf419-157">Erstellen von PO-Pluraldateien</span><span class="sxs-lookup"><span data-stu-id="bf419-157">Creating pluralization PO files</span></span>

<span data-ttu-id="bf419-158">Fügen Sie den folgenden Inhalt zur zuvor erwähnten *fr.po*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-158">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="bf419-159">Unter [Was ist eine PO-Datei?](#what-is-a-po-file) finden Sie eine Erläuterung für das, was jeder Eintrag in diesem Beispiel darstellt.</span><span class="sxs-lookup"><span data-stu-id="bf419-159">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="bf419-160">Hinzufügen einer Sprache mit verschiedenen Pluralformen</span><span class="sxs-lookup"><span data-stu-id="bf419-160">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="bf419-161">Im vorherigen Beispiel wurden englische und französische Zeichenfolgen verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-161">English and French strings were used in the previous example.</span></span> <span data-ttu-id="bf419-162">Englisch und Französisch haben nur zwei Pluralformen und teilen die gleichen Formregeln. Die Kardinalität der einen wird der ersten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="bf419-162">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="bf419-163">Alle anderen Kardinalitäten werden der zweiten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="bf419-163">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="bf419-164">Nicht alle Sprachen verfügen über die gleichen Regeln.</span><span class="sxs-lookup"><span data-stu-id="bf419-164">Not all languages share the same rules.</span></span> <span data-ttu-id="bf419-165">Tschechisch verfügt beispielsweise über drei Pluralformen.</span><span class="sxs-lookup"><span data-stu-id="bf419-165">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="bf419-166">Erstellen Sie die `cs.po`-Datei wie folgt, und beachten Sie die drei verschiedenen Übersetzungen der Pluralformen:</span><span class="sxs-lookup"><span data-stu-id="bf419-166">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="bf419-167">Fügen Sie für die Annahme der tschechischen Lokalisierungen `"cs"` zur Liste der unterstützten Kulturen in der `ConfigureServices`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-167">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="bf419-168">Bearbeiten Sie die *Views/Home/About.cshtml*-Datei zum Rendern von lokalisierten Pluralzeichenfolgen für mehrere Kardinalitäten:</span><span class="sxs-lookup"><span data-stu-id="bf419-168">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="bf419-169">**Hinweis**: In einem echten Szenario würde eine Variable die Anzahl darstellen.</span><span class="sxs-lookup"><span data-stu-id="bf419-169">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="bf419-170">Hier wiederholen wir den gleichen Code mit drei verschiedenen Werten, um einen sehr speziellen Fall verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="bf419-170">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="bf419-171">Beim Wechsel von Kulturen sehen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bf419-171">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="bf419-172">Für `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="bf419-172">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="bf419-173">Für `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="bf419-173">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="bf419-174">Für `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="bf419-174">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="bf419-175">Beachten Sie, dass die drei Übersetzungen für die tschechische Kultur unterschiedlich sind.</span><span class="sxs-lookup"><span data-stu-id="bf419-175">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="bf419-176">Die französische und englische Kultur teilen die gleiche Konstruktion für die beiden letzten übersetzten Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="bf419-176">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="bf419-177">Aufgaben für Fortgeschrittene</span><span class="sxs-lookup"><span data-stu-id="bf419-177">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="bf419-178">Kontextualisieren von Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="bf419-178">Contextualizing strings</span></span>

<span data-ttu-id="bf419-179">Anwendungen enthalten die zu übersetzenden Zeichenfolgen häufig an mehreren Stellen.</span><span class="sxs-lookup"><span data-stu-id="bf419-179">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="bf419-180">Dieselbe Zeichenfolge kann möglicherweise über unterschiedliche Übersetzungen in bestimmten Speicherorten innerhalb einer Anwendung verfügen (Razor-Ansichten oder Klassendateien).</span><span class="sxs-lookup"><span data-stu-id="bf419-180">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="bf419-181">Eine PO-Datei unterstützt das Konzept eines Dateikontexts, der zum Kategorisieren von dargestellten Zeichenfolgen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bf419-181">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="bf419-182">Bei der Verwendung eines Dateikontexts kann eine Zeichenfolge unterschiedlich übersetzt werden. Dies hängt vom Dateikontext (oder einem fehlenden Dateikontext) ab.</span><span class="sxs-lookup"><span data-stu-id="bf419-182">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="bf419-183">Die PO-Lokalisierungsdienste verwenden den Namen der vollständigen Klasse oder der Ansicht, die bei der Übersetzung einer Zeichenfolge verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf419-183">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="bf419-184">Dies wird durch Festlegen des Werts im `msgctxt`-Eintrag erreicht.</span><span class="sxs-lookup"><span data-stu-id="bf419-184">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="bf419-185">Ziehen Sie eine geringfügige Hinzufügung zum vorherigen *fr.po*-Beispiel in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="bf419-185">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="bf419-186">Eine Razor-Ansicht in *Views/Home/About.cshtml* kann als Dateikontext definiert werden, indem der reservierte `msgctxt`-Eintragswert festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="bf419-186">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="bf419-187">Wenn `msgctxt` festgelegt ist, wird bei der Navigation zu `/Home/About?culture=fr-FR` eine Textübersetzung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bf419-187">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="bf419-188">Die Übersetzung wird nicht bei der Navigation zu `/Home/Contact?culture=fr-FR` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bf419-188">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="bf419-189">Wenn kein bestimmter Eintrag mit einem bestimmten Dateikontext abgeglichen wird, sucht der Fallbackmechanismus von Orchard Core nach einer entsprechenden PO-Datei ohne Kontext.</span><span class="sxs-lookup"><span data-stu-id="bf419-189">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="bf419-190">Vorausgesetzt, dass kein bestimmter Dateikontext für *Views/Home/Contact.cshtml* definiert ist, lädt eine Navigation zu `/Home/Contact?culture=fr-FR` eine PO-Datei, wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="bf419-190">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="bf419-191">Ändern des Speicherorts für PO-Dateien</span><span class="sxs-lookup"><span data-stu-id="bf419-191">Changing the location of PO files</span></span>

<span data-ttu-id="bf419-192">Der Standardspeicherort der PO-Dateien kann in `ConfigureServices` geändert werden:</span><span class="sxs-lookup"><span data-stu-id="bf419-192">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="bf419-193">In diesem Beispiel werden die PO-Dateien aus dem Ordner *Lokalisierung* geladen.</span><span class="sxs-lookup"><span data-stu-id="bf419-193">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="bf419-194">Implementieren einer benutzerdefinierten Logik für die Suche nach Lokalisierungsdateien</span><span class="sxs-lookup"><span data-stu-id="bf419-194">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="bf419-195">Wenn eine komplexere Logik erforderlich ist, um die PO-Dateien zu finden, kann die `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`-Schnittstelle implementiert und als Dienst registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bf419-195">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="bf419-196">Dies ist nützlich, wenn PO-Dateien an unterschiedlichen Speicherorten gespeichert oder die Dateien innerhalb einer Hierarchie von Ordnern gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="bf419-196">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="bf419-197">Verwenden einer anderen Standardpluralsprache</span><span class="sxs-lookup"><span data-stu-id="bf419-197">Using a different default pluralized language</span></span>

<span data-ttu-id="bf419-198">Das Paket enthält eine `Plural`-Erweiterungsmethode, die für zwei Pluralformen spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-198">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="bf419-199">Erstellen Sie eine Erweiterungsmethode für Sprachen, die weitere Pluralformen erfordern.</span><span class="sxs-lookup"><span data-stu-id="bf419-199">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="bf419-200">Mit einer Erweiterungsmethode müssen Sie keine Lokalisierungsdatei für die Standardsprache angeben &mdash; die ursprünglichen Zeichenfolgen sind bereits direkt im Code verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bf419-200">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="bf419-201">Sie können die generische `Plural(int count, string[] pluralForms, params object[] arguments)`-Überladung verwenden, die ein Zeichenfolgenarray von Übersetzungen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="bf419-201">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bf419-202">Von [Sébastien Ros](https://github.com/sebastienros) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="bf419-202">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="bf419-203">Dieser Artikel erläutert die Schritte zur Verwendung von portablen Objektdateien (PO) in einer ASP.NET Core-Anwendung mit dem Framework [Orchard Core](https://github.com/OrchardCMS/OrchardCore).</span><span class="sxs-lookup"><span data-stu-id="bf419-203">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="bf419-204">**Hinweis**: Orchard Core ist kein Microsoft-Produkt.</span><span class="sxs-lookup"><span data-stu-id="bf419-204">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="bf419-205">Microsoft bietet daher keinen Support für dieses Feature an.</span><span class="sxs-lookup"><span data-stu-id="bf419-205">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="bf419-206">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bf419-206">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="bf419-207">Was ist eine PO-Datei?</span><span class="sxs-lookup"><span data-stu-id="bf419-207">What is a PO file?</span></span>

<span data-ttu-id="bf419-208">PO-Dateien werden als Textdateien verteilt, die übersetzte Zeichenfolgen für eine bestimmte Sprache enthalten.</span><span class="sxs-lookup"><span data-stu-id="bf419-208">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="bf419-209">Einige Vorteile der Verwendung von PO-Dateien im Vergleich zu *RESX*-Dateien sind:</span><span class="sxs-lookup"><span data-stu-id="bf419-209">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="bf419-210">PO-Dateien unterstützen Pluralisierung; *RESX*-Dateien unterstützen sie nicht.</span><span class="sxs-lookup"><span data-stu-id="bf419-210">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="bf419-211">PO-Dateien werden nicht wie *RESX*-Dateien kompiliert.</span><span class="sxs-lookup"><span data-stu-id="bf419-211">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="bf419-212">Daher sind spezialisierte Tools und die Buildschritte nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bf419-212">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="bf419-213">PO-Dateien funktionieren gut mit gemeinsamen Onlinebearbeitungstools.</span><span class="sxs-lookup"><span data-stu-id="bf419-213">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="bf419-214">Beispiel</span><span class="sxs-lookup"><span data-stu-id="bf419-214">Example</span></span>

<span data-ttu-id="bf419-215">Hier sehen Sie eine PO-Beispieldatei, die die Übersetzung für zwei Zeichenfolgen in Französisch, einschließlich der Pluralform einer der Zeichenfolgen enthält:</span><span class="sxs-lookup"><span data-stu-id="bf419-215">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="bf419-216">*fr.po*</span><span class="sxs-lookup"><span data-stu-id="bf419-216">*fr.po*</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="bf419-217">In diesem Beispiel wird die folgende Syntax verwendet:</span><span class="sxs-lookup"><span data-stu-id="bf419-217">This example uses the following syntax:</span></span>

- <span data-ttu-id="bf419-218">`#:`: ein Kommentar, der den Kontext der zu übersetzenden Zeichenfolge angibt.</span><span class="sxs-lookup"><span data-stu-id="bf419-218">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="bf419-219">Die gleiche Zeichenfolge wird möglicherweise anders übersetzt, je nachdem, wo sie verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf419-219">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="bf419-220">`msgid`: die nicht übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-220">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="bf419-221">`msgstr`: die übersetzte Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-221">`msgstr`: The translated string.</span></span>

<span data-ttu-id="bf419-222">Wenn die Pluralisierung unterstützt wird, können mehrere Einträge definiert werden.</span><span class="sxs-lookup"><span data-stu-id="bf419-222">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="bf419-223">`msgid_plural`: die nicht übersetzte Zeichenfolge im Plural.</span><span class="sxs-lookup"><span data-stu-id="bf419-223">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="bf419-224">`msgstr[0]`: die übersetzte Zeichenfolge für den Fall 0.</span><span class="sxs-lookup"><span data-stu-id="bf419-224">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="bf419-225">`msgstr[N]`: die übersetzte Zeichenfolge für den Fall N.</span><span class="sxs-lookup"><span data-stu-id="bf419-225">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="bf419-226">Die PO-Dateispezifikation finden Sie [hier](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span><span class="sxs-lookup"><span data-stu-id="bf419-226">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="bf419-227">Konfigurieren der PO-Dateiunterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="bf419-227">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="bf419-228">Dieses Beispiel beruht auf einer ASP.NET Core MVC-Anwendung, die aus einer Visual Studio 2017-Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="bf419-228">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="bf419-229">Verweisen auf das Paket</span><span class="sxs-lookup"><span data-stu-id="bf419-229">Referencing the package</span></span>

<span data-ttu-id="bf419-230">Fügen Sie einen Verweis auf das NuGet-Paket `OrchardCore.Localization.Core` hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf419-230">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

<span data-ttu-id="bf419-231">Die *CSPROJ*-Datei enthält nun eine Zeile, die der folgenden Zeile ähnelt (die Versionsnummer kann davon abweichen):</span><span class="sxs-lookup"><span data-stu-id="bf419-231">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="bf419-232">Registrieren des Diensts</span><span class="sxs-lookup"><span data-stu-id="bf419-232">Registering the service</span></span>

<span data-ttu-id="bf419-233">Fügen Sie die erforderlichen Dienste zur `ConfigureServices`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-233">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="bf419-234">Fügen Sie die erforderliche Middleware zur `Configure`-Methode von *Startup.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-234">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="bf419-235">Fügen Sie den folgenden Code zur Razor-Ansicht Ihrer Wahl hinzu.</span><span class="sxs-lookup"><span data-stu-id="bf419-235">Add the following code to your Razor view of choice.</span></span> <span data-ttu-id="bf419-236">In diesem Beispiel wird *About.cshtml* verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-236">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="bf419-237">Eine `IViewLocalizer`-Instanz wird eingefügt und zum Übersetzen des Texts „Hello world!“ verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-237">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="bf419-238">Erstellen einer PO-Datei</span><span class="sxs-lookup"><span data-stu-id="bf419-238">Creating a PO file</span></span>

<span data-ttu-id="bf419-239">Erstellen Sie eine Datei mit dem Namen *\<culture code>.po* im Stammordner Ihrer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="bf419-239">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="bf419-240">In diesem Beispiel lautet der Dateiname *fr.po*, weil die französische Sprache verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="bf419-240">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="bf419-241">Diese Datei speichert die zu übersetzende Zeichenfolge sowie die französische Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="bf419-241">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="bf419-242">Übersetzungen stellen ihre übergeordnete Kultur wieder her, wenn erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bf419-242">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="bf419-243">In diesem Beispiel wird die *fr.po*-Datei verwendet, wenn die angeforderte Kultur `fr-FR` oder `fr-CA` ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-243">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="bf419-244">Testen der Anwendung</span><span class="sxs-lookup"><span data-stu-id="bf419-244">Testing the application</span></span>

<span data-ttu-id="bf419-245">Führen Sie Ihre Anwendung aus, und navigieren Sie zur URL`/Home/About`.</span><span class="sxs-lookup"><span data-stu-id="bf419-245">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="bf419-246">Der Text **Hello world!**</span><span class="sxs-lookup"><span data-stu-id="bf419-246">The text **Hello world!**</span></span> <span data-ttu-id="bf419-247">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf419-247">is displayed.</span></span>

<span data-ttu-id="bf419-248">Navigieren Sie zur URL `/Home/About?culture=fr-FR`.</span><span class="sxs-lookup"><span data-stu-id="bf419-248">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="bf419-249">Der Text **Bonjour le Monde!**</span><span class="sxs-lookup"><span data-stu-id="bf419-249">The text **Bonjour le monde!**</span></span> <span data-ttu-id="bf419-250">wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="bf419-250">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="bf419-251">Pluralisierung</span><span class="sxs-lookup"><span data-stu-id="bf419-251">Pluralization</span></span>

<span data-ttu-id="bf419-252">PO-Dateien unterstützen Pluralformen. Dies ist nützlich, wenn die gleiche Zeichenfolge aufgrund einer Kardinalität unterschiedlich übersetzt werden muss.</span><span class="sxs-lookup"><span data-stu-id="bf419-252">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="bf419-253">Diese Aufgabe ist komplizierter, da jede Sprache benutzerdefinierte Regeln festlegt, um auf der Grundlage von Kardinalität auszuwählen, welche Zeichenfolge zu verwenden ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-253">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="bf419-254">Das Orchard Localization-Paket stellt eine API zur Verfügung, um diese anderen Pluralformen automatisch aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="bf419-254">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="bf419-255">Erstellen von PO-Pluraldateien</span><span class="sxs-lookup"><span data-stu-id="bf419-255">Creating pluralization PO files</span></span>

<span data-ttu-id="bf419-256">Fügen Sie den folgenden Inhalt zur zuvor erwähnten *fr.po*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-256">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="bf419-257">Unter [Was ist eine PO-Datei?](#what-is-a-po-file) finden Sie eine Erläuterung für das, was jeder Eintrag in diesem Beispiel darstellt.</span><span class="sxs-lookup"><span data-stu-id="bf419-257">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="bf419-258">Hinzufügen einer Sprache mit verschiedenen Pluralformen</span><span class="sxs-lookup"><span data-stu-id="bf419-258">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="bf419-259">Im vorherigen Beispiel wurden englische und französische Zeichenfolgen verwendet.</span><span class="sxs-lookup"><span data-stu-id="bf419-259">English and French strings were used in the previous example.</span></span> <span data-ttu-id="bf419-260">Englisch und Französisch haben nur zwei Pluralformen und teilen die gleichen Formregeln. Die Kardinalität der einen wird der ersten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="bf419-260">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="bf419-261">Alle anderen Kardinalitäten werden der zweiten Pluralform zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="bf419-261">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="bf419-262">Nicht alle Sprachen verfügen über die gleichen Regeln.</span><span class="sxs-lookup"><span data-stu-id="bf419-262">Not all languages share the same rules.</span></span> <span data-ttu-id="bf419-263">Tschechisch verfügt beispielsweise über drei Pluralformen.</span><span class="sxs-lookup"><span data-stu-id="bf419-263">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="bf419-264">Erstellen Sie die `cs.po`-Datei wie folgt, und beachten Sie die drei verschiedenen Übersetzungen der Pluralformen:</span><span class="sxs-lookup"><span data-stu-id="bf419-264">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="bf419-265">Fügen Sie für die Annahme der tschechischen Lokalisierungen `"cs"` zur Liste der unterstützten Kulturen in der `ConfigureServices`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="bf419-265">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="bf419-266">Bearbeiten Sie die *Views/Home/About.cshtml*-Datei zum Rendern von lokalisierten Pluralzeichenfolgen für mehrere Kardinalitäten:</span><span class="sxs-lookup"><span data-stu-id="bf419-266">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="bf419-267">**Hinweis**: In einem echten Szenario würde eine Variable die Anzahl darstellen.</span><span class="sxs-lookup"><span data-stu-id="bf419-267">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="bf419-268">Hier wiederholen wir den gleichen Code mit drei verschiedenen Werten, um einen sehr speziellen Fall verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="bf419-268">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="bf419-269">Beim Wechsel von Kulturen sehen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="bf419-269">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="bf419-270">Für `/Home/About`:</span><span class="sxs-lookup"><span data-stu-id="bf419-270">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="bf419-271">Für `/Home/About?culture=fr`:</span><span class="sxs-lookup"><span data-stu-id="bf419-271">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="bf419-272">Für `/Home/About?culture=cs`:</span><span class="sxs-lookup"><span data-stu-id="bf419-272">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="bf419-273">Beachten Sie, dass die drei Übersetzungen für die tschechische Kultur unterschiedlich sind.</span><span class="sxs-lookup"><span data-stu-id="bf419-273">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="bf419-274">Die französische und englische Kultur teilen die gleiche Konstruktion für die beiden letzten übersetzten Zeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="bf419-274">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="bf419-275">Aufgaben für Fortgeschrittene</span><span class="sxs-lookup"><span data-stu-id="bf419-275">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="bf419-276">Kontextualisieren von Zeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="bf419-276">Contextualizing strings</span></span>

<span data-ttu-id="bf419-277">Anwendungen enthalten die zu übersetzenden Zeichenfolgen häufig an mehreren Stellen.</span><span class="sxs-lookup"><span data-stu-id="bf419-277">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="bf419-278">Dieselbe Zeichenfolge kann möglicherweise über unterschiedliche Übersetzungen in bestimmten Speicherorten innerhalb einer Anwendung verfügen (Razor-Ansichten oder Klassendateien).</span><span class="sxs-lookup"><span data-stu-id="bf419-278">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="bf419-279">Eine PO-Datei unterstützt das Konzept eines Dateikontexts, der zum Kategorisieren von dargestellten Zeichenfolgen verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="bf419-279">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="bf419-280">Bei der Verwendung eines Dateikontexts kann eine Zeichenfolge unterschiedlich übersetzt werden. Dies hängt vom Dateikontext (oder einem fehlenden Dateikontext) ab.</span><span class="sxs-lookup"><span data-stu-id="bf419-280">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="bf419-281">Die PO-Lokalisierungsdienste verwenden den Namen der vollständigen Klasse oder der Ansicht, die bei der Übersetzung einer Zeichenfolge verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bf419-281">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="bf419-282">Dies wird durch Festlegen des Werts im `msgctxt`-Eintrag erreicht.</span><span class="sxs-lookup"><span data-stu-id="bf419-282">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="bf419-283">Ziehen Sie eine geringfügige Hinzufügung zum vorherigen *fr.po*-Beispiel in Erwägung.</span><span class="sxs-lookup"><span data-stu-id="bf419-283">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="bf419-284">Eine Razor-Ansicht in *Views/Home/About.cshtml* kann als Dateikontext definiert werden, indem der reservierte `msgctxt`-Eintragswert festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="bf419-284">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="bf419-285">Wenn `msgctxt` festgelegt ist, wird bei der Navigation zu `/Home/About?culture=fr-FR` eine Textübersetzung ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bf419-285">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="bf419-286">Die Übersetzung wird nicht bei der Navigation zu `/Home/Contact?culture=fr-FR` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bf419-286">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="bf419-287">Wenn kein bestimmter Eintrag mit einem bestimmten Dateikontext abgeglichen wird, sucht der Fallbackmechanismus von Orchard Core nach einer entsprechenden PO-Datei ohne Kontext.</span><span class="sxs-lookup"><span data-stu-id="bf419-287">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="bf419-288">Vorausgesetzt, dass kein bestimmter Dateikontext für *Views/Home/Contact.cshtml* definiert ist, lädt eine Navigation zu `/Home/Contact?culture=fr-FR` eine PO-Datei, wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="bf419-288">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="bf419-289">Ändern des Speicherorts für PO-Dateien</span><span class="sxs-lookup"><span data-stu-id="bf419-289">Changing the location of PO files</span></span>

<span data-ttu-id="bf419-290">Der Standardspeicherort der PO-Dateien kann in `ConfigureServices` geändert werden:</span><span class="sxs-lookup"><span data-stu-id="bf419-290">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="bf419-291">In diesem Beispiel werden die PO-Dateien aus dem Ordner *Lokalisierung* geladen.</span><span class="sxs-lookup"><span data-stu-id="bf419-291">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="bf419-292">Implementieren einer benutzerdefinierten Logik für die Suche nach Lokalisierungsdateien</span><span class="sxs-lookup"><span data-stu-id="bf419-292">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="bf419-293">Wenn eine komplexere Logik erforderlich ist, um die PO-Dateien zu finden, kann die `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider`-Schnittstelle implementiert und als Dienst registriert werden.</span><span class="sxs-lookup"><span data-stu-id="bf419-293">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="bf419-294">Dies ist nützlich, wenn PO-Dateien an unterschiedlichen Speicherorten gespeichert oder die Dateien innerhalb einer Hierarchie von Ordnern gefunden werden können.</span><span class="sxs-lookup"><span data-stu-id="bf419-294">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="bf419-295">Verwenden einer anderen Standardpluralsprache</span><span class="sxs-lookup"><span data-stu-id="bf419-295">Using a different default pluralized language</span></span>

<span data-ttu-id="bf419-296">Das Paket enthält eine `Plural`-Erweiterungsmethode, die für zwei Pluralformen spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="bf419-296">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="bf419-297">Erstellen Sie eine Erweiterungsmethode für Sprachen, die weitere Pluralformen erfordern.</span><span class="sxs-lookup"><span data-stu-id="bf419-297">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="bf419-298">Mit einer Erweiterungsmethode müssen Sie keine Lokalisierungsdatei für die Standardsprache angeben &mdash; die ursprünglichen Zeichenfolgen sind bereits direkt im Code verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bf419-298">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="bf419-299">Sie können die generische `Plural(int count, string[] pluralForms, params object[] arguments)`-Überladung verwenden, die ein Zeichenfolgenarray von Übersetzungen akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="bf419-299">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end
