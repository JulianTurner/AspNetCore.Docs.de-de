---
title: Hosten und Bereitstellen von ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor Server-App mit ASP.NET Core hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2020
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
uid: blazor/host-and-deploy/server
ms.openlocfilehash: 75682171a59a610a24364778616774c49257d2ad
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100279848"
---
# <a name="host-and-deploy-blazor-server"></a><span data-ttu-id="b3ca5-103">Hosten und Bereitstellen Blazor Server</span><span class="sxs-lookup"><span data-stu-id="b3ca5-103">Host and deploy Blazor Server</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="b3ca5-104">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="b3ca5-104">Host configuration values</span></span>

<span data-ttu-id="b3ca5-105">[Blazor Server-Apps](xref:blazor/hosting-models#blazor-server) können [generische Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-105">[Blazor Server apps](xref:blazor/hosting-models#blazor-server) can accept [Generic Host configuration values](xref:fundamentals/host/generic-host#host-configuration).</span></span>

## <a name="deployment"></a><span data-ttu-id="b3ca5-106">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="b3ca5-106">Deployment</span></span>

<span data-ttu-id="b3ca5-107">Mit dem [Blazor Server-Hostingmodell](xref:blazor/hosting-models#blazor-server) wird Blazor in einer ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-107">Using the [Blazor Server hosting model](xref:blazor/hosting-models#blazor-server), Blazor is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="b3ca5-108">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-108">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="b3ca5-109">Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-109">A web server capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="b3ca5-110">Visual Studio enthält die Projektvorlage der **Blazor Server-App** (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [`dotnet new`](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-110">Visual Studio includes the **Blazor Server App** project template (`blazorserverside` template when using the [`dotnet new`](/dotnet/core/tools/dotnet-new) command).</span></span>

## <a name="scalability"></a><span data-ttu-id="b3ca5-111">Skalierbarkeit</span><span class="sxs-lookup"><span data-stu-id="b3ca5-111">Scalability</span></span>

<span data-ttu-id="b3ca5-112">Planen Sie eine Bereitstellung, um die verfügbare Infrastruktur für eine Blazor Server-App optimal zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-112">Plan a deployment to make the best use of the available infrastructure for a Blazor Server app.</span></span> <span data-ttu-id="b3ca5-113">Sehen Sie sich die folgenden Ressourcen an, um die Skalierbarkeit von Blazor Server-Apps zu behandeln:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-113">See the following resources to address Blazor Server app scalability:</span></span>

* [<span data-ttu-id="b3ca5-114">Grundlagen von Blazor Server-Apps</span><span class="sxs-lookup"><span data-stu-id="b3ca5-114">Fundamentals of Blazor Server apps</span></span>](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a><span data-ttu-id="b3ca5-115">Bereitstellungsserver</span><span class="sxs-lookup"><span data-stu-id="b3ca5-115">Deployment server</span></span>

<span data-ttu-id="b3ca5-116">Wenn Sie die Skalierbarkeit eines einzelnen Servers in Erwägung ziehen (zentrales Hochskalieren), ist der für eine App verfügbare Arbeitsspeicher wahrscheinlich die erste Ressource, die die App erschöpft, wenn sich die Benutzeranforderungen erhöhen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-116">When considering the scalability of a single server (scale up), the memory available to an app is likely the first resource that the app will exhaust as user demands increase.</span></span> <span data-ttu-id="b3ca5-117">Der verfügbare Arbeitsspeicher auf dem Server wirkt sich auf Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-117">The available memory on the server affects the:</span></span>

* <span data-ttu-id="b3ca5-118">Anzahl der aktiven Verbindungen, die ein Server unterstützen kann.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-118">Number of active circuits that a server can support.</span></span>
* <span data-ttu-id="b3ca5-119">Benutzeroberflächenlatenz auf dem Client.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-119">UI latency on the client.</span></span>

<span data-ttu-id="b3ca5-120">Anleitungen zum Erstellen sicherer und skalierbarer Blazor Server-Apps finden Sie unter <xref:blazor/security/server/threat-mitigation>.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-120">For guidance on building secure and scalable Blazor server apps, see <xref:blazor/security/server/threat-mitigation>.</span></span>

<span data-ttu-id="b3ca5-121">Jede Verbindung verwendet ungefähr 250 KB Arbeitsspeicher für eine minimale App im *Hello World*-Stil.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-121">Each circuit uses approximately 250 KB of memory for a minimal *Hello World*-style app.</span></span> <span data-ttu-id="b3ca5-122">Die Größe einer Verbindung hängt vom App-Code und den Zustandsverwaltungsanforderungen der einzelnen Komponenten ab.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-122">The size of a circuit depends on the app's code and the state maintenance requirements associated with each component.</span></span> <span data-ttu-id="b3ca5-123">Sie sollten die Ressourcenanforderungen während der Entwicklung für Ihre App und die Infrastruktur messen, aber die folgende Baseline kann ein Ausgangspunkt zur Planung des Bereitstellungsziels sein: Wenn Sie davon ausgehen, dass Ihre App 5.000 gleichzeitige Benutzer unterstützt, sollten Sie erwägen, mindestens 1,3 GB Serverarbeitsspeicher (oder ~273 KB pro Benutzer) für die App einzukalkulieren.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-123">We recommend that you measure resource demands during development for your app and infrastructure, but the following baseline can be a starting point in planning your deployment target: If you expect your app to support 5,000 concurrent users, consider budgeting at least 1.3 GB of server memory to the app (or ~273 KB per user).</span></span>

### <a name="signalr-configuration"></a><span data-ttu-id="b3ca5-124">SignalR-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b3ca5-124">SignalR configuration</span></span>

<span data-ttu-id="b3ca5-125">Blazor Server-Apps verwenden ASP.NET Core SignalR, um mit dem Browser zu kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-125">Blazor Server apps use ASP.NET Core SignalR to communicate with the browser.</span></span> <span data-ttu-id="b3ca5-126">[Die Hosting- und Skalierungsbedingungen von SignalR](xref:signalr/publish-to-azure-web-app) gelten für Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-126">[SignalR's hosting and scaling conditions](xref:signalr/publish-to-azure-web-app) apply to Blazor Server apps.</span></span>

<span data-ttu-id="b3ca5-127">Blazor funktioniert am besten, wenn WebSockets aufgrund geringerer Latenz und wegen Zuverlässigkeit und [Sicherheit](xref:signalr/security) zum SignalR-Transport verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-127">Blazor works best when using WebSockets as the SignalR transport due to lower latency, reliability, and [security](xref:signalr/security).</span></span> <span data-ttu-id="b3ca5-128">SignalR verwendet Long Polling, wenn WebSockets nicht verfügbar oder die App explizit für die Verwendung von Long Polling konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-128">Long Polling is used by SignalR when WebSockets isn't available or when the app is explicitly configured to use Long Polling.</span></span> <span data-ttu-id="b3ca5-129">Konfigurieren Sie die App bei Bereitstellung für Azure App Service in den Einstellungen für den Dienst im Azure-Portal für die Verwendung von WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-129">When deploying to Azure App Service, configure the app to use WebSockets in the Azure portal settings for the service.</span></span> <span data-ttu-id="b3ca5-130">Weitere Informationen zum Konfigurieren der App für Azure App Service finden Sie in den [Richtlinien für die SignalR-Veröffentlichung](xref:signalr/publish-to-azure-web-app).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-130">For details on configuring the app for Azure App Service, see the [SignalR publishing guidelines](xref:signalr/publish-to-azure-web-app).</span></span>

#### <a name="azure-signalr-service"></a><span data-ttu-id="b3ca5-131">Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="b3ca5-131">Azure SignalR Service</span></span>

<span data-ttu-id="b3ca5-132">Sie sollten [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) für Blazor Server-Apps verwenden.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-132">We recommend using the [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) for Blazor Server apps.</span></span> <span data-ttu-id="b3ca5-133">Der Dienst ermöglicht das Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-133">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="b3ca5-134">Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren von SignalR Service erheblich zur Verringerung der geografiebedingten Latenz bei.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-134">In addition, the SignalR service's global reach and high-performance data centers significantly aid in reducing latency due to geography.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b3ca5-135">Wenn [WebSockets](https://wikipedia.org/wiki/WebSocket) deaktiviert sind, simuliert Azure App Service eine Echtzeitverbindung mithilfe von langen HTTP-Abrufen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-135">When [WebSockets](https://wikipedia.org/wiki/WebSocket) are disabled, Azure App Service simulates a real-time connection using HTTP Long Polling.</span></span> <span data-ttu-id="b3ca5-136">Das lange Abrufen über HTTP ist deutlich langsamer als die Ausführung mit aktiviertem WebSockets, das keinen Abruf verwendet, um eine Clientserververbindung zu simulieren.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-136">HTTP Long Polling is noticeably slower than running with WebSockets enabled, which doesn't use polling to simulate a client-server connection.</span></span>
>
> <span data-ttu-id="b3ca5-137">Es wird empfohlen, Websockets für Blazor Server-Apps zu verwenden, die in Azure App Service bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-137">We recommend using WebSockets for Blazor Server apps deployed to Azure App Service.</span></span> <span data-ttu-id="b3ca5-138">In [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) werden Websockets standardmäßig verwendet.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-138">The [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) uses WebSockets by default.</span></span> <span data-ttu-id="b3ca5-139">Lesen Sie SignalR, wenn die App nicht Azure <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> Service verwendet.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-139">If the app doesn't use the Azure SignalR Service, see <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service>.</span></span>
>
> <span data-ttu-id="b3ca5-140">Weitere Informationen finden Sie in folgenden Quellen:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-140">For more information, see:</span></span>
>
> * [<span data-ttu-id="b3ca5-141">Was ist Azure SignalR Service?</span><span class="sxs-lookup"><span data-stu-id="b3ca5-141">What is Azure SignalR Service?</span></span>](/azure/azure-signalr/signalr-overview)
> * [<span data-ttu-id="b3ca5-142">Leitfaden zur Leistung für Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="b3ca5-142">Performance guide for Azure SignalR Service</span></span>](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a><span data-ttu-id="b3ca5-143">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="b3ca5-143">Configuration</span></span>

<span data-ttu-id="b3ca5-144">Wenn Sie eine App für Azure SignalR Service konfigurieren müssen, muss die App *persistente Sitzungen* unterstützen, in denen Clients [beim Prerendering wieder zurück zum gleichen Server geleitet werden](xref:blazor/hosting-models#connection-to-the-server).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-144">To configure an app for the Azure SignalR Service, the app must support *sticky sessions*, where clients are [redirected back to the same server when prerendering](xref:blazor/hosting-models#connection-to-the-server).</span></span> <span data-ttu-id="b3ca5-145">Die `ServerStickyMode`-Option oder der Konfigurationswert ist auf `Required` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-145">The `ServerStickyMode` option or configuration value is set to `Required`.</span></span> <span data-ttu-id="b3ca5-146">In der Regel erstellt eine App die Konfiguration nach **_EINEM_** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-146">Typically, an app creates the configuration using **_ONE_** of the following approaches:</span></span>

   * <span data-ttu-id="b3ca5-147">`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-147">`Startup.ConfigureServices`:</span></span>
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * <span data-ttu-id="b3ca5-148">Konfiguration (verwenden Sie **_EINEN_** der folgenden Ansätze):</span><span class="sxs-lookup"><span data-stu-id="b3ca5-148">Configuration (use **_ONE_** of the following approaches):</span></span>
  
     * <span data-ttu-id="b3ca5-149">In `appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-149">In `appsettings.json`:</span></span>

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * <span data-ttu-id="b3ca5-150">**Konfiguration** > **Anwendungseinstellungen** des App-Diensts im Azure-Portal (**Name**: `Azure__SignalR__StickyServerMode`, **Wert**: `Required`).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-150">The app service's **Configuration** > **Application settings** in the Azure portal (**Name**: `Azure__SignalR__StickyServerMode`, **Value**: `Required`).</span></span> <span data-ttu-id="b3ca5-151">Dieser Ansatz wird für die App automatisch übernommen, wenn Sie [Azure SignalR Service bereitstellen](#provision-the-azure-signalr-service).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-151">This approach is adopted for the app automatically if you [provision the Azure SignalR Service](#provision-the-azure-signalr-service).</span></span>

### <a name="provision-the-azure-signalr-service"></a><span data-ttu-id="b3ca5-152">Bereitstellen von Azure SignalR Service</span><span class="sxs-lookup"><span data-stu-id="b3ca5-152">Provision the Azure SignalR Service</span></span>

<span data-ttu-id="b3ca5-153">So stellen Sie Azure SignalR Service für eine App in Visual Studio bereit:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-153">To provision the Azure SignalR Service for an app in Visual Studio:</span></span>

1. <span data-ttu-id="b3ca5-154">Erstellen Sie ein Veröffentlichungsprofil für Azure-Apps in Visual Studio für die Blazor Server-App.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-154">Create an Azure Apps publish profile in Visual Studio for the Blazor Server app.</span></span>
1. <span data-ttu-id="b3ca5-155">Fügen Sie dem Profil die **Azure SignalR Service**-Abhängigkeit hinzu.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-155">Add the **Azure SignalR Service** dependency to the profile.</span></span> <span data-ttu-id="b3ca5-156">Wenn das Azure-Abonnement nicht über eine bereits vorhandene Azure SignalR Service-Instanz verfügt, die der App zugewiesen werden soll, wählen Sie **Neue Azure SignalR Service-Instanz erstellen** aus, um eine neue Dienstinstanz bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-156">If the Azure subscription doesn't have a pre-existing Azure SignalR Service instance to assign to the app, select **Create a new Azure SignalR Service instance** to provision a new service instance.</span></span>
1. <span data-ttu-id="b3ca5-157">Veröffentlichen Sie die App in Azure.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-157">Publish the app to Azure.</span></span>

<span data-ttu-id="b3ca5-158">Durch die Bereitstellung von Azure SignalR Service in Visual Studio werden automatisch [*persistente Sitzungen* aktiviert](#configuration), und die SignalR-Verbindungszeichenfolge wird der Konfiguration des App-Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-158">Provisioning the Azure SignalR Service in Visual Studio automatically [enables *sticky sessions*](#configuration) and adds the SignalR connection string to the app service's configuration.</span></span>

#### <a name="iis"></a><span data-ttu-id="b3ca5-159">IIS</span><span class="sxs-lookup"><span data-stu-id="b3ca5-159">IIS</span></span>

<span data-ttu-id="b3ca5-160">Aktivieren Sie bei Verwendung von IIS Folgendes:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-160">When using IIS, enable:</span></span>

* <span data-ttu-id="b3ca5-161">[WebSockets in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-161">[WebSockets on IIS](xref:fundamentals/websockets#enabling-websockets-on-iis).</span></span>
* <span data-ttu-id="b3ca5-162">[Persistente Sitzungen mit Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-162">[Sticky sessions with Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="kubernetes"></a><span data-ttu-id="b3ca5-163">Kubernetes</span><span class="sxs-lookup"><span data-stu-id="b3ca5-163">Kubernetes</span></span>

<span data-ttu-id="b3ca5-164">Erstellen Sie eine Eingangsdefinition mithilfe der folgenden [Kubernetes-Anmerkungen für persistente Sitzungen:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-164">Create an ingress definition with the following [Kubernetes annotations for sticky sessions](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/):</span></span>

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: <ingress-name>
  annotations:
    nginx.ingress.kubernetes.io/affinity: "cookie"
    nginx.ingress.kubernetes.io/session-cookie-name: "affinity"
    nginx.ingress.kubernetes.io/session-cookie-expires: "14400"
    nginx.ingress.kubernetes.io/session-cookie-max-age: "14400"
```

#### <a name="linux-with-nginx"></a><span data-ttu-id="b3ca5-165">Linux mit Nginx</span><span class="sxs-lookup"><span data-stu-id="b3ca5-165">Linux with Nginx</span></span>

<span data-ttu-id="b3ca5-166">Damit SignalR WebSockets ordnungsgemäß funktioniert, sollten Sie sich vergewissern, dass die Header `Upgrade` und `Connection` des Proxys auf die folgenden Werte festgelegt sind und dass `$connection_upgrade` einem der folgenden Werte zugeordnet ist:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-166">For SignalR WebSockets to function properly, confirm that the proxy's `Upgrade` and `Connection` headers are set to the following values and that `$connection_upgrade` is mapped to either:</span></span>

* <span data-ttu-id="b3ca5-167">Der Standardwert für den Upgradeheader</span><span class="sxs-lookup"><span data-stu-id="b3ca5-167">The Upgrade header value by default.</span></span>
* <span data-ttu-id="b3ca5-168">`close`, wenn der Upgradeheader fehlt oder leer ist</span><span class="sxs-lookup"><span data-stu-id="b3ca5-168">`close` when the Upgrade header is missing or empty.</span></span>

```
http {
    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen      80;
        server_name example.com *.example.com
        location / {
            proxy_pass         http://localhost:5000;
            proxy_http_version 1.1;
            proxy_set_header   Upgrade $http_upgrade;
            proxy_set_header   Connection $connection_upgrade;
            proxy_set_header   Host $host;
            proxy_cache_bypass $http_upgrade;
            proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header   X-Forwarded-Proto $scheme;
        }
    }
}
```

<span data-ttu-id="b3ca5-169">Weitere Informationen finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-169">For more information, see the following articles:</span></span>

* [<span data-ttu-id="b3ca5-170">NGINX als WebSocket-Proxy</span><span class="sxs-lookup"><span data-stu-id="b3ca5-170">NGINX as a WebSocket Proxy</span></span>](https://www.nginx.com/blog/websocket-nginx/)
* [<span data-ttu-id="b3ca5-171">WebSocket-Proxyfunktion</span><span class="sxs-lookup"><span data-stu-id="b3ca5-171">WebSocket proxying</span></span>](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a><span data-ttu-id="b3ca5-172">Linux mit Apache</span><span class="sxs-lookup"><span data-stu-id="b3ca5-172">Linux with Apache</span></span>

<span data-ttu-id="b3ca5-173">Konfigurieren Sie `ProxyPass` für den HTTP- und WebSockets-Datenverkehr, um eine Blazor-App hinter Apache unter Linux zu hosten.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-173">To host a Blazor app behind Apache on Linux, configure `ProxyPass` for HTTP and WebSockets traffic.</span></span>

<span data-ttu-id="b3ca5-174">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-174">In the following example:</span></span>

* <span data-ttu-id="b3ca5-175">Der Kestrel-Server wird auf dem Hostcomputer ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-175">Kestrel server is running on the host machine.</span></span>
* <span data-ttu-id="b3ca5-176">Die App lauscht an Port 5000 auf Datenverkehr.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-176">The app listens for traffic on port 5000.</span></span>

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

<span data-ttu-id="b3ca5-177">Aktivieren Sie die folgenden Module:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-177">Enable the following modules:</span></span>

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

<span data-ttu-id="b3ca5-178">Überprüfen Sie die Browserkonsole auf WebSockets-Fehler.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-178">Check the browser console for WebSockets errors.</span></span> <span data-ttu-id="b3ca5-179">Fehlerbeispiele:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-179">Example errors:</span></span>

* <span data-ttu-id="b3ca5-180">Firefox kann unter „ws://the-domain-name.tld/_blazor?id=XXX“ keine Verbindung mit dem Server herstellen.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-180">Firefox can't establish a connection to the server at ws://the-domain-name.tld/_blazor?id=XXX.</span></span>
* <span data-ttu-id="b3ca5-181">Fehler: "Failed to start the transport 'WebSockets'" (Beim Starten des Transports „Websockets“ ist ein Fehler aufgetreten): Fehler: "There was an error with the transport." (Beim Transport ist ein Fehler aufgetreten.)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-181">Error: Failed to start the transport 'WebSockets': Error: There was an error with the transport.</span></span>
* <span data-ttu-id="b3ca5-182">Fehler: "Failed to start the transport 'LongPolling'" (Beim Starten des Transports „LongPolling“ ist ein Fehler aufgetreten): "TypeError: this.transport is undefined" (TypeError: Dieser Transport ist nicht definiert)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-182">Error: Failed to start the transport 'LongPolling': TypeError: this.transport is undefined</span></span>
* <span data-ttu-id="b3ca5-183">Fehler: "Unable to connect to the server with any of the available transports." (Es kann mit keinem der verfügbaren Transporte eine Verbindung mit dem Server hergestellt werden.)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-183">Error: Unable to connect to the server with any of the available transports.</span></span> <span data-ttu-id="b3ca5-184">"WebSockets failed" (WebSockets-Fehler)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-184">WebSockets failed</span></span>
* <span data-ttu-id="b3ca5-185">Fehler: "Cannot send data if the connection is not in the 'Connected' State." (Es können keine Daten gesendet werden, wenn die Verbindung nicht den Zustand „Verbunden“ aufweist.)</span><span class="sxs-lookup"><span data-stu-id="b3ca5-185">Error: Cannot send data if the connection is not in the 'Connected' State.</span></span>

<span data-ttu-id="b3ca5-186">Weitere Informationen finden Sie in der [Apache-Dokumentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span><span class="sxs-lookup"><span data-stu-id="b3ca5-186">For more information, see the [Apache documentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).</span></span>

### <a name="measure-network-latency"></a><span data-ttu-id="b3ca5-187">Messen der Netzwerklatenz</span><span class="sxs-lookup"><span data-stu-id="b3ca5-187">Measure network latency</span></span>

<span data-ttu-id="b3ca5-188">Mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) kann die Netzwerklatenz gemessen werden, wie im folgenden Beispiel veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="b3ca5-188">[JS interop](xref:blazor/call-javascript-from-dotnet) can be used to measure network latency, as the following example demonstrates:</span></span>

```razor
@inject IJSRuntime JS

@if (latency is null)
{
    <span>Calculating...</span>
}
else
{
    <span>@(latency.Value.TotalMilliseconds)ms</span>
}

@code {
    private DateTime startTime;
    private TimeSpan? latency;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            startTime = DateTime.UtcNow;
            var _ = await JS.InvokeAsync<string>("toString");
            latency = DateTime.UtcNow - startTime;
            StateHasChanged();
        }
    }
}
```

<span data-ttu-id="b3ca5-189">Für eine sinnvolle Benutzeroberfläche empfehlen wir eine dauerhafte Latenz von höchstens 250 ms.</span><span class="sxs-lookup"><span data-stu-id="b3ca5-189">For a reasonable UI experience, we recommend a sustained UI latency of 250ms or less.</span></span>
