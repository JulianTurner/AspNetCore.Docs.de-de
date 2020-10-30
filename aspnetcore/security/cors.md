---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060819"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="122e8-103">Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="122e8-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="122e8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="122e8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="122e8-105">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="122e8-106">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="122e8-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="122e8-107">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="122e8-107">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="122e8-108">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="122e8-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="122e8-109">Manchmal möchten Sie, dass andere Sites Ursprungs übergreifende Anforderungen an Ihre APP senden können.</span><span class="sxs-lookup"><span data-stu-id="122e8-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="122e8-110">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="122e8-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="122e8-111">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="122e8-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="122e8-112">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="122e8-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="122e8-113">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="122e8-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="122e8-114">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="122e8-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="122e8-115">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="122e8-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="122e8-116">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="122e8-117">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="122e8-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="122e8-118">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="122e8-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="122e8-119">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="122e8-119">Same origin</span></span>

<span data-ttu-id="122e8-120">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="122e8-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="122e8-121">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="122e8-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="122e8-122">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="122e8-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="122e8-123">`https://example.net`: Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="122e8-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="122e8-124">`https://www.example.com/foo.html`: Untergeordnete Domäne</span><span class="sxs-lookup"><span data-stu-id="122e8-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="122e8-125">`http://example.com/foo.html`: Anderes Schema</span><span class="sxs-lookup"><span data-stu-id="122e8-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="122e8-126">`https://example.com:9000/foo.html`: Anderer Port</span><span class="sxs-lookup"><span data-stu-id="122e8-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="122e8-127">Aktivieren von CORS</span><span class="sxs-lookup"><span data-stu-id="122e8-127">Enable CORS</span></span>

<span data-ttu-id="122e8-128">Es gibt drei Möglichkeiten, cors zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="122e8-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="122e8-129">In Middleware, die eine [benannte Richtlinie](#np) oder eine [Standard Richtlinie](#dp)verwendet.</span><span class="sxs-lookup"><span data-stu-id="122e8-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="122e8-130">Verwenden des [Endpunkt Routings](#ecors).</span><span class="sxs-lookup"><span data-stu-id="122e8-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="122e8-131">Mit dem [[enablecors]](#attr) -Attribut.</span><span class="sxs-lookup"><span data-stu-id="122e8-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="122e8-132">Die Verwendung des [[enablecors]](#attr) -Attributs mit einer benannten Richtlinie ermöglicht das beste Steuerelement in einschränkenden Endpunkten, die cors unterstützen.</span><span class="sxs-lookup"><span data-stu-id="122e8-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="122e8-133"><xref:Owin.CorsExtensions.UseCors%2A> muss vor aufgerufen werden, <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> Wenn verwendet wird `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="122e8-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="122e8-134">Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.</span><span class="sxs-lookup"><span data-stu-id="122e8-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="122e8-135">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="122e8-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="122e8-136">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="122e8-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="122e8-137">Mit dem folgenden Code wird eine cors-Richtlinie auf alle Endpunkte der APP mit den angegebenen Ursprüngen angewendet:</span><span class="sxs-lookup"><span data-stu-id="122e8-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="122e8-138">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122e8-138">The preceding code:</span></span>

* <span data-ttu-id="122e8-139">Legt den Richtlinien Namen auf fest `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="122e8-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="122e8-140">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="122e8-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="122e8-141">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf und gibt die  `_myAllowSpecificOrigins` cors-Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="122e8-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="122e8-142">`UseCors` Fügt die cors-Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="122e8-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="122e8-143">Der-Anruf `UseCors` muss nach, jedoch `UseRouting` vor eingefügt werden `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="122e8-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="122e8-144">Weitere Informationen finden Sie unter [Middleware Order](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="122e8-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="122e8-145">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="122e8-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="122e8-146">Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="122e8-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="122e8-147">[Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="122e8-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="122e8-148">Aktiviert die `_myAllowSpecificOrigins` cors-Richtlinie für alle Controller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="122e8-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="122e8-149">Weitere Informationen finden Sie unter [EndPoint Routing](#ecors) zum Anwenden einer cors-Richtlinie auf bestimmte Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="122e8-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="122e8-150">Wenn Sie die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware)verwenden, sollten Sie <xref:Owin.CorsExtensions.UseCors%2A> vor <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A></span><span class="sxs-lookup"><span data-stu-id="122e8-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="122e8-151">Beim Endpunkt Routing **muss** die cors-Middleware für die Ausführung zwischen den Aufrufen von und konfiguriert werden `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="122e8-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="122e8-152">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="122e8-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="122e8-153">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="122e8-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="122e8-154">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="122e8-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="122e8-155">Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="122e8-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="122e8-156">Hinweis: die angegebene URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten.</span><span class="sxs-lookup"><span data-stu-id="122e8-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="122e8-157">Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="122e8-158">Cors mit Standard Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="122e8-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="122e8-159">Der folgende markierte Code aktiviert die cors-Standard Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="122e8-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="122e8-160">Im vorangehenden Code wird die cors-Standard Richtlinie auf alle Controller Endpunkte angewendet.</span><span class="sxs-lookup"><span data-stu-id="122e8-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="122e8-161">Aktivieren von Cors mit Endpunktrouting</span><span class="sxs-lookup"><span data-stu-id="122e8-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="122e8-162">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="122e8-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="122e8-163">Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer).</span><span class="sxs-lookup"><span data-stu-id="122e8-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="122e8-164">Mit dem Endpunkt Routing kann cors pro Endpunkt mit dem <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Satz von Erweiterungs Methoden aktiviert werden:</span><span class="sxs-lookup"><span data-stu-id="122e8-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="122e8-165">Im obigen Code:</span><span class="sxs-lookup"><span data-stu-id="122e8-165">In the preceding code:</span></span>

* <span data-ttu-id="122e8-166">`app.UseCors` aktiviert die cors-Middleware.</span><span class="sxs-lookup"><span data-stu-id="122e8-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="122e8-167">Da eine Standard Richtlinie nicht konfiguriert wurde, werden `app.UseCors()` cors von alleine nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="122e8-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="122e8-168">Die `/echo` -und-Controller Endpunkte lassen Ursprungs übergreifende Anforderungen zu, die die angegebene Richtlinie verwenden.</span><span class="sxs-lookup"><span data-stu-id="122e8-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="122e8-169">Die `/echo2` -und :::no-loc(Razor)::: -Seiten Endpunkte lassen **keine** Ursprungs übergreifenden Anforderungen zu, da keine Standard Richtlinie angegeben wurde.</span><span class="sxs-lookup"><span data-stu-id="122e8-169">The `/echo2` and :::no-loc(Razor)::: Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="122e8-170">Das [[disablecors]](#dc) -Attribut deaktiviert **keine**  cors, die durch das Endpunkt Routing mit aktiviert wurden `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="122e8-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="122e8-171">Anweisungen zum Testen von Code, der dem vorangehenden ähnelt, finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="122e8-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="122e8-172">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="122e8-172">Enable CORS with attributes</span></span>

<span data-ttu-id="122e8-173">Das Aktivieren von cors mit dem [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut und das Anwenden einer benannten Richtlinie auf die Endpunkte, die cors erfordern, stellt das beste Steuerelement bereit.</span><span class="sxs-lookup"><span data-stu-id="122e8-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="122e8-174">Das [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="122e8-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="122e8-175">Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte:</span><span class="sxs-lookup"><span data-stu-id="122e8-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="122e8-176">`[EnableCors]` Gibt die Standard Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="122e8-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="122e8-177">`[EnableCors("{Policy String}")]` gibt eine benannte Richtlinie an.</span><span class="sxs-lookup"><span data-stu-id="122e8-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="122e8-178">Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="122e8-178">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="122e8-179">:::no-loc(Razor)::: S `PageModel`</span><span class="sxs-lookup"><span data-stu-id="122e8-179">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="122e8-180">Controller</span><span class="sxs-lookup"><span data-stu-id="122e8-180">Controller</span></span>
* <span data-ttu-id="122e8-181">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="122e8-181">Controller action method</span></span>

<span data-ttu-id="122e8-182">Mithilfe des-Attributs können unterschiedliche Richtlinien auf Controller, Seiten Modelle oder Aktionsmethoden angewendet werden `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="122e8-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="122e8-183">Wenn das `[EnableCors]` -Attribut auf einen Controller, ein Seiten Modell oder eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="122e8-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="122e8-184">**Es wird empfohlen, Richtlinien zu kombinieren. Verwenden Sie das** `[EnableCors]` **Attribut oder die Middleware, nicht beide in der gleichen app.**</span><span class="sxs-lookup"><span data-stu-id="122e8-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="122e8-185">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="122e8-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="122e8-186">Der folgende Code erstellt zwei cors-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="122e8-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="122e8-187">Für die beste Kontrolle über das Einschränken von cors-Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="122e8-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="122e8-188">Verwenden Sie `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="122e8-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="122e8-189">Definieren Sie keine Standard Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="122e8-189">Don't define a default policy.</span></span>
* <span data-ttu-id="122e8-190">Verwenden Sie das [Endpunkt Routing](#ecors)nicht.</span><span class="sxs-lookup"><span data-stu-id="122e8-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="122e8-191">Der Code im nächsten Abschnitt entspricht der vorangehenden Liste.</span><span class="sxs-lookup"><span data-stu-id="122e8-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="122e8-192">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="122e8-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="122e8-193">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="122e8-193">Disable CORS</span></span>

<span data-ttu-id="122e8-194">Das [[disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert **keine**  cors, die durch das [Endpunkt Routing](#ecors)aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="122e8-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="122e8-195">Der folgende Code definiert die cors-Richtlinie `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="122e8-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="122e8-196">Der folgende Code deaktiviert cors für die `GetValues2` Aktion:</span><span class="sxs-lookup"><span data-stu-id="122e8-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="122e8-197">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122e8-197">The preceding code:</span></span>

* <span data-ttu-id="122e8-198">Aktivieren von cors mit dem [Endpunkt Routing](#ecors)nicht.</span><span class="sxs-lookup"><span data-stu-id="122e8-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="122e8-199">Definiert keine [cors-Standard Richtlinie](#dp).</span><span class="sxs-lookup"><span data-stu-id="122e8-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="122e8-200">Verwendet [[enablecors ("MyPolicy")]](#attr) , um die `"MyPolicy"` cors-Richtlinie für den Controller zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="122e8-201">Deaktiviert cors für die- `GetValues2` Methode.</span><span class="sxs-lookup"><span data-stu-id="122e8-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="122e8-202">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#testc) .</span><span class="sxs-lookup"><span data-stu-id="122e8-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="122e8-203">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="122e8-203">CORS policy options</span></span>

<span data-ttu-id="122e8-204">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="122e8-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="122e8-205">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="122e8-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="122e8-206">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="122e8-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="122e8-207">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="122e8-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="122e8-208">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="122e8-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="122e8-209">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="122e8-210">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="122e8-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="122e8-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in aufgerufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="122e8-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="122e8-212">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="122e8-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="122e8-213">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="122e8-213">Set the allowed origins</span></span>

<span data-ttu-id="122e8-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ).</span><span class="sxs-lookup"><span data-stu-id="122e8-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="122e8-215">`AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="122e8-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="122e8-216">Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="122e8-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="122e8-217">Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="122e8-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="122e8-218">`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="122e8-219">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="122e8-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist</span><span class="sxs-lookup"><span data-stu-id="122e8-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="122e8-221">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="122e8-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="122e8-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="122e8-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="122e8-223">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="122e8-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="122e8-224">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="122e8-225">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="122e8-226">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="122e8-226">Set the allowed request headers</span></span>

<span data-ttu-id="122e8-227">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als [Autor Request Headers](https://xhr.spec.whatwg.org/#request)bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:</span><span class="sxs-lookup"><span data-stu-id="122e8-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="122e8-228">Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="122e8-229">`AllowAnyHeader` wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="122e8-230">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="122e8-231">Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die durch angegeben werden `WithHeaders` `Access-Control-Request-Headers` `WithHeaders`</span><span class="sxs-lookup"><span data-stu-id="122e8-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="122e8-232">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="122e8-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="122e8-233">Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, da `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="122e8-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="122e8-234">Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="122e8-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="122e8-235">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="122e8-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="122e8-236">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="122e8-236">Set the exposed response headers</span></span>

<span data-ttu-id="122e8-237">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="122e8-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="122e8-238">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="122e8-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="122e8-239">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="122e8-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="122e8-240">Mit der cors-Spezifikation werden die Header für *einfache Antworten* aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="122e8-240">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="122e8-241">Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="122e8-242">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="122e8-243">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="122e8-244">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="122e8-245">Anmelde Informationen umfassen :::no-loc(cookie)::: e-und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="122e8-245">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="122e8-246">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="122e8-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="122e8-247">`XMLHttpRequest`Direkt verwenden:</span><span class="sxs-lookup"><span data-stu-id="122e8-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="122e8-248">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="122e8-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="122e8-249">Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="122e8-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="122e8-250">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="122e8-250">The server must allow the credentials.</span></span> <span data-ttu-id="122e8-251">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="122e8-252">Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="122e8-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="122e8-253">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="122e8-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="122e8-254">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="122e8-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="122e8-255">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="122e8-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="122e8-256">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="122e8-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="122e8-257">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-257">Preflight requests</span></span>

<span data-ttu-id="122e8-258">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="122e8-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="122e8-259">Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="122e8-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="122e8-260">Der Browser kann die Preflight-Anforderung überspringen, wenn **alle** der folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="122e8-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="122e8-261">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="122e8-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="122e8-262">Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122e8-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="122e8-263">`Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="122e8-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="122e8-264">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="122e8-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="122e8-265">In der cors-Spezifikation werden diese Header [Anforderungs Header für Autoren](https://www.w3.org/TR/cors/#author-request-headers)aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="122e8-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="122e8-266">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="122e8-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="122e8-267">Im folgenden finden Sie eine Beispiel Antwort, die der Preflight-Anforderung ähnelt, die im Abschnitt " [Test cors](#testc) " dieses Dokuments von der Schaltfläche **[Put Test]** erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="122e8-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="122e8-268">Die Preflight-Anforderung verwendet die [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) -Methode.</span><span class="sxs-lookup"><span data-stu-id="122e8-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="122e8-269">Die folgenden Header sind möglicherweise enthalten:</span><span class="sxs-lookup"><span data-stu-id="122e8-269">It may include the following headers:</span></span>

* <span data-ttu-id="122e8-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="122e8-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="122e8-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="122e8-272">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..</span><span class="sxs-lookup"><span data-stu-id="122e8-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="122e8-273">Access-Control-Allow-Methods</span><span class="sxs-lookup"><span data-stu-id="122e8-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="122e8-274">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine Antwort zurück, `200 OK` legt jedoch keine cors-Header fest.</span><span class="sxs-lookup"><span data-stu-id="122e8-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="122e8-275">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="122e8-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="122e8-276">Ein Beispiel für eine verweigerte Preflight-Anforderung finden Sie im Abschnitt zum [Testen von cors](#testc) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="122e8-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="122e8-277">Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen Fehler an, der einem der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="122e8-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="122e8-278">Firefox: Ursprungs übergreifende Anforderung blockiert: die gleiche Ursprungs Richtlinie lässt das Lesen der Remote Ressource unter nicht zu `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="122e8-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="122e8-279">(Ursache: die cors-Anforderung war nicht erfolgreich).</span><span class="sxs-lookup"><span data-stu-id="122e8-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="122e8-280">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="122e8-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="122e8-281">Chrom basiert: der Zugriff auf " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " vom Ursprung "" wurde https://cors3.azurewebsites.net durch eine cors-Richtlinie blockiert: die Antwort auf eine Preflight-Anforderung übergibt die Zugriffs Steuerungs Prüfung nicht: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden.</span><span class="sxs-lookup"><span data-stu-id="122e8-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="122e8-282">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="122e8-283">Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="122e8-284">Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="122e8-285">Browser sind nicht konsistent, wie Sie festgelegt werden `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="122e8-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="122e8-286">Wenn beides:</span><span class="sxs-lookup"><span data-stu-id="122e8-286">If either:</span></span>

* <span data-ttu-id="122e8-287">Header werden auf einen anderen Wert als festgelegt. `"*"`</span><span class="sxs-lookup"><span data-stu-id="122e8-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="122e8-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> wird aufgerufen: Schließen Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header ein, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="122e8-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="122e8-289">Automatischer Preflight-Anforderungs Code</span><span class="sxs-lookup"><span data-stu-id="122e8-289">Automatic preflight request code</span></span>

<span data-ttu-id="122e8-290">Wenn die cors-Richtlinie angewendet wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="122e8-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="122e8-291">Global durch Aufrufen von `app.UseCors` in `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="122e8-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="122e8-292">Verwenden des- `[EnableCors]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="122e8-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="122e8-293">ASP.net Core antwortet auf die Preflight-Options Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="122e8-294">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt derzeit **keine** automatischen Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="122e8-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="122e8-295">Dieses Verhalten wird im Abschnitt " [Test-cors](#testc) " dieses Dokuments veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="122e8-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="122e8-296">[Httpoptions]-Attribut für Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="122e8-297">Wenn cors mit der entsprechenden Richtlinie aktiviert ist, reagieren ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="122e8-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="122e8-298">In einigen Szenarios ist dies möglicherweise nicht der Fall.</span><span class="sxs-lookup"><span data-stu-id="122e8-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="122e8-299">Beispielsweise bei Verwendung von [cors mit dem Endpunkt Routing](#ecors).</span><span class="sxs-lookup"><span data-stu-id="122e8-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="122e8-300">Der folgende Code verwendet das [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) -Attribut, um Endpunkte für Options Anforderungen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="122e8-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="122e8-301">Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="122e8-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="122e8-302">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="122e8-302">Set the preflight expiration time</span></span>

<span data-ttu-id="122e8-303">Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="122e8-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="122e8-304">Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="122e8-305">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="122e8-305">How CORS works</span></span>

<span data-ttu-id="122e8-306">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="122e8-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="122e8-307">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="122e8-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="122e8-308">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="122e8-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="122e8-309">Ein böswilliger Akteur könnte z. b. Site übergreifende Skripts [(Cross-Site Scripting, XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="122e8-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="122e8-310">Eine API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="122e8-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="122e8-311">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="122e8-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="122e8-312">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="122e8-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="122e8-313">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="122e8-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="122e8-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="122e8-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="122e8-315">Postman</span><span class="sxs-lookup"><span data-stu-id="122e8-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="122e8-316">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="122e8-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="122e8-317">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="122e8-318">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="122e8-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="122e8-319">Browser ohne cors können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="122e8-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="122e8-320">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="122e8-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="122e8-321">JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="122e8-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="122e8-322">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="122e8-323">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="122e8-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="122e8-324">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122e8-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="122e8-325">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="122e8-326">[Schaltfläche "Test platzieren](https://cors3.azurewebsites.net/test) " im bereitgestellten [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="122e8-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="122e8-327">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung von der Test Schaltfläche " [Werte](https://cors3.azurewebsites.net/) " zu `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="122e8-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="122e8-328">Der `Origin` Header:</span><span class="sxs-lookup"><span data-stu-id="122e8-328">The `Origin` header:</span></span>

* <span data-ttu-id="122e8-329">Stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="122e8-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="122e8-330">Ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="122e8-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="122e8-331">**Allgemeine Header**</span><span class="sxs-lookup"><span data-stu-id="122e8-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="122e8-332">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="122e8-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="122e8-333">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="122e8-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="122e8-334">In `OPTIONS` Anforderungen legt der Server den Header der **Antwortheader** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="122e8-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="122e8-335">Beispielsweise enthält die Schaltfläche für das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` die folgenden Header:</span><span class="sxs-lookup"><span data-stu-id="122e8-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="122e8-336">**Allgemeine Header**</span><span class="sxs-lookup"><span data-stu-id="122e8-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="122e8-337">**Antwortheader**</span><span class="sxs-lookup"><span data-stu-id="122e8-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-:::no-loc(Cookie):::: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="122e8-338">**Anforderungsheader**</span><span class="sxs-lookup"><span data-stu-id="122e8-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="122e8-339">In den vorangehenden **Antwort Headern** legt der Server den [Access-Control-Allow-Origin-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) Header in der Antwort fest.</span><span class="sxs-lookup"><span data-stu-id="122e8-339">In the preceding **Response headers** , the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="122e8-340">Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="122e8-341">Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der Platzhalter `Access-Control-Allow-Origin: *` Wert zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="122e8-342">`AllowAnyOrigin` ermöglicht einen beliebigen Ursprung.</span><span class="sxs-lookup"><span data-stu-id="122e8-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="122e8-343">Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="122e8-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="122e8-344">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="122e8-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="122e8-345">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="122e8-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="122e8-346">Anzeige Options Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-346">Display OPTIONS requests</span></span>

<span data-ttu-id="122e8-347">Standardmäßig zeigen die Browser Chrome und Edge keine Options Anforderungen auf der Registerkarte Netzwerk der F12-Tools an.</span><span class="sxs-lookup"><span data-stu-id="122e8-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="122e8-348">So zeigen Sie Options Anforderungen in diesen Browsern an:</span><span class="sxs-lookup"><span data-stu-id="122e8-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="122e8-349">`chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="122e8-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="122e8-350">Deaktivieren Sie das-Flag.</span><span class="sxs-lookup"><span data-stu-id="122e8-350">disable the flag.</span></span>
* <span data-ttu-id="122e8-351">„Neu starten“.</span><span class="sxs-lookup"><span data-stu-id="122e8-351">restart.</span></span>

<span data-ttu-id="122e8-352">Firefox zeigt Options Anforderungen standardmäßig an.</span><span class="sxs-lookup"><span data-stu-id="122e8-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="122e8-353">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="122e8-353">CORS in IIS</span></span>

<span data-ttu-id="122e8-354">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="122e8-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="122e8-355">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="122e8-356">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="122e8-356">Test CORS</span></span>

<span data-ttu-id="122e8-357">Der [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von cors.</span><span class="sxs-lookup"><span data-stu-id="122e8-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="122e8-358">[Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="122e8-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="122e8-359">Das Beispiel ist ein API-Projekt mit :::no-loc(Razor)::: hinzugefügten Seiten:</span><span class="sxs-lookup"><span data-stu-id="122e8-359">The sample is an API project with :::no-loc(Razor)::: Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="122e8-360">`WithOrigins("https://localhost:<port>");` sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="122e8-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="122e8-361">Im folgenden `ValuesController` finden Sie die Endpunkte für Tests:</span><span class="sxs-lookup"><span data-stu-id="122e8-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="122e8-362">[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Rick.Docs. Samples. routeinfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) -nuget-Paket bereitgestellt und zeigt Routeninformationen an.</span><span class="sxs-lookup"><span data-stu-id="122e8-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="122e8-363">Testen Sie den vorangehenden Beispielcode, indem Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="122e8-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="122e8-364">Verwenden Sie die bereitgestellte Beispiel-app unter [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="122e8-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="122e8-365">Das Beispiel muss nicht heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="122e8-366">Führen Sie das Beispiel mit `dotnet run` der Standard-URL von aus `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="122e8-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="122e8-367">Führen Sie das Beispiel aus Visual Studio aus, wobei der Port auf 44398 für eine URL von festgelegt ist `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="122e8-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="122e8-368">Verwenden eines Browsers mit den F12-Tools:</span><span class="sxs-lookup"><span data-stu-id="122e8-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="122e8-369">Wählen Sie die Schaltfläche **Werte** , und überprüfen Sie die Header auf der Registerkarte **Netzwerk** .</span><span class="sxs-lookup"><span data-stu-id="122e8-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="122e8-370">Wählen Sie die Schaltfläche **Test platzieren** aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-370">Select the **PUT test** button.</span></span> <span data-ttu-id="122e8-371">Anweisungen zum Anzeigen der Options Anforderung finden Sie unter [Anzeigen von Options Anforderungen](#options) .</span><span class="sxs-lookup"><span data-stu-id="122e8-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="122e8-372">Der **Put-Test** erstellt zwei Anforderungen, eine Preflight-Anforderung für Optionen und die PUT-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="122e8-373">Wählen Sie die **`GetValues2 [DisableCors]`** Schaltfläche aus, um eine fehlgeschlagene cors-Anforderung</span><span class="sxs-lookup"><span data-stu-id="122e8-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="122e8-374">Wie im Dokument erwähnt, gibt die Antwort 200 Erfolg zurück, aber die cors-Anforderung wird nicht hergestellt.</span><span class="sxs-lookup"><span data-stu-id="122e8-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="122e8-375">Wählen Sie die Registerkarte **Konsole** aus, um den cors-Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="122e8-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="122e8-376">Abhängig vom Browser wird ein Fehler angezeigt, der dem folgenden Beispiel ähnelt:</span><span class="sxs-lookup"><span data-stu-id="122e8-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="122e8-377">Der Zugriff zum Abrufen auf `'https://cors1.azurewebsites.net/api/values/GetValues2'` der Ursprungsseite wurde `'https://cors3.azurewebsites.net'` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden.</span><span class="sxs-lookup"><span data-stu-id="122e8-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="122e8-378">Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="122e8-379">Cors-aktivierte Endpunkte können mit einem Tool wie [curl](https://curl.haxx.se/), " [fddler](https://www.telerik.com/fiddler)" oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="122e8-380">Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="122e8-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="122e8-381">Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :</span><span class="sxs-lookup"><span data-stu-id="122e8-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="122e8-382">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="122e8-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="122e8-383">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="122e8-384">Der folgende Befehl verwendet `curl` , um eine Options Anforderung mit Informationen auszugeben:</span><span class="sxs-lookup"><span data-stu-id="122e8-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="122e8-385">Testen von cors mit dem Endpunkt Routing und [httpoptions]</span><span class="sxs-lookup"><span data-stu-id="122e8-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="122e8-386">Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf).</span><span class="sxs-lookup"><span data-stu-id="122e8-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="122e8-387">Beachten Sie den folgenden Code, der das [Endpunkt Routing verwendet, um cors zu aktivieren](#ecors):</span><span class="sxs-lookup"><span data-stu-id="122e8-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="122e8-388">Im folgenden `TodoItems1Controller` finden Sie Endpunkte zum Testen:</span><span class="sxs-lookup"><span data-stu-id="122e8-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="122e8-389">Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=1) " des bereitgestellten [Beispiels.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span><span class="sxs-lookup"><span data-stu-id="122e8-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="122e8-390">Die Schaltflächen " **delete [enablecors]** " und " **Get [enablecors]** " sind erfolgreich, da die Endpunkte `[EnableCors]` Preflight-Anforderungen aufweisen und darauf reagieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="122e8-391">Die anderen Endpunkte schlagen fehl.</span><span class="sxs-lookup"><span data-stu-id="122e8-391">The other endpoints fails.</span></span> <span data-ttu-id="122e8-392">Die Schaltfläche **Get** schlägt fehl, da das [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Folgendes sendet:</span><span class="sxs-lookup"><span data-stu-id="122e8-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="122e8-393">Im folgenden `TodoItems2Controller` werden ähnliche Endpunkte bereitgestellt, aber es ist expliziter Code enthalten, der auf Options Anforderungen antwortet:</span><span class="sxs-lookup"><span data-stu-id="122e8-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="122e8-394">Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=2) " des bereitgestellten Beispiels.</span><span class="sxs-lookup"><span data-stu-id="122e8-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="122e8-395">Wählen Sie in der Dropdown Liste **Controller** den Wert **Preflight** aus, und legen Sie dann **Controller fest** .</span><span class="sxs-lookup"><span data-stu-id="122e8-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller** .</span></span> <span data-ttu-id="122e8-396">Alle cors-Aufrufe an die `TodoItems2Controller` Endpunkte sind erfolgreich.</span><span class="sxs-lookup"><span data-stu-id="122e8-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="122e8-397">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="122e8-397">Additional resources</span></span>

* [<span data-ttu-id="122e8-398">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="122e8-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="122e8-399">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="122e8-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="122e8-400">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="122e8-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="122e8-401">In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="122e8-402">Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat.</span><span class="sxs-lookup"><span data-stu-id="122e8-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="122e8-403">Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="122e8-403">This restriction is called the *same-origin policy* .</span></span> <span data-ttu-id="122e8-404">Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest.</span><span class="sxs-lookup"><span data-stu-id="122e8-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="122e8-405">Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen.</span><span class="sxs-lookup"><span data-stu-id="122e8-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="122e8-406">Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="122e8-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="122e8-407">[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):</span><span class="sxs-lookup"><span data-stu-id="122e8-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="122e8-408">Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="122e8-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="122e8-409">Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors.</span><span class="sxs-lookup"><span data-stu-id="122e8-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="122e8-410">Eine API wird durch das Zulassen von cors nicht sicherer.</span><span class="sxs-lookup"><span data-stu-id="122e8-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="122e8-411">Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="122e8-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="122e8-412">Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="122e8-413">Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).</span><span class="sxs-lookup"><span data-stu-id="122e8-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="122e8-414">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="122e8-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="122e8-415">Gleicher Ursprung</span><span class="sxs-lookup"><span data-stu-id="122e8-415">Same origin</span></span>

<span data-ttu-id="122e8-416">Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="122e8-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="122e8-417">Diese beiden URLs haben denselben Ursprung:</span><span class="sxs-lookup"><span data-stu-id="122e8-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="122e8-418">Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:</span><span class="sxs-lookup"><span data-stu-id="122e8-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="122e8-419">`https://example.net`: Andere Domäne</span><span class="sxs-lookup"><span data-stu-id="122e8-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="122e8-420">`https://www.example.com/foo.html`: Untergeordnete Domäne</span><span class="sxs-lookup"><span data-stu-id="122e8-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="122e8-421">`http://example.com/foo.html`: Anderes Schema</span><span class="sxs-lookup"><span data-stu-id="122e8-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="122e8-422">`https://example.com:9000/foo.html`: Anderer Port</span><span class="sxs-lookup"><span data-stu-id="122e8-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="122e8-423">Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="122e8-424">Cors mit benannten Richtlinie und Middleware</span><span class="sxs-lookup"><span data-stu-id="122e8-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="122e8-425">Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="122e8-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="122e8-426">Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:</span><span class="sxs-lookup"><span data-stu-id="122e8-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="122e8-427">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="122e8-427">The preceding code:</span></span>

* <span data-ttu-id="122e8-428">Legt den Richtlinien Namen auf " \_ myallowspecificorigins" fest.</span><span class="sxs-lookup"><span data-stu-id="122e8-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="122e8-429">Der Richtlinien Name ist willkürlich.</span><span class="sxs-lookup"><span data-stu-id="122e8-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="122e8-430">Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf, die cors aktiviert.</span><span class="sxs-lookup"><span data-stu-id="122e8-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="122e8-431">Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf.</span><span class="sxs-lookup"><span data-stu-id="122e8-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="122e8-432">Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an.</span><span class="sxs-lookup"><span data-stu-id="122e8-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="122e8-433">[Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="122e8-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="122e8-434">Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:</span><span class="sxs-lookup"><span data-stu-id="122e8-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="122e8-435">Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="122e8-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="122e8-436">Die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:</span><span class="sxs-lookup"><span data-stu-id="122e8-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="122e8-437">Hinweis: die URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten.</span><span class="sxs-lookup"><span data-stu-id="122e8-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="122e8-438">Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="122e8-439">Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:</span><span class="sxs-lookup"><span data-stu-id="122e8-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="122e8-440">Hinweis: `UseCors` muss vor aufgerufen werden `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="122e8-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="122e8-441">Weitere Informationen finden Sie unter [Aktivieren von cors in :::no-loc(Razor)::: Seiten, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions</span><span class="sxs-lookup"><span data-stu-id="122e8-441">See [Enable CORS in :::no-loc(Razor)::: Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="122e8-442">Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#test) .</span><span class="sxs-lookup"><span data-stu-id="122e8-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="122e8-443">Aktivieren von cors mit Attributen</span><span class="sxs-lookup"><span data-stu-id="122e8-443">Enable CORS with attributes</span></span>

<span data-ttu-id="122e8-444">Das [ &lbrack; enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors.</span><span class="sxs-lookup"><span data-stu-id="122e8-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="122e8-445">Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="122e8-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="122e8-446">Verwenden `[EnableCors]` Sie, um die Standard Richtlinie anzugeben und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="122e8-447">Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="122e8-447">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="122e8-448">:::no-loc(Razor)::: S `PageModel`</span><span class="sxs-lookup"><span data-stu-id="122e8-448">:::no-loc(Razor)::: Page `PageModel`</span></span>
* <span data-ttu-id="122e8-449">Controller</span><span class="sxs-lookup"><span data-stu-id="122e8-449">Controller</span></span>
* <span data-ttu-id="122e8-450">Controller-Aktionsmethode</span><span class="sxs-lookup"><span data-stu-id="122e8-450">Controller action method</span></span>

<span data-ttu-id="122e8-451">Mit dem-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden  `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="122e8-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="122e8-452">Wenn das `[EnableCors]` -Attribut auf ein Controller/Seiten Modell/eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet.</span><span class="sxs-lookup"><span data-stu-id="122e8-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="122e8-453">Wir empfehlen, Richtlinien **nicht** zu kombinieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="122e8-454">Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, **nicht beides** .</span><span class="sxs-lookup"><span data-stu-id="122e8-454">Use the `[EnableCors]` attribute or middleware, **not both** .</span></span> <span data-ttu-id="122e8-455">Definieren Sie bei Verwendung von `[EnableCors]` **keine** Standard Richtlinie.</span><span class="sxs-lookup"><span data-stu-id="122e8-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="122e8-456">Der folgende Code wendet eine andere Richtlinie auf jede Methode an:</span><span class="sxs-lookup"><span data-stu-id="122e8-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="122e8-457">Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="122e8-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="122e8-458">Cors deaktivieren</span><span class="sxs-lookup"><span data-stu-id="122e8-458">Disable CORS</span></span>

<span data-ttu-id="122e8-459">Das [ &lbrack; disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert cors für Controller/Page-Model/Action.</span><span class="sxs-lookup"><span data-stu-id="122e8-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="122e8-460">Cors-Richtlinien Optionen</span><span class="sxs-lookup"><span data-stu-id="122e8-460">CORS policy options</span></span>

<span data-ttu-id="122e8-461">In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="122e8-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="122e8-462">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="122e8-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="122e8-463">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="122e8-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="122e8-464">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="122e8-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="122e8-465">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="122e8-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="122e8-466">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="122e8-467">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="122e8-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="122e8-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in aufgerufen `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="122e8-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="122e8-469">Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.</span><span class="sxs-lookup"><span data-stu-id="122e8-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="122e8-470">Festlegen der zulässigen Ursprünge</span><span class="sxs-lookup"><span data-stu-id="122e8-470">Set the allowed origins</span></span>

<span data-ttu-id="122e8-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ).</span><span class="sxs-lookup"><span data-stu-id="122e8-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="122e8-472">`AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.</span><span class="sxs-lookup"><span data-stu-id="122e8-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="122e8-473">Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen.</span><span class="sxs-lookup"><span data-stu-id="122e8-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="122e8-474">Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.</span><span class="sxs-lookup"><span data-stu-id="122e8-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="122e8-475">`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="122e8-476">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="122e8-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist</span><span class="sxs-lookup"><span data-stu-id="122e8-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="122e8-478">Festlegen der zulässigen HTTP-Methoden</span><span class="sxs-lookup"><span data-stu-id="122e8-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="122e8-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="122e8-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="122e8-480">Lässt jede HTTP-Methode zu:</span><span class="sxs-lookup"><span data-stu-id="122e8-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="122e8-481">Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="122e8-482">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="122e8-483">Festlegen der zulässigen Anforderungs Header</span><span class="sxs-lookup"><span data-stu-id="122e8-483">Set the allowed request headers</span></span>

<span data-ttu-id="122e8-484">Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers* bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:</span><span class="sxs-lookup"><span data-stu-id="122e8-484">To allow specific headers to be sent in a CORS request, called *author request headers* , call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="122e8-485">Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="122e8-486">Diese Einstellung wirkt sich auf Preflight-Anforderungen und den- `Access-Control-Request-Headers` Header aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="122e8-487">Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).</span><span class="sxs-lookup"><span data-stu-id="122e8-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="122e8-488">Cors-Middleware erlaubt immer, dass vier Header in der `Access-Control-Request-Headers` gesendet werden, unabhängig von den in corspolicy. Headers konfigurierten Werten.</span><span class="sxs-lookup"><span data-stu-id="122e8-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="122e8-489">Diese Liste von Headern umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="122e8-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="122e8-490">Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="122e8-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="122e8-491">Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, da `Content-Language` immer zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="122e8-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="122e8-492">Festlegen der verfügbar gemachten Antwortheader</span><span class="sxs-lookup"><span data-stu-id="122e8-492">Set the exposed response headers</span></span>

<span data-ttu-id="122e8-493">Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="122e8-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="122e8-494">Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="122e8-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="122e8-495">Die standardmäßig verfügbaren Antwortheader lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="122e8-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="122e8-496">Mit der cors-Spezifikation werden die Header für *einfache Antworten* aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="122e8-496">The CORS specification calls these headers *simple response headers* .</span></span> <span data-ttu-id="122e8-497">Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="122e8-498">Anmelde Informationen in Ursprungs übergreifenden Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="122e8-499">Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="122e8-500">Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="122e8-501">Anmelde Informationen umfassen :::no-loc(cookie)::: e-und http-Authentifizierungs Schemas.</span><span class="sxs-lookup"><span data-stu-id="122e8-501">Credentials include :::no-loc(cookie):::s and HTTP authentication schemes.</span></span> <span data-ttu-id="122e8-502">Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="122e8-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="122e8-503">`XMLHttpRequest`Direkt verwenden:</span><span class="sxs-lookup"><span data-stu-id="122e8-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="122e8-504">Verwenden von jQuery:</span><span class="sxs-lookup"><span data-stu-id="122e8-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="122e8-505">Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span><span class="sxs-lookup"><span data-stu-id="122e8-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="122e8-506">Der Server muss die Anmelde Informationen zulassen.</span><span class="sxs-lookup"><span data-stu-id="122e8-506">The server must allow the credentials.</span></span> <span data-ttu-id="122e8-507">Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="122e8-508">Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.</span><span class="sxs-lookup"><span data-stu-id="122e8-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="122e8-509">Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="122e8-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="122e8-510">Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="122e8-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="122e8-511">Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden.</span><span class="sxs-lookup"><span data-stu-id="122e8-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="122e8-512">In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="122e8-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="122e8-513">Preflight-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="122e8-513">Preflight requests</span></span>

<span data-ttu-id="122e8-514">Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="122e8-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="122e8-515">Diese Anforderung wird als *Preflight-Anforderung* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="122e8-515">This request is called a *preflight request* .</span></span> <span data-ttu-id="122e8-516">Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="122e8-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="122e8-517">Die Anforderungs Methode ist Get, Head oder Post.</span><span class="sxs-lookup"><span data-stu-id="122e8-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="122e8-518">Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122e8-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="122e8-519">`Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:</span><span class="sxs-lookup"><span data-stu-id="122e8-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="122e8-520">Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="122e8-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="122e8-521">In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren* aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="122e8-521">The CORS specification calls these headers *author request headers* .</span></span> <span data-ttu-id="122e8-522">Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="122e8-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="122e8-523">Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:</span><span class="sxs-lookup"><span data-stu-id="122e8-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="122e8-524">Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode.</span><span class="sxs-lookup"><span data-stu-id="122e8-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="122e8-525">Es enthält zwei spezielle Header:</span><span class="sxs-lookup"><span data-stu-id="122e8-525">It includes two special headers:</span></span>

* <span data-ttu-id="122e8-526">`Access-Control-Request-Method`: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="122e8-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="122e8-527">`Access-Control-Request-Headers`: Eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="122e8-528">Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..</span><span class="sxs-lookup"><span data-stu-id="122e8-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="122e8-529">Wenn cors mit der entsprechenden Richtlinie aktiviert ist, antwortet ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="122e8-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="122e8-530">Weitere Informationen finden Sie unter [[httpoptions]-Attribut für Preflight-Anforderungen](#pro).</span><span class="sxs-lookup"><span data-stu-id="122e8-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="122e8-531">Eine cors-Preflight-Anforderung kann einen- `Access-Control-Request-Headers` Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="122e8-532">Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="122e8-533">Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="122e8-534">Browser sind nicht vollständig konsistent, wenn Sie festgelegt werden `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="122e8-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="122e8-535">Wenn Sie Header auf einen anderen Wert als festlegen `"*"` (oder verwenden <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), sollten Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="122e8-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="122e8-536">Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):</span><span class="sxs-lookup"><span data-stu-id="122e8-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="122e8-537">Die Antwort enthält einen `Access-Control-Allow-Methods` Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers` Header, der die zulässigen Header auflistet.</span><span class="sxs-lookup"><span data-stu-id="122e8-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="122e8-538">Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="122e8-539">Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück.</span><span class="sxs-lookup"><span data-stu-id="122e8-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="122e8-540">Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="122e8-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="122e8-541">Festlegen der Preflight-Ablaufzeit</span><span class="sxs-lookup"><span data-stu-id="122e8-541">Set the preflight expiration time</span></span>

<span data-ttu-id="122e8-542">Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann.</span><span class="sxs-lookup"><span data-stu-id="122e8-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="122e8-543">Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="122e8-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="122e8-544">Funktionsweise von cors</span><span class="sxs-lookup"><span data-stu-id="122e8-544">How CORS works</span></span>

<span data-ttu-id="122e8-545">In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.</span><span class="sxs-lookup"><span data-stu-id="122e8-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="122e8-546">Cors ist **kein** Sicherheits Feature.</span><span class="sxs-lookup"><span data-stu-id="122e8-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="122e8-547">Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.</span><span class="sxs-lookup"><span data-stu-id="122e8-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="122e8-548">Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.</span><span class="sxs-lookup"><span data-stu-id="122e8-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="122e8-549">Ihre API ist nicht sicherer, da Sie cors zulässt.</span><span class="sxs-lookup"><span data-stu-id="122e8-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="122e8-550">Es liegt an dem Client (Browser), cors zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="122e8-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="122e8-551">Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert.</span><span class="sxs-lookup"><span data-stu-id="122e8-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="122e8-552">Eines der folgenden Tools zeigt z. b. die Serverantwort an:</span><span class="sxs-lookup"><span data-stu-id="122e8-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="122e8-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="122e8-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="122e8-554">Postman</span><span class="sxs-lookup"><span data-stu-id="122e8-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="122e8-555">.Net httpclient</span><span class="sxs-lookup"><span data-stu-id="122e8-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="122e8-556">Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="122e8-557">Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.</span><span class="sxs-lookup"><span data-stu-id="122e8-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="122e8-558">Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen.</span><span class="sxs-lookup"><span data-stu-id="122e8-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="122e8-559">Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen.</span><span class="sxs-lookup"><span data-stu-id="122e8-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="122e8-560">JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="122e8-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="122e8-561">Skripts können Ursprungs übergreifend geladen werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="122e8-562">In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="122e8-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="122e8-563">Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122e8-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="122e8-564">Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="122e8-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="122e8-565">Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung.</span><span class="sxs-lookup"><span data-stu-id="122e8-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="122e8-566">Der `Origin` -Header stellt die Domäne der Site bereit, von der die Anforderung stammt.</span><span class="sxs-lookup"><span data-stu-id="122e8-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="122e8-567">Der `Origin` -Header ist erforderlich und muss sich vom Host unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="122e8-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="122e8-568">Wenn der Server die Anforderung zulässt, wird der- `Access-Control-Allow-Origin` Header in der Antwort festgelegt.</span><span class="sxs-lookup"><span data-stu-id="122e8-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="122e8-569">Der Wert dieses Headers stimmt entweder mit dem `Origin` Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"` , was bedeutet, dass ein beliebiger Ursprung zulässig ist:</span><span class="sxs-lookup"><span data-stu-id="122e8-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="122e8-570">Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="122e8-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="122e8-571">Der Browser lässt die Anforderung insbesondere nicht zu.</span><span class="sxs-lookup"><span data-stu-id="122e8-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="122e8-572">Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="122e8-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="122e8-573">Testen von CORS</span><span class="sxs-lookup"><span data-stu-id="122e8-573">Test CORS</span></span>

<span data-ttu-id="122e8-574">So testen Sie cors:</span><span class="sxs-lookup"><span data-stu-id="122e8-574">To test CORS:</span></span>

1. <span data-ttu-id="122e8-575">[Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="122e8-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="122e8-576">Alternativ können Sie [das Beispiel herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="122e8-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="122e8-577">Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="122e8-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="122e8-578">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="122e8-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="122e8-579">`WithOrigins("https://localhost:<port>");` sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.</span><span class="sxs-lookup"><span data-stu-id="122e8-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="122e8-580">Erstellen Sie ein Web-App-Projekt ( :::no-loc(Razor)::: Pages oder MVC).</span><span class="sxs-lookup"><span data-stu-id="122e8-580">Create a web app project (:::no-loc(Razor)::: Pages or MVC).</span></span> <span data-ttu-id="122e8-581">Im Beispiel werden :::no-loc(Razor)::: Seiten verwendet.</span><span class="sxs-lookup"><span data-stu-id="122e8-581">The sample uses :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="122e8-582">Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="122e8-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="122e8-583">Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="122e8-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="122e8-584">Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.</span><span class="sxs-lookup"><span data-stu-id="122e8-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="122e8-585">Stellen Sie das API-Projekt bereit.</span><span class="sxs-lookup"><span data-stu-id="122e8-585">Deploy the API project.</span></span> <span data-ttu-id="122e8-586">Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="122e8-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="122e8-587">Führen :::no-loc(Razor)::: Sie die Seiten oder die MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** .</span><span class="sxs-lookup"><span data-stu-id="122e8-587">Run the :::no-loc(Razor)::: Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="122e8-588">Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="122e8-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="122e8-589">Entfernen Sie den localhost-Ursprung aus, und stellen Sie `WithOrigins` die APP bereit.</span><span class="sxs-lookup"><span data-stu-id="122e8-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="122e8-590">Alternativ können Sie die Client-App mit einem anderen Port ausführen.</span><span class="sxs-lookup"><span data-stu-id="122e8-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="122e8-591">Führen Sie z. b. in Visual Studio aus.</span><span class="sxs-lookup"><span data-stu-id="122e8-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="122e8-592">Testen Sie mit der Client-App.</span><span class="sxs-lookup"><span data-stu-id="122e8-592">Test with the client app.</span></span> <span data-ttu-id="122e8-593">Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="122e8-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="122e8-594">Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="122e8-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="122e8-595">Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="122e8-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="122e8-596">Verwenden von Microsoft Edge:</span><span class="sxs-lookup"><span data-stu-id="122e8-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="122e8-597">**SEC7120: [cors] der Ursprung `https://localhost:44375` wurde `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für die Ursprungs übergreifende Ressource nicht gefunden in `https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="122e8-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="122e8-598">Verwenden von Chrome:</span><span class="sxs-lookup"><span data-stu-id="122e8-598">Using Chrome:</span></span>

     <span data-ttu-id="122e8-599">**Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` der Ursprungsseite wurde `https://localhost:44375` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden.**</span><span class="sxs-lookup"><span data-stu-id="122e8-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="122e8-600">Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="122e8-601">Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt.</span><span class="sxs-lookup"><span data-stu-id="122e8-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="122e8-602">Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :</span><span class="sxs-lookup"><span data-stu-id="122e8-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="122e8-603">Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="122e8-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="122e8-604">Cors-Header werden in der Antwort nicht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="122e8-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="122e8-605">Cors in IIS</span><span class="sxs-lookup"><span data-stu-id="122e8-605">CORS in IIS</span></span>

<span data-ttu-id="122e8-606">Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="122e8-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="122e8-607">Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="122e8-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="122e8-608">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="122e8-608">Additional resources</span></span>

* [<span data-ttu-id="122e8-609">Cross-Origin Resource Sharing (cors)</span><span class="sxs-lookup"><span data-stu-id="122e8-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="122e8-610">Einführung in das IIS cors-Modul</span><span class="sxs-lookup"><span data-stu-id="122e8-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
