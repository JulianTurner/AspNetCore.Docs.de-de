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
# <a name="deploy-an-app-to-app-service"></a><span data-ttu-id="42fea-103">Bereitstellen einer App für App Service</span><span class="sxs-lookup"><span data-stu-id="42fea-103">Deploy an app to App Service</span></span>

<span data-ttu-id="42fea-104">[Azure App Service](/azure/app-service/) ist die Webhostingplattform von Azure.</span><span class="sxs-lookup"><span data-stu-id="42fea-104">[Azure App Service](/azure/app-service/) is Azure's web hosting platform.</span></span> <span data-ttu-id="42fea-105">Das Bereitstellen einer Web-App für Azure App Service kann manuell oder über einen automatisierten Prozess erfolgen.</span><span class="sxs-lookup"><span data-stu-id="42fea-105">Deploying a web app to Azure App Service can be done manually or by an automated process.</span></span> <span data-ttu-id="42fea-106">In diesem Abschnitt des Leitfadens werden Bereitstellungsmethoden behandelt, die manuell oder per Skript über die Befehlszeile bzw. manuell mit Visual Studio ausgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="42fea-106">This section of the guide discusses deployment methods that can be triggered manually or by script using the command line, or triggered manually using Visual Studio.</span></span>

<span data-ttu-id="42fea-107">In diesem Abschnitt führen Sie die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="42fea-107">In this section, you'll accomplish the following tasks:</span></span>

* <span data-ttu-id="42fea-108">Herunterladen und Erstellen der Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="42fea-108">Download and build the sample app.</span></span>
* <span data-ttu-id="42fea-109">Erstellen einer Azure App Service-Web-App mit der Azure Cloud Shell</span><span class="sxs-lookup"><span data-stu-id="42fea-109">Create an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="42fea-110">Bereitstellen der Beispiel-App in Azure mit Git</span><span class="sxs-lookup"><span data-stu-id="42fea-110">Deploy the sample app to Azure using Git.</span></span>
* <span data-ttu-id="42fea-111">Bereitstellen einer Änderung für die App mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42fea-111">Deploy a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="42fea-112">Hinzufügen eines Stagingslots zur Web-App</span><span class="sxs-lookup"><span data-stu-id="42fea-112">Add a staging slot to the web app.</span></span>
* <span data-ttu-id="42fea-113">Bereitstellen eines Updates für den Stagingslot</span><span class="sxs-lookup"><span data-stu-id="42fea-113">Deploy an update to the staging slot.</span></span>
* <span data-ttu-id="42fea-114">Vertauschen von Staging- und Produktionsslots</span><span class="sxs-lookup"><span data-stu-id="42fea-114">Swap the staging and production slots.</span></span>

## <a name="download-and-test-the-app"></a><span data-ttu-id="42fea-115">Herunterladen und Testen der App</span><span class="sxs-lookup"><span data-stu-id="42fea-115">Download and test the app</span></span>

<span data-ttu-id="42fea-116">Die in diesem Leitfaden verwendete App ist eine vorgefertigte ASP.NET Core-App, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span><span class="sxs-lookup"><span data-stu-id="42fea-116">The app used in this guide is a pre-built ASP.NET Core app, [Simple Feed Reader](https://github.com/Azure-Samples/simple-feed-reader/).</span></span> <span data-ttu-id="42fea-117">Es handelt sich um eine Razor Pages-App, die die `Microsoft.SyndicationFeed.ReaderWriter`-API verwendet, um einen RSS-/Atom-Feed abzurufen und die Nachrichtenelemente in einer Liste anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42fea-117">It's a Razor Pages app that uses the `Microsoft.SyndicationFeed.ReaderWriter` API to retrieve an RSS/Atom feed and display the news items in a list.</span></span>

<span data-ttu-id="42fea-118">Sie können den Code nach Belieben überprüfen, aber es ist wichtig, zu verstehen, dass diese App keine Besonderheiten aufweist.</span><span class="sxs-lookup"><span data-stu-id="42fea-118">Feel free to review the code, but it's important to understand that there's nothing special about this app.</span></span> <span data-ttu-id="42fea-119">Es handelt sich lediglich um eine einfache ASP.NET Core-App zur Veranschaulichung.</span><span class="sxs-lookup"><span data-stu-id="42fea-119">It's just a simple ASP.NET Core app for illustrative purposes.</span></span>

<span data-ttu-id="42fea-120">Laden Sie den Code über eine Befehlsshell herunter, erstellen Sie das Projekt und führen Sie es wie folgt aus.</span><span class="sxs-lookup"><span data-stu-id="42fea-120">From a command shell, download the code, build the project, and run it as follows.</span></span>

> <span data-ttu-id="42fea-121">*Hinweis: Linux-/macOS-Benutzer sollten entsprechende Änderungen an den Pfaden vornehmen, z. B. einen Schrägstrich (`/`) anstatt eines umgekehrten Schrägstrichs (`\`) verwenden.*</span><span class="sxs-lookup"><span data-stu-id="42fea-121">*Note: Linux/macOS users should make appropriate changes for paths, e.g., using forward slash (`/`) rather than back slash (`\`).*</span></span>

1. <span data-ttu-id="42fea-122">Klonen Sie den Code in einen Ordner auf Ihrem lokalen Computer.</span><span class="sxs-lookup"><span data-stu-id="42fea-122">Clone the code to a folder on your local machine.</span></span>

    ```console
    git clone https://github.com/Azure-Samples/simple-feed-reader/
    ```

2. <span data-ttu-id="42fea-123">Ändern Sie Ihren Arbeitsordner in den *simple-feed-reader*-Ordner, der erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="42fea-123">Change your working folder to the *simple-feed-reader* folder that was created.</span></span>

    ```console
    cd .\simple-feed-reader\SimpleFeedReader
    ```

3. <span data-ttu-id="42fea-124">Stellen Sie die Pakete wieder her, und erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="42fea-124">Restore the packages, and build the solution.</span></span>

    ```dotnetcli
    dotnet build
    ```

4. <span data-ttu-id="42fea-125">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="42fea-125">Run the app.</span></span>

    ```dotnetcli
    dotnet run
    ```

    ![Der Befehl „dotnet run“ wurde erfolgreich ausgeführt.](./media/deploying-to-app-service/dotnet-run.png)

5. <span data-ttu-id="42fea-127">Öffnen Sie einen Browser, und navigieren Sie zu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="42fea-127">Open a browser and navigate to `http://localhost:5000`.</span></span> <span data-ttu-id="42fea-128">Die App ermöglicht es Ihnen, eine Syndication-Feed-URL einzugeben oder einzufügen und eine Liste von Nachrichtenelementen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42fea-128">The app allows you to type or paste a syndication feed URL and view a list of news items.</span></span>

     ![Die App, die den Inhalt eines RSS-Feeds anzeigt.](./media/deploying-to-app-service/app-in-browser.png)

6. <span data-ttu-id="42fea-130">Wenn Sie sich vergewissert haben, dass die Anwendung ordnungsgemäß funktioniert, beenden Sie sie durch Drücken von **STRG** + **C** in der Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="42fea-130">Once you're satisfied the app is working correctly, shut it down by pressing **Ctrl**+**C** in the command shell.</span></span>

## <a name="create-the-azure-app-service-web-app"></a><span data-ttu-id="42fea-131">Erstellen der Azure App Service-Web-App</span><span class="sxs-lookup"><span data-stu-id="42fea-131">Create the Azure App Service Web App</span></span>

<span data-ttu-id="42fea-132">Zum Bereitstellen der App müssen Sie eine App Service-[Web-App](/azure/app-service/app-service-web-overview) erstellen.</span><span class="sxs-lookup"><span data-stu-id="42fea-132">To deploy the app, you'll need to create an App Service [Web App](/azure/app-service/app-service-web-overview).</span></span> <span data-ttu-id="42fea-133">Nachdem Sie die Web-App erstellt haben, können Sie mit Git von Ihrem lokalen Computer aus für sie bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="42fea-133">After creation of the Web App, you'll deploy to it from your local machine using Git.</span></span>

1. <span data-ttu-id="42fea-134">Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com/bash) an.</span><span class="sxs-lookup"><span data-stu-id="42fea-134">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash).</span></span> <span data-ttu-id="42fea-135">Hinweis: Wenn Sie sich zum ersten Mal anmelden, fordert Cloud Shell Sie auf, ein Speicherkonto für Konfigurationsdateien zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="42fea-135">Note: When you sign in for the first time, Cloud Shell prompts to create a storage account for configuration files.</span></span> <span data-ttu-id="42fea-136">Akzeptieren Sie die Standardwerte, oder geben Sie einen eindeutigen Namen an.</span><span class="sxs-lookup"><span data-stu-id="42fea-136">Accept the defaults or provide a unique name.</span></span>

2. <span data-ttu-id="42fea-137">Verwenden Sie Cloud Shell für die folgenden Schritte.</span><span class="sxs-lookup"><span data-stu-id="42fea-137">Use the Cloud Shell for the following steps.</span></span>

    <span data-ttu-id="42fea-138">a.</span><span class="sxs-lookup"><span data-stu-id="42fea-138">a.</span></span> <span data-ttu-id="42fea-139">Deklarieren Sie eine Variable, um den Namen Ihrer Web-App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="42fea-139">Declare a variable to store your web app's name.</span></span> <span data-ttu-id="42fea-140">Der Name muss eindeutig sein, damit er in der Standard-URL verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="42fea-140">The name must be unique to be used in the default URL.</span></span> <span data-ttu-id="42fea-141">Die Verwendung der Bash-Funktion `$RANDOM` zum Erstellen des Namens sorgt für Eindeutigkeit und Ergebnisse im Format `webappname99999`.</span><span class="sxs-lookup"><span data-stu-id="42fea-141">Using the `$RANDOM` Bash function to construct the name guarantees uniqueness and results in the format `webappname99999`.</span></span>

    ```console
    webappname=mywebapp$RANDOM
    ```

    <span data-ttu-id="42fea-142">b.</span><span class="sxs-lookup"><span data-stu-id="42fea-142">b.</span></span> <span data-ttu-id="42fea-143">Erstellen Sie eine Ressourcengruppe.</span><span class="sxs-lookup"><span data-stu-id="42fea-143">Create a resource group.</span></span> <span data-ttu-id="42fea-144">Ressourcengruppen bieten eine Möglichkeit, Azure-Ressourcen zu aggregieren und als Gruppe zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="42fea-144">Resource groups provide a means to aggregate Azure resources to be managed as a group.</span></span>

    ```azurecli
    az group create --location centralus --name AzureTutorial
    ```

    <span data-ttu-id="42fea-145">Der Befehl `az` ruft die [Azure-Befehlszeilenschnittstelle](/cli/azure/) auf.</span><span class="sxs-lookup"><span data-stu-id="42fea-145">The `az` command invokes the [Azure CLI](/cli/azure/).</span></span> <span data-ttu-id="42fea-146">Die Befehlszeilenschnittstelle kann lokal ausgeführt werden, aber die Verwendung in Cloud Shell spart Zeit und Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="42fea-146">The CLI can be run locally, but using it in the Cloud Shell saves time and configuration.</span></span>

    <span data-ttu-id="42fea-147">c.</span><span class="sxs-lookup"><span data-stu-id="42fea-147">c.</span></span> <span data-ttu-id="42fea-148">Erstellen Sie einen App Service-Plan im S1-Tarif.</span><span class="sxs-lookup"><span data-stu-id="42fea-148">Create an App Service plan in the S1 tier.</span></span> <span data-ttu-id="42fea-149">Ein App Service-Plan ist eine Gruppierung von Web-Apps, die denselben Tarif aufweisen.</span><span class="sxs-lookup"><span data-stu-id="42fea-149">An App Service plan is a grouping of web apps that share the same pricing tier.</span></span> <span data-ttu-id="42fea-150">Der S1-Tarif ist nicht kostenlos, aber er ist für das Stagingslots-Feature erforderlich.</span><span class="sxs-lookup"><span data-stu-id="42fea-150">The S1 tier isn't free, but it's required for the staging slots feature.</span></span>

    ```azurecli
    az appservice plan create --name $webappname --resource-group AzureTutorial --sku S1
    ```

    <span data-ttu-id="42fea-151">d.</span><span class="sxs-lookup"><span data-stu-id="42fea-151">d.</span></span> <span data-ttu-id="42fea-152">Erstellen Sie die Web-App-Ressource mit dem App Service-Plan in derselben Ressourcengruppe.</span><span class="sxs-lookup"><span data-stu-id="42fea-152">Create the web app resource using the App Service plan in the same resource group.</span></span>

    ```azurecli
    az webapp create --name $webappname --resource-group AzureTutorial --plan $webappname
    ```

    <span data-ttu-id="42fea-153">e.</span><span class="sxs-lookup"><span data-stu-id="42fea-153">e.</span></span> <span data-ttu-id="42fea-154">Legen Sie die Anmeldeinformationen für die Bereitstellung fest.</span><span class="sxs-lookup"><span data-stu-id="42fea-154">Set the deployment credentials.</span></span> <span data-ttu-id="42fea-155">Diese Anmeldeinformationen für die Bereitstellung gelten für alle Web-Apps in Ihrem Abonnement.</span><span class="sxs-lookup"><span data-stu-id="42fea-155">These deployment credentials apply to all the web apps in your subscription.</span></span> <span data-ttu-id="42fea-156">Verwenden Sie keine Sonderzeichen im Benutzernamen.</span><span class="sxs-lookup"><span data-stu-id="42fea-156">Don't use special characters in the user name.</span></span>

    ```azurecli
    az webapp deployment user set --user-name REPLACE_WITH_USER_NAME --password REPLACE_WITH_PASSWORD
    ```

    <span data-ttu-id="42fea-157">f.</span><span class="sxs-lookup"><span data-stu-id="42fea-157">f.</span></span> <span data-ttu-id="42fea-158">Konfigurieren Sie die Web-App so, dass sie Bereitstellungen vom lokalen Git akzeptiert und die *URL für die Git-Bereitstellung* anzeigt.</span><span class="sxs-lookup"><span data-stu-id="42fea-158">Configure the web app to accept deployments from local Git and display the *Git deployment URL*.</span></span> <span data-ttu-id="42fea-159">**Notieren Sie diese URL zur späteren Bezugnahme**.</span><span class="sxs-lookup"><span data-stu-id="42fea-159">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --query url --output tsv)
    ```

    <span data-ttu-id="42fea-160">g.</span><span class="sxs-lookup"><span data-stu-id="42fea-160">g.</span></span> <span data-ttu-id="42fea-161">Zeigen Sie die *URL der Web-App* an.</span><span class="sxs-lookup"><span data-stu-id="42fea-161">Display the *web app URL*.</span></span> <span data-ttu-id="42fea-162">Navigieren Sie zu dieser URL, um die leere Web-App anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42fea-162">Browse to this URL to see the blank web app.</span></span> <span data-ttu-id="42fea-163">**Notieren Sie diese URL zur späteren Bezugnahme**.</span><span class="sxs-lookup"><span data-stu-id="42fea-163">**Note this URL for reference later**.</span></span>

    ```console
    echo Web app URL: http://$webappname.azurewebsites.net
    ```

3. <span data-ttu-id="42fea-164">Navigieren Sie über eine Befehlsshell auf Ihrem lokalen Computer zum Projektordner der Web-App (z B. `.\simple-feed-reader\SimpleFeedReader`).</span><span class="sxs-lookup"><span data-stu-id="42fea-164">Using a command shell on your local machine, navigate to the web app's project folder (for example, `.\simple-feed-reader\SimpleFeedReader`).</span></span> <span data-ttu-id="42fea-165">Führen Sie die folgenden Befehle aus, um Git so einzurichten, dass es einen Push für die Bereitstellungs-URL ausführt:</span><span class="sxs-lookup"><span data-stu-id="42fea-165">Execute the following commands to set up Git to push to the deployment URL:</span></span>

    <span data-ttu-id="42fea-166">a.</span><span class="sxs-lookup"><span data-stu-id="42fea-166">a.</span></span> <span data-ttu-id="42fea-167">Fügen Sie die Remote-URL zum lokalen Repository hinzu.</span><span class="sxs-lookup"><span data-stu-id="42fea-167">Add the remote URL to the local repository.</span></span>

    ```console
    git remote add azure-prod GIT_DEPLOYMENT_URL
    ```

    <span data-ttu-id="42fea-168">b.</span><span class="sxs-lookup"><span data-stu-id="42fea-168">b.</span></span> <span data-ttu-id="42fea-169">Pushen Sie den lokalen *Masterbranch* zum Remote-*Masterbranch* von *azure-prod*.</span><span class="sxs-lookup"><span data-stu-id="42fea-169">Push the local *master* branch to the *azure-prod* remote's *master* branch.</span></span>

    ```console
    git push azure-prod master
    ```

    <span data-ttu-id="42fea-170">Sie werden aufgefordert, die zuvor erstellten Anmeldeinformationen für die Bereitstellung einzugeben.</span><span class="sxs-lookup"><span data-stu-id="42fea-170">You'll be prompted for the deployment credentials you created earlier.</span></span> <span data-ttu-id="42fea-171">Beobachten Sie die Ausgabe in der Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="42fea-171">Observe the output in the command shell.</span></span> <span data-ttu-id="42fea-172">Azure erstellt die ASP.NET Core-App remote.</span><span class="sxs-lookup"><span data-stu-id="42fea-172">Azure builds the ASP.NET Core app remotely.</span></span>

4. <span data-ttu-id="42fea-173">Navigieren Sie in einem Browser zu der *Web-App-URL* und beachten Sie, dass die App erstellt und bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="42fea-173">In a browser, navigate to the *Web app URL* and note the app has been built and deployed.</span></span> <span data-ttu-id="42fea-174">Weitere Änderungen können mit `git commit` für das lokale Git-Repository committet werden.</span><span class="sxs-lookup"><span data-stu-id="42fea-174">Additional changes can be committed to the local Git repository with `git commit`.</span></span> <span data-ttu-id="42fea-175">Diese Änderungen werden mit dem vorhergehenden `git push`-Befehl nach Azure gepusht.</span><span class="sxs-lookup"><span data-stu-id="42fea-175">These changes are pushed to Azure with the preceding `git push` command.</span></span>

## <a name="deployment-with-visual-studio"></a><span data-ttu-id="42fea-176">Bereitstellung mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="42fea-176">Deployment with Visual Studio</span></span>

> <span data-ttu-id="42fea-177">*Hinweis: Dieser Abschnitt gilt nur für Windows. Linux- und macOS-Benutzer sollten die unter Schritt 2 beschriebene Änderung vornehmen. Speichern Sie die Datei, und übertragen Sie die Änderung mit `git commit` in das lokale Repository. Pushen Sie abschließend die Änderung mit `git push`, wie im ersten Abschnitt.*</span><span class="sxs-lookup"><span data-stu-id="42fea-177">*Note: This section applies to Windows only. Linux and macOS users should make the change described in step 2 below. Save the file, and commit the change to the local repository with `git commit`. Finally, push the change with `git push`, as in the first section.*</span></span>

<span data-ttu-id="42fea-178">Die App wurde bereits von der Befehlsshell aus bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-178">The app has already been deployed from the command shell.</span></span> <span data-ttu-id="42fea-179">Lassen Sie uns die integrierten Tools von Visual Studio verwenden, um ein Update für die App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="42fea-179">Let's use Visual Studio's integrated tools to deploy an update to the app.</span></span> <span data-ttu-id="42fea-180">Im Hintergrund erreicht Visual Studio dasselbe wie das Befehlszeilentool, jedoch innerhalb der vertrauten Benutzeroberfläche von Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="42fea-180">Behind the scenes, Visual Studio accomplishes the same thing as the command line tooling, but within Visual Studio's familiar UI.</span></span>

1. <span data-ttu-id="42fea-181">Öffnen Sie *SimpleFeedReader.sln* in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="42fea-181">Open *SimpleFeedReader.sln* in Visual Studio.</span></span>
2. <span data-ttu-id="42fea-182">Öffnen Sie *Pages\Index.cshtml* im Projektmappen-Explorer.</span><span class="sxs-lookup"><span data-stu-id="42fea-182">In Solution Explorer, open *Pages\Index.cshtml*.</span></span> <span data-ttu-id="42fea-183">Ändern Sie `<h2>Simple Feed Reader</h2>` in `<h2>Simple Feed Reader - V2</h2>`.</span><span class="sxs-lookup"><span data-stu-id="42fea-183">Change `<h2>Simple Feed Reader</h2>` to `<h2>Simple Feed Reader - V2</h2>`.</span></span>
3. <span data-ttu-id="42fea-184">Drücken Sie **STRG** + **UMSCHALT** + **B**, um die App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="42fea-184">Press **Ctrl**+**Shift**+**B** to build the app.</span></span>
4. <span data-ttu-id="42fea-185">Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="42fea-185">In Solution Explorer, right-click on the project and click **Publish**.</span></span>

    ![Screenshot mit Rechtsklick und anschließendem Veröffentlichen](./media/deploying-to-app-service/publish.png)
5. <span data-ttu-id="42fea-187">Visual Studio kann eine neue App Service-Ressource erstellen, aber dieses Update wird über die bestehende Bereitstellung veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="42fea-187">Visual Studio can create a new App Service resource, but this update will be published over the existing deployment.</span></span> <span data-ttu-id="42fea-188">Wählen Sie im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** aus der linken Liste und dann die Option **Vorhandene auswählen** aus.</span><span class="sxs-lookup"><span data-stu-id="42fea-188">In the **Pick a publish target** dialog, select **App Service** from the list on the left, and then select **Select Existing**.</span></span> <span data-ttu-id="42fea-189">Klicken Sie auf **Veröffentlichen**.</span><span class="sxs-lookup"><span data-stu-id="42fea-189">Click **Publish**.</span></span>
6. <span data-ttu-id="42fea-190">Bestätigen Sie im Dialogfeld **App Service**, dass das Microsoft- oder Organisationskonto, das zum Erstellen Ihres Azure-Abonnements verwendet wurde, oben rechts angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="42fea-190">In the **App Service** dialog, confirm that the Microsoft or Organizational account used to create your Azure subscription is displayed in the upper right.</span></span> <span data-ttu-id="42fea-191">Wenn dies nicht der Fall ist, klicken Sie auf die Dropdownliste und fügen Sie es hinzu.</span><span class="sxs-lookup"><span data-stu-id="42fea-191">If it's not, click the drop-down and add it.</span></span>
7. <span data-ttu-id="42fea-192">Bestätigen Sie, dass das richtige Azure-**Abonnement** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="42fea-192">Confirm that the correct Azure **Subscription** is selected.</span></span> <span data-ttu-id="42fea-193">Wählen Sie für **Ansicht** die Option **Ressourcengruppe** aus.</span><span class="sxs-lookup"><span data-stu-id="42fea-193">For **View**, select **Resource Group**.</span></span> <span data-ttu-id="42fea-194">Erweitern Sie die Ressourcengruppe **AzureTutorial**, und wählen Sie dann die vorhandene Web-App aus.</span><span class="sxs-lookup"><span data-stu-id="42fea-194">Expand the **AzureTutorial** resource group and then select the existing web app.</span></span> <span data-ttu-id="42fea-195">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="42fea-195">Click **OK**.</span></span>

    ![Screenshot mit Dialogfeld zum Veröffentlichen von App Service](./media/deploying-to-app-service/publish-dialog.png)

<span data-ttu-id="42fea-197">Visual Studio erstellt die App für Azure und stellt sie bereit.</span><span class="sxs-lookup"><span data-stu-id="42fea-197">Visual Studio builds and deploys the app to Azure.</span></span> <span data-ttu-id="42fea-198">Navigieren Sie zur URL der Web-App.</span><span class="sxs-lookup"><span data-stu-id="42fea-198">Browse to the web app URL.</span></span> <span data-ttu-id="42fea-199">Überprüfen Sie, ob die Änderung des `<h2>`-Elements aktiv ist.</span><span class="sxs-lookup"><span data-stu-id="42fea-199">Validate that the `<h2>` element modification is live.</span></span>

![Die App mit dem geänderten Titel.](./media/deploying-to-app-service/app-v2.png)

## <a name="deployment-slots"></a><span data-ttu-id="42fea-201">Bereitstellungsslots</span><span class="sxs-lookup"><span data-stu-id="42fea-201">Deployment slots</span></span>

<span data-ttu-id="42fea-202">Bereitstellungsslots unterstützen das Staging von Änderungen, ohne die in der Produktion ausgeführte App zu beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="42fea-202">Deployment slots support the staging of changes without impacting the app running in production.</span></span> <span data-ttu-id="42fea-203">Sobald die bereitgestellte Version der App von einem Qualitätssicherungsteam überprüft wurde, können die Produktions- und Stagingslots ausgetauscht werden.</span><span class="sxs-lookup"><span data-stu-id="42fea-203">Once the staged version of the app is validated by a quality assurance team, the production and staging slots can be swapped.</span></span> <span data-ttu-id="42fea-204">Die im Staging befindliche App wird auf diese Weise in die Produktionsumgebung heraufgestuft.</span><span class="sxs-lookup"><span data-stu-id="42fea-204">The app in staging is promoted to production in this manner.</span></span> <span data-ttu-id="42fea-205">In den folgenden Schritten wird ein Stagingslot erstellt, dann werden einige Änderungen daran vorgenommen und der Stagingslot nach der Überprüfung mit der Produktionsumgebung ausgetauscht.</span><span class="sxs-lookup"><span data-stu-id="42fea-205">The following steps create a staging slot, deploy some changes to it, and swap the staging slot with production after verification.</span></span>

1. <span data-ttu-id="42fea-206">Melden Sie sich bei [Azure Cloud Shell](https://shell.azure.com/bash) an, wenn Sie nicht bereits angemeldet sind.</span><span class="sxs-lookup"><span data-stu-id="42fea-206">Sign in to the [Azure Cloud Shell](https://shell.azure.com/bash), if not already signed in.</span></span>
2. <span data-ttu-id="42fea-207">Erstellen Sie den Stagingslot.</span><span class="sxs-lookup"><span data-stu-id="42fea-207">Create the staging slot.</span></span>

    <span data-ttu-id="42fea-208">a.</span><span class="sxs-lookup"><span data-stu-id="42fea-208">a.</span></span> <span data-ttu-id="42fea-209">Erstellen Sie einen Bereitstellungsslot mit dem Namen *Staging*.</span><span class="sxs-lookup"><span data-stu-id="42fea-209">Create a deployment slot with the name *staging*.</span></span>

    ```azurecli
    az webapp deployment slot create --name $webappname --resource-group AzureTutorial --slot staging
    ```

    <span data-ttu-id="42fea-210">b.</span><span class="sxs-lookup"><span data-stu-id="42fea-210">b.</span></span> <span data-ttu-id="42fea-211">Konfigurieren Sie den Stagingslot so, dass die Bereitstellung aus dem lokalen Git verwendet wird, und rufen Sie die Bereitstellungs-URL für das **Staging** ab.</span><span class="sxs-lookup"><span data-stu-id="42fea-211">Configure the staging slot to use deployment from local Git and get the **staging** deployment URL.</span></span> <span data-ttu-id="42fea-212">**Notieren Sie diese URL zur späteren Bezugnahme**.</span><span class="sxs-lookup"><span data-stu-id="42fea-212">**Note this URL for reference later**.</span></span>

    ```azurecli
    echo Git deployment URL for staging: $(az webapp deployment source config-local-git --name $webappname --resource-group AzureTutorial --slot staging --query url --output tsv)
    ```

    <span data-ttu-id="42fea-213">c.</span><span class="sxs-lookup"><span data-stu-id="42fea-213">c.</span></span> <span data-ttu-id="42fea-214">Zeigen Sie die URL des Stagingslots an.</span><span class="sxs-lookup"><span data-stu-id="42fea-214">Display the staging slot's URL.</span></span> <span data-ttu-id="42fea-215">Navigieren Sie zu der URL, um den leeren Stagingslot anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42fea-215">Browse to the URL to see the empty staging slot.</span></span> <span data-ttu-id="42fea-216">**Notieren Sie diese URL zur späteren Bezugnahme**.</span><span class="sxs-lookup"><span data-stu-id="42fea-216">**Note this URL for reference later**.</span></span>

    ```console
    echo Staging web app URL: http://$webappname-staging.azurewebsites.net
    ```

3. <span data-ttu-id="42fea-217">Ändern Sie in einem Texteditor oder in Visual Studio erneut *Pages/Index.cshtml*, sodass das Element `<h2>` den Wert `<h2>Simple Feed Reader - V3</h2>` aufweist, und speichern Sie dann die Datei.</span><span class="sxs-lookup"><span data-stu-id="42fea-217">In a text editor or Visual Studio, modify *Pages/Index.cshtml* again so that the `<h2>` element reads `<h2>Simple Feed Reader - V3</h2>` and save the file.</span></span>

4. <span data-ttu-id="42fea-218">Committen Sie die Datei an das lokale Git-Repository, indem Sie entweder die Seite **Änderungen** auf der Registerkarte *Team Explorer* von Visual Studio verwenden, oder indem Sie Folgendes über die Befehlsshell des lokalen Computers eingeben:</span><span class="sxs-lookup"><span data-stu-id="42fea-218">Commit the file to the local Git repository, using either the **Changes** page in Visual Studio's *Team Explorer* tab, or by entering the following using the local machine's command shell:</span></span>

    ```console
    git commit -a -m "upgraded to V3"
    ```

5. <span data-ttu-id="42fea-219">Fügen Sie über die Befehlsshell des lokalen Computers die URL der Stagingbereitstellung als Git-Remote ein, und pushen Sie die committeten Änderungen:</span><span class="sxs-lookup"><span data-stu-id="42fea-219">Using the local machine's command shell, add the staging deployment URL as a Git remote and push the committed changes:</span></span>

    <span data-ttu-id="42fea-220">a.</span><span class="sxs-lookup"><span data-stu-id="42fea-220">a.</span></span> <span data-ttu-id="42fea-221">Fügen Sie die Remote-URL für das Staging zum lokalen Git-Repository hinzu.</span><span class="sxs-lookup"><span data-stu-id="42fea-221">Add the remote URL for staging to the local Git repository.</span></span>

    ```console
    git remote add azure-staging <Git_staging_deployment_URL>
    ```

    <span data-ttu-id="42fea-222">b.</span><span class="sxs-lookup"><span data-stu-id="42fea-222">b.</span></span> <span data-ttu-id="42fea-223">Pushen Sie den lokalen *Masterbranch* zum Remote-*Masterbranch* von *azure-staging*.</span><span class="sxs-lookup"><span data-stu-id="42fea-223">Push the local *master* branch to the *azure-staging* remote's *master* branch.</span></span>

    ```console
    git push azure-staging master
    ```

    <span data-ttu-id="42fea-224">Warten Sie, während Azure die App erstellt und bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-224">Wait while Azure builds and deploys the app.</span></span>

6. <span data-ttu-id="42fea-225">Öffnen Sie zwei Browserfenster, um zu überprüfen, ob V3 für den Stagingslot bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="42fea-225">To verify that V3 has been deployed to the staging slot, open two browser windows.</span></span> <span data-ttu-id="42fea-226">Navigieren Sie in einem Fenster zur ursprünglichen URL der Web-App.</span><span class="sxs-lookup"><span data-stu-id="42fea-226">In one window, navigate to the original web app URL.</span></span> <span data-ttu-id="42fea-227">Navigieren Sie im anderen Fenster zur URL der Staging-Web-App.</span><span class="sxs-lookup"><span data-stu-id="42fea-227">In the other window, navigate to the staging web app URL.</span></span> <span data-ttu-id="42fea-228">Die URL der Produktionsumgebung liefert V2 der App.</span><span class="sxs-lookup"><span data-stu-id="42fea-228">The production URL serves V2 of the app.</span></span> <span data-ttu-id="42fea-229">Die URL der Stagingumgebung liefert V3 der App.</span><span class="sxs-lookup"><span data-stu-id="42fea-229">The staging URL serves V3 of the app.</span></span>

    ![Screenshot zum Vergleich der Browserfenster](./media/deploying-to-app-service/ready-to-swap.png)

7. <span data-ttu-id="42fea-231">Tauschen Sie in der Cloud Shell den überprüften/vorbereiteten Stagingslot mit der Produktionsumgebung.</span><span class="sxs-lookup"><span data-stu-id="42fea-231">In the Cloud Shell, swap the verified/warmed-up staging slot into production.</span></span>

    ```azurecli
    az webapp deployment slot swap --name $webappname --resource-group AzureTutorial --slot staging
    ```

8. <span data-ttu-id="42fea-232">Überprüfen Sie, ob der Austausch erfolgt ist, indem Sie die beiden Browserfenster aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="42fea-232">Verify that the swap occurred by refreshing the two browser windows.</span></span>

    ![Vergleichen der Browserfenster nach dem Austausch](./media/deploying-to-app-service/swapped.png)

## <a name="summary"></a><span data-ttu-id="42fea-234">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="42fea-234">Summary</span></span>

<span data-ttu-id="42fea-235">In diesem Abschnitt wurden die folgenden Aufgaben ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="42fea-235">In this section, the following tasks were completed:</span></span>

* <span data-ttu-id="42fea-236">Die Beispiel-App wurde heruntergeladen und erstellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-236">Downloaded and built the sample app.</span></span>
* <span data-ttu-id="42fea-237">Eine Azure App Service-Web-App wurde mit der Azure Cloud Shell erstellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-237">Created an Azure App Service Web App using the Azure Cloud Shell.</span></span>
* <span data-ttu-id="42fea-238">Die Beispiel-App wurde mit Git in Azure bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-238">Deployed the sample app to Azure using Git.</span></span>
* <span data-ttu-id="42fea-239">Eine Änderung für die App wurde mit Visual Studio bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-239">Deployed a change to the app using Visual Studio.</span></span>
* <span data-ttu-id="42fea-240">Ein Stagingslot wurde zur Web-App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="42fea-240">Added a staging slot to the web app.</span></span>
* <span data-ttu-id="42fea-241">Es wurde eine Aktualisierung des Stagingslots bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="42fea-241">Deployed an update to the staging slot.</span></span>
* <span data-ttu-id="42fea-242">Die Staging- und Produktionsslots wurden ausgetauscht.</span><span class="sxs-lookup"><span data-stu-id="42fea-242">Swapped the staging and production slots.</span></span>

<span data-ttu-id="42fea-243">Im nächsten Abschnitt erfahren Sie, wie Sie eine DevOps-Pipeline mit Azure Pipelines erstellen können.</span><span class="sxs-lookup"><span data-stu-id="42fea-243">In the next section, you'll learn how to build a DevOps pipeline with Azure Pipelines.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="42fea-244">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="42fea-244">Additional reading</span></span>

* [<span data-ttu-id="42fea-245">Web-Apps – Übersicht</span><span class="sxs-lookup"><span data-stu-id="42fea-245">Web Apps overview</span></span>](/azure/app-service/app-service-web-overview)
* [<span data-ttu-id="42fea-246">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="42fea-246">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [<span data-ttu-id="42fea-247">Konfigurieren von Anmeldeinformationen für die Azure App Service-Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="42fea-247">Configure deployment credentials for Azure App Service</span></span>](/azure/app-service/app-service-deployment-credentials)
* [<span data-ttu-id="42fea-248">Einrichten von Stagingumgebungen in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="42fea-248">Set up staging environments in Azure App Service</span></span>](/azure/app-service/web-sites-staged-publishing)
