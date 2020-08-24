---
title: Neuerungen in ASP.NET Core 3.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 3.0.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: aspnetcore-3.0
ms.openlocfilehash: a53ba22c301b04aafa1eeb53d91f82f90dab0bad
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631549"
---
# <a name="whats-new-in-aspnet-core-30"></a>Neuerungen in ASP.NET Core 3.0

In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 3.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.

## Blazor

Blazor ist ein Framework in ASP.NET Core zum Erstellen von interaktiven clientseitigen Webbenutzeroberflächen mit .NET:

* Erstellen Sie umfassende interaktive Benutzeroberflächen mit C# anstatt mit JavaScript.
* Gemeinsames Verwenden von server- und clientseitiger App-Logik, die in .NET geschrieben wurde.
* Rendern der Benutzeroberfläche als HTML und CSS für umfassende Browserunterstützung (einschließlich mobiler Browser).

Vom Blazor-Framework unterstützte Szenarien:

* Wiederverwendbare Benutzeroberflächenkomponenten (Razor-Komponenten)
* Clientseitiges Routing
* Komponentenlayouts
* Unterstützung für Abhängigkeitsinjektion
* Formulare und Überprüfung
* Erstellen von Komponentenbibliotheken mit Razor-Klassenbibliotheken
* JavaScript-Interoperabilität

Weitere Informationen finden Sie unter <xref:blazor/index>.

### Blazor Server

Blazor entkoppelt die Komponentenrenderinglogik von Aktualisierungen der Benutzeroberfläche. Blazor Server bietet Unterstützung zum Hosten von Razor-Komponenten in einer ASP.NET Core-App auf dem Server. Aktualisierungen der Benutzeroberfläche werden über eine SignalR-Verbindung verarbeitet. Blazor Server wird in ASP.NET Core 3.0 unterstützt.

### <a name="no-locblazor-webassembly-preview"></a>Blazor WebAssembly (Vorschau)

Blazor-Apps können auch mit einer WebAssembly-basierten .NET-Runtime direkt im Browser ausgeführt werden. Blazor WebAssembly befindet sich in der Vorschau und wird in ASP.NET Core 3.0 *nicht* unterstützt. Blazor WebAssembly wird in einem zukünftigen Release von ASP.NET Core unterstützt.

### <a name="no-locrazor-components"></a>Razor-Komponenten

Blazor-Apps setzen sich aus Komponenten zusammen. Komponenten sind eigenständige Elemente einer Benutzeroberfläche, z. B. eine Seite, ein Dialogfeld oder ein Formular. Komponenten sind normale .NET-Klassen, die die Renderinglogik für die Benutzeroberfläche sowie clientseitige Ereignishandler definieren. Damit können Sie umfangreiche interaktive Web-Apps ohne JavaScript erstellen.

Komponenten in Blazor werden üblicherweise mit der Razor-Syntax erstellt, einer natürlichen Mischung aus HTML und C#. Razor-Komponenten ähneln Razor Pages und MVC-Ansichten dahingehend, dass sie ebenfalls Razor verwenden. Im Gegensatz zu Pages und Ansichten, die auf einem Anforderung/Antwort-Modell basieren, werden Komponenten speziell für die Verarbeitung der Benutzeroberflächengestaltung verwendet.

## <a name="grpc"></a>gRPC

[gRPC](https://grpc.io/):

* Ein beliebtes, hochleistungsfähiges RPC-Framework (Remote Procedure Call, Remoteprozeduraufruf).
* Bietet einen strikten Contract First-Ansatz für die API-Entwicklung.
* Verwendet moderne Technologien wie z. B.:

  * HTTP/2 für den Transport.
  * Protokollpuffer als Sprache zur Schnittstellenbeschreibung.
  * Format der binären Serialisierung.
* Bietet beispielsweise folgende Features:

  * Authentifizierung
  * Bidirektionales Streaming und Flusssteuerung.
  * Stornierung und Timeouts.

Die gRPC-Funktionalität in ASP.NET Core 3.0 umfasst Folgendes:

* [Grpc.AspNetCore:](https://www.nuget.org/packages/Grpc.AspNetCore) Dies ist ein ASP.NET Core-Framework zum Hosten von gRPC-Diensten. gRPC in ASP.NET Core lässt sich in ASP.NET Core-Standardfeatures wie Protokollierung, Abhängigkeitsinjektion (Dependency Injection, DI), Authentifizierung und Autorisierung integrieren.
* [Grpc.Net.Client:](https://www.nuget.org/packages/Grpc.Net.Client) Dies ist ein gRPC-Client für .NET Core, der auf dem bekannten `HttpClient` aufbaut.
* [Grpc.Net.ClientFactory:](https://www.nuget.org/packages/Grpc.Net.ClientFactory) Dies ist eine gRPC-Clientintegration mit `HttpClientFactory`.

Weitere Informationen finden Sie unter <xref:grpc/index>.

## SignalR

Anweisungen zur Migration finden Sie unter [Aktualisieren von SignalR-Code](xref:migration/22-to-30#signalr). SignalR verwendet jetzt `System.Text.Json`, um JSON-Nachrichten zu serialisieren bzw. zu deserialisieren. Anleitungen zum Wiederherstellen des `Newtonsoft.Json`-basierten Serialisierungsmoduls finden Sie unter [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson) (Wechseln zu Newtonsoft.Json).

In den JavaScript- und .NET-Clients für SignalR wurde Unterstützung für eine automatische erneute Verbindungsherstellung hinzugefügt. Standardmäßig versucht der Client sofort, die Verbindung wiederherzustellen, und wiederholt den Versuch nötigenfalls nach 2, 10 und 30 Sekunden. Wenn der Client die Verbindung erfolgreich wiederherstellt, erhält er eine neue Verbindungs-ID. Die automatische erneute Herstellung einer Verbindung kann optional aktiviert werden:

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Die Intervalle für die Wiederherstellung einer Verbindung können durch Übergabe eines Arrays aus millisekundenbasierten Zeitspannen angegeben werden:

```javascript
.withAutomaticReconnect([0, 3000, 5000, 10000, 15000, 30000])
//.withAutomaticReconnect([0, 2000, 10000, 30000]) The default intervals.
```

Zur vollständigen Steuerung der Wiederherstellungsintervalle kann eine benutzerdefinierte Implementierung übergeben werden.

Wenn die Verbindung nicht wiederhergestellt werden kann, passiert nach dem letzten Intervall Folgendes:

* Der Client betrachtet die Verbindung als offline.
* Der Client versucht nicht mehr, die Verbindung wiederherzustellen.

Aktualisieren Sie die App-Benutzeroberfläche während der Verbindungsversuche, um den Benutzer darüber zu informieren, dass versucht wird, erneut eine Verbindung herzustellen.

Um bei einer unterbrochenen Verbindung Feedback auf der Benutzeroberfläche bereitstellen zu können, wurde die SignalR-Client-API um die folgenden Ereignishandler ergänzt:

* `onreconnecting`:  Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu deaktivieren oder Benutzer darüber zu informieren, dass die App offline ist.
* `onreconnected`: Bietet Entwicklern die Möglichkeit, die Benutzeroberfläche zu aktualisieren, sobald die Verbindung wiederhergestellt wurde.

Der folgende Code verwendet `onreconnecting`, um die Benutzeroberfläche während der Verbindungsversuche zu aktualisieren:

```javascript
connection.onreconnecting((error) => {
    const status = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messageInput").disabled = true;
    document.getElementById("sendButton").disabled = true;
    document.getElementById("connectionStatus").innerText = status;
});
```

Der folgende Code verwendet `onreconnected`, um die Benutzeroberfläche nach erfolgreich hergestellter Verbindung zu aktualisieren:

```javascript
connection.onreconnected((connectionId) => {
    const status = `Connection reestablished. Connected.`;
    document.getElementById("messageInput").disabled = false;
    document.getElementById("sendButton").disabled = false;
    document.getElementById("connectionStatus").innerText = status;
});
```

SignalR 3.0 und höher bietet eine benutzerdefinierte Ressource für Autorisierungshandler, wenn eine Hubmethode eine Autorisierung erfordert. Die Ressource ist eine Instanz von `HubInvocationContext`. Der `HubInvocationContext` umfasst Folgendes:

* `HubCallerContext`
* Den Namen der aufgerufenen Hubmethode.
* Argumente für die Hubmethode.

Sehen Sie sich das folgende Beispiel einer Chatroom-App an, die mehreren Mitgliedern einer Organisation die Anmeldung über Azure Active Directory ermöglicht. Jede Person mit einem Microsoft-Konto kann sich beim Chat anmelden, aber nur Mitglieder der besitzenden Organisation können Benutzer sperren oder den Chatverlauf anderer Benutzer anzeigen. Die App könnte bestimmte Funktionen für bestimmte Benutzer einschränken.

```csharp
public class DomainRestrictedRequirement :
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>,
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement,
        HubInvocationContext resource)
    {
        if (context.User?.Identity?.Name == null)
        {
            return Task.CompletedTask;
        }

        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name))
        {
            context.Succeed(requirement);
        }

        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName, string currentUsername)
    {
        if (hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase))
        {
            return currentUsername.Equals("bob42@jabbr.net", StringComparison.OrdinalIgnoreCase);
        }

        return currentUsername.EndsWith("@jabbr.net", StringComparison.OrdinalIgnoreCase));
    }
}
```

Im vorherigen Code dient `DomainRestrictedRequirement` als benutzerdefinierte `IAuthorizationRequirement`. Da der Ressourcenparameter `HubInvocationContext` übergeben wird, kann die interne Logik folgende Aktionen ausführen:

* Sie kann den Kontext untersuchen, in dem der Hub aufgerufen wird.
* Sie kann Entscheidungen darüber treffen, ob einem Benutzer die Ausführung einzelner Hubmethoden gestattet wird.

Einzelne Hubmethoden können mit dem Namen der Richtlinie gekennzeichnet werden, die der Code zur Laufzeit prüft. Wenn Clients versuchen, einzelne Hubmethoden aufzurufen, wird der `DomainRestrictedRequirement`-Handler ausgeführt und steuert den Zugriff auf die Methoden. Basierend auf der Art und Weise, in der `DomainRestrictedRequirement` den Zugriff steuert, kann Folgendes passieren:

* Alle angemeldeten Benutzer können die `SendMessage`-Methode aufrufen.
* Nur Benutzer, die sich mit einer `@jabbr.net`-E-Mail-Adresse angemeldet haben, können den Verlauf anderer Benutzer anzeigen.
* Nur `bob42@jabbr.net` kann Benutzer aus dem Chatroom ausschließen.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}
```

Die Erstellung der `DomainRestricted`-Richtlinie kann Folgendes umfassen:

* Fügen Sie die neue Richtlinie in *Startup.cs* hinzu.
* Stellen Sie die benutzerdefinierte Anforderung `DomainRestrictedRequirement` als Parameter bereit.
* Registrieren Sie `DomainRestricted` bei der Autorisierungsmiddleware.

```csharp
services
    .AddAuthorization(options =>
    {
        options.AddPolicy("DomainRestricted", policy =>
        {
            policy.Requirements.Add(new DomainRestrictedRequirement());
        });
    });
```

SignalR-Hubs verwenden [Endpunktrouting](xref:fundamentals/routing). Eine SignalR-Hubverbindung wurde zuvor explizit eingerichtet:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});
```

In der vorherigen Version mussten Entwickler Controller, Razor-Seiten und Hubs an verschiedenen Orten miteinander verknüpfen. Explizite Verbindungen führen zu einer Reihe nahezu identischer Routingsegmente:

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("hubs/chat");
});

app.UseRouting(routes =>
{
    routes.MapRazorPages();
});
```

SignalR 3.0-Hubs können per Endpunktrouting weitergeleitet werden. Beim Endpunktrouting kann in der Regel das gesamte Routing in `UseRouting` konfiguriert werden:

```csharp
app.UseRouting(routes =>
{
    routes.MapRazorPages();
    routes.MapHub<ChatHub>("hubs/chat");
});
```

In ASP.NET Core 3.0 SignalR wurde Folgendes hinzugefügt:

Streaming vom Client zum Server. Mit dem Streaming vom Client zum Server können serverseitige Methoden Instanzen von `IAsyncEnumerable<T>` oder `ChannelReader<T>` akzeptieren. Im folgenden C#-Beispiel empfängt die `UploadStream`-Methode im Hub einen Zeichenfolgenstream vom Client:

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        // process content
    }
}
```

.NET-Client-Apps können entweder eine `IAsyncEnumerable<T>`-oder eine `ChannelReader<T>`-Instanz als `stream`-Argument der oben genannten `UploadStream`-Hubmethode übergeben.

Nachdem die `for`-Schleife abgeschlossen ist und die lokale Funktion existiert, wird der Streamabschluss gesendet:

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
}

await connection.SendAsync("UploadStream", clientStreamData());
```

JavaScript-Client-Apps verwenden SignalR `Subject` (oder [RxJS Subject](https://rxjs.dev/api/index/class/Subject)) für das `stream`-Argument der oben genannten Hubmethode `UploadStream`.

```javascript
let subject = new signalR.Subject();
await connection.send("StartStream", "MyAsciiArtStream", subject);
```

Der JavaScript-Code könnte die `subject.next`-Methode verwenden, um Zeichenfolgen zu verarbeiten, sobald diese erfasst werden und zum Senden an den Server bereit sin.

```javascript
subject.next("example");
subject.complete();
```

Mit Code wie in den beiden obigen Codeausschnitten lassen sich Echtzeitstreamingfunktionen erzeugen.

## <a name="new-json-serialization"></a>Neue JSON-Serialisierung

ASP.NET Core 3.0 verwendet jetzt standardmäßig <xref:System.Text.Json> zur JSON-Serialisierung:

* Lese- und Schreibvorgänge in JSON erfolgen asynchron.
* Der Code ist für UTF-8-Text optimiert.
* In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.

Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support).

## <a name="new-no-locrazor-directives"></a>Neue Razor-Anweisungen

Die folgende Liste enthält neue Razor-Anweisungen:

* [`@attribute`](xref:mvc/views/razor#attribute): die Anweisung `@attribute` wendet das angegebene Attribut auf die Klasse der generierten Seite oder Ansicht an. Beispielsweise `@attribute [Authorize]`.
* [`@implements`](xref:mvc/views/razor#implements): Die `@implements`-Anweisung implementiert eine Schnittstelle für die generierte Klasse. Beispielsweise `@implements IDisposable`.

## <a name="no-locidentityserver4-supports-authentication-and-authorization-for-web-apis-and-spas"></a>IdentityServer4 unterstützt Authentifizierung und Autorisierung für Web-APIs und SPAs.

ASP.NET Core 3.0 bietet eine Authentifizierung in Single-Page-Webanwendungen (SPAs) unter Verwendung der Unterstützung für die Web-API-Autorisierung. ASP.NET Core Identity zum Authentifizieren und Speichern von Benutzern wird mit [IdentityServer4](https://identityserver.io/) zur Implementierung von OpenID Connect kombiniert.

IdentityServer4 ist ein OpenID Connect- und OAuth 2.0-Framework für ASP.NET Core 3.0. Es ermöglicht die folgenden Sicherheitsfunktionen:

* Authentifizierung als Dienst
* Einmaliges Anmelden und einmaliges Abmelden für mehrere Anwendungstypen
* Zugriffssteuerung für APIs
* Federation Gateway

Weitere Informationen finden Sie in der [IdentityServer4-Dokumentation](http://docs.identityserver.io/en/latest/index.html) oder unter [Authentifizierung und Autorisierung für SPAs](xref:security/authentication/identity/spa).

## <a name="certificate-and-kerberos-authentication"></a>Zertifikat- und Kerberos-Authentifizierung

Die Zertifikatsauthentifizierung erfordert Folgendes:

* Der Server muss so konfiguriert werden, dass er Zertifikate akzeptiert.
* Die Authentifizierungsmiddleware muss in `Startup.Configure` hinzugefügt werden.
* Der Zertifikatauthentifizierungsdienst muss in `Startup.ConfigureServices` hinzugefügt werden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Die Optionen für die Zertifikatauthentifizierung umfassen folgende Möglichkeiten:

* Akzeptieren selbstsignierter Zertifikate.
* Überprüfen auf Zertifikatswiderruf.
* Überprüfen, ob das angebotene Zertifikat die richtigen Nutzungsflags enthält.

Aus den Zertifikateigenschaften wird ein Standardbenutzerprinzipal erstellt. Der Benutzerprinzipal enthält ein Ereignis, das das Ergänzen oder Ersetzen des Prinzipals ermöglicht. Weitere Informationen finden Sie unter <xref:security/authentication/certauth>.

Die [Windows-Authentifizierung](/windows-server/security/windows-authentication/windows-authentication-overview) wurde auf Linux und macOS erweitert. In vorherigen Versionen war die Windows-Authentifizierung auf [IIS](xref:host-and-deploy/iis/index) und [HttpSys](xref:fundamentals/servers/httpsys) beschränkt. In ASP.NET Core 3.0 besitzt [Kestrel](xref:fundamentals/servers/kestrel) die Möglichkeit, Negotiate, [Kerberos](/windows-server/security/kerberos/kerberos-authentication-overview) und [NTLM unter Windows](/windows-server/security/kerberos/ntlm-overview), Linux und macOS für Hosts zu verwenden, die der Windows-Domäne beigetreten sind. Die Kestrel-Unterstützung für diese Authentifizierungsschemas wird durch das Paket [Microsoft.AspNetCore.Authentication.Negotiate NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) bereitgestellt. Wie bei anderen Authentifizierungsdiensten auch sollte erst die Authentifizierung auf App-Ebene und dann der Dienst konfiguriert werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
        .AddNegotiate();
    // Other service configuration removed.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();
    // Other app configuration removed.
}
```

Hostanforderungen:

* Windows-Hosts müssen dem Benutzerkonto, das die App hostet, [Dienstprinzipalnamen](/windows/win32/ad/service-principal-names) hinzufügen.
* Linux- und macOS-Computer müssen der Domäne beigetreten sein.
  * Für den Webprozess müssen Dienstprinzipalnamen erstellt werden.
  * [Keytab-Dateien](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) müssen generiert und auf dem Hostcomputer konfiguriert werden.

Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.

## <a name="template-changes"></a>Änderungen an Vorlagen

Aus den Vorlagen für Webbenutzeroberflächen (Razor Pages, MVC mit Controller und Ansichten) wurde Folgendes entfernt:

* Die Benutzeroberfläche für die Zustimmung zu cookies ist nicht mehr enthalten. Informationen dazu, wie Sie die Zustimmung zu cookies in einer aus einer ASP.NET Core 3.0-Vorlage generierten App aktivieren, finden Sie unter <xref:security/gdpr>.
* Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs mehr dafür verwendet. Weitere Informationen finden Sie unter [Scripts and related static assets are now referenced as local files instead of using CDNs based on the current environment (aspnet/AspNetCore.Docs #14350)](https://github.com/dotnet/AspNetCore.Docs/issues/14350) (Skripts und damit zusammenhängende statische Ressourcen werden jetzt als lokale Dateien referenziert, es werden keine CDNs basierend auf der aktuellen Umgebung mehr verwendet).

Die Angular-Vorlage wurde auf Angular 8 aktualisiert.

Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung. Eine neue Vorlagenoption in Visual Studio bietet Vorlagenunterstützung für Seiten und Ansichten. Übergeben Sie beim Erstellen einer RCL aus einer Vorlage in einer Befehlsshell die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`).

## <a name="generic-host"></a>Generischer Host

Die ASP.NET Core 3.0-Vorlagen verwenden <xref:fundamentals/host/generic-host>. In vorherigen Versionen wurde <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> verwendet. Durch Verwendung des generischen .NET Core-Hosts (<xref:Microsoft.Extensions.Hosting.HostBuilder>) lässt sich eine bessere Integration von ASP.NET Core-Apps in andere, nicht webspezifische Serverszenarios erzielen. Weitere Informationen finden Sie unter [HostBuilder ersetzt WebHostBuilder](xref:migration/22-to-30?view=aspnetcore-2.2#hostbuilder-replaces-webhostbuilder).

### <a name="host-configuration"></a>Konfiguration des Hosts

Vor ASP.NET Core 3.0 wurden Umgebungsvariablen mit dem Präfix `ASPNETCORE_` für die Hostkonfiguration des Webhosts geladen. In Version 3.0 wird `AddEnvironmentVariables` zum Laden von Umgebungsvariablen mit dem Präfix `DOTNET_` zur Hostkonfiguration mit `CreateDefaultBuilder` verwendet.

### <a name="changes-to-startup-constructor-injection"></a>Änderungen an der Startkonstruktorinjektion

Der generische Host unterstützt nur die folgenden Typen für die Injektion des `Startup`-Konstruktors:

* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

Alle Dienste können weiterhin direkt als Argumente in die `Startup.Configure`-Methode eingefügt werden. Weitere Informationen finden Sie unter [Generic Host restricts Startup constructor injection (aspnet/Announcements #353)](https://github.com/aspnet/Announcements/issues/353) (Generischer Host schränkt Startkonstruktorinjektion ein).

## <a name="kestrel"></a>Kestrel

* Die Kestrel-Konfiguration wurde für die Migration zum generischen Host aktualisiert. In Version 3.0 ist Kestrel im Webhostgenerator konfiguriert, der durch `ConfigureWebHostDefaults` bereitgestellt wird.
* Verbindungsadapter wurden aus Kestrel entfernt und durch Verbindungsmiddleware ersetzt. Diese ähnelt der HTTP-Middleware in der ASP.NET Core-Pipeline, ist aber für Verbindungen auf niedrigerer Ebene konzipiert.
* Die Kestrel-Transportschicht wurde in `Connections.Abstractions` als öffentliche Schnittstelle verfügbar gemacht.
* Eine Mehrdeutigkeit zwischen Headern und Trailern wurde aufgelöst, indem nachstehende Header in eine neue Sammlung verschoben wurden.
* APIs mit synchronen E/A-Vorgängen, wie z. B. `HttpRequest.Body.Read`, sind eine häufige Quelle für einen Ressourcenmangel im Thread, der zu App-Abstürzen führt. In 3.0 ist `AllowSynchronousIO` standardmäßig deaktiviert.

Weitere Informationen finden Sie unter <xref:migration/22-to-30#kestrel>.

## <a name="http2-enabled-by-default"></a>HTTP/2 standardmäßig aktiviert

HTTP/2 ist in Kestrel für HTTPS-Endpunkte standardmäßig aktiviert. Die HTTP/2-Unterstützung für IIS oder HTTP.sys ist aktiviert, wenn dies vom Betriebssystem unterstützt wird.

## <a name="eventcounters-on-request"></a>EventCounters auf Anforderung

Die Hostingereignisquelle `Microsoft.AspNetCore.Hosting` gibt die folgenden neuen <xref:System.Diagnostics.Tracing.EventCounter>-Typen zurück, die im Zusammenhang mit eingehenden Anforderungen stehen:

* `requests-per-second`
* `total-requests`
* `current-requests`
* `failed-requests`

## <a name="endpoint-routing"></a>Endpunktrouting

Das Endpunktrouting, das den reibungslosen Einsatz von Frameworks (z. B. MVC) mit Middleware ermöglicht, wurde erweitert:

* Die Reihenfolge von Middlewarekomponenten und Endpunkten kann in der `Startup.Configure`-Pipeline für die Anforderungsverarbeitung konfiguriert werden.
* Endpunkte und Middlewarekomponenten lassen sich gut mit anderen ASP.NET Core-basierten Technologien wie z. B. Integritätsprüfungen kombinieren.
* Endpunkte können sowohl in der Middleware als auch in MVC eine Richtlinie wie beispielsweise CORS oder Autorisierung implementieren.
* Filter und Attribute können in Controllermethoden platziert werden.

Weitere Informationen finden Sie unter <xref:fundamentals/routing#routing-basics>.

## <a name="health-checks"></a>Integritätsprüfungen

Integritätsprüfungen verwenden das Endpunktrouting mit dem generischen Host. Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

Für Endpunkte für Integritätsprüfungen gilt Folgendes:

* Sie geben mindestens einen zugelassenen Host oder Port an.
* Sie erfordern Autorisierung.
* Sie erfordern CORS.

Weitere Informationen finden Sie in den folgenden Artikeln:

* <xref:migration/22-to-30#health-checks>
* <xref:host-and-deploy/health-checks>

## <a name="pipes-on-httpcontext"></a>Pipes in HttpContext

Es ist jetzt möglich, mit der <xref:System.IO.Pipelines>-API den Anforderungstext zu lesen und den Antworttext schreiben. Die <!-- <xref:Microsoft.AspNetCore.Http.HttpRequest.BodyReader> --> `HttpRequest.BodyReader`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeReader> bereit, der zum Lesen des Anforderungstexts verwendet werden kann. Die <!-- <xref:Microsoft.AspNetCore.Http.> --> `HttpResponse.BodyWriter`-Eigenschaft stellt einen <xref:System.IO.Pipelines.PipeWriter> bereit, der zum Schreiben des Antworttexts verwendet werden kann. `HttpRequest.BodyReader` funktioniert analog zum `HttpRequest.Body`-Stream. `HttpResponse.BodyWriter` funktioniert analog zum `HttpResponse.Body`-Stream.

<!-- indirectly related, https://github.com/dotnet/docs/pull/14414 won't be published by 9/23  -->

## <a name="improved-error-reporting-in-iis"></a>Verbesserte Fehlerberichterstellung in IIS

Startfehler beim Hosten von ASP.NET Core-Apps in IIS erzeugen jetzt umfangreichere Diagnosedaten. Diese Fehler werden mit Stapelüberwachung im Windows-Ereignisprotokoll gemeldet, wann immer möglich. Darüber hinaus werden alle Warnungen, Fehler und Ausnahmefehler im Windows-Ereignisprotokoll protokolliert.

## <a name="worker-service-and-worker-sdk"></a>Workerdienst und Worker SDK

.NET Core 3.0 führt eine neue Vorlage für die Workerdienst-App ein. Diese Vorlage dient als Ausgangspunkt für das Schreiben von zeitintensiven Diensten in .NET Core.

Weitere Informationen finden Sie unter:

* [.NET Core Workers as Windows Services](https://devblogs.microsoft.com/aspnet/net-core-workers-as-windows-services/) (.NET Core-Worker als Windows-Dienste)
* <xref:fundamentals/host/hosted-services>
* <xref:host-and-deploy/windows-service>

## <a name="forwarded-headers-middleware-improvements"></a>Verbesserungen an Middleware für weitergeleitete Header

In früheren Versionen von ASP.NET Core war der Aufruf von <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> und <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> problematisch, wenn die Bereitstellung auf einem Azure Linux-Computer oder hinter einem anderen Reverseproxy als IIS erfolgt war. Die Problembehebung für frühere Versionen ist unter [Das Schema für Linux- und Nicht-IIS-Reverseproxys weiterleiten](xref:host-and-deploy/proxy-load-balancer#forward-the-scheme-for-linux-and-non-iis-reverse-proxies) dokumentiert.

Dieses Szenario wurde in ASP.NET Core 3.0 behoben. Der Host aktiviert die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer#forwarded-headers-middleware-options), wenn die Umgebungsvariable `ASPNETCORE_FORWARDEDHEADERS_ENABLED` auf `true` festgelegt ist. `ASPNETCORE_FORWARDEDHEADERS_ENABLED` ist in unseren Containerimages auf `true` festgelegt.

## <a name="performance-improvements"></a>Leistungsverbesserungen

ASP.NET Core 3.0 enthält viele Verbesserungen, die die Arbeitsspeichernutzung reduzieren und den Durchsatz erhöhen:

* Bei Verwendung des integrierten Containers für die Abhängigkeitsinjektion für bereichsbezogene Dienste wird weniger Arbeitsspeicher verbraucht.
* Im gesamten Framework, einschließlich Middlewareszenarien und Routing, wurden Zuweisungen reduziert.
* Für Websocketverbindungen wird weniger Arbeitsspeicher verbraucht.
* Bei HTTPS-Verbindungen wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.
* Es steht ein neues optimiertes und vollständig asynchrones JSON-Serialisierungsmodul zur Verfügung.
* Bei der Formularanalyse wurde die Arbeitsspeichernutzung reduziert und der Durchsatz verbessert.

## <a name="aspnet-core-30-only-runs-on-net-core-30"></a>ASP.NET Core 3.0 funktioniert nur in .NET Core 3.0

Ab ASP.NET Core 3.0 ist .NET Framework kein unterstütztes Zielframework mehr. Projekte, die auf .NET Framework abzielen, können mit dem [.NET Core 2.1 LTS-Release](https://dotnet.microsoft.com/download/dotnet-core/2.1) weiterhin mit vollständiger Unterstützung ausgeführt werden. Die meisten auf ASP.NET Core 2.1.x bezogenen Pakete werden unbegrenzt unterstützt, auch nach dem dreijährigen LTS-Zeitraum für .NET Core 2.1.

Informationen zur Migration finden Sie unter [Portieren Ihres Codes von .NET Framework auf .NET Core](/dotnet/core/porting/).

## <a name="use-the-aspnet-core-shared-framework"></a>Verwenden des freigegebenen ASP.NET Core-Frameworks

Das freigegebene ASP.NET Core 3.0-Framework, das im [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app) enthalten ist, erfordert kein explizites `<PackageReference />`-Element in der Projektdatei mehr. Bei Verwendung des `Microsoft.NET.Sdk.Web` SDK in der Projektdatei wird automatisch auf das freigegebene Framework verwiesen:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

## <a name="assemblies-removed-from-the-aspnet-core-shared-framework"></a>Aus dem freigegebenen ASP.NET Core-Framework entfernte Assemblys

Die wichtigsten Assemblys, die aus dem freigegebenen ASP.NET Core 3.0-Framework entfernt wurden:

* [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) (Json.NET). Informationen zum Hinzufügen von Json.NET zu ASP.NET Core 3.0 finden Sie unter [Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format](xref:web-api/advanced/formatting#add-newtonsoftjson-based-json-format-support). ASP.NET Core 3.0 führt `System.Text.Json` zum Lesen und Schreiben von JSON ein. Weitere Informationen finden Sie im Abschnitt [Neue JSON-Serialisierung](#new-json-serialization) im vorliegenden Dokument.
* [Entity Framework Core](/ef/core/)

Eine vollständige Liste der Assemblys, die aus dem freigegebenen Framework entfernt wurden, finden Sie unter [Assemblies being removed from Microsoft.AspNetCore.App 3.0](https://github.com/dotnet/AspNetCore/issues/3755) (Assemblys, die aus Microsoft.AspNetCore.App 3.0 entfernt werden). Weitere Informationen zu den Beweggründen für diese Änderung finden Sie unter [Breaking changes to Microsoft.AspNetCore.App in 3.0](https://github.com/aspnet/Announcements/issues/325) (Breaking Changes an Microsoft.AspNetCore.App in Version 3.0) und [A first look at changes coming in ASP.NET Core 3.0](https://devblogs.microsoft.com/aspnet/a-first-look-at-changes-coming-in-asp-net-core-3-0/) (Ein erster Blick auf die Änderungen in ASP.NET Core 3.0).

<!-- 
## Additional information
For the complete list of changes, see the [ASP.NET Core 3.0 Release Notes](WHERE IS THIS????).
-->
 