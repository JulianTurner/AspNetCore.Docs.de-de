---
title: 'Problembehandlung bei ASP.net Core SignalR Verbindung'
author: bradygaster
description: 'Problembehandlung bei der ASP.net Core SignalR Verbindung.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059610"
---
# <a name="troubleshoot-connection-errors"></a><span data-ttu-id="05d97-103">Beheben von Verbindungsproblemen</span><span class="sxs-lookup"><span data-stu-id="05d97-103">Troubleshoot connection errors</span></span>

<span data-ttu-id="05d97-104">Dieser Abschnitt enthält Hilfe bei Fehlern, die auftreten können, wenn Sie versuchen, eine Verbindung mit einem ASP.net Core SignalR Hub herzustellen.</span><span class="sxs-lookup"><span data-stu-id="05d97-104">This section provides help with errors that can occur when trying to establish a connection to a ASP.NET Core SignalR hub.</span></span>

### <a name="response-code-404"></a><span data-ttu-id="05d97-105">Antwort Code 404</span><span class="sxs-lookup"><span data-stu-id="05d97-105">Response code 404</span></span>

<span data-ttu-id="05d97-106">Bei Verwendung von websockets und `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="05d97-106">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* <span data-ttu-id="05d97-107">Wenn Sie mehrere Server ohne persistente Sitzungen verwenden, kann die Verbindung auf einem Server gestartet und dann zu einem anderen Server gewechselt werden.</span><span class="sxs-lookup"><span data-stu-id="05d97-107">When using multiple servers without sticky sessions, the connection can start on one server and then switch to another server.</span></span> <span data-ttu-id="05d97-108">Der andere Server kennt die vorherige Verbindung nicht.</span><span class="sxs-lookup"><span data-stu-id="05d97-108">The other server is not aware of the previous connection.</span></span>
* <span data-ttu-id="05d97-109">Vergewissern Sie sich, dass der Client eine Verbindung mit dem richtigen Endpunkt herstellt.</span><span class="sxs-lookup"><span data-stu-id="05d97-109">Verify the client is connecting to the correct endpoint.</span></span> <span data-ttu-id="05d97-110">Beispielsweise wird der Server unter gehostet, `http://127.0.0.1:5000/hub/myHub` und der Client versucht, eine Verbindung mit herzustellen `http://127.0.0.1:5000/myHub` .</span><span class="sxs-lookup"><span data-stu-id="05d97-110">For example, the server is hosted at `http://127.0.0.1:5000/hub/myHub` and client is trying to connect to `http://127.0.0.1:5000/myHub`.</span></span>
* <span data-ttu-id="05d97-111">Wenn die Verbindung die ID verwendet und zu lange dauert, bis nach der Aushandlung eine Anforderung an den Server gesendet wird, wird der Server:</span><span class="sxs-lookup"><span data-stu-id="05d97-111">If the connection uses the ID and takes too long to send a request to the server after the negotiate, the server:</span></span>

  * <span data-ttu-id="05d97-112">Löscht die ID.</span><span class="sxs-lookup"><span data-stu-id="05d97-112">Deletes the ID.</span></span>
  * <span data-ttu-id="05d97-113">Gibt 404 zurück.</span><span class="sxs-lookup"><span data-stu-id="05d97-113">Returns a 404.</span></span>

### <a name="response-code-400-or-503"></a><span data-ttu-id="05d97-114">Antwort Code 400 oder 503</span><span class="sxs-lookup"><span data-stu-id="05d97-114">Response code 400 or 503</span></span>

<span data-ttu-id="05d97-115">Für den folgenden Fehler:</span><span class="sxs-lookup"><span data-stu-id="05d97-115">For the following error:</span></span>

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

<span data-ttu-id="05d97-116">Dieser Fehler wird normalerweise dadurch verursacht, dass ein Client nur den websockets-Transport verwendet, aber das websockets-Protokoll auf dem Server nicht aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="05d97-116">This error is usually caused by a client using only the WebSockets transport but the WebSockets protocol is not enabled on the server.</span></span>

### <a name="response-code-307"></a><span data-ttu-id="05d97-117">Antwort Code 307</span><span class="sxs-lookup"><span data-stu-id="05d97-117">Response code 307</span></span>

<span data-ttu-id="05d97-118">Bei Verwendung von websockets und `skipNegotiation = true`</span><span class="sxs-lookup"><span data-stu-id="05d97-118">When using WebSockets and `skipNegotiation = true`</span></span>
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

<span data-ttu-id="05d97-119">Dieser Fehler kann auch während der Aushandlungs Anforderung auftreten.</span><span class="sxs-lookup"><span data-stu-id="05d97-119">This error can also happen during the negotiate request.</span></span>

<span data-ttu-id="05d97-120">Häufige Ursache:</span><span class="sxs-lookup"><span data-stu-id="05d97-120">Common cause:</span></span>
* <span data-ttu-id="05d97-121">Die APP ist so konfiguriert, dass Sie HTTPS durch Aufrufen von `UseHttpsRedirection` in erzwingen `Startup` oder HTTPS per URL-Rewrite-Regel erzwingt.</span><span class="sxs-lookup"><span data-stu-id="05d97-121">App is configured to enforce HTTPS by calling `UseHttpsRedirection` in `Startup`, or enforces HTTPS via URL rewrite rule.</span></span>

<span data-ttu-id="05d97-122">Mögliche Lösung:</span><span class="sxs-lookup"><span data-stu-id="05d97-122">Possible solution:</span></span>
* <span data-ttu-id="05d97-123">Ändern Sie die URL auf der Clientseite von "http" in "https".</span><span class="sxs-lookup"><span data-stu-id="05d97-123">Change the URL on the client side from "http" to "https".</span></span> `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a><span data-ttu-id="05d97-124">Antwort Code 405</span><span class="sxs-lookup"><span data-stu-id="05d97-124">Response code 405</span></span>

<span data-ttu-id="05d97-125">HTTP-Statuscode 405-Methode nicht zulässig</span><span class="sxs-lookup"><span data-stu-id="05d97-125">Http status code 405 - Method Not Allowed</span></span>

* <span data-ttu-id="05d97-126">Für die APP ist [cors](xref:signalr/security#cross-origin-resource-sharing) nicht aktiviert</span><span class="sxs-lookup"><span data-stu-id="05d97-126">The app doesn't have [CORS](xref:signalr/security#cross-origin-resource-sharing) enabled</span></span>

### <a name="response-code-0"></a><span data-ttu-id="05d97-127">Antwort Code 0</span><span class="sxs-lookup"><span data-stu-id="05d97-127">Response code 0</span></span>

<span data-ttu-id="05d97-128">HTTP-Statuscode 0 (in der Regel ein [cors](xref:signalr/security#cross-origin-resource-sharing) -Problem), wird kein Statuscode angegeben.</span><span class="sxs-lookup"><span data-stu-id="05d97-128">Http status code 0 - Usually a [CORS](xref:signalr/security#cross-origin-resource-sharing) issue, no status code is given</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* <span data-ttu-id="05d97-129">Fügen Sie die erwarteten Ursprünge hinzu. `.WithOrigins(...)`</span><span class="sxs-lookup"><span data-stu-id="05d97-129">Add the expected origins to `.WithOrigins(...)`</span></span>

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* <span data-ttu-id="05d97-130">Fügen Sie die `.AllowCredentials()` cors-Richtlinie hinzu.</span><span class="sxs-lookup"><span data-stu-id="05d97-130">Add `.AllowCredentials()` to your CORS policy.</span></span> <span data-ttu-id="05d97-131">`.AllowAnyOrigin()`Oder `.WithOrigins("*")` mit dieser Option kann nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="05d97-131">Cannot use `.AllowAnyOrigin()` or `.WithOrigins("*")` with this option</span></span>

### <a name="response-code-413"></a><span data-ttu-id="05d97-132">Antwort Code 413</span><span class="sxs-lookup"><span data-stu-id="05d97-132">Response code 413</span></span>

<span data-ttu-id="05d97-133">HTTP-Statuscode 413-Nutzlast zu groß</span><span class="sxs-lookup"><span data-stu-id="05d97-133">Http status code 413 - Payload Too Large</span></span>

<span data-ttu-id="05d97-134">Dies wird häufig durch ein Zugriffs Token verursacht, das über 4 KB ist.</span><span class="sxs-lookup"><span data-stu-id="05d97-134">This is often caused by having an access token that is over 4k.</span></span>

* <span data-ttu-id="05d97-135">Wenn Sie den Azure- SignalR Dienst verwenden, verringern Sie die Tokengröße, indem Sie die Ansprüche anpassen, die über den Dienst gesendet werden:</span><span class="sxs-lookup"><span data-stu-id="05d97-135">If using the Azure SignalR Service, reduce the token size by customizing the claims being sent through the Service with:</span></span>
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a><span data-ttu-id="05d97-136">Vorübergehende Netzwerkfehler</span><span class="sxs-lookup"><span data-stu-id="05d97-136">Transient network failures</span></span>

<span data-ttu-id="05d97-137">Vorübergehende Netzwerkfehler können die SignalR Verbindung schließen.</span><span class="sxs-lookup"><span data-stu-id="05d97-137">Transient network failures may close the SignalR connection.</span></span> <span data-ttu-id="05d97-138">Der Server interpretiert die geschlossene Verbindung möglicherweise als eine ordnungsgemäße Client Verbindung.</span><span class="sxs-lookup"><span data-stu-id="05d97-138">The server may interpret the closed connection as a graceful client disconnect.</span></span> <span data-ttu-id="05d97-139">Wenn Sie mehr darüber erfahren möchten, warum ein Client in diesen Fällen getrennt ist, [erfassen Sie Protokolle vom Client und Server](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="05d97-139">To get more info on why a client disconnected in those cases [gather logs from the client and server](xref:signalr/diagnostics).</span></span>