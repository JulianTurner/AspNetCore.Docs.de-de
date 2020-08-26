---
title: Razor-Syntax Referenz für ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über Razor Markup Syntax zum Einbetten von Server basiertem Code in Webseiten.
ms.author: riande
ms.date: 02/12/2020
no-loc:
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
uid: mvc/views/razor
ms.openlocfilehash: e42ee7a80475e784ea4791395d9b0665aca94a03
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865173"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a><span data-ttu-id="904e5-103">Razor Syntax Verweis für ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="904e5-103">Razor syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="904e5-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)und [Dan vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="904e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

<span data-ttu-id="904e5-105">Razor ist eine Markup Syntax zum Einbetten von Server basiertem Code in Webseiten.</span><span class="sxs-lookup"><span data-stu-id="904e5-105">Razor is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="904e5-106">Die Razor Syntax besteht aus Razor Markup, c# und HTML.</span><span class="sxs-lookup"><span data-stu-id="904e5-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="904e5-107">Dateien mit Razor der Dateierweiterung " *. cshtml* ".</span><span class="sxs-lookup"><span data-stu-id="904e5-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> <span data-ttu-id="904e5-108">Razorwird auch in [ Razor Komponenten](xref:blazor/components/index) Dateien (*Razor*-Dateien) gefunden.</span><span class="sxs-lookup"><span data-stu-id="904e5-108">Razor is also found in [Razor components](xref:blazor/components/index) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="904e5-109">Rendern von HTML</span><span class="sxs-lookup"><span data-stu-id="904e5-109">Rendering HTML</span></span>

<span data-ttu-id="904e5-110">Die Standard Razor Sprache ist HTML.</span><span class="sxs-lookup"><span data-stu-id="904e5-110">The default Razor language is HTML.</span></span> <span data-ttu-id="904e5-111">Das Rendern von HTML aus Razor Markup unterscheidet sich nicht vom Rendern von HTML aus einer HTML-Datei.</span><span class="sxs-lookup"><span data-stu-id="904e5-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="904e5-112">Das HTML-Markup in *. cshtml* - Razor Dateien wird vom Server unverändert gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="no-locrazor-syntax"></a><span data-ttu-id="904e5-113">Razor-Syntax</span><span class="sxs-lookup"><span data-stu-id="904e5-113">Razor syntax</span></span>

<span data-ttu-id="904e5-114">Razor unterstützt c# und verwendet das `@` Symbol für den Übergang von HTML zu c#.</span><span class="sxs-lookup"><span data-stu-id="904e5-114">Razor supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> <span data-ttu-id="904e5-115">Razor wertet c#-Ausdrücke aus und rendert Sie in der HTML-Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="904e5-115">Razor evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="904e5-116">Wenn auf ein `@` Symbol ein [ Razor reserviertes Schlüsselwort](#razor-reserved-keywords)folgt, wechselt es in Razor -spezifisches Markup.</span><span class="sxs-lookup"><span data-stu-id="904e5-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="904e5-117">Andernfalls erfolgt der Übergang in normales C#.</span><span class="sxs-lookup"><span data-stu-id="904e5-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="904e5-118">`@` Razor Verwenden Sie ein zweites Symbol, um ein Symbol im Markup zu versehen `@` :</span><span class="sxs-lookup"><span data-stu-id="904e5-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="904e5-119">Der Code wird in HTML mit einem einzelnen `@`-Symbol gerendert:</span><span class="sxs-lookup"><span data-stu-id="904e5-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="904e5-120">Bei HTML-Attributen und Inhalt mit E-Mail-Adressen wird das `@`-Symbol nicht als ein Übergangszeichen behandelt.</span><span class="sxs-lookup"><span data-stu-id="904e5-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="904e5-121">Die e-Mail-Adressen im folgenden Beispiel werden bei der folgenden Verarbeitung nicht untersucht Razor :</span><span class="sxs-lookup"><span data-stu-id="904e5-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a><span data-ttu-id="904e5-122">Implizite Razor Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="904e5-122">Implicit Razor expressions</span></span>

<span data-ttu-id="904e5-123">Implizite Razor Ausdrücke beginnen mit, `@` gefolgt von c#-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="904e5-124">Mit Ausnahme des C#-Schlüsselworts `await` dürfen implizite Ausdrücke keine Leerzeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="904e5-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="904e5-125">Wird die C#-Anweisung eindeutig beendet, können auch Leerzeichen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="904e5-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="904e5-126">Implizite Ausdrücke dürfen **keine** C#-Generics enthalten, da die Zeichen innerhalb der Klammern (`<>`) als HTML-Tag interpretiert werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="904e5-127">Der folgende Code ist **ungültig**:</span><span class="sxs-lookup"><span data-stu-id="904e5-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="904e5-128">Der vorhergehende Code erzeugt einen Compilerfehler, der folgendermaßen aussehen kann:</span><span class="sxs-lookup"><span data-stu-id="904e5-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="904e5-129">Das Element „int“ wurde nicht geschlossen.</span><span class="sxs-lookup"><span data-stu-id="904e5-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="904e5-130">Alle Elemente müssen entweder selbstschließend sein oder ein zugehöriges Endtag besitzen.</span><span class="sxs-lookup"><span data-stu-id="904e5-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="904e5-131">Die Methodengruppe „GenericMethod“ kann nicht in den Nichtdelegattyp „Objekt“ konvertiert werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="904e5-132">Wollten Sie die Methode aufrufen?</span><span class="sxs-lookup"><span data-stu-id="904e5-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="904e5-133">Generische Methodenaufrufe müssen in einen [expliziten Razor Ausdruck](#explicit-razor-expressions) oder einen [ Razor Codeblock](#razor-code-blocks)umschließt werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-no-locrazor-expressions"></a><span data-ttu-id="904e5-134">Explizite Razor Ausdrücke</span><span class="sxs-lookup"><span data-stu-id="904e5-134">Explicit Razor expressions</span></span>

<span data-ttu-id="904e5-135">Explizite Razor Ausdrücke bestehen aus einem `@` Symbol mit einer ausgeglichenen Klammer.</span><span class="sxs-lookup"><span data-stu-id="904e5-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="904e5-136">Zum Rendering der letzten Woche wird das folgende Razor Markup verwendet:</span><span class="sxs-lookup"><span data-stu-id="904e5-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="904e5-137">Jeglicher Inhalt innerhalb der `@()`-Klammer wird ausgewertet und in der Ausgabe gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="904e5-138">Die im vorherigen Abschnitt beschriebenen impliziten Ausdrücke dürfen grundsätzlich keine Leerzeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="904e5-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="904e5-139">Im folgenden Code wird eine Woche nicht von der aktuellen Uhrzeit abgezogen:</span><span class="sxs-lookup"><span data-stu-id="904e5-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="904e5-140">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="904e5-141">Explizite Ausdrücke können zum Verketten von Text mit einem Ergebnis des Ausdrucks verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="904e5-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="904e5-142">Ohne den expliziten Ausdruck wird `<p>Age@joe.Age</p>` als E-Mail-Adresse behandelt und `<p>Age@joe.Age</p>` gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="904e5-143">`<p>Age33</p>` wird gerendert, wenn es als expliziter Ausdruck geschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="904e5-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="904e5-144">Explizite Ausdrücke können zum Rendern der Ausgabe von generischen Methoden in *.cshtml*-Dateien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="904e5-145">Das folgende Markup zeigt, wie der weiter oben gezeigte Fehler behoben wird, der durch die Klammern einer generischen C#-Funktion verursacht wurde.</span><span class="sxs-lookup"><span data-stu-id="904e5-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="904e5-146">Der Code wird als expliziter Ausdruck geschrieben:</span><span class="sxs-lookup"><span data-stu-id="904e5-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="904e5-147">Codieren von Ausdrücken</span><span class="sxs-lookup"><span data-stu-id="904e5-147">Expression encoding</span></span>

<span data-ttu-id="904e5-148">C#-Ausdrücke, die als Zeichenfolge ausgewertet werden, werden HTML-codiert.</span><span class="sxs-lookup"><span data-stu-id="904e5-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="904e5-149">C#-Ausdrücke, die als `IHtmlContent` ausgewertet werden, werden direkt durch `IHtmlContent.WriteTo` gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="904e5-150">C#-Ausdrücke, die nicht als `IHtmlContent` ausgewertet werden, werden durch `ToString` in eine Zeichenfolge konvertiert und vor dem Rendern codiert.</span><span class="sxs-lookup"><span data-stu-id="904e5-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="904e5-151">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="904e5-152">Der HTML-Code wird im Browser folgendermaßen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="904e5-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="904e5-153">Die Ausgabe `HtmlHelper.Raw` wird nicht codiert, sondern als HTML-Markup gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="904e5-154">Die Verwendung von `HtmlHelper.Raw` bei einer nicht bereinigten Benutzereingabe stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="904e5-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="904e5-155">Benutzereingaben können schädlichen JavaScript-Code oder andere Sicherheitsrisiken enthalten.</span><span class="sxs-lookup"><span data-stu-id="904e5-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="904e5-156">Das Bereinigen von Benutzereingaben ist schwierig.</span><span class="sxs-lookup"><span data-stu-id="904e5-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="904e5-157">Vermeiden Sie daher die Verwendung von `HtmlHelper.Raw` bei Benutzereingaben.</span><span class="sxs-lookup"><span data-stu-id="904e5-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="904e5-158">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a><span data-ttu-id="904e5-159">Razor Code Blöcke</span><span class="sxs-lookup"><span data-stu-id="904e5-159">Razor code blocks</span></span>

<span data-ttu-id="904e5-160">Razor Code Blöcke beginnen mit `@` und werden von eingeschlossen `{}` .</span><span class="sxs-lookup"><span data-stu-id="904e5-160">Razor code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="904e5-161">Im Gegensatz zu Ausdrücken wird C#-Code in Codeblöcken nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="904e5-162">Codeblöcke und Ausdrücke in einer Ansicht nutzen den gleichen Bereich und werden der Reihe nach definiert:</span><span class="sxs-lookup"><span data-stu-id="904e5-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="904e5-163">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="904e5-164">Deklarieren Sie in Codeblöcken [lokale Funktionen](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) mit Markup als Vorlagenmethoden:</span><span class="sxs-lookup"><span data-stu-id="904e5-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="904e5-165">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="904e5-166">Implizite Übergänge</span><span class="sxs-lookup"><span data-stu-id="904e5-166">Implicit transitions</span></span>

<span data-ttu-id="904e5-167">Die Standardsprache in einem Codeblock ist c#, aber die Razor Seite kann zurück in HTML wechseln:</span><span class="sxs-lookup"><span data-stu-id="904e5-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="904e5-168">Durch Trennzeichen getrennte explizite Übergänge</span><span class="sxs-lookup"><span data-stu-id="904e5-168">Explicit delimited transition</span></span>

<span data-ttu-id="904e5-169">Um einen unter Abschnitt eines Code Blocks zu definieren, der HTML rendern soll, müssen Sie die Zeichen für das Rendering mit dem-Tag umschließen Razor `<text>` :</span><span class="sxs-lookup"><span data-stu-id="904e5-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="904e5-170">Verwenden Sie diese Methode zum Rendern von HTML-Code, der nicht von einem HTML-Tag umschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="904e5-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="904e5-171">Ohne HTML oder Razor Tag Razor tritt ein Laufzeitfehler auf.</span><span class="sxs-lookup"><span data-stu-id="904e5-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="904e5-172">Das `<text>`-Tag ist nützlich, wenn Sie beim Rendern von Inhalt Leerzeichen steuern möchten:</span><span class="sxs-lookup"><span data-stu-id="904e5-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="904e5-173">Nur der Inhalt zwischen den `<text>`-Tags wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="904e5-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="904e5-174">In der HTML-Ausgabe werden keine Leerzeichen vor oder nach dem `<text>`-Tag angezeigt.</span><span class="sxs-lookup"><span data-stu-id="904e5-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="904e5-175">Explizite Zeilenübergänge</span><span class="sxs-lookup"><span data-stu-id="904e5-175">Explicit line transition</span></span>

<span data-ttu-id="904e5-176">Verwenden Sie die `@:`-Syntax, um den Rest einer kompletten Zeile als HTML-Code in einem Codeblock zu rendern:</span><span class="sxs-lookup"><span data-stu-id="904e5-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="904e5-177">Ohne `@:` im Code Razor wird ein Laufzeitfehler generiert.</span><span class="sxs-lookup"><span data-stu-id="904e5-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="904e5-178">Zusätzliche `@` Zeichen in einer Razor Datei können zu Compilerfehlern bei Anweisungen später im-Block führen.</span><span class="sxs-lookup"><span data-stu-id="904e5-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="904e5-179">Diese Compilerfehler können dann schwer nachvollziehbar sein, da der tatsächliche vor dem gemeldeten Fehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="904e5-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="904e5-180">Dieser Fehler tritt häufig auf, wenn mehrere implizite/explizite Ausdrücke in einem einzigen Codeblock kombiniert werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="904e5-181">Steuerungsstrukturen</span><span class="sxs-lookup"><span data-stu-id="904e5-181">Control structures</span></span>

<span data-ttu-id="904e5-182">Steuerungsstrukturen sind eine Erweiterung von Codeblöcken.</span><span class="sxs-lookup"><span data-stu-id="904e5-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="904e5-183">Alle Aspekte von Codeblöcken (Übergang zu Markup, Inline-C#) gelten auch für die folgenden Strukturen:</span><span class="sxs-lookup"><span data-stu-id="904e5-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="904e5-184">Bedingungen `@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="904e5-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="904e5-185">`@if` steuert, wann der Code ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="904e5-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="904e5-186">`else` und `else if` erfordern kein `@`-Symbol:</span><span class="sxs-lookup"><span data-stu-id="904e5-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="904e5-187">Im folgenden Markup wird die Verwendung einer switch-Anweisung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="904e5-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="904e5-188">Schleifen `@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="904e5-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="904e5-189">Auf Vorlagen basierender HTML-Code kann mit Anweisungen zur Steuerung von Schleifen gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="904e5-190">So rendern Sie eine Liste mit Personen.</span><span class="sxs-lookup"><span data-stu-id="904e5-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="904e5-191">Die folgenden Schleifenanweisungen werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="904e5-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="904e5-192">Zusammengesetztes `@using`</span><span class="sxs-lookup"><span data-stu-id="904e5-192">Compound `@using`</span></span>

<span data-ttu-id="904e5-193">In C# kann mit einer `using`-Anweisung sichergestellt werden, dass ein Objekt freigegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="904e5-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="904e5-194">In Razor wird derselbe Mechanismus zum Erstellen von HTML-Hilfsprogrammen verwendet, die zusätzlichen Inhalt enthalten.</span><span class="sxs-lookup"><span data-stu-id="904e5-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="904e5-195">Im folgenden Code wird ein `<form>`-Tag mit der `@using`-Anweisung durch HTML-Hilfsprogramme gerendert:</span><span class="sxs-lookup"><span data-stu-id="904e5-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="904e5-196">Die Behandlung von Ausnahmen ist vergleichbar mit C#:</span><span class="sxs-lookup"><span data-stu-id="904e5-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

<span data-ttu-id="904e5-197">Razor bietet die Möglichkeit, kritische Abschnitte mit Lock-Anweisungen zu schützen:</span><span class="sxs-lookup"><span data-stu-id="904e5-197">Razor has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="904e5-198">Kommentare</span><span class="sxs-lookup"><span data-stu-id="904e5-198">Comments</span></span>

<span data-ttu-id="904e5-199">Razor unterstützt c#-und HTML-Kommentare:</span><span class="sxs-lookup"><span data-stu-id="904e5-199">Razor supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="904e5-200">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

<span data-ttu-id="904e5-201">Razor Kommentare werden vom Server entfernt, bevor die Webseite gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="904e5-201">Razor comments are removed by the server before the webpage is rendered.</span></span> <span data-ttu-id="904e5-202">Razor verwendet `@*  *@` , um Kommentare zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="904e5-202">Razor uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="904e5-203">Der folgende Code ist auskommentiert, damit vom Server kein Markup gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="904e5-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="904e5-204">Anweisungen</span><span class="sxs-lookup"><span data-stu-id="904e5-204">Directives</span></span>

<span data-ttu-id="904e5-205">Razor -Anweisungen werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem- `@` Symbol dargestellt.</span><span class="sxs-lookup"><span data-stu-id="904e5-205">Razor directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="904e5-206">Eine Anweisung ändert in der Regel die Analyse einer Ansicht oder aktiviert unterschiedliche Funktionen.</span><span class="sxs-lookup"><span data-stu-id="904e5-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="904e5-207">RazorWenn Sie verstehen, wie Code für eine Sicht generiert, ist es einfacher, die Funktionsweise von Anweisungen zu verstehen.</span><span class="sxs-lookup"><span data-stu-id="904e5-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="904e5-208">Durch den Code wird eine Klasse ähnlich der folgenden generiert:</span><span class="sxs-lookup"><span data-stu-id="904e5-208">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="904e5-209">Weiter unten in diesem Artikel wird der Abschnitt unter [Suchen der Razor c#-Klasse, die für eine Sicht generiert](#inspect-the-razor-c-class-generated-for-a-view) wurde erläutert, wie diese generierte Klasse angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="904e5-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="904e5-210">Die `@attribute`-Anweisung fügt das angegebene Attribut zu der Klasse der generierten Seite oder Ansicht hinzu.</span><span class="sxs-lookup"><span data-stu-id="904e5-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="904e5-211">Im folgenden Beispiel wird das `[Authorize]`-Attribut hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="904e5-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="904e5-212">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-213">Der- `@code` Block ermöglicht einer [ Razor Komponente](xref:blazor/components/index) das Hinzufügen von c#-Membern (Feldern, Eigenschaften und Methoden) zu einer Komponente:</span><span class="sxs-lookup"><span data-stu-id="904e5-213">The `@code` block enables a [Razor component](xref:blazor/components/index) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="904e5-214">Für Razor -Komponenten `@code` ist ein Alias von [`@functions`](#functions) und wird für empfohlen `@functions` .</span><span class="sxs-lookup"><span data-stu-id="904e5-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="904e5-215">Mehrere `@code`-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="904e5-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="904e5-216">Die `@functions`-Anweisung ermöglicht das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zur generierten Klasse:</span><span class="sxs-lookup"><span data-stu-id="904e5-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="904e5-217">Verwenden Sie in- [ Razor Komponenten](xref:blazor/components/index), `@code` `@functions` um c#-Member hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="904e5-217">In [Razor components](xref:blazor/components/index), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="904e5-218">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="904e5-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="904e5-219">Der Code generiert das folgende HTML-Markup:</span><span class="sxs-lookup"><span data-stu-id="904e5-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="904e5-220">Der folgende Code ist die generierte Razor c#-Klasse:</span><span class="sxs-lookup"><span data-stu-id="904e5-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="904e5-221">`@functions`-Methoden dienen als Vorlagenmethoden, wenn sie Markup aufweisen:</span><span class="sxs-lookup"><span data-stu-id="904e5-221">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="904e5-222">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="904e5-223">Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.</span><span class="sxs-lookup"><span data-stu-id="904e5-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="904e5-224">Im folgenden Beispiel wird <xref:System.IDisposable?displayProperty=fullName> implementiert, sodass die <xref:System.IDisposable.Dispose*>-Methode aufgerufen werden kann:</span><span class="sxs-lookup"><span data-stu-id="904e5-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

<span data-ttu-id="904e5-225">Die `@inherits`-Anweisung bietet uneingeschränkten Zugriff auf die Klasse, die die Ansicht erbt:</span><span class="sxs-lookup"><span data-stu-id="904e5-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="904e5-226">Der folgende Code ist ein benutzerdefinierter Razor Seitentyp:</span><span class="sxs-lookup"><span data-stu-id="904e5-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="904e5-227">`CustomText` wird in einer Ansicht angezeigt:</span><span class="sxs-lookup"><span data-stu-id="904e5-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="904e5-228">Der Code rendert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="904e5-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="904e5-229">`@model` und `@inherits` können in derselben Ansicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="904e5-230">`@inherits` kann in einer *_ViewImports.cshtml*-Datei verwendet werden, die von der Ansicht importiert wird:</span><span class="sxs-lookup"><span data-stu-id="904e5-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="904e5-231">Der folgende Code ist ein Beispiel für eine stark typisierte Ansicht:</span><span class="sxs-lookup"><span data-stu-id="904e5-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="904e5-232">Wird „rick@contoso.com“ im Modell übergeben, generiert die Ansicht das folgende HTML-Markup:</span><span class="sxs-lookup"><span data-stu-id="904e5-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="904e5-233">Die- `@inject` Direktive ermöglicht der Razor Seite, einen Dienst aus dem [Dienst Container](xref:fundamentals/dependency-injection) in eine Ansicht einzufügen.</span><span class="sxs-lookup"><span data-stu-id="904e5-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="904e5-234">Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="904e5-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="904e5-235">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-236">Die- `@layout` Direktive gibt ein Layout für eine Razor Komponente an.</span><span class="sxs-lookup"><span data-stu-id="904e5-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="904e5-237">Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="904e5-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="904e5-238">Weitere Informationen finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="904e5-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="904e5-239">*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="904e5-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="904e5-240">Die `@model`-Anweisung gibt den Typ des Modells an, das an eine Ansicht oder Seite übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="904e5-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="904e5-241">In einer ASP.net Core MVC Razor -oder Pages-APP, die mit einzelnen Benutzerkonten erstellt wurde, enthält *views/Account/Login. cshtml* die folgende Modell Deklaration:</span><span class="sxs-lookup"><span data-stu-id="904e5-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="904e5-242">Die generierte Klasse erbt von `RazorPage<dynamic>`:</span><span class="sxs-lookup"><span data-stu-id="904e5-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

<span data-ttu-id="904e5-243">Razor macht eine `Model` Eigenschaft für den Zugriff auf das an die Ansicht über gegebene Modell verfügbar:</span><span class="sxs-lookup"><span data-stu-id="904e5-243">Razor exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="904e5-244">Die `@model`-Anweisung gibt den Typ der `Model`-Eigenschaft an.</span><span class="sxs-lookup"><span data-stu-id="904e5-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="904e5-245">Die Anweisung legt das `T` in `RazorPage<T>` der generierten Klasse fest, von der die Ansicht abgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="904e5-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="904e5-246">Wird die `@model`-Anweisung nicht angegeben, hat die `Model`-Eigenschaft den Typ `dynamic`.</span><span class="sxs-lookup"><span data-stu-id="904e5-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="904e5-247">Weitere Informationen finden Sie unter [stark typisierte Modelle und das @model Schlüsselwort](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span><span class="sxs-lookup"><span data-stu-id="904e5-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="904e5-248">Die `@namespace`-Anweisung:</span><span class="sxs-lookup"><span data-stu-id="904e5-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="904e5-249">Legt den Namespace der Klasse der generierten Razor Seite, MVC-Ansicht oder Komponente fest Razor .</span><span class="sxs-lookup"><span data-stu-id="904e5-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="904e5-250">Legt die von der nächstgelegenen Import Datei in der Verzeichnisstruktur *_ViewImports. cshtml* (Views oder Pages) oder *_Imports. Razor* ( Razor Components) Stamm abgeleiteten Namespaces der Seiten, Sichten oder Komponenten Klassen fest.</span><span class="sxs-lookup"><span data-stu-id="904e5-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="904e5-251">Für das Razor Beispiel "Pages" in der folgenden Tabelle:</span><span class="sxs-lookup"><span data-stu-id="904e5-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="904e5-252">Jede Seite importiert *Pages/_ViewImports.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="904e5-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="904e5-253">*Pages/_ViewImports.cshtml* enthält `@namespace Hello.World`.</span><span class="sxs-lookup"><span data-stu-id="904e5-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="904e5-254">Jede Seite weist `Hello.World` als Stamm ihres Namespace auf.</span><span class="sxs-lookup"><span data-stu-id="904e5-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="904e5-255">Page</span><span class="sxs-lookup"><span data-stu-id="904e5-255">Page</span></span>                                        | <span data-ttu-id="904e5-256">Namespace</span><span class="sxs-lookup"><span data-stu-id="904e5-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="904e5-257">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="904e5-258">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="904e5-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="904e5-260">Die vorstehenden Beziehungen gelten für Import Dateien, die mit MVC-Ansichten und-Komponenten verwendet werden Razor .</span><span class="sxs-lookup"><span data-stu-id="904e5-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="904e5-261">Wenn mehrere Importdateien über eine `@namespace`-Anweisung verfügen, wird die Datei verwendet, die der Seite, der Ansicht oder der Komponente in der Verzeichnisstruktur am nächsten ist, um den Stammnamespace festzulegen.</span><span class="sxs-lookup"><span data-stu-id="904e5-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="904e5-262">Wenn der Ordner *EvenMorePages* im Beispiel oben eine Importdatei mit `@namespace Another.Planet` enthält (oder die Datei *Pages/MorePages/EvenMorePages/Page.cshtml*`@namespace Another.Planet` enthält), finden Sie das Ergebnis in der folgenden Tabelle.</span><span class="sxs-lookup"><span data-stu-id="904e5-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="904e5-263">Page</span><span class="sxs-lookup"><span data-stu-id="904e5-263">Page</span></span>                                        | <span data-ttu-id="904e5-264">Namespace</span><span class="sxs-lookup"><span data-stu-id="904e5-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="904e5-265">*Pages/index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="904e5-266">*Pages/MorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="904e5-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span><span class="sxs-lookup"><span data-stu-id="904e5-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="904e5-268">Die `@page`-Anweisung hat abhängig vom Typ der Datei, in der Sie verwendet wird, unterschiedliche Auswirkungen.</span><span class="sxs-lookup"><span data-stu-id="904e5-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="904e5-269">Für die Anweisung gilt:</span><span class="sxs-lookup"><span data-stu-id="904e5-269">The directive:</span></span>

* <span data-ttu-id="904e5-270">In einer *cshtml* -Datei gibt an, dass die Datei eine Razor Seite ist.</span><span class="sxs-lookup"><span data-stu-id="904e5-270">In a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="904e5-271">Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes) und <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="904e5-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="904e5-272">Gibt an, dass eine Razor Komponente Anforderungen direkt verarbeiten soll.</span><span class="sxs-lookup"><span data-stu-id="904e5-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="904e5-273">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="904e5-273">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="904e5-274">Die- `@page` Direktive in der ersten Zeile einer *cshtml* -Datei gibt an, dass die Datei eine Razor Seite ist.</span><span class="sxs-lookup"><span data-stu-id="904e5-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="904e5-275">Weitere Informationen finden Sie unter <xref:razor-pages/index>.</span><span class="sxs-lookup"><span data-stu-id="904e5-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="904e5-276">*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="904e5-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="904e5-277">Die `@section` -Direktive wird zusammen mit [MVC-und Razor Seitenlayouts](xref:mvc/views/layout) verwendet, um Ansichten oder Seiten zum Rendering von Inhalten in verschiedenen Teilen der HTML-Seite zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="904e5-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="904e5-278">Weitere Informationen finden Sie unter <xref:mvc/views/layout>.</span><span class="sxs-lookup"><span data-stu-id="904e5-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="904e5-279">Die `@using`-Anweisung fügt die C#-Anweisung `using`der generierten Ansicht hinzu:</span><span class="sxs-lookup"><span data-stu-id="904e5-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="904e5-280">In- [ Razor Komponenten](xref:blazor/components/index) `@using` steuert auch, welche Komponenten sich im Gültigkeitsbereich befinden.</span><span class="sxs-lookup"><span data-stu-id="904e5-280">In [Razor components](xref:blazor/components/index), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="904e5-281">Direktivenattribute</span><span class="sxs-lookup"><span data-stu-id="904e5-281">Directive attributes</span></span>

<span data-ttu-id="904e5-282">Razor Direktivenattribute werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem- `@` Symbol dargestellt.</span><span class="sxs-lookup"><span data-stu-id="904e5-282">Razor directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="904e5-283">Ein direktivenattribut ändert in der Regel die Art und Weise, in der ein Element analysiert wird, oder unterstützt</span><span class="sxs-lookup"><span data-stu-id="904e5-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="904e5-284">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-285">`@attributes` ermöglicht es einer Komponente, nicht deklarierte Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="904e5-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="904e5-286">Weitere Informationen finden Sie unter <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span><span class="sxs-lookup"><span data-stu-id="904e5-286">For more information, see <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="904e5-287">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-288">Die Datenbindung in-Komponenten wird mit dem `@bind`-Attribut erreicht.</span><span class="sxs-lookup"><span data-stu-id="904e5-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="904e5-289">Weitere Informationen finden Sie unter <xref:blazor/components/data-binding>.</span><span class="sxs-lookup"><span data-stu-id="904e5-289">For more information, see <xref:blazor/components/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="904e5-290">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-290">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-291">Razor stellt Funktionen für die Ereignis Behandlung für-Komponenten bereit.</span><span class="sxs-lookup"><span data-stu-id="904e5-291">Razor provides event handling features for components.</span></span> <span data-ttu-id="904e5-292">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling>.</span><span class="sxs-lookup"><span data-stu-id="904e5-292">For more information, see <xref:blazor/components/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="904e5-293">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-294">Verhindert die Standardaktion für das Ereignis.</span><span class="sxs-lookup"><span data-stu-id="904e5-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="904e5-295">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-296">Beendet die Ereignisweitergabe für das Ereignis.</span><span class="sxs-lookup"><span data-stu-id="904e5-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="904e5-297">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-298">Das Direktivenattribut `@key` bewirkt, dass der Komponentenvergleichsalgorithmus die Erhaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert.</span><span class="sxs-lookup"><span data-stu-id="904e5-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="904e5-299">Weitere Informationen finden Sie unter <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span><span class="sxs-lookup"><span data-stu-id="904e5-299">For more information, see <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="904e5-300">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-301">Komponentenverweise (`@ref`) bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="904e5-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="904e5-302">Weitere Informationen finden Sie unter <xref:blazor/components/index#capture-references-to-components>.</span><span class="sxs-lookup"><span data-stu-id="904e5-302">For more information, see <xref:blazor/components/index#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="904e5-303">*Dieses Szenario gilt nur für Razor Komponenten (Razor).*</span><span class="sxs-lookup"><span data-stu-id="904e5-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="904e5-304">Die `@typeparam`-Anweisung deklariert einen generischen Typparameter für die generierte Komponentenklasse.</span><span class="sxs-lookup"><span data-stu-id="904e5-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="904e5-305">Weitere Informationen finden Sie unter <xref:blazor/components/templated-components#generic-typed-components>.</span><span class="sxs-lookup"><span data-stu-id="904e5-305">For more information, see <xref:blazor/components/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a><span data-ttu-id="904e5-306">Auf Vorlagen basierende Delegaten Razor</span><span class="sxs-lookup"><span data-stu-id="904e5-306">Templated Razor delegates</span></span>

<span data-ttu-id="904e5-307">Razor mithilfe von Vorlagen können Sie einen UI-Code Ausschnitt im folgenden Format definieren:</span><span class="sxs-lookup"><span data-stu-id="904e5-307">Razor templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="904e5-308">Im folgenden Beispiel wird veranschaulicht, wie Sie einen Vorlagen basierten Delegaten Razor als angeben <xref:System.Func%602> .</span><span class="sxs-lookup"><span data-stu-id="904e5-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="904e5-309">Der Typ [dynamic](/dotnet/csharp/programming-guide/types/using-type-dynamic) wird für den Parameter der Methode angegeben, die der Delegat einkapselt.</span><span class="sxs-lookup"><span data-stu-id="904e5-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="904e5-310">Ein [object](/dotnet/csharp/language-reference/keywords/object)-Typ wird als Rückgabewert des Delegats angegeben.</span><span class="sxs-lookup"><span data-stu-id="904e5-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="904e5-311">Die Vorlage wird mit <xref:System.Collections.Generic.List%601> von `Pet` mit einer `Name`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="904e5-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="904e5-312">Die Vorlage wird mit `pets` in einer `foreach`-Anweisung gerendert:</span><span class="sxs-lookup"><span data-stu-id="904e5-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="904e5-313">Gerenderte Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="904e5-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="904e5-314">Sie können auch eine Inline Razor Vorlage als Argument für eine Methode bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="904e5-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="904e5-315">Im folgenden Beispiel empfängt die- `Repeat` Methode eine Razor Vorlage.</span><span class="sxs-lookup"><span data-stu-id="904e5-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="904e5-316">Die Methode verwendet die Vorlage dann zum Erstellen von HTML-Inhalten mit Wiederholungen von Elementen aus einer Liste:</span><span class="sxs-lookup"><span data-stu-id="904e5-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="904e5-317">Wird die Liste mit Haustieren aus dem vorherigen Beispiel verwendet, wird die `Repeat`-Methode wie folgt aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="904e5-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="904e5-318"><xref:System.Collections.Generic.List%601> von `Pet`</span><span class="sxs-lookup"><span data-stu-id="904e5-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="904e5-319">Anzahl der Wiederholungen für jedes Haustier</span><span class="sxs-lookup"><span data-stu-id="904e5-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="904e5-320">Inlinevorlage zur Verwendung für die Elemente einer unsortierten Liste</span><span class="sxs-lookup"><span data-stu-id="904e5-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="904e5-321">Gerenderte Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="904e5-321">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="904e5-322">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="904e5-322">Tag Helpers</span></span>

<span data-ttu-id="904e5-323">*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*</span><span class="sxs-lookup"><span data-stu-id="904e5-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="904e5-324">Die folgenden drei Anweisungen gehören zu den [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro).</span><span class="sxs-lookup"><span data-stu-id="904e5-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="904e5-325">Anweisung</span><span class="sxs-lookup"><span data-stu-id="904e5-325">Directive</span></span> | <span data-ttu-id="904e5-326">Funktion</span><span class="sxs-lookup"><span data-stu-id="904e5-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="904e5-327">Macht Taghilfsprogramme für eine Ansicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="904e5-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="904e5-328">Entfernt zuvor hinzugefügte Taghilfsprogramme aus einer Ansicht.</span><span class="sxs-lookup"><span data-stu-id="904e5-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="904e5-329">Gibt ein Tagpräfix an, um Unterstützung für Taghilfsprogramme zu aktivieren und ihre Verwendung explizit zu machen.</span><span class="sxs-lookup"><span data-stu-id="904e5-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="no-locrazor-reserved-keywords"></a><span data-ttu-id="904e5-330">Razor reservierte Schlüsselwörter</span><span class="sxs-lookup"><span data-stu-id="904e5-330">Razor reserved keywords</span></span>

### <a name="no-locrazor-keywords"></a><span data-ttu-id="904e5-331">Razor Keywords</span><span class="sxs-lookup"><span data-stu-id="904e5-331">Razor keywords</span></span>

* <span data-ttu-id="904e5-332">`page` (Erfordert ASP.net Core 2,1 oder höher)</span><span class="sxs-lookup"><span data-stu-id="904e5-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="904e5-333">`helper` (Wird zurzeit nicht von ASP.net Core unterstützt)</span><span class="sxs-lookup"><span data-stu-id="904e5-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

<span data-ttu-id="904e5-334">Razor Schlüsselwörter werden mit Escapezeichen versehen `@(Razor Keyword)` (z `@(functions)` . b.).</span><span class="sxs-lookup"><span data-stu-id="904e5-334">Razor keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-no-locrazor-keywords"></a><span data-ttu-id="904e5-335">C#- Razor Schlüsselwörter</span><span class="sxs-lookup"><span data-stu-id="904e5-335">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="904e5-336">C# Razor -Schlüsselwörter müssen mit einem doppelten Escapezeichen versehen werden `@(@C# Razor Keyword)` (z `@(@case)` . b.).</span><span class="sxs-lookup"><span data-stu-id="904e5-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="904e5-337">Der erste schützt `@` den Razor Parser.</span><span class="sxs-lookup"><span data-stu-id="904e5-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="904e5-338">Das zweite `@` dient als Escapezeichen für den C#-Parser.</span><span class="sxs-lookup"><span data-stu-id="904e5-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-no-locrazor"></a><span data-ttu-id="904e5-339">Reservierte Schlüsselwörter werden von nicht verwendet Razor</span><span class="sxs-lookup"><span data-stu-id="904e5-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a><span data-ttu-id="904e5-340">Überprüfen der Razor für eine Sicht generierten c#-Klasse</span><span class="sxs-lookup"><span data-stu-id="904e5-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="904e5-341">Bei .net Core SDK 2,1 oder höher übernimmt das [ Razor SDK](xref:razor-pages/sdk) die Kompilierung von Razor Dateien.</span><span class="sxs-lookup"><span data-stu-id="904e5-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="904e5-342">Beim Entwickeln eines Projekts generiert das Razor SDK eine \*obj/<build_configuration>/<target_framework_moniker>/ Razor \* Verzeichnis im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="904e5-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="904e5-343">Die Verzeichnisstruktur im *Razor* Verzeichnis spiegelt die Verzeichnisstruktur des Projekts wider.</span><span class="sxs-lookup"><span data-stu-id="904e5-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="904e5-344">Sehen Sie sich die folgende Verzeichnisstruktur in einem ASP.net Core 2,1 pages-Projekt an, das auf Razor .net Core 2,1 abzielt:</span><span class="sxs-lookup"><span data-stu-id="904e5-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="904e5-345">Wenn Sie das Projekt mit der Konfiguration zum *Debuggen* erstellen, wird folgendes *obj*-Verzeichnis erstellt:</span><span class="sxs-lookup"><span data-stu-id="904e5-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="904e5-346">Öffnen Sie *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*, um die generierte Klasse für *pages/index. cshtml*anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="904e5-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="904e5-347">Fügen Sie dem ASP.NET Core MVC-Projekt die folgende Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="904e5-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="904e5-348">Überschreiben Sie in `Startup.ConfigureServices` die von MVC hinzugefügte `RazorTemplateEngine`-Klasse mit der `CustomTemplateEngine`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="904e5-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="904e5-349">Legen Sie auf der `return csharpDocument;`-Anweisung von `CustomTemplateEngine` einen Haltepunkt fest.</span><span class="sxs-lookup"><span data-stu-id="904e5-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="904e5-350">Wenn die Ausführung des Programms am Haltepunkt angehalten wird, können Sie den Wert von `generatedCode` anzeigen.</span><span class="sxs-lookup"><span data-stu-id="904e5-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![Ansicht „Text-Schnellansicht“ von „generatedCode“](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="904e5-352">Ansicht der Suchvorgänge und Groß-/Kleinschreibung</span><span class="sxs-lookup"><span data-stu-id="904e5-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="904e5-353">Die Razor Ansichts-Engine führt Suchvorgänge bei der Suche nach Groß-und Kleinschreibung durch.</span><span class="sxs-lookup"><span data-stu-id="904e5-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="904e5-354">Der tatsächliche Suchvorgang wird jedoch vom zugrunde liegenden Dateisystem bestimmt:</span><span class="sxs-lookup"><span data-stu-id="904e5-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="904e5-355">Dateibasierte Quelle:</span><span class="sxs-lookup"><span data-stu-id="904e5-355">File based source:</span></span>
  * <span data-ttu-id="904e5-356">Bei Betriebssystemen, die Dateisysteme ohne Berücksichtigung von Groß-/Kleinschreibung verwenden (z.B. Windows), wird bei Suchvorgängen nach physischen Dateianbietern die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="904e5-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="904e5-357">`return View("Test")` liefert beispielsweise Treffer für */Views/Home/Test.cshtml*, */Views/home/test.cshtml* sowie für jede andere Schreibweise.</span><span class="sxs-lookup"><span data-stu-id="904e5-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="904e5-358">Bei Dateisystemen, die die Groß-/Kleinschreibung berücksichtigen (z.B. Linux, OSX sowie mit `EmbeddedFileProvider`), wird die Groß-/Kleinschreibung auch bei Suchvorgängen berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="904e5-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="904e5-359">`return View("Test")` liefert beispielsweise ganz konkret Treffer für */Views/Home/Test.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="904e5-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="904e5-360">Vorkompilierte Ansichten: Ab ASP.NET Core 2.0 wird bei der Suche nach vorkompilierten Ansichten unter allen Betriebssystemen die Groß-/Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="904e5-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="904e5-361">Das Verhalten ist mit dem des physischen Dateianbieters unter Windows identisch.</span><span class="sxs-lookup"><span data-stu-id="904e5-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="904e5-362">Unterscheiden sich zwei vorkompilierte Ansichten nur in der Groß-/Kleinschreibung, ist das Ergebnis der Suche nicht deterministisch.</span><span class="sxs-lookup"><span data-stu-id="904e5-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="904e5-363">Entwicklern wird empfohlen, sich bei der Groß-/Kleinschreibung von Datei- und Verzeichnisnamen an der Schreibweise folgender Begriffe zu orientieren:</span><span class="sxs-lookup"><span data-stu-id="904e5-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="904e5-364">Bereichs-, Controller- und Aktionsnamen</span><span class="sxs-lookup"><span data-stu-id="904e5-364">Area, controller, and action names.</span></span>
* <span data-ttu-id="904e5-365">Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="904e5-365">Razor Pages.</span></span>

<span data-ttu-id="904e5-366">Durch Überprüfung der Groß-/Kleinschreibung wird sichergestellt, dass die entsprechenden Ansichten für die Bereitstellungen unabhängig von dem zugrunde liegenden Dateisystem gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="904e5-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="904e5-367">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="904e5-367">Additional resources</span></span>

<span data-ttu-id="904e5-368">[Einführung in ASP.net-Webprogrammierung mithilfe Razor von Die Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) bietet viele Beispiele für die Programmierung mit Razor Syntax.</span><span class="sxs-lookup"><span data-stu-id="904e5-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
