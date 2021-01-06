---
title: Datei „web.config“
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zum Inhalt der web.config-Datei und zum Konfigurieren verschiedener Optionen des ASP.NET Core-Moduls.
monikerRange: '>= aspnetcore-5.0'
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
uid: host-and-deploy/iis/web-config
ms.openlocfilehash: edeef31042547db79fcec98f1236787f78e187a5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057283"
---
# <a name="webconfig-file"></a>`web.config`-Datei

Bei `web.config` handelt es sich um eine Datei, die von den IIS und vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) gelesen wird, um eine mit den IIS gehostete App zu konfigurieren.

## <a name="webconfig-file-location"></a>Speicherort der Datei `web.config`

Zum Erstellen des [ASP.NET Core-Moduls](xref:host-and-deploy/aspnet-core-module) muss die Datei `web.config` im [Inhaltsstammpfad](xref:fundamentals/index#content-root) (normalerweise dem App-Basispfad) der bereitgestellten App vorhanden sein. Dies ist der physische Pfad der Website, der in IIS bereitgestellt wurde. Die Datei `web.config` wird im Stammverzeichnis der App benötigt, um die Veröffentlichung mehrerer Apps mit Web Deploy zu ermöglichen.

Vertrauliche Dateien wie `{ASSEMBLY}.runtimeconfig.json`, `{ASSEMBLY}.xml` (Kommentare zur XML-Dokumentation) und `{ASSEMBLY}.deps.json` sind im physischen Pfad der App vorhanden. Der Platzhalter `{ASSEMBLY}` steht hierbei für den Assemblynamen. Wenn die `web.config`-Datei vorhanden ist und die Website normal startet, werden diese vertraulichen Dateien bei Anforderung nicht offengelegt. Wenn die Datei `web.config` fehlt, falsch benannt wurde oder die Website nicht für den normalen Start konfigurieren kann, macht IIS vertrauliche Dateien möglicherweise öffentlich verfügbar.

**Die Datei `web.config` muss immer in der Bereitstellung vorhanden, richtig benannt und in der Lage sein, die Website für einen normalen Start zu konfigurieren. Entfernen Sie die Datei `web.config` niemals aus einer Produktionsbereitstellung.**

Wenn im Projekt keine Datei namens `web.config` vorhanden ist, wird sie zur Konfiguration des ASP.NET Core-Moduls mit dem richtigen `processPath`- und `arguments`-Attribut erstellt und in die [veröffentlichte Ausgabe](xref:host-and-deploy/directory-structure) verschoben.

Ist eine Datei namens `web.config` im Projekt vorhanden, wird sie zur Konfiguration des ASP.NET Core-Moduls mit dem richtigen `processPath`- und `arguments`-Attribut transformiert und in die veröffentlichte Ausgabe verschoben. Die Transformation ändert die IIS-Konfigurationseinstellungen in der Datei nicht.

Die Datei `web.config` kann zusätzliche IIS-Konfigurationseinstellungen zum Steuern der aktiven IIS-Module bereitstellen. Informationen zu IIS-Modulen, die Anforderungen mit ASP.NET Core-Apps verarbeiten können, finden Sie im Thema [IIS-Module](xref:host-and-deploy/iis/modules).

Die Erstellung, Transformation und Veröffentlichung der Datei `web.config` wird bei der Projektveröffentlichung von einem MSBuild-Ziel (`_TransformWebConfig`) abgewickelt. Dieses Ziel ist in den Web SDK-Zielen (`Microsoft.NET.Sdk.Web`) vorhanden. Das SDK wird am Anfang der Projektdatei festgelegt:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Verwenden Sie die Eigenschaft `<IsTransformWebConfigDisabled>` in der Projektdatei, um zu verhindern, dass das Web-SDK die Datei `web.config` transformiert:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Wenn die Transformation der Datei durch das Web-SDK deaktiviert wird, müssen die Attribute `processPath` und `arguments` manuell durch den Entwickler festgelegt werden. Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-aspnet-core-module-with-webconfig"></a>Konfiguration des ASP.NET Core-Moduls mit `web.config`

Das ASP.NET Core-Modul wurde mit dem `aspNetCore`-Abschnitt des `system.webServer`-Knotens in der Datei `web.config` der Site konfiguriert.

Die folgende `web.config`-Datei wird für eine [Framework-abhängige Bereitstellung](/dotnet/articles/core/deploying/#framework-dependent-deployments-fdd) veröffentlicht und konfiguriert für da sASP.NET Core-Modul die Handhabung von Siteanforderungen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath="dotnet"
                  arguments=".\MyApp.dll"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Die folgende `web.config`-Datei wird für eine [eigenständige Bereitstellung](/dotnet/articles/core/deploying/#self-contained-deployments-scd) veröffentlicht:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\MyApp.exe"
                  stdoutLogEnabled="false"
                  stdoutLogFile=".\logs\stdout"
                  hostingModel="inprocess" />
    </system.webServer>
  </location>
</configuration>
```

Die <xref:System.Configuration.SectionInformation.InheritInChildApplications%2A>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [`<location>`](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der App gespeichert sind.

Wenn eine App für [Azure App Service](https://azure.microsoft.com/services/app-service/) bereitgestellt wird, wird der Pfad `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` gesetzt. Der Pfad speichert stdout-Protokolle zum Ordner `LogFiles`, einem Speicherort, der automatisch vom Dienst erstellt wird.

Weitere Informationen zur Konfiguration von IIS untergeordneten Anwendungen finden Sie unter <xref:host-and-deploy/iis/index#sub-applications>.

### <a name="attributes-of-the-aspnetcore-element"></a>Attribute des `aspNetCore`-Elements

| attribute | BESCHREIBUNG | Standard |
| --------- | ----------- | :-----: |
| `arguments` | <p>Optionales Zeichenfolgeattribut.</p><p>Argumente zur ausführbaren Datei, die in `processPath` angegeben wurde.</p> | |
| `disableStartUpErrorPage` | <p>Optionales boolesches Attribut.</p><p>Wenn TRUE, wird die Seite **502.5: Prozessfehler** unterdrückt, und die in der Datei `web.config` konfigurierte Seite für den Statuscode 502 hat Vorrang.</p> | `false` |
| `forwardWindowsAuthToken` | <p>Optionales boolesches Attribut.</p><p>Wenn TRUE, wird das Token an den untergeordneten Prozess weitergeleitet, der an `%ASPNETCORE_PORT%` als Header 'MS-ASPNETCORE-WINAUTHTOKEN' pro Anforderung lauscht. Es liegt in der Verantwortung des entsprechenden Prozesses, CloseHandle auf dem Token pro Anforderung aufzurufen.</p> | `true` |
| `hostingModel` | <p>Optionales Zeichenfolgeattribut.</p><p>Gibt das Hostingmodell als In-Process (`InProcess`/`inprocess`) oder Out-of-Process (`OutOfProcess`/`outofprocess`) an.</p> | `InProcess`<br>`inprocess` |
| `processesPerApplication` | <p>Optionales Ganzzahlattribut.</p><p>Gibt die Anzahl der Instanzen des Prozesses aus der Einstellung `processPath` an, die aus der App gestartet werden können.</p><p>&dagger;Für In-Process-Hosting ist dieser Wert auf `1` beschränkt.</p><p>Einstellen von `processesPerApplication` wird nicht empfohlen. Dieses Attribut wird in einer der nächsten Releases entfernt.</p> | Standardwert: `1`<br>Min.: `1`<br>Max.: `100`&dagger; |
| `processPath` | <p>Erforderliches Zeichenfolgenattribut.</p><p>Pfad zur ausführbaren Datei, die einen Prozess startet, der auf HTTP-Anforderungen lauscht. Relative Pfade werden unterstützt. Wenn der Pfad mit `.` beginnt, gilt er als relativ zum Stammverzeichnis.</p> | |
| `rapidFailsPerMinute` | <p>Optionales Ganzzahlattribut.</p><p>Gibt an, wie viele Abstürze des in `processPath` angegebenen Prozesses pro Minute zulässig sind. Wenn dieses Limit überschritten wird, beendet das Modul das Starten des Prozesses für den Rest der Minute.</p><p>Bei In-Process-Hosting nicht unterstützt.</p> | Standardwert: `10`<br>Min.: `0`<br>Max.: `100` |
| `requestTimeout` | <p>Optionales TimeSpan-Attribut.</p><p>Gibt an, wie lange das ASP.NET Core-Modul auf eine Antwort des Prozesses wartet, der auf „% ASPNETCORE_PORT“ lauscht.</p><p>In den Versionen des ASP.NET Core-Moduls, die mit Version ASP.NET Core 2.1 oder später ausgeliefert wurden, wird `requestTimeout` in Stunden, Minuten und Sekunden angegeben.</p><p>Trifft auf In-Process-Hosting nicht zu. Bei In-Process-Hosting wartet das Modul darauf, dass die App die Anforderung verarbeitet.</p><p>Gültige Werte für Minuten- und Sekundensegmente der Zeichenfolge befinden sich im Bereich 0–59. Die Verwendung von `60` im Wert für Minuten- oder Sekundenergebnisse führt zu einem *500 – Interner Serverfehler*.</p> | Standardwert: `00:02:00`<br>Min.: `00:00:00`<br>Max.: `360:00:00` |
| `shutdownTimeLimit` | <p>Optionales Ganzzahlattribut.</p><p>Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei ordnungsgemäß beendet wird, wenn die Datei `app_offline.htm` erkannt wird.</p> | Standardwert: `10`<br>Min.: `0`<br>Max.: `600` |
| `startupTimeLimit` | <p>Optionales Ganzzahlattribut.</p><p>Gibt in Sekunden an, wie lange das Modul darauf wartet, dass die ausführbare Datei einen Prozess startet, der an dem Port lauscht. Wenn dieses Zeitlimit überschritten wird, beendet das Modul den Prozess.</p><p>Bei *In-Process*-Hosting: Der Prozess wird **nicht** neu gestartet und verwendet **nicht** die Einstellung `rapidFailsPerMinute`.</p><p>Bei *Out-of-Process*-Hosting: Das Modul versucht, den Prozess neu zu starten, wenn es eine neue Anforderung erhält, und versucht weiterhin, den Prozess bei nachfolgenden eingehenden Anforderungen neu zu starten, es sei denn, die App kann `rapidFailsPerMinute`-Anzahl in der letzten fortlaufenden Minute nicht starten.</p><p>Der Wert 0 (null) wird **nicht** als unendliches Timeout angesehen.</p> | Standardwert: `120`<br>Min.: `0`<br>Max.: `3600` |
| `stdoutLogEnabled` | <p>Optionales boolesches Attribut.</p><p>Wenn TRUE, werden `stdout` und `stderr` für den Prozess, der in `processPath` angegeben wurde, zu der Datei weitergeleitet, die in `stdoutLogFile` angegeben wurde.</p> | `false` |
| `stdoutLogFile` | <p>Optionales Zeichenfolgeattribut.</p><p>Gibt den relativen oder absoluten Pfad an, für den `stdout` und `stderr` aus dem in `processPath` angegebenen Prozess protokolliert wurden. Relative Pfade sind relativ zum Stamm der Site. Jeder mit `.` beginnende Pfad ist zum Stammverzeichnis relativ, und alle anderen Pfade werden als absolute Pfade behandelt. Ordner, die im Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird. Mithilfe von Unterstrichtrennzeichen werden ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung ( `.log`) dem letzten Segment des Pfads `stdoutLogFile` hinzugefügt. Wenn `.\logs\stdout` als Wert angegeben wird, wird ein stdout-Beispielprotokoll als `stdout_20180205194132_1934.log` im Ordner `logs` gespeichert, sofern es am 2.5.2018 um 19:41:32 mit Prozess-ID 1934 gespeichert wurde.</p> | `aspnetcore-stdout` |

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Umgebungsvariablen können für den Prozess im Attribut `processPath` angegeben werden. Geben Sie eine Umgebungsvariable mit dem untergeordneten Element `<environmentVariable>` eines `<environmentVariables>`-Auflistungselements an. In diesem Abschnitt festgelegte Umgebungsvariablen haben Vorrang vor Systemumgebungsvariablen.

Im folgenden Beispiel werden zwei Umgebungsvariablen in `web.config` festgelegt. `ASPNETCORE_ENVIRONMENT` konfiguriert die Umgebung der App als `Development`. Ein Entwickler setzt diesen Wert möglicherweise vorübergehend in der Datei `web.config`, um das Laden der [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) beim Debugging einer App-Ausnahme zu erzwingen. `CONFIG_DIR` ist ein Beispiel für eine benutzerdefinierte Umgebungsvariable, wobei der Entwickler Code geschrieben hat, der den Wert beim Start liest, um einen Pfad zum Laden der Konfigurationsdatei der App zu bilden.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="inprocess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
    <environmentVariable name="CONFIG_DIR" value="f:\application_config" />
  </environmentVariables>
</aspNetCore>
```

> [!NOTE]
> Eine Alternative zum direkten Festlegen der Umgebung in `web.config` ist das Einschließen der `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (`.pubxml`)](xref:host-and-deploy/visual-studio-publish-profiles) oder eine Projektdatei. Dieser Ansatz legt die Umgebung in `web.config` fest, wenn das Projekt veröffentlicht wird:
>
> ```xml
> <PropertyGroup>
>   <EnvironmentName>Development</EnvironmentName>
> </PropertyGroup>
> ```

> [!WARNING]
> Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` nur auf Staging- und Testservern auf `Development` fest, auf die nicht vertrauenswürdige Netzwerke, z.B. das Internet, nicht zugreifen können.

## <a name="configuration-of-iis-with-webconfig"></a>IIS-Konfiguration mit `web.config`

Die IIS-Konfiguration wird von dem Abschnitt `<system.webServer>` der Datei `web.config` für IIS-Szenarien beeinflusst, die für ASP.NET Core-Apps mit dem ASP.NET Core-Modul funktional sind. Beispielsweise eignet sich die IIS-Konfiguration für dynamische Komprimierung. Wenn IIS auf Serverebene für die Verwendung der dynamischen Komprimierung konfiguriert ist, kann diese Einstellung mit dem `<urlCompression>`-Element in der Datei `web.config` der App für eine ASP.NET Core-App deaktiviert werden.

Weitere Informationen finden Sie unter den folgenden Themen:

* [Referenz zur Konfiguration von `<system.webServer>`](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/modules>

Lesen Sie den Abschnitt zum *Befehl `AppCmd.exe`* im Thema [Umgebungsvariablen`<environmentVariables>`](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) in der IIS-Referenzdokumentation, um Umgebungsvariablen für einzelne Apps festzulegen, die in isolierten App-Pools ausgeführt werden (unterstützt für IIS 10.0 oder höher).

## <a name="configuration-sections-of-webconfig"></a>Konfigurationsabschnitte für `web.config`

Konfigurationsabschnitte von ASP.NET 4.x-Apps in der Datei `web.config` werden von ASP.NET Core-Apps nicht zur Konfiguration verwendet:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core-Apps werden mit anderen Konfigurationsanbietern konfiguriert. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

## <a name="transform-webconfig"></a>Transformieren von web.config

Wenn Sie `web.config` beim Veröffentlichen transformieren müssen, finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>. Sie müssen möglicherweise `web.config` beim Veröffentlichen transformieren, um Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festzulegen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [IIS\<system.webServer>](/iis/configuration/system.webServer/)
* <xref:host-and-deploy/iis/modules>
* <xref:host-and-deploy/iis/transform-webconfig>
