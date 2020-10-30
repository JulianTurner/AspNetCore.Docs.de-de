---
title: Verschlüsselung ruhender Schlüssel in Windows und Azure mithilfe ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails ASP.net Core Verschlüsselung von Datenschutz Schlüsseln ruhender Daten.
ms.author: riande
ms.date: 07/16/2018
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061274"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a><span data-ttu-id="9c75e-103">Verschlüsselung ruhender Schlüssel in Windows und Azure mithilfe ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="9c75e-103">Key encryption at rest in Windows and Azure using ASP.NET Core</span></span>

<span data-ttu-id="9c75e-104">Das Datenschutzsystem [setzt standardmäßig einen Ermittlungs Mechanismus ein](xref:security/data-protection/configuration/default-settings) , um zu bestimmen, wie kryptografische Schlüssel im Ruhezustand verschlüsselt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9c75e-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine how cryptographic keys should be encrypted at rest.</span></span> <span data-ttu-id="9c75e-105">Der Entwickler kann den Ermittlungs Mechanismus überschreiben und manuell angeben, wie Schlüssel im Ruhezustand verschlüsselt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="9c75e-105">The developer can override the discovery mechanism and manually specify how keys should be encrypted at rest.</span></span>

> [!WARNING]
> <span data-ttu-id="9c75e-106">Wenn Sie einen expliziten [Speicherort für die Schlüssel Persistenz](xref:security/data-protection/implementation/key-storage-providers)angeben, hebt das Datenschutzsystem die Standardverschlüsselung im Ruhezustand auf.</span><span class="sxs-lookup"><span data-stu-id="9c75e-106">If you specify an explicit [key persistence location](xref:security/data-protection/implementation/key-storage-providers), the data protection system deregisters the default key encryption at rest mechanism.</span></span> <span data-ttu-id="9c75e-107">Folglich werden Schlüssel im Ruhezustand nicht mehr verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="9c75e-107">Consequently, keys are no longer encrypted at rest.</span></span> <span data-ttu-id="9c75e-108">Es wird empfohlen, dass Sie [einen expliziten Schlüssel Verschlüsselungsmechanismus](xref:security/data-protection/implementation/key-encryption-at-rest) für Produktions Bereitstellungen angeben.</span><span class="sxs-lookup"><span data-stu-id="9c75e-108">We recommend that you [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span> <span data-ttu-id="9c75e-109">Die Optionen für die Verschlüsselung ruhender Mechanismen werden in diesem Thema beschrieben.</span><span class="sxs-lookup"><span data-stu-id="9c75e-109">The encryption-at-rest mechanism options are described in this topic.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a><span data-ttu-id="9c75e-110">Azure-Schlüsseltresor</span><span class="sxs-lookup"><span data-stu-id="9c75e-110">Azure Key Vault</span></span>

<span data-ttu-id="9c75e-111">Um Schlüssel in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)zu speichern, konfigurieren Sie das System mit [protectkeyswithazurekeyvault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in der- `Startup` Klasse:</span><span class="sxs-lookup"><span data-stu-id="9c75e-111">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

<span data-ttu-id="9c75e-112">Weitere Informationen finden Sie unter [Konfigurieren von ASP.net Core Datenschutz: protectkeyswithazurekeyvault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span><span class="sxs-lookup"><span data-stu-id="9c75e-112">For more information, see [Configure ASP.NET Core Data Protection: ProtectKeysWithAzureKeyVault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).</span></span>

::: moniker-end

## <a name="windows-dpapi"></a><span data-ttu-id="9c75e-113">Windows-DPAPI</span><span class="sxs-lookup"><span data-stu-id="9c75e-113">Windows DPAPI</span></span>

<span data-ttu-id="9c75e-114">**Gilt nur für Windows-bereit Stellungen.**</span><span class="sxs-lookup"><span data-stu-id="9c75e-114">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="9c75e-115">Bei Verwendung von Windows DPAPI wird das Schlüsselmaterial mit " [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) " verschlüsselt, bevor es im Speicher gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="9c75e-115">When Windows DPAPI is used, key material is encrypted with [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) before being persisted to storage.</span></span> <span data-ttu-id="9c75e-116">DPAPI ist ein geeigneter Verschlüsselungsmechanismus für Daten, die nie außerhalb des aktuellen Computers gelesen werden (obwohl es möglich ist, diese Schlüssel bis Active Directory zu sichern. Weitere Informationen finden Sie unter [DPAPI und Roamingprofile](https://support.microsoft.com/kb/309408/#6)).</span><span class="sxs-lookup"><span data-stu-id="9c75e-116">DPAPI is an appropriate encryption mechanism for data that's never read outside of the current machine (though it's possible to back these keys up to Active Directory; see [DPAPI and Roaming Profiles](https://support.microsoft.com/kb/309408/#6)).</span></span> <span data-ttu-id="9c75e-117">Um die Verschlüsselung für den DPAPI-Schlüssel für die Verschlüsselung zu konfigurieren, wenden Sie eine der [protectkeyswithdpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) -Erweiterungs Methoden an:</span><span class="sxs-lookup"><span data-stu-id="9c75e-117">To configure DPAPI key-at-rest encryption, call one of the [ProtectKeysWithDpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) extension methods:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

<span data-ttu-id="9c75e-118">Wenn `ProtectKeysWithDpapi` ohne Parameter aufgerufen wird, kann nur das aktuelle Windows-Benutzerkonto den persistenten Schlüsselring entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="9c75e-118">If `ProtectKeysWithDpapi` is called with no parameters, only the current Windows user account can decipher the persisted key ring.</span></span> <span data-ttu-id="9c75e-119">Optional können Sie angeben, dass jedes Benutzerkonto auf dem Computer (nicht nur das aktuelle Benutzerkonto) in der Lage sein soll, den Schlüsselring zu entschlüsseln:</span><span class="sxs-lookup"><span data-stu-id="9c75e-119">You can optionally specify that any user account on the machine (not just the current user account) be able to decipher the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a><span data-ttu-id="9c75e-120">X.509-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="9c75e-120">X.509 certificate</span></span>

<span data-ttu-id="9c75e-121">Wenn die APP auf mehrere Computer verteilt ist, ist es möglicherweise bequem, ein frei gegebenes X. 509-Zertifikat auf den Computern zu verteilen und die gehosteten apps so zu konfigurieren, dass das Zertifikat für die Verschlüsselung der Schlüssel im Ruhezustand verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="9c75e-121">If the app is spread across multiple machines, it may be convenient to distribute a shared X.509 certificate across the machines and configure the hosted apps to use the certificate for encryption of keys at rest:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

<span data-ttu-id="9c75e-122">Aufgrund .NET Framework Einschränkungen werden nur Zertifikate mit privaten CAPI-Schlüsseln unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9c75e-122">Due to .NET Framework limitations, only certificates with CAPI private keys are supported.</span></span> <span data-ttu-id="9c75e-123">In den folgenden Inhalten finden Sie mögliche Problem Umgehungen für diese Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="9c75e-123">See the content below for possible workarounds to these limitations.</span></span>

::: moniker-end

## <a name="windows-dpapi-ng"></a><span data-ttu-id="9c75e-124">Windows-DPAPI-ng</span><span class="sxs-lookup"><span data-stu-id="9c75e-124">Windows DPAPI-NG</span></span>

<span data-ttu-id="9c75e-125">**Dieser Mechanismus ist nur unter Windows 8/Windows Server 2012 oder höher verfügbar.**</span><span class="sxs-lookup"><span data-stu-id="9c75e-125">**This mechanism is available only on Windows 8/Windows Server 2012 or later.**</span></span>

<span data-ttu-id="9c75e-126">Ab Windows 8 unterstützt das Windows-Betriebssystem DPAPI-ng (auch CNG DPAPI genannt).</span><span class="sxs-lookup"><span data-stu-id="9c75e-126">Beginning with Windows 8, Windows OS supports DPAPI-NG (also called CNG DPAPI).</span></span> <span data-ttu-id="9c75e-127">Weitere Informationen finden Sie unter Informationen [zu CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span><span class="sxs-lookup"><span data-stu-id="9c75e-127">For more information, see [About CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).</span></span>

<span data-ttu-id="9c75e-128">Der Prinzipal wird als Schutz beschreibungsregel codiert.</span><span class="sxs-lookup"><span data-stu-id="9c75e-128">The principal is encoded as a protection descriptor rule.</span></span> <span data-ttu-id="9c75e-129">Im folgenden Beispiel, in dem [protectkeyswithdpaping](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)aufgerufen wird, kann nur der in die Domäne eingebundenen Benutzer mit der angegebenen SID den Schlüsselbund entschlüsseln:</span><span class="sxs-lookup"><span data-stu-id="9c75e-129">In the following example that calls [ProtectKeysWithDpapiNG](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping), only the domain-joined user with the specified SID can decrypt the key ring:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="9c75e-130">Es gibt auch eine parameterlose Überladung von `ProtectKeysWithDpapiNG` .</span><span class="sxs-lookup"><span data-stu-id="9c75e-130">There's also a parameterless overload of `ProtectKeysWithDpapiNG`.</span></span> <span data-ttu-id="9c75e-131">Verwenden Sie diese Hilfsmethode, um die Regel "sid = {CURRENT_ACCOUNT_SID}" anzugeben, wobei *CURRENT_ACCOUNT_SID* die SID des aktuellen Windows-Benutzerkontos ist:</span><span class="sxs-lookup"><span data-stu-id="9c75e-131">Use this convenience method to specify the rule "SID={CURRENT_ACCOUNT_SID}", where *CURRENT_ACCOUNT_SID* is the SID of the current Windows user account:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

<span data-ttu-id="9c75e-132">In diesem Szenario ist der AD-Domänen Controller für die Verteilung der Verschlüsselungsschlüssel verantwortlich, die von den DPAPI-ng-Vorgängen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9c75e-132">In this scenario, the AD domain controller is responsible for distributing the encryption keys used by the DPAPI-NG operations.</span></span> <span data-ttu-id="9c75e-133">Der Ziel Benutzer kann die verschlüsselte Nutzlast von einem beliebigen in eine Domäne eingebundenen Computer entschlüsseln (vorausgesetzt, der Prozess wird unter seiner Identität ausgeführt).</span><span class="sxs-lookup"><span data-stu-id="9c75e-133">The target user can decipher the encrypted payload from any domain-joined machine (provided that the process is running under their identity).</span></span>

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a><span data-ttu-id="9c75e-134">Zertifikat basierte Verschlüsselung mit Windows DPAPI-ng</span><span class="sxs-lookup"><span data-stu-id="9c75e-134">Certificate-based encryption with Windows DPAPI-NG</span></span>

<span data-ttu-id="9c75e-135">Wenn die APP unter Windows 8.1/Windows Server 2012 R2 oder höher ausgeführt wird, können Sie Windows DPAPI-ng verwenden, um eine Zertifikat basierte Verschlüsselung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="9c75e-135">If the app is running on Windows 8.1/Windows Server 2012 R2 or later, you can use Windows DPAPI-NG to perform certificate-based encryption.</span></span> <span data-ttu-id="9c75e-136">Verwenden Sie die Regel Deskriptorzeichenfolge "Certificate = Hashid: Fingerabdruck", wobei " *Fingerabdruck* " der hexadezimal codierte SHA1-Fingerabdruck des Zertifikats ist:</span><span class="sxs-lookup"><span data-stu-id="9c75e-136">Use the rule descriptor string "CERTIFICATE=HashId:THUMBPRINT", where *THUMBPRINT* is the hex-encoded SHA1 thumbprint of the certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

<span data-ttu-id="9c75e-137">Jede APP, die auf dieses Repository verweist, muss auf Windows 8.1/Windows Server 2012 R2 oder höher ausgeführt werden, um die Schlüssel zu entschlüsseln.</span><span class="sxs-lookup"><span data-stu-id="9c75e-137">Any app pointed at this repository must be running on Windows 8.1/Windows Server 2012 R2 or later to decipher the keys.</span></span>

## <a name="custom-key-encryption"></a><span data-ttu-id="9c75e-138">Verschlüsselung von benutzerdefinierten Schlüsseln</span><span class="sxs-lookup"><span data-stu-id="9c75e-138">Custom key encryption</span></span>

<span data-ttu-id="9c75e-139">Wenn die in-Box-Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Schlüssel Verschlüsselungsmechanismus angeben, indem er einen benutzerdefinierten [ixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="9c75e-139">If the in-box mechanisms aren't appropriate, the developer can specify their own key encryption mechanism by providing a custom [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor).</span></span>
