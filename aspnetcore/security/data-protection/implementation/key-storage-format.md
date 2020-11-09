---
title: Schlüsselspeicher Format in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails des Speicher Formats für die ASP.net Core Datenschutz Schlüssel.
ms.author: riande
ms.date: 04/08/2020
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
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 4a8503964c98d1828dc9d02640a7621b370e679c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060143"
---
# <a name="key-storage-format-in-aspnet-core"></a><span data-ttu-id="cfbd1-103">Schlüsselspeicher Format in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="cfbd1-103">Key storage format in ASP.NET Core</span></span>

<a name="data-protection-implementation-key-storage-format"></a>

<span data-ttu-id="cfbd1-104">Objekte werden im Ruhezustand in der XML-Darstellung gespeichert.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-104">Objects are stored at rest in XML representation.</span></span> <span data-ttu-id="cfbd1-105">Das Standardverzeichnis für den Schlüsselspeicher lautet:</span><span class="sxs-lookup"><span data-stu-id="cfbd1-105">The default directory for key storage is:</span></span>

* <span data-ttu-id="cfbd1-106">Windows: \*%localappdata%\ASP.net\dataschutz-Keys\*</span><span class="sxs-lookup"><span data-stu-id="cfbd1-106">Windows: \*%LOCALAPPDATA%\ASP.NET\DataProtection-Keys\*</span></span>
* <span data-ttu-id="cfbd1-107">macOS/Linux: *$Home/.Aspnet/DataProtection-Keys*</span><span class="sxs-lookup"><span data-stu-id="cfbd1-107">macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*</span></span>

## <a name="the-key-element"></a><span data-ttu-id="cfbd1-108">Das \<key>-Element</span><span class="sxs-lookup"><span data-stu-id="cfbd1-108">The \<key> element</span></span>

<span data-ttu-id="cfbd1-109">Schlüssel sind im schlüsselrepository als Objekte der obersten Ebene vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-109">Keys exist as top-level objects in the key repository.</span></span> <span data-ttu-id="cfbd1-110">Gemäß den kontokonventionenschlüsseln haben Sie den Dateinamen **Key-{GUID}. XML** , wobei {GUID} die ID des Schlüssels ist.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-110">By convention keys have the filename **key-{guid}.xml** , where {guid} is the id of the key.</span></span> <span data-ttu-id="cfbd1-111">Jede dieser Dateien enthält einen einzelnen Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-111">Each such file contains a single key.</span></span> <span data-ttu-id="cfbd1-112">Das Format der Datei lautet wie folgt.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-112">The format of the file is as follows.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

<span data-ttu-id="cfbd1-113">Das \<key> -Element enthält die folgenden Attribute und untergeordneten Elemente:</span><span class="sxs-lookup"><span data-stu-id="cfbd1-113">The \<key> element contains the following attributes and child elements:</span></span>

* <span data-ttu-id="cfbd1-114">Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach ein nicseinwert für die Lesbarkeit von Menschen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-114">The key id. This value is treated as authoritative; the filename is simply a nicety for human readability.</span></span>

* <span data-ttu-id="cfbd1-115">Die Version des \<key> Elements, die derzeit bei 1 korrigiert ist.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-115">The version of the \<key> element, currently fixed at 1.</span></span>

* <span data-ttu-id="cfbd1-116">Die Erstellungs-, Aktivierungs-und Ablaufdatums Angaben des Schlüssels.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-116">The key's creation, activation, and expiration dates.</span></span>

* <span data-ttu-id="cfbd1-117">Ein- \<descriptor> Element, das Informationen zur Implementierung der authentifizierten Verschlüsselung enthält, die in diesem Schlüssel enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-117">A \<descriptor> element, which contains information on the authenticated encryption implementation contained within this key.</span></span>

<span data-ttu-id="cfbd1-118">Im obigen Beispiel lautet die ID des Schlüssels {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, wurde am 19. März 2015 erstellt und aktiviert und verfügt über eine Gültigkeitsdauer von 90 Tagen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-118">In the above example, the key's id is {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, it was created and activated on March 19, 2015, and it has a lifetime of 90 days.</span></span> <span data-ttu-id="cfbd1-119">(Gelegentlich ist das Aktivierungsdatum möglicherweise etwas vor dem Erstellungsdatum, wie in diesem Beispiel.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-119">(Occasionally the activation date might be slightly before the creation date as in this example.</span></span> <span data-ttu-id="cfbd1-120">Der Grund hierfür ist die Funktionsweise der APIs und ist in der Praxis harmlos.)</span><span class="sxs-lookup"><span data-stu-id="cfbd1-120">This is due to a nit in how the APIs work and is harmless in practice.)</span></span>

## <a name="the-descriptor-element"></a><span data-ttu-id="cfbd1-121">Das \<descriptor>-Element</span><span class="sxs-lookup"><span data-stu-id="cfbd1-121">The \<descriptor> element</span></span>

<span data-ttu-id="cfbd1-122">Das äußere \<descriptor> Element enthält ein deserializertype-Attribut, bei dem es sich um den durch die Assembly qualifizierten Namen eines Typs handelt, der iauthenticatedencryptordescriptordeserializer implementiert.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-122">The outer \<descriptor> element contains an attribute deserializerType, which is the assembly-qualified name of a type which implements IAuthenticatedEncryptorDescriptorDeserializer.</span></span> <span data-ttu-id="cfbd1-123">Dieser Typ ist für das Lesen des inneren \<descriptor> Elements und für das Auswerten der in enthaltenen Informationen verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-123">This type is responsible for reading the inner \<descriptor> element and for parsing the information contained within.</span></span>

<span data-ttu-id="cfbd1-124">Das jeweilige Format des- \<descriptor> Elements hängt von der durch den Schlüssel gekapselten authentifizierten Verschlüsselungs Implementierung ab, und jeder deserialisierungstyp erwartet ein etwas anderes Format für dieses.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-124">The particular format of the \<descriptor> element depends on the authenticated encryptor implementation encapsulated by the key, and each deserializer type expects a slightly different format for this.</span></span> <span data-ttu-id="cfbd1-125">Im Allgemeinen enthält dieses Element jedoch algorithmische Informationen (Namen, Typen, OIDs oder ähnliches) und Geheimnis Schlüsselmaterial.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-125">In general, though, this element will contain algorithmic information (names, types, OIDs, or similar) and secret key material.</span></span> <span data-ttu-id="cfbd1-126">Im obigen Beispiel gibt der Deskriptor an, dass dieser Schlüssel die AES-256-CBC-Verschlüsselung + HMACSHA256-Überprüfung umschließt.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-126">In the above example, the descriptor specifies that this key wraps AES-256-CBC encryption + HMACSHA256 validation.</span></span>

## <a name="the-encryptedsecret-element"></a><span data-ttu-id="cfbd1-127">Das \<encryptedSecret>-Element</span><span class="sxs-lookup"><span data-stu-id="cfbd1-127">The \<encryptedSecret> element</span></span>

<span data-ttu-id="cfbd1-128">Wenn die [Verschlüsselung von geheimen Schlüsseln aktiviert ist](xref:security/data-protection/implementation/key-encryption-at-rest), kann ein **&lt; verschlüsseltedsecret &gt;** -Element vorhanden sein, das die verschlüsselte Form des geheimen Schlüssel Materials enthält.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-128">An **&lt;encryptedSecret&gt;** element which contains the encrypted form of the secret key material may be present if [encryption of secrets at rest is enabled](xref:security/data-protection/implementation/key-encryption-at-rest).</span></span> <span data-ttu-id="cfbd1-129">Das-Attribut `decryptorType` ist der durch die Assembly qualifizierte Name eines Typs, der [ixmldecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)implementiert.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-129">The attribute `decryptorType` is the assembly-qualified name of a type which implements [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor).</span></span> <span data-ttu-id="cfbd1-130">Dieser Typ ist verantwortlich für das Lesen des inneren **&lt; verschlüsseltedkey &gt;** -Elements und das entschlüsseln, um den ursprünglichen Klartext wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-130">This type is responsible for reading the inner **&lt;encryptedKey&gt;** element and decrypting it to recover the original plaintext.</span></span>

<span data-ttu-id="cfbd1-131">Wie bei `<descriptor>` ist das jeweilige Format des `<encryptedSecret>` -Elements abhängig von dem verwendeten ruhenden Verschlüsselungsmechanismus.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-131">As with `<descriptor>`, the particular format of the `<encryptedSecret>` element depends on the at-rest encryption mechanism in use.</span></span> <span data-ttu-id="cfbd1-132">Im obigen Beispiel wird der Hauptschlüssel mit Windows DPAPI gemäß dem Kommentar verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-132">In the above example, the master key is encrypted using Windows DPAPI per the comment.</span></span>

## <a name="the-revocation-element"></a><span data-ttu-id="cfbd1-133">Das \<revocation>-Element</span><span class="sxs-lookup"><span data-stu-id="cfbd1-133">The \<revocation> element</span></span>

<span data-ttu-id="cfbd1-134">Widerrufen sind im schlüsselrepository als Objekte der obersten Ebene vorhanden.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-134">Revocations exist as top-level objects in the key repository.</span></span> <span data-ttu-id="cfbd1-135">Gemäß der Konvention verfügen Widerrufungen über den Dateinamen " **Widerruf-{Timestamp}. XML** " (zum Aufheben aller Schlüssel vor einem bestimmten Datum) oder " **Widerruf-{GUID}. XML** " (für das Aufheben eines bestimmten Schlüssels).</span><span class="sxs-lookup"><span data-stu-id="cfbd1-135">By convention revocations have the filename **revocation-{timestamp}.xml** (for revoking all keys before a specific date) or **revocation-{guid}.xml** (for revoking a specific key).</span></span> <span data-ttu-id="cfbd1-136">Jede Datei enthält ein einzelnes \<revocation> Element.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-136">Each file contains a single \<revocation> element.</span></span>

<span data-ttu-id="cfbd1-137">Für das Aufheben der einzelnen Schlüssel wird der Inhalt der Datei wie unten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-137">For revocations of individual keys, the file contents will be as below.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="cfbd1-138">In diesem Fall wird nur der angegebene Schlüssel widerrufen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-138">In this case, only the specified key is revoked.</span></span> <span data-ttu-id="cfbd1-139">Wenn die Schlüssel-ID jedoch "\*" lautet, wie im folgenden Beispiel, werden alle Schlüssel, deren Erstellungsdatum vor dem angegebenen Sperr Datum liegt, widerrufen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-139">If the key id is "\*", however, as in the below example, all keys whose creation date is prior to the specified revocation date are revoked.</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

<span data-ttu-id="cfbd1-140">Das- \<reason> Element wird nie vom System gelesen.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-140">The \<reason> element is never read by the system.</span></span> <span data-ttu-id="cfbd1-141">Es ist einfach ein guter Ort, um einen lesbaren Grund für den Widerruf zu speichern.</span><span class="sxs-lookup"><span data-stu-id="cfbd1-141">It's simply a convenient place to store a human-readable reason for revocation.</span></span>
