---
title: Details zu authentifizierten Verschlüsselung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails ASP.net Core authentifizierte Verschlüsselung für den Datenschutz.
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
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051836"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Details zu authentifizierten Verschlüsselung in ASP.net Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Aufrufe von idataprotector. Protect sind authentifizierte Verschlüsselungs Vorgänge. Die Protect-Methode bietet sowohl Vertraulichkeit als auch Authentizität und ist an die Zweck Kette gebunden, die zum Ableiten dieser speziellen idataprotector-Instanz von Ihrem idataprotection Provider-Stamm verwendet wurde.

Idataprotector. Protect nimmt einen Byte []-Klartext-Parameter an und erzeugt eine durch Byte [] geschützte Nutzlast, deren Format unten beschrieben wird. (Es gibt auch eine Erweiterungs Methoden Überladung, die einen String-Klartext-Parameter annimmt und eine durch eine Zeichenfolge geschützte Nutzlast zurückgibt. Wenn diese API verwendet wird, verfügt das geschützte Nutz Last Format weiterhin über die folgende Struktur, aber es wird [base64url-codiert](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Geschütztes Nutz Last Format

Das geschützte Nutz Last Format besteht aus drei Hauptkomponenten:

* Ein 32-Bit-Magic-Header, der die Version des Datenschutzsystems identifiziert.

* Eine 128-Bit-Schlüssel-ID, die den Schlüssel identifiziert, mit dem diese bestimmte Nutzlast geschützt wird.

* Der Rest der geschützten Nutzlast ist [spezifisch für die Verschlüsselung, die von diesem Schlüssel gekapselt](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)wird. Im folgenden Beispiel stellt der Schlüssel eine AES-256-CBC + HMACSHA256-Verschlüsselung dar, und die Nutzlast wird wie folgt weiter unterteilt:
  * Ein 128-Bit-schlüsselmodifizierer.
  * Ein 128-Bit-Initialisierungs Vektor.
  * 48 Bytes der AES-256-CBC-Ausgabe.
  * Ein HMACSHA256-authentifizierungstag.

Eine Beispiel geschützte Nutzlast wird unten veranschaulicht.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Aus dem Nutz Last Format oberhalb der ersten 32 Bits, oder 4 Bytes sind der Magic-Header, der die Version identifiziert (09 F0-F0).

Die nächsten 128 Bits oder 16 Bytes sind der Schlüssel Bezeichner (80 9c 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57).

Der Rest enthält die Nutzlast und ist spezifisch für das verwendete Format.

> [!WARNING]
> Alle Nutzlasten, die mit einem bestimmten Schlüssel geschützt werden, beginnen mit dem gleichen 20-Byte-Header (Magic Value, Key ID). Administratoren können diese Tatsache für Diagnosezwecke verwenden, um zu identifizieren, wann eine Nutzlast generiert wurde. Beispielsweise entspricht die obige Nutzlast dem Schlüssel {0c819c80-6619-4019-9536-53f8aaffee57}. Wenn Sie nach dem Überprüfen des schlüsselrepository feststellen, dass das Aktivierungsdatum dieses bestimmten Schlüssels 2015-01-01 und das Ablaufdatum 2015-03-01 war, sollten Sie davon ausgehen, dass die Nutzlast (wenn nicht manipuliert) innerhalb dieses Fensters generiert wurde.
