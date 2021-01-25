---
title: Klassenbibliotheken für ASP.NET Core-Razor-Komponenten
author: guardrex
description: Erfahren Sie, wie Komponenten aus einer externen Komponentenbibliothek in Blazor-Apps einbezogen werden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/12/2021
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
uid: blazor/components/class-libraries
ms.openlocfilehash: 14370f9bbf45079fd3654d3e55af4178691cf4f5
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252551"
---
# <a name="aspnet-core-no-locrazor-components-class-libraries"></a><span data-ttu-id="fe4bb-103">Klassenbibliotheken für ASP.NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="fe4bb-103">ASP.NET Core Razor components class libraries</span></span>

<span data-ttu-id="fe4bb-104">Von [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="fe4bb-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="fe4bb-105">Komponenten können in einer [Razor-Klassenbibliothek (RCL)](xref:razor-pages/ui-class) projektübergreifend gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-105">Components can be shared in a [Razor class library (RCL)](xref:razor-pages/ui-class) across projects.</span></span> <span data-ttu-id="fe4bb-106">Eine *Klassenbibliothek für Razor-Komponenten* kann wie folgt einbezogen werden:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-106">A *Razor components class library* can be included from:</span></span>

* <span data-ttu-id="fe4bb-107">Über ein anderes Projekt in der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-107">Another project in the solution.</span></span>
* <span data-ttu-id="fe4bb-108">Über ein NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-108">A NuGet package.</span></span>
* <span data-ttu-id="fe4bb-109">Über eine referenzierte .NET-Bibliothek.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-109">A referenced .NET library.</span></span>

<span data-ttu-id="fe4bb-110">Genauso wie Komponenten normale .NET-Typen sind, sind von der RCL bereitgestellte Komponenten normale .NET-Assemblys.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-110">Just as components are regular .NET types, components provided by an RCL are normal .NET assemblies.</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="fe4bb-111">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="fe4bb-111">Create an RCL</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fe4bb-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fe4bb-112">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="fe4bb-113">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-113">Create a new project.</span></span>
1. <span data-ttu-id="fe4bb-114">Klicken Sie auf **Razor-Klassenbibliothek**.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-114">Select **Razor Class Library**.</span></span> <span data-ttu-id="fe4bb-115">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-115">Select **Next**.</span></span>
1. <span data-ttu-id="fe4bb-116">Klicken Sie im Dialogfeld **Neue Razor-Klassenbibliothek erstellen** auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-116">In the **Create a new Razor class library** dialog, select **Create**.</span></span>
1. <span data-ttu-id="fe4bb-117">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-117">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="fe4bb-118">Die Beispiele in diesem Thema verwenden den Projektnamen `ComponentLibrary`.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-118">The examples in this topic use the project name `ComponentLibrary`.</span></span> <span data-ttu-id="fe4bb-119">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-119">Select **Create**.</span></span>
1. <span data-ttu-id="fe4bb-120">Fügen Sie die RCL zu einer Projektmappe hinzu:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-120">Add the RCL to a solution:</span></span>
   1. <span data-ttu-id="fe4bb-121">Klicken Sie mit der rechten Maustaste auf die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-121">Right-click the solution.</span></span> <span data-ttu-id="fe4bb-122">Wählen Sie **Hinzufügen** > **Vorhandenes Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-122">Select **Add** > **Existing Project**.</span></span>
   1. <span data-ttu-id="fe4bb-123">Navigieren Sie zur Projektdatei der RCL.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-123">Navigate to the RCL's project file.</span></span>
   1. <span data-ttu-id="fe4bb-124">Wählen Sie die Projektdatei (`.csproj`) der RCL aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-124">Select the RCL's project file (`.csproj`).</span></span>
1. <span data-ttu-id="fe4bb-125">Fügen Sie in der App einen Verweis auf die RCL hinzu:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-125">Add a reference to the RCL from the app:</span></span>
   1. <span data-ttu-id="fe4bb-126">Klicken Sie mit der rechten Maustaste auf das App-Projekt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-126">Right-click the app project.</span></span> <span data-ttu-id="fe4bb-127">Wählen Sie **Hinzufügen** > **Verweis** aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-127">Select **Add** > **Reference**.</span></span>
   1. <span data-ttu-id="fe4bb-128">Wählen Sie das RCL-Projekt aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-128">Select the RCL project.</span></span> <span data-ttu-id="fe4bb-129">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-129">Select **OK**.</span></span>

> [!NOTE]
> <span data-ttu-id="fe4bb-130">Wenn das Kontrollkästchen **Seiten und Ansichten unterstützen** beim Erstellen der RCL aus der Vorlage aktiviert ist, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-130">If the **Support pages and views** check box is selected when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Components.Web
> ```
>
> <span data-ttu-id="fe4bb-131">Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-131">Manually add the file the root of the generated project.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fe4bb-132">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="fe4bb-132">.NET Core CLI</span></span>](#tab/netcore-cli)

1. <span data-ttu-id="fe4bb-133">Verwenden Sie die Vorlage **Razor-Klassenbibliothek** (`razorclasslib`) mit dem Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-133">Use the **Razor Class Library** template (`razorclasslib`) with the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in a command shell.</span></span> <span data-ttu-id="fe4bb-134">Im folgenden Beispiel wird eine RCL mit dem Namen `ComponentLibrary` erstellt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-134">In the following example, an RCL is created named `ComponentLibrary`.</span></span> <span data-ttu-id="fe4bb-135">Der Ordner, der `ComponentLibrary` enthält, wird automatisch erstellt, wenn der Befehl ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-135">The folder that holds `ComponentLibrary` is created automatically when the command is executed:</span></span>

   ```dotnetcli
   dotnet new razorclasslib -o ComponentLibrary
   ```

   > [!NOTE]
   > <span data-ttu-id="fe4bb-136">Wenn der Switch `-s|--support-pages-and-views` beim Erstellen der RCL aus der Vorlage verwendet wird, fügen Sie auch eine `_Imports.razor`-Datei mit folgendem Inhalt zum Stammverzeichnis des generierten Projekts hinzu, um das Erstellen von Razor-Komponenten zu ermöglichen:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-136">If the `-s|--support-pages-and-views` switch is used when generating the RCL from the template, then also add an `_Imports.razor` file to root of the generated project with the following contents to enable Razor component authoring:</span></span>
   >
   > ```razor
   > @using Microsoft.AspNetCore.Components.Web
   > ```
   >
   > <span data-ttu-id="fe4bb-137">Fügen Sie die Datei manuell dem Stammverzeichnis des generierten Projekts hinzu.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-137">Manually add the file the root of the generated project.</span></span>

1. <span data-ttu-id="fe4bb-138">Verwenden Sie den Befehl [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) in einer Befehlsshell, um die Bibliothek zu einem bestehenden Projekt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-138">To add the library to an existing project, use the [`dotnet add reference`](/dotnet/core/tools/dotnet-add-reference) command in a command shell.</span></span> <span data-ttu-id="fe4bb-139">Im folgenden Beispiel wird die RCL der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-139">In the following example, the RCL is added to the app.</span></span> <span data-ttu-id="fe4bb-140">Führen Sie den folgenden Befehl aus dem Projektordner der App mit dem Pfad zur Bibliothek aus:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-140">Execute the following command from the app's project folder with the path to the library:</span></span>

   ```dotnetcli
   dotnet add reference {PATH TO LIBRARY}
   ```

---

## <a name="consume-a-library-component"></a><span data-ttu-id="fe4bb-141">Verwenden einer Bibliothekskomponente</span><span class="sxs-lookup"><span data-stu-id="fe4bb-141">Consume a library component</span></span>

<span data-ttu-id="fe4bb-142">Um Komponenten, die in einer Bibliothek definiert sind, in einem anderen Projekt zu verwenden, verwenden Sie einen der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-142">In order to consume components defined in a library in another project, use either of the following approaches:</span></span>

* <span data-ttu-id="fe4bb-143">Verwenden Sie den vollständigen Typnamen mit dem Namespace.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-143">Use the full type name with the namespace.</span></span>
* <span data-ttu-id="fe4bb-144">Verwenden Sie die Razor-Anweisung [`@using`](xref:mvc/views/razor#using).</span><span class="sxs-lookup"><span data-stu-id="fe4bb-144">Use Razor's [`@using`](xref:mvc/views/razor#using) directive.</span></span> <span data-ttu-id="fe4bb-145">Einzelne Komponenten können über den Namen hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-145">Individual components can be added by name.</span></span>

<span data-ttu-id="fe4bb-146">In den folgenden Beispielen ist `ComponentLibrary` eine Komponentenbibliothek, die die `Component1`-Komponente (`Component1.razor`) enthält.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-146">In the following examples, `ComponentLibrary` is a component library containing the `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="fe4bb-147">Die `Component1`-Komponente ist eine Beispielkomponente, die bei der Erstellung der Bibliothek automatisch durch die RCL-Projektvorlage hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-147">The `Component1` component is an example component automatically added by the RCL project template when the library is created.</span></span>

<span data-ttu-id="fe4bb-148">Verweisen Sie mit dem zugehörigen Namespace auf die `Component1`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-148">Reference the `Component1` component using its namespace:</span></span>

```razor
<h1>Hello, world!</h1>

Welcome to your new app.

<ComponentLibrary.Component1 />
```

<span data-ttu-id="fe4bb-149">Alternativ können Sie die Bibliothek mit einer [`@using`](xref:mvc/views/razor#using)-Anweisung in den Gültigkeitsbereich einbeziehen und die Komponente ohne ihren Namespace verwenden:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-149">Alternatively, bring the library into scope with an [`@using`](xref:mvc/views/razor#using) directive and use the component without its namespace:</span></span>

```razor
@using ComponentLibrary

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="fe4bb-150">Fügen Sie die `@using ComponentLibrary`-Anweisung optional in die `_Import.razor`-Datei der obersten Ebene ein, um die Komponenten der Bibliothek für ein ganzes Projekt zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-150">Optionally, include the `@using ComponentLibrary` directive in the top-level `_Import.razor` file to make the library's components available to an entire project.</span></span> <span data-ttu-id="fe4bb-151">Fügen Sie die Anweisung auf beliebiger Ebene zu einer `_Import.razor`-Datei hinzu, um den Namespace auf eine einzelne Komponente oder eine Reihe von Komponenten innerhalb eines Ordners anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-151">Add the directive to an `_Import.razor` file at any level to apply the namespace to a single component or set of components within a folder.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="fe4bb-152">Bei Bibliothekskomponenten, die [CSS-Isolation](xref:blazor/components/css-isolation) verwenden, muss keine explizite Verknüpfung zu den Stylesheets der einzelnen Komponenten der Bibliothek in der App hinzugefügt werden, die die Bibliothek verwendet.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-152">For library components that use [CSS isolation](xref:blazor/components/css-isolation), there's no need to explicitly link the library's individual component stylesheets in the app that consumes the library.</span></span> <span data-ttu-id="fe4bb-153">Die Komponentenstile werden für die entsprechende App automatisch verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-153">The component styles are automatically made available to the consuming app.</span></span>

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) in `Component1.razor`:

```razor
<div class="my-component">
    <Link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />

    <p>
        This Blazor component is defined in the <strong>ComponentLibrary</strong> package.
    </p>
</div>
```

NEXT PARAGRAPH: RECAST TO 'CAN ALSO ADOPT ...'

-->

<span data-ttu-id="fe4bb-154">Wenn zusätzliche Stile der Bibliothekskomponenten aus den Stylesheets im `wwwroot`-Ordner der Bibliothek hinzugefügt werden sollen, verknüpfen Sie die Stylesheets in der `wwwroot/index.html`-Datei (Blazor WebAssembly) oder der `Pages/_Host.cshtml`-Datei (Blazor Server) der entsprechenden App:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-154">To provide additional library component styles from stylesheets in the library's `wwwroot` folder, link the stylesheets in the consuming app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/additionalStyles.css" rel="stylesheet" />
</head>
```

<!-- REACTIVATE WHEN HEAD COMPONENTS COME BACK AT 6.0

When the `Link` component is used in a child component, the linked asset is also available to any other child component of the parent component as long as the child with the `Link` component is rendered. The distinction between using the `Link` component in a child component and placing a `<link>` HTML tag in `wwwroot/index.html` or `Pages/_Host.cshtml` is that a framework component's rendered HTML tag:

* Can be modified by application state. A hard-coded `<link>` HTML tag can't be modified by application state.
* Is removed from the HTML `<head>` when the parent component is no longer rendered.

-->

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="fe4bb-155">Um die `my-component`-CSS-Klasse von `Component1`bereitzustellen, stellen Sie eine Verknüpfung mit dem Stylesheet der Bibliothek in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) der App her:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-155">To provide `Component1`'s `my-component` CSS class, link to the library's stylesheet in the app's `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server):</span></span>

```html
<head>
    ...
    <link href="_content/ComponentLibrary/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

## <a name="create-a-no-locrazor-components-class-library-with-static-assets"></a><span data-ttu-id="fe4bb-156">Erstellen einer Klassenbibliothek für Razor-Komponenten mit statischen Objekten</span><span class="sxs-lookup"><span data-stu-id="fe4bb-156">Create a Razor components class library with static assets</span></span>

<span data-ttu-id="fe4bb-157">Eine RCL kann statische Objekte enthalten.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-157">An RCL can include static assets.</span></span> <span data-ttu-id="fe4bb-158">Die statischen Objekte sind für jede App verfügbar, die die Bibliothek nutzt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-158">The static assets are available to any app that consumes the library.</span></span> <span data-ttu-id="fe4bb-159">Weitere Informationen finden Sie unter <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-159">For more information, see <xref:razor-pages/ui-class#create-an-rcl-with-static-assets>.</span></span>

## <a name="supply-components-and-static-assets-to-multiple-hosted-no-locblazor-apps"></a><span data-ttu-id="fe4bb-160">Bereitstellen von Komponenten und statischen Ressourcen für mehrere gehostete Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="fe4bb-160">Supply components and static assets to multiple hosted Blazor apps</span></span>

<span data-ttu-id="fe4bb-161">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-161">For more information, see <xref:blazor/host-and-deploy/webassembly#static-assets-and-class-libraries>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="browser-compatibility-analyzer-for-no-locblazor-webassembly"></a><span data-ttu-id="fe4bb-162">Browserkompatibilitäts-Analysetool für Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="fe4bb-162">Browser compatibility analyzer for Blazor WebAssembly</span></span>

<span data-ttu-id="fe4bb-163">Blazor WebAssembly-Apps zielen auf die vollständige .NET-API-Oberfläche ab, aber aufgrund von Browsersandboxeinschränkungen werden nicht alle .NET-APIs in WebAssembly unterstützt.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-163">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="fe4bb-164">Nicht unterstützte APIs lösen <xref:System.PlatformNotSupportedException> bei der Ausführung in Webassembly aus.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-164">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="fe4bb-165">Ein Plattformkompatibilitäts-Analysetool warnt den Entwickler, wenn die API APIs verwendet, die nicht von den Zielplattformen der App unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-165">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="fe4bb-166">Bei Blazor WebAssembly-Apps bedeutet dies eine Überprüfung, dass die APIs in Browsern unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-166">For Blazor WebAssembly apps, this means checking that APIs are supported in browsers.</span></span> <span data-ttu-id="fe4bb-167">Das Kommentieren von .NET Framework-APIs für das Kompatibilitätsanalysetool Analyzer ist ein fortlaufenden Prozess, sodass nicht alle .NET Framework-APIs derzeit mit Anmerkungen versehen sind.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-167">Annotating .NET framework APIs for the compatibility analyzer is an on-going process, so not all .NET framework API is currently annotated.</span></span>

<span data-ttu-id="fe4bb-168">Blazor WebAssembly- und Razor-Klassenbibliotheksprojekte aktivieren *automatisch* Browserkompatibilitätsprüfungen, indem `browser` als unterstützte Plattform mit dem MSBuild-Element `SupportedPlatform` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-168">Blazor WebAssembly and Razor class library projects *automatically* enable browser compatibilty checks by adding `browser` as a supported platform with the `SupportedPlatform` MSBuild item.</span></span> <span data-ttu-id="fe4bb-169">Bibliotheksentwickler können das `SupportedPlatform`-Element manuell zur Projektdatei einer Bibliothek hinzufügen, um das Feature zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-169">Library developers can manually add the `SupportedPlatform` item to a library's project file to enable the feature:</span></span>

```xml
<ItemGroup>
  <SupportedPlatform Include="browser" />
</ItemGroup>
```

<span data-ttu-id="fe4bb-170">Geben Sie beim Erstellen einer Bibliothek an, dass eine bestimmte API in Browsern nicht unterstützt wird, indem Sie `browser` für <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>angeben:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-170">When authoring a library, indicate that a particular API isn't supported in browsers by specifying `browser` to <xref:System.Runtime.Versioning.UnsupportedOSPlatformAttribute>:</span></span>

```csharp
[UnsupportedOSPlatform("browser")]
private static string GetLoggingDirectory()
{
    ...
}
```

<span data-ttu-id="fe4bb-171">Weitere Informationen finden Sie unter [Kommentieren von APIs als nicht unterstützt auf bestimmten Plattformen (dotnet/designs-GitHub-Repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span><span class="sxs-lookup"><span data-stu-id="fe4bb-171">For more information, see [Annotating APIs as unsupported on specific platforms (dotnet/designs GitHub repository](https://github.com/dotnet/designs/blob/main/accepted/2020/platform-exclusion/platform-exclusion.md#build-configuration-for-platforms).</span></span>

## <a name="no-locblazor-javascript-isolation-and-object-references"></a><span data-ttu-id="fe4bb-172">Blazor-JavaScript-Isolierung und Objektverweise</span><span class="sxs-lookup"><span data-stu-id="fe4bb-172">Blazor JavaScript isolation and object references</span></span>

<span data-ttu-id="fe4bb-173">Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="fe4bb-173">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="fe4bb-174">JavaScript-Isolierung bietet die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-174">JavaScript isolation provides the following benefits:</span></span>

* <span data-ttu-id="fe4bb-175">Importiertes JavaScript verschmutzt nicht mehr den globalen Namespace.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-175">Imported JavaScript no longer pollutes the global namespace.</span></span>
* <span data-ttu-id="fe4bb-176">Consumer der Bibliothek und Komponenten müssen das zugehörige JavaScript nicht manuell importieren.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-176">Consumers of the library and components aren't required to manually import the related JavaScript.</span></span>

<span data-ttu-id="fe4bb-177">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-177">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

::: moniker-end

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="fe4bb-178">Erstellen, Verpacken und Liefern an NuGet</span><span class="sxs-lookup"><span data-stu-id="fe4bb-178">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="fe4bb-179">Da es sich bei Komponentenbibliotheken um standardmäßige .NET-Bibliotheken handelt, unterscheidet sich das Verpacken und Liefern an NuGet nicht vom Verpacken und Liefern einer beliebigen Bibliothek an NuGet.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-179">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="fe4bb-180">Das Verpacken wird mit dem Befehl [`dotnet pack`](/dotnet/core/tools/dotnet-pack) in einer Befehlsshell durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="fe4bb-180">Packaging is performed using the [`dotnet pack`](/dotnet/core/tools/dotnet-pack) command in a command shell:</span></span>

```dotnetcli
dotnet pack
```

<span data-ttu-id="fe4bb-181">Laden Sie das Paket mit dem Befehl [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) in einer Befehlsshell in NuGet hoch.</span><span class="sxs-lookup"><span data-stu-id="fe4bb-181">Upload the package to NuGet using the [`dotnet nuget push`](/dotnet/core/tools/dotnet-nuget-push) command in a command shell.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fe4bb-182">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fe4bb-182">Additional resources</span></span>

::: moniker range=">= aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="fe4bb-183">Hinzufügen eines XML-IL-Trimmer-Konfigurationsdatei (Intermediate Language, Zwischensprache) zu einer Bibliothek</span><span class="sxs-lookup"><span data-stu-id="fe4bb-183">Add an XML Intermediate Language (IL) Trimmer configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-trimmer)
* [<span data-ttu-id="fe4bb-184">CSS-Isolationsunterstützung mit Razor-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="fe4bb-184">CSS isolation support with Razor class libraries</span></span>](xref:blazor/components/css-isolation#razor-class-library-rcl-support)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <xref:razor-pages/ui-class>
* [<span data-ttu-id="fe4bb-185">Hinzufügen eines XML-IL-Linker-Konfigurationsdatei (Intermediate Language, Zwischensprache) zu einer Bibliothek</span><span class="sxs-lookup"><span data-stu-id="fe4bb-185">Add an XML Intermediate Language (IL) Linker configuration file to a library</span></span>](xref:blazor/host-and-deploy/configure-linker#add-an-xml-linker-configuration-file-to-a-library)

::: moniker-end
