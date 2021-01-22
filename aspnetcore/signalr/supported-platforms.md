---
title: Unterstützte Plattformen für ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689226"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="bf799-103">Unterstützte Plattformen für ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="bf799-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="bf799-104">Systemanforderungen an Server</span><span class="sxs-lookup"><span data-stu-id="bf799-104">Server system requirements</span></span>

<span data-ttu-id="bf799-105">SignalR für ASP.net Core unterstützt jede Server Plattform, die von ASP.net Core unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="bf799-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="bf799-106">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="bf799-106">JavaScript client</span></span>

<span data-ttu-id="bf799-107">Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="bf799-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="bf799-108">Browser</span><span class="sxs-lookup"><span data-stu-id="bf799-108">Browser</span></span>                          | <span data-ttu-id="bf799-109">Version</span><span class="sxs-lookup"><span data-stu-id="bf799-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="bf799-110">Apple Safari, einschließlich iOS</span><span class="sxs-lookup"><span data-stu-id="bf799-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="bf799-111">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="bf799-111">Current&dagger;</span></span> |
| <span data-ttu-id="bf799-112">Google Chrome, einschließlich Android</span><span class="sxs-lookup"><span data-stu-id="bf799-112">Google Chrome, including Android</span></span> | <span data-ttu-id="bf799-113">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="bf799-113">Current&dagger;</span></span> |
| <span data-ttu-id="bf799-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="bf799-114">Microsoft Edge</span></span>                   | <span data-ttu-id="bf799-115">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="bf799-115">Current&dagger;</span></span> |
| <span data-ttu-id="bf799-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="bf799-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="bf799-117">Aktuell&dagger;</span><span class="sxs-lookup"><span data-stu-id="bf799-117">Current&dagger;</span></span> |

<span data-ttu-id="bf799-118">&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.</span><span class="sxs-lookup"><span data-stu-id="bf799-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="bf799-119">Internet Explorer und andere ältere Browser werden vom JavaScript-Client nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bf799-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="bf799-120">Der Client hat möglicherweise ein unerwartetes Verhalten und Fehler in nicht unterstützten Browsern.</span><span class="sxs-lookup"><span data-stu-id="bf799-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="bf799-121">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="bf799-121">.NET client</span></span>

<span data-ttu-id="bf799-122">Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bf799-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="bf799-123">[Xamarin- SignalR Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. zum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="bf799-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="bf799-124">Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher.</span><span class="sxs-lookup"><span data-stu-id="bf799-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="bf799-125">Andere Transporte werden auf allen Plattformen unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bf799-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="bf799-126">Java-Client</span><span class="sxs-lookup"><span data-stu-id="bf799-126">Java client</span></span>

<span data-ttu-id="bf799-127">Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.</span><span class="sxs-lookup"><span data-stu-id="bf799-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="bf799-128">Nicht unterstützte Clients</span><span class="sxs-lookup"><span data-stu-id="bf799-128">Unsupported clients</span></span>

<span data-ttu-id="bf799-129">Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell.</span><span class="sxs-lookup"><span data-stu-id="bf799-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="bf799-130">Sie werden derzeit nicht unterstützt und sind möglicherweise nie.</span><span class="sxs-lookup"><span data-stu-id="bf799-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="bf799-131">[C++-Client](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="bf799-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="bf799-132">[SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="bf799-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
