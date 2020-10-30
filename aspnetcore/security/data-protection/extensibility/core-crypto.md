---
title: Zentrale kryptografieerweiterbarkeit in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über iauthenticatedencryptor, iauthenticatedencryptordescriptor, iauthenticatedencryptordescriptordeserializer und die Factory der obersten Ebene.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060780"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a>Zentrale kryptografieerweiterbarkeit in ASP.net Core

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> Typen, die eine der folgenden Schnittstellen implementieren, sollten für mehrere Aufrufer Thread sicher sein.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a>Iauthenticatedencryptor

Die **iauthenticatedencryptor** -Schnittstelle ist der Grundbaustein des kryptografischen Subsystems. Im Allgemeinen gibt es einen iauthenticatedencryptor pro Schlüssel, und die Instanz iauthenticatedencryptor umschließt sämtliche kryptografieschlüsselmaterialien und algorithmische Informationen, die für die Durchführung kryptografischer Vorgänge erforderlich sind.

Wie der Name bereits vermuten lässt, ist der Typ für die Bereitstellung von authentifizierten Verschlüsselungs-und Entschlüsselungs Diensten verantwortlich. Es werden die folgenden zwei APIs verfügbar gemacht.

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

Die Methode "verschlüsseln" gibt ein BLOB zurück, das den verschlüsselten Klartext und ein authentifizierungstag enthält. Das authentifizierungstag muss die zusätzlichen authentifizierten Daten (AAD) umfassen, obwohl das Aad-Element von der endgültigen Nutzlast nicht wieder hergestellt werden muss. Die Entschlüsselungsmethode überprüft das authentifizierungstag und gibt die entschlüsselte Nutzlast zurück. Alle Fehler (außer ArgumentNullException und ähnlich) sollten zu CryptographicException homogenisiert werden.

> [!NOTE]
> Die iauthenticatedencryptor-Instanz selbst muss das Schlüsselmaterial nicht enthalten. Die-Implementierung könnte z. b. für alle Vorgänge einen Delegaten an ein HSM delegieren.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a>Erstellen eines iauthenticatedencryptor

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **iauthenticatedencryptorfactory** -Schnittstelle stellt einen Typ dar, der weiß, wie eine [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz erstellt wird. Die API ist wie folgt.

* "Kreateverschlüsseltorinstance" (iKey-Schlüssel): "iauthenticatedencryptor"

Für eine bestimmte iKey-Instanz sollten alle authentifizierten Verschlüsselungsmethoden, die von der-Methode von der-Methode erstellt wurden, als Äquivalent angesehen werden, wie im folgenden Codebeispiel gezeigt.

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **iauthenticatedencryptordescriptor** -Schnittstelle stellt einen Typ dar, der weiß, wie eine [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz erstellt wird. Die API ist wie folgt.

* "Kreateverschlüsseltorinstance ()": "iauthenticatedencryptor"

* Exporttoxml (): xmlserializeddescriptorinfo

Wie bei iauthenticatedencryptor wird angenommen, dass eine Instanz von iauthenticatedencryptordescriptor einen bestimmten Schlüssel packt. Dies bedeutet, dass für jede beliebige iauthenticatedencryptordescriptor-Instanz alle authentifizierten Verschlüsselungsmethoden, die von der Methode "itateverschlüsseltorinstance" erstellt wurden, als Äquivalent angesehen werden, wie im folgenden Codebeispiel gezeigt.

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a>Iauthenticatedencryptordescriptor (nur ASP.net Core 2. x)

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **iauthenticatedencryptordescriptor** -Schnittstelle stellt einen Typ dar, der weiß, wie er sich selbst in XML exportieren kann. Die API ist wie folgt.

* Exporttoxml (): xmlserializeddescriptorinfo

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a>XML-Serialisierung

Der primäre Unterschied zwischen iauthenticatedencryptor und iauthenticatedencryptordescriptor besteht darin, dass der Deskriptor weiß, wie die Verschlüsselung erstellt und mit gültigen Argumenten bereitgestellt wird. Beachten Sie einen iauthenticatedencryptor, dessen Implementierung von SymmetricAlgorithm und KeyedHashAlgorithm abhängig ist. Der Verschlüsselungstyp ist für die Verwendung dieser Typen, aber es ist nicht unbedingt bekannt, woher diese Typen stammen. es ist daher nicht möglich, eine korrekte Beschreibung zu erstellen, wenn die Anwendung neu gestartet wird. Der Deskriptor fungiert oberhalb dieses als eine höhere Ebene. Da der Deskriptor weiß, wie die Verschlüsselungs Instanz erstellt wird (z. b. er weiß, wie die erforderlichen Algorithmen erstellt werden), kann er dieses Wissen in XML-Form serialisieren, sodass die Verschlüsselungs Instanz nach dem Zurücksetzen einer Anwendung neu erstellt werden kann.

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

Der Deskriptor kann über seine exporttoxml-Routine serialisiert werden. Diese Routine gibt ein xmlserializeddescriptorinfo-Element zurück, das zwei Eigenschaften enthält: die XElement-Darstellung des Deskriptors und den Typ, der ein [iauthenticatedencryptordescriptordeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) -Element darstellt, das verwendet werden kann, um diesen Deskriptor mit dem entsprechenden XElement wiederzuverwenden.

Der serialisierte Deskriptor kann vertrauliche Informationen, wie z. b. kryptografieschlüsselmaterial, enthalten. Das Datenschutzsystem verfügt über eine integrierte Unterstützung für die Verschlüsselung von Informationen, bevor Sie im Speicher gespeichert werden. Um dies zu nutzen, sollte der Deskriptor das Element markieren, das vertrauliche Informationen mit dem Attributnamen "Requirements sencryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ") und dem Wert "true" enthält.

>[!TIP]
> Es gibt eine hilfsprogrammapi zum Festlegen dieses Attributs. Nennen Sie die Erweiterungsmethode "XElement. markasrequirements sencryption ()", die sich im Namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.Config"urationmodel" befindet.

Es können auch Fälle geben, in denen der serialisierte Deskriptor keine vertraulichen Informationen enthält. Beachten Sie erneut den Fall eines Kryptografieschlüssels, der in einem HSM gespeichert ist. Der Deskriptor kann das Schlüsselmaterial bei der Serialisierung nicht schreiben, da das HSM das Material nicht in Klartext-Form verfügbar macht. Stattdessen schreibt der Deskriptor möglicherweise die Schlüssel umschließende Version des Schlüssels (wenn das HSM auf diese Weise exportiert werden kann) oder den eigenen eindeutigen Bezeichner des HSM für den Schlüssel.

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a>Iauthenticatedencryptordescriptordeserializer

Die **iauthenticatedencryptordescriptordeserializer** -Schnittstelle stellt einen Typ dar, der weiß, wie eine iauthenticatedencryptordescriptor-Instanz aus einem XElement-Element deserialisiert werden kann. Es macht eine einzelne Methode verfügbar:

* Importfromxml (XElement-Element): iauthenticatedencryptordescriptor

Die importfromxml-Methode übernimmt das XElement, das von [iauthenticatedencryptordescriptor. exporttoxml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) zurückgegeben wurde, und erstellt eine Entsprechung des ursprünglichen iauthenticatedencryptordescriptor.

Typen, die iauthenticatedencryptordescriptordeserializer implementieren, sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:

* . ctor (IServiceProvider)

* . ctor ()

> [!NOTE]
> Der an den Konstruktor übergeben "IServiceProvider" ist möglicherweise NULL.

## <a name="the-top-level-factory"></a>Die Factory der obersten Ebene

# <a name="aspnet-core-2x"></a>[ASP.NET Core 2.x](#tab/aspnetcore2x)

Die **algorithmconfiguration** -Klasse stellt einen Typ dar, der weiß, wie [iauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) -Instanzen erstellt werden. Es macht eine einzelne API verfügbar.

* "Kreatenewdescriptor ()": iauthenticatedencryptordescriptor

Stellen Sie sich algorithmconfiguration als Factory der obersten Ebene vor. Die Konfiguration dient als Vorlage. Er umschließt algorithmische Informationen (z. b., diese Konfiguration erzeugt Deskriptoren mit einem AES-128-GCM-Hauptschlüssel), ist aber noch keinem bestimmten Schlüssel zugeordnet.

Wenn "kreatenewdescriptor" aufgerufen wird, wird das neue Schlüsselmaterial ausschließlich für diesen Aufruf erstellt, und es wird ein neuer "iauthenticatedencryptordescriptor" erstellt, der dieses Schlüsselmaterial und die algorithmischen Informationen umschließt, die zur Nutzung des Materials erforderlich sind. Das Schlüsselmaterial kann in der Software erstellt werden (und im Arbeitsspeicher gespeichert), es kann erstellt und in einem HSM gespeichert werden usw. Der entscheidende Punkt ist, dass bei zwei Aufrufen von "" für "" "" "" "" "" "" "" "" "" "".

Der algorithmconfiguration-Typ dient als Einstiegspunkt für Schlüssel Erstellungs Routinen wie [Automatisches Rollback von Schlüsseln](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel zu ändern, legen Sie die authenticatedencryptorconfiguration-Eigenschaft in keymanagementoptions fest.

# <a name="aspnet-core-1x"></a>[ASP.NET Core 1.x](#tab/aspnetcore1x)

Die **iauthenticatedencryptorconfiguration** -Schnittstelle stellt einen Typ dar, der weiß, wie [iauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) -Instanzen erstellt werden. Es macht eine einzelne API verfügbar.

* "Kreatenewdescriptor ()": iauthenticatedencryptordescriptor

Betrachten Sie iauthenticatedencryptor Configuration als Factory der obersten Ebene. Die Konfiguration dient als Vorlage. Er umschließt algorithmische Informationen (z. b., diese Konfiguration erzeugt Deskriptoren mit einem AES-128-GCM-Hauptschlüssel), ist aber noch keinem bestimmten Schlüssel zugeordnet.

Wenn "kreatenewdescriptor" aufgerufen wird, wird das neue Schlüsselmaterial ausschließlich für diesen Aufruf erstellt, und es wird ein neuer "iauthenticatedencryptordescriptor" erstellt, der dieses Schlüsselmaterial und die algorithmischen Informationen umschließt, die zur Nutzung des Materials erforderlich sind. Das Schlüsselmaterial kann in der Software erstellt werden (und im Arbeitsspeicher gespeichert), es kann erstellt und in einem HSM gespeichert werden usw. Der entscheidende Punkt ist, dass bei zwei Aufrufen von "" für "" "" "" "" "" "" "" "" "" "".

Der iauthenticatedencryptor Configuration-Typ dient als Einstiegspunkt für Schlüssel Erstellungs Routinen wie [Automatisches Rollback von Schlüsseln](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling). Um die Implementierung für alle zukünftigen Schlüssel zu ändern, registrieren Sie eine Singleton-iauthenticatedencryptorconfiguration im Dienst Container.

---
