---
title: 'Migrieren der Authentifizierung und :::no-loc(Identity)::: zu ASP.net Core 2,0'
author: scottaddie
description: 'In diesem Artikel werden die häufigsten Schritte zum Migrieren ASP.net Core 1. x-Authentifizierung und :::no-loc(Identity)::: zu ASP.net Core 2,0 beschrieben.'
ms.author: scaddie
ms.date: 06/21/2019
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
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057179"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="c99bc-103">Migrieren der Authentifizierung und :::no-loc(Identity)::: zu ASP.net Core 2,0</span><span class="sxs-lookup"><span data-stu-id="c99bc-103">Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0</span></span>

<span data-ttu-id="c99bc-104">Von [Scott Adder](https://github.com/scottaddie) und [Hao Kung](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="c99bc-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="c99bc-105">ASP.net Core 2,0 verfügt über ein neues Modell für die Authentifizierung und [:::no-loc(Identity):::](xref:security/authentication/identity) vereinfacht die Konfiguration mithilfe von Diensten.</span><span class="sxs-lookup"><span data-stu-id="c99bc-105">ASP.NET Core 2.0 has a new model for authentication and [:::no-loc(Identity):::](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="c99bc-106">ASP.net Core 1. x-Anwendungen, die die-Authentifizierung verwenden, oder :::no-loc(Identity)::: können aktualisiert werden, um das neue Modell wie unten beschrieben zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-106">ASP.NET Core 1.x applications that use authentication or :::no-loc(Identity)::: can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="c99bc-107">Namespaces aktualisieren</span><span class="sxs-lookup"><span data-stu-id="c99bc-107">Update namespaces</span></span>

<span data-ttu-id="c99bc-108">In 1. x wurden Klassen wie `:::no-loc(Identity):::Role` und `:::no-loc(Identity):::User` im- `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` Namespace gefunden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-108">In 1.x, classes such `:::no-loc(Identity):::Role` and `:::no-loc(Identity):::User` were found in the `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="c99bc-109">In 2,0 wurde der <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> Namespace zur neuen Startseite für einige dieser Klassen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-109">In 2.0, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="c99bc-110">Mit dem Standard :::no-loc(Identity)::: Code enthalten die betroffenen Klassen `ApplicationUser` und `Startup` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-110">With the default :::no-loc(Identity)::: code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="c99bc-111">Passen Sie `using` die Anweisungen so an, dass die betroffenen Verweise aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="c99bc-112">Authentifizierungs Middleware und-Dienste</span><span class="sxs-lookup"><span data-stu-id="c99bc-112">Authentication Middleware and services</span></span>

<span data-ttu-id="c99bc-113">In 1. x-Projekten wird die Authentifizierung über Middleware konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c99bc-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="c99bc-114">Für jedes Authentifizierungsschema, das Sie unterstützen möchten, wird eine Middleware-Methode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="c99bc-115">Im folgenden 1. x-Beispiel wird die Facebook-Authentifizierung mit :::no-loc(Identity)::: in *Startup.cs* konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="c99bc-115">The following 1.x example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.Use:::no-loc(Identity):::();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="c99bc-116">In 2,0-Projekten wird die Authentifizierung über Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="c99bc-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="c99bc-117">Jedes Authentifizierungsschema ist in der- `ConfigureServices` Methode von *Startup.cs* registriert.</span><span class="sxs-lookup"><span data-stu-id="c99bc-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="c99bc-118">Die- `Use:::no-loc(Identity):::` Methode wird durch ersetzt `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-118">The `Use:::no-loc(Identity):::` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="c99bc-119">Im 2,0 folgenden Beispiel wird die Facebook-Authentifizierung mit :::no-loc(Identity)::: in *Startup.cs* konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="c99bc-119">The following 2.0 example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak :::no-loc(Identity)::: :::no-loc(cookie):::s, they're no longer part of :::no-loc(Identity):::Options.
    services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="c99bc-120">Die- `UseAuthentication` Methode fügt eine einzelne Authentifizierungs Middleware-Komponente hinzu, die für die automatische Authentifizierung und die Behandlung von Remote Authentifizierungsanforderungen zuständig ist.</span><span class="sxs-lookup"><span data-stu-id="c99bc-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="c99bc-121">Es ersetzt alle einzelnen middlewarekomponenten durch eine einzelne, gängige Middlewarekomponente.</span><span class="sxs-lookup"><span data-stu-id="c99bc-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="c99bc-122">Im folgenden finden Sie 2,0 Migrations Anweisungen für jedes wichtige Authentifizierungsschema.</span><span class="sxs-lookup"><span data-stu-id="c99bc-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="c99bc-123">:::no-loc(Cookie):::-basierte Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c99bc-123">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="c99bc-124">Wählen Sie eine der beiden folgenden Optionen aus, und nehmen Sie die erforderlichen Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="c99bc-125">Verwenden :::no-loc(cookie)::: von s mit :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="c99bc-125">Use :::no-loc(cookie):::s with :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="c99bc-126">Ersetzen Sie `Use:::no-loc(Identity):::` durch `UseAuthentication` in der- `Configure` Methode:</span><span class="sxs-lookup"><span data-stu-id="c99bc-126">Replace `Use:::no-loc(Identity):::` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="c99bc-127">Rufen Sie die- `Add:::no-loc(Identity):::` Methode in der- `ConfigureServices` Methode auf, um die :::no-loc(cookie)::: Authentifizierungsdienste hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-127">Invoke the `Add:::no-loc(Identity):::` method in the `ConfigureServices` method to add the :::no-loc(cookie)::: authentication services.</span></span>
    - <span data-ttu-id="c99bc-128">Rufen Sie optional die- `ConfigureApplication:::no-loc(Cookie):::` Methode oder die- `ConfigureExternal:::no-loc(Cookie):::` Methode in der- `ConfigureServices` Methode auf, um die Einstellungen zu optimieren :::no-loc(Identity)::: :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="c99bc-128">Optionally, invoke the `ConfigureApplication:::no-loc(Cookie):::` or `ConfigureExternal:::no-loc(Cookie):::` method in the `ConfigureServices` method to tweak the :::no-loc(Identity)::: :::no-loc(cookie)::: settings.</span></span>

        ```csharp
        services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="c99bc-129">:::no-loc(cookie):::S ohne verwenden:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="c99bc-129">Use :::no-loc(cookie):::s without :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="c99bc-130">Ersetzen Sie den `Use:::no-loc(Cookie):::Authentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-130">Replace the `Use:::no-loc(Cookie):::Authentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="c99bc-131">Rufen Sie `AddAuthentication` die `Add:::no-loc(Cookie):::` Methoden und in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-131">Invoke the `AddAuthentication` and `Add:::no-loc(Cookie):::` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the :::no-loc(cookie)::: to be automatically authenticated and assigned to HttpContext.User,
        // remove the :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
                .Add:::no-loc(Cookie):::(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="c99bc-132">JWT-Träger Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c99bc-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="c99bc-133">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="c99bc-134">Ersetzen Sie den `UseJwtBearerAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-135">Rufen Sie die- `AddJwtBearer` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="c99bc-136">Dieser Code Ausschnitt verwendet nicht :::no-loc(Identity)::: . Daher sollte das Standardschema durch Übergabe an die-Methode festgelegt werden `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-136">This code snippet doesn't use :::no-loc(Identity):::, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="c99bc-137">OpenID Connect-Authentifizierung (oidc)</span><span class="sxs-lookup"><span data-stu-id="c99bc-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="c99bc-138">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="c99bc-139">Ersetzen Sie den `UseOpenIdConnectAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-140">Rufen Sie die- `AddOpenIdConnect` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="c99bc-141">Ersetzen Sie die- `PostLogoutRedirectUri` Eigenschaft in der `OpenIdConnectOptions` Aktion durch `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="c99bc-142">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c99bc-142">Facebook authentication</span></span>

<span data-ttu-id="c99bc-143">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="c99bc-144">Ersetzen Sie den `UseFacebookAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-145">Rufen Sie die- `AddFacebook` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="c99bc-146">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c99bc-146">Google authentication</span></span>

<span data-ttu-id="c99bc-147">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="c99bc-148">Ersetzen Sie den `UseGoogleAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-149">Rufen Sie die- `AddGoogle` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="c99bc-150">Authentifizierung über Microsoft-Konto</span><span class="sxs-lookup"><span data-stu-id="c99bc-150">Microsoft Account authentication</span></span>

<span data-ttu-id="c99bc-151">Weitere Informationen zur Microsoft-Konto-Authentifizierung finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span><span class="sxs-lookup"><span data-stu-id="c99bc-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="c99bc-152">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="c99bc-153">Ersetzen Sie den `UseMicrosoftAccountAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-154">Rufen Sie die- `AddMicrosoftAccount` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="c99bc-155">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="c99bc-155">Twitter authentication</span></span>

<span data-ttu-id="c99bc-156">Nehmen Sie die folgenden Änderungen in *Startup.cs* vor:</span><span class="sxs-lookup"><span data-stu-id="c99bc-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="c99bc-157">Ersetzen Sie den `UseTwitterAuthentication` Methodenaufrufe in der- `Configure` Methode durch `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="c99bc-158">Rufen Sie die- `AddTwitter` Methode in der- `ConfigureServices` Methode auf:</span><span class="sxs-lookup"><span data-stu-id="c99bc-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="c99bc-159">Festlegen von Standard Authentifizierungs Schemas</span><span class="sxs-lookup"><span data-stu-id="c99bc-159">Setting default authentication schemes</span></span>

<span data-ttu-id="c99bc-160">In 1. x wollten die `AutomaticAuthenticate` -Eigenschaft und die-Eigenschaft `AutomaticChallenge` der [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) -Basisklasse für ein einzelnes Authentifizierungsschema festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="c99bc-161">Es gab keinen guten Weg, dies zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="c99bc-162">In 2,0 wurden diese beiden Eigenschaften als Eigenschaften für die jeweilige `AuthenticationOptions` Instanz entfernt.</span><span class="sxs-lookup"><span data-stu-id="c99bc-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="c99bc-163">Sie können im `AddAuthentication` Methoden Befehl innerhalb der- `ConfigureServices` Methode von *Startup.cs* konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="c99bc-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="c99bc-164">Im vorangehenden Code Ausschnitt ist das Standardschema auf `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") festgelegt.</span><span class="sxs-lookup"><span data-stu-id="c99bc-164">In the preceding code snippet, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="c99bc-165">Verwenden Sie alternativ eine überladene Version der- `AddAuthentication` Methode, um mehr als eine Eigenschaft festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="c99bc-166">Im folgenden Beispiel für eine überladene Methode wird das Standardschema auf festgelegt `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-166">In the following overloaded method example, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="c99bc-167">Das Authentifizierungsschema kann alternativ in ihren einzelnen `[Authorize]` Attributen oder Autorisierungs Richtlinien angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="c99bc-168">Definieren Sie ein Standardschema in 2,0, wenn eine der folgenden Bedingungen zutrifft:</span><span class="sxs-lookup"><span data-stu-id="c99bc-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="c99bc-169">Sie möchten, dass der Benutzer automatisch angemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="c99bc-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="c99bc-170">Sie verwenden die `[Authorize]` Attribute oder Autorisierungs Richtlinien ohne Angabe von Schemas.</span><span class="sxs-lookup"><span data-stu-id="c99bc-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="c99bc-171">Eine Ausnahme von dieser Regel ist die- `Add:::no-loc(Identity):::` Methode.</span><span class="sxs-lookup"><span data-stu-id="c99bc-171">An exception to this rule is the `Add:::no-loc(Identity):::` method.</span></span> <span data-ttu-id="c99bc-172">Mit dieser Methode werden :::no-loc(cookie)::: für Sie hinzugefügt, und die standardmäßigen authentifizierten und Challenge-Schemas werden für die Anwendung festgelegt :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-172">This method adds :::no-loc(cookie):::s for you and sets the default authenticate and challenge schemes to the application :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme`.</span></span> <span data-ttu-id="c99bc-173">Außerdem wird das Standard Anmelde Schema auf das externe festgelegt :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-173">Additionally, it sets the default sign-in scheme to the external :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="c99bc-174">Verwenden von HttpContext-Authentifizierungs Erweiterungen</span><span class="sxs-lookup"><span data-stu-id="c99bc-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="c99bc-175">Die- `IAuthenticationManager` Schnittstelle ist der Haupteinstiegspunkt in das 1. x-Authentifizierungssystem.</span><span class="sxs-lookup"><span data-stu-id="c99bc-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="c99bc-176">Sie wurde durch einen neuen Satz von `HttpContext` Erweiterungs Methoden im- `Microsoft.AspNetCore.Authentication` Namespace ersetzt.</span><span class="sxs-lookup"><span data-stu-id="c99bc-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="c99bc-177">Beispielsweise verweisen 1. x-Projekte auf eine `Authentication` Eigenschaft:</span><span class="sxs-lookup"><span data-stu-id="c99bc-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c99bc-178">Importieren Sie in 2,0-Projekten den `Microsoft.AspNetCore.Authentication` -Namespace, und löschen Sie die `Authentication` Eigenschafts Verweise:</span><span class="sxs-lookup"><span data-stu-id="c99bc-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="c99bc-179">Windows-Authentifizierung (HTTP.sys/IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="c99bc-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="c99bc-180">Es gibt zwei Variationen der Windows-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="c99bc-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="c99bc-181">Der Host erlaubt nur authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="c99bc-181">The host only allows authenticated users.</span></span> <span data-ttu-id="c99bc-182">Diese Variation wirkt sich nicht auf die 2,0-Änderungen aus.</span><span class="sxs-lookup"><span data-stu-id="c99bc-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="c99bc-183">Der Host ermöglicht sowohl anonyme als auch authentifizierte Benutzer.</span><span class="sxs-lookup"><span data-stu-id="c99bc-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="c99bc-184">Diese Variation ist von den 2,0-Änderungen betroffen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="c99bc-185">Beispielsweise sollte die APP anonyme Benutzer auf der [IIS](xref:host-and-deploy/iis/index) -oder [HTTP.sys](xref:fundamentals/servers/httpsys) Schicht zulassen, aber Benutzer auf Controller Ebene autorisieren.</span><span class="sxs-lookup"><span data-stu-id="c99bc-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="c99bc-186">Legen Sie in diesem Szenario das Standardschema in der- `Startup.ConfigureServices` Methode fest.</span><span class="sxs-lookup"><span data-stu-id="c99bc-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="c99bc-187">Legen Sie für [Microsoft. aspnetcore. Server. IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)das Standardschema auf Folgendes fest `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="c99bc-188">Legen Sie für [Microsoft. aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)das Standardschema auf Folgendes fest `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="c99bc-189">Wenn Sie das Standardschema nicht festlegen, wird verhindert, dass die Autorisierungs Anforderung (Challenge) mit der folgenden Ausnahme funktioniert:</span><span class="sxs-lookup"><span data-stu-id="c99bc-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="c99bc-190">`System.InvalidOperationException`: Es wurde kein "authenticationScheme" angegeben, und es wurde kein defaultherausfordergescheme gefunden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="c99bc-191">Weitere Informationen finden Sie unter <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="c99bc-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-:::no-loc(cookie):::-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="c99bc-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options Instanzen</span><span class="sxs-lookup"><span data-stu-id="c99bc-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options instances</span></span>

<span data-ttu-id="c99bc-193">Ein Nebeneffekt der 2,0-Änderungen ist der Wechsel zur Verwendung von benannten Optionen anstelle von :::no-loc(cookie)::: options Instanzen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-193">A side effect of the 2.0 changes is the switch to using named options instead of :::no-loc(cookie)::: options instances.</span></span> <span data-ttu-id="c99bc-194">Die Möglichkeit zum Anpassen der :::no-loc(Identity)::: :::no-loc(cookie)::: Schema Namen wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="c99bc-194">The ability to customize the :::no-loc(Identity)::: :::no-loc(cookie)::: scheme names is removed.</span></span>

<span data-ttu-id="c99bc-195">Beispielsweise verwenden 1. x-Projekte die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) , um einen `:::no-loc(Identity)::::::no-loc(Cookie):::Options` Parameter an *AccountController.cs* und *ManageController.cs* zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="c99bc-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="c99bc-196">Der Zugriff auf das externe :::no-loc(cookie)::: Authentifizierungsschema erfolgt über die bereitgestellte-Instanz:</span><span class="sxs-lookup"><span data-stu-id="c99bc-196">The external :::no-loc(cookie)::: authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="c99bc-197">Die oben genannte Konstruktorinjektion ist in 2,0-Projekten unnötig, und das `_external:::no-loc(Cookie):::Scheme` Feld kann gelöscht werden:</span><span class="sxs-lookup"><span data-stu-id="c99bc-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_external:::no-loc(Cookie):::Scheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="c99bc-198">1. x-Projekte haben das `_external:::no-loc(Cookie):::Scheme` Feld wie folgt verwendet:</span><span class="sxs-lookup"><span data-stu-id="c99bc-198">1.x projects used the `_external:::no-loc(Cookie):::Scheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c99bc-199">Ersetzen Sie in 2,0-Projekten den vorangehenden Code durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="c99bc-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="c99bc-200">Die `:::no-loc(Identity):::Constants.ExternalScheme` Konstante kann direkt verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c99bc-200">The `:::no-loc(Identity):::Constants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="c99bc-201">Beheben Sie den neu hinzugefügten-Befehl, `SignOutAsync` indem Sie den folgenden Namespace importieren:</span><span class="sxs-lookup"><span data-stu-id="c99bc-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="c99bc-202">:::no-loc(Identity):::Benutzer poco-Navigations Eigenschaften hinzufügen</span><span class="sxs-lookup"><span data-stu-id="c99bc-202">Add :::no-loc(Identity):::User POCO navigation properties</span></span>

<span data-ttu-id="c99bc-203">Die Entity Framework (EF)-Kern Navigations Eigenschaften des poco-Basis `:::no-loc(Identity):::User` Objekts (Plain Old CLR Object) wurden entfernt.</span><span class="sxs-lookup"><span data-stu-id="c99bc-203">The Entity Framework (EF) Core navigation properties of the base `:::no-loc(Identity):::User` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="c99bc-204">Wenn Ihr 1. x-Projekt diese Eigenschaften verwendet hat, fügen Sie Sie dem Projekt 2,0 manuell wieder hinzu:</span><span class="sxs-lookup"><span data-stu-id="c99bc-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserRole<int>> Roles { get; } = new List<:::no-loc(Identity):::UserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserClaim<int>> Claims { get; } = new List<:::no-loc(Identity):::UserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserLogin<int>> Logins { get; } = new List<:::no-loc(Identity):::UserLogin<int>>();
```

<span data-ttu-id="c99bc-205">Um beim Ausführen EF Core Migrationen doppelte Fremdschlüssel zu verhindern, fügen Sie der- `:::no-loc(Identity):::DbContext` `OnModelCreating` Methode (nach dem-Befehl) Folgendes hinzu `base.OnModelCreating();` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `:::no-loc(Identity):::DbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
    // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="c99bc-206">Ersetzen von getexternalauthenticationschemas</span><span class="sxs-lookup"><span data-stu-id="c99bc-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="c99bc-207">Die synchrone Methode `GetExternalAuthenticationSchemes` wurde entfernt, um eine asynchrone Version zu bevorzugen.</span><span class="sxs-lookup"><span data-stu-id="c99bc-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="c99bc-208">1. x-Projekte verfügen über den folgenden Code in *Controllers/managecontroller. cs* :</span><span class="sxs-lookup"><span data-stu-id="c99bc-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="c99bc-209">Diese Methode wird auch in *views/Account/Login. cshtml* angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c99bc-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="c99bc-210">Verwenden Sie in 2,0-Projekten die- <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> Methode.</span><span class="sxs-lookup"><span data-stu-id="c99bc-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="c99bc-211">Die Änderung in *ManageController.cs* ähnelt dem folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="c99bc-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="c99bc-212">In *Login. cshtml* wird die `AuthenticationScheme` Eigenschaft, auf die in der Schleife zugegriffen wird, in `foreach` geändert `Name` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="c99bc-213">Änderung der manageloginsviewmodel-Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="c99bc-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="c99bc-214">Ein- `ManageLoginsViewModel` Objekt wird in der `ManageLogins` *ManageController.cs* -Aktion verwendet.</span><span class="sxs-lookup"><span data-stu-id="c99bc-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="c99bc-215">In 1. x-Projekten `OtherLogins` lautet der Rückgabetyp des-Objekts `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="c99bc-216">Dieser Rückgabetyp erfordert den Import von `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="c99bc-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="c99bc-217">In 2,0-Projekten wird der Rückgabetyp in geändert `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="c99bc-218">Dieser neue Rückgabetyp erfordert, dass der `Microsoft.AspNetCore.Http.Authentication` Import durch einen Import ersetzt wird `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="c99bc-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="c99bc-219">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c99bc-219">Additional resources</span></span>

<span data-ttu-id="c99bc-220">Weitere Informationen finden Sie in der [Erörterung des Themas auth 2,0](https://github.com/aspnet/Security/issues/1338) auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="c99bc-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
