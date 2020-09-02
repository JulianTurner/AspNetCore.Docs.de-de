---
title: Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server
author: guardrex
description: In diesem Artikel finden Sie Informationen zum Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
no-loc:
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
ms.openlocfilehash: 58c21f4dbe831e99570ca8b0d7bc78616c1e5bfb
ms.sourcegitcommit: 9a90b956af8d8584d597f1e5c1dbfb0ea9bb8454
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88712375"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a>Sichern einer gehosteten Blazor WebAssembly-App in ASP.NET Core mit Identity Server

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

In diesem Artikel wird erläutert, wie eine von [ gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellt wird, die [IdentityServer](https://identityserver.io/) verwendet, um Benutzer und API-Aufrufe zu authentifizieren.

> [!NOTE]
> Um eine eigenständige oder gehostete Blazor WebAssembly-App für die Verwendung einer vorhandenen externen Identity Server-Instanz zu konfigurieren, befolgen Sie die Anweisungen in <xref:blazor/security/webassembly/standalone-with-authentication-library>.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:

1. Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor WebAssembly-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern** aus.

1. Wählen Sie **Einzelne Benutzerkonten** mit der Option **In-App-Speicherung von Benutzerkonten** aus, um Benutzer über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core innerhalb der App zu speichern.

1. Aktivieren Sie im Bereich **Erweitert** das Kontrollkästchen **Von ASP.NET Core gehostet**.

# <a name="visual-studio-code--net-core-cli"></a>[Visual Studio Code / .NET Core-CLI](#tab/visual-studio-code+netcore-cli)

Um ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus in einem leeren Ordner zu erstellen, geben Sie den Authentifizierungsmechanismus `Individual` mit der `-au|--auth`Option an, Benutzer innerhalb der App über das [Identity](xref:security/authentication/identity)-System von ASP.NET Core zu speichern:

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| Platzhalter  | Beispiel        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

Weitere Informationen finden Sie im Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

So erstellen Sie ein neues Blazor WebAssembly-Projekt mit einem Authentifizierungsmechanismus:

1. Wählen Sie im Schritt **Konfigurieren Ihrer neuen Blazor WebAssembly-App** in der Dropdownliste **Authentifizierung** die Option **Individual Authentication (in-app)** (Individuelle Authentifizierung [in der App]) aus.

1. Die App wird für einzelne Benutzer erstellt, die über [Identity](xref:security/authentication/identity) von ASP.NET Core in der App gespeichert sind.

1. Aktivieren Sie das Kontrollkästchen **Von ASP.NET Core gehostet**.

---

## <a name="server-app-configuration"></a>Server-App-Konfiguration

In den folgenden Abschnitten werden Ergänzungen zum Projekt erläutert, wenn die Authentifizierungsunterstützung eingeschlossen wird.

### <a name="startup-class"></a>Startklasse

Die `Startup`-Klasse verfügt über die folgenden Ergänzungen.

* In `Startup.ConfigureServices`:

  * ASP.NET Core Identity:

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * IdentityServer mit einer zusätzlichen <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode, die ASP.NET Core-Standardkonventionen zusätzlich zu IdentityServer einrichtet:

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * Authentifizierung mit einer zusätzlichen <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsmethode, die die App so konfiguriert, dass sie von IdentityServer generierte JWT-Token überprüft:

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* In `Startup.Configure`:

  * Die IdentityServer-Middleware stellt die Open ID Connect-Endpunkte (OIDC) bereit:

    ```csharp
    app.UseIdentityServer();
    ```

  * Die Authentifizierungsmiddleware ist für die Überprüfung der Anforderungsanmeldeinformationen und für das Festlegen des Benutzers auf den Anforderungskontext verantwortlich:

    ```csharp
    app.UseAuthentication();
    ```

  * Die Autorisierungsmiddleware aktiviert Autorisierungsfunktionen:

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a>AddApiAuthorization

Die <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>-Hilfsmethode konfiguriert [IdentityServer](https://identityserver.io/) für ASP.NET Core-Szenarien. IdentityServer ist ein leistungsfähiges und erweiterbares Framework für Überlegungen zum Thema „App-Sicherheit“. IdentityServer ist für die meisten gängigen Szenarien unnötig komplex. Als Folge werden mehrere Konventionen und Konfigurationsoptionen bereitgestellt, die sich gut als Startpunkt eignen. Wenn sich Ihre Anforderungen an die Authentifizierung ändern, bietet IdentityServer eine Vielzahl leistungsfähiger Funktionen, mit denen Sie die Authentifizierung genau an die Anforderungen einer App anpassen können.

### <a name="addno-locidentityserverjwt"></a>AddIdentityServerJwt

Die <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>-Hilfsprogrammmethode konfiguriert ein Richtlinienschema für die App als Standardauthentifizierungshandler. Die Richtlinie ist so konfiguriert, dass Identity alle an beliebige Unterpfade im Identity-URL-Raum `/Identity` weitergeleiteten Anforderungen verarbeiten kann. <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> verarbeitet alle anderen Anforderungen. Außerdem übernimmt diese Methode die folgenden Aufgaben:

* Registrieren einer `{APPLICATION NAME}API`-API-Ressource mit IdentityServer mit dem Standardbereich `{APPLICATION NAME}API`
* Konfigurieren der JWT-Bearertoken-Middleware zum Überprüfen der von IdentityServer für die App ausgegebenen Token

### <a name="weatherforecastcontroller"></a>WeatherForecastController

In `WeatherForecastController` (`Controllers/WeatherForecastController.cs`) wird das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut auf die Klasse angewendet. Das Attribut gibt an, dass der Benutzer basierend auf der Standardrichtlinie autorisiert werden muss, um auf die Ressource zuzugreifen. Die Standardautorisierungsrichtlinie ist so konfiguriert, dass das Standardauthentifizierungsschema verwendet wird, das von <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> eingerichtet wird. Die Hilfsprogrammmethode konfiguriert <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> als Standardhandler für Anforderungen an die App.

### <a name="applicationdbcontext"></a>ApplicationDbContext

In `ApplicationDbContext` (`Data/ApplicationDbContext.cs`) wird <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> durch <xref:Microsoft.EntityFrameworkCore.DbContext> erweitert, damit das Schema für IdentityServer eingeschlossen wird. <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> wird von <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> abgeleitet.

Für eine vollständige Kontrolle des Datenbankschemas wird von einer der verfügbaren Identity-<xref:Microsoft.EntityFrameworkCore.DbContext>-Klassen geerbt, und der Kontext wird so konfiguriert, dass er das Identity-Schema einschließt, indem `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in der <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>-Methode aufgerufen wird.

### <a name="oidcconfigurationcontroller"></a>OidcConfigurationController

In `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`) wird der Clientendpunkt bereitgestellt, um OIDC-Parameter bereitzustellen.

### <a name="app-settings"></a>App-Einstellungen

In der App-Einstellungsdatei (`appsettings.json`) beschreibt der Bereich `IdentityServer` auf Projektstammebene die Liste der konfigurierten Clients. Im folgenden Beispiel gibt es einen einzelnen Client. Der Clientname entspricht dem App-Namen, und er wird durch Konventionen dem OAuth-`ClientId`-Parameter zugeordnet. Das Profil gibt den App-Typ an, der konfiguriert wird. Das Profil wird intern verwendet, um Konventionen zu etablieren, die den Konfigurationsprozess für den Server vereinfachen. <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.Client`).

## <a name="client-app-configuration"></a>Client-App-Konfiguration

### <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App erstellt wird, die einzelne Benutzerkonten verwendet (`Individual`), erhält die App automatisch einen Paketverweis für das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket in der Projektdatei der App. Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) ermittelt werden.

### <a name="httpclient-configuration"></a>`HttpClient`-Konfiguration

In `Program.Main` (`Program.cs`) ist ein benannter <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) konfiguriert, um <xref:System.Net.Http.HttpClient>-Instanzen bereitzustellen, die bei Anforderungen an die Server-API Zugriffstoken einschließen:

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> Wenn Sie eine Blazor WebAssembly-App für die Verwendung einer vorhandenen Identity Server-Instanz konfigurieren, die nicht Teil einer gehosteten Blazor-Lösung ist, ändern Sie die Registrierung der <xref:System.Net.Http.HttpClient>-Basisadresse von <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) in die URL des API-Autorisierungsendpunkts der Server-App.

### <a name="api-authorization-support"></a>API-Autorisierungsunterstützung

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer von der im [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket bereitgestellten -Erweiterungsmethode eingefügt. Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem vorhandenen Autorisierungssystem interagiert.

```csharp
builder.Services.AddApiAuthorization();
```

Standardmäßig wird die Konfiguration für die App durch die Konvention von `_configuration/{client-id}` geladen. Per Konvention wird die Client-ID auf den Assemblynamen der App festgelegt. Diese URL kann so geändert werden, dass sie auf einen eigenen Endpunkt verweist, indem das Überladen mit Optionen aufgerufen wird.

### <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay-Komponente

Die `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) wird in der `MainLayout`-Komponente (`Shared/MainLayout.razor`) gerendert, und sie verwaltet die folgenden Verhaltensweisen:

* Für authentifizierte Benutzer:
  * Zeigt den aktuellen Benutzernamen an
  * Stellt einen Link zur Benutzerprofilseite in ASP.NET Core Identity bereit.
  * Bietet eine Schaltfläche zum Abmelden von der App
* Für anonyme Benutzer:
  * Bietet die Option zum Registrieren
  * Bietet die Option zur Anmeldung

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

### <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData-Komponente

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die App aus dem Serverprojekt aus. Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:

* Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.
* Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.

## <a name="name-and-role-claim-with-api-authorization"></a>Namens- und Rollenanspruch mit API-Autorisierung

### <a name="custom-user-factory"></a>Benutzerdefinierte Benutzerfactory

Erstellen Sie in der Client-App eine benutzerdefinierte Benutzerfactory. IdentityServer sendet mehrere Rollen als JSON-Array in einem einzelnen `role`-Anspruch. Eine einzelne Rolle wird im Anspruch als Zeichenfolgenwert gesendet. Die Factory erstellt einen einzelnen `role`-Anspruch für die einzelnen Rollen des Benutzers.

`CustomUserFactory.cs`:

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
            var roleClaims = identity.FindAll(identity.RoleClaimType);

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

Registrieren Sie in der Client-App die Factory in `Program.Main` (`Program.cs`):

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

Rufen Sie in der Server-App <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> für den Identity-Generator auf, der rollenbezogene Dienste hinzufügt:

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a>Konfigurieren von IdentityServer

Verwenden Sie **einen** der folgenden Ansätze:

* [API-Autorisierungsoptionen](#api-authorization-options)
* [Profildienst](#profile-service)

#### <a name="api-authorization-options"></a>API-Autorisierungsoptionen

Führen Sie in der Server-App die folgenden Aktionen aus:

* Konfigurieren Sie IdentityServer so, dass der `name`- und der `role`-Anspruch in das ID-Token und das Zugriffstoken platziert werden.
* Verhindern Sie die Standardzuordnung für Rollen im JWT-Tokenhandler.

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

#### <a name="profile-service"></a>Profildienst

Erstellen Sie in der Server-App eine `ProfileService`-Implementierung.

`ProfileService.cs`:

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

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

Registrieren Sie in der Server-App den Profildienst in `Startup.ConfigureServices`:

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a>Verwenden der Autorisierungsmechanismen

Die Komponentenautorisierungsansätze in der Client-App sind Stand jetzt funktional. Jeder der Autorisierungsmechanismen bei den Komponenten kann eine Rolle verwenden, um den Benutzer zu autorisieren:

* [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)
* [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)
* [Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)

  Mehrere Rollentests werden unterstützt:

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

`User.Identity.Name` wird in der Client-App mit dem Benutzernamen des Benutzers aufgefüllt. In der Regel handelt es sich dabei um die E-Mail-Adresse für die Anmeldung.

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bereitstellen für die Produktion](xref:security/authentication/identity/spa#deploy-to-production)
* [Importieren eines Zertifikats aus Key Vault](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
