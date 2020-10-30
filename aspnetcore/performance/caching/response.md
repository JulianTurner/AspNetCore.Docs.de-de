---
title: Zwischenspeichern von Antworten in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die Zwischenspeicherung von Antworten verwenden können, um die Bandbreitenanforderungen zu senken und die Leistung von ASP.NET Core-Apps zu steigern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
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
uid: performance/caching/response
ms.openlocfilehash: 2864de5b9931ed255569cb087c67c71004c4df92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059012"
---
# <a name="response-caching-in-aspnet-core"></a>Zwischenspeichern von Antworten in ASP.net Core

Von [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT)und [Steve Smith](https://ardalis.com/)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Beim Zwischenspeichern von Antworten wird die Anzahl von Anforderungen reduziert, die ein Client oder Proxy an einen Webserver sendet. Durch das Zwischenspeichern von Antworten wird auch der Arbeitsaufwand reduziert, der vom Webserver zum Generieren einer Antwort ausgeführt wird. Das Zwischenspeichern von Antworten wird durch Header gesteuert, die angeben, wie die Client-, Proxy-und Middleware Antworten zwischenspeichern soll.

Das [responsecache-Attribut](#responsecache-attribute) nimmt an der Einstellung von Cache Headern für Antworten Teil. Clients und zwischen Proxys sollten die Header zum Zwischenspeichern von Antworten unter der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen.

Verwenden Sie für die serverseitige Zwischenspeicherung, die der HTTP 1,1-Cache Spezifikation folgt, die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware). Die Middleware kann die <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Eigenschaften verwenden, um das serverseitige zwischen Speicherungs Verhalten zu beeinflussen.

## <a name="http-based-response-caching"></a>HTTP-basiertes Zwischenspeichern von Antworten

In der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234) wird beschrieben, wie sich die Internet Caches Verhalten. Der primäre HTTP-Header, der zum Zwischenspeichern verwendet wird, ist [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), der zum Angeben von Cache *Anweisungen* verwendet wird. Die Direktiven steuern das zwischen Speicherungs Verhalten, da Anforderungen von Clients auf Server und Antworten von Servern an Clients zurückgeben. Anforderungen und Antworten werden durch Proxy Server verschoben, und Proxy Server müssen ebenfalls der HTTP 1,1-cachingspezifikation entsprechen.

Allgemeine `Cache-Control` Anweisungen sind in der folgenden Tabelle aufgeführt.

| Anweisung                                                       | Aktion |
| --------------------------------------------------------------- | ------ |
| [öffentlich](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Die Antwort kann in einem Cache gespeichert werden. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Die Antwort darf nicht von einem freigegebenen Cache gespeichert werden. In einem privaten Cache kann die Antwort gespeichert und wieder verwendet werden. |
| [Max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Der Client akzeptiert keine Antwort, deren Alter größer ist als die angegebene Anzahl von Sekunden. Beispiele: `max-age=60` (60 Sekunden), `max-age=2592000` (1 Monat) |
| [No-Cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Bei Anforderungen** : ein Cache darf keine gespeicherte Antwort verwenden, um die Anforderung zu erfüllen. Der Ursprungsserver generiert die Antwort für den Client erneut, und die Middleware aktualisiert die gespeicherte Antwort im Cache.<br><br>**Bei Antworten** : die Antwort darf nicht für eine nachfolgende Anforderung ohne Validierung auf dem Ursprungsserver verwendet werden. |
| [No-Store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Bei Anforderungen** : die Anforderung darf nicht in einem Cache gespeichert werden.<br><br>**Bei Antworten** : ein Cache darf keinen Teil der Antwort speichern. |

Andere Cache Header, die eine Rolle beim Caching spielen, sind in der folgenden Tabelle aufgeführt.

| Header                                                     | Funktion |
| ---------------------------------------------------------- | -------- |
| [Age](https://tools.ietf.org/html/rfc7234#section-5.1) (Alter)     | Eine Schätzung der Zeitspanne (in Sekunden), seit die die Antwort auf dem Ursprungsserver generiert oder erfolgreich überprüft wurde. |
| [Laufzeit](https://tools.ietf.org/html/rfc7234#section-5.3) | Die Zeit, nach der die Antwort als veraltet eingestuft wird. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Besteht aus Gründen der Abwärtskompatibilität mit HTTP/1.0-Caches zum Festlegen des `no-cache` Verhaltens. Wenn der `Cache-Control` Header vorhanden ist, `Pragma` wird der Header ignoriert. |
| [Abweichen](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Gibt an, dass eine zwischengespeicherte Antwort nicht gesendet werden darf, es sei denn, alle `Vary` Header Felder stimmen mit der ursprünglichen Anforderung der zwischengespeicherten Antwort und der neuen Anforderung gleich. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>HTTP-basiertes Zwischenspeichern von Anforderungen Cache-Control Direktiven

Die [http 1,1-Cache Spezifikation für den Cache-Control-Header](https://tools.ietf.org/html/rfc7234#section-5.2) erfordert, dass ein Cache einen gültigen Header berücksichtigt, `Cache-Control` der vom Client gesendet wird. Ein Client kann Anforderungen mit einem `no-cache` Header Wert senden und erzwingen, dass der Server eine neue Antwort für jede Anforderung generiert.

`Cache-Control`Wenn Sie das Ziel der HTTP-Zwischenspeicherung in Erwägung gezogen haben, ist es sinnvoll, Client Anforderungs Header zu berücksichtigen. Unter der offiziellen Spezifikation soll das Caching die Latenz und den Netzwerk Aufwand bei der Erfüllung von Anforderungen in einem Netzwerk von Clients, Proxys und Servern reduzieren. Es ist nicht notwendigerweise eine Möglichkeit, die Last auf einem Ursprungsserver zu steuern.

Es gibt keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die [Middleware](xref:performance/caching/middleware) zum Zwischenspeichern von Antworten verwendet wird, da die Middleware die offizielle cachingspezifikation befolgt. [Geplante Erweiterungen der Middleware](https://github.com/dotnet/AspNetCore/issues/2612) sind die Möglichkeit, die Middleware so zu konfigurieren, dass der Header einer Anforderung ignoriert wird, `Cache-Control` Wenn eine zwischengespeicherte Antwort verarbeitet wird. Geplante Erweiterungen bieten die Möglichkeit, die Serverlast besser zu steuern.

## <a name="other-caching-technology-in-aspnet-core"></a>Andere cachingtechnologie in ASP.net Core

### <a name="in-memory-caching"></a>In-Memory-Caching

In-Memory-Caching verwendet Server Arbeitsspeicher zum Speichern von zwischengespeicherten Daten. Diese Art der Zwischenspeicherung eignet sich für einen einzelnen Server oder mehrere Server, die persistente *Sitzungen* verwenden. Mit "persistente Sitzungen" können die Anforderungen von einem Client für die Verarbeitung immer an denselben Server weitergeleitet werden.

Weitere Informationen finden Sie unter <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Verteilter Cache

Verwenden Sie einen verteilten Cache, um Daten im Arbeitsspeicher zu speichern, wenn die app in einer Cloud-oder Serverfarm gehostet wird. Der Cache wird auf den Servern freigegeben, die Anforderungen verarbeiten. Ein Client kann eine Anforderung übermitteln, die von einem beliebigen Server in der Gruppe verarbeitet wird, wenn zwischengespeicherte Daten für den Client verfügbar sind. ASP.net Core funktioniert mit verteilten Caches SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)und [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Cache-Taghilfsprogramm

Zwischenspeichern des Inhalts aus einer MVC-Ansicht oder- Razor Seite mit dem cachetaghilfsprogramm. Das Cache-taghilfsprogramm verwendet das Zwischenspeichern im Arbeitsspeicher zum Speichern von Daten.

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Taghilfsprogramm für verteilten Cache

Zwischenspeichern des Inhalts aus einer MVC-Ansicht oder- Razor Seite in verteilten Cloud-oder Webfarm Szenarios mit dem taghilfsprogramm für verteilte Caches. Das taghilfsprogramm für verteilte Caches verwendet SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)oder [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) zum Speichern von Daten.

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>Response Cache-Attribut

Der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> gibt die Parameter an, die zum Festlegen geeigneter Header beim Zwischenspeichern von Antworten erforderlich sind.

> [!WARNING]
> Deaktivieren Sie das Zwischenspeichern für Inhalte, die Informationen für authentifizierte Clients enthalten. Das Zwischenspeichern sollte nur für Inhalt aktiviert werden, der sich nicht auf der Grundlage der Identität eines Benutzers ändert, oder ob ein Benutzer angemeldet ist.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> variiert die gespeicherte Antwort anhand der Werte der angegebenen Liste von Abfrage Schlüsseln. Wenn ein einzelner Wert `*` bereitgestellt wird, variiert die Middleware mit den Antworten aller Parameter der Anforderungs Abfrage Zeichenfolge.

Die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware) muss aktiviert sein, um die Eigenschaft festzulegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> . Andernfalls wird eine Lauf Zeit Ausnahme ausgelöst. Es gibt keinen entsprechenden HTTP-Header für die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Eigenschaft. Die-Eigenschaft ist eine HTTP-Funktion, die von der zwischenware zum Zwischenspeichern von Antworten Damit die Middleware eine zwischengespeicherte Antwort bereitstellt, müssen die Abfrage Zeichenfolge und der Abfrage Zeichenfolgen-Wert mit einer vorherigen Anforderung identisch sein. Stellen Sie sich z. b. die Abfolge der in der folgenden Tabelle gezeigten Anforderungen und Ergebnisse vor.

| Anforderung                          | Ergebnis                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Vom Server zurückgegeben. |
| `http://example.com?key1=value1` | Von der Middleware zurückgegeben. |
| `http://example.com?key1=value2` | Vom Server zurückgegeben. |

Die erste Anforderung wird vom Server zurückgegeben und in der Middleware zwischengespeichert. Die zweite Anforderung wird von Middleware zurückgegeben, da die Abfrage Zeichenfolge mit der vorherigen Anforderung übereinstimmt. Die dritte Anforderung befindet sich nicht im middlewarecache, da der Wert der Abfrage Zeichenfolge mit einer vorherigen Anforderung nicht identisch ist.

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Wird verwendet, um (via) zu konfigurieren und zu erstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter` . Der `ResponseCacheFilter` führt die Aktualisierung der entsprechenden HTTP-Header und Features der Antwort aus. Der Filter:

* Entfernt alle vorhandenen Header für `Vary` , `Cache-Control` und `Pragma` .
* Schreibt die entsprechenden Header auf der Grundlage der im festgelegten Eigenschaften <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> .
* Aktualisiert das HTTP-Feature zum Zwischenspeichern von Antworten, wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> festgelegt ist

### <a name="vary"></a>Abweichen

Dieser Header wird nur geschrieben, wenn die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Eigenschaft festgelegt wird. Die Eigenschaft, die auf den Wert der Eigenschaft festgelegt `Vary` ist. Im folgenden Beispiel wird die- <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Eigenschaft verwendet:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Verwenden Sie die Beispiel-APP, um die Antwortheader mit den Netzwerk Tools des Browsers anzuzeigen. Die folgenden Antwortheader werden mit der Cache1-Seiten Antwort gesendet:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore und Location. None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> überschreibt die meisten anderen Eigenschaften. Wenn diese Eigenschaft auf festgelegt ist `true` , `Cache-Control` wird der-Header auf festgelegt `no-store` . Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> auf festgelegt ist `None` :

* Für `Cache-Control` ist `no-store,no-cache` festgelegt.
* Für `Pragma` ist `no-cache` festgelegt.

Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` den Wert <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> hat und den Wert `None` , `Cache-Control` und `Pragma` auf festgelegt ist `no-cache` .

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> wird in der Regel `true` für Fehlerseiten auf festgelegt. Die Cache2-Seite in der Beispiel-app erzeugt Antwortheader, die den Client anweisen, die Antwort nicht zu speichern.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Die Beispiel-App gibt die Cache2-Seite mit den folgenden Headern zurück:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Speicherort und Dauer

Um das Zwischenspeichern zu aktivieren, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> muss auf einen positiven Wert festgelegt werden und <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> muss entweder `Any` (Standard) oder sein `Client` . Das Framework legt den- `Cache-Control` Header auf den Speicherort Wert fest, gefolgt vom `max-age` der Antwort.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>die Optionen von `Any` und `Client` werden in `Cache-Control` Header Werte von `public` `private` bzw. übersetzt. Wie im Abschnitt [NoStore und Location. None](#nostore-and-locationnone) vermerkt, legt das Festlegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> von auf `None` sowohl `Cache-Control` -als auch- `Pragma` Header auf fest `no-cache` .

`Location.Any`( `Cache-Control` festgelegt auf `public` ) gibt an, dass der Wert des *Clients oder eines beliebigen zwischen Proxys zwischen* gespeichert werden kann, einschließlich der Zwischenspeicherung von zwischen [Speichern](xref:performance/caching/middleware)

`Location.Client` ( `Cache-Control` festgelegt auf `private` ) gibt an, dass der Wert *nur vom Client* zwischengespeichert werden darf. Der Wert kann nicht zwischengespeichert werden, einschließlich der [Middleware](xref:performance/caching/middleware)zum Zwischenspeichern von Antworten.

Cache Steuerungs Header bieten lediglich Anleitungen für Clients und Vermittler Proxys, wann und wie Antworten zwischengespeichert werden. Es gibt keine Garantie dafür, dass Clients und Proxys die [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen. Die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware) folgt immer den durch die Spezifikation vorgegebenen Cache Regeln.

Das folgende Beispiel zeigt das Cache3 Page Model aus der Beispiel-APP und die über das Festlegen <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> und belassen des Standard <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> Werts erstellten Header:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Die Beispiel-App gibt die Cache3-Seite mit dem folgenden Header zurück:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Cache profile

Anstatt die Einstellungen des Antwort Caches für viele Controller Aktions Attribute zu duplizieren, können Cache Profile beim Einrichten von MVC/pages in als Optionen konfiguriert werden Razor `Startup.ConfigureServices` . Werte, die in einem Cache Profil gefunden werden, auf das verwiesen wird, werden als Standardwerte verwendet <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> und von allen Eigenschaften überschrieben, die für das Attribut angegeben sind.

Einrichten eines Cache Profils. Das folgende Beispiel zeigt ein Cache Profil mit 30 Sekunden in der Beispiel-APP `Startup.ConfigureServices` :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](response/samples/3.x/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Das Cache4-Seiten Modell der Beispiel-App verweist auf das `Default30` Cache Profil:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>Kann angewendet werden auf:

* Razor Pages: Attribute können nicht auf Handlermethoden angewendet werden.
* MVC-Controller.
* MVC-Aktionsmethoden: Attribute auf Methoden Ebene überschreiben die in Attributen auf Klassenebene angegebenen Einstellungen.

Der resultierende Header, der für die Antwort auf die Cache4-Seite vom `Default30` Cache Profil übernommen wird:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Speichern von Antworten in Caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
