---
title: DevOps mit ASP.NET Core und Azure
author: CamSoper
description: Ein Leitfaden, der End-to-End-Anleitungen zum Erstellen einer DevOps-Pipeline für eine in Azure gehostete ASP.NET Core-App bereitstellt.
ms.author: casoper
ms.date: 08/07/2018
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
uid: azure/devops/index
ms.openlocfilehash: 10a6bae73743d3063ad2e1ab49fc418ad1d63756
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052291"
---
# <a name="devops-with-aspnet-core-and-azure"></a>DevOps mit ASP.NET Core und Azure

[![Titelbild](./media/cover-large.png)](https://aka.ms/devopsbook)

Von [Cam Soper](https://twitter.com/camsoper) und [Scott Addie](https://twitter.com/scottaddie)

Dieser Leitfaden ist als [herunterladbares E-Book im PDF-Format](https://aka.ms/devopsbook) verfügbar.

## <a name="welcome"></a>Willkommen 

Willkommen zum Leitfaden für den Azure-Entwicklungslebenszyklus für .NET. In diesem Leitfaden werden die grundlegenden Konzepte zum Erstellen eines Entwicklungslebenszyklus für Azure mithilfe von .NET-Tools und -Prozessen vorgestellt. Nach Abschluss dieses Leitfadens können Sie die Vorteile einer ausgereiften DevOps-Toolkette nutzen.

## <a name="who-this-guide-is-for"></a>Für wen ist dieser Leitfaden gedacht?

Sie sollten ein erfahrener ASP.NET Core-Entwickler (auf Ebene 200–300) sein. Sie müssen nicht mit Azure vertraut sein, da dieses Thema in der Einführung behandelt wird. DevOps-Techniker, die sich eher auf die Vorgänge als auf die Entwicklung konzentrieren, können ebenso von diesem Leitfaden profitieren.

Dieser Leitfaden ist für Windows-Entwickler konzipiert. Linux und macOS werden jedoch vollständig von .NET Core unterstützt. Um diesen Leitfaden für Linux bzw. macOS zu nutzen, beachten Sie die Anzeigen für Linux-/macOS-Unterschiede.

## <a name="what-this-guide-doesnt-cover"></a>Was in diesem Leitfaden nicht behandelt wird

Er bezieht sich auf eine kontinuierliche End-to-End-Bereitstellungserfahrung für .NET-Entwickler. Es handelt sich nicht um eine vollständige Anleitung für alle Azure-Themen sowie für .NET-APIs für Azure-Dienste. Der Schwerpunkt liegt vor allem bei der Continuous Integration, der Bereitstellung, der Überwachung und dem Debuggen. Gegen Ende dieses Leitfadens finden Sie Empfehlungen für weitere Schritte. Darin enthalten sind Azure-Plattformdienste, die für ASP.NET Core-Entwickler nützlich sind.

## <a name="whats-in-this-guide"></a>In diesem Handbuch

### <a name="tools-and-downloads"></a>[Tools und Downloads](xref:azure/devops/tools-and-downloads)

Erfahren Sie, wo die in diesem Leitfaden verwendeten Tools abgerufen werden können.

### <a name="deploy-to-app-service"></a>[Bereitstellen in App Service](xref:azure/devops/deploy-to-app-service)

Informationen zu den verschiedenen Methoden zum Bereitstellen einer ASP.NET Core-App zu Azure App Service.

### <a name="continuous-integration-and-deployment"></a>[Continuous Integration und Continuous Deployment](xref:azure/devops/cicd)

Stellen Sie eine End-to-End-Lösung für Continuous Integration und Continuous Deployment für Ihre ASP.NET Core-App mit GitHub, Azure DevOps Services und Azure bereit.

### <a name="monitor-and-debug"></a>[Überwachen und Debuggen](xref:azure/devops/monitor)

Verwenden Sie Azure-Tools zum Überwachen, Optimieren und für die Problembehandlung Ihrer Anwendung.

### <a name="next-steps"></a>[Nächste Schritte](xref:azure/devops/next-steps)

Andere Lernpfade für die ASP.NET Core-Entwickler, die sich mit Azure vertraut machen.

## <a name="additional-introductory-reading"></a>Weiterführende einführende Literatur

Wenn Sie zum ersten Mal mit Cloud Computing arbeiten, können Sie in folgenden Artikeln die Grundlagen dazu nachlesen.

* [Was ist Cloud Computing?](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [Examples of Cloud Computing (Beispiele für Cloud Computing)](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [Was ist IaaS?](https://azure.microsoft.com/overview/what-is-iaas/)
* [Was ist PaaS?](https://azure.microsoft.com/overview/what-is-paas/)
