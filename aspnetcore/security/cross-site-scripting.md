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
ms.openlocfilehash: 03bdfe9260ef6433456ba53d0cab8c7bf9f86377
ms.sourcegitcommit: 422e02bad384775bfe19a90910737340ad106c5b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90083465"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Verhindern von Cross-Site Scripting (XSS) in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Cross-Site Scripting (XSS) ist ein Sicherheitsrisiko, das es Angreifern ermöglicht, Client seitige Skripts (normalerweise JavaScript) in Webseiten zu platzieren. Wenn andere Benutzer die betroffenen Seiten laden, werden die Skripts des Angreifers ausgeführt, sodass der Angreifer cookie s-und Sitzungs Token stehlen, den Inhalt der Webseite durch DOM-Manipulation ändern oder den Browser an eine andere Seite umleiten kann. XSS-Sicherheitsrisiken treten in der Regel auf, wenn eine Anwendung Benutzereingaben annimmt und Sie an eine Seite ausgibt, ohne Sie zu validieren, zu codieren oder zu

## <a name="protecting-your-application-against-xss"></a>Schutz Ihrer Anwendung vor XSS

XSS funktioniert auf der Basisebene, indem Sie die Anwendung in das Einfügen eines `<script>` Tags in die gerenderte Seite einfügen oder ein `On*` Ereignis in ein Element einfügen. Entwickler sollten die folgenden Präventionsmaßnahmen ergreifen, um die Einführung von XSS in Ihre Anwendung zu vermeiden.

1. Fügen Sie niemals nicht vertrauenswürdige Daten in Ihre HTML-Eingabe ein, es sei denn, Sie führen die restlichen Schritte aus. Bei nicht vertrauenswürdigen Daten handelt es sich um Daten, die von einem Angreifer gesteuert werden können, HTML-Formular Eingaben, Abfrage Zeichenfolgen, HTTP-Header, und auch Daten, die aus einer Datenbank als Angreifer stammen, können möglicherweise die Datenbank verletzen, auch wenn Sie Ihre Anwendung nicht verletzen können.

2. Vor dem Einfügen nicht vertrauenswürdiger Daten in ein HTML-Element stellen Sie sicher, dass die HTML-Codierung vorliegt Bei der HTML-Codierung werden Zeichen wie z &lt; . b. und in eine sichere Form wie &amp; lt geändert.

3. Bevor nicht vertrauenswürdige Daten in ein HTML-Attribut gesetzt werden, stellen Sie sicher, dass die HTML-Codierung Die HTML-Attribut Codierung ist eine supermenge der HTML-Codierung und codiert zusätzliche Zeichen wie z. b. "and".

4. Vor dem Einfügen nicht vertrauenswürdiger Daten in JavaScript platzieren Sie die Daten in einem HTML-Element, dessen Inhalt Sie zur Laufzeit abrufen. Wenn dies nicht möglich ist, stellen Sie sicher, dass die Daten JavaScript-codiert sind. Die JavaScript-Codierung erfordert gefährliche Zeichen für JavaScript und ersetzt Sie durch ihre hexadezimal Zeichen, die beispielsweise &lt; als codiert werden `\u003C` .

5. Vor dem Einfügen von nicht vertrauenswürdigen Daten in eine URL-Abfrage Zeichenfolge stellen Sie sicher, dass die URL

## <a name="html-encoding-using-no-locrazor"></a>HTML-Codierung mithilfe von Razor

Die Razor Engine, die in MVC verwendet wird, codiert automatisch alle Ausgaben aus Variablen, es sei denn, Sie arbeiten wirklich hart, um dies zu verhindern. Dabei werden bei der Verwendung der-Direktive HTML-Attribut Codierungsregeln verwendet *@* . Da die HTML-Attribut Codierung eine supermenge der HTML-Codierung ist, bedeutet dies, dass Sie sich nicht darum kümmern müssen, ob Sie HTML-Codierung oder HTML-Attribut Codierung verwenden sollten. Sie müssen sicherstellen, dass Sie @ nur in einem HTML-Kontext verwenden, nicht, wenn Sie versuchen, nicht vertrauenswürdige Eingaben direkt in JavaScript einzufügen. Taghilfsprogramme codieren auch Eingaben, die Sie in tagparametern verwenden.

Sehen Sie sich die folgende Razor Ansicht an:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

In dieser Ansicht wird der Inhalt der Variablen *untreudinput ausgegeben* . Diese Variable enthält einige Zeichen, die in XSS-Angriffen verwendet werden, nämlich &lt; "und &gt; . Beim Untersuchen der Quelle wird die gerenderte Ausgabe angezeigt, die als

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.net Core MVC stellt eine- `HtmlString` Klasse bereit, die bei der Ausgabe nicht automatisch codiert wird. Dies sollte nie in Kombination mit nicht vertrauenswürdigen Eingaben verwendet werden, da dadurch ein XSS-Sicherheitsrisiko verfügbar gemacht wird.

## <a name="javascript-encoding-using-no-locrazor"></a>JavaScript-Codierung mithilfe von Razor

Es kann vorkommen, dass Sie einen Wert in JavaScript einfügen möchten, um Ihre Ansicht zu verarbeiten. Es gibt hierbei zwei Möglichkeiten. Die sicherste Möglichkeit zum Einfügen von Werten besteht darin, den Wert in einem Daten Attribut eines Tags zu platzieren und ihn in JavaScript abzurufen. Beispiel:

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

Das vorangehende Markup generiert den folgenden HTML-Code:

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

Mit dem vorangehenden Code wird die folgende Ausgabe generiert:

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> Verketten Sie ***keine*** nicht vertrauenswürdigen Eingaben in JavaScript, um DOM-Elemente zu erstellen, oder verwenden Sie `document.write()` für dynamisch generierten Inhalt.
>
> Verwenden Sie einen der folgenden Ansätze, um zu verhindern, dass Code für DOM-basiertes XSS verfügbar gemacht wird:
> * `createElement()` und weisen Eigenschaftswerte den entsprechenden Methoden oder Eigenschaften zu, wie z `node.textContent=` . b. oder. InnerText = '.
> * `document.CreateTextNode()` und fügen Sie Sie an den entsprechenden DOM-Speicherort an.
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a>Aufrufen von Encodern im Code

Die HTML-, JavaScript-und URL-Encoder sind für den Code auf zwei Arten verfügbar. Sie können Sie über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) einfügen, oder Sie können die im-Namespace enthaltenen Standard Encoder verwenden `System.Text.Encodings.Web` . Wenn Sie die Standard Encoder verwenden, werden alle, die Sie auf Zeichen Bereiche angewendet haben, als sicher behandelt werden, nicht wirksam. die Standard Codierungen verwenden die sichersten Codierungsregeln.

Um die konfigurierbaren Encoder über di verwenden zu können, sollten die Konstruktoren nach Bedarf einen *htmlencoder*-, *javascriptencoder* -und *urlencoder* -Parameter verwenden. Beispiel:

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

## <a name="encoding-url-parameters"></a>URL-Codierungs Parameter

Wenn Sie eine URL-Abfrage Zeichenfolge mit nicht vertrauenswürdiger Eingabe als Wert erstellen möchten `UrlEncoder` , verwenden Sie, um den Wert zu codieren. Ein auf ein Objekt angewendeter

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Nach der Codierung enthält die encodvalue-Variable `%22Quoted%20Value%20with%20spaces%20and%20%26%22` . Leerzeichen, Anführungszeichen, Interpunktions Zeichen und andere unsichere Zeichen werden in den hexadezimalen Wert Prozent codiert, z. b. wird ein Leerzeichen zu %20.

>[!WARNING]
> Verwenden Sie nicht vertrauenswürdige Eingaben nicht als Teil eines URL-Pfads. Übergeben Sie immer nicht vertrauenswürdige Eingaben als Abfrage Zeichenfolgen-Wert.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Anpassen der Encoder

Standardmäßig verwenden Encoder eine sichere Liste, die auf den grundlegenden lateinischen Unicode-Bereich beschränkt ist, und Codieren alle Zeichen außerhalb dieses Bereichs als Zeichencode Entsprechungen. Dieses Verhalten wirkt Razor sich auch auf das Rendering taghelper und htmlhelper aus, da es die Encoder zum Ausgeben der Zeichen folgen verwendet.

Die dahinter liegende Argumentation besteht darin, vor unbekannten oder zukünftigen Browserfehlern zu schützen (vorherige Browser Fehler haben die Analysierung basierend auf der Verarbeitung von nicht englischen Zeichen abgeglichen). Wenn Ihre Website nicht lateinische Zeichen wie Chinesisch, Kyrillisch oder andere verwendet, ist dies wahrscheinlich nicht das gewünschte Verhalten.

Sie können die Liste der Encoder-sichere Listen so anpassen, dass Sie für Ihre Anwendung geeignete Unicode-Bereiche in enthalten `ConfigureServices()` .

Wenn Sie beispielsweise die Standardkonfiguration verwenden, können Sie eine Razor htmlhelper-Methode wie folgt verwenden.

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Wenn Sie die Quelle der Webseite anzeigen, sehen Sie, dass Sie wie folgt gerendert wurde, wobei der chinesische Text codiert ist.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Um die vom Encoder als sicher behandelten Zeichen zu erweitern, fügen Sie die folgende Zeile in die- `ConfigureServices()` Methode ein `startup.cs` .

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

In diesem Beispiel wird die sichere Liste so erweitert, dass Sie den Unicode-Bereich cjkunifedideographs einschließt. Die gerenderte Ausgabe wird jetzt

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Sichere Listen Bereiche werden als Unicode-Code Diagramme, nicht als Sprachen angegeben. Der [Unicode-Standard](https://unicode.org/) enthält eine Liste von [Code Diagrammen](https://www.unicode.org/charts/index.html) , die Sie verwenden können, um das Diagramm mit ihren Zeichen zu suchen. Jeder Encoder, HTML, JavaScript und jede URL muss separat konfiguriert werden.

> [!NOTE]
> Die Anpassung der sicheren Liste wirkt sich nur auf Encoder aus, die über di übertragen werden. Wenn Sie über einen direkten Zugriff auf einen Encoder verfügen `System.Text.Encodings.Web.*Encoder.Default` , wird die standardmäßige lateinische Standard SafeList verwendet.

## <a name="where-should-encoding-take-place"></a>Wo sollte die Codierung erfolgen?

Die allgemein akzeptierte Übung besteht darin, dass die Codierung an der Stelle der Ausgabe stattfindet und codierte Werte niemals in einer Datenbank gespeichert werden sollen. Die Codierung an der Stelle der Ausgabe ermöglicht es Ihnen, die Verwendung von Daten zu ändern, z. b. von HTML in einen Abfrage Zeichen folgen Wert. Außerdem können Sie Ihre Daten problemlos durchsuchen, ohne Werte vor der Suche codieren zu müssen, und Sie können alle Änderungen oder Fehlerbehebungen von Encodern nutzen.

## <a name="validation-as-an-xss-prevention-technique"></a>Validierung als XSS-Präventionsmethode

Die Validierung kann ein nützliches Tool zum Einschränken von XSS-Angriffen sein. Beispielsweise löst eine numerische Zeichenfolge, die nur die Zeichen 0-9 enthält, keinen XSS-Angriff aus. Die Validierung wird komplizierter, wenn HTML in Benutzereingaben akzeptiert wird. Das Auswerten von HTML-Eingaben ist schwierig, wenn nicht unmöglich. Markdown ist mit einem Parser gekoppelt, der eingebettete HTML-Code entfernt, ist eine sicherere Option zum akzeptieren umfassender Eingaben. Verlassen Sie sich niemals ausschließlich auf die Validierung. Codieren Sie vor der Ausgabe immer nicht vertrauenswürdige Eingaben, unabhängig davon, welche Validierung oder Bereinigung durchgeführt wurde.
