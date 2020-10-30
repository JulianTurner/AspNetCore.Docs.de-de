---
title: Schlüsselspeicher Anbieter in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die wichtigsten Speicher Anbieter in ASP.net Core und wie Sie Schlüsselspeicher Orte konfigurieren.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 36e8bc494125d0770347ddf32390365d83a91d27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051745"
---
# <a name="key-storage-providers-in-aspnet-core"></a>Schlüsselspeicher Anbieter in ASP.net Core

Das Datenschutzsystem [setzt standardmäßig einen Ermittlungs Mechanismus ein](xref:security/data-protection/configuration/default-settings) , um zu bestimmen, wo Kryptografieschlüssel persistent gespeichert werden sollen. Der Entwickler kann den Standard Ermittlungs Mechanismus überschreiben und den Speicherort manuell angeben.

> [!WARNING]
> Wenn Sie einen expliziten Speicherort für die Schlüssel Persistenz angeben, hebt das Datenschutzsystem die Standardverschlüsselung im Ruhezustand auf, sodass Schlüssel im Ruhezustand nicht mehr verschlüsselt werden. Es wird empfohlen, zusätzlich [einen expliziten Schlüssel Verschlüsselungsmechanismus](xref:security/data-protection/implementation/key-encryption-at-rest) für Produktions Bereitstellungen anzugeben.

## <a name="file-system"></a>Dateisystem

Um ein Dateisystem basiertes schlüsselrepository zu konfigurieren, müssen Sie die [persistkeystofile](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) System-Konfigurations Routine aufrufen, wie unten gezeigt. Geben Sie einen [DirectoryInfo](/dotnet/api/system.io.directoryinfo) -Verweis auf das Repository an, in dem die Schlüssel gespeichert werden sollen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

`DirectoryInfo`Kann auf ein Verzeichnis auf dem lokalen Computer zeigen, oder es kann auf einen Ordner auf einer Netzwerkfreigabe verweisen. Wenn Sie auf ein Verzeichnis auf dem lokalen Computer verweisen (und das Szenario darin besteht, dass nur apps auf dem lokalen Computer Zugriff benötigen, um dieses Repository zu verwenden), können Sie die Verwendung von [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (unter Windows) zum Verschlüsseln der Schlüssel im Ruhezustand in Erwägung gezogen. Andernfalls sollten Sie die Verwendung eines [X. 509-Zertifikats](xref:security/data-protection/implementation/key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand in Erwägung gezogen.

## <a name="azure-storage"></a>Azure Storage

Das Paket [Microsoft. aspnetcore. dataprotection. azurestorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) ermöglicht die Speicherung von Datenschutz Schlüsseln in Azure BLOB Storage. Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden. Apps können Authentifizierungs- cookie oder CSRF-Schutz auf mehreren Servern freigeben.

Zum Konfigurieren des Azure BLOB Storage Anbieters müssen Sie eine der [persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) -über Ladungen abrufen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

Wenn die Web-App als Azure-Dienst ausgeführt wird, können Authentifizierungs Token automatisch mithilfe von [Microsoft. Azure. Services. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)erstellt werden.

```csharp
var tokenProvider = new AzureServiceTokenProvider();
var token = await tokenProvider.GetAccessTokenAsync("https://storage.azure.com/");
var credentials = new StorageCredentials(new TokenCredential(token));
var storageAccount = new CloudStorageAccount(credentials, "mystorageaccount", "core.windows.net", useHttps: true);
var client = storageAccount.CreateCloudBlobClient();
var container = client.GetContainerReference("my-key-container");

// optional - provision the container automatically
await container.CreateIfNotExistsAsync();

services.AddDataProtection()
    .PersistKeysToAzureBlobStorage(container, "keys.xml");
```

[Weitere Informationen zum Konfigurieren der Dienst-zu-Dienst-Authentifizierung finden Sie unter.](/azure/key-vault/service-to-service-authentication)

## <a name="redis"></a>Redis

::: moniker range=">= aspnetcore-2.2"

Das [Microsoft. aspnetcore. dataprotection. stackexchangeredis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) -Paket ermöglicht das Speichern von Datenschutz Schlüsseln in einem redis-Cache. Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden. Apps können Authentifizierungs- cookie oder CSRF-Schutz auf mehreren Servern freigeben.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Das [Microsoft. aspnetcore. dataprotection. redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) -Paket ermöglicht das Speichern von Datenschutz Schlüsseln in einem redis-Cache. Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden. Apps können Authentifizierungs- cookie oder CSRF-Schutz auf mehreren Servern freigeben.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Rufen Sie zum Konfigurieren von redis eine der [persistkeystostackexchangeredis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) -über Ladungen auf:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToStackExchangeRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Um für redis zu konfigurieren, wenden Sie eine der [persistkeystoredis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) -über Ladungen an:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

Weitere Informationen finden Sie unter den folgenden Themen:

* [Stackexchange. redis connectionmultiplexer](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [Azure Redis Cache](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [ASP.net Core DataProtection-Beispiele](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a>Registrierung

**Gilt nur für Windows-bereit Stellungen.**

Manchmal verfügt die APP möglicherweise nicht über Schreibzugriff auf das Dateisystem. Stellen Sie sich ein Szenario vor, in dem eine App als virtuelles Dienst Konto (z. b. die Identität des App-Pools für *w3wp.exe* ) ausgeführt wird. In diesen Fällen kann der Administrator einen Registrierungsschlüssel bereitstellen, der über die Dienst Konto Identität zugänglich ist. Nennen Sie die [persistkeystoregistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) -Erweiterungsmethode, wie unten gezeigt. Geben Sie einen [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) an, der auf den Speicherort verweist, an dem kryptografische Schlüssel gespeichert werden sollen:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> Wir empfehlen die Verwendung von [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand.

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a>Entity Framework Core

Das [Microsoft. aspnetcore. dataprotection. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) -Paket bietet einen Mechanismus zum Speichern von Datenschutz Schlüsseln in einer Datenbank mithilfe von Entity Framework Core. Das `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` nuget-Paket muss der Projektdatei hinzugefügt werden. es ist nicht Teil des [Microsoft. aspnetcore. app-Metapakets](xref:fundamentals/metapackage-app).

Mit diesem Paket können Schlüssel für mehrere Instanzen einer Web-App freigegeben werden.

Zum Konfigurieren des EF Core Anbieters müssen Sie die [persistkeystodbcontext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) -Methode abrufen:

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

Der generische Parameter `TContext` muss von [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) erben und [idataschutzkeycontext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)implementieren:

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

Erstellen Sie die Tabelle `DataProtectionKeys`.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Führen Sie die folgenden Befehle im Fenster **Paket-Manager-Konsole** (PMC) aus:

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie die folgenden Befehle in einer Befehlsshell aus:

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

`MyKeysContext` ist der, der `DbContext` im vorangehenden Codebeispiel definiert wurde. Wenn Sie einen `DbContext` mit einem anderen Namen verwenden, ersetzen Sie den `DbContext` Namen durch `MyKeysContext` .

Die `DataProtectionKeys` Klasse/Entität übernimmt die Struktur, die in der folgenden Tabelle gezeigt wird.

| Eigenschaft/Feld | CLR-Typ | SQL-Typ              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | `int`, PK, `IDENTITY(1,1)` nicht NULL   |
| `FriendlyName` | `string` | `nvarchar(MAX)`, NULL |
| `Xml`          | `string` | `nvarchar(MAX)`, NULL |

::: moniker-end

## <a name="custom-key-repository"></a>Repository für benutzerdefinierte Schlüssel

Wenn die in-Box-Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Schlüssel Persistenzmechanismus angeben, indem er ein benutzerdefiniertes [ixmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)bereitstellt.
