---
title: ASP.NET Core Blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie Anforderungen in Apps und über die NavLink-Komponente weiterleiten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/02/2020
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
ms.openlocfilehash: 8f0aa80d092b6678131a2b7152f21ecb8e168257
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430990"
---
# <a name="aspnet-core-no-locblazor-routing"></a><span data-ttu-id="171f1-103">ASP.NET Core Blazor-Routing</span><span class="sxs-lookup"><span data-stu-id="171f1-103">ASP.NET Core Blazor routing</span></span>

<span data-ttu-id="171f1-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="171f1-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="171f1-105">Erfahren Sie, wie Sie Anforderungen weiterleiten und die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verwenden, um Navigationslinks in Blazor-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="171f1-105">Learn how to route requests and how to use the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component to create navigation links in Blazor apps.</span></span>

## <a name="aspnet-core-endpoint-routing-integration"></a><span data-ttu-id="171f1-106">Integration von ASP.NET Core-Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="171f1-106">ASP.NET Core endpoint routing integration</span></span>

<span data-ttu-id="171f1-107">Blazor Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert.</span><span class="sxs-lookup"><span data-stu-id="171f1-107">Blazor Server is integrated into [ASP.NET Core Endpoint Routing](xref:fundamentals/routing).</span></span> <span data-ttu-id="171f1-108">Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="171f1-108">An ASP.NET Core app is configured to accept incoming connections for interactive components with <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure`:</span></span>

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

<span data-ttu-id="171f1-109">Die typischste Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert.</span><span class="sxs-lookup"><span data-stu-id="171f1-109">The most typical configuration is to route all requests to a Razor page, which acts as the host for the server-side part of the Blazor Server app.</span></span> <span data-ttu-id="171f1-110">Gemäß der Konvention wird die *Host*-Seite normalerweise `_Host.cshtml` genannt.</span><span class="sxs-lookup"><span data-stu-id="171f1-110">By convention, the *host* page is usually named `_Host.cshtml`.</span></span> <span data-ttu-id="171f1-111">Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet.</span><span class="sxs-lookup"><span data-stu-id="171f1-111">The route specified in the host file is called a *fallback route* because it operates with a low priority in route matching.</span></span> <span data-ttu-id="171f1-112">Die Fallbackroute wird verwendet, wenn andere Routen nicht passen.</span><span class="sxs-lookup"><span data-stu-id="171f1-112">The fallback route is considered when other routes don't match.</span></span> <span data-ttu-id="171f1-113">Dadurch kann die App andere Controller und Seiten verwenden, ohne die Blazor Server-App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="171f1-113">This allows the app to use others controllers and pages without interfering with the Blazor Server app.</span></span>

<span data-ttu-id="171f1-114">Informationen zum Konfigurieren von <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> für das Serverhosting an einer Nicht-Stamm-URL finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="171f1-114">For information on configuring <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> for non-root URL server hosting, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="route-templates"></a><span data-ttu-id="171f1-115">Routenvorlagen</span><span class="sxs-lookup"><span data-stu-id="171f1-115">Route templates</span></span>

<span data-ttu-id="171f1-116">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht das Routing zu jeder Komponente mit einer bestimmten Route.</span><span class="sxs-lookup"><span data-stu-id="171f1-116">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component enables routing to each component with a specified route.</span></span> <span data-ttu-id="171f1-117">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente wird in der `App.razor`-Datei angezeigt:</span><span class="sxs-lookup"><span data-stu-id="171f1-117">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component appears in the `App.razor` file:</span></span>

```razor
<Router AppAssembly="@typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

<span data-ttu-id="171f1-118">Wenn eine `.razor`-Datei mit einer `@page`-Anweisung kompiliert wird, erhält die generierte Klasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Element, das die Routenvorlage angibt.</span><span class="sxs-lookup"><span data-stu-id="171f1-118">When a `.razor` file with an `@page` directive is compiled, the generated class is provided a <xref:Microsoft.AspNetCore.Components.RouteAttribute> specifying the route template.</span></span>

<span data-ttu-id="171f1-119">Zur Laufzeit führt die <xref:Microsoft.AspNetCore.Components.RouteView>-Komponente Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="171f1-119">At runtime, the <xref:Microsoft.AspNetCore.Components.RouteView> component:</span></span>

* <span data-ttu-id="171f1-120">Sie empfängt das <xref:Microsoft.AspNetCore.Components.RouteData>-Element aus dem <xref:Microsoft.AspNetCore.Components.Routing.Router>-Element zusammen mit den gewünschten Parametern.</span><span class="sxs-lookup"><span data-stu-id="171f1-120">Receives the <xref:Microsoft.AspNetCore.Components.RouteData> from the <xref:Microsoft.AspNetCore.Components.Routing.Router> along with any desired parameters.</span></span>
* <span data-ttu-id="171f1-121">Sie rendert die angegebene Komponente mit ihrem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.</span><span class="sxs-lookup"><span data-stu-id="171f1-121">Renders the specified component with its layout (or an optional default layout) using the specified parameters.</span></span>

<span data-ttu-id="171f1-122">Optional können Sie einen <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout>-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout festlegen.</span><span class="sxs-lookup"><span data-stu-id="171f1-122">You can optionally specify a <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout> parameter with a layout class to use for components that don't specify a layout.</span></span> <span data-ttu-id="171f1-123">In den Blazor-Standardvorlagen wird die `MainLayout`-Komponente angegeben.</span><span class="sxs-lookup"><span data-stu-id="171f1-123">The default Blazor templates specify the `MainLayout` component.</span></span> <span data-ttu-id="171f1-124">`MainLayout.razor` befindet sich im Ordner `Shared` des Vorlagenprojekts.</span><span class="sxs-lookup"><span data-stu-id="171f1-124">`MainLayout.razor` is in the template project's `Shared` folder.</span></span> <span data-ttu-id="171f1-125">Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="171f1-125">For more information on layouts, see <xref:blazor/layouts>.</span></span>

<span data-ttu-id="171f1-126">Mehrere Routenvorlagen können auf eine Komponente angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="171f1-126">Multiple route templates can be applied to a component.</span></span> <span data-ttu-id="171f1-127">Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:</span><span class="sxs-lookup"><span data-stu-id="171f1-127">The following component responds to requests for `/BlazorRoute` and `/DifferentBlazorRoute`:</span></span>

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> <span data-ttu-id="171f1-128">Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) mit dem im `href`-Attribut (`<base href="/">`) angegebenen App-Basispfad enthalten.</span><span class="sxs-lookup"><span data-stu-id="171f1-128">For URLs to resolve correctly, the app must include a `<base>` tag in its `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server) with the app base path specified in the `href` attribute (`<base href="/">`).</span></span> <span data-ttu-id="171f1-129">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="171f1-129">For more information, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

## <a name="provide-custom-content-when-content-isnt-found"></a><span data-ttu-id="171f1-130">Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde</span><span class="sxs-lookup"><span data-stu-id="171f1-130">Provide custom content when content isn't found</span></span>

<span data-ttu-id="171f1-131">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="171f1-131">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component allows the app to specify custom content if content isn't found for the requested route.</span></span>

<span data-ttu-id="171f1-132">Legen Sie in der Datei `App.razor` den benutzerdefinierten Inhalt im <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Vorlagenparameter der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fest:</span><span class="sxs-lookup"><span data-stu-id="171f1-132">In the `App.razor` file, set custom content in the <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> template parameter of the <xref:Microsoft.AspNetCore.Components.Routing.Router> component:</span></span>

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

<span data-ttu-id="171f1-133">Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="171f1-133">The content of `<NotFound>` tags can include arbitrary items, such as other interactive components.</span></span> <span data-ttu-id="171f1-134">Informationen zum Anwenden eines Standardlayouts auf <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalte finden Sie unter <xref:blazor/layouts>.</span><span class="sxs-lookup"><span data-stu-id="171f1-134">To apply a default layout to <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, see <xref:blazor/layouts>.</span></span>

## <a name="route-to-components-from-multiple-assemblies"></a><span data-ttu-id="171f1-135">Weiterleiten an Komponenten aus mehreren Assemblys</span><span class="sxs-lookup"><span data-stu-id="171f1-135">Route to components from multiple assemblies</span></span>

<span data-ttu-id="171f1-136">Verwenden Sie den <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Parameter, um zusätzliche Assemblys anzugeben, die die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll.</span><span class="sxs-lookup"><span data-stu-id="171f1-136">Use the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> parameter to specify additional assemblies for the <xref:Microsoft.AspNetCore.Components.Routing.Router> component to consider when searching for routable components.</span></span> <span data-ttu-id="171f1-137">Angegebene Assemblys werden zusätzlich zur mit `AppAssembly` angegebenen Assembly berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="171f1-137">Specified assemblies are considered in addition to the `AppAssembly`-specified assembly.</span></span> <span data-ttu-id="171f1-138">Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer referenzierten Klassenbibliothek definiert ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-138">In the following example, `Component1` is a routable component defined in a referenced class library.</span></span> <span data-ttu-id="171f1-139">Im folgenden Beispiel zu <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> wird die Routingunterstützung für `Component1` ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="171f1-139">The following <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> example results in routing support for `Component1`:</span></span>

```razor
<Router
    AppAssembly="@typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a><span data-ttu-id="171f1-140">Routenparameter</span><span class="sxs-lookup"><span data-stu-id="171f1-140">Route parameters</span></span>

<span data-ttu-id="171f1-141">Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter mit demselben Namen (mit Beachtung der Groß-/Kleinschreibung) aufzufüllen:</span><span class="sxs-lookup"><span data-stu-id="171f1-141">The router uses route parameters to populate the corresponding component parameters with the same name (case insensitive):</span></span>

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

<span data-ttu-id="171f1-142">Optionale Parameter werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="171f1-142">Optional parameters aren't supported.</span></span> <span data-ttu-id="171f1-143">Im vorherigen Beispiel werden zwei `@page`-Anweisungen angewendet.</span><span class="sxs-lookup"><span data-stu-id="171f1-143">Two `@page` directives are applied in the previous example.</span></span> <span data-ttu-id="171f1-144">Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter.</span><span class="sxs-lookup"><span data-stu-id="171f1-144">The first permits navigation to the component without a parameter.</span></span> <span data-ttu-id="171f1-145">Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.</span><span class="sxs-lookup"><span data-stu-id="171f1-145">The second `@page` directive takes the `{text}` route parameter and assigns the value to the `Text` property.</span></span>

## <a name="route-constraints"></a><span data-ttu-id="171f1-146">Routeneinschränkungen</span><span class="sxs-lookup"><span data-stu-id="171f1-146">Route constraints</span></span>

<span data-ttu-id="171f1-147">Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.</span><span class="sxs-lookup"><span data-stu-id="171f1-147">A route constraint enforces type matching on a route segment to a component.</span></span>

<span data-ttu-id="171f1-148">Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur überein, wenn:</span><span class="sxs-lookup"><span data-stu-id="171f1-148">In the following example, the route to the `Users` component only matches if:</span></span>

* <span data-ttu-id="171f1-149">Ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-149">An `Id` route segment is present on the request URL.</span></span>
* <span data-ttu-id="171f1-150">Das `Id`-Segment eine Ganzzahl (`int`) ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-150">The `Id` segment is an integer (`int`).</span></span>

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

<span data-ttu-id="171f1-151">Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar.</span><span class="sxs-lookup"><span data-stu-id="171f1-151">The route constraints shown in the following table are available.</span></span> <span data-ttu-id="171f1-152">Informationen zu den Routeneinschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="171f1-152">For the route constraints that match with the invariant culture, see the warning below the table for more information.</span></span>

| <span data-ttu-id="171f1-153">Constraint</span><span class="sxs-lookup"><span data-stu-id="171f1-153">Constraint</span></span> | <span data-ttu-id="171f1-154">Beispiel</span><span class="sxs-lookup"><span data-stu-id="171f1-154">Example</span></span>           | <span data-ttu-id="171f1-155">Beispiele für Übereinstimmungen</span><span class="sxs-lookup"><span data-stu-id="171f1-155">Example Matches</span></span>                                                                  | <span data-ttu-id="171f1-156">Invariante</span><span class="sxs-lookup"><span data-stu-id="171f1-156">Invariant</span></span><br><span data-ttu-id="171f1-157">Kultur</span><span class="sxs-lookup"><span data-stu-id="171f1-157">culture</span></span><br><span data-ttu-id="171f1-158">Übereinstimmend</span><span class="sxs-lookup"><span data-stu-id="171f1-158">matching</span></span> |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | <span data-ttu-id="171f1-159">`true`, `FALSE`</span><span class="sxs-lookup"><span data-stu-id="171f1-159">`true`, `FALSE`</span></span>                                                                  | <span data-ttu-id="171f1-160">Nein</span><span class="sxs-lookup"><span data-stu-id="171f1-160">No</span></span>                               |
| `datetime` | `{dob:datetime}`  | <span data-ttu-id="171f1-161">`2016-12-31`, `2016-12-31 7:32pm`</span><span class="sxs-lookup"><span data-stu-id="171f1-161">`2016-12-31`, `2016-12-31 7:32pm`</span></span>                                                | <span data-ttu-id="171f1-162">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-162">Yes</span></span>                              |
| `decimal`  | `{price:decimal}` | <span data-ttu-id="171f1-163">`49.99`, `-1,000.01`</span><span class="sxs-lookup"><span data-stu-id="171f1-163">`49.99`, `-1,000.01`</span></span>                                                             | <span data-ttu-id="171f1-164">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-164">Yes</span></span>                              |
| `double`   | `{weight:double}` | <span data-ttu-id="171f1-165">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="171f1-165">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="171f1-166">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-166">Yes</span></span>                              |
| `float`    | `{weight:float}`  | <span data-ttu-id="171f1-167">`1.234`, `-1,001.01e8`</span><span class="sxs-lookup"><span data-stu-id="171f1-167">`1.234`, `-1,001.01e8`</span></span>                                                           | <span data-ttu-id="171f1-168">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-168">Yes</span></span>                              |
| `guid`     | `{id:guid}`       | <span data-ttu-id="171f1-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span><span class="sxs-lookup"><span data-stu-id="171f1-169">`CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}`</span></span> | <span data-ttu-id="171f1-170">Nein</span><span class="sxs-lookup"><span data-stu-id="171f1-170">No</span></span>                               |
| `int`      | `{id:int}`        | <span data-ttu-id="171f1-171">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="171f1-171">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="171f1-172">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-172">Yes</span></span>                              |
| `long`     | `{ticks:long}`    | <span data-ttu-id="171f1-173">`123456789`, `-123456789`</span><span class="sxs-lookup"><span data-stu-id="171f1-173">`123456789`, `-123456789`</span></span>                                                        | <span data-ttu-id="171f1-174">Ja</span><span class="sxs-lookup"><span data-stu-id="171f1-174">Yes</span></span>                              |

> [!WARNING]
> <span data-ttu-id="171f1-175">Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder <xref:System.DateTime>), wird immer die invariante Kultur verwendet.</span><span class="sxs-lookup"><span data-stu-id="171f1-175">Route constraints that verify the URL and are converted to a CLR type (such as `int` or <xref:System.DateTime>) always use the invariant culture.</span></span> <span data-ttu-id="171f1-176">Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-176">These constraints assume that the URL is non-localizable.</span></span>

### <a name="routing-with-urls-that-contain-dots"></a><span data-ttu-id="171f1-177">Routing mit URLs, die Punkte enthalten</span><span class="sxs-lookup"><span data-stu-id="171f1-177">Routing with URLs that contain dots</span></span>

<span data-ttu-id="171f1-178">Wenn das letzte Segment einer Anforderungs-URL einen Punkt (`.`) enthält (z. B. `https://localhost.com:5001/example/some.thing`), wird bei gehosteten Blazor WebAssembly- und Blazor Server-Apps von der serverseitigen Standardvorlage für Routen davon ausgegangen, dass eine Datei angefordert wird.</span><span class="sxs-lookup"><span data-stu-id="171f1-178">For hosted Blazor WebAssembly and Blazor Server apps, the server-side default route template assumes that if the last segment of a request URL contains a dot (`.`) that a file is requested (for example, `https://localhost.com:5001/example/some.thing`).</span></span> <span data-ttu-id="171f1-179">Ohne zusätzliche Konfiguration gibt eine App eine *404 – Nicht gefunden*-Antwort zurück, wenn damit an eine Komponente weitergeleitet werden soll.</span><span class="sxs-lookup"><span data-stu-id="171f1-179">Without additional configuration, an app returns a *404 - Not Found* response if this was meant to route to a component.</span></span> <span data-ttu-id="171f1-180">Wenn eine Route mit einem Parameter verwendet werden soll, der einen Punkt enthält, muss die Route von der App mit einer benutzerdefinierten Vorlage konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="171f1-180">To use a route with one or more parameters that contains a dot, the app must configure the route with a custom template.</span></span>

<span data-ttu-id="171f1-181">Betrachten Sie die folgende `Example`-Komponente, die einen Routenparameter aus dem letzten Segment der URL empfangen kann:</span><span class="sxs-lookup"><span data-stu-id="171f1-181">Consider the following `Example` component that can receive a route parameter from the last segment of the URL:</span></span>

```razor
@page "/example"
@page "/example/{param}"

<p>
    Param: @Param
</p>

@code {
    [Parameter]
    public string Param { get; set; }
}
```

<span data-ttu-id="171f1-182">Wenn Sie möchten, dass die *Server*-App einer gehosteten Blazor WebAssembly-Lösung die Anforderung mit einem Punkt im `param`-Parameter weiterleiten können soll, fügen Sie eine Ausweichdateiroutenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` (`Startup.cs`) befindet:</span><span class="sxs-lookup"><span data-stu-id="171f1-182">To permit the *Server* app of a hosted Blazor WebAssembly solution to route the request with a dot in the `param` parameter, add a fallback file route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

<span data-ttu-id="171f1-183">Um eine Blazor Server-App so zu konfigurieren, dass die Anforderung mit einem Punkt im `param`-Parameter weiterleitet wird, fügen Sie eine Ausweichdateiroutenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` (`Startup.cs`) befindet:</span><span class="sxs-lookup"><span data-stu-id="171f1-183">To configure a Blazor Server app to route the request with a dot in the `param` parameter, add a fallback page route template with the optional parameter in `Startup.Configure` (`Startup.cs`):</span></span>

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

<span data-ttu-id="171f1-184">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="171f1-184">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="catch-all-route-parameters"></a><span data-ttu-id="171f1-185">Catch-All-Routenparameter</span><span class="sxs-lookup"><span data-stu-id="171f1-185">Catch-all route parameters</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="171f1-186">*Dieser Abschnitt gilt für ASP.NET Core in .NET 5 Release Candidate 1 (RC1) oder höher.*</span><span class="sxs-lookup"><span data-stu-id="171f1-186">*This section applies to ASP.NET Core in .NET 5 Release Candidate 1 (RC1) or later.*</span></span>

<span data-ttu-id="171f1-187">Catch-All-Routenparameter, die Pfade über mehrere Ordnergrenzen hinweg erfassen, werden in Komponenten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="171f1-187">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="171f1-188">Für die Catch-All-Routenparameter gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="171f1-188">The catch-all route parameter must be:</span></span>

* <span data-ttu-id="171f1-189">Sie müssen so benannt werden, dass sie dem Routensegmentnamen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="171f1-189">Named to match the route segment name.</span></span> <span data-ttu-id="171f1-190">Die Groß-/Kleinschreibung muss bei der Benennung nicht beachtet werden.</span><span class="sxs-lookup"><span data-stu-id="171f1-190">Naming isn't case sensitive.</span></span>
* <span data-ttu-id="171f1-191">Ein `string`-Typ.</span><span class="sxs-lookup"><span data-stu-id="171f1-191">A `string` type.</span></span> <span data-ttu-id="171f1-192">Im Framework steht keine automatische Übertragung zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="171f1-192">The framework doesn't provide automatic casting.</span></span>
* <span data-ttu-id="171f1-193">Am Ende der URL.</span><span class="sxs-lookup"><span data-stu-id="171f1-193">At the end of the URL.</span></span>

```razor
@page "/page/{*pageRoute}"

@code {
    [Parameter]
    public string PageRoute { get; set; }
}
```

<span data-ttu-id="171f1-194">Bei der URL `/page/this/is/a/test` mit der Routenvorlage `/page/{*pageRoute}` wird der Wert für `PageRoute` auf `this/is/a/test` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="171f1-194">For the URL `/page/this/is/a/test` with a route template of `/page/{*pageRoute}`, the value of `PageRoute` is set to `this/is/a/test`.</span></span>

<span data-ttu-id="171f1-195">Schrägstriche und Segmente des erfassten Pfads werden decodiert.</span><span class="sxs-lookup"><span data-stu-id="171f1-195">Slashes and segments of the captured path are decoded.</span></span> <span data-ttu-id="171f1-196">Bei der Routenvorlage `/page/{*pageRoute}` hält die URL `/page/this/is/a%2Ftest%2A` `this/is/a/test*` an.</span><span class="sxs-lookup"><span data-stu-id="171f1-196">For a route template of `/page/{*pageRoute}`, the URL `/page/this/is/a%2Ftest%2A` yields `this/is/a/test*`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="171f1-197">Catch-All-Routenparameter werden ab ASP.NET Core 5.0 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="171f1-197">Catch-all route parameters are supported in ASP.NET Core 5.0 or later.</span></span>

::: moniker-end

## <a name="navlink-component"></a><span data-ttu-id="171f1-198">NavLink-Komponente</span><span class="sxs-lookup"><span data-stu-id="171f1-198">NavLink component</span></span>

<span data-ttu-id="171f1-199">Verwenden Sie bei der Erstellung von Navigationslinks eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`).</span><span class="sxs-lookup"><span data-stu-id="171f1-199">Use a <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component in place of HTML hyperlink elements (`<a>`) when creating navigation links.</span></span> <span data-ttu-id="171f1-200">Eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="171f1-200">A <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component behaves like an `<a>` element, except it toggles an `active` CSS class based on whether its `href` matches the current URL.</span></span> <span data-ttu-id="171f1-201">Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-201">The `active` class helps a user understand which page is the active page among the navigation links displayed.</span></span> <span data-ttu-id="171f1-202">Optional können Sie <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> einen CSS-Klassennamen zuweisen, um eine benutzerdefinierte CSS-Klasse auf den gerenderten Link anzuwenden, wenn die aktuelle Route mit `href` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="171f1-202">Optionally, assign a CSS class name to <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> to apply a custom CSS class to the rendered link when the current route matches the `href`.</span></span>

<span data-ttu-id="171f1-203">Die folgende `NavMenu`-Komponente erstellt eine [`Bootstrap`](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="171f1-203">The following `NavMenu` component creates a [`Bootstrap`](https://getbootstrap.com/docs/) navigation bar that demonstrates how to use <xref:Microsoft.AspNetCore.Components.Routing.NavLink> components:</span></span>

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

<span data-ttu-id="171f1-204">Es gibt zwei <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:</span><span class="sxs-lookup"><span data-stu-id="171f1-204">There are two <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch> options that you can assign to the `Match` attribute of the `<NavLink>` element:</span></span>

* <span data-ttu-id="171f1-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="171f1-205"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches the entire current URL.</span></span>
* <span data-ttu-id="171f1-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*Standardwert*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn ein Präfix mit der aktuellen URL übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="171f1-206"><xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*default*): The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> is active when it matches any prefix of the current URL.</span></span>

<span data-ttu-id="171f1-207">Im vorherigen Beispiel stimmt die Startseite <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`).</span><span class="sxs-lookup"><span data-stu-id="171f1-207">In the preceding example, the Home <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` matches the home URL and only receives the `active` CSS class at the app's default base path URL (for example, `https://localhost:5001/`).</span></span> <span data-ttu-id="171f1-208">Die zweite <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `MyComponent`-Präfix aufruft (z. B. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`).</span><span class="sxs-lookup"><span data-stu-id="171f1-208">The second <xref:Microsoft.AspNetCore.Components.Routing.NavLink> receives the `active` class when the user visits any URL with a `MyComponent` prefix (for example, `https://localhost:5001/MyComponent` and `https://localhost:5001/MyComponent/AnotherSegment`).</span></span>

<span data-ttu-id="171f1-209">Zusätzliche <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponentenattribute werden an das gerenderte Ankertag weitergegeben.</span><span class="sxs-lookup"><span data-stu-id="171f1-209">Additional <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component attributes are passed through to the rendered anchor tag.</span></span> <span data-ttu-id="171f1-210">Im folgenden Beispiel schließt die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente das `target`-Attribut ein:</span><span class="sxs-lookup"><span data-stu-id="171f1-210">In the following example, the <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component includes the `target` attribute:</span></span>

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

<span data-ttu-id="171f1-211">Das folgende HTML-Markup wird gerendert:</span><span class="sxs-lookup"><span data-stu-id="171f1-211">The following HTML markup is rendered:</span></span>

```html
<a href="my-page" target="_blank">My page</a>
```

> [!WARNING]
> <span data-ttu-id="171f1-212">Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von `NavLink`-Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente (`NavLink`) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="171f1-212">Due to the way that Blazor renders child content, rendering `NavLink` components inside a `for` loop requires a local index variable if the incrementing loop variable is used in the `NavLink` (child) component's content:</span></span>
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
> <span data-ttu-id="171f1-213">Die Verwendung einer Indexvariable in diesem Szenario ist eine Anforderung für **jede** untergeordnete Komponente, die eine Schleifenvariable im [untergeordneten Inhalt](xref:blazor/components/index#child-content) verwendet, nicht nur für die `NavLink`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="171f1-213">Using an index variable in this scenario is a requirement for **any** child component that uses a loop variable in its [child content](xref:blazor/components/index#child-content), not just the `NavLink` component.</span></span>
>
> <span data-ttu-id="171f1-214">Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:</span><span class="sxs-lookup"><span data-stu-id="171f1-214">Alternatively, use a `foreach` loop with <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType>:</span></span>
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

## <a name="uri-and-navigation-state-helpers"></a><span data-ttu-id="171f1-215">Hilfsprogramme für URI und Navigationszustand</span><span class="sxs-lookup"><span data-stu-id="171f1-215">URI and navigation state helpers</span></span>

<span data-ttu-id="171f1-216">Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um mit URIs und Navigationselementen in C#-Code zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="171f1-216">Use <xref:Microsoft.AspNetCore.Components.NavigationManager> to work with URIs and navigation in C# code.</span></span> <span data-ttu-id="171f1-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="171f1-217"><xref:Microsoft.AspNetCore.Components.NavigationManager> provides the event and methods shown in the following table.</span></span>

| <span data-ttu-id="171f1-218">Member</span><span class="sxs-lookup"><span data-stu-id="171f1-218">Member</span></span> | <span data-ttu-id="171f1-219">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="171f1-219">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | <span data-ttu-id="171f1-220">Ruft den aktuellen absoluten URI ab.</span><span class="sxs-lookup"><span data-stu-id="171f1-220">Gets the current absolute URI.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | <span data-ttu-id="171f1-221">Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="171f1-221">Gets the base URI (with a trailing slash) that can be prepended to relative URI paths to produce an absolute URI.</span></span> <span data-ttu-id="171f1-222">In der Regel entspricht <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> dem `href`-Attribut im `<base>`-Element des Dokuments in `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server).</span><span class="sxs-lookup"><span data-stu-id="171f1-222">Typically, <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> corresponds to the `href` attribute on the document's `<base>` element in `wwwroot/index.html` (Blazor WebAssembly) or `Pages/_Host.cshtml` (Blazor Server).</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | <span data-ttu-id="171f1-223">Navigiert zum angegebenen URI.</span><span class="sxs-lookup"><span data-stu-id="171f1-223">Navigates to the specified URI.</span></span> <span data-ttu-id="171f1-224">Bei `forceLoad` lautet der Wert `true`:</span><span class="sxs-lookup"><span data-stu-id="171f1-224">If `forceLoad` is `true`:</span></span><ul><li><span data-ttu-id="171f1-225">Clientseitiges Routing wird umgangen.</span><span class="sxs-lookup"><span data-stu-id="171f1-225">Client-side routing is bypassed.</span></span></li><li><span data-ttu-id="171f1-226">Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</span><span class="sxs-lookup"><span data-stu-id="171f1-226">The browser is forced to load the new page from the server, whether or not the URI is normally handled by the client-side router.</span></span></li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | <span data-ttu-id="171f1-227">Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat.</span><span class="sxs-lookup"><span data-stu-id="171f1-227">An event that fires when the navigation location has changed.</span></span> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | <span data-ttu-id="171f1-228">Konvertiert einen relativen URI in einen absoluten URI.</span><span class="sxs-lookup"><span data-stu-id="171f1-228">Converts a relative URI into an absolute URI.</span></span> |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | <span data-ttu-id="171f1-229">Wenn ein Basis-URI (z. B. ein URI, der zuvor von <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert.</span><span class="sxs-lookup"><span data-stu-id="171f1-229">Given a base URI (for example, a URI previously returned by <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri>), converts an absolute URI into a URI relative to the base URI prefix.</span></span> |

<span data-ttu-id="171f1-230">Die folgende Komponente navigiert zu der `Counter`-Komponente der App, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="171f1-230">The following component navigates to the app's `Counter` component when the button is selected:</span></span>

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

<span data-ttu-id="171f1-231">Die folgende Komponente verarbeitet ein Speicherortwechselereignis, indem <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> abonniert wird.</span><span class="sxs-lookup"><span data-stu-id="171f1-231">The following component handles a location changed event by subscribing to <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType>.</span></span> <span data-ttu-id="171f1-232">Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="171f1-232">The `HandleLocationChanged` method is unhooked when `Dispose` is called by the framework.</span></span> <span data-ttu-id="171f1-233">Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="171f1-233">Unhooking the method permits garbage collection of the component.</span></span>

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<span data-ttu-id="171f1-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> stellt die folgenden Informationen zum Ereignis bereit:</span><span class="sxs-lookup"><span data-stu-id="171f1-234"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> provides the following information about the event:</span></span>

* <span data-ttu-id="171f1-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Die URL des neuen Speicherorts.</span><span class="sxs-lookup"><span data-stu-id="171f1-235"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: The URL of the new location.</span></span>
* <span data-ttu-id="171f1-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Wenn der Wert `true` ist, hat Blazor die Navigation vom Browser abgefangen.</span><span class="sxs-lookup"><span data-stu-id="171f1-236"><xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: If `true`, Blazor intercepted the navigation from the browser.</span></span> <span data-ttu-id="171f1-237">Wenn der Wert `false` ist, hat <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> bewirkt, dass die Navigation erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="171f1-237">If `false`, <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> caused the navigation to occur.</span></span>

<span data-ttu-id="171f1-238">Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="171f1-238">For more information on component disposal, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

## <a name="query-string-and-parse-parameters"></a><span data-ttu-id="171f1-239">Abfragezeichenfolgen und Analysieren von Parametern</span><span class="sxs-lookup"><span data-stu-id="171f1-239">Query string and parse parameters</span></span>

<span data-ttu-id="171f1-240">Die Abfragezeichenfolge einer Anforderung kann aus der <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri>-Eigenschaft von <xref:Microsoft.AspNetCore.Components.NavigationManager> abgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="171f1-240">The query string of a request can be obtained from the <xref:Microsoft.AspNetCore.Components.NavigationManager>'s <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> property:</span></span>

```razor
@inject NavigationManager Navigation

...

var query = new Uri(Navigation.Uri).Query;
```

<span data-ttu-id="171f1-241">So analysieren Sie die Parameter einer Abfragezeichenfolge:</span><span class="sxs-lookup"><span data-stu-id="171f1-241">To parse a query string's parameters:</span></span>

* <span data-ttu-id="171f1-242">Fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) hinzu.</span><span class="sxs-lookup"><span data-stu-id="171f1-242">Add a package reference for [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities).</span></span>
* <span data-ttu-id="171f1-243">Nach dem Analysieren der Abfragezeichenfolge mit <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> rufen Sie den Wert ab.</span><span class="sxs-lookup"><span data-stu-id="171f1-243">Obtain the value after parsing the query string with <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType>.</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.WebUtilities
@inject NavigationManager NavigationManager

<h1>Query string parse example</h1>

<p>Value: @queryValue</p>

@code {
    private string queryValue = "Not set";

    protected override void OnInitialized()
    {
        var query = new Uri(NavigationManager.Uri).Query;

        if (QueryHelpers.ParseQuery(query).TryGetValue("{KEY}", out var value))
        {
            queryValue = value;
        }
    }
}
```

<span data-ttu-id="171f1-244">Der Platzhalter `{KEY}` im vorherigen Beispiel ist der Parameterschlüssel der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="171f1-244">The placeholder `{KEY}` in the preceding example is the query string parameter key.</span></span> <span data-ttu-id="171f1-245">Das URL-Schlüssel-Wert-Paar `?ship=Tardis` verwendet beispielsweise den Schlüssel `ship`.</span><span class="sxs-lookup"><span data-stu-id="171f1-245">For example, the URL key-value pair `?ship=Tardis` uses a key of `ship`.</span></span>
