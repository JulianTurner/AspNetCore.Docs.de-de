---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/17/2020
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
uid: blazor/templates
ms.openlocfilehash: 602ad2908d607703a3b77b2047d51d912645b043
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703721"
---
# <a name="aspnet-core-no-locblazor-templates"></a><span data-ttu-id="9032b-103">ASP.NET Core-Blazor-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="9032b-103">ASP.NET Core Blazor templates</span></span>

<span data-ttu-id="9032b-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="9032b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9032b-105">Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:</span><span class="sxs-lookup"><span data-stu-id="9032b-105">The Blazor framework provides templates to develop apps for each of the Blazor hosting models:</span></span>

* <span data-ttu-id="9032b-106">Blazor WebAssembly (`blazorwasm`)</span><span class="sxs-lookup"><span data-stu-id="9032b-106">Blazor WebAssembly (`blazorwasm`)</span></span>
* <span data-ttu-id="9032b-107">Blazor Server (`blazorserver`)</span><span class="sxs-lookup"><span data-stu-id="9032b-107">Blazor Server (`blazorserver`)</span></span>

<span data-ttu-id="9032b-108">Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="9032b-108">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="9032b-109">Vorlagenoptionen sind verfügbar, indem Sie die `--help`-Option an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:</span><span class="sxs-lookup"><span data-stu-id="9032b-109">Template options are available by passing the `--help` option to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command:</span></span>

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a><span data-ttu-id="9032b-110">Blazor-Projektstruktur</span><span class="sxs-lookup"><span data-stu-id="9032b-110">Blazor project structure</span></span>

<span data-ttu-id="9032b-111">Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Projektvorlage generiert wird:</span><span class="sxs-lookup"><span data-stu-id="9032b-111">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9032b-112">`Program.cs`: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="9032b-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="9032b-113">ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9032b-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="9032b-114">WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9032b-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="9032b-115">Die `App`-Komponente ist die Stammkomponente der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="9032b-116">Die `App`-Komponente wird als das `app`-DOM-Element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("#app")`) angegeben.</span><span class="sxs-lookup"><span data-stu-id="9032b-116">The `App` component is specified as the `app` DOM element (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="9032b-117">[Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="9032b-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9032b-118">`Program.cs`: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="9032b-118">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="9032b-119">ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="9032b-119">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="9032b-120">WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="9032b-120">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="9032b-121">Die `App`-Komponente ist die Stammkomponente der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-121">The `App` component is the root component of the app.</span></span> <span data-ttu-id="9032b-122">Die `App`-Komponente wird als das `app`-DOM-Element (`<app>Loading...</app>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("app")`) angegeben.</span><span class="sxs-lookup"><span data-stu-id="9032b-122">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="9032b-123">[Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="9032b-123">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="9032b-124">`Startup.cs` (Blazor Server): Diese Datei enthält die Startlogik der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-124">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="9032b-125">Die `Startup`-Klasse definiert zwei Methoden:</span><span class="sxs-lookup"><span data-stu-id="9032b-125">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="9032b-126">`ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-126">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="9032b-127">In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird dem Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="9032b-127">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="9032b-128">`Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:</span><span class="sxs-lookup"><span data-stu-id="9032b-128">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="9032b-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten.</span><span class="sxs-lookup"><span data-stu-id="9032b-129"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="9032b-130">Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.</span><span class="sxs-lookup"><span data-stu-id="9032b-130">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="9032b-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (`Pages/_Host.cshtml`) einzurichten und die Navigation zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="9032b-131">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

* <span data-ttu-id="9032b-132">`wwwroot/index.html` (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="9032b-132">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="9032b-133">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9032b-133">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="9032b-134">Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9032b-134">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="9032b-135">Die Komponente wird an der Position des `app`-DOM-Elements (`<app>...</app>`) gerendert.</span><span class="sxs-lookup"><span data-stu-id="9032b-135">The component is rendered at the location of the `app` DOM element (`<app>...</app>`).</span></span>
  * <span data-ttu-id="9032b-136">Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:</span><span class="sxs-lookup"><span data-stu-id="9032b-136">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="9032b-137">Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.</span><span class="sxs-lookup"><span data-stu-id="9032b-137">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="9032b-138">Sie initialisiert die Runtime, damit die App ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="9032b-138">Initializes the runtime to run the app.</span></span>

* <span data-ttu-id="9032b-139">`App.razor`: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet.</span><span class="sxs-lookup"><span data-stu-id="9032b-139">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="9032b-140">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.</span><span class="sxs-lookup"><span data-stu-id="9032b-140">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="9032b-141">`Pages`-Ordner: Dieser Ordner enthält die routingfähigen Komponenten/Seiten (`.razor`), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="9032b-141">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="9032b-142">Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben.</span><span class="sxs-lookup"><span data-stu-id="9032b-142">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="9032b-143">Die Vorlage besteht aus den folgenden Teilen:</span><span class="sxs-lookup"><span data-stu-id="9032b-143">The template includes the following:</span></span>
  * <span data-ttu-id="9032b-144">`_Host.cshtml` (Blazor Server): Die Stammseite der App, die als Razor-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="9032b-144">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="9032b-145">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="9032b-145">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="9032b-146">Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="9032b-146">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="9032b-147">Die Hostseite gibt an, wo die `App`-Stammkomponente (`App.razor`) gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="9032b-147">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="9032b-148">`Counter`-Komponente (`Pages/Counter.razor`): Implementiert die Zählerseite.</span><span class="sxs-lookup"><span data-stu-id="9032b-148">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="9032b-149">`Error`-Komponente (`Error.razor`, nur Blazor Server-App): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="9032b-149">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="9032b-150">`FetchData`-Komponente (`Pages/FetchData.razor`): Implementiert die Seite zum Abrufen von Daten.</span><span class="sxs-lookup"><span data-stu-id="9032b-150">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="9032b-151">`Index`-Komponente (`Pages/Index.razor`): Implementiert die Homepage.</span><span class="sxs-lookup"><span data-stu-id="9032b-151">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="9032b-152">`Properties/launchSettings.json`: Enthält die [Konfiguration der Entwicklungsumgebung](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="9032b-152">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="9032b-153">`Shared`-Ordner: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (`.razor`), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9032b-153">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="9032b-154">`MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-154">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="9032b-155">`MainLayout.razor.css`: Stylesheet für das Hauptlayout der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-155">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="9032b-156">`NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="9032b-156">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="9032b-157">Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="9032b-157">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="9032b-158">Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="9032b-158">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="9032b-159">`NavMenu.razor.css`: Stylesheet für das Navigationsmenü der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-159">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="9032b-160">`Shared`-Ordner: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (`.razor`), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="9032b-160">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="9032b-161">`MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.</span><span class="sxs-lookup"><span data-stu-id="9032b-161">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="9032b-162">`NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="9032b-162">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="9032b-163">Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="9032b-163">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="9032b-164">Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="9032b-164">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="9032b-165">`_Imports.razor`: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (`.razor`) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.</span><span class="sxs-lookup"><span data-stu-id="9032b-165">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="9032b-166">`Data`-Ordner (Blazor Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="9032b-166">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="9032b-167">`wwwroot`: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.</span><span class="sxs-lookup"><span data-stu-id="9032b-167">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="9032b-168">`appsettings.json`: Enthält [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration) für die App.</span><span class="sxs-lookup"><span data-stu-id="9032b-168">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="9032b-169">In einer Blazor WebAssembly-App befindet sich die Datei mit den App-Einstellungen im Ordner `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="9032b-169">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="9032b-170">In einer Blazor Server-App befindet sich die Datei mit den App-Einstellungen im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="9032b-170">In a Blazor Server app, the app settings file is located at the project root.</span></span>
