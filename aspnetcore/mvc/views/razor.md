---
title: Razor-Syntax Referenz für ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über Razor Markup Syntax zum Einbetten von Server basiertem Code in Webseiten.
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/views/razor
ms.openlocfilehash: cb9ffab19062bf726dd519c782d502f76e372073
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058284"
---
# <a name="no-locrazor-syntax-reference-for-aspnet-core"></a>Razor Syntax Verweis für ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen)und [Dan vicarel](https://github.com/Rabadash8820)

Razor ist eine Markup Syntax zum Einbetten von Server basiertem Code in Webseiten. Die Razor Syntax besteht aus Razor Markup, c# und HTML. Dateien mit Razor der Dateierweiterung " *. cshtml* ". Razorwird auch in [ Razor Komponenten](xref:blazor/components/index) Dateien (*Razor*-Dateien) gefunden.

## <a name="rendering-html"></a>Rendern von HTML

Die Standard Razor Sprache ist HTML. Das Rendern von HTML aus Razor Markup unterscheidet sich nicht vom Rendern von HTML aus einer HTML-Datei. Das HTML-Markup in *. cshtml* - Razor Dateien wird vom Server unverändert gerendert.

## <a name="no-locrazor-syntax"></a>Syntax von Razor

Razor unterstützt c# und verwendet das `@` Symbol für den Übergang von HTML zu c#. Razor wertet c#-Ausdrücke aus und rendert Sie in der HTML-Ausgabe.

Wenn auf ein `@` Symbol ein [ Razor reserviertes Schlüsselwort](#razor-reserved-keywords)folgt, wechselt es in Razor -spezifisches Markup. Andernfalls erfolgt der Übergang in normales C#.

`@` Razor Verwenden Sie ein zweites Symbol, um ein Symbol im Markup zu versehen `@` :

```cshtml
<p>@@Username</p>
```

Der Code wird in HTML mit einem einzelnen `@`-Symbol gerendert:

```html
<p>@Username</p>
```

Bei HTML-Attributen und Inhalt mit E-Mail-Adressen wird das `@`-Symbol nicht als ein Übergangszeichen behandelt. Die e-Mail-Adressen im folgenden Beispiel werden bei der folgenden Verarbeitung nicht untersucht Razor :

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-no-locrazor-expressions"></a>Implizite Razor Ausdrücke

Implizite Razor Ausdrücke beginnen mit, `@` gefolgt von c#-Code:

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Mit Ausnahme des C#-Schlüsselworts `await` dürfen implizite Ausdrücke keine Leerzeichen enthalten. Wird die C#-Anweisung eindeutig beendet, können auch Leerzeichen verwendet werden:

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

Implizite Ausdrücke dürfen **keine** C#-Generics enthalten, da die Zeichen innerhalb der Klammern (`<>`) als HTML-Tag interpretiert werden. Der folgende Code ist **ungültig**:

```cshtml
<p>@GenericMethod<int>()</p>
```

Der vorhergehende Code erzeugt einen Compilerfehler, der folgendermaßen aussehen kann:

* Das Element „int“ wurde nicht geschlossen. Alle Elemente müssen entweder selbstschließend sein oder ein zugehöriges Endtag besitzen.
* Die Methodengruppe „GenericMethod“ kann nicht in den Nichtdelegattyp „Objekt“ konvertiert werden. Wollten Sie die Methode aufrufen?

Generische Methodenaufrufe müssen in einen [expliziten Razor Ausdruck](#explicit-razor-expressions) oder einen [ Razor Codeblock](#razor-code-blocks)umschließt werden.

## <a name="explicit-no-locrazor-expressions"></a>Explizite Razor Ausdrücke

Explizite Razor Ausdrücke bestehen aus einem `@` Symbol mit einer ausgeglichenen Klammer. Zum Rendering der letzten Woche wird das folgende Razor Markup verwendet:

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Jeglicher Inhalt innerhalb der `@()`-Klammer wird ausgewertet und in der Ausgabe gerendert.

Die im vorherigen Abschnitt beschriebenen impliziten Ausdrücke dürfen grundsätzlich keine Leerzeichen enthalten. Im folgenden Code wird eine Woche nicht von der aktuellen Uhrzeit abgezogen:

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

Der Code rendert den folgenden HTML-Code:

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

Explizite Ausdrücke können zum Verketten von Text mit einem Ergebnis des Ausdrucks verwendet werden:

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

Ohne den expliziten Ausdruck wird `<p>Age@joe.Age</p>` als E-Mail-Adresse behandelt und `<p>Age@joe.Age</p>` gerendert. `<p>Age33</p>` wird gerendert, wenn es als expliziter Ausdruck geschrieben wird.

Explizite Ausdrücke können zum Rendern der Ausgabe von generischen Methoden in *.cshtml*-Dateien verwendet werden. Das folgende Markup zeigt, wie der weiter oben gezeigte Fehler behoben wird, der durch die Klammern einer generischen C#-Funktion verursacht wurde. Der Code wird als expliziter Ausdruck geschrieben:

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a>Codieren von Ausdrücken

C#-Ausdrücke, die als Zeichenfolge ausgewertet werden, werden HTML-codiert. C#-Ausdrücke, die als `IHtmlContent` ausgewertet werden, werden direkt durch `IHtmlContent.WriteTo` gerendert. C#-Ausdrücke, die nicht als `IHtmlContent` ausgewertet werden, werden durch `ToString` in eine Zeichenfolge konvertiert und vor dem Rendern codiert.

```cshtml
@("<span>Hello World</span>")
```

Der vorangehende Code rendert den folgenden HTML-Code:

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

Der HTML-Code wird im Browser als nur-Text angezeigt:

&lt;Span &gt; Hallo Welt &lt; /span&gt;

Die Ausgabe `HtmlHelper.Raw` wird nicht codiert, sondern als HTML-Markup gerendert.

> [!WARNING]
> Die Verwendung von `HtmlHelper.Raw` bei einer nicht bereinigten Benutzereingabe stellt ein Sicherheitsrisiko dar. Benutzereingaben können schädlichen JavaScript-Code oder andere Sicherheitsrisiken enthalten. Das Bereinigen von Benutzereingaben ist schwierig. Vermeiden Sie daher die Verwendung von `HtmlHelper.Raw` bei Benutzereingaben.

```cshtml
@Html.Raw("<span>Hello World</span>")
```

Der Code rendert den folgenden HTML-Code:

```html
<span>Hello World</span>
```

## <a name="no-locrazor-code-blocks"></a>Razor Code Blöcke

Razor Code Blöcke beginnen mit `@` und werden von eingeschlossen `{}` . Im Gegensatz zu Ausdrücken wird C#-Code in Codeblöcken nicht gerendert. Codeblöcke und Ausdrücke in einer Ansicht nutzen den gleichen Bereich und werden der Reihe nach definiert:

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

Der Code rendert den folgenden HTML-Code:

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

Deklarieren Sie in Codeblöcken [lokale Funktionen](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) mit Markup als Vorlagenmethoden:

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

Der Code rendert den folgenden HTML-Code:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a>Implizite Übergänge

Die Standardsprache in einem Codeblock ist c#, aber die Razor Seite kann zurück in HTML wechseln:

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a>Durch Trennzeichen getrennte explizite Übergänge

Um einen unter Abschnitt eines Code Blocks zu definieren, der HTML rendern soll, müssen Sie die Zeichen für das Rendering mit dem-Tag umschließen Razor `<text>` :

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

Verwenden Sie diese Methode zum Rendern von HTML-Code, der nicht von einem HTML-Tag umschlossen ist. Ohne HTML oder Razor Tag Razor tritt ein Laufzeitfehler auf.

Das `<text>`-Tag ist nützlich, wenn Sie beim Rendern von Inhalt Leerzeichen steuern möchten:

* Nur der Inhalt zwischen den `<text>`-Tags wird gerendert.
* In der HTML-Ausgabe werden keine Leerzeichen vor oder nach dem `<text>`-Tag angezeigt.

### <a name="explicit-line-transition"></a>Explizite Zeilenübergänge

Verwenden Sie die `@:`-Syntax, um den Rest einer kompletten Zeile als HTML-Code in einem Codeblock zu rendern:

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

Ohne `@:` im Code Razor wird ein Laufzeitfehler generiert.

Zusätzliche `@` Zeichen in einer Razor Datei können zu Compilerfehlern bei Anweisungen später im-Block führen. Diese Compilerfehler können dann schwer nachvollziehbar sein, da der tatsächliche vor dem gemeldeten Fehler auftritt. Dieser Fehler tritt häufig auf, wenn mehrere implizite/explizite Ausdrücke in einem einzigen Codeblock kombiniert werden.

## <a name="control-structures"></a>Steuerungsstrukturen

Steuerungsstrukturen sind eine Erweiterung von Codeblöcken. Alle Aspekte von Codeblöcken (Übergang zu Markup, Inline-C#) gelten auch für die folgenden Strukturen:

### <a name="conditionals-if-else-if-else-and-switch"></a>Bedingungen `@if, else if, else, and @switch`

`@if` steuert, wann der Code ausgeführt wird:

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

`else` und `else if` erfordern kein `@`-Symbol:

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

Im folgenden Markup wird die Verwendung einer switch-Anweisung veranschaulicht:

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

### <a name="looping-for-foreach-while-and-do-while"></a>Schleifen `@for, @foreach, @while, and @do while`

Auf Vorlagen basierender HTML-Code kann mit Anweisungen zur Steuerung von Schleifen gerendert werden. So rendern Sie eine Liste mit Personen.

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

Die folgenden Schleifenanweisungen werden unterstützt:

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

### <a name="compound-using"></a>Zusammengesetztes `@using`

In C# kann mit einer `using`-Anweisung sichergestellt werden, dass ein Objekt freigegeben wurde. In Razor wird derselbe Mechanismus zum Erstellen von HTML-Hilfsprogrammen verwendet, die zusätzlichen Inhalt enthalten. Im folgenden Code wird ein `<form>`-Tag mit der `@using`-Anweisung durch HTML-Hilfsprogramme gerendert:

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

Die Behandlung von Ausnahmen ist vergleichbar mit C#:

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor bietet die Möglichkeit, kritische Abschnitte mit Lock-Anweisungen zu schützen:

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a>Kommentare

Razor unterstützt c#-und HTML-Kommentare:

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Der Code rendert den folgenden HTML-Code:

```html
<!-- HTML comment -->
```

Razor Kommentare werden vom Server entfernt, bevor die Webseite gerendert wird. Razor verwendet `@*  *@` , um Kommentare zu begrenzen. Der folgende Code ist auskommentiert, damit vom Server kein Markup gerendert wird:

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a>Anweisungen

Razor -Anweisungen werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem- `@` Symbol dargestellt. Eine Anweisung ändert in der Regel die Analyse einer Ansicht oder aktiviert unterschiedliche Funktionen.

RazorWenn Sie verstehen, wie Code für eine Sicht generiert, ist es einfacher, die Funktionsweise von Anweisungen zu verstehen.

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

Durch den Code wird eine Klasse ähnlich der folgenden generiert:

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

Weiter unten in diesem Artikel wird der Abschnitt unter [Suchen der Razor c#-Klasse, die für eine Sicht generiert](#inspect-the-razor-c-class-generated-for-a-view) wurde erläutert, wie diese generierte Klasse angezeigt wird.

### `@attribute`

Die `@attribute`-Anweisung fügt das angegebene Attribut zu der Klasse der generierten Seite oder Ansicht hinzu. Im folgenden Beispiel wird das `[Authorize]`-Attribut hinzugefügt:

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Der- `@code` Block ermöglicht einer [ Razor Komponente](xref:blazor/components/index) das Hinzufügen von c#-Membern (Feldern, Eigenschaften und Methoden) zu einer Komponente:

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

Für Razor -Komponenten `@code` ist ein Alias von [`@functions`](#functions) und wird für empfohlen `@functions` . Mehrere `@code`-Blöcke sind zulässig.

::: moniker-end

### `@functions`

Die `@functions`-Anweisung ermöglicht das Hinzufügen von C#-Membern (Feldern, Eigenschaften und Methoden) zur generierten Klasse:

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

Verwenden Sie in- [ Razor Komponenten](xref:blazor/components/index), `@code` `@functions` um c#-Member hinzuzufügen.

::: moniker-end

Beispiel:

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

Der Code generiert das folgende HTML-Markup:

```html
<div>From method: Hello</div>
```

Der folgende Code ist die generierte Razor c#-Klasse:

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

`@functions`-Methoden dienen als Vorlagenmethoden, wenn sie Markup aufweisen:

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

Der Code rendert den folgenden HTML-Code:

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse.

Im folgenden Beispiel wird <xref:System.IDisposable?displayProperty=fullName> implementiert, sodass die <xref:System.IDisposable.Dispose*>-Methode aufgerufen werden kann:

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

Die `@inherits`-Anweisung bietet uneingeschränkten Zugriff auf die Klasse, die die Ansicht erbt:

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

Der folgende Code ist ein benutzerdefinierter Razor Seitentyp:

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

`CustomText` wird in einer Ansicht angezeigt:

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

Der Code rendert den folgenden HTML-Code:

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 `@model` und `@inherits` können in derselben Ansicht verwendet werden. `@inherits` kann in einer *_ViewImports.cshtml*-Datei verwendet werden, die von der Ansicht importiert wird:

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

Der folgende Code ist ein Beispiel für eine stark typisierte Ansicht:

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

Wird „rick@contoso.com“ im Modell übergeben, generiert die Ansicht das folgende HTML-Markup:

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

Die- `@inject` Direktive ermöglicht der Razor Seite, einen Dienst aus dem [Dienst Container](xref:fundamentals/dependency-injection) in eine Ansicht einzufügen. Weitere Informationen finden Sie unter [Dependency Injection in Ansichten](xref:mvc/views/dependency-injection).

::: moniker range=">= aspnetcore-3.0"

### `@layout`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Die- `@layout` Anweisung gibt ein Layout für Routing fähige Razor Komponenten an, die über eine- [`@page`](#page) Direktive verfügen. Layoutkomponenten werden verwendet, um Codeduplizierung und Inkonsistenzen zu vermeiden. Weitere Informationen finden Sie unter <xref:blazor/layouts>.

::: moniker-end

### `@model`

*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*

Die `@model`-Anweisung gibt den Typ des Modells an, das an eine Ansicht oder Seite übergeben wird:

```cshtml
@model TypeNameOfModel
```

In einer ASP.net Core MVC Razor -oder Pages-APP, die mit einzelnen Benutzerkonten erstellt wurde, enthält *views/Account/Login. cshtml* die folgende Modell Deklaration:

```cshtml
@model LoginViewModel
```

Die generierte Klasse erbt von `RazorPage<dynamic>`:

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor macht eine `Model` Eigenschaft für den Zugriff auf das an die Ansicht über gegebene Modell verfügbar:

```cshtml
<div>The Login Email: @Model.Email</div>
```

Die `@model`-Anweisung gibt den Typ der `Model`-Eigenschaft an. Die Anweisung legt das `T` in `RazorPage<T>` der generierten Klasse fest, von der die Ansicht abgeleitet wird. Wird die `@model`-Anweisung nicht angegeben, hat die `Model`-Eigenschaft den Typ `dynamic`. Weitere Informationen finden Sie unter [stark typisierte Modelle und das @model Schlüsselwort](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).

### `@namespace`

Die `@namespace`-Anweisung:

* Legt den Namespace der Klasse der generierten Razor Seite, MVC-Ansicht oder Komponente fest Razor .
* Legt die von der nächstgelegenen Import Datei in der Verzeichnisstruktur *_ViewImports. cshtml* (Views oder Pages) oder *_Imports. Razor* ( Razor Components) Stamm abgeleiteten Namespaces der Seiten, Sichten oder Komponenten Klassen fest.

```cshtml
@namespace Your.Namespace.Here
```

Für das Razor Beispiel "Pages" in der folgenden Tabelle:

* Jede Seite importiert *Pages/_ViewImports.cshtml*.
* *Pages/_ViewImports.cshtml* enthält `@namespace Hello.World`.
* Jede Seite weist `Hello.World` als Stamm ihres Namespace auf.

| erweitert?“                                        | Namespace                             |
| ------------------------------------------- | ------------------------------------- |
| *Pages/index. cshtml*                        | `Hello.World`                         |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages`               |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Hello.World.MorePages.EvenMorePages` |

Die vorstehenden Beziehungen gelten für Import Dateien, die mit MVC-Ansichten und-Komponenten verwendet werden Razor .

Wenn mehrere Importdateien über eine `@namespace`-Anweisung verfügen, wird die Datei verwendet, die der Seite, der Ansicht oder der Komponente in der Verzeichnisstruktur am nächsten ist, um den Stammnamespace festzulegen.

Wenn der Ordner *EvenMorePages* im Beispiel oben eine Importdatei mit `@namespace Another.Planet` enthält (oder die Datei *Pages/MorePages/EvenMorePages/Page.cshtml*`@namespace Another.Planet` enthält), finden Sie das Ergebnis in der folgenden Tabelle.

| erweitert?“                                        | Namespace               |
| ------------------------------------------- | ----------------------- |
| *Pages/index. cshtml*                        | `Hello.World`           |
| *Pages/MorePages/Page.cshtml*               | `Hello.World.MorePages` |
| *Pages/MorePages/EvenMorePages/Page.cshtml* | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

Die `@page`-Anweisung hat abhängig vom Typ der Datei, in der Sie verwendet wird, unterschiedliche Auswirkungen. Für die Anweisung gilt:

* In einer *cshtml* -Datei gibt an, dass die Datei eine Razor Seite ist. Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes) und <xref:razor-pages/index>.
* Gibt an, dass eine Razor Komponente Anforderungen direkt verarbeiten soll. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Die- `@page` Direktive in der ersten Zeile einer *cshtml* -Datei gibt an, dass die Datei eine Razor Seite ist. Weitere Informationen finden Sie unter <xref:razor-pages/index>.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### `@preservewhitespace`

*Dieses Szenario gilt nur für- Razor Komponenten ( `.razor` ).*

Wenn die Einstellung auf `false` (Standard) festgelegt ist, werden Leerzeichen im gerenderten Markup aus Razor Komponenten ( `.razor` ) entfernt, wenn Folgendes gilt:

* Sie stehen in einem Element am Anfang oder am Ende.
* Sie stehen in einem `RenderFragment`-Parameter am Anfang oder am Ende. Ein Beispiel hierfür ist untergeordneter Inhalt, der an eine andere Komponente übergeben wird
* Sie stehen am Anfang oder Ende eines C#-Codeblocks wie `@if` oder `@foreach`.

::: moniker-end

### `@section`

*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*

Die `@section` -Direktive wird zusammen mit [MVC-und Razor Seitenlayouts](xref:mvc/views/layout) verwendet, um Ansichten oder Seiten zum Rendering von Inhalten in verschiedenen Teilen der HTML-Seite zu aktivieren. Weitere Informationen finden Sie unter <xref:mvc/views/layout>.

### `@using`

Die `@using`-Anweisung fügt die C#-Anweisung `using`der generierten Ansicht hinzu:

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

In- [ Razor Komponenten](xref:blazor/components/index) `@using` steuert auch, welche Komponenten sich im Gültigkeitsbereich befinden.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a>Direktivenattribute

Razor Direktivenattribute werden durch implizite Ausdrücke mit reservierten Schlüsselwörtern nach dem- `@` Symbol dargestellt. Ein direktivenattribut ändert in der Regel die Art und Weise, in der ein Element analysiert wird, oder unterstützt

### `@attributes`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

`@attributes` ermöglicht es einer Komponente, nicht deklarierte Attribute zu rendern. Weitere Informationen finden Sie unter <xref:blazor/components/index#attribute-splatting-and-arbitrary-parameters>.

### `@bind`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Die Datenbindung in-Komponenten wird mit dem `@bind`-Attribut erreicht. Weitere Informationen finden Sie unter <xref:blazor/components/data-binding>.

### `@on{EVENT}`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Razor stellt Funktionen für die Ereignis Behandlung für-Komponenten bereit. Weitere Informationen finden Sie unter <xref:blazor/components/event-handling>.

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Verhindert die Standardaktion für das Ereignis.

### `@on{EVENT}:stopPropagation`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Beendet die Ereignisweitergabe für das Ereignis.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Das Direktivenattribut `@key` bewirkt, dass der Komponentenvergleichsalgorithmus die Erhaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert. Weitere Informationen finden Sie unter <xref:blazor/components/index#use-key-to-control-the-preservation-of-elements-and-components>.

### `@ref`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Komponentenverweise (`@ref`) bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle für diese Instanz ausgeben können. Weitere Informationen finden Sie unter <xref:blazor/components/index#capture-references-to-components>.

### `@typeparam`

*Dieses Szenario gilt nur für Razor Komponenten (Razor).*

Die `@typeparam`-Anweisung deklariert einen generischen Typparameter für die generierte Komponentenklasse. Weitere Informationen finden Sie unter <xref:blazor/components/templated-components#generic-typed-components>.

::: moniker-end

## <a name="templated-no-locrazor-delegates"></a>Auf Vorlagen basierende Delegaten Razor

Razor mithilfe von Vorlagen können Sie einen UI-Code Ausschnitt im folgenden Format definieren:

```cshtml
@<tag>...</tag>
```

Im folgenden Beispiel wird veranschaulicht, wie Sie einen Vorlagen basierten Delegaten Razor als angeben <xref:System.Func%602> . Der Typ [dynamic](/dotnet/csharp/programming-guide/types/using-type-dynamic) wird für den Parameter der Methode angegeben, die der Delegat einkapselt. Ein [object](/dotnet/csharp/language-reference/keywords/object)-Typ wird als Rückgabewert des Delegats angegeben. Die Vorlage wird mit <xref:System.Collections.Generic.List%601> von `Pet` mit einer `Name`-Eigenschaft verwendet.

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

Die Vorlage wird mit `pets` in einer `foreach`-Anweisung gerendert:

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

Gerenderte Ausgabe:

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

Sie können auch eine Inline Razor Vorlage als Argument für eine Methode bereitstellen. Im folgenden Beispiel empfängt die- `Repeat` Methode eine Razor Vorlage. Die Methode verwendet die Vorlage dann zum Erstellen von HTML-Inhalten mit Wiederholungen von Elementen aus einer Liste:

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

Wird die Liste mit Haustieren aus dem vorherigen Beispiel verwendet, wird die `Repeat`-Methode wie folgt aufgerufen:

* <xref:System.Collections.Generic.List%601> von `Pet`
* Anzahl der Wiederholungen für jedes Haustier
* Inlinevorlage zur Verwendung für die Elemente einer unsortierten Liste

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

Gerenderte Ausgabe:

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

## <a name="tag-helpers"></a>Taghilfsprogramme

*Dieses Szenario gilt nur für MVC-Ansichten und- Razor Seiten (. cshtml).*

Die folgenden drei Anweisungen gehören zu den [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro).

| Anweisung | Funktion |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | Macht Taghilfsprogramme für eine Ansicht verfügbar. |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | Entfernt zuvor hinzugefügte Taghilfsprogramme aus einer Ansicht. |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | Gibt ein Tagpräfix an, um Unterstützung für Taghilfsprogramme zu aktivieren und ihre Verwendung explizit zu machen. |

## <a name="no-locrazor-reserved-keywords"></a>Razor reservierte Schlüsselwörter

### <a name="no-locrazor-keywords"></a>Razor Keywords

* `page` (Erfordert ASP.net Core 2,1 oder höher)
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* `helper` (Wird zurzeit nicht von ASP.net Core unterstützt)

Razor Schlüsselwörter werden mit Escapezeichen versehen `@(Razor Keyword)` (z `@(functions)` . b.).

### <a name="c-no-locrazor-keywords"></a>C#- Razor Schlüsselwörter

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

C# Razor -Schlüsselwörter müssen mit einem doppelten Escapezeichen versehen werden `@(@C# Razor Keyword)` (z `@(@case)` . b.). Der erste schützt `@` den Razor Parser. Das zweite `@` dient als Escapezeichen für den C#-Parser.

### <a name="reserved-keywords-not-used-by-no-locrazor"></a>Reservierte Schlüsselwörter werden von nicht verwendet Razor

* `class`

## <a name="inspect-the-no-locrazor-c-class-generated-for-a-view"></a>Überprüfen der Razor für eine Sicht generierten c#-Klasse

::: moniker range=">= aspnetcore-2.1"

Bei .net Core SDK 2,1 oder höher übernimmt das [ Razor SDK](xref:razor-pages/sdk) die Kompilierung von Razor Dateien. Beim Entwickeln eines Projekts generiert das Razor SDK eine *obj/<build_configuration>/<target_framework_moniker>/ Razor* Verzeichnis im Stammverzeichnis des Projekts. Die Verzeichnisstruktur im *Razor* Verzeichnis spiegelt die Verzeichnisstruktur des Projekts wider.

Sehen Sie sich die folgende Verzeichnisstruktur in einem ASP.net Core 2,1 pages-Projekt an, das auf Razor .net Core 2,1 abzielt:

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

Wenn Sie das Projekt mit der Konfiguration zum *Debuggen* erstellen, wird folgendes *obj*-Verzeichnis erstellt:

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

Öffnen Sie *obj/Debug/netcoreapp 2.1/ Razor /pages/index.g.cshtml.cs*, um die generierte Klasse für *pages/index. cshtml* anzuzeigen.

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

Fügen Sie dem ASP.NET Core MVC-Projekt die folgende Klasse hinzu:

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

Überschreiben Sie in `Startup.ConfigureServices` die von MVC hinzugefügte `RazorTemplateEngine`-Klasse mit der `CustomTemplateEngine`-Klasse:

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

Legen Sie auf der `return csharpDocument;`-Anweisung von `CustomTemplateEngine` einen Haltepunkt fest. Wenn die Ausführung des Programms am Haltepunkt angehalten wird, können Sie den Wert von `generatedCode` anzeigen.

![Ansicht „Text-Schnellansicht“ von „generatedCode“](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a>Ansicht der Suchvorgänge und Groß-/Kleinschreibung

Die Razor Ansichts-Engine führt Suchvorgänge bei der Suche nach Groß-und Kleinschreibung durch. Der tatsächliche Suchvorgang wird jedoch vom zugrunde liegenden Dateisystem bestimmt:

* Dateibasierte Quelle:
  * Bei Betriebssystemen, die Dateisysteme ohne Berücksichtigung von Groß-/Kleinschreibung verwenden (z.B. Windows), wird bei Suchvorgängen nach physischen Dateianbietern die Groß- und Kleinschreibung nicht berücksichtigt. `return View("Test")` liefert beispielsweise Treffer für */Views/Home/Test.cshtml*, */Views/home/test.cshtml* sowie für jede andere Schreibweise.
  * Bei Dateisystemen, die die Groß-/Kleinschreibung berücksichtigen (z.B. Linux, OSX sowie mit `EmbeddedFileProvider`), wird die Groß-/Kleinschreibung auch bei Suchvorgängen berücksichtigt. `return View("Test")` liefert beispielsweise ganz konkret Treffer für */Views/Home/Test.cshtml*.
* Vorkompilierte Ansichten: Ab ASP.NET Core 2.0 wird bei der Suche nach vorkompilierten Ansichten unter allen Betriebssystemen die Groß-/Kleinschreibung nicht berücksichtigt. Das Verhalten ist mit dem des physischen Dateianbieters unter Windows identisch. Unterscheiden sich zwei vorkompilierte Ansichten nur in der Groß-/Kleinschreibung, ist das Ergebnis der Suche nicht deterministisch.

Entwicklern wird empfohlen, sich bei der Groß-/Kleinschreibung von Datei- und Verzeichnisnamen an der Schreibweise folgender Begriffe zu orientieren:

* Bereichs-, Controller- und Aktionsnamen
* Razor Seiten.

Durch Überprüfung der Groß-/Kleinschreibung wird sichergestellt, dass die entsprechenden Ansichten für die Bereitstellungen unabhängig von dem zugrunde liegenden Dateisystem gefunden werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Einführung in ASP.net-Webprogrammierung mithilfe Razor von Die Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) bietet viele Beispiele für die Programmierung mit Razor Syntax.
