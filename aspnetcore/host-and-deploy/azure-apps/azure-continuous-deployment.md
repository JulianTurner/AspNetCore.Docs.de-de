---
title: Continuous Deployment in Azure mit Visual Studio und Git mit ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
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
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053786"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a>Continuous Deployment in Azure mit Visual Studio und Git mit ASP.NET Core

Von [Erik Reitan](https://github.com/Erikre)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

Dieses Tutorial zeigt, wie mit Visual Studio eine ASP.NET Core-Web-App erstellt und aus Visual Studio mithilfe von Continuous Deployment in Azure App Service bereitgestellt wird.

Siehe auch [Erstellen einer ersten Pipeline mit Azure Pipelines](/azure/devops/pipelines/get-started-yaml). Diese Anleitung zeigt, wie Sie einen CD-Workflow (Continuous Delivery) für [Azure App Service](/azure/app-service/app-service-web-overview) mit Azure DevOps Services konfigurieren. Azure Pipelines (ein Azure DevOps Services-Dienst) vereinfacht das Einrichten einer robusten Bereitstellungspipeline zur Veröffentlichung von Updates für Apps, die in Azure App Service gehostet werden. Die Pipeline kann im Azure-Portal für die folgenden Aufgaben konfiguriert werden: Erstellen von Builds, Ausführen von Tests, Bereitstellen in einem Stagingslot und anschließendes Bereitstellen in der Produktion.

> [!NOTE]
> Für dieses Tutorial ist ein Microsoft Azure-Konto erforderlich. Zum Abrufen eines Kontos können Sie die [Leistungen für MSDN-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) oder sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial setzt voraus, dass folgende Software installiert ist:

* [Visual Studio](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* [Git](https://git-scm.com/downloads) für Windows

## <a name="create-an-aspnet-core-web-app"></a>Erstellen einer ASP.NET Core-Web-App

1. Starten Sie Visual Studio.

1. Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.

1. Wählen Sie die Projektvorlage **ASP.NET Core-Webanwendung** aus. Sie wird unter **Installierte** > **Vorlagen** > **Visual C#**  >  **.NET Core** angezeigt. Benennen Sie das Projekt mit `SampleWebAppDemo`. Wählen Sie die Option **Neues Git-Repository erstellen** aus, und klicken Sie auf **OK**.

   ![Dialogfeld "Neues Projekt"](azure-continuous-deployment/_static/01-new-project.png)

1. Wählen Sie im Dialogfeld **Neues ASP.NET Core-Projekt** die ASP.NET Core-Vorlage **Leer** aus, und klicken Sie dann auf **OK**.

   ![Dialogfeld „Neues ASP.NET Core-Projekt“](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> Das neueste Release von .NET Core ist 2.0.

### <a name="running-the-web-app-locally"></a>Lokales Ausführen der Web-App

1. Sobald Visual Studio das Erstellen der App abgeschlossen hat, führen Sie die App durch Auswählen von **Debuggen** > **Debuggen starten** aus. Drücken Sie alternativ die Taste **F5**.

   Das Initialisieren von Visual Studio und der neuen App dauert möglicherweise einen Moment. Nach Abschluss der Initialisierung wird im Browser die ausgeführte App angezeigt.

   ![Browserfenster mit der ausgeführten Anwendung, in der „Hello World!“ angezeigt wird](azure-continuous-deployment/_static/04-browser-runapp.png)

1. Schließen Sie nach der Überprüfung der ausgeführten Web-App den Browser, und klicken Sie in der Symbolleiste von Visual Studio auf das Symbol „Debuggen beenden“, um die App zu beenden.

## <a name="create-a-web-app-in-the-azure-portal"></a>Erstellen einer Web-App im Azure-Portal

Mit den folgenden Schritten wird eine Web-App im Azure-Portal erstellt:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie links oben auf der Portalschnittstelle auf die Option **NEU**.

1. Wählen Sie **Web + Mobil** > **Web-App** aus.

   ![Microsoft Azure-Portal: Schaltfläche „Neu“: „Web und mobil“ unter „Marketplace“: Schaltfläche „Web App“ unter „Ausgewählte Apps“](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. Geben Sie auf dem Blatt **Web-App** für **App Service-Name** einen eindeutigen Wert ein.

   ![Blatt „Web-App“](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > Der Name unter **App Service-Name** muss eindeutig sein. Wenn der Name angegeben ist, erzwingt das Portal diese Regel. Wenn ein anderer Wert eingegeben wird, müssen Sie diesen Wert bei jedem Vorkommen von **SampleWebAppDemo** in diesem Tutorial ersetzen.

   Wählen Sie außerdem auf dem Blatt **Web-App** einen vorhandenen **App Service-Plan/Standort** aus, oder erstellen Sie einen neuen. Wenn Sie einen neuen Plan erstellen, wählen Sie den Tarif, den Standort und andere Optionen aus. Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

1. Wählen Sie **Erstellen** aus. Azure stellt die Web-App bereit und startet sie.

   ![Azure-Portal: SampleWebAppDemo01, Blatt „Zusammenfassung“](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a>Aktivieren der Git-Veröffentlichung für die neue Web-App

Bei Git handelt es sich um ein verteiltes Versionskontrollsystem, mit dem Sie eine Azure App Service-Web-App bereitstellen können. Web-App-Code wird in einem lokalen Git-Repository gespeichert, und der Code wird durch Übertragung in ein Remoterepository in Azure bereitgestellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Klicken Sie auf **App-Dienste** , um eine Liste der App-Dienste anzuzeigen, die Ihrem Azure-Abonnement zugeordnet sind.

1. Wählen Sie die Web-App aus, die im vorherigen Abschnitt dieses Tutorials erstellt wurde.

1. Klicken Sie auf dem Blatt **Bereitstellung** auf **Bereitstellungsoptionen** > **Quelle auswählen** > **Lokales Git-Repository**.

   ![Blatt "Einstellungen": Blatt „Bereitstellungsquelle“: Quellblatt auswählen](azure-continuous-deployment/_static/deployment-options.png)

1. Klicken Sie auf **OK**.

1. Wenn zuvor keine Anmeldeinformationen für die Bereitstellung zum Veröffentlichen einer Web-App oder anderen App Service-App festgelegt wurden, richten Sie diese nun ein:

   * Wählen Sie **Einstellungen** > **Anmeldeinformationen für die Bereitstellung** aus. Das Blatt **Anmeldeinformationen für die Bereitstellung festlegen** wird angezeigt.
   * Erstellen Sie einen Benutzernamen und ein Kennwort. Speichern Sie das Kennwort zur späteren Verwendung beim Einrichten von Git.
   * Klicken Sie auf **Speichern**.

1. Wählen Sie auf dem Blatt **Web-App** den Eintrag **Einstellungen** > **Eigenschaften** aus. Die URL des Git-Remoterepositorys, in dem die Bereitstellung erfolgt, wird unter **GIT-URL** angezeigt.

1. Kopieren Sie den Wert **GIT-URL** zur späteren Verwendung in diesem Tutorial.

   ![Azure-Portal: Blatt „Eigenschaften“ der Anwendung](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a>Veröffentlichen der App in Azure App Service

In diesem Abschnitt erstellen Sie ein lokales Git-Repository mit Visual Studio und führen einen Pushvorgang aus diesem Repository in Azure aus, um die Web-App bereitzustellen. Folgende Schritte müssen ausgeführt werden:

* Fügen Sie die Einstellung des Remoterepositorys mithilfe des GIT-URL-Werts hinzu, damit das lokale Repository in Azure bereitgestellt werden kann.
* Führen Sie für die Projektänderungen einen Commit aus.
* Übertragen Sie die Projektänderungen mithilfe von Push aus Ihrem lokalen Repository in das Remoterepository in Azure.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Projektmappe „SampleWebAppDemo“** , und wählen Sie **Commit ausführen** aus. Der **Team Explorer** wird angezeigt.

   ![Team Explorer-Registerkarte „Verbinden“](azure-continuous-deployment/_static/10-team-explorer.png)

1. Klicken Sie in **Team Explorer** auf **Start** (Startsymbol) > **Einstellungen** > **Repositoryeinstellungen**.

1. Klicken Sie in **Repositoryeinstellungen** im Abschnitt **Remotes** auf **Hinzufügen**. Das Dialogfeld **Remote hinzufügen** wird angezeigt.

1. Legen Sie den **Namen** der Remoteinstanz auf **Azure SampleApp** fest.

1. Legen Sie den Wert für **Fetch** auf die **Git-URL** fest, die Sie zuvor in diesem Tutorial aus Azure kopiert haben. Beachten Sie, dass dies die URL ist, die mit **.git** endet.

   ![Dialogfeld „Remote bearbeiten“](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > Geben Sie alternativ über das **Befehlsfenster** das Remoterepository an, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und den Befehl eingeben. Beispiel:
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. Klicken Sie auf **Start** (Startsymbol) > **Einstellungen** > **Globale Einstellungen**. Überprüfen Sie, ob der Name und die E-Mail-Adresse festgelegt wurden. Wählen Sie ggf. **Aktualisieren** aus.

1. Klicken Sie auf **Start** > **Änderungen** , um zur Ansicht **Änderungen** zurückzukehren.

1. Geben Sie eine Commit-Nachricht ein, z.B. **Anfänglicher Push Nr. 1** , und klicken Sie auf **Commit ausführen**. Durch diese Aktion wird lokal ein *Commit* erstellt.

   ![Team Explorer-Registerkarte „Verbinden“](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > Führen Sie alternativ für Ihre Änderungen über das **Befehlsfenster** einen Commit aus, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und die Git-Befehle eingeben. Beispiel:
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. Klicken Sie auf **Start** > **Synchronisieren** > **Aktionen** > **Eingabeaufforderung öffnen**. Die Eingabeaufforderung wird im Projektverzeichnis geöffnet.

1. Geben Sie im Befehlsfenster folgenden Befehl ein:

   `git push -u Azure-SampleApp master`

1. Geben Sie das Kennwort für die Azure- **Anmeldeinformationen für die Bereitstellung** ein, das Sie zuvor in Azure erstellt haben.

   Dieser Befehl startet eine Übertragung der lokalen Projektdateien mithilfe von Push in Azure. Die Ausgabe des obigen Befehls endet mit der Meldung, dass die Bereitstellung erfolgreich war.

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > Wenn bei dem Projekt eine Zusammenarbeit erforderlich ist, sollten Sie in Erwägung ziehen, vor der Übertragung mithilfe von Push in Azure eine Übertragung mithilfe von Push in [GitHub](https://github.com) durchzuführen.
 
### <a name="verify-the-active-deployment"></a>Überprüfen der aktiven Bereitstellung

Überprüfen Sie, ob die Übertragung der Web-App über die lokale Umgebung in Azure erfolgreich war.

Wählen Sie die Web-App im [Azure-Portal](https://portal.azure.com) aus. Wählen Sie **Bereitstellung** > **Bereitstellungsoptionen** aus.

![Azure-Portal: Blatt "Einstellungen": Blatt „Bereitstellungen“ mit erfolgreicher Bereitstellung](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a>Ausführen der App in Azure

Führen Sie die App aus, nachdem die Web-App in Azure bereitgestellt wurde.

Dafür stehen zwei Methoden zur Verfügung:

* Suchen Sie im Azure-Portal das Blatt „Web-App“ für die Web-App. Wählen Sie **Durchsuchen** , um die App im Standardbrowser anzuzeigen.
* Öffnen Sie einen Browser, und geben Sie die URL für die Web-App ein. Ein Beispiel: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="update-the-web-app-and-republish"></a>Aktualisieren der Web-App und erneutes Veröffentlichen

Veröffentlichen Sie den lokalen Code erneut, nachdem Sie Änderungen daran vorgenommen haben:

1. Öffnen Sie in Visual Studio im **Projektmappen-Explorer** die Datei *Startup.cs*.

1. Ändern Sie in der `Configure`-Methode die `Response.WriteAsync`-Methode so, dass sie wie folgt angezeigt wird:

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. Speichern Sie die Änderungen in *Startup.cs*.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Projektmappe „SampleWebAppDemo“** , und wählen Sie **Commit ausführen** aus. Der **Team Explorer** wird angezeigt.

1. Geben Sie eine Commit-Nachricht ein, wie z.B. `Update #2`.

1. Klicken Sie auf die Schaltfläche **Commit ausführen** , um die Projektänderungen zu committen.

1. Klicken Sie auf **Start** > **Synchronisieren** > **Aktionen** > **Push**.

> [!NOTE]
> Als Alternative können Sie die Änderungen im **Befehlsfenster** mithilfe von Push übertragen, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und einen Git-Befehl eingeben. Beispiel:
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a>Anzeigen der aktualisierten Web-App in Azure

Zeigen Sie die aktualisierte Web-App an, indem Sie im Azure-Portal auf dem Blatt „Web-App“ **Durchsuchen** auswählen oder einen Browser öffnen und die URL der Web-App eingeben. Ein Beispiel: `http://SampleWebAppDemo.azurewebsites.net`

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erstellen Sie Ihre erste Pipeline mit Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
