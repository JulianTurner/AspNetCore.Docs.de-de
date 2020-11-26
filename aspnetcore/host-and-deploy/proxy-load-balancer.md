---
title: Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich
author: rick-anderson
description: Informationen zur Konfiguration von hinter Proxyservern und Lastenausgleichsmodulen gehosteten Apps, wobei wichtige Anforderungsinformationen häufig verdeckt werden
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: 461f6d2105d38c5dbea2f8cf479e027c2edede14
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024941"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="97c59-103">Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="97c59-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="97c59-104">Von [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="97c59-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="97c59-105">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="97c59-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="97c59-106">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="97c59-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="97c59-107">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="97c59-108">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="97c59-109">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="97c59-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="97c59-110">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="97c59-110">Forwarded headers</span></span>

<span data-ttu-id="97c59-111">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="97c59-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="97c59-112">Header</span><span class="sxs-lookup"><span data-stu-id="97c59-112">Header</span></span> | <span data-ttu-id="97c59-113">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="97c59-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="97c59-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="97c59-114">X-Forwarded-For</span></span> | <span data-ttu-id="97c59-115">Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="97c59-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="97c59-116">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="97c59-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="97c59-117">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="97c59-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="97c59-118">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="97c59-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="97c59-119">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="97c59-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="97c59-120">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="97c59-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="97c59-121">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="97c59-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="97c59-122">Der Wert des ursprünglichen Schemas (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="97c59-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="97c59-123">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="97c59-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="97c59-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="97c59-124">X-Forwarded-Host</span></span> | <span data-ttu-id="97c59-125">Der ursprüngliche Wert des Felds „Hostheader“.</span><span class="sxs-lookup"><span data-stu-id="97c59-125">The original value of the Host header field.</span></span> <span data-ttu-id="97c59-126">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="97c59-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="97c59-127">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="97c59-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="97c59-128">Die Middleware für weitergeleitete Header (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="97c59-128">The Forwarded Headers Middleware (<xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersMiddleware>), reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="97c59-129">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="97c59-129">The middleware updates:</span></span>

* <span data-ttu-id="97c59-130">[HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="97c59-131">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="97c59-132">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="97c59-133">[HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="97c59-134">[HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="97c59-135">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="97c59-136">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="97c59-136">The default settings are:</span></span>

* <span data-ttu-id="97c59-137">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="97c59-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="97c59-138">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="97c59-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="97c59-139">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="97c59-140">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="97c59-141">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="97c59-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="97c59-142">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="97c59-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="97c59-143">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="97c59-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="97c59-144">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="97c59-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="97c59-145">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="97c59-146">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="97c59-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="97c59-147">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="97c59-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="97c59-148">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="97c59-149">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="97c59-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="97c59-150">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="97c59-151">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="97c59-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="97c59-152">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="97c59-153">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="97c59-154">Middleware für weitergeleitete Header: Auftrag</span><span class="sxs-lookup"><span data-stu-id="97c59-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="97c59-155">Die Middleware für weitergeleitete Header muss noch vor einer anderen Middleware ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="97c59-156">Mit dieser Reihenfolge wird sichergestellt, dass die auf Informationen von weitergeleiteten Headern basierende Middleware die zu verarbeitenden Headerwerte nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="97c59-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="97c59-157">Die Middleware für weitergeleitete Header kann nach der Diagnose und Fehlerbehandlung ausgeführt werden. Sie muss jedoch vor dem Aufrufen von `UseHsts`ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="97c59-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="97c59-158">Rufen Sie alternativ vor der Diagnose `UseForwardedHeaders` auf:</span><span class="sxs-lookup"><span data-stu-id="97c59-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="97c59-159">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="97c59-160">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="97c59-161">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97c59-161">Nginx configuration</span></span>

<span data-ttu-id="97c59-162">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="97c59-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="97c59-163">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="97c59-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="97c59-164">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97c59-164">Apache configuration</span></span>

<span data-ttu-id="97c59-165">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="97c59-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="97c59-166">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="97c59-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="97c59-167">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="97c59-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="97c59-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="97c59-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="97c59-169">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="97c59-169">The following example changes the default values:</span></span>

* <span data-ttu-id="97c59-170">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="97c59-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="97c59-171">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="97c59-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="97c59-172">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="97c59-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="97c59-173">Option</span><span class="sxs-lookup"><span data-stu-id="97c59-173">Option</span></span> | <span data-ttu-id="97c59-174">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="97c59-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="97c59-175">Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="97c59-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="97c59-176">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="97c59-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="97c59-177">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="97c59-178">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="97c59-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="97c59-179">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="97c59-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="97c59-180">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="97c59-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="97c59-181">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="97c59-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="97c59-182">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="97c59-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="97c59-183">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="97c59-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="97c59-184">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="97c59-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="97c59-185">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="97c59-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="97c59-186">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="97c59-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="97c59-187">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="97c59-188">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-189">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="97c59-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="97c59-190">Gibt an, welche Weiterleitungen verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="97c59-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="97c59-191">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="97c59-192">Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="97c59-193">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="97c59-194">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="97c59-195">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-196">Der Standardwert ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="97c59-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="97c59-197">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="97c59-198">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-199">Der Standardwert ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="97c59-200">Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="97c59-201">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="97c59-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="97c59-202">Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="97c59-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="97c59-203">Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="97c59-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="97c59-204">Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="97c59-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="97c59-205">Der Standardwert ist `1`.</span><span class="sxs-lookup"><span data-stu-id="97c59-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="97c59-206">Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="97c59-207">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="97c59-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="97c59-208">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="97c59-209">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-210">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="97c59-211">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="97c59-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="97c59-212">Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="97c59-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="97c59-213">Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="97c59-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="97c59-214">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="97c59-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="97c59-215">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="97c59-216">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-217">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="97c59-218">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="97c59-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="97c59-219">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="97c59-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="97c59-220">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="97c59-221">Der Standardwert ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="97c59-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="97c59-222">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="97c59-223">Der Standardwert ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="97c59-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="97c59-224">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="97c59-225">Der Standardwert ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="97c59-226">Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="97c59-227">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="97c59-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="97c59-228">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="97c59-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="97c59-229">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="97c59-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="97c59-230">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="97c59-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="97c59-231">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="97c59-232">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="97c59-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="97c59-233">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="97c59-234">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="97c59-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="97c59-235">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="97c59-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="97c59-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="97c59-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="97c59-237">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="97c59-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="97c59-238">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="97c59-239">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="97c59-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="97c59-240">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="97c59-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="97c59-241">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="97c59-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="97c59-242">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="97c59-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="97c59-243">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="97c59-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="97c59-244">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="97c59-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="97c59-245">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="97c59-246">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-247">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="97c59-248">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="97c59-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="97c59-249">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="97c59-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="97c59-250">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="97c59-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="97c59-251">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="97c59-251">Converted prefix length: 104</span></span>

<span data-ttu-id="97c59-252">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="97c59-253">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="97c59-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="97c59-254">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="97c59-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="97c59-255">Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt.</span><span class="sxs-lookup"><span data-stu-id="97c59-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="97c59-256">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="97c59-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="97c59-257">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="97c59-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="97c59-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="97c59-259">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="97c59-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="97c59-260">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="97c59-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="97c59-261">Zertifikatweiterleitung</span><span class="sxs-lookup"><span data-stu-id="97c59-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="97c59-262">Azure</span><span class="sxs-lookup"><span data-stu-id="97c59-262">Azure</span></span>

<span data-ttu-id="97c59-263">Informationen zum Konfigurieren von Azure App Service für die Zertifikatsweiterleitung finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="97c59-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="97c59-264">Die folgende Anleitung bezieht sich auf die Konfiguration der ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="97c59-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="97c59-265">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="97c59-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="97c59-266">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung, um den von Azure verwendeten Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="97c59-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="97c59-267">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="97c59-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="97c59-268">Andere Webproxys</span><span class="sxs-lookup"><span data-stu-id="97c59-268">Other web proxies</span></span>

<span data-ttu-id="97c59-269">Wenn ein Proxy verwendet wird, der nicht IIS oder Routing von Anwendungsanforderungen von Azure App Service entspricht, konfigurieren Sie den Proxy so, dass das empfangene Zertifikat in einem HTTP-Header weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="97c59-270">Fügen Sie `Startup.Configure` direkt vor dem Aufruf von `app.UseAuthentication();` den folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="97c59-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="97c59-271">Konfigurieren Sie die Middleware für die Zertifikatweiterleitung konfigurieren, um den Headernamen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="97c59-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="97c59-272">Fügen Sie in `Startup.ConfigureServices` den folgenden Code hinzu, um den Header zu konfigurieren, anhand dessen die Middleware ein Zertifikat erstellt:</span><span class="sxs-lookup"><span data-stu-id="97c59-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="97c59-273">Wenn der Proxy das Zertifikat mit nicht mit base64 codiert (wie bei Nginx), legen Sie die Option `HeaderConverter` fest.</span><span class="sxs-lookup"><span data-stu-id="97c59-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="97c59-274">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="97c59-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="97c59-275">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="97c59-275">Troubleshoot</span></span>

<span data-ttu-id="97c59-276">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="97c59-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="97c59-277">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="97c59-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="97c59-278">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="97c59-279">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="97c59-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="97c59-280">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="97c59-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="97c59-281">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="97c59-282">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="97c59-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="97c59-283">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="97c59-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="97c59-284">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="97c59-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="97c59-285">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="97c59-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="97c59-286">Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="97c59-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="97c59-287">Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="97c59-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="97c59-288">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="97c59-289">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="97c59-290">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="97c59-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="97c59-291">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="97c59-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="97c59-292">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="97c59-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="97c59-293">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="97c59-294">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="97c59-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="97c59-295">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="97c59-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97c59-296">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="97c59-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="97c59-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="97c59-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="97c59-298">In der für ASP.NET Core empfohlenen Konfiguration wird die App mit dem IIS/ASP.NET Core-Modul, Nginx oder Apache gehostet.</span><span class="sxs-lookup"><span data-stu-id="97c59-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="97c59-299">Proxyserver, Lastenausgleichsmodule und anderen Netzwerkgeräte verdecken häufig Informationen über die Anforderung, bevor diese die App erreicht:</span><span class="sxs-lookup"><span data-stu-id="97c59-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="97c59-300">Wenn HTTPS-Anforderungen über HTTP-Proxy ausgeführt werden, geht das ursprüngliche Schema (HTTPS) verloren und muss in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="97c59-301">Da eine App eine Anforderung über den Proxy empfängt und nicht über ihre echte Quelle im Internet oder Unternehmensnetzwerk, muss die ursprüngliche Client-IP-Adresse ebenfalls in einem Header weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="97c59-302">Diese Informationen können im Anforderungsprozess, z.B. in Umleitungen, bei der Authentifizierung, der Linkgenerierung, der Richtlinienauswertung und der Client-Geolocation wichtig sein.</span><span class="sxs-lookup"><span data-stu-id="97c59-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="97c59-303">Weitergeleitete Header</span><span class="sxs-lookup"><span data-stu-id="97c59-303">Forwarded headers</span></span>

<span data-ttu-id="97c59-304">Gemäß der Konvention leiten Proxys Informationen in HTTP-Headern weiter.</span><span class="sxs-lookup"><span data-stu-id="97c59-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="97c59-305">Header</span><span class="sxs-lookup"><span data-stu-id="97c59-305">Header</span></span> | <span data-ttu-id="97c59-306">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="97c59-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="97c59-307">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="97c59-307">X-Forwarded-For</span></span> | <span data-ttu-id="97c59-308">Enthält Informationen zum Client, der die Anforderung und die nachfolgenden Proxys in einer Kette von Proxys initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="97c59-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="97c59-309">Dieser Parameter kann IP-Adressen (und optional Portnummern) enthalten.</span><span class="sxs-lookup"><span data-stu-id="97c59-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="97c59-310">Der erste Parameter in einer Kette von Proxyservern gibt den Client an, auf dem die Anforderung zuerst gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="97c59-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="97c59-311">Darauf folgen weitere Proxybezeichner.</span><span class="sxs-lookup"><span data-stu-id="97c59-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="97c59-312">Der letzte Proxy in der Kette ist nicht in der Liste der Parameter.</span><span class="sxs-lookup"><span data-stu-id="97c59-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="97c59-313">Die IP-Adresse des letzten Proxys und optional eine Portnummer stehen als Remote IP-Adresse auf der Transportschicht zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="97c59-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="97c59-314">X-Forwarded-Proto</span><span class="sxs-lookup"><span data-stu-id="97c59-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="97c59-315">Der Wert des ursprünglichen Schemas (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="97c59-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="97c59-316">Der Wert kann auch eine Liste von Schemas sein, wenn die Anforderung mehrere Proxys durchlaufen hat.</span><span class="sxs-lookup"><span data-stu-id="97c59-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="97c59-317">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="97c59-317">X-Forwarded-Host</span></span> | <span data-ttu-id="97c59-318">Der ursprüngliche Wert des Felds „Hostheader“.</span><span class="sxs-lookup"><span data-stu-id="97c59-318">The original value of the Host header field.</span></span> <span data-ttu-id="97c59-319">In der Regel ändern Proxys den Hostheader nicht.</span><span class="sxs-lookup"><span data-stu-id="97c59-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="97c59-320">Informationen zu einem Sicherheitsrisiko, das Rechteerweiterungen ermöglicht und sich auf Systeme auswirkt, in denen der Proxy Hostheader nicht validiert oder auf als unbedenklich bekannte Werte beschränkt, finden Sie in der [Microsoft-Sicherheitsempfehlung CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295).</span><span class="sxs-lookup"><span data-stu-id="97c59-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="97c59-321">Die Middleware für weitergeleitete Header aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) liest die Header und füllt die zugehörigen Felder in <xref:Microsoft.AspNetCore.Http.HttpContext> aus.</span><span class="sxs-lookup"><span data-stu-id="97c59-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="97c59-322">Die Middleware aktualisiert:</span><span class="sxs-lookup"><span data-stu-id="97c59-322">The middleware updates:</span></span>

* <span data-ttu-id="97c59-323">[HttpContext.Connection.RemoteIpAddress:](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) Wird mit dem Headerwert `X-Forwarded-For` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="97c59-324">Zusätzliche Einstellungen beeinflussen, wie die Middleware `RemoteIpAddress` festlegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="97c59-325">Einzelheiten finden Sie unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="97c59-326">[HttpContext.Request.Scheme:](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) Wird mit dem Headerwert `X-Forwarded-Proto` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="97c59-327">[HttpContext.Request.Host:](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) Wird mit dem Headerwert `X-Forwarded-Host` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="97c59-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="97c59-328">Die [Standardeinstellungen](#forwarded-headers-middleware-options) der Middleware für weitergeleitete Header können konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="97c59-329">Folgende Standardeinstellungen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="97c59-329">The default settings are:</span></span>

* <span data-ttu-id="97c59-330">Zwischen App und Quelle der Anforderungen ist nur ein *Proxy* vorhanden.</span><span class="sxs-lookup"><span data-stu-id="97c59-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="97c59-331">Loopbackadressen sind für bekannte Proxys und bekannte Netzwerke konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="97c59-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="97c59-332">Die weitergeleiteten Header heißen `X-Forwarded-For` und `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="97c59-333">Beachten Sie, dass bei manchen Netzwerkgeräten eine zusätzliche Konfiguration erforderlich ist, damit die Header `X-Forwarded-For` und `X-Forwarded-Proto` hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="97c59-334">Lesen Sie in der Anleitung des Geräteherstellers nach, wenn über einen Proxy übermittelte Anfragen ohne diese Header in der Anwendung eingehen.</span><span class="sxs-lookup"><span data-stu-id="97c59-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="97c59-335">Verwendet das Gerät andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="97c59-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="97c59-336">Weitere Informationen finden Sie in den Abschnitten [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options) und [Konfiguration für einen Proxy, der andere Headernamen nutzt](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="97c59-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="97c59-337">IIS-/IIS Express und ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="97c59-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="97c59-338">Middleware für weitergeleitete Header wird standardmäßig durch [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) aktiviert, wenn die App hinter IIS und dem ASP.NET Core-Modul von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="97c59-339">Middleware für weitergeleitete Header wird aktiviert und zuerst in der Middlewarepipeline mit einer beschränkten Konfiguration ausgeführt, die für das ASP.NET Core-Modul spezifisch ist. Dies ist auf Bedenken in Bezug auf die Vertrauenswürdigkeit von weitergeleiteten Headern zurückzuführen (z.B. [IP-Spoofing](https://www.iplocation.net/ip-spoofing)).</span><span class="sxs-lookup"><span data-stu-id="97c59-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="97c59-340">Die Middleware wird so konfiguriert, dass sie die Header `X-Forwarded-For` und `X-Forwarded-Proto` weiterleitet, und ist auf einen einzelnen localhost-Proxy beschränkt.</span><span class="sxs-lookup"><span data-stu-id="97c59-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="97c59-341">Wenn zusätzliche Konfigurationsschritte erforderlich sind, finden Sie weitere Informationen unter [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="97c59-342">Andere Proxyserver- und Lastenausgleichsszenarios</span><span class="sxs-lookup"><span data-stu-id="97c59-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="97c59-343">Außer bei der Verwendung der [Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird die Middleware für weitergeleitete Header nicht standardmäßig aktiviert, wenn sie von einem [Out-of-Process](xref:host-and-deploy/iis/index#out-of-process-hosting-model)-Host gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="97c59-344">Middleware für weitergeleitete Header muss aktiviert sein, damit eine App weitergeleitete Header mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="97c59-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="97c59-345">Sind nach der Aktivierung der Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> für die Middleware angegeben, sind die standardmäßigen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="97c59-346">Konfigurieren Sie die Middleware mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` in `Startup.ConfigureServices` weitergeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="97c59-347">Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure` auf, bevor Sie andere Middleware aufrufen:</span><span class="sxs-lookup"><span data-stu-id="97c59-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="97c59-348">Sind keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> in `Startup.ConfigureServices` oder direkt für die Erweiterungsmethode mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> angegeben, sind die weiterzuleitenden Standardheader gleich [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="97c59-349">Die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>-Eigenschaft muss mit den weiterzuleitenden Headern konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="97c59-350">Nginx-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97c59-350">Nginx configuration</span></span>

<span data-ttu-id="97c59-351">Informationen zur Weiterleitung der Header `X-Forwarded-For` und `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-nginx#configure-nginx>.</span><span class="sxs-lookup"><span data-stu-id="97c59-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="97c59-352">Weitere Informationen finden Sie unter [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="97c59-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="97c59-353">Apache-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="97c59-353">Apache configuration</span></span>

<span data-ttu-id="97c59-354">`X-Forwarded-For` wird automatisch hinzugefügt (siehe [Apache Module mod_proxy: Reverse Proxy Request Headers (Apache-Modul mod_proxy: Anforderungsheader für Reverseproxys)](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="97c59-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="97c59-355">Informationen zur Weiterleitung des Headers `X-Forwarded-Proto` finden Sie unter <xref:host-and-deploy/linux-apache#configure-apache>.</span><span class="sxs-lookup"><span data-stu-id="97c59-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="97c59-356">Middleware für weitergeleitete Header: Optionen</span><span class="sxs-lookup"><span data-stu-id="97c59-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="97c59-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> steuern das Verhalten der Middleware für weitergeleitete Header.</span><span class="sxs-lookup"><span data-stu-id="97c59-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="97c59-358">Im folgenden Beispiel werden die Standardwerte geändert:</span><span class="sxs-lookup"><span data-stu-id="97c59-358">The following example changes the default values:</span></span>

* <span data-ttu-id="97c59-359">Beschränken Sie die Zahl der Einträge in den weitergeleiteten Headern auf `2`.</span><span class="sxs-lookup"><span data-stu-id="97c59-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="97c59-360">Fügen Sie eine bekannte Proxyadresse von `127.0.10.1` hinzu.</span><span class="sxs-lookup"><span data-stu-id="97c59-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="97c59-361">Ändern Sie den Namen des weitergeleiteten Headers vom Standardwert `X-Forwarded-For` in den Wert `X-Forwarded-For-My-Custom-Header-Name`.</span><span class="sxs-lookup"><span data-stu-id="97c59-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="97c59-362">Option</span><span class="sxs-lookup"><span data-stu-id="97c59-362">Option</span></span> | <span data-ttu-id="97c59-363">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="97c59-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="97c59-364">Begrenzt Hosts durch den `X-Forwarded-Host`-Header auf die angegebenen Werte.</span><span class="sxs-lookup"><span data-stu-id="97c59-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="97c59-365">Werte werden mit Ordnungszahl/Groß-/Kleinschreibung ignorieren verglichen.</span><span class="sxs-lookup"><span data-stu-id="97c59-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="97c59-366">Portnummern müssen ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="97c59-367">Wenn die Liste leer ist, sind alle Hosts zulässig.</span><span class="sxs-lookup"><span data-stu-id="97c59-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="97c59-368">Ein Platzhalter `*` auf der obersten Ebene lässt alle nicht leeren Hosts zu.</span><span class="sxs-lookup"><span data-stu-id="97c59-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="97c59-369">Unterdomänen-Platzhalter sind zulässig, stimmen aber nicht mit der Stammdomäne überein.</span><span class="sxs-lookup"><span data-stu-id="97c59-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="97c59-370">Beispielsweise entspricht `*.contoso.com` der Unterdomäne `foo.contoso.com`, aber nicht der Stammdomäne `contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="97c59-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="97c59-371">Unicode-Hostnamen sind zulässig, werden jedoch für den Abgleich in [Punycode](https://tools.ietf.org/html/rfc3492) konvertiert.</span><span class="sxs-lookup"><span data-stu-id="97c59-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="97c59-372">[IPv6-Adressen](https://tools.ietf.org/html/rfc4291) müssen Begrenzungsklammern einschließen und im [konventionellen Format](https://tools.ietf.org/html/rfc4291#section-2.2) vorliegen (z.B. `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span><span class="sxs-lookup"><span data-stu-id="97c59-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="97c59-373">IPv6-Adressen sind keine Sonderfälle, um auf logische Gleichheit zwischen verschiedenen Formaten zu prüfen, und es wird keine Kanonisierung durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="97c59-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="97c59-374">Dadurch, dass die zulässigen Hosts nicht begrenzt werden, kann einem Angreifer die Möglichkeit eröffnet werden, von dem Dienst generierte Links zu fälschen.</span><span class="sxs-lookup"><span data-stu-id="97c59-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="97c59-375">Der Standardwert ist eine leere `IList<string>`.</span><span class="sxs-lookup"><span data-stu-id="97c59-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="97c59-376">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="97c59-377">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-For` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-378">Der Standardwert ist `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="97c59-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="97c59-379">Gibt an, welche Weiterleitungen verarbeitet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="97c59-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="97c59-380">Weitere Informationen zur Liste der anzuwendenden Felder finden Sie unter [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="97c59-381">Typische Werte, die dieser Eigenschaft zugewiesen wurden, sind `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="97c59-382">Der Standardwert ist [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="97c59-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="97c59-383">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="97c59-384">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Host` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-385">Der Standardwert ist `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="97c59-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="97c59-386">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="97c59-387">Diese Option wird verwendet, wenn beim Proxy/Weiterleitenden nicht der Header `X-Forwarded-Proto` sondern ein anderer Header für die Weiterleitung der Informationen genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="97c59-388">Der Standardwert ist `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="97c59-389">Schränkt die Anzahl der Einträge in den Headern ein, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="97c59-390">Legen Sie den Wert `null` fest, um die Einschränkung zu deaktivieren. Diese Einstellung sollte jedoch nur vorgenommen werden, wenn `KnownProxies` oder `KnownNetworks` konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="97c59-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="97c59-391">Das Festlegen eines Werts ungleich `null` ist eine Vorsichtsmaßnahme (aber keine Garantie) zum Schutz vor falsch konfigurierten Proxys und schädlichen Anforderungen aus Seitenkanälen im Netzwerk.</span><span class="sxs-lookup"><span data-stu-id="97c59-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="97c59-392">Die Middleware der Weiterleitungsheader verarbeitet Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="97c59-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="97c59-393">Wenn der Standardwert von (`1`) verwendet wird, wird nur der äußere rechte Wert aus den Headern verarbeitet, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="97c59-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="97c59-394">Der Standardwert ist `1`.</span><span class="sxs-lookup"><span data-stu-id="97c59-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="97c59-395">Adressbereiche bekannter Netzwerke; von dort weitergeleitete Header müssen akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="97c59-396">Geben Sie IP-Adressbereiche mithilfe der CIDR-Notation (Classless Interdomain Routing) an.</span><span class="sxs-lookup"><span data-stu-id="97c59-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="97c59-397">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="97c59-398">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-399">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="97c59-400">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="97c59-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="97c59-401">Der Standardwert ist `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> mit einem Eintrag für `IPAddress.Loopback`.</span><span class="sxs-lookup"><span data-stu-id="97c59-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="97c59-402">Adressen bekannter Proxys, von denen weitergeleitete Header akzeptiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="97c59-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="97c59-403">Verwenden Sie `KnownProxies`, um genaue IP-Adressübereinstimmungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="97c59-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="97c59-404">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="97c59-405">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-406">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="97c59-407">Weitere Informationen finden Sie im Abschnitt [Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address).</span><span class="sxs-lookup"><span data-stu-id="97c59-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="97c59-408">Der Standardwert ist `IList`\<<xref:System.Net.IPAddress>> mit einem Eintrag für `IPAddress.IPv6Loopback`.</span><span class="sxs-lookup"><span data-stu-id="97c59-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="97c59-409">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="97c59-410">Der Standardwert ist `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="97c59-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="97c59-411">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="97c59-412">Der Standardwert ist `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="97c59-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="97c59-413">Verwenden Sie den Header, der von dieser Eigenschaft angegeben wurde, anstelle des von [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName) angegebenen.</span><span class="sxs-lookup"><span data-stu-id="97c59-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="97c59-414">Der Standardwert ist `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="97c59-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="97c59-415">Die Anzahl von Headerwerten muss in den [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) übereinstimmen, die verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="97c59-416">Die Standardeinstellung in ASP.NET Core 1.x ist `true`.</span><span class="sxs-lookup"><span data-stu-id="97c59-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="97c59-417">Die Standardeinstellung in ASP.NET Core 2.0 oder höher ist `false`.</span><span class="sxs-lookup"><span data-stu-id="97c59-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="97c59-418">Szenarios und Anwendungsfälle</span><span class="sxs-lookup"><span data-stu-id="97c59-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="97c59-419">Wenn keine weitergeleiteten Header hinzugefügt werden können und alle Anforderungen sicher sind</span><span class="sxs-lookup"><span data-stu-id="97c59-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="97c59-420">In einigen Fällen ist es möglicherweise ausgeschlossen, weitergeleitete Header den Anforderungen hinzuzufügen, die über einen Proxy an die App übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="97c59-421">Wenn der Proxy erzwingt, dass alle öffentlichen externen Anforderungen HTTPS-Anforderungen sind, kann das Schema manuell in `Startup.Configure` festgelegt werden, bevor ein beliebiger Middlewaretyp verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="97c59-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="97c59-422">Dieser Code kann mit einer Umgebungsvariablen oder einer anderen Konfigurationseinstellung in einer Entwicklungs- oder Stagingumgebung deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="97c59-423">Umgang mit Pfadbasis und Proxys, die den Anforderungspfad ändern</span><span class="sxs-lookup"><span data-stu-id="97c59-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="97c59-424">Einige Proxys übergeben den Pfad intakt, aber mit einem App-basierten Pfad, der entfernt werden sollte, damit das Routing ordnungsgemäß funktioniert.</span><span class="sxs-lookup"><span data-stu-id="97c59-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="97c59-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*)-Middleware teilt den Pfad in [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) und den App-Basispfad in [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span><span class="sxs-lookup"><span data-stu-id="97c59-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="97c59-426">Wenn `/foo` der App-Basispfad für einen als `/foo/api/1` übergebenen Proxypfad ist, setzt die Middleware mit dem folgenden Befehl `Request.PathBase` auf `/foo` und `Request.Path` auf `/api/1`:</span><span class="sxs-lookup"><span data-stu-id="97c59-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="97c59-427">Der ursprüngliche Pfad und die Pfadbasis werden erneut angewendet, wenn die Middleware in umgekehrter Reihenfolge erneut aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="97c59-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="97c59-428">Weitere Informationen zur Verarbeitungsreihenfolge in der Middleware finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="97c59-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="97c59-429">Wenn der Proxy den Pfad abschneidet (z.B. Weiterleitung von `/foo/api/1` zu `/api/1`), korrigieren Sie Umleitungen und Links, indem Sie die Eigenschaft [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) der Anforderungen setzen:</span><span class="sxs-lookup"><span data-stu-id="97c59-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="97c59-430">Wenn der Proxy Pfaddaten hinzufügt, verwerfen Sie einen Teil des Pfads, um Umleitungen und Links zu korrigieren, indem Sie <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> verwenden und der <xref:Microsoft.AspNetCore.Http.HttpRequest.Path>-Eigenschaft zuweisen:</span><span class="sxs-lookup"><span data-stu-id="97c59-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="97c59-431">Konfiguration für einen Proxy, der andere Headernamen verwendet</span><span class="sxs-lookup"><span data-stu-id="97c59-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="97c59-432">Verwendet der Proxy zum Weiterleiten der Proxyadresse/des Ports und zum Erzeugen der Schemainformationen andere Headernamen als `X-Forwarded-For` und `X-Forwarded-Proto`, passen Sie die Optionen <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> und <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> entsprechend an:</span><span class="sxs-lookup"><span data-stu-id="97c59-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="97c59-433">Konfiguration für eine IPv4-Adresse, die als IPv6-Adresse dargestellt wird</span><span class="sxs-lookup"><span data-stu-id="97c59-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="97c59-434">Wenn der Server Dualmodussockets verwendet, werden IPv4-Adressen in einem IPv6-Format bereitgestellt (z.B. wird `10.0.0.1` in IPv4 in IPv6 als `::ffff:10.0.0.1` oder `::ffff:a00:1` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="97c59-435">Siehe [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="97c59-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="97c59-436">Bestimmen Sie mithilfe der [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*), ob dieses Format erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="97c59-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="97c59-437">Im folgenden Beispiel wird eine Netzwerkadresse, die weitergeleitete Header bereitstellt, der `KnownNetworks`-Liste im IPv6-Format hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="97c59-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="97c59-438">IPv4-Adresse: `10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="97c59-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="97c59-439">Konvertierte IPv6-Adresse: `::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="97c59-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="97c59-440">Konvertierte Präfixlänge: 104</span><span class="sxs-lookup"><span data-stu-id="97c59-440">Converted prefix length: 104</span></span>

<span data-ttu-id="97c59-441">Sie können die Adresse auch im Hexadezimalformat angeben (`10.11.12.1` in IPv6 als `::ffff:0a0b:0c01` dargestellt).</span><span class="sxs-lookup"><span data-stu-id="97c59-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="97c59-442">Fügen Sie bei der Konvertierung einer IPv4-Adresse in IPv6 der CIDR-Präfixlänge 96 (`8` im Beispiel) für das zusätzliche `::ffff:`-IPv6-Präfix hinzu (8 + 96 = 104).</span><span class="sxs-lookup"><span data-stu-id="97c59-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="97c59-443">Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten</span><span class="sxs-lookup"><span data-stu-id="97c59-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="97c59-444">Apps, die <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> und <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> aufrufen, versetzen eine Site in eine Endlosschleife, falls die Bereitstellung in einem Azure Linux App Service, auf einem virtuellen Azure Linux-Computer, oder hinter einem anderen Reverseproxy als IIS erfolgt.</span><span class="sxs-lookup"><span data-stu-id="97c59-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="97c59-445">TLS wird vom Reverseproxy beendet, und Kestrel wird nicht auf das richtige Anforderungsschema aufmerksam gemacht.</span><span class="sxs-lookup"><span data-stu-id="97c59-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="97c59-446">OAuth und OIDC schlagen in dieser Konfiguration ebenfalls fehl, weil sie falsche Umleitungen generieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="97c59-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> fügt die Middleware für weitergeleitete Header hinzu und konfiguriert sie, wenn es hinter IIS ausgeführt wird, aber es gibt keine entsprechende automatische Konfiguration für Linux (Apache- oder Nginx-Integration).</span><span class="sxs-lookup"><span data-stu-id="97c59-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="97c59-448">Um das Schema von dem Proxy in Nicht-IIS-Szenarios weiterzuleiten, fügen Sie die Middleware für weitergeleitete Header hinzu, und konfigurieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="97c59-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="97c59-449">In `Startup.ConfigureServices` verwenden Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="97c59-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="97c59-450">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="97c59-450">Troubleshoot</span></span>

<span data-ttu-id="97c59-451">Wenn Header nicht wie erwartet zugewiesen werden, aktivieren Sie die [Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="97c59-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="97c59-452">Wenn die Protokolle nicht genügend Informationen zur Problembehandlung bereitstellen, listen Sie die vom Server empfangenen Anforderungsheader auf.</span><span class="sxs-lookup"><span data-stu-id="97c59-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="97c59-453">Verwenden Sie Inlinemiddleware, um Anforderungsheader in eine App-Antwort zu schreiben oder die Header zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="97c59-454">Um die Header in die Antwort der App zu schreiben, platzieren Sie die folgende Terminalinlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="97c59-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="97c59-455">Statt in den Antworttext können Sie in Protokolle schreiben.</span><span class="sxs-lookup"><span data-stu-id="97c59-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="97c59-456">Das Schreiben in Protokolle ermöglicht es der Website, während des Debuggens normal zu funktionieren.</span><span class="sxs-lookup"><span data-stu-id="97c59-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="97c59-457">So schreiben Sie in Protokolle und nicht in den Antworttext:</span><span class="sxs-lookup"><span data-stu-id="97c59-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="97c59-458">Fügen Sie `ILogger<Startup>` in die `Startup`-Klasse ein, wie unter [Erstellen von Protokollen beim Start](xref:fundamentals/logging/index#create-logs-in-startup) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="97c59-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="97c59-459">Platzieren Sie die folgende Inlinemiddleware unmittelbar hinter den Aufruf von <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="97c59-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="97c59-460">Bei der Verarbeitung werden `X-Forwarded-{For|Proto|Host}`-Werte in `X-Original-{For|Proto|Host}` verschoben.</span><span class="sxs-lookup"><span data-stu-id="97c59-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="97c59-461">Wenn in einem bestimmten Header mehrere Werte vorhanden sind, verarbeitet die Middleware der Weiterleitungsheader die Header in umgekehrter Reihenfolge von rechts nach links.</span><span class="sxs-lookup"><span data-stu-id="97c59-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="97c59-462">Der Standardwert von `ForwardLimit` ist `1` (eins), sodass nur der äußere rechte Wert aus den Headern verarbeitet wird, es sei denn, der Wert von `ForwardLimit` wird erhöht.</span><span class="sxs-lookup"><span data-stu-id="97c59-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="97c59-463">Die ursprüngliche IP-Remoteadresse der Anforderung muss mit einem Eintrag in den `KnownProxies`- oder `KnownNetworks`-Listen übereinstimmen, bevor weitergeleitete Header verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="97c59-464">Auf diese Weise wird das Headerspoofing begrenzt, da keine Weiterleitungen von nicht vertrauenswürdigen Proxys akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="97c59-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="97c59-465">Wenn ein unbekannter Proxy erkannt wird, gibt die Protokollierung die Adresse des Proxys an:</span><span class="sxs-lookup"><span data-stu-id="97c59-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="97c59-466">Im vorherigen Beispiel ist 10.0.0.100 ein Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="97c59-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="97c59-467">Wenn der Server ein vertrauenswürdiger Proxy ist, fügen Sie die IP-Adresse des Servers `KnownProxies` (oder ein vertrauenswürdiges Netzwerk `KnownNetworks`) in `Startup.ConfigureServices` hinzu.</span><span class="sxs-lookup"><span data-stu-id="97c59-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="97c59-468">Weitere Informationen finden Sie im Abschnitt [Middleware für weitergeleitete Header: Optionen](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="97c59-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="97c59-469">Erlauben Sie nur vertrauenswürdigen Proxys und Netzwerken die Weiterleitung von Headern.</span><span class="sxs-lookup"><span data-stu-id="97c59-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="97c59-470">Andernfalls sind Angriffe des Typs [IP-Spoofing](https://www.iplocation.net/ip-spoofing) möglich.</span><span class="sxs-lookup"><span data-stu-id="97c59-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="97c59-471">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="97c59-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="97c59-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability (Microsoft-Sicherheitsempfehlung CVE-2018-0787: Sicherheitsrisiko durch Rechteerweiterungen in ASP.NET Core)</span><span class="sxs-lookup"><span data-stu-id="97c59-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
