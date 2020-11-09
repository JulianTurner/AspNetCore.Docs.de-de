---
title: Einführung in die Authentifizierung für Single-Page-apps auf ASP.net Core
author: javiercn
description: 'Verwenden Sie dies Identity mit einer einseitigen APP, die in einer ASP.net Core-App gehostet wird.'
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/identity/spa
ms.openlocfilehash: 8acc34c88bf62b3da1b920acc7318c94435c100e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051979"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="bd662-103">Authentifizierung und Autorisierung für Spas</span><span class="sxs-lookup"><span data-stu-id="bd662-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="bd662-104">Der ASP.net Core 3,1-Vorlagen und spätere Vorlagen bieten die Authentifizierung in Single-Page-Apps (Spas) mithilfe der Unterstützung für API-Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="bd662-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="bd662-105">ASP.NET Core Identityzum Authentifizieren und Speichern von Benutzern wird der [ Identity Server](https://identityserver.io/) zum Implementieren von OpenID Connect kombiniert.</span><span class="sxs-lookup"><span data-stu-id="bd662-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="bd662-106">Der **Angular** -und **der-Projekt** Vorlagen, die dem-Authentifizierungs Parameter in den Projektvorlagen für **Webanwendungen (Model-View-Controller)** (MVC) und **Webanwendungen** (Seiten) ähneln, wurde ein Authentifizierungs Parameter hinzugefügt Razor .</span><span class="sxs-lookup"><span data-stu-id="bd662-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="bd662-107">Die zulässigen Parameterwerte sind **None** und **Individual** .</span><span class="sxs-lookup"><span data-stu-id="bd662-107">The allowed parameter values are **None** and **Individual** .</span></span> <span data-ttu-id="bd662-108">Die Projektvorlage " **React.js und Redux** " unterstützt derzeit den Authentifizierungs Parameter nicht.</span><span class="sxs-lookup"><span data-stu-id="bd662-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="bd662-109">Erstellen einer APP mit Unterstützung für die API-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="bd662-109">Create an app with API authorization support</span></span>

<span data-ttu-id="bd662-110">Benutzerauthentifizierung und-Autorisierung können mit Angular-und reagingspas verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="bd662-111">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="bd662-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="bd662-112">**Angular** :</span><span class="sxs-lookup"><span data-stu-id="bd662-112">**Angular** :</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="bd662-113">**Reagieren** :</span><span class="sxs-lookup"><span data-stu-id="bd662-113">**React** :</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="bd662-114">Der vorherige Befehl erstellt eine ASP.net Core-App mit einem *ClientApp* -Verzeichnis, das die Spa enthält.</span><span class="sxs-lookup"><span data-stu-id="bd662-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="bd662-115">Allgemeine Beschreibung der ASP.net Core Komponenten der APP</span><span class="sxs-lookup"><span data-stu-id="bd662-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="bd662-116">In den folgenden Abschnitten werden Ergänzungen zum-Projekt beschrieben, wenn die Authentifizierungs Unterstützung enthalten ist:</span><span class="sxs-lookup"><span data-stu-id="bd662-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="bd662-117">Startklasse</span><span class="sxs-lookup"><span data-stu-id="bd662-117">Startup class</span></span>

<span data-ttu-id="bd662-118">Die folgenden Codebeispiele basieren auf [Microsoft. aspnetcore. apiauthorization. Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) Das nuget-Paket des Servers.</span><span class="sxs-lookup"><span data-stu-id="bd662-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="bd662-119">In den Beispielen werden die API-Authentifizierung und-Autorisierung mithilfe der <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> Erweiterungs Methoden und konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="bd662-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="bd662-120">Projekte, die die Projektvorlagen "Projekt" oder "Angular Spa" mit Authentifizierung verwenden, enthalten einen Verweis auf dieses Paket.</span><span class="sxs-lookup"><span data-stu-id="bd662-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="bd662-121">Die- `Startup` Klasse verfügt über die folgenden Ergänzungen:</span><span class="sxs-lookup"><span data-stu-id="bd662-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="bd662-122">In der- `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="bd662-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="bd662-123">Identity mit der Standardbenutzer Oberfläche:</span><span class="sxs-lookup"><span data-stu-id="bd662-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="bd662-124">IdentityServer mit einer zusätzlichen `AddApiAuthorization` Hilfsmethode, mit der einige Standard ASP.net Core Konventionen auf dem Server eingerichtet werden Identity :</span><span class="sxs-lookup"><span data-stu-id="bd662-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="bd662-125">Authentifizierung mit einer zusätzlichen `AddIdentityServerJwt`-Hilfsmethode, die die App so konfiguriert, dass sie von IdentityServer generierte JWT-Token überprüft:</span><span class="sxs-lookup"><span data-stu-id="bd662-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="bd662-126">In der- `Startup.Configure` Methode:</span><span class="sxs-lookup"><span data-stu-id="bd662-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="bd662-127">Die Authentifizierungs Middleware, die für die Validierung der Anforderungs Anmelde Informationen und das Festlegen des Benutzers im Anforderungs Kontext zuständig ist:</span><span class="sxs-lookup"><span data-stu-id="bd662-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="bd662-128">Die Identity Server Middleware, die die OpenID Connect-Endpunkte verfügbar macht:</span><span class="sxs-lookup"><span data-stu-id="bd662-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="bd662-129">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="bd662-129">AddApiAuthorization</span></span>

<span data-ttu-id="bd662-130">Diese Hilfsmethode konfiguriert Identity den Server für die Verwendung unserer unterstützten Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="bd662-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="bd662-131">IdentityServer ist ein leistungsfähiges und erweiterbares Framework für Überlegungen zum Thema „App-Sicherheit“.</span><span class="sxs-lookup"><span data-stu-id="bd662-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="bd662-132">Gleichzeitig stellt dies unnötige Komplexität für die gängigsten Szenarien bereit.</span><span class="sxs-lookup"><span data-stu-id="bd662-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="bd662-133">Folglich werden Ihnen eine Reihe von Konventionen und Konfigurationsoptionen zur Verfügung gestellt, die als guter Ausgangspunkt angesehen werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="bd662-134">Nachdem sich Ihre Authentifizierung geändert hat, ist die volle Leistung des Identity Servers weiterhin verfügbar, um die Authentifizierung an Ihre Bedürfnisse anzupassen.</span><span class="sxs-lookup"><span data-stu-id="bd662-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="bd662-135">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="bd662-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="bd662-136">Diese Hilfsmethode konfiguriert ein Richtlinien Schema für die APP als Standard Authentifizierungs Handler.</span><span class="sxs-lookup"><span data-stu-id="bd662-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="bd662-137">Die Richtlinie ist so konfiguriert, Identity dass alle Anforderungen verarbeitet werden können, die an einen Unterpfad im Identity URL-Bereich "/" weitergeleitet werden Identity .</span><span class="sxs-lookup"><span data-stu-id="bd662-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="bd662-138">`JwtBearerHandler` verarbeitet alle anderen Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="bd662-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="bd662-139">Darüber hinaus registriert diese Methode eine `<<ApplicationName>>API` API-Ressource bei Identity einem Server mit einem Standardbereich von `<<ApplicationName>>API` und konfiguriert die JWT-bearertoken-Middleware, um Token zu überprüfen, Identity die vom Server für die APP ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="bd662-140">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="bd662-140">WeatherForecastController</span></span>

<span data-ttu-id="bd662-141">Beachten Sie in der Datei " *controllers\weatherforecastcontroller.cs* " das-Attribut, das `[Authorize]` auf die-Klasse angewendet wird und angibt, dass der Benutzer auf der Basis der Standard Richtlinie für den Zugriff auf die Ressource autorisiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="bd662-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="bd662-142">Die Standard Autorisierungs Richtlinie wird so konfiguriert, dass das standardmäßige Authentifizierungsschema verwendet wird, das von `AddIdentityServerJwt` dem oben erwähnten Richtlinien Schema festgelegt wird `JwtBearerHandler` . Dadurch wird der von dieser Hilfsmethode konfigurierte Standard Handler für Anforderungen an die app.</span><span class="sxs-lookup"><span data-stu-id="bd662-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="bd662-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="bd662-143">ApplicationDbContext</span></span>

<span data-ttu-id="bd662-144">Beachten Sie in der Datei *data\applicationdbcontext.cs* , dass die gleiche `DbContext` in verwendet wird, Identity mit der Ausnahme, dass Sie erweitert wird `ApiAuthorizationDbContext` (eine stärker abgeleitete Klasse von `IdentityDbContext` ), um das Schema für den Server einzuschließen Identity .</span><span class="sxs-lookup"><span data-stu-id="bd662-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="bd662-145">Um die vollständige Kontrolle über das Datenbankschema zu erhalten, erben Sie von einer der verfügbaren Identity `DbContext` Klassen und konfigurieren den Kontext so, dass das Identity Schema durch Aufrufen von `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` für die- `OnModelCreating` Methode eingeschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="bd662-146">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="bd662-146">OidcConfigurationController</span></span>

<span data-ttu-id="bd662-147">Beachten Sie in der Datei " *controllers\oidcconfigurationcontroller.cs* " den Endpunkt, der bereitgestellt wird, um die oidc-Parameter bereitzustellen, die vom Client verwendet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="bd662-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="bd662-148">In der *appsettings.json* -Datei des Projekt Stamms gibt es einen neuen `IdentityServer` Abschnitt, in dem die Liste der konfigurierten Clients beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-148">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="bd662-149">Im folgenden Beispiel gibt es einen einzelnen Client.</span><span class="sxs-lookup"><span data-stu-id="bd662-149">In the following example, there's a single client.</span></span> <span data-ttu-id="bd662-150">Der Clientname entspricht dem App-Namen, und er wird durch Konventionen dem OAuth-`ClientId`-Parameter zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="bd662-150">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="bd662-151">Das Profil gibt den App-Typ an, der konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-151">The profile indicates the app type being configured.</span></span> <span data-ttu-id="bd662-152">Sie wird intern verwendet, um Konventionen zu fördern, die den Konfigurationsprozess für den Server vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="bd662-152">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="bd662-153">Es stehen mehrere Profile zur Verfügung, wie im Abschnitt [Anwendungsprofile](#application-profiles) erläutert.</span><span class="sxs-lookup"><span data-stu-id="bd662-153">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="bd662-154">appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="bd662-154">appsettings.Development.json</span></span>

<span data-ttu-id="bd662-155">Im *appsettings.Development.jsfür* die Datei des Projekt Stamms gibt es einen `IdentityServer` Abschnitt, in dem der Schlüssel zum Signieren von Token beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-155">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="bd662-156">Beim Bereitstellen in der Produktion muss neben der APP ein Schlüssel bereitgestellt und bereitgestellt werden, wie im Abschnitt bereitstellen [in der Produktion](#deploy-to-production) erläutert.</span><span class="sxs-lookup"><span data-stu-id="bd662-156">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="bd662-157">Allgemeine Beschreibung der Angular-App</span><span class="sxs-lookup"><span data-stu-id="bd662-157">General description of the Angular app</span></span>

<span data-ttu-id="bd662-158">Die Unterstützung von Authentifizierung und API-Autorisierung in der Angular-Vorlage befindet sich in einem eigenen Angular-Modul im Verzeichnis *clientapp\src\api-Authorization* .</span><span class="sxs-lookup"><span data-stu-id="bd662-158">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="bd662-159">Das Modul besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="bd662-159">The module is composed of the following elements:</span></span>

* <span data-ttu-id="bd662-160">3 Komponenten:</span><span class="sxs-lookup"><span data-stu-id="bd662-160">3 components:</span></span>
  * <span data-ttu-id="bd662-161">*Login. Component. TS* : verarbeitet den Anmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="bd662-161">*login.component.ts* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="bd662-162">*Abmelde. Component. TS* : verarbeitet den Abmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="bd662-162">*logout.component.ts* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="bd662-163">*Login-Menu. Component. TS* : ein Widget, das eine der folgenden Gruppen von Links anzeigt:</span><span class="sxs-lookup"><span data-stu-id="bd662-163">*login-menu.component.ts* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="bd662-164">Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-164">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="bd662-165">Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-165">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="bd662-166">Ein Weiterleitungs Wächter `AuthorizeGuard` , der Routen hinzugefügt werden kann und erfordert, dass ein Benutzer vor dem Besuch der Route authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-166">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="bd662-167">Ein HTTP-Interceptor `AuthorizeInterceptor` , der das Zugriffs Token an ausgehende HTTP-Anforderungen anbindet, die auf die API abzielen, wenn der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-167">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="bd662-168">Ein Dienst `AuthorizeService` , der die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="bd662-168">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="bd662-169">Ein Angular-Modul, das Routen definiert, die den Authentifizierungs Teilen der APP zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="bd662-169">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="bd662-170">Er macht die Anmelde Menü Komponente, den Interceptor, den Wächter und den Dienst für die Nutzung durch den Rest der app verfügbar.</span><span class="sxs-lookup"><span data-stu-id="bd662-170">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="bd662-171">Allgemeine Beschreibung der App "reagieren"</span><span class="sxs-lookup"><span data-stu-id="bd662-171">General description of the React app</span></span>

<span data-ttu-id="bd662-172">Die Unterstützung für die Authentifizierung und API-Autorisierung in der "reagieren"-Vorlage befindet sich im Verzeichnis " *clientapp\src\components\api-Authorization* ".</span><span class="sxs-lookup"><span data-stu-id="bd662-172">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="bd662-173">Sie besteht aus den folgenden Elementen:</span><span class="sxs-lookup"><span data-stu-id="bd662-173">It's composed of the following elements:</span></span>

* <span data-ttu-id="bd662-174">4 Komponenten:</span><span class="sxs-lookup"><span data-stu-id="bd662-174">4 components:</span></span>
  * <span data-ttu-id="bd662-175">*Login.js* : übernimmt den Anmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="bd662-175">*Login.js* : Handles the app's login flow.</span></span>
  * <span data-ttu-id="bd662-176">*Logout.js* : verarbeitet den Abmelde Fluss der app.</span><span class="sxs-lookup"><span data-stu-id="bd662-176">*Logout.js* : Handles the app's logout flow.</span></span>
  * <span data-ttu-id="bd662-177">*LoginMenu.js* : ein Widget, das eine der folgenden Gruppen von Links anzeigt:</span><span class="sxs-lookup"><span data-stu-id="bd662-177">*LoginMenu.js* : A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="bd662-178">Benutzerprofil Verwaltung und Abmelde Links, wenn der Benutzer authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-178">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="bd662-179">Links zur Registrierung und Anmeldung, wenn der Benutzer nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-179">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="bd662-180">*AuthorizeRoute.js* : eine Routen Komponente, für die vor dem Rendern der im-Parameter angegeben Komponente eine Authentifizierung des Benutzers erforderlich ist `Component` .</span><span class="sxs-lookup"><span data-stu-id="bd662-180">*AuthorizeRoute.js* : A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="bd662-181">Eine exportierte `authService` Instanz der `AuthorizeService` -Klasse, die die Details des Authentifizierungsprozesses auf niedrigerer Ebene behandelt und Informationen über den authentifizierten Benutzer für den Rest der App zur Nutzung verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="bd662-181">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="bd662-182">Nachdem Sie die Hauptkomponenten der Lösung kennengelernt haben, können Sie sich einen tieferen Einblick in die einzelnen Szenarios für die APP machen.</span><span class="sxs-lookup"><span data-stu-id="bd662-182">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="bd662-183">Autorisierung für eine neue API erforderlich</span><span class="sxs-lookup"><span data-stu-id="bd662-183">Require authorization on a new API</span></span>

<span data-ttu-id="bd662-184">Standardmäßig ist das System so konfiguriert, dass die Autorisierung für neue APIs problemlos erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-184">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="bd662-185">Erstellen Sie hierzu einen neuen Controller, und fügen Sie das- `[Authorize]` Attribut zur Controller Klasse oder zu einer beliebigen Aktion im Controller hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd662-185">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="bd662-186">Anpassen des API-Authentifizierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="bd662-186">Customize the API authentication handler</span></span>

<span data-ttu-id="bd662-187">Konfigurieren Sie zum Anpassen der Konfiguration des JWT-Handlers der API seine <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> Instanz:</span><span class="sxs-lookup"><span data-stu-id="bd662-187">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="bd662-188">Der JWT-Handler der API löst Ereignisse aus, die die Steuerung des Authentifizierungsprozesses mithilfe von ermöglichen `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="bd662-188">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="bd662-189">Um Unterstützung für die API-Autorisierung zu bieten, `AddIdentityServerJwt` registriert seine eigenen Ereignishandler.</span><span class="sxs-lookup"><span data-stu-id="bd662-189">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="bd662-190">Um die Behandlung eines Ereignisses anzupassen, binden Sie den vorhandenen Ereignishandler nach Bedarf mit zusätzlicher Logik ein.</span><span class="sxs-lookup"><span data-stu-id="bd662-190">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="bd662-191">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd662-191">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="bd662-192">Im vorangehenden Code wird der `OnTokenValidated` Ereignishandler durch eine benutzerdefinierte Implementierung ersetzt.</span><span class="sxs-lookup"><span data-stu-id="bd662-192">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="bd662-193">Diese Implementierung:</span><span class="sxs-lookup"><span data-stu-id="bd662-193">This implementation:</span></span>

1. <span data-ttu-id="bd662-194">Ruft die ursprüngliche, von der API-Autorisierungs Unterstützung bereitgestellte Implementierung auf.</span><span class="sxs-lookup"><span data-stu-id="bd662-194">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="bd662-195">Führen Sie eine eigene benutzerdefinierte Logik aus.</span><span class="sxs-lookup"><span data-stu-id="bd662-195">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="bd662-196">Schützen einer Client seitigen Route (Angular)</span><span class="sxs-lookup"><span data-stu-id="bd662-196">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="bd662-197">Der Schutz einer Client seitigen Route erfolgt durch Hinzufügen von Autorisierungs Wächter zur Liste der Wächter, die beim Konfigurieren einer Route ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-197">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="bd662-198">Beispielsweise können Sie sehen, wie die `fetch-data` Route innerhalb des Haupt-App-Angular-Moduls konfiguriert ist:</span><span class="sxs-lookup"><span data-stu-id="bd662-198">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="bd662-199">Beachten Sie, dass der Schutz einer Route nicht den eigentlichen Endpunkt schützt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist), sondern dass der Benutzer nur die Navigation zur angegebenen Client seitigen Route verhindert, wenn er nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-199">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="bd662-200">Authentifizieren von API-Anforderungen (Angular)</span><span class="sxs-lookup"><span data-stu-id="bd662-200">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="bd662-201">Die Authentifizierung von Anforderungen an APIs, die zusammen mit der APP gehostet werden, erfolgt automatisch durch die Verwendung des von der APP definierten http-Client-Interceptors.</span><span class="sxs-lookup"><span data-stu-id="bd662-201">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="bd662-202">Schützen einer Client seitigen Route (reagieren)</span><span class="sxs-lookup"><span data-stu-id="bd662-202">Protect a client-side route (React)</span></span>

<span data-ttu-id="bd662-203">Schützen Sie eine Client seitige Route mithilfe der `AuthorizeRoute` Komponente anstelle der einfachen `Route` Komponente.</span><span class="sxs-lookup"><span data-stu-id="bd662-203">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="bd662-204">Beachten Sie z. b., wie die `fetch-data` Route innerhalb der-Komponente konfiguriert ist `App` :</span><span class="sxs-lookup"><span data-stu-id="bd662-204">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="bd662-205">Schützen einer Route:</span><span class="sxs-lookup"><span data-stu-id="bd662-205">Protecting a route:</span></span>

* <span data-ttu-id="bd662-206">Schützt nicht den eigentlichen Endpunkt (für den immer noch ein `[Authorize]` angewendetes Attribut erforderlich ist).</span><span class="sxs-lookup"><span data-stu-id="bd662-206">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="bd662-207">Verhindert, dass der Benutzer zur angegebenen Client seitigen Route navigiert, wenn er nicht authentifiziert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-207">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="bd662-208">Authentifizieren von API-Anforderungen (reagieren)</span><span class="sxs-lookup"><span data-stu-id="bd662-208">Authenticate API requests (React)</span></span>

<span data-ttu-id="bd662-209">Das Authentifizieren von Anforderungen mit reagieren erfolgt durch das erste Importieren der- `authService` Instanz aus `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="bd662-209">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="bd662-210">Das Zugriffs Token wird aus dem abgerufen `authService` und wie unten dargestellt an die Anforderung angefügt.</span><span class="sxs-lookup"><span data-stu-id="bd662-210">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="bd662-211">In reagiere Komponenten wird diese Aufgabe in der Regel in der `componentDidMount` Lebenszyklus Methode oder als Ergebnis einer Benutzerinteraktion ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="bd662-211">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="bd662-212">Importieren Sie den authService in Ihre Komponente.</span><span class="sxs-lookup"><span data-stu-id="bd662-212">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="bd662-213">Abrufen und Anfügen des Zugriffs Tokens an die Antwort</span><span class="sxs-lookup"><span data-stu-id="bd662-213">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="bd662-214">Bereitstellen für die Produktion</span><span class="sxs-lookup"><span data-stu-id="bd662-214">Deploy to production</span></span>

<span data-ttu-id="bd662-215">Um die app in der Produktionsumgebung bereitzustellen, müssen die folgenden Ressourcen bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="bd662-215">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="bd662-216">Eine Datenbank, in der die Identity Benutzerkonten und die Identity Server erteilt werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-216">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="bd662-217">Ein Produktions Zertifikat, das zum Signieren von Token verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="bd662-217">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="bd662-218">Es gibt keine speziellen Anforderungen für dieses Zertifikat. Dabei kann es sich um ein selbst signiertes Zertifikat oder um ein Zertifikat handeln, das über eine Zertifizierungsstelle bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="bd662-218">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="bd662-219">Sie kann über Standard Tools wie PowerShell oder OpenSSL generiert werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-219">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="bd662-220">Sie kann im Zertifikat Speicher auf den Ziel Computern installiert oder als *PFX* -Datei mit einem sicheren Kennwort bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-220">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="bd662-221">Beispiel: Bereitstellen für einen nicht-Azure-Webhostinganbieter</span><span class="sxs-lookup"><span data-stu-id="bd662-221">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="bd662-222">Erstellen oder laden Sie Ihr Zertifikat im webhostingpanel.</span><span class="sxs-lookup"><span data-stu-id="bd662-222">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="bd662-223">Ändern Sie dann in der Datei der APP den Abschnitt so, dass er *appsettings.json* `IdentityServer` die Schlüssel Details enthält.</span><span class="sxs-lookup"><span data-stu-id="bd662-223">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="bd662-224">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd662-224">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="bd662-225">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd662-225">In the preceding example:</span></span>

* <span data-ttu-id="bd662-226">`StoreName` stellt den Namen des Zertifikat Speicher dar, in dem das Zertifikat gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-226">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="bd662-227">In diesem Fall verweist Sie auf den webhostingspeicher.</span><span class="sxs-lookup"><span data-stu-id="bd662-227">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="bd662-228">`StoreLocation` Gibt an, wo das Zertifikat geladen werden soll ( `CurrentUser` in diesem Fall).</span><span class="sxs-lookup"><span data-stu-id="bd662-228">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="bd662-229">`Name` entspricht dem Distinguished Subject für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="bd662-229">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="bd662-230">Beispiel: Bereitstellen für Azure App Service</span><span class="sxs-lookup"><span data-stu-id="bd662-230">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="bd662-231">In diesem Abschnitt wird beschrieben, wie Sie die APP für Azure App Service mithilfe eines im Zertifikat Speicher gespeicherten Zertifikats bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="bd662-231">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="bd662-232">Wenn Sie die APP so ändern möchten, dass ein Zertifikat aus dem Zertifikat Speicher geladen wird, ist beim Konfigurieren der app in der Azure-Portal in einem späteren Schritt ein Serviceplan vom Typ Standard oder besser erforderlich.</span><span class="sxs-lookup"><span data-stu-id="bd662-232">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="bd662-233">Ändern Sie in der *appsettings.json* Datei der APP den `IdentityServer` Abschnitt, um die Schlüssel Details einzuschließen:</span><span class="sxs-lookup"><span data-stu-id="bd662-233">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="bd662-234">Der Speicher Name steht für den Namen des Zertifikat Speicher, in dem das Zertifikat gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="bd662-234">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="bd662-235">In diesem Fall verweist Sie auf den persönlichen Benutzerspeicher.</span><span class="sxs-lookup"><span data-stu-id="bd662-235">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="bd662-236">Der Speicherort stellt dar, wo das Zertifikat geladen werden soll ( `CurrentUser` oder `LocalMachine` ).</span><span class="sxs-lookup"><span data-stu-id="bd662-236">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="bd662-237">Die Name-Eigenschaft für das Zertifikat entspricht dem Distinguished Subject für das Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="bd662-237">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="bd662-238">Befolgen Sie zum Bereitstellen von auf Azure App Service die Schritte unter Bereitstellen [der app in Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), in der erläutert wird, wie Sie die erforderlichen Azure-Ressourcen erstellen und die app in der Produktionsumgebung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="bd662-238">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="bd662-239">Nachdem Sie die obigen Anweisungen ausgeführt haben, wird die app in Azure bereitgestellt, ist aber noch nicht funktionsfähig.</span><span class="sxs-lookup"><span data-stu-id="bd662-239">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="bd662-240">Das von der APP verwendete Zertifikat muss in der Azure-Portal konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-240">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="bd662-241">Suchen Sie den Fingerabdruck für das Zertifikat, und befolgen Sie die Schritte unter [Laden von Zertifikaten](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span><span class="sxs-lookup"><span data-stu-id="bd662-241">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="bd662-242">Obwohl diese Schritte SSL erwähnen, gibt es einen Abschnitt mit **privaten Zertifikaten** in der Azure-Portal, in dem Sie das bereitgestellte Zertifikat für die Verwendung mit der APP hochladen können.</span><span class="sxs-lookup"><span data-stu-id="bd662-242">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="bd662-243">Nachdem Sie die APP und die App-Einstellungen im Azure-Portal konfiguriert haben, starten Sie die APP im Portal neu.</span><span class="sxs-lookup"><span data-stu-id="bd662-243">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="bd662-244">Andere Konfigurationsoptionen</span><span class="sxs-lookup"><span data-stu-id="bd662-244">Other configuration options</span></span>

<span data-ttu-id="bd662-245">Die Unterstützung für die API-Autorisierung basiert auf dem Identity Server mit einem Satz von Konventionen, Standardwerten und Verbesserungen, um die Bedienung für Spas zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="bd662-245">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="bd662-246">Natürlich ist die volle Leistungsfähigkeit von Server im Hintergrund Identity verfügbar, wenn die ASP.net Core Integrationen Ihr Szenario nicht abdecken.</span><span class="sxs-lookup"><span data-stu-id="bd662-246">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="bd662-247">Die ASP.net Core-Unterstützung konzentriert sich auf "erst Anbieter-Apps", bei denen alle apps von unserer Organisation erstellt und bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bd662-247">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="bd662-248">Daher wird die Unterstützung für Dinge wie die Zustimmung oder den Verbund nicht angeboten.</span><span class="sxs-lookup"><span data-stu-id="bd662-248">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="bd662-249">Verwenden Sie für diese Szenarien Identity Server, und befolgen Sie die zugehörigen Dokumentationen.</span><span class="sxs-lookup"><span data-stu-id="bd662-249">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="bd662-250">Anwendungsprofile</span><span class="sxs-lookup"><span data-stu-id="bd662-250">Application profiles</span></span>

<span data-ttu-id="bd662-251">Anwendungsprofile sind vordefinierte Konfigurationen für apps, die ihre Parameter weiter definieren.</span><span class="sxs-lookup"><span data-stu-id="bd662-251">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="bd662-252">Zu diesem Zeitpunkt werden die folgenden Profile unterstützt:</span><span class="sxs-lookup"><span data-stu-id="bd662-252">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="bd662-253">`IdentityServerSPA`: Stellt eine mit dem Server gehostete Spa Identity als einzelne Einheit dar.</span><span class="sxs-lookup"><span data-stu-id="bd662-253">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="bd662-254">Der `redirect_uri` Standardwert ist `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="bd662-254">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="bd662-255">Der `post_logout_redirect_uri` Standardwert ist `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="bd662-255">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="bd662-256">Der Satz von Bereichen umfasst `openid` , und alle Bereiche, die `profile` für die APIs in der APP definiert sind.</span><span class="sxs-lookup"><span data-stu-id="bd662-256">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="bd662-257">Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder jeweils einzeln ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="bd662-257">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="bd662-258">Der zulässige Antwortmodus ist `fragment` .</span><span class="sxs-lookup"><span data-stu-id="bd662-258">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="bd662-259">`SPA`: Stellt eine Spa dar, die nicht mit dem Server gehostet wird Identity .</span><span class="sxs-lookup"><span data-stu-id="bd662-259">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="bd662-260">Der Satz von Bereichen umfasst `openid` , und alle Bereiche, die `profile` für die APIs in der APP definiert sind.</span><span class="sxs-lookup"><span data-stu-id="bd662-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="bd662-261">Der Satz zulässiger oidc-Antworttypen ist `id_token token` oder jeweils einzeln ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="bd662-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="bd662-262">Der zulässige Antwortmodus ist `fragment` .</span><span class="sxs-lookup"><span data-stu-id="bd662-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="bd662-263">`IdentityServerJwt`: Stellt eine API dar, die zusammen mit dem-Server gehostet wird Identity .</span><span class="sxs-lookup"><span data-stu-id="bd662-263">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="bd662-264">Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.</span><span class="sxs-lookup"><span data-stu-id="bd662-264">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="bd662-265">`API`: Stellt eine API dar, die nicht mit dem Server gehostet wird Identity .</span><span class="sxs-lookup"><span data-stu-id="bd662-265">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="bd662-266">Die APP ist so konfiguriert, dass Sie einen einzelnen Bereich hat, der standardmäßig den APP-Namen verwendet.</span><span class="sxs-lookup"><span data-stu-id="bd662-266">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="bd662-267">Konfiguration über appSettings</span><span class="sxs-lookup"><span data-stu-id="bd662-267">Configuration through AppSettings</span></span>

<span data-ttu-id="bd662-268">Konfigurieren Sie die apps über das Konfigurationssystem, indem Sie Sie der Liste `Clients` oder hinzufügen `Resources` .</span><span class="sxs-lookup"><span data-stu-id="bd662-268">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="bd662-269">Konfigurieren Sie die- `redirect_uri` Eigenschaft und die-Eigenschaft des Clients `post_logout_redirect_uri` , wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="bd662-269">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="bd662-270">Wenn Sie Ressourcen konfigurieren, können Sie die Bereiche für die Ressource konfigurieren, wie unten dargestellt:</span><span class="sxs-lookup"><span data-stu-id="bd662-270">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="bd662-271">Konfiguration durch Code</span><span class="sxs-lookup"><span data-stu-id="bd662-271">Configuration through code</span></span>

<span data-ttu-id="bd662-272">Sie können die Clients und Ressourcen auch über den Code konfigurieren, indem Sie eine Überladung von verwenden `AddApiAuthorization` , die eine Aktion zum Konfigurieren von Optionen übernimmt.</span><span class="sxs-lookup"><span data-stu-id="bd662-272">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="bd662-273">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="bd662-273">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
