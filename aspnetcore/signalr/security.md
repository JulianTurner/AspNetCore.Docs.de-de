---
title: 'Sicherheitsüberlegungen in ASP.net Core :::no-loc(SignalR):::'
author: bradygaster
description: 'Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core verwenden :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050835"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="7eddc-103">Sicherheitsüberlegungen in ASP.net Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="7eddc-103">Security considerations in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="7eddc-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="7eddc-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="7eddc-105">Dieser Artikel enthält Informationen zum Sichern von :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="7eddc-105">This article provides information on securing :::no-loc(SignalR):::.</span></span>

## <a name="cross-origin-resource-sharing"></a><span data-ttu-id="7eddc-106">Cross-Origin Resource Sharing</span><span class="sxs-lookup"><span data-stu-id="7eddc-106">Cross-origin resource sharing</span></span>

<span data-ttu-id="7eddc-107">[Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/) kann verwendet werden, um Ursprungs übergreifende :::no-loc(SignalR)::: Verbindungen im Browser zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-107">[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) can be used to allow cross-origin :::no-loc(SignalR)::: connections in the browser.</span></span> <span data-ttu-id="7eddc-108">Wenn JavaScript-Code in einer anderen Domäne als der APP gehostet wird :::no-loc(SignalR)::: , muss [cors-Middleware](xref:security/cors) aktiviert werden, damit JavaScript eine Verbindung mit der App herstellen kann :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="7eddc-108">If JavaScript code is hosted on a different domain from the :::no-loc(SignalR)::: app, [CORS middleware](xref:security/cors) must be enabled to allow the JavaScript to connect to the :::no-loc(SignalR)::: app.</span></span> <span data-ttu-id="7eddc-109">Lassen Sie Ursprungs übergreifende Anforderungen nur von vertrauenswürdigen Domänen zu.</span><span class="sxs-lookup"><span data-stu-id="7eddc-109">Allow cross-origin requests only from domains you trust or control.</span></span> <span data-ttu-id="7eddc-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7eddc-110">For example:</span></span>

* <span data-ttu-id="7eddc-111">Ihre Website wird gehostet auf `http://www.example.com`</span><span class="sxs-lookup"><span data-stu-id="7eddc-111">Your site is hosted on `http://www.example.com`</span></span>
* <span data-ttu-id="7eddc-112">Ihre :::no-loc(SignalR)::: App wird gehostet auf `http://signalr.example.com`</span><span class="sxs-lookup"><span data-stu-id="7eddc-112">Your :::no-loc(SignalR)::: app is hosted on `http://signalr.example.com`</span></span>

<span data-ttu-id="7eddc-113">Cors sollte in der APP so konfiguriert werden :::no-loc(SignalR)::: , dass Sie nur den Ursprung zulässt `www.example.com` .</span><span class="sxs-lookup"><span data-stu-id="7eddc-113">CORS should be configured in the :::no-loc(SignalR)::: app to only allow the origin `www.example.com`.</span></span>

<span data-ttu-id="7eddc-114">Weitere Informationen zum Konfigurieren von cors finden Sie unter [Aktivieren von Cross-Origin-Anforderungen (cors)](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="7eddc-114">For more information on configuring CORS, see [Enable Cross-Origin Requests (CORS)](xref:security/cors).</span></span> <span data-ttu-id="7eddc-115">:::no-loc(SignalR):::**erfordert** die folgenden cors-Richtlinien:</span><span class="sxs-lookup"><span data-stu-id="7eddc-115">:::no-loc(SignalR)::: **requires** the following CORS policies:</span></span>

* <span data-ttu-id="7eddc-116">Hiermit werden bestimmte erwartete Ursprünge zugelassen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-116">Allow the specific expected origins.</span></span> <span data-ttu-id="7eddc-117">Es ist möglich, einen beliebigen Ursprung zuzulassen, aber er ist **nicht** sicher oder wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-117">Allowing any origin is possible but is **not** secure or recommended.</span></span>
* <span data-ttu-id="7eddc-118">HTTP `GET` -Methoden und `POST` müssen zulässig sein.</span><span class="sxs-lookup"><span data-stu-id="7eddc-118">HTTP methods `GET` and `POST` must be allowed.</span></span>
* <span data-ttu-id="7eddc-119">Anmelde Informationen müssen zulässig sein, damit :::no-loc(cookie)::: -basierte persistente Sitzungen ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="7eddc-119">Credentials must be allowed in order for :::no-loc(cookie):::-based sticky sessions to work correctly.</span></span> <span data-ttu-id="7eddc-120">Sie müssen auch dann aktiviert werden, wenn die Authentifizierung nicht verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7eddc-120">They must be enabled even when authentication isn't used.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7eddc-121">In 5,0 haben wir jedoch eine Option im typescript-Client bereitgestellt, um keine Anmelde Informationen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-121">However, in 5.0 we have provided an option in the TypeScript client to not use credentials.</span></span>
<span data-ttu-id="7eddc-122">Die Option, Anmelde Informationen nicht zu verwenden, sollte nur verwendet werden, wenn Sie 100% wissen, dass Anmelde Informationen wie z :::no-loc(Cookie)::: . b. in Ihrer APP nicht benötigt werden ( :::no-loc(cookie)::: e werden von Azure App Service verwendet, wenn mehrere Server für persistente Sitzungen verwendet werden).</span><span class="sxs-lookup"><span data-stu-id="7eddc-122">The option to not use credentials should only be used when you know 100% that credentials like :::no-loc(Cookie):::s are not needed in your app (:::no-loc(cookie):::s are used by azure app service when using multiple servers for sticky sessions).</span></span>

::: moniker-end

<span data-ttu-id="7eddc-123">Mit der folgenden cors-Richtlinie kann beispielsweise ein auf :::no-loc(SignalR)::: gehosteter Browser Client auf `https://example.com` die :::no-loc(SignalR)::: auf gehostete App zugreifen `https://signalr.example.com` :</span><span class="sxs-lookup"><span data-stu-id="7eddc-123">For example, the following CORS policy allows a :::no-loc(SignalR)::: browser client hosted on `https://example.com` to access the :::no-loc(SignalR)::: app hosted on `https://signalr.example.com`:</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of :::no-loc(SignalR):::.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a><span data-ttu-id="7eddc-124">WebSocket-Ursprungs Einschränkung</span><span class="sxs-lookup"><span data-stu-id="7eddc-124">WebSocket Origin Restriction</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7eddc-125">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7eddc-125">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7eddc-126">Informationen zur Ursprungs Einschränkung für websockets finden Sie unter [websockets Origin-Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).</span><span class="sxs-lookup"><span data-stu-id="7eddc-126">For origin restriction on WebSockets, read [WebSockets origin restriction](xref:fundamentals/websockets#websocket-origin-restriction).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7eddc-127">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="7eddc-127">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="7eddc-128">Für Browser gilt Folgendes **nicht** :</span><span class="sxs-lookup"><span data-stu-id="7eddc-128">Browsers do **not** :</span></span>

* <span data-ttu-id="7eddc-129">Ausführen von CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="7eddc-129">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="7eddc-130">Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind</span><span class="sxs-lookup"><span data-stu-id="7eddc-130">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="7eddc-131">Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-131">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="7eddc-132">Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="7eddc-132">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="7eddc-133">In ASP.net Core 2,1 und höher kann die Header Validierung mithilfe einer benutzerdefinierten Middleware **vor und der `Use:::no-loc(SignalR):::` Authentifizierungs Middleware** in erreicht werden `Configure` :</span><span class="sxs-lookup"><span data-stu-id="7eddc-133">In ASP.NET Core 2.1 and later, header validation can be achieved using a custom middleware placed **before `Use:::no-loc(SignalR):::`, and authentication middleware** in `Configure`:</span></span>

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> <span data-ttu-id="7eddc-134">Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-134">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="7eddc-135">Diese Header sollten **nicht** als Authentifizierungsmechanismus verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-135">These headers should **not** be used as an authentication mechanism.</span></span>

::: moniker-end

## <a name="connectionid"></a><span data-ttu-id="7eddc-136">ConnectionId</span><span class="sxs-lookup"><span data-stu-id="7eddc-136">ConnectionId</span></span>

<span data-ttu-id="7eddc-137">Das verfügbar machen `ConnectionId` von kann zu böswilligem Identitätswechsel führen, wenn die :::no-loc(SignalR)::: Server-oder Client Version ASP.net Core 2,2 oder früher ist.</span><span class="sxs-lookup"><span data-stu-id="7eddc-137">Exposing `ConnectionId` can lead to malicious impersonation if the :::no-loc(SignalR)::: server or client version is ASP.NET Core 2.2 or earlier.</span></span> <span data-ttu-id="7eddc-138">Wenn die :::no-loc(SignalR)::: Server-und die Client Version ASP.net Core 3,0 oder höher sind, `ConnectionToken` muss anstelle des `ConnectionId` geheim gehalten werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-138">If the :::no-loc(SignalR)::: server and client version are ASP.NET Core 3.0 or later, the `ConnectionToken` rather than the `ConnectionId` must be kept secret.</span></span> <span data-ttu-id="7eddc-139">Der `ConnectionToken` wird absichtlich nicht in einer API verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="7eddc-139">The `ConnectionToken` is purposely not exposed in any API.</span></span>  <span data-ttu-id="7eddc-140">Es kann schwierig sein, sicherzustellen, dass ältere :::no-loc(SignalR)::: Clients keine Verbindung mit dem Server herstellen. selbst wenn Ihre :::no-loc(SignalR)::: Server Version ASP.net Core 3,0 oder höher ist, sollte der nicht verfügbar gemacht `ConnectionId` werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-140">It can be difficult to ensure that older :::no-loc(SignalR)::: clients aren't connecting to the server, so even if your :::no-loc(SignalR)::: server version is ASP.NET Core 3.0 or later, the `ConnectionId` shouldn't be exposed.</span></span>

## <a name="access-token-logging"></a><span data-ttu-id="7eddc-141">Protokollierung von Zugriffs Token</span><span class="sxs-lookup"><span data-stu-id="7eddc-141">Access token logging</span></span>

<span data-ttu-id="7eddc-142">Wenn websockets oder Server-Sent Ereignisse verwendet werden, sendet der Browser Client das Zugriffs Token in der Abfrage Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="7eddc-142">When using WebSockets or Server-Sent Events, the browser client sends the access token in the query string.</span></span> <span data-ttu-id="7eddc-143">Der Empfang des Zugriffs Tokens über eine Abfrage Zeichenfolge ist im Allgemeinen sicher, wenn der Standard Header verwendet wird `Authorization` .</span><span class="sxs-lookup"><span data-stu-id="7eddc-143">Receiving the access token via query string is generally secure as using the standard `Authorization` header.</span></span> <span data-ttu-id="7eddc-144">Verwenden Sie immer HTTPS, um eine sichere End-to-End-Verbindung zwischen dem Client und dem Server sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-144">Always use HTTPS to ensure a secure end-to-end connection between the client and the server.</span></span> <span data-ttu-id="7eddc-145">Viele Webserver protokollieren die URL für jede Anforderung, einschließlich der Abfrage Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="7eddc-145">Many web servers log the URL for each request, including the query string.</span></span> <span data-ttu-id="7eddc-146">Durch das Protokollieren der URLs kann das Zugriffs Token protokolliert werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-146">Logging the URLs may log the access token.</span></span> <span data-ttu-id="7eddc-147">ASP.net Core protokolliert standardmäßig die URL für jede Anforderung, die die Abfrage Zeichenfolge enthält.</span><span class="sxs-lookup"><span data-stu-id="7eddc-147">ASP.NET Core logs the URL for each request by default, which will include the query string.</span></span> <span data-ttu-id="7eddc-148">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7eddc-148">For example:</span></span>

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

<span data-ttu-id="7eddc-149">Wenn Sie Bedenken haben, dass Sie diese Daten mit ihren Serverprotokollen protokollieren möchten, können Sie diese Protokollierung vollständig deaktivieren, indem Sie die Protokollierung `Microsoft.AspNetCore.Hosting` auf die `Warning` Ebene oder höher konfigurieren (diese Nachrichten werden auf der `Info` Ebene geschrieben).</span><span class="sxs-lookup"><span data-stu-id="7eddc-149">If you have concerns about logging this data with your server logs, you can disable this logging entirely by configuring the `Microsoft.AspNetCore.Hosting` logger to the `Warning` level or above (these messages are written at `Info` level).</span></span> <span data-ttu-id="7eddc-150">Weitere Informationen finden Sie unter [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) .</span><span class="sxs-lookup"><span data-stu-id="7eddc-150">For more information, see [Log Filtering](xref:fundamentals/logging/index#log-filtering) for more information.</span></span> <span data-ttu-id="7eddc-151">Wenn Sie trotzdem bestimmte Anforderungs Informationen protokollieren möchten, können Sie [eine Middleware schreiben](xref:fundamentals/middleware/write) , um die benötigten Daten zu protokollieren und den `access_token` Wert der Abfrage Zeichenfolge (sofern vorhanden) herauszufiltern.</span><span class="sxs-lookup"><span data-stu-id="7eddc-151">If you still want to log certain request information, you can [write a middleware](xref:fundamentals/middleware/write) to log the data you require and filter out the `access_token` query string value (if present).</span></span>

## <a name="exceptions"></a><span data-ttu-id="7eddc-152">Ausnahmen</span><span class="sxs-lookup"><span data-stu-id="7eddc-152">Exceptions</span></span>

<span data-ttu-id="7eddc-153">Ausnahmemeldungen werden generell als vertrauliche Daten angesehen, die nicht für einen Client offengelegt werden dürfen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-153">Exception messages are generally considered sensitive data that shouldn't be revealed to a client.</span></span> <span data-ttu-id="7eddc-154">Standardmäßig :::no-loc(SignalR)::: sendet nicht die Details einer Ausnahme, die von einer Hub-Methode ausgelöst wird, an den Client.</span><span class="sxs-lookup"><span data-stu-id="7eddc-154">By default, :::no-loc(SignalR)::: doesn't send the details of an exception thrown by a hub method to the client.</span></span> <span data-ttu-id="7eddc-155">Stattdessen erhält der Client eine generische Nachricht, die angibt, dass ein Fehler aufgetreten ist.</span><span class="sxs-lookup"><span data-stu-id="7eddc-155">Instead, the client receives a generic message indicating an error occurred.</span></span> <span data-ttu-id="7eddc-156">Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit [enabledetailederrors](xref:signalr/configuration#configure-server-options).</span><span class="sxs-lookup"><span data-stu-id="7eddc-156">Exception message delivery to the client can be overridden (for example in development or test) with [EnableDetailedErrors](xref:signalr/configuration#configure-server-options).</span></span> <span data-ttu-id="7eddc-157">Ausnahme Meldungen sollten in Produktions-apps nicht für den Client verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-157">Exception messages should not be exposed to the client in production apps.</span></span>

## <a name="buffer-management"></a><span data-ttu-id="7eddc-158">Pufferverwaltung</span><span class="sxs-lookup"><span data-stu-id="7eddc-158">Buffer management</span></span>

<span data-ttu-id="7eddc-159">:::no-loc(SignalR)::: verwendet Verbindungs Puffer für eingehende und ausgehende Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="7eddc-159">:::no-loc(SignalR)::: uses per-connection buffers to manage incoming and outgoing messages.</span></span> <span data-ttu-id="7eddc-160">Standardmäßig :::no-loc(SignalR)::: beschränkt diese Puffer auf 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7eddc-160">By default, :::no-loc(SignalR)::: limits these buffers to 32 KB.</span></span> <span data-ttu-id="7eddc-161">Die größte Nachricht, die ein Client oder Server senden kann, ist 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7eddc-161">The largest message a client or server can send is 32 KB.</span></span> <span data-ttu-id="7eddc-162">Der maximale Arbeitsspeicher, der von einer Verbindung für Nachrichten belegt wird, beträgt 32 KB.</span><span class="sxs-lookup"><span data-stu-id="7eddc-162">The maximum memory consumed by a connection for messages is 32 KB.</span></span> <span data-ttu-id="7eddc-163">Wenn die Nachrichten immer kleiner als 32 KB sind, können Sie den Grenzwert verringern:</span><span class="sxs-lookup"><span data-stu-id="7eddc-163">If your messages are always smaller than 32 KB, you can reduce the limit, which:</span></span>

* <span data-ttu-id="7eddc-164">Verhindert, dass ein Client eine größere Nachricht senden kann.</span><span class="sxs-lookup"><span data-stu-id="7eddc-164">Prevents a client from being able to send a larger message.</span></span>
* <span data-ttu-id="7eddc-165">Der Server muss niemals große Puffer zuordnen, um Nachrichten zu akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="7eddc-165">The server will never need to allocate large buffers to accept messages.</span></span>

<span data-ttu-id="7eddc-166">Wenn die Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-166">If your messages are larger than 32 KB, you can increase the limit.</span></span> <span data-ttu-id="7eddc-167">Das Erhöhen dieses Limits bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7eddc-167">Increasing this limit means:</span></span>

* <span data-ttu-id="7eddc-168">Der-Client kann bewirken, dass der Server große Speicherpuffer zuweist.</span><span class="sxs-lookup"><span data-stu-id="7eddc-168">The client can cause the server to allocate large memory buffers.</span></span>
* <span data-ttu-id="7eddc-169">Die Server Zuordnung großer Puffer verringert möglicherweise die Anzahl der gleichzeitigen Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-169">Server allocation of large buffers may reduce the number of concurrent connections.</span></span>

<span data-ttu-id="7eddc-170">Es gibt Grenzwerte für eingehende und ausgehende Nachrichten. beide können für das [httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) -Objekt konfiguriert werden, das in konfiguriert ist `MapHub` :</span><span class="sxs-lookup"><span data-stu-id="7eddc-170">There are limits for incoming and outgoing messages, both can be configured on the [HttpConnectionDispatcherOptions](xref:signalr/configuration#configure-server-options) object configured in `MapHub`:</span></span>

* <span data-ttu-id="7eddc-171">`ApplicationMaxBufferSize` stellt die maximale Anzahl von Bytes vom Client dar, die der Server puffert.</span><span class="sxs-lookup"><span data-stu-id="7eddc-171">`ApplicationMaxBufferSize` represents the maximum number of bytes from the client that the server buffers.</span></span> <span data-ttu-id="7eddc-172">Wenn der Client versucht, eine Nachricht zu senden, die über diesen Grenzwert hinausgeht, wird die Verbindung möglicherweise geschlossen.</span><span class="sxs-lookup"><span data-stu-id="7eddc-172">If the client attempts to send a message larger than this limit, the connection may be closed.</span></span>
* <span data-ttu-id="7eddc-173">`TransportMaxBufferSize` stellt die maximale Anzahl von Bytes dar, die vom Server gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="7eddc-173">`TransportMaxBufferSize` represents the maximum number of bytes the server can send.</span></span> <span data-ttu-id="7eddc-174">Wenn der Server versucht, eine Nachricht (einschließlich Rückgabewerte von hubmethoden) zu senden, die größer als dieser Grenzwert ist, wird eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="7eddc-174">If the server attempts to send a message (including return values from hub methods) larger than this limit, an exception will be thrown.</span></span>

<span data-ttu-id="7eddc-175">Durch Festlegen des Limits auf wird `0` der Grenzwert deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="7eddc-175">Setting the limit to `0` disables the limit.</span></span> <span data-ttu-id="7eddc-176">Durch das Entfernen des Limits kann ein Client eine beliebige Größe einer Nachricht senden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-176">Removing the limit allows a client to send a message of any size.</span></span> <span data-ttu-id="7eddc-177">Böswillige Clients, die große Nachrichten senden, können dazu führen, dass mehr Speicherplatz zugewiesen</span><span class="sxs-lookup"><span data-stu-id="7eddc-177">Malicious clients sending large messages can cause excess memory to be allocated.</span></span> <span data-ttu-id="7eddc-178">Durch eine übermäßige Speicherauslastung kann die Anzahl gleichzeitiger Verbindungen erheblich reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="7eddc-178">Excess memory usage can significantly reduce the number of concurrent connections.</span></span>
