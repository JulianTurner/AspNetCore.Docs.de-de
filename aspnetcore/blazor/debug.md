---
title: Debuggen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/debug
ms.openlocfilehash: 990882c03ddc14c664aa8da0518fb36087199aca
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550516"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="93bed-103">Debuggen von ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="93bed-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="93bed-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="93bed-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="93bed-105">Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Microsoft Edge/Chrome) durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="93bed-106">Sie können Ihre App auch mithilfe der folgenden integrierten Entwicklungsumgebungen (IDEs) debuggen:</span><span class="sxs-lookup"><span data-stu-id="93bed-106">You can also debug your app using the following integrated development environments (IDEs):</span></span>

* <span data-ttu-id="93bed-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93bed-107">Visual Studio</span></span>
* <span data-ttu-id="93bed-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="93bed-108">Visual Studio for Mac</span></span>
* <span data-ttu-id="93bed-109">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93bed-109">Visual Studio Code</span></span>

<span data-ttu-id="93bed-110">Verfügbare Szenarien:</span><span class="sxs-lookup"><span data-stu-id="93bed-110">Available scenarios include:</span></span>

* <span data-ttu-id="93bed-111">Festlegen und Entfernen von Breakpoints.</span><span class="sxs-lookup"><span data-stu-id="93bed-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="93bed-112">Ausführen der App mit Debugunterstützung in IDEs</span><span class="sxs-lookup"><span data-stu-id="93bed-112">Run the app with debugging support in IDEs.</span></span>
* <span data-ttu-id="93bed-113">Ausführung von Code in Einzelschritten</span><span class="sxs-lookup"><span data-stu-id="93bed-113">Single-step through the code.</span></span>
* <span data-ttu-id="93bed-114">Wiederaufnehmen der Codeausführung mit Tastenkombinationen in IDEs</span><span class="sxs-lookup"><span data-stu-id="93bed-114">Resume code execution with a keyboard shortcut in IDEs.</span></span>
* <span data-ttu-id="93bed-115">Beobachten der Werte lokaler Variablen im Fenster *Lokale Variablen*</span><span class="sxs-lookup"><span data-stu-id="93bed-115">In the *Locals* window, observe the values of local variables.</span></span>
* <span data-ttu-id="93bed-116">Anzeigen der Aufrufliste einschließlich Aufrufketten zwischen JavaScript und .NET</span><span class="sxs-lookup"><span data-stu-id="93bed-116">See the call stack, including call chains between JavaScript and .NET.</span></span>

<span data-ttu-id="93bed-117">Folgendes ist derzeit *nicht möglich*:</span><span class="sxs-lookup"><span data-stu-id="93bed-117">For now, you *can't*:</span></span>

* <span data-ttu-id="93bed-118">Halt bei Ausnahmefehlern</span><span class="sxs-lookup"><span data-stu-id="93bed-118">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="93bed-119">Erreichen von Breakpoints während des App-Starts, bevor der Debugproxy ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-119">Hit breakpoints during app startup before the debug proxy is running.</span></span> <span data-ttu-id="93bed-120">Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-120">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>
* <span data-ttu-id="93bed-121">Debuggen Sie in nicht lokalen Szenarien (z. B. [Windows-Subsystem für Linux (WSL)](/windows/wsl/) oder [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span><span class="sxs-lookup"><span data-stu-id="93bed-121">Debug in non-local scenarios (for example, [Windows Subsystem for Linux (WSL)](/windows/wsl/) or [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="93bed-122">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="93bed-122">Prerequisites</span></span>

<span data-ttu-id="93bed-123">Zum Debuggen ist einer der folgenden Browser erforderlich:</span><span class="sxs-lookup"><span data-stu-id="93bed-123">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="93bed-124">Google Chrome (Version 70 oder höher) (Standard)</span><span class="sxs-lookup"><span data-stu-id="93bed-124">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="93bed-125">Microsoft Edge (Version 80 oder höher)</span><span class="sxs-lookup"><span data-stu-id="93bed-125">Microsoft Edge (version 80 or later)</span></span>

<span data-ttu-id="93bed-126">Stellen Sie sicher, dass Firewalls oder Proxys die Kommunikation mit dem Debugproxy (`NodeJS`-Prozess) nicht blockieren.</span><span class="sxs-lookup"><span data-stu-id="93bed-126">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="93bed-127">Weitere Informationen finden Sie im Abschnitt [Firewallkonfiguration](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bed-127">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

<span data-ttu-id="93bed-128">Für Visual Studio für Mac ist die Version 8.8 (Build 1532) oder höher erforderlich:</span><span class="sxs-lookup"><span data-stu-id="93bed-128">Visual Studio for Mac requires version 8.8 (build 1532) or later:</span></span>

1. <span data-ttu-id="93bed-129">Installieren Sie die aktuelle Version von Visual Studio für Mac, indem Sie auf die Schaltfläche **Visual Studio für Mac herunterladen** klicken. Diese finden Sie unter [Microsoft: Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="93bed-129">Install the latest release of Visual Studio for Mac by selecting the **Download Visual Studio for Mac** button at [Microsoft: Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
1. <span data-ttu-id="93bed-130">Klicken Sie auf den Kanal *Vorschau* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="93bed-130">Select the *Preview* channel from within Visual Studio.</span></span> <span data-ttu-id="93bed-131">Weitere Informationen finden Sie unter [Installieren einer Vorschauversion von Visual Studio für Mac](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="93bed-131">For more information, see [Install a preview version of Visual Studio for Mac](/visualstudio/mac/install-preview).</span></span>

> [!NOTE]
> <span data-ttu-id="93bed-132">Safari von Apple unter macOS wird aktuell nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-132">Apple Safari on macOS isn't currently supported.</span></span>

## <a name="enable-debugging"></a><span data-ttu-id="93bed-133">Aktivieren des Debuggings</span><span class="sxs-lookup"><span data-stu-id="93bed-133">Enable debugging</span></span>

<span data-ttu-id="93bed-134">Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei `launchSettings.json` im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:</span><span class="sxs-lookup"><span data-stu-id="93bed-134">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="93bed-135">Nach dem Aktualisieren sollte die Datei `launchSettings.json` dem folgenden Beispiel ähneln:</span><span class="sxs-lookup"><span data-stu-id="93bed-135">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="93bed-136">Die `inspectUri`-Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="93bed-136">The `inspectUri` property:</span></span>

* <span data-ttu-id="93bed-137">ermöglicht der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.</span><span class="sxs-lookup"><span data-stu-id="93bed-137">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="93bed-138">weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.</span><span class="sxs-lookup"><span data-stu-id="93bed-138">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="93bed-139">Die Platzhalterwerte für das WebSocket-Protokoll (`wsProtocol`), den Host (`url.hostname`), den Port (`url.port`) und den Inspektor-URI im gestarteten Browser (`browserInspectUri`) werden vom Framework bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="93bed-139">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="93bed-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="93bed-140">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="93bed-141">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="93bed-141">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="93bed-142">Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="93bed-142">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="93bed-143">Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="93bed-143">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93bed-144">Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-144">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="93bed-145">Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.</span><span class="sxs-lookup"><span data-stu-id="93bed-145">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="93bed-146">Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-146">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="93bed-147">Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-147">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>
1. <span data-ttu-id="93bed-148">Sehen Sie sich in Visual Studio den Wert des `currentCount`-Felds im Fenster **Lokale Variablen** an.</span><span class="sxs-lookup"><span data-stu-id="93bed-148">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="93bed-149">Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="93bed-149">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="93bed-150">Während Sie eine Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="93bed-150">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="93bed-151">Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-151">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="93bed-152">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-152">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="93bed-153">Rufen Sie die `Fetch Data`-Seite auf, damit der Code in der `FetchData`-Komponente bis zum ersten Breakpoint ausgeführt wird, bevor eine HTTP-Anforderung für den Server ausgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-153">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="93bed-154">Drücken Sie <kbd>F5</kbd>, damit die Ausführung bis zum Breakpoint für den Server in `WeatherForecastController` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-154">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="93bed-155">Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle im Browser gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-155">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="93bed-156">Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-156">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="93bed-157">Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-157">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="93bed-158">Wenn die App in einem anderen [App-Basispfad](xref:blazor/host-and-deploy/index#app-base-path) als `/` gehostet wird, aktualisieren Sie die folgenden Eigenschaften in `Properties/launchSettings.json`, um den Basispfad der App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="93bed-158">If the app is hosted at a different [app base path](xref:blazor/host-and-deploy/index#app-base-path) than `/`, update the following properties in `Properties/launchSettings.json` to reflect the app's base path:</span></span>

* <span data-ttu-id="93bed-159">`applicationUrl`:</span><span class="sxs-lookup"><span data-stu-id="93bed-159">`applicationUrl`:</span></span>

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* <span data-ttu-id="93bed-160">`inspectUri` von jedem Profil:</span><span class="sxs-lookup"><span data-stu-id="93bed-160">`inspectUri` of each profile:</span></span>

  ```json
  "profiles": {
    ...
    "{PROFILE 1, 2, ... N}": {
      ...
      "inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/{APP BASE PATH}/_framework/debug/ws-proxy?browser={browserInspectUri}",
      ...
    }
  }
  ```

<span data-ttu-id="93bed-161">Die Platzhalter in den vorherigen Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="93bed-161">The placeholders in the preceding settings:</span></span>

* <span data-ttu-id="93bed-162">`{INSECURE PORT}`: Der unsichere Port.</span><span class="sxs-lookup"><span data-stu-id="93bed-162">`{INSECURE PORT}`: The insecure port.</span></span> <span data-ttu-id="93bed-163">Standardmäßig wird ein zufälliger Wert bereitgestellt, ein benutzerdefinierter Port ist aber zulässig.</span><span class="sxs-lookup"><span data-stu-id="93bed-163">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="93bed-164">`{APP BASE PATH}`: Der Basispfad der App.</span><span class="sxs-lookup"><span data-stu-id="93bed-164">`{APP BASE PATH}`: The app's base path.</span></span>
* <span data-ttu-id="93bed-165">`{SECURE PORT}`: Der sichere Port.</span><span class="sxs-lookup"><span data-stu-id="93bed-165">`{SECURE PORT}`: The secure port.</span></span> <span data-ttu-id="93bed-166">Standardmäßig wird ein zufälliger Wert bereitgestellt, ein benutzerdefinierter Port ist aber zulässig.</span><span class="sxs-lookup"><span data-stu-id="93bed-166">A random value is provided by default, but a custom port is permitted.</span></span>
* <span data-ttu-id="93bed-167">`{PROFILE 1, 2, ... N}`: Profile mit Starteinstellungen.</span><span class="sxs-lookup"><span data-stu-id="93bed-167">`{PROFILE 1, 2, ... N}`: Launch settings profiles.</span></span> <span data-ttu-id="93bed-168">In der Regel gibt eine App standardmäßig mehrere Profile an (z. B. ein Profil für IIS Express und ein Projektprofil, das vom Kestrel-Server verwendet wird).</span><span class="sxs-lookup"><span data-stu-id="93bed-168">Usually, an app specifies more than one profile by default (for example, a profile for IIS Express and a project profile, which is used by Kestrel server).</span></span>

<span data-ttu-id="93bed-169">In den folgenden Beispielen wird die App unter `/OAT` gehostet, und der in `wwwroot/index.html` konfigurierte Basispfad lautet `<base href="/OAT/">`:</span><span class="sxs-lookup"><span data-stu-id="93bed-169">In the following examples, the app is hosted at `/OAT` with an app base path configured in `wwwroot/index.html` as `<base href="/OAT/">`:</span></span>

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

<span data-ttu-id="93bed-170">Weitere Informationen zur Verwendung eines benutzerdefinierten App-Basispfads für Blazor WebAssembly-Apps finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.</span><span class="sxs-lookup"><span data-stu-id="93bed-170">For information on using a custom app base path for Blazor WebAssembly apps, see <xref:blazor/host-and-deploy/index#app-base-path>.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="93bed-171">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="93bed-171">Visual Studio Code</span></span>](#tab/visual-studio-code)

<h2 id="vscode"><span data-ttu-id="93bed-172">Debuggen von eigenständiger Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="93bed-172">Debug standalone Blazor WebAssembly</span></span></h2>

1. <span data-ttu-id="93bed-173">Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="93bed-173">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="93bed-174">Möglicherweise erhalten Sie die folgende Benachrichtigung, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="93bed-174">You may receive a notification that additional setup is required to enable debugging:</span></span>

   > <span data-ttu-id="93bed-175">Für das Debuggen von Blazor WebAssembly-Anwendungen ist ein zusätzliches Setup erforderlich.</span><span class="sxs-lookup"><span data-stu-id="93bed-175">Additional setup is required to debug Blazor WebAssembly applications.</span></span>

   <span data-ttu-id="93bed-176">Wenn Sie diese Benachrichtigung erhalten, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="93bed-176">If you receive the notification:</span></span>

   * <span data-ttu-id="93bed-177">Stellen Sie sicher, dass die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) installiert ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-177">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="93bed-178">Öffnen Sie zur Überprüfung der installierten Erweiterungen in der Menüleiste die Option **Ansicht** > **Erweiterungen**, oder klicken Sie in der Seitenleiste **Aktivität** auf das Symbol **Erweiterungen**.</span><span class="sxs-lookup"><span data-stu-id="93bed-178">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="93bed-179">Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-179">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="93bed-180">Öffnen Sie die Einstellungen über die Menüleiste (**Datei** > **Einstellungen** > **Einstellungen**).</span><span class="sxs-lookup"><span data-stu-id="93bed-180">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="93bed-181">Führen Sie mithilfe der Schlüsselwörter `debug preview` eine Suche durch.</span><span class="sxs-lookup"><span data-stu-id="93bed-181">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="93bed-182">Vergewissern Sie sich in den Suchergebnissen, dass das Kontrollkästchen **Debuggen > JavaScript: Vorschau verwenden** aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-182">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span> <span data-ttu-id="93bed-183">Wenn die Option zum Aktivieren des Vorschaudebuggens nicht vorhanden ist, führen Sie entweder ein Upgrade auf die neueste VS Code-Version durch, oder installieren Sie die [JavaScript-Debuggererweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code-Versionen 1.46 oder früher).</span><span class="sxs-lookup"><span data-stu-id="93bed-183">If the option to enable preview debugging isn't present, either upgrade to the latest version of VS Code or install the [JavaScript Debugger Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code versions 1.46 or earlier).</span></span>
   * <span data-ttu-id="93bed-184">Laden Sie das Fenster neu.</span><span class="sxs-lookup"><span data-stu-id="93bed-184">Reload the window.</span></span>

1. <span data-ttu-id="93bed-185">Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.</span><span class="sxs-lookup"><span data-stu-id="93bed-185">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93bed-186">Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-186">**Start Without Debugging** (<kbd>Ctrl</kbd>+<kbd>F5</kbd>) isn't supported.</span></span> <span data-ttu-id="93bed-187">Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.</span><span class="sxs-lookup"><span data-stu-id="93bed-187">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

1. <span data-ttu-id="93bed-188">Wenn Sie dazu aufgefordert werden, wählen Sie die Option **Blazor WebAssembly debuggen**, um das Debuggen zu starten.</span><span class="sxs-lookup"><span data-stu-id="93bed-188">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="93bed-189">Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.</span><span class="sxs-lookup"><span data-stu-id="93bed-189">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="93bed-190">Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-190">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>

1. <span data-ttu-id="93bed-191">Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-191">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint.</span></span>

> [!NOTE]
> <span data-ttu-id="93bed-192">Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-192">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="93bed-193">Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-193">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

## <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="93bed-194">Debuggen der gehosteten Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="93bed-194">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="93bed-195">Öffnen Sie den Projektmappenordner der gehosteten Blazor WebAssembly-App in VS Code.</span><span class="sxs-lookup"><span data-stu-id="93bed-195">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="93bed-196">Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="93bed-196">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="93bed-197">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="93bed-197">Select **Yes**.</span></span>

   > <span data-ttu-id="93bed-198">Required assets to build and debug are missing from „{APPLICATION NAME}“. (Die erforderlichen Objekte für die Erstellung und das Debugging sind in „{ANWENDUNGSNAME}“ nicht vorhanden.)</span><span class="sxs-lookup"><span data-stu-id="93bed-198">Required assets to build and debug are missing from '{APPLICATION NAME}'.</span></span> <span data-ttu-id="93bed-199">Sollen Sie hinzugefügt werden?“) angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-199">Add them?</span></span>

1. <span data-ttu-id="93bed-200">Wählen Sie in der Befehlspalette oben im Fenster das Projekt *Server* in der gehosteten Lösung aus.</span><span class="sxs-lookup"><span data-stu-id="93bed-200">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="93bed-201">Zum Starten des Debuggers wird eine `launch.json`-Datei mit der Startkonfiguration generiert.</span><span class="sxs-lookup"><span data-stu-id="93bed-201">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

## <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="93bed-202">Anfügen an eine vorhandene Debugsitzung</span><span class="sxs-lookup"><span data-stu-id="93bed-202">Attach to an existing debugging session</span></span>

<span data-ttu-id="93bed-203">Um an eine laufende Blazor-App anzufügen, erstellen Sie eine `launch.json`-Datei mit der folgenden Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="93bed-203">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="93bed-204">Das Anfügen an eine Debugsitzung wird nur für eigenständige Apps unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-204">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="93bed-205">Um das Full-Stack-Debuggen zu verwenden, muss die App über VS Code gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-205">To use full-stack debugging, you must launch the app from VS Code.</span></span>

## <a name="launch-configuration-options"></a><span data-ttu-id="93bed-206">Optionen für die Startkonfiguration</span><span class="sxs-lookup"><span data-stu-id="93bed-206">Launch configuration options</span></span>

<span data-ttu-id="93bed-207">Für den Debugtyp `blazorwasm` (`.vscode/launch.json`) werden die folgenden Optionen für die Startkonfiguration unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-207">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="93bed-208">Option</span><span class="sxs-lookup"><span data-stu-id="93bed-208">Option</span></span>    | <span data-ttu-id="93bed-209">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="93bed-209">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="93bed-210">Verwenden Sie `launch`, um eine Debugsitzung zu starten und einer Blazor WebAssembly-App anzufügen oder `attach`, um eine Debugsitzung einer bereits laufenden App anzufügen.</span><span class="sxs-lookup"><span data-stu-id="93bed-210">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="93bed-211">Die URL, die beim Debuggen im Browser geöffnet werden soll.</span><span class="sxs-lookup"><span data-stu-id="93bed-211">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="93bed-212">Wird standardmäßig auf `https://localhost:5001` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="93bed-212">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="93bed-213">Der Browser, der für die Debugsitzung gestartet werden muss.</span><span class="sxs-lookup"><span data-stu-id="93bed-213">The browser to launch for the debugging session.</span></span> <span data-ttu-id="93bed-214">Wird auf `edge` oder `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="93bed-214">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="93bed-215">Wird standardmäßig auf `chrome` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="93bed-215">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="93bed-216">Wird zum Generieren von Protokollen vom JS-Debugger verwendet.</span><span class="sxs-lookup"><span data-stu-id="93bed-216">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="93bed-217">Zum Generieren von Protokollen auf `true` festlegen.</span><span class="sxs-lookup"><span data-stu-id="93bed-217">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="93bed-218">Muss auf `true` gesetzt werden, wenn eine gehostete Blazor WebAssembly-App gestartet und debuggt werden soll.</span><span class="sxs-lookup"><span data-stu-id="93bed-218">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="93bed-219">Gibt den absoluten Pfad des Webservers an.</span><span class="sxs-lookup"><span data-stu-id="93bed-219">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="93bed-220">Muss gesetzt werden, wenn eine App von einer Unterroute bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-220">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="93bed-221">Die Anzahl der Millisekunden, die auf das Anfügen der Debugsitzung gewartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="93bed-221">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="93bed-222">Wird standardmäßig auf 30.000 Millisekunden (30 Sekunden) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="93bed-222">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="93bed-223">Ein Verweis auf die ausführbare Datei zur Ausführung des Servers der gehosteten App.</span><span class="sxs-lookup"><span data-stu-id="93bed-223">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="93bed-224">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="93bed-224">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="93bed-225">Das Arbeitsverzeichnis, unter dem die App gestartet werden soll.</span><span class="sxs-lookup"><span data-stu-id="93bed-225">The working directory to launch the app under.</span></span> <span data-ttu-id="93bed-226">Muss festgelegt werden, wenn `hosted` den Wert `true` hat.</span><span class="sxs-lookup"><span data-stu-id="93bed-226">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="93bed-227">Die Umgebungsvariable, die für den gestarteten Prozess bereitgestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="93bed-227">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="93bed-228">Trifft nur zu, wenn `hosted` auf `true` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-228">Only applicable if `hosted` is set to `true`.</span></span> |

## <a name="example-launch-configurations"></a><span data-ttu-id="93bed-229">Beispiel für Startkonfigurationen</span><span class="sxs-lookup"><span data-stu-id="93bed-229">Example launch configurations</span></span>

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="93bed-230">Starten und Debuggen einer eigenständigen Blazor WebAssembly-App</span><span class="sxs-lookup"><span data-stu-id="93bed-230">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="93bed-231">Anfügen an eine laufende App unter einer angegebenen URL</span><span class="sxs-lookup"><span data-stu-id="93bed-231">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="93bed-232">Starten und Debuggen einer gehosteten Blazor WebAssembly-App mit Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="93bed-232">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="93bed-233">Die Browserkonfiguration verwendet standardmäßig Google Chrome.</span><span class="sxs-lookup"><span data-stu-id="93bed-233">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="93bed-234">Wenn Sie Microsoft Edge zum Debuggen verwenden möchten, legen Sie `browser` auf `edge` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-234">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="93bed-235">Um Google Chrome zu verwenden, legen Sie die Option `browser` nicht fest, oder legen Sie den Optionswert auf `chrome` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-235">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="93bed-236">Im vorherigen Beispiel ist `MyHostedApp.Server.dll` die Assembly der *Server*-App.</span><span class="sxs-lookup"><span data-stu-id="93bed-236">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="93bed-237">Der Ordner `.vscode` befindet sich im Projektmappenordner neben den Ordnern `Client`, `Server` und `Shared`.</span><span class="sxs-lookup"><span data-stu-id="93bed-237">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="93bed-238">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="93bed-238">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="93bed-239">So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code für Mac:</span><span class="sxs-lookup"><span data-stu-id="93bed-239">To debug a Blazor WebAssembly app in Visual Studio for Mac:</span></span>

1. <span data-ttu-id="93bed-240">Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.</span><span class="sxs-lookup"><span data-stu-id="93bed-240">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="93bed-241">Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die App im Debugger auszuführen.</span><span class="sxs-lookup"><span data-stu-id="93bed-241">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to run the app in the debugger.</span></span>

   > [!NOTE]
   > <span data-ttu-id="93bed-242">**Starten ohne Debuggen** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="93bed-242">**Start Without Debugging** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) isn't supported.</span></span> <span data-ttu-id="93bed-243">Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.</span><span class="sxs-lookup"><span data-stu-id="93bed-243">When the app is run in Debug configuration, debugging overhead always results in a small performance reduction.</span></span>

   > [!IMPORTANT]
   > <span data-ttu-id="93bed-244">Google Chrome oder Microsoft Edge muss der für die Debugsitzung ausgewählte Browser sein.</span><span class="sxs-lookup"><span data-stu-id="93bed-244">Google Chrome or Microsoft Edge must be the selected browser for the debugging session.</span></span>

1. <span data-ttu-id="93bed-245">Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-245">In the `*Client*` app, set a breakpoint on the `currentCount++;` line in `Pages/Counter.razor`.</span></span>
1. <span data-ttu-id="93bed-246">Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-246">In the browser, navigate to `Counter` page and select the **Click me** button to hit the breakpoint:</span></span>
1. <span data-ttu-id="93bed-247">Sehen Sie sich in Visual Studio den Wert des `currentCount`-Felds im Fenster **Lokale Variablen** an.</span><span class="sxs-lookup"><span data-stu-id="93bed-247">In Visual Studio, inspect the value of the `currentCount` field in the **Locals** window.</span></span>
1. <span data-ttu-id="93bed-248">Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die Ausführung fortzusetzen.</span><span class="sxs-lookup"><span data-stu-id="93bed-248">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution.</span></span>

<span data-ttu-id="93bed-249">Während Sie eine Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:</span><span class="sxs-lookup"><span data-stu-id="93bed-249">While debugging a Blazor WebAssembly app, you can also debug server code:</span></span>

1. <span data-ttu-id="93bed-250">Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-250">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="93bed-251">Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.</span><span class="sxs-lookup"><span data-stu-id="93bed-251">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="93bed-252">Rufen Sie die `Fetch Data`-Seite auf, damit der Code in der `FetchData`-Komponente bis zum ersten Breakpoint ausgeführt wird, bevor eine HTTP-Anforderung für den Server ausgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-252">Browse to the `Fetch Data` page to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server.</span></span>
1. <span data-ttu-id="93bed-253">Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, damit die Ausführung bis zum Breakpoint auf dem Server in `WeatherForecastController` fortgesetzt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-253">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`.</span></span>
1. <span data-ttu-id="93bed-254">Drücken Sie wieder <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle im Browser gerendert wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-254">Press <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd> again to let execution continue and see the weather forecast table rendered in the browser.</span></span>

> [!NOTE]
> <span data-ttu-id="93bed-255">Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-255">Breakpoints are **not** hit during app startup before the debug proxy is running.</span></span> <span data-ttu-id="93bed-256">Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-256">This includes breakpoints in `Program.Main` (`Program.cs`) and breakpoints in the [`OnInitialized{Async}` methods](xref:blazor/components/lifecycle#component-initialization-methods) of components that are loaded by the first page requested from the app.</span></span>

<span data-ttu-id="93bed-257">Weitere Informationen finden Sie unter [Debuggen in Visual Studio für Mac](/visualstudio/mac/debugging).</span><span class="sxs-lookup"><span data-stu-id="93bed-257">For more information, see [Debugging with Visual Studio for Mac](/visualstudio/mac/debugging).</span></span>

---

## <a name="debug-in-the-browser"></a><span data-ttu-id="93bed-258">Debuggen im Browser</span><span class="sxs-lookup"><span data-stu-id="93bed-258">Debug in the browser</span></span>

<span data-ttu-id="93bed-259">*Die Anleitung in diesem Abschnitt gilt für Google Chrome und Microsoft Edge unter Windows.*</span><span class="sxs-lookup"><span data-stu-id="93bed-259">*The guidance in this section applies to Google Chrome and Microsoft Edge running on Windows.*</span></span>

1. <span data-ttu-id="93bed-260">Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.</span><span class="sxs-lookup"><span data-stu-id="93bed-260">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="93bed-261">Starten Sie einen Browser, und navigieren Sie zur URL der App (z. B. `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="93bed-261">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="93bed-262">Versuchen Sie, das Remotedebuggen im Browser zu starten, indem Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd> drücken.</span><span class="sxs-lookup"><span data-stu-id="93bed-262">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd>.</span></span>

   <span data-ttu-id="93bed-263">Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden, was nicht das Standardverhalten ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-263">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="93bed-264">Wenn das Remotedebuggen deaktiviert ist, wird eine Seite mit der Meldung **Debugfähige Browserregisterkarte nicht gefunden** gerendert, die Anweisungen zum Starten des Browsers mit geöffnetem Debuggingport enthält.</span><span class="sxs-lookup"><span data-stu-id="93bed-264">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="93bed-265">Befolgen Sie die für Ihren Browser geltenden Anweisungen. Dadurch wird ein neues Browserfenster geöffnet.</span><span class="sxs-lookup"><span data-stu-id="93bed-265">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="93bed-266">Schließen Sie das vorherige Browserfenster.</span><span class="sxs-lookup"><span data-stu-id="93bed-266">Close the previous browser window.</span></span>

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. <span data-ttu-id="93bed-267">Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination zum Debuggen aus dem vorherigen Schritt eine neue Debuggerregisterkarte.</span><span class="sxs-lookup"><span data-stu-id="93bed-267">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut in the previous step opens a new debugger tab.</span></span>

1. <span data-ttu-id="93bed-268">Nach einem kurzen Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys der App im Knoten `file://` an.</span><span class="sxs-lookup"><span data-stu-id="93bed-268">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="93bed-269">In Komponentencode (`.razor`-Dateien) und C#-Codedateien (`.cs`) werden bei Codeausführung von Ihnen festgelegte Breakpoints erreicht.</span><span class="sxs-lookup"><span data-stu-id="93bed-269">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="93bed-270">Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).</span><span class="sxs-lookup"><span data-stu-id="93bed-270">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="93bed-271">Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert.</span><span class="sxs-lookup"><span data-stu-id="93bed-271">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="93bed-272">Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy.</span><span class="sxs-lookup"><span data-stu-id="93bed-272">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="93bed-273">Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).</span><span class="sxs-lookup"><span data-stu-id="93bed-273">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="93bed-274">Browserquellzuordnungen</span><span class="sxs-lookup"><span data-stu-id="93bed-274">Browser source maps</span></span>

<span data-ttu-id="93bed-275">Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet.</span><span class="sxs-lookup"><span data-stu-id="93bed-275">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="93bed-276">Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu.</span><span class="sxs-lookup"><span data-stu-id="93bed-276">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="93bed-277">Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.</span><span class="sxs-lookup"><span data-stu-id="93bed-277">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="firewall-configuration"></a><span data-ttu-id="93bed-278">Firewallkonfiguration</span><span class="sxs-lookup"><span data-stu-id="93bed-278">Firewall configuration</span></span>

<span data-ttu-id="93bed-279">Wenn eine Firewall die Kommunikation mit dem Debugproxy blockiert, erstellen Sie eine Firewallausnahmeregel, die die Kommunikation zwischen Browser und `NodeJS`-Prozess zulässt.</span><span class="sxs-lookup"><span data-stu-id="93bed-279">If a firewall blocks communication with the debug proxy, create a firewall exception rule that permits communication between the browser and the `NodeJS` process.</span></span>

> [!WARNING]
> <span data-ttu-id="93bed-280">Änderungen an einer Firewallkonfiguration müssen umsichtig erfolgen, damit keine Sicherheitsrisiken entstehen.</span><span class="sxs-lookup"><span data-stu-id="93bed-280">Modification of a firewall configuration must be made with care to avoid creating security vulnerablities.</span></span> <span data-ttu-id="93bed-281">Setzen Sie Sicherheitsleitlinien sorgfältig um, befolgen Sie bewährte Methoden, und beachten Sie die Warnungen des Herstellers der Firewall.</span><span class="sxs-lookup"><span data-stu-id="93bed-281">Carefully apply security guidance, follow best security practices, and respect warnings issued by the firewall's manufacturer.</span></span>
>
> <span data-ttu-id="93bed-282">Für das Zulassen einer offenen Kommunikation mit dem `NodeJS`-Prozess gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="93bed-282">Permitting open communication with the `NodeJS` process:</span></span>
>
> * <span data-ttu-id="93bed-283">Öffnet den Node-Server für jede beliebige Verbindung abhängig von den Fähigkeiten und der Konfiguration der Firewall.</span><span class="sxs-lookup"><span data-stu-id="93bed-283">Opens up the Node server to any connection, depending on the firewall's capabilities and configuration.</span></span>
> * <span data-ttu-id="93bed-284">Kann abhängig von Ihrem Netzwerk riskant sein.</span><span class="sxs-lookup"><span data-stu-id="93bed-284">Might be risky depending on your network.</span></span>
> * <span data-ttu-id="93bed-285">**Wird nur auf Entwicklercomputern empfohlen.**</span><span class="sxs-lookup"><span data-stu-id="93bed-285">**Is only recommended on developer machines.**</span></span>
>
> <span data-ttu-id="93bed-286">Lassen Sie nach Möglichkeit eine offene Kommunikation mit dem `NodeJS`-Prozess  **nur in vertrauenswürdigen oder privaten Netzwerken** zu.</span><span class="sxs-lookup"><span data-stu-id="93bed-286">If possible, only allow open communication with the `NodeJS` process **on trusted or private networks**.</span></span>

<span data-ttu-id="93bed-287">Eine Anleitung zur Konfiguration der [Windows-Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) finden Sie unter [Erstellen einer Regel für eingehende Programme oder Dienste](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span><span class="sxs-lookup"><span data-stu-id="93bed-287">For [Windows Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) configuration guidance, see [Create an Inbound Program or Service Rule](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule).</span></span> <span data-ttu-id="93bed-288">Weitere Informationen finden Sie unter [Windows Defender-Firewall mit erweiterter Sicherheit](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) und zugehörigen Artikeln in der Dokumentation zur Windows-Firewall.</span><span class="sxs-lookup"><span data-stu-id="93bed-288">For more information, see [Windows Defender Firewall with Advanced Security](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) and related articles in the Windows Firewall documentation set.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="93bed-289">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="93bed-289">Troubleshoot</span></span>

<span data-ttu-id="93bed-290">Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:</span><span class="sxs-lookup"><span data-stu-id="93bed-290">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="93bed-291">Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="93bed-291">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="93bed-292">Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="93bed-292">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="93bed-293">Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen.</span><span class="sxs-lookup"><span data-stu-id="93bed-293">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="93bed-294">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span><span class="sxs-lookup"><span data-stu-id="93bed-294">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="93bed-295">Visual Studio erfordert die Option **JavaScript-Debugging für ASP.NET aktivieren (Chrome, Microsoft Edge und IE)** in **Tools** > **Optionen** > **Debuggen** > **Allgemein**.</span><span class="sxs-lookup"><span data-stu-id="93bed-295">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="93bed-296">Dies ist die Standardeinstellung für Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="93bed-296">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="93bed-297">Wenn das Debuggen nicht funktioniert, vergewissern Sie sich, dass die Option ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-297">If debugging isn't working, confirm that the option is selected.</span></span>
* <span data-ttu-id="93bed-298">Wenn Ihre Umgebung einen HTTP-Proxy verwendet, sorgen Sie dafür, dass `localhost` in den Umgehungseinstellungen des Proxys eingeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-298">If your environment uses an HTTP proxy, make sure that `localhost` is included in the proxy bypass settings.</span></span> <span data-ttu-id="93bed-299">Dies können Sie tun, indem Sie die `NO_PROXY`-Umgebungsvariable an einem der beiden folgenden Orte festlegen:</span><span class="sxs-lookup"><span data-stu-id="93bed-299">This can be done by setting the `NO_PROXY` environment variable in either:</span></span>
  * <span data-ttu-id="93bed-300">Die `launchSettings.json`-Datei für das Projekt</span><span class="sxs-lookup"><span data-stu-id="93bed-300">The `launchSettings.json` file for the project.</span></span>
  * <span data-ttu-id="93bed-301">Auf Ebene der Umgebungsvariablen für Benutzer oder System, damit sie auf alle Apps angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-301">At the user or system environment variables level for it to apply to all apps.</span></span> <span data-ttu-id="93bed-302">Wenn Sie eine Umgebungsvariable verwenden, starten Sie Visual Studio neu, damit die Änderungen übernommen werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-302">When using an environment variable, restart Visual Studio for the change to take effect.</span></span>
* <span data-ttu-id="93bed-303">Stellen Sie sicher, dass Firewalls oder Proxys die Kommunikation mit dem Debugproxy (`NodeJS`-Prozess) nicht blockieren.</span><span class="sxs-lookup"><span data-stu-id="93bed-303">Ensure that firewalls or proxies don't block communication with the debug proxy (`NodeJS` process).</span></span> <span data-ttu-id="93bed-304">Weitere Informationen finden Sie im Abschnitt [Firewallkonfiguration](#firewall-configuration).</span><span class="sxs-lookup"><span data-stu-id="93bed-304">For more information, see the [Firewall configuration](#firewall-configuration) section.</span></span>

### <a name="breakpoints-in-oninitializedasync-not-hit"></a><span data-ttu-id="93bed-305">In `OnInitialized{Async}` nicht verwendete Breakpoints</span><span class="sxs-lookup"><span data-stu-id="93bed-305">Breakpoints in `OnInitialized{Async}` not hit</span></span>

<span data-ttu-id="93bed-306">Der Debugproxy des Blazor-Frameworks benötigt eine kurze Zeit zum Starten, sodass Breakpoints in der [`OnInitialized{Async}`-Lebenszyklusmethode](xref:blazor/components/lifecycle#component-initialization-methods) möglicherweise nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="93bed-306">The Blazor framework's debugging proxy takes a short time to launch, so breakpoints in the [`OnInitialized{Async}` lifecycle method](xref:blazor/components/lifecycle#component-initialization-methods) might not be hit.</span></span> <span data-ttu-id="93bed-307">Es wird empfohlen, am Anfang des Methodentextes eine Verzögerung hinzuzufügen, um dem Debugproxy etwas Zeit zum Starten zu geben, bevor der Breakpoint erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="93bed-307">We recommend adding a delay at the start of the method body to give the debug proxy some time to launch before the breakpoint is hit.</span></span> <span data-ttu-id="93bed-308">Sie können die Verzögerung auf Grundlage einer [`if`-Compileranweisung einschließen](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), um sicherzustellen, dass die Verzögerung für einen Releasebuild der App nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="93bed-308">You can include the delay based on an [`if` compiler directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) to ensure that the delay isn't present for a release build of the app.</span></span>

<span data-ttu-id="93bed-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span><span class="sxs-lookup"><span data-stu-id="93bed-309"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:</span></span>

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<span data-ttu-id="93bed-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span><span class="sxs-lookup"><span data-stu-id="93bed-310"><xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:</span></span>

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a><span data-ttu-id="93bed-311">Timeout in Visual Studio (Windows)</span><span class="sxs-lookup"><span data-stu-id="93bed-311">Visual Studio (Windows) timeout</span></span>

<span data-ttu-id="93bed-312">Wenn Visual Studio eine Ausnahme auslöst, dass der Debugadapter nicht gestartet werden konnte, und meldet, dass das Timeout erreicht wurde, können Sie das Timeout mit einer Registrierungseinstellung anpassen:</span><span class="sxs-lookup"><span data-stu-id="93bed-312">If Visual Studio throws an exception that the debug adapter failed to launch mentioning that the timeout was reached, you can adjust the timeout with a Registry setting:</span></span>

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

<span data-ttu-id="93bed-313">Der Platzhalter `{TIMEOUT}` im vorangehenden Befehl wird in Millisekunden angegeben.</span><span class="sxs-lookup"><span data-stu-id="93bed-313">The `{TIMEOUT}` placeholder in the preceding command is in milliseconds.</span></span> <span data-ttu-id="93bed-314">Beispielsweise wird eine Minute als `60000` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="93bed-314">For example, one minute is assigned as `60000`.</span></span>
