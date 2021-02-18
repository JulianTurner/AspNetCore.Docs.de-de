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
# <a name="host-and-deploy-blazor-server"></a>Hosten und Bereitstellen Blazor Server

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

[Blazor Server-Apps](xref:blazor/hosting-models#blazor-server) können [generische Hostkonfigurationswerte](xref:fundamentals/host/generic-host#host-configuration) akzeptieren.

## <a name="deployment"></a>Bereitstellung

Mit dem [Blazor Server-Hostingmodell](xref:blazor/hosting-models#blazor-server) wird Blazor in einer ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

Hierfür wird ein Webserver benötigt, der eine ASP.NET Core-App hosten kann. Visual Studio enthält die Projektvorlage der **Blazor Server-App** (oder die `blazorserverside`-Vorlage bei Verwendung des Befehls [`dotnet new`](/dotnet/core/tools/dotnet-new)).

## <a name="scalability"></a>Skalierbarkeit

Planen Sie eine Bereitstellung, um die verfügbare Infrastruktur für eine Blazor Server-App optimal zu nutzen. Sehen Sie sich die folgenden Ressourcen an, um die Skalierbarkeit von Blazor Server-Apps zu behandeln:

* [Grundlagen von Blazor Server-Apps](xref:blazor/hosting-models#blazor-server)
* <xref:blazor/security/server/threat-mitigation>

### <a name="deployment-server"></a>Bereitstellungsserver

Wenn Sie die Skalierbarkeit eines einzelnen Servers in Erwägung ziehen (zentrales Hochskalieren), ist der für eine App verfügbare Arbeitsspeicher wahrscheinlich die erste Ressource, die die App erschöpft, wenn sich die Benutzeranforderungen erhöhen. Der verfügbare Arbeitsspeicher auf dem Server wirkt sich auf Folgendes aus:

* Anzahl der aktiven Verbindungen, die ein Server unterstützen kann.
* Benutzeroberflächenlatenz auf dem Client.

Anleitungen zum Erstellen sicherer und skalierbarer Blazor Server-Apps finden Sie unter <xref:blazor/security/server/threat-mitigation>.

Jede Verbindung verwendet ungefähr 250 KB Arbeitsspeicher für eine minimale App im *Hello World*-Stil. Die Größe einer Verbindung hängt vom App-Code und den Zustandsverwaltungsanforderungen der einzelnen Komponenten ab. Sie sollten die Ressourcenanforderungen während der Entwicklung für Ihre App und die Infrastruktur messen, aber die folgende Baseline kann ein Ausgangspunkt zur Planung des Bereitstellungsziels sein: Wenn Sie davon ausgehen, dass Ihre App 5.000 gleichzeitige Benutzer unterstützt, sollten Sie erwägen, mindestens 1,3 GB Serverarbeitsspeicher (oder ~273 KB pro Benutzer) für die App einzukalkulieren.

### <a name="signalr-configuration"></a>SignalR-Konfiguration

Blazor Server-Apps verwenden ASP.NET Core SignalR, um mit dem Browser zu kommunizieren. [Die Hosting- und Skalierungsbedingungen von SignalR](xref:signalr/publish-to-azure-web-app) gelten für Blazor Server-Apps.

Blazor funktioniert am besten, wenn WebSockets aufgrund geringerer Latenz und wegen Zuverlässigkeit und [Sicherheit](xref:signalr/security) zum SignalR-Transport verwendet wird. SignalR verwendet Long Polling, wenn WebSockets nicht verfügbar oder die App explizit für die Verwendung von Long Polling konfiguriert ist. Konfigurieren Sie die App bei Bereitstellung für Azure App Service in den Einstellungen für den Dienst im Azure-Portal für die Verwendung von WebSockets. Weitere Informationen zum Konfigurieren der App für Azure App Service finden Sie in den [Richtlinien für die SignalR-Veröffentlichung](xref:signalr/publish-to-azure-web-app).

#### <a name="azure-signalr-service"></a>Azure SignalR Service

Sie sollten [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) für Blazor Server-Apps verwenden. Der Dienst ermöglicht das Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen. Außerdem tragen die globale Reichweite und die Hochleistungsrechenzentren von SignalR Service erheblich zur Verringerung der geografiebedingten Latenz bei.

> [!IMPORTANT]
> Wenn [WebSockets](https://wikipedia.org/wiki/WebSocket) deaktiviert sind, simuliert Azure App Service eine Echtzeitverbindung mithilfe von langen HTTP-Abrufen. Das lange Abrufen über HTTP ist deutlich langsamer als die Ausführung mit aktiviertem WebSockets, das keinen Abruf verwendet, um eine Clientserververbindung zu simulieren.
>
> Es wird empfohlen, Websockets für Blazor Server-Apps zu verwenden, die in Azure App Service bereitgestellt werden. In [Azure SignalR Service](xref:signalr/scale#azure-signalr-service) werden Websockets standardmäßig verwendet. Lesen Sie SignalR, wenn die App nicht Azure <xref:signalr/publish-to-azure-web-app#configure-the-app-in-azure-app-service> Service verwendet.
>
> Weitere Informationen finden Sie in folgenden Quellen:
>
> * [Was ist Azure SignalR Service?](/azure/azure-signalr/signalr-overview)
> * [Leitfaden zur Leistung für Azure SignalR Service](/azure-signalr/signalr-concept-performance#performance-factors)

### <a name="configuration"></a>Konfiguration

Wenn Sie eine App für Azure SignalR Service konfigurieren müssen, muss die App *persistente Sitzungen* unterstützen, in denen Clients [beim Prerendering wieder zurück zum gleichen Server geleitet werden](xref:blazor/hosting-models#connection-to-the-server). Die `ServerStickyMode`-Option oder der Konfigurationswert ist auf `Required` festgelegt. In der Regel erstellt eine App die Konfiguration nach **_EINEM_** der folgenden Ansätze:

   * `Startup.ConfigureServices`:
  
     ```csharp
     services.AddSignalR().AddAzureSignalR(options =>
     {
         options.ServerStickyMode = 
             Microsoft.Azure.SignalR.ServerStickyMode.Required;
     });
     ```

   * Konfiguration (verwenden Sie **_EINEN_** der folgenden Ansätze):
  
     * In `appsettings.json`:

       ```json
       "Azure:SignalR:StickyServerMode": "Required"
       ```

     * **Konfiguration** > **Anwendungseinstellungen** des App-Diensts im Azure-Portal (**Name**: `Azure__SignalR__StickyServerMode`, **Wert**: `Required`). Dieser Ansatz wird für die App automatisch übernommen, wenn Sie [Azure SignalR Service bereitstellen](#provision-the-azure-signalr-service).

### <a name="provision-the-azure-signalr-service"></a>Bereitstellen von Azure SignalR Service

So stellen Sie Azure SignalR Service für eine App in Visual Studio bereit:

1. Erstellen Sie ein Veröffentlichungsprofil für Azure-Apps in Visual Studio für die Blazor Server-App.
1. Fügen Sie dem Profil die **Azure SignalR Service**-Abhängigkeit hinzu. Wenn das Azure-Abonnement nicht über eine bereits vorhandene Azure SignalR Service-Instanz verfügt, die der App zugewiesen werden soll, wählen Sie **Neue Azure SignalR Service-Instanz erstellen** aus, um eine neue Dienstinstanz bereitzustellen.
1. Veröffentlichen Sie die App in Azure.

Durch die Bereitstellung von Azure SignalR Service in Visual Studio werden automatisch [*persistente Sitzungen* aktiviert](#configuration), und die SignalR-Verbindungszeichenfolge wird der Konfiguration des App-Diensts hinzugefügt.

#### <a name="iis"></a>IIS

Aktivieren Sie bei Verwendung von IIS Folgendes:

* [WebSockets in IIS](xref:fundamentals/websockets#enabling-websockets-on-iis)
* [Persistente Sitzungen mit Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)

#### <a name="kubernetes"></a>Kubernetes

Erstellen Sie eine Eingangsdefinition mithilfe der folgenden [Kubernetes-Anmerkungen für persistente Sitzungen:](https://kubernetes.github.io/ingress-nginx/examples/affinity/cookie/)

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

#### <a name="linux-with-nginx"></a>Linux mit Nginx

Damit SignalR WebSockets ordnungsgemäß funktioniert, sollten Sie sich vergewissern, dass die Header `Upgrade` und `Connection` des Proxys auf die folgenden Werte festgelegt sind und dass `$connection_upgrade` einem der folgenden Werte zugeordnet ist:

* Der Standardwert für den Upgradeheader
* `close`, wenn der Upgradeheader fehlt oder leer ist

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

Weitere Informationen finden Sie in den folgenden Artikeln:

* [NGINX als WebSocket-Proxy](https://www.nginx.com/blog/websocket-nginx/)
* [WebSocket-Proxyfunktion](http://nginx.org/docs/http/websocket.html)
* <xref:host-and-deploy/linux-nginx>

## <a name="linux-with-apache"></a>Linux mit Apache

Konfigurieren Sie `ProxyPass` für den HTTP- und WebSockets-Datenverkehr, um eine Blazor-App hinter Apache unter Linux zu hosten.

Im folgenden Beispiel:

* Der Kestrel-Server wird auf dem Hostcomputer ausgeführt.
* Die App lauscht an Port 5000 auf Datenverkehr.

```
ProxyRequests       On
ProxyPreserveHost   On
ProxyPassMatch      ^/_blazor/(.*) http://localhost:5000/_blazor/$1
ProxyPass           /_blazor ws://localhost:5000/_blazor
ProxyPass           / http://localhost:5000/
ProxyPassReverse    / http://localhost:5000/
```

Aktivieren Sie die folgenden Module:

```
a2enmod   proxy
a2enmod   proxy_wstunnel
```

Überprüfen Sie die Browserkonsole auf WebSockets-Fehler. Fehlerbeispiele:

* Firefox kann unter „ws://the-domain-name.tld/_blazor?id=XXX“ keine Verbindung mit dem Server herstellen.
* Fehler: "Failed to start the transport 'WebSockets'" (Beim Starten des Transports „Websockets“ ist ein Fehler aufgetreten): Fehler: "There was an error with the transport." (Beim Transport ist ein Fehler aufgetreten.)
* Fehler: "Failed to start the transport 'LongPolling'" (Beim Starten des Transports „LongPolling“ ist ein Fehler aufgetreten): "TypeError: this.transport is undefined" (TypeError: Dieser Transport ist nicht definiert)
* Fehler: "Unable to connect to the server with any of the available transports." (Es kann mit keinem der verfügbaren Transporte eine Verbindung mit dem Server hergestellt werden.) "WebSockets failed" (WebSockets-Fehler)
* Fehler: "Cannot send data if the connection is not in the 'Connected' State." (Es können keine Daten gesendet werden, wenn die Verbindung nicht den Zustand „Verbunden“ aufweist.)

Weitere Informationen finden Sie in der [Apache-Dokumentation](https://httpd.apache.org/docs/current/mod/mod_proxy.html).

### <a name="measure-network-latency"></a>Messen der Netzwerklatenz

Mit [JS-Interop](xref:blazor/call-javascript-from-dotnet) kann die Netzwerklatenz gemessen werden, wie im folgenden Beispiel veranschaulicht:

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

Für eine sinnvolle Benutzeroberfläche empfehlen wir eine dauerhafte Latenz von höchstens 250 ms.
