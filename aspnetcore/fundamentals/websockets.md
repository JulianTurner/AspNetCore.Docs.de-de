---
title: WebSockets-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/1/2020
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
uid: fundamentals/websockets
ms.openlocfilehash: 6edf2017cc889321cfb484e643b75711fd66004d
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058349"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="fcc71-103">WebSockets-Unterstützung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fcc71-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="fcc71-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="fcc71-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="fcc71-105">In diesem Artikel erfahren Sie, wie Sie mit WebSockets in ASP.NET beginnen.</span><span class="sxs-lookup"><span data-stu-id="fcc71-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="fcc71-106">Bei [WebSockets](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) handelt es sich um ein Protokoll, das bidirektionale persistente Kommunikationskanäle über TCP-Verbindungen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="fcc71-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="fcc71-107">Es wird in Apps verwendet, die von schneller Kommunikation in Echtzeit profitieren, z.B. Chats, Dashboards und Spiele-Apps.</span><span class="sxs-lookup"><span data-stu-id="fcc71-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="fcc71-108">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="fcc71-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="fcc71-109">[Laufvergleich](#sample-app).</span><span class="sxs-lookup"><span data-stu-id="fcc71-109">[How to run](#sample-app).</span></span>

## SignalR

<span data-ttu-id="fcc71-110">[ASP.NET Core SignalR](xref:signalr/introduction) ist eine Bibliothek, die das Hinzufügen von Echtzeit-Webfunktionalität zu Apps erleichtert.</span><span class="sxs-lookup"><span data-stu-id="fcc71-110">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="fcc71-111">Sie verwendet wenn möglich immer WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fcc71-111">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="fcc71-112">Für die meisten Anwendungen empfehlen wir SignalR über RAW-WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fcc71-112">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="fcc71-113">SignalR stellt ein Transportfallback für Umgebungen bereit, in denen WebSockets nicht verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="fcc71-113">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="fcc71-114">Darüber hinaus bietet es ein einfaches App-Modell für Remoteprozeduraufrufe.</span><span class="sxs-lookup"><span data-stu-id="fcc71-114">It also provides a basic remote procedure call app model.</span></span> <span data-ttu-id="fcc71-115">In den meisten Szenarien hat SignalR außerdem keinen signifikanten Leistungsnachteil gegenüber der Verwendung von RAW-WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fcc71-115">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

<span data-ttu-id="fcc71-116">Für einige Apps stellt [gRPC in .NET](xref:grpc/index) eine Alternative zu WebSockets dar.</span><span class="sxs-lookup"><span data-stu-id="fcc71-116">For some apps, [gRPC on .NET](xref:grpc/index) provides an alternative to WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fcc71-117">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="fcc71-117">Prerequisites</span></span>

* <span data-ttu-id="fcc71-118">Jedes Betriebssystem, das ASP.NET Core unterstützt:</span><span class="sxs-lookup"><span data-stu-id="fcc71-118">Any OS that supports ASP.NET Core:</span></span>  
  * <span data-ttu-id="fcc71-119">Windows 7/Windows Server 2008 und höher</span><span class="sxs-lookup"><span data-stu-id="fcc71-119">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="fcc71-120">Linux</span><span class="sxs-lookup"><span data-stu-id="fcc71-120">Linux</span></span>
  * <span data-ttu-id="fcc71-121">macOS</span><span class="sxs-lookup"><span data-stu-id="fcc71-121">macOS</span></span>  
* <span data-ttu-id="fcc71-122">Wenn die App unter Windows mit IIS ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="fcc71-122">If the app runs on Windows with IIS:</span></span>
  * <span data-ttu-id="fcc71-123">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="fcc71-123">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="fcc71-124">IIS 8/IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="fcc71-124">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="fcc71-125">WebSockets müssen aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="fcc71-125">WebSockets must be enabled.</span></span> <span data-ttu-id="fcc71-126">Weitere Informationen finden Sie im Abschnitt [Unterstützung für IIS und IIS Express](#iisiis-express-support).</span><span class="sxs-lookup"><span data-stu-id="fcc71-126">See the [IIS/IIS Express support](#iisiis-express-support) section.</span></span>  
* <span data-ttu-id="fcc71-127">Wenn die App unter [HTTP.sys](xref:fundamentals/servers/httpsys) ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="fcc71-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>
  * <span data-ttu-id="fcc71-128">Windows 8/Windows Server 2012 und höher</span><span class="sxs-lookup"><span data-stu-id="fcc71-128">Windows 8 / Windows Server 2012 or later</span></span>
* <span data-ttu-id="fcc71-129">Weitere Informationen zu unterstützten Browsern finden Sie unter https://caniuse.com/#feat=websockets.</span><span class="sxs-lookup"><span data-stu-id="fcc71-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

## <a name="configure-the-middleware"></a><span data-ttu-id="fcc71-130">Konfigurieren der Middleware</span><span class="sxs-lookup"><span data-stu-id="fcc71-130">Configure the middleware</span></span>

<span data-ttu-id="fcc71-131">Fügen Sie die WebSockets-Middleware zur `Configure`-Methode der `Startup`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="fcc71-131">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="fcc71-132">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="fcc71-132">The following settings can be configured:</span></span>

* <span data-ttu-id="fcc71-133">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="fcc71-133">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="fcc71-134">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-134">The default is two minutes.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fcc71-135">Sie können die folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="fcc71-135">The following settings can be configured:</span></span>

* <span data-ttu-id="fcc71-136">`KeepAliveInterval`: Legt fest, wie oft Ping-Frames an den Client gesendet werden, um sicherzustellen, dass Proxys die Verbindung aufrechterhalten</span><span class="sxs-lookup"><span data-stu-id="fcc71-136">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="fcc71-137">Der Standardwert beträgt zwei Minuten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-137">The default is two minutes.</span></span>
* <span data-ttu-id="fcc71-138">`AllowedOrigins` – Eine Liste der zulässigen Origin-Headerwerte für WebSocket-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="fcc71-138">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="fcc71-139">Alle Ursprünge sind standardmäßig zulässig.</span><span class="sxs-lookup"><span data-stu-id="fcc71-139">By default, all origins are allowed.</span></span> <span data-ttu-id="fcc71-140">Weitere Informationen finden Sie unter „Beschränkung von WebSocket-Ursprüngen“ weiter unten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-140">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="fcc71-141">Akzeptieren der Anforderungen von WebSocket</span><span class="sxs-lookup"><span data-stu-id="fcc71-141">Accept WebSocket requests</span></span>

<span data-ttu-id="fcc71-142">Prüfen Sie zu einem späteren Zeitpunkt im Lebenszyklus einer Anforderung (z.B. später in der `Configure`-Methode oder in einer Aktionsmethode), ob es sich um eine WebSocket-Anforderung handelt, und akzeptieren Sie diese.</span><span class="sxs-lookup"><span data-stu-id="fcc71-142">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="fcc71-143">Das folgende Beispiel ist ein späterer Auszug der `Configure`-Methode.</span><span class="sxs-lookup"><span data-stu-id="fcc71-143">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="fcc71-144">WebSocket-Anforderungen können bei jeder URL eingehen. Dieser Beispielcode akzeptiert jedoch nur Anforderungen für `/ws`.</span><span class="sxs-lookup"><span data-stu-id="fcc71-144">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="fcc71-145">Bei Verwendung eines WebSockets **muss** die Middlewarepipeline während der gesamten Dauer der Verbindung ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-145">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="fcc71-146">Wenn Sie versuchen, eine WebSocket-Nachricht zu senden oder zu empfangen, nachdem die Middlewarepipeline beendet wurde, erhalten Sie möglicherweise eine Ausnahme wie die folgende:</span><span class="sxs-lookup"><span data-stu-id="fcc71-146">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="fcc71-147">Wenn Sie einen Hintergrunddienst zum Schreiben von Daten an ein WebSocket verwenden, stellen Sie sicher, dass die Middlewarepipeline dauerhaft ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-147">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="fcc71-148">Verwenden Sie dafür ein <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span><span class="sxs-lookup"><span data-stu-id="fcc71-148">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="fcc71-149">Übergeben Sie die `TaskCompletionSource` an Ihren Hintergrunddienst, und lassen Sie sie <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> aufrufen, wenn Sie das WebSocket beenden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-149">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="fcc71-150">Verwenden Sie dann `await` für die <xref:System.Threading.Tasks.TaskCompletionSource%601.Task>-Eigenschaft während der Anforderung, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="fcc71-150">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup2.cs?name=AcceptWebSocket)]

<span data-ttu-id="fcc71-151">Die Ausnahme „WebSocket closed“ kann auch auftreten, wenn die Rückkehr von einer Aktionsmethode zu früh erfolgt.</span><span class="sxs-lookup"><span data-stu-id="fcc71-151">The WebSocket closed exception can also happen when returning too soon from an action method.</span></span> <span data-ttu-id="fcc71-152">Wenn Sie einen Socket in einer Aktionsmethode akzeptieren, warten Sie vor dem Zurückkehren von der Aktionsmethode, bis der Code, der den Socket verwendet, abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="fcc71-152">When accepting a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="fcc71-153">Verwenden Sie nie `Task.Wait`, `Task.Result` oder ähnliche Blockierungsaufrufe, um auf den Abschluss des Sockets zu warten, da dies zu schwerwiegenden Threadingproblemen führen kann.</span><span class="sxs-lookup"><span data-stu-id="fcc71-153">Never use `Task.Wait`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="fcc71-154">Verwenden Sie immer `await`.</span><span class="sxs-lookup"><span data-stu-id="fcc71-154">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="fcc71-155">Senden und Empfangen von Nachrichten</span><span class="sxs-lookup"><span data-stu-id="fcc71-155">Send and receive messages</span></span>

<span data-ttu-id="fcc71-156">Die `AcceptWebSocketAsync`-Methode ändert die TCP-Verbindung in eine WebSocket-Verbindung und stellt ein [WebSocket](/dotnet/core/api/system.net.websockets.websocket)-Objekt bereit.</span><span class="sxs-lookup"><span data-stu-id="fcc71-156">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="fcc71-157">Verwenden Sie das `WebSocket`-Objekt, um Nachrichten zu senden und zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="fcc71-157">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="fcc71-158">Der weiter oben gezeigte Code, der WebSocket-Anforderungen akzeptiert, übergibt das `WebSocket`-Objekt an eine `Echo`-Methode.</span><span class="sxs-lookup"><span data-stu-id="fcc71-158">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="fcc71-159">Der Code empfängt eine Nachricht und sendet diese umgehend wieder zurück.</span><span class="sxs-lookup"><span data-stu-id="fcc71-159">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="fcc71-160">Nachrichten werden in einer Schleife gesendet und empfangen, bis der Client die Verbindung schließt:</span><span class="sxs-lookup"><span data-stu-id="fcc71-160">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="fcc71-161">Wenn Sie die WebSocket-Verbindung vor Beginn der Schleife akzeptieren, endet die Middlewarepipeline.</span><span class="sxs-lookup"><span data-stu-id="fcc71-161">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="fcc71-162">Wenn Sie das Socket schließen, wird die Pipeline entladen.</span><span class="sxs-lookup"><span data-stu-id="fcc71-162">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="fcc71-163">Das bedeutet, dass sich die Anforderung in der Pipeline nicht mehr weiter bewegt, wenn der WebSocket akzeptiert wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-163">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="fcc71-164">Wenn die Schleife beendet und der Socket geschlossen ist, wird die Anforderung in der Pipeline weiter verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="fcc71-164">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="fcc71-165">Behandeln der Trennung der Verbindung mit dem Client</span><span class="sxs-lookup"><span data-stu-id="fcc71-165">Handle client disconnects</span></span>

<span data-ttu-id="fcc71-166">Der Server wird nicht automatisch informiert, wenn die Verbindung mit dem Client wegen Konnektivitätsverlust getrennt wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-166">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="fcc71-167">Der Server empfängt nur dann eine Trennungsnachricht, wenn der Client sie sendet, was bei Verlust der Verbindung nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="fcc71-167">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="fcc71-168">Wenn Sie Maßnahmen ergreifen möchten, wenn dies der Fall ist, legen Sie ein Timeout fest für den Fall, dass innerhalb eines bestimmten Zeitfensters keine Eingabe vom Client empfangen wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-168">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="fcc71-169">Wenn der Client nicht immer Nachrichten sendet und Sie kein Timeout festlegen möchten, nur weil die Verbindung in den Leerlauf übergeht, lassen Sie den Client einen Timer verwenden, um alle X Sekunden eine Ping-Nachricht zu senden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-169">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="fcc71-170">Wenn auf dem Server nicht innerhalb von 2\*X Sekunden nach einer Nachricht die nächste eingeht, beenden Sie die Verbindung, und melden Sie, dass der Client die Verbindung getrennt hat.</span><span class="sxs-lookup"><span data-stu-id="fcc71-170">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="fcc71-171">Warten Sie für den doppelten Zeitraum des erwarteten Zeitintervalls, um zusätzliche Zeit für Netzwerkverzögerungen einzuräumen, die die Ping-Nachricht aufhalten könnten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-171">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="fcc71-172">Beschränkung von WebSocket-Ursprüngen</span><span class="sxs-lookup"><span data-stu-id="fcc71-172">WebSocket origin restriction</span></span>

<span data-ttu-id="fcc71-173">Der von CORS erzeugte Schutz gilt nicht für WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fcc71-173">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="fcc71-174">Für Browser gilt Folgendes **nicht**:</span><span class="sxs-lookup"><span data-stu-id="fcc71-174">Browsers do **not**:</span></span>

* <span data-ttu-id="fcc71-175">Ausführen von CORS-Preflightanforderungen</span><span class="sxs-lookup"><span data-stu-id="fcc71-175">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="fcc71-176">Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind</span><span class="sxs-lookup"><span data-stu-id="fcc71-176">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="fcc71-177">Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-177">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="fcc71-178">Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.</span><span class="sxs-lookup"><span data-stu-id="fcc71-178">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="fcc71-179">Wenn Sie Ihren Server unter „https://server.com“ und Ihren Client unter „https://client.com“ hosten, fügen Sie „https://client.com“ zur Liste `AllowedOrigins` hinzu, damit sie von WebSockets überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-179">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="fcc71-180">Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-180">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="fcc71-181">Verwenden Sie diese Header **nicht** als Authentifizierungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="fcc71-181">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="fcc71-182">Unterstützung für IIS und IIS Express</span><span class="sxs-lookup"><span data-stu-id="fcc71-182">IIS/IIS Express support</span></span>

<span data-ttu-id="fcc71-183">In Windows Server 2012 oder höher und in Windows 8 oder höher mit IIS 8 oder IIS Express 8 oder höher ist die Unterstützung für das WebSocket-Protokoll enthalten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-183">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc71-184">WebSockets sind immer aktiviert, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-184">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="fcc71-185">Aktivieren von WebSockets in IIS</span><span class="sxs-lookup"><span data-stu-id="fcc71-185">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="fcc71-186">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows Server 2012 oder höher:</span><span class="sxs-lookup"><span data-stu-id="fcc71-186">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="fcc71-187">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-187">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="fcc71-188">Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-188">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="fcc71-189">Klicken Sie auf **Rollenbasierte oder featurebasierte Installation**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-189">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="fcc71-190">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-190">Select **Next**.</span></span>
1. <span data-ttu-id="fcc71-191">Wählen Sie den entsprechenden Server aus (standardmäßig ist der lokale Server ausgewählt).</span><span class="sxs-lookup"><span data-stu-id="fcc71-191">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="fcc71-192">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-192">Select **Next**.</span></span>
1. <span data-ttu-id="fcc71-193">Erweitern Sie **Webserver (IIS)** in der Struktur **Rollen**, und erweitern Sie **Webserver** und anschließend **Anwendungsentwicklung**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-193">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="fcc71-194">Wählen Sie **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="fcc71-194">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="fcc71-195">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-195">Select **Next**.</span></span>
1. <span data-ttu-id="fcc71-196">Wenn keine zusätzlichen Features erforderlich sind, klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-196">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="fcc71-197">Klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-197">Select **Install**.</span></span>
1. <span data-ttu-id="fcc71-198">Wenn die Installation abgeschlossen ist, klicken Sie auf **Schließen**, um den Assistenten zu beenden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-198">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="fcc71-199">So aktivieren Sie die Unterstützung für das WebSocket-Protokoll unter Windows 8 oder höher:</span><span class="sxs-lookup"><span data-stu-id="fcc71-199">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="fcc71-200">Diese Schritte sind nicht erforderlich, wenn Sie IIS Express verwenden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-200">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="fcc71-201">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="fcc71-201">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="fcc71-202">Öffnen Sie die folgenden Knoten: **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-202">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="fcc71-203">Wählen Sie das Feature **WebSocket-Protokoll** aus.</span><span class="sxs-lookup"><span data-stu-id="fcc71-203">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="fcc71-204">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="fcc71-204">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="fcc71-205">Deaktivieren von WebSocket bei Verwendung von „socket.io“ in „Node.js“</span><span class="sxs-lookup"><span data-stu-id="fcc71-205">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="fcc71-206">Wenn Sie die WebSocket-Unterstützung in [socket.io](https://socket.io/) in [Node.js](https://nodejs.org/) verwenden, deaktivieren Sie das IIS-WebSocket-Standardmodul mithilfe des `webSocket`-Elements in *web.config* oder *applicationHost.config*. Wenn dieser Schritt nicht durchgeführt wird, versucht das IIS-WebSocket-Modul, die WebSocket-Kommunikation statt Node.js und der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="fcc71-206">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="fcc71-207">Beispiel-App</span><span class="sxs-lookup"><span data-stu-id="fcc71-207">Sample app</span></span>

<span data-ttu-id="fcc71-208">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples), die in diesem Artikel verwendet wird, ist eine Echo-App.</span><span class="sxs-lookup"><span data-stu-id="fcc71-208">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="fcc71-209">Sie verfügt über eine Webseite, die WebSocket-Verbindungen herstellt. Der Server schickt alle empfangenen Nachrichten zurück an den Client.</span><span class="sxs-lookup"><span data-stu-id="fcc71-209">It has a webpage that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="fcc71-210">Die Beispiel-App ist nicht so konfiguriert, dass sie mit IIS Express über Visual Studio ausgeführt wird. Führen Sie die App daher mit [`dotnet run`](/dotnet/core/tools/dotnet-run) in einer Befehlsshell aus, und navigieren Sie in einem Browser zu `http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="fcc71-210">The sample app isn't configured to run from Visual Studio with IIS Express, so run the app in a command shell with [`dotnet run`](/dotnet/core/tools/dotnet-run) and navigate in a browser to `http://localhost:5000`.</span></span> <span data-ttu-id="fcc71-211">Auf der Webseite wird der Verbindungsstatus angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fcc71-211">The webpage shows the connection status:</span></span>

![Ursprünglicher Status der Webseite vor der WebSocket-Verbindung](websockets/_static/start.png)

<span data-ttu-id="fcc71-213">Klicken Sie auf **Connect** (Verbinden), um eine WebSocket-Anforderung an die gezeigte URL zu senden.</span><span class="sxs-lookup"><span data-stu-id="fcc71-213">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="fcc71-214">Geben Sie einen Testtext ein, und klicken Sie auf **Send** (Senden).</span><span class="sxs-lookup"><span data-stu-id="fcc71-214">Enter a test message and select **Send**.</span></span> <span data-ttu-id="fcc71-215">Wenn dies abgeschlossen ist, klicken Sie auf **Close Socket** (Socket schließen).</span><span class="sxs-lookup"><span data-stu-id="fcc71-215">When done, select **Close Socket**.</span></span> <span data-ttu-id="fcc71-216">Der Abschnitt **Kommunikationsprotokoll** meldet jede open-, send- und close-Aktion, wenn diese durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="fcc71-216">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![Endzustand der Webseite nach dem Senden und Empfangen der WebSocket-Verbindung und Testnachrichten](websockets/_static/end.png)
