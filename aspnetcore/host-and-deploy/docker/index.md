---
title: Hosten von ASP.NET Core in Docker-Containern
author: rick-anderson
description: Erkunden Sie die Links zu Ressourcen, um das Hosten von ASP.NET Core-Apps in Docker-Containern zu erlernen.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059779"
---
# <a name="host-aspnet-core-in-docker-containers"></a>Hosten von ASP.NET Core in Docker-Containern

Die folgenden Artikel sind zum Erlernen des Hostens von ASP.NET Core-Apps in Docker verfügbar:

[Introduction to Containers and Docker (Einführung in Container und Docker)](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
In diesem Artikel erfahren Sie mehr über das Containerisieren als Ansatz in der Softwareentwicklung, bei dem eine Anwendung oder ein Dienst sowie die zugehörigen Abhängigkeiten und Konfigurationen zusammen als Containerimage verpackt werden. Das Image kann getestet und anschließend auf einem Host bereitgestellt werden.

[Was ist Docker?](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
In diesem Artikel erfahren Sie mehr über Docker, einem Open Source-Projekt zur Automatisierung der Bereitstellung von Apps als mobile, eigenständige Container, die in der Cloud oder lokal ausgeführt werden können.

[Docker-Terminologie](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
In diesem Artikel lernen Sie Begriffe und Definitionen rund um die Docker-Technologie kennen.

[Docker containers, images, and registries (Docker-Container, -Images und -Registrierungen)](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
Erfahren Sie, wie Docker-Containerimages in einer Imageregistrierung gespeichert werden, um eine konsistente Bereitstellung in verschiedenen Umgebungen zu gewährleisten.

<xref:host-and-deploy/docker/building-net-docker-images> In diesem Artikel erfahren Sie, wie eine ASP.NET Core-App erstellt und in Docker bereitgestellt wird. Erkunden Sie Docker-Images, die von Microsoft verwaltet werden, und untersuchen Sie Anwendungsfälle.

[Visual Studio-Containertools](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
Erfahren Sie mehr über die Unterstützung für das Erstellen, Debuggen und Ausführen von ASP.NET Core-Apps in Visual Studio, die für .NET Framework oder .NET Core in Docker für Windows entwickelt wurden. Sowohl Windows- als auch Linux-Container werden unterstützt.

[Veröffentlichen in Azure Container Registry](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
Hier erfahren Sie, wie Sie die Erweiterung für Visual Studio-Containertools verwenden, um mithilfe von PowerShell eine ASP.NET Core-App für einen Docker-Host in Azure bereitzustellen.

[Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer)  
Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Ein Übergeben von Anforderungen über einen Proxy verschleiert häufig Informationen über die ursprüngliche Anforderung, z. B. das Schema und die Client-IP. Er kann erforderlich sein, dass einige Informationen über die Anforderung manuell an die App weitergeleitet werden.

[GC mit Docker und kleinen Containern](xref:performance/memory#sc) Erläutert die Auswahl der Garbage Collection mit kleinen Containern.