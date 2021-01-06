---
title: Blazor-Projektstruktur in ASP.NET Core
author: guardrex
description: Hier erfahren Sie mehr über die Blazor-App-Projektstruktur in ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
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
uid: blazor/project-structure
ms.openlocfilehash: 6df84366dc3fc99d31a8a0e614ca860a50df7f5c
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97513532"
---
# <a name="aspnet-core-no-locblazor-project-structure"></a><span data-ttu-id="d7306-103">Blazor-Projektstruktur in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d7306-103">ASP.NET Core Blazor project structure</span></span>

<span data-ttu-id="d7306-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d7306-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d7306-105">Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Projektvorlage generiert wird:</span><span class="sxs-lookup"><span data-stu-id="d7306-105">The following files and folders make up a Blazor app generated from a Blazor project template:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="d7306-106">`Program.cs`: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="d7306-106">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="d7306-107">ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d7306-107">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="d7306-108">WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d7306-108">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="d7306-109">Die `App`-Komponente ist die Stammkomponente der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-109">The `App` component is the root component of the app.</span></span> <span data-ttu-id="d7306-110">Die `App`-Komponente wird als das `div`-DOM-Element mit einem `id`-Objekt von `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("#app")`) angegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-110">The `App` component is specified as the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("#app")`).</span></span>
    * <span data-ttu-id="d7306-111">[Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="d7306-111">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="d7306-112">`Program.cs`: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:</span><span class="sxs-lookup"><span data-stu-id="d7306-112">`Program.cs`: The app's entry point that sets up the:</span></span>

  * <span data-ttu-id="d7306-113">ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)</span><span class="sxs-lookup"><span data-stu-id="d7306-113">ASP.NET Core [host](xref:fundamentals/host/generic-host) (Blazor Server)</span></span>
  * <span data-ttu-id="d7306-114">WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="d7306-114">WebAssembly host (Blazor WebAssembly): The code in this file is unique to apps created from the Blazor WebAssembly template (`blazorwasm`).</span></span>
    * <span data-ttu-id="d7306-115">Die `App`-Komponente ist die Stammkomponente der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-115">The `App` component is the root component of the app.</span></span> <span data-ttu-id="d7306-116">Die `App`-Komponente wird als das `app`-DOM-Element (`<app>Loading...</app>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("app")`) angegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-116">The `App` component is specified as the `app` DOM element (`<app>Loading...</app>` in `wwwroot/index.html`) to the root component collection (`builder.RootComponents.Add<App>("app")`).</span></span>
    * <span data-ttu-id="d7306-117">[Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span><span class="sxs-lookup"><span data-stu-id="d7306-117">[Services](xref:blazor/fundamentals/dependency-injection) are added and configured (for example, `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).</span></span>

::: moniker-end

* <span data-ttu-id="d7306-118">`Startup.cs` (Blazor Server): Diese Datei enthält die Startlogik der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-118">`Startup.cs` (Blazor Server): Contains the app's startup logic.</span></span> <span data-ttu-id="d7306-119">Die `Startup`-Klasse definiert zwei Methoden:</span><span class="sxs-lookup"><span data-stu-id="d7306-119">The `Startup` class defines two methods:</span></span>

  * <span data-ttu-id="d7306-120">`ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-120">`ConfigureServices`: Configures the app's [dependency injection (DI)](xref:fundamentals/dependency-injection) services.</span></span> <span data-ttu-id="d7306-121">In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird dem Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="d7306-121">In Blazor Server apps, services are added by calling <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A>, and the `WeatherForecastService` is added to the service container for use by the example `FetchData` component.</span></span>
  * <span data-ttu-id="d7306-122">`Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:</span><span class="sxs-lookup"><span data-stu-id="d7306-122">`Configure`: Configures the app's request handling pipeline:</span></span>
    * <span data-ttu-id="d7306-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten.</span><span class="sxs-lookup"><span data-stu-id="d7306-123"><xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> is called to set up an endpoint for the real-time connection with the browser.</span></span> <span data-ttu-id="d7306-124">Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.</span><span class="sxs-lookup"><span data-stu-id="d7306-124">The connection is created with [SignalR](xref:signalr/introduction), which is a framework for adding real-time web functionality to apps.</span></span>
    * <span data-ttu-id="d7306-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (`Pages/_Host.cshtml`) einzurichten und die Navigation zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="d7306-125">[`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) is called to set up the root page of the app (`Pages/_Host.cshtml`) and enable navigation.</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="d7306-126">`wwwroot/index.html` (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="d7306-126">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="d7306-127">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-127">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="d7306-128">Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="d7306-128">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="d7306-129">Die Komponente wird an der Position des `div`-DOM-Elements mit einem `id`-Objekt von `app` (`<div id="app">Loading...</div>`) gerendert.</span><span class="sxs-lookup"><span data-stu-id="d7306-129">The component is rendered at the location of the `div` DOM element with an `id` of `app` (`<div id="app">Loading...</div>`).</span></span>
  * <span data-ttu-id="d7306-130">Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:</span><span class="sxs-lookup"><span data-stu-id="d7306-130">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="d7306-131">Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.</span><span class="sxs-lookup"><span data-stu-id="d7306-131">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="d7306-132">Sie initialisiert die Runtime, damit die App ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="d7306-132">Initializes the runtime to run the app.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="d7306-133">`wwwroot/index.html` (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="d7306-133">`wwwroot/index.html` (Blazor WebAssembly): The root page of the app implemented as an HTML page:</span></span>
  * <span data-ttu-id="d7306-134">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-134">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
  * <span data-ttu-id="d7306-135">Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="d7306-135">The page specifies where the root `App` component is rendered.</span></span> <span data-ttu-id="d7306-136">Die Komponente wird an der Position des `app`-DOM-Elements (`<app>Loading...</app>`) gerendert.</span><span class="sxs-lookup"><span data-stu-id="d7306-136">The component is rendered at the location of the `app` DOM element (`<app>Loading...</app>`).</span></span>
  * <span data-ttu-id="d7306-137">Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:</span><span class="sxs-lookup"><span data-stu-id="d7306-137">The `_framework/blazor.webassembly.js` JavaScript file is loaded, which:</span></span>
    * <span data-ttu-id="d7306-138">Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.</span><span class="sxs-lookup"><span data-stu-id="d7306-138">Downloads the .NET runtime, the app, and the app's dependencies.</span></span>
    * <span data-ttu-id="d7306-139">Sie initialisiert die Runtime, damit die App ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="d7306-139">Initializes the runtime to run the app.</span></span>

::: moniker-end

* <span data-ttu-id="d7306-140">`App.razor`: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet.</span><span class="sxs-lookup"><span data-stu-id="d7306-140">`App.razor`: The root component of the app that sets up client-side routing using the <xref:Microsoft.AspNetCore.Components.Routing.Router> component.</span></span> <span data-ttu-id="d7306-141">Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.</span><span class="sxs-lookup"><span data-stu-id="d7306-141">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component intercepts browser navigation and renders the page that matches the requested address.</span></span>

* <span data-ttu-id="d7306-142">`Pages`-Ordner: Dieser Ordner enthält die routingfähigen Komponenten/Seiten (`.razor`), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="d7306-142">`Pages` folder: Contains the routable components/pages (`.razor`) that make up the Blazor app and the root Razor page of a Blazor Server app.</span></span> <span data-ttu-id="d7306-143">Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-143">The route for each page is specified using the [`@page`](xref:mvc/views/razor#page) directive.</span></span> <span data-ttu-id="d7306-144">Die Vorlage besteht aus den folgenden Teilen:</span><span class="sxs-lookup"><span data-stu-id="d7306-144">The template includes the following:</span></span>
  * <span data-ttu-id="d7306-145">`_Host.cshtml` (Blazor Server): Die Stammseite der App, die als Razor-Seite implementiert ist:</span><span class="sxs-lookup"><span data-stu-id="d7306-145">`_Host.cshtml` (Blazor Server): The root page of the app implemented as a Razor Page:</span></span>
    * <span data-ttu-id="d7306-146">Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="d7306-146">When any page of the app is initially requested, this page is rendered and returned in the response.</span></span>
    * <span data-ttu-id="d7306-147">Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.</span><span class="sxs-lookup"><span data-stu-id="d7306-147">The `_framework/blazor.server.js` JavaScript file is loaded, which sets up the real-time SignalR connection between the browser and the server.</span></span>
    * <span data-ttu-id="d7306-148">Die Hostseite gibt an, wo die `App`-Stammkomponente (`App.razor`) gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="d7306-148">The Host page specifies where the root `App` component (`App.razor`) is rendered.</span></span>
  * <span data-ttu-id="d7306-149">`Counter`-Komponente (`Pages/Counter.razor`): Implementiert die Zählerseite.</span><span class="sxs-lookup"><span data-stu-id="d7306-149">`Counter` component (`Pages/Counter.razor`): Implements the Counter page.</span></span>
  * <span data-ttu-id="d7306-150">`Error`-Komponente (`Error.razor`, nur Blazor Server-App): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.</span><span class="sxs-lookup"><span data-stu-id="d7306-150">`Error` component (`Error.razor`, Blazor Server app only): Rendered when an unhandled exception occurs in the app.</span></span>
  * <span data-ttu-id="d7306-151">`FetchData`-Komponente (`Pages/FetchData.razor`): Implementiert die Seite zum Abrufen von Daten.</span><span class="sxs-lookup"><span data-stu-id="d7306-151">`FetchData` component (`Pages/FetchData.razor`): Implements the Fetch data page.</span></span>
  * <span data-ttu-id="d7306-152">`Index`-Komponente (`Pages/Index.razor`): Implementiert die Homepage.</span><span class="sxs-lookup"><span data-stu-id="d7306-152">`Index` component (`Pages/Index.razor`): Implements the Home page.</span></span>
  
* <span data-ttu-id="d7306-153">`Properties/launchSettings.json`: Enthält die [Konfiguration der Entwicklungsumgebung](xref:fundamentals/environments#development-and-launchsettingsjson).</span><span class="sxs-lookup"><span data-stu-id="d7306-153">`Properties/launchSettings.json`: Holds [development environment configuration](xref:fundamentals/environments#development-and-launchsettingsjson).</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="d7306-154">`Shared`-Ordner: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (`.razor`), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="d7306-154">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="d7306-155">`MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-155">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="d7306-156">`MainLayout.razor.css`: Stylesheet für das Hauptlayout der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-156">`MainLayout.razor.css`: Stylesheet for the app's main layout.</span></span>
  * <span data-ttu-id="d7306-157">`NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="d7306-157">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="d7306-158">Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="d7306-158">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="d7306-159">Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d7306-159">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  * <span data-ttu-id="d7306-160">`NavMenu.razor.css`: Stylesheet für das Navigationsmenü der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-160">`NavMenu.razor.css`: Stylesheet for the app's navigation menu.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="d7306-161">`Shared`-Ordner: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (`.razor`), die von der App verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="d7306-161">`Shared` folder: Contains other UI components (`.razor`) used by the app:</span></span>
  * <span data-ttu-id="d7306-162">`MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.</span><span class="sxs-lookup"><span data-stu-id="d7306-162">`MainLayout` component (`MainLayout.razor`): The app's [layout component](xref:blazor/layouts).</span></span>
  * <span data-ttu-id="d7306-163">`NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste.</span><span class="sxs-lookup"><span data-stu-id="d7306-163">`NavMenu` component (`NavMenu.razor`): Implements sidebar navigation.</span></span> <span data-ttu-id="d7306-164">Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert.</span><span class="sxs-lookup"><span data-stu-id="d7306-164">Includes the [`NavLink` component](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), which renders navigation links to other Razor components.</span></span> <span data-ttu-id="d7306-165">Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="d7306-165">The <xref:Microsoft.AspNetCore.Components.Routing.NavLink> component automatically indicates a selected state when its component is loaded, which helps the user understand which component is currently displayed.</span></span>
  
::: moniker-end

* <span data-ttu-id="d7306-166">`_Imports.razor`: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (`.razor`) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.</span><span class="sxs-lookup"><span data-stu-id="d7306-166">`_Imports.razor`: Includes common Razor directives to include in the app's components (`.razor`), such as [`@using`](xref:mvc/views/razor#using) directives for namespaces.</span></span>

* <span data-ttu-id="d7306-167">`Data`-Ordner (Blazor Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="d7306-167">`Data` folder (Blazor Server): Contains the `WeatherForecast` class and implementation of the `WeatherForecastService` that provide example weather data to the app's `FetchData` component.</span></span>

* <span data-ttu-id="d7306-168">`wwwroot`: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.</span><span class="sxs-lookup"><span data-stu-id="d7306-168">`wwwroot`: The [Web Root](xref:fundamentals/index#web-root) folder for the app containing the app's public static assets.</span></span>

* <span data-ttu-id="d7306-169">`appsettings.json`: Enthält [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration) für die App.</span><span class="sxs-lookup"><span data-stu-id="d7306-169">`appsettings.json`: Holds [configuration settings](xref:blazor/fundamentals/configuration) for the app.</span></span> <span data-ttu-id="d7306-170">In einer Blazor WebAssembly-App befindet sich die Datei mit den App-Einstellungen im Ordner `wwwroot`.</span><span class="sxs-lookup"><span data-stu-id="d7306-170">In a Blazor WebAssembly app, the app settings file is located in the `wwwroot` folder.</span></span> <span data-ttu-id="d7306-171">In einer Blazor Server-App befindet sich die Datei mit den App-Einstellungen im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="d7306-171">In a Blazor Server app, the app settings file is located at the project root.</span></span>
