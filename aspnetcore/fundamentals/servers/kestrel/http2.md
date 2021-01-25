---
title: Verwenden von HTTP/2 mit dem ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zur Verwendung von HTTP/2 mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253847"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="55a58-103">Verwenden von HTTP/2 mit dem ASP.NET Core-Kestrel-Webserver</span><span class="sxs-lookup"><span data-stu-id="55a58-103">Use HTTP/2 with the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="55a58-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:</span><span class="sxs-lookup"><span data-stu-id="55a58-104">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="55a58-105">Betriebssystem&dagger;</span><span class="sxs-lookup"><span data-stu-id="55a58-105">Operating system&dagger;</span></span>
  * <span data-ttu-id="55a58-106">Windows Server 2016/Windows 10 oder höher&Dagger;</span><span class="sxs-lookup"><span data-stu-id="55a58-106">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="55a58-107">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="55a58-107">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="55a58-108">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="55a58-108">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="55a58-109">[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)</span><span class="sxs-lookup"><span data-stu-id="55a58-109">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="55a58-110">TLS 1.2-Verbindung oder höher</span><span class="sxs-lookup"><span data-stu-id="55a58-110">TLS 1.2 or later connection</span></span>

<span data-ttu-id="55a58-111">&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="55a58-111">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="55a58-112">&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="55a58-112">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="55a58-113">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="55a58-113">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="55a58-114">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="55a58-114">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="55a58-115">Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)`HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="55a58-115">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) reports `HTTP/2`.</span></span>

<span data-ttu-id="55a58-116">Ab .NET Core 3.0 ist HTTP/2 standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="55a58-116">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="55a58-117">Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [HTTP/2-Einschränkungen](xref:fundamentals/servers/kestrel/options#http2-limits) und [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).</span><span class="sxs-lookup"><span data-stu-id="55a58-117">For more information on configuration, see the [Kestrel HTTP/2 limits](xref:fundamentals/servers/kestrel/options#http2-limits) and [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) sections.</span></span>

## <a name="advanced-http2-features"></a><span data-ttu-id="55a58-118">Erweiterte HTTP/2-Features</span><span class="sxs-lookup"><span data-stu-id="55a58-118">Advanced HTTP/2 features</span></span>

<span data-ttu-id="55a58-119">Hierbei handelt es sich um weitere HTTP/2-Features in Kestrel zur Unterstützung von gRPC, einschließlich der Unterstützung für Antwortnachspanne und das Senden von Frames zum Zurücksetzen.</span><span class="sxs-lookup"><span data-stu-id="55a58-119">Additional HTTP/2 features in Kestrel support gRPC, including support for response trailers and sending reset frames.</span></span>

### <a name="trailers"></a><span data-ttu-id="55a58-120">Trailer</span><span class="sxs-lookup"><span data-stu-id="55a58-120">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="55a58-121">Reset</span><span class="sxs-lookup"><span data-stu-id="55a58-121">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]
