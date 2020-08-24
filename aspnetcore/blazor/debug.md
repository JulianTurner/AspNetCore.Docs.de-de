---
title: Debuggen von ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps debuggen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/17/2020
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
uid: blazor/debug
ms.openlocfilehash: 5aeb333dc36ebc4c3a324b397793343e0335b1e1
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628364"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a>Debuggen von ASP.NET Core Blazor WebAssembly

[Daniel Roth](https://github.com/danroth27)

Das Debuggen von Blazor WebAssembly-Apps kann mithilfe der Browserentwicklertools in Chromium-basierten Browsern (Edge/Chrome) durchgeführt werden. Alternativ können Sie Ihre App mit Visual Studio oder Visual Studio Code debuggen.

Verfügbare Szenarien:

* Festlegen und Entfernen von Breakpoints.
* Führen Sie die App mit Debugunterstützung in Visual Studio und Visual Studio Code (<kbd>F5</kbd>-Unterstützung) aus.
* Führen Sie die einzelnen Schritte (<kbd>F10</kbd>) Ihres Codes aus.
* Fahren Sie die Codeausführung mit <kbd>F8</kbd> in einem Browser oder mit <kbd>F5</kbd> in Visual Studio oder Visual Studio Code fort.
* Beobachten Sie die Werte der lokalen Variablen in der Anzeige *Lokale Variablen*.
* Betrachten Sie die Aufrufliste, einschließlich der Aufrufketten, die von JavaScript zu .NET und von .NET zu JavaScript verlaufen.

Folgendes ist derzeit *nicht möglich*:

* Halt bei Ausnahmefehlern
* Erreichen von Breakpoints während des App-Starts, bevor der Debugproxy ausgeführt wird. Dazu gehören Breakpoints in `Program.Main` (`Program.cs`) und Breakpoints in den [`OnInitialized{Async}`-Methoden](xref:blazor/components/lifecycle#component-initialization-methods) von Komponenten, die von der ersten von der App angeforderten Seite geladen werden.

Die Debuggingfunktionen werden in zukünftigen Releases weiter verbessert.

## <a name="prerequisites"></a>Voraussetzungen

Zum Debuggen ist einer der folgenden Browser erforderlich:

* Google Chrome (Version 70 oder höher) (Standard)
* Microsoft Edge (Version 80 oder höher)

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a>Aktivieren des Debuggens für Visual Studio und Visual Studio Code

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

## <a name="visual-studio"></a>Visual Studio

So debuggen Sie eine Blazor WebAssembly-App in Visual Studio:

1. Erstellen Sie eine neue von ASP.NET Core gehostete Blazor WebAssembly-App.
1. Drücken Sie <kbd>F5</kbd>, um die App im Debugger auszuführen.

   > [!NOTE]
   > Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt. Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.

1. Legen Sie in `Pages/Counter.razor` einen Breakpoint in der `IncrementCount`-Methode fest.
1. Rufen Sie die Registerkarte **`Counter`** auf, und klicken Sie auf die Schaltfläche zum Erreichen des Breakpoints:

   ![„Counter.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. Überprüfen Sie den Wert im `currentCount`-Feld im Fenster „Lokale Variablen“:

   ![Lokale Variablen anzeigen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen.

Während Sie Ihre Blazor WebAssembly-App debuggen, können Sie auch Ihren Servercode debuggen:

1. Legen Sie einen Breakpoint auf der Seite `Pages/FetchData.razor` in<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> fest.
1. Legen Sie einen Breakpoint in der Aktionsmethode `Get` in `WeatherForecastController` fest.
1. Rufen Sie die Registerkarte **`Fetch Data`** auf, um den ersten Breakpoint in der Komponente `FetchData` zu erreichen, bevor sie eine HTTP-Anforderung an den Server übermittelt:

   ![„FetchData.razor“ debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. Drücken Sie <kbd>F5</kbd>, um die Ausführung fortzusetzen und dann den Breakpoint in `WeatherForecastController` auf dem Server zu erreichen:

   ![Debugserver](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. Drücken Sie wieder <kbd>F5</kbd>, um die Ausführung fortzusetzen, damit die WeatherForecast-Tabelle gerendert wird.

<a id="vscode"></a>

## <a name="visual-studio-code"></a>Visual Studio Code

### <a name="debug-standalone-no-locblazor-webassembly"></a>Debuggen von eigenständiger Blazor WebAssembly

1. Öffnen Sie die eigenständige Blazor WebAssembly-App in VS Code.

   Möglicherweise erhalten Sie die folgende Benachrichtigung, dass zum Aktivieren des Debuggens ein zusätzliches Setup erforderlich ist:
   
   ![Zusätzliches Setup erforderlich](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   Wenn Sie diese Benachrichtigung erhalten, führen Sie die folgenden Schritte aus:

   * Stellen Sie sicher, dass die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) installiert ist. Öffnen Sie zur Überprüfung der installierten Erweiterungen in der Menüleiste die Option **Ansicht** > **Erweiterungen**, oder klicken Sie in der Seitenleiste **Aktivität** auf das Symbol **Erweiterungen**.
   * Vergewissern Sie sich, dass das JavaScript-Vorschaudebuggen aktiviert ist. Öffnen Sie die Einstellungen über die Menüleiste (**Datei** > **Einstellungen** > **Einstellungen**). Führen Sie mithilfe der Schlüsselwörter `debug preview` eine Suche durch. Vergewissern Sie sich in den Suchergebnissen, dass das Kontrollkästchen **Debuggen > JavaScript: Vorschau verwenden** aktiviert ist. Wenn die Option zum Aktivieren des Vorschaudebuggens nicht vorhanden ist, führen Sie entweder ein Upgrade auf die neueste VS Code-Version durch, oder installieren Sie die [JavaScript-Debuggererweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) (VS Code-Versionen 1.46 oder früher).
   * Laden Sie das Fenster neu.

1. Starten Sie das Debuggen mit der Tastenkombination <kbd>F5</kbd> oder dem Menü Element.

   > [!NOTE]
   > Das **Starten ohne Debuggen** (<kbd>STRG</kbd>+<kbd>F5</kbd>) wird nicht unterstützt. Wenn die App in der Debugkonfiguration ausgeführt wird, führt der Debugmehraufwand immer zu einer geringen Leistungsminderung.

1. Wenn Sie dazu aufgefordert werden, wählen Sie die Option **Blazor WebAssembly debuggen**, um das Debuggen zu starten.

   ![Liste der verfügbaren Debugoptionen](index/_static/blazor-vscode-debugtypes.png)

1. Die eigenständige App wird gestartet, und es wird ein Debuggingbrowser geöffnet.

1. Legen Sie einen Breakpoint in der `IncrementCount`-Methode der `Counter`-Komponente fest, und klicken Sie dann auf die Schaltfläche, um den Breakpoint zu erreichen:

   ![Counter.razor in VS Code debuggen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a>Debuggen der gehosteten Blazor WebAssembly

1. Öffnen Sie den Projektmappenordner der gehosteten Blazor WebAssembly-App in VS Code.

1. Wenn für das Projekt keine Startkonfiguration eingestellt ist, wird folgende Benachrichtigung angezeigt. Wählen Sie **Ja**.

   ![Erforderliche Ressourcen hinzufügen](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. Wählen Sie in der Befehlspalette oben im Fenster das Projekt *Server* in der gehosteten Lösung aus.

Zum Starten des Debuggers wird eine `launch.json`-Datei mit der Startkonfiguration generiert.

### <a name="attach-to-an-existing-debugging-session"></a>Anfügen an eine vorhandene Debugsitzung

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

### <a name="launch-configuration-options"></a>Optionen für die Startkonfiguration

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

### <a name="example-launch-configurations"></a>Beispiel für Startkonfigurationen

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a>Starten und Debuggen einer eigenständigen Blazor WebAssembly-App

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a>Anfügen an eine laufende App unter einer angegebenen URL

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a>Starten und Debuggen einer gehosteten Blazor WebAssembly-App mit Microsoft Edge

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

## <a name="debug-in-the-browser"></a>Debuggen im Browser

1. Führen Sie ein Debugbuild der App in der Entwicklungsumgebung aus.

1. Starten Sie einen Browser, und navigieren Sie zur URL der App (z. B. `https://localhost:5001`).

1. Versuchen Sie, das Remotedebuggen im Browser zu starten, indem Sie <kbd>UMSCHALTTASTE</kbd>+<kbd>ALT-TASTE</kbd>+<kbd>D</kbd> drücken.

   Der Browser muss mit aktiviertem Remotedebuggen ausgeführt werden, was nicht das Standardverhalten ist. Wenn das Remotedebuggen deaktiviert ist, wird eine Seite mit der Meldung **Debugfähige Browserregisterkarte nicht gefunden** gerendert, die Anweisungen zum Starten des Browsers mit geöffnetem Debuggingport enthält. Befolgen Sie die für Ihren Browser geltenden Anweisungen. Dadurch wird ein neues Browserfenster geöffnet. Schließen Sie das vorherige Browserfenster.

1. Sobald der Browser mit aktiviertem Remotedebuggen ausgeführt wird, öffnet die Tastenkombination zum Debuggen (<kbd>UMSCHALTTASTE</kbd>+<kbd>ALT-TASTE</kbd>+<kbd>D</kbd>) eine neue Debuggerregisterkarte.

1. Nach einem kurzen Moment zeigt die Registerkarte **Quellen** eine Liste der .NET-Assemblys der App im Knoten `file://` an.

1. In Komponentencode (`.razor`-Dateien) und C#-Codedateien (`.cs`) werden bei Codeausführung von Ihnen festgelegte Breakpoints erreicht. Nachdem ein Breakpoint erreicht wurde, durchlaufen Sie den Code per Einzelschritt (<kbd>F10</kbd>), oder setzen Sie die Codeausführung normal fort (<kbd>F8</kbd>).

Blazor bietet einen Debugproxy, der das [Chrome DevTools-Protokoll](https://chromedevtools.github.io/devtools-protocol/) implementiert und das Protokoll um .NET-spezifische Informationen erweitert. Wenn die Tastenkombination zum Debuggen gedrückt wird, verweist Blazor die Chrome DevTools auf den Proxy. Der Proxy stellt eine Verbindung mit dem Browserfenster her, das Sie debuggen möchten (daher die Notwendigkeit, das Remotedebuggen zu aktivieren).

## <a name="browser-source-maps"></a>Browserquellzuordnungen

Browserquellzuordnungen ermöglichen es dem Browser, kompilierte Dateien zu ihren ursprünglichen Quelldateien zurück zuzuordnen und sie werden häufig für clientseitiges Debuggen verwendet. Allerdings ordnet Blazor C# derzeit nicht direkt zu JavaScript/WASM zu. Stattdessen übernimmt Blazor die IL-Interpretation innerhalb des Browsers, sodass Quellzuordnungen nicht relevant sind.

## <a name="troubleshoot"></a>Problembehandlung

Wenn Sie auf Fehler stoßen, könnten die folgenden Tipps helfen:

* Öffnen Sie auf der Registerkarte **Debugger** die Entwicklertools in Ihrem Browser. Führen Sie in der Konsole `localStorage.clear()` aus, um alle Breakpoints zu entfernen.
* Vergewissern Sie sich, dass Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat installiert haben und diesem vertrauen. Weitere Informationen finden Sie unter <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.
* Visual Studio erfordert die Option **JavaScript-Debugging für ASP.NET aktivieren (Chrome, Microsoft Edge und IE)** in **Tools** > **Optionen** > **Debuggen** > **Allgemein**. Dies ist die Standardeinstellung für Visual Studio. Wenn das Debuggen nicht funktioniert, vergewissern Sie sich, dass die Option ausgewählt ist.

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

### <a name="visual-studio-timeout"></a>Visual Studio-Timeout

Wenn Visual Studio eine Ausnahme auslöst, dass der Debugadapter nicht gestartet werden konnte, und meldet, dass das Timeout erreicht wurde, können Sie das Timeout mit einer Registrierungseinstellung anpassen:

```console
VsRegEdit.exe set "<VSInstallFolder>" HKCU JSDebugger\Options\Debugging "BlazorTimeoutInMilliseconds" dword {TIMEOUT}
```

Der Platzhalter `{TIMEOUT}` im vorangehenden Befehl wird in Millisekunden angegeben. Beispielsweise wird eine Minute als `60000` zugewiesen.
