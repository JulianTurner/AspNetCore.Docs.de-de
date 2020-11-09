---
title: Verwenden von gRPC in Browser-Apps
author: jamesnk
description: Erfahren Sie, wie Sie gRPC-Dienste für ASP.NET Core so konfigurieren, dass sie mit gRPC-Web von Browser-Apps aus aufgerufen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/browser
ms.openlocfilehash: 6456707620ae1c1f4d23f3562c78d1bf05d4844f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058908"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="04852-103">Verwenden von gRPC in Browser-Apps</span><span class="sxs-lookup"><span data-stu-id="04852-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="04852-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="04852-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="04852-105">Erfahren Sie, wie Sie einen vorhandenen gRPC-Dienst für ASP.NET Core so konfigurieren, dass er mit dem [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md)-Protokoll von Browser-Apps aus aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="04852-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="04852-106">gRPC-Web erlaubt JavaScript- und Blazor-Apps im Browser, gRPC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="04852-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="04852-107">Es ist nicht möglich, einen HTTP/2-gRPC-Dienst von einer browserbasierten App aus aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="04852-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="04852-108">gRPC-Dienste, die in ASP.NET Core gehostet werden, können so konfiguriert werden, dass sie neben HTTP/2 gRPC auch gRPC-Web unterstützen.</span><span class="sxs-lookup"><span data-stu-id="04852-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="04852-109">Anweisungen zum Hinzufügen eines gRPC-Diensts zu einer vorhandenen ASP.NET Core-App finden Sie unter [Hinzufügen von gRPC-Diensten zu einer ASP.NET Core-App](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="04852-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="04852-110">Anweisungen zum Erstellen eines gRPC-Projekts finden Sie unter <xref:tutorials/grpc/grpc-start>.</span><span class="sxs-lookup"><span data-stu-id="04852-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="04852-111">gRPC-Web in ASP.NET Core im Vergleich zu Envoy</span><span class="sxs-lookup"><span data-stu-id="04852-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="04852-112">Es gibt zwei Möglichkeiten zum Hinzufügen von gRPC-Web zu einer ASP.NET Core-App:</span><span class="sxs-lookup"><span data-stu-id="04852-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="04852-113">Unterstützung von gRPC-Web parallel zu gRPC HTTP/2 in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="04852-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="04852-114">Diese Option verwendet die mit dem `Grpc.AspNetCore.Web`-Paket bereitgestellte Middleware.</span><span class="sxs-lookup"><span data-stu-id="04852-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="04852-115">Verwenden Sie die gRPC-Webunterstützung des [Envoy Proxys](https://www.envoyproxy.io/), um gRPC-Web in gRPC HTTP/2 zu übersetzen.</span><span class="sxs-lookup"><span data-stu-id="04852-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="04852-116">Der übersetzte Aufruf wird dann an die ASP.NET Core-App weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="04852-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="04852-117">Beide Ansätze haben Vor- und Nachteile.</span><span class="sxs-lookup"><span data-stu-id="04852-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="04852-118">Wenn die Umgebung einer App bereits Envoy als Proxy verwendet, kann es sinnvoll sein, Envoy auch zur Bereitstellung von gRPC-Webunterstützung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="04852-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="04852-119">Als einfache Lösung für gRPC-Web, die nur ASP.NET Core erfordert, ist `Grpc.AspNetCore.Web` eine gute Wahl.</span><span class="sxs-lookup"><span data-stu-id="04852-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="04852-120">Konfigurieren von gRPC-Web in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="04852-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="04852-121">gRPC-Dienste, die in ASP.NET Core gehostet werden, können so konfiguriert werden, dass sie neben HTTP/2 gRPC auch gRPC-Web unterstützen.</span><span class="sxs-lookup"><span data-stu-id="04852-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="04852-122">Für gRPC-Web sind keine Änderungen der Dienste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="04852-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="04852-123">Die einzige Änderung ist die Startkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="04852-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="04852-124">So aktivieren Sie gRPC-Web mit einem ASP.NET Core gRPC-Dienst</span><span class="sxs-lookup"><span data-stu-id="04852-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="04852-125">Fügen Sie einen Verweis auf das Paket [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) hinzu.</span><span class="sxs-lookup"><span data-stu-id="04852-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="04852-126">Konfigurieren Sie die App zur Verwendung von gRPC-Web, indem Sie `UseGrpcWeb` und `EnableGrpcWeb` zu *Startup.cs* hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="04852-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs* :</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="04852-127">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="04852-127">The preceding code:</span></span>

* <span data-ttu-id="04852-128">Fügt die gRPC-Web-Middleware, `UseGrpcWeb`, nach dem Routing und vor den Endpunkten hinzu.</span><span class="sxs-lookup"><span data-stu-id="04852-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="04852-129">Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die gRPC-Web mit `EnableGrpcWeb` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="04852-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="04852-130">Alternativ kann die gRPC-Web-Middleware so konfiguriert werden, dass alle Dienste standardmäßig gRPC-Web unterstützen und `EnableGrpcWeb` nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="04852-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="04852-131">Geben Sie `new GrpcWebOptions { DefaultEnabled = true }` an, wenn die Middleware hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="04852-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="04852-132">Es gibt ein bekanntes Problem, das dazu führen kann, dass gRPC-Web fehlschlägt, wenn es [von Htpp.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="04852-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="04852-133">Eine Problemumgehung, um gRPC-Web auf Http.sys betreiben zu können, finden Sie [hier](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span><span class="sxs-lookup"><span data-stu-id="04852-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="04852-134">gRPC-Web und CORS</span><span class="sxs-lookup"><span data-stu-id="04852-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="04852-135">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="04852-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="04852-136">Diese Einschränkung gilt für das Durchführen von gRPC-Webaufrufen mit Browser-Apps.</span><span class="sxs-lookup"><span data-stu-id="04852-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="04852-137">So wird z. B. eine Browser-App, die von `https://www.contoso.com` versorgt wird, daran gehindert, gRPC-Web-Dienste aufzurufen, die auf `https://services.contoso.com` gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="04852-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="04852-138">Die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross Origin Resource Sharing, CORS) kann verwendet werden, um diese Einschränkung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="04852-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="04852-139">Damit eine Browser-App die Möglichkeit hat, gRPC-Web-Aufrufe ursprungsübergreifend zu tätigen, richten Sie [CORS in ASP.NET Core](xref:security/cors) ein.</span><span class="sxs-lookup"><span data-stu-id="04852-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="04852-140">Verwenden Sie die integrierte CORS-Unterstützung, und machen Sie gRPC-spezifische Header mit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> verfügbar.</span><span class="sxs-lookup"><span data-stu-id="04852-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="04852-141">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="04852-141">The preceding code:</span></span>

* <span data-ttu-id="04852-142">Ruft `AddCors` zum Hinzufügen von CORS-Diensten auf und konfiguriert eine CORS-Richtlinie, die gRPC-spezifische Header verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="04852-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="04852-143">Ruft `UseCors` zum Hinzufügen der CORS-Middleware nach dem Routing und vor den Endpunkten auf.</span><span class="sxs-lookup"><span data-stu-id="04852-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="04852-144">Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die CORS mit `RequiresCors` unterstützt.</span><span class="sxs-lookup"><span data-stu-id="04852-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="04852-145">gRPC-Web und Streaming</span><span class="sxs-lookup"><span data-stu-id="04852-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="04852-146">Herkömmliches gRPC über HTTP/2 unterstützt das Streamen in alle Richtungen.</span><span class="sxs-lookup"><span data-stu-id="04852-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="04852-147">gRPC-Web bietet eingeschränkte Unterstützung für Streaming:</span><span class="sxs-lookup"><span data-stu-id="04852-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="04852-148">gRPC-Web-Browserclients unterstützen das Aufrufen von Clientstreaming- und bidirektionalen Streamingmethoden nicht.</span><span class="sxs-lookup"><span data-stu-id="04852-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="04852-149">ASP.NET Core-gRPC-Dienste, die in Azure App Service und IIS gehostet werden, unterstützen das bidirektionale Streaming nicht.</span><span class="sxs-lookup"><span data-stu-id="04852-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="04852-150">Bei der Verwendung von gRPC-Web wird nur der Einsatz von unären Methoden und Serverstreamingmethoden empfohlen.</span><span class="sxs-lookup"><span data-stu-id="04852-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="04852-151">Aufrufen von gRPC-Web über den Browser</span><span class="sxs-lookup"><span data-stu-id="04852-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="04852-152">Browser-Apps können gRPC-Web verwenden, um gRPC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="04852-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="04852-153">Es gibt einige Anforderungen und Einschränkungen beim Aufrufen von gRPC-Diensten mit gRPC-Web aus dem Browser heraus:</span><span class="sxs-lookup"><span data-stu-id="04852-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="04852-154">Der Server muss für die Unterstützung von gRPC-Web konfiguriert worden sein.</span><span class="sxs-lookup"><span data-stu-id="04852-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="04852-155">Clientstreaming und bidirektionale Streamingaufrufe werden nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="04852-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="04852-156">Das Serverstreaming wird unterstützt.</span><span class="sxs-lookup"><span data-stu-id="04852-156">Server streaming is supported.</span></span>
* <span data-ttu-id="04852-157">Das Aufrufen von gRPC-Diensten in einer anderen Domäne erfordert die Konfiguration von [CORS](xref:security/cors) auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="04852-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="04852-158">JavaScript gRPC-Web-Client</span><span class="sxs-lookup"><span data-stu-id="04852-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="04852-159">Es gibt einen JavaScript gRPC-Web-Client.</span><span class="sxs-lookup"><span data-stu-id="04852-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="04852-160">Anweisungen zur Verwendung von gRPC-Web aus JavaScript heraus finden Sie unter [Schreiben von JavaScript-Clientcode mit gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="04852-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="04852-161">Konfigurieren von gRPC-Web mit dem .NET gRPC-Client</span><span class="sxs-lookup"><span data-stu-id="04852-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="04852-162">Der .NET gRPC-Client kann für gRPC-Web-Aufrufe konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="04852-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="04852-163">Dies ist nützlich für [Blazor WebAssembly](xref:blazor/index#blazor-webassembly)-Apps, die im Browser gehostet werden und dieselben HTTP-Einschränkungen des JavaScript-Codes aufweisen.</span><span class="sxs-lookup"><span data-stu-id="04852-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="04852-164">Das Aufrufen von gRPC-Web mit einem .NET-Client ist [dasselbe wie das Aufrufen von HTTP/2 gRPC](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="04852-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="04852-165">Die einzige Änderung besteht darin, wie der Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="04852-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="04852-166">So verwenden Sie gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="04852-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="04852-167">Fügen Sie einen Verweis auf das Paket [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) hinzu.</span><span class="sxs-lookup"><span data-stu-id="04852-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="04852-168">Stellen Sie sicher, dass der Verweis auf das Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.29.0 oder höher ist.</span><span class="sxs-lookup"><span data-stu-id="04852-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="04852-169">Konfigurieren Sie den Kanal für die Verwendung von `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="04852-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="04852-170">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="04852-170">The preceding code:</span></span>

* <span data-ttu-id="04852-171">Konfiguriert einen Kanal zur Verwendung von gRPC-Web.</span><span class="sxs-lookup"><span data-stu-id="04852-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="04852-172">Erstellt einen Client und tätigt einen Aufruf über den Kanal.</span><span class="sxs-lookup"><span data-stu-id="04852-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="04852-173">`GrpcWebHandler` hat die folgenden Konfigurationsoptionen:</span><span class="sxs-lookup"><span data-stu-id="04852-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="04852-174">**InnerHandler** : Der zugrundeliegende <xref:System.Net.Http.HttpMessageHandler>, der die gRPC HTTP-Anforderung stellt, z. B. `HttpClientHandler`.</span><span class="sxs-lookup"><span data-stu-id="04852-174">**InnerHandler** : The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="04852-175">**GrpcWebMode** : Ein Enumerationstyp, der angibt, ob die gRPC-HTTP-Anforderung `Content-Type` `application/grpc-web` oder `application/grpc-web-text` ist.</span><span class="sxs-lookup"><span data-stu-id="04852-175">**GrpcWebMode** : An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="04852-176">`GrpcWebMode.GrpcWeb` konfiguriert Inhalte, die ohne Codierung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="04852-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="04852-177">Standardwert.</span><span class="sxs-lookup"><span data-stu-id="04852-177">Default value.</span></span>
    * <span data-ttu-id="04852-178">`GrpcWebMode.GrpcWebText` konfiguriert den Inhalt so, dass er Base64-codiert ist.</span><span class="sxs-lookup"><span data-stu-id="04852-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="04852-179">Erforderlich für Serverstreamingaufrufe in Browsern.</span><span class="sxs-lookup"><span data-stu-id="04852-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="04852-180">**HttpVersion** : Die `Version` des HTTP-Protokolls, die verwendet wird, um [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) für die zugrunde liegende gRPC-HTTP-Anforderung festzulegen.</span><span class="sxs-lookup"><span data-stu-id="04852-180">**HttpVersion** : HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="04852-181">gRPC-Web erfordert keine bestimmte Version und setzt die Standardeinstellung nicht außer Kraft, sofern nicht anders angegeben.</span><span class="sxs-lookup"><span data-stu-id="04852-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="04852-182">Generierte gRPC-Clients verfügen über synchrone und asynchrone Methoden für den Aufruf unärer Methoden.</span><span class="sxs-lookup"><span data-stu-id="04852-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="04852-183">Beispiel: `SayHello` ist synchron und `SayHelloAsync` ist asynchron.</span><span class="sxs-lookup"><span data-stu-id="04852-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="04852-184">Der Aufruf einer synchronen Methode in einer Blazor WebAssembly-App führt dazu, dass die App nicht mehr reagiert.</span><span class="sxs-lookup"><span data-stu-id="04852-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="04852-185">Asynchrone Methoden müssen in Blazor WebAssembly immer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="04852-185">Async methods must always be used in Blazor WebAssembly.</span></span>

### <a name="use-grpc-client-factory-with-grpc-web"></a><span data-ttu-id="04852-186">Verwenden der gRPC-Clientfactory mit gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="04852-186">Use gRPC client factory with gRPC-Web</span></span>

<span data-ttu-id="04852-187">Ein mit gRPC-Web kompatibler .NET-Client kann mithilfe der gRPC-Integration mit [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory) erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="04852-187">A gRPC-Web compatible .NET client can be created using gRPC's integration with [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory).</span></span>

<span data-ttu-id="04852-188">So verwenden Sie gRPC-Web mit der Clientfactory:</span><span class="sxs-lookup"><span data-stu-id="04852-188">To use gRPC-Web with client factory:</span></span>

* <span data-ttu-id="04852-189">Fügen Sie der Projektdatei Paketverweise für folgende Pakete hinzu:</span><span class="sxs-lookup"><span data-stu-id="04852-189">Add package references to the project file for the following packages:</span></span>
  * [<span data-ttu-id="04852-190">Grpc.Net.Client.Web</span><span class="sxs-lookup"><span data-stu-id="04852-190">Grpc.Net.Client.Web</span></span>](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [<span data-ttu-id="04852-191">Grpc.Net.ClientFactory</span><span class="sxs-lookup"><span data-stu-id="04852-191">Grpc.Net.ClientFactory</span></span>](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* <span data-ttu-id="04852-192">Registrieren Sie einen gRPC-Client mit Dependency Injection (DI), indem Sie die generische Erweiterungsmethode `AddGrpcClient` verwenden.</span><span class="sxs-lookup"><span data-stu-id="04852-192">Register a gRPC client with dependency injection (DI) using the generic `AddGrpcClient` extension method.</span></span> <span data-ttu-id="04852-193">In einer Blazor WebAssembly-App werden Dienste mit DI in `Program.cs` registriert.</span><span class="sxs-lookup"><span data-stu-id="04852-193">In a Blazor WebAssembly app, services are registered with DI in `Program.cs`.</span></span>
* <span data-ttu-id="04852-194">Konfigurieren Sie `GrpcWebHandler` mithilfe der Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="04852-194">Configure `GrpcWebHandler` using the <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A> extension method.</span></span>

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

<span data-ttu-id="04852-195">Weitere Informationen finden Sie unter <xref:grpc/clientfactory>.</span><span class="sxs-lookup"><span data-stu-id="04852-195">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="04852-196">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="04852-196">Additional resources</span></span>

* [<span data-ttu-id="04852-197">gRPC für GitHub-Projekt für Webclients</span><span class="sxs-lookup"><span data-stu-id="04852-197">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
