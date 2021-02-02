---
title: Übersicht über die ASP.NET Core-Authentifizierung
author: mjrousos
description: Hier erfahren Sie mehr zur Authentifizierung in ASP.NET Core.
ms.author: riande
ms.custom: mvc
ms.date: 1/24/2021
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
uid: security/authentication/index
ms.openlocfilehash: 72036e9c4c92ee5dd82ac4a67e766fb0e5c8f924
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057290"
---
# <a name="overview-of-aspnet-core-authentication"></a>Übersicht über die ASP.NET Core-Authentifizierung

Von [Mike Rousos](https://github.com/mjrousos)

Als Authentifizierung wird der Vorgang bezeichnet, mit dem die Identität eines Benutzers festgestellt wird. [Autorisierung](xref:security/authorization/introduction) ist der Vorgang, mit dem ermittelt wird, ob ein Benutzer Zugriff auf eine Ressource hat. In ASP.NET Core wird die Authentifizierung vom `IAuthenticationService` verarbeitet, der von der [Middleware](xref:fundamentals/middleware/index) für die Authentifizierung verwendet wird. Der Authentifizierungsdienst verwendet registrierte Authentifizierungshandler, um Aktionen im Zusammenhang mit der Authentifizierung auszuführen. Beispiele für Aktionen im Zusammenhang mit der Authentifizierung:

* Die Authentifizierung eines Benutzers.
* Reagieren auf den Versuch eines nicht authentifizierten Benutzers, auf eine eingeschränkte Ressource zuzugreifen.

Die registrierten Authentifizierungshandler und ihre Konfigurationsoptionen werden als „Schemas“ bezeichnet.

Authentifizierungsschemas werden festgelegt, indem die Authentifizierungsdienste bei `Startup.ConfigureServices` registriert werden:

* Durch Aufrufen einer schemaspezifischen Erweiterungsmethode nach einem Aufruf von `services.AddAuthentication` (z. B. `AddJwtBearer` oder `AddCookie`). Diese Erweiterungsmethoden verwenden [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*), um Schemas mit den geeigneten Einstellungen zu registrieren.
* In seltenen Fällen wird [AuthenticationBuilder.AddScheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder.AddScheme*) direkt aufgerufen.

Im folgenden Code werden beispielsweise die Authentifizierungsdienste und -handler für cookie- und JWT-Bearer-Authentifizierungsschemas registriert:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(JwtBearerDefaults.AuthenticationScheme, options => Configuration.Bind("JwtSettings", options))
    .AddCookie(CookieAuthenticationDefaults.AuthenticationScheme, options => Configuration.Bind("CookieSettings", options));
```

Der `AddAuthentication`-Parameter `JwtBearerDefaults.AuthenticationScheme` ist der Name des Schemas, das standardmäßig verwendet werden soll, wenn kein bestimmtes Schema angefordert wird.

Wenn mehrere Schemas verwendet werden, können Autorisierungsrichtlinien (oder Autorisierungsattribute) [das Authentifizierungsschema (oder die Authentifizierungsschemas) angeben](xref:security/authorization/limitingidentitybyscheme), von denen sie zur Authentifizierung des Benutzers abhängig sind. Im Beispiel oben kann das cookieauthentifizierungsschema verwendet werden, indem der Name angegeben wird (standardmäßig `CookieAuthenticationDefaults.AuthenticationScheme`, obwohl beim Aufruf von `AddCookie` ein anderer Name angegeben werden kann).

In bestimmten Fällen erfolgt der Aufruf von `AddAuthentication` automatisch durch andere Erweiterungsmethoden. Wenn Sie z. B. [ASP.NET Core Identity](xref:security/authentication/identity) verwenden, wird `AddAuthentication` intern aufgerufen.

Die Authentifizierungsmiddleware wird in `Startup.Configure` durch Aufrufen der <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>-Erweiterungsmethode für den `IApplicationBuilder` der App hinzugefügt. Durch Aufrufen von `UseAuthentication` wird die Middleware registriert, die die zuvor registrierten Authentifizierungsschemas verwendet. Rufen Sie `UseAuthentication` vor jeder Middleware auf, die voraussetzt, dass Benutzer authentifiziert sind. Wenn Sie das Endpunktrouting verwenden, muss der Aufruf von `UseAuthentication`:

* nach `UseRouting` stehen, damit die Routeninformationen für Authentifizierungsentscheidungen verfügbar sind.
* vor `UseEndpoints` stehen, damit Benutzer vor dem Zugriff auf die Endpunkte authentifiziert werden.

## <a name="authentication-concepts"></a>Authentifizierungskonzepte

Die Authentifizierung ist verantwortlich für die Bereitstellung der <xref:System.Security.Claims.ClaimsPrincipal>-Klasse für die Autorisierung, um Berechtigungsentscheidungen zu treffen. Es gibt mehrere Ansätze zum Thema Authentifizierungsschema, um auszuwählen, welcher Authentifizierungshandler für die Erstellung der richtigen Ansprüche zuständig ist:

  * [Das Authentifizierungsschema](xref:security/authorization/limitingidentitybyscheme), das auch im nächsten Abschnitt erläutert wird
  * Das Standardauthentifizierungsschema, das im nächsten Abschnitt erläutert wird
  * Direktes Festlegen von [HttpContext.User](xref:Microsoft.AspNetCore.Http.HttpContext.User)

Es gibt keine automatische Überprüfung von Schemas. Wenn das Standardschema nicht angegeben wird, muss das Schema im Autorisierungsattribut angegeben werden. andernfalls wird der folgende Fehler ausgelöst:

  InvalidOperationException: No authenticationScheme was specified, and there was no DefaultAuthenticateScheme found. The default schemes can be set using either AddAuthentication(string defaultScheme) or AddAuthentication(Action&lt;AuthenticationOptions&gt; configureOptions) (Es wurde kein authenticationScheme angegeben, und DefaultAuthenticateScheme wurde nicht gefunden. Die Standardschemas können entweder mit AddAuthentication(Zeichenfolge defaultScheme) oder AddAuthentication(Aktion<AuthenticationOptions> configureOptions) festgelegt werden.)

### <a name="authentication-scheme"></a>Authentifizierungsschema

Das [Authentifizierungsschema](xref:security/authorization/limitingidentitybyscheme) kann auswählen, welcher Authentifizierungshandler zum Generieren der richtigen Ansprüche verantwortlich ist. Weitere Informationen finden Sie unter [Autorisieren mit einem bestimmten Schema](xref:security/authorization/limitingidentitybyscheme).

Ein Authentifizierungsschema ist ein Name, der Folgendem entspricht:

* Einem Authentifizierungshandler.
* Optionen zum Konfigurieren dieser bestimmten Instanz des Handlers.

Schemas sind als Mechanismus nützlich, um auf die Authentifizierungs-, Aufforderungs- und Unterbindungsverhalten des zugeordneten Handlers zu verweisen. Eine Autorisierungsrichtlinie kann beispielsweise anhand von Schemanamen angeben, welche Authentifizierungsschemas zum Authentifizieren des Benutzers verwendet werden sollen. Beim Konfigurieren der Authentifizierung ist es üblich, das Standardauthentifizierungsschema anzugeben. Das Standardschema wird verwendet, sofern eine Ressource kein bestimmtes Schema anfordert. Weitere Möglichkeiten:

* Angeben unterschiedlicher Standardschemas für Authentifizierungs-, Aufforderungs- und Unterbindungsaktionen.
* Kombinieren mehrerer Schemas mithilfe von [Richtlinienschemas](xref:security/authentication/policyschemes).

### <a name="authentication-handler"></a>Authentifizierungshandler

Für einen Authentifizierungshandler gilt Folgendes:

* Es handelt sich um einen Typ, der das Verhalten eines Schemas implementiert.
* Er wird von <xref:Microsoft.AspNetCore.Authentication.IAuthenticationHandler> oder <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> abgeleitet.
* Er trägt die Hauptverantwortung für die Authentifizierung von Benutzern.

In Abhängigkeit von der Konfiguration des Authentifizierungsschemas und vom Kontext der eingehenden Anforderung führen Authentifizierungshandler folgende Aktionen aus:

* Sie erstellen <xref:Microsoft.AspNetCore.Authentication.AuthenticationTicket>-Objekte, die die Identität des Benutzers darstellen, wenn die Authentifizierung erfolgreich ist.
* Sie geben „kein Ergebnis“ oder „Fehler“ zurück, wenn die Authentifizierung nicht erfolgreich ist.
* Sie verfügen über Methoden für Aufforderungs- und Unterbindungsaktionen, wenn Benutzer versuchen, auf Ressourcen zuzugreifen:
  * Sie sind für den Zugriff nicht autorisiert (Unterbindung).
  * Sie sind nicht authentifiziert (Aufforderung).

### <a name="authenticate"></a>Authenticate

Die Authentifizierungsaktion eines Authentifizierungsschemas ist für das Erstellen der Identität des Benutzers basierend auf dem Anforderungskontext verantwortlich. Sie gibt ein <xref:Microsoft.AspNetCore.Authentication.AuthenticateResult>, das angibt, ob die Authentifizierung erfolgreich war, und in diesem Fall außerdem die Identität des Benutzers in einem Authentifizierungsticket zurück. Siehe <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync%2A>. Beispiele für die Authentifizierung:

* Ein cookieauthentifizierungsschema, bei dem die Identität des Benutzers aus cookies erstellt wird.
* Ein JWT-Bearer-Schema, das ein JWT-Bearertoken zum Erstellen der Identität des Benutzers deserialisiert und überprüft.

### <a name="challenge"></a>Herausforderung

Eine Authentifizierungsaufforderung wird durch die Autorisierung aufgerufen, wenn ein nicht authentifizierter Benutzer einen Endpunkt anfordert, für den eine Authentifizierung erforderlich ist. Eine Authentifizierungsaufforderung wird z. B. ausgegeben, wenn ein anonymer Benutzer eine eingeschränkte Ressource anfordert oder auf einen Anmeldelink klickt. Die Autorisierung ruft eine Aufforderung mithilfe des/der angegebenen Authentifizierungsschema(s) oder, wenn keines angegeben ist, des Standardschemas auf. Siehe <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync%2A>. Beispiele für Authentifizierungsaufforderungen:

* Ein cookieauthentifizierungsschema, das den Benutzer auf eine Anmeldeseite umleitet.
* Ein JWT-Bearer-Schema, das ein 401-Ergebnis mit einem `www-authenticate: bearer`-Header zurückgibt.

Eine Aufforderungsaktion sollte dem Benutzer mitteilen können, welcher Authentifizierungsmechanismus für den Zugriff auf die angeforderte Ressource verwendet werden soll.

### <a name="forbid"></a>Unterbindung

Die Unterbindungsaktion eines Authentifizierungsschemas wird von der Autorisierung aufgerufen, wenn ein authentifizierter Benutzer versucht, auf eine Ressource zuzugreifen, für die er keine Berechtigung besitzt. Siehe <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync%2A>. Beispiele für Authentifizierungsunterbindungen:
* Ein cookieauthentifizierungsschema, das den Benutzer auf eine Seite umleitet, die anzeigt, dass der Zugriff unzulässig war.
* Ein JWT-Bearer-Schema, das ein 403-Ergebnis zurückgibt.
* Ein benutzerdefiniertes Authentifizierungsschema, das den Benutzer auf eine Seite umleitet, auf der er den Zugriff auf die Ressource anfordern kann.

Eine Unterbindungsaktion kann den Benutzer über Folgendes informieren:

* Er ist authentifiziert.
* Er ist nicht berechtigt, auf die angeforderte Ressource zuzugreifen.

Unter den folgenden Links finden Sie Informationen zu den Unterschieden zwischen Aufforderung und Unterbindung:

* [Aufforderung und Unterbindung mit einem funktionsfähigen Ressourcenhandler](xref:security/authorization/resourcebased#challenge-and-forbid-with-an-operational-resource-handler)
* [Unterschiede zwischen Aufforderung und Unterbindung](xref:security/authorization/secure-data#challenge)

## <a name="authentication-providers-per-tenant"></a>Authentifizierungsanbieter pro Mandant

Das ASP.NET Core-Framework verfügt nicht über eine integrierte Lösung für eine mehrinstanzenfähige Authentifizierung.
Obwohl es für Kunden sicherlich möglich ist, mithilfe der integrierten Features eine zu schreiben, empfehlen wir unseren Kunden, sich zu diesem Zweck [Orchard Core](https://www.orchardcore.net/) anzusehen.

Orchard Core ist:

* ein modulares und mehrinstanzenfähiges Open-Source-App-Framework, das mit ASP.NET Core erstellt wurde.
* ein Content Management System (CMS), das auf Grundlage dieses App-Frameworks erstellt wurde.

Im Quellcode von [Orchard Core](https://github.com/OrchardCMS/OrchardCore) finden Sie ein Beispiel für Authentifizierungsanbieter pro Mandant.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authentication/policyschemes>
* <xref:security/authorization/secure-data>
* [Globales Anfordern authentifizierter Benutzer](xref:security/authorization/secure-data#rau)
* [Issue auf GitHub zum Verwenden mehrerer Authentifizierungsschemas](https://github.com/dotnet/aspnetcore/issues/26002)
