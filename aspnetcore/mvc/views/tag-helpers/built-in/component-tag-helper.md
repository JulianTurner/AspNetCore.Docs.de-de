---
title: Komponententaghilfsprogramm in ASP.net Core
author: guardrex
ms.author: riande
description: 'Erfahren Sie, wie Sie das taghilfsprogramm ASP.net Core Component zum Rendering von :::no-loc(Razor)::: Komponenten in Seiten und Ansichten verwenden.'
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/views/tag-helpers/builtin-th/component-tag-helper
ms.openlocfilehash: 8e780de2367f66ad1f5197077d5243e0b85a41dd
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431042"
---
# <a name="component-tag-helper-in-aspnet-core"></a><span data-ttu-id="623ae-103">Komponententaghilfsprogramm in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="623ae-103">Component Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="623ae-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="623ae-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="prerequisites"></a><span data-ttu-id="623ae-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="623ae-105">Prerequisites</span></span>

<span data-ttu-id="623ae-106">Befolgen Sie die Anweisungen im *Konfigurations* Abschnitt für eine der folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="623ae-106">Follow the guidance in the *Configuration* section for either:</span></span>

* [:::no-loc(Blazor WebAssembly):::](xref:blazor/components/prerendering-and-integration?pivots=webassembly)
* [:::no-loc(Blazor Server):::](xref:blazor/components/prerendering-and-integration?pivots=server)

## <a name="component-tag-helper"></a><span data-ttu-id="623ae-107">Komponententaghilfsprogramm</span><span class="sxs-lookup"><span data-stu-id="623ae-107">Component Tag Helper</span></span>

<span data-ttu-id="623ae-108">Verwenden Sie das [komponententaghilfsprogramm](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (Tag), um eine Komponente aus einer Seite oder Sicht zu Rendering `<component>` .</span><span class="sxs-lookup"><span data-stu-id="623ae-108">To render a component from a page or view, use the [Component Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper) (`<component>` tag).</span></span>

> [!NOTE]
> <span data-ttu-id="623ae-109">Das Integrieren von :::no-loc(Razor)::: Komponenten in :::no-loc(Razor)::: Seiten und MVC-apps in einer *gehosteten :::no-loc(Blazor WebAssembly)::: App* wird in ASP.net Core in .net 5,0 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="623ae-109">Integrating :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps in a *hosted :::no-loc(Blazor WebAssembly)::: app* is supported in ASP.NET Core in .NET 5.0 or later.</span></span>

<span data-ttu-id="623ae-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="623ae-110"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="623ae-111">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="623ae-111">Is prerendered into the page.</span></span>
* <span data-ttu-id="623ae-112">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer :::no-loc(Blazor):::-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="623ae-112">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="623ae-113">:::no-loc(Blazor WebAssembly)::: App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="623ae-113">:::no-loc(Blazor WebAssembly)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="623ae-114">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="623ae-114">Render Mode</span></span> | <span data-ttu-id="623ae-115">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="623ae-115">Description</span></span> |
| ----------- | ----------- |
| `WebAssembly` | <span data-ttu-id="623ae-116">Rendert einen Marker für eine :::no-loc(Blazor WebAssembly)::: app, um beim Laden in den Browser eine interaktive Komponente einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="623ae-116">Renders a marker for a :::no-loc(Blazor WebAssembly)::: app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="623ae-117">Die Komponente wird nicht vorab übernommen.</span><span class="sxs-lookup"><span data-stu-id="623ae-117">The component isn't prerendered.</span></span> <span data-ttu-id="623ae-118">Diese Option erleichtert das Rendering verschiedener :::no-loc(Blazor WebAssembly)::: Komponenten auf verschiedenen Seiten.</span><span class="sxs-lookup"><span data-stu-id="623ae-118">This option makes it easier to render different :::no-loc(Blazor WebAssembly)::: components on different pages.</span></span> |
| `WebAssemblyPrerendered` | <span data-ttu-id="623ae-119">Gibt die Komponente in statischem HTML-Code aus und umfasst einen Marker für eine :::no-loc(Blazor WebAssembly)::: app, der später verwendet wird, um die Komponente beim Laden im Browser interaktiv zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="623ae-119">Prerenders the component into static HTML and includes a marker for a :::no-loc(Blazor WebAssembly)::: app for later use to make the component interactive when loaded in the browser.</span></span> |

<span data-ttu-id="623ae-120">:::no-loc(Blazor Server)::: App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="623ae-120">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="623ae-121">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="623ae-121">Render Mode</span></span> | <span data-ttu-id="623ae-122">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="623ae-122">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="623ae-123">Rendert die Komponente in statisches HTML und fügt einen Marker für eine :::no-loc(Blazor Server):::-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="623ae-123">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="623ae-124">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="623ae-124">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="623ae-125">Rendert einen Marker für eine :::no-loc(Blazor Server):::-App.</span><span class="sxs-lookup"><span data-stu-id="623ae-125">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="623ae-126">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="623ae-126">Output from the component isn't included.</span></span> <span data-ttu-id="623ae-127">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="623ae-127">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="623ae-128">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="623ae-128">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="623ae-129">:::no-loc(Blazor Server)::: App-Rendermodi sind in der folgenden Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="623ae-129">:::no-loc(Blazor Server)::: app render modes are shown in the following table.</span></span>

| <span data-ttu-id="623ae-130">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="623ae-130">Render Mode</span></span> | <span data-ttu-id="623ae-131">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="623ae-131">Description</span></span> |
| ----------- | ----------- |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="623ae-132">Rendert die Komponente in statisches HTML und fügt einen Marker für eine :::no-loc(Blazor Server):::-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="623ae-132">Renders the component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="623ae-133">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="623ae-133">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="623ae-134">Rendert einen Marker für eine :::no-loc(Blazor Server):::-App.</span><span class="sxs-lookup"><span data-stu-id="623ae-134">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="623ae-135">Die Ausgabe der Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="623ae-135">Output from the component isn't included.</span></span> <span data-ttu-id="623ae-136">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="623ae-136">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
| <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="623ae-137">Rendert die Komponente in statischen HTML-Code.</span><span class="sxs-lookup"><span data-stu-id="623ae-137">Renders the component into static HTML.</span></span> |

::: moniker-end

<span data-ttu-id="623ae-138">Zu den zusätzlichen Merkmalen gehören:</span><span class="sxs-lookup"><span data-stu-id="623ae-138">Additional characteristics include:</span></span>

* <span data-ttu-id="623ae-139">Mehrere komponententaghilfsprogramme, die mehrere Komponenten rendern, :::no-loc(Razor)::: sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="623ae-139">Multiple Component Tag Helpers rendering multiple :::no-loc(Razor)::: components is allowed.</span></span>
* <span data-ttu-id="623ae-140">Komponenten können nicht dynamisch gerendert werden, nachdem die APP gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="623ae-140">Components can't be dynamically rendered after the app has started.</span></span>
* <span data-ttu-id="623ae-141">Während Seiten und Ansichten Komponenten verwenden können, ist das Gegenteil nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="623ae-141">While pages and views can use components, the converse isn't true.</span></span> <span data-ttu-id="623ae-142">Komponenten können keine Ansichts-und Seiten spezifischen Funktionen verwenden, wie z. b. partielle Sichten und Abschnitte.</span><span class="sxs-lookup"><span data-stu-id="623ae-142">Components can't use view- and page-specific features, such as partial views and sections.</span></span> <span data-ttu-id="623ae-143">Wenn Sie Logik aus einer partiellen Sicht in einer Komponente verwenden möchten, müssen Sie die partielle Sicht Logik in eine Komponente einbeziehen.</span><span class="sxs-lookup"><span data-stu-id="623ae-143">To use logic from a partial view in a component, factor out the partial view logic into a component.</span></span>
* <span data-ttu-id="623ae-144">Das Rendern von Serverkomponenten über eine statische HTML-Seite wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="623ae-144">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="623ae-145">Das folgende komponententaghilfsprogramm rendert die `Counter` Komponente in einer Seite oder Ansicht in einer- :::no-loc(Blazor Server)::: App mit `ServerPrerendered` :</span><span class="sxs-lookup"><span data-stu-id="623ae-145">The following Component Tag Helper renders the `Counter` component in a page or view in a :::no-loc(Blazor Server)::: app with `ServerPrerendered`:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Pages

...

<component type="typeof(Counter)" render-mode="ServerPrerendered" />
```

<span data-ttu-id="623ae-146">Im vorangehenden Beispiel wird davon ausgegangen, dass `Counter` sich die Komponente im *Seiten* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="623ae-146">The preceding example assumes that the `Counter` component is in the app's *Pages* folder.</span></span> <span data-ttu-id="623ae-147">Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z. b. `@using :::no-loc(Blazor):::Sample.Pages` oder `@using :::no-loc(Blazor):::Sample.Client.Pages` in einer gehosteten :::no-loc(Blazor)::: Lösung).</span><span class="sxs-lookup"><span data-stu-id="623ae-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Pages` or `@using :::no-loc(Blazor):::Sample.Client.Pages` in a hosted :::no-loc(Blazor)::: solution).</span></span>

<span data-ttu-id="623ae-148">Mit dem komponententaghilfsprogramm können auch Parameter an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="623ae-148">The Component Tag Helper can also pass parameters to components.</span></span> <span data-ttu-id="623ae-149">Sehen `ColorfulCheckbox` Sie sich die folgende Komponente an, mit der die Farbe und Größe der Kontrollkästchen Bezeichnung festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="623ae-149">Consider the following `ColorfulCheckbox` component that sets the check box label's color and size:</span></span>

```razor
<label style="font-size:@(Size)px;color:@Color">
    <input @bind="Value"
           id="survey" 
           name="blazor" 
           type="checkbox" />
    Enjoying :::no-loc(Blazor):::?
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

<span data-ttu-id="623ae-150">Die `Size` `int` Komponenten Parameter () und `Color` ( `string` ) können vom komponententaghilfsprogramm festgelegt werden: [component parameters](xref:blazor/components/index#component-parameters)</span><span class="sxs-lookup"><span data-stu-id="623ae-150">The `Size` (`int`) and `Color` (`string`) [component parameters](xref:blazor/components/index#component-parameters) can be set by the Component Tag Helper:</span></span>

```cshtml
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@using {APP ASSEMBLY}.Shared

...

<component type="typeof(ColorfulCheckbox)" render-mode="ServerPrerendered" 
    param-Size="14" param-Color="@("blue")" />
```

<span data-ttu-id="623ae-151">Im vorangehenden Beispiel wird davon ausgegangen, dass `ColorfulCheckbox` sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="623ae-151">The preceding example assumes that the `ColorfulCheckbox` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="623ae-152">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `@using :::no-loc(Blazor):::Sample.Shared`).</span><span class="sxs-lookup"><span data-stu-id="623ae-152">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample.Shared`).</span></span>

<span data-ttu-id="623ae-153">Der folgende HTML-Code wird in der Seite oder Sicht gerendert:</span><span class="sxs-lookup"><span data-stu-id="623ae-153">The following HTML is rendered in the page or view:</span></span>

```html
<label style="font-size:24px;color:blue">
    <input id="survey" name="blazor" type="checkbox">
    Enjoying :::no-loc(Blazor):::?
</label>
```

<span data-ttu-id="623ae-154">Das übergeben einer Zeichenfolge in Anführungszeichen erfordert einen [expliziten :::no-loc(Razor)::: Ausdruck](xref:mvc/views/razor#explicit-razor-expressions), wie `param-Color` im vorherigen Beispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="623ae-154">Passing a quoted string requires an [explicit :::no-loc(Razor)::: expression](xref:mvc/views/razor#explicit-razor-expressions), as shown for `param-Color` in the preceding example.</span></span> <span data-ttu-id="623ae-155">Das Verarbeitungs :::no-loc(Razor)::: Verhalten für einen `string` Typwert gilt nicht für ein- `param-*` Attribut, da es sich bei dem Attribut um einen- `object` Typ handelt.</span><span class="sxs-lookup"><span data-stu-id="623ae-155">The :::no-loc(Razor)::: parsing behavior for a `string` type value doesn't apply to a `param-*` attribute because the attribute is an `object` type.</span></span>

<span data-ttu-id="623ae-156">Alle Parametertypen werden unterstützt, ausgenommen:</span><span class="sxs-lookup"><span data-stu-id="623ae-156">All types of parameters are supported, except:</span></span>

* <span data-ttu-id="623ae-157">Generische Parameter.</span><span class="sxs-lookup"><span data-stu-id="623ae-157">Generic parameters.</span></span>
* <span data-ttu-id="623ae-158">Nicht serialisierbare Parameter.</span><span class="sxs-lookup"><span data-stu-id="623ae-158">Non-serializable parameters.</span></span>
* <span data-ttu-id="623ae-159">Vererbung in Sammlungs Parametern.</span><span class="sxs-lookup"><span data-stu-id="623ae-159">Inheritance in collection parameters.</span></span>
* <span data-ttu-id="623ae-160">Parameter, deren Typ außerhalb der :::no-loc(Blazor WebAssembly)::: app oder innerhalb einer verzögert geladenen Assembly definiert ist.</span><span class="sxs-lookup"><span data-stu-id="623ae-160">Parameters whose type is defined outside of the :::no-loc(Blazor WebAssembly)::: app or within a lazily-loaded assembly.</span></span>

<span data-ttu-id="623ae-161">Der Parametertyp muss JSON-serialisierbar sein. Dies bedeutet in der Regel, dass der Typ einen Standardkonstruktor und festleg bare Eigenschaften aufweisen muss.</span><span class="sxs-lookup"><span data-stu-id="623ae-161">The parameter type must be JSON serializable, which typically means that the type must have a default constructor and settable properties.</span></span> <span data-ttu-id="623ae-162">Beispielsweise können Sie einen Wert für `Size` und `Color` im vorangehenden Beispiel angeben, da die Typen von `Size` und `Color` primitive Typen ( `int` und `string` ) sind, die vom JSON-Serialisierungsprogramm unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="623ae-162">For example, you can specify a value for `Size` and `Color` in the preceding example because the types of `Size` and `Color` are primitive types (`int` and `string`), which are supported by the JSON serializer.</span></span>

<span data-ttu-id="623ae-163">Im folgenden Beispiel wird ein-Klassenobjekt an die-Komponente übermittelt:</span><span class="sxs-lookup"><span data-stu-id="623ae-163">In the following example, a class object is passed to the component:</span></span>

<span data-ttu-id="623ae-164">*MyClass.cs* :</span><span class="sxs-lookup"><span data-stu-id="623ae-164">*MyClass.cs* :</span></span>

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

<span data-ttu-id="623ae-165">**Die Klasse muss über einen öffentlichen Parameter losen Konstruktor verfügen.**</span><span class="sxs-lookup"><span data-stu-id="623ae-165">**The class must have a public parameterless constructor.**</span></span>

<span data-ttu-id="623ae-166">*Shared/MyComponent. Razor* :</span><span class="sxs-lookup"><span data-stu-id="623ae-166">*Shared/MyComponent.razor* :</span></span>

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

<span data-ttu-id="623ae-167">*Pages/mypage. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="623ae-167">*Pages/MyPage.cshtml* :</span></span>

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

<span data-ttu-id="623ae-168">Im vorangehenden Beispiel wird davon ausgegangen, dass `MyComponent` sich die Komponente im frei *gegebenen* Ordner der APP befindet.</span><span class="sxs-lookup"><span data-stu-id="623ae-168">The preceding example assumes that the `MyComponent` component is in the app's *Shared* folder.</span></span> <span data-ttu-id="623ae-169">Der Platzhalter `{APP ASSEMBLY}` ist der AssemblyName der APP (z `@using :::no-loc(Blazor):::Sample` `@using :::no-loc(Blazor):::Sample.Shared` . b. und).</span><span class="sxs-lookup"><span data-stu-id="623ae-169">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `@using :::no-loc(Blazor):::Sample` and `@using :::no-loc(Blazor):::Sample.Shared`).</span></span> <span data-ttu-id="623ae-170">`MyClass` befindet sich im-Namespace der app.</span><span class="sxs-lookup"><span data-stu-id="623ae-170">`MyClass` is in the app's namespace.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="623ae-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="623ae-171">Additional resources</span></span>

* <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper>
* <xref:mvc/views/tag-helpers/intro>
* <xref:blazor/components/index>
