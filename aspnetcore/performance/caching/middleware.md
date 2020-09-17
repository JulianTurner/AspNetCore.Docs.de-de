---
title: Zwischen Speicherungs Middleware für Antworten in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie Middleware für die Zwischenspeicherung von Antworten in ASP.NET Core konfigurieren und verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/middleware
ms.openlocfilehash: 7fe9629e1c60a6156c69e546736049653a4229b7
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722643"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>Zwischen Speicherungs Middleware für Antworten in ASP.net Core

Von [John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

In diesem Artikel wird erläutert, wie Sie die Zwischenspeicherung von Antwort Caching in einer ASP.net Core-App konfigurieren. Die Middleware bestimmt, wann Antworten zwischengespeichert werden können, speichert Antworten und verarbeitet Antworten aus dem Cache. Eine Einführung in das http-Caching und das-Attribut finden Sie unter zwischen [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) [Speichern von Antworten](xref:performance/caching/response).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfiguration

Die Middleware zum Zwischenspeichern von Antworten ist für ASP.net Core-apps implizit über das freigegebene Framework verfügbar.

`Startup.ConfigureServices`Fügen Sie in der Dienst Sammlung die Middleware zum Zwischenspeichern von Antworten hinzu:

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurieren Sie die APP für die Verwendung der Middleware mit der- <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> Erweiterungsmethode, mit der die Middleware der Pipeline für die Anforderungs Verarbeitung in hinzugefügt wird `Startup.Configure` :

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17)]

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A> muss vor <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> der Verwendung von [cors-Middleware](xref:security/cors)aufgerufen werden.

Die Beispiel-App Fügt Header zum Steuern der Zwischenspeicherung bei nachfolgenden Anforderungen hinzu:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): speichert zwischen speicherbare Antworten bis zu 10 Sekunden.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): konfiguriert die Middleware so, dass Sie nur dann eine zwischengespeicherte Antwort bedient, wenn der [Accept-Encoding-](https://tools.ietf.org/html/rfc7231#section-5.3.4) Header der nachfolgenden Anforderungen mit der ursprünglichen Anforderung übereinstimmt.

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

Die vorangehenden Header werden nicht in die Antwort geschrieben und überschrieben, wenn ein Controller, eine Aktion oder eine Razor Seite:

* Verfügt über ein [[Response Cache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) -Attribut. Dies gilt auch, wenn eine Eigenschaft nicht festgelegt ist. Wenn Sie z. b. die [VaryByHeader](./response.md#vary) -Eigenschaft weglassen, bewirkt dies, dass der entsprechende Header aus der Antwort entfernt wird.

Die Middleware zum Zwischenspeichern von Antworten speichert nur Server Antworten zwischen, die zu einem 200 (OK)-Statuscode führen. Alle anderen Antworten, einschließlich der [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Antworten, die Inhalte für authentifizierte Clients enthalten, müssen als nicht zwischen speicherbar gekennzeichnet werden, um zu verhindern, dass die Middleware diese Antworten speichert und bedient. Ausführliche Informationen dazu, wie die Middleware festlegt, ob eine Antwort zwischengespeichert werden kann, finden Sie unter [Bedingungen für das Caching](#conditions-for-caching) .

## <a name="options"></a>Tastatur

Die Optionen zum Zwischenspeichern von Antworten sind in der folgenden Tabelle aufgeführt.

| Option | BESCHREIBUNG |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Die größte zwischen speicherbare Größe für den Antworttext in Bytes. Der Standardwert ist `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Die Größenbeschränkung für die Antwort Cache-Middleware in Bytes. Der Standardwert ist `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Bestimmt, ob Antworten bei Pfaden zwischen Groß-und Kleinschreibung zwischengespeichert werden. Der Standardwert ist `false`. |

Im folgenden Beispiel wird die Middleware wie folgt konfiguriert:

* Zwischenspeichern von Antworten mit einer Textgröße, die kleiner oder gleich 1.024 Bytes ist.
* Speichert die Antworten nach Pfaden mit Unterscheidung nach Groß-/Kleinschreibung Beispielsweise `/page1` werden und `/Page1` separat gespeichert.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>Varybyquerykeys

Bei Verwendung von MVC/Web-API-Controllern oder Razor Seiten Modellen gibt das- [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Attribut die Parameter an, die zum Festlegen der entsprechenden Header für das Zwischenspeichern von Antworten erforderlich sind. Der einzige Parameter des `[ResponseCache]` Attributs, der die Middleware strikt erfordert <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , ist, was keinem tatsächlichen HTTP-Header entspricht. Weitere Informationen finden Sie unter <xref:performance/caching/response#responsecache-attribute>.

Wenn das-Attribut nicht verwendet wird `[ResponseCache]` , kann das Zwischenspeichern von Antworten mit variiert werden `VaryByQueryKeys` . Verwenden Sie das <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> direkt aus [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Durch die Verwendung eines einzelnen Werts `*` `VaryByQueryKeys` von in variiert der Cache von allen Anforderungs Abfrage Parametern.

## <a name="http-headers-used-by-response-caching-middleware"></a>HTTP-Header, die von der zwischenware zum Zwischenspeichern

Die folgende Tabelle enthält Informationen über HTTP-Header, die sich auf das Zwischenspeichern von Antworten auswirken

| Header | Details |
| ------ | ------- |
| `Authorization` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. |
| `Cache-Control` | Die Middleware berücksichtigt nur zwischen Speicherungs Antworten, die mit der `public` Cache Direktive gekennzeichnet sind. Steuern Sie die Zwischenspeicherung mit den folgenden Parametern:<ul><li>Max-age</li><li>Max-veralteter&#8224;</li><li>min-Fresh</li><li>must-revalidate</li><li>Kein Cache</li><li>No-Store</li><li>nur-if-Cache</li><li>Privat</li><li>öffentlich</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;wenn kein Limit für angegeben ist `max-stale` , führt die Middleware keine Aktion aus.<br>&#8225;`proxy-revalidate` hat dieselbe Wirkung wie `must-revalidate` .<br><br>Weitere Informationen finden Sie unter [RFC 7231: Anfordern von Cache-Control-Direktiven](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Ein- `Pragma: no-cache` Header in der Anforderung erzeugt denselben Effekt wie `Cache-Control: no-cache` . Dieser Header wird durch die relevanten Direktiven im Header überschrieben `Cache-Control` , sofern vorhanden. Wird aus Gründen der Abwärtskompatibilität mit HTTP/1.0 berücksichtigt. |
| `Set-Cookie` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. Jede Middleware in der Anforderungs Verarbeitungs Pipeline, mit der ein oder mehrere s festgelegt werden, verhindert, dass cookie die Antwort zwischen speichernde Middleware die Antwort zwischenspeichert (z. b. dem [ cookie -basierten TempData-Anbieter](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Der- `Vary` Header wird verwendet, um die zwischengespeicherte Antwort von einem anderen Header zu verändern. Beispielsweise können Sie Antworten durch die Codierung Zwischenspeichern, indem Sie den `Vary: Accept-Encoding` -Header einschließen, der Antworten für Anforderungen mit Headern `Accept-Encoding: gzip` und separat zwischenspeichert `Accept-Encoding: text/plain` . Eine Antwort mit dem-Header Wert `*` wird nie gespeichert. |
| `Expires` | Eine Antwort, die von diesem Header als veraltet eingestuft wird, wird nur dann gespeichert oder abgerufen, wenn Sie von anderen `Cache-Control` Headern |
| `If-None-Match` | Die vollständige Antwort wird aus dem Cache bereitgestellt, wenn der Wert nicht ist, `*` und die `ETag` der Antwort entspricht keinem der angegebenen Werte. Andernfalls wird eine 304-Antwort (nicht geändert) bereitgestellt. |
| `If-Modified-Since` | Wenn der `If-None-Match` Header nicht vorhanden ist, wird eine vollständige Antwort aus dem Cache bereitgestellt, wenn das zwischengespeicherte Antwort Datum neuer als der angegebene Wert ist. Andernfalls wird eine *304-nicht geänderte* Antwort verarbeitet. |
| `Date` | Bei der Bereitstellung aus dem Cache `Date` wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Content-Length` | Bei der Bereitstellung aus dem Cache `Content-Length` wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Age` | Der `Age` in der ursprünglichen Antwort gesendete Header wird ignoriert. Die Middleware berechnet einen neuen Wert, wenn eine zwischengespeicherte Antwort bedient wird. |

## <a name="caching-respects-request-cache-control-directives"></a>Caching-Anweisungs Anforderungen Cache-Control-Direktiven

Die Middleware respektiert die Regeln der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234#section-5.2). Die Regeln erfordern einen Cache, um einen gültigen Header zu berücksichtigen, `Cache-Control` der vom Client gesendet wird. Unter der Spezifikation kann ein Client Anforderungen mit einem `no-cache` Header Wert senden und erzwingen, dass der Server eine neue Antwort für jede Anforderung generiert. Derzeit gibt es keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die Middleware verwendet wird, da die Middleware die offizielle zwischen Speicherungs Spezifikation befolgt.

Weitere Informationen zum zwischen Speicherungs Verhalten finden Sie unter untersuchen anderer zwischen Speicherungs Funktionen von ASP.net Core. Weitere Informationen finden Sie in den folgenden Artikeln:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Problembehandlung

Wenn das Cachingverhalten nicht erwartungsgemäß ist, vergewissern Sie sich, dass die Antworten zwischengespeichert werden können und aus dem Cache bedient werden können. Überprüfen der eingehenden Header der Anforderung und der ausgehenden Header der Antwort. Aktivieren der [Protokollierung](xref:fundamentals/logging/index) zum unterstützen des Debuggens.

Beim Testen und behandeln von Problemen zwischen Speicherungs Verhalten kann ein Browser Anforderungs Header festlegen, die das Caching auf unerwünschte Weise beeinflussen. Ein Browser kann beispielsweise den- `Cache-Control` Header auf oder festlegen, `no-cache` `max-age=0` Wenn eine Seite aktualisiert wird. Die folgenden Tools können Anforderungs Header explizit festlegen und werden für das Testen der Zwischenspeicherung bevorzugt:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Bedingungen für das Zwischenspeichern

* Die Anforderung muss eine Serverantwort mit dem Statuscode 200 (OK) ergeben.
* Die Anforderungs Methode muss "Get" oder "Head" lauten.
* In `Startup.Configure` muss die Zwischenspeicherung von Antwort Caching vor Middleware platziert werden, die Zwischenspeichern erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.
* Der `Authorization` Header darf nicht vorhanden sein.
* `Cache-Control` Header Parameter müssen gültig sein, und die Antwort muss markiert `public` und nicht gekennzeichnet sein `private` .
* Der `Pragma: no-cache` Header darf nicht vorhanden sein, wenn der `Cache-Control` Header nicht vorhanden ist, da der Header `Cache-Control` den Header überschreibt, `Pragma` Wenn er vorhanden ist.
* Der `Set-Cookie` Header darf nicht vorhanden sein.
* `Vary` Header Parameter müssen gültig und nicht gleich sein `*` .
* Der `Content-Length` Header Wert (falls festgelegt) muss mit der Größe des Antwort Texts identisch sein.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Wird nicht verwendet.
* Die Antwort darf nicht veraltet sein, wie von der- `Expires` Header-und der `max-age` -und- `s-maxage` Cache Direktive angegeben.
* Die Antwort Pufferung muss erfolgreich sein. Die Größe der Antwort muss kleiner als der konfigurierte oder der Standardwert sein <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Die Textgröße der Antwort muss kleiner als der konfigurierte oder der Standardwert sein <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Die Antwort muss gemäß den Spezifikationen von [RFC 7234](https://tools.ietf.org/html/rfc7234) zwischengespeichert werden können. Beispielsweise darf die- `no-store` Direktive nicht in den Feldern der Anforderungs-oder Antwortheader vorhanden sein. Weitere Informationen finden Sie *in Abschnitt 3: Speichern von Antworten in Caches* von [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Das antifälschungs System zum Erstellen von sicheren Token, um Angriffe durch Website übergreifende Anforderungs Fälschung (CSRF) zu verhindern, legt die `Cache-Control` -und- `Pragma` Header auf fest, `no-cache` sodass Antworten nicht zwischengespeichert werden. Informationen zum Deaktivieren von antifälschungstoken für HTML-Formularelemente finden Sie unter <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird erläutert, wie Sie die Zwischenspeicherung von Antwort Caching in einer ASP.net Core-App konfigurieren. Die Middleware bestimmt, wann Antworten zwischengespeichert werden können, speichert Antworten und verarbeitet Antworten aus dem Cache. Eine Einführung in das http-Caching und das-Attribut finden Sie unter zwischen [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) [Speichern von Antworten](xref:performance/caching/response).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="configuration"></a>Konfiguration

Verwenden Sie das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) ", oder fügen Sie dem Paket " [Microsoft. aspnetcore. responsecaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/) " einen Paket Verweis hinzu.

`Startup.ConfigureServices`Fügen Sie in der Dienst Sammlung die Middleware zum Zwischenspeichern von Antworten hinzu:

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

Konfigurieren Sie die APP für die Verwendung der Middleware mit der- <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*> Erweiterungsmethode, mit der die Middleware der Pipeline für die Anforderungs Verarbeitung in hinzugefügt wird `Startup.Configure` :

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

Die Beispiel-App Fügt Header zum Steuern der Zwischenspeicherung bei nachfolgenden Anforderungen hinzu:

* [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2): speichert zwischen speicherbare Antworten bis zu 10 Sekunden.
* [Vary](https://tools.ietf.org/html/rfc7231#section-7.1.4): konfiguriert die Middleware so, dass Sie nur dann eine zwischengespeicherte Antwort bedient, wenn der [Accept-Encoding-](https://tools.ietf.org/html/rfc7231#section-5.3.4) Header der nachfolgenden Anforderungen mit der ursprünglichen Anforderung übereinstimmt.

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

Die vorangehenden Header werden nicht in die Antwort geschrieben und überschrieben, wenn ein Controller, eine Aktion oder eine Razor Seite:

* Verfügt über ein [[Response Cache]](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) -Attribut. Dies gilt auch, wenn eine Eigenschaft nicht festgelegt ist. Wenn Sie z. b. die [VaryByHeader](./response.md#vary) -Eigenschaft weglassen, bewirkt dies, dass der entsprechende Header aus der Antwort entfernt wird.

Die Middleware zum Zwischenspeichern von Antworten speichert nur Server Antworten zwischen, die zu einem 200 (OK)-Statuscode führen. Alle anderen Antworten, einschließlich der [Fehlerseiten](xref:fundamentals/error-handling), werden von der Middleware ignoriert.

> [!WARNING]
> Antworten, die Inhalte für authentifizierte Clients enthalten, müssen als nicht zwischen speicherbar gekennzeichnet werden, um zu verhindern, dass die Middleware diese Antworten speichert und bedient. Ausführliche Informationen dazu, wie die Middleware festlegt, ob eine Antwort zwischengespeichert werden kann, finden Sie unter [Bedingungen für das Caching](#conditions-for-caching) .

## <a name="options"></a>Tastatur

Die Optionen zum Zwischenspeichern von Antworten sind in der folgenden Tabelle aufgeführt.

| Option | BESCHREIBUNG |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | Die größte zwischen speicherbare Größe für den Antworttext in Bytes. Der Standardwert ist `64 * 1024 * 1024` (64 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | Die Größenbeschränkung für die Antwort Cache-Middleware in Bytes. Der Standardwert ist `100 * 1024 * 1024` (100 MB). |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | Bestimmt, ob Antworten bei Pfaden zwischen Groß-und Kleinschreibung zwischengespeichert werden. Der Standardwert ist `false`. |

Im folgenden Beispiel wird die Middleware wie folgt konfiguriert:

* Zwischenspeichern von Antworten mit einer Textgröße, die kleiner oder gleich 1.024 Bytes ist.
* Speichert die Antworten nach Pfaden mit Unterscheidung nach Groß-/Kleinschreibung Beispielsweise `/page1` werden und `/Page1` separat gespeichert.

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>Varybyquerykeys

Bei Verwendung von MVC/Web-API-Controllern oder Razor Seiten Modellen gibt das- [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) Attribut die Parameter an, die zum Festlegen der entsprechenden Header für das Zwischenspeichern von Antworten erforderlich sind. Der einzige Parameter des `[ResponseCache]` Attributs, der die Middleware strikt erfordert <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys> , ist, was keinem tatsächlichen HTTP-Header entspricht. Weitere Informationen finden Sie unter <xref:performance/caching/response#responsecache-attribute>.

Wenn das-Attribut nicht verwendet wird `[ResponseCache]` , kann das Zwischenspeichern von Antworten mit variiert werden `VaryByQueryKeys` . Verwenden Sie das <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature> direkt aus [HttpContext. Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features):

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

Durch die Verwendung eines einzelnen Werts `*` `VaryByQueryKeys` von in variiert der Cache von allen Anforderungs Abfrage Parametern.

## <a name="http-headers-used-by-response-caching-middleware"></a>HTTP-Header, die von der zwischenware zum Zwischenspeichern

Die folgende Tabelle enthält Informationen über HTTP-Header, die sich auf das Zwischenspeichern von Antworten auswirken

| Header | Details |
| ------ | ------- |
| `Authorization` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. |
| `Cache-Control` | Die Middleware berücksichtigt nur zwischen Speicherungs Antworten, die mit der `public` Cache Direktive gekennzeichnet sind. Steuern Sie die Zwischenspeicherung mit den folgenden Parametern:<ul><li>Max-age</li><li>Max-veralteter&#8224;</li><li>min-Fresh</li><li>must-revalidate</li><li>Kein Cache</li><li>No-Store</li><li>nur-if-Cache</li><li>Privat</li><li>öffentlich</li><li>s-maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;wenn kein Limit für angegeben ist `max-stale` , führt die Middleware keine Aktion aus.<br>&#8225;`proxy-revalidate` hat dieselbe Wirkung wie `must-revalidate` .<br><br>Weitere Informationen finden Sie unter [RFC 7231: Anfordern von Cache-Control-Direktiven](https://tools.ietf.org/html/rfc7234#section-5.2.1). |
| `Pragma` | Ein- `Pragma: no-cache` Header in der Anforderung erzeugt denselben Effekt wie `Cache-Control: no-cache` . Dieser Header wird durch die relevanten Direktiven im Header überschrieben `Cache-Control` , sofern vorhanden. Wird aus Gründen der Abwärtskompatibilität mit HTTP/1.0 berücksichtigt. |
| `Set-Cookie` | Die Antwort wird nicht zwischengespeichert, wenn der Header vorhanden ist. Jede Middleware in der Anforderungs Verarbeitungs Pipeline, mit der ein oder mehrere s festgelegt werden, verhindert, dass cookie die Antwort zwischen speichernde Middleware die Antwort zwischenspeichert (z. b. dem [ cookie -basierten TempData-Anbieter](xref:fundamentals/app-state#tempdata)).  |
| `Vary` | Der- `Vary` Header wird verwendet, um die zwischengespeicherte Antwort von einem anderen Header zu verändern. Beispielsweise können Sie Antworten durch die Codierung Zwischenspeichern, indem Sie den `Vary: Accept-Encoding` -Header einschließen, der Antworten für Anforderungen mit Headern `Accept-Encoding: gzip` und separat zwischenspeichert `Accept-Encoding: text/plain` . Eine Antwort mit dem-Header Wert `*` wird nie gespeichert. |
| `Expires` | Eine Antwort, die von diesem Header als veraltet eingestuft wird, wird nur dann gespeichert oder abgerufen, wenn Sie von anderen `Cache-Control` Headern |
| `If-None-Match` | Die vollständige Antwort wird aus dem Cache bereitgestellt, wenn der Wert nicht ist, `*` und die `ETag` der Antwort entspricht keinem der angegebenen Werte. Andernfalls wird eine 304-Antwort (nicht geändert) bereitgestellt. |
| `If-Modified-Since` | Wenn der `If-None-Match` Header nicht vorhanden ist, wird eine vollständige Antwort aus dem Cache bereitgestellt, wenn das zwischengespeicherte Antwort Datum neuer als der angegebene Wert ist. Andernfalls wird eine *304-nicht geänderte* Antwort verarbeitet. |
| `Date` | Bei der Bereitstellung aus dem Cache `Date` wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Content-Length` | Bei der Bereitstellung aus dem Cache `Content-Length` wird der Header von der Middleware festgelegt, wenn er nicht in der ursprünglichen Antwort angegeben wurde. |
| `Age` | Der `Age` in der ursprünglichen Antwort gesendete Header wird ignoriert. Die Middleware berechnet einen neuen Wert, wenn eine zwischengespeicherte Antwort bedient wird. |

## <a name="caching-respects-request-cache-control-directives"></a>Caching-Anweisungs Anforderungen Cache-Control-Direktiven

Die Middleware respektiert die Regeln der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234#section-5.2). Die Regeln erfordern einen Cache, um einen gültigen Header zu berücksichtigen, `Cache-Control` der vom Client gesendet wird. Unter der Spezifikation kann ein Client Anforderungen mit einem `no-cache` Header Wert senden und erzwingen, dass der Server eine neue Antwort für jede Anforderung generiert. Derzeit gibt es keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die Middleware verwendet wird, da die Middleware die offizielle zwischen Speicherungs Spezifikation befolgt.

Weitere Informationen zum zwischen Speicherungs Verhalten finden Sie unter untersuchen anderer zwischen Speicherungs Funktionen von ASP.net Core. Weitere Informationen finden Sie in den folgenden Artikeln:

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>Problembehandlung

Wenn das Cachingverhalten nicht erwartungsgemäß ist, vergewissern Sie sich, dass die Antworten zwischengespeichert werden können und aus dem Cache bedient werden können. Überprüfen der eingehenden Header der Anforderung und der ausgehenden Header der Antwort. Aktivieren der [Protokollierung](xref:fundamentals/logging/index) zum unterstützen des Debuggens.

Beim Testen und behandeln von Problemen zwischen Speicherungs Verhalten kann ein Browser Anforderungs Header festlegen, die das Caching auf unerwünschte Weise beeinflussen. Ein Browser kann beispielsweise den- `Cache-Control` Header auf oder festlegen, `no-cache` `max-age=0` Wenn eine Seite aktualisiert wird. Die folgenden Tools können Anforderungs Header explizit festlegen und werden für das Testen der Zwischenspeicherung bevorzugt:

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>Bedingungen für das Zwischenspeichern

* Die Anforderung muss eine Serverantwort mit dem Statuscode 200 (OK) ergeben.
* Die Anforderungs Methode muss "Get" oder "Head" lauten.
* In `Startup.Configure` muss die Zwischenspeicherung von Antwort Caching vor Middleware platziert werden, die Zwischenspeichern erfordert. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.
* Der `Authorization` Header darf nicht vorhanden sein.
* `Cache-Control` Header Parameter müssen gültig sein, und die Antwort muss markiert `public` und nicht gekennzeichnet sein `private` .
* Der `Pragma: no-cache` Header darf nicht vorhanden sein, wenn der `Cache-Control` Header nicht vorhanden ist, da der Header `Cache-Control` den Header überschreibt, `Pragma` Wenn er vorhanden ist.
* Der `Set-Cookie` Header darf nicht vorhanden sein.
* `Vary` Header Parameter müssen gültig und nicht gleich sein `*` .
* Der `Content-Length` Header Wert (falls festgelegt) muss mit der Größe des Antwort Texts identisch sein.
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>Wird nicht verwendet.
* Die Antwort darf nicht veraltet sein, wie von der- `Expires` Header-und der `max-age` -und- `s-maxage` Cache Direktive angegeben.
* Die Antwort Pufferung muss erfolgreich sein. Die Größe der Antwort muss kleiner als der konfigurierte oder der Standardwert sein <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> . Die Textgröße der Antwort muss kleiner als der konfigurierte oder der Standardwert sein <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> .
* Die Antwort muss gemäß den Spezifikationen von [RFC 7234](https://tools.ietf.org/html/rfc7234) zwischengespeichert werden können. Beispielsweise darf die- `no-store` Direktive nicht in den Feldern der Anforderungs-oder Antwortheader vorhanden sein. Weitere Informationen finden Sie *in Abschnitt 3: Speichern von Antworten in Caches* von [RFC 7234](https://tools.ietf.org/html/rfc7234) .

> [!NOTE]
> Das antifälschungs System zum Erstellen von sicheren Token, um Angriffe durch Website übergreifende Anforderungs Fälschung (CSRF) zu verhindern, legt die `Cache-Control` -und- `Pragma` Header auf fest, `no-cache` sodass Antworten nicht zwischengespeichert werden. Informationen zum Deaktivieren von antifälschungstoken für HTML-Formularelemente finden Sie unter <xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration> .

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end