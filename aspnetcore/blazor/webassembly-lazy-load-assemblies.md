---
title: Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly-Apps ausgeführt wird.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 46f98080ad40f614f9cb1af2190f263d205c1016
ms.sourcegitcommit: f09407d128634d200c893bfb1c163e87fa47a161
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88865160"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="c9d66-103">Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c9d66-103">Lazy load assemblies in ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="c9d66-104">Von [Safia Abdalla](https://safia.rocks) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c9d66-104">By [Safia Abdalla](https://safia.rocks) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c9d66-105">Die Startleistung einer Blazor WebAssembly-App kann verbessert werden, indem das Laden einiger Anwendungsassemblys verzögert wird, bis sie erforderlich sind. Dies wird als *Lazy Loading* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="c9d66-105">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they are required, which is called *lazy loading*.</span></span> <span data-ttu-id="c9d66-106">Beispielsweise können Assemblys, die nur zum Rendern einer einzelnen Komponente verwendet werden, so eingerichtet werden, dass Sie nur geladen werden, wenn der Benutzer zu dieser Komponente navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-106">For example, assemblies that are only used to render a single component can be set up to load only if the user navigates to that component.</span></span> <span data-ttu-id="c9d66-107">Nach dem Laden werden die Assemblys clientseitig zwischengespeichert und sind für alle zukünftigen Navigationsvorgänge verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c9d66-107">After loading, the assemblies are cached client-side and are available for all future navigations.</span></span>

<span data-ttu-id="c9d66-108">Mit der Lazy Loading-Funktion von Blazor können Sie App-Assemblys für Lazy Loading markieren. Die Assemblys werden dann während der Laufzeit geladen, wenn der Benutzer zu einer bestimmten Route navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-108">Blazor's lazy loading feature allows you to mark app assemblies for lazy loading, which loads the assemblies during runtime when the user navigates to a particular route.</span></span> <span data-ttu-id="c9d66-109">Die Funktion besteht aus Änderungen an der Projektdatei und Änderungen am Router der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="c9d66-109">The feature consists of changes to the project file and changes to the application's router.</span></span>

> [!NOTE]
> <span data-ttu-id="c9d66-110">Blazor Server-Apps profitieren nicht von Lazy Loading, da Assemblys in einer Blazor Server-App nicht auf den Client heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-110">Assembly lazy loading doesn't benefit Blazor Server apps because assemblies aren't downloaded to the client in a Blazor Server app.</span></span>

## <a name="project-file"></a><span data-ttu-id="c9d66-111">Projektdatei</span><span class="sxs-lookup"><span data-stu-id="c9d66-111">Project file</span></span>

<span data-ttu-id="c9d66-112">Markieren Sie Assemblys in der Projektdatei der App (`.csproj`) mithilfe des `BlazorWebAssemblyLazyLoad`-Elements für Lazy Loading.</span><span class="sxs-lookup"><span data-stu-id="c9d66-112">Mark assemblies for lazy loading in the app's project file (`.csproj`) using the `BlazorWebAssemblyLazyLoad` item.</span></span> <span data-ttu-id="c9d66-113">Verwenden Sie den Assemblynamen ohne die Erweiterung `.dll`.</span><span class="sxs-lookup"><span data-stu-id="c9d66-113">Use the assembly name without the `.dll` extension.</span></span> <span data-ttu-id="c9d66-114">Das Blazor Framework verhindert, dass die durch diese Elementgruppe angegebenen Assemblys beim App-Start geladen werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-114">The Blazor framework prevents the assemblies specified by this item group from loading at app launch.</span></span> <span data-ttu-id="c9d66-115">Im folgenden Beispiel wird eine große benutzerdefinierte Assembly (`GrantImaharaRobotControls.dll`) für Lazy Loading markiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-115">The following example marks a large custom assembly (`GrantImaharaRobotControls.dll`) for lazy loading.</span></span> <span data-ttu-id="c9d66-116">Wenn eine Assembly, die für Lazy Loading markiert ist, Abhängigkeiten aufweist, muss sie auch in der Projektdatei für Lazy Loading markiert werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-116">If an assembly that's marked for lazy loading has dependencies, they must also be marked for lazy loading in the project file.</span></span>

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls" />
</ItemGroup>
```

<span data-ttu-id="c9d66-117">Nur für Assemblys, die von der App verwendet werden, kann Lazy Loading genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-117">Only assemblies that are used by the app can be lazily loaded.</span></span> <span data-ttu-id="c9d66-118">Der Linker entfernt nicht verwendete Assemblys aus der veröffentlichten Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="c9d66-118">The linker strips unused assemblies from published output.</span></span>

> [!NOTE]
> <span data-ttu-id="c9d66-119">Ab dem Release Candidate 1 (RC1) von .NET 5, der Mitte September veröffentlicht wird, benötigt der Assemblyname die `.dll`-Erweiterung:</span><span class="sxs-lookup"><span data-stu-id="c9d66-119">In .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September, the assembly name will require the `.dll` extension:</span></span>
>
> ```xml
> <ItemGroup>
>  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
> </ItemGroup>
> ```

## <a name="router-component"></a><span data-ttu-id="c9d66-120">`Router`-Komponente</span><span class="sxs-lookup"><span data-stu-id="c9d66-120">`Router` component</span></span>

<span data-ttu-id="c9d66-121">Die `Router`-Komponente von Blazor bestimmt, welche Assemblys Blazor nach routingfähigen Komponenten durchsucht.</span><span class="sxs-lookup"><span data-stu-id="c9d66-121">Blazor's `Router` component designates which assemblies Blazor searches for routable components.</span></span> <span data-ttu-id="c9d66-122">Die `Router`-Komponente ist auch dafür verantwortlich, die Komponente für die Route zu rendern, in der der Benutzer navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-122">The `Router` component is also responsible for rendering the component for the route where the user navigates.</span></span> <span data-ttu-id="c9d66-123">Die `Router`-Komponente unterstützt ein `OnNavigateAsync`-Feature, das in Verbindung mit Lazy Loading verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c9d66-123">The `Router` component supports an `OnNavigateAsync` feature that can be used in conjunction with lazy loading.</span></span>

<span data-ttu-id="c9d66-124">In der `Router`-Komponente der App (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="c9d66-124">In the app's `Router` component (`App.razor`):</span></span>

* <span data-ttu-id="c9d66-125">Fügen Sie einen `OnNavigateAsync`-Rückruf hinzu.</span><span class="sxs-lookup"><span data-stu-id="c9d66-125">Add an `OnNavigateAsync` callback.</span></span> <span data-ttu-id="c9d66-126">Der `OnNavigateAsync`-Handler wird aufgerufen, wenn der Benutzer:</span><span class="sxs-lookup"><span data-stu-id="c9d66-126">The `OnNavigateAsync` handler is invoked when the user:</span></span>
  * <span data-ttu-id="c9d66-127">Zum ersten Mal eine Route besucht, indem er direkt in seinem Browser zu ihr navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-127">Visits a route for the first time by navigating to it directly from their browser.</span></span>
  * <span data-ttu-id="c9d66-128">Zu einer neuen Route mithilfe eines Links oder eines <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>-Aufrufs navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-128">Navigates to a new route using a link or a <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> invocation.</span></span>
* <span data-ttu-id="c9d66-129">Wenn für Lazy Loading markierte Assemblys routingfähige Komponenten enthalten, fügen Sie der Komponente eine [Liste](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (z. B. mit dem Namen `lazyLoadedAssemblies`) hinzu.</span><span class="sxs-lookup"><span data-stu-id="c9d66-129">If lazy-loaded assemblies contain routable components, add a [List](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (for example, named `lazyLoadedAssemblies`) to the component.</span></span> <span data-ttu-id="c9d66-130">Die Assemblys werden an die <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Sammlung zurückgegeben, wenn die Assemblys routingfähige Komponenten enthalten.</span><span class="sxs-lookup"><span data-stu-id="c9d66-130">The assemblies are passed back to the <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies> collection in case the assemblies contain routable components.</span></span> <span data-ttu-id="c9d66-131">Das Framework durchsucht die Assemblys nach Routen und aktualisiert die Routensammlung, wenn neue Routen gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-131">The framework searches the assemblies for routes and updates the route collection if any new routes are found.</span></span>

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

<span data-ttu-id="c9d66-132">Wenn der `OnNavigateAsync`-Rückruf einen Ausnahmefehler auslöst, wird die [Fehlerbenutzeroberfläche von Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c9d66-132">If the `OnNavigateAsync` callback throws an unhandled exception, the [Blazor error UI](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) is invoked.</span></span>

### <a name="assembly-load-logic-in-onnavigateasync"></a><span data-ttu-id="c9d66-133">Assemblyladelogik in `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="c9d66-133">Assembly load logic in `OnNavigateAsync`</span></span>

<span data-ttu-id="c9d66-134">`OnNavigateAsync` verfügt über einen `NavigationContext`-Parameter, der Informationen zum aktuellen asynchronen Navigationsereignis bereitstellt, einschließlich des Zielpfads (`Path`) und des Abbruchtokens (`CancellationToken`):</span><span class="sxs-lookup"><span data-stu-id="c9d66-134">`OnNavigateAsync` has a `NavigationContext` parameter that provides information about the current asynchronous navigation event, including the target path (`Path`) and the cancellation token (`CancellationToken`):</span></span>

* <span data-ttu-id="c9d66-135">Die `Path`-Eigenschaft ist der Zielpfad des Benutzers relativ zum Basispfad der App, z. B. `/robot`.</span><span class="sxs-lookup"><span data-stu-id="c9d66-135">The `Path` property is the user's destination path relative to the app's base path, such as `/robot`.</span></span>
* <span data-ttu-id="c9d66-136">Das `CancellationToken` kann verwendet werden, um den Abbruch des asynchronen Tasks zu beobachten.</span><span class="sxs-lookup"><span data-stu-id="c9d66-136">The `CancellationToken` can be used to observe the cancellation of the asynchronous task.</span></span> <span data-ttu-id="c9d66-137">`OnNavigateAsync` bricht den aktuell ausgeführten Navigationstask automatisch ab, wenn der Benutzer zu einer anderen Seite navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-137">`OnNavigateAsync` automatically cancels the currently running navigation task when the user navigates to a different page.</span></span>

<span data-ttu-id="c9d66-138">Implementieren Sie in `OnNavigateAsync` Logik, um die zu ladenden Assemblys zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="c9d66-138">Inside `OnNavigateAsync`, implement logic to determine the assemblies to load.</span></span> <span data-ttu-id="c9d66-139">Folgende Optionen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="c9d66-139">Options include:</span></span>

* <span data-ttu-id="c9d66-140">Bedingte Überprüfungen innerhalb der `OnNavigateAsync`-Methode.</span><span class="sxs-lookup"><span data-stu-id="c9d66-140">Conditional checks inside the `OnNavigateAsync` method.</span></span>
* <span data-ttu-id="c9d66-141">Eine Nachschlagetabelle, in der Routen Assemblynamen zugeordnet werden, die entweder in die Komponente eingefügt oder innerhalb des [`@code`](xref:mvc/views/razor#code)-Blocks implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-141">A lookup table that maps routes to assembly names, either injected into the component or implemented within the [`@code`](xref:mvc/views/razor#code) block.</span></span>

<span data-ttu-id="c9d66-142">`LazyAssemblyLoader` ist ein vom Framework bereitgestellter Singleton-Dienst zum Laden von Assemblys.</span><span class="sxs-lookup"><span data-stu-id="c9d66-142">`LazyAssemblyLoader` is a framework-provided singleton service for loading assemblies.</span></span> <span data-ttu-id="c9d66-143">Fügen Sie `LazyAssemblyLoader` in die `Router`-Komponente ein:</span><span class="sxs-lookup"><span data-stu-id="c9d66-143">Inject `LazyAssemblyLoader` into the `Router` component:</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

<span data-ttu-id="c9d66-144">`LazyAssemblyLoader` stellt die `LoadAssembliesAsync`-Methode bereit, für die Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="c9d66-144">The `LazyAssemblyLoader` provides the `LoadAssembliesAsync` method that:</span></span>

* <span data-ttu-id="c9d66-145">Sie verwendet JS Interop zum Abrufen von Assemblys über einen Netzwerkaufruf.</span><span class="sxs-lookup"><span data-stu-id="c9d66-145">Uses JS interop to fetch assemblies via a network call.</span></span>
* <span data-ttu-id="c9d66-146">Lädt Assemblys in die Laufzeit, die für WebAssembly im Browser ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="c9d66-146">Loads assemblies into the runtime executing on WebAssembly in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="c9d66-147">Die Lazy Loading-Implementierung des Frameworks unterstützt PreRendering auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c9d66-147">The framework's lazy loading implementation supports prerendering on the server.</span></span> <span data-ttu-id="c9d66-148">Während des PreRenderings werden alle Assemblys (einschließlich der für Lazy Loading markierten Assemblys) als geladen angenommen.</span><span class="sxs-lookup"><span data-stu-id="c9d66-148">During prerendering, all assemblies, including those marked for lazy loading, are assumed to be loaded.</span></span>

### <a name="user-interaction-with-navigating-content"></a><span data-ttu-id="c9d66-149">Benutzerinteraktion mit `<Navigating>`-Inhalt</span><span class="sxs-lookup"><span data-stu-id="c9d66-149">User interaction with `<Navigating>` content</span></span>

<span data-ttu-id="c9d66-150">Während des Ladens von Assemblys, was mehrere Sekunden dauern kann, kann die `Router`-Komponente dem Benutzer anzeigen, dass ein Seitenübergang stattfindet:</span><span class="sxs-lookup"><span data-stu-id="c9d66-150">While loading assemblies, which can take several seconds, the `Router` component can indicate to the user that a page transition is occurring:</span></span>

* <span data-ttu-id="c9d66-151">Fügen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung für den <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>-Namespace hinzu.</span><span class="sxs-lookup"><span data-stu-id="c9d66-151">Add an [`@using`](xref:mvc/views/razor#using) directive for the <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName> namespace.</span></span>
* <span data-ttu-id="c9d66-152">Fügen Sie der Komponente ein `<Navigating>`-Tag mit Markup für die Anzeige während Seitenübergangsereignissen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c9d66-152">Add a `<Navigating>` tag to the component with markup to display during page transition events.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

### <a name="handle-cancellations-in-onnavigateasync"></a><span data-ttu-id="c9d66-153">Verarbeiten von Abbrüchen in `OnNavigateAsync`</span><span class="sxs-lookup"><span data-stu-id="c9d66-153">Handle cancellations in `OnNavigateAsync`</span></span>

<span data-ttu-id="c9d66-154">Das `NavigationContext`-Objekt, das an den `OnNavigateAsync`-Rückruf übergeben wird, enthält ein `CancellationToken`, das beim Auftreten eines neuen Navigationsereignisses festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c9d66-154">The `NavigationContext` object passed to the `OnNavigateAsync` callback contains a `CancellationToken` that's set when a new navigation event occurs.</span></span> <span data-ttu-id="c9d66-155">Der `OnNavigateAsync`-Rückruf muss ausgelöst werden, wenn dieses Abbruchtoken festgelegt wird, um zu vermeiden, dass der `OnNavigateAsync`-Rückruf für eine veraltete Navigation weiterhin ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="c9d66-155">The `OnNavigateAsync` callback must throw when this cancellation token is set to avoid continuing to run the `OnNavigateAsync` callback on a outdated navigation.</span></span>

<span data-ttu-id="c9d66-156">Wenn ein Benutzer zu Route A und dann sofort zu Route B navigiert, sollte die App den `OnNavigateAsync`-Rückruf für Route A nicht weiterhin ausführen:</span><span class="sxs-lookup"><span data-stu-id="c9d66-156">If a user navigates to Route A and then immediately to Route B, the app shouldn't continue running the `OnNavigateAsync` callback for Route A:</span></span>

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="c9d66-157">Wenn der Rückruf nicht ausgelöst wird, wenn das Abbruchtoken in `NavigationContext` abgebrochen wird, kann dies zu unbeabsichtigtem Verhalten führen, z. B. zum Rendern einer Komponente aus einer vorherigen Navigation.</span><span class="sxs-lookup"><span data-stu-id="c9d66-157">Not throwing if the cancellation token in `NavigationContext` is canceled can result in unintended behavior, such as rendering a component from a previous navigation.</span></span>

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a><span data-ttu-id="c9d66-158">`OnNavigateAsync`-Ereignisse und umbenannte Assemblydateien</span><span class="sxs-lookup"><span data-stu-id="c9d66-158">`OnNavigateAsync` events and renamed assembly files</span></span>

<span data-ttu-id="c9d66-159">Das Ressourcenladeprogramm benötigt die Assemblynamen, die in der `blazor.boot.json`-Datei definiert sind.</span><span class="sxs-lookup"><span data-stu-id="c9d66-159">The resource loader relies on the assembly names that are defined in the `blazor.boot.json` file.</span></span> <span data-ttu-id="c9d66-160">Wenn [Assemblys umbenannt werden](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), stimmen die Assemblynamen, die in `OnNavigateAsync`-Methoden verwendet werden, und die Assemblynamen in der `blazor.boot.json`-Datei nicht mehr überein.</span><span class="sxs-lookup"><span data-stu-id="c9d66-160">If [assemblies are renamed](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), the assembly names used in `OnNavigateAsync` methods and the assembly names in the `blazor.boot.json` file are out of sync.</span></span>

<span data-ttu-id="c9d66-161">So beheben Sie dies:</span><span class="sxs-lookup"><span data-stu-id="c9d66-161">To rectify this:</span></span>

* <span data-ttu-id="c9d66-162">Überprüfen Sie, ob die App in der Produktionsumgebung ausgeführt wird, wenn Sie bestimmen, welche Assemblynamen verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c9d66-162">Check to see if the app is running in the Production environment when determining which assembly names to use.</span></span>
* <span data-ttu-id="c9d66-163">Speichern Sie die umbenannten Assemblynamen in einer eigenen Datei, und lesen Sie aus dieser Datei, um zu bestimmen, welche Assemblynamen in der `LazyLoadAssemblyService`- und der `OnNavigateAsync`-Methode verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c9d66-163">Store the renamed assembly names in a separate file and read from that file to determine what assembly name to use in the `LazyLoadAssemblyService` and `OnNavigateAsync` methods.</span></span>

### <a name="complete-example"></a><span data-ttu-id="c9d66-164">Vollständiges Beispiel</span><span class="sxs-lookup"><span data-stu-id="c9d66-164">Complete example</span></span>

<span data-ttu-id="c9d66-165">Die folgende vollständige `Router`-Komponente veranschaulicht das Laden der `GrantImaharaRobotControls.dll`-Assembly, wenn der Benutzer zu `/robot` navigiert.</span><span class="sxs-lookup"><span data-stu-id="c9d66-165">The following complete `Router` component demonstrates loading the `GrantImaharaRobotControls.dll` assembly when the user navigates to `/robot`.</span></span> <span data-ttu-id="c9d66-166">Während Seitenübergängen wird dem Benutzer eine formatierte Meldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c9d66-166">During page transitions, a styled message is displayed to the user.</span></span>

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="c9d66-167">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="c9d66-167">Troubleshoot</span></span>

* <span data-ttu-id="c9d66-168">Wenn unerwartetes Rendering auftritt (wenn z. B. eine Komponente aus einer vorherigen Navigation gerendert wird), vergewissern Sie sich, dass der Code ausgelöst wird, wenn das Abbruchtoken festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="c9d66-168">If unexpected rendering occurs (for example, a component from a previous navigation is rendered), confirm that the code throws if the cancellation token is set.</span></span>
* <span data-ttu-id="c9d66-169">Wenn Assemblys beim Starten der Anwendung weiterhin geladen werden, überprüfen Sie, ob die Assembly in der Projektdatei für Lazy Loading markiert ist.</span><span class="sxs-lookup"><span data-stu-id="c9d66-169">If assemblies are still loaded at application start, check that the assembly is marked as lazy loaded in the project file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c9d66-170">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c9d66-170">Additional resources</span></span>

* <xref:blazor/webassembly-performance-best-practices>
