---
title: Speicher interne Speicherung in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Daten im Arbeitsspeicher in ASP.NET Core zwischenspeichern können.
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 19e8dc0ae4d5f8fd28d03d5be87c0b1bbf32d940
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107219"
---
# <a name="cache-in-memory-in-aspnet-core"></a>Speicher interne Speicherung in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)und [Steve Smith](https://ardalis.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Grundlagen zum Caching

Die Zwischenspeicherung kann die Leistung und Skalierbarkeit einer APP erheblich verbessern, indem die zum Generieren von Inhalten erforderliche Arbeit reduziert wird. Das Caching funktioniert am besten mit Daten, die sich nur selten ändern **und** sehr aufwendig generiert werden. Durch das Zwischenspeichern wird eine Kopie der Daten erstellt, die viel schneller als die Quelle zurückgegeben werden können. Apps sollten so geschrieben und getestet werden, dass Sie **nie** von zwischengespeicherten Daten abhängen.

ASP.net Core unterstützt mehrere verschiedene Caches. Der einfachste Cache basiert auf [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache). `IMemoryCache` stellt einen im Arbeitsspeicher des Webservers gespeicherten Cache dar. Apps, die in einer Serverfarm (mehrere Server) ausgeführt werden, sollten sicherstellen, dass Sitzungen bei Verwendung des in-Memory-Caches kurz sind. Mithilfe von persistenten Sitzungen wird sichergestellt, dass nachfolgende Anforderungen von einem Client an denselben Server gesendet werden. Azure-Web-Apps verwenden z. b. [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (arr), um alle nachfolgenden Anforderungen an denselben Server weiterzuleiten.

Für nicht persistente Sitzungen in einer Webfarm ist ein [verteilter Cache](distributed.md) erforderlich, um Cache Konsistenzprobleme zu vermeiden. Bei einigen apps kann ein verteilter Cache eine höhere horizontale Skalierung unterstützen als ein in-Memory-Cache. Wenn Sie einen verteilten Cache verwenden, wird der Cache Speicher auf einen externen Prozess verlagert.

Der in-Memory-Cache kann beliebige Objekte speichern. Die verteilte Cache Schnittstelle ist auf beschränkt `byte[]` . Die Cache Elemente im Arbeitsspeicher und im verteilten Cache werden als Schlüssel-Wert-Paare gespeichert.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Nuget-Paket](https://www.nuget.org/packages/System.Runtime.Caching/)) kann mit folgenden Aktionen verwendet werden:

* .NET Standard 2,0 oder höher.
* Alle [.net-Implementierungen](/dotnet/standard/net-standard#net-implementation-support) , die auf .NET Standard 2,0 oder höher ausgerichtet sind. Beispielsweise ASP.net Core 2,0 oder höher.
* .NET Framework 4.5 oder höher.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (in diesem Artikel beschrieben) wird empfohlen, `System.Runtime.Caching` / `MemoryCache` da Sie besser in ASP.net Core integriert ist. Beispielsweise `IMemoryCache` funktioniert nativ mit ASP.net Core- [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

Verwenden `System.Runtime.Caching` / `MemoryCache` Sie als Kompatibilitäts Bridge, wenn Sie Code von ASP.NET 4. x auf ASP.net Core portieren.

## <a name="cache-guidelines"></a>Cache Richtlinien

* Code sollte immer über eine Fall Back-Option zum Abrufen von Daten verfügen und **nicht** davon abhängen, dass ein zwischen gespeicherter Wert verfügbar ist.
* Der Cache verwendet eine knappe Ressource, den Arbeitsspeicher. Beschränken Sie das Cache Wachstum:
  * Verwenden Sie **keine** externe Eingabe als Cache Schlüssel.
  * Verwenden Sie Ablauf, um das Cache Wachstum einzuschränken.
  * [Verwenden Sie SetSize, Size und SizeLimit, um die Cache Größe einzuschränken](#use-setsize-size-and-sizelimit-to-limit-cache-size). Die ASP.net Core Laufzeit schränkt die Cache Größe **nicht** auf Grundlage des Arbeitsspeichers ein. Der Entwickler muss die Cache Größe einschränken.

## <a name="use-imemorycache"></a>Verwenden von IMemoryCache

> [!WARNING]
> Die Verwendung eines *Shared* Memory-Caches aus der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) und das Aufrufen von `SetSize` , oder, `Size` `SizeLimit` um die Cache Größe einzuschränken, kann zu einem Fehler der APP führen. Wenn eine Größenbeschränkung für einen Cache festgelegt wird, müssen alle Einträge beim Hinzufügen eine Größe angeben. Dies kann zu Problemen führen, da Entwickler möglicherweise nicht die vollständige Kontrolle darüber haben, was den freigegebenen Cache verwendet. Beispielsweise wird von Entity Framework Core der freigegebene Cache verwendet, und es wird keine Größe angegeben. Wenn eine APP eine Cache Größenbeschränkung festlegt und EF Core verwendet, löst die APP eine aus `InvalidOperationException` .
> Wenn Sie `SetSize` , `Size` oder verwenden, `SizeLimit` um den Cache einzuschränken, erstellen Sie einen Cache Singleton zum Zwischenspeichern. Weitere Informationen und ein Beispiel finden Sie unter [Verwenden von "setSize", "size" und "SizeLimit", um die Cache Größe einzuschränken](#use-setsize-size-and-sizelimit-to-limit-cache-size).
> Ein frei gegebener Cache wird von anderen Frameworks oder Bibliotheken gemeinsam genutzt. Beispielsweise wird von EF Core der freigegebene Cache verwendet, und es wird keine Größe angegeben. 

In-Memory-Caching ist ein *Dienst* , auf den von einer App mithilfe von [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verwiesen wird. Fordern Sie die `IMemoryCache` Instanz im Konstruktor an:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

Im folgenden Code wird [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) verwendet, um zu überprüfen, ob sich eine Uhrzeit im Cache befindet. Wenn eine Zeit nicht zwischengespeichert wird, wird ein neuer Eintrag erstellt und mit [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)dem Cache hinzugefügt. Die- `CacheKeys` Klasse ist Teil des Download Beispiels.

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

Die aktuelle Uhrzeit und die zwischengespeicherte Zeit werden angezeigt:

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

Der folgende Code verwendet die [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_System_TimeSpan_) -Erweiterungsmethode, um Daten für eine relative Zeit zwischenzuspeichern, ohne das-Objekt zu erstellen `MemoryCacheEntryOptions` .
[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_set)]

Der zwischengespeicherte `DateTime` Wert verbleibt im Cache, während innerhalb des Timeout Zeitraums Anforderungen vorhanden sind.

Der folgende Code verwendet [getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) und [getorkreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) zum Zwischenspeichern von Daten.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Mit dem folgenden Code wird [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) aufgerufen, um die zwischengespeicherte Zeit abzurufen:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

Mit dem folgenden Code wird ein zwischengespeichertes Element mit dem absoluten Ablauf abgerufen oder erstellt:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

Ein zwischengespeichertes Element mit einer gleitenden Ablaufzeit besteht lediglich aus dem Risiko, veraltet zu werden. Wenn der Zugriff häufiger erfolgt als das gleitende Ablauf Intervall, läuft das Element nie ab. Kombinieren Sie einen gleitenden Ablauf mit einem absoluten Ablauf, um sicherzustellen, dass das Element abläuft, sobald die absolute Ablaufzeit abgelaufen ist. Der absolute Ablauf legt eine obere Grenze fest, die angibt, wie lange das Element zwischengespeichert werden kann, während das Element noch früher ablaufen kann, wenn es innerhalb des gleitenden Ablauf Intervalls nicht angefordert wird. Wenn absolute und gleitender Ablauf angegeben werden, werden die Abläufe logisch ORed. Wenn entweder das gleitende Ablauf Intervall *oder* die absolute Ablaufzeit abgelaufen ist, wird das Element aus dem Cache entfernt.

Mit dem folgenden Code wird ein zwischengespeichertes Element mit gleitender *und* absolutem Ablauf abgerufen oder erstellt:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

Der vorangehende Code stellt sicher, dass die Daten nicht länger als die absolute Zeit zwischengespeichert werden.

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> und <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> sind Erweiterungs Methoden in der- <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> Klasse. Diese Methoden erweitern die Funktionalität von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> .

## <a name="memorycacheentryoptions"></a>Memorycacheentryoptions

Im folgenden Beispiel:

* Legt eine gleitende Ablaufzeit fest. Anforderungen, die auf dieses zwischengespeicherte Element zugreifen, setzen die gleitende Ablaufzeit zurück.
* Legt die Cache Priorität auf [CacheItemPriority. neverremove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)fest.
* Legt einen [postevictiondelegaten](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) fest, der nach dem Entfernen des Eintrags aus dem Cache aufgerufen wird. Der Rückruf wird in einem anderen Thread als dem Code ausgeführt, der das Element aus dem Cache entfernt.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Verwenden von "setSize", "size" und "SizeLimit" zum Begrenzen der Cache Größe

Eine `MemoryCache` -Instanz kann optional eine Größenbeschränkung angeben und erzwingen. Das Cache Größenlimit weist keine definierte Maßeinheit auf, da der Cache keinen Mechanismus zum Messen der Größe der Einträge aufweist. Wenn die Cache Größenbeschränkung festgelegt ist, müssen alle Einträge die Größe angeben. Die ASP.net Core Laufzeit schränkt die Cache Größe nicht auf Grundlage des Arbeitsspeichers ein. Der Entwickler muss die Cache Größe einschränken. Die angegebene Größe befindet sich in Einheiten, die vom Entwickler ausgewählt werden.

Beispiel:

* Wenn die Web-App primär Zeichen folgen zwischenspeichert, könnte jede Cache Eintrags Größe die Zeichen folgen Länge aufweisen.
* Die APP kann die Größe aller Einträge als 1 angeben, und die Größenbeschränkung ist die Anzahl der Einträge.

Wenn <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> nicht festgelegt ist, wächst der Cache ohne Bindung. Die ASP.net Core-Laufzeit schneidet den Cache nicht ab, wenn der Systemspeicher gering ist. Apps müssen für Folgendes entworfen werden:

* Beschränken Sie das Cache Wachstum.
* <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>Oder, <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Wenn der verfügbare Arbeitsspeicher begrenzt ist:

Mit dem folgenden Code wird eine nicht für die <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbare, nicht aufgelegte Größe erstellt

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` verfügt über keine Einheiten. Bei zwischengespeicherten Einträgen muss die Größe in allen Einheiten angegeben werden, die Sie am besten als geeignet eingrenzen, wenn die Cache Größe festgelegt wurde Alle Benutzer einer Cache Instanz sollten das gleiche Einheitensystem verwenden. Ein Eintrag wird nicht zwischengespeichert, wenn die Summe der zwischengespeicherten Eintrags Größen den von angegebenen Wert überschreitet `SizeLimit` . Wenn keine Cache Größenbeschränkung festgelegt ist, wird die für den Eintrag festgelegte Cache Größe ignoriert.

Der folgende Code wird `MyMemoryCache` mit dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert.

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

`MyMemoryCache` wird als unabhängiger Speicher Cache für Komponenten erstellt, die diesen Größen eingeschränkten Cache beachten, und wissen, wie die Größe des Cache Eintrags entsprechend festgelegt wird.

Der folgende Code verwendet `MyMemoryCache` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

Die Größe des Cache Eintrags kann von <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> oder den Erweiterungs Methoden festgelegt werden <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> :

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` versucht, den angegebenen Prozentsatz des Caches in der folgenden Reihenfolge zu entfernen:

* Alle abgelaufenen Elemente.
* Elemente nach Priorität. Elemente der niedrigsten Priorität werden zuerst entfernt.
* Zuletzt verwendete Objekte.
* Elemente mit dem frühesten absoluten Ablauf.
* Elemente mit der frühesten gleitenden Ablaufzeit.

Fixierte Elemente mit Priorität <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> werden nie entfernt. Der folgende Code entfernt ein Cache Element und ruft auf `Compact` :

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Weitere Informationen finden Sie [unter Compact Source auf GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Cache Abhängigkeiten

Im folgenden Beispiel wird gezeigt, wie ein Cache Eintrag abläuft, wenn ein abhängiger Eintrag abläuft. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Dem zwischengespeicherten Element wird ein hinzugefügt. Wenn `Cancel` für aufgerufen wird `CancellationTokenSource` , werden beide Cache Einträge entfernt.

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

Durch <xref:System.Threading.CancellationTokenSource> die Verwendung von können mehrere Cache Einträge als Gruppe entfernt werden. Mit dem `using` Muster im obigen Code erben Cache Einträge, die innerhalb des- `using` Blocks erstellt werden, Trigger und Ablauf Einstellungen.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Das Ablaufdatum wird im Hintergrund nicht angezeigt. Es gibt keinen Timer, der den Cache aktiv nach abgelaufenen Elementen scannt. Jede Aktivität im Cache ( `Get` , `Set` , `Remove` ) kann eine Hintergrund Überprüfung für abgelaufene Elemente auslöst. Ein Timer für `CancellationTokenSource` ( <xref:System.Threading.CancellationTokenSource.CancelAfter*> ) entfernt ebenfalls den Eintrag und löst eine Überprüfung abgelaufener Elemente aus. Im folgenden Beispiel wird [CancellationTokenSource (TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) für das registrierte Token verwendet. Wenn dieses Token ausgelöst wird, wird der Eintrag sofort entfernt, und die Entfernungs Rückrufe werden ausgelöst:

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* Bei Verwendung eines Rückrufs zum erneuten Auffüllen eines Cache Elements:

  * Mehrere Anforderungen können den zwischengespeicherten Schlüsselwert leer finden, da der Rückruf noch nicht abgeschlossen wurde.
  * Dies kann dazu führen, dass mehrere Threads das zwischengespeicherte Element neu auffüllen.

* Wenn ein Cache Eintrag verwendet wird, um einen anderen zu erstellen, kopiert das untergeordnete Element die Ablauf Token und zeitbasierten Ablauf Einstellungen des übergeordneten Eintrags. Das untergeordnete Element ist nicht abgelaufen, wenn der übergeordnete Eintrag manuell entfernt oder aktualisiert wird.

* Verwenden <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> Sie, um die Rückrufe festzulegen, die ausgelöst werden, nachdem der Cache Eintrag aus dem Cache entfernt wurde.
* Für die meisten apps `IMemoryCache` ist aktiviert. Wenn z. b `AddMvc` `AddControllersWithViews` .,,, `AddRazorPages` `AddMvcCore().AddRazorViewEngine` und viele andere `Add{Service}` Methoden in `ConfigureServices` aufgerufen werden, wird aktiviert `IMemoryCache` . Für apps, die keine der vorangehenden Methoden aufrufen `Add{Service}` , muss möglicherweise in aufgerufen werden <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> `ConfigureServices` .

## <a name="background-cache-update"></a>Cache Update im Hintergrund

Verwenden Sie einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) wie <xref:Microsoft.Extensions.Hosting.IHostedService> zum Aktualisieren des Caches. Der Hintergrunddienst kann die Einträge neu berechnen und Sie dann nur dann dem Cache zuweisen, wenn Sie bereit sind.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
Von [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo)und [Steve Smith](https://ardalis.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="caching-basics"></a>Grundlagen zum Caching

Die Zwischenspeicherung kann die Leistung und Skalierbarkeit einer APP erheblich verbessern, indem die zum Generieren von Inhalten erforderliche Arbeit reduziert wird. Das Caching funktioniert am besten mit Daten, die sich nur selten ändern. Durch das Zwischenspeichern wird eine Kopie der Daten erstellt, die viel schneller als aus der ursprünglichen Quelle zurückgegeben werden können. Code sollte so geschrieben und getestet werden, dass er **nie** von zwischengespeicherten Daten abhängt.

ASP.net Core unterstützt mehrere verschiedene Caches. Der einfachste Cache basiert auf dem [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), der einen Cache darstellt, der im Arbeitsspeicher des Webservers gespeichert ist. Apps, die in einer Serverfarm (mehrere Server) ausgeführt werden, sollten sicherstellen, dass Sitzungen bei Verwendung des in-Memory-Caches kurz sind. Mithilfe von persistenten Sitzungen wird sichergestellt, dass spätere Anforderungen von einem Client an denselben Server gesendet werden. Azure-Web-Apps verwenden z. b. [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (arr), um alle Anforderungen von einem Benutzer-Agent an denselben Server weiterzuleiten.

Für nicht persistente Sitzungen in einer Webfarm ist ein [verteilter Cache](distributed.md) erforderlich, um Cache Konsistenzprobleme zu vermeiden. Bei einigen apps kann ein verteilter Cache eine höhere horizontale Skalierung unterstützen als ein in-Memory-Cache. Wenn Sie einen verteilten Cache verwenden, wird der Cache Speicher auf einen externen Prozess verlagert.

Der in-Memory-Cache kann beliebige Objekte speichern. Die verteilte Cache Schnittstelle ist auf beschränkt `byte[]` . Die Cache Elemente im Arbeitsspeicher und im verteilten Cache werden als Schlüssel-Wert-Paare gespeichert.

## <a name="systemruntimecachingmemorycache"></a>System. Runtime. Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([Nuget-Paket](https://www.nuget.org/packages/System.Runtime.Caching/)) kann mit folgenden Aktionen verwendet werden:

* .NET Standard 2,0 oder höher.
* Alle [.net-Implementierungen](/dotnet/standard/net-standard#net-implementation-support) , die auf .NET Standard 2,0 oder höher ausgerichtet sind. Beispielsweise ASP.net Core 2,0 oder höher.
* .NET Framework 4.5 oder höher.

[Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` (in diesem Artikel beschrieben) wird empfohlen, `System.Runtime.Caching` / `MemoryCache` da Sie besser in ASP.net Core integriert ist. Beispielsweise `IMemoryCache` funktioniert nativ mit ASP.net Core- [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).

Verwenden `System.Runtime.Caching` / `MemoryCache` Sie als Kompatibilitäts Bridge, wenn Sie Code von ASP.NET 4. x auf ASP.net Core portieren.

## <a name="cache-guidelines"></a>Cache Richtlinien

* Code sollte immer über eine Fall Back-Option zum Abrufen von Daten verfügen und **nicht** davon abhängen, dass ein zwischen gespeicherter Wert verfügbar ist.
* Der Cache verwendet eine knappe Ressource, den Arbeitsspeicher. Beschränken Sie das Cache Wachstum:
  * Verwenden Sie **keine** externe Eingabe als Cache Schlüssel.
  * Verwenden Sie Ablauf, um das Cache Wachstum einzuschränken.
  * [Verwenden Sie SetSize, Size und SizeLimit, um die Cache Größe einzuschränken](#use-setsize-size-and-sizelimit-to-limit-cache-size). Die ASP.net Core Laufzeit schränkt die Cache Größe nicht auf Grundlage des Arbeitsspeichers ein. Der Entwickler muss die Cache Größe einschränken.

## <a name="using-imemorycache"></a>Verwenden von IMemoryCache

> [!WARNING]
> Die Verwendung eines *Shared* Memory-Caches aus der [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) und das Aufrufen von `SetSize` , oder, `Size` `SizeLimit` um die Cache Größe einzuschränken, kann zu einem Fehler der APP führen. Wenn eine Größenbeschränkung für einen Cache festgelegt wird, müssen alle Einträge beim Hinzufügen eine Größe angeben. Dies kann zu Problemen führen, da Entwickler möglicherweise nicht die vollständige Kontrolle darüber haben, was den freigegebenen Cache verwendet. Beispielsweise wird von Entity Framework Core der freigegebene Cache verwendet, und es wird keine Größe angegeben. Wenn eine APP eine Cache Größenbeschränkung festlegt und EF Core verwendet, löst die APP eine aus `InvalidOperationException` .
> Wenn Sie `SetSize` , `Size` oder verwenden, `SizeLimit` um den Cache einzuschränken, erstellen Sie einen Cache Singleton zum Zwischenspeichern. Weitere Informationen und ein Beispiel finden Sie unter [Verwenden von "setSize", "size" und "SizeLimit", um die Cache Größe einzuschränken](#use-setsize-size-and-sizelimit-to-limit-cache-size).

In-Memory-Caching ist ein *Dienst* , auf den von ihrer App mithilfe von [Abhängigkeitsinjektion](../../fundamentals/dependency-injection.md)verwiesen wird. Anrufen `AddMemoryCache` in `ConfigureServices` :

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

Fordern Sie die `IMemoryCache` Instanz im Konstruktor an:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

`IMemoryCache` erfordert das nuget-Paket [Microsoft. Extensions. Caching. Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), das im [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)verfügbar ist.

Im folgenden Code wird [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) verwendet, um zu überprüfen, ob sich eine Uhrzeit im Cache befindet. Wenn eine Zeit nicht zwischengespeichert wird, wird ein neuer Eintrag erstellt und mit [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)dem Cache hinzugefügt.

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

Die aktuelle Uhrzeit und die zwischengespeicherte Zeit werden angezeigt:

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

Der zwischengespeicherte `DateTime` Wert verbleibt im Cache, während innerhalb des Timeout Zeitraums Anforderungen vorhanden sind. Die folgende Abbildung zeigt die aktuelle Uhrzeit und eine ältere Zeit, die aus dem Cache abgerufen wurde:

![Index Sicht mit zwei unterschiedlichen Uhrzeiten](memory/_static/time.png)

Der folgende Code verwendet [getorcreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) und [getorkreateasync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) zum Zwischenspeichern von Daten.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

Mit dem folgenden Code wird [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) aufgerufen, um die zwischengespeicherte Zeit abzurufen:

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> und [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) sind Erweiterungs Methoden, die Teil der [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) -Klasse sind und die Funktionen von erweitern <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> . Eine Beschreibung anderer Cache Methoden finden Sie unter [IMemoryCache-Methoden](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) und [CacheExtensions-Methoden](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) .

## <a name="memorycacheentryoptions"></a>Memorycacheentryoptions

Im folgenden Beispiel:

* Legt eine gleitende Ablaufzeit fest. Anforderungen, die auf dieses zwischengespeicherte Element zugreifen, setzen die gleitende Ablaufzeit zurück.
* Legt die Cache Priorität auf fest `CacheItemPriority.NeverRemove` .
* Legt einen [postevictiondelegaten](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) fest, der nach dem Entfernen des Eintrags aus dem Cache aufgerufen wird. Der Rückruf wird in einem anderen Thread als dem Code ausgeführt, der das Element aus dem Cache entfernt.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>Verwenden von "setSize", "size" und "SizeLimit" zum Begrenzen der Cache Größe

Eine `MemoryCache` -Instanz kann optional eine Größenbeschränkung angeben und erzwingen. Das Cache Größenlimit weist keine definierte Maßeinheit auf, da der Cache keinen Mechanismus zum Messen der Größe der Einträge aufweist. Wenn die Cache Größenbeschränkung festgelegt ist, müssen alle Einträge die Größe angeben. Die ASP.net Core Laufzeit schränkt die Cache Größe nicht auf Grundlage des Arbeitsspeichers ein. Der Entwickler muss die Cache Größe einschränken. Die angegebene Größe befindet sich in Einheiten, die vom Entwickler ausgewählt werden.

Beispiel:

* Wenn die Web-App primär Zeichen folgen zwischenspeichert, könnte jede Cache Eintrags Größe die Zeichen folgen Länge aufweisen.
* Die APP kann die Größe aller Einträge als 1 angeben, und die Größenbeschränkung ist die Anzahl der Einträge.

Wenn <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> nicht festgelegt ist, wächst der Cache ohne Bindung. Die ASP.net Core Laufzeit schneidet den Cache nicht ab, wenn der Systemspeicher gering ist. Apps sind für Folgendes ausgelegt:

* Beschränken Sie das Cache Wachstum.
* <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>Oder, <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> Wenn der verfügbare Arbeitsspeicher begrenzt ist:

Mit dem folgenden Code wird eine nicht für die <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbare, nicht aufgelegte Größe erstellt

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

`SizeLimit` verfügt über keine Einheiten. Bei zwischengespeicherten Einträgen muss die Größe in allen Einheiten angegeben werden, die Sie am besten als geeignet eingrenzen, wenn die Cache Größe festgelegt wurde Alle Benutzer einer Cache Instanz sollten das gleiche Einheitensystem verwenden. Ein Eintrag wird nicht zwischengespeichert, wenn die Summe der zwischengespeicherten Eintrags Größen den von angegebenen Wert überschreitet `SizeLimit` . Wenn keine Cache Größenbeschränkung festgelegt ist, wird die für den Eintrag festgelegte Cache Größe ignoriert.

Der folgende Code wird `MyMemoryCache` mit dem Container für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) registriert.

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` wird als unabhängiger Speicher Cache für Komponenten erstellt, die diesen Größen eingeschränkten Cache beachten, und wissen, wie die Größe des Cache Eintrags entsprechend festgelegt wird.

Der folgende Code verwendet `MyMemoryCache` :

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

Die Größe des Cache Eintrags kann nach [Größe](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) oder der Erweiterungsmethode [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) festgelegt werden:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a>MemoryCache. Compact

`MemoryCache.Compact` versucht, den angegebenen Prozentsatz des Caches in der folgenden Reihenfolge zu entfernen:

* Alle abgelaufenen Elemente.
* Elemente nach Priorität. Elemente der niedrigsten Priorität werden zuerst entfernt.
* Zuletzt verwendete Objekte.
* Elemente mit dem frühesten absoluten Ablauf.
* Elemente mit der frühesten gleitenden Ablaufzeit.

Fixierte Elemente mit Priorität <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> werden nie entfernt.

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

Weitere Informationen finden Sie [unter Compact Source auf GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) .

## <a name="cache-dependencies"></a>Cache Abhängigkeiten

Im folgenden Beispiel wird gezeigt, wie ein Cache Eintrag abläuft, wenn ein abhängiger Eintrag abläuft. <xref:Microsoft.Extensions.Primitives.CancellationChangeToken>Dem zwischengespeicherten Element wird ein hinzugefügt. Wenn `Cancel` für aufgerufen wird `CancellationTokenSource` , werden beide Cache Einträge entfernt.

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

Durch `CancellationTokenSource` die Verwendung von können mehrere Cache Einträge als Gruppe entfernt werden. Mit dem `using` Muster im obigen Code erben Cache Einträge, die innerhalb des- `using` Blocks erstellt werden, Trigger und Ablauf Einstellungen.

## <a name="additional-notes"></a>Zusätzliche Hinweise

* Bei Verwendung eines Rückrufs zum erneuten Auffüllen eines Cache Elements:

  * Mehrere Anforderungen können den zwischengespeicherten Schlüsselwert leer finden, da der Rückruf noch nicht abgeschlossen wurde.
  * Dies kann dazu führen, dass mehrere Threads das zwischengespeicherte Element neu auffüllen.

* Wenn ein Cache Eintrag verwendet wird, um einen anderen zu erstellen, kopiert das untergeordnete Element die Ablauf Token und zeitbasierten Ablauf Einstellungen des übergeordneten Eintrags. Das untergeordnete Element ist nicht abgelaufen, wenn der übergeordnete Eintrag manuell entfernt oder aktualisiert wird.

* Verwenden Sie [postevictioncallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) , um die Rückrufe festzulegen, die ausgelöst werden, nachdem der Cache Eintrag aus dem Cache entfernt wurde.

## <a name="background-cache-update"></a>Cache Update im Hintergrund

Verwenden Sie einen [Hintergrunddienst](xref:fundamentals/host/hosted-services) wie <xref:Microsoft.Extensions.Hosting.IHostedService> zum Aktualisieren des Caches. Der Hintergrunddienst kann die Einträge neu berechnen und Sie dann nur dann dem Cache zuweisen, wenn Sie bereit sind.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
