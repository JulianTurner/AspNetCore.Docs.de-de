---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von gRPC-Diensten mit ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530202"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="4adb2-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4adb2-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="4adb2-104">In diesem Dokument wird gezeigt, wie Sie mit gRPC-Diensten unter Verwendung von ASP.NET Core die ersten Schritte unternehmen können.</span><span class="sxs-lookup"><span data-stu-id="4adb2-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="4adb2-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4adb2-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4adb2-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4adb2-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="4adb2-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4adb2-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="4adb2-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4adb2-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="4adb2-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4adb2-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="4adb2-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="4adb2-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4adb2-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4adb2-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4adb2-112">Ausführliche Informationen zum Erstellen eines gRPC-Projekts finden Sie unter [Erste Schritte mit gRPC-Diensten](xref:tutorials/grpc/grpc-start).</span><span class="sxs-lookup"><span data-stu-id="4adb2-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="4adb2-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="4adb2-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="4adb2-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="4adb2-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="4adb2-115">Hinzufügen von gRPC-Diensten zu einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="4adb2-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="4adb2-116">gRPC erfordert das Paket [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="4adb2-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="4adb2-117">Konfigurieren von gRPC</span><span class="sxs-lookup"><span data-stu-id="4adb2-117">Configure gRPC</span></span>

<span data-ttu-id="4adb2-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="4adb2-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="4adb2-119">gRPC wird mit der Methode `AddGrpc` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="4adb2-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="4adb2-120">Jeder gRPC-Dienst wird der Routingpipeline durch die `MapGrpcService`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4adb2-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="4adb2-121">ASP.NET Core-Middleware und -Features teilen sich die Routingpipeline, daher kann eine App so konfiguriert werden, dass sie zusätzliche Anforderungshandler bedient.</span><span class="sxs-lookup"><span data-stu-id="4adb2-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="4adb2-122">Die zusätzlichen Anforderungshandler wie MVC-Controller arbeiten parallel zu den konfigurierten gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="4adb2-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="server-options"></a><span data-ttu-id="4adb2-123">Serveroptionen</span><span class="sxs-lookup"><span data-stu-id="4adb2-123">Server options</span></span>

<span data-ttu-id="4adb2-124">gRPC-Dienste können von allen integrierten ASP.NET Core-Servern gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-124">gRPC services can be hosted by all built-in ASP.NET Core servers.</span></span>

> [!div class="checklist"]
>
> * <span data-ttu-id="4adb2-125">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4adb2-125">Kestrel</span></span>
> * <span data-ttu-id="4adb2-126">TestServer</span><span class="sxs-lookup"><span data-stu-id="4adb2-126">TestServer</span></span>
> * <span data-ttu-id="4adb2-127">IIS&dagger;</span><span class="sxs-lookup"><span data-stu-id="4adb2-127">IIS&dagger;</span></span>
> * <span data-ttu-id="4adb2-128">HTTP.sys&Dagger;</span><span class="sxs-lookup"><span data-stu-id="4adb2-128">HTTP.sys&Dagger;</span></span>

<span data-ttu-id="4adb2-129">&dagger;IIS erfordert .NET 5 und Windows 10 Build 20241 oder höher.</span><span class="sxs-lookup"><span data-stu-id="4adb2-129">&dagger;IIS requires .NET 5 and Windows 10 Build 20241 or later.</span></span>

<span data-ttu-id="4adb2-130">&Dagger;HTTP.sys erfordert .NET 5 und Windows 10 Build 19529 oder höher.</span><span class="sxs-lookup"><span data-stu-id="4adb2-130">&Dagger;HTTP.sys requires .NET 5 and Windows 10 Build 19529 or later.</span></span>

<span data-ttu-id="4adb2-131">Weitere Informationen zum Auswählen des richtigen Servers für eine ASP.NET Core-App finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="4adb2-131">For more information about choosing the right server for an ASP.NET Core app, see <xref:fundamentals/servers/index>.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="4adb2-132">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4adb2-132">Kestrel</span></span>

<span data-ttu-id="4adb2-133">[Kestrel](xref:fundamentals/servers/kestrel) ist ein plattformübergreifender Webserver für ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4adb2-133">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="4adb2-134">Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige der erweiterten Features in HTTP.sys wie Portfreigabe.</span><span class="sxs-lookup"><span data-stu-id="4adb2-134">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="4adb2-135">Kestrel gRPC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4adb2-135">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="4adb2-136">Erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4adb2-136">Require HTTP/2.</span></span>
* <span data-ttu-id="4adb2-137">Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-137">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="4adb2-138">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4adb2-138">HTTP/2</span></span>

<span data-ttu-id="4adb2-139">gRPC erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4adb2-139">gRPC requires HTTP/2.</span></span> <span data-ttu-id="4adb2-140">gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="4adb2-140">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="4adb2-141">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel/http2) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="4adb2-141">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="4adb2-142">Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4adb2-142">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="4adb2-143">TLS</span><span class="sxs-lookup"><span data-stu-id="4adb2-143">TLS</span></span>

<span data-ttu-id="4adb2-144">Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-144">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="4adb2-145">In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4adb2-145">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="4adb2-146">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4adb2-146">No configuration is required.</span></span> <span data-ttu-id="4adb2-147">Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-147">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="4adb2-148">In der Produktion muss TLS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="4adb2-148">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="4adb2-149">Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="4adb2-149">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="4adb2-150">Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="4adb2-150">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="4adb2-151">Protokollaushandlung</span><span class="sxs-lookup"><span data-stu-id="4adb2-151">Protocol negotiation</span></span>

<span data-ttu-id="4adb2-152">TLS wird für mehr als nur die Sicherung der Kommunikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-152">TLS is used for more than securing communication.</span></span> <span data-ttu-id="4adb2-153">Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4adb2-153">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="4adb2-154">Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-154">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="4adb2-155">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-155">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="4adb2-156">Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="4adb2-156">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="4adb2-157">Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="4adb2-157">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="4adb2-158">Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="4adb2-158">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="4adb2-159">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="4adb2-159">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="4adb2-160">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4adb2-160">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="4adb2-161">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="4adb2-161">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="iis"></a><span data-ttu-id="4adb2-162">IIS</span><span class="sxs-lookup"><span data-stu-id="4adb2-162">IIS</span></span>

<span data-ttu-id="4adb2-163">[IIS (Internet Information Services, Internetinformationsdienste)](xref:host-and-deploy/iis/index) stellen einen flexiblen, sicheren und verwaltbaren Webserver für das Hosten von Web-Apps (einschließlich ASP.NET Core) dar.</span><span class="sxs-lookup"><span data-stu-id="4adb2-163">[Internet Information Services (IIS)](xref:host-and-deploy/iis/index) is a flexible, secure and manageable Web Server for hosting web apps, including ASP.NET Core.</span></span> <span data-ttu-id="4adb2-164">.NET 5 und Windows 10 Build 20241 oder höher sind erforderlich, um gRPC-Dienste mit IIS zu hosten.</span><span class="sxs-lookup"><span data-stu-id="4adb2-164">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with IIS.</span></span>

<span data-ttu-id="4adb2-165">IIS muss für die Verwendung von TLS und HTTP/2 konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="4adb2-165">IIS must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="4adb2-166">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/protocols>.</span><span class="sxs-lookup"><span data-stu-id="4adb2-166">For more information, see <xref:host-and-deploy/iis/protocols>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="4adb2-167">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="4adb2-167">HTTP.sys</span></span>

<span data-ttu-id="4adb2-168">[HTTP.sys](xref:fundamentals/servers/httpsys) ist ein Webserver für ASP.NET Core, der nur unter Windows ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="4adb2-168">[HTTP.sys](xref:fundamentals/servers/httpsys) is a web server for ASP.NET Core that only runs on Windows.</span></span> <span data-ttu-id="4adb2-169">.NET 5 und Windows 10 Build 20241 oder höher sind erforderlich, um gRPC-Dienste mit HTTP.sys zu hosten.</span><span class="sxs-lookup"><span data-stu-id="4adb2-169">.NET 5 and Windows 10 Build 20241 or later are required to host gRPC services with HTTP.sys.</span></span>

<span data-ttu-id="4adb2-170">HTTP.sys muss für die Verwendung von TLS und HTTP/2 konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="4adb2-170">HTTP.sys must be configured to use TLS and HTTP/2.</span></span> <span data-ttu-id="4adb2-171">Weitere Informationen finden Sie unter [HTTP/2-Unterstützung](xref:fundamentals/servers/httpsys#http2-support).</span><span class="sxs-lookup"><span data-stu-id="4adb2-171">For more information, see  [HTTP.sys web server HTTP/2 support](xref:fundamentals/servers/httpsys#http2-support).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a><span data-ttu-id="4adb2-172">Kestrel</span><span class="sxs-lookup"><span data-stu-id="4adb2-172">Kestrel</span></span>

<span data-ttu-id="4adb2-173">[Kestrel](xref:fundamentals/servers/kestrel) ist ein plattformübergreifender Webserver für ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="4adb2-173">[Kestrel](xref:fundamentals/servers/kestrel) is a cross-platform web server for ASP.NET Core.</span></span> <span data-ttu-id="4adb2-174">Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige der erweiterten Features in HTTP.sys wie Portfreigabe.</span><span class="sxs-lookup"><span data-stu-id="4adb2-174">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys such as port sharing.</span></span>

<span data-ttu-id="4adb2-175">Kestrel gRPC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="4adb2-175">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="4adb2-176">Erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4adb2-176">Require HTTP/2.</span></span>
* <span data-ttu-id="4adb2-177">Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-177">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

### <a name="http2"></a><span data-ttu-id="4adb2-178">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="4adb2-178">HTTP/2</span></span>

<span data-ttu-id="4adb2-179">gRPC erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="4adb2-179">gRPC requires HTTP/2.</span></span> <span data-ttu-id="4adb2-180">gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="4adb2-180">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="4adb2-181">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="4adb2-181">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="4adb2-182">Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4adb2-182">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

### <a name="tls"></a><span data-ttu-id="4adb2-183">TLS</span><span class="sxs-lookup"><span data-stu-id="4adb2-183">TLS</span></span>

<span data-ttu-id="4adb2-184">Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-184">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="4adb2-185">In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="4adb2-185">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="4adb2-186">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4adb2-186">No configuration is required.</span></span> <span data-ttu-id="4adb2-187">Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-187">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="4adb2-188">In der Produktion muss TLS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="4adb2-188">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="4adb2-189">Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="4adb2-189">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="4adb2-190">Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="4adb2-190">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a><span data-ttu-id="4adb2-191">Protokollaushandlung</span><span class="sxs-lookup"><span data-stu-id="4adb2-191">Protocol negotiation</span></span>

<span data-ttu-id="4adb2-192">TLS wird für mehr als nur die Sicherung der Kommunikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-192">TLS is used for more than securing communication.</span></span> <span data-ttu-id="4adb2-193">Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4adb2-193">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="4adb2-194">Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="4adb2-194">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="4adb2-195">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4adb2-195">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="4adb2-196">Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="4adb2-196">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="4adb2-197">Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="4adb2-197">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="4adb2-198">Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="4adb2-198">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="4adb2-199">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="4adb2-199">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="4adb2-200">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4adb2-200">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="4adb2-201">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="4adb2-201">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="4adb2-202">Integration mit ASP.NET Core-APIs</span><span class="sxs-lookup"><span data-stu-id="4adb2-202">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="4adb2-203">gRPC-Dienste haben vollen Zugriff auf die ASP.NET Core-Features wie [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) und [ Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="4adb2-203">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="4adb2-204">So kann die Dienstimplementierung z. B. einen Protokollierungsdienst aus dem Container der Abhängigkeitsinjektion über den Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="4adb2-204">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="4adb2-205">Standardmäßig kann die gRPC-Dienstimplementierung andere Abhängigkeitsinjektionsdienste mit beliebiger Lebensdauer (Singleton, Bereichsbezogen oder Vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="4adb2-205">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="4adb2-206">Auflösen von HttpContext in gRPC-Methoden</span><span class="sxs-lookup"><span data-stu-id="4adb2-206">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="4adb2-207">Die gRPC-API bietet Zugriff auf einige HTTP/2-Nachrichtendaten, z. B. Methode, Host, Header und Nachspanne.</span><span class="sxs-lookup"><span data-stu-id="4adb2-207">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="4adb2-208">Der Zugriff erfolgt über das Argument `ServerCallContext`, das an jede gRPC-Methode übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="4adb2-208">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="4adb2-209">`ServerCallContext` bietet nicht in allen ASP.NET-APIs vollen Zugriff auf `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="4adb2-209">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="4adb2-210">Die `GetHttpContext`-Erweiterungsmethode bietet vollen Zugriff auf `HttpContext`, das die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="4adb2-210">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="4adb2-211">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4adb2-211">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
