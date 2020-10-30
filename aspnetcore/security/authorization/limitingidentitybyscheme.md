---
title: Autorisieren mit einem bestimmten Schema in ASP.net Core
author: rick-anderson
description: In diesem Artikel wird erläutert, wie Sie die Identität auf ein bestimmtes Schema beschränken, wenn Sie mit mehreren Authentifizierungsmethoden arbeiten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 4dc86480d40d8ee40b3c03aa7fd2994e6c15b105
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053123"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a>Autorisieren mit einem bestimmten Schema in ASP.net Core

In einigen Szenarien, z. b. Single-Page-Anwendungen (Spas), ist es üblich, mehrere Authentifizierungsmethoden zu verwenden. Beispielsweise kann die APP die cookie -basierte Authentifizierung für die Anmeldung und die JWT-Träger Authentifizierung für JavaScript-Anforderungen verwenden. In einigen Fällen verfügt die APP möglicherweise über mehrere Instanzen eines Authentifizierungs Handlers. Beispielsweise zwei cookie Handler, bei denen eine eine grundlegende Identität enthält und eine erstellt wird, wenn eine Multi-Factor Authentication (MFA) ausgelöst wurde. MFA kann ausgelöst werden, da der Benutzer einen Vorgang angefordert hat, der zusätzliche Sicherheit erfordert. Weitere Informationen zum Erzwingen von MFA, wenn ein Benutzer eine Ressource anfordert, für die MFA erforderlich ist, finden Sie im Abschnitt "GitHub-Problem [Schutz" mit MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).

Ein Authentifizierungsschema wird benannt, wenn der Authentifizierungsdienst während der Authentifizierung konfiguriert wird. Zum Beispiel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .AddCookie(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

Im vorangehenden Code wurden zwei Authentifizierungs Handler hinzugefügt: eine für cookie s und eine für Bearer.

>[!NOTE]
>Wenn Sie das Standardschema angeben, `HttpContext.User` wird die-Eigenschaft auf diese Identität festgelegt. Wenn dieses Verhalten nicht erwünscht ist, deaktivieren Sie es durch Aufrufen der Parameter losen Form von `AddAuthentication` .

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a>Auswählen des Schemas mit dem Attribut "autorisieren"

Zum Zeitpunkt der Autorisierung gibt die APP den zu verwendenden Handler an. Wählen Sie den Handler aus, mit dem die APP autorisiert werden soll, indem Sie eine durch Trennzeichen getrennte Liste von Authentifizierungs Schemas an übergeben `[Authorize]` . Das- `[Authorize]` Attribut gibt das Authentifizierungsschema oder die zu verwendenden Schemas an, unabhängig davon, ob ein Standard konfiguriert ist. Zum Beispiel:

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.Cookies;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        CookieAuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

Im vorherigen Beispiel werden sowohl der cookie -als auch der bearerhandler ausgeführt, und Sie haben die Möglichkeit, eine Identität für den aktuellen Benutzer zu erstellen und anzufügen. Wenn Sie nur ein einzelnes Schema angeben, wird der entsprechende Handler ausgeführt.

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

Im vorangehenden Code wird nur der Handler mit dem Schema "Träger" ausgeführt. Alle cookie -basierten Identitäten werden ignoriert.

## <a name="selecting-the-scheme-with-policies"></a>Auswählen des Schemas mit Richtlinien

Wenn Sie die gewünschten Schemas in der [Richtlinie](xref:security/authorization/policies)angeben möchten, können Sie die `AuthenticationSchemes` Sammlung beim Hinzufügen Ihrer Richtlinie festlegen:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("Over18", policy =>
    {
        policy.AuthenticationSchemes.Add(JwtBearerDefaults.AuthenticationScheme);
        policy.RequireAuthenticatedUser();
        policy.Requirements.Add(new MinimumAgeRequirement());
    });
});
```

Im vorherigen Beispiel wird die Richtlinie "Over18" nur für die Identität ausgeführt, die vom Handler "Träger" erstellt wurde. Verwenden Sie die-Richtlinie durch Festlegen der `[Authorize]` -Eigenschaft des Attributs `Policy` :

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a>Verwenden mehrerer Authentifizierungs Schemas

Einige apps müssen möglicherweise mehrere Arten der Authentifizierung unterstützen. Beispielsweise kann Ihre App Benutzer von Azure Active Directory und einer Benutzerdatenbank authentifizieren. Ein weiteres Beispiel ist eine APP, die Benutzer sowohl aus Active Directory-Verbunddienste (AD FS) als auch aus Azure Active Directory B2C authentifiziert. In diesem Fall sollte die APP ein JWT-bearertoken von mehreren Ausstellern akzeptieren.

Fügen Sie alle Authentifizierungs Schemas hinzu, die angenommen werden sollen. Mit dem folgenden Code in werden z. b. `Startup.ConfigureServices` zwei JWT-Träger Authentifizierungs Schemas mit unterschiedlichen Ausstellern hinzugefügt:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddJwtBearer(options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://localhost:5000/identity/";
        })
        .AddJwtBearer("AzureAD", options =>
        {
            options.Audience = "https://localhost:5000/";
            options.Authority = "https://login.microsoftonline.com/eb971100-6f99-4bdc-8611-1bc8edd7f436/";
        });
}
```

> [!NOTE]
> Nur eine JWT-Träger Authentifizierung wird mit dem Standard Authentifizierungsschema registriert `JwtBearerDefaults.AuthenticationScheme` . Die zusätzliche Authentifizierung muss mit einem eindeutigen Authentifizierungsschema registriert werden.

Der nächste Schritt besteht darin, die Standard Autorisierungs Richtlinie so zu aktualisieren, dass beide Authentifizierungs Schemas akzeptiert werden. Zum Beispiel:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthorization(options =>
    {
        var defaultAuthorizationPolicyBuilder = new AuthorizationPolicyBuilder(
            JwtBearerDefaults.AuthenticationScheme,
            "AzureAD");
        defaultAuthorizationPolicyBuilder = 
            defaultAuthorizationPolicyBuilder.RequireAuthenticatedUser();
        options.DefaultPolicy = defaultAuthorizationPolicyBuilder.Build();
    });
}
```

Wenn die Standard Autorisierungs Richtlinie überschrieben wird, ist es möglich, das- `[Authorize]` Attribut in Controllern zu verwenden. Der Controller akzeptiert dann Anforderungen mit JWT, die vom ersten oder zweiten Aussteller ausgegeben wurden.

::: moniker-end
