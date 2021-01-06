---
title: Neuerungen in ASP.NET Core 1.1
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 1.1.
ms.author: riande
ms.date: 12/18/2018
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
uid: aspnetcore-1.1
ms.openlocfilehash: cc891280a6314dbc4c0838d5b4a8d2a20698eab6
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059740"
---
# <a name="whats-new-in-aspnet-core-11"></a><span data-ttu-id="cd5ac-103">Neuerungen in ASP.NET Core 1.1</span><span class="sxs-lookup"><span data-stu-id="cd5ac-103">What's new in ASP.NET Core 1.1</span></span>

<span data-ttu-id="cd5ac-104">Die folgenden Funktionen sind in ASP.NET Core 1.1 neu:</span><span class="sxs-lookup"><span data-stu-id="cd5ac-104">ASP.NET Core 1.1 includes the following new features:</span></span>

- [<span data-ttu-id="cd5ac-105">URL-umschreibende Middleware</span><span class="sxs-lookup"><span data-stu-id="cd5ac-105">URL Rewriting Middleware</span></span>](xref:fundamentals/url-rewriting)
- [<span data-ttu-id="cd5ac-106">Antworten zwischenspeichernde Middleware</span><span class="sxs-lookup"><span data-stu-id="cd5ac-106">Response Caching Middleware</span></span>](xref:performance/caching/middleware)
- [<span data-ttu-id="cd5ac-107">Anzeigen von Komponenten als Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="cd5ac-107">View Components as Tag Helpers</span></span>](xref:mvc/views/view-components#invoking-a-view-component-as-a-tag-helper)
- [<span data-ttu-id="cd5ac-108">Middleware als MVC-Filter</span><span class="sxs-lookup"><span data-stu-id="cd5ac-108">Middleware as MVC filters</span></span>](xref:mvc/controllers/filters#using-middleware-in-the-filter-pipeline)
- [<span data-ttu-id="cd5ac-109">Cookie-basierter TempData-Anbieter</span><span class="sxs-lookup"><span data-stu-id="cd5ac-109">Cookie-based TempData provider</span></span>](xref:fundamentals/app-state#tempdata)
- [<span data-ttu-id="cd5ac-110">Azure App Service-Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="cd5ac-110">Azure App Service logging provider</span></span>](xref:fundamentals/logging/index#azure-app-service-provider)
- [<span data-ttu-id="cd5ac-111">Azure Key Vault-Konfigurationsanbieter</span><span class="sxs-lookup"><span data-stu-id="cd5ac-111">Azure Key Vault configuration provider</span></span>](xref:security/key-vault-configuration)
- [<span data-ttu-id="cd5ac-112">Schlüsselrepositorys zum Schutz von Azure und Redis-Speicher</span><span class="sxs-lookup"><span data-stu-id="cd5ac-112">Azure and Redis Storage Data Protection Key Repositories</span></span>](xref:security/data-protection/implementation/key-storage-providers)
- <span data-ttu-id="cd5ac-113">WebListener-Server für Windows</span><span class="sxs-lookup"><span data-stu-id="cd5ac-113">WebListener Server for Windows</span></span>
- [<span data-ttu-id="cd5ac-114">WebSockets-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="cd5ac-114">WebSockets support</span></span>](xref:fundamentals/websockets)

## <a name="choosing-between-versions-10-and-11-of-aspnet-core"></a><span data-ttu-id="cd5ac-115">Wählen zwischen den Versionen 1.0 und 1.1 von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd5ac-115">Choosing between versions 1.0 and 1.1 of ASP.NET Core</span></span>

<span data-ttu-id="cd5ac-116">ASP.NET Core 1.1 bietet mehr Funktionen als ASP.NET Core 1.0.</span><span class="sxs-lookup"><span data-stu-id="cd5ac-116">ASP.NET Core 1.1 has more features than ASP.NET Core 1.0.</span></span> <span data-ttu-id="cd5ac-117">Allgemeinen wird empfohlen, die neueste Version zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="cd5ac-117">In general, we recommend you use the latest version.</span></span>

## <a name="additional-information"></a><span data-ttu-id="cd5ac-118">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="cd5ac-118">Additional Information</span></span>

- [<span data-ttu-id="cd5ac-119">ASP.NET Core 1.1.0: Anmerkungen zu dieser Version</span><span class="sxs-lookup"><span data-stu-id="cd5ac-119">ASP.NET Core 1.1.0 Release Notes</span></span>](https://github.com/dotnet/aspnetcore/releases/tag/1.1.0)
- <span data-ttu-id="cd5ac-120">Wenn Sie über den Fortschritt und die Pläne des ASP.NET Core-Entwicklungsteams auf dem Laufenden bleiben möchten, sehen Sie sich das wöchentliche [ASP.NET Community Standup](https://live.asp.net/) an.</span><span class="sxs-lookup"><span data-stu-id="cd5ac-120">To connect with the ASP.NET Core development team's progress and plans, tune in to the [ASP.NET Community Standup](https://live.asp.net/).</span></span>
