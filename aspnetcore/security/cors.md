---
title: Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie cors als Standard zum Zulassen oder ablehnen von Ursprungs übergreifenden Anforderungen in einer ASP.net Core-app.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
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
uid: security/cors
ms.openlocfilehash: 03008f40fc1c4b323535d08a1bb4c4007bc145f7
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060819"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>Aktivieren von Cross-Origin-Anforderungen (cors) in ASP.net Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Kirk Larkin](https://twitter.com/serpent5)

In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat. Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet. Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Manchmal möchten Sie, dass andere Sites Ursprungs übergreifende Anforderungen an Ihre APP senden können. Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):

* Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
* Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors. Eine API wird durch das Zulassen von cors nicht sicherer. Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).
* Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.
* Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Gleicher Ursprung

Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese beiden URLs haben denselben Ursprung:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:

* `https://example.net`: Andere Domäne
* `https://www.example.com/foo.html`: Untergeordnete Domäne
* `http://example.com/foo.html`: Anderes Schema
* `https://example.com:9000/foo.html`: Anderer Port

## <a name="enable-cors"></a>Aktivieren von CORS

Es gibt drei Möglichkeiten, cors zu aktivieren:

* In Middleware, die eine [benannte Richtlinie](#np) oder eine [Standard Richtlinie](#dp)verwendet.
* Verwenden des [Endpunkt Routings](#ecors).
* Mit dem [[enablecors]](#attr) -Attribut.

Die Verwendung des [[enablecors]](#attr) -Attributs mit einer benannten Richtlinie ermöglicht das beste Steuerelement in einschränkenden Endpunkten, die cors unterstützen.

> [!WARNING]
> <xref:Owin.CorsExtensions.UseCors%2A> muss vor aufgerufen werden, <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> Wenn verwendet wird `UseResponseCaching` .

Jeder Ansatz wird in den folgenden Abschnitten ausführlich beschrieben.

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>Cors mit benannten Richtlinie und Middleware

Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen. Mit dem folgenden Code wird eine cors-Richtlinie auf alle Endpunkte der APP mit den angegebenen Ursprüngen angewendet:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

Der vorangehende Code:

* Legt den Richtlinien Namen auf fest `_myAllowSpecificOrigins` . Der Richtlinien Name ist willkürlich.
* Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf und gibt die  `_myAllowSpecificOrigins` cors-Richtlinie an. `UseCors` Fügt die cors-Middleware hinzu. Der-Anruf `UseCors` muss nach, jedoch `UseRouting` vor eingefügt werden `UseAuthorization` . Weitere Informationen finden Sie unter [Middleware Order](xref:fundamentals/middleware/index#middleware-order).
* Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf. Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an. [Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.
* Aktiviert die `_myAllowSpecificOrigins` cors-Richtlinie für alle Controller Endpunkte. Weitere Informationen finden Sie unter [EndPoint Routing](#ecors) zum Anwenden einer cors-Richtlinie auf bestimmte Endpunkte.
* Wenn Sie die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware)verwenden, sollten Sie <xref:Owin.CorsExtensions.UseCors%2A> vor <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>

Beim Endpunkt Routing **muss** die cors-Middleware für die Ausführung zwischen den Aufrufen von und konfiguriert werden `UseRouting` `UseEndpoints` .

Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .

Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.

Die <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methoden können verkettet werden, wie im folgenden Code gezeigt:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

Hinweis: die angegebene URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten. Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>Cors mit Standard Richtlinie und Middleware

Der folgende markierte Code aktiviert die cors-Standard Richtlinie:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

Im vorangehenden Code wird die cors-Standard Richtlinie auf alle Controller Endpunkte angewendet.

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>Aktivieren von Cors mit Endpunktrouting

Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf). Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/20709) und [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer).

Mit dem Endpunkt Routing kann cors pro Endpunkt mit dem <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> Satz von Erweiterungs Methoden aktiviert werden:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

Im obigen Code:

* `app.UseCors` aktiviert die cors-Middleware. Da eine Standard Richtlinie nicht konfiguriert wurde, werden `app.UseCors()` cors von alleine nicht aktiviert.
* Die `/echo` -und-Controller Endpunkte lassen Ursprungs übergreifende Anforderungen zu, die die angegebene Richtlinie verwenden.
* Die `/echo2` -und Razor -Seiten Endpunkte lassen **keine** Ursprungs übergreifenden Anforderungen zu, da keine Standard Richtlinie angegeben wurde.

Das [[disablecors]](#dc) -Attribut deaktiviert **keine**  cors, die durch das Endpunkt Routing mit aktiviert wurden `RequireCors` .

Anweisungen zum Testen von Code, der dem vorangehenden ähnelt, finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>Aktivieren von cors mit Attributen

Das Aktivieren von cors mit dem [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut und das Anwenden einer benannten Richtlinie auf die Endpunkte, die cors erfordern, stellt das beste Steuerelement bereit.

Das [[enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors. Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte:

* `[EnableCors]` Gibt die Standard Richtlinie an.
* `[EnableCors("{Policy String}")]` gibt eine benannte Richtlinie an.

Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:

* Razor S `PageModel`
* Controller
* Controller-Aktionsmethode

Mithilfe des-Attributs können unterschiedliche Richtlinien auf Controller, Seiten Modelle oder Aktionsmethoden angewendet werden `[EnableCors]` . Wenn das `[EnableCors]` -Attribut auf einen Controller, ein Seiten Modell oder eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet. **Es wird empfohlen, Richtlinien zu kombinieren. Verwenden Sie das** `[EnableCors]` **Attribut oder die Middleware, nicht beide in der gleichen app.**

Der folgende Code wendet eine andere Richtlinie auf jede Methode an:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt zwei cors-Richtlinien:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

Für die beste Kontrolle über das Einschränken von cors-Anforderungen:

* Verwenden Sie `[EnableCors("MyPolicy")]` mit einer benannten Richtlinie.
* Definieren Sie keine Standard Richtlinie.
* Verwenden Sie das [Endpunkt Routing](#ecors)nicht.

Der Code im nächsten Abschnitt entspricht der vorangehenden Liste.

Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#testc) .

<a name="dc"></a>

### <a name="disable-cors"></a>Cors deaktivieren

Das [[disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert **keine**  cors, die durch das [Endpunkt Routing](#ecors)aktiviert wurden.

Der folgende Code definiert die cors-Richtlinie `"MyPolicy"` :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

Der folgende Code deaktiviert cors für die `GetValues2` Aktion:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

Der vorangehende Code:

* Aktivieren von cors mit dem [Endpunkt Routing](#ecors)nicht.
* Definiert keine [cors-Standard Richtlinie](#dp).
* Verwendet [[enablecors ("MyPolicy")]](#attr) , um die `"MyPolicy"` cors-Richtlinie für den Controller zu aktivieren.
* Deaktiviert cors für die- `GetValues2` Methode.

Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Test-cors](#testc) .

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Cors-Richtlinien Optionen

In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:

* [Festlegen der zulässigen Ursprünge](#set-the-allowed-origins)
* [Festlegen der zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Festlegen der zulässigen Anforderungs Header](#set-the-allowed-request-headers)
* [Festlegen der verfügbar gemachten Antwortheader](#set-the-exposed-response-headers)
* [Anmelde Informationen in Ursprungs übergreifenden Anforderungen](#credentials-in-cross-origin-requests)
* [Festlegen der Preflight-Ablaufzeit](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in aufgerufen `Startup.ConfigureServices` . Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.

## <a name="set-the-allowed-origins"></a>Festlegen der zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ). `AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.

> [!NOTE]
> Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen. Der cors-Dienst gibt eine ungültige cors-Antwort zurück, wenn eine APP mit beiden Methoden konfiguriert ist.

`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>Festlegen der zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Lässt jede HTTP-Methode zu:
* Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

### <a name="set-the-allowed-request-headers"></a>Festlegen der zulässigen Anforderungs Header

Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als [Autor Request Headers](https://xhr.spec.whatwg.org/#request)bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader` wirkt sich auf Preflight-Anforderungen und den [Access-Control-Request-Headers-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

Eine cors-Middleware-Richtlinie ist mit bestimmten Headern übereinstimmen, die durch angegeben werden `WithHeaders` `Access-Control-Request-Headers` `WithHeaders`

Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

Cors-Middleware lehnt eine Preflight-Anforderung mit dem folgenden Anforderungs Header ab, da `Content-Language` ([headernames. contentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) nicht in aufgeführt ist `WithHeaders` :

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

Die APP gibt eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück. Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.

### <a name="set-the-exposed-response-headers"></a>Festlegen der verfügbar gemachten Antwortheader

Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Die standardmäßig verfügbaren Antwortheader lauten wie folgt:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Mit der cors-Spezifikation werden die Header für *einfache Antworten* aufgerufen. Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>Anmelde Informationen in Ursprungs übergreifenden Anforderungen

Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung. Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung. Anmelde Informationen umfassen cookie e-und http-Authentifizierungs Schemas. Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .

`XMLHttpRequest`Direkt verwenden:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Verwenden von jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss die Anmelde Informationen zulassen. Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.

Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.

Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.

<a name="pref"></a>

## <a name="preflight-requests"></a>Preflight-Anforderungen

Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche [options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) Anforderung, bevor die tatsächliche Anforderung gesendet wird. Diese Anforderung wird als [Preflight-Anforderung](https://developer.mozilla.org/docs/Glossary/Preflight_request)bezeichnet. Der Browser kann die Preflight-Anforderung überspringen, wenn **alle** der folgenden Bedingungen zutreffen:

* Die Anforderungs Methode ist Get, Head oder Post.
* Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.
* `Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` . In der cors-Spezifikation werden diese Header [Anforderungs Header für Autoren](https://www.w3.org/TR/cors/#author-request-headers)aufgerufen. Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .

Im folgenden finden Sie eine Beispiel Antwort, die der Preflight-Anforderung ähnelt, die im Abschnitt " [Test cors](#testc) " dieses Dokuments von der Schaltfläche **[Put Test]** erstellt wurde.

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

Die Preflight-Anforderung verwendet die [http Options](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) -Methode. Die folgenden Header sind möglicherweise enthalten:

* [Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.
* [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden. Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..
* [Access-Control-Allow-Methods](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine Antwort zurück, `200 OK` legt jedoch keine cors-Header fest. Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden. Ein Beispiel für eine verweigerte Preflight-Anforderung finden Sie im Abschnitt zum [Testen von cors](#testc) in diesem Dokument.

Mit den F12-Tools zeigt die Konsolen-App je nach Browser einen Fehler an, der einem der folgenden ähnelt:

* Firefox: Ursprungs übergreifende Anforderung blockiert: die gleiche Ursprungs Richtlinie lässt das Lesen der Remote Ressource unter nicht zu `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` . (Ursache: die cors-Anforderung war nicht erfolgreich). [Weitere Informationen](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* Chrom basiert: der Zugriff auf " https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 " vom Ursprung "" wurde https://cors3.azurewebsites.net durch eine cors-Richtlinie blockiert: die Antwort auf eine Preflight-Anforderung übergibt die Zugriffs Steuerungs Prüfung nicht: Es ist kein "Access-Control-Allow-Origin"-Header für die angeforderte Ressource vorhanden. Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.

Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

Um alle [Autoren Anforderungs Header](https://www.w3.org/TR/cors/#author-request-headers)zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

Browser sind nicht konsistent, wie Sie festgelegt werden `Access-Control-Request-Headers` . Wenn beides:

* Header werden auf einen anderen Wert als festgelegt. `"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> wird aufgerufen: Schließen Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header ein, die Sie unterstützen möchten.

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>Automatischer Preflight-Anforderungs Code

Wenn die cors-Richtlinie angewendet wird, gilt Folgendes:

* Global durch Aufrufen von `app.UseCors` in `Startup.Configure` .
* Verwenden des- `[EnableCors]` Attributs.

ASP.net Core antwortet auf die Preflight-Options Anforderung.

Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt derzeit **keine** automatischen Preflight-Anforderungen.

Dieses Verhalten wird im Abschnitt " [Test-cors](#testc) " dieses Dokuments veranschaulicht.

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>[Httpoptions]-Attribut für Preflight-Anforderungen

Wenn cors mit der entsprechenden Richtlinie aktiviert ist, reagieren ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen. In einigen Szenarios ist dies möglicherweise nicht der Fall. Beispielsweise bei Verwendung von [cors mit dem Endpunkt Routing](#ecors).

Der folgende Code verwendet das [[httpoptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) -Attribut, um Endpunkte für Options Anforderungen zu erstellen:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

Anweisungen zum Testen des vorangehenden Codes finden Sie unter [Testen von cors mit dem Endpunkt Routing und [httpoptions]](#tcer) .

### <a name="set-the-preflight-expiration-time"></a>Festlegen der Preflight-Ablaufzeit

Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann. Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von cors

In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.

* Cors ist **kein** Sicherheits Feature. Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
  * Ein böswilliger Akteur könnte z. b. Site übergreifende Skripts [(Cross-Site Scripting, XSS)](xref:security/cross-site-scripting) für Ihre Website verwenden und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.
* Eine API ist nicht sicherer, da Sie cors zulässt.
  * Es liegt an dem Client (Browser), cors zu erzwingen. Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert. Eines der folgenden Tools zeigt z. b. die Serverantwort an:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.Net httpclient](/dotnet/csharp/tutorials/console-webapiclient)
    * Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.
* Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.
  * Browser ohne cors können keine Ursprungs übergreifenden Anforderungen ausführen. Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen. JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen. Skripts können Ursprungs übergreifend geladen werden.

In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen. Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt. Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.

[Schaltfläche "Test platzieren](https://cors3.azurewebsites.net/test) " im bereitgestellten [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung von der Test Schaltfläche " [Werte](https://cors3.azurewebsites.net/) " zu `https://cors1.azurewebsites.net/api/values` . Der `Origin` Header:

* Stellt die Domäne der Site bereit, von der die Anforderung stammt.
* Ist erforderlich und muss sich vom Host unterscheiden.

**Allgemeine Header**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**Antwortheader**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**Anforderungsheader**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

In `OPTIONS` Anforderungen legt der Server den Header der **Antwortheader** `Access-Control-Allow-Origin: {allowed origin}` in der Antwort fest. Beispielsweise enthält die Schaltfläche für das bereitgestellte [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) `OPTIONS` die folgenden Header:

**Allgemeine Header**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**Antwortheader**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**Anforderungsheader**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

In den vorangehenden **Antwort Headern** legt der Server den [Access-Control-Allow-Origin-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) Header in der Antwort fest. Der `https://cors1.azurewebsites.net` Wert dieses Headers entspricht dem `Origin` Header aus der Anforderung.

Wenn <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> aufgerufen wird, wird der Platzhalter `Access-Control-Allow-Origin: *` Wert zurückgegeben. `AllowAnyOrigin` ermöglicht einen beliebigen Ursprung.

Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl. Der Browser lässt die Anforderung insbesondere nicht zu. Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.

<a name="options"></a>

### <a name="display-options-requests"></a>Anzeige Options Anforderungen

Standardmäßig zeigen die Browser Chrome und Edge keine Options Anforderungen auf der Registerkarte Netzwerk der F12-Tools an. So zeigen Sie Options Anforderungen in diesen Browsern an:

* `chrome://flags/#out-of-blink-cors` oder `edge://flags/#out-of-blink-cors`
* Deaktivieren Sie das-Flag.
* „Neu starten“.

Firefox zeigt Options Anforderungen standardmäßig an.

## <a name="cors-in-iis"></a>Cors in IIS

Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist. Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.

<a name="testc"></a>

## <a name="test-cors"></a>Testen von CORS

Der [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) enthält Code zum Testen von cors. [Informationen zum Herunterladen](xref:index#how-to-download-a-sample) finden Sie hier. Das Beispiel ist ein API-Projekt mit Razor hinzugefügten Seiten:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)ähnelt.

Im folgenden `ValuesController` finden Sie die Endpunkte für Tests:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[Mydisplayrouteinfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) wird vom [Rick.Docs. Samples. routeinfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) -nuget-Paket bereitgestellt und zeigt Routeninformationen an.

Testen Sie den vorangehenden Beispielcode, indem Sie einen der folgenden Ansätze verwenden:

* Verwenden Sie die bereitgestellte Beispiel-app unter [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) . Das Beispiel muss nicht heruntergeladen werden.
* Führen Sie das Beispiel mit `dotnet run` der Standard-URL von aus `https://localhost:5001` .
* Führen Sie das Beispiel aus Visual Studio aus, wobei der Port auf 44398 für eine URL von festgelegt ist `https://localhost:44398` .

Verwenden eines Browsers mit den F12-Tools:

* Wählen Sie die Schaltfläche **Werte** , und überprüfen Sie die Header auf der Registerkarte **Netzwerk** .
* Wählen Sie die Schaltfläche **Test platzieren** aus. Anweisungen zum Anzeigen der Options Anforderung finden Sie unter [Anzeigen von Options Anforderungen](#options) . Der **Put-Test** erstellt zwei Anforderungen, eine Preflight-Anforderung für Optionen und die PUT-Anforderung.
* Wählen Sie die **`GetValues2 [DisableCors]`** Schaltfläche aus, um eine fehlgeschlagene cors-Anforderung Wie im Dokument erwähnt, gibt die Antwort 200 Erfolg zurück, aber die cors-Anforderung wird nicht hergestellt. Wählen Sie die Registerkarte **Konsole** aus, um den cors-Fehler anzuzeigen. Abhängig vom Browser wird ein Fehler angezeigt, der dem folgenden Beispiel ähnelt:

     Der Zugriff zum Abrufen auf `'https://cors1.azurewebsites.net/api/values/GetValues2'` der Ursprungsseite wurde `'https://cors3.azurewebsites.net'` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden. Falls eine opake Antwort Ihre Anforderungen erfüllt, legen Sie den Modus der Anforderung auf 'no-cors' fest, um CORS für den Ressourcenabruf zu deaktivieren.
     
Cors-aktivierte Endpunkte können mit einem Tool wie [curl](https://curl.haxx.se/), " [fddler](https://www.telerik.com/fiddler)" oder [Postman](https://www.getpostman.com/)getestet werden. Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt. Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :

* Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.
* Cors-Header werden in der Antwort nicht zurückgegeben.

Der folgende Befehl verwendet `curl` , um eine Options Anforderung mit Informationen auszugeben:

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>Testen von cors mit dem Endpunkt Routing und [httpoptions]

Die Aktivierung von cors auf Basis von Endpunkten `RequireCors` unterstützt **not** derzeit keine [automatischen Preflight-Anforderungen](#apf). Beachten Sie den folgenden Code, der das [Endpunkt Routing verwendet, um cors zu aktivieren](#ecors):

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

Im folgenden `TodoItems1Controller` finden Sie Endpunkte zum Testen:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=1) " des bereitgestellten [Beispiels.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)

Die Schaltflächen " **delete [enablecors]** " und " **Get [enablecors]** " sind erfolgreich, da die Endpunkte `[EnableCors]` Preflight-Anforderungen aufweisen und darauf reagieren. Die anderen Endpunkte schlagen fehl. Die Schaltfläche **Get** schlägt fehl, da das [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) Folgendes sendet:

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

Im folgenden `TodoItems2Controller` werden ähnliche Endpunkte bereitgestellt, aber es ist expliziter Code enthalten, der auf Options Anforderungen antwortet:

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

Testen Sie den vorangehenden Code auf der [Seite "Test](https://cors1.azurewebsites.net/test?number=2) " des bereitgestellten Beispiels. Wählen Sie in der Dropdown Liste **Controller** den Wert **Preflight** aus, und legen Sie dann **Controller fest** . Alle cors-Aufrufe an die `TodoItems2Controller` Endpunkte sind erfolgreich.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (cors)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Einführung in das IIS cors-Modul](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

In diesem Artikel wird gezeigt, wie Sie cors in einer ASP.net Core-App aktivieren.

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat. Diese Einschränkung wird als *Richtlinie des gleichen Ursprungs* bezeichnet. Die Richtlinie des gleichen Ursprungs verhindert, dass eine schädliche Website sensible Daten von einer anderen Website liest. Manchmal möchten Sie möglicherweise, dass andere Websites Ursprungs übergreifende Anforderungen an Ihre APP stellen. Weitere Informationen finden Sie im [Artikel zu Mozilla cors](https://developer.mozilla.org/docs/Web/HTTP/CORS).

[Cross-Origin Resource Sharing](https://www.w3.org/TR/cors/) (cors):

* Ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
* Bei handelt es sich **nicht** um ein Sicherheits Feature, sondern um die Sicherheit von cors. Eine API wird durch das Zulassen von cors nicht sicherer. Weitere Informationen finden Sie unter [Funktionsweise von cors](#how-cors).
* Ermöglicht einem Server das explizite zulassen einiger Ursprungs übergreifender Anforderungen, während andere abgelehnt werden.
* Ist sicherer und flexibler als frühere Techniken, z. b. [JSONP](/dotnet/framework/wcf/samples/jsonp).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="same-origin"></a>Gleicher Ursprung

Zwei URLs haben denselben Ursprung, wenn Sie identische Schemas, Hosts und Ports aufweisen ([RFC 6454](https://tools.ietf.org/html/rfc6454)).

Diese beiden URLs haben denselben Ursprung:

* `https://example.com/foo.html`
* `https://example.com/bar.html`

Diese URLs haben andere Ursprünge als die beiden vorherigen URLs:

* `https://example.net`: Andere Domäne
* `https://www.example.com/foo.html`: Untergeordnete Domäne
* `http://example.com/foo.html`: Anderes Schema
* `https://example.com:9000/foo.html`: Anderer Port

Internet Explorer berücksichtigt den Port nicht, wenn Ursprünge verglichen werden.

## <a name="cors-with-named-policy-and-middleware"></a>Cors mit benannten Richtlinie und Middleware

Cors-Middleware verarbeitet Ursprungs übergreifende Anforderungen. Der folgende Code aktiviert cors für die gesamte App mit dem angegebenen Ursprung:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

Der vorangehende Code:

* Legt den Richtlinien Namen auf " \_ myallowspecificorigins" fest. Der Richtlinien Name ist willkürlich.
* Ruft die <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> Erweiterungsmethode auf, die cors aktiviert.
* Ruft <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> mit einem [Lambda-Ausdruck](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)auf. Der Lambda-Ausdruck nimmt ein- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Objekt an. [Konfigurationsoptionen](#cors-policy-options), wie z `WithOrigins` . b., werden weiter unten in diesem Artikel beschrieben.

Der <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> -Methodenaufrufe fügt cors-Dienste zum Dienst Container der APP hinzu:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

Weitere Informationen finden Sie unter [cors-Richtlinien Optionen](#cpo) in diesem Dokument.

Die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> Methode kann Methoden verketten, wie im folgenden Code gezeigt:

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

Hinweis: die URL darf **keinen** nachgestellten Schrägstrich ( `/` ) enthalten. Wenn die URL mit beendet `/` wird, gibt der Vergleich zurück, `false` und es wird kein-Header zurückgegeben.

Mit dem folgenden Code werden cors-Richtlinien auf alle App-Endpunkte über cors-Middleware angewendet:
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
Hinweis: `UseCors` muss vor aufgerufen werden `UseMvc` .

Weitere Informationen finden Sie unter [Aktivieren von cors in Razor Seiten, Controllern und Aktionsmethoden](#ecors) zum Anwenden der cors-Richtlinie auf Seiten-/Controller-/Aktions

Anweisungen zum Testen von Code, der dem vorangehenden Code ähnelt, finden Sie unter [Test-cors](#test) .

## <a name="enable-cors-with-attributes"></a>Aktivieren von cors mit Attributen

Das [ &lbrack; enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) -Attribut bietet eine Alternative zum globalen Anwenden von cors. Das- `[EnableCors]` Attribut aktiviert cors für ausgewählte Endpunkte anstelle aller Endpunkte.

Verwenden `[EnableCors]` Sie, um die Standard Richtlinie anzugeben und `[EnableCors("{Policy String}")]` eine Richtlinie anzugeben.

Das- `[EnableCors]` Attribut kann auf Folgendes angewendet werden:

* Razor S `PageModel`
* Controller
* Controller-Aktionsmethode

Mit dem-Attribut können Sie verschiedene Richtlinien auf Controller/Seiten Modell/Aktion anwenden  `[EnableCors]` . Wenn das `[EnableCors]` -Attribut auf ein Controller/Seiten Modell/eine Aktionsmethode angewendet wird und cors in der Middleware aktiviert ist, werden **beide** Richtlinien angewendet. Wir empfehlen, Richtlinien **nicht** zu kombinieren. Verwenden Sie das `[EnableCors]` Attribut oder die Middleware, **nicht beides** . Definieren Sie bei Verwendung von `[EnableCors]` **keine** Standard Richtlinie.

Der folgende Code wendet eine andere Richtlinie auf jede Methode an:

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

Der folgende Code erstellt eine cors-Standard Richtlinie und eine Richtlinie mit dem Namen `"AnotherPolicy"` :

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>Cors deaktivieren

Das [ &lbrack; disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) -Attribut deaktiviert cors für Controller/Page-Model/Action.

<a name="cpo"></a>

## <a name="cors-policy-options"></a>Cors-Richtlinien Optionen

In diesem Abschnitt werden die verschiedenen Optionen beschrieben, die in einer cors-Richtlinie festgelegt werden können:

* [Festlegen der zulässigen Ursprünge](#set-the-allowed-origins)
* [Festlegen der zulässigen HTTP-Methoden](#set-the-allowed-http-methods)
* [Festlegen der zulässigen Anforderungs Header](#set-the-allowed-request-headers)
* [Festlegen der verfügbar gemachten Antwortheader](#set-the-exposed-response-headers)
* [Anmelde Informationen in Ursprungs übergreifenden Anforderungen](#credentials-in-cross-origin-requests)
* [Festlegen der Preflight-Ablaufzeit](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> wird in aufgerufen `Startup.ConfigureServices` . Für einige Optionen kann es hilfreich sein, zuerst den Abschnitt [wie cors Works](#how-cors) zu lesen.

## <a name="set-the-allowed-origins"></a>Festlegen der zulässigen Ursprünge

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Ermöglicht cors-Anforderungen von allen Ursprüngen mit einem beliebigen Schema ( `http` oder `https` ). `AllowAnyOrigin` ist unsicher, weil *jede Website* Ursprungs übergreifende Anforderungen an die APP stellen kann.

> [!NOTE]
> Die Angabe von `AllowAnyOrigin` und `AllowCredentials` ist eine unsichere Konfiguration und kann zu einer Website übergreifenden Anforderungs Fälschung führen. Geben Sie für eine sichere App eine exakte Liste von Ursprüngen an, wenn sich der Client für den Zugriff auf Server Ressourcen selbst autorisieren muss.

`AllowAnyOrigin` wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Origin` Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Legt fest, dass die- <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Eigenschaft der Richtlinie eine Funktion ist, die es den Ursprüngen ermöglicht, eine konfigurierte Platzhalter Domäne zuzuordnen, wenn ausgewertet wird, ob der Ursprung zulässig ist

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>Festlegen der zulässigen HTTP-Methoden

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* Lässt jede HTTP-Methode zu:
* Wirkt sich auf Preflight-Anforderungen und den `Access-Control-Allow-Methods` Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

### <a name="set-the-allowed-request-headers"></a>Festlegen der zulässigen Anforderungs Header

Um zu ermöglichen, dass bestimmte Header in einer cors-Anforderung gesendet werden, die als *Autor Request Headers* bezeichnet wird, müssen Sie aufrufen <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> und die zulässigen Header angeben:

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Diese Einstellung wirkt sich auf Preflight-Anforderungen und den- `Access-Control-Request-Headers` Header aus. Weitere Informationen finden Sie im Abschnitt [Preflight Requests (Preflight-Anforderungen](#preflight-requests) ).

Cors-Middleware erlaubt immer, dass vier Header in der `Access-Control-Request-Headers` gesendet werden, unabhängig von den in corspolicy. Headers konfigurierten Werten. Diese Liste von Headern umfasst Folgendes:

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

Nehmen Sie beispielsweise an, dass eine APP wie folgt konfiguriert ist:

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

Cors-Middleware antwortet erfolgreich auf eine Preflight-Anforderung mit dem folgenden Anforderungs Header, da `Content-Language` immer zulässig ist:

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>Festlegen der verfügbar gemachten Antwortheader

Standardmäßig macht der Browser nicht alle Antwortheader für die app verfügbar. Weitere Informationen finden Sie unter [W3C Cross-Origin Resource Sharing (Terminologie): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).

Die standardmäßig verfügbaren Antwortheader lauten wie folgt:

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

Mit der cors-Spezifikation werden die Header für *einfache Antworten* aufgerufen. Um andere Header für die app verfügbar zu machen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>Anmelde Informationen in Ursprungs übergreifenden Anforderungen

Anmelde Informationen erfordern eine spezielle Behandlung in einer cors-Anforderung. Standardmäßig sendet der Browser keine Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung. Anmelde Informationen umfassen cookie e-und http-Authentifizierungs Schemas. Um Anmelde Informationen mit einer Ursprungs übergreifenden Anforderung zu senden, muss der Client `XMLHttpRequest.withCredentials` auf festlegen `true` .

`XMLHttpRequest`Direkt verwenden:

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

Verwenden von jQuery:

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

Verwenden der [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API):

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

Der Server muss die Anmelde Informationen zulassen. Um Ursprungs übergreifende Anmelde Informationen zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

Die HTTP-Antwort enthält einen- `Access-Control-Allow-Credentials` Header, der dem Browser mitteilt, dass der Server Anmelde Informationen für eine Ursprungs übergreifende Anforderung zulässt.

Wenn der Browser Anmelde Informationen sendet, die Antwort jedoch keinen gültigen `Access-Control-Allow-Credentials` Header enthält, macht der Browser die Antwort nicht an die app verfügbar, und die Ursprungs übergreifende Anforderung schlägt fehl.

Das Zulassen von Ursprungs übergreifenden Anmelde Informationen stellt ein Sicherheitsrisiko dar. Eine Website in einer anderen Domäne kann die Anmelde Informationen des angemeldeten Benutzers ohne das Wissen des Benutzers an die APP im Auftrag des Benutzers senden. <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

In der cors-Spezifikation wird auch festgelegt, dass das Festlegen von Ursprüngen auf `"*"` (alle Ursprünge) ungültig ist, wenn der `Access-Control-Allow-Credentials` Header vorhanden ist.

### <a name="preflight-requests"></a>Preflight-Anforderungen

Bei einigen cors-Anforderungen sendet der Browser eine zusätzliche Anforderung, bevor die tatsächliche Anforderung gesendet wird. Diese Anforderung wird als *Preflight-Anforderung* bezeichnet. Der Browser kann die Preflight-Anforderung überspringen, wenn die folgenden Bedingungen zutreffen:

* Die Anforderungs Methode ist Get, Head oder Post.
* Von der App werden keine anderen Anforderungs Header als `Accept` , `Accept-Language` , `Content-Language` , `Content-Type` oder `Last-Event-ID` festgelegt.
* `Content-Type`Wenn festgelegt, hat der-Header einen der folgenden Werte:
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

Die Regel für Anforderungs Header, die für die Client Anforderung festgelegt wurde, gilt für Header, die die APP festlegt, indem `setRequestHeader` für das-Objekt aufgerufen wird `XMLHttpRequest` . In der cors-Spezifikation werden diese Header *Anforderungs Header für Autoren* aufgerufen. Die Regel gilt nicht für Header, die vom Browser festgelegt werden können, z `User-Agent` `Host` . b., oder `Content-Length` .

Im folgenden finden Sie ein Beispiel für eine Preflight-Anforderung:

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

Die Pre-Flight-Anforderung verwendet die HTTP OPTIONS-Methode. Es enthält zwei spezielle Header:

* `Access-Control-Request-Method`: Die HTTP-Methode, die für die tatsächliche Anforderung verwendet wird.
* `Access-Control-Request-Headers`: Eine Liste mit Anforderungs Headern, die von der APP für die tatsächliche Anforderung festgelegt werden. Wie bereits erwähnt, enthält dies keine Header, die vom Browser festgelegt werden, z `User-Agent` . b..

<!-- I think this needs to be removed, was put here accidently -->

Wenn cors mit der entsprechenden Richtlinie aktiviert ist, antwortet ASP.net Core in der Regel automatisch auf cors-Preflight-Anforderungen. Weitere Informationen finden Sie unter [[httpoptions]-Attribut für Preflight-Anforderungen](#pro).

Eine cors-Preflight-Anforderung kann einen- `Access-Control-Request-Headers` Header enthalten, der dem Server die Header angibt, die mit der tatsächlichen Anforderung gesendet werden.

Um bestimmte Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

Um alle Autoren Anforderungs Header zuzulassen, geben Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

Browser sind nicht vollständig konsistent, wenn Sie festgelegt werden `Access-Control-Request-Headers` . Wenn Sie Header auf einen anderen Wert als festlegen `"*"` (oder verwenden <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> ), sollten Sie mindestens `Accept` , `Content-Type` , und `Origin` sowie alle benutzerdefinierten Header einschließen, die Sie unterstützen möchten.

Im folgenden finden Sie ein Beispiel für eine Antwort auf die Preflight-Anforderung (vorausgesetzt, der Server lässt die Anforderung zu):

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

Die Antwort enthält einen `Access-Control-Allow-Methods` Header, der die zulässigen Methoden auflistet, und optional einen `Access-Control-Allow-Headers` Header, der die zulässigen Header auflistet. Wenn die Preflight-Anforderung erfolgreich ist, sendet der Browser die tatsächliche Anforderung.

Wenn die Preflight-Anforderung verweigert wird, gibt die APP eine *200 OK* -Antwort zurück, sendet jedoch keine cors-Header zurück. Der Browser versucht daher nicht, die Ursprungs übergreifende Anforderung zu verwenden.

### <a name="set-the-preflight-expiration-time"></a>Festlegen der Preflight-Ablaufzeit

Der- `Access-Control-Max-Age` Header gibt an, wie lange die Antwort auf die Preflight-Anforderung zwischengespeichert werden kann. Um diesen Header festzulegen, wenden Sie Folgendes an <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>Funktionsweise von cors

In diesem Abschnitt wird beschrieben, was in einer [cors](https://developer.mozilla.org/docs/Web/HTTP/CORS) -Anforderung auf der Ebene der HTTP-Nachrichten geschieht.

* Cors ist **kein** Sicherheits Feature. Cors ist ein W3C-Standard, der es einem Server ermöglicht, die Richtlinie für denselben Ursprung zu lockern.
  * Beispielsweise könnte ein böswilliger Akteur die Verwendung von [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) für Ihre Website verhindern und eine Website übergreifende Anforderung an Ihre cors-aktivierte Website ausführen, um Informationen zu stehlen.
* Ihre API ist nicht sicherer, da Sie cors zulässt.
  * Es liegt an dem Client (Browser), cors zu erzwingen. Der Server führt die Anforderung aus und gibt die Antwort zurück. dabei handelt es sich um den Client, der einen Fehler zurückgibt und die Antwort blockiert. Eines der folgenden Tools zeigt z. b. die Serverantwort an:
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.Net httpclient](/dotnet/csharp/tutorials/console-webapiclient)
    * Einen Webbrowser, indem Sie die URL in die Adressleiste eingeben.
* Es ist eine Möglichkeit für einen Server, Browser das Ausführen einer Ursprungs übergreifenden [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) -oder [Fetch-API](https://developer.mozilla.org/docs/Web/API/Fetch_API) -Anforderung zu gestatten, die andernfalls unzulässig wäre.
  * Browser (ohne cors) können keine Ursprungs übergreifenden Anforderungen ausführen. Vor cors wurde [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) verwendet, um diese Einschränkung zu umgehen. JSONP verwendet nicht XHR, sondern verwendet das- `<script>` Tag, um die Antwort zu empfangen. Skripts können Ursprungs übergreifend geladen werden.

In der [cors-Spezifikation](https://www.w3.org/TR/cors/) wurden mehrere neue HTTP-Header eingeführt, die Ursprungs übergreifende Anforderungen ermöglichen. Wenn ein Browser cors unterstützt, werden diese Header automatisch für Ursprungs übergreifende Anforderungen festgelegt. Benutzerdefinierter JavaScript-Code ist nicht erforderlich, um cors zu aktivieren.

Im folgenden finden Sie ein Beispiel für eine Ursprungs übergreifende Anforderung. Der `Origin` -Header stellt die Domäne der Site bereit, von der die Anforderung stammt. Der `Origin` -Header ist erforderlich und muss sich vom Host unterscheiden.

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

Wenn der Server die Anforderung zulässt, wird der- `Access-Control-Allow-Origin` Header in der Antwort festgelegt. Der Wert dieses Headers stimmt entweder mit dem `Origin` Header aus der Anforderung überein, oder ist der Platzhalter Wert `"*"` , was bedeutet, dass ein beliebiger Ursprung zulässig ist:

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

Wenn die Antwort den Header nicht enthält `Access-Control-Allow-Origin` , schlägt die Ursprungs übergreifende Anforderung fehl. Der Browser lässt die Anforderung insbesondere nicht zu. Auch wenn der Server eine erfolgreiche Antwort zurückgibt, stellt der Browser die Antwort nicht für die Client-App zur Verfügung.

<a name="test"></a>

## <a name="test-cors"></a>Testen von CORS

So testen Sie cors:

1. [Erstellen Sie ein API-Projekt](xref:tutorials/first-web-api). Alternativ können Sie [das Beispiel herunterladen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).
1. Aktivieren Sie cors mithilfe eines der Vorgehensweisen in diesem Dokument. Zum Beispiel:

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` sollte nur zum Testen einer Beispiel-App verwendet werden, die dem [Download Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)ähnelt.

1. Erstellen Sie ein Web-App-Projekt ( Razor Pages oder MVC). Im Beispiel werden Razor Seiten verwendet. Sie können die Web-App in der gleichen Projekt Mappe wie das API-Projekt erstellen.
1. Fügen Sie der Datei " *Index. cshtml* " den folgenden hervorgehobenen Code hinzu:

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. Ersetzen Sie im vorangehenden Code `url: 'https://<web app>.azurewebsites.net/api/values/1',` durch die URL zur bereitgestellten app.
1. Stellen Sie das API-Projekt bereit. Nehmen Sie beispielsweise die Bereitstellung [in Azure vor](xref:host-and-deploy/azure-apps/index).
1. Führen Razor Sie die Seiten oder die MVC-App über den Desktop aus, und klicken Sie auf die Schaltfläche **Testen** . Verwenden Sie die F12-Tools, um Fehlermeldungen zu überprüfen.
1. Entfernen Sie den localhost-Ursprung aus, und stellen Sie `WithOrigins` die APP bereit. Alternativ können Sie die Client-App mit einem anderen Port ausführen. Führen Sie z. b. in Visual Studio aus.
1. Testen Sie mit der Client-App. Cors-Fehler geben einen Fehler zurück, aber die Fehlermeldung ist für JavaScript nicht verfügbar. Verwenden Sie die Registerkarte Konsole in den F12-Tools, um den Fehler anzuzeigen. Abhängig vom Browser erhalten Sie eine Fehlermeldung (in der F12-toolkonsole) ähnlich der folgenden:

   * Verwenden von Microsoft Edge:

     **SEC7120: [cors] der Ursprung `https://localhost:44375` wurde `https://localhost:44375` im Antwortheader "Access-Control-Allow-Origin" für die Ursprungs übergreifende Ressource nicht gefunden in `https://webapi.azurewebsites.net/api/values/1`**

   * Verwenden von Chrome:

     **Der Zugriff auf XMLHttpRequest auf `https://webapi.azurewebsites.net/api/values/1` der Ursprungsseite wurde `https://localhost:44375` durch die cors-Richtlinie blockiert: für die angeforderte Ressource ist kein "Access-Control-Allow-Origin"-Header vorhanden.**
     
Cors-aktivierte Endpunkte können mit einem Tool wie z. b. " [fddler](https://www.telerik.com/fiddler) " oder [Postman](https://www.getpostman.com/)getestet werden. Wenn Sie ein Tool verwenden, muss sich der Ursprung der durch den-Header angegebenen Anforderung `Origin` von dem Host unterscheiden, der die Anforderung empfängt. Wenn die Anforderung aufgrund des Werts des Headers nicht *Kreuz Ursprungs* ist `Origin` :

* Es ist nicht erforderlich, dass cors-Middleware die Anforderung verarbeitet.
* Cors-Header werden in der Antwort nicht zurückgegeben.

## <a name="cors-in-iis"></a>Cors in IIS

Beim Bereitstellen in IIS muss cors vor der Windows-Authentifizierung ausgeführt werden, wenn der Server nicht für den anonymen Zugriff konfiguriert ist. Zur Unterstützung dieses Szenarios muss das [IIS cors-Modul](https://www.iis.net/downloads/microsoft/iis-cors-module) für die APP installiert und konfiguriert werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Cross-Origin Resource Sharing (cors)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [Einführung in das IIS cors-Modul](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
