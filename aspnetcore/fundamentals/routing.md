---
title: Routing in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie das ASP.NET Core-Routing für das Abgleichen von HTTP-Anforderungen und das Verteilen an ausführbare Endpunkte eingesetzt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 4/1/2020
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
uid: fundamentals/routing
ms.openlocfilehash: e134832ad00b10bb01239afa06acc74d86707af1
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422560"
---
# <a name="routing-in-aspnet-core"></a>Routing in ASP.NET Core

Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Das Routing wird für das Abgleichen von HTTP-Anforderungen und das Verteilen an ausführbare Endpunkte der App eingesetzt. [Endpunkte](#endpoint) sind die Einheiten des ausführbaren Codes für die Anforderungsverarbeitung in der App. Endpunkte werden in der App definiert und beim Start der App konfiguriert. Beim Endpunktabgleich können Werte aus der Anforderungs-URL extrahiert und für die Verarbeitung der Anforderung bereitgestellt werden. Mithilfe von Endpunktinformationen aus der App lassen sich durch das Routing URLs generieren, die Endpunkten zugeordnet werden.

Apps können das Routing mit folgenden Funktionen konfigurieren:

- Controller
- Razor Pages
- SignalR
- gRPC-Dienste
- Endpunktfähige [Middleware](xref:fundamentals/middleware/index) wie [Integritätsprüfungen](xref:host-and-deploy/health-checks)
- Für das Routing registrierte Delegate und Lambdas

In diesem Artikel werden die grundlegenden Details zum ASP.NET Core-Routing beschrieben. Informationen zur Routingkonfiguration finden Sie wie folgt:

* Für Controller unter <xref:mvc/controllers/routing>.
* Konventionen für Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.

Das in diesem Dokument beschriebene Endpunktroutingsystem gilt für ASP.NET Core 3.0 und höher. Weitere Informationen zum vorherigen Routingsystem, das auf <xref:Microsoft.AspNetCore.Routing.IRouter> basiert, erhalten Sie, wenn Sie die ASP.NET Core 2.1-Version wie folgt auswählen:

* Mit der Versionsauswahl für eine vorherige Version.
* Mit Auswahl von [ASP.NET Core 2.1](?view=aspnetcore-2.1).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Downloadbeispiele für dieses Dokument werden durch eine bestimmte `Startup`-Klasse aktiviert. Um ein bestimmtes Beispiel auszuführen, ändern Sie *Program.cs*, um die gewünschte `Startup`-Klasse aufzurufen.

## <a name="routing-basics"></a>Routinggrundlagen

Alle ASP.NET Core-Vorlagen enthalten die Routingfunktionen im generierten Code. Das Routing wird in `Startup.Configure` in der [Middlewarepipeline](xref:fundamentals/middleware/index) registriert.

Der folgende Code veranschaulicht ein einfaches Beispiel für das Routing:

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet&highlight=8,10)]

Beim Routing wird ein Middlewarepaar verwendet, das durch <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> registriert wird:

* `UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu. Diese Middleware prüft die in der App definierten Endpunkte und wählt anhand der Anforderung die [beste Übereinstimmung](#urlm) aus.
* `UseEndpoints` fügt der Middlewarepipeline die Endpunktausführung hinzu. Dabei wird der mit dem ausgewählten Endpunkt verknüpfte Delegat ausgeführt.

Das vorherige Beispiel enthält eine einzelnen *Route-zu- Code*-Endpunkt unter Verwendung der [MapGet](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*)-Methode:

* Wenn eine `GET`-HTTP-Anforderung an die Stamm-URL `/`gesendet wird:
  * Der angezeigte Anforderungsdelegat wird ausgeführt.
  * `Hello World!` wird in die HTTP-Antwort geschrieben. Die Stamm-URL `/` lautet standardmäßig `https://localhost:5001/`.
* Wenn die Anforderungsmethode nicht `GET` bzw. die Stamm-URL nicht `/` ist, gibt es keinen Routenabgleich und es wird ein HTTP-404-Fehler zurückgegeben.

### <a name="endpoint"></a>Endpunkt

<a name="endpoint"></a>

Die `MapGet`-Methode wird verwendet, um einen **Endpunkt** zu definieren. Ein Endpunkt kann Folgendes sein:

* Ausgewählt, indem die URL und die HTTP-Methode abgeglichen werden.
* Ausgeführt, indem ein Delegat ausgeführt wird.

Endpunkte, die von der App zugeordnet und ausgeführt werden können, sind in `UseEndpoints` konfiguriert. Mit <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapGet*>, <xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions.MapPost*> und [ähnlichen Methoden](xref:Microsoft.AspNetCore.Builder.EndpointRouteBuilderExtensions) werden beispielsweise Anforderungsdelegate mit dem Routingsystem verbunden.
Zudem können weitere Methoden zur Verbindung von ASP.NET Core-Frameworkfunktionen mit dem Routingsystem verwendet werden:
- [MapRazorPages für Razor Pages](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapRazorPages*)
- [MapControllers für Controller](xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*)
- [MapHub\<THub> für SignalR](xref:Microsoft.AspNetCore.SignalR.HubRouteBuilder.MapHub*) 
- [MapGrpcService\<TService> für gRPC](xref:grpc/aspnetcore)

Das folgende Beispiel zeigt das Routing mit einer anspruchsvolleren Routenvorlage:

[!code-csharp[](routing/samples/3.x/RoutingSample/RouteTemplateStartup.cs?name=snippet)]

Die Zeichenfolge `/hello/{name:alpha}` ist eine **Routenvorlage**. Sie wird verwendet, um zu konfigurieren, wie der Endpunkt abgeglichen wird. In diesem Fall gleicht die Vorlage Folgendes ab:

* Eine URL wie `/hello/Ryan`
* Alle URL-Pfade, die mit `/hello/` beginnen, gefolgt von einer Sequenz alphabetischer Zeichen.  `:alpha` wendet eine Routeneinschränkung an, die nur alphabetische Zeichen abgleicht. [Routeneinschränkungen](#route-constraint-reference) werden weiter unten in diesem Dokument erläutert.

Das zweite Segment des URL-Pfads, `{name:alpha}`:

* Ist an den Parameter `name` gebunden.
* Ist in [HttpRequest.RouteValues](xref:Microsoft.AspNetCore.Http.HttpRequest.RouteValues*) erfasst und gespeichert.

Das in diesem Dokument beschriebene Endpunktroutingsystem gilt ab ASP.NET Core 3.0. Alle Versionen von ASP.NET Core unterstützen jedoch dieselben Routenvorlagenfunktionen und Routeneinschränkungen.

Das folgende Beispiel zeigt das Routing mit [Integritätsprüfungen](xref:host-and-deploy/health-checks) und Autorisierung:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Im vorherigen Beispiel wird veranschaulicht, wie Sie:

* Die Autorisierungsmiddleware für das Routing verwenden.
* Endpunkte zum Konfigurieren des Autorisierungsverhaltens verwendet werden können.

Der <xref:Microsoft.AspNetCore.Builder.HealthCheckEndpointRouteBuilderExtensions.MapHealthChecks*>-Aufruf fügt einen Endpunkt für eine Integritätsprüfung hinzu. Durch die Verkettung von <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*> mit diesem Aufruf wird eine Autorisierungsrichtlinie an den Endpunkt angefügt.

Der Aufruf von <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*> wird die Authentifizierungs- und Autorisierungsmiddleware hinzugefügt. Diese Middleware wird zum Ausführen folgender Aktionen zwischen <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*> und `UseEndpoints` platziert:

* Anzeigen des von `UseRouting` ausgewählten Endpunkts.
* Anwenden einer Autorisierungsrichtlinie vor dem Senden von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> an den Endpunkt.

<a name="metadata"></a>

### <a name="endpoint-metadata"></a>Endpunktmetadaten

Im vorangehenden Beispiel gibt es zwei Endpunkte, aber nur dem für die Integritätsprüfung ist eine Autorisierungsrichtlinie angefügt. Wenn die Anforderung mit dem Endpunkt der Integritätsprüfung, `/healthz`, übereinstimmt, wird eine Autorisierungsprüfung durchgeführt. Dadurch wird veranschaulicht, dass Endpunkten zusätzliche Daten zugeordnet werden können. Diese zusätzlichen Daten werden als **Metadaten** des Endpunkts bezeichnet:

* Die Metadaten lassen sich von routingfähiger Middleware verarbeiten.
* Die Metadaten können einen beliebigen .NET-Typ aufweisen.

## <a name="routing-concepts"></a>Routingkonzepte

Durch Hinzufügen des effizienten **Endpunkt**-Konzepts stellt das Routingsystem eine Ergänzung der Middlewarepipeline dar. Endpunkte stehen für Funktionseinheiten der App, die sich in Bezug auf Routing, Autorisierung und die Anzahl der ASP.NET Core-Systeme voneinander unterscheiden.

<a name="endpoint"></a>

### <a name="aspnet-core-endpoint-definition"></a>ASP.NET Core-Endpunktdefinition

Ein ASP.NET Core-Endpunkt ist:

* Ausführbar: Verfügt über eine <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft.
* Erweiterbar: Verfügt über eine [Metadata](xref:Microsoft.AspNetCore.Http.Endpoint.Metadata*)-Sammlung.
* Selectable (Auswählbar): Verfügt optional über [Routinginformationen](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern*).
* Aufzählbar: Die Sammlung von Endpunkten kann durch Abrufen der <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>-Klasse aus [DI](xref:fundamentals/dependency-injection) aufgelistet werden.

Der folgende Code zeigt, wie der Endpunkt, der mit der aktuellen Anforderung übereinstimmt, abgerufen und geprüft werden kann:

[!code-csharp[](routing/samples/3.x/RoutingSample/EndpointInspectorStartup.cs?name=snippet)]

Der Endpunkt, falls ausgewählt, kann aus dem `HttpContext`-Element abgerufen werden. Seine Eigenschaften können geprüft werden. Endpunktobjekte sind unveränderlich und können nach der Erstellung nicht mehr geändert werden. Der häufigste Typ des Endpunkts ist eine <xref:Microsoft.AspNetCore.Routing.RouteEndpoint>-Klasse. Die `RouteEndpoint`-Klasse enthält Informationen, durch die diese vom Routingsystem ausgewählt werden kann.

Im vorangehenden Code wird mit [app.Use](xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*) eine [Middleware](xref:fundamentals/middleware/index) inline konfiguriert.

<a name="mt"></a>

Der folgende Code zeigt, dass es, je nachdem, wo `app.Use` in der Pipeline aufgerufen wird, möglicherweise keinen Endpunkt gibt:

[!code-csharp[](routing/samples/3.x/RoutingSample/MiddlewareFlowStartup.cs?name=snippet)]

In diesem vorangehenden Beispiel werden `Console.WriteLine`-Anweisungen hinzugefügt, die anzeigen, ob ein Endpunkt ausgewählt wurde oder nicht. Aus Gründen der Übersichtlichkeit wird in dem Beispiel dem bereitgestellten `/`-Endpunkt ein Anzeigename zugewiesen.

Wenn Sie diesen Code mit einer URL `/` ausführen, wird Folgendes angezeigt:

```txt
1. Endpoint: (null)
2. Endpoint: Hello
3. Endpoint: Hello
```

Wenn Sie diesen Code mit einer anderen URL ausführen, wird Folgendes angezeigt:

```txt
1. Endpoint: (null)
2. Endpoint: (null)
4. Endpoint: (null)
```

Diese Ausgabe zeigt Folgendes:

* Der Endpunkt ist immer NULL, bevor `UseRouting` aufgerufen wird.
* Wenn eine Übereinstimmung gefunden wird, ist der Endpunkt zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ungleich NULL.
* Die `UseEndpoints`-Middleware ist eine **Terminalmiddleware**, wenn eine Übereinstimmung gefunden wird. [Terminalmiddleware](#tm) wird weiter unten in diesem Dokument definiert.
* Die Middleware nach `UseEndpoints` wird nur ausgeführt, wenn keine Übereinstimmung gefunden wird.

Die `UseRouting`-Middleware verwendet die [SetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.SetEndpoint*)-Methode, um den Endpunkt an den aktuellen Kontext anzufügen. Es ist möglich, die `UseRouting`-Middleware durch benutzerdefinierte Logik zu ersetzen und dennoch die Vorteile durch die Verwendung von Endpunkten zu nutzen. Endpunkte befinden sich auf niedriger Ebene, wie Middleware, und sind nicht an die Routingimplementierung gekoppelt. Die meisten Apps müssen `UseRouting` nicht durch eigene Logik ersetzen.

Die `UseEndpoints`-Middleware ist so konzipiert, dass Sie zusammen mit der `UseRouting`-Middleware verwendet werden kann. Die Hauptlogik zum Ausführen eines Endpunkts ist nicht kompliziert. Mit <xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*> können Sie einen Endpunkt abrufen und dann dessen <xref:Microsoft.AspNetCore.Http.Endpoint.RequestDelegate>-Eigenschaft aufrufen.

Der folgende Code veranschaulicht, wie Middleware das Routing beeinflussen oder darauf reagieren kann:

[!code-csharp[](routing/samples/3.x/RoutingSample/IntegratedMiddlewareStartup.cs?name=snippet)]

Im vorherigen Beispiel werden zwei wichtige Konzepte dargestellt:

* Die Middleware kann vor `UseRouting` ausgeführt werden, um die Daten zu ändern, auf denen das Routing basiert.
    * Normalerweise verändert Middleware, die vor dem Routing eingesetzt wird, eine Eigenschaft der Anforderung, wie z.B. <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>, <xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions.UseHttpMethodOverride*> oder <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*>.
* Die Middleware kann zwischen `UseRouting` und <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt werden, um die Ergebnisse des Routings vor der Ausführung des Endpunkts zu verarbeiten.
    * Middleware, die zwischen `UseRouting` und `UseEndpoints` ausgeführt wird:
      * Überprüft in der Regel die Metadaten, um die Endpunkte zu ermitteln.
      * Trifft häufig Sicherheitsentscheidungen, wie `UseAuthorization` und `UseCors`.
    * Durch die Kombination aus Middleware und Metadaten ist es möglich, für jeden einzelnen Endpunkt Richtlinien zu konfigurieren.

Der vorangehende Code zeigt ein Beispiel für eine benutzerdefinierte Middleware, die entpunktbezogene Richtlinien unterstützt. Die Middleware schreibt ein *Überwachungsprotokoll* für den Zugriff auf vertrauliche Daten in der Konsole. Die Middleware kann so konfiguriert werden, dass ein Endpunkt mit den `AuditPolicyAttribute`-Metadaten *überwacht* wird. In diesem Beispiel wird ein *Opt-In*-Muster veranschaulicht, bei dem nur Endpunkte überwacht werden, die als vertraulich markiert sind. Es ist möglich, diese Logik umgekehrt zu definieren, indem beispielsweise alles geprüft wird, was nicht als sicher markiert ist. Das Endpunktmetadaten-System ist flexibel. Diese Logik lässt sich für jeden Anwendungsfall passend schreiben.

Der vorherige Beispielcode soll die grundlegenden Konzepte von Endpunkten veranschaulichen. **Das Beispiel ist nicht für Produktionsumgebungen vorgesehen.** Eine vollständigere Version einer Middleware für *Überwachungsprotokolle* würde Folgendes bieten:

* Protokollieren in einer Datei oder einer Datenbank.
* Einschließen von Details wie Benutzer, IP-Adresse, Name des vertraulichen Endpunkts usw.

Die Metadaten der Überwachungsrichtlinie `AuditPolicyAttribute` sind als `Attribute` definiert, um die Verwendung mit klassenbasierten Frameworks wie Controllern und SignalR zu erleichtern. Bei Verwendung von *Route-zu-Code*:

* Metadaten werden an eine Generator-API angefügt.
* Klassenbasierte Frameworks enthalten beim Erstellen von Endpunkten alle Attribute der entsprechenden Methode und Klasse.

Die bewährten Methoden für Metadatentypen sind, sie entweder als Schnittstellen oder als Attribute zu definieren. Schnittstellen und Attribute ermöglichen die Wiederverwendung von Code. Das Metadatensystem ist flexibel und weist keine Einschränkungen auf.

<a name="tm"></a>

### <a name="comparing-a-terminal-middleware-and-routing"></a>Vergleichen von Terminalmiddleware und Routing

Das folgende Codebeispiel zeigt den Unterschied zwischen Middleware und Routing:

[!code-csharp[](routing/samples/3.x/RoutingSample/TerminalMiddlewareStartup.cs?name=snippet)]

Beim in `Approach 1:` gezeigten Stil von Middleware handelt es sich um **Terminalmiddleware**. Sie wird als Terminalmiddleware bezeichnet, da sie einen Abgleich durchgeführt.

* Der Abgleich im vorangehenden Beispiel ist `Path == "/"` für die Middleware und `Path == "/Movie"` für das Routing.
* Bei einem erfolgreichen Abgleich führt sie bestimmte Funktionen aus und kehrt zurück, anstatt die `next`-Middleware aufzurufen.

Sie wird „Terminal Middleware“ genannt, da sie die Suche beendet, einige Funktionen ausführt und dann zurückkehrt.

Vergleichen von Terminalmiddleware und Routing:
* Beide Ansätze ermöglichen das Beenden der Verarbeitungspipeline:
    * Die Middleware beendet die Pipeline, indem Sie zurückkehrt, anstatt `next` aufzurufen.
    * Endpunkte beenden immer die Verarbeitung.
* Terminalmiddleware ermöglicht die Positionierung der Middleware an einer beliebigen Stelle in der Pipeline:
    * Endpunkte werden an der Position von <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> ausgeführt.
* Mit Terminalmiddleware kann beliebiger Code beendet werden, wenn die Middleware übereinstimmt:
    * Ein benutzerdefinierter Routenabgleichscode kann sehr umständlich und schwierig zu schreiben sein.
    * Das Routing bietet unkomplizierte Lösungen für typische Apps. Die meisten Apps erfordern keinen benutzerdefinierten Routenabgleichscode.
* Endpunkte haben eine Schnittstelle mit Middleware wie `UseAuthorization` und `UseCors`.
    * Die Verwendung einer Terminalmiddleware mit `UseAuthorization` oder `UseCors` erfordert eine manuelle Verknüpfung mit dem Autorisierungssystem.

Ein [Endpunkt](#endpoint) definiert beides:

* Einen Delegaten zum Verarbeiten von Anforderungen.
* Eine Sammlung beliebiger Metadaten. Die Metadaten werden zur Implementierung von übergreifenden Belangen verwendet, die auf Richtlinien und der Konfiguration basieren, die den einzelnen Endpunkten angefügt sind.

Terminalmiddleware kann sehr nützlich sein, erfordert aber möglicherweise auch:

* Einen großen Codierungs- und Testaufwand.
* Eine manuelle Integration in andere Systeme für die gewünschte Flexibilität.

Ziehen Sie daher zunächst die Integration von Routingfunktionen in Betracht, bevor Sie damit beginnen, Terminalmiddleware zu schreiben.

Vorhandene Terminalmiddleware, die in [Map](xref:fundamentals/middleware/index#branch-the-middleware-pipeline) oder <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> integriert ist, kann in der Regel in einen routingfähigen Endpunkt umgewandelt werden. [MapHealthChecks](https://github.com/aspnet/AspNetCore/blob/master/src/Middleware/HealthChecks/src/Builder/HealthCheckEndpointRouteBuilderExtensions.cs#L16) zeigt das Muster für eine Routinglösung:
* Schreiben Sie eine Erweiterungsmethode in der <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder>-Schnittstelle.
* Erstellen Sie eine geschachtelte Middlewarepipeline mit <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.CreateApplicationBuilder*>.
* Fügen Sie die Middleware an die neue Pipeline an. In diesem Fall <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>.
* Verwenden Sie <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.Build*>, um die Middlewarepipeline in einem <xref:Microsoft.AspNetCore.Http.RequestDelegate>-Delegaten zu erstellen.
* Rufen Sie `Map` auf, und stellen Sie die neue Middlewarepipeline bereit.
* Geben Sie das Generatorobjekt zurück, das von `Map` aus der Erweiterungsmethode bereitgestellt wurde.

Im folgenden Code ist die Verwendung von [MapHealthChecks](xref:host-and-deploy/health-checks) gezeigt:

[!code-csharp[](routing/samples/3.x/RoutingSample/AuthorizationStartup.cs?name=snippet)]

Das vorangehende Beispiel zeigt, warum das Zurückgeben des Generatorobjekts wichtig ist. Wenn das Generatorobjekt zurückgegeben wird, kann der App-Entwickler Richtlinien konfigurieren, z. B. die Autorisierung für den Endpunkt. In diesem Beispiel ist die Middleware für Integritätsprüfungen nicht direkt in das Autorisierungssystem integriert.

Das Metadatensystem wurde als Antwort auf die Probleme erstellt, die von Erweiterbarkeitsautoren mithilfe von Terminalmiddleware aufgetreten sind. Für jede Middleware ist es problematisch, deren eigene Integration in das Autorisierungssystem umzusetzen.

<a name="urlm"></a>

### <a name="url-matching"></a>URL-Zuordnung

* Bei diesem Prozess werden eingehende Anforderungen durch Routing mit einem [Endpunkt](#endpoint) abgeglichen.
* Basiert auf Daten im URL-Pfad und den URL-Headern.
* Kann erweitert werden, um beliebige Daten in der Anforderung zu überprüfen.

Wenn eine Routingmiddleware ausgeführt wird, legt sie ein `Endpoint`-Element fest und leitet Werte an eine [Anforderungsfunktion](xref:fundamentals/request-features) in der <xref:Microsoft.AspNetCore.Http.HttpContext>-Klasse der aktuellen Anforderung weiter:

* Durch Aufrufen von [HttpContext.GetEndPoint](<xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint*>) wird der Endpunkt abgerufen.
* `HttpRequest.RouteValues` ruft die Sammlung der Routenwerte ab.

[Middleware](xref:fundamentals/middleware/index), die nach der Routingmiddleware ausgeführt wird, kann den Endpunkt erkennen und Maßnahmen ergreifen. So kann beispielsweise eine Autorisierungsmiddleware die Erfassung der Metadaten des Endpunkts für eine Autorisierungsrichtlinie abfragen. Nachdem die gesamte Middleware in der Anforderungsverarbeitungspipeline ausgeführt wurde, wird der Delegat des ausgewählten Endpunkts aufgerufen.

Das Routingsystem ist beim Endpunktrouting für alle Weiterleitungsentscheidungen zuständig. Da die Middleware Richtlinien auf der Grundlage des ausgewählten Endpunkts anwendet, ist Folgendes wichtig:

* Alle Entscheidungen, die sich auf die Verteilung oder die Anwendung von Sicherheitsrichtlinien auswirken können, werden im Routingsystem getroffen.

> [!WARNING]
> Wenn für Abwärtskompatibilität ein Controller- oder Razor Pages-Endpunktdelegat ausgeführt wird, werden die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte festgelegt.
>
> Der Typ `RouteContext` wird in einer zukünftigen Version als veraltet markiert:
>
> * Migrieren Sie `RouteData.Values` zu `HttpRequest.RouteValues`.
> * Migrieren Sie `RouteData.DataTokens`, um [IDataTokensMetadata](xref:Microsoft.AspNetCore.Routing.IDataTokensMetadata) aus den Endpunktmetadaten abzurufen.

Der URL-Abgleich erfolgt in mehreren Phasen und kann konfiguriert werden. In jeder Phase werden mehrere Übereinstimmungen ausgegeben. Diese Übereinstimmungen lassen sich in der nächsten Phase weiter eingrenzen. Die Routingimplementierung garantiert keine Verarbeitungsreihenfolge für übereinstimmende Endpunkte. **Alle** möglichen Übereinstimmungen werden gleichzeitig verarbeitet. Für die URL-Abgleichsphasen gilt folgende Reihenfolge. ASP.NET Core:

1. Verarbeitet den URL-Pfad mit mehreren Endpunkten und ihren Routenvorlagen und sammelt **alle** Übereinstimmungen.
1. Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den angewendeten Routeneinschränkungen passen.
1. Nimmt die vorangehende Liste und entfernt die Übereinstimmungen, die nicht zu den [MatcherPolicy](xref:Microsoft.AspNetCore.Routing.MatcherPolicy)-Instanzen passen.
1. Verwendet [EndpointSelector](xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector), um eine abschließende Entscheidung anhand der vorangehenden Liste zu treffen.

Die Liste der Endpunkte wird entsprechend den folgenden Punkten priorisiert:

* Die [RouteEndpoint.Order](xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order*)-Eigenschaft
* Die [Priorität der Routenvorlage](#rtp)

Alle übereinstimmenden Endpunkte werden in jeder Phase verarbeitet, bis <xref:Microsoft.AspNetCore.Routing.Matching.EndpointSelector> erreicht ist. `EndpointSelector` stellt die abschließende Phase dar. Darin wird der Endpunkt mit der höchsten Priorität aus den Übereinstimmungen als beste Übereinstimmung ausgewählt. Wenn es andere Übereinstimmungen mit derselben Priorität wie die beste Übereinstimmung gibt, wird ein Ausnahmefehler wegen einer nicht eindeutigen Übereinstimmung ausgelöst.

Die Routenpriorität wird anhand einer **spezifischeren** Routenvorlage berechnet, der eine höhere Priorität eingeräumt wird. Dies wird z. B. anhand der Vorlagen `/hello` und `/{message}` deutlich:

* Beide stimmen mit dem URL-Pfad `/hello` überein.
* `/hello` ist spezifischer und hat daher eine höhere Priorität.

Im Allgemeinen eignet sich die Routenpriorität gut, um die beste Übereinstimmung für die in der Praxis verwendeten URL-Schemata zu finden. Verwenden Sie <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.Order> nur bei Bedarf, um eine Mehrdeutigkeit zu vermeiden.

Aufgrund der Erweiterungsmöglichkeiten, die das Routing bietet, kann das Routingsystem die mehrdeutigen Routen nicht im Voraus berechnen. Betrachten Sie ein Beispiel wie die Routenvorlagen `/{message:alpha}` und `/{message:int}`:

* Die `alpha`-Einschränkung gleicht nur alphabetische Zeichen ab.
* Die `int`-Einschränkung gleicht nur Zahlen ab.
* Diese Vorlagen haben die gleiche Routenpriorität, aber es gibt keine einzige URL, auf die sie beide zutreffen.
* Wenn das Routingsystem beim Starten einen Mehrdeutigkeitsfehler gemeldet hat, würde dieser den gültigen Anwendungsfall blockieren.

> [!WARNING]
>
> Die Reihenfolge der Vorgänge in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> wirkt sich nicht auf das Routingverhalten aus, mit einer Ausnahme. <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*> und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> weisen ihren Endpunkten automatisch einen Reihenfolgenwert zu, basierend auf der Reihenfolge, in der sie aufgerufen werden. Dadurch wird das Langzeitverhalten von Controllern simuliert, ohne dass das Routingsystem die gleichen Garantien bietet wie ältere Routingimplementierungen.
>
> Bei der bisherigen Routingimplementierung war es möglich, eine Routingerweiterung vorzunehmen, die von der Verarbeitungsreihenfolge der Routen abhängt. Endpunktrouting in ASP.NET Core 3.0 und höher:
> 
> * Weist kein Konzept für Routen auf.
> * Bietet keine garantierte Reihenfolge. Alle Endpunkte werden gleichzeitig verarbeitet.

<a name="rtp"></a>

### <a name="route-template-precedence-and-endpoint-selection-order"></a>Routenvorlagenpriorität und Reihenfolge der Endpunktauswahl

Die [Routenvorlagenpriorität](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L16) ist ein System, bei dem jeder Routenvorlage ein Wert zugewiesen wird, je nachdem, wie spezifisch diese ist. Routenvorlagenpriorität:

* Verhindert, dass die Reihenfolge der Endpunkte häufig angepasst werden muss.
* Versucht, die allgemeinen Erwartungen an das Routingverhalten abzugleichen.

Dies wird z. B. anhand der Vorlagen `/Products/List` und `/Products/{id}` deutlich. Es wäre begründet, anzunehmen, dass `/Products/List` eine bessere Übereinstimmung als `/Products/{id}` für den URL-Pfad `/Products/List` ist. Dies funktioniert, weil das Literalsegment `/List` eine höhere Priorität als das Parametersegment `/{id}` hat.

Wie die Priorisierung im Einzelnen funktioniert, ist an die Definition der Routenvorlagen gekoppelt:

* Vorlagen mit mehr Segmenten sind in der Regel spezifischer.
* Ein Segment mit Literaltext gilt als spezifischer als ein Parametersegment.
* Ein Parametersegment mit einer Einschränkung gilt als spezifischer als ein Parametersegment ohne Einschränkung.
* Ein komplexes Segment wird als genauso spezifisch betrachtet wie ein Parametersegment mit einer Einschränkung.
* Catch-All-Parameter, die am wenigsten spezifisch sind. Unter **Catch-All** in der [Referenz zu Routenvorlagen](#rtr) finden Sie wichtige Informationen zu Catch-All-Routen.

Einen Verweis auf genaue Werte finden Sie im [Quellcode auf GitHub](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Template/RoutePrecedence.cs#L189).

<a name="lg"></a>

### <a name="url-generation-concepts"></a>Konzepte zur URL-Generierung

URL-Generierung:

* Der Prozess, bei dem durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt wird.
* Sie ermöglicht eine logische Trennung zwischen den Endpunkten und den URLs, die auf diese zugreifen.

Das Endpunktrouting umfasst die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). `LinkGenerator` ist ein Singleton-Dienst, der in [DI](xref:fundamentals/dependency-injection) verfügbar ist. Die `LinkGenerator`-API kann außerhalb des Kontexts einer ausgeführten Anforderung verwendet werden. [Mvc.IUrlHelper](xref:Microsoft.AspNetCore.Mvc.IUrlHelper) und Szenarios, die von <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> abhängig sind (z. B. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), HTML-Hilfsprogramme und [Aktionsergebnisse](xref:mvc/controllers/actions)), verwenden die `LinkGenerator`-API, um entsprechende Funktionen bereitzustellen.

Die API zur Linkgenerierung wird von Konzepten wie **Adressen** und **Adressschemas** unterstützt. Sie können mithilfe eines Adressschemas die Endpunkte bestimmen, die bei der Linkgenerierung berücksichtigt werden sollen. Beispielsweise werden Routennamen und Routenwerte als Adressschemas implementiert. Diese Szenarios kennen viele Benutzer von Controllern und Razor Pages.

Die API zur Linkgenerierung kann Controller und Razor Pages über die folgenden Erweiterungsmethoden miteinander verknüpfen:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Beim Überladen dieser Methoden werden Argumente akzeptiert, die den `HttpContext` umfassen. Diese Methoden sind zwar in funktionaler Hinsicht äquivalent zu [Url.Action](xref:System.Web.Mvc.UrlHelper.Action*) und [Url.Page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*), bieten aber zusätzliche Flexibilität und Optionen.

Die `GetPath*`-Methoden sind `Url.Action` und `Url.Page` in der Hinsicht ähnlich, dass sie einen URI mit einem absoluten Pfad generieren. Die `GetUri*`-Methoden generieren immer einen absoluten URI mit einem Schema und einem Host. Die Methoden, die einen `HttpContext` akzeptieren, generieren im Kontext der ausgeführten Anforderung einen URI. Die [Umgebungsroutenwerte](#ambient), der URL-basierte Pfad, das Schema und der Host von der ausführenden Anforderung werden so lange verwendet, bis sie außer Kraft gesetzt werden.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> wird mit einer Adresse aufgerufen. Ein URI wird in zwei Schritten generiert:

1. Eine Adresse wird an eine Liste von Endpunkten gebunden, die der Adresse zugeordnet werden können.
1. Jedes <xref:Microsoft.AspNetCore.Routing.RouteEndpoint.RoutePattern> eines Endpunkts wird bewertet, bis ein Routenmuster gefunden wird, das den angegebenen Werten zugeordnet werden kann. Die daraus resultierende Ausgabe wird mit URI-Teilen kombiniert, die für die API zur Linkgenerierung bereitgestellt wird, und zurückgegeben.

Die von <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bereitgestellten Methoden unterstützen die Standardfunktionen zur Generierung von Links für jeden beliebigen Adresstypen. Am praktischsten ist es, die API zur Linkgenerierung mit Erweiterungsmethoden zu verwenden, die Vorgänge für einen bestimmten Adresstypen ausführen:

| Erweiterungsmethode | Beschreibung |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generiert einen URI mit einem absoluten Pfad, der auf den angegebenen Werten basiert. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generiert einen absoluten URI, der auf den angegebenen Werten basiert.             |

> [!WARNING]
> Beachten Sie die folgenden Implikationen zum Aufrufen von <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-Methoden:
>
> * Verwenden Sie `GetUri*`-Erweiterungsmethoden in App-Konfigurationen, die den `Host`-Header von eingehenden Anforderungen nicht überprüfen, mit Bedacht. Wenn der `Host`-Header von eingehenden Anforderungen nicht überprüft wird, können nicht vertrauenswürdige Anforderungseingaben zurück an den Client in URIs einer Ansicht bzw. Seite zurückgesendet werden. Es wird empfohlen, dass alle Produktions-Apps ihren Server so konfigurieren, dass der `Host`-Header auf bekannte gültige Werte überprüft wird.
>
> * Verwenden Sie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> in Kombination mit `Map` oder `MapWhen` in Middleware mit Bedacht. `Map*` ändert den Basispfad der ausgeführten Anforderung. Dies beeinflusst die Ausgabe der Linkgenerierung. Für alle <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-APIs ist die Angabe eines Basispfads zulässig. Geben Sie einen leeren Basispfad an, um die Auswirkungen von `Map*` auf die Linkgenerierung rückgängig zu machen.

### <a name="middleware-example"></a>Middlewarebeispiel

Im folgenden Beispiel verwendet eine Middleware die <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-API, um eine Verknüpfung zu einer Aktionsmethode herzustellen, die Speicherprodukte aufführt. Sie können für jede beliebige Klasse in einer App die API zur Linkgenerierung verwenden, indem Sie diese in eine Klasse einfügen und `GenerateLink` aufrufen:

[!code-csharp[](routing/samples/3.x/RoutingSample/Middleware/ProductsLinkMiddleware.cs?name=snippet)]

<a name="rtr"></a>

## <a name="route-template-reference"></a>Referenz für Routenvorlagen

Token in `{}` definieren Routenparameter, die beim Abgleich der Route gebunden werden. In einem Routensegment können mehrere Routenparameter definiert werden, müssen aber durch einen Literalwert getrennt werden. `{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet.  Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen.

Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen. Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads. Damit das Trennzeichen (`{` oder `}`) der Routenparameter-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein, was einem Escapezeichen entspricht. Beispielsweise `{{` oder `}}`.

Sternchen `*` oder Doppelsternchen `**`:

* Können als Präfix für einen Routenparameter verwendet werden, um eine Bindung zum verbleibenden Teil des URI herzustellen.
* Werden **Catch-All**-Parameter genannt. Zum Beispiel `blog/{**slug}`:
  * Entspricht einem beliebigen URI, der mit `/blog` beginnt und einem beliebigen Wert folgt.
  * Der Wert nach `/blog` wird dem [Slug](https://developer.mozilla.org/docs/Glossary/Slug)-Routenwert zugewiesen.

[!INCLUDE[](~/includes/catchall.md)]

Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.

Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen (`/`) zu generieren. Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }``foo/my%2Fpath`. Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen. Um Trennzeichen für Roundtrips einsetzen zu können, verwenden Sie das Routenparameterpräfix `**`. Die Route `foo/{**path}` mit `{ path = "my/path" }` generiert `foo/my/path`.

Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen. Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich. Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben. Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Übereinstimmung ermittelt, da der nachstehende Punkt (`.`) optional ist. Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:

* `/files/myFile.txt`
* `/files/myFile`

Routenparameter können über mehrere **Standardwerte** verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden. Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert. Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist. Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird. Beispielsweise `id?`. Zwischen optionalen Werten und Standardroutenparametern besteht folgender Unterschied:

* Ein Routenparameter mit einem Standardwert erzeugt immer einen Wert.
* Ein optionaler Parameter hat nur dann einen Wert, wenn ein Wert von der Anforderungs-URL bereitgestellt wird.

Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen. Eine Inline-Einschränkung für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen. Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen. Mehrere *Inline-Einschränkungen* können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden.

Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann. In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt. Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Routenparameter können darüber hinaus über Parametertransformatoren verfügen. Diese wandeln den Wert eines Parameters beim Generieren von Links um und passen Aktionen und Seiten an URLs an. Wie Einschränkungen können auch Parametertransformatoren einem Routenparameter inline hinzugefügt werden, indem ein Doppelpunkt (`:`) und der Name des Transformators hinter dem Namen des Routenparameters hinzugefügt werden. In der Routenvorlage `blog/{article:slugify}` wird beispielsweise der Transformator `slugify` festgelegt. Weitere Informationen zu Parametertransformatoren finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).

Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten:

| Routenvorlage                           | Beispiel-URI für Übereinstimmung    | Der Anforderungs-URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt.                                     |
| `{Page=Home}`                            | `/`                     | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Home` festgelegt.                                         |
| `{Page=Home}`                            | `/Contact`              | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Contact` festgelegt.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Stimmt mit dem `Products`-Controller und der `List`-Aktion überein.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Stimmt mit dem `Products`-Controller und der `Details`-Aktion (mit `id` auf 123 festgelegt) überein. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Stimmt mit dem `Home`-Controller und der `Index`-Methode überein. `id` wird ignoriert.        |
| `{controller=Home}/{action=Index}/{id?}` | `/Products`         | Stimmt mit dem `Products`-Controller und der `Index`-Methode überein. `id` wird ignoriert.        |

Mit Vorlagen lässt sich Routing besonders leicht durchführen. Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.

### <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente werden von rechts nach links auf eine [nicht gierige](#greedy) Weise durch entsprechende Literaltrennzeichen verarbeitet. Beispielsweise ist `[Route("/a{b}c{d}")]` ein komplexes Segment.
Komplexe Segmente funktionieren auf eine bestimmte Weise, die für eine erfolgreiche Verwendung verstanden werden muss. Das Beispiel in diesem Abschnitt zeigt, warum komplexe Segmente nur dann wirklich gut funktionieren, wenn der Trennzeichentext nicht innerhalb der Parameterwerte erscheint. Für komplexere Fälle ist die Verwendung eines [RegEx](/dotnet/standard/base-types/regular-expressions) und das anschließende manuelle Extrahieren der Werte erforderlich.

[!INCLUDE[](~/includes/regex.md)]

Dies ist eine Zusammenfassung der Schritte, die beim Routing mit der Vorlage `/a{b}c{d}` und dem URL-Pfad `/abcd` ausgeführt werden. `|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen:

* Das erste Literal von rechts nach links ist `c`. Daher wird `/abcd` von rechts durchsucht und `/ab|c|d` gefunden.
* Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen.
* Das nächste Literal von rechts nach links ist `a`. Also wird `/ab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/|a|b|c|d` gefunden.
* Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen.
* Es ist kein verbleibender Text und keine verbleibende Routenvorlage vorhanden. Folglich ist dies eine Übereinstimmung.

Nachfolgend ist ein Beispiel für einen negativen Fall mit derselben Vorlage `/a{b}c{d}` und dem URL-Pfad `/aabcd`. `|` wird verwendet, um die Funktionsweise des Algorithmus besser zu veranschaulichen: Bei diesem Fall handelt es sich nicht um eine Übereinstimmung, was durch denselben Algorithmus belegt wird:
* Das erste Literal von rechts nach links ist `c`. Daher wird `/aabcd` von rechts durchsucht und `/aab|c|d` gefunden.
* Alles auf der rechten Seite (`d`) ist jetzt mit dem Routenparameter `{d}` abgeglichen.
* Das nächste Literal von rechts nach links ist `a`. Also wird `/aab|c|d` dort gesucht, wo die Suche unterbrochen wurde, und dann wird `a` in `/a|a|b|c|d` gefunden.
* Der Wert auf der rechten Seite (`b`) ist jetzt mit dem Routenparameter `{b}` abgeglichen.
* Zu diesem Zeitpunkt gibt es noch verbleibenden Text `a`, aber es gibt keine Routenvorlage mehr, die der Algorithmus analysieren kann, weshalb dies keine Übereinstimmung ist.

Da der übereinstimmende Algorithmus [nicht gierig](#greedy) ist:

* Entspricht er der kleinstmöglichen Textmenge in jedem Schritt.
* Alle Fälle, in denen der Trennzeichenwert in den Parameterwerten angezeigt wird, stimmen nicht überein.

Reguläre Ausdrücke bieten eine viel bessere Kontrolle über das Abgleichsverhalten.

<a name="greedy"></a>

Beim gierigen Abgleich, auch als [Lazy Matching](https://wikipedia.org/wiki/Regular_expression#Lazy_matching) bezeichnet, wird die größtmögliche Zeichenfolge abgeglichen. Beim nicht gierigen Abgleich ist dies die kürzeste Zeichenfolge.

## <a name="route-constraint-reference"></a>Referenz für Routeneinschränkungen

Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird. In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird anhand einer True/False-Entscheidung bestimmt, ob der Wert gültig ist. Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist. <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen. Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet.

> [!WARNING]
> Verwenden Sie keine Einschränkungen für die Eingabeüberprüfung. Wenn Einschränkungen für die Eingabevalidierung verwendet werden, führt eine ungültige Eingabe zu einem `404`-Fehler (Nicht gefunden). Eine ungültige Eingabe sollte zu einer ungültigen Anforderung (`400`) mit einer entsprechenden Fehlermeldung führen. Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnliche Routen zu unterscheiden.

In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschrieben:

| Einschränkung | Beispiel | Beispiele für Übereinstimmungen | Hinweise |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Für jeden Integer wird eine Übereinstimmung ermittelt. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Entspricht `true` oder `false`. Groß-/Kleinschreibung nicht beachten |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Entspricht einem gültigen `DateTime`-Wert in der invarianten Kultur. Siehe vorherige Warnung. |
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Entspricht einem gültigen `decimal`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `double`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `float`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638` | Für einen gültigen `Guid`-Wert wird eine Übereinstimmung ermittelt. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Für einen gültigen `long`-Wert wird eine Übereinstimmung ermittelt. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens eine Länge von 4 Zeichen aufweisen. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Die Zeichenfolge darf maximal eine Länge von 8 Zeichen aufweisen. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau 12 Zeichen aufweisen. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge muss mindestens eine Länge von 8 und darf maximal eine Länge von 16 Zeichen aufweisen. |
| `min(value)` | `{age:min(18)}` | `19` | Der Integerwert muss mindestens 18 sein. |
| `max(value)` | `{age:max(120)}` | `91` | Der Integerwert darf nicht größer als 120 sein. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Der Integerwert muss zwischen 18 und 120 liegen. |
| `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem alphabetische Zeichen bestehen, `a`-`z` und ohne Unterscheidung zwischen Groß-/Kleinbuchstaben. |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss mit dem regulären Ausdruck übereinstimmen. Weitere Informationen finden Sie unter Tipps zum Definieren eines regulären Ausdrucks. |
| `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss. |

[!INCLUDE[](~/includes/regex.md)]

Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden. Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden, wird immer die invariante Kultur verwendet. Dies gilt z. B. für die Konvertierung in den CLR-Typ `int` oder `DateTime`. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist. Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind. Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert. Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.

### <a name="regular-expressions-in-constraints"></a>Reguläre Ausdrücke in Einschränkungen

[!INCLUDE[](~/includes/regex.md)]

Reguläre Ausdrücke können mithilfe der `regex(...)`-Routeneinschränkung als Inline-Einschränkungen angegeben werden. Methoden der <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>-Familie akzeptieren auch ein Objektliteral von Einschränkungen. Wenn dieses Formular verwendet wird, werden Zeichenfolgenwerte als reguläre Ausdrücke interpretiert.

Der folgende Code verwendet eine Inline-RegEx-Einschränkung:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex.cs?name=snippet)]

Der folgende Code verwendet ein Objektliteral, um eine RegEx-Einschränkung anzugeben:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRegex2.cs?name=snippet)]

Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt. Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.

In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden. Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden. Wenn Sie den regulären Ausdruck `^\d{3}-\d{2}-\d{4}$` in einer Inline-Einschränkung verwenden möchten, nutzen Sie eine der folgenden Optionen:

* Ersetzen Sie `\`-Zeichen in der Zeichenfolge durch `\\`-Zeichen in der C#-Quelldatei, um das Escapezeichen für die Zeichenfolge `\` zu setzen.
* [Ausführliche Zeichenfolgeliterale](/dotnet/csharp/language-reference/keywords/string)

Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (z. B. `{{`, `}}`, `[[`, `]]`). In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind:

| Regulärer Ausdruck    | Mit Escapezeichen versehener regulärer Ausdruck     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Beim Routing verwendete reguläre Ausdrücke beginnen oft mit dem `^`-Zeichen und stellen die Startposition der Zeichenfolge dar. Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar. Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt. Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist. In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt:

| expression   | Zeichenfolge    | Match | Kommentar               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | mz        | Ja   | Ausdruck stimmt überein    |
| `[a-z]{2}`   | MZ        | Ja   | keine Unterscheidung zwischen Groß-/Kleinbuchstaben    |
| `^[a-z]{2}$` | hello     | Nein    | siehe Erläuterungen zu `^` und `$` oben |
| `^[a-z]{2}$` | 123abc456 | Nein    | siehe Erläuterungen zu `^` und `$` oben |

Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken. Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen. Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis. Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden als reguläre Ausdrücke behandelt. Einschränkungen, die innerhalb einer Vorlage übergeben werden und mit keiner vorgegebenen Einschränkung übereinstimmen, werden nicht als reguläre Ausdrücke behandelt.

### <a name="custom-route-constraints"></a>Benutzerdefinierte Routeneinschränkungen

Benutzerdefinierte Routeneinschränkungen können durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden. Die `IRouteConstraint`-Schnittstelle umfasst die <xref:System.Web.Routing.IRouteConstraint.Match*>-Methode, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`.

Benutzerdefinierte Routeneinschränkungen werden nur selten benötigt. Bevor Sie eine benutzerdefinierte Routeneinschränkung implementieren, sollten Sie Alternativen in Betracht ziehen, wie z. B. Modellbindung.

Der ASP.NET Core-Ordner [Constraints](https://github.com/dotnet/aspnetcore/tree/master/src/Http/Routing/src/Constraints) bietet nützliche Beispiele für die Erstellung einer Einschränkung. Beispiel: [GuidRouteConstraint](https://github.com/dotnet/aspnetcore/blob/master/src/Http/Routing/src/Constraints/GuidRouteConstraint.cs#L18).

Zum Verwenden eines benutzerdefinierten `IRouteConstraint`-Elements muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap>-Eigenschaft der App im Dienstcontainer registriert werden. Eine `ConstraintMap` ist ein Wörterbuch, das Routeneinschränkungsschlüssel `IRouteConstraint`-Implementierungen zuordnet, die diese Einschränkungen überprüfen. Die `ConstraintMap` einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden. Zum Beispiel:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet)]

Die vorangehende Einschränkung wird im folgenden Code angewendet:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet&highlight=6,13)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

Die Implementierung von `MyCustomConstraint` verhindert die Anwendung von `0` auf einen Routenparameter:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

Der vorangehende Code:

* Verhindert, dass `0` im `{id}`-Segment der Route vorhanden ist.
* Dient als einfaches Beispiel für die Implementierung einer benutzerdefinierten Einschränkung. Es sollte nicht in einer Produktions-App eingesetzt werden.

Der folgende Code bietet einen besseren Ansatz, um zu verhindern, dass eine `id` mit einer `0` verarbeitet wird:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/TestController.cs?name=snippet2)]

Der vorangehende Code bietet im Vergleich zum `MyCustomConstraint`-Ansatz folgende Vorteile:

* Eine benutzerdefinierte Einschränkung ist nicht erforderlich.
* Es wird ein beschreibender Fehler zurückgegeben, wenn der Routenparameter `0` enthält.

## <a name="parameter-transformer-reference"></a>Parametertransformatorreferenz

Parametertransformatoren:

* Werden beim Generieren eines Links mit <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ausgeführt.
* Implementieren Sie <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer?displayProperty=fullName>.
* Werden mithilfe von <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> konfiguriert.
* Nehmen den Routenwert des Parameters an und transformieren ihn in einen neuen Zeichenfolgenwert.
* Haben die Verwendung des transformierten Wert in einem generierten Link zur Folge.

Beispielsweise generiert ein benutzerdefinierter Parametertransformator `slugify` im Routenmuster `blog\{article:slugify}` mit `Url.Action(new { article = "MyTestArticle" })``blog\my-test-article`.

Betrachten Sie die folgende Implementierung von `IOutboundParameterTransformer`:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet2)]

Um einen Parametertransformator in einem Routenmuster zu verwenden, konfigurieren Sie ihn mit <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupConstraint2.cs?name=snippet)]

Das ASP.NET Core-Framework verwendet Parametertransformatoren, um den URI zu transformieren, zu dem ein Endpunkt aufgelöst wird. Beispielsweise wandeln Parametertransformatoren die Routenwerte um, die zum Zuordnen folgender Elemente verwendet werden: `area`, `controller`, `action` und `page`.

```csharp
routes.MapControllerRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Mit der vorstehenden Routenvorlage wird die Aktion `SubscriptionManagementController.GetAll` dem URI `/subscription-management/get-all` zugeordnet. Ein Parametertransformator ändert nicht die zum Generieren eines Links verwendeten Routenwerte. Beispielsweise gibt `Url.Action("GetAll", "SubscriptionManagement")``/subscription-management/get-all` aus.

ASP.NET Core bietet API-Konventionen für die Verwendung von Parametertransformatoren mit generierten Routen:

* Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention?displayProperty=fullName>-Konvention wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an. Der Parametertransformator transformiert Attributroutentoken, wenn diese ersetzt werden. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention> Pages verwendet die Razor-API-Konvention. Diese Konvention wendet einen angegebenen Parametertransformator auf alle automatisch erkannten Razor Pages-Seiten an. Der Parametertransformator transformiert die Ordner- und Dateinamensegmente von Razor Pages-Routen. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen von Seitenrouten](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

<a name="ugr"></a>

## <a name="url-generation-reference"></a>Referenz für URL-Generierung

Dieser Abschnitt enthält eine Referenz für den Algorithmus, der durch die URL-Generierung implementiert wird. In der Praxis werden bei den meisten komplexen Beispielen für die URL-Generierung Controller oder Razor Pages verwendet. Weitere Informationen finden Sie unter [Routing in Controllern](xref:mvc/controllers/routing) (Routing in Controllern).

Die URL-Generierung beginnt mit einem Aufruf von [LinkGenerator.GetPathByAddress](xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*) oder einer ähnlichen Methode. Die Methode wird mit einer Adresse, mehreren Routenwerten und optional mit Informationen zur aktuellen Anforderung von `HttpContext` versehen.

Im ersten Schritt wird die Adresse verwendet, um bestimmte Endpunktkandidaten mithilfe einer [`IEndpointAddressScheme<TAddress>`](xref:Microsoft.AspNetCore.Routing.IEndpointAddressScheme`1)-Schnittstelle aufzulösen, die dem Adresstyp entspricht.

Sobald eine Kandidatengruppe anhand des Adressschemas gefunden wurde, werden die Endpunkte geordnet und iterativ verarbeitet, bis die URL-Generierung erfolgreich abgeschlossen ist. Bei der URL-Generierung wird **nicht** auf Mehrdeutigkeiten geprüft, daher ist das erste zurückgegebene Ergebnis das Endergebnis.

### <a name="troubleshooting-url-generation-with-logging"></a>Behandeln von Problemen mit der Protokollierung bei der URL-Generierung

Der erste Schritt bei der Behebung von Problemen bei der URL-Generierung ist die Einstellung des Protokolliergrads von `Microsoft.AspNetCore.Routing` auf `TRACE`. `LinkGenerator` protokolliert viele Details über die Verarbeitung, die bei der Problembehebung nützlich sein können.

Ausführliche Informationen zur URL-Generierung finden Sie unter [Referenz für URL-Generierung](#ugr).

### <a name="addresses"></a>Adressen

Mithilfe von Adressen wird bei der URL-Generierung ein Aufruf in der API zur Linkgenerierung an mehrere Endpunktkandidaten gebunden.

Adressen sind ein erweiterbares Konzept, das standardmäßig mit zwei Implementierungen bereitgestellt wird:

* Die Verwendung von *Endpunktname* (`string`) als Adresse:
    * Bietet ähnliche Funktionalität wie der Routenname von MVC.
    * Wird der <xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata>-Metadatentyp verwendet.
    * Löst die bereitgestellte Zeichenfolge anhand der Metadaten aller registrierten Endpunkte auf.
    * Löst beim Start eine Ausnahme aus, wenn mehrere Endpunkte den gleichen Namen aufweisen.
    * Wird für die allgemeine Verwendung außerhalb von Controllern und Razor Pages empfohlen.
* Die Verwendung von *Routenwerten* (<xref:Microsoft.AspNetCore.Routing.RouteValuesAddress>) als Adresse:
    * Bietet eine ähnliche Funktionalität wie die veraltete Funktion zur URL-Generierung von Controllern und Razor Pages.
    * Lässt sich nur schwer erweitern und debuggen.
    * Bietet die Implementierung, die von `IUrlHelper`, Taghilfsprogrammen, HTML-Hilfsprogrammen, Aktionsergebnissen usw. verwendet wird.

Aufgabe des Adressschemas ist es, die Verbindung zwischen der Adresse und den übereinstimmenden Endpunkten anhand von beliebigen Kriterien herzustellen:

* Das Schema für Endpunktenamen führt eine allgemeine Wörterbuchsuche durch.
* Das Schema der Routenwerte weist eine komplexe beste Teilmenge des Mengenalgorithmus auf.

<a name="ambient"></a>

### <a name="ambient-values-and-explicit-values"></a>Umgebungswerte und explizite Werte

Aus der aktuellen Anforderung greift das Routing auf die Routenwerte der aktuellen Anforderung `HttpContext.Request.RouteValues` zu. Die mit der aktuellen Anforderung verbundenen Werte werden als **Umgebungswerte** bezeichnet. Aus Gründen der Übersichtlichkeit werden in der Dokumentation die an die Methoden übergebenen Routenwerte als **explizite Werte** bezeichnet.

Das folgende Beispiel zeigt Umgebungswerte und explizite Werte. Er liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet)]

Der vorangehende Code:

* Gibt `/Widget/Index/17` zurück.
* Ruft <xref:Microsoft.AspNetCore.Routing.LinkGenerator> über [DI](xref:fundamentals/dependency-injection) ab.

Der folgende Code liefert keine Umgebungswerte und keine expliziten Werte: `{ controller = "Home", action = "Subscribe", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet2)]

Die vorhergehende Methode gibt `/Home/Subscribe/17` zurück

Der folgende Code in `WidgetController` gibt `/Widget/Subscribe/17` zurück:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/WidgetController.cs?name=snippet3)]

Der folgende Code stellt den Controller aus den Umgebungswerten in der aktuellen Anforderung und explizite Werte dar: `{ action = "Edit", id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/GadgetController.cs?name=snippet)]

Für den Code oben gilt:

* `/Gadget/Edit/17` wird zurückgegeben.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Url> ruft die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle ab.
* <xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*>   
generiert eine URL mit einem absoluten Pfad für eine Aktionsmethode. Die URL enthält den angegebenen `action`-Namen und `route`-Werte.

Sie liefert Umgebungswerte aus der aktuellen Anforderung und explizite Werte: `{ page = "./Edit, id = 17, }`:

[!code-csharp[](routing/samples/3.x/RoutingSample/Pages/Index.cshtml.cs?name=snippet)]

Im vorangehenden Code wird `url` auf `/Edit/17` festgelegt, wenn die Option zum Bearbeiten der Razor Page die folgende Seitenanweisung enthält:

 `@page "{id:int}"`

Wenn die Routenvorlage `"{id:int}"` nicht in der Seite „Bearbeiten“ enthalten ist, ist `url` gleich `/Edit?id=17`.

Das Verhalten der <xref:Microsoft.AspNetCore.Mvc.IUrlHelper>-Schnittstelle von MVC fügt zusätzlich zu den hier beschriebenen Regeln eine weitere Komplexitätsebene hinzu:

* `IUrlHelper` liefert immer die Routenwerte aus der aktuellen Anforderung als Umgebungswerte.
* [IUrlHelper.action](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Action*) kopiert immer die aktuellen Routenwerte `action` und `controller` als explizite Werte, sofern sie nicht vom Entwickler außer Kraft gesetzt werden.
* [IUrlHelper.page](xref:Microsoft.AspNetCore.Mvc.UrlHelperExtensions.Page*) kopiert immer den aktuellen Routenwert `page` als expliziten Wert, sofern er nicht außer Kraft gesetzt wird. <!--by the user-->
* `IUrlHelper.Page` setzt immer den aktuellen Routenwert `handler` mit `null` als expliziten Wert außer Kraft, sofern er nicht außer Kraft gesetzt wird.

Benutzer sind oft von den Verhaltensdetails der Umgebungswerte überrascht, da MVC anscheinend nicht den eigenen Regeln folgt. Aus Verlaufs- und Kompatibilitätsgründen weisen bestimmte Routenwerte wie `action`, `controller`, `page` und `handler` ein spezielles Verhalten auf.

Die äquivalente Funktionalität, die durch `LinkGenerator.GetPathByAction` und `LinkGenerator.GetPathByPage` bereitgestellt wird, verdoppelt diese Anomalien von `IUrlHelper` aus Kompatibilitätsgründen.

### <a name="url-generation-process"></a>URL-Generierungsprozess

Sobald die Gruppe der Endpunktkandidaten ermittelt ist, wird der URL-Generierungsalgorithmus angewendet:

* Die Endpunkte werden iterativ verarbeitet.
* Das erste erfolgreiche Ergebnis wird zurückgegeben.

Der erste Schritt in diesem Prozess wird als **Routenwertinvalidierung** bezeichnet.  Die Routenwertinvalidierung ist der Prozess, bei dem das Routing entscheidet, welche Routenwerte aus den Umgebungswerten verwendet und welche ignoriert werden sollen. Jeder Umgebungswert wird berücksichtigt und entweder mit den expliziten Werten kombiniert oder aber ignoriert.

Denken Sie daran, dass Umgebungswerte Anwendungsentwicklern in allgemeinen Fällen das Schreiben von Code sparen können. In der Regel sind die Szenarios, in denen Umgebungswerte hilfreich sind, mit MVC verknüpft:

* Bei der Verknüpfung mit einer anderen Aktion im gleichen Controller muss der Controllername nicht angegeben werden.
* Bei der Verknüpfung mit einem anderen Controller im gleichen Bereich muss der Bereich nicht angegeben werden.
* Bei der Verknüpfung mit der gleichen Aktionsmethode müssen keine Routenwerte angegeben werden.
* Bei der Verknüpfung mit einem anderen Teil der App sollen keine Routenwerte übertragen werden, die für diesen Teil der App irrelevant sind.

Aufrufe an `LinkGenerator` oder `IUrlHelper`, die `null` zurückgeben, sind meist dadurch bedingt, dass die Routenwertinvalidierung nicht verstanden wurde. Beheben Sie die Routenwertinvalidierung, indem Sie explizit mehr Routenwerte angeben, um zu prüfen, ob das Problem dadurch gelöst wird.

Bei der Routenwertinvalidierung wird davon ausgegangen, dass das URL-Schema der Anwendung hierarchisch ist, mit einer von links nach rechts gebildeten Hierarchie. Sehen Sie sich die einfache Controllerroutenvorlage `{controller}/{action}/{id?}` an, um ein Gespür dafür zu bekommen, wie dies in der Praxis funktioniert. Durch eine **Änderung** auf einen Wert werden alle rechts angezeigten Routenwerte **ungültig**. Dies spricht für die These von der Hierarchie. Wenn die App einen Umgebungswert für `id` hat und der Vorgang einen anderen Wert für `controller` angibt:

* `id` wird nicht wiederverwendet, weil `{controller}` links von `{id?}` steht.

Einige Beispiele veranschaulichen dieses Prinzip:

* Wenn die expliziten Werte einen Wert für `id` enthalten, wird der Umgebungswert für `id` ignoriert. Die Umgebungswerte für `controller` und `action` können verwendet werden.
* Wenn die expliziten Werte einen Wert für `action` enthalten, wird jeder Umgebungswert für `action` ignoriert. Die Umgebungswerte für `controller` können verwendet werden. Wenn sich der explizite Wert für `action` von dem Umgebungswert für `action` unterscheidet, wird der Wert `id` nicht verwendet.  Wenn der explizite Wert für `action` mit dem Umgebungswert für `action` übereinstimmt, kann der Wert `id` verwendet werden.
* Wenn die expliziten Werte einen Wert für `controller` enthalten, wird jeder Umgebungswert für `controller` ignoriert. Wenn sich der explizite Wert für `controller` von dem Umgebungswert für `controller` unterscheidet, werden die Werte `action` und `id` nicht verwendet. Wenn der explizite Wert für `controller` mit dem Umgebungswert für `controller` übereinstimmt, können die Werte `action` und `id` verwendet werden.

Dieser Prozess wird zusätzlich durch die vorhandenen Attributrouten und dedizierten konventionellen Routen erschwert. Konventionelle Routen des Controllers wie `{controller}/{action}/{id?}` legen eine Hierarchie mithilfe von Routenparametern fest. Bei [bestimmten konventionellen Routen](xref:mvc/controllers/routing#dcr) und [Attributrouten](xref:mvc/controllers/routing#ar) zu Controllern und Razor Pages:

* Gibt es eine Hierarchie für Routenwerte.
* Werden diese nicht in der Vorlage angezeigt.

Für diese Fälle definiert die URL-Generierung das Konzept der **erforderlichen Werte**. Bei Endpunkten, die von Controllern und Razor Pages erstellt wurden, sind erforderliche Werte angegeben, die eine Routenwertinvalidierung ermöglichen.

Der Algorithmus der Routenwertinvalidierung im Detail:

* Die erforderlichen Wertnamen werden mit den Routenparametern kombiniert und dann von links nach rechts verarbeitet.
* Für jeden Parameter werden der Umgebungswert und der explizite Wert verglichen:
    * Wenn der Umgebungswert und der explizite Wert gleich sind, wird der Prozess fortgesetzt.
    * Wenn der Umgebungswert vorhanden ist und der explizite Wert nicht, wird der Umgebungswert bei der URL-Generierung verwendet.
    * Wenn der Umgebungswert nicht vorhanden ist und der explizite Wert vorhanden ist, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte.
    * Wenn der Umgebungswert und der explizite Wert vorhanden und die beiden Werte unterschiedlich sind, verwerfen Sie den Umgebungswert und alle nachfolgenden Umgebungswerte.

An diesem Punkt ist der Vorgang zur URL-Generierung bereit, Routeneinschränkungen auszuwerten. Die akzeptierten Werte werden mit den Standardwerten der Parameter kombiniert, die für Einschränkungen bereitgestellt werden. Wenn alle Einschränkungen erfüllt sind, wird der Vorgang fortgesetzt.

Als Nächstes können die **akzeptierten Werte** verwendet werden, um die Routenvorlage zu erweitern. Die Routenvorlage wird verarbeitet:

* Von links nach rechts.
* Für jeden Parameter wird der akzeptierte Wert ersetzt.
* In den folgenden Sonderfällen:
  * Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter einen Standardwert hat, wird der Standardwert verwendet.
  * Wenn bei den akzeptierten Werten ein Wert fehlt und der Parameter optional ist, wird die Verarbeitung fortgesetzt.
  * Wenn irgendein Routenparameter rechts neben einem fehlenden optionalen Parameter einen Wert hat, schlägt der Vorgang fehl.
  * <!-- review default-valued parameters optional parameters --> Zusammenhängende Parameter mit Standardwerten und optionale Parameter werden, wenn möglich, reduziert dargestellt.

Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt. In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen:

| Umgebungswerte                     | Explizite Werte                        | Ergebnis                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

### <a name="problems-with-route-value-invalidation"></a>Probleme mit der Routenwertinvalidierung

Ab ASP.NET Core 3.0 funktionieren einige Schemas zur URL-Generierung, die in früheren ASP.NET Core-Versionen verwendet wurden, nicht mehr sehr gut. Das ASP.NET Core-Team plant, in einer zukünftigen Version Features hinzuzufügen, die diese Anforderungen erfüllen. Im Moment ist die beste Lösung die Verwendung von Legacy-Routingfunktionen.

Der folgende Code zeigt ein Beispiel für ein Schema zur URL-Generierung, das vom Routing nicht unterstützt wird.

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupUnsupported.cs?name=snippet)]

Im vorangehenden Code wird der `culture`-Routenparameter für die Lokalisierung verwendet. Ziel ist es, dass der `culture`-Parameter immer als Umgebungswert akzeptiert wird. Der `culture`-Parameter wird jedoch aufgrund der Art und Weise, wie die erforderlichen Werte funktionieren, nicht als Umgebungswert akzeptiert:

* In der `"default"`-Routenvorlage befindet sich der `culture`-Routenparameter links von `controller`, sodass `culture` durch Änderungen an `controller` nicht ungültig wird.
* In der `"blog"`-Routenvorlage wird der `culture`-Routenparameter rechts von `controller` betrachtet, der in den erforderlichen Werten aufgeführt ist.

## <a name="configuring-endpoint-metadata"></a>Konfigurieren von Endpunktmetadaten

Die folgenden Links enthalten Informationen zum Konfigurieren von Endpunktmetadaten:

* [Aktivieren von Cors mit Endpunktrouting](xref:security/cors#enable-cors-with-endpoint-routing)
* [IAuthorizationPolicyProvider-Beispiel](https://github.com/dotnet/AspNetCore/tree/release/3.1/src/Security/samples/CustomPolicyProvider) mit einem benutzerdefinierten `[MinimumAgeAuthorize]`-Attribut
* [Testen der Authentifizierung mit dem [Authorize]-Attribut](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [Auswählen des Schemas mit dem [Authorize]-Attribut](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [Anwenden von Richtlinien mithilfe des [Authorize]-Attributs](xref:security/authorization/policies#apply-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>Hostabgleich in Routen mit RequireHost

<xref:Microsoft.AspNetCore.Builder.RoutingEndpointConventionBuilderExtensions.RequireHost*> wendet eine Einschränkung auf die Route an, für die der angegebene Host erforderlich ist. Der `RequireHost`- oder [[Host]](xref:Microsoft.AspNetCore.Routing.HostAttribute)-Parameter kann wie folgt lauten:

* Host: `www.domain.com`, entspricht `www.domain.com` mit einem beliebigen Port.
* Host mit Platzhalter: `*.domain.com`, entspricht `www.domain.com`, `subdomain.domain.com` oder `www.subdomain.domain.com` an einem beliebigen Port.
* Port: `*:5000`, entspricht Port 5000 mit einem beliebigen Host.
* Host und Port: `www.domain.com:5000` oder `*.domain.com:5000`, entspricht dem Host und Port.

Es können mehrere Parameter mit `RequireHost` oder `[Host]` angegeben werden. Die Einschränkung gleicht die Hosts ab, die für einen der Parameter gültig sind. Beispielsweise entspricht `[Host("domain.com", "*.domain.com")]` `domain.com`, `www.domain.com` und `subdomain.domain.com`.

Im folgenden Code wird `RequireHost` verwendet, um den angegebenen Host auf der Route anzufordern:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupRequireHost.cs?name=snippet)]

Im folgenden Code wird das `[Host]`-Attribut für den Controller verwendet, um die einzelnen angegebenen Hosts anzufordern:

[!code-csharp[](routing/samples/3.x/RoutingSample/Controllers/ProductController.cs?name=snippet)]

Wenn das `[Host]`-Attribut sowohl auf die Controller- als auch auf die Aktionsmethode angewendet wird, trifft Folgendes zu:

* Das Attribut auf der Aktion wird verwendet.
* Das Controllerattribut wird ignoriert.

## <a name="performance-guidance-for-routing"></a>Leistungsleitfaden für das Routing

Der Großteil der Routingfunktionalität wurde in ASP.NET Core 3.0 aktualisiert, um die Leistung zu erhöhen.

Wenn eine App Leistungsprobleme hat, wird die Ursache häufig beim Routing vermutet. Das Routing wird deshalb in Betracht gezogen, weil Frameworks wie Controller und Razor Pages in ihren Protokollierungsmeldungen die innerhalb des Frameworks verbrachte Zeit angeben. Wenn es einen signifikanten Unterschied zwischen der von den Controllern gemeldeten Zeit und der Gesamtzeit der Anforderung gibt:

* Schließen Entwickler ihren App-Code als Ursache des Problems aus.
* Wird in der Regel angenommen, dass das Routing die Ursache für das Problem ist.

Die Leistung des Routings wird anhand von Tausenden von Endpunkten getestet. Es ist unwahrscheinlich, dass eine typische App auf ein Leistungsproblem stößt, nur weil diese zu umfangreich ist. Die häufigste Ursache für eine langsames Routing ist üblicherweise eine schlecht funktionierende benutzerdefinierte Middleware.

Das folgende Codebeispiel veranschaulicht eine grundlegende Technik zur Eingrenzung der Verzögerungsquelle:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupDelay.cs?name=snippet)]

Auf das Zeitrouting:

* Verschachteln Sie jede Middleware mit einer Kopie der im vorherigen Code gezeigten Zeitmiddleware.
* Fügen Sie einen eindeutigen Bezeichner hinzu, um die Zeitdaten mit dem Code zu korrelieren.

Dies ist ein einfacher Weg, um die Verzögerung zu verringern, wenn sie signifikant ist, zum Beispiel größer als `10ms`.  Wenn `Time 2` von `Time 1` subtrahiert wird, ergibt sich die in der `UseRouting`-Middleware benötigte Zeit.

Der folgende Code verwendet einen kompakteren Ansatz als der vorangegangene Zeitcode:

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippetSW)]

[!code-csharp[](routing/samples/3.x/RoutingSample/StartupSW.cs?name=snippet)]

### <a name="potentially-expensive-routing-features"></a>Potenziell teure Routingfeatures

Die folgende Liste gibt einen Einblick in Routingfeatures, die im Vergleich zu einfachen Routenvorlagen relativ teuer sind:

* Reguläre Ausdrücke: Mit einer kleinen Menge an Eingaben ist möglich, reguläre Ausdrücke zu schreiben, die komplex sind oder eine lange Ausführungszeit haben.

* Komplexe Segmente (`{x}-{y}-{z}`): 
  * Sind wesentlich teurer als das Analysieren eines regulären URL-Pfadsegments.
  * Führen dazu, dass viel mehr Teilzeichenfolgen zugeordnet werden.
  * Die komplexe Segmentlogik wurde beim Aktualisieren der ASP.NET Core 3.0-Routingleistung nicht aktualisiert.

* Synchroner Datenzugriff: Viele komplexe Apps verfügen im Rahmen Ihrer Routingfunktionen über Datenbankzugriff. Das Routing in ASP.NET Core 2.2 und früheren Versionen bietet möglicherweise nicht die richtigen Erweiterungspunkte zur Unterstützung des Routings für den Datenbankzugriff. Zum Beispiel sind <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> und <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> synchron. Erweiterbarkeitspunkte wie <xref:Microsoft.AspNetCore.Routing.MatcherPolicy> und <xref:Microsoft.AspNetCore.Routing.EndpointSelectorContext> sind asynchron.

## <a name="guidance-for-library-authors"></a>Leitfaden für Bibliotheksautoren

Dieser Abschnitt enthält Hinweise für Bibliotheksautoren, die auf dem Routing aufbauen. Diese Details sollen sicherstellen, dass App-Entwickler gute Erfahrungen mit Bibliotheken und Frameworks machen, die das Routing erweitern.

### <a name="define-endpoints"></a>Definieren von Endpunkten

Wenn Sie ein Framework erstellen möchten, das das Routing für den URL-Abgleich verwendet, sollten Sie zunächst eine Benutzeroberfläche definieren, die auf <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> aufbaut.

**Es wird empfohlen,** dass Sie auf <xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder> aufbauen. Auf diese Weise können Benutzer Ihr Framework mit anderen ASP.NET Core-Features problemlos zusammenstellen. Jede ASP.NET Core-Vorlage umfasst die Routingfunktionalität. Gehen Sie davon aus, dass das Routing vorhanden und den Benutzern vertraut ist.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...);

    endpoints.MapHealthChecks("/healthz");
});
```

**Es wird empfohlen,** dass Sie einen versiegelten konkreten Typ aus einem Aufruf an `MapMyFramework(...)` zurückgeben, der <xref:Microsoft.AspNetCore.Builder.IEndpointConventionBuilder> implementiert. Die meisten `Map...`-Methoden in Frameworks folgen diesem Muster. Die `IEndpointConventionBuilder`-Schnittstelle:

* Ermöglicht die Kombinierbarkeit von Metadaten.
* Wird von verschiedenen Erweiterungsmethoden angesteuert.

Wenn Sie Ihren eigenen Typ deklarieren, können Sie dem Generator Ihre eigene frameworkspezifische Funktionalität hinzufügen. Es ist in Ordnung, einen vom Framework deklarierten Generator zu umschließen und Aufrufe an ihn weiterzuleiten.

```csharp
app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization()
                                 .WithMyFrameworkFeature(awesome: true);

    endpoints.MapHealthChecks("/healthz");
});
```

**Ziehen Sie in Betracht,** Ihre eigene <xref:Microsoft.AspNetCore.Routing.EndpointDataSource>-Klasse zu schreiben. Die `EndpointDataSource`-Klasse vom primitiven Typ auf niedriger Ebene eignet sich zum Deklarieren und Aktualisieren einer Sammlung von Endpunkten. `EndpointDataSource` ist eine leistungsstarke API, die von Controllern und Razor Pages verwendet wird.

Die Routingtests haben ein [grundlegendes Beispiel](https://github.com/aspnet/AspNetCore/blob/master/src/Http/Routing/test/testassets/RoutingSandbox/Framework/FrameworkEndpointDataSource.cs#L17) für eine Datenquelle, die nicht aktualisiert wird.

**Versuchen Sie nicht,** eine `EndpointDataSource`-Klasse standardmäßig zu registrieren. Fordern Sie die Benutzer auf, Ihr Framework in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*> zu registrieren. Der Grundgedanke beim Routing ist, dass standardmäßig nichts enthalten ist, und dass die Endpunkte bei `UseEndpoints` registriert werden müssen.

### <a name="creating-routing-integrated-middleware"></a>Erstellen von in das Routing integrierter Middleware

**Ziehen Sie in Betracht,** Metadatentypen als Schnittstelle zu definieren.

**Ermöglichen Sie,** Metadatentypen als Attribut für Klassen und Methoden zu verwenden.

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet2)]

Frameworks wie Controller und Razor Pages unterstützen die Anwendung von Metadatenattributen auf Typen und Methoden. Für das Deklarieren von Metadatentypen gilt:

* Machen Sie diese als [Attribute](/dotnet/csharp/programming-guide/concepts/attributes/) zugänglich.
* Die meisten Benutzer sind mit der Anwendung von Attributen vertraut.

Durch die Deklaration eines Metadatentyps als Schnittstelle wird die Flexibilität zusätzlich erhöht:

* Schnittstelle können zusammengesetzt werden.
* Entwickler können ihre eigenen Typen deklarieren, die mehrere Richtlinien kombinieren.

**Ermöglichen Sie,** Metadaten außer Kraft zu setzen, wie in folgendem Beispiel gezeigt:

[!code-csharp[](routing/samples/3.x/RoutingSample/ICoolMetadata.cs?name=snippet)]

Die beste Möglichkeit, diese Richtlinien zu befolgen, besteht darin, die Definition von **Markermetadaten** zu vermeiden:

* Suchen Sie nicht nur nach dem Vorhandensein eines Metadatentyps.
* Definieren Sie eine Eigenschaft für die Metadaten, und überprüfen Sie die Eigenschaft.

Die Metadatensammlung ist geordnet und unterstützt das Außerkraftsetzen nach Priorität. Im Fall von Controllern sind Metadaten für die Aktionsmethode am spezifischsten.

**Nutzen Sie** Middleware mit und ohne Routing.

```csharp
app.UseRouting();

app.UseAuthorization(new AuthorizationPolicy() { ... });

app.UseEndpoints(endpoints =>
{
    // Your framework
    endpoints.MapMyFramework(...).RequireAuthorization();
});
```

Ein gutes Beispiel für diese Vorgabe ist die `UseAuthorization`-Middleware. Mithilfe der Autorisierungsmiddleware können Sie eine Fallbackrichtlinie einbauen. <!-- shown where?  (shown here) --> Die Fallbackrichtlinie gilt, falls angegeben, für beide:

* Endpunkte ohne angegebene Richtlinie.
* Anforderungen, die nicht mit einem Endpunkt übereinstimmen.

Dies macht die Autorisierungsmiddleware außerhalb des Routingkontexts sehr nützlich. Die Autorisierungsmiddleware kann für die traditionelle Middlewareprogrammierung verwendet werden.

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Beim Routing werden Anforderungs-URIs Endpunkten zugeordnet und Anforderungen an diese Endpunkte weitergeleitet. Routen werden in der App definiert und beim Start der App konfiguriert. Eine Route kann optional Werte aus der URL extrahieren, die in der Anforderung enthalten ist. Diese Werte können anschließend für die Verarbeitung der Anforderung verwendet werden. Mithilfe von Routeninformationen aus der App lassen sich durch das Routing URLs generieren, die Endpunkten zugeordnet werden.

Wenn Sie die aktuellsten Routingszenarios in ASP.NET Core 2.2 verwenden möchten, geben Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) zur Registrierung der MVC-Dienste in `Startup.ConfigureServices` an:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Die Option <xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> bestimmt, ob für das Routing intern endpunktbasierte Logik oder <xref:Microsoft.AspNetCore.Routing.IRouter>-basierte Logik von ASP.NET Core 2.1 oder früher verwendet werden soll. Wenn die Kompatibilitätsversion auf 2.2 oder höher festgelegt ist, lautet der Standardwert `true`. Legen Sie für den Wert `false` fest, um die vorherige Routinglogik zu verwenden:

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

Weitere Informationen zum <xref:Microsoft.AspNetCore.Routing.IRouter>-basierten Routing finden Sie in dem [Artikel zur Version 2.1 von ASP.NET Core](?view=aspnetcore-2.1).

> [!IMPORTANT]
> In diesem Artikel wird das Low-Level-Routing in ASP.NET Core beschrieben. Weitere Informationen zum Routing mit ASP.NET Core MVC finden Sie unter <xref:mvc/controllers/routing>. Weitere Informationen zu Routingkonventionen in Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Routinggrundlagen

Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten. Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:

* Sie unterstützt ein grundlegendes und beschreibendes Routingschema.
* Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.

Entwickler fügen in der Regel in speziellen Situationen durch [Attributrouting](xref:mvc/controllers/routing#attribute-routing) oder dedizierte konventionelle Routen zusätzliche kurze Routen zu stark frequentierten Bereichen der App hinzu. Beispiele für spezielle Situationen sind unter anderem Blog- und E-Commerce-Endpunkte.

Web-APIs sollten das Attributrouting verwenden, um die Funktionalität der App als einen Satz von Ressourcen zu modellieren, bei denen Vorgänge durch HTTP-Verben dargestellt werden. Dies bedeutet, dass viele Vorgänge, z. B. GET und POST, für dieselbe logische Ressource dieselbe URL verwenden. Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.

Razor Pages-Apps verwenden konventionelles Standardrouting, um benannte Ressourcen im *Pages*-Ordner einer App bereitzustellen. Außerdem gibt es weitere Konventionen zum Anpassen des Routingverhaltens für Razor Pages. Weitere Informationen finden Sie unter <xref:razor-pages/index> und <xref:razor-pages/razor-pages-conventions>.

Die Unterstützung zum Generieren von URLs ermöglicht es, die App ohne Hartcodierung der URLs zum Verlinken der App zu entwickeln. Auf diese Weise kann mit einer grundlegenden Routingkonfiguration begonnen werden, und die Routen können geändert werden, wenn das Ressourcenlayout der App festgelegt wurde.

Es werden *Endpunkte* (`Endpoint`) für das Routing verwendet, um logische Endpunkte in einer App darzustellen.

Ein Endpunkt definiert einen Delegaten zum Verarbeiten von Anforderungen und eine Sammlung von beliebigen Metadaten. Die Metadaten werden zur Implementierung von übergreifenden Belangen verwendet, die auf Richtlinien und der Konfiguration basieren, die den einzelnen Endpunkten angefügt sind.

Das Routingsystem weist die folgenden Eigenschaften auf:

* Die Syntax der Routenvorlage wird zum Definieren von Routen mit Routenparametern verwendet, die mit Token versehen sind.
* Sowohl die konventionelle Endpunktkonfiguration als auch die Endpunktkonfiguration mit Attributen ist zulässig.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> wird verwendet, um zu bestimmen, ob ein URL-Parameter einen gültigen Wert für eine bestimmte Endpunkteinschränkung enthält.
* App-Modelle wie MVC bzw. Razor Pages registrieren sämtliche Endpunkte, für die die Implementierung von Routingszenarios vorhersagbar ist.
* Die Routingimplementierung trifft Routingentscheidungen, wenn diese in der Middlewarepipeline gewünscht sind.
* Middleware, die nach Routingmiddleware angezeigt wird, kann das Ergebnis der Endpunktentscheidung der Routingmiddleware untersuchen, die für einen bestimmten Anforderungs-URI getroffen wurde.
* Alle diese Endpunkte können an einer beliebigen Stelle in der Middlewarepipeline in der App aufgelistet werden.
* Apps können mithilfe von Routing URLs generieren (z. B. für Umleitungen oder Links), die auf Endpunktinformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.
* Die URL-Generierung basiert auf Adressen, die beliebige Erweiterbarkeit unterstützen:

  * Die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) kann mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection) an jeder beliebigen Stelle aufgelöst werden, um URLs zu generieren.
  * Wenn diese API nicht über Dependency Injection verfügbar ist, bietet <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Methoden zum Erstellen von URLs.

> [!NOTE]
> Mit der Freigabe des Endpunktroutings in ASP.NET Core 2.2 wird die Endpunktverknüpfung auf MVC- bzw. Razor Pages-Aktionen und -Seiten eingeschränkt. Zukünftige Releases sollen Erweiterungen für Funktionen zur Verknüpfung von Endpunkten enthalten.

Die Routingfunktionalität wird über die Klasse <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> mit der [Middlewarepipeline](xref:fundamentals/middleware/index) verbunden. [ASP.NET Core-MVC](xref:mvc/overview) fügt im Rahmen der Konfiguration die Routingfunktionalität der Middlewarepipeline hinzu und verarbeitet das Routing in MVC- und Razor Pages-Apps. Informationen darüber, wie Sie Routing als eigenständige Komponente verwenden, finden Sie im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware).

### <a name="url-matching"></a>URL-Zuordnung

Bei einer URL-Zuordnung werden eingehende Anforderungen durch Routing an einen *Endpunkt* gesendet. Für diesen Prozess werden die Daten des URL-Pfads verwendet. Es können jedoch auch alle Daten der Anforderung genutzt werden. Für die Skalierung der Größe und Komplexität einer App ist das Versenden von Anforderungen an unterschiedliche Handler entscheidend.

Das Routingsystem ist beim Endpunktrouting für alle Weiterleitungsentscheidungen zuständig. Da die Middleware basierend auf dem ausgewählten Endpunkt Richtlinien anwendet, ist es wichtig, dass jede Entscheidung, die die Weiterleitung oder die Anwendung von Sicherheitsrichtlinien beeinträchtigen kann, innerhalb des Routingsystems getroffen wird.

Wenn der Endpunktdelegat ausgeführt wird, werden die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte festgelegt.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) ist ein Wörterbuch mit *Routenwerten*, das aus der Route erstellt wird. Die Werte werden in der Regel durch die Tokenisierung der URL ermittelt und können so verwendet werden, dass Benutzereingaben akzeptiert oder weitere Entscheidungen in der App zum Versenden von Anforderungen getroffen werden.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) ist eine Eigenschaftensammlung mit zusätzlichen Daten zur zugeordneten Route. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> werden bereitgestellt, damit sich jeder Route Zustandsdaten zuordnen lassen, sodass in der App später Entscheidungen auf Grundlage der zugeordneten Route getroffen werden können. Diese Werte werden vom Entwickler vorgegeben und beeinflussen das Routingverhalten **in keiner Weise**. Außerdem können im Gegensatz zu [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), die sich in bzw. aus Zeichenfolgen konvertieren lassen müssen, Werte in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) einem beliebigen Typ entsprechen.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) ist eine Liste der Routen, die an der Zuordnung der Anforderung beteiligt waren. Routen können in anderen Routen geschachtelt werden. Die <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>-Eigenschaft stellt den Pfad mithilfe der logischen Routenstruktur dar, die zu der Zuordnung geführt hat. Üblicherweise ist das erste Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> die Routensammlung. Dieses sollte zur URL-Generierung verwendet werden. Das letzte Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> ist der Routenhandler, für den eine Zuordnung vorgenommen wurde.

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>URL-Generierung mit LinkGenerator

Bei der URL-Generierung wird durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt. Dies ermöglicht eine logische Trennung zwischen den Endpunkten und den URLs, die auf diese zugreifen.

Das Endpunktrouting umfasst die API zur Linkgenerierung (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>). <xref:Microsoft.AspNetCore.Routing.LinkGenerator> ist ein Singletondienst, der über [DI](xref:fundamentals/dependency-injection) abgerufen werden kann. Die API kann außerhalb des Kontexts einer ausgeführten Anforderung verwendet werden. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> und Szenarios für MVC, die von <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> abhängig sind (z. B. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro), HTML-Hilfsprogramme und [Aktionsergebnisse](xref:mvc/controllers/actions)), verwenden die API zur Linkgenerierung, um entsprechende Funktionen bereitzustellen.

Die API zur Linkgenerierung wird von Konzepten wie *Adressen* und *Adressschemas* unterstützt. Sie können mithilfe eines Adressschemas die Endpunkte bestimmen, die bei der Linkgenerierung berücksichtigt werden sollen. Beispielsweise werden Routennamen und Routenwerte als Adressschemas implementiert. Diese Szenarios kennen viele Benutzer von MVC bzw. Razor Pages.

Die API zur Linkgenerierung kann MVC- bzw. Razor Pages-Aktionen und Seiten über die folgenden Erweiterungsmethoden miteinander verknüpfen:

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

Beim Überladen dieser Methoden werden Argumente akzeptiert, die den `HttpContext` umfassen. Diese Methoden sind zwar in funktionaler Hinsicht äquivalent zu `Url.Action` und `Url.Page`, bieten aber zusätzliche Flexibilität und Optionen.

Die `GetPath*`-Methoden sind `Url.Action` und `Url.Page` in der Hinsicht ähnlich, dass sie einen URI mit einem absoluten Pfad generieren. Die `GetUri*`-Methoden generieren immer einen absoluten URI mit einem Schema und einem Host. Die Methoden, die einen `HttpContext` akzeptieren, generieren im Kontext der ausgeführten Anforderung einen URI. Die Umgebungsroutenwerte, der URL-basierte Pfad, das Schema und der Host von der ausführenden Anforderung werden so lange verwendet, bis sie außer Kraft gesetzt werden.

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> wird mit einer Adresse aufgerufen. Ein URI wird in zwei Schritten generiert:

1. Eine Adresse wird an eine Liste von Endpunkten gebunden, die der Adresse zugeordnet werden können.
1. Jedes `RoutePattern` eines Endpunkts wird bewertet, bis ein Routenmuster gefunden wird, das den angegebenen Werten zugeordnet werden kann. Die daraus resultierende Ausgabe wird mit URI-Teilen kombiniert, die für die API zur Linkgenerierung bereitgestellt wird, und zurückgegeben.

Die von <xref:Microsoft.AspNetCore.Routing.LinkGenerator> bereitgestellten Methoden unterstützen die Standardfunktionen zur Generierung von Links für jeden beliebigen Adresstypen. Am praktischsten ist es, die API zur Linkgenerierung mit Erweiterungsmethoden zu verwenden, die Vorgänge für einen bestimmten Adresstypen ausführen.

| Erweiterungsmethode   | Beschreibung                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | Generiert einen URI mit einem absoluten Pfad, der auf den angegebenen Werten basiert. |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | Generiert einen absoluten URI, der auf den angegebenen Werten basiert.             |

> [!WARNING]
> Beachten Sie die folgenden Implikationen zum Aufrufen von <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-Methoden:
>
> * Verwenden Sie `GetUri*`-Erweiterungsmethoden in App-Konfigurationen, die den `Host`-Header von eingehenden Anforderungen nicht überprüfen, mit Bedacht. Wenn der `Host`-Header von eingehenden Anforderungen nicht überprüft wird, können nicht vertrauenswürdige Anforderungseingaben zurück an den Client in URIs einer Ansicht bzw. Seite zurückgesendet werden. Es wird empfohlen, dass alle Produktions-Apps ihren Server so konfigurieren, dass der `Host`-Header auf bekannte gültige Werte überprüft wird.
>
> * Verwenden Sie <xref:Microsoft.AspNetCore.Routing.LinkGenerator> in Kombination mit `Map` oder `MapWhen` in Middleware mit Bedacht. `Map*` ändert den Basispfad der ausgeführten Anforderung. Dies beeinflusst die Ausgabe der Linkgenerierung. Für alle <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-APIs ist die Angabe eines Basispfads zulässig. Geben Sie immer einen leeren Basispfad an, um den Einfluss von `Map*` auf die Linkgenerierung rückgängig zu machen.

## <a name="differences-from-earlier-versions-of-routing"></a>Unterschiede im Vergleich zu früheren Routingversionen

Es gibt einige Unterschiede zwischen dem Endpunktrouting in ASP.NET Core 2.2 oder höher und dem Routing in früheren Versionen von ASP.NET Core:

* Das Endpunktroutingsystem unterstützt die <xref:Microsoft.AspNetCore.Routing.IRouter>-basierte Erweiterbarkeit einschließlich des Erbens von <xref:Microsoft.AspNetCore.Routing.Route> nicht.

* Das Endpunktrouting unterstützt [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) nicht. Verwenden Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) für 2.1 (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`), um den Kompatibilitätsshim weiter zu verwenden.

* Das Verhalten des Endpunktroutings für die Schreibweise von generierten URIs ist bei der Verwendung von konventionellen Routen anders.

  Sehen Sie sich die folgende Standardvorlage für Routen an:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Angenommen, Sie generieren mithilfe der folgenden Route einen Link zu einer Aktion:

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  Bei <xref:Microsoft.AspNetCore.Routing.IRouter>-basierten Routing generiert dieser Code einen URI von `/blog/ReadPost/17`, der die Schreibweise des angegebenen Routenwerts berücksichtigt. Durch das Endpunktrouting in ASP.NET Core 2.2 oder höher wird `/Blog/ReadPost/17` hergestellt („Blog“ wird großgeschrieben). Über das Endpunktrouting wird die `IOutboundParameterTransformer`-Schnittstelle bereitgestellt, die verwendet werden kann, um dieses Verhalten entweder global anzupassen oder auf unterschiedliche Konventionen zum Zuordnen von URLs anzuwenden.

  Weitere Informationen finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).

* Das Verhalten der von MVC bzw. Razor Pages mit konventionellen Routen verwendeten Linkgenerierung unterscheidet sich, wenn versucht wird, einen Controller bzw. eine Aktion oder Seite zu verknüpfen, die nicht vorhanden ist.

  Sehen Sie sich die folgende Standardvorlage für Routen an:

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  Angenommen, Sie generieren mithilfe der Standardvorlage wie folgt einen Link zu einer Aktion:

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  Bei `IRouter`-basiertem Routing ist das Ergebnis immer `/Blog/ReadPost/17`, auch wenn `BlogController` nicht vorhanden ist oder keine Aktionsmethode für `ReadPost` aufweist. Erwartungsgemäß wird durch das Endpunktrouting in ASP.NET Core 2.2 oder höher `/Blog/ReadPost/17` erstellt, wenn die Aktionsmethode vorhanden ist. *Wenn die Aktionsmethode allerdings nicht vorhanden ist, wird beim Endpunktrouting eine leere Zeichenfolge erstellt.* Vom Konzept her wird beim Endpunktrouting nicht angenommen, dass der Endpunkt vorhanden ist, wenn es die Aktion nicht gibt.

* Das Verhalten des *Algorithmus zum Aufheben der Gültigkeit von Umgebunsgwerten* bei der Linkgenerierung ist anders, wenn dieser im Zusammenhang mit dem Endpunktrouting verwendet wird.

  Anhand des Algorithmus *zum Aufheben der Gültigkeit von Umgebungswerten* wird entschieden, welche Routenwerte der zu diesem Zeitpunkt ausgeführten Anforderung (die Umgebunsgwerte) für die Linkgenerierung verwendet werden können. Beim konventionellen Routing wurde stets die Gültigkeit von zusätzlichen Routenwerten aufgehoben, wenn eine Verknüpfung zu einer anderen Aktion hergestellt wurde. Beim Attributrouting ist dieses Verhalten vor dem Release von ASP.NET Core 2.2 nicht aufgetreten. In früheren Versionen von ASP.NET Core wurden beim Herstellen von Verknüpfungen mit einer anderen Aktion, die dieselben Namen für Routenparameter verwendete, ein Fehler bei der Linkgenerierung ausgelöst. In ASP.NET Core 2.2 oder höher wird bei beiden Arten des Routings die Gültigkeit von Werten aufgehoben, wenn eine Verknüpfung zu einer anderen Aktion hergestellt wird.

  Sehen Sie sich das folgende Beispiel in ASP.NET Core 2.1 oder früher an. Wenn eine Verknüpfung zu einer anderen Aktion (oder Seite) hergestellt wird, können Routenwerte auf unerwünschte Weise wiederverwendet werden.

  In */Pages/Store/Product.cshtml*:

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  In */Pages/Login.cshtml*:

  ```cshtml
  @page "{id?}"
  ```

  Wenn der URI in ASP.NET Core 2.1 oder früher `/Store/Product/18` ist, ist der Link, der von `@Url.Page("/Login")` auf der Speicher- bzw. Informationsseite generiert wird, `/Login/18`. Der `id`-Wert von 18 wird wiederverwendet, obwohl das Ziel des Links ein vollkommen anderer Teil der App ist. Bei dem `id`-Routenwert im Kontext der `/Login`-Seite handelt es sich wahrscheinlich um den Wert einer Benutzer-ID und nicht um einen Wert einer Speicherprodukt-ID.

  Beim Endpunktrouting mit ASP.NET Core 2.2 oder höher lautet das Ergebnis `/Login`. Umgebungswerte werden nicht wiederverwendet, wenn das verknüpfte Ziel eine andere Aktion oder Seite ist.

* Syntax für Routenparameter für Roundtrips: Schrägstriche werden nicht codiert, wenn eine Syntax mit zwei Sternchen (`**`) für Catch-All-Parameter verwendet wird.

  Bei der Linkgenerierung codiert das Routingsystem mit Ausnahme von Schrägstrichen den Wert in einem Catch-All-Parameter, dem zwei Sternchen voranstehen (`**`), z. B. `{**myparametername}`. Der Catch-All-Parameter mit zwei Sternchen wird beim `IRouter`-basierten Routing in ASP.NET Core 2.2 oder höher unterstützt.

  Die Syntax mit einem Sternchen für Catch-All-Parameter in früheren Versionen von ASP.NET Core (`{*myparametername}`) wird weiter unterstützt, und Schrägstriche werden codiert.

  | Route              | Link generiert mit:<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts` (der Schrägstrich wird codiert)             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>Middlewarebeispiel

Im folgenden Beispiel verwendet eine Middleware die <xref:Microsoft.AspNetCore.Routing.LinkGenerator>-API, um eine Verknüpfung zu einer Aktionsmethode herzustellen, die Speicherprodukte aufführt. Sie können für jede beliebige Klasse in einer App die API zur Linkgenerierung verwenden, indem Sie diese in eine Klasse einfügen und `GenerateLink` aufrufen.

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>Erstellen von Routen

Die meisten Apps erstellen Routen, indem sie <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> oder eine ähnliche Erweiterungsmethode aufrufen, die in <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> definiert ist. All diese <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>-Erweiterungsmethoden erstellen eine Instanz von <xref:Microsoft.AspNetCore.Routing.Route> und fügen diese der Routensammlung hinzu.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> akzeptiert keine Routenhandlerparameter. Durch <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden nur Routen hinzugefügt, die vom <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> verarbeitet werden. Weitere Informationen zum Routing in MVC finden Sie unter <xref:mvc/controllers/routing>.

Im folgenden Codebeispiel wird ein <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Aufruf dargestellt, der von einer typischen ASP.NET Core MVC-Routendefinition verwendet wird:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Diese Vorlage ordnet einen URL-Pfad zu und extrahiert die Routenwerte. Beispielsweise generiert der Pfad `/Products/Details/17` die folgenden Routenwerte: `{ controller = Products, action = Details, id = 17 }`.

Routenwerte werden ermittelt, indem der URL-Pfad in Segmente aufgeteilt wird und jedes Segment mit dem Namen des *Routenparameters* in der Routenvorlage abgeglichen wird. Jeder Routenparameter hat einen Namen. Die Parameter werden von Klammern `{ ... }` eingeschlossen und dadurch definiert.

Die obige Vorlage kann auch dem URL-Pfad `/` zugeordnet werden und daraus die Werte `{ controller = Home, action = Index }` generieren. Dies liegt daran, dass die Routenparameter `{controller}` und `{action}` über Standardwerte verfügen und der Routenparameter `id` optional ist. Hinter dem Namen des Routenparameters steht das Gleichheitszeichen (`=`), auf das der Wert folgt, der als Standardwert für den Parameter definiert wird. Durch ein Fragezeichen (`?`) nach dem Namen des Routenparameters wird ein optionaler Parameter definiert.

Durch Routenparameter mit einem Standardwert wird *immer* ein Routenwert erzeugt, wenn eine Übereinstimmung für die Route ermittelt wird. Bei optionalen Parametern wird kein Routenwert generiert, wenn kein entsprechendes URL-Pfadsegment vorhanden ist. Eine ausführliche Beschreibung zu den Szenarios und zur Syntax der Routenvorlage finden Sie unter [Referenz zu Routenvorlagen](#route-template-reference).

Im folgenden Beispiel definiert die Routenparameterdefinition `{id:int}` eine [Routeneinschränkung](#route-constraint-reference) für den Routenparameter `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Durch diese Vorlage wird bei einem Abgleich beispielsweise der URL-Pfad `/Products/Details/17`, aber nicht `/Products/Details/Apples` gefunden. In derartigen Einschränkungen wird <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementiert, und die Routenwerte werden auf Gültigkeit geprüft. Im obigen Beispiel muss sich der Routenwert `id` in einen Integer konvertieren lassen. Weitere Informationen zu Routeneinschränkungen, die vom Framework bereitgestellt werden, finden Sie in der [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Bei zusätzlichen Überladungen von <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden Werte für `constraints`, `dataTokens` und `defaults` akzeptiert. Üblicherweise werden diese Parameter so verwendet, dass ein Objekt eines anonymen Typs übergeben wird, in dem die Eigenschaftsnamen des anonymen Typs mit den Routenparameternamen abgeglichen werden.

In den folgenden <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Beispielen werden identische Routen erstellt:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Für einfache Routen eignet sich die Inline-Syntax zum Definieren von Einschränkungen und Standardwerten. Bestimmte Szenarios wie die Verwendung von Datentoken werden von dieser allerdings nicht unterstützt.

Das folgende Beispiel veranschaulicht einige weitere Szenarios:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Diese Vorlage kann einem URL-Pfad wie `/Blog/All-About-Routing/Introduction` zugeordnet werden. Sie extrahier die `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`-Werte. Die Standardroutenwerte für `controller` und `action` werden von der Route generiert, obwohl keine entsprechenden Routenparameter in der Vorlage vorhanden sind. Standardwerte können in der Routenvorlage angegeben werden. Der Routenparameter `article` wird durch zwei Sternchen (`**`) vor dem zugehörigen Namen als *Catch-All-Parameter* definiert. So wird der verbleibende Teil des URL-Pfads erfasst, und auch leere Zeichenfolgen können gefunden werden.

Im folgenden Beispiel werden Routeneinschränkungen und Datentoken hinzugefügt:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Diese Vorlage kann einem URL-Pfad wie `/en-US/Products/5` zugeordnet werden, und die `{ controller = Products, action = Details, id = 5 }`-Werte und `{ locale = en-US }`-Datentoken werden extrahiert.

![Gebietsschemas, Windows-Tokens](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generieren der URL zu einer Routenklasse

Durch die Kombination von mehreren Routenwerten und der zugehörigen Routenvorlage kann die <xref:Microsoft.AspNetCore.Routing.Route>-Klasse auch URLs generieren. Dieser Prozess verläuft umgekehrt zum Abgleich eines URL-Pfads.

> [!TIP]
> Wenn Sie die URL-Generierung besser nachvollziehen möchten, sollten Sie sich überlegen, welche URL generiert werden soll und wie diese mithilfe der Routenvorlage abgeglichen wird. Dabei sollten Sie auch darüber nachdenken, welche Werte erstellt werden. Dieser Vorgang entspricht in etwa der URL-Generierung in der <xref:Microsoft.AspNetCore.Routing.Route>-Klasse.

Im folgenden Beispiel wird eine allgemeine Standardroute für ASP.NET Core-MVC verwendet:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Mithilfe der Routenwerte `{ controller = Products, action = List }` wird die URL `/Products/List` generiert. Die Routenwerte werden als Ersatz für die entsprechenden Routenparameter verwendet, wodurch ein URL-Pfad erstellt werden kann. Da es sich bei `id` um einen optionalen Routenparameter handelt, wird die URL erfolgreich ohne einen Wert für `id` generiert.

Mithilfe der Routenwerte `{ controller = Home, action = Index }` wird die URL `/` generiert. Die bereitgestellten Routenwerte entsprechen den Standardwerten, sodass die Segmente, die mit diesen Werten übereinstimmen, problemlos ausgelassen werden können.

Mit beiden generierten URLs (`/Home/Index` und `/`) und dieser Routendefinition wird ein Roundtrip ausgeführt, und es werden dieselben Routenwerte erstellt, die auch zur Generierung der URL verwendet wurden.

> [!NOTE]
> Eine auf ASP.NET Core MVC basierende App sollte zur Generierung von URLs die Routingfunktion nicht direkt aufrufen, sondern <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> verwenden.

Weitere Informationen zur Generierung von URLs finden Sie im Abschnitt [Referenz zur URL-Generierung](#url-generation-reference).

## <a name="use-routing-middleware"></a>Verwenden von Routingmiddleware

Legen Sie das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei der App als Verweis fest.

Fügen Sie anschließend dem Dienstcontainer in `Startup.ConfigureServices` die Routingfunktionalität hinzu:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Routen müssen in der `Startup.Configure`-Methode konfiguriert werden. In der Beispiel-App werden die folgenden APIs verwendet:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Ordnet nur HTTP GET-Anforderungen zu.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

In der folgenden Tabelle werden die Antworten mit den angegebenen URIs aufgelistet:

| URI                    | Antwort                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hallo! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/`      | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | The request falls through, matches HTTP GET only (Die Anforderung ist fehlgeschlagen, nur für eine HTTP-GET-Anforderung wird eine Übereinstimmung ermittelt) |
| `GET /hello/Joe/Smith` | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |

Das Framework stellt mehrere Erweiterungsmethoden zum Erstellen von Routen zur Verfügung (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

In den `Map[Verb]`-Methoden werden Einschränkungen verwendet, um die Route auf das HTTP-Verb im Methodennamen zu beschränken. Beispielsweise unter <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> und <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referenz für Routenvorlagen

Token in geschweiften Klammern (`{ ... }`) definieren *Routenparameter*, die beim Abgleich der Route gebunden werden. Sie können in einem Routensegment mehrere Routenparameter definieren, wenn Sie sie durch einen Literalwert trennen. `{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet. Diese Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen.

Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen. Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads. Damit das Trennzeichen (`{` oder `}`) der Routenparameterliteralzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`{{` oder `}}`), was einem Escapezeichen entspricht.

Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen. Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich. Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben. Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Zuordnung ermittelt, da der nachstehende Punkt (`.`) optional ist. Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:

* `/files/myFile.txt`
* `/files/myFile`

Sie können ein (`*`) oder zwei (`**`) Sternchen als Präfix für einen Routenparameter verwenden, um eine Bindung zum verbleibenden Teil des URI herzustellen. Hierbei wird von *Catch-All*-Parametern gesprochen. Durch `blog/{**slug}` wird beispielsweise jeder URI ermittelt, der mit `/blog` beginnt und dahinter einen beliebigen Wert aufweist, der dann dem `slug`-Routenwert zugeordnet wird. Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.

Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen zu generieren (`/`). Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }``foo/my%2Fpath`. Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen. Um Trennzeichen für Roundtrips einsetzen zu können, verwenden Sie das Routenparameterpräfix `**`. Die Route `foo/{**path}` mit `{ path = "my/path" }` generiert `foo/my/path`.

Routenparameter können über mehrere *Standardwerte* verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden. Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert. Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist. Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird, z. B. `id?`. Der Unterschied zwischen optionalen Parametern und Routenparametern mit einem Standardwert besteht darin, dass mithilfe der letzteren immer ein Wert erzeugt wird. Ein optionaler Parameter verfügt demgegenüber nur dann über einen Wert, wenn ein solcher von der Anforderungs-URL bereitgestellt wird.

Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen. Eine *Inline-Einschränkung* für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen. Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen. Mehrere Inline-Einschränkungen können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden.

Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann. In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt. Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Routenparameter können darüber hinaus über Parametertransformatoren verfügen, die den Wert eines Parameters beim Generieren von Links umwandeln und Aktionen und Seiten an URLs anpassen. Wie Einschränkungen können auch Parametertransformatoren einem Routenparameter inline hinzugefügt werden, indem ein Doppelpunkt (`:`) und der Name des Transformators hinter dem Namen des Routenparameters hinzugefügt werden. In der Routenvorlage `blog/{article:slugify}` wird beispielsweise der Transformator `slugify` festgelegt. Weitere Informationen zu Parametertransformatoren finden Sie im Abschnitt [Parametertransformatorreferenz](#parameter-transformer-reference).

Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten.

| Routenvorlage                           | Beispiel-URI für Übereinstimmung    | Der Anforderungs-URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt.                                     |
| `{Page=Home}`                            | `/`                     | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Home` festgelegt.                                         |
| `{Page=Home}`                            | `/Contact`              | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Contact` festgelegt.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Stimmt mit dem `Products`-Controller und der `List`-Aktion überein.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Stimmt mit dem `Products`-Controller und der `Details`-Aktion (`id` auf 123 festgelegt) überein. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Stimmt mit dem `Home`-Controller und der `Index`-Aktion überein (`id` wird ignoriert).        |

Mit Vorlagen lässt sich Routing besonders leicht durchführen. Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.

> [!TIP]
> Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.

## <a name="reserved-routing-names"></a>Reservierte Routingnamen

Die folgenden Schlüsselwörter sind reservierte Namen und können nicht als Routennamen oder Parameter verwendet werden:

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>Referenz für Routeneinschränkungen

Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird. In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird bestimmt, ob der Wert gültig ist. Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist. <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen. Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet.

> [!WARNING]
> Verwenden Sie keine Einschränkungen für die **Eingabeüberprüfung**. Wenn bei der **Eingabevalidierung** Einschränkungen verwendet werden, lösen ungültige Eingaben den Fehler *404 - Not Found* (404 – Nicht gefunden) aus. Stattdessen sollte jedoch der Fehler *400 - Bad Request* (400 – Fehlerhafte Anforderung) ausgelöst und mit einer entsprechenden Fehlermeldung angezeigt werden. Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnlichen Routen zu **unterscheiden**.

In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschriebe.

| Constraint | Beispiel | Beispiele für Übereinstimmungen | Hinweise |
|------------|---------|-----------------|-------|
| `int` | `{id:int}` | `123456789`, `-123456789` | Für jeden Integer wird eine Übereinstimmung ermittelt.|
| `bool` | `{active:bool}` | `true`, `FALSE` | Entspricht `true` oder `false`. Groß-/Kleinschreibung wird nicht beachtet.|
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Entspricht einem gültigen `DateTime`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Entspricht einem gültigen `decimal`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `double`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `float`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Für einen gültigen `Guid`-Wert wird eine Übereinstimmung ermittelt.|
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Für einen gültigen `long`-Wert wird eine Übereinstimmung ermittelt.|
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens eine Länge von 4 Zeichen aufweisen.|
| `maxlength(value)` | `{filename:maxlength(8)}` | `MyFile` | Die Zeichenfolge hat maximal 8 Zeichen.|
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau 12 Zeichen aufweisen.|
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge muss mindestens 8 und darf maximal 16 Zeichen lang sein.|
| `min(value)` | `{age:min(18)}` | `19` | Der ganzzahlige Wert muss mindestens 18 sein.|
| `max(value)` | `{age:max(120)}` | `91` | Der ganzzahlige Wert darf höchstens 120 sein.|
| `range(min,max)` | `{age:range(18,120)}` | `91` | Der ganzzahlige Wert muss mindestens 18 und darf höchstens 120 sein.|
| `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem alphabetische Zeichen bestehen, `a`-`z`. Groß-/Kleinschreibung wird nicht beachtet.|
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss mit dem regulären Ausdruck übereinstimmen. Weitere Informationen finden Sie unter Tipps zum Definieren eines regulären Ausdrucks.|
| `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss.|

Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden. Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist. Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind. Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert. Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.

## <a name="regular-expressions"></a>Reguläre Ausdrücke

Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt. Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.

In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden. Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden. Der zu verwendende reguläre Ausdruck `^\d{3}-\d{2}-\d{4}$` für das Routing:

* Der Ausdruck muss den einfachen umgekehrten Schrägstrich `\` enthalten, der im Quellcode in der Zeichenfolge als doppelter umgekehrter Schrägstrich `\\` vorhanden ist.
* Der reguläre Ausdruck muss `\\` verwenden, um die Zeichenfolge `\` mit Escapezeichen zu versehen.
* Für den regulären Ausdruck ist `\\` nicht erforderlich, wenn [ausführliche Zeichenfolgenliterale](/dotnet/csharp/language-reference/keywords/string) verwendet werden.

Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (`{{`, `}`, `[[`, `]]`). In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind:

| Regulärer Ausdruck    | Mit Escapezeichen versehener regulärer Ausdruck     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Beim Routing verwendete reguläre Ausdrücke beginnen oft mit einem Caretzeichen (`^`) und stellen die Startposition der Zeichenfolge dar. Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar. Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt. Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist. In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt.

| expression   | Zeichenfolge    | Match | Kommentar               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | mz        | Ja   | Ausdruck stimmt überein    |
| `[a-z]{2}`   | MZ        | Ja   | keine Unterscheidung zwischen Groß-/Kleinbuchstaben    |
| `^[a-z]{2}$` | hello     | Nein    | siehe Erläuterungen zu `^` und `$` oben |
| `^[a-z]{2}$` | 123abc456 | Nein    | siehe Erläuterungen zu `^` und `$` oben |

Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken. Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen. Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis. Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen , werden als reguläre Ausdrücke behandelt. Dies gilt allerdings nicht für Inline-Einschränkungen in einer Vorlage.

## <a name="custom-route-constraints"></a>Benutzerdefinierte Routeneinschränkungen

Zusätzlich zu den integrierten Routeneinschränkungen können benutzerdefinierte Routeneinschränkungen durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden. Die <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle enthält eine einzelne Methode, `Match`, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`.

Zum Verwenden einer benutzerdefinierten <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> der App im Dienstcontainer der App registriert werden. Eine <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> ist ein Wörterbuch, das Routeneinschränkungsschlüssel <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Implementierungen zuordnet, die diese Einschränkungen überprüfen. Die <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden. Zum Beispiel:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Die Einschränkung kann dann auf die übliche Weise mit dem bei der Registrierung des Einschränkungstyps angegebenen Namen auf Routen angewendet werden. Zum Beispiel:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>Parametertransformatorreferenz

Parametertransformatoren:

* Werden beim Generieren eines Links für eine <xref:Microsoft.AspNetCore.Routing.Route> ausgeführt.
* Implementieren Sie `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`.
* Werden mithilfe von <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> konfiguriert.
* Nehmen den Routenwert des Parameters an und transformieren ihn in einen neuen Zeichenfolgenwert.
* Haben die Verwendung des transformierten Wert in einem generierten Link zur Folge.

Beispielsweise generiert ein benutzerdefinierter Parametertransformator `slugify` im Routenmuster `blog\{article:slugify}` mit `Url.Action(new { article = "MyTestArticle" })``blog\my-test-article`.

Um einen Parametertransformator in einem Routenmuster zu verwenden, konfigurieren Sie ihn zuerst mit <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> in `Startup.ConfigureServices`:

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

Parametertransformatoren werden auch von Frameworks verwendet, um den URI zu transformieren, zu dem ein Endpunkt aufgelöst wird. Beispielsweise verwendet ASP.NET Core MVC Parametertransformatoren zum Transformieren des Routenwerts, der zum Zuordnen einer `area`, eines `controller`, einer `action` und einer `page` verwendet wird.

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

Mit der vorstehenden Route wird die Aktion `SubscriptionManagementController.GetAll` dem URI `/subscription-management/get-all` zugeordnet. Ein Parametertransformator ändert nicht die zum Generieren eines Links verwendeten Routenwerte. Beispielsweise gibt `Url.Action("GetAll", "SubscriptionManagement")``/subscription-management/get-all` aus.

ASP.NET Core bietet API-Konventionen für die Verwendung von Parametertransformatoren mit generierten Routen:

* ASP.NET Core MVC verwendet die `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention`-API-Konvention. Diese Konvention wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an. Der Parametertransformator transformiert Attributroutentoken, wenn diese ersetzt werden. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung](xref:mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement).
* `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` Pages verwendet die Razor-API-Konvention. Diese Konvention wendet einen angegebenen Parametertransformator auf alle automatisch erkannten Razor Pages-Seiten an. Der Parametertransformator transformiert die Ordner- und Dateinamensegmente von Razor Pages-Routen. Weitere Informationen finden Sie unter [Verwenden eines Parametertransformators zum Anpassen von Seitenrouten](xref:razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes).

## <a name="url-generation-reference"></a>Referenz für URL-Generierung

Im folgenden Beispiel wird gezeigt, wie Sie einen Link zu einer Route unter Berücksichtigung eines vorhandenen Wörterbuchs mit Routenwerten und einer <xref:Microsoft.AspNetCore.Routing.RouteCollection> erstellen.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Die <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>-Eigenschaft, die am Ende des obigen Beispiels erstellt wird, hat den Wert `/package/create/123`. Das Wörterbuch stellt die Routenwerte von `operation` und `id` aus der Vorlage „Track Package Route“ (`package/{operation}/{id}`) bereit. Weitere Informationen finden Sie im Beispielcode im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware) oder in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Der zweite Parameter für den <xref:Microsoft.AspNetCore.Routing.VirtualPathContext>-Konstruktor ist eine Auflistung von *Umgebungswerten*. Mit diesen lässt sich leicht die Anzahl der Werte einschränken, die ein Entwickler innerhalb eines Anforderungskontexts angeben muss. Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt. In einer ASP.NET Core-MVC-App müssen Sie innerhalb der `About`-Aktion von `HomeController` nicht den Controllerroutenwert angeben, um eine Verknüpfung mit der `Index`-Aktion herzustellen, da der Umgebungswert von `Home` verwendet wird.

Umgebungswerte, die mit keinem Parameter übereinstimmen, werden ignoriert. Außerdem werden Umgebungswerte ignoriert, wenn ein explizit angegebener Wert den betreffenden Umgebungswert außer Kraft setzt. Der Abgleich in der URL wird von links nach rechts ausgeführt.

Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt. In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen:

| Umgebungswerte                     | Explizite Werte                        | Ergebnis                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Wenn eine Route über einen Standardwert verfügt, der keinem Parameter entspricht, und dieser Wert explizit bereitgestellt wird, muss er dem Standardwert entsprechen:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Für diese Route wird nur dann ein Link generiert, wenn die übereinstimmenden Werte für `controller` und `action` bereitgestellt werden.

## <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente (z.B. `[Route("/x{token}y")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet. Unter [diesem Code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) finden Sie eine ausführliche Erklärung für das Abgleichen komplexer Segmente. Das [Codebeispiel](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) wird von ASP.NET Core nicht verwendet, aber es bietet eine gute Erklärung komplexer Segmente.
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/dotnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Beim Routing werden Anforderungs-URIs Routenhandlern zugeordnet und Anforderungen weitergeleitet. Routen werden in der App definiert und beim Start der App konfiguriert. Eine Route kann optional Werte aus der URL extrahieren, die in der Anforderung enthalten ist. Diese Werte können anschließend für die Verarbeitung der Anforderung verwendet werden. Unter Verwendung von konfigurierten Routen aus der App können durch das Routing URLs erstellt werden, die Routenhandlern zugeordnet werden.

Wenn Sie die aktuellsten Routingszenarios in ASP.NET Core 2.1 verwenden möchten, geben Sie die [Kompatibilitätsversion](xref:mvc/compatibility-version) zur Registrierung der MVC-Dienste in `Startup.ConfigureServices` an:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> In diesem Artikel wird das Low-Level-Routing in ASP.NET Core beschrieben. Weitere Informationen zum Routing mit ASP.NET Core MVC finden Sie unter <xref:mvc/controllers/routing>. Weitere Informationen zu Routingkonventionen in Razor Pages finden Sie unter <xref:razor-pages/razor-pages-conventions>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="routing-basics"></a>Routinggrundlagen

Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten. Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:

* Sie unterstützt ein grundlegendes und beschreibendes Routingschema.
* Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.

Entwickler fügen in der Regel in speziellen Situationen (z. B. bei Blog- und E-Commerce-Endpunkten) durch [Attributrouting](xref:mvc/controllers/routing#attribute-routing) oder dedizierte konventionelle Routen zusätzliche kurze Routen zu stark frequentierten Bereichen der App hinzu.

Web-APIs sollten das Attributrouting verwenden, um die Funktionalität der App als einen Satz von Ressourcen zu modellieren, bei denen Vorgänge durch HTTP-Verben dargestellt werden. Dies bedeutet, dass viele Vorgänge (z.B. GET, POST) für dieselbe logische Ressource dieselbe URL verwenden. Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.

Razor Pages-Apps verwenden konventionelles Standardrouting, um benannte Ressourcen im *Pages*-Ordner einer App bereitzustellen. Außerdem gibt es weitere Konventionen zum Anpassen des Routingverhaltens für Razor Pages. Weitere Informationen finden Sie unter <xref:razor-pages/index> und <xref:razor-pages/razor-pages-conventions>.

Die Unterstützung zum Generieren von URLs ermöglicht es, die App ohne Hartcodierung der URLs zum Verlinken der App zu entwickeln. Auf diese Weise kann mit einer grundlegenden Routingkonfiguration begonnen werden, und die Routen können geändert werden, wenn das Ressourcenlayout der App festgelegt wurde.

Beim Routing werden Routenimplementierungen von <xref:Microsoft.AspNetCore.Routing.IRouter> für Folgendes verwendet:

* Zuordnen von eingehenden Anforderungen zu *Routenhandlern*
* Generieren von URLs für Antworten

Eine App verfügt standardmäßig über genau eine Routensammlung. Wenn eine Anforderung eingeht, werden die Routen in der Sammlung in der Reihenfolge verarbeitet, in der sie in der Sammlung aufgelistet werden. Dann versucht das Framework, eine eingehende Anforderungs-URL einer Route in der Sammlung zuzuordnen, indem es die <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*>-Methode für jede Route in der Sammlung aufruft. Bei einer Antwort kann Routing verwendet werden, um URLs zu generieren (z.B. für Umleitungen oder Links), die auf Routeninformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.

Das Routingsystem weist die folgenden Eigenschaften auf:

* Die Syntax der Routenvorlage wird zum Definieren von Routen mit Routenparametern verwendet, die mit Token versehen sind.
* Sowohl die konventionelle Endpunktkonfiguration als auch die Endpunktkonfiguration mit Attributen ist zulässig.
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> wird verwendet, um zu bestimmen, ob ein URL-Parameter einen gültigen Wert für eine bestimmte Endpunkteinschränkung enthält.
* App-Modelle wie MVC bzw Razor Pages registrieren sämtliche Routen, für die die Implementierung von Routingszenarios vorhersagbar ist.
* Bei einer Antwort kann Routing verwendet werden, um URLs zu generieren (z.B. für Umleitungen oder Links), die auf Routeninformationen basieren. Dadurch wird die Erstellung von hartcodierten URLs verhindert, was wiederrum die Wartbarkeit verbessert.
* Die URL-Generierung basiert auf Routen, die beliebige Erweiterbarkeit unterstützen. <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> bietet Methoden zum Erstellen von URLs.
<!-- fix [middleware](xref:fundamentals/middleware/index) -->
Die Routingfunktionalität wird über die Klasse <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> mit der [Middlewarepipeline](xref:fundamentals/middleware/index) verbunden. [ASP.NET Core-MVC](xref:mvc/overview) fügt im Rahmen der Konfiguration die Routingfunktionalität der Middlewarepipeline hinzu und verarbeitet das Routing in MVC- und Razor Pages-Apps. Informationen darüber, wie Sie Routing als eigenständige Komponente verwenden, finden Sie im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware).

### <a name="url-matching"></a>URL-Zuordnung

Bei einer URL-Zuordnung werden eingehende Anforderungen durch Routing an einen *Handler* gesendet. Für diesen Prozess werden die Daten des URL-Pfads verwendet. Es können jedoch auch alle Daten der Anforderung genutzt werden. Für die Skalierung der Größe und Komplexität einer App ist das Versenden von Anforderungen an unterschiedliche Handler entscheidend.

Eingehende Anforderungen werden vom <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>-Objekt bearbeitet, das die Methode <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> für alle Routen nacheinander aufruft. In der <xref:Microsoft.AspNetCore.Routing.IRouter>-Instanz wird entschieden, ob die Anforderung *verarbeitet* wird, indem für den [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) ein <xref:Microsoft.AspNetCore.Http.RequestDelegate>-Delegat festgelegt wird, der nicht NULL ist. Wenn von einer Route ein Handler für die Anforderung festgelegt wird, endet die Routenverarbeitung, und der Handler wird zur Verarbeitung der Anforderung aufgerufen. Wenn kein Routenhandler gefunden wird, um die Anforderung zu verarbeiten, leitet die Middleware die Anforderung an die nächste Middleware in der Anforderungspipeline weiter.

Die Haupteingabe für <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> ist der [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*), der der aktuellen Anforderung zugeordnet ist. [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) und [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) sind Ausgaben, die festgelegt werden, nachdem eine Route zugeordnet wurde.

Eine Zuordnung, die <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> aufruft, legt außerdem die Eigenschaften von [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) auf Grundlage der bisher verarbeiteten Anforderungen auf entsprechende Werte fest.

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) ist ein Wörterbuch mit *Routenwerten*, das aus der Route erstellt wird. Die Werte werden in der Regel durch die Tokenisierung der URL ermittelt und können so verwendet werden, dass Benutzereingaben akzeptiert oder weitere Entscheidungen in der App zum Versenden von Anforderungen getroffen werden.

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) ist eine Eigenschaftensammlung mit zusätzlichen Daten zur zugeordneten Route. <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> werden bereitgestellt, damit sich jeder Route Zustandsdaten zuordnen lassen, sodass in der App später Entscheidungen auf Grundlage der zugeordneten Route getroffen werden können. Diese Werte werden vom Entwickler vorgegeben und beeinflussen das Routingverhalten **in keiner Weise**. Außerdem können im Gegensatz zu [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values), die sich in bzw. aus Zeichenfolgen konvertieren lassen müssen, Werte in [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) einem beliebigen Typ entsprechen.

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) ist eine Liste der Routen, die an der Zuordnung der Anforderung beteiligt waren. Routen können in anderen Routen geschachtelt werden. Die <xref:Microsoft.AspNetCore.Routing.RouteData.Routers>-Eigenschaft stellt den Pfad mithilfe der logischen Routenstruktur dar, die zu der Zuordnung geführt hat. Üblicherweise ist das erste Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> die Routensammlung. Dieses sollte zur URL-Generierung verwendet werden. Das letzte Element in <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> ist der Routenhandler, für den eine Zuordnung vorgenommen wurde.

<a name="lg"></a>

### <a name="url-generation"></a>URL-Generierung

Bei der URL-Generierung wird durch Routing ein URL-Pfad basierend auf mehreren Routenwerten erstellt. Dies ermöglicht eine logische Trennung zwischen den Routenhandlern und den URLs, die auf diese zugreifen.

Auch für die URL-Generierung wird ein iterativer Prozess verwendet, bei dem allerdings zuerst vom Benutzer- oder Frameworkcode die <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>-Methode der Routensammlung aufgerufen wird. Nacheinander wird für jede *Route* die zugehörige <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*>-Methode aufgerufen, bis ein <xref:Microsoft.AspNetCore.Routing.VirtualPathData>-Objekt zurückgegeben wird, dessen Wert nicht NULL ist.

Die primären Eingaben für <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> sind:

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

Für Routen werden überwiegend die Routenwerte verwendet, die von <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> und <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> bereitgestellt werden. Dadurch wird ermittelt, ob eine URL generiert werden kann und welche Werte diese enthalten soll. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> sind Routenwerte, die durch die Zuordnung zur aktuellen Anforderung erstellt wurden. Im Gegensatz dazu sind <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> die Routenwerte, die angeben, wie die gewünschte URL für den aktuellen Vorgang generiert werden soll. <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext> wird bereitgestellt, falls für eine Route Dienste oder zusätzliche Daten, die mit dem aktuellen Kontext verknüpft sind, abgerufen werden müssen.

> [!TIP]
> [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) kann als Menge von überschriebenen Eigenschaften für [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) betrachtet werden. Bei der URL-Generierung wird versucht, Routenwerte der aktuellen Anforderung wiederzuverwenden, um so URLs für Links generieren zu können, die dieselbe Route oder dieselben Routenwerte verwenden.

Die Ausgabe von <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> ist ein <xref:Microsoft.AspNetCore.Routing.VirtualPathData>-Objekt. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> verhält sich analog zu <xref:Microsoft.AspNetCore.Routing.RouteData>. <xref:Microsoft.AspNetCore.Routing.VirtualPathData> enthält das <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>-Objekt für die Ausgabe-URL und einige zusätzlichen Eigenschaften, die von der Route festgelegt werden sollten.

Die [VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*)-Eigenschaft enthält den *virtuellen Pfad*, der von der Route erstellt wird. Je nach Anforderungen muss der Pfad eventuell noch weiter verarbeitet werden. Wenn die generierte URL in HTML gerendert werden soll, müssen Sie den App-Basispfad voranstellen.

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) ist ein Verweis auf die Route, mit der die URL generiert wurde.

Die [VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*)-Eigenschaften stellen ein Wörterbuch mit zusätzlichen Daten zur Route dar, mit der die URL generiert wurde. Diese Eigenschaften verhalten sich analog zu [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*).

### <a name="create-routes"></a>Erstellen von Routen

Für die Routingfunktionalität wird die <xref:Microsoft.AspNetCore.Routing.Route>-Klasse als Standardimplementierung von <xref:Microsoft.AspNetCore.Routing.IRouter> zur Verfügung gestellt. <xref:Microsoft.AspNetCore.Routing.Route> verwendet die Syntax für *Routenvorlagen*, um Muster zu definieren, die dem URL-Pfad zugeordnet werden können, wenn <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> aufgerufen wird. <xref:Microsoft.AspNetCore.Routing.Route> nutzt dieselbe Routenvorlage zum Generieren einer URL, wenn <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> aufgerufen wird.

Die meisten Apps erstellen Routen, indem sie <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> oder eine ähnliche Erweiterungsmethode aufrufen, die in <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> definiert ist. All diese <xref:Microsoft.AspNetCore.Routing.IRouteBuilder>-Erweiterungsmethoden erstellen eine Instanz von <xref:Microsoft.AspNetCore.Routing.Route> und fügen diese der Routensammlung hinzu.

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> akzeptiert keine Routenhandlerparameter. Durch <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden nur Routen hinzugefügt, die vom <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> verarbeitet werden. Der Standardhandler ist ein `IRouter`. Möglicherweise verarbeitet der Handler die Anforderung nicht. Beispielsweise ist ASP.NET Core MVC üblicherweise als Standardhandler konfiguriert, der nur Anforderungen verarbeitet, die mit einem verfügbaren Controller und einer Aktion übereinstimmen. Weitere Informationen zum Routing in MVC finden Sie unter <xref:mvc/controllers/routing>.

Im folgenden Codebeispiel wird ein <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Aufruf dargestellt, der von einer typischen ASP.NET Core MVC-Routendefinition verwendet wird:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Diese Vorlage ordnet einen URL-Pfad zu und extrahiert die Routenwerte. Beispielsweise generiert der Pfad `/Products/Details/17` die folgenden Routenwerte: `{ controller = Products, action = Details, id = 17 }`.

Routenwerte werden ermittelt, indem der URL-Pfad in Segmente aufgeteilt wird und jedes Segment mit dem Namen des *Routenparameters* in der Routenvorlage abgeglichen wird. Jeder Routenparameter hat einen Namen. Die Parameter werden von Klammern `{ ... }` eingeschlossen und dadurch definiert.

Die obige Vorlage kann auch dem URL-Pfad `/` zugeordnet werden und daraus die Werte `{ controller = Home, action = Index }` generieren. Dies liegt daran, dass die Routenparameter `{controller}` und `{action}` über Standardwerte verfügen und der Routenparameter `id` optional ist. Hinter dem Namen des Routenparameters steht das Gleichheitszeichen (`=`), auf das der Wert folgt, der als Standardwert für den Parameter definiert wird. Durch ein Fragezeichen (`?`) nach dem Namen des Routenparameters wird ein optionaler Parameter definiert.

Durch Routenparameter mit einem Standardwert wird *immer* ein Routenwert erzeugt, wenn eine Übereinstimmung für die Route ermittelt wird. Bei optionalen Parametern wird kein Routenwert generiert, wenn kein entsprechendes URL-Pfadsegment vorhanden ist. Eine ausführliche Beschreibung zu den Szenarios und zur Syntax der Routenvorlage finden Sie unter [Referenz zu Routenvorlagen](#route-template-reference).

Im folgenden Beispiel definiert die Routenparameterdefinition `{id:int}` eine [Routeneinschränkung](#route-constraint-reference) für den Routenparameter `id`:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

Durch diese Vorlage wird bei einem Abgleich beispielsweise der URL-Pfad `/Products/Details/17`, aber nicht `/Products/Details/Apples` gefunden. In derartigen Einschränkungen wird <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> implementiert, und die Routenwerte werden auf Gültigkeit geprüft. Im obigen Beispiel muss sich der Routenwert `id` in einen Integer konvertieren lassen. Weitere Informationen zu Routeneinschränkungen, die vom Framework bereitgestellt werden, finden Sie in der [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Bei zusätzlichen Überladungen von <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> werden Werte für `constraints`, `dataTokens` und `defaults` akzeptiert. Üblicherweise werden diese Parameter so verwendet, dass ein Objekt eines anonymen Typs übergeben wird, in dem die Eigenschaftsnamen des anonymen Typs mit den Routenparameternamen abgeglichen werden.

In den folgenden <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*>-Beispielen werden identische Routen erstellt:

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> Für einfache Routen eignet sich die Inline-Syntax zum Definieren von Einschränkungen und Standardwerten. Bestimmte Szenarios wie die Verwendung von Datentoken werden von dieser allerdings nicht unterstützt.

Das folgende Beispiel veranschaulicht einige weitere Szenarios:

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

Diese Vorlage kann einem URL-Pfad wie `/Blog/All-About-Routing/Introduction` zugeordnet werden. Sie extrahier die `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`-Werte. Die Standardroutenwerte für `controller` und `action` werden von der Route generiert, obwohl keine entsprechenden Routenparameter in der Vorlage vorhanden sind. Standardwerte können in der Routenvorlage angegeben werden. Der Routenparameter `article` wird durch ein Sternchen (`*`) vor dem zugehörigen Namen als *Catch-All-Parameter* definiert. So wird der verbleibende Teil des URL-Pfads erfasst, und auch leere Zeichenfolgen können gefunden werden.

Im folgenden Beispiel werden Routeneinschränkungen und Datentoken hinzugefügt:

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

Diese Vorlage kann einem URL-Pfad wie `/en-US/Products/5` zugeordnet werden, und die `{ controller = Products, action = Details, id = 5 }`-Werte und `{ locale = en-US }`-Datentoken werden extrahiert.

![Gebietsschemas, Windows-Tokens](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>Generieren der URL zu einer Routenklasse

Durch die Kombination von mehreren Routenwerten und der zugehörigen Routenvorlage kann die <xref:Microsoft.AspNetCore.Routing.Route>-Klasse auch URLs generieren. Dieser Prozess verläuft umgekehrt zum Abgleich eines URL-Pfads.

> [!TIP]
> Wenn Sie die URL-Generierung besser nachvollziehen möchten, sollten Sie sich überlegen, welche URL generiert werden soll und wie diese mithilfe der Routenvorlage abgeglichen wird. Dabei sollten Sie auch darüber nachdenken, welche Werte erstellt werden. Dieser Vorgang entspricht in etwa der URL-Generierung in der <xref:Microsoft.AspNetCore.Routing.Route>-Klasse.

Im folgenden Beispiel wird eine allgemeine Standardroute für ASP.NET Core-MVC verwendet:

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

Mithilfe der Routenwerte `{ controller = Products, action = List }` wird die URL `/Products/List` generiert. Die Routenwerte werden als Ersatz für die entsprechenden Routenparameter verwendet, wodurch ein URL-Pfad erstellt werden kann. Da es sich bei `id` um einen optionalen Routenparameter handelt, wird die URL erfolgreich ohne einen Wert für `id` generiert.

Mithilfe der Routenwerte `{ controller = Home, action = Index }` wird die URL `/` generiert. Die bereitgestellten Routenwerte entsprechen den Standardwerten, sodass die Segmente, die mit diesen Werten übereinstimmen, problemlos ausgelassen werden können.

Mit beiden generierten URLs (`/Home/Index` und `/`) und dieser Routendefinition wird ein Roundtrip ausgeführt, und es werden dieselben Routenwerte erstellt, die auch zur Generierung der URL verwendet wurden.

> [!NOTE]
> Eine auf ASP.NET Core MVC basierende App sollte zur Generierung von URLs die Routingfunktion nicht direkt aufrufen, sondern <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> verwenden.

Weitere Informationen zur Generierung von URLs finden Sie im Abschnitt [Referenz zur URL-Generierung](#url-generation-reference).

## <a name="use-routing-middleware"></a>Verwenden von Routingmiddleware

Legen Sie das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) in der Projektdatei der App als Verweis fest.

Fügen Sie anschließend dem Dienstcontainer in `Startup.ConfigureServices` die Routingfunktionalität hinzu:

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

Routen müssen in der `Startup.Configure`-Methode konfiguriert werden. In der Beispiel-App werden die folgenden APIs verwendet:

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>: Ordnet nur HTTP GET-Anforderungen zu.
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

In der folgenden Tabelle werden die Antworten mit den angegebenen URIs aufgelistet:

| URI                    | Antwort                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hallo! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hallo! Route values: [operation, track], [id, -3] |
| `/package/track/`      | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | The request falls through, matches HTTP GET only (Die Anforderung ist fehlgeschlagen, nur für eine HTTP-GET-Anforderung wird eine Übereinstimmung ermittelt) |
| `GET /hello/Joe/Smith` | The request falls through, no match. (Die Anforderung ist fehlgeschlagen, keine Übereinstimmung.)              |

Wenn Sie eine einzelne Route konfigurieren, müssen Sie <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> aufrufen und eine `IRouter`-Instanz übergeben. <xref:Microsoft.AspNetCore.Routing.RouteBuilder> muss nicht verwendet werden.

Das Framework stellt mehrere Erweiterungsmethoden zum Erstellen von Routen zur Verfügung (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>):

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

Einige der aufgeführten Methoden (z. B. <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>) erfordern einen <xref:Microsoft.AspNetCore.Http.RequestDelegate>. <xref:Microsoft.AspNetCore.Http.RequestDelegate> wird als *Routenhandler* verwendet, wenn der Abgleich für die Route erfolgreich ist. Für andere Erweiterungsmethoden kann eine Middlewarepipeline konfiguriert werden, die als Routenhandler verwendet wird. Wenn die `Map*`-Methode einen Handler wie <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*> nicht akzeptiert, verwendet sie <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>.

In den `Map[Verb]`-Methoden werden Einschränkungen verwendet, um die Route auf das HTTP-Verb im Methodennamen zu beschränken. Beispielsweise unter <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> und <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>.

## <a name="route-template-reference"></a>Referenz für Routenvorlagen

Token in geschweiften Klammern (`{ ... }`) definieren *Routenparameter*, die beim Abgleich der Route gebunden werden. Sie können in einem Routensegment mehrere Routenparameter definieren, wenn Sie sie durch einen Literalwert trennen. `{controller=Home}{action=Index}` ist z.B. keine gültige Route, da sich zwischen `{controller}` und `{action}` kein Literalwert befindet. Diese Routenparameter müssen einen Namen besitzen und können zusätzliche Attribute aufweisen.

Eine Literalzeichenfolge, die nicht den Routenparametern entspricht (z.B. `{id}`), muss zusammen mit dem Pfadtrennzeichen `/` mit dem URL-Text übereinstimmen. Beim Abgleich von Text wird nicht zwischen Groß-/Kleinbuchstaben unterschieden, und die Übereinstimmung basiert auf der decodierten Repräsentation des URL-Pfads. Damit das Trennzeichen (`{` oder `}`) der Routenparameterliteralzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`{{` oder `}}`), was einem Escapezeichen entspricht.

Bei einem URL-Muster, durch das ein Dateiname mit einer optionalen Erweiterung erfasst werden soll, sind noch weitere Aspekte zu berücksichtigen. Dies wird z.B. anhand der Vorlage `files/{filename}.{ext?}` deutlich. Wenn sowohl für `filename` als auch für `ext` Werte vorhanden sind, werden beide Werte angegeben. Wenn nur für `filename` ein Wert in der URL vorhanden ist, wird für die Route eine Zuordnung ermittelt, da der nachstehende Punkt (`.`) optional ist. Für die folgenden URLs wird eine Übereinstimmung für die Route ermittelt:

* `/files/myFile.txt`
* `/files/myFile`

Sie können das Sternchen (`*`) als Präfix für einen Routenparameter verwenden, um eine Bindung zum verbleibenden Teil des URI herzustellen. Hierbei wird von einem *Catch-All*-Parameter gesprochen. Durch `blog/{*slug}` wird beispielsweise jeder URI ermittelt, der mit `/blog` beginnt und dahinter einen beliebigen Wert aufweist, der dann dem `slug`-Routenwert zugeordnet wird. Durch Catch-All-Parameter können auch leere Zeichenfolgen gefunden werden.

Der Catch-All-Parameter schützt die entsprechenden Zeichen (Escaping), wenn die Route verwendet wird, um eine URL, einschließlich Pfadtrennzeichen zu generieren (`/`). Z.B. generiert die Route `foo/{*path}` mit den Routenwerten `{ path = "my/path" }``foo/my%2Fpath`. Beachten Sie den umgekehrten Schrägstrich mit Escapezeichen.

Routenparameter können über mehrere *Standardwerte* verfügen, die nach dem Parameternamen angegeben werden und durch ein Gleichheitszeichen (`=`) voneinander getrennt werden. Mit `{controller=Home}` wird beispielsweise `Home` als Standardwert für `controller` definiert. Der Standardwert wird verwendet, wenn kein Wert in der Parameter-URL vorhanden ist. Routenparameter sind optional, wenn am Ende des Parameternamens ein Fragezeichen (`?`) angefügt wird, z. B. `id?`. Der Unterschied zwischen optionalen Parametern und Routenparametern mit einem Standardwert besteht darin, dass mithilfe der letzteren immer ein Wert erzeugt wird. Ein optionaler Parameter verfügt demgegenüber nur dann über einen Wert, wenn ein solcher von der Anforderungs-URL bereitgestellt wird.

Routenparameter können Einschränkungen aufweisen, die mit dem gebundenen Routenwert der URL übereinstimmen müssen. Eine *Inline-Einschränkung* für einen Routenparameter geben Sie an, indem Sie hinter dem Namen des Routenparameters einen Doppelpunkt (`:`) und einen Einschränkungsnamen hinzufügen. Wenn für die Einschränkung Argumente erforderlich sind, werden diese nach dem Einschränkungsnamen in Klammern (`(...)`) eingeschlossen. Mehrere Inline-Einschränkungen können festgelegt werden, indem ein weiterer Doppelpunkt (`:`) und Einschränkungsname hinzugefügt werden.

Der Einschränkungsname und die Argumente werden dem <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver>-Dienst übergeben, wodurch eine Instanz von <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> für die URL-Verarbeitung erstellt werden kann. In der Routenvorlage `blog/{article:minlength(10)}` wird beispielsweise die Einschränkung `minlength` mit dem Argument `10` festgelegt. Weitere Informationen zu Routeneinschränkungen und eine Liste der vom Framework bereitgestellten Einschränkungen finden Sie im Abschnitt [Referenz zu Routeneinschränkungen](#route-constraint-reference).

Die folgende Tabelle enthält Beispielvorlagen für Routen und deren Verhalten.

| Routenvorlage                           | Beispiel-URI für Übereinstimmung    | Der Anforderungs-URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | Nur für den Pfad `/hello` wird eine Übereinstimmung ermittelt.                                     |
| `{Page=Home}`                            | `/`                     | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Home` festgelegt.                                         |
| `{Page=Home}`                            | `/Contact`              | Eine Übereinstimmung wird ermittelt, und `Page` wird auf `Contact` festgelegt.                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | Stimmt mit dem `Products`-Controller und der `List`-Aktion überein.                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | Stimmt mit dem `Products`-Controller und der `Details`-Aktion (`id` auf 123 festgelegt) überein. |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | Stimmt mit dem `Home`-Controller und der `Index`-Aktion überein (`id` wird ignoriert).        |

Mit Vorlagen lässt sich Routing besonders leicht durchführen. Einschränkungen und Standardwerte können auch außerhalb der Routenvorlage angegeben werden.

> [!TIP]
> Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.

## <a name="route-constraint-reference"></a>Referenz für Routeneinschränkungen

Routeneinschränkungen werden angewendet, wenn eine Übereinstimmung mit der eingehenden URL gefunden wurde und der URL-Pfad in Routenwerten mit Token versehen wird. In der Regel wird mit Routeneinschränkungen der Routenwert der zugehörigen Vorlage geprüft. Dabei wird bestimmt, ob der Wert gültig ist. Für einige Routeneinschränkungen werden anstelle des Routenwerts andere Daten verwendet, um zu ermitteln, ob das Routing einer Anforderung möglich ist. <xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> kann beispielsweise auf der Grundlage des HTTP-Verbs eine Anforderung entweder annehmen oder ablehnen. Einschränkungen werden in Routinganforderungen und bei der Linkgenerierung verwendet.

> [!WARNING]
> Verwenden Sie keine Einschränkungen für die **Eingabeüberprüfung**. Wenn bei der **Eingabevalidierung** Einschränkungen verwendet werden, lösen ungültige Eingaben den Fehler *404 - Not Found* (404 – Nicht gefunden) aus. Stattdessen sollte jedoch der Fehler *400 - Bad Request* (400 – Fehlerhafte Anforderung) ausgelöst und mit einer entsprechenden Fehlermeldung angezeigt werden. Verwenden Sie Routeneinschränkungen nicht, um Eingaben für eine bestimmte Route zu überprüfen, sondern um ähnlichen Routen zu **unterscheiden**.

In der folgenden Tabelle werden Beispiele für Routeneinschränkungen und deren zu erwartendes Verhalten beschriebe.

| Einschränkung | Beispiel | Beispiele für Übereinstimmungen | Hinweise |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`, `-123456789` | Für jeden Integer wird eine Übereinstimmung ermittelt. |
| `bool` | `{active:bool}` | `true`, `FALSE` | Für `true` oder `false` wird eine Übereinstimmung ermittelt (keine Unterscheidung zwischen Groß-/Kleinbuchstaben). |
| `datetime` | `{dob:datetime}` | `2016-12-31`, `2016-12-31 7:32pm` | Entspricht einem gültigen `DateTime`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `decimal` | `{price:decimal}` | `49.99`, `-1,000.01` | Entspricht einem gültigen `decimal`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `double` | `{weight:double}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `double`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `float` | `{weight:float}` | `1.234`, `-1,001.01e8` | Entspricht einem gültigen `float`-Wert in der invarianten Kultur. Siehe vorherige Warnung.|
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Für einen gültigen `Guid`-Wert wird eine Übereinstimmung ermittelt. |
| `long` | `{ticks:long}` | `123456789`, `-123456789` | Für einen gültigen `long`-Wert wird eine Übereinstimmung ermittelt. |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | Die Zeichenfolge muss mindestens eine Länge von 4 Zeichen aufweisen. |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | Die Zeichenfolge darf maximal eine Länge von 8 Zeichen aufweisen. |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | Die Zeichenfolge muss genau 12 Zeichen aufweisen. |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | Die Zeichenfolge muss mindestens eine Länge von 8 und darf maximal eine Länge von 16 Zeichen aufweisen. |
| `min(value)` | `{age:min(18)}` | `19` | Der Integerwert muss mindestens 18 sein. |
| `max(value)` | `{age:max(120)}` | `91` | Der Integerwert darf nicht größer als 120 sein. |
| `range(min,max)` | `{age:range(18,120)}` | `91` | Der Integerwert muss zwischen 18 und 120 liegen. |
| `alpha` | `{name:alpha}` | `Rick` | Die Zeichenfolge muss aus mindestens einem alphabetische Zeichen bestehen (`a`-`z`, keine Unterscheidung zwischen Groß-/Kleinbuchstaben). |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | Die Zeichenfolge muss dem regulären Ausdruck entsprechen (siehe Tipp zum Definieren eines regulären Ausdrucks) |
| `required` | `{name:required}` | `Rick` | Hierdurch wird erzwungen, dass ein Wert, der kein Parameter ist, für die URL-Generierung vorhanden sein muss. |

Auf einen einzelnen Parameter können mehrere durch Doppelpunkte getrennte Einschränkungen angewendet werden. Durch die folgende Einschränkung wird ein Parameter beispielsweise auf einen Integerwert größer oder gleich 1 beschränkt:

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist. Die vom Framework bereitgestellten Routeneinschränkungen ändern nicht die Werte, die in Routenwerten gespeichert sind. Alle Routenwerte, die aus der URL analysiert werden, werden als Zeichenfolgen gespeichert. Durch die `float`-Einschränkung wird beispielsweise versucht, den Routenwert in einen Gleitkommawert zu konvertieren. Mit dem konvertierten Wert wird allerdings nur überprüft, ob eine Umwandlung überhaupt möglich ist.

## <a name="regular-expressions"></a>Reguläre Ausdrücke

Im ASP.NET Core-Framework wird dem Konstruktor für reguläre Ausdrücke `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant` hinzugefügt. Eine Beschreibung dieser Member finden Sie unter <xref:System.Text.RegularExpressions.RegexOptions>.

In regulären Ausdrücken werden Trennzeichen und Token verwendet, die auch beim Routing und in der Programmiersprache C# in ähnlicher Weise verwendet werden. Token, die reguläre Ausdrücke enthalten, müssen mit einem Escapezeichen versehen werden. Wenn Sie den regulären Ausdruck `^\d{3}-\d{2}-\d{4}$` für das Routing verwenden möchten, muss der Ausdruck `\`-Zeichen (einzelner Schrägstrich) enthalten, die in der C#-Quelldatei in der Zeichenfolge als `\\`-Zeichen (doppelter Schrägstrich) angegeben sind, damit die `\`-Zeichenfolge mit einem Escapezeichen versehen wird. Dies ist nicht erforderlich, wenn [ausführliche Zeichenfolgenliterale](/dotnet/csharp/language-reference/keywords/string) verwendet werden. Wenn Sie Trennzeichen für Routenparameter mit Escapezeichen versehen möchten (`{`, `}`, `[`, `]`), geben Sie jedes Zeichen im Ausdruck doppelt ein (`{{`, `}`, `[[`, `]]`). In der folgenden Tabelle werden reguläre Ausdrücke und Ausdrücke aufgeführt, die mit Escapezeichen versehen sind.

| Regulärer Ausdruck    | Mit Escapezeichen versehener regulärer Ausdruck     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

Beim Routing verwendete reguläre Ausdrücke beginnen oft mit einem Caretzeichen (`^`) und stellen die Startposition der Zeichenfolge dar. Die Ausdrücke enden häufig mit einem Dollarzeichen (`$`) und stellen das Ende der Zeichenfolge dar. Mit den Zeichen `^` und `$` wird sichergestellt, dass der reguläre Ausdruck mit dem vollständigen Routenparameterwert übereinstimmt. Ohne die Zeichen `^` und `$` werden mit dem regulären Ausdruck alle Teilzeichenfolgen ermittelt, was häufig nicht gewünscht ist. In der folgenden Tabelle finden Sie Beispiele für reguläre Ausdrücke. Außerdem wird erklärt, warum ein Abgleich erfolgreich ist oder fehlschlägt.

| expression   | Zeichenfolge    | Match | Kommentar               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | 123abc456 | Ja   | Teilzeichenfolge stimmt überein     |
| `[a-z]{2}`   | mz        | Ja   | Ausdruck stimmt überein    |
| `[a-z]{2}`   | MZ        | Ja   | keine Unterscheidung zwischen Groß-/Kleinbuchstaben    |
| `^[a-z]{2}$` | hello     | Nein    | siehe Erläuterungen zu `^` und `$` oben |
| `^[a-z]{2}$` | 123abc456 | Nein    | siehe Erläuterungen zu `^` und `$` oben |

Weitere Informationen zur Syntax von regulären Ausdrücken finden Sie unter [Sprachelemente für reguläre Ausdrücke – Kurzübersicht](/dotnet/standard/base-types/regular-expression-language-quick-reference).

Einen regulären Ausdruck können Sie verwenden, um einen Parameter auf zulässige Werte einzuschränken. Mit `{action:regex(^(list|get|create)$)}` werden beispielsweise für den `action`-Routenwert nur die Werte `list`, `get` oder `create` abgeglichen. Wenn die Zeichenfolge `^(list|get|create)$` dem Einschränkungswörterbuch übergeben wird, führt dies zum gleichen Ergebnis. Auch Einschränkungen, die dem zugehörigen Wörterbuch hinzugefügt werden und mit keiner vorgegebenen Einschränkung übereinstimmen , werden als reguläre Ausdrücke behandelt. Dies gilt allerdings nicht für Inline-Einschränkungen in einer Vorlage.

## <a name="custom-route-constraints"></a>Benutzerdefinierte Routeneinschränkungen

Zusätzlich zu den integrierten Routeneinschränkungen können benutzerdefinierte Routeneinschränkungen durch Implementierung der <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle erstellt werden. Die <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Schnittstelle enthält eine einzelne Methode, `Match`, die `true` zurückgibt, wenn die Einschränkung erfüllt wird, und andernfalls `false`.

Zum Verwenden einer benutzerdefinierten <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> muss der Routeneinschränkungstyp bei der <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> der App im Dienstcontainer der App registriert werden. Eine <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> ist ein Wörterbuch, das Routeneinschränkungsschlüssel <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>-Implementierungen zuordnet, die diese Einschränkungen überprüfen. Die <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> einer App kann in `Startup.ConfigureServices` entweder als Teil eines [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*)-Aufrufs oder durch direktes Konfigurieren von <xref:Microsoft.AspNetCore.Routing.RouteOptions> mit `services.Configure<RouteOptions>` aktualisiert werden. Zum Beispiel:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

Die Einschränkung kann dann auf die übliche Weise mit dem bei der Registrierung des Einschränkungstyps angegebenen Namen auf Routen angewendet werden. Zum Beispiel:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>Referenz für URL-Generierung

Im folgenden Beispiel wird gezeigt, wie Sie einen Link zu einer Route unter Berücksichtigung eines vorhandenen Wörterbuchs mit Routenwerten und einer <xref:Microsoft.AspNetCore.Routing.RouteCollection> erstellen.

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

Die <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath>-Eigenschaft, die am Ende des obigen Beispiels erstellt wird, hat den Wert `/package/create/123`. Das Wörterbuch stellt die Routenwerte von `operation` und `id` aus der Vorlage „Track Package Route“ (`package/{operation}/{id}`) bereit. Weitere Informationen finden Sie im Beispielcode im Abschnitt [Verwenden von Routingmiddleware](#use-routing-middleware) oder in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples).

Der zweite Parameter für den <xref:Microsoft.AspNetCore.Routing.VirtualPathContext>-Konstruktor ist eine Auflistung von *Umgebungswerten*. Mit diesen lässt sich leicht die Anzahl der Werte einschränken, die ein Entwickler innerhalb eines Anforderungskontexts angeben muss. Die aktuellen Routenwerte der aktuellen Anforderung werden bei der Linkgenerierung als Umgebungswerte behandelt. In einer ASP.NET Core-MVC-App müssen Sie innerhalb der `About`-Aktion von `HomeController` nicht den Controllerroutenwert angeben, um eine Verknüpfung mit der `Index`-Aktion herzustellen, da der Umgebungswert von `Home` verwendet wird.

Umgebungswerte, die mit keinem Parameter übereinstimmen, werden ignoriert. Außerdem werden Umgebungswerte ignoriert, wenn ein explizit angegebener Wert den betreffenden Umgebungswert außer Kraft setzt. Der Abgleich in der URL wird von links nach rechts ausgeführt.

Explizit bereitgestellte Werte, für die keine Übereinstimmungen mit einem Routensegment ermittelt werden, werden der Abfragezeichenfolge hinzugefügt. In der folgenden Tabelle werden die Ergebnisse dargestellt, die aus der Verwendung der Routenvorlage `{controller}/{action}/{id?}` hervorgehen:

| Umgebungswerte                     | Explizite Werte                        | Ergebnis                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| controller = "Home"                | action = "About"                       | `/Home/About`           |
| controller = "Home"                | controller = "Order", action = "About" | `/Order/About`          |
| controller = "Home", color = "Red" | action = "About"                       | `/Home/About`           |
| controller = "Home"                | action = "About", color = "Red"        | `/Home/About?color=Red` |

Wenn eine Route über einen Standardwert verfügt, der keinem Parameter entspricht, und dieser Wert explizit bereitgestellt wird, muss er dem Standardwert entsprechen:

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

Für diese Route wird nur dann ein Link generiert, wenn die übereinstimmenden Werte für `controller` und `action` bereitgestellt werden.

## <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente (z.B. `[Route("/x{token}y")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet. Unter [diesem Code](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) finden Sie eine ausführliche Erklärung für das Abgleichen komplexer Segmente. Das [Codebeispiel](https://github.com/dotnet/aspnetcore/blob/v2.2.13/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293) wird von ASP.NET Core nicht verwendet, aber es bietet eine gute Erklärung komplexer Segmente.

::: moniker-end
