---
title: ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie mehr über ASP.NET Core-Middleware und die Anforderungspipeline.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2020
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
uid: fundamentals/middleware/index
ms.openlocfilehash: aa51e53284bc25629b3975ff0e6de967b9a2b866
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513121"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core-Middleware

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten. Jede Komponente kann Folgendes ausführen:

* Entscheiden, ob die Anforderung an die nächste Komponente in der Pipeline übergeben werden soll.
* Ausführen von Arbeiten, bevor oder nachdem die nächste Komponente in der Pipeline aufgerufen wird.

Anforderungsdelegaten werden verwendet, um die Anforderungspipeline zu erstellen. Die Anforderungsdelegaten behandeln jede HTTP-Anforderung.

Anforderungsdelegaten werden mit den Erweiterungsmethoden <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> und <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> konfiguriert. Ein einzelner Anforderungsdelegat kann inline als anonyme Methode angegeben werden (sogenannte Inline-Middleware), oder er kann in einer wiederverwendbaren Klasse definiert werden. Diese wiederverwendbaren Klassen und anonymen Inline-Methoden sind *Middleware* bzw. *Middlewarekomponenten*. Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Pipeline zuständig. Wenn eine Middleware einen Kurzschluss verursacht, wird diese als *Terminalmiddleware* bezeichnet, da sie verhindert, dass weitere Middleware die Anforderung verarbeiten kann.

Unter <xref:migration/http-modules> wird der Unterschied zwischen Anforderungspipelines in ASP.NET Core und ASP.NET 4.x erklärt. Außerdem werden dort zusätzliche Beispiele für Middleware bereitgestellt.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Erstellen einer Middlewarepipeline mit IApplicationBuilder

Die ASP.NET Core-Anforderungspipeline besteht aus einer Sequenz von Anforderungsdelegaten, die nacheinander aufgerufen werden. Das Konzept wird im folgenden Diagramm veranschaulicht. Der Ausführungsthread folgt den schwarzen Pfeilen.

![Anforderungsverarbeitungsmuster mit eingehender Anforderung, deren Verarbeitung von drei Middlewares und die ausgehende Antwort der Anwendung. Jede Middleware führt ihre Logik aus und übergibt die Anforderung an der next()-Anweisung an die nächste Middleware. Nachdem die Anforderung von der dritten Middleware verarbeitet wurde, wird sie wieder an die vorherigen Middlewares in umgekehrter Reihenfolge übergeben, nachdem die next()-Anweisungen erreicht wurden. Dann verlässt sie die Anwendung als Antwort an den Client.](index/_static/request-delegate-pipeline.png)

Jeder Delegat kann Vorgänge vor und nach dem nächsten Delegaten ausführen. Die Ausnahmebehandlungsdelegaten müssen am Anfang der Pipeline aufgerufen werden, sodass sie Ausnahmen abfangen können, die zu einem späteren Zeitpunkt in der Pipeline ausgelöst werden.

Die einfachste mögliche ASP.NET Core-App enthält einen einzigen Anforderungsdelegaten, der alle Anforderungen verarbeitet. In diesem Fall ist keine tatsächliche Anforderungspipeline vorhanden. Stattdessen wird eine einzelne anonyme Funktion als Antwort auf jede HTTP-Anforderung aufgerufen.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Mit <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> können Sie mehrere Anforderungedelegate miteinander verknüpfen. Der Parameter `next` steht für den nächsten Delegaten in der Pipeline. Sie können die Pipeline kurzschließen, indem Sie den Parameter *next* *nicht* aufrufen. Normalerweise können Sie Aktionen sowohl vor als auch nach dem nächsten Delegaten durchführen. Dies wird in folgendem Beispiel veranschaulicht:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

Wenn ein keine Anforderung an den nächsten Delegaten übergibt, wird dies als *Kurzschluss der Anforderungspipeline* bezeichnet. Das Kurzschließen ist oft sinnvoll, da es unnötige Arbeit verhindert. Die [Middleware für statische Dateien](xref:fundamentals/static-files) kann beispielsweise als *Terminalmiddleware* fungieren, indem sie eine Anforderung für eine statische Datei zurückgibt und den Rest der Pipeline kurzschließt. Middleware, die noch vor der Middleware, die die weitere Verarbeitung beendet, zur Pipeline hinzugefügt wird, verarbeitet Code noch nach den `next.Invoke`-Anweisungen weiter. Sehen Sie sich allerdings die folgende Warnung zum Versuch an, in eine Antwort zu schreiben, die bereits gesendet wurde.

> [!WARNING]
> Rufen Sie `next.Invoke` nicht auf, nachdem die Antwort an den Client gesendet wurde. An <xref:Microsoft.AspNetCore.Http.HttpResponse> vorgenommene Änderungen lösen nach dem Start der Antwort eine Ausnahme aus. Das [Festlegen von Headern und einem Statuscode lösen beispielsweise eine Ausnahme aus](xref:performance/performance-best-practices#do-not-modify-the-status-code-or-headers-after-the-response-body-has-started). Wenn Sie nach dem Aufruf von `next` in den Antworttext schreiben, kann dies:
>
> * einen Protokollverstoß verursachen, wenn Sie z.B. mehr als das genannte `Content-Length`-Objekt schreiben.
> * Fehler im Textformat auslösen, wenn Sie z.B. eine HTML-Fußzeile in eine CSS-Datei schreiben.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> ist ein nützlicher Hinweis, der angibt, ob Header gesendet wurden oder ob in den Text geschrieben wurde.

<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>-Delegaten erhalten keinen `next`-Parameter. Der erste `Run`-Delegat beendet immer die Pipeline. `Run` ist eine Konvention. Einige Middlewarekomponenten machen möglicherweise `Run[Middleware]`-Methoden verfügbar, die am Ende einer Pipeline ausgeführt werden:

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Im vorherigen Beispiel schreibt der `Run`-Delegat `"Hello from 2nd delegate."` zur Antwort und beendet dann die Pipeline. Wenn ein anderer `Use`- oder `Run`-Delegat nach dem `Run`-Delegaten hinzugefügt wird, wird dieser nicht aufgerufen.

<a name="order"></a>

## <a name="middleware-order"></a>Middlewarereihenfolge

In der folgenden Abbildung wird die gesamte Anforderungsverarbeitungspipeline für MVC- und Razor Pages-Apps in ASP.NET Core dargestellt. Es ist zu sehen, wie vorhandene Middleware in einer typischen App sortiert ist und an welcher Stelle benutzerdefinierte Middleware hinzugefügt wird. Sie haben vollständige Kontrolle darüber, wie vorhandene Middleware neu angeordnet oder neue benutzerdefinierte Middleware nach Bedarf eingefügt wird.

![ASP.NET Core-Middlewarepipeline](index/_static/middleware-pipeline.svg)

Der **Endpunktmiddleware** in der vorangehenden Abbildung führt die Filterpipeline für den entsprechenden App-Typ aus (MVC oder Razor Pages).

![ASP.NET Core-Filterpipeline](index/_static/mvc-endpoint.svg)

Die Reihenfolge, in der Middlewarekomponenten in der `Startup.Configure`-Methode hinzugefügt werden, legt die Reihenfolge fest, in der die Middlewarekomponenten bei Anforderungen aufgerufen werden. Bei Antworten gilt die umgekehrte Reihenfolge. Die Reihenfolge ist in Bezug auf Sicherheit, Leistung und Funktionalität **entscheidend**.

Die folgende `Startup.Configure`-Methode fügt sicherheitsbezogene Middlewarekomponenten in der typischen empfohlenen Reihenfolge hinzu:

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

Für den Code oben gilt:

* Middleware, die beim Erstellen einer neuen Web-App mit [einzelnen Benutzerkonten](xref:security/authentication/identity) nicht hinzugefügt wird, wird auskommentiert.
* Nicht jede Middleware muss in genau dieser Reihenfolge vorliegen, aber für viele Middlewarekomponenten ist dies erforderlich. Zum Beispiel:
  * `UseCors`, `UseAuthentication` und `UseAuthorization` müssen in der gezeigten Reihenfolge vorliegen.
  * `UseCors` muss sich derzeit aufgrund [dieses Fehlers](https://github.com/dotnet/aspnetcore/issues/23218) vor `UseResponseCaching` befinden.

In einigen Szenarien gilt bei Middleware eine andere Reihenfolge. Die Reihenfolge für Zwischenspeicherung und Komprimierung beispielsweise ist szenariospezifisch, und es gibt verschiedene gültige Reihenfolgen. Beispiel:

```csharp
app.UseResponseCaching();
app.UseResponseCompression();
```

Mit dem obigen Code könnte durch Zwischenspeichern der komprimierten Antwort die CPU-Auslastung verringert werden, dies könnte aber dazu führen, dass mehrere Darstellungen einer Ressource mithilfe verschiedener Komprimierungsalgorithmen wie z. B. gzip oder brotli zwischengespeichert werden.

Die folgende Reihenfolge kombiniert statische Dateien, um eine Zwischenspeicherung komprimierter statischer Dateien zuzulassen:

```csharp
app.UseResponseCaching
app.UseResponseCompression
app.UseStaticFiles
```

Die folgenden `Startup.Configure`-Methode fügt Middlewarekomponenten für allgemeine App-Szenarien hinzu:

1. Ausnahme-/Fehlerbehandlung
   * Bei Ausführung der App in der Entwicklungsumgebung:
     * Middleware der Seite mit Ausnahmen für Entwickler (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) meldet App-Laufzeitfehler.
     * Middleware der Seite mit Datenbankfehlern meldet Datenbanklaufzeitfehler.
   * Bei Ausführung der App in der Produktionsumgebung:
     * Middleware für Ausnahmehandler (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) fängt Ausnahmen ab, die in den folgenden Middlewarekomponenten ausgelöst werden.
     * HTTP Strict Transport Security Protocol-Middleware (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) fügt den `Strict-Transport-Security`-Header hinzu.
1. Middleware zur HTTPS-Umleitung (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) leitet HTTP-Anforderungen an HTTPS um.
1. Middleware für statische Dateien (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) gibt statische Dateien zurück und umgeht die weitere Anforderungsverarbeitung.
1. Middleware für Cookierichtlinien (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) sorgt dafür, dass die App die Anforderungen der europäischen Datenschutzgrundverordnung (DSGVO) erfüllt.
1. Routingmiddleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>) zum Weiterleiten von Anforderungen.
1. Middleware für die Authentifizierung (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) versucht, den Benutzer zu authentifizieren, bevor der Zugriff auf sichere Ressourcen zugelassen wird.
1. Autorisierungsmiddleware (<xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>) wird zum Autorisieren des Zugriffs auf sichere Ressourcen eines Benutzers verwendet.
1. Middleware für Sitzungen (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) richtet einen Sitzungsstatus ein und erhält diesen aufrecht. Wenn die App den Sitzungsstatus verwendet, rufen Sie die Middleware für Sitzungen nach der Middleware für Cookierichtlinien und vor der MVC-Middleware auf.
1. Endpunktroutingmiddleware (<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> mit <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages%2A>) zum Hinzufügen von Razor Pages-Endpunkten zur Anforderungspipeline.

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Im vorhergehenden Beispielcode wird jede Middleware-Erweiterungsmethode in <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> über den <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>-Namespace verfügbar gemacht.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ist die erste Middlewarekomponente, die der Pipeline hinzugefügt wird. Aus diesem Grund fängt die Middleware für den Ausnahmehandler alle Ausnahmen ab, die in späteren Aufrufen auftreten.

Die Middleware für statische Dateien wird am Anfang der Pipeline aufgerufen, damit sie Anforderungen und Kurzschlüsse verarbeiten kann, ohne dass die verbleibenden Komponenten durchlaufen werden müssen. Die Middleware für statische Dateien stellt **keine** Autorisierungsüberprüfungen bereit. Alle Dateien, die von Middleware für statische Dateien unterstützt werden, einschließlich der Dateien unter *wwwroot*, sind öffentlich verfügbar. Informationen zum Sichern statischer Dateien finden Sie unter <xref:fundamentals/static-files>.

Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Authentifizierungsmiddleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) übergeben, welche die Authentifizierung durchführt. Die Authentifizierung schließt nicht authentifizierte Anforderungen nicht kurz. Auch wenn die Authentifizierungsmiddleware Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC eine spezifische Razor Page oder einen MVC-Controller und eine Aktion ausgewählt hat.

Im folgenden Beispiel wird eine Middlewarereihenfolge veranschaulicht, bei der Anforderungen statischer Dateien von der Middleware für statische Dateien vor der Middleware für die Antwortkomprimierung verarbeitet werden. Die statischen Dateien werden bei dieser Middlewarereihenfolge nicht komprimiert. Die Razor Pages-Antworten können komprimiert werden.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

Bei Single-Page-Webanwendungen (Single-Page Applications, SPAs) ist die entsprechende SPA-Middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles%2A> in der Regel der letzte Teil der Middlewarepipeline. Die SPA-Middleware kommt zuletzt, damit:

* Jede andere Middleware zuerst auf entsprechende Anforderungen reagieren können.
* SPAs mit clientseitigem Routing für alle Routen ausgeführt werden können, die von der Server-App nicht erkannt werden.

Weitere Informationen zu SPAs finden Sie in den Anleitungen zu den [React](xref:spa/react)- und [Angular](xref:spa/angular)-Projektvorlagen.

### <a name="forwarded-headers-middleware-order"></a>Middleware für weitergeleitete Header: Auftrag

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

## <a name="branch-the-middleware-pipeline"></a>Branchen der Middlewarepipeline

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>-Erweiterungen werden als Konvention zum Branchen der Pipeline verwendet. `Map` brancht die Anforderungspipeline auf Grundlage von Übereinstimmungen des angegebenen Anforderungspfads. Wenn der Anforderungspfad mit dem angegebenen Pfad beginnt, wird der Branch ausgeführt.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.

| Anforderung             | Antwort                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Bei Verwendung von `Map` werden die übereinstimmenden Pfadsegmente aus `HttpRequest.Path` entfernt und für jede Anforderung an `HttpRequest.PathBase` angehängt.

`Map` unterstützt das Schachteln, wie z.B. in folgendem Code:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` kann auch mehrere Segmente auf einmal zuordnen:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> brancht die Anforderungspipeline auf Grundlage des Ergebnisses des angegebenen Prädikats. Jedes Prädikat vom Typ `Func<HttpContext, bool>` kann verwendet werden, um Anforderungen einem neuen Branch der Pipeline zuzuordnen. Im folgenden Beispiel wird ein Prädikat verwendet, um das Vorhandensein der Abfragezeichenfolgenvariablen `branch` zu ermitteln:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet:

| Anforderung                       | Antwort                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

<xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen%2A> brancht auch die Anforderungspipeline auf Grundlage des Ergebnisses des angegebenen Prädikats. Anders als bei `MapWhen` wird dieser Branch wieder mit der Hauptpipeline verbunden, wenn er nicht kurzgeschlossen wird oder eine Terminalmiddleware enthält:

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=25-26)]

Im vorherigen Beispiel wird „Hello from main pipeline“ (Hallo aus der Hauptpipeline) für alle Anforderungen geschrieben. Wenn die Anforderung eine Abfragezeichenfolgevariable `branch` enthält, wird der Wert der Pipeline protokolliert, bevor eine neue Verbindung hergestellt wird.

## <a name="built-in-middleware"></a>Integrierte Middleware

Die folgenden Middlewarekomponenten sind im Lieferumfang von ASP.NET Core enthalten. Die Spalte *Reihenfolge* enthält Hinweise zur Platzierung der Middleware in der Pipeline, die die Anforderung verarbeitet, und zu den Bedingungen, unter denen die Middleware die Anforderungsverarbeitung möglicherweise beendet. Wenn eine Middleware einen Kurzschluss in der Anforderungsverarbeitungspipeline verursacht und verhindert, dass Downstreammiddleware eine Anforderung verarbeitet, wird diese als *Terminalmiddleware* bezeichnet. Weitere Informationen zu Kurzschlüssen finden Sie im Abschnitt [Erstellen einer Middlewarepipeline mit IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Beschreibung | Auftrag |
| ---------- | ----------- | ----- |
| [Authentifizierung](xref:security/authentication/identity) | Bietet Unterstützung für Authentifizierungen. | Bevor `HttpContext.User` erforderlich ist. Terminal für OAuth-Rückrufe. |
| [Autorisierung](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) | Bietet Unterstützung für Authentifizierungen | Direkt nach der Authentifizierungsmiddleware |
| [Cookierichtlinie](xref:security/gdpr) | Verfolgt die Zustimmung von Benutzern zum Speichern persönlicher Informationen und erzwingt die Mindeststandards für cookiefelder, z. B. `secure` und `SameSite`. | Befindet sich vor der Middleware, die cookies ausstellt. Beispiele: Authentifizierung, Sitzung, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguriert die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS). | Vor Komponenten, die CORS verwenden. `UseCors` muss sich derzeit aufgrund [dieses Fehlers](https://github.com/dotnet/aspnetcore/issues/23218) vor `UseResponseCaching` befinden.|
| [Diagnose](xref:fundamentals/error-handling) | Mehrere separate Middlewares, die Entwicklern eine Ausnahmeseite, Ausnahmebehandlung, Statuscodeseiten und die Standardwebseite für neue Apps bereitstellen. | Vor Komponenten, die Fehler erzeugen. Terminal für Ausnahmen oder zum Bereitstellen der Standardwebseite für neue Apps. |
| [Weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) | Leitet Proxyheader an die aktuelle Anforderung weiter. | Vor Komponenten, die die aktualisierten Felder nutzen. Beispiele: Schema, Host, Client-IP, Methode. |
| [Integritätsprüfung](xref:host-and-deploy/health-checks) | Überprüft die Integrität der ASP.NET Core-App und ihrer Abhängigkeiten, z. B. Überprüfung der Datenbankverfügbarkeit. | Abschließend, wenn eine Anforderung mit einem Integritätsprüfungs-Endpunkt übereinstimmt. |
| [Headerweitergabe](xref:fundamentals/http-requests#header-propagation-middleware) | Überträgt HTTP-Header aus der eingehenden Anforderung zu ausgehenden HTTP-Clientanforderungen |
| [Außerkraftsetzung der HTTP-Methode](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Ermöglicht es eingehenden POST-Anforderungen, die Methode außer Kraft zu setzen. | Vor Komponenten, die die aktualisierte Methode nutzen. |
| [HTTPS-Umleitung](xref:security/enforcing-ssl#require-https) | Leitet alle HTTP-Anforderungen an HTTPS um. | Vor Komponenten, die die URL nutzen. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware für erweiterte Sicherheit, die einen besonderen Antwortheader hinzufügt. | Bevor Antworten gesendet werden und nach Komponenten, die Anforderungen ändern. Beispiele: weitergeleitete Header, URL-Umschreibung. |
| [MVC](xref:mvc/overview) | Verarbeitet Anforderungen mit MVC/Razor Pages. | Abschließend, wenn eine Anforderung mit einer Route übereinstimmt. |
| [OWIN](xref:fundamentals/owin) | Interoperabilität mit auf OWIN basierten Apps, Servern und Middleware. | Abschließend, wenn die OWIN-Middleware die Anforderung vollständig verarbeitet. |
| [Zwischenspeichern von Antworten](xref:performance/caching/middleware) | Bietet Unterstützung für das Zwischenspeichern von Antworten. | Vor Komponenten, für die das Zwischenspeichern erforderlich ist. `UseCORS` muss sich vor `UseResponseCaching` befinden.|
| [Antwortkomprimierung](xref:performance/response-compression) | Bietet Unterstützung für das Komprimieren von Antworten. | Vor Komponenten, für die das Komprimieren erforderlich ist. |
| [Lokalisierung von Anforderungen](xref:fundamentals/localization) | Bietet Unterstützung für die Lokalisierung. | Vor der Lokalisierung vertraulicher Komponenten. |
| [Endpunktrouting](xref:fundamentals/routing) | Definiert Anforderungsrouten und schränkt diese ein. | Terminal für entsprechende Routen. |
| [SPA](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa%2A) | Verarbeitet alle Anforderungen ab diesem Punkt in der Middlewarekette, indem die Standardseite für die Single-Page-Anwendung zurückgegeben wird. | Kommt spät in der Kette, sodass andere Middleware, z. B. die zum Bereitstellen von statischen Dateien oder MVC-Aktionen, Vorrang hat.|
| [Sitzung](xref:fundamentals/app-state) | Bietet Unterstützung für das Verwalten von Benutzersitzungen. | Vor Komponenten, für die Sitzungen erforderlich sind. | 
| [Statische Dateien](xref:fundamentals/static-files) | Bietet Unterstützung für das Verarbeiten statischer Dateien und das Durchsuchen des Verzeichnisses. | Abschließend, wenn eine Anforderung mit einer Datei übereinstimmt. |
| [Umschreiben einer URL](xref:fundamentals/url-rewriting) | Bietet Unterstützung für das Umschreiben von URLs und das Umleiten von Anforderungen. | Vor Komponenten, die die URL nutzen. |
| [WebSockets](xref:fundamentals/websockets) | Aktiviert das WebSockets-Protokoll. | Vor Komponenten, die WebSocket-Anforderungen annehmen müssen. |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Die [Lebensdauer und Registrierungsoptionen](xref:fundamentals/dependency-injection#lifetime-and-registration-options) enthalten ein vollständiges Beispiel der Middleware mit Diensten mit *bereichsbezogener*, *vorübergehender* und *Singletonlebensdauer*.
* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten. Jede Komponente kann Folgendes ausführen:

* Entscheiden, ob die Anforderung an die nächste Komponente in der Pipeline übergeben werden soll.
* Ausführen von Arbeiten, bevor oder nachdem die nächste Komponente in der Pipeline aufgerufen wird.

Anforderungsdelegaten werden verwendet, um die Anforderungspipeline zu erstellen. Die Anforderungsdelegaten behandeln jede HTTP-Anforderung.

Anforderungsdelegaten werden mit den Erweiterungsmethoden <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A> und <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> konfiguriert. Ein einzelner Anforderungsdelegat kann inline als anonyme Methode angegeben werden (sogenannte Inline-Middleware), oder er kann in einer wiederverwendbaren Klasse definiert werden. Diese wiederverwendbaren Klassen und anonymen Inline-Methoden sind *Middleware* bzw. *Middlewarekomponenten*. Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Pipeline zuständig. Wenn eine Middleware einen Kurzschluss verursacht, wird diese als *Terminalmiddleware* bezeichnet, da sie verhindert, dass weitere Middleware die Anforderung verarbeiten kann.

Unter <xref:migration/http-modules> wird der Unterschied zwischen Anforderungspipelines in ASP.NET Core und ASP.NET 4.x erklärt. Außerdem werden dort zusätzliche Beispiele für Middleware bereitgestellt.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Erstellen einer Middlewarepipeline mit IApplicationBuilder

Die ASP.NET Core-Anforderungspipeline besteht aus einer Sequenz von Anforderungsdelegaten, die nacheinander aufgerufen werden. Das Konzept wird im folgenden Diagramm veranschaulicht. Der Ausführungsthread folgt den schwarzen Pfeilen.

![Anforderungsverarbeitungsmuster mit eingehender Anforderung, deren Verarbeitung von drei Middlewares und die ausgehende Antwort der Anwendung. Jede Middleware führt ihre Logik aus und übergibt die Anforderung an der next()-Anweisung an die nächste Middleware. Nachdem die Anforderung von der dritten Middleware verarbeitet wurde, wird sie wieder an die vorherigen Middlewares in umgekehrter Reihenfolge übergeben, nachdem die next()-Anweisungen erreicht wurden. Dann verlässt sie die Anwendung als Antwort an den Client.](index/_static/request-delegate-pipeline.png)

Jeder Delegat kann Vorgänge vor und nach dem nächsten Delegaten ausführen. Die Ausnahmebehandlungsdelegaten müssen am Anfang der Pipeline aufgerufen werden, sodass sie Ausnahmen abfangen können, die zu einem späteren Zeitpunkt in der Pipeline ausgelöst werden.

Die einfachste mögliche ASP.NET Core-App enthält einen einzigen Anforderungsdelegaten, der alle Anforderungen verarbeitet. In diesem Fall ist keine tatsächliche Anforderungspipeline vorhanden. Stattdessen wird eine einzelne anonyme Funktion als Antwort auf jede HTTP-Anforderung aufgerufen.

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

Der erste <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A>-Delegat beendet die Pipeline.

Mit <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A> können Sie mehrere Anforderungedelegate miteinander verknüpfen. Der Parameter `next` steht für den nächsten Delegaten in der Pipeline. Sie können die Pipeline kurzschließen, indem Sie den Parameter *next* *nicht* aufrufen. Normalerweise können Sie Aktionen sowohl vor als auch nach dem nächsten Delegaten durchführen. Dies wird in folgendem Beispiel veranschaulicht:

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

Wenn ein keine Anforderung an den nächsten Delegaten übergibt, wird dies als *Kurzschluss der Anforderungspipeline* bezeichnet. Das Kurzschließen ist oft sinnvoll, da es unnötige Arbeit verhindert. Die [Middleware für statische Dateien](xref:fundamentals/static-files) kann beispielsweise als *Terminalmiddleware* fungieren, indem sie eine Anforderung für eine statische Datei zurückgibt und den Rest der Pipeline kurzschließt. Middleware, die noch vor der Middleware, die die weitere Verarbeitung beendet, zur Pipeline hinzugefügt wird, verarbeitet Code noch nach den `next.Invoke`-Anweisungen weiter. Sehen Sie sich allerdings die folgende Warnung zum Versuch an, in eine Antwort zu schreiben, die bereits gesendet wurde.

> [!WARNING]
> Rufen Sie `next.Invoke` nicht auf, nachdem die Antwort an den Client gesendet wurde. An <xref:Microsoft.AspNetCore.Http.HttpResponse> vorgenommene Änderungen lösen nach dem Start der Antwort eine Ausnahme aus. Änderungen wie das Festlegen von Headern und einem Statuscode lösen beispielsweise eine Ausnahme aus. Wenn Sie nach dem Aufruf von `next` in den Antworttext schreiben, kann dies:
>
> * einen Protokollverstoß verursachen, wenn Sie z.B. mehr als das genannte `Content-Length`-Objekt schreiben.
> * Fehler im Textformat auslösen, wenn Sie z.B. eine HTML-Fußzeile in eine CSS-Datei schreiben.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted%2A> ist ein nützlicher Hinweis, der angibt, ob Header gesendet wurden oder ob in den Text geschrieben wurde.

<a name="order"></a>

## <a name="middleware-order"></a>Middlewarereihenfolge

Die Reihenfolge, in der Middlewarekomponenten in der `Startup.Configure`-Methode hinzugefügt werden, legt die Reihenfolge fest, in der die Middlewarekomponenten bei Anforderungen aufgerufen werden. Bei Antworten gilt die umgekehrte Reihenfolge. Die Reihenfolge ist in Bezug auf Sicherheit, Leistung und Funktionalität **entscheidend**.

Die folgende `Startup.Configure`-Methode fügt sicherheitsbezogene Middlewarekomponenten in der empfohlenen Reihenfolge hinzu:

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

Für den Code oben gilt:

* Middleware, die beim Erstellen einer neuen Web-App mit [einzelnen Benutzerkonten](xref:security/authentication/identity) nicht hinzugefügt wird, wird auskommentiert.
* Nicht jede Middleware muss in genau dieser Reihenfolge vorliegen, aber für viele Middlewarekomponenten ist dies erforderlich. Beispielsweise müssen `UseCors` und `UseAuthentication` in der gezeigten Reihenfolge vorliegen.

Die folgenden `Startup.Configure`-Methode fügt Middlewarekomponenten für allgemeine App-Szenarien hinzu:

1. Ausnahme-/Fehlerbehandlung
   * Bei Ausführung der App in der Entwicklungsumgebung:
     * Middleware der Seite mit Ausnahmen für Entwickler (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>) meldet App-Laufzeitfehler.
     * Middleware der Seite mit Datenbankfehlern (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) meldet Datenbanklaufzeitfehler.
   * Bei Ausführung der App in der Produktionsumgebung:
     * Middleware für Ausnahmehandler (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>) fängt Ausnahmen ab, die in den folgenden Middlewarekomponenten ausgelöst werden.
     * HTTP Strict Transport Security Protocol-Middleware (HSTS) (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts%2A>) fügt den `Strict-Transport-Security`-Header hinzu.
1. Middleware zur HTTPS-Umleitung (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection%2A>) leitet HTTP-Anforderungen an HTTPS um.
1. Middleware für statische Dateien (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>) gibt statische Dateien zurück und umgeht die weitere Anforderungsverarbeitung.
1. Middleware für Cookierichtlinien (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy%2A>) sorgt dafür, dass die App die Anforderungen der europäischen Datenschutzgrundverordnung (DSGVO) erfüllt.
1. Middleware für die Authentifizierung (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) versucht, den Benutzer zu authentifizieren, bevor der Zugriff auf sichere Ressourcen zugelassen wird.
1. Middleware für Sitzungen (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A>) richtet einen Sitzungsstatus ein und erhält diesen aufrecht. Wenn die App den Sitzungsstatus verwendet, rufen Sie die Middleware für Sitzungen nach der Middleware für Cookierichtlinien und vor der MVC-Middleware auf.
1. MVC-Middleware (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>) zum Hinzufügen von MVC zur Anforderungspipeline.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

Im vorhergehenden Beispielcode wird jede Middleware-Erweiterungsmethode in <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> über den <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>-Namespace verfügbar gemacht.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ist die erste Middlewarekomponente, die der Pipeline hinzugefügt wird. Aus diesem Grund fängt die Middleware für den Ausnahmehandler alle Ausnahmen ab, die in späteren Aufrufen auftreten.

Die Middleware für statische Dateien wird am Anfang der Pipeline aufgerufen, damit sie Anforderungen und Kurzschlüsse verarbeiten kann, ohne dass die verbleibenden Komponenten durchlaufen werden müssen. Die Middleware für statische Dateien stellt **keine** Autorisierungsüberprüfungen bereit. Alle Dateien, die von Middleware für statische Dateien unterstützt werden, einschließlich der Dateien unter *wwwroot*, sind öffentlich verfügbar. Informationen zum Sichern statischer Dateien finden Sie unter <xref:fundamentals/static-files>.

Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Authentifizierungsmiddleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>) übergeben, welche die Authentifizierung durchführt. Die Authentifizierung schließt nicht authentifizierte Anforderungen nicht kurz. Auch wenn die Authentifizierungsmiddleware Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC eine spezifische Razor Page oder einen MVC-Controller und eine Aktion ausgewählt hat.

Im folgenden Beispiel wird eine Middlewarereihenfolge veranschaulicht, bei der Anforderungen statischer Dateien von der Middleware für statische Dateien vor der Middleware für die Antwortkomprimierung verarbeitet werden. Die statischen Dateien werden bei dieser Middlewarereihenfolge nicht komprimiert. Die MVC-Antworten von <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> können komprimiert werden.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a>Use, Run und Map

Konfigurieren Sie die HTTP-Pipeline mit <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use%2A>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run%2A> und <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>. Die `Use`-Methode kann die Pipeline kurzschließen (wenn sie keinen `next`-Anforderungsdelegaten aufruft). `Run` ist eine Konvention. Einige Middlewarekomponenten machen möglicherweise `Run[Middleware]`-Methoden verfügbar, die am Ende einer Pipeline ausgeführt werden.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map%2A>-Erweiterungen werden als Konvention zum Branchen der Pipeline verwendet. `Map` brancht die Anforderungspipeline auf Grundlage von Übereinstimmungen des angegebenen Anforderungspfads. Wenn der Anforderungspfad mit dem angegebenen Pfad beginnt, wird der Branch ausgeführt.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.

| Anforderung             | Antwort                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Bei Verwendung von `Map` werden die übereinstimmenden Pfadsegmente aus `HttpRequest.Path` entfernt und für jede Anforderung an `HttpRequest.PathBase` angehängt.

<xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen%2A> brancht die Anforderungspipeline auf Grundlage des Ergebnisses des angegebenen Prädikats. Jedes Prädikat vom Typ `Func<HttpContext, bool>` kann verwendet werden, um Anforderungen einem neuen Branch der Pipeline zuzuordnen. Im folgenden Beispiel wird ein Prädikat verwendet, um das Vorhandensein der Abfragezeichenfolgenvariablen `branch` zu ermitteln:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.

| Anforderung                       | Antwort                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

`Map` unterstützt das Schachteln, wie z.B. in folgendem Code:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

`Map` kann auch mehrere Segmente auf einmal zuordnen:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a>Integrierte Middleware

Die folgenden Middlewarekomponenten sind im Lieferumfang von ASP.NET Core enthalten. Die Spalte *Reihenfolge* enthält Hinweise zur Platzierung der Middleware in der Pipeline, die die Anforderung verarbeitet, und zu den Bedingungen, unter denen die Middleware die Anforderungsverarbeitung möglicherweise beendet. Wenn eine Middleware einen Kurzschluss in der Anforderungsverarbeitungspipeline verursacht und verhindert, dass Downstreammiddleware eine Anforderung verarbeitet, wird diese als *Terminalmiddleware* bezeichnet. Weitere Informationen zu Kurzschlüssen finden Sie im Abschnitt [Erstellen einer Middlewarepipeline mit IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Beschreibung | Auftrag |
| ---------- | ----------- | ----- |
| [Authentifizierung](xref:security/authentication/identity) | Bietet Unterstützung für Authentifizierungen. | Bevor `HttpContext.User` erforderlich ist. Terminal für OAuth-Rückrufe. |
| [Cookierichtlinie](xref:security/gdpr) | Verfolgt die Zustimmung von Benutzern zum Speichern persönlicher Informationen und erzwingt die Mindeststandards für cookiefelder, z. B. `secure` und `SameSite`. | Befindet sich vor der Middleware, die cookies ausstellt. Beispiele: Authentifizierung, Sitzung, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguriert die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS). | Vor Komponenten, die CORS verwenden. |
| [Diagnose](xref:fundamentals/error-handling) | Mehrere separate Middlewares, die Entwicklern eine Ausnahmeseite, Ausnahmebehandlung, Statuscodeseiten und die Standardwebseite für neue Apps bereitstellen. | Vor Komponenten, die Fehler erzeugen. Terminal für Ausnahmen oder zum Bereitstellen der Standardwebseite für neue Apps. |
| [Weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) | Leitet Proxyheader an die aktuelle Anforderung weiter. | Vor Komponenten, die die aktualisierten Felder nutzen. Beispiele: Schema, Host, Client-IP, Methode. |
| [Integritätsprüfung](xref:host-and-deploy/health-checks) | Überprüft die Integrität der ASP.NET Core-App und ihrer Abhängigkeiten, z. B. Überprüfung der Datenbankverfügbarkeit. | Abschließend, wenn eine Anforderung mit einem Integritätsprüfungs-Endpunkt übereinstimmt. |
| [Außerkraftsetzung der HTTP-Methode](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | Ermöglicht es eingehenden POST-Anforderungen, die Methode außer Kraft zu setzen. | Vor Komponenten, die die aktualisierte Methode nutzen. |
| [HTTPS-Umleitung](xref:security/enforcing-ssl#require-https) | Leitet alle HTTP-Anforderungen an HTTPS um. | Vor Komponenten, die die URL nutzen. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Middleware für erweiterte Sicherheit, die einen besonderen Antwortheader hinzufügt. | Bevor Antworten gesendet werden und nach Komponenten, die Anforderungen ändern. Beispiele: weitergeleitete Header, URL-Umschreibung. |
| [MVC](xref:mvc/overview) | Verarbeitet Anforderungen mit MVC/Razor Pages. | Abschließend, wenn eine Anforderung mit einer Route übereinstimmt. |
| [OWIN](xref:fundamentals/owin) | Interoperabilität mit auf OWIN basierten Apps, Servern und Middleware. | Abschließend, wenn die OWIN-Middleware die Anforderung vollständig verarbeitet. |
| [Zwischenspeichern von Antworten](xref:performance/caching/middleware) | Bietet Unterstützung für das Zwischenspeichern von Antworten. | Vor Komponenten, für die das Zwischenspeichern erforderlich ist. |
| [Antwortkomprimierung](xref:performance/response-compression) | Bietet Unterstützung für das Komprimieren von Antworten. | Vor Komponenten, für die das Komprimieren erforderlich ist. |
| [Lokalisierung von Anforderungen](xref:fundamentals/localization) | Bietet Unterstützung für die Lokalisierung. | Vor der Lokalisierung vertraulicher Komponenten. |
| [Endpunktrouting](xref:fundamentals/routing) | Definiert Anforderungsrouten und schränkt diese ein. | Terminal für entsprechende Routen. |
| [Sitzung](xref:fundamentals/app-state) | Bietet Unterstützung für das Verwalten von Benutzersitzungen. | Vor Komponenten, für die Sitzungen erforderlich sind. |
| [Statische Dateien](xref:fundamentals/static-files) | Bietet Unterstützung für das Verarbeiten statischer Dateien und das Durchsuchen des Verzeichnisses. | Abschließend, wenn eine Anforderung mit einer Datei übereinstimmt. |
| [Umschreiben einer URL](xref:fundamentals/url-rewriting) | Bietet Unterstützung für das Umschreiben von URLs und das Umleiten von Anforderungen. | Vor Komponenten, die die URL nutzen. |
| [WebSockets](xref:fundamentals/websockets) | Aktiviert das WebSockets-Protokoll. | Vor Komponenten, die WebSocket-Anforderungen annehmen müssen. |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/middleware/write>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
