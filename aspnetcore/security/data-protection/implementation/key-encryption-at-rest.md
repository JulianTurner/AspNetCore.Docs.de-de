---
title: Verschlüsselung ruhender Schlüssel in Windows und Azure mithilfe ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails ASP.net Core Verschlüsselung von Datenschutz Schlüsseln ruhender Daten.
ms.author: riande
ms.date: 07/16/2018
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
uid: security/data-protection/implementation/key-encryption-at-rest
ms.openlocfilehash: e2841278e8262e6dbbfcf172e0b6599bb19f1d6c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061274"
---
# <a name="key-encryption-at-rest-in-windows-and-azure-using-aspnet-core"></a>Verschlüsselung ruhender Schlüssel in Windows und Azure mithilfe ASP.net Core

Das Datenschutzsystem [setzt standardmäßig einen Ermittlungs Mechanismus ein](xref:security/data-protection/configuration/default-settings) , um zu bestimmen, wie kryptografische Schlüssel im Ruhezustand verschlüsselt werden sollen. Der Entwickler kann den Ermittlungs Mechanismus überschreiben und manuell angeben, wie Schlüssel im Ruhezustand verschlüsselt werden sollen.

> [!WARNING]
> Wenn Sie einen expliziten [Speicherort für die Schlüssel Persistenz](xref:security/data-protection/implementation/key-storage-providers)angeben, hebt das Datenschutzsystem die Standardverschlüsselung im Ruhezustand auf. Folglich werden Schlüssel im Ruhezustand nicht mehr verschlüsselt. Es wird empfohlen, dass Sie [einen expliziten Schlüssel Verschlüsselungsmechanismus](xref:security/data-protection/implementation/key-encryption-at-rest) für Produktions Bereitstellungen angeben. Die Optionen für die Verschlüsselung ruhender Mechanismen werden in diesem Thema beschrieben.

::: moniker range=">= aspnetcore-2.1"

## <a name="azure-key-vault"></a>Azure-Schlüsseltresor

Um Schlüssel in [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)zu speichern, konfigurieren Sie das System mit [protectkeyswithazurekeyvault](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.protectkeyswithazurekeyvault) in der- `Startup` Klasse:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blobUriWithSasToken>"))
        .ProtectKeysWithAzureKeyVault("<keyIdentifier>", "<clientId>", "<clientSecret>");
}
```

Weitere Informationen finden Sie unter [Konfigurieren von ASP.net Core Datenschutz: protectkeyswithazurekeyvault](xref:security/data-protection/configuration/overview#protectkeyswithazurekeyvault).

::: moniker-end

## <a name="windows-dpapi"></a>Windows-DPAPI

**Gilt nur für Windows-bereit Stellungen.**

Bei Verwendung von Windows DPAPI wird das Schlüsselmaterial mit " [CryptProtectData](/windows/desktop/api/dpapi/nf-dpapi-cryptprotectdata) " verschlüsselt, bevor es im Speicher gespeichert wird. DPAPI ist ein geeigneter Verschlüsselungsmechanismus für Daten, die nie außerhalb des aktuellen Computers gelesen werden (obwohl es möglich ist, diese Schlüssel bis Active Directory zu sichern. Weitere Informationen finden Sie unter [DPAPI und Roamingprofile](https://support.microsoft.com/kb/309408/#6)). Um die Verschlüsselung für den DPAPI-Schlüssel für die Verschlüsselung zu konfigurieren, wenden Sie eine der [protectkeyswithdpapi](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpapi) -Erweiterungs Methoden an:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Only the local user account can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi();
}
```

Wenn `ProtectKeysWithDpapi` ohne Parameter aufgerufen wird, kann nur das aktuelle Windows-Benutzerkonto den persistenten Schlüsselring entschlüsseln. Optional können Sie angeben, dass jedes Benutzerkonto auf dem Computer (nicht nur das aktuelle Benutzerkonto) in der Lage sein soll, den Schlüsselring zu entschlüsseln:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // All user accounts on the machine can decrypt the keys
    services.AddDataProtection()
        .ProtectKeysWithDpapi(protectToLocalMachine: true);
}
```

::: moniker range=">= aspnetcore-2.0"

## <a name="x509-certificate"></a>X.509-Zertifikat

Wenn die APP auf mehrere Computer verteilt ist, ist es möglicherweise bequem, ein frei gegebenes X. 509-Zertifikat auf den Computern zu verteilen und die gehosteten apps so zu konfigurieren, dass das Zertifikat für die Verschlüsselung der Schlüssel im Ruhezustand verwendet wird:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithCertificate("3BCE558E2AD3E0E34A7743EAB5AEA2A9BD2575A0");
}
```

Aufgrund .NET Framework Einschränkungen werden nur Zertifikate mit privaten CAPI-Schlüsseln unterstützt. In den folgenden Inhalten finden Sie mögliche Problem Umgehungen für diese Einschränkungen.

::: moniker-end

## <a name="windows-dpapi-ng"></a>Windows-DPAPI-ng

**Dieser Mechanismus ist nur unter Windows 8/Windows Server 2012 oder höher verfügbar.**

Ab Windows 8 unterstützt das Windows-Betriebssystem DPAPI-ng (auch CNG DPAPI genannt). Weitere Informationen finden Sie unter Informationen [zu CNG DPAPI](/windows/desktop/SecCNG/cng-dpapi).

Der Prinzipal wird als Schutz beschreibungsregel codiert. Im folgenden Beispiel, in dem [protectkeyswithdpaping](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.protectkeyswithdpaping)aufgerufen wird, kann nur der in die Domäne eingebundenen Benutzer mit der angegebenen SID den Schlüsselbund entschlüsseln:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Uses the descriptor rule "SID=S-1-5-21-..."
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("SID=S-1-5-21-...",
        flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Es gibt auch eine parameterlose Überladung von `ProtectKeysWithDpapiNG` . Verwenden Sie diese Hilfsmethode, um die Regel "sid = {CURRENT_ACCOUNT_SID}" anzugeben, wobei *CURRENT_ACCOUNT_SID* die SID des aktuellen Windows-Benutzerkontos ist:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Use the descriptor rule "SID={current account SID}"
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG();
}
```

In diesem Szenario ist der AD-Domänen Controller für die Verteilung der Verschlüsselungsschlüssel verantwortlich, die von den DPAPI-ng-Vorgängen verwendet werden. Der Ziel Benutzer kann die verschlüsselte Nutzlast von einem beliebigen in eine Domäne eingebundenen Computer entschlüsseln (vorausgesetzt, der Prozess wird unter seiner Identität ausgeführt).

## <a name="certificate-based-encryption-with-windows-dpapi-ng"></a>Zertifikat basierte Verschlüsselung mit Windows DPAPI-ng

Wenn die APP unter Windows 8.1/Windows Server 2012 R2 oder höher ausgeführt wird, können Sie Windows DPAPI-ng verwenden, um eine Zertifikat basierte Verschlüsselung auszuführen. Verwenden Sie die Regel Deskriptorzeichenfolge "Certificate = Hashid: Fingerabdruck", wobei " *Fingerabdruck* " der hexadezimal codierte SHA1-Fingerabdruck des Zertifikats ist:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .ProtectKeysWithDpapiNG("CERTIFICATE=HashId:3BCE558E2...B5AEA2A9BD2575A0",
            flags: DpapiNGProtectionDescriptorFlags.None);
}
```

Jede APP, die auf dieses Repository verweist, muss auf Windows 8.1/Windows Server 2012 R2 oder höher ausgeführt werden, um die Schlüssel zu entschlüsseln.

## <a name="custom-key-encryption"></a>Verschlüsselung von benutzerdefinierten Schlüsseln

Wenn die in-Box-Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Schlüssel Verschlüsselungsmechanismus angeben, indem er einen benutzerdefinierten [ixmlencryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmlencryptor)bereitstellt.
