---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: blazor/host-and-deploy/index
ms.openlocfilehash: a23bee120611ee603305a88dabac76566481fa4a
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97485887"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor"></a><span data-ttu-id="6a65b-103">Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="6a65b-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="6a65b-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="6a65b-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="6a65b-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="6a65b-105">Publish the app</span></span>

<span data-ttu-id="6a65b-106">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="6a65b-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6a65b-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6a65b-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6a65b-108">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="6a65b-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="6a65b-109">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="6a65b-109">Select the *publish target*.</span></span> <span data-ttu-id="6a65b-110">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="6a65b-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="6a65b-111">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="6a65b-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="6a65b-112">Wählen Sie die Schaltfläche **`Publish`** aus.</span><span class="sxs-lookup"><span data-stu-id="6a65b-112">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6a65b-113">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="6a65b-113">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6a65b-114">Klicken Sie auf **Erstellen** > **In Ordner veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="6a65b-114">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="6a65b-115">Bestätigen Sie den Ordner, in dem die veröffentlichten Ressourcen gespeichert werden, und wählen Sie **`Publish`** aus.</span><span class="sxs-lookup"><span data-stu-id="6a65b-115">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6a65b-116">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="6a65b-116">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6a65b-117">Verwenden Sie den Befehl [`dotnet publish`](/dotnet/core/tools/dotnet-publish), um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="6a65b-117">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="6a65b-118">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="6a65b-118">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="6a65b-119">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="6a65b-119">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="6a65b-120">Veröffentlichungsspeicherorte:</span><span class="sxs-lookup"><span data-stu-id="6a65b-120">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="6a65b-121">Eigenständig: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="6a65b-121">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="6a65b-122">Zum Bereitstellen der App als statische Website kopieren Sie den Inhalt des Ordners `wwwroot` auf den Host der statischen Website.</span><span class="sxs-lookup"><span data-stu-id="6a65b-122">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="6a65b-123">Gehostet: Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="6a65b-123">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="6a65b-124">Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.</span><span class="sxs-lookup"><span data-stu-id="6a65b-124">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="6a65b-125">Blazor Server: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="6a65b-125">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="6a65b-126">Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.</span><span class="sxs-lookup"><span data-stu-id="6a65b-126">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="6a65b-127">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6a65b-127">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="6a65b-128">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-128">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="6a65b-129">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="6a65b-129">App base path</span></span>

<span data-ttu-id="6a65b-130">Der *Basispfad einer App* beschreibt den URL-Stammpfad der App.</span><span class="sxs-lookup"><span data-stu-id="6a65b-130">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="6a65b-131">Betrachten Sie die folgende ASP.NET Core-App und die untergeordnete Blazor-App:</span><span class="sxs-lookup"><span data-stu-id="6a65b-131">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="6a65b-132">Die ASP.NET Core-App heißt `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="6a65b-132">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="6a65b-133">Die App befindet sich physisch unter `d:/MyApp`.</span><span class="sxs-lookup"><span data-stu-id="6a65b-133">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="6a65b-134">Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-134">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="6a65b-135">Eine Blazor-App mit dem Namen `CoolApp` ist die untergeordnete App von `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="6a65b-135">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="6a65b-136">Die untergeordnete App befindet sich physisch unter `d:/MyApp/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="6a65b-136">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="6a65b-137">Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-137">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="6a65b-138">Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="6a65b-138">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="6a65b-139">Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.</span><span class="sxs-lookup"><span data-stu-id="6a65b-139">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="6a65b-140">Um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben, wird das `href`-Attribut des `<base>`-Tags auf den relativen Stammpfad in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6a65b-140">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="6a65b-141">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="6a65b-141">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="6a65b-142">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="6a65b-142">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="6a65b-143">Blazor Server-Apps legen zusätzlich den serverseitigen Basispfad fest, indem sie <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in der Anforderungspipeline `Startup.Configure` der App aufrufen:</span><span class="sxs-lookup"><span data-stu-id="6a65b-143">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="6a65b-144">Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-144">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="6a65b-145">Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-145">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="6a65b-146">Ferner wird der Basispfad der App verwendet, um ausgewählte Hyperlinks abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet.</span><span class="sxs-lookup"><span data-stu-id="6a65b-146">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="6a65b-147">Der Router Blazor verarbeitet interne Navigation.</span><span class="sxs-lookup"><span data-stu-id="6a65b-147">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="6a65b-148">Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="6a65b-148">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="6a65b-149">In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="6a65b-149">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="6a65b-150">Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers der App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="6a65b-150">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="6a65b-151">Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei `Pages/_Host.cshtml` (Blazor Server) oder der Datei `wwwroot/index.html` (Blazor WebAssembly), um den Basispfad der App festzulegen.</span><span class="sxs-lookup"><span data-stu-id="6a65b-151">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="6a65b-152">Legen Sie den Wert des `href`-Attributs auf `/{RELATIVE URL PATH}/` (Blazor WebAssembly) oder `~/{RELATIVE URL PATH}/` (Blazor Server) fest.</span><span class="sxs-lookup"><span data-stu-id="6a65b-152">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="6a65b-153">**Der nachstehende Schrägstrich ist erforderlich.**</span><span class="sxs-lookup"><span data-stu-id="6a65b-153">**The trailing slash is required.**</span></span> <span data-ttu-id="6a65b-154">Der Platzhalter `{RELATIVE URL PATH}` ist der vollständige relative URL-Pfad der App.</span><span class="sxs-lookup"><span data-stu-id="6a65b-154">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="6a65b-155">Bei einer Blazor WebAssembly-App mit einem relativen URL-Pfad, der nicht zum Stammverzeichnis führt (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="6a65b-155">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="6a65b-156">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="6a65b-156">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="6a65b-157">**Fügen Sie keinen nachgestellten Schrägstrich ein.**</span><span class="sxs-lookup"><span data-stu-id="6a65b-157">**Don't include a trailing slash.**</span></span> <span data-ttu-id="6a65b-158">Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="6a65b-158">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="6a65b-159">Für eine Blazor WebAssembly-App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:</span><span class="sxs-lookup"><span data-stu-id="6a65b-159">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="6a65b-160">Die Blazor WebAssembly-App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="6a65b-160">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="6a65b-161">**Blazor Server `MapFallbackToPage`-Konfiguration**</span><span class="sxs-lookup"><span data-stu-id="6a65b-161">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="6a65b-162">Übergeben Sie den folgenden Pfad zu <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="6a65b-162">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="6a65b-163">Der Platzhalter `{RELATIVE PATH}` repräsentiert den Pfad auf dem Server (nicht der Stamm).</span><span class="sxs-lookup"><span data-stu-id="6a65b-163">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="6a65b-164">Beispielsweise lautet das Platzhaltersegment `CoolApp`, wenn die URL (nicht der Stamm) zur App `https://{HOST}:{PORT}/CoolApp/` lautet:</span><span class="sxs-lookup"><span data-stu-id="6a65b-164">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="6a65b-165">**Hosten mehrerer Blazor WebAssembly-Apps**</span><span class="sxs-lookup"><span data-stu-id="6a65b-165">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="6a65b-166">Weitere Informationen zum Hosten mehrerer Blazor WebAssembly-Apps in einer gehosteten Blazor-Lösung finden Sie unter <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span><span class="sxs-lookup"><span data-stu-id="6a65b-166">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="6a65b-167">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="6a65b-167">Deployment</span></span>

<span data-ttu-id="6a65b-168">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="6a65b-168">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
