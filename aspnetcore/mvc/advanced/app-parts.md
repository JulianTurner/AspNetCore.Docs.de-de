---
title: 'Freigeben von Controllern, Ansichten, Razor Seiten und mehr mit Anwendungs Teilen in ASP.net Core'
author: rick-anderson
description: 'Freigeben von Controllern, anzeigen, Razor Seiten und mehr mit Anwendungs Teilen in ASP.net Core'
ms.author: riande
ms.date: 11/11/2019
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
uid: mvc/extensibility/app-parts
ms.openlocfilehash: 33deb5ff794982e0c074186bb2abb88344e8a116
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061183"
---
# <a name="share-controllers-views-no-locrazor-pages-and-more-with-application-parts"></a><span data-ttu-id="f4ee9-103">Freigeben von Controllern, Ansichten, Razor Seiten und mehr mit Anwendungs teilen</span><span class="sxs-lookup"><span data-stu-id="f4ee9-103">Share controllers, views, Razor Pages and more with Application Parts</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f4ee9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f4ee9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f4ee9-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4ee9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4ee9-106">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-106">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="f4ee9-107">Anwendungsteile ermöglichen ASP.net Core das Ermitteln von Controllern, Ansichts Komponenten, taghilfsprogramme, Razor Seiten, Razor-Kompilierungs Quellen und vieles mehr.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-107">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="f4ee9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> ist ein Anwendungspart.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-108"><xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> is an Application part.</span></span> <span data-ttu-id="f4ee9-109">`AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-109">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="f4ee9-110">[Featureanbieter](#fp) arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-110">[Feature providers](#fp) work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="f4ee9-111">Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet).</span><span class="sxs-lookup"><span data-stu-id="f4ee9-111">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="f4ee9-112">Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-112">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="f4ee9-113">Mithilfe von Anwendungs teilen können Sie eine Assembly (dll) mit Controllern, Ansichten, Razor Seiten, Razor-Kompilierungs Quellen, taghilfsprogrammen und mehr für mehrere apps freigeben.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-113">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="f4ee9-114">Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-114">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="f4ee9-115">ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-115">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="f4ee9-116">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-116">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="f4ee9-117">Laden von ASP.NET Core-Features</span><span class="sxs-lookup"><span data-stu-id="f4ee9-117">Load ASP.NET Core features</span></span>

<span data-ttu-id="f4ee9-118">Verwenden Sie die Klassen <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> und <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-118">Use the <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> and <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="f4ee9-119">Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-119">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="f4ee9-120">Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-120">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="f4ee9-121">Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-121">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/3.0sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="f4ee9-122">Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-122">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="f4ee9-123">Der `SharedController` befindet sich nicht im Projekt der App.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-123">The `SharedController` is not in the app's project.</span></span> <span data-ttu-id="f4ee9-124">Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="f4ee9-124">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/3.0sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="f4ee9-125">Einschließen von Ansichten</span><span class="sxs-lookup"><span data-stu-id="f4ee9-125">Include views</span></span>

<span data-ttu-id="f4ee9-126">Verwenden Sie eine [ Razor Klassenbibliothek](xref:razor-pages/ui-class) , um Sichten in die Assembly einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-126">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="f4ee9-127">Vermeiden des Ladens von Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f4ee9-127">Prevent loading resources</span></span>

<span data-ttu-id="f4ee9-128">Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden* .</span><span class="sxs-lookup"><span data-stu-id="f4ee9-128">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="f4ee9-129">Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-129">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="f4ee9-130">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-130">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="f4ee9-131">Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-131">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="f4ee9-132">Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-132">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="f4ee9-133">Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-133">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="f4ee9-134">Der `ApplicationPartManager` enthält Parts für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-134">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="f4ee9-135">Die Assembly der App und die abhängigen Assemblys.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-135">The app's assembly and dependent assemblies.</span></span>
* `Microsoft.AspNetCore.Mvc.ApplicationParts.CompiledRazorAssemblyPart`
* `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`
* <span data-ttu-id="f4ee9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-136">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="f4ee9-137">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-137">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

<a name="fp"></a>

## <a name="feature-providers"></a><span data-ttu-id="f4ee9-138">Featureanbieter</span><span class="sxs-lookup"><span data-stu-id="f4ee9-138">Feature providers</span></span>

<span data-ttu-id="f4ee9-139">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-139">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="f4ee9-140">Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-140">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Controllers.ControllerFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.MetadataReferenceFeatureProvider>
* <xref:Microsoft.AspNetCore.Mvc.Razor.Compilation.ViewsFeatureProvider>
* <span data-ttu-id="f4ee9-141">`internal class`[ Razor Compileditemfeatureprovider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span><span class="sxs-lookup"><span data-stu-id="f4ee9-141">`internal class` [RazorCompiledItemFeatureProvider](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Razor/src/ApplicationParts/RazorCompiledItemFeatureProvider.cs#L14)</span></span>

<span data-ttu-id="f4ee9-142">Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-142">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="f4ee9-143">Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-143">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="f4ee9-144">Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-144">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="f4ee9-145">Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-145">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="f4ee9-146">Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="f4ee9-146">Display available features</span></span>

<span data-ttu-id="f4ee9-147">Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-147">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="f4ee9-148">Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-148">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="f4ee9-149">Ermittlung in Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="f4ee9-149">Discovery in application parts</span></span>

<span data-ttu-id="f4ee9-150">Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-150">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="f4ee9-151">Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-151">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="f4ee9-152">Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-152">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="f4ee9-153">Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-153">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="f4ee9-154">Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-154">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="f4ee9-155">Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-155">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="f4ee9-156">Der Verweis kann direkt oder transitiv sein.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-156">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="f4ee9-157">Die Stammassembly muss auf das Web-SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-157">The root assembly needs to reference the Web SDK.</span></span> <span data-ttu-id="f4ee9-158">Das Framework verfügt über eine Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-158">The framework has logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f4ee9-159">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f4ee9-159">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f4ee9-160">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f4ee9-160">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f4ee9-161">Ein *Anwendungspart* ist eine Abstraktion der Ressourcen einer App.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-161">An *Application Part* is an abstraction over the resources of an app.</span></span> <span data-ttu-id="f4ee9-162">Anwendungsteile ermöglichen ASP.net Core das Ermitteln von Controllern, Ansichts Komponenten, taghilfsprogramme, Razor Seiten, Razor-Kompilierungs Quellen und vieles mehr.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-162">Application Parts allow ASP.NET Core to discover controllers, view components, tag helpers, Razor Pages, razor compilation sources, and more.</span></span> <span data-ttu-id="f4ee9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) ist ein Anwendungspart.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-163">[AssemblyPart](/dotnet/api/microsoft.aspnetcore.mvc.applicationparts.assemblypart#Microsoft_AspNetCore_Mvc_ApplicationParts_AssemblyPart) is an Application part.</span></span> <span data-ttu-id="f4ee9-164">`AssemblyPart` kapselt einen Assemblyverweis und macht Typen und Kompilierungsverweise verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-164">`AssemblyPart` encapsulates an assembly reference and exposes types and compilation references.</span></span>

<span data-ttu-id="f4ee9-165">*Featureanbieter* arbeiten mit Anwendungsparts, um die Features einer ASP.NET Core-Anwendung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-165">*Feature providers* work with application parts to populate the features of an ASP.NET Core app.</span></span> <span data-ttu-id="f4ee9-166">Anwendungsparts werden hauptsächlich dafür eingesetzt, eine App so zu konfigurieren, dass sie die ASP.NET Core-Features in einer Assembly ermittelt (oder das Laden solcher Features vermeidet).</span><span class="sxs-lookup"><span data-stu-id="f4ee9-166">The main use case for application parts is to configure an app to discover (or avoid loading) ASP.NET Core features from an assembly.</span></span> <span data-ttu-id="f4ee9-167">Ein Beispiel: Sie möchten einige gängige Funktionen in mehreren Apps gemeinsam nutzen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-167">For example, you might want to share common functionality between multiple apps.</span></span> <span data-ttu-id="f4ee9-168">Mithilfe von Anwendungs teilen können Sie eine Assembly (dll) mit Controllern, Ansichten, Razor Seiten, Razor-Kompilierungs Quellen, taghilfsprogrammen und mehr für mehrere apps freigeben.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-168">Using Application Parts, you can share an assembly (DLL) containing controllers, views, Razor Pages, razor compilation sources, Tag Helpers, and more with multiple apps.</span></span> <span data-ttu-id="f4ee9-169">Die Freigabe einer Assembly ist dem Duplizieren von Code in mehreren Projekten vorzuziehen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-169">Sharing an assembly is preferred to duplicating code in multiple projects.</span></span>

<span data-ttu-id="f4ee9-170">ASP.NET Core-Apps laden Features aus <xref:System.Web.WebPages.ApplicationPart>.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-170">ASP.NET Core apps load features from <xref:System.Web.WebPages.ApplicationPart>.</span></span> <span data-ttu-id="f4ee9-171">Die <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart>-Klasse stellt ein Anwendungspart dar, das durch eine Assembly gesichert wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-171">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.AssemblyPart> class represents an application part that's backed by an assembly.</span></span>

## <a name="load-aspnet-core-features"></a><span data-ttu-id="f4ee9-172">Laden von ASP.NET Core-Features</span><span class="sxs-lookup"><span data-stu-id="f4ee9-172">Load ASP.NET Core features</span></span>

<span data-ttu-id="f4ee9-173">Verwenden Sie die Klassen `ApplicationPart` und `AssemblyPart`, um ASP.NET Core-Features (Controller, Ansichtskomponenten usw.) zu ermitteln und zu laden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-173">Use the `ApplicationPart` and `AssemblyPart` classes to discover and load ASP.NET Core features (controllers, view components, etc.).</span></span> <span data-ttu-id="f4ee9-174">Der <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> verfolgt die verfügbaren Anwendungsparts und Featureanbieter nach.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-174">The <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.ApplicationPartManager> tracks the application parts and feature providers available.</span></span> <span data-ttu-id="f4ee9-175">Der `ApplicationPartManager` wird in `Startup.ConfigureServices` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-175">`ApplicationPartManager` is configured in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup.cs?name=snippet)]

<span data-ttu-id="f4ee9-176">Der folgende Code bietet einen alternativen Ansatz, um den `ApplicationPartManager` mithilfe von `AssemblyPart` zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-176">The following code provides an alternative approach to configuring `ApplicationPartManager` using `AssemblyPart`:</span></span>

[!code-csharp[](./app-parts/sample1/WebAppParts/Startup2.cs?name=snippet)]

<span data-ttu-id="f4ee9-177">Die beiden oben gezeigten Codebeispiele laden den `SharedController` aus einer Assembly.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-177">The preceding two code samples load the `SharedController` from an assembly.</span></span> <span data-ttu-id="f4ee9-178">Der `SharedController` befindet sich nicht im Projekt der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-178">The `SharedController` is not in the application's project.</span></span> <span data-ttu-id="f4ee9-179">Weitere Informationen finden Sie im Beispieldownload für eine [WebAppParts-Lösung](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts).</span><span class="sxs-lookup"><span data-stu-id="f4ee9-179">See the [WebAppParts solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample1/WebAppParts) sample download.</span></span>

### <a name="include-views"></a><span data-ttu-id="f4ee9-180">Einschließen von Ansichten</span><span class="sxs-lookup"><span data-stu-id="f4ee9-180">Include views</span></span>

<span data-ttu-id="f4ee9-181">Verwenden Sie eine [ Razor Klassenbibliothek](xref:razor-pages/ui-class) , um Sichten in die Assembly einzuschließen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-181">Use a [Razor class library](xref:razor-pages/ui-class) to include views in the assembly.</span></span>

### <a name="prevent-loading-resources"></a><span data-ttu-id="f4ee9-182">Vermeiden des Ladens von Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f4ee9-182">Prevent loading resources</span></span>

<span data-ttu-id="f4ee9-183">Anwendungsparts können verwendet werden, um das Laden von Ressourcen in einer bestimmten Assembly oder einem bestimmten Speicherort zu *vermeiden* .</span><span class="sxs-lookup"><span data-stu-id="f4ee9-183">Application parts can be used to *avoid* loading resources in a particular assembly or location.</span></span> <span data-ttu-id="f4ee9-184">Fügen Sie der Sammlung <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> Elemente hinzu, oder entfernen Sie Elemente aus der Sammlung, um Ressourcen auszublenden oder verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-184">Add or remove members of the  <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> collection to hide or make available resources.</span></span> <span data-ttu-id="f4ee9-185">Die Reihenfolge der Einträge in der `ApplicationParts`-Sammlung ist nicht wichtig.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-185">The order of the entries in the `ApplicationParts` collection isn't important.</span></span> <span data-ttu-id="f4ee9-186">Konfigurieren Sie den `ApplicationPartManager`, bevor er zur Konfiguration von Diensten im Container verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-186">Configure the `ApplicationPartManager` before using it to configure services in the container.</span></span> <span data-ttu-id="f4ee9-187">Beispielsweise sollten Sie den `ApplicationPartManager` vor dem Aufrufen von `AddControllersAsServices` konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-187">For example, configure the `ApplicationPartManager` before invoking `AddControllersAsServices`.</span></span> <span data-ttu-id="f4ee9-188">Rufen Sie `Remove` in der `ApplicationParts`-Sammlung auf, um eine Ressource zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-188">Call `Remove` on the `ApplicationParts` collection to remove a resource.</span></span>

<span data-ttu-id="f4ee9-189">Der folgende Code verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationParts>, um `MyDependentLibrary` aus der App zu entfernen: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span><span class="sxs-lookup"><span data-stu-id="f4ee9-189">The following code uses <xref:Microsoft.AspNetCore.Mvc.ApplicationParts> to remove `MyDependentLibrary` from the app: [!code-csharp[](./app-parts/sample1/WebAppParts/StartupRm.cs?name=snippet)]</span></span>

<span data-ttu-id="f4ee9-190">Der `ApplicationPartManager` enthält Parts für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-190">The `ApplicationPartManager` includes parts for:</span></span>

* <span data-ttu-id="f4ee9-191">Die Assembly der App und die abhängigen Assemblys.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-191">The app's assembly and dependent assemblies.</span></span>
* <span data-ttu-id="f4ee9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-192">`Microsoft.AspNetCore.Mvc.TagHelpers`.</span></span>
* <span data-ttu-id="f4ee9-193">`Microsoft.AspNetCore.Mvc.Razor`.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-193">`Microsoft.AspNetCore.Mvc.Razor`.</span></span>

## <a name="application-feature-providers"></a><span data-ttu-id="f4ee9-194">Anwendungsfeatureanbieter</span><span class="sxs-lookup"><span data-stu-id="f4ee9-194">Application feature providers</span></span>

<span data-ttu-id="f4ee9-195">Anwendungsfeatureanbieter untersuchen Anwendungsparts und bieten Features für diese.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-195">Application feature providers examine application parts and provide features for those parts.</span></span> <span data-ttu-id="f4ee9-196">Es gibt integrierte Featureanbieter für die folgenden ASP.NET Core-Features:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-196">There are built-in feature providers for the following ASP.NET Core features:</span></span>

* [<span data-ttu-id="f4ee9-197">Controller</span><span class="sxs-lookup"><span data-stu-id="f4ee9-197">Controllers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.controllers.controllerfeatureprovider)
* [<span data-ttu-id="f4ee9-198">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="f4ee9-198">Tag Helpers</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.razor.taghelpers.taghelperfeatureprovider)
* [<span data-ttu-id="f4ee9-199">Komponenten anzeigen</span><span class="sxs-lookup"><span data-stu-id="f4ee9-199">View Components</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponents.viewcomponentfeatureprovider)

<span data-ttu-id="f4ee9-200">Featureanbieter erben von <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, wobei `T` der Typ des Features ist.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-200">Feature providers inherit from <xref:Microsoft.AspNetCore.Mvc.ApplicationParts.IApplicationFeatureProvider`1>, where `T` is the type of the feature.</span></span> <span data-ttu-id="f4ee9-201">Featureanbieter können für jeden der oben aufgeführten Featuretypen implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-201">Feature providers can be implemented for any of the previously listed feature types.</span></span> <span data-ttu-id="f4ee9-202">Die Reihenfolge der Featureanbieter in `ApplicationPartManager.FeatureProviders` kann sich auf das Laufzeitverhalten auswirken.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-202">The order of feature providers in the `ApplicationPartManager.FeatureProviders` can impact run time behavior.</span></span> <span data-ttu-id="f4ee9-203">Später hinzugefügte Anbieter können auf Aktionen reagieren, die von früher hinzugefügten Anbietern ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-203">Later added providers can react to actions taken by earlier added providers.</span></span>

### <a name="display-available-features"></a><span data-ttu-id="f4ee9-204">Anzeigen verfügbarer Features</span><span class="sxs-lookup"><span data-stu-id="f4ee9-204">Display available features</span></span>

<span data-ttu-id="f4ee9-205">Die für eine App verfügbaren Features können aufgelistet werden, indem durch [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md) ein `ApplicationPartManager` angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-205">The features available to an app can be enumerated by requesting an `ApplicationPartManager` through [dependency injection](../../fundamentals/dependency-injection.md):</span></span>

[!code-csharp[](./app-parts/sample2/AppPartsSample/Controllers/FeaturesController.cs?highlight=16,25-27)]

<span data-ttu-id="f4ee9-206">Das [Downloadbeispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) verwendet den oben stehenden Code, um die App-Features anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-206">The [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/advanced/app-parts/sample2) uses the preceding code to display the app features:</span></span>

```text
Controllers:
    - FeaturesController
    - HomeController
    - HelloController
    - GenericController`1
    - GenericController`1
Tag Helpers:
    - PrerenderTagHelper
    - AnchorTagHelper
    - CacheTagHelper
    - DistributedCacheTagHelper
    - EnvironmentTagHelper
    - Additional Tag Helpers omitted for brevity.
View Components:
    - SampleViewComponent
```

## <a name="discovery-in-application-parts"></a><span data-ttu-id="f4ee9-207">Ermittlung in Anwendungsparts</span><span class="sxs-lookup"><span data-stu-id="f4ee9-207">Discovery in application parts</span></span>

<span data-ttu-id="f4ee9-208">Bei der Entwicklung mit Anwendungsparts sind HTTP-404-Fehler nicht ungewöhnlich.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-208">HTTP 404 errors are not uncommon when developing with application parts.</span></span> <span data-ttu-id="f4ee9-209">Diese Fehler werden in der Regel dadurch verursacht, dass eine essenzielle Anforderung an die Ermittlung von Anwendungsparts nicht erfüllt ist.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-209">These errors are typically caused by missing an essential requirement for how applications parts are discovered.</span></span> <span data-ttu-id="f4ee9-210">Wenn Ihre App einen HTTP-404-Fehler zurückgibt, überprüfen Sie, ob die folgenden Anforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="f4ee9-210">If your app returns an HTTP 404 error, verify the following requirements have been met:</span></span>

* <span data-ttu-id="f4ee9-211">Die `applicationName`-Einstellungen müssen auf die Stammassembly festgelegt sein, die für die Ermittlung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-211">The `applicationName` setting needs to be set to the root assembly used for discovery.</span></span> <span data-ttu-id="f4ee9-212">Die für die Ermittlung verwendete Stammassembly ist normalerweise die Einstiegspunktassembly.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-212">The root assembly used for discovery is normally the entry point assembly.</span></span>
* <span data-ttu-id="f4ee9-213">Die Stammassembly muss einen Verweis auf das für die Ermittlung verwendete Part enthalten.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-213">The root assembly needs to have a reference to the parts used for discovery.</span></span> <span data-ttu-id="f4ee9-214">Der Verweis kann direkt oder transitiv sein.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-214">The reference can be direct or transitive.</span></span>
* <span data-ttu-id="f4ee9-215">Die Stammassembly muss auf das Web-SDK verweisen.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-215">The root assembly needs to reference the Web SDK.</span></span>
  * <span data-ttu-id="f4ee9-216">Das ASP.NET Core-Framework verfügt über eine benutzerdefinierte Logik, die Attribute in die Stammassembly einfügt, die für die Ermittlung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f4ee9-216">The ASP.NET Core framework has custom build logic that stamps attributes into the root assembly that are used for discovery.</span></span>

::: moniker-end
