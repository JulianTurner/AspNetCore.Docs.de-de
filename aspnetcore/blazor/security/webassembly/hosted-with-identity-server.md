---
title: Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server
author: guardrex
description: In diesem Artikel finden Sie Informationen zum Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: fdd7eb3c4a3b07022760a43cbde80838bfaf7c84
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024794"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="76b2f-103">Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server</span><span class="sxs-lookup"><span data-stu-id="76b2f-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="76b2f-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="76b2f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="76b2f-105">In diesem Artikel wird erläutert, wie eine von [ gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellt wird, die [IdentityServer](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="76b2f-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="76b2f-106">Um eine eigenständige oder gehostete Blazor WebAssembly-App für die Verwendung einer vorhandenen externen Identity Server-Instanz zu konfigurieren, befolgen Sie die Anweisungen in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="76b2f-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="76b2f-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="76b2f-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="76b2f-108">So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:</span><span class="sxs-lookup"><span data-stu-id="76b2f-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="76b2f-109">Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor WebAssembly-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="76b2f-110">Wählen Sie **Einzelne Benutzerkonten** mit der Option **In-App-Speicherung von Benutzerkonten** aus, um Benutzer über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core innerhalb der App zu speichern.</span><span class="sxs-lookup"><span data-stu-id="76b2f-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="76b2f-111">Aktivieren Sie im Bereich **Erweitert** das Kontrollkästchen **Von ASP.NET Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="76b2f-112">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="76b2f-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="76b2f-113">Um ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus in einem leeren Ordner zu erstellen, geben Sie den Authentifizierungsmechanismus `Individual` mit der `-au|--auth`Option an, Benutzer innerhalb der App über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core zu speichern:</span><span class="sxs-lookup"><span data-stu-id="76b2f-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="76b2f-114">Platzhalter</span><span class="sxs-lookup"><span data-stu-id="76b2f-114">Placeholder</span></span>  | <span data-ttu-id="76b2f-115">Beispiel</span><span class="sxs-lookup"><span data-stu-id="76b2f-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="76b2f-116">Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="76b2f-117">Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.</span><span class="sxs-lookup"><span data-stu-id="76b2f-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="76b2f-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="76b2f-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="76b2f-119">So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:</span><span class="sxs-lookup"><span data-stu-id="76b2f-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="76b2f-120">Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor WebAssembly-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="76b2f-121">Die App wird für einzelne Benutzer erstellt, die über [Identity](xref:security/authentication/identity) von ASP.NET Core in der App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="76b2f-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="76b2f-122">Aktivieren Sie das Kontrollkästchen **Von ASP.NET Core gehostet**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="76b2f-123">*`Server`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76b2f-123">*`Server`* app configuration</span></span>

<span data-ttu-id="76b2f-124">In den folgenden Abschnitten werden Ergänzungen zum Projekt erläutert, wenn die Authentifizierungsunterstützung eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="76b2f-125">Startklasse</span><span class="sxs-lookup"><span data-stu-id="76b2f-125">Startup class</span></span>

<span data-ttu-id="76b2f-126">Die `Startup`-Klasse verfügt über die folgenden Ergänzungen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="76b2f-127">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76b2f-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="76b2f-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="76b2f-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="76b2f-129">IdentityServer mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode, die ASP.NET Core-Standardkonventionen zusätzlich zu IdentityServer einrichtet:</span><span class="sxs-lookup"><span data-stu-id="76b2f-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="76b2f-130">Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsmethode, die die App so konfiguriert, dass sie von IdentityServer generierte JWT-Token überprüft:</span><span class="sxs-lookup"><span data-stu-id="76b2f-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="76b2f-131">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="76b2f-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="76b2f-132">Die IdentityServer-Middleware stellt die Open ID Connect-Endpunkte (OIDC) bereit:</span><span class="sxs-lookup"><span data-stu-id="76b2f-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="76b2f-133">Die Authentifizierungsmiddleware ist für die Überprüfung der Anforderungsanmeldeinformationen und für das Festlegen des Benutzers auf den Anforderungskontext verantwortlich:</span><span class="sxs-lookup"><span data-stu-id="76b2f-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="76b2f-134">Die Autorisierungsmiddleware aktiviert Autorisierungsfunktionen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="76b2f-135">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="76b2f-135">AddApiAuthorization</span></span>

<span data-ttu-id="76b2f-136">Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode konfiguriert [IdentityServer](https://identityserver.io/) für ASP.NET Core-Szenarien.</span><span class="sxs-lookup"><span data-stu-id="76b2f-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="76b2f-137">IdentityServer ist ein leistungsfähiges und erweiterbares Framework für Überlegungen zum Thema „App-Sicherheit“.</span><span class="sxs-lookup"><span data-stu-id="76b2f-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="76b2f-138">IdentityServer ist für die meisten gängigen Szenarien unnötig komplex.</span><span class="sxs-lookup"><span data-stu-id="76b2f-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="76b2f-139">Als Folge werden mehrere Konventionen und Konfigurationsoptionen bereitgestellt, die sich gut als Startpunkt eignen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="76b2f-140">Wenn sich Ihre Anforderungen an die Authentifizierung ändern, bietet IdentityServer eine Vielzahl leistungsfähiger Funktionen, mit denen Sie die Authentifizierung genau an die Anforderungen einer App anpassen können.</span><span class="sxs-lookup"><span data-stu-id="76b2f-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="76b2f-141">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="76b2f-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="76b2f-142">Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsprogrammmethode konfiguriert ein Richtlinienschema für die App als Standardauthentifizierungshandler.</span><span class="sxs-lookup"><span data-stu-id="76b2f-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="76b2f-143">Die Richtlinie ist so konfiguriert, dass Identity alle an beliebige Unterpfade im Identity-URL-Raum `/Identity` weitergeleiteten Anforderungen verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="76b2f-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="76b2f-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="76b2f-145">Außerdem übernimmt diese Methode die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="76b2f-145">Additionally, this method:</span></span>

* <span data-ttu-id="76b2f-146">Registrieren einer `{APPLICATION NAME}API`-API-Ressource mit IdentityServer mit dem Standardbereich `{APPLICATION NAME}API`</span><span class="sxs-lookup"><span data-stu-id="76b2f-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="76b2f-147">Konfigurieren der JWT-Bearertoken-Middleware zum Überprüfen der von IdentityServer für die App ausgegebenen Token</span><span class="sxs-lookup"><span data-stu-id="76b2f-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="76b2f-148">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="76b2f-148">WeatherForecastController</span></span>

<span data-ttu-id="76b2f-149">In `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Klasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="76b2f-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="76b2f-150">Das Attribut gibt an, dass der Benutzer basierend auf der Standardrichtlinie autorisiert werden muss, um auf die Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="76b2f-151">Die Standardautorisierungsrichtlinie ist so konfiguriert, dass das Standardauthentifizierungsschema verwendet wird, das von <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> eingerichtet wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="76b2f-152">Die Hilfsprogrammmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standardhandler für Anforderungen an die App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="76b2f-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="76b2f-153">ApplicationDbContext</span></span>

<span data-ttu-id="76b2f-154">In `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) wird <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> durch <xref:Microsoft.EntityFrameworkCore.DbContext> erweitert, damit das Schema für IdentityServer eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="76b2f-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="76b2f-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="76b2f-156">Für eine vollständige Kontrolle des Datenbankschemas wird von einer der verfügbaren Identity-<xref:Microsoft.EntityFrameworkCore.DbContext>-Klassen geerbt, und der Kontext wird so konfiguriert, dass er das Identity-Schema einschließt, indem `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="76b2f-157">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="76b2f-157">OidcConfigurationController</span></span>

<span data-ttu-id="76b2f-158">In `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) wird der Clientendpunkt bereitgestellt, um OIDC-Parameter bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="76b2f-159">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="76b2f-159">App settings</span></span>

<span data-ttu-id="76b2f-160">In der App-Einstellungsdatei (`appsettings.json`) beschreibt der Bereich `IdentityServer` auf Projektstammebene die Liste der konfigurierten Clients.</span><span class="sxs-lookup"><span data-stu-id="76b2f-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="76b2f-161">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="76b2f-161">In the following example, there's a single client.</span></span> <span data-ttu-id="76b2f-162">Der Clientname entspricht dem App-Namen, und er wird durch Konventionen dem OAuth-`ClientId`-Parameter zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="76b2f-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="76b2f-163">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="76b2f-164">Das Profil wird intern verwendet, um Konventionen zu etablieren, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="76b2f-165">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="76b2f-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="76b2f-166">*`Client`* -App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76b2f-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="76b2f-167">Authentifizierungspaket</span><span class="sxs-lookup"><span data-stu-id="76b2f-167">Authentication package</span></span>

<span data-ttu-id="76b2f-168">Wenn eine App erstellt wird, die einzelne Benutzerkonten verwendet (`Individual`), erhält die App automatisch einen Paketverweis für das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket in der Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="76b2f-169">Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="76b2f-170">Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:</span><span class="sxs-lookup"><span data-stu-id="76b2f-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="76b2f-171">Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="76b2f-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="76b2f-172">`HttpClient`-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="76b2f-172">`HttpClient` configuration</span></span>

<span data-ttu-id="76b2f-173">In `Program.Main` (`Program.cs`) ist ein benannter <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) konfiguriert, um <xref:System.Net.Http.HttpClient>-Instanzen bereitzustellen, die bei Anforderungen an die Server-API Zugriffstoken einschließen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="76b2f-174">Wenn Sie eine Blazor WebAssembly-App für die Verwendung einer vorhandenen Identity Server-Instanz konfigurieren, die nicht Teil einer gehosteten Blazor-Lösung ist, ändern Sie die Registrierung der <xref:System.Net.Http.HttpClient>-Basisadresse von <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) in die URL des API-Autorisierungsendpunkts der Server-App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="76b2f-175">API-Autorisierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="76b2f-175">API authorization support</span></span>

<span data-ttu-id="76b2f-176">Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer von der im [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket bereitgestellten -Erweiterungsmethode eingefügt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="76b2f-177">Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem vorhandenen Autorisierungssystem interagiert.</span><span class="sxs-lookup"><span data-stu-id="76b2f-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="76b2f-178">Standardmäßig wird die Konfiguration für die App durch die Konvention von `_configuration/{client-id}` geladen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="76b2f-179">Per Konvention wird die Client-ID auf den Assemblynamen der App festgelegt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="76b2f-180">Diese URL kann so geändert werden, dass sie auf einen eigenen Endpunkt verweist, indem das Überladen mit Optionen aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="76b2f-181">Imports-Datei</span><span class="sxs-lookup"><span data-stu-id="76b2f-181">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="76b2f-182">Indexseite</span><span class="sxs-lookup"><span data-stu-id="76b2f-182">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="76b2f-183">App-Komponente</span><span class="sxs-lookup"><span data-stu-id="76b2f-183">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="76b2f-184">RedirectToLogin-Komponente</span><span class="sxs-lookup"><span data-stu-id="76b2f-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="76b2f-185">LoginDisplay-Komponente</span><span class="sxs-lookup"><span data-stu-id="76b2f-185">LoginDisplay component</span></span>

<span data-ttu-id="76b2f-186">Die `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) wird in der `MainLayout`-Komponente (`Shared/MainLayout.razor`) gerendert, und sie verwaltet die folgenden Verhaltensweisen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="76b2f-187">Für authentifizierte Benutzer:</span><span class="sxs-lookup"><span data-stu-id="76b2f-187">For authenticated users:</span></span>
  * <span data-ttu-id="76b2f-188">Zeigt den aktuellen Benutzernamen an</span><span class="sxs-lookup"><span data-stu-id="76b2f-188">Displays the current user name.</span></span>
  * <span data-ttu-id="76b2f-189">Stellt einen Link zur Benutzerprofilseite in ASP.NET Core Identity bereit.</span><span class="sxs-lookup"><span data-stu-id="76b2f-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="76b2f-190">Bietet eine Schaltfläche zum Abmelden von der App</span><span class="sxs-lookup"><span data-stu-id="76b2f-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="76b2f-191">Für anonyme Benutzer:</span><span class="sxs-lookup"><span data-stu-id="76b2f-191">For anonymous users:</span></span>
  * <span data-ttu-id="76b2f-192">Bietet die Option zum Registrieren</span><span class="sxs-lookup"><span data-stu-id="76b2f-192">Offers the option to register.</span></span>
  * <span data-ttu-id="76b2f-193">Bietet die Option zur Anmeldung</span><span class="sxs-lookup"><span data-stu-id="76b2f-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="76b2f-194">Authentication-Komponente</span><span class="sxs-lookup"><span data-stu-id="76b2f-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="76b2f-195">FetchData-Komponente</span><span class="sxs-lookup"><span data-stu-id="76b2f-195">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="76b2f-196">Ausführen der App</span><span class="sxs-lookup"><span data-stu-id="76b2f-196">Run the app</span></span>

<span data-ttu-id="76b2f-197">Führen Sie die App aus dem Serverprojekt aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-197">Run the app from the Server project.</span></span> <span data-ttu-id="76b2f-198">Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="76b2f-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="76b2f-199">Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="76b2f-200">Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="76b2f-201">Namens- und Rollenanspruch mit API-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="76b2f-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="76b2f-202">Benutzerdefinierte Benutzerfactory</span><span class="sxs-lookup"><span data-stu-id="76b2f-202">Custom user factory</span></span>

<span data-ttu-id="76b2f-203">Erstellen Sie in der *`Client`* -App eine benutzerdefinierte Benutzerfactory.</span><span class="sxs-lookup"><span data-stu-id="76b2f-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="76b2f-204">IdentityServer sendet mehrere Rollen als JSON-Array in einem einzelnen `role`-Anspruch.</span><span class="sxs-lookup"><span data-stu-id="76b2f-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="76b2f-205">Eine einzelne Rolle wird im Anspruch als Zeichenfolgenwert gesendet.</span><span class="sxs-lookup"><span data-stu-id="76b2f-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="76b2f-206">Die Factory erstellt einen einzelnen `role`-Anspruch für die einzelnen Rollen des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="76b2f-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="76b2f-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="76b2f-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="76b2f-208">Registrieren Sie in der *`Client`* -App die Factory in `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="76b2f-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="76b2f-209">Rufen Sie in der *`Server`* -App <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> für den Identity-Generator auf, der rollenbezogene Dienste hinzufügt:</span><span class="sxs-lookup"><span data-stu-id="76b2f-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="76b2f-210">Konfigurieren von IdentityServer</span><span class="sxs-lookup"><span data-stu-id="76b2f-210">Configure Identity Server</span></span>

<span data-ttu-id="76b2f-211">Verwenden Sie **einen** der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="76b2f-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="76b2f-212">API-Autorisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="76b2f-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="76b2f-213">Profildienst</span><span class="sxs-lookup"><span data-stu-id="76b2f-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="76b2f-214">API-Autorisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="76b2f-214">API authorization options</span></span>

<span data-ttu-id="76b2f-215">Gehen Sie in der *`Server`* -App wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="76b2f-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="76b2f-216">Konfigurieren Sie IdentityServer so, dass der `name`- und der `role`-Anspruch in das ID-Token und das Zugriffstoken platziert werden.</span><span class="sxs-lookup"><span data-stu-id="76b2f-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="76b2f-217">Verhindern Sie die Standardzuordnung für Rollen im JWT-Tokenhandler.</span><span class="sxs-lookup"><span data-stu-id="76b2f-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="76b2f-218">Profildienst</span><span class="sxs-lookup"><span data-stu-id="76b2f-218">Profile Service</span></span>

<span data-ttu-id="76b2f-219">Erstellen Sie in der *`Server`* -App eine `ProfileService`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="76b2f-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="76b2f-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="76b2f-220">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="76b2f-221">Registrieren Sie in der *`Server`* -App den Profildienst in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="76b2f-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="76b2f-222">Verwenden der Autorisierungsmechanismen</span><span class="sxs-lookup"><span data-stu-id="76b2f-222">Use authorization mechanisms</span></span>

<span data-ttu-id="76b2f-223">Die Ansätze zur Komponentenautorisierung in der *`Client`* -App sind an diesem Punkt funktional.</span><span class="sxs-lookup"><span data-stu-id="76b2f-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="76b2f-224">Jeder der Autorisierungsmechanismen bei den Komponenten kann eine Rolle verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="76b2f-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="76b2f-225">[`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="76b2f-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="76b2f-226">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="76b2f-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="76b2f-227">[Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="76b2f-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="76b2f-228">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="76b2f-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="76b2f-229">`User.Identity.Name` wird in der *`Client`* -App mit dem Benutzernamen des Benutzers aufgefüllt. In der Regel handelt es sich dabei um die E-Mail-Adresse für die Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="76b2f-229">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="76b2f-230">Hosten in Azure App Service mit einer benutzerdefinierten Domäne</span><span class="sxs-lookup"><span data-stu-id="76b2f-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="76b2f-231">In der folgenden Anleitung wird erläutert, wie Sie eine gehostete Blazor WebAssembly-App mit Identity Server für [Azure App Service](https://azure.microsoft.com/services/app-service/) mit einer benutzerdefinierten Domäne bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="76b2f-232">Verwenden Sie für dieses Hostingszenario **nicht** dasselbe Zertifikat für den [Tokensignaturschlüssel von Identity Server](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) und die sichere HTTPS-Kommunikation der Website mit Browsern:</span><span class="sxs-lookup"><span data-stu-id="76b2f-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="76b2f-233">Die Verwendung anderer Zertifikate für diese beiden Anforderungen gilt aus Sicherheitsgründen als Best Practice, da private Schlüssel für die einzelnen Zwecke isoliert werden.</span><span class="sxs-lookup"><span data-stu-id="76b2f-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="76b2f-234">TLS-Zertifikate für die Kommunikation mit Browsern werden unabhängig verwaltet, ohne dass dadurch eine Auswirkung auf die Tokensignatur von Identity Server entsteht.</span><span class="sxs-lookup"><span data-stu-id="76b2f-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="76b2f-235">Wenn [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) aus Gründen der Bindung einer benutzerdefinierten Domäne ein Zertifikat für eine App Service-App bereitstellt, kann Identity Server nicht dasselbe Zertifikat von Azure Key Vault abrufen, um das Token zu signieren.</span><span class="sxs-lookup"><span data-stu-id="76b2f-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="76b2f-236">Obwohl es möglich ist, dass Identity Server so konfiguriert wird, dass dasselbe TLS-Zertifikat aus einem physischen Pfad verwendet wird, ist das Unterstellen von Zertifikaten unter die Quellcodeverwaltung **nicht zu empfehlen und sollte in den meisten Szenarios vermieden werden**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="76b2f-237">In der folgenden Anleitung wird ein selbstsigniertes Zertifikat in Azure Key Vault erstellt, das lediglich der Tokensignatur in Identity Server dient.</span><span class="sxs-lookup"><span data-stu-id="76b2f-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="76b2f-238">Die Identity Server-Konfiguration nutzt das Zertifikat in Key Vault über den `My` > `CurrentUser`-Zertifikatspeicher der App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="76b2f-239">Andere für HTTPS-Datenverkehr mit benutzerdefinierten Domänen verwendete Zertifikate werden unabhängig vom Signaturzertifikat für Identity Server erstellt und konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="76b2f-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="76b2f-240">Konfigurieren einer App sowie Konfigurieren von Azure App Service und Azure Key Vault für Hosting mit benutzerdefinierter Domäne und HTTPS:</span><span class="sxs-lookup"><span data-stu-id="76b2f-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="76b2f-241">Erstellen Sie einen [App Service-Plan](/azure/app-service/overview-hosting-plans) mindestens mit dem Tarif `Basic B1`.</span><span class="sxs-lookup"><span data-stu-id="76b2f-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="76b2f-242">App Service erfordert mindestens eine `Basic B1`-Dienstebene, um benutzerdefinierte Domänen nutzen zu können.</span><span class="sxs-lookup"><span data-stu-id="76b2f-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="76b2f-243">Erstellen Sie für eine sichere Browserkommunikation der Website (HTTPS-Protokoll) ein PFX-Zertifikat. Verwenden Sie dabei einen allgemeinen Namen für den vollqualifizierten Domänenname (Fully Qualified Domain Name, FQDN) der Website, den Ihre Organisation steuert, z. B. `www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="76b2f-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="76b2f-244">Erstellen Sie das Zertifikat mit folgender Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="76b2f-244">Create the certificate with:</span></span>
   * <span data-ttu-id="76b2f-245">Schlüsselverwendung</span><span class="sxs-lookup"><span data-stu-id="76b2f-245">Key uses</span></span>
     * <span data-ttu-id="76b2f-246">Digitale Signaturüberprüfung (`digitalSignature`)</span><span class="sxs-lookup"><span data-stu-id="76b2f-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="76b2f-247">Schlüsselverschlüsselung (`keyEncipherment`)</span><span class="sxs-lookup"><span data-stu-id="76b2f-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="76b2f-248">Erweiterte Schlüsselverwendung</span><span class="sxs-lookup"><span data-stu-id="76b2f-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="76b2f-249">Clientauthentifizierung (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="76b2f-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="76b2f-250">Serverauthentifizierung (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="76b2f-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="76b2f-251">Verwenden Sie einen der folgenden Ansätze oder ein anderes geeignetes Tool oder einen entsprechenden Onlinedienst, um das Zertifikat zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="76b2f-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="76b2f-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="76b2f-253">MakeCert unter Windows</span><span class="sxs-lookup"><span data-stu-id="76b2f-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="76b2f-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="76b2f-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="76b2f-255">Notieren Sie sich das Kennwort, das später verwendet wird, um das Zertifikat in Azure Key Vault zu importieren.</span><span class="sxs-lookup"><span data-stu-id="76b2f-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="76b2f-256">Weitere Informationen zu Azure Key Vault-Zertifikaten finden Sie unter [Azure Key Vault: Zertifikate](/azure/key-vault/certificates/) aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="76b2f-257">Erstellen Sie eine neue Azure Key Vault-Instanz, oder verwenden Sie einen Schlüsseltresor in Ihrem Azure-Abonnement.</span><span class="sxs-lookup"><span data-stu-id="76b2f-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="76b2f-258">Importieren Sie im Bereich **Zertifikate** des Schlüsseltresors das PFX-Sitezertifikat.</span><span class="sxs-lookup"><span data-stu-id="76b2f-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="76b2f-259">Speichern Sie den Zertifikatfingerabdruck, der später für die Konfiguration der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="76b2f-260">Erstellen Sie in Azure Key Vault ein selbstsigniertes Zertifikat für die Identity Server-Tokensignatur.</span><span class="sxs-lookup"><span data-stu-id="76b2f-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="76b2f-261">Versehen Sie das Zertifikat mit einem **Zertifikatname** und einem **Antragsteller**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="76b2f-262">Der **Antragsteller** wird als `CN={COMMON NAME}` angegeben. Der Platzhalter `{COMMON NAME}` steht dabei für den allgemeinen Namen des Zertifikats.</span><span class="sxs-lookup"><span data-stu-id="76b2f-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="76b2f-263">Beim allgemeinen Namen kann es sich um eine beliebige alphanumerische Zeichenfolge handeln.</span><span class="sxs-lookup"><span data-stu-id="76b2f-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="76b2f-264">Bei `CN=IdentityServerSigning` handelt es sich beispielsweise um einen gültigen **Antragsteller** für ein Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="76b2f-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="76b2f-265">Verwenden Sie die Standardeinstellungen der **Erweiterten Richtlinienkonfiguration**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="76b2f-266">Speichern Sie den Zertifikatfingerabdruck, der später für die Konfiguration der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="76b2f-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="76b2f-267">Navigieren Sie im Azure-Portal zu Azure App Service, und erstellen Sie eine neu App Service-Instanz mit der folgenden Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="76b2f-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="76b2f-268">Legen Sie **Veröffentlichen** auf `Code` fest.</span><span class="sxs-lookup"><span data-stu-id="76b2f-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="76b2f-269">Legen Sie als **Runtimestapel** die Runtime der App fest.</span><span class="sxs-lookup"><span data-stu-id="76b2f-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="76b2f-270">Bestätigen Sie für **SKU und Größe**, dass die Dienstebene der App Service-Instanz mindestens `Basic B1` ist.</span><span class="sxs-lookup"><span data-stu-id="76b2f-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="76b2f-271">App Service erfordert mindestens eine `Basic B1`-Dienstebene, um benutzerdefinierte Domänen nutzen zu können.</span><span class="sxs-lookup"><span data-stu-id="76b2f-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="76b2f-272">Sobald die App Service-Instanz von Azure erstellt wurde, öffnen Sie die **Konfiguration** der App, und fügen Sie eine neue Anwendungseinstellung hinzu, die den zuvor gespeicherten Zertifikatfingerabdruck angibt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="76b2f-273">Der App-Einstellungsschlüssel lautet `WEBSITE_LOAD_CERTIFICATES`.</span><span class="sxs-lookup"><span data-stu-id="76b2f-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="76b2f-274">Trennen Sie die Zertifikatfingerabdrücke im App-Einstellungswert jeweils durch ein Komma, wie es im folgenden Beispiel zu sehen ist:</span><span class="sxs-lookup"><span data-stu-id="76b2f-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="76b2f-275">Schlüssel: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="76b2f-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="76b2f-276">Wert: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="76b2f-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="76b2f-277">Das Speichern der App-Einstellungen im Azure-Portal ist ein zweistufiger Prozess: Speichern Sie die Schlüssel-Wert-Einstellung `WEBSITE_LOAD_CERTIFICATES`, und klicken Sie dann oben auf dem Blatt auf die Schaltfläche **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="76b2f-278">Klicken Sie auf die **TLS-/SSL-Einstellungen** der App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="76b2f-279">Klicken Sie auf **Private Schlüsselzertifikate (PFX)** .</span><span class="sxs-lookup"><span data-stu-id="76b2f-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="76b2f-280">Verwenden Sie den **Key Vault-Zertifikat importieren**-Prozess zweimal, um sowohl das Zertifikat der Website für die HTTPS-Kommunikation als auch das selbstsignierte Identity Server-Zertifikat für die Tokensignatur der Website zu importieren.</span><span class="sxs-lookup"><span data-stu-id="76b2f-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="76b2f-281">Navigieren Sie zum Blatt **Benutzerdefinierte Domänen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="76b2f-282">Verwenden Sie auf der Website Ihrer Domänenregistrierungsstelle die **IP-Adresse** und eine **Verifizierungs-ID für eine benutzerdefinierte Domäne**, um die Domäne zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="76b2f-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="76b2f-283">Eine typische Domänenkonfiguration umfasst Folgendes:</span><span class="sxs-lookup"><span data-stu-id="76b2f-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="76b2f-284">Einen **A-Datensatz** mit `@` als **Host** und einen Wert der IP-Adresse aus dem Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="76b2f-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="76b2f-285">Einen **TXT-Datensatz** mit `asuid` als **Host** und den Wert der von Azure generierten und vom Azure-Portal bereitgestellten Verifizierungs-ID</span><span class="sxs-lookup"><span data-stu-id="76b2f-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="76b2f-286">Achten Sie darauf, dass Sie die Änderungen auf der Website Ihrer Domänenregistrierungsstelle korrekt speichern.</span><span class="sxs-lookup"><span data-stu-id="76b2f-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="76b2f-287">Für manche Registrierungsstellenwebsites ist ein zweistufiger Prozess erforderlich, um Domänendatensätze zu speichern: Ein oder mehrere Datensätze werden einzeln gespeichert. Dann wird die Registrierung der Domäne über eine eigene Schaltfläche aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="76b2f-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="76b2f-288">Wechseln Sie zurück zum Blatt **Benutzerdefinierte Domänen** im Azure-Portal.</span><span class="sxs-lookup"><span data-stu-id="76b2f-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="76b2f-289">Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-289">Select **Add custom domain**.</span></span> <span data-ttu-id="76b2f-290">Klicken Sie auf die Option **A-Datensatz**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-290">Select the **A Record** option.</span></span> <span data-ttu-id="76b2f-291">Geben Sie die Domäne an, und klicken Sie auf **Überprüfen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="76b2f-292">Wenn die Domänendatensätze korrekt sind und über das Internet weitergegeben werden, haben Sie im Portal die Möglichkeit, auf die Schaltfläche **Benutzerdefinierte Domäne hinzufügen** zu klicken.</span><span class="sxs-lookup"><span data-stu-id="76b2f-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="76b2f-293">Es kann einige Tage dauern, bis Änderungen an der Domänenregistrierung über Domänennamenserver (Domain Name Servers, DNS) im Internet weitergegeben werden, nachdem sie von Ihrer Domänenregistrierungsstelle verarbeitet wurden.</span><span class="sxs-lookup"><span data-stu-id="76b2f-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="76b2f-294">Wenn Domänendatensätze nicht innerhalb von drei Werktagen aktualisiert wurden, überprüfen Sie, ob die Datensätze bei der Registrierungsstelle richtig festgelegt wurden, und wenden Sie sich an den entsprechenden Kundensupport.</span><span class="sxs-lookup"><span data-stu-id="76b2f-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="76b2f-295">Auf dem Blatt **Benutzerdefinierte Domänen** ist die Option **SSL-Status** für die Domäne mit `Not Secure` markiert.</span><span class="sxs-lookup"><span data-stu-id="76b2f-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="76b2f-296">Klicken Sie auf den Link **Bindung hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-296">Select the **Add binding** link.</span></span> <span data-ttu-id="76b2f-297">Wählen Sie für die benutzerdefinierte Domänenbindung das HTTPS-Zertifikat der Website aus dem Schlüsseltresor aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="76b2f-298">Öffnen Sie in Visual Studio die Datei mit den App-Einstellungen des *Server*-Projekts (`appsettings.json` oder `appsettings.Production.json`).</span><span class="sxs-lookup"><span data-stu-id="76b2f-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="76b2f-299">Fügen Sie in der Identity Server-Konfiguration den folgenden `Key`-Abschnitt hinzu.</span><span class="sxs-lookup"><span data-stu-id="76b2f-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="76b2f-300">Geben Sie den **Antragsteller** des selbstsignierten Zertifikats für den `Name`-Schlüssel an.</span><span class="sxs-lookup"><span data-stu-id="76b2f-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="76b2f-301">Im folgenden Beispiel handelt es sich beim im Schlüsseltresor zugewiesenen allgemeinen Namen des Zertifikats um `IdentityServerSigning`, was zu `CN=IdentityServerSigning` als **Antragsteller** führt:</span><span class="sxs-lookup"><span data-stu-id="76b2f-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="76b2f-302">Erstellen Sie in Visual Studio ein Azure App Service-[Veröffentlichungsprofil](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) für das *Server*-Projekt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="76b2f-303">Klicken Sie in der Menüleiste auf Folgendes: **Erstellen** > **Veröffentlichen** > **Neu** > **Azure** > **Azure App Service** (Windows oder Linux).</span><span class="sxs-lookup"><span data-stu-id="76b2f-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="76b2f-304">Wenn Visual Studio mit einem Azure-Abonnement verbunden ist, können Sie die **Anzeige** der Azure-Ressourcen nach **Ressourcentyp** festlegen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="76b2f-305">Suchen Sie in der Liste mit **Web-Apps** nach der App Service-Instanz für die App, und klicken Sie darauf.</span><span class="sxs-lookup"><span data-stu-id="76b2f-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="76b2f-306">Wählen Sie **Fertig stellen** aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-306">Select **Finish**.</span></span>
1. <span data-ttu-id="76b2f-307">Wenn Sie in Visual Studio zum **Veröffentlichen**-Fenster zurückgeleitet werden, werden der Schlüsseltresor und die Abhängigkeiten vom SQL Server-Datenbank-Dienst automatisch erkannt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="76b2f-308">Für die Azure Key Vault-Instanz sind keine Konfigurationsänderungen an den Standardeinstellungen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="76b2f-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="76b2f-309">Zu Testzwecken kann die lokale [SQLite](https://www.sqlite.org/index.html)-Datenbank einer App, die standardmäßig von der Blazor-Vorlage konfiguriert wird, mit der App ohne zusätzliche Konfiguration bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="76b2f-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="76b2f-310">Das Konfigurieren einer anderen Datenbank für Identity Server für Produktionsumgebungen wird in diesem Artikel nicht abgedeckt.</span><span class="sxs-lookup"><span data-stu-id="76b2f-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="76b2f-311">Weitere Informationen dazu finden Sie in den Abschnitten zu Datenbankressourcen in den folgenden Dokumentationsartikeln:</span><span class="sxs-lookup"><span data-stu-id="76b2f-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="76b2f-312">App Service</span><span class="sxs-lookup"><span data-stu-id="76b2f-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="76b2f-313">Identity Server</span><span class="sxs-lookup"><span data-stu-id="76b2f-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="76b2f-314">Klicken Sie unter dem Namen des Bereitstellungsprofils oben im Fenster auf den Link **Bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="76b2f-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="76b2f-315">Ändern Sie die Ziel-URL in die URL der benutzerdefinierten Domäne der Website, z. B. `https://www.contoso.com`.</span><span class="sxs-lookup"><span data-stu-id="76b2f-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="76b2f-316">Speichern Sie die Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-316">Save the settings.</span></span>
1. <span data-ttu-id="76b2f-317">Veröffentlichen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="76b2f-317">Publish the app.</span></span> <span data-ttu-id="76b2f-318">Visual Studio öffnet ein Browserfenster und fordert die Website unter der dazugehörigen benutzerdefinierten Domäne an.</span><span class="sxs-lookup"><span data-stu-id="76b2f-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="76b2f-319">Die Azure-Dokumentation enthält weitere Informationen zur Verwendung von Azure-Diensten und benutzerdefinierten Domänen mit TLS-Bindung in App Service, einschließlich Details zur Verwendung von CNAME-Einträgen anstelle von A-Datensätzen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="76b2f-320">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="76b2f-321">App Service-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="76b2f-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="76b2f-322">Tutorial: Zuordnen eines vorhandenen benutzerdefinierten DNS-Namens zu Azure App Service</span><span class="sxs-lookup"><span data-stu-id="76b2f-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="76b2f-323">Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="76b2f-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="76b2f-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="76b2f-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="76b2f-325">Es wird empfohlen, nach einer Änderung an der App, an der App-Konfiguration oder an Azure-Diensten im Azure-Portal für jede App-Testausführung jeweils ein neues Browserfenster im InPrivate- oder Inkognitomodus zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="76b2f-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="76b2f-326">Veraltete Cookies vorheriger Testläufe können zu Fehlern bei der Authentifizierung oder Autorisierung führen, wenn die Website getestet wird, selbst wenn die Konfiguration der Website korrekt ist.</span><span class="sxs-lookup"><span data-stu-id="76b2f-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="76b2f-327">Weitere Informationen dazu, wie Sie Visual Studio so konfigurieren, dass für jeden Testlauf ein neues Browserfenster im InPrivate- oder Inkognitomodus geöffnet wird, finden Sie im Abschnitt [Cookies und Standortdaten](#cookies-and-site-data).</span><span class="sxs-lookup"><span data-stu-id="76b2f-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="76b2f-328">Wenn die App Service-Konfiguration im Azure-Portal geändert wird, werden Aktualisierungen in der Regel schnell angewendet, jedoch nicht sofort.</span><span class="sxs-lookup"><span data-stu-id="76b2f-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="76b2f-329">Manchmal müssen Sie einen kurzen Zeitraum abwarten, damit App Service neu gestartet wird, damit eine Konfigurationsänderung angewendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="76b2f-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="76b2f-330">Wenn Sie eine Problembehandlung für Ladeprobleme eines Zertifikats ausführen, führen Sie den folgenden Befehl in einer [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)-PowerShell-Befehlsshell im Azure-Portal aus.</span><span class="sxs-lookup"><span data-stu-id="76b2f-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="76b2f-331">Der Befehl stellt eine Zertifikatliste bereit, auf die die App über den `My` > `CurrentUser`-Zertifikatspeicher zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="76b2f-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="76b2f-332">Zur Ausgabe gehören auch der Antragsteller von Zertifikaten und Fingerabdrücke, die hilfreich sind, wenn Sie eine App debuggen:</span><span class="sxs-lookup"><span data-stu-id="76b2f-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="76b2f-333">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="76b2f-333">Additional resources</span></span>

* [<span data-ttu-id="76b2f-334">Bereitstellen für die Produktion</span><span class="sxs-lookup"><span data-stu-id="76b2f-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="76b2f-335">Importieren eines Zertifikats aus Key Vault</span><span class="sxs-lookup"><span data-stu-id="76b2f-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="76b2f-336">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="76b2f-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="76b2f-337"><xref:host-and-deploy/proxy-load-balancer>: Umfasst Hinweise zu:</span><span class="sxs-lookup"><span data-stu-id="76b2f-337"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="76b2f-338">der Verwendung der Middleware für weitergeleitete Header, um HTTPS-Schemainformationen für Proxyserver und interne Netzwerke zu schützen</span><span class="sxs-lookup"><span data-stu-id="76b2f-338">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="76b2f-339">zusätzlichen Szenarios und Anwendungsfällen, einschließlich der manuellen Schemakonfiguration, Änderungen von Anforderungspfaden für fehlerfreies Routing von Anforderungen und der Weiterleitung des Anforderungsschemas für Linux- und Nicht-IIS-Reverseproxys.</span><span class="sxs-lookup"><span data-stu-id="76b2f-339">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
