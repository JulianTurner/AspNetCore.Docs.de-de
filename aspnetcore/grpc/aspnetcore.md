---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von gRPC-Diensten mit ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
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
uid: grpc/aspnetcore
ms.openlocfilehash: 44a6f1d2a25314460fa4bce469f697a2fa4c0825
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252850"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="8c5dd-103">gRPC-Dienste mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c5dd-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="8c5dd-104">In diesem Dokument wird gezeigt, wie Sie mit gRPC-Diensten unter Verwendung von ASP.NET Core die ersten Schritte unternehmen können.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a><span data-ttu-id="8c5dd-105">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="8c5dd-105">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c5dd-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c5dd-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8c5dd-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8c5dd-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8c5dd-108">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8c5dd-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="8c5dd-109">Erste Schritte mit dem gRPC-Dienst in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8c5dd-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="8c5dd-110">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8c5dd-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8c5dd-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8c5dd-112">Ausführliche Informationen zum Erstellen eines gRPC-Projekts finden Sie unter [Erste Schritte mit gRPC-Diensten](xref:tutorials/grpc/grpc-start).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="8c5dd-113">Visual Studio Code / Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8c5dd-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="8c5dd-114">Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="8c5dd-115">Hinzufügen von gRPC-Diensten zu einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="8c5dd-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="8c5dd-116">gRPC erfordert das Paket [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="8c5dd-117">Konfigurieren von gRPC</span><span class="sxs-lookup"><span data-stu-id="8c5dd-117">Configure gRPC</span></span>

<span data-ttu-id="8c5dd-118">In *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="8c5dd-119">gRPC wird mit der Methode `AddGrpc` aktiviert.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="8c5dd-120">Jeder gRPC-Dienst wird der Routingpipeline durch die `MapGrpcService`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="8c5dd-121">ASP.NET Core-Middleware und -Features teilen sich die Routingpipeline, daher kann eine App so konfiguriert werden, dass sie zusätzliche Anforderungshandler bedient.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-121">ASP.NET Core middleware and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="8c5dd-122">Die zusätzlichen Anforderungshandler wie MVC-Controller arbeiten parallel zu den konfigurierten gRPC-Diensten.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

::: moniker range=">= aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="8c5dd-123">Konfigurieren von Kestrel</span><span class="sxs-lookup"><span data-stu-id="8c5dd-123">Configure Kestrel</span></span>

<span data-ttu-id="8c5dd-124">Kestrel gRPC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8c5dd-125">Erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-125">Require HTTP/2.</span></span>
* <span data-ttu-id="8c5dd-126">Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="8c5dd-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8c5dd-127">HTTP/2</span></span>

<span data-ttu-id="8c5dd-128">gRPC erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8c5dd-129">gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8c5dd-130">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel/http2) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel/http2) on most modern operating systems.</span></span> <span data-ttu-id="8c5dd-131">Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="8c5dd-132">TLS</span><span class="sxs-lookup"><span data-stu-id="8c5dd-132">TLS</span></span>

<span data-ttu-id="8c5dd-133">Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8c5dd-134">In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8c5dd-135">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-135">No configuration is required.</span></span> <span data-ttu-id="8c5dd-136">Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8c5dd-137">In der Produktion muss TLS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8c5dd-138">Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8c5dd-139">Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="8c5dd-140">Protokollaushandlung</span><span class="sxs-lookup"><span data-stu-id="8c5dd-140">Protocol negotiation</span></span>

<span data-ttu-id="8c5dd-141">TLS wird für mehr als nur die Sicherung der Kommunikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8c5dd-142">Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8c5dd-143">Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8c5dd-144">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8c5dd-145">Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8c5dd-146">Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8c5dd-147">Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel/endpoints).</span></span>

> [!NOTE]
> <span data-ttu-id="8c5dd-148">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8c5dd-149">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8c5dd-150">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="configure-kestrel"></a><span data-ttu-id="8c5dd-151">Konfigurieren von Kestrel</span><span class="sxs-lookup"><span data-stu-id="8c5dd-151">Configure Kestrel</span></span>

<span data-ttu-id="8c5dd-152">Kestrel gRPC-Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-152">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="8c5dd-153">Erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-153">Require HTTP/2.</span></span>
* <span data-ttu-id="8c5dd-154">Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-154">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="8c5dd-155">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="8c5dd-155">HTTP/2</span></span>

<span data-ttu-id="8c5dd-156">gRPC erfordert HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-156">gRPC requires HTTP/2.</span></span> <span data-ttu-id="8c5dd-157">gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-157">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) is `HTTP/2`.</span></span>

<span data-ttu-id="8c5dd-158">Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-158">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="8c5dd-159">Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-159">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="8c5dd-160">TLS</span><span class="sxs-lookup"><span data-stu-id="8c5dd-160">TLS</span></span>

<span data-ttu-id="8c5dd-161">Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-161">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="8c5dd-162">In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-162">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="8c5dd-163">Es ist keine Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-163">No configuration is required.</span></span> <span data-ttu-id="8c5dd-164">Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-164">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="8c5dd-165">In der Produktion muss TLS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-165">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="8c5dd-166">Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-166">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="8c5dd-167">Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-167">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="8c5dd-168">Protokollaushandlung</span><span class="sxs-lookup"><span data-stu-id="8c5dd-168">Protocol negotiation</span></span>

<span data-ttu-id="8c5dd-169">TLS wird für mehr als nur die Sicherung der Kommunikation verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-169">TLS is used for more than securing communication.</span></span> <span data-ttu-id="8c5dd-170">Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-170">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="8c5dd-171">Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-171">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="8c5dd-172">Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-172">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="8c5dd-173">Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-173">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there's no negotiation.</span></span> <span data-ttu-id="8c5dd-174">Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-174">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="8c5dd-175">Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-175">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="8c5dd-176">macOS unterstützt ASP.NET Core gRPC mit TLS nicht.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-176">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="8c5dd-177">Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-177">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="8c5dd-178">Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-178">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="8c5dd-179">Integration mit ASP.NET Core-APIs</span><span class="sxs-lookup"><span data-stu-id="8c5dd-179">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="8c5dd-180">gRPC-Dienste haben vollen Zugriff auf die ASP.NET Core-Features wie [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) und [ Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="8c5dd-180">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8c5dd-181">So kann die Dienstimplementierung z. B. einen Protokollierungsdienst aus dem Container der Abhängigkeitsinjektion über den Konstruktor auflösen:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-181">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="8c5dd-182">Standardmäßig kann die gRPC-Dienstimplementierung andere Abhängigkeitsinjektionsdienste mit beliebiger Lebensdauer (Singleton, Bereichsbezogen oder Vorübergehend) auflösen.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-182">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="8c5dd-183">Auflösen von HttpContext in gRPC-Methoden</span><span class="sxs-lookup"><span data-stu-id="8c5dd-183">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="8c5dd-184">Die gRPC-API bietet Zugriff auf einige HTTP/2-Nachrichtendaten, z. B. Methode, Host, Header und Nachspanne.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-184">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="8c5dd-185">Der Zugriff erfolgt über das Argument `ServerCallContext`, das an jede gRPC-Methode übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-185">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="8c5dd-186">`ServerCallContext` bietet nicht in allen ASP.NET-APIs vollen Zugriff auf `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8c5dd-186">`ServerCallContext` doesn't provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="8c5dd-187">Die `GetHttpContext`-Erweiterungsmethode bietet vollen Zugriff auf `HttpContext`, das die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:</span><span class="sxs-lookup"><span data-stu-id="8c5dd-187">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a><span data-ttu-id="8c5dd-188">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8c5dd-188">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
