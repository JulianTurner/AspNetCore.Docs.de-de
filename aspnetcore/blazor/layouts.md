---
title: :::no-loc(Blazor):::-Layouts in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für :::no-loc(Blazor):::-Apps erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: e61c76b5d53ad7646961632d00b047ecd2d9e477
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055606"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="7eaa3-103">:::no-loc(Blazor):::-Layouts in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7eaa3-103">ASP.NET Core :::no-loc(Blazor)::: layouts</span></span>

<span data-ttu-id="7eaa3-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7eaa3-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7eaa3-105">Einige App-Elemente wie Menüs, Copyrightmeldungen und Firmenlogos sind in der Regel Teil des allgemeinen Layouts von Apps und werden von allen Komponenten der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="7eaa3-106">Das Kopieren des Codes dieser Elemente in alle Komponenten einer App ist kein effizientes Konzept.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="7eaa3-107">Jedes Mal, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="7eaa3-108">Eine solche Duplizierung ist schwer zu verwalten und kann im Laufe der Zeit zu inkonsistenten Inhalten führen.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="7eaa3-109">Mit *Layouts* wird dieses Problem gelöst.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="7eaa3-110">Technisch gesehen ist ein Layout nur eine weitere Komponente.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="7eaa3-111">Layouts werden in :::no-loc(Razor):::-Vorlagen oder in C#-Code definiert und können [Datenbindung](xref:blazor/components/data-binding), [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) und andere Komponentenszenarien nutzen.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-111">A layout is defined in a :::no-loc(Razor)::: template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="7eaa3-112">Zum Umwandeln einer *Komponente* in ein *Layout* muss die Komponente:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-112">To turn a *component* into a *layout* , the component:</span></span>

* <span data-ttu-id="7eaa3-113">von <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> erben, was eine <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>-Eigenschaft für die gerenderten Inhalte im Layout definiert.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-113">Inherits from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>, which defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="7eaa3-114">die :::no-loc(Razor):::-Syntax `@Body` verwenden, um den Standort im Layoutmarkup anzugeben, ab den der Inhalt gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-114">Uses the :::no-loc(Razor)::: syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="7eaa3-115">Das folgende Codebeispiel zeigt die :::no-loc(Razor):::-Vorlage einer Layoutkomponente (`MainLayout.razor`).</span><span class="sxs-lookup"><span data-stu-id="7eaa3-115">The following code sample shows the :::no-loc(Razor)::: template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="7eaa3-116">Das Layout erbt <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> und legt `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-116">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor?highlight=1,13)]

## <a name="mainlayout-component"></a><span data-ttu-id="7eaa3-117">`MainLayout`-Komponente</span><span class="sxs-lookup"><span data-stu-id="7eaa3-117">`MainLayout` component</span></span>

<span data-ttu-id="7eaa3-118">In einer App, die auf einer der :::no-loc(Blazor):::-Projektvorlagen basiert, befindet sich die Komponente `MainLayout` (`MainLayout.razor`) im Ordner `Shared` der App:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-118">In an app based on one of the :::no-loc(Blazor)::: project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

[!code-razor[](./common/samples/3.x/:::no-loc(Blazor):::WebAssemblySample/Shared/MainLayout.razor)]

## <a name="default-layout"></a><span data-ttu-id="7eaa3-119">Standardlayout</span><span class="sxs-lookup"><span data-stu-id="7eaa3-119">Default layout</span></span>

<span data-ttu-id="7eaa3-120">Legen Sie das Standardlayout für die App in der Komponente <xref:Microsoft.AspNetCore.Components.Routing.Router> der Datei `App.razor` der App fest.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-120">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="7eaa3-121">Mit der folgenden <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente, die von den :::no-loc(Blazor):::-Standardvorlagen bereitgestellt wird, wird das Standardlayout auf die `MainLayout`-Komponente festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-121">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default :::no-loc(Blazor)::: templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

<span data-ttu-id="7eaa3-122">Geben Sie <xref:Microsoft.AspNetCore.Components.LayoutView> für <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalt an, um ein Standardlayout für <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalt anzugeben:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-122">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

<span data-ttu-id="7eaa3-123">Weitere Informationen zur <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-123">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="7eaa3-124">Das Festlegen des Layouts als Standardlayout im Router ist nützlich, da es pro Komponente oder pro Ordner überschrieben werden kann.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-124">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="7eaa3-125">Das Verwenden des Routers zum Festlegen des Standardlayouts der App wird bevorzugt, da es sich dabei um die allgemeinste Methode handelt.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-125">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="7eaa3-126">Festlegen eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="7eaa3-126">Specify a layout in a component</span></span>

<span data-ttu-id="7eaa3-127">Verwenden Sie die :::no-loc(Razor):::-Anweisung `@layout`, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-127">Use the :::no-loc(Razor)::: directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="7eaa3-128">Der Compiler konvertiert `@layout` in eine <xref:Microsoft.AspNetCore.Components.LayoutAttribute>-Eigenschaft, die auf die Komponentenklasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-128">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="7eaa3-129">Der Inhalt der folgenden `MasterList`-Komponente wird bei der Position von `@Body` in `MasterLayout` eingefügt:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-129">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="7eaa3-130">Durch das direkte Festlegen eines Layouts in einer Komponente wird das im Router oder mit einer aus `_Imports.razor` importierten `@layout`-Anweisung festgelegte *Standardlayout* überschrieben.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-130">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="7eaa3-131">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="7eaa3-131">Centralized layout selection</span></span>

<span data-ttu-id="7eaa3-132">Alle Ordner einer App können optional eine Vorlagendatei namens `_Imports.razor` enthalten.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-132">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="7eaa3-133">Der Compiler enthält die in der Importdatei angegebenen Anweisungen in allen :::no-loc(Razor):::-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-133">The compiler includes the directives specified in the imports file in all of the :::no-loc(Razor)::: templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="7eaa3-134">Daher wird mit einer `_Imports.razor`-Datei, die `@layout MyCoolLayout` enthält, sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout` verwenden.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-134">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="7eaa3-135">`@layout MyCoolLayout` muss nicht wiederholt zu allen `.razor`-Dateien im Ordner und in den Unterordnern hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-135">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="7eaa3-136">`@using`-Anweisungen werden auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-136">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="7eaa3-137">Die folgende `_Imports.razor`-Datei importiert:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-137">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="7eaa3-138">`MyCoolLayout`</span><span class="sxs-lookup"><span data-stu-id="7eaa3-138">`MyCoolLayout`.</span></span>
* <span data-ttu-id="7eaa3-139">Alle :::no-loc(Razor):::-Komponenten im gleichen Ordner und in dessen Unterordnern</span><span class="sxs-lookup"><span data-stu-id="7eaa3-139">All :::no-loc(Razor)::: components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="7eaa3-140">Der `:::no-loc(Blazor):::App1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-140">The `:::no-loc(Blazor):::App1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="7eaa3-141">Die Datei `_Imports.razor` ähnelt der Datei [_ViewImports.cshtml für :::no-loc(Razor):::-Ansichten und -Seiten](xref:mvc/views/layout#importing-shared-directives), gilt aber spezifisch für :::no-loc(Razor):::-Komponentendateien.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-141">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for :::no-loc(Razor)::: views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to :::no-loc(Razor)::: component files.</span></span>

<span data-ttu-id="7eaa3-142">Durch das Festlegen eines Layouts in `_Imports.razor` wird ein Layout überschrieben, das als *Standardlayout* des Routers festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-142">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="7eaa3-143">Fügen Sie der `_Imports.razor`-Stammdatei **keine** :::no-loc(Razor)::: `@layout`-Anweisung hinzu. Dies würde zu einer Endlosschleife von Layouts in der App führen.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-143">Do **not** add a :::no-loc(Razor)::: `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="7eaa3-144">Um das Standardlayout der App zu steuern, legen Sie es in der Komponente `Router` fest.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-144">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="7eaa3-145">Weitere Informationen finden Sie im Abschnitt [Standardlayout](#default-layout).</span><span class="sxs-lookup"><span data-stu-id="7eaa3-145">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="7eaa3-146">Geschachtelte Layouts</span><span class="sxs-lookup"><span data-stu-id="7eaa3-146">Nested layouts</span></span>

<span data-ttu-id="7eaa3-147">Apps können aus geschachtelten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-147">Apps can consist of nested layouts.</span></span> <span data-ttu-id="7eaa3-148">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-148">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="7eaa3-149">Geschachtelte Layouts werden beispielsweise dazu verwendet, eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-149">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="7eaa3-150">Im folgenden Beispiel wird die Verwendung von geschachtelten Layouts veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-150">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="7eaa3-151">Die Datei `EpisodesComponent.razor` ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-151">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="7eaa3-152">Die Komponente verweist auf `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-152">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="7eaa3-153">Die Datei `MasterListLayout.razor` stellt das `MasterListLayout` bereit.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-153">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="7eaa3-154">Das Layout verweist auf ein anderes Layout (`MasterLayout`), in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-154">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="7eaa3-155">`EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-155">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="7eaa3-156">Schließlich enthält das `MasterLayout` in der Datei `MasterLayout.razor` die allgemeinen Layoutelemente, z. B. die Kopfzeile, das Hauptmenü und die Fußzeile.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-156">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="7eaa3-157">Das Layout `MasterListLayout` mit der Komponente `EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="7eaa3-157">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="7eaa3-158">Freigeben eines :::no-loc(Razor)::: Pages-Layouts mit integrierten Komponenten</span><span class="sxs-lookup"><span data-stu-id="7eaa3-158">Share a :::no-loc(Razor)::: Pages layout with integrated components</span></span>

<span data-ttu-id="7eaa3-159">Wenn routingfähige Komponenten in eine :::no-loc(Razor)::: Pages-App integriert werden, kann das freigegebene Layout der App mit den Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-159">When routable components are integrated into a :::no-loc(Razor)::: Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="7eaa3-160">Weitere Informationen finden Sie unter <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span><span class="sxs-lookup"><span data-stu-id="7eaa3-160">For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7eaa3-161">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7eaa3-161">Additional resources</span></span>

* <xref:mvc/views/layout>
