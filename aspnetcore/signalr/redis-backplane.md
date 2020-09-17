---
title: Redis-Rückwand für ASP.net Core horizontales hoch SignalR skalieren
author: bradygaster
description: Erfahren Sie, wie Sie eine redis-Rückwand einrichten, um das horizontale hochskalieren für eine ASP.net Core-APP zu aktivieren SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/redis-backplane
ms.openlocfilehash: a57176409c5f9bcc620db7e070f6616951eb9a54
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722773"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-no-locsignalr-scale-out"></a>Einrichten einer redis-Rückwand für die ASP.net Core horizontales hoch SignalR skalieren

Von [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaester](https://twitter.com/bradygaster)und [Tom Dykstra](https://github.com/tdykstra)

In diesem Artikel SignalR werden spezifische Aspekte der Einrichtung eines [redis](https://redis.io/) -Servers erläutert, der zum horizontalen Skalieren einer ASP.net Core-App verwendet werden soll SignalR .

## <a name="set-up-a-redis-backplane"></a>Einrichten einer redis-Rückwand

* Stellen Sie einen redis-Server bereit.

  > [!IMPORTANT] 
  > Für den Einsatz in der Produktion wird eine redis-Rückwand nur dann empfohlen, wenn Sie im selben Rechenzentrum wie die app ausgeführt wird SignalR . Andernfalls beeinträchtigt die Netzwerk Latenz die Leistung. Wenn Ihre SignalR app in der Azure-Cloud ausgeführt wird, empfehlen wir den Azure- SignalR Dienst anstelle einer redis-Backplane. Sie können den Azure redis Cache-Dienst für Entwicklungs-und Testumgebungen verwenden.

  Weitere Informationen finden Sie in den folgenden Ressourcen:

  * <xref:signalr/scale>
  * [Redis-Dokumentation](https://redis.io/)
  * [Dokumentation zu Azure Redis Cache](/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* Installieren Sie SignalR das nuget-Paket in der APP `Microsoft.AspNetCore.SignalR.Redis` .
* In der- `Startup.ConfigureServices` Methode wird `AddRedis` nach aufgerufen `AddSignalR` :

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.

  Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` . In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Installieren Sie in der SignalR App eines der folgenden nuget-Pakete:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -Abhängig von stackexchange. redis 2. x Dies ist das empfohlene Paket für ASP.net Core 2,2 und höher.
  * `Microsoft.AspNetCore.SignalR.Redis` -Abhängig von stackexchange. redis 1. x Dieses Paket ist nicht in ASP.net Core 3,0 und höher enthalten.

* In der- `Startup.ConfigureServices` Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 Wenn Sie verwenden `Microsoft.AspNetCore.SignalR.Redis` , wird aufgerufen <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.

  Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` . In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 Wenn Sie verwenden `Microsoft.AspNetCore.SignalR.Redis` , wird aufgerufen <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*> .

  Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.

  Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* Installieren Sie in der SignalR App das folgende nuget-Paket:

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* In der- `Startup.ConfigureServices` Methode <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*> :

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* Konfigurieren Sie die Optionen nach Bedarf:
 
  Die meisten Optionen können in der Verbindungs Zeichenfolge oder im [configurationoptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) -Objekt festgelegt werden. Die in angegebenen Optionen über `ConfigurationOptions` schreiben die in der Verbindungs Zeichenfolge festgelegten Optionen.

  Im folgenden Beispiel wird gezeigt, wie Optionen im-Objekt festgelegt werden `ConfigurationOptions` . In diesem Beispiel wird ein Kanal Präfix hinzugefügt, sodass mehrere apps dieselbe redis-Instanz gemeinsam nutzen können, wie im folgenden Schritt erläutert.

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  Im vorangehenden Code `options.Configuration` wird mit dem, was in der Verbindungs Zeichenfolge angegeben wurde, initialisiert.

  Weitere Informationen zu redis-Optionen finden Sie in der [stackexchange redis-Dokumentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).

::: moniker-end

* Wenn Sie einen redis-Server für mehrere SignalR Apps verwenden, verwenden Sie für jede APP ein anderes channelpräfix SignalR .

  Wenn Sie ein Kanal Präfix festlegen SignalR , wird eine APP von anderen Personen isoliert, die unterschiedliche channelpräfixe verwenden. Wenn Sie keine unterschiedlichen Präfixe zuweisen, wird eine Nachricht, die von einer APP an alle Ihre eigenen Clients gesendet wird, an alle Clients aller apps gesendet, die den redis-Server als Rück Ebene verwenden.

* Konfigurieren Sie die Lasten Ausgleichs Software der Serverfarm für persistente Sitzungen. Im folgenden finden Sie einige Beispiele für die Vorgehensweise:

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis-Server Fehler

Wenn ein redis-Server ausfällt, löst Ausnahmen aus, SignalR die darauf hinweisen, dass Nachrichten nicht übermittelt werden. Einige typische Ausnahme Meldungen:

* *Fehler beim Schreiben der Nachricht*
* *Fehler beim Aufrufen der Hub-Methode "MethodName".*
* *Fehler bei Verbindung mit redis.*

SignalR puffert Nachrichten nicht, um Sie zu senden, wenn der Server wieder verfügbar ist. Alle Nachrichten, die gesendet werden, während der redis-Server ausfällt, gehen verloren.

SignalR die Verbindung wird automatisch erneut hergestellt, wenn der redis-Server wieder verfügbar ist.

### <a name="custom-behavior-for-connection-failures"></a>Benutzerdefiniertes Verhalten bei Verbindungsfehlern

Es folgt ein Beispiel, das zeigt, wie redis-Verbindungsfehler Ereignisse behandelt werden.

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="redis-clustering"></a>Redis-Clustering

[Redis-Clustering](https://redis.io/topics/cluster-spec) ist eine Methode zum Erreichen von Hochverfügbarkeit mithilfe mehrerer redis-Server. Clustering wird nicht offiziell unterstützt, kann jedoch funktionieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Ressourcen:

* <xref:signalr/scale>
* [Redis-Dokumentation](https://redis.io/documentation)
* [Dokumentation zu stackexchange redis](https://stackexchange.github.io/StackExchange.Redis/)
* [Dokumentation zu Azure Redis Cache](/azure/redis-cache/)