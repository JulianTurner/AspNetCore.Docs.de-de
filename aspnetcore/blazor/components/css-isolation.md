---
title: ASP.NET Core Blazor-CSS-Isolation
author: daveabrock
description: Erfahren Sie, wie Sie mit der CSS-Isolation den Bereich von CSS auf Ihre Komponenten beschränken können, um Ihr CSS zu vereinfachen und Konflikte mit anderen Komponenten oder Bibliotheken zu vermeiden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: blazor/components/css-isolation
ms.openlocfilehash: 0748f606314963788e6733ca2ae2ca2123d839b3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99529981"
---
# <a name="aspnet-core-blazor-css-isolation"></a><span data-ttu-id="7af32-103">ASP.NET Core Blazor-CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="7af32-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="7af32-104">Von [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="7af32-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="7af32-105">Die CSS-Isolation verringert den CSS-Speicherbedarf einer App, indem Abhängigkeiten von globalen Stilen verhindert werden. Außerdem werden damit Stilkonflikte zwischen Komponenten und Bibliotheken vermieden.</span><span class="sxs-lookup"><span data-stu-id="7af32-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="7af32-106">Aktivieren der CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="7af32-106">Enable CSS isolation</span></span> 

<span data-ttu-id="7af32-107">Damit Sie komponentenspezifische Stile definieren können, erstellen Sie eine Datei `.razor.css`, die mit dem Namen der `.razor`-Datei für die Komponente im selben Ordner übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="7af32-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component in the same folder.</span></span> <span data-ttu-id="7af32-108">Die Datei `.razor.css` ist eine *bereichsbezogene CSS-Datei*.</span><span class="sxs-lookup"><span data-stu-id="7af32-108">The `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="7af32-109">Erstellen Sie für eine `Example`-Komponente in einer `Example.razor`-Datei eine Datei mit dem Namen `Example.razor.css`.</span><span class="sxs-lookup"><span data-stu-id="7af32-109">For an `Example` component in an `Example.razor` file, create a file alongside the component named `Example.razor.css`.</span></span> <span data-ttu-id="7af32-110">Die `Example.razor.css`-Datei muss sich im gleichen Ordner befinden wie die `Example`-Komponente (`Example.razor`).</span><span class="sxs-lookup"><span data-stu-id="7af32-110">The `Example.razor.css` file must reside in the same folder as the `Example` component (`Example.razor`).</span></span> <span data-ttu-id="7af32-111">Beim `Example`-Basisnamen der Datei wird die Groß-/Kleinschreibung **nicht** beachtet.</span><span class="sxs-lookup"><span data-stu-id="7af32-111">The "`Example`" base name of the file is **not** case-sensitive.</span></span>

<span data-ttu-id="7af32-112">`Pages/Example.razor`:</span><span class="sxs-lookup"><span data-stu-id="7af32-112">`Pages/Example.razor`:</span></span>

```razor
@page "/example"

<h1>Scoped CSS Example</h1>
```

<span data-ttu-id="7af32-113">`Pages/Example.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="7af32-113">`Pages/Example.razor.css`:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="7af32-114">**Die in `Example.razor.css` definierten Stile werden nur auf die gerenderte Ausgabe der `Example`-Komponente angewandt.**</span><span class="sxs-lookup"><span data-stu-id="7af32-114">**The styles defined in `Example.razor.css` are only applied to the rendered output of the `Example` component.**</span></span> <span data-ttu-id="7af32-115">Die CSS-Isolation wird auf HTML-Elemente in der entsprechenden Razor-Datei angewandt.</span><span class="sxs-lookup"><span data-stu-id="7af32-115">CSS isolation is applied to HTML elements in the matching Razor file.</span></span> <span data-ttu-id="7af32-116">`h1`-CSS-Deklarationen, die an anderer Stelle in der App definiert sind, verursachen keine Konflikte mit den Stilen der `Example`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="7af32-116">Any `h1` CSS declarations defined elsewhere in the app don't conflict with the `Example` component's styles.</span></span>

> [!NOTE]
> <span data-ttu-id="7af32-117">Damit die Stilisolation auch bei der Bündelung gewährleistet ist, wird das Importieren von CSS in Razor-Codeblöcken nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7af32-117">In order to guarantee style isolation when bundling occurs, importing CSS in Razor code blocks isn't supported.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="7af32-118">Bündelung mit CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="7af32-118">CSS isolation bundling</span></span>

<span data-ttu-id="7af32-119">Die CSS-Isolation erfolgt zum Zeitpunkt der Erstellung.</span><span class="sxs-lookup"><span data-stu-id="7af32-119">CSS isolation occurs at build time.</span></span> <span data-ttu-id="7af32-120">Während dieses Vorgangs schreibt Blazor CSS-Selektoren um, damit diese mit dem von der Komponente gerenderten Markup übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="7af32-120">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="7af32-121">Diese umgeschriebenen CSS-Stile werden gebündelt und als statisches Objekt in `{PROJECT NAME}.styles.css` erstellt, wobei der Platzhalter `{PROJECT NAME}` das referenzierte Paket oder der Produktname ist.</span><span class="sxs-lookup"><span data-stu-id="7af32-121">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="7af32-122">Auf diese statischen Dateien wird standardmäßig aus dem Stammpfad der App verwiesen.</span><span class="sxs-lookup"><span data-stu-id="7af32-122">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="7af32-123">Die Referenzierung auf die gebündelte Datei in der App erfolgt durch Überprüfen des Verweises im `<head>`-Tag des generierten HTML-Codes:</span><span class="sxs-lookup"><span data-stu-id="7af32-123">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="ProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="7af32-124">Innerhalb der gebündelten Datei ist jede Komponente einem Bereichsbezeichner zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="7af32-124">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="7af32-125">Für jede formatierte Komponente wird ein HTML-Attribut im Format `b-<10-character-string>` angehängt.</span><span class="sxs-lookup"><span data-stu-id="7af32-125">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="7af32-126">Der Bezeichner ist für jede App eindeutig.</span><span class="sxs-lookup"><span data-stu-id="7af32-126">The identifier is unique and different for each app.</span></span> <span data-ttu-id="7af32-127">In der gerenderten `Counter`-Komponente fügt Blazor einen Bereichsbezeichner an das `h1`-Element an:</span><span class="sxs-lookup"><span data-stu-id="7af32-127">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="7af32-128">Die Datei `ProjectName.styles.css` verwendet den Bereichsbezeichner, um eine Stildeklaration mit Ihrer Komponente zu gruppieren.</span><span class="sxs-lookup"><span data-stu-id="7af32-128">The `ProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="7af32-129">Im folgenden Beispiel wird der Stil für das vorherige `<h1>`-Element bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="7af32-129">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="7af32-130">Zum Zeitpunkt der Erstellung wird ein Projektbündel mit der Konvention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css` erstellt, wobei der Platzhalter `{STATIC WEB ASSETS BASE PATH}` der statische Basispfad für Webressourcen ist.</span><span class="sxs-lookup"><span data-stu-id="7af32-130">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/Project.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="7af32-131">Wenn andere Projekte verwendet werden, z. B. NuGet-Pakete oder [Razor-Klassenbibliotheken](xref:blazor/components/class-libraries), gilt für die gebündelte Datei Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7af32-131">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="7af32-132">Verweise auf Stile erfolgen mithilfe von CSS-Importen.</span><span class="sxs-lookup"><span data-stu-id="7af32-132">References the styles using CSS imports.</span></span>
* <span data-ttu-id="7af32-133">Sie wird nicht als statische Webressource der App veröffentlicht, die die Stile verwendet.</span><span class="sxs-lookup"><span data-stu-id="7af32-133">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="7af32-134">Unterstützung für untergeordnete Komponenten</span><span class="sxs-lookup"><span data-stu-id="7af32-134">Child component support</span></span>

<span data-ttu-id="7af32-135">Standardmäßig gilt die CSS-Isolation nur für die Komponente, die Sie dem Format `{COMPONENT NAME}.razor.css` zuordnen, wobei der Platzhalter `{COMPONENT NAME}` in der Regel der Komponentenname ist.</span><span class="sxs-lookup"><span data-stu-id="7af32-135">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="7af32-136">Verwenden Sie zum Anwenden von Änderungen auf eine untergeordnete Komponente den `::deep`-Kombinator für alle Nachfolgerelemente in der Datei `.razor.css` der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="7af32-136">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="7af32-137">Der `::deep`-Kombinator wählt Elemente aus, die *Nachfolger* des generierten Bereichsbezeichners eines Elements sind.</span><span class="sxs-lookup"><span data-stu-id="7af32-137">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="7af32-138">Das folgende Beispiel zeigt die übergeordnete Komponente `Parent`, die eine untergeordnete Komponente namens `Child` hat.</span><span class="sxs-lookup"><span data-stu-id="7af32-138">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="7af32-139">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7af32-139">`Pages/Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="7af32-140">`Shared/Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="7af32-140">`Shared/Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="7af32-141">Aktualisieren Sie die `h1`-Deklaration in `Parent.razor.css` mit dem `::deep`-Kombinator, um anzugeben, dass die Stildeklaration `h1` auf die übergeordnete Komponente und deren untergeordnete Komponenten angewandt werden muss.</span><span class="sxs-lookup"><span data-stu-id="7af32-141">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children.</span></span>

<span data-ttu-id="7af32-142">`Pages/Parent.razor.css`:</span><span class="sxs-lookup"><span data-stu-id="7af32-142">`Pages/Parent.razor.css`:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="7af32-143">Der `h1`-Stil gilt jetzt für die Komponenten `Parent` und `Child`, ohne dass eine separate CSS-Datei für die untergeordnete Komponente erstellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="7af32-143">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

<span data-ttu-id="7af32-144">Der `::deep`-Kombinator funktioniert nur mit Nachfolgerelementen.</span><span class="sxs-lookup"><span data-stu-id="7af32-144">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="7af32-145">Das folgende Markup wendet die `h1`-Stile wie erwartet auf Komponenten an.</span><span class="sxs-lookup"><span data-stu-id="7af32-145">The following markup applies the `h1` styles to components as expected.</span></span> <span data-ttu-id="7af32-146">Der Bereichsbezeichner der übergeordneten Komponente wird auf das `div`-Element angewendet, damit der Browser weiß, dass Stile von der übergeordneten Komponente geerbt werden.</span><span class="sxs-lookup"><span data-stu-id="7af32-146">The parent component's scope identifier is applied to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>

<span data-ttu-id="7af32-147">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7af32-147">`Pages/Parent.razor`:</span></span>

```razor
<div>
    <h1>Parent</h1>

    <Child />
</div>
```

<span data-ttu-id="7af32-148">Durch Ausschließen des `div`-Elements wird jedoch die Nachfolgerbeziehung entfernt.</span><span class="sxs-lookup"><span data-stu-id="7af32-148">However, excluding the `div` element removes the descendant relationship.</span></span> <span data-ttu-id="7af32-149">Im folgenden Beispiel wird der Stil **nicht** auf die untergeordnete Komponente angewendet.</span><span class="sxs-lookup"><span data-stu-id="7af32-149">In the following example, the style is **not** applied to the child component.</span></span>

<span data-ttu-id="7af32-150">`Pages/Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="7af32-150">`Pages/Parent.razor`:</span></span>

```razor
<h1>Parent</h1>

<Child />
```

## <a name="css-preprocessor-support"></a><span data-ttu-id="7af32-151">Unterstützung für CSS-Präprozessoren</span><span class="sxs-lookup"><span data-stu-id="7af32-151">CSS preprocessor support</span></span>

<span data-ttu-id="7af32-152">CSS-Präprozessoren tragen zur Verbesserung der CSS-Entwicklung bei, indem sie Features wie Variablen, Schachtelung, Module, Mischung und Vererbung nutzen.</span><span class="sxs-lookup"><span data-stu-id="7af32-152">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="7af32-153">CSS-Isolation unterstützt CSS-Präprozessoren wie Sass oder Less zwar nicht nativ, dennoch können CSS-Präprozessoren nahtlos integriert werden, sofern die Kompilierung des Präprozessors erfolgt, bevor Blazor die CSS-Selektoren während des Buildprozesses umschreibt.</span><span class="sxs-lookup"><span data-stu-id="7af32-153">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="7af32-154">Konfigurieren Sie z. B. mithilfe von Visual Studio die vorhandene Präprozessorkompilierung als Aufgabe **Vor Build** im Aufgabenausführungs-Explorer von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7af32-154">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="7af32-155">Viele NuGet-Pakete von Drittanbietern (z. B. [Delegat.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)) können die SASS-/SCSS-Dateien am Anfang des Buildprozesses – noch vor der CSS-Isolation – kompilieren, ohne dass eine Konfiguration erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7af32-155">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="7af32-156">Konfiguration der CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="7af32-156">CSS isolation configuration</span></span>

<span data-ttu-id="7af32-157">Die CSS-Isolation ist so konzipiert, dass sie sofort einsatzbereit ist. Es ist aber eine Konfiguration für einige erweiterte Szenarien möglich, wenn z. B. Abhängigkeiten von vorhandenen Tools oder Workflows vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="7af32-157">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="7af32-158">Anpassen des Formats von Bereichsbezeichnern</span><span class="sxs-lookup"><span data-stu-id="7af32-158">Customize scope identifier format</span></span>

<span data-ttu-id="7af32-159">Standardmäßig wird für Bereichsbezeichner das Format `b-<10-character-string>` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7af32-159">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="7af32-160">Um das Format der Bereichsbezeichner anzupassen, aktualisieren Sie die Projektdatei mit dem gewünschten Muster:</span><span class="sxs-lookup"><span data-stu-id="7af32-160">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/Example.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="7af32-161">Im vorherigen Beispiel ändert der für `Example.Razor.css` generierte CSS-Code seinen Bereichsbezeichner von `b-<10-character-string>` in `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="7af32-161">In the preceding example, the CSS generated for `Example.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

<span data-ttu-id="7af32-162">Verwenden Sie Bereichsbezeichner, um eine Vererbung mit bereichsbezogenen CSS-Dateien zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="7af32-162">Use scope identifiers to achieve inheritance with scoped CSS files.</span></span> <span data-ttu-id="7af32-163">Im folgenden Beispiel für eine Projektdatei enthält eine `BaseComponent.razor.css`-Datei allgemeine Stile in Komponenten.</span><span class="sxs-lookup"><span data-stu-id="7af32-163">In the following project file example, a `BaseComponent.razor.css` file contains common styles across components.</span></span> <span data-ttu-id="7af32-164">Eine `DerivedComponent.razor.css`-Datei erbt diese Stile.</span><span class="sxs-lookup"><span data-stu-id="7af32-164">A `DerivedComponent.razor.css` file inherits these styles.</span></span>

```xml
<ItemGroup>
  <None Update="Pages/BaseComponent.razor.css" CssScope="my-custom-scope-identifier" />
  <None Update="Pages/DerivedComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="7af32-165">Verwenden Sie den Platzhalteroperator (`*`), um Bereichsbezeichner für mehrere Dateien freizugeben:</span><span class="sxs-lookup"><span data-stu-id="7af32-165">Use the wildcard (`*`) operator to share scope identifiers across multiple files:</span></span>

```xml
<ItemGroup>
  <None Update="Pages/*.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="7af32-166">Ändern des Basispfads für statische Webressourcen</span><span class="sxs-lookup"><span data-stu-id="7af32-166">Change base path for static web assets</span></span>

<span data-ttu-id="7af32-167">Die Datei `scoped.styles.css` wird im Stammverzeichnis der App generiert.</span><span class="sxs-lookup"><span data-stu-id="7af32-167">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="7af32-168">Verwenden Sie in der Projektdatei die `StaticWebAssetBasePath`-Eigenschaft, um den Standardpfad zu ändern.</span><span class="sxs-lookup"><span data-stu-id="7af32-168">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="7af32-169">Im folgenden Beispiel werden die Datei `scoped.styles.css` und die restlichen Ressourcen der App unter dem Pfad `_content` gespeichert:</span><span class="sxs-lookup"><span data-stu-id="7af32-169">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="7af32-170">Deaktivieren der automatischen Bündelung</span><span class="sxs-lookup"><span data-stu-id="7af32-170">Disable automatic bundling</span></span>

<span data-ttu-id="7af32-171">Wenn Sie das Verhalten von Blazor beim Veröffentlichen und Laden bereichsbezogener Dateien zur Laufzeit ändern möchten, verwenden Sie die `DisableScopedCssBundling`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="7af32-171">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="7af32-172">Die Verwendung dieser Eigenschaft bedeutet, dass die isolierten CSS-Dateien durch andere Tools oder Prozesse aus dem Verzeichnis `obj` übernommen und zur Laufzeit veröffentlicht und geladen werden:</span><span class="sxs-lookup"><span data-stu-id="7af32-172">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="razor-class-library-rcl-support"></a><span data-ttu-id="7af32-173">Unterstützung für Razor-Klassenbibliothek (RCL)</span><span class="sxs-lookup"><span data-stu-id="7af32-173">Razor class library (RCL) support</span></span>

<span data-ttu-id="7af32-174">Wenn eine [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) isolierte Stile bereitstellt, zeigt das `href`-Attribut des `<link>`-Tags auf `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, wobei die Platzhalter für Folgendes stehen:</span><span class="sxs-lookup"><span data-stu-id="7af32-174">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="7af32-175">`{STATIC WEB ASSET BASE PATH}`: Basispfad der statischen Webressource</span><span class="sxs-lookup"><span data-stu-id="7af32-175">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="7af32-176">`{ASSEMBLY NAME}`: Assemblyname der Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="7af32-176">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="7af32-177">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7af32-177">In the following example:</span></span>

* <span data-ttu-id="7af32-178">Der Basispfad der statischen Webressource lautet `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="7af32-178">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="7af32-179">Der Assemblyname der Klassenbibliothek lautet `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="7af32-179">The class library's assembly name is `ClassLib`.</span></span>

<span data-ttu-id="7af32-180">`wwwroot/index.html` (Blazor WebAssembly) oder `Pages_Host.cshtml` (Blazor Server):</span><span class="sxs-lookup"><span data-stu-id="7af32-180">`wwwroot/index.html` (Blazor WebAssembly) or `Pages_Host.cshtml` (Blazor Server):</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="7af32-181">Weitere Informationen zu RCLs und Komponentenbibliotheken finden Sie unter folgendem Link:</span><span class="sxs-lookup"><span data-stu-id="7af32-181">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="7af32-182"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="7af32-182"><xref:blazor/components/class-libraries>.</span></span>
