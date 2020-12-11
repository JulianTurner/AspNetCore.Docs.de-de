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
ms.sourcegitcommit: 6af9016d1ffc2dffbb2454c7da29c880034cefcd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96901209"
---
# <a name="continuous-integration-and-deployment"></a><span data-ttu-id="dbc49-103">Continuous Integration und Continuous Deployment</span><span class="sxs-lookup"><span data-stu-id="dbc49-103">Continuous integration and deployment</span></span>

<span data-ttu-id="dbc49-104">Im vorherigen Kapitel haben Sie ein lokales Git-Repository für die Simple Feed Reader-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-104">In the previous chapter, you created a local Git repository for the Simple Feed Reader app.</span></span> <span data-ttu-id="dbc49-105">In diesem Kapitel veröffentlichen Sie diesen Code in einem GitHub-Repository und erstellen mithilfe von Azure Pipelines eine Azure DevOps Services-Pipeline.</span><span class="sxs-lookup"><span data-stu-id="dbc49-105">In this chapter, you'll publish that code to a GitHub repository and construct an Azure DevOps Services pipeline using Azure Pipelines.</span></span> <span data-ttu-id="dbc49-106">Die Pipeline ermöglicht fortlaufende Builds und Bereitstellungen der App.</span><span class="sxs-lookup"><span data-stu-id="dbc49-106">The pipeline enables continuous builds and deployments of the app.</span></span> <span data-ttu-id="dbc49-107">Jeder Commit für das GitHub-Repository löst einen Build und eine Bereitstellung im Stagingslot der Azure-Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="dbc49-107">Any commit to the GitHub repository triggers a build and a deployment to the Azure Web App's staging slot.</span></span>

<span data-ttu-id="dbc49-108">In diesem Abschnitt führen Sie die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="dbc49-108">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="dbc49-109">Veröffentlichen des App-Codes auf GitHub</span><span class="sxs-lookup"><span data-stu-id="dbc49-109">Publish the app's code to GitHub</span></span>
* <span data-ttu-id="dbc49-110">Trennen der Verbindung mit lokaler Git-Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="dbc49-110">Disconnect local Git deployment</span></span>
* <span data-ttu-id="dbc49-111">Erstellen einer Azure DevOps-Organisation</span><span class="sxs-lookup"><span data-stu-id="dbc49-111">Create an Azure DevOps organization</span></span>
* <span data-ttu-id="dbc49-112">Erstellen eines Teamprojekts in Azure DevOps Services</span><span class="sxs-lookup"><span data-stu-id="dbc49-112">Create a team project in Azure DevOps Services</span></span>
* <span data-ttu-id="dbc49-113">Erstellen einer Builddefinition</span><span class="sxs-lookup"><span data-stu-id="dbc49-113">Create a build definition</span></span>
* <span data-ttu-id="dbc49-114">Erstellen einer Releasepipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-114">Create a release pipeline</span></span>
* <span data-ttu-id="dbc49-115">Ausführen eines Commits für Änderungen an GitHub und automatisches Bereitstellen in Azure</span><span class="sxs-lookup"><span data-stu-id="dbc49-115">Commit changes to GitHub and automatically deploy to Azure</span></span>
* <span data-ttu-id="dbc49-116">Überprüfen der Azure Pipelines-Pipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-116">Examine the Azure Pipelines pipeline</span></span>

## <a name="publish-the-apps-code-to-github"></a><span data-ttu-id="dbc49-117">Veröffentlichen des App-Codes auf GitHub</span><span class="sxs-lookup"><span data-stu-id="dbc49-117">Publish the app's code to GitHub</span></span>

1. <span data-ttu-id="dbc49-118">Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com`.</span><span class="sxs-lookup"><span data-stu-id="dbc49-118">Open a browser window, and navigate to `https://github.com`.</span></span>
1. <span data-ttu-id="dbc49-119">Klicken Sie in der Kopfzeile auf das Dropdownmenü **+** und dann auf **Neues Repository**:</span><span class="sxs-lookup"><span data-stu-id="dbc49-119">Click the **+** drop-down in the header, and select **New repository**:</span></span>

    ![GitHub-Option „Neues Repository“](media/cicd/github-new-repo.png)

1. <span data-ttu-id="dbc49-121">Klicken Sie im Dropdownmenü **Besitzer** auf Ihr Konto, und geben Sie *simple-feed-reader* in das Textfeld **Repositoryname** ein.</span><span class="sxs-lookup"><span data-stu-id="dbc49-121">Select your account in the **Owner** drop-down, and enter *simple-feed-reader* in the **Repository name** textbox.</span></span>
1. <span data-ttu-id="dbc49-122">Klicken Sie auf die Schaltfläche **Repository erstellen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-122">Click the **Create repository** button.</span></span>
1. <span data-ttu-id="dbc49-123">Öffnen Sie die Befehlsshell Ihres lokalen Computers.</span><span class="sxs-lookup"><span data-stu-id="dbc49-123">Open your local machine's command shell.</span></span> <span data-ttu-id="dbc49-124">Navigieren Sie zu dem Verzeichnis, in dem das Git-Repository *simple-feed-reader* gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-124">Navigate to the directory in which the *simple-feed-reader* Git repository is stored.</span></span>
1. <span data-ttu-id="dbc49-125">Benennen Sie das vorhandene *origin*-Remoteelement in *upstream* um.</span><span class="sxs-lookup"><span data-stu-id="dbc49-125">Rename the existing *origin* remote to *upstream*.</span></span> <span data-ttu-id="dbc49-126">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="dbc49-126">Execute the following command:</span></span>

    ```console
    git remote rename origin upstream
    ```

1. <span data-ttu-id="dbc49-127">Fügen Sie ein neues *origin*-Remoteelement hinzu, das auf die Kopie des Repositorys auf GitHub zeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-127">Add a new *origin* remote pointing to your copy of the repository on GitHub.</span></span> <span data-ttu-id="dbc49-128">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="dbc49-128">Execute the following command:</span></span>

    ```console
    git remote add origin https://github.com/<GitHub_username>/simple-feed-reader/
    ```

1. <span data-ttu-id="dbc49-129">Veröffentlichen Sie Ihr lokales Git-Repository im neu erstellten GitHub-Repository.</span><span class="sxs-lookup"><span data-stu-id="dbc49-129">Publish your local Git repository to the newly created GitHub repository.</span></span> <span data-ttu-id="dbc49-130">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="dbc49-130">Execute the following command:</span></span>

    ```console
    git push -u origin master
    ```

1. <span data-ttu-id="dbc49-131">Öffnen Sie ein Browserfenster, und navigieren Sie zu `https://github.com/<GitHub_username>/simple-feed-reader/`.</span><span class="sxs-lookup"><span data-stu-id="dbc49-131">Open a browser window, and navigate to `https://github.com/<GitHub_username>/simple-feed-reader/`.</span></span> <span data-ttu-id="dbc49-132">Überprüfen Sie, ob Ihr Code im GitHub-Repository angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="dbc49-132">Validate that your code appears in the GitHub repository.</span></span>

## <a name="disconnect-local-git-deployment"></a><span data-ttu-id="dbc49-133">Trennen der Verbindung mit lokaler Git-Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="dbc49-133">Disconnect local Git deployment</span></span>

<span data-ttu-id="dbc49-134">Entfernen Sie die lokale Git-Bereitstellung mit den folgenden Schritten.</span><span class="sxs-lookup"><span data-stu-id="dbc49-134">Remove the local Git deployment with the following steps.</span></span> <span data-ttu-id="dbc49-135">Azure Pipelines (ein Azure DevOps-Dienst) ersetzt diese Funktionalität und erweitert sie.</span><span class="sxs-lookup"><span data-stu-id="dbc49-135">Azure Pipelines (an Azure DevOps service) both replaces and augments that functionality.</span></span>

1. <span data-ttu-id="dbc49-136">Öffnen Sie das [Azure-Portal](https://portal.azure.com/), und navigieren Sie zur Web-App *staging (mywebapp\<unique_number\>/staging)* .</span><span class="sxs-lookup"><span data-stu-id="dbc49-136">Open the [Azure portal](https://portal.azure.com/), and navigate to the *staging (mywebapp\<unique_number\>/staging)* Web App.</span></span> <span data-ttu-id="dbc49-137">Die Web-App kann schnell gefunden werden, indem Sie im Suchfeld des Portals *staging* eingeben:</span><span class="sxs-lookup"><span data-stu-id="dbc49-137">The Web App can be quickly located by entering *staging* in the portal's search box:</span></span>

    ![Suchbegriff „staging“ in der Web-App](media/cicd/portal-search-box.png)

1. <span data-ttu-id="dbc49-139">Klicken Sie auf **Bereitstellungscenter**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-139">Click **Deployment Center**.</span></span> <span data-ttu-id="dbc49-140">Ein neues Panel wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-140">A new panel appears.</span></span> <span data-ttu-id="dbc49-141">Klicken Sie auf **Verbindung trennen**, um die lokale Konfiguration der Git-Quellcodeverwaltung zu entfernen, die im vorherigen Kapitel hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="dbc49-141">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="dbc49-142">Bestätigen Sie den Entfernungsvorgang, indem Sie auf die Schaltfläche **Ja** klicken.</span><span class="sxs-lookup"><span data-stu-id="dbc49-142">Confirm the removal operation by clicking the **Yes** button.</span></span>
1. <span data-ttu-id="dbc49-143">Navigieren Sie zum App Service *mywebapp<unique_number>* .</span><span class="sxs-lookup"><span data-stu-id="dbc49-143">Navigate to the *mywebapp<unique_number>* App Service.</span></span> <span data-ttu-id="dbc49-144">Zur Erinnerung: Das Suchfeld des Portals kann verwendet werden, um App Service schnell zu finden.</span><span class="sxs-lookup"><span data-stu-id="dbc49-144">As a reminder, the portal's search box can be used to quickly locate the App Service.</span></span>
1. <span data-ttu-id="dbc49-145">Klicken Sie auf **Bereitstellungscenter**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-145">Click **Deployment Center**.</span></span> <span data-ttu-id="dbc49-146">Ein neues Panel wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-146">A new panel appears.</span></span> <span data-ttu-id="dbc49-147">Klicken Sie auf **Verbindung trennen**, um die lokale Konfiguration der Git-Quellcodeverwaltung zu entfernen, die im vorherigen Kapitel hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="dbc49-147">Click **Disconnect** to remove the local Git source control configuration that was added in the previous chapter.</span></span> <span data-ttu-id="dbc49-148">Bestätigen Sie den Entfernungsvorgang, indem Sie auf die Schaltfläche **Ja** klicken.</span><span class="sxs-lookup"><span data-stu-id="dbc49-148">Confirm the removal operation by clicking the **Yes** button.</span></span>

## <a name="create-an-azure-devops-organization"></a><span data-ttu-id="dbc49-149">Erstellen einer Azure DevOps-Organisation</span><span class="sxs-lookup"><span data-stu-id="dbc49-149">Create an Azure DevOps organization</span></span>

1. <span data-ttu-id="dbc49-150">Öffnen Sie einen Browser, und navigieren Sie zur Seite für das [Erstellen einer Azure DevOps-Organisation](https://go.microsoft.com/fwlink/?LinkId=307137).</span><span class="sxs-lookup"><span data-stu-id="dbc49-150">Open a browser, and navigate to the [Azure DevOps organization creation page](https://go.microsoft.com/fwlink/?LinkId=307137).</span></span>
1. <span data-ttu-id="dbc49-151">Geben Sie einen eindeutigen Namen in das Textfeld **Wählen Sie einen einprägsamen Namen.** ein, um die URL für den Zugriff auf Ihre Azure DevOps-Organisation zu bilden.</span><span class="sxs-lookup"><span data-stu-id="dbc49-151">Type a unique name into the **Pick a memorable name** textbox to form the URL for accessing your Azure DevOps organization.</span></span>
1. <span data-ttu-id="dbc49-152">Aktivieren Sie das Optionsfeld **Git**, da der Code in einem GitHub-Repository gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="dbc49-152">Select the **Git** radio button, since the code is hosted in a GitHub repository.</span></span>
1. <span data-ttu-id="dbc49-153">Klicken Sie auf die Schaltfläche **Continue** (Weiter).</span><span class="sxs-lookup"><span data-stu-id="dbc49-153">Click the **Continue** button.</span></span> <span data-ttu-id="dbc49-154">Nach einer kurzen Wartezeit werden ein Konto und ein Teamprojekt mit dem Namen *MyFirstProject* erstellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-154">After a short wait, an account and a team project, named *MyFirstProject*, are created.</span></span>

    ![Seite zum Erstellen der Azure DevOps-Organisation](media/cicd/vsts-account-creation.png)

1. <span data-ttu-id="dbc49-156">Öffnen Sie die Bestätigungs-E-Mail, die anzeigt, dass die Azure DevOps-Organisation und das Projekt verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="dbc49-156">Open the confirmation email indicating that the Azure DevOps organization and project are ready for use.</span></span> <span data-ttu-id="dbc49-157">Klicken Sie auf die Schaltfläche **Starten Sie Ihr Projekt**:</span><span class="sxs-lookup"><span data-stu-id="dbc49-157">Click the **Start your project** button:</span></span>

    ![Schaltfläche „Starten Sie Ihr Projekt“](media/cicd/vsts-start-project.png)

1. <span data-ttu-id="dbc49-159">In einem Browser wird *\<account_name\>.visualstudio.com* geöffnet.</span><span class="sxs-lookup"><span data-stu-id="dbc49-159">A browser opens to *\<account_name\>.visualstudio.com*.</span></span> <span data-ttu-id="dbc49-160">Klicken Sie auf den Link *MyFirstProject*, um mit der Konfiguration der DevOps-Pipeline des Projekts zu beginnen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-160">Click the *MyFirstProject* link to begin configuring the project's DevOps pipeline.</span></span>

## <a name="configure-the-azure-pipelines-pipeline"></a><span data-ttu-id="dbc49-161">Konfigurieren der Azure Pipelines-Pipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-161">Configure the Azure Pipelines pipeline</span></span>

<span data-ttu-id="dbc49-162">Für den Abschluss des Vorgangs sind drei Schritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="dbc49-162">There are three distinct steps to complete.</span></span> <span data-ttu-id="dbc49-163">Durch die Ausführung der Schritte in den folgenden drei Abschnitten wird eine funktionsfähige DevOps-Pipeline erstellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-163">Completing the steps in the following three sections results in an operational DevOps pipeline.</span></span>

### <a name="grant-azure-devops-access-to-the-github-repository"></a><span data-ttu-id="dbc49-164">Gewähren des Zugriffs auf das GitHub-Repository für Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="dbc49-164">Grant Azure DevOps access to the GitHub repository</span></span>

1. <span data-ttu-id="dbc49-165">Erweitern **oder erstellen Sie den Code über den Accordion-Bereich eines externen Repositorys**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-165">Expand the **or build code from an external repository** accordion.</span></span> <span data-ttu-id="dbc49-166">Klicken Sie auf die Schaltfläche **Build einrichten**:</span><span class="sxs-lookup"><span data-stu-id="dbc49-166">Click the **Setup Build** button:</span></span>

    ![Schaltfläche „Build einrichten“](media/cicd/vsts-setup-build.png)

1. <span data-ttu-id="dbc49-168">Klicken Sie im Abschnitt **Quelle auswählen** auf die Option **GitHub**:</span><span class="sxs-lookup"><span data-stu-id="dbc49-168">Select the **GitHub** option from the **Select a source** section:</span></span>

    ![Auswählen einer Quelle (GitHub)](media/cicd/vsts-select-source.png)

1. <span data-ttu-id="dbc49-170">Die Autorisierung ist erforderlich, bevor Azure DevOps auf Ihr GitHub-Repository zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="dbc49-170">Authorization is required before Azure DevOps can access your GitHub repository.</span></span> <span data-ttu-id="dbc49-171">Geben Sie im Textfeld **Verbindungsname** *<GitHub_username> GitHub connection* ein.</span><span class="sxs-lookup"><span data-stu-id="dbc49-171">Enter *<GitHub_username> GitHub connection* in the **Connection name** textbox.</span></span> <span data-ttu-id="dbc49-172">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="dbc49-172">For example:</span></span>

    ![GitHub-Verbindungsname](media/cicd/vsts-repo-authz.png)

1. <span data-ttu-id="dbc49-174">Wenn für Ihr GitHub-Konto die zweistufige Authentifizierung aktiviert ist, ist ein persönliches Zugriffstoken erforderlich.</span><span class="sxs-lookup"><span data-stu-id="dbc49-174">If two-factor authentication is enabled on your GitHub account, a personal access token is required.</span></span> <span data-ttu-id="dbc49-175">Klicken Sie in diesem Fall auf den Link **Verwenden Sie zum Autorisieren das persönliche GitHub-Zugriffstoken.**</span><span class="sxs-lookup"><span data-stu-id="dbc49-175">In that case, click the **Authorize with a GitHub personal access token** link.</span></span> <span data-ttu-id="dbc49-176">Wenn Sie Hilfe benötigen, finden Sie weitere Informationen in den [offiziellen GitHub-Anweisungen für das Erstellen von persönlichen Zugriffstokens](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).</span><span class="sxs-lookup"><span data-stu-id="dbc49-176">See the [official GitHub personal access token creation instructions](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/) for help.</span></span> <span data-ttu-id="dbc49-177">Nur der *repo*-Geltungsbereich wird benötigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-177">Only the *repo* scope of permissions is needed.</span></span> <span data-ttu-id="dbc49-178">Klicken Sie andernfalls auf die Schaltfläche **Mithilfe von OAuth autorisieren**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-178">Otherwise, click the **Authorize using OAuth** button.</span></span>
1. <span data-ttu-id="dbc49-179">Melden Sie sich bei Ihrem GitHub-Konto an, wenn Sie dazu aufgefordert werden.</span><span class="sxs-lookup"><span data-stu-id="dbc49-179">When prompted, sign in to your GitHub account.</span></span> <span data-ttu-id="dbc49-180">Klicken Sie dann auf „Autorisieren“, um Zugriff auf Ihre Azure DevOps-Organisation zu gewähren.</span><span class="sxs-lookup"><span data-stu-id="dbc49-180">Then select Authorize to grant access to your Azure DevOps organization.</span></span> <span data-ttu-id="dbc49-181">Bei erfolgreicher Ausführung wird ein neuer Dienstendpunkt erstellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-181">If successful, a new service endpoint is created.</span></span>
1. <span data-ttu-id="dbc49-182">Klicken Sie neben der Schaltfläche **Repository** auf die Auslassungspunkte (...).</span><span class="sxs-lookup"><span data-stu-id="dbc49-182">Click the ellipsis button next to the **Repository** button.</span></span> <span data-ttu-id="dbc49-183">Wählen Sie das Repository *<GitHub_username>/simple-feed-reader* aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="dbc49-183">Select the *<GitHub_username>/simple-feed-reader* repository from the list.</span></span> <span data-ttu-id="dbc49-184">Klicken Sie auf die Schaltfläche **Auswählen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-184">Click the **Select** button.</span></span>
1. <span data-ttu-id="dbc49-185">Wählen Sie im Dropdownmenü **Standardbranch für manuelle und geplante Builds** auf den Standardbranch (*master*).</span><span class="sxs-lookup"><span data-stu-id="dbc49-185">Select the default branch (*master*) from the **Default branch for manual and scheduled builds** drop-down.</span></span> <span data-ttu-id="dbc49-186">Klicken Sie auf die Schaltfläche **Continue** (Weiter).</span><span class="sxs-lookup"><span data-stu-id="dbc49-186">Click the **Continue** button.</span></span> <span data-ttu-id="dbc49-187">Die Seite für die Vorlagenauswahl wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-187">The template selection page appears.</span></span>

### <a name="create-the-build-definition"></a><span data-ttu-id="dbc49-188">Erstellen der Builddefinition</span><span class="sxs-lookup"><span data-stu-id="dbc49-188">Create the build definition</span></span>

1. <span data-ttu-id="dbc49-189">Geben Sie auf der Seite für die Vorlagenauswahl *ASP.NET Core* in das Suchfeld ein:</span><span class="sxs-lookup"><span data-stu-id="dbc49-189">From the template selection page, enter *ASP.NET Core* in the search box:</span></span>

    ![Suche nach ASP.NET Core auf der Vorlagenseite](media/cicd/vsts-template-selection.png)

1. <span data-ttu-id="dbc49-191">Die Suchergebnisse für die Vorlage werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-191">The template search results appear.</span></span> <span data-ttu-id="dbc49-192">Zeigen Sie mit der Maustaste auf die Vorlage **ASP.NET Core**, und klicken Sie auf die Schaltfläche **Anwenden**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-192">Hover over the **ASP.NET Core** template, and click the **Apply** button.</span></span>
1. <span data-ttu-id="dbc49-193">Die Registerkarte **Aufgaben** der Builddefinition wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-193">The **Tasks** tab of the build definition appears.</span></span> <span data-ttu-id="dbc49-194">Klicken Sie auf die Registerkarte **Trigger**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-194">Click the **Triggers** tab.</span></span>
1. <span data-ttu-id="dbc49-195">Aktivieren Sie das Kontrollkästchen **Continuous Integration aktivieren**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-195">Check the **Enable continuous integration** box.</span></span> <span data-ttu-id="dbc49-196">Vergewissern Sie sich, dass im Abschnitt **Branchfilter** das Dropdownmenü **Typ** auf *Einschließen* festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-196">Under the **Branch filters** section, confirm that the **Type** drop-down is set to *Include*.</span></span> <span data-ttu-id="dbc49-197">Legen Sie das Dropdownmenü **Branchspezifikation** auf *master* fest.</span><span class="sxs-lookup"><span data-stu-id="dbc49-197">Set the **Branch specification** drop-down to *master*.</span></span>

    ![Aktivieren der Continuous Integration-Einstellungen](media/cicd/vsts-enable-ci.png)

    <span data-ttu-id="dbc49-199">Diese Einstellungen bewirken die Auslösung eines Builds, wenn eine Änderung an den Standardbranch (*master*) des GitHub-Repositorys gepusht wird.</span><span class="sxs-lookup"><span data-stu-id="dbc49-199">These settings cause a build to trigger when any change is pushed to the default branch (*master*) of the GitHub repository.</span></span> <span data-ttu-id="dbc49-200">Continuous Integration wird im Abschnitt [Committen von Änderungen auf GitHub und automatisches Bereitstellen in Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) getestet.</span><span class="sxs-lookup"><span data-stu-id="dbc49-200">Continuous integration is tested in the [Commit changes to GitHub and automatically deploy to Azure](#commit-changes-to-github-and-automatically-deploy-to-azure) section.</span></span>

1. <span data-ttu-id="dbc49-201">Klicken Sie auf die Schaltfläche **Speichern und in Warteschlange einreihen** und dann auf die Option **Speichern**:</span><span class="sxs-lookup"><span data-stu-id="dbc49-201">Click the **Save & queue** button, and select the **Save** option:</span></span>

    ![Schaltfläche „Speichern“](media/cicd/vsts-save-build.png)

1. <span data-ttu-id="dbc49-203">Das folgende modale Dialogfeld wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-203">The following modal dialog appears:</span></span>

    ![Speichern der Builddefinition: modales Dialogfeld](media/cicd/vsts-save-modal.png)

    <span data-ttu-id="dbc49-205">Verwenden Sie den Standardordner von *\\* , und klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-205">Use the default folder of *\\*, and click the **Save** button.</span></span>

### <a name="create-the-release-pipeline"></a><span data-ttu-id="dbc49-206">Erstellen der Releasepipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-206">Create the release pipeline</span></span>

1. <span data-ttu-id="dbc49-207">Klicken Sie auf die Registerkarte **Releases** Ihres Teamprojekts.</span><span class="sxs-lookup"><span data-stu-id="dbc49-207">Click the **Releases** tab of your team project.</span></span> <span data-ttu-id="dbc49-208">Klicken Sie auf die Schaltfläche **Neue Pipeline**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-208">Click the **New pipeline** button.</span></span>

    ![Registerkarte „Releases“: Schaltfläche für neue Definition](media/cicd/vsts-new-release-definition.png)

    <span data-ttu-id="dbc49-210">Der Bereich für die Vorlagenauswahl wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-210">The template selection pane appears.</span></span>

1. <span data-ttu-id="dbc49-211">Geben Sie auf der Seite für die Vorlagenauswahl *App Service* in das Suchfeld ein:</span><span class="sxs-lookup"><span data-stu-id="dbc49-211">From the template selection page, enter *App Service* in the search box:</span></span>

    ![Releasepipeline: Suchfeld für Vorlage](media/cicd/vsts-release-template-search.png)

1. <span data-ttu-id="dbc49-213">Die Suchergebnisse für die Vorlage werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-213">The template search results appear.</span></span> <span data-ttu-id="dbc49-214">Bewegen Sie den Mauszeiger über die Vorlage **Azure App Service-Bereitstellung mit Slot**, und klicken Sie auf die Schaltfläche **Anwenden**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-214">Hover over the **Azure App Service Deployment with Slot** template, and click the **Apply** button.</span></span> <span data-ttu-id="dbc49-215">Die Registerkarte **Pipeline** der Releasepipeline wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-215">The **Pipeline** tab of the release pipeline appears.</span></span>

    ![Registerkarte „Pipeline“ der Releasepipeline](media/cicd/vsts-release-definition-pipeline.png)

1. <span data-ttu-id="dbc49-217">Klicken Sie im Feld **Artefakte** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-217">Click the **Add** button in the **Artifacts** box.</span></span> <span data-ttu-id="dbc49-218">Das Panel **Artefakt hinzufügen** wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-218">The **Add artifact** panel appears:</span></span>

    ![Releasepipeline: Panel „Artefakt hinzufügen“](media/cicd/vsts-release-add-artifact.png)

1. <span data-ttu-id="dbc49-220">Klicken Sie im Abschnitt **Quelltyp** auf die Kachel **Build**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-220">Select the **Build** tile from the **Source type** section.</span></span> <span data-ttu-id="dbc49-221">Dieser Typ ermöglicht das Verknüpfen der Releasepipeline mit der Builddefinition.</span><span class="sxs-lookup"><span data-stu-id="dbc49-221">This type allows for the linking of the release pipeline to the build definition.</span></span>
1. <span data-ttu-id="dbc49-222">Klicken Sie im Dropdownmenü **Projekt** auf *MyFirstProject*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-222">Select *MyFirstProject* from the **Project** drop-down.</span></span>
1. <span data-ttu-id="dbc49-223">Klicken sie im Dropdownmenü **Source (Build definition)** (Quelle (Builddefinition)) auf den Builddefinitionsnamen *MyFirstProject-ASP.NET Core-CI*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-223">Select the build definition name, *MyFirstProject-ASP.NET Core-CI*, from the **Source (Build definition)** drop-down.</span></span>
1. <span data-ttu-id="dbc49-224">Wählen Sie im Dropdownmenü **Standardversion** die Option *Neueste* aus.</span><span class="sxs-lookup"><span data-stu-id="dbc49-224">Select *Latest* from the **Default version** drop-down.</span></span> <span data-ttu-id="dbc49-225">Mit dieser Option werden die Artefakte erstellt, die von der neuesten Builddefinition erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="dbc49-225">This option builds the artifacts produced by the latest run of the build definition.</span></span>
1. <span data-ttu-id="dbc49-226">Ersetzen Sie den Text im Textfeld **Quellalias** durch *Ablegen*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-226">Replace the text in the **Source alias** textbox with *Drop*.</span></span>
1. <span data-ttu-id="dbc49-227">Klicken Sie auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-227">Click the **Add** button.</span></span> <span data-ttu-id="dbc49-228">Der Abschnitt **Artefakte** wird aktualisiert, um die Änderungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-228">The **Artifacts** section updates to display the changes.</span></span>
1. <span data-ttu-id="dbc49-229">Durch Klicken auf das Blitzsymbol wird Continuous Deployment aktiviert:</span><span class="sxs-lookup"><span data-stu-id="dbc49-229">Click the lightning bolt icon to enable continuous deployments:</span></span>

    ![Releasepipeline: Blitzsymbol im Abschnitt „Artefakte“](media/cicd/vsts-artifacts-lightning-bolt.png)

    <span data-ttu-id="dbc49-231">Wenn diese Option aktiviert ist, wird jedes Mal eine Bereitstellung durchgeführt, wenn ein neuer Build verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-231">With this option enabled, a deployment occurs each time a new build is available.</span></span>
1. <span data-ttu-id="dbc49-232">Im rechten Bereich wird das Panel **Continuous Deployment-Trigger** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-232">A **Continuous deployment trigger** panel appears to the right.</span></span> <span data-ttu-id="dbc49-233">Klicken Sie auf die Umschaltfläche, um das Feature zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="dbc49-233">Click the toggle button to enable the feature.</span></span> <span data-ttu-id="dbc49-234">Es ist nicht erforderlich, den **Pull Request-Trigger** zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="dbc49-234">It isn't necessary to enable the **Pull request trigger**.</span></span>
1. <span data-ttu-id="dbc49-235">Klicken Sie im Abschnitt **Build-Branchfilter** auf das Dropdownmenü **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-235">Click the **Add** drop-down in the **Build branch filters** section.</span></span> <span data-ttu-id="dbc49-236">Wählen Sie die Option **Build Definition's default branch** (Standardbranch der Builddefinition) aus.</span><span class="sxs-lookup"><span data-stu-id="dbc49-236">Choose the **Build Definition's default branch** option.</span></span> <span data-ttu-id="dbc49-237">Dieser Filter bewirkt, dass das Release nur für einen Build aus dem Standardbranch (*master*) des GitHub-Repositorys ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="dbc49-237">This filter causes the release to trigger only for a build from the GitHub repository's default branch (*master*).</span></span>
1. <span data-ttu-id="dbc49-238">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-238">Click the **Save** button.</span></span> <span data-ttu-id="dbc49-239">Klicken Sie im angezeigten modalen Dialogfeld **Speichern** auf die Schaltfläche **OK**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-239">Click the **OK** button in the resulting **Save** modal dialog.</span></span>
1. <span data-ttu-id="dbc49-240">Klicken Sie auf das Feld **Umgebung 1**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-240">Click the **Environment 1** box.</span></span> <span data-ttu-id="dbc49-241">Auf der rechten Seite wird das Panel **Umgebung** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-241">An **Environment** panel appears to the right.</span></span> <span data-ttu-id="dbc49-242">Ändern Sie den Text *Umgebung 1* im Textfeld **Umgebungsname** in *Produktion*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-242">Change the *Environment 1* text in the **Environment name** textbox to *Production*.</span></span>

   ![Releasepipeline: Textfeld für Umgebungsname](media/cicd/vsts-environment-name-textbox.png)

1. <span data-ttu-id="dbc49-244">Klicken Sie im Textfeld **Produktion** auf den Link **1 phase, 2 tasks** (1 Phase, 2 Aufgaben):</span><span class="sxs-lookup"><span data-stu-id="dbc49-244">Click the **1 phase, 2 tasks** link in the **Production** box:</span></span>

    ![Releasepipeline: Produktionsumgebungslink.png](media/cicd/vsts-production-link.png)

    <span data-ttu-id="dbc49-246">Die Registerkarte **Aufgaben** der Umgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-246">The **Tasks** tab of the environment appears.</span></span>
1. <span data-ttu-id="dbc49-247">Klicken Sie auf die Registerkarte **Deploy Azure App Service to Slot** (Azure App Service in Slot bereitstellen).</span><span class="sxs-lookup"><span data-stu-id="dbc49-247">Click the **Deploy Azure App Service to Slot** task.</span></span> <span data-ttu-id="dbc49-248">Die Einstellungen werden in einem Panel auf der rechten Seite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-248">Its settings appear in a panel to the right.</span></span>
1. <span data-ttu-id="dbc49-249">Wählen Sie das Azure-Abonnement aus, das App Service im Dropdownmenü **Azure-Abonnement** zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-249">Select the Azure subscription associated with the App Service from the **Azure subscription** drop-down.</span></span> <span data-ttu-id="dbc49-250">Klicken Sie nach der Auswahl auf die Schaltfläche **Autorisieren**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-250">Once selected, click the **Authorize** button.</span></span>
1. <span data-ttu-id="dbc49-251">Klicken Sie im Dropdownmenü **App-Typ** auf die Option *Web-App*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-251">Select *Web App* from the **App type** drop-down.</span></span>
1. <span data-ttu-id="dbc49-252">Klicken Sie im Dropdownmenü **App Service-Name** auf die Option *mywebapp/<unique_number/>* .</span><span class="sxs-lookup"><span data-stu-id="dbc49-252">Select *mywebapp/<unique_number/>* from the **App service name** drop-down.</span></span>
1. <span data-ttu-id="dbc49-253">Klicken Sie im Dropdownmenü **Ressourcengruppe** auf die Option *AzureTutorial*.</span><span class="sxs-lookup"><span data-stu-id="dbc49-253">Select *AzureTutorial* from the **Resource group** drop-down.</span></span>
1. <span data-ttu-id="dbc49-254">Klicken Sie im Dropdownmenü *Slot* auf die Option **Staging**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-254">Select *staging* from the **Slot** drop-down.</span></span>
1. <span data-ttu-id="dbc49-255">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-255">Click the **Save** button.</span></span>
1. <span data-ttu-id="dbc49-256">Zeigen Sie auf den Namen der standardmäßigen Releasepipeline.</span><span class="sxs-lookup"><span data-stu-id="dbc49-256">Hover over the default release pipeline name.</span></span> <span data-ttu-id="dbc49-257">Klicken Sie auf das Stiftsymbol, um diese zu bearbeiten.</span><span class="sxs-lookup"><span data-stu-id="dbc49-257">Click the pencil icon to edit it.</span></span> <span data-ttu-id="dbc49-258">Verwenden Sie *MyFirstProject-ASP.NET Core-CD* als Namen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-258">Use *MyFirstProject-ASP.NET Core-CD* as the name.</span></span>

    ![Releasepipelinename](media/cicd/vsts-release-definition-name.png)

1. <span data-ttu-id="dbc49-260">Klicken Sie auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-260">Click the **Save** button.</span></span>

## <a name="commit-changes-to-github-and-automatically-deploy-to-azure"></a><span data-ttu-id="dbc49-261">Ausführen eines Commits für Änderungen an GitHub und automatisches Bereitstellen in Azure</span><span class="sxs-lookup"><span data-stu-id="dbc49-261">Commit changes to GitHub and automatically deploy to Azure</span></span>

1. <span data-ttu-id="dbc49-262">Öffnen Sie *SimpleFeedReader.sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dbc49-262">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
1. <span data-ttu-id="dbc49-263">Öffnen Sie *Pages\Index.cshtml* im Projektmappen-Explorer.</span><span class="sxs-lookup"><span data-stu-id="dbc49-263">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="dbc49-264">Ändern Sie `<h2>Simple Feed Reader - V3</h2>` in `<h2>Simple Feed Reader - V4</h2>`.</span><span class="sxs-lookup"><span data-stu-id="dbc49-264">Change `<h2>Simple Feed Reader - V3</h2>` to `<h2>Simple Feed Reader - V4</h2>`.</span></span>
1. <span data-ttu-id="dbc49-265">Drücken Sie **STRG**+**UMSCHALT**+**B**, um die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-265">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
1. <span data-ttu-id="dbc49-266">Committen Sie die Datei in das GitHub-Repository.</span><span class="sxs-lookup"><span data-stu-id="dbc49-266">Commit the file to the GitHub repository.</span></span> <span data-ttu-id="dbc49-267">Verwenden Sie entweder die Seite **Änderungen** in der Registerkarte *Team Explorer* von Visual Studio, oder führen Sie mithilfe der Befehlsshell des lokalen Computers Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="dbc49-267">Use either the **Changes** page in Visual Studio's *Team Explorer* tab, or execute the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V4"
    ```

1. <span data-ttu-id="dbc49-268">Pushen Sie die Änderung im Standardbranch (*master*) an das *origin*-Remoterepository Ihres GitHub-Repositorys.</span><span class="sxs-lookup"><span data-stu-id="dbc49-268">Push the change in the default branch (*master*) to the *origin* remote of your GitHub repository.</span></span> <span data-ttu-id="dbc49-269">Ersetzen Sie im folgenden Befehl den Platzhalter `{BRANCH}` durch den Standardbranch (verwenden Sie `master`):</span><span class="sxs-lookup"><span data-stu-id="dbc49-269">In the following command, replace the placeholder `{BRANCH}` with the default branch (use `master`):</span></span>

    ```console
    git push origin {BRANCH}
    ```

    <span data-ttu-id="dbc49-270">Der Commit wird im Standardbranch (*master*) des GitHub-Repositorys angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-270">The commit appears in the GitHub repository's default branch (*master*):</span></span>

    ![GitHub-Commit im Standardbranch („master“)](media/cicd/github-commit.png)

    <span data-ttu-id="dbc49-272">Der Build wird ausgelöst, da Continuous Integration in der Registerkarte **Trigger** der Builddefinition aktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="dbc49-272">The build is triggered, since continuous integration is enabled in the build definition's **Triggers** tab:</span></span>

    ![Aktivieren von Continuous Integration](media/cicd/enable-ci.png)

1. <span data-ttu-id="dbc49-274">Navigieren Sie in Azure DevOps Services auf der Seite **Azure Pipelines** > **Builds** zur Registerkarte **In Warteschlange eingereiht**</span><span class="sxs-lookup"><span data-stu-id="dbc49-274">Navigate to the **Queued** tab of the **Azure Pipelines** > **Builds** page in Azure DevOps Services.</span></span> <span data-ttu-id="dbc49-275">Der Build in der Warteschlange zeigt den Branch und den Commit, die den Build ausgelöst haben:</span><span class="sxs-lookup"><span data-stu-id="dbc49-275">The queued build shows the branch and commit that triggered the build:</span></span>

    ![Build in Warteschlange](media/cicd/build-queued.png)

1. <span data-ttu-id="dbc49-277">Sobald der Buildvorgang erfolgreich ist, erfolgt eine Bereitstellung in Azure.</span><span class="sxs-lookup"><span data-stu-id="dbc49-277">Once the build succeeds, a deployment to Azure occurs.</span></span> <span data-ttu-id="dbc49-278">Navigieren Sie im Browser zur App.</span><span class="sxs-lookup"><span data-stu-id="dbc49-278">Navigate to the app in the browser.</span></span> <span data-ttu-id="dbc49-279">Beachten Sie, dass der Text „V4“ in der Überschrift angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="dbc49-279">Notice that the "V4" text appears in the heading:</span></span>

    ![Aktualisierte App](media/cicd/updated-app-v4.png)

## <a name="examine-the-azure-pipelines-pipeline"></a><span data-ttu-id="dbc49-281">Überprüfen der Azure Pipelines-Pipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-281">Examine the Azure Pipelines pipeline</span></span>

### <a name="build-definition"></a><span data-ttu-id="dbc49-282">Builddefinition</span><span class="sxs-lookup"><span data-stu-id="dbc49-282">Build definition</span></span>

<span data-ttu-id="dbc49-283">Eine Builddefinition mit dem Namen *MyFirstProject-ASP.NET Core-CI* wurde erstellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-283">A build definition was created with the name *MyFirstProject-ASP.NET Core-CI*.</span></span> <span data-ttu-id="dbc49-284">Nach Abschluss erstellt der Build eine *ZIP*-Datei einschließlich der zu veröffentlichenden Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-284">Upon completion, the build produces a *.zip* file including the assets to be published.</span></span> <span data-ttu-id="dbc49-285">Die Releasepipeline stellt diese Ressourcen in Azure bereit.</span><span class="sxs-lookup"><span data-stu-id="dbc49-285">The release pipeline deploys those assets to Azure.</span></span>

<span data-ttu-id="dbc49-286">Auf der Registerkarte **Aufgaben** der Builddefinition werden die einzelnen Schritte aufgelistet, die verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="dbc49-286">The build definition's **Tasks** tab lists the individual steps being used.</span></span> <span data-ttu-id="dbc49-287">Es gibt fünf Buildaufgaben.</span><span class="sxs-lookup"><span data-stu-id="dbc49-287">There are five build tasks.</span></span>

![Builddefinitionsaufgaben](media/cicd/build-definition-tasks.png)

1. <span data-ttu-id="dbc49-289">**Wiederherstellen** führt den Befehl `dotnet restore` aus, um die NuGet-Pakete der App wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-289">**Restore** &mdash; Executes the `dotnet restore` command to restore the app's NuGet packages.</span></span> <span data-ttu-id="dbc49-290">Der verwendete Standardpaketfeed ist nuget.org.</span><span class="sxs-lookup"><span data-stu-id="dbc49-290">The default package feed used is nuget.org.</span></span>
1. <span data-ttu-id="dbc49-291">**Build** führt den Befehl `dotnet build --configuration release` aus, um den App-Code zu kompilieren.</span><span class="sxs-lookup"><span data-stu-id="dbc49-291">**Build** &mdash; Executes the `dotnet build --configuration release` command to compile the app's code.</span></span> <span data-ttu-id="dbc49-292">Diese `--configuration`-Option wird verwendet, um eine optimierte Version des Codes zu erstellen, die für die Bereitstellung in einer Produktionsumgebung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-292">This `--configuration` option is used to produce an optimized version of the code, which is suitable for deployment to a production environment.</span></span> <span data-ttu-id="dbc49-293">Ändern Sie die Variable *BuildConfiguration* auf der Registerkarte **Variablen** der Builddefinition, wenn beispielsweise eine Debugkonfiguration erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-293">Modify the *BuildConfiguration* variable on the build definition's **Variables** tab if, for example, a debug configuration is needed.</span></span>
1. <span data-ttu-id="dbc49-294">**Testen** führt den `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>`-Befehl aus, um die Komponententests der App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-294">**Test** &mdash; Executes the `dotnet test --configuration release --logger trx --results-directory <local_path_on_build_agent>` command to run the app's unit tests.</span></span> <span data-ttu-id="dbc49-295">Komponententests werden in jedem C#-Projekt ausgeführt, das mit dem `**/*Tests/*.csproj`-Globmuster übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-295">Unit tests are executed within any C# project matching the `**/*Tests/*.csproj` glob pattern.</span></span> <span data-ttu-id="dbc49-296">Testergebnisse werden in einer *TRX*-Datei an dem Speicherort gespeichert, der durch die Option `--results-directory` angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="dbc49-296">Test results are saved in a *.trx* file at the location specified by the `--results-directory` option.</span></span> <span data-ttu-id="dbc49-297">Wenn ein Test fehlschlägt, schlägt der Build fehl und wird nicht bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-297">If any tests fail, the build fails and isn't deployed.</span></span>

    > [!NOTE]
    > <span data-ttu-id="dbc49-298">Ändern Sie *SimpleFeedReader.Tests\Services\NewsServiceTests.cs*, um einen der Tests absichtlich zu unterbrechen, um zu überprüfen, ob die Komponententests funktionieren.</span><span class="sxs-lookup"><span data-stu-id="dbc49-298">To verify the unit tests work, modify *SimpleFeedReader.Tests\Services\NewsServiceTests.cs* to purposefully break one of the tests.</span></span> <span data-ttu-id="dbc49-299">Ändern Sie beispielsweise in der `Returns_News_Stories_Given_Valid_Uri`-Methode `Assert.True(result.Count > 0);` in `Assert.False(result.Count > 0);` .</span><span class="sxs-lookup"><span data-stu-id="dbc49-299">For example, change `Assert.True(result.Count > 0);` to `Assert.False(result.Count > 0);` in the `Returns_News_Stories_Given_Valid_Uri` method.</span></span> <span data-ttu-id="dbc49-300">Committen Sie die Änderung, und übertragen Sie diese mithilfe von Push an GitHub.</span><span class="sxs-lookup"><span data-stu-id="dbc49-300">Commit and push the change to GitHub.</span></span> <span data-ttu-id="dbc49-301">Der Build wird ausgelöst und schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="dbc49-301">The build is triggered and fails.</span></span> <span data-ttu-id="dbc49-302">Der Status der Buildpipeline wird in **failed** (Fehlgeschlagen) geändert.</span><span class="sxs-lookup"><span data-stu-id="dbc49-302">The build pipeline status changes to **failed**.</span></span> <span data-ttu-id="dbc49-303">Machen Sie die Änderung rückgängig, committen Sie noch mal, und übertragen Sie sie erneut mithilfe von Push.</span><span class="sxs-lookup"><span data-stu-id="dbc49-303">Revert the change, commit, and push again.</span></span> <span data-ttu-id="dbc49-304">Der Build ist erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="dbc49-304">The build succeeds.</span></span>

1. <span data-ttu-id="dbc49-305">**Veröffentlichen** führt den `dotnet publish --configuration release --output <local_path_on_build_agent>`-Befehl aus, um eine *ZIP*-Datei mit den bereitzustellenden Artefakten zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-305">**Publish** &mdash; Executes the `dotnet publish --configuration release --output <local_path_on_build_agent>` command to produce a *.zip* file with the artifacts to be deployed.</span></span> <span data-ttu-id="dbc49-306">Die Option `--output` gibt den Veröffentlichungsort der *ZIP*-Datei an.</span><span class="sxs-lookup"><span data-stu-id="dbc49-306">The `--output` option specifies the publish location of the *.zip* file.</span></span> <span data-ttu-id="dbc49-307">Dieser Speicherort wird durch Übergeben einer [vordefinierten Variablen](/azure/devops/pipelines/build/variables) namens `$(build.artifactstagingdirectory)` angegeben.</span><span class="sxs-lookup"><span data-stu-id="dbc49-307">That location is specified by passing a [predefined variable](/azure/devops/pipelines/build/variables) named `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="dbc49-308">Diese Variable wird zu einem lokalen Pfad wie beispielsweise *c:\agent\_work\1\a* auf dem Build-Agent erweitert.</span><span class="sxs-lookup"><span data-stu-id="dbc49-308">That variable expands to a local path, such as *c:\agent\_work\1\a*, on the build agent.</span></span>
1. <span data-ttu-id="dbc49-309">**Artefakt veröffentlichen** veröffentlicht die *ZIP*-Datei, die von der Aufgabe **Veröffentlichen** erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="dbc49-309">**Publish Artifact** &mdash; Publishes the *.zip* file produced by the **Publish** task.</span></span> <span data-ttu-id="dbc49-310">Die Aufgabe akzeptiert den Speicherort der *ZIP*-Datei als Parameter, bei dem es sich um die vordefinierte Variable `$(build.artifactstagingdirectory)` handelt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-310">The task accepts the *.zip* file location as a parameter, which is the predefined variable `$(build.artifactstagingdirectory)`.</span></span> <span data-ttu-id="dbc49-311">Die *ZIP*-Datei wird als Ordner namens *drop* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="dbc49-311">The *.zip* file is published as a folder named *drop*.</span></span>

<span data-ttu-id="dbc49-312">Klicken Sie auf den Link **Zusammenfassung** der Builddefinition, um den Verlauf der Builds mit der folgenden Definition anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="dbc49-312">Click the build definition's **Summary** link to view a history of builds with the definition:</span></span>

![Screenshot mit dem Builddefinitionsverlauf](media/cicd/build-definition-summary.png)

<span data-ttu-id="dbc49-314">Klicken Sie auf der dann angezeigten Seite auf den Link, der der eindeutigen Buildnummer entspricht:</span><span class="sxs-lookup"><span data-stu-id="dbc49-314">On the resulting page, click the link corresponding to the unique build number:</span></span>

![Screenshot mit der Zusammenfassungsseite der Builddefinition](media/cicd/build-definition-completed.png)

<span data-ttu-id="dbc49-316">Eine Zusammenfassung dieses spezifischen Builds wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-316">A summary of this specific build is displayed.</span></span> <span data-ttu-id="dbc49-317">Klicken Sie auf die Registerkarte **Artefakte**, und beachten Sie, dass der vom Build erstellte Ordner *drop* aufgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="dbc49-317">Click the **Artifacts** tab, and notice the *drop* folder produced by the build is listed:</span></span>

![Screenshot mit den Builddefinitionsartefakten: „drop“-Ordner](media/cicd/build-definition-artifacts.png)

<span data-ttu-id="dbc49-319">Verwenden Sie die Links **Download** und **Durchsuchen**, um die veröffentlichten Artefakte zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="dbc49-319">Use the **Download** and **Explore** links to inspect the published artifacts.</span></span>

### <a name="release-pipeline"></a><span data-ttu-id="dbc49-320">Releasepipeline</span><span class="sxs-lookup"><span data-stu-id="dbc49-320">Release pipeline</span></span>

<span data-ttu-id="dbc49-321">Eine Releasepipeline mit dem Namen *MyFirstProject-ASP.NET Core-CD* wurde erstellt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-321">A release pipeline was created with the name *MyFirstProject-ASP.NET Core-CD*:</span></span>

![Screenshot mit der Übersicht über Releasepipelines](media/cicd/release-definition-overview.png)

<span data-ttu-id="dbc49-323">Die beiden Hauptkomponenten der Releasepipeline sind die **Artefakte** und die **Umgebungen**.</span><span class="sxs-lookup"><span data-stu-id="dbc49-323">The two major components of the release pipeline are the **Artifacts** and the **Environments**.</span></span> <span data-ttu-id="dbc49-324">Wenn Sie im Bereich **Artefakte** auf das Feld klicken, wird das folgende Panel angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-324">Clicking the box in the **Artifacts** section reveals the following panel:</span></span>

![Screenshot mit den Releasepipelineartefakten](media/cicd/release-definition-artifacts.png)

<span data-ttu-id="dbc49-326">Der Wert **Source (Build definition)** (Quelle (Builddefinition)) steht für die Builddefinition, mit der diese Releasepipeline verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="dbc49-326">The **Source (Build definition)** value represents the build definition to which this release pipeline is linked.</span></span> <span data-ttu-id="dbc49-327">Die *ZIP*-Datei, die bei erfolgreicher Ausführung der Builddefinition erstellt wurde, wird für die Umgebung *Produktion* für die Bereitstellung in Azure bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-327">The *.zip* file produced by a successful run of the build definition is provided to the *Production* environment for deployment to Azure.</span></span> <span data-ttu-id="dbc49-328">Klicken Sie im Feld für die Umgebung *Produktion* auf den Link *1 phase, 2 tasks* (1 Phase, 2 Aufgaben), um die Aufgaben der Releasepipeline anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="dbc49-328">Click the *1 phase, 2 tasks* link in the *Production* environment box to view the release pipeline tasks:</span></span>

![Screenshot mit den Releasepipelineaufgaben](media/cicd/release-definition-tasks.png)

<span data-ttu-id="dbc49-330">Die Releasepipeline besteht aus zwei Aufgaben: *Deploy Azure App Service to Slot* (Azure App Service in Slot bereitstellen) und *Manage Azure App Service - Slot Swap* (Azure App Service verwalten – Slotaustausch).</span><span class="sxs-lookup"><span data-stu-id="dbc49-330">The release pipeline consists of two tasks: *Deploy Azure App Service to Slot* and *Manage Azure App Service - Slot Swap*.</span></span> <span data-ttu-id="dbc49-331">Wenn Sie auf die erste Aufgabe klicken, wird die folgende Aufgabenkonfiguration angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-331">Clicking the first task reveals the following task configuration:</span></span>

![Screenshot mit der Bereitstellungsaufgabe für die Releasepipeline](media/cicd/release-definition-task1.png)

<span data-ttu-id="dbc49-333">Das Azure-Abonnement, der Diensttyp, der Name der Web-App, die Ressourcengruppe und der Bereitstellungsslot werden in der Bereitstellungsaufgabe definiert.</span><span class="sxs-lookup"><span data-stu-id="dbc49-333">The Azure subscription, service type, web app name, resource group, and deployment slot are defined in the deployment task.</span></span> <span data-ttu-id="dbc49-334">Das Textfeld **Paket oder Ordner** enthält den Pfad der *ZIP*-Datei, die extrahiert und im *Stagingslot* der Web-App *mywebapp\<unique_number\>* bereitgestellt werden soll.</span><span class="sxs-lookup"><span data-stu-id="dbc49-334">The **Package or folder** textbox holds the *.zip* file path to be extracted and deployed to the *staging* slot of the *mywebapp\<unique_number\>* web app.</span></span>

<span data-ttu-id="dbc49-335">Wenn Sie auf die Aufgabe für den Slotaustausch klicken, wird die folgende Aufgabenkonfiguration angezeigt:</span><span class="sxs-lookup"><span data-stu-id="dbc49-335">Clicking the slot swap task reveals the following task configuration:</span></span>

![Screenshot mit der Slotaustauschaufgabe für die Releasepipeline](media/cicd/release-definition-task2.png)

<span data-ttu-id="dbc49-337">Das Abonnement, die Ressourcengruppe, der Diensttyp, der Name der Web-App und Details zum Bereitstellungsslot werden bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="dbc49-337">The subscription, resource group, service type, web app name, and deployment slot details are provided.</span></span> <span data-ttu-id="dbc49-338">Das Kontrollkästchen **Mit Produktion tauschen** ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="dbc49-338">The **Swap with Production** check box is checked.</span></span> <span data-ttu-id="dbc49-339">Folglich werden die Bits, die im *Stagingslot* bereitgestellt werden, in der Produktionsumgebung ausgetauscht.</span><span class="sxs-lookup"><span data-stu-id="dbc49-339">Consequently, the bits deployed to the *staging* slot are swapped into the production environment.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="dbc49-340">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="dbc49-340">Additional reading</span></span>

* [<span data-ttu-id="dbc49-341">Erstellen Sie Ihre erste Pipeline mit Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="dbc49-341">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="dbc49-342">Erstellen und Testen eines .NET Core-Projekts</span><span class="sxs-lookup"><span data-stu-id="dbc49-342">Build and .NET Core project</span></span>](/azure/devops/pipelines/languages/dotnet-core)
* [<span data-ttu-id="dbc49-343">Bereitstellen einer Web-App mit Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="dbc49-343">Deploy a web app with Azure Pipelines</span></span>](/azure/devops/pipelines/targets/webapp)
