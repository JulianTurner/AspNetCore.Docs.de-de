---
title: 'Überwachen und Debuggen: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Überwachen und Debuggen von Code als Teil einer DevOps-Lösung mit ASP.NET Core und Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 07/10/2019
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056750"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="911e7-103">Überwachen und Debuggen</span><span class="sxs-lookup"><span data-stu-id="911e7-103">Monitor and debug</span></span>

<span data-ttu-id="911e7-104">Nachdem Sie die App bereitgestellt und eine DevOps-Pipeline erstellt haben, sollten Sie lernen, wie Sie die App überwachen und Probleme beheben können.</span><span class="sxs-lookup"><span data-stu-id="911e7-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="911e7-105">In diesem Abschnitt führen Sie die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="911e7-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="911e7-106">Suchen grundlegender Überwachungs- und Problembehandlungsdaten im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="911e7-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="911e7-107">Erfahren, wie Azure Monitor einen tieferen Einblick in die Metriken aller Azure-Dienste bietet</span><span class="sxs-lookup"><span data-stu-id="911e7-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="911e7-108">Verbinden der Web-App mit Application Insights für die App-Profilerstellung</span><span class="sxs-lookup"><span data-stu-id="911e7-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="911e7-109">Aktivieren der Protokollierung und Informationen zum Herunterladen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="911e7-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="911e7-110">Streamen von Protokollen in Echtzeit</span><span class="sxs-lookup"><span data-stu-id="911e7-110">Stream logs in real time</span></span>
* <span data-ttu-id="911e7-111">Informationen zum Einrichten von Warnungen</span><span class="sxs-lookup"><span data-stu-id="911e7-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="911e7-112">Erfahren Sie mehr über das Remotedebuggen der Web-Apps von Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="911e7-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="911e7-113">Grundlegende Überwachung und Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="911e7-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="911e7-114">App Service-Web-Apps können problemlos in Echtzeit überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="911e7-115">Das Azure-Portal rendert Metriken in leicht verständlichen Diagrammen und Graphen.</span><span class="sxs-lookup"><span data-stu-id="911e7-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="911e7-116">Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie dann zum App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="911e7-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="911e7-117">Auf der Registerkarte **Übersicht** werden nützliche Informationen „auf einen Blick“ angezeigt, einschließlich Diagrammen mit neuesten Metriken.</span><span class="sxs-lookup"><span data-stu-id="911e7-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![Screenshot: Bereich „Übersicht“](./media/monitoring/overview.png)

    * <span data-ttu-id="911e7-119">**Http 5xx** : Anzahl serverseitiger Fehler, normalerweise Ausnahmen im ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="911e7-119">**Http 5xx** : Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="911e7-120">**Eingehende Daten** : Eingehende Daten Ihrer Web-App.</span><span class="sxs-lookup"><span data-stu-id="911e7-120">**Data In** : Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="911e7-121">**Ausgehende Daten** : Ausgehende Daten von Ihrer Web-App an Clients.</span><span class="sxs-lookup"><span data-stu-id="911e7-121">**Data Out** : Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="911e7-122">**Anforderungen** : Anzahl der HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="911e7-122">**Requests** : Count of HTTP requests.</span></span>
    * <span data-ttu-id="911e7-123">**Durchschnittliche Antwortzeit** : Die durchschnittliche Zeit, bis die Web-App auf HTTP-Anforderungen antwortet.</span><span class="sxs-lookup"><span data-stu-id="911e7-123">**Average Response Time** : Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="911e7-124">Auf dieser Seite finden Sie auch mehrere Self-Service-Tools für die Problembehandlung und Optimierung.</span><span class="sxs-lookup"><span data-stu-id="911e7-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![Screenshot: Self-Service-Tools](./media/monitoring/wizards.png)

    * <span data-ttu-id="911e7-126">**Diagnose und Problembehandlung** ist eine Self-Service-Problembehandlungsfunktion.</span><span class="sxs-lookup"><span data-stu-id="911e7-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="911e7-127">**Application Insights** dient der Profilerstellung zur Leistung und zum App-Verhalten und wird weiter unten in diesem Abschnitt erläutert.</span><span class="sxs-lookup"><span data-stu-id="911e7-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="911e7-128">**App Service-Ratgeber** nennt Empfehlungen zum Optimieren der Leistung Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="911e7-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="911e7-129">Erweiterte Überwachung</span><span class="sxs-lookup"><span data-stu-id="911e7-129">Advanced monitoring</span></span>

<span data-ttu-id="911e7-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) ist der zentrale Dienst für das Überwachen aller Metriken und das Festlegen von Warnungen für Azure-Dienste.</span><span class="sxs-lookup"><span data-stu-id="911e7-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="911e7-131">Innerhalb von Azure Monitor können Administratoren die Leistung genau nachverfolgen und Trends erkennen.</span><span class="sxs-lookup"><span data-stu-id="911e7-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="911e7-132">Jeder Azure-Dienst bietet einen eigene [Metriken](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) für Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="911e7-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="911e7-133">Profilen mit Application Insights</span><span class="sxs-lookup"><span data-stu-id="911e7-133">Profile with Application Insights</span></span>

<span data-ttu-id="911e7-134">[Application Insights](/azure/application-insights/app-insights-overview) ist ein Azure-Dienst, mit dem Sie die Leistung und Stabilität von Web-Apps sowie deren Verwendung durch Benutzer analysieren können.</span><span class="sxs-lookup"><span data-stu-id="911e7-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="911e7-135">Die Daten aus Application Insights sind breiter und tiefer als die Daten von Azure Monitor.</span><span class="sxs-lookup"><span data-stu-id="911e7-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="911e7-136">Die Daten können Entwicklern und Administratoren wichtige Informationen zur Verbesserung von Apps bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="911e7-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="911e7-137">Application Insights kann ohne Codeänderungen zu einer Azure App Service-Ressource hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="911e7-138">Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie dann zum App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="911e7-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="911e7-139">Klicken Sie auf der Registerkarte **Übersicht** auf die Kachel **Application Insights**.</span><span class="sxs-lookup"><span data-stu-id="911e7-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights-Kachel](./media/monitoring/app-insights.png)

1. <span data-ttu-id="911e7-141">Wählen Sie das Optionsfeld **Neue Ressource erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="911e7-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="911e7-142">Verwenden Sie den Standardressourcennamen, und wählen Sie den Speicherort für die Application Insights-Ressource aus.</span><span class="sxs-lookup"><span data-stu-id="911e7-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="911e7-143">Der Speicherort muss nicht mit dem Speicherort Ihrer Web-App identisch sein.</span><span class="sxs-lookup"><span data-stu-id="911e7-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights-Einrichtung](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="911e7-145">Wählen Sie für **Runtime/Framework** **ASP.NET Core** aus.</span><span class="sxs-lookup"><span data-stu-id="911e7-145">For **Runtime/Framework** , select **ASP.NET Core**.</span></span> <span data-ttu-id="911e7-146">Übernehmen Sie die Standardeinstellungen.</span><span class="sxs-lookup"><span data-stu-id="911e7-146">Accept the default settings.</span></span>
1. <span data-ttu-id="911e7-147">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="911e7-147">Select **OK**.</span></span> <span data-ttu-id="911e7-148">Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="911e7-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="911e7-149">Nachdem die Ressource erstellt wurde, klicken Sie auf den Namen der Application Insights-Ressource, um direkt zur Application Insights-Seite zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="911e7-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![Neue Application Insights-Ressource ist bereit](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="911e7-151">Mit der Verwendung der App werden Daten gesammelt.</span><span class="sxs-lookup"><span data-stu-id="911e7-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="911e7-152">Wählen Sie **Aktualisieren** aus, um das Blatt mit neuen Daten neu zu laden.</span><span class="sxs-lookup"><span data-stu-id="911e7-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights-Registerkarte „Übersicht“](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="911e7-154">Application Insights bietet nützliche serverseitige Informationen ohne zusätzliche Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="911e7-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="911e7-155">Um einen größtmöglichen Nutzen mit Application Insights zu erzielen, [instrumentieren Sie Ihre App mit dem Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="911e7-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="911e7-156">Bei ordnungsgemäßer Konfiguration bietet der Dienst eine End-to-End-Überwachung auf dem Webserver und Browser, einschließlich der clientseitigen Leistung.</span><span class="sxs-lookup"><span data-stu-id="911e7-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="911e7-157">Weitere Informationen finden Sie in der [Dokumentation zu Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="911e7-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="911e7-158">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="911e7-158">Logging</span></span>

<span data-ttu-id="911e7-159">Webserver- und App-Protokolle sind in Azure App Service standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="911e7-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="911e7-160">Aktivieren Sie die Protokolle mit den folgenden Schritten:</span><span class="sxs-lookup"><span data-stu-id="911e7-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="911e7-161">Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="911e7-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="911e7-162">Scrollen Sie im Menü auf der linken Seite nach unten zum Abschnitt **Überwachung**.</span><span class="sxs-lookup"><span data-stu-id="911e7-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="911e7-163">Wählen Sie **Diagnoseprotokolle** aus.</span><span class="sxs-lookup"><span data-stu-id="911e7-163">Select **Diagnostics logs**.</span></span>

    ![Link zu Diagnoseprotokollen](./media/monitoring/logging.png)

1. <span data-ttu-id="911e7-165">Aktivieren Sie die **Anwendungsprotokollierung (Dateisystem)** .</span><span class="sxs-lookup"><span data-stu-id="911e7-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="911e7-166">Wenn Sie dazu aufgefordert werden, klicken Sie auf das Feld, um die Erweiterungen zum Aktivieren der App-Protokollierung in der Web-App zu installieren.</span><span class="sxs-lookup"><span data-stu-id="911e7-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="911e7-167">Legen Sie die **Webserverprotokollierung** auf **Dateisystem** fest.</span><span class="sxs-lookup"><span data-stu-id="911e7-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="911e7-168">Geben Sie den **Aufbewahrungszeitraum** in Tagen ein.</span><span class="sxs-lookup"><span data-stu-id="911e7-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="911e7-169">Beispielsweise 30.</span><span class="sxs-lookup"><span data-stu-id="911e7-169">For example, 30.</span></span>
1. <span data-ttu-id="911e7-170">Klicken Sie auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="911e7-170">Click **Save**.</span></span>

<span data-ttu-id="911e7-171">Für die Web-App werden ASP.NET Core- und Webserver- (App Service-)Protokolle generiert.</span><span class="sxs-lookup"><span data-stu-id="911e7-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="911e7-172">Sie können mithilfe der angezeigten FTP/FTPS-Informationen heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="911e7-173">Das Kennwort entspricht den Anmeldeinformationen für die Bereitstellung, die weiter oben in dieser Anleitung erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="911e7-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="911e7-174">Die Protokolle können [mit PowerShell oder der Azure CLI direkt auf Ihren lokalen Computer gestreamt werden](/azure/app-service/web-sites-enable-diagnostic-log#download).</span><span class="sxs-lookup"><span data-stu-id="911e7-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="911e7-175">Protokolle können ferner [in Application Insights angezeigt](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights) werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="911e7-176">Protokollstreaming</span><span class="sxs-lookup"><span data-stu-id="911e7-176">Log streaming</span></span>

<span data-ttu-id="911e7-177">App- und Webserver-Protokolle können über das Portal in Echtzeit gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="911e7-178">Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum App Service *mywebapp\<unique_number\>* .</span><span class="sxs-lookup"><span data-stu-id="911e7-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="911e7-179">Scrollen Sie im Menü auf der linken Seite nach unten zum Abschnitt **Überwachung** , und wählen Sie **Protokollstream** aus.</span><span class="sxs-lookup"><span data-stu-id="911e7-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![Screenshot: Link zum Protokollstream](./media/monitoring/log-stream.png)

<span data-ttu-id="911e7-181">Protokolle können auch [über die Azure CLI oder Azure PowerShell gestreamt](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs) werden, einschließlich der Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="911e7-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="911e7-182">Benachrichtigungen</span><span class="sxs-lookup"><span data-stu-id="911e7-182">Alerts</span></span>

<span data-ttu-id="911e7-183">Azure Monitor bietet zudem [Echtzeitwarnungen](/azure/monitoring-and-diagnostics/insights-alerts-portal) basierend auf Metriken, administrativen Ereignissen und anderen Kriterien.</span><span class="sxs-lookup"><span data-stu-id="911e7-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="911e7-184">*Hinweis: Zurzeit ist eine Warnung zu Web-App-Metriken nur im Dienst „Warnungen (klassisch)“ verfügbar.*</span><span class="sxs-lookup"><span data-stu-id="911e7-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="911e7-185">Den [Dienst „Warnungen (klassisch)“](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) finden Sie in Azure Monitor oder im Abschnitt **Überwachung** der App Service-Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="911e7-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![Link „Warnungen (klassisch)“](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="911e7-187">Live-Debuggen</span><span class="sxs-lookup"><span data-stu-id="911e7-187">Live debugging</span></span>

<span data-ttu-id="911e7-188">Azure App Service kann [remote mit Visual Studio debuggt werden](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug), wenn die Protokolle nicht genügend Informationen bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="911e7-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="911e7-189">Für das Remotedebuggen muss die App jedoch mit Debugsymbolen kompiliert werden.</span><span class="sxs-lookup"><span data-stu-id="911e7-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="911e7-190">Das Debuggen sollte nicht in der Produktionsumgebung erfolgen, außer als letztes Mittel.</span><span class="sxs-lookup"><span data-stu-id="911e7-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="911e7-191">Schlussbemerkung</span><span class="sxs-lookup"><span data-stu-id="911e7-191">Conclusion</span></span>

<span data-ttu-id="911e7-192">In diesem Abschnitt haben Sie die folgenden Aufgaben abgeschlossen:</span><span class="sxs-lookup"><span data-stu-id="911e7-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="911e7-193">Suchen grundlegender Überwachungs- und Problembehandlungsdaten im Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="911e7-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="911e7-194">Erfahren, wie Azure Monitor einen tieferen Einblick in die Metriken aller Azure-Dienste bietet</span><span class="sxs-lookup"><span data-stu-id="911e7-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="911e7-195">Verbinden der Web-App mit Application Insights für die App-Profilerstellung</span><span class="sxs-lookup"><span data-stu-id="911e7-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="911e7-196">Aktivieren der Protokollierung und Informationen zum Herunterladen von Protokollen</span><span class="sxs-lookup"><span data-stu-id="911e7-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="911e7-197">Streamen von Protokollen in Echtzeit</span><span class="sxs-lookup"><span data-stu-id="911e7-197">Stream logs in real time</span></span>
* <span data-ttu-id="911e7-198">Informationen zum Einrichten von Warnungen</span><span class="sxs-lookup"><span data-stu-id="911e7-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="911e7-199">Erfahren Sie mehr über das Remotedebuggen der Web-Apps von Azure App Service.</span><span class="sxs-lookup"><span data-stu-id="911e7-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="911e7-200">Weiterführende Literatur</span><span class="sxs-lookup"><span data-stu-id="911e7-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="911e7-201">Überwachen der Leistung der Azure-Web-App mit Application Insights</span><span class="sxs-lookup"><span data-stu-id="911e7-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="911e7-202">Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="911e7-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="911e7-203">Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio</span><span class="sxs-lookup"><span data-stu-id="911e7-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="911e7-204">Erstellen klassischer Metrikwarnungen in Azure Monitor für Azure-Dienste – Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="911e7-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
