---
title: 'Continuous Integration und Continuous Deployment: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Continuous Integration und Continuous Deployment in DevOps mit ASP.NET Core und Azure
ms.author: scaddie
ms.date: 10/24/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/cicd
ms.openlocfilehash: 2ac7a130d223b21330d0a797c1d460fc0cf467d7
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "96901209"
---
# <a name="continuous-integration-and-deployment"></a>Continuous Integration und Continuous Deployment

Im vorherigen Kapitel haben Sie ein lokales Git-Repository für die Simple Feed Reader-App erstellt. In diesem Kapitel veröffentlichen Sie diesen Code in einem GitHub-Repository und erstellen mithilfe von Azure Pipelines eine Azure DevOps Services-Pipeline. Die Pipeline ermöglicht fortlaufende Builds und Bereitstellungen der App. Jeder Commit für das GitHub-Repository löst einen Build und eine Bereitstellung im Stagingslot der Azure-Web-App aus.

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Veröffentlichen des App-Codes auf GitHub
* Trennen der Verbindung mit lokaler Git-Bereitstellung
* Erstellen einer Azure DevOps-Organisation
* Erstellen eines Teamprojekts in Azure DevOps Services
* Erstellen einer Builddefinition
* Erstellen einer Releasepipeline
* Ausführen eines Commits für Änderungen an GitHub und automatisches Bereitstellen in Azure
* Überprüfen der Azure Pipelines-Pipeline

## <a name="publish-the-apps-code-to-github"></a>Veröffentlichen des App-Codes auf GitHub

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com`.
1. Klicken Sie in der Kopfzeile auf das Dropdownmenü **+** und dann auf **Neues Repository**:

    ![GitHub-Option „Neues Repository“](media/cicd/github-new-repo.png)

1. Klicken Sie im Dropdownmenü **Besitzer** auf Ihr Konto, und geben Sie *simple-feed-reader* in das Textfeld **Repositoryname** ein.
1. Klicken Sie auf die Schaltfläche **Repository erstellen**.
1. Öffnen Sie die Befehlsshell Ihres lokalen Computers. Navigieren Sie zu dem Verzeichnis, in dem das Git-Repository *simple-feed-reader* gespeichert ist.
1. Benennen Sie das vorhandene *origin*-Remoteelement in *upstream* um. Führen Sie den folgenden Befehl aus:

    ```console
    git remote rename origin upstream
    ```

1. Fügen Sie ein neues *origin*-Remoteelement hinzu, das auf die Kopie des Repositorys auf GitHub zeigt. Führen Sie den folgenden Befehl aus:

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. Veröffentlichen Sie Ihr lokales Git-Repository im neu erstellten GitHub-Repository. Führen Sie den folgenden Befehl aus:

    ```console
    git push -u origin master
    ```

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com/<GitHub_username>/simple-feed-reader/`. Überprüfen Sie, ob Ihr Code im GitHub-Repository angezeigt wird.

## <a name="disconnect-local-git-deployment"></a>Trennen der Verbindung mit lokaler Git-Bereitstellung

Entfernen Sie die lokale Git-Bereitstellung mit den folgenden Schritten. Azure Pipelines (ein Azure DevOps-Dienst) ersetzt diese Funktionalität und erweitert sie.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zur Web-App *staging (mywebapp\<unique_number\>/staging)* . Die Web-App kann schnell gefunden werden, indem Sie im Suchfeld des Portals *staging* eingeben:

    ![Suchbegriff „staging“ in der Web-App](media/cicd/portal-search-box.png)

1. Klicken Sie auf **Bereitstellungscenter**. Ein neues Panel wird angezeigt. Klicken Sie auf **Verbindung trennen**, um die lokale Konfiguration der Git-Quellcodeverwaltung zu entfernen, die im vorherigen Kapitel hinzugefügt wurde. Bestätigen Sie den Entfernungsvorgang, indem Sie auf die Schaltfläche **Ja** klicken.
1. Navigieren Sie zum App Service *mywebapp<unique_number>* . Zur Erinnerung: Das Suchfeld des Portals kann verwendet werden, um App Service schnell zu finden.
1. Klicken Sie auf **Bereitstellungscenter**. Ein neues Panel wird angezeigt. Klicken Sie auf **Verbindung trennen**, um die lokale Konfiguration der Git-Quellcodeverwaltung zu entfernen, die im vorherigen Kapitel hinzugefügt wurde. Bestätigen Sie den Entfernungsvorgang, indem Sie auf die Schaltfläche **Ja** klicken.

## <a name="create-an-azure-devops-organization"></a>Erstellen einer Azure DevOps-Organisation

1. Öffnen Sie einen Browser, und navigieren Sie zur Seite für das [Erstellen einer Azure DevOps-Organisation](https://go.microsoft.com/fwlink/?LinkId=307137).
1. Geben Sie einen eindeutigen Namen in das Textfeld **Wählen Sie einen einprägsamen Namen.** ein, um die URL für den Zugriff auf Ihre Azure DevOps-Organisation zu bilden.
1. Aktivieren Sie das Optionsfeld **Git**, da der Code in einem GitHub-Repository gehostet wird.
1. Klicken Sie auf die Schaltfläche **Continue** (Weiter). Nach einer kurzen Wartezeit werden ein Konto und ein Teamprojekt mit dem Namen *MyFirstProject* erstellt.

    ![Seite zum Erstellen der Azure DevOps-Organisation](media/cicd/vsts-account-creation.png)

1. Öffnen Sie die Bestätigungs-E-Mail, die anzeigt, dass die Azure DevOps-Organisation und das Projekt verwendet werden können. Klicken Sie auf die Schaltfläche **Starten Sie Ihr Projekt**:

    ![Schaltfläche „Starten Sie Ihr Projekt“](media/cicd/vsts-start-project.png)

1. In einem Browser wird *\<account_name\>.visualstudio.com* geöffnet. Klicken Sie auf den Link *MyFirstProject*, um mit der Konfiguration der DevOps-Pipeline des Projekts zu beginnen.

## <a name="configure-the-azure-pipelines-pipeline"></a>Konfigurieren der Azure Pipelines-Pipeline

Für den Abschluss des Vorgangs sind drei Schritte erforderlich. Durch die Ausführung der Schritte in den folgenden drei Abschnitten wird eine funktionsfähige DevOps-Pipeline erstellt.

### <a name="grant-azure-devops-access-to-the-github-repository"></a>Gewähren des Zugriffs auf das GitHub-Repository für Azure DevOps

1. Erweitern **oder erstellen Sie den Code über den Accordion-Bereich eines externen Repositorys**. Klicken Sie auf die Schaltfläche **Build einrichten**:

    ![Schaltfläche „Build einrichten“](media/cicd/vsts-setup-build.png)

1. Klicken Sie im Abschnitt **Quelle auswählen** auf die Option **GitHub**:

    ![Auswählen einer Quelle (GitHub)](media/cicd/vsts-select-source.png)

1. Die Autorisierung ist erforderlich, bevor Azure DevOps auf Ihr GitHub-Repository zugreifen kann. Geben Sie im Textfeld **Verbindungsname** *<GitHub_username> GitHub connection* ein. Zum Beispiel:

    ![GitHub-Verbindungsname](media/cicd/vsts-repo-authz.png)

1. Wenn für Ihr GitHub-Konto die zweistufige Authentifizierung aktiviert ist, ist ein persönliches Zugriffstoken erforderlich. Klicken Sie in diesem Fall auf den Link **Verwenden Sie zum Autorisieren das persönliche GitHub-Zugriffstoken.** Wenn Sie Hilfe benötigen, finden Sie weitere Informationen in den [offiziellen GitHub-Anweisungen für das Erstellen von persönlichen Zugriffstokens](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/). Nur der *repo*-Geltungsbereich wird benötigt. Klicken Sie andernfalls auf die Schaltfläche **Mithilfe von OAuth autorisieren**.
1. Melden Sie sich bei Ihrem GitHub-Konto an, wenn Sie dazu aufgefordert werden. Klicken Sie dann auf „Autorisieren“, um Zugriff auf Ihre Azure DevOps-Organisation zu gewähren. Bei erfolgreicher Ausführung wird ein neuer Dienstendpunkt erstellt.
1. Klicken Sie neben der Schaltfläche **Repository** auf die Auslassungspunkte (...). Wählen Sie das Repository *<GitHub_username>/simple-feed-reader* aus der Liste aus. Klicken Sie auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dropdownmenü **Standardbranch für manuelle und geplante Builds** auf den Standardbranch (*master*). Klicken Sie auf die Schaltfläche **Continue** (Weiter). Die Seite für die Vorlagenauswahl wird angezeigt.

### <a name="create-the-build-definition"></a>Erstellen der Builddefinition

1. Geben Sie auf der Seite für die Vorlagenauswahl *ASP.NET Core* in das Suchfeld ein:

    ![Suche nach ASP.NET Core auf der Vorlagenseite](media/cicd/vsts-template-selection.png)

1. Die Suchergebnisse für die Vorlage werden angezeigt. Zeigen Sie mit der Maustaste auf die Vorlage **ASP.NET Core**, und klicken Sie auf die Schaltfläche **Anwenden**.
1. Die Registerkarte **Aufgaben** der Builddefinition wird angezeigt. Klicken Sie auf die Registerkarte **Trigger**.
1. Aktivieren Sie das Kontrollkästchen **Continuous Integration aktivieren**. Vergewissern Sie sich, dass im Abschnitt **Branchfilter** das Dropdownmenü **Typ** auf *Einschließen* festgelegt ist. Legen Sie das Dropdownmenü **Branchspezifikation** auf *master* fest.

    ![Aktivieren der Continuous Integration-Einstellungen](media/cicd/vsts-enable-ci.png)

    Diese Einstellungen bewirken die Auslösung eines Builds, wenn eine Änderung an den Standardbranch (*master*) des GitHub-Repositorys gepusht wird. Continuous Integration wird im Abschnitt [Committen von Änderungen auf GitHub und automatisches Bereitstellen in Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) getestet.

1. Klicken Sie auf die Schaltfläche **Speichern und in Warteschlange einreihen** und dann auf die Option **Speichern**:

    ![Schaltfläche „Speichern“](media/cicd/vsts-save-build.png)

1. Das folgende modale Dialogfeld wird angezeigt:

    ![Speichern der Builddefinition: modales Dialogfeld](media/cicd/vsts-save-modal.png)

    Verwenden Sie den Standardordner von *\\* , und klicken Sie auf die Schaltfläche **Speichern**.

### <a name="create-the-release-pipeline"></a>Erstellen der Releasepipeline

1. Klicken Sie auf die Registerkarte **Releases** Ihres Teamprojekts. Klicken Sie auf die Schaltfläche **Neue Pipeline**.

    ![Registerkarte „Releases“: Schaltfläche für neue Definition](media/cicd/vsts-new-release-definition.png)

    Der Bereich für die Vorlagenauswahl wird angezeigt.

1. Geben Sie auf der Seite für die Vorlagenauswahl *App Service* in das Suchfeld ein:

    ![Releasepipeline: Suchfeld für Vorlage](media/cicd/vsts-release-template-search.png)

1. Die Suchergebnisse für die Vorlage werden angezeigt. Bewegen Sie den Mauszeiger über die Vorlage **Azure App Service-Bereitstellung mit Slot**, und klicken Sie auf die Schaltfläche **Anwenden**. Die Registerkarte **Pipeline** der Releasepipeline wird angezeigt.

    ![Registerkarte „Pipeline“ der Releasepipeline](media/cicd/vsts-release-definition-pipeline.png)

1. Klicken Sie im Feld **Artefakte** auf die Schaltfläche **Hinzufügen**. Das Panel **Artefakt hinzufügen** wird angezeigt:

    ![Releasepipeline: Panel „Artefakt hinzufügen“](media/cicd/vsts-release-add-artifact.png)

1. Klicken Sie im Abschnitt **Quelltyp** auf die Kachel **Build**. Dieser Typ ermöglicht das Verknüpfen der Releasepipeline mit der Builddefinition.
1. Klicken Sie im Dropdownmenü **Projekt** auf *MyFirstProject*.
1. Klicken sie im Dropdownmenü **Source (Build definition)** (Quelle (Builddefinition)) auf den Builddefinitionsnamen *MyFirstProject-ASP.NET Core-CI*.
1. Wählen Sie im Dropdownmenü **Standardversion** die Option *Neueste* aus. Mit dieser Option werden die Artefakte erstellt, die von der neuesten Builddefinition erstellt wurden.
1. Ersetzen Sie den Text im Textfeld **Quellalias** durch *Ablegen*.
1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Der Abschnitt **Artefakte** wird aktualisiert, um die Änderungen anzuzeigen.
1. Durch Klicken auf das Blitzsymbol wird Continuous Deployment aktiviert:

    ![Releasepipeline: Blitzsymbol im Abschnitt „Artefakte“](media/cicd/vsts-artifacts-lightning-bolt.png)

    Wenn diese Option aktiviert ist, wird jedes Mal eine Bereitstellung durchgeführt, wenn ein neuer Build verfügbar ist.
1. Im rechten Bereich wird das Panel **Continuous Deployment-Trigger** angezeigt. Klicken Sie auf die Umschaltfläche, um das Feature zu aktivieren. Es ist nicht erforderlich, den **Pull Request-Trigger** zu aktivieren.
1. Klicken Sie im Abschnitt **Build-Branchfilter** auf das Dropdownmenü **Hinzufügen**. Wählen Sie die Option **Build Definition's default branch** (Standardbranch der Builddefinition) aus. Dieser Filter bewirkt, dass das Release nur für einen Build aus dem Standardbranch (*master*) des GitHub-Repositorys ausgelöst wird.
1. Klicken Sie auf die Schaltfläche **Speichern**. Klicken Sie im angezeigten modalen Dialogfeld **Speichern** auf die Schaltfläche **OK**.
1. Klicken Sie auf das Feld **Umgebung 1**. Auf der rechten Seite wird das Panel **Umgebung** angezeigt. Ändern Sie den Text *Umgebung 1* im Textfeld **Umgebungsname** in *Produktion*.

   ![Releasepipeline: Textfeld für Umgebungsname](media/cicd/vsts-environment-name-textbox.png)

1. Klicken Sie im Textfeld **Produktion** auf den Link **1 phase, 2 tasks** (1 Phase, 2 Aufgaben):

    ![Releasepipeline: Produktionsumgebungslink.png](media/cicd/vsts-production-link.png)

    Die Registerkarte **Aufgaben** der Umgebung wird angezeigt.
1. Klicken Sie auf die Registerkarte **Deploy Azure App Service to Slot** (Azure App Service in Slot bereitstellen). Die Einstellungen werden in einem Panel auf der rechten Seite angezeigt.
1. Wählen Sie das Azure-Abonnement aus, das App Service im Dropdownmenü **Azure-Abonnement** zugeordnet ist. Klicken Sie nach der Auswahl auf die Schaltfläche **Autorisieren**.
1. Klicken Sie im Dropdownmenü **App-Typ** auf die Option *Web-App*.
1. Klicken Sie im Dropdownmenü **App Service-Name** auf die Option *mywebapp/<unique_number/>* .
1. Klicken Sie im Dropdownmenü **Ressourcengruppe** auf die Option *AzureTutorial*.
1. Klicken Sie im Dropdownmenü *Slot* auf die Option **Staging**.
1. Klicken Sie auf die Schaltfläche **Speichern**.
1. Zeigen Sie auf den Namen der standardmäßigen Releasepipeline. Klicken Sie auf das Stiftsymbol, um diese zu bearbeiten. Verwenden Sie *MyFirstProject-ASP.NET Core-CD* als Namen.

    ![Releasepipelinename](media/cicd/vsts-release-definition-name.png)

1. Klicken Sie auf die Schaltfläche **Speichern**.

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a>Ausführen eines Commits für Änderungen an GitHub und automatisches Bereitstellen in Azure

1. Öffnen Sie *SimpleFeedReader.sln* in Visual Studio.
1. Öffnen Sie *Pages\Index.cshtml* im Projektmappen-Explorer. Ändern Sie `<h2>Simple Feed Reader - V3</h2>` in `<h2>Simple Feed Reader - V4</h2>`.
1. Drücken Sie **STRG**+**UMSCHALT**+**B**, um die App zu erstellen.
1. Committen Sie die Datei in das GitHub-Repository. Verwenden Sie entweder die Seite **Änderungen** in der Registerkarte *Team Explorer* von Visual Studio, oder führen Sie mithilfe der Befehlsshell des lokalen Computers Folgendes aus:

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. Pushen Sie die Änderung im Standardbranch (*master*) an das *origin*-Remoterepository Ihres GitHub-Repositorys. Ersetzen Sie im folgenden Befehl den Platzhalter `{BRANCH}` durch den Standardbranch (verwenden Sie `master`):

    ```console
    git push origin {BRANCH}
    ```

    Der Commit wird im Standardbranch (*master*) des GitHub-Repositorys angezeigt:

    ![GitHub-Commit im Standardbranch („master“)](media/cicd/github-commit.png)

    Der Build wird ausgelöst, da Continuous Integration in der Registerkarte **Trigger** der Builddefinition aktiviert ist:

    ![Aktivieren von Continuous Integration](media/cicd/enable-ci.png)

1. Navigieren Sie in Azure DevOps Services auf der Seite **Azure Pipelines** > **Builds** zur Registerkarte **In Warteschlange eingereiht** Der Build in der Warteschlange zeigt den Branch und den Commit, die den Build ausgelöst haben:

    ![Build in Warteschlange](media/cicd/build-queued.png)

1. Sobald der Buildvorgang erfolgreich ist, erfolgt eine Bereitstellung in Azure. Navigieren Sie im Browser zur App. Beachten Sie, dass der Text „V4“ in der Überschrift angezeigt wird:

    ![Aktualisierte App](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a>Überprüfen der Azure Pipelines-Pipeline

### <a name="build-definition"></a>Builddefinition

Eine Builddefinition mit dem Namen *MyFirstProject-ASP.NET Core-CI* wurde erstellt. Nach Abschluss erstellt der Build eine *ZIP*-Datei einschließlich der zu veröffentlichenden Ressourcen. Die Releasepipeline stellt diese Ressourcen in Azure bereit.

Auf der Registerkarte **Aufgaben** der Builddefinition werden die einzelnen Schritte aufgelistet, die verwendet werden. Es gibt fünf Buildaufgaben.

![Builddefinitionsaufgaben](media/cicd/build-definition-tasks.png)

1. **Wiederherstellen** führt den Befehl `dotnet restore` aus, um die NuGet-Pakete der App wiederherzustellen. Der verwendete Standardpaketfeed ist nuget.org.
1. **Build** führt den Befehl `dotnet build --configuration release` aus, um den App-Code zu kompilieren. Diese `--configuration`-Option wird verwendet, um eine optimierte Version des Codes zu erstellen, die für die Bereitstellung in einer Produktionsumgebung geeignet ist. Ändern Sie die Variable *BuildConfiguration* auf der Registerkarte **Variablen** der Builddefinition, wenn beispielsweise eine Debugkonfiguration erforderlich ist.
1. **Testen** führt den `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>`-Befehl aus, um die Komponententests der App auszuführen. Komponententests werden in jedem C#-Projekt ausgeführt, das mit dem `**/*Tests/*.csproj`-Globmuster übereinstimmt. Testergebnisse werden in einer *TRX*-Datei an dem Speicherort gespeichert, der durch die Option `--results-directory` angegeben wird. Wenn ein Test fehlschlägt, schlägt der Build fehl und wird nicht bereitgestellt.

    > [!NOTE]
    > Ändern Sie *SimpleFeedReader.Tests\Services\NewsServiceTests.cs*, um einen der Tests absichtlich zu unterbrechen, um zu überprüfen, ob die Komponententests funktionieren. Ändern Sie beispielsweise in der `Returns_News_Stories_Given_Valid_Uri`-Methode `Assert.True(result.Count > 0);` in `Assert.False(result.Count > 0);` . Committen Sie die Änderung, und übertragen Sie diese mithilfe von Push an GitHub. Der Build wird ausgelöst und schlägt fehl. Der Status der Buildpipeline wird in **failed** (Fehlgeschlagen) geändert. Machen Sie die Änderung rückgängig, committen Sie noch mal, und übertragen Sie sie erneut mithilfe von Push. Der Build ist erfolgreich.

1. **Veröffentlichen** führt den `dotnet publish --configuration release --output <local_path_on_build_agent>`-Befehl aus, um eine *ZIP*-Datei mit den bereitzustellenden Artefakten zu erstellen. Die Option `--output` gibt den Veröffentlichungsort der *ZIP*-Datei an. Dieser Speicherort wird durch Übergeben einer [vordefinierten Variablen](/azure/devops/pipelines/build/variables) namens `$(build.artifactstagingdirectory)` angegeben. Diese Variable wird zu einem lokalen Pfad wie beispielsweise *c:\agent\_work\1\a* auf dem Build-Agent erweitert.
1. **Artefakt veröffentlichen** veröffentlicht die *ZIP*-Datei, die von der Aufgabe **Veröffentlichen** erstellt wurde. Die Aufgabe akzeptiert den Speicherort der *ZIP*-Datei als Parameter, bei dem es sich um die vordefinierte Variable `$(build.artifactstagingdirectory)` handelt. Die *ZIP*-Datei wird als Ordner namens *drop* veröffentlicht.

Klicken Sie auf den Link **Zusammenfassung** der Builddefinition, um den Verlauf der Builds mit der folgenden Definition anzuzeigen:

![Screenshot mit dem Builddefinitionsverlauf](media/cicd/build-definition-summary.png)

Klicken Sie auf der dann angezeigten Seite auf den Link, der der eindeutigen Buildnummer entspricht:

![Screenshot mit der Zusammenfassungsseite der Builddefinition](media/cicd/build-definition-completed.png)

Eine Zusammenfassung dieses spezifischen Builds wird angezeigt. Klicken Sie auf die Registerkarte **Artefakte**, und beachten Sie, dass der vom Build erstellte Ordner *drop* aufgeführt wird:

![Screenshot mit den Builddefinitionsartefakten: „drop“-Ordner](media/cicd/build-definition-artifacts.png)

Verwenden Sie die Links **Download** und **Durchsuchen**, um die veröffentlichten Artefakte zu überprüfen.

### <a name="release-pipeline"></a>Releasepipeline

Eine Releasepipeline mit dem Namen *MyFirstProject-ASP.NET Core-CD* wurde erstellt:

![Screenshot mit der Übersicht über Releasepipelines](media/cicd/release-definition-overview.png)

Die beiden Hauptkomponenten der Releasepipeline sind die **Artefakte** und die **Umgebungen**. Wenn Sie im Bereich **Artefakte** auf das Feld klicken, wird das folgende Panel angezeigt:

![Screenshot mit den Releasepipelineartefakten](media/cicd/release-definition-artifacts.png)

Der Wert **Source (Build definition)** (Quelle (Builddefinition)) steht für die Builddefinition, mit der diese Releasepipeline verknüpft ist. Die *ZIP*-Datei, die bei erfolgreicher Ausführung der Builddefinition erstellt wurde, wird für die Umgebung *Produktion* für die Bereitstellung in Azure bereitgestellt. Klicken Sie im Feld für die Umgebung *Produktion* auf den Link *1 phase, 2 tasks* (1 Phase, 2 Aufgaben), um die Aufgaben der Releasepipeline anzuzeigen:

![Screenshot mit den Releasepipelineaufgaben](media/cicd/release-definition-tasks.png)

Die Releasepipeline besteht aus zwei Aufgaben: *Deploy Azure App Service to Slot* (Azure App Service in Slot bereitstellen) und *Manage Azure App Service - Slot Swap* (Azure App Service verwalten – Slotaustausch). Wenn Sie auf die erste Aufgabe klicken, wird die folgende Aufgabenkonfiguration angezeigt:

![Screenshot mit der Bereitstellungsaufgabe für die Releasepipeline](media/cicd/release-definition-task1.png)

Das Azure-Abonnement, der Diensttyp, der Name der Web-App, die Ressourcengruppe und der Bereitstellungsslot werden in der Bereitstellungsaufgabe definiert. Das Textfeld **Paket oder Ordner** enthält den Pfad der *ZIP*-Datei, die extrahiert und im *Stagingslot* der Web-App *mywebapp\<unique_number\>* bereitgestellt werden soll.

Wenn Sie auf die Aufgabe für den Slotaustausch klicken, wird die folgende Aufgabenkonfiguration angezeigt:

![Screenshot mit der Slotaustauschaufgabe für die Releasepipeline](media/cicd/release-definition-task2.png)

Das Abonnement, die Ressourcengruppe, der Diensttyp, der Name der Web-App und Details zum Bereitstellungsslot werden bereitgestellt. Das Kontrollkästchen **Mit Produktion tauschen** ist aktiviert. Folglich werden die Bits, die im *Stagingslot* bereitgestellt werden, in der Produktionsumgebung ausgetauscht.

## <a name="additional-reading"></a>Weiterführende Literatur

* [Erstellen Sie Ihre erste Pipeline mit Azure Pipelines](/azure/devops/pipelines/get-started-yaml)
* [Erstellen und Testen eines .NET Core-Projekts](/azure/devops/pipelines/languages/dotnet-core)
* [Bereitstellen einer Web-App mit Azure Pipelines](/azure/devops/pipelines/targets/webapp)
