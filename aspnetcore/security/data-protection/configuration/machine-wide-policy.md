---
title: Unterstützung der Computer weiten Datenschutzrichtlinie in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Unterstützung für das Festlegen einer standardmäßigen Computer weiten Richtlinie für alle apps, die ASP.net Core Datenschutz nutzen.
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
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: f6f258e193bf964cb9b4cbd24da2740c5ed89a91
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052993"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Unterstützung der Computer weiten Datenschutzrichtlinie in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Bei Ausführung unter Windows bietet das Datenschutzsystem eingeschränkte Unterstützung für das Festlegen einer standardmäßigen Computer weiten Richtlinie für alle apps, die ASP.net Core Datenschutz nutzen. Die allgemeine Idee ist, dass ein Administrator möglicherweise eine Standardeinstellung ändern möchte, z. b. die verwendeten Algorithmen oder die Schlüssel Lebensdauer, ohne dass jede APP auf dem Computer manuell aktualisiert werden muss.

> [!WARNING]
> Der Systemadministrator kann die Standard Richtlinie festlegen, aber nicht erzwingen. Der App-Entwickler kann jeden beliebigen Wert mit einer ihrer eigenen Auswahl überschreiben. Die Standard Richtlinie wirkt sich nur auf apps aus, bei denen der Entwickler keinen expliziten Wert für eine Einstellung angegeben hat.

## <a name="setting-default-policy"></a>Festlegen der Standard Richtlinie

Um die Standard Richtlinie festzulegen, kann ein Administrator bekannte Werte in der Systemregistrierung unter folgendem Registrierungsschlüssel festlegen:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Wenn Sie ein 64-Bit-Betriebssystem verwenden und das Verhalten von 32-Bit-apps beeinflussen möchten, denken Sie daran, die Wow6432Node-Entsprechung des obigen Schlüssels zu konfigurieren.

Die unterstützten Werte sind unten dargestellt.

| Wert              | Typ   | Beschreibung |
| ------------------ | :----: | ----------- |
| EncryptionType     | Zeichenfolge | Gibt an, welche Algorithmen für den Datenschutz verwendet werden sollen. Der Wert muss CNG-CBC, CNG-GCM oder Managed lauten und wird unten ausführlicher beschrieben. |
| Defaultkeylifetime | DWORD  | Gibt die Lebensdauer für neu generierte Schlüssel an. Der Wert wird in Tagen angegeben und muss >= 7 sein. |
| Keyescrowsinks     | Zeichenfolge | Gibt die Typen an, die für die Schlüssel Hinterlegung verwendet werden. Der Wert ist eine durch Semikolons getrennte Liste von Schlüssel Hinterlegungs senken, wobei jedes Element in der Liste der durch die Assembly qualifizierte Name eines Typs ist, der [ikeyescrowsink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink)implementiert. |

## <a name="encryption-types"></a>Verschlüsselungstypen

Wenn Verschlüsselungstyp CNG-CBC ist, wird das System für die Verwendung eines symmetrischen Blockchiffre im CBC-Modus für Vertraulichkeit und HMAC für die Authentizität mit den von Windows CNG bereitgestellten Diensten konfiguriert (Weitere Informationen finden Sie unter [Angeben von benutzerdefinierten Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft im cngcbcauthenticatedencryptionsettings-Typ entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name eines symmetrischen Blockchiffre Algorithmus, der von CNG interpretiert wird. Dieser Algorithmus wird im CBC-Modus geöffnet. |
| Verschlüsselungs algorithmuprovider | Zeichenfolge | Der Name der CNG-Anbieter Implementierung, die den Algorithmus "Verschlüsselungalgorithmus" bilden kann. |
| Verschlüsselungsalgorithmkeysize  | DWORD  | Die Länge des Schlüssels (in Bits), der für den symmetrischen Blockchiffre Algorithmus abgeleitet werden soll. |
| HashAlgorithm               | Zeichenfolge | Der Name eines Hash Algorithmus, der von CNG interpretiert wird. Dieser Algorithmus wird im HMAC-Modus geöffnet. |
| Hashalgorithmprovider       | Zeichenfolge | Der Name der CNG-Anbieter Implementierung, die den Algorithmus HashAlgorithm bilden kann. |

Wenn der Verschlüsselungstyp CNG-GCM ist, wird das System so konfiguriert, dass er eine Verschlüsselung mit dem symmetrischen Block "Galois/Counter Mode" für Vertraulichkeit und Authentizität mit Diensten von Windows CNG verwendet (Weitere Informationen finden Sie unter [Angeben von benutzerdefinierten Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) ). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft des cnggcmauthenticatedencryptionsettings-Typs entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name eines symmetrischen Blockchiffre Algorithmus, der von CNG interpretiert wird. Dieser Algorithmus wird im Modus "Galois/Counter" geöffnet. |
| Verschlüsselungs algorithmuprovider | Zeichenfolge | Der Name der CNG-Anbieter Implementierung, die den Algorithmus "Verschlüsselungalgorithmus" bilden kann. |
| Verschlüsselungsalgorithmkeysize  | DWORD  | Die Länge des Schlüssels (in Bits), der für den symmetrischen Blockchiffre Algorithmus abgeleitet werden soll. |

Wenn verschlüsselungstype verwaltet wird, ist das System so konfiguriert, dass ein verwaltetes SymmetricAlgorithm für Vertraulichkeit und KeyedHashAlgorithm für die Authentizität verwendet wird (Weitere Informationen finden Sie unter [Angeben von benutzerdefinierten verwalteten Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) ). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft im managedauthenticatedencryptionsettings-Typ entspricht.

| Wert                      | Typ   | Beschreibung |
| -------------------------- | :----: | ----------- |
| Verschlüsselungsalgorithmtype    | Zeichenfolge | Der durch die Assembly qualifizierte Name eines Typs, der SymmetricAlgorithm implementiert. |
| Verschlüsselungsalgorithmkeysize | DWORD  | Die Länge des Schlüssels (in Bits), der für den symmetrischen Verschlüsselungsalgorithmus abgeleitet werden soll. |
| Validationalgorithmtype    | Zeichenfolge | Der durch die Assembly qualifizierte Name eines Typs, der KeyedHashAlgorithm implementiert. |

Wenn der Verschlüsselungstyp einen anderen Wert als NULL oder leer hat, löst das Datenschutzsystem beim Start eine Ausnahme aus.

> [!WARNING]
> Beim Konfigurieren einer Standardrichtlinien Einstellung, die Typnamen einschließt ("verschlüsselungalgorithmtype", "validationalgorithmtype", "keyescrowsinks"), müssen die Typen für die app verfügbar sein. Dies bedeutet, dass die Assemblys, die diese Typen enthalten, im globalen Assemblycache (Global Assembly Cache, GAC) vorhanden sein sollten. Für ASP.net Core-apps, die unter .net Core ausgeführt werden, sollten die Pakete installiert werden, die diese Typen enthalten.
