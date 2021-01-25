---
title: Hostfilterung mit dem Kestrel-Webserver für ASP.NET Core
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zur Verwendung der Hostfilterung mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
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
uid: fundamentals/servers/kestrel/host-filtering
ms.openlocfilehash: d55c211f05a77f6acabedef2ff62a621d9a1844e
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253850"
---
# <a name="host-filtering-with-aspnet-core-kestrel-web-server"></a><span data-ttu-id="33199-103">Hostfilterung mit dem Kestrel-Webserver für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="33199-103">Host filtering with ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="33199-104">Obwohl Kestrel die Konfiguration basierend auf Präfixe wie `http://example.com:5000` unterstützt, ignoriert Kestrel den Hostnamen weitgehend.</span><span class="sxs-lookup"><span data-stu-id="33199-104">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="33199-105">Der Host `localhost` ist ein Sonderfall, der für die Bindung an Loopback-Adressen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="33199-105">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="33199-106">Jeder Host, der keine explizite IP-Adresse ist, wird an alle öffentlichen IP-Adressen gebunden.</span><span class="sxs-lookup"><span data-stu-id="33199-106">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="33199-107">`Host`-Header werden nicht überprüft.</span><span class="sxs-lookup"><span data-stu-id="33199-107">`Host` headers aren't validated.</span></span>

<span data-ttu-id="33199-108">Verwenden Sie Middleware zum Filtern von Hosts, um dieses Problem zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="33199-108">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="33199-109">Die Middleware zum Filtern von Hosts wird durch das [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering)-Paket bereitgestellt, das implizit für ASP.NET Core-Apps zur Verfügung steht.</span><span class="sxs-lookup"><span data-stu-id="33199-109">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="33199-110">Die Middleware wird von <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> hinzugefügt, wodurch <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A> aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="33199-110">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering%2A>:</span></span>

[!code-csharp[](samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="33199-111">Die Middleware zum Filtern von Hosts ist standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="33199-111">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="33199-112">Wenn Sie die Middleware aktivieren möchten, definieren Sie einen `AllowedHosts`-Schlüssel in *appsettings.json* /*appsettings.\<EnvironmentName>json*.</span><span class="sxs-lookup"><span data-stu-id="33199-112">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="33199-113">Der Wert ist eine durch Semikolons getrennte Liste von Hostnamen ohne Portnummern:</span><span class="sxs-lookup"><span data-stu-id="33199-113">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="33199-114">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="33199-114">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="33199-115">[Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) hat auch eine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>-Option.</span><span class="sxs-lookup"><span data-stu-id="33199-115">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="33199-116">Middleware für weitergeleitete Header und Middleware zum Filtern von Hosts besitzen ähnliche Funktionen für unterschiedliche Szenarios.</span><span class="sxs-lookup"><span data-stu-id="33199-116">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="33199-117">Legen Sie `AllowedHosts` mit Middleware für weitergeleitete Header fest, wenn der `Host`-Header beim Weiterleiten von Anforderungen mit einem Reverseproxyserver oder einem Lastenausgleichsmodul nicht beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="33199-117">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="33199-118">Legen Sie `AllowedHosts` mit Middleware zum Filtern von Hosts fest, wenn Kestrel als öffentlicher Edgeserver verwendet oder der `Host`-Header direkt weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="33199-118">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="33199-119">Weitere Informationen zu Middleware für weitergeleitete Header finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="33199-119">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>
