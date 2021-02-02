---
title: Webserverimplementierungen in ASP.NET Core
author: rick-anderson
description: Ermitteln Sie die Webserver Kestrel und HTTP.sys für ASP.NET Core. Erfahren Sie mehr über das Auswählen eines Servers und darüber, wann ein Reverseproxyserver zu verwenden ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217556"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="db85d-104">Webserverimplementierungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db85d-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="db85d-105">Von [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) und [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="db85d-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="db85d-106">Eine ASP.NET Core-App wird über eine In-Process-Implementierung eines HTTP-Servers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="db85d-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="db85d-107">Die Serverimplementierung lauscht auf HTTP-Anforderungen und leitet diese als [Anforderungsfunktionen](xref:fundamentals/request-features), die in einer <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse zusammengefasst werden, an die App weiter.</span><span class="sxs-lookup"><span data-stu-id="db85d-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="db85d-108">Windows</span><span class="sxs-lookup"><span data-stu-id="db85d-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="db85d-109">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="db85d-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="db85d-110">Ein [Kestrel-Server](xref:fundamentals/servers/kestrel) stellt die plattformübergreifende Standardimplementierung von HTTP-Servern dar.</span><span class="sxs-lookup"><span data-stu-id="db85d-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="db85d-111">Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige erweiterte Features in HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="db85d-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="db85d-112">Weitere Informationen finden Sie unter [Kestrel im Vergleich zu HTTP.sys](#korh) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="db85d-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="db85d-113">IIS-HTTP-Server ist ein [In-Process-Server](#hosting-models) für IIS.</span><span class="sxs-lookup"><span data-stu-id="db85d-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="db85d-114">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="db85d-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="db85d-115">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App auf zwei unterschiedliche Arten ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="db85d-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="db85d-116">Im gleichen Prozess wie im IIS-Workerprozess (das [In-Process-Hostingmodell](#hosting-models)) mit dem IIS-HTTP-Server.</span><span class="sxs-lookup"><span data-stu-id="db85d-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="db85d-117">*In-Process* ist die empfohlene Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="db85d-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="db85d-118">In einem anderen Prozess als im IIS-Workerprozess (das [Out-of-Process-Hostingmodell](#hosting-models)) mit dem [Kestrel-Server](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="db85d-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="db85d-119">Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ist ein natives IIS-Modul, das native IIS-Anforderungen zwischen IIS und dem In-Process-IIS-HTTP-Server oder Kestrel verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="db85d-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="db85d-120">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="db85d-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="db85d-121">Kestrel im Vergleich zu HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="db85d-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="db85d-122">Kestrel bietet die folgenden Vorteile gegenüber HTTP.sys:</span><span class="sxs-lookup"><span data-stu-id="db85d-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="db85d-123">Bessere Leistung und Arbeitsspeicherauslastung</span><span class="sxs-lookup"><span data-stu-id="db85d-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="db85d-124">Plattformübergreifend</span><span class="sxs-lookup"><span data-stu-id="db85d-124">Cross platform</span></span>
  * <span data-ttu-id="db85d-125">Agilität: Entwicklung und Patchen unabhängig vom Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="db85d-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="db85d-126">Programmgesteuerte Port- und TLS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="db85d-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="db85d-127">Erweiterbarkeit mit Möglichkeit für Protokolle wie [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) und alternative Transporte</span><span class="sxs-lookup"><span data-stu-id="db85d-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="db85d-128">HTTP.sys fungiert als freigegebene Kernelmoduskomponente mit den folgenden Features, die Kestrel nicht besitzt:</span><span class="sxs-lookup"><span data-stu-id="db85d-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="db85d-129">Portfreigabe</span><span class="sxs-lookup"><span data-stu-id="db85d-129">Port sharing</span></span>
  * <span data-ttu-id="db85d-130">Windows-Authentifizierung im Kernelmodus.</span><span class="sxs-lookup"><span data-stu-id="db85d-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="db85d-131">[Kestrel unterstützt nur die Authentifizierung im Benutzermodus](xref:security/authentication/windowsauth#kestrel).</span><span class="sxs-lookup"><span data-stu-id="db85d-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="db85d-132">Schnelle Proxyfunktion über Warteschlangenübertragungen</span><span class="sxs-lookup"><span data-stu-id="db85d-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="db85d-133">Direkte Dateiübertragung</span><span class="sxs-lookup"><span data-stu-id="db85d-133">Direct file transmission</span></span>
  * <span data-ttu-id="db85d-134">Zwischenspeichern von Antworten</span><span class="sxs-lookup"><span data-stu-id="db85d-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="db85d-135">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="db85d-135">Hosting models</span></span>

<span data-ttu-id="db85d-136">Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Arbeitsprozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="db85d-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="db85d-137">Durch das In-Process-Hosting wird die Leistung des Out-of-Process-Hosting verbessert, da Anforderungen nicht per Proxy über den Loopbackadapter weitergeleitet werden. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zum selben Computer zurück leitet.</span><span class="sxs-lookup"><span data-stu-id="db85d-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="db85d-138">IIS erledigt das Prozessmanagement mit dem [Windows-Prozessaktivierungsdienst (Process Activation Service, WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="db85d-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="db85d-139">Unter Verwendung von Out-of-Process-Hosting werden ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt, und das Modul führt die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="db85d-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="db85d-140">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="db85d-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="db85d-141">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="db85d-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="db85d-142">Weitere Informationen und Anleitungen zur Konfiguration finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="db85d-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="db85d-143">macOS</span><span class="sxs-lookup"><span data-stu-id="db85d-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="db85d-144">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="db85d-145">Linux</span><span class="sxs-lookup"><span data-stu-id="db85d-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="db85d-146">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="db85d-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="db85d-147">Kestrel</span></span>

 <span data-ttu-id="db85d-148">Ein [Kestrel-Server](xref:fundamentals/servers/kestrel) stellt die plattformübergreifende Standardimplementierung von HTTP-Servern dar.</span><span class="sxs-lookup"><span data-stu-id="db85d-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="db85d-149">Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige erweiterte Features in HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="db85d-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="db85d-150">Weitere Informationen finden Sie unter [Kestrel im Vergleich zu HTTP.sys](#korh) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="db85d-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="db85d-151">Verwendung von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="db85d-151">Use Kestrel:</span></span>

* <span data-ttu-id="db85d-152">Eigenständig als Edge-Server zur Verarbeitung von Anforderungen, die direkt aus einem Netzwerk, auch über das Internet, kommen.</span><span class="sxs-lookup"><span data-stu-id="db85d-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="db85d-154">Mit einem *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="db85d-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="db85d-155">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="db85d-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="db85d-157">Beide Hostingkonfigurationen (&mdash;mit oder ohne Reverseproxyserver&mdash;) werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="db85d-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="db85d-158">Leitfäden zur Kestrel-Konfiguration und Informationen darüber, wann Kestrel in einer Reverseproxykonfiguration verwendet wird, finden Sie unter <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="db85d-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="db85d-159">Windows</span><span class="sxs-lookup"><span data-stu-id="db85d-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="db85d-160">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="db85d-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="db85d-161">[Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.</span><span class="sxs-lookup"><span data-stu-id="db85d-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="db85d-162">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="db85d-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="db85d-163">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App in einem anderen Prozess als im IIS-Workerprozess (*Out-of-Process*) mit dem [Kestrel-Server](#kestrel) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="db85d-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="db85d-164">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="db85d-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="db85d-165">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="db85d-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="db85d-166">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="db85d-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="db85d-167">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="db85d-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

<span data-ttu-id="db85d-169">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="db85d-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="db85d-170">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="db85d-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="db85d-171">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="db85d-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="db85d-172">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="db85d-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="db85d-173">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="db85d-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="db85d-174">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="db85d-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="db85d-175">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="db85d-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="db85d-176">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="db85d-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="db85d-177">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="db85d-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="db85d-178">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="db85d-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="db85d-179">In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:</span><span class="sxs-lookup"><span data-stu-id="db85d-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="db85d-180">macOS</span><span class="sxs-lookup"><span data-stu-id="db85d-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="db85d-181">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="db85d-182">Linux</span><span class="sxs-lookup"><span data-stu-id="db85d-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="db85d-183">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="db85d-184">Nginx und Kestrel</span><span class="sxs-lookup"><span data-stu-id="db85d-184">Nginx with Kestrel</span></span>

<span data-ttu-id="db85d-185">Informationen dazu, wie Nginx unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="db85d-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="db85d-186">Apache und Kestrel</span><span class="sxs-lookup"><span data-stu-id="db85d-186">Apache with Kestrel</span></span>

<span data-ttu-id="db85d-187">Informationen dazu, wie Apache unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="db85d-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="db85d-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="db85d-188">HTTP.sys</span></span>

<span data-ttu-id="db85d-189">Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist HTTP.sys eine Alternative zu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="db85d-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="db85d-190">Kestrel wird vor HTTP.sys empfohlen, es sei denn, die App benötigt Features, die in Kestrel nicht verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="db85d-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="db85d-191">Weitere Informationen finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="db85d-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="db85d-193">Außerdem kann HTTP.sys auch bei Apss zum Einsatz kommen, die nur in einem internen Netzwerk verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="db85d-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="db85d-195">Den Leitfaden für die HTTP.sys-Konfiguration finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="db85d-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="db85d-196">ASP.NET Core-Serverinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="db85d-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="db85d-197">Die Schnittstelle <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>, die in der `Startup.Configure`-Methode vorhanden ist, macht die Eigenschaft <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vom Typ <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="db85d-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="db85d-198">Kestrel und HTTP.sys machen nur jeweils eine einzelne Funktion verfügbar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Andere Serverimplementierungen machen jedoch möglicherweise weitere Funktionalitäten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="db85d-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="db85d-199">Mit `IServerAddressesFeature` kann ermittelt werden, an welchen Port die Serverimplementierung zur Laufzeit gebunden ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="db85d-200">Benutzerdefinierte Server</span><span class="sxs-lookup"><span data-stu-id="db85d-200">Custom servers</span></span>

<span data-ttu-id="db85d-201">Wenn die integrierten Server nicht den Anforderungen der App entsprechen, kann eine benutzerdefinierte Serverimplementierung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="db85d-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="db85d-202">In der [Anleitung zu Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) wird erläutert, wie eine auf [Nowin](https://github.com/Bobris/Nowin) basierende <xref:Microsoft.AspNetCore.Hosting.Server.IServer>-Implementierung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="db85d-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="db85d-203">Nur die Schnittstellen von Funktionen, die von der App verwendet werden, erfordern Implementierung, wobei mindestens <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> und <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> unterstützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="db85d-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="db85d-204">Serverstart</span><span class="sxs-lookup"><span data-stu-id="db85d-204">Server startup</span></span>

<span data-ttu-id="db85d-205">Der Server wird gestartet, wenn die integrierte Entwicklungsumgebung (IDE) oder der Editor die App startet:</span><span class="sxs-lookup"><span data-stu-id="db85d-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="db85d-206">[Visual Studio:](https://visualstudio.microsoft.com) Startprofile können verwendet werden, um die App und den Server mit [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) oder mit der Konsole zu starten.</span><span class="sxs-lookup"><span data-stu-id="db85d-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="db85d-207">[Visual Studio Code](https://code.visualstudio.com/): Die App und der Server werden durch [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) gestartet, das den CoreCLR-Debugger aktiviert.</span><span class="sxs-lookup"><span data-stu-id="db85d-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="db85d-208">[Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/): Die App und der Server werden durch den [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) gestartet.</span><span class="sxs-lookup"><span data-stu-id="db85d-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="db85d-209">Wird die App über eine Eingabeaufforderung im Ordner des Projekts gestartet, startet [dotnet run](/dotnet/core/tools/dotnet-run) die App und den Server (nur Kestrel und HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="db85d-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="db85d-210">Die Konfiguration wird durch die Option `-c|--configuration` angegeben, die entweder auf `Debug` (Standardwert) oder `Release` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="db85d-211">Eine *launchSettings.json*-Datei bietet Konfiguration während des App-Starts mit `dotnet run` oder mit einem in Tools integrierten Debugger, z. B. Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="db85d-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="db85d-212">Sind in der Datei *launchSettings.json* Startprofile enthalten, verwenden Sie die Option `--launch-profile {PROFILE NAME}` mit dem Befehl `dotnet run`, oder wählen Sie das Profil in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="db85d-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="db85d-213">Weitere Informationen hierzu finden Sie unter [dotnet run](/dotnet/core/tools/dotnet-run) und unter [Verpacken einer Verteilung von .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="db85d-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="db85d-214">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="db85d-214">HTTP/2 support</span></span>

<span data-ttu-id="db85d-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden Bereitstellungsszenarien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="db85d-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="db85d-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="db85d-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="db85d-217">Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="db85d-217">Operating system</span></span>
    * <span data-ttu-id="db85d-218">Windows Server 2016/Windows 10 oder höher&dagger;</span><span class="sxs-lookup"><span data-stu-id="db85d-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="db85d-219">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="db85d-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="db85d-220">HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="db85d-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="db85d-221">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="db85d-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="db85d-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="db85d-223">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="db85d-224">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="db85d-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="db85d-225">IIS (In-Process)</span><span class="sxs-lookup"><span data-stu-id="db85d-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="db85d-226">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="db85d-227">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="db85d-228">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="db85d-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="db85d-229">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="db85d-230">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="db85d-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="db85d-231">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="db85d-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="db85d-232">&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="db85d-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="db85d-233">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="db85d-234">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="db85d-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="db85d-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="db85d-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="db85d-236">Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="db85d-236">Operating system</span></span>
    * <span data-ttu-id="db85d-237">Windows Server 2016/Windows 10 oder höher&dagger;</span><span class="sxs-lookup"><span data-stu-id="db85d-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="db85d-238">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="db85d-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="db85d-239">HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="db85d-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="db85d-240">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="db85d-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="db85d-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="db85d-242">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="db85d-243">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="db85d-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="db85d-244">IIS (In-Process)</span><span class="sxs-lookup"><span data-stu-id="db85d-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="db85d-245">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="db85d-246">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="db85d-247">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="db85d-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="db85d-248">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="db85d-249">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="db85d-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="db85d-250">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="db85d-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="db85d-251">&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="db85d-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="db85d-252">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="db85d-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="db85d-253">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="db85d-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="db85d-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="db85d-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="db85d-255">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="db85d-256">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="db85d-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="db85d-257">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="db85d-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="db85d-258">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="db85d-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="db85d-259">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="db85d-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="db85d-260">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="db85d-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="db85d-261">Eine HTTP/2-Verbindung muss [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) und TLS 1.2 oder höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="db85d-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="db85d-262">Weitere Informationen finden Sie in den Themen, die Ihre Serverbereitstellungsszenarien betreffen.</span><span class="sxs-lookup"><span data-stu-id="db85d-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="db85d-263">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="db85d-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
