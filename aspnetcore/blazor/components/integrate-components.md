---
title: 'Integrieren von ASP.NET Core :::no-loc(Razor):::-Komponenten in :::no-loc(Razor)::: Pages- und MVC-Apps'
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsszenarios für Komponenten und DOM-Elemente in :::no-loc(Blazor):::-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056256"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a><span data-ttu-id="e2869-103">Integrieren von ASP.NET Core :::no-loc(Razor):::-Komponenten in :::no-loc(Razor)::: Pages- und MVC-Apps</span><span class="sxs-lookup"><span data-stu-id="e2869-103">Integrate ASP.NET Core :::no-loc(Razor)::: components into :::no-loc(Razor)::: Pages and MVC apps</span></span>

<span data-ttu-id="e2869-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e2869-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e2869-105">:::no-loc(Razor):::-Komponenten können in :::no-loc(Razor)::: Pages- und MVC-Apps integriert werden.</span><span class="sxs-lookup"><span data-stu-id="e2869-105">:::no-loc(Razor)::: components can be integrated into :::no-loc(Razor)::: Pages and MVC apps.</span></span> <span data-ttu-id="e2869-106">Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="e2869-106">When the page or view is rendered, components can be prerendered at the same time.</span></span>

<span data-ttu-id="e2869-107">Nachdem Sie [die App vorbereitet haben](#prepare-the-app), verwenden Sie die Anleitungen in den folgenden Abschnitten, abhängig von den Anforderungen der App:</span><span class="sxs-lookup"><span data-stu-id="e2869-107">After [preparing the app](#prepare-the-app), use the guidance in the following sections depending on the app's requirements:</span></span>

* <span data-ttu-id="e2869-108">Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="e2869-108">Routable components: For components that are directly routable from user requests.</span></span> <span data-ttu-id="e2869-109">Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e2869-109">Follow this guidance when visitors should be able to make an HTTP request in their browser for a component with an [`@page`](xref:mvc/views/razor#page) directive.</span></span>
  * [<span data-ttu-id="e2869-110">Verwenden routingfähiger Komponenten in einer :::no-loc(Razor)::: Pages-App</span><span class="sxs-lookup"><span data-stu-id="e2869-110">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>](#use-routable-components-in-a-razor-pages-app)
  * [<span data-ttu-id="e2869-111">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="e2869-111">Use routable components in an MVC app</span></span>](#use-routable-components-in-an-mvc-app)
* <span data-ttu-id="e2869-112">[Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind.</span><span class="sxs-lookup"><span data-stu-id="e2869-112">[Render components from a page or view](#render-components-from-a-page-or-view): For components that aren't directly routable from user requests.</span></span> <span data-ttu-id="e2869-113">Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.</span><span class="sxs-lookup"><span data-stu-id="e2869-113">Follow this guidance when the app embeds components into existing pages and views with the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

## <a name="prepare-the-app"></a><span data-ttu-id="e2869-114">Vorbereiten der App</span><span class="sxs-lookup"><span data-stu-id="e2869-114">Prepare the app</span></span>

<span data-ttu-id="e2869-115">Eine vorhandene :::no-loc(Razor)::: Pages- oder MVC-App kann :::no-loc(Razor):::-Komponenten in Seiten und Ansichten integrieren:</span><span class="sxs-lookup"><span data-stu-id="e2869-115">An existing :::no-loc(Razor)::: Pages or MVC app can integrate :::no-loc(Razor)::: components into pages and views:</span></span>

1. <span data-ttu-id="e2869-116">In der Layoutdatei der App (`_Layout.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="e2869-116">In the app's layout file (`_Layout.cshtml`):</span></span>

   * <span data-ttu-id="e2869-117">Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-117">Add the following `<base>` tag to the `<head>` element:</span></span>

     ```html
     <base href="~/" />
     ```

     <span data-ttu-id="e2869-118">Der `href`-Wert (der *App-Basispfad* ) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet.</span><span class="sxs-lookup"><span data-stu-id="e2869-118">The `href` value (the *app base path* ) in the preceding example assumes that the app resides at the root URL path (`/`).</span></span> <span data-ttu-id="e2869-119">Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:blazor/host-and-deploy/index#app-base-path>-Artikels.</span><span class="sxs-lookup"><span data-stu-id="e2869-119">If the app is a sub-application, follow the guidance in the *App base path* section of the <xref:blazor/host-and-deploy/index#app-base-path> article.</span></span>

     <span data-ttu-id="e2869-120">Die Datei `_Layout.cshtml` befindet sich bei :::no-loc(Razor):::-Pages-Apps im Ordner *Pages/Shared* und bei MVC-Apps im Ordner *Views/Shared*.</span><span class="sxs-lookup"><span data-stu-id="e2869-120">The `_Layout.cshtml` file is located in the *Pages/Shared* folder in a :::no-loc(Razor)::: Pages app or *Views/Shared* folder in an MVC app.</span></span>

   * <span data-ttu-id="e2869-121">Fügen Sie direkt vor dem schließenden `</body>`-Tag ein `<script>`-Tag für das Skript *blazor.server.js* ein:</span><span class="sxs-lookup"><span data-stu-id="e2869-121">Add a `<script>` tag for the *blazor.server.js* script immediately before the closing `</body>` tag:</span></span>

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     <span data-ttu-id="e2869-122">Das Framework fügt das Skript *blazor.server.js* zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-122">The framework adds the *blazor.server.js* script to the app.</span></span> <span data-ttu-id="e2869-123">Das Skript muss nicht manuell zur App hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e2869-123">There's no need to manually add the script to the app.</span></span>

1. <span data-ttu-id="e2869-124">Fügen Sie im Stammordner des Projekts eine `_Imports.razor`-Datei mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):</span><span class="sxs-lookup"><span data-stu-id="e2869-124">Add an `_Imports.razor` file to the root folder of the project with the following content (change the last namespace, `MyAppNamespace`, to the namespace of the app):</span></span>

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

1. <span data-ttu-id="e2869-125">Registrieren Sie in `Startup.ConfigureServices` den :::no-loc(Blazor Server):::-Dienst:</span><span class="sxs-lookup"><span data-stu-id="e2869-125">In `Startup.ConfigureServices`, register the :::no-loc(Blazor Server)::: service:</span></span>

   ```csharp
   services.AddServerSide:::no-loc(Blazor):::();
   ```

1. <span data-ttu-id="e2869-126">Fügen Sie in `Startup.Configure` den :::no-loc(Blazor)::: Hub-Endpunkt zu `app.UseEndpoints` hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-126">In `Startup.Configure`, add the :::no-loc(Blazor)::: Hub endpoint to `app.UseEndpoints`:</span></span>

   ```csharp
   endpoints.Map:::no-loc(Blazor):::Hub();
   ```

1. <span data-ttu-id="e2869-127">Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="e2869-127">Integrate components into any page or view.</span></span> <span data-ttu-id="e2869-128">Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).</span><span class="sxs-lookup"><span data-stu-id="e2869-128">For more information, see the [Render components from a page or view](#render-components-from-a-page-or-view) section.</span></span>

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a><span data-ttu-id="e2869-129">Verwenden routingfähiger Komponenten in einer :::no-loc(Razor)::: Pages-App</span><span class="sxs-lookup"><span data-stu-id="e2869-129">Use routable components in a :::no-loc(Razor)::: Pages app</span></span>

<span data-ttu-id="e2869-130">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="e2869-130">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e2869-131">So richten Sie die Unterstützung von routingfähigen :::no-loc(Razor):::-Komponenten in :::no-loc(Razor)::: Pages-Apps ein:</span><span class="sxs-lookup"><span data-stu-id="e2869-131">To support routable :::no-loc(Razor)::: components in :::no-loc(Razor)::: Pages apps:</span></span>

1. <span data-ttu-id="e2869-132">Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="e2869-132">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="e2869-133">Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-133">Add an `App.razor` file to the project root with the following content:</span></span>

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

1. <span data-ttu-id="e2869-134">Fügen Sie dem Ordner `Pages` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-134">Add a `_Host.cshtml` file to the `Pages` folder with the following content:</span></span>

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e2869-135">Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="e2869-135">Components use the shared `_Layout.cshtml` file for their layout.</span></span>

   <span data-ttu-id="e2869-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="e2869-136"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e2869-137">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="e2869-137">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e2869-138">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer :::no-loc(Blazor):::-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="e2869-138">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="e2869-139">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="e2869-139">Render Mode</span></span> | <span data-ttu-id="e2869-140">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e2869-140">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e2869-141">Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine :::no-loc(Blazor Server):::-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-141">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="e2869-142">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="e2869-142">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e2869-143">Rendert einen Marker für eine :::no-loc(Blazor Server):::-App.</span><span class="sxs-lookup"><span data-stu-id="e2869-143">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="e2869-144">Die Ausgabe der `App`-Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="e2869-144">Output from the `App` component isn't included.</span></span> <span data-ttu-id="e2869-145">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="e2869-145">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e2869-146">Rendert die `App`-Komponente in statischem HTML.</span><span class="sxs-lookup"><span data-stu-id="e2869-146">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="e2869-147">Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e2869-147">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e2869-148">Fügen Sie eine Route mit niedriger Priorität für die Seite `_Host.cshtml` zur Endpunktkonfiguration in `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-148">Add a low-priority route for the `_Host.cshtml` page to endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. <span data-ttu-id="e2869-149">Fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-149">Add routable components to the app.</span></span> <span data-ttu-id="e2869-150">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e2869-150">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e2869-151">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="e2869-151">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="use-routable-components-in-an-mvc-app"></a><span data-ttu-id="e2869-152">Verwenden routingfähiger Komponenten in einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="e2869-152">Use routable components in an MVC app</span></span>

<span data-ttu-id="e2869-153">*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="e2869-153">*This section pertains to adding components that are directly routable from user requests.*</span></span>

<span data-ttu-id="e2869-154">So richten Sie die Unterstützung von routingfähigen :::no-loc(Razor):::-Komponenten in MVC-Apps ein:</span><span class="sxs-lookup"><span data-stu-id="e2869-154">To support routable :::no-loc(Razor)::: components in MVC apps:</span></span>

1. <span data-ttu-id="e2869-155">Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).</span><span class="sxs-lookup"><span data-stu-id="e2869-155">Follow the guidance in the [Prepare the app](#prepare-the-app) section.</span></span>

1. <span data-ttu-id="e2869-156">Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-156">Add an `App.razor` file to the root of the project with the following content:</span></span>

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

1. <span data-ttu-id="e2869-157">Fügen Sie dem Ordner `Views/Home` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-157">Add a `_Host.cshtml` file to the `Views/Home` folder with the following content:</span></span>

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   <span data-ttu-id="e2869-158">Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.</span><span class="sxs-lookup"><span data-stu-id="e2869-158">Components use the shared `_Layout.cshtml` file for their layout.</span></span>
   
   <span data-ttu-id="e2869-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="e2869-159"><xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> configures whether the `App` component:</span></span>

   * <span data-ttu-id="e2869-160">Ob die Komponente zuvor für die Seite gerendert wird</span><span class="sxs-lookup"><span data-stu-id="e2869-160">Is prerendered into the page.</span></span>
   * <span data-ttu-id="e2869-161">Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer :::no-loc(Blazor):::-App über den Benutzer-Agent enthält.</span><span class="sxs-lookup"><span data-stu-id="e2869-161">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a :::no-loc(Blazor)::: app from the user agent.</span></span>

   | <span data-ttu-id="e2869-162">Rendermodus</span><span class="sxs-lookup"><span data-stu-id="e2869-162">Render Mode</span></span> | <span data-ttu-id="e2869-163">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="e2869-163">Description</span></span> |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | <span data-ttu-id="e2869-164">Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine :::no-loc(Blazor Server):::-App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-164">Renders the `App` component into static HTML and includes a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="e2869-165">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="e2869-165">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | <span data-ttu-id="e2869-166">Rendert einen Marker für eine :::no-loc(Blazor Server):::-App.</span><span class="sxs-lookup"><span data-stu-id="e2869-166">Renders a marker for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="e2869-167">Die Ausgabe der `App`-Komponente ist nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="e2869-167">Output from the `App` component isn't included.</span></span> <span data-ttu-id="e2869-168">Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer :::no-loc(Blazor):::-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="e2869-168">When the user-agent starts, this marker is used to bootstrap a :::no-loc(Blazor)::: app.</span></span> |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | <span data-ttu-id="e2869-169">Rendert die `App`-Komponente in statischem HTML.</span><span class="sxs-lookup"><span data-stu-id="e2869-169">Renders the `App` component into static HTML.</span></span> |

   <span data-ttu-id="e2869-170">Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e2869-170">For more information on the Component Tag Helper, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

1. <span data-ttu-id="e2869-171">Fügen Sie eine Aktion zum Home-Controller hinzu:</span><span class="sxs-lookup"><span data-stu-id="e2869-171">Add an action to the Home controller:</span></span>

   ```csharp
   public IActionResult :::no-loc(Blazor):::()
   {
      return View("_Host");
   }
   ```

1. <span data-ttu-id="e2869-172">Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht `_Host.cshtml` an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="e2869-172">Add a low-priority route for the controller action that returns the `_Host.cshtml` view to the endpoint configuration in `Startup.Configure`:</span></span>

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController(":::no-loc(Blazor):::", "Home");
   });
   ```

1. <span data-ttu-id="e2869-173">Erstellen Sie einen `Pages`-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-173">Create a `Pages` folder and add routable components to the app.</span></span> <span data-ttu-id="e2869-174">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e2869-174">For example:</span></span>

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

<span data-ttu-id="e2869-175">Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).</span><span class="sxs-lookup"><span data-stu-id="e2869-175">For more information on namespaces, see the [Component namespaces](#component-namespaces) section.</span></span>

## <a name="render-components-from-a-page-or-view"></a><span data-ttu-id="e2869-176">Rendern von Komponenten einer Seite oder Ansicht</span><span class="sxs-lookup"><span data-stu-id="e2869-176">Render components from a page or view</span></span>

<span data-ttu-id="e2869-177">*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*</span><span class="sxs-lookup"><span data-stu-id="e2869-177">*This section pertains to adding components to pages or views, where the components aren't directly routable from user requests.*</span></span>

<span data-ttu-id="e2869-178">Verwenden Sie das [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) zum Rendern einer Komponente einer Seite oder Ansicht.</span><span class="sxs-lookup"><span data-stu-id="e2869-178">To render a component from a page or view, use the [Component Tag Helper](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper).</span></span>

### <a name="render-stateful-interactive-components"></a><span data-ttu-id="e2869-179">Rendern zustandsbehafteter interaktiver Komponenten</span><span class="sxs-lookup"><span data-stu-id="e2869-179">Render stateful interactive components</span></span>

<span data-ttu-id="e2869-180">Zustandsbehaftete interaktive Komponenten können einer :::no-loc(Razor):::-Seite oder -Ansicht hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="e2869-180">Stateful interactive components can be added to a :::no-loc(Razor)::: page or view.</span></span>

<span data-ttu-id="e2869-181">Wenn die Seite oder Ansicht gerendert wird:</span><span class="sxs-lookup"><span data-stu-id="e2869-181">When the page or view renders:</span></span>

* <span data-ttu-id="e2869-182">Die Komponente wird mit der Seite oder Ansicht vorab gerendert.</span><span class="sxs-lookup"><span data-stu-id="e2869-182">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="e2869-183">Der anfängliche Komponentenzustand, der zum Rendern vorab genutzt wurde, geht verloren.</span><span class="sxs-lookup"><span data-stu-id="e2869-183">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="e2869-184">Der neue Komponentenzustand wird erstellt, wenn die :::no-loc(SignalR):::-Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e2869-184">New component state is created when the :::no-loc(SignalR)::: connection is established.</span></span>

<span data-ttu-id="e2869-185">Die folgende :::no-loc(Razor):::-Seite rendert eine `Counter`-Komponente:</span><span class="sxs-lookup"><span data-stu-id="e2869-185">The following :::no-loc(Razor)::: page renders a `Counter` component:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="e2869-186">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e2869-186">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

### <a name="render-noninteractive-components"></a><span data-ttu-id="e2869-187">Rendern nicht interaktiver Komponenten</span><span class="sxs-lookup"><span data-stu-id="e2869-187">Render noninteractive components</span></span>

<span data-ttu-id="e2869-188">Auf der folgenden :::no-loc(Razor):::-Seite wird die `Counter`-Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e2869-188">In the following :::no-loc(Razor)::: page, the `Counter` component is statically rendered with an initial value that's specified using a form.</span></span> <span data-ttu-id="e2869-189">Da die Komponente statisch gerendert wird, ist die Komponente nicht interaktiv:</span><span class="sxs-lookup"><span data-stu-id="e2869-189">Since the component is statically rendered, the component isn't interactive:</span></span>

```cshtml
<h1>My :::no-loc(Razor)::: Page</h1>

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

<span data-ttu-id="e2869-190">Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span><span class="sxs-lookup"><span data-stu-id="e2869-190">For more information, see <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.</span></span>

## <a name="component-namespaces"></a><span data-ttu-id="e2869-191">Komponentennamespaces</span><span class="sxs-lookup"><span data-stu-id="e2869-191">Component namespaces</span></span>

<span data-ttu-id="e2869-192">Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace des Ordners zur Seite/Ansicht oder zur Datei `_ViewImports.cshtml` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e2869-192">When using a custom folder to hold the app's components, add the namespace representing the folder to either the page/view or to the `_ViewImports.cshtml` file.</span></span> <span data-ttu-id="e2869-193">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e2869-193">In the following example:</span></span>

* <span data-ttu-id="e2869-194">Ändern Sie `MyAppNamespace` in den Namespace der App.</span><span class="sxs-lookup"><span data-stu-id="e2869-194">Change `MyAppNamespace` to the app's namespace.</span></span>
* <span data-ttu-id="e2869-195">Wenn die Komponenten nicht in einem Ordner namens *Components* enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.</span><span class="sxs-lookup"><span data-stu-id="e2869-195">If a folder named *Components* isn't used to hold the components, change `Components` to the folder where the components reside.</span></span>

```cshtml
@using MyAppNamespace.Components
```

<span data-ttu-id="e2869-196">Die Datei `_ViewImports.cshtml` befindet sich im Ordner `Pages` einer :::no-loc(Razor):::-Pages-App oder im Ordner `Views` einer MVC-App.</span><span class="sxs-lookup"><span data-stu-id="e2869-196">The `_ViewImports.cshtml` file is located in the `Pages` folder of a :::no-loc(Razor)::: Pages app or the `Views` folder of an MVC app.</span></span>

<span data-ttu-id="e2869-197">Weitere Informationen finden Sie unter <xref:blazor/components/index#namespaces>.</span><span class="sxs-lookup"><span data-stu-id="e2869-197">For more information, see <xref:blazor/components/index#namespaces>.</span></span>
