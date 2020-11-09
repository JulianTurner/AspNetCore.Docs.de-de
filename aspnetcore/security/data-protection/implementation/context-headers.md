---
title: Kontext Header in ASP.net Core
author: rick-anderson
description: Weitere Informationen zur Implementierung von ASP.net Core Datenschutz-Kontext Headern.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051719"
---
# <a name="context-headers-in-aspnet-core"></a><span data-ttu-id="5dded-103">Kontext Header in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="5dded-103">Context headers in ASP.NET Core</span></span>

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a><span data-ttu-id="5dded-104">Hintergrund und Theorie</span><span class="sxs-lookup"><span data-stu-id="5dded-104">Background and theory</span></span>

<span data-ttu-id="5dded-105">Im Datenschutzsystem bedeutet ein "Schlüssel" ein Objekt, das authentifizierte Verschlüsselungsdienste bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="5dded-105">In the data protection system, a "key" means an object that can provide authenticated encryption services.</span></span> <span data-ttu-id="5dded-106">Jeder Schlüssel wird durch eine eindeutige ID (eine GUID) identifiziert, und er enthält algorithmische Informationen und Entropie Material.</span><span class="sxs-lookup"><span data-stu-id="5dded-106">Each key is identified by a unique id (a GUID), and it carries with it algorithmic information and entropic material.</span></span> <span data-ttu-id="5dded-107">Es ist beabsichtigt, dass jeder Schlüssel eine eindeutige Entropie trägt, aber das System kann dies nicht erzwingen. Außerdem müssen Entwickler, die den Schlüsselbund manuell ändern können, die algorithmischen Informationen eines vorhandenen Schlüssels im Schlüsselbund ändern.</span><span class="sxs-lookup"><span data-stu-id="5dded-107">It's intended that each key carry unique entropy, but the system cannot enforce that, and we also need to account for developers who might change the key ring manually by modifying the algorithmic information of an existing key in the key ring.</span></span> <span data-ttu-id="5dded-108">Um unsere Sicherheitsanforderungen in diesen Fällen zu erfüllen, verfügt das Datenschutzsystem über ein Konzept der [kryptografischen Agilität](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), das eine sichere Verwendung eines einzelnen Entropie Werts in mehreren Kryptografiealgorithmen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="5dded-108">To achieve our security requirements given these cases the data protection system has a concept of [cryptographic agility](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), which allows securely using a single entropic value across multiple cryptographic algorithms.</span></span>

<span data-ttu-id="5dded-109">Bei den meisten Systemen, die kryptografische Agilität unterstützen, werden in der Nutzlast einige identifizierende Informationen über den Algorithmus eingeschlossen.</span><span class="sxs-lookup"><span data-stu-id="5dded-109">Most systems which support cryptographic agility do so by including some identifying information about the algorithm inside the payload.</span></span> <span data-ttu-id="5dded-110">Die OID des Algorithmus ist im Allgemeinen ein guter Kandidat dafür.</span><span class="sxs-lookup"><span data-stu-id="5dded-110">The algorithm's OID is generally a good candidate for this.</span></span> <span data-ttu-id="5dded-111">Allerdings besteht ein Problem darin, dass es mehrere Möglichkeiten gibt, denselben Algorithmus anzugeben: "die Klassen AES (CNG) und Managed AES, AesManaged, AesCryptoServiceProvider, aescng und RijndaelManaged (mit bestimmten Parametern) sind tatsächlich identisch, und wir müssten eine Zuordnung all dieser Elemente zur richtigen OID beibehalten.</span><span class="sxs-lookup"><span data-stu-id="5dded-111">However, one problem that we ran into is that there are multiple ways to specify the same algorithm: "AES" (CNG) and the managed Aes, AesManaged, AesCryptoServiceProvider, AesCng, and RijndaelManaged (given specific parameters) classes are all actually the same thing, and we'd need to maintain a mapping of all of these to the correct OID.</span></span> <span data-ttu-id="5dded-112">Wenn ein Entwickler einen benutzerdefinierten Algorithmus (oder sogar eine andere Implementierung von AES!) bereitstellen möchte, muss er uns seine OID mitteilen.</span><span class="sxs-lookup"><span data-stu-id="5dded-112">If a developer wanted to provide a custom algorithm (or even another implementation of AES!), they'd have to tell us its OID.</span></span> <span data-ttu-id="5dded-113">Durch diesen zusätzlichen Registrierungs Schritt ist die Systemkonfiguration besonders mühsam.</span><span class="sxs-lookup"><span data-stu-id="5dded-113">This extra registration step makes system configuration particularly painful.</span></span>

<span data-ttu-id="5dded-114">Wir haben uns entschieden, das Problem aus der falschen Richtung zu beheben.</span><span class="sxs-lookup"><span data-stu-id="5dded-114">Stepping back, we decided that we were approaching the problem from the wrong direction.</span></span> <span data-ttu-id="5dded-115">Eine OID gibt Aufschluss darüber, was der Algorithmus ist, aber das ist nicht wirklich wichtig.</span><span class="sxs-lookup"><span data-stu-id="5dded-115">An OID tells you what the algorithm is, but we don't actually care about this.</span></span> <span data-ttu-id="5dded-116">Wenn wir einen einzelnen Entropie Wert sicher in zwei verschiedenen Algorithmen verwenden müssen, ist es nicht erforderlich, dass wir wissen, was die Algorithmen tatsächlich sind.</span><span class="sxs-lookup"><span data-stu-id="5dded-116">If we need to use a single entropic value securely in two different algorithms, it's not necessary for us to know what the algorithms actually are.</span></span> <span data-ttu-id="5dded-117">Wir kümmern uns darum, wie Sie sich Verhalten.</span><span class="sxs-lookup"><span data-stu-id="5dded-117">What we actually care about is how they behave.</span></span> <span data-ttu-id="5dded-118">Jeder angemessene symmetrische Blockchiffre Algorithmus ist auch eine starke Pseudo Zufalls-permutations (PRP): Korrigieren der Eingaben (Schlüssel, Verkettungs Modus, IV, Klartext), und die Chiffre Textausgabe wird mit überwältigender Wahrscheinlichkeit von allen anderen symmetrischen Blockverschlüsselungsalgorithmen unterschieden, wenn dieselben Eingaben verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5dded-118">Any decent symmetric block cipher algorithm is also a strong pseudorandom permutation (PRP): fix the inputs (key, chaining mode, IV, plaintext) and the ciphertext output will with overwhelming probability be distinct from any other symmetric block cipher algorithm given the same inputs.</span></span> <span data-ttu-id="5dded-119">Ebenso ist jede beliebige Schlüssel gebundene Hash Funktion auch eine starke Pseudo Zufallsfunktion (PRF), und bei einem festen Eingabe Satz unterscheidet sich Ihre Ausgabe überwiegend von jeder anderen Schlüssel gebundenen Hash Funktion.</span><span class="sxs-lookup"><span data-stu-id="5dded-119">Similarly, any decent keyed hash function is also a strong pseudorandom function (PRF), and given a fixed input set its output will overwhelmingly be distinct from any other keyed hash function.</span></span>

<span data-ttu-id="5dded-120">Wir verwenden dieses Konzept mit starken prps und prfs, um einen Kontext Header zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5dded-120">We use this concept of strong PRPs and PRFs to build up a context header.</span></span> <span data-ttu-id="5dded-121">Diese Kontext Kopfzeile fungiert im Wesentlichen als stabiler Fingerabdruck für die Algorithmen, die für einen bestimmten Vorgang verwendet werden, und bietet die kryptografische Agilität, die vom Datenschutzsystem benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="5dded-121">This context header essentially acts as a stable thumbprint over the algorithms in use for any given operation, and it provides the cryptographic agility needed by the data protection system.</span></span> <span data-ttu-id="5dded-122">Dieser Header ist reproduzierbar und wird später als Teil der [Unterschlüssel Ableitung](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)verwendet.</span><span class="sxs-lookup"><span data-stu-id="5dded-122">This header is reproducible and is used later as part of the [subkey derivation process](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation).</span></span> <span data-ttu-id="5dded-123">Es gibt zwei verschiedene Möglichkeiten, den Kontext Header abhängig von den Betriebsmodi der zugrunde liegenden Algorithmen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5dded-123">There are two different ways to build the context header depending on the modes of operation of the underlying algorithms.</span></span>

## <a name="cbc-mode-encryption--hmac-authentication"></a><span data-ttu-id="5dded-124">CBC-Modus-Verschlüsselung + HMAC-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5dded-124">CBC-mode encryption + HMAC authentication</span></span>

<a name="data-protection-implementation-context-headers-cbc-components"></a>

<span data-ttu-id="5dded-125">Der Kontext Header besteht aus den folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="5dded-125">The context header consists of the following components:</span></span>

* <span data-ttu-id="5dded-126">[16 Bits] Der Wert 00 00, bei dem es sich um einen Marker handelt, der "CBC-Verschlüsselung + HMAC-Authentifizierung" bedeutet.</span><span class="sxs-lookup"><span data-stu-id="5dded-126">[16 bits] The value 00 00, which is a marker meaning "CBC encryption + HMAC authentication".</span></span>

* <span data-ttu-id="5dded-127">[32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des Algorithmus für symmetrische Blockchiffre.</span><span class="sxs-lookup"><span data-stu-id="5dded-127">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="5dded-128">[32 Bits] Die Blockgröße (in Byte, Big-Endian) des Algorithmus für symmetrische Blockchiffre.</span><span class="sxs-lookup"><span data-stu-id="5dded-128">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="5dded-129">[32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des HMAC-Algorithmus.</span><span class="sxs-lookup"><span data-stu-id="5dded-129">[32 bits] The key length (in bytes, big-endian) of the HMAC algorithm.</span></span> <span data-ttu-id="5dded-130">(Derzeit stimmt die Schlüsselgröße immer mit der Digest-Größe überein.)</span><span class="sxs-lookup"><span data-stu-id="5dded-130">(Currently the key size always matches the digest size.)</span></span>

* <span data-ttu-id="5dded-131">[32 Bits] Die Digest-Größe (in Byte, Big-Endian) des HMAC-Algorithmus.</span><span class="sxs-lookup"><span data-stu-id="5dded-131">[32 bits] The digest size (in bytes, big-endian) of the HMAC algorithm.</span></span>

* <span data-ttu-id="5dded-132">`EncCBC(K_E, IV, "")`, bei der es sich um die Ausgabe des symmetrischen Block Verschlüsselungsalgorithmus handelt, bei der eine leere Zeichen folgen Eingabe angegeben ist und wobei der Wert von "IV" ein Vektor ist.</span><span class="sxs-lookup"><span data-stu-id="5dded-132">`EncCBC(K_E, IV, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where IV is an all-zero vector.</span></span> <span data-ttu-id="5dded-133">Die Erstellung von `K_E` wird nachfolgend beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5dded-133">The construction of `K_E` is described below.</span></span>

* <span data-ttu-id="5dded-134">`MAC(K_H, "")`, bei dem es sich um die Ausgabe des HMAC-Algorithmus handelt, wenn eine leere Zeichenfolge eingegeben wird.</span><span class="sxs-lookup"><span data-stu-id="5dded-134">`MAC(K_H, "")`, which is the output of the HMAC algorithm given an empty string input.</span></span> <span data-ttu-id="5dded-135">Die Erstellung von `K_H` wird nachfolgend beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5dded-135">The construction of `K_H` is described below.</span></span>

<span data-ttu-id="5dded-136">Im Idealfall könnten wir alle Vektoren mit allen Nullen für `K_E` und übergeben `K_H` .</span><span class="sxs-lookup"><span data-stu-id="5dded-136">Ideally, we could pass all-zero vectors for `K_E` and `K_H`.</span></span> <span data-ttu-id="5dded-137">Wir möchten jedoch vermeiden, dass die Situation, in der der zugrunde liegende Algorithmus vor dem Ausführen von Vorgängen (insbesondere der und 3DES) das vorhanden sein von schwachen Schlüsseln prüft, die Verwendung eines einfachen oder wiederholbaren Musters, wie z. b. eines Vektor mit allen Nullen, verhindert.</span><span class="sxs-lookup"><span data-stu-id="5dded-137">However, we want to avoid the situation where the underlying algorithm checks for the existence of weak keys before performing any operations (notably DES and 3DES), which precludes using a simple or repeatable pattern like an all-zero vector.</span></span>

<span data-ttu-id="5dded-138">Stattdessen verwenden wir die NIST SP800-108 KDF im Counter-Modus (siehe [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sec. 5,1) mit einem Schlüssel, einer Bezeichnung und einem Kontext der Länge 0 (null) und HMACSHA512 als zugrunde liegende PRF.</span><span class="sxs-lookup"><span data-stu-id="5dded-138">Instead, we use the NIST SP800-108 KDF in Counter Mode (see [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), Sec. 5.1) with a zero-length key, label, and context and HMACSHA512 as the underlying PRF.</span></span> <span data-ttu-id="5dded-139">Wir leiten `| K_E | + | K_H |` Bytes der Ausgabe ab und zerlegen das Ergebnis dann in `K_E` und `K_H` selbst.</span><span class="sxs-lookup"><span data-stu-id="5dded-139">We derive `| K_E | + | K_H |` bytes of output, then decompose the result into `K_E` and `K_H` themselves.</span></span> <span data-ttu-id="5dded-140">Dies wird mathematisch wie folgt dargestellt.</span><span class="sxs-lookup"><span data-stu-id="5dded-140">Mathematically, this is represented as follows.</span></span>

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a><span data-ttu-id="5dded-141">Beispiel: AES-192-CBC + HMACSHA256</span><span class="sxs-lookup"><span data-stu-id="5dded-141">Example: AES-192-CBC + HMACSHA256</span></span>

<span data-ttu-id="5dded-142">Nehmen Sie als Beispiel den Fall, in dem der symmetrische Blockchiffre Algorithmus AES-192-CBC und der Validierungs Algorithmus HMACSHA256 ist.</span><span class="sxs-lookup"><span data-stu-id="5dded-142">As an example, consider the case where the symmetric block cipher algorithm is AES-192-CBC and the validation algorithm is HMACSHA256.</span></span> <span data-ttu-id="5dded-143">Das System generiert den Kontext Header mithilfe der folgenden Schritte.</span><span class="sxs-lookup"><span data-stu-id="5dded-143">The system would generate the context header using the following steps.</span></span>

<span data-ttu-id="5dded-144">First, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 192 bits` und `| K_H | = 256 bits` pro angegebenen Algorithmen.</span><span class="sxs-lookup"><span data-stu-id="5dded-144">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 256 bits` per the specified algorithms.</span></span> <span data-ttu-id="5dded-145">Dies führt zu `K_E = 5BB6..21DD` und `K_H = A04A..00A9` im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5dded-145">This leads to `K_E = 5BB6..21DD` and `K_H = A04A..00A9` in the example below:</span></span>

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

<span data-ttu-id="5dded-146">Berechnen Sie als nächstes Compute `Enc_CBC (K_E, IV, "")` für AES-192-CBC, `IV = 0*` `K_E` wie oben angegeben.</span><span class="sxs-lookup"><span data-stu-id="5dded-146">Next, compute `Enc_CBC (K_E, IV, "")` for AES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := F474B1872B3B53E4721DE19C0841DB6F`

<span data-ttu-id="5dded-147">Als Nächstes berechnen Sie `MAC(K_H, "")` für HMACSHA256, `K_H` wie oben angegeben.</span><span class="sxs-lookup"><span data-stu-id="5dded-147">Next, compute `MAC(K_H, "")` for HMACSHA256 given `K_H` as above.</span></span>

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

<span data-ttu-id="5dded-148">Dies erzeugt den vollständigen Kontext Header unten:</span><span class="sxs-lookup"><span data-stu-id="5dded-148">This produces the full context header below:</span></span>

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

<span data-ttu-id="5dded-149">Diese Kontext Kopfzeile ist der Fingerabdruck des authentifizierten Verschlüsselungsalgorithmus-Paars (AES-192-CBC-Verschlüsselung + HMACSHA256-Überprüfung).</span><span class="sxs-lookup"><span data-stu-id="5dded-149">This context header is the thumbprint of the authenticated encryption algorithm pair (AES-192-CBC encryption + HMACSHA256 validation).</span></span> <span data-ttu-id="5dded-150">Die Komponenten, wie [oben](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) beschrieben, lauten wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5dded-150">The components, as described [above](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) are:</span></span>

* <span data-ttu-id="5dded-151">der Marker `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="5dded-151">the marker `(00 00)`</span></span>

* <span data-ttu-id="5dded-152">die Länge des Blockchiffre Schlüssels `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="5dded-152">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="5dded-153">Blockgröße für Blockchiffre `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="5dded-153">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="5dded-154">die Länge des HMAC-Schlüssels `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="5dded-154">the HMAC key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="5dded-155">die HMAC-Digest-Größe `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="5dded-155">the HMAC digest size `(00 00 00 20)`</span></span>

* <span data-ttu-id="5dded-156">die Blockchiffre-PRP `(F4 74 - DB 6F)` -Ausgabe und</span><span class="sxs-lookup"><span data-stu-id="5dded-156">the block cipher PRP output `(F4 74 - DB 6F)` and</span></span>

* <span data-ttu-id="5dded-157">die HMAC-PRF-Ausgabe `(D4 79 - end)` .</span><span class="sxs-lookup"><span data-stu-id="5dded-157">the HMAC PRF output `(D4 79 - end)`.</span></span>

> [!NOTE]
> <span data-ttu-id="5dded-158">Der Kontext Header "Verschlüsselung im CBC-Modus + HMAC-Authentifizierung" wird auf dieselbe Weise erstellt, unabhängig davon, ob die Algorithmen Implementierungen von Windows CNG oder von verwalteten SymmetricAlgorithm-und KeyedHashAlgorithm-Typen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="5dded-158">The CBC-mode encryption + HMAC authentication context header is built the same way regardless of whether the algorithms implementations are provided by Windows CNG or by managed SymmetricAlgorithm and KeyedHashAlgorithm types.</span></span> <span data-ttu-id="5dded-159">Dadurch können Anwendungen, die unter verschiedenen Betriebssystemen ausgeführt werden, zuverlässig denselben Kontext Header entwickeln, auch wenn sich die Implementierungen der Algorithmen zwischen den Betriebssystemen unterscheiden.</span><span class="sxs-lookup"><span data-stu-id="5dded-159">This allows applications running on different operating systems to reliably produce the same context header even though the implementations of the algorithms differ between OSes.</span></span> <span data-ttu-id="5dded-160">(In der Praxis muss KeyedHashAlgorithm nicht der richtige HMAC sein.</span><span class="sxs-lookup"><span data-stu-id="5dded-160">(In practice, the KeyedHashAlgorithm doesn't have to be a proper HMAC.</span></span> <span data-ttu-id="5dded-161">Dabei kann es sich um einen beliebigen Hash Algorithmustyp handeln.)</span><span class="sxs-lookup"><span data-stu-id="5dded-161">It can be any keyed hash algorithm type.)</span></span>

### <a name="example-3des-192-cbc--hmacsha1"></a><span data-ttu-id="5dded-162">Beispiel: 3DES-192-CBC + HMACSHA1</span><span class="sxs-lookup"><span data-stu-id="5dded-162">Example: 3DES-192-CBC + HMACSHA1</span></span>

<span data-ttu-id="5dded-163">First, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 192 bits` und `| K_H | = 160 bits` pro angegebenen Algorithmen.</span><span class="sxs-lookup"><span data-stu-id="5dded-163">First, let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 192 bits` and `| K_H | = 160 bits` per the specified algorithms.</span></span> <span data-ttu-id="5dded-164">Dies führt zu `K_E = A219..E2BB` und `K_H = DC4A..B464` im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5dded-164">This leads to `K_E = A219..E2BB` and `K_H = DC4A..B464` in the example below:</span></span>

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

<span data-ttu-id="5dded-165">Als Nächstes berechnen Sie `Enc_CBC (K_E, IV, "")` für 3DES-192-CBC `IV = 0*` und `K_E` wie oben angegeben.</span><span class="sxs-lookup"><span data-stu-id="5dded-165">Next, compute `Enc_CBC (K_E, IV, "")` for 3DES-192-CBC given `IV = 0*` and `K_E` as above.</span></span>

`result := ABB100F81E53E10E`

<span data-ttu-id="5dded-166">Als Nächstes berechnen Sie `MAC(K_H, "")` für HMACSHA1, `K_H` wie oben angegeben.</span><span class="sxs-lookup"><span data-stu-id="5dded-166">Next, compute `MAC(K_H, "")` for HMACSHA1 given `K_H` as above.</span></span>

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

<span data-ttu-id="5dded-167">Dies erzeugt den vollständigen Kontext Header, bei dem es sich um einen Fingerabdruck des authentifizierten Verschlüsselungsalgorithmus-Paars handelt (3DES-192-CBC-Verschlüsselung + HMACSHA1-Validierung), wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="5dded-167">This produces the full context header which is a thumbprint of the authenticated encryption algorithm pair (3DES-192-CBC encryption + HMACSHA1 validation), shown below:</span></span>

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

<span data-ttu-id="5dded-168">Die Komponenten unterbrechen wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5dded-168">The components break down as follows:</span></span>

* <span data-ttu-id="5dded-169">der Marker `(00 00)`</span><span class="sxs-lookup"><span data-stu-id="5dded-169">the marker `(00 00)`</span></span>

* <span data-ttu-id="5dded-170">die Länge des Blockchiffre Schlüssels `(00 00 00 18)`</span><span class="sxs-lookup"><span data-stu-id="5dded-170">the block cipher key length `(00 00 00 18)`</span></span>

* <span data-ttu-id="5dded-171">Blockgröße für Blockchiffre `(00 00 00 08)`</span><span class="sxs-lookup"><span data-stu-id="5dded-171">the block cipher block size `(00 00 00 08)`</span></span>

* <span data-ttu-id="5dded-172">die Länge des HMAC-Schlüssels `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="5dded-172">the HMAC key length `(00 00 00 14)`</span></span>

* <span data-ttu-id="5dded-173">die HMAC-Digest-Größe `(00 00 00 14)`</span><span class="sxs-lookup"><span data-stu-id="5dded-173">the HMAC digest size `(00 00 00 14)`</span></span>

* <span data-ttu-id="5dded-174">die Blockchiffre-PRP `(AB B1 - E1 0E)` -Ausgabe und</span><span class="sxs-lookup"><span data-stu-id="5dded-174">the block cipher PRP output `(AB B1 - E1 0E)` and</span></span>

* <span data-ttu-id="5dded-175">die HMAC-PRF-Ausgabe `(76 EB - end)` .</span><span class="sxs-lookup"><span data-stu-id="5dded-175">the HMAC PRF output `(76 EB - end)`.</span></span>

## <a name="galoiscounter-mode-encryption--authentication"></a><span data-ttu-id="5dded-176">Galois/Counter-modusverschlüsselung + Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5dded-176">Galois/Counter Mode encryption + authentication</span></span>

<span data-ttu-id="5dded-177">Der Kontext Header besteht aus den folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="5dded-177">The context header consists of the following components:</span></span>

* <span data-ttu-id="5dded-178">[16 Bits] Der Wert 00 01, bei dem es sich um einen Marker handelt, der "GCM-Verschlüsselung + Authentifizierung" bedeutet.</span><span class="sxs-lookup"><span data-stu-id="5dded-178">[16 bits] The value 00 01, which is a marker meaning "GCM encryption + authentication".</span></span>

* <span data-ttu-id="5dded-179">[32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des Algorithmus für symmetrische Blockchiffre.</span><span class="sxs-lookup"><span data-stu-id="5dded-179">[32 bits] The key length (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span>

* <span data-ttu-id="5dded-180">[32 Bits] Die Nonce-Größe (in Byte, Big-Endian), die bei authentifizierten Verschlüsselungs Vorgängen verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5dded-180">[32 bits] The nonce size (in bytes, big-endian) used during authenticated encryption operations.</span></span> <span data-ttu-id="5dded-181">(Für unser System wird dies bei Nonce size = 96 Bits korrigiert.)</span><span class="sxs-lookup"><span data-stu-id="5dded-181">(For our system, this is fixed at nonce size = 96 bits.)</span></span>

* <span data-ttu-id="5dded-182">[32 Bits] Die Blockgröße (in Byte, Big-Endian) des Algorithmus für symmetrische Blockchiffre.</span><span class="sxs-lookup"><span data-stu-id="5dded-182">[32 bits] The block size (in bytes, big-endian) of the symmetric block cipher algorithm.</span></span> <span data-ttu-id="5dded-183">(Bei GCM wird dies bei Blockgröße = 128 Bits korrigiert.)</span><span class="sxs-lookup"><span data-stu-id="5dded-183">(For GCM, this is fixed at block size = 128 bits.)</span></span>

* <span data-ttu-id="5dded-184">[32 Bits] Die von der authentifizierten Verschlüsselungsfunktion erzeugte authentifizierungstag Größe (in Byte, Big-Endian).</span><span class="sxs-lookup"><span data-stu-id="5dded-184">[32 bits] The authentication tag size (in bytes, big-endian) produced by the authenticated encryption function.</span></span> <span data-ttu-id="5dded-185">(Für unser System wird dies bei Tag Size = 128 Bits korrigiert.)</span><span class="sxs-lookup"><span data-stu-id="5dded-185">(For our system, this is fixed at tag size = 128 bits.)</span></span>

* <span data-ttu-id="5dded-186">[128 Bits] Das-Tag von `Enc_GCM (K_E, nonce, "")` , bei dem es sich um die Ausgabe des symmetrischen Blockchiffre Algorithmus handelt, wenn eine leere Zeichenfolge eingegeben wird und wobei Nonce ein 96-Bit-Vektor mit allen NULL-Werten ist.</span><span class="sxs-lookup"><span data-stu-id="5dded-186">[128 bits] The tag of `Enc_GCM (K_E, nonce, "")`, which is the output of the symmetric block cipher algorithm given an empty string input and where nonce is a 96-bit all-zero vector.</span></span>

<span data-ttu-id="5dded-187">`K_E` wird unter Verwendung desselben Mechanismus wie im Szenario für die CBC-Verschlüsselung und HMAC-Authentifizierung abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5dded-187">`K_E` is derived using the same mechanism as in the CBC encryption + HMAC authentication scenario.</span></span> <span data-ttu-id="5dded-188">Da es hier keine Rolle gibt, `K_H` haben wir im Grunde `| K_H | = 0` , und der Algorithmus wird auf das folgende Formular reduziert.</span><span class="sxs-lookup"><span data-stu-id="5dded-188">However, since there's no `K_H` in play here, we essentially have `| K_H | = 0`, and the algorithm collapses to the below form.</span></span>

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a><span data-ttu-id="5dded-189">Beispiel: AES-256-GCM</span><span class="sxs-lookup"><span data-stu-id="5dded-189">Example: AES-256-GCM</span></span>

<span data-ttu-id="5dded-190">Zuerst, Let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 256 bits` .</span><span class="sxs-lookup"><span data-stu-id="5dded-190">First, let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`, where `| K_E | = 256 bits`.</span></span>

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

<span data-ttu-id="5dded-191">Berechnen Sie als nächstes das authentifizierungstag von `Enc_GCM (K_E, nonce, "")` für AES-256-GCM, `nonce = 096` und zwar `K_E` wie oben angegeben.</span><span class="sxs-lookup"><span data-stu-id="5dded-191">Next, compute the authentication tag of `Enc_GCM (K_E, nonce, "")` for AES-256-GCM given `nonce = 096` and `K_E` as above.</span></span>

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

<span data-ttu-id="5dded-192">Dies erzeugt den vollständigen Kontext Header unten:</span><span class="sxs-lookup"><span data-stu-id="5dded-192">This produces the full context header below:</span></span>

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

<span data-ttu-id="5dded-193">Die Komponenten unterbrechen wie folgt:</span><span class="sxs-lookup"><span data-stu-id="5dded-193">The components break down as follows:</span></span>

* <span data-ttu-id="5dded-194">der Marker `(00 01)`</span><span class="sxs-lookup"><span data-stu-id="5dded-194">the marker `(00 01)`</span></span>

* <span data-ttu-id="5dded-195">die Länge des Blockchiffre Schlüssels `(00 00 00 20)`</span><span class="sxs-lookup"><span data-stu-id="5dded-195">the block cipher key length `(00 00 00 20)`</span></span>

* <span data-ttu-id="5dded-196">die Nonce-Größe `(00 00 00 0C)`</span><span class="sxs-lookup"><span data-stu-id="5dded-196">the nonce size `(00 00 00 0C)`</span></span>

* <span data-ttu-id="5dded-197">Blockgröße für Blockchiffre `(00 00 00 10)`</span><span class="sxs-lookup"><span data-stu-id="5dded-197">the block cipher block size `(00 00 00 10)`</span></span>

* <span data-ttu-id="5dded-198">die authentifizierungstag Größe `(00 00 00 10)` und</span><span class="sxs-lookup"><span data-stu-id="5dded-198">the authentication tag size `(00 00 00 10)` and</span></span>

* <span data-ttu-id="5dded-199">Das authentifizierungstag der Ausführung des Blockchiffre `(E7 DC - end)` .</span><span class="sxs-lookup"><span data-stu-id="5dded-199">the authentication tag from running the block cipher `(E7 DC - end)`.</span></span>
