---
title: Sitzung in ASP.NET Core
author: rick-anderson
description: Entdecken Sie Ansätze zum Erhalten der Sitzung zwischen Anforderungen.
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2020
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
uid: fundamentals/app-state
ms.openlocfilehash: c11b748f9d79235b14c9541019da6e1fb3428af6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051407"
---
# <a name="session-and-state-management-in-aspnet-core"></a>Sitzungs- und Zustandsverwaltung in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Kirk Larkin](https://twitter.com/serpent5) und [Diana LaRose](https://github.com/DianaLaRose)

Bei HTTP handelt es sich um ein zustandsloses Protokoll. HTTP-Anforderungen sind standardmäßig unabhängige Nachrichten, die keine Benutzerwerte beibehalten. In diesem Artikel werden mehrere Ansätze zum Beibehalten von Benutzerdaten zwischen Anforderungen beschrieben.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zustandsverwaltung

Zustände können mithilfe mehrerer Ansätze gespeichert werden. Die Ansätze werden im Verlauf dieses Artikels beschrieben.

| Speicheransatz | Speichermechanismus |
| ---------------- | ----------------- |
| [Cookies](#cookies) | HTTP-cookies. Schließt möglicherweise Daten ein, die mit serverseitigem App-Code gespeichert wurden. |
| [Sitzungszustand](#session-state) | HTTP-cookies und serverseitiger App-Code |
| [TempData](#tempdata) | HTTP-cookies oder Sitzungszustand |
| [Abfragezeichenfolgen](#query-strings) | HTTP-Abfragezeichenfolgen |
| [Verborgene Felder](#hidden-fields) | HTTP-Formularfelder |
| [HttpContext.Items](#httpcontextitems) | Serverseitiger App-Code |
| [Cache](#cache) | Serverseitiger App-Code |

## <a name="no-loccookies"></a>Cookie

Cookies speichern Daten anforderungsübergreifend. Da mit jeder Anforderung cookies gesendet werden, sollte deren Größe auf ein Minimum begrenzt sein. Idealerweise sollte nur ein Bezeichner in einem cookie gespeichert werden, während die Daten von der App gespeichert werden sollten. Die meisten Browser beschränken die Größe von cookies auf 4096 Byte. Nur eine begrenzte Anzahl von cookies ist für jede Domäne verfügbar.

Da cookies manipuliert werden können, müssen sie von der App überprüft werden. Cookies können von Benutzern gelöscht werden und können auf Clients ablaufen. cookies sind für gewöhnlich die dauerhafteste Form der Datenpersistenz auf dem Client.

Cookies werden häufig aus Personalisierungsgründen verwendet, wenn Inhalt für einen bekannten Benutzer angepasst wird. In den meisten Fällen wird der Benutzer nur identifiziert und nicht authentifiziert. Das cookie kann den Benutzernamen, Kontonamen oder eine eindeutige Benutzer-ID wie die GUID speichern. Dann können Sie mit dem cookie auf die persönlichen Einstellungen (z. B. die bevorzugte Farbe des Websitehintergrunds) des Benutzers zugreifen.

Beachten Sie die [Europäische Datenschutz-Grundverordnung (DSGVO)](https://ec.europa.eu/info/law/law-topic/data-protection) beim Ausstellen von cookies und beim Umgang mit Aspekten des Datenschutzes. Weitere In finden Sie unter [General Data Protection Regulation (GDPR) support in ASP.NET Core (DSGVO-Unterstützung in ASP.NET Core)](xref:security/gdpr).

## <a name="session-state"></a>Sitzungszustand

Der Sitzungszustand ist ein Szenario in ASP.NET Core zum Speichern von Benutzerdaten, wenn der Benutzer eine Web-App verwendet. Der Sitzungszustand verwendet einen von der App verwalteten Speicher, um Daten für mehrere Anforderungen eines Clients beizubehalten. Die Sitzungsdaten werden durch einen Cache gesichert und als kurzlebige Daten betrachtet. Die Website sollte auch ohne die Sitzungsdaten weiterhin funktionieren. Kritische Anwendungsdaten sollten in der Benutzerdatenbank gespeichert und nur zur Leistungsoptimierung in der Sitzung zwischengespeichert werden.

Sitzungen werden in [SignalRSignalR-Apps nicht unterstützt, da ein ](xref:signalr/index)[-Hub](xref:signalr/hubs) unabhängig vom HTTP-Kontext ausgeführt werden kann. Das kann z.B. passieren, wenn eine lange Abrufanforderung von einem Hub länger als die Lebensdauer des HTTP-Kontexts einer Anforderung offen gehalten wird.

ASP.NET Core verwaltet den Sitzungszustand, indem ein cookie an den Client übergeben wird, das die Sitzungs-ID enthält. Die Sitzungscookie-ID:

* Sie wird mit jeder Anforderung an die App gesendet.
* Sie wird von der App zum Abrufen der Sitzungsdaten verwendet.

Der Sitzungszustand verhält sich wie folgt:

* Das Sitzungscookie ist für den Browser spezifisch. Die Sitzungen werden nicht browserübergreifend geteilt.
* Sitzungscookies werden gelöscht, wenn die Browsersitzung abläuft.
* Wenn für eine abgelaufene Sitzung ein cookie empfangen wird, wird eine neue Sitzung erstellt, die dasselbe cookie verwendet.
* Leere Sitzungen werden nicht beibehalten. Für die Sitzung muss mindestens ein Wert festgelegt sein, damit die Sitzung anforderungsübergreifend beibehalten wird. Wenn eine Sitzung nicht beibehalten wird, wird eine neue Sitzungs-ID für jede neue Anforderung erzeugt.
* Die App speichert Sitzungen für einen beschränkten Zeitraum nach der letzten Anforderung. Die App legt entweder ein Zeitlimit für die Sitzungen fest oder verwendet den Standardwert von 20 Minuten. Der Sitzungszustand ist ideal zum Speichern von Benutzerdaten:
  * Das ist spezifisch für eine bestimmte Sitzung.
  * Wo die Daten keine permanente und sitzungsübergreifende Speicherung erfordern.
* Sitzungsdaten werden gelöscht, wenn die Implementierung von <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> aufgerufen wird oder wenn die Sitzung abläuft.
* Es gibt kein Standardverfahren, wie App-Code darüber informiert wird, dass ein Clientbrowser geschlossen wurde oder dass ein Sitzungscookie gelöscht wurde oder auf dem Client abgelaufen ist.
* Sitzungszustandscookies sind standardmäßig nicht als grundlegend gekennzeichnet. Der Sitzungszustand ist nicht praktisch, es sei denn, der Besucher der Website gestattet die Nachverfolgung. Weitere Informationen finden Sie unter <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Speichern Sie keine vertraulichen Daten im Sitzungszustand. Es besteht die Möglichkeit, dass der Benutzer seinen Browser nicht schließt oder die Sitzungscookies nicht löscht. Einige Browser behalten gültige Sitzungscookies browserfensterübergreifend bei. Eine Sitzung ist möglicherweise nicht auf einen einzelnen Benutzer beschränkt. Der nächste Benutzer kann die App mit demselben Sitzungscookie weiter durchsuchen.

Der Cacheanbieter im Arbeitsspeicher speichert Sitzungsdaten im Arbeitsspeicher des Servers, auf dem sich die App befindet. Beachten Sie in einem Szenario mit einer Serverfarm Folgendes:

* Verwenden Sie *Sticky Sessions* (anhaftende Sitzungen), um jede Sitzung an eine spezifische App-Instanz auf einem separaten Server zu binden. [Azure App Service](https://azure.microsoft.com/services/app-service/) verwendet [Routing von Anwendungsanforderungen (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module), um Sticky Sessions standardmäßig zu erzwingen. Diese Sitzungen können allerdings Auswirkungen auf die Skalierbarkeit haben und Updates für Web-Apps erschweren. Ein geeigneteres Vorgehen ist das Verwenden von Redis oder über SQL Server verteilte Caches, für die keine Sticky Sessions notwendig sind. Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.
* Das Sitzungscookie wird über <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> verschlüsselt. Der Datenschutz muss ordnungsgemäß konfiguriert sein, sodass er Sitzungscookies auf jedem Computer liest. Weitere Informationen finden Sie unter <xref:security/data-protection/introduction> und [Schlüsselspeicheranbieter](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurieren des Sitzungszustands

Das [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/)-Paket:

* Wird durch das Framework implizit einbezogen.
* Stellt Middleware zum Verwalten eines Sitzungszustands zur Verfügung.

`Startup` muss folgende Elemente enthalten, um die Sitzungsmiddleware zu aktivieren:

* Einen beliebigen der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Arbeitsspeichercaches. Die `IDistributedCache`-Implementierung wird als Sicherungsspeicher für Sitzungen verwendet. Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.
* Ein Aufruf von <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> in `ConfigureServices`.
* Ein Aufruf von <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> in `Configure`.

Der folgende Code zeigt, wie Sie den speicherinternen Sitzungsanbieter mit einer Standardimplementierung von `IDistributedCache` im Arbeitsspeicher einrichten:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup4.cs?name=snippet1&highlight=12-19,45)]

Der vorangegangene Code setzt ein kurzes Zeitlimit, um das Testen zu vereinfachen.

Die Reihenfolge der Middleware ist wichtig.  Rufen Sie `UseSession` nach `UseRouting` und vor `UseEndpoints` auf. Informationen finden Sie unter [Festlegen einer Reihenfolge für Middleware](xref:fundamentals/middleware/index#order).

[HttpContext.Session](xref:Microsoft.AspNetCore.Http.HttpContext.Session) ist verfügbar, nachdem der Sitzungszustand konfiguriert wurde.

Auf `HttpContext.Session` kann vor einem Aufruf von `UseSession` nicht zugegriffen werden.

Nachdem die App mit dem Schreiben in den Antwortdatenstrom begonnen hat, kann keine neue Sitzung mit einem neuen Sitzungscookie erstellt werden. Die Ausnahme wird im Webserverprotokoll erfasst und nicht im Browser angezeigt.

### <a name="load-session-state-asynchronously"></a>Asynchrones Laden des Sitzungszustands

Der Standardsitzungsanbieter in ASP.NET Core lädt Sitzungsaufzeichnungen aus dem zugrunde liegenden <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Speicher nur dann asynchron, wenn die <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType>-Methode explizit vor den Methoden <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> oder <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> aufgerufen wird. Wenn `LoadAsync` nicht zuerst aufgerufen wird, werden die zugrunde liegenden Sitzungsaufzeichnungen synchron geladen, was entsprechende Auswirkungen auf die Leistung haben kann.

Damit Apps dieses Muster erzwingen, umschließen Sie die Implementierungen <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> und <xref:Microsoft.AspNetCore.Session.DistributedSession> mit Versionen, die eine Ausnahme auslösen, wenn die `LoadAsync`-Methode nicht vor `TryGetValue`, `Set` oder `Remove` aufgerufen wird. Registrieren Sie die umschlossenen Versionen in den Dienstcontainern.

### <a name="session-options"></a>Sitzungsoptionen

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.SessionOptions>, um Standardwerte für Sitzungen zu überschreiben.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Bestimmt die Einstellungen, die zum Erstellen des cookies verwendet wurden. Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> ist <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> ist <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> ist <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> ist `true`. Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` gibt an, wie lang die Sitzung sich im Leerlauf befinden darf, bevor die Inhalte verworfen werden. Jeder Zugriff auf eine Sitzung setzt das Zeitlimit zurück. Diese Einstellung gilt nur für den Inhalt der Sitzung und nicht für das cookie. Der Standardwert beträgt 20 Minuten. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Der maximale Zeitraum, in dem eine Sitzung aus dem Speicher geladen oder mithilfe eines Commits erneut in diesem hinterlegt werden kann. Diese Einstellung gilt möglicherweise nur für asynchrone Vorgänge. Dieses Zeitlimit kann mit <xref:System.Threading.Timeout.InfiniteTimeSpan> deaktiviert werden. Der Standardwert beträgt 1&#160;Minute. |

Die Sitzung verwendet ein cookie, um Anforderungen eines Browsers nachzuverfolgen und zu identifizieren. Standardmäßig wird das cookie `.AspNetCore.Session` genannt und verwendet den Pfad von `/`. Da die Standardeinstellungen des cookies keine Domäne festlegen, wird es dem clientseitigen Skript auf der Seite nicht zur Verfügung gestellt (da <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> standardmäßig auf `true` festgelegt ist).

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.SessionOptions>, um Standardwerte für cookiesitzungen zu überschreiben.

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup2.cs?name=snippet1&highlight=5-10)]

Die App verwendet die <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout>-Eigenschaft, um zu bestimmen, wie lange sich eine Sitzung im Leerlauf befinden darf, bevor ihre Inhalte im Cache des Servers verworfen werden. Diese Eigenschaft ist unabhängig vom Ablauf der cookies. Jede Anforderung, die über die [Sitzungsmiddleware](xref:Microsoft.AspNetCore.Session.SessionMiddleware) übergeben wird, setzt das Zeitlimit zurück.

Der Sitzungszustand ist *nicht sperrend*. Die letzte Anforderung überschreibt die erste, wenn zwei Anforderungen gleichzeitig versuchen, die Inhalte einer Sitzung zu bearbeiten. `Session` wird als *kohärente Sitzung* implementiert, d.h., dass alle Inhalte zusammen gespeichert werden. Wenn zwei Anforderungen unterschiedliche Sitzungswerte bearbeiten möchten, überschreibt die letzte Anforderung möglicherweise Anforderungen der ersten.

### <a name="set-and-get-session-values"></a>Festlegen und Abrufen von Sitzungswerten

Auf den Sitzungszustand wird von der Razor Pages-Klasse <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> oder der MVC-Klasse <xref:Microsoft.AspNetCore.Mvc.Controller> mit <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> zugegriffen. Diese Eigenschaft ist eine Implementierung von <xref:Microsoft.AspNetCore.Http.ISession>.

Die `ISession`-Implementierung bietet mehrere Erweiterungsmethoden zum Festlegen und Abrufen von Integerwerten und Zeichenfolgenwerten. Die Erweiterungsmethoden befinden sich im Namespace <xref:Microsoft.AspNetCore.Http>.

`ISession`-Erweiterungsmethoden:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Mit dem folgenden Codebeispiel wird der Sitzungswert für den`IndexModel.SessionKeyName`-Schlüssel (`_Name` in der Beispiel-App) auf einer Razor Pages-Seite abgerufen:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Im folgenden Codebeispiel wird veranschaulicht, wie Sie eine ganze Zahl und eine Zeichenfolge abrufen und festlegen können:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Alle Sitzungsdaten müssen serialisiert werden, um ein Szenario mit einem verteilten Cache zu ermöglichen, auch wenn Sie den speicherinternen Cache verwenden. Serialisierer für Zeichenfolgen und ganze Zahlen werden durch die Erweiterungsmethoden von <xref:Microsoft.AspNetCore.Http.ISession> bereitgestellt. Komplexe Typen müssen vom Benutzer mit einem anderen Verfahren wie etwa JSON serialisiert werden.

Verwenden Sie den folgenden Beispielcode, um Objekte zu serialisieren:

[!code-csharp[](app-state/samples/3.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Im folgenden Beispiel wird dargestellt, wie ein serialisierbares Objekt mit der Klasse `SessionExtensions` festgelegt und abgerufen werden kann:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core macht die Razor Pages-[TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) oder Controller-<xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar. Diese Eigenschaft speichert Daten, bis sie in einer anderen Anforderung gelesen werden. Mit den Methoden [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) und [Peek(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) können die Daten untersucht werden, ohne am Ende der Anforderung gelöscht zu werden. [Keep](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) markiert alle Elemente im Wörterbuch für die Aufbewahrung. `TempData` ist:

* Nützlich für die Umleitung, wenn Daten für mehr als eine einzelne Anforderung erforderlich sind.
* Wird von `TempData`-Anbietern durch Verwendung von cookies oder des Sitzungszustands implementiert.

## <a name="tempdata-samples"></a>TempData-Beispiele

Beachten Sie die folgende Seite, die einen Kunden erstellt:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Die folgende Seite zeigt `TempData["Message"]` an:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Im vorangehenden Markup wird `TempData["Message"]` am Ende der Anforderung **nicht** gelöscht, da `Peek` verwendet wird. Beim Aktualisieren der Seite wird der Inhalt von `TempData["Message"]` angezeigt.

Das folgende Markup ähnelt dem vorangehenden Code, verwendet jedoch `Keep`, um die Daten am Ende der Anforderung beizubehalten:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Die Navigation zwischen den Seiten *IndexPeek* und *IndexKeep* löscht `TempData["Message"]` nicht.

Der folgende Code zeigt `TempData["Message"]` an, aber am Ende der Anforderung wird `TempData["Message"]` gelöscht:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData-Anbieter

Der cookiebasierte TempData-Anbieter wird standardmäßig verwendet, um TempData in cookies zu speichern.

Die cookiedaten werden mit <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> verschlüsselt, mit <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> codiert und dann segmentiert. Die maximale cookiegröße beträgt aufgrund von Verschlüsselung und Segmentierung weniger als [4096 Bytes](http://www.faqs.org/rfcs/rfc2965.html). Die cookiedaten werden nicht komprimiert, da es zu Sicherheitsproblemen kommen kann, wenn Daten verschlüsselt werden, z. B. durch [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit))- und [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit))-Angriffe. Weitere Informationen zum cookiebasierten TempData-Anbieter finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Auswählen eines TempData-Anbieters

Bevor Sie einen TempData-Anbieter auswählen, müssen Sie folgende Überlegungen anstellen:

* Verwendet die App bereits den Sitzungszustand? Falls dies der Fall ist, hat die Verwendung des TempData-Anbieters für den Sitzungszustand abgesehen von der Größe der Daten keine zusätzlichen Auswirkungen auf die App.
* Verwendet die App TempData nur selten für verhältnismäßig kleine Datenmengen bis zu 500 Bytes? Falls dies der Fall ist, entsteht durch das cookie des TempData-Anbieters nur ein kleiner zusätzlicher Aufwand für jede Anforderung, die TempData enthält. Falls dies nicht der Fall ist, kann der Sitzungszustand des TempData-Anbieters nützlich sein, um Roundtrips für große Datenmengen für jede Anforderung durchzuführen, bis TempData verarbeitet wird.
* Wird die App in einer Serverfarm auf mehreren Servern ausgeführt? Wenn dies der Fall ist, ist keine weitere Konfiguration erforderlich, um das cookie des TempData-Anbieters außerhalb des Schutzes von Daten zu verwenden (weitere Informationen finden Sie unter <xref:security/data-protection/introduction> und [Schlüsselspeicheranbieter](xref:security/data-protection/implementation/key-storage-providers)).

Die meisten Webclients (z. B. Webbrowser) erzwingen Einschränkungen für die maximale Größe der cookies und für die Gesamtzahl der cookies. Wenn Sie das cookie des TempData-Anbieters verwenden, sollten Sie überprüfen, ob die App [diese Einschränkungen](http://www.faqs.org/rfcs/rfc2965.html) überschreitet. Beachten Sie die Gesamtgröße der Daten. Rechnen Sie mit erhöhter cookiegröße aufgrund von Verschlüsselung und Segmentierung.

### <a name="configure-the-tempdata-provider"></a>Konfigurieren des TempData-Anbieters

Der cookiebasierte TempData-Anbieter ist standardmäßig aktiviert.

Verwenden Sie die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>, um den sitzungsbasierten TempData-Anbieter zu aktivieren. Es ist nur ein Aufruf von `AddSessionStateTempDataProvider` erforderlich:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup3.cs?name=snippet1&highlight=4,6,8,30)]

## <a name="query-strings"></a>Abfragezeichenfolgen

Eine begrenzte Menge an Daten kann von einer Anforderung an eine andere übergeben werden, indem Sie diese zu der Abfragezeichenfolge einer neuen Anforderung hinzufügen. Dies ist nützlich, um den Zustand dauerhaft zu erfassen und Links mit einem eingebetteten Zustand zuzulassen, die über E-Mail oder soziale Netzwerke geteilt werden sollen. Verwenden Sie keine Abfragezeichenfolgen für vertrauliche Daten, da URL-Abfragezeichenfolgen öffentlich sind.

Zusätzlich zur unbeabsichtigten Freigabe können auch Daten in Abfragezeichenfolgen die App entsprechenden [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF))-Angriffen (websiteübergreifende Anforderungsfälschung) aussetzen. Jeder gespeicherte Sitzungszustand muss vor solchen Anforderungsfälschungen geschützt sein. Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Verborgene Felder

Daten können in ausgeblendeten Formularfeldern gespeichert und an die nächste Anforderung zurückgesendet werden. Dies ist häufig in mehrseitigen Formularen der Fall. Die App muss die in verborgenen Feldern gespeicherten Daten immer wieder überprüfen, da der Client die Daten manipulieren könnte.

## <a name="httpcontextitems"></a>HttpContext.Items

Die Auflistung <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> wird verwendet, um Daten zu speichern, während eine einzelne Anforderung verarbeitet wird. Die Inhalte der Auflistung werden nach der Verarbeitung jeder Anforderung verworfen. Die `Items`-Auflistung wird häufig von Komponenten oder Middleware zur Kommunikation verwendet, wenn sie zu unterschiedlichen Zeitpunkten während einer Anforderung ausgeführt werden und es keinen direkten Weg gibt, Parameter zu übergeben.

Im folgenden Beispiel fügt [Middleware](xref:fundamentals/middleware/index) `isVerified` zur Auflistung `Items` hinzu:

[!code-csharp[](app-state/samples/3.x/SessionSample/Startup.cs?name=snippet1)]

Für Middleware, die nur in einer einzelnen App verwendet wird, werden `string`-Schlüssel akzeptiert. Middleware, die von mehreren Apps verwendet wird, sollte eindeutige Objektschlüssel verwenden, um Schlüsselkonflikte zu vermeiden. Das folgenden Beispiel zeigt, wie Sie einen eindeutigen Objektschlüssel verwenden, der in einer Middlewareklasse definiert wurde:

[!code-csharp[](app-state/samples/3.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Anderer Code kann auf den Wert zugreifen, der in `HttpContext.Items` gespeichert ist, indem er den Schlüssel verwendet, der von der Middlewareklasse zur Verfügung gestellt wird:

[!code-csharp[](app-state/samples/3.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Dieser Ansatz hat außerdem den Vorteil, dass das Verwenden von Schlüsselzeichenfolgen im Code vermieden wird.

## <a name="cache"></a>cache

Das Caching stellt eine effiziente Möglichkeit zum Speichern und Abrufen von Daten dar. Die App kann die Lebensdauer zwischengespeicherter Elemente bestimmen. Weitere Informationen finden Sie unter <xref:performance/caching/response>.

Zwischengespeicherte Daten sind nicht mit einer spezifischen Anforderung, einem spezifischen Benutzer oder einer spezifischen Sitzung verknüpft. **Speichern Sie keine benutzerspezifischen Daten zwischen, die von anderen Benutzeranforderungen abgerufen werden können.**

Informationen zum Zwischenspeichern anwendungsweiter Daten finden Sie unter <xref:performance/caching/memory>.

## <a name="common-errors"></a>Häufige Fehler

* „Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'. (Dienst kann nicht für den Typ „Microsoft.Extensions.Caching.Distributed.IDistributedCache“ aufgelöst werden, während versucht wird, auf „Microsoft.AspNetCore.Session.DistributedSessionStore“ zuzugreifen.)

  Dieser Fehler entsteht in der Regel, wenn nicht alle `IDistributedCache`-Implementierungen konfiguriert werden. Weitere Informationen finden Sie unter <xref:performance/caching/distributed> und <xref:performance/caching/memory>.

Wenn die Middleware für Sitzungen eine Sitzung nicht aufrechterhalten kann:

* Die Middleware protokolliert die Ausnahme, und die Anforderung wird normal fortgesetzt.
* Das führt zu unvorhersehbarem Verhalten.

Die Middleware für Sitzungen kann eine Sitzung nicht aufrechterhalten, wenn der Sicherungsspeicher nicht verfügbar ist. Nehmen wir an, dass ein Benutzer seinen Einkaufswagen in einer Sitzung speichert. Der Benutzer fügt ein Element zum Einkaufswagen hinzu, aber der Commit schlägt fehl. Die App wird nicht über den Fehler informiert und meldet dem Benutzer, dass das Element zum Einkaufswagen hinzugefügt wurde. Dies stimmt jedoch nicht.

Es wird empfohlen, nach Fehlern zu suchen, indem Sie `await feature.Session.CommitAsync` aufrufen, wenn die App mit dem Schreiben in die Sitzung fertig ist. <xref:Microsoft.AspNetCore.Http.ISession.CommitAsync*> löst eine Ausnahme aus, wenn der Sicherungsspeicher nicht verfügbar ist. Wenn `CommitAsync` fehlschlägt, kann die App die Ausnahme verarbeiten. <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync*> wird unter den gleichen Bedingungen ausgelöst, wenn der Sicherungsspeicher nicht verfügbar ist.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR und Sitzungszustand

SignalR-Apps dürfen nicht den Sitzungszustand verwenden, um Informationen zu speichern. SignalR-Apps können Informationen je nach Verbindungszustand in `Context.Items` im Hub speichern. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:host-and-deploy/web-farm>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), [Diana LaRose](https://github.com/DianaLaRose) und [Luke Latham](https://github.com/guardrex)

Bei HTTP handelt es sich um ein zustandsloses Protokoll. HTTP-Anforderungen sind ohne zusätzliche Schritte unabhängige Nachrichten, die keine Benutzerwerte oder App-Zustände beibehalten. In diesem Artikel werden mehrere Ansätze zum Beibehalten von Benutzerdaten und App-Zuständen zwischen Anforderungen beschrieben.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/app-state/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="state-management"></a>Zustandsverwaltung

Zustände können mithilfe mehrerer Ansätze gespeichert werden. Die Ansätze werden im Verlauf dieses Artikels beschrieben.

| Speicheransatz | Speichermechanismus |
| ---------------- | ----------------- |
| [Cookies](#cookies) | HTTP-cookies schließen möglicherweise Daten ein, die mit serverseitigem App-Code gespeichert wurden. |
| [Sitzungszustand](#session-state) | HTTP-cookies und serverseitiger App-Code |
| [TempData](#tempdata) | HTTP-cookies oder Sitzungszustand |
| [Abfragezeichenfolgen](#query-strings) | HTTP-Abfragezeichenfolgen |
| [Verborgene Felder](#hidden-fields) | HTTP-Formularfelder |
| [HttpContext.Items](#httpcontextitems) | Serverseitiger App-Code |
| [Cache](#cache) | Serverseitiger App-Code |
| [Abhängigkeitsinjektion](#dependency-injection) | Serverseitiger App-Code |

## <a name="no-loccookies"></a>Cookie

Cookies speichern Daten anforderungsübergreifend. Da mit jeder Anforderung cookies gesendet werden, sollte deren Größe auf ein Minimum begrenzt sein. Idealerweise sollte nur ein Bezeichner in einem cookie gespeichert werden, während die Daten von der App gespeichert werden sollten. Die meisten Browser beschränken die Größe von cookies auf 4096 Byte. Nur eine begrenzte Anzahl von cookies ist für jede Domäne verfügbar.

Da cookies manipuliert werden können, müssen sie von der App überprüft werden. Cookies können von Benutzern gelöscht werden und können auf Clients ablaufen. cookies sind für gewöhnlich die dauerhafteste Form der Datenpersistenz auf dem Client.

Cookies werden häufig aus Personalisierungsgründen verwendet, wenn Inhalt für einen bekannten Benutzer angepasst wird. In den meisten Fällen wird der Benutzer nur identifiziert und nicht authentifiziert. Das cookie kann den Benutzernamen, Kontonamen oder eine eindeutige Benutzer-ID wie die GUID speichern. Dann können Sie mit dem cookie auf die persönlichen Einstellungen (z. B. die bevorzugte Farbe des Websitehintergrunds) des Benutzers zugreifen.

Beachten Sie die [Europäische Datenschutz-Grundverordnung (DSGVO)](https://ec.europa.eu/info/law/law-topic/data-protection) beim Ausstellen von cookies und beim Umgang mit Aspekten des Datenschutzes. Weitere In finden Sie unter [General Data Protection Regulation (GDPR) support in ASP.NET Core (DSGVO-Unterstützung in ASP.NET Core)](xref:security/gdpr).

## <a name="session-state"></a>Sitzungszustand

Der Sitzungszustand ist ein Szenario in ASP.NET Core zum Speichern von Benutzerdaten, wenn der Benutzer eine Web-App verwendet. Der Sitzungszustand verwendet einen von der App verwalteten Speicher, um Daten für mehrere Anforderungen eines Clients beizubehalten. Die Sitzungsdaten werden in einem Cache zwischengespeichert und sind kurzlebig. Die Website sollte auch ohne die Sitzungsdaten weiterhin funktionieren. Kritische Anwendungsdaten sollten in der Benutzerdatenbank gespeichert und nur zur Leistungsoptimierung in der Sitzung zwischengespeichert werden.

> [!NOTE]
> Sitzungen werden in [SignalRSignalR-Apps nicht unterstützt, da ein ](xref:signalr/index)[-Hub](xref:signalr/hubs) unabhängig vom HTTP-Kontext ausgeführt werden kann. Das kann z.B. passieren, wenn eine lange Abrufanforderung von einem Hub länger als die Lebensdauer des HTTP-Kontexts einer Anforderung offen gehalten wird.

ASP.NET Core verwaltet den Sitzungszustand, indem ein cookie an den Client übergeben wird, das die Sitzungs-ID enthält, die mit jeder Anforderung an den Server gesendet wird. Die App verwendet die Sitzungs-ID, um die Sitzungsdaten abzurufen.

Der Sitzungszustand verhält sich wie folgt:

* Da das Sitzungscookie browserspezifisch ist, können Sitzungen nicht für mehrere Browser gleichzeitig verwendet werden.
* Sitzungscookies werden gelöscht, wenn die Browsersitzung abläuft.
* Wenn für eine abgelaufene Sitzung ein cookie empfangen wird, wird eine neue Sitzung erstellt, die dasselbe cookie verwendet.
* Leere Sitzungen werden nicht beibehalten. Die Sitzung muss mindestens einen Wert enthalten, damit die Sitzung anforderungsübergreifend beibehalten wird. Wenn eine Sitzung nicht beibehalten wird, wird eine neue Sitzungs-ID für jede neue Anforderung erzeugt.
* Die App speichert Sitzungen für einen beschränkten Zeitraum nach der letzten Anforderung. Die App legt entweder ein Zeitlimit für die Sitzungen fest oder verwendet den Standardwert von 20 Minuten. Der Sitzungszustand eignet sich ideal zum Speichern von Benutzerdaten, die für eine bestimmte Sitzung zwar wichtig sind, die jedoch nicht dauerhaft sitzungsübergreifend gespeichert werden müssen.
* Sitzungsdaten werden gelöscht, wenn die Implementierung von <xref:Microsoft.AspNetCore.Http.ISession.Clear%2A?displayProperty=nameWithType> aufgerufen wird oder wenn die Sitzung abläuft.
* Es gibt kein Standardverfahren, wie App-Code darüber informiert wird, dass ein Clientbrowser geschlossen wurde oder dass ein Sitzungscookie gelöscht wurde oder auf dem Client abgelaufen ist.
* Die Vorlagen für ASP.NET Core MVC- und Razor Pages bieten Unterstützung für die Datenschutz-Grundverordnung (DSGVO). Sitzungsstatuscookie werden nicht standardmäßig als wichtig markiert, sodass der Sitzungsstatus erst dann eine Funktion hat, wenn der Besucher der Website die Nachverfolgung zulässt. Weitere Informationen finden Sie unter <xref:security/gdpr#tempdata-provider-and-session-state-cookies-arent-essential>.

> [!WARNING]
> Speichern Sie keine vertraulichen Daten im Sitzungszustand. Es besteht die Möglichkeit, dass der Benutzer seinen Browser nicht schließt oder die Sitzungscookies nicht löscht. Einige Browser behalten gültige Sitzungscookies browserfensterübergreifend bei. Eine Sitzung ist möglicherweise nicht nur auf einen Benutzer beschränkt, sodass der nächste Benutzer dasselbe Sitzungscookie verwendet.

Der Cacheanbieter im Arbeitsspeicher speichert Sitzungsdaten im Arbeitsspeicher des Servers, auf dem sich die App befindet. Beachten Sie in einem Szenario mit einer Serverfarm Folgendes:

* Verwenden Sie *Sticky Sessions* (anhaftende Sitzungen), um jede Sitzung an eine spezifische App-Instanz auf einem separaten Server zu binden. [Azure App Service](https://azure.microsoft.com/services/app-service/) verwendet [Routing von Anwendungsanforderungen (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module), um Sticky Sessions standardmäßig zu erzwingen. Diese Sitzungen können allerdings Auswirkungen auf die Skalierbarkeit haben und Updates für Web-Apps erschweren. Ein geeigneteres Vorgehen ist das Verwenden von Redis oder über SQL Server verteilte Caches, für die keine Sticky Sessions notwendig sind. Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.
* Das Sitzungscookie wird über <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> verschlüsselt. Der Datenschutz muss ordnungsgemäß konfiguriert sein, sodass er Sitzungscookies auf jedem Computer liest. Weitere Informationen finden Sie unter <xref:security/data-protection/introduction> und [Schlüsselspeicheranbieter](xref:security/data-protection/implementation/key-storage-providers).

### <a name="configure-session-state"></a>Konfigurieren des Sitzungszustands

Das Paket [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/), das im [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten ist, bietet Middleware zum Verwalten des Sitzungszustands. `Startup` muss folgende Elemente enthalten, um die Sitzungsmiddleware zu aktivieren:

* Einen beliebigen der <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Arbeitsspeichercaches. Die `IDistributedCache`-Implementierung wird als Sicherungsspeicher für Sitzungen verwendet. Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.
* Ein Aufruf von <xref:Microsoft.Extensions.DependencyInjection.SessionServiceCollectionExtensions.AddSession%2A> in `ConfigureServices`.
* Ein Aufruf von <xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession%2A> in `Configure`.

Der folgende Code zeigt, wie Sie den speicherinternen Sitzungsanbieter mit einer Standardimplementierung von `IDistributedCache` im Arbeitsspeicher einrichten:

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=5-14,34)]

Die Reihenfolge der Middleware ist wichtig. Im vorherigen Beispiel kommt es zu einer `InvalidOperationException`-Ausnahme, wenn `UseSession` nach `UseMvc` aufgerufen wird. Weitere Informationen finden Sie unter [Middleware](xref:fundamentals/middleware/index#order).

<xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> ist verfügbar, nachdem der Sitzungszustand konfiguriert wurde.

Auf `HttpContext.Session` kann vor einem Aufruf von `UseSession` nicht zugegriffen werden.

Nachdem die App mit dem Schreiben in den Antwortdatenstrom begonnen hat, kann keine neue Sitzung mit einem neuen Sitzungscookie erstellt werden. Die Ausnahme wird im Webserverprotokoll erfasst und nicht im Browser angezeigt.

### <a name="load-session-state-asynchronously"></a>Asynchrones Laden des Sitzungszustands

Der Standardsitzungsanbieter in ASP.NET Core lädt Sitzungsaufzeichnungen aus dem zugrunde liegenden <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>-Speicher nur dann asynchron, wenn die <xref:Microsoft.AspNetCore.Http.ISession.LoadAsync%2A?displayProperty=nameWithType>-Methode explizit vor den Methoden <xref:Microsoft.AspNetCore.Http.ISession.TryGetValue%2A>, <xref:Microsoft.AspNetCore.Http.ISession.Set%2A> oder <xref:Microsoft.AspNetCore.Http.ISession.Remove%2A> aufgerufen wird. Wenn `LoadAsync` nicht zuerst aufgerufen wird, werden die zugrunde liegenden Sitzungsaufzeichnungen synchron geladen, was entsprechende Auswirkungen auf die Leistung haben kann.

Damit Apps dieses Muster erzwingen, umschließen Sie die Implementierungen <xref:Microsoft.AspNetCore.Session.DistributedSessionStore> und <xref:Microsoft.AspNetCore.Session.DistributedSession> mit Versionen, die eine Ausnahme auslösen, wenn die `LoadAsync`-Methode nicht vor `TryGetValue`, `Set` oder `Remove` aufgerufen wird. Registrieren Sie die umschlossenen Versionen in den Dienstcontainern.

### <a name="session-options"></a>Sitzungsoptionen

Verwenden Sie <xref:Microsoft.AspNetCore.Builder.SessionOptions>, um Standardwerte für Sitzungen zu überschreiben.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie> | Bestimmt die Einstellungen, die zum Erstellen des cookies verwendet wurden. Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.Name> ist <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookieName?displayProperty=nameWithType> (`.AspNetCore.Session`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.Path> ist <xref:Microsoft.AspNetCore.Session.SessionDefaults.CookiePath?displayProperty=nameWithType> (`/`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> ist <xref:Microsoft.AspNetCore.Http.SameSiteMode.Lax?displayProperty=nameWithType> (`1`). Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> ist `true`. Der Standardwert von <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist `false`. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout> | `IdleTimeout` gibt an, wie lang die Sitzung sich im Leerlauf befinden darf, bevor die Inhalte verworfen werden. Jeder Zugriff auf eine Sitzung setzt das Zeitlimit zurück. Diese Einstellung gilt nur für den Inhalt der Sitzung und nicht für das cookie. Der Standardwert beträgt 20 Minuten. |
| <xref:Microsoft.AspNetCore.Builder.SessionOptions.IOTimeout> | Der maximale Zeitraum, in dem eine Sitzung aus dem Speicher geladen oder mithilfe eines Commits erneut in diesem hinterlegt werden kann. Diese Einstellung gilt möglicherweise nur für asynchrone Vorgänge. Dieses Zeitlimit kann mit <xref:System.Threading.Timeout.InfiniteTimeSpan> deaktiviert werden. Der Standardwert beträgt 1&#160;Minute. |

Die Sitzung verwendet ein cookie, um Anforderungen eines Browsers nachzuverfolgen und zu identifizieren. Standardmäßig wird das cookie `.AspNetCore.Session` genannt und verwendet den Pfad von `/`. Da die Standardeinstellungen des cookies keine Domäne festlegen, wird es dem clientseitigen Skript auf der Seite nicht zur Verfügung gestellt (da <xref:Microsoft.AspNetCore.Http.CookieBuilder.HttpOnly> standardmäßig auf `true` festgelegt ist).

Verwenden Sie `SessionOptions`, um Standardwerte für cookiesitzungen zu überschreiben.

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=14-19)]

Die App verwendet die <xref:Microsoft.AspNetCore.Builder.SessionOptions.IdleTimeout>-Eigenschaft, um zu bestimmen, wie lange sich eine Sitzung im Leerlauf befinden darf, bevor ihre Inhalte im Cache des Servers verworfen werden. Diese Eigenschaft ist unabhängig vom Ablauf der cookies. Jede Anforderung, die über die [Sitzungsmiddleware](xref:Microsoft.AspNetCore.Session.SessionMiddleware) übergeben wird, setzt das Zeitlimit zurück.

Der Sitzungszustand ist *nicht sperrend*. Die letzte Anforderung überschreibt die erste, wenn zwei Anforderungen gleichzeitig versuchen, die Inhalte einer Sitzung zu bearbeiten. `Session` wird als *kohärente Sitzung* implementiert, d.h., dass alle Inhalte zusammen gespeichert werden. Wenn zwei Anforderungen unterschiedliche Sitzungswerte bearbeiten möchten, überschreibt die letzte Anforderung möglicherweise Anforderungen der ersten.

### <a name="set-and-get-session-values"></a>Festlegen und Abrufen von Sitzungswerten

Auf den Sitzungszustand wird von der Razor Pages-Klasse <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> oder der MVC-Klasse <xref:Microsoft.AspNetCore.Mvc.Controller> mit <xref:Microsoft.AspNetCore.Http.HttpContext.Session?displayProperty=nameWithType> zugegriffen. Diese Eigenschaft ist eine Implementierung von <xref:Microsoft.AspNetCore.Http.ISession>.

Die `ISession`-Implementierung bietet mehrere Erweiterungsmethoden zum Festlegen und Abrufen von Integerwerten und Zeichenfolgenwerten. Die Erweiterungsmethoden befinden sich im Namespace <xref:Microsoft.AspNetCore.Http> (fügen Sie eine `using Microsoft.AspNetCore.Http;`-Anweisung hinzu, um Zugriff auf die Erweiterungsmethoden zu erhalten), wenn das Projekt auf das Paket [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) verweist. Beide Pakete sind im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.

`ISession`-Erweiterungsmethoden:

* [Get(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.Get%2A)
* [GetInt32(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetInt32%2A)
* [GetString(ISession, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.GetString%2A)
* [SetInt32(ISession, String, Int32)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetInt32%2A)
* [SetString(ISession, String, String)](xref:Microsoft.AspNetCore.Http.SessionExtensions.SetString%2A)

Mit dem folgenden Codebeispiel wird der Sitzungswert für den`IndexModel.SessionKeyName`-Schlüssel (`_Name` in der Beispiel-App) auf einer Razor Pages-Seite abgerufen:

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

Im folgenden Codebeispiel wird veranschaulicht, wie Sie eine ganze Zahl und eine Zeichenfolge abrufen und festlegen können:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

Alle Sitzungsdaten müssen serialisiert werden, um ein Szenario mit einem verteilten Cache zu ermöglichen, auch wenn Sie den speicherinternen Cache verwenden. Serialisierer für Zeichenfolgen und ganze Zahlen werden durch die Erweiterungsmethoden von <xref:Microsoft.AspNetCore.Http.ISession> bereitgestellt. Komplexe Typen müssen vom Benutzer mit einem anderen Verfahren wie etwa JSON serialisiert werden.

Fügen Sie die folgenden Erweiterungsmethoden hinzu, um serialisierbare Objekte für eine Sitzung festzulegen und abzurufen:

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

Im folgenden Beispiel wird dargestellt, wie Sie serialisierbare Objekte mit den Erweiterungsmethoden festlegen und abrufen:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="tempdata"></a>TempData

ASP.NET Core macht die Razor Pages-[TempData](xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.TempData) oder Controller-<xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar. Diese Eigenschaft speichert Daten, bis sie in einer anderen Anforderung gelesen werden. Mit den Methoden [Keep(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) und [Peek(String)](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Peek*) können die Daten untersucht werden, ohne am Ende der Anforderung gelöscht zu werden. [Keep()](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ITempDataDictionary.Keep*) markiert alle Elemente im Wörterbuch für die Aufbewahrung. `TempData` eignet sich besonders für die Weiterleitung, wenn Daten für mehr als eine Anforderung erforderlich sind. `TempData` wird von `TempData`-Anbietern durch Verwendung von cookies oder des Sitzungszustands implementiert.

## <a name="tempdata-samples"></a>TempData-Beispiele

Beachten Sie die folgende Seite, die einen Kunden erstellt:

[!code-csharp[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet&highlight=15-16,30)]

Die folgende Seite zeigt `TempData["Message"]` an:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexPeek.cshtml?range=1-14)]

Im vorangehenden Markup wird `TempData["Message"]` am Ende der Anforderung **nicht** gelöscht, da `Peek` verwendet wird. Bei Aktualisierung der Seite wird `TempData["Message"]` angezeigt.

Das folgende Markup ähnelt dem vorangehenden Code, verwendet jedoch `Keep`, um die Daten am Ende der Anforderung beizubehalten:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/IndexKeep.cshtml?range=1-14)]

Die Navigation zwischen den Seiten *IndexPeek* und *IndexKeep* löscht `TempData["Message"]` nicht.

Der folgende Code zeigt `TempData["Message"]` an, aber am Ende der Anforderung wird `TempData["Message"]` gelöscht:

[!code-cshtml[](app-state/3.0samples/RazorPagesContacts/Pages/Customers/Index.cshtml?range=1-14)]

### <a name="tempdata-providers"></a>TempData-Anbieter

Der cookiebasierte TempData-Anbieter wird standardmäßig verwendet, um TempData in cookies zu speichern.

Die cookiedaten werden mit <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> verschlüsselt, mit <xref:Microsoft.AspNetCore.WebUtilities.Base64UrlTextEncoder> codiert und dann segmentiert. Da das cookie in Blöcke unterteilt wird, gilt die in ASP.NET Core 1.x gefundene Größenbeschränkung nicht für einzelne cookies. Die cookiedaten werden nicht komprimiert, da es zu Sicherheitsproblemen kommen kann, wenn Daten verschlüsselt werden, z. B. durch [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit))- und [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit))-Angriffe. Weitere Informationen zum cookiebasierten TempData-Anbieter finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>.

### <a name="choose-a-tempdata-provider"></a>Auswählen eines TempData-Anbieters

Bevor Sie einen TempData-Anbieter auswählen, müssen Sie folgende Überlegungen anstellen:

1. Verwendet die App bereits den Sitzungszustand? Falls dies der Fall ist, hat die Verwendung des TempData-Anbieters abgesehen von der Größe der Daten keine zusätzlichen Auswirkungen auf die App.
2. Verwendet die App TempData nur selten für verhältnismäßig kleine Datenmengen (bis zu 500 Bytes)? Falls dies der Fall ist, entsteht durch das cookie des TempData-Anbieters nur ein kleiner zusätzlicher Aufwand für jede Anforderung, die TempData enthält. Falls dies nicht der Fall ist, kann der Sitzungszustand des TempData-Anbieters nützlich sein, um Roundtrips für große Datenmengen für jede Anforderung durchzuführen, bis TempData verarbeitet wird.
3. Wird die App in einer Serverfarm auf mehreren Servern ausgeführt? Wenn dies der Fall ist, ist keine weitere Konfiguration erforderlich, um das cookie des TempData-Anbieters außerhalb des Schutzes von Daten zu verwenden (weitere Informationen finden Sie unter <xref:security/data-protection/introduction> und [Schlüsselspeicheranbieter](xref:security/data-protection/implementation/key-storage-providers)).

> [!NOTE]
> Die meisten Webclients (z. B. Webbrowser) erzwingen Einschränkungen für die maximale Größe der cookies, für die Gesamtanzahl der cookies oder für beides. Wenn Sie das cookie des TempData-Anbieters verwenden, sollten Sie überprüfen, ob die App diese Einschränkungen überschreitet. Beachten Sie die Gesamtgröße der Daten. Rechnen Sie mit erhöhter cookiegröße aufgrund von Verschlüsselung und Segmentierung.

### <a name="configure-the-tempdata-provider"></a>Konfigurieren des TempData-Anbieters

Der cookiebasierte TempData-Anbieter ist standardmäßig aktiviert.

Verwenden Sie die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcViewFeaturesMvcBuilderExtensions.AddSessionStateTempDataProvider%2A>, um den sitzungsbasierten TempData-Anbieter zu aktivieren:

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

Die Reihenfolge der Middleware ist wichtig. Im vorherigen Beispiel kommt es zu einer `InvalidOperationException`-Ausnahme, wenn `UseSession` nach `UseMvc` aufgerufen wird. Weitere Informationen finden Sie unter [Middleware](xref:fundamentals/middleware/index#order).

> [!IMPORTANT]
> Wenn Sie für .NET Framework entwickeln und den sitzungsbasierten TempData-Anbieter verwenden, fügen Sie das Paket [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) zu Ihrem Projekt hinzu.

## <a name="query-strings"></a>Abfragezeichenfolgen

Eine begrenzte Menge an Daten kann von einer Anforderung an eine andere übergeben werden, indem Sie diese zu der Abfragezeichenfolge einer neuen Anforderung hinzufügen. Dies ist nützlich, um den Zustand dauerhaft zu erfassen und Links mit einem eingebetteten Zustand zuzulassen, die über E-Mail oder soziale Netzwerke geteilt werden sollen. Verwenden Sie keine Abfragezeichenfolgen für vertrauliche Daten, da URL-Abfragezeichenfolgen öffentlich sind.

Wenn Daten in Abfragezeichenfolgen eingefügt werden, können sie nicht nur versehentlich freigegeben werden, sondern es entstehen auch Gelegenheiten für Versuche einer [websiteübergreifenden Anforderungsfälschung](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)), wodurch Benutzer dazu gebracht werden können, auf bösartige Websites zuzugreifen, während sie authentifiziert sind. Angreifer können dann Benutzerdaten von der App stehlen oder schädliche Aktionen im Namen eines Benutzers durchführen. Jeder gespeicherte App- oder Sitzungszustand muss vor solchen Anforderungsfälschungen geschützt sein. Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

## <a name="hidden-fields"></a>Verborgene Felder

Daten können in ausgeblendeten Formularfeldern gespeichert und an die nächste Anforderung zurückgesendet werden. Dies ist häufig in mehrseitigen Formularen der Fall. Die App muss die in verborgenen Feldern gespeicherten Daten immer wieder überprüfen, da der Client die Daten manipulieren könnte.

## <a name="httpcontextitems"></a>HttpContext.Items

Die Auflistung <xref:Microsoft.AspNetCore.Http.HttpContext.Items?displayProperty=nameWithType> wird verwendet, um Daten zu speichern, während eine einzelne Anforderung verarbeitet wird. Die Inhalte der Auflistung werden nach der Verarbeitung jeder Anforderung verworfen. Die `Items`-Auflistung wird häufig von Komponenten oder Middleware zur Kommunikation verwendet, wenn sie zu unterschiedlichen Zeitpunkten während einer Anforderung ausgeführt werden und es keinen direkten Weg gibt, Parameter zu übergeben.

Im folgenden Beispiel fügt [Middleware](xref:fundamentals/middleware/index)`isVerified` zur Auflistung `Items` hinzu.

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

An einem späteren Punkt in der Pipeline kann eine andere Middleware den Wert von `isVerified` zugreifen:

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

Für Middleware, die nur von einer App verwendet wird, werden `string`-Schlüssel akzeptiert. Middleware, die von mehreren App-Instanzen verwendet wird, sollte eindeutige Objektschlüssel verwenden, um Schlüsselkonflikte zu vermeiden. Das folgenden Beispiel zeigt, wie Sie einen eindeutigen Objektschlüssel verwenden, der in einer Middlewareklasse definiert wurde:

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

Anderer Code kann auf den Wert zugreifen, der in `HttpContext.Items` gespeichert ist, indem er den Schlüssel verwendet, der von der Middlewareklasse zur Verfügung gestellt wird:

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

Dieser Ansatz hat außerdem den Vorteil, dass das Verwenden von Schlüsselzeichenfolgen im Code vermieden wird.

## <a name="cache"></a>cache

Das Caching stellt eine effiziente Möglichkeit zum Speichern und Abrufen von Daten dar. Die App kann die Lebensdauer zwischengespeicherter Elemente bestimmen.

Zwischengespeicherte Daten sind nicht mit einer spezifischen Anforderung, einem spezifischen Benutzer oder einer spezifischen Sitzung verknüpft. **Achten Sie darauf, dass Sie keine benutzerspezifischen Daten zwischenspeichern, die von den Anforderungen anderen Benutzer abgerufen werden können.**

Weitere Informationen finden Sie unter <xref:performance/caching/response>.

## <a name="dependency-injection"></a>Dependency Injection

Verwenden Sie [Dependency Injection](xref:fundamentals/dependency-injection), um Daten für Benutzer bereitzustellen:

1. Definieren Sie einen Dienst, der die Daten enthält. Definieren Sie z.B. die Klasse `MyAppData`:

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. Fügen Sie die Dienstklasse zu `Startup.ConfigureServices` hinzu:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. Verwenden Sie die Datendienstklasse:

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

## <a name="common-errors"></a>Häufige Fehler

* „Unable to resolve service for type 'Microsoft.Extensions.Caching.Distributed.IDistributedCache' while attempting to activate 'Microsoft.AspNetCore.Session.DistributedSessionStore'. (Dienst kann nicht für den Typ „Microsoft.Extensions.Caching.Distributed.IDistributedCache“ aufgelöst werden, während versucht wird, auf „Microsoft.AspNetCore.Session.DistributedSessionStore“ zuzugreifen.)

  Dieser Fehler entsteht in der Regel, wenn nicht alle `IDistributedCache`-Implementierungen konfiguriert werden. Weitere Informationen finden Sie unter <xref:performance/caching/distributed> und <xref:performance/caching/memory>.

* Falls die Sitzungsmiddleware eine Sitzung nicht speichert (z.B. wenn der Sicherungsspeicher nicht verfügbar ist), protokolliert sie die Ausnahme, und die Anforderung wird ordnungsgemäß ausgeführt. Das führt zu unvorhersehbarem Verhalten.

  Nehmen wir an, dass ein Benutzer seinen Einkaufswagen in einer Sitzung speichert. Der Benutzer fügt ein Element zum Einkaufswagen hinzu, aber der Commit schlägt fehl. Die App wird nicht über den Fehler informiert und meldet dem Benutzer, dass das Element zum Einkaufswagen hinzugefügt wurde. Dies stimmt jedoch nicht.

  Es wird empfohlen, nach Fehlern zu suchen, indem Sie `await feature.Session.CommitAsync();` über App-Code aufrufen, wenn die App mit dem Schreiben in die Sitzung fertig ist. `CommitAsync` löst eine Ausnahme aus, wenn der Sicherungsspeicher nicht verfügbar ist. Wenn `CommitAsync` fehlschlägt, kann die App die Ausnahme verarbeiten. `LoadAsync` wird unter den gleichen Bedingungen ausgelöst, wenn der Sicherungsspeicher nicht verfügbar ist.
  
## <a name="no-locsignalr-and-session-state"></a>SignalR und Sitzungszustand

SignalR-Apps dürfen nicht den Sitzungszustand verwenden, um Informationen zu speichern. SignalR-Apps können Informationen je nach Verbindungszustand in `Context.Items` im Hub speichern. <!-- https://github.com/aspnet/SignalR/issues/2139 -->

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:host-and-deploy/web-farm>
::: moniker-end
