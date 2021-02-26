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
# <a name="tooling-for-aspnet-core-blazor"></a>Tools für ASP.NET-Core Blazor

::: zone pivot="windows"

1. Installieren Sie die neueste Version von [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) mit der Workload **ASP.NET und Webentwicklung**.

1. Erstellen Sie ein neues Projekt.

1. Klicken Sie auf **Blazor-App**. Klicken Sie auf **Weiter**.

1. Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus. Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus. Wählen Sie **Erstellen** aus.

   Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

Wenn Sie eine gehostete Blazor WebAssembly-App ausführen, führen Sie sie über das Projekt **`Server`** der Projektmappe aus.

::: zone-end

::: zone pivot="linux"

1. Installieren Sie die neueste Version des [.NET Core SDK](https://dotnet.microsoft.com/download). Wenn Sie das SDK zuvor installiert haben, können Sie die installierte Version ermitteln, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

   ```dotnetcli
   dotnet --version
   ```

1. Installieren Sie die neueste Version von [Visual Studio Code](https://code.visualstudio.com).

1. Installieren Sie die aktuellste [C# für Visual Studio Code-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

1. Für eine Blazor WebAssembly-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   Fügen Sie dem Befehl für ein gehostetes Blazor WebAssembly-Modell die Hostingoption (`-ho` oder `--hosted`) hinzu:

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1 -ho
   ```

   Für eine Blazor Server-Benutzeroberfläche führen Sie den folgenden Befehl in einer Befehlsshell aus:

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Öffnen Sie in Visual Studio Code den Ordner `WebApplication1`.

1. Die IDE fordert an, dass Sie Ressourcen zum Erstellen und Debuggen des Projekts hinzufügen. Wählen Sie **Ja**.

   **Start- und Taskkonfiguration für gehostete Blazor WebAssembly-Projektmappen**

   Fügen Sie für gehostete Blazor WebAssembly-Projektmappen den Ordner `.vscode` mit den Dateien `launch.json` und `tasks.json` zum übergeordneten Ordner der Projektmappe hinzu, bei dem es sich um den Ordner handelt, der die typischen Projektordnernamen `Client`, `Server` und `Shared` enthält (oder verschieben Sie ihn in diesen übergeordneten Ordner). Aktualisieren Sie die Konfiguration in den Dateien `launch.json` und `tasks.json`, oder bestätigen Sie, dass diese eine gehostete Blazor WebAssembly-App im Projekt **`Server`** ausführen.

   **`.vscode/launch.json`** (`launch`-Konfiguration):

   ```json
   ...
   "cwd": "${workspaceFolder}/{SERVER APP FOLDER}",
   ...
   ```

   In der obigen Konfiguration für das aktuelle Arbeitsverzeichnis (`cwd`) steht der Platzhalter `{SERVER APP FOLDER}` für den Ordner des Projekts **`Server`** , in der Regel „`Server`“.

   Wenn Microsoft Edge verwendet wird und Google Chrome nicht auf dem System installiert ist, fügen Sie der Konfiguration die zusätzliche Eigenschaft `"browser": "edge"` hinzu.

   Beispiel für einen Projektordner von `Server`, der Microsoft Edge statt den Standardbrowser Google Chrome als Browser für Debugausführungen verwendet:

   ```json
   ...
   "cwd": "${workspaceFolder}/Server",
   "browser": "edge"
   ...
   ```

   **`.vscode/tasks.json`** (Argumente des [`dotnet`-Befehls](/dotnet/core/tools/dotnet)):

   ```json
   ...
   "${workspaceFolder}/{SERVER APP FOLDER}/{PROJECT NAME}.csproj",
   ...
   ```

   Für das obige Argument gilt Folgendes:

   * Der Platzhalter `{SERVER APP FOLDER}` steht für den Ordner des Projekts **`Server`** , in der Regel „`Server`“.
   * Der Platzhalter `{PROJECT NAME}` steht für den Namen der App, der in der Regel auf dem Namen der Projektmappe basiert, gefolgt von „`.Server`“ bei einer App, die aus einer Blazor-Projektvorlage generiert wurde.

   Im folgenden Beispiel aus dem [Tutorial für die Verwendung von SignalR mit einer Blazor WebAssembly-App](xref:tutorials/signalr-blazor) werden der Projektordnername `Server` und der Projektname `BlazorWebAssemblySignalRApp.Server` verwendet:

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

1. Drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App auszuführen.

## <a name="trust-a-development-certificate"></a>Festlegen eines Entwicklungszertifikats als vertrauenswürdig

Es gibt keine zentrale Möglichkeit, ein Zertifikat in Linux als vertrauenswürdig festzulegen. In der Regel wird einer der folgenden Ansätze verwendet:

* Ausschließen der App-URL über die Ausschlussliste des Browsers.
* Festlegen aller selbstsignierten Zertifikate für `localhost` als vertrauenswürdig.
* Hinzufügen des Zertifikats zur Liste der vertrauenswürdigen Zertifikate im Browser.

Weitere Informationen finden Sie in der Dokumentation zu Ihrem Browseranbieter und Ihrer Linux-Distribution.

::: zone-end

::: zone pivot="macos"

1. Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

   Wählen Sie für eine Blazor WebAssembly-Benutzeroberfläche die **Blazor WebAssembly-App-** Vorlage aus. Wählen Sie für eine Blazor Server-Benutzeroberfläche die **Blazor Server-App-** Vorlage aus. Klicken Sie auf **Weiter**.

   Informationen zu den beiden Hostingmodellen für Blazor, *Blazor WebAssembly* (eigenständig und gehostet) und *Blazor Server* , finden Sie unter <xref:blazor/hosting-models>.

1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Klicken Sie auf **Weiter**.

1. Aktivieren Sie für das gehostete Modell von Blazor WebAssembly das Kontrollkästchen **ASP.NET Core, gehostet**.

1. Benennen Sie im Feld **Projektname** die App `WebApplication1`. Wählen Sie **Erstellen** aus.

1. Wählen Sie **Ausführen** > **Ohne Debuggen starten** aus, um die App *ohne Debugger* auszuführen. Führen Sie die App entweder über **Ausführen** > **Debuggen starten** oder über die Schaltfläche „Ausführen“ (&#9654;) *mit dem Debugger aus*.

Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen. Weitere Informationen zum Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig finden Sie hier: <xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos>.

Wenn Sie eine gehostete Blazor WebAssembly-App ausführen, führen Sie sie über das Projekt **`Server`** der Projektmappe aus.

::: zone-end

## <a name="use-visual-studio-code-for-cross-platform-blazor-development"></a>Verwenden von Visual Studio Code für die plattformübergreifende Blazor-Entwicklung

[Visual Studio Code](https://code.visualstudio.com/) ist eine plattformübergreifende Open-Source-Entwicklungsumgebung (Integrated Development Environment, IDE), mit der Blazor-Apps entwickelt werden können. Verwenden Sie die .NET-CLI zum Erstellen einer neuen Blazor-App für die Entwicklung mit Visual Studio Code. Weitere Informationen finden Sie in der [Linux-Version dieses Artikels](?pivots=linux).

## <a name="blazor-template-options"></a>Blazor-Vorlagenoptionen

Das Blazor-Framework bietet für beide Blazor-Hostingmodelle Vorlagen zum Erstellen neuer Apps. Mithilfe der Vorlagen können neue Blazor-Projekte und -Projektmappen erstellt werden, unabhängig von den Tools, die Sie für die Blazor-Entwicklung auswählen (Visual Studio, Visual Studio für Mac, Visual Studio Code oder die .NET-CLI):

* Blazor WebAssembly-Projektvorlage: `blazorwasm`
* Blazor Server-Projektvorlage: `blazorserver`

Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.

Vorlagenoptionen sind verfügbar, indem Sie die Hilfeoption (`-h` oder `--help`) in einer Befehlsshell an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:

```dotnetcli
dotnet new blazorwasm -h
dotnet new blazorserver -h
```
