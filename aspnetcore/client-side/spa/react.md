---
title: Verwenden der React-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für React und create-react-app vertraut machen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 03/07/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: spa/react
ms.openlocfilehash: 6c3539d96329489189f49a3af0c718791824be6b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054449"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="628c3-103">Verwenden der React-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="628c3-103">Use the React project template with ASP.NET Core</span></span>

<span data-ttu-id="628c3-104">Die aktualisierte React-Projektvorlage stellt einen geeigneten Anfangspunkt für ASP.NET Core-Apps dar, die React und CRA-Konventionen ([create-react-app](https://github.com/facebookincubator/create-react-app)) für die Implementierung einer umfangreichen, clientseitigen Benutzerschnittstelle (User Interface, UI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="628c3-104">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="628c3-105">Mit der Vorlage können ein ASP.NET Core-Projekt, das als API-Back-End fungieren soll, und ein CRA React-Projekt, das als Benutzeroberfläche fungieren soll, erstellt werden. Sie bietet jedoch den Komfort, dass beide Projekte in einem einzigen App-Projekt gehostet werden, das als eine Einheit erstellt und veröffentlicht werden kann.</span><span class="sxs-lookup"><span data-stu-id="628c3-105">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

<span data-ttu-id="628c3-106">Die React-Projektvorlage ist nicht für serverseitiges Rendering (SSR) vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="628c3-106">The React project template isn't meant for server-side rendering (SSR).</span></span> <span data-ttu-id="628c3-107">Für SSR mit React und Node.js sollten Sie [Next.js](https://github.com/zeit/next.js/) oder [Razzle](https://github.com/jaredpalmer/razzle) in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="628c3-107">For SSR with React and Node.js, consider [Next.js](https://github.com/zeit/next.js/) or [Razzle](https://github.com/jaredpalmer/razzle).</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="628c3-108">Erstellen einer neuen App</span><span class="sxs-lookup"><span data-stu-id="628c3-108">Create a new app</span></span>

<span data-ttu-id="628c3-109">Wenn ASP.NET Core 2.1 auf Ihrem Computer installiert ist, müssen Sie die Vorlage für React-Projekte nicht installieren.</span><span class="sxs-lookup"><span data-stu-id="628c3-109">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

<span data-ttu-id="628c3-110">Erstellen Sie über eine Eingabeaufforderung mit dem Befehl `dotnet new react` in einem leeren Verzeichnis ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="628c3-110">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="628c3-111">Mit den folgenden Befehlen wird die App beispielsweise im Verzeichnis *my-new-app* erstellt und zu diesem Verzeichnis gewechselt:</span><span class="sxs-lookup"><span data-stu-id="628c3-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="628c3-112">Führen Sie die App über Visual Studio oder die .NET Core CLI aus:</span><span class="sxs-lookup"><span data-stu-id="628c3-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="628c3-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="628c3-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="628c3-114">Öffnen Sie die generierte *CSPROJ* -Datei, und führen Sie die App von dort wie gewohnt aus.</span><span class="sxs-lookup"><span data-stu-id="628c3-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="628c3-115">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="628c3-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="628c3-116">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="628c3-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="628c3-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="628c3-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="628c3-118">Stellen Sie sicher, dass Sie über eine Umgebungsvariable mit dem Namen `ASPNETCORE_Environment` und dem Wert `Development` verfügen.</span><span class="sxs-lookup"><span data-stu-id="628c3-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="628c3-119">Führen Sie unter Windows (bei Eingabeaufforderungen außerhalb von PowerShell) `SET ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="628c3-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="628c3-120">Führen Sie unter Linux oder macOS `export ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="628c3-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="628c3-121">Führen Sie [dotnet build](/dotnet/core/tools/dotnet-build) aus, um zu überprüfen, ob Ihre App ordnungsgemäß erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="628c3-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="628c3-122">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="628c3-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="628c3-123">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="628c3-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="628c3-124">Führen Sie [dotnet run](/dotnet/core/tools/dotnet-run) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="628c3-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="628c3-125">Mit der Projektvorlage werden eine ASP.NET Core-App und eine React-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="628c3-125">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="628c3-126">Die ASP.NET Core-App ist für die Verwendung für den Datenzugriff, die Autorisierung und weitere serverseitige Belange vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="628c3-126">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="628c3-127">Die React-App, die sich im Unterverzeichnis *ClientApp* befindet, ist für sämtliche Belange der Benutzeroberfläche vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="628c3-127">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="628c3-128">Hinzufügen von Seiten, Images, Formatvorlagen, Modulen usw.</span><span class="sxs-lookup"><span data-stu-id="628c3-128">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="628c3-129">Das Verzeichnis *ClientApp* ist eine CRA-React-Standard-App.</span><span class="sxs-lookup"><span data-stu-id="628c3-129">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="628c3-130">Weitere Informationen finden Sie in der offiziellen [CRA-Dokumentation](https://create-react-app.dev/docs/getting-started/).</span><span class="sxs-lookup"><span data-stu-id="628c3-130">See the official [CRA documentation](https://create-react-app.dev/docs/getting-started/) for more information.</span></span>

<span data-ttu-id="628c3-131">Zwischen der React-App, die mit dieser Vorlage erstellt wurde, und der App, die von CRA selbst erstellt wurde, gibt es jedoch leichte Unterschiede; die Funktionen der App sind jedoch unverändert.</span><span class="sxs-lookup"><span data-stu-id="628c3-131">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="628c3-132">Die mit der Vorlage erstellte App enthält ein auf [Bootstrap](https://getbootstrap.com/) basiertes Layout und ein Beispiel für grundlegendes Routing.</span><span class="sxs-lookup"><span data-stu-id="628c3-132">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="628c3-133">NPM-Pakete installieren</span><span class="sxs-lookup"><span data-stu-id="628c3-133">Install npm packages</span></span>

<span data-ttu-id="628c3-134">Verwenden Sie für die Installation von npm-Paketen von Drittanbietern eine Eingabeaufforderung im Unterverzeichnis *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="628c3-134">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="628c3-135">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="628c3-135">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="628c3-136">Veröffentlichen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="628c3-136">Publish and deploy</span></span>

<span data-ttu-id="628c3-137">Bei der Entwicklung wird die App in einem für Entwickler optimierten Modus ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="628c3-137">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="628c3-138">JavaScript-Pakete enthalten beispielsweise Quellzuordnungen (damit Sie beim Debuggen Ihren ursprünglichen Quellcode anzeigen können).</span><span class="sxs-lookup"><span data-stu-id="628c3-138">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="628c3-139">Die App überwacht JavaScript-, HTML- und CSS-Dateiänderungen auf dem Datenträger und führt bei Feststellung dieser Dateiänderungen automatisch eine Neukompilierung und ein erneutes Laden durch.</span><span class="sxs-lookup"><span data-stu-id="628c3-139">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="628c3-140">Stellen Sie bei der Produktion eine für Leistung optimierte Version Ihrer App bereit.</span><span class="sxs-lookup"><span data-stu-id="628c3-140">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="628c3-141">Dies erfolgt gemäß der Konfiguration automatisch.</span><span class="sxs-lookup"><span data-stu-id="628c3-141">This is configured to happen automatically.</span></span> <span data-ttu-id="628c3-142">Beim Veröffentlichen gibt die Buildkonfiguration einen verkleinerten, transpilierten Build Ihres clientseitigen Codes aus.</span><span class="sxs-lookup"><span data-stu-id="628c3-142">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="628c3-143">Im Gegensatz zum Entwicklungsbuild muss Node.js beim Produktionsbuild nicht auf dem Server installiert sein.</span><span class="sxs-lookup"><span data-stu-id="628c3-143">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="628c3-144">Sie können [ASP.NET Core-Standardhosting- und -bereitstellungsmethoden](xref:host-and-deploy/index) verwenden.</span><span class="sxs-lookup"><span data-stu-id="628c3-144">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="628c3-145">Unabhängiges Ausführen des CRA-Servers</span><span class="sxs-lookup"><span data-stu-id="628c3-145">Run the CRA server independently</span></span>

<span data-ttu-id="628c3-146">Das Projekt ist so konfiguriert, dass die eigene Instanz des CRA-Entwicklungsservers im Hintergrund gestartet wird, wenn die ASP.NET Core-App im Entwicklungsmodus gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="628c3-146">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="628c3-147">Dies ist nützlich, da es bedeutet, dass Sie keinen separaten Server manuell ausführen müssen.</span><span class="sxs-lookup"><span data-stu-id="628c3-147">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="628c3-148">Bei diesem Standardsetup gibt es einen Nachteil.</span><span class="sxs-lookup"><span data-stu-id="628c3-148">There's a drawback to this default setup.</span></span> <span data-ttu-id="628c3-149">Jedes Mal, wenn Sie Ihren C#-Code ändern und Ihre ASP.NET Core-App neu gestartet werden muss, wird auch der CRA-Server neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="628c3-149">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="628c3-150">Es dauert einige Sekunden, bis der Sicherungsvorgang gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="628c3-150">A few seconds are required to start back up.</span></span> <span data-ttu-id="628c3-151">Wenn Sie Ihren C#-Code häufig bearbeiten und nicht warten möchten, bis der CRA-Server neu gestartet wurde, können Sie den CRA-Server extern ausführen, unabhängig vom ASP.NET Core-Prozess.</span><span class="sxs-lookup"><span data-stu-id="628c3-151">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="628c3-152">Gehen Sie hierzu wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="628c3-152">To do so:</span></span>

1. <span data-ttu-id="628c3-153">Fügen Sie dem Unterverzeichnis *ClientApp* eine *ENV* -Datei mit der folgenden Einstellung hinzu:</span><span class="sxs-lookup"><span data-stu-id="628c3-153">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```

    <span data-ttu-id="628c3-154">Dadurch wird verhindert, dass der Webbrowser geöffnet wird, wenn der CRA-Server extern gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="628c3-154">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="628c3-155">Wechseln Sie in einer Eingabeaufforderung zu dem Unterverzeichnis *ClientApp* , und starten Sie den CRA-Bereitstellungsserver:</span><span class="sxs-lookup"><span data-stu-id="628c3-155">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="628c3-156">Ändern Sie Ihre ASP.NET Core-App so, dass die externe CRA-Serverinstanz verwendet wird, statt eine eigene Instanz zu starten.</span><span class="sxs-lookup"><span data-stu-id="628c3-156">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="628c3-157">Ersetzen Sie den `spa.UseReactDevelopmentServer`-Aufruf in Ihrer *Startklasse* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="628c3-157">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="628c3-158">Wenn Sie Ihre ASP.NET Core-App starten, wird kein CRA-Server gestartet.</span><span class="sxs-lookup"><span data-stu-id="628c3-158">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="628c3-159">Stattdessen wird die Instanz verwendet, die Sie manuell gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="628c3-159">The instance you started manually is used instead.</span></span> <span data-ttu-id="628c3-160">Dadurch kann sie schneller gestartet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="628c3-160">This enables it to start and restart faster.</span></span> <span data-ttu-id="628c3-161">So müssen Sie nicht mehr jedes Mal warten, bis Ihre React-App neu erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="628c3-161">It's no longer waiting for your React app to rebuild each time.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="628c3-162">„Serverseitiges Rendering“ ist kein unterstütztes Feature dieser Vorlage.</span><span class="sxs-lookup"><span data-stu-id="628c3-162">"Server-side rendering" is not a supported feature of this template.</span></span> <span data-ttu-id="628c3-163">Unser Ziel hinsichtlich dieser Vorlage ist es, die Parität mit „create-react-app“ zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="628c3-163">Our goal with this template is to meet parity with "create-react-app".</span></span> <span data-ttu-id="628c3-164">Daher werden Szenarios und Features, die nicht in einem „create-react-app“-Projekt (wie SSR) enthalten sind, nicht unterstützt und stehen als Übung für die Benutzer zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="628c3-164">As such, scenarios and features not included in a "create-react-app" project (such as SSR) are not supported and are left as an exercise for the user.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="628c3-165">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="628c3-165">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
