---
title: 'Veröffentlichen einer ASP.net Core- SignalR app in Azure App Service'
author: bradygaster
description: 'Erfahren Sie, wie Sie eine ASP.net Core- SignalR app in Azure App Service veröffentlichen.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: signalr/publish-to-azure-web-app
ms.openlocfilehash: 8e6d36fe0b38486f94078b8f9cf12b852da7e0d9
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234508"
---
# <a name="publish-an-aspnet-core-no-locsignalr-app-to-azure-app-service"></a><span data-ttu-id="3991f-103">Veröffentlichen einer ASP.net Core- SignalR app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3991f-103">Publish an ASP.NET Core SignalR app to Azure App Service</span></span>

<span data-ttu-id="3991f-104">Von [Brady Gastern](https://twitter.com/bradygaster)</span><span class="sxs-lookup"><span data-stu-id="3991f-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="3991f-105">[Azure App Service](/azure/app-service/app-service-web-overview) ist ein [Microsoft Cloud Computing](https://azure.microsoft.com/) Platform-Dienst zum Hosting von Web-Apps, einschließlich ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="3991f-105">[Azure App Service](/azure/app-service/app-service-web-overview) is a [Microsoft cloud computing](https://azure.microsoft.com/) platform service for hosting web apps, including ASP.NET Core.</span></span>

> [!NOTE]
> <span data-ttu-id="3991f-106">Dieser Artikel bezieht sich auf das Veröffentlichen einer ASP.net Core- SignalR App aus Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="3991f-106">This article refers to publishing an ASP.NET Core SignalR app from Visual Studio.</span></span> <span data-ttu-id="3991f-107">Weitere Informationen finden Sie unter [ SignalR Service for Azure](https://azure.microsoft.com/services/signalr-service).</span><span class="sxs-lookup"><span data-stu-id="3991f-107">For more information, see [SignalR service for Azure](https://azure.microsoft.com/services/signalr-service).</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="3991f-108">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="3991f-108">Publish the app</span></span>

<span data-ttu-id="3991f-109">In diesem Artikel wird die Veröffentlichung mit den Tools in Visual Studio behandelt.</span><span class="sxs-lookup"><span data-stu-id="3991f-109">This article covers publishing using the tools in Visual Studio.</span></span> <span data-ttu-id="3991f-110">Visual Studio Code Benutzer können [Azure CLI](/cli/azure) Befehle zum Veröffentlichen von apps in Azure verwenden.</span><span class="sxs-lookup"><span data-stu-id="3991f-110">Visual Studio Code users can use [Azure CLI](/cli/azure) commands to publish apps to Azure.</span></span> <span data-ttu-id="3991f-111">Weitere Informationen finden Sie unter [Veröffentlichen einer ASP.net Core-app in Azure mit Befehlszeilen Tools](/azure/app-service/app-service-web-get-started-dotnet).</span><span class="sxs-lookup"><span data-stu-id="3991f-111">For more information, see [Publish an ASP.NET Core app to Azure with command line tools](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

1. <span data-ttu-id="3991f-112">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="3991f-112">Right-click on the project in **Solution Explorer** and select **Publish** .</span></span>

1. <span data-ttu-id="3991f-113">Vergewissern Sie sich, dass im Dialogfeld **Veröffentlichungsziel auswählen** die Option **App Service** und **neu erstellen** ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="3991f-113">Confirm that **App Service** and **Create new** are selected in the **Pick a publish target** dialog.</span></span>

1. <span data-ttu-id="3991f-114">Wählen Sie im Dropdown Feld " **veröffentlichen** " die Option **Profil erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3991f-114">Select **Create Profile** from the **Publish** button drop down.</span></span>

   <span data-ttu-id="3991f-115">Geben Sie die in der folgenden Tabelle beschriebenen Informationen in das Dialogfeld **App Service erstellen** ein, und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3991f-115">Enter the information described in the following table in the **Create App Service** dialog and select **Create** .</span></span>

   | <span data-ttu-id="3991f-116">Element</span><span class="sxs-lookup"><span data-stu-id="3991f-116">Item</span></span>               | <span data-ttu-id="3991f-117">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="3991f-117">Description</span></span> |
   | ------------------ | ----------- |
   | <span data-ttu-id="3991f-118">**Name**</span><span class="sxs-lookup"><span data-stu-id="3991f-118">**Name**</span></span>           | <span data-ttu-id="3991f-119">Der eindeutige Name der app.</span><span class="sxs-lookup"><span data-stu-id="3991f-119">Unique name of the app.</span></span> |
   | <span data-ttu-id="3991f-120">**Abonnement**</span><span class="sxs-lookup"><span data-stu-id="3991f-120">**Subscription**</span></span>   | <span data-ttu-id="3991f-121">Azure-Abonnement, das von der APP verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="3991f-121">Azure subscription that the app uses.</span></span> |
   | <span data-ttu-id="3991f-122">**Ressourcengruppe**</span><span class="sxs-lookup"><span data-stu-id="3991f-122">**Resource Group**</span></span> | <span data-ttu-id="3991f-123">Gruppe verwandter Ressourcen, zu denen die APP gehört.</span><span class="sxs-lookup"><span data-stu-id="3991f-123">Group of related resources to which the app belongs.</span></span> |
   | <span data-ttu-id="3991f-124">**Hostingplan**</span><span class="sxs-lookup"><span data-stu-id="3991f-124">**Hosting Plan**</span></span>   | <span data-ttu-id="3991f-125">Der Tarif für die Web-App.</span><span class="sxs-lookup"><span data-stu-id="3991f-125">Pricing plan for the web app.</span></span> |

1. <span data-ttu-id="3991f-126">Wählen Sie im Abschnitt **Dienst Abhängigkeiten** die Option **Azure- SignalR Dienst** aus.</span><span class="sxs-lookup"><span data-stu-id="3991f-126">Select **Azure SignalR Service** in the **Service Dependencies** section.</span></span> <span data-ttu-id="3991f-127">Wählen Sie die **+** Schaltfläche aus:</span><span class="sxs-lookup"><span data-stu-id="3991f-127">Select the **+** button:</span></span>

   ![Bereich Abhängigkeiten mit der Auswahl von Azure::: NO-LOC (signalr)::: Service in der Dropdown Liste hinzufügen](publish-to-azure-web-app/_static/signalr-service-dependency.png)

1. <span data-ttu-id="3991f-129">Wählen Sie im Dialogfeld **Azure- SignalR Dienst** die Option **neue Azure- SignalR Dienst Instanz erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="3991f-129">In the **Azure SignalR Service** dialog, select **Create a new Azure SignalR Service instance** .</span></span>

1. <span data-ttu-id="3991f-130">Geben Sie einen **Namen** , eine **Ressourcengruppe** und einen **Speicherort** an.</span><span class="sxs-lookup"><span data-stu-id="3991f-130">Provide a **Name** , **Resource Group** , and **Location** .</span></span> <span data-ttu-id="3991f-131">Wechseln Sie zum Dialogfeld **Azure- SignalR Dienst** , und wählen Sie **Hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="3991f-131">Return to the **Azure SignalR Service** dialog and select **Add** .</span></span>

<span data-ttu-id="3991f-132">Visual Studio führt die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="3991f-132">Visual Studio completes the following tasks:</span></span>

* <span data-ttu-id="3991f-133">Erstellt ein Veröffentlichungs Profil mit Veröffentlichungs Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="3991f-133">Creates a Publish Profile containing publish settings.</span></span>
* <span data-ttu-id="3991f-134">Erstellt eine *Azure-Web-App* mit den bereitgestellten Details.</span><span class="sxs-lookup"><span data-stu-id="3991f-134">Creates an *Azure Web App* with the provided details.</span></span>
* <span data-ttu-id="3991f-135">Veröffentlicht die app.</span><span class="sxs-lookup"><span data-stu-id="3991f-135">Publishes the app.</span></span>
* <span data-ttu-id="3991f-136">Hiermit wird ein Browser gestartet, der die Web-App lädt.</span><span class="sxs-lookup"><span data-stu-id="3991f-136">Launches a browser, which loads the web app.</span></span>

<span data-ttu-id="3991f-137">Das Format der App-URL lautet `{APP SERVICE NAME}.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="3991f-137">The format of the app's URL is `{APP SERVICE NAME}.azurewebsites.net`.</span></span> <span data-ttu-id="3991f-138">Eine APP `SignalRChatApp` mit dem Namen hat z. b. eine URL von `https://signalrchatapp.azurewebsites.net` .</span><span class="sxs-lookup"><span data-stu-id="3991f-138">For example, an app named `SignalRChatApp` has a URL of `https://signalrchatapp.azurewebsites.net`.</span></span>

<span data-ttu-id="3991f-139">Wenn beim Bereitstellen einer APP, die auf eine Vorschauversion von .net Core ausgerichtet ist, ein Fehler vom Typ "http *502,2-* ungültiges Gateway" auftritt, finden Sie weitere Informationen unter Bereitstellen [ASP.net Core Vorschau Release Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)</span><span class="sxs-lookup"><span data-stu-id="3991f-139">If an HTTP *502.2 - Bad Gateway* error occurs when deploying an app that targets a preview .NET Core release, see [Deploy ASP.NET Core preview release to Azure App Service](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service) to resolve it.</span></span>

## <a name="configure-the-app-in-azure-app-service"></a><span data-ttu-id="3991f-140">Konfigurieren der app in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="3991f-140">Configure the app in Azure App Service</span></span>

> [!NOTE]
> <span data-ttu-id="3991f-141">*Dieser Abschnitt gilt nur für apps, die den Azure-Dienst nicht verwenden SignalR .*</span><span class="sxs-lookup"><span data-stu-id="3991f-141">*This section only applies to apps not using the Azure SignalR Service.*</span></span>
>
> <span data-ttu-id="3991f-142">Wenn die APP den Azure- SignalR Dienst verwendet, ist für den App Service nicht die Konfiguration der in diesem Abschnitt beschriebenen arr-Affinität (Application Request Routing) und der in diesem Abschnitt beschriebenen websockets erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3991f-142">If the app uses the Azure SignalR Service, the App Service doesn't require the configuration of Application Request Routing (ARR) Affinity and Web Sockets described in this section.</span></span> <span data-ttu-id="3991f-143">Clients verbinden ihre websockets mit dem Azure- SignalR Dienst und nicht direkt mit der app.</span><span class="sxs-lookup"><span data-stu-id="3991f-143">Clients connect their Web Sockets to the Azure SignalR Service, not directly to the app.</span></span>

<span data-ttu-id="3991f-144">Aktivieren Sie für apps, die ohne Azure- SignalR Dienst gehostet werden, Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3991f-144">For apps hosted without the Azure SignalR Service, enable:</span></span>

* <span data-ttu-id="3991f-145">[Arr-Affinität] ( https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity- cookie -(Arr- cookie ) -for-Azure-web-apps.html), um Anforderungen von einem Benutzer zurück an dieselbe App Service Instanz weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="3991f-145">[ARR Affinity](https://azure.github.io/AppService/2016/05/16/Disable-Session-affinity-cookie-(ARR-cookie)-for-Azure-web-apps.html) to route requests from a user back to the same App Service instance.</span></span> <span data-ttu-id="3991f-146">Die Standardeinstellung ist **on** .</span><span class="sxs-lookup"><span data-stu-id="3991f-146">The default setting is **On** .</span></span>
* <span data-ttu-id="3991f-147">[Websockets](xref:fundamentals/websockets) , damit der websockets-Transport funktioniert.</span><span class="sxs-lookup"><span data-stu-id="3991f-147">[Web Sockets](xref:fundamentals/websockets) to allow the Web Sockets transport to function.</span></span> <span data-ttu-id="3991f-148">Die Standardeinstellung ist **Off** .</span><span class="sxs-lookup"><span data-stu-id="3991f-148">The default setting is **Off** .</span></span>

1. <span data-ttu-id="3991f-149">Navigieren Sie in der Azure-Portal zu der Web-App in **App Services** .</span><span class="sxs-lookup"><span data-stu-id="3991f-149">In the Azure portal, navigate to the web app in **App Services** .</span></span>
1. <span data-ttu-id="3991f-150">Öffnen Sie die allgemeinen **Konfigurations**  >  **Einstellungen** .</span><span class="sxs-lookup"><span data-stu-id="3991f-150">Open **Configuration** > **General settings** .</span></span>
1. <span data-ttu-id="3991f-151">Legen Sie **websockets** **auf ein** fest.</span><span class="sxs-lookup"><span data-stu-id="3991f-151">Set **Web sockets** to **On** .</span></span>
1. <span data-ttu-id="3991f-152">Vergewissern Sie sich, dass **arr-Affinität** **auf** ein festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="3991f-152">Verify that **ARR affinity** is set to **On** .</span></span>

## <a name="app-service-plan-limits"></a><span data-ttu-id="3991f-153">App Service Plan Limits</span><span class="sxs-lookup"><span data-stu-id="3991f-153">App Service Plan limits</span></span>

<span data-ttu-id="3991f-154">Websockets und andere Transporte sind auf Grundlage des ausgewählten App Service Plans beschränkt.</span><span class="sxs-lookup"><span data-stu-id="3991f-154">Web Sockets and other transports are limited based on the App Service Plan selected.</span></span> <span data-ttu-id="3991f-155">Weitere Informationen finden Sie in den Abschnitten *Azure-Cloud Services Limits* und *App Service Limits* im Artikel Einschränkungen für [Azure-Abonnements und Dienste, Kontingente und Einschränkungen](/azure/azure-subscription-service-limits#app-service-limits) .</span><span class="sxs-lookup"><span data-stu-id="3991f-155">For more information, see the *Azure Cloud Services limits* and *App Service limits* sections of the [Azure subscription and service limits, quotas, and constraints](/azure/azure-subscription-service-limits#app-service-limits) article.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3991f-156">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3991f-156">Additional resources</span></span>

* [<span data-ttu-id="3991f-157">Was ist Azure SignalR Service?</span><span class="sxs-lookup"><span data-stu-id="3991f-157">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
* <xref:signalr/introduction>
* <xref:host-and-deploy/index>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="3991f-158">Veröffentlichen einer ASP.NET Core-App in Azure mithilfe von Befehlszeilentools</span><span class="sxs-lookup"><span data-stu-id="3991f-158">Publish an ASP.NET Core app to Azure with command line tools</span></span>](/azure/app-service/app-service-web-get-started-dotnet)
* [<span data-ttu-id="3991f-159">Hosten und Bereitstellen ASP.net Core Vorschau-apps in Azure</span><span class="sxs-lookup"><span data-stu-id="3991f-159">Host and deploy ASP.NET Core Preview apps on Azure</span></span>](xref:host-and-deploy/azure-apps/index#deploy-aspnet-core-preview-release-to-azure-app-service)
