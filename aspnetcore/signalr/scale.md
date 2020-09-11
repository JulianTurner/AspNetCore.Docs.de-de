---
title: ASP.net Core SignalR Produktions Hosting und-Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie Leistungs-und Skalierungsprobleme in apps vermeiden, die ASP.net Core verwenden SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
no-loc:
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
uid: signalr/scale
ms.openlocfilehash: bba965e14058663c3ed9c0f15afc6a8d78997aea
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009751"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="37290-103">ASP.net Core SignalR Hosting und Skalierung</span><span class="sxs-lookup"><span data-stu-id="37290-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="37290-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="37290-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="37290-105">In diesem Artikel werden die Überlegungen zu Hosting und Skalierung für apps mit hohem Datenverkehr erläutert, SignalR die ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="37290-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="37290-106">Persistente Sitzungen</span><span class="sxs-lookup"><span data-stu-id="37290-106">Sticky Sessions</span></span>

<span data-ttu-id="37290-107">SignalR erfordert, dass alle HTTP-Anforderungen für eine bestimmte Verbindung vom gleichen Server Prozess verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="37290-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="37290-108">Wenn SignalR in einer Serverfarm (mehrere Server) ausgeführt wird, müssen "persistente Sitzungen" verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="37290-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="37290-109">"Persistente Sitzungen" werden von einigen Lasten Ausgleichs Modulen auch als Sitzungs Affinität bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="37290-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="37290-110">Azure App Service mithilfe von [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) Anforderungen weiterleiten.</span><span class="sxs-lookup"><span data-stu-id="37290-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="37290-111">Wenn Sie die Einstellung "arr-Affinität" in ihrer Azure App Service aktivieren, werden "persistente Sitzungen" aktiviert.</span><span class="sxs-lookup"><span data-stu-id="37290-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="37290-112">Die einzigen Situationen, in denen keine persistenten Sitzungen erforderlich sind, sind:</span><span class="sxs-lookup"><span data-stu-id="37290-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="37290-113">Beim Hosting auf einem einzelnen Server in einem einzelnen Prozess.</span><span class="sxs-lookup"><span data-stu-id="37290-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="37290-114">Bei Verwendung des Azure- SignalR Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="37290-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="37290-115">Wenn alle Clients so konfiguriert sind, dass Sie **nur** websockets verwenden, **und** die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) in der Client Konfiguration aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="37290-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="37290-116">In allen anderen Fällen (einschließlich der Verwendung der redis-Rückwand) muss die Serverumgebung für persistente Sitzungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="37290-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="37290-117">Anleitungen zum Konfigurieren von Azure App Service für SignalR finden Sie unter <xref:signalr/publish-to-azure-web-app> .</span><span class="sxs-lookup"><span data-stu-id="37290-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="37290-118">TCP-Verbindungs Ressourcen</span><span class="sxs-lookup"><span data-stu-id="37290-118">TCP connection resources</span></span>

<span data-ttu-id="37290-119">Die Anzahl der gleichzeitigen TCP-Verbindungen, die ein Webserver unterstützen kann, ist begrenzt.</span><span class="sxs-lookup"><span data-stu-id="37290-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="37290-120">Standard-HTTP-Clients verwenden *kurzlebige* Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="37290-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="37290-121">Diese Verbindungen können geschlossen werden, wenn der Client in den Leerlauf wechselt und später erneut geöffnet wird.</span><span class="sxs-lookup"><span data-stu-id="37290-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="37290-122">Auf der anderen Seite ist eine SignalR Verbindung *persistent*.</span><span class="sxs-lookup"><span data-stu-id="37290-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="37290-123">SignalR Verbindungen bleiben auch dann geöffnet, wenn der Client in den Leerlauf wechselt.</span><span class="sxs-lookup"><span data-stu-id="37290-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="37290-124">In einer APP mit hohem Datenverkehr, die viele Clients bedient, können diese persistenten Verbindungen bewirken, dass Server die maximale Anzahl von Verbindungen erreichen.</span><span class="sxs-lookup"><span data-stu-id="37290-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="37290-125">Persistente Verbindungen verbrauchen auch zusätzlichen Arbeitsspeicher, um die einzelnen Verbindungen zu verfolgen.</span><span class="sxs-lookup"><span data-stu-id="37290-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="37290-126">Die intensive Verwendung von Verbindungs bezogenen Ressourcen von SignalR kann sich auf andere Web-Apps auswirken, die auf demselben Server gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="37290-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="37290-127">Wenn SignalR geöffnet wird und die letzten verfügbaren TCP-Verbindungen enthält, sind auch andere Web-Apps auf demselben Server nicht mehr verfügbar.</span><span class="sxs-lookup"><span data-stu-id="37290-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="37290-128">Wenn auf einem Server keine Verbindungen mehr auftreten, werden zufällige Socketfehler und Fehler beim Zurücksetzen der Verbindung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="37290-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="37290-129">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="37290-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="37290-130">Um zu verhindern SignalR , dass die Ressourcenverwendung in anderen Web-Apps zu Fehlern führt, führen SignalR Sie auf unterschiedlichen Servern aus als Ihre anderen Web-Apps</span><span class="sxs-lookup"><span data-stu-id="37290-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="37290-131">Um zu verhindern SignalR , dass die Ressourcenverwendung in einer APP zu Fehlern führt, Skalieren Sie horizontal hoch, SignalR um die Anzahl der Verbindungen zu begrenzen, die ein Server verarbeiten muss</span><span class="sxs-lookup"><span data-stu-id="37290-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="37290-132">Aufskalieren</span><span class="sxs-lookup"><span data-stu-id="37290-132">Scale out</span></span>

<span data-ttu-id="37290-133">Eine APP, die verwendet SignalR , muss alle zugehörigen Verbindungen nachverfolgen, wodurch Probleme für eine Serverfarm verursacht werden.</span><span class="sxs-lookup"><span data-stu-id="37290-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="37290-134">Fügen Sie einen Server hinzu, der neue Verbindungen erhält, die von den anderen Servern nicht bekannt sind.</span><span class="sxs-lookup"><span data-stu-id="37290-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="37290-135">SignalRAuf jedem Server im folgenden Diagramm sind z. b. die Verbindungen auf den anderen Servern nicht bekannt.</span><span class="sxs-lookup"><span data-stu-id="37290-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="37290-136">Wenn SignalR auf einem der Server eine Nachricht an alle Clients senden soll, wird die Nachricht nur an die Clients gesendet, die mit diesem Server verbunden sind.</span><span class="sxs-lookup"><span data-stu-id="37290-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![Skalierung:: NO-LOC (signalr)::: ohne Rückwand](scale/_static/scale-no-backplane.png)

<span data-ttu-id="37290-138">Die Optionen für die Lösung dieses Problems sind der [Azure- SignalR Dienst](#azure-signalr-service) und die [redis-Rückwand](#redis-backplane).</span><span class="sxs-lookup"><span data-stu-id="37290-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="37290-139">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="37290-139">Azure SignalR Service</span></span>

<span data-ttu-id="37290-140">Beim Azure- SignalR Dienst handelt es sich um einen Proxy und nicht um eine Backplane.</span><span class="sxs-lookup"><span data-stu-id="37290-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="37290-141">Jedes Mal, wenn ein Client eine Verbindung mit dem Server initiiert, wird der Client umgeleitet, um eine Verbindung mit dem Dienst herzustellen.</span><span class="sxs-lookup"><span data-stu-id="37290-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="37290-142">Dieser Prozess wird in der folgenden Abbildung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="37290-142">That process is illustrated in the following diagram:</span></span>

![Herstellen einer Verbindung mit Azure::: NO-LOC (signalr)::: Service](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="37290-144">Das Ergebnis ist, dass der Dienst alle Clientverbindungen verwaltet, während jeder Server nur eine kleine Konstante Anzahl von Verbindungen mit dem Dienst benötigt, wie im folgenden Diagramm dargestellt:</span><span class="sxs-lookup"><span data-stu-id="37290-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![Mit dem Dienst verbundene Clients, Server, die mit dem Dienst verbunden sind](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="37290-146">Diese Vorgehensweise zum horizontalen hochskalieren hat gegenüber der redis-Rückwand-Alternative mehrere Vorteile:</span><span class="sxs-lookup"><span data-stu-id="37290-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="37290-147">Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind nicht erforderlich, da Clients sofort an den Azure-Dienst umgeleitet werden, SignalR Wenn Sie eine Verbindung herstellen.</span><span class="sxs-lookup"><span data-stu-id="37290-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="37290-148">Eine SignalR App kann basierend auf der Anzahl der gesendeten Nachrichten horizontal hochskaliert werden, während der Azure- SignalR Dienst für eine beliebige Anzahl von Verbindungen skaliert wird.</span><span class="sxs-lookup"><span data-stu-id="37290-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service scales to handle any number of connections.</span></span> <span data-ttu-id="37290-149">Es könnten z. b. Tausende von Clients vorhanden sein. Wenn jedoch nur wenige Nachrichten pro Sekunde gesendet werden, SignalR muss die APP nicht auf mehrere Server horizontal hochskaliert werden, um die Verbindungen selbst zu bewältigen.</span><span class="sxs-lookup"><span data-stu-id="37290-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="37290-150">Eine SignalR App verwendet nicht wesentlich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR .</span><span class="sxs-lookup"><span data-stu-id="37290-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="37290-151">Aus diesen Gründen wird der Azure- SignalR Dienst für alle ASP.net Core- SignalR apps empfohlen, die in Azure gehostet werden, einschließlich APP Service, VMS und Containern.</span><span class="sxs-lookup"><span data-stu-id="37290-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="37290-152">Weitere Informationen finden Sie in [der SignalR Dokumentation zum Azure-Dienst](/azure/azure-signalr/signalr-overview).</span><span class="sxs-lookup"><span data-stu-id="37290-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="37290-153">Redis-Backplane</span><span class="sxs-lookup"><span data-stu-id="37290-153">Redis backplane</span></span>

<span data-ttu-id="37290-154">[Redis](https://redis.io/) ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher, der ein Messaging System mit einem Veröffentlichungs-/Abonnementmodell unterstützt.</span><span class="sxs-lookup"><span data-stu-id="37290-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="37290-155">Die SignalR redis-Rückwand verwendet die Pub/Sub-Funktion, um Nachrichten an andere Server weiterzuleiten.</span><span class="sxs-lookup"><span data-stu-id="37290-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="37290-156">Wenn ein Client eine Verbindung herstellt, werden die Verbindungsinformationen an die Backplane übermittelt.</span><span class="sxs-lookup"><span data-stu-id="37290-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="37290-157">Wenn ein Server eine Nachricht an alle Clients senden möchte, sendet er an die Backplane.</span><span class="sxs-lookup"><span data-stu-id="37290-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="37290-158">Die Rückwand kennt alle verbundenen Clients und die Server, auf denen Sie sich befinden.</span><span class="sxs-lookup"><span data-stu-id="37290-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="37290-159">Die Nachricht wird über die entsprechenden Server an alle Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="37290-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="37290-160">Dieser Vorgang wird in der folgenden Abbildung veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="37290-160">This process is illustrated in the following diagram:</span></span>

![Redis-Backplane, von einem Server an alle Clients gesendete Nachrichten](scale/_static/redis-backplane.png)

<span data-ttu-id="37290-162">Die redis-Rückwand ist der empfohlene Ansatz für horizontales Skalieren für apps, die in ihrer eigenen Infrastruktur gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="37290-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="37290-163">Wenn zwischen Ihrem Rechenzentrum und einem Azure-Rechenzentrum eine beträchtliche Verbindungs Wartezeit vorliegt, ist der Azure- SignalR Dienst möglicherweise nicht die praktische Option für lokale apps mit geringer Latenz oder hohen Durchsatzanforderungen.</span><span class="sxs-lookup"><span data-stu-id="37290-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="37290-164">Die SignalR zuvor notierten Vorteile des Azure-Dienstanbieter sind Nachteile der redis-Backplane:</span><span class="sxs-lookup"><span data-stu-id="37290-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="37290-165">Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind erforderlich, außer wenn **beide** der folgenden Punkte zutreffen:</span><span class="sxs-lookup"><span data-stu-id="37290-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="37290-166">Alle Clients sind so konfiguriert, dass Sie **nur** websockets verwenden.</span><span class="sxs-lookup"><span data-stu-id="37290-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="37290-167">Die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) ist in der Client Konfiguration aktiviert.</span><span class="sxs-lookup"><span data-stu-id="37290-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="37290-168">Nachdem eine Verbindung auf einem Server initiiert wurde, muss die Verbindung auf diesem Server bestehen.</span><span class="sxs-lookup"><span data-stu-id="37290-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="37290-169">Eine SignalR app muss basierend auf der Anzahl von Clients horizontal hochskaliert werden, auch wenn nur wenige Nachrichten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="37290-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="37290-170">Eine- SignalR App verwendet erheblich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR .</span><span class="sxs-lookup"><span data-stu-id="37290-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="37290-171">IIS-Einschränkungen für Windows-Client Betriebssystem</span><span class="sxs-lookup"><span data-stu-id="37290-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="37290-172">Windows 10 und Windows 8. x sind Client Betriebssysteme.</span><span class="sxs-lookup"><span data-stu-id="37290-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="37290-173">IIS unter Client Betriebssystemen hat eine Beschränkung von 10 gleichzeitigen Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="37290-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="37290-174">SignalRVerbindungen sind:</span><span class="sxs-lookup"><span data-stu-id="37290-174">SignalR's connections are:</span></span>

* <span data-ttu-id="37290-175">Vorübergehend und häufig wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="37290-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="37290-176">Wird **nicht** sofort verworfen, wenn Sie nicht mehr verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="37290-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="37290-177">Die vorangehenden Bedingungen machen es wahrscheinlich, dass die 10 Verbindungs Beschränkung für ein Client Betriebssystem erreicht wird.</span><span class="sxs-lookup"><span data-stu-id="37290-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="37290-178">Wenn ein Client Betriebssystem für die Entwicklung verwendet wird, wird Folgendes empfohlen:</span><span class="sxs-lookup"><span data-stu-id="37290-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="37290-179">Vermeiden Sie IIS.</span><span class="sxs-lookup"><span data-stu-id="37290-179">Avoid IIS.</span></span>
* <span data-ttu-id="37290-180">Verwenden Sie Kestrel oder IIS Express als Bereitstellungs Ziele.</span><span class="sxs-lookup"><span data-stu-id="37290-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="37290-181">Linux mit Nginx</span><span class="sxs-lookup"><span data-stu-id="37290-181">Linux with Nginx</span></span>

<span data-ttu-id="37290-182">Legen Sie die-und-Header des Proxys `Connection` `Upgrade` für websockets auf Folgendes fest SignalR :</span><span class="sxs-lookup"><span data-stu-id="37290-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="37290-183">Weitere Informationen finden Sie unter [NGINX as a WebSocket Proxy (NGINX als Websocket-Proxy)](https://www.nginx.com/blog/websocket-nginx/).</span><span class="sxs-lookup"><span data-stu-id="37290-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="37290-184">Drittanbieter- SignalR Rückwand-Anbieter</span><span class="sxs-lookup"><span data-stu-id="37290-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="37290-185">NCache</span><span class="sxs-lookup"><span data-stu-id="37290-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="37290-186">[Tro](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="37290-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="37290-187">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="37290-187">Next steps</span></span>

<span data-ttu-id="37290-188">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="37290-188">For more information, see the following resources:</span></span>

* [<span data-ttu-id="37290-189">Dokumentation zu Azure- SignalR Diensten</span><span class="sxs-lookup"><span data-stu-id="37290-189">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="37290-190">Einrichten einer redis-Rückwand</span><span class="sxs-lookup"><span data-stu-id="37290-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
