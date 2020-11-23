---
title: Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/components/index
ms.openlocfilehash: cc4604f7f67a6648c96e099572ff27bfed838916
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981868"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a>Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Tobias Bartsch](https://www.aveo-solutions.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Blazor-Apps werden mithilfe von *Komponenten* erstellt. Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular. Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist. Komponenten sind flexibel und kompakt. Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.

## <a name="component-classes"></a>Komponentenklassen

Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien (`.razor`) implementiert. Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.

### <a name="no-locrazor-syntax"></a>Razor-Syntax

Razor-Komponenten in Blazor-Apps verwenden Razor-Syntax ausführlich. Wenn Sie mit der Razor-Markupsprache nicht vertraut sind, sollten Sie <xref:mvc/views/razor> lesen, bevor Sie fortfahren.

Beachten Sie beim Zugriff auf den Inhalt der Razor-Syntax besonders die folgenden Abschnitte:

* [Direktiven](xref:mvc/views/razor#directives): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie das Komponentenmarkup analysiert wird oder funktioniert.
* [Direktivenattribute](xref:mvc/views/razor#directive-attributes): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie Komponentenelemente analysiert werden oder funktionieren.

### <a name="names"></a>Namen

Der Name einer Komponente muss mit einem Großbuchstaben beginnen. Beispielsweise ist `MyCoolComponent.razor` zulässig, aber `myCoolComponent.razor` ist unzulässig.

### <a name="routing"></a>Routing

Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App. Wenn eine Razor-Datei mit einer [`@page`][9]-Anweisung kompiliert wird, wird der generierten Klasse ein <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben. Zur Laufzeit sucht der Router nach Komponentenklassen mit <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing>.

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a>Markup

Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert. Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen *Razor* hinzugefügt. Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert. Der Name der generierten Klasse entspricht dem Namen der Datei.

Member der Komponentenklasse werden in einem [`@code`][1]-Block definiert. Im [`@code`][1]-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben. Mehrere [`@code`][1]-Blöcke sind zulässig.

Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen. Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird. Das Beispiel wertet Folgendes aus und führt ein Rendering durch:

* `headingFontStyle` in den CSS-Eigenschaftswert für `font-style`
* `headingText` in den Inhalt des `<h1>`-Elements

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut. Blazor vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.

Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden. Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner `Pages`. Nicht für Seiten verwendete Komponenten werden häufig im Ordner `Shared` oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.

### <a name="namespaces"></a>Namespaces

In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet. Wenn der Stammnamespace der App `BlazorSample` lautet und sich die `Counter` Komponente im Ordner `Pages` befindet, gilt Folgendes:

* der Namespace der `Counter`-Komponente `BlazorSample.Pages`, und
* der vollqualifizierten Typname der Komponente ist `BlazorSample.Pages.Counter`.

Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine [`@using`][2]-Anweisung zur übergeordneten Komponente oder zur `_Imports.razor`-Datei der App hinzu. Das folgende Beispiel stellt Komponenten im Ordner `Components` zur Verfügung:

```razor
@using BlazorSample.Components
```

Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`][2] nicht erforderlich:

```razor
<BlazorSample.Components.MyComponent />
```

Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):

* [`@namespace`][8]-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei (`.razor`).
* Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)
* Der Projektname, der aus dem Namen der Projektdatei (`.csproj`) und dem Pfad vom Projektstamm zur Komponente resultiert. Ein Beispiel: Das Framework löst `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) in den Namespace `BlazorSample.Pages` auf. Komponenten folgen den Namensbindungsregeln für C#. Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:
  * Im gleichen Ordner: `Pages`.
  * Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben

> [!NOTE]
> Die `global::`-Qualifizierung wird nicht unterstützt.
>
> Das Importieren von Komponenten mit [`using`](/dotnet/csharp/language-reference/keywords/using-statement)-Aliasanweisungen (z. B. `@using Foo = Bar`) wird nicht unterstützt.
>
> Teilweise qualifizierte Namen werden nicht unterstützt. Das Hinzufügen von `@using BlazorSample` und das Verweisen auf die `NavMenu`-Komponente (`NavMenu.razor`) mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.

### <a name="partial-class-support"></a>Unterstützung für partielle Klassen

Razor-Komponenten werden als partielle Klassen generiert. Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:

* C#-Code wird in einem [`@code`][1]-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert. Blazor-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.
* C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.

Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem [`@code`][1]-Block in einer App, die aus einer Blazor-Vorlage generiert wurde. HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:

`Pages/Counter.razor`:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

`Counter.razor.cs`:

```csharp
namespace BlazorSample.Pages
{
    public partial class Counter
    {
        private int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu. Folgende typischen Namespaces werden von Razor-Komponenten verwendet:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> [`@using`][2]-Direktive in der `_Imports.razor`-Datei werden nur auf Razor-Dateien (`.razor`) angewendet und nicht auf C#-Dateien (`.cs`).

### <a name="specify-a-base-class"></a>Angeben einer Basisklasse

Die [`@inherits`][6]-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben. Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen. Die Basisklasse sollte von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleitet werden.

`Pages/BlazorRocks.razor`:

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

`BlazorRocksBase.cs`:

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

### <a name="use-components"></a>Verwenden von Komponenten

Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren. Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.

Das folgende Markup in `Pages/Index.razor` rendert eine `HeadingComponent`-Instanz:

```razor
<HeadingComponent />
```

`Components/HeadingComponent.razor`:

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist. Durch das Hinzufügen einer [`@using`][2]-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.

## <a name="parameters"></a>Parameter

### <a name="route-parameters"></a>Routenparameter

Komponenten können Routenparameter von der Routenvorlage empfangen, die in der [`@page`][9]-Anweisung bereitgestellt wird. Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.

::: moniker range=">= aspnetcore-5.0"

Optionale Parameter werden unterstützt. Im folgenden Beispiel weist der optionale Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu. Wenn das Segment nicht vorhanden ist, wird der Wert von `Text` auf `fantastic` festgelegt.

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

`Pages/RouteParameter.razor`:

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei [`@page`][9]-Anweisungen angewendet. Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter. Die zweite [`@page`][9]-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.

::: moniker-end

Informationen zu Routenparametern (`{*pageRoute}`), die Pfade über mehrere Ordnergrenzen hinweg erfassen, finden Sie unter <xref:blazor/fundamentals/routing#catch-all-route-parameters>.

### <a name="component-parameters"></a>Komponentenparameter

Komponenten können *Komponentenparameter* enthalten, die mithilfe öffentlicher einfacher oder komplexer Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert werden. Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

Gemäß Konvention wird ein aus C#-Code bestehender Attributwert einem Parameter mithilfe des [reservierten `@`-Symbols von Razor zugewiesen](xref:mvc/views/razor#razor-syntax):

* Übergeordnetes Feld oder übergeordnete Eigenschaft: `Title="@{FIELD OR PROPERTY}`, wobei der Platzhalter `{FIELD OR PROPERTY}` ein C#-Feld oder eine C#-Eigenschaft der übergeordneten Komponente ist.
* Ergebnis einer Methode: `Title="@{METHOD}"`, wobei der Platzhalter `{METHOD}` eine C#-Methode der übergeordneten Komponente ist.
* [Impliziter oder expliziter Ausdruck](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, wobei der Platzhalter `{EXPRESSION}` ein C#-Ausdruck ist.
  
Weitere Informationen finden Sie unter <xref:mvc/views/razor>.

> [!WARNING]
> Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld. Weitere Informationen finden Sie im Abschnitt [Überschriebene Parameter](#overwritten-parameters).

## <a name="child-content"></a>Untergeordneter Inhalt

Komponenten können den Inhalt anderer Komponenten festlegen. Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.

Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt. Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll. Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.

`Components/ChildComponent.razor`:

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> Die Eigenschaft, die den Inhalt von <xref:Microsoft.AspNetCore.Components.RenderFragment> empfängt, muss gemäß der Konvention `ChildContent` benannt werden.

Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.

`Pages/ParentComponent.razor`:

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente verwendet wird:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Title="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attributsplatting und arbiträre Parameter

Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern. Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`][3]-Anweisung Razor gerendert wird. Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt. Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.

Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:

```razor
<input id="useIndividualParams"
       maxlength="@maxlength"
       placeholder="@placeholder"
       required="@required"
       size="@size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    public string maxlength = "10";
    public string placeholder = "Input placeholder text";
    public string required = "required";
    public string size = "50";

    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` mit Zeichenfolgenschlüsseln implementieren.

Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

Definieren Sie einen Komponentenparameter mithilfe des [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attributs, bei dem die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

Die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft für [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ermöglicht dem Parameter den Abgleich aller Attribute, die keinem anderen Parameter entsprechen. Eine Komponente kann nur einen einzelnen Parameter mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> definieren. Der mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können. `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.

Die Position von [`@attributes`][3] relativ zur Position der Elementattribute ist wichtig. Wenn [`@attributes`][3]-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet. Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Das `extra`-Attribut der `Child`-Komponente ist rechts von [`@attributes`][3] festgelegt. Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:

```html
<div extra="5" />
```

Im folgenden Beispiel wird die Reihenfolge von `extra` und [`@attributes`][3] in `<div>` der `Child`-Komponente umgekehrt:

`ParentComponent.razor`:

```razor
<ChildComponent extra="10" />
```

`ChildComponent.razor`:

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>Erfassen von Verweisen auf Komponenten

Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können. So erfassen Sie einen Komponentenverweis:

* Fügen Sie der untergeordneten Komponente ein [`@ref`][4]-Attribut hinzu.
* Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.

```razor
<CustomLoginDialog @ref="loginDialog" ... />

@code {
    private CustomLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `MyLoginDialog`-Komponenteninstanz aufgefüllt. Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.

> [!IMPORTANT]
> Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält. Vor dem Rendern der Komponente gibt es nichts, auf das verwiesen werden kann.
>
> Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [`OnAfterRenderAsync`- oder die `OnAfterRender`-Methode](xref:blazor/components/lifecycle#after-component-render) verwenden.
>
> Um eine Verweisvariable mit einem Ereignishandler zu verwenden, verwenden Sie einen Lambdaausdruck oder weisen einen Ereignishandlerdelegaten in den [Methoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) zu. So ist sichergestellt, dass die Verweisvariable vor dem Ereignishandler zugewiesen wird.
>
> ```razor
> <button type="button" 
>     @onclick="@(() => loginDialog.DoSomething())">Do Something</button>
>
> <MyLoginDialog @ref="loginDialog" ... />
>
> @code {
>     private MyLoginDialog loginDialog;
> }
> ```

Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).

Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature. Komponentenverweise werden nicht an JavaScript-Code übermittelt. Komponentenverweise werden nur in .NET-Code verwendet.

> [!NOTE]
> Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern. Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben. Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.

## <a name="synchronization-context"></a>Synchronisierungskontext

Blazor verwendet einen Synchronisierungskontext (<xref:System.Threading.SynchronizationContext>), um einen einzelnen logischen Ausführungsthread zu erzwingen. Die [Lebenszyklusmethoden](xref:blazor/components/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.

Der Synchronisierungskontext des Blazor Server versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist. Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht. Zwei Vorgänge werden nicht gleichzeitig ausgeführt.

### <a name="avoid-thread-blocking-calls"></a>Vermeiden Sie eine Threadblockierung von Aufrufen.

Rufen Sie generell folgende Methoden nicht auf. Die folgenden Methoden blockieren den Thread und hindern somit die App an der Wiederaufnahme der Arbeit, bis der zugrunde liegende <xref:System.Threading.Tasks.Task> beendet ist:

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a>Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status

Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird. Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

Registrieren des `NotifierService`:

* Registrieren Sie in Blazor WebAssembly den Dienst als Singleton in `Program.Main`:

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* Registrieren Sie in Blazor Server den Dienst bereichsbezogen in `Startup.ConfigureServices`:

  ```csharp
  services.AddScoped<NotifierService>();
  ```

Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf. `InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten

Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen. Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.

Betrachten Sie das folgende Beispiel:

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern. Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann. Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen. In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.

Der Zuordnungsprozess kann mit dem [`@key`][5]-Anweisungsattribut gesteuert werden. [`@key`][5] bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:

* Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt. Andere Instanzen bleiben unverändert.
* Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt. Andere Instanzen bleiben unverändert.
* Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.

In einigen Szenarios minimiert die Verwendung von [`@key`][5] die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.

> [!IMPORTANT]
> Schlüssel sind für jedes Containerelement oder jede Komponente lokal. Schlüssel werden nicht dokumentübergreifend global verglichen.

### <a name="when-to-use-key"></a>Anwendungsfälle für \@key

In der Regel ist es sinnvoll, [`@key`][5] zu verwenden, wenn eine Liste gerendert wird (z. B. in einem [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Block) und ein geeigneter Wert vorhanden ist, um den [`@key`][5] zu definieren.

Sie können [`@key`][5] auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

Wenn `@currentPerson` geändert wird, zwingt die [`@key`][5]-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen. Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.

### <a name="when-not-to-use-key"></a>Ungeeignete Anwendungsfälle für \@key

Bei einem Vergleich mit [`@key`][5] wird die Leistung beeinträchtigt. Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten [`@key`][5] jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.

Auch wenn [`@key`][5] nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei. Der einzige Vorteil bei der Verwendung von [`@key`][5] besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet werden, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.

### <a name="what-values-to-use-for-key"></a>Zu verwendende Werte für \@key

Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für [`@key`][5] bereitzustellen:

* Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel): Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.
* Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):

Stellen Sie sicher, dass die für [`@key`][5] verwendeten Werte nicht kollidieren. Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können. Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.

## <a name="overwritten-parameters"></a>Überschriebene Parameter

Das Blazor-Framework erzwingt im Allgemeinen eine sichere Parameterzuordnung von über- und untergeordneten Elementen:

* Parameter werden nicht unerwartet überschrieben.
* Nebeneffekte werden minimiert. Beispielsweise werden zusätzliche Rendervorgänge vermieden, da Sie zu unendlichen Renderingschleifen führen können.

Eine untergeordnete Komponente empfängt neue Parameterwerte, die möglicherweise vorhandene Werte überschreiben, wenn die übergeordnete Komponente erneut gerendert wird. Das versehentliches Überschreiben von Parameterwerten in einer untergeordneten Komponente tritt häufig auf, wenn die Komponente mit mindestens einem datengebundenen Parameter entwickelt wird und der Entwickler direkt in einen Parameter im untergeordneten Element schreibt:

* Die untergeordnete Komponente wird mit mindestens einem Parameterwert aus der übergeordneten Komponente gerendert.
* Das untergeordnete Element schreibt direkt in den Wert eines Parameters.
* Die übergeordnete Komponente wird erneut gerendert und überschreibt den Wert des Parameters des untergeordneten Elements.

Das Potenzial, Parameterwerte zu überschreiben, gilt auch für Eigenschaftensetter der untergeordneten Komponente.

**Unsere allgemeine Richtlinie besagt, dass keine Komponenten erstellt werden sollen, die direkt in ihre eigenen Parameter schreiben.**

Angenommen, die folgende fehlerhafte `Expander`-Komponente:

* Rendert untergeordneten Inhalt.
* Schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter (`Expanded`) um.
* Die Komponente schreibt direkt in den `Expanded`-Parameter, der das Problem mit überschriebenen Parametern veranschaulicht, das vermieden werden sollte.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>Expanded</code> = @Expanded)</h2>

        @if (Expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    private void Toggle()
    {
        Expanded = !Expanded;
    }
}
```

Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen kann:

```razor
@page "/expander"

<Expander Expanded="true">
    Expander 1 content
</Expander>

<Expander Expanded="true" />

<button @onclick="StateHasChanged">
    Call StateHasChanged
</button>
```

Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden. Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei. Wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`). Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.

Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.

Die folgende überarbeitete `Expander`-Komponente:

* akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.
* weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/components/lifecycle#component-initialization-methods) zu.
* Verwendet das private Feld, um den internen Umschaltzustand zu verwalten. Dies veranschaulicht, wie das direkte Schreiben in einen Parameter vermieden wird.

```razor
<div @onclick="Toggle" class="card bg-light mb-3" style="width:30rem">
    <div class="card-body">
        <h2 class="card-title">Toggle (<code>expanded</code> = @expanded)</h2>

        @if (expanded)
        {
            <p class="card-text">@ChildContent</p>
        }
    </div>
</div>

@code {
    private bool expanded;

    [Parameter]
    public bool Expanded { get; set; }

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    protected override void OnInitialized()
    {
        expanded = Expanded;
    }

    private void Toggle()
    {
        expanded = !expanded;
    }
}
```

Weitere Informationen finden Sie unter [Bidirektionaler Blazor-Bindungsfehler (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599). 

## <a name="apply-an-attribute"></a>Hinzufügen eines Attributs

Attribute können in Razor-Komponenten mit der [`@attribute`][7]-Anweisung hinzugefügt werden. Im folgenden Beispiel wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Komponentenklasse angewendet:

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a>Attribute für bedingte HTML-Elemente

HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert. Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert. Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.

Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" checked />
```

Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:

```html
<input type="checkbox" />
```

Weitere Informationen finden Sie unter <xref:mvc/views/razor>.

> [!WARNING]
> Einige HTML-Attribute wie [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist. Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.

## <a name="raw-html"></a>Unformatierter HTML-Code

Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird. Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen. Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.

> [!WARNING]
> Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.

Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a>Razor-Vorlagen

Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden. Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:

```razor
@<{HTML tag}>...</{HTML tag}>
```

Im folgenden Beispiel wird veranschaulicht, wie Sie <xref:Microsoft.AspNetCore.Components.RenderFragment>- und <xref:Microsoft.AspNetCore.Components.RenderFragment%601>-Werte angeben und Vorlagen direkt in einer-Komponente rendern können. Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/components/templated-components) übergeben werden.

```razor
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

Gerenderte Ausgabe des vorangehenden Codes:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a>Statische Ressourcen

Blazor befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen im [`web root (wwwroot)`-Ordner](xref:fundamentals/index#web-root) des Projekts zu speichern.

Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen. Im folgenden Beispiel befindet sich `logo.png` physisch im Ordner `{PROJECT ROOT}/wwwroot/images`:

```razor
<img alt="Company logo" src="/images/logo.png" />
```

Razor-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).

Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="tag-helpers-arent-supported-in-components"></a>Keine Unterstützung von Taghilfsprogrammen in Komponenten

[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten (`.razor`-Dateien) nicht unterstützt. Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.

## <a name="scalable-vector-graphics-svg-images"></a>SVG-Bilder

Da Blazor HTML rendert, werden browsergestützte Bilder wie skalierbare Vektorgrafiken (Scalable Vector Graphics, `.svg`) über das `<img>`-Tag unterstützt:

```html
<img alt="Example image" src="some-image.svg" />
```

Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei (`.css`) unterstützt:

```css
.my-element {
    background-image: url("some-image.svg");
}
```

SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt. Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei (`.razor`) einfügen, wird das grundlegende Bildrendering unterstützt, viele fortgeschrittene Szenarios aber noch nicht. Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und [`@bind`][10] kann nicht mit einigen SVG-Tags verwendet werden. Weitere Informationen finden Sie unter [Improve SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271) (Verbessern der SVG-Unterstützung in Blazor).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/server/threat-mitigation>: Enthält Anleitungen zum Erstellen von Blazor Server-Apps, die Ressourcenauslastung handhaben müssen.

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code>
[2]: <xref:mvc/views/razor#using>
[3]: <xref:mvc/views/razor#attributes>
[4]: <xref:mvc/views/razor#ref>
[5]: <xref:mvc/views/razor#key>
[6]: <xref:mvc/views/razor#inherits>
[7]: <xref:mvc/views/razor#attribute>
[8]: <xref:mvc/views/razor#namespace>
[9]: <xref:mvc/views/razor#page>
[10]: <xref:mvc/views/razor#bind>
