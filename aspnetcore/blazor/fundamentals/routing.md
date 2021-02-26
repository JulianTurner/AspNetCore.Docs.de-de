---
title: ASP.NET Core Blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie das Anforderungsrouting in Apps verwalten und wie Sie die NavLink-Komponente in Blazor-Apps zur Navigation verwenden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 55e2cbc01af7352facad7121c05c754e9d438ae3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279884"
---
# <a name="aspnet-core-blazor-routing"></a><span data-ttu-id="242b6-103">ASP.NET Core Blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="242b6-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="242b6-104">In diesem Artikel erfahren Sie, wie Sie das Anforderungsrouting verwalten und die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verwenden können, um Navigationslinks in Blazor-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="242b6-104">In this article, learn how to manage request routing and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create a navigation links in Blazor apps.</span></span>

## <a name="route-templates"></a><span data-ttu-id="242b6-105">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="242b6-105">Route templates</span></span>

<span data-ttu-id="242b6-106">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht das Routing an Razor-Komponenten in einer Blazor-App.</span><span class="sxs-lookup"><span data-stu-id="242b6-106">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to Razor components in a Blazor app.</span></span> <span data-ttu-id="242b6-107">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente wird in der `App`-Komponente von Blazor-Apps verwendet.</span><span class="sxs-lookup"><span data-stu-id="242b6-107">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component is used in the `App` component of Blazor apps.</span></span>

<span data-ttu-id="242b6-108">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-108">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App1.razor)]

::: moniker-end

<span data-ttu-id="242b6-109">Wenn eine Razor-Komponente (`.razor`) mit einer [`@page`-Anweisung](xref:mvc/views/razor#page) kompiliert wird, wird für die generierte Komponentenklasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Objekt bereitgestellt, das die Routenvorlage der Komponente angibt.</span><span class="sxs-lookup"><span data-stu-id="242b6-109">When a Razor component (`.razor`) with an [`@page` directive](xref:mvc/views/razor#page) is compiled, the generated component class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the component's route template.</span></span>

<span data-ttu-id="242b6-110">Wenn die App gestartet wird, wird die Assembly gescannt, die als `AppAssembly`-Objekt des Routers angegeben wurde, um Routeninformationen für die Komponenten der App zu erfassen, die über ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Objekt verfügen.</span><span class="sxs-lookup"><span data-stu-id="242b6-110">When the app starts, the assembly specified as the Router's `AppAssembly` is scanned to gather route information for the app's components that have a <xref:Microsoft.AspNetCore.Components.RouteAttribute>.</span></span>

<span data-ttu-id="242b6-111">Zur Laufzeit führt die <xref:Microsoft.AspNetCore.Components.RouteView>-Komponente Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="242b6-111">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="242b6-112">Sie empfängt das <xref:Microsoft.AspNetCore.Components.RouteData>-Objekt vom <xref:Microsoft.AspNetCore.Components.Routing.Router>-Objekt zusammen mit den Routenparametern.</span><span class="sxs-lookup"><span data-stu-id="242b6-112">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any route parameters.</span></span>
* <span data-ttu-id="242b6-113">Sie rendert die angegebene Komponente mit deren [Layout](xref:blazor/layouts), einschließlich aller weiteren geschachtelten Layouts.</span><span class="sxs-lookup"><span data-stu-id="242b6-113">Renders the specified component with its [layout](xref:blazor/layouts), including any further nested layouts.</span></span>

<span data-ttu-id="242b6-114">Optional können Sie einen <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout>-Parameter mit einer Layoutklasse für Komponenten angeben, die kein Layout mit der [`@layout`-Anweisung](xref:blazor/layouts#specify-a-layout-in-a-component) festlegen.</span><span class="sxs-lookup"><span data-stu-id="242b6-114">Optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class for components that don't specify a layout with the [`@layout` directive](xref:blazor/layouts#specify-a-layout-in-a-component).</span></span> <span data-ttu-id="242b6-115">Die Blazor-Projektvorlagen des Frameworks geben die `MainLayout`-Komponente (`Shared/MainLayout.razor`) als Standardlayout der App an.</span><span class="sxs-lookup"><span data-stu-id="242b6-115">The framework's Blazor project templates specify the `MainLayout` component (`Shared/MainLayout.razor`) as the app's default layout.</span></span> <span data-ttu-id="242b6-116">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="242b6-116">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="242b6-117">Komponenten unterstützen mehrere Routenvorlagen mithilfe mehrerer [`@page`-Anweisungen](xref:mvc/views/razor#page).</span><span class="sxs-lookup"><span data-stu-id="242b6-117">Components support multiple route templates using multiple [`@page` directives](xref:mvc/views/razor#page).</span></span> <span data-ttu-id="242b6-118">Die folgende Beispielkomponente lädt Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`.</span><span class="sxs-lookup"><span data-stu-id="242b6-118">The following example component loads on requests for `/BlazorRoute` and `/DifferentBlazorRoute`.</span></span>

<span data-ttu-id="242b6-119">`Pages/BlazorRoute.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-119">`Pages/BlazorRoute.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> <span data-ttu-id="242b6-120">Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) mit dem im `href`-Attribut angegebenen App-Basispfad enthalten.</span><span class="sxs-lookup"><span data-stu-id="242b6-120">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute.</span></span> <span data-ttu-id="242b6-121">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="242b6-121">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="242b6-122">Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde</span><span class="sxs-lookup"><span data-stu-id="242b6-122">Provide custom content when content isn't found</span></span>

<span data-ttu-id="242b6-123">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="242b6-123">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="242b6-124">In der `App`-Komponente können Sie benutzerdefinierten Inhalt in der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Vorlage der <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Komponente festlegen.</span><span class="sxs-lookup"><span data-stu-id="242b6-124">In the `App` component, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component's <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template.</span></span>

<span data-ttu-id="242b6-125">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-125">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App2.razor?highlight=5-8)]

::: moniker-end

<span data-ttu-id="242b6-126">Beliebige Elemente werden als Inhalt der `<NotFound>`-Tags unterstützt, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="242b6-126">Arbitrary items are supported as content of the `<NotFound>` tags, such as other interactive components.</span></span> <span data-ttu-id="242b6-127">Informationen zum Anwenden eines Standardlayouts auf <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalte finden Sie unter <xref:blazor/layouts#default-layout>.</span><span class="sxs-lookup"><span data-stu-id="242b6-127">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts#default-layout>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="242b6-128">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="242b6-128">Route to components from multiple assemblies</span></span>

<span data-ttu-id="242b6-129">Verwenden Sie den <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Parameter, um zusätzliche Assemblys anzugeben, die die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll.</span><span class="sxs-lookup"><span data-stu-id="242b6-129">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="242b6-130">Zusätzliche Assemblys werden neben der für `AppAssembly` angegebenen Assembly gescannt.</span><span class="sxs-lookup"><span data-stu-id="242b6-130">Additional assemblies are scanned in addition to the assembly specified to `AppAssembly`.</span></span> <span data-ttu-id="242b6-131">Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer [Komponentenklassenbibliothek](xref:blazor/components/class-libraries) definiert ist, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="242b6-131">In the following example, `Component1` is a routable component defined in a referenced [component class library](xref:blazor/components/class-libraries).</span></span> <span data-ttu-id="242b6-132">Im folgenden <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Beispiel wird Routing für `Component1` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="242b6-132">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`.</span></span>

<span data-ttu-id="242b6-133">`App.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-133">`App.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/routing/App3.razor?name=snippet)]

::: moniker-end

## <a name="route-parameters"></a><span data-ttu-id="242b6-134">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="242b6-134">Route parameters</span></span>

<span data-ttu-id="242b6-135">Der Router verwendet Routenparameter, um die entsprechenden [Komponentenparameter](xref:blazor/components/index#component-parameters) mit demselben Namen aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="242b6-135">The router uses route parameters to populate the corresponding [component parameters](xref:blazor/components/index#component-parameters) with the same name.</span></span> <span data-ttu-id="242b6-136">Bei den Routenparameternamen muss die Groß- und Kleinschreibung nicht berücksichtigt werden.</span><span class="sxs-lookup"><span data-stu-id="242b6-136">Route parameter names are case insensitive.</span></span> <span data-ttu-id="242b6-137">Im folgenden Beispiel weist der Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu.</span><span class="sxs-lookup"><span data-stu-id="242b6-137">In the following example, the `text` parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="242b6-138">Wenn eine Anforderung für `/RouteParameter/amazing` erfolgt, wird der Inhalt der `<h1>`-Tags als `Blazor is amazing!` gerendert.</span><span class="sxs-lookup"><span data-stu-id="242b6-138">When a request is made for `/RouteParameter/amazing`, the `<h1>` tag content is rendered as `Blazor is amazing!`.</span></span>

<span data-ttu-id="242b6-139">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-139">`Pages/RouteParameter.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="242b6-140">Optionale Parameter werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="242b6-140">Optional parameters are supported.</span></span> <span data-ttu-id="242b6-141">Im folgenden Beispiel weist der optionale Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu.</span><span class="sxs-lookup"><span data-stu-id="242b6-141">In the following example, the `text` optional parameter assigns the value of the route segment to the component's `Text` property.</span></span> <span data-ttu-id="242b6-142">Wenn das Segment nicht vorhanden ist, wird der Wert von `Text` auf `fantastic` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="242b6-142">If the segment isn't present, the value of `Text` is set to `fantastic`.</span></span>

<span data-ttu-id="242b6-143">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-143">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="242b6-144">Optionale Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="242b6-144">Optional parameters aren't supported.</span></span> <span data-ttu-id="242b6-145">Im folgenden Beispiel werden zwei [`@page`-Anweisungen](xref:mvc/views/razor#page) angewendet.</span><span class="sxs-lookup"><span data-stu-id="242b6-145">In the following example, two [`@page` directives](xref:mvc/views/razor#page) are applied.</span></span> <span data-ttu-id="242b6-146">Die erste Anweisung ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="242b6-146">The first directive permits navigation to the component without a parameter.</span></span> <span data-ttu-id="242b6-147">Die zweite Direktive weist der `Text`-Eigenschaft der Komponente den Wert für die `{text}`-Routenparameter zu.</span><span class="sxs-lookup"><span data-stu-id="242b6-147">The second directive assigns the `{text}` route parameter value to the component's `Text` property.</span></span>

<span data-ttu-id="242b6-148">`Pages/RouteParameter.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-148">`Pages/RouteParameter.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/RouteParameter2.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="242b6-149">Verwenden Sie [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) anstelle von [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods), um die App-Navigation zur gleichen Komponente mit einem anderen optionalen Parameterwert zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="242b6-149">Use [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) instead of [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods) to permit app navigation to the same component with a different optional parameter value.</span></span> <span data-ttu-id="242b6-150">Verwenden Sie auf der Grundlage des vorangehenden Beispiels `OnParametersSet`, wenn der Benutzer in der Lage sein soll, von `/RouteParameter` zu `/RouteParameter/amazing` oder von `/RouteParameter/amazing` zu `/RouteParameter` zu navigieren:</span><span class="sxs-lookup"><span data-stu-id="242b6-150">Based on the preceding example, use `OnParametersSet` when the user should be able to navigate from `/RouteParameter` to `/RouteParameter/amazing` or from `/RouteParameter/amazing` to `/RouteParameter`:</span></span>

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a><span data-ttu-id="242b6-151">Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="242b6-151">Route constraints</span></span>

<span data-ttu-id="242b6-152">Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="242b6-152">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="242b6-153">Im folgenden Beispiel stimmt die Route zur `User`-Komponente nur überein, wenn:</span><span class="sxs-lookup"><span data-stu-id="242b6-153">In the following example, the route to the `User` component only matches if:</span></span>

* <span data-ttu-id="242b6-154">ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-154">An `Id` route segment is present in the request URL.</span></span>
* <span data-ttu-id="242b6-155">das `Id`-Segment ein Integer (`int`) ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-155">The `Id` segment is an integer (`int`) type.</span></span>

<span data-ttu-id="242b6-156">`Pages/User.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-156">`Pages/User.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/User.razor?highlight=1)]

::: moniker-end

<span data-ttu-id="242b6-157">Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="242b6-157">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="242b6-158">Informationen zu den Routeneinschränkungen der invarianten Kultur finden Sie in der Warnung unter der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="242b6-158">For the route constraints that match the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="242b6-159">Constraint</span><span class="sxs-lookup"><span data-stu-id="242b6-159">Constraint</span></span> | <span data-ttu-id="242b6-160">Beispiel</span><span class="sxs-lookup"><span data-stu-id="242b6-160">Example</span></span>           | <span data-ttu-id="242b6-161">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="242b6-161">Example Matches</span></span>                                                                  | <span data-ttu-id="242b6-162">Invariante</span><span class="sxs-lookup"><span data-stu-id="242b6-162">Invariant</span></span><br><span data-ttu-id="242b6-163">Kultur</span><span class="sxs-lookup"><span data-stu-id="242b6-163">culture</span></span><br><span data-ttu-id="242b6-164">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="242b6-164">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="242b6-165">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="242b6-165">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="242b6-166">Nein</span><span class="sxs-lookup"><span data-stu-id="242b6-166">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="242b6-167">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="242b6-167">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="242b6-168">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-168">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="242b6-169">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="242b6-169">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="242b6-170">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-170">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="242b6-171">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="242b6-171">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="242b6-172">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-172">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="242b6-173">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="242b6-173">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="242b6-174">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-174">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="242b6-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="242b6-175">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="242b6-176">Nein</span><span class="sxs-lookup"><span data-stu-id="242b6-176">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="242b6-177">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="242b6-177">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="242b6-178">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-178">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="242b6-179">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="242b6-179">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="242b6-180">Ja</span><span class="sxs-lookup"><span data-stu-id="242b6-180">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="242b6-181">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder <xref:System.DateTime>), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="242b6-181">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="242b6-182">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-182">These constraints assume that the URL is non-localizable.</span></span>

## <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="242b6-183">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="242b6-183">Routing with URLs that contain dots</span></span>

<span data-ttu-id="242b6-184">Wenn das letzte Segment einer Anforderungs-URL einen Punkt (`.`) enthält, wird bei gehosteten Blazor WebAssembly- und Blazor Server-Apps von der serverseitigen Standardvorlage für Routen davon ausgegangen, dass eine Datei angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="242b6-184">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested.</span></span> <span data-ttu-id="242b6-185">Beispielsweise wird die URL `https://localhost.com:5001/example/some.thing` vom Router als Anforderung für eine Datei mit dem Namen `some.thing` interpretiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-185">For example, the URL `https://localhost.com:5001/example/some.thing` is interpreted by the router as a request for a file named `some.thing`.</span></span> <span data-ttu-id="242b6-186">Ohne zusätzliche Konfiguration gibt eine App die Antwort *404 – Nicht gefunden* zurück, wenn `some.thing` an eine Komponente mit einer [`@page`-Anweisung](xref:mvc/views/razor#page) weiterleiten sollte und `some.thing` ein Routenparameterwert ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-186">Without additional configuration, an app returns a *404 - Not Found* response if `some.thing` was meant to route to a component with an [`@page` directive](xref:mvc/views/razor#page) and `some.thing` is a route parameter value.</span></span> <span data-ttu-id="242b6-187">Wenn eine Route mit mindestens einem Parameter verwendet werden soll, der einen Punkt enthält, muss die Route von der App mit einer benutzerdefinierten Vorlage konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="242b6-187">To use a route with one or more parameters that contain a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="242b6-188">Sehen Sie sich z. B. die folgende `Example`-Komponente an, die einen Routenparameter aus dem letzten Segment der URL empfangen kann.</span><span class="sxs-lookup"><span data-stu-id="242b6-188">Consider the following `Example` component that can receive a route parameter from the last segment of the URL.</span></span>

<span data-ttu-id="242b6-189">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-189">`Pages/Example.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Example.razor?highlight=2)]

::: moniker-end

<span data-ttu-id="242b6-190">Wenn Sie möchten, dass die *`Server`* -App einer gehosteten Blazor WebAssembly-Lösung die Anforderung mit einem Punkt im `param`-Routenparameter weiterleiten kann, fügen Sie eine Ausweich-Dateiroutenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` befindet.</span><span class="sxs-lookup"><span data-stu-id="242b6-190">To permit the *`Server`* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` route parameter, add a fallback file route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="242b6-191">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="242b6-191">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="242b6-192">Fügen Sie eine Ausweichdatei-Routenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` befindet, um eine Blazor Server-App so zu konfigurieren, dass die Anforderung mit einem Punkt im `param`-Routenparameter weiterleitet wird.</span><span class="sxs-lookup"><span data-stu-id="242b6-192">To configure a Blazor Server app to route the request with a dot in the `param` route parameter, add a fallback page route template with the optional parameter in `Startup.Configure`.</span></span>

<span data-ttu-id="242b6-193">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="242b6-193">`Startup.cs`:</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="242b6-194">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="242b6-194">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="242b6-195">Catch-All-Routenparameter</span><span class="sxs-lookup"><span data-stu-id="242b6-195">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="242b6-196">Catch-All-Routenparameter, die Pfade über mehrere Ordnergrenzen hinweg erfassen, werden in Komponenten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="242b6-196">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span>

<span data-ttu-id="242b6-197">Für Catch-All-Routenparameter gilt:</span><span class="sxs-lookup"><span data-stu-id="242b6-197">Catch-all route parameters are:</span></span>

* <span data-ttu-id="242b6-198">Sie müssen so benannt werden, dass sie dem Routensegmentnamen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="242b6-198">Named to match the route segment name.</span></span> <span data-ttu-id="242b6-199">Die Groß-/Kleinschreibung muss bei der Benennung nicht beachtet werden.</span><span class="sxs-lookup"><span data-stu-id="242b6-199">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="242b6-200">Ein `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="242b6-200">A `string` type.</span></span> <span data-ttu-id="242b6-201">Im Framework steht keine automatische Übertragung zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="242b6-201">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="242b6-202">Am Ende der URL.</span><span class="sxs-lookup"><span data-stu-id="242b6-202">At the end of the URL.</span></span>

<span data-ttu-id="242b6-203">`Pages/CatchAll.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-203">`Pages/CatchAll.razor`:</span></span>

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/CatchAll.razor)]

<span data-ttu-id="242b6-204">Bei der URL `/catch-all/this/is/a/test` mit der Routenvorlage `/catch-all/{*pageRoute}` wird der Wert für `PageRoute` auf `this/is/a/test` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="242b6-204">For the URL `/catch-all/this/is/a/test` with a route template of `/catch-all/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="242b6-205">Schrägstriche und Segmente des erfassten Pfads werden decodiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-205">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="242b6-206">Bei der Routenvorlage `/catch-all/{*pageRoute}` hält die URL `/catch-all/this/is/a%2Ftest%2A` `this/is/a/test*` an.</span><span class="sxs-lookup"><span data-stu-id="242b6-206">For a route template of `/catch-all/{*pageRoute}`, the URL `/catch-all/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="242b6-207">Catch-All-Routenparameter werden ab ASP.NET Core 5.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="242b6-207">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span> <span data-ttu-id="242b6-208">Weitere Informationen finden Sie im Artikel zur 5.0-Version.</span><span class="sxs-lookup"><span data-stu-id="242b6-208">For more information, select the 5.0 version of this article.</span></span>

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="242b6-209">Hilfsprogramme für URI und Navigationszustand</span><span class="sxs-lookup"><span data-stu-id="242b6-209">URI and navigation state helpers</span></span>

<span data-ttu-id="242b6-210">Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um URIs und die Navigation im C#-Code zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="242b6-210">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to manage URIs and navigation in C# code.</span></span> <span data-ttu-id="242b6-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="242b6-211"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="242b6-212">Member</span><span class="sxs-lookup"><span data-stu-id="242b6-212">Member</span></span> | <span data-ttu-id="242b6-213">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="242b6-213">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="242b6-214">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="242b6-214">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="242b6-215">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="242b6-215">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="242b6-216">In der Regel entspricht <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> dem `href`-Attribut im `<base>`-Element des Dokuments in `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="242b6-216">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="242b6-217">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="242b6-217">Navigates to the specified URI.</span></span> <span data-ttu-id="242b6-218">Bei `forceLoad` lautet der Wert `true`:</span><span class="sxs-lookup"><span data-stu-id="242b6-218">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="242b6-219">Clientseitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="242b6-219">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="242b6-220">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</span><span class="sxs-lookup"><span data-stu-id="242b6-220">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="242b6-221">Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat.</span><span class="sxs-lookup"><span data-stu-id="242b6-221">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="242b6-222">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="242b6-222">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="242b6-223">Wenn ein Basis-URI (z. B. ein URI, der zuvor von <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-223">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="242b6-224">Für das <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged>-Ereignis bietet <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> die folgenden Informationen zu Navigationsereignissen:</span><span class="sxs-lookup"><span data-stu-id="242b6-224">For the <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> event, <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about navigation events:</span></span>

* <span data-ttu-id="242b6-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Die URL des neuen Speicherorts.</span><span class="sxs-lookup"><span data-stu-id="242b6-225"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="242b6-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Wenn der Wert `true` ist, hat Blazor die Navigation vom Browser abgefangen.</span><span class="sxs-lookup"><span data-stu-id="242b6-226"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="242b6-227">Wenn der Wert `false` ist, hat <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> bewirkt, dass die Navigation erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-227">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="242b6-228">Die folgende Komponente führt folgende Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="242b6-228">The following component:</span></span>

* <span data-ttu-id="242b6-229">Sie navigiert zur `Counter`-Komponente der App (`Pages/Counter.razor`), wenn die Schaltfläche mit <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> geklickt wird.</span><span class="sxs-lookup"><span data-stu-id="242b6-229">Navigates to the app's `Counter` component (`Pages/Counter.razor`) when the button is selected using <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A>.</span></span>
* <span data-ttu-id="242b6-230">Sie reagiert auf das „Location Changed“-Ereignis, indem sie <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> abonniert.</span><span class="sxs-lookup"><span data-stu-id="242b6-230">Handles the location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span>
  * <span data-ttu-id="242b6-231">Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="242b6-231">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="242b6-232">Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="242b6-232">Unhooking the method permits garbage collection of the component.</span></span>
  * <span data-ttu-id="242b6-233">Die Protokollierungsimplementierung protokolliert die folgenden Informationen, wenn die Schaltfläche geklickt wird:</span><span class="sxs-lookup"><span data-stu-id="242b6-233">The logger implementation logs the following information when the button is selected:</span></span>

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

<span data-ttu-id="242b6-234">`Pages/Navigate.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-234">`Pages/Navigate.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/Navigate.razor)]

::: moniker-end

<span data-ttu-id="242b6-235">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="242b6-235">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="242b6-236">Abfragezeichenfolgen und Analysieren von Parametern</span><span class="sxs-lookup"><span data-stu-id="242b6-236">Query string and parse parameters</span></span>

<span data-ttu-id="242b6-237">Die Abfragezeichenfolge einer Anforderung wird von der <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>-Eigenschaft abgerufen:</span><span class="sxs-lookup"><span data-stu-id="242b6-237">The query string of a request is obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType> property:</span></span>

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

<span data-ttu-id="242b6-238">So analysieren Sie die Parameter einer Abfragezeichenfolge:</span><span class="sxs-lookup"><span data-stu-id="242b6-238">To parse a query string's parameters:</span></span>

* <span data-ttu-id="242b6-239">Eine App kann die <xref:Microsoft.AspNetCore.WebUtilities>-API verwenden.</span><span class="sxs-lookup"><span data-stu-id="242b6-239">An app can use the <xref:Microsoft.AspNetCore.WebUtilities> API.</span></span> <span data-ttu-id="242b6-240">Wenn die API für die App nicht verfügbar ist, fügen Sie in der Projektdatei der App einen Paketverweis für [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) hinzu.</span><span class="sxs-lookup"><span data-stu-id="242b6-240">If the API isn't available to the app, add a package reference in the app's project file for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="242b6-241">Nach dem Analysieren der Abfragezeichenfolge mit <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> rufen Sie den Wert ab.</span><span class="sxs-lookup"><span data-stu-id="242b6-241">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="242b6-242">Im folgenden `ParseQueryString`-Komponentenbeispiel wird ein Parameterschlüssel einer Abfragezeichenfolge mit dem Namen `ship` analysiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-242">The following `ParseQueryString` component example parses a query string parameter key named `ship`.</span></span> <span data-ttu-id="242b6-243">Das Schlüssel-Wert-Paar für die URL-Abfragezeichenfolge `?ship=Tardis` erfasst z. B. den Wert `Tardis` in `queryValue`.</span><span class="sxs-lookup"><span data-stu-id="242b6-243">For example, the URL query string key-value pair `?ship=Tardis` captures the value `Tardis` in `queryValue`.</span></span> <span data-ttu-id="242b6-244">Navigieren Sie im folgenden Beispiel mit der URL `https://localhost:5001/parse-query-string?ship=Tardis` zur App.</span><span class="sxs-lookup"><span data-stu-id="242b6-244">For the following example, navigate to the app with the URL `https://localhost:5001/parse-query-string?ship=Tardis`.</span></span>

<span data-ttu-id="242b6-245">`Pages/ParseQueryString.razor`:</span><span class="sxs-lookup"><span data-stu-id="242b6-245">`Pages/ParseQueryString.razor`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Pages/routing/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a><span data-ttu-id="242b6-246">`NavLink`- und `NavMenu`-Komponenten</span><span class="sxs-lookup"><span data-stu-id="242b6-246">`NavLink` and `NavMenu` components</span></span>

<span data-ttu-id="242b6-247">Verwenden Sie bei der Erstellung von Navigationslinks eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="242b6-247">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="242b6-248">Eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="242b6-248">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="242b6-249">Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="242b6-249">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="242b6-250">Optional können Sie <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> einen CSS-Klassennamen zuweisen, um eine benutzerdefinierte CSS-Klasse auf den gerenderten Link anzuwenden, wenn die aktuelle Route mit `href` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="242b6-250">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="242b6-251">Die folgende `NavMenu`-Komponente erstellt eine [`Bootstrap`](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="242b6-251">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/5.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](~/blazor/common/samples/3.x/BlazorSample_WebAssembly/Shared/routing/NavMenu.razor?name=snippet&highlight=4,9)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="242b6-252">Die `NavMenu`-Komponente (`NavMenu.razor`) wird im Ordner `Shared` einer App bereitgestellt, die aus den Blazor-Projektvorlagen generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="242b6-252">The `NavMenu` component (`NavMenu.razor`) is provided in the `Shared` folder of an app generated from the Blazor project templates.</span></span>

<span data-ttu-id="242b6-253">Es gibt zwei <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="242b6-253">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="242b6-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="242b6-254"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="242b6-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*Standardwert*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn ein Präfix mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="242b6-255"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="242b6-256">Im vorherigen Beispiel stimmt die Startseite <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="242b6-256">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="242b6-257">Die zweite <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `component`-Präfix aufruft (z. B. `https://localhost:5001/component` und `https://localhost:5001/component/another-segment`).</span><span class="sxs-lookup"><span data-stu-id="242b6-257">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `component` prefix (for example, `https://localhost:5001/component` and `https://localhost:5001/component/another-segment`).</span></span>

<span data-ttu-id="242b6-258">Zusätzliche <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponentenattribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="242b6-258">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="242b6-259">Im folgenden Beispiel schließt die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente das `target`-Attribut ein:</span><span class="sxs-lookup"><span data-stu-id="242b6-259">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

<span data-ttu-id="242b6-260">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="242b6-260">The following HTML markup is rendered:</span></span>

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> <span data-ttu-id="242b6-261">Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von `NavLink`-Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente (`NavLink`) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="242b6-261">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> <span data-ttu-id="242b6-262">Die Verwendung einer Indexvariable in diesem Szenario ist eine Anforderung für **jede** untergeordnete Komponente, die eine Schleifenvariable im [untergeordneten Inhalt](xref:blazor/components/index#child-content) verwendet, nicht nur für die `NavLink`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="242b6-262">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="242b6-263">Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:</span><span class="sxs-lookup"><span data-stu-id="242b6-263">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="242b6-264">Integration von ASP.NET Core-Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="242b6-264">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="242b6-265">*Dieser Abschnitt gilt nur für Blazor Server-Apps.*</span><span class="sxs-lookup"><span data-stu-id="242b6-265">*This section only applies to Blazor Server apps.*</span></span>

<span data-ttu-id="242b6-266">Blazor Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert.</span><span class="sxs-lookup"><span data-stu-id="242b6-266">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="242b6-267">Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-267">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`.</span></span>

<span data-ttu-id="242b6-268">`Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="242b6-268">`Startup.cs`:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/5.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](~/blazor/common/samples/3.x/BlazorSample_Server/routing/Startup.cs?name=snippet&highlight=5)]

::: moniker-end

<span data-ttu-id="242b6-269">Die typische Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="242b6-269">The typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="242b6-270">Gemäß der Konvention wird die *Hostseite* in der Regel im Ordner `Pages` der App mit `_Host.cshtml` benannt.</span><span class="sxs-lookup"><span data-stu-id="242b6-270">By convention, the *host* page is usually named `_Host.cshtml` in the `Pages` folder of the app.</span></span>

<span data-ttu-id="242b6-271">Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="242b6-271">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="242b6-272">Die Ausweichroute wird verwendet, wenn andere Routen nicht passen.</span><span class="sxs-lookup"><span data-stu-id="242b6-272">The fallback route is used when other routes don't match.</span></span> <span data-ttu-id="242b6-273">Dadurch kann die App andere Controller und Seiten verwenden, ohne das Komponentenrouting in der Blazor Server-App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="242b6-273">This allows the app to use other controllers and pages without interfering with component routing in the Blazor Server app.</span></span>

<span data-ttu-id="242b6-274">Informationen zum Konfigurieren von <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> für das Serverhosting an einer Nicht-Stamm-URL finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="242b6-274">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>
