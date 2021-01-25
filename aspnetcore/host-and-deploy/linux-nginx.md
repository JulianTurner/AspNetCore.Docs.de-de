---
title: Hosten von ASP.NET Core unter Linux mit Nginx
author: rick-anderson
description: Hier finden Sie Informationen zum Einrichten von Nginx als Reverseproxy unter Ubuntu 16.04, um den HTTP-Datenverkehr an eine ASP.NET Core-Web-App weiterzuleiten, die auf Kestrel ausgeführt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/30/2020
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
uid: host-and-deploy/linux-nginx
ms.openlocfilehash: 6a8fd8e3498dda9b7c10834791e64df6276e2823
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253019"
---
# <a name="host-aspnet-core-on-linux-with-nginx"></a>Hosten von ASP.NET Core unter Linux mit Nginx

Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)

In diesem Leitfaden wird das Einrichten einer produktionsbereiten ASP.NET Core-Umgebung auf einem Ubuntu 16.04-Server erläutert. Diese Anweisungen sind wahrscheinlich auf neuere Versionen von Ubuntu anwendbar, sie wurden jedoch noch nicht mit neueren Versionen getestet.

Weitere Informationen zu anderen Linux-Distributionen, die von ASP.NET Core unterstützt werden, finden Sie unter [Voraussetzungen für .NET Core unter Linux](/dotnet/core/linux-prerequisites).

> [!NOTE]
> Für Ubuntu 14.04 wird `supervisord` für die Überwachung des Kestrel-Prozesses empfohlen. `systemd` ist unter Ubuntu 14.04 nicht verfügbar. Anweisungen zu Ubuntu 14.04 finden Sie in der [vorherigen Version dieses Themas](https://github.com/dotnet/AspNetCore.Docs/blob/e9c1419175c4dd7e152df3746ba1df5935aaafd5/aspnetcore/publishing/linuxproduction.md).

In diesem Leitfaden:

* Wird eine bestehende ASP.NET Core-App hinter einem Reverseproxyserver eingefügt.
* Wird der Reverseproxyserver eingerichtet, um Anforderungen an den Kestrel-Webserver weiterzuleiten.
* Wird sichergestellt, dass die Web-App beim Start als Daemon ausgeführt wird.
* Wird ein Prozessverwaltungstool konfiguriert, das die Web-App beim Neustarten unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

1. Greifen Sie auf einen Ubuntu 16.04-Server mit einem Standardbenutzerkonto mit sudo-Berechtigung zu.
1. Installieren Sie die .NET Core-Runtime auf dem Server.
   1. Besuchen Sie die [.NET Core-Downloadseite](https://dotnet.microsoft.com/download/dotnet-core).
   1. Wählen Sie die neueste Version von .NET Core aus, die keine Vorschauversion ist.
   1. Laden Sie die neueste Runtime aus der Tabelle unter **Run apps - Runtime** (App-Ausführung – Runtime) herunter, bei der es sich nicht um eine Vorschauversion handelt.
   1. Klicken Sie auf den Link zu den **Anweisungen zum Linux-Paket-Manager**, und führen Sie die Ubuntu-Anweisungen zu Ihrer Version von Ubuntu aus.
1. Eine vorhandene ASP.NET Core-App.

Starten Sie die vom Server gehosteten ASP.NET Core-Apps zu einem beliebigen Zeitpunkt nach dem Upgrade des freigegebenen Frameworks neu.

## <a name="publish-and-copy-over-the-app"></a>Veröffentlichen und Kopieren der App

Konfigurieren Sie die App für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Wenn die App lokal ausgeführt wird und nicht so konfiguriert wurde, dass sie sichere Verbindungen (HTTPS) herstellt, können Sie einen der folgenden Ansätze verwenden:

* Konfigurieren der App, sodass diese sichere lokale Verbindungen verarbeitet. Weitere Informationen finden Sie im Abschnitt [HTTPS-Konfiguration](#https-configuration).
* Entfernen Sie `https://localhost:5001` (falls vorhanden) aus der `applicationUrl`-Eigenschaft in der Datei `Properties/launchSettings.json`.

Führen Sie in der Entwicklungsumgebung [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um eine App in ein Verzeichnis zu packen (z. B. `bin/Release/{TARGET FRAMEWORK MONIKER}/publish`, wobei der Platzhalter `{TARGET FRAMEWORK MONIKER}` der TFM [Target Framework Moniker, Zielframeworkmoniker] ist), das auf dem Server ausgeführt werden kann:

```dotnetcli
dotnet publish --configuration Release
```

Die App kann auch als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht werden, wenn Sie die .NET Core-Runtime nicht auf dem Server verwalten möchten.

Kopieren Sie die ASP.NET Core-App auf den Server, indem Sie ein beliebiges Tool verwenden, das in den Workflow der Organisation integriert ist (z. B. `SCP` oder `SFTP`). Web-Apps befinden sich üblicherweise im Verzeichnis `var` (z.B. `var/www/helloapp`).

> [!NOTE]
> In einem Szenario für die Bereitstellung in der Produktion übernimmt ein Continuous Integration-Workflow die Veröffentlichung der App und das Kopieren der Objekte auf den Server.

Testen der App:

1. Führen Sie die App über die Befehlszeile aus: `dotnet <app_assembly>.dll`.
1. Navigieren Sie in einem Browser zu `http://<serveraddress>:<port>`, und überprüfen Sie, ob die App lokal unter Linux funktioniert.

## <a name="configure-a-reverse-proxy-server"></a>Konfigurieren eines Reverseproxyservers

Ein Reverseproxy wird im Allgemeinen zur Unterstützung dynamischer Web-Apps eingerichtet. Ein Reverseproxy beendet die HTTP-Anforderung und leitet diese an die ASP.NET Core-App weiter.

### <a name="use-a-reverse-proxy-server"></a>Verwenden eines Reverseproxyservers

Kestrel eignet sich hervorragend für die Bereitstellung dynamischer Inhalte aus ASP.NET Core. Die Webbereitstellungsfunktionen sind jedoch nicht so umfangreich wie bei Servern wie IIS, Apache oder Nginx. Ein Reverseproxyserver kann Arbeiten wie das Verarbeiten von statischen Inhalten, das Zwischenspeichern und Komprimieren von Anforderungen und das Beenden von HTTPS vom HTTP-Server auslagern. Ein Reverseproxyserver kann sich auf einem dedizierten Computer befinden oder zusammen mit einem HTTP-Server bereitgestellt werden.

Für diesen Leitfaden wird eine einzelne Instanz von Nginx verwendet. Diese wird auf demselben Server ausgeführt, zusammen mit dem HTTP-Server. Je nach Anforderungen kann ein anderes Setup ausgewählt werden.

Da Anforderungen vom Reverseproxy weitergeleitet werden, verwenden Sie die [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) im Paket [`Microsoft.AspNetCore.HttpOverrides`](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides). Diese Middleware aktualisiert `Request.Scheme` mithilfe des `X-Forwarded-Proto`-Headers, sodass Umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren.

[!INCLUDE[](~/includes/ForwardedHeaders.md)]

Rufen Sie die Methode <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders%2A> am Anfang von `Startup.Configure` auf, bevor Sie andere Middleware aufrufen. Konfigurieren Sie die Middleware so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` weitergeleitet werden:

```csharp
using Microsoft.AspNetCore.HttpOverrides;

...

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

Wenn für die Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> angegeben sind, lauten die weiterzuleitenden Standardheader `None`.

Proxys, die unter Loopbackadressen (`127.0.0.0/8`, `[::1]`) ausgeführt werden, einschließlich der standardmäßigen Adresse von localhost (`127.0.0.1`), werden standardmäßig als vertrauenswürdig eingestuft. Wenn andere vertrauenswürdige Proxys oder Netzwerke innerhalb des Unternehmens Anforderungen zwischen dem Internet und dem Webserver verarbeiten, fügen Sie diese der Liste der <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies%2A> oder <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks%2A> mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> hinzu. Das folgende Beispiel fügt einen vertrauenswürdigen Proxyserver mit der IP-Adresse 10.0.0.0.100 der Middleware für weitergeleitete Header `KnownProxies` in `Startup.ConfigureServices` hinzu:

```csharp
using System.Net;

...

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-nginx"></a>Installieren von Nginx

Verwenden Sie `apt-get` zum Installieren von Nginx. Das Installationsprogramm erstellt ein `systemd`-Initialisierungsskript, das Nginx beim Systemstart als Dämon ausführt. Befolgen Sie die Installationsanleitungen für Ubuntu auf [NGINX: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages).

> [!NOTE]
> Wenn optionale Nginx-Module benötigt werden, kann die Erstellung von Nginx aus der Quelle erforderlich sein.

Da Nginx zum ersten Mal installiert wurde, starten Sie es explizit, indem Sie Folgendes ausführen:

```bash
sudo service nginx start
```

Stellen Sie sicher, dass ein Browser die Standardangebotsseite für Nginx anzeigt. Die Landing Page ist unter `http://<server_IP_address>/index.nginx-debian.html` erreichbar.

### <a name="configure-nginx"></a>Konfigurieren von Nginx

Ändern Sie `/etc/nginx/sites-available/default`, um Nginx als Reverseproxy für das Weiterleiten von Anforderungen zu Ihrer ASP.NET Core-App zu konfigurieren. Öffnen Sie die Datei in einem Text-Editor, und ersetzen Sie den Inhalt durch den folgenden Codeausschnitt:

```nginx
server {
    listen        80;
    server_name   example.com *.example.com;
    location / {
        proxy_pass         http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header   Upgrade $http_upgrade;
        proxy_set_header   Connection keep-alive;
        proxy_set_header   Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header   X-Forwarded-Proto $scheme;
    }
}
```

Wenn es sich bei der App um eine SignalR- oder Blazor Server-App handelt, finden Sie jeweils unter <xref:signalr/scale#linux-with-nginx> und <xref:blazor/host-and-deploy/server#linux-with-nginx> weitere Informationen.

Wenn keine Übereinstimmung mit `server_name` gefunden wird, verwendet Nginx den Standardserver. Wenn kein Server definiert ist, ist der erste Server in der Konfigurationsdatei der Standardserver. Als Best Practice gilt, einen bestimmten Standardserver hinzuzufügen, der den Statuscode 444 in Ihrer Konfigurationsdatei zurückgibt. Im Folgenden wird ein Beispiel für eine Standardserverkonfiguration aufgeführt:

```nginx
server {
    listen   80 default_server;
    # listen [::]:80 default_server deferred;
    return   444;
}
```

::: moniker range=">= aspnetcore-5.0"

Mit der vorhergehenden Konfigurationsdatei und dem vorhergehenden Standardserver akzeptiert Nginx den öffentlichen Datenverkehr über Port 80 mit dem Hostheader `example.com` oder `*.example.com`. Anforderungen, die mit diesen Hosts nicht übereinstimmen, werden nicht an Kestrel weitergeleitet. Nginx leitet die übereinstimmenden Anforderungen unter `http://localhost:5000` an Kestrel weiter. Weitere Informationen finden Sie unter [Verarbeitung einer Anforderung mit Nginx](https://nginx.org/docs/http/request_processing.html). Informationen zum Ändern der IP-Adresse bzw. des Ports von Kestrel finden Sie unter [Kestrel: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel/endpoints).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Mit der vorhergehenden Konfigurationsdatei und dem vorhergehenden Standardserver akzeptiert Nginx den öffentlichen Datenverkehr über Port 80 mit dem Hostheader `example.com` oder `*.example.com`. Anforderungen, die mit diesen Hosts nicht übereinstimmen, werden nicht an Kestrel weitergeleitet. Nginx leitet die übereinstimmenden Anforderungen unter `http://localhost:5000` an Kestrel weiter. Weitere Informationen finden Sie unter [Verarbeitung einer Anforderung mit Nginx](https://nginx.org/docs/http/request_processing.html). Informationen zum Ändern der IP-Adresse bzw. des Ports von Kestrel finden Sie unter [Kestrel: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).

::: moniker-end

> [!WARNING]
> Schlägt die Angabe einer ordnungsgemäßen [server_name-Anweisung](https://nginx.org/docs/http/server_names.html) fehlt, ist Ihre App Sicherheitsrisiken ausgesetzt. Platzhalterbindungen in untergeordneten Domänen (z.B. `*.example.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist). Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

Wenn die Nginx-Konfiguration eingerichtet ist, können Sie zur Überprüfung der Syntax der Konfigurationsdateien `sudo nginx -t` ausführen. Wenn der Test der Konfigurationsdatei erfolgreich ist, können Sie durch Ausführen von `sudo nginx -s reload` erzwingen, dass Nginx die Änderungen übernimmt.

Gehen Sie wie folgt vor, um die App auf dem Server direkt auszuführen:

1. Navigieren Sie zum Verzeichnis der App.
1. Führen Sie die App `dotnet <app_assembly.dll>` aus, wobei `app_assembly.dll` der Name der Assemblydatei der App ist.

Wenn die App auf dem Server ausgeführt wird, über das Internet jedoch nicht reagiert, sollten Sie die Firewall des Servers überprüfen und sicherstellen, dass Port 80 geöffnet ist. Fügen Sie bei Verwendung einer Azure-Ubuntu-VM eine Regel der Netzwerksicherheitsgruppe (NSG) zur Aktivierung des eingehenden Datenverkehrs an Port 80 hinzu. Eine Regel für ausgehenden Datenverkehr an Port 80 muss nicht aktiviert werden, da der ausgehende Datenverkehr automatisch gewährt wird, wenn die Regel für den eingehenden Datenverkehr aktiviert ist.

Nach dem Testen der App können Sie die App mit <kbd>STRG</kbd> + <kbd>C</kbd> in der Eingabeaufforderung beenden.

## <a name="monitor-the-app"></a>Überwachen der App

Der Server ist dafür eingerichtet, Anforderungen an `http://<serveraddress>:80` an die ASP.NET Core-App weiterzuleiten, die unter `http://127.0.0.1:5000` unter Kestrel ausgeführt wird. Nginx wurde jedoch nicht dafür eingerichtet, den Kestrel-Prozess zu verwalten. `systemd` kann für die Erstellung einer Dienstdatei verwendet werden, um die zugrunde liegende Web-App zu starten und zu überwachen. `systemd` ist ein Initialisierungssystem, das viele leistungsstarke Features zum Starten, Beenden und Verwalten von Prozessen bereitstellt. 

### <a name="create-the-service-file"></a>Erstellen der Dienstdatei

Erstellen Sie die Dienstdefinitionsdatei:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Bei Folgendem handelt es sich um eine Dienstdatei für die App:

```ini
[Unit]
Description=Example .NET Web API App running on Ubuntu

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=www-data
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

Im vorherigen Beispiel wird der Benutzer, der den Dienst verwaltet, durch die Option `User` angegeben. Der Benutzer (`www-data`) muss vorhanden und der ordnungsgemäße Besitzer der App-Dateien sein.

Verwenden Sie `TimeoutStopSec`, um die Dauer der Wartezeit bis zum Herunterfahren der App zu konfigurieren, nachdem diese das anfängliche Interruptsignal empfangen hat. Wenn die App in diesem Zeitraum nicht heruntergefahren wird, wird SIGKILL ausgegeben, um die App zu beenden. Geben Sie den Wert als einheitenlose Sekunden (z.B. `150`), einen Zeitspannenwert (z.B. `2min 30s`) oder `infinity` an, um das Timeout zu deaktivieren. `TimeoutStopSec` hat den Standardwert `DefaultTimeoutStopSec` in der Manager-Konfigurationsdatei (`systemd-system.conf`, `system.conf.d`, `systemd-user.conf`, `user.conf.d`). Das Standardtimeout für die meisten Distributionen beträgt 90 Sekunden.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Linux verfügt über ein Dateisystem, bei dem die Groß-/Kleinschreibung beachtet wird. Wenn Sie `ASPNETCORE_ENVIRONMENT` auf `Production` festlegen, wird die Konfigurationsdatei `appsettings.Production.json` und nicht `appsettings.production.json` gesucht.

Einige Werte (z.B. SQL-Verbindungszeichenfolgen) müssen mit Escapezeichen versehen werden, damit die Konfigurationsanbieter die Umgebungsvariablen lesen können. Mit dem folgenden Befehl können Sie einen ordnungsgemäß mit Escapezeichen versehenen Wert für die Verwendung in der Konfigurationsdatei generieren:

```console
systemd-escape "<value-to-escape>"
```

::: moniker range=">= aspnetcore-3.0"

Doppelpunkte (`:`) als Trennzeichen werden in Umgebungsvariablennamen nicht unterstützt. Verwenden Sie statt eines Doppelpunkts zwei Unterstriche (`__`). Der [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables) konvertiert jeweils die zwei Unterstriche in einen Doppelpunkt, wenn die Umgebungsvariablen in die Konfiguration gelesen werden. Im folgenden Beispiel wird der Verbindungszeichenfolgeschlüssel `ConnectionStrings:DefaultConnection` als `ConnectionStrings__DefaultConnection` in die Dienstdefinitionsdatei platziert:

::: moniker-end
::: moniker range="< aspnetcore-3.0"

Doppelpunkte (`:`) als Trennzeichen werden in Umgebungsvariablennamen nicht unterstützt. Verwenden Sie statt eines Doppelpunkts zwei Unterstriche (`__`). Der [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konvertiert jeweils die zwei Unterstriche in einen Doppelpunkt, wenn die Umgebungsvariablen in die Konfiguration gelesen werden. Im folgenden Beispiel wird der Verbindungszeichenfolgeschlüssel `ConnectionStrings:DefaultConnection` als `ConnectionStrings__DefaultConnection` in die Dienstdefinitionsdatei platziert:

::: moniker-end

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

Speichern Sie die Datei, und aktivieren Sie den Dienst.

```bash
sudo systemctl enable kestrel-helloapp.service
```

Starten Sie den Dienst, und überprüfen Sie, ob er ausgeführt wird.

```
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

◝ kestrel-helloapp.service - Example .NET Web API App running on Ubuntu
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Wenn der Reverseproxy konfiguriert ist und Kestrel durch `systemd` verwaltet wird, ist die Webanwendung vollständig konfiguriert. Auf diese kann dann über einen Browser auf dem lokalen Computer unter `http://localhost` zugegriffen werden. Der Zugriff ist auch von einem Remotecomputer aus möglich, sofern keine Firewall diesen blockiert. Beim Überprüfen der Antwortheader zeigt der Header `Server` die ASP.NET Core-App an, die von Kestrel verarbeitet wird.

```text
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Protokoll anzeigen...

Da die Web-App, die Kestrel verwendet, von `systemd` verwaltet wird, werden alle Ereignisse und Prozesse in einem zentralen Journal protokolliert. Dieses Journal enthält jedoch alle Einträge für alle Dienste und Prozesse, die von `systemd` verwaltet werden. Verwenden Sie folgenden Befehl, um die `kestrel-helloapp.service`-spezifischen Elemente anzuzeigen:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Für weiteres Filtern können Zeitoptionen wie `--since today`, `--until 1 hour ago` oder eine Kombination aus diesen die Menge der zurückgegebenen Einträge verringern.

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Schutz von Daten

Der [Stapel zum Schutz von Daten in ASP.NET Core](xref:security/data-protection/introduction) wird von mehreren [ASP.NET Core-Middlewares](xref:fundamentals/middleware/index) verwendet. Hierzu gehören die Authentifizierungsmiddleware (zum Beispiel die cookiemiddleware) und Maßnahmen zum Schutz vor websiteübergreifenden Anforderungsfälschungen (CSRF). Selbst wenn Datenschutz-APIs nicht im Benutzercode aufgerufen werden, sollte der Schutz von Daten konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen. Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.

Falls der Schlüsselbund im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:

* Alle cookiebasierten Authentifizierungstoken werden für ungültig erklärt.
* Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden.
* Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden. Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-cookies](xref:fundamentals/app-state#tempdata) einschließen.

Wenn Sie den Schutz von Daten konfigurieren möchten, um den Schlüsselring persistent zu speichern und zu verschlüsseln, finden Sie in den folgenden Artikeln weitere Informationen:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="long-request-header-fields"></a>Lange Anforderungsheaderfelder

Die Standardeinstellungen für den Proxyserver schränken die Anforderungsheaderfelder in der Regel je nach Plattform auf 4 K oder 8 K ein. Eine App erfordert möglicherweise Felder, die länger als die Standardwerte sind (z. B. Apps, die [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) verwenden). Wenn längere Felder erforderlich sind, müssen die Standardeinstellungen des Proxyservers angepasst werden. Die anzuwendenden Werte hängen vom jeweiligen Szenario ab. Weitere Informationen finden Sie in der Dokumentation Ihres Servers.

* [proxy_buffer_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffer_size)
* [proxy_buffers](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_buffers)
* [proxy_busy_buffers_size](https://nginx.org/docs/http/ngx_http_proxy_module.html#proxy_busy_buffers_size)
* [large_client_header_buffers](https://nginx.org/docs/http/ngx_http_core_module.html#large_client_header_buffers)

> [!WARNING]
> Erhöhen Sie die Standardwerte der Proxypuffer nur dann, wenn dies absolut erforderlich ist. Ein Erhöhen dieser Werte vergrößert das Risiko von Pufferüberlauf- (Überlauf-) und Denial-of-Service-Angriffen durch böswillige Benutzer.

## <a name="secure-the-app"></a>Sichern der App

### <a name="enable-apparmor"></a>Aktivieren von AppArmor

Bei Linux Security Modules (LSM) handelt es sich um ein Framework, das seit Linux 2.6 Teil des Linux-Kernels ist. LSM unterstützt verschiedene Implementierungen von Sicherheitsmodulen. [AppArmor](https://wiki.ubuntu.com/AppArmor) ist ein LSM, das ein obligatorisches Zugriffssteuerungssystem implementiert, das das Programm auf eine beschränkte Menge an Ressourcen begrenzt. Versichern Sie sich, dass AppArmor aktiviert und ordnungsgemäß konfiguriert ist.

### <a name="configure-the-firewall"></a>Konfigurieren der Firewall

Schließen Sie alle externen Ports, die nicht verwendet werden. „Uncomplicated Firewall“ (UFW) stellt ein Front-End für `iptables` bereit, indem eine CLI zum Konfigurieren der Firewall bereitgestellt wird.

> [!WARNING]
> Eine Firewall verhindert den Zugriff auf das gesamte System, wenn dieses nicht ordnungsgemäß konfiguriert ist. Falls Sie SSH zum Verbindungsaufbau verwenden und den falschen SSH-Port angeben, können Sie nicht mehr auf das System zugreifen. Der Standardport ist 22. Weitere Informationen finden Sie unter [introduction to ufw (Einführung in ufw)](https://help.ubuntu.com/community/UFW) und in den [Manpages](https://manpages.ubuntu.com/manpages/bionic/man8/ufw.8.html).

Installieren Sie `ufw`, und konfigurieren Sie das Tool so, dass der Datenverkehr auf allen erforderlich Ports zugelassen wird.

```bash
sudo apt-get install ufw

sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

sudo ufw enable
```

### <a name="secure-nginx"></a>Sichern von Nginx

#### <a name="change-the-nginx-response-name"></a>Ändern des Namens der Nginx-Antwort

Bearbeiten Sie `src/http/ngx_http_header_filter_module.c`:

```
static char ngx_http_server_string[] = "Server: Web Server" CRLF;
static char ngx_http_server_full_string[] = "Server: Web Server" CRLF;
```

#### <a name="configure-options"></a>Konfigurieren von Optionen

Konfigurieren Sie den Server mit zusätzlichen erforderlichen Modulen. Erwägen Sie zum Schutz der App die Verwendung einer Web-App-Firewall wie z.B. [ModSecurity](https://www.modsecurity.org/).

#### <a name="https-configuration"></a>HTTPS-Konfiguration

**Konfigurieren der App für sichere (HTTPS) lokale Verbindungen**

Der [dotnet run](/dotnet/core/tools/dotnet-run)-Befehl verwendet die *Properties/launchSettings.json*-Datei der App, die die App so konfiguriert, dass diese an den URLs lauscht, die von der `applicationUrl`-Eigenschaft bereitgestellt werden. Beispiel: `https://localhost:5001;http://localhost:5000`.

Konfigurieren Sie mithilfe eines der folgenden Ansätze die App so, dass sie bei der Entwicklung für den Befehl `dotnet run` oder die Entwicklungsumgebung (<kbd>F5</kbd> oder <kbd>STRG</kbd>+<kbd>F5</kbd> in Visual Studio Code) ein Zertifikat verwendet:

::: moniker range=">= aspnetcore-5.0"

* [Ersetzen des Standardzertifikats aus der Konfiguration](xref:fundamentals/servers/kestrel/endpoints#configuration) (*Empfohlen*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel/endpoints#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* [Ersetzen des Standardzertifikats aus der Konfiguration](xref:fundamentals/servers/kestrel#configuration) (*Empfohlen*)
* [KestrelServerOptions.ConfigureHttpsDefaults](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

::: moniker-end

**Konfigurieren des Reverseproxys für sichere (HTTPS) Clientverbindungen**

* Konfigurieren Sie den Server so, dass dieser für den HTTPS-Datenverkehr an Port 443 lauscht, indem Sie ein gültiges Zertifikat angeben, das von einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) ausgestellt wurde.

* Stärken Sie Ihre Sicherheit, indem Sie einige der in der folgenden Datei ( */etc/nginx/nginx.conf*) dargestellten Methoden verwenden. Die Beispiele schließen das Auswählen einer stärkeren Verschlüsselung und das Weiterleiten allen Datenverkehrs über HTTP auf HTTPS ein.

  > [!NOTE]
  > Für Entwicklungsumgebungen empfehlen sich temporäre Umleitungen (302) anstelle permanenter Umleitungen (301). Das Zwischenspeichern von Links kann in Entwicklungsumgebungen zu instabilem Verhalten führen.

* Durch das Hinzufügen eines `HTTP Strict-Transport-Security`-Headers (HSTS) wird sichergestellt, dass alle nachfolgenden Anforderungen vom Client über HTTPS erfolgen.

  Wichtige Anleitungen zu HSTS finden Sie unter <xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts>.

* Wenn HTTPS in Zukunft deaktiviert wird, halten Sie sich an einen der folgenden Ansätze:

  * Fügen Sie den HSTS-Header nicht hinzu.
  * Wählen Sie einen kurzen `max-age`-Wert aus.

Fügen Sie die Konfigurationsdatei */etc/nginx/proxy.conf* hinzu:

[!code-nginx[](linux-nginx/proxy.conf)]

**Ersetzen** Sie die Inhalte der Konfigurationsdatei */etc/nginx/nginx.conf* durch die folgende Datei. Das Beispiel enthält die Abschnitte `http` und `server` in einer Konfigurationsdatei.

[!code-nginx[](linux-nginx/nginx.conf?highlight=2)]

> [!NOTE]
> Blazor WebAssembly-Apps erfordern einen größeren `burst`-Parameterwert, damit die größere Anzahl von Anforderungen verarbeitet werden kann, die von einer App ausgeführt werden. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/webassembly#nginx>.

#### <a name="secure-nginx-from-clickjacking"></a>Sichern von Nginx vor Clickjacking

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), auch bekannt als *UI Redress-Angriff*, ist ein böswilliger Angriff, bei dem ein Websitebesucher dazu verleitet wird, auf einen Link oder eine Schaltfläche zu klicken, der bzw. die sich auf einer anderen Seite als der aktuell besuchten Seite befindet. Verwenden Sie `X-FRAME-OPTIONS` zum Sichern der Website.

So wehren Sie Clickjacking-Angriffe ab:

1. Bearbeiten Sie die Datei *nginx.conf*:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Fügen Sie die Zeile `add_header X-Frame-Options "SAMEORIGIN";` hinzu:

1. Speichern Sie die Datei.
1. Starten Sie Nginx neu.

#### <a name="mime-type-sniffing"></a>MIME-Typermittlung

Dieser Header hindert die meisten Browser an der MIME-Ermittlung einer Antwort vom deklarierten Inhaltstyp weg, da der Header den Browser anweist, den Inhaltstyp der Antwort nicht zu überschreiben. Durch die Option `nosniff` wird der Inhalt vom Browser als `text/html` gerendert, wenn der Server sagt, dass es sich bei diesem um `text/html` handelt.

1. Bearbeiten Sie die Datei *nginx.conf*:

   ```bash
   sudo nano /etc/nginx/nginx.conf
   ```

   Fügen Sie die Zeile `add_header X-Content-Type-Options "nosniff";` hinzu:

1. Speichern Sie die Datei.
1. Starten Sie Nginx neu.

## <a name="additional-nginx-suggestions"></a>Zusätzliche Vorschläge zu Nginx

Starten Sie die vom Server gehosteten ASP.NET Core-Apps nach dem Upgrade des freigegebenen Frameworks neu.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Voraussetzungen für .NET Core unter Linux](/dotnet/core/linux-prerequisites)
* [Nginx: Binary Releases: Official Debian/Ubuntu packages](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#official-debian-ubuntu-packages)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
* [NGINX: Using the Forwarded header (NGINX: Verwenden des weitergeleiteten Headers)](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)
