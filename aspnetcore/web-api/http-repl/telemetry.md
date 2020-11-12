---
title: Httprepl-Telemetrie
author: scottaddie
description: Erfahren Sie mehr über die Telemetriedaten, die von httprepl gesammelt werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.date: 11/11/2020
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
uid: web-api/http-repl/telemetry
ms.openlocfilehash: 5ff22753f566c494e51dae67c8c4f6371211be78
ms.sourcegitcommit: 202144092067ea81be1dbb229329518d781dbdfb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550607"
---
# <a name="httprepl-telemetry"></a>Httprepl-Telemetrie

[Httprepl](xref:web-api/http-repl) enthält eine telemetriefunktion, mit der Verwendungs Daten erfasst werden. Es ist wichtig, dass das httprepl-Team versteht, wie das Tool verwendet wird, um es zu verbessern.

## <a name="how-to-opt-out"></a>Deaktivierung der Telemetriefunktion

Die httprepl-telemetriefunktion ist standardmäßig aktiviert. Sie können das Telemetriefeature deaktivieren, indem Sie die Umgebungsvariable `DOTNET_HTTPREPL_TELEMETRY_OPTOUT` auf `1` oder `true` festlegen.

## <a name="disclosure"></a>Offenlegung

Die httprepl zeigt Text an, der dem folgenden ähnelt, wenn Sie das Tool zum ersten Mal ausführen. Der Text kann abhängig von der Version des Tools, das Sie ausführen, leicht abweichen. Über diese erste Ausführung benachrichtigt Microsoft Sie über die Datensammlung.

```console
Telemetry
---------
The .NET tools collect usage data in order to help us improve your experience. It is collected by Microsoft and shared with the community. You can opt-out of telemetry by setting the DOTNET_HTTPREPL_TELEMETRY_OPTOUT environment variable to '1' or 'true' using your favorite shell.
```

Legen Sie die `DOTNET_HTTPREPL_SKIP_FIRST_TIME_EXPERIENCE` Umgebungsvariable auf oder fest, um den Text der "ersten Testlauf" zu unterdrücken `1` `true` .

## <a name="data-points"></a>Datenpunkte

Die telemetriefunktion ist nicht:

* Erfassen Sie persönliche Daten, z. b. Benutzernamen, e-Mail-Adressen oder URLs.
* Überprüfen Sie die HTTP-Anforderungen oder-Antworten.

Die Daten werden sicher an Microsoft-Server gesendet und mit eingeschränktem Zugriff gespeichert.

Der Schutz Ihrer Privatsphäre ist uns wichtig. Wenn Sie vermuten, dass die telemetriefunktion sensible Daten sammelt oder die Daten unsicher oder unpassend verarbeitet werden, führen Sie eine der folgenden Aktionen aus:

* Melden Sie ein Problem im [dotnet/httprepl-](https://github.com/dotnet/httprepl/issues) Repository.
* Senden Sie eine e-Mail zur [dotnet@microsoft.com](mailto:dotnet@microsoft.com) Untersuchung an.

Die telemetriefunktion sammelt die folgenden Daten.

| .NET SDK-Versionen | Daten |
|--------------|------|
| >= 5,0        | Den Zeitstempel des Aufrufs |
| >= 5,0        | IP-Adresse für das drei-Oktett, mit der der geografische Standort bestimmt wird. |
| >= 5,0        | Betriebssystem und Version |
| >= 5,0        | Die Runtime-ID (RID), auf der das Tool ausgeführt wird. |
| >= 5,0        | Gibt an, ob das Tool in einem Container ausgeführt wird. |
| >= 5,0        | Hash Media Access Control (Mac)-Adresse: ein kryptografischer (SHA256) Hashwert und eine eindeutige ID für einen Computer. |
| >= 5,0        | Kernelversion |
| >= 5,0        | Httprepl-Version. |
| >= 5,0        | Gibt an, ob das Tool mit-,-oder-Argumenten gestartet wurde `help` `run` `connect` . Tatsächliche Argument Werte werden nicht erfasst. |
| >= 5,0        | Der Befehl wurde aufgerufen (z `get` . b.), und ob er erfolgreich war. |
| >= 5,0        | Für den- `connect` Befehl, ob die- `root` ,-oder- `base` `openapi` Argumente angegeben wurden. Tatsächliche Argument Werte werden nicht erfasst. |
| >= 5,0        | Für den `pref` Befehl, ob `get` oder `set` ausgestellt wurde und auf welche Einstellung zugegriffen wurde. Wenn es sich nicht um eine bekannte bevorzugte Einstellung handelt, wird der Name als Hashwert bezeichnet. Der Wert wird nicht erfasst. |
| >= 5,0        | Für den `set header` Befehl wird der Header Name festgelegt. Wenn es sich nicht um einen bekannten Header handelt, wird der Name mit einem Hashwert versehen. Der Wert wird nicht erfasst. |
| >= 5,0        | Für den `connect` -Befehl, ob ein Sonderfall für `dotnet new webapi` verwendet wurde und, ob er per Preference umgangen wurde. |
| >= 5,0        | Für alle HTTP-Befehle (z. b. Get, Post, Put), unabhängig davon, ob jede der Optionen angegeben wurde. Die Werte der Optionen werden nicht erfasst. |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [.NET Core SDK-Telemetrie](/dotnet/core/tools/telemetry)
* [.Net Core-CLI von Telemetriedaten](https://dotnet.microsoft.com/platform/telemetry)
