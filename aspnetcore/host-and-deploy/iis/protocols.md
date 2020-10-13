---
title: Verwenden von ASP.NET Core mit HTTP/2 in IIS
author: rick-anderson
description: Hier erfahren Sie, wie Sie HTTP/2-Features mit IIS verwenden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
no-loc:
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 1c3748eeead1b8cccdb6112150cf90fb5597b689
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2020
ms.locfileid: "91654344"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a><span data-ttu-id="3e4e5-103">Verwenden von ASP.NET Core mit HTTP/2 in IIS</span><span class="sxs-lookup"><span data-stu-id="3e4e5-103">Use ASP.NET Core with HTTP/2 on IIS</span></span>

<span data-ttu-id="3e4e5-104">Von [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="3e4e5-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="3e4e5-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden IIS-Bereitstellungsszenarien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="3e4e5-105">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following IIS deployment scenarios:</span></span>

* <span data-ttu-id="3e4e5-106">Windows Server 2016 oder höher/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="3e4e5-106">Windows Server 2016 or later / Windows 10 or later</span></span>
* <span data-ttu-id="3e4e5-107">IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="3e4e5-107">IIS 10 or later</span></span>
* <span data-ttu-id="3e4e5-108">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="3e4e5-108">TLS 1.2 or later connection</span></span>
* <span data-ttu-id="3e4e5-109">Beim [Out-of-Process-Hosting](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-109">When [hosting out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Public-facing edge server connections use HTTP/2, but the reverse proxy connection to the [Kestrel server](xref:fundamentals/servers/kestrel) uses HTTP/1.1.</span></span>

<span data-ttu-id="3e4e5-110">Für eine In-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-110">For an in-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span> <span data-ttu-id="3e4e5-111">Für eine Out-of-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/1.1`.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-111">For an out-of-process deployment when an HTTP/2 connection is established, [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="3e4e5-112">Weitere Informationen zu den In-Process- und Out-of-Process-Hostingmodellen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-112">For more information on the in-process and out-of-process hosting models, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<span data-ttu-id="3e4e5-113">HTTP/2 ist standardmäßig für HTTPS-/TLS-Verbindungen aktiviert.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-113">HTTP/2 is enabled by default for HTTPS/TLS connections.</span></span> <span data-ttu-id="3e4e5-114">Verbindungen führen ein Fallback auf HTTP/1.1 aus, wenn keine HTTP/2-Verbindung hergestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-114">Connections fall back to HTTP/1.1 if an HTTP/2 connection isn't established.</span></span> <span data-ttu-id="3e4e5-115">Weitere Informationen zur HTTP/2-Konfiguration mit IIS-Bereitstellungen finden Sie unter [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span><span class="sxs-lookup"><span data-stu-id="3e4e5-115">For more information on HTTP/2 configuration with IIS deployments, see [HTTP/2 on IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="3e4e5-116">Erweiterte HTTP/2-Features zur Unterstützung von gRPC</span><span class="sxs-lookup"><span data-stu-id="3e4e5-116">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="3e4e5-117">Zusätzliche HTTP/2-Features in IIS zur Unterstützung von gRPC, einschließlich der Unterstützung für Antwortnachspanne und das Senden von Frames zum Zurücksetzen</span><span class="sxs-lookup"><span data-stu-id="3e4e5-117">Additional HTTP/2 features in IIS support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="3e4e5-118">Anforderungen zum Ausführen von gRPC in IIS:</span><span class="sxs-lookup"><span data-stu-id="3e4e5-118">Requirements to run gRPC on IIS:</span></span>

* <span data-ttu-id="3e4e5-119">In-Process-Hosting</span><span class="sxs-lookup"><span data-stu-id="3e4e5-119">In-process hosting.</span></span>
* <span data-ttu-id="3e4e5-120">Windows 10, OS Build 20300.1000 oder höher.</span><span class="sxs-lookup"><span data-stu-id="3e4e5-120">Windows 10, OS Build 20300.1000 or later.</span></span> <span data-ttu-id="3e4e5-121">Erfordert möglicherweise die Verwendung von Windows Insider-Builds</span><span class="sxs-lookup"><span data-stu-id="3e4e5-121">May require use of Windows Insider Builds.</span></span>
* <span data-ttu-id="3e4e5-122">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="3e4e5-122">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="3e4e5-123">Trailer</span><span class="sxs-lookup"><span data-stu-id="3e4e5-123">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="3e4e5-124">Reset</span><span class="sxs-lookup"><span data-stu-id="3e4e5-124">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
