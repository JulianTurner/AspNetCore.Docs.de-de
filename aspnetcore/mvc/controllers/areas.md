---
title: Bereiche in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über Bereiche, ein Feature von ASP.NET MVC, das für die Organisation von verwandten Funktionalitäten in einer Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.
ms.author: riande
ms.date: 03/21/2019
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
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053526"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="0d009-103">Bereiche in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0d009-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="0d009-104">Von [Dhananjay Kumar](https://twitter.com/debug_mode) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0d009-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0d009-105">Bereiche sind eine ASP.net-Funktion, die verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:</span><span class="sxs-lookup"><span data-stu-id="0d009-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="0d009-106">Namespace für das Routing.</span><span class="sxs-lookup"><span data-stu-id="0d009-106">Namespace for routing.</span></span>
* <span data-ttu-id="0d009-107">Ordnerstruktur für Sichten und Razor Seiten.</span><span class="sxs-lookup"><span data-stu-id="0d009-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="0d009-108">Mithilfe von Bereichen wird eine Hierarchie für das Routing erstellt, indem ein weiterer Routen Parameter, `area` , zu `controller` und `action` oder einer Razor Seite hinzugefügt wird `page` .</span><span class="sxs-lookup"><span data-stu-id="0d009-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="0d009-109">Bereiche bieten eine Möglichkeit, eine ASP.net Core-Web-App in kleinere funktionale Gruppen zu partitionieren, die jeweils über einen eigenen Satz von Razor Seiten, Controllern, Ansichten und Modellen verfügen.</span><span class="sxs-lookup"><span data-stu-id="0d009-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="0d009-110">Ein Bereich ist im Grunde genommen eine Struktur in einer App.</span><span class="sxs-lookup"><span data-stu-id="0d009-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="0d009-111">In einem ASP.NET Core-Webprojekt werden logische Komponenten wie Seiten, Modelle, Controller und Ansichten in verschiedenen Ordner aufbewahrt.</span><span class="sxs-lookup"><span data-stu-id="0d009-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="0d009-112">Die ASP.NET Core-Runtime verwendet Namenskonventionen, um die Beziehung zwischen diesen Komponenten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0d009-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="0d009-113">Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="0d009-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="0d009-114">Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche.</span><span class="sxs-lookup"><span data-stu-id="0d009-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="0d009-115">Jede dieser Einheiten verfügt über einen eigenen Bereich, der Sichten, Controller, Razor Seiten und Modelle enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0d009-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="0d009-116">Die Verwendung von Bereichen in einem Projekt ist erwägenswert, wenn:</span><span class="sxs-lookup"><span data-stu-id="0d009-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="0d009-117">Ihre App aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein können.</span><span class="sxs-lookup"><span data-stu-id="0d009-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="0d009-118">Sie Ihre App partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0d009-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="0d009-119">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0d009-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="0d009-120">Das Downloadbeispiel stellt eine einfache App für Testbereiche zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="0d009-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="0d009-121">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Bereiche mit Razor Seiten](#areas-with-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="0d009-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="0d009-122">Bereiche für Controller mit Ansichten</span><span class="sxs-lookup"><span data-stu-id="0d009-122">Areas for controllers with views</span></span>

<span data-ttu-id="0d009-123">Eine typische ASP.NET Core-Web-App, die Bereiche, Controller und Ansichten verwendet, beinhaltet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0d009-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="0d009-124">Eine [Bereichsordnerstruktur](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="0d009-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="0d009-125">Controller mit dem- [`[Area]`](#attribute) Attribut, um den Controller dem Bereich zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="0d009-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="0d009-126">Die [Bereichsroute, die dem Startup hinzugefügt wurde](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="0d009-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="0d009-127">Bereichsordnerstruktur</span><span class="sxs-lookup"><span data-stu-id="0d009-127">Area folder structure</span></span>

<span data-ttu-id="0d009-128">Stellen Sie sich eine App vor, die zwei logische Gruppen hat, *Produkte* und *Dienste* .</span><span class="sxs-lookup"><span data-stu-id="0d009-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="0d009-129">Wenn Bereiche verwendet werden, würde die Ordnerstruktur ähnlich dem Folgenden aussehen:</span><span class="sxs-lookup"><span data-stu-id="0d009-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="0d009-130">Projektname</span><span class="sxs-lookup"><span data-stu-id="0d009-130">Project name</span></span>
  * <span data-ttu-id="0d009-131">Bereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-131">Areas</span></span>
    * <span data-ttu-id="0d009-132">Produkte</span><span class="sxs-lookup"><span data-stu-id="0d009-132">Products</span></span>
      * <span data-ttu-id="0d009-133">Controller</span><span class="sxs-lookup"><span data-stu-id="0d009-133">Controllers</span></span>
        * <span data-ttu-id="0d009-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-134">HomeController.cs</span></span>
        * <span data-ttu-id="0d009-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-135">ManageController.cs</span></span>
      * <span data-ttu-id="0d009-136">Sichten</span><span class="sxs-lookup"><span data-stu-id="0d009-136">Views</span></span>
        * <span data-ttu-id="0d009-137">Startseite</span><span class="sxs-lookup"><span data-stu-id="0d009-137">Home</span></span>
          * <span data-ttu-id="0d009-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-138">Index.cshtml</span></span>
        * <span data-ttu-id="0d009-139">Verwalten</span><span class="sxs-lookup"><span data-stu-id="0d009-139">Manage</span></span>
          * <span data-ttu-id="0d009-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-140">Index.cshtml</span></span>
          * <span data-ttu-id="0d009-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-141">About.cshtml</span></span>
    * <span data-ttu-id="0d009-142">Dienste</span><span class="sxs-lookup"><span data-stu-id="0d009-142">Services</span></span>
      * <span data-ttu-id="0d009-143">Controller</span><span class="sxs-lookup"><span data-stu-id="0d009-143">Controllers</span></span>
        * <span data-ttu-id="0d009-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-144">HomeController.cs</span></span>
      * <span data-ttu-id="0d009-145">Sichten</span><span class="sxs-lookup"><span data-stu-id="0d009-145">Views</span></span>
        * <span data-ttu-id="0d009-146">Startseite</span><span class="sxs-lookup"><span data-stu-id="0d009-146">Home</span></span>
          * <span data-ttu-id="0d009-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-147">Index.cshtml</span></span>

<span data-ttu-id="0d009-148">Während das vorherige Layout typisch ist, wenn Bereiche verwendet werden, müssen nur die Ansichtsdateien diese Ordnerstruktur verwenden.</span><span class="sxs-lookup"><span data-stu-id="0d009-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="0d009-149">Die Ansichtsermittlung sucht nach einer passenden Bereichsansichtsdatei im folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="0d009-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="0d009-150">Zuordnen eines Controllers zu einem Bereich</span><span class="sxs-lookup"><span data-stu-id="0d009-150">Associate the controller with an Area</span></span>

<span data-ttu-id="0d009-151">Bereichs Controller werden mit dem [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut angegeben:</span><span class="sxs-lookup"><span data-stu-id="0d009-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="0d009-152">Hinzufügen einer Bereichsroute</span><span class="sxs-lookup"><span data-stu-id="0d009-152">Add Area route</span></span>

<span data-ttu-id="0d009-153">Bereichs Routen verwenden normalerweise  [herkömmliches Routing](xref:mvc/controllers/routing#cr) anstelle von [Attribut Routing](xref:mvc/controllers/routing#ar).</span><span class="sxs-lookup"><span data-stu-id="0d009-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="0d009-154">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="0d009-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="0d009-155">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="0d009-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="0d009-156">`{area:...}` kann als Token in Routenvorlagen verwendet werden, wenn der URL-Raum in allen Bereichen einheitlich ist:</span><span class="sxs-lookup"><span data-stu-id="0d009-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="0d009-157">Im vorherigen Code wendet `exists` eine Einschränkung an: Die Route muss mit einem Bereich übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="0d009-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="0d009-158">Verwenden `{area:...}` mit `MapControllerRoute` :</span><span class="sxs-lookup"><span data-stu-id="0d009-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="0d009-159">Ist der am wenigsten komplizierte Mechanismus zum Hinzufügen von Routing zu Bereichen.</span><span class="sxs-lookup"><span data-stu-id="0d009-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="0d009-160">Entspricht allen Controllern mit dem- `[Area("Area name")]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="0d009-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="0d009-161">Im folgenden Code wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> verwendet, um zwei benannte Bereichsrouten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0d009-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="0d009-162">Weitere Informationen finden Sie im Artikel [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="0d009-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="0d009-163">Erstellen von Links mit MVC-Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-163">Link generation with MVC areas</span></span>

<span data-ttu-id="0d009-164">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich:</span><span class="sxs-lookup"><span data-stu-id="0d009-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0d009-165">Der Beispiel Download umfasst eine [partielle Sicht](xref:mvc/views/partial) , die Folgendes enthält:</span><span class="sxs-lookup"><span data-stu-id="0d009-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="0d009-166">Die vorangehenden Links.</span><span class="sxs-lookup"><span data-stu-id="0d009-166">The preceding links.</span></span>
* <span data-ttu-id="0d009-167">Links, die mit dem vorangehenden vergleichbar sind, außer `area` sind nicht angegeben.</span><span class="sxs-lookup"><span data-stu-id="0d009-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="0d009-168">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="0d009-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0d009-169">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich und Controller verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="0d009-170">Wenn der Bereich oder der Kontroller nicht angegeben werden, hängt das Routing von den [Umgebungswerten](xref:mvc/controllers/routing#ambient) ab.</span><span class="sxs-lookup"><span data-stu-id="0d009-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="0d009-171">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="0d009-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0d009-172">In vielen Fällen für die Beispiel-App generiert die Verwendung der Ambient-Werte falsche Verknüpfungen mit dem Markup, das nicht den Bereich angibt.</span><span class="sxs-lookup"><span data-stu-id="0d009-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="0d009-173">Weitere Informationen finden Sie unter [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="0d009-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="0d009-174">Freigegebenes Layout für Bereiche unter Verwendung der _ViewStart.cshtml-Datei</span><span class="sxs-lookup"><span data-stu-id="0d009-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="0d009-175">Wenn Sie ein gemeinsames Layout für die gesamte App freigeben möchten, behalten Sie die *_ViewStart. cshtml* im Stamm [Ordner der Anwendung](#arf)bei.</span><span class="sxs-lookup"><span data-stu-id="0d009-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="0d009-176">Weitere Informationen finden Sie unter <xref:mvc/views/layout></span><span class="sxs-lookup"><span data-stu-id="0d009-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="0d009-177">Anwendungs Stamm Ordner</span><span class="sxs-lookup"><span data-stu-id="0d009-177">Application root folder</span></span>

<span data-ttu-id="0d009-178">Der Stamm Ordner der Anwendung ist der Ordner, der *Startup.cs* in Web-App enthält, die mit den ASP.net Core Vorlagen erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="0d009-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="0d009-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="0d009-180">*/Views/_ViewImports. cshtml* , for MVC und */pages/_ViewImports. cshtml* für Razor Seiten, wird nicht in Ansichten in Bereichen importiert.</span><span class="sxs-lookup"><span data-stu-id="0d009-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="0d009-181">Verwenden Sie einen der folgenden Ansätze, um Ansichts Importe für alle Sichten bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="0d009-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="0d009-182">Fügen Sie *_ViewImports. cshtml* dem [Anwendungs Stamm Ordner](#arf)hinzu.</span><span class="sxs-lookup"><span data-stu-id="0d009-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="0d009-183">Eine *_ViewImports. cshtml* -Datei im Stamm Ordner der Anwendung gilt für alle Sichten in der app.</span><span class="sxs-lookup"><span data-stu-id="0d009-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="0d009-184">Kopieren Sie die Datei *_ViewImports. cshtml* in den entsprechenden Ansichts Ordner Unterbereiche.</span><span class="sxs-lookup"><span data-stu-id="0d009-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="0d009-185">Die Datei *_ViewImports. cshtml* enthält normalerweise [taghilfsprogramme](xref:mvc/views/tag-helpers/intro) , die `@using` - `@inject` Anweisungen, und.</span><span class="sxs-lookup"><span data-stu-id="0d009-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="0d009-186">Weitere Informationen finden Sie unter [Importieren von freigegebenen Direktiven](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="0d009-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="0d009-187">Ändern des Standardbereichsordners, in dem Ansichten gespeichert sind</span><span class="sxs-lookup"><span data-stu-id="0d009-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="0d009-188">Der folgende Code ändert den Standardbereichsordner von `"Areas"` in `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="0d009-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="0d009-189">Bereiche mit Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-189">Areas with Razor Pages</span></span>

<span data-ttu-id="0d009-190">Bereiche mit Razor Seiten erfordern einen `Areas/<area name>/Pages` Ordner im Stamm der app.</span><span class="sxs-lookup"><span data-stu-id="0d009-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="0d009-191">Die folgende Ordnerstruktur wird mit dem [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d009-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="0d009-192">Projektname</span><span class="sxs-lookup"><span data-stu-id="0d009-192">Project name</span></span>
  * <span data-ttu-id="0d009-193">Bereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-193">Areas</span></span>
    * <span data-ttu-id="0d009-194">Produkte</span><span class="sxs-lookup"><span data-stu-id="0d009-194">Products</span></span>
      * <span data-ttu-id="0d009-195">Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-195">Pages</span></span>
        * <span data-ttu-id="0d009-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="0d009-196">_ViewImports</span></span>
        * <span data-ttu-id="0d009-197">Info</span><span class="sxs-lookup"><span data-stu-id="0d009-197">About</span></span>
        * <span data-ttu-id="0d009-198">Index</span><span class="sxs-lookup"><span data-stu-id="0d009-198">Index</span></span>
    * <span data-ttu-id="0d009-199">Dienste</span><span class="sxs-lookup"><span data-stu-id="0d009-199">Services</span></span>
      * <span data-ttu-id="0d009-200">Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-200">Pages</span></span>
        * <span data-ttu-id="0d009-201">Verwalten</span><span class="sxs-lookup"><span data-stu-id="0d009-201">Manage</span></span>
          * <span data-ttu-id="0d009-202">Info</span><span class="sxs-lookup"><span data-stu-id="0d009-202">About</span></span>
          * <span data-ttu-id="0d009-203">Index</span><span class="sxs-lookup"><span data-stu-id="0d009-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="0d009-204">Link Generierung mit Razor Seiten und Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="0d009-205">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich (z.B. `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="0d009-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0d009-206">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="0d009-207">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="0d009-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0d009-208">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="0d009-209">Wenn der Bereich nicht angegeben wird, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="0d009-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="0d009-210">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="0d009-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0d009-211">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0d009-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="0d009-212">Betrachten Sie hierzu die aus dem folgenden Code generierten Links:</span><span class="sxs-lookup"><span data-stu-id="0d009-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="0d009-213">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="0d009-213">For the preceding code:</span></span>

* <span data-ttu-id="0d009-214">Der aus `<a asp-page="/Manage/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite im `Services`-Bereich erfolgte.</span><span class="sxs-lookup"><span data-stu-id="0d009-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="0d009-215">Beispiel: `/Services/Manage/`, `/Services/Manage/Index` oder `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="0d009-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="0d009-216">Der aus `<a asp-page="/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite in `/Home` erfolgte.</span><span class="sxs-lookup"><span data-stu-id="0d009-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="0d009-217">Der Code stammt aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="0d009-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="0d009-218">Importieren von Namespace und Taghilfsprogrammen mit der _ViewImports-Datei</span><span class="sxs-lookup"><span data-stu-id="0d009-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="0d009-219">Eine *_ViewImports. cshtml* -Datei kann jedem Ordner der Bereichs *Seiten* hinzugefügt werden, um die Namespace-und taghilfsprogramme auf jede Razor Seite im Ordner zu importieren.</span><span class="sxs-lookup"><span data-stu-id="0d009-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="0d009-220">Betrachten Sie den Bereich *Services* des Beispielcodes, der keine *_ViewImports.cshtml* -Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="0d009-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0d009-221">Das folgende Markup zeigt die */Services/Manage/about* - Razor Seite an:</span><span class="sxs-lookup"><span data-stu-id="0d009-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="0d009-222">Im obenstehenden Markup:</span><span class="sxs-lookup"><span data-stu-id="0d009-222">In the preceding markup:</span></span>

* <span data-ttu-id="0d009-223">Der vollqualifizierte Domänenname muss verwendet werden, um das Modell anzugeben (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="0d009-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="0d009-224">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden aktiviert durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="0d009-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="0d009-225">Im Beispieldownload enthält der Bereich „Products“ die folgende *_ViewImports.cshtml* -Datei:</span><span class="sxs-lookup"><span data-stu-id="0d009-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0d009-226">Das folgende Markup zeigt die */Products/about* - Razor Seite an:</span><span class="sxs-lookup"><span data-stu-id="0d009-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="0d009-227">In der vorherigen Datei werden Namespace und `@addTagHelper`-Anweisung von der Datei *Areas/Products/Pages/_ViewImports.cshtml* in die Datei importiert.</span><span class="sxs-lookup"><span data-stu-id="0d009-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="0d009-228">Weitere Informationen finden Sie unter [Verwalten des Taghilfsprogrammbereichs](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) und [Importieren gemeinsam verwendeter Anweisungen](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="0d009-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="0d009-229">Frei gegebenes Layout für Razor Seitenbereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="0d009-230">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="0d009-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="0d009-231">Veröffentlichen von Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-231">Publishing Areas</span></span>

<span data-ttu-id="0d009-232">Alle \*.cshtml-Dateien und Dateien im *wwwroot* -Verzeichnis werden in der Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der \*.csproj-Datei enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="0d009-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0d009-233">Bereiche sind ein Feature von ASP.NET, das für die Organisation von verwandten Funktionalitäten in eine Gruppe als separater Namespace (für Routing) und Ordnerstruktur (für Ansichten) verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="0d009-234">Mithilfe von Bereichen wird eine Hierarchie für das Routing erstellt, indem ein weiterer Routen Parameter, `area` , zu `controller` und `action` oder einer Razor Seite hinzugefügt wird `page` .</span><span class="sxs-lookup"><span data-stu-id="0d009-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="0d009-235">Bereiche bieten eine Möglichkeit, eine ASP.net Core-Web-App in kleinere funktionale Gruppen zu partitionieren, die jeweils über einen eigenen Satz von Razor Seiten, Controllern, Ansichten und Modellen verfügen.</span><span class="sxs-lookup"><span data-stu-id="0d009-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="0d009-236">Ein Bereich ist im Grunde genommen eine Struktur in einer App.</span><span class="sxs-lookup"><span data-stu-id="0d009-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="0d009-237">In einem ASP.NET Core-Webprojekt werden logische Komponenten wie Seiten, Modelle, Controller und Ansichten in verschiedenen Ordner aufbewahrt.</span><span class="sxs-lookup"><span data-stu-id="0d009-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="0d009-238">Die ASP.NET Core-Runtime verwendet Namenskonventionen, um die Beziehung zwischen diesen Komponenten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="0d009-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="0d009-239">Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="0d009-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="0d009-240">Dies gilt z.B. für eine E-Commerce-App mit mehreren Geschäftseinheiten, wie Auftragsabschluss, Abrechnung und Suche.</span><span class="sxs-lookup"><span data-stu-id="0d009-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="0d009-241">Jede dieser Einheiten verfügt über einen eigenen Bereich, der Sichten, Controller, Razor Seiten und Modelle enthalten soll.</span><span class="sxs-lookup"><span data-stu-id="0d009-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="0d009-242">Die Verwendung von Bereichen in einem Projekt ist erwägenswert, wenn:</span><span class="sxs-lookup"><span data-stu-id="0d009-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="0d009-243">Ihre App aus mehreren funktionalen Komponenten auf hoher Ebene besteht, die logisch getrennt sein können.</span><span class="sxs-lookup"><span data-stu-id="0d009-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="0d009-244">Sie Ihre App partitionieren möchten, damit jeder funktionale Bereich unabhängig voneinander bearbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="0d009-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="0d009-245">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="0d009-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="0d009-246">Das Downloadbeispiel stellt eine einfache App für Testbereiche zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="0d009-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="0d009-247">Wenn Sie Seiten verwenden Razor , finden Sie weitere Informationen unter [Bereiche mit Razor Seiten](#areas-with-razor-pages) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="0d009-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="0d009-248">Bereiche für Controller mit Ansichten</span><span class="sxs-lookup"><span data-stu-id="0d009-248">Areas for controllers with views</span></span>

<span data-ttu-id="0d009-249">Eine typische ASP.NET Core-Web-App, die Bereiche, Controller und Ansichten verwendet, beinhaltet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="0d009-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="0d009-250">Eine [Bereichsordnerstruktur](#area-folder-structure).</span><span class="sxs-lookup"><span data-stu-id="0d009-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="0d009-251">Controller mit dem- [`[Area]`](#attribute) Attribut, um den Controller dem Bereich zuzuordnen:</span><span class="sxs-lookup"><span data-stu-id="0d009-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="0d009-252">Die [Bereichsroute, die dem Startup hinzugefügt wurde](#add-area-route):</span><span class="sxs-lookup"><span data-stu-id="0d009-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="0d009-253">Bereichsordnerstruktur</span><span class="sxs-lookup"><span data-stu-id="0d009-253">Area folder structure</span></span>

<span data-ttu-id="0d009-254">Stellen Sie sich eine App vor, die zwei logische Gruppen hat, *Produkte* und *Dienste* .</span><span class="sxs-lookup"><span data-stu-id="0d009-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="0d009-255">Wenn Bereiche verwendet werden, würde die Ordnerstruktur ähnlich dem Folgenden aussehen:</span><span class="sxs-lookup"><span data-stu-id="0d009-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="0d009-256">Projektname</span><span class="sxs-lookup"><span data-stu-id="0d009-256">Project name</span></span>
  * <span data-ttu-id="0d009-257">Bereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-257">Areas</span></span>
    * <span data-ttu-id="0d009-258">Produkte</span><span class="sxs-lookup"><span data-stu-id="0d009-258">Products</span></span>
      * <span data-ttu-id="0d009-259">Controller</span><span class="sxs-lookup"><span data-stu-id="0d009-259">Controllers</span></span>
        * <span data-ttu-id="0d009-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-260">HomeController.cs</span></span>
        * <span data-ttu-id="0d009-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-261">ManageController.cs</span></span>
      * <span data-ttu-id="0d009-262">Sichten</span><span class="sxs-lookup"><span data-stu-id="0d009-262">Views</span></span>
        * <span data-ttu-id="0d009-263">Startseite</span><span class="sxs-lookup"><span data-stu-id="0d009-263">Home</span></span>
          * <span data-ttu-id="0d009-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-264">Index.cshtml</span></span>
        * <span data-ttu-id="0d009-265">Verwalten</span><span class="sxs-lookup"><span data-stu-id="0d009-265">Manage</span></span>
          * <span data-ttu-id="0d009-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-266">Index.cshtml</span></span>
          * <span data-ttu-id="0d009-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-267">About.cshtml</span></span>
    * <span data-ttu-id="0d009-268">Dienste</span><span class="sxs-lookup"><span data-stu-id="0d009-268">Services</span></span>
      * <span data-ttu-id="0d009-269">Controller</span><span class="sxs-lookup"><span data-stu-id="0d009-269">Controllers</span></span>
        * <span data-ttu-id="0d009-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0d009-270">HomeController.cs</span></span>
      * <span data-ttu-id="0d009-271">Sichten</span><span class="sxs-lookup"><span data-stu-id="0d009-271">Views</span></span>
        * <span data-ttu-id="0d009-272">Startseite</span><span class="sxs-lookup"><span data-stu-id="0d009-272">Home</span></span>
          * <span data-ttu-id="0d009-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-273">Index.cshtml</span></span>

<span data-ttu-id="0d009-274">Während das vorherige Layout typisch ist, wenn Bereiche verwendet werden, müssen nur die Ansichtsdateien diese Ordnerstruktur verwenden.</span><span class="sxs-lookup"><span data-stu-id="0d009-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="0d009-275">Die Ansichtsermittlung sucht nach einer passenden Bereichsansichtsdatei im folgenden Ordner:</span><span class="sxs-lookup"><span data-stu-id="0d009-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="0d009-276">Zuordnen eines Controllers zu einem Bereich</span><span class="sxs-lookup"><span data-stu-id="0d009-276">Associate the controller with an Area</span></span>

<span data-ttu-id="0d009-277">Bereichs Controller werden mit dem [ &lbrack; Area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut angegeben:</span><span class="sxs-lookup"><span data-stu-id="0d009-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="0d009-278">Hinzufügen einer Bereichsroute</span><span class="sxs-lookup"><span data-stu-id="0d009-278">Add Area route</span></span>

<span data-ttu-id="0d009-279">Bereichsrouten verwenden normalerweise konventionelles Routing anstatt Attributrouting.</span><span class="sxs-lookup"><span data-stu-id="0d009-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="0d009-280">Beim herkömmlichen Routing ist die Reihenfolge wichtig.</span><span class="sxs-lookup"><span data-stu-id="0d009-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="0d009-281">Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.</span><span class="sxs-lookup"><span data-stu-id="0d009-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="0d009-282">`{area:...}` kann als Token in Routenvorlagen verwendet werden, wenn der URL-Raum in allen Bereichen einheitlich ist:</span><span class="sxs-lookup"><span data-stu-id="0d009-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="0d009-283">Im vorherigen Code wendet `exists` eine Einschränkung an: Die Route muss mit einem Bereich übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="0d009-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="0d009-284">`{area:...}` zu verwenden ist die unkomplizierteste Methode, um Bereichen Routing hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="0d009-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="0d009-285">Im folgenden Code wird <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> verwendet, um zwei benannte Bereichsrouten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="0d009-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="0d009-286">Wenn `MapAreaRoute` mit ASP.NET Core 2.2 verwendet werden soll, sehen Sie sich diesen [GitHub-Artikel](https://github.com/dotnet/AspNetCore/issues/7772) an.</span><span class="sxs-lookup"><span data-stu-id="0d009-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="0d009-287">Weitere Informationen finden Sie im Artikel [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing#areas).</span><span class="sxs-lookup"><span data-stu-id="0d009-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="0d009-288">Erstellen von Links mit MVC-Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-288">Link generation with MVC areas</span></span>

<span data-ttu-id="0d009-289">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich:</span><span class="sxs-lookup"><span data-stu-id="0d009-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0d009-290">Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.</span><span class="sxs-lookup"><span data-stu-id="0d009-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="0d009-291">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="0d009-292">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="0d009-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0d009-293">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich und Controller verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="0d009-294">Wenn der Bereich oder der Kontroller nicht angegeben werden, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="0d009-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="0d009-295">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="0d009-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0d009-296">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0d009-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="0d009-297">Weitere Informationen finden Sie unter [Routing zu Controlleraktionen in ASP.NET Core](xref:mvc/controllers/routing).</span><span class="sxs-lookup"><span data-stu-id="0d009-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="0d009-298">Freigegebenes Layout für Bereiche unter Verwendung der _ViewStart.cshtml-Datei</span><span class="sxs-lookup"><span data-stu-id="0d009-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="0d009-299">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="0d009-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="0d009-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="0d009-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="0d009-301">An ihrem Standardspeicherort gilt die Datei */Views/_ViewImports.cshtml* nicht für Bereiche.</span><span class="sxs-lookup"><span data-stu-id="0d009-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="0d009-302">Um allgemeine [taghilfsprogramme](xref:mvc/views/tag-helpers/intro), `@using` oder `@inject` in Ihrer Region zu verwenden, stellen Sie sicher, dass eine ordnungsgemäße *_ViewImports. cshtml* -Datei [für Ihre Bereichs Ansichten gilt](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="0d009-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="0d009-303">Wenn Sie das gleiche Verhalten in allen Ansichten wünschen, verschieben Sie */Views/_ViewImports.cshtml* in den Anwendungsstamm.</span><span class="sxs-lookup"><span data-stu-id="0d009-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="0d009-304">Ändern des Standardbereichsordners, in dem Ansichten gespeichert sind</span><span class="sxs-lookup"><span data-stu-id="0d009-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="0d009-305">Der folgende Code ändert den Standardbereichsordner von `"Areas"` in `"MyAreas"`:</span><span class="sxs-lookup"><span data-stu-id="0d009-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="0d009-306">Bereiche mit Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-306">Areas with Razor Pages</span></span>

<span data-ttu-id="0d009-307">Bereiche mit Razor Seiten erfordern einen `Areas/<area name>/Pages` Ordner im Stamm der app.</span><span class="sxs-lookup"><span data-stu-id="0d009-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="0d009-308">Die folgende Ordnerstruktur wird mit dem [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) verwendet.</span><span class="sxs-lookup"><span data-stu-id="0d009-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="0d009-309">Projektname</span><span class="sxs-lookup"><span data-stu-id="0d009-309">Project name</span></span>
  * <span data-ttu-id="0d009-310">Bereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-310">Areas</span></span>
    * <span data-ttu-id="0d009-311">Produkte</span><span class="sxs-lookup"><span data-stu-id="0d009-311">Products</span></span>
      * <span data-ttu-id="0d009-312">Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-312">Pages</span></span>
        * <span data-ttu-id="0d009-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="0d009-313">_ViewImports</span></span>
        * <span data-ttu-id="0d009-314">Info</span><span class="sxs-lookup"><span data-stu-id="0d009-314">About</span></span>
        * <span data-ttu-id="0d009-315">Index</span><span class="sxs-lookup"><span data-stu-id="0d009-315">Index</span></span>
    * <span data-ttu-id="0d009-316">Dienste</span><span class="sxs-lookup"><span data-stu-id="0d009-316">Services</span></span>
      * <span data-ttu-id="0d009-317">Seiten</span><span class="sxs-lookup"><span data-stu-id="0d009-317">Pages</span></span>
        * <span data-ttu-id="0d009-318">Verwalten</span><span class="sxs-lookup"><span data-stu-id="0d009-318">Manage</span></span>
          * <span data-ttu-id="0d009-319">Info</span><span class="sxs-lookup"><span data-stu-id="0d009-319">About</span></span>
          * <span data-ttu-id="0d009-320">Index</span><span class="sxs-lookup"><span data-stu-id="0d009-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="0d009-321">Link Generierung mit Razor Seiten und Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="0d009-322">Im folgenden Code aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) sehen Sie die Erstellung eines Links mit dem angegebenen Bereich (z.B. `asp-area="Products"`):</span><span class="sxs-lookup"><span data-stu-id="0d009-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0d009-323">Links, die mit dem vorherigen Code erstellt wurden, gelten überall in der App.</span><span class="sxs-lookup"><span data-stu-id="0d009-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="0d009-324">Im Beispieldownload ist eine [partielle Ansicht](xref:mvc/views/partial) enthalten, die die vorherigen Links und dieselben Links beinhaltet, ohne dass der Bereich angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="0d009-325">In der [Layoutdatei](xref:mvc/views/layout) wird auf die partielle Ansicht verwiesen. Jede Seite in der App stellt also die erstellen Links dar.</span><span class="sxs-lookup"><span data-stu-id="0d009-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0d009-326">Die Links, die ohne Angabe eines Bereichs erstellt werden, sind nur gültig, wenn auf sie von einer Seite im selben Bereich verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="0d009-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="0d009-327">Wenn der Bereich nicht angegeben wird, hängt das Routing von den *Umgebungswerten* ab.</span><span class="sxs-lookup"><span data-stu-id="0d009-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="0d009-328">Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt.</span><span class="sxs-lookup"><span data-stu-id="0d009-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0d009-329">Oft werden ungültige Links erstellt, wenn für die Beispiel-App die Umgebungswerte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0d009-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="0d009-330">Betrachten Sie hierzu die aus dem folgenden Code generierten Links:</span><span class="sxs-lookup"><span data-stu-id="0d009-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="0d009-331">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="0d009-331">For the preceding code:</span></span>

* <span data-ttu-id="0d009-332">Der aus `<a asp-page="/Manage/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite im `Services`-Bereich erfolgte.</span><span class="sxs-lookup"><span data-stu-id="0d009-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="0d009-333">Beispiel: `/Services/Manage/`, `/Services/Manage/Index` oder `/Services/Manage/About`.</span><span class="sxs-lookup"><span data-stu-id="0d009-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="0d009-334">Der aus `<a asp-page="/About">` generierte Link stimmt nur, wenn die letzte Anforderung für eine Seite in `/Home` erfolgte.</span><span class="sxs-lookup"><span data-stu-id="0d009-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="0d009-335">Der Code stammt aus dem [Beispieldownload](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span><span class="sxs-lookup"><span data-stu-id="0d009-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="0d009-336">Importieren von Namespace und Taghilfsprogrammen mit der _ViewImports-Datei</span><span class="sxs-lookup"><span data-stu-id="0d009-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="0d009-337">Eine *_ViewImports. cshtml* -Datei kann jedem Ordner der Bereichs *Seiten* hinzugefügt werden, um die Namespace-und taghilfsprogramme auf jede Razor Seite im Ordner zu importieren.</span><span class="sxs-lookup"><span data-stu-id="0d009-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="0d009-338">Betrachten Sie den Bereich *Services* des Beispielcodes, der keine *_ViewImports.cshtml* -Datei enthält.</span><span class="sxs-lookup"><span data-stu-id="0d009-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0d009-339">Das folgende Markup zeigt die */Services/Manage/about* - Razor Seite an:</span><span class="sxs-lookup"><span data-stu-id="0d009-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="0d009-340">Im obenstehenden Markup:</span><span class="sxs-lookup"><span data-stu-id="0d009-340">In the preceding markup:</span></span>

* <span data-ttu-id="0d009-341">Der vollqualifizierte Domänenname muss verwendet werden, um das Modell anzugeben (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span><span class="sxs-lookup"><span data-stu-id="0d009-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="0d009-342">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) werden aktiviert durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="0d009-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="0d009-343">Im Beispieldownload enthält der Bereich „Products“ die folgende *_ViewImports.cshtml* -Datei:</span><span class="sxs-lookup"><span data-stu-id="0d009-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0d009-344">Das folgende Markup zeigt die */Products/about* - Razor Seite an:</span><span class="sxs-lookup"><span data-stu-id="0d009-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="0d009-345">In der vorherigen Datei werden Namespace und `@addTagHelper`-Anweisung von der Datei *Areas/Products/Pages/_ViewImports.cshtml* in die Datei importiert.</span><span class="sxs-lookup"><span data-stu-id="0d009-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="0d009-346">Weitere Informationen finden Sie unter [Verwalten des Taghilfsprogrammbereichs](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) und [Importieren gemeinsam verwendeter Anweisungen](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="0d009-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="0d009-347">Frei gegebenes Layout für Razor Seitenbereiche</span><span class="sxs-lookup"><span data-stu-id="0d009-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="0d009-348">Um ein gemeinsames Layout für die gesamte App freizugeben, verschieben Sie *_ViewStart.cshtml* in den Stammordner der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="0d009-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="0d009-349">Veröffentlichen von Bereichen</span><span class="sxs-lookup"><span data-stu-id="0d009-349">Publishing Areas</span></span>

<span data-ttu-id="0d009-350">Alle \*.cshtml-Dateien und Dateien im *wwwroot* -Verzeichnis werden in der Ausgabe veröffentlicht, wenn `<Project Sdk="Microsoft.NET.Sdk.Web">` in der \*.csproj-Datei enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="0d009-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
