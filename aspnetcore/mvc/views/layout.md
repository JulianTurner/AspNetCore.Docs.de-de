---
title: Layout in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie man gängige Layouts verwendet, Anweisungen von mehreren Ansichten gemeinsam nutzen lässt und Programmcode vor dem Rendern der Ansichten in einer ASP.NET Core-App ausführt.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 502df268e7f5f33acfffccd5ec0bd65267fa12da
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060975"
---
# <a name="layout-in-aspnet-core"></a><span data-ttu-id="4c27f-103">Layout in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4c27f-103">Layout in ASP.NET Core</span></span>

<span data-ttu-id="4c27f-104">Von [Steve Smith](https://ardalis.com/) und [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="4c27f-104">By [Steve Smith](https://ardalis.com/) and [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="4c27f-105">Seiten und Ansichten beinhalten häufig sowohl visuelle als auch programmgesteuerte Elemente.</span><span class="sxs-lookup"><span data-stu-id="4c27f-105">Pages and views frequently share visual and programmatic elements.</span></span> <span data-ttu-id="4c27f-106">Dieser Artikel demonstriert Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4c27f-106">This article demonstrates how to:</span></span>

* <span data-ttu-id="4c27f-107">Verwendung von allgemeinen Layouts</span><span class="sxs-lookup"><span data-stu-id="4c27f-107">Use common layouts.</span></span>
* <span data-ttu-id="4c27f-108">Freigeben von Anweisungen</span><span class="sxs-lookup"><span data-stu-id="4c27f-108">Share directives.</span></span>
* <span data-ttu-id="4c27f-109">Führen Sie den allgemeinen Code aus, bevor Sie Seiten oder Ansichten rendern.</span><span class="sxs-lookup"><span data-stu-id="4c27f-109">Run common code before rendering pages or views.</span></span>

<span data-ttu-id="4c27f-110">In diesem Dokument werden die Layouts für die beiden unterschiedlichen Ansätze zum ASP.net Core MVC erläutert: :::no-loc(Razor)::: Seiten und Controller mit Ansichten.</span><span class="sxs-lookup"><span data-stu-id="4c27f-110">This document discusses layouts for the two different approaches to ASP.NET Core MVC: :::no-loc(Razor)::: Pages and controllers with views.</span></span> <span data-ttu-id="4c27f-111">In diesem Thema sind die Unterschiede minimal:</span><span class="sxs-lookup"><span data-stu-id="4c27f-111">For this topic, the differences are minimal:</span></span>

* <span data-ttu-id="4c27f-112">:::no-loc(Razor)::: Seiten befinden sich im Ordner *pages* .</span><span class="sxs-lookup"><span data-stu-id="4c27f-112">:::no-loc(Razor)::: Pages are in the *Pages* folder.</span></span>
* <span data-ttu-id="4c27f-113">Controller mit Ansichten verwenden einen Ordner namens *Views* für Ansichten.</span><span class="sxs-lookup"><span data-stu-id="4c27f-113">Controllers with views uses a *Views* folder for views.</span></span>

## <a name="what-is-a-layout"></a><span data-ttu-id="4c27f-114">Was ist ein Layout?</span><span class="sxs-lookup"><span data-stu-id="4c27f-114">What is a Layout</span></span>

<span data-ttu-id="4c27f-115">Die meisten Web-Apps haben ein gebräuchliches Layout, das dem Benutzer beim Navigieren auf den Seiten ein konsistentes Verhalten bietet.</span><span class="sxs-lookup"><span data-stu-id="4c27f-115">Most web apps have a common layout that provides the user with a consistent experience as they navigate from page to page.</span></span> <span data-ttu-id="4c27f-116">Das Layout enthält normalerweise allgemeine Benutzeroberflächenelemente wie App-Header, Navigations- oder Menüelemente sowie eine Fußzeile.</span><span class="sxs-lookup"><span data-stu-id="4c27f-116">The layout typically includes common user interface elements such as the app header, navigation or menu elements, and footer.</span></span>

![Beispiel für ein Seitenlayout](layout/_static/page-layout.png)

<span data-ttu-id="4c27f-118">Oft verwendete HTML-Strukturen wie Skripts und Stylesheets werden auch häufig von vielen Seiten in einer App genutzt.</span><span class="sxs-lookup"><span data-stu-id="4c27f-118">Common HTML structures such as scripts and stylesheets are also frequently used by many pages within an app.</span></span> <span data-ttu-id="4c27f-119">Alle diese freigegebenen Elemente können in einer *Layoutdatei* definiert werden, auf die von jeder in der APP verwendeten Ansicht verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="4c27f-119">All of these shared elements may be defined in a *layout* file, which can then be referenced by any view used within the app.</span></span> <span data-ttu-id="4c27f-120">Layouts verringern Codeduplikate in Ansichten.</span><span class="sxs-lookup"><span data-stu-id="4c27f-120">Layouts reduce duplicate code in views.</span></span>

<span data-ttu-id="4c27f-121">Gemäß Konvention ist *_Layout.cshtml* das Standardlayout für eine ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="4c27f-121">By convention, the default layout for an ASP.NET Core app is named *_Layout.cshtml* .</span></span> <span data-ttu-id="4c27f-122">Für neue ASP.NET Core-Projekte, die mit den Vorlagen erstellt wurden, sind folgende Layoutdateien vorhanden:</span><span class="sxs-lookup"><span data-stu-id="4c27f-122">The layout files for new ASP.NET Core projects created with the templates are:</span></span>

* <span data-ttu-id="4c27f-123">:::no-loc(Razor)::: Seiten: *pages/Shared/_Layout. cshtml*</span><span class="sxs-lookup"><span data-stu-id="4c27f-123">:::no-loc(Razor)::: Pages: *Pages/Shared/_Layout.cshtml*</span></span>

  ![Ordner „Pages“ im Projektmappen-Explorer](layout/_static/rp-web-project-views.png)

* <span data-ttu-id="4c27f-125">Controller mit Ansichten: *Views/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="4c27f-125">Controller with views: *Views/Shared/_Layout.cshtml*</span></span>

  ![Ordner „Views“ im Projektmappen-Explorer](layout/_static/mvc-web-project-views.png)

<span data-ttu-id="4c27f-127">Das Layout definiert eine übergeordnete Vorlage für die Ansichten einer App.</span><span class="sxs-lookup"><span data-stu-id="4c27f-127">The layout defines a top level template for views in the app.</span></span> <span data-ttu-id="4c27f-128">Für Apps ist kein Layout erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4c27f-128">Apps don't require a layout.</span></span> <span data-ttu-id="4c27f-129">Sie können allerdings mehrere Layouts mit unterschiedlichen Ansichten aufweisen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-129">Apps can define more than one layout, with different views specifying different layouts.</span></span>

<span data-ttu-id="4c27f-130">Der folgende Code zeigt die Layoutdatei für eine Vorlage, die mit dem Projekt mit einem Controller und Ansichten erstellt wurde:</span><span class="sxs-lookup"><span data-stu-id="4c27f-130">The following code shows the layout file for a template created project with a controller and views:</span></span>

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a><span data-ttu-id="4c27f-131">Festlegen eines Layouts</span><span class="sxs-lookup"><span data-stu-id="4c27f-131">Specifying a Layout</span></span>

<span data-ttu-id="4c27f-132">:::no-loc(Razor)::: Sichten verfügen über eine- `Layout` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4c27f-132">:::no-loc(Razor)::: views have a `Layout` property.</span></span> <span data-ttu-id="4c27f-133">Durch Festlegen dieser Eigenschaft wird das Layout der jeweiligen Ansicht bestimmt:</span><span class="sxs-lookup"><span data-stu-id="4c27f-133">Individual views specify a layout by setting this property:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

<span data-ttu-id="4c27f-134">Das angegebene Layout kann einen vollständigen Pfad (z.B. */Pages/Shared/_Layout.cshtml* oder */Views/Shared/_Layout.cshtml* ) oder einen Teil des Namens (Beispiel: `_Layout`) aufweisen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-134">The layout specified can use a full path (for example, */Pages/Shared/_Layout.cshtml* or */Views/Shared/_Layout.cshtml* ) or a partial name (example: `_Layout`).</span></span> <span data-ttu-id="4c27f-135">Wenn ein partieller Name angegeben wird, :::no-loc(Razor)::: sucht die Ansichts-Engine mithilfe Ihres Standard Ermittlungs Prozesses nach der Layoutdatei.</span><span class="sxs-lookup"><span data-stu-id="4c27f-135">When a partial name is provided, the :::no-loc(Razor)::: view engine searches for the layout file using its standard discovery process.</span></span> <span data-ttu-id="4c27f-136">Der Ordner, in dem sich die die Handlermethode (oder der Controller) befindet, wird zuerst durchsucht und danach der Ordner *Shared* .</span><span class="sxs-lookup"><span data-stu-id="4c27f-136">The folder where the handler method (or controller) exists is searched first, followed by the *Shared* folder.</span></span> <span data-ttu-id="4c27f-137">Dieser Ermittlungsprozess ist identisch mit dem Prozess zum Auffinden von [Teilansichten](xref:mvc/views/partial#partial-view-discovery).</span><span class="sxs-lookup"><span data-stu-id="4c27f-137">This discovery process is identical to the process used to discover [partial views](xref:mvc/views/partial#partial-view-discovery).</span></span>

<span data-ttu-id="4c27f-138">Standardmäßig muss jedes Layout `RenderBody` aufrufen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-138">By default, every layout must call `RenderBody`.</span></span> <span data-ttu-id="4c27f-139">Wo immer der Aufruf von `RenderBody` platziert ist, wird der Inhalt der Ansicht gerendert.</span><span class="sxs-lookup"><span data-stu-id="4c27f-139">Wherever the call to `RenderBody` is placed, the contents of the view will be rendered.</span></span>

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a><span data-ttu-id="4c27f-140">Abschnitte</span><span class="sxs-lookup"><span data-stu-id="4c27f-140">Sections</span></span>

<span data-ttu-id="4c27f-141">Optional kann ein Layout auf mindestens einen *Abschnitt* verweisen, indem es `RenderSection` aufruft.</span><span class="sxs-lookup"><span data-stu-id="4c27f-141">A layout can optionally reference one or more *sections* , by calling `RenderSection`.</span></span> <span data-ttu-id="4c27f-142">Abschnitte geben an, wo bestimmte Seitenelemente platziert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-142">Sections provide a way to organize where certain page elements should be placed.</span></span> <span data-ttu-id="4c27f-143">Jeder Aufruf von `RenderSection` kann angeben, ob dieser Abschnitt erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="4c27f-143">Each call to `RenderSection` can specify whether that section is required or optional:</span></span>

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

<span data-ttu-id="4c27f-144">Wenn ein erforderlicher Bereich nicht gefunden werden kann, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="4c27f-144">If a required section isn't found, an exception is thrown.</span></span> <span data-ttu-id="4c27f-145">Einzelne Ansichten geben den Inhalt an, der in einem Abschnitt mithilfe der Syntax gerendert werden soll `@section` :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="4c27f-145">Individual views specify the content to be rendered within a section using the `@section` :::no-loc(Razor)::: syntax.</span></span> <span data-ttu-id="4c27f-146">Wenn eine Seite oder eine Ansicht einen Abschnitt definiert, muss dieser auch gerendert werden. Andernfalls tritt ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="4c27f-146">If a page or view defines a section, it must be rendered (or an error will occur).</span></span>

<span data-ttu-id="4c27f-147">Eine Beispiel `@section` Definition in der :::no-loc(Razor)::: Seitenansicht:</span><span class="sxs-lookup"><span data-stu-id="4c27f-147">An example `@section` definition in :::no-loc(Razor)::: Pages view:</span></span>

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

<span data-ttu-id="4c27f-148">Im vorangehenden Code wird *scripts/main.js* zum Abschnitt `scripts` auf einer Seite oder in einer Ansicht hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4c27f-148">In the preceding code, *scripts/main.js* is added to the `scripts` section on a page or view.</span></span> <span data-ttu-id="4c27f-149">Andere Seiten oder Ansichten in der gleichen App benötigen dieses Skript möglicherweise nicht und definieren einen Abschnitt zu Skripts.</span><span class="sxs-lookup"><span data-stu-id="4c27f-149">Other pages or views in the same app might not require this script and wouldn't define a scripts section.</span></span>

<span data-ttu-id="4c27f-150">In der folgenden Markupdatei wird die Datei *_ValidationScriptsPartial.cshtml* mit dem [Partial-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) gerendert:</span><span class="sxs-lookup"><span data-stu-id="4c27f-150">The following markup uses the [Partial Tag Helper](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) to render  *_ValidationScriptsPartial.cshtml* :</span></span>

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

<span data-ttu-id="4c27f-151">Das vorangehende Markup wurde durch [ :::no-loc(Identity)::: Gerüstbau ](xref:security/authentication/scaffold-identity)generiert.</span><span class="sxs-lookup"><span data-stu-id="4c27f-151">The preceding markup was generated by [scaffolding :::no-loc(Identity):::](xref:security/authentication/scaffold-identity).</span></span>

<span data-ttu-id="4c27f-152">Die Abschnitte, die auf einer Seite oder in einer Ansicht definiert wurden, stehen nur auf deren Layoutseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4c27f-152">Sections defined in a page or view are available only in its immediate layout page.</span></span> <span data-ttu-id="4c27f-153">Teilansichten, Ansichtskomponenten und andere Teile eines Ansichtssystems können nicht auf sie verweisen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-153">They cannot be referenced from partials, view components, or other parts of the view system.</span></span>

### <a name="ignoring-sections"></a><span data-ttu-id="4c27f-154">Ignorieren von Abschnitten</span><span class="sxs-lookup"><span data-stu-id="4c27f-154">Ignoring sections</span></span>

<span data-ttu-id="4c27f-155">Standardmäßig müssen der Text und die Abschnitte einer Inhaltsseite alle von der Layoutseite gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-155">By default, the body and all sections in a content page must all be rendered by the layout page.</span></span> <span data-ttu-id="4c27f-156">Die :::no-loc(Razor)::: Ansichts-Engine erzwingt dies durch nachverfolgen, ob der Text und jeder Abschnitt gerendert wurden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-156">The :::no-loc(Razor)::: view engine enforces this by tracking whether the body and each section have been rendered.</span></span>

<span data-ttu-id="4c27f-157">Rufen Sie die Methoden `IgnoreBody` und `IgnoreSection` auf, um die Ansichtsengine anzuweisen, den Text oder die Abschnitte zu ignorieren.</span><span class="sxs-lookup"><span data-stu-id="4c27f-157">To instruct the view engine to ignore the body or sections, call the `IgnoreBody` and `IgnoreSection` methods.</span></span>

<span data-ttu-id="4c27f-158">Der Text und jeder Abschnitt einer :::no-loc(Razor)::: Seite müssen entweder gerendert oder ignoriert werden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-158">The body and every section in a :::no-loc(Razor)::: page must be either rendered or ignored.</span></span>

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a><span data-ttu-id="4c27f-159">Importieren gemeinsam verwendeter Anweisungen</span><span class="sxs-lookup"><span data-stu-id="4c27f-159">Importing Shared Directives</span></span>

<span data-ttu-id="4c27f-160">Sichten und Seiten können :::no-loc(Razor)::: -Direktiven verwenden, um Namespaces zu importieren und [Abhängigkeitsinjektion](dependency-injection.md)zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-160">Views and pages can use :::no-loc(Razor)::: directives to import namespaces and use [dependency injection](dependency-injection.md).</span></span> <span data-ttu-id="4c27f-161">Anweisungen, die von mehreren Ansichten gemeinsam verwendet werden, können in einer gemeinsam verwendeten Datei namens *_ViewImports.cshtml* angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-161">Directives shared by many views may be specified in a common *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="4c27f-162">Die `_ViewImports`-Datei unterstützt die folgenden Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="4c27f-162">The `_ViewImports` file supports the following directives:</span></span>

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

<span data-ttu-id="4c27f-163">Die Datei unterstützt keine anderen :::no-loc(Razor)::: Features, wie z. b. Funktionen und Abschnitts Definitionen.</span><span class="sxs-lookup"><span data-stu-id="4c27f-163">The file doesn't support other :::no-loc(Razor)::: features, such as functions and section definitions.</span></span>

<span data-ttu-id="4c27f-164">Eine `_ViewImports.cshtml`-Beispieldatei:</span><span class="sxs-lookup"><span data-stu-id="4c27f-164">A sample `_ViewImports.cshtml` file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

<span data-ttu-id="4c27f-165">Die Datei *_ViewImports.cshtml* für eine ASP.NET Core MVC-App befindet sich normalerweise im Ordner *Pages* (oder *Views* ).</span><span class="sxs-lookup"><span data-stu-id="4c27f-165">The *_ViewImports.cshtml* file for an ASP.NET Core MVC app is typically placed in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="4c27f-166">Eine Datei *_ViewImports.cshtml* kann auch in einen anderen Ordner verschoben werden. In diesem Fall wird sie nur auf die Seiten oder Ansichten in diesem Ordner und in dessen Unterordnern angewendet.</span><span class="sxs-lookup"><span data-stu-id="4c27f-166">A *_ViewImports.cshtml* file can be placed within any folder, in which case it will only be applied to pages or views within that folder and its subfolders.</span></span> <span data-ttu-id="4c27f-167">`_ViewImports`-Dateien werden beginnend ab der Stammebene verarbeitet und dann einzeln für jeden Ordner bis zum Speicherort der Seite oder der Ansicht selbst.</span><span class="sxs-lookup"><span data-stu-id="4c27f-167">`_ViewImports` files are processed starting at the root level and then for each folder leading up to the location of the page or view itself.</span></span> <span data-ttu-id="4c27f-168">Die `_ViewImports`-Einstellungen auf der Stammebene werden möglicherweise auf Ordnerebene außer Kraft gesetzt.</span><span class="sxs-lookup"><span data-stu-id="4c27f-168">`_ViewImports` settings specified at the root level may be overridden at the folder level.</span></span>

<span data-ttu-id="4c27f-169">Nehmen Sie beispielsweise Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="4c27f-169">For example, suppose:</span></span>

* <span data-ttu-id="4c27f-170">Die Datei *_ViewImports.cshtml* auf der Stammebene enthält `@model MyModel1` und `@addTagHelper *, MyTagHelper1`.</span><span class="sxs-lookup"><span data-stu-id="4c27f-170">The  root level *_ViewImports.cshtml* file includes `@model MyModel1` and `@addTagHelper *, MyTagHelper1`.</span></span>
* <span data-ttu-id="4c27f-171">Die Datei *_ViewImports.cshtml* in einem Unterordner enthält `@model MyModel2` und `@addTagHelper *, MyTagHelper2`.</span><span class="sxs-lookup"><span data-stu-id="4c27f-171">A subfolder  *_ViewImports.cshtml* file includes `@model MyModel2` and `@addTagHelper *, MyTagHelper2`.</span></span>

<span data-ttu-id="4c27f-172">Seiten und Ansichten im Unterordner haben Zugriff auf beide Taghilfsprogramme und das Modell `MyModel2`.</span><span class="sxs-lookup"><span data-stu-id="4c27f-172">Pages and views in the subfolder will have access to both Tag Helpers and the `MyModel2` model.</span></span>

<span data-ttu-id="4c27f-173">Wenn sich mehrere Dateien namens *_ViewImports.cshtml* in der Hierarchie befinden, umfasst das kombinierte Verhalten der Anweisungen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="4c27f-173">If multiple *_ViewImports.cshtml* files are found in the file hierarchy, the combined behavior of the directives are:</span></span>

* <span data-ttu-id="4c27f-174">`@addTagHelper`, `@removeTagHelper`: werden nach der Reihe ausgeführt</span><span class="sxs-lookup"><span data-stu-id="4c27f-174">`@addTagHelper`, `@removeTagHelper`: all run, in order</span></span>
* <span data-ttu-id="4c27f-175">`@tagHelperPrefix`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft</span><span class="sxs-lookup"><span data-stu-id="4c27f-175">`@tagHelperPrefix`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="4c27f-176">`@model`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft</span><span class="sxs-lookup"><span data-stu-id="4c27f-176">`@model`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="4c27f-177">`@inherits`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft</span><span class="sxs-lookup"><span data-stu-id="4c27f-177">`@inherits`: the closest one to the view overrides any others</span></span>
* <span data-ttu-id="4c27f-178">`@using`: alle einbezogen; Duplikate werden ignoriert</span><span class="sxs-lookup"><span data-stu-id="4c27f-178">`@using`: all are included; duplicates are ignored</span></span>
* <span data-ttu-id="4c27f-179">`@inject`: dasjenige, das der Ansicht am Nächsten ist, setzt für jede Eigenschaft alle anderen mit dem gleichen Namen außer Kraft</span><span class="sxs-lookup"><span data-stu-id="4c27f-179">`@inject`: for each property, the closest one to the view overrides any others with the same property name</span></span>

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a><span data-ttu-id="4c27f-180">Ausführen von Code vor jeder Ansicht</span><span class="sxs-lookup"><span data-stu-id="4c27f-180">Running Code Before Each View</span></span>

<span data-ttu-id="4c27f-181">Code, der ausgeführt werden muss, bevor die einzelnen Ansichten oder Seiten in die Datei *_ViewStart.cshtml* platziert werden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-181">Code that needs to run before each view or page should be placed in the *_ViewStart.cshtml* file.</span></span> <span data-ttu-id="4c27f-182">Gemäß der Konvention befindet sich die Datei *_ViewStart.cshtml* im Ordner *Seiten* (oder *Ansichten* ).</span><span class="sxs-lookup"><span data-stu-id="4c27f-182">By convention, the *_ViewStart.cshtml* file is located in the *Pages* (or *Views* ) folder.</span></span> <span data-ttu-id="4c27f-183">Die in *_ViewStart.cshtml* aufgelisteten Anweisungen werden vor jeder vollständigen Ansicht (also keine Layouts und keine Teilansichten) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4c27f-183">The statements listed in *_ViewStart.cshtml* are run before every full view (not layouts, and not partial views).</span></span> <span data-ttu-id="4c27f-184">*_ViewStart.cshtml* ist genauso wie [ViewImports.cshtml](xref:mvc/views/layout#viewimports) hierarchisch.</span><span class="sxs-lookup"><span data-stu-id="4c27f-184">Like [ViewImports.cshtml](xref:mvc/views/layout#viewimports), *_ViewStart.cshtml* is hierarchical.</span></span> <span data-ttu-id="4c27f-185">Wenn eine Datei namens *_ViewStart.cshtml* im Ordner „Ansichten“ oder „Seiten“, der mit einem Controller verknüpft ist, definiert wird, wird sie nach derjenigen ausgeführt, die im Stamm des Ordners *Seiten* (oder *Ansichten* ) definiert wurde (falls vorhanden).</span><span class="sxs-lookup"><span data-stu-id="4c27f-185">If a *_ViewStart.cshtml* file is defined in the view or pages folder, it will be run after the one defined in the root of the *Pages* (or *Views* ) folder (if any).</span></span>

<span data-ttu-id="4c27f-186">Ein Beispiel für die Datei *_ViewStart.cshtml* :</span><span class="sxs-lookup"><span data-stu-id="4c27f-186">A sample *_ViewStart.cshtml* file:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

<span data-ttu-id="4c27f-187">Die oben stehende Datei gibt an, dass alle Ansichten das Layout *_Layout.cshtml* verwenden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-187">The file above specifies that all views will use the *_Layout.cshtml* layout.</span></span>

<span data-ttu-id="4c27f-188">*_ViewStart.cshtml* und *_ViewImports.cshtml* werden in der Regel **nicht** in den Ordner */Pages/Shared* (oder */Views/Shared* ) platziert.</span><span class="sxs-lookup"><span data-stu-id="4c27f-188">*_ViewStart.cshtml* and *_ViewImports.cshtml* are **not** typically placed in the */Pages/Shared* (or */Views/Shared* ) folder.</span></span> <span data-ttu-id="4c27f-189">Die Versionen dieser Dateien auf Anwendungsebene sollten direkt in den Ordner */Pages* (oder */Views* ) platziert werden.</span><span class="sxs-lookup"><span data-stu-id="4c27f-189">The app-level versions of these files should be placed directly in the */Pages* (or */Views* ) folder.</span></span>
