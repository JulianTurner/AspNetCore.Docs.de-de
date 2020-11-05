---
title: 'Überwachen und Debuggen: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Überwachen und Debuggen von Code als Teil einer DevOps-Lösung mit ASP.NET Core und Azure
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/monitor
ms.openlocfilehash: 74e789828bf5d54e3457f235657f8ed7086df80d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056750"
---
# <a name="monitor-and-debug"></a>Überwachen und Debuggen

Nachdem Sie die App bereitgestellt und eine DevOps-Pipeline erstellt haben, sollten Sie lernen, wie Sie die App überwachen und Probleme beheben können.

In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Suchen grundlegender Überwachungs- und Problembehandlungsdaten im Azure-Portal
* Erfahren, wie Azure Monitor einen tieferen Einblick in die Metriken aller Azure-Dienste bietet
* Verbinden der Web-App mit Application Insights für die App-Profilerstellung
* Aktivieren der Protokollierung und Informationen zum Herunterladen von Protokollen
* Streamen von Protokollen in Echtzeit
* Informationen zum Einrichten von Warnungen
* Erfahren Sie mehr über das Remotedebuggen der Web-Apps von Azure App Service.

## <a name="basic-monitoring-and-troubleshooting"></a>Grundlegende Überwachung und Problembehandlung

App Service-Web-Apps können problemlos in Echtzeit überwacht werden. Das Azure-Portal rendert Metriken in leicht verständlichen Diagrammen und Graphen.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie dann zum App Service *mywebapp\<unique_number\>* .

1. Auf der Registerkarte **Übersicht** werden nützliche Informationen „auf einen Blick“ angezeigt, einschließlich Diagrammen mit neuesten Metriken.

    ![Screenshot: Bereich „Übersicht“](./media/monitoring/overview.png)

    * **Http 5xx** : Anzahl serverseitiger Fehler, normalerweise Ausnahmen im ASP.NET Core.
    * **Eingehende Daten** : Eingehende Daten Ihrer Web-App.
    * **Ausgehende Daten** : Ausgehende Daten von Ihrer Web-App an Clients.
    * **Anforderungen** : Anzahl der HTTP-Anforderungen.
    * **Durchschnittliche Antwortzeit** : Die durchschnittliche Zeit, bis die Web-App auf HTTP-Anforderungen antwortet.

    Auf dieser Seite finden Sie auch mehrere Self-Service-Tools für die Problembehandlung und Optimierung.

    ![Screenshot: Self-Service-Tools](./media/monitoring/wizards.png)

    * **Diagnose und Problembehandlung** ist eine Self-Service-Problembehandlungsfunktion.
    * **Application Insights** dient der Profilerstellung zur Leistung und zum App-Verhalten und wird weiter unten in diesem Abschnitt erläutert.
    * **App Service-Ratgeber** nennt Empfehlungen zum Optimieren der Leistung Ihrer App.

## <a name="advanced-monitoring"></a>Erweiterte Überwachung

[Azure Monitor](/azure/monitoring-and-diagnostics/) ist der zentrale Dienst für das Überwachen aller Metriken und das Festlegen von Warnungen für Azure-Dienste. Innerhalb von Azure Monitor können Administratoren die Leistung genau nachverfolgen und Trends erkennen. Jeder Azure-Dienst bietet einen eigene [Metriken](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) für Azure Monitor.

## <a name="profile-with-application-insights"></a>Profilen mit Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) ist ein Azure-Dienst, mit dem Sie die Leistung und Stabilität von Web-Apps sowie deren Verwendung durch Benutzer analysieren können. Die Daten aus Application Insights sind breiter und tiefer als die Daten von Azure Monitor. Die Daten können Entwicklern und Administratoren wichtige Informationen zur Verbesserung von Apps bereitstellen. Application Insights kann ohne Codeänderungen zu einer Azure App Service-Ressource hinzugefügt werden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie dann zum App Service *mywebapp\<unique_number\>* .
1. Klicken Sie auf der Registerkarte **Übersicht** auf die Kachel **Application Insights**.

    ![Application Insights-Kachel](./media/monitoring/app-insights.png)

1. Wählen Sie das Optionsfeld **Neue Ressource erstellen** aus. Verwenden Sie den Standardressourcennamen, und wählen Sie den Speicherort für die Application Insights-Ressource aus. Der Speicherort muss nicht mit dem Speicherort Ihrer Web-App identisch sein.

    ![Application Insights-Einrichtung](./media/monitoring/new-app-insights.png)

1. Wählen Sie für **Runtime/Framework** **ASP.NET Core** aus. Übernehmen Sie die Standardeinstellungen.
1. Klicken Sie auf **OK**. Wenn Sie aufgefordert werden, die Auswahl zu bestätigen, klicken Sie auf **Weiter**.
1. Nachdem die Ressource erstellt wurde, klicken Sie auf den Namen der Application Insights-Ressource, um direkt zur Application Insights-Seite zu navigieren.

    ![Neue Application Insights-Ressource ist bereit](./media/monitoring/new-app-insights-done.png)

Mit der Verwendung der App werden Daten gesammelt. Wählen Sie **Aktualisieren** aus, um das Blatt mit neuen Daten neu zu laden.

![Application Insights-Registerkarte „Übersicht“](./media/monitoring/app-insights-overview.png)

Application Insights bietet nützliche serverseitige Informationen ohne zusätzliche Konfiguration. Um einen größtmöglichen Nutzen mit Application Insights zu erzielen, [instrumentieren Sie Ihre App mit dem Application Insights SDK](/azure/application-insights/app-insights-asp-net-core). Bei ordnungsgemäßer Konfiguration bietet der Dienst eine End-to-End-Überwachung auf dem Webserver und Browser, einschließlich der clientseitigen Leistung. Weitere Informationen finden Sie in der [Dokumentation zu Application Insights](/azure/application-insights/app-insights-overview).

## <a name="logging"></a>Protokollierung

Webserver- und App-Protokolle sind in Azure App Service standardmäßig deaktiviert. Aktivieren Sie die Protokolle mit den folgenden Schritten:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum App Service *mywebapp\<unique_number\>* .
1. Scrollen Sie im Menü auf der linken Seite nach unten zum Abschnitt **Überwachung**. Wählen Sie **Diagnoseprotokolle** aus.

    ![Link zu Diagnoseprotokollen](./media/monitoring/logging.png)

1. Aktivieren Sie die **Anwendungsprotokollierung (Dateisystem)** . Wenn Sie dazu aufgefordert werden, klicken Sie auf das Feld, um die Erweiterungen zum Aktivieren der App-Protokollierung in der Web-App zu installieren.
1. Legen Sie die **Webserverprotokollierung** auf **Dateisystem** fest.
1. Geben Sie den **Aufbewahrungszeitraum** in Tagen ein. Beispielsweise 30.
1. Klicken Sie auf **Speichern**.

Für die Web-App werden ASP.NET Core- und Webserver- (App Service-)Protokolle generiert. Sie können mithilfe der angezeigten FTP/FTPS-Informationen heruntergeladen werden. Das Kennwort entspricht den Anmeldeinformationen für die Bereitstellung, die weiter oben in dieser Anleitung erstellt wurden. Die Protokolle können [mit PowerShell oder der Azure CLI direkt auf Ihren lokalen Computer gestreamt werden](/azure/app-service/web-sites-enable-diagnostic-log#download). Protokolle können ferner [in Application Insights angezeigt](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights) werden.

## <a name="log-streaming"></a>Protokollstreaming

App- und Webserver-Protokolle können über das Portal in Echtzeit gestreamt werden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum App Service *mywebapp\<unique_number\>* .
1. Scrollen Sie im Menü auf der linken Seite nach unten zum Abschnitt **Überwachung** , und wählen Sie **Protokollstream** aus.

    ![Screenshot: Link zum Protokollstream](./media/monitoring/log-stream.png)

Protokolle können auch [über die Azure CLI oder Azure PowerShell gestreamt](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs) werden, einschließlich der Cloud Shell.

## <a name="alerts"></a>Benachrichtigungen

Azure Monitor bietet zudem [Echtzeitwarnungen](/azure/monitoring-and-diagnostics/insights-alerts-portal) basierend auf Metriken, administrativen Ereignissen und anderen Kriterien.

> *Hinweis: Zurzeit ist eine Warnung zu Web-App-Metriken nur im Dienst „Warnungen (klassisch)“ verfügbar.*

Den [Dienst „Warnungen (klassisch)“](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) finden Sie in Azure Monitor oder im Abschnitt **Überwachung** der App Service-Einstellungen.

![Link „Warnungen (klassisch)“](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>Live-Debuggen

Azure App Service kann [remote mit Visual Studio debuggt werden](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug), wenn die Protokolle nicht genügend Informationen bereitstellen. Für das Remotedebuggen muss die App jedoch mit Debugsymbolen kompiliert werden. Das Debuggen sollte nicht in der Produktionsumgebung erfolgen, außer als letztes Mittel.

## <a name="conclusion"></a>Schlussbemerkung

In diesem Abschnitt haben Sie die folgenden Aufgaben abgeschlossen:

* Suchen grundlegender Überwachungs- und Problembehandlungsdaten im Azure-Portal
* Erfahren, wie Azure Monitor einen tieferen Einblick in die Metriken aller Azure-Dienste bietet
* Verbinden der Web-App mit Application Insights für die App-Profilerstellung
* Aktivieren der Protokollierung und Informationen zum Herunterladen von Protokollen
* Streamen von Protokollen in Echtzeit
* Informationen zum Einrichten von Warnungen
* Erfahren Sie mehr über das Remotedebuggen der Web-Apps von Azure App Service.

## <a name="additional-reading"></a>Weiterführende Literatur

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [Überwachen der Leistung der Azure-Web-App mit Application Insights](/azure/application-insights/app-insights-azure-web-apps)
* [Aktivieren der Diagnoseprotokollierung für Apps in Azure App Service](/azure/app-service/web-sites-enable-diagnostic-log)
* [Problembehandlung in einer Web-App in Azure App Service mithilfe von Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Erstellen klassischer Metrikwarnungen in Azure Monitor für Azure-Dienste – Azure-Portal](/azure/monitoring-and-diagnostics/insights-alerts-portal)
