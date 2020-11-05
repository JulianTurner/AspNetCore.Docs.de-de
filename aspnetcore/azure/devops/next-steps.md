---
title: Nächste Schritte – DevOps mit ASP.NET Core und Azure
author: CamSoper
description: Weitere Lernpfade für DevOps mit ASP.NET Core und Azure.
ms.author: casoper
ms.custom: devx-track-csharp, mvc, seodec18
ms.date: 10/24/2018
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
uid: azure/devops/next-steps
ms.openlocfilehash: 35b0486611cc15f25b1c8b54584c264e4c1298c9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056594"
---
# <a name="next-steps"></a><span data-ttu-id="5f095-103">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="5f095-103">Next steps</span></span>

<span data-ttu-id="5f095-104">In diesem Handbuch haben Sie eine DevOps-Pipeline für eine ASP.NET Core-Beispiel-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="5f095-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="5f095-105">Herzlichen Glückwunsch!</span><span class="sxs-lookup"><span data-stu-id="5f095-105">Congratulations!</span></span> <span data-ttu-id="5f095-106">Wir hoffen, Sie hatten Spaß zu Erfahren, wie ASP.NET Core-Web-Apps in Azure App Service veröffentlicht werden und wie die kontinuierliche Integration von Änderungen automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="5f095-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="5f095-107">Über das Webhosting und DevOps hinaus bietet Azure eine breite Palette von PaaS-Diensten (Platform-as-a-Service), die für ASP.NET Core-Entwickler nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="5f095-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="5f095-108">Dieser Abschnitt enthält eine kurze Übersicht über einige der am häufigsten verwendeten Dienste.</span><span class="sxs-lookup"><span data-stu-id="5f095-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="5f095-109">Speicher und Datenbanken</span><span class="sxs-lookup"><span data-stu-id="5f095-109">Storage and databases</span></span>

<span data-ttu-id="5f095-110">[Redis Cache](/azure/redis-cache/) ist ein Zwischenspeicher für Daten mit hohem Durchsatz und niedriger Wartezeit, der als Dienst zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="5f095-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="5f095-111">Er kann für die Zwischenspeicherung der Seitenausgabe, die Reduzierung von Datenbankanforderungen und die Bereitstellung des ASP.NET Core-Sitzungszustands über mehrere Instanzen einer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5f095-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="5f095-112">[Azure Storage](/azure/storage/) ist der extrem skalierbare Cloudspeicher von Azure.</span><span class="sxs-lookup"><span data-stu-id="5f095-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="5f095-113">Entwickler können die Vorteile von [Warteschlangenspeicher](/azure/storage/queues/storage-queues-introduction) für zuverlässiges Message Queuing nutzen, und [Tabellenspeicher](/azure/storage/tables/table-storage-overview) ist ein NoSQL-Schlüsselwertspeicher, der für die schnelle Entwicklung mit extremen, teilweise strukturierten Datasets konzipiert ist.</span><span class="sxs-lookup"><span data-stu-id="5f095-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="5f095-114">[Azure SQL-Datenbank](/azure/sql-database/) bietet bekannte relationale Datenbankfunktionen als Dienst unter Verwendung der Microsoft SQL Server Engine.</span><span class="sxs-lookup"><span data-stu-id="5f095-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="5f095-115">[Cosmos DB](/azure/cosmos-db/) ist ein global verteilter NoSQL-Datenbankdienst mit mehreren Modellen.</span><span class="sxs-lookup"><span data-stu-id="5f095-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="5f095-116">Es stehen mehrere APIs zur Verfügung, darunter SQL API (früher DocumentDB), Cassandra und MongoDB.</span><span class="sxs-lookup"><span data-stu-id="5f095-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## :::no-loc(Identity):::

<span data-ttu-id="5f095-117">[Azure Active Directory](/azure/active-directory/) und [Azure Active Directory B2C](/azure/active-directory-b2c/) sind beides Identitätsdienste.</span><span class="sxs-lookup"><span data-stu-id="5f095-117">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="5f095-118">Azure Active Directory ist für Unternehmensszenarien konzipiert und ermöglicht die Zusammenarbeit mit Azure AD B2B (Business-to-Business), während Azure Active Directory B2C für Business-to-Customer-Szenarien vorgesehen ist, einschließlich der Anmeldung in sozialen Netzwerken.</span><span class="sxs-lookup"><span data-stu-id="5f095-118">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="5f095-119">Mobil</span><span class="sxs-lookup"><span data-stu-id="5f095-119">Mobile</span></span>

<span data-ttu-id="5f095-120">[Notification Hubs](/azure/notification-hubs/) ist eine skalierbare Multiplattform-Engine für Pushbenachrichtigungen, um schnell Millionen von Nachrichten an Apps auf verschiedenen Geräten zu senden.</span><span class="sxs-lookup"><span data-stu-id="5f095-120">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="5f095-121">Webinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="5f095-121">Web infrastructure</span></span>

<span data-ttu-id="5f095-122">[Azure Container Service](/azure/aks/) verwaltet Ihre gehostete Kubernetes-Umgebung und ermöglicht so die schnelle und einfache Bereitstellung und Verwaltung von Container-Apps – ganz ohne Kenntnisse in Sachen Containerorchestrierung.</span><span class="sxs-lookup"><span data-stu-id="5f095-122">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="5f095-123">[Azure Search](/azure/search/) wird verwendet, um eine Unternehmenslösung für die Suche über private, heterogene Inhalte zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5f095-123">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="5f095-124">[Service Fabric](/azure/service-fabric/) ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices und Container vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="5f095-124">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
