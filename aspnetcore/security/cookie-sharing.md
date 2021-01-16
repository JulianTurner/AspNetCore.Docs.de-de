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
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a>Freigeben von Authentifizierungs- cookie e unter ASP.net-apps

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten. Zum Bereitstellen eines Single Sign-on (SSO) müssen Web-Apps innerhalb einer Website Authentifizierung cookie s freigeben. Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana cookie -Authentifizierung und ASP.net Core cookie Authentifizierungs Tickets.

In den folgenden Beispielen:

* Der Authentifizierungs cookie Name wird auf einen gemeinsamen Wert von festgelegt `.AspNet.SharedCookie` .
* Der `AuthenticationType` wird `Identity.Application` entweder explizit oder standardmäßig auf festgelegt.
* Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz Schlüsseln () zu ermöglichen `SharedCookieApp` .
* `Identity.Application` wird als Authentifizierungsschema verwendet. Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Apps verwendet werden, cookie entweder als Standardschema oder explizit festgelegt. Das Schema wird beim Verschlüsseln und Entschlüsseln von cookie s verwendet. Daher muss ein konsistentes Schema zwischen allen Apps verwendet werden.
* Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.
  * In ASP.net Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Speicherort des Schlüssels festzulegen.
  * In .NET Framework-apps Cookie verwendet die Authentifizierungs Middleware eine Implementierung von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> . `DataProtectionProvider` stellt Datenschutzdienste für die Verschlüsselung und Entschlüsselung von Authentifizierungs cookie Nutzlastdaten bereit. Die- `DataProtectionProvider` Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird. [Dataschutzprovider. Create (System. IO. Director yinfo, Action \<IDataProtectionBuilder> )](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo> , um den Speicherort für den Datenschutz Schlüssel anzugeben.
* `DataProtectionProvider` erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":
  * Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".
  * Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Legt den Namen der allgemeinen App fest.

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a>Freigeben cookie von Authentifizierung s mit ASP.NET Core Identity

Folgendes ist bei Verwendung von ASP.NET Core Identity zu beachten:

* Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden. In den folgenden Beispielen wird die-Methode mit einem allgemeinen Schlüssel Speicherort bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> . Verwenden <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Sie, um einen gemeinsamen freigegebenen APP-Namen ( `SharedCookieApp` in den folgenden Beispielen) zu konfigurieren. Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.
* Verwenden Sie die- <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> Erweiterungsmethode, um den Datenschutz Dienst für cookie s einzurichten.
* Der Standard Authentifizierungstyp ist `Identity.Application` .

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

**Hinweis:** Die vorangehenden Anweisungen funktionieren nicht mit `ITicketStore` ( `CookieAuthenticationOptions.SessionStore` ).  Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a>Freigeben von Authentifizierung cookie s ohne ASP.NET Core Identity

Wenn Sie cookie e direkt ohne verwenden ASP.NET Core Identity , konfigurieren Sie den Datenschutz und die Authentifizierung in `Startup.ConfigureServices` . Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `Identity.Application` :

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

## <a name="share-no-loccookies-across-different-base-paths"></a>Freigeben von cookie s über verschiedene Basis Pfade hinweg

Eine Authentifizierung cookie verwendet [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) als Standardwert [ Cookie . Der Pfad](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Wenn die der APP cookie über verschiedene Basis Pfade gemeinsam genutzt werden muss, `Path` muss überschrieben werden:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a>Freigeben von cookie s über Unterdomänen hinweg

Beim Hosting von apps, die cookie über Unterdomänen hinweg gemeinsam genutzt werden, geben Sie eine gemeinsame Domäne in an [ Cookie . Domänen](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) Eigenschaft. Wenn Sie cookie s für apps unter `contoso.com` , wie z. b. `first_subdomain.contoso.com` und `second_subdomain.contoso.com` , freigeben `Cookie.Domain` möchten, geben Sie Folgendes an `.contoso.com` :

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand

Konfigurieren Sie für Produktions Bereitstellungen den so, dass `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate verschlüsselt werden. Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>. Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für Folgendes bereitgestellt <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben cookie von Authentifizierung s zwischen ASP.NET 4. x-und ASP.net Core-apps

ASP.NET 4. x-apps, die Katana- Cookie Authentifizierungs Middleware verwenden, können so konfiguriert werden, dass Sie Authentifizierungen generieren cookie , die mit der ASP.net Core Cookie Authentifizierungs Middleware kompatibel sind. Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.

Wenn eine APP Katana Cookie -Authentifizierungs Middleware verwendet, ruft Sie `UseCookieAuthentication` in der *Startup.auth.cs* -Datei des Projekts auf. ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana- Cookie Authentifizierungs Middleware. Obwohl `UseCookieAuthentication` veraltet ist und für ASP.net Core-apps nicht unterstützt wird, `UseCookieAuthentication` ist der Aufruf von in einer ASP.NET 4. x-APP, die die Katana- Cookie Authentifizierungs Middleware verwendet, gültig.

Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein. Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.

cookieKonfigurieren Sie die ASP.net Core-APP, wie im Abschnitt [Freigeben von Authentifizierungen für cookie ASP.net Core apps](#share-authentication-cookies-with-aspnet-core-identity) angegeben, und konfigurieren Sie die ASP.NET 4. x-APP wie folgt, um die Authentifizierungs-e zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freizugeben.

Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden. Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.

Suchen und ändern Sie den-Befehl an `UseCookieAuthentication` :

* Ändern Sie den Namen so, dass er cookie dem Namen entspricht, der von der ASP.net Core Cookie Authentifizierungs Middleware verwendet wird ( `.AspNet.SharedCookie` in diesem Beispiel).
* Im folgenden Beispiel wird der Authentifizierungstyp auf festgelegt `Identity.Application` .
* Geben Sie eine Instanz von `DataProtectionProvider` an, die für den Common Data Protection Key-Speicherort initialisiert wurde.
* Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von allen Apps verwendet wird, die Authentifizierungs- cookie e verwenden ( `SharedCookieApp` im Beispiel)

Wenn `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` Sie und nicht festlegen `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , legen <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> Sie auf einen Anspruch fest, der eindeutige Benutzer unterscheidet.

*App_Start/Startup.auth.cs*:

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

Beim Erstellen einer Benutzeridentität muss der Authentifizierungstyp ( `Identity.Application` ) mit dem Typ identisch sein, der in `AuthenticationType` Set with `UseCookieAuthentication` in *App_Start/Startup.auth.cs* definiert ist.

*Modelle/ Identity Models.cs*:

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

## <a name="use-a-common-user-database"></a>Verwenden einer allgemeinen Benutzerdatenbank

Wenn apps dasselbe Schema verwenden Identity (dieselbe Version von Identity ), vergewissern Sie sich, dass das Identity System für jede APP auf dieselbe Benutzerdatenbank verweist. Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit, wenn versucht wird, die Informationen in der Authentifizierung mit cookie den Informationen in der Datenbank abzugleichen.

Wenn sich das Identity Schema von apps unterscheidet, in der Regel, weil apps verschiedene Versionen verwenden, ist die Identity gemeinsame Nutzung einer gemeinsamen Datenbank basierend auf der neuesten Version von Identity nicht möglich, ohne dass Spalten in den Schemas anderer apps neu zugeordnet und hinzugefügt werden Identity . Häufig ist es effizienter, die anderen apps so zu aktualisieren, dass Sie die neueste Version verwenden, Identity damit eine gemeinsame Datenbank von den apps gemeinsam genutzt werden kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/web-farm>
