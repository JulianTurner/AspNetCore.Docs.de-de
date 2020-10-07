---
title: Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/25/2020
no-loc:
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
ms.openlocfilehash: 3436620123618ab32daa44c4a37057aaadb89563
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393690"
---
# <a name="host-and-deploy-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="63131-103">Hosten und Bereitstellen von ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="63131-103">Host and deploy ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="63131-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams) und [Safia Abdalla](https://safia.rocks)</span><span class="sxs-lookup"><span data-stu-id="63131-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), [Daniel Roth](https://github.com/danroth27), [Ben Adams](https://twitter.com/ben_a_adams), and [Safia Abdalla](https://safia.rocks)</span></span>

<span data-ttu-id="63131-105">Mit dem [Blazor WebAssembly-Hostingmodell](xref:blazor/hosting-models#blazor-webassembly):</span><span class="sxs-lookup"><span data-stu-id="63131-105">With the [Blazor WebAssembly hosting model](xref:blazor/hosting-models#blazor-webassembly):</span></span>

* <span data-ttu-id="63131-106">Die Blazor-App, ihre Abhängigkeiten und die .NET-Runtime werden parallel im Browser heruntergeladen.</span><span class="sxs-lookup"><span data-stu-id="63131-106">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser in parallel.</span></span>
* <span data-ttu-id="63131-107">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="63131-107">The app is executed directly on the browser UI thread.</span></span>

<span data-ttu-id="63131-108">Die folgenden Bereitstellungsstrategien werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="63131-108">The following deployment strategies are supported:</span></span>

* <span data-ttu-id="63131-109">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63131-109">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="63131-110">Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.</span><span class="sxs-lookup"><span data-stu-id="63131-110">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
* <span data-ttu-id="63131-111">Die Blazor-App wird auf einem Webserver oder Webservice für statisches Hosting abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-111">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="63131-112">Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt, der Informationen zum Hosten einer Blazor WebAssembly-App als untergeordnete IIS-App enthält.</span><span class="sxs-lookup"><span data-stu-id="63131-112">This strategy is covered in the [Standalone deployment](#standalone-deployment) section, which includes information on hosting a Blazor WebAssembly app as an IIS sub-app.</span></span>

## <a name="compression"></a><span data-ttu-id="63131-113">Komprimierung</span><span class="sxs-lookup"><span data-stu-id="63131-113">Compression</span></span>

<span data-ttu-id="63131-114">Wenn eine Blazor WebAssembly-App veröffentlicht wird, wird die Ausgabe bei der Veröffentlichung statisch komprimiert, um die App-Größe zu verringern und den Aufwand für eine Laufzeitkomprimierung zu beseitigen.</span><span class="sxs-lookup"><span data-stu-id="63131-114">When a Blazor WebAssembly app is published, the output is statically compressed during publish to reduce the app's size and remove the overhead for runtime compression.</span></span> <span data-ttu-id="63131-115">Die folgenden Komprimierungsalgorithmen werden verwendet:</span><span class="sxs-lookup"><span data-stu-id="63131-115">The following compression algorithms are used:</span></span>

* <span data-ttu-id="63131-116">[Brotli](https://tools.ietf.org/html/rfc7932) (höchste Stufe)</span><span class="sxs-lookup"><span data-stu-id="63131-116">[Brotli](https://tools.ietf.org/html/rfc7932) (highest level)</span></span>
* [<span data-ttu-id="63131-117">Gzip</span><span class="sxs-lookup"><span data-stu-id="63131-117">Gzip</span></span>](https://tools.ietf.org/html/rfc1952)

<span data-ttu-id="63131-118">Blazor basiert auf dem Host, um die entsprechenden komprimierten Dateien bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="63131-118">Blazor relies on the host to the serve the appropriate compressed files.</span></span> <span data-ttu-id="63131-119">Wenn Sie ein mit ASP.NET Core gehostetes Projekt verwenden, kann das Hostprojekt die Inhaltsaushandlung durchführen und die statisch komprimierten Dateien bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="63131-119">When using an ASP.NET Core hosted project, the host project is capable of performing content negotiation and serving the statically-compressed files.</span></span> <span data-ttu-id="63131-120">Beim Hosten einer eigenständigen Blazor WebAssembly-App sind möglicherweise zusätzliche Schritte erforderlich, um sicherzustellen, dass die statisch komprimierten Dateien bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="63131-120">When hosting a Blazor WebAssembly standalone app, additional work might be required to ensure that statically-compressed files are served:</span></span>

* <span data-ttu-id="63131-121">Informationen zur Komprimierungskonfiguration der Datei `web.config` von IIS finden Sie im Abschnitt [IIS: Brotli- und Gzip-Komprimierung](#brotli-and-gzip-compression).</span><span class="sxs-lookup"><span data-stu-id="63131-121">For IIS `web.config` compression configuration, see the [IIS: Brotli and Gzip compression](#brotli-and-gzip-compression) section.</span></span> 
* <span data-ttu-id="63131-122">Beim Hosten über statische Hostinglösungen, die die Inhaltsaushandlung für statisch komprimierte Dateien nicht unterstützen (z. B. GitHub-Seiten), sollten Sie die App so konfigurieren, dass sie die in das Brotli-Format komprimierten Dateien abruft und decodiert:</span><span class="sxs-lookup"><span data-stu-id="63131-122">When hosting on static hosting solutions that don't support statically-compressed file content negotiation, such as GitHub Pages, consider configuring the app to fetch and decode Brotli compressed files:</span></span>

  * <span data-ttu-id="63131-123">Rufen Sie den JavaScript Brotli-Decoder aus dem [GitHub-Repository „google/brotli“](https://github.com/google/brotli) ab.</span><span class="sxs-lookup"><span data-stu-id="63131-123">Obtain the JavaScript Brotli decoder from the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="63131-124">Ab September 2020 heißt die Decoderdatei `decode.js` und befindet sich im [Ordner `js`](https://github.com/google/brotli/tree/master/js) des Repositorys.</span><span class="sxs-lookup"><span data-stu-id="63131-124">As of September 2020, the decoder file is named `decode.js` and found in the repository's [`js` folder](https://github.com/google/brotli/tree/master/js).</span></span>
  
    > [!NOTE]
    > <span data-ttu-id="63131-125">Eine Regression ist in der verkleinerten Version des Skripts `decode.js` (`decode.min.js`) im [GitHub-Repository „google/brotli“](https://github.com/google/brotli) enthalten.</span><span class="sxs-lookup"><span data-stu-id="63131-125">A regression is present in the minified version of the `decode.js` script (`decode.min.js`) in the [google/brotli GitHub repository](https://github.com/google/brotli).</span></span> <span data-ttu-id="63131-126">Verkleinern Sie das Skript entweder selbst, oder verwenden Sie das [npm-Paket](https://www.npmjs.com/package/brotli), bis das Problem [Window.BrotliDecode ist nicht in „decode.min.js“ festgelegt (google/brotli, 844)](https://github.com/google/brotli/issues/844) behoben wurde.</span><span class="sxs-lookup"><span data-stu-id="63131-126">Either minify the script on your own or use the [npm package](https://www.npmjs.com/package/brotli) until the issue [Window.BrotliDecode is not set in decode.min.js (google/brotli #844)](https://github.com/google/brotli/issues/844) is resolved.</span></span> <span data-ttu-id="63131-127">Im Beispielcode in diesem Abschnitt wird die **nicht verkleinerte** Version des Skripts verwendet.</span><span class="sxs-lookup"><span data-stu-id="63131-127">The example code in this section uses the **unminified** version of the script.</span></span>

  * <span data-ttu-id="63131-128">Aktualisieren Sie die App zur Verwendung des Decoders.</span><span class="sxs-lookup"><span data-stu-id="63131-128">Update the app to use the decoder.</span></span> <span data-ttu-id="63131-129">Ändern Sie das Markup innerhalb des schließenden `<body>`-Tags in der Datei `wwwroot/index.html` wie folgt:</span><span class="sxs-lookup"><span data-stu-id="63131-129">Change the markup inside the closing `<body>` tag in `wwwroot/index.html` to the following:</span></span>
  
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
 
<span data-ttu-id="63131-130">Um die Komprimierung zu deaktivieren, fügen Sie der Projektdatei der App die `BlazorEnableCompression` MSBuild-Eigenschaft hinzu, und setzen Sie den Wert auf `false`:</span><span class="sxs-lookup"><span data-stu-id="63131-130">To disable compression, add the `BlazorEnableCompression` MSBuild property to the app's project file and set the value to `false`:</span></span>

```xml
<PropertyGroup>
  <BlazorEnableCompression>false</BlazorEnableCompression>
</PropertyGroup>
```

<span data-ttu-id="63131-131">Die `BlazorEnableCompression`-Eigenschaft kann an den [`dotnet publish`](/dotnet/core/tools/dotnet-publish)-Befehl mit der folgenden Syntax in einer Befehlsshell übergeben werden:</span><span class="sxs-lookup"><span data-stu-id="63131-131">The `BlazorEnableCompression` property can be passed to the [`dotnet publish`](/dotnet/core/tools/dotnet-publish) command with the following syntax in a command shell:</span></span>

```dotnetcli
dotnet publish -p:BlazorEnableCompression=false
```

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="63131-132">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="63131-132">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="63131-133">Routinganforderungen für Seitenkomponenten in einer Blazor WebAssembly-App sind nicht so unkompliziert wie Routinganforderungen in einer gehosteten Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="63131-133">Routing requests for page components in a Blazor WebAssembly app isn't as straightforward as routing requests in a Blazor Server, hosted app.</span></span> <span data-ttu-id="63131-134">Betrachten wir eine Blazor WebAssembly-App mit zwei Komponenten:</span><span class="sxs-lookup"><span data-stu-id="63131-134">Consider a Blazor WebAssembly app with two components:</span></span>

* <span data-ttu-id="63131-135">`Main.razor`: Wird im Stammverzeichnis der App geladen und enthält einen Link zur `About`-Komponente (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="63131-135">`Main.razor`: Loads at the root of the app and contains a link to the `About` component (`href="About"`).</span></span>
* <span data-ttu-id="63131-136">`About.razor`: `About`-Komponente.</span><span class="sxs-lookup"><span data-stu-id="63131-136">`About.razor`: `About` component.</span></span>

<span data-ttu-id="63131-137">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="63131-137">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="63131-138">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="63131-138">The browser makes a request.</span></span>
1. <span data-ttu-id="63131-139">Die Standardseite wird zurückgegeben, in der Regel `index.html`.</span><span class="sxs-lookup"><span data-stu-id="63131-139">The default page is returned, which is usually `index.html`.</span></span>
1. <span data-ttu-id="63131-140">`index.html` startet die App.</span><span class="sxs-lookup"><span data-stu-id="63131-140">`index.html` bootstraps the app.</span></span>
1. <span data-ttu-id="63131-141">Der Blazor-Router wird geladen, und die Razor-Komponente `Main` wird gerendert.</span><span class="sxs-lookup"><span data-stu-id="63131-141">Blazor's router loads, and the Razor `Main` component is rendered.</span></span>

<span data-ttu-id="63131-142">Auf der Seite „Main“ kann der Link zur `About`-Komponente auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die gerenderte `About`-Komponente selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="63131-142">In the Main page, selecting the link to the `About` component works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the rendered `About` component itself.</span></span> <span data-ttu-id="63131-143">Alle Anforderungen von internen Endpunkten *innerhalb der Blazor WebAssembly-App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="63131-143">All of the requests for internal endpoints *within the Blazor WebAssembly app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="63131-144">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="63131-144">The router handles the requests internally.</span></span>

<span data-ttu-id="63131-145">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="63131-145">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="63131-146">Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="63131-146">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="63131-147">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite `index.html` erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="63131-147">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the `index.html` page.</span></span> <span data-ttu-id="63131-148">Wenn `index.html` zurückgegeben wird, übernimmt der Blazor-Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="63131-148">When `index.html` is returned, the app's Blazor router takes over and responds with the correct resource.</span></span>

<span data-ttu-id="63131-149">Wenn Sie die Bereitstellung auf einem IIS-Server durchführen, können Sie das URL-Rewrite-Modul mit der veröffentlichten `web.config`-Datei der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="63131-149">When deploying to an IIS server, you can use the URL Rewrite Module with the app's published `web.config` file.</span></span> <span data-ttu-id="63131-150">Weitere Informationen finden Sie im Abschnitt [IIS](#iis).</span><span class="sxs-lookup"><span data-stu-id="63131-150">For more information, see the [IIS](#iis) section.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="63131-151">Gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63131-151">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="63131-152">Mit einer *gehosteten Bereitstellung* wird die Blazor WebAssembly-App über eine auf einem Webserver ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="63131-152">A *hosted deployment* serves the Blazor WebAssembly app to browsers from an [ASP.NET Core app](xref:index) that runs on a web server.</span></span>

<span data-ttu-id="63131-153">Die Blazor WebAssembly-Client-App wird im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` der Server-App zusammen mit allen anderen statischen Webressourcen der Server-App veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="63131-153">The client Blazor WebAssembly app is published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder of the server app, along with any other static web assets of the server app.</span></span> <span data-ttu-id="63131-154">Die beiden Apps werden zusammen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="63131-154">The two apps are deployed together.</span></span> <span data-ttu-id="63131-155">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="63131-155">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="63131-156">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor WebAssembly-App**-Projektvorlage (`blazorwasm`-Vorlage bei Verwendung des Befehls [`dotnet new`](/dotnet/core/tools/dotnet-new)) mit der ausgewählten Option **`Hosted`** (`-ho|--hosted`, wenn Sie den Befehl `dotnet new` verwenden).</span><span class="sxs-lookup"><span data-stu-id="63131-156">For a hosted deployment, Visual Studio includes the **Blazor WebAssembly App** project template (`blazorwasm` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command) with the **`Hosted`** option selected (`-ho|--hosted` when using the `dotnet new` command).</span></span>

<span data-ttu-id="63131-157">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="63131-157">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="63131-158">Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="63131-158">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="hosted-deployment-with-multiple-no-locblazor-webassembly-apps"></a><span data-ttu-id="63131-159">Gehostete Bereitstellung mit mehreren Blazor WebAssembly-Apps</span><span class="sxs-lookup"><span data-stu-id="63131-159">Hosted deployment with multiple Blazor WebAssembly apps</span></span>

### <a name="app-configuration"></a><span data-ttu-id="63131-160">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="63131-160">App configuration</span></span>

<span data-ttu-id="63131-161">So konfigurieren Sie eine gehostete Blazor-Lösung für mehrere Blazor WebAssembly-Apps:</span><span class="sxs-lookup"><span data-stu-id="63131-161">To configure a hosted Blazor solution to serve multiple Blazor WebAssembly apps:</span></span>

* <span data-ttu-id="63131-162">Verwenden Sie eine vorhandene gehostete Blazor-Lösung, oder erstellen Sie eine neue Projektmappe aus der Projektvorlage „Blazor Hosted“.</span><span class="sxs-lookup"><span data-stu-id="63131-162">Use an existing hosted Blazor solution or create a new solution from the Blazor Hosted project template.</span></span>

* <span data-ttu-id="63131-163">Fügen Sie in der Projektdatei der Client-App `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `FirstApp` hinzu, um den Basispfad für die statischen Ressourcen des Projekts festzulegen:</span><span class="sxs-lookup"><span data-stu-id="63131-163">In the client app's project file, add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `FirstApp` to set the base path for the project's static assets:</span></span>

  ```xml
  <PropertyGroup>
    ...
    <StaticWebAssetBasePath>FirstApp</StaticWebAssetBasePath>
  </PropertyGroup>
  ```

* <span data-ttu-id="63131-164">Fügen Sie der Projektmappe eine zweite Client-App hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-164">Add a second client app to the solution:</span></span>

  * <span data-ttu-id="63131-165">Fügen Sie dem Ordner der Projektmappe einen Ordner mit dem Namen `SecondClient` hinzu.</span><span class="sxs-lookup"><span data-stu-id="63131-165">Add a folder named `SecondClient` to the solution's folder.</span></span>
  * <span data-ttu-id="63131-166">Erstellen Sie eine Blazor WebAssembly-App mit dem Namen `SecondBlazorApp.Client` im Ordner `SecondClient` aus der Blazor WebAssembly-Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="63131-166">Create a Blazor WebAssembly app named `SecondBlazorApp.Client` in the `SecondClient` folder from the Blazor WebAssembly project template.</span></span>
  * <span data-ttu-id="63131-167">In der Projektdatei der App:</span><span class="sxs-lookup"><span data-stu-id="63131-167">In the app's project file:</span></span>

    * <span data-ttu-id="63131-168">Fügen Sie `<PropertyGroup>` eine `<StaticWebAssetBasePath>`-Eigenschaft mit dem Wert `SecondApp` hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-168">Add a `<StaticWebAssetBasePath>` property to the `<PropertyGroup>` with a value of `SecondApp`:</span></span>

      ```xml
      <PropertyGroup>
        ...
        <StaticWebAssetBasePath>SecondApp</StaticWebAssetBasePath>
      </PropertyGroup>
      ```

    * <span data-ttu-id="63131-169">Fügt einen Projektverweis auf das `Shared`-Projekt hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-169">Add a project reference to the `Shared` project:</span></span>

      ```xml
      <ItemGroup>
        <ProjectReference Include="..\Shared\{SOLUTION NAME}.Shared.csproj" />
      </ItemGroup>
      ```

      <span data-ttu-id="63131-170">Der Platzhalter `{SOLUTION NAME}` ist der Name der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="63131-170">The placeholder `{SOLUTION NAME}` is the solution's name.</span></span>

* <span data-ttu-id="63131-171">Erstellen Sie in der Projektdatei der Server-App einen Projektverweis für die hinzugefügte Client-App:</span><span class="sxs-lookup"><span data-stu-id="63131-171">In the server app's project file, create a project reference for the added client app:</span></span>

  ```xml
  <ItemGroup>
    ...
    <ProjectReference Include="..\SecondClient\SecondBlazorApp.Client.csproj" />
  </ItemGroup>
  ```

* <span data-ttu-id="63131-172">Konfigurieren Sie in der Datei `Properties/launchSettings.json` der Server-App die `applicationUrl` des Kestrel-Profils (`{SOLUTION NAME}.Server`) für den Zugriff auf Client-Apps an den Ports 5001 und 5002:</span><span class="sxs-lookup"><span data-stu-id="63131-172">In the server app's `Properties/launchSettings.json` file, configure the `applicationUrl` of the Kestrel profile (`{SOLUTION NAME}.Server`) to access the client apps at ports 5001 and 5002:</span></span>

  ```json
  "applicationUrl": "https://localhost:5001;https://localhost:5002",
  ```

* <span data-ttu-id="63131-173">Entfernen Sie in der `Startup.Configure`-Methode (`Startup.cs`) der Server-App die folgenden Zeilen nach dem Aufruf von <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span><span class="sxs-lookup"><span data-stu-id="63131-173">In the server app's `Startup.Configure` method (`Startup.cs`), remove the following lines, which appear after the call to <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>:</span></span>

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

  <span data-ttu-id="63131-174">Fügen Sie Middleware hinzu, die Anforderungen den Client-Apps zuordnet.</span><span class="sxs-lookup"><span data-stu-id="63131-174">Add middleware that maps requests to the client apps.</span></span> <span data-ttu-id="63131-175">Im folgenden Beispiel wird die Middleware so konfiguriert, dass Sie unter den folgenden Umständen ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="63131-175">The following example configures the middleware to run when:</span></span>

  * <span data-ttu-id="63131-176">Der Anforderungsport ist entweder 5001 für die ursprüngliche Client-App oder 5002 für die hinzugefügte Client-App.</span><span class="sxs-lookup"><span data-stu-id="63131-176">The request port is either 5001 for the original client app or 5002 for the added client app.</span></span>
  * <span data-ttu-id="63131-177">Der Anforderungshost ist entweder `firstapp.com` für die ursprüngliche Client-App oder `secondapp.com` für die hinzugefügte Client-App.</span><span class="sxs-lookup"><span data-stu-id="63131-177">The request host is either `firstapp.com` for the original client app or `secondapp.com` for the added client app.</span></span>

    > [!NOTE]
    > <span data-ttu-id="63131-178">Das in diesem Abschnitt gezeigte Beispiel erfordert zusätzliche Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="63131-178">The example shown in this section requires additional configuration for:</span></span>
    >
    > * <span data-ttu-id="63131-179">Für dasZugreifen auf die Apps auf den Beispielhostdomänen: `firstapp.com` und `secondapp.com`.</span><span class="sxs-lookup"><span data-stu-id="63131-179">Accessing the apps at the example host domains, `firstapp.com` and `secondapp.com`.</span></span>
    > * <span data-ttu-id="63131-180">Zertifikate für die Client-Apps zum Aktivieren von TLS-Sicherheit (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="63131-180">Certificates for the client apps to enable TLS security (HTTPS).</span></span>
    >
    > <span data-ttu-id="63131-181">Die erforderliche Konfiguration sprengt den Rahmen dieses Artikels und ist davon abhängig, wie die Lösung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-181">The required configuration is beyond the scope of this article and depends on how the solution is hosted.</span></span> <span data-ttu-id="63131-182">Weitere Informationen finden Sie in den Artikeln zum [Hosten und Bereitstellen](xref:host-and-deploy/index).</span><span class="sxs-lookup"><span data-stu-id="63131-182">For more information see the [Host and deploy articles](xref:host-and-deploy/index).</span></span>

  <span data-ttu-id="63131-183">Platzieren Sie den folgenden Code dort, wo zuvor Zeilen entfernt wurden:</span><span class="sxs-lookup"><span data-stu-id="63131-183">Place the following code where the lines were removed earlier:</span></span>

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

* <span data-ttu-id="63131-184">Ersetzen Sie im Wettervorhersagecontroller (`Controllers/WeatherForecastController.cs`) der Server-App die vorhandene Route (`[Route("[controller]")]`) zu `WeatherForecastController` durch die folgenden Routen:</span><span class="sxs-lookup"><span data-stu-id="63131-184">In the server app's weather forecast controller (`Controllers/WeatherForecastController.cs`), replace the existing route (`[Route("[controller]")]`) to `WeatherForecastController` with the following routes:</span></span>

  ```csharp
  [Route("FirstApp/[controller]")]
  [Route("SecondApp/[controller]")]
  ```

  <span data-ttu-id="63131-185">Die Middleware, die der `Startup.Configure`-Methode der Server-App hinzugefügt wurde, ändert eingehende Anforderungen an `/WeatherForecast` abhängig vom Port (5001/5002) oder der Domäne (`firstapp.com`/`secondapp.com`) in `/FirstApp/WeatherForecast` oder `/SecondApp/WeatherForecast`.</span><span class="sxs-lookup"><span data-stu-id="63131-185">The middleware added to the server app's `Startup.Configure` method earlier modifies incoming requests to `/WeatherForecast` to either `/FirstApp/WeatherForecast` or `/SecondApp/WeatherForecast` depending on the port (5001/5002) or domain (`firstapp.com`/`secondapp.com`).</span></span> <span data-ttu-id="63131-186">Die vorherigen Controllerrouten sind erforderlich, um Wetterdaten von der Server-App an die Client-Apps zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="63131-186">The preceding controller routes are required in order to return weather data from the server app to the client apps.</span></span>

### <a name="static-assets-and-class-libraries"></a><span data-ttu-id="63131-187">Statische Ressourcen und Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="63131-187">Static assets and class libraries</span></span>

<span data-ttu-id="63131-188">Verwenden Sie die folgenden Verfahren für statische Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="63131-188">Use the following approaches for static assets:</span></span>

* <span data-ttu-id="63131-189">Wenn sich die Ressource im Ordner `wwwroot` der Client-App befindet, geben Sie Pfade normal an:</span><span class="sxs-lookup"><span data-stu-id="63131-189">When the asset is in the client app's `wwwroot` folder, provide their paths normally:</span></span>

  ```razor
  <img alt="..." src="/{ASSET FILE NAME}" />
  ```

* <span data-ttu-id="63131-190">Wenn sich die Ressource im Ordner `wwwroot` einer [Razor-Klassenbibliothek (RCL)](xref:blazor/components/class-libraries) befindet, verweisen Sie in der Client-App gemäß der Anleitung im [RCL-Artikel](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl) auf die statische Ressource:</span><span class="sxs-lookup"><span data-stu-id="63131-190">When the asset is in the `wwwroot` folder of a [Razor Class Library (RCL)](xref:blazor/components/class-libraries), reference the static asset in the client app per the guidance in the [RCL article](xref:razor-pages/ui-class#consume-content-from-a-referenced-rcl):</span></span>

  ```razor
  <img alt="..." src="_content/{LIBRARY NAME}/{ASSET FILE NAME}" />
  ```

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="63131-191">Auf Komponenten, die für eine Client-App durch eine Klassenbibliothek bereitgestellt werden, wird normal verwiesen.</span><span class="sxs-lookup"><span data-stu-id="63131-191">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="63131-192">Wenn für Komponenten Stylesheets oder JavaScript-Dateien erforderlich sind, verwenden Sie einen der folgenden Ansätze, um die statischen Ressourcen abzurufen:</span><span class="sxs-lookup"><span data-stu-id="63131-192">If any components require stylesheets or JavaScript files, use either of the following approaches to obtain the static assets:</span></span>

* <span data-ttu-id="63131-193">Die Datei `wwwroot/index.html` der Client-App kann mit den statischen Ressourcen verknüpft werden (`<link>`).</span><span class="sxs-lookup"><span data-stu-id="63131-193">The client app's `wwwroot/index.html` file can link (`<link>`) to the static assets.</span></span>
* <span data-ttu-id="63131-194">Die Komponente kann die [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks verwenden, um die statischen Ressourcen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="63131-194">The component can use the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) to obtain the static assets.</span></span>

<span data-ttu-id="63131-195">Die oben aufgeführten Konzepte werden in den folgenden Beispielen gezeigt.</span><span class="sxs-lookup"><span data-stu-id="63131-195">The preceding approaches are demonstrated in the following examples.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="63131-196">Auf Komponenten, die für eine Client-App durch eine Klassenbibliothek bereitgestellt werden, wird normal verwiesen.</span><span class="sxs-lookup"><span data-stu-id="63131-196">Components provided to a client app by a class library are referenced normally.</span></span> <span data-ttu-id="63131-197">Wenn für Komponenten Stylesheets oder JavaScript-Dateien erforderlich sind, muss die Datei `wwwroot/index.html` der Client-App die richtigen Links der statischen Ressourcen enthalten.</span><span class="sxs-lookup"><span data-stu-id="63131-197">If any components require stylesheets or JavaScript files, the client app's `wwwroot/index.html` file must include the correct static asset links.</span></span> <span data-ttu-id="63131-198">Diese Konzepte werden in den folgenden Beispielen gezeigt.</span><span class="sxs-lookup"><span data-stu-id="63131-198">These approaches are demonstrated in the following examples.</span></span>

::: moniker-end

<span data-ttu-id="63131-199">Fügen Sie einer der Client-Apps die folgende `Jeep`-Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="63131-199">Add the following `Jeep` component to one of the client apps.</span></span> <span data-ttu-id="63131-200">Die `Jeep`-Komponente verwendet:</span><span class="sxs-lookup"><span data-stu-id="63131-200">The `Jeep` component uses:</span></span>

* <span data-ttu-id="63131-201">Ein Bild aus dem Ordner `wwwroot` der Client-App (`jeep-cj.png`).</span><span class="sxs-lookup"><span data-stu-id="63131-201">An image from the client app's `wwwroot` folder (`jeep-cj.png`).</span></span>
* <span data-ttu-id="63131-202">Ein Bild aus einem Ordner `wwwroot` (`jeep-yj.png`) einer [hinzugefügten Razor Komponentenbibliothek](xref:blazor/components/class-libraries) (`JeepImage`).</span><span class="sxs-lookup"><span data-stu-id="63131-202">An image from an [added Razor component library](xref:blazor/components/class-libraries) (`JeepImage`) `wwwroot` folder (`jeep-yj.png`).</span></span>
* <span data-ttu-id="63131-203">Die Beispielkomponente (`Component1`) wird von der RCL-Projektvorlage automatisch erstellt, wenn die `JeepImage`-Bibliothek der Projektmappe hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="63131-203">The example component (`Component1`) is created automatically by the RCL project template when the `JeepImage` library is added to the solution.</span></span>

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
> <span data-ttu-id="63131-204">Veröffentlichen Sie **keine** Bilder von Fahrzeugen, es sei denn, Sie sind der Eigentümer der Bilder.</span><span class="sxs-lookup"><span data-stu-id="63131-204">Do **not** publish images of vehicles publicly unless you own the images.</span></span> <span data-ttu-id="63131-205">Andernfalls riskieren Sie Urheberrechtsverletzungen.</span><span class="sxs-lookup"><span data-stu-id="63131-205">Otherwise, you risk copyright infringement.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="63131-206">Das Bild `jeep-yj.png` der Bibliothek kann auch der `Component1`-Komponente (`Component1.razor`) der Bibliothek hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="63131-206">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`).</span></span> <span data-ttu-id="63131-207">Wenn Sie die `my-component`-CSS-Klasse auf der Seite der Client-App bereitstellen möchten, verknüpfen Sie das Stylesheet der Bibliothek mithilfe der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) des Frameworks:</span><span class="sxs-lookup"><span data-stu-id="63131-207">To provide the `my-component` CSS class to the client app's page, link to the library's stylesheet using the framework's [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements):</span></span>

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

<span data-ttu-id="63131-208">Eine Alternative zur Verwendung der [`Link`-Komponente](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) besteht darin, das Stylesheet aus der Datei `wwwroot/index.html` der Client-App zu laden.</span><span class="sxs-lookup"><span data-stu-id="63131-208">An alternative to using the [`Link` component](xref:blazor/fundamentals/additional-scenarios#influence-html-head-tag-elements) is to load the stylesheet from the client app's `wwwroot/index.html` file.</span></span> <span data-ttu-id="63131-209">Bei diesem Ansatz ist das Stylesheet für alle Komponenten in der Client-App verfügbar:</span><span class="sxs-lookup"><span data-stu-id="63131-209">This approach makes the stylesheet available to all of the components in the client app:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="63131-210">Das Bild `jeep-yj.png`der Bibliothek kann auch der `Component1`-Komponente der Bibliothek (`Component1.razor`) hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="63131-210">The library's `jeep-yj.png` image can also be added to the library's `Component1` component (`Component1.razor`):</span></span>

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

<span data-ttu-id="63131-211">Die Datei `wwwroot/index.html` der Client-App fordert das Stylesheet der Bibliothek mit dem folgenden hinzugefügten `<link>`-Tag an:</span><span class="sxs-lookup"><span data-stu-id="63131-211">The client app's `wwwroot/index.html` file requests the library's stylesheet with the following added `<link>` tag:</span></span>

```html
<head>
    ...
    <link href="_content/JeepImage/styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

<span data-ttu-id="63131-212">Fügen Sie der `Jeep`-Komponente in der `NavMenu`-Komponente der Client-App (`Shared/NavMenu.razor`) Navigation hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-212">Add navigation to the `Jeep` component in the client app's `NavMenu` component (`Shared/NavMenu.razor`):</span></span>

```razor
<li class="nav-item px-3">
    <NavLink class="nav-link" href="Jeep">
        <span class="oi oi-list-rich" aria-hidden="true"></span> Jeep
    </NavLink>
</li>
```

<span data-ttu-id="63131-213">Weitere Informationen zu RCLs finden Sie hier:</span><span class="sxs-lookup"><span data-stu-id="63131-213">For more information on RCLs, see:</span></span>

* <xref:blazor/components/class-libraries>
* <xref:razor-pages/ui-class>

## <a name="standalone-deployment"></a><span data-ttu-id="63131-214">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="63131-214">Standalone deployment</span></span>

<span data-ttu-id="63131-215">Bei einer *eigenständigen Bereitstellung* wird die Blazor WebAssembly-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="63131-215">A *standalone deployment* serves the Blazor WebAssembly app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="63131-216">Jeder Server für statische Dateien kann die Blazor-App bedienen.</span><span class="sxs-lookup"><span data-stu-id="63131-216">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="63131-217">Eigenständige Bereitstellungsressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="63131-217">Standalone deployment assets are published into the `/bin/Release/{TARGET FRAMEWORK}/publish/wwwroot` folder.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="63131-218">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="63131-218">Azure App Service</span></span>

<span data-ttu-id="63131-219">Blazor WebAssembly-Apps können in Azure App Service unter Windows bereitgestellt werden. Dort wird die App auf [IIS](#iis) gehostet.</span><span class="sxs-lookup"><span data-stu-id="63131-219">Blazor WebAssembly apps can be deployed to Azure App Services on Windows, which hosts the app on [IIS](#iis).</span></span>

<span data-ttu-id="63131-220">Die Bereitstellung einer eigenständigen Blazor WebAssembly-App in Azure App Service für Linux wird derzeit nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63131-220">Deploying a standalone Blazor WebAssembly app to Azure App Service for Linux isn't currently supported.</span></span> <span data-ttu-id="63131-221">Ein Linux-Serverimage zum Hosten der App ist derzeit nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="63131-221">A Linux server image to host the app isn't available at this time.</span></span> <span data-ttu-id="63131-222">Wir arbeiten daran, sodass dieses Szenario bald unterstützt werden kann.</span><span class="sxs-lookup"><span data-stu-id="63131-222">Work is in progress to enable this scenario.</span></span>

### <a name="iis"></a><span data-ttu-id="63131-223">IIS</span><span class="sxs-lookup"><span data-stu-id="63131-223">IIS</span></span>

<span data-ttu-id="63131-224">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="63131-224">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="63131-225">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis) (Erstellen einer statischen Website unter IIS).</span><span class="sxs-lookup"><span data-stu-id="63131-225">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="63131-226">Veröffentlichte Ressourcen werden im Ordner `/bin/Release/{TARGET FRAMEWORK}/publish` erstellt.</span><span class="sxs-lookup"><span data-stu-id="63131-226">Published assets are created in the `/bin/Release/{TARGET FRAMEWORK}/publish` folder.</span></span> <span data-ttu-id="63131-227">Die Inhalte des Ordners `publish` werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="63131-227">Host the contents of the `publish` folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="63131-228">web.config</span><span class="sxs-lookup"><span data-stu-id="63131-228">web.config</span></span>

<span data-ttu-id="63131-229">Beim Veröffentlichen eines Blazor-Projekts wird eine `web.config`-Datei mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="63131-229">When a Blazor project is published, a `web.config` file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="63131-230">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="63131-230">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="63131-231">`.dll`: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="63131-231">`.dll`: `application/octet-stream`</span></span>
  * <span data-ttu-id="63131-232">`.json`: `application/json`</span><span class="sxs-lookup"><span data-stu-id="63131-232">`.json`: `application/json`</span></span>
  * <span data-ttu-id="63131-233">`.wasm`: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="63131-233">`.wasm`: `application/wasm`</span></span>
  * <span data-ttu-id="63131-234">`.woff`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="63131-234">`.woff`: `application/font-woff`</span></span>
  * <span data-ttu-id="63131-235">`.woff2`: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="63131-235">`.woff2`: `application/font-woff`</span></span>
* <span data-ttu-id="63131-236">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="63131-236">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="63131-237">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="63131-237">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="63131-238">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (`wwwroot/{PATH REQUESTED}`).</span><span class="sxs-lookup"><span data-stu-id="63131-238">Serve the sub-directory where the app's static assets reside (`wwwroot/{PATH REQUESTED}`).</span></span>
  * <span data-ttu-id="63131-239">Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (`wwwroot/index.html`) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="63131-239">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (`wwwroot/index.html`).</span></span>
  
#### <a name="use-a-custom-webconfig"></a><span data-ttu-id="63131-240">Verwenden einer benutzerdefinierten Datei web.config</span><span class="sxs-lookup"><span data-stu-id="63131-240">Use a custom web.config</span></span>

<span data-ttu-id="63131-241">Um eine benutzerdefinierte Datei `web.config` zu verwenden, speichern Sie die benutzerdefinierte Datei `web.config` im Stammverzeichnis des Projektordners und veröffentlichen das Projekt.</span><span class="sxs-lookup"><span data-stu-id="63131-241">To use a custom `web.config` file, place the custom `web.config` file at the root of the project folder and publish the project.</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="63131-242">Installieren des URL-Rewrite-Moduls</span><span class="sxs-lookup"><span data-stu-id="63131-242">Install the URL Rewrite Module</span></span>

<span data-ttu-id="63131-243">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="63131-243">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="63131-244">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="63131-244">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="63131-245">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="63131-245">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="63131-246">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="63131-246">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="63131-247">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="63131-247">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="63131-248">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="63131-248">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="63131-249">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="63131-249">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="63131-250">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="63131-250">Copy the installer to the server.</span></span> <span data-ttu-id="63131-251">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="63131-251">Run the installer.</span></span> <span data-ttu-id="63131-252">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="63131-252">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="63131-253">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="63131-253">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="63131-254">Konfigurieren der Website</span><span class="sxs-lookup"><span data-stu-id="63131-254">Configure the website</span></span>

<span data-ttu-id="63131-255">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="63131-255">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="63131-256">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="63131-256">The folder contains:</span></span>

* <span data-ttu-id="63131-257">Die Datei `web.config`, die von IIS zum Konfigurieren der Website verwendet wird, einschließlich der erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="63131-257">The `web.config` file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="63131-258">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="63131-258">The app's static asset folder.</span></span>

#### <a name="host-as-an-iis-sub-app"></a><span data-ttu-id="63131-259">Hosten als IIS-untergeordnete App</span><span class="sxs-lookup"><span data-stu-id="63131-259">Host as an IIS sub-app</span></span>

<span data-ttu-id="63131-260">Wenn eine eigenständige App als IIS-untergeordnete App gehostet wird, führen Sie einen der folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="63131-260">If a standalone app is hosted as an IIS sub-app, perform either of the following:</span></span>

* <span data-ttu-id="63131-261">Deaktivieren Sie den vererbten ASP.NET Core Module-Handler.</span><span class="sxs-lookup"><span data-stu-id="63131-261">Disable the inherited ASP.NET Core Module handler.</span></span>

  <span data-ttu-id="63131-262">Entfernen Sie den Handler in der veröffentlichen Datei `web.config` der Blazor-App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="63131-262">Remove the handler in the Blazor app's published `web.config` file by adding a `<handlers>` section to the file:</span></span>

  ```xml
  <handlers>
    <remove name="aspNetCore" />
  </handlers>
  ```

* <span data-ttu-id="63131-263">Deaktivieren Sie die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung), indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="63131-263">Disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

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

<span data-ttu-id="63131-264">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zur [Konfiguration des Basispfads der App](xref:blazor/host-and-deploy/index#app-base-path) durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="63131-264">Removing the handler or disabling inheritance is performed in addition to [configuring the app's base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span> <span data-ttu-id="63131-265">Legen Sie den Basispfad der App in der Datei `index.html` der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-265">Set the app base path in the app's `index.html` file to the IIS alias used when configuring the sub-app in IIS.</span></span>

#### <a name="brotli-and-gzip-compression"></a><span data-ttu-id="63131-266">Brotli- und Gzip-Komprimierung</span><span class="sxs-lookup"><span data-stu-id="63131-266">Brotli and Gzip compression</span></span>

<span data-ttu-id="63131-267">IIS kann über `web.config` so konfiguriert werden, dass Brotli- oder Gzip-komprimierte Blazor-Ressourcen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="63131-267">IIS can be configured via `web.config` to serve Brotli or Gzip compressed Blazor assets.</span></span> <span data-ttu-id="63131-268">Eine Beispielkonfiguration finden Sie unter [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span><span class="sxs-lookup"><span data-stu-id="63131-268">For an example configuration, see [`web.config`](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/blazor/host-and-deploy/webassembly/_samples/web.config?raw=true).</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="63131-269">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="63131-269">Troubleshooting</span></span>

<span data-ttu-id="63131-270">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="63131-270">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="63131-271">Wenn das Modul nicht installiert ist, kann die Datei `web.config` nicht von IIS analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="63131-271">When the module isn't installed, the `web.config` file can't be parsed by IIS.</span></span> <span data-ttu-id="63131-272">Dadurch wird verhindert, dass die Konfiguration der Website vom IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="63131-272">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="63131-273">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="63131-273">For more information on troubleshooting deployments to IIS, see <xref:test/troubleshoot-azure-iis>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="63131-274">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="63131-274">Azure Storage</span></span>

<span data-ttu-id="63131-275">Das Hosten statischer Dateien von [Azure Storage](/azure/storage/) ermöglicht das serverlose Blazor-App-Hosting.</span><span class="sxs-lookup"><span data-stu-id="63131-275">[Azure Storage](/azure/storage/) static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="63131-276">Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63131-276">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="63131-277">Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="63131-277">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="63131-278">Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="63131-278">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="63131-279">Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="63131-279">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="63131-280">Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="63131-280">Razor components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="63131-281">Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blob-Dienst generierte *Fehler 404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter.</span><span class="sxs-lookup"><span data-stu-id="63131-281">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="63131-282">Das Blob `index.html` wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.</span><span class="sxs-lookup"><span data-stu-id="63131-282">The `index.html` blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="63131-283">Wenn Dateien aufgrund ungeeigneter MIME-Typen im `Content-Type`-Headern der Dateien nicht zur Laufzeit geladen werden, führen Sie eine der folgenden Aktionen durch:</span><span class="sxs-lookup"><span data-stu-id="63131-283">If files aren't loaded at runtime due to inappropriate MIME types in the files' `Content-Type` headers, take either of the following actions:</span></span>

* <span data-ttu-id="63131-284">Konfigurieren Sie die Tools so, dass beim Bereitstellen der Dateien die richtigen MIME-Typen (`Content-Type`-Header) bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="63131-284">Configure your tooling to set the correct MIME types (`Content-Type` headers) when the files are deployed.</span></span>
* <span data-ttu-id="63131-285">Ändern Sie nach Bereitstellung der App die MIME-Typen (`Content-Type`-Header) für die Dateien.</span><span class="sxs-lookup"><span data-stu-id="63131-285">Change the MIME types (`Content-Type` headers) for the files after the app is deployed.</span></span>

  <span data-ttu-id="63131-286">Führen Sie im Storage-Explorer (Azure-Portal) für jede Datei folgende Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="63131-286">In Storage Explorer (Azure portal) for each file:</span></span>
  
  1. <span data-ttu-id="63131-287">Klicken Sie mit der rechten Maustaste auf die Datei, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="63131-287">Right-click the file and select **Properties**.</span></span>
  1. <span data-ttu-id="63131-288">Legen Sie **ContentType-** fest, und klicken Sie auf die Schaltfläche **Speichern**</span><span class="sxs-lookup"><span data-stu-id="63131-288">Set the **ContentType** and select the **Save** button.</span></span>

<span data-ttu-id="63131-289">Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="63131-289">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="63131-290">Nginx</span><span class="sxs-lookup"><span data-stu-id="63131-290">Nginx</span></span>

<span data-ttu-id="63131-291">Die folgende Datei `nginx.conf` wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei `index.html` gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="63131-291">The following `nginx.conf` file is simplified to show how to configure Nginx to send the `index.html` file whenever it can't find a corresponding file on disk.</span></span>

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

<span data-ttu-id="63131-292">Wenn Sie [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req) für das [NGINX-Burstratenlimit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) festlegen, erfordern Blazor WebAssembly-Apps möglicherweise einen größeren `burst`-Parameterwert, um die relativ große Anzahl an Anforderungen zu erfüllen, die eine App stellen kann.</span><span class="sxs-lookup"><span data-stu-id="63131-292">When setting the [NGINX burst rate limit](https://www.nginx.com/blog/rate-limiting-nginx/#bursts) with [`limit_req`](https://nginx.org/docs/http/ngx_http_limit_req_module.html#limit_req), Blazor WebAssembly apps may require a large `burst` parameter value to accommodate the relatively large number of requests made by an app.</span></span> <span data-ttu-id="63131-293">Legen Sie den Wert zunächst auf 60 fest:</span><span class="sxs-lookup"><span data-stu-id="63131-293">Initially, set the value to at least 60:</span></span>

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

<span data-ttu-id="63131-294">Erhöhen Sie den Wert, wenn Browserentwicklertools oder Netzwerkdatenverkehrstools angeben, dass für Anforderungen der Statuscode *503 – Dienst nicht verfügbar* zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="63131-294">Increase the value if browser developer tools or a network traffic tool indicates that requests are receiving a *503 - Service Unavailable* status code.</span></span>

<span data-ttu-id="63131-295">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="63131-295">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="63131-296">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="63131-296">Nginx in Docker</span></span>

<span data-ttu-id="63131-297">Richten Sie zum Hosten von Blazor in Docker mithilfe von NGINX das Dockerfile für die Verwendung des auf Alpine basierenden NGINX-Images ein.</span><span class="sxs-lookup"><span data-stu-id="63131-297">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="63131-298">Aktualisieren Sie die Dockerfile zum Kopieren der Datei `nginx.config` in den Container.</span><span class="sxs-lookup"><span data-stu-id="63131-298">Update the Dockerfile to copy the `nginx.config` file into the container.</span></span>

<span data-ttu-id="63131-299">Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="63131-299">Add one line to the Dockerfile, as shown in the following example:</span></span>

```dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="apache"></a><span data-ttu-id="63131-300">Apache</span><span class="sxs-lookup"><span data-stu-id="63131-300">Apache</span></span>

<span data-ttu-id="63131-301">So stellen Sie eine Blazor WebAssembly-App für CentOS 7 oder höher bereit:</span><span class="sxs-lookup"><span data-stu-id="63131-301">To deploy a Blazor WebAssembly app to CentOS 7 or later:</span></span>

1. <span data-ttu-id="63131-302">Erstellen Sie die Apache-Konfigurationsdatei.</span><span class="sxs-lookup"><span data-stu-id="63131-302">Create the Apache configuration file.</span></span> <span data-ttu-id="63131-303">Das folgenden Beispiel zeigt eine vereinfachte Konfigurationsdatei (`blazorapp.config`):</span><span class="sxs-lookup"><span data-stu-id="63131-303">The following example is a simplified configuration file (`blazorapp.config`):</span></span>

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

1. <span data-ttu-id="63131-304">Platzieren Sie die Apache-Konfigurationsdatei in das `/etc/httpd/conf.d/`-Verzeichnis, das das standardmäßige Apache-Konfigurationsverzeichnis in CentOS 7 ist.</span><span class="sxs-lookup"><span data-stu-id="63131-304">Place the Apache configuration file into the `/etc/httpd/conf.d/` directory, which is the default Apache configuration directory in CentOS 7.</span></span>

1. <span data-ttu-id="63131-305">Platzieren Sie die Dateien der App in das `/var/www/blazorapp`-Verzeichnis (den Speicherort, der für `DocumentRoot` in der Konfigurationsdatei angegeben ist).</span><span class="sxs-lookup"><span data-stu-id="63131-305">Place the app's files into the `/var/www/blazorapp` directory (the location specified to `DocumentRoot` in the configuration file).</span></span>

1. <span data-ttu-id="63131-306">Starten Sie den Apache-Dienst neu.</span><span class="sxs-lookup"><span data-stu-id="63131-306">Restart the Apache service.</span></span>

<span data-ttu-id="63131-307">Weitere Informationen finden Sie unter [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) und [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span><span class="sxs-lookup"><span data-stu-id="63131-307">For more information, see [`mod_mime`](https://httpd.apache.org/docs/2.4/mod/mod_mime.html) and [`mod_deflate`](https://httpd.apache.org/docs/current/mod/mod_deflate.html).</span></span>

### <a name="github-pages"></a><span data-ttu-id="63131-308">GitHub-Seiten</span><span class="sxs-lookup"><span data-stu-id="63131-308">GitHub Pages</span></span>

<span data-ttu-id="63131-309">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine `wwwroot/404.html`-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite `index.html` verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-309">To handle URL rewrites, add a `wwwroot/404.html` file with a script that handles redirecting the request to the `index.html` page.</span></span> <span data-ttu-id="63131-310">Ein Beispiel finden Sie im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span><span class="sxs-lookup"><span data-stu-id="63131-310">For an example, see the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages):</span></span>

* [`wwwroot/404.html`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/wwwroot/404.html)
* <span data-ttu-id="63131-311">[Livesite](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span><span class="sxs-lookup"><span data-stu-id="63131-311">[Live site](https://stevesandersonms.github.io/BlazorOnGitHubPages/))</span></span>

<span data-ttu-id="63131-312">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, aktualisieren Sie das `<base>`-Tag in `wwwroot/index.html`.</span><span class="sxs-lookup"><span data-stu-id="63131-312">When using a project site instead of an organization site, update the `<base>` tag in `wwwroot/index.html`.</span></span> <span data-ttu-id="63131-313">Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Schrägstrich fest (z. B. `/my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="63131-313">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `/my-repository/`).</span></span> <span data-ttu-id="63131-314">Im GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) wird die Basis `href` bei der Veröffentlichung durch die [ Konfigurationsdatei `.github/workflows/main.yml`](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml) aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="63131-314">In the [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages), the base `href` is updated at publish by the [`.github/workflows/main.yml` configuration file](https://github.com/SteveSandersonMS/BlazorOnGitHubPages/blob/master/.github/workflows/main.yml).</span></span>

> [!NOTE]
> <span data-ttu-id="63131-315">Das GitHub-Repository [SteveSandersonMS/BlazorOnGitHubPages](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) befindet sich nicht im Besitz der .NET Foundation oder von Microsoft und wird auch nicht von diesen verwaltet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63131-315">The [SteveSandersonMS/BlazorOnGitHubPages GitHub repository](https://github.com/SteveSandersonMS/BlazorOnGitHubPages) isn't owned, maintained, or supported by the .NET Foundation or Microsoft.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="63131-316">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="63131-316">Host configuration values</span></span>

<span data-ttu-id="63131-317">[Blazor WebAssembly-Apps](xref:blazor/hosting-models#blazor-webassembly) können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Runtime in der Entwicklungsumgebung akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="63131-317">[Blazor WebAssembly apps](xref:blazor/hosting-models#blazor-webassembly) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="63131-318">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="63131-318">Content root</span></span>

<span data-ttu-id="63131-319">Mit dem Argument `--contentroot` wird der absolute Pfad zum Verzeichnis festgelegt, das die Inhaltsdateien (das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root)) der App enthält.</span><span class="sxs-lookup"><span data-stu-id="63131-319">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files ([content root](xref:fundamentals/index#content-root)).</span></span> <span data-ttu-id="63131-320">In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.</span><span class="sxs-lookup"><span data-stu-id="63131-320">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="63131-321">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="63131-321">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="63131-322">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="63131-322">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="63131-323">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="63131-323">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="63131-324">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="63131-324">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="63131-325">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="63131-325">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="63131-326">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63131-326">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="63131-327">Basispfad</span><span class="sxs-lookup"><span data-stu-id="63131-327">Path base</span></span>

<span data-ttu-id="63131-328">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem relativen URL-Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="63131-328">The `--pathbase` argument sets the app base path for an app run locally with a non-root relative URL path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="63131-329">In den folgenden Beispielen ist `/relative-URL-path` die Pfadbasis der App.</span><span class="sxs-lookup"><span data-stu-id="63131-329">In the following examples, `/relative-URL-path` is the app's path base.</span></span> <span data-ttu-id="63131-330">Weitere Informationen finden Sie unter [Basispfad einer App](xref:blazor/host-and-deploy/index#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="63131-330">For more information, see [App base path](xref:blazor/host-and-deploy/index#app-base-path).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="63131-331">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="63131-331">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="63131-332">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="63131-332">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="63131-333">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="63131-333">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="63131-334">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="63131-334">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --pathbase=/relative-URL-path
  ```

* <span data-ttu-id="63131-335">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="63131-335">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="63131-336">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="63131-336">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/relative-URL-path"
  ```

* <span data-ttu-id="63131-337">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="63131-337">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="63131-338">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63131-338">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --pathbase=/relative-URL-path
  ```

### <a name="urls"></a><span data-ttu-id="63131-339">URLs</span><span class="sxs-lookup"><span data-stu-id="63131-339">URLs</span></span>

<span data-ttu-id="63131-340">Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.</span><span class="sxs-lookup"><span data-stu-id="63131-340">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="63131-341">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="63131-341">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="63131-342">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="63131-342">From the app's directory, execute:</span></span>

  ```dotnetcli
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="63131-343">Fügen Sie der Datei `launchSettings.json` der App im Profil **IIS Express** einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="63131-343">Add an entry to the app's `launchSettings.json` file in the **IIS Express** profile.</span></span> <span data-ttu-id="63131-344">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="63131-344">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="63131-345">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="63131-345">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="63131-346">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei `launchSettings.json` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63131-346">Setting the argument in the Visual Studio property page adds the argument to the `launchSettings.json` file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

::: moniker range=">= aspnetcore-5.0"

## <a name="configure-the-trimmer"></a><span data-ttu-id="63131-347">Konfigurieren des Trimmers</span><span class="sxs-lookup"><span data-stu-id="63131-347">Configure the Trimmer</span></span>

<span data-ttu-id="63131-348">Blazor führt auf jedem Releasebuild eine IL-Kürzung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="63131-348">Blazor performs Intermediate Language (IL) trimming on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="63131-349">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-trimmer>.</span><span class="sxs-lookup"><span data-stu-id="63131-349">For more information, see <xref:blazor/host-and-deploy/configure-trimmer>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="configure-the-linker"></a><span data-ttu-id="63131-350">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="63131-350">Configure the Linker</span></span>

<span data-ttu-id="63131-351">Blazor führt auf jedem Releasebuild eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="63131-351">Blazor performs Intermediate Language (IL) linking on each Release build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="63131-352">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="63131-352">For more information, see <xref:blazor/host-and-deploy/configure-linker>.</span></span>

::: moniker-end

## <a name="custom-boot-resource-loading"></a><span data-ttu-id="63131-353">Benutzerdefiniertes Laden von Startressourcen</span><span class="sxs-lookup"><span data-stu-id="63131-353">Custom boot resource loading</span></span>

<span data-ttu-id="63131-354">Eine Blazor WebAssembly-App kann mit der Funktion `loadBootResource` initialisiert werden, um den integrierten Mechanismus zum Laden von Startressourcen zu überschreiben.</span><span class="sxs-lookup"><span data-stu-id="63131-354">A Blazor WebAssembly app can be initialized with the `loadBootResource` function to override the built-in boot resource loading mechanism.</span></span> <span data-ttu-id="63131-355">Verwenden Sie `loadBootResource` für die folgenden Szenarien:</span><span class="sxs-lookup"><span data-stu-id="63131-355">Use `loadBootResource` for the following scenarios:</span></span>

* <span data-ttu-id="63131-356">Benutzern erlauben, statische Ressourcen wie Zeitzonendaten oder `dotnet.wasm` aus einem CDN zu laden.</span><span class="sxs-lookup"><span data-stu-id="63131-356">Allow users to load static resources, such as timezone data or `dotnet.wasm` from a CDN.</span></span>
* <span data-ttu-id="63131-357">Komprimierte Assemblys über eine HTTP-Anforderung laden und auf dem Client für Hosts dekomprimieren, die das Abrufen komprimierter Inhalte vom Server nicht unterstützen.</span><span class="sxs-lookup"><span data-stu-id="63131-357">Load compressed assemblies using an HTTP request and decompress them on the client for hosts that don't support fetching compressed contents from the server.</span></span>
* <span data-ttu-id="63131-358">Ressourcen per Alias an einen anderen Namen umleiten, indem jede `fetch`-Anforderung an einen neuen Namen umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-358">Alias resources to a different name by redirecting each `fetch` request to a new name.</span></span>

<span data-ttu-id="63131-359">`loadBootResource`-Parameter sind in der folgenden Tabelle aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="63131-359">`loadBootResource` parameters appear in the following table.</span></span>

| <span data-ttu-id="63131-360">Parameter</span><span class="sxs-lookup"><span data-stu-id="63131-360">Parameter</span></span>    | <span data-ttu-id="63131-361">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="63131-361">Description</span></span> |
| ------------ | ----------- |
| `type`       | <span data-ttu-id="63131-362">Der Typ der Ressource.</span><span class="sxs-lookup"><span data-stu-id="63131-362">The type of the resource.</span></span> <span data-ttu-id="63131-363">Zulässige Typen: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span><span class="sxs-lookup"><span data-stu-id="63131-363">Permissable types: `assembly`, `pdb`, `dotnetjs`, `dotnetwasm`, `timezonedata`</span></span> |
| `name`       | <span data-ttu-id="63131-364">Der Name der Ressource.</span><span class="sxs-lookup"><span data-stu-id="63131-364">The name of the resource.</span></span> |
| `defaultUri` | <span data-ttu-id="63131-365">Der relative oder absolute URI der Ressource.</span><span class="sxs-lookup"><span data-stu-id="63131-365">The relative or absolute URI of the resource.</span></span> |
| `integrity`  | <span data-ttu-id="63131-366">Die Integritätszeichenfolge, die den erwarteten Inhalt in der Antwort darstellt.</span><span class="sxs-lookup"><span data-stu-id="63131-366">The integrity string representing the expected content in the response.</span></span> |

<span data-ttu-id="63131-367">`loadBootResource` gibt eines der folgenden Elemente zurück, um den Ladevorgang außer Kraft zu setzen:</span><span class="sxs-lookup"><span data-stu-id="63131-367">`loadBootResource` returns any of the following to override the loading process:</span></span>

* <span data-ttu-id="63131-368">URI-Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="63131-368">URI string.</span></span> <span data-ttu-id="63131-369">Im Beispiel unten (`wwwroot/index.html`) werden die folgenden Dateien von einem CDN unter `https://my-awesome-cdn.com/` bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="63131-369">In the following example (`wwwroot/index.html`), the following files are served from a CDN at `https://my-awesome-cdn.com/`:</span></span>

  * `dotnet.*.js`
  * `dotnet.wasm`
  * <span data-ttu-id="63131-370">Zeitzonendaten</span><span class="sxs-lookup"><span data-stu-id="63131-370">Timezone data</span></span>

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

* <span data-ttu-id="63131-371">`Promise<Response>`.</span><span class="sxs-lookup"><span data-stu-id="63131-371">`Promise<Response>`.</span></span> <span data-ttu-id="63131-372">Übergeben Sie den Parameter `integrity` in einem Header, um das Standardverhalten bei der Integritätsprüfung beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="63131-372">Pass the `integrity` parameter in a header to retain the default integrity-checking behavior.</span></span>

  <span data-ttu-id="63131-373">Das folgende Beispiel (`wwwroot/index.html`) fügt einen benutzerdefinierten HTTP-Header den ausgehenden Anforderungen hinzu und übergibt den Parameter `integrity` an den Aufruf von `fetch`:</span><span class="sxs-lookup"><span data-stu-id="63131-373">The following example (`wwwroot/index.html`) adds a custom HTTP header to the outbound requests and passes the `integrity` parameter through to the `fetch` call:</span></span>
  
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

* <span data-ttu-id="63131-374">`null`/`undefined`, was zum standardmäßigen Ladeverhalten führt.</span><span class="sxs-lookup"><span data-stu-id="63131-374">`null`/`undefined`, which results in the default loading behavior.</span></span>

<span data-ttu-id="63131-375">Externe Quellen müssen die erforderlichen CORS-Header für Browser zurückgeben, um das ursprungsübergreifende Laden von Ressourcen zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="63131-375">External sources must return the required CORS headers for browsers to allow the cross-origin resource loading.</span></span> <span data-ttu-id="63131-376">CDNs stellen in der Regel die erforderlichen Header standardmäßig zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="63131-376">CDNs usually provide the required headers by default.</span></span>

<span data-ttu-id="63131-377">Sie müssen nur für benutzerdefinierte Verhaltensmuster Typen angeben.</span><span class="sxs-lookup"><span data-stu-id="63131-377">You only need to specify types for custom behaviors.</span></span> <span data-ttu-id="63131-378">Nicht mit `loadBootResource` angegebene Typen werden vom Framework entsprechend ihres Standardladeverhaltens geladen.</span><span class="sxs-lookup"><span data-stu-id="63131-378">Types not specified to `loadBootResource` are loaded by the framework per their default loading behaviors.</span></span>

## <a name="change-the-filename-extension-of-dll-files"></a><span data-ttu-id="63131-379">Ändern der Dateinamenerweiterung von DLL-Dateien</span><span class="sxs-lookup"><span data-stu-id="63131-379">Change the filename extension of DLL files</span></span>

<span data-ttu-id="63131-380">Falls Sie die Dateinamenerweiterungen der veröffentlichten `.dll`-Dateien der App ändern müssen, befolgen Sie die Anleitung in diesem Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="63131-380">In case you have a need to change the filename extensions of the app's published `.dll` files, follow the guidance in this section.</span></span>

<span data-ttu-id="63131-381">Verwenden Sie nach Veröffentlichen der App ein Shellskript oder eine DevOps-Buildpipeline, um `.dll`-Dateien umzubenennen und eine andere Dateierweiterung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="63131-381">After publishing the app, use a shell script or DevOps build pipeline to rename `.dll` files to use a different file extension.</span></span> <span data-ttu-id="63131-382">Verwenden Sie die `.dll`-Dateien im `wwwroot`-Verzeichnis der veröffentlichten Ausgabe der App (z. B. `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span><span class="sxs-lookup"><span data-stu-id="63131-382">Target the `.dll` files in the `wwwroot` directory of the app's published output (for example, `{CONTENT ROOT}/bin/Release/netstandard2.1/publish/wwwroot`).</span></span>

<span data-ttu-id="63131-383">In den folgenden Beispielen werden `.dll`-Dateien so umbenannt, dass die Dateierweiterung `.bin` verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63131-383">In the following examples, `.dll` files are renamed to use the `.bin` file extension.</span></span>

<span data-ttu-id="63131-384">Unter Windows:</span><span class="sxs-lookup"><span data-stu-id="63131-384">On Windows:</span></span>

```powershell
dir .\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content .\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content .\_framework\blazor.boot.json
```

<span data-ttu-id="63131-385">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-385">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content .\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content .\service-worker-assets.js
```

<span data-ttu-id="63131-386">Unter Linux oder macOS:</span><span class="sxs-lookup"><span data-stu-id="63131-386">On Linux or macOS:</span></span>

```console
for f in _framework/_bin/*; do mv "$f" "`echo $f | sed -e 's/\.dll\b/.bin/g'`"; done
sed -i 's/\.dll"/.bin"/g' _framework/blazor.boot.json
```

<span data-ttu-id="63131-387">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-387">If service worker assets are also in use, add the following command:</span></span>

```console
sed -i 's/\.dll"/.bin"/g' service-worker-assets.js
```
   
<span data-ttu-id="63131-388">Um eine andere Dateierweiterung als `.bin` zu verwenden, ersetzen Sie `.bin` in den vorherigen Befehlen.</span><span class="sxs-lookup"><span data-stu-id="63131-388">To use a different file extension than `.bin`, replace `.bin` in the preceding commands.</span></span>

<span data-ttu-id="63131-389">Um die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br` zu verarbeiten, wenden Sie einen der folgenden Ansätze an:</span><span class="sxs-lookup"><span data-stu-id="63131-389">To address the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files, adopt either of the following approaches:</span></span>

* <span data-ttu-id="63131-390">Entfernen Sie die komprimierten Dateien `blazor.boot.json.gz` und `blazor.boot.json.br`.</span><span class="sxs-lookup"><span data-stu-id="63131-390">Remove the compressed `blazor.boot.json.gz` and `blazor.boot.json.br` files.</span></span> <span data-ttu-id="63131-391">Bei diesem Ansatz ist die Komprimierung deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="63131-391">Compression is disabled with this approach.</span></span>
* <span data-ttu-id="63131-392">Komprimieren Sie die aktualisierte Datei `blazor.boot.json` erneut.</span><span class="sxs-lookup"><span data-stu-id="63131-392">Recompress the updated `blazor.boot.json` file.</span></span>

<span data-ttu-id="63131-393">Die vorherige Anleitung gilt auch, wenn Service Worker-Assets verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="63131-393">The preceding guidance also applies when service worker assets are in use.</span></span> <span data-ttu-id="63131-394">Entfernen Sie die Dateien `wwwroot/service-worker-assets.js.br` und `wwwroot/service-worker-assets.js.gz`, oder komprimieren Sie diese erneut.</span><span class="sxs-lookup"><span data-stu-id="63131-394">Remove or recompress `wwwroot/service-worker-assets.js.br` and `wwwroot/service-worker-assets.js.gz`.</span></span> <span data-ttu-id="63131-395">Andernfalls treten bei Dateiintegritätsprüfungen im Browser Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="63131-395">Otherwise, file integrity checks fail in the browser.</span></span>

<span data-ttu-id="63131-396">Im folgenden Windows-Beispiel wird ein PowerShell-Skript verwendet, das sich im Stammverzeichnis des Projekts befindet.</span><span class="sxs-lookup"><span data-stu-id="63131-396">The following Windows example uses a PowerShell script placed at the root of the project.</span></span>

<span data-ttu-id="63131-397">`ChangeDLLExtensions.ps1:`:</span><span class="sxs-lookup"><span data-stu-id="63131-397">`ChangeDLLExtensions.ps1:`:</span></span>

```powershell
param([string]$filepath,[string]$tfm)
dir $filepath\bin\Release\$tfm\wwwroot\_framework\_bin | rename-item -NewName { $_.name -replace ".dll\b",".bin" }
((Get-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json
Remove-Item $filepath\bin\Release\$tfm\wwwroot\_framework\blazor.boot.json.gz
```

<span data-ttu-id="63131-398">Wenn auch Service Worker-Assets verwendet werden, fügen Sie den folgenden Befehl hinzu:</span><span class="sxs-lookup"><span data-stu-id="63131-398">If service worker assets are also in use, add the following command:</span></span>

```powershell
((Get-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js -Raw) -replace '.dll"','.bin"') | Set-Content $filepath\bin\Release\$tfm\wwwroot\service-worker-assets.js
```

<span data-ttu-id="63131-399">In der Projektdatei wird das Skript nach Veröffentlichung der App ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="63131-399">In the project file, the script is run after publishing the app:</span></span>

```xml
<Target Name="ChangeDLLFileExtensions" AfterTargets="Publish" Condition="'$(Configuration)'=='Release'">
  <Exec Command="powershell.exe -command &quot;&amp; { .\ChangeDLLExtensions.ps1 '$(SolutionDir)' '$(TargetFramework)'}&quot;" />
</Target>
```

> [!NOTE]
> <span data-ttu-id="63131-400">Für das Umbenennen und verzögerte Laden derselben Assemblys finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files> eine Anleitung.</span><span class="sxs-lookup"><span data-stu-id="63131-400">When renaming and lazy loading the same assemblies, see the guidance in <xref:blazor/webassembly-lazy-load-assemblies#onnavigateasync-events-and-renamed-assembly-files>.</span></span>
