---
title: Beschränken der Lebensdauer geschützter Nutzlasten in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die Lebensdauer einer geschützten Nutzlast mithilfe der ASP.net Core Datenschutz-APIs begrenzen.
ms.author: riande
ms.date: 10/14/2016
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
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 74417d076399066a49271c27ff128d9de6c10f94
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060793"
---
# <a name="limit-the-lifetime-of-protected-payloads-in-aspnet-core"></a>Beschränken der Lebensdauer geschützter Nutzlasten in ASP.net Core

Es gibt Szenarien, in denen der Anwendungsentwickler eine geschützte Nutzlast erstellen möchte, die nach einer festgelegten Zeitspanne abläuft. Beispielsweise kann die geschützte Nutzlast ein Token zum Zurücksetzen von Kenn Wörtern darstellen, das nur eine Stunde gültig sein sollte. Es ist für den Entwickler sicherlich möglich, ein eigenes Nutz Last Format zu erstellen, das ein eingebettetes Ablaufdatum enthält, und fortgeschrittene Entwickler möchten dies möglicherweise trotzdem tun, aber für die Mehrzahl der Entwickler, die diese Abläufe verwalten, kann es mühsam sein.

Das Paket [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) enthält hilfsprogrammapis zum Erstellen von Nutzlasten, die nach einem festgelegten Zeitraum automatisch ablaufen, um dies für unsere Entwickler Zielgruppe zu vereinfachen. Diese APIs hängen vom Typ ab `ITimeLimitedDataProtector` .

## <a name="api-usage"></a>API-Verwendung

Die `ITimeLimitedDataProtector` -Schnittstelle ist die Kernschnittstelle zum Schutz und zum Schutz von zeitlich begrenzten und selbst abgelaufenen Nutzlasten. Zum Erstellen einer Instanz von `ITimeLimitedDataProtector` benötigen Sie zunächst eine Instanz einer regulären [idataprotector](xref:security/data-protection/consumer-apis/overview) , die mit einem bestimmten Zweck erstellt wurde. Sobald die `IDataProtector` Instanz verfügbar ist, können `IDataProtector.ToTimeLimitedDataProtector` Sie die Erweiterungsmethode abrufen, um eine Schutzvorrichtung mit den integrierten Ablauf Funktionen zu erhalten.

`ITimeLimitedDataProtector` macht die folgenden API-Oberflächen-und Erweiterungs Methoden verfügbar:

* Erstellungs Schutzvorrichtung (Zeichenfolge): itimelimiteddataprotector: Diese API ähnelt der vorhandenen `IDataProtectionProvider.CreateProtector` in, die verwendet werden kann, um [Zweck Ketten](xref:security/data-protection/consumer-apis/purpose-strings) aus einer Schutzvorrichtung mit eingeschränkter Stamm Zeit zu erstellen.

* Protect (Byte [] Klartext, DateTimeOffset-Ablauf): Byte []

* Protect (Byte [] Klartext, TimeSpan-Lebensdauer): Byte []

* Protect (Byte [] Klartext): Byte []

* Protect (String Plaintext, DateTimeOffset-Ablauf): Zeichenfolge

* Protect (Zeichenfolge Plaintext, TimeSpan Lifetime): String

* Protect (Zeichenfolge Klartext): Zeichenfolge

Zusätzlich zu den Kern `Protect` Methoden, die nur den nur-Text verwenden, gibt es neue über Ladungen, die das Angeben des Ablaufdatums der Nutzlast ermöglichen. Das Ablaufdatum kann als absolutes Datum (über einen `DateTimeOffset` ) oder als relative Zeit (von der aktuellen Systemzeit über einen) angegeben werden `TimeSpan` . Wenn eine Überladung, die keine Ablaufzeit annimmt, aufgerufen wird, wird davon ausgegangen, dass die Nutzlast nie abläuft.

* Schutz aufheben (Byte [] ProtectedData, Ausgabe DateTimeOffset): Byte []

* Schutz aufheben (Byte [] ProtectedData): Byte []

* Schutz aufheben (Zeichenfolge ProtectedData, out DateTimeOffset-Ablauf): Zeichenfolge

* Schutz aufheben (Zeichenfolge ProtectedData): Zeichenfolge

Die- `Unprotect` Methoden geben die ursprünglichen ungeschützten Daten zurück. Wenn die Nutzlast noch nicht abgelaufen ist, wird der absolute Ablauf zusammen mit den ursprünglichen ungeschützten Daten als optionaler out-Parameter zurückgegeben. Wenn die Nutzlast abgelaufen ist, lösen alle über Ladungen der Methode zum Aufheben des Schutzes CryptographicException aus.

>[!WARNING]
> Es wird nicht empfohlen, diese APIs zu verwenden, um Nutzlasten zu schützen, für die eine langfristige oder unbegrenzte Persistenz erforderlich ist. "Kann ich mir leisten, dass die geschützten Nutzlasten nach einem Monat dauerhaft nicht wieder hergestellt werden können?" kann als gute Faustregel dienen. Wenn die Antwort nein lautet, sollten Entwickler alternative APIs in Erwägung gezogen.

Im folgenden Beispiel werden die [nicht-di-Codepfade](xref:security/data-protection/configuration/non-di-scenarios) zum Instanziieren des Datenschutzsystems verwendet. Um dieses Beispiel auszuführen, stellen Sie sicher, dass Sie zunächst einen Verweis auf das Paket Microsoft. aspnetcore. dataprotection. Extensions hinzugefügt haben.

[!code-csharp[](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]
