---
title: Continuous Deployment in Azure mit Visual Studio und Git mit ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit Visual Studio eine ASP.NET Core-Web-App erstellen und sie unter Verwendung von Git für Continuous Deployment in Azure App Service bereitstellen.
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 12/06/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: host-and-deploy/azure-apps/azure-continuous-deployment
ms.openlocfilehash: 1485e1a97e51bb4d12d0d74ca1550e5cf18258d8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053786"
---
# <a name="continuous-deployment-to-azure-with-visual-studio-and-git-with-aspnet-core"></a><span data-ttu-id="fd24c-103">Continuous Deployment in Azure mit Visual Studio und Git mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd24c-103">Continuous deployment to Azure with Visual Studio and Git with ASP.NET Core</span></span>

<span data-ttu-id="fd24c-104">Von [Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="fd24c-104">By [Erik Reitan](https://github.com/Erikre)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="fd24c-105">Dieses Tutorial zeigt, wie mit Visual Studio eine ASP.NET Core-Web-App erstellt und aus Visual Studio mithilfe von Continuous Deployment in Azure App Service bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="fd24c-105">This tutorial shows how to create an ASP.NET Core web app using Visual Studio and deploy it from Visual Studio to Azure App Service using continuous deployment.</span></span>

<span data-ttu-id="fd24c-106">Siehe auch [Erstellen einer ersten Pipeline mit Azure Pipelines](/azure/devops/pipelines/get-started-yaml). Diese Anleitung zeigt, wie Sie einen CD-Workflow (Continuous Delivery) für [Azure App Service](/azure/app-service/app-service-web-overview) mit Azure DevOps Services konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="fd24c-106">See also [Create your first pipeline with Azure Pipelines](/azure/devops/pipelines/get-started-yaml), which shows how to configure a continuous delivery (CD) workflow for [Azure App Service](/azure/app-service/app-service-web-overview) using Azure DevOps Services.</span></span> <span data-ttu-id="fd24c-107">Azure Pipelines (ein Azure DevOps Services-Dienst) vereinfacht das Einrichten einer robusten Bereitstellungspipeline zur Veröffentlichung von Updates für Apps, die in Azure App Service gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="fd24c-107">Azure Pipelines (an Azure DevOps Services service) simplifies setting up a robust deployment pipeline to publish updates for apps hosted in Azure App Service.</span></span> <span data-ttu-id="fd24c-108">Die Pipeline kann im Azure-Portal für die folgenden Aufgaben konfiguriert werden: Erstellen von Builds, Ausführen von Tests, Bereitstellen in einem Stagingslot und anschließendes Bereitstellen in der Produktion.</span><span class="sxs-lookup"><span data-stu-id="fd24c-108">The pipeline can be configured from the Azure portal to build, run tests, deploy to a staging slot, and then deploy to production.</span></span>

> [!NOTE]
> <span data-ttu-id="fd24c-109">Für dieses Tutorial ist ein Microsoft Azure-Konto erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fd24c-109">To complete this tutorial, a Microsoft Azure account is required.</span></span> <span data-ttu-id="fd24c-110">Zum Abrufen eines Kontos können Sie die [Leistungen für MSDN-Abonnenten aktivieren](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) oder sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span><span class="sxs-lookup"><span data-stu-id="fd24c-110">To obtain an account, [activate MSDN subscriber benefits](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A261C142F) or [sign up for a free trial](https://azure.microsoft.com/free/dotnet/?WT.mc_id=A261C142F).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fd24c-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="fd24c-111">Prerequisites</span></span>

<span data-ttu-id="fd24c-112">Dieses Tutorial setzt voraus, dass folgende Software installiert ist:</span><span class="sxs-lookup"><span data-stu-id="fd24c-112">This tutorial assumes the following software is installed:</span></span>

* [<span data-ttu-id="fd24c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fd24c-113">Visual Studio</span></span>](https://visualstudio.microsoft.com)
* [!INCLUDE [](~/includes/net-core-sdk-download-link.md)]
* <span data-ttu-id="fd24c-114">[Git](https://git-scm.com/downloads) für Windows</span><span class="sxs-lookup"><span data-stu-id="fd24c-114">[Git](https://git-scm.com/downloads) for Windows</span></span>

## <a name="create-an-aspnet-core-web-app"></a><span data-ttu-id="fd24c-115">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="fd24c-115">Create an ASP.NET Core web app</span></span>

1. <span data-ttu-id="fd24c-116">Starten Sie Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="fd24c-116">Start Visual Studio.</span></span>

1. <span data-ttu-id="fd24c-117">Klicken Sie im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-117">From the **File** menu, select **New** > **Project**.</span></span>

1. <span data-ttu-id="fd24c-118">Wählen Sie die Projektvorlage **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-118">Select the **ASP.NET Core Web Application** project template.</span></span> <span data-ttu-id="fd24c-119">Sie wird unter **Installierte** > **Vorlagen** > **Visual C#**  >  **.NET Core** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-119">It appears under **Installed** > **Templates** > **Visual C#** > **.NET Core**.</span></span> <span data-ttu-id="fd24c-120">Benennen Sie das Projekt mit `SampleWebAppDemo`.</span><span class="sxs-lookup"><span data-stu-id="fd24c-120">Name the project `SampleWebAppDemo`.</span></span> <span data-ttu-id="fd24c-121">Wählen Sie die Option **Neues Git-Repository erstellen** aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-121">Select the **Create new Git repository** option and click **OK**.</span></span>

   ![Dialogfeld "Neues Projekt"](azure-continuous-deployment/_static/01-new-project.png)

1. <span data-ttu-id="fd24c-123">Wählen Sie im Dialogfeld **Neues ASP.NET Core-Projekt** die ASP.NET Core-Vorlage **Leer** aus, und klicken Sie dann auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-123">In the **New ASP.NET Core Project** dialog, select the ASP.NET Core **Empty** template, then click **OK**.</span></span>

   ![Dialogfeld „Neues ASP.NET Core-Projekt“](azure-continuous-deployment/_static/02-web-site-template.png)

> [!NOTE]
> <span data-ttu-id="fd24c-125">Das neueste Release von .NET Core ist 2.0.</span><span class="sxs-lookup"><span data-stu-id="fd24c-125">The most recent release of .NET Core is 2.0.</span></span>

### <a name="running-the-web-app-locally"></a><span data-ttu-id="fd24c-126">Lokales Ausführen der Web-App</span><span class="sxs-lookup"><span data-stu-id="fd24c-126">Running the web app locally</span></span>

1. <span data-ttu-id="fd24c-127">Sobald Visual Studio das Erstellen der App abgeschlossen hat, führen Sie die App durch Auswählen von **Debuggen** > **Debuggen starten** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-127">Once Visual Studio finishes creating the app, run the app by selecting **Debug** > **Start Debugging**.</span></span> <span data-ttu-id="fd24c-128">Drücken Sie alternativ die Taste **F5**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-128">As an alternative, press **F5**.</span></span>

   <span data-ttu-id="fd24c-129">Das Initialisieren von Visual Studio und der neuen App dauert möglicherweise einen Moment.</span><span class="sxs-lookup"><span data-stu-id="fd24c-129">It may take time to initialize Visual Studio and the new app.</span></span> <span data-ttu-id="fd24c-130">Nach Abschluss der Initialisierung wird im Browser die ausgeführte App angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-130">Once it's complete, the browser shows the running app.</span></span>

   ![Browserfenster mit der ausgeführten Anwendung, in der „Hello World!“ angezeigt wird](azure-continuous-deployment/_static/04-browser-runapp.png)

1. <span data-ttu-id="fd24c-132">Schließen Sie nach der Überprüfung der ausgeführten Web-App den Browser, und klicken Sie in der Symbolleiste von Visual Studio auf das Symbol „Debuggen beenden“, um die App zu beenden.</span><span class="sxs-lookup"><span data-stu-id="fd24c-132">After reviewing the running Web app, close the browser and select the "Stop Debugging" icon in the toolbar of Visual Studio to stop the app.</span></span>

## <a name="create-a-web-app-in-the-azure-portal"></a><span data-ttu-id="fd24c-133">Erstellen einer Web-App im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="fd24c-133">Create a web app in the Azure Portal</span></span>

<span data-ttu-id="fd24c-134">Mit den folgenden Schritten wird eine Web-App im Azure-Portal erstellt:</span><span class="sxs-lookup"><span data-stu-id="fd24c-134">The following steps create a web app in the Azure Portal:</span></span>

1. <span data-ttu-id="fd24c-135">Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.</span><span class="sxs-lookup"><span data-stu-id="fd24c-135">Log in to the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="fd24c-136">Klicken Sie links oben auf der Portalschnittstelle auf die Option **NEU**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-136">Select **NEW** at the top left of the portal interface.</span></span>

1. <span data-ttu-id="fd24c-137">Wählen Sie **Web + Mobil** > **Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-137">Select **Web + Mobile** > **Web App**.</span></span>

   ![Microsoft Azure-Portal: Schaltfläche „Neu“: „Web und mobil“ unter „Marketplace“: Schaltfläche „Web App“ unter „Ausgewählte Apps“](azure-continuous-deployment/_static/05-azure-newwebapp.png)

1. <span data-ttu-id="fd24c-139">Geben Sie auf dem Blatt **Web-App** für **App Service-Name** einen eindeutigen Wert ein.</span><span class="sxs-lookup"><span data-stu-id="fd24c-139">In the **Web App** blade, enter a unique value for the **App Service Name**.</span></span>

   ![Blatt „Web-App“](azure-continuous-deployment/_static/06-azure-newappblade.png)

   > [!NOTE]
   > <span data-ttu-id="fd24c-141">Der Name unter **App Service-Name** muss eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="fd24c-141">The **App Service Name** name must be unique.</span></span> <span data-ttu-id="fd24c-142">Wenn der Name angegeben ist, erzwingt das Portal diese Regel.</span><span class="sxs-lookup"><span data-stu-id="fd24c-142">The portal enforces this rule when the name is provided.</span></span> <span data-ttu-id="fd24c-143">Wenn ein anderer Wert eingegeben wird, müssen Sie diesen Wert bei jedem Vorkommen von **SampleWebAppDemo** in diesem Tutorial ersetzen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-143">If providing a different value, substitute that value for each occurrence of **SampleWebAppDemo** in this tutorial.</span></span>

   <span data-ttu-id="fd24c-144">Wählen Sie außerdem auf dem Blatt **Web-App** einen vorhandenen **App Service-Plan/Standort** aus, oder erstellen Sie einen neuen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-144">Also in the **Web App** blade, select an existing **App Service Plan/Location** or create a new one.</span></span> <span data-ttu-id="fd24c-145">Wenn Sie einen neuen Plan erstellen, wählen Sie den Tarif, den Standort und andere Optionen aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-145">If creating a new plan, select the pricing tier, location, and other options.</span></span> <span data-ttu-id="fd24c-146">Weitere Informationen zu App Service-Plänen finden Sie unter [Azure App Service-Pläne – Detaillierte Übersicht](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span><span class="sxs-lookup"><span data-stu-id="fd24c-146">For more information on App Service plans, see [Azure App Service plans in-depth overview](/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).</span></span>

1. <span data-ttu-id="fd24c-147">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-147">Select **Create**.</span></span> <span data-ttu-id="fd24c-148">Azure stellt die Web-App bereit und startet sie.</span><span class="sxs-lookup"><span data-stu-id="fd24c-148">Azure will provision and start the web app.</span></span>

   ![Azure-Portal: SampleWebAppDemo01, Blatt „Zusammenfassung“](azure-continuous-deployment/_static/07-azure-webappblade.png)

## <a name="enable-git-publishing-for-the-new-web-app"></a><span data-ttu-id="fd24c-150">Aktivieren der Git-Veröffentlichung für die neue Web-App</span><span class="sxs-lookup"><span data-stu-id="fd24c-150">Enable Git publishing for the new web app</span></span>

<span data-ttu-id="fd24c-151">Bei Git handelt es sich um ein verteiltes Versionskontrollsystem, mit dem Sie eine Azure App Service-Web-App bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="fd24c-151">Git is a distributed version control system that can be used to deploy an Azure App Service web app.</span></span> <span data-ttu-id="fd24c-152">Web-App-Code wird in einem lokalen Git-Repository gespeichert, und der Code wird durch Übertragung in ein Remoterepository in Azure bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-152">Web app code is stored in a local Git repository, and the code is deployed to Azure by pushing to a remote repository.</span></span>

1. <span data-ttu-id="fd24c-153">Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.</span><span class="sxs-lookup"><span data-stu-id="fd24c-153">Log into the [Azure Portal](https://portal.azure.com).</span></span>

1. <span data-ttu-id="fd24c-154">Klicken Sie auf **App-Dienste** , um eine Liste der App-Dienste anzuzeigen, die Ihrem Azure-Abonnement zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="fd24c-154">Select **App Services** to view a list of the app services associated with the Azure subscription.</span></span>

1. <span data-ttu-id="fd24c-155">Wählen Sie die Web-App aus, die im vorherigen Abschnitt dieses Tutorials erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="fd24c-155">Select the web app created in the previous section of this tutorial.</span></span>

1. <span data-ttu-id="fd24c-156">Klicken Sie auf dem Blatt **Bereitstellung** auf **Bereitstellungsoptionen** > **Quelle auswählen** > **Lokales Git-Repository**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-156">In the **Deployment** blade, select **Deployment options** > **Choose Source** > **Local Git Repository**.</span></span>

   ![Blatt "Einstellungen": Blatt „Bereitstellungsquelle“: Quellblatt auswählen](azure-continuous-deployment/_static/deployment-options.png)

1. <span data-ttu-id="fd24c-158">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-158">Select **OK**.</span></span>

1. <span data-ttu-id="fd24c-159">Wenn zuvor keine Anmeldeinformationen für die Bereitstellung zum Veröffentlichen einer Web-App oder anderen App Service-App festgelegt wurden, richten Sie diese nun ein:</span><span class="sxs-lookup"><span data-stu-id="fd24c-159">If deployment credentials for publishing a web app or other App Service app haven't previously been set up, set them up now:</span></span>

   * <span data-ttu-id="fd24c-160">Wählen Sie **Einstellungen** > **Anmeldeinformationen für die Bereitstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-160">Select **Settings** > **Deployment credentials**.</span></span> <span data-ttu-id="fd24c-161">Das Blatt **Anmeldeinformationen für die Bereitstellung festlegen** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-161">The **Set deployment credentials** blade is displayed.</span></span>
   * <span data-ttu-id="fd24c-162">Erstellen Sie einen Benutzernamen und ein Kennwort.</span><span class="sxs-lookup"><span data-stu-id="fd24c-162">Create a user name and password.</span></span> <span data-ttu-id="fd24c-163">Speichern Sie das Kennwort zur späteren Verwendung beim Einrichten von Git.</span><span class="sxs-lookup"><span data-stu-id="fd24c-163">Save the password for later use when setting up Git.</span></span>
   * <span data-ttu-id="fd24c-164">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-164">Select **Save**.</span></span>

1. <span data-ttu-id="fd24c-165">Wählen Sie auf dem Blatt **Web-App** den Eintrag **Einstellungen** > **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-165">In the **Web App** blade, select **Settings** > **Properties**.</span></span> <span data-ttu-id="fd24c-166">Die URL des Git-Remoterepositorys, in dem die Bereitstellung erfolgt, wird unter **GIT-URL** angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-166">The URL of the remote Git repository to deploy to is shown under **GIT URL**.</span></span>

1. <span data-ttu-id="fd24c-167">Kopieren Sie den Wert **GIT-URL** zur späteren Verwendung in diesem Tutorial.</span><span class="sxs-lookup"><span data-stu-id="fd24c-167">Copy the **GIT URL** value for later use in the tutorial.</span></span>

   ![Azure-Portal: Blatt „Eigenschaften“ der Anwendung](azure-continuous-deployment/_static/09-azure-giturl.png)

## <a name="publish-the-web-app-to-azure-app-service"></a><span data-ttu-id="fd24c-169">Veröffentlichen der App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="fd24c-169">Publish the web app to Azure App Service</span></span>

<span data-ttu-id="fd24c-170">In diesem Abschnitt erstellen Sie ein lokales Git-Repository mit Visual Studio und führen einen Pushvorgang aus diesem Repository in Azure aus, um die Web-App bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-170">In this section, create a local Git repository using Visual Studio and push from that repository to Azure to deploy the web app.</span></span> <span data-ttu-id="fd24c-171">Folgende Schritte müssen ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="fd24c-171">The steps involved include the following:</span></span>

* <span data-ttu-id="fd24c-172">Fügen Sie die Einstellung des Remoterepositorys mithilfe des GIT-URL-Werts hinzu, damit das lokale Repository in Azure bereitgestellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fd24c-172">Add the remote repository setting using the GIT URL value, so the local repository can be deployed to Azure.</span></span>
* <span data-ttu-id="fd24c-173">Führen Sie für die Projektänderungen einen Commit aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-173">Commit project changes.</span></span>
* <span data-ttu-id="fd24c-174">Übertragen Sie die Projektänderungen mithilfe von Push aus Ihrem lokalen Repository in das Remoterepository in Azure.</span><span class="sxs-lookup"><span data-stu-id="fd24c-174">Push project changes from the local repository to the remote repository on Azure.</span></span>

1. <span data-ttu-id="fd24c-175">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Projektmappe „SampleWebAppDemo“** , und wählen Sie **Commit ausführen** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-175">In **Solution Explorer** right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="fd24c-176">Der **Team Explorer** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-176">The **Team Explorer** is displayed.</span></span>

   ![Team Explorer-Registerkarte „Verbinden“](azure-continuous-deployment/_static/10-team-explorer.png)

1. <span data-ttu-id="fd24c-178">Klicken Sie in **Team Explorer** auf **Start** (Startsymbol) > **Einstellungen** > **Repositoryeinstellungen**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-178">In **Team Explorer** , select the **Home** (home icon) > **Settings** > **Repository Settings**.</span></span>

1. <span data-ttu-id="fd24c-179">Klicken Sie in **Repositoryeinstellungen** im Abschnitt **Remotes** auf **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-179">In the **Remotes** section of the **Repository Settings** , select **Add**.</span></span> <span data-ttu-id="fd24c-180">Das Dialogfeld **Remote hinzufügen** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-180">The **Add Remote** dialog box is displayed.</span></span>

1. <span data-ttu-id="fd24c-181">Legen Sie den **Namen** der Remoteinstanz auf **Azure SampleApp** fest.</span><span class="sxs-lookup"><span data-stu-id="fd24c-181">Set the **Name** of the remote to **Azure-SampleApp**.</span></span>

1. <span data-ttu-id="fd24c-182">Legen Sie den Wert für **Fetch** auf die **Git-URL** fest, die Sie zuvor in diesem Tutorial aus Azure kopiert haben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-182">Set the value for **Fetch** to the **Git URL** that copied from Azure earlier in this tutorial.</span></span> <span data-ttu-id="fd24c-183">Beachten Sie, dass dies die URL ist, die mit **.git** endet.</span><span class="sxs-lookup"><span data-stu-id="fd24c-183">Note that this is the URL that ends with **.git**.</span></span>

   ![Dialogfeld „Remote bearbeiten“](azure-continuous-deployment/_static/11-add-remote.png)

   > [!NOTE]
   > <span data-ttu-id="fd24c-185">Geben Sie alternativ über das **Befehlsfenster** das Remoterepository an, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-185">As an alternative, specify the remote repository from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering the command.</span></span> <span data-ttu-id="fd24c-186">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fd24c-186">Example:</span></span>
   >
   > `git remote add Azure-SampleApp https://me@sampleapp.scm.azurewebsites.net:443/SampleApp.git`

1. <span data-ttu-id="fd24c-187">Klicken Sie auf **Start** (Startsymbol) > **Einstellungen** > **Globale Einstellungen**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-187">Select the **Home** (home icon) > **Settings** > **Global Settings**.</span></span> <span data-ttu-id="fd24c-188">Überprüfen Sie, ob der Name und die E-Mail-Adresse festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="fd24c-188">Confirm that the name and email address are set.</span></span> <span data-ttu-id="fd24c-189">Wählen Sie ggf. **Aktualisieren** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-189">Select **Update** if required.</span></span>

1. <span data-ttu-id="fd24c-190">Klicken Sie auf **Start** > **Änderungen** , um zur Ansicht **Änderungen** zurückzukehren.</span><span class="sxs-lookup"><span data-stu-id="fd24c-190">Select **Home** > **Changes** to return to the **Changes** view.</span></span>

1. <span data-ttu-id="fd24c-191">Geben Sie eine Commit-Nachricht ein, z.B. **Anfänglicher Push Nr. 1** , und klicken Sie auf **Commit ausführen**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-191">Enter a commit message, such as **Initial Push #1** and select **Commit**.</span></span> <span data-ttu-id="fd24c-192">Durch diese Aktion wird lokal ein *Commit* erstellt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-192">This action creates a *commit* locally.</span></span>

   ![Team Explorer-Registerkarte „Verbinden“](azure-continuous-deployment/_static/12-initial-commit.png)

   > [!NOTE]
   > <span data-ttu-id="fd24c-194">Führen Sie alternativ für Ihre Änderungen über das **Befehlsfenster** einen Commit aus, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und die Git-Befehle eingeben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-194">As an alternative, commit changes from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering the git commands.</span></span> <span data-ttu-id="fd24c-195">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fd24c-195">Example:</span></span>
   >
   > `git add .`
   >
   > `git commit -am "Initial Push #1"`

1. <span data-ttu-id="fd24c-196">Klicken Sie auf **Start** > **Synchronisieren** > **Aktionen** > **Eingabeaufforderung öffnen**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-196">Select **Home** > **Sync** > **Actions** > **Open Command Prompt**.</span></span> <span data-ttu-id="fd24c-197">Die Eingabeaufforderung wird im Projektverzeichnis geöffnet.</span><span class="sxs-lookup"><span data-stu-id="fd24c-197">The command prompt opens to the project directory.</span></span>

1. <span data-ttu-id="fd24c-198">Geben Sie im Befehlsfenster folgenden Befehl ein:</span><span class="sxs-lookup"><span data-stu-id="fd24c-198">Enter the following command in the command window:</span></span>

   `git push -u Azure-SampleApp master`

1. <span data-ttu-id="fd24c-199">Geben Sie das Kennwort für die Azure- **Anmeldeinformationen für die Bereitstellung** ein, das Sie zuvor in Azure erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-199">Enter the Azure **deployment credentials** password created earlier in Azure.</span></span>

   <span data-ttu-id="fd24c-200">Dieser Befehl startet eine Übertragung der lokalen Projektdateien mithilfe von Push in Azure.</span><span class="sxs-lookup"><span data-stu-id="fd24c-200">This command starts the process of pushing the local project files to Azure.</span></span> <span data-ttu-id="fd24c-201">Die Ausgabe des obigen Befehls endet mit der Meldung, dass die Bereitstellung erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="fd24c-201">The output from the above command ends with a message that the deployment was successful.</span></span>

   ```
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   To https://username@samplewebappdemo01.scm.azurewebsites.net:443/SampleWebAppDemo01.git
   * [new branch]      master -> master
   Branch master set up to track remote branch master from Azure-SampleApp.
   ```

   > [!NOTE]
   > <span data-ttu-id="fd24c-202">Wenn bei dem Projekt eine Zusammenarbeit erforderlich ist, sollten Sie in Erwägung ziehen, vor der Übertragung mithilfe von Push in Azure eine Übertragung mithilfe von Push in [GitHub](https://github.com) durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-202">If collaboration on the project is required, consider pushing to [GitHub](https://github.com) before pushing to Azure.</span></span>
 
### <a name="verify-the-active-deployment"></a><span data-ttu-id="fd24c-203">Überprüfen der aktiven Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="fd24c-203">Verify the Active Deployment</span></span>

<span data-ttu-id="fd24c-204">Überprüfen Sie, ob die Übertragung der Web-App über die lokale Umgebung in Azure erfolgreich war.</span><span class="sxs-lookup"><span data-stu-id="fd24c-204">Verify that the web app transfer from the local environment to Azure is successful.</span></span>

<span data-ttu-id="fd24c-205">Wählen Sie die Web-App im [Azure-Portal](https://portal.azure.com) aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-205">In the [Azure Portal](https://portal.azure.com), select the web app.</span></span> <span data-ttu-id="fd24c-206">Wählen Sie **Bereitstellung** > **Bereitstellungsoptionen** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-206">Select **Deployment** > **Deployment options**.</span></span>

![Azure-Portal: Blatt "Einstellungen": Blatt „Bereitstellungen“ mit erfolgreicher Bereitstellung](azure-continuous-deployment/_static/13-verify-deployment.png)

## <a name="run-the-app-in-azure"></a><span data-ttu-id="fd24c-208">Ausführen der App in Azure</span><span class="sxs-lookup"><span data-stu-id="fd24c-208">Run the app in Azure</span></span>

<span data-ttu-id="fd24c-209">Führen Sie die App aus, nachdem die Web-App in Azure bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="fd24c-209">Now that the web app is deployed to Azure, run the app.</span></span>

<span data-ttu-id="fd24c-210">Dafür stehen zwei Methoden zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="fd24c-210">This can be accomplished in two ways:</span></span>

* <span data-ttu-id="fd24c-211">Suchen Sie im Azure-Portal das Blatt „Web-App“ für die Web-App.</span><span class="sxs-lookup"><span data-stu-id="fd24c-211">In the Azure Portal, locate the web app blade for the web app.</span></span> <span data-ttu-id="fd24c-212">Wählen Sie **Durchsuchen** , um die App im Standardbrowser anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-212">Select **Browse** to view the app in the default browser.</span></span>
* <span data-ttu-id="fd24c-213">Öffnen Sie einen Browser, und geben Sie die URL für die Web-App ein.</span><span class="sxs-lookup"><span data-stu-id="fd24c-213">Open a browser and enter the URL for the web app.</span></span> <span data-ttu-id="fd24c-214">Ein Beispiel: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="fd24c-214">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="update-the-web-app-and-republish"></a><span data-ttu-id="fd24c-215">Aktualisieren der Web-App und erneutes Veröffentlichen</span><span class="sxs-lookup"><span data-stu-id="fd24c-215">Update the web app and republish</span></span>

<span data-ttu-id="fd24c-216">Veröffentlichen Sie den lokalen Code erneut, nachdem Sie Änderungen daran vorgenommen haben:</span><span class="sxs-lookup"><span data-stu-id="fd24c-216">After making changes to the local code, republish:</span></span>

1. <span data-ttu-id="fd24c-217">Öffnen Sie in Visual Studio im **Projektmappen-Explorer** die Datei *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="fd24c-217">In **Solution Explorer** of Visual Studio, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="fd24c-218">Ändern Sie in der `Configure`-Methode die `Response.WriteAsync`-Methode so, dass sie wie folgt angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="fd24c-218">In the `Configure` method, modify the `Response.WriteAsync` method so that it appears as follows:</span></span>

   ```csharp
   await context.Response.WriteAsync("Hello World! Deploy to Azure.");
   ```

1. <span data-ttu-id="fd24c-219">Speichern Sie die Änderungen in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="fd24c-219">Save the changes to *Startup.cs*.</span></span>

1. <span data-ttu-id="fd24c-220">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Projektmappe „SampleWebAppDemo“** , und wählen Sie **Commit ausführen** aus.</span><span class="sxs-lookup"><span data-stu-id="fd24c-220">In **Solution Explorer** , right-click **Solution 'SampleWebAppDemo'** and select **Commit**.</span></span> <span data-ttu-id="fd24c-221">Der **Team Explorer** wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fd24c-221">The **Team Explorer** is displayed.</span></span>

1. <span data-ttu-id="fd24c-222">Geben Sie eine Commit-Nachricht ein, wie z.B. `Update #2`.</span><span class="sxs-lookup"><span data-stu-id="fd24c-222">Enter a commit message, such as `Update #2`.</span></span>

1. <span data-ttu-id="fd24c-223">Klicken Sie auf die Schaltfläche **Commit ausführen** , um die Projektänderungen zu committen.</span><span class="sxs-lookup"><span data-stu-id="fd24c-223">Press the **Commit** button to commit the project changes.</span></span>

1. <span data-ttu-id="fd24c-224">Klicken Sie auf **Start** > **Synchronisieren** > **Aktionen** > **Push**.</span><span class="sxs-lookup"><span data-stu-id="fd24c-224">Select **Home** > **Sync** > **Actions** > **Push**.</span></span>

> [!NOTE]
> <span data-ttu-id="fd24c-225">Als Alternative können Sie die Änderungen im **Befehlsfenster** mithilfe von Push übertragen, indem Sie das **Befehlsfenster** öffnen, zum Projektverzeichnis wechseln und einen Git-Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-225">As an alternative, push the changes from the **Command Window** by opening the **Command Window** , changing to the project directory, and entering a git command.</span></span> <span data-ttu-id="fd24c-226">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fd24c-226">Example:</span></span>
> 
> `git push -u Azure-SampleApp master`

## <a name="view-the-updated-web-app-in-azure"></a><span data-ttu-id="fd24c-227">Anzeigen der aktualisierten Web-App in Azure</span><span class="sxs-lookup"><span data-stu-id="fd24c-227">View the updated web app in Azure</span></span>

<span data-ttu-id="fd24c-228">Zeigen Sie die aktualisierte Web-App an, indem Sie im Azure-Portal auf dem Blatt „Web-App“ **Durchsuchen** auswählen oder einen Browser öffnen und die URL der Web-App eingeben.</span><span class="sxs-lookup"><span data-stu-id="fd24c-228">View the updated web app by selecting **Browse** from the web app blade in the Azure Portal or by opening a browser and entering the URL for the web app.</span></span> <span data-ttu-id="fd24c-229">Ein Beispiel: `http://SampleWebAppDemo.azurewebsites.net`</span><span class="sxs-lookup"><span data-stu-id="fd24c-229">Example: `http://SampleWebAppDemo.azurewebsites.net`</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fd24c-230">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fd24c-230">Additional resources</span></span>

* [<span data-ttu-id="fd24c-231">Erstellen Sie Ihre erste Pipeline mit Azure Pipelines</span><span class="sxs-lookup"><span data-stu-id="fd24c-231">Create your first pipeline with Azure Pipelines</span></span>](/azure/devops/pipelines/get-started-yaml)
* [<span data-ttu-id="fd24c-232">Projekt Kudu</span><span class="sxs-lookup"><span data-stu-id="fd24c-232">Project Kudu</span></span>](https://github.com/projectkudu/kudu/wiki)
* <xref:host-and-deploy/visual-studio-publish-profiles>
