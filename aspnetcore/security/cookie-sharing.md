---
title: 'Freigeben von Authentifizierungs- :::no-loc(cookie)::: e unter ASP.net-apps'
author: rick-anderson
description: 'Erfahren Sie, wie Sie Authentifizierungs :::no-loc(cookie)::: -s für ASP.NET 4. x-und ASP.net Core-apps freigeben.'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/:::no-loc(cookie):::-sharing
ms.openlocfilehash: 8f54f2e4894328f8471d5f80c8184839ce47add6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059688"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="ea91c-103">Freigeben von Authentifizierungs- :::no-loc(cookie)::: e unter ASP.net-apps</span><span class="sxs-lookup"><span data-stu-id="ea91c-103">Share authentication :::no-loc(cookie):::s among ASP.NET apps</span></span>

<span data-ttu-id="ea91c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ea91c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ea91c-105">Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="ea91c-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="ea91c-106">Zum Bereitstellen eines Single Sign-on (SSO) müssen Web-Apps innerhalb einer Website Authentifizierung :::no-loc(cookie)::: s freigeben.</span><span class="sxs-lookup"><span data-stu-id="ea91c-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication :::no-loc(cookie):::s.</span></span> <span data-ttu-id="ea91c-107">Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana :::no-loc(cookie)::: -Authentifizierung und ASP.net Core :::no-loc(cookie)::: Authentifizierungs Tickets.</span><span class="sxs-lookup"><span data-stu-id="ea91c-107">To support this scenario, the data protection stack allows sharing Katana :::no-loc(cookie)::: authentication and ASP.NET Core :::no-loc(cookie)::: authentication tickets.</span></span>

<span data-ttu-id="ea91c-108">In den folgenden Beispielen:</span><span class="sxs-lookup"><span data-stu-id="ea91c-108">In the examples that follow:</span></span>

* <span data-ttu-id="ea91c-109">Der Authentifizierungs :::no-loc(cookie)::: Name wird auf einen gemeinsamen Wert von festgelegt `.AspNet.Shared:::no-loc(Cookie):::` .</span><span class="sxs-lookup"><span data-stu-id="ea91c-109">The authentication :::no-loc(cookie)::: name is set to a common value of `.AspNet.Shared:::no-loc(Cookie):::`.</span></span>
* <span data-ttu-id="ea91c-110">Der `AuthenticationType` wird `:::no-loc(Identity):::.Application` entweder explizit oder standardmäßig auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ea91c-110">The `AuthenticationType` is set to `:::no-loc(Identity):::.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="ea91c-111">Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz Schlüsseln () zu ermöglichen `Shared:::no-loc(Cookie):::App` .</span><span class="sxs-lookup"><span data-stu-id="ea91c-111">A common app name is used to enable the data protection system to share data protection keys (`Shared:::no-loc(Cookie):::App`).</span></span>
* <span data-ttu-id="ea91c-112">`:::no-loc(Identity):::.Application` wird als Authentifizierungsschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="ea91c-112">`:::no-loc(Identity):::.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="ea91c-113">Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Apps verwendet werden, :::no-loc(cookie)::: entweder als Standardschema oder explizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="ea91c-113">Whatever scheme is used, it must be used consistently *within and across* the shared :::no-loc(cookie)::: apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="ea91c-114">Das Schema wird beim Verschlüsseln und Entschlüsseln von :::no-loc(cookie)::: s verwendet. Daher muss ein konsistentes Schema zwischen allen Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ea91c-114">The scheme is used when encrypting and decrypting :::no-loc(cookie):::s, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="ea91c-115">Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.</span><span class="sxs-lookup"><span data-stu-id="ea91c-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="ea91c-116">In ASP.net Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Speicherort des Schlüssels festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ea91c-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="ea91c-117">In .NET Framework-apps :::no-loc(Cookie)::: verwendet die Authentifizierungs Middleware eine Implementierung von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="ea91c-117">In .NET Framework apps, :::no-loc(Cookie)::: Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="ea91c-118">`DataProtectionProvider` stellt Datenschutzdienste für die Verschlüsselung und Entschlüsselung von Authentifizierungs :::no-loc(cookie)::: Nutzlastdaten bereit.</span><span class="sxs-lookup"><span data-stu-id="ea91c-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication :::no-loc(cookie)::: payload data.</span></span> <span data-ttu-id="ea91c-119">Die- `DataProtectionProvider` Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="ea91c-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="ea91c-120">[Dataschutzprovider. Create (System. IO. Director yinfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo> , um den Speicherort für den Datenschutz Schlüssel anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ea91c-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="ea91c-121">`DataProtectionProvider` erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":</span><span class="sxs-lookup"><span data-stu-id="ea91c-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="ea91c-122">Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".</span><span class="sxs-lookup"><span data-stu-id="ea91c-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="ea91c-123">Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea91c-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="ea91c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Legt den Namen der allgemeinen App fest.</span><span class="sxs-lookup"><span data-stu-id="ea91c-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="ea91c-125">Freigeben :::no-loc(cookie)::: von Authentifizierung s mit :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="ea91c-125">Share authentication :::no-loc(cookie):::s with :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="ea91c-126">Folgendes ist bei Verwendung von :::no-loc(ASP.NET Core Identity)::: zu beachten:</span><span class="sxs-lookup"><span data-stu-id="ea91c-126">When using :::no-loc(ASP.NET Core Identity)::::</span></span>

* <span data-ttu-id="ea91c-127">Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="ea91c-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="ea91c-128">In den folgenden Beispielen wird die-Methode mit einem allgemeinen Schlüssel Speicherort bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="ea91c-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="ea91c-129">Verwenden <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Sie, um einen gemeinsamen freigegebenen APP-Namen ( `Shared:::no-loc(Cookie):::App` in den folgenden Beispielen) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ea91c-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`Shared:::no-loc(Cookie):::App` in the following examples).</span></span> <span data-ttu-id="ea91c-130">Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="ea91c-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="ea91c-131">Verwenden Sie die- <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> Erweiterungsmethode, um den Datenschutz Dienst für :::no-loc(cookie)::: s einzurichten.</span><span class="sxs-lookup"><span data-stu-id="ea91c-131">Use the <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.ConfigureApplication:::no-loc(Cookie):::*> extension method to set up the data protection service for :::no-loc(cookie):::s.</span></span>
* <span data-ttu-id="ea91c-132">Der Standard Authentifizierungstyp ist `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="ea91c-132">The default authentication type is `:::no-loc(Identity):::.Application`.</span></span>

<span data-ttu-id="ea91c-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="ea91c-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
});
```

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="ea91c-134">Freigeben von Authentifizierung :::no-loc(cookie)::: s ohne :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="ea91c-134">Share authentication :::no-loc(cookie):::s without :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="ea91c-135">Wenn Sie :::no-loc(cookie)::: e direkt ohne verwenden :::no-loc(ASP.NET Core Identity)::: , konfigurieren Sie den Datenschutz und die Authentifizierung in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ea91c-135">When using :::no-loc(cookie):::s directly without :::no-loc(ASP.NET Core Identity):::, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ea91c-136">Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `:::no-loc(Identity):::.Application` :</span><span class="sxs-lookup"><span data-stu-id="ea91c-136">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.AddAuthentication(":::no-loc(Identity):::.Application")
    .Add:::no-loc(Cookie):::(":::no-loc(Identity):::.Application", options =>
    {
        options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="ea91c-137">Freigeben von :::no-loc(cookie)::: s über verschiedene Basis Pfade hinweg</span><span class="sxs-lookup"><span data-stu-id="ea91c-137">Share :::no-loc(cookie):::s across different base paths</span></span>

<span data-ttu-id="ea91c-138">Eine Authentifizierung :::no-loc(cookie)::: verwendet [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) als Standardwert [ :::no-loc(Cookie)::: . Der Pfad](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span><span class="sxs-lookup"><span data-stu-id="ea91c-138">An authentication :::no-loc(cookie)::: uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [:::no-loc(Cookie):::.Path](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Path).</span></span> <span data-ttu-id="ea91c-139">Wenn die der APP :::no-loc(cookie)::: über verschiedene Basis Pfade gemeinsam genutzt werden muss, `Path` muss überschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="ea91c-139">If the app's :::no-loc(cookie)::: must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("Shared:::no-loc(Cookie):::App");

services.ConfigureApplication:::no-loc(Cookie):::(options => {
    options.:::no-loc(Cookie):::.Name = ".AspNet.Shared:::no-loc(Cookie):::";
    options.:::no-loc(Cookie):::.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="ea91c-140">Freigeben von :::no-loc(cookie)::: s über Unterdomänen hinweg</span><span class="sxs-lookup"><span data-stu-id="ea91c-140">Share :::no-loc(cookie):::s across subdomains</span></span>

<span data-ttu-id="ea91c-141">Beim Hosting von apps, die :::no-loc(cookie)::: über Unterdomänen hinweg gemeinsam genutzt werden, geben Sie eine gemeinsame Domäne in an [ :::no-loc(Cookie)::: . Domänen](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ea91c-141">When hosting apps that share :::no-loc(cookie):::s across subdomains, specify a common domain in the [:::no-loc(Cookie):::.Domain](xref:Microsoft.AspNetCore.Http.:::no-loc(Cookie):::Builder.Domain) property.</span></span> <span data-ttu-id="ea91c-142">Wenn Sie :::no-loc(cookie)::: s für apps unter `contoso.com` , wie z. b. `first_subdomain.contoso.com` und `second_subdomain.contoso.com` , freigeben `:::no-loc(Cookie):::.Domain` möchten, geben Sie Folgendes an `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="ea91c-142">To share :::no-loc(cookie):::s across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `:::no-loc(Cookie):::.Domain` as `.contoso.com`:</span></span>

```csharp
options.:::no-loc(Cookie):::.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="ea91c-143">Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand</span><span class="sxs-lookup"><span data-stu-id="ea91c-143">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="ea91c-144">Konfigurieren Sie für Produktions Bereitstellungen den so, dass `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate verschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="ea91c-144">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="ea91c-145">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="ea91c-145">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="ea91c-146">Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für Folgendes bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="ea91c-146">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="ea91c-147">Freigeben :::no-loc(cookie)::: von Authentifizierung s zwischen ASP.NET 4. x-und ASP.net Core-apps</span><span class="sxs-lookup"><span data-stu-id="ea91c-147">Share authentication :::no-loc(cookie):::s between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="ea91c-148">ASP.NET 4. x-apps, die Katana- :::no-loc(Cookie)::: Authentifizierungs Middleware verwenden, können so konfiguriert werden, dass Sie Authentifizierungen generieren :::no-loc(cookie)::: , die mit der ASP.net Core :::no-loc(Cookie)::: Authentifizierungs Middleware kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="ea91c-148">ASP.NET 4.x apps that use Katana :::no-loc(Cookie)::: Authentication Middleware can be configured to generate authentication :::no-loc(cookie):::s that are compatible with the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware.</span></span> <span data-ttu-id="ea91c-149">Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.</span><span class="sxs-lookup"><span data-stu-id="ea91c-149">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="ea91c-150">Wenn eine APP Katana :::no-loc(Cookie)::: -Authentifizierungs Middleware verwendet, ruft Sie `Use:::no-loc(Cookie):::Authentication` in der *Startup.auth.cs* -Datei des Projekts auf.</span><span class="sxs-lookup"><span data-stu-id="ea91c-150">When an app uses Katana :::no-loc(Cookie)::: Authentication Middleware, it calls `Use:::no-loc(Cookie):::Authentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="ea91c-151">ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana- :::no-loc(Cookie)::: Authentifizierungs Middleware.</span><span class="sxs-lookup"><span data-stu-id="ea91c-151">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana :::no-loc(Cookie)::: Authentication Middleware by default.</span></span> <span data-ttu-id="ea91c-152">Obwohl `Use:::no-loc(Cookie):::Authentication` veraltet ist und für ASP.net Core-apps nicht unterstützt wird, `Use:::no-loc(Cookie):::Authentication` ist der Aufruf von in einer ASP.NET 4. x-APP, die die Katana- :::no-loc(Cookie)::: Authentifizierungs Middleware verwendet, gültig.</span><span class="sxs-lookup"><span data-stu-id="ea91c-152">Although `Use:::no-loc(Cookie):::Authentication` is obsolete and unsupported for ASP.NET Core apps, calling `Use:::no-loc(Cookie):::Authentication` in an ASP.NET 4.x app that uses Katana :::no-loc(Cookie)::: Authentication Middleware is valid.</span></span>

<span data-ttu-id="ea91c-153">Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein.</span><span class="sxs-lookup"><span data-stu-id="ea91c-153">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="ea91c-154">Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.</span><span class="sxs-lookup"><span data-stu-id="ea91c-154">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="ea91c-155">:::no-loc(cookie):::Konfigurieren Sie die ASP.net Core-APP, wie im Abschnitt [Freigeben von Authentifizierungen für :::no-loc(cookie)::: ASP.net Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) angegeben, und konfigurieren Sie die ASP.NET 4. x-APP wie folgt, um die Authentifizierungs-e zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freizugeben.</span><span class="sxs-lookup"><span data-stu-id="ea91c-155">To share authentication :::no-loc(cookie):::s between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication :::no-loc(cookie):::s among ASP.NET Core apps](#share-authentication-:::no-loc(cookie):::s-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="ea91c-156">Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="ea91c-156">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="ea91c-157">Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.</span><span class="sxs-lookup"><span data-stu-id="ea91c-157">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="ea91c-158">Suchen und ändern Sie den-Befehl an `Use:::no-loc(Cookie):::Authentication` :</span><span class="sxs-lookup"><span data-stu-id="ea91c-158">Locate and modify the call to `Use:::no-loc(Cookie):::Authentication`:</span></span>

* <span data-ttu-id="ea91c-159">Ändern Sie den Namen so, dass er :::no-loc(cookie)::: dem Namen entspricht, der von der ASP.net Core :::no-loc(Cookie)::: Authentifizierungs Middleware verwendet wird ( `.AspNet.Shared:::no-loc(Cookie):::` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="ea91c-159">Change the :::no-loc(cookie)::: name to match the name used by the ASP.NET Core :::no-loc(Cookie)::: Authentication Middleware (`.AspNet.Shared:::no-loc(Cookie):::` in the example).</span></span>
* <span data-ttu-id="ea91c-160">Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `:::no-loc(Identity):::.Application` .</span><span class="sxs-lookup"><span data-stu-id="ea91c-160">In the following example, the authentication type is set to `:::no-loc(Identity):::.Application`.</span></span>
* <span data-ttu-id="ea91c-161">Geben Sie eine Instanz von `DataProtectionProvider` an, die für den Common Data Protection Key-Speicherort initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="ea91c-161">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="ea91c-162">Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von allen Apps verwendet wird, die Authentifizierungs- :::no-loc(cookie)::: e verwenden ( `Shared:::no-loc(Cookie):::App` im Beispiel)</span><span class="sxs-lookup"><span data-stu-id="ea91c-162">Confirm that the app name is set to the common app name used by all apps that share authentication :::no-loc(cookie):::s (`Shared:::no-loc(Cookie):::App` in the example).</span></span>

<span data-ttu-id="ea91c-163">Wenn `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` Sie und nicht festlegen `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , legen <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> Sie auf einen Anspruch fest, der eindeutige Benutzer unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="ea91c-163">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="ea91c-164">*App_Start/Startup.auth.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea91c-164">*App_Start/Startup.Auth.cs* :</span></span>

```csharp
app.Use:::no-loc(Cookie):::Authentication(new :::no-loc(Cookie):::AuthenticationOptions
{
    AuthenticationType = ":::no-loc(Identity):::.Application",
    :::no-loc(Cookie):::Name = ".AspNet.Shared:::no-loc(Cookie):::",
    LoginPath = new PathString("/Account/Login"),
    Provider = new :::no-loc(Cookie):::AuthenticationProvider
    {
        OnValidate:::no-loc(Identity)::: =
            SecurityStampValidator
                .OnValidate:::no-loc(Identity):::<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerate:::no-loc(Identity):::: (manager, user) =>
                        user.GenerateUser:::no-loc(Identity):::Async(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("Shared:::no-loc(Cookie):::App"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s." +
                    ":::no-loc(Cookie):::AuthenticationMiddleware",
                ":::no-loc(Identity):::.Application",
                "v2"))),
    :::no-loc(Cookie):::Manager = new Chunking:::no-loc(Cookie):::Manager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="ea91c-165">Beim Erstellen einer Benutzeridentität muss der Authentifizierungstyp ( `:::no-loc(Identity):::.Application` ) mit dem Typ identisch sein, der in `AuthenticationType` Set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.auth.cs* definiert ist.</span><span class="sxs-lookup"><span data-stu-id="ea91c-165">When generating a user identity, the authentication type (`:::no-loc(Identity):::.Application`) must match the type defined in `AuthenticationType` set with `Use:::no-loc(Cookie):::Authentication` in *App_Start/Startup.Auth.cs* .</span></span>

<span data-ttu-id="ea91c-166">*Modelle/ :::no-loc(Identity)::: Models.cs* :</span><span class="sxs-lookup"><span data-stu-id="ea91c-166">*Models/:::no-loc(Identity):::Models.cs* :</span></span>

```csharp
public class ApplicationUser : :::no-loc(Identity):::User
{
    public async Task<Claims:::no-loc(Identity):::> GenerateUser:::no-loc(Identity):::Async(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // :::no-loc(Cookie):::AuthenticationOptions.AuthenticationType
        var user:::no-loc(Identity)::: = 
            await manager.Create:::no-loc(Identity):::Async(this, ":::no-loc(Identity):::.Application");

        // Add custom user claims here

        return user:::no-loc(Identity):::;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="ea91c-167">Verwenden einer allgemeinen Benutzerdatenbank</span><span class="sxs-lookup"><span data-stu-id="ea91c-167">Use a common user database</span></span>

<span data-ttu-id="ea91c-168">Wenn apps dasselbe Schema verwenden :::no-loc(Identity)::: (dieselbe Version von :::no-loc(Identity)::: ), vergewissern Sie sich, dass das :::no-loc(Identity)::: System für jede APP auf dieselbe Benutzerdatenbank verweist.</span><span class="sxs-lookup"><span data-stu-id="ea91c-168">When apps use the same :::no-loc(Identity)::: schema (same version of :::no-loc(Identity):::), confirm that the :::no-loc(Identity)::: system for each app is pointed at the same user database.</span></span> <span data-ttu-id="ea91c-169">Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit, wenn versucht wird, die Informationen in der Authentifizierung mit :::no-loc(cookie)::: den Informationen in der Datenbank abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="ea91c-169">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication :::no-loc(cookie)::: against the information in its database.</span></span>

<span data-ttu-id="ea91c-170">Wenn sich das :::no-loc(Identity)::: Schema von apps unterscheidet, in der Regel, weil apps verschiedene Versionen verwenden, ist die :::no-loc(Identity)::: gemeinsame Nutzung einer gemeinsamen Datenbank basierend auf der neuesten Version von :::no-loc(Identity)::: nicht möglich, ohne dass Spalten in den Schemas anderer apps neu zugeordnet und hinzugefügt werden :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="ea91c-170">When the :::no-loc(Identity)::: schema is different among apps, usually because apps are using different :::no-loc(Identity)::: versions, sharing a common database based on the latest version of :::no-loc(Identity)::: isn't possible without remapping and adding columns in other app's :::no-loc(Identity)::: schemas.</span></span> <span data-ttu-id="ea91c-171">Häufig ist es effizienter, die anderen apps so zu aktualisieren, dass Sie die neueste Version verwenden, :::no-loc(Identity)::: damit eine gemeinsame Datenbank von den apps gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="ea91c-171">It's often more efficient to upgrade the other apps to use the latest :::no-loc(Identity)::: version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea91c-172">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ea91c-172">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
