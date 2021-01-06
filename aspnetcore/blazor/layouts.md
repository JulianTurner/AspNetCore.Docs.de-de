---
title: Blazor-Layouts in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie wiederverwendbare Layoutkomponenten für Blazor-Apps erstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/23/2020
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
uid: blazor/layouts
ms.openlocfilehash: 3cb7c6184c13a003b4f4294f887d8938caa42f97
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506902"
---
# <a name="aspnet-core-no-locblazor-layouts"></a><span data-ttu-id="a6532-103">Blazor-Layouts in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a6532-103">ASP.NET Core Blazor layouts</span></span>

<span data-ttu-id="a6532-104">Von [Rainer Stropek](https://www.timecockpit.com) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a6532-104">By [Rainer Stropek](https://www.timecockpit.com) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a6532-105">Einige App-Elemente wie Menüs, Copyrightmeldungen und Firmenlogos sind in der Regel Teil des allgemeinen Layouts von Apps und werden von allen Komponenten der App verwendet.</span><span class="sxs-lookup"><span data-stu-id="a6532-105">Some app elements, such as menus, copyright messages, and company logos, are usually part of app's overall layout and used by every component in the app.</span></span> <span data-ttu-id="a6532-106">Das Kopieren des Codes dieser Elemente in alle Komponenten einer App ist kein effizientes Konzept.</span><span class="sxs-lookup"><span data-stu-id="a6532-106">Copying the code of these elements into all of the components of an app isn't an efficient approach.</span></span> <span data-ttu-id="a6532-107">Jedes Mal, wenn eines der Elemente ein Update erfordert, muss jede Komponente aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="a6532-107">Every time one of the elements requires an update, every component must be updated.</span></span> <span data-ttu-id="a6532-108">Eine solche Duplizierung ist schwer zu verwalten und kann im Laufe der Zeit zu inkonsistenten Inhalten führen.</span><span class="sxs-lookup"><span data-stu-id="a6532-108">Such duplication is difficult to maintain and can lead to inconsistent content over time.</span></span> <span data-ttu-id="a6532-109">Mit *Layouts* wird dieses Problem gelöst.</span><span class="sxs-lookup"><span data-stu-id="a6532-109">*Layouts* solve this problem.</span></span>

<span data-ttu-id="a6532-110">Technisch gesehen ist ein Layout nur eine weitere Komponente.</span><span class="sxs-lookup"><span data-stu-id="a6532-110">Technically, a layout is just another component.</span></span> <span data-ttu-id="a6532-111">Layouts werden in Razor-Vorlagen oder in C#-Code definiert und können [Datenbindung](xref:blazor/components/data-binding), [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) und andere Komponentenszenarien nutzen.</span><span class="sxs-lookup"><span data-stu-id="a6532-111">A layout is defined in a Razor template or in C# code and can use [data binding](xref:blazor/components/data-binding), [dependency injection](xref:blazor/fundamentals/dependency-injection), and other component scenarios.</span></span>

<span data-ttu-id="a6532-112">So konvertieren Sie eine Komponente in ein Layout:</span><span class="sxs-lookup"><span data-stu-id="a6532-112">To convert a component into a layout:</span></span>

* <span data-ttu-id="a6532-113">Legen Sie die Komponente so fest, dass sie von <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> erbt.</span><span class="sxs-lookup"><span data-stu-id="a6532-113">Inherit the component from <xref:Microsoft.AspNetCore.Components.LayoutComponentBase>.</span></span> <span data-ttu-id="a6532-114"><xref:Microsoft.AspNetCore.Components.LayoutComponentBase> definiert eine <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body>-Eigenschaft für die gerenderten Inhalte im Layout.</span><span class="sxs-lookup"><span data-stu-id="a6532-114">The <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> defines a <xref:Microsoft.AspNetCore.Components.LayoutComponentBase.Body> property for the rendered content inside the layout.</span></span>
* <span data-ttu-id="a6532-115">Verwenden Sie die Razor-Syntax `@Body`, um die Position im Layoutmarkup anzugeben, an der die Inhalte gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="a6532-115">Use the Razor syntax `@Body` to specify the location in the layout markup where the content is rendered.</span></span>

<span data-ttu-id="a6532-116">Das folgende Codebeispiel zeigt die Razor-Vorlage einer Layoutkomponente (`MainLayout.razor`).</span><span class="sxs-lookup"><span data-stu-id="a6532-116">The following code sample shows the Razor template of a layout component, `MainLayout.razor`.</span></span> <span data-ttu-id="a6532-117">Das Layout erbt <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> und legt `@Body` zwischen der Navigationsleiste und der Fußzeile fest:</span><span class="sxs-lookup"><span data-stu-id="a6532-117">The layout inherits <xref:Microsoft.AspNetCore.Components.LayoutComponentBase> and sets the `@Body` between the navigation bar and the footer:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MainLayout.razor)]

## <a name="mainlayout-component"></a><span data-ttu-id="a6532-118">`MainLayout`-Komponente</span><span class="sxs-lookup"><span data-stu-id="a6532-118">`MainLayout` component</span></span>

<span data-ttu-id="a6532-119">In einer App, die auf einer der Blazor-Projektvorlagen basiert, befindet sich die Komponente `MainLayout` (`MainLayout.razor`) im Ordner `Shared` der App:</span><span class="sxs-lookup"><span data-stu-id="a6532-119">In an app based on one of the Blazor project templates, the `MainLayout` component (`MainLayout.razor`) is in the app's `Shared` folder:</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](./common/samples/5.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](./common/samples/3.x/BlazorWebAssemblySample/Shared/MainLayout.razor)]

::: moniker-end

## <a name="default-layout"></a><span data-ttu-id="a6532-120">Standardlayout</span><span class="sxs-lookup"><span data-stu-id="a6532-120">Default layout</span></span>

<span data-ttu-id="a6532-121">Legen Sie das Standardlayout für die App in der Komponente <xref:Microsoft.AspNetCore.Components.Routing.Router> der Datei `App.razor` der App fest.</span><span class="sxs-lookup"><span data-stu-id="a6532-121">Specify the default app layout in the <xref:Microsoft.AspNetCore.Components.Routing.Router> component in the app's `App.razor` file.</span></span> <span data-ttu-id="a6532-122">Mit der folgenden <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente, die von den Blazor-Standardvorlagen bereitgestellt wird, wird das Standardlayout auf die `MainLayout`-Komponente festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a6532-122">The following <xref:Microsoft.AspNetCore.Components.Routing.Router> component, which is provided by the default Blazor templates, sets the default layout to the `MainLayout` component:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App1.razor?highlight=3)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="a6532-123">Geben Sie <xref:Microsoft.AspNetCore.Components.LayoutView> für <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalt an, um ein Standardlayout für <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalt anzugeben:</span><span class="sxs-lookup"><span data-stu-id="a6532-123">To supply a default layout for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content, specify a <xref:Microsoft.AspNetCore.Components.LayoutView> for <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound> content:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/App2.razor?highlight=6-9)]

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="a6532-124">Weitere Informationen zur <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente finden Sie unter <xref:blazor/fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="a6532-124">For more information on the <xref:Microsoft.AspNetCore.Components.Routing.Router> component, see <xref:blazor/fundamentals/routing>.</span></span>

<span data-ttu-id="a6532-125">Das Festlegen des Layouts als Standardlayout im Router ist nützlich, da es pro Komponente oder pro Ordner überschrieben werden kann.</span><span class="sxs-lookup"><span data-stu-id="a6532-125">Specifying the layout as a default layout in the router is a useful practice because it can be overridden on a per-component or per-folder basis.</span></span> <span data-ttu-id="a6532-126">Das Verwenden des Routers zum Festlegen des Standardlayouts der App wird bevorzugt, da es sich dabei um die allgemeinste Methode handelt.</span><span class="sxs-lookup"><span data-stu-id="a6532-126">Prefer using the router to set the app's default layout because it's the most general technique.</span></span>

## <a name="specify-a-layout-in-a-component"></a><span data-ttu-id="a6532-127">Festlegen eines Layouts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="a6532-127">Specify a layout in a component</span></span>

<span data-ttu-id="a6532-128">Verwenden Sie die Razor-Anweisung `@layout`, um ein Layout auf eine Komponente anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="a6532-128">Use the Razor directive `@layout` to apply a layout to a component.</span></span> <span data-ttu-id="a6532-129">Der Compiler konvertiert `@layout` in eine <xref:Microsoft.AspNetCore.Components.LayoutAttribute>-Eigenschaft, die auf die Komponentenklasse angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="a6532-129">The compiler converts `@layout` into a <xref:Microsoft.AspNetCore.Components.LayoutAttribute>, which is applied to the component class.</span></span>

<span data-ttu-id="a6532-130">Der Inhalt der folgenden `MasterList`-Komponente wird bei der Position von `@Body` in `MasterLayout` eingefügt:</span><span class="sxs-lookup"><span data-stu-id="a6532-130">The content of the following `MasterList` component is inserted into the `MasterLayout` at the position of `@Body`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterList.razor?highlight=1)]

<span data-ttu-id="a6532-131">Durch das direkte Festlegen eines Layouts in einer Komponente wird das im Router oder mit einer aus `_Imports.razor` importierten `@layout`-Anweisung festgelegte *Standardlayout* überschrieben.</span><span class="sxs-lookup"><span data-stu-id="a6532-131">Specifying the layout directly in a component overrides a *default layout* set in the router or an `@layout` directive imported from `_Imports.razor`.</span></span>

## <a name="centralized-layout-selection"></a><span data-ttu-id="a6532-132">Zentrale Layoutauswahl</span><span class="sxs-lookup"><span data-stu-id="a6532-132">Centralized layout selection</span></span>

<span data-ttu-id="a6532-133">Alle Ordner einer App können optional eine Vorlagendatei namens `_Imports.razor` enthalten.</span><span class="sxs-lookup"><span data-stu-id="a6532-133">Every folder of an app can optionally contain a template file named `_Imports.razor`.</span></span> <span data-ttu-id="a6532-134">Der Compiler enthält die in der Importdatei angegebenen Anweisungen in allen Razor-Vorlagen im gleichen Ordner und rekursiv in allen Unterordnern.</span><span class="sxs-lookup"><span data-stu-id="a6532-134">The compiler includes the directives specified in the imports file in all of the Razor templates in the same folder and recursively in all of its subfolders.</span></span> <span data-ttu-id="a6532-135">Daher wird mit einer `_Imports.razor`-Datei, die `@layout MyCoolLayout` enthält, sichergestellt, dass alle Komponenten in einem Ordner `MyCoolLayout` verwenden.</span><span class="sxs-lookup"><span data-stu-id="a6532-135">Therefore, an `_Imports.razor` file containing `@layout MyCoolLayout` ensures that all of the components in a folder use `MyCoolLayout`.</span></span> <span data-ttu-id="a6532-136">`@layout MyCoolLayout` muss nicht wiederholt zu allen `.razor`-Dateien im Ordner und in den Unterordnern hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a6532-136">There's no need to repeatedly add `@layout MyCoolLayout` to all of the `.razor` files within the folder and subfolders.</span></span> <span data-ttu-id="a6532-137">`@using`-Anweisungen werden auf die gleiche Weise auf Komponenten angewendet.</span><span class="sxs-lookup"><span data-stu-id="a6532-137">`@using` directives are also applied to components in the same way.</span></span>

<span data-ttu-id="a6532-138">Die folgende `_Imports.razor`-Datei importiert:</span><span class="sxs-lookup"><span data-stu-id="a6532-138">The following `_Imports.razor` file imports:</span></span>

* <span data-ttu-id="a6532-139">`MyCoolLayout`.</span><span class="sxs-lookup"><span data-stu-id="a6532-139">`MyCoolLayout`.</span></span>
* <span data-ttu-id="a6532-140">Alle Razor-Komponenten im gleichen Ordner und in dessen Unterordnern</span><span class="sxs-lookup"><span data-stu-id="a6532-140">All Razor components in the same folder and any subfolders.</span></span>
* <span data-ttu-id="a6532-141">Der `BlazorApp1.Data` -Namespace.</span><span class="sxs-lookup"><span data-stu-id="a6532-141">The `BlazorApp1.Data` namespace.</span></span>
 
[!code-razor[](layouts/sample_snapshot/3.x/_Imports.razor)]

<span data-ttu-id="a6532-142">Die Datei `_Imports.razor` ähnelt der Datei [_ViewImports.cshtml für Razor-Ansichten und -Seiten](xref:mvc/views/layout#importing-shared-directives), gilt aber spezifisch für Razor-Komponentendateien.</span><span class="sxs-lookup"><span data-stu-id="a6532-142">The `_Imports.razor` file is similar to the [_ViewImports.cshtml file for Razor views and pages](xref:mvc/views/layout#importing-shared-directives) but applied specifically to Razor component files.</span></span>

<span data-ttu-id="a6532-143">Durch das Festlegen eines Layouts in `_Imports.razor` wird ein Layout überschrieben, das als *Standardlayout* des Routers festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="a6532-143">Specifying a layout in `_Imports.razor` overrides a layout specified as the router's *default layout*.</span></span>

> [!WARNING]
> <span data-ttu-id="a6532-144">Fügen Sie der `_Imports.razor`-Stammdatei **keine** Razor `@layout`-Anweisung hinzu. Dies würde zu einer Endlosschleife von Layouts in der App führen.</span><span class="sxs-lookup"><span data-stu-id="a6532-144">Do **not** add a Razor `@layout` directive to the root `_Imports.razor` file, which results in an infinite loop of layouts in the app.</span></span> <span data-ttu-id="a6532-145">Um das Standardlayout der App zu steuern, legen Sie es in der Komponente `Router` fest.</span><span class="sxs-lookup"><span data-stu-id="a6532-145">To control the default app layout, specify the layout in the `Router` component.</span></span> <span data-ttu-id="a6532-146">Weitere Informationen finden Sie im Abschnitt [Standardlayout](#default-layout).</span><span class="sxs-lookup"><span data-stu-id="a6532-146">For more information, see the [Default layout](#default-layout) section.</span></span>

## <a name="nested-layouts"></a><span data-ttu-id="a6532-147">Geschachtelte Layouts</span><span class="sxs-lookup"><span data-stu-id="a6532-147">Nested layouts</span></span>

<span data-ttu-id="a6532-148">Apps können aus geschachtelten Layouts bestehen.</span><span class="sxs-lookup"><span data-stu-id="a6532-148">Apps can consist of nested layouts.</span></span> <span data-ttu-id="a6532-149">Eine Komponente kann auf ein Layout verweisen, das wiederum auf ein anderes Layout verweist.</span><span class="sxs-lookup"><span data-stu-id="a6532-149">A component can reference a layout which in turn references another layout.</span></span> <span data-ttu-id="a6532-150">Geschachtelte Layouts werden beispielsweise dazu verwendet, eine Menüstruktur mit mehreren Ebenen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a6532-150">For example, nesting layouts are used to create a multi-level menu structure.</span></span>

<span data-ttu-id="a6532-151">Im folgenden Beispiel wird die Verwendung von geschachtelten Layouts veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="a6532-151">The following example shows how to use nested layouts.</span></span> <span data-ttu-id="a6532-152">Die Datei `EpisodesComponent.razor` ist die Komponente, die angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="a6532-152">The `EpisodesComponent.razor` file is the component to display.</span></span> <span data-ttu-id="a6532-153">Die Komponente verweist auf `MasterListLayout`:</span><span class="sxs-lookup"><span data-stu-id="a6532-153">The component references the `MasterListLayout`:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/EpisodesComponent.razor?highlight=1)]

<span data-ttu-id="a6532-154">Die Datei `MasterListLayout.razor` stellt das `MasterListLayout` bereit.</span><span class="sxs-lookup"><span data-stu-id="a6532-154">The `MasterListLayout.razor` file provides the `MasterListLayout`.</span></span> <span data-ttu-id="a6532-155">Das Layout verweist auf ein anderes Layout (`MasterLayout`), in dem es gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="a6532-155">The layout references another layout, `MasterLayout`, where it's rendered.</span></span> <span data-ttu-id="a6532-156">`EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="a6532-156">`EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterListLayout.razor?highlight=1,9)]

<span data-ttu-id="a6532-157">Schließlich enthält das `MasterLayout` in der Datei `MasterLayout.razor` die allgemeinen Layoutelemente, z. B. die Kopfzeile, das Hauptmenü und die Fußzeile.</span><span class="sxs-lookup"><span data-stu-id="a6532-157">Finally, `MasterLayout` in `MasterLayout.razor` contains the top-level layout elements, such as the header, main menu, and footer.</span></span> <span data-ttu-id="a6532-158">Das Layout `MasterListLayout` mit der Komponente `EpisodesComponent` wird dort gerendert, wo `@Body` vorkommt:</span><span class="sxs-lookup"><span data-stu-id="a6532-158">`MasterListLayout` with the `EpisodesComponent` is rendered where `@Body` appears:</span></span>

[!code-razor[](layouts/sample_snapshot/3.x/MasterLayout.razor?highlight=6)]

## <a name="share-a-no-locrazor-pages-layout-with-integrated-components"></a><span data-ttu-id="a6532-159">Freigeben eines Razor Pages-Layouts mit integrierten Komponenten</span><span class="sxs-lookup"><span data-stu-id="a6532-159">Share a Razor Pages layout with integrated components</span></span>

<span data-ttu-id="a6532-160">Wenn routingfähige Komponenten in eine Razor Pages-App integriert werden, kann das freigegebene Layout der App mit den Komponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a6532-160">When routable components are integrated into a Razor Pages app, the app's shared layout can be used with the components.</span></span> <span data-ttu-id="a6532-161">Weitere Informationen finden Sie unter <xref:blazor/components/prerendering-and-integration>.</span><span class="sxs-lookup"><span data-stu-id="a6532-161">For more information, see <xref:blazor/components/prerendering-and-integration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a6532-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a6532-162">Additional resources</span></span>

* <xref:mvc/views/layout>
