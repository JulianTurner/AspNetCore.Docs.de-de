---
title: Vorabrendern und Integrieren von ASP.NET Core Razor-Komponenten
author: guardrex
description: Informieren Sie sich über Razor-Komponentenintegrationsszenarien für Blazor-Apps, einschließlich des Vorabrenderns von Razor-Komponenten auf dem Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506980"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a><span data-ttu-id="d3630-103">Vorabrendern und Integrieren von ASP.NET Core Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="d3630-103">Prerender and integrate ASP.NET Core Razor components</span></span>

<span data-ttu-id="d3630-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="d3630-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d3630-105">Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden, die in einer Blazor WebAssembly-Lösung gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-105">Razor components can be integrated into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution.</span></span> <span data-ttu-id="d3630-106">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

## <a name="configuration"></a><span data-ttu-id="d3630-107">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d3630-107">Configuration</span></span>

<span data-ttu-id="d3630-108">So richten Sie das Vorabrendern für eine Blazor WebAssembly-App ein</span><span class="sxs-lookup"><span data-stu-id="d3630-108">To set up prerendering for a Blazor WebAssembly app:</span></span>

1. <span data-ttu-id="d3630-109">Hosten Sie die Blazor WebAssembly-App in einer ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="d3630-109">Host the Blazor WebAssembly app in an ASP.NET Core app.</span></span> <span data-ttu-id="d3630-110">Eine eigenständige Blazor WebAssembly-App kann einer ASP.NET Core-Lösung hinzugefügt werden, oder Sie können eine gehostete Blazor WebAssembly-App verwenden, die anhand der Blazor-Projektvorlage „Hosted“ erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="d3630-110">A standalone Blazor WebAssembly app can be added to an ASP.NET Core solution, or you can use a hosted Blazor WebAssembly app created from the Blazor Hosted project template.</span></span>

1. <span data-ttu-id="d3630-111">Entfernen Sie die standardmäßige statische Datei `wwwroot/index.html` aus dem Blazor WebAssembly-Clientprojekt.</span><span class="sxs-lookup"><span data-stu-id="d3630-111">Remove the default static `wwwroot/index.html` file from the Blazor WebAssembly client project.</span></span>

1. <span data-ttu-id="d3630-112">Löschen Sie im Clientprojekt die folgende Zeile in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="d3630-112">Delete the following line in `Program.Main` in the client project:</span></span>

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. <span data-ttu-id="d3630-113">Fügen Sie dem Serverprojekt die Datei `Pages/_Host.cshtml` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-113">Add a `Pages/_Host.cshtml` file to the server project.</span></span> <span data-ttu-id="d3630-114">Sie können die Datei `_Host.cshtml` aus einer App abrufen, die anhand der Vorlage Blazor Server mit dem Befehl `dotnet new blazorserver -o BlazorServer` in einer Befehlsshell erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="d3630-114">You can obtain a `_Host.cshtml` file from an app created from the Blazor Server template with the `dotnet new blazorserver -o BlazorServer` command in a command shell.</span></span> <span data-ttu-id="d3630-115">Nachdem Sie die Datei `Pages/_Host.cshtml` in der Server-App der gehosteten Blazor WebAssembly-Lösung abgelegt haben, nehmen Sie die folgenden Änderungen an der Datei vor:</span><span class="sxs-lookup"><span data-stu-id="d3630-115">After placing the `Pages/_Host.cshtml` file into the server app of the hosted Blazor WebAssembly solution, make the following changes to the file:</span></span>

   * <span data-ttu-id="d3630-116">Legen Sie den Namespace auf den Ordner `Pages` der Server-App fest (z. B. `@namespace BlazorHosted.Server.Pages`).</span><span class="sxs-lookup"><span data-stu-id="d3630-116">Set the namespace to the server app's `Pages` folder (for example, `@namespace BlazorHosted.Server.Pages`).</span></span>
   * <span data-ttu-id="d3630-117">Legen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung für das Clientprojekt fest (z. B. `@using BlazorHosted.Client`).</span><span class="sxs-lookup"><span data-stu-id="d3630-117">Set an [`@using`](xref:mvc/views/razor#using) directive for the client project (for example, `@using BlazorHosted.Client`).</span></span>
   * <span data-ttu-id="d3630-118">Aktualisieren Sie die Stylesheetlinks so, dass sie auf das Stylesheet der WebAssembly-App zeigen.</span><span class="sxs-lookup"><span data-stu-id="d3630-118">Update the stylesheet links to point to the WebAssembly app's stylesheet.</span></span> <span data-ttu-id="d3630-119">Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="d3630-119">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * <span data-ttu-id="d3630-120">Aktualisieren Sie den `render-mode` des [Hilfsprogramm für Komponententags](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so, dass die Stammkomponente `App` gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="d3630-120">Update the `render-mode` of the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) to prerender the root `App` component:</span></span>

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * <span data-ttu-id="d3630-121">Aktualisieren Sie die Quelle des Blazor-Skripts so, dass das clientseitige Blazor WebAssembly-Skript verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="d3630-121">Update the Blazor script source to use the client-side Blazor WebAssembly script:</span></span>

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. <span data-ttu-id="d3630-122">In `Startup.Configure` (`Startup.cs`) des Serverprojekts:</span><span class="sxs-lookup"><span data-stu-id="d3630-122">In `Startup.Configure` (`Startup.cs`) of the server project:</span></span>

   * <span data-ttu-id="d3630-123">Rufen Sie `UseDeveloperExceptionPage` für den App-Generator in der Entwicklungsumgebung auf.</span><span class="sxs-lookup"><span data-stu-id="d3630-123">Call `UseDeveloperExceptionPage` on the app builder in the Development environment.</span></span>
   * <span data-ttu-id="d3630-124">Rufen Sie `UseBlazorFrameworkFiles` für den App-Generator auf.</span><span class="sxs-lookup"><span data-stu-id="d3630-124">Call `UseBlazorFrameworkFiles` on the app builder.</span></span>
   * <span data-ttu-id="d3630-125">Ändern Sie das Fallback von der Seite `index.html` (`endpoints.MapFallbackToFile("index.html");`) in die Seite `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="d3630-125">Change the fallback from the `index.html` page (`endpoints.MapFallbackToFile("index.html");`) to the `_Host.cshtml` page.</span></span>

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a><span data-ttu-id="d3630-126">Rendern von Komponenten auf einer Seite oder in einer Ansicht mit dem Hilfsprogramm für Komponententags</span><span class="sxs-lookup"><span data-stu-id="d3630-126">Render components in a page or view with the Component Tag Helper</span></span>

<span data-ttu-id="d3630-127">Das Hilfsprogramm für Komponententags unterstützt zwei Rendermodi zum Rendern einer Komponente aus einer Blazor WebAssembly-App auf einer Seite oder in einer Ansicht:</span><span class="sxs-lookup"><span data-stu-id="d3630-127">The Component Tag Helper supports two render modes for rendering a component from a Blazor WebAssembly app in a page or view:</span></span>

* <span data-ttu-id="d3630-128">`WebAssembly`: Rendert einen Marker für eine Blazor WebAssembly-App zur Einbindung einer interaktiven Komponente, wenn diese im Browser geladen wird.</span><span class="sxs-lookup"><span data-stu-id="d3630-128">`WebAssembly`: Renders a marker for a Blazor WebAssembly app for use to include an interactive component when loaded in the browser.</span></span> <span data-ttu-id="d3630-129">Die Komponente wird nicht vorab gerendert.</span><span class="sxs-lookup"><span data-stu-id="d3630-129">The component isn't prerendered.</span></span> <span data-ttu-id="d3630-130">Diese Option erleichtert das Rendern verschiedener Blazor WebAssembly-Komponenten auf verschiedenen Seiten.</span><span class="sxs-lookup"><span data-stu-id="d3630-130">This option makes it easier to render different Blazor WebAssembly components on different pages.</span></span>
* <span data-ttu-id="d3630-131">`WebAssemblyPrerendered`: Rendert die Komponente vorab in statischen HTML-Code und enthält einen Marker für eine Blazor WebAssembly-App zur späteren Verwendung, um die Komponente nach Laden in den Browser interaktiv zu gestalten.</span><span class="sxs-lookup"><span data-stu-id="d3630-131">`WebAssemblyPrerendered`: Prerenders the component into static HTML and includes a marker for a Blazor WebAssembly app for later use to make the component interactive when loaded in the browser.</span></span>

<span data-ttu-id="d3630-132">Im folgenden Razor Pages-Beispiel wird die Komponente `Counter` auf einer Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="d3630-132">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="d3630-133">Um die Komponente interaktiv zu gestalten, ist das Blazor WebAssembly-Skript im [Renderabschnitt](xref:mvc/views/layout#sections) der Seite enthalten.</span><span class="sxs-lookup"><span data-stu-id="d3630-133">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections).</span></span> <span data-ttu-id="d3630-134">Um die Verwendung des vollständigen Namespaces für die Komponente `Counter` mit dem Hilfsprogramm für Komponententags (`{APP ASSEMBLY}.Pages.Counter`) zu vermeiden, fügen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung dem Namespace `Pages` der Client-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-134">To avoid using the full namespace for the `Counter` component with the Component Tag Helper (`{APP ASSEMBLY}.Pages.Counter`), add an [`@using`](xref:mvc/views/razor#using) directive for the client app's `Pages` namespace.</span></span> <span data-ttu-id="d3630-135">Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="d3630-135">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="d3630-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:</span><span class="sxs-lookup"><span data-stu-id="d3630-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the component:</span></span>

* <span data-ttu-id="d3630-137">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="d3630-137">Is prerendered into the page.</span></span>
* <span data-ttu-id="d3630-138">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="d3630-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

<span data-ttu-id="d3630-139">Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d3630-139">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

<span data-ttu-id="d3630-140">Das vorherige Beispiel erfordert, dass das Layout (`_Layout.cshtml`) der Server-App innerhalb des schließenden `</body>`-Tags einen [Renderabschnitt](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) für das Skript enthält:</span><span class="sxs-lookup"><span data-stu-id="d3630-140">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="d3630-141">Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.</span><span class="sxs-lookup"><span data-stu-id="d3630-141">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="d3630-142">Wenn die App auch Komponenten mit den Stilen in der Blazor WebAssembly-App formatieren soll, binden Sie die Stile der Anwendung in die Datei `_Layout.cshtml` ein.</span><span class="sxs-lookup"><span data-stu-id="d3630-142">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="d3630-143">Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="d3630-143">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a><span data-ttu-id="d3630-144">Rendern von Komponenten auf einer Seite oder in einer Ansicht mit dem CSS-Selektor</span><span class="sxs-lookup"><span data-stu-id="d3630-144">Render components in a page or view with a CSS selector</span></span>

<span data-ttu-id="d3630-145">Fügen Sie dem *Client*-Projekt in `Program.Main` (`Program.cs`) Stammkomponenten hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-145">Add root components to the *Client* project in `Program.Main` (`Program.cs`).</span></span> <span data-ttu-id="d3630-146">Im folgenden Beispiel wird die Komponente `Counter` als Stammkomponente mit einem CSS-Selektor deklariert, der das Element mit der `id` auswählt, die mit `my-counter` übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="d3630-146">In the following example, the `Counter` component is declared as a root component with a CSS selector that selects the element with the `id` that matches `my-counter`.</span></span> <span data-ttu-id="d3630-147">Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="d3630-147">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

<span data-ttu-id="d3630-148">Im folgenden Razor Pages-Beispiel wird die Komponente `Counter` auf einer Seite gerendert.</span><span class="sxs-lookup"><span data-stu-id="d3630-148">In the following Razor Pages example, the `Counter` component is rendered in a page.</span></span> <span data-ttu-id="d3630-149">Um die Komponente interaktiv zu gestalten, ist das Blazor WebAssembly-Skript im [Renderabschnitt](xref:mvc/views/layout#sections) der Seite enthalten:</span><span class="sxs-lookup"><span data-stu-id="d3630-149">To make the component interactive, the Blazor WebAssembly script is included in the page's [render section](xref:mvc/views/layout#sections):</span></span>

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<span data-ttu-id="d3630-150">Das vorherige Beispiel erfordert, dass das Layout (`_Layout.cshtml`) der Server-App innerhalb des schließenden `</body>`-Tags einen [Renderabschnitt](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) für das Skript enthält:</span><span class="sxs-lookup"><span data-stu-id="d3630-150">The preceding example requires that the server app's layout (`_Layout.cshtml`) include a [render section](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) for the script inside the closing `</body>` tag:</span></span>

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

<span data-ttu-id="d3630-151">Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.</span><span class="sxs-lookup"><span data-stu-id="d3630-151">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

<span data-ttu-id="d3630-152">Wenn die App auch Komponenten mit den Stilen in der Blazor WebAssembly-App formatieren soll, binden Sie die Stile der Anwendung in die Datei `_Layout.cshtml` ein.</span><span class="sxs-lookup"><span data-stu-id="d3630-152">If the app should also style components with the styles in the Blazor WebAssembly app, include the app's styles in the `_Layout.cshtml` file.</span></span> <span data-ttu-id="d3630-153">Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:</span><span class="sxs-lookup"><span data-stu-id="d3630-153">In the following example, the client app's namespace is `BlazorHosted.Client`:</span></span>

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="d3630-154">Das Integrieren von Razor-Komponenten in Razor Pages- und MVC-Apps in einer gehosteten Blazor WebAssembly-Lösung wird in ASP.NET Core ab .NET 5 unterstützt.</span><span class="sxs-lookup"><span data-stu-id="d3630-154">Integrating Razor components into Razor Pages and MVC apps in a hosted Blazor WebAssembly solution is supported in ASP.NET Core in .NET 5 or later.</span></span> <span data-ttu-id="d3630-155">Wählen Sie eine .NET 5- oder neuere Version dieses Artikels aus.</span><span class="sxs-lookup"><span data-stu-id="d3630-155">Select a .NET 5 or later version of this article.</span></span>

::: moniker-end

::: zone-end

::: zone pivot="server"

<span data-ttu-id="d3630-156">Razor-Komponenten können in einer Blazor Server-App in Razor Pages- und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-156">Razor components can be integrated into Razor Pages and MVC apps in a Blazor Server app.</span></span> <span data-ttu-id="d3630-157">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-157">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="d3630-158">Nachdem Sie [die App konfiguriert haben](#configuration), befolgen Sie die Anleitungen in den folgenden Abschnitten abhängig von den Anforderungen der App:</span><span class="sxs-lookup"><span data-stu-id="d3630-158">After [configuring the app](#configuration), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="d3630-159">Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="d3630-159">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="d3630-160">Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="d3630-160">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="d3630-161">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="d3630-161">Use routable components in a Razor Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="d3630-162">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="d3630-162">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="d3630-163">[Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="d3630-163">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="d3630-164">Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.</span><span class="sxs-lookup"><span data-stu-id="d3630-164">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="configuration"></a><span data-ttu-id="d3630-165">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="d3630-165">Configuration</span></span>

<span data-ttu-id="d3630-166">Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="d3630-166">An existing Razor Pages or MVC app can integrate Razor components into pages and views:</span></span>

1. <span data-ttu-id="d3630-167">In der Layoutdatei der App (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="d3630-167">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="d3630-168">Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-168">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="d3630-169">Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="d3630-169">The `href` value (the *app base path*) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="d3630-170">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:blazor/host-and-deploy/index#app-base-path>-Artikels.</span><span class="sxs-lookup"><span data-stu-id="d3630-170">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="d3630-171">Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.</span><span class="sxs-lookup"><span data-stu-id="d3630-171">The `_Layout.cshtml` file is located in the `Pages/Shared` folder in a Razor Pages app or `Views/Shared` folder in an MVC app.</span></span>

   * <span data-ttu-id="d3630-172">Fügen Sie ein `<script>`-Tag für das `blazor.server.js`-Skript unmittelbar vor dem Renderabschnitt `Scripts` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-172">Add a `<script>` tag for the `blazor.server.js` script immediately before the `Scripts` render section:</span></span>

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     <span data-ttu-id="d3630-173">Das Framework fügt das Skript `blazor.server.js` zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-173">The framework adds the `blazor.server.js` script to the app.</span></span> <span data-ttu-id="d3630-174">Das Skript muss nicht manuell zur App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-174">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="d3630-175">Fügen Sie im Stammordner des Projekts eine `_Imports.razor`-Datei mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):</span><span class="sxs-lookup"><span data-stu-id="d3630-175">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. <span data-ttu-id="d3630-176">Registrieren Sie in `Startup.ConfigureServices` den Blazor Server-Dienst:</span><span class="sxs-lookup"><span data-stu-id="d3630-176">In `Startup.ConfigureServices`, register the Blazor Server service:</span></span>

   ```csharp
   services.AddServerSideBlazor();
   ```

1. <span data-ttu-id="d3630-177">Fügen Sie in `Startup.Configure` den Blazor Hub-Endpunkt zu `app.UseEndpoints` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-177">In `Startup.Configure`, add the Blazor Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. <span data-ttu-id="d3630-178">Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d3630-178">Integrate components into any page or view.</span></span> <span data-ttu-id="d3630-179">Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="d3630-179">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="d3630-180">Verwenden routingfähiger Komponenten in einer Razor Pages-App</span><span class="sxs-lookup"><span data-stu-id="d3630-180">Use routable components in a Razor Pages app</span></span>

<span data-ttu-id="d3630-181">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="d3630-181">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="d3630-182">So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in Razor Pages-Apps ein:</span><span class="sxs-lookup"><span data-stu-id="d3630-182">To support routable Razor components in Razor Pages apps:</span></span>

1. <span data-ttu-id="d3630-183">Befolgen Sie die Anweisungen im Abschnitt [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="d3630-183">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="d3630-184">Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-184">Add an `App.razor` file to the project root with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="d3630-185">Fügen Sie dem Ordner `Pages` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-185">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="d3630-186">Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="d3630-186">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="d3630-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="d3630-187"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="d3630-188">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="d3630-188">Is prerendered into the page.</span></span>
   * <span data-ttu-id="d3630-189">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="d3630-189">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="d3630-190">Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d3630-190">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="d3630-191">Fügen Sie eine Route mit niedriger Priorität für die Seite `_Host.cshtml` zur Endpunktkonfiguration in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-191">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="d3630-192">Fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-192">Add routable components to the app.</span></span> <span data-ttu-id="d3630-193">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d3630-193">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="d3630-194">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="d3630-194">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="d3630-195">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="d3630-195">Use routable components in an MVC app</span></span>

<span data-ttu-id="d3630-196">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="d3630-196">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="d3630-197">So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in MVC-Apps ein:</span><span class="sxs-lookup"><span data-stu-id="d3630-197">To support routable Razor components in MVC apps:</span></span>

1. <span data-ttu-id="d3630-198">Befolgen Sie die Anweisungen im Abschnitt [Configuration](#configuration).</span><span class="sxs-lookup"><span data-stu-id="d3630-198">Follow the guidance in the [Configuration](#configuration) section.</span></span>

1. <span data-ttu-id="d3630-199">Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-199">Add an `App.razor` file to the root of the project with the following content:</span></span>

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

1. <span data-ttu-id="d3630-200">Fügen Sie dem Ordner `Views/Home` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-200">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="d3630-201">Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="d3630-201">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="d3630-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="d3630-202"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="d3630-203">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="d3630-203">Is prerendered into the page.</span></span>
   * <span data-ttu-id="d3630-204">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="d3630-204">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

   <span data-ttu-id="d3630-205">Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d3630-205">For more information on the Component Tag Helper, including passing parameters and <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configuration, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="d3630-206">Fügen Sie eine Aktion zum Home-Controller hinzu:</span><span class="sxs-lookup"><span data-stu-id="d3630-206">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="d3630-207">Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht `_Host.cshtml` an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="d3630-207">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. <span data-ttu-id="d3630-208">Erstellen Sie einen `Pages`-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-208">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="d3630-209">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d3630-209">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="d3630-210">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="d3630-210">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="d3630-211">Rendern von Komponenten einer Seite oder Ansicht</span><span class="sxs-lookup"><span data-stu-id="d3630-211">Render components from a page or view</span></span>

<span data-ttu-id="d3630-212">*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="d3630-212">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="d3630-213">Verwenden Sie das [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) zum Rendern einer Komponente einer Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d3630-213">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="d3630-214">Rendern zustandsbehafteter interaktiver Komponenten</span><span class="sxs-lookup"><span data-stu-id="d3630-214">Render stateful interactive components</span></span>

<span data-ttu-id="d3630-215">Zustandsbehaftete interaktive Komponenten können einer Razor-Seite oder -Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="d3630-215">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="d3630-216">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="d3630-216">When the page or view renders:</span></span>

* <span data-ttu-id="d3630-217">Die Komponente wird mit der Seite oder Ansicht vorab gerendert.</span><span class="sxs-lookup"><span data-stu-id="d3630-217">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="d3630-218">Der anfängliche Komponentenzustand, der zum Rendern vorab genutzt wurde, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="d3630-218">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="d3630-219">Der neue Komponentenzustand wird erstellt, wenn die SignalR-Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="d3630-219">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="d3630-220">Die folgende Razor-Seite rendert eine `Counter`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="d3630-220">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d3630-221">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d3630-221">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="d3630-222">Rendern nicht interaktiver Komponenten</span><span class="sxs-lookup"><span data-stu-id="d3630-222">Render noninteractive components</span></span>

<span data-ttu-id="d3630-223">Auf der folgenden Razor-Seite wird die `Counter`-Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="d3630-223">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="d3630-224">Da die Komponente statisch gerendert wird, ist die Komponente nicht interaktiv:</span><span class="sxs-lookup"><span data-stu-id="d3630-224">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="d3630-225">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="d3630-225">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="d3630-226">Komponentennamespaces</span><span class="sxs-lookup"><span data-stu-id="d3630-226">Component namespaces</span></span>

<span data-ttu-id="d3630-227">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace des Ordners zur Seite/Ansicht oder zur Datei `_ViewImports.cshtml` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d3630-227">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="d3630-228">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d3630-228">In the following example:</span></span>

* <span data-ttu-id="d3630-229">Ändern Sie `MyAppNamespace` in den Namespace der App.</span><span class="sxs-lookup"><span data-stu-id="d3630-229">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="d3630-230">Wenn die Komponenten nicht in einem Ordner namens `Components` enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="d3630-230">If a folder named `Components` isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="d3630-231">Die Datei `_ViewImports.cshtml` befindet sich im Ordner `Pages` einer Razor-Pages-App oder im Ordner `Views` einer MVC-App.</span><span class="sxs-lookup"><span data-stu-id="d3630-231">The `_ViewImports.cshtml` file is located in the `Pages` folder of a Razor Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="d3630-232">Weitere Informationen finden Sie unter <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="d3630-232">For more information, see <xref:blazor/components/index#namespaces>.</span></span>

::: zone-end
