---
title: 'Unterstützte Plattformen für ASP.NET Core :::no-loc(SignalR):::'
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core :::no-loc(SignalR):::.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/16/2020
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
uid: signalr/supported-platforms
ms.openlocfilehash: ee6e263fb5bef7bfb84587c3b0f04175eb8073cd
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051017"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="910d6-103">Unterstützte Plattformen für ASP.NET Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="910d6-103">ASP.NET Core :::no-loc(SignalR)::: supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="910d6-104">Systemanforderungen an Server</span><span class="sxs-lookup"><span data-stu-id="910d6-104">Server system requirements</span></span>

<span data-ttu-id="910d6-105">:::no-loc(SignalR)::: für ASP.net Core unterstützt jede Server Plattform, die von ASP.net Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="910d6-105">:::no-loc(SignalR)::: for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="910d6-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="910d6-106">JavaScript client</span></span>

<span data-ttu-id="910d6-107">Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="910d6-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="910d6-108">Browser</span><span class="sxs-lookup"><span data-stu-id="910d6-108">Browser</span></span>                          | <span data-ttu-id="910d6-109">Version</span><span class="sxs-lookup"><span data-stu-id="910d6-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="910d6-110">Apple Safari, einschließlich iOS</span><span class="sxs-lookup"><span data-stu-id="910d6-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="910d6-111">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="910d6-111">Current&dagger;</span></span> |
| <span data-ttu-id="910d6-112">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="910d6-112">Google Chrome, including Android</span></span> | <span data-ttu-id="910d6-113">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="910d6-113">Current&dagger;</span></span> |
| <span data-ttu-id="910d6-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="910d6-114">Microsoft Edge</span></span>                   | <span data-ttu-id="910d6-115">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="910d6-115">Current&dagger;</span></span> |
| <span data-ttu-id="910d6-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="910d6-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="910d6-117">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="910d6-117">Current&dagger;</span></span> |

<span data-ttu-id="910d6-118">&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="910d6-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="910d6-119">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="910d6-119">.NET client</span></span>

<span data-ttu-id="910d6-120">Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="910d6-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="910d6-121">[Xamarin- :::no-loc(SignalR)::: Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. zum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="910d6-121">For example, [Xamarin developers can use :::no-loc(SignalR):::](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="910d6-122">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="910d6-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="910d6-123">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="910d6-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="910d6-124">Java-Client</span><span class="sxs-lookup"><span data-stu-id="910d6-124">Java client</span></span>

<span data-ttu-id="910d6-125">Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="910d6-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="910d6-126">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="910d6-126">Unsupported clients</span></span>

<span data-ttu-id="910d6-127">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="910d6-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="910d6-128">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="910d6-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="910d6-129">[C++-Client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="910d6-129">[C++ client](https://github.com/aspnet/:::no-loc(SignalR):::-Client-Cpp)</span></span>

* <span data-ttu-id="910d6-130">[SWIFT-Client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="910d6-130">[Swift client](https://github.com/moozzyk/:::no-loc(SignalR):::-Client-Swift)</span></span>
