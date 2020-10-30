---
title: Erzwingen von HTTPS in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie HTTPS/TLS in einer ASP.net Core-Web-App anfordern.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: e473da9a7cbd91a601ad4af0c7c02c7f576f348c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051121"
---
# <a name="enforce-https-in-aspnet-core"></a>Erzwingen von HTTPS in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Dokument zeigt Folgendes:

* Erfordert HTTPS für alle Anforderungen.
* Leitet alle HTTP-Anforderungen an HTTPS um.

Eine API kann verhindern, dass ein Client sensible Daten bei der ersten Anforderung sendet.

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a>API-Projekte
>
> Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten. `RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten. API-Clients können Umleitungen von http zu HTTPS nicht verstehen oder darauf verweisen. Diese Clients senden möglicherweise Informationen über http. Web-APIs sollten folgende Aktionen ausführen:
>
> * Nicht an http lauschen.
> * Schließen Sie die Verbindung mit dem Statuscode 400 (ungültige Anforderung), und verarbeiten Sie die Anforderung nicht.
>
> ## <a name="hsts-and-api-projects"></a>Hsts-und API-Projekte
>
> Die Standard-API-Projekte enthalten keine [hsts](#hsts) , da hsts im Allgemeinen eine reine Browser Anweisung ist. Andere Aufrufer, z. b. Telefon-oder Desktop-Apps, gehorchen **nicht** der Anweisung. Auch innerhalb von Browsern hat ein einzelner authentifizierter API-API-Rückruf Risiken für unsichere Netzwerke. Der sichere Ansatz besteht darin, API-Projekte so zu konfigurieren, dass Sie nur über HTTPS lauschen und darauf reagieren.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a>API-Projekte
>
> Verwenden Sie **nicht** "Requirements [httpsattribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) " für Web-APIs, die vertrauliche Informationen erhalten. `RequireHttpsAttribute` verwendet HTTP-Statuscodes, um Browser von http zu HTTPS umzuleiten. API-Clients können Umleitungen von http zu HTTPS nicht verstehen oder darauf verweisen. Diese Clients senden möglicherweise Informationen über http. Web-APIs sollten folgende Aktionen ausführen:
>
> * Nicht an http lauschen.
> * Schließen Sie die Verbindung mit dem Statuscode 400 (ungültige Anforderung), und verarbeiten Sie die Anforderung nicht.

::: moniker-end

## <a name="require-https"></a>Erzwingen von HTTPS

Es wird empfohlen, dass die Web-Apps für die Produktion ASP.net Core:

* HTTPS-Umleitungs Middleware ( <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> ) zum Umleiten von HTTP-Anforderungen an HTTPS.
* Hsts-Middleware ([usehsts](#http-strict-transport-security-protocol-hsts)) zum Senden von http-hsts-Headern (Strict Transport Security Protocol) an Clients.

> [!NOTE]
> Apps, die in einer Reverseproxykonfiguration bereitgestellt werden, ermöglichen dem Proxy das Verarbeiten der Verbindungssicherheit (HTTPS) Wenn der Proxy auch die HTTPS-Umleitung verarbeitet, muss keine HTTPS-Umleitungs Middleware verwendet werden. Wenn der Proxy Server auch das Schreiben von hsts-Headern verarbeitet (z. b. System [eigene hsts-Unterstützung in IIS 10,0 (1709) oder](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)höher), ist hsts-Middleware für die APP nicht erforderlich. Weitere Informationen finden Sie unter [Ablehnen von HTTPS/hsts bei der Projekt Erstellung](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>Usehttpsredirection

Der folgende Code ruft `UseHttpsRedirection` in der- `Startup` Klasse auf:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

Der vorangehende markierte Code:

* Verwendet den Standardwert [httpsredirectionoptions. redirectstatus Code](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Verwendet den Standardwert [httpsredirectionoptions. httpsport](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), es sei denn, er wurde durch die `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [iserveraddressesfeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)überschrieben.

Es wird empfohlen, temporäre Umleitungen anstelle von permanenten Umleitungen zu verwenden. Das Zwischenspeichern von Links kann in Entwicklungsumgebungen zu instabiles Verhalten führen. Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, finden Sie weitere Informationen im Abschnitt [configure permanent Umleitungen in Production](#configure-permanent-redirects-in-production) . Wir empfehlen die Verwendung von [hsts](#http-strict-transport-security-protocol-hsts) , um Clients zu signalisieren, dass nur sichere Ressourcenanforderungen an die APP gesendet werden sollen (nur in der Produktion).

### <a name="port-configuration"></a>Portkonfiguration

Ein Port muss verfügbar sein, damit die Middleware eine unsichere Anforderung an HTTPS umleiten muss. Wenn kein Port verfügbar ist:

* Umleitung zu HTTPS erfolgt nicht.
* Die Middleware protokolliert die Warnung "der HTTPS-Port für die Umleitung konnte nicht bestimmt werden".

Geben Sie den HTTPS-Port an, indem Sie einen der folgenden Ansätze verwenden:

* Legen Sie [httpsredirectionoptions. httpsport](#options)fest.

::: moniker range=">= aspnetcore-3.0"

* Festlegen der `https_port` [Host Einstellung](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):

  * In der Host Konfiguration.
  * Durch Festlegen der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable.
  * Durch Hinzufügen eines Eintrags auf oberster Ebene in *appsettings.json* :

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)verwenden. Die Umgebungsvariable konfiguriert den Server. Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* Festlegen der `https_port` [Host Einstellung](xref:fundamentals/host/web-host#https-port):

  * In der Host Konfiguration.
  * Durch Festlegen der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable.
  * Durch Hinzufügen eines Eintrags auf oberster Ebene in *appsettings.json* :

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* Geben Sie einen Port mit dem sicheren Schema an, indem Sie die [ASPNETCORE_URLS-Umgebungsvariable](xref:fundamentals/host/web-host#server-urls)verwenden. Die Umgebungsvariable konfiguriert den Server. Die Middleware ermittelt indirekt den HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> . Diese Vorgehensweise funktioniert nicht in reverseproxybereitstellungen.

::: moniker-end

* Legen Sie in Entwicklung in *launchsettings.js* eine HTTPS-URL fest. Aktivieren Sie HTTPS, wenn IIS Express verwendet wird.

* Konfigurieren Sie einen HTTPS-URL-Endpunkt für eine Public-Edge-Bereitstellung von [Kestrel](xref:fundamentals/servers/kestrel) -Server oder [HTTP.sys](xref:fundamentals/servers/httpsys) -Server. Nur **ein HTTPS-Port** wird von der APP verwendet. Die Middleware ermittelt den Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .

> [!NOTE]
> Wenn eine app in einer Reverseproxykonfiguration ausgeführt wird, ist <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> nicht verfügbar. Legen Sie den Port mithilfe eines der anderen in diesem Abschnitt beschriebenen Ansätze fest.

### <a name="edge-deployments"></a>Edgebereitstellungen 

Wenn Kestrel oder HTTP.sys als öffentlicher Edge-Server verwendet wird, muss Kestrel oder HTTP.sys für das lauschen an beiden Optionen konfiguriert werden:

* Der sichere Port, an den der Client umgeleitet wird (in der Regel 443 in der Produktionsumgebung und 5001 in der Entwicklung).
* Der unsichere Port (in der Regel 80 in der Produktionsumgebung und 5000 in der Entwicklung).

Der Client muss auf den unsicheren Port zugreifen können, damit die APP eine unsichere Anforderung empfängt und den Client an den sicheren Port umleitet.

Weitere Informationen finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) oder <xref:fundamentals/servers/httpsys> .

### <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Für jede Firewall zwischen dem Client und dem Server müssen auch Kommunikationsports für den Datenverkehr geöffnet sein.

Wenn Anforderungen in einer Reverseproxykonfiguration weitergeleitet werden, verwenden Sie die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) , bevor Sie die HTTPS-Umleitungs Die Middleware für weitergeleitete Header aktualisiert `Request.Scheme` mit dem- `X-Forwarded-Proto` Header. Die Middleware ermöglicht das ordnungsgemäße Funktionieren von Umleitungs-URIs und anderen Sicherheitsrichtlinien. Wenn die Middleware für weitergeleitete Header nicht verwendet wird, empfängt die Back-End-App möglicherweise nicht das richtige Schema und befindet sich in einer Umleitungs Schleife. Eine gängige Fehlermeldung für den Endbenutzer besteht darin, dass zu viele Umleitungen aufgetreten sind.

Wenn Sie Azure App Service bereitstellen, befolgen Sie die Anweisungen im [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Optionen

Der folgende markierte Code ruft [addhttpsredirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) auf, um Middleware-Optionen zu konfigurieren:


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


`AddHttpsRedirection`Das Aufrufen von ist nur erforderlich, um die Werte von oder zu ändern `HttpsPort` `RedirectStatusCode` .

Der vorangehende markierte Code:

* Legt [httpsredirectionoptions. redirectstatus Code](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) auf fest <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> . Dies ist der Standardwert. Verwenden Sie die Felder der- <xref:Microsoft.AspNetCore.Http.StatusCodes> Klasse für Zuweisungen zu `RedirectStatusCode` .
* Legt den HTTPS-Port auf 5001 fest.

#### <a name="configure-permanent-redirects-in-production"></a>Konfigurieren dauerhafter Umleitungen in der Produktion

Die Middleware sendet standardmäßig ein [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) -Wert mit allen Umleitungen. Wenn Sie lieber einen permanenten Umleitungs Statuscode senden möchten, wenn sich die app in einer nicht Entwicklungsumgebung befindet, packen Sie die middlewareoptions-Konfiguration in eine bedingte Prüfung für eine nicht Entwicklungsumgebung.

::: moniker range=">= aspnetcore-3.0"

Beim Konfigurieren von Diensten in *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Beim Konfigurieren von Diensten in *Startup.cs* :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a>Alternativer Ansatz der HTTPS-Umleitungs Middleware

Eine Alternative zur Verwendung der HTTPS-Umleitungs Middleware ( `UseHttpsRedirection` ) ist die Verwendung der URL-Umschreib enden Middleware ( `AddRedirectToHttps` ). `AddRedirectToHttps` kann auch den Statuscode und den Port festlegen, wenn die Umleitung ausgeführt wird. Weitere Informationen finden Sie unter [URL-Umschreib Ende Middleware](xref:fundamentals/url-rewriting).

Beim Umleiten an HTTPS ohne die Anforderung zusätzlicher Umleitungs Regeln empfiehlt sich die Verwendung der in diesem Thema beschriebenen HTTPS-Umleitungs Middleware ( `UseHttpsRedirection` ).

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP Strict Transport Security Protocol (hsts)

Pro [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)ist die [http-strikte Transport Sicherheit (hsts)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) eine Opt-in-Sicherheits Erweiterung, die durch eine Web-App durch die Verwendung eines Antwort Headers angegeben wird. Wenn ein [Browser, der hsts unterstützt,](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) diesen Header empfängt:

* Der Browser speichert die Konfiguration für die Domäne, die das Senden jeglicher Kommunikation über HTTP verhindert. Der Browser erzwingt die gesamte Kommunikation über HTTPS.
* Der Browser hindert den Benutzer daran, nicht vertrauenswürdige oder ungültige Zertifikate zu verwenden. Der Browser deaktiviert Eingabe Aufforderungen, mit denen ein Benutzer ein solches Zertifikat vorübergehend als vertrauenswürdig einstufen kann.

Da hsts vom Client erzwungen wird, gelten einige Einschränkungen:

* Der Client muss hsts unterstützen.
* Hsts erfordert mindestens eine erfolgreiche HTTPS-Anforderung zum Einrichten der hsts-Richtlinie.
* Die Anwendung muss jede HTTP-Anforderung überprüfen und die HTTP-Anforderung umleiten oder ablehnen.

ASP.net Core 2,1 und höher implementiert hsts mit der- `UseHsts` Erweiterungsmethode. Der folgende Code ruft `UseHsts` auf, wenn sich die APP nicht im [Entwicklungsmodus befindet](xref:fundamentals/environments):

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

`UseHsts` wird in der Entwicklung nicht empfohlen, da die hsts-Einstellungen durch Browser stark zwischen speicherbar sind. Standardmäßig `UseHsts` schließt die lokale Loopback Adresse aus.

Legen Sie für Produktionsumgebungen, die HTTPS zum ersten Mal implementieren, die anfängliche [hstsoptions. MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) -Einstellung mithilfe einer der-Methoden auf einen kleinen Wert fest. <xref:System.TimeSpan> Legen Sie den Wert von Stunden auf nicht mehr als einen Tag fest, falls Sie die HTTPS-Infrastruktur auf http zurücksetzen müssen. Wenn Sie sicher sind, dass die Konfiguration der HTTPS-Konfiguration von der Bedeutung ist, erhöhen Sie den hsts- `max-age` Wert. ein häufig verwendeter Wert ist ein Jahr.

Der folgende Code


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* Legt den vorab laden-Parameter des `Strict-Transport-Security` Headers fest. Vorab laden ist nicht Teil der [RFC hsts-Spezifikation](https://tools.ietf.org/html/rfc6797), wird aber von Webbrowsern unterstützt, um hsts-Websites bei einer Neuinstallation vorab zu laden. Weitere Informationen finden Sie unter [https://hstspreload.org/](https://hstspreload.org/).
* Aktiviert [includesubdomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), die die hsts-Richtlinie auf Host Unterdomänen anwendet.
* Legt den- `max-age` Parameter des- `Strict-Transport-Security` Headers explizit auf 60 Tage fest. Wenn nicht festgelegt, wird standardmäßig 30 Tage verwendet. Weitere Informationen finden Sie in der [max-age-Direktive](https://tools.ietf.org/html/rfc6797#section-6.1.1).
* Fügt `example.com` der Liste der auszuschließenden Hosts hinzu.

`UseHsts` schließt die folgenden Loopback Hosts aus:

* `localhost` : Die IPv4-Loopback Adresse.
* `127.0.0.1` : Die IPv4-Loopback Adresse.
* `[::1]` : Die IPv6-Loopback Adresse.

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Ausschließen von HTTPS/hsts bei der Projekt Erstellung

In einigen Back-End-Dienst Szenarios, in denen die Verbindungssicherheit an der öffentlichen Kante des Netzwerks behandelt wird, ist die Konfiguration der Verbindungssicherheit an den einzelnen Knoten nicht erforderlich. Web-Apps, die aus den Vorlagen in Visual Studio oder dem [dotnet New](/dotnet/core/tools/dotnet-new) -Befehl generiert werden, ermöglichen die [HTTPS-Umleitung](#require-https) und [hsts](#http-strict-transport-security-protocol-hsts). Bei bereit Stellungen, für die diese Szenarios nicht erforderlich sind, können Sie HTTPS/hsts bei der Erstellung der APP aus der Vorlage ablehnen.

So beenden Sie HTTPS/hsts:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

Deaktivieren Sie das Kontrollkästchen **für HTTPS konfigurieren** .

::: moniker range=">= aspnetcore-3.0"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![Das Dialogfeld neue ASP.net Core Webanwendung mit dem Kontrollkästchen für HTTPS konfigurieren ist nicht ausgewählt.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli) 

Verwenden Sie die `--no-https`-Option. Beispiel:

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Vertrauen des ASP.net Core HTTPS-Entwicklungs Zertifikats unter Windows und macOS

Die .net Core SDK enthält ein HTTPS-Entwicklungs Zertifikat. Das Zertifikat wird im Rahmen der erstmaligen Durchführung installiert. Beispielsweise `dotnet --info` erzeugt eine Variation der folgenden Ausgabe:

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Durch das Installieren des ASP.NET Core SDK wird das ASP.NET Core-HTTPS-Entwicklungszertifikat im lokalen Benutzerzertifikatspeicher installiert. Das Zertifikat wurde installiert, aber es ist nicht vertrauenswürdig. Um dem Zertifikat zu vertrauen, führen Sie den einmaligen Schritt aus, um das dotnet- `dev-certs` Tool auszuführen:

```dotnetcli
dotnet dev-certs https --trust
```

Der folgende Befehl stellt Hilfe zum `dev-certs`-Tool bereit:

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>Einrichten eines Entwickler Zertifikats für docker

Weitere Informationen finden Sie im entsprechenden [GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a>Vertrauen des HTTPS-Zertifikats unter Linux

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

Anweisungen zu Linux finden Sie in der Dokumentation zur Distribution.

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a>Vertrauen des HTTPS-Zertifikats aus dem Windows-Subsystem für Linux

Das Windows-Subsystem für Linux (WSL) generiert ein selbst signiertes HTTPS-Zertifikat. So konfigurieren Sie den Windows-Zertifikat Speicher für die Vertrauenswürdigkeit des WSL-Zertifikats:

* Führen Sie den folgenden Befehl aus, um das von WSL generierte Zertifikat zu exportieren:

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* Führen Sie in einem WSL-Fenster den folgenden Befehl aus:

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  Mit dem vorangehenden Befehl werden die Umgebungsvariablen festgelegt, sodass Linux das vertrauenswürdige Windows-Zertifikat verwendet.

## <a name="troubleshoot-certificate-problems"></a>Beheben von Zertifikats Problemen

Dieser Abschnitt enthält Hilfe, wenn das ASP.net Core HTTPS-Entwicklungs Zertifikat [installiert und vertrauenswürdig](#trust)ist, Sie jedoch weiterhin Browser Warnungen haben, dass das Zertifikat nicht vertrauenswürdig ist. Das ASP.net Core HTTPS-Entwicklungs Zertifikat wird von [Kestrel](xref:fundamentals/servers/kestrel)verwendet.

### <a name="all-platforms---certificate-not-trusted"></a>Alle Plattformen-Zertifikat nicht vertrauenswürdig

Führen Sie die folgenden Befehle aus:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Schließen Sie alle geöffneten Browser Instanzen. Öffnen Sie ein neues Browserfenster für die app. Die Zertifikats Vertrauensstellung wird von Browsern zwischengespeichert.

Mit den obigen Befehlen werden die meisten Browser Vertrauensstellungs Probleme gelöst. Wenn der Browser dem Zertifikat immer noch nicht vertraut, befolgen Sie die folgenden plattformspezifischen Vorschläge.

### <a name="docker---certificate-not-trusted"></a>Docker-Zertifikat nicht vertrauenswürdig

* Löschen Sie den Ordner " *c:\Users \{ User} \appdata\roaming\asp.net\https* ".
* Bereinigen Sie die Projekt Mappe. Löschen Sie die Ordner *bin* und *obj* .
* Starten Sie das Entwicklungs Tool neu. Beispielsweise Visual Studio, Visual Studio Code oder Visual Studio für Mac.

### <a name="windows---certificate-not-trusted"></a>Windows-Zertifikat nicht vertrauenswürdig

* Überprüfen Sie die Zertifikate im Zertifikat Speicher. Es muss ein `localhost` Zertifikat mit dem anzeigen `ASP.NET Core HTTPS development certificate` Amen unter `Current User > Personal > Certificates` und vorhanden sein. `Current User > Trusted root certification authorities > Certificates`
* Entfernen Sie alle gefundenen Zertifikate von persönlichen und vertrauenswürdigen Stamm Zertifizierungsstellen. Entfernen Sie das IIS Express localhost-Zertifikat **nicht** .
* Führen Sie die folgenden Befehle aus:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Schließen Sie alle geöffneten Browser Instanzen. Öffnen Sie ein neues Browserfenster für die app.

### <a name="os-x---certificate-not-trusted"></a>OS X-Zertifikat nicht vertrauenswürdig

* Öffnen Sie den Keychain-Zugriff.
* Wählen Sie die System Schlüsselkette aus.
* Überprüfen Sie, ob ein localhost-Zertifikat vorhanden ist.
* Überprüfen Sie, ob es ein `+` Symbol auf dem Symbol enthält, um anzugeben, dass es für alle Benutzer vertrauenswürdig ist.
* Entfernen Sie das Zertifikat aus der Keychain des Systems.
* Führen Sie die folgenden Befehle aus:

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

Schließen Sie alle geöffneten Browser Instanzen. Öffnen Sie ein neues Browserfenster für die app.

Informationen zur Behandlung von Zertifikat Problemen mit Visual Studio finden [Sie unter https-Fehler mit IIS Express (dotnet/aspnetcore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) .

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a>IIS Express SSL-Zertifikat, das mit Visual Studio verwendet wird.

Um Probleme mit dem IIS Express Zertifikat zu beheben, wählen Sie im Visual Studio-Installer **Reparieren** aus. Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/16892).

## <a name="additional-information"></a>Zusätzliche Informationen

* <xref:host-and-deploy/proxy-load-balancer>
* [Host ASP.net Core unter Linux mit Apache: HTTPS-Konfiguration](xref:host-and-deploy/linux-apache#https-configuration)
* [Host ASP.net Core unter Linux mit Nginx: HTTPS-Konfiguration](xref:host-and-deploy/linux-nginx#https-configuration)
* [Einrichten von SSL unter IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP hsts-Browserunterstützung](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)