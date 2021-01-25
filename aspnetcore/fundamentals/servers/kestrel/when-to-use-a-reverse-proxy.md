---
title: Anwendungsfälle für einen Reverseproxy mit dem Kestrel-Webserver für ASP.NET Core
author: rick-anderson
description: Hier erhalten Sie Informationen dazu, wann Sie einen Reverseproxy vor Kestrel verwenden sollten. Bei Kestrel handelt es sich um den plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2021
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
uid: fundamentals/servers/kestrel/when-to-use-a-reverse-proxy
ms.openlocfilehash: fc89a9f841403bbccedff0a9c0720a08c11abdd6
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253839"
---
# <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="cca16-103">Verwenden von Kestrel mit einem Reverseproxy</span><span class="sxs-lookup"><span data-stu-id="cca16-103">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="cca16-104">Kestrel kann eigenständig oder mit einem *Reverseproxyserver* wie z.B. [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cca16-104">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="cca16-105">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Netzwerk und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="cca16-105">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="cca16-106">Kestrel bei Verwendung als Webserver mit direkter Internetverbindung:</span><span class="sxs-lookup"><span data-stu-id="cca16-106">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](_static/kestrel-to-internet2.png)

<span data-ttu-id="cca16-108">Kestrel bei Verwendung in einer Reverseproxykonfiguration:</span><span class="sxs-lookup"><span data-stu-id="cca16-108">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](_static/kestrel-to-internet.png)

<span data-ttu-id="cca16-110">Jede der beiden Konfigurationen – mit oder ohne einen Reverseproxyserver – stellt eine unterstützte Hostingkonfiguration dar.</span><span class="sxs-lookup"><span data-stu-id="cca16-110">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="cca16-111">Wenn Kestrel als Edgeserver ohne Reverseproxyserver verwendet wird, wird das gemeinsame Nutzen derselben IP-Adresse und desselben Ports für mehrere Prozesse nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="cca16-111">When Kestrel is used as an edge server without a reverse proxy server, sharing of the same IP address and port among multiple processes is unsupported.</span></span> <span data-ttu-id="cca16-112">Wenn Kestrel für das Lauschen an einem Port konfiguriert ist, verarbeitet Kestrel den gesamten Datenverkehr für diesen Port unabhängig von den `Host`-Headern der Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="cca16-112">When Kestrel is configured to listen on a port, Kestrel handles all traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="cca16-113">Ein Reverseproxy, der Ports freigeben kann, kann Anforderungen an Kestrel über eine eindeutige IP und einen eindeutigen Port weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="cca16-113">A reverse proxy that can share ports can forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="cca16-114">Auch wenn kein Reverseproxyserver erforderlich ist, kann die Verwendung eines solchen empfehlenswert sein.</span><span class="sxs-lookup"><span data-stu-id="cca16-114">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="cca16-115">Für einen Reverseproxy gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="cca16-115">A reverse proxy:</span></span>

* <span data-ttu-id="cca16-116">Er kann die verfügbar gemachten öffentlichen Oberflächen der von ihm gehosteten Apps einschränken.</span><span class="sxs-lookup"><span data-stu-id="cca16-116">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="cca16-117">Er stellt eine zusätzliche Ebene für Konfiguration und Schutz bereit.</span><span class="sxs-lookup"><span data-stu-id="cca16-117">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="cca16-118">Er lässt sich besser in die vorhandene Infrastruktur integrieren.</span><span class="sxs-lookup"><span data-stu-id="cca16-118">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="cca16-119">Er vereinfacht die Konfiguration von Lastenausgleich und sicherer Kommunikation (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="cca16-119">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="cca16-120">Nur der Reverseproxyserver erfordert ein X.509-Zertifikat, und dieser Server kann mit den Servern der App im internen Netzwerk über einfaches HTTP kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="cca16-120">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="cca16-121">Das Hosting in einer Reverseproxykonfiguration erfordert [Hostfilterung](xref:fundamentals/servers/kestrel/host-filtering).</span><span class="sxs-lookup"><span data-stu-id="cca16-121">Hosting in a reverse proxy configuration requires [host filtering](xref:fundamentals/servers/kestrel/host-filtering).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cca16-122">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cca16-122">Additional resources</span></span>

<xref:host-and-deploy/proxy-load-balancer>

