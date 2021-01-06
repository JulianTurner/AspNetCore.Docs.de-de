---
title: Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio Code
author: rick-anderson
description: Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio Code veröffentlicht wird.
ms.author: riserrad
ms.custom: devx-track-csharp, mvc
ms.date: 07/10/2019
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
uid: tutorials/publish-to-azure-webapp-using-vscode
ms.openlocfilehash: a5a863682655517e27f6540af76342f95d4ecae0
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061261"
---
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a>Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio Code

Von [Ricardo Serradas](https://twitter.com/ricardoserradas)

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

Informationen zur Problembehandlung bei der App Service-Bereitstellung finden Sie unter <xref:test/troubleshoot-azure-iis>.

## <a name="intro"></a>Einführung

In diesem Tutorial erfahren Sie, wie Sie eine ASP.Net Core MVC-Anwendung erstellen und diese in Visual Studio Code bereitstellen.

## <a name="set-up"></a>Einrichten

- Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.
- Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download).
- Installieren Sie [Visual Studio Code](https://code.visualstudio.com/Download).
  - Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code.
  - Installieren Sie die [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code und konfigurieren Sie diese, bevor Sie fortfahren.

## <a name="create-an-aspnet-core-mvc-project"></a>Erstellen eines ASP.NET Core MVC-Projekts

Navigieren Sie bei Verwendung eines Terminals zu dem Ordner, in dem Sie das Projekt erstellen wollen, und verwenden Sie den folgenden Befehl:

```dotnetcli
dotnet new mvc
```

Es wird Ihnen eine Ordnerstruktur wie die folgende angezeigt:

```cmd
      appsettings.Development.json
      appsettings.json
<DIR> Controllers
<DIR> Models
      netcore-vscode.csproj
<DIR> obj
      Program.cs
<DIR> Properties
      Startup.cs
<DIR> Views
<DIR> wwwroot
```

## <a name="open-it-with-visual-studio-code"></a>Öffnen des Projekts mit Visual Studio Code

Nachdem das Projekt erstellt wurde, können Sie es mit Visual Studio Code öffnen, indem Sie eine der folgenden Optionen verwenden:

### <a name="through-the-command-line"></a>Über die Befehlszeile

Verwenden Sie in dem Ordner, in dem das Projekt erstellt wurde, den folgenden Befehl:

```cmd
> code .
```

Wenn der oben angegebene Befehl nicht funktioniert, überprüfen Sie, ob Ihre Installation ordnungsgemäß konfiguriert ist, indem Sie auf [diesen Link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform) verweisen.

### <a name="through-visual-studio-code-interface"></a>Über die Visual Studio Code-Schnittstelle

- Öffnen Sie Visual Studio Code.
- Wählen Sie im Menü `File > Open Folder` aus.
- Wählen Sie den Stamm des Ordners aus, in dem das MVC-Projekt erstellt wurde.

Wenn Sie den Projektordner öffnen, wird eine Meldung angezeigt, dass erforderliche Ressourcen zum Erstellen und Debuggen nicht vorhanden sind. Akzeptieren Sie die Hilfe, um sie hinzuzufügen.

![Visual Studio Code-Schnittstelle mit geladenem Projekt](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

Ein `.vscode`-Ordner wird in der Projektstruktur erstellt. Er enthält die folgenden Dateien:

```cmd
launch.json
tasks.json
```

Hierbei handelt es sich um Hilfsprogrammdateien zum Erstellen und Debuggen Ihrer .NET Core-Web-App.

## <a name="run-the-app"></a>Ausführen der App

Bevor Sie die App in Azure bereitstellen, sollten Sie sicherstellen, dass sie auf Ihrem lokalen Computer ordnungsgemäß ausgeführt wird.

- Drücken Sie F5, um das Projekt auszuführen.

Ihre Web-App wird in einem neuen Tab Ihres Standardbrowsers ausgeführt. Möglicherweise wird beim Starten eine Datenschutzwarnung angezeigt. Dies liegt daran, dass Ihre App zum Starten entweder HTTP oder HTTPs verwendet, und sie standardmäßig zum HTTPS-Endpunkt navigiert.

![Datenschutzwarnung beim lokalen Debuggen der App](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

Klicken Sie auf `Advanced` und dann auf `Continue to localhost (unsafe)`, um die Debugsitzung weiter auszuführen.

## <a name="generate-the-deployment-package-locally"></a>Bereitstellungspaket lokal generieren

- Öffnen Sie das Visual Studio Code-Terminal.
- Verwenden Sie den folgenden Befehl zum Generieren eines `Release`-Pakets in einem Unterordner namens `publish`:
  - `dotnet publish -c Release -o ./publish`
- Ein neuer `publish`-Ordner wird in der Projektstruktur erstellt.

![Struktur des „publish“-Ordners](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a>Veröffentlichen in Azure App Service

Nutzen Sie die Azure App Service-Erweiterung für Visual Studio Code, und führen Sie die unten aufgeführten Schritte aus, um die Website direkt in Azure App Service zu veröffentlichen.

### <a name="if-youre-creating-a-new-web-app"></a>Wenn Sie eine neue Web-App erstellen

- Klicken Sie mit der rechten Maustaste auf den `publish`-Ordner, und wählen Sie `Deploy to Web App...` aus.
- Wählen Sie das Abonnement aus, das Sie zum Erstellen der Web-App verwenden möchten.
- Klicken Sie auf `Create New Web App`.
- Geben Sie einen Namen für die Web-App ein.

Mit dieser Erweiterung wird die neue Web-App erstellt, und das Paket für die App wird automatisch bereitgestellt. Nachdem die Bereitstellung abgeschlossen ist, klicken Sie auf `Browse Website`, um die Bereitstellung zu überprüfen.

![Meldung über erfolgreiche Bereitstellung](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

Durch Klicken auf `Browse Website` gelangen Sie über Ihren Standardbrowser zur App:

![Erfolgreich bereitgestellte neue Web-App](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a>Wenn Sie Inhalte in einer vorhandenen Web-App bereitstellen

- Klicken Sie mit der rechten Maustaste auf den `publish`-Ordner, und wählen Sie `Deploy to Web App...` aus.
- Wählen Sie das Abonnement aus, in dem sich die vorhandene Web-App befindet.
- Wählen Sie die Web-App aus der Liste aus.
- Sie werden von Visual Studio Code gefragt, ob die vorhandenen Inhalte überschrieben werden sollen. Klicken Sie zum Bestätigen auf `Deploy`.

Die Erweiterung stellt die aktualisierten Inhalte in der Web-App bereit. Sobald dieser Vorgang abgeschlossen ist, klicken Sie auf `Browse Website`, um die Bereitstellung zu überprüfen.

![Erfolgreich bereitgestellte Inhalte in vorhandener Web-App](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Ihrer ersten Azure DevOps-Pipeline](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Azure App Service](/azure/app-service/app-service-web-overview)
- [Azure-Ressourcengruppen](/azure/azure-resource-manager/resource-group-overview#resource-groups)
