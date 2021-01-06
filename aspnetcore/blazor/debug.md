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
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "94550516"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Debuggen von ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden. Sie können Ihre App auch mithilfe der folgenden integrierten Entwicklungsumgebungen (IDEs) debuggen:

* Visual Studio
* Visual Studio für Mac
* Visual Studio Code

Verfügbare Szenarien:

* Festlegen und Entfernen von Breakpoints.
* Ausführen der App mit Debugunterstützung in IDEs
* Ausführung von Code in Einzelschritten
* Wiederaufnehmen der Codeausführung mit Tastenkombinationen in IDEs
* Beobachten der Werte lokaler Variablen im Fenster *Lokale Variablen*
* Anzeigen der Aufrufliste einschließlich Aufrufketten zwischen JavaScript und .NET

Folgendes ist derzeit *nicht möglich*:

* Halt bei Ausnahmefehlern
* Erreichen von Breakpoints während des App-Starts, bevor der Debugproxy ausgeführt wird. Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.
* Debuggen Sie in nicht lokalen Szenarien (z. B. [Windows-Subsystem für Linux (WSL)](/windows/wsl/) oder [Visual Studio Codespaces](/visualstudio/codespaces/overview/what-is-vsonline)).

## <a name="prerequisites"></a>Voraussetzungen

Zum Debuggen ist einer der folgenden Browser erforderlich:

* Google Chrome (Version 70 oder höher) (Standard)
* Microsoft Edge (Version 80 oder höher)

Stellen Sie sicher, dass Firewalls oder Proxys die Kommunikation mit dem Debugproxy (`NodeJS`-Prozess) nicht blockieren. Weitere Informationen finden Sie im Abschnitt [Firewallkonfiguration](#firewall-configuration).

Für Visual Studio für Mac ist die Version 8.8 (Build 1532) oder höher erforderlich:

1. Installieren Sie die aktuelle Version von Visual Studio für Mac, indem Sie auf die Schaltfläche **Visual Studio für Mac herunterladen** klicken. Diese finden Sie unter [Microsoft: Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).
1. Klicken Sie auf den Kanal *Vorschau* in Visual Studio. Weitere Informationen finden Sie unter [Installieren einer Vorschauversion von Visual Studio für Mac](/visualstudio/mac/install-preview).

> [!NOTE]
> Safari von Apple unter macOS wird aktuell nicht unterstützt.

## <a name="enable-debugging"></a>Aktivieren des Debuggings

Zum Aktivieren des Debuggens für eine vorhandene Blazor WebAssembly-App müssen Sie die Datei `launchSettings.json` im Startprojekt aktualisieren, sodass sie die folgende `inspectUri`-Eigenschaft in allen Startprofilen enthält:

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

Nach dem Aktualisieren sollte die Datei `launchSettings.json` dem folgenden Beispiel ähneln:

[!code-json[](debug/launchSettings.json?highlight=14,22)]

Die `inspectUri`-Eigenschaft:

* ermöglicht der IDE, zu ermitteln, ob es sich bei einer App um eine Blazor WebAssembly-App handelt.
* weist die Skriptdebugginginfrastruktur zum Herstellen einer Verbindung mit dem Browser über den Debugproxy von Blazor an.

Die Platzhalterwerte für das WebSocket-Protokoll (`wsProtocol`), den Host (`url.hostname`), den Port (`url.port`) und den Inspektor-URI im gestarteten Browser (`browserInspectUri`) werden vom Framework bereitgestellt.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:

1. Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.
1. Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.

   > [!NOTE]
   > Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt. Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.

1. Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.
1. Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.
1. Sehen Sie sich in Visual Studio den Wert des `currentCount`-Felds im Fenster **Lokale Variablen** an.
1. Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.

Während Sie eine Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:

1. Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.
1. Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.
1. Rufen Sie die `Fetch Data`-Seite auf, damit der Code in der `FetchData`-Komponente bis zum ersten Breakpoint ausgeführt wird, bevor eine HTTP-Anforderung für den Server ausgegeben wird.
1. Drücken Sie <kbd>F5</kbd>, damit die Ausführung bis zum Breakpoint für den Server in `WeatherForecastController` ausgeführt wird.
1. Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle im Browser gerendert wird.

> [!NOTE]
> Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird. Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.

Wenn die App in einem anderen [App-Basispfad](xref:blazor/host-and-deploy/index#app-base-path) als `/` gehostet wird, aktualisieren Sie die folgenden Eigenschaften in `Properties/launchSettings.json`, um den Basispfad der App anzugeben:

* `applicationUrl`:

  ```json
  "iisSettings": {
    ...
    "iisExpress": {
      "applicationUrl": "http://localhost:{INSECURE PORT}/{APP BASE PATH}/",
      "sslPort": {SECURE PORT}
    }
  },
  ```

* `inspectUri` von jedem Profil:

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

Die Platzhalter in den vorherigen Einstellungen:

* `{INSECURE PORT}`: Der unsichere Port. Standardmäßig wird ein zufälliger Wert bereitgestellt, ein benutzerdefinierter Port ist aber zulässig.
* `{APP BASE PATH}`: Der Basispfad der App.
* `{SECURE PORT}`: Der sichere Port. Standardmäßig wird ein zufälliger Wert bereitgestellt, ein benutzerdefinierter Port ist aber zulässig.
* `{PROFILE 1, 2, ... N}`: Profile mit Starteinstellungen. In der Regel gibt eine App standardmäßig mehrere Profile an (z. B. ein Profil für IIS Express und ein Projektprofil, das vom Kestrel-Server verwendet wird).

In den folgenden Beispielen wird die App unter `/OAT` gehostet, und der in `wwwroot/index.html` konfigurierte Basispfad lautet `<base href="/OAT/">`:

```json
"applicationUrl": "http://localhost:{INSECURE PORT}/OAT/",
```

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/OAT/_framework/debug/ws-proxy?browser={browserInspectUri}",
```

Weitere Informationen zur Verwendung eines benutzerdefinierten App-Basispfads für Blazor WebAssembly-Apps finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<h2 id="vscode">Debuggen von eigenständiger Blazor WebAssembly</h2>

1. Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.

   Möglicherweise erhalten Sie die folgende Benachrichtigung, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:

   > Für das Debuggen von Blazor WebAssembly-Anwendungen ist ein zusätzliches Setup erforderlich.

   Wenn Sie diese Benachrichtigung erhalten, führen Sie die folgenden Schritte aus:

   * Stellen Sie sicher, dass die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) installiert ist. Öffnen Sie zur Überprüfung der installierten Erweiterungen in der Menüleiste die Option **Ansicht** > **Erweiterungen**, oder klicken Sie in der Seitenleiste **Aktivität** auf das Symbol **Erweiterungen**.
   * Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist. Öffnen Sie die Einstellungen über die Menüleiste (**Datei** > **Einstellungen** > **Einstellungen**). Führen Sie mithilfe der Schlüsselwörter `debug preview` eine Suche durch. Vergewissern Sie sich in den Suchergebnissen, dass das Kontrollkästchen **Debuggen > JavaScript: Vorschau verwenden** aktiviert ist. Wenn die Option zum Aktivieren des Vorschaudebuggens nicht vorhanden ist, führen Sie entweder ein Upgrade auf die neueste VS Code-Version durch, oder installieren Sie die [JavaScript-Debuggererweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code-Versionen 1.46 oder früher).
   * Laden Sie das Fenster neu.

1. Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.

   > [!NOTE]
   > Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt. Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.

1. Wenn Sie dazu aufgefordert werden, wählen Sie die Option **Blazor WebAssembly debuggen**, um das Debuggen zu starten.

1. Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.

1. Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.

1. Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.

> [!NOTE]
> Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird. Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.

## <a name="debug-hosted-no-locblazor-webassembly"></a>Debuggen der gehosteten Blazor WebAssembly

1. Öffnen Sie den Projektmappenordner der gehosteten Blazor WebAssembly-App in VS Code.

1. Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt. Wählen Sie **Ja**.

   > Required assets to build and debug are missing from „{APPLICATION NAME}“. (Die erforderlichen Objekte für die Erstellung und das Debugging sind in „{ANWENDUNGSNAME}“ nicht vorhanden.) Sollen Sie hinzugefügt werden?“) angezeigt wird.

1. Wählen Sie in der Befehlspalette oben im Fenster das Projekt *Server* in der gehosteten Lösung aus.

Zum Starten des Debuggers wird eine `launch.json`-Datei mit der Startkonfiguration generiert.

## <a name="attach-to-an-existing-debugging-session"></a>Anfügen an eine vorhandene Debugsitzung

Um an eine laufende Blazor-App anzufügen, erstellen Sie eine `launch.json`-Datei mit der folgenden Konfiguration:

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> Das Anfügen an eine Debugsitzung wird nur für eigenständige Apps unterstützt. Um das Full-Stack-Debuggen zu verwenden, muss die App über VS Code gestartet werden.

## <a name="launch-configuration-options"></a>Optionen für die Startkonfiguration

Für den Debugtyp `blazorwasm` (`.vscode/launch.json`) werden die folgenden Optionen für die Startkonfiguration unterstützt.

| Option    | Beschreibung |
| --------- | ----------- |
| `request` | Verwenden Sie `launch`, um eine Debugsitzung zu starten und einer Blazor WebAssembly-App anzufügen oder `attach`, um eine Debugsitzung einer bereits laufenden App anzufügen. |
| `url`     | Die URL, die beim Debuggen im Browser geöffnet werden soll. Wird standardmäßig auf `https://localhost:5001` festgelegt. |
| `browser` | Der Browser, der für die Debugsitzung gestartet werden muss. Wird auf `edge` oder `chrome` festgelegt. Wird standardmäßig auf `chrome` festgelegt. |
| `trace`   | Wird zum Generieren von Protokollen vom JS-Debugger verwendet. Zum Generieren von Protokollen auf `true` festlegen. |
| `hosted`  | Muss auf `true` gesetzt werden, wenn eine gehostete Blazor WebAssembly-App gestartet und debuggt werden soll. |
| `webRoot` | Gibt den absoluten Pfad des Webservers an. Muss gesetzt werden, wenn eine App von einer Unterroute bereitgestellt wird. |
| `timeout` | Die Anzahl der Millisekunden, die auf das Anfügen der Debugsitzung gewartet werden soll. Wird standardmäßig auf 30.000 Millisekunden (30 Sekunden) festgelegt. |
| `program` | Ein Verweis auf die ausführbare Datei zur Ausführung des Servers der gehosteten App. Muss festgelegt werden, wenn `hosted` den Wert `true` hat. |
| `cwd`     | Das Arbeitsverzeichnis, unter dem die App gestartet werden soll. Muss festgelegt werden, wenn `hosted` den Wert `true` hat. |
| `env`     | Die Umgebungsvariable, die für den gestarteten Prozess bereitgestellt werden muss. Trifft nur zu, wenn `hosted` auf `true` festgelegt ist. |

## <a name="example-launch-configurations"></a>Beispiel für Startkonfigurationen

### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Starten und Debuggen einer eigenständigen Blazor WebAssembly-App

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anfügen an eine laufende App unter einer angegebenen URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Starten und Debuggen einer gehosteten Blazor WebAssembly-App mit Microsoft Edge

Die Browserkonfiguration verwendet standardmäßig Google Chrome. Wenn Sie Microsoft Edge zum Debuggen verwenden möchten, legen Sie `browser` auf `edge` fest. Um Google Chrome zu verwenden, legen Sie die Option `browser` nicht fest, oder legen Sie den Optionswert auf `chrome` fest.

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

Im vorherigen Beispiel ist `MyHostedApp.Server.dll` die Assembly der *Server*-App. Der Ordner `.vscode` befindet sich im Projektmappenordner neben den Ordnern `Client`, `Server` und `Shared`.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

So debuggen Sie eine Blazor WebAssembly-App in Visual Studio Code für Mac:

1. Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.
1. Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die App im Debugger auszuführen.

   > [!NOTE]
   > **Starten ohne Debuggen** (<kbd>&#8997;</kbd>+<kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>) wird nicht unterstützt. Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.

   > [!IMPORTANT]
   > Google Chrome oder Microsoft Edge muss der für die Debugsitzung ausgewählte Browser sein.

1. Legen Sie in der `*Client*`-App einen Breakpoint für die `currentCount++;`-Zeile in `Pages/Counter.razor` fest.
1. Navigieren Sie im Browser zur `Counter`-Seite, und klicken Sie auf die Schaltfläche **Hier klicken**, damit der Code bis zum Breakpoint ausgeführt wird.
1. Sehen Sie sich in Visual Studio den Wert des `currentCount`-Felds im Fenster **Lokale Variablen** an.
1. Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die Ausführung fortzusetzen.

Während Sie eine Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:

1. Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.
1. Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.
1. Rufen Sie die `Fetch Data`-Seite auf, damit der Code in der `FetchData`-Komponente bis zum ersten Breakpoint ausgeführt wird, bevor eine HTTP-Anforderung für den Server ausgegeben wird.
1. Drücken Sie <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, damit die Ausführung bis zum Breakpoint auf dem Server in `WeatherForecastController` fortgesetzt wird.
1. Drücken Sie wieder <kbd>&#8984;</kbd>+<kbd>&#8617;</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle im Browser gerendert wird.

> [!NOTE]
> Während App-Starts werden Breakpoints **nicht** erreicht, während der Debugproxy ausgeführt wird. Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.

Weitere Informationen finden Sie unter [Debuggen in Visual Studio für Mac](/visualstudio/mac/debugging).

---

## <a name="debug-in-the-browser"></a>Debuggen im Browser

*Die Anleitung in diesem Abschnitt gilt für Google Chrome und Microsoft Edge unter Windows.*

1. Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.

1. Starten Sie einen Browser, und navigieren Sie zur URL der App (z. B. `https://localhost:5001`).

1. Versuchen Sie, das Remotedebuggen im Browser zu starten, indem Sie <kbd>UMSCHALT</kbd>+<kbd>ALT</kbd>+<kbd>D</kbd> drücken.

   Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden, was nicht das Standardverhalten ist. Wenn das Remotedebuggen deaktiviert ist, wird eine Seite mit der Meldung **Debugfähige Browserregisterkarte nicht gefunden** gerendert, die Anweisungen zum Starten des Browsers mit geöffnetem Debuggingport enthält. Befolgen Sie die für Ihren Browser geltenden Anweisungen. Dadurch wird ein neues Browserfenster geöffnet. Schließen Sie das vorherige Browserfenster.

<!-- HOLD 
1. In the browser, attempt to commence remote debugging by pressing:

   * <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>d</kbd> on Windows.
   * <kbd>Shift</kbd>+<kbd>&#8984;</kbd>+<kbd>d</kbd> on macOS.

   The browser must be running with remote debugging enabled, which isn't the default. If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open. Follow the instructions for your browser, which opens a new browser window. Close the previous browser window.
-->

1. Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination zum Debuggen aus dem vorherigen Schritt eine neue Debuggerregisterkarte.

1. Nach einem kurzen Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys der App im Knoten `file://` an.

1. In Komponentencode (`.razor`-Dateien) und C#-Codedateien (`.cs`) werden bei Codeausführung von Ihnen festgelegte Breakpoints erreicht. Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).

Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert. Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).

## <a name="browser-source-maps"></a>Browserquellzuordnungen

Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet. Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu. Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.

## <a name="firewall-configuration"></a>Firewallkonfiguration

Wenn eine Firewall die Kommunikation mit dem Debugproxy blockiert, erstellen Sie eine Firewallausnahmeregel, die die Kommunikation zwischen Browser und `NodeJS`-Prozess zulässt.

> [!WARNING]
> Änderungen an einer Firewallkonfiguration müssen umsichtig erfolgen, damit keine Sicherheitsrisiken entstehen. Setzen Sie Sicherheitsleitlinien sorgfältig um, befolgen Sie bewährte Methoden, und beachten Sie die Warnungen des Herstellers der Firewall.
>
> Für das Zulassen einer offenen Kommunikation mit dem `NodeJS`-Prozess gilt Folgendes:
>
> * Öffnet den Node-Server für jede beliebige Verbindung abhängig von den Fähigkeiten und der Konfiguration der Firewall.
> * Kann abhängig von Ihrem Netzwerk riskant sein.
> * **Wird nur auf Entwicklercomputern empfohlen.**
>
> Lassen Sie nach Möglichkeit eine offene Kommunikation mit dem `NodeJS`-Prozess  **nur in vertrauenswürdigen oder privaten Netzwerken** zu.

Eine Anleitung zur Konfiguration der [Windows-Firewall](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) finden Sie unter [Erstellen einer Regel für eingehende Programme oder Dienste](/windows/security/threat-protection/windows-firewall/create-an-inbound-program-or-service-rule). Weitere Informationen finden Sie unter [Windows Defender-Firewall mit erweiterter Sicherheit](/windows/security/threat-protection/windows-firewall/windows-firewall-with-advanced-security) und zugehörigen Artikeln in der Dokumentation zur Windows-Firewall.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:

* Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.
* Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen. Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio erfordert die Option **JavaScript-Debugging für ASP.NET aktivieren (Chrome, Microsoft Edge und IE)** in **Tools** > **Optionen** > **Debuggen** > **Allgemein**. Dies ist die Standardeinstellung für Visual Studio. Wenn das Debuggen nicht funktioniert, vergewissern Sie sich, dass die Option ausgewählt ist.
* Wenn Ihre Umgebung einen HTTP-Proxy verwendet, sorgen Sie dafür, dass `localhost` in den Umgehungseinstellungen des Proxys eingeschlossen ist. Dies können Sie tun, indem Sie die `NO_PROXY`-Umgebungsvariable an einem der beiden folgenden Orte festlegen:
  * Die `launchSettings.json`-Datei für das Projekt
  * Auf Ebene der Umgebungsvariablen für Benutzer oder System, damit sie auf alle Apps angewendet wird. Wenn Sie eine Umgebungsvariable verwenden, starten Sie Visual Studio neu, damit die Änderungen übernommen werden.
* Stellen Sie sicher, dass Firewalls oder Proxys die Kommunikation mit dem Debugproxy (`NodeJS`-Prozess) nicht blockieren. Weitere Informationen finden Sie im Abschnitt [Firewallkonfiguration](#firewall-configuration).

### <a name="breakpoints-in-oninitializedasync-not-hit"></a>In `OnInitialized{Async}` nicht verwendete Breakpoints

Der Debugproxy des Blazor-Frameworks benötigt eine kurze Zeit zum Starten, sodass Breakpoints in der [`OnInitialized{Async}`-Lebenszyklusmethode](xref:blazor/components/lifecycle#component-initialization-methods) möglicherweise nicht verwendet werden. Es wird empfohlen, am Anfang des Methodentextes eine Verzögerung hinzuzufügen, um dem Debugproxy etwas Zeit zum Starten zu geben, bevor der Breakpoint erkannt wird. Sie können die Verzögerung auf Grundlage einer [`if`-Compileranweisung einschließen](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if), um sicherzustellen, dass die Verzögerung für einen Releasebuild der App nicht vorhanden ist.

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A>:

```csharp
protected override void OnInitialized()
{
#if DEBUG
    Thread.Sleep(10000)
#endif

    ...
}
```

<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>:

```csharp
protected override async Task OnInitializedAsync()
{
#if DEBUG
    await Task.Delay(10000)
#endif

    ...
}
```

### <a name="visual-studio-windows-timeout"></a>Timeout in Visual Studio (Windows)

Wenn Visual Studio eine Ausnahme auslöst, dass der Debugadapter nicht gestartet werden konnte, und meldet, dass das Timeout erreicht wurde, können Sie das Timeout mit einer Registrierungseinstellung anpassen:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

Der Platzhalter `{TIMEOUT}` im vorangehenden Befehl wird in Millisekunden angegeben. Beispielsweise wird eine Minute als `60000` zugewiesen.
