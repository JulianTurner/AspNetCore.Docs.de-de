---
title: Neuerungen in ASP.NET Core 2.1
author: isaac2004
description: Informationen zu den neuen Features in ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-2.1
ms.openlocfilehash: 199ca62f71df5c0ee5ca2f78caf53ad8d6be1179
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854560"
---
# <a name="whats-new-in-aspnet-core-21"></a>Neuerungen in ASP.NET Core 2.1

In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 2.1 hervorgehoben und Links zu relevanter Dokumentation bereitgestellt.

## SignalR

SignalR wurde für ASP.NET Core 2.1 umgeschrieben. ASP.NET Core SignalR bietet eine Reihe von Verbesserungen:

* Ein vereinfachtes Modell für horizontale Skalierung.
* Einen neuen JavaScript-Client ohne jQuery-Abhängigkeit.
* Ein neues kompaktes, binäres Protokoll, das auf MessagePack basiert.
* Unterstützung für benutzerdefinierte Protokolle.
* Ein neues Streaming-Antwortmodell.
* Unterstützung für Clients basierend auf Bare-WebSockets.

Weitere Informationen finden Sie unter [ASP.NET Core SignalR](xref:signalr/introduction).

## <a name="no-locrazor-class-libraries"></a>Razor-Klassenbibliotheken

ASP.NET Core 2.1 erleichtert die Erstellung und Einbeziehung einer Razor-basierten Benutzeroberfläche in einer Bibliothek sowie die mehrere Projekte übergreifende Freigabe. Mit dem neuen Razor SDK können Razor-Dateien in einem Klassenbibliotheksprojekt erstellt werden, das in ein NuGet-Paket gepackt werden kann. Ansichten und Seiten in Bibliotheken werden automatisch ermittelt und können von der App überschrieben werden. Folgen der Integration der Razor-Kompilierung in den Build:

* Die App wird deutlich schneller gestartet.
* Schnelle Updates von Razor-Ansichten und -Seiten zur Laufzeit sind weiterhin als Bestandteil eines iterativen Entwicklungsworkflows verfügbar.

Weitere Informationen finden Sie unter [Erstellen einer wiederverwendbaren Benutzeroberfläche mithilfe des Razor-Klassenbibliotheksprojekts](xref:razor-pages/ui-class).

## <a name="no-locidentity-ui-library--scaffolding"></a>Bibliothek und Gerüst der Identity-Benutzeroberfläche

ASP.NET Core 2.1 stellt [ASP.NET Core Identity](xref:security/authentication/identity) als eine [Razor-Klassenbibliothek](xref:razor-pages/ui-class) bereit. Apps, die über Identity verfügen, können das neue Gerüst Identity anwenden, um die in der Identity-Klassenbibliothek Razor enthaltenen Quellcode selektiv hinzuzufügen. Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können. Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren. Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek Identity.

Apps **ohne** Authentifizierung können das Gerüst Identity anwenden, um das Paket der Razor-Klassenbibliothek Identity hinzuzufügen. Sie können Code der Klassenbibliothek Identity auswählen, der generiert werden soll.

Weitere Informationen finden Sie unter [Gerüst Identity in ASP.NET Core-Projekten](xref:security/authentication/scaffold-identity).

## <a name="https"></a>HTTPS

Durch die verstärkte Ausrichtung auf Sicherheit und Datenschutz ist die Aktivierung von HTTPS für Web-Apps von großer Bedeutung. Die Erzwingung von HTTPS ist im Web zunehmend strenger. Websites, die kein HTTPS verwenden, gelten als unsicher. Browser (Chromium, Mozilla) beginnen zu erzwingen, dass Web-Features über einen sicheren Kontext verwendet werden müssen. Für die [DSGVO](xref:security/gdpr) ist die Verwendung von HTTPS aus Datenschutzgründen erforderlich. Während die Verwendung von HTTPS in der Produktion wichtig ist, können durch die Verwendung von HTTPS in der Entwicklung Probleme bei der Bereitstellung verhindert werden (z.B. unsichere Links). ASP.NET Core 2.1 umfasst eine Reihe von Verbesserungen, die die Verwendung von HTTPS in der Entwicklung und die Konfiguration von HTTPS in der Produktion erleichtern. Weitere Informationen finden Sie unter [Erzwingen von HTTPS](xref:security/enforcing-ssl).

### <a name="on-by-default"></a>Standardmäßig aktiviert

HTTPS ist jetzt standardmäßig aktiviert, um die Entwicklung sicherer Websites zu erleichtern. Seit Version 2.1 lauscht Kestrel unter `https://localhost:5001`, wenn ein lokales Entwicklungszertifikat vorhanden ist. Ein Entwicklungszertifikat wird erstellt:

* Im Rahmen des Eindrucks des .NET Core SDK beim ersten Ausführen, wenn Sie das SDK zum ersten Mal verwenden.
* Manuell mithilfe des neuen `dev-certs`-Tools.

Führen Sie `dotnet dev-certs https --trust` aus, um den Zertifikat vertrauen zu können.

### <a name="https-redirection-and-enforcement"></a>HTTPS-Umleitung und -Erzwingung

Web-Apps müssen in der Regel HTTP und HTTPS lauschen, leiten dann aber sämtlichen HTTP-Verkehr an HTTPS um. In Version 2.1 wurde eine spezielle Middleware für die HTTPS-Umleitung eingeführt, die Umleitungen auf intelligente Weise basierend auf dem Vorhandensein einer Konfiguration oder gebundener Server-Ports durchführt.

Darüber hinaus kann die Verwendung von HTTPS mit dem [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) erzwungen werden. Das HSTS gibt Browsern die Anweisung, immer über HTTPS auf die Website zuzugreifen. ASP.NET Core 2.1 fügt HSTS-Middleware hinzu, die Optionen für das max. Alter, Unterdomänen und die Preload-Liste des HSTS unterstützt.

### <a name="configuration-for-production"></a>Konfiguration für die Produktion

In der Produktion muss HTTPS explizit konfiguriert sein. In Version 2.1 wurde ein Konfigurationsschema für die Konfiguration von HTTPS für Kestrel hinzugefügt. Apps können für folgende Verwendung konfiguriert werden:

* Mehrere Endpunkte einschließlich der URLs. Weitere Informationen finden Sie unter [Kestrel web server implementation: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).
* Das für HTTPS zu verwendende Zertifikat aus einer Datei auf dem Datenträger oder aus einem Zertifikatspeicher.

## <a name="gdpr"></a>DSGVO

ASP.NET Core stellt APIs und Vorlagen für die Erfüllung einiger der Anforderungen der [Datenschutz-Grundverordnung (DSGV=)](https://www.eugdpr.org/) bereit. Weitere Informationen finden Sie unter [DSGVO-Unterstützung in ASP.NET Core](xref:security/gdpr). Eine [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) veranschaulicht, wie die meisten DSGVO-Erweiterungspunkte verwendet und getestet werden können. Ferner enthält sie zu den Vorlagen von ASP.NET Core 2.1 hinzugefügte APIs.

## <a name="integration-tests"></a>Integrationstests

Es wurde ein neues Paket eingeführt, durch das die Testerstellung und -ausführung optimiert wird. Das Paket [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) behandelt folgende Aufgaben:

* Es kopiert die Abhängigkeitsdatei ( *\*.deps*) aus der getesteten App in den Ordner *bin* des Testprojekts.
* Es legt das Inhaltsstammelement auf das Projektstammelement der App fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung des Tests gefunden werden.
* Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings der getesteten App mit [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) bereit.

Im folgenden Test wird mithilfe von [xUnit](https://xunit.github.io/) überprüft, ob die Indexseite mit einem erfolgreichen Statuscode und mit dem richtigen Content-Type-Header geladen wird:

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

Weitere Informationen finden Sie im Thema [Integrationstests](xref:test/integration-tests).

## <a name="apicontroller-actionresultt"></a>[ApiController], ActionResult\<T>

In ASP.NET Core 2.1 werden neue Programmierungskonventionen hinzugefügt, die eine Erstellung bereinigter und beschreibender Web-APIs vereinfacht. `ActionResult<T>` ist ein neuer Typ, der hinzugefügt wurde, damit eine App einen Antworttyp oder ein anderes Aktionsergebnis zurückgeben kann (vergleichbar mit IActionResult), während der Antworttyp weiterhin angegeben wird. Das Attribut `[ApiController]` wurde auch hinzugefügt, um Web-API-spezifischen Konventionen und Verhaltensweisen zuzustimmen.

Weitere Informationen finden Sie unter [Erstellen von Web-APIs mit ASP.NET Core](xref:web-api/index).

## <a name="ihttpclientfactory"></a>IHttpClientFactory

ASP.NET Core 2.1 umfasst einen neuen `IHttpClientFactory`-Dienst, durch den die Konfiguration und der Verbrauch von `HttpClient`-Instanzen in Apps vereinfacht wird. `HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden könnten. Die Factory:

* Sie macht die Registrierung von `HttpClient`-Instanzen pro benanntem Client intuitiver.
* Sie implementiert einen Polly-Handler, der zulässt, dass Polly-Richtlinien für „Retry“, „CircuitBreakers“ usw. verwendet werden.

Weitere Informationen finden Sie unter [Initiieren von HTTP-Anforderungen](xref:fundamentals/http-requests).

## <a name="kestrel-libuv-transport-configuration"></a>Kestrel-Libuv-Transportkonfiguration

Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets. Weitere Informationen finden Sie unter [Kestrel web server implementation: Libuv-Transportkonfiguration](xref:fundamentals/servers/kestrel#libuv-transport-configuration).

## <a name="generic-host-builder"></a>Generator für generische Hosts

Der Generator für generische Hosts (`HostBuilder`) wurde eingeführt. Dieser Generator kann für Apps verwendet werden, die keine HTTP-Anforderungen verarbeiten (Messaging, Hintergrundaufgaben usw.).

Weitere Informationen finden Sie unter [Generischer .NET-Host](xref:fundamentals/host/generic-host).

## <a name="updated-spa-templates"></a>Aktualisierte SPA-Vorlagen

Die Vorlagen der Single-Page-Webanwendung für Angular, React und React & Redux werden aktualisiert, damit die Standardprojektstrukturen und Buildsysteme für die einzelnen Frameworks verwendet werden können.

Die Angular-Vorlage basiert auf der Angular-CLI, während die React-Vorlagen auf „create-react-app“ basieren.

Weitere Informationen finden Sie unter:

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="no-locrazor-pages-search-for-no-locrazor-assets"></a>Razor Pages-Suche nach Razor-Objekten

In Version 2.1 sucht Razor Pages in den folgenden Verzeichnissen in der aufgeführten Reihenfolge nach Razor-Objekten (z. B. Layouts und Teilausführungen):

1. Im aktuellen Ordner „Pages“.
1. */Pages/Shared/*
1. */Views/Shared/*

## <a name="no-locrazor-pages-in-an-area"></a>Razor Pages in einem Bereich

Razor Pages unterstützen jetzt [Bereiche](xref:mvc/controllers/areas). Erstellen Sie für die Anzeige eines Beispiels für Bereiche eine neue Razor Pages-Web-App mit individuellen Benutzerkonten. Eine Razor Pages-Web-App mit individuellen Benutzerkonten enthält */Areas/Identity/Pages*.

## <a name="mvc-compatibility-version"></a>MVC-Kompatibilitätsversion

Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen.

Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.

## <a name="migrate-from-20-to-21"></a>Migrieren von 2.0 zu 2.1

Siehe [Migrieren von ASP.NET Core 2.0 zu 2.1](xref:migration/20_21).

## <a name="additional-information"></a>Zusätzliche Informationen

Die vollständige Liste der Änderungen finden Sie unter [ASP.NET Core 2.1 – Anmerkungen zu dieser Version](https://github.com/dotnet/aspnetcore/releases/tag/2.1.0).
