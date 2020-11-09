---
title: Zentrale kryptografieerweiterbarkeit in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über iauthenticatedencryptor, iauthenticatedencryptordescriptor, iauthenticatedencryptordescriptordeserializer und die Factory der obersten Ebene.
ms.author: riande
ms.date: 08/11/2017
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
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 99ce283d56a6010ddd846f21e0ca9a9a324d55fc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060780"
---
# <a name="core-cryptography-extensibility-in-aspnet-core"></a><span data-ttu-id="58099-103">Zentrale kryptografieerweiterbarkeit in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="58099-103">Core cryptography extensibility in ASP.NET Core</span></span>

<a name="data-protection-extensibility-core-crypto"></a>

>[!WARNING]
> <span data-ttu-id="58099-104">Typen, die eine der folgenden Schnittstellen implementieren, sollten für mehrere Aufrufer Thread sicher sein.</span><span class="sxs-lookup"><span data-stu-id="58099-104">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptor"></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="58099-105">Iauthenticatedencryptor</span><span class="sxs-lookup"><span data-stu-id="58099-105">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="58099-106">Die **iauthenticatedencryptor** -Schnittstelle ist der Grundbaustein des kryptografischen Subsystems.</span><span class="sxs-lookup"><span data-stu-id="58099-106">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="58099-107">Im Allgemeinen gibt es einen iauthenticatedencryptor pro Schlüssel, und die Instanz iauthenticatedencryptor umschließt sämtliche kryptografieschlüsselmaterialien und algorithmische Informationen, die für die Durchführung kryptografischer Vorgänge erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="58099-107">There's generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="58099-108">Wie der Name bereits vermuten lässt, ist der Typ für die Bereitstellung von authentifizierten Verschlüsselungs-und Entschlüsselungs Diensten verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="58099-108">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="58099-109">Es werden die folgenden zwei APIs verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="58099-109">It exposes the following two APIs.</span></span>

* `Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

* `Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]`

<span data-ttu-id="58099-110">Die Methode "verschlüsseln" gibt ein BLOB zurück, das den verschlüsselten Klartext und ein authentifizierungstag enthält.</span><span class="sxs-lookup"><span data-stu-id="58099-110">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="58099-111">Das authentifizierungstag muss die zusätzlichen authentifizierten Daten (AAD) umfassen, obwohl das Aad-Element von der endgültigen Nutzlast nicht wieder hergestellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="58099-111">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="58099-112">Die Entschlüsselungsmethode überprüft das authentifizierungstag und gibt die entschlüsselte Nutzlast zurück.</span><span class="sxs-lookup"><span data-stu-id="58099-112">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="58099-113">Alle Fehler (außer ArgumentNullException und ähnlich) sollten zu CryptographicException homogenisiert werden.</span><span class="sxs-lookup"><span data-stu-id="58099-113">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="58099-114">Die iauthenticatedencryptor-Instanz selbst muss das Schlüsselmaterial nicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="58099-114">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="58099-115">Die-Implementierung könnte z. b. für alle Vorgänge einen Delegaten an ein HSM delegieren.</span><span class="sxs-lookup"><span data-stu-id="58099-115">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory"></a>
<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor"></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="58099-116">Erstellen eines iauthenticatedencryptor</span><span class="sxs-lookup"><span data-stu-id="58099-116">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="58099-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="58099-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="58099-118">Die **iauthenticatedencryptorfactory** -Schnittstelle stellt einen Typ dar, der weiß, wie eine [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="58099-118">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="58099-119">Die API ist wie folgt.</span><span class="sxs-lookup"><span data-stu-id="58099-119">Its API is as follows.</span></span>

* <span data-ttu-id="58099-120">"Kreateverschlüsseltorinstance" (iKey-Schlüssel): "iauthenticatedencryptor"</span><span class="sxs-lookup"><span data-stu-id="58099-120">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="58099-121">Für eine bestimmte iKey-Instanz sollten alle authentifizierten Verschlüsselungsmethoden, die von der-Methode von der-Methode erstellt wurden, als Äquivalent angesehen werden, wie im folgenden Codebeispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="58099-121">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="58099-122">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="58099-122">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="58099-123">Die **iauthenticatedencryptordescriptor** -Schnittstelle stellt einen Typ dar, der weiß, wie eine [iauthenticatedencryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) -Instanz erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="58099-123">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="58099-124">Die API ist wie folgt.</span><span class="sxs-lookup"><span data-stu-id="58099-124">Its API is as follows.</span></span>

* <span data-ttu-id="58099-125">"Kreateverschlüsseltorinstance ()": "iauthenticatedencryptor"</span><span class="sxs-lookup"><span data-stu-id="58099-125">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="58099-126">Exporttoxml (): xmlserializeddescriptorinfo</span><span class="sxs-lookup"><span data-stu-id="58099-126">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="58099-127">Wie bei iauthenticatedencryptor wird angenommen, dass eine Instanz von iauthenticatedencryptordescriptor einen bestimmten Schlüssel packt.</span><span class="sxs-lookup"><span data-stu-id="58099-127">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="58099-128">Dies bedeutet, dass für jede beliebige iauthenticatedencryptordescriptor-Instanz alle authentifizierten Verschlüsselungsmethoden, die von der Methode "itateverschlüsseltorinstance" erstellt wurden, als Äquivalent angesehen werden, wie im folgenden Codebeispiel gezeigt.</span><span class="sxs-lookup"><span data-stu-id="58099-128">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

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

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="58099-129">Iauthenticatedencryptordescriptor (nur ASP.net Core 2. x)</span><span class="sxs-lookup"><span data-stu-id="58099-129">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="58099-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="58099-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="58099-131">Die **iauthenticatedencryptordescriptor** -Schnittstelle stellt einen Typ dar, der weiß, wie er sich selbst in XML exportieren kann.</span><span class="sxs-lookup"><span data-stu-id="58099-131">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="58099-132">Die API ist wie folgt.</span><span class="sxs-lookup"><span data-stu-id="58099-132">Its API is as follows.</span></span>

* <span data-ttu-id="58099-133">Exporttoxml (): xmlserializeddescriptorinfo</span><span class="sxs-lookup"><span data-stu-id="58099-133">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="58099-134">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="58099-134">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="58099-135">XML-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="58099-135">XML Serialization</span></span>

<span data-ttu-id="58099-136">Der primäre Unterschied zwischen iauthenticatedencryptor und iauthenticatedencryptordescriptor besteht darin, dass der Deskriptor weiß, wie die Verschlüsselung erstellt und mit gültigen Argumenten bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="58099-136">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="58099-137">Beachten Sie einen iauthenticatedencryptor, dessen Implementierung von SymmetricAlgorithm und KeyedHashAlgorithm abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="58099-137">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="58099-138">Der Verschlüsselungstyp ist für die Verwendung dieser Typen, aber es ist nicht unbedingt bekannt, woher diese Typen stammen. es ist daher nicht möglich, eine korrekte Beschreibung zu erstellen, wenn die Anwendung neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="58099-138">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="58099-139">Der Deskriptor fungiert oberhalb dieses als eine höhere Ebene.</span><span class="sxs-lookup"><span data-stu-id="58099-139">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="58099-140">Da der Deskriptor weiß, wie die Verschlüsselungs Instanz erstellt wird (z. b. er weiß, wie die erforderlichen Algorithmen erstellt werden), kann er dieses Wissen in XML-Form serialisieren, sodass die Verschlüsselungs Instanz nach dem Zurücksetzen einer Anwendung neu erstellt werden kann.</span><span class="sxs-lookup"><span data-stu-id="58099-140">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name="data-protection-extensibility-core-crypto-exporttoxml"></a>

<span data-ttu-id="58099-141">Der Deskriptor kann über seine exporttoxml-Routine serialisiert werden.</span><span class="sxs-lookup"><span data-stu-id="58099-141">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="58099-142">Diese Routine gibt ein xmlserializeddescriptorinfo-Element zurück, das zwei Eigenschaften enthält: die XElement-Darstellung des Deskriptors und den Typ, der ein [iauthenticatedencryptordescriptordeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) -Element darstellt, das verwendet werden kann, um diesen Deskriptor mit dem entsprechenden XElement wiederzuverwenden.</span><span class="sxs-lookup"><span data-stu-id="58099-142">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="58099-143">Der serialisierte Deskriptor kann vertrauliche Informationen, wie z. b. kryptografieschlüsselmaterial, enthalten.</span><span class="sxs-lookup"><span data-stu-id="58099-143">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="58099-144">Das Datenschutzsystem verfügt über eine integrierte Unterstützung für die Verschlüsselung von Informationen, bevor Sie im Speicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="58099-144">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="58099-145">Um dies zu nutzen, sollte der Deskriptor das Element markieren, das vertrauliche Informationen mit dem Attributnamen "Requirements sencryption" (xmlns " <http://schemas.asp.net/2015/03/dataProtection> ") und dem Wert "true" enthält.</span><span class="sxs-lookup"><span data-stu-id="58099-145">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "<http://schemas.asp.net/2015/03/dataProtection>"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="58099-146">Es gibt eine hilfsprogrammapi zum Festlegen dieses Attributs.</span><span class="sxs-lookup"><span data-stu-id="58099-146">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="58099-147">Nennen Sie die Erweiterungsmethode "XElement. markasrequirements sencryption ()", die sich im Namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.Config"urationmodel" befindet.</span><span class="sxs-lookup"><span data-stu-id="58099-147">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="58099-148">Es können auch Fälle geben, in denen der serialisierte Deskriptor keine vertraulichen Informationen enthält.</span><span class="sxs-lookup"><span data-stu-id="58099-148">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="58099-149">Beachten Sie erneut den Fall eines Kryptografieschlüssels, der in einem HSM gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="58099-149">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="58099-150">Der Deskriptor kann das Schlüsselmaterial bei der Serialisierung nicht schreiben, da das HSM das Material nicht in Klartext-Form verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="58099-150">The descriptor cannot write out the key material when serializing itself since the HSM won't expose the material in plaintext form.</span></span> <span data-ttu-id="58099-151">Stattdessen schreibt der Deskriptor möglicherweise die Schlüssel umschließende Version des Schlüssels (wenn das HSM auf diese Weise exportiert werden kann) oder den eigenen eindeutigen Bezeichner des HSM für den Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="58099-151">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name="data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer"></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="58099-152">Iauthenticatedencryptordescriptordeserializer</span><span class="sxs-lookup"><span data-stu-id="58099-152">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="58099-153">Die **iauthenticatedencryptordescriptordeserializer** -Schnittstelle stellt einen Typ dar, der weiß, wie eine iauthenticatedencryptordescriptor-Instanz aus einem XElement-Element deserialisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="58099-153">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="58099-154">Es macht eine einzelne Methode verfügbar:</span><span class="sxs-lookup"><span data-stu-id="58099-154">It exposes a single method:</span></span>

* <span data-ttu-id="58099-155">Importfromxml (XElement-Element): iauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="58099-155">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="58099-156">Die importfromxml-Methode übernimmt das XElement, das von [iauthenticatedencryptordescriptor. exporttoxml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) zurückgegeben wurde, und erstellt eine Entsprechung des ursprünglichen iauthenticatedencryptordescriptor.</span><span class="sxs-lookup"><span data-stu-id="58099-156">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="58099-157">Typen, die iauthenticatedencryptordescriptordeserializer implementieren, sollten einen der folgenden zwei öffentlichen Konstruktoren aufweisen:</span><span class="sxs-lookup"><span data-stu-id="58099-157">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="58099-158">. ctor (IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="58099-158">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="58099-159">. ctor ()</span><span class="sxs-lookup"><span data-stu-id="58099-159">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="58099-160">Der an den Konstruktor übergeben "IServiceProvider" ist möglicherweise NULL.</span><span class="sxs-lookup"><span data-stu-id="58099-160">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="58099-161">Die Factory der obersten Ebene</span><span class="sxs-lookup"><span data-stu-id="58099-161">The top-level factory</span></span>

# <a name="aspnet-core-2x"></a>[<span data-ttu-id="58099-162">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="58099-162">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="58099-163">Die **algorithmconfiguration** -Klasse stellt einen Typ dar, der weiß, wie [iauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) -Instanzen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="58099-163">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="58099-164">Es macht eine einzelne API verfügbar.</span><span class="sxs-lookup"><span data-stu-id="58099-164">It exposes a single API.</span></span>

* <span data-ttu-id="58099-165">"Kreatenewdescriptor ()": iauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="58099-165">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="58099-166">Stellen Sie sich algorithmconfiguration als Factory der obersten Ebene vor.</span><span class="sxs-lookup"><span data-stu-id="58099-166">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="58099-167">Die Konfiguration dient als Vorlage.</span><span class="sxs-lookup"><span data-stu-id="58099-167">The configuration serves as a template.</span></span> <span data-ttu-id="58099-168">Er umschließt algorithmische Informationen (z. b., diese Konfiguration erzeugt Deskriptoren mit einem AES-128-GCM-Hauptschlüssel), ist aber noch keinem bestimmten Schlüssel zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="58099-168">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="58099-169">Wenn "kreatenewdescriptor" aufgerufen wird, wird das neue Schlüsselmaterial ausschließlich für diesen Aufruf erstellt, und es wird ein neuer "iauthenticatedencryptordescriptor" erstellt, der dieses Schlüsselmaterial und die algorithmischen Informationen umschließt, die zur Nutzung des Materials erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="58099-169">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="58099-170">Das Schlüsselmaterial kann in der Software erstellt werden (und im Arbeitsspeicher gespeichert), es kann erstellt und in einem HSM gespeichert werden usw.</span><span class="sxs-lookup"><span data-stu-id="58099-170">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="58099-171">Der entscheidende Punkt ist, dass bei zwei Aufrufen von "" für "" "" "" "" "" "" "" "" "" "".</span><span class="sxs-lookup"><span data-stu-id="58099-171">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="58099-172">Der algorithmconfiguration-Typ dient als Einstiegspunkt für Schlüssel Erstellungs Routinen wie [Automatisches Rollback von Schlüsseln](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="58099-172">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="58099-173">Um die Implementierung für alle zukünftigen Schlüssel zu ändern, legen Sie die authenticatedencryptorconfiguration-Eigenschaft in keymanagementoptions fest.</span><span class="sxs-lookup"><span data-stu-id="58099-173">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1x"></a>[<span data-ttu-id="58099-174">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="58099-174">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="58099-175">Die **iauthenticatedencryptorconfiguration** -Schnittstelle stellt einen Typ dar, der weiß, wie [iauthenticatedencryptordescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) -Instanzen erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="58099-175">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="58099-176">Es macht eine einzelne API verfügbar.</span><span class="sxs-lookup"><span data-stu-id="58099-176">It exposes a single API.</span></span>

* <span data-ttu-id="58099-177">"Kreatenewdescriptor ()": iauthenticatedencryptordescriptor</span><span class="sxs-lookup"><span data-stu-id="58099-177">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="58099-178">Betrachten Sie iauthenticatedencryptor Configuration als Factory der obersten Ebene.</span><span class="sxs-lookup"><span data-stu-id="58099-178">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="58099-179">Die Konfiguration dient als Vorlage.</span><span class="sxs-lookup"><span data-stu-id="58099-179">The configuration serves as a template.</span></span> <span data-ttu-id="58099-180">Er umschließt algorithmische Informationen (z. b., diese Konfiguration erzeugt Deskriptoren mit einem AES-128-GCM-Hauptschlüssel), ist aber noch keinem bestimmten Schlüssel zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="58099-180">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it's not yet associated with a specific key.</span></span>

<span data-ttu-id="58099-181">Wenn "kreatenewdescriptor" aufgerufen wird, wird das neue Schlüsselmaterial ausschließlich für diesen Aufruf erstellt, und es wird ein neuer "iauthenticatedencryptordescriptor" erstellt, der dieses Schlüsselmaterial und die algorithmischen Informationen umschließt, die zur Nutzung des Materials erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="58099-181">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="58099-182">Das Schlüsselmaterial kann in der Software erstellt werden (und im Arbeitsspeicher gespeichert), es kann erstellt und in einem HSM gespeichert werden usw.</span><span class="sxs-lookup"><span data-stu-id="58099-182">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="58099-183">Der entscheidende Punkt ist, dass bei zwei Aufrufen von "" für "" "" "" "" "" "" "" "" "" "".</span><span class="sxs-lookup"><span data-stu-id="58099-183">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="58099-184">Der iauthenticatedencryptor Configuration-Typ dient als Einstiegspunkt für Schlüssel Erstellungs Routinen wie [Automatisches Rollback von Schlüsseln](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span><span class="sxs-lookup"><span data-stu-id="58099-184">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](xref:security/data-protection/implementation/key-management#key-expiration-and-rolling).</span></span> <span data-ttu-id="58099-185">Um die Implementierung für alle zukünftigen Schlüssel zu ändern, registrieren Sie eine Singleton-iauthenticatedencryptorconfiguration im Dienst Container.</span><span class="sxs-lookup"><span data-stu-id="58099-185">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---
