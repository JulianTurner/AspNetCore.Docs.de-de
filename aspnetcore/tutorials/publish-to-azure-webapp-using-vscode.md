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
# <a name="publish-an-aspnet-core-app-to-azure-with-visual-studio-code"></a><span data-ttu-id="5fcca-103">Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fcca-103">Publish an ASP.NET Core app to Azure with Visual Studio Code</span></span>

<span data-ttu-id="5fcca-104">Von [Ricardo Serradas](https://twitter.com/ricardoserradas)</span><span class="sxs-lookup"><span data-stu-id="5fcca-104">By [Ricardo Serradas](https://twitter.com/ricardoserradas)</span></span>

[!INCLUDE [Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

<span data-ttu-id="5fcca-105">Informationen zur Problembehandlung bei der App Service-Bereitstellung finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="5fcca-105">To troubleshoot an App Service deployment issue, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="intro"></a><span data-ttu-id="5fcca-106">Einführung</span><span class="sxs-lookup"><span data-stu-id="5fcca-106">Intro</span></span>

<span data-ttu-id="5fcca-107">In diesem Tutorial erfahren Sie, wie Sie eine ASP.Net Core MVC-Anwendung erstellen und diese in Visual Studio Code bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-107">With this tutorial, you'll learn how to create an ASP.Net Core MVC Application and deploy it within Visual Studio Code.</span></span>

## <a name="set-up"></a><span data-ttu-id="5fcca-108">Einrichten</span><span class="sxs-lookup"><span data-stu-id="5fcca-108">Set up</span></span>

- <span data-ttu-id="5fcca-109">Eröffnen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/dotnet/), wenn Sie noch über kein Konto verfügen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-109">Open a [free Azure account](https://azure.microsoft.com/free/dotnet/) if you don't have one.</span></span>
- <span data-ttu-id="5fcca-110">Installieren Sie das [.NET Core SDK](https://dotnet.microsoft.com/download).</span><span class="sxs-lookup"><span data-stu-id="5fcca-110">Install [.NET Core SDK](https://dotnet.microsoft.com/download)</span></span>
- <span data-ttu-id="5fcca-111">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/Download).</span><span class="sxs-lookup"><span data-stu-id="5fcca-111">Install [Visual Studio Code](https://code.visualstudio.com/Download)</span></span>
  - <span data-ttu-id="5fcca-112">Installieren Sie die [C#-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) für Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5fcca-112">Install the [C# Extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) to Visual Studio Code</span></span>
  - <span data-ttu-id="5fcca-113">Installieren Sie die [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) für Visual Studio Code und konfigurieren Sie diese, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="5fcca-113">Install the [Azure App Service Extension](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice) to Visual Studio Code and configure it before proceeding</span></span>

## <a name="create-an-aspnet-core-mvc-project"></a><span data-ttu-id="5fcca-114">Erstellen eines ASP.NET Core MVC-Projekts</span><span class="sxs-lookup"><span data-stu-id="5fcca-114">Create an ASP.Net Core MVC project</span></span>

<span data-ttu-id="5fcca-115">Navigieren Sie bei Verwendung eines Terminals zu dem Ordner, in dem Sie das Projekt erstellen wollen, und verwenden Sie den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="5fcca-115">Using a terminal, navigate to the folder you want the project to be created on and use the following command:</span></span>

```dotnetcli
dotnet new mvc
```

<span data-ttu-id="5fcca-116">Es wird Ihnen eine Ordnerstruktur wie die folgende angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5fcca-116">You'll have a folder structure similar to the following:</span></span>

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

## <a name="open-it-with-visual-studio-code"></a><span data-ttu-id="5fcca-117">Öffnen des Projekts mit Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5fcca-117">Open it with Visual Studio Code</span></span>

<span data-ttu-id="5fcca-118">Nachdem das Projekt erstellt wurde, können Sie es mit Visual Studio Code öffnen, indem Sie eine der folgenden Optionen verwenden:</span><span class="sxs-lookup"><span data-stu-id="5fcca-118">After your project is created, you can open it with Visual Studio Code by using one of the options below:</span></span>

### <a name="through-the-command-line"></a><span data-ttu-id="5fcca-119">Über die Befehlszeile</span><span class="sxs-lookup"><span data-stu-id="5fcca-119">Through the command line</span></span>

<span data-ttu-id="5fcca-120">Verwenden Sie in dem Ordner, in dem das Projekt erstellt wurde, den folgenden Befehl:</span><span class="sxs-lookup"><span data-stu-id="5fcca-120">Use the following command within the folder you created the project:</span></span>

```cmd
> code .
```

<span data-ttu-id="5fcca-121">Wenn der oben angegebene Befehl nicht funktioniert, überprüfen Sie, ob Ihre Installation ordnungsgemäß konfiguriert ist, indem Sie auf [diesen Link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform) verweisen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-121">If the command below does not work, check if your installation is configured properly by referencing [this link](https://code.visualstudio.com/docs/setup/setup-overview#_cross-platform).</span></span>

### <a name="through-visual-studio-code-interface"></a><span data-ttu-id="5fcca-122">Über die Visual Studio Code-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="5fcca-122">Through Visual Studio Code interface</span></span>

- <span data-ttu-id="5fcca-123">Öffnen Sie Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="5fcca-123">Open Visual Studio Code</span></span>
- <span data-ttu-id="5fcca-124">Wählen Sie im Menü `File > Open Folder` aus.</span><span class="sxs-lookup"><span data-stu-id="5fcca-124">On the menu, select `File > Open Folder`</span></span>
- <span data-ttu-id="5fcca-125">Wählen Sie den Stamm des Ordners aus, in dem das MVC-Projekt erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5fcca-125">Select the root of the folder you created the MVC Project</span></span>

<span data-ttu-id="5fcca-126">Wenn Sie den Projektordner öffnen, wird eine Meldung angezeigt, dass erforderliche Ressourcen zum Erstellen und Debuggen nicht vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="5fcca-126">When you open the project folder, you'll receive a message saying that required assets to build and debug are missing.</span></span> <span data-ttu-id="5fcca-127">Akzeptieren Sie die Hilfe, um sie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-127">Accept the help to add them.</span></span>

![Visual Studio Code-Schnittstelle mit geladenem Projekt](publish-to-azure-webapp-using-vscode/_static/folder-structure-restore-netcore.jpg)

<span data-ttu-id="5fcca-129">Ein `.vscode`-Ordner wird in der Projektstruktur erstellt.</span><span class="sxs-lookup"><span data-stu-id="5fcca-129">A `.vscode` folder will be created under the project structure.</span></span> <span data-ttu-id="5fcca-130">Er enthält die folgenden Dateien:</span><span class="sxs-lookup"><span data-stu-id="5fcca-130">It will contain the following files:</span></span>

```cmd
launch.json
tasks.json
```

<span data-ttu-id="5fcca-131">Hierbei handelt es sich um Hilfsprogrammdateien zum Erstellen und Debuggen Ihrer .NET Core-Web-App.</span><span class="sxs-lookup"><span data-stu-id="5fcca-131">These are utility files to help you build and debug your .NET Core Web App.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="5fcca-132">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="5fcca-132">Run the app</span></span>

<span data-ttu-id="5fcca-133">Bevor Sie die App in Azure bereitstellen, sollten Sie sicherstellen, dass sie auf Ihrem lokalen Computer ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5fcca-133">Before we deploy the app to Azure, make sure it is running properly on your local machine.</span></span>

- <span data-ttu-id="5fcca-134">Drücken Sie F5, um das Projekt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-134">Press F5 to run the project</span></span>

<span data-ttu-id="5fcca-135">Ihre Web-App wird in einem neuen Tab Ihres Standardbrowsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5fcca-135">Your web app will start running on a new tab of your default browser.</span></span> <span data-ttu-id="5fcca-136">Möglicherweise wird beim Starten eine Datenschutzwarnung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5fcca-136">You may notice a privacy warning as soon as it starts.</span></span> <span data-ttu-id="5fcca-137">Dies liegt daran, dass Ihre App zum Starten entweder HTTP oder HTTPs verwendet, und sie standardmäßig zum HTTPS-Endpunkt navigiert.</span><span class="sxs-lookup"><span data-stu-id="5fcca-137">This is because your app will start either using HTTP and HTTPS, and it navigates to the HTTPS endpoint by default.</span></span>

![Datenschutzwarnung beim lokalen Debuggen der App](publish-to-azure-webapp-using-vscode/_static/run-webapp-https-warning.jpg)

<span data-ttu-id="5fcca-139">Klicken Sie auf `Advanced` und dann auf `Continue to localhost (unsafe)`, um die Debugsitzung weiter auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-139">To keep the debugging session, click `Advanced` and then `Continue to localhost (unsafe)`.</span></span>

## <a name="generate-the-deployment-package-locally"></a><span data-ttu-id="5fcca-140">Bereitstellungspaket lokal generieren</span><span class="sxs-lookup"><span data-stu-id="5fcca-140">Generate the deployment package locally</span></span>

- <span data-ttu-id="5fcca-141">Öffnen Sie das Visual Studio Code-Terminal.</span><span class="sxs-lookup"><span data-stu-id="5fcca-141">Open Visual Studio Code terminal</span></span>
- <span data-ttu-id="5fcca-142">Verwenden Sie den folgenden Befehl zum Generieren eines `Release`-Pakets in einem Unterordner namens `publish`:</span><span class="sxs-lookup"><span data-stu-id="5fcca-142">Use the following command to generate a `Release` package to a sub folder called `publish`:</span></span>
  - `dotnet publish -c Release -o ./publish`
- <span data-ttu-id="5fcca-143">Ein neuer `publish`-Ordner wird in der Projektstruktur erstellt.</span><span class="sxs-lookup"><span data-stu-id="5fcca-143">A new `publish` folder will be created under the project structure</span></span>

![Struktur des „publish“-Ordners](publish-to-azure-webapp-using-vscode/_static/publish-folder.jpg)

## <a name="publish-to-azure-app-service"></a><span data-ttu-id="5fcca-145">Veröffentlichen in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5fcca-145">Publish to Azure App Service</span></span>

<span data-ttu-id="5fcca-146">Nutzen Sie die Azure App Service-Erweiterung für Visual Studio Code, und führen Sie die unten aufgeführten Schritte aus, um die Website direkt in Azure App Service zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-146">Leveraging the Azure App Service extension for Visual Studio Code, follow the steps below to publish the website directly to the Azure App Service.</span></span>

### <a name="if-youre-creating-a-new-web-app"></a><span data-ttu-id="5fcca-147">Wenn Sie eine neue Web-App erstellen</span><span class="sxs-lookup"><span data-stu-id="5fcca-147">If you're creating a new Web App</span></span>

- <span data-ttu-id="5fcca-148">Klicken Sie mit der rechten Maustaste auf den `publish`-Ordner, und wählen Sie `Deploy to Web App...` aus.</span><span class="sxs-lookup"><span data-stu-id="5fcca-148">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5fcca-149">Wählen Sie das Abonnement aus, das Sie zum Erstellen der Web-App verwenden möchten.</span><span class="sxs-lookup"><span data-stu-id="5fcca-149">Select the subscription you want to create the Web App</span></span>
- <span data-ttu-id="5fcca-150">Klicken Sie auf `Create New Web App`.</span><span class="sxs-lookup"><span data-stu-id="5fcca-150">Select `Create New Web App`</span></span>
- <span data-ttu-id="5fcca-151">Geben Sie einen Namen für die Web-App ein.</span><span class="sxs-lookup"><span data-stu-id="5fcca-151">Enter a name for the Web App</span></span>

<span data-ttu-id="5fcca-152">Mit dieser Erweiterung wird die neue Web-App erstellt, und das Paket für die App wird automatisch bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="5fcca-152">The extension will create the new Web App and will automatically start deploying the package to it.</span></span> <span data-ttu-id="5fcca-153">Nachdem die Bereitstellung abgeschlossen ist, klicken Sie auf `Browse Website`, um die Bereitstellung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-153">Once the deployment is finished, click `Browse Website` to validate the deployment.</span></span>

![Meldung über erfolgreiche Bereitstellung](publish-to-azure-webapp-using-vscode/_static/deployment-succeeded-message.jpg)

<span data-ttu-id="5fcca-155">Durch Klicken auf `Browse Website` gelangen Sie über Ihren Standardbrowser zur App:</span><span class="sxs-lookup"><span data-stu-id="5fcca-155">Once you click `Browse Website`, you'll navigate to it using your default browser:</span></span>

![Erfolgreich bereitgestellte neue Web-App](publish-to-azure-webapp-using-vscode/_static/new-webapp-deployed.jpg)

### <a name="if-youre-deploying-to-an-existing-web-app"></a><span data-ttu-id="5fcca-157">Wenn Sie Inhalte in einer vorhandenen Web-App bereitstellen</span><span class="sxs-lookup"><span data-stu-id="5fcca-157">If you're deploying to an existing Web App</span></span>

- <span data-ttu-id="5fcca-158">Klicken Sie mit der rechten Maustaste auf den `publish`-Ordner, und wählen Sie `Deploy to Web App...` aus.</span><span class="sxs-lookup"><span data-stu-id="5fcca-158">Right click the `publish` folder and select `Deploy to Web App...`</span></span>
- <span data-ttu-id="5fcca-159">Wählen Sie das Abonnement aus, in dem sich die vorhandene Web-App befindet.</span><span class="sxs-lookup"><span data-stu-id="5fcca-159">Select the subscription the existing Web App resides</span></span>
- <span data-ttu-id="5fcca-160">Wählen Sie die Web-App aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="5fcca-160">Select the Web App from the list</span></span>
- <span data-ttu-id="5fcca-161">Sie werden von Visual Studio Code gefragt, ob die vorhandenen Inhalte überschrieben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-161">Visual Studio Code will ask you if you want to overwrite the existing content.</span></span> <span data-ttu-id="5fcca-162">Klicken Sie zum Bestätigen auf `Deploy`.</span><span class="sxs-lookup"><span data-stu-id="5fcca-162">Click `Deploy` to confirm</span></span>

<span data-ttu-id="5fcca-163">Die Erweiterung stellt die aktualisierten Inhalte in der Web-App bereit.</span><span class="sxs-lookup"><span data-stu-id="5fcca-163">The extension will deploy the updated content to the Web App.</span></span> <span data-ttu-id="5fcca-164">Sobald dieser Vorgang abgeschlossen ist, klicken Sie auf `Browse Website`, um die Bereitstellung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5fcca-164">Once it's done, click `Browse Website` to validate the deployment.</span></span>

![Erfolgreich bereitgestellte Inhalte in vorhandener Web-App](publish-to-azure-webapp-using-vscode/_static/existing-webapp-deployed.jpg)

## <a name="next-steps"></a><span data-ttu-id="5fcca-166">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5fcca-166">Next steps</span></span>

- [<span data-ttu-id="5fcca-167">Erstellen Ihrer ersten Azure DevOps-Pipeline</span><span class="sxs-lookup"><span data-stu-id="5fcca-167">Create your first Azure DevOps pipeline</span></span>](/azure/devops/pipelines/create-first-pipeline)

## <a name="additional-resources"></a><span data-ttu-id="5fcca-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5fcca-168">Additional resources</span></span>

- [<span data-ttu-id="5fcca-169">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5fcca-169">Azure App Service</span></span>](/azure/app-service/app-service-web-overview)
- [<span data-ttu-id="5fcca-170">Azure-Ressourcengruppen</span><span class="sxs-lookup"><span data-stu-id="5fcca-170">Azure resource groups</span></span>](/azure/azure-resource-manager/resource-group-overview#resource-groups)
