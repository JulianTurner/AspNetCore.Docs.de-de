---
title: Kontext Header in ASP.net Core
author: rick-anderson
description: Weitere Informationen zur Implementierung von ASP.net Core Datenschutz-Kontext Headern.
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
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 45463c9d96ad58e1721f7cb5c16912f783f646ed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051719"
---
# <a name="context-headers-in-aspnet-core"></a>Kontext Header in ASP.net Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Hintergrund und Theorie

Im Datenschutzsystem bedeutet ein "Schlüssel" ein Objekt, das authentifizierte Verschlüsselungsdienste bereitstellen kann. Jeder Schlüssel wird durch eine eindeutige ID (eine GUID) identifiziert, und er enthält algorithmische Informationen und Entropie Material. Es ist beabsichtigt, dass jeder Schlüssel eine eindeutige Entropie trägt, aber das System kann dies nicht erzwingen. Außerdem müssen Entwickler, die den Schlüsselbund manuell ändern können, die algorithmischen Informationen eines vorhandenen Schlüssels im Schlüsselbund ändern. Um unsere Sicherheitsanforderungen in diesen Fällen zu erfüllen, verfügt das Datenschutzsystem über ein Konzept der [kryptografischen Agilität](https://www.microsoft.com/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption), das eine sichere Verwendung eines einzelnen Entropie Werts in mehreren Kryptografiealgorithmen ermöglicht.

Bei den meisten Systemen, die kryptografische Agilität unterstützen, werden in der Nutzlast einige identifizierende Informationen über den Algorithmus eingeschlossen. Die OID des Algorithmus ist im Allgemeinen ein guter Kandidat dafür. Allerdings besteht ein Problem darin, dass es mehrere Möglichkeiten gibt, denselben Algorithmus anzugeben: "die Klassen AES (CNG) und Managed AES, AesManaged, AesCryptoServiceProvider, aescng und RijndaelManaged (mit bestimmten Parametern) sind tatsächlich identisch, und wir müssten eine Zuordnung all dieser Elemente zur richtigen OID beibehalten. Wenn ein Entwickler einen benutzerdefinierten Algorithmus (oder sogar eine andere Implementierung von AES!) bereitstellen möchte, muss er uns seine OID mitteilen. Durch diesen zusätzlichen Registrierungs Schritt ist die Systemkonfiguration besonders mühsam.

Wir haben uns entschieden, das Problem aus der falschen Richtung zu beheben. Eine OID gibt Aufschluss darüber, was der Algorithmus ist, aber das ist nicht wirklich wichtig. Wenn wir einen einzelnen Entropie Wert sicher in zwei verschiedenen Algorithmen verwenden müssen, ist es nicht erforderlich, dass wir wissen, was die Algorithmen tatsächlich sind. Wir kümmern uns darum, wie Sie sich Verhalten. Jeder angemessene symmetrische Blockchiffre Algorithmus ist auch eine starke Pseudo Zufalls-permutations (PRP): Korrigieren der Eingaben (Schlüssel, Verkettungs Modus, IV, Klartext), und die Chiffre Textausgabe wird mit überwältigender Wahrscheinlichkeit von allen anderen symmetrischen Blockverschlüsselungsalgorithmen unterschieden, wenn dieselben Eingaben verwendet werden. Ebenso ist jede beliebige Schlüssel gebundene Hash Funktion auch eine starke Pseudo Zufallsfunktion (PRF), und bei einem festen Eingabe Satz unterscheidet sich Ihre Ausgabe überwiegend von jeder anderen Schlüssel gebundenen Hash Funktion.

Wir verwenden dieses Konzept mit starken prps und prfs, um einen Kontext Header zu erstellen. Diese Kontext Kopfzeile fungiert im Wesentlichen als stabiler Fingerabdruck für die Algorithmen, die für einen bestimmten Vorgang verwendet werden, und bietet die kryptografische Agilität, die vom Datenschutzsystem benötigt wird. Dieser Header ist reproduzierbar und wird später als Teil der [Unterschlüssel Ableitung](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation)verwendet. Es gibt zwei verschiedene Möglichkeiten, den Kontext Header abhängig von den Betriebsmodi der zugrunde liegenden Algorithmen zu erstellen.

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC-Modus-Verschlüsselung + HMAC-Authentifizierung

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Der Kontext Header besteht aus den folgenden Komponenten:

* [16 Bits] Der Wert 00 00, bei dem es sich um einen Marker handelt, der "CBC-Verschlüsselung + HMAC-Authentifizierung" bedeutet.

* [32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des Algorithmus für symmetrische Blockchiffre.

* [32 Bits] Die Blockgröße (in Byte, Big-Endian) des Algorithmus für symmetrische Blockchiffre.

* [32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des HMAC-Algorithmus. (Derzeit stimmt die Schlüsselgröße immer mit der Digest-Größe überein.)

* [32 Bits] Die Digest-Größe (in Byte, Big-Endian) des HMAC-Algorithmus.

* `EncCBC(K_E, IV, "")`, bei der es sich um die Ausgabe des symmetrischen Block Verschlüsselungsalgorithmus handelt, bei der eine leere Zeichen folgen Eingabe angegeben ist und wobei der Wert von "IV" ein Vektor ist. Die Erstellung von `K_E` wird nachfolgend beschrieben.

* `MAC(K_H, "")`, bei dem es sich um die Ausgabe des HMAC-Algorithmus handelt, wenn eine leere Zeichenfolge eingegeben wird. Die Erstellung von `K_H` wird nachfolgend beschrieben.

Im Idealfall könnten wir alle Vektoren mit allen Nullen für `K_E` und übergeben `K_H` . Wir möchten jedoch vermeiden, dass die Situation, in der der zugrunde liegende Algorithmus vor dem Ausführen von Vorgängen (insbesondere der und 3DES) das vorhanden sein von schwachen Schlüsseln prüft, die Verwendung eines einfachen oder wiederholbaren Musters, wie z. b. eines Vektor mit allen Nullen, verhindert.

Stattdessen verwenden wir die NIST SP800-108 KDF im Counter-Modus (siehe [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sec. 5,1) mit einem Schlüssel, einer Bezeichnung und einem Kontext der Länge 0 (null) und HMACSHA512 als zugrunde liegende PRF. Wir leiten `| K_E | + | K_H |` Bytes der Ausgabe ab und zerlegen das Ergebnis dann in `K_E` und `K_H` selbst. Dies wird mathematisch wie folgt dargestellt.

`( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-192-cbc--hmacsha256"></a>Beispiel: AES-192-CBC + HMACSHA256

Nehmen Sie als Beispiel den Fall, in dem der symmetrische Blockchiffre Algorithmus AES-192-CBC und der Validierungs Algorithmus HMACSHA256 ist. Das System generiert den Kontext Header mithilfe der folgenden Schritte.

First, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 192 bits` und `| K_H | = 256 bits` pro angegebenen Algorithmen. Dies führt zu `K_E = 5BB6..21DD` und `K_H = A04A..00A9` im folgenden Beispiel:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Berechnen Sie als nächstes Compute `Enc_CBC (K_E, IV, "")` für AES-192-CBC, `IV = 0*` `K_E` wie oben angegeben.

`result := F474B1872B3B53E4721DE19C0841DB6F`

Als Nächstes berechnen Sie `MAC(K_H, "")` für HMACSHA256, `K_H` wie oben angegeben.

`result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C`

Dies erzeugt den vollständigen Kontext Header unten:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Diese Kontext Kopfzeile ist der Fingerabdruck des authentifizierten Verschlüsselungsalgorithmus-Paars (AES-192-CBC-Verschlüsselung + HMACSHA256-Überprüfung). Die Komponenten, wie [oben](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) beschrieben, lauten wie folgt:

* der Marker `(00 00)`

* die Länge des Blockchiffre Schlüssels `(00 00 00 18)`

* Blockgröße für Blockchiffre `(00 00 00 10)`

* die Länge des HMAC-Schlüssels `(00 00 00 20)`

* die HMAC-Digest-Größe `(00 00 00 20)`

* die Blockchiffre-PRP `(F4 74 - DB 6F)` -Ausgabe und

* die HMAC-PRF-Ausgabe `(D4 79 - end)` .

> [!NOTE]
> Der Kontext Header "Verschlüsselung im CBC-Modus + HMAC-Authentifizierung" wird auf dieselbe Weise erstellt, unabhängig davon, ob die Algorithmen Implementierungen von Windows CNG oder von verwalteten SymmetricAlgorithm-und KeyedHashAlgorithm-Typen bereitgestellt werden. Dadurch können Anwendungen, die unter verschiedenen Betriebssystemen ausgeführt werden, zuverlässig denselben Kontext Header entwickeln, auch wenn sich die Implementierungen der Algorithmen zwischen den Betriebssystemen unterscheiden. (In der Praxis muss KeyedHashAlgorithm nicht der richtige HMAC sein. Dabei kann es sich um einen beliebigen Hash Algorithmustyp handeln.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Beispiel: 3DES-192-CBC + HMACSHA1

First, Let `( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 192 bits` und `| K_H | = 160 bits` pro angegebenen Algorithmen. Dies führt zu `K_E = A219..E2BB` und `K_H = DC4A..B464` im folgenden Beispiel:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Als Nächstes berechnen Sie `Enc_CBC (K_E, IV, "")` für 3DES-192-CBC `IV = 0*` und `K_E` wie oben angegeben.

`result := ABB100F81E53E10E`

Als Nächstes berechnen Sie `MAC(K_H, "")` für HMACSHA1, `K_H` wie oben angegeben.

`result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555`

Dies erzeugt den vollständigen Kontext Header, bei dem es sich um einen Fingerabdruck des authentifizierten Verschlüsselungsalgorithmus-Paars handelt (3DES-192-CBC-Verschlüsselung + HMACSHA1-Validierung), wie unten dargestellt:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Die Komponenten unterbrechen wie folgt:

* der Marker `(00 00)`

* die Länge des Blockchiffre Schlüssels `(00 00 00 18)`

* Blockgröße für Blockchiffre `(00 00 00 08)`

* die Länge des HMAC-Schlüssels `(00 00 00 14)`

* die HMAC-Digest-Größe `(00 00 00 14)`

* die Blockchiffre-PRP `(AB B1 - E1 0E)` -Ausgabe und

* die HMAC-PRF-Ausgabe `(76 EB - end)` .

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/Counter-modusverschlüsselung + Authentifizierung

Der Kontext Header besteht aus den folgenden Komponenten:

* [16 Bits] Der Wert 00 01, bei dem es sich um einen Marker handelt, der "GCM-Verschlüsselung + Authentifizierung" bedeutet.

* [32 Bits] Die Schlüssellänge (in Byte, Big-tedian) des Algorithmus für symmetrische Blockchiffre.

* [32 Bits] Die Nonce-Größe (in Byte, Big-Endian), die bei authentifizierten Verschlüsselungs Vorgängen verwendet wird. (Für unser System wird dies bei Nonce size = 96 Bits korrigiert.)

* [32 Bits] Die Blockgröße (in Byte, Big-Endian) des Algorithmus für symmetrische Blockchiffre. (Bei GCM wird dies bei Blockgröße = 128 Bits korrigiert.)

* [32 Bits] Die von der authentifizierten Verschlüsselungsfunktion erzeugte authentifizierungstag Größe (in Byte, Big-Endian). (Für unser System wird dies bei Tag Size = 128 Bits korrigiert.)

* [128 Bits] Das-Tag von `Enc_GCM (K_E, nonce, "")` , bei dem es sich um die Ausgabe des symmetrischen Blockchiffre Algorithmus handelt, wenn eine leere Zeichenfolge eingegeben wird und wobei Nonce ein 96-Bit-Vektor mit allen NULL-Werten ist.

`K_E` wird unter Verwendung desselben Mechanismus wie im Szenario für die CBC-Verschlüsselung und HMAC-Authentifizierung abgeleitet. Da es hier keine Rolle gibt, `K_H` haben wir im Grunde `| K_H | = 0` , und der Algorithmus wird auf das folgende Formular reduziert.

`K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")`

### <a name="example-aes-256-gcm"></a>Beispiel: AES-256-GCM

Zuerst, Let `K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")` , where `| K_E | = 256 bits` .

`K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8`

Berechnen Sie als nächstes das authentifizierungstag von `Enc_GCM (K_E, nonce, "")` für AES-256-GCM, `nonce = 096` und zwar `K_E` wie oben angegeben.

`result := E7DCCE66DF855A323A6BB7BD7A59BE45`

Dies erzeugt den vollständigen Kontext Header unten:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Die Komponenten unterbrechen wie folgt:

* der Marker `(00 01)`

* die Länge des Blockchiffre Schlüssels `(00 00 00 20)`

* die Nonce-Größe `(00 00 00 0C)`

* Blockgröße für Blockchiffre `(00 00 00 10)`

* die authentifizierungstag Größe `(00 00 00 10)` und

* Das authentifizierungstag der Ausführung des Blockchiffre `(E7 DC - end)` .
