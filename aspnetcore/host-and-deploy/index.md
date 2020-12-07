---
title: Hosten und Bereitstellen von ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie Hostingumgebungen einrichten und ASP.NET Core-Apps bereitstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/index
ms.openlocfilehash: 4f3d4c29a189cf6aa14eb10f570f0b35d8ff9abc
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556618"
---
# <a name="host-and-deploy-aspnet-core"></a><span data-ttu-id="0de40-103">Hosten und Bereitstellen von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0de40-103">Host and deploy ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0de40-104">Um eine ASP.NET Core-App in einer Hostingumgebung bereitzustellen, müssen Sie allgemein folgende Schritte durchführen:</span><span class="sxs-lookup"><span data-stu-id="0de40-104">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="0de40-105">Stellen Sie die veröffentlichte App in einem Ordner auf dem Hostserver bereit.</span><span class="sxs-lookup"><span data-stu-id="0de40-105">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="0de40-106">Richten Sie einen Prozess-Manager ein, der die App startet, wenn Anforderungen eingehen und die App nach einem Absturz oder Serverneustart neu startet.</span><span class="sxs-lookup"><span data-stu-id="0de40-106">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="0de40-107">Richten Sie zur Konfiguration eines Reverseproxys einen Reverseproxy ein, der Anforderungen an die App weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="0de40-107">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="0de40-108">Veröffentlichen in einem Ordner</span><span class="sxs-lookup"><span data-stu-id="0de40-108">Publish to a folder</span></span>

<span data-ttu-id="0de40-109">Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) kompiliert App-Code und kopiert die Dateien, die zum Ausführen der App erforderlich sind, in den Ordner *publish*.</span><span class="sxs-lookup"><span data-stu-id="0de40-109">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="0de40-110">Bei der Bereitstellung von Visual Studio erfolgt der `dotnet publish`-Schritt automatisch, bevor die Dateien in das Bereitstellungsziel kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-110">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

## <a name="publish-settings-files"></a><span data-ttu-id="0de40-111">Dateien mit Veröffentlichungseinstellungen</span><span class="sxs-lookup"><span data-stu-id="0de40-111">Publish settings files</span></span>

<span data-ttu-id="0de40-112">`*.json`-Dateien werden standardmäßig veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="0de40-112">`*.json` files are published by default.</span></span> <span data-ttu-id="0de40-113">Um andere Einstellungsdateien zu veröffentlichen, geben Sie sie in einem [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content)-Element in der Projektdatei an.</span><span class="sxs-lookup"><span data-stu-id="0de40-113">To publish other settings files, specify them in an [`<ItemGroup><Content Include= ... />`](/visualstudio/msbuild/common-msbuild-project-items#content) element in the project file.</span></span> <span data-ttu-id="0de40-114">Im folgenden Beispiel werden XML-Dateien veröffentlicht:</span><span class="sxs-lookup"><span data-stu-id="0de40-114">The following example publishes XML files:</span></span>

```xml
<ItemGroup>
  <Content Include="**\*.xml" Exclude="bin\**\*;obj\**\*"
    CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

### <a name="folder-contents"></a><span data-ttu-id="0de40-115">Ordnerinhalte</span><span class="sxs-lookup"><span data-stu-id="0de40-115">Folder contents</span></span>

<span data-ttu-id="0de40-116">Der Ordner *publish* enthält mindestens eine App-Assemblydatei, Abhängigkeiten und optional die .NET-Runtime.</span><span class="sxs-lookup"><span data-stu-id="0de40-116">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="0de40-117">Eine .NET Core-App kann als *eigenständige Bereitstellung* oder als *Framework-abhängige Bereitstellung* veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-117">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="0de40-118">Wenn die App eigenständig ist, sind die Assemblydateien, die die .NET-Runtime enthalten, im Ordner *publish* enthalten.</span><span class="sxs-lookup"><span data-stu-id="0de40-118">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="0de40-119">Wenn die App frameworkabhängig ist, sind die Dateien für die .NET-Runtime nicht enthalten, da die App über einen Verweis auf eine auf dem Server installierte .NET-Version verfügt.</span><span class="sxs-lookup"><span data-stu-id="0de40-119">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="0de40-120">Das Standardmodell für die Bereitstellung ist Framework-abhängig.</span><span class="sxs-lookup"><span data-stu-id="0de40-120">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="0de40-121">Weitere Informationen finden Sie unter [.NET Core application deployment (.NET Core-Anwendungsbereitstellung)](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="0de40-121">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="0de40-122">Zusätzlich zu den *EXE*- und *DLL*-Dateien enthält der Ordner *publish* für eine ASP.NET Core-App üblicherweise noch Konfigurationsdateien, statische Objekte und MVC-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="0de40-122">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="0de40-123">Weitere Informationen finden Sie unter <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="0de40-123">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="0de40-124">Einrichten eines Prozessmanagers</span><span class="sxs-lookup"><span data-stu-id="0de40-124">Set up a process manager</span></span>

<span data-ttu-id="0de40-125">Bei einer ASP.NET Core-App handelt es sich um eine Konsolen-App, die gestartet werden muss, wenn ein Server startet und nach Abstürzen neu startet.</span><span class="sxs-lookup"><span data-stu-id="0de40-125">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="0de40-126">Sie benötigen einen Prozess-Manager, um die Starts und Neustarts zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="0de40-126">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="0de40-127">Die gängigsten Prozess-Manager für ASP.NET Core sind Folgende:</span><span class="sxs-lookup"><span data-stu-id="0de40-127">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="0de40-128">Linux</span><span class="sxs-lookup"><span data-stu-id="0de40-128">Linux</span></span>
  * [<span data-ttu-id="0de40-129">Nginx</span><span class="sxs-lookup"><span data-stu-id="0de40-129">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="0de40-130">Apache</span><span class="sxs-lookup"><span data-stu-id="0de40-130">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="0de40-131">Windows</span><span class="sxs-lookup"><span data-stu-id="0de40-131">Windows</span></span>
  * [<span data-ttu-id="0de40-132">IIS</span><span class="sxs-lookup"><span data-stu-id="0de40-132">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="0de40-133">Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="0de40-133">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="0de40-134">Einrichten eines Reverseproxys</span><span class="sxs-lookup"><span data-stu-id="0de40-134">Set up a reverse proxy</span></span>

<span data-ttu-id="0de40-135">Wenn die App den [Kestrel](xref:fundamentals/servers/kestrel) Server verwendet, können [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) oder [IIS](xref:host-and-deploy/iis/index) als Reverseproxyserver verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-135">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="0de40-136">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="0de40-136">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="0de40-137">Jede der beiden Konfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; ist eine unterstützte Hostingkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="0de40-137">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="0de40-138">Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="0de40-138">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0de40-139">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="0de40-139">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0de40-140">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-140">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="0de40-141">Ohne zusätzliche Konfiguration hat eine App möglicherweise keinen Zugriff auf das Schema (HTTP/HTTPS) und die Remote-IP-Adresse, von der eine Anforderung stammte.</span><span class="sxs-lookup"><span data-stu-id="0de40-141">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="0de40-142">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="0de40-142">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="0de40-143">Verwenden von Visual Studio und MSBuild zum Automatisieren der Bereitstellungen</span><span class="sxs-lookup"><span data-stu-id="0de40-143">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="0de40-144">Die Bereitstellung erfordert neben dem Kopieren der Ausgabe von [dotnet publish](/dotnet/core/tools/dotnet-publish) auf einen Server oft zusätzliche Aufgaben.</span><span class="sxs-lookup"><span data-stu-id="0de40-144">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="0de40-145">Beispielsweise können zusätzliche Dateien aus dem Ordner *publish* erforderlich oder ausgeschlossen sein.</span><span class="sxs-lookup"><span data-stu-id="0de40-145">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="0de40-146">Visual Studio verwendet [MSBuild](/visualstudio/msbuild/msbuild) für die Webbereitstellung, und MSBuild kann für die Ausführung vieler weiterer Tasks während der Bereitstellung angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-146">Visual Studio uses [MSBuild](/visualstudio/msbuild/msbuild) for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="0de40-147">Weitere Informationen finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles> und im Buch [Using MSBuild and Team Foundation Build (Verwenden von MSBuild und Team Foundation Build)](http://msbuildbook.com/).</span><span class="sxs-lookup"><span data-stu-id="0de40-147">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="0de40-148">Mithilfe des [Features zum Veröffentlichen einer Web-App](xref:tutorials/publish-to-azure-webapp-using-vs) oder der [integrierten Git-Unterstützung](xref:host-and-deploy/azure-apps/azure-continuous-deployment) können Apps direkt von Visual Studio in Azure App Service bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-148">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="0de40-149">Azure DevOps Services unterstützt [Continuous Deployment](/azure/devops/pipelines/targets/webapp) in Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0de40-149">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="0de40-150">Weitere Informationen finden Sie unter [DevOps mit ASP.NET Core und Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="0de40-150">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="0de40-151">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="0de40-151">Publish to Azure</span></span>

<span data-ttu-id="0de40-152">Anleitungen zum Veröffentlichen einer App in Azure mithilfe von Visual Studio finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="0de40-152">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="0de40-153">Ein weiteres Beispiel wird unter [Erstellen von ASP.NET Core-Web-Apps in Azure](/azure/app-service/app-service-web-get-started-dotnet) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0de40-153">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="0de40-154">Veröffentlichen mit MSDeploy unter Windows</span><span class="sxs-lookup"><span data-stu-id="0de40-154">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="0de40-155">Anleitungen zum Veröffentlichen einer App mit einem Visual Studio-Veröffentlichungsprofil z.B. von einer Windows-Eingabeaufforderung mit dem Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="0de40-155">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="0de40-156">IIS (Internet Information Services)</span><span class="sxs-lookup"><span data-stu-id="0de40-156">Internet Information Services (IIS)</span></span>

<span data-ttu-id="0de40-157">Informationen zu Bereitstellungen zu Internetinformationsdiensten (IIS) mit von der *web.config*-Datei bereitgestellter Konfiguration, finden Sie in den Artikeln unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="0de40-157">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="0de40-158">Hosten in einer Webfarm</span><span class="sxs-lookup"><span data-stu-id="0de40-158">Host in a web farm</span></span>

<span data-ttu-id="0de40-159">Weitere Informationen zur Konfiguration des Hostings von ASP.NET Core-Apps in einer Webfarmumgebung (z.B. Bereitstellen mehrerer App-Instanzen zur besseren Skalierbarkeit) finden Sie unter <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="0de40-159">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="0de40-160">Hosten auf Docker</span><span class="sxs-lookup"><span data-stu-id="0de40-160">Host on Docker</span></span>

<span data-ttu-id="0de40-161">Weitere Informationen finden Sie unter <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="0de40-161">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="perform-health-checks"></a><span data-ttu-id="0de40-162">Ausführen von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="0de40-162">Perform health checks</span></span>

<span data-ttu-id="0de40-163">Verwenden Sie Middleware für die Integritätsprüfung, um Integritätsprüfungen für eine App und deren Abhängigkeiten auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0de40-163">Use Health Check Middleware to perform health checks on an app and its dependencies.</span></span> <span data-ttu-id="0de40-164">Weitere Informationen finden Sie unter <xref:host-and-deploy/health-checks>.</span><span class="sxs-lookup"><span data-stu-id="0de40-164">For more information, see <xref:host-and-deploy/health-checks>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0de40-165">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0de40-165">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="0de40-166">Hosten von ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0de40-166">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="0de40-167">Um eine ASP.NET Core-App in einer Hostingumgebung bereitzustellen, müssen Sie allgemein folgende Schritte durchführen:</span><span class="sxs-lookup"><span data-stu-id="0de40-167">In general, to deploy an ASP.NET Core app to a hosting environment:</span></span>

* <span data-ttu-id="0de40-168">Stellen Sie die veröffentlichte App in einem Ordner auf dem Hostserver bereit.</span><span class="sxs-lookup"><span data-stu-id="0de40-168">Deploy the published app to a folder on the hosting server.</span></span>
* <span data-ttu-id="0de40-169">Richten Sie einen Prozess-Manager ein, der die App startet, wenn Anforderungen eingehen und die App nach einem Absturz oder Serverneustart neu startet.</span><span class="sxs-lookup"><span data-stu-id="0de40-169">Set up a process manager that starts the app when requests arrive and restarts the app after it crashes or the server reboots.</span></span>
* <span data-ttu-id="0de40-170">Richten Sie zur Konfiguration eines Reverseproxys einen Reverseproxy ein, der Anforderungen an die App weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="0de40-170">For configuration of a reverse proxy, set up a reverse proxy to forward requests to the app.</span></span>

## <a name="publish-to-a-folder"></a><span data-ttu-id="0de40-171">Veröffentlichen in einem Ordner</span><span class="sxs-lookup"><span data-stu-id="0de40-171">Publish to a folder</span></span>

<span data-ttu-id="0de40-172">Der Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) kompiliert App-Code und kopiert die Dateien, die zum Ausführen der App erforderlich sind, in den Ordner *publish*.</span><span class="sxs-lookup"><span data-stu-id="0de40-172">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command compiles app code and copies the files required to run the app into a *publish* folder.</span></span> <span data-ttu-id="0de40-173">Bei der Bereitstellung von Visual Studio erfolgt der `dotnet publish`-Schritt automatisch, bevor die Dateien in das Bereitstellungsziel kopiert werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-173">When deploying from Visual Studio, the `dotnet publish` step occurs automatically before the files are copied to the deployment destination.</span></span>

### <a name="folder-contents"></a><span data-ttu-id="0de40-174">Ordnerinhalte</span><span class="sxs-lookup"><span data-stu-id="0de40-174">Folder contents</span></span>

<span data-ttu-id="0de40-175">Der Ordner *publish* enthält mindestens eine App-Assemblydatei, Abhängigkeiten und optional die .NET-Runtime.</span><span class="sxs-lookup"><span data-stu-id="0de40-175">The *publish* folder contains one or more app assembly files, dependencies, and optionally the .NET runtime.</span></span>

<span data-ttu-id="0de40-176">Eine .NET Core-App kann als *eigenständige Bereitstellung* oder als *Framework-abhängige Bereitstellung* veröffentlicht werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-176">A .NET Core app can be published as *self-contained deployment* or *framework-dependent deployment*.</span></span> <span data-ttu-id="0de40-177">Wenn die App eigenständig ist, sind die Assemblydateien, die die .NET-Runtime enthalten, im Ordner *publish* enthalten.</span><span class="sxs-lookup"><span data-stu-id="0de40-177">If the app is self-contained, the assembly files that contain the .NET runtime are included in the *publish* folder.</span></span> <span data-ttu-id="0de40-178">Wenn die App frameworkabhängig ist, sind die Dateien für die .NET-Runtime nicht enthalten, da die App über einen Verweis auf eine auf dem Server installierte .NET-Version verfügt.</span><span class="sxs-lookup"><span data-stu-id="0de40-178">If the app is framework-dependent, the .NET runtime files aren't included because the app has a reference to a version of .NET that's installed on the server.</span></span> <span data-ttu-id="0de40-179">Das Standardmodell für die Bereitstellung ist Framework-abhängig.</span><span class="sxs-lookup"><span data-stu-id="0de40-179">The default deployment model is framework-dependent.</span></span> <span data-ttu-id="0de40-180">Weitere Informationen finden Sie unter [.NET Core application deployment (.NET Core-Anwendungsbereitstellung)](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="0de40-180">For more information, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

<span data-ttu-id="0de40-181">Zusätzlich zu den *EXE*- und *DLL*-Dateien enthält der Ordner *publish* für eine ASP.NET Core-App üblicherweise noch Konfigurationsdateien, statische Objekte und MVC-Ansichten.</span><span class="sxs-lookup"><span data-stu-id="0de40-181">In addition to *.exe* and *.dll* files, the *publish* folder for an ASP.NET Core app typically contains configuration files, static assets, and MVC views.</span></span> <span data-ttu-id="0de40-182">Weitere Informationen finden Sie unter <xref:host-and-deploy/directory-structure>.</span><span class="sxs-lookup"><span data-stu-id="0de40-182">For more information, see <xref:host-and-deploy/directory-structure>.</span></span>

## <a name="set-up-a-process-manager"></a><span data-ttu-id="0de40-183">Einrichten eines Prozessmanagers</span><span class="sxs-lookup"><span data-stu-id="0de40-183">Set up a process manager</span></span>

<span data-ttu-id="0de40-184">Bei einer ASP.NET Core-App handelt es sich um eine Konsolen-App, die gestartet werden muss, wenn ein Server startet und nach Abstürzen neu startet.</span><span class="sxs-lookup"><span data-stu-id="0de40-184">An ASP.NET Core app is a console app that must be started when a server boots and restarted if it crashes.</span></span> <span data-ttu-id="0de40-185">Sie benötigen einen Prozess-Manager, um die Starts und Neustarts zu automatisieren.</span><span class="sxs-lookup"><span data-stu-id="0de40-185">To automate starts and restarts, a process manager is required.</span></span> <span data-ttu-id="0de40-186">Die gängigsten Prozess-Manager für ASP.NET Core sind Folgende:</span><span class="sxs-lookup"><span data-stu-id="0de40-186">The most common process managers for ASP.NET Core are:</span></span>

* <span data-ttu-id="0de40-187">Linux</span><span class="sxs-lookup"><span data-stu-id="0de40-187">Linux</span></span>
  * [<span data-ttu-id="0de40-188">Nginx</span><span class="sxs-lookup"><span data-stu-id="0de40-188">Nginx</span></span>](xref:host-and-deploy/linux-nginx)
  * [<span data-ttu-id="0de40-189">Apache</span><span class="sxs-lookup"><span data-stu-id="0de40-189">Apache</span></span>](xref:host-and-deploy/linux-apache)
* <span data-ttu-id="0de40-190">Windows</span><span class="sxs-lookup"><span data-stu-id="0de40-190">Windows</span></span>
  * [<span data-ttu-id="0de40-191">IIS</span><span class="sxs-lookup"><span data-stu-id="0de40-191">IIS</span></span>](xref:host-and-deploy/iis/index)
  * [<span data-ttu-id="0de40-192">Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="0de40-192">Windows Service</span></span>](xref:host-and-deploy/windows-service)

## <a name="set-up-a-reverse-proxy"></a><span data-ttu-id="0de40-193">Einrichten eines Reverseproxys</span><span class="sxs-lookup"><span data-stu-id="0de40-193">Set up a reverse proxy</span></span>

<span data-ttu-id="0de40-194">Wenn die App den [Kestrel](xref:fundamentals/servers/kestrel) Server verwendet, können [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache) oder [IIS](xref:host-and-deploy/iis/index) als Reverseproxyserver verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-194">If the app uses the [Kestrel](xref:fundamentals/servers/kestrel) server, [Nginx](xref:host-and-deploy/linux-nginx), [Apache](xref:host-and-deploy/linux-apache), or [IIS](xref:host-and-deploy/iis/index) can be used as a reverse proxy server.</span></span> <span data-ttu-id="0de40-195">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="0de40-195">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

<span data-ttu-id="0de40-196">Jede der beiden Konfigurationen &mdash;mit oder ohne einen Reverseproxyserver&mdash; ist eine unterstützte Hostingkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="0de40-196">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration.</span></span> <span data-ttu-id="0de40-197">Weitere Informationen finden Sie unter [When to use Kestrel with a reverse proxy (Verwenden von Kestrel mit einem Reverseproxy)](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span><span class="sxs-lookup"><span data-stu-id="0de40-197">For more information, see [When to use Kestrel with a reverse proxy](xref:fundamentals/servers/kestrel#when-to-use-kestrel-with-a-reverse-proxy).</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="0de40-198">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="0de40-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="0de40-199">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-199">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="0de40-200">Ohne zusätzliche Konfiguration hat eine App möglicherweise keinen Zugriff auf das Schema (HTTP/HTTPS) und die Remote-IP-Adresse, von der eine Anforderung stammte.</span><span class="sxs-lookup"><span data-stu-id="0de40-200">Without additional configuration, an app might not have access to the scheme (HTTP/HTTPS) and the remote IP address where a request originated.</span></span> <span data-ttu-id="0de40-201">Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="0de40-201">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="use-visual-studio-and-msbuild-to-automate-deployments"></a><span data-ttu-id="0de40-202">Verwenden von Visual Studio und MSBuild zum Automatisieren der Bereitstellungen</span><span class="sxs-lookup"><span data-stu-id="0de40-202">Use Visual Studio and MSBuild to automate deployments</span></span>

<span data-ttu-id="0de40-203">Die Bereitstellung erfordert neben dem Kopieren der Ausgabe von [dotnet publish](/dotnet/core/tools/dotnet-publish) auf einen Server oft zusätzliche Aufgaben.</span><span class="sxs-lookup"><span data-stu-id="0de40-203">Deployment often requires additional tasks besides copying the output from [dotnet publish](/dotnet/core/tools/dotnet-publish) to a server.</span></span> <span data-ttu-id="0de40-204">Beispielsweise können zusätzliche Dateien aus dem Ordner *publish* erforderlich oder ausgeschlossen sein.</span><span class="sxs-lookup"><span data-stu-id="0de40-204">For example, extra files might be required or excluded from the *publish* folder.</span></span> <span data-ttu-id="0de40-205">Visual Studio verwendet MSBuild für die Webbereitstellung, und MSBuild kann für die Ausführung vieler weiterer Tasks während der Bereitstellung angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-205">Visual Studio uses MSBuild for web deployment, and MSBuild can be customized to do many other tasks during deployment.</span></span> <span data-ttu-id="0de40-206">Weitere Informationen finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles> und im Buch [Using MSBuild and Team Foundation Build (Verwenden von MSBuild und Team Foundation Build)](http://msbuildbook.com/).</span><span class="sxs-lookup"><span data-stu-id="0de40-206">For more information, see <xref:host-and-deploy/visual-studio-publish-profiles> and the [Using MSBuild and Team Foundation Build](http://msbuildbook.com/) book.</span></span>

<span data-ttu-id="0de40-207">Mithilfe des [Features zum Veröffentlichen einer Web-App](xref:tutorials/publish-to-azure-webapp-using-vs) oder der [integrierten Git-Unterstützung](xref:host-and-deploy/azure-apps/azure-continuous-deployment) können Apps direkt von Visual Studio in Azure App Service bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="0de40-207">By using [the Publish Web feature](xref:tutorials/publish-to-azure-webapp-using-vs) or [built-in Git support](xref:host-and-deploy/azure-apps/azure-continuous-deployment), apps can be deployed directly from Visual Studio to the Azure App Service.</span></span> <span data-ttu-id="0de40-208">Azure DevOps Services unterstützt [Continuous Deployment](/azure/devops/pipelines/targets/webapp) in Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="0de40-208">Azure DevOps Services supports [continuous deployment to Azure App Service](/azure/devops/pipelines/targets/webapp).</span></span> <span data-ttu-id="0de40-209">Weitere Informationen finden Sie unter [DevOps mit ASP.NET Core und Azure](xref:azure/devops/index).</span><span class="sxs-lookup"><span data-stu-id="0de40-209">For more information, see [DevOps with ASP.NET Core and Azure](xref:azure/devops/index).</span></span>

## <a name="publish-to-azure"></a><span data-ttu-id="0de40-210">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="0de40-210">Publish to Azure</span></span>

<span data-ttu-id="0de40-211">Anleitungen zum Veröffentlichen einer App in Azure mithilfe von Visual Studio finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="0de40-211">See <xref:tutorials/publish-to-azure-webapp-using-vs> for instructions on how to publish an app to Azure using Visual Studio.</span></span> <span data-ttu-id="0de40-212">Ein weiteres Beispiel wird unter [Erstellen von ASP.NET Core-Web-Apps in Azure](/azure/app-service/app-service-web-get-started-dotnet) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="0de40-212">An additional example is provided by [Create an ASP.NET Core web app in Azure](/azure/app-service/app-service-web-get-started-dotnet).</span></span>

## <a name="publish-with-msdeploy-on-windows"></a><span data-ttu-id="0de40-213">Veröffentlichen mit MSDeploy unter Windows</span><span class="sxs-lookup"><span data-stu-id="0de40-213">Publish with MSDeploy on Windows</span></span>

<span data-ttu-id="0de40-214">Anleitungen zum Veröffentlichen einer App mit einem Visual Studio-Veröffentlichungsprofil z.B. von einer Windows-Eingabeaufforderung mit dem Befehl [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) finden Sie unter <xref:host-and-deploy/visual-studio-publish-profiles>.</span><span class="sxs-lookup"><span data-stu-id="0de40-214">See <xref:host-and-deploy/visual-studio-publish-profiles> for instructions on how to publish an app with a Visual Studio publish profile, including from a Windows command prompt using the [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command.</span></span>

## <a name="internet-information-services-iis"></a><span data-ttu-id="0de40-215">IIS (Internet Information Services)</span><span class="sxs-lookup"><span data-stu-id="0de40-215">Internet Information Services (IIS)</span></span>

<span data-ttu-id="0de40-216">Informationen zu Bereitstellungen zu Internetinformationsdiensten (IIS) mit von der *web.config*-Datei bereitgestellter Konfiguration, finden Sie in den Artikeln unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="0de40-216">For deployments to Internet Information Services (IIS) with configuration provided by the *web.config* file, see the articles under <xref:host-and-deploy/iis/index>.</span></span>

## <a name="host-in-a-web-farm"></a><span data-ttu-id="0de40-217">Hosten in einer Webfarm</span><span class="sxs-lookup"><span data-stu-id="0de40-217">Host in a web farm</span></span>

<span data-ttu-id="0de40-218">Weitere Informationen zur Konfiguration des Hostings von ASP.NET Core-Apps in einer Webfarmumgebung (z.B. Bereitstellen mehrerer App-Instanzen zur besseren Skalierbarkeit) finden Sie unter <xref:host-and-deploy/web-farm>.</span><span class="sxs-lookup"><span data-stu-id="0de40-218">For information on configuration for hosting ASP.NET Core apps in a web farm environment (for example, deployment of multiple instances of your app for scalability), see <xref:host-and-deploy/web-farm>.</span></span>

## <a name="host-on-docker"></a><span data-ttu-id="0de40-219">Hosten auf Docker</span><span class="sxs-lookup"><span data-stu-id="0de40-219">Host on Docker</span></span>

<span data-ttu-id="0de40-220">Weitere Informationen finden Sie unter <xref:host-and-deploy/docker/index>.</span><span class="sxs-lookup"><span data-stu-id="0de40-220">For more information, see <xref:host-and-deploy/docker/index>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0de40-221">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="0de40-221">Additional resources</span></span>

* <xref:test/troubleshoot>
* [<span data-ttu-id="0de40-222">Hosten von ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0de40-222">ASP.NET Hosting</span></span>](https://dotnet.microsoft.com/apps/aspnet/hosting)

::: moniker-end
