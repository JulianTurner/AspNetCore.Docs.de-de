---
title: Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in Azure Apps Service und IIS beheben können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: b009cc61a94e618a48d96ecbd770ef6371308f6a
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059844"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core

::: moniker range=">= aspnetcore-2.2"

In diesem Artikel werden häufige Fehler beschrieben und Ratschläge zu bestimmten Fehlern beim Hosten von ASP.NET Core-Apps auf Azure App Service und IIS erteilt.

Allgemeine Anleitungen zur Problembehandlung finden Sie unter <xref:test/troubleshoot-azure-iis>.

Sammeln Sie folgende Informationen:

* Browserverhalten (Statuscode und Fehlermeldung)
* Einträge im Anwendungsereignisprotokoll
  * Azure App Service: Siehe <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Klicken Sie im **Windows**-Menü auf **Start**, geben Sie *Ereignisanzeige* ein, und drücken Sie die **EINGABETASTE**.
    1. Erweitern Sie in der **Ereignisanzeige** die Option **Windows-Protokolle** > **Anwendung** auf der Randleiste.
* stdout- und Debugprotokolleinträge im ASP.NET Core-Modul
  * Azure App Service: Siehe <xref:test/troubleshoot-azure-iis>.
  * IIS: Befolgen Sie die Anweisungen in den Abschnitten [Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) und [Erweiterte Diagnoseprotokolle](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) des Artikels zum ASP.NET Core-Modul.

Vergleichen Sie die Fehlerinformationen mit folgenden häufigen Fehlern. Befolgen Sie die Hinweise zur Fehlerbehebung, wenn eine Übereinstimmung gefunden wird.

Die Fehlerliste in diesem Artikel ist nicht vollständig. Wenn bei Ihnen ein Fehler auftritt, der hier nicht aufgeführt wird, öffnen Sie über die Schaltfläche **Feedback zum Inhalt** am Ende des Artikels ein neues Issue, in dem Sie den Fehler ausführlich beschreiben sollten, damit er reproduziert werden kann.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Durch ein Upgrade des Betriebssystems wird das ASP.NET Core-Modul (32-Bit) entfernt

**Anwendungsprotokoll:** Fehler beim Laden der Modul-DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**. Die Daten sind der Fehler.

Problembehandlung:

Nicht zum Betriebssystem gehörende Dateien im Verzeichnis **C:\Windows\SysWOW64\inetsrv** werden während eines Betriebssystemupgrades nicht beibehalten. Dieses Problem tritt auf, wenn vor der Durchführung eines Betriebssystemupgrades das ASP.NET Core-Modul installiert wurde und nach einem Betriebssystemupgrade ein App-Pool im 32-Bit-Modus ausgeführt wird. Reparieren Sie nach einem Betriebssystemupgrade das ASP.NET Core-Modul. Siehe [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wählen Sie **Reparatur** aus, wenn der Installer ausgeführt wird.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Websiteerweiterung fehlt, 32-Bit- und 64-Bit-Websiteerweiterungen (x86 und x64) installiert oder falsche Prozessbitanzahl festgelegt

*Gilt für Apps, die von Azure App Services gehostet werden.*

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Could not find inprocess request handler. Captured output from invoking hostfxr: Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden. Die Anwendung „/LM/W3SVC/1416782824/ROOT“ konnte nicht gestartet werden. Fehlercode: „0x8000ffff“.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.

* **Debugprotokoll des ASP.NET Core-Moduls:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen. Failed HRESULT returned: 0x8000ffff. Could not find inprocess request handler. Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.

Problembehandlung:

* Wenn die App in einer Vorschauruntime ausgeführt wird, installieren Sie entweder die 32-Bit- **oder** 64-Bit-Websiteerweiterung (x86 oder x64), die der Bitanzahl und der Runtimeversion der App entspricht. **Installieren Sie nicht sowohl Erweiterungen als auch mehrere Runtimeversionen der Erweiterung.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Runtime
  * ASP.NET Core {RUNTIME VERSION} (x64) Runtime

  Starten Sie die App neu. Warten Sie einige Sekunden, bis die App neu startet.

* Wenn Sie die App auf einer Vorschauruntime ausführen und sowohl die 32-Bit- als auch die 64-Bit-[Websiteerweiterung](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) (x86 und x64) installiert sind, deinstallieren Sie die Websiteerweiterung, die nicht der Bitanzahl der App entspricht. Starten Sie die App nach dem Entfernen der Websiteerweiterung neu. Warten Sie einige Sekunden, bis die App neu startet.

* Wenn Sie die App auf einer Vorschauruntime ausführen und die Bitanzahl der Websiteerweiterung mit der Bitanzahl der App übereinstimmt, bestätigen Sie, dass die *Runtimeversion* der Websiteerweiterung der Vorschau der Runtimeversion der App entspricht.

* Überprüfen Sie, ob die **Plattform** der App in **Anwendungseinstellungen** der Bitanzahl der App entspricht.

Weitere Informationen finden Sie unter <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Bereitstellung einer x86-App, obwohl der App-Pool nicht für 32-Bit-Apps aktiviert ist

* **Browser:** HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'. Überprüfen Sie die stderr-Protokolle, um weitere Informationen zu erhalten. Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application. Der CLR-Arbeitsthread wurde vorzeitig beendet

* **stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.

* **Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8007023e

Dieses Szenario wird vom SDK ausgelöst, wenn eine eigenständige App veröffentlicht wird. Das SDK generiert einen Fehler, wenn die relative ID (RID) nicht mit der Zielplattform übereinstimmt (z. B. bei der RID `win10-x64` für eine Projektdatei mit `<PlatformTarget>x86</PlatformTarget>`).

Problembehandlung:

Aktivieren Sie den IIS-App-Pool für 32-Bit-Apps, wenn Sie von x86-Frameworks abhängige Bereitstellungen (`<PlatformTarget>x86</PlatformTarget>`) durchführen möchten. Öffnen Sie im IIS-Manager die Option **Erweiterte Einstellungen** des App-Pools, und legen Sie **32-Bit-Anwendungen aktivieren** auf **Wahr** fest.

## <a name="platform-conflicts-with-rid"></a>Plattformkonflikte mit RID

* **Browser:** HTTP-Fehler 502.5: Prozessfehler

* **Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm „C:\{PATH}\' konnte den Prozess mit der Befehlszeile „C:\{PATH}{assembly}.{exe|dll}“ nicht starten. Fehlercode = 0x80004005 : ff.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Ausnahmefehler: System.BadImageFormatException: Die Datei oder Assembly „{ASSEMBLY}.dll“ wurde nicht gefunden. Es wurde versucht, ein Programm mit einem falschen Format zu laden.

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Wenn diese Ausnahme für eine Azure-App-Bereitstellung beim Aktualisieren einer App und beim Bereitstellen neuerer Assemblys auftritt, löschen Sie alle Dateien aus der vorherigen Bereitstellung manuell. Veraltete inkompatible Assemblys können zu einer `System.BadImageFormatException`-Ausnahme führen, wenn Sie eine aktualisierte App bereitstellen.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI-Endpunkt falsch oder beendete Website

* **Browser:** „ERR_CONNECTION_REFUSED“ **oder** „Es kann keine Verbindung hergestellt werden.“

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

* Vergewissern Sie sich, dass der richtige URI-Endpunkt für die App verwendet wird. Überprüfen Sie die Bindungen.

* Vergewissern Sie sich, dass die IIS-Website nicht den Status *Beendet* aufweist.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine oder W3SVC-Serverfeatures deaktiviert

**Betriebssystemausnahme:** Die Features „CoreWebEngine“ und „W3SVC“ von IIS 7.0 müssen installiert sein, um das ASP.NET Core-Modul zu verwenden.

Problembehandlung:

Vergewissern Sie sich, dass die richtigen Rollen und Features aktiviert wurden. Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Falscher physischer Pfad der Website oder App fehlt

* **Browser:** „403 – Verboten: Zugriff verweigert.“ **oder** „403.14 Verboten: Der Webserver wurde dafür konfiguriert, die Inhalte dieses Verzeichnisses nicht aufzulisten.

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

Überprüfen Sie die **Grundeinstellungen** der IIS-Website und den physischen App-Ordner. Vergewissern Sie sich, dass sich die App im Ordner im **physischen Pfad** der IIS-Website befindet.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>„Falsche Rolle“, „ASP.NET Core-Modul nicht installiert“ oder „Falsche Berechtigungen“

* **Browser:** 500.19 – Interner Serverfehler: Auf die angeforderte Seite kann nicht zugegriffen werden, da die zugehörigen Konfigurationsdaten für die Seite ungültig sind. **oder** „Diese Seite kann nicht angezeigt werden.“

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

* Vergewissern Sie sich, dass die richtige Rolle aktiviert wurde. Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).

* Öffnen Sie **Programme und Features** oder **Apps und Features**, und vergewissern Sie sich, dass **Windows Server Hosting** installiert ist. Wenn **Windows Server Hosting** nicht in der Liste der installierten Programme vorhanden ist, laden Sie das .NET Core Hosting-Paket herunter, und installieren Sie es.

  [Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Stellen Sie sicher, dass **Anwendungspool** > **Prozessmodell** > **Identity** auf **ApplicationPoolIdentity** festgelegt ist, oder dass die benutzerdefinierte Identität über die erforderlichen Berechtigungen für den Zugriff auf den Bereitstellungsordner der App verfügt.

* Wenn Sie das ASP.NET Core-Hostingpaket deinstallieren und eine frühere Version des Hostingpakets installieren, ist in der *applicationHost.config*-Datei kein Abschnitt für das ASP.NET Core-Modul enthalten. Öffnen Sie die *applicationHost.config*-Datei unter *%windir%/System32/inetsrv/config*, und suchen Sie nach der Abschnittsgruppe `<configuration><configSections><sectionGroup name="system.webServer">`. Wenn der Abschnitt für das ASP.NET Core-Modul in der Abschnittsgruppe nicht vorhanden ist, fügen Sie dieses Abschnittselement hinzu:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Installieren Sie alternativ die neueste Version des ASP.NET Core-Hostingbundles. Die neueste Version ist mit den unterstützten ASP.NET Core-Apps abwärtskompatibel.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Falscher processPath-Wert, fehlende PATH-Variable, Hostingpaket nicht installiert, System/IIS wird nicht neu gestartet, VC++ Redistributable nicht installiert oder dotnet.exe-Zugriffsverletzung

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}" ', ErrorCode = '0x80070002 : 0. Application '{PATH}' wasn't able to start. Es wurde keine ausführbare Datei unter „{PATH}“ gefunden. Die Anwendung „/LM/W3SVC/2/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8007023e.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Ereignisprotokoll: 'Application '{PATH}' wasn't able to start. Es wurde keine ausführbare Datei unter „{PATH}“ gefunden. Failed HRESULT returned: 0x8007023e

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Überprüfen Sie das Attribut *processPath* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass es den Wert `dotnet` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) oder `.\{ASSEMBLY}.exe` für eine [eigenständige Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) enthält.

* Für eine Framework-abhängige Bereitstellung ist *dotnet.exe* möglicherweise über die PATH-Einstellungen nicht verfügbar. Überprüfen Sie, ob *C:\Programme\dotnet\\* in den PATH-Einstellungen des Systems vorhanden ist.

* Für eine frameworkabhängige Bereitstellung ist *dotnet.exe* möglicherweise für die Benutzeridentität des App-Pools nicht verfügbar. Vergewissern Sie sich, dass die Benutzeridentität des App-Pools Zugriff auf das Verzeichnis *C:\Programme\dotnet* hat. Vergewissern Sie sich, dass keine Ablehnungsregeln für die Benutzeridentität des App-Pools im Verzeichnis *C:\Programme\dotnet* und in den App-Verzeichnissen konfiguriert sind.

* Möglicherweise wurde eine FDD bereitgestellt und .NET Core installiert, ohne IIS neu zu starten. Starten Sie den Server neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.

* Möglicherweise wurde eine FDD bereitgestellt, ohne die .NET Core-Runtime auf dem Hostsystem zu installieren. Führen Sie den **Installer für das .NET Core-Hostingpaket** auf dem System aus, wenn die .NET Core-Runtime nicht installiert wurde.

  [Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System. Schließen Sie die Installation ab, indem Sie das System oder IIS neu starten. Führen Sie dazu **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung aus.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Falsche Argumente des Elements \<aspNetCore>

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen. Could not find inprocess request handler. Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands? Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Fehler beim Starten der Anwendung "/ LM/W3SVC/3/ROOT", ErrorCode "0x8000ffff".

* **stdout-Protokoll des ASP.NET Core-Moduls:** Did you mean to run dotnet SDK commands? Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **Debugprotokoll des ASP.NET Core-Moduls:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen. Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler. Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands? Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT-Fehler zurückgegeben: 0x8000ffff

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Überprüfen Sie das Attribut *arguments* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass der Wert entweder `.\{ASSEMBLY}.dll` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) entspricht oder nicht vorhanden, eine leere Zeichenfolge (`arguments=""`) oder eine Liste von Argumenten der App (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) für eine eigenständige Bereitstellung (Self-Contained Deployment, SCD) ist.

## <a name="missing-net-core-shared-framework"></a>Fehlendes freigegebenes .NET Core-Framework

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen. Could not find inprocess request handler. Captured output from invoking hostfxr: Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.

Die Anwendung „/LM/W3SVC/5/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8000ffff.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.

* **Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8000ffff

Problembehandlung:

Stellen Sie für eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) sicher, dass die richtige Runtime im System installiert ist.

## <a name="stopped-application-pool"></a>Beendeter Anwendungspool

* **Browser:** 503 – Dienst nicht verfügbar

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

Vergewissern Sie sich, dass der Anwendungspool nicht den Status *Beendet* aufweist.

## <a name="sub-application-includes-a-handlers-section"></a>Untergeordnete Anwendung mit einem \<handlers>-Abschnitt

* **Browser:** HTTP-Fehler 500.19: Interner Serverfehler

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation. Die Protokolldatei der untergeordneten App wurde nicht erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation. Die Protokolldatei der untergeordneten App wurde nicht erstellt.

Problembehandlung:

Überprüfen Sie, ob der Abschnitt `<handlers>` in der *web.config*-Datei der untergeordneten App fehlt oder ob die untergeordnete App die Handler der übergeordneten App nicht erbt.

Der Abschnitt `<system.webServer>` in der *web.config*-Datei der übergeordneten App befindet sich in einem `<location>`-Element. Die Eigenschaft <xref:System.Configuration.SectionInformation.InheritInChildApplications*> wird auf `false` festgelegt, um anzugeben, dass die im Element [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der übergeordneten App gespeichert sind. Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>Der stdout-Protokollpfad ist falsch

* **Browser:** Die App reagiert normal.

* **Anwendungsprotokoll:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden. Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben. Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden. Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}. Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

* **Debugprotokoll des ASP.NET Core-Moduls:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden. Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben. Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden. Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}. Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.

Problembehandlung:

* Der im Element `<aspNetCore>` angegebene Pfad `stdoutLogFile` in der Datei *web.config* ist nicht vorhanden. Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul: Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Der Benutzer des App-Pools hat keinen Schreibzugriff für den Pfad des stdout-Protokolls.

## <a name="application-configuration-general-issue"></a>Allgemeines Problem mit der Anwendungskonfiguration

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)  **oder**  HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Variable

* **stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer oder wurde mit gewöhnlichen Einträgen erstellt, bis die App fehlgeschlagen ist.

* **Debugprotokoll des ASP.NET Core-Moduls:** Variable

Problembehandlung:

Der Prozess konnte nicht gestartet werden, weil wahrscheinlich ein Problem mit der Konfiguration oder Programmierung der App vorliegt.

Weitere Informationen finden Sie unter den folgenden Themen:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

In diesem Artikel werden häufige Fehler beschrieben und Ratschläge zu bestimmten Fehlern beim Hosten von ASP.NET Core-Apps auf Azure App Service und IIS erteilt.

Allgemeine Anleitungen zur Problembehandlung finden Sie unter <xref:test/troubleshoot-azure-iis>.

Sammeln Sie folgende Informationen:

* Browserverhalten (Statuscode und Fehlermeldung)
* Einträge im Anwendungsereignisprotokoll
  * Azure App Service: Siehe <xref:test/troubleshoot-azure-iis>.
  * IIS
    1. Klicken Sie im **Windows**-Menü auf **Start**, geben Sie *Ereignisanzeige* ein, und drücken Sie die **EINGABETASTE**.
    1. Erweitern Sie in der **Ereignisanzeige** die Option **Windows-Protokolle** > **Anwendung** auf der Randleiste.
* stdout- und Debugprotokolleinträge im ASP.NET Core-Modul
  * Azure App Service: Siehe <xref:test/troubleshoot-azure-iis>.
  * IIS: Befolgen Sie die Anweisungen in den Abschnitten [Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) und [Erweiterte Diagnoseprotokolle](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) des Artikels zum ASP.NET Core-Modul.

Vergleichen Sie die Fehlerinformationen mit folgenden häufigen Fehlern. Befolgen Sie die Hinweise zur Fehlerbehebung, wenn eine Übereinstimmung gefunden wird.

Die Fehlerliste in diesem Artikel ist nicht vollständig. Wenn bei Ihnen ein Fehler auftritt, der hier nicht aufgeführt wird, öffnen Sie über die Schaltfläche **Feedback zum Inhalt** am Ende des Artikels ein neues Issue, in dem Sie den Fehler ausführlich beschreiben sollten, damit er reproduziert werden kann.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>Durch ein Upgrade des Betriebssystems wird das ASP.NET Core-Modul (32-Bit) entfernt

**Anwendungsprotokoll:** Fehler beim Laden der Modul-DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**. Die Daten sind der Fehler.

Problembehandlung:

Nicht zum Betriebssystem gehörende Dateien im Verzeichnis **C:\Windows\SysWOW64\inetsrv** werden während eines Betriebssystemupgrades nicht beibehalten. Dieses Problem tritt auf, wenn vor der Durchführung eines Betriebssystemupgrades das ASP.NET Core-Modul installiert wurde und nach einem Betriebssystemupgrade ein App-Pool im 32-Bit-Modus ausgeführt wird. Reparieren Sie nach einem Betriebssystemupgrade das ASP.NET Core-Modul. Siehe [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle). Wählen Sie **Reparatur** aus, wenn der Installer ausgeführt wird.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Websiteerweiterung fehlt, 32-Bit- und 64-Bit-Websiteerweiterungen (x86 und x64) installiert oder falsche Prozessbitanzahl festgelegt

*Gilt für Apps, die von Azure App Services gehostet werden.*

* **Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln. Could not find inprocess request handler. Captured output from invoking hostfxr: Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden. Die Anwendung „/LM/W3SVC/1416782824/ROOT“ konnte nicht gestartet werden. Fehlercode: „0x8000ffff“.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden. Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.

Problembehandlung:

* Wenn die App in einer Vorschauruntime ausgeführt wird, installieren Sie entweder die 32-Bit- **oder** 64-Bit-Websiteerweiterung (x86 oder x64), die der Bitanzahl und der Runtimeversion der App entspricht. **Installieren Sie nicht sowohl Erweiterungen als auch mehrere Runtimeversionen der Erweiterung.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Runtime
  * ASP.NET Core {RUNTIME VERSION} (x64) Runtime

  Starten Sie die App neu. Warten Sie einige Sekunden, bis die App neu startet.

* Wenn Sie die App auf einer Vorschauruntime ausführen und sowohl die 32-Bit- als auch die 64-Bit-[Websiteerweiterung](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) (x86 und x64) installiert sind, deinstallieren Sie die Websiteerweiterung, die nicht der Bitanzahl der App entspricht. Starten Sie die App nach dem Entfernen der Websiteerweiterung neu. Warten Sie einige Sekunden, bis die App neu startet.

* Wenn Sie die App auf einer Vorschauruntime ausführen und die Bitanzahl der Websiteerweiterung mit der Bitanzahl der App übereinstimmt, bestätigen Sie, dass die *Runtimeversion* der Websiteerweiterung der Vorschau der Runtimeversion der App entspricht.

* Überprüfen Sie, ob die **Plattform** der App in **Anwendungseinstellungen** der Bitanzahl der App entspricht.

Weitere Informationen finden Sie unter <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Bereitstellung einer x86-App, obwohl der App-Pool nicht für 32-Bit-Apps aktiviert ist

* **Browser:** HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)

* **Anwendungsprotokoll:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'. Überprüfen Sie die stderr-Protokolle, um weitere Informationen zu erhalten. Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application. Der CLR-Arbeitsthread wurde vorzeitig beendet

* **stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.

Dieses Szenario wird vom SDK ausgelöst, wenn eine eigenständige App veröffentlicht wird. Das SDK generiert einen Fehler, wenn die relative ID (RID) nicht mit der Zielplattform übereinstimmt (z. B. bei der RID `win10-x64` für eine Projektdatei mit `<PlatformTarget>x86</PlatformTarget>`).

Problembehandlung:

Aktivieren Sie den IIS-App-Pool für 32-Bit-Apps, wenn Sie von x86-Frameworks abhängige Bereitstellungen (`<PlatformTarget>x86</PlatformTarget>`) durchführen möchten. Öffnen Sie im IIS-Manager die Option **Erweiterte Einstellungen** des App-Pools, und legen Sie **32-Bit-Anwendungen aktivieren** auf **Wahr** fest.

## <a name="platform-conflicts-with-rid"></a>Plattformkonflikte mit RID

* **Browser:** HTTP-Fehler 502.5: Prozessfehler

* **Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm „C:\{PATH}\' konnte den Prozess mit der Befehlszeile „C:\{PATH}{assembly}.{exe|dll}“ nicht starten. Fehlercode = 0x80004005 : ff.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Ausnahmefehler: System.BadImageFormatException: Die Datei oder Assembly „{ASSEMBLY}.dll“ wurde nicht gefunden. Es wurde versucht, ein Programm mit einem falschen Format zu laden.

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Wenn diese Ausnahme für eine Azure-App-Bereitstellung beim Aktualisieren einer App und beim Bereitstellen neuerer Assemblys auftritt, löschen Sie alle Dateien aus der vorherigen Bereitstellung manuell. Veraltete inkompatible Assemblys können zu einer `System.BadImageFormatException`-Ausnahme führen, wenn Sie eine aktualisierte App bereitstellen.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI-Endpunkt falsch oder beendete Website

* **Browser:** „ERR_CONNECTION_REFUSED“ **oder** „Es kann keine Verbindung hergestellt werden.“

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

* Vergewissern Sie sich, dass der richtige URI-Endpunkt für die App verwendet wird. Überprüfen Sie die Bindungen.

* Vergewissern Sie sich, dass die IIS-Website nicht den Status *Beendet* aufweist.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine oder W3SVC-Serverfeatures deaktiviert

**Betriebssystemausnahme:** Die Features „CoreWebEngine“ und „W3SVC“ von IIS 7.0 müssen installiert sein, um das ASP.NET Core-Modul zu verwenden.

Problembehandlung:

Vergewissern Sie sich, dass die richtigen Rollen und Features aktiviert wurden. Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).

## <a name="incorrect-website-physical-path-or-app-missing"></a>Falscher physischer Pfad der Website oder App fehlt

* **Browser:** „403 – Verboten: Zugriff verweigert.“ **oder** „403.14 Verboten: Der Webserver wurde dafür konfiguriert, die Inhalte dieses Verzeichnisses nicht aufzulisten.

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

Überprüfen Sie die **Grundeinstellungen** der IIS-Website und den physischen App-Ordner. Vergewissern Sie sich, dass sich die App im Ordner im **physischen Pfad** der IIS-Website befindet.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>„Falsche Rolle“, „ASP.NET Core-Modul nicht installiert“ oder „Falsche Berechtigungen“

* **Browser:** 500.19 – Interner Serverfehler: Auf die angeforderte Seite kann nicht zugegriffen werden, da die zugehörigen Konfigurationsdaten für die Seite ungültig sind. **oder** „Diese Seite kann nicht angezeigt werden.“

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

* Vergewissern Sie sich, dass die richtige Rolle aktiviert wurde. Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).

* Öffnen Sie **Programme und Features** oder **Apps und Features**, und vergewissern Sie sich, dass **Windows Server Hosting** installiert ist. Wenn **Windows Server Hosting** nicht in der Liste der installierten Programme vorhanden ist, laden Sie das .NET Core Hosting-Paket herunter, und installieren Sie es.

  [Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

* Stellen Sie sicher, dass **Anwendungspool** > **Prozessmodell** > **Identity** auf **ApplicationPoolIdentity** festgelegt ist, oder dass die benutzerdefinierte Identität über die erforderlichen Berechtigungen für den Zugriff auf den Bereitstellungsordner der App verfügt.

* Wenn Sie das ASP.NET Core-Hostingpaket deinstallieren und eine frühere Version des Hostingpakets installieren, ist in der *applicationHost.config*-Datei kein Abschnitt für das ASP.NET Core-Modul enthalten. Öffnen Sie die *applicationHost.config*-Datei unter *%windir%/System32/inetsrv/config*, und suchen Sie nach der Abschnittsgruppe `<configuration><configSections><sectionGroup name="system.webServer">`. Wenn der Abschnitt für das ASP.NET Core-Modul in der Abschnittsgruppe nicht vorhanden ist, fügen Sie dieses Abschnittselement hinzu:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Installieren Sie alternativ die neueste Version des ASP.NET Core-Hostingbundles. Die neueste Version ist mit den unterstützten ASP.NET Core-Apps abwärtskompatibel.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Falscher processPath-Wert, fehlende PATH-Variable, Hostingpaket nicht installiert, System/IIS wird nicht neu gestartet, VC++ Redistributable nicht installiert oder dotnet.exe-Zugriffsverletzung

* **Browser:** HTTP-Fehler 502.5: Prozessfehler

* **Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}" ', ErrorCode = '0x80070002 : 0.

* **stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Überprüfen Sie das Attribut *processPath* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass es den Wert `dotnet` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) oder `.\{ASSEMBLY}.exe` für eine [eigenständige Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) enthält.

* Für eine Framework-abhängige Bereitstellung ist *dotnet.exe* möglicherweise über die PATH-Einstellungen nicht verfügbar. Überprüfen Sie, ob *C:\Programme\dotnet\\* in den PATH-Einstellungen des Systems vorhanden ist.

* Für eine frameworkabhängige Bereitstellung ist *dotnet.exe* möglicherweise für die Benutzeridentität des App-Pools nicht verfügbar. Vergewissern Sie sich, dass die Benutzeridentität des App-Pools Zugriff auf das Verzeichnis *C:\Programme\dotnet* hat. Vergewissern Sie sich, dass keine Ablehnungsregeln für die Benutzeridentität des App-Pools im Verzeichnis *C:\Programme\dotnet* und in den App-Verzeichnissen konfiguriert sind.

* Möglicherweise wurde eine FDD bereitgestellt und .NET Core installiert, ohne IIS neu zu starten. Starten Sie den Server neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.

* Möglicherweise wurde eine FDD bereitgestellt, ohne die .NET Core-Runtime auf dem Hostsystem zu installieren. Führen Sie den **Installer für das .NET Core-Hostingpaket** auf dem System aus, wenn die .NET Core-Runtime nicht installiert wurde.

  [Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).

  Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System. Schließen Sie die Installation ab, indem Sie das System oder IIS neu starten. Führen Sie dazu **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung aus.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>Falsche Argumente des Elements \<aspNetCore>

* **Browser:** HTTP-Fehler 502.5: Prozessfehler

* **Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.

* **stdout-Protokoll des ASP.NET Core-Moduls:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'

Problembehandlung:

* Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird. Ein Prozessfehler könnte die Folge eines Problems in der App sein. Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.

* Überprüfen Sie das Attribut *arguments* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass der Wert entweder `.\{ASSEMBLY}.dll` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) entspricht oder nicht vorhanden, eine leere Zeichenfolge (`arguments=""`) oder eine Liste von Argumenten der App (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) für eine eigenständige Bereitstellung (Self-Contained Deployment, SCD) ist.

Problembehandlung:

Stellen Sie für eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) sicher, dass die richtige Runtime im System installiert ist.

## <a name="stopped-application-pool"></a>Beendeter Anwendungspool

* **Browser:** 503 – Dienst nicht verfügbar

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

Vergewissern Sie sich, dass der Anwendungspool nicht den Status *Beendet* aufweist.

## <a name="sub-application-includes-a-handlers-section"></a>Untergeordnete Anwendung mit einem \<handlers>-Abschnitt

* **Browser:** HTTP-Fehler 500.19: Interner Serverfehler

* **Anwendungsprotokoll:** Kein Eintrag

* **stdout-Protokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation. Die Protokolldatei der untergeordneten App wurde nicht erstellt.

Problembehandlung:

Überprüfen Sie, ob die Datei *web.config* der untergeordneten App einen `<handlers>`-Abschnitt enthält.

## <a name="stdout-log-path-incorrect"></a>Der stdout-Protokollpfad ist falsch

* **Browser:** Die App reagiert normal.

* **Anwendungsprotokoll:** Warnung: Die stdout-Protokolldatei „ \\?\{ PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log“ konnte nicht erstellt werden. Fehlercode: -2147024893.)

* **stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.

Problembehandlung:

* Der im Element `<aspNetCore>` angegebene Pfad `stdoutLogFile` in der Datei *web.config* ist nicht vorhanden. Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul: Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).

* Der Benutzer des App-Pools hat keinen Schreibzugriff für den Pfad des stdout-Protokolls.

## <a name="application-configuration-general-issue"></a>Allgemeines Problem mit der Anwendungskonfiguration

* **Browser:** HTTP-Fehler 502.5: Prozessfehler

* **Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm ‚C:\{PATH}\' hat den Prozess mit der Befehlszeile „C:\{PATH}\{ASSEMBLY}.{exe|dll}“ erstellt, ist aber abgestürzt, reagiert nicht oder lauscht dem angegebenen Port „{PORT}“ nicht. Fehlercode: {ERROR CODE}.)

* **stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.

Problembehandlung:

Der Prozess konnte nicht gestartet werden, weil wahrscheinlich ein Problem mit der Konfiguration oder Programmierung der App vorliegt.

Weitere Informationen finden Sie unter den folgenden Themen:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
