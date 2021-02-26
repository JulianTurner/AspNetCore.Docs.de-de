---
title: Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
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
uid: blazor/host-and-deploy/webassembly
ms.openlocfilehash: 04eba2e004e920e9ca799b316781857f0b0b4ca3
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279779"
---
# <a name="host-and-deploy-aspnet-core-blazor-webassembly"></a><span data-ttu-id="41e50-103">Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="41e50-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="41e50-104">Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="41e50-104">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="41e50-105">Die Blazor-App, ihre Abhängigkeiten und die .NET-Runtime werden parallel im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="41e50-105">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="41e50-106">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="41e50-106">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="41e50-107">Die folgenden Bereitstellungsstrategien werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="41e50-107">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="41e50-108">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="41e50-108">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="41e50-109">Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.</span><span class="sxs-lookup"><span data-stu-id="41e50-109">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="41e50-110">Die Blazor-App wird auf einem Webserver oder Webservice für statisches Hosting abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-110">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="41e50-111">Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als untergeordnete IIS-App enthält.</span><span class="sxs-lookup"><span data-stu-id="41e50-111">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="41e50-112">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="41e50-112">Compression</span></span>

<span data-ttu-id="41e50-113">Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="41e50-113">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="41e50-114">Die folgenden Komprimierungsalgorithmen werden verwendet:</span><span class="sxs-lookup"><span data-stu-id="41e50-114">The following compression algorithms are used:</span></span>

* <span data-ttu-id="41e50-115">[Brotli](https://tools.ietf.org/html/rfc7932) (höchste Stufe)</span><span class="sxs-lookup"><span data-stu-id="41e50-115">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="41e50-116">Gzip</span><span class="sxs-lookup"><span data-stu-id="41e50-116">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="41e50-117">Blazor basiert auf dem Host, um die entsprechenden komprimierten Dateien bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="41e50-117">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="41e50-118">Wenn Sie ein mit ASP.NET Core gehostetes Projekt verwenden, kann das Hostprojekt die Inhaltsaushandlung durchführen und die statisch komprimierten Dateien bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="41e50-118">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="41e50-119">Beim Hosten einer eigenständigen Blazor WebAssembly-App sind möglicherweise zusätzliche Schritte erforderlich, um sicherzustellen, dass die statisch komprimierten Dateien bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="41e50-119">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="41e50-120">Informationen zur Komprimierungskonfiguration der Datei `web.config` von IIS finden Sie im Abschnitt [IIS: Brotli- und Gzip-Komprimierung](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="41e50-120">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="41e50-121">Beim Hosten über statische Hostinglösungen, die die Inhaltsaushandlung für statisch komprimierte Dateien nicht unterstützen (z. B. GitHub-Seiten), sollten Sie die App so konfigurieren, dass sie die in das Brotli-Format komprimierten Dateien abruft und decodiert:</span><span class="sxs-lookup"><span data-stu-id="41e50-121">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="41e50-122">Rufen Sie den JavaScript Brotli-Decoder aus dem [GitHub-Repository „google/brotli“](https://github.com/google/brotli) ab.</span><span class="sxs-lookup"><span data-stu-id="41e50-122">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="41e50-123">Die Decoderdatei heißt `decode.js` und befindet sich im [Ordner `js`](https://github.com/google/brotli/tree/master/js) des Repositorys.</span><span class="sxs-lookup"><span data-stu-id="41e50-123">The decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="41e50-124">Eine Regression ist in der verkleinerten Version des Skripts `decode.js` (`decode.min.js`) im [GitHub-Repository „google/brotli“](https://github.com/google/brotli) enthalten.</span><span class="sxs-lookup"><span data-stu-id="41e50-124">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="41e50-125">Sie können das Skript entweder selbst minimieren (siehe z. B. [Konfigurieren der Bündelung und Minimierung](xref:client-side/bundling-and-minification#configure-bundling-and-minification)) oder das [npm-Paket](https://www.npmjs.com/package/brotli) verwenden, bis das Issue [TypeError in decode.min.js (google/brotli #881)](https://github.com/google/brotli/issues/881) behoben ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-125">Either minify the script on your own (for example, see [BuildBundlerMinifier bundling and minification](xref:client-side/bundling-and-minification#configure-bundling-and-minification)) or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [TypeError in decode.min.js (google/brotli #881)](https://github.com/google/brotli/issues/881) is resolved.</span></span> <span data-ttu-id="41e50-126">Im Beispielcode in diesem Abschnitt wird die **nicht verkleinerte** Version des Skripts verwendet.</span><span class="sxs-lookup"><span data-stu-id="41e50-126">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="41e50-127">Aktualisieren Sie die App zur Verwendung des Decoders.</span><span class="sxs-lookup"><span data-stu-id="41e50-127">Update the app to use the decoder.</span></span> <span data-ttu-id="41e50-128">Ändern Sie das Markup innerhalb des schließenden `<body>`-Tags in der Datei `wwwroot/index.html` wie folgt:</span><span class="sxs-lookup"><span data-stu-id="41e50-128">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
    ```html
    <script src="decode.js"></script>
    <script src="_framework/blazor.webassembly.js" autostart="false"></script>
    <script>
      Blazor.start({
        loadBootResource: function (type, name, defaultUri, integrity) {
          if (type !== 'dotnetjs' && location.hostname !== 'localhost') {
            return (async function () {
              const response = await fetch(defaultUri + '.br', { cache: 'no-cache' });
              if (!response.ok) {
                throw new Error(response.statusText);
              }
              const originalResponseBuffer = await response.arrayBuffer();
              const originalResponseArray = new Int8Array(originalResponseBuffer);
              const decompressedResponseArray = BrotliDecode(originalResponseArray);
              const contentType = type === 
                'dotnetwasm' ? 'application/wasm' : 'application/octet-stream';
              return new Response(decompressedResponseArray, 
                { headers: { 'content-type': contentType } });
            })();
          }
        }
      });
    </script>
    ```
 
<span data-ttu-id="41e50-129">Um die Komprimierung zu deaktivieren, fügen Sie der Projektdatei der App die `BlazorEnableCompression` MSBuild-Eigenschaft hinzu, und setzen Sie den Wert auf `false`:</span><span class="sxs-lookup"><span data-stu-id="41e50-129">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="41e50-130">Die `BlazorEnableCompression`-Eigenschaft kann an den [`dotnet publish`](/dotnet/core/tools/dotnet-publish)-Befehl mit der folgenden Syntax in einer Befehlsshell übergeben werden:</span><span class="sxs-lookup"><span data-stu-id="41e50-130">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="41e50-131">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="41e50-131">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="41e50-132">Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="41e50-132">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="41e50-133">Betrachten wir eine Blazor WebAssembly-App mit zwei Komponenten:</span><span class="sxs-lookup"><span data-stu-id="41e50-133">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="41e50-134">`Main.razor`: Wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="41e50-134">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="41e50-135">`About.razor`: `About`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="41e50-135">`About.razor`: `About` component.</span></span>

<span data-ttu-id="41e50-136">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="41e50-136">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="41e50-137">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="41e50-137">The browser makes a request.</span></span>
1. <span data-ttu-id="41e50-138">Die Standardseite wird zurückgegeben, in der Regel `index.html`.</span><span class="sxs-lookup"><span data-stu-id="41e50-138">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="41e50-139">`index.html` startet die App.</span><span class="sxs-lookup"><span data-stu-id="41e50-139">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="41e50-140">Der Blazor-Router wird geladen, und die Razor-Komponente `Main` wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="41e50-140">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="41e50-141">Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-141">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="41e50-142">Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="41e50-142">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="41e50-143">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="41e50-143">The router handles the requests internally.</span></span>

<span data-ttu-id="41e50-144">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="41e50-144">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="41e50-145">Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-145">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="41e50-146">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite `index.html` erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-146">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="41e50-147">Wenn `index.html` zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="41e50-147">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="41e50-148">Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten `web.config`-Datei der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="41e50-148">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="41e50-149">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="41e50-149">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="41e50-150">Gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="41e50-150">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="41e50-151">Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-151">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="41e50-152">Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="41e50-152">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="41e50-153">Die beiden Apps werden zusammen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-153">The two apps are deployed together.</span></span> <span data-ttu-id="41e50-154">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="41e50-154">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="41e50-155">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [`dotnet new`](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **`Hosted`** (`-ho|--hosted`, wenn Sie den Befehl `dotnet new` verwenden).</span><span class="sxs-lookup"><span data-stu-id="41e50-155">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="41e50-156">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="41e50-156">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="41e50-157">Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="41e50-157">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-blazor-webassembly-apps"></a><span data-ttu-id="41e50-158">Gehostete Bereitstellung mit mehreren Blazor WebAssembly-Apps</span><span class="sxs-lookup"><span data-stu-id="41e50-158">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="41e50-159">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="41e50-159">App configuration</span></span>

<span data-ttu-id="41e50-160">Gehostete Blazor-Lösungen können mehrere Blazor WebAssembly-Apps verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="41e50-160">Hosted Blazor solutions can serve multiple Blazor WebAssembly apps.</span></span>

> [!NOTE]
> <span data-ttu-id="41e50-161">Das Beispiel in diesem Abschnitt verweist auf die Verwendung einer Visual Studio-*Projektmappe*, aber die Verwendung von Visual Studio und einer Visual Studio-Projektmappe ist nicht erforderlich, damit mehrere Client-Apps in einem Szenario mit gehosteten Blazor WebAssembly-Apps funktionieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-161">The example in this section references the use of a Visual Studio *solution*, but the use of Visual Studio and a Visual Studio solution isn't required for multiple client apps to work in a hosted Blazor WebAssembly apps scenario.</span></span> <span data-ttu-id="41e50-162">Wenn Sie Visual Studio nicht verwenden, ignorieren Sie die `{SOLUTION NAME}.sln`-Datei und alle anderen Dateien, die für Visual Studio erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="41e50-162">If you aren't using Visual Studio, ignore the `{SOLUTION NAME}.sln` file and any other files created for Visual Studio.</span></span>

<span data-ttu-id="41e50-163">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="41e50-163">In the following example:</span></span>

* <span data-ttu-id="41e50-164">Die anfängliche (erste) Client-App ist das Standardclientprojekt einer Projektmappe, die aus der Blazor WebAssembly-Projektvorlage erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="41e50-164">The initial (first) client app is the default client project of a solution created from the Blazor WebAssembly project template.</span></span> <span data-ttu-id="41e50-165">Der Zugriff auf die erste Client-App erfolgt in einem Browser über die URL `/FirstApp` entweder auf Port 5001 oder mit einem Host von `firstapp.com`.</span><span class="sxs-lookup"><span data-stu-id="41e50-165">The first client app is accessible in a browser from the URL `/FirstApp` on either port 5001 or with a host of `firstapp.com`.</span></span>
* <span data-ttu-id="41e50-166">Eine zweite Client-App, `SecondBlazorApp.Client`, wird der Projektmappe hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e50-166">A second client app is added to the solution, `SecondBlazorApp.Client`.</span></span> <span data-ttu-id="41e50-167">Der Zugriff auf die zweite Client-App erfolgt in einem Browser über die URL `/SecondApp` entweder auf Port 5002 oder mit einem Host von `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="41e50-167">The second client app is accessible in a browser from the the URL `/SecondApp` on either port 5002 or with a host of `secondapp.com`.</span></span>

<span data-ttu-id="41e50-168">Verwenden Sie eine vorhandene gehostete Blazor-Lösung, oder erstellen Sie eine neue Projektmappe aus der Projektvorlage „Blazor Hosted“:</span><span class="sxs-lookup"><span data-stu-id="41e50-168">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template:</span></span>

* <span data-ttu-id="41e50-169">Fügen Sie in der Projektdatei der Client-App `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `FirstApp` hinzu, um den Basispfad für die statischen Ressourcen des Projekts festzulegen:</span><span class="sxs-lookup"><span data-stu-id="41e50-169">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="41e50-170">Fügen Sie der Projektmappe eine zweite Client-App hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-170">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="41e50-171">Fügen Sie dem Ordner der Projektmappe einen Ordner mit dem Namen `SecondClient` hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-171">Add a folder named `SecondClient` to the solution's folder.</span></span> <span data-ttu-id="41e50-172">Der Projektmappenordner, der aus der Projektvorlage erstellt wurde, enthält die folgende Projektmappendatei und die folgenden Ordner, nachdem der `SecondClient`-Ordner hinzugefügt wurde:</span><span class="sxs-lookup"><span data-stu-id="41e50-172">The solution folder created from the project template contains the following solution file and folders after the `SecondClient` folder is added:</span></span>
  
    * <span data-ttu-id="41e50-173">`Client` (Ordner)</span><span class="sxs-lookup"><span data-stu-id="41e50-173">`Client` (folder)</span></span>
    * <span data-ttu-id="41e50-174">`SecondClient` (Ordner)</span><span class="sxs-lookup"><span data-stu-id="41e50-174">`SecondClient` (folder)</span></span>
    * <span data-ttu-id="41e50-175">`Server` (Ordner)</span><span class="sxs-lookup"><span data-stu-id="41e50-175">`Server` (folder)</span></span>
    * <span data-ttu-id="41e50-176">`Shared` (Ordner)</span><span class="sxs-lookup"><span data-stu-id="41e50-176">`Shared` (folder)</span></span>
    * <span data-ttu-id="41e50-177">`{SOLUTION NAME}.sln` (Datei)</span><span class="sxs-lookup"><span data-stu-id="41e50-177">`{SOLUTION NAME}.sln` (file)</span></span>
    
    <span data-ttu-id="41e50-178">Der Platzhalter `{SOLUTION NAME}` ist der Name der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="41e50-178">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

  * <span data-ttu-id="41e50-179">Erstellen Sie eine Blazor WebAssembly-App mit dem Namen `SecondBlazorApp.Client` im Ordner `SecondClient` aus der Blazor WebAssembly-Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="41e50-179">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>

  * <span data-ttu-id="41e50-180">In der Projektdatei der `SecondBlazorApp.Client`-App:</span><span class="sxs-lookup"><span data-stu-id="41e50-180">In the `SecondBlazorApp.Client` app's project file:</span></span>

    * <span data-ttu-id="41e50-181">Fügen Sie `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `SecondApp` hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-181">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="41e50-182">Fügt einen Projektverweis auf das `Shared`-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-182">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="41e50-183">Der Platzhalter `{SOLUTION NAME}` ist der Name der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="41e50-183">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="41e50-184">Erstellen Sie in der Projektdatei der Server-App einen Projektverweis für die hinzugefügte `SecondBlazorApp.Client`-Client-App:</span><span class="sxs-lookup"><span data-stu-id="41e50-184">In the server app's project file, create a project reference for the added `SecondBlazorApp.Client` client app:</span></span>

  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Client\{SOLUTION NAME}.Client.csproj" />
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
    <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
  </ItemGroup>
  ```
  
  <span data-ttu-id="41e50-185">Der Platzhalter `{SOLUTION NAME}` ist der Name der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="41e50-185">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="41e50-186">Konfigurieren Sie in der Datei `Properties/launchSettings.json` der Server-App die `applicationUrl` des Kestrel-Profils (`{SOLUTION NAME}.Server`) für den Zugriff auf Client-Apps an den Ports 5001 und 5002:</span><span class="sxs-lookup"><span data-stu-id="41e50-186">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="41e50-187">Entfernen Sie in der `Startup.Configure`-Methode (`Startup.cs`) der Server-App die folgenden Zeilen nach dem Aufruf von <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="41e50-187">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

  ```csharp
  app.UseBlazorFrameworkFiles();
  app.UseStaticFiles();

  app.UseRouting();

  app.UseEndpoints(endpoints =>
  {
      endpoints.MapRazorPages();
      endpoints.MapControllers();
      endpoints.MapFallbackToFile("index.html");
  });
  ```

  <span data-ttu-id="41e50-188">Fügen Sie Middleware hinzu, die Anforderungen den Client-Apps zuordnet.</span><span class="sxs-lookup"><span data-stu-id="41e50-188">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="41e50-189">Im folgenden Beispiel wird die Middleware so konfiguriert, dass Sie unter den folgenden Umständen ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="41e50-189">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="41e50-190">Der Anforderungsport ist entweder 5001 für die ursprüngliche Client-App oder 5002 für die hinzugefügte Client-App.</span><span class="sxs-lookup"><span data-stu-id="41e50-190">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="41e50-191">Der Anforderungshost ist entweder `firstapp.com` für die ursprüngliche Client-App oder `secondapp.com` für die hinzugefügte Client-App.</span><span class="sxs-lookup"><span data-stu-id="41e50-191">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="41e50-192">Das in diesem Abschnitt gezeigte Beispiel erfordert zusätzliche Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="41e50-192">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="41e50-193">Für dasZugreifen auf die Apps auf den Beispielhostdomänen: `firstapp.com` und `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="41e50-193">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="41e50-194">Zertifikate für die Client-Apps zum Aktivieren von TLS-Sicherheit (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="41e50-194">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="41e50-195">Die erforderliche Konfiguration sprengt den Rahmen dieses Artikels und ist davon abhängig, wie die Lösung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-195">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="41e50-196">Weitere Informationen finden Sie in den Artikeln zum [Hosten und Bereitstellen](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="41e50-196">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="41e50-197">Platzieren Sie den folgenden Code dort, wo zuvor Zeilen entfernt wurden:</span><span class="sxs-lookup"><span data-stu-id="41e50-197">Place the following code where the lines were removed earlier:</span></span>

  ```csharp
  app.MapWhen(ctx => ctx.Request.Host.Port == 5001 || 
      ctx.Request.Host.Equals("firstapp.com"), first =>
  {
      first.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/FirstApp" + ctx.Request.Path;
          return nxt();
      });

      first.UseBlazorFrameworkFiles("/FirstApp");
      first.UseStaticFiles();
      first.UseStaticFiles("/FirstApp");
      first.UseRouting();

      first.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/FirstApp/{*path:nonfile}", 
              "FirstApp/index.html");
      });
  });
  
  app.MapWhen(ctx => ctx.Request.Host.Port == 5002 || 
      ctx.Request.Host.Equals("secondapp.com"), second =>
  {
      second.Use((ctx, nxt) =>
      {
          ctx.Request.Path = "/SecondApp" + ctx.Request.Path;
          return nxt();
      });

      second.UseBlazorFrameworkFiles("/SecondApp");
      second.UseStaticFiles();
      second.UseStaticFiles("/SecondApp");
      second.UseRouting();

      second.UseEndpoints(endpoints =>
      {
          endpoints.MapControllers();
          endpoints.MapFallbackToFile("/SecondApp/{*path:nonfile}", 
              "SecondApp/index.html");
      });
  });
  ```

* <span data-ttu-id="41e50-198">Ersetzen Sie im Wettervorhersagecontroller (`Controllers/WeatherForecastController.cs`) der Server-App die vorhandene Route (`[Route("[controller]")]`) zu `WeatherForecastController` durch die folgenden Routen:</span><span class="sxs-lookup"><span data-stu-id="41e50-198">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="41e50-199">Die Middleware, die der `Startup.Configure`-Methode der Server-App hinzugefügt wurde, ändert eingehende Anforderungen an `/WeatherForecast` abhängig vom Port (5001/5002) oder der Domäne (`firstapp.com`/`secondapp.com`) in `/FirstApp/WeatherForecast` oder `/SecondApp/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="41e50-199">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="41e50-200">Die vorherigen Controllerrouten sind erforderlich, um Wetterdaten von der Server-App an die Client-Apps zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-200">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="41e50-201">Statische Ressourcen und Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="41e50-201">Static assets and class libraries</span></span>

<span data-ttu-id="41e50-202">Verwenden Sie die folgenden Verfahren für statische Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="41e50-202">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="41e50-203">Wenn sich die Ressource im Ordner `wwwroot` der Client-App befindet, geben Sie Pfade normal an:</span><span class="sxs-lookup"><span data-stu-id="41e50-203">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="41e50-204">Wenn sich die Ressource im Ordner `wwwroot` einer [Razor-Klassenbibliothek (RCL)](xref:blazor/components/class-libraries) befindet, verweisen Sie in der Client-App gemäß der Anleitung im [RCL-Artikel](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl) auf die statische Ressource:</span><span class="sxs-lookup"><span data-stu-id="41e50-204">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

Components provided to a client app by a class library are referenced normally. If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:

* The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.
* The component can use the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) to obtain the static assets.

The preceding approaches are demonstrated in the following examples.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="41e50-205">Auf Komponenten, die für eine Client-App durch eine Klassenbibliothek bereitgestellt werden, wird normal verwiesen.</span><span class="sxs-lookup"><span data-stu-id="41e50-205">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="41e50-206">Wenn für Komponenten Stylesheets oder JavaScript-Dateien erforderlich sind, muss die Datei `wwwroot/index.html` der Client-App die richtigen Links der statischen Ressourcen enthalten.</span><span class="sxs-lookup"><span data-stu-id="41e50-206">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="41e50-207">Diese Konzepte werden in den folgenden Beispielen gezeigt.</span><span class="sxs-lookup"><span data-stu-id="41e50-207">These approaches are demonstrated in the following examples.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="41e50-208">Fügen Sie einer der Client-Apps die folgende `Jeep`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-208">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="41e50-209">Die `Jeep`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="41e50-209">The `Jeep` component uses:</span></span>

* <span data-ttu-id="41e50-210">Ein Bild aus dem Ordner `wwwroot` der Client-App (`jeep-cj.png`).</span><span class="sxs-lookup"><span data-stu-id="41e50-210">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="41e50-211">Ein Bild aus einem Ordner `wwwroot` (`jeep-yj.png`) einer [hinzugefügten Razor Komponentenbibliothek](xref:blazor/components/class-libraries) (`JeepImage`).</span><span class="sxs-lookup"><span data-stu-id="41e50-211">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="41e50-212">Die Beispielkomponente (`Component1`) wird von der RCL-Projektvorlage automatisch erstellt, wenn die `JeepImage`-Bibliothek der Projektmappe hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-212">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

```razor
@page "/Jeep"

<h1>1979 Jeep CJ-5&trade;</h1>

<p>
    <img alt="1979 Jeep CJ-5&trade;" src="/jeep-cj.png" />
</p>

<h1>1991 Jeep YJ&trade;</h1>

<p>
    <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
</p>

<p>
    <em>Jeep CJ-5</em> and <em>Jeep YJ</em> are a trademarks of 
    <a href="https://www.fcagroup.com">Fiat Chrysler Automobiles</a>.
</p>

<JeepImage.Component1 />
```

> [!WARNING]
> <span data-ttu-id="41e50-213">Veröffentlichen Sie **keine** Bilder von Fahrzeugen, es sei denn, Sie sind der Eigentümer der Bilder.</span><span class="sxs-lookup"><span data-stu-id="41e50-213">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="41e50-214">Andernfalls riskieren Sie Urheberrechtsverletzungen.</span><span class="sxs-lookup"><span data-stu-id="41e50-214">Otherwise, you risk copyright infringement.</span></span>

<!-- HOLD for reactivation at 5.x

::: moniker range=">= aspnetcore-5.0"

The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`). To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements):

```razor
<div class="my-component">
    <Link href="_content/JeepImage/styles.css" rel="stylesheet" />

    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

An alternative to using the [`Link` component](xref:blazor/fundamentals/signalr#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file. This approach makes the stylesheet available to all of the components in the client app:

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

-->

<span data-ttu-id="41e50-215">Das Bild `jeep-yj.png`der Bibliothek kann auch der `Component1`-Komponente der Bibliothek (`Component1.razor`) hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="41e50-215">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

```razor
<div class="my-component">
    <h1>JeepImage.Component1</h1>

    <p>
        This Blazor component is defined in the <strong>JeepImage</strong> package.
    </p>

    <p>
        <img alt="1991 Jeep YJ&trade;" src="_content/JeepImage/jeep-yj.png" />
    </p>
</div>
```

<span data-ttu-id="41e50-216">Die Datei `wwwroot/index.html` der Client-App fordert das Stylesheet der Bibliothek mit dem folgenden hinzugefügten `<link>`-Tag an:</span><span class="sxs-lookup"><span data-stu-id="41e50-216">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

<!-- HOLD for reactivation at 5.x

::: moniker-end

-->

<span data-ttu-id="41e50-217">Fügen Sie der `Jeep`-Komponente in der `NavMenu`-Komponente der Client-App (`Shared/NavMenu.razor`) Navigation hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-217">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="41e50-218">Weitere Informationen zu RCLs finden Sie hier:</span><span class="sxs-lookup"><span data-stu-id="41e50-218">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="41e50-219">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="41e50-219">Standalone deployment</span></span>

<span data-ttu-id="41e50-220">Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-220">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="41e50-221">Jeder Server für statische Dateien kann die Blazor-App bedienen.</span><span class="sxs-lookup"><span data-stu-id="41e50-221">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="41e50-222">Eigenständige Bereitstellungsressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="41e50-222">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="41e50-223">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="41e50-223">Azure App Service</span></span>

<span data-ttu-id="41e50-224">Blazor WebAssembly-Apps können in Azure App Service unter Windows bereitgestellt werden. Dort wird die App auf [IIS](#iis) gehostet.</span><span class="sxs-lookup"><span data-stu-id="41e50-224">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="41e50-225">Die Bereitstellung einer eigenständigen Blazor WebAssembly-App in Azure App Service für Linux wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="41e50-225">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="41e50-226">Ein Linux-Serverimage zum Hosten der App ist derzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="41e50-226">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="41e50-227">Wir arbeiten daran, sodass dieses Szenario bald unterstützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="41e50-227">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="41e50-228">IIS</span><span class="sxs-lookup"><span data-stu-id="41e50-228">IIS</span></span>

<span data-ttu-id="41e50-229">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="41e50-229">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="41e50-230">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Erstellen einer statischen Website unter IIS).</span><span class="sxs-lookup"><span data-stu-id="41e50-230">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="41e50-231">Veröffentlichte Ressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` erstellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-231">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="41e50-232">Die Inhalte des Ordners `publish` werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="41e50-232">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="41e50-233">web.config</span><span class="sxs-lookup"><span data-stu-id="41e50-233">web.config</span></span>

<span data-ttu-id="41e50-234">Beim Veröffentlichen eines Blazor-Projekts wird eine `web.config`-Datei mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="41e50-234">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="41e50-235">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="41e50-235">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="41e50-236">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="41e50-236">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="41e50-237">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="41e50-237">`.json`: `application/json`</span></span>
  * <span data-ttu-id="41e50-238">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="41e50-238">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="41e50-239">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="41e50-239">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="41e50-240">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="41e50-240">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="41e50-241">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="41e50-241">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="41e50-242">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="41e50-242">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="41e50-243">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="41e50-243">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="41e50-244">Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (`wwwroot/index.html`) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-244">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="41e50-245">Verwenden einer benutzerdefinierten Datei web.config</span><span class="sxs-lookup"><span data-stu-id="41e50-245">Use a custom web.config</span></span>

<span data-ttu-id="41e50-246">Um eine benutzerdefinierte Datei `web.config` zu verwenden, speichern Sie die benutzerdefinierte Datei `web.config` im Stammverzeichnis des Projektordners.</span><span class="sxs-lookup"><span data-stu-id="41e50-246">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder.</span></span> <span data-ttu-id="41e50-247">Konfigurieren Sie das Projekt für die Veröffentlichung IIS-spezifischer Ressourcen mithilfe von `PublishIISAssets` in der Projektdatei der App, und veröffentlichen Sie das Projekt:</span><span class="sxs-lookup"><span data-stu-id="41e50-247">Configure the project to publish IIS-specific assets using `PublishIISAssets` in the app's project file and publish the project:</span></span>

```xml
<PropertyGroup>
  <PublishIISAssets>true</PublishIISAssets>
</PropertyGroup>
```

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="41e50-248">Installieren des URL-Rewrite-Moduls</span><span class="sxs-lookup"><span data-stu-id="41e50-248">Install the URL Rewrite Module</span></span>

<span data-ttu-id="41e50-249">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="41e50-249">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="41e50-250">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="41e50-250">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="41e50-251">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-251">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="41e50-252">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="41e50-252">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="41e50-253">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="41e50-253">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="41e50-254">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="41e50-254">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="41e50-255">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="41e50-255">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="41e50-256">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="41e50-256">Copy the installer to the server.</span></span> <span data-ttu-id="41e50-257">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="41e50-257">Run the installer.</span></span> <span data-ttu-id="41e50-258">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="41e50-258">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="41e50-259">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-259">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="41e50-260">Konfigurieren der Website</span><span class="sxs-lookup"><span data-stu-id="41e50-260">Configure the website</span></span>

<span data-ttu-id="41e50-261">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="41e50-261">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="41e50-262">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="41e50-262">The folder contains:</span></span>

* <span data-ttu-id="41e50-263">Die Datei `web.config`, die von IIS zum Konfigurieren der Website verwendet wird, einschließlich der erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="41e50-263">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="41e50-264">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="41e50-264">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="41e50-265">Hosten als IIS-untergeordnete App</span><span class="sxs-lookup"><span data-stu-id="41e50-265">Host as an IIS sub-app</span></span>

<span data-ttu-id="41e50-266">Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="41e50-266">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="41e50-267">Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.</span><span class="sxs-lookup"><span data-stu-id="41e50-267">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="41e50-268">Entfernen Sie den Handler in der veröffentlichen Datei `web.config` der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="41e50-268">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="41e50-269">Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="41e50-269">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <handlers>
          <add name="aspNetCore" ... />
        </handlers>
        <aspNetCore ... />
      </system.webServer>
    </location>
  </configuration>
  ```

<span data-ttu-id="41e50-270">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:blazor/host-and-deploy/index#app-base-path) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="41e50-270">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="41e50-271">Legen Sie den Basispfad der App in der Datei `index.html` der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-271">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="41e50-272">Brotli- und Gzip-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="41e50-272">Brotli and Gzip compression</span></span>

<span data-ttu-id="41e50-273">*Dieser Abschnitt gilt nur für eigenständige Blazor WebAssembly-Apps. Gehostete Blazor-Apps verwenden eine für ASP.NET Core-App standardmäßige Datei `web.config`, nicht die in diesem Abschnitt verknüpfte Datei.*</span><span class="sxs-lookup"><span data-stu-id="41e50-273">*This section only applies to standalone Blazor WebAssembly apps. Hosted Blazor apps use a default ASP.NET Core app `web.config` file, not the file linked in this section.*</span></span>

<span data-ttu-id="41e50-274">IIS kann über `web.config` so konfiguriert werden, dass mit Brotli oder Gzip komprimierte Blazor-Ressourcen für eigenständige Blazor WebAssembly-Apps bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-274">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets for standalone Blazor WebAssembly apps.</span></span> <span data-ttu-id="41e50-275">Eine Beispielkonfigurationsdatei finden Sie unter [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="41e50-275">For an example configuration file, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

<span data-ttu-id="41e50-276">In den folgenden Szenarien kann eine zusätzliche Konfiguration der Beispieldatei `web.config` erforderlich sein:</span><span class="sxs-lookup"><span data-stu-id="41e50-276">Additional configuration of the example `web.config` file might be required in the following scenarios:</span></span>

* <span data-ttu-id="41e50-277">Die Spezifikation der App erfordert eine der folgenden Aktionen:</span><span class="sxs-lookup"><span data-stu-id="41e50-277">The app's specification calls for either of the following:</span></span>
  * <span data-ttu-id="41e50-278">Bereitstellung komprimierter Dateien, die nicht durch die Beispieldatei `web.config` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="41e50-278">Serving compressed files that aren't configured by the example `web.config` file.</span></span>
  * <span data-ttu-id="41e50-279">Bereitstellung komprimierter Dateien, die durch die Beispieldatei `web.config` in einem nicht komprimierten Format konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="41e50-279">Serving compressed files configured by the example `web.config` file in an uncompressed format.</span></span>
* <span data-ttu-id="41e50-280">Die IIS-Konfiguration des Servers (z. B. `applicationHost.config`) stellt IIS-Standardwerte auf Serverebene bereit.</span><span class="sxs-lookup"><span data-stu-id="41e50-280">The server's IIS configuration (for example, `applicationHost.config`) provides server-level IIS defaults.</span></span> <span data-ttu-id="41e50-281">Je nach Konfiguration auf Serverebene erfordert die App möglicherweise eine andere IIS-Konfiguration als diejenige, die in der Beispieldatei `web.config` enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-281">Depending on the server-level configuration, the app might require a different IIS configuration than what the example `web.config` file contains.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="41e50-282">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="41e50-282">Troubleshooting</span></span>

<span data-ttu-id="41e50-283">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-283">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="41e50-284">Wenn das Modul nicht installiert ist, kann die Datei `web.config` nicht von IIS analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-284">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="41e50-285">Dadurch wird verhindert, dass die Konfiguration der Website vom IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-285">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="41e50-286">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="41e50-286">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="41e50-287">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="41e50-287">Azure Storage</span></span>

<span data-ttu-id="41e50-288">Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting.</span><span class="sxs-lookup"><span data-stu-id="41e50-288">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="41e50-289">Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="41e50-289">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="41e50-290">Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="41e50-290">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="41e50-291">Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="41e50-291">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="41e50-292">Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="41e50-292">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="41e50-293">Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-293">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="41e50-294">Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blob-Dienst generierte *Fehler 404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter.</span><span class="sxs-lookup"><span data-stu-id="41e50-294">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="41e50-295">Das Blob `index.html` wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.</span><span class="sxs-lookup"><span data-stu-id="41e50-295">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="41e50-296">Wenn Dateien aufgrund ungeeigneter MIME-Typen im `Content-Type`-Headern der Dateien nicht zur Laufzeit geladen werden, führen Sie eine der folgenden Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="41e50-296">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="41e50-297">Konfigurieren Sie die Tools so, dass beim Bereitstellen der Dateien die richtigen MIME-Typen (`Content-Type`-Header) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-297">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="41e50-298">Ändern Sie nach Bereitstellung der App die MIME-Typen (`Content-Type`-Header) für die Dateien.</span><span class="sxs-lookup"><span data-stu-id="41e50-298">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="41e50-299">Führen Sie im Storage-Explorer (Azure-Portal) für jede Datei folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="41e50-299">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="41e50-300">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="41e50-300">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="41e50-301">Legen Sie **ContentType-** fest, und klicken Sie auf die Schaltfläche **Speichern**</span><span class="sxs-lookup"><span data-stu-id="41e50-301">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="41e50-302">Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="41e50-302">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="41e50-303">Nginx</span><span class="sxs-lookup"><span data-stu-id="41e50-303">Nginx</span></span>

<span data-ttu-id="41e50-304">Die folgende Datei `nginx.conf` wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei `index.html` gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-304">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root      /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="41e50-305">Wenn Sie [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) für das [NGINX-Burstratenlimit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) festlegen, erfordern Blazor WebAssembly-Apps möglicherweise einen größeren `burst`-Parameterwert, um die relativ große Anzahl an Anforderungen zu erfüllen, die eine App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="41e50-305">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="41e50-306">Legen Sie den Wert zunächst auf 60 fest:</span><span class="sxs-lookup"><span data-stu-id="41e50-306">Initially, set the value to at least 60:</span></span>

```
http {
    server {
        ...

        location / {
            ...

            limit_req zone=one burst=60 nodelay;
        }
    }
}
```

<span data-ttu-id="41e50-307">Erhöhen Sie den Wert, wenn Browserentwicklertools oder Netzwerkdatenverkehrstools angeben, dass für Anforderungen der Statuscode *503 – Dienst nicht verfügbar* zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-307">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="41e50-308">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="41e50-308">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="apache"></a><span data-ttu-id="41e50-309">Apache</span><span class="sxs-lookup"><span data-stu-id="41e50-309">Apache</span></span>

<span data-ttu-id="41e50-310">So stellen Sie eine Blazor WebAssembly-App für CentOS 7 oder höher bereit:</span><span class="sxs-lookup"><span data-stu-id="41e50-310">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="41e50-311">Erstellen Sie die Apache-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="41e50-311">Create the Apache configuration file.</span></span> <span data-ttu-id="41e50-312">Das folgenden Beispiel zeigt eine vereinfachte Konfigurationsdatei (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="41e50-312">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

   ```
   <VirtualHost *:80>
       ServerName www.example.com
       ServerAlias *.example.com

       DocumentRoot "/var/www/blazorapp"
       ErrorDocument 404 /index.html

       AddType application/wasm .wasm
       AddType application/octet-stream .dll
   
       <Directory "/var/www/blazorapp">
           Options -Indexes
           AllowOverride None
       </Directory>

       <IfModule mod_deflate.c>
           AddOutputFilterByType DEFLATE text/css
           AddOutputFilterByType DEFLATE application/javascript
           AddOutputFilterByType DEFLATE text/html
           AddOutputFilterByType DEFLATE application/octet-stream
           AddOutputFilterByType DEFLATE application/wasm
           <IfModule mod_setenvif.c>
           BrowserMatch ^Mozilla/4 gzip-only-text/html
           BrowserMatch ^Mozilla/4.0[678] no-gzip
           BrowserMatch bMSIE !no-gzip !gzip-only-text/html
       </IfModule>
       </IfModule>

       ErrorLog /var/log/httpd/blazorapp-error.log
       CustomLog /var/log/httpd/blazorapp-access.log common
   </VirtualHost>
   ```

1. <span data-ttu-id="41e50-313">Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-313">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="41e50-314">Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="41e50-314">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="41e50-315">Starten Sie den Apache-Dienst neu.</span><span class="sxs-lookup"><span data-stu-id="41e50-315">Restart the Apache service.</span></span>

<span data-ttu-id="41e50-316">Weitere Informationen finden Sie unter [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="41e50-316">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="41e50-317">GitHub-Seiten</span><span class="sxs-lookup"><span data-stu-id="41e50-317">GitHub Pages</span></span>

<span data-ttu-id="41e50-318">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine `wwwroot/404.html`-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite `index.html` verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-318">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="41e50-319">Ein Beispiel finden Sie im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="41e50-319">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="41e50-320">[Livesite](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="41e50-320">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="41e50-321">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, aktualisieren Sie das `<base>`-Tag in `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="41e50-321">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="41e50-322">Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Schrägstrich fest (z. B. `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="41e50-322">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="41e50-323">Im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) wird die Basis `href` bei der Veröffentlichung durch die [ Konfigurationsdatei `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="41e50-323">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="41e50-324">Das GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) befindet sich nicht im Besitz der .NET Foundation oder von Microsoft und wird auch nicht von diesen verwaltet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="41e50-324">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="41e50-325">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="41e50-325">Host configuration values</span></span>

<span data-ttu-id="41e50-326">[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Runtime in der Entwicklungsumgebung akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-326">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="41e50-327">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="41e50-327">Content root</span></span>

<span data-ttu-id="41e50-328">Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält.</span><span class="sxs-lookup"><span data-stu-id="41e50-328">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="41e50-329">In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.</span><span class="sxs-lookup"><span data-stu-id="41e50-329">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="41e50-330">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-330">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="41e50-331">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="41e50-331">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="41e50-332">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-332">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="41e50-333">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-333">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="41e50-334">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="41e50-334">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="41e50-335">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e50-335">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="41e50-336">Basispfad</span><span class="sxs-lookup"><span data-stu-id="41e50-336">Path base</span></span>

<span data-ttu-id="41e50-337">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="41e50-337">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="41e50-338">In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App.</span><span class="sxs-lookup"><span data-stu-id="41e50-338">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="41e50-339">Weitere Informationen finden Sie unter [Basispfad einer App](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="41e50-339">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="41e50-340">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-340">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="41e50-341">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-341">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="41e50-342">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-342">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="41e50-343">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="41e50-343">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="41e50-344">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-344">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="41e50-345">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-345">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="41e50-346">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="41e50-346">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="41e50-347">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e50-347">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="41e50-348">URLs</span><span class="sxs-lookup"><span data-stu-id="41e50-348">URLs</span></span>

<span data-ttu-id="41e50-349">Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.</span><span class="sxs-lookup"><span data-stu-id="41e50-349">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="41e50-350">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-350">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="41e50-351">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="41e50-351">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="41e50-352">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-352">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="41e50-353">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-353">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="41e50-354">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="41e50-354">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="41e50-355">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="41e50-355">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="41e50-356">Konfigurieren des Trimmers</span><span class="sxs-lookup"><span data-stu-id="41e50-356">Configure the Trimmer</span></span>

<span data-ttu-id="41e50-357">Blazor führt auf jedem Releasebuild eine IL-Kürzung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="41e50-357">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="41e50-358">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="41e50-358">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="41e50-359">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="41e50-359">Configure the Linker</span></span>

<span data-ttu-id="41e50-360">Blazor führt auf jedem Releasebuild eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="41e50-360">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="41e50-361">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="41e50-361">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="41e50-362">Benutzerdefiniertes Laden von Startressourcen</span><span class="sxs-lookup"><span data-stu-id="41e50-362">Custom boot resource loading</span></span>

<span data-ttu-id="41e50-363">Eine Blazor WebAssembly-App kann mit der Funktion `loadBootResource` initialisiert werden, um den integrierten Mechanismus zum Laden von Startressourcen zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="41e50-363">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="41e50-364">Verwenden Sie `loadBootResource` für die folgenden Szenarien:</span><span class="sxs-lookup"><span data-stu-id="41e50-364">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="41e50-365">Benutzern erlauben, statische Ressourcen wie Zeitzonendaten oder `dotnet.wasm` aus einem CDN zu laden.</span><span class="sxs-lookup"><span data-stu-id="41e50-365">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="41e50-366">Komprimierte Assemblys über eine HTTP-Anforderung laden und auf dem Client für Hosts dekomprimieren, die das Abrufen komprimierter Inhalte vom Server nicht unterstützen.</span><span class="sxs-lookup"><span data-stu-id="41e50-366">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="41e50-367">Ressourcen per Alias an einen anderen Namen umleiten, indem jede `fetch`-Anforderung an einen neuen Namen umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-367">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="41e50-368">`loadBootResource`-Parameter sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="41e50-368">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="41e50-369">Parameter</span><span class="sxs-lookup"><span data-stu-id="41e50-369">Parameter</span></span>    | <span data-ttu-id="41e50-370">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="41e50-370">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="41e50-371">Der Typ der Ressource.</span><span class="sxs-lookup"><span data-stu-id="41e50-371">The type of the resource.</span></span> <span data-ttu-id="41e50-372">Zulässige Typen: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="41e50-372">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="41e50-373">Der Name der Ressource.</span><span class="sxs-lookup"><span data-stu-id="41e50-373">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="41e50-374">Der relative oder absolute URI der Ressource.</span><span class="sxs-lookup"><span data-stu-id="41e50-374">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="41e50-375">Die Integritätszeichenfolge, die den erwarteten Inhalt in der Antwort darstellt.</span><span class="sxs-lookup"><span data-stu-id="41e50-375">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="41e50-376">`loadBootResource` gibt eines der folgenden Elemente zurück, um den Ladevorgang außer Kraft zu setzen:</span><span class="sxs-lookup"><span data-stu-id="41e50-376">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="41e50-377">URI-Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="41e50-377">URI string.</span></span> <span data-ttu-id="41e50-378">Im Beispiel unten (`wwwroot/index.html`) werden die folgenden Dateien von einem CDN unter `https://my-awesome-cdn.com/` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="41e50-378">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="41e50-379">Zeitzonendaten</span><span class="sxs-lookup"><span data-stu-id="41e50-379">Timezone data</span></span>

  ```html
  ...

  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        console.log(`Loading: '${type}', '${name}', '${defaultUri}', '${integrity}'`);
        switch (type) {
          case 'dotnetjs':
          case 'dotnetwasm':
          case 'timezonedata':
            return `https://my-awesome-cdn.com/blazorwebassembly/3.2.0/${name}`;
        }
      }
    });
  </script>
  ```

* <span data-ttu-id="41e50-380">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="41e50-380">`Promise<Response>`.</span></span> <span data-ttu-id="41e50-381">Übergeben Sie den Parameter `integrity` in einem Header, um das Standardverhalten bei der Integritätsprüfung beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="41e50-381">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="41e50-382">Das folgende Beispiel (`wwwroot/index.html`) fügt einen benutzerdefinierten HTTP-Header den ausgehenden Anforderungen hinzu und übergibt den Parameter `integrity` an den Aufruf von `fetch`:</span><span class="sxs-lookup"><span data-stu-id="41e50-382">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
  ```html
  <script src="_framework/blazor.webassembly.js" autostart="false"></script>
  <script>
    Blazor.start({
      loadBootResource: function (type, name, defaultUri, integrity) {
        return fetch(defaultUri, { 
          cache: 'no-cache',
          integrity: integrity,
          headers: { 'MyCustomHeader': 'My custom value' }
        });
      }
    });
  </script>
  ```

* <span data-ttu-id="41e50-383">`null`/`undefined`, was zum standardmäßigen Ladeverhalten führt.</span><span class="sxs-lookup"><span data-stu-id="41e50-383">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="41e50-384">Externe Quellen müssen die erforderlichen CORS-Header für Browser zurückgeben, um das ursprungsübergreifende Laden von Ressourcen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="41e50-384">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="41e50-385">CDNs stellen in der Regel die erforderlichen Header standardmäßig zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="41e50-385">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="41e50-386">Sie müssen nur für benutzerdefinierte Verhaltensmuster Typen angeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-386">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="41e50-387">Nicht mit `loadBootResource` angegebene Typen werden vom Framework entsprechend ihres Standardladeverhaltens geladen.</span><span class="sxs-lookup"><span data-stu-id="41e50-387">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="41e50-388">Ändern der Dateinamenerweiterung von DLL-Dateien</span><span class="sxs-lookup"><span data-stu-id="41e50-388">Change the filename extension of DLL files</span></span>

<span data-ttu-id="41e50-389">Falls Sie die Dateinamenerweiterungen der veröffentlichten `.dll`-Dateien der App ändern müssen, befolgen Sie die Anleitung in diesem Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="41e50-389">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="41e50-390">Verwenden Sie nach Veröffentlichen der App ein Shellskript oder eine DevOps-Buildpipeline, um `.dll`-Dateien umzubenennen und eine andere Dateierweiterung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="41e50-390">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="41e50-391">Verwenden Sie die `.dll`-Dateien im `wwwroot`-Verzeichnis der veröffentlichten Ausgabe der App (z. B. `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="41e50-391">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="41e50-392">In den folgenden Beispielen werden `.dll`-Dateien so umbenannt, dass die Dateierweiterung `.bin` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-392">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="41e50-393">Unter Windows:</span><span class="sxs-lookup"><span data-stu-id="41e50-393">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="41e50-394">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-394">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="41e50-395">Unter Linux oder macOS:</span><span class="sxs-lookup"><span data-stu-id="41e50-395">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="41e50-396">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-396">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="41e50-397">Um eine andere Dateierweiterung als `.bin` zu verwenden, ersetzen Sie `.bin` in den vorherigen Befehlen.</span><span class="sxs-lookup"><span data-stu-id="41e50-397">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="41e50-398">Um die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br` zu verarbeiten, wenden Sie einen der folgenden Ansätze an:</span><span class="sxs-lookup"><span data-stu-id="41e50-398">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="41e50-399">Entfernen Sie die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="41e50-399">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="41e50-400">Bei diesem Ansatz ist die Komprimierung deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="41e50-400">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="41e50-401">Komprimieren Sie die aktualisierte Datei `blazor.boot.json` erneut.</span><span class="sxs-lookup"><span data-stu-id="41e50-401">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="41e50-402">Die vorherige Anleitung gilt auch, wenn Service Worker-Assets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="41e50-402">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="41e50-403">Entfernen Sie die Dateien `wwwroot/service-worker-assets.js.br` und `wwwroot/service-worker-assets.js.gz`, oder komprimieren Sie diese erneut.</span><span class="sxs-lookup"><span data-stu-id="41e50-403">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="41e50-404">Andernfalls treten bei Dateiintegritätsprüfungen im Browser Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="41e50-404">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="41e50-405">Im folgenden Windows-Beispiel wird ein PowerShell-Skript verwendet, das sich im Stammverzeichnis des Projekts befindet.</span><span class="sxs-lookup"><span data-stu-id="41e50-405">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="41e50-406">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="41e50-406">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="41e50-407">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-407">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="41e50-408">In der Projektdatei wird das Skript nach Veröffentlichung der App ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="41e50-408">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="41e50-409">Für das Umbenennen und verzögerte Laden derselben Assemblys finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> eine Anleitung.</span><span class="sxs-lookup"><span data-stu-id="41e50-409">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>

## <a name="resolve-integrity-check-failures"></a><span data-ttu-id="41e50-410">Lösen von Fehlern bei der Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="41e50-410">Resolve integrity check failures</span></span>

<span data-ttu-id="41e50-411">Wenn Blazor WebAssembly die Startdateien einer App herunterlädt, wird der Browser angewiesen, Integritätsprüfungen der Antworten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="41e50-411">When Blazor WebAssembly downloads an app's startup files, it instructs the browser to perform integrity checks on the responses.</span></span> <span data-ttu-id="41e50-412">Informationen in der `blazor.boot.json`-Datei werden verwendet, um die erwarteten SHA-256-Hashwerte für `.dll`-, `.wasm`- und andere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-412">It uses information in the `blazor.boot.json` file to specify the expected SHA-256 hash values for `.dll`, `.wasm`, and other files.</span></span> <span data-ttu-id="41e50-413">Dies ist aus folgenden Gründen vorteilhaft:</span><span class="sxs-lookup"><span data-stu-id="41e50-413">This is beneficial for the following reasons:</span></span>

* <span data-ttu-id="41e50-414">Es wird sichergestellt, dass kein inkonsistenter Satz von Dateien geladen wird, wenn z. B. eine neue Bereitstellung auf den Webserver angewendet wird, während der Benutzer die Anwendungsdateien herunterlädt.</span><span class="sxs-lookup"><span data-stu-id="41e50-414">It ensures you don't risk loading an inconsistent set of files, for example if a new deployment is applied to your web server while the user is in the process of downloading the application files.</span></span> <span data-ttu-id="41e50-415">Inkonsistente Dateien könnten zu undefiniertem Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="41e50-415">Inconsistent files could lead to undefined behavior.</span></span>
* <span data-ttu-id="41e50-416">Es wird sichergestellt, dass der Browser des Benutzers nie inkonsistente oder ungültige Antworten zwischenspeichert. Dies könnte verhindern, dass er die App startet, auch wenn er die Seite manuell aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="41e50-416">It ensures the user's browser never caches inconsistent or invalid responses, which could prevent them from starting the app even if they manually refresh the page.</span></span>
* <span data-ttu-id="41e50-417">Es wird dadurch sicher, die Antworten zwischenzuspeichern und nicht einmal auf serverseitige Änderungen zu prüfen, bis die erwarteten SHA-256-Hashwerte selbst geändert werden. Nachfolgende Seitenladevorgänge sind so mit weniger Anforderungen verbunden und werden viel schneller abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="41e50-417">It makes it safe to cache the responses and not even check for server-side changes until the expected SHA-256 hashes themselves change, so subsequent page loads involve fewer requests and complete much faster.</span></span>

<span data-ttu-id="41e50-418">Wenn Ihr Webserver Antworten zurückgibt, die nicht den erwarteten SHA-256-Hashwerten entsprechen, wird in der Entwicklerkonsole des Browsers eine Fehlermeldung angezeigt, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="41e50-418">If your web server returns responses that don't match the expected SHA-256 hashes, you will see an error similar to the following appear in the browser's developer console:</span></span>

> <span data-ttu-id="41e50-419">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazor App.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span><span class="sxs-lookup"><span data-stu-id="41e50-419">Failed to find a valid digest in the 'integrity' attribute for resource 'https://myapp.example.com/\_framework/MyBlazorApp.dll' with computed SHA-256 integrity 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='.</span></span> <span data-ttu-id="41e50-420">The resource has been blocked. (Fehler beim Ermitteln eines gültigen Hashs im 'integrity'-Attribut für die Ressource 'https://myapp.example.com/_framework/MyBlazorApp.dll' mit der berechneten SHA-256-Integrität 'IIa70iwvmEg5WiDV17OpQ5eCztNYqL186J56852RpJY='. Die Ressource wurde blockiert.)</span><span class="sxs-lookup"><span data-stu-id="41e50-420">The resource has been blocked.</span></span>

<span data-ttu-id="41e50-421">In den meisten Fällen liegt hier *kein* Problem mit der Integritätsprüfung selbst vor.</span><span class="sxs-lookup"><span data-stu-id="41e50-421">In most cases, this is *not* a problem with integrity checking itself.</span></span> <span data-ttu-id="41e50-422">Stattdessen bedeutet dies, dass ein anderes Problem vorliegt, und die Integritätsüberprüfung warnt Sie vor diesem anderen Problem.</span><span class="sxs-lookup"><span data-stu-id="41e50-422">Instead, it means there is some other problem, and the integrity check is warning you about that other problem.</span></span>

### <a name="diagnosing-integrity-problems"></a><span data-ttu-id="41e50-423">Diagnostizieren von Integritätsproblemen</span><span class="sxs-lookup"><span data-stu-id="41e50-423">Diagnosing integrity problems</span></span>

<span data-ttu-id="41e50-424">Wenn eine App erstellt wird, beschreibt das generierte `blazor.boot.json`-Manifest die SHA-256-Hashwerte ihrer Startressourcen (z. B. `.dll`, `.wasm` und andere Dateien) zum Zeitpunkt der Erstellung der Buildausgabe.</span><span class="sxs-lookup"><span data-stu-id="41e50-424">When an app is built, the generated `blazor.boot.json` manifest describes the SHA-256 hashes of your boot resources (for example, `.dll`, `.wasm`, and other files) at the time that the build output is produced.</span></span> <span data-ttu-id="41e50-425">Die Integritätsprüfung wird durchgeführt, solange die SHA-256-Hashwerte in `blazor.boot.json` mit den Dateien, die an den Browser übermittelt werden, übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="41e50-425">The integrity check passes as long as the SHA-256 hashes in `blazor.boot.json` match the files delivered to the browser.</span></span>

<span data-ttu-id="41e50-426">Häufige Ursachen für Fehler sind hier:</span><span class="sxs-lookup"><span data-stu-id="41e50-426">Common reasons why this fails are:</span></span>

 * <span data-ttu-id="41e50-427">Die Antwort des Webservers ist eine Fehlermeldung (z. B. *404 – nicht gefunden* oder *500 – Interner Serverfehler*) anstelle der vom Browser angeforderten Datei.</span><span class="sxs-lookup"><span data-stu-id="41e50-427">The web server's response is an error (for example, a *404 - Not Found* or a *500 - Internal Server Error*) instead of the file the browser requested.</span></span> <span data-ttu-id="41e50-428">Dies wird vom Browser als Fehler bei der Integritätsprüfung gemeldet, nicht als Antwortfehler.</span><span class="sxs-lookup"><span data-stu-id="41e50-428">This is reported by the browser as an integrity check failure and not as a response failure.</span></span>
 * <span data-ttu-id="41e50-429">Der Inhalt der Dateien wurde zwischen dem Build und der Übermittlung der Dateien an den Browser geändert.</span><span class="sxs-lookup"><span data-stu-id="41e50-429">Something has changed the contents of the files between the build and delivery of the files to the browser.</span></span> <span data-ttu-id="41e50-430">Dies kann vorkommen:</span><span class="sxs-lookup"><span data-stu-id="41e50-430">This might happen:</span></span>
   * <span data-ttu-id="41e50-431">Wenn Sie oder Buildtools die Buildausgabe manuell ändern.</span><span class="sxs-lookup"><span data-stu-id="41e50-431">If you or build tools manually modify the build output.</span></span>
   * <span data-ttu-id="41e50-432">Wenn ein Aspekt des Bereitstellungsprozesses die Dateien geändert hat.</span><span class="sxs-lookup"><span data-stu-id="41e50-432">If some aspect of the deployment process modified the files.</span></span> <span data-ttu-id="41e50-433">Wenn Sie z. B. einen Bereitstellungsmechanismus auf Git-Basis verwenden, bedenken Sie, dass Git die Zeilenenden im Windows-Format transparent in UNIX-Zeilenenden konvertiert, wenn Sie Dateien unter Windows übertragen und unter Linux auschecken.</span><span class="sxs-lookup"><span data-stu-id="41e50-433">For example if you use a Git-based deployment mechanism, bear in mind that Git transparently converts Windows-style line endings to Unix-style line endings if you commit files on Windows and check them out on Linux.</span></span> <span data-ttu-id="41e50-434">Durch das Ändern von Dateizeilenenden werden die SHA-256-Hashwerte geändert.</span><span class="sxs-lookup"><span data-stu-id="41e50-434">Changing file line endings change the SHA-256 hashes.</span></span> <span data-ttu-id="41e50-435">Um dieses Problem zu vermeiden, sollten Sie die [Verwendung von `.gitattributes` erwägen, um Buildartefakte als `binary`-Dateien](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes) zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="41e50-435">To avoid this problem, consider [using `.gitattributes` to treat build artifacts as `binary` files](https://git-scm.com/book/en/v2/Customizing-Git-Git-Attributes).</span></span>
   * <span data-ttu-id="41e50-436">Der Webserver ändert Dateiinhalte im Rahmen ihrer Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="41e50-436">The web server modifies the file contents as part of serving them.</span></span> <span data-ttu-id="41e50-437">Einige Content Distribution Networks (CDNs) versuchen z. B. automatisch, HTML zu [minimieren](xref:client-side/bundling-and-minification#minification) und damit zu ändern.</span><span class="sxs-lookup"><span data-stu-id="41e50-437">For example, some content distribution networks (CDNs) automatically attempt to [minify](xref:client-side/bundling-and-minification#minification) HTML, thereby modifying it.</span></span> <span data-ttu-id="41e50-438">Sie müssen diese Features möglicherweise deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-438">You may need to disable such features.</span></span>

<span data-ttu-id="41e50-439">So diagnostizieren Sie, was davon in Ihrem Fall zutrifft:</span><span class="sxs-lookup"><span data-stu-id="41e50-439">To diagnose which of these applies in your case:</span></span>

 1. <span data-ttu-id="41e50-440">Beachten Sie, welche Datei den Fehler auslöst, indem Sie die Fehlermeldung lesen.</span><span class="sxs-lookup"><span data-stu-id="41e50-440">Note which file is triggering the error by reading the error message.</span></span>
 1. <span data-ttu-id="41e50-441">Öffnen Sie die Entwicklertools Ihres Browsers, und sehen Sie sich die Registerkarte *Netzwerk* an. Laden Sie ggf. die Seite erneut, um die Liste der Anforderungen und Antworten anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="41e50-441">Open your browser's developer tools and look in the *Network* tab. If necessary, reload the page to see the list of requests and responses.</span></span> <span data-ttu-id="41e50-442">Suchen Sie die Datei, die den in dieser Liste ausgeführten Fehler auslöst.</span><span class="sxs-lookup"><span data-stu-id="41e50-442">Find the file that is triggering the error in that list.</span></span>
 1. <span data-ttu-id="41e50-443">Überprüfen Sie den HTTP-Statuscode in der Antwort.</span><span class="sxs-lookup"><span data-stu-id="41e50-443">Check the HTTP status code in the response.</span></span> <span data-ttu-id="41e50-444">Wenn der Server etwas anderes als *200 – OK* (oder einen anderen 2xx-Statuscode) zurückgibt, müssen Sie ein serverseitiges Problem diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-444">If the server returns anything other than *200 - OK* (or another 2xx status code), then you have a server-side problem to diagnose.</span></span> <span data-ttu-id="41e50-445">Der Statuscode 403 bedeutet beispielsweise, dass ein Autorisierungsproblem vorliegt, während der Statuscode 500 bedeutet, dass ein nicht angegebener Serverfehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-445">For example, status code 403 means there's an authorization problem, whereas status code 500 means the server is failing in an unspecified manner.</span></span> <span data-ttu-id="41e50-446">Untersuchen Sie serverseitige Protokolle, um die App zu diagnostizieren und zu korrigieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-446">Consult server-side logs to diagnose and fix the app.</span></span>
 1. <span data-ttu-id="41e50-447">Wenn der Statuscode für die Ressource *200 – OK* lautet, sehen Sie sich den Antwortinhalt in den Entwicklertools des Browsers an, und überprüfen Sie, ob der Inhalt mit den erwarteten Daten übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="41e50-447">If the status code is *200 - OK* for the resource, look at the response content in browser's developer tools and check that the content matches up with the data expected.</span></span> <span data-ttu-id="41e50-448">Ein häufiges Problem ist beispielsweise die falsche Konfiguration des Routings, sodass Anforderungen Ihre `index.html`-Daten auch für andere Dateien zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="41e50-448">For example, a common problem is to misconfigure routing so that requests return your `index.html` data even for other files.</span></span> <span data-ttu-id="41e50-449">Stellen Sie sicher, dass Antworten auf `.wasm`-Anforderungen WebAssembly-Binärdateien und Antworten auf `.dll`-Anforderungen .NET-Assembly-Binärdateien sind.</span><span class="sxs-lookup"><span data-stu-id="41e50-449">Make sure that responses to `.wasm` requests are WebAssembly binaries and that responses to `.dll` requests are .NET assembly binaries.</span></span> <span data-ttu-id="41e50-450">Wenn dies nicht der Fall ist, müssen Sie ein serverseitiges Routingproblem diagnostizieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-450">If not, you have a server-side routing problem to diagnose.</span></span>
 1. <span data-ttu-id="41e50-451">Überprüfen Sie die veröffentlichte und bereitgestellte Ausgabe der App mithilfe des [PowerShell-Skripts für die Behebung von Problemen bei der Integrität](#troubleshoot-integrity-powershell-script).</span><span class="sxs-lookup"><span data-stu-id="41e50-451">Seek to validate the app's published and deployed output with the [Troubleshoot integrity PowerShell script](#troubleshoot-integrity-powershell-script).</span></span>

<span data-ttu-id="41e50-452">Wenn Sie sich vergewissert haben, dass der Server überzeugend korrekte Daten zurückgibt, muss irgendetwas anderes zwischen Build und Übermittlung der Datei den Inhalt ändern.</span><span class="sxs-lookup"><span data-stu-id="41e50-452">If you confirm that the server is returning plausibly correct data, there must be something else modifying the contents in between build and delivery of the file.</span></span> <span data-ttu-id="41e50-453">So ermitteln Sie dies:</span><span class="sxs-lookup"><span data-stu-id="41e50-453">To investigate this:</span></span>

 * <span data-ttu-id="41e50-454">Untersuchen Sie die Buildtoolkette und den Bereitstellungsmechanismus darauf hin, ob sie Dateien nach dem Erstellen ändern.</span><span class="sxs-lookup"><span data-stu-id="41e50-454">Examine the build toolchain and deployment mechanism in case they're modifying files after the files are built.</span></span> <span data-ttu-id="41e50-455">Dies ist beispielsweise der Fall, wenn Git wie zuvor beschrieben Dateizeilenenden transformiert.</span><span class="sxs-lookup"><span data-stu-id="41e50-455">An example of this is when Git transforms file line endings, as described earlier.</span></span>
 * <span data-ttu-id="41e50-456">Untersuchen Sie den Webserver oder die CDN-Konfiguration darauf hin, ob sie so eingerichtet sind, dass Antworten dynamisch geändert werden (z. B. der Versuch, HTML zu minimieren).</span><span class="sxs-lookup"><span data-stu-id="41e50-456">Examine the web server or CDN configuration in case they're set up to modify responses dynamically (for example, trying to minify HTML).</span></span> <span data-ttu-id="41e50-457">Es ist in Ordnung, dass der Webserver die HTTP-Komprimierung implementiert (z. B. Rückgabe von `content-encoding: br` oder `content-encoding: gzip`), da dies das Ergebnis nach der Dekomprimierung nicht beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="41e50-457">It's fine for the web server to implement HTTP compression (for example, returning `content-encoding: br` or `content-encoding: gzip`), since this doesn't affect the result after decompression.</span></span> <span data-ttu-id="41e50-458">Es ist jedoch *nicht* in Ordnung, dass der Webserver die nicht komprimierten Daten ändert.</span><span class="sxs-lookup"><span data-stu-id="41e50-458">However, it's *not* fine for the web server to modify the uncompressed data.</span></span>

### <a name="troubleshoot-integrity-powershell-script"></a><span data-ttu-id="41e50-459">PowerShell-Skript für die Behebung von Problemen bei der Integrität</span><span class="sxs-lookup"><span data-stu-id="41e50-459">Troubleshoot integrity PowerShell script</span></span>

<span data-ttu-id="41e50-460">Verwenden Sie das PowerShell-Skript [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) zum Überprüfen einer veröffentlichten und bereitgestellten Blazor-App.</span><span class="sxs-lookup"><span data-stu-id="41e50-460">Use the [`integrity.ps1`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/integrity.ps1?raw=true) PowerShell script to validate a published and deployed Blazor app.</span></span> <span data-ttu-id="41e50-461">Das Skript wird als Startpunkt zur Behebung von Integritätsproblemen mit der App bereitgestellt, die vom Blazor-Framework nicht identifiziert werden können.</span><span class="sxs-lookup"><span data-stu-id="41e50-461">The script is provided as a starting point when the app has integrity issues that the Blazor framework can't identify.</span></span> <span data-ttu-id="41e50-462">Möglicherweise müssen Sie das Skript auf Ihre Apps anpassen.</span><span class="sxs-lookup"><span data-stu-id="41e50-462">Customization of the script might be required for your apps.</span></span>

<span data-ttu-id="41e50-463">Das Skript überprüft die Dateien im Ordner `publish` sowie die von der bereitgestellten App heruntergeladenen Dateien, um Probleme in den verschiedenen Manifesten zu erkennen, die Integritätshashes enthalten.</span><span class="sxs-lookup"><span data-stu-id="41e50-463">The script checks the files in the `publish` folder and downloaded from the deployed app to detect issues in the different manifests that contain integrity hashes.</span></span> <span data-ttu-id="41e50-464">Mit diesen Prüfungen sollten sich die häufigsten Probleme erkennen lassen:</span><span class="sxs-lookup"><span data-stu-id="41e50-464">These checks should detect the most common problems:</span></span>

* <span data-ttu-id="41e50-465">Sie haben eine Datei in der veröffentlichten Ausgabe geändert, ohne es zu bemerken.</span><span class="sxs-lookup"><span data-stu-id="41e50-465">You modified a file in the published output without realizing it.</span></span>
* <span data-ttu-id="41e50-466">Die App wurde nicht ordnungsgemäß im Bereitstellungsziel bereitgestellt, oder etwas hat sich in der Umgebung des Bereitstellungsziels geändert.</span><span class="sxs-lookup"><span data-stu-id="41e50-466">The app wasn't correctly deployed to the deployment target, or something changed within the deployment target's environment.</span></span>
* <span data-ttu-id="41e50-467">Es gibt Unterschiede zwischen der bereitgestellten App und der Ausgabe der Veröffentlichung der App.</span><span class="sxs-lookup"><span data-stu-id="41e50-467">There are differences between the deployed app and the output from publishing the app.</span></span>

<span data-ttu-id="41e50-468">Rufen Sie das Skript mit dem folgenden Befehl in einer PowerShell-Befehlsshell auf:</span><span class="sxs-lookup"><span data-stu-id="41e50-468">Invoke the script with the following command in a PowerShell command shell:</span></span>

```powershell
.\integrity.ps1 {BASE URL} {PUBLISH OUTPUT FOLDER}
```

<span data-ttu-id="41e50-469">Platzhalter:</span><span class="sxs-lookup"><span data-stu-id="41e50-469">Placeholders:</span></span>

* <span data-ttu-id="41e50-470">`{BASE URL}`: Die URL der bereitgestellten App.</span><span class="sxs-lookup"><span data-stu-id="41e50-470">`{BASE URL}`: The URL of the deployed app.</span></span>
* <span data-ttu-id="41e50-471">`{PUBLISH OUTPUT FOLDER}`: Der Pfad zum `publish`-Ordner der App oder zu dem Speicherort, in dem die App für die Bereitstellung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="41e50-471">`{PUBLISH OUTPUT FOLDER}`: The path to the app's `publish` folder or location where the app is published for deployment.</span></span>

> [!NOTE]
> <span data-ttu-id="41e50-472">Wenn Sie das GitHub-Repository `dotnet/AspNetCore.Docs` in einem System klonen möchten, das den Virusscanner [Bitdefender](https://www.bitdefender.com) verwendet, fügen Sie in Bitdefender eine Ausnahme für das `integrity.ps1`-Skript hinzu.</span><span class="sxs-lookup"><span data-stu-id="41e50-472">To clone the `dotnet/AspNetCore.Docs` GitHub repository to a system that uses the [Bitdefender](https://www.bitdefender.com) virus scanner, add an exception to Bitdefender for the `integrity.ps1` script.</span></span> <span data-ttu-id="41e50-473">Fügen Sie Bitdefender die Ausnahme hinzu, bevor Sie das Repository klonen, um zu vermeiden, dass das Skript vom Virusscanner unter Quarantäne gestellt wird.</span><span class="sxs-lookup"><span data-stu-id="41e50-473">Add the exception to Bitdefender before cloning the repo to avoid having the script quarantined by the virus scanner.</span></span> <span data-ttu-id="41e50-474">Das folgende Beispiel enthält einen typischen Pfad zum Skript für das geklonte Repository in einem Windows-System.</span><span class="sxs-lookup"><span data-stu-id="41e50-474">The following example is a typical path to the script for the cloned repo on a Windows system.</span></span> <span data-ttu-id="41e50-475">Passen Sie den Pfad nach Bedarf an.</span><span class="sxs-lookup"><span data-stu-id="41e50-475">Adjust the path as needed.</span></span> <span data-ttu-id="41e50-476">Der Platzhalter `{USER}` steht für das Pfadsegment des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="41e50-476">The placeholder `{USER}` is the user's path segment.</span></span>
>
> ```
> C:\Users\{USER}\Documents\GitHub\AspNetCore.Docs\aspnetcore\blazor\host-and-deploy\webassembly\_samples\integrity.ps1
> ```

### <a name="disable-integrity-checking-for-non-pwa-apps"></a><span data-ttu-id="41e50-477">Deaktivieren der Integritätsprüfung für Nicht-PWA-Apps</span><span class="sxs-lookup"><span data-stu-id="41e50-477">Disable integrity checking for non-PWA apps</span></span>

<span data-ttu-id="41e50-478">Deaktivieren Sie die Integritätsprüfung in den meisten Fällen nicht.</span><span class="sxs-lookup"><span data-stu-id="41e50-478">In most cases, don't disable integrity checking.</span></span> <span data-ttu-id="41e50-479">Das Deaktivieren der Integritätsprüfung löst nicht das zugrunde liegende Problem, das die unerwarteten Antworten verursacht hat, und führt zu einem Verlust der zuvor aufgeführten Vorteile.</span><span class="sxs-lookup"><span data-stu-id="41e50-479">Disabling integrity checking doesn't solve the underlying problem that has caused the unexpected responses and results in losing the benefits listed earlier.</span></span>

<span data-ttu-id="41e50-480">Möglicherweise gibt es Fälle, in denen Sie sich nicht darauf verlassen können, dass der Webserver konsistente Antworten zurückgibt, und Sie haben keine andere Wahl, als Integritätsprüfungen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="41e50-480">There may be cases where the web server can't be relied upon to return consistent responses, and you have no choice but to disable integrity checks.</span></span> <span data-ttu-id="41e50-481">Fügen Sie zum Deaktivieren von Integritätsprüfungen einer Eigenschaftengruppe in der `.csproj`-Datei des Blazor WebAssembly-Projekts Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="41e50-481">To disable integrity checks, add the following to a property group in the Blazor WebAssembly project's `.csproj` file:</span></span>

```xml
<BlazorCacheBootResources>false</BlazorCacheBootResources>
```

<span data-ttu-id="41e50-482">`BlazorCacheBootResources` deaktiviert auch das Standardverhalten von Blazor beim Zwischenspeichern der `.dll`-, `.wasm`- und anderer Dateien auf der Grundlage ihrer SHA-256-Hashwerte, da die Eigenschaft angibt, dass die Richtigkeit der SHA-256-Hashwerte nicht verlässlich ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-482">`BlazorCacheBootResources` also disables Blazor's default behavior of caching the `.dll`, `.wasm`, and other files based on their SHA-256 hashes because the property indicates that the SHA-256 hashes can't be relied upon for correctness.</span></span> <span data-ttu-id="41e50-483">Auch mit dieser Einstellung kann der normale HTTP-Cache des Browsers diese Dateien weiterhin zwischenspeichern. Ob dies der Fall ist, hängt von der Konfiguration Ihres Webservers und den `cache-control`-Headern ab, die er bedient.</span><span class="sxs-lookup"><span data-stu-id="41e50-483">Even with this setting, the browser's normal HTTP cache may still cache those files, but whether or not this happens depends on your web server configuration and the `cache-control` headers that it serves.</span></span>

> [!NOTE]
> <span data-ttu-id="41e50-484">Die `BlazorCacheBootResources`-Eigenschaft deaktiviert keine Integritätsprüfungen für [progressive Webanwendungen (Progressive Web Applications, PWAs)](xref:blazor/progressive-web-app).</span><span class="sxs-lookup"><span data-stu-id="41e50-484">The `BlazorCacheBootResources` property doesn't disable integrity checks for [Progressive Web Applications (PWAs)](xref:blazor/progressive-web-app).</span></span> <span data-ttu-id="41e50-485">Anleitungen zu PWAs finden Sie im Abschnitt [Deaktivieren der Integritätsprüfung für PWAs](#disable-integrity-checking-for-pwas).</span><span class="sxs-lookup"><span data-stu-id="41e50-485">For guidance pertaining to PWAs, see the [Disable integrity checking for PWAs](#disable-integrity-checking-for-pwas) section.</span></span>

### <a name="disable-integrity-checking-for-pwas"></a><span data-ttu-id="41e50-486">Deaktivieren der Integritätsprüfung für PWAs</span><span class="sxs-lookup"><span data-stu-id="41e50-486">Disable integrity checking for PWAs</span></span>

<span data-ttu-id="41e50-487">Die Vorlage für progressive Webanwendungen (Progressive Web Applications, PWAs) von Blazor enthält eine vorgeschlagene `service-worker.published.js`-Datei, die für das Abrufen und Speichern von Anwendungsdateien für die Offlineverwendung zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="41e50-487">Blazor's Progressive Web Application (PWA) template contains a suggested `service-worker.published.js` file that's responsible for fetching and storing application files for offline use.</span></span> <span data-ttu-id="41e50-488">Dabei handelt es sich um einen vom normalen App-Startmechanismus separaten Prozess, der über eine eigene, separate Logik zur Integritätsprüfung verfügt.</span><span class="sxs-lookup"><span data-stu-id="41e50-488">This is a separate process from the normal app startup mechanism and has its own separate integrity checking logic.</span></span>

<span data-ttu-id="41e50-489">Die `service-worker.published.js`-Datei enthält folgende Zeile:</span><span class="sxs-lookup"><span data-stu-id="41e50-489">Inside the `service-worker.published.js` file, following line is present:</span></span>

```javascript
.map(asset => new Request(asset.url, { integrity: asset.hash }));
```

<span data-ttu-id="41e50-490">Entfernen Sie zum Deaktivieren der Integritätsprüfung den `integrity`-Parameter, indem Sie die Zeile wie folgt ändern:</span><span class="sxs-lookup"><span data-stu-id="41e50-490">To disable integrity checking, remove the `integrity` parameter by changing the line to the following:</span></span>

```javascript
.map(asset => new Request(asset.url));
```

<span data-ttu-id="41e50-491">Noch einmal: Das Deaktivieren der Integritätsprüfung bedeutet, dass Sie die Sicherheitsgarantien verlieren, die die Integritätsprüfung bietet.</span><span class="sxs-lookup"><span data-stu-id="41e50-491">Again, disabling integrity checking means that you lose the safety guarantees offered by integrity checking.</span></span> <span data-ttu-id="41e50-492">Beispielsweise besteht folgendes Risiko: Wenn der Browser des Benutzers genau in dem Moment, in dem Sie eine neue Version bereitstellen, die App zwischenspeichert, könnte er einige Dateien aus der alten und einige aus der neuen Bereitstellung zwischenspeichern.</span><span class="sxs-lookup"><span data-stu-id="41e50-492">For example, there is a risk that if the user's browser is caching the app at the exact moment that you deploy a new version, it could cache some files from the old deployment and some from the new deployment.</span></span> <span data-ttu-id="41e50-493">Wenn dies der Fall ist, bleibt die App in einem defekten Zustand, bis Sie ein weiteres Update bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="41e50-493">If that happens, the app becomes stuck in a broken state until you deploy a further update.</span></span>
