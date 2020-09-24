---
title: Bereitstellen einer App für App Service – DevOps mit ASP.NET Core und Azure
author: CamSoper
description: Stellen Sie eine ASP.NET Core-App für Azure App Service bereit, der erste Schritt für DevOps mit ASP.NET Core und Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-azurecli
ms.date: 10/24/2018
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
uid: azure/devops/deploy-to-app-service
ms.openlocfilehash: e6d8b4bcbbbe909fde971a8c706287654fcc98ba
ms.sourcegitcommit: 62cc131969b2379f7a45c286a751e22d961dfbdb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847623"
---
# <a name="deploy-an-app-to-app-service"></a>Bereitstellen einer App für App Service

[Azure App Service](/azure/app-service/) ist die Webhostingplattform von Azure. Das Bereitstellen einer Web-App für Azure App Service kann manuell oder über einen automatisierten Prozess erfolgen. In diesem Abschnitt des Leitfadens werden Bereitstellungsmethoden behandelt, die manuell oder per Skript über die Befehlszeile bzw. manuell mit Visual Studio ausgelöst werden können.

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Herunterladen und Erstellen der Beispiel-App
* Erstellen einer Azure App Service-Web-App mit der Azure Cloud Shell
* Bereitstellen der Beispiel-App in Azure mit Git
* Bereitstellen einer Änderung für die App mit Visual Studio
* Hinzufügen eines Stagingslots zur Web-App
* Bereitstellen eines Updates für den Stagingslot
* Vertauschen von Staging- und Produktionsslots

## <a name="download-and-test-the-app"></a>Herunterladen und Testen der App

Die in diesem Leitfaden verwendete App ist eine vorgefertigte ASP.NET Core-App, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/). Es handelt sich um eine Razor Pages-App, die die `Microsoft.SyndicationFeed.ReaderWriter`-API verwendet, um einen RSS-/Atom-Feed abzurufen und die Nachrichtenelemente in einer Liste anzuzeigen.

Sie können den Code nach Belieben überprüfen, aber es ist wichtig, zu verstehen, dass diese App keine Besonderheiten aufweist. Es handelt sich lediglich um eine einfache ASP.NET Core-App zur Veranschaulichung.

Laden Sie den Code über eine Befehlsshell herunter, erstellen Sie das Projekt und führen Sie es wie folgt aus.

> *Hinweis: Linux-/macOS-Benutzer sollten entsprechende Änderungen an den Pfaden vornehmen, z. B. einen Schrägstrich (`/`) anstatt eines umgekehrten Schrägstrichs (`\`) verwenden.*

1. Klonen Sie den Code in einen Ordner auf Ihrem lokalen Computer.

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. Ändern Sie Ihren Arbeitsordner in den *simple-feed-reader*-Ordner, der erstellt wurde.

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. Stellen Sie die Pakete wieder her, und erstellen Sie die Projektmappe.

    ```dotnetcli
    dotnet build
    ```

4. Führen Sie die App aus.

    ```dotnetcli
    dotnet run
    ```

    ![Der Befehl „dotnet run“ wurde erfolgreich ausgeführt.](./media/deploying-to-app-service/dotnet-run.png)

5. Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:5000`. Die App ermöglicht es Ihnen, eine Syndication-Feed-URL einzugeben oder einzufügen und eine Liste von Nachrichtenelementen anzuzeigen.

     ![Die App, die den Inhalt eines RSS-Feeds anzeigt.](./media/deploying-to-app-service/app-in-browser.png)

6. Wenn Sie sich vergewissert haben, dass die Anwendung ordnungsgemäß funktioniert, beenden Sie sie durch Drücken von **STRG** + **C** in der Befehlsshell.

## <a name="create-the-azure-app-service-web-app"></a>Erstellen der Azure App Service-Web-App

Zum Bereitstellen der App müssen Sie eine App Service-[Web-App](/azure/app-service/app-service-web-overview) erstellen. Nachdem Sie die Web-App erstellt haben, können Sie mit Git von Ihrem lokalen Computer aus für sie bereitstellen.

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com/bash) an. Hinweis: Wenn Sie sich zum ersten Mal anmelden, fordert Cloud Shell Sie auf, ein Speicherkonto für Konfigurationsdateien zu erstellen. Akzeptieren Sie die Standardwerte, oder geben Sie einen eindeutigen Namen an.

2. Verwenden Sie Cloud Shell für die folgenden Schritte.

    a. Deklarieren Sie eine Variable, um den Namen Ihrer Web-App zu speichern. Der Name muss eindeutig sein, damit er in der Standard-URL verwendet werden kann. Die Verwendung der Bash-Funktion `$RANDOM` zum Erstellen des Namens sorgt für Eindeutigkeit und Ergebnisse im Format `webappname99999`.

    ```console
    webappname=mywebapp$RANDOM
    ```

    b. Erstellen Sie eine Ressourcengruppe. Ressourcengruppen bieten eine Möglichkeit, Azure-Ressourcen zu aggregieren und als Gruppe zu verwalten.

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    Der Befehl `az` ruft die [Azure-Befehlszeilenschnittstelle](/cli/azure/) auf. Die Befehlszeilenschnittstelle kann lokal ausgeführt werden, aber die Verwendung in Cloud Shell spart Zeit und Konfiguration.

    c. Erstellen Sie einen App Service-Plan im S1-Tarif. Ein App Service-Plan ist eine Gruppierung von Web-Apps, die denselben Tarif aufweisen. Der S1-Tarif ist nicht kostenlos, aber er ist für das Stagingslots-Feature erforderlich.

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    d. Erstellen Sie die Web-App-Ressource mit dem App Service-Plan in derselben Ressourcengruppe.

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    e. Legen Sie die Anmeldeinformationen für die Bereitstellung fest. Diese Anmeldeinformationen für die Bereitstellung gelten für alle Web-Apps in Ihrem Abonnement. Verwenden Sie keine Sonderzeichen im Benutzernamen.

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    f. Konfigurieren Sie die Web-App so, dass sie Bereitstellungen vom lokalen Git akzeptiert und die *URL für die Git-Bereitstellung* anzeigt. **Notieren Sie diese URL zur späteren Bezugnahme**.

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    g. Zeigen Sie die *URL der Web-App* an. Navigieren Sie zu dieser URL, um die leere Web-App anzuzeigen. **Notieren Sie diese URL zur späteren Bezugnahme**.

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. Navigieren Sie über eine Befehlsshell auf Ihrem lokalen Computer zum Projektordner der Web-App (z B. `.\simple-feed-reader\SimpleFeedReader`). Führen Sie die folgenden Befehle aus, um Git so einzurichten, dass es einen Push für die Bereitstellungs-URL ausführt:

    a. Fügen Sie die Remote-URL zum lokalen Repository hinzu.

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    b. Pushen Sie den lokalen *Masterbranch* zum Remote-*Masterbranch* von *azure-prod*.

    ```console
    git push azure-prod master
    ```

    Sie werden aufgefordert, die zuvor erstellten Anmeldeinformationen für die Bereitstellung einzugeben. Beobachten Sie die Ausgabe in der Befehlsshell. Azure erstellt die ASP.NET Core-App remote.

4. Navigieren Sie in einem Browser zu der *Web-App-URL* und beachten Sie, dass die App erstellt und bereitgestellt wurde. Weitere Änderungen können mit `git commit` für das lokale Git-Repository committet werden. Diese Änderungen werden mit dem vorhergehenden `git push`-Befehl nach Azure gepusht.

## <a name="deployment-with-visual-studio"></a>Bereitstellung mit Visual Studio

> *Hinweis: Dieser Abschnitt gilt nur für Windows. Linux- und macOS-Benutzer sollten die unter Schritt 2 beschriebene Änderung vornehmen. Speichern Sie die Datei, und übertragen Sie die Änderung mit `git commit` in das lokale Repository. Pushen Sie abschließend die Änderung mit `git push`, wie im ersten Abschnitt.*

Die App wurde bereits von der Befehlsshell aus bereitgestellt. Lassen Sie uns die integrierten Tools von Visual Studio verwenden, um ein Update für die App bereitzustellen. Im Hintergrund erreicht Visual Studio dasselbe wie das Befehlszeilentool, jedoch innerhalb der vertrauten Benutzeroberfläche von Visual Studio.

1. Öffnen Sie *SimpleFeedReader.sln* in Visual Studio.
2. Öffnen Sie *Pages\Index.cshtml* im Projektmappen-Explorer. Ändern Sie `<h2>Simple Feed Reader</h2>` in `<h2>Simple Feed Reader - V2</h2>`.
3. Drücken Sie **STRG** + **UMSCHALT** + **B**, um die App zu erstellen.
4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

    ![Screenshot mit Rechtsklick und anschließendem Veröffentlichen](./media/deploying-to-app-service/publish.png)
5. Visual Studio kann eine neue App Service-Ressource erstellen, aber dieses Update wird über die bestehende Bereitstellung veröffentlicht. Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** aus der linken Liste und dann die Option **Vorhandene auswählen** aus. Klicken Sie auf **Veröffentlichen**.
6. Bestätigen Sie im Dialogfeld **App Service**, dass das Microsoft- oder Organisationskonto, das zum Erstellen Ihres Azure-Abonnements verwendet wurde, oben rechts angezeigt wird. Wenn dies nicht der Fall ist, klicken Sie auf die Dropdownliste und fügen Sie es hinzu.
7. Bestätigen Sie, dass das richtige Azure-**Abonnement** ausgewählt ist. Wählen Sie für **Ansicht** die Option **Ressourcengruppe** aus. Erweitern Sie die Ressourcengruppe **AzureTutorial**, und wählen Sie dann die vorhandene Web-App aus. Klicken Sie auf **OK**.

    ![Screenshot mit Dialogfeld zum Veröffentlichen von App Service](./media/deploying-to-app-service/publish-dialog.png)

Visual Studio erstellt die App für Azure und stellt sie bereit. Navigieren Sie zur URL der Web-App. Überprüfen Sie, ob die Änderung des `<h2>`-Elements aktiv ist.

![Die App mit dem geänderten Titel.](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a>Bereitstellungsslots

Bereitstellungsslots unterstützen das Staging von Änderungen, ohne die in der Produktion ausgeführte App zu beeinträchtigen. Sobald die bereitgestellte Version der App von einem Qualitätssicherungsteam überprüft wurde, können die Produktions- und Stagingslots ausgetauscht werden. Die im Staging befindliche App wird auf diese Weise in die Produktionsumgebung heraufgestuft. In den folgenden Schritten wird ein Stagingslot erstellt, dann werden einige Änderungen daran vorgenommen und der Stagingslot nach der Überprüfung mit der Produktionsumgebung ausgetauscht.

1. Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com/bash) an, wenn Sie nicht bereits angemeldet sind.
2. Erstellen Sie den Stagingslot.

    a. Erstellen Sie einen Bereitstellungsslot mit dem Namen *Staging*.

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    b. Konfigurieren Sie den Stagingslot so, dass die Bereitstellung aus dem lokalen Git verwendet wird, und rufen Sie die Bereitstellungs-URL für das **Staging** ab. **Notieren Sie diese URL zur späteren Bezugnahme**.

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    c. Zeigen Sie die URL des Stagingslots an. Navigieren Sie zu der URL, um den leeren Stagingslot anzuzeigen. **Notieren Sie diese URL zur späteren Bezugnahme**.

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. Ändern Sie in einem Texteditor oder in Visual Studio erneut *Pages/Index.cshtml*, sodass das Element `<h2>` den Wert `<h2>Simple Feed Reader - V3</h2>` aufweist, und speichern Sie dann die Datei.

4. Committen Sie die Datei an das lokale Git-Repository, indem Sie entweder die Seite **Änderungen** auf der Registerkarte *Team Explorer* von Visual Studio verwenden, oder indem Sie Folgendes über die Befehlsshell des lokalen Computers eingeben:

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. Fügen Sie über die Befehlsshell des lokalen Computers die URL der Stagingbereitstellung als Git-Remote ein, und pushen Sie die committeten Änderungen:

    a. Fügen Sie die Remote-URL für das Staging zum lokalen Git-Repository hinzu.

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    b. Pushen Sie den lokalen *Masterbranch* zum Remote-*Masterbranch* von *azure-staging*.

    ```console
    git push azure-staging master
    ```

    Warten Sie, während Azure die App erstellt und bereitstellt.

6. Öffnen Sie zwei Browserfenster, um zu überprüfen, ob V3 für den Stagingslot bereitgestellt wurde. Navigieren Sie in einem Fenster zur ursprünglichen URL der Web-App. Navigieren Sie im anderen Fenster zur URL der Staging-Web-App. Die URL der Produktionsumgebung liefert V2 der App. Die URL der Stagingumgebung liefert V3 der App.

    ![Screenshot zum Vergleich der Browserfenster](./media/deploying-to-app-service/ready-to-swap.png)

7. Tauschen Sie in der Cloud Shell den überprüften/vorbereiteten Stagingslot mit der Produktionsumgebung.

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. Überprüfen Sie, ob der Austausch erfolgt ist, indem Sie die beiden Browserfenster aktualisieren.

    ![Vergleichen der Browserfenster nach dem Austausch](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a>Zusammenfassung

In diesem Abschnitt wurden die folgenden Aufgaben ausgeführt:

* Die Beispiel-App wurde heruntergeladen und erstellt.
* Eine Azure App Service-Web-App wurde mit der Azure Cloud Shell erstellt.
* Die Beispiel-App wurde mit Git in Azure bereitgestellt.
* Eine Änderung für die App wurde mit Visual Studio bereitgestellt.
* Ein Stagingslot wurde zur Web-App hinzugefügt.
* Es wurde eine Aktualisierung des Stagingslots bereitgestellt.
* Die Staging- und Produktionsslots wurden ausgetauscht.

Im nächsten Abschnitt erfahren Sie, wie Sie eine DevOps-Pipeline mit Azure Pipelines erstellen können.

## <a name="additional-reading"></a>Weiterführende Literatur

* [Web-Apps – Übersicht](/azure/app-service/app-service-web-overview)
* [Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [Konfigurieren von Anmeldeinformationen für die Azure App Service-Bereitstellung](/azure/app-service/app-service-deployment-credentials)
* [Einrichten von Stagingumgebungen in Azure App Service](/azure/app-service/web-sites-staged-publishing)
