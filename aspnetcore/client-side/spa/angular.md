---
title: Verwenden der Angular-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für Angular und die Angular-CLI vertraut machen.
monikerRange: '>= aspnetcore-2.1'
ms.author: stevesa
ms.custom: mvc
ms.date: 02/06/2020
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
uid: spa/angular
ms.openlocfilehash: 2fff0d60b71bbbab9347dbe74cad023264247388
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93054566"
---
# <a name="use-the-angular-project-template-with-aspnet-core"></a><span data-ttu-id="98103-103">Verwenden der Angular-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="98103-103">Use the Angular project template with ASP.NET Core</span></span>

<span data-ttu-id="98103-104">Die aktualisierte Angular-Projektvorlage stellt einen geeigneten Anfangspunkt für ASP.NET Core-Apps dar, die Angular und die Angular-CLI für die Implementierung einer umfangreichen, clientseitigen Benutzerschnittstelle (User Interface, UI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="98103-104">The updated Angular project template provides a convenient starting point for ASP.NET Core apps using Angular and the Angular CLI to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="98103-105">Mit der Vorlage können Sie ein ASP.NET Core-Projekt, das als API-Back-End fungieren soll, sowie ein Angular-CLI-Projekt erstellen, das als Benutzerschnittstelle fungieren soll.</span><span class="sxs-lookup"><span data-stu-id="98103-105">The template is equivalent to creating an ASP.NET Core project to act as an API backend and an Angular CLI project to act as a UI.</span></span> <span data-ttu-id="98103-106">Die Vorlage bietet den Vorteil, dass beide Projekte in einem App-Projekt gehostet werden können.</span><span class="sxs-lookup"><span data-stu-id="98103-106">The template offers the convenience of hosting both project types in a single app project.</span></span> <span data-ttu-id="98103-107">Folglich kann das App-Projekt als eine einzelne Einheit erstellt und veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="98103-107">Consequently, the app project can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="98103-108">Erstellen einer neuen App</span><span class="sxs-lookup"><span data-stu-id="98103-108">Create a new app</span></span>

<span data-ttu-id="98103-109">Wenn ASP.NET Core 2.1 auf Ihrem Computer installiert ist, müssen Sie die Vorlage für Angular-Projekte nicht installieren.</span><span class="sxs-lookup"><span data-stu-id="98103-109">If you have ASP.NET Core 2.1 installed, there's no need to install the Angular project template.</span></span>

<span data-ttu-id="98103-110">Erstellen Sie über eine Eingabeaufforderung mit dem Befehl `dotnet new angular` in einem leeren Verzeichnis ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="98103-110">Create a new project from a command prompt using the command `dotnet new angular` in an empty directory.</span></span> <span data-ttu-id="98103-111">Mit den folgenden Befehlen wird die App beispielsweise im Verzeichnis *my-new-app* erstellt und zu diesem Verzeichnis gewechselt:</span><span class="sxs-lookup"><span data-stu-id="98103-111">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```dotnetcli
dotnet new angular -o my-new-app
cd my-new-app
```

<span data-ttu-id="98103-112">Führen Sie die App über Visual Studio oder die .NET Core CLI aus:</span><span class="sxs-lookup"><span data-stu-id="98103-112">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98103-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98103-113">Visual Studio</span></span>](#tab/visual-studio/)

<span data-ttu-id="98103-114">Öffnen Sie die generierte *CSPROJ*-Datei, und führen Sie die App von dort wie gewohnt aus.</span><span class="sxs-lookup"><span data-stu-id="98103-114">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="98103-115">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="98103-115">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="98103-116">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="98103-116">Subsequent builds are much faster.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="98103-117">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="98103-117">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="98103-118">Stellen Sie sicher, dass Sie über eine Umgebungsvariable mit dem Namen `ASPNETCORE_Environment` und dem Wert `Development` verfügen.</span><span class="sxs-lookup"><span data-stu-id="98103-118">Ensure you have an environment variable called `ASPNETCORE_Environment` with a value of `Development`.</span></span> <span data-ttu-id="98103-119">Führen Sie unter Windows (bei Eingabeaufforderungen außerhalb von PowerShell) `SET ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="98103-119">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="98103-120">Führen Sie unter Linux oder macOS `export ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="98103-120">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="98103-121">Führen Sie [dotnet build](/dotnet/core/tools/dotnet-build) aus, um zu überprüfen, ob die App ordnungsgemäß erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="98103-121">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify the app builds correctly.</span></span> <span data-ttu-id="98103-122">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="98103-122">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="98103-123">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="98103-123">Subsequent builds are much faster.</span></span>

<span data-ttu-id="98103-124">Führen Sie [dotnet run](/dotnet/core/tools/dotnet-run) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="98103-124">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span> <span data-ttu-id="98103-125">Eine der folgenden ähnelnde Meldung wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="98103-125">A message similar to the following is logged:</span></span>

```console
Now listening on: http://localhost:<port>
```

<span data-ttu-id="98103-126">Navigieren Sie in einem Browser zu dieser URL.</span><span class="sxs-lookup"><span data-stu-id="98103-126">Navigate to this URL in a browser.</span></span>

> [!WARNING]
> <span data-ttu-id="98103-127">Die App startet im Hintergrund eine Instanz des Angular-CLI-Servers.</span><span class="sxs-lookup"><span data-stu-id="98103-127">The app starts up an instance of the Angular CLI server in the background.</span></span> <span data-ttu-id="98103-128">Eine der folgenden ähnelnde Meldung wird angezeigt: *NG Live Development Server lauscht an localhost:&lt;otherport&gt;, öffnen Sie einen Browser für http://localhost:&lt;otherport&gt;/* .</span><span class="sxs-lookup"><span data-stu-id="98103-128">A message similar to the following is logged: *NG Live Development Server is listening on localhost:&lt;otherport&gt;, open a browser to http://localhost:&lt;otherport&gt;/*.</span></span> <span data-ttu-id="98103-129">Ignorieren Sie die Meldung&mdash;Es handelt sich **nicht** um die URL für die kombinierte ASP.NET Core und Angular-LI-App.</span><span class="sxs-lookup"><span data-stu-id="98103-129">Ignore this message&mdash;it's **not** the URL for the combined ASP.NET Core and Angular CLI app.</span></span>

---

<span data-ttu-id="98103-130">Mit der Projektvorlage werden eine ASP.NET Core-App und eine Angular-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="98103-130">The project template creates an ASP.NET Core app and an Angular app.</span></span> <span data-ttu-id="98103-131">Die ASP.NET Core-App ist zur Verwendung für den Datenzugriff, die Autorisierung und weitere serverseitige Belange vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="98103-131">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="98103-132">Die Angular-App, die sich im Unterverzeichnis *ClientApp* befindet, ist für sämtliche Belange der Benutzerschnittstelle vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="98103-132">The Angular app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="98103-133">Hinzufügen von Seiten, Images, Formatvorlagen, Modulen usw.</span><span class="sxs-lookup"><span data-stu-id="98103-133">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="98103-134">Das Verzeichnis *ClientApp* enthält eine standardmäßige Angular-CLI-App.</span><span class="sxs-lookup"><span data-stu-id="98103-134">The *ClientApp* directory contains a standard Angular CLI app.</span></span> <span data-ttu-id="98103-135">Weitere Informationen finden Sie in der offiziellen [Angular-Dokumentation](https://angular.io).</span><span class="sxs-lookup"><span data-stu-id="98103-135">See the official [Angular documentation](https://angular.io) for more information.</span></span>

<span data-ttu-id="98103-136">Die Angular-App, die mit dieser Vorlage erstellt wurde, und die App, die von der Angular-CLI selbst (über `ng new`) erstellt wurde, unterscheiden sich geringfügig. Die Funktionen der App sind jedoch unverändert.</span><span class="sxs-lookup"><span data-stu-id="98103-136">There are slight differences between the Angular app created by this template and the one created by Angular CLI itself (via `ng new`); however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="98103-137">Die mit der Vorlage erstellte App enthält ein auf [Bootstrap](https://getbootstrap.com/) basiertes Layout und ein Beispiel für grundlegendes Routing.</span><span class="sxs-lookup"><span data-stu-id="98103-137">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="run-ng-commands"></a><span data-ttu-id="98103-138">Ausführen von ng-Befehlen</span><span class="sxs-lookup"><span data-stu-id="98103-138">Run ng commands</span></span>

<span data-ttu-id="98103-139">Wechseln Sie über eine Eingabeaufforderung zum Unterverzeichnis *ClientApp*:</span><span class="sxs-lookup"><span data-stu-id="98103-139">In a command prompt, switch to the *ClientApp* subdirectory:</span></span>

```console
cd ClientApp
```

<span data-ttu-id="98103-140">Wenn das `ng`-Tool global installiert ist, können Sie seine Befehle ausführen.</span><span class="sxs-lookup"><span data-stu-id="98103-140">If you have the `ng` tool installed globally, you can run any of its commands.</span></span> <span data-ttu-id="98103-141">Sie können z.B. `ng lint`, `ng test` oder einen beliebigen anderen [Angular-CLI-Befehl](https://angular.io/cli) ausführen.</span><span class="sxs-lookup"><span data-stu-id="98103-141">For example, you can run `ng lint`, `ng test`, or any of the other [Angular CLI commands](https://angular.io/cli).</span></span> <span data-ttu-id="98103-142">Allerdings muss `ng serve` nicht ausgeführt werden, da Ihre ASP.NET Core-App den serverseitigen und den clientseitigen Teil Ihrer App bedient.</span><span class="sxs-lookup"><span data-stu-id="98103-142">There's no need to run `ng serve` though, because your ASP.NET Core app deals with serving both server-side and client-side parts of your app.</span></span> <span data-ttu-id="98103-143">Intern verwendet sie `ng serve` bei der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="98103-143">Internally, it uses `ng serve` in development.</span></span>

<span data-ttu-id="98103-144">Wenn Sie das `ng`-Tool nicht installiert haben, führen Sie stattdessen `npm run ng` aus.</span><span class="sxs-lookup"><span data-stu-id="98103-144">If you don't have the `ng` tool installed, run `npm run ng` instead.</span></span> <span data-ttu-id="98103-145">Sie können beispielsweise `npm run ng lint``npm run ng test` ausführen.</span><span class="sxs-lookup"><span data-stu-id="98103-145">For example, you can run `npm run ng lint` or `npm run ng test`.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="98103-146">NPM-Pakete installieren</span><span class="sxs-lookup"><span data-stu-id="98103-146">Install npm packages</span></span>

<span data-ttu-id="98103-147">Verwenden Sie für die Installation von npm-Paketen von Drittanbietern eine Eingabeaufforderung im Unterverzeichnis *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="98103-147">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="98103-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="98103-148">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="98103-149">Veröffentlichen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="98103-149">Publish and deploy</span></span>

<span data-ttu-id="98103-150">Bei der Entwicklung wird die App in einem für Entwickler optimierten Modus ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="98103-150">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="98103-151">JavaScript-Pakete enthalten beispielsweise Quellzuordnungen (damit Sie beim Debuggen Ihren ursprünglichen TypeScript-Code anzeigen können).</span><span class="sxs-lookup"><span data-stu-id="98103-151">For example, JavaScript bundles include source maps (so that when debugging, you can see your original TypeScript code).</span></span> <span data-ttu-id="98103-152">Die App überwacht TypeScript-, HTML- und CSS-Dateiänderungen auf dem Datenträger und führt bei Feststellung dieser Dateiänderungen automatisch eine Neukompilierung und ein erneutes Laden durch.</span><span class="sxs-lookup"><span data-stu-id="98103-152">The app watches for TypeScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="98103-153">Stellen Sie bei der Produktion eine für Leistung optimierte Version Ihrer App bereit.</span><span class="sxs-lookup"><span data-stu-id="98103-153">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="98103-154">Dies erfolgt gemäß der Konfiguration automatisch.</span><span class="sxs-lookup"><span data-stu-id="98103-154">This is configured to happen automatically.</span></span> <span data-ttu-id="98103-155">Beim Veröffentlichen gibt die Buildkonfiguration einen verkleinerten, Ahead-of-Time-kompilierten Build (AOT-Build) Ihres clientseitigen Codes aus.</span><span class="sxs-lookup"><span data-stu-id="98103-155">When you publish, the build configuration emits a minified, ahead-of-time (AoT) compiled build of your client-side code.</span></span> <span data-ttu-id="98103-156">Im Gegensatz zum Entwicklungsbuild muss Node.js beim Produktionsbuild nicht auf dem Server installiert sein (sofern Sie das serverseitige Rendern nicht aktiviert haben).</span><span class="sxs-lookup"><span data-stu-id="98103-156">Unlike the development build, the production build doesn't require Node.js to be installed on the server (unless you have enabled server-side rendering (SSR)).</span></span>

<span data-ttu-id="98103-157">Sie können [ASP.NET Core-Standardhosting- und -bereitstellungsmethoden](xref:host-and-deploy/index) verwenden.</span><span class="sxs-lookup"><span data-stu-id="98103-157">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-ng-serve-independently"></a><span data-ttu-id="98103-158">Unabhängiges Ausführen von „ng serve“</span><span class="sxs-lookup"><span data-stu-id="98103-158">Run "ng serve" independently</span></span>

<span data-ttu-id="98103-159">Das Projekt ist so konfiguriert, dass die eigene Instanz des Angular-CLI-Servers im Hintergrund gestartet wird, wenn die ASP.NET Core-App im Entwicklungsmodus gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="98103-159">The project is configured to start its own instance of the Angular CLI server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="98103-160">Dies ist nützlich, da es bedeutet, dass Sie keinen separaten Server manuell ausführen müssen.</span><span class="sxs-lookup"><span data-stu-id="98103-160">This is convenient because you don't have to run a separate server manually.</span></span>

<span data-ttu-id="98103-161">Bei diesem Standardsetup gibt es einen Nachteil.</span><span class="sxs-lookup"><span data-stu-id="98103-161">There's a drawback to this default setup.</span></span> <span data-ttu-id="98103-162">Jedes Mal, wenn Sie Ihren C#-Code ändern und Ihre ASP.NET Core-App neu gestartet werden muss, wird auch der Angular-CLI-Server neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="98103-162">Each time you modify your C# code and your ASP.NET Core app needs to restart, the Angular CLI server restarts.</span></span> <span data-ttu-id="98103-163">Die Sicherung wird nach ca. 10 Sekunden gestartet.</span><span class="sxs-lookup"><span data-stu-id="98103-163">Around 10 seconds is required to start back up.</span></span> <span data-ttu-id="98103-164">Wenn Sie Ihren C#-Code häufig bearbeiten und nicht warten möchten, bis der Angular-CLI-Server neu gestartet wurde, können Sie den Angular-CLI-Server unabhängig vom ASP.NET Core-Prozess extern ausführen.</span><span class="sxs-lookup"><span data-stu-id="98103-164">If you're making frequent C# code edits and don't want to wait for Angular CLI to restart, run the Angular CLI server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="98103-165">Gehen Sie hierzu wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="98103-165">To do so:</span></span>

1. <span data-ttu-id="98103-166">Wechseln Sie in einer Eingabeaufforderung zu dem Unterverzeichnis *ClientApp*, und starten Sie den Angular-CLI-Entwicklungsserver:</span><span class="sxs-lookup"><span data-stu-id="98103-166">In a command prompt, switch to the *ClientApp* subdirectory, and launch the Angular CLI development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

    > [!IMPORTANT]
    > <span data-ttu-id="98103-167">Starten Sie den Angular-CLI-Entwicklungsserver mit `npm start` und nicht mit `ng serve`, damit die Konfiguration in *package.json* gewahrt wird.</span><span class="sxs-lookup"><span data-stu-id="98103-167">Use `npm start` to launch the Angular CLI development server, not `ng serve`, so that the configuration in *package.json* is respected.</span></span> <span data-ttu-id="98103-168">Um zusätzliche Parameter an den Angular-CLI-Server zu übergeben, fügen Sie diese der entsprechenden `scripts`-Zeile in der Datei *package.json* hinzu.</span><span class="sxs-lookup"><span data-stu-id="98103-168">To pass additional parameters to the Angular CLI server, add them to the relevant `scripts` line in your *package.json* file.</span></span>

2. <span data-ttu-id="98103-169">Ändern Sie Ihre ASP.NET Core-App so, dass die externe Angular-CLI-Instanz verwendet wird, anstatt eine eigene Instanz zu starten.</span><span class="sxs-lookup"><span data-stu-id="98103-169">Modify your ASP.NET Core app to use the external Angular CLI instance instead of launching one of its own.</span></span> <span data-ttu-id="98103-170">Ersetzen Sie den `spa.UseAngularCliServer`-Aufruf in Ihrer *Startklasse* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="98103-170">In your *Startup* class, replace the `spa.UseAngularCliServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:4200");
    ```

<span data-ttu-id="98103-171">Wenn Sie Ihre ASP.NET Core-App starten, wird kein Angular-CLI-Server gestartet.</span><span class="sxs-lookup"><span data-stu-id="98103-171">When you start your ASP.NET Core app, it won't launch an Angular CLI server.</span></span> <span data-ttu-id="98103-172">Stattdessen wird die Instanz verwendet, die Sie manuell gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="98103-172">The instance you started manually is used instead.</span></span> <span data-ttu-id="98103-173">Dadurch kann sie schneller gestartet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="98103-173">This enables it to start and restart faster.</span></span> <span data-ttu-id="98103-174">Sie müssen also nicht mehr jedes Mal warten, bis Angular-CLI Ihre Client-App neu erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="98103-174">It's no longer waiting for Angular CLI to rebuild your client app each time.</span></span>

### <a name="pass-data-from-net-code-into-typescript-code"></a><span data-ttu-id="98103-175">Übergeben von Daten aus .NET Code in TypeScript-Code</span><span class="sxs-lookup"><span data-stu-id="98103-175">Pass data from .NET code into TypeScript code</span></span>

<span data-ttu-id="98103-176">Während des SSR empfiehlt es sich, per Anforderung Daten aus der ASP.NET Core-App an die Angular-App zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="98103-176">During SSR, you might want to pass per-request data from your ASP.NET Core app into your Angular app.</span></span> <span data-ttu-id="98103-177">Beispielsweise könnten Sie cookieinformationen übergeben oder etwas aus einer Datenbank lesen.</span><span class="sxs-lookup"><span data-stu-id="98103-177">For example, you could pass cookie information or something read from a database.</span></span> <span data-ttu-id="98103-178">Bearbeiten Sie hierzu die *Startup*-Klasse.</span><span class="sxs-lookup"><span data-stu-id="98103-178">To do this, edit your *Startup* class.</span></span> <span data-ttu-id="98103-179">Im Rückruf für `UseSpaPrerendering` legen Sie einen Wert für `options.SupplyData` fest, z.B.:</span><span class="sxs-lookup"><span data-stu-id="98103-179">In the callback for `UseSpaPrerendering`, set a value for `options.SupplyData` such as the following:</span></span>

```csharp
options.SupplyData = (context, data) =>
{
    // Creates a new value called isHttpsRequest that's passed to TypeScript code
    data["isHttpsRequest"] = context.Request.IsHttps;
};
```

<span data-ttu-id="98103-180">Der `SupplyData`-Rückruf ermöglicht Ihnen, beliebige JSON-serialisierbare Daten per Anforderung zu übergeben (z.B. Zeichenfolgen, boolesche Werte oder Zahlen).</span><span class="sxs-lookup"><span data-stu-id="98103-180">The `SupplyData` callback lets you pass arbitrary, per-request, JSON-serializable data (for example, strings, booleans, or numbers).</span></span> <span data-ttu-id="98103-181">Der *main.server.ts*-Code erhält diese als `params.data`.</span><span class="sxs-lookup"><span data-stu-id="98103-181">Your *main.server.ts* code receives this as `params.data`.</span></span> <span data-ttu-id="98103-182">Im vorangehenden Codebeispiel wird z.B. ein boolescher Wert als `params.data.isHttpsRequest` an den `createServerRenderer`-Rückruf übergeben.</span><span class="sxs-lookup"><span data-stu-id="98103-182">For example, the preceding code sample passes a boolean value as `params.data.isHttpsRequest` into the `createServerRenderer` callback.</span></span> <span data-ttu-id="98103-183">Sie können dies auf eine beliebige Art und Weise, die von Angular unterstützt wird, an andere Teile Ihrer App übergeben.</span><span class="sxs-lookup"><span data-stu-id="98103-183">You can pass this to other parts of your app in any way supported by Angular.</span></span> <span data-ttu-id="98103-184">Sehen Sie beispielsweise, wie *main.server.ts* den `BASE_URL`-Wert an eine beliebige Komponente übergibt, deren Konstruktor für den Empfang deklariert wurde.</span><span class="sxs-lookup"><span data-stu-id="98103-184">For example, see how *main.server.ts* passes the `BASE_URL` value to any component whose constructor is declared to receive it.</span></span>

### <a name="drawbacks-of-ssr"></a><span data-ttu-id="98103-185">Nachteile von SSR</span><span class="sxs-lookup"><span data-stu-id="98103-185">Drawbacks of SSR</span></span>

<span data-ttu-id="98103-186">Nicht alle Apps profitieren von SSR.</span><span class="sxs-lookup"><span data-stu-id="98103-186">Not all apps benefit from SSR.</span></span> <span data-ttu-id="98103-187">Der Hauptvorteil ist die wahrgenommene Leistung.</span><span class="sxs-lookup"><span data-stu-id="98103-187">The primary benefit is perceived performance.</span></span> <span data-ttu-id="98103-188">Besucher, die Ihre App über eine langsame Netzwerkverbindung oder auf langsamen mobilen Geräten erreichen, finden die ursprüngliche UI schnell, auch wenn das Abrufen und Analysieren der JavaScript-Pakete einige Zeit dauert.</span><span class="sxs-lookup"><span data-stu-id="98103-188">Visitors reaching your app over a slow network connection or on slow mobile devices see the initial UI quickly, even if it takes a while to fetch or parse the JavaScript bundles.</span></span> <span data-ttu-id="98103-189">Allerdings werden viele SPAs hauptsächlich über schnelle, interne Unternehmensnetzwerke auf schnellen Computern verwendet, auf denen die App nahezu umgehend angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="98103-189">However, many SPAs are mainly used over fast, internal company networks on fast computers where the app appears almost instantly.</span></span>

<span data-ttu-id="98103-190">Gleichzeitig hat die Aktivierung von SSR zahlreiche Nachteile.</span><span class="sxs-lookup"><span data-stu-id="98103-190">At the same time, there are significant drawbacks to enabling SSR.</span></span> <span data-ttu-id="98103-191">Sie erhöht die Komplexität des Entwicklungsprozesses.</span><span class="sxs-lookup"><span data-stu-id="98103-191">It adds complexity to your development process.</span></span> <span data-ttu-id="98103-192">Der Code muss in zwei verschiedenen Umgebungen ausgeführt werden: clientseitig und serverseitig (über ASP.NET Core in einer Node.js-Umgebung aufgerufen).</span><span class="sxs-lookup"><span data-stu-id="98103-192">Your code must run in two different environments: client-side and server-side (in a Node.js environment invoked from ASP.NET Core).</span></span> <span data-ttu-id="98103-193">Nachfolgend sind einige Dinge aufgeführt, die Sie berücksichtigen sollten:</span><span class="sxs-lookup"><span data-stu-id="98103-193">Here are some things to bear in mind:</span></span>

* <span data-ttu-id="98103-194">Das SSR erfordert eine Node.js-Installation auf Produktionsservern.</span><span class="sxs-lookup"><span data-stu-id="98103-194">SSR requires a Node.js installation on your production servers.</span></span> <span data-ttu-id="98103-195">Dies ist für einige Bereitstellungsszenarios, wie z.B. Azure App Services, automatisch der Fall, für andere, wie etwa Azure Service Fabric, dagegen nicht.</span><span class="sxs-lookup"><span data-stu-id="98103-195">This is automatically the case for some deployment scenarios, such as Azure App Services, but not for others, such as Azure Service Fabric.</span></span>
* <span data-ttu-id="98103-196">Bei Aktivierung des `BuildServerSideRenderer`-Buildflags veröffentlicht das *node_modules*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="98103-196">Enabling the `BuildServerSideRenderer` build flag causes your *node_modules* directory to publish.</span></span> <span data-ttu-id="98103-197">Dieser Ordner enthält mehr als 20.000 Dateien, was die Bereitstellungszeit erhöht.</span><span class="sxs-lookup"><span data-stu-id="98103-197">This folder contains 20,000+ files, which increases deployment time.</span></span>
* <span data-ttu-id="98103-198">Zum Ausführen von Code in einer Node.js-Umgebung kann nicht davon ausgegangen werden, dass Browser-spezifische JavaScript-APIs, wie z.B. `window` oder `localStorage`, vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="98103-198">To run your code in a Node.js environment, it can't rely on the existence of browser-specific JavaScript APIs such as `window` or `localStorage`.</span></span> <span data-ttu-id="98103-199">Wenn Ihr Code (oder eine Bibliothek eines Drittanbieters, auf die Sie verweisen) versucht, diese APIs zu verwenden, erhalten Sie einen Fehler während des SSR.</span><span class="sxs-lookup"><span data-stu-id="98103-199">If your code (or some third-party library you reference) tries to use these APIs, you'll get an error during SSR.</span></span> <span data-ttu-id="98103-200">Verwenden Sie beispielsweise jQuery nicht, da es häufig auf Browser-spezifische APIs verweist.</span><span class="sxs-lookup"><span data-stu-id="98103-200">For example, don't use jQuery because it references browser-specific APIs in many places.</span></span> <span data-ttu-id="98103-201">Um Fehler auszuschließen, müssen Sie das SSR vermeiden oder aber Browser-spezifische APIs oder Bibliotheken vermeiden.</span><span class="sxs-lookup"><span data-stu-id="98103-201">To prevent errors, you must either avoid SSR or avoid browser-specific APIs or libraries.</span></span> <span data-ttu-id="98103-202">Sie können alle Aufrufe dieser APIs in Überprüfungen einschließen, um sicherzustellen, dass sie nicht während des SSR aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="98103-202">You can wrap any calls to such APIs in checks to ensure they aren't invoked during SSR.</span></span> <span data-ttu-id="98103-203">Verwenden Sie z.B. eine Überprüfung wie die im folgenden JavaScript- oder TypeScript-Code enthaltene:</span><span class="sxs-lookup"><span data-stu-id="98103-203">For example, use a check such as the following in JavaScript or TypeScript code:</span></span>

    ```javascript
    if (typeof window !== 'undefined') {
        // Call browser-specific APIs here
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="98103-204">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="98103-204">Additional resources</span></span>

* <xref:security/authentication/identity/spa>
