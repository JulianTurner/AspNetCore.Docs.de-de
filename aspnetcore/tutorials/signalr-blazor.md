---
title: Verwenden von ASP.NET Core SignalR mit Blazor
author: guardrex
description: Erstellen Sie eine Chat-App, die ASP.NET Core SignalR mit Blazor verwendet.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/25/2021
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
uid: tutorials/signalr-blazor
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: f4e51b39c4c3b0c444b08025e9bd74eec0747541
ms.sourcegitcommit: a49c47d5a573379effee5c6b6e36f5c302aa756b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100536373"
---
# <a name="use-aspnet-core-signalr-with-blazor"></a>Verwenden von ASP.NET Core SignalR mit Blazor

In diesem Tutorial werden die Grundlagen zur Erstellung einer Echtzeit-App mit SignalR mit Blazor beschrieben. Sie lernen Folgendes:

> [!div class="checklist"]
> * Erstellen eines Blazor-Projekts
> * Hinzufügen der SignalR-Clientbibliothek
> * Hinzufügen eines SignalR-Hubs
> * Hinzufügen von SignalR-Diensten und eines Endpunkts zum SignalR-Hub
> * Hinzufügen von Razor-Komponentencode für Chat

Am Ende dieses Tutorials verfügen Sie über eine funktionierende Chat-App.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* [Visual Studio 2019 Version 16.8 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET und Webentwicklung**
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* [Visual Studio für Mac ab Version 8.8](https://visualstudio.microsoft.com/vs/mac/)
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

::: zone pivot="webassembly"

## <a name="create-a-hosted-blazor-webassembly-app"></a>Erstellen einer gehosteten Blazor WebAssembly-App

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

1. Geben Sie im Feld **Projektname** `BlazorWebAssemblySignalRApp` ein. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie die **Blazor WebAssembly App**-Vorlage aus.

1. Aktivieren Sie unter **Erweitert** das Kontrollkästchen **Von ASP.net Core gehostet**.

1. Wählen Sie **Erstellen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie in einer Befehlsshell den folgenden Befehle aus:

   ```dotnetcli
   dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
   ```

   Mit der Option `-ho|--hosted` wird eine gehostete Blazor WebAssembly-Projektmappe erstellt. Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.

   Mit der Option `-o|--output` wird ein Ordner für die Projektmappe erstellt. Wenn Sie einen Ordner für die Projektmappe erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen der Projektmappe die Option `-o|--output` und den entsprechenden Wert aus.

1. Öffnen Sie in Visual Studio Code den Projektordner der App.

1. Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus. Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

1. Wählen Sie die **Blazor WebAssembly App**-Vorlage aus. Klicken Sie auf **Weiter**.

1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Aktivieren Sie das Kontrollkästchen **ASP.NET Core Hosted** (Auf ASP.NET Core gehostet). Klicken Sie auf **Weiter**.

1. Benennen Sie im Feld **Projektname** die App `BlazorWebAssemblySignalRApp`. Wählen Sie **Erstellen** aus.

   Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.

1. Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell den folgenden Befehle aus:

```dotnetcli
dotnet new blazorwasm -ho -o BlazorWebAssemblySignalRApp
```

Mit der Option `-ho|--hosted` wird eine gehostete Blazor WebAssembly-Projektmappe erstellt.

Mit der Option `-o|--output` wird ein Ordner für die Projektmappe erstellt. Wenn Sie einen Ordner für die Projektmappe erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen der Projektmappe die Option `-o|--output` und den entsprechenden Wert aus.

---

## <a name="add-the-signalr-client-library"></a>Hinzufügen der SignalR-Clientbibliothek

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Wählen Sie in den Suchergebnissen das Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) aus. Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt. Wählen Sie **Installieren** aus.

1. Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** auf der Symbolleiste) den folgenden Befehl aus:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Client`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt. Klicken Sie auf **Paket hinzufügen**.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell im Ordner für die Projektmappe den folgenden Befehl aus:

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Hinzufügen des System.Text.Encodings.Web-Pakets

*Dieser Abschnitt gilt nur für Apps für ASP.NET Core Version 3.x.*

Aufgrund eines Problems bei der Paketauflösung bei der Verwendung von [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in einer ASP.NET Core 3.x-App erfordert das `BlazorWebAssemblySignalRApp.Server`-Projekt einen Paketverweis für [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). Das zugrunde liegende Problem wird in einem zukünftigen Patchrelease von .NET 5 gelöst. Weitere Informationen finden Sie unter [System.Text.Json defines netcoreapp3.0 with no dependencies #45560](https://github.com/dotnet/runtime/issues/45560) (System.Text.Json definiert netcoreapp3.0 ohne Abhängigkeiten – Nr.45560).

Um [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) dem `BlazorWebAssemblySignalRApp.Server`-Projekt der unter ASP.NET Core 3.1 gehosteten Blazor-Lösung hinzuzufügen, befolgen Sie die Anleitungen für Ihre Toolsauswahl:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.

1. Wählen Sie in den Suchergebnissen das Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) aus. Wählen Sie die Version des Pakets aus, die zum verwendeten freigegebenen Framework passt. Wählen Sie **Installieren** aus.

1. Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Führen Sie im **integrierten Terminal** (**Ansicht**>**Terminal** in der Symbolleiste) die folgenden Befehle aus:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorWebAssemblySignalRApp.Server`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.

1. Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), wählen Sie die richtige Version des Pakets für das verwendete freigegebene Framework aus, und klicken Sie auf **Paket hinzufügen**.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell im Ordner für die Projektmappe den folgenden Befehl aus:

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Hinzufügen eines SignalR-Hubs

Erstellen Sie im `BlazorWebAssemblySignalRApp.Server`-Projekt einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub

1. Öffnen Sie im Projekt `BlazorWebAssemblySignalRApp.Server` die Datei `Startup.cs`.

1. Fügen Sie am Anfang der Datei den Namespace für die `ChatHub`-Klasse hinzu:

   ```csharp
   using BlazorWebAssemblySignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]

1. In `Startup.Configure`:

   * Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.
   * Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Fügen Sie SignalR und Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. In `Startup.Configure`:

   * Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.
   * Fügen Sie zwischen den Endpunkten für Controller und dem clientseitigen Fallback einen Endpunkt für den Hub hinzu.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Hinzufügen von Razor-Komponentencode für Chat

1. Öffnen Sie im Projekt `BlazorWebAssemblySignalRApp.Client` die Datei `Pages/Index.razor`.

::: moniker range=">= aspnetcore-5.0"

1. Ersetzen Sie das Markup durch folgenden Code:

   [!code-razor[](signalr-blazor/samples/5.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Ersetzen Sie das Markup durch folgenden Code:

   [!code-razor[](signalr-blazor/samples/3.x/BlazorWebAssemblySignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Ausführen der App

Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Wählen Sie im **Projektmappen-Explorer** das `BlazorWebAssemblySignalRApp.Server`-Projekt aus. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.

1. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Wählen Sie in der **Projektmappen**-Randleiste das `BlazorWebAssemblySignalRApp.Server`-Projekt aus. Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Führen Sie in einer Befehlsshell im Ordner für die Projektmappe die folgenden Befehle aus:

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

::: zone pivot="server"

## <a name="create-a-blazor-server-app"></a>Erstellen einer Blazor Server-App

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

1. Geben Sie im Feld **Projektname** `BlazorServerSignalRApp` ein. Vergewissern Sie sich, dass der Eintrag für den **Speicherort** korrekt ist, oder geben Sie einen Speicherort für das Projekt an. Wählen Sie **Erstellen** aus.

1. Wählen Sie die **Blazor Server App**-Vorlage aus.

1. Wählen Sie **Erstellen** aus.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie in einer Befehlsshell den folgenden Befehle aus:

   ```dotnetcli
   dotnet new blazorserver -o BlazorServerSignalRApp
   ```

   Mit der Option `-o|--output` wird ein Ordner für das Projekt erstellt. Wenn Sie einen Ordner für das Projekt erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen des Projekts die Option `-o|--output` und den entsprechenden Wert aus.

1. Öffnen Sie in Visual Studio Code den Projektordner der App.

1. Wenn das Dialogfeld angezeigt wird, um Assets zum Erstellen und Debuggen der App hinzuzufügen, wählen Sie **Ja** aus. Visual Studio Code fügt den `.vscode`-Ordner automatisch mit generierten `launch.json`- und `tasks.json`-Dateien hinzu. Weitere Informationen zur Konfiguration von VS Code-Objekten im Ordner `.vscode` finden Sie im Leitfaden für **Linux**-Betriebssysteme unter <xref:blazor/tooling>.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Installieren Sie die neueste Version von [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/), und führen Sie die folgenden Schritte aus:

1. Wählen Sie **Datei** > **Neue Projektmappe** aus, oder erstellen Sie im **Startfenster** ein **Neues Projekt**.

1. Klicken Sie auf der Randleiste auf **Web and Console** > **App** (Web und Konsole).

1. Wählen Sie die **Blazor Server App**-Vorlage aus. Klicken Sie auf **Weiter**.

1. Vergewissern Sie sich, dass **Authentifizierung** auf **Keine Authentifizierung** festgelegt ist. Klicken Sie auf **Weiter**.

1. Benennen Sie im Feld **Projektname** die App `BlazorServerSignalRApp`. Wählen Sie **Erstellen** aus.

   Wenn eine Eingabeaufforderung dem Entwicklungszertifikat zu vertrauen scheint, vertrauen Sie dem Zertifikat und fahren Sie fort. Das Benutzer- und Keychainkennwort sind erforderlich, um dem Zertifikat zu vertrauen.

1. Öffnen Sie das Projekt, indem Sie zum Projektordner navigieren und die Projektmappendatei des Projekts (`.sln`) öffnen.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell den folgenden Befehle aus:

```dotnetcli
dotnet new blazorserver -o BlazorServerSignalRApp
```

Mit der Option `-o|--output` wird ein Ordner für das Projekt erstellt. Wenn Sie einen Ordner für das Projekt erstellt haben und dieser Ordner in der Befehlsshell geöffnet ist, lassen Sie zum Erstellen des Projekts die Option `-o|--output` und den entsprechenden Wert aus.

---

## <a name="add-the-signalr-client-library"></a>Hinzufügen der SignalR-Clientbibliothek

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Wählen Sie in den Suchergebnissen das Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) aus. Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt. Wählen Sie **Installieren** aus.

1. Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Führen Sie im **integrierten Terminal** (**Ansicht** > **Terminal** auf der Symbolleiste) den folgenden Befehl aus:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `Microsoft.AspNetCore.SignalR.Client` in das Suchfeld ein.

1. Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client). Legen Sie die Version so fest, dass sie zum freigegebenen Framework der App passt. Klicken Sie auf **Paket hinzufügen**.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell im Ordner für das Projekt den folgenden Befehl aus:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a>Hinzufügen des System.Text.Encodings.Web-Pakets

*Dieser Abschnitt gilt nur für Apps für ASP.NET Core Version 3.x.*

Aufgrund eines Problems bei der Paketauflösung bei der Verwendung von [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.x in einer ASP.NET Core 3.x-App erfordert das Projekt einen Paketverweis für [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web). Das zugrunde liegende Problem wird in einem zukünftigen Patchrelease von .NET 5 gelöst. Weitere Informationen finden Sie unter [System.Text.Json defines netcoreapp3.0 with no dependencies #45560](https://github.com/dotnet/runtime/issues/45560) (System.Text.Json definiert netcoreapp3.0 ohne Abhängigkeiten – Nr.45560).

Befolgen Sie den Leitfaden für Ihr bevorzugtes Tool, um [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) zum Projekt hinzuzufügen:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio/)

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob die **Paketquelle** auf `nuget.org` eingestellt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.

1. Wählen Sie in den Suchergebnissen das Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) aus. Wählen Sie die Version des Pakets aus, die zum verwendeten freigegebenen Framework passt. Wählen Sie **Installieren** aus.

1. Wenn das Dialogfeld **Vorschau der Änderungen anzeigen** angezeigt wird, wählen Sie die Option **OK** aus.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code/)

Führen Sie im **integrierten Terminal** (**Ansicht**>**Terminal** in der Symbolleiste) die folgenden Befehle aus:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Klicken Sie in der **Projektmappen**-Randleiste mit der rechten Maustaste auf das `BlazorServerSignalRApp`-Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Überprüfen Sie im Dialogfeld **NuGet-Pakete verwalten**, ob in der Dropdownliste für die Paketquelle `nuget.org` ausgewählt ist.

1. Geben Sie bei Auswahl von **Durchsuchen** `System.Text.Encodings.Web` in das Suchfeld ein.

1. Aktivieren Sie in den Suchergebnissen das Kontrollkästchen neben dem Paket [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web), wählen Sie die richtige Version des Pakets für das verwendete freigegebene Framework aus, und klicken Sie auf **Paket hinzufügen**.

1. Wenn das Dialogfeld **Zustimmung zur Lizenz** erscheint, wählen Sie **Ich stimme zu** aus, wenn Sie mit den Lizenzbedingungen einverstanden sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Führen Sie in einer Befehlsshell im Ordner für das Projekt den folgenden Befehl aus:

```dotnetcli
dotnet add package System.Text.Encodings.Web
```

Geben Sie die Option `--version {VERSION}` an (wobei der Platzhalter `{VERSION}` für die Version des hinzuzufügenden Pakets steht), um eine frühere Version des Pakets hinzuzufügen.

---

::: moniker-end

## <a name="add-a-signalr-hub"></a>Hinzufügen eines SignalR-Hubs

Erstellen Sie einen `Hubs`-Ordner (Plural), und fügen Sie die folgende `ChatHub`-Klasse (`Hubs/ChatHub.cs`) hinzu:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a>Hinzufügen von Diensten und eines Endpunkts zum SignalR-Hub

1. Öffnen Sie die Datei `Startup.cs` .

1. Fügen Sie am Anfang der Datei die Namespaces für <xref:Microsoft.AspNetCore.ResponseCompression?displayProperty=fullName> und die `ChatHub`-Klasse hinzu:

   ```csharp
   using Microsoft.AspNetCore.ResponseCompression;
   using BlazorServerSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. Fügen Sie Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. In `Startup.Configure`:

   * Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.
   * Fügen Sie zwischen den Endpunkten für die Zuordnung des Blazor-Hubs und den clientseitigen Fallback einen Endpunkt für den Hub hinzu.

   [!code-csharp[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Fügen Sie Middlewaredienste für die Komprimierung von Antworten zu `Startup.ConfigureServices` hinzu:

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

1. In `Startup.Configure`:

   * Verwenden Sie die Middlewaredienste für die Komprimierung von Antworten am Anfang der Konfiguration der Verarbeitungspipeline.
   * Fügen Sie zwischen den Endpunkten für die Zuordnung des Blazor-Hubs und den clientseitigen Fallback einen Endpunkt für den Hub hinzu.

   [!code-csharp[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Startup.cs?name=snippet_Configure&highlight=3,23)]

::: moniker-end

## <a name="add-razor-component-code-for-chat"></a>Hinzufügen von Razor-Komponentencode für Chat

1. Öffnen Sie die Datei `Pages/Index.razor` .

::: moniker range=">= aspnetcore-5.0"

1. Ersetzen Sie das Markup durch folgenden Code:

   [!code-razor[](signalr-blazor/samples/5.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Ersetzen Sie das Markup durch folgenden Code:

   [!code-razor[](signalr-blazor/samples/3.x/BlazorServerSignalRApp/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a>Ausführen der App

Befolgen Sie die Anleitungen für die Auswahl Ihrer Tools:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Drücken Sie <kbd>F5</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>STRG</kbd>+<kbd>F5</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Drücken Sie <kbd>⌘</kbd>+<kbd>↩</kbd>, um die App mit Debuggen auszuführen, oder drücken Sie <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd>, um die App ohne Debuggen auszuführen.

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

1. Führen Sie in einer Befehlsshell im Ordner für das Projekt die folgenden Befehle aus:

   ```dotnetcli
   dotnet run
   ```

1. Kopieren Sie die URL aus der Adressleiste, öffnen Sie eine neue Browserinstanz oder Registerkarte, und fügen Sie die URL in die Adressleiste ein.

1. Geben Sie in einem der beiden Browser einen Namen und eine Nachricht ein, und klicken Sie auf die Schaltfläche zum Senden der Nachricht. Der Name und die Nachricht werden sofort auf beiden Seiten angezeigt:

   ![In zwei Browserfenstern geöffnete SignalR Blazor-Beispiel-App mit ausgetauschten Nachrichten](signalr-blazor/_static/signalr-blazor-finished.png)

   Zitate: *Star Trek VI: Das unentdeckte Land* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)

---

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Blazor-Projekts
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
* [Ursprungsübergreifende SignalR-Aushandlung für die Authentifizierung](xref:blazor/fundamentals/signalr#signalr-cross-origin-negotiation-for-authentication)
* <xref:blazor/debug>
* <xref:blazor/security/server/threat-mitigation>
