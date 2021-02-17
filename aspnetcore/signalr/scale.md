---
title: ASP.net Core SignalR Produktions Hosting und-Skalierung
author: bradygaster
description: Erfahren Sie, wie Sie Leistungs-und Skalierungsprobleme in apps vermeiden, die ASP.net Core verwenden SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: e70f3143159a1817e326a95b30e7369a5c9ab025
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100564008"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.net Core SignalR Hosting und Skalierung

Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)

In diesem Artikel werden die Überlegungen zu Hosting und Skalierung für apps mit hohem Datenverkehr erläutert, SignalR die ASP.net Core

## <a name="sticky-sessions"></a>Persistente Sitzungen

SignalR erfordert, dass alle HTTP-Anforderungen für eine bestimmte Verbindung vom gleichen Server Prozess verarbeitet werden. Wenn SignalR in einer Serverfarm (mehrere Server) ausgeführt wird, müssen "persistente Sitzungen" verwendet werden. "Persistente Sitzungen" werden von einigen Lasten Ausgleichs Modulen auch als Sitzungs Affinität bezeichnet. Azure App Service mithilfe von [Application Request Routing](/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (arr) Anforderungen weiterleiten. Wenn Sie die Einstellung "arr-Affinität" in ihrer Azure App Service aktivieren, werden "persistente Sitzungen" aktiviert. Die einzigen Situationen, in denen keine persistenten Sitzungen erforderlich sind, sind:

1. Beim Hosting auf einem einzelnen Server in einem einzelnen Prozess.
1. Bei Verwendung des Azure- SignalR Dienstanbieter.
1. Wenn alle Clients so konfiguriert sind, dass Sie **nur** websockets verwenden, **und** die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) in der Client Konfiguration aktiviert ist.

In allen anderen Fällen (einschließlich der Verwendung der redis-Rückwand) muss die Serverumgebung für persistente Sitzungen konfiguriert werden.

Anleitungen zum Konfigurieren von Azure App Service für SignalR finden Sie unter <xref:signalr/publish-to-azure-web-app> .

## <a name="tcp-connection-resources"></a>TCP-Verbindungs Ressourcen

Die Anzahl der gleichzeitigen TCP-Verbindungen, die ein Webserver unterstützen kann, ist begrenzt. Standard-HTTP-Clients verwenden *kurzlebige* Verbindungen. Diese Verbindungen können geschlossen werden, wenn der Client in den Leerlauf wechselt und später erneut geöffnet wird. Auf der anderen Seite ist eine SignalR Verbindung *persistent*. SignalR Verbindungen bleiben auch dann geöffnet, wenn der Client in den Leerlauf wechselt. In einer APP mit hohem Datenverkehr, die viele Clients bedient, können diese persistenten Verbindungen bewirken, dass Server die maximale Anzahl von Verbindungen erreichen.

Persistente Verbindungen verbrauchen auch zusätzlichen Arbeitsspeicher, um die einzelnen Verbindungen zu verfolgen.

Die intensive Verwendung von Verbindungs bezogenen Ressourcen von SignalR kann sich auf andere Web-Apps auswirken, die auf demselben Server gehostet werden. Wenn SignalR geöffnet wird und die letzten verfügbaren TCP-Verbindungen enthält, sind auch andere Web-Apps auf demselben Server nicht mehr verfügbar.

Wenn auf einem Server keine Verbindungen mehr auftreten, werden zufällige Socketfehler und Fehler beim Zurücksetzen der Verbindung angezeigt. Beispiel:

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

Um zu verhindern SignalR , dass die Ressourcenverwendung in anderen Web-Apps zu Fehlern führt, führen SignalR Sie auf unterschiedlichen Servern aus als Ihre anderen Web-Apps

Um zu verhindern SignalR , dass die Ressourcenverwendung in einer APP zu Fehlern führt, Skalieren Sie horizontal hoch, SignalR um die Anzahl der Verbindungen zu begrenzen, die ein Server verarbeiten muss

## <a name="scale-out"></a>Aufskalieren

Eine APP, die verwendet SignalR , muss alle zugehörigen Verbindungen nachverfolgen, wodurch Probleme für eine Serverfarm verursacht werden. Fügen Sie einen Server hinzu, der neue Verbindungen erhält, die von den anderen Servern nicht bekannt sind. SignalRAuf jedem Server im folgenden Diagramm sind z. b. die Verbindungen auf den anderen Servern nicht bekannt. Wenn SignalR auf einem der Server eine Nachricht an alle Clients senden soll, wird die Nachricht nur an die Clients gesendet, die mit diesem Server verbunden sind.

![Skalierung:: NO-LOC (signalr)::: ohne Rückwand](scale/_static/scale-no-backplane.png)

Die Optionen für die Lösung dieses Problems sind der [Azure- SignalR Dienst](#azure-signalr-service) und die [redis-Rückwand](#redis-backplane).

## <a name="azure-signalr-service"></a>Azure SignalR Service

Beim Azure- SignalR Dienst handelt es sich um einen Proxy und nicht um eine Backplane. Jedes Mal, wenn ein Client eine Verbindung mit dem Server initiiert, wird der Client umgeleitet, um eine Verbindung mit dem Dienst herzustellen. Dieser Prozess wird in der folgenden Abbildung veranschaulicht:

![Herstellen einer Verbindung mit Azure::: NO-LOC (signalr)::: Service](scale/_static/azure-signalr-service-one-connection.png)

Das Ergebnis ist, dass der Dienst alle Clientverbindungen verwaltet, während jeder Server nur eine kleine Konstante Anzahl von Verbindungen mit dem Dienst benötigt, wie im folgenden Diagramm dargestellt:

![Mit dem Dienst verbundene Clients, Server, die mit dem Dienst verbunden sind](scale/_static/azure-signalr-service-multiple-connections.png)

Diese Vorgehensweise zum horizontalen hochskalieren hat gegenüber der redis-Rückwand-Alternative mehrere Vorteile:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind nicht erforderlich, da Clients sofort an den Azure-Dienst umgeleitet werden, SignalR Wenn Sie eine Verbindung herstellen.
* Eine SignalR App kann basierend auf der Anzahl der gesendeten Nachrichten horizontal hochskaliert werden, während der Azure- SignalR Dienst für eine beliebige Anzahl von Verbindungen skaliert wird. Es könnten z. b. Tausende von Clients vorhanden sein. Wenn jedoch nur wenige Nachrichten pro Sekunde gesendet werden, SignalR muss die APP nicht auf mehrere Server horizontal hochskaliert werden, um die Verbindungen selbst zu bewältigen.
* Eine SignalR App verwendet nicht wesentlich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR .

Aus diesen Gründen wird der Azure- SignalR Dienst für alle ASP.net Core- SignalR apps empfohlen, die in Azure gehostet werden, einschließlich APP Service, VMS und Containern.

Weitere Informationen finden Sie in [der SignalR Dokumentation zum Azure-Dienst](/azure/azure-signalr/signalr-overview).

## <a name="redis-backplane"></a>Redis-Backplane

[Redis](https://redis.io/) ist ein Schlüssel-Wert-Speicher im Arbeitsspeicher, der ein Messaging System mit einem Veröffentlichungs-/Abonnementmodell unterstützt. Die SignalR redis-Rückwand verwendet die Pub/Sub-Funktion, um Nachrichten an andere Server weiterzuleiten. Wenn ein Client eine Verbindung herstellt, werden die Verbindungsinformationen an die Backplane übermittelt. Wenn ein Server eine Nachricht an alle Clients senden möchte, sendet er an die Backplane. Die Rückwand kennt alle verbundenen Clients und die Server, auf denen Sie sich befinden. Die Nachricht wird über die entsprechenden Server an alle Clients gesendet. Dieser Vorgang wird in der folgenden Abbildung veranschaulicht:

![Redis-Backplane, von einem Server an alle Clients gesendete Nachrichten](scale/_static/redis-backplane.png)

Die redis-Rückwand ist der empfohlene Ansatz für horizontales Skalieren für apps, die in ihrer eigenen Infrastruktur gehostet werden. Wenn zwischen Ihrem Rechenzentrum und einem Azure-Rechenzentrum eine beträchtliche Verbindungs Wartezeit vorliegt, ist der Azure- SignalR Dienst möglicherweise nicht die praktische Option für lokale apps mit geringer Latenz oder hohen Durchsatzanforderungen.

Die SignalR zuvor notierten Vorteile des Azure-Dienstanbieter sind Nachteile der redis-Backplane:

* Persistente Sitzungen, auch bekannt als [Client Affinität](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), sind erforderlich, außer wenn **beide** der folgenden Punkte zutreffen:
  * Alle Clients sind so konfiguriert, dass Sie **nur** websockets verwenden.
  * Die [Einstellung skipaushandlung](xref:signalr/configuration#configure-additional-options) ist in der Client Konfiguration aktiviert. 
   Nachdem eine Verbindung auf einem Server initiiert wurde, muss die Verbindung auf diesem Server bestehen.
* Eine SignalR app muss basierend auf der Anzahl von Clients horizontal hochskaliert werden, auch wenn nur wenige Nachrichten gesendet werden.
* Eine- SignalR App verwendet erheblich mehr Verbindungs Ressourcen als eine Web-App ohne SignalR .

## <a name="iis-limitations-on-windows-client-os"></a>IIS-Einschränkungen für Windows-Client Betriebssystem

Windows 10 und Windows 8. x sind Client Betriebssysteme. IIS unter Client Betriebssystemen hat eine Beschränkung von 10 gleichzeitigen Verbindungen. SignalRVerbindungen sind:

* Vorübergehend und häufig wieder hergestellt.
* Wird **nicht** sofort verworfen, wenn Sie nicht mehr verwendet wird.

Die vorangehenden Bedingungen machen es wahrscheinlich, dass die 10 Verbindungs Beschränkung für ein Client Betriebssystem erreicht wird. Wenn ein Client Betriebssystem für die Entwicklung verwendet wird, wird Folgendes empfohlen:

* Vermeiden Sie IIS.
* Verwenden Sie Kestrel oder IIS Express als Bereitstellungs Ziele.

## <a name="linux-with-nginx"></a>Linux mit Nginx

Im folgenden finden Sie die mindestens erforderlichen Einstellungen zum Aktivieren von websockets, serversentevents und longabruf für SignalR :

```nginx
http {
  map $http_connection $connection_upgrade {
    "~*Upgrade" $http_connection;
    default keep-alive;
}

  server {
    listen 80;
    server_name example.com *.example.com;

    # Configure the SignalR Endpoint
    location /hubroute {
      # App server url
      proxy_pass http://localhost:5000;

      # Configuration for WebSockets
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection $connection_upgrade;
      proxy_cache off;

      # Configuration for ServerSentEvents
      proxy_buffering off;

      # Configuration for LongPolling or if your KeepAliveInterval is longer than 60 seconds
      proxy_read_timeout 100s;

      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
    }
  }
}
```

Wenn mehrere Back-End-Server verwendet werden, müssen persistente Sitzungen hinzugefügt werden, um zu verhindern, dass SignalR beim Herstellen einer Verbindung Verbindungen zwischen Es gibt mehrere Möglichkeiten zum Hinzufügen von persistenten Sitzungen in Nginx. Unten werden zwei Ansätze angezeigt, je nachdem, was Ihnen zur Verfügung steht.

Zusätzlich zur vorherigen Konfiguration wird Folgendes hinzugefügt. In den folgenden Beispielen `backend` ist der Name der Server Gruppe.

Mit [nginx Open Source](https://nginx.org/en/)verwenden `ip_hash` Sie, um Verbindungen mit einem Server basierend auf der IP-Adresse des Clients weiterzuleiten:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    ip_hash;
  }
}
```

Mit [nginx Plus](https://www.nginx.com/products/nginx)können Sie verwenden, `sticky` um cookie Anforderungen hinzuzufügen und die Anforderungen des Benutzers an einen Server anzuheften:

```nginx
http {
  upstream backend {
    # App server 1
    server http://localhost:5000;
    # App server 2
    server http://localhost:5002;

    sticky cookie srv_id expires=max domain=.example.com path=/ httponly;
  }
}
```

Ändern Sie abschließend `proxy_pass http://localhost:5000` den `server` Abschnitt in `proxy_pass http://backend` .

Weitere Informationen zu websockets über nginx finden Sie unter [nginx als WebSocket-Proxy](https://www.nginx.com/blog/websocket-nginx).

Weitere Informationen zum Lastenausgleich und zu kurz Notizen finden Sie unter [nginx-Lastenausgleich](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/).

Weitere Informationen zu ASP.net Core mit nginx finden Sie im folgenden Artikel:
* <xref:host-and-deploy/linux-nginx>

## <a name="third-party-signalr-backplane-providers"></a>Drittanbieter- SignalR Rückwand-Anbieter

* [NCache](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* [Tro](https://github.com/OrleansContrib/SignalR.Orleans)
* [Rebus](https://github.com/rebus-org/Rebus.SignalR)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Dokumentation zu Azure- SignalR Diensten](/azure/azure-signalr/signalr-overview)
* [Einrichten einer redis-Rückwand](xref:signalr/redis-backplane)
