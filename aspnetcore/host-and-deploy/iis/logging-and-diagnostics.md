---
title: Protokollerstellung und -umleitung mit dem ASP.NET Core-Modul
author: rick-anderson
description: Konfigurieren Sie IIS und das ASP.NET Core-Modul, um Protokolle und Diagnoseinformationen aufzuzeichnen.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 9af2311dd1f42cce3547c8215af22d2613453510
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755168"
---
# <a name="log-creation-and-redirection"></a>Protokollerstellung und Weiterleitung

Das ASP.NET Core-Modul leitet die Konsolenausgabe „stdout“ und „stderr“ auf den Datenträger weiter, wenn die Attribute `stdoutLogEnabled` und `stdoutLogFile` des `aspNetCore`-Elements festgelegt werden. Ordner, die im `stdoutLogFile`-Pfad angegeben werden, werden vom Modul erstellt, wenn die Protokolldatei erstellt wird. Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, in den die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\{APP POOL NAME}`, um die Schreibberechtigung bereitzustellen, wenn der Platzhalter `{APP POOL NAME}` der App-Poolname ist).

Protokolle werden nur dann rotiert, wenn die Prozesswiederverwendung/der Prozessneustart stattfindet. Der Hoster ist für die Begrenzung des Speicherplatzes zuständig, den die Protokolle nutzen.

Die Verwendung des stdout-Protokolls wird nur für die Problembehandlung bei App-Startproblemen beim Hosten in IIS oder beim Verwenden von [Support zum Zeitpunkt der Entwicklung für IIS in Visual Studio](xref:host-and-deploy/iis/development-time-iis-support) empfohlen, nicht aber für das lokale Debuggen und das Ausführen der App mit IIS Express.

Verwenden Sie das Protokoll „stdout“ nicht zu allgemeinen App-Protokollierungszwecken. Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert. Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).

Ein Zeitstempel und eine Dateierweiterung werden automatisch hinzugefügt, wenn die Protokolldatei erstellt wird. Ein Protokolldateiname wird erstellt, indem der Zeitstempel, die Prozess-ID und die Dateierweiterung ( `.log`) an das letzte Segment des `stdoutLogFile`-Pfads (in der Regel `stdout`), getrennt durch Unterstriche, angehängt werden. Wenn der `stdoutLogFile`-Pfad mit `stdout` endet, hat ein Protokoll für eine App mit der PID 1934, erstellt am 2.5.2018 um 19:42:32, den Dateinamen `stdout_20180205194132_1934.log`.

Wenn `stdoutLogEnabled` „false“ ist, werden Fehler beim App-Start erfasst und in das Ereignisprotokoll mit bis zu 30 KB ausgegeben. Nach dem Start werden alle zusätzlichen Protokolle verworfen.

Das folgende Beispielelement `aspNetCore` konfiguriert die stdout-Protokollierung im relativen `.\log\`-Pfad. Vergewissern Sie sich, dass die Identität des AppPool-Benutzers über die Berechtigung zum Schreiben in den angegebenen Pfad verfügt.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Beim Veröffentlichen einer App für die Azure App Service-Bereitstellung legt das Web SDK den Wert `stdoutLogFile` auf `\\?\%home%\LogFiles\stdout` fest. Die Umgebungsvariable `%home` ist für Apps vordefiniert, die von Azure App Service gehostet werden.

Informationen zum Erstellen von Protokollierungsfilterregeln finden Sie in den Abschnitten [Konfiguration](xref:fundamentals/logging/index#log-filtering) und [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) der Dokumentation zur ASP.NET Core-Protokollierung.

Weitere Informationen zu Pfadformaten finden Sie unter [Formate von Dateipfaden unter Windows-Systemen](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Erweiterte Diagnoseprotokolle

Das ASP.NET Core-Modul kann so konfiguriert werden, dass es erweiterte Diagnoseprotokolle bereitstellt. Fügen Sie das Element `<handlerSettings>` zum Element `<aspNetCore>` in `web.config` hinzu. Wenn `debugLevel` auf `TRACE` festgelegt wird, werden genauere Diagnoseinformationen zur Verfügung gestellt:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Ordner, die im Pfad angegeben werden (`logs` im vorherigen Beispiel), werden vom Modul erstellt, wenn die Protokolldatei erstellt wird. Der App-Pool muss über Schreibzugriff auf den Speicherort verfügen, in den die Protokolle geschrieben werden (verwenden Sie `IIS AppPool\{APP POOL NAME}`, um die Schreibberechtigung bereitzustellen, wenn der Platzhalter `{APP POOL NAME}` der App-Poolname ist).

`debugLevel`-Werte können sowohl die Ebene als auch den Speicherort enthalten.

Ebenen (von der geringsten zur höchsten Genauigkeit):

* ERROR
* WARNING
* INFO
* TRACE

Speicherorte (mehrere Speicherorte sind zulässig):

* CONSOLE
* EVENTLOG
* DATEI

Die Handlereinstellungen können auch über Umgebungsvariablen angegeben werden:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Dies ist der Pfad zur Debugprotokolldatei. (Standard: `aspnetcore-debug.log`)
* `ASPNETCORE_MODULE_DEBUG`: Dies ist die Einstellung der Debugebene.

> [!WARNING]
> Lassen Sie die Debugprotokollierung **nicht** länger als erforderlich für die Bereitstellung aktiviert, wenn Sie einen Fehler beheben. Die Größe des Protokolls ist nicht beschränkt. Wenn die Debugprotokollierung aktiviert bleibt, kann der verfügbare Speicherplatz aufgebraucht werden, und der Server- oder App-Dienst können abstürzen.

Unter [Konfiguration mit `web.config`](xref:host-and-deploy/iis/web-config#configuration-with-webconfig) finden Sie ein Beispiel des Elements `aspNetCore` in der Datei `web.config`.
