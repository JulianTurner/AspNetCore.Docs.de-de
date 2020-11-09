---
title: 'Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack'
author: ssougnez
description: In diesem Tutorial konfigurieren Sie Webpack zum Bündeln und Erstellen einer ASP.NET Core SignalR-Web-App, deren Client in TypeScript geschrieben ist.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/10/2020
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
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: 949276bf4aae33c3af3fd1b8219a83868095f378
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056841"
---
# <a name="use-aspnet-core-no-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="c9251-103">Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack</span><span class="sxs-lookup"><span data-stu-id="c9251-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="c9251-104">Von [Sébastien Sougnez](https://twitter.com/ssougnez) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c9251-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c9251-105">Mit [Webpack](https://webpack.js.org/) können Entwickler clientseitige Ressourcen einer Web-App bündeln und erstellen.</span><span class="sxs-lookup"><span data-stu-id="c9251-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="c9251-106">In diesem Tutorial wird veranschaulicht, wie Webpack in einer ASP.NET Core SignalR-Web-App verwendet wird, deren Client in [TypeScript](https://www.typescriptlang.org/) geschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="c9251-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="c9251-107">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c9251-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c9251-108">Aufbauen einer einfachen ASP.NET Core SignalR-App</span><span class="sxs-lookup"><span data-stu-id="c9251-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="c9251-109">Konfigurieren des SignalR-TypeScript-Clients</span><span class="sxs-lookup"><span data-stu-id="c9251-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="c9251-110">Konfigurieren einer Buildpipeline mithilfe von Webpack</span><span class="sxs-lookup"><span data-stu-id="c9251-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="c9251-111">Konfigurieren des SignalR-Servers</span><span class="sxs-lookup"><span data-stu-id="c9251-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="c9251-112">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="c9251-112">Enable communication between client and server</span></span>

<span data-ttu-id="c9251-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c9251-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c9251-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c9251-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9251-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="c9251-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c9251-117">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-117">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c9251-118">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c9251-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c9251-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c9251-121">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-121">.NET Core SDK 3.0 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c9251-122">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c9251-123">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c9251-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c9251-124">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="c9251-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9251-126">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c9251-127">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="c9251-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c9251-128">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c9251-129">Starten Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c9251-129">Launch Visual Studio.</span></span> <span data-ttu-id="c9251-130">Wählen Sie im Startfenster die Option **Ohne Code fortfahren** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-130">At the start window, select **Continue without code**.</span></span>
1. <span data-ttu-id="c9251-131">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="c9251-131">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c9251-132">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-132">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c9251-133">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="c9251-133">Click the up arrow to move the entry to the second position in the list, and select **OK**.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c9251-135">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c9251-135">Visual Studio configuration is complete.</span></span>

1. <span data-ttu-id="c9251-136">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt** , und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-136">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c9251-137">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c9251-137">Select **Next**.</span></span>
1. <span data-ttu-id="c9251-138">Benennen Sie das Projekt *SignalRWebPack* , und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c9251-138">Name the project *SignalRWebPack* , and select **Create**.</span></span>
1. <span data-ttu-id="c9251-139">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 3.1* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-139">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.1* from the framework selector drop-down.</span></span> <span data-ttu-id="c9251-140">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-140">Select the **Empty** template, and select **Create**.</span></span>

<span data-ttu-id="c9251-141">`Microsoft.TypeScript.MSBuild`-Paket zu Projekt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="c9251-141">Add the `Microsoft.TypeScript.MSBuild` package to the project:</span></span>

1. <span data-ttu-id="c9251-142">Klicken Sie im **Projektmappen-Explorer** (rechter Bereich) mit der rechten Maustaste auf den Projektknoten, und wählen Sie **NuGet-Pakete verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-142">In **Solution Explorer** (right pane), right-click the project node and select **Manage NuGet Packages**.</span></span> <span data-ttu-id="c9251-143">Suchen Sie auf der Registerkarte **Durchsuchen** nach `Microsoft.TypeScript.MSBuild`, und klicken Sie dann rechts auf **Installieren** , um das Paket zu installieren.</span><span class="sxs-lookup"><span data-stu-id="c9251-143">In the **Browse** tab, search for `Microsoft.TypeScript.MSBuild`, and then click **Install** on the right to install the package.</span></span>

<span data-ttu-id="c9251-144">Visual Studio fügt das NuGet-Paket unter dem Knoten **Abhängigkeiten** im **Projektmappen-Explorer** hinzu, wodurch die TypeScript-Kompilierung im Projekt aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-144">Visual Studio adds the NuGet package under the **Dependencies** node in **Solution Explorer** , enabling TypeScript compilation in the project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-145">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-145">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9251-146">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-146">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
code -r SignalRWebPack
```

* <span data-ttu-id="c9251-147">Durch den Befehl `dotnet new` wird eine leere ASP.NET Core-Web-App in einem *SignalRWebPack* -Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-147">The `dotnet new` command creates an empty ASP.NET Core web app in a *SignalRWebPack* directory.</span></span>
* <span data-ttu-id="c9251-148">Der Befehl `code` öffnet den Ordner *SignalRWebPack* in der aktuellen Instanz von Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="c9251-148">The `code` command opens the *SignalRWebPack* folder in the current instance of Visual Studio Code.</span></span>

<span data-ttu-id="c9251-149">Führen Sie im **integrierten Terminal** den folgenden .NET Core-CLI-Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-149">Run the following .NET Core CLI command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet add package Microsoft.TypeScript.MSBuild
```

<span data-ttu-id="c9251-150">Über den vorherigen Befehl wird das Paket [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) hinzugefügt, wodurch die TypeScript-Kompilierung im Projekt aktiviert wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-150">The preceding command adds the [Microsoft.TypeScript.MSBuild](https://www.nuget.org/packages/Microsoft.TypeScript.MSBuild/) package, enabling TypeScript compilation in the project.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c9251-151">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="c9251-151">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c9251-152">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c9251-152">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c9251-153">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json* -Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="c9251-153">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c9251-154">Fügen Sie der *package.json* -Datei die hervorgehobene Eigenschaft hinzu, und speichern Sie die Dateiänderungen:</span><span class="sxs-lookup"><span data-stu-id="c9251-154">Add the highlighted property to the *package.json* file and save the file changes:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c9251-155">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="c9251-155">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c9251-156">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="c9251-156">Install the required npm packages.</span></span> <span data-ttu-id="c9251-157">Führen Sie den folgenden Befehl über den Projektstamm aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-157">Run the following command from the project root:</span></span>

    ```console
    npm i -D -E clean-webpack-plugin@3.0.0 css-loader@3.4.2 html-webpack-plugin@3.2.0 mini-css-extract-plugin@0.9.0 ts-loader@6.2.1 typescript@3.7.5 webpack@4.41.5 webpack-cli@3.3.10
    ```

    <span data-ttu-id="c9251-158">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="c9251-158">Some command details to note:</span></span>

    * <span data-ttu-id="c9251-159">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="c9251-159">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c9251-160">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="c9251-160">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c9251-161">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json* -Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-161">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c9251-162">Beispielsweise wird `"webpack": "4.41.5"` anstelle von `"webpack": "^4.41.5"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-162">For example, `"webpack": "4.41.5"` is used instead of `"webpack": "^4.41.5"`.</span></span> <span data-ttu-id="c9251-163">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="c9251-163">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c9251-164">Ausführliche Informationen finden Sie in der Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="c9251-164">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c9251-165">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-165">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c9251-166">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="c9251-166">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c9251-167">`build`: Bündelt die clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="c9251-167">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c9251-168">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-168">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c9251-169">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="c9251-169">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c9251-170">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="c9251-170">Only use `build` in development.</span></span>
    * <span data-ttu-id="c9251-171">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus</span><span class="sxs-lookup"><span data-stu-id="c9251-171">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c9251-172">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="c9251-172">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c9251-173">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="c9251-173">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c9251-174">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-174">Create a file named *webpack.config.js* , in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="c9251-175">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="c9251-175">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c9251-176">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="c9251-176">Some configuration details to note:</span></span>

    * <span data-ttu-id="c9251-177">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="c9251-177">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c9251-178">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="c9251-178">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c9251-179">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="c9251-179">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c9251-180">Erstellen Sie ein neues *src* -Verzeichnis im Projektstamm, um die clientseitigen Ressourcen des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c9251-180">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c9251-181">Erstellen Sie *src/index.html* mit dem folgenden Markup.</span><span class="sxs-lookup"><span data-stu-id="c9251-181">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="c9251-182">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="c9251-182">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c9251-183">Erstellen Sie ein neues *src/css* -Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="c9251-183">Create a new *src/css* directory.</span></span> <span data-ttu-id="c9251-184">Es dient als Speicherort für die *CSS* -Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c9251-184">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c9251-185">Erstellen Sie *src/css/main.css* mit der folgenden CSS-Spezifikation:</span><span class="sxs-lookup"><span data-stu-id="c9251-185">Create *src/css/main.css* with the following CSS:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="c9251-186">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="c9251-186">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c9251-187">Erstellen Sie *src/tsconfig.json* mit der folgenden JSON-Spezifikation:</span><span class="sxs-lookup"><span data-stu-id="c9251-187">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="c9251-188">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="c9251-188">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c9251-189">Erstellen Sie *src/index.ts* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-189">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c9251-190">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="c9251-190">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c9251-191">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das `tbMessage`-Textfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-191">`keyup`: This event fires when the user types in the `tbMessage`textbox.</span></span> <span data-ttu-id="c9251-192">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="c9251-192">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c9251-193">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="c9251-193">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c9251-194">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c9251-194">The `send` function is called.</span></span>

## <a name="configure-the-app"></a><span data-ttu-id="c9251-195">Konfigurieren der App</span><span class="sxs-lookup"><span data-stu-id="c9251-195">Configure the app</span></span>

1. <span data-ttu-id="c9251-196">Fügen Sie `Startup.Configure` Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) hinzu.</span><span class="sxs-lookup"><span data-stu-id="c9251-196">In `Startup.Configure`, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=9-10)]

   <span data-ttu-id="c9251-197">Der vorherige Code ermöglicht dem Server, die *index.html* -Datei zu finden und bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="c9251-197">The preceding code allows the server to locate and serve the *index.html* file.</span></span>  <span data-ttu-id="c9251-198">Die Datei wird bereitgestellt, wenn der Benutzer die vollständige URL oder nur die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-198">The file is served whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c9251-199">Ordnen Sie am Ende von `Startup.Configure` eine */hub* -Route dem `ChatHub`-Hub zu.</span><span class="sxs-lookup"><span data-stu-id="c9251-199">At the end of `Startup.Configure`, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c9251-200">Ersetzen Sie den Code, der *Hello World!* anzeigt,</span><span class="sxs-lookup"><span data-stu-id="c9251-200">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="c9251-201">durch folgende Zeile:</span><span class="sxs-lookup"><span data-stu-id="c9251-201">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="c9251-202">In `Startup.ConfigureServices`, rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) auf.</span><span class="sxs-lookup"><span data-stu-id="c9251-202">In `Startup.ConfigureServices`, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c9251-203">Erstellen Sie im Projektstamm *SignalRWebPack/* ein neues Verzeichnis namens *Hubs* , um den SignalR-Hub zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c9251-203">Create a new directory named *Hubs* in the project root *SignalRWebPack/* to store the SignalR hub.</span></span>

1. <span data-ttu-id="c9251-204">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs* :</span><span class="sxs-lookup"><span data-stu-id="c9251-204">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c9251-205">Fügen Sie die folgende `using`-Anweisung am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c9251-205">Add the following `using` statement at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c9251-206">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="c9251-206">Enable client and server communication</span></span>

<span data-ttu-id="c9251-207">Die App stellt zurzeit ein Basisformular zum Senden von Nachrichten bereit, ist jedoch sonst nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="c9251-207">The app currently displays a basic form to send messages, but is not yet functional.</span></span> <span data-ttu-id="c9251-208">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="c9251-208">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c9251-209">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-209">Run the following command at the project root:</span></span>

    ```console
    npm i @microsoft/signalr @types/node
    ```

    <span data-ttu-id="c9251-210">Mit dem vorangestellten Komma wird Folgendes installiert:</span><span class="sxs-lookup"><span data-stu-id="c9251-210">The preceding command installs:</span></span>

     * <span data-ttu-id="c9251-211">Der [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="c9251-211">The [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>
     * <span data-ttu-id="c9251-212">Die TypeScript-Typdefinitionen für Node.js, die die Überprüfung zur Kompilierzeit von Node.js-Typen ermöglichen</span><span class="sxs-lookup"><span data-stu-id="c9251-212">The TypeScript type definitions for Node.js, which enables compile-time checking of Node.js types.</span></span>

1. <span data-ttu-id="c9251-213">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="c9251-213">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c9251-214">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="c9251-214">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c9251-215">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="c9251-215">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c9251-216">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="c9251-216">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c9251-217">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="c9251-217">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c9251-218">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="c9251-218">Each message has a specific name.</span></span> <span data-ttu-id="c9251-219">Beispielsweise können Nachrichten mit dem Namen `messageReceived` die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="c9251-219">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c9251-220">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="c9251-220">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c9251-221">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="c9251-221">Any number of message names can be listened to.</span></span> <span data-ttu-id="c9251-222">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="c9251-222">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c9251-223">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-223">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c9251-224">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-224">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c9251-225">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="c9251-225">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c9251-226">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="c9251-226">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c9251-227">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c9251-227">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c9251-228">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="c9251-228">The method's first parameter is the message name.</span></span> <span data-ttu-id="c9251-229">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="c9251-229">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c9251-230">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-230">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c9251-231">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="c9251-231">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c9251-232">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c9251-232">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c9251-233">Fügen Sie der Klasse `ChatHub` die Methode `NewMessage` hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9251-233">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c9251-234">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="c9251-234">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c9251-235">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c9251-235">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c9251-236">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="c9251-236">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c9251-237">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="c9251-237">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c9251-238">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="c9251-238">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c9251-239">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c9251-239">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c9251-240">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-240">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c9251-241">Testen der App</span><span class="sxs-lookup"><span data-stu-id="c9251-241">Test the app</span></span>

<span data-ttu-id="c9251-242">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="c9251-242">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-243">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-243">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c9251-244">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-244">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c9251-245">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-245">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c9251-246">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="c9251-246">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c9251-247">Klicken Sie auf **Debuggen** > **Starten ohne Debugging** , um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="c9251-247">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c9251-248">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-248">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="c9251-249">Wenn Sie Kompilierungsfehlermeldungen erhalten, versuchen Sie, die Projektmappe zu schließen und erneut zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="c9251-249">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="c9251-250">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="c9251-250">Open another browser instance (any browser).</span></span> <span data-ttu-id="c9251-251">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="c9251-251">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c9251-252">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="c9251-252">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c9251-253">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-253">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c9251-255">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="c9251-255">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c9251-256">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="c9251-256">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c9251-257">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="c9251-257">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c9251-258">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="c9251-258">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c9251-259">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-259">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c9251-260">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="c9251-260">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c9251-261">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="c9251-261">Open another browser instance (any browser).</span></span> <span data-ttu-id="c9251-262">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="c9251-262">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c9251-263">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="c9251-263">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c9251-264">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-264">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="c9251-266">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c9251-266">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-267">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-267">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c9251-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="c9251-268">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c9251-269">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-269">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* <span data-ttu-id="c9251-270">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c9251-270">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-271">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-271">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c9251-272">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-272">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c9251-273">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-273">.NET Core SDK 2.2 or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)
* [<span data-ttu-id="c9251-274">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="c9251-274">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* <span data-ttu-id="c9251-275">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c9251-275">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="c9251-276">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="c9251-276">Create the ASP.NET Core web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-277">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-277">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c9251-278">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-278">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="c9251-279">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="c9251-279">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="c9251-280">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-280">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="c9251-281">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="c9251-281">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="c9251-282">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-282">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="c9251-283">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="c9251-283">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="c9251-285">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="c9251-285">Visual Studio configuration is completed.</span></span> <span data-ttu-id="c9251-286">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c9251-286">It's time to create the project.</span></span>

1. <span data-ttu-id="c9251-287">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt** , und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-287">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="c9251-288">Benennen Sie das Projekt *SignalRWebPack* , und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c9251-288">Name the project *SignalRWebPack* , and select **Create**.</span></span>
1. <span data-ttu-id="c9251-289">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 2.2* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-289">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="c9251-290">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-290">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="c9251-292">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-292">Run the following command in the **Integrated Terminal** :</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="c9251-293">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack* -Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-293">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="c9251-294">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="c9251-294">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="c9251-295">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c9251-295">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="c9251-296">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json* -Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="c9251-296">Run the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="c9251-297">Fügen Sie der *package.json* -Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9251-297">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="c9251-298">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="c9251-298">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="c9251-299">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="c9251-299">Install the required npm packages.</span></span> <span data-ttu-id="c9251-300">Führen Sie den folgenden Befehl über den Projektstamm aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-300">Run the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="c9251-301">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="c9251-301">Some command details to note:</span></span>

    * <span data-ttu-id="c9251-302">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="c9251-302">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="c9251-303">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="c9251-303">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="c9251-304">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json* -Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-304">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="c9251-305">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-305">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="c9251-306">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="c9251-306">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="c9251-307">Ausführliche Informationen finden Sie in der Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="c9251-307">See the [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="c9251-308">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-308">Replace the `scripts` property of the *package.json* file with the following code:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="c9251-309">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="c9251-309">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="c9251-310">`build`: Bündelt die clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="c9251-310">`build`: Bundles the client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="c9251-311">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-311">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="c9251-312">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="c9251-312">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="c9251-313">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="c9251-313">Only use `build` in development.</span></span>
    * <span data-ttu-id="c9251-314">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus</span><span class="sxs-lookup"><span data-stu-id="c9251-314">`release`: Bundles the client-side resources in production mode.</span></span>
    * <span data-ttu-id="c9251-315">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="c9251-315">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="c9251-316">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="c9251-316">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="c9251-317">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-317">Create a file named *webpack.config.js* in the project root, with the following code:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="c9251-318">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="c9251-318">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="c9251-319">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="c9251-319">Some configuration details to note:</span></span>

    * <span data-ttu-id="c9251-320">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="c9251-320">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="c9251-321">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="c9251-321">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="c9251-322">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="c9251-322">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="c9251-323">Erstellen Sie ein neues *src* -Verzeichnis im Projektstamm, um die clientseitigen Ressourcen des Projekts zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c9251-323">Create a new *src* directory in the project root to store the project's client-side assets.</span></span>

1. <span data-ttu-id="c9251-324">Erstellen Sie *src/index.html* mit dem folgenden Markup.</span><span class="sxs-lookup"><span data-stu-id="c9251-324">Create *src/index.html* with the following markup.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="c9251-325">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="c9251-325">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="c9251-326">Erstellen Sie ein neues *src/css* -Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="c9251-326">Create a new *src/css* directory.</span></span> <span data-ttu-id="c9251-327">Es dient als Speicherort für die *CSS* -Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c9251-327">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="c9251-328">Erstellen Sie *src/css/main.css* mit dem folgenden Markup:</span><span class="sxs-lookup"><span data-stu-id="c9251-328">Create *src/css/main.css* with the following markup:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="c9251-329">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="c9251-329">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="c9251-330">Erstellen Sie *src/tsconfig.json* mit der folgenden JSON-Spezifikation:</span><span class="sxs-lookup"><span data-stu-id="c9251-330">Create *src/tsconfig.json* with the following JSON:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="c9251-331">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="c9251-331">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="c9251-332">Erstellen Sie *src/index.ts* mit dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c9251-332">Create *src/index.ts* with the following code:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="c9251-333">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="c9251-333">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="c9251-334">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das `tbMessage`-Textfeld eingibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-334">`keyup`: This event fires when the user types in the `tbMessage` textbox.</span></span> <span data-ttu-id="c9251-335">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="c9251-335">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="c9251-336">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="c9251-336">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="c9251-337">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c9251-337">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="c9251-338">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="c9251-338">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="c9251-339">Der in der Methode `Startup.Configure` bereitgestellte Code zeigt *Hello World!* an.</span><span class="sxs-lookup"><span data-stu-id="c9251-339">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="c9251-340">Ersetzen Sie den Aufruf der Methode `app.Run` durch Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="c9251-340">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="c9251-341">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="c9251-341">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="c9251-342">Rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices` auf.</span><span class="sxs-lookup"><span data-stu-id="c9251-342">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="c9251-343">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="c9251-343">It adds the SignalR services to the project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="c9251-344">Ordnen Sie dem `ChatHub`-Hub eine */hub* -Route zu.</span><span class="sxs-lookup"><span data-stu-id="c9251-344">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="c9251-345">Fügen Sie die folgenden Zeilen am Ende von `Startup.Configure` hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9251-345">Add the following lines at the end of `Startup.Configure`:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="c9251-346">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="c9251-346">Create a new directory, called *Hubs* , in the project root.</span></span> <span data-ttu-id="c9251-347">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c9251-347">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="c9251-348">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs* :</span><span class="sxs-lookup"><span data-stu-id="c9251-348">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="c9251-349">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="c9251-349">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="c9251-350">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="c9251-350">Enable client and server communication</span></span>

<span data-ttu-id="c9251-351">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="c9251-351">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="c9251-352">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c9251-352">Nothing happens when you try to do so.</span></span> <span data-ttu-id="c9251-353">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="c9251-353">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="c9251-354">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="c9251-354">Run the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="c9251-355">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="c9251-355">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="c9251-356">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="c9251-356">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="c9251-357">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="c9251-357">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="c9251-358">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="c9251-358">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="c9251-359">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="c9251-359">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="c9251-360">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="c9251-360">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="c9251-361">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="c9251-361">Each message has a specific name.</span></span> <span data-ttu-id="c9251-362">Beispielsweise können Nachrichten mit dem Namen `messageReceived` die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="c9251-362">For example, messages with the name `messageReceived` can run the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="c9251-363">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="c9251-363">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="c9251-364">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="c9251-364">You can listen to any number of message names.</span></span> <span data-ttu-id="c9251-365">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="c9251-365">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="c9251-366">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-366">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="c9251-367">Die neue Nachricht wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-367">The new message is added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="c9251-368">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="c9251-368">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="c9251-369">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="c9251-369">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="c9251-370">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c9251-370">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="c9251-371">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="c9251-371">The method's first parameter is the message name.</span></span> <span data-ttu-id="c9251-372">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="c9251-372">The message data inhabits the other parameters.</span></span> <span data-ttu-id="c9251-373">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-373">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="c9251-374">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="c9251-374">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="c9251-375">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="c9251-375">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="c9251-376">Fügen Sie der Klasse `ChatHub` die Methode `NewMessage` hinzu:</span><span class="sxs-lookup"><span data-stu-id="c9251-376">Add the `NewMessage` method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="c9251-377">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="c9251-377">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="c9251-378">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c9251-378">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="c9251-379">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="c9251-379">A method named after the message name suffices.</span></span>

    <span data-ttu-id="c9251-380">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="c9251-380">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="c9251-381">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="c9251-381">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="c9251-382">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c9251-382">A call is made to [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="c9251-383">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="c9251-383">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="c9251-384">Testen der App</span><span class="sxs-lookup"><span data-stu-id="c9251-384">Test the app</span></span>

<span data-ttu-id="c9251-385">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="c9251-385">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c9251-386">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c9251-386">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c9251-387">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-387">Run Webpack in *release* mode.</span></span> <span data-ttu-id="c9251-388">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="c9251-388">Using the **Package Manager Console** window, run the following command in the project root.</span></span> <span data-ttu-id="c9251-389">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="c9251-389">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c9251-390">Klicken Sie auf **Debuggen** > **Starten ohne Debugging** , um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="c9251-390">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="c9251-391">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-391">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="c9251-392">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="c9251-392">Open another browser instance (any browser).</span></span> <span data-ttu-id="c9251-393">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="c9251-393">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c9251-394">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="c9251-394">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c9251-395">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-395">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="c9251-396">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c9251-396">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c9251-397">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="c9251-397">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="c9251-398">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="c9251-398">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="c9251-399">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="c9251-399">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="c9251-400">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="c9251-400">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="c9251-401">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c9251-401">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="c9251-402">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="c9251-402">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="c9251-403">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="c9251-403">Open another browser instance (any browser).</span></span> <span data-ttu-id="c9251-404">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="c9251-404">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="c9251-405">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="c9251-405">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="c9251-406">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c9251-406">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c9251-408">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c9251-408">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
