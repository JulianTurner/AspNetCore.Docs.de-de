---
title: Schlüsselspeicher Anbieter in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die wichtigsten Speicher Anbieter in ASP.net Core und wie Sie Schlüsselspeicher Orte konfigurieren.
ms.author: riande
ms.date: 12/05/2019
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
uid: security/data-protection/implementation/key-storage-providers
ms.openlocfilehash: 36e8bc494125d0770347ddf32390365d83a91d27
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051745"
---
# <a name="key-storage-providers-in-aspnet-core"></a><span data-ttu-id="2ab28-103">Schlüsselspeicher Anbieter in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="2ab28-103">Key storage providers in ASP.NET Core</span></span>

<span data-ttu-id="2ab28-104">Das Datenschutzsystem [setzt standardmäßig einen Ermittlungs Mechanismus ein](xref:security/data-protection/configuration/default-settings) , um zu bestimmen, wo Kryptografieschlüssel persistent gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="2ab28-104">The data protection system [employs a discovery mechanism by default](xref:security/data-protection/configuration/default-settings) to determine where cryptographic keys should be persisted.</span></span> <span data-ttu-id="2ab28-105">Der Entwickler kann den Standard Ermittlungs Mechanismus überschreiben und den Speicherort manuell angeben.</span><span class="sxs-lookup"><span data-stu-id="2ab28-105">The developer can override the default discovery mechanism and manually specify the location.</span></span>

> [!WARNING]
> <span data-ttu-id="2ab28-106">Wenn Sie einen expliziten Speicherort für die Schlüssel Persistenz angeben, hebt das Datenschutzsystem die Standardverschlüsselung im Ruhezustand auf, sodass Schlüssel im Ruhezustand nicht mehr verschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-106">If you specify an explicit key persistence location, the data protection system deregisters the default key encryption at rest mechanism, so keys are no longer encrypted at rest.</span></span> <span data-ttu-id="2ab28-107">Es wird empfohlen, zusätzlich [einen expliziten Schlüssel Verschlüsselungsmechanismus](xref:security/data-protection/implementation/key-encryption-at-rest) für Produktions Bereitstellungen anzugeben.</span><span class="sxs-lookup"><span data-stu-id="2ab28-107">It's recommended that you additionally [specify an explicit key encryption mechanism](xref:security/data-protection/implementation/key-encryption-at-rest) for production deployments.</span></span>

## <a name="file-system"></a><span data-ttu-id="2ab28-108">Dateisystem</span><span class="sxs-lookup"><span data-stu-id="2ab28-108">File system</span></span>

<span data-ttu-id="2ab28-109">Um ein Dateisystem basiertes schlüsselrepository zu konfigurieren, müssen Sie die [persistkeystofile](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) System-Konfigurations Routine aufrufen, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="2ab28-109">To configure a file system-based key repository, call the [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) configuration routine as shown below.</span></span> <span data-ttu-id="2ab28-110">Geben Sie einen [DirectoryInfo](/dotnet/api/system.io.directoryinfo) -Verweis auf das Repository an, in dem die Schlüssel gespeichert werden sollen:</span><span class="sxs-lookup"><span data-stu-id="2ab28-110">Provide a [DirectoryInfo](/dotnet/api/system.io.directoryinfo) pointing to the repository where keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo(@"c:\temp-keys\"));
}
```

<span data-ttu-id="2ab28-111">`DirectoryInfo`Kann auf ein Verzeichnis auf dem lokalen Computer zeigen, oder es kann auf einen Ordner auf einer Netzwerkfreigabe verweisen.</span><span class="sxs-lookup"><span data-stu-id="2ab28-111">The `DirectoryInfo` can point to a directory on the local machine, or it can point to a folder on a network share.</span></span> <span data-ttu-id="2ab28-112">Wenn Sie auf ein Verzeichnis auf dem lokalen Computer verweisen (und das Szenario darin besteht, dass nur apps auf dem lokalen Computer Zugriff benötigen, um dieses Repository zu verwenden), können Sie die Verwendung von [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (unter Windows) zum Verschlüsseln der Schlüssel im Ruhezustand in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="2ab28-112">If pointing to a directory on the local machine (and the scenario is that only apps on the local machine require access to use this repository), consider using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) (on Windows) to encrypt the keys at rest.</span></span> <span data-ttu-id="2ab28-113">Andernfalls sollten Sie die Verwendung eines [X. 509-Zertifikats](xref:security/data-protection/implementation/key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand in Erwägung gezogen.</span><span class="sxs-lookup"><span data-stu-id="2ab28-113">Otherwise, consider using an [X.509 certificate](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt keys at rest.</span></span>

## <a name="azure-storage"></a><span data-ttu-id="2ab28-114">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="2ab28-114">Azure Storage</span></span>

<span data-ttu-id="2ab28-115">Das Paket [Microsoft. aspnetcore. dataprotection. azurestorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) ermöglicht die Speicherung von Datenschutz Schlüsseln in Azure BLOB Storage.</span><span class="sxs-lookup"><span data-stu-id="2ab28-115">The [Microsoft.AspNetCore.DataProtection.AzureStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.AzureStorage/) package allows storing data protection keys in Azure Blob Storage.</span></span> <span data-ttu-id="2ab28-116">Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-116">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="2ab28-117">Apps können Authentifizierungs- :::no-loc(cookie)::: oder CSRF-Schutz auf mehreren Servern freigeben.</span><span class="sxs-lookup"><span data-stu-id="2ab28-117">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

<span data-ttu-id="2ab28-118">Zum Konfigurieren des Azure BLOB Storage Anbieters müssen Sie eine der [persistkeystoazureblobstorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) -über Ladungen abrufen.</span><span class="sxs-lookup"><span data-stu-id="2ab28-118">To configure the Azure Blob Storage provider, call one of the [PersistKeysToAzureBlobStorage](/dotnet/api/microsoft.aspnetcore.dataprotection.azuredataprotectionbuilderextensions.persistkeystoazureblobstorage) overloads.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToAzureBlobStorage(new Uri("<blob URI including SAS token>"));
}
```

<span data-ttu-id="2ab28-119">Wenn die Web-App als Azure-Dienst ausgeführt wird, können Authentifizierungs Token automatisch mithilfe von [Microsoft. Azure. Services. appauthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-119">If the web app is running as an Azure service, authentication tokens can be automatically created using [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/).</span></span>

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

<span data-ttu-id="2ab28-120">[Weitere Informationen zum Konfigurieren der Dienst-zu-Dienst-Authentifizierung finden Sie unter.](/azure/key-vault/service-to-service-authentication)</span><span class="sxs-lookup"><span data-stu-id="2ab28-120">See [more details about configuring service-to-service authentication.](/azure/key-vault/service-to-service-authentication)</span></span>

## <a name="redis"></a><span data-ttu-id="2ab28-121">Redis</span><span class="sxs-lookup"><span data-stu-id="2ab28-121">Redis</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2ab28-122">Das [Microsoft. aspnetcore. dataprotection. stackexchangeredis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) -Paket ermöglicht das Speichern von Datenschutz Schlüsseln in einem redis-Cache.</span><span class="sxs-lookup"><span data-stu-id="2ab28-122">The [Microsoft.AspNetCore.DataProtection.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.StackExchangeRedis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="2ab28-123">Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-123">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="2ab28-124">Apps können Authentifizierungs- :::no-loc(cookie)::: oder CSRF-Schutz auf mehreren Servern freigeben.</span><span class="sxs-lookup"><span data-stu-id="2ab28-124">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2ab28-125">Das [Microsoft. aspnetcore. dataprotection. redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) -Paket ermöglicht das Speichern von Datenschutz Schlüsseln in einem redis-Cache.</span><span class="sxs-lookup"><span data-stu-id="2ab28-125">The [Microsoft.AspNetCore.DataProtection.Redis](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Redis/) package allows storing data protection keys in a Redis cache.</span></span> <span data-ttu-id="2ab28-126">Schlüssel können für mehrere Instanzen einer Web-App freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-126">Keys can be shared across several instances of a web app.</span></span> <span data-ttu-id="2ab28-127">Apps können Authentifizierungs- :::no-loc(cookie)::: oder CSRF-Schutz auf mehreren Servern freigeben.</span><span class="sxs-lookup"><span data-stu-id="2ab28-127">Apps can share authentication :::no-loc(cookie):::s or CSRF protection across multiple servers.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="2ab28-128">Rufen Sie zum Konfigurieren von redis eine der [persistkeystostackexchangeredis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) -über Ladungen auf:</span><span class="sxs-lookup"><span data-stu-id="2ab28-128">To configure on Redis, call one of the [PersistKeysToStackExchangeRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.stackexchangeredisdataprotectionbuilderextensions.persistkeystostackexchangeredis) overloads:</span></span>

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

<span data-ttu-id="2ab28-129">Um für redis zu konfigurieren, wenden Sie eine der [persistkeystoredis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) -über Ladungen an:</span><span class="sxs-lookup"><span data-stu-id="2ab28-129">To configure on Redis, call one of the [PersistKeysToRedis](/dotnet/api/microsoft.aspnetcore.dataprotection.redisdataprotectionbuilderextensions.persistkeystoredis) overloads:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    var redis = ConnectionMultiplexer.Connect("<URI>");
    services.AddDataProtection()
        .PersistKeysToRedis(redis, "DataProtection-Keys");
}
```

::: moniker-end

<span data-ttu-id="2ab28-130">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="2ab28-130">For more information, see the following topics:</span></span>

* [<span data-ttu-id="2ab28-131">Stackexchange. redis connectionmultiplexer</span><span class="sxs-lookup"><span data-stu-id="2ab28-131">StackExchange.Redis ConnectionMultiplexer</span></span>](https://github.com/StackExchange/StackExchange.Redis/blob/master/docs/Basics.md)
* [<span data-ttu-id="2ab28-132">Azure Redis Cache</span><span class="sxs-lookup"><span data-stu-id="2ab28-132">Azure Redis Cache</span></span>](/azure/redis-cache/cache-dotnet-how-to-use-azure-redis-cache#connect-to-the-cache)
* [<span data-ttu-id="2ab28-133">ASP.net Core DataProtection-Beispiele</span><span class="sxs-lookup"><span data-stu-id="2ab28-133">ASP.NET Core DataProtection samples</span></span>](https://github.com/dotnet/AspNetCore/tree/2.2.0/src/DataProtection/samples)

## <a name="registry"></a><span data-ttu-id="2ab28-134">Registrierung</span><span class="sxs-lookup"><span data-stu-id="2ab28-134">Registry</span></span>

<span data-ttu-id="2ab28-135">**Gilt nur für Windows-bereit Stellungen.**</span><span class="sxs-lookup"><span data-stu-id="2ab28-135">**Only applies to Windows deployments.**</span></span>

<span data-ttu-id="2ab28-136">Manchmal verfügt die APP möglicherweise nicht über Schreibzugriff auf das Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="2ab28-136">Sometimes the app might not have write access to the file system.</span></span> <span data-ttu-id="2ab28-137">Stellen Sie sich ein Szenario vor, in dem eine App als virtuelles Dienst Konto (z. b. die Identität des App-Pools für *w3wp.exe* ) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2ab28-137">Consider a scenario where an app is running as a virtual service account (such as *w3wp.exe* 's app pool identity).</span></span> <span data-ttu-id="2ab28-138">In diesen Fällen kann der Administrator einen Registrierungsschlüssel bereitstellen, der über die Dienst Konto Identität zugänglich ist.</span><span class="sxs-lookup"><span data-stu-id="2ab28-138">In these cases, the administrator can provision a registry key that's accessible by the service account identity.</span></span> <span data-ttu-id="2ab28-139">Nennen Sie die [persistkeystoregistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) -Erweiterungsmethode, wie unten gezeigt.</span><span class="sxs-lookup"><span data-stu-id="2ab28-139">Call the [PersistKeysToRegistry](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystoregistry) extension method as shown below.</span></span> <span data-ttu-id="2ab28-140">Geben Sie einen [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) an, der auf den Speicherort verweist, an dem kryptografische Schlüssel gespeichert werden sollen:</span><span class="sxs-lookup"><span data-stu-id="2ab28-140">Provide a [RegistryKey](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.registryxmlrepository.registrykey) pointing to the location where cryptographic keys should be stored:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection()
        .PersistKeysToRegistry(Registry.CurrentUser.OpenSubKey(@"SOFTWARE\Sample\keys"));
}
```

> [!IMPORTANT]
> <span data-ttu-id="2ab28-141">Wir empfehlen die Verwendung von [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) zum Verschlüsseln der Schlüssel im Ruhezustand.</span><span class="sxs-lookup"><span data-stu-id="2ab28-141">We recommend using [Windows DPAPI](xref:security/data-protection/implementation/key-encryption-at-rest) to encrypt the keys at rest.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="entity-framework-core"></a><span data-ttu-id="2ab28-142">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="2ab28-142">Entity Framework Core</span></span>

<span data-ttu-id="2ab28-143">Das [Microsoft. aspnetcore. dataprotection. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) -Paket bietet einen Mechanismus zum Speichern von Datenschutz Schlüsseln in einer Datenbank mithilfe von Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="2ab28-143">The [Microsoft.AspNetCore.DataProtection.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.EntityFrameworkCore/) package provides a mechanism for storing data protection keys to a database using Entity Framework Core.</span></span> <span data-ttu-id="2ab28-144">Das `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` nuget-Paket muss der Projektdatei hinzugefügt werden. es ist nicht Teil des [Microsoft. aspnetcore. app-Metapakets](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2ab28-144">The `Microsoft.AspNetCore.DataProtection.EntityFrameworkCore` NuGet package must be added to the project file, it's not part of the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2ab28-145">Mit diesem Paket können Schlüssel für mehrere Instanzen einer Web-App freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="2ab28-145">With this package, keys can be shared across multiple instances of a web app.</span></span>

<span data-ttu-id="2ab28-146">Zum Konfigurieren des EF Core Anbieters müssen Sie die [persistkeystodbcontext \<TContext> ](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) -Methode abrufen:</span><span class="sxs-lookup"><span data-stu-id="2ab28-146">To configure the EF Core provider, call the [PersistKeysToDbContext\<TContext>](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcoredataprotectionextensions.persistkeystodbcontext) method:</span></span>

[!code-csharp[Main](key-storage-providers/sample/Startup.cs?name=snippet&highlight=13-20)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="2ab28-147">Der generische Parameter `TContext` muss von [dbcontext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) erben und [idataschutzkeycontext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext)implementieren:</span><span class="sxs-lookup"><span data-stu-id="2ab28-147">The generic parameter, `TContext`, must inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) and implement [IDataProtectionKeyContext](/dotnet/api/microsoft.aspnetcore.dataprotection.entityframeworkcore.idataprotectionkeycontext):</span></span>

[!code-csharp[Main](key-storage-providers/sample/MyKeysContext.cs)]

<span data-ttu-id="2ab28-148">Erstellen Sie die Tabelle `DataProtectionKeys`.</span><span class="sxs-lookup"><span data-stu-id="2ab28-148">Create the `DataProtectionKeys` table.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2ab28-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ab28-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2ab28-150">Führen Sie die folgenden Befehle im Fenster **Paket-Manager-Konsole** (PMC) aus:</span><span class="sxs-lookup"><span data-stu-id="2ab28-150">Execute the following commands in the **Package Manager Console** (PMC) window:</span></span>

```powershell
Add-Migration AddDataProtectionKeys -Context MyKeysContext
Update-Database -Context MyKeysContext
```

# <a name="net-core-cli"></a>[<span data-ttu-id="2ab28-151">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2ab28-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2ab28-152">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="2ab28-152">Execute the following commands in a command shell:</span></span>

```dotnetcli
dotnet ef migrations add AddDataProtectionKeys --context MyKeysContext
dotnet ef database update --context MyKeysContext
```

---

<span data-ttu-id="2ab28-153">`MyKeysContext` ist der, der `DbContext` im vorangehenden Codebeispiel definiert wurde.</span><span class="sxs-lookup"><span data-stu-id="2ab28-153">`MyKeysContext` is the `DbContext` defined in the preceding code sample.</span></span> <span data-ttu-id="2ab28-154">Wenn Sie einen `DbContext` mit einem anderen Namen verwenden, ersetzen Sie den `DbContext` Namen durch `MyKeysContext` .</span><span class="sxs-lookup"><span data-stu-id="2ab28-154">If you're using a `DbContext` with a different name, substitute your `DbContext` name for `MyKeysContext`.</span></span>

<span data-ttu-id="2ab28-155">Die `DataProtectionKeys` Klasse/Entität übernimmt die Struktur, die in der folgenden Tabelle gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="2ab28-155">The `DataProtectionKeys` class/entity adopts the structure shown in the following table.</span></span>

| <span data-ttu-id="2ab28-156">Eigenschaft/Feld</span><span class="sxs-lookup"><span data-stu-id="2ab28-156">Property/Field</span></span> | <span data-ttu-id="2ab28-157">CLR-Typ</span><span class="sxs-lookup"><span data-stu-id="2ab28-157">CLR Type</span></span> | <span data-ttu-id="2ab28-158">SQL-Typ</span><span class="sxs-lookup"><span data-stu-id="2ab28-158">SQL Type</span></span>              |
| -------------- | -------- | --------------------- |
| `Id`           | `int`    | <span data-ttu-id="2ab28-159">`int`, PK, `IDENTITY(1,1)` nicht NULL</span><span class="sxs-lookup"><span data-stu-id="2ab28-159">`int`, PK, `IDENTITY(1,1)`, not null</span></span>   |
| `FriendlyName` | `string` | <span data-ttu-id="2ab28-160">`nvarchar(MAX)`, NULL</span><span class="sxs-lookup"><span data-stu-id="2ab28-160">`nvarchar(MAX)`, null</span></span> |
| `Xml`          | `string` | <span data-ttu-id="2ab28-161">`nvarchar(MAX)`, NULL</span><span class="sxs-lookup"><span data-stu-id="2ab28-161">`nvarchar(MAX)`, null</span></span> |

::: moniker-end

## <a name="custom-key-repository"></a><span data-ttu-id="2ab28-162">Repository für benutzerdefinierte Schlüssel</span><span class="sxs-lookup"><span data-stu-id="2ab28-162">Custom key repository</span></span>

<span data-ttu-id="2ab28-163">Wenn die in-Box-Mechanismen nicht geeignet sind, kann der Entwickler einen eigenen Schlüssel Persistenzmechanismus angeben, indem er ein benutzerdefiniertes [ixmlrepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository)bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="2ab28-163">If the in-box mechanisms aren't appropriate, the developer can specify their own key persistence mechanism by providing a custom [IXmlRepository](/dotnet/api/microsoft.aspnetcore.dataprotection.repositories.ixmlrepository).</span></span>
