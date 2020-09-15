---
title: Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/19/2020
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
uid: blazor/components/index
ms.openlocfilehash: be1584e72fc1504ac9f8ca10a6b084c95a579b5b
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009621"
---
# <a name="create-and-use-aspnet-core-no-locrazor-components"></a><span data-ttu-id="7496e-103">Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="7496e-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="7496e-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) und [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="7496e-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="7496e-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7496e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7496e-106">Blazor-Apps werden mithilfe von *Komponenten* erstellt.</span><span class="sxs-lookup"><span data-stu-id="7496e-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="7496e-107">Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="7496e-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="7496e-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7496e-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="7496e-109">Komponenten sind flexibel und kompakt.</span><span class="sxs-lookup"><span data-stu-id="7496e-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="7496e-110">Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="7496e-111">Komponentenklassen</span><span class="sxs-lookup"><span data-stu-id="7496e-111">Component classes</span></span>

<span data-ttu-id="7496e-112">Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien (`.razor`) implementiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="7496e-113">Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="7496e-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="no-locrazor-syntax"></a><span data-ttu-id="7496e-114">Razor-Syntax</span><span class="sxs-lookup"><span data-stu-id="7496e-114">Razor syntax</span></span>

<span data-ttu-id="7496e-115">Razor-Komponenten in Blazor-Apps verwenden Razor-Syntax ausführlich.</span><span class="sxs-lookup"><span data-stu-id="7496e-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="7496e-116">Wenn Sie mit der Razor-Markupsprache nicht vertraut sind, sollten Sie <xref:mvc/views/razor> lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="7496e-116">If you aren't familiar with the Razor markup language, we recommend reading <xref:mvc/views/razor> before proceeding.</span></span>

<span data-ttu-id="7496e-117">Beachten Sie beim Zugriff auf den Inhalt der Razor-Syntax besonders die folgenden Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="7496e-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="7496e-118">[Direktiven](xref:mvc/views/razor#directives): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie das Komponentenmarkup analysiert wird oder funktioniert.</span><span class="sxs-lookup"><span data-stu-id="7496e-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="7496e-119">[Direktivenattribute](xref:mvc/views/razor#directive-attributes): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie Komponentenelemente analysiert werden oder funktionieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="7496e-120">Namen</span><span class="sxs-lookup"><span data-stu-id="7496e-120">Names</span></span>

<span data-ttu-id="7496e-121">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="7496e-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="7496e-122">Beispielsweise ist `MyCoolComponent.razor` zulässig, aber `myCoolComponent.razor` ist unzulässig.</span><span class="sxs-lookup"><span data-stu-id="7496e-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="7496e-123">Routing</span><span class="sxs-lookup"><span data-stu-id="7496e-123">Routing</span></span>

<span data-ttu-id="7496e-124">Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App.</span><span class="sxs-lookup"><span data-stu-id="7496e-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="7496e-125">Wenn eine Razor-Datei mit einer [`@page`][9]-Anweisung kompiliert wird, wird der generierten Klasse ein <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben.</span><span class="sxs-lookup"><span data-stu-id="7496e-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="7496e-126">Zur Laufzeit sucht der Router nach Komponentenklassen mit <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="7496e-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="7496e-127">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="7496e-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="7496e-128">Markup</span><span class="sxs-lookup"><span data-stu-id="7496e-128">Markup</span></span>

<span data-ttu-id="7496e-129">Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="7496e-130">Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen *Razor* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7496e-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="7496e-131">Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="7496e-132">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="7496e-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="7496e-133">Member der Komponentenklasse werden in einem [`@code`][1]-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="7496e-134">Im [`@code`][1]-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="7496e-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="7496e-135">Mehrere [`@code`][1]-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="7496e-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="7496e-136">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="7496e-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="7496e-137">Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="7496e-138">Das Beispiel wertet Folgendes aus und führt ein Rendering durch:</span><span class="sxs-lookup"><span data-stu-id="7496e-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="7496e-139">`headingFontStyle` in den CSS-Eigenschaftswert für `font-style`</span><span class="sxs-lookup"><span data-stu-id="7496e-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="7496e-140">`headingText` in den Inhalt des `<h1>`-Elements</span><span class="sxs-lookup"><span data-stu-id="7496e-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="7496e-141">Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="7496e-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="7496e-142">Blazor vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.</span><span class="sxs-lookup"><span data-stu-id="7496e-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span>

<span data-ttu-id="7496e-143">Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-143">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="7496e-144">Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner `Pages`.</span><span class="sxs-lookup"><span data-stu-id="7496e-144">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="7496e-145">Nicht für Seiten verwendete Komponenten werden häufig im Ordner `Shared` oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="7496e-145">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="7496e-146">Namespaces</span><span class="sxs-lookup"><span data-stu-id="7496e-146">Namespaces</span></span>

<span data-ttu-id="7496e-147">In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="7496e-147">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="7496e-148">Wenn der Stammnamespace der App `BlazorSample` lautet und sich die `Counter` Komponente im Ordner `Pages` befindet, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7496e-148">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="7496e-149">der Namespace der `Counter`-Komponente `BlazorSample.Pages`, und</span><span class="sxs-lookup"><span data-stu-id="7496e-149">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="7496e-150">der vollqualifizierten Typname der Komponente ist `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="7496e-150">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="7496e-151">Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine [`@using`][2]-Anweisung zur übergeordneten Komponente oder zur `_Imports.razor`-Datei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="7496e-151">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="7496e-152">Das folgende Beispiel stellt Komponenten im Ordner `Components` zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="7496e-152">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="7496e-153">Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`][2] nicht erforderlich:</span><span class="sxs-lookup"><span data-stu-id="7496e-153">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="7496e-154">Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):</span><span class="sxs-lookup"><span data-stu-id="7496e-154">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="7496e-155">[`@namespace`][8]-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="7496e-155">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="7496e-156">Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)</span><span class="sxs-lookup"><span data-stu-id="7496e-156">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="7496e-157">Der Projektname, der aus dem Namen der Projektdatei (`.csproj`) und dem Pfad vom Projektstamm zur Komponente resultiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-157">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="7496e-158">Ein Beispiel: Das Framework löst `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) in den Namespace `BlazorSample.Pages` auf.</span><span class="sxs-lookup"><span data-stu-id="7496e-158">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="7496e-159">Komponenten folgen den Namensbindungsregeln für C#.</span><span class="sxs-lookup"><span data-stu-id="7496e-159">Components follow C# name binding rules.</span></span> <span data-ttu-id="7496e-160">Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="7496e-160">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="7496e-161">Im gleichen Ordner: `Pages`.</span><span class="sxs-lookup"><span data-stu-id="7496e-161">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="7496e-162">Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben</span><span class="sxs-lookup"><span data-stu-id="7496e-162">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="7496e-163">Die `global::`-Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-163">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="7496e-164">Das Importieren von Komponenten mit [`using`](/dotnet/csharp/language-reference/keywords/using-statement)-Aliasanweisungen (z. B. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-164">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="7496e-165">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-165">Partially qualified names aren't supported.</span></span> <span data-ttu-id="7496e-166">Das Hinzufügen von `@using BlazorSample` und das Verweisen auf die `NavMenu`-Komponente (`NavMenu.razor`) mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-166">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="7496e-167">Unterstützung für partielle Klassen</span><span class="sxs-lookup"><span data-stu-id="7496e-167">Partial class support</span></span>

<span data-ttu-id="7496e-168">Razor-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-168">Razor components are generated as partial classes.</span></span> <span data-ttu-id="7496e-169">Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="7496e-169">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="7496e-170">C#-Code wird in einem [`@code`][1]-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="7496e-170">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="7496e-171">Blazor-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="7496e-171">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="7496e-172">C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="7496e-172">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="7496e-173">Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem [`@code`][1]-Block in einer App, die aus einer Blazor-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="7496e-173">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="7496e-174">HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="7496e-174">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="7496e-175">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-175">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="7496e-176">Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="7496e-176">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="7496e-177">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-177">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="7496e-178">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="7496e-178">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="7496e-179">Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="7496e-179">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="7496e-180">Folgende typischen Namespaces werden von Razor-Komponenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="7496e-180">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="7496e-181">[`@using`][2]-Direktive in der `_Imports.razor`-Datei werden nur auf Razor-Dateien (`.razor`) angewendet und nicht auf C#-Dateien (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="7496e-181">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="7496e-182">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="7496e-182">Specify a base class</span></span>

<span data-ttu-id="7496e-183">Die [`@inherits`][6]-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7496e-183">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="7496e-184">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="7496e-184">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="7496e-185">Die Basisklasse sollte von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-185">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="7496e-186">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-186">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="7496e-187">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="7496e-187">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="7496e-188">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="7496e-188">Use components</span></span>

<span data-ttu-id="7496e-189">Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-189">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="7496e-190">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="7496e-190">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="7496e-191">Das folgende Markup in `Pages/Index.razor` rendert eine `HeadingComponent`-Instanz:</span><span class="sxs-lookup"><span data-stu-id="7496e-191">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="7496e-192">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-192">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="7496e-193">Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="7496e-193">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="7496e-194">Durch das Hinzufügen einer [`@using`][2]-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.</span><span class="sxs-lookup"><span data-stu-id="7496e-194">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="7496e-195">Parameter</span><span class="sxs-lookup"><span data-stu-id="7496e-195">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="7496e-196">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="7496e-196">Route parameters</span></span>

<span data-ttu-id="7496e-197">Komponenten können Routenparameter von der Routenvorlage empfangen, die in der [`@page`][9]-Anweisung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-197">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="7496e-198">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="7496e-198">The router uses route parameters to populate the corresponding component parameters.</span></span>

<span data-ttu-id="7496e-199">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-199">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

<span data-ttu-id="7496e-200">Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei [`@page`][9]-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="7496e-200">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="7496e-201">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="7496e-201">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="7496e-202">Die zweite [`@page`][9]-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="7496e-202">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

<span data-ttu-id="7496e-203">Informationen zu Routenparametern (`{*pageRoute}`), die Pfade über mehrere Ordnergrenzen hinweg erfassen, finden Sie unter <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="7496e-203">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="7496e-204">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="7496e-204">Component parameters</span></span>

<span data-ttu-id="7496e-205">Komponenten können *Komponentenparameter* enthalten, die mithilfe öffentlicher Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-205">Components can have *component parameters*, which are defined using public properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="7496e-206">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7496e-206">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="7496e-207">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-207">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="7496e-208">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.</span><span class="sxs-lookup"><span data-stu-id="7496e-208">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="7496e-209">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-209">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

> [!WARNING]
> <span data-ttu-id="7496e-210">Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld.</span><span class="sxs-lookup"><span data-stu-id="7496e-210">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="7496e-211">Weitere Informationen finden Sie im Abschnitt [Überschriebene Parameter](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="7496e-211">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="7496e-212">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="7496e-212">Child content</span></span>

<span data-ttu-id="7496e-213">Komponenten können den Inhalt anderer Komponenten festlegen.</span><span class="sxs-lookup"><span data-stu-id="7496e-213">Components can set the content of another component.</span></span> <span data-ttu-id="7496e-214">Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-214">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="7496e-215">Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="7496e-215">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="7496e-216">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="7496e-216">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="7496e-217">Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.</span><span class="sxs-lookup"><span data-stu-id="7496e-217">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="7496e-218">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-218">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="7496e-219">Die Eigenschaft, die den Inhalt von <xref:Microsoft.AspNetCore.Components.RenderFragment> empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-219">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="7496e-220">Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-220">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="7496e-221">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-221">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="7496e-222">Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="7496e-222">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <ChildComponent Param1="@c">
>         Child Content: Count: @current
>     </ChildComponent>
> }
> ```
>
> <span data-ttu-id="7496e-223">Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:</span><span class="sxs-lookup"><span data-stu-id="7496e-223">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Param1="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="7496e-224">Attributsplatting und arbiträre Parameter</span><span class="sxs-lookup"><span data-stu-id="7496e-224">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="7496e-225">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern.</span><span class="sxs-lookup"><span data-stu-id="7496e-225">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="7496e-226">Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`][3]-Anweisung Razor gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-226">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="7496e-227">Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-227">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="7496e-228">Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-228">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="7496e-229">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="7496e-229">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="7496e-230">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` mit Zeichenfolgenschlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-230">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="7496e-231">Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:</span><span class="sxs-lookup"><span data-stu-id="7496e-231">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="7496e-232">Definieren Sie einen Komponentenparameter mithilfe des [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attributs, bei dem die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="7496e-232">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="7496e-233">Die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft für [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ermöglicht dem Parameter den Abgleich aller Attribute, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="7496e-233">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="7496e-234">Eine Komponente kann nur einen einzelnen Parameter mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> definieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-234">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="7496e-235">Der mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="7496e-235">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="7496e-236">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.</span><span class="sxs-lookup"><span data-stu-id="7496e-236">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="7496e-237">Die Position von [`@attributes`][3] relativ zur Position der Elementattribute ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="7496e-237">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="7496e-238">Wenn [`@attributes`][3]-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7496e-238">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="7496e-239">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="7496e-239">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="7496e-240">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-240">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="7496e-241">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-241">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="7496e-242">Das `extra`-Attribut der `Child`-Komponente ist rechts von [`@attributes`][3] festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7496e-242">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="7496e-243">Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="7496e-243">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="7496e-244">Im folgenden Beispiel wird die Reihenfolge von `extra` und [`@attributes`][3] in `<div>` der `Child`-Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="7496e-244">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="7496e-245">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-245">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="7496e-246">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7496e-246">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="7496e-247">Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="7496e-247">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="7496e-248">Erfassen von Verweisen auf Komponenten</span><span class="sxs-lookup"><span data-stu-id="7496e-248">Capture references to components</span></span>

<span data-ttu-id="7496e-249">Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="7496e-249">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="7496e-250">So erfassen Sie einen Komponentenverweis:</span><span class="sxs-lookup"><span data-stu-id="7496e-250">To capture a component reference:</span></span>

* <span data-ttu-id="7496e-251">Fügen Sie der untergeordneten Komponente ein [`@ref`][4]-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="7496e-251">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="7496e-252">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="7496e-252">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="7496e-253">Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `MyLoginDialog`-Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="7496e-253">When the component is rendered, the `loginDialog` field is populated with the `MyLoginDialog` child component instance.</span></span> <span data-ttu-id="7496e-254">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7496e-254">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7496e-255">Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="7496e-255">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="7496e-256">Vor dem Rendern der Komponente gibt es nichts, auf das verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="7496e-256">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="7496e-257">Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [`OnAfterRenderAsync`- oder die `OnAfterRender`-Methode](xref:blazor/components/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="7496e-257">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="7496e-258">Um eine Verweisvariable mit einem Ereignishandler zu verwenden, verwenden Sie einen Lambdaausdruck oder weisen einen Ereignishandlerdelegaten in den [Methoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) zu.</span><span class="sxs-lookup"><span data-stu-id="7496e-258">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="7496e-259">So ist sichergestellt, dass die Verweisvariable vor dem Ereignishandler zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-259">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="7496e-260">Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="7496e-260">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="7496e-261">Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature.</span><span class="sxs-lookup"><span data-stu-id="7496e-261">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="7496e-262">Komponentenverweise werden nicht an JavaScript-Code übermittelt.</span><span class="sxs-lookup"><span data-stu-id="7496e-262">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="7496e-263">Komponentenverweise werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="7496e-263">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="7496e-264">Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern.</span><span class="sxs-lookup"><span data-stu-id="7496e-264">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="7496e-265">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="7496e-265">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="7496e-266">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-266">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="7496e-267">Synchronisierungskontext</span><span class="sxs-lookup"><span data-stu-id="7496e-267">Synchronization context</span></span>

<span data-ttu-id="7496e-268">Blazor verwendet einen Synchronisierungskontext (<xref:System.Threading.SynchronizationContext>), um einen einzelnen logischen Ausführungsthread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="7496e-268">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="7496e-269">Die [Lebenszyklusmethoden](xref:blazor/components/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7496e-269">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="7496e-270">Der Synchronisierungskontext des Blazor Server versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist.</span><span class="sxs-lookup"><span data-stu-id="7496e-270">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="7496e-271">Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht.</span><span class="sxs-lookup"><span data-stu-id="7496e-271">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="7496e-272">Zwei Vorgänge werden nicht gleichzeitig ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7496e-272">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="7496e-273">Vermeiden Sie eine Threadblockierung von Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="7496e-273">Avoid thread-blocking calls</span></span>

<span data-ttu-id="7496e-274">Rufen Sie generell folgende Methoden nicht auf.</span><span class="sxs-lookup"><span data-stu-id="7496e-274">Generally, don't call the following methods.</span></span> <span data-ttu-id="7496e-275">Die folgenden Methoden blockieren den Thread und hindern somit die App an der Wiederaufnahme der Arbeit, bis der zugrunde liegende <xref:System.Threading.Tasks.Task> beendet ist:</span><span class="sxs-lookup"><span data-stu-id="7496e-275">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="7496e-276">Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status</span><span class="sxs-lookup"><span data-stu-id="7496e-276">Invoke component methods externally to update state</span></span>

<span data-ttu-id="7496e-277">Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-277">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="7496e-278">Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="7496e-278">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="7496e-279">Registrieren des `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="7496e-279">Register the `NotifierService`:</span></span>

* <span data-ttu-id="7496e-280">Registrieren Sie in Blazor WebAssembly den Dienst als Singleton in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="7496e-280">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="7496e-281">Registrieren Sie in Blazor Server den Dienst bereichsbezogen in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7496e-281">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="7496e-282">Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="7496e-282">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="7496e-283">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf.</span><span class="sxs-lookup"><span data-stu-id="7496e-283">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="7496e-284">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="7496e-284">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="7496e-285">Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten</span><span class="sxs-lookup"><span data-stu-id="7496e-285">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="7496e-286">Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7496e-286">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="7496e-287">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.</span><span class="sxs-lookup"><span data-stu-id="7496e-287">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="7496e-288">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7496e-288">Consider the following example:</span></span>

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

<span data-ttu-id="7496e-289">Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="7496e-289">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="7496e-290">Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann.</span><span class="sxs-lookup"><span data-stu-id="7496e-290">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="7496e-291">Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen.</span><span class="sxs-lookup"><span data-stu-id="7496e-291">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="7496e-292">In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="7496e-292">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="7496e-293">Der Zuordnungsprozess kann mit dem [`@key`][5]-Anweisungsattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-293">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="7496e-294">[`@key`][5] bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert:</span><span class="sxs-lookup"><span data-stu-id="7496e-294">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="7496e-295">Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="7496e-295">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="7496e-296">Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="7496e-296">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="7496e-297">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="7496e-297">Other instances are left unchanged.</span></span>
* <span data-ttu-id="7496e-298">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="7496e-298">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="7496e-299">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="7496e-299">Other instances are left unchanged.</span></span>
* <span data-ttu-id="7496e-300">Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="7496e-300">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="7496e-301">In einigen Szenarios minimiert die Verwendung von [`@key`][5] die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="7496e-301">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7496e-302">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="7496e-302">Keys are local to each container element or component.</span></span> <span data-ttu-id="7496e-303">Schlüssel werden nicht dokumentübergreifend global verglichen.</span><span class="sxs-lookup"><span data-stu-id="7496e-303">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="7496e-304">Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="7496e-304">When to use \@key</span></span>

<span data-ttu-id="7496e-305">In der Regel ist es sinnvoll, [`@key`][5] zu verwenden, wenn eine Liste gerendert wird (z. B. in einem [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Block) und ein geeigneter Wert vorhanden ist, um den [`@key`][5] zu definieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-305">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="7496e-306">Sie können [`@key`][5] auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="7496e-306">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="7496e-307">Wenn `@currentPerson` geändert wird, zwingt die [`@key`][5]-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7496e-307">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="7496e-308">Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-308">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="7496e-309">Ungeeignete Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="7496e-309">When not to use \@key</span></span>

<span data-ttu-id="7496e-310">Bei einem Vergleich mit [`@key`][5] wird die Leistung beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="7496e-310">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="7496e-311">Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten [`@key`][5] jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.</span><span class="sxs-lookup"><span data-stu-id="7496e-311">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="7496e-312">Auch wenn [`@key`][5] nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="7496e-312">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="7496e-313">Der einzige Vorteil bei der Verwendung von [`@key`][5] besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet werden, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="7496e-313">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="7496e-314">Zu verwendende Werte für \@key</span><span class="sxs-lookup"><span data-stu-id="7496e-314">What values to use for \@key</span></span>

<span data-ttu-id="7496e-315">Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für [`@key`][5] bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="7496e-315">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="7496e-316">Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel):</span><span class="sxs-lookup"><span data-stu-id="7496e-316">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="7496e-317">Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="7496e-317">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="7496e-318">Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):</span><span class="sxs-lookup"><span data-stu-id="7496e-318">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="7496e-319">Stellen Sie sicher, dass die für [`@key`][5] verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="7496e-319">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="7496e-320">Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="7496e-320">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="7496e-321">Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.</span><span class="sxs-lookup"><span data-stu-id="7496e-321">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="7496e-322">Überschriebene Parameter</span><span class="sxs-lookup"><span data-stu-id="7496e-322">Overwritten parameters</span></span>

<span data-ttu-id="7496e-323">Es werden neue Parameterwerte bereitgestellt, mit denen in der Regel vorhandene Parameter überschrieben werden, wenn die übergeordnete Komponente wiederholt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-323">New parameter values are supplied, typically overwriting existing ones, when the parent component rerenders.</span></span>

<span data-ttu-id="7496e-324">Angenommen, die folgende `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="7496e-324">Consider the following `Expander` component that:</span></span>

* <span data-ttu-id="7496e-325">rendert untergeordneten Inhalt.</span><span class="sxs-lookup"><span data-stu-id="7496e-325">Renders child content.</span></span>
* <span data-ttu-id="7496e-326">schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter um.</span><span class="sxs-lookup"><span data-stu-id="7496e-326">Toggles showing child content with a component parameter.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
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

<span data-ttu-id="7496e-327">Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen kann:</span><span class="sxs-lookup"><span data-stu-id="7496e-327">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="7496e-328">Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-328">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="7496e-329">Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei.</span><span class="sxs-lookup"><span data-stu-id="7496e-329">The child components maintain their states as expected.</span></span> <span data-ttu-id="7496e-330">Wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`).</span><span class="sxs-lookup"><span data-stu-id="7496e-330">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="7496e-331">Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-331">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="7496e-332">Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="7496e-332">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="7496e-333">Die folgende überarbeitete `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="7496e-333">The following revised `Expander` component:</span></span>

* <span data-ttu-id="7496e-334">akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="7496e-334">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="7496e-335">weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/components/lifecycle#component-initialization-methods) zu.</span><span class="sxs-lookup"><span data-stu-id="7496e-335">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="7496e-336">verwendet das private Feld, um seinen internen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="7496e-336">Uses the private field to maintain its internal toggle state.</span></span>

```razor
<div @onclick="@Toggle" class="card bg-light mb-3" style="width:30rem">
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

## <a name="apply-an-attribute"></a><span data-ttu-id="7496e-337">Hinzufügen eines Attributs</span><span class="sxs-lookup"><span data-stu-id="7496e-337">Apply an attribute</span></span>

<span data-ttu-id="7496e-338">Attribute können in Razor-Komponenten mit der [`@attribute`][7]-Anweisung hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-338">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="7496e-339">Im folgenden Beispiel wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Komponentenklasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="7496e-339">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="7496e-340">Attribute für bedingte HTML-Elemente</span><span class="sxs-lookup"><span data-stu-id="7496e-340">Conditional HTML element attributes</span></span>

<span data-ttu-id="7496e-341">HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="7496e-341">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="7496e-342">Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="7496e-342">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="7496e-343">Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="7496e-343">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="7496e-344">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="7496e-344">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="7496e-345">Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="7496e-345">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="7496e-346">Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="7496e-346">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="7496e-347">Weitere Informationen finden Sie unter <xref:mvc/views/razor>.</span><span class="sxs-lookup"><span data-stu-id="7496e-347">For more information, see <xref:mvc/views/razor>.</span></span>

> [!WARNING]
> <span data-ttu-id="7496e-348">Einige HTML-Attribute wie [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist.</span><span class="sxs-lookup"><span data-stu-id="7496e-348">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="7496e-349">Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="7496e-349">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="7496e-350">Unformatierter HTML-Code</span><span class="sxs-lookup"><span data-stu-id="7496e-350">Raw HTML</span></span>

<span data-ttu-id="7496e-351">Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="7496e-351">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="7496e-352">Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen.</span><span class="sxs-lookup"><span data-stu-id="7496e-352">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="7496e-353">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="7496e-353">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="7496e-354">Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-354">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="7496e-355">Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="7496e-355">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="no-locrazor-templates"></a><span data-ttu-id="7496e-356">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="7496e-356">Razor templates</span></span>

<span data-ttu-id="7496e-357">Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-357">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="7496e-358">Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:</span><span class="sxs-lookup"><span data-stu-id="7496e-358">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="7496e-359">Im folgenden Beispiel wird veranschaulicht, wie Sie <xref:Microsoft.AspNetCore.Components.RenderFragment>- und <xref:Microsoft.AspNetCore.Components.RenderFragment%601>-Werte angeben und Vorlagen direkt in einer-Komponente rendern können.</span><span class="sxs-lookup"><span data-stu-id="7496e-359">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="7496e-360">Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/components/templated-components) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-360">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="7496e-361">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="7496e-361">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="7496e-362">Statische Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7496e-362">Static assets</span></span>

<span data-ttu-id="7496e-363">Blazor befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen im [`web root (wwwroot)`-Ordner](xref:fundamentals/index#web-root) des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="7496e-363">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="7496e-364">Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="7496e-364">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="7496e-365">Im folgenden Beispiel befindet sich `logo.png` physisch im Ordner `{PROJECT ROOT}/wwwroot/images`:</span><span class="sxs-lookup"><span data-stu-id="7496e-365">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="7496e-366">Razor-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).</span><span class="sxs-lookup"><span data-stu-id="7496e-366">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="7496e-367">Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="7496e-367">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="7496e-368">Keine Unterstützung von Taghilfsprogrammen in Komponenten</span><span class="sxs-lookup"><span data-stu-id="7496e-368">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="7496e-369">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten (`.razor`-Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-369">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="7496e-370">Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.</span><span class="sxs-lookup"><span data-stu-id="7496e-370">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="7496e-371">SVG-Bilder</span><span class="sxs-lookup"><span data-stu-id="7496e-371">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="7496e-372">Da Blazor HTML rendert, werden browsergestützte Bilder wie skalierbare Vektorgrafiken (Scalable Vector Graphics, `.svg`) über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="7496e-372">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="7496e-373">Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei (`.css`) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="7496e-373">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="7496e-374">SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7496e-374">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="7496e-375">Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei (`.razor`) einfügen, wird das grundlegende Bildrendering unterstützt, viele fortgeschrittene Szenarios aber noch nicht.</span><span class="sxs-lookup"><span data-stu-id="7496e-375">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="7496e-376">Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und [`@bind`][10] kann nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7496e-376">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="7496e-377">Weitere Informationen finden Sie unter [Improve SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271) (Verbessern der SVG-Unterstützung in Blazor).</span><span class="sxs-lookup"><span data-stu-id="7496e-377">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7496e-378">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7496e-378">Additional resources</span></span>

* <span data-ttu-id="7496e-379"><xref:blazor/security/server/threat-mitigation>: Enthält Anleitungen zum Erstellen von Blazor Server-Apps, die Ressourcenauslastung handhaben müssen.</span><span class="sxs-lookup"><span data-stu-id="7496e-379"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

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
