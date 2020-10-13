---
title: App-Offlinedatei (app_offline.htm)
author: rick-anderson
description: Hier erhalten Sie Informationen dazu, wie die App-Offlinedatei (`app_offline.htm`) mit dem ASP.NET Core-Modul verwendet werden kann.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 95dfadd084af5909fee754308ad5d65f54d4875d
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755164"
---
# <a name="app-offline-file-app_offlinehtm"></a>App-Offlinedatei (`app_offline.htm`)

Die App-Offlinedatei (`app_offline.htm`) wird vom ASP.NET Core-Modul verwendet, um eine App herunterzufahren.

Wenn eine Datei mit dem Namen `app_offline.htm` im Stammverzeichnis einer App erkannt wird, versucht das ASP.NET Core-Modul, die App ordnungsgemäß zu beenden und die Verarbeitung eingehender Anforderungen zu stoppen. Wenn die App nach der Anzahl von Sekunden, die in `shutdownTimeLimit` definiert wurde, noch ausgeführt wird, beendet das ASP.NET Core-Modul den laufenden Prozess.

Wenn die Datei `app_offline.htm` vorhanden ist, reagiert das ASP.NET Core-Modul auf Anforderungen, indem es den Inhalt der `app_offline.htm`-Datei zurücksendet. Wenn die Datei `app_offline.htm` entfernt wurde, wird die App von der nächsten Anforderung gestartet.

Beim Verwenden des Out-of-Process-Hostingmodells wird die App möglicherweise nicht sofort heruntergefahren, wenn eine offene Verbindung besteht. Beispielsweise kann eine WebSocket-Verbindung eine Verzögerung beim Herunterfahren der App bewirken.

## <a name="locked-deployment-files"></a>Gesperrte Bereitstellungsdateien

Die Dateien im Bereitstellungsordner werden gesperrt, wenn die App ausgeführt wird. Gesperrte Dateien können während der Bereitstellung nicht überschrieben werden.

`app_offline.htm` ist die primäre Methode, mit der gesperrte Dateien entsperrt werden. `app_offline.htm` wird von Web Deploy verwendet, um die App ordnungsgemäß anzuhalten und zu starten.

`app_offline.htm` kann manuell verwendet werden, um die App zu starten und anzuhalten (PowerShell 5 oder höher erforderlich):

```powershell
$pathToApp = '{PATH TO APP}'

New-Item -Path $pathToApp app_offline.htm

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp app_offline.htm
```

Für das vorangehende PowerShell-Skript gilt Folgendes:

* Der Platzhalter `{PATH TO APP}` ist der Pfad zur App.
* Der `New-Item`-Befehl hält den App-Pool an.
* Der `Remove-Item`-Befehl startet den App-Pool.
* Befehle zwischen dem `New-Item`-Befehl und dem `Remove-Item`-Befehl werden vom Entwickler bereitgestellt, um die App bereitzustellen.

Dateien können auch entsperrt werden, indem der App-Pool vom IIS-Manager auf dem Server manuell angehalten wird. Verwenden Sie die `app_offine.htm`-Datei nicht, wenn der IIS-Manager verwendet wird, um den App-Pool anzuhalten und neu zu starten.
