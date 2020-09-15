---
title: Verhindern von Cross-Site Scripting (XSS) in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über Cross-Site Scripting (XSS) und Techniken zum Beheben dieses Sicherheitsrisikos in einer ASP.net Core-app.
ms.author: riande
ms.date: 10/02/2018
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
uid: security/cross-site-scripting
ms.openlocfilehash: 38e9e102e9ac18ec14bceebf391c11a434492ac9
ms.sourcegitcommit: 6ecdc481d5b9a10d2c6e091217f017b36bdba957
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90456061"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="50c2e-103">Verhindern von Cross-Site Scripting (XSS) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="50c2e-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="50c2e-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="50c2e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="50c2e-105">Cross-Site Scripting (XSS) ist ein Sicherheitsrisiko, das es Angreifern ermöglicht, Client seitige Skripts (normalerweise JavaScript) in Webseiten zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="50c2e-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="50c2e-106">Wenn andere Benutzer die betroffenen Seiten laden, werden die Skripts des Angreifers ausgeführt, sodass der Angreifer cookie s-und Sitzungs Token stehlen, den Inhalt der Webseite durch DOM-Manipulation ändern oder den Browser an eine andere Seite umleiten kann.</span><span class="sxs-lookup"><span data-stu-id="50c2e-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="50c2e-107">XSS-Sicherheitsrisiken treten in der Regel auf, wenn eine Anwendung Benutzereingaben annimmt und Sie an eine Seite ausgibt, ohne Sie zu validieren, zu codieren oder zu</span><span class="sxs-lookup"><span data-stu-id="50c2e-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="50c2e-108">Schutz Ihrer Anwendung vor XSS</span><span class="sxs-lookup"><span data-stu-id="50c2e-108">Protecting your application against XSS</span></span>

<span data-ttu-id="50c2e-109">XSS funktioniert auf der Basisebene, indem Sie die Anwendung in das Einfügen eines `<script>` Tags in die gerenderte Seite einfügen oder ein `On*` Ereignis in ein Element einfügen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="50c2e-110">Entwickler sollten die folgenden Präventionsmaßnahmen ergreifen, um die Einführung von XSS in Ihre Anwendung zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="50c2e-111">Fügen Sie niemals nicht vertrauenswürdige Daten in Ihre HTML-Eingabe ein, es sei denn, Sie führen die restlichen Schritte aus.</span><span class="sxs-lookup"><span data-stu-id="50c2e-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="50c2e-112">Bei nicht vertrauenswürdigen Daten handelt es sich um Daten, die von einem Angreifer gesteuert werden können, HTML-Formular Eingaben, Abfrage Zeichenfolgen, HTTP-Header, und auch Daten, die aus einer Datenbank als Angreifer stammen, können möglicherweise die Datenbank verletzen, auch wenn Sie Ihre Anwendung nicht verletzen können.</span><span class="sxs-lookup"><span data-stu-id="50c2e-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="50c2e-113">Vor dem Einfügen nicht vertrauenswürdiger Daten in ein HTML-Element stellen Sie sicher, dass die HTML-Codierung vorliegt</span><span class="sxs-lookup"><span data-stu-id="50c2e-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="50c2e-114">Bei der HTML-Codierung werden Zeichen wie z &lt; . b. und in eine sichere Form wie &amp; lt geändert.</span><span class="sxs-lookup"><span data-stu-id="50c2e-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="50c2e-115">Bevor nicht vertrauenswürdige Daten in ein HTML-Attribut gesetzt werden, stellen Sie sicher, dass die HTML-Codierung</span><span class="sxs-lookup"><span data-stu-id="50c2e-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="50c2e-116">Die HTML-Attribut Codierung ist eine supermenge der HTML-Codierung und codiert zusätzliche Zeichen wie z. b. "and".</span><span class="sxs-lookup"><span data-stu-id="50c2e-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="50c2e-117">Vor dem Einfügen nicht vertrauenswürdiger Daten in JavaScript platzieren Sie die Daten in einem HTML-Element, dessen Inhalt Sie zur Laufzeit abrufen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="50c2e-118">Wenn dies nicht möglich ist, stellen Sie sicher, dass die Daten JavaScript-codiert sind.</span><span class="sxs-lookup"><span data-stu-id="50c2e-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="50c2e-119">Die JavaScript-Codierung erfordert gefährliche Zeichen für JavaScript und ersetzt Sie durch ihre hexadezimal Zeichen, die beispielsweise &lt; als codiert werden `\u003C` .</span><span class="sxs-lookup"><span data-stu-id="50c2e-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="50c2e-120">Vor dem Einfügen von nicht vertrauenswürdigen Daten in eine URL-Abfrage Zeichenfolge stellen Sie sicher, dass die URL</span><span class="sxs-lookup"><span data-stu-id="50c2e-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="50c2e-121">HTML-Codierung mithilfe von Razor</span><span class="sxs-lookup"><span data-stu-id="50c2e-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="50c2e-122">Die Razor Engine, die in MVC verwendet wird, codiert automatisch alle Ausgaben aus Variablen, es sei denn, Sie arbeiten wirklich hart, um dies zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="50c2e-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="50c2e-123">Dabei werden bei der Verwendung der-Direktive HTML-Attribut Codierungsregeln verwendet *@* .</span><span class="sxs-lookup"><span data-stu-id="50c2e-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="50c2e-124">Da die HTML-Attribut Codierung eine supermenge der HTML-Codierung ist, bedeutet dies, dass Sie sich nicht darum kümmern müssen, ob Sie HTML-Codierung oder HTML-Attribut Codierung verwenden sollten.</span><span class="sxs-lookup"><span data-stu-id="50c2e-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="50c2e-125">Sie müssen sicherstellen, dass Sie @ nur in einem HTML-Kontext verwenden, nicht, wenn Sie versuchen, nicht vertrauenswürdige Eingaben direkt in JavaScript einzufügen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="50c2e-126">Taghilfsprogramme codieren auch Eingaben, die Sie in tagparametern verwenden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="50c2e-127">Sehen Sie sich die folgende Razor Ansicht an:</span><span class="sxs-lookup"><span data-stu-id="50c2e-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="50c2e-128">In dieser Ansicht wird der Inhalt der Variablen *untreudinput ausgegeben* .</span><span class="sxs-lookup"><span data-stu-id="50c2e-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="50c2e-129">Diese Variable enthält einige Zeichen, die in XSS-Angriffen verwendet werden, nämlich &lt; "und &gt; .</span><span class="sxs-lookup"><span data-stu-id="50c2e-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="50c2e-130">Beim Untersuchen der Quelle wird die gerenderte Ausgabe angezeigt, die als</span><span class="sxs-lookup"><span data-stu-id="50c2e-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="50c2e-131">ASP.net Core MVC stellt eine- `HtmlString` Klasse bereit, die bei der Ausgabe nicht automatisch codiert wird.</span><span class="sxs-lookup"><span data-stu-id="50c2e-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="50c2e-132">Dies sollte nie in Kombination mit nicht vertrauenswürdigen Eingaben verwendet werden, da dadurch ein XSS-Sicherheitsrisiko verfügbar gemacht wird.</span><span class="sxs-lookup"><span data-stu-id="50c2e-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="50c2e-133">JavaScript-Codierung mithilfe von Razor</span><span class="sxs-lookup"><span data-stu-id="50c2e-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="50c2e-134">Es kann vorkommen, dass Sie einen Wert in JavaScript einfügen möchten, um Ihre Ansicht zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="50c2e-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="50c2e-135">Es gibt hierbei zwei Möglichkeiten.</span><span class="sxs-lookup"><span data-stu-id="50c2e-135">There are two ways to do this.</span></span> <span data-ttu-id="50c2e-136">Die sicherste Möglichkeit zum Einfügen von Werten besteht darin, den Wert in einem Daten Attribut eines Tags zu platzieren und ihn in JavaScript abzurufen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="50c2e-137">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="50c2e-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="50c2e-138">Das vorangehende Markup generiert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="50c2e-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="50c2e-139">Mit dem vorangehenden Code wird die folgende Ausgabe generiert:</span><span class="sxs-lookup"><span data-stu-id="50c2e-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="50c2e-140">Verketten Sie ***keine*** nicht vertrauenswürdigen Eingaben in JavaScript, um DOM-Elemente zu erstellen, oder verwenden Sie `document.write()` für dynamisch generierten Inhalt.</span><span class="sxs-lookup"><span data-stu-id="50c2e-140">Do ***NOT*** concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="50c2e-141">Verwenden Sie einen der folgenden Ansätze, um zu verhindern, dass Code für DOM-basiertes XSS verfügbar gemacht wird:</span><span class="sxs-lookup"><span data-stu-id="50c2e-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS:</span></span>
> * <span data-ttu-id="50c2e-142">`createElement()` und weisen Eigenschaftswerte den entsprechenden Methoden oder Eigenschaften zu, wie z `node.textContent=` . b. oder. InnerText = '.</span><span class="sxs-lookup"><span data-stu-id="50c2e-142">`createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="50c2e-143">`document.CreateTextNode()` und fügen Sie Sie an den entsprechenden DOM-Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="50c2e-143">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="50c2e-144">Aufrufen von Encodern im Code</span><span class="sxs-lookup"><span data-stu-id="50c2e-144">Accessing encoders in code</span></span>

<span data-ttu-id="50c2e-145">Die HTML-, JavaScript-und URL-Encoder sind für den Code auf zwei Arten verfügbar. Sie können Sie über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) einfügen, oder Sie können die im-Namespace enthaltenen Standard Encoder verwenden `System.Text.Encodings.Web` .</span><span class="sxs-lookup"><span data-stu-id="50c2e-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="50c2e-146">Wenn Sie die Standard Encoder verwenden, werden alle, die Sie auf Zeichen Bereiche angewendet haben, als sicher behandelt werden, nicht wirksam. die Standard Codierungen verwenden die sichersten Codierungsregeln.</span><span class="sxs-lookup"><span data-stu-id="50c2e-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="50c2e-147">Um die konfigurierbaren Encoder über di verwenden zu können, sollten die Konstruktoren nach Bedarf einen *htmlencoder*-, *javascriptencoder* -und *urlencoder* -Parameter verwenden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="50c2e-148">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="50c2e-148">For example;</span></span>

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a><span data-ttu-id="50c2e-149">URL-Codierungs Parameter</span><span class="sxs-lookup"><span data-stu-id="50c2e-149">Encoding URL Parameters</span></span>

<span data-ttu-id="50c2e-150">Wenn Sie eine URL-Abfrage Zeichenfolge mit nicht vertrauenswürdiger Eingabe als Wert erstellen möchten `UrlEncoder` , verwenden Sie, um den Wert zu codieren.</span><span class="sxs-lookup"><span data-stu-id="50c2e-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="50c2e-151">Ein auf ein Objekt angewendeter</span><span class="sxs-lookup"><span data-stu-id="50c2e-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="50c2e-152">Nach der Codierung enthält die encodvalue-Variable `%22Quoted%20Value%20with%20spaces%20and%20%26%22` .</span><span class="sxs-lookup"><span data-stu-id="50c2e-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="50c2e-153">Leerzeichen, Anführungszeichen, Interpunktions Zeichen und andere unsichere Zeichen werden in den hexadezimalen Wert Prozent codiert, z. b. wird ein Leerzeichen zu %20.</span><span class="sxs-lookup"><span data-stu-id="50c2e-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="50c2e-154">Verwenden Sie nicht vertrauenswürdige Eingaben nicht als Teil eines URL-Pfads.</span><span class="sxs-lookup"><span data-stu-id="50c2e-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="50c2e-155">Übergeben Sie immer nicht vertrauenswürdige Eingaben als Abfrage Zeichenfolgen-Wert.</span><span class="sxs-lookup"><span data-stu-id="50c2e-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="50c2e-156">Anpassen der Encoder</span><span class="sxs-lookup"><span data-stu-id="50c2e-156">Customizing the Encoders</span></span>

<span data-ttu-id="50c2e-157">Standardmäßig verwenden Encoder eine sichere Liste, die auf den grundlegenden lateinischen Unicode-Bereich beschränkt ist, und Codieren alle Zeichen außerhalb dieses Bereichs als Zeichencode Entsprechungen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="50c2e-158">Dieses Verhalten wirkt Razor sich auch auf das Rendering taghelper und htmlhelper aus, da es die Encoder zum Ausgeben der Zeichen folgen verwendet.</span><span class="sxs-lookup"><span data-stu-id="50c2e-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="50c2e-159">Die dahinter liegende Argumentation besteht darin, vor unbekannten oder zukünftigen Browserfehlern zu schützen (vorherige Browser Fehler haben die Analysierung basierend auf der Verarbeitung von nicht englischen Zeichen abgeglichen).</span><span class="sxs-lookup"><span data-stu-id="50c2e-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="50c2e-160">Wenn Ihre Website nicht lateinische Zeichen wie Chinesisch, Kyrillisch oder andere verwendet, ist dies wahrscheinlich nicht das gewünschte Verhalten.</span><span class="sxs-lookup"><span data-stu-id="50c2e-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="50c2e-161">Sie können die Liste der Encoder-sichere Listen so anpassen, dass Sie für Ihre Anwendung geeignete Unicode-Bereiche in enthalten `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="50c2e-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="50c2e-162">Wenn Sie beispielsweise die Standardkonfiguration verwenden, können Sie eine Razor htmlhelper-Methode wie folgt verwenden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="50c2e-163">Wenn Sie die Quelle der Webseite anzeigen, sehen Sie, dass Sie wie folgt gerendert wurde, wobei der chinesische Text codiert ist.</span><span class="sxs-lookup"><span data-stu-id="50c2e-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="50c2e-164">Um die vom Encoder als sicher behandelten Zeichen zu erweitern, fügen Sie die folgende Zeile in die- `ConfigureServices()` Methode ein `startup.cs` .</span><span class="sxs-lookup"><span data-stu-id="50c2e-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="50c2e-165">In diesem Beispiel wird die sichere Liste so erweitert, dass Sie den Unicode-Bereich cjkunifedideographs einschließt.</span><span class="sxs-lookup"><span data-stu-id="50c2e-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="50c2e-166">Die gerenderte Ausgabe wird jetzt</span><span class="sxs-lookup"><span data-stu-id="50c2e-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="50c2e-167">Sichere Listen Bereiche werden als Unicode-Code Diagramme, nicht als Sprachen angegeben.</span><span class="sxs-lookup"><span data-stu-id="50c2e-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="50c2e-168">Der [Unicode-Standard](https://unicode.org/) enthält eine Liste von [Code Diagrammen](https://www.unicode.org/charts/index.html) , die Sie verwenden können, um das Diagramm mit ihren Zeichen zu suchen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-168">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="50c2e-169">Jeder Encoder, HTML, JavaScript und jede URL muss separat konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="50c2e-170">Die Anpassung der sicheren Liste wirkt sich nur auf Encoder aus, die über di übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="50c2e-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="50c2e-171">Wenn Sie über einen direkten Zugriff auf einen Encoder verfügen `System.Text.Encodings.Web.*Encoder.Default` , wird die standardmäßige lateinische Standard SafeList verwendet.</span><span class="sxs-lookup"><span data-stu-id="50c2e-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="50c2e-172">Wo sollte die Codierung erfolgen?</span><span class="sxs-lookup"><span data-stu-id="50c2e-172">Where should encoding take place?</span></span>

<span data-ttu-id="50c2e-173">Die allgemein akzeptierte Übung besteht darin, dass die Codierung an der Stelle der Ausgabe stattfindet und codierte Werte niemals in einer Datenbank gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="50c2e-174">Die Codierung an der Stelle der Ausgabe ermöglicht es Ihnen, die Verwendung von Daten zu ändern, z. b. von HTML in einen Abfrage Zeichen folgen Wert.</span><span class="sxs-lookup"><span data-stu-id="50c2e-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="50c2e-175">Außerdem können Sie Ihre Daten problemlos durchsuchen, ohne Werte vor der Suche codieren zu müssen, und Sie können alle Änderungen oder Fehlerbehebungen von Encodern nutzen.</span><span class="sxs-lookup"><span data-stu-id="50c2e-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="50c2e-176">Validierung als XSS-Präventionsmethode</span><span class="sxs-lookup"><span data-stu-id="50c2e-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="50c2e-177">Die Validierung kann ein nützliches Tool zum Einschränken von XSS-Angriffen sein.</span><span class="sxs-lookup"><span data-stu-id="50c2e-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="50c2e-178">Beispielsweise löst eine numerische Zeichenfolge, die nur die Zeichen 0-9 enthält, keinen XSS-Angriff aus.</span><span class="sxs-lookup"><span data-stu-id="50c2e-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="50c2e-179">Die Validierung wird komplizierter, wenn HTML in Benutzereingaben akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="50c2e-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="50c2e-180">Das Auswerten von HTML-Eingaben ist schwierig, wenn nicht unmöglich.</span><span class="sxs-lookup"><span data-stu-id="50c2e-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="50c2e-181">Markdown ist mit einem Parser gekoppelt, der eingebettete HTML-Code entfernt, ist eine sicherere Option zum akzeptieren umfassender Eingaben.</span><span class="sxs-lookup"><span data-stu-id="50c2e-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="50c2e-182">Verlassen Sie sich niemals ausschließlich auf die Validierung.</span><span class="sxs-lookup"><span data-stu-id="50c2e-182">Never rely on validation alone.</span></span> <span data-ttu-id="50c2e-183">Codieren Sie vor der Ausgabe immer nicht vertrauenswürdige Eingaben, unabhängig davon, welche Validierung oder Bereinigung durchgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="50c2e-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
