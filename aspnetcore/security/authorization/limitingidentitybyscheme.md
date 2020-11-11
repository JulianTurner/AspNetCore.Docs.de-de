---
title: Autorisieren mit einem bestimmten Schema in ASP.net Core
author: rick-anderson
description: In diesem Artikel wird erläutert, wie Sie die Identität auf ein bestimmtes Schema beschränken, wenn Sie mit mehreren Authentifizierungsmethoden arbeiten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: a5f2dff7b0e0d4f209ba445b2efb6fb261cbaab1
ms.sourcegitcommit: fbd5427293d9ecccc388bd5fd305c2eb8ada7281
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94464015"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="7ec8e-103">Autorisieren mit einem bestimmten Schema in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="7ec8e-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="7ec8e-104">In einigen Szenarien, z. b. Single-Page-Anwendungen (Spas), ist es üblich, mehrere Authentifizierungsmethoden zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="7ec8e-105">Beispielsweise kann die APP die :::no-loc(cookie)::: -basierte Authentifizierung für die Anmeldung und die JWT-Träger Authentifizierung für JavaScript-Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-105">For example, the app may use :::no-loc(cookie):::-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="7ec8e-106">In einigen Fällen verfügt die APP möglicherweise über mehrere Instanzen eines Authentifizierungs Handlers.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="7ec8e-107">Beispielsweise zwei :::no-loc(cookie)::: Handler, bei denen eine eine grundlegende Identität enthält und eine erstellt wird, wenn eine Multi-Factor Authentication (MFA) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-107">For example, two :::no-loc(cookie)::: handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="7ec8e-108">MFA kann ausgelöst werden, da der Benutzer einen Vorgang angefordert hat, der zusätzliche Sicherheit erfordert.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span> <span data-ttu-id="7ec8e-109">Weitere Informationen zum Erzwingen von MFA, wenn ein Benutzer eine Ressource anfordert, für die MFA erforderlich ist, finden Sie im Abschnitt "GitHub-Problem [Schutz" mit MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span><span class="sxs-lookup"><span data-stu-id="7ec8e-109">For more information on enforcing MFA when a user requests a resource that requires MFA, see the GitHub issue [Protect section with MFA](https://github.com/dotnet/AspNetCore.Docs/issues/15791#issuecomment-580464195).</span></span>

<span data-ttu-id="7ec8e-110">Ein Authentifizierungsschema wird benannt, wenn der Authentifizierungsdienst während der Authentifizierung konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="7ec8e-111">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ec8e-111">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Code omitted for brevity

    services.AddAuthentication()
        .Add:::no-loc(Cookie):::(options => {
            options.LoginPath = "/Account/Unauthorized/";
            options.AccessDeniedPath = "/Account/Forbidden/";
        })
        .AddJwtBearer(options => {
            options.Audience = "http://localhost:5001/";
            options.Authority = "http://localhost:5000/";
        });
```

<span data-ttu-id="7ec8e-112">Im vorangehenden Code wurden zwei Authentifizierungs Handler hinzugefügt: eine für :::no-loc(cookie)::: s und eine für Bearer.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-112">In the preceding code, two authentication handlers have been added: one for :::no-loc(cookie):::s and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="7ec8e-113">Wenn Sie das Standardschema angeben, `HttpContext.User` wird die-Eigenschaft auf diese Identität festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="7ec8e-114">Wenn dieses Verhalten nicht erwünscht ist, deaktivieren Sie es durch Aufrufen der Parameter losen Form von `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="7ec8e-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="7ec8e-115">Auswählen des Schemas mit dem Attribut "autorisieren"</span><span class="sxs-lookup"><span data-stu-id="7ec8e-115">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="7ec8e-116">Zum Zeitpunkt der Autorisierung gibt die APP den zu verwendenden Handler an.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-116">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="7ec8e-117">Wählen Sie den Handler aus, mit dem die APP autorisiert werden soll, indem Sie eine durch Trennzeichen getrennte Liste von Authentifizierungs Schemas an übergeben `[Authorize]` .</span><span class="sxs-lookup"><span data-stu-id="7ec8e-117">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="7ec8e-118">Das- `[Authorize]` Attribut gibt das Authentifizierungsschema oder die zu verwendenden Schemas an, unabhängig davon, ob ein Standard konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-118">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="7ec8e-119">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ec8e-119">For example:</span></span>

```csharp
[Authorize(AuthenticationSchemes = AuthSchemes)]
public class MixedController : Controller
    // Requires the following imports:
    // using Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s;
    // using Microsoft.AspNetCore.Authentication.JwtBearer;
    private const string AuthSchemes =
        :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme + "," +
        JwtBearerDefaults.AuthenticationScheme;
```

<span data-ttu-id="7ec8e-120">Im vorherigen Beispiel werden sowohl der :::no-loc(cookie)::: -als auch der bearerhandler ausgeführt, und Sie haben die Möglichkeit, eine Identität für den aktuellen Benutzer zu erstellen und anzufügen.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-120">In the preceding example, both the :::no-loc(cookie)::: and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="7ec8e-121">Wenn Sie nur ein einzelnes Schema angeben, wird der entsprechende Handler ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-121">By specifying a single scheme only, the corresponding handler runs.</span></span>

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

<span data-ttu-id="7ec8e-122">Im vorangehenden Code wird nur der Handler mit dem Schema "Träger" ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-122">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="7ec8e-123">Alle :::no-loc(cookie)::: -basierten Identitäten werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-123">Any :::no-loc(cookie):::-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="7ec8e-124">Auswählen des Schemas mit Richtlinien</span><span class="sxs-lookup"><span data-stu-id="7ec8e-124">Selecting the scheme with policies</span></span>

<span data-ttu-id="7ec8e-125">Wenn Sie die gewünschten Schemas in der [Richtlinie](xref:security/authorization/policies)angeben möchten, können Sie die `AuthenticationSchemes` Sammlung beim Hinzufügen Ihrer Richtlinie festlegen:</span><span class="sxs-lookup"><span data-stu-id="7ec8e-125">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="7ec8e-126">Im vorherigen Beispiel wird die Richtlinie "Over18" nur für die Identität ausgeführt, die vom Handler "Träger" erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-126">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="7ec8e-127">Verwenden Sie die-Richtlinie durch Festlegen der `[Authorize]` -Eigenschaft des Attributs `Policy` :</span><span class="sxs-lookup"><span data-stu-id="7ec8e-127">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="7ec8e-128">Verwenden mehrerer Authentifizierungs Schemas</span><span class="sxs-lookup"><span data-stu-id="7ec8e-128">Use multiple authentication schemes</span></span>

<span data-ttu-id="7ec8e-129">Einige apps müssen möglicherweise mehrere Arten der Authentifizierung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-129">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="7ec8e-130">Beispielsweise kann Ihre App Benutzer von Azure Active Directory und einer Benutzerdatenbank authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-130">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="7ec8e-131">Ein weiteres Beispiel ist eine APP, die Benutzer sowohl aus Active Directory-Verbunddienste (AD FS) als auch aus Azure Active Directory B2C authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-131">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="7ec8e-132">In diesem Fall sollte die APP ein JWT-bearertoken von mehreren Ausstellern akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-132">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="7ec8e-133">Fügen Sie alle Authentifizierungs Schemas hinzu, die angenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-133">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="7ec8e-134">Mit dem folgenden Code in werden z. b. `Startup.ConfigureServices` zwei JWT-Träger Authentifizierungs Schemas mit unterschiedlichen Ausstellern hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="7ec8e-134">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="7ec8e-135">Nur eine JWT-Träger Authentifizierung wird mit dem Standard Authentifizierungsschema registriert `JwtBearerDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="7ec8e-135">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="7ec8e-136">Die zusätzliche Authentifizierung muss mit einem eindeutigen Authentifizierungsschema registriert werden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-136">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="7ec8e-137">Der nächste Schritt besteht darin, die Standard Autorisierungs Richtlinie so zu aktualisieren, dass beide Authentifizierungs Schemas akzeptiert werden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-137">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="7ec8e-138">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="7ec8e-138">For example:</span></span>

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

<span data-ttu-id="7ec8e-139">Wenn die Standard Autorisierungs Richtlinie überschrieben wird, ist es möglich, das- `[Authorize]` Attribut in Controllern zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-139">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="7ec8e-140">Der Controller akzeptiert dann Anforderungen mit JWT, die vom ersten oder zweiten Aussteller ausgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="7ec8e-140">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end

<span data-ttu-id="7ec8e-141">Weitere Informationen zur Verwendung mehrerer Authentifizierungs Schemas finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/aspnetcore/issues/26002) .</span><span class="sxs-lookup"><span data-stu-id="7ec8e-141">See [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/26002) on using multiple authentication schemes.</span></span>
