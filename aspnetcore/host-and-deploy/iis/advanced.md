---
title: Erweiterte Konfiguration
author: rick-anderson
description: Erweiterte Konfiguration mit dem ASP.NET Core-Modul und den Internetinformationsdiensten (IIS).
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 5/7/2020
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
uid: host-and-deploy/iis/advanced
ms.openlocfilehash: 9f14929a7d298d6f4d66abcc88665db34fc072bf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058611"
---
# <a name="advanced-configuration-of-the-aspnet-core-module-and-iis"></a>Erweiterte Konfiguration des ASP.NET Core-Moduls und der IIS

In diesem Artikel geht es um erweiterte Konfigurationsoptionen und entsprechende Szenarios für das ASP.NET Core-Modul und die IIS.

## <a name="modify-the-stack-size"></a>Ändern der Stapelgröße

*Gilt nur bei Verwendung des In-Process-Hostingmodells.*

Konfigurieren Sie die Größe des verwalteten Stapels mithilfe der `stackSize`-Einstellung in Byte in der `web.config`-Datei. Die Standardgröße beträgt 1.048.576 Byte (1 MB). Im folgenden Beispiel wird die Stapelgröße in 2 MB (2.097.152 Byte) geändert:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="stackSize" value="2097152" />
  </handlerSettings>
</aspNetCore>
```

## <a name="proxy-configuration-uses-http-protocol-and-a-pairing-token"></a>Die Proxykonfiguration verwendet das HTTP-Protokoll und ein Paarbildungstoken

*Gilt nur für Out-of-Process-Hosting.*

Der Proxy, der zwischen dem ASP.NET Core-Modul und Kestrel erstellt wurde, verwendet das HTTP-Protokoll. Es gibt kein Risiko für Lauschangriffe auf den Datenverkehr zwischen dem Modul und Kestrel von einem Speicherort außerhalb des Servers.

Ein Paarbildungstoken wird verwendet, um sicherzustellen, dass die von Kestrel empfangenen Anfragen von IIS über einen Proxy gesendet wurden und nicht von einer anderen Quelle stammen. Das Paarbildungstoken wurde durch das Modul erstellt und in einer Umgebungsvariablen (`ASPNETCORE_TOKEN`) festgelegt. Das Paarbildungstoken ist auch bei jeder Proxyanforderung in einem Header (`MS-ASPNETCORE-TOKEN`) festgelegt. IIS-Middleware überprüft jede erhaltene Anforderung, um sicherzustellen, dass der Headerwert des Paarbildungstokens dem Wert der Umgebungsvariablen entspricht. Wenn die Tokenwerte nicht übereinstimmen, wird die Anforderung protokolliert und abgelehnt. Es kann nicht von einem Speicherort außerhalb des Servers auf die Umgebungsvariablen des Paarbildungstokens und den Datenverkehr zwischen dem Modul und Kestrel zugegriffen werden. Wenn ein Angreifer den Wert des Paarbildungstokens nicht kennt, kann er keine Anforderungen einreichen, die die IIS-Middleware-Prüfung umgehen.

## <a name="aspnet-core-module-with-an-iis-shared-configuration"></a>ASP.NET Core-Modul mit einer IIS-Freigabekonfiguration

Das Installationsprogramm des ASP.NET Core-Moduls wird mit den Berechtigungen des `TrustedInstaller`-Kontos ausgeführt. Da das lokale Systemkonto keine Berechtigung zum Ändern für den von der IIS-Freigabekonfiguration verwendeten Freigabepfad hat, stößt der Installer beim Versuch, die Moduleinstellungen in der `applicationHost.config`-Datei auf der Freigabe zu konfigurieren, auf einen „Zugriff verweigert“-Fehler.

Wenn eine ISS-Freigabekonfiguration auf demselben Computer verwendet wird wie die ISS-Installation, führen Sie das Installationsprogramm des ASP.NET -Core-Hosting-Pakets mit auf `1` festgelegtem Parameter `OPT_NO_SHARED_CONFIG_CHECK` aus:

```console
dotnet-hosting-{VERSION}.exe OPT_NO_SHARED_CONFIG_CHECK=1
```

Wenn sich der Pfad zur freigegebenen Konfiguration nicht auf demselben Computer wie die ISS-Installation befindet, befolgen Sie die folgenden Schritte:

1. Deaktivieren Sie die IIS-Freigabekonfiguration.
1. Führen Sie den Installer aus.
1. Exportieren Sie die aktualisierte `applicationHost.config`-Datei in die Dateifreigabe.
1. Aktivieren Sie die IIS-Freigabekonfiguration erneut.

## <a name="data-protection"></a>Schutz von Daten

Der [ASP.NET Core-Stapel zum Schutz von Daten](xref:security/data-protection/introduction) wird von mehreren ASP.NET-[Middlewarekomponenten](xref:fundamentals/middleware/index) genutzt, darunter auch von Middleware, die bei der Authentifizierung verwendet wird. Selbst wenn die APIs zum Schutz von Daten nicht aus dem Benutzercode aufgerufen werden, sollte der Schutz von Daten mit einem Bereitstellungsskript oder im Benutzercode konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen. Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.

Falls die Schlüsselsammlung für den Datenschutz im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:

* Alle cookiebasierten Authentifizierungstoken werden für ungültig erklärt. 
* Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden. 
* Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden. Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-cookies](xref:fundamentals/app-state#tempdata) einschließen.

Zum Konfigurieren des Schutzes von Daten unter IIS mithilfe des persistenten Schlüsselbunds verwenden Sie **einen** der folgenden Ansätze:

* **Erstellen von Registrierungsschlüsseln für den Schutz von Daten**

  Schlüssel für den Schutz von Daten, die von ASP.NET Core-Apps verwendet werden, werden in der Registrierung außerhalb der Apps gespeichert. Sie müssen Registrierungsschlüssel für den App-Pool erstellen, um die Schlüssel für eine bestimmte App dauerhaft zu speichern.

  Bei eigenständigen IIS-Installationen, die ohne Webfarm vorgesehen sind, kann das [PowerShell-Skript „Provision-AutoGenKeys.ps1“ für den Schutz von Daten](https://github.com/dotnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) für jeden App-Pool genutzt werden, das mit einer ASP.NET Core-App verwendet wird. Dieses Skript erstellt einen Registrierungsschlüssel in der HKLM-Registrierung, der nur für das Workerprozesskonto des App-Pools der App zugänglich ist. Schlüssel werden in ruhendem Zustand mit DPAPI mit einem computerweiten Schlüssel verschlüsselt.

  In Webfarmszenarios kann eine App so konfiguriert werden, dass sie einen UNC-Pfad verwendet, um die Schlüsselsammlung für den Schutz von Daten zu speichern. Standardmäßig werden die Schlüssel nicht verschlüsselt. Stellen Sie sicher, dass die Dateiberechtigungen für die Netzwerkfreigabe auf das Windows-Konto beschränkt sind, mit dem die App ausgeführt wird. Ein X.509-Zertifikat kann zum Schutz von Schlüsseln im ruhenden Zustand verwendet werden. Richten Sie ggf. einen Mechanismus ein, um es Benutzern zu ermöglichen, Zertifikate hochzuladen. Platzieren Sie Zertifikate im Zertifikatspeicher des Benutzers für vertrauenswürdige Anbieter, und stellen Sie sicher, dass sie auf allen Computern verfügbar sind, auf denen die App des Benutzers ausgeführt wird. Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.

* **Konfigurieren des IIS-Anwendungspools zum Laden des Benutzerprofils**

  Diese Einstellung befindet sich im Abschnitt **Prozessmodell** unter **Erweiterte Einstellungen** für den App-Pool. Legen Sie **Benutzerprofil laden** auf `True` fest. Wenn diese Option auf `True` festgelegt ist, werden Schlüssel im Benutzerprofilverzeichnis gespeichert und mit DPAPI mit einem für das Benutzerkonto spezifischen Schlüssel geschützt. Schlüssel werden im Ordner `%LOCALAPPDATA%/ASP.NET/DataProtection-Keys` gespeichert.

  Das [`setProfileEnvironment`-Attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) des App-Pools muss ebenfalls aktiviert sein. Der Standardwert von `setProfileEnvironment` ist `true`. In einigen Szenarien (z.B. Windows-Betriebssystem) ist `setProfileEnvironment` auf `false` festgelegt. Gehen Sie folgendermaßen vor, wenn Schlüssel nicht wie erwartet im Benutzerprofilverzeichnis gespeichert werden:

  1. Navigieren Sie zum Ordner `%windir%/system32/inetsrv/config`.
  1. Öffnen Sie die Datei `applicationHost.config` .
  1. Suchen Sie das Element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Bestätigen Sie, dass das `setProfileEnvironment`-Attribut nicht vorhanden ist, das standardmäßig den Wert `true` aufweist, oder legen Sie den Wert des Attributs explizit auf `true` fest.

* **Verwenden des Dateisystems als Schlüsselbundspeicher**

  Passen Sie den App-Code so an, dass er [das Dateisystem als Schlüsselbundspeicher verwendet](xref:security/data-protection/configuration/overview). Verwenden Sie ein X.509-Zertifikat, um den Schlüsselbund zu schützen, und stellen Sie sicher, dass es sich bei dem Zertifikat um ein vertrauenswürdiges Zertifikat handelt. Wenn es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie es im vertrauenswürdigen Stammspeicher platzieren.

  Wenn IIS in einer Webfarm verwendet wird:

  * Verwenden Sie eine Dateifreigabe, auf die alle Computer zugreifen können.
  * Stellen Sie ein X509-Zertifikat auf jedem Computer bereit. Konfigurieren Sie den [Schutz von Daten im Code](xref:security/data-protection/configuration/overview).

* **Festlegen einer computerweiten Richtlinie für den Schutz von Daten**

  Das System zum Schutz von Daten verfügt über eine eingeschränkte Unterstützung zum Festlegen einer [computerweiten Standardrichtlinie](xref:security/data-protection/configuration/machine-wide-policy) für alle Apps, die die Datenschutz-APIs nutzen. Weitere Informationen finden Sie unter <xref:security/data-protection/introduction>.

## <a name="iis-configuration"></a>IIS-Konfiguration

**Windows Server-Betriebssysteme**

Aktivieren Sie die Serverrolle **Webserver (IIS)** , und richten Sie Rollendienste ein.

1. Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**. Aktivieren Sie im Schritt **Serverrollen** das Kontrollkästchen für **Webserver (IIS)** .

   ![Die Rolle „Webserver (IIS)“ wird im Schritt „Serverrollen auswählen“ ausgewählt.](index/_static/server-roles-ws2016.png)

1. Nach dem Schritt **Features** wird der Schritt **Rollendienste** für Webserver (IIS) geladen. Wählen Sie die gewünschten IIS-Rollendienste aus, oder übernehmen Sie die bereitgestellten Standardrollendienste.

   ![Die Standardrollendienste werden im Schritt „Rollendienste auswählen“ ausgewählt.](index/_static/role-services-ws2016.png)

   **Windows-Authentifizierung (optional)**  
   Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Sicherheit**. Wählen Sie das Feature **Windows-Authentifizierung** aus. Weitere Informationen finden Sie unter [Windows-Authentifizierung `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).

   **WebSockets (optional)**  
   WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt. Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Anwendungsentwicklung**. Wählen Sie das Feature **WebSocket-Protokoll** aus. Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).

1. Fahren Sie mit dem Schritt **Bestätigung** fort, um die Webserverrolle und die Dienste zu installieren. Ein Server-/IIS-Neustart ist nach der Installation der Rolle **Webserver (IIS)** nicht erforderlich.

**Windows-Desktopbetriebssysteme**

Aktivieren Sie die **IIS-Verwaltungskonsole** und die **WWW-Dienste**.

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).

1. Öffnen Sie den Knoten **Internetinformationsdienste**. Öffnen Sie den Knoten **Webverwaltungstools**.

1. Aktivieren Sie das Kontrollkästchen für **IIS-Verwaltungskonsole**.

1. Aktivieren Sie das Kontrollkästchen für **WWW-Dienste**.

1. Akzeptieren Sie die Standardfeatures für **WWW-Dienste** , oder passen Sie die IIS-Features an.

   **Windows-Authentifizierung (optional)**  
   Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Sicherheit**. Wählen Sie das Feature **Windows-Authentifizierung** aus. Weitere Informationen finden Sie unter [Windows-Authentifizierung `<windowsAuthentication>`](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).

   **WebSockets (optional)**  
   WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt. Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Anwendungsentwicklungsfeatures**. Wählen Sie das Feature **WebSocket-Protokoll** aus. Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).

1. Wenn die IIS-Installation einen Neustart erfordert, starten Sie das System neu.

![Die IIS-Verwaltungskonsole und WWW-Dienste werden in Windows-Features ausgewählt.](index/_static/windows-features-win10.png)

## <a name="virtual-directories"></a>Virtuelle Verzeichnisse

[Virtuelle IIS-Verzeichnisse](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) werden mit ASP.NET Core-Apps nicht unterstützt. Eine App kann als [untergeordnete Anwendung](#sub-applications) gehostet werden.

## <a name="sub-applications"></a>Untergeordnete Anwendungen

Eine ASP.NET Core-App kann als [untergeordnete IIS-Anwendung (untergeordnete App)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) gehostet werden. Der Pfad der untergeordneten App wird ein Teil der URL der Stamm-App.

Statische Assetlinks in der untergeordneten App sollten die Tilde/Schrägstrich-Notation verwenden (`~/`). Die Tilde/Schrägstrich-Notation löst ein [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aus, um die Pfadbasis der untergeordneten App dem gerenderten relativen Link voranzustellen. Für eine untergeordnete App unter `/subapp_path` wird ein mit `src="~/image.png"` verknüpftes Bild als `src="/subapp_path/image.png"` gerendert. Die Static File Middleware verarbeitet die Anforderung der statischen Datei nicht. Die Anforderung wird von der Static File Middleware der untergeordneten App verarbeitet.

Wenn das `src`-Attribut eines statischen Objekts auf einen absoluten Pfad festgelegt wird (z.B. `src="/image.png"`), wird der Link ohne die Pfadbasis der untergeordneten App gerendert. Die Middleware für statische Dateien der Stamm-App versucht, das Objekt vom [Webstamm](xref:fundamentals/index#web-root) der Stamm-App aus bereitzustellen, was zu einer *404 Nicht gefunden* -Antwort führt, solange das statische Objekt in der Stamm-App nicht verfügbar ist.

So hosten Sie eine ASP.NET Core-App als untergeordnete App unter einer anderen ASP.NET Core-App:

1. Richten Sie einen App-Pool für die untergeordnete App ein. Legen Sie die **.NET CLR-Version** auf **Kein verwalteter Code** fest, weil die Core Common Language Runtime (CoreCLR) für .NET Core gestartet wird, um die App im Workerprozess zu hosten, nicht der Desktop-CLR (.NET CLR).

1. Fügen Sie die Stammwebsite im IIS-Manager mit der untergeordneten App in einem unter der Stammwebsite liegenden Ordner hinzu.

1. Klicken Sie mit der rechten Maustaste im IIS-Manager auf den Ordner der untergeordneten App, und wählen Sie **In Anwendung konvertieren** aus.

1. Weisen Sie im Dialogfeld **Anwendung hinzufügen** mit der Schaltfläche **Auswählen** den **Anwendungspool** dem App-Pool zu, den Sie für die untergeordnete App erstellt haben. Klicken Sie auf **OK**.

Die Zuweisung eines separaten App-Pools zur untergeordneten App ist eine Anforderung, wenn Sie das In-Process-Hostingmodell verwenden.

Weitere Informationen zum In-Process-Hostingmodell und Konfigurieren des ASP.NET Core-Moduls finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

## <a name="application-pools"></a>Anwendungspools

Die App-Poolisolation wird durch das Hostingmodell bestimmt.

* In-Process-Hosting: Apps müssen in separaten App-Pools ausgeführt werden.
* Out-of-Process-Hosting: Es wird empfohlen, die Apps voneinander zu isolieren, indem Sie jede App in ihrem eigenen App-Pool ausführen.

Im IIS-Dialogfeld **Website hinzufügen** wird standardmäßig ein einzelner App-Pool pro App eingesetzt. Wenn ein **Websitename** angegeben ist, wird der Text automatisch in das Textfeld **Anwendungspool** übertragen. Ein neuer App-Pool mit dem Namen der Website wird erstellt, wenn die Website hinzugefügt wird.

## <a name="application-pool-no-locidentity"></a>Anwendungspool Identity

Mit einem Konto für die Identität des App-Pools können Sie eine App unter einem eindeutigen Konto ausführen, ohne Domänen oder lokale Konten erstellen und verwalten zu müssen. Unter IIS 8.0 oder höher erstellt der IIS-Administratorworkerprozess (WAS) ein virtuelles Konto mit dem Namen des neuen App-Pools und führt die Workerprozesse des App-Pools standardmäßig unter diesem Konto aus. Stellen Sie sicher, dass in der IIS-Verwaltungskonsole unter **Erweiterte Einstellungen** für den App-Pool die Option **Identity** auf `ApplicationPoolIdentity` festgelegt ist:

![Dialogfeld „Erweiterte Einstellungen“ für den Anwendungspool](index/_static/apppool-identity.png)

Der IIS-Verwaltungsprozess erstellt im Windows-Sicherheitssystem einen sicheren Bezeichner mit dem Namen des App-Pools. Ressourcen können mithilfe dieser Identität geschützt werden. Allerdings ist diese Identität kein echtes Benutzerkonto und wird in der Windows-Benutzerverwaltungskonsole nicht angezeigt.

Wenn der IIS-Workerprozess erhöhte Rechte für den Zugriff auf Ihre Anwendung erfordert, ändern Sie die Zugriffssteuerungsliste (ACL) für das Verzeichnis mit der App:

1. Öffnen Sie Windows Explorer, und navigieren Sie zum Verzeichnis.

1. Klicken Sie mit der rechten Maustaste auf das Verzeichnis, und klicken Sie auf **Eigenschaften**.

1. Klicken Sie auf der Registerkarte **Sicherheit** auf die Schaltfläche **Bearbeiten** und dann auf die Schaltfläche **Hinzufügen**.

1. Wählen Sie die Schaltfläche **Speicherorte** aus, und stellen Sie sicher, dass das System ausgewählt ist.

1. Geben Sie im Bereich **Geben Sie die Namen der auszuwählenden Objekte ein** das `IIS AppPool\{APP POOL NAME}`-Format ein. Hierbei steht der Platzhalter `{APP POOL NAME}` für den Namen des App-Pools. Klicken Sie auf die Schaltfläche **Namen überprüfen**. Überprüfen Sie für *DefaultAppPool* die Namen mit `IIS AppPool\DefaultAppPool`. Bei Auswahl der Schaltfläche **Namen überprüfen** wird im Bereich für Objektnamen der Wert `DefaultAppPool` angegeben. Es ist nicht möglich, den Namen des App-Pools direkt in den Bereich für Objektnamen einzugeben. Verwenden Sie das Format `IIS AppPool\{APP POOL NAME}`, wenn Sie den Objektnamen überprüfen. Hierbei steht der Platzhalter `{APP POOL NAME}` für den Namen des App-Pools.

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Der Name des App-Pools, „DefaultAppPool“, wird an „IIS AppPool\"“ im Bereich der Objektnamen angehängt, bevor „Namen überprüfen“ ausgewählt wird.](index/_static/select-users-or-groups-1.png)

1. Klicken Sie auf **OK**.

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Nach Auswahl von „Namen überprüfen“ wird der Objektname „DefaultAppPool“ im Bereich der Objektnamen angezeigt.](index/_static/select-users-or-groups-2.png)

1. Standardmäßig sollten Lese- und Schreibberechtigungen gewährt werden. Erteilen Sie weitere Berechtigungen, sofern erforderlich.

Zugriff kann auch über eine Eingabeaufforderung mit dem Tool **ICACLS** gewährt werden. Im folgenden Befehl wird als Beispiel *DefaultAppPool* verwendet:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Weitere Informationen finden Sie im Thema [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden IIS-Bereitstellungsszenarien unterstützt:

* In-Process
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * TLS 1.2-Verbindung oder höher
* Out-of-Process
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.
  * TLS 1.2-Verbindung oder höher

Für eine In-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/2`. Für eine Out-of-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/1.1`.

Weitere Informationen zu den In-Process- und Out-of-Process-Hostingmodellen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 ist standardmäßig aktiviert. Verbindungen führen ein Fallback auf HTTP/1.1 aus, wenn keine HTTP/2-Verbindung hergestellt wurde. Weitere Informationen zur HTTP/2-Konfiguration mit IIS-Bereitstellungen finden Sie unter [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS-Preflightanforderungen

*Dieser Abschnitt gilt nur für ASP.NET Core-Apps, die auf das .NET Framework ausgerichtet sind.*

Für eine ASP.NET Core-App, die auf das .NET Framework ausgerichtet ist, werden OPTIONS-Anforderungen in IIS standardmäßig nicht an die App übergeben. Informationen dazu, wie Sie die IIS-Handler der App in `web.config` so konfigurieren, dass OPTIONS-Anforderungen übergeben werden, finden Sie unter [Aktivieren ursprungsübergreifender Anforderungen in ASP.NET-Web-API 2: Funktionsweise von CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="application-initialization-module-and-idle-timeout"></a>Anwendungsinitialisierungsmodul und Leerlauftimeout

Bei Hosting in IIS durch Version 2 das ASP.NET Core-Moduls:

* [Anwendungsinitialisierungsmodul:](#application-initialization-module) App-Hostings vom Typ [In-Process](xref:host-and-deploy/iis/in-process-hosting) oder [Out-of-Process](xref:host-and-deploy/iis/out-of-process-hosting) können so konfiguriert werden, dass sie automatisch im Rahmen eines Workerprozessneustarts oder eines Serverneustarts gestartet werden.
* [Leerlauftimeout:](#idle-timeout) App-Hostings vom Typ [In-Process](xref:host-and-deploy/iis/in-process-hosting) können so konfiguriert werden, dass in Zeiten ohne Aktivität kein Timeout eintritt.

### <a name="application-initialization-module"></a>Anwendungsinitialisierungsmodul

*Gilt für In-Process und Out-of-Process gehostete Apps.*

[IIS-Anwendungsinitialisierung](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) ist ein IIS-Feature, das eine HTTP-Anforderung an die App sendet, wenn der App-Pool startet oder wiederverwendet wird. Die Anforderung löst den Start der App aus. Standardmäßig gibt IIS eine Anforderung an die Stamm-URL der App (`/`) zum Initialisieren der App aus (weitere Informationen zur Konfiguration finden Sie unter [Zusätzliche Ressourcen](#application-initialization-module-and-idle-timeout-additional-resources)).

Vergewissern Sie sich, dass die IIS-Anwendungsinitialisierungs-Rollenfunktion aktiviert ist:

Unter Windows 7 oder höher gilt für Desktopsysteme bei lokaler Verwendung von IIS:

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Öffnen Sie **Internetinformationsdienste** > **WWW-Dienste** > **Anwendungsentwicklungsfeatures**.
1. Aktivieren Sie das Kontrollkästchen für **Anwendungsinitialisierung**.

Unter Windows Server 2008 R2 oder höher:

1. Öffnen Sie den **Assistenten zum Hinzufügen von Rollen und Features**.
1. Öffnen Sie im Bereich **Rollendienste auswählen** den Knoten **Anwendungsentwicklung**.
1. Aktivieren Sie das Kontrollkästchen für **Anwendungsinitialisierung**.

Verwenden Sie einen der folgenden Ansätze, um das Anwendungsinitialisierungsmodul für die Website zu aktivieren:

* Bei Verwenden von IIS-Manager:

  1. Wählen Sie **Anwendungspools** im Bereich **Verbindungen** aus.
  1. Klicken Sie mit der rechten Maustaste im App-Pool der App in die Liste, und wählen Sie **Erweiterte Einstellungen** aus.
  1. Der standardmäßige **Startmodus** ist `OnDemand`. Legen Sie den **Startmodus** auf `AlwaysRunning` fest. Klicken Sie auf **OK**.
  1. Öffnen Sie den Knoten **Websites** im Bereich **Verbindungen**.
  1. Klicken Sie mit der rechten Maustaste auf die App, und wählen Sie **Website verwalten** > **Erweiterte Einstellungen** aus.
  1. Die Standardeinstellung für **Vorabladen aktiviert** ist `False`. Legen Sie für **Vorabladen aktiviert** `True` fest. Klicken Sie auf **OK**.

* Fügen Sie mithilfe von `web.config` das `<applicationInitialization>`-Element, für das `doAppInitAfterRestart` auf `true` festgelegt ist, den `<system.webServer>`-Elementen in der `web.config`-Datei der App hinzu:

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <configuration>
    <location path="." inheritInChildApplications="false">
      <system.webServer>
        <applicationInitialization doAppInitAfterRestart="true" />
      </system.webServer>
    </location>
  </configuration>
  ```

### <a name="idle-timeout"></a>Leerlauftimeout

*Gilt nur für In-Process gehostete Apps.*

Um zu verhindern, dass die App in den Leerlauf wechselt, legen Sie das Leerlauftimeout des App-Pools mit IIS-Manager fest:

1. Wählen Sie **Anwendungspools** im Bereich **Verbindungen** aus.
1. Klicken Sie mit der rechten Maustaste im App-Pool der App in die Liste, und wählen Sie **Erweiterte Einstellungen** aus.
1. Der Standardwert für **Leerlauftimeout (Minuten)** ist `20` Minuten. Legen Sie **Leerlauftimeout (Minuten)** auf `0` (null) fest. Klicken Sie auf **OK**.
1. Recyceln Sie den Workerprozess.

Um zu verhindern, dass in Apps, die [Out-of-Process](xref:host-and-deploy/iis/out-of-process-hosting) gehostet werden, ein Timeout auftritt, verwenden Sie einen der folgenden Ansätze:

* Pingen Sie die App von einem externen Dienst aus, damit sie kontinuierlich ausgeführt wird.
* Wenn die App nur Hintergrunddienste hostet, vermeiden Sie IIS-Hosting, und verwenden Sie einen [Windows-Dienst, um die ASP.NET Core-App zu hosten](xref:host-and-deploy/windows-service).

### <a name="application-initialization-module-and-idle-timeout-additional-resources"></a>Zusätzliche Ressourcen für das Anwendungsinitialisierungsmodul und das Leerlauftimeout

* [IIS 8.0 Anwendungsinitialisierung](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization)
* [Anwendungsinitialisierung `<applicationInitialization>`](/iis/configuration/system.webserver/applicationinitialization/).
* [Verarbeiten von Modelleinstellungen für einen Anwendungspool `<processModel>`](/iis/configuration/system.applicationhost/applicationpools/add/processmodel).

## <a name="module-schema-and-configuration-file-locations"></a>Dateispeicherorte für Modul, Schema und Konfiguration

### <a name="module"></a>Modul

**IIS (x86/amd64):**

* `%windir%\System32\inetsrv\aspnetcore.dll`

* `%windir%\SysWOW64\inetsrv\aspnetcore.dll`

* `%ProgramFiles%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll`

**IIS Express (x86/amd64):**

* `%ProgramFiles%\IIS Express\aspnetcore.dll`

* `%ProgramFiles(x86)%\IIS Express\aspnetcore.dll`

* `%ProgramFiles%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

* `%ProgramFiles(x86)%\IIS Express\Asp.Net Core Module\V2\aspnetcorev2.dll`

### <a name="schema"></a>Schema

**IIS**

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema.xml`

* `%windir%\System32\inetsrv\config\schema\aspnetcore_schema_v2.xml`

**IIS Express**

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema.xml`

* `%ProgramFiles%\IIS Express\config\schema\aspnetcore_schema_v2.xml`

### <a name="configuration"></a>Konfiguration

**IIS**

* `%windir%\System32\inetsrv\config\applicationHost.config`

**IIS Express**

* Visual Studio: `{APPLICATION ROOT}\.vs\config\applicationHost.config`

* *iisexpress.exe* CLI: `%USERPROFILE%\Documents\IISExpress\config\applicationhost.config`

Den Speicherort dieser Dateien finden Sie, indem Sie `aspnetcore` in der Datei `applicationHost.config` suchen.
