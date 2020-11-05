---
title: Verwenden von ASP.NET Core SignalR mit Blazor WebAssembly
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor WebAssembly verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052706"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a>Verwenden von ASP.NET Core SignalR mit Blazor WebAssembly

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor WebAssembly beschrieben. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines gehosteten Blazor WebAssembly-App-Projekts
> * Hinzufügen der SignalR-Clientbibliothek
> * Hinzufügen eines SignalR-Hubs
> * Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub
> * Hinzufügen von Razor-Komponentencode für Chat

Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* [Visual Studio 2019 Version 16.8 oder höher (Vorschauversion)](https://visualstudio.microsoft.com/vs/preview/) mit der Workload **ASP.NET und Webentwicklung**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [Visual Studio für Mac, Version 8.8 oder höher (Vorschauversion)](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 Version 16.6 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* [Visual Studio für Mac Version 8.6 oder höher](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a>Erstellen eines gehosteten Blazor WebAssembly-App-Projekts

Befolgen Sie die Anleitungen für die Auswahl der Tools:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.8 oder höher und das .NET Core SDK 5.0.0 oder höher sind erforderlich.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Visual Studio 16.6 oder höher und das .NET Core SDK 3.1.300 oder höher sind erforderlich.

::: moniker-end

1. Erstellen Sie ein neues Projekt.

1. Klicken Sie auf **Blazor-App** und dann auf **Weiter**.

1. Geben Sie im Feld **Projektname** `BlazorSignalRApp` ein. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie die **Blazor WebAssembly App** -Vorlage aus.

1. Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.

1. Wählen Sie **Erstellen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie in einer Befehlsshell den folgenden Befehle aus:

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. Öffnen Sie in Visual Studio Code den Projektordner der App.

1. Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus. Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

1. Wählen Sie die **Blazor WebAssembly App** -Vorlage aus. Klicken Sie auf **Weiter**.

1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet). Klicken Sie auf **Weiter**.

1. Benennen Sie im Feld **Projektname** die App `BlazorSignalRApp`. Wählen Sie **Erstellen** aus.

   Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.

1. Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell den folgenden Befehle aus:

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a>Hinzufügen der SignalR-Clientbibliothek

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorSignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten** , ob die **Paketquelle** auf `nuget.org` eingestellt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Wählen Sie in den Suchergebnissen das [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)-Paket aus, und klicken Sie dann auf **Installieren**.

1. Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Führen Sie im **integrierten Terminal** ( **Ansicht** > **Terminal** in der Symbolleiste) die folgenden Befehle aus:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappen** -Randleiste mit der rechten Maustaste auf das `BlazorSignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten** , ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)-Paket, und wählen Sie **Paket hinzufügen** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell die folgenden Befehle aus:

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a>Hinzufügen eines SignalR-Hubs

Erstellen Sie im `BlazorSignalRApp.Server`-Projekt einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a>Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub

1. Öffnen Sie im Projekt `BlazorSignalRApp.Server` die Datei `Startup.cs`.

1. Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. In `Startup.Configure`:

   * Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.
   * Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a>Hinzufügen von Razor-Komponentencode für Chat

1. Öffnen Sie im Projekt `BlazorSignalRApp.Client` die Datei `Pages/Index.razor`.

1. Ersetzen Sie das Markup durch folgenden Code:

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Ausführen der App

1. Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie im **Projektmappen-Explorer** das `BlazorSignalRApp.Server`-Projekt aus. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Wenn VS Code anbietet, ein Startprofil für die Server-App zu erstellen (`.vscode/launch.json`), scheint der `program`-Eintrag, ähnlich wie der folgende, auf die Assembly der App (`{APPLICATION NAME}.Server.dll`) zu zeigen:

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Wählen Sie in der **Projektmappen** -Randleiste das `BlazorSignalRApp.Server`-Projekt aus. Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Führen Sie in einer Befehlsshell die folgenden Befehle aus:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![SignalR Blazor WebAssemblyBeispiel-App wird in zwei Browserfenstern geöffnet, in denen die ausgetauschte Nachrichten angezeigt werden.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Erstellen eines gehosteten Blazor WebAssembly-App-Projekts
> * Hinzufügen der SignalR-Clientbibliothek
> * Hinzufügen eines SignalR-Hubs
> * Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub
> * Hinzufügen von Razor-Komponentencode für Chat

Weitere Informationen zum Erstellen von Blazor-Apps finden Sie in der Blazor-Dokumentation:

> [!div class="nextstepaction"]
> <xref:blazor/index>
> [Bearertokenauthentifizierung mit Identity-Server, WebSockets und vom Server gesendeten Ereignissen](xref:signalr/authn-and-authz#bearer-token-authentication)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
* [Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
