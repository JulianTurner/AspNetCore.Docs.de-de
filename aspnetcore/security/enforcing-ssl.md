---
title: Erzwingen von HTTPS in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie HTTPS/TLS in einer ASP.net Core-Web-App anfordern.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: e473da9a7cbd91a601ad4af0c7c02c7f576f348c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051121"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="985c7-103">Erzwingen von HTTPS in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="985c7-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="985c7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="985c7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="985c7-105">Dieses Dokument zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="985c7-105">This document shows how to:</span></span>

* <span data-ttu-id="985c7-106">Erfordert HTTPS für alle Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="985c7-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="985c7-107">Leitet alle HTTP-Anforderungen an HTTPS um.</span><span class="sxs-lookup"><span data-stu-id="985c7-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="985c7-108">Eine API kann verhindern, dass ein Client sensible Daten bei der ersten Anforderung sendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="985c7-109">API-Projekte</span><span class="sxs-lookup"><span data-stu-id="985c7-109">API projects</span></span>
>
> <span data-ttu-id="985c7-110">Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten.</span><span class="sxs-lookup"><span data-stu-id="985c7-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="985c7-111">`RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="985c7-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="985c7-112">API-Clients können Umleitungen von http zu HTTPS nicht verstehen oder darauf verweisen.</span><span class="sxs-lookup"><span data-stu-id="985c7-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="985c7-113">Diese Clients senden möglicherweise Informationen über http.</span><span class="sxs-lookup"><span data-stu-id="985c7-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="985c7-114">Web-APIs sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="985c7-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="985c7-115">Nicht an http lauschen.</span><span class="sxs-lookup"><span data-stu-id="985c7-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="985c7-116">Schließen Sie die Verbindung mit dem Statuscode 400 (ungültige Anforderung), und verarbeiten Sie die Anforderung nicht.</span><span class="sxs-lookup"><span data-stu-id="985c7-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="985c7-117">Hsts-und API-Projekte</span><span class="sxs-lookup"><span data-stu-id="985c7-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="985c7-118">Die Standard-API-Projekte enthalten keine [hsts](#hsts) , da hsts im Allgemeinen eine reine Browser Anweisung ist.</span><span class="sxs-lookup"><span data-stu-id="985c7-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="985c7-119">Andere Aufrufer, z. b. Telefon-oder Desktop-Apps, gehorchen **nicht** der Anweisung.</span><span class="sxs-lookup"><span data-stu-id="985c7-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="985c7-120">Auch innerhalb von Browsern hat ein einzelner authentifizierter API-API-Rückruf Risiken für unsichere Netzwerke.</span><span class="sxs-lookup"><span data-stu-id="985c7-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="985c7-121">Der sichere Ansatz besteht darin, API-Projekte so zu konfigurieren, dass Sie nur über HTTPS lauschen und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="985c7-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="985c7-122">API-Projekte</span><span class="sxs-lookup"><span data-stu-id="985c7-122">API projects</span></span>
>
> <span data-ttu-id="985c7-123">Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten.</span><span class="sxs-lookup"><span data-stu-id="985c7-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="985c7-124">`RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten.</span><span class="sxs-lookup"><span data-stu-id="985c7-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="985c7-125">API-Clients können Umleitungen von http zu HTTPS nicht verstehen oder darauf verweisen.</span><span class="sxs-lookup"><span data-stu-id="985c7-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="985c7-126">Diese Clients senden möglicherweise Informationen über http.</span><span class="sxs-lookup"><span data-stu-id="985c7-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="985c7-127">Web-APIs sollten folgende Aktionen ausführen:</span><span class="sxs-lookup"><span data-stu-id="985c7-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="985c7-128">Nicht an http lauschen.</span><span class="sxs-lookup"><span data-stu-id="985c7-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="985c7-129">Schließen Sie die Verbindung mit dem Statuscode 400 (ungültige Anforderung), und verarbeiten Sie die Anforderung nicht.</span><span class="sxs-lookup"><span data-stu-id="985c7-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="985c7-130">Erzwingen von HTTPS</span><span class="sxs-lookup"><span data-stu-id="985c7-130">Require HTTPS</span></span>

<span data-ttu-id="985c7-131">Es wird empfohlen, dass die Web-Apps für die Produktion ASP.net Core:</span><span class="sxs-lookup"><span data-stu-id="985c7-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="985c7-132">HTTPS-Umleitungs Middleware ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) zum Umleiten von HTTP-Anforderungen an HTTPS.</span><span class="sxs-lookup"><span data-stu-id="985c7-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="985c7-133">Hsts-Middleware ([usehsts](#http-strict-transport-security-protocol-hsts)) zum Senden von http-hsts-Headern (Strict Transport Security Protocol) an Clients.</span><span class="sxs-lookup"><span data-stu-id="985c7-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="985c7-134">Apps, die in einer Reverseproxykonfiguration bereitgestellt werden, ermöglichen dem Proxy das Verarbeiten der Verbindungssicherheit (HTTPS)</span><span class="sxs-lookup"><span data-stu-id="985c7-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="985c7-135">Wenn der Proxy auch die HTTPS-Umleitung verarbeitet, muss keine HTTPS-Umleitungs Middleware verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="985c7-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="985c7-136">Wenn der Proxy Server auch das Schreiben von hsts-Headern verarbeitet (z. b. System [eigene hsts-Unterstützung in IIS 10,0 (1709) oder](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)höher), ist hsts-Middleware für die APP nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="985c7-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="985c7-137">Weitere Informationen finden Sie unter [Ablehnen von HTTPS/hsts bei der Projekt Erstellung](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="985c7-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="985c7-138">Usehttpsredirection</span><span class="sxs-lookup"><span data-stu-id="985c7-138">UseHttpsRedirection</span></span>

<span data-ttu-id="985c7-139">Der folgende Code ruft `UseHttpsRedirection` in der- `Startup` Klasse auf:</span><span class="sxs-lookup"><span data-stu-id="985c7-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="985c7-140">Der vorangehende markierte Code:</span><span class="sxs-lookup"><span data-stu-id="985c7-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="985c7-141">Verwendet den Standardwert [httpsredirectionoptions. redirectstatus Code](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span><span class="sxs-lookup"><span data-stu-id="985c7-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="985c7-142">Verwendet den Standardwert [httpsredirectionoptions. httpsport](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), es sei denn, er wurde durch die `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [iserveraddressesfeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)überschrieben.</span><span class="sxs-lookup"><span data-stu-id="985c7-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="985c7-143">Es wird empfohlen, temporäre Umleitungen anstelle von permanenten Umleitungen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="985c7-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="985c7-144">Das Zwischenspeichern von Links kann in Entwicklungsumgebungen zu instabiles Verhalten führen.</span><span class="sxs-lookup"><span data-stu-id="985c7-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="985c7-145">Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, finden Sie weitere Informationen im Abschnitt [configure permanent Umleitungen in Production](#configure-permanent-redirects-in-production) .</span><span class="sxs-lookup"><span data-stu-id="985c7-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="985c7-146">Wir empfehlen die Verwendung von [hsts](#http-strict-transport-security-protocol-hsts) , um Clients zu signalisieren, dass nur sichere Ressourcenanforderungen an die APP gesendet werden sollen (nur in der Produktion).</span><span class="sxs-lookup"><span data-stu-id="985c7-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="985c7-147">Portkonfiguration</span><span class="sxs-lookup"><span data-stu-id="985c7-147">Port configuration</span></span>

<span data-ttu-id="985c7-148">Ein Port muss verfügbar sein, damit die Middleware eine unsichere Anforderung an HTTPS umleiten muss.</span><span class="sxs-lookup"><span data-stu-id="985c7-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="985c7-149">Wenn kein Port verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="985c7-149">If no port is available:</span></span>

* <span data-ttu-id="985c7-150">Umleitung zu HTTPS erfolgt nicht.</span><span class="sxs-lookup"><span data-stu-id="985c7-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="985c7-151">Die Middleware protokolliert die Warnung "der HTTPS-Port für die Umleitung konnte nicht bestimmt werden".</span><span class="sxs-lookup"><span data-stu-id="985c7-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="985c7-152">Geben Sie den HTTPS-Port an, indem Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="985c7-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="985c7-153">Legen Sie [httpsredirectionoptions. httpsport](#options)fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="985c7-154">Festlegen der `https_port` [Host Einstellung](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span><span class="sxs-lookup"><span data-stu-id="985c7-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="985c7-155">In der Host Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="985c7-155">In host configuration.</span></span>
  * <span data-ttu-id="985c7-156">Durch Festlegen der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable.</span><span class="sxs-lookup"><span data-stu-id="985c7-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="985c7-157">Durch Hinzufügen eines Eintrags auf oberster Ebene in *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="985c7-157">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="985c7-158">Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)verwenden.</span><span class="sxs-lookup"><span data-stu-id="985c7-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="985c7-159">Die Umgebungsvariable konfiguriert den Server.</span><span class="sxs-lookup"><span data-stu-id="985c7-159">The environment variable configures the server.</span></span> <span data-ttu-id="985c7-160">Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="985c7-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="985c7-161">Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="985c7-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="985c7-162">Festlegen der `https_port` [Host Einstellung](xref:fundamentals/host/web-host#https-port):</span><span class="sxs-lookup"><span data-stu-id="985c7-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="985c7-163">In der Host Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="985c7-163">In host configuration.</span></span>
  * <span data-ttu-id="985c7-164">Durch Festlegen der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable.</span><span class="sxs-lookup"><span data-stu-id="985c7-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="985c7-165">Durch Hinzufügen eines Eintrags auf oberster Ebene in *:::no-loc(appsettings.json):::* :</span><span class="sxs-lookup"><span data-stu-id="985c7-165">By adding a top-level entry in *:::no-loc(appsettings.json):::* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/:::no-loc(appsettings.json):::?highlight=2)]

* <span data-ttu-id="985c7-166">Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](xref:fundamentals/host/web-host#server-urls)verwenden.</span><span class="sxs-lookup"><span data-stu-id="985c7-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="985c7-167">Die Umgebungsvariable konfiguriert den Server.</span><span class="sxs-lookup"><span data-stu-id="985c7-167">The environment variable configures the server.</span></span> <span data-ttu-id="985c7-168">Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="985c7-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="985c7-169">Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="985c7-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="985c7-170">Legen Sie in Entwicklung in *launchsettings.js* eine HTTPS-URL fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-170">In development, set an HTTPS URL in *launchsettings.json* .</span></span> <span data-ttu-id="985c7-171">Aktivieren Sie HTTPS, wenn IIS Express verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="985c7-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="985c7-172">Konfigurieren Sie einen HTTPS-URL-Endpunkt für eine Public-Edge-Bereitstellung von [Kestrel](xref:fundamentals/servers/kestrel) -Server oder [HTTP.sys](xref:fundamentals/servers/httpsys) -Server.</span><span class="sxs-lookup"><span data-stu-id="985c7-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="985c7-173">Nur **ein HTTPS-Port** wird von der APP verwendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="985c7-174">Die Middleware ermittelt den Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="985c7-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="985c7-175">Wenn eine app in einer Reverseproxykonfiguration ausgeführt wird, ist <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="985c7-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="985c7-176">Legen Sie den Port mithilfe eines der anderen in diesem Abschnitt beschriebenen Ansätze fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="985c7-177">Edgebereitstellungen</span><span class="sxs-lookup"><span data-stu-id="985c7-177">Edge deployments</span></span> 

<span data-ttu-id="985c7-178">Wenn Kestrel oder HTTP.sys als öffentlicher Edge-Server verwendet wird, muss Kestrel oder HTTP.sys für das lauschen an beiden Optionen konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="985c7-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="985c7-179">Der sichere Port, an den der Client umgeleitet wird (in der Regel 443 in der Produktionsumgebung und 5001 in der Entwicklung).</span><span class="sxs-lookup"><span data-stu-id="985c7-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="985c7-180">Der unsichere Port (in der Regel 80 in der Produktionsumgebung und 5000 in der Entwicklung).</span><span class="sxs-lookup"><span data-stu-id="985c7-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="985c7-181">Der Client muss auf den unsicheren Port zugreifen können, damit die APP eine unsichere Anforderung empfängt und den Client an den sicheren Port umleitet.</span><span class="sxs-lookup"><span data-stu-id="985c7-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="985c7-182">Weitere Informationen finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) oder <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="985c7-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="985c7-183">Bereitstellungsszenarien</span><span class="sxs-lookup"><span data-stu-id="985c7-183">Deployment scenarios</span></span>

<span data-ttu-id="985c7-184">Für jede Firewall zwischen dem Client und dem Server müssen auch Kommunikationsports für den Datenverkehr geöffnet sein.</span><span class="sxs-lookup"><span data-stu-id="985c7-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="985c7-185">Wenn Anforderungen in einer Reverseproxykonfiguration weitergeleitet werden, verwenden Sie die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) , bevor Sie die HTTPS-Umleitungs</span><span class="sxs-lookup"><span data-stu-id="985c7-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="985c7-186">Die Middleware für weitergeleitete Header aktualisiert `Request.Scheme` mit dem- `X-Forwarded-Proto` Header.</span><span class="sxs-lookup"><span data-stu-id="985c7-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="985c7-187">Die Middleware ermöglicht das ordnungsgemäße Funktionieren von Umleitungs-URIs und anderen Sicherheitsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="985c7-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="985c7-188">Wenn die Middleware für weitergeleitete Header nicht verwendet wird, empfängt die Back-End-App möglicherweise nicht das richtige Schema und befindet sich in einer Umleitungs Schleife.</span><span class="sxs-lookup"><span data-stu-id="985c7-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="985c7-189">Eine gängige Fehlermeldung für den Endbenutzer besteht darin, dass zu viele Umleitungen aufgetreten sind.</span><span class="sxs-lookup"><span data-stu-id="985c7-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="985c7-190">Wenn Sie Azure App Service bereitstellen, befolgen Sie die Anweisungen im [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="985c7-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="985c7-191">Optionen</span><span class="sxs-lookup"><span data-stu-id="985c7-191">Options</span></span>

<span data-ttu-id="985c7-192">Der folgende markierte Code ruft [addhttpsredirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) auf, um Middleware-Optionen zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="985c7-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="985c7-193">`AddHttpsRedirection`Das Aufrufen von ist nur erforderlich, um die Werte von oder zu ändern `HttpsPort` `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="985c7-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="985c7-194">Der vorangehende markierte Code:</span><span class="sxs-lookup"><span data-stu-id="985c7-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="985c7-195">Legt [httpsredirectionoptions. redirectstatus Code](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) auf fest <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> . Dies ist der Standardwert.</span><span class="sxs-lookup"><span data-stu-id="985c7-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="985c7-196">Verwenden Sie die Felder der- <xref:Microsoft.AspNetCore.Http.StatusCodes> Klasse für Zuweisungen zu `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="985c7-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="985c7-197">Legt den HTTPS-Port auf 5001 fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="985c7-198">Konfigurieren dauerhafter Umleitungen in der Produktion</span><span class="sxs-lookup"><span data-stu-id="985c7-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="985c7-199">Die Middleware sendet standardmäßig ein [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) -Wert mit allen Umleitungen.</span><span class="sxs-lookup"><span data-stu-id="985c7-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="985c7-200">Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, packen Sie die middlewareoptions-Konfiguration in eine bedingte Prüfung für eine nicht Entwicklungsumgebung.</span><span class="sxs-lookup"><span data-stu-id="985c7-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="985c7-201">Beim Konfigurieren von Diensten in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="985c7-201">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="985c7-202">Beim Konfigurieren von Diensten in *Startup.cs* :</span><span class="sxs-lookup"><span data-stu-id="985c7-202">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="985c7-203">Alternativer Ansatz der HTTPS-Umleitungs Middleware</span><span class="sxs-lookup"><span data-stu-id="985c7-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="985c7-204">Eine Alternative zur Verwendung der HTTPS-Umleitungs Middleware ( `UseHttpsRedirection` ) ist die Verwendung der URL-Umschreib enden Middleware ( `AddRedirectToHttps` ).</span><span class="sxs-lookup"><span data-stu-id="985c7-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="985c7-205">`AddRedirectToHttps` kann auch den Statuscode und den Port festlegen, wenn die Umleitung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="985c7-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="985c7-206">Weitere Informationen finden Sie unter [URL-Umschreib Ende Middleware](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="985c7-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="985c7-207">Beim Umleiten an HTTPS ohne die Anforderung zusätzlicher Umleitungs Regeln empfiehlt sich die Verwendung der in diesem Thema beschriebenen HTTPS-Umleitungs Middleware ( `UseHttpsRedirection` ).</span><span class="sxs-lookup"><span data-stu-id="985c7-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="985c7-208">HTTP Strict Transport Security Protocol (hsts)</span><span class="sxs-lookup"><span data-stu-id="985c7-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="985c7-209">Pro [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)ist die [http-strikte Transport Sicherheit (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) eine Opt-in-Sicherheits Erweiterung, die durch eine Web-App durch die Verwendung eines Antwort Headers angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="985c7-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="985c7-210">Wenn ein [Browser, der hsts unterstützt,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) diesen Header empfängt:</span><span class="sxs-lookup"><span data-stu-id="985c7-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="985c7-211">Der Browser speichert die Konfiguration für die Domäne, die das Senden jeglicher Kommunikation über HTTP verhindert.</span><span class="sxs-lookup"><span data-stu-id="985c7-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="985c7-212">Der Browser erzwingt die gesamte Kommunikation über HTTPS.</span><span class="sxs-lookup"><span data-stu-id="985c7-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="985c7-213">Der Browser hindert den Benutzer daran, nicht vertrauenswürdige oder ungültige Zertifikate zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="985c7-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="985c7-214">Der Browser deaktiviert Eingabe Aufforderungen, mit denen ein Benutzer ein solches Zertifikat vorübergehend als vertrauenswürdig einstufen kann.</span><span class="sxs-lookup"><span data-stu-id="985c7-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="985c7-215">Da hsts vom Client erzwungen wird, gelten einige Einschränkungen:</span><span class="sxs-lookup"><span data-stu-id="985c7-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="985c7-216">Der Client muss hsts unterstützen.</span><span class="sxs-lookup"><span data-stu-id="985c7-216">The client must support HSTS.</span></span>
* <span data-ttu-id="985c7-217">Hsts erfordert mindestens eine erfolgreiche HTTPS-Anforderung zum Einrichten der hsts-Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="985c7-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="985c7-218">Die Anwendung muss jede HTTP-Anforderung überprüfen und die HTTP-Anforderung umleiten oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="985c7-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="985c7-219">ASP.net Core 2,1 und höher implementiert hsts mit der- `UseHsts` Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="985c7-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="985c7-220">Der folgende Code ruft `UseHsts` auf, wenn sich die APP nicht im [Entwicklungsmodus befindet](xref:fundamentals/environments):</span><span class="sxs-lookup"><span data-stu-id="985c7-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="985c7-221">`UseHsts` wird in der Entwicklung nicht empfohlen, da die hsts-Einstellungen durch Browser stark zwischen speicherbar sind.</span><span class="sxs-lookup"><span data-stu-id="985c7-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="985c7-222">Standardmäßig `UseHsts` schließt die lokale Loopback Adresse aus.</span><span class="sxs-lookup"><span data-stu-id="985c7-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="985c7-223">Legen Sie für Produktionsumgebungen, die HTTPS zum ersten Mal implementieren, die anfängliche [hstsoptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) -Einstellung mithilfe einer der-Methoden auf einen kleinen Wert fest. <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="985c7-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="985c7-224">Legen Sie den Wert von Stunden auf nicht mehr als einen Tag fest, falls Sie die HTTPS-Infrastruktur auf http zurücksetzen müssen.</span><span class="sxs-lookup"><span data-stu-id="985c7-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="985c7-225">Wenn Sie sicher sind, dass die Konfiguration der HTTPS-Konfiguration von der Bedeutung ist, erhöhen Sie den hsts- `max-age` Wert. ein häufig verwendeter Wert ist ein Jahr.</span><span class="sxs-lookup"><span data-stu-id="985c7-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="985c7-226">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="985c7-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="985c7-227">Legt den vorab laden-Parameter des `Strict-Transport-Security` Headers fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="985c7-228">Vorab laden ist nicht Teil der [RFC hsts-Spezifikation](https://tools.ietf.org/html/rfc6797), wird aber von Webbrowsern unterstützt, um hsts-Websites bei einer Neuinstallation vorab zu laden.</span><span class="sxs-lookup"><span data-stu-id="985c7-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="985c7-229">Weitere Informationen finden Sie unter [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="985c7-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="985c7-230">Aktiviert [includesubdomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), die die hsts-Richtlinie auf Host Unterdomänen anwendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="985c7-231">Legt den- `max-age` Parameter des- `Strict-Transport-Security` Headers explizit auf 60 Tage fest.</span><span class="sxs-lookup"><span data-stu-id="985c7-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="985c7-232">Wenn nicht festgelegt, wird standardmäßig 30 Tage verwendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="985c7-233">Weitere Informationen finden Sie in der [max-age-Direktive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="985c7-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="985c7-234">Fügt `example.com` der Liste der auszuschließenden Hosts hinzu.</span><span class="sxs-lookup"><span data-stu-id="985c7-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="985c7-235">`UseHsts` schließt die folgenden Loopback Hosts aus:</span><span class="sxs-lookup"><span data-stu-id="985c7-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="985c7-236">`localhost` : Die IPv4-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="985c7-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="985c7-237">`127.0.0.1` : Die IPv4-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="985c7-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="985c7-238">`[::1]` : Die IPv6-Loopback Adresse.</span><span class="sxs-lookup"><span data-stu-id="985c7-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="985c7-239">Ausschließen von HTTPS/hsts bei der Projekt Erstellung</span><span class="sxs-lookup"><span data-stu-id="985c7-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="985c7-240">In einigen Back-End-Dienst Szenarios, in denen die Verbindungssicherheit an der öffentlichen Kante des Netzwerks behandelt wird, ist die Konfiguration der Verbindungssicherheit an den einzelnen Knoten nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="985c7-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="985c7-241">Web-Apps, die aus den Vorlagen in Visual Studio oder dem [dotnet New](/dotnet/core/tools/dotnet-new) -Befehl generiert werden, ermöglichen die [HTTPS-Umleitung](#require-https) und [hsts](#http-strict-transport-security-protocol-hsts).</span><span class="sxs-lookup"><span data-stu-id="985c7-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="985c7-242">Bei bereit Stellungen, für die diese Szenarios nicht erforderlich sind, können Sie HTTPS/hsts bei der Erstellung der APP aus der Vorlage ablehnen.</span><span class="sxs-lookup"><span data-stu-id="985c7-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="985c7-243">So beenden Sie HTTPS/hsts:</span><span class="sxs-lookup"><span data-stu-id="985c7-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="985c7-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="985c7-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="985c7-245">Deaktivieren Sie das Kontrollkästchen **für HTTPS konfigurieren** .</span><span class="sxs-lookup"><span data-stu-id="985c7-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="985c7-248">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="985c7-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="985c7-249">Verwenden Sie die `--no-https`-Option.</span><span class="sxs-lookup"><span data-stu-id="985c7-249">Use the `--no-https` option.</span></span> <span data-ttu-id="985c7-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="985c7-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="985c7-251">Vertrauen des ASP.net Core HTTPS-Entwicklungs Zertifikats unter Windows und macOS</span><span class="sxs-lookup"><span data-stu-id="985c7-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="985c7-252">Die .net Core SDK enthält ein HTTPS-Entwicklungs Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="985c7-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="985c7-253">Das Zertifikat wird im Rahmen der erstmaligen Durchführung installiert.</span><span class="sxs-lookup"><span data-stu-id="985c7-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="985c7-254">Beispielsweise `dotnet --info` erzeugt eine Variation der folgenden Ausgabe:</span><span class="sxs-lookup"><span data-stu-id="985c7-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="985c7-255">Durch das Installieren des ASP.NET Core SDK wird das ASP.NET Core-HTTPS-Entwicklungszertifikat im lokalen Benutzerzertifikatspeicher installiert.</span><span class="sxs-lookup"><span data-stu-id="985c7-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="985c7-256">Das Zertifikat wurde installiert, aber es ist nicht vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="985c7-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="985c7-257">Um dem Zertifikat zu vertrauen, führen Sie den einmaligen Schritt aus, um das dotnet- `dev-certs` Tool auszuführen:</span><span class="sxs-lookup"><span data-stu-id="985c7-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="985c7-258">Der folgende Befehl stellt Hilfe zum `dev-certs`-Tool bereit:</span><span class="sxs-lookup"><span data-stu-id="985c7-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="985c7-259">Einrichten eines Entwickler Zertifikats für docker</span><span class="sxs-lookup"><span data-stu-id="985c7-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="985c7-260">Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span><span class="sxs-lookup"><span data-stu-id="985c7-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="985c7-261">Vertrauen des HTTPS-Zertifikats unter Linux</span><span class="sxs-lookup"><span data-stu-id="985c7-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="985c7-262">Anweisungen zu Linux finden Sie in der Dokumentation zur Distribution.</span><span class="sxs-lookup"><span data-stu-id="985c7-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="985c7-263">Vertrauen des HTTPS-Zertifikats aus dem Windows-Subsystem für Linux</span><span class="sxs-lookup"><span data-stu-id="985c7-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="985c7-264">Das Windows-Subsystem für Linux (WSL) generiert ein selbst signiertes HTTPS-Zertifikat. So konfigurieren Sie den Windows-Zertifikat Speicher für die Vertrauenswürdigkeit des WSL-Zertifikats:</span><span class="sxs-lookup"><span data-stu-id="985c7-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="985c7-265">Führen Sie den folgenden Befehl aus, um das von WSL generierte Zertifikat zu exportieren:</span><span class="sxs-lookup"><span data-stu-id="985c7-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="985c7-266">Führen Sie in einem WSL-Fenster den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="985c7-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="985c7-267">Mit dem vorangehenden Befehl werden die Umgebungsvariablen festgelegt, sodass Linux das vertrauenswürdige Windows-Zertifikat verwendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="985c7-268">Beheben von Zertifikats Problemen</span><span class="sxs-lookup"><span data-stu-id="985c7-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="985c7-269">Dieser Abschnitt enthält Hilfe, wenn das ASP.net Core HTTPS-Entwicklungs Zertifikat [installiert und vertrauenswürdig](#trust)ist, Sie jedoch weiterhin Browser Warnungen haben, dass das Zertifikat nicht vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="985c7-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="985c7-270">Das ASP.net Core HTTPS-Entwicklungs Zertifikat wird von [Kestrel](xref:fundamentals/servers/kestrel)verwendet.</span><span class="sxs-lookup"><span data-stu-id="985c7-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="985c7-271">Alle Plattformen-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="985c7-271">All platforms - certificate not trusted</span></span>

<span data-ttu-id="985c7-272">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="985c7-272">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="985c7-273">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="985c7-273">Close any browser instances open.</span></span> <span data-ttu-id="985c7-274">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="985c7-274">Open a new browser window to app.</span></span> <span data-ttu-id="985c7-275">Die Zertifikats Vertrauensstellung wird von Browsern zwischengespeichert.</span><span class="sxs-lookup"><span data-stu-id="985c7-275">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="985c7-276">Mit den obigen Befehlen werden die meisten Browser Vertrauensstellungs Probleme gelöst.</span><span class="sxs-lookup"><span data-stu-id="985c7-276">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="985c7-277">Wenn der Browser dem Zertifikat immer noch nicht vertraut, befolgen Sie die folgenden plattformspezifischen Vorschläge.</span><span class="sxs-lookup"><span data-stu-id="985c7-277">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="985c7-278">Docker-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="985c7-278">Docker - certificate not trusted</span></span>

* <span data-ttu-id="985c7-279">Löschen Sie den Ordner " *c:\Users \{ User} \appdata\roaming\asp.net\https* ".</span><span class="sxs-lookup"><span data-stu-id="985c7-279">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="985c7-280">Bereinigen Sie die Projekt Mappe.</span><span class="sxs-lookup"><span data-stu-id="985c7-280">Clean the solution.</span></span> <span data-ttu-id="985c7-281">Löschen Sie die Ordner *bin* und *obj* .</span><span class="sxs-lookup"><span data-stu-id="985c7-281">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="985c7-282">Starten Sie das Entwicklungs Tool neu.</span><span class="sxs-lookup"><span data-stu-id="985c7-282">Restart the development tool.</span></span> <span data-ttu-id="985c7-283">Beispielsweise Visual Studio, Visual Studio Code oder Visual Studio für Mac.</span><span class="sxs-lookup"><span data-stu-id="985c7-283">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="985c7-284">Windows-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="985c7-284">Windows - certificate not trusted</span></span>

* <span data-ttu-id="985c7-285">Überprüfen Sie die Zertifikate im Zertifikat Speicher.</span><span class="sxs-lookup"><span data-stu-id="985c7-285">Check the certificates in the certificate store.</span></span> <span data-ttu-id="985c7-286">Es muss ein `localhost` Zertifikat mit dem anzeigen `ASP.NET Core HTTPS development certificate` Amen unter `Current User > Personal > Certificates` und vorhanden sein. `Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="985c7-286">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="985c7-287">Entfernen Sie alle gefundenen Zertifikate von persönlichen und vertrauenswürdigen Stamm Zertifizierungsstellen.</span><span class="sxs-lookup"><span data-stu-id="985c7-287">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="985c7-288">Entfernen Sie das IIS Express localhost-Zertifikat **nicht** .</span><span class="sxs-lookup"><span data-stu-id="985c7-288">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="985c7-289">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="985c7-289">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="985c7-290">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="985c7-290">Close any browser instances open.</span></span> <span data-ttu-id="985c7-291">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="985c7-291">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="985c7-292">OS X-Zertifikat nicht vertrauenswürdig</span><span class="sxs-lookup"><span data-stu-id="985c7-292">OS X - certificate not trusted</span></span>

* <span data-ttu-id="985c7-293">Öffnen Sie den Keychain-Zugriff.</span><span class="sxs-lookup"><span data-stu-id="985c7-293">Open KeyChain Access.</span></span>
* <span data-ttu-id="985c7-294">Wählen Sie die System Schlüsselkette aus.</span><span class="sxs-lookup"><span data-stu-id="985c7-294">Select the System keychain.</span></span>
* <span data-ttu-id="985c7-295">Überprüfen Sie, ob ein localhost-Zertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="985c7-295">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="985c7-296">Überprüfen Sie, ob es ein `+` Symbol auf dem Symbol enthält, um anzugeben, dass es für alle Benutzer vertrauenswürdig ist.</span><span class="sxs-lookup"><span data-stu-id="985c7-296">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="985c7-297">Entfernen Sie das Zertifikat aus der Keychain des Systems.</span><span class="sxs-lookup"><span data-stu-id="985c7-297">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="985c7-298">Führen Sie die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="985c7-298">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="985c7-299">Schließen Sie alle geöffneten Browser Instanzen.</span><span class="sxs-lookup"><span data-stu-id="985c7-299">Close any browser instances open.</span></span> <span data-ttu-id="985c7-300">Öffnen Sie ein neues Browserfenster für die app.</span><span class="sxs-lookup"><span data-stu-id="985c7-300">Open a new browser window to app.</span></span>

<span data-ttu-id="985c7-301">Informationen zur Behandlung von Zertifikat Problemen mit Visual Studio finden [Sie unter https-Fehler mit IIS Express (dotnet/aspnetcore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) .</span><span class="sxs-lookup"><span data-stu-id="985c7-301">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="985c7-302">IIS Express SSL-Zertifikat, das mit Visual Studio verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="985c7-302">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="985c7-303">Um Probleme mit dem IIS Express Zertifikat zu beheben, wählen Sie im Visual Studio-Installer **Reparieren** aus.</span><span class="sxs-lookup"><span data-stu-id="985c7-303">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="985c7-304">Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/16892).</span><span class="sxs-lookup"><span data-stu-id="985c7-304">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="985c7-305">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="985c7-305">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="985c7-306">Host ASP.net Core unter Linux mit Apache: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="985c7-306">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="985c7-307">Host ASP.net Core unter Linux mit Nginx: HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="985c7-307">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="985c7-308">Einrichten von SSL unter IIS</span><span class="sxs-lookup"><span data-stu-id="985c7-308">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="985c7-309">OWASP hsts-Browserunterstützung</span><span class="sxs-lookup"><span data-stu-id="985c7-309">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)