---
title: Verteiltes Zwischenspeichern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie einen ASP.net Core verteilten Cache verwenden können, um die Leistung und Skalierbarkeit der APP zu verbessern, insbesondere in einer Cloud-oder Serverfarm Umgebung.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 6d87c8de66bf5600189465b96dee903841106b6f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061144"
---
# <a name="distributed-caching-in-aspnet-core"></a>Verteiltes Zwischenspeichern in ASP.net Core

Von " [nahusin Nasir](https://github.com/mohsinnasir) " und [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

Bei einem verteilten Cache handelt es sich um einen Cache, der von mehreren App-Servern gemeinsam verwendet wird, die in der Regel als externer Dienst für die App-Server verwaltet werden Ein verteilter Cache kann die Leistung und Skalierbarkeit einer ASP.net Core-App verbessern, insbesondere wenn die APP von einem Cloud-Dienst oder einer Serverfarm gehostet wird.

Ein verteilter Cache hat mehrere Vorteile gegenüber anderen cachingszenarien, in denen zwischengespeicherte Daten auf einzelnen App-Servern gespeichert werden.

Wenn zwischengespeicherte Daten verteilt werden, sind die Daten wie folgt:

* Ist *in* allen Anforderungen an mehrere Server konsistent (konsistent).
* Überstehen Serverneustarts und App-bereit Stellungen.
* Verwendet keinen lokalen Arbeitsspeicher.

Die Konfiguration verteilter Caches ist Implementierungs spezifisch. In diesem Artikel wird beschrieben, wie Sie SQL Server und verteilte redis-Caches konfigurieren. Implementierungen von Drittanbietern sind ebenfalls verfügbar, wie z. b. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)). Unabhängig davon, welche Implementierung ausgewählt ist, interagiert die App mithilfe der-Schnittstelle mit dem Cache <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

Um einen SQL Server verteilten Cache zu verwenden, fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.

Um einen verteilten redis-Cache zu verwenden, fügen Sie dem Paket [Microsoft. Extensions. Caching. stackexchangeredis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) einen Paket Verweis hinzu.

Fügen Sie zum Verwenden von NCache-verteiltem Cache einen Paket Verweis zum [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket hinzu.

## <a name="idistributedcache-interface"></a>Idistributedcache-Schnittstelle

Die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle stellt die folgenden Methoden zum Bearbeiten von Elementen in der Implementierung verteilter Caches bereit:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akzeptiert einen Zeichen folgen Schlüssel und ruft ein zwischengespeichertes Element als-Array ab, `byte[]` Wenn es im Cache gefunden wird.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Fügt dem Cache ein Element (als `byte[]` Array) mithilfe eines Zeichen folgen Schlüssels hinzu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualisiert ein Element im Cache auf der Grundlage seines Schlüssels und setzt das gleitende Ablauf Timeout (sofern vorhanden) zurück.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Entfernt ein Cache Element basierend auf seinem Zeichen folgen Schlüssel.

## <a name="establish-distributed-caching-services"></a>Einrichten verteilter Cache Dienste

Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` . Die in diesem Thema beschriebenen Framework-Implementierungen umfassen Folgendes:

* [Verteilter Speicher Cache](#distributed-memory-cache)
* [Verteilter SQL Server Cache](#distributed-sql-server-cache)
* [Verteilter redis-Cache](#distributed-redis-cache)
* [Verteilter NCache-Cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Verteilter Speicher Cache

Der verteilte Arbeitsspeicher Cache ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) ist eine vom Framework bereitgestellte Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , die Elemente im Arbeitsspeicher speichert. Der verteilte Arbeitsspeicher Cache ist kein tatsächlicher verteilter Cache. Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, auf dem die app ausgeführt wird.

Der verteilte Arbeitsspeicher Cache ist eine nützliche Implementierung:

* In Entwicklungs-und Testszenarien.
* Wenn ein einzelner Server in der Produktionsumgebung verwendet wird, ist der Arbeitsspeicher Verbrauch kein Problem. Durch das Implementieren des Cache für verteilte Arbeitsspeicher wird der zwischengespeicherte Datenspeicher Sie ermöglicht die Implementierung einer echten verteilten cachinglösung in Zukunft, wenn mehrere Knoten oder Fehlertoleranz erforderlich werden.

Die Beispiel-App nutzt den verteilten Arbeitsspeicher Cache, wenn die app in der Entwicklungsumgebung in ausgeführt wird `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Verteilter SQL Server Cache

Die verteilte SQL Server Cache Implementierung ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) ermöglicht es dem verteilten Cache, eine SQL Server Datenbank als Sicherungs Speicher zu verwenden. Zum Erstellen einer SQL Server zwischengespeicherten Element Tabelle in einer SQL Server Instanz können Sie das `sql-cache` Tool verwenden. Das Tool erstellt eine Tabelle mit dem Namen und dem Schema, die Sie angeben.

Erstellen Sie eine Tabelle in SQL Server, indem Sie den `sql-cache create` Befehl ausführen. Geben Sie die SQL Server Instanz ( `Data Source` ), die Datenbank ( `Initial Catalog` ), das Schema (z. b. `dbo` ) und den Tabellennamen (z. b. `TestCache` ) an:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Eine Meldung wird protokolliert, um anzuzeigen, dass das Tool erfolgreich war:

```console
Table and index were created successfully.
```

Die vom Tool erstellte Tabelle `sql-cache` hat das folgende Schema:

![SQLServer-Cache Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Eine APP sollte Cache Werte mithilfe einer Instanz von, nicht mithilfe von bearbeiten <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Die Beispiel-App implementiert in <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> einer nicht Entwicklungsumgebung in `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) werden in der Regel außerhalb der Quell Code Verwaltung gespeichert (z. b. durch den [Geheimnis-Manager](xref:security/app-secrets) oder in *appsettings.json* / *appSettings gespeichert. { Umgebung}. JSON* -Dateien). Die Verbindungs Zeichenfolge kann Anmelde Informationen enthalten, die aus den Quell Code Verwaltungssystemen ausgeschlossen werden sollen.

### <a name="distributed-redis-cache"></a>Verteilte redis Cache

[Redis](https://redis.io/) ist ein Open Source-Datenspeicher im Arbeitsspeicher, der häufig als verteilter Cache verwendet wird.  Sie können eine [Azure redis Cache](https://azure.microsoft.com/services/cache/) für eine in Azure gehostete ASP.net Core-App konfigurieren und eine Azure redis Cache für die lokale Entwicklung verwenden.

Eine App konfiguriert die Cache Implementierung mithilfe einer- <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> Instanz ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ).

Weitere Informationen finden Sie unter [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).

In [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/19542) finden Sie eine Erläuterung zu alternativen Ansätzen für einen lokalen redis Cache.

### <a name="distributed-ncache-cache"></a>Verteilter NCache-Cache

[NCache](https://github.com/Alachisoft/NCache) ist ein Open Source-verteilter, in-Memory-Cache, der System intern in .net und .net Core entwickelt wurde. NCache funktioniert sowohl lokal als auch als verteilter Cache Cluster für eine ASP.net Core-APP, die in Azure oder auf anderen Hostingplattformen ausgeführt wird.

Informationen zum Installieren und Konfigurieren von NCache auf dem lokalen Computer finden Sie im Leitfaden zu den ersten Schritten mit [NCache für Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

So konfigurieren Sie NCache:

1. Installieren [Sie NCache Open Source nuget](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Konfigurieren Sie den Cache Cluster in " [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)".
1. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Verwenden des verteilten Caches

Um die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle zu verwenden, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> von einem beliebigen Konstruktor in der APP an. Die-Instanz wird von der [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection)bereitgestellt.

Wenn die Beispiel-App gestartet wird, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in eingefügt `Startup.Configure` . Die aktuelle Zeit wird mithilfe von zwischengespeichert <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (Weitere Informationen finden Sie unter [generischer Host: ihostapplicationlifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Die Beispiel-APP <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in den eingefügt, `IndexModel` um von der Index Seite verwendet werden zu können.

Jedes Mal, wenn die Index Seite geladen wird, wird der Cache für die zwischengespeicherte Zeit in geprüft `OnGetAsync` . Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Uhrzeit angezeigt. Wenn 20 Sekunden seit dem letzten Zugriff auf die zwischengespeicherte Zeit (beim letzten Laden dieser Seite) verstrichen sind, wird auf der Seite die *zwischengespeicherte Zeit abgelaufen* angezeigt.

Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Zeit, indem Sie die Schaltfläche **zwischengespeicherte Zeit zurücksetzen** auswählen. Die Schaltfläche löst die `OnPostResetCachedTime` Handlermethode aus.

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Es ist nicht erforderlich, eine Singleton-oder Gültigkeitsdauer für <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (zumindest für die integrierten Implementierungen) zu verwenden.
>
> Sie können auch immer dann eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz erstellen, wenn Sie eine Instanz benötigen, anstatt di zu verwenden, aber das Erstellen einer Instanz im Code kann dazu führen, dass Ihr Code schwieriger zu testen ist und gegen das [explizite Abhängigkeits Prinzip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)verstößt.

## <a name="recommendations"></a>Empfehlungen

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Beachten Sie Folgendes, wenn Sie entscheiden, welche Implementierung von für Ihre APP am besten geeignet ist:

* Vorhandene Infrastruktur
* Leistungsanforderungen
* Kosten
* Team Darstellung

Zwischen Speicherungs Lösungen basieren in der Regel auf in-Memory-Speicher, um das schnelle Abrufen von zwischengespeicherten Daten zu ermöglichen, aber der Arbeitsspeicher ist eine begrenzte Ressource und ist aufwändig Speichert nur häufig verwendete Daten in einem Cache.

Im Allgemeinen bietet ein redis Cache einen höheren Durchsatz und eine niedrigere Latenz als bei einem SQL Server Cache. Es ist jedoch normalerweise ein Benchmarkwert erforderlich, um die Leistungsmerkmale von Cache Strategien zu ermitteln.

Wenn SQL Server als Sicherungs Speicher für verteilte Caches verwendet wird, kann sich die Verwendung der gleichen Datenbank für den Cache und den normalen Datenspeicher und-Abruf der APP negativ auf die Leistung beider Anwendungen auswirken. Es wird empfohlen, für den Sicherungs Speicher im verteilten Cache eine dedizierte SQL Server Instanz zu verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Redis Cache in Azure](/azure/azure-cache-for-redis/)
* [SQL-Datenbank in Azure](/azure/sql-database/)
* [ASP.net Core idistributedcache-Anbieter für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bei einem verteilten Cache handelt es sich um einen Cache, der von mehreren App-Servern gemeinsam verwendet wird, die in der Regel als externer Dienst für die App-Server verwaltet werden Ein verteilter Cache kann die Leistung und Skalierbarkeit einer ASP.net Core-App verbessern, insbesondere wenn die APP von einem Cloud-Dienst oder einer Serverfarm gehostet wird.

Ein verteilter Cache hat mehrere Vorteile gegenüber anderen cachingszenarien, in denen zwischengespeicherte Daten auf einzelnen App-Servern gespeichert werden.

Wenn zwischengespeicherte Daten verteilt werden, sind die Daten wie folgt:

* Ist *in* allen Anforderungen an mehrere Server konsistent (konsistent).
* Überstehen Serverneustarts und App-bereit Stellungen.
* Verwendet keinen lokalen Arbeitsspeicher.

Die Konfiguration verteilter Caches ist Implementierungs spezifisch. In diesem Artikel wird beschrieben, wie Sie SQL Server und verteilte redis-Caches konfigurieren. Implementierungen von Drittanbietern sind ebenfalls verfügbar, wie z. b. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)). Unabhängig davon, welche Implementierung ausgewählt ist, interagiert die App mithilfe der-Schnittstelle mit dem Cache <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie einen SQL Server verteilten Cache verwenden möchten, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.

Um einen verteilten redis-Cache zu verwenden, verweisen Sie auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) , und fügen Sie dem Paket [Microsoft. Extensions. Caching. stackexchangeredis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) einen Paket Verweis hinzu. Das redis-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten. Daher müssen Sie das redis-Paket separat in der Projektdatei referenzieren.

Um den verteilten NCache-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket einen Paket Verweis hinzu. Das NCache-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten. Daher müssen Sie in der Projektdatei separat auf das NCache-Paket verweisen.

## <a name="idistributedcache-interface"></a>Idistributedcache-Schnittstelle

Die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle stellt die folgenden Methoden zum Bearbeiten von Elementen in der Implementierung verteilter Caches bereit:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akzeptiert einen Zeichen folgen Schlüssel und ruft ein zwischengespeichertes Element als-Array ab, `byte[]` Wenn es im Cache gefunden wird.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Fügt dem Cache ein Element (als `byte[]` Array) mithilfe eines Zeichen folgen Schlüssels hinzu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualisiert ein Element im Cache auf der Grundlage seines Schlüssels und setzt das gleitende Ablauf Timeout (sofern vorhanden) zurück.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Entfernt ein Cache Element basierend auf seinem Zeichen folgen Schlüssel.

## <a name="establish-distributed-caching-services"></a>Einrichten verteilter Cache Dienste

Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` . Die in diesem Thema beschriebenen Framework-Implementierungen umfassen Folgendes:

* [Verteilter Speicher Cache](#distributed-memory-cache)
* [Verteilter SQL Server Cache](#distributed-sql-server-cache)
* [Verteilter redis-Cache](#distributed-redis-cache)
* [Verteilter NCache-Cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Verteilter Speicher Cache

Der verteilte Arbeitsspeicher Cache ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) ist eine vom Framework bereitgestellte Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , die Elemente im Arbeitsspeicher speichert. Der verteilte Arbeitsspeicher Cache ist kein tatsächlicher verteilter Cache. Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, auf dem die app ausgeführt wird.

Der verteilte Arbeitsspeicher Cache ist eine nützliche Implementierung:

* In Entwicklungs-und Testszenarien.
* Wenn ein einzelner Server in der Produktionsumgebung verwendet wird, ist der Arbeitsspeicher Verbrauch kein Problem. Durch das Implementieren des Cache für verteilte Arbeitsspeicher wird der zwischengespeicherte Datenspeicher Sie ermöglicht die Implementierung einer echten verteilten cachinglösung in Zukunft, wenn mehrere Knoten oder Fehlertoleranz erforderlich werden.

Die Beispiel-App nutzt den verteilten Arbeitsspeicher Cache, wenn die app in der Entwicklungsumgebung in ausgeführt wird `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Verteilter SQL Server Cache

Die verteilte SQL Server Cache Implementierung ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) ermöglicht es dem verteilten Cache, eine SQL Server Datenbank als Sicherungs Speicher zu verwenden. Zum Erstellen einer SQL Server zwischengespeicherten Element Tabelle in einer SQL Server Instanz können Sie das `sql-cache` Tool verwenden. Das Tool erstellt eine Tabelle mit dem Namen und dem Schema, die Sie angeben.

Erstellen Sie eine Tabelle in SQL Server, indem Sie den `sql-cache create` Befehl ausführen. Geben Sie die SQL Server Instanz ( `Data Source` ), die Datenbank ( `Initial Catalog` ), das Schema (z. b. `dbo` ) und den Tabellennamen (z. b. `TestCache` ) an:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Eine Meldung wird protokolliert, um anzuzeigen, dass das Tool erfolgreich war:

```console
Table and index were created successfully.
```

Die vom Tool erstellte Tabelle `sql-cache` hat das folgende Schema:

![SQLServer-Cache Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Eine APP sollte Cache Werte mithilfe einer Instanz von, nicht mithilfe von bearbeiten <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Die Beispiel-App implementiert in <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> einer nicht Entwicklungsumgebung in `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) werden in der Regel außerhalb der Quell Code Verwaltung gespeichert (z. b. durch den [Geheimnis-Manager](xref:security/app-secrets) oder in *appsettings.json* / *appSettings gespeichert. { Umgebung}. JSON* -Dateien). Die Verbindungs Zeichenfolge kann Anmelde Informationen enthalten, die aus den Quell Code Verwaltungssystemen ausgeschlossen werden sollen.

### <a name="distributed-redis-cache"></a>Verteilte redis Cache

[Redis](https://redis.io/) ist ein Open Source-Datenspeicher im Arbeitsspeicher, der häufig als verteilter Cache verwendet wird. Sie können redis lokal verwenden, und Sie können eine [Azure redis Cache](https://azure.microsoft.com/services/cache/) für eine in Azure gehostete ASP.net Core-App konfigurieren.

Eine App konfiguriert die Cache Implementierung mithilfe einer- <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> Instanz ( <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> ) in einer nicht-Entwicklungsumgebung in `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

So installieren Sie redis auf dem lokalen Computer:

1. Installieren Sie das [Chocolatey redis-Paket](https://chocolatey.org/packages/redis-64/).
1. Führen Sie `redis-server` an einer Eingabeaufforderung aus.

### <a name="distributed-ncache-cache"></a>Verteilter NCache-Cache

[NCache](https://github.com/Alachisoft/NCache) ist ein Open Source-verteilter, in-Memory-Cache, der System intern in .net und .net Core entwickelt wurde. NCache funktioniert sowohl lokal als auch als verteilter Cache Cluster für eine ASP.net Core-APP, die in Azure oder auf anderen Hostingplattformen ausgeführt wird.

Informationen zum Installieren und Konfigurieren von NCache auf dem lokalen Computer finden Sie im Leitfaden zu den ersten Schritten mit [NCache für Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

So konfigurieren Sie NCache:

1. Installieren [Sie NCache Open Source nuget](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Konfigurieren Sie den Cache Cluster in " [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)".
1. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Verwenden des verteilten Caches

Um die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle zu verwenden, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> von einem beliebigen Konstruktor in der APP an. Die-Instanz wird von der [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection)bereitgestellt.

Wenn die Beispiel-App gestartet wird, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in eingefügt `Startup.Configure` . Die aktuelle Zeit wird mithilfe von zwischengespeichert <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Weitere Informationen finden Sie unter [Webhost: iapplicationlifetime-Schnittstelle](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Die Beispiel-APP <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in den eingefügt, `IndexModel` um von der Index Seite verwendet werden zu können.

Jedes Mal, wenn die Index Seite geladen wird, wird der Cache für die zwischengespeicherte Zeit in geprüft `OnGetAsync` . Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Uhrzeit angezeigt. Wenn 20 Sekunden seit dem letzten Zugriff auf die zwischengespeicherte Zeit (beim letzten Laden dieser Seite) verstrichen sind, wird auf der Seite die *zwischengespeicherte Zeit abgelaufen* angezeigt.

Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Zeit, indem Sie die Schaltfläche **zwischengespeicherte Zeit zurücksetzen** auswählen. Die Schaltfläche löst die `OnPostResetCachedTime` Handlermethode aus.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Es ist nicht erforderlich, eine Singleton-oder Gültigkeitsdauer für <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (zumindest für die integrierten Implementierungen) zu verwenden.
>
> Sie können auch immer dann eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz erstellen, wenn Sie eine Instanz benötigen, anstatt di zu verwenden, aber das Erstellen einer Instanz im Code kann dazu führen, dass Ihr Code schwieriger zu testen ist und gegen das [explizite Abhängigkeits Prinzip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)verstößt.

## <a name="recommendations"></a>Empfehlungen

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Beachten Sie Folgendes, wenn Sie entscheiden, welche Implementierung von für Ihre APP am besten geeignet ist:

* Vorhandene Infrastruktur
* Leistungsanforderungen
* Kosten
* Team Darstellung

Zwischen Speicherungs Lösungen basieren in der Regel auf in-Memory-Speicher, um das schnelle Abrufen von zwischengespeicherten Daten zu ermöglichen, aber der Arbeitsspeicher ist eine begrenzte Ressource und ist aufwändig Speichert nur häufig verwendete Daten in einem Cache.

Im Allgemeinen bietet ein redis Cache einen höheren Durchsatz und eine niedrigere Latenz als bei einem SQL Server Cache. Es ist jedoch normalerweise ein Benchmarkwert erforderlich, um die Leistungsmerkmale von Cache Strategien zu ermitteln.

Wenn SQL Server als Sicherungs Speicher für verteilte Caches verwendet wird, kann sich die Verwendung der gleichen Datenbank für den Cache und den normalen Datenspeicher und-Abruf der APP negativ auf die Leistung beider Anwendungen auswirken. Es wird empfohlen, für den Sicherungs Speicher im verteilten Cache eine dedizierte SQL Server Instanz zu verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Redis Cache in Azure](/azure/azure-cache-for-redis/)
* [SQL-Datenbank in Azure](/azure/sql-database/)
* [ASP.net Core idistributedcache-Anbieter für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bei einem verteilten Cache handelt es sich um einen Cache, der von mehreren App-Servern gemeinsam verwendet wird, die in der Regel als externer Dienst für die App-Server verwaltet werden Ein verteilter Cache kann die Leistung und Skalierbarkeit einer ASP.net Core-App verbessern, insbesondere wenn die APP von einem Cloud-Dienst oder einer Serverfarm gehostet wird.

Ein verteilter Cache hat mehrere Vorteile gegenüber anderen cachingszenarien, in denen zwischengespeicherte Daten auf einzelnen App-Servern gespeichert werden.

Wenn zwischengespeicherte Daten verteilt werden, sind die Daten wie folgt:

* Ist *in* allen Anforderungen an mehrere Server konsistent (konsistent).
* Überstehen Serverneustarts und App-bereit Stellungen.
* Verwendet keinen lokalen Arbeitsspeicher.

Die Konfiguration verteilter Caches ist Implementierungs spezifisch. In diesem Artikel wird beschrieben, wie Sie SQL Server und verteilte redis-Caches konfigurieren. Implementierungen von Drittanbietern sind ebenfalls verfügbar, wie z. b. [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache)). Unabhängig davon, welche Implementierung ausgewählt ist, interagiert die App mithilfe der-Schnittstelle mit dem Cache <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> .

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie einen SQL Server verteilten Cache verwenden möchten, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. Extensions. Caching. SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) " einen Paket Verweis hinzu.

Um einen verteilten redis-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem Paket " [Microsoft. Extensions. Caching. redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) " einen Paket Verweis hinzu. Das redis-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten. Daher müssen Sie das redis-Paket separat in der Projektdatei referenzieren.

Um den verteilten NCache-Cache zu verwenden, verweisen Sie auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", und fügen Sie dem [ncache. Microsoft. Extensions. Caching. opensource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) -Paket einen Paket Verweis hinzu. Das NCache-Paket ist nicht im `Microsoft.AspNetCore.App` Paket enthalten. Daher müssen Sie in der Projektdatei separat auf das NCache-Paket verweisen.

## <a name="idistributedcache-interface"></a>Idistributedcache-Schnittstelle

Die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle stellt die folgenden Methoden zum Bearbeiten von Elementen in der Implementierung verteilter Caches bereit:

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> : Akzeptiert einen Zeichen folgen Schlüssel und ruft ein zwischengespeichertes Element als-Array ab, `byte[]` Wenn es im Cache gefunden wird.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> : Fügt dem Cache ein Element (als `byte[]` Array) mithilfe eines Zeichen folgen Schlüssels hinzu.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> : Aktualisiert ein Element im Cache auf der Grundlage seines Schlüssels und setzt das gleitende Ablauf Timeout (sofern vorhanden) zurück.
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> : Entfernt ein Cache Element basierend auf seinem Zeichen folgen Schlüssel.

## <a name="establish-distributed-caching-services"></a>Einrichten verteilter Cache Dienste

Registrieren Sie eine Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices` . Die in diesem Thema beschriebenen Framework-Implementierungen umfassen Folgendes:

* [Verteilter Speicher Cache](#distributed-memory-cache)
* [Verteilter SQL Server Cache](#distributed-sql-server-cache)
* [Verteilter redis-Cache](#distributed-redis-cache)
* [Verteilter NCache-Cache](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>Verteilter Speicher Cache

Der verteilte Arbeitsspeicher Cache ( <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ) ist eine vom Framework bereitgestellte Implementierung von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> , die Elemente im Arbeitsspeicher speichert. Der verteilte Arbeitsspeicher Cache ist kein tatsächlicher verteilter Cache. Zwischengespeicherte Elemente werden von der app-Instanz auf dem Server gespeichert, auf dem die app ausgeführt wird.

Der verteilte Arbeitsspeicher Cache ist eine nützliche Implementierung:

* In Entwicklungs-und Testszenarien.
* Wenn ein einzelner Server in der Produktionsumgebung verwendet wird, ist der Arbeitsspeicher Verbrauch kein Problem. Durch das Implementieren des Cache für verteilte Arbeitsspeicher wird der zwischengespeicherte Datenspeicher Sie ermöglicht die Implementierung einer echten verteilten cachinglösung in Zukunft, wenn mehrere Knoten oder Fehlertoleranz erforderlich werden.

Die Beispiel-App nutzt den verteilten Arbeitsspeicher Cache, wenn die app in der Entwicklungsumgebung in ausgeführt wird `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>Verteilter SQL Server Cache

Die verteilte SQL Server Cache Implementierung ( <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ) ermöglicht es dem verteilten Cache, eine SQL Server Datenbank als Sicherungs Speicher zu verwenden. Zum Erstellen einer SQL Server zwischengespeicherten Element Tabelle in einer SQL Server Instanz können Sie das `sql-cache` Tool verwenden. Das Tool erstellt eine Tabelle mit dem Namen und dem Schema, die Sie angeben.

Erstellen Sie eine Tabelle in SQL Server, indem Sie den `sql-cache create` Befehl ausführen. Geben Sie die SQL Server Instanz ( `Data Source` ), die Datenbank ( `Initial Catalog` ), das Schema (z. b. `dbo` ) und den Tabellennamen (z. b. `TestCache` ) an:

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

Eine Meldung wird protokolliert, um anzuzeigen, dass das Tool erfolgreich war:

```console
Table and index were created successfully.
```

Die vom Tool erstellte Tabelle `sql-cache` hat das folgende Schema:

![SQLServer-Cache Tabelle](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> Eine APP sollte Cache Werte mithilfe einer Instanz von, nicht mithilfe von bearbeiten <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> .

Die Beispiel-App implementiert in <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> einer nicht Entwicklungsumgebung in `Startup.ConfigureServices` :

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (und optional <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> und <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ) werden in der Regel außerhalb der Quell Code Verwaltung gespeichert (z. b. durch den [Geheimnis-Manager](xref:security/app-secrets) oder in *appsettings.json* / *appSettings gespeichert. { Umgebung}. JSON* -Dateien). Die Verbindungs Zeichenfolge kann Anmelde Informationen enthalten, die aus den Quell Code Verwaltungssystemen ausgeschlossen werden sollen.

### <a name="distributed-redis-cache"></a>Verteilte redis Cache

[Redis](https://redis.io/) ist ein Open Source-Datenspeicher im Arbeitsspeicher, der häufig als verteilter Cache verwendet wird. Sie können redis lokal verwenden, und Sie können eine [Azure redis Cache](https://azure.microsoft.com/services/cache/) für eine in Azure gehostete ASP.net Core-App konfigurieren.

Eine App konfiguriert die Cache Implementierung mithilfe einer- <xref:Microsoft.Extensions.Caching.Redis.RedisCache> Instanz ( <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

So installieren Sie redis auf dem lokalen Computer:

1. Installieren Sie das [Chocolatey redis-Paket](https://chocolatey.org/packages/redis-64/).
1. Führen Sie `redis-server` an einer Eingabeaufforderung aus.

### <a name="distributed-ncache-cache"></a>Verteilter NCache-Cache

[NCache](https://github.com/Alachisoft/NCache) ist ein Open Source-verteilter, in-Memory-Cache, der System intern in .net und .net Core entwickelt wurde. NCache funktioniert sowohl lokal als auch als verteilter Cache Cluster für eine ASP.net Core-APP, die in Azure oder auf anderen Hostingplattformen ausgeführt wird.

Informationen zum Installieren und Konfigurieren von NCache auf dem lokalen Computer finden Sie im Leitfaden zu den ersten Schritten mit [NCache für Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).

So konfigurieren Sie NCache:

1. Installieren [Sie NCache Open Source nuget](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).
1. Konfigurieren Sie den Cache Cluster in " [Client. ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)".
1. Fügen Sie den folgenden Code zu `Startup.ConfigureServices` hinzu:

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>Verwenden des verteilten Caches

Um die- <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Schnittstelle zu verwenden, fordern Sie eine Instanz von <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> von einem beliebigen Konstruktor in der APP an. Die-Instanz wird von der [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection)bereitgestellt.

Wenn die Beispiel-App gestartet wird, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in eingefügt `Startup.Configure` . Die aktuelle Zeit wird mithilfe von zwischengespeichert <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (Weitere Informationen finden Sie unter [Webhost: iapplicationlifetime-Schnittstelle](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

Die Beispiel-APP <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> wird in den eingefügt, `IndexModel` um von der Index Seite verwendet werden zu können.

Jedes Mal, wenn die Index Seite geladen wird, wird der Cache für die zwischengespeicherte Zeit in geprüft `OnGetAsync` . Wenn die zwischengespeicherte Zeit nicht abgelaufen ist, wird die Uhrzeit angezeigt. Wenn 20 Sekunden seit dem letzten Zugriff auf die zwischengespeicherte Zeit (beim letzten Laden dieser Seite) verstrichen sind, wird auf der Seite die *zwischengespeicherte Zeit abgelaufen* angezeigt.

Aktualisieren Sie die zwischengespeicherte Zeit sofort auf die aktuelle Zeit, indem Sie die Schaltfläche **zwischengespeicherte Zeit zurücksetzen** auswählen. Die Schaltfläche löst die `OnPostResetCachedTime` Handlermethode aus.

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> Es ist nicht erforderlich, eine Singleton-oder Gültigkeitsdauer für <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanzen (zumindest für die integrierten Implementierungen) zu verwenden.
>
> Sie können auch immer dann eine <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> Instanz erstellen, wenn Sie eine Instanz benötigen, anstatt di zu verwenden, aber das Erstellen einer Instanz im Code kann dazu führen, dass Ihr Code schwieriger zu testen ist und gegen das [explizite Abhängigkeits Prinzip](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)verstößt.

## <a name="recommendations"></a>Empfehlungen

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>Beachten Sie Folgendes, wenn Sie entscheiden, welche Implementierung von für Ihre APP am besten geeignet ist:

* Vorhandene Infrastruktur
* Leistungsanforderungen
* Kosten
* Team Darstellung

Zwischen Speicherungs Lösungen basieren in der Regel auf in-Memory-Speicher, um das schnelle Abrufen von zwischengespeicherten Daten zu ermöglichen, aber der Arbeitsspeicher ist eine begrenzte Ressource und ist aufwändig Speichert nur häufig verwendete Daten in einem Cache.

Im Allgemeinen bietet ein redis Cache einen höheren Durchsatz und eine niedrigere Latenz als bei einem SQL Server Cache. Es ist jedoch normalerweise ein Benchmarkwert erforderlich, um die Leistungsmerkmale von Cache Strategien zu ermitteln.

Wenn SQL Server als Sicherungs Speicher für verteilte Caches verwendet wird, kann sich die Verwendung der gleichen Datenbank für den Cache und den normalen Datenspeicher und-Abruf der APP negativ auf die Leistung beider Anwendungen auswirken. Es wird empfohlen, für den Sicherungs Speicher im verteilten Cache eine dedizierte SQL Server Instanz zu verwenden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Redis Cache in Azure](/azure/azure-cache-for-redis/)
* [SQL-Datenbank in Azure](/azure/sql-database/)
* [ASP.net Core idistributedcache-Anbieter für NCache in Webfarmen](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache auf GitHub](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
