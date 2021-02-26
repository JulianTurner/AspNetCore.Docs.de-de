---
title: Tools für ASP.NET-Core Blazor
author: guardrex
description: Informieren Sie sich über die Tools, die zum Erstellen von Blazor-Apps verfügbar sind.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2021
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
uid: blazor/tooling
zone_pivot_groups: operating-systems
ms.openlocfilehash: 6b61d9a4645d273b0c78fae0388d569771c43a2d
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536245"
---
# <a name="tooling-for-aspnet-core-blazor"></a><span data-ttu-id="99293-103">Tools für ASP.NET-Core Blazor</span><span class="sxs-lookup"><span data-stu-id="99293-103">Tooling for ASP.NET Core Blazor</span></span>

::: zone pivot="windows"

1. <span data-ttu-id="99293-104">Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="99293-104">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="99293-105">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="99293-105">Create a new project.</span></span>

1. <span data-ttu-id="99293-106">Klicken Sie auf **Blazor-App**.</span><span class="sxs-lookup"><span data-stu-id="99293-106">Select **Blazor App**.</span></span> <span data-ttu-id="99293-107">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="99293-107">Select **Next**.</span></span>

1. <span data-ttu-id="99293-108">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="99293-108">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="99293-109">Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an.</span><span class="sxs-lookup"><span data-stu-id="99293-109">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="99293-110">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="99293-110">Select **Create**.</span></span>

1. <span data-ttu-id="99293-111">Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="99293-111">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="99293-112">Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="99293-112">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="99293-113">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="99293-113">Select **Create**.</span></span>

   <span data-ttu-id="99293-114">Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.</span><span class="sxs-lookup"><span data-stu-id="99293-114">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

   <span data-ttu-id="99293-115">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="99293-115">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99293-116">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="99293-116">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

<span data-ttu-id="99293-117">Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="99293-117">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="99293-118">Wenn Sie eine gehostete Blazor WebAssembly-App ausführen, führen Sie sie über das Projekt **`Server`** der Projektmappe aus.</span><span class="sxs-lookup"><span data-stu-id="99293-118">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

::: zone pivot="linux"

1. <span data-ttu-id="99293-119">Installieren Sie die neueste Version des [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="99293-119">Install the latest version of the [.NET Core SDK](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="99293-120">Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="99293-120">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="99293-121">Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com).</span><span class="sxs-lookup"><span data-stu-id="99293-121">Install the latest version of [Visual Studio Code](https://code.visualstudio.com).</span></span>

1. <span data-ttu-id="99293-122">Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span><span class="sxs-lookup"><span data-stu-id="99293-122">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

1. <span data-ttu-id="99293-123">Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="99293-123">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="99293-124">Fügen Sie dem Befehl für ein gehostetes Blazor WebAssembly-Modell die Hostingoption (`-ho` oder `--hosted`) hinzu:</span><span class="sxs-lookup"><span data-stu-id="99293-124">For a hosted Blazor WebAssembly experience, add the hosted option (`-ho` or `--hosted`) option to the command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   <span data-ttu-id="99293-125">Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="99293-125">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="99293-126">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="99293-126">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99293-127">Öffnen Sie in Visual Studio Code den Ordner `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="99293-127">Open the `WebApplication1` folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="99293-128">Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="99293-128">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="99293-129">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="99293-129">Select **Yes**.</span></span>

   <span data-ttu-id="99293-130">**Start- und Taskkonfiguration für gehostete Blazor WebAssembly-Projektmappen**</span><span class="sxs-lookup"><span data-stu-id="99293-130">**Hosted Blazor WebAssembly launch and task configuration**</span></span>

   <span data-ttu-id="99293-131">Fügen Sie für gehostete Blazor WebAssembly-Projektmappen den Ordner `.vscode` mit den Dateien `launch.json` und `tasks.json` zum übergeordneten Ordner der Projektmappe hinzu, bei dem es sich um den Ordner handelt, der die typischen Projektordnernamen `Client`, `Server` und `Shared` enthält (oder verschieben Sie ihn in diesen übergeordneten Ordner).</span><span class="sxs-lookup"><span data-stu-id="99293-131">For hosted Blazor WebAssembly solutions, add (or move) the `.vscode` folder with `launch.json` and `tasks.json` files to the solution's parent folder, which is the folder that contains the typical project folder names of `Client`, `Server`, and `Shared`.</span></span> <span data-ttu-id="99293-132">Aktualisieren Sie die Konfiguration in den Dateien `launch.json` und `tasks.json`, oder bestätigen Sie, dass diese eine gehostete Blazor WebAssembly-App im Projekt **`Server`** ausführen.</span><span class="sxs-lookup"><span data-stu-id="99293-132">Update or confirm that the configuration in the `launch.json` and `tasks.json` files execute a hosted Blazor WebAssembly app from the **`Server`** project.</span></span>

   <span data-ttu-id="99293-133">**`.vscode/launch.json`** (`launch`-Konfiguration):</span><span class="sxs-lookup"><span data-stu-id="99293-133">**`.vscode/launch.json`** (`launch` configuration):</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   <span data-ttu-id="99293-134">In der obigen Konfiguration für das aktuelle Arbeitsverzeichnis (`cwd`) steht der Platzhalter `{SERVER APP FOLDER}` für den Ordner des Projekts **`Server`** , in der Regel „`Server`“.</span><span class="sxs-lookup"><span data-stu-id="99293-134">In the preceding configuration for the current working directory (`cwd`), the `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>

   <span data-ttu-id="99293-135">Wenn Microsoft Edge verwendet wird und Google Chrome nicht auf dem System installiert ist, fügen Sie der Konfiguration die zusätzliche Eigenschaft `"browser": "edge"` hinzu.</span><span class="sxs-lookup"><span data-stu-id="99293-135">If Microsoft Edge is used and Google Chrome isn't installed on the system, add an additional property of `"browser": "edge"` to the configuration.</span></span>

   <span data-ttu-id="99293-136">Beispiel für einen Projektordner von `Server`, der Microsoft Edge statt den Standardbrowser Google Chrome als Browser für Debugausführungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="99293-136">Example for a project folder of `Server` and that spawns Microsoft Edge as the browser for debug runs instead of the default browser Google Chrome:</span></span>

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   <span data-ttu-id="99293-137">**`.vscode/tasks.json`** (Argumente des [`dotnet`-Befehls](/dotnet/core/tools/dotnet)):</span><span class="sxs-lookup"><span data-stu-id="99293-137">**`.vscode/tasks.json`** ([`dotnet` command](/dotnet/core/tools/dotnet) arguments):</span></span>

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   <span data-ttu-id="99293-138">Für das obige Argument gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="99293-138">In the preceding argument:</span></span>

   * <span data-ttu-id="99293-139">Der Platzhalter `{SERVER APP FOLDER}` steht für den Ordner des Projekts **`Server`** , in der Regel „`Server`“.</span><span class="sxs-lookup"><span data-stu-id="99293-139">The `{SERVER APP FOLDER}` placeholder is the **`Server`** project's folder, typically "`Server`".</span></span>
   * <span data-ttu-id="99293-140">Der Platzhalter `{PROJECT NAME}` steht für den Namen der App, der in der Regel auf dem Namen der Projektmappe basiert, gefolgt von „`.Server`“ bei einer App, die aus einer Blazor-Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="99293-140">The `{PROJECT NAME}` placeholder is the app's name, typically based on the solution's name followed by "`.Server`" in an app generated from the Blazor project template.</span></span>

   <span data-ttu-id="99293-141">Im folgenden Beispiel aus dem [Tutorial für die Verwendung von SignalR mit einer Blazor WebAssembly-App](xref:tutorials/signalr-blazor) werden der Projektordnername `Server` und der Projektname `BlazorWebAssemblySignalRApp.Server` verwendet:</span><span class="sxs-lookup"><span data-stu-id="99293-141">The following example from the [tutorial for using SignalR with a Blazor WebAssembly app](xref:tutorials/signalr-blazor) uses a project folder name of `Server` and a project name of `BlazorWebAssemblySignalRApp.Server`:</span></span>

   ```json
   ...
   "args": [
     "build",
       "${workspaceFolder}/Server/BlazorWebAssemblySignalRApp.Server.csproj",
       "/property:GenerateFullPaths=true",
       "/consoleloggerparameters:NoSummary"
   ],
   ...
   ```

1. <span data-ttu-id="99293-142">Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="99293-142">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

## <a name="trust-a-development-certificate"></a><span data-ttu-id="99293-143">Festlegen eines Entwicklungszertifikats als vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="99293-143">Trust a development certificate</span></span>

<span data-ttu-id="99293-144">Es gibt keine zentrale Möglichkeit, ein Zertifikat in Linux als vertrauenswürdig festzulegen.</span><span class="sxs-lookup"><span data-stu-id="99293-144">There's no centralized way to trust a certificate on Linux.</span></span> <span data-ttu-id="99293-145">In der Regel wird einer der folgenden Ansätze verwendet:</span><span class="sxs-lookup"><span data-stu-id="99293-145">Typically, one of the following approaches is adopted:</span></span>

* <span data-ttu-id="99293-146">Ausschließen der App-URL über die Ausschlussliste des Browsers.</span><span class="sxs-lookup"><span data-stu-id="99293-146">Exclude the app's URL in browser's exclude list.</span></span>
* <span data-ttu-id="99293-147">Festlegen aller selbstsignierten Zertifikate für `localhost` als vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="99293-147">Trust all self-signed certificates for `localhost`.</span></span>
* <span data-ttu-id="99293-148">Hinzufügen des Zertifikats zur Liste der vertrauenswürdigen Zertifikate im Browser.</span><span class="sxs-lookup"><span data-stu-id="99293-148">Add the certificate to the list of trusted certificates in the browser.</span></span>

<span data-ttu-id="99293-149">Weitere Informationen finden Sie in der Dokumentation zu Ihrem Browseranbieter und Ihrer Linux-Distribution.</span><span class="sxs-lookup"><span data-stu-id="99293-149">For more information, see the guidance provided by your browser manufacturer and Linux distribution.</span></span>

::: zone-end

::: zone pivot="macos"

1. <span data-ttu-id="99293-150">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="99293-150">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="99293-151">Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="99293-151">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="99293-152">Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).</span><span class="sxs-lookup"><span data-stu-id="99293-152">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="99293-153">Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="99293-153">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="99293-154">Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="99293-154">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="99293-155">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="99293-155">Select **Next**.</span></span>

   <span data-ttu-id="99293-156">Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="99293-156">For information on the two Blazor hosting models, *Blazor WebAssembly* (standalone and hosted) and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="99293-157">Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="99293-157">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="99293-158">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="99293-158">Select **Next**.</span></span>

1. <span data-ttu-id="99293-159">Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.</span><span class="sxs-lookup"><span data-stu-id="99293-159">For a hosted Blazor WebAssembly experience, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="99293-160">Benennen Sie im Feld **Projektname** die App `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="99293-160">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="99293-161">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="99293-161">Select **Create**.</span></span>

1. <span data-ttu-id="99293-162">Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen.</span><span class="sxs-lookup"><span data-stu-id="99293-162">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="99293-163">Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.</span><span class="sxs-lookup"><span data-stu-id="99293-163">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="99293-164">Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="99293-164">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="99293-165">Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.</span><span class="sxs-lookup"><span data-stu-id="99293-165">The user and keychain passwords are required to trust the certificate.</span></span> <span data-ttu-id="99293-166">Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span><span class="sxs-lookup"><span data-stu-id="99293-166">For more information on trusting the ASP.NET Core HTTPS development certificate, see <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.</span></span>

<span data-ttu-id="99293-167">Wenn Sie eine gehostete Blazor WebAssembly-App ausführen, führen Sie sie über das Projekt **`Server`** der Projektmappe aus.</span><span class="sxs-lookup"><span data-stu-id="99293-167">When executing a hosted Blazor WebAssembly app, run the app from the solution's **`Server`** project.</span></span>

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a><span data-ttu-id="99293-168">Verwenden von Visual Studio Code für die plattformübergreifende Blazor-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="99293-168">Use Visual Studio Code for cross-platform Blazor development</span></span>

<span data-ttu-id="99293-169">[Visual Studio Code](https://code.visualstudio.com/) ist eine plattformübergreifende Open-Source-Entwicklungsumgebung (Integrated Development Environment, IDE), mit der Blazor-Apps entwickelt werden können.</span><span class="sxs-lookup"><span data-stu-id="99293-169">[Visual Studio Code](https://code.visualstudio.com/) is an open source, cross-platform Integrated Development Environment (IDE) that can be used to develop Blazor apps.</span></span> <span data-ttu-id="99293-170">Verwenden Sie die .NET-CLI zum Erstellen einer neuen Blazor-App für die Entwicklung mit Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="99293-170">Use the .NET CLI to create a new Blazor app for development with Visual Studio Code.</span></span> <span data-ttu-id="99293-171">Weitere Informationen finden Sie in der [Linux-Version dieses Artikels](?pivots=linux).</span><span class="sxs-lookup"><span data-stu-id="99293-171">For more information, see the [Linux version of this article](?pivots=linux).</span></span>

## <a name="blazor-template-options"></a><span data-ttu-id="99293-172">Blazor-Vorlagenoptionen</span><span class="sxs-lookup"><span data-stu-id="99293-172">Blazor template options</span></span>

<span data-ttu-id="99293-173">Das Blazor-Framework bietet für beide Blazor-Hostingmodelle Vorlagen zum Erstellen neuer Apps.</span><span class="sxs-lookup"><span data-stu-id="99293-173">The Blazor framework provides templates for creating new apps for each of the two Blazor hosting models.</span></span> <span data-ttu-id="99293-174">Mithilfe der Vorlagen können neue Blazor-Projekte und -Projektmappen erstellt werden, unabhängig von den Tools, die Sie für die Blazor-Entwicklung auswählen (Visual Studio, Visual Studio für Mac, Visual Studio Code oder die .NET-CLI):</span><span class="sxs-lookup"><span data-stu-id="99293-174">The templates are used to create new Blazor projects and solutions regardless of the tooling that you select for Blazor development (Visual Studio, Visual Studio for Mac, Visual Studio Code, or the .NET CLI):</span></span>

* <span data-ttu-id="99293-175">Blazor WebAssembly-Projektvorlage: `blazorwasm`</span><span class="sxs-lookup"><span data-stu-id="99293-175">Blazor WebAssembly project template: `blazorwasm`</span></span>
* <span data-ttu-id="99293-176">Blazor Server-Projektvorlage: `blazorserver`</span><span class="sxs-lookup"><span data-stu-id="99293-176">Blazor Server project template: `blazorserver`</span></span>

<span data-ttu-id="99293-177">Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="99293-177">For more information on Blazor's hosting models, see <xref:blazor/hosting-models>.</span></span>

<span data-ttu-id="99293-178">Vorlagenoptionen sind verfügbar, indem Sie die Hilfeoption (`-h` oder `--help`) in einer Befehlsshell an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:</span><span class="sxs-lookup"><span data-stu-id="99293-178">Template options are available by passing the help option (`-h` or `--help`) to the [`dotnet new`](/dotnet/core/tools/dotnet-new) CLI command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
