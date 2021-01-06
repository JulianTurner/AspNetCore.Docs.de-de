---
title: Browserverknüpfung in ASP.NET Core
author: ncarandini
description: Erläutert das Visual Studio-Feature „Browserverknüpfung“, über das sich die Entwicklungsumgebung mit einem oder mehreren Webbrowsern verknüpfen lässt
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 01/09/2020
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
uid: client-side/using-browserlink
ms.openlocfilehash: 80f05acab55af973faf08b5db79ea4cbaf896b14
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054488"
---
# <a name="browser-link-in-aspnet-core"></a><span data-ttu-id="094f3-103">Browserverknüpfung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="094f3-103">Browser Link in ASP.NET Core</span></span>

<span data-ttu-id="094f3-104">Von [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="094f3-104">By [Nicolò Carandini](https://github.com/ncarandini), [Mike Wasson](https://github.com/MikeWasson), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="094f3-105">Browserverknüpfungen sind ein Visual Studio-Feature.</span><span class="sxs-lookup"><span data-stu-id="094f3-105">Browser Link is a Visual Studio feature.</span></span> <span data-ttu-id="094f3-106">Sie stellen einen Kommunikationskanal zwischen der Entwicklungsumgebung und einem oder mehreren Webbrowsern dar.</span><span class="sxs-lookup"><span data-stu-id="094f3-106">It creates a communication channel between the development environment and one or more web browsers.</span></span> <span data-ttu-id="094f3-107">Mit einer Browserverknüpfung können Sie Ihre Web-App in mehreren Browsern gleichzeitig aktualisieren, was für browserübergreifende Tests nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="094f3-107">You can use Browser Link to refresh your web app in several browsers at once, which is useful for cross-browser testing.</span></span>

## <a name="browser-link-setup"></a><span data-ttu-id="094f3-108">Einrichtung einer Browserverknüpfung</span><span class="sxs-lookup"><span data-stu-id="094f3-108">Browser Link setup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="094f3-109">Fügen Sie Ihrem Projekt das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="094f3-109">Add the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package to your project.</span></span> <span data-ttu-id="094f3-110">Aktivieren Sie für ASP.NET Core-Projekte mit Razor Pages oder MVC außerdem die Laufzeitkompilierung von Razor-Dateien ( *.cshtml*). Dieser Vorgang wird unter <xref:mvc/views/view-compilation> beschrieben.</span><span class="sxs-lookup"><span data-stu-id="094f3-110">For ASP.NET Core Razor Pages or MVC projects, also enable runtime compilation of Razor (*.cshtml*) files as described in <xref:mvc/views/view-compilation>.</span></span> <span data-ttu-id="094f3-111">Razor-Syntaxänderungen werden nur bei aktivierter Laufzeitkompilierung angewendet.</span><span class="sxs-lookup"><span data-stu-id="094f3-111">Razor syntax changes are applied only when runtime compilation has been enabled.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="094f3-112">Wenn Sie ein ASP.NET Core 2.0-Projekt in ein ASP.NET Core 2.1-Projekt konvertieren und auf das [Metapaket „Microsoft.AspNetCore.App“](xref:fundamentals/metapackage-app) umstellen, installieren Sie das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) für das Feature „Browserverknüpfung“.</span><span class="sxs-lookup"><span data-stu-id="094f3-112">When converting an ASP.NET Core 2.0 project to ASP.NET Core 2.1 and transitioning to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), install the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package for Browser Link functionality.</span></span> <span data-ttu-id="094f3-113">In ASP.NET Core 2.1-Projektvorlagen wird standardmäßig das Metapaket `Microsoft.AspNetCore.App` verwendet.</span><span class="sxs-lookup"><span data-stu-id="094f3-113">The ASP.NET Core 2.1 project templates use the `Microsoft.AspNetCore.App` metapackage by default.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="094f3-114">In den ASP.NET Core 2.0-Projektvorlagen **Webanwendung**, **Leer** und **Web-API** wird das [Metapaket „Microsoft.AspNetCore.All“](xref:fundamentals/metapackage) verwendet, das einen Paketverweis auf [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) enthält.</span><span class="sxs-lookup"><span data-stu-id="094f3-114">The ASP.NET Core 2.0 **Web Application**, **Empty**, and **Web API** project templates use the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage), which contains a package reference for [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span></span> <span data-ttu-id="094f3-115">Daher ist für das Metapaket `Microsoft.AspNetCore.All` keine weitere Aktion erforderlich, um Browserverknüpfungen verwendbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="094f3-115">Therefore, using the `Microsoft.AspNetCore.All` metapackage requires no further action to make Browser Link available for use.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="094f3-116">Die ASP.NET Core 1.x-Projektvorlage **Webanwendung** verfügt über einen Paketverweis auf das Paket [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/).</span><span class="sxs-lookup"><span data-stu-id="094f3-116">The ASP.NET Core 1.x **Web Application** project template has a package reference for the [Microsoft.VisualStudio.Web.BrowserLink](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.BrowserLink/) package.</span></span> <span data-ttu-id="094f3-117">Bei anderen Projekttypen müssen Sie `Microsoft.VisualStudio.Web.BrowserLink` den Paketverweis manuell hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="094f3-117">Other project types require you to add a package reference to `Microsoft.VisualStudio.Web.BrowserLink`.</span></span>

::: moniker-end

### <a name="configuration"></a><span data-ttu-id="094f3-118">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="094f3-118">Configuration</span></span>

<span data-ttu-id="094f3-119">Rufen Sie `UseBrowserLink` in der `Startup.Configure`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="094f3-119">Call `UseBrowserLink` in the `Startup.Configure` method:</span></span>

```csharp
app.UseBrowserLink();
```

<span data-ttu-id="094f3-120">Der Befehl `UseBrowserLink` wird in der Regel in einem `if`-Block platziert, mit dem Browserverknüpfungen nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="094f3-120">The `UseBrowserLink` call is typically placed inside an `if` block that only enables Browser Link in the Development environment.</span></span> <span data-ttu-id="094f3-121">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="094f3-121">For example:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseBrowserLink();
}
```

<span data-ttu-id="094f3-122">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="094f3-122">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="how-to-use-browser-link"></a><span data-ttu-id="094f3-123">Verwenden von Browserverknüpfungen</span><span class="sxs-lookup"><span data-stu-id="094f3-123">How to use Browser Link</span></span>

<span data-ttu-id="094f3-124">Wenn Sie ein ASP.NET Core-Projekt geöffnet haben, zeigt Visual Studio das Symbolleisten-Steuerelement für Browserverknüpfungen neben dem Symbolleisten-Steuerelement **Debugziel** an:</span><span class="sxs-lookup"><span data-stu-id="094f3-124">When you have an ASP.NET Core project open, Visual Studio shows the Browser Link toolbar control next to the **Debug Target** toolbar control:</span></span>

![Dropdownmenü für Browserverknüpfungen](using-browserlink/_static/browserLink-dropdown-menu.png)

<span data-ttu-id="094f3-126">Über das Symbolleisten-Steuerelement für Browserverknüpfungen können Sie folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="094f3-126">From the Browser Link toolbar control, you can:</span></span>

* <span data-ttu-id="094f3-127">Aktualisieren einer Web-App in mehreren Browsern gleichzeitig</span><span class="sxs-lookup"><span data-stu-id="094f3-127">Refresh the web app in several browsers at once.</span></span>
* <span data-ttu-id="094f3-128">Öffnen des **Dashboards für Browserverknüpfungen**</span><span class="sxs-lookup"><span data-stu-id="094f3-128">Open the **Browser Link Dashboard**.</span></span>
* <span data-ttu-id="094f3-129">Aktivieren/Deaktivieren von **Browserverknüpfungen**.</span><span class="sxs-lookup"><span data-stu-id="094f3-129">Enable or disable **Browser Link**.</span></span> <span data-ttu-id="094f3-130">Hinweis: Browserverknüpfungen sind in Visual Studio standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="094f3-130">Note: Browser Link is disabled by default in Visual Studio.</span></span>
* <span data-ttu-id="094f3-131">Aktivieren oder Deaktivieren der [automatischen CSS-Synchronisierung](#enable-or-disable-css-auto-sync)</span><span class="sxs-lookup"><span data-stu-id="094f3-131">Enable or disable [CSS Auto-Sync](#enable-or-disable-css-auto-sync).</span></span>

## <a name="refresh-the-web-app-in-several-browsers-at-once"></a><span data-ttu-id="094f3-132">Aktualisieren einer Web-App in mehreren Browsern gleichzeitig</span><span class="sxs-lookup"><span data-stu-id="094f3-132">Refresh the web app in several browsers at once</span></span>

<span data-ttu-id="094f3-133">Wenn Sie einen einzelnen Webbrowser auswählen möchten, der beim Starten des Projekts gestartet werden soll, verwenden Sie das Dropdownmenü im Symbolleisten-Steuerelement **Debugziel**:</span><span class="sxs-lookup"><span data-stu-id="094f3-133">To choose a single web browser to launch when starting the project, use the drop-down menu in the **Debug Target** toolbar control:</span></span>

![F5-Dropdownmenü](using-browserlink/_static/debug-target-dropdown-menu.png)

<span data-ttu-id="094f3-135">Zum gleichzeitigen Öffnen mehrerer Browser klicken Sie in der Dropdownliste auf **Browserauswahl...** .</span><span class="sxs-lookup"><span data-stu-id="094f3-135">To open multiple browsers at once, choose **Browse with...** from the same drop-down.</span></span> <span data-ttu-id="094f3-136">Halten Sie die Taste <kbd>STRG</kbd> gedrückt, um die gewünschten Browser auszuwählen, und klicken Sie dann auf **Durchsuchen**:</span><span class="sxs-lookup"><span data-stu-id="094f3-136">Hold down the <kbd>Ctrl</kbd> key to select the browsers you want, and then click **Browse**:</span></span>

![Gleichzeitiges Öffnen mehrerer Browser](using-browserlink/_static/open-many-browsers-at-once.png)

<span data-ttu-id="094f3-138">Der folgende Screenshot zeigt Visual Studio mit geöffneter Indexansicht und zwei geöffneten Browsern:</span><span class="sxs-lookup"><span data-stu-id="094f3-138">The following screenshot shows Visual Studio with the Index view open and two open browsers:</span></span>

![Beispiel für die Synchronisierung mit zwei Browsern](using-browserlink/_static/sync-with-two-browsers-example.png)

<span data-ttu-id="094f3-140">Zeigen Sie auf das Symbolleisten-Steuerelement für Browserverknüpfungen, um sich die Browser anzeigen zu lassen, die mit dem Projekt verbunden sind:</span><span class="sxs-lookup"><span data-stu-id="094f3-140">Hover over the Browser Link toolbar control to see the browsers that are connected to the project:</span></span>

![Hovertipp](using-browserlink/_static/hoover-tip.png)

<span data-ttu-id="094f3-142">Wenn Sie etwas in der Indexansicht ändern und anschließend im Symbolleisten-Steuerelement für Browserverknüpfungen auf die Schaltfläche zum Aktualisieren klicken, werden alle verbundenen Browser aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="094f3-142">Change the Index view, and all connected browsers are updated when you click the Browser Link refresh button:</span></span>

![Browser werden mit Änderungen synchronisiert](using-browserlink/_static/browsers-sync-to-changes.png)

<span data-ttu-id="094f3-144">Browserverknüpfungen funktionieren auch, wenn Sie Browser außerhalb von Visual Studio starten und zur App-URL navigieren.</span><span class="sxs-lookup"><span data-stu-id="094f3-144">Browser Link also works with browsers that you launch from outside Visual Studio and navigate to the app URL.</span></span>

### <a name="the-browser-link-dashboard"></a><span data-ttu-id="094f3-145">Das Dashboard für Browserverknüpfungen</span><span class="sxs-lookup"><span data-stu-id="094f3-145">The Browser Link Dashboard</span></span>

<span data-ttu-id="094f3-146">Öffnen Sie im Featuredropdownmenü das Fenster **Browserlink-Dashboard**, um die Verbindung mit geöffneten Browsern zu verwalten:</span><span class="sxs-lookup"><span data-stu-id="094f3-146">Open the **Browser Link Dashboard** window from the Browser Link drop down menu to manage the connection with open browsers:</span></span>

![Dashboard für Browserverknüpfungen öffnen](using-browserlink/_static/open-browserlink-dashboard.png)

<span data-ttu-id="094f3-148">Wenn kein Browser verbunden ist, können Sie eine Nichtdebuggingsitzung starten, indem Sie auf den Link **In Browser anzeigen** klicken:</span><span class="sxs-lookup"><span data-stu-id="094f3-148">If no browser is connected, you can start a non-debugging session by selecting the **View in Browser** link:</span></span>

![Dashboard für Browserverknüpfungen: keine Verbindungen](using-browserlink/_static/browserlink-dashboard-no-connections.png)

<span data-ttu-id="094f3-150">Andernfalls werden die verbundenen Browser mit dem Pfad zu der Seite angezeigt, die von den einzelnen Browsern angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="094f3-150">Otherwise, the connected browsers are shown with the path to the page that each browser is showing:</span></span>

![Dashboard für Browserverknüpfungen: zwei Verbindungen](using-browserlink/_static/browserlink-dashboard-two-connections.png)

<span data-ttu-id="094f3-152">Sie können auch auf einen einzelnen Browsernamen klicken, um nur diesen einen Browser zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="094f3-152">You can also click on an individual browser name to refresh only that browser.</span></span>

### <a name="enable-or-disable-browser-link"></a><span data-ttu-id="094f3-153">Aktivieren oder Deaktivieren von Browserverknüpfungen</span><span class="sxs-lookup"><span data-stu-id="094f3-153">Enable or disable Browser Link</span></span>

<span data-ttu-id="094f3-154">Wenn Sie die Browserverknüpfungen nach einer Deaktivierung wieder aktivieren, müssen Sie die Browser aktualisieren, um sie neu zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="094f3-154">When you re-enable Browser Link after disabling it, you must refresh the browsers to reconnect them.</span></span>

### <a name="enable-or-disable-css-auto-sync"></a><span data-ttu-id="094f3-155">Aktivieren oder Deaktivieren der automatischen CSS-Synchronisierung</span><span class="sxs-lookup"><span data-stu-id="094f3-155">Enable or disable CSS Auto-Sync</span></span>

<span data-ttu-id="094f3-156">Wenn die automatische CSS-Synchronisierung aktiviert ist, werden verbundene Browser automatisch aktualisiert, wenn Sie Änderungen an CSS-Dateien vornehmen.</span><span class="sxs-lookup"><span data-stu-id="094f3-156">When CSS Auto-Sync is enabled, connected browsers are automatically refreshed when you make any change to CSS files.</span></span>

## <a name="how-it-works"></a><span data-ttu-id="094f3-157">Funktionsweise</span><span class="sxs-lookup"><span data-stu-id="094f3-157">How it works</span></span>

<span data-ttu-id="094f3-158">Die Browserverknüpfung erstellt mit [SignalR](xref:signalr/introduction) einen Kommunikationskanal zwischen Visual Studio und dem Browser.</span><span class="sxs-lookup"><span data-stu-id="094f3-158">Browser Link uses [SignalR](xref:signalr/introduction) to create a communication channel between Visual Studio and the browser.</span></span> <span data-ttu-id="094f3-159">Wenn die Browserverknüpfung aktiviert ist, fungiert Visual Studio als SignalR-Server, mit dem mehrere Clients (Browser) eine Verbindung herstellen können.</span><span class="sxs-lookup"><span data-stu-id="094f3-159">When Browser Link is enabled, Visual Studio acts as a SignalR server that multiple clients (browsers) can connect to.</span></span> <span data-ttu-id="094f3-160">Die Browserverknüpfung registriert auch eine Middlewarekomponente in der Anforderungspipeline von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="094f3-160">Browser Link also registers a middleware component in the ASP.NET Core request pipeline.</span></span> <span data-ttu-id="094f3-161">Diese Komponente fügt jeder Seitenanforderung des Servers besondere `<script>`-Verweise hinzu.</span><span class="sxs-lookup"><span data-stu-id="094f3-161">This component injects special `<script>` references into every page request from the server.</span></span> <span data-ttu-id="094f3-162">Sie können sich die Skriptverweise anzeigen lassen, indem Sie im Browser auf **Quelltext anzeigen** klicken und zum Ende des `<body>`-Taginhalts scrollen:</span><span class="sxs-lookup"><span data-stu-id="094f3-162">You can see the script references by selecting **View source** in the browser and scrolling to the end of the `<body>` tag content:</span></span>

```html
    <!-- Visual Studio Browser Link -->
    <script type="application/json" id="__browserLink_initializationData">
        {"requestId":"a717d5a07c1741949a7cefd6fa2bad08","requestMappingFromServer":false}
    </script>
    <script type="text/javascript" src="http://localhost:54139/b6e36e429d034f578ebccd6a79bf19bf/browserLink" async="async"></script>
    <!-- End Browser Link -->
</body>
```

<span data-ttu-id="094f3-163">Ihre Quelldateien werden nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="094f3-163">Your source files aren't modified.</span></span> <span data-ttu-id="094f3-164">Die Middlewarekomponente fügt die Skriptverweise dynamisch ein.</span><span class="sxs-lookup"><span data-stu-id="094f3-164">The middleware component injects the script references dynamically.</span></span>

<span data-ttu-id="094f3-165">Da der browserseitige Code komplett in JavaScript geschrieben ist, funktioniert er in allen Browsern, die SignalR ohne ein zusätzliches Browser-Plug-In unterstützt.</span><span class="sxs-lookup"><span data-stu-id="094f3-165">Because the browser-side code is all JavaScript, it works on all browsers that SignalR supports without requiring a browser plug-in.</span></span>
