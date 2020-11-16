---
title: Komponententaghilfsprogramm in ASP.net Core
author: guardrex
ms.author: riande
description: 'Erfahren Sie, wie Sie das taghilfsprogramm ASP.net Core Component zum Rendering von Razor Komponenten in Seiten und Ansichten verwenden.'
ms.custom: mvc
ms.date: 10/29/2020
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
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 761c125e3c5f94157cf7bf4524374db2545610b1
ms.sourcegitcommit: 98f92d766d4f343d7e717b542c1b08da29e789c1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94595453"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="72f9f-103">Komponententaghilfsprogramm in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="72f9f-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="72f9f-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="72f9f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="72f9f-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="72f9f-105">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="72f9f-106">Befolgen Sie die Anweisungen im *Konfigurations* Abschnitt für eine der folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="72f9f-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [Blazor WebAssembly](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [Blazor Server](xref:blazor/components/prerendering-and-integration?pivots=server)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="72f9f-107">Befolgen Sie die Anweisungen im Abschnitt " *Konfiguration* " des <xref:blazor/components/prerendering-and-integration?pivots=server> Artikels.</span><span class="sxs-lookup"><span data-stu-id="72f9f-107">Follow the guidance in the *Configuration* section of the <xref:blazor/components/prerendering-and-integration?pivots=server> article.</span></span>

::: moniker-end

## <a name="component-tag-helper"></a><span data-ttu-id="72f9f-108">Komponententaghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="72f9f-108">Component Tag Helper</span></span>

<span data-ttu-id="72f9f-109">Verwenden Sie das [komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (Tag), um eine Komponente aus einer Seite oder Sicht zu Rendering `<component>` .</span><span class="sxs-lookup"><span data-stu-id="72f9f-109">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="72f9f-110">Das Integrieren von Razor Komponenten in Razor Seiten und MVC-apps in einer *gehosteten Blazor WebAssembly App* wird in ASP.net Core in .net 5,0 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-110">Integrating Razor components into Razor Pages and MVC apps in a *hosted Blazor WebAssembly app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="72f9f-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="72f9f-111"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="72f9f-112">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="72f9f-112">Is prerendered into the page.</span></span>
* <span data-ttu-id="72f9f-113">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="72f9f-113">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="72f9f-114">Blazor WebAssembly App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-114">Blazor WebAssembly app render modes are shown in the following table.</span></span>

| <span data-ttu-id="72f9f-115">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="72f9f-115">Render Mode</span></span> | <span data-ttu-id="72f9f-116">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="72f9f-116">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="72f9f-117">Rendert einen Marker für eine Blazor WebAssembly app, um beim Laden in den Browser eine interaktive Komponente einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="72f9f-117">Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="72f9f-118">Die Komponente wird nicht vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="72f9f-118">The component isn't prerendered.</span></span> <span data-ttu-id="72f9f-119">Diese Option erleichtert das Rendering verschiedener Blazor WebAssembly Komponenten auf verschiedenen Seiten.</span><span class="sxs-lookup"><span data-stu-id="72f9f-119">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="72f9f-120">Gibt die Komponente in statischem HTML-Code aus und umfasst einen Marker für eine Blazor WebAssembly app, der später verwendet wird, um die Komponente beim Laden im Browser interaktiv zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="72f9f-120">Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="72f9f-121">Blazor Server App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-121">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="72f9f-122">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="72f9f-122">Render Mode</span></span> | <span data-ttu-id="72f9f-123">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="72f9f-123">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="72f9f-124">Rendert die Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="72f9f-124">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="72f9f-125">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-125">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="72f9f-126">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="72f9f-126">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="72f9f-127">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="72f9f-127">Output from the component isn't included.</span></span> <span data-ttu-id="72f9f-128">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-128">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="72f9f-129">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="72f9f-129">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="72f9f-130">Blazor Server App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-130">Blazor Server app render modes are shown in the following table.</span></span>

| <span data-ttu-id="72f9f-131">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="72f9f-131">Render Mode</span></span> | <span data-ttu-id="72f9f-132">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="72f9f-132">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="72f9f-133">Rendert die Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="72f9f-133">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="72f9f-134">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-134">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="72f9f-135">Rendert einen Marker für eine Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="72f9f-135">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="72f9f-136">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="72f9f-136">Output from the component isn't included.</span></span> <span data-ttu-id="72f9f-137">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-137">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="72f9f-138">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="72f9f-138">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="72f9f-139">Zu den zusätzlichen Merkmalen gehören:</span><span class="sxs-lookup"><span data-stu-id="72f9f-139">Additional characteristics include:</span></span>

* <span data-ttu-id="72f9f-140">Mehrere komponententaghilfsprogramme, die mehrere Komponenten rendern, Razor sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="72f9f-140">Multiple Component Tag Helpers rendering multiple Razor components is allowed.</span></span>
* <span data-ttu-id="72f9f-141">Komponenten können nicht dynamisch gerendert werden, nachdem die APP gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="72f9f-141">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="72f9f-142">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="72f9f-142">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="72f9f-143">Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="72f9f-143">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="72f9f-144">Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="72f9f-144">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="72f9f-145">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-145">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="72f9f-146">Das folgende komponententaghilfsprogramm rendert die `Counter` Komponente in einer Seite oder Ansicht in einer- Blazor Server App mit `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="72f9f-146">The following Component Tag Helper renders the `Counter` component in a page or view in a Blazor Server app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="72f9f-147">Im vorangehenden Beispiel wird davon ausgegangen, dass `Counter` sich die Komponente im *Seiten* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-147">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="72f9f-148">Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z. b. `@using BlazorSample.Pages` oder `@using BlazorSample.Client.Pages` in einer gehosteten Blazor Lösung).</span><span class="sxs-lookup"><span data-stu-id="72f9f-148">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Pages` or `@using BlazorSample.Client.Pages` in a hosted Blazor solution).</span></span>

<span data-ttu-id="72f9f-149">Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="72f9f-149">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="72f9f-150">Sehen `ColorfulCheckbox` Sie sich die folgende Komponente an, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="72f9f-150">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying Blazor?
</label>

@code {
    [Parameter]
    public bool Value { get; set; }

    [Parameter]
    public int Size { get; set; } = 8;

    [Parameter]
    public string Color { get; set; }

    protected override void OnInitialized()
    {
        Size += 10;
    }
}
```

<span data-ttu-id="72f9f-151">Die `Size` `int` Komponenten Parameter () und `Color` ( `string` ) können vom komponententaghilfsprogramm festgelegt werden: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="72f9f-151">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="72f9f-152">Im vorangehenden Beispiel wird davon ausgegangen, dass `ColorfulCheckbox` sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-152">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="72f9f-153">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `@using BlazorSample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="72f9f-153">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample.Shared`).</span></span>

<span data-ttu-id="72f9f-154">Der folgende HTML-Code wird in der Seite oder Sicht gerendert:</span><span class="sxs-lookup"><span data-stu-id="72f9f-154">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying Blazor?
</label>
```

<span data-ttu-id="72f9f-155">Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten Razor Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie `param-Color` im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-155">Passing a quoted string requires an [explicit Razor expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="72f9f-156">Das Verarbeitungs Razor Verhalten für einen `string` Typwert gilt nicht für ein- `param-*` Attribut, da es sich bei dem Attribut um einen- `object` Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="72f9f-156">The Razor parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="72f9f-157">Alle Parametertypen werden unterstützt, ausgenommen:</span><span class="sxs-lookup"><span data-stu-id="72f9f-157">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="72f9f-158">Generische Parameter.</span><span class="sxs-lookup"><span data-stu-id="72f9f-158">Generic parameters.</span></span>
* <span data-ttu-id="72f9f-159">Nicht serialisierbare Parameter.</span><span class="sxs-lookup"><span data-stu-id="72f9f-159">Non-serializable parameters.</span></span>
* <span data-ttu-id="72f9f-160">Vererbung in Sammlungs Parametern.</span><span class="sxs-lookup"><span data-stu-id="72f9f-160">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="72f9f-161">Parameter, deren Typ außerhalb der Blazor WebAssembly app oder innerhalb einer verzögert geladenen Assembly definiert ist.</span><span class="sxs-lookup"><span data-stu-id="72f9f-161">Parameters whose type is defined outside of the Blazor WebAssembly app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="72f9f-162">Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="72f9f-162">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="72f9f-163">Beispielsweise können Sie einen Wert für `Size` und `Color` im vorangehenden Beispiel angeben, da die Typen von `Size` und `Color` primitive Typen ( `int` und `string` ) sind, die vom JSON-Serialisierungsprogramm unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="72f9f-163">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="72f9f-164">Im folgenden Beispiel wird ein-Klassenobjekt an die-Komponente übermittelt:</span><span class="sxs-lookup"><span data-stu-id="72f9f-164">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="72f9f-165">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="72f9f-165">*MyClass.cs* :</span></span>

```csharp
public class MyClass
{
    public MyClass()
    {
    }

    public int MyInt { get; set; } = 999;
    public string MyString { get; set; } = "Initial value";
}
```

<span data-ttu-id="72f9f-166">**Die Klasse muss über einen öffentlichen Parameter losen Konstruktor verfügen.**</span><span class="sxs-lookup"><span data-stu-id="72f9f-166">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="72f9f-167">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="72f9f-167">*Shared/MyComponent.razor* :</span></span>

```razor
<h2>MyComponent</h2>

<p>Int: @MyObject.MyInt</p>
<p>String: @MyObject.MyString</p>

@code
{
    [Parameter]
    public MyClass MyObject { get; set; }
}
```

<span data-ttu-id="72f9f-168">*Pages/mypage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="72f9f-168">*Pages/MyPage.cshtml* :</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}
@using {APP ASSEMBLY}.Shared

...

@{
    var myObject = new MyClass();
    myObject.MyInt = 7;
    myObject.MyString = "Set by MyPage";
}

<component type="typeof(MyComponent)" render-mode="ServerPrerendered" 
    param-MyObject="@myObject" />
```

<span data-ttu-id="72f9f-169">Im vorangehenden Beispiel wird davon ausgegangen, dass `MyComponent` sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="72f9f-169">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="72f9f-170">Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z `@using BlazorSample` `@using BlazorSample.Shared` . b. und).</span><span class="sxs-lookup"><span data-stu-id="72f9f-170">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using BlazorSample` and `@using BlazorSample.Shared`).</span></span> <span data-ttu-id="72f9f-171">`MyClass` befindet sich im-Namespace der app.</span><span class="sxs-lookup"><span data-stu-id="72f9f-171">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="72f9f-172">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="72f9f-172">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
