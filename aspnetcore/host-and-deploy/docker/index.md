---
title: Hosten von ASP.NET Core in Docker-Containern
author: rick-anderson
description: Erkunden Sie die Links zu Ressourcen, um das Hosten von ASP.NET Core-Apps in Docker-Containern zu erlernen.
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059779"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="3e390-103">Hosten von ASP.NET Core in Docker-Containern</span><span class="sxs-lookup"><span data-stu-id="3e390-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="3e390-104">Die folgenden Artikel sind zum Erlernen des Hostens von ASP.NET Core-Apps in Docker verfügbar:</span><span class="sxs-lookup"><span data-stu-id="3e390-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="3e390-105">Introduction to Containers and Docker (Einführung in Container und Docker)</span><span class="sxs-lookup"><span data-stu-id="3e390-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="3e390-106">In diesem Artikel erfahren Sie mehr über das Containerisieren als Ansatz in der Softwareentwicklung, bei dem eine Anwendung oder ein Dienst sowie die zugehörigen Abhängigkeiten und Konfigurationen zusammen als Containerimage verpackt werden.</span><span class="sxs-lookup"><span data-stu-id="3e390-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="3e390-107">Das Image kann getestet und anschließend auf einem Host bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="3e390-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="3e390-108">Was ist Docker?</span><span class="sxs-lookup"><span data-stu-id="3e390-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="3e390-109">In diesem Artikel erfahren Sie mehr über Docker, einem Open Source-Projekt zur Automatisierung der Bereitstellung von Apps als mobile, eigenständige Container, die in der Cloud oder lokal ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="3e390-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="3e390-110">Docker-Terminologie</span><span class="sxs-lookup"><span data-stu-id="3e390-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="3e390-111">In diesem Artikel lernen Sie Begriffe und Definitionen rund um die Docker-Technologie kennen.</span><span class="sxs-lookup"><span data-stu-id="3e390-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="3e390-112">Docker containers, images, and registries (Docker-Container, -Images und -Registrierungen)</span><span class="sxs-lookup"><span data-stu-id="3e390-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="3e390-113">Erfahren Sie, wie Docker-Containerimages in einer Imageregistrierung gespeichert werden, um eine konsistente Bereitstellung in verschiedenen Umgebungen zu gewährleisten.</span><span class="sxs-lookup"><span data-stu-id="3e390-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="3e390-114"><xref:host-and-deploy/docker/building-net-docker-images> In diesem Artikel erfahren Sie, wie eine ASP.NET Core-App erstellt und in Docker bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3e390-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="3e390-115">Erkunden Sie Docker-Images, die von Microsoft verwaltet werden, und untersuchen Sie Anwendungsfälle.</span><span class="sxs-lookup"><span data-stu-id="3e390-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="3e390-116">Visual Studio-Containertools</span><span class="sxs-lookup"><span data-stu-id="3e390-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="3e390-117">Erfahren Sie mehr über die Unterstützung für das Erstellen, Debuggen und Ausführen von ASP.NET Core-Apps in Visual Studio, die für .NET Framework oder .NET Core in Docker für Windows entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="3e390-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="3e390-118">Sowohl Windows- als auch Linux-Container werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3e390-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="3e390-119">Veröffentlichen in Azure Container Registry</span><span class="sxs-lookup"><span data-stu-id="3e390-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="3e390-120">Hier erfahren Sie, wie Sie die Erweiterung für Visual Studio-Containertools verwenden, um mithilfe von PowerShell eine ASP.NET Core-App für einen Docker-Host in Azure bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="3e390-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="3e390-121">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="3e390-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="3e390-122">Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="3e390-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="3e390-123">Ein Übergeben von Anforderungen über einen Proxy verschleiert häufig Informationen über die ursprüngliche Anforderung, z. B. das Schema und die Client-IP.</span><span class="sxs-lookup"><span data-stu-id="3e390-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="3e390-124">Er kann erforderlich sein, dass einige Informationen über die Anforderung manuell an die App weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="3e390-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="3e390-125">[GC mit Docker und kleinen Containern](xref:performance/memory#sc) Erläutert die Auswahl der Garbage Collection mit kleinen Containern.</span><span class="sxs-lookup"><span data-stu-id="3e390-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>