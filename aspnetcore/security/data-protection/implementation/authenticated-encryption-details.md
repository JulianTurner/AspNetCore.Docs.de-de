---
title: Details zu authentifizierten Verschlüsselung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails ASP.net Core authentifizierte Verschlüsselung für den Datenschutz.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 7978725534cdd3a5b425851f61b1c7ae3ada88df
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051836"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a><span data-ttu-id="e0812-103">Details zu authentifizierten Verschlüsselung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e0812-103">Authenticated encryption details in ASP.NET Core</span></span>

<a name="data-protection-implementation-authenticated-encryption-details"></a>

<span data-ttu-id="e0812-104">Aufrufe von idataprotector. Protect sind authentifizierte Verschlüsselungs Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="e0812-104">Calls to IDataProtector.Protect are authenticated encryption operations.</span></span> <span data-ttu-id="e0812-105">Die Protect-Methode bietet sowohl Vertraulichkeit als auch Authentizität und ist an die Zweck Kette gebunden, die zum Ableiten dieser speziellen idataprotector-Instanz von Ihrem idataprotection Provider-Stamm verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="e0812-105">The Protect method offers both confidentiality and authenticity, and it's tied to the purpose chain that was used to derive this particular IDataProtector instance from its root IDataProtectionProvider.</span></span>

<span data-ttu-id="e0812-106">Idataprotector. Protect nimmt einen Byte []-Klartext-Parameter an und erzeugt eine durch Byte [] geschützte Nutzlast, deren Format unten beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="e0812-106">IDataProtector.Protect takes a byte[] plaintext parameter and produces a byte[] protected payload, whose format is described below.</span></span> <span data-ttu-id="e0812-107">(Es gibt auch eine Erweiterungs Methoden Überladung, die einen String-Klartext-Parameter annimmt und eine durch eine Zeichenfolge geschützte Nutzlast zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="e0812-107">(There's also an extension method overload which takes a string plaintext parameter and returns a string protected payload.</span></span> <span data-ttu-id="e0812-108">Wenn diese API verwendet wird, verfügt das geschützte Nutz Last Format weiterhin über die folgende Struktur, aber es wird [base64url-codiert](https://tools.ietf.org/html/rfc4648#section-5).)</span><span class="sxs-lookup"><span data-stu-id="e0812-108">If this API is used the protected payload format will still have the below structure, but it will be [base64url-encoded](https://tools.ietf.org/html/rfc4648#section-5).)</span></span>

## <a name="protected-payload-format"></a><span data-ttu-id="e0812-109">Geschütztes Nutz Last Format</span><span class="sxs-lookup"><span data-stu-id="e0812-109">Protected payload format</span></span>

<span data-ttu-id="e0812-110">Das geschützte Nutz Last Format besteht aus drei Hauptkomponenten:</span><span class="sxs-lookup"><span data-stu-id="e0812-110">The protected payload format consists of three primary components:</span></span>

* <span data-ttu-id="e0812-111">Ein 32-Bit-Magic-Header, der die Version des Datenschutzsystems identifiziert.</span><span class="sxs-lookup"><span data-stu-id="e0812-111">A 32-bit magic header that identifies the version of the data protection system.</span></span>

* <span data-ttu-id="e0812-112">Eine 128-Bit-Schlüssel-ID, die den Schlüssel identifiziert, mit dem diese bestimmte Nutzlast geschützt wird.</span><span class="sxs-lookup"><span data-stu-id="e0812-112">A 128-bit key id that identifies the key used to protect this particular payload.</span></span>

* <span data-ttu-id="e0812-113">Der Rest der geschützten Nutzlast ist [spezifisch für die Verschlüsselung, die von diesem Schlüssel gekapselt](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)wird.</span><span class="sxs-lookup"><span data-stu-id="e0812-113">The remainder of the protected payload is [specific to the encryptor encapsulated by this key](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="e0812-114">Im folgenden Beispiel stellt der Schlüssel eine AES-256-CBC + HMACSHA256-Verschlüsselung dar, und die Nutzlast wird wie folgt weiter unterteilt:</span><span class="sxs-lookup"><span data-stu-id="e0812-114">In the example below, the key represents an AES-256-CBC + HMACSHA256 encryptor, and the payload is further subdivided as follows:</span></span>
  * <span data-ttu-id="e0812-115">Ein 128-Bit-schlüsselmodifizierer.</span><span class="sxs-lookup"><span data-stu-id="e0812-115">A 128-bit key modifier.</span></span>
  * <span data-ttu-id="e0812-116">Ein 128-Bit-Initialisierungs Vektor.</span><span class="sxs-lookup"><span data-stu-id="e0812-116">A 128-bit initialization vector.</span></span>
  * <span data-ttu-id="e0812-117">48 Bytes der AES-256-CBC-Ausgabe.</span><span class="sxs-lookup"><span data-stu-id="e0812-117">48 bytes of AES-256-CBC output.</span></span>
  * <span data-ttu-id="e0812-118">Ein HMACSHA256-authentifizierungstag.</span><span class="sxs-lookup"><span data-stu-id="e0812-118">An HMACSHA256 authentication tag.</span></span>

<span data-ttu-id="e0812-119">Eine Beispiel geschützte Nutzlast wird unten veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="e0812-119">A sample protected payload is illustrated below.</span></span>

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

<span data-ttu-id="e0812-120">Aus dem Nutz Last Format oberhalb der ersten 32 Bits, oder 4 Bytes sind der Magic-Header, der die Version identifiziert (09 F0-F0).</span><span class="sxs-lookup"><span data-stu-id="e0812-120">From the payload format above the first 32 bits, or 4 bytes are the magic header identifying the version (09 F0 C9 F0)</span></span>

<span data-ttu-id="e0812-121">Die nächsten 128 Bits oder 16 Bytes sind der Schlüssel Bezeichner (80 9c 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57).</span><span class="sxs-lookup"><span data-stu-id="e0812-121">The next 128 bits, or 16 bytes is the key identifier (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)</span></span>

<span data-ttu-id="e0812-122">Der Rest enthält die Nutzlast und ist spezifisch für das verwendete Format.</span><span class="sxs-lookup"><span data-stu-id="e0812-122">The remainder contains the payload and is specific to the format used.</span></span>

> [!WARNING]
> <span data-ttu-id="e0812-123">Alle Nutzlasten, die mit einem bestimmten Schlüssel geschützt werden, beginnen mit dem gleichen 20-Byte-Header (Magic Value, Key ID).</span><span class="sxs-lookup"><span data-stu-id="e0812-123">All payloads protected to a given key will begin with the same 20-byte (magic value, key id) header.</span></span> <span data-ttu-id="e0812-124">Administratoren können diese Tatsache für Diagnosezwecke verwenden, um zu identifizieren, wann eine Nutzlast generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e0812-124">Administrators can use this fact for diagnostic purposes to approximate when a payload was generated.</span></span> <span data-ttu-id="e0812-125">Beispielsweise entspricht die obige Nutzlast dem Schlüssel {0c819c80-6619-4019-9536-53f8aaffee57}.</span><span class="sxs-lookup"><span data-stu-id="e0812-125">For example, the payload above corresponds to key {0c819c80-6619-4019-9536-53f8aaffee57}.</span></span> <span data-ttu-id="e0812-126">Wenn Sie nach dem Überprüfen des schlüsselrepository feststellen, dass das Aktivierungsdatum dieses bestimmten Schlüssels 2015-01-01 und das Ablaufdatum 2015-03-01 war, sollten Sie davon ausgehen, dass die Nutzlast (wenn nicht manipuliert) innerhalb dieses Fensters generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="e0812-126">If after checking the key repository you find that this specific key's activation date was 2015-01-01 and its expiration date was 2015-03-01, then it's reasonable to assume that the payload (if not tampered with) was generated within that window, give or take a small fudge factor on either side.</span></span>
