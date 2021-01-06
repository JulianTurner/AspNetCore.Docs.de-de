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
ms.openlocfilehash: 92545eab4004f6b67080f79d64b94bb424d5a102
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "96320082"
---
# <a name="aspnet-core-no-locblazor-css-isolation"></a><span data-ttu-id="70ed6-103">ASP.NET Core Blazor-CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="70ed6-103">ASP.NET Core Blazor CSS isolation</span></span>

<span data-ttu-id="70ed6-104">Von [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="70ed6-104">By [Dave Brock](https://twitter.com/daveabrock)</span></span>

<span data-ttu-id="70ed6-105">Die CSS-Isolation verringert den CSS-Speicherbedarf einer App, indem Abhängigkeiten von globalen Stilen verhindert werden. Außerdem werden damit Stilkonflikte zwischen Komponenten und Bibliotheken vermieden.</span><span class="sxs-lookup"><span data-stu-id="70ed6-105">CSS isolation simplifies an app's CSS footprint by preventing dependencies on global styles and helps to avoid styling conflicts among components and libraries.</span></span>

## <a name="enable-css-isolation"></a><span data-ttu-id="70ed6-106">Aktivieren der CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="70ed6-106">Enable CSS isolation</span></span> 

<span data-ttu-id="70ed6-107">Damit Sie komponentenspezifische Stile definieren können, erstellen Sie eine Datei `.razor.css`, die mit dem Namen der `.razor`-Datei für die Komponente übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="70ed6-107">To define component-specific styles, create a `.razor.css` file matching the name of the `.razor` file for the component.</span></span> <span data-ttu-id="70ed6-108">Eine solche Datei `.razor.css` ist eine *bereichsbezogene CSS-Datei*.</span><span class="sxs-lookup"><span data-stu-id="70ed6-108">This `.razor.css` file is a *scoped CSS file*.</span></span> 

<span data-ttu-id="70ed6-109">Erstellen Sie für eine `MyComponent`-Komponente, die eine Datei `MyComponent.razor` enthält, eine Datei mit dem Namen `MyComponent.razor.css`.</span><span class="sxs-lookup"><span data-stu-id="70ed6-109">For a `MyComponent` component that has a `MyComponent.razor` file, create a file alongside the component called `MyComponent.razor.css`.</span></span> <span data-ttu-id="70ed6-110">Beim `MyComponent`-Wert im Dateinamen von `.razor.css` wird **nicht** zwischen Groß- und Kleinschreibung unterschieden.</span><span class="sxs-lookup"><span data-stu-id="70ed6-110">The `MyComponent` value in the `.razor.css` filename is **not** case-sensitive.</span></span>

<span data-ttu-id="70ed6-111">Wenn Sie beispielsweise der `Counter`-Komponente in der Standardprojektvorlage Blazor CSS-Isolation hinzufügen möchten, fügen Sie neben der Datei `Counter.razor` eine neue Datei mit dem Namen `Counter.razor.css` und dann folgenden CSS-Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="70ed6-111">For example to add CSS isolation to the `Counter` component in the default Blazor project template, add a new file named `Counter.razor.css` alongside the `Counter.razor` file, then add the following CSS:</span></span>

```css
h1 { 
    color: brown;
    font-family: Tahoma, Geneva, Verdana, sans-serif;
}
```

<span data-ttu-id="70ed6-112">Die in `Counter.razor.css` definierten Stile werden nur auf die gerenderte Ausgabe der `Counter`-Komponente angewandt.</span><span class="sxs-lookup"><span data-stu-id="70ed6-112">The styles defined in `Counter.razor.css` are only applied to the rendered output of the `Counter` component.</span></span> <span data-ttu-id="70ed6-113">`h1`-CSS-Deklarationen, die an anderer Stelle in der App definiert sind, verursachen keine Konflikte mit `Counter`-Stilen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-113">Any `h1` CSS declarations defined elsewhere in the app don't conflict with `Counter` styles.</span></span>

> [!NOTE]
> <span data-ttu-id="70ed6-114">Damit die Stilisolation auch bei der Bündelung gewährleistet ist, werden `@import` Razor-Blöcke in bereichsbezogenen CSS-Dateien nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="70ed6-114">In order to guarantee style isolation when bundling occurs, `@import` Razor blocks aren't supported with scoped CSS files.</span></span>

## <a name="css-isolation-bundling"></a><span data-ttu-id="70ed6-115">Bündelung mit CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="70ed6-115">CSS isolation bundling</span></span>

<span data-ttu-id="70ed6-116">Die CSS-Isolation erfolgt zum Zeitpunkt der Erstellung.</span><span class="sxs-lookup"><span data-stu-id="70ed6-116">CSS isolation occurs at build time.</span></span> <span data-ttu-id="70ed6-117">Während dieses Vorgangs schreibt Blazor CSS-Selektoren um, damit diese mit dem von der Komponente gerenderten Markup übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-117">During this process, Blazor rewrites CSS selectors to match markup rendered by the component.</span></span> <span data-ttu-id="70ed6-118">Diese umgeschriebenen CSS-Stile werden gebündelt und als statisches Objekt in `{PROJECT NAME}.styles.css` erstellt, wobei der Platzhalter `{PROJECT NAME}` das referenzierte Paket oder der Produktname ist.</span><span class="sxs-lookup"><span data-stu-id="70ed6-118">These rewritten CSS styles are bundled and produced as a static asset at `{PROJECT NAME}.styles.css`, where the placeholder `{PROJECT NAME}` is the referenced package or product name.</span></span>

<span data-ttu-id="70ed6-119">Auf diese statischen Dateien wird standardmäßig aus dem Stammpfad der App verwiesen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-119">These static files are referenced from the root path of the app by default.</span></span> <span data-ttu-id="70ed6-120">Die Referenzierung auf die gebündelte Datei in der App erfolgt durch Überprüfen des Verweises im `<head>`-Tag des generierten HTML-Codes:</span><span class="sxs-lookup"><span data-stu-id="70ed6-120">In the app, reference the bundled file by inspecting the reference inside the `<head>` tag of the generated HTML:</span></span>

```html
<link href="MyProjectName.styles.css" rel="stylesheet">
```

<span data-ttu-id="70ed6-121">Innerhalb der gebündelten Datei ist jede Komponente einem Bereichsbezeichner zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="70ed6-121">Within the bundled file, each component is associated with a scope identifier.</span></span> <span data-ttu-id="70ed6-122">Für jede formatierte Komponente wird ein HTML-Attribut im Format `b-<10-character-string>` angehängt.</span><span class="sxs-lookup"><span data-stu-id="70ed6-122">For each styled component, an HTML attribute is appended with the format `b-<10-character-string>`.</span></span> <span data-ttu-id="70ed6-123">Der Bezeichner ist für jede App eindeutig.</span><span class="sxs-lookup"><span data-stu-id="70ed6-123">The identifier is unique and different for each app.</span></span> <span data-ttu-id="70ed6-124">In der gerenderten `Counter`-Komponente fügt Blazor einen Bereichsbezeichner an das `h1`-Element an:</span><span class="sxs-lookup"><span data-stu-id="70ed6-124">In the rendered `Counter` component, Blazor appends a scope identifier to the `h1` element:</span></span>

```html
<h1 b-3xxtam6d07>
```

<span data-ttu-id="70ed6-125">Die Datei `MyProjectName.styles.css` verwendet den Bereichsbezeichner, um eine Stildeklaration mit Ihrer Komponente zu gruppieren.</span><span class="sxs-lookup"><span data-stu-id="70ed6-125">The `MyProjectName.styles.css` file uses the scope identifier to group a style declaration with its component.</span></span> <span data-ttu-id="70ed6-126">Im folgenden Beispiel wird der Stil für das vorherige `<h1>`-Element bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="70ed6-126">The following example provides the style for the preceding `<h1>` element:</span></span>

```css
/* /Pages/Counter.razor.rz.scp.css */
h1[b-3xxtam6d07] {
    color: brown;
}
```

<span data-ttu-id="70ed6-127">Zum Zeitpunkt der Erstellung wird ein Projektbündel mit der Konvention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css` erstellt, wobei der Platzhalter `{STATIC WEB ASSETS BASE PATH}` der statische Basispfad für Webressourcen ist.</span><span class="sxs-lookup"><span data-stu-id="70ed6-127">At build time, a project bundle is created with the convention `{STATIC WEB ASSETS BASE PATH}/MyProject.lib.scp.css`, where the placeholder `{STATIC WEB ASSETS BASE PATH}` is the static web assets base path.</span></span>

<span data-ttu-id="70ed6-128">Wenn andere Projekte verwendet werden, z. B. NuGet-Pakete oder [Razor-Klassenbibliotheken](xref:blazor/components/class-libraries), gilt für die gebündelte Datei Folgendes:</span><span class="sxs-lookup"><span data-stu-id="70ed6-128">If other projects are utilized, such as NuGet packages or [Razor class libraries](xref:blazor/components/class-libraries), the bundled file:</span></span>

* <span data-ttu-id="70ed6-129">Verweise auf Stile erfolgen mithilfe von CSS-Importen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-129">References the styles using CSS imports.</span></span>
* <span data-ttu-id="70ed6-130">Sie wird nicht als statische Webressource der App veröffentlicht, die die Stile verwendet.</span><span class="sxs-lookup"><span data-stu-id="70ed6-130">Isn't published as a static web asset of the app that consumes the styles.</span></span>

## <a name="child-component-support"></a><span data-ttu-id="70ed6-131">Unterstützung für untergeordnete Komponenten</span><span class="sxs-lookup"><span data-stu-id="70ed6-131">Child component support</span></span>

<span data-ttu-id="70ed6-132">Standardmäßig gilt die CSS-Isolation nur für die Komponente, die Sie dem Format `{COMPONENT NAME}.razor.css` zuordnen, wobei der Platzhalter `{COMPONENT NAME}` in der Regel der Komponentenname ist.</span><span class="sxs-lookup"><span data-stu-id="70ed6-132">By default, CSS isolation only applies to the component you associate with the format `{COMPONENT NAME}.razor.css`, where the placeholder `{COMPONENT NAME}` is usually the component name.</span></span> <span data-ttu-id="70ed6-133">Verwenden Sie zum Anwenden von Änderungen auf eine untergeordnete Komponente den `::deep`-Kombinator für alle Nachfolgerelemente in der Datei `.razor.css` der übergeordneten Komponente.</span><span class="sxs-lookup"><span data-stu-id="70ed6-133">To apply changes to a child component, use the `::deep` combinator to any descendant elements in the parent component's `.razor.css` file.</span></span> <span data-ttu-id="70ed6-134">Der `::deep`-Kombinator wählt Elemente aus, die *Nachfolger* des generierten Bereichsbezeichners eines Elements sind.</span><span class="sxs-lookup"><span data-stu-id="70ed6-134">The `::deep` combinator selects elements that are *descendants* of an element's generated scope identifier.</span></span> 

<span data-ttu-id="70ed6-135">Das folgende Beispiel zeigt die übergeordnete Komponente `Parent`, die eine untergeordnete Komponente namens `Child` hat.</span><span class="sxs-lookup"><span data-stu-id="70ed6-135">The following example shows a parent component called `Parent` with a child component called `Child`.</span></span>

<span data-ttu-id="70ed6-136">`Parent.razor`:</span><span class="sxs-lookup"><span data-stu-id="70ed6-136">`Parent.razor`:</span></span>

```razor
@page "/parent"

<div>
    <h1>Parent component</h1>

    <Child />
</div>
```

<span data-ttu-id="70ed6-137">`Child.razor`:</span><span class="sxs-lookup"><span data-stu-id="70ed6-137">`Child.razor`:</span></span>

```razor
<h1>Child Component</h1>
```

<span data-ttu-id="70ed6-138">Aktualisieren Sie die `h1`-Deklaration in `Parent.razor.css` mit dem `::deep`-Kombinator, um anzugeben, dass die Stildeklaration `h1` auf die übergeordnete Komponente und deren untergeordnete Komponenten angewandt werden muss:</span><span class="sxs-lookup"><span data-stu-id="70ed6-138">Update the `h1` declaration in `Parent.razor.css` with the `::deep` combinator to signify the `h1` style declaration must apply to the parent component and its children:</span></span>

```css
::deep h1 { 
    color: red;
}
```

<span data-ttu-id="70ed6-139">Der `h1`-Stil gilt jetzt für die Komponenten `Parent` und `Child`, ohne dass eine separate CSS-Datei für die untergeordnete Komponente erstellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="70ed6-139">The `h1` style now applies to the `Parent` and `Child` components without the need to create a separate scoped CSS file for the child component.</span></span>

> [!NOTE]
> <span data-ttu-id="70ed6-140">Der `::deep`-Kombinator funktioniert nur mit Nachfolgerelementen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-140">The `::deep` combinator only works with descendant elements.</span></span> <span data-ttu-id="70ed6-141">Die folgende HTML-Struktur wendet die `h1`-Stile wie erwartet auf Komponenten an:</span><span class="sxs-lookup"><span data-stu-id="70ed6-141">The following HTML structure applies the `h1` styles to components as expected:</span></span>
> 
> ```razor
> <div>
>     <h1>Parent</h1>
>
>     <Child />
> </div>
> ```
>
> <span data-ttu-id="70ed6-142">In diesem Szenario wendet ASP.NET Core den Bereichsbezeichner der übergeordneten Komponente auf das `div`-Element an, damit der Browser weiß, dass Stile von der übergeordneten Komponente geerbt werden.</span><span class="sxs-lookup"><span data-stu-id="70ed6-142">In this scenario, ASP.NET Core applies the parent component's scope identifier to the `div` element, so the browser knows to inherit styles from the parent component.</span></span>
>
> <span data-ttu-id="70ed6-143">Wenn Sie jedoch das `div`-Element ausschließen, wird die Nachfolgerbeziehung aufgehoben, und der Stil wird **nicht** auf die untergeordnete Komponente angewandt:</span><span class="sxs-lookup"><span data-stu-id="70ed6-143">However, excluding the `div` element removes the descendant relationship, and the style is **not** applied to the child component:</span></span>
>
> ```razor
> <h1>Parent</h1>
>
> <Child />
> ```

## <a name="css-preprocessor-support"></a><span data-ttu-id="70ed6-144">Unterstützung für CSS-Präprozessoren</span><span class="sxs-lookup"><span data-stu-id="70ed6-144">CSS preprocessor support</span></span>

<span data-ttu-id="70ed6-145">CSS-Präprozessoren tragen zur Verbesserung der CSS-Entwicklung bei, indem sie Features wie Variablen, Schachtelung, Module, Mischung und Vererbung nutzen.</span><span class="sxs-lookup"><span data-stu-id="70ed6-145">CSS preprocessors are useful for improving CSS development by utilizing features such as variables, nesting, modules, mixins, and inheritance.</span></span> <span data-ttu-id="70ed6-146">CSS-Isolation unterstützt CSS-Präprozessoren wie Sass oder Less zwar nicht nativ, dennoch können CSS-Präprozessoren nahtlos integriert werden, sofern die Kompilierung des Präprozessors erfolgt, bevor Blazor die CSS-Selektoren während des Buildprozesses umschreibt.</span><span class="sxs-lookup"><span data-stu-id="70ed6-146">While CSS isolation doesn't natively support CSS preprocessors such as Sass or Less, integrating CSS preprocessors is seamless as long as preprocessor compilation occurs before Blazor rewrites the CSS selectors during the build process.</span></span> <span data-ttu-id="70ed6-147">Konfigurieren Sie z. B. mithilfe von Visual Studio die vorhandene Präprozessorkompilierung als Aufgabe **Vor Build** im Aufgabenausführungs-Explorer von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="70ed6-147">Using Visual Studio for example, configure existing preprocessor compilation as a **Before Build** task in the Visual Studio Task Runner Explorer.</span></span>

<span data-ttu-id="70ed6-148">Viele NuGet-Pakete von Drittanbietern (z. B. [Delegat.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder)) können die SASS-/SCSS-Dateien am Anfang des Buildprozesses – noch vor der CSS-Isolation – kompilieren, ohne dass eine Konfiguration erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="70ed6-148">Many third-party NuGet packages, such as [Delegate.SassBuilder](https://www.nuget.org/packages/Delegate.SassBuilder), can compile SASS/SCSS files at the beginning of the build process before CSS isolation occurs, and no additional configuration is required.</span></span>

## <a name="css-isolation-configuration"></a><span data-ttu-id="70ed6-149">Konfiguration der CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="70ed6-149">CSS isolation configuration</span></span>

<span data-ttu-id="70ed6-150">Die CSS-Isolation ist so konzipiert, dass sie sofort einsatzbereit ist. Es ist aber eine Konfiguration für einige erweiterte Szenarien möglich, wenn z. B. Abhängigkeiten von vorhandenen Tools oder Workflows vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="70ed6-150">CSS isolation is designed to work out-of-the-box but provides configuration for some advanced scenarios, such as when there are dependencies on existing tools or workflows.</span></span>

### <a name="customize-scope-identifier-format"></a><span data-ttu-id="70ed6-151">Anpassen des Formats von Bereichsbezeichnern</span><span class="sxs-lookup"><span data-stu-id="70ed6-151">Customize scope identifier format</span></span>

<span data-ttu-id="70ed6-152">Standardmäßig wird für Bereichsbezeichner das Format `b-<10-character-string>` verwendet.</span><span class="sxs-lookup"><span data-stu-id="70ed6-152">By default, scope identifiers use the format `b-<10-character-string>`.</span></span> <span data-ttu-id="70ed6-153">Um das Format der Bereichsbezeichner anzupassen, aktualisieren Sie die Projektdatei mit dem gewünschten Muster:</span><span class="sxs-lookup"><span data-stu-id="70ed6-153">To customize the scope identifier format, update the project file to a desired pattern:</span></span>

```xml
<ItemGroup>
    <None Update="MyComponent.razor.css" CssScope="my-custom-scope-identifier" />
</ItemGroup>
```

<span data-ttu-id="70ed6-154">Im vorherigen Beispiel ändert der für `MyComponent.Razor.css` generierte CSS-Code seinen Bereichsbezeichner von `b-<10-character-string>` in `my-custom-scope-identifier`.</span><span class="sxs-lookup"><span data-stu-id="70ed6-154">In the preceding example, the CSS generated for `MyComponent.Razor.css` changes its scope identifier from `b-<10-character-string>` to `my-custom-scope-identifier`.</span></span>

### <a name="change-base-path-for-static-web-assets"></a><span data-ttu-id="70ed6-155">Ändern des Basispfads für statische Webressourcen</span><span class="sxs-lookup"><span data-stu-id="70ed6-155">Change base path for static web assets</span></span>

<span data-ttu-id="70ed6-156">Die Datei `scoped.styles.css` wird im Stammverzeichnis der App generiert.</span><span class="sxs-lookup"><span data-stu-id="70ed6-156">The `scoped.styles.css` file is generated at the root of the app.</span></span> <span data-ttu-id="70ed6-157">Verwenden Sie in der Projektdatei die `StaticWebAssetBasePath`-Eigenschaft, um den Standardpfad zu ändern.</span><span class="sxs-lookup"><span data-stu-id="70ed6-157">In the project file, use the `StaticWebAssetBasePath` property to change the default path.</span></span> <span data-ttu-id="70ed6-158">Im folgenden Beispiel werden die Datei `scoped.styles.css` und die restlichen Ressourcen der App unter dem Pfad `_content` gespeichert:</span><span class="sxs-lookup"><span data-stu-id="70ed6-158">The following example places the `scoped.styles.css` file, and the rest of the app's assets, at the `_content` path:</span></span>

```xml
<PropertyGroup>
  <StaticWebAssetBasePath>_content/$(PackageId)</StaticWebAssetBasePath>
</PropertyGroup>
```

### <a name="disable-automatic-bundling"></a><span data-ttu-id="70ed6-159">Deaktivieren der automatischen Bündelung</span><span class="sxs-lookup"><span data-stu-id="70ed6-159">Disable automatic bundling</span></span>

<span data-ttu-id="70ed6-160">Wenn Sie das Verhalten von Blazor beim Veröffentlichen und Laden bereichsbezogener Dateien zur Laufzeit ändern möchten, verwenden Sie die `DisableScopedCssBundling`-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="70ed6-160">To opt out of how Blazor publishes and loads scoped files at runtime, use the `DisableScopedCssBundling` property.</span></span> <span data-ttu-id="70ed6-161">Die Verwendung dieser Eigenschaft bedeutet, dass die isolierten CSS-Dateien durch andere Tools oder Prozesse aus dem Verzeichnis `obj` übernommen und zur Laufzeit veröffentlicht und geladen werden:</span><span class="sxs-lookup"><span data-stu-id="70ed6-161">When using this property, it means other tools or processes are responsible for taking the isolated CSS files from the `obj` directory and publishing and loading them at runtime:</span></span>

```xml
<PropertyGroup>
  <DisableScopedCssBundling>true</DisableScopedCssBundling>
</PropertyGroup>
```

## <a name="no-locrazor-class-library-rcl-support"></a><span data-ttu-id="70ed6-162">Unterstützung für Razor-Klassenbibliothek (RCL)</span><span class="sxs-lookup"><span data-stu-id="70ed6-162">Razor class library (RCL) support</span></span>

<span data-ttu-id="70ed6-163">Wenn eine [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) isolierte Stile bereitstellt, zeigt das `href`-Attribut des `<link>`-Tags auf `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, wobei die Platzhalter für Folgendes stehen:</span><span class="sxs-lookup"><span data-stu-id="70ed6-163">When a [Razor class library (RCL)](xref:razor-pages/ui-class) provides isolated styles, the `<link>` tag's `href` attribute points to `{STATIC WEB ASSET BASE PATH}/{ASSEMBLY NAME}.bundle.scp.css`, where the placeholders are:</span></span>

* <span data-ttu-id="70ed6-164">`{STATIC WEB ASSET BASE PATH}`: Basispfad der statischen Webressource</span><span class="sxs-lookup"><span data-stu-id="70ed6-164">`{STATIC WEB ASSET BASE PATH}`: The static web asset base path.</span></span>
* <span data-ttu-id="70ed6-165">`{ASSEMBLY NAME}`: Assemblyname der Klassenbibliothek</span><span class="sxs-lookup"><span data-stu-id="70ed6-165">`{ASSEMBLY NAME}`: The class library's assembly name.</span></span>

<span data-ttu-id="70ed6-166">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="70ed6-166">In the following example:</span></span>

* <span data-ttu-id="70ed6-167">Der Basispfad der statischen Webressource lautet `_content/ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="70ed6-167">The static web asset base path is `_content/ClassLib`.</span></span>
* <span data-ttu-id="70ed6-168">Der Assemblyname der Klassenbibliothek lautet `ClassLib`.</span><span class="sxs-lookup"><span data-stu-id="70ed6-168">The class library's assembly name is `ClassLib`.</span></span>

```html
<link href="_content/ClassLib/ClassLib.bundle.scp.css" rel="stylesheet">
```

<span data-ttu-id="70ed6-169">Weitere Informationen zu RCLs und Komponentenbibliotheken finden Sie unter folgendem Link:</span><span class="sxs-lookup"><span data-stu-id="70ed6-169">For more information on RCLs and component libraries, see:</span></span>

* <xref:razor-pages/ui-class>
* <span data-ttu-id="70ed6-170"><xref:blazor/components/class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="70ed6-170"><xref:blazor/components/class-libraries>.</span></span>
