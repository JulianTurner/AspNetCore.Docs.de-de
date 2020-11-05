---
title: Anforderungsfeatures in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, die in Schnittstellen definiert werden.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: d906474b0fd291cc4a68390f390b2bf538e21eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053708"
---
# <a name="request-features-in-aspnet-core"></a>Anforderungsfeatures in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

Die `HttpContext`-API, mit der Anwendungen und Middleware Anforderungen verarbeiten, verfügt über eine zugrunde liegende Abstraktionsebene in Form von sogenannten *Featureschnittstellen*. Jede Featureschnittstelle bietet eine differenzierte Teilmenge der Funktionen, die von `HttpContext` verfügbar gemacht werden. Diese Schnittstellen können vom Server oder der Middleware während der Verarbeitung der Anforderung hinzugefügt, geändert, umschlossen, ersetzt oder sogar entfernt werden, ohne dass der gesamte `HttpContext` erneut implementiert werden muss. Sie können auch verwendet werden, um bei Tests Funktionen zu simulieren.

## <a name="feature-collections"></a>Featuresammlung

Die <xref:Microsoft.AspNetCore.Http.HttpContext.Features>-Eigenschaft von `HttpContext` bietet Zugriff auf die Sammlung von Featureschnittstellen für die aktuelle Anforderung. Da die Featuresammlung auch im Kontext einer Anforderung änderbar ist, kann Middleware verwendet werden, um die Sammlung zu modifizieren und Unterstützung für zusätzliche Features hinzuzufügen. Einige erweiterte Features sind nur verfügbar, wenn Sie über die Featuresammlung auf die zugehörige Schnittstelle zugreifen.

## <a name="feature-interfaces"></a>Featureschnittstellen

ASP.NET Core definiert einige allgemeine HTTP-Featureschnittstellen in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> mit deren Hilfe die verschiedenen Server und Middleware die unterstützten Features ermitteln können. Server und Middleware können auch eigene Schnittstellen mit zusätzlichen Funktionen bereitstellen.

Die meisten Featureschnittstellen bieten optionale, ergänzende Funktionen, und die zugehörigen `HttpContext`-APIs stellen Standardwerte bereit, wenn das Feature nicht vorhanden ist. Einige Schnittstellen werden im Folgenden als erforderlich dargestellt, da sie wichtige Anforderungs- und Antwortfunktionen bereitstellen und implementiert werden müssen, um die Anforderung zu verarbeiten.

Die folgenden Featureschnittstellen stammen von <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Definiert die Struktur einer HTTP-Anforderung, einschließlich Protokoll, Pfad, Abfragezeichenfolge, Header und Text. Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Definiert die Struktur einer HTTP-Antwort, einschließlich Statuscode, Header und Antworttext. Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Definiert verschiedene Methoden zum Schreiben des Antworttexts, wobei ein `Stream`, ein `PipeWriter` oder eine Datei verwendet wird. Dieses Feature ist erforderlich, um Anforderungen zu verarbeiten. Hierdurch werden `IHttpResponseFeature.Body` und `IHttpSendFileFeature` ersetzt.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Enthält den der Anforderung derzeit zugeordneten <xref:System.Security.Claims.ClaimsPrincipal>.

<xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Dient zum Analysieren und Zwischenspeichern eingehender HTTP- und mehrteiliger Formularübermittlungen.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Steuert, ob synchrone E/A-Vorgänge für den Anforderungs- oder Antworttext zulässig sind.

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Definiert Methoden zum Deaktivieren des Pufferns von Anforderungen und/oder Antworten.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Definiert Eigenschaften für die Verbindungs-ID, lokale und Remoteadressen sowie Ports.

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Steuert die maximal zulässige Größe des Anforderungstexts für die aktuelle Anforderung.

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

`IHttpRequestBodyDetectionFeature`: Gibt an, ob die Anforderung einen Text enthalten kann.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Fügt eine Eigenschaft hinzu, die implementiert werden kann, um eine Anforderung eindeutig zu identifizieren.

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Definiert Unterstützung für das Abbrechen von Verbindungen oder das Erkennen einer verfrühten Beendigung einer Anforderung, z. B. durch eine abgebrochene Verbindung mit dem Client.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Ermöglicht Zugriff auf die Nachspannheader der Anforderung, sofern vorhanden.

<xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Wird zum Senden von Zurücksetzungsmeldungen für Protokolle verwendet, die diese unterstützen, z. B. HTTP/2 oder HTTP/3.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Ermöglicht der Anwendung, Nachspannheader in der Antwort anzugeben, sofern diese unterstützt werden.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Definiert eine Methode zum asynchronen Senden von Dateien.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Definiert Unterstützung für [HTTP-Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), mit denen der Client festlegen kann, welche zusätzlichen Protokolle er verwenden möchte, wenn der Server Protokolle wechseln möchte.

<xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Definiert eine API zur Unterstützung von Websockets.

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Steuert, ob über HTTPS-Verbindungen eine Antwortkomprimierung verwendet werden soll.

::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Speichert die <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items>-Sammlung für den Anwendungszustand pro Anforderung.

<xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Analysiert die Abfragezeichenfolge und speichert sie zwischen.
   
::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Stellt den Anforderungstext als einen <xref:System.IO.Pipelines.PipeReader> dar.
 
::: moniker-end

<xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Analysiert die `Cookie`-Headerwerte der Anforderung und speichert sie zwischen.

<xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Steuert, wie die cookie der Antwort auf den `Set-Cookie`-Header angewandt werden.

::: moniker range=">= aspnetcore-2.2"

<xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Dieses Feature ermöglicht den Zugriff auf Servervariablen der Anforderung, z. B. die von IIS bereitgestellten.

::: moniker-end
   
<xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Ermöglicht den Zugriff auf einen <xref:System.IServiceProvider> mit bereichsspezifischen Anforderungsdiensten.

<xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Definiert die Abstraktionen `ISessionFactory` und <xref:Microsoft.AspNetCore.Http.ISession> zur Unterstützung von Benutzersitzungen. `ISessionFeature` wird von <xref:Microsoft.AspNetCore.Session.SessionMiddleware> implementiert (siehe <xref:fundamentals/app-state>).

<xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Definiert eine API zum Abrufen von Clientzertifikaten.

<xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Definiert Methoden zum Arbeiten mit TLS-Tokenbindungsparametern.
   
::: moniker range=">= aspnetcore-2.2"
   
<xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Dient zum Abfragen, Gewähren und Widerrufen der Benutzereinwilligung zur Speicherung von Benutzerinformationen im Zusammenhang mit Aktivitäten und Funktionen der Website.
   
::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
