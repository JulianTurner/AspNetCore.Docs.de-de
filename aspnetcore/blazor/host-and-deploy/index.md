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
ms.openlocfilehash: e7bc44b396b46e2ac3e0279520c7cc8ea6679f5a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279773"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="122b6-103">Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="122b6-103">Host and deploy ASP.NET Core Blazor</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="122b6-104">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="122b6-104">Publish the app</span></span>

<span data-ttu-id="122b6-105">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="122b6-105">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="122b6-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="122b6-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="122b6-107">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="122b6-107">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="122b6-108">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="122b6-108">Select the *publish target*.</span></span> <span data-ttu-id="122b6-109">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="122b6-109">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="122b6-110">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="122b6-110">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="122b6-111">Wählen Sie die Schaltfläche **`Publish`** aus.</span><span class="sxs-lookup"><span data-stu-id="122b6-111">Select the **`Publish`** button.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="122b6-112">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="122b6-112">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="122b6-113">Klicken Sie auf **Erstellen** > **In Ordner veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="122b6-113">Select **Build** > **Publish to Folder**.</span></span>
1. <span data-ttu-id="122b6-114">Bestätigen Sie den Ordner, in dem die veröffentlichten Ressourcen gespeichert werden, und wählen Sie **`Publish`** aus.</span><span class="sxs-lookup"><span data-stu-id="122b6-114">Confirm the folder to receive the published assets and select **`Publish`**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="122b6-115">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="122b6-115">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="122b6-116">Verwenden Sie den Befehl [`dotnet publish`](/dotnet/core/tools/dotnet-publish), um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="122b6-116">Use the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="122b6-117">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="122b6-117">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="122b6-118">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="122b6-118">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="122b6-119">Veröffentlichungsspeicherorte:</span><span class="sxs-lookup"><span data-stu-id="122b6-119">Publish locations:</span></span>

* Blazor WebAssembly
  * <span data-ttu-id="122b6-120">Eigenständig: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="122b6-120">Standalone: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span> <span data-ttu-id="122b6-121">Zum Bereitstellen der App als statische Website kopieren Sie den Inhalt des Ordners `wwwroot` auf den Host der statischen Website.</span><span class="sxs-lookup"><span data-stu-id="122b6-121">To deploy the app as a static site, copy the contents of the `wwwroot` folder to the static site host.</span></span>
  * <span data-ttu-id="122b6-122">Gehostet: Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="122b6-122">Hosted: The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="122b6-123">Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.</span><span class="sxs-lookup"><span data-stu-id="122b6-123">Deploy the contents of the `publish` folder to the host.</span></span>
* <span data-ttu-id="122b6-124">Blazor Server: Die App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="122b6-124">Blazor Server: The app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="122b6-125">Stellen Sie die Inhalte des Ordners `publish` auf dem Host bereit.</span><span class="sxs-lookup"><span data-stu-id="122b6-125">Deploy the contents of the `publish` folder to the host.</span></span>

<span data-ttu-id="122b6-126">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="122b6-126">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="122b6-127">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="122b6-127">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="122b6-128">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="122b6-128">App base path</span></span>

<span data-ttu-id="122b6-129">Der *Basispfad einer App* beschreibt den URL-Stammpfad der App.</span><span class="sxs-lookup"><span data-stu-id="122b6-129">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="122b6-130">Betrachten Sie die folgende ASP.NET Core-App und die untergeordnete Blazor-App:</span><span class="sxs-lookup"><span data-stu-id="122b6-130">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="122b6-131">Die ASP.NET Core-App heißt `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="122b6-131">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="122b6-132">Die App befindet sich physisch unter `d:/MyApp`.</span><span class="sxs-lookup"><span data-stu-id="122b6-132">The app physically resides at `d:/MyApp`.</span></span>
  * <span data-ttu-id="122b6-133">Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="122b6-133">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="122b6-134">Eine Blazor-App mit dem Namen `CoolApp` ist die untergeordnete App von `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="122b6-134">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="122b6-135">Die untergeordnete App befindet sich physisch unter `d:/MyApp/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="122b6-135">The sub-app physically resides at `d:/MyApp/CoolApp`.</span></span>
  * <span data-ttu-id="122b6-136">Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="122b6-136">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="122b6-137">Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="122b6-137">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="122b6-138">Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.</span><span class="sxs-lookup"><span data-stu-id="122b6-138">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="122b6-139">Um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben, wird das `href`-Attribut des `<base>`-Tags auf den relativen Stammpfad in der Datei `wwwroot/index.html` (Blazor WebAssembly) oder der Datei `Pages/_Host.cshtml` (Blazor Server) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122b6-139">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the `wwwroot/index.html` file (Blazor WebAssembly) or `Pages/_Host.cshtml` file (Blazor Server).</span></span>

<span data-ttu-id="122b6-140">Blazor WebAssembly (`wwwroot/index.html`):</span><span class="sxs-lookup"><span data-stu-id="122b6-140">Blazor WebAssembly (`wwwroot/index.html`):</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="122b6-141">Blazor Server (`Pages/_Host.cshtml`):</span><span class="sxs-lookup"><span data-stu-id="122b6-141">Blazor Server (`Pages/_Host.cshtml`):</span></span>

```html
<base href="~/CoolApp/">
```

<span data-ttu-id="122b6-142">Blazor Server-Apps legen zusätzlich den serverseitigen Basispfad fest, indem sie <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in der Anforderungspipeline `Startup.Configure` der App aufrufen:</span><span class="sxs-lookup"><span data-stu-id="122b6-142">Blazor Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="122b6-143">Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen.</span><span class="sxs-lookup"><span data-stu-id="122b6-143">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="122b6-144">Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen.</span><span class="sxs-lookup"><span data-stu-id="122b6-144">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="122b6-145">Ferner wird der Basispfad der App verwendet, um ausgewählte Hyperlinks abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet.</span><span class="sxs-lookup"><span data-stu-id="122b6-145">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="122b6-146">Der Router Blazor verarbeitet interne Navigation.</span><span class="sxs-lookup"><span data-stu-id="122b6-146">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="122b6-147">Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="122b6-147">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="122b6-148">In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="122b6-148">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="122b6-149">Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers der App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="122b6-149">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="122b6-150">Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei `Pages/_Host.cshtml` (Blazor Server) oder der Datei `wwwroot/index.html` (Blazor WebAssembly), um den Basispfad der App festzulegen.</span><span class="sxs-lookup"><span data-stu-id="122b6-150">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the `Pages/_Host.cshtml` file (Blazor Server) or `wwwroot/index.html` file (Blazor WebAssembly).</span></span> <span data-ttu-id="122b6-151">Legen Sie den Wert des `href`-Attributs auf `/{RELATIVE URL PATH}/` (Blazor WebAssembly) oder `~/{RELATIVE URL PATH}/` (Blazor Server) fest.</span><span class="sxs-lookup"><span data-stu-id="122b6-151">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (Blazor WebAssembly) or `~/{RELATIVE URL PATH}/` (Blazor Server).</span></span> <span data-ttu-id="122b6-152">**Der nachstehende Schrägstrich ist erforderlich.**</span><span class="sxs-lookup"><span data-stu-id="122b6-152">**The trailing slash is required.**</span></span> <span data-ttu-id="122b6-153">Der Platzhalter `{RELATIVE URL PATH}` ist der vollständige relative URL-Pfad der App.</span><span class="sxs-lookup"><span data-stu-id="122b6-153">The placeholder `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="122b6-154">Bei einer Blazor WebAssembly-App mit einem relativen URL-Pfad, der nicht zum Stammverzeichnis führt (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="122b6-154">For a Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="122b6-155">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="122b6-155">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="122b6-156">**Fügen Sie keinen nachgestellten Schrägstrich ein.**</span><span class="sxs-lookup"><span data-stu-id="122b6-156">**Don't include a trailing slash.**</span></span> <span data-ttu-id="122b6-157">Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="122b6-157">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="122b6-158">Für eine Blazor WebAssembly-App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:</span><span class="sxs-lookup"><span data-stu-id="122b6-158">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="122b6-159">Die Blazor WebAssembly-App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="122b6-159">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="122b6-160">**Blazor Server `MapFallbackToPage`-Konfiguration**</span><span class="sxs-lookup"><span data-stu-id="122b6-160">**Blazor Server `MapFallbackToPage` configuration**</span></span>

<span data-ttu-id="122b6-161">Übergeben Sie den folgenden Pfad zu <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="122b6-161">Pass the following path to <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> in `Startup.Configure`:</span></span>

```csharp
endpoints.MapFallbackToPage("/{RELATIVE PATH}/{**path:nonfile}");
```

<span data-ttu-id="122b6-162">Der Platzhalter `{RELATIVE PATH}` repräsentiert den Pfad auf dem Server (nicht der Stamm).</span><span class="sxs-lookup"><span data-stu-id="122b6-162">The placeholder `{RELATIVE PATH}` is the non-root path on the server.</span></span> <span data-ttu-id="122b6-163">Beispielsweise lautet das Platzhaltersegment `CoolApp`, wenn die URL (nicht der Stamm) zur App `https://{HOST}:{PORT}/CoolApp/` lautet:</span><span class="sxs-lookup"><span data-stu-id="122b6-163">For example, `CoolApp` is the placeholder segment if the non-root URL to the app is `https://{HOST}:{PORT}/CoolApp/`):</span></span>

```csharp
endpoints.MapFallbackToPage("/CoolApp/{**path:nonfile}");
```

<span data-ttu-id="122b6-164">**Hosten mehrerer Blazor WebAssembly-Apps**</span><span class="sxs-lookup"><span data-stu-id="122b6-164">**Host multiple Blazor WebAssembly apps**</span></span>

<span data-ttu-id="122b6-165">Weitere Informationen zum Hosten mehrerer Blazor WebAssembly-Apps in einer gehosteten Blazor-Lösung finden Sie unter <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span><span class="sxs-lookup"><span data-stu-id="122b6-165">For more information on hosting multiple Blazor WebAssembly apps in a hosted Blazor solution, see <xref:blazor/host-and-deploy/webassembly#hosted-deployment-with-multiple-blazor-webassembly-apps>.</span></span>

## <a name="deployment"></a><span data-ttu-id="122b6-166">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="122b6-166">Deployment</span></span>

<span data-ttu-id="122b6-167">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="122b6-167">For deployment guidance, see the following topics:</span></span>

* <xref:blazor/host-and-deploy/webassembly>
* <xref:blazor/host-and-deploy/server>
