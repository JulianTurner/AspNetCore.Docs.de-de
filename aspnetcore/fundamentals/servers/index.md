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
ms.openlocfilehash: a27fdd70963830d22b3501972d6150dde5e1ea54
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174596"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="b8dfd-104">Webserverimplementierungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b8dfd-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="b8dfd-105">Von [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73) und [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="b8dfd-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="b8dfd-106">Eine ASP.NET Core-App wird über eine In-Process-Implementierung eines HTTP-Servers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="b8dfd-107">Die Serverimplementierung lauscht auf HTTP-Anforderungen und leitet diese als [Anforderungsfunktionen](xref:fundamentals/request-features), die in einer <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse zusammengefasst werden, an die App weiter.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="b8dfd-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b8dfd-108">Kestrel</span></span>

<span data-ttu-id="b8dfd-109">Kestrel ist der Standardwebserver, der für ASP.NET Core-Projektvorlagen erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="b8dfd-110">Verwendung von Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-110">Use Kestrel:</span></span>

* <span data-ttu-id="b8dfd-111">Eigenständig als Edge-Server zur Verarbeitung von Anforderungen, die direkt aus einem Netzwerk, auch über das Internet, kommen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel kommuniziert direkt und ohne Reverseproxyserver mit dem Internet](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="b8dfd-113">Mit einem *Reverseproxyserver* wie [IIS (Internetinformationsdienste)](https://www.iis.net/), [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b8dfd-114">Ein Reverseproxyserver empfängt HTTP-Anforderungen aus dem Internet und leitet diese an Kestrel weiter.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel kommuniziert indirekt mit dem Internet über einen Reverseproxyserver wie IIS, Nginx oder Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b8dfd-116">Beide Hostingkonfigurationen (&mdash;mit oder ohne Reverseproxyserver&mdash;) werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="b8dfd-117">Leitfäden zur Kestrel-Konfiguration und Informationen darüber, wann Kestrel in einer Reverseproxykonfiguration verwendet wird, finden Sie unter <xref:fundamentals/servers/kestrel>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="b8dfd-118">Windows</span><span class="sxs-lookup"><span data-stu-id="b8dfd-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8dfd-119">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="b8dfd-120">Ein [Kestrel-Server](xref:fundamentals/servers/kestrel) stellt die plattformübergreifende Standardimplementierung von HTTP-Servern dar.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="b8dfd-121">IIS-HTTP-Server ist ein [In-Process-Server](#hosting-models) für IIS.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="b8dfd-122">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="b8dfd-123">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App auf zwei unterschiedliche Arten ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="b8dfd-124">Im gleichen Prozess wie im IIS-Workerprozess (das [In-Process-Hostingmodell](#hosting-models)) mit dem IIS-HTTP-Server.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="b8dfd-125">*In-Process* ist die empfohlene Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="b8dfd-126">In einem anderen Prozess als im IIS-Workerprozess (das [Out-of-Process-Hostingmodell](#hosting-models)) mit dem [Kestrel-Server](#kestrel).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="b8dfd-127">Das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) ist ein natives IIS-Modul, das native IIS-Anforderungen zwischen IIS und dem In-Process-IIS-HTTP-Server oder Kestrel verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="b8dfd-128">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="b8dfd-129">Hostingmodelle</span><span class="sxs-lookup"><span data-stu-id="b8dfd-129">Hosting models</span></span>

<span data-ttu-id="b8dfd-130">Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Arbeitsprozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="b8dfd-131">Durch das In-Process-Hosting wird die Leistung des Out-of-Process-Hosting verbessert, da Anforderungen nicht per Proxy über den Loopbackadapter weitergeleitet werden. Dabei handelt es sich um eine Netzwerkschnittstelle, die ausgehenden Netzwerkdatenverkehr zum selben Computer zurück leitet.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="b8dfd-132">IIS erledigt das Prozessmanagement mit dem [Windows-Prozessaktivierungsdienst (Process Activation Service, WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b8dfd-133">Unter Verwendung von Out-of-Process-Hosting werden ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt, und das Modul führt die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="b8dfd-134">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="b8dfd-135">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b8dfd-136">Weitere Informationen und Anleitungen zur Konfiguration finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="b8dfd-137">macOS</span><span class="sxs-lookup"><span data-stu-id="b8dfd-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="b8dfd-138">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="b8dfd-139">Linux</span><span class="sxs-lookup"><span data-stu-id="b8dfd-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="b8dfd-140">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="b8dfd-141">Windows</span><span class="sxs-lookup"><span data-stu-id="b8dfd-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="b8dfd-142">ASP.NET Core wird mit folgendem Umfang ausgeliefert:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="b8dfd-143">[Kestrel Server](xref:fundamentals/servers/kestrel) ist der plattformübergreifende HTTP-Standardserver.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="b8dfd-144">[HTTP.sys Server](xref:fundamentals/servers/httpsys) ist ein nur für Windows verfügbarer HTTP-Server, der auf dem [Http.sys-Kerneltreiber und der HTTP Server API](/windows/desktop/Http/http-api-start-page) basiert.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="b8dfd-145">Wenn Sie [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) verwenden, wird die App in einem anderen Prozess als im IIS-Workerprozess (*Out-of-Process*) mit dem [Kestrel-Server](#kestrel) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="b8dfd-146">Da ASP.NET Core-Apps in einem Prozess getrennt vom IIS-Arbeitsprozess ausgeführt werden, führt das Modul die Prozessverwaltung durch.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="b8dfd-147">Das Modul startet den Prozess für die ASP.NET Core-App, wenn die erste Anforderung eingeht und startet die App neu, wenn sie heruntergefahren wird oder abstürzt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="b8dfd-148">Dies ist im Prinzip das gleiche Verhalten wie bei Apps, die prozessintern ausgeführt und durch den [Windows-Prozessaktivierungsdienst (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was) verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="b8dfd-149">Das folgende Diagramm zeigt die Beziehung zwischen IIS, dem ASP.NET Core-Modul und einer Out-of-Process gehosteten App:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core-Modul](_static/ancm-outofprocess.png)

<span data-ttu-id="b8dfd-151">Anforderungen gehen aus dem Internet an den Treiber „HTTP.sys“ ein, der im Kernelmodus betrieben wird.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="b8dfd-152">Der Treiber leitet die Anforderungen an IIS auf dem konfigurierten Port der Webseite weiter, normalerweise 80 (HTTP) oder 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="b8dfd-153">Das Modul leitet die Anforderung an Kestrel auf einem zufälligen Port der App weiter, der nicht Port 80 oder 443 entspricht.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="b8dfd-154">Das Modul gibt den Port über die Umgebungsvariable beim Start an. Die [Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) konfiguriert den Server so, dass er auf `http://localhost:{port}` lauscht.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="b8dfd-155">Zusätzliche Überprüfungen werden durchgeführt. Anforderungen, die nicht vom Modul stammen, werden abgelehnt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="b8dfd-156">Das Modul unterstützt die HTTPS-Weiterleitung nicht. Deshalb werden Anforderungen über HTTP weitergeleitet, selbst wenn sie von IIS über HTTPS empfangen wurden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="b8dfd-157">Nachdem Kestrel die Anforderung vom Modul erhalten hat, wird die Anforderung in die Middleware-Pipeline von ASP.NET Core eingestellt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="b8dfd-158">Die Middleware-Pipeline behandelt die Anforderung und gibt sie als `HttpContext`-Instanz an die App-Logik weiter.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="b8dfd-159">Die durch IIS-Integration hinzugefügte Middleware aktualisiert das Schema, die Remote-IP und die Pfadbasis, um der Weiterleitung der Anforderung an Kestrel Rechnung zu tragen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="b8dfd-160">Die Antwort der App wird dann an IIS zurückgegeben, wo sie per Push an den HTTP-Client zurückgegeben wird, der die Anforderung initiiert hat.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="b8dfd-161">In den folgenden Artikeln finden Sie Konfigurationsrichtlinien für IIS und ein ASP.NET Core-Modul:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="b8dfd-162">macOS</span><span class="sxs-lookup"><span data-stu-id="b8dfd-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="b8dfd-163">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="b8dfd-164">Linux</span><span class="sxs-lookup"><span data-stu-id="b8dfd-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="b8dfd-165">ASP.NET Core wird mit [Kestrel Server](xref:fundamentals/servers/kestrel) ausgeliefert, der der plattformübergreifende HTTP-Standardserver ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="b8dfd-166">Nginx und Kestrel</span><span class="sxs-lookup"><span data-stu-id="b8dfd-166">Nginx with Kestrel</span></span>

<span data-ttu-id="b8dfd-167">Informationen dazu, wie Nginx unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-nginx>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="b8dfd-168">Apache und Kestrel</span><span class="sxs-lookup"><span data-stu-id="b8dfd-168">Apache with Kestrel</span></span>

<span data-ttu-id="b8dfd-169">Informationen dazu, wie Apache unter Linux als Reverseproxyserver für Kestrel verwendet wird, finden Sie unter <xref:host-and-deploy/linux-apache>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="b8dfd-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b8dfd-170">HTTP.sys</span></span>

<span data-ttu-id="b8dfd-171">Wenn ASP.NET Core-Apps unter Windows ausgeführt werden, ist HTTP.sys eine Alternative zu Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="b8dfd-172">Grundsätzlich empfiehlt sich Kestrel, um die beste Leistung zu erzielen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="b8dfd-173">HTTP.sys kann in Szenarien verwendet werden, in denen die App mit dem Internet verbunden ist und erforderliche Funktionen von HTTP.sys, aber nicht von Kestrel unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="b8dfd-174">Weitere Informationen finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="b8dfd-176">Außerdem kann HTTP.sys auch bei Apss zum Einsatz kommen, die nur in einem internen Netzwerk verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="b8dfd-178">Den Leitfaden für die HTTP.sys-Konfiguration finden Sie unter <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="b8dfd-179">ASP.NET Core-Serverinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="b8dfd-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="b8dfd-180">Die Schnittstelle <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>, die in der `Startup.Configure`-Methode vorhanden ist, macht die Eigenschaft <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> vom Typ <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="b8dfd-181">Kestrel und HTTP.sys machen nur jeweils eine einzelne Funktion verfügbar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Andere Serverimplementierungen machen jedoch möglicherweise weitere Funktionalitäten verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="b8dfd-182">Mit `IServerAddressesFeature` kann ermittelt werden, an welchen Port die Serverimplementierung zur Laufzeit gebunden ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="b8dfd-183">Benutzerdefinierte Server</span><span class="sxs-lookup"><span data-stu-id="b8dfd-183">Custom servers</span></span>

<span data-ttu-id="b8dfd-184">Wenn die integrierten Server nicht den Anforderungen der App entsprechen, kann eine benutzerdefinierte Serverimplementierung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="b8dfd-185">In der [Anleitung zu Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) wird erläutert, wie eine auf [Nowin](https://github.com/Bobris/Nowin) basierende <xref:Microsoft.AspNetCore.Hosting.Server.IServer>-Implementierung erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="b8dfd-186">Nur die Schnittstellen von Funktionen, die von der App verwendet werden, erfordern Implementierung, wobei mindestens <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> und <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> unterstützt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="b8dfd-187">Serverstart</span><span class="sxs-lookup"><span data-stu-id="b8dfd-187">Server startup</span></span>

<span data-ttu-id="b8dfd-188">Der Server wird gestartet, wenn die integrierte Entwicklungsumgebung (IDE) oder der Editor die App startet:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="b8dfd-189">[Visual Studio:](https://visualstudio.microsoft.com) Startprofile können verwendet werden, um die App und den Server mit [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) oder mit der Konsole zu starten.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="b8dfd-190">[Visual Studio Code](https://code.visualstudio.com/): Die App und der Server werden durch [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode) gestartet, das den CoreCLR-Debugger aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="b8dfd-191">[Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/): Die App und der Server werden durch den [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/) gestartet.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="b8dfd-192">Wird die App über eine Eingabeaufforderung im Ordner des Projekts gestartet, startet [dotnet run](/dotnet/core/tools/dotnet-run) die App und den Server (nur Kestrel und HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="b8dfd-193">Die Konfiguration wird durch die Option `-c|--configuration` angegeben, die entweder auf `Debug` (Standardwert) oder `Release` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="b8dfd-194">Eine *launchSettings.json*-Datei bietet Konfiguration während des App-Starts mit `dotnet run` oder mit einem in Tools integrierten Debugger, z. B. Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="b8dfd-195">Sind in der Datei *launchSettings.json* Startprofile enthalten, verwenden Sie die Option `--launch-profile {PROFILE NAME}` mit dem Befehl `dotnet run`, oder wählen Sie das Profil in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="b8dfd-196">Weitere Informationen hierzu finden Sie unter [dotnet run](/dotnet/core/tools/dotnet-run) und unter [Verpacken einer Verteilung von .NET Core](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="b8dfd-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="b8dfd-197">HTTP/2-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="b8dfd-197">HTTP/2 support</span></span>

<span data-ttu-id="b8dfd-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden Bereitstellungsszenarien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="b8dfd-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="b8dfd-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="b8dfd-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="b8dfd-200">Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="b8dfd-200">Operating system</span></span>
    * <span data-ttu-id="b8dfd-201">Windows Server 2016/Windows 10 oder höher&dagger;</span><span class="sxs-lookup"><span data-stu-id="b8dfd-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="b8dfd-202">Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)</span><span class="sxs-lookup"><span data-stu-id="b8dfd-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="b8dfd-203">HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="b8dfd-204">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="b8dfd-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b8dfd-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="b8dfd-206">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="b8dfd-207">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="b8dfd-208">IIS (In-Process)</span><span class="sxs-lookup"><span data-stu-id="b8dfd-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b8dfd-209">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b8dfd-210">Zielframework: .NET Core 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="b8dfd-211">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="b8dfd-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b8dfd-212">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b8dfd-213">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="b8dfd-214">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="b8dfd-215">&dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="b8dfd-216">Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="b8dfd-217">Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="b8dfd-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="b8dfd-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="b8dfd-219">Windows Server 2016/Windows 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="b8dfd-220">Zielframework: Gilt nicht für HTTP.sys-Bereitstellungen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="b8dfd-221">IIS (Out-of-Process)</span><span class="sxs-lookup"><span data-stu-id="b8dfd-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="b8dfd-222">Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher</span><span class="sxs-lookup"><span data-stu-id="b8dfd-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="b8dfd-223">Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit Kestrel verwendet HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="b8dfd-224">Zielframework: Gilt nicht für Out-of-Process-Bereitstellungen von IIS.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="b8dfd-225">Eine HTTP/2-Verbindung muss [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) und TLS 1.2 oder höher verwenden.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="b8dfd-226">Weitere Informationen finden Sie in den Themen, die Ihre Serverbereitstellungsszenarien betreffen.</span><span class="sxs-lookup"><span data-stu-id="b8dfd-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8dfd-227">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b8dfd-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
