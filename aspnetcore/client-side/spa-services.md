---
title: Verwenden von JavaScript Services zum Erstellen von Single-Page-Anwendungen in ASP.NET Core
author: scottaddie
description: Erfahren Sie mehr über die Vorteile von JavaScript Services zum Erstellen einer von ASP.NET Core gestützten Single-Page-Anwendung (SPA).
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: H1Hack27Feb2017, devx-track-js
ms.date: 09/06/2019
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
uid: client-side/spa-services
ms.openlocfilehash: 1b9f5b4b4e066cdd3dd5fbfa666c7a087949979f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054644"
---
# <a name="use-javascript-services-to-create-single-page-applications-in-aspnet-core"></a><span data-ttu-id="81c42-103">Verwenden von JavaScript Services zum Erstellen von Single-Page-Anwendungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81c42-103">Use JavaScript Services to Create Single Page Applications in ASP.NET Core</span></span>

<span data-ttu-id="81c42-104">Von [Scott Addie](https://github.com/scottaddie) und [Fiyaz Hasan](https://fiyazhasan.me/)</span><span class="sxs-lookup"><span data-stu-id="81c42-104">By [Scott Addie](https://github.com/scottaddie) and [Fiyaz Hasan](https://fiyazhasan.me/)</span></span>

<span data-ttu-id="81c42-105">Eine Single-Page-Anwendung (SPA) ist aufgrund ihrer inhärenten umfassenden Benutzerumgebung ein gängiger Webanwendungstyp.</span><span class="sxs-lookup"><span data-stu-id="81c42-105">A Single Page Application (SPA) is a popular type of web application due to its inherent rich user experience.</span></span> <span data-ttu-id="81c42-106">Die Integration von clientseitigen SPA-Frameworks oder -Bibliotheken wie [Angular](https://angular.io/) oder [React](https://facebook.github.io/react/) mit serverseitigen Frameworks wie ASP.NET Core kann schwierig sein.</span><span class="sxs-lookup"><span data-stu-id="81c42-106">Integrating client-side SPA frameworks or libraries, such as [Angular](https://angular.io/) or [React](https://facebook.github.io/react/), with server-side frameworks such as ASP.NET Core can be difficult.</span></span> <span data-ttu-id="81c42-107">JavaScript Services wurde entwickelt, um eine reibungslose Integration zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="81c42-107">JavaScript Services was developed to reduce friction in the integration process.</span></span> <span data-ttu-id="81c42-108">Die Lösung ermöglicht einen nahtlosen Betrieb zwischen den verschiedenen Client- und Servertechnologiestacks.</span><span class="sxs-lookup"><span data-stu-id="81c42-108">It enables seamless operation between the different client and server technology stacks.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="81c42-109">Die in diesem Artikel beschriebenen Funktionen sind ab ASP.NET Core 3.0 veraltet.</span><span class="sxs-lookup"><span data-stu-id="81c42-109">The features described in this article are obsolete as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="81c42-110">Im NuGet-Paket [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) steht ein einfacherer Integrationsmechanismus für SPA-Frameworks zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="81c42-110">A simpler SPA frameworks integration mechanism is available in the [Microsoft.AspNetCore.SpaServices.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices.Extensions) NuGet package.</span></span> <span data-ttu-id="81c42-111">Weitere Informationen finden Sie unter [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890) ([Ankündigung] Ablösung von Microsoft.AspNetCore.SpaServices und Microsoft.AspNetCore.NodeServices).</span><span class="sxs-lookup"><span data-stu-id="81c42-111">For more information, see [[Announcement] Obsoleting Microsoft.AspNetCore.SpaServices and Microsoft.AspNetCore.NodeServices](https://github.com/dotnet/AspNetCore/issues/12890).</span></span>

::: moniker-end

## <a name="what-is-javascript-services"></a><span data-ttu-id="81c42-112">Was ist JavaScript Services?</span><span class="sxs-lookup"><span data-stu-id="81c42-112">What is JavaScript Services</span></span>

<span data-ttu-id="81c42-113">JavaScript Services sind eine Sammlung von clientseitigen Technologien für ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81c42-113">JavaScript Services is a collection of client-side technologies for ASP.NET Core.</span></span> <span data-ttu-id="81c42-114">Ziel ist es, ASP.NET Core als bevorzugte serverseitige Plattform für den Aufbau von SPAs zu positionieren.</span><span class="sxs-lookup"><span data-stu-id="81c42-114">Its goal is to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span>

<span data-ttu-id="81c42-115">JavaScript Services besteht aus zwei unterschiedlichen NuGet-Paketen:</span><span class="sxs-lookup"><span data-stu-id="81c42-115">JavaScript Services consists of two distinct NuGet packages:</span></span>

* <span data-ttu-id="81c42-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span><span class="sxs-lookup"><span data-stu-id="81c42-116">[Microsoft.AspNetCore.NodeServices](https://www.nuget.org/packages/Microsoft.AspNetCore.NodeServices/) (NodeServices)</span></span>
* <span data-ttu-id="81c42-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span><span class="sxs-lookup"><span data-stu-id="81c42-117">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) (SpaServices)</span></span>

<span data-ttu-id="81c42-118">Diese Pakete sind in folgenden Szenarios nützlich:</span><span class="sxs-lookup"><span data-stu-id="81c42-118">These packages are useful in the following scenarios:</span></span>

* <span data-ttu-id="81c42-119">Ausführen von JavaScript auf dem Server</span><span class="sxs-lookup"><span data-stu-id="81c42-119">Run JavaScript on the server</span></span>
* <span data-ttu-id="81c42-120">Verwenden eines SPA-Frameworks bzw. einer SPA-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="81c42-120">Use a SPA framework or library</span></span>
* <span data-ttu-id="81c42-121">Erstellen von clientseitigen Ressourcen mit Webpack</span><span class="sxs-lookup"><span data-stu-id="81c42-121">Build client-side assets with Webpack</span></span>

<span data-ttu-id="81c42-122">Der Schwerpunkt dieses Artikels liegt auf der Verwendung des SpaServices-Pakets.</span><span class="sxs-lookup"><span data-stu-id="81c42-122">Much of the focus in this article is placed on using the SpaServices package.</span></span>

## <a name="what-is-spaservices"></a><span data-ttu-id="81c42-123">Was ist SpaServices?</span><span class="sxs-lookup"><span data-stu-id="81c42-123">What is SpaServices</span></span>

<span data-ttu-id="81c42-124">SpaServices wurde konzipiert, um ASP.NET Core als bevorzugte serverseitige Plattform für den Aufbau von SPAs zu positionieren.</span><span class="sxs-lookup"><span data-stu-id="81c42-124">SpaServices was created to position ASP.NET Core as developers' preferred server-side platform for building SPAs.</span></span> <span data-ttu-id="81c42-125">SpaServices ist für die Entwicklung von SPAs mit ASP.NET Core nicht erforderlich und bindet Entwickler nicht an ein bestimmtes Clientframework.</span><span class="sxs-lookup"><span data-stu-id="81c42-125">SpaServices isn't required to develop SPAs with ASP.NET Core, and it doesn't lock developers into a particular client framework.</span></span>

<span data-ttu-id="81c42-126">SpaServices bietet nützliche Infrastrukturen wie:</span><span class="sxs-lookup"><span data-stu-id="81c42-126">SpaServices provides useful infrastructure such as:</span></span>

* [<span data-ttu-id="81c42-127">Serverseitiges Prerendering</span><span class="sxs-lookup"><span data-stu-id="81c42-127">Server-side prerendering</span></span>](#server-side-prerendering)
* [<span data-ttu-id="81c42-128">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="81c42-128">Webpack Dev Middleware</span></span>](#webpack-dev-middleware)
* [<span data-ttu-id="81c42-129">Hot Module Replacement</span><span class="sxs-lookup"><span data-stu-id="81c42-129">Hot Module Replacement</span></span>](#hot-module-replacement)
* [<span data-ttu-id="81c42-130">Hilfsprogramme für das Routing</span><span class="sxs-lookup"><span data-stu-id="81c42-130">Routing helpers</span></span>](#routing-helpers)

<span data-ttu-id="81c42-131">Zusammengenommen verbessern diese Infrastrukturkomponenten sowohl den Entwicklungsvorgang als auch die Laufzeitumgebung.</span><span class="sxs-lookup"><span data-stu-id="81c42-131">Collectively, these infrastructure components enhance both the development workflow and the runtime experience.</span></span> <span data-ttu-id="81c42-132">Die Komponenten können individuell angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-132">The components can be adopted individually.</span></span>

## <a name="prerequisites-for-using-spaservices"></a><span data-ttu-id="81c42-133">Voraussetzungen für das Verwenden von SpaServices</span><span class="sxs-lookup"><span data-stu-id="81c42-133">Prerequisites for using SpaServices</span></span>

<span data-ttu-id="81c42-134">Für die Arbeit mit SpaServices muss Folgendes installiert sein:</span><span class="sxs-lookup"><span data-stu-id="81c42-134">To work with SpaServices, install the following:</span></span>

* <span data-ttu-id="81c42-135">[Node.js](https://nodejs.org/) (Version 6 oder höher) mit npm</span><span class="sxs-lookup"><span data-stu-id="81c42-135">[Node.js](https://nodejs.org/) (version 6 or later) with npm</span></span>

  * <span data-ttu-id="81c42-136">Um sicherzustellen, dass diese Komponenten installiert sind und gefunden werden können, führen Sie den folgenden Befehl über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="81c42-136">To verify these components are installed and can be found, run the following from the command line:</span></span>

    ```console
    node -v && npm -v
    ```

  * <span data-ttu-id="81c42-137">Wenn die Bereitstellung auf einer Azure-Website erfolgt, ist keine Aktion erforderlich &mdash; Node.js ist installiert und in den Serverumgebungen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="81c42-137">If deploying to an Azure web site, no action is required&mdash;Node.js is installed and available in the server environments.</span></span>

* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]

  * <span data-ttu-id="81c42-138">Unter Windows mit Visual Studio 2017 wird das SDK installiert, indem Sie die Workload **Plattformübergreifende .NET Core-Entwicklung** auswählen.</span><span class="sxs-lookup"><span data-stu-id="81c42-138">On Windows using Visual Studio 2017, the SDK is installed by selecting the **.NET Core cross-platform development** workload.</span></span>

* <span data-ttu-id="81c42-139">NuGet-Paket [Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/)</span><span class="sxs-lookup"><span data-stu-id="81c42-139">[Microsoft.AspNetCore.SpaServices](https://www.nuget.org/packages/Microsoft.AspNetCore.SpaServices/) NuGet package</span></span>

## <a name="server-side-prerendering"></a><span data-ttu-id="81c42-140">Serverseitiges Prerendering</span><span class="sxs-lookup"><span data-stu-id="81c42-140">Server-side prerendering</span></span>

<span data-ttu-id="81c42-141">Eine universelle (oder auch isomorphe) Anwendung ist eine JavaScript-Anwendung, die sowohl auf dem Server als auch auf dem Client ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="81c42-141">A universal (also known as isomorphic) application is a JavaScript application capable of running both on the server and the client.</span></span> <span data-ttu-id="81c42-142">Angular, React und andere beliebte Frameworks bieten eine universelle Plattform für diese Art der Anwendungsentwicklung.</span><span class="sxs-lookup"><span data-stu-id="81c42-142">Angular, React, and other popular frameworks provide a universal platform for this application development style.</span></span> <span data-ttu-id="81c42-143">Das Konzept besteht darin, zunächst die Frameworkkomponenten auf dem Server über Node.js zu rendern und dann die weitere Ausführung an den Client zu delegieren.</span><span class="sxs-lookup"><span data-stu-id="81c42-143">The idea is to first render the framework components on the server via Node.js, and then delegate further execution to the client.</span></span>

<span data-ttu-id="81c42-144">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) für ASP.NET Core von SpaServices vereinfachen die Implementierung von serverseitigem Prerendering, indem die JavaScript-Funktionen auf dem Server aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-144">ASP.NET Core [Tag Helpers](xref:mvc/views/tag-helpers/intro) provided by SpaServices simplify the implementation of server-side prerendering by invoking the JavaScript functions on the server.</span></span>

### <a name="server-side-prerendering-prerequisites"></a><span data-ttu-id="81c42-145">Voraussetzungen für serverseitiges Prerendering</span><span class="sxs-lookup"><span data-stu-id="81c42-145">Server-side prerendering prerequisites</span></span>

<span data-ttu-id="81c42-146">Installieren Sie das npm-Paket [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering):</span><span class="sxs-lookup"><span data-stu-id="81c42-146">Install the [aspnet-prerendering](https://www.npmjs.com/package/aspnet-prerendering) npm package:</span></span>

```console
npm i -S aspnet-prerendering
```

### <a name="server-side-prerendering-configuration"></a><span data-ttu-id="81c42-147">Konfiguration des serverseitigen Prerenderings</span><span class="sxs-lookup"><span data-stu-id="81c42-147">Server-side prerendering configuration</span></span>

<span data-ttu-id="81c42-148">Die Taghilfsprogramme werden durch die Registrierung des Namespaces in der Datei *_ViewImports.cshtml* des Projekts zugänglich gemacht:</span><span class="sxs-lookup"><span data-stu-id="81c42-148">The Tag Helpers are made discoverable via namespace registration in the project's *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/_ViewImports.cshtml?highlight=3)]

<span data-ttu-id="81c42-149">Diese Taghilfsprogramme beseitigen die Komplexität der direkten Kommunikation mit Low-Level-APIs, indem sie eine HTML-ähnliche Syntax innerhalb der Razor-Ansicht verwenden:</span><span class="sxs-lookup"><span data-stu-id="81c42-149">These Tag Helpers abstract away the intricacies of communicating directly with low-level APIs by leveraging an HTML-like syntax inside the Razor view:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=5)]

### <a name="asp-prerender-module-tag-helper"></a><span data-ttu-id="81c42-150">Taghilfsprogramm „asp-prerender-module“</span><span class="sxs-lookup"><span data-stu-id="81c42-150">asp-prerender-module Tag Helper</span></span>

<span data-ttu-id="81c42-151">Das Taghilfsprogramm `asp-prerender-module`, das im vorherigen Codebeispiel verwendet wird, führt *ClientApp/dist/main-server. js* auf dem Server über Node.js aus.</span><span class="sxs-lookup"><span data-stu-id="81c42-151">The `asp-prerender-module` Tag Helper, used in the preceding code example, executes *ClientApp/dist/main-server.js* on the server via Node.js.</span></span> <span data-ttu-id="81c42-152">Der Übersichtlichkeit halber ist die Datei *main-server.js* ein Artefakt der TypeScript-zu-JavaScript-Transpilationstask im [Webpack](https://webpack.github.io/)-Erstellungsprozess.</span><span class="sxs-lookup"><span data-stu-id="81c42-152">For clarity's sake, *main-server.js* file is an artifact of the TypeScript-to-JavaScript transpilation task in the [Webpack](https://webpack.github.io/) build process.</span></span> <span data-ttu-id="81c42-153">Webpack definiert einen Einstiegspunktalias von `main-server`; und die Durchquerung des Abhängigkeitsdiagramms für diesen Alias beginnt bei der Datei *ClientApp/boot-server.ts* :</span><span class="sxs-lookup"><span data-stu-id="81c42-153">Webpack defines an entry point alias of `main-server`; and, traversal of the dependency graph for this alias begins at the *ClientApp/boot-server.ts* file:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=53)]

<span data-ttu-id="81c42-154">Im folgenden Angular-Beispiel verwendet die Datei *ClientApp/boot-server.ts* die Funktion `createServerRenderer` und den Typ `RenderResult` des npm-Pakets `aspnet-prerendering`, um das Serverrendering über Node.js zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="81c42-154">In the following Angular example, the *ClientApp/boot-server.ts* file utilizes the `createServerRenderer` function and `RenderResult` type of the `aspnet-prerendering` npm package to configure server rendering via Node.js.</span></span> <span data-ttu-id="81c42-155">Das HTML-Markup, das für das serverseitige Rendering bestimmt ist, wird an einen Auflösungsfunktionsaufruf übergeben, der ein stark typisiertes JavaScript-`Promise`-Objekt umschließt.</span><span class="sxs-lookup"><span data-stu-id="81c42-155">The HTML markup destined for server-side rendering is passed to a resolve function call, which is wrapped in a strongly-typed JavaScript `Promise` object.</span></span> <span data-ttu-id="81c42-156">Wichtig ist, dass das `Promise`-Objekt das HTML-Markup asynchron an die Seite übermittelt, damit es in das DOM-Platzhalterelement eingefügt werden kann.</span><span class="sxs-lookup"><span data-stu-id="81c42-156">The `Promise` object's significance is that it asynchronously supplies the HTML markup to the page for injection in the DOM's placeholder element.</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-34,79-)]

### <a name="asp-prerender-data-tag-helper"></a><span data-ttu-id="81c42-157">Taghilfsprogramm „asp-prerender-data“</span><span class="sxs-lookup"><span data-stu-id="81c42-157">asp-prerender-data Tag Helper</span></span>

<span data-ttu-id="81c42-158">In Verbindung mit dem Taghilfsprogramm `asp-prerender-module` kann das Taghilfsprogramm `asp-prerender-data` verwendet werden, um kontextbezogene Informationen aus der Razor-Ansicht an den serverseitigen JavaScript-Code zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="81c42-158">When coupled with the `asp-prerender-module` Tag Helper, the `asp-prerender-data` Tag Helper can be used to pass contextual information from the Razor view to the server-side JavaScript.</span></span> <span data-ttu-id="81c42-159">Das folgende Markup übergibt z. B. Benutzerdaten an das `main-server`-Modul:</span><span class="sxs-lookup"><span data-stu-id="81c42-159">For example, the following markup passes user data to the `main-server` module:</span></span>

[!code-cshtml[](../client-side/spa-services/sample/SpaServicesSampleApp/Views/Home/Index.cshtml?range=9-12)]

<span data-ttu-id="81c42-160">Das empfangene `UserName`-Argument wird mit dem integrierten JSON-Serialisierungsmodul serialisiert und im `params.data`-Objekt gespeichert.</span><span class="sxs-lookup"><span data-stu-id="81c42-160">The received `UserName` argument is serialized using the built-in JSON serializer and is stored in the `params.data` object.</span></span> <span data-ttu-id="81c42-161">Im folgenden Angular-Beispiel werden die Daten verwendet, um einen personalisierten Gruß innerhalb eines `h1`-Elements zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="81c42-161">In the following Angular example, the data is used to construct a personalized greeting within an `h1` element:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,38-52,79-)]

<span data-ttu-id="81c42-162">Eigenschaftsnamen, die in Taghilfsprogramme übergeben werden, werden mit **PascalCase** -Notation dargestellt.</span><span class="sxs-lookup"><span data-stu-id="81c42-162">Property names passed in Tag Helpers are represented with **PascalCase** notation.</span></span> <span data-ttu-id="81c42-163">Dies steht im Gegensatz zu JavaScript, wo die gleichen Eigenschaftsnamen mit **camelCase** dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-163">Contrast that to JavaScript, where the same property names are represented with **camelCase**.</span></span> <span data-ttu-id="81c42-164">Dieser Unterschied ist auf die Standardkonfiguration der JSON-Serialisierung zurückzuführen.</span><span class="sxs-lookup"><span data-stu-id="81c42-164">The default JSON serialization configuration is responsible for this difference.</span></span>

<span data-ttu-id="81c42-165">Um das vorherige Codebeispiel zu erweitern, können Daten vom Server an die Ansicht durch Umwandlung der `globals`-Eigenschaft, die der `resolve`-Funktion zur Verfügung gestellt wird, übergeben werden:</span><span class="sxs-lookup"><span data-stu-id="81c42-165">To expand upon the preceding code example, data can be passed from the server to the view by hydrating the `globals` property provided to the `resolve` function:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/boot-server.ts?range=6,10-21,57-77,79-)]

<span data-ttu-id="81c42-166">Das innerhalb des `globals`-Objekts definierte `postList`-Array wird an das globale `window`-Objekt des Browsers angefügt.</span><span class="sxs-lookup"><span data-stu-id="81c42-166">The `postList` array defined inside the `globals` object is attached to the browser's global `window` object.</span></span> <span data-ttu-id="81c42-167">Durch dieses Verschieben der Variablen in den globalen Bereich wird doppelter Arbeitsaufwand vermieden, insbesondere wenn dieselben Daten einmal auf den Server und ein zweites Mal auf den Client geladen werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-167">This variable hoisting to global scope eliminates duplication of effort, particularly as it pertains to loading the same data once on the server and again on the client.</span></span>

![Globale postList-Variable, angefügt an window-Objekt](spa-services/_static/global_variable.png)

## <a name="webpack-dev-middleware"></a><span data-ttu-id="81c42-169">Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="81c42-169">Webpack Dev Middleware</span></span>

<span data-ttu-id="81c42-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) führt einen optimierten Entwicklungsworkflow ein, wobei Webpack Ressourcen nach Bedarf erstellt.</span><span class="sxs-lookup"><span data-stu-id="81c42-170">[Webpack Dev Middleware](https://webpack.js.org/guides/development/#using-webpack-dev-middleware) introduces a streamlined development workflow whereby Webpack builds resources on demand.</span></span> <span data-ttu-id="81c42-171">Die Middleware kompiliert und bedient automatisch die clientseitigen Ressourcen, wenn eine Seite im Browser neu geladen wird.</span><span class="sxs-lookup"><span data-stu-id="81c42-171">The middleware automatically compiles and serves client-side resources when a page is reloaded in the browser.</span></span> <span data-ttu-id="81c42-172">Der alternative Ansatz besteht darin, Webpack manuell über das npm-Buildskript des Projekts aufzurufen, wenn sich eine Abhängigkeit von einem Drittanbieter oder der benutzerdefinierte Code ändert.</span><span class="sxs-lookup"><span data-stu-id="81c42-172">The alternate approach is to manually invoke Webpack via the project's npm build script when a third-party dependency or the custom code changes.</span></span> <span data-ttu-id="81c42-173">Ein npm-Buildskript in der Datei *package.json* wird im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="81c42-173">An npm build script in the *package.json* file is shown in the following example:</span></span>

```json
"build": "npm run build:vendor && npm run build:custom",
```

### <a name="webpack-dev-middleware-prerequisites"></a><span data-ttu-id="81c42-174">Voraussetzungen für Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="81c42-174">Webpack Dev Middleware prerequisites</span></span>

<span data-ttu-id="81c42-175">Installieren Sie das npm-Paket [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack):</span><span class="sxs-lookup"><span data-stu-id="81c42-175">Install the [aspnet-webpack](https://www.npmjs.com/package/aspnet-webpack) npm package:</span></span>

```console
npm i -D aspnet-webpack
```

### <a name="webpack-dev-middleware-configuration"></a><span data-ttu-id="81c42-176">Konfiguration von Webpack Dev Middleware</span><span class="sxs-lookup"><span data-stu-id="81c42-176">Webpack Dev Middleware configuration</span></span>

<span data-ttu-id="81c42-177">Webpack Dev Middleware wird in der HTTP-Anforderungspipeline über den folgenden Code in der Datei *Startup.cs* mit der `Configure`-Methode registriert:</span><span class="sxs-lookup"><span data-stu-id="81c42-177">Webpack Dev Middleware is registered into the HTTP request pipeline via the following code in the *Startup.cs* file's `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_WebpackMiddlewareRegistration&highlight=4)]

<span data-ttu-id="81c42-178">Die `UseWebpackDevMiddleware`-Erweiterungsmethode muss aufgerufen werden, bevor das [Registrieren von statischem Dateihosting](xref:fundamentals/static-files) über die `UseStaticFiles`-Erweiterungsmethode erfolgt.</span><span class="sxs-lookup"><span data-stu-id="81c42-178">The `UseWebpackDevMiddleware` extension method must be called before [registering static file hosting](xref:fundamentals/static-files) via the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="81c42-179">Aus Sicherheitsgründen sollten Sie die Middleware nur registrieren, wenn die App im Entwicklungsmodus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="81c42-179">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="81c42-180">Die Eigenschaft `output.publicPath` der Datei *webpack.config.js* weist die Middleware an, den Ordner `dist` auf Änderungen zu überwachen:</span><span class="sxs-lookup"><span data-stu-id="81c42-180">The *webpack.config.js* file's `output.publicPath` property tells the middleware to watch the `dist` folder for changes:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,13-16)]

## <a name="hot-module-replacement"></a><span data-ttu-id="81c42-181">Hot Module Replacement</span><span class="sxs-lookup"><span data-stu-id="81c42-181">Hot Module Replacement</span></span>

<span data-ttu-id="81c42-182">Stellen Sie sich die Funktion [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) von Webpack als eine Weiterentwicklung von [Webpack Dev Middleware](#webpack-dev-middleware) vor.</span><span class="sxs-lookup"><span data-stu-id="81c42-182">Think of Webpack's [Hot Module Replacement](https://webpack.js.org/concepts/hot-module-replacement/) (HMR) feature as an evolution of [Webpack Dev Middleware](#webpack-dev-middleware).</span></span> <span data-ttu-id="81c42-183">HMR bietet dieselben Vorteile, optimiert aber den Entwicklungsworkflow zusätzlich, indem der Seiteninhalt nach der Zusammenstellung der Änderungen automatisch aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="81c42-183">HMR introduces all the same benefits, but it further streamlines the development workflow by automatically updating page content after compiling the changes.</span></span> <span data-ttu-id="81c42-184">Verwechseln Sie dies nicht mit einer Aktualisierung des Browsers, die den aktuellen In-Memory-Status und die Debugsitzung der SPA beeinträchtigen würde.</span><span class="sxs-lookup"><span data-stu-id="81c42-184">Don't confuse this with a refresh of the browser, which would interfere with the current in-memory state and debugging session of the SPA.</span></span> <span data-ttu-id="81c42-185">Es gibt einen Livelink zwischen dem Webpack Dev Middleware-Dienst und dem Browser, was bedeutet, dass Änderungen in den Browser per Push übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-185">There's a live link between the Webpack Dev Middleware service and the browser, which means changes are pushed to the browser.</span></span>

### <a name="hot-module-replacement-prerequisites"></a><span data-ttu-id="81c42-186">Voraussetzungen für Hot Module Replacement</span><span class="sxs-lookup"><span data-stu-id="81c42-186">Hot Module Replacement prerequisites</span></span>

<span data-ttu-id="81c42-187">Installieren Sie das npm-Paket [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware):</span><span class="sxs-lookup"><span data-stu-id="81c42-187">Install the [webpack-hot-middleware](https://www.npmjs.com/package/webpack-hot-middleware) npm package:</span></span>

```console
npm i -D webpack-hot-middleware
```

### <a name="hot-module-replacement-configuration"></a><span data-ttu-id="81c42-188">Konfiguration von Hot Module Replacement</span><span class="sxs-lookup"><span data-stu-id="81c42-188">Hot Module Replacement configuration</span></span>

<span data-ttu-id="81c42-189">Die HMR-Komponente muss in der HTTP-Anforderungspipeline von MVC in der `Configure`-Methode registriert sein:</span><span class="sxs-lookup"><span data-stu-id="81c42-189">The HMR component must be registered into MVC's HTTP request pipeline in the `Configure` method:</span></span>

```csharp
app.UseWebpackDevMiddleware(new WebpackDevMiddlewareOptions {
    HotModuleReplacement = true
});
```

<span data-ttu-id="81c42-190">Wie bei der [Webpack Dev Middleware](#webpack-dev-middleware) muss die `UseWebpackDevMiddleware`-Erweiterungsmethode vor der `UseStaticFiles`-Erweiterungsmethode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-190">As was true with [Webpack Dev Middleware](#webpack-dev-middleware), the `UseWebpackDevMiddleware` extension method must be called before the `UseStaticFiles` extension method.</span></span> <span data-ttu-id="81c42-191">Aus Sicherheitsgründen sollten Sie die Middleware nur registrieren, wenn die App im Entwicklungsmodus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="81c42-191">For security reasons, register the middleware only when the app runs in development mode.</span></span>

<span data-ttu-id="81c42-192">Die Datei *webpack.config.js* muss ein `plugins`-Array definieren, auch wenn es leer gelassen wurde:</span><span class="sxs-lookup"><span data-stu-id="81c42-192">The *webpack.config.js* file must define a `plugins` array, even if it's left empty:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/webpack.config.js?range=6,25)]

<span data-ttu-id="81c42-193">Nach dem Laden der Anwendung im Browser sehen Sie auf der Registerkarte „Konsole“ der Entwicklertools eine Bestätigung der HMR-Aktivierung:</span><span class="sxs-lookup"><span data-stu-id="81c42-193">After loading the app in the browser, the developer tools' Console tab provides confirmation of HMR activation:</span></span>

![Verbindungsmeldung von Hot Module Replacement](spa-services/_static/hmr_connected.png)

## <a name="routing-helpers"></a><span data-ttu-id="81c42-195">Hilfsprogramme für das Routing</span><span class="sxs-lookup"><span data-stu-id="81c42-195">Routing helpers</span></span>

<span data-ttu-id="81c42-196">In den meisten ASP.NET Core-basierten SPAs wird neben dem serverseitigen Routing oft auch ein clientseitiges Routing gewünscht.</span><span class="sxs-lookup"><span data-stu-id="81c42-196">In most ASP.NET Core-based SPAs, client-side routing is often desired in addition to server-side routing.</span></span> <span data-ttu-id="81c42-197">Die SPA- und MVC-Routingsysteme können unabhängig und ohne Beeinträchtigung betrieben werden.</span><span class="sxs-lookup"><span data-stu-id="81c42-197">The SPA and MVC routing systems can work independently without interference.</span></span> <span data-ttu-id="81c42-198">Es gibt jedoch einen Grenzfall, der eine Herausforderung darstellt: die Identifizierung von 404-HTTP-Antworten.</span><span class="sxs-lookup"><span data-stu-id="81c42-198">There's, however, one edge case posing challenges: identifying 404 HTTP responses.</span></span>

<span data-ttu-id="81c42-199">Denken Sie an das Szenario, in dem eine Route ohne Erweiterung von `/some/page` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="81c42-199">Consider the scenario in which an extensionless route of `/some/page` is used.</span></span> <span data-ttu-id="81c42-200">Angenommen, die Anforderung stimmt nicht mit dem Muster einer serverseitigen Route überein, aber deren Muster stimmt mit einer clientseitigen Route überein.</span><span class="sxs-lookup"><span data-stu-id="81c42-200">Assume the request doesn't pattern-match a server-side route, but its pattern does match a client-side route.</span></span> <span data-ttu-id="81c42-201">Stellen Sie sich nun eine eingehende Anforderung für `/images/user-512.png` vor, bei der in der Regel davon ausgegangen wird, dass sie eine Bilddatei auf dem Server findet.</span><span class="sxs-lookup"><span data-stu-id="81c42-201">Now consider an incoming request for `/images/user-512.png`, which generally expects to find an image file on the server.</span></span> <span data-ttu-id="81c42-202">Wenn dieser angeforderte Ressourcenpfad mit keiner serverseitigen Route oder statischen Datei übereinstimmt, ist es unwahrscheinlich, dass die clientseitige Anwendung ihn verarbeiten würde, da normalerweise ein 404-HTTP-Statuscode zurückgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="81c42-202">If that requested resource path doesn't match any server-side route or static file, it's unlikely that the client-side application would handle it&mdash;generally returning a 404 HTTP status code is desired.</span></span>

### <a name="routing-helpers-prerequisites"></a><span data-ttu-id="81c42-203">Voraussetzungen für Hilfsprogramme für das Routing</span><span class="sxs-lookup"><span data-stu-id="81c42-203">Routing helpers prerequisites</span></span>

<span data-ttu-id="81c42-204">Installieren Sie das clientseitige npm-Paket für das Routing.</span><span class="sxs-lookup"><span data-stu-id="81c42-204">Install the client-side routing npm package.</span></span> <span data-ttu-id="81c42-205">Verwenden von Angular als Beispiel:</span><span class="sxs-lookup"><span data-stu-id="81c42-205">Using Angular as an example:</span></span>

```console
npm i -S @angular/router
```

### <a name="routing-helpers-configuration"></a><span data-ttu-id="81c42-206">Konfiguration der Hilfsprogramme für das Routing</span><span class="sxs-lookup"><span data-stu-id="81c42-206">Routing helpers configuration</span></span>

<span data-ttu-id="81c42-207">Eine Erweiterungsmethode mit dem Namen `MapSpaFallbackRoute` wird in der `Configure`-Methode verwendet:</span><span class="sxs-lookup"><span data-stu-id="81c42-207">An extension method named `MapSpaFallbackRoute` is used in the `Configure` method:</span></span>

[!code-csharp[](../client-side/spa-services/sample/SpaServicesSampleApp/Startup.cs?name=snippet_MvcRoutingTable&highlight=7-9)]

<span data-ttu-id="81c42-208">Routen werden in der Reihenfolge ausgewertet, in der sie konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="81c42-208">Routes are evaluated in the order in which they're configured.</span></span> <span data-ttu-id="81c42-209">Folglich wird die `default`-Route im vorangehenden Codebeispiel zuerst für den Musterabgleich verwendet.</span><span class="sxs-lookup"><span data-stu-id="81c42-209">Consequently, the `default` route in the preceding code example is used first for pattern matching.</span></span>

## <a name="create-a-new-project"></a><span data-ttu-id="81c42-210">Erstellt ein neues Projekt</span><span class="sxs-lookup"><span data-stu-id="81c42-210">Create a new project</span></span>

<span data-ttu-id="81c42-211">JavaScript Services stellt vorkonfigurierte Anwendungsvorlagen bereit.</span><span class="sxs-lookup"><span data-stu-id="81c42-211">JavaScript Services provide pre-configured application templates.</span></span> <span data-ttu-id="81c42-212">SpaServices wird in diesen Vorlagen zusammen mit verschiedenen Frameworks und Bibliotheken wie Angular, React und Redux verwendet.</span><span class="sxs-lookup"><span data-stu-id="81c42-212">SpaServices is used in these templates in conjunction with different frameworks and libraries such as Angular, React, and Redux.</span></span>

<span data-ttu-id="81c42-213">Diese Vorlagen können mit dem folgenden Befehl über die .NET Core-CLI installiert werden:</span><span class="sxs-lookup"><span data-stu-id="81c42-213">These templates can be installed via the .NET Core CLI by running the following command:</span></span>

```dotnetcli
dotnet new --install Microsoft.AspNetCore.SpaTemplates::*
```

<span data-ttu-id="81c42-214">Es wird eine Liste der verfügbaren SPA-Vorlagen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="81c42-214">A list of available SPA templates is displayed:</span></span>

| <span data-ttu-id="81c42-215">Vorlagen</span><span class="sxs-lookup"><span data-stu-id="81c42-215">Templates</span></span>                                 | <span data-ttu-id="81c42-216">Kurzname</span><span class="sxs-lookup"><span data-stu-id="81c42-216">Short Name</span></span> | <span data-ttu-id="81c42-217">Sprache</span><span class="sxs-lookup"><span data-stu-id="81c42-217">Language</span></span> | <span data-ttu-id="81c42-218">Tags</span><span class="sxs-lookup"><span data-stu-id="81c42-218">Tags</span></span>        |
| ------------------------------------------| :--------: | :------: | :---------: |
| <span data-ttu-id="81c42-219">MVC ASP.NET Core mit Angular</span><span class="sxs-lookup"><span data-stu-id="81c42-219">MVC ASP.NET Core with Angular</span></span>             | <span data-ttu-id="81c42-220">angular</span><span class="sxs-lookup"><span data-stu-id="81c42-220">angular</span></span>    | <span data-ttu-id="81c42-221">[C#]</span><span class="sxs-lookup"><span data-stu-id="81c42-221">[C#]</span></span>     | <span data-ttu-id="81c42-222">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="81c42-222">Web/MVC/SPA</span></span> |
| <span data-ttu-id="81c42-223">MVC ASP.NET Core mit React.js</span><span class="sxs-lookup"><span data-stu-id="81c42-223">MVC ASP.NET Core with React.js</span></span>            | <span data-ttu-id="81c42-224">react</span><span class="sxs-lookup"><span data-stu-id="81c42-224">react</span></span>      | <span data-ttu-id="81c42-225">[C#]</span><span class="sxs-lookup"><span data-stu-id="81c42-225">[C#]</span></span>     | <span data-ttu-id="81c42-226">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="81c42-226">Web/MVC/SPA</span></span> |
| <span data-ttu-id="81c42-227">MVC ASP.NET Core mit React.js und Redux</span><span class="sxs-lookup"><span data-stu-id="81c42-227">MVC ASP.NET Core with React.js and Redux</span></span>  | <span data-ttu-id="81c42-228">reactredux</span><span class="sxs-lookup"><span data-stu-id="81c42-228">reactredux</span></span> | <span data-ttu-id="81c42-229">[C#]</span><span class="sxs-lookup"><span data-stu-id="81c42-229">[C#]</span></span>     | <span data-ttu-id="81c42-230">Web/MVC/SPA</span><span class="sxs-lookup"><span data-stu-id="81c42-230">Web/MVC/SPA</span></span> |

<span data-ttu-id="81c42-231">Wenn Sie ein neues Projekt mit einer der SPA-Vorlagen erstellen möchten, schließen Sie den **Kurznamen** der Vorlage in den [dotnet new](/dotnet/core/tools/dotnet-new)-Befehl ein.</span><span class="sxs-lookup"><span data-stu-id="81c42-231">To create a new project using one of the SPA templates, include the **Short Name** of the template in the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="81c42-232">Der folgende Befehl erstellt eine Angular-Anwendung mit ASP.NET Core MVC, die für die Serverseite konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="81c42-232">The following command creates an Angular application with ASP.NET Core MVC configured for the server side:</span></span>

```dotnetcli
dotnet new angular
```

### <a name="set-the-runtime-configuration-mode"></a><span data-ttu-id="81c42-233">Festlegen des Laufzeitkonfigurationsmodus</span><span class="sxs-lookup"><span data-stu-id="81c42-233">Set the runtime configuration mode</span></span>

<span data-ttu-id="81c42-234">Es gibt zwei primäre Laufzeitkonfigurationsmodi:</span><span class="sxs-lookup"><span data-stu-id="81c42-234">Two primary runtime configuration modes exist:</span></span>

* <span data-ttu-id="81c42-235">**Entwicklung:**</span><span class="sxs-lookup"><span data-stu-id="81c42-235">**Development** :</span></span>
  * <span data-ttu-id="81c42-236">Enthält Quellzuordnungsdateien zum Vereinfachen des Debuggens.</span><span class="sxs-lookup"><span data-stu-id="81c42-236">Includes source maps to ease debugging.</span></span>
  * <span data-ttu-id="81c42-237">Der clientseitige Code wird nicht optimiert, um die Leistung zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="81c42-237">Doesn't optimize the client-side code for performance.</span></span>
* <span data-ttu-id="81c42-238">**Produktion:**</span><span class="sxs-lookup"><span data-stu-id="81c42-238">**Production** :</span></span>
  * <span data-ttu-id="81c42-239">Schließt Quellzuordnungsdateien aus.</span><span class="sxs-lookup"><span data-stu-id="81c42-239">Excludes source maps.</span></span>
  * <span data-ttu-id="81c42-240">Optimiert den clientseitigen Code über Bündelung und Minimierung.</span><span class="sxs-lookup"><span data-stu-id="81c42-240">Optimizes the client-side code via bundling and minification.</span></span>

<span data-ttu-id="81c42-241">ASP.NET Core verwendet eine Umgebungsvariable namens `ASPNETCORE_ENVIRONMENT`, um den Konfigurationsmodus zu speichern.</span><span class="sxs-lookup"><span data-stu-id="81c42-241">ASP.NET Core uses an environment variable named `ASPNETCORE_ENVIRONMENT` to store the configuration mode.</span></span> <span data-ttu-id="81c42-242">Weitere Informationen finden Sie unter [Festlegen der Umgebung](xref:fundamentals/environments#set-the-environment).</span><span class="sxs-lookup"><span data-stu-id="81c42-242">For more information, see [Set the environment](xref:fundamentals/environments#set-the-environment).</span></span>

### <a name="run-with-net-core-cli"></a><span data-ttu-id="81c42-243">Ausführen mit der .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="81c42-243">Run with .NET Core CLI</span></span>

<span data-ttu-id="81c42-244">Stellen Sie die erforderlichen NuGet- und npm-Pakete wieder her, indem Sie den folgenden Befehl im Stammverzeichnis des Projekts ausführen:</span><span class="sxs-lookup"><span data-stu-id="81c42-244">Restore the required NuGet and npm packages by running the following command at the project root:</span></span>

```dotnetcli
dotnet restore && npm i
```

<span data-ttu-id="81c42-245">Erstellen Sie die Anwendung, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="81c42-245">Build and run the application:</span></span>

```dotnetcli
dotnet run
```

<span data-ttu-id="81c42-246">Die Anwendung startet auf Localhost entsprechend dem [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode).</span><span class="sxs-lookup"><span data-stu-id="81c42-246">The application starts on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span> <span data-ttu-id="81c42-247">Wenn Sie im Browser zu `http://localhost:5000` navigieren, wird die Landing Page angezeigt.</span><span class="sxs-lookup"><span data-stu-id="81c42-247">Navigating to `http://localhost:5000` in the browser displays the landing page.</span></span>

### <a name="run-with-visual-studio-2017"></a><span data-ttu-id="81c42-248">Ausführen mit Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="81c42-248">Run with Visual Studio 2017</span></span>

<span data-ttu-id="81c42-249">Öffnen Sie die Datei *.csproj* , die mit dem [dotnet new](/dotnet/core/tools/dotnet-new)-Befehl generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="81c42-249">Open the *.csproj* file generated by the [dotnet new](/dotnet/core/tools/dotnet-new) command.</span></span> <span data-ttu-id="81c42-250">Die erforderlichen NuGet- und npm-Pakete werden beim Öffnen des Projekts automatisch wiederhergestellt.</span><span class="sxs-lookup"><span data-stu-id="81c42-250">The required NuGet and npm packages are restored automatically upon project open.</span></span> <span data-ttu-id="81c42-251">Dieser Wiederherstellungsprozess kann einige Minuten in Anspruch nehmen, und die Anwendung ist nach Abschluss des Vorgangs einsatzbereit.</span><span class="sxs-lookup"><span data-stu-id="81c42-251">This restoration process may take up to a few minutes, and the application is ready to run when it completes.</span></span> <span data-ttu-id="81c42-252">Klicken Sie auf die grüne Schaltfläche zum Ausführen, oder drücken Sie `Ctrl + F5`, und der Browser öffnet die Landing Page der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="81c42-252">Click the green run button or press `Ctrl + F5`, and the browser opens to the application's landing page.</span></span> <span data-ttu-id="81c42-253">Die Anwendung wird auf Localhost entsprechend dem [Laufzeitkonfigurationsmodus](#set-the-runtime-configuration-mode) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="81c42-253">The application runs on localhost according to the [runtime configuration mode](#set-the-runtime-configuration-mode).</span></span>

## <a name="test-the-app"></a><span data-ttu-id="81c42-254">Testen der App</span><span class="sxs-lookup"><span data-stu-id="81c42-254">Test the app</span></span>

<span data-ttu-id="81c42-255">SpaServices-Vorlagen sind vorkonfiguriert, um clientseitige Tests mit [Karma](https://karma-runner.github.io/1.0/index.html) und [Jasmine](https://jasmine.github.io/) auszuführen.</span><span class="sxs-lookup"><span data-stu-id="81c42-255">SpaServices templates are pre-configured to run client-side tests using [Karma](https://karma-runner.github.io/1.0/index.html) and [Jasmine](https://jasmine.github.io/).</span></span> <span data-ttu-id="81c42-256">Jasmine ist ein beliebtes Komponententestframework für JavaScript, wohingegen Karma ein Test Runner für diese Tests ist.</span><span class="sxs-lookup"><span data-stu-id="81c42-256">Jasmine is a popular unit testing framework for JavaScript, whereas Karma is a test runner for those tests.</span></span> <span data-ttu-id="81c42-257">Karma ist für die Verwendung mit [Webpack Dev Middleware](#webpack-dev-middleware) konfiguriert, sodass Entwickler nicht bei jeder Änderung den Test anhalten und ausführen müssen.</span><span class="sxs-lookup"><span data-stu-id="81c42-257">Karma is configured to work with the [Webpack Dev Middleware](#webpack-dev-middleware) such that the developer isn't required to stop and run the test every time changes are made.</span></span> <span data-ttu-id="81c42-258">Der Test wird automatisch ausgeführt, unabhängig davon, ob es sich um den Code mit dem Testfall oder den Testfall selbst handelt.</span><span class="sxs-lookup"><span data-stu-id="81c42-258">Whether it's the code running against the test case or the test case itself, the test runs automatically.</span></span>

<span data-ttu-id="81c42-259">Wenn Sie beispielsweise die Angular-Anwendung verwenden, werden damit zwei Jasmine-Testfälle für `CounterComponent` in der Datei *counter.component.spec.ts* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="81c42-259">Using the Angular application as an example, two Jasmine test cases are already provided for the `CounterComponent` in the *counter.component.spec.ts* file:</span></span>

[!code-typescript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/app/components/counter/counter.component.spec.ts?range=15-28)]

<span data-ttu-id="81c42-260">Öffnen Sie die Eingabeaufforderung im Verzeichnis *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="81c42-260">Open the command prompt in the *ClientApp* directory.</span></span> <span data-ttu-id="81c42-261">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="81c42-261">Run the following command:</span></span>

```console
npm test
```

<span data-ttu-id="81c42-262">Das Skript startet den Karma-Test Runner, der die Einstellungen liest, die in der Datei *karma.conf.js* definiert sind.</span><span class="sxs-lookup"><span data-stu-id="81c42-262">The script launches the Karma test runner, which reads the settings defined in the *karma.conf.js* file.</span></span> <span data-ttu-id="81c42-263">Neben anderen Einstellungen identifiziert die Datei *karma.conf.js* über das `files`-Array die auszuführenden Testdateien:</span><span class="sxs-lookup"><span data-stu-id="81c42-263">Among other settings, the *karma.conf.js* identifies the test files to be executed via its `files` array:</span></span>

[!code-javascript[](../client-side/spa-services/sample/SpaServicesSampleApp/ClientApp/test/karma.conf.js?range=4-5,8-11)]

## <a name="publish-the-app"></a><span data-ttu-id="81c42-264">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="81c42-264">Publish the app</span></span>

<span data-ttu-id="81c42-265">Weitere Informationen zur Veröffentlichung in Azure finden Sie in [diesem GitHub-Thema](https://github.com/dotnet/AspNetCore.Docs/issues/12474).</span><span class="sxs-lookup"><span data-stu-id="81c42-265">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/12474) for more information on publishing to Azure.</span></span>

<span data-ttu-id="81c42-266">Die Zusammenstellung der generierten clientseitigen Ressourcen und der veröffentlichten ASP.NET Core-Artefakte zu einem einsatzbereiten Paket kann umständlich sein.</span><span class="sxs-lookup"><span data-stu-id="81c42-266">Combining the generated client-side assets and the published ASP.NET Core artifacts into a ready-to-deploy package can be cumbersome.</span></span> <span data-ttu-id="81c42-267">Daher ist es gut, dass SpaServices diesen gesamten Veröffentlichungsprozess mit einem benutzerdefinierten MSBuild-Target namens `RunWebpack` orchestriert:</span><span class="sxs-lookup"><span data-stu-id="81c42-267">Thankfully, SpaServices orchestrates that entire publication process with a custom MSBuild target named `RunWebpack`:</span></span>

[!code-xml[](../client-side/spa-services/sample/SpaServicesSampleApp/SpaServicesSampleApp.csproj?range=31-45)]

<span data-ttu-id="81c42-268">Das MSBuild-Ziel hat die folgenden Zuständigkeiten:</span><span class="sxs-lookup"><span data-stu-id="81c42-268">The MSBuild target has the following responsibilities:</span></span>

1. <span data-ttu-id="81c42-269">Wiederherstellen der npm-Pakete</span><span class="sxs-lookup"><span data-stu-id="81c42-269">Restore the npm packages.</span></span>
1. <span data-ttu-id="81c42-270">Erstellen eines produktionsfähigen Builds der clientseitigen Drittanbieterressourcen</span><span class="sxs-lookup"><span data-stu-id="81c42-270">Create a production-grade build of the third-party, client-side assets.</span></span>
1. <span data-ttu-id="81c42-271">Erstellen eines produktionsfähigen Builds der benutzerdefinierten, clientseitigen Ressourcen</span><span class="sxs-lookup"><span data-stu-id="81c42-271">Create a production-grade build of the custom client-side assets.</span></span>
1. <span data-ttu-id="81c42-272">Kopieren der von Webpack generierten Ressourcen in den Ordner für die Veröffentlichung</span><span class="sxs-lookup"><span data-stu-id="81c42-272">Copy the Webpack-generated assets to the publish folder.</span></span>

<span data-ttu-id="81c42-273">Das MSBuild-Ziel wird aufgerufen, wenn Folgendes ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="81c42-273">The MSBuild target is invoked when running:</span></span>

```dotnetcli
dotnet publish -c Release
```

## <a name="additional-resources"></a><span data-ttu-id="81c42-274">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="81c42-274">Additional resources</span></span>

* [<span data-ttu-id="81c42-275">Angular-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="81c42-275">Angular Docs</span></span>](https://angular.io/docs)
