---
title: Konfigurieren des Schutzes von Daten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in ASP.net Core konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: security/data-protection/configuration/overview
ms.openlocfilehash: 048f6d6d57d3cc5d64004e18b18a3347ee92e450
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234568"
---
# <a name="configure-aspnet-core-data-protection"></a><span data-ttu-id="1a8ec-103">Konfigurieren des Schutzes von Daten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1a8ec-103">Configure ASP.NET Core Data Protection</span></span>

<span data-ttu-id="1a8ec-104">Wenn das Datenschutzsystem initialisiert ist, werden die [Standardeinstellungen](xref:security/data-protection/configuration/default-settings) basierend auf der Betriebsumgebung angewendet.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-104">When the Data Protection system is initialized, it applies [default settings](xref:security/data-protection/configuration/default-settings) based on the operational environment.</span></span> <span data-ttu-id="1a8ec-105">Diese Einstellungen eignen sich in der Regel für apps, die auf einem einzelnen Computer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-105">These settings are generally appropriate for apps running on a single machine.</span></span> <span data-ttu-id="1a8ec-106">Es gibt Fälle, in denen Entwickler möglicherweise die Standardeinstellungen ändern möchten:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-106">There are cases where a developer may want to change the default settings:</span></span>

* <span data-ttu-id="1a8ec-107">Die APP ist auf mehrere Computer verteilt.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-107">The app is spread across multiple machines.</span></span>
* <span data-ttu-id="1a8ec-108">Aus Kompatibilitätsgründen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-108">For compliance reasons.</span></span>

<span data-ttu-id="1a8ec-109">In diesen Szenarien bietet das Datenschutzsystem eine umfangreiche Konfigurations-API.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-109">For these scenarios, the Data Protection system offers a rich configuration API.</span></span>

> [!WARNING]
> <span data-ttu-id="1a8ec-110">Ähnlich wie Konfigurationsdateien sollte der Schlüsselbund für den Datenschutz mit den entsprechenden Berechtigungen geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-110">Similar to configuration files, the data protection key ring should be protected using appropriate permissions.</span></span> <span data-ttu-id="1a8ec-111">Sie können auswählen, dass Schlüssel im Ruhezustand verschlüsselt werden, aber dies hindert Angreifer daran, keine neuen Schlüssel zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-111">You can choose to encrypt keys at rest, but this doesn't prevent attackers from creating new keys.</span></span> <span data-ttu-id="1a8ec-112">Folglich ist die Sicherheit Ihrer APP beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-112">Consequently, your app's security is impacted.</span></span> <span data-ttu-id="1a8ec-113">Der mit dem Schutz von Daten konfigurierte Speicherort muss auf die APP selbst beschränkt sein, ähnlich wie beim Schutz von Konfigurationsdateien.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-113">The storage location configured with Data Protection should have its access limited to the app itself, similar to the way you would protect configuration files.</span></span> <span data-ttu-id="1a8ec-114">Wenn Sie z. b. den Schlüsselbund auf einem Datenträger speichern möchten, verwenden Sie die Dateisystem Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-114">For example, if you choose to store your key ring on disk, use file system permissions.</span></span> <span data-ttu-id="1a8ec-115">Stellen Sie sicher, dass nur die Identität, unter der Ihre Web-App ausgeführt wird, Lese-und Schreibzugriff auf dieses Verzeichnis hat.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-115">Ensure only the identity under which your web app runs has read, write, and create access to that directory.</span></span> <span data-ttu-id="1a8ec-116">Wenn Sie Azure BLOB Storage verwenden, sollte nur die Web-App in der Lage sein, neue Einträge im BLOB-Speicher zu lesen, zu schreiben oder zu erstellen, usw.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-116">If you use Azure Blob Storage, only the web app should have the ability to read, write, or create new entries in the blob store, etc.</span></span>
>
> <span data-ttu-id="1a8ec-117">Die Erweiterungsmethode [adddataprotection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) gibt ein [idataschutzbuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder)-Element zurück.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-117">The extension method [AddDataProtection](/dotnet/api/microsoft.extensions.dependencyinjection.dataprotectionservicecollectionextensions.adddataprotection) returns an [IDataProtectionBuilder](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotectionbuilder).</span></span> <span data-ttu-id="1a8ec-118">`IDataProtectionBuilder` macht Erweiterungs Methoden verfügbar, die Sie verbinden können, um Datenschutzoptionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-118">`IDataProtectionBuilder` exposes extension methods that you can chain together to configure Data Protection options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1a8ec-119">Die folgenden nuget-Pakete sind für die Datenschutz Erweiterungen erforderlich, die in diesem Artikel verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-119">The following NuGet packages are required for the Data Protection extensions used in this article:</span></span>

* [<span data-ttu-id="1a8ec-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span><span class="sxs-lookup"><span data-stu-id="1a8ec-120">Azure.Extensions.AspNetCore.DataProtection.Blobs</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs)
* [<span data-ttu-id="1a8ec-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span><span class="sxs-lookup"><span data-stu-id="1a8ec-121">Azure.Extensions.AspNetCore.DataProtection.Keys</span></span>](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys)

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="protectkeyswithazurekeyvault"></a><span data-ttu-id="1a8ec-122">Protectkeyswithazurekeyvault</span><span class="sxs-lookup"><span data-stu-id="1a8ec-122">ProtectKeysWithAzureKeyVault</span></span>

<span data-ttu-id="1a8ec-123">Melden Sie sich bei Azure mithilfe der CLI an, z. b.:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-123">Log in to Azure using the CLI, for example:</span></span>

```azurecli
az login
``` 

<span data-ttu-id="1a8ec-124">Um Schlüssel in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)zu speichern, konfigurieren Sie das System mit [protectkeyswithazurekeyvault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in der- `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-124">To store keys in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), configure the system with [ProtectKeysWithAzureKeyVault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in the `Startup` class.</span></span> <span data-ttu-id="1a8ec-125">`blobUriWithSasToken` der vollständige URI, unter dem die Schlüsseldatei gespeichert werden soll.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-125">`blobUriWithSasToken` is the full URI where the key file should be stored.</span></span> <span data-ttu-id="1a8ec-126">Der URI muss das SAS-Token als Abfrage Zeichenfolgen-Parameter enthalten:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-126">The URI must contain the SAS token as a query string parameter:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
}
```

<span data-ttu-id="1a8ec-127">Legen Sie den Speicherort des Schlüsselrings fest (z. [b. persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span><span class="sxs-lookup"><span data-stu-id="1a8ec-127">Set the key ring storage location (for example, [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage)).</span></span> <span data-ttu-id="1a8ec-128">Der Speicherort muss festgelegt werden, da der Aufruf von `ProtectKeysWithAzureKeyVault` einen [ixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) implementiert, der die Einstellungen für den automatischen Datenschutz deaktiviert, einschließlich des Speicher Orts für den Schlüsselring.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-128">The location must be set because calling `ProtectKeysWithAzureKeyVault` implements an [IXmlEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor) that disables automatic data protection settings, including the key ring storage location.</span></span> <span data-ttu-id="1a8ec-129">Im vorangehenden Beispiel wird Azure BLOB Storage verwendet, um den Schlüsselring beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-129">The preceding example uses Azure Blob Storage to persist the key ring.</span></span> <span data-ttu-id="1a8ec-130">Weitere Informationen finden Sie unter [Schlüsselspeicher Anbieter: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="1a8ec-130">For more information, see [Key storage providers: Azure Storage](xref:security/data-protection/implementation/key-storage-providers#azure-storage).</span></span> <span data-ttu-id="1a8ec-131">Sie können den Schlüsselring auch lokal mit [persistkeystofile System](xref:security/data-protection/implementation/key-storage-providers#file-system)speichern.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-131">You can also persist the key ring locally with [PersistKeysToFileSystem](xref:security/data-protection/implementation/key-storage-providers#file-system).</span></span>

<span data-ttu-id="1a8ec-132">Der `keyIdentifier` ist der Key Vault-Schlüssel Bezeichner, der für die Schlüssel Verschlüsselung verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-132">The `keyIdentifier` is the key vault key identifier used for key encryption.</span></span> <span data-ttu-id="1a8ec-133">Ein Schlüssel, der in Key Vault namens erstellt wurde, hat beispielsweise `dataprotection` `contosokeyvault` den Schlüssel Bezeichner `https://contosokeyvault.vault.azure.net/keys/dataprotection/` .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-133">For example, a key created in key vault named `dataprotection` in the `contosokeyvault` has the key identifier `https://contosokeyvault.vault.azure.net/keys/dataprotection/`.</span></span> <span data-ttu-id="1a8ec-134">Stellen Sie für die APP die Berechtigungen **Entpacken** **und Schlüssel einschließen** für den Schlüssel Tresor bereit.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-134">Provide the app with **Unwrap Key** and **Wrap Key** permissions to the key vault.</span></span>

<span data-ttu-id="1a8ec-135">`ProtectKeysWithAzureKeyVault` über Ladungen</span><span class="sxs-lookup"><span data-stu-id="1a8ec-135">`ProtectKeysWithAzureKeyVault` overloads:</span></span>

* <span data-ttu-id="1a8ec-136">[Protectkeyswithazurekeyvault (idataschutzbuilder, Uri, tokencredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) ermöglicht die Verwendung eines KeyIdentifier-URIs und von tokencredential, damit das Datenschutzsystem den Schlüssel Tresor verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-136">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, Uri, TokenCredential)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier Uri and a tokenCredential to enable the data protection system to use the key vault.</span></span>
* <span data-ttu-id="1a8ec-137">[Protectkeyswithazurekeyvault (idataschutzbuilder, String, ikeyverschlüsseltionkeyresolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) ermöglicht die Verwendung einer KeyIdentifier-Zeichenfolge und von ikeyverschlüsseltionkeyresolver, damit das Datenschutzsystem den Schlüssel Tresor verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-137">[ProtectKeysWithAzureKeyVault(IDataProtectionBuilder, string, IKeyEncryptionKeyResolver)](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionkeyvaultkeybuilderextensions.protectkeyswithazurekeyvault#Microsoft_AspNetCore_DataProtection_AzureDataProtectionKeyVaultKeyBuilderExtensions_ProtectKeysWithAzureKeyVault_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_Uri_Azure_Core_TokenCredential_) permits the use of a keyIdentifier string and IKeyEncryptionKeyResolver to enable the data protection system to use the key vault.</span></span>

<span data-ttu-id="1a8ec-138">Wenn die APP die früheren Azure-Pakete ( [`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) und [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault) ) sowie eine Kombination aus Azure Key Vault und Azure Storage zum Speichern und schützen von Schlüsseln verwendet, wird ausgelöst, <xref:System.UriFormatException?displayProperty=nameWithType> Wenn das BLOB für den Schlüsselspeicher nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-138">If the app uses the prior Azure packages ([`Microsoft.AspNetCore.DataProtection.AzureStorage`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage) and [`Microsoft.AspNetCore.DataProtection.AzureKeyVault`](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureKeyVault)) and a combination of Azure Key Vault and Azure Storage to store and protect keys, <xref:System.UriFormatException?displayProperty=nameWithType> is thrown if the blob for key storage doesn't exist.</span></span> <span data-ttu-id="1a8ec-139">Das BLOB kann vor dem Ausführen der APP im Azure-Portal manuell erstellt werden, oder Sie können das folgende Verfahren verwenden:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-139">The blob can be manually created ahead of running the app in the Azure portal, or use the following procedure:</span></span>

1. <span data-ttu-id="1a8ec-140">Entfernen Sie den-Befehl `ProtectKeysWithAzureKeyVault` für die erste Testlauf, um das BLOB zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-140">Remove the call to `ProtectKeysWithAzureKeyVault` for the first run to create the blob in place.</span></span>
1. <span data-ttu-id="1a8ec-141">Fügen Sie den-Rückruf `ProtectKeysWithAzureKeyVault` für nachfolgende Ausführungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-141">Add the call to `ProtectKeysWithAzureKeyVault` for subsequent runs.</span></span>

<span data-ttu-id="1a8ec-142">Das Entfernen der `ProtectKeysWithAzureKeyVault` ersten Testlauf wird empfohlen, da dadurch sichergestellt wird, dass die Datei mit dem richtigen Schema und den richtigen Werten erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-142">Removing `ProtectKeysWithAzureKeyVault` for the first run is advised, as it ensures that the file is created with the proper schema and values in place.</span></span> 

<span data-ttu-id="1a8ec-143">Es wird empfohlen, ein Upgrade auf die Pakete [Azure. Extensions. aspnetcore. dataprotection. BLOBs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) und [Azure. Extensions. aspnetcore. dataprotection. Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) durchzuführen, da die bereitgestellte API automatisch das BLOB erstellt, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-143">We recommended upgrading to the [Azure.Extensions.AspNetCore.DataProtection.Blobs](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Blobs) and [Azure.Extensions.AspNetCore.DataProtection.Keys](https://www.nuget.org/packages/Azure.Extensions.AspNetCore.DataProtection.Keys) packages because the API provided automatically creates the blob if it doesn't exist.</span></span>

```csharp
services.AddDataProtection()
    //This blob must already exist before the application is run
    .PersistKeysToAzureBlobStorage("<storage account connection string", "<key store container name>", "<key store blob name>")
    //Removing this line below for an initial run will ensure the file is created correctly
    .ProtectKeysWithAzureKeyVault(new Uri("<keyIdentifier>"), new DefaultAzureCredential());
```

::: moniker-end

## <a name="persistkeystofilesystem"></a><span data-ttu-id="1a8ec-144">Persistkeystofile System</span><span class="sxs-lookup"><span data-stu-id="1a8ec-144">PersistKeysToFileSystem</span></span>

<span data-ttu-id="1a8ec-145">Zum Speichern von Schlüsseln auf einer UNC-Freigabe anstelle von *% LocalAppData%* Standard Speicherort konfigurieren Sie das System mit [persistkeystofile](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem)System:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-145">To store keys on a UNC share instead of at the *%LOCALAPPDATA%* default location, configure the system with [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"));
}
```

> [!WARNING]
> <span data-ttu-id="1a8ec-146">Wenn Sie den Speicherort für die Schlüssel Persistenz ändern, verschlüsselt das System die Schlüssel im Ruhezustand nicht mehr automatisch, da nicht bekannt ist, ob DPAPI ein geeigneter Verschlüsselungsmechanismus ist.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-146">If you change the key persistence location, the system no longer automatically encrypts keys at rest, since it doesn't know whether DPAPI is an appropriate encryption mechanism.</span></span>

## <a name="protectkeyswith"></a><span data-ttu-id="1a8ec-147">Protectkeyswith\*</span><span class="sxs-lookup"><span data-stu-id="1a8ec-147">ProtectKeysWith\*</span></span>

<span data-ttu-id="1a8ec-148">Sie können das System konfigurieren, um Schlüssel im Ruhezustand zu schützen, indem Sie eine der [protectkeyswith \* ](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) -Konfigurations-APIs aufrufen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-148">You can configure the system to protect keys at rest by calling any of the [ProtectKeysWith\*](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions) configuration APIs.</span></span> <span data-ttu-id="1a8ec-149">Betrachten Sie das folgende Beispiel, in dem Schlüssel auf einer UNC-Freigabe gespeichert werden und die Schlüssel im Ruhezustand mit einem bestimmten X. 509-Zertifikat verschlüsselt werden:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-149">Consider the example below, which stores keys on a UNC share and encrypts those keys at rest with a specific X.509 certificate:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate("thumbprint");
}
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="1a8ec-150">In ASP.net Core 2,1 oder höher können Sie eine [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) für [protectkeyswithcertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate)bereitstellen, z. b. ein Zertifikat, das aus einer Datei geladen wurde:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-150">In ASP.NET Core 2.1 or later, you can provide an [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) to [ProtectKeysWithCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithcertificate), such as a certificate loaded from a file:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
}
```

::: moniker-end

<span data-ttu-id="1a8ec-151">Weitere Beispiele und Erörterung der integrierten Schlüssel Verschlüsselungsmechanismen finden Sie unter [Verschlüsselung ruhender Schlüssel](xref:security/data-protection/implementation/key-encryption-at-rest) .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-151">See [Key Encryption At Rest](xref:security/data-protection/implementation/key-encryption-at-rest) for more examples and discussion on the built-in key encryption mechanisms.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="unprotectkeyswithanycertificate"></a><span data-ttu-id="1a8ec-152">Unprotectkeyswithanycertificate</span><span class="sxs-lookup"><span data-stu-id="1a8ec-152">UnprotectKeysWithAnyCertificate</span></span>

<span data-ttu-id="1a8ec-153">In ASP.net Core 2,1 oder höher können Sie Zertifikate rotieren und Schlüssel im Ruhezustand mit einem Array von [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -Zertifikaten mit [unprotectkeyswithanycertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate)entschlüsseln:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-153">In ASP.NET Core 2.1 or later, you can rotate certificates and decrypt keys at rest using an array of [X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) certificates with [UnprotectKeysWithAnyCertificate](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.unprotectkeyswithanycertificate):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"\\server\share\directory\"))
        .ProtectKeysWithCertificate(
            new X509Certificate2("certificate.pfx", "password"));
        .UnprotectKeysWithAnyCertificate(
            new X509Certificate2("certificate_old_1.pfx", "password_1"),
            new X509Certificate2("certificate_old_2.pfx", "password_2"));
}
```

::: moniker-end

## <a name="setdefaultkeylifetime"></a><span data-ttu-id="1a8ec-154">Setdefaultkeylifetime</span><span class="sxs-lookup"><span data-stu-id="1a8ec-154">SetDefaultKeyLifetime</span></span>

<span data-ttu-id="1a8ec-155">Verwenden Sie [setdefaultkeylifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime), um das System so zu konfigurieren, dass eine Schlüssel Lebensdauer von 14 Tagen anstelle der standardmäßigen 90 Tage verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-155">To configure the system to use a key lifetime of 14 days instead of the default 90 days, use [SetDefaultKeyLifetime](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setdefaultkeylifetime):</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
}
```

## <a name="setapplicationname"></a><span data-ttu-id="1a8ec-156">SetApplicationName</span><span class="sxs-lookup"><span data-stu-id="1a8ec-156">SetApplicationName</span></span>

<span data-ttu-id="1a8ec-157">Standardmäßig isoliert das Datenschutzsystem apps auf der Grundlage ihrer [Inhalts](xref:fundamentals/index#content-root) Stamm Pfade voneinander, auch wenn Sie das gleiche physische schlüsselrepository nutzen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-157">By default, the Data Protection system isolates apps from one another based on their [content root](xref:fundamentals/index#content-root) paths, even if they're sharing the same physical key repository.</span></span> <span data-ttu-id="1a8ec-158">Dadurch wird verhindert, dass die apps gegenseitig die geschützten Nutzlasten verstehen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-158">This prevents the apps from understanding each other's protected payloads.</span></span>

<span data-ttu-id="1a8ec-159">So geben Sie geschützte Nutzlasten für apps frei:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-159">To share protected payloads among apps:</span></span>

* <span data-ttu-id="1a8ec-160">Konfigurieren Sie <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in jeder App denselben Wert.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-160">Configure <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> in each app with the same value.</span></span>
* <span data-ttu-id="1a8ec-161">Verwenden Sie die gleiche Version des Datenschutz-API-Stapels in den apps.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-161">Use the same version of the Data Protection API stack across the apps.</span></span> <span data-ttu-id="1a8ec-162">Führen **Sie** in den Projektdateien der apps eine der folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-162">Perform **either** of the following in the apps' project files:</span></span>
  * <span data-ttu-id="1a8ec-163">Verweisen Sie über das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)" auf dieselbe freigegebene Framework-Version.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-163">Reference the same shared framework version via the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1a8ec-164">Verweisen auf die gleiche Version des [datenschutzpakets](xref:security/data-protection/introduction#package-layout) .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-164">Reference the same [Data Protection package](xref:security/data-protection/introduction#package-layout) version.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .SetApplicationName("shared app name");
}
```

## <a name="disableautomatickeygeneration"></a><span data-ttu-id="1a8ec-165">Disableautomatickeygeneration</span><span class="sxs-lookup"><span data-stu-id="1a8ec-165">DisableAutomaticKeyGeneration</span></span>

<span data-ttu-id="1a8ec-166">Möglicherweise gibt es ein Szenario, in dem eine APP nicht automatisch ein Rollback für Schlüssel durchführen soll (neue Schlüssel erstellen), wenn Sie den Ablauf erreichen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-166">You may have a scenario where you don't want an app to automatically roll keys (create new keys) as they approach expiration.</span></span> <span data-ttu-id="1a8ec-167">Ein Beispiel hierfür sind apps, die in einer primären/sekundären Beziehung eingerichtet sind, bei der nur die primäre App für wichtige Verwaltungsprobleme zuständig ist und sekundäre Apps einfach nur eine schreibgeschützte Ansicht des Schlüsselrings haben.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-167">One example of this might be apps set up in a primary/secondary relationship, where only the primary app is responsible for key management concerns and secondary apps simply have a read-only view of the key ring.</span></span> <span data-ttu-id="1a8ec-168">Die sekundären Apps können so konfiguriert werden, dass Sie den Schlüsselbund als schreibgeschützt behandeln, indem Sie das System mit konfigurieren <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*> :</span><span class="sxs-lookup"><span data-stu-id="1a8ec-168">The secondary apps can be configured to treat the key ring as read-only by configuring the system with <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.DisableAutomaticKeyGeneration*>:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .DisableAutomaticKeyGeneration();
}
```

## <a name="per-application-isolation"></a><span data-ttu-id="1a8ec-169">Isolation pro Anwendung</span><span class="sxs-lookup"><span data-stu-id="1a8ec-169">Per-application isolation</span></span>

<span data-ttu-id="1a8ec-170">Wenn das Datenschutzsystem von einem ASP.net Core Host bereitgestellt wird, werden die Apps automatisch voneinander isoliert, auch wenn diese apps unter demselben Workerprozesskonto ausgeführt werden und dasselbe Masterschlüssel Material verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-170">When the Data Protection system is provided by an ASP.NET Core host, it automatically isolates apps from one another, even if those apps are running under the same worker process account and are using the same master keying material.</span></span> <span data-ttu-id="1a8ec-171">Dies ähnelt dem IsolateApps-Modifizierer aus dem System. Web- `<machineKey>` Element.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-171">This is somewhat similar to the IsolateApps modifier from System.Web's `<machineKey>` element.</span></span>

<span data-ttu-id="1a8ec-172">Der Isolations Mechanismus funktioniert, indem jede APP auf dem lokalen Computer als eindeutiger Mandant betrachtet wird, sodass der Stamm <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> für eine bestimmte App automatisch die APP-ID als Diskriminator einschließt.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-172">The isolation mechanism works by considering each app on the local machine as a unique tenant, thus the <xref:Microsoft.AspNetCore.DataProtection.IDataProtector> rooted for any given app automatically includes the app ID as a discriminator.</span></span> <span data-ttu-id="1a8ec-173">Die eindeutige ID der APP ist der physische Pfad der APP:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-173">The app's unique ID is the app's physical path:</span></span>

* <span data-ttu-id="1a8ec-174">Bei in IIS gehosteten Apps ist die eindeutige ID der physische IIS-Pfad der app.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-174">For apps hosted in IIS, the unique ID is the IIS physical path of the app.</span></span> <span data-ttu-id="1a8ec-175">Wenn eine app in einer Webfarm Umgebung bereitgestellt wird, ist dieser wertstabil, wenn Sie davon ausgehen, dass die IIS-Umgebungen auf allen Computern in der Webfarm ähnlich konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-175">If an app is deployed in a web farm environment, this value is stable assuming that the IIS environments are configured similarly across all machines in the web farm.</span></span>
* <span data-ttu-id="1a8ec-176">Für selbst gehostete Apps, die auf dem [Kestrel-Server](xref:fundamentals/servers/index#kestrel)ausgeführt werden, ist die eindeutige ID der physische Pfad zur APP auf dem Datenträger.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-176">For self-hosted apps running on the [Kestrel server](xref:fundamentals/servers/index#kestrel), the unique ID is the physical path to the app on disk.</span></span>

<span data-ttu-id="1a8ec-177">Der eindeutige Bezeichner ist so konzipiert, dass er &mdash; die beiden einzelnen apps und des Computers selbst zurücksetzt.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-177">The unique identifier is designed to survive resets&mdash;both of the individual app and of the machine itself.</span></span>

<span data-ttu-id="1a8ec-178">Dieser Isolations Mechanismus geht davon aus, dass die apps nicht schädlich sind.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-178">This isolation mechanism assumes that the apps are not malicious.</span></span> <span data-ttu-id="1a8ec-179">Eine böswillige App kann sich immer auf jede andere APP auswirken, die unter demselben Workerprozesskonto ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-179">A malicious app can always impact any other app running under the same worker process account.</span></span> <span data-ttu-id="1a8ec-180">In einer freigegebenen Hostingumgebung, in der apps gegenseitig nicht vertrauenswürdig sind, sollte der Hostinganbieter Maßnahmen ergreifen, um die Isolation auf Betriebssystemebene zwischen apps sicherzustellen, einschließlich der Trennung der zugrunde liegenden Schlüssel Depots</span><span class="sxs-lookup"><span data-stu-id="1a8ec-180">In a shared hosting environment where apps are mutually untrusted, the hosting provider should take steps to ensure OS-level isolation between apps, including separating the apps' underlying key repositories.</span></span>

<span data-ttu-id="1a8ec-181">Wenn das Datenschutzsystem nicht von einem ASP.net Core Host bereitgestellt wird (wenn Sie z. b. über den konkreten Typ instanziieren `DataProtectionProvider` ), ist die APP-Isolation standardmäßig deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-181">If the Data Protection system isn't provided by an ASP.NET Core host (for example, if you instantiate it via the `DataProtectionProvider` concrete type) app isolation is disabled by default.</span></span> <span data-ttu-id="1a8ec-182">Wenn die APP-Isolation deaktiviert ist, können alle apps, die durch das gleiche Schlüssel Erstellungs Material unterstützt werden, Nutzlasten gemeinsam nutzen, sofern Sie die entsprechenden [Zwecke](xref:security/data-protection/consumer-apis/purpose-strings)bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-182">When app isolation is disabled, all apps backed by the same keying material can share payloads as long as they provide the appropriate [purposes](xref:security/data-protection/consumer-apis/purpose-strings).</span></span> <span data-ttu-id="1a8ec-183">Um die APP-Isolation in dieser Umgebung bereitzustellen, wenden Sie die [setapplicationname](#setapplicationname) -Methode für das Konfigurationsobjekt an, und geben Sie einen eindeutigen Namen für jede APP an.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-183">To provide app isolation in this environment, call the [SetApplicationName](#setapplicationname) method on the configuration object and provide a unique name for each app.</span></span>

## <a name="changing-algorithms-with-usecryptographicalgorithms"></a><span data-ttu-id="1a8ec-184">Ändern von Algorithmen mit usecrypumgraphicalgorithms</span><span class="sxs-lookup"><span data-stu-id="1a8ec-184">Changing algorithms with UseCryptographicAlgorithms</span></span>

<span data-ttu-id="1a8ec-185">Der Datenschutz Stapel ermöglicht es Ihnen, den von neu generierten Schlüsseln verwendeten Standard Algorithmus zu ändern.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-185">The Data Protection stack allows you to change the default algorithm used by newly-generated keys.</span></span> <span data-ttu-id="1a8ec-186">Der einfachste Weg hierfür ist das Abrufen von " [usecrypregraphicalgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) " aus dem Konfigurations Rückruf:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-186">The simplest way to do this is to call [UseCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecryptographicalgorithms) from the configuration callback:</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptorConfiguration()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .UseCryptographicAlgorithms(
        new AuthenticatedEncryptionSettings()
    {
        EncryptionAlgorithm = EncryptionAlgorithm.AES_256_CBC,
        ValidationAlgorithm = ValidationAlgorithm.HMACSHA256
    });
```

::: moniker-end

<span data-ttu-id="1a8ec-187">Der Standardverschlüsselungsalgorithmus ist AES-256-CBC, und der standardmäßige validationalgorithmus ist HMACSHA256.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-187">The default EncryptionAlgorithm is AES-256-CBC, and the default ValidationAlgorithm is HMACSHA256.</span></span> <span data-ttu-id="1a8ec-188">Die Standard Richtlinie kann von einem Systemadministrator über eine [Computer weite Richtlinie](xref:security/data-protection/configuration/machine-wide-policy)festgelegt werden, aber ein expliziter-Befehl, `UseCryptographicAlgorithms` der die Standard Richtlinie überschreibt.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-188">The default policy can be set by a system administrator via a [machine-wide policy](xref:security/data-protection/configuration/machine-wide-policy), but an explicit call to `UseCryptographicAlgorithms` overrides the default policy.</span></span>

<span data-ttu-id="1a8ec-189">`UseCryptographicAlgorithms`Durch Aufrufen von können Sie den gewünschten Algorithmus aus einer vordefinierten integrierten Liste angeben.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-189">Calling `UseCryptographicAlgorithms` allows you to specify the desired algorithm from a predefined built-in list.</span></span> <span data-ttu-id="1a8ec-190">Sie müssen sich keine Gedanken über die Implementierung des Algorithmus machen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-190">You don't need to worry about the implementation of the algorithm.</span></span> <span data-ttu-id="1a8ec-191">Im obigen Szenario versucht das Datenschutzsystem, die CNG-Implementierung von AES zu verwenden, wenn es unter Windows ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-191">In the scenario above, the Data Protection system attempts to use the CNG implementation of AES if running on Windows.</span></span> <span data-ttu-id="1a8ec-192">Andernfalls greift Sie auf die verwaltete [System. Security. Cryptography. AES](/dotnet/api/system.security.cryptography.aes) -Klasse zurück.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-192">Otherwise, it falls back to the managed [System.Security.Cryptography.Aes](/dotnet/api/system.security.cryptography.aes) class.</span></span>

<span data-ttu-id="1a8ec-193">Sie können eine Implementierung manuell über einen [callcustomcryptographicalgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms)-Befehl angeben.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-193">You can manually specify an implementation via a call to [UseCustomCryptographicAlgorithms](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.usecustomcryptographicalgorithms).</span></span>

> [!TIP]
> <span data-ttu-id="1a8ec-194">Veränderliche Algorithmen wirken sich nicht auf vorhandene Schlüssel im Schlüsselbund aus.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-194">Changing algorithms doesn't affect existing keys in the key ring.</span></span> <span data-ttu-id="1a8ec-195">Er wirkt sich nur auf neu generierte Schlüssel aus.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-195">It only affects newly-generated keys.</span></span>

### <a name="specifying-custom-managed-algorithms"></a><span data-ttu-id="1a8ec-196">Angeben von benutzerdefinierten verwalteten Algorithmen</span><span class="sxs-lookup"><span data-stu-id="1a8ec-196">Specifying custom managed algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1a8ec-197">Um benutzerdefinierte verwaltete Algorithmen anzugeben, erstellen Sie eine [managedauthenticatedencryptorconfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) -Instanz, die auf die Implementierungs Typen zeigt:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-197">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.managedauthenticatedencryptorconfiguration) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptorConfiguration()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1a8ec-198">Um benutzerdefinierte verwaltete Algorithmen anzugeben, erstellen Sie eine [managedauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) -Instanz, die auf die Implementierungs Typen zeigt:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-198">To specify custom managed algorithms, create a [ManagedAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.managedauthenticatedencryptionsettings) instance that points to the implementation types:</span></span>

```csharp
serviceCollection.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new ManagedAuthenticatedEncryptionSettings()
    {
        // A type that subclasses SymmetricAlgorithm
        EncryptionAlgorithmType = typeof(Aes),

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // A type that subclasses KeyedHashAlgorithm
        ValidationAlgorithmType = typeof(HMACSHA256)
    });
```

::: moniker-end

<span data-ttu-id="1a8ec-199">In der Regel \* müssen die Typeigenschaften auf konkrete, instanziier Bare Implementierungen (über einen öffentlichen Parameter losen ctor) von [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) und [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm)verweisen `typeof(Aes)`</span><span class="sxs-lookup"><span data-stu-id="1a8ec-199">Generally the \*Type properties must point to concrete, instantiable (via a public parameterless ctor) implementations of [SymmetricAlgorithm](/dotnet/api/system.security.cryptography.symmetricalgorithm) and [KeyedHashAlgorithm](/dotnet/api/system.security.cryptography.keyedhashalgorithm), though the system special-cases some values like `typeof(Aes)` for convenience.</span></span>

> [!NOTE]
> <span data-ttu-id="1a8ec-200">Der SymmetricAlgorithm muss eine Schlüssellänge von 128 Bits und eine Blockgröße von 64 Bits aufweisen und muss die Verschlüsselung im CBC-Modus mit PKCS #7 Padding unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-200">The SymmetricAlgorithm must have a key length of ≥ 128 bits and a block size of ≥ 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1a8ec-201">Der KeyedHashAlgorithm muss eine Hashgröße von >= 128 Bits aufweisen, und er muss Schlüssel mit einer Länge unterstützen, die der Digest-Länge des Hash Algorithmus entspricht.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-201">The KeyedHashAlgorithm must have a digest size of >= 128 bits, and it must support keys of length equal to the hash algorithm's digest length.</span></span> <span data-ttu-id="1a8ec-202">KeyedHashAlgorithm muss nicht unbedingt HMAC sein.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-202">The KeyedHashAlgorithm isn't strictly required to be HMAC.</span></span>

### <a name="specifying-custom-windows-cng-algorithms"></a><span data-ttu-id="1a8ec-203">Angeben von benutzerdefinierten Windows CNG-Algorithmen</span><span class="sxs-lookup"><span data-stu-id="1a8ec-203">Specifying custom Windows CNG algorithms</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1a8ec-204">Wenn Sie einen benutzerdefinierten Windows CNG-Algorithmus mithilfe der CBC-modusverschlüsselung mit HMAC-Überprüfung angeben möchten, erstellen Sie eine [cngcbcauthenticatedencryptor Configuration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) -Instanz, die algorithmische Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-204">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1a8ec-205">Wenn Sie einen benutzerdefinierten Windows CNG-Algorithmus mithilfe der CBC-modusverschlüsselung mit HMAC-Überprüfung angeben möchten, erstellen Sie eine [cngcbcauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) -Instanz, die die algorithmischen Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-205">To specify a custom Windows CNG algorithm using CBC-mode encryption with HMAC validation, create a [CngCbcAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cngcbcauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngCbcAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256,

        // Passed to BCryptOpenAlgorithmProvider
        HashAlgorithm = "SHA256",
        HashAlgorithmProvider = null
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1a8ec-206">Der Algorithmus für symmetrische Blockchiffre muss eine Schlüssellänge von >= 128 Bits, eine Blockgröße von >= 64 Bits aufweisen und muss die Verschlüsselung im CBC-Modus mit PKCS #7 Padding unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-206">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of >= 64 bits, and it must support CBC-mode encryption with PKCS #7 padding.</span></span> <span data-ttu-id="1a8ec-207">Der Hash Algorithmus muss eine Hashgröße von >= 128 Bits aufweisen und muss das Öffnen mit dem Flag bcrypt \_ ALG \_ handle \_ HMAC \_ Flag unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-207">The hash algorithm must have a digest size of >= 128 bits and must support being opened with the BCRYPT\_ALG\_HANDLE\_HMAC\_FLAG flag.</span></span> <span data-ttu-id="1a8ec-208">Die \* Anbieter Eigenschaften können auf NULL festgelegt werden, um den Standardanbieter für den angegebenen Algorithmus zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-208">The \*Provider properties can be set to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1a8ec-209">Weitere Informationen finden Sie in der Dokumentation zu [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-209">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="1a8ec-210">Zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus mit der Datei "Galois/Counter Mode Encryption with Validation" erstellen Sie eine [cnggcmauthenticatedencryptor Configuration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) -Instanz, die die algorithmischen Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-210">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptorConfiguration](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cnggcmauthenticatedencryptorconfiguration) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptorConfiguration()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="1a8ec-211">Zum Angeben eines benutzerdefinierten Windows CNG-Algorithmus mit der Datei "Galois/Counter Mode Encryption with Validation" erstellen Sie eine [cnggcmauthenticatedencryptionsettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) -Instanz, die die algorithmischen Informationen enthält:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-211">To specify a custom Windows CNG algorithm using Galois/Counter Mode encryption with validation, create a [CngGcmAuthenticatedEncryptionSettings](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.cnggcmauthenticatedencryptionsettings) instance that contains the algorithmic information:</span></span>

```csharp
services.AddDataProtection()
    .UseCustomCryptographicAlgorithms(
        new CngGcmAuthenticatedEncryptionSettings()
    {
        // Passed to BCryptOpenAlgorithmProvider
        EncryptionAlgorithm = "AES",
        EncryptionAlgorithmProvider = null,

        // Specified in bits
        EncryptionAlgorithmKeySize = 256
    });
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="1a8ec-212">Der Algorithmus für symmetrische Blockchiffre muss eine Schlüssellänge von >= 128 Bits, eine Blockgröße von genau 128 Bits und eine GCM-Verschlüsselung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-212">The symmetric block cipher algorithm must have a key length of >= 128 bits, a block size of exactly 128 bits, and it must support GCM encryption.</span></span> <span data-ttu-id="1a8ec-213">Sie können die Eigenschaft " [verschlüsselungalgorithmprovider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) " auf NULL festlegen, um den Standardanbieter für den angegebenen Algorithmus zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-213">You can set the [EncryptionAlgorithmProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.configurationmodel.cngcbcauthenticatedencryptorconfiguration.encryptionalgorithmprovider) property to null to use the default provider for the specified algorithm.</span></span> <span data-ttu-id="1a8ec-214">Weitere Informationen finden Sie in der Dokumentation zu [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-214">See the [BCryptOpenAlgorithmProvider](/windows/win32/api/bcrypt/nf-bcrypt-bcryptopenalgorithmprovider) documentation for more information.</span></span>

### <a name="specifying-other-custom-algorithms"></a><span data-ttu-id="1a8ec-215">Angeben anderer benutzerdefinierter Algorithmen</span><span class="sxs-lookup"><span data-stu-id="1a8ec-215">Specifying other custom algorithms</span></span>

<span data-ttu-id="1a8ec-216">Obwohl das Datenschutzsystem nicht als erstklassige API verfügbar gemacht wird, ist es erweiterbar, um eine beliebige Art von Algorithmus angeben zu können.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-216">Though not exposed as a first-class API, the Data Protection system is extensible enough to allow specifying almost any kind of algorithm.</span></span> <span data-ttu-id="1a8ec-217">Beispielsweise ist es möglich, alle Schlüssel, die in einem Hardware Sicherheitsmodul (HSM) enthalten sind, beizubehalten und eine benutzerdefinierte Implementierung der wichtigsten Verschlüsselungs-und Entschlüsselungs Routinen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-217">For example, it's possible to keep all keys contained within a Hardware Security Module (HSM) and to provide a custom implementation of the core encryption and decryption routines.</span></span> <span data-ttu-id="1a8ec-218">Weitere Informationen finden Sie unter [iauthenticatedencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in der [zentralen kryptografieerweiterbarkeit](xref:security/data-protection/extensibility/core-crypto) .</span><span class="sxs-lookup"><span data-stu-id="1a8ec-218">See [IAuthenticatedEncryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.authenticatedencryption.iauthenticatedencryptor) in [Core cryptography extensibility](xref:security/data-protection/extensibility/core-crypto) for more information.</span></span>

## <a name="persisting-keys-when-hosting-in-a-docker-container"></a><span data-ttu-id="1a8ec-219">Beibehalten von Schlüsseln beim Hosting in einem docker-Container</span><span class="sxs-lookup"><span data-stu-id="1a8ec-219">Persisting keys when hosting in a Docker container</span></span>

<span data-ttu-id="1a8ec-220">Beim Hosting in einem [docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) -Container sollten Schlüssel in folgenden Umgebungen verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="1a8ec-220">When hosting in a [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/) container, keys should be maintained in either:</span></span>

* <span data-ttu-id="1a8ec-221">Ein Ordner, bei dem es sich um ein docker-Volume handelt, das über die Lebensdauer des Containers hinaus beibehalten wird, z. b. ein frei gegebenes Volume oder ein Host</span><span class="sxs-lookup"><span data-stu-id="1a8ec-221">A folder that's a Docker volume that persists beyond the container's lifetime, such as a shared volume or a host-mounted volume.</span></span>
* <span data-ttu-id="1a8ec-222">Ein externer Anbieter, z. b. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) oder [redis](https://redis.io/).</span><span class="sxs-lookup"><span data-stu-id="1a8ec-222">An external provider, such as [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) or [Redis](https://redis.io/).</span></span>

## <a name="persisting-keys-with-redis"></a><span data-ttu-id="1a8ec-223">Beibehalten von Schlüsseln mit redis</span><span class="sxs-lookup"><span data-stu-id="1a8ec-223">Persisting keys with Redis</span></span>

<span data-ttu-id="1a8ec-224">Zum Speichern von Schlüsseln sollten nur redis-Versionen verwendet werden, die [redis-Daten Persistenz](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) unterstützen.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-224">Only Redis versions supporting [Redis Data Persistence](/azure/azure-cache-for-redis/cache-how-to-premium-persistence) should be used to store keys.</span></span> <span data-ttu-id="1a8ec-225">[Azure BLOB Storage](/azure/storage/blobs/storage-blobs-introduction) ist persistent und kann zum Speichern von Schlüsseln verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1a8ec-225">[Azure Blob storage](/azure/storage/blobs/storage-blobs-introduction) is persistent and can be used to store keys.</span></span> <span data-ttu-id="1a8ec-226">Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/13476).</span><span class="sxs-lookup"><span data-stu-id="1a8ec-226">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/13476).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1a8ec-227">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1a8ec-227">Additional resources</span></span>

* <xref:security/data-protection/configuration/non-di-scenarios>
* <xref:security/data-protection/configuration/machine-wide-policy>
* <xref:host-and-deploy/web-farm>
* <xref:security/data-protection/implementation/key-storage-providers>
