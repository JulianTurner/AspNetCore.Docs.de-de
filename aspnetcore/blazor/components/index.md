---
title: Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten erstellen und verwenden, Datenbindungen durchführen, Ereignisse behandeln und die Lebenszyklen von Komponenten verwalten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2020
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
ms.openlocfilehash: efc73022d0bd8c29595f9bed1c06fe07002eb54a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530098"
---
# <a name="create-and-use-aspnet-core-razor-components"></a><span data-ttu-id="f9ba4-103">Erstellen und Verwenden von ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="f9ba4-103">Create and use ASP.NET Core Razor components</span></span>

<span data-ttu-id="f9ba4-104">Von [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27) [Scott Addie](https://github.com/scottaddie) und [Tobias Bartsch](https://www.aveo-solutions.com/)</span><span class="sxs-lookup"><span data-stu-id="f9ba4-104">By [Luke Latham](https://github.com/guardrex), [Daniel Roth](https://github.com/danroth27), [Scott Addie](https://github.com/scottaddie), and [Tobias Bartsch](https://www.aveo-solutions.com/)</span></span>

<span data-ttu-id="f9ba4-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f9ba4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f9ba4-106">Blazor-Apps werden mithilfe von *Komponenten* erstellt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-106">Blazor apps are built using *components*.</span></span> <span data-ttu-id="f9ba4-107">Eine Komponente ist ein eigenständiges Element einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-107">A component is a self-contained chunk of user interface (UI), such as a page, dialog, or form.</span></span> <span data-ttu-id="f9ba4-108">Eine Komponente enthält HTML-Markup und die Verarbeitungslogik, die zum Einfügen von Daten oder zum Reagieren auf Benutzeroberflächenereignisse erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-108">A component includes HTML markup and the processing logic required to inject data or respond to UI events.</span></span> <span data-ttu-id="f9ba4-109">Komponenten sind flexibel und kompakt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-109">Components are flexible and lightweight.</span></span> <span data-ttu-id="f9ba4-110">Sie können geschachtelt, wiederverwendet und für mehrere Projekte freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-110">They can be nested, reused, and shared among projects.</span></span>

## <a name="component-classes"></a><span data-ttu-id="f9ba4-111">Komponentenklassen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-111">Component classes</span></span>

<span data-ttu-id="f9ba4-112">Komponenten werden mithilfe einer Kombination aus C# und HTML-Markup in [Razor](xref:mvc/views/razor)-Komponentendateien (`.razor`) implementiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-112">Components are implemented in [Razor](xref:mvc/views/razor) component files (`.razor`) using a combination of C# and HTML markup.</span></span> <span data-ttu-id="f9ba4-113">Eine Komponente in Blazor wird formal als *Razor-Komponente* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-113">A component in Blazor is formally referred to as a *Razor component*.</span></span>

### <a name="razor-syntax"></a><span data-ttu-id="f9ba4-114">Razor-Syntax</span><span class="sxs-lookup"><span data-stu-id="f9ba4-114">Razor syntax</span></span>

<span data-ttu-id="f9ba4-115">Razor-Komponenten in Blazor-Apps verwenden Razor-Syntax ausführlich.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-115">Razor components in Blazor apps extensively use Razor syntax.</span></span> <span data-ttu-id="f9ba4-116">Wenn Sie mit der Razor-Markupsprache nicht vertraut sind, empfiehlt es sich, die [Razor-Syntaxreferenz für ASP.NET Core](xref:mvc/views/razor) zu lesen, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-116">If you aren't familiar with the Razor markup language, we recommend reading [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor) before proceeding.</span></span>

<span data-ttu-id="f9ba4-117">Beachten Sie beim Zugriff auf den Inhalt der Razor-Syntax besonders die folgenden Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-117">When accessing the content on Razor syntax, pay special attention to the following sections:</span></span>

* <span data-ttu-id="f9ba4-118">[Direktiven](xref:mvc/views/razor#directives): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie das Komponentenmarkup analysiert wird oder funktioniert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-118">[Directives](xref:mvc/views/razor#directives): `@`-prefixed reserved keywords that typically change the way component markup is parsed or function.</span></span>
* <span data-ttu-id="f9ba4-119">[Direktivenattribute](xref:mvc/views/razor#directive-attributes): Reservierte Schlüsselwörter mit `@`-Präfix, die in der Regel die Art und Weise ändern, wie Komponentenelemente analysiert werden oder funktionieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-119">[Directive attributes](xref:mvc/views/razor#directive-attributes): `@`-prefixed reserved keywords that typically change the way component elements are parsed or function.</span></span>

### <a name="names"></a><span data-ttu-id="f9ba4-120">Namen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-120">Names</span></span>

<span data-ttu-id="f9ba4-121">Der Name einer Komponente muss mit einem Großbuchstaben beginnen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-121">A component's name must start with an uppercase character.</span></span> <span data-ttu-id="f9ba4-122">Beispielsweise ist `MyCoolComponent.razor` zulässig, aber `myCoolComponent.razor` ist unzulässig.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-122">For example, `MyCoolComponent.razor` is valid, and `myCoolComponent.razor` is invalid.</span></span>

### <a name="routing"></a><span data-ttu-id="f9ba4-123">Routing</span><span class="sxs-lookup"><span data-stu-id="f9ba4-123">Routing</span></span>

<span data-ttu-id="f9ba4-124">Das Routing in Blazor erfolgt durch die Bereitstellung einer Routenvorlage für jede zugängliche Komponente in der App.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-124">Routing in Blazor is achieved by providing a route template to each accessible component in the app.</span></span> <span data-ttu-id="f9ba4-125">Wenn eine Razor-Datei mit einer [`@page`][9]-Anweisung kompiliert wird, wird der generierten Klasse ein <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> hinzugefügt und so die Routenvorlage angegeben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-125">When a Razor file with an [`@page`][9] directive is compiled, the generated class is given a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> specifying the route template.</span></span> <span data-ttu-id="f9ba4-126">Zur Laufzeit sucht der Router nach Komponentenklassen mit <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> und rendert die Komponente, deren Routenvorlage mit der angeforderten URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-126">At runtime, the router looks for component classes with a <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> and renders whichever component has a route template that matches the requested URL.</span></span> <span data-ttu-id="f9ba4-127">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-127">For more information, see <xref:blazor/fundamentals/routing>.</span></span>

```razor
@page "/ParentComponent"

...
```

### <a name="markup"></a><span data-ttu-id="f9ba4-128">Markup</span><span class="sxs-lookup"><span data-stu-id="f9ba4-128">Markup</span></span>

<span data-ttu-id="f9ba4-129">Die Benutzeroberfläche einer Komponente wird mithilfe von HTML definiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-129">The UI for a component is defined using HTML.</span></span> <span data-ttu-id="f9ba4-130">Dynamische Renderinglogik (z. B. Schleifen, Bedingungen, Ausdrücke) wird über eine eingebettete C#-Syntax mit dem Namen *Razor* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-130">Dynamic rendering logic (for example, loops, conditionals, expressions) is added using an embedded C# syntax called *Razor*.</span></span> <span data-ttu-id="f9ba4-131">Wenn eine App kompiliert wird, werden das HTML-Markup und die C#-Renderinglogik in eine Komponentenklasse konvertiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-131">When an app is compiled, the HTML markup and C# rendering logic are converted into a component class.</span></span> <span data-ttu-id="f9ba4-132">Der Name der generierten Klasse entspricht dem Namen der Datei.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-132">The name of the generated class matches the name of the file.</span></span>

<span data-ttu-id="f9ba4-133">Member der Komponentenklasse werden in einem [`@code`][1]-Block definiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-133">Members of the component class are defined in an [`@code`][1] block.</span></span> <span data-ttu-id="f9ba4-134">Im [`@code`][1]-Block wird der Komponentenstatus (Eigenschaften, Felder) mit Methoden für die Behandlung von Ereignissen oder das Definieren anderer Komponentenlogik angegeben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-134">In the [`@code`][1] block, component state (properties, fields) is specified with methods for event handling or for defining other component logic.</span></span> <span data-ttu-id="f9ba4-135">Mehrere [`@code`][1]-Blöcke sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-135">More than one [`@code`][1] block is permissible.</span></span>

<span data-ttu-id="f9ba4-136">Komponentenmember können als Teil der Renderinglogik der Komponente mithilfe von C#-Ausdrücken verwendet werden, die mit `@`beginnen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-136">Component members can be used as part of the component's rendering logic using C# expressions that start with `@`.</span></span> <span data-ttu-id="f9ba4-137">Beispielsweise wird ein C#-Feld gerendert, indem `@` dem Feldnamen vorangestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-137">For example, a C# field is rendered by prefixing `@` to the field name.</span></span> <span data-ttu-id="f9ba4-138">Das Beispiel wertet Folgendes aus und führt ein Rendering durch:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-138">The following example evaluates and renders:</span></span>

* <span data-ttu-id="f9ba4-139">`headingFontStyle` in den CSS-Eigenschaftswert für `font-style`</span><span class="sxs-lookup"><span data-stu-id="f9ba4-139">`headingFontStyle` to the CSS property value for `font-style`.</span></span>
* <span data-ttu-id="f9ba4-140">`headingText` in den Inhalt des `<h1>`-Elements</span><span class="sxs-lookup"><span data-stu-id="f9ba4-140">`headingText` to the content of the `<h1>` element.</span></span>

```razor
<h1 style="font-style:@headingFontStyle">@headingText</h1>

@code {
    private string headingFontStyle = "italic";
    private string headingText = "Put on your new Blazor!";
}
```

<span data-ttu-id="f9ba4-141">Nachdem die Komponente zum ersten Mal gerendert wurde, generiert sie ihre Renderingstruktur als Reaktion auf Ereignisse erneut.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-141">After the component is initially rendered, the component regenerates its render tree in response to events.</span></span> <span data-ttu-id="f9ba4-142">Blazor vergleicht die neue Renderingstruktur dann mit der vorherigen und wendet alle Änderungen auf das Browser-Dokumentobjektmodell (Document Object Model, DOM) an.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-142">Blazor then compares the new render tree against the previous one and applies any modifications to the browser's Document Object Model (DOM).</span></span> <span data-ttu-id="f9ba4-143">Weitere Informationen finden Sie in <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-143">Additional detail is provided in <xref:blazor/components/rendering>.</span></span>

<span data-ttu-id="f9ba4-144">Komponenten sind normale C#-Klassen und können an beliebiger Stelle innerhalb eines Projekts eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-144">Components are ordinary C# classes and can be placed anywhere within a project.</span></span> <span data-ttu-id="f9ba4-145">Komponenten, die Webseiten erzeugen, befinden sich in der Regel im Ordner `Pages`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-145">Components that produce webpages usually reside in the `Pages` folder.</span></span> <span data-ttu-id="f9ba4-146">Nicht für Seiten verwendete Komponenten werden häufig im Ordner `Shared` oder einem benutzerdefinierten Ordner gespeichert, der dem Projekt hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-146">Non-page components are frequently placed in the `Shared` folder or a custom folder added to the project.</span></span>

### <a name="namespaces"></a><span data-ttu-id="f9ba4-147">Namespaces</span><span class="sxs-lookup"><span data-stu-id="f9ba4-147">Namespaces</span></span>

<span data-ttu-id="f9ba4-148">In der Regel wird der Namespace einer Komponente aus dem Stammnamespace der App und dem Speicherort (Ordner) der Komponente in der App abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-148">Typically, a component's namespace is derived from the app's root namespace and the component's location (folder) within the app.</span></span> <span data-ttu-id="f9ba4-149">Wenn der Stammnamespace der App `BlazorSample` lautet und sich die `Counter` Komponente im Ordner `Pages` befindet, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-149">If the app's root namespace is `BlazorSample` and the `Counter` component resides in the `Pages` folder:</span></span>

* <span data-ttu-id="f9ba4-150">der Namespace der `Counter`-Komponente `BlazorSample.Pages`, und</span><span class="sxs-lookup"><span data-stu-id="f9ba4-150">The `Counter` component's namespace is `BlazorSample.Pages`.</span></span>
* <span data-ttu-id="f9ba4-151">der vollqualifizierten Typname der Komponente ist `BlazorSample.Pages.Counter`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-151">The fully qualified type name of the component is `BlazorSample.Pages.Counter`.</span></span>

<span data-ttu-id="f9ba4-152">Fügen Sie für benutzerdefinierte Ordner, die Komponenten enthalten, eine [`@using`][2]-Anweisung zur übergeordneten Komponente oder zur `_Imports.razor`-Datei der App hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-152">For custom folders that hold components, add a [`@using`][2] directive to the parent component or to the app's `_Imports.razor` file.</span></span> <span data-ttu-id="f9ba4-153">Das folgende Beispiel stellt Komponenten im Ordner `Components` zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-153">The following example makes components in the `Components` folder available:</span></span>

```razor
@using BlazorSample.Components
```

<span data-ttu-id="f9ba4-154">Auf Komponenten kann auch mit ihrem vollqualifizierten Namen verwiesen werden. Hierfür ist die Anweisung [`@using`][2] nicht erforderlich:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-154">Components can also be referenced using their fully qualified names, which doesn't require the [`@using`][2] directive:</span></span>

```razor
<BlazorSample.Components.MyComponent />
```

<span data-ttu-id="f9ba4-155">Der Namespace einer mit Razor erstellten Komponente basiert auf Folgendem (nach Priorität):</span><span class="sxs-lookup"><span data-stu-id="f9ba4-155">The namespace of a component authored with Razor is based on (in priority order):</span></span>

* <span data-ttu-id="f9ba4-156">[`@namespace`][8]-Bezeichnung im Markup (`@namespace BlazorSample.MyNamespace`) der Razor-Datei (`.razor`).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-156">[`@namespace`][8] designation in Razor file (`.razor`) markup (`@namespace BlazorSample.MyNamespace`).</span></span>
* <span data-ttu-id="f9ba4-157">Die `RootNamespace`-Eigenschaft des Projekts in der Projektdatei (`<RootNamespace>BlazorSample</RootNamespace>`)</span><span class="sxs-lookup"><span data-stu-id="f9ba4-157">The project's `RootNamespace` in the project file (`<RootNamespace>BlazorSample</RootNamespace>`).</span></span>
* <span data-ttu-id="f9ba4-158">Der Projektname, der aus dem Namen der Projektdatei (`.csproj`) und dem Pfad vom Projektstamm zur Komponente resultiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-158">The project name, taken from the project file's file name (`.csproj`), and the path from the project root to the component.</span></span> <span data-ttu-id="f9ba4-159">Ein Beispiel: Das Framework löst `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) in den Namespace `BlazorSample.Pages` auf.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-159">For example, the framework resolves `{PROJECT ROOT}/Pages/Index.razor` (`BlazorSample.csproj`) to the namespace `BlazorSample.Pages`.</span></span> <span data-ttu-id="f9ba4-160">Komponenten folgen den Namensbindungsregeln für C#.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-160">Components follow C# name binding rules.</span></span> <span data-ttu-id="f9ba4-161">Für die `Index`-Komponente in diesem Beispiel werden folgende Komponenten berücksichtigt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-161">For the `Index` component in this example, the components in scope are all of the components:</span></span>
  * <span data-ttu-id="f9ba4-162">Im gleichen Ordner: `Pages`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-162">In the same folder, `Pages`.</span></span>
  * <span data-ttu-id="f9ba4-163">Komponenten im Stammverzeichnis des Projekts, die nicht explizit einen anderen Namespace angeben</span><span class="sxs-lookup"><span data-stu-id="f9ba4-163">The components in the project's root that don't explicitly specify a different namespace.</span></span>

> [!NOTE]
> <span data-ttu-id="f9ba4-164">Die `global::`-Qualifizierung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-164">The `global::` qualification isn't supported.</span></span>
>
> <span data-ttu-id="f9ba4-165">Das Importieren von Komponenten mit [`using`](/dotnet/csharp/language-reference/keywords/using-statement)-Aliasanweisungen (z. B. `@using Foo = Bar`) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-165">Importing components with aliased [`using`](/dotnet/csharp/language-reference/keywords/using-statement) statements (for example, `@using Foo = Bar`) isn't supported.</span></span>
>
> <span data-ttu-id="f9ba4-166">Teilweise qualifizierte Namen werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-166">Partially qualified names aren't supported.</span></span> <span data-ttu-id="f9ba4-167">Das Hinzufügen von `@using BlazorSample` und das Verweisen auf die `NavMenu`-Komponente (`NavMenu.razor`) mit `<Shared.NavMenu></Shared.NavMenu>` werden beispielsweise nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-167">For example, adding `@using BlazorSample` and referencing the `NavMenu` component (`NavMenu.razor`) with `<Shared.NavMenu></Shared.NavMenu>` isn't supported.</span></span>

### <a name="partial-class-support"></a><span data-ttu-id="f9ba4-168">Unterstützung für partielle Klassen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-168">Partial class support</span></span>

<span data-ttu-id="f9ba4-169">Razor-Komponenten werden als partielle Klassen generiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-169">Razor components are generated as partial classes.</span></span> <span data-ttu-id="f9ba4-170">Razor-Komponenten werden mithilfe eines der folgenden Ansätze erstellt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-170">Razor components are authored using either of the following approaches:</span></span>

* <span data-ttu-id="f9ba4-171">C#-Code wird in einem [`@code`][1]-Block mit HTML-Markup und Razor-Code in einer einzelnen Datei definiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-171">C# code is defined in an [`@code`][1] block with HTML markup and Razor code in a single file.</span></span> <span data-ttu-id="f9ba4-172">Blazor-Vorlagen definieren Razor-Komponenten mithilfe dieses Ansatzes.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-172">Blazor templates define their Razor components using this approach.</span></span>
* <span data-ttu-id="f9ba4-173">C#-Code wird in einer CodeBehind-Datei gespeichert, die als partielle Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-173">C# code is placed in a code-behind file defined as a partial class.</span></span>

<span data-ttu-id="f9ba4-174">Das folgende Beispiel zeigt die `Counter`-Standardkomponente mit einem [`@code`][1]-Block in einer App, die aus einer Blazor-Vorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-174">The following example shows the default `Counter` component with an [`@code`][1] block in an app generated from a Blazor template.</span></span> <span data-ttu-id="f9ba4-175">HTML-Markup, Razor-Code und C#-Code befinden sich in derselben Datei:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-175">HTML markup, Razor code, and C# code are in the same file:</span></span>

<span data-ttu-id="f9ba4-176">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-176">`Pages/Counter.razor`:</span></span>

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

<span data-ttu-id="f9ba4-177">Die `Counter`-Komponente kann auch mit einer CodeBehind-Datei mit einer partiellen Klasse erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-177">The `Counter` component can also be created using a code-behind file with a partial class:</span></span>

<span data-ttu-id="f9ba4-178">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-178">`Pages/Counter.razor`:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

<span data-ttu-id="f9ba4-179">`Counter.razor.cs`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-179">`Counter.razor.cs`:</span></span>

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

<span data-ttu-id="f9ba4-180">Fügen Sie der partiellen Klassendatei alle erforderlichen Namespaces nach Bedarf hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-180">Add any required namespaces to the partial class file as needed.</span></span> <span data-ttu-id="f9ba4-181">Folgende typischen Namespaces werden von Razor-Komponenten verwendet:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-181">Typical namespaces used by Razor components include:</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

> [!IMPORTANT]
> <span data-ttu-id="f9ba4-182">[`@using`][2]-Direktive in der `_Imports.razor`-Datei werden nur auf Razor-Dateien (`.razor`) angewendet und nicht auf C#-Dateien (`.cs`).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-182">[`@using`][2] directives in the `_Imports.razor` file are only applied to Razor files (`.razor`), not C# files (`.cs`).</span></span>

### <a name="specify-a-base-class"></a><span data-ttu-id="f9ba4-183">Angeben einer Basisklasse</span><span class="sxs-lookup"><span data-stu-id="f9ba4-183">Specify a base class</span></span>

<span data-ttu-id="f9ba4-184">Die [`@inherits`][6]-Anweisung kann verwendet werden, um eine Basisklasse für eine Komponente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-184">The [`@inherits`][6] directive can be used to specify a base class for a component.</span></span> <span data-ttu-id="f9ba4-185">Das folgende Beispiel zeigt, wie eine Komponente eine Basisklasse (`BlazorRocksBase`) erben kann, um die Eigenschaften und Methoden der Komponente bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-185">The following example shows how a component can inherit a base class, `BlazorRocksBase`, to provide the component's properties and methods.</span></span> <span data-ttu-id="f9ba4-186">Die Basisklasse sollte von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-186">The base class should derive from <xref:Microsoft.AspNetCore.Components.ComponentBase>.</span></span>

<span data-ttu-id="f9ba4-187">`Pages/BlazorRocks.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-187">`Pages/BlazorRocks.razor`:</span></span>

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

<span data-ttu-id="f9ba4-188">`BlazorRocksBase.cs`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-188">`BlazorRocksBase.cs`:</span></span>

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

### <a name="use-components"></a><span data-ttu-id="f9ba4-189">Verwenden von Komponenten</span><span class="sxs-lookup"><span data-stu-id="f9ba4-189">Use components</span></span>

<span data-ttu-id="f9ba4-190">Komponenten können andere Komponenten enthalten, sofern Sie sie mithilfe der HTML-Elementsyntax deklarieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-190">Components can include other components by declaring them using HTML element syntax.</span></span> <span data-ttu-id="f9ba4-191">Das Markup für die Verwendung einer Komponente sieht wie ein HTML-Tag aus, wobei der Name des Tags der Typ der Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-191">The markup for using a component looks like an HTML tag where the name of the tag is the component type.</span></span>

<span data-ttu-id="f9ba4-192">Das folgende Markup in `Pages/Index.razor` rendert eine `HeadingComponent`-Instanz:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-192">The following markup in `Pages/Index.razor` renders a `HeadingComponent` instance:</span></span>

```razor
<HeadingComponent />
```

<span data-ttu-id="f9ba4-193">`Components/HeadingComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-193">`Components/HeadingComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/HeadingComponent.razor)]

<span data-ttu-id="f9ba4-194">Wenn eine Komponente ein HTML-Element mit einem groß geschriebenen ersten Buchstaben enthält, der nicht mit einem Komponentennamen identisch ist, wird eine Warnung ausgegeben, dass das Element einen unerwarteten Namen aufweist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-194">If a component contains an HTML element with an uppercase first letter that doesn't match a component name, a warning is emitted indicating that the element has an unexpected name.</span></span> <span data-ttu-id="f9ba4-195">Durch das Hinzufügen einer [`@using`][2]-Anweisung für den Namespace der Komponente wird die Komponente zur Verfügung gestellt, wodurch die Warnung nicht mehr auftritt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-195">Adding an [`@using`][2] directive for the component's namespace makes the component available, which resolves the warning.</span></span>

## <a name="parameters"></a><span data-ttu-id="f9ba4-196">Parameter</span><span class="sxs-lookup"><span data-stu-id="f9ba4-196">Parameters</span></span>

### <a name="route-parameters"></a><span data-ttu-id="f9ba4-197">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="f9ba4-197">Route parameters</span></span>

<span data-ttu-id="f9ba4-198">Komponenten können Routenparameter von der Routenvorlage empfangen, die in der [`@page`][9]-Anweisung bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-198">Components can receive route parameters from the route template provided in the [`@page`][9] directive.</span></span> <span data-ttu-id="f9ba4-199">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-199">The router uses route parameters to populate the corresponding component parameters.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f9ba4-200">Optionale Parameter werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-200">Optional parameters are supported.</span></span> <span data-ttu-id="f9ba4-201">Im folgenden Beispiel weist der optionale Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-201">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="f9ba4-202">Wenn das Segment nicht vorhanden ist, wird der Wert von `Text` auf `fantastic` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-202">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="f9ba4-203">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-203">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-5x.razor?highlight=1,6-7)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f9ba4-204">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-204">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](index/samples_snapshot/RouteParameter-3x.razor?highlight=2,7-8)]

<span data-ttu-id="f9ba4-205">Optionale Parameter werden nicht unterstützt. Deshalb werden im vorherigen Beispiel zwei [`@page`][9]-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-205">Optional parameters aren't supported, so two [`@page`][9] directives are applied in the preceding example.</span></span> <span data-ttu-id="f9ba4-206">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-206">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="f9ba4-207">Die zweite [`@page`][9]-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-207">The second [`@page`][9] directive receives the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

::: moniker-end

<span data-ttu-id="f9ba4-208">Informationen zu Routenparametern (`{*pageRoute}`), die Pfade über mehrere Ordnergrenzen hinweg erfassen, finden Sie unter <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-208">For information on catch-all route parameters (`{*pageRoute}`), which capture paths across multiple folder boundaries, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="component-parameters"></a><span data-ttu-id="f9ba4-209">Komponentenparameter</span><span class="sxs-lookup"><span data-stu-id="f9ba4-209">Component parameters</span></span>

<span data-ttu-id="f9ba4-210">Komponenten können *Komponentenparameter* enthalten, die mithilfe öffentlicher einfacher oder komplexer Eigenschaften für die Komponentenklasse mit dem [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attribut definiert werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-210">Components can have *component parameters*, which are defined using public simple or complex properties on the component class with the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span> <span data-ttu-id="f9ba4-211">Verwenden Sie Attribute, um Argumente für eine Komponente im Markup anzugeben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-211">Use attributes to specify arguments for a component in markup.</span></span>

<span data-ttu-id="f9ba4-212">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-212">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

<span data-ttu-id="f9ba4-213">Komponentenparametern kann ein Standardwert zugewiesen werden:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-213">Component parameters can be assigned a default value:</span></span>

```csharp
[Parameter]
public string Title { get; set; } = "Panel Title from Child";
```

<span data-ttu-id="f9ba4-214">Im folgenden Beispiel aus der Beispiel-App legt der `ParentComponent` den Wert der `Title`-Eigenschaft von `ChildComponent` fest.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-214">In the following example from the sample app, the `ParentComponent` sets the value of the `Title` property of the `ChildComponent`.</span></span>

<span data-ttu-id="f9ba4-215">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-215">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=5-6)]

<span data-ttu-id="f9ba4-216">Gemäß Konvention wird ein aus C#-Code bestehender Attributwert einem Parameter mithilfe des [reservierten `@`-Symbols von Razor zugewiesen](xref:mvc/views/razor#razor-syntax):</span><span class="sxs-lookup"><span data-stu-id="f9ba4-216">By convention, an attribute value that consists of C# code is assigned to a parameter using [Razor's reserved `@` symbol](xref:mvc/views/razor#razor-syntax):</span></span>

* <span data-ttu-id="f9ba4-217">Übergeordnetes Feld oder übergeordnete Eigenschaft: `Title="@{FIELD OR PROPERTY}`, wobei der Platzhalter `{FIELD OR PROPERTY}` ein C#-Feld oder eine C#-Eigenschaft der übergeordneten Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-217">Parent field or property: `Title="@{FIELD OR PROPERTY}`, where the placeholder `{FIELD OR PROPERTY}` is a C# field or property of the parent component.</span></span>
* <span data-ttu-id="f9ba4-218">Ergebnis einer Methode: `Title="@{METHOD}"`, wobei der Platzhalter `{METHOD}` eine C#-Methode der übergeordneten Komponente ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-218">Result of a method: `Title="@{METHOD}"`, where the placeholder `{METHOD}` is a C# method of the parent component.</span></span>
* <span data-ttu-id="f9ba4-219">[Impliziter oder expliziter Ausdruck](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, wobei der Platzhalter `{EXPRESSION}` ein C#-Ausdruck ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-219">[Implicit or explicit expression](xref:mvc/views/razor#implicit-razor-expressions): `Title="@({EXPRESSION})"`, where the placeholder `{EXPRESSION}` is a C# expression.</span></span>
  
<span data-ttu-id="f9ba4-220">Weitere Informationen finden Sie unter [Razor-Syntaxreferenz für ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-220">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="f9ba4-221">Erstellen Sie keine Komponenten, die in ihre eigenen *Komponentenparameter* schreiben, sondern verwenden Sie stattdessen ein privates Feld.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-221">Don't create components that write to their own *component parameters*, use a private field instead.</span></span> <span data-ttu-id="f9ba4-222">Weitere Informationen finden Sie im Abschnitt [Überschriebene Parameter](#overwritten-parameters).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-222">For more information, see the [Overwritten parameters](#overwritten-parameters) section.</span></span>

## <a name="child-content"></a><span data-ttu-id="f9ba4-223">Untergeordneter Inhalt</span><span class="sxs-lookup"><span data-stu-id="f9ba4-223">Child content</span></span>

<span data-ttu-id="f9ba4-224">Komponenten können den Inhalt anderer Komponenten festlegen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-224">Components can set the content of another component.</span></span> <span data-ttu-id="f9ba4-225">Die zuweisende Komponente stellt den Inhalt zwischen den Tags bereit, mit denen die empfangende Komponente angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-225">The assigning component provides the content between the tags that specify the receiving component.</span></span>

<span data-ttu-id="f9ba4-226">Im folgenden Beispiel enthält `ChildComponent` eine `ChildContent`-Eigenschaft, die einen <xref:Microsoft.AspNetCore.Components.RenderFragment>-Delegaten darstellt, der ein zu renderndes Segment der Benutzeroberfläche darstellt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-226">In the following example, the `ChildComponent` has a `ChildContent` property that represents a <xref:Microsoft.AspNetCore.Components.RenderFragment>, which represents a segment of UI to render.</span></span> <span data-ttu-id="f9ba4-227">Der Wert von `ChildContent` wird im Markup der Komponente positioniert, wo der Inhalt gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-227">The value of `ChildContent` is positioned in the component's markup where the content should be rendered.</span></span> <span data-ttu-id="f9ba4-228">Der Wert von `ChildContent` wird von der übergeordneten Komponente empfangen und innerhalb des `panel-body`-Elements des Bootstrappanels gerendert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-228">The value of `ChildContent` is received from the parent component and rendered inside the Bootstrap panel's `panel-body`.</span></span>

<span data-ttu-id="f9ba4-229">`Components/ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-229">`Components/ChildComponent.razor`:</span></span>

[!code-razor[](../common/samples/5.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> <span data-ttu-id="f9ba4-230">Die Eigenschaft, die den Inhalt von <xref:Microsoft.AspNetCore.Components.RenderFragment> empfängt, muss gemäß der Konvention `ChildContent` benannt werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-230">The property receiving the <xref:Microsoft.AspNetCore.Components.RenderFragment> content must be named `ChildContent` by convention.</span></span>

<span data-ttu-id="f9ba4-231">Die `ParentComponent`-Datei in der Beispiel-App kann Inhalte zum Rendern von `ChildComponent` bereitstellen, indem der Inhalt innerhalb der `<ChildComponent>`-Tags eingefügt wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-231">The `ParentComponent` in the sample app can provide content for rendering the `ChildComponent` by placing the content inside the `<ChildComponent>` tags.</span></span>

<span data-ttu-id="f9ba4-232">`Pages/ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-232">`Pages/ParentComponent.razor`:</span></span>

[!code-razor[](index/samples_snapshot/ParentComponent.razor?highlight=7-8)]

<span data-ttu-id="f9ba4-233">Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-233">Due to the way that Blazor renders child content, rendering components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the child component's content:</span></span>
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
> <span data-ttu-id="f9ba4-234">Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-234">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <ChildComponent Title="@c">
>         Child Content: Count: @c
>     </ChildComponent>
> }
> ```

<span data-ttu-id="f9ba4-235">Informationen zur Verwendung von <xref:Microsoft.AspNetCore.Components.RenderFragment> als Vorlage für die Benutzeroberfläche einer Razor-Komponente finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-235">For information on how a <xref:Microsoft.AspNetCore.Components.RenderFragment> can be used as a template for Razor component UI, see the following articles:</span></span>

* <xref:blazor/components/templated-components>
* <xref:blazor/webassembly-performance-best-practices#define-reusable-renderfragments-in-code>

## <a name="attribute-splatting-and-arbitrary-parameters"></a><span data-ttu-id="f9ba4-236">Attributsplatting und arbiträre Parameter</span><span class="sxs-lookup"><span data-stu-id="f9ba4-236">Attribute splatting and arbitrary parameters</span></span>

<span data-ttu-id="f9ba4-237">Komponenten können zusätzlich zu den deklarierten Parametern der Komponente weitere Attribute erfassen und rendern.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-237">Components can capture and render additional attributes in addition to the component's declared parameters.</span></span> <span data-ttu-id="f9ba4-238">Zusätzliche Attribute können in einem Wörterbuch erfasst und dann für ein Element *gesplattet* werden, wenn die Komponente mithilfe der [`@attributes`][3]-Anweisung Razor gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-238">Additional attributes can be captured in a dictionary and then *splatted* onto an element when the component is rendered using the [`@attributes`][3] Razor directive.</span></span> <span data-ttu-id="f9ba4-239">Dieses Option ist nützlich, wenn Sie eine Komponente definieren, die ein Markupelement erzeugt, das viele verschiedene Anpassungen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-239">This scenario is useful when defining a component that produces a markup element that supports a variety of customizations.</span></span> <span data-ttu-id="f9ba4-240">Beispielsweise kann es mühsam sein, Attribute für ein `<input>`-Element separat zu definieren, das viele Parameter unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-240">For example, it can be tedious to define attributes separately for an `<input>` that supports many parameters.</span></span>

<span data-ttu-id="f9ba4-241">Im folgenden Beispiel verwendet das erste `<input>`-Element (`id="useIndividualParams"`) einzelne Komponentenparameter, während das zweite `<input>`-Element (`id="useAttributesDict"`) Attributsplatting verwendet:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-241">In the following example, the first `<input>` element (`id="useIndividualParams"`) uses individual component parameters, while the second `<input>` element (`id="useAttributesDict"`) uses attribute splatting:</span></span>

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

<span data-ttu-id="f9ba4-242">Der Typ des Parameters muss `IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` mit Zeichenfolgenschlüsseln implementieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-242">The type of the parameter must implement `IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` with string keys.</span></span>

<span data-ttu-id="f9ba4-243">Die gerenderten `<input>`-Elemente sind mit beiden Ansätzen identisch:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-243">The rendered `<input>` elements using both approaches is identical:</span></span>

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

<span data-ttu-id="f9ba4-244">Definieren Sie einen Komponentenparameter mithilfe des [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute)-Attributs, bei dem die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft auf `true` festgelegt ist, damit beliebige Attribute akzeptiert werden:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-244">To accept arbitrary attributes, define a component parameter using the [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute with the <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property set to `true`:</span></span>

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

<span data-ttu-id="f9ba4-245">Die <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>-Eigenschaft für [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) ermöglicht dem Parameter den Abgleich aller Attribute, die keinem anderen Parameter entsprechen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-245">The <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> property on [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) allows the parameter to match all attributes that don't match any other parameter.</span></span> <span data-ttu-id="f9ba4-246">Eine Komponente kann nur einen einzelnen Parameter mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> definieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-246">A component can only define a single parameter with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues>.</span></span> <span data-ttu-id="f9ba4-247">Der mit <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> verwendete Eigenschaftentyp muss von `Dictionary<string, object>` mit Zeichenfolgenschlüsseln zugewiesen werden können.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-247">The property type used with <xref:Microsoft.AspNetCore.Components.ParameterAttribute.CaptureUnmatchedValues> must be assignable from `Dictionary<string, object>` with string keys.</span></span> <span data-ttu-id="f9ba4-248">`IEnumerable<KeyValuePair<string, object>>` oder `IReadOnlyDictionary<string, object>` sind in diesem Szenario ebenfalls mögliche Optionen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-248">`IEnumerable<KeyValuePair<string, object>>` or `IReadOnlyDictionary<string, object>` are also options in this scenario.</span></span>

<span data-ttu-id="f9ba4-249">Die Position von [`@attributes`][3] relativ zur Position der Elementattribute ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-249">The position of [`@attributes`][3] relative to the position of element attributes is important.</span></span> <span data-ttu-id="f9ba4-250">Wenn [`@attributes`][3]-Anweisungen für das Element gesplattet werden, werden die Attribute von rechts nach links (letztes bis erstes) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-250">When [`@attributes`][3] are splatted on the element, the attributes are processed from right to left (last to first).</span></span> <span data-ttu-id="f9ba4-251">Sehen Sie sich das folgende Beispiel für eine Komponente an, die eine `Child`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-251">Consider the following example of a component that consumes a `Child` component:</span></span>

<span data-ttu-id="f9ba4-252">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-252">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f9ba4-253">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-253">`ChildComponent.razor`:</span></span>

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f9ba4-254">Das `extra`-Attribut der `Child`-Komponente ist rechts von [`@attributes`][3] festgelegt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-254">The `Child` component's `extra` attribute is set to the right of [`@attributes`][3].</span></span> <span data-ttu-id="f9ba4-255">Das gerenderte `<div>`-Element der `Parent`-Komponente enthält `extra="5"`, wenn dieses über das zusätzliche Attribut weitergeleitet wird, da die Attribute von rechts nach links (letztes bis erstes) verarbeitet werden:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-255">The `Parent` component's rendered `<div>` contains `extra="5"` when passed through the additional attribute because the attributes are processed right to left (last to first):</span></span>

```html
<div extra="5" />
```

<span data-ttu-id="f9ba4-256">Im folgenden Beispiel wird die Reihenfolge von `extra` und [`@attributes`][3] in `<div>` der `Child`-Komponente umgekehrt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-256">In the following example, the order of `extra` and [`@attributes`][3] is reversed in the `Child` component's `<div>`:</span></span>

<span data-ttu-id="f9ba4-257">`ParentComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-257">`ParentComponent.razor`:</span></span>

```razor
<ChildComponent extra="10" />
```

<span data-ttu-id="f9ba4-258">`ChildComponent.razor`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-258">`ChildComponent.razor`:</span></span>

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

<span data-ttu-id="f9ba4-259">Das gerenderte `<div>`-Element in der `Parent`-Komponente enthält `extra="10"`, wenn es über das zusätzliche Attribut weitergeleitet wird:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-259">The rendered `<div>` in the `Parent` component contains `extra="10"` when passed through the additional attribute:</span></span>

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a><span data-ttu-id="f9ba4-260">Erfassen von Verweisen auf Komponenten</span><span class="sxs-lookup"><span data-stu-id="f9ba4-260">Capture references to components</span></span>

<span data-ttu-id="f9ba4-261">Komponentenverweise bieten eine Möglichkeit, auf eine Komponenteninstanz zu verweisen, damit Sie Befehle wie `Show` oder `Reset` für diese Instanz ausgeben können.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-261">Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.</span></span> <span data-ttu-id="f9ba4-262">So erfassen Sie einen Komponentenverweis:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-262">To capture a component reference:</span></span>

* <span data-ttu-id="f9ba4-263">Fügen Sie der untergeordneten Komponente ein [`@ref`][4]-Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-263">Add an [`@ref`][4] attribute to the child component.</span></span>
* <span data-ttu-id="f9ba4-264">Definieren Sie ein Feld mit demselben Typ wie die untergeordnete Komponente.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-264">Define a field with the same type as the child component.</span></span>

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

<span data-ttu-id="f9ba4-265">Wenn die Komponente gerendert wird, wird das `loginDialog`-Feld mit der untergeordneten `CustomLoginDialog`-Komponenteninstanz aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-265">When the component is rendered, the `loginDialog` field is populated with the `CustomLoginDialog` child component instance.</span></span> <span data-ttu-id="f9ba4-266">Anschließend können Sie .NET-Methoden für die Komponenteninstanz aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-266">You can then invoke .NET methods on the component instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f9ba4-267">Die `loginDialog`-Variable wird erst aufgefüllt, nachdem die Komponente gerendert wurde und die Ausgabe das `MyLoginDialog`-Element enthält.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-267">The `loginDialog` variable is only populated after the component is rendered and its output includes the `MyLoginDialog` element.</span></span> <span data-ttu-id="f9ba4-268">Vor dem Rendern der Komponente gibt es nichts, auf das verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-268">Until the component is rendered, there's nothing to reference.</span></span>
>
> <span data-ttu-id="f9ba4-269">Sie können Komponentenverweise bearbeiten, nachdem die Komponente das Rendering abgeschlossen hat, indem Sie die [`OnAfterRenderAsync`- oder die `OnAfterRender`-Methode](xref:blazor/components/lifecycle#after-component-render) verwenden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-269">To manipulate components references after the component has finished rendering, use the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span>
>
> <span data-ttu-id="f9ba4-270">Um eine Verweisvariable mit einem Ereignishandler zu verwenden, verwenden Sie einen Lambdaausdruck oder weisen einen Ereignishandlerdelegaten in den [Methoden `OnAfterRenderAsync` oder `OnAfterRender`](xref:blazor/components/lifecycle#after-component-render) zu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-270">To use a reference variable with an event handler, use a lambda expression or assign the event handler delegate in the [`OnAfterRenderAsync` or `OnAfterRender` methods](xref:blazor/components/lifecycle#after-component-render).</span></span> <span data-ttu-id="f9ba4-271">So ist sichergestellt, dass die Verweisvariable vor dem Ereignishandler zugewiesen wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-271">This ensures that the reference variable is assigned before the event handler is assigned.</span></span>
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

<span data-ttu-id="f9ba4-272">Informationen zum Verweisen auf Komponenten in einer Schleife finden Sie unter [Erfassen von Verweisen auf mehrere ähnliche untergeordnete Komponenten (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-272">To reference components in a loop, see [Capture references to multiple similar child-components (dotnet/aspnetcore #13358)](https://github.com/dotnet/aspnetcore/issues/13358).</span></span>

<span data-ttu-id="f9ba4-273">Beim Erfassen von Komponentenverweisen wird zwar eine ähnliche Syntax verwendet, um [Elementverweise zu erfassen](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), es handelt sich jedoch nicht um ein JavaScript-Interop-Feature.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-273">While capturing component references use a similar syntax to [capturing element references](xref:blazor/call-javascript-from-dotnet#capture-references-to-elements), it isn't a JavaScript interop feature.</span></span> <span data-ttu-id="f9ba4-274">Komponentenverweise werden nicht an JavaScript-Code übermittelt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-274">Component references aren't passed to JavaScript code.</span></span> <span data-ttu-id="f9ba4-275">Komponentenverweise werden nur in .NET-Code verwendet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-275">Component references are only used in .NET code.</span></span>

> [!NOTE]
> <span data-ttu-id="f9ba4-276">Verwenden Sie **keine** Komponentenverweise verwenden, um den Zustand von untergeordneten Komponenten zu verändern.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-276">Do **not** use component references to mutate the state of child components.</span></span> <span data-ttu-id="f9ba4-277">Verwenden Sie stattdessen normale deklarative Parameter, um Daten an untergeordnete Komponenten zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-277">Instead, use normal declarative parameters to pass data to child components.</span></span> <span data-ttu-id="f9ba4-278">Die Verwendung normaler deklarativer Parameter führt dazu, dass untergeordnete Komponenten automatisch zu den richtigen Zeitpunkten gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-278">Use of normal declarative parameters result in child components that rerender at the correct times automatically.</span></span>

## <a name="synchronization-context"></a><span data-ttu-id="f9ba4-279">Synchronisierungskontext</span><span class="sxs-lookup"><span data-stu-id="f9ba4-279">Synchronization context</span></span>

<span data-ttu-id="f9ba4-280">Blazor verwendet einen Synchronisierungskontext (<xref:System.Threading.SynchronizationContext>), um einen einzelnen logischen Ausführungsthread zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-280">Blazor uses a synchronization context (<xref:System.Threading.SynchronizationContext>) to enforce a single logical thread of execution.</span></span> <span data-ttu-id="f9ba4-281">Die [Lebenszyklusmethoden](xref:blazor/components/lifecycle) einer Komponente und alle Ereignisrückrufe, die von Blazor ausgelöst werden, werden in diesem Synchronisierungskontext ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-281">A component's [lifecycle methods](xref:blazor/components/lifecycle) and any event callbacks that are raised by Blazor are executed on the synchronization context.</span></span>

<span data-ttu-id="f9ba4-282">Der Synchronisierungskontext des Blazor Server versucht, eine Singlethreadumgebung zu emulieren, sodass er genau mit dem WebAssembly-Modell im Browser übereinstimmt, das ein Singlethreadmodell ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-282">Blazor Server's synchronization context attempts to emulate a single-threaded environment so that it closely matches the WebAssembly model in the browser, which is single threaded.</span></span> <span data-ttu-id="f9ba4-283">Zu jedem Zeitpunkt wird die Arbeit für genau einen Thread ausgeführt, woraus der Eindruck eines einzelnen logischen Threads entsteht.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-283">At any given point in time, work is performed on exactly one thread, giving the impression of a single logical thread.</span></span> <span data-ttu-id="f9ba4-284">Zwei Vorgänge werden nicht gleichzeitig ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-284">No two operations execute concurrently.</span></span>

### <a name="avoid-thread-blocking-calls"></a><span data-ttu-id="f9ba4-285">Vermeiden Sie eine Threadblockierung von Aufrufen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-285">Avoid thread-blocking calls</span></span>

<span data-ttu-id="f9ba4-286">Rufen Sie generell folgende Methoden nicht auf.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-286">Generally, don't call the following methods.</span></span> <span data-ttu-id="f9ba4-287">Die folgenden Methoden blockieren den Thread und hindern somit die App an der Wiederaufnahme der Arbeit, bis der zugrunde liegende <xref:System.Threading.Tasks.Task> beendet ist:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-287">The following methods block the thread and thus block the app from resuming work until the underlying <xref:System.Threading.Tasks.Task> is complete:</span></span>

* <xref:System.Threading.Tasks.Task%601.Result%2A>
* <xref:System.Threading.Tasks.Task.Wait%2A>
* <xref:System.Threading.Tasks.Task.WaitAny%2A>
* <xref:System.Threading.Tasks.Task.WaitAll%2A>
* <xref:System.Threading.Thread.Sleep%2A>
* <xref:System.Runtime.CompilerServices.TaskAwaiter.GetResult%2A>

### <a name="invoke-component-methods-externally-to-update-state"></a><span data-ttu-id="f9ba4-288">Externes Aufrufen von Komponentenmethoden zur Aktualisierung des Status</span><span class="sxs-lookup"><span data-stu-id="f9ba4-288">Invoke component methods externally to update state</span></span>

<span data-ttu-id="f9ba4-289">Wenn eine Komponente aufgrund eines externen Ereignisses (z. B. eines Timers oder anderer Benachrichtigungen) aktualisiert werden muss, verwenden Sie die `InvokeAsync`-Methode, die an den Synchronisierungskontext von Blazor weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-289">In the event a component must be updated based on an external event, such as a timer or other notifications, use the `InvokeAsync` method, which dispatches to Blazor's synchronization context.</span></span> <span data-ttu-id="f9ba4-290">Nehmen Sie einen *Benachrichtigungsdienst* als Beispiel, der jede überwachende Komponente des aktualisierten Zustands benachrichtigen kann:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-290">For example, consider a *notifier service* that can notify any listening component of the updated state:</span></span>

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

<span data-ttu-id="f9ba4-291">Registrieren des `NotifierService`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-291">Register the `NotifierService`:</span></span>

* <span data-ttu-id="f9ba4-292">Registrieren Sie in Blazor WebAssembly den Dienst als Singleton in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-292">In Blazor WebAssembly, register the service as singleton in `Program.Main`:</span></span>

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* <span data-ttu-id="f9ba4-293">Registrieren Sie in Blazor Server den Dienst bereichsbezogen in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-293">In Blazor Server, register the service as scoped in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.AddScoped<NotifierService>();
  ```

<span data-ttu-id="f9ba4-294">Verwenden Sie `NotifierService`, um eine Komponente zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-294">Use the `NotifierService` to update a component:</span></span>

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

<span data-ttu-id="f9ba4-295">Im vorherigen Beispiel ruft `NotifierService` die `OnNotify`-Methode der Komponente außerhalb des Synchronisierungskontexts von Blazor auf.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-295">In the preceding example, `NotifierService` invokes the component's `OnNotify` method outside of Blazor's synchronization context.</span></span> <span data-ttu-id="f9ba4-296">`InvokeAsync` wird verwendet, um zum richtigen Kontext zu wechseln und ein Rendering in die Warteschlange zu stellen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-296">`InvokeAsync` is used to switch to the correct context and queue a render.</span></span> <span data-ttu-id="f9ba4-297">Weitere Informationen finden Sie unter <xref:blazor/components/rendering>.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-297">For more information, see <xref:blazor/components/rendering>.</span></span>

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a><span data-ttu-id="f9ba4-298">Verwenden von \@key zur Steuerung der Beibehaltung von Elementen und Komponenten</span><span class="sxs-lookup"><span data-stu-id="f9ba4-298">Use \@key to control the preservation of elements and components</span></span>

<span data-ttu-id="f9ba4-299">Wenn Sie eine Element- oder Komponentenliste rendern und die Elemente oder Komponenten nachfolgend geändert werden, muss der Vergleichsalgorithmus von Blazor bestimmen, welche der vorherigen Elemente oder Komponenten beibehalten werden können und wie Modellobjekte diesen zugeordnet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-299">When rendering a list of elements or components and the elements or components subsequently change, Blazor's diffing algorithm must decide which of the previous elements or components can be retained and how model objects should map to them.</span></span> <span data-ttu-id="f9ba4-300">Normalerweise erfolgt dieser Prozess automatisch und kann ignoriert werden, aber in einigen Fällen sollten Sie den Prozess steuern.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-300">Normally, this process is automatic and can be ignored, but there are cases where you may want to control the process.</span></span>

<span data-ttu-id="f9ba4-301">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-301">Consider the following example:</span></span>

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

<span data-ttu-id="f9ba4-302">Der Inhalt der `People`-Sammlung kann sich durch eingefügte, gelöschte oder neu sortierte Einträgen ändern.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-302">The contents of the `People` collection may change with inserted, deleted, or re-ordered entries.</span></span> <span data-ttu-id="f9ba4-303">Wenn die Komponente wiederholt gerendert wird, kann sich die `<DetailsEditor>`-Komponente ändern, damit sie andere `Details`-Parameterwerte empfangen kann.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-303">When the component rerenders, the `<DetailsEditor>` component may change to receive different `Details` parameter values.</span></span> <span data-ttu-id="f9ba4-304">Dadurch kann es zu einem unerwartet komplexen erneuten Rendering kommen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-304">This may cause more complex rerendering than expected.</span></span> <span data-ttu-id="f9ba4-305">In einigen Fällen kann das erneute Rendering zu erkennbaren Verhaltensabweichungen führen, z. B. zu einem verlorenen Elementfokus.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-305">In some cases, rerendering can lead to visible behavior differences, such as lost element focus.</span></span>

<span data-ttu-id="f9ba4-306">Der Zuordnungsprozess kann mit dem [`@key`][5]-Anweisungsattribut gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-306">The mapping process can be controlled with the [`@key`][5] directive attribute.</span></span> <span data-ttu-id="f9ba4-307">[`@key`][5] bewirkt, dass der Vergleichsalgorithmus die Beibehaltung von Elementen oder Komponenten basierend auf dem Wert des Schlüssels garantiert:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-307">[`@key`][5] causes the diffing algorithm to guarantee preservation of elements or components based on the key's value:</span></span>

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

<span data-ttu-id="f9ba4-308">Wenn sich die `People`-Sammlung ändert, behält der Vergleichsalgorithmus die Zuordnung zwischen `<DetailsEditor>`-Instanzen und `person`-Instanzen bei:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-308">When the `People` collection changes, the diffing algorithm retains the association between `<DetailsEditor>` instances and `person` instances:</span></span>

* <span data-ttu-id="f9ba4-309">Wenn ein `Person`-Element aus der `People`-Liste gelöscht wird, wird nur die entsprechende `<DetailsEditor>`-Instanz von der Benutzeroberfläche entfernt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-309">If a `Person` is deleted from the `People` list, only the corresponding `<DetailsEditor>` instance is removed from the UI.</span></span> <span data-ttu-id="f9ba4-310">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-310">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f9ba4-311">Wenn ein `Person` an einer bestimmten Position in der Liste eingefügt wird, wird eine neue `<DetailsEditor>`-Instanz an der entsprechenden Position eingefügt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-311">If a `Person` is inserted at some position in the list, one new `<DetailsEditor>` instance is inserted at that corresponding position.</span></span> <span data-ttu-id="f9ba4-312">Andere Instanzen bleiben unverändert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-312">Other instances are left unchanged.</span></span>
* <span data-ttu-id="f9ba4-313">Wenn `Person`-Einträge neu sortiert werden, werden die entsprechenden `<DetailsEditor>`-Instanzen beibehalten und auf der Benutzeroberfläche neu angeordnet.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-313">If `Person` entries are re-ordered, the corresponding `<DetailsEditor>` instances are preserved and re-ordered in the UI.</span></span>

<span data-ttu-id="f9ba4-314">In einigen Szenarios minimiert die Verwendung von [`@key`][5] die Komplexität des erneuten Renderings und vermeidet potenzielle Probleme mit zustandsbehafteten Teilen der DOM-Änderung, z. B. der Fokusposition.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-314">In some scenarios, use of [`@key`][5] minimizes the complexity of rerendering and avoids potential issues with stateful parts of the DOM changing, such as focus position.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f9ba4-315">Schlüssel sind für jedes Containerelement oder jede Komponente lokal.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-315">Keys are local to each container element or component.</span></span> <span data-ttu-id="f9ba4-316">Schlüssel werden nicht dokumentübergreifend global verglichen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-316">Keys aren't compared globally across the document.</span></span>

### <a name="when-to-use-key"></a><span data-ttu-id="f9ba4-317">Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="f9ba4-317">When to use \@key</span></span>

<span data-ttu-id="f9ba4-318">In der Regel ist es sinnvoll, [`@key`][5] zu verwenden, wenn eine Liste gerendert wird (z. B. in einem [foreach](/dotnet/csharp/language-reference/keywords/foreach-in)-Block) und ein geeigneter Wert vorhanden ist, um den [`@key`][5] zu definieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-318">Typically, it makes sense to use [`@key`][5] whenever a list is rendered (for example, in a [foreach](/dotnet/csharp/language-reference/keywords/foreach-in) block) and a suitable value exists to define the [`@key`][5].</span></span>

<span data-ttu-id="f9ba4-319">Sie können [`@key`][5] auch verwenden, um zu verhindern, dass Blazor eine Element- oder Komponententeilstruktur beibehält, wenn sich ein Objekt ändert:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-319">You can also use [`@key`][5] to prevent Blazor from preserving an element or component subtree when an object changes:</span></span>

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

<span data-ttu-id="f9ba4-320">Wenn `@currentPerson` geändert wird, zwingt die [`@key`][5]-Attributanweisung Blazor, das gesamte `<div>`-Element und dessen Nachfolger zu verwerfen und die Teilstruktur auf der Benutzeroberfläche mit neuen Elementen und Komponenten noch mal zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-320">If `@currentPerson` changes, the [`@key`][5] attribute directive forces Blazor to discard the entire `<div>` and its descendants and rebuild the subtree within the UI with new elements and components.</span></span> <span data-ttu-id="f9ba4-321">Das kann hilfreich sein, wenn Sie sicherstellen müssen, dass kein Benutzeroberflächenzustand beibehalten wird, wenn `@currentPerson` geändert wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-321">This can be useful if you need to guarantee that no UI state is preserved when `@currentPerson` changes.</span></span>

### <a name="when-not-to-use-key"></a><span data-ttu-id="f9ba4-322">Ungeeignete Anwendungsfälle für \@key</span><span class="sxs-lookup"><span data-stu-id="f9ba4-322">When not to use \@key</span></span>

<span data-ttu-id="f9ba4-323">Bei einem Vergleich mit [`@key`][5] wird die Leistung beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-323">There's a performance cost when diffing with [`@key`][5].</span></span> <span data-ttu-id="f9ba4-324">Die Beeinträchtigung der Leistung ist nicht erheblich. Sie sollten [`@key`][5] jedoch nur angeben, wenn sich die Beibehaltungsregeln für das Element oder die Komponente positiv auf die App auswirken.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-324">The performance cost isn't large, but only specify [`@key`][5] if controlling the element or component preservation rules benefit the app.</span></span>

<span data-ttu-id="f9ba4-325">Auch wenn [`@key`][5] nicht verwendet wird, behält Blazor die untergeordneten Element- und Komponenteninstanzen so weit wie möglich bei.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-325">Even if [`@key`][5] isn't used, Blazor preserves child element and component instances as much as possible.</span></span> <span data-ttu-id="f9ba4-326">Der einzige Vorteil bei der Verwendung von [`@key`][5] besteht in der Kontrolle darüber, *wie* Modellinstanzen den beibehaltenen Komponenteninstanzen zugeordnet werden, anstatt die Zuordnung durch den Vergleichsalgorithmus bestimmen zu lassen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-326">The only advantage to using [`@key`][5] is control over *how* model instances are mapped to the preserved component instances, instead of the diffing algorithm selecting the mapping.</span></span>

### <a name="what-values-to-use-for-key"></a><span data-ttu-id="f9ba4-327">Zu verwendende Werte für \@key</span><span class="sxs-lookup"><span data-stu-id="f9ba4-327">What values to use for \@key</span></span>

<span data-ttu-id="f9ba4-328">Im Allgemeinen ist es sinnvoll, Werte der folgenden Kategorien für [`@key`][5] bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-328">Generally, it makes sense to supply one of the following kinds of value for [`@key`][5]:</span></span>

* <span data-ttu-id="f9ba4-329">Modellobjektinstanzen (z. B. eine `Person`-Instanz wie im vorherigen Beispiel):</span><span class="sxs-lookup"><span data-stu-id="f9ba4-329">Model object instances (for example, a `Person` instance as in the earlier example).</span></span> <span data-ttu-id="f9ba4-330">Dadurch wird die Beibehaltung basierend auf der Objektverweisgleichheit sichergestellt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-330">This ensures preservation based on object reference equality.</span></span>
* <span data-ttu-id="f9ba4-331">Eindeutige Bezeichner (z. B. Primärschlüsselwerte vom Typ `int`, `string`oder `Guid`):</span><span class="sxs-lookup"><span data-stu-id="f9ba4-331">Unique identifiers (for example, primary key values of type `int`, `string`, or `Guid`).</span></span>

<span data-ttu-id="f9ba4-332">Stellen Sie sicher, dass die für [`@key`][5] verwendeten Werte nicht kollidieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-332">Ensure that values used for [`@key`][5] don't clash.</span></span> <span data-ttu-id="f9ba4-333">Wenn innerhalb desselben übergeordneten Elements kollidierende Werte erkannt werden, löst Blazor eine Ausnahme aus, da alte Elemente oder Komponenten nicht deterministisch neuen Elementen oder Komponenten zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-333">If clashing values are detected within the same parent element, Blazor throws an exception because it can't deterministically map old elements or components to new elements or components.</span></span> <span data-ttu-id="f9ba4-334">Verwenden Sie nur eindeutige Werte wie Objektinstanzen oder Primärschlüsselwerte.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-334">Only use distinct values, such as object instances or primary key values.</span></span>

## <a name="overwritten-parameters"></a><span data-ttu-id="f9ba4-335">Überschriebene Parameter</span><span class="sxs-lookup"><span data-stu-id="f9ba4-335">Overwritten parameters</span></span>

<span data-ttu-id="f9ba4-336">Das Blazor-Framework erzwingt im Allgemeinen eine sichere Parameterzuordnung von über- und untergeordneten Elementen:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-336">The Blazor framework generally imposes safe parent-to-child parameter assignment:</span></span>

* <span data-ttu-id="f9ba4-337">Parameter werden nicht unerwartet überschrieben.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-337">Parameters aren't overwritten unexpectedly.</span></span>
* <span data-ttu-id="f9ba4-338">Nebeneffekte werden minimiert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-338">Side-effects are minimized.</span></span> <span data-ttu-id="f9ba4-339">Beispielsweise werden zusätzliche Rendervorgänge vermieden, da Sie zu unendlichen Renderingschleifen führen können.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-339">For example, additional renders are avoided because they may create infinite rendering loops.</span></span>

<span data-ttu-id="f9ba4-340">Eine untergeordnete Komponente empfängt neue Parameterwerte, die möglicherweise vorhandene Werte überschreiben, wenn die übergeordnete Komponente erneut gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-340">A child component receives new parameter values that possibly overwrite existing values when the parent component rerenders.</span></span> <span data-ttu-id="f9ba4-341">Das versehentliches Überschreiben von Parameterwerten in einer untergeordneten Komponente tritt häufig auf, wenn die Komponente mit mindestens einem datengebundenen Parameter entwickelt wird und der Entwickler direkt in einen Parameter im untergeordneten Element schreibt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-341">Accidentially overwriting parameter values in a child component often occurs when developing the component with one or more data-bound parameters and the developer writes directly to a parameter in the child:</span></span>

* <span data-ttu-id="f9ba4-342">Die untergeordnete Komponente wird mit mindestens einem Parameterwert aus der übergeordneten Komponente gerendert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-342">The child component is rendered with one or more parameter values from the parent component.</span></span>
* <span data-ttu-id="f9ba4-343">Das untergeordnete Element schreibt direkt in den Wert eines Parameters.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-343">The child writes directly to the value of a parameter.</span></span>
* <span data-ttu-id="f9ba4-344">Die übergeordnete Komponente wird erneut gerendert und überschreibt den Wert des Parameters des untergeordneten Elements.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-344">The parent component rerenders and overwrites the value of the child's parameter.</span></span>

<span data-ttu-id="f9ba4-345">Das Potenzial, Parameterwerte zu überschreiben, gilt auch für Eigenschaftensetter der untergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-345">The potential for overwriting paramater values extends into the child component's property setters, too.</span></span>

<span data-ttu-id="f9ba4-346">**Unsere allgemeine Richtlinie besagt, dass keine Komponenten erstellt werden sollen, die direkt in ihre eigenen Parameter schreiben.**</span><span class="sxs-lookup"><span data-stu-id="f9ba4-346">**Our general guidance is not to create components that directly write to their own parameters.**</span></span>

<span data-ttu-id="f9ba4-347">Angenommen, die folgende fehlerhafte `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-347">Consider the following faulty `Expander` component that:</span></span>

* <span data-ttu-id="f9ba4-348">Rendert untergeordneten Inhalt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-348">Renders child content.</span></span>
* <span data-ttu-id="f9ba4-349">Schaltet die Anzeige von untergeordnetem Inhalt mit einem Komponentenparameter (`Expanded`) um.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-349">Toggles showing child content with a component parameter (`Expanded`).</span></span>
* <span data-ttu-id="f9ba4-350">Die Komponente schreibt direkt in den `Expanded`-Parameter, der das Problem mit überschriebenen Parametern veranschaulicht, das vermieden werden sollte.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-350">The component writes directly to the `Expanded` parameter, which demonstrates the problem with overwritten parameters and should be avoided.</span></span>

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

<span data-ttu-id="f9ba4-351">Die `Expander`-Komponente wird einer übergeordneten Komponente hinzugefügt, die <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> aufrufen kann:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-351">The `Expander` component is added to a parent component that may call <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A>:</span></span>

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

<span data-ttu-id="f9ba4-352">Anfänglich verhalten sich die `Expander`-Komponenten unabhängig, wenn ihre `Expanded`-Eigenschaften umgeschaltet werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-352">Initially, the `Expander` components behave independently when their `Expanded` properties are toggled.</span></span> <span data-ttu-id="f9ba4-353">Die untergeordneten Komponenten behalten ihre Zustände erwartungsgemäß bei.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-353">The child components maintain their states as expected.</span></span> <span data-ttu-id="f9ba4-354">Wenn <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> in der übergeordneten Komponente aufgerufen wird, wird der `Expanded`-Parameter der ersten untergeordneten Komponente wieder zurück auf seinen ursprünglichen Wert gesetzt (`true`).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-354">When <xref:Microsoft.AspNetCore.Components.ComponentBase.StateHasChanged%2A> is called in the parent, the `Expanded` parameter of the first child component is reset back to its initial value (`true`).</span></span> <span data-ttu-id="f9ba4-355">Der `Expanded`-Wert der zweiten `Expander`-Komponente wird nicht zurückgesetzt, weil in der zweiten Komponente kein untergeordneter Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-355">The second `Expander` component's `Expanded` value isn't reset because no child content is rendered in the second component.</span></span>

<span data-ttu-id="f9ba4-356">Um den Zustand im vorangehenden Szenario beizubehalten, verwenden Sie ein *privates Feld* in der `Expander`-Komponente, um dessen Umschaltungszustand beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-356">To maintain state in the preceding scenario, use a *private field* in the `Expander` component to maintain its toggled state.</span></span>

<span data-ttu-id="f9ba4-357">Die folgende überarbeitete `Expander`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-357">The following revised `Expander` component:</span></span>

* <span data-ttu-id="f9ba4-358">akzeptiert den Wert des `Expanded`-Komponentenparameters aus der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-358">Accepts the `Expanded` component parameter value from the parent.</span></span>
* <span data-ttu-id="f9ba4-359">weist den Wert des Komponentenparameters einem *privaten Feld* (`expanded`) im [OnInitialized-Ereignis](xref:blazor/components/lifecycle#component-initialization-methods) zu.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-359">Assigns the component parameter value to a *private field* (`expanded`) in the [OnInitialized event](xref:blazor/components/lifecycle#component-initialization-methods).</span></span>
* <span data-ttu-id="f9ba4-360">Verwendet das private Feld, um den internen Umschaltzustand zu verwalten. Dies veranschaulicht, wie das direkte Schreiben in einen Parameter vermieden wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-360">Uses the private field to maintain its internal toggle state, which demonstrates how to avoid writing directly to a parameter.</span></span>

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

<span data-ttu-id="f9ba4-361">Weitere Informationen finden Sie unter [Bidirektionaler Blazor-Bindungsfehler (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-361">For additional information, see [Blazor Two Way Binding Error (dotnet/aspnetcore #24599)](https://github.com/dotnet/aspnetcore/issues/24599).</span></span> 

## <a name="apply-an-attribute"></a><span data-ttu-id="f9ba4-362">Hinzufügen eines Attributs</span><span class="sxs-lookup"><span data-stu-id="f9ba4-362">Apply an attribute</span></span>

<span data-ttu-id="f9ba4-363">Attribute können in Razor-Komponenten mit der [`@attribute`][7]-Anweisung hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-363">Attributes can be applied to Razor components with the [`@attribute`][7] directive.</span></span> <span data-ttu-id="f9ba4-364">Im folgenden Beispiel wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Komponentenklasse angewendet:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-364">The following example applies the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the component class:</span></span>

```razor
@page "/"
@attribute [Authorize]
```

## <a name="conditional-html-element-attributes"></a><span data-ttu-id="f9ba4-365">Attribute für bedingte HTML-Elemente</span><span class="sxs-lookup"><span data-stu-id="f9ba4-365">Conditional HTML element attributes</span></span>

<span data-ttu-id="f9ba4-366">HTML-Elementattribute werden basierend auf dem .NET-Wert bedingt gerendert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-366">HTML element attributes are conditionally rendered based on the .NET value.</span></span> <span data-ttu-id="f9ba4-367">Wenn der Wert `false` oder `null` ist, wird das Attribut nicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-367">If the value is `false` or `null`, the attribute isn't rendered.</span></span> <span data-ttu-id="f9ba4-368">Wenn der Wert `true` ist, wird das Attribut minimiert gerendert.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-368">If the value is `true`, the attribute is rendered minimized.</span></span>

<span data-ttu-id="f9ba4-369">Im folgenden Beispiel bestimmt `IsCompleted`, ob `checked` im Markup des Elements gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-369">In the following example, `IsCompleted` determines if `checked` is rendered in the element's markup:</span></span>

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

<span data-ttu-id="f9ba4-370">Wenn `IsCompleted` den Wert `true` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-370">If `IsCompleted` is `true`, the check box is rendered as:</span></span>

```html
<input type="checkbox" checked />
```

<span data-ttu-id="f9ba4-371">Wenn `IsCompleted` den Wert `false` aufweist, wird das Kontrollkästchen wie folgt gerendert:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-371">If `IsCompleted` is `false`, the check box is rendered as:</span></span>

```html
<input type="checkbox" />
```

<span data-ttu-id="f9ba4-372">Weitere Informationen finden Sie unter [Razor-Syntaxreferenz für ASP.NET Core](xref:mvc/views/razor).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-372">For more information, see [Razor syntax reference for ASP.NET Core](xref:mvc/views/razor).</span></span>

> [!WARNING]
> <span data-ttu-id="f9ba4-373">Einige HTML-Attribute wie [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons) funktionieren nicht ordnungsgemäß, wenn der .NET-Typ `bool` ist.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-373">Some HTML attributes, such as [`aria-pressed`](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons), don't function properly when the .NET type is a `bool`.</span></span> <span data-ttu-id="f9ba4-374">Verwenden Sie in diesen Fällen statt `bool` einen `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-374">In those cases, use a `string` type instead of a `bool`.</span></span>

## <a name="raw-html"></a><span data-ttu-id="f9ba4-375">Unformatierter HTML-Code</span><span class="sxs-lookup"><span data-stu-id="f9ba4-375">Raw HTML</span></span>

<span data-ttu-id="f9ba4-376">Zeichenfolgen werden normalerweise mithilfe von DOM-Textknoten gerendert. Das bedeutet, dass das darin enthaltene Markup vollständig ignoriert und als Literaltext behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-376">Strings are normally rendered using DOM text nodes, which means that any markup they may contain is ignored and treated as literal text.</span></span> <span data-ttu-id="f9ba4-377">Sie können unformatierten HTML-Code rendern, indem Sie den HTML-Inhalt mit einem `MarkupString`-Wert umschließen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-377">To render raw HTML, wrap the HTML content in a `MarkupString` value.</span></span> <span data-ttu-id="f9ba4-378">Der Wert wird als HTML oder SVG analysiert und in das DOM eingefügt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-378">The value is parsed as HTML or SVG and inserted into the DOM.</span></span>

> [!WARNING]
> <span data-ttu-id="f9ba4-379">Das Rendern von unformatiertem HTML-Code, der aus einer nicht vertrauenswürdigen Quelle stammt, gilt als **Sicherheitsrisiko** und sollte vermieden werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-379">Rendering raw HTML constructed from any untrusted source is a **security risk** and should be avoided!</span></span>

<span data-ttu-id="f9ba4-380">Im folgenden Beispiel wird veranschaulicht, wie der `MarkupString`-Typ verwendet wird, um der gerenderten Ausgabe einer Komponente einen Block mit statischem HTML-Inhalt hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-380">The following example shows using the `MarkupString` type to add a block of static HTML content to the rendered output of a component:</span></span>

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="razor-templates"></a><span data-ttu-id="f9ba4-381">Razor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-381">Razor templates</span></span>

<span data-ttu-id="f9ba4-382">Renderingfragmente können mithilfe der Razor-Vorlagensyntax definiert werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-382">Render fragments can be defined using Razor template syntax.</span></span> <span data-ttu-id="f9ba4-383">Mit Razor-Vorlagen können Sie einen Benutzeroberflächencodeausschnitt festlegen und das folgende Format voraussetzen:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-383">Razor templates are a way to define a UI snippet and assume the following format:</span></span>

```razor
@<{HTML tag}>...</{HTML tag}>
```

<span data-ttu-id="f9ba4-384">Im folgenden Beispiel wird veranschaulicht, wie Sie <xref:Microsoft.AspNetCore.Components.RenderFragment>- und <xref:Microsoft.AspNetCore.Components.RenderFragment%601>-Werte angeben und Vorlagen direkt in einer-Komponente rendern können.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-384">The following example illustrates how to specify <xref:Microsoft.AspNetCore.Components.RenderFragment> and <xref:Microsoft.AspNetCore.Components.RenderFragment%601> values and render templates directly in a component.</span></span> <span data-ttu-id="f9ba4-385">Renderingfragmente können auch als Argumente an [Komponentenvorlagen](xref:blazor/components/templated-components) übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-385">Render fragments can also be passed as arguments to [templated components](xref:blazor/components/templated-components).</span></span>

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

<span data-ttu-id="f9ba4-386">Gerenderte Ausgabe des vorangehenden Codes:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-386">Rendered output of the preceding code:</span></span>

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="static-assets"></a><span data-ttu-id="f9ba4-387">Statische Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-387">Static assets</span></span>

<span data-ttu-id="f9ba4-388">Blazor befolgt die Konvention für ASP.NET Core-Apps, statische Ressourcen im [`web root (wwwroot)`-Ordner](xref:fundamentals/index#web-root) des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-388">Blazor follows the convention of ASP.NET Core apps placing static assets under the project's [`web root (wwwroot)` folder](xref:fundamentals/index#web-root).</span></span>

<span data-ttu-id="f9ba4-389">Verwenden Sie einen zur Basis relativen Pfad (`/`), um auf den Webstamm einer statischen Ressource zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-389">Use a base-relative path (`/`) to refer to the web root for a static asset.</span></span> <span data-ttu-id="f9ba4-390">Im folgenden Beispiel befindet sich `logo.png` physisch im Ordner `{PROJECT ROOT}/wwwroot/images`:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-390">In the following example, `logo.png` is physically located in the `{PROJECT ROOT}/wwwroot/images` folder:</span></span>

```razor
<img alt="Company logo" src="/images/logo.png" />
```

<span data-ttu-id="f9ba4-391">Razor-Komponenten unterstützen **keine** Notation mit Tilde und Schrägstrich (`~/`).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-391">Razor components do **not** support tilde-slash notation (`~/`).</span></span>

<span data-ttu-id="f9ba4-392">Weitere Informationen zum Festlegen des Basispfads einer App finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-392">For information on setting an app's base path, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="tag-helpers-arent-supported-in-components"></a><span data-ttu-id="f9ba4-393">Keine Unterstützung von Taghilfsprogrammen in Komponenten</span><span class="sxs-lookup"><span data-stu-id="f9ba4-393">Tag Helpers aren't supported in components</span></span>

<span data-ttu-id="f9ba4-394">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) werden in Razor-Komponenten (`.razor`-Dateien) nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-394">[`Tag Helpers`](xref:mvc/views/tag-helpers/intro) aren't supported in Razor components (`.razor` files).</span></span> <span data-ttu-id="f9ba4-395">Sie können Taghilfsobjekte in Blazor bereitstellen, indem Sie eine Komponente mit der gleichen Funktionalität wie das Taghilfsprogramm erstellen und diese stattdessen verwenden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-395">To provide Tag Helper-like functionality in Blazor, create a component with the same functionality as the Tag Helper and use the component instead.</span></span>

## <a name="scalable-vector-graphics-svg-images"></a><span data-ttu-id="f9ba4-396">SVG-Bilder</span><span class="sxs-lookup"><span data-stu-id="f9ba4-396">Scalable Vector Graphics (SVG) images</span></span>

<span data-ttu-id="f9ba4-397">Da Blazor HTML rendert, werden browsergestützte Bilder wie skalierbare Vektorgrafiken (Scalable Vector Graphics, `.svg`) über das `<img>`-Tag unterstützt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-397">Since Blazor renders HTML, browser-supported images, including Scalable Vector Graphics (SVG) images (`.svg`), are supported via the `<img>` tag:</span></span>

```html
<img alt="Example image" src="some-image.svg" />
```

<span data-ttu-id="f9ba4-398">Ebenso werden SVG-Bilder in den CSS-Regeln einer Stylesheetdatei (`.css`) unterstützt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-398">Similarly, SVG images are supported in the CSS rules of a stylesheet file (`.css`):</span></span>

```css
.my-element {
    background-image: url("some-image.svg");
}
```

<span data-ttu-id="f9ba4-399">SVG-Inlinemarkup wird jedoch nicht in allen Szenarios unterstützt.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-399">However, inline SVG markup isn't supported in all scenarios.</span></span> <span data-ttu-id="f9ba4-400">Wenn Sie ein `<svg>`-Tag direkt in eine Komponentendatei (`.razor`) einfügen, wird das grundlegende Bildrendering unterstützt, viele fortgeschrittene Szenarios aber noch nicht.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-400">If you place an `<svg>` tag directly into a component file (`.razor`), basic image rendering is supported but many advanced scenarios aren't yet supported.</span></span> <span data-ttu-id="f9ba4-401">Beispielsweise werden `<use>`-Tags derzeit nicht beachtet, und [`@bind`][10] kann nicht mit einigen SVG-Tags verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-401">For example, `<use>` tags aren't currently respected, and [`@bind`][10] can't be used with some SVG tags.</span></span> <span data-ttu-id="f9ba4-402">Weitere Informationen finden Sie unter [Improve SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271) (Verbessern der SVG-Unterstützung in Blazor).</span><span class="sxs-lookup"><span data-stu-id="f9ba4-402">For more information, see [SVG support in Blazor (dotnet/aspnetcore #18271)](https://github.com/dotnet/aspnetcore/issues/18271).</span></span>

## <a name="whitespace-rendering-behavior"></a><span data-ttu-id="f9ba4-403">Verhalten beim Rendern von Leerzeichen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-403">Whitespace rendering behavior</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="f9ba4-404">Wenn die [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace)-Direktive mit dem Wert `true`verwendet wird, werden standardmäßig zusätzliche Leerzeichen entfernt, wenn Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-404">Unless the [`@preservewhitespace`](xref:mvc/views/razor#preservewhitespace) directive is used with a value of `true`, extra whitespace is removed by default if:</span></span>

* <span data-ttu-id="f9ba4-405">Sie stehen in einem Element am Anfang oder am Ende.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-405">Leading or trailing within an element.</span></span>
* <span data-ttu-id="f9ba4-406">Sie stehen in einem `RenderFragment`-Parameter am Anfang oder am Ende.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-406">Leading or trailing within a `RenderFragment` parameter.</span></span> <span data-ttu-id="f9ba4-407">Ein Beispiel hierfür ist untergeordneter Inhalt, der an eine andere Komponente übergeben wird</span><span class="sxs-lookup"><span data-stu-id="f9ba4-407">For example, child content passed to another component.</span></span>
* <span data-ttu-id="f9ba4-408">Sie stehen am Anfang oder Ende eines C#-Codeblocks wie `@if` oder `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-408">It precedes or follows a C# code block, such as `@if` or `@foreach`.</span></span>

<span data-ttu-id="f9ba4-409">Das Entfernen der Leerzeichen kann sich auf die gerenderte Ausgabe auswirken, wenn eine CSS-Regel wie `white-space: pre` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-409">Whitespace removal might affect the rendered output when using a CSS rule, such as `white-space: pre`.</span></span> <span data-ttu-id="f9ba4-410">Führen Sie eine der folgenden Aktionen durch, um diese Leistungsoptimierung zu deaktivieren und die Leerzeichen beizubehalten:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-410">To disable this performance optimization and preserve the whitespace, take one of the following actions:</span></span>

* <span data-ttu-id="f9ba4-411">Fügen Sie oben in der `.razor`-Datei die Anweisung `@preservewhitespace true` hinzu, um sie auf eine bestimmte Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-411">Add the `@preservewhitespace true` directive at the top of the `.razor` file to apply the preference to a specific component.</span></span>
* <span data-ttu-id="f9ba4-412">Fügen Sie die Anweisung `@preservewhitespace true` in einer `_Imports.razor`-Datei hinzu, um sie auf ein gesamtes Unterverzeichnis oder das gesamte Projekt anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-412">Add the `@preservewhitespace true` directive inside an `_Imports.razor` file to apply the preference to an entire subdirectory or the entire project.</span></span>

<span data-ttu-id="f9ba4-413">In den meisten Fällen ist keine Aktion erforderlich, weil sich Apps normalerweise wie gewohnt (aber schneller) verhalten.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-413">In most cases, no action is required, as apps typically continue to behave normally (but faster).</span></span> <span data-ttu-id="f9ba4-414">Falls das Entfernen der Leerzeichen für eine bestimmte Komponente zu Problemen führt, sollten Sie darin `@preservewhitespace true` nutzen, um diese Optimierung zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-414">If stripping whitespace causes any problem for a particular component, use `@preservewhitespace true` in that component to disable this optimization.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="f9ba4-415">Ein Leerzeichen wird im Quellcode einer Komponente beibehalten.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-415">Whitespace is retained in a component's source code.</span></span> <span data-ttu-id="f9ba4-416">Text nur mit Leerzeichen wird im Dokumentobjektmodell (DOM) des Browsers auch dann gerendert, wenn dies keine visuellen Auswirkungen hat.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-416">Whitespace-only text renders in the browser's Document Object Model (DOM) even when there's no visual effect.</span></span>

<span data-ttu-id="f9ba4-417">Betrachten Sie die Datei der folgenden Razor-Komponentencode:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-417">Consider the following Razor component code:</span></span>

```razor
<ul>
    @foreach (var item in Items)
    {
        <li>
            @item.Text
        </li>
    }
</ul>
```

<span data-ttu-id="f9ba4-418">Im vorangehenden Beispiel werden folgende unnötige Leerzeichen gerendert:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-418">The preceding example renders the following unnecessary whitespace:</span></span>

* <span data-ttu-id="f9ba4-419">Außerhalb des Codeblocks `@foreach`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-419">Outside of the `@foreach` code block.</span></span>
* <span data-ttu-id="f9ba4-420">Im Bereich des Elements `<li>`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-420">Around the `<li>` element.</span></span>
* <span data-ttu-id="f9ba4-421">Im Bereich der Ausgabe `@item.Text`.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-421">Around the `@item.Text` output.</span></span>

<span data-ttu-id="f9ba4-422">Eine Liste, die 100 Elemente enthält, ergibt 402 Bereiche mit Leerzeichen, und keines der zusätzlichen Leerzeichen wirkt sich visuell auf die gerenderte Ausgabe aus.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-422">A list containing 100 items results in 402 areas of whitespace, and none of the extra whitespace visually affects the rendered output.</span></span>

<span data-ttu-id="f9ba4-423">Beim Rendern von statischem HTML-Code für Komponenten werden Leerzeichen innerhalb eines Tags nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-423">When rendering static HTML for components, whitespace inside a tag isn't preserved.</span></span> <span data-ttu-id="f9ba4-424">Sehen Sie sich beispielsweise die Quelle der folgenden Komponente in der gerenderten Ausgabe an:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-424">For example, view the source of the following component in rendered output:</span></span>

```razor
<img     alt="My image"   src="img.png"     />
```

<span data-ttu-id="f9ba4-425">Leerzeichen werden aus dem vorangehenden Razor-Markup nicht beibehalten:</span><span class="sxs-lookup"><span data-stu-id="f9ba4-425">Whitespace isn't preserved from the preceding Razor markup:</span></span>

```razor
<img alt="My image" src="img.png" />
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f9ba4-426">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f9ba4-426">Additional resources</span></span>

* <span data-ttu-id="f9ba4-427"><xref:blazor/security/server/threat-mitigation>: Enthält Anleitungen zum Erstellen von Blazor Server-Apps, die Ressourcenauslastung handhaben müssen.</span><span class="sxs-lookup"><span data-stu-id="f9ba4-427"><xref:blazor/security/server/threat-mitigation>: Includes guidance on building Blazor Server apps that must contend with resource exhaustion.</span></span>

<!--Reference links in article-->
[1]: <xref:mvc/views/razor#code> "Razor Syntaxreferenz für ASP.NET Core"
[2]: <xref:mvc/views/razor#using> "Razor Syntaxreferenz für ASP.NET Core"
[3]: <xref:mvc/views/razor#attributes> "Razor Syntaxreferenz für ASP.NET Core"
[4]: <xref:mvc/views/razor#ref> "Razor Syntaxreferenz für ASP.NET Core"
[5]: <xref:mvc/views/razor#key> "Razor Syntaxreferenz für ASP.NET Core"
[6]: <xref:mvc/views/razor#inherits> "Razor Syntaxreferenz für ASP.NET Core"
[7]: <xref:mvc/views/razor#attribute> "Razor Syntaxreferenz für ASP.NET Core"
[8]: <xref:mvc/views/razor#namespace> "Razor Syntaxreferenz für ASP.NET Core"
[9]: <xref:mvc/views/razor#page> "Razor Syntaxreferenz für ASP.NET Core"
[10]: <xref:mvc/views/razor#bind> "Razor Syntaxreferenz für ASP.NET Core"
