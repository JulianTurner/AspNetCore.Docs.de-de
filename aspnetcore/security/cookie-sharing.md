---
title: Freigeben von Authentifizierungs- cookie e unter ASP.net-apps
author: rick-anderson
description: Erfahren Sie, wie Sie Authentifizierungs cookie -s für ASP.NET 4. x-und ASP.net Core-apps freigeben.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252993"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="25ae0-103">Freigeben von Authentifizierungs- cookie e unter ASP.net-apps</span><span class="sxs-lookup"><span data-stu-id="25ae0-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="25ae0-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="25ae0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="25ae0-105">Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="25ae0-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="25ae0-106">Zum Bereitstellen eines Single Sign-on (SSO) müssen Web-Apps innerhalb einer Website Authentifizierung cookie s freigeben.</span><span class="sxs-lookup"><span data-stu-id="25ae0-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="25ae0-107">Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana cookie -Authentifizierung und ASP.net Core cookie Authentifizierungs Tickets.</span><span class="sxs-lookup"><span data-stu-id="25ae0-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="25ae0-108">In den folgenden Beispielen:</span><span class="sxs-lookup"><span data-stu-id="25ae0-108">In the examples that follow:</span></span>

* <span data-ttu-id="25ae0-109">Der Authentifizierungs cookie Name wird auf einen gemeinsamen Wert von festgelegt `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="25ae0-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="25ae0-110">Der `AuthenticationType` wird `Identity.Application` entweder explizit oder standardmäßig auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="25ae0-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="25ae0-111">Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz Schlüsseln () zu ermöglichen `SharedCookieApp` .</span><span class="sxs-lookup"><span data-stu-id="25ae0-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="25ae0-112">`Identity.Application` wird als Authentifizierungsschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="25ae0-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="25ae0-113">Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Apps verwendet werden, cookie entweder als Standardschema oder explizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="25ae0-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="25ae0-114">Das Schema wird beim Verschlüsseln und Entschlüsseln von cookie s verwendet. Daher muss ein konsistentes Schema zwischen allen Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="25ae0-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="25ae0-115">Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.</span><span class="sxs-lookup"><span data-stu-id="25ae0-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="25ae0-116">In ASP.net Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Speicherort des Schlüssels festzulegen.</span><span class="sxs-lookup"><span data-stu-id="25ae0-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="25ae0-117">In .NET Framework-apps Cookie verwendet die Authentifizierungs Middleware eine Implementierung von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="25ae0-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="25ae0-118">`DataProtectionProvider` stellt Datenschutzdienste für die Verschlüsselung und Entschlüsselung von Authentifizierungs cookie Nutzlastdaten bereit.</span><span class="sxs-lookup"><span data-stu-id="25ae0-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="25ae0-119">Die- `DataProtectionProvider` Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="25ae0-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="25ae0-120">[Dataschutzprovider. Create (System. IO. Director yinfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo> , um den Speicherort für den Datenschutz Schlüssel anzugeben.</span><span class="sxs-lookup"><span data-stu-id="25ae0-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="25ae0-121">`DataProtectionProvider` erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":</span><span class="sxs-lookup"><span data-stu-id="25ae0-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="25ae0-122">Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".</span><span class="sxs-lookup"><span data-stu-id="25ae0-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="25ae0-123">Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.</span><span class="sxs-lookup"><span data-stu-id="25ae0-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="25ae0-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Legt den Namen der allgemeinen App fest.</span><span class="sxs-lookup"><span data-stu-id="25ae0-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="25ae0-125">Freigeben cookie von Authentifizierung s mit ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="25ae0-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="25ae0-126">Folgendes ist bei Verwendung von ASP.NET Core Identity zu beachten:</span><span class="sxs-lookup"><span data-stu-id="25ae0-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="25ae0-127">Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="25ae0-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="25ae0-128">In den folgenden Beispielen wird die-Methode mit einem allgemeinen Schlüssel Speicherort bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="25ae0-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="25ae0-129">Verwenden <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Sie, um einen gemeinsamen freigegebenen APP-Namen ( `SharedCookieApp` in den folgenden Beispielen) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="25ae0-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="25ae0-130">Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="25ae0-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="25ae0-131">Verwenden Sie die- <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> Erweiterungsmethode, um den Datenschutz Dienst für cookie s einzurichten.</span><span class="sxs-lookup"><span data-stu-id="25ae0-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="25ae0-132">Der Standard Authentifizierungstyp ist `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="25ae0-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="25ae0-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="25ae0-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="25ae0-134">**Hinweis:** Die vorangehenden Anweisungen funktionieren nicht mit `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).</span><span class="sxs-lookup"><span data-stu-id="25ae0-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="25ae0-135">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span><span class="sxs-lookup"><span data-stu-id="25ae0-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="25ae0-136">Freigeben von Authentifizierung cookie s ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="25ae0-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="25ae0-137">Wenn Sie cookie e direkt ohne verwenden ASP.NET Core Identity , konfigurieren Sie den Datenschutz und die Authentifizierung in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="25ae0-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="25ae0-138">Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="25ae0-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="25ae0-139">Freigeben von cookie s über verschiedene Basis Pfade hinweg</span><span class="sxs-lookup"><span data-stu-id="25ae0-139">Share cookies across different base paths</span></span>

<span data-ttu-id="25ae0-140">Eine Authentifizierung cookie verwendet [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) als Standardwert [ Cookie . Der Pfad](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="25ae0-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="25ae0-141">Wenn die der APP cookie über verschiedene Basis Pfade gemeinsam genutzt werden muss, `Path` muss überschrieben werden:</span><span class="sxs-lookup"><span data-stu-id="25ae0-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="25ae0-142">Freigeben von cookie s über Unterdomänen hinweg</span><span class="sxs-lookup"><span data-stu-id="25ae0-142">Share cookies across subdomains</span></span>

<span data-ttu-id="25ae0-143">Beim Hosting von apps, die cookie über Unterdomänen hinweg gemeinsam genutzt werden, geben Sie eine gemeinsame Domäne in an [ Cookie . Domänen](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="25ae0-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="25ae0-144">Wenn Sie cookie s für apps unter `contoso.com` , wie z. b. `first_subdomain.contoso.com` und `second_subdomain.contoso.com` , freigeben `Cookie.Domain` möchten, geben Sie Folgendes an `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="25ae0-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="25ae0-145">Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand</span><span class="sxs-lookup"><span data-stu-id="25ae0-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="25ae0-146">Konfigurieren Sie für Produktions Bereitstellungen den so, dass `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate verschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="25ae0-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="25ae0-147">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="25ae0-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="25ae0-148">Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für Folgendes bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="25ae0-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="25ae0-149">Freigeben cookie von Authentifizierung s zwischen ASP.NET 4. x-und ASP.net Core-apps</span><span class="sxs-lookup"><span data-stu-id="25ae0-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="25ae0-150">ASP.NET 4. x-apps, die Katana- Cookie Authentifizierungs Middleware verwenden, können so konfiguriert werden, dass Sie Authentifizierungen generieren cookie , die mit der ASP.net Core Cookie Authentifizierungs Middleware kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="25ae0-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="25ae0-151">Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.</span><span class="sxs-lookup"><span data-stu-id="25ae0-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="25ae0-152">Wenn eine APP Katana Cookie -Authentifizierungs Middleware verwendet, ruft Sie `UseCookieAuthentication` in der *Startup.auth.cs* -Datei des Projekts auf.</span><span class="sxs-lookup"><span data-stu-id="25ae0-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="25ae0-153">ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana- Cookie Authentifizierungs Middleware.</span><span class="sxs-lookup"><span data-stu-id="25ae0-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="25ae0-154">Obwohl `UseCookieAuthentication` veraltet ist und für ASP.net Core-apps nicht unterstützt wird, `UseCookieAuthentication` ist der Aufruf von in einer ASP.NET 4. x-APP, die die Katana- Cookie Authentifizierungs Middleware verwendet, gültig.</span><span class="sxs-lookup"><span data-stu-id="25ae0-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="25ae0-155">Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein.</span><span class="sxs-lookup"><span data-stu-id="25ae0-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="25ae0-156">Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.</span><span class="sxs-lookup"><span data-stu-id="25ae0-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="25ae0-157">cookieKonfigurieren Sie die ASP.net Core-APP, wie im Abschnitt [Freigeben von Authentifizierungen für cookie ASP.net Core apps](#share-authentication-cookies-with-aspnet-core-identity) angegeben, und konfigurieren Sie die ASP.NET 4. x-APP wie folgt, um die Authentifizierungs-e zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freizugeben.</span><span class="sxs-lookup"><span data-stu-id="25ae0-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="25ae0-158">Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="25ae0-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="25ae0-159">Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.</span><span class="sxs-lookup"><span data-stu-id="25ae0-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="25ae0-160">Suchen und ändern Sie den-Befehl an `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="25ae0-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="25ae0-161">Ändern Sie den Namen so, dass er cookie dem Namen entspricht, der von der ASP.net Core Cookie Authentifizierungs Middleware verwendet wird ( `.AspNet.SharedCookie` in diesem Beispiel).</span><span class="sxs-lookup"><span data-stu-id="25ae0-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="25ae0-162">Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="25ae0-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="25ae0-163">Geben Sie eine Instanz von `DataProtectionProvider` an, die für den Common Data Protection Key-Speicherort initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="25ae0-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="25ae0-164">Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von allen Apps verwendet wird, die Authentifizierungs- cookie e verwenden ( `SharedCookieApp` im Beispiel)</span><span class="sxs-lookup"><span data-stu-id="25ae0-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="25ae0-165">Wenn `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` Sie und nicht festlegen `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , legen <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> Sie auf einen Anspruch fest, der eindeutige Benutzer unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="25ae0-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="25ae0-166">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ae0-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="25ae0-167">Beim Erstellen einer Benutzeridentität muss der Authentifizierungstyp ( `Identity.Application` ) mit dem Typ identisch sein, der in `AuthenticationType` Set with `UseCookieAuthentication` in *App_Start/Startup.auth.cs* definiert ist.</span><span class="sxs-lookup"><span data-stu-id="25ae0-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="25ae0-168">*Modelle/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="25ae0-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="25ae0-169">Verwenden einer allgemeinen Benutzerdatenbank</span><span class="sxs-lookup"><span data-stu-id="25ae0-169">Use a common user database</span></span>

<span data-ttu-id="25ae0-170">Wenn apps dasselbe Schema verwenden Identity (dieselbe Version von Identity ), vergewissern Sie sich, dass das Identity System für jede APP auf dieselbe Benutzerdatenbank verweist.</span><span class="sxs-lookup"><span data-stu-id="25ae0-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="25ae0-171">Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit, wenn versucht wird, die Informationen in der Authentifizierung mit cookie den Informationen in der Datenbank abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="25ae0-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="25ae0-172">Wenn sich das Identity Schema von apps unterscheidet, in der Regel, weil apps verschiedene Versionen verwenden, ist die Identity gemeinsame Nutzung einer gemeinsamen Datenbank basierend auf der neuesten Version von Identity nicht möglich, ohne dass Spalten in den Schemas anderer apps neu zugeordnet und hinzugefügt werden Identity .</span><span class="sxs-lookup"><span data-stu-id="25ae0-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="25ae0-173">Häufig ist es effizienter, die anderen apps so zu aktualisieren, dass Sie die neueste Version verwenden, Identity damit eine gemeinsame Datenbank von den apps gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="25ae0-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25ae0-174">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="25ae0-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
