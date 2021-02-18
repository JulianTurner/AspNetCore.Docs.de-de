---
title: ASP.NET Core Blazor WebAssembly mit Azure Active Directory-Gruppen und -Rollen
author: guardrex
description: Erfahren Sie, wie Sie Blazor WebAssembly für die Verwendung von Azure Active Directory-Gruppen und -Rollen konfigurieren.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 01/24/2021
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: c180580ec56313e444f2daf2b7d08c4d909b498a
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280525"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a>Gruppen, Administratorrollen und App-Rollen in Azure Active Directory (AAD)

Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:

* Gruppen
  * Sicherheit
  * Microsoft 365
  * Verteilung
* Rollen
  * AAD-Administratorrollen
  * App-Rollen

Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:

* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

Dieser Artikel enthält Anleitungen für Client- und Server-Apps:

* **CLIENT**: Eigenständig Blazor WebAssembly-Apps oder die *`Client`* -App einer gehosteten Blazor-Lösung.
* **SERVER**: Eigenständige ASP.NET Core-Server-API-/Web API-Apps oder die *`Server`* -App einer gehosteten Blazor-Lösung.

## <a name="scopes"></a>Bereiche

Damit die [Microsoft Graph-API](/graph/use-the-api) Daten zu Benutzerprofil, Rollenzuweisung und Gruppenmitgliedschaft abrufen kann, ist der **CLIENT** mit (`https://graph.microsoft.com/User.Read`) [Graph-API-Berechtigung (Bereich)](/graph/permissions-reference) im Azure-Portal konfiguriert.

Einer **SERVER**-App, die die Graph-API aufruft, um Rollen- und Gruppenmitgliedschaftsdaten zu erhalten, wird die `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph-API-Berechtigung (Bereich)](/graph/permissions-reference) im Azure-Portal gewährt.

Diese Bereiche werden zusätzlich zu den in AAD-Bereitstellungsszenarien erforderlichen Bereichen benötigt, die in den im ersten Abschnitt dieses Artikels aufgeführten Themen beschrieben werden.

> [!NOTE]
> Die Wörter „Berechtigung“ und „Bereich“ werden im Azure-Portal und in verschiedenen Dokumentationsmaterialien von Microsoft und externen Anbietern austauschbar verwendet. Im vorliegenden Artikel wird „Bereich“ durchgehend für die Berechtigungen verwendet, die einer App im Azure-Portal zugewiesen werden.

## <a name="group-membership-claims-attribute"></a>groupMembershipClaims-Attribut

Legen Sie im Azure-Portal im App-Manifest für **CLIENT**- und **SERVER**-Apps das [`groupMembershipClaims`-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) auf `All` fest. Der Wert `All` führt dazu, dass sämtliche Sicherheitsgruppen, Verteilergruppe und Rollen abgerufen werden, in denen der angemeldete Benutzer Mitglied ist.

1. Öffnen Sie die Azure-Portal-Registrierung der App.
1. Wählen Sie in der Randleiste **Verwalten** > **Manifest** aus.
1. Suchen Sie das `groupMembershipClaims`-Attribut.
1. Legen Sie den Wert auf `All` fest.
1. Wählen Sie die Schaltfläche **Speichern** aus.

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a>Benutzerdefiniertes Benutzerkonto

Weisen Sie Benutzer im Azure-Portal zu AAD-Sicherheitsgruppen und AAD-Administratorrollen zu.

Für die Beispiele in diesem Artikel gilt:

* Es wird angenommen, dass einem Benutzer im AAD-Mandanten im Azure-Portal die AAD-Rolle *Abrechnungsadministrator* zugewiesen ist, damit dieser Benutzer für den Zugriff auf die Zugriffsserver-API-Daten autorisiert ist.
* Verwenden Sie [Autorisierungsrichtlinien](xref:security/authorization/policies), um den Zugriff innerhalb der **CLIENT**- und **SERVER**-Apps zu steuern.

Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in der **CLIENT**-App, um Eigenschaften für Folgendes einzuschließen:

* `Roles`: Array aus AAD-App-Rollen (beschrieben im Abschnitt [App-Rollen](#app-roles))
* `Wids`: AAD-Administratorrollen in [`wids`-Ansprüchen (Ansprüche bekannter IDs)](/azure/active-directory/develop/access-tokens#payload-claims)
* `Oid`: Unveränderlicher [`oid`-Anspruch (Objektbezeichneranspruch)](/azure/active-directory/develop/id-tokens#payload-claims) (identifiziert einen Benutzer mandantenintern und mandantenübergreifend eindeutig)

Weisen Sie jeder Arrayeigenschaft ein leeres Array zu, damit die Überprüfung auf `null` nicht erforderlich ist, wenn diese Eigenschaften in `foreach`-Schleifen verwendet werden.

`CustomUserAccount.cs`:

```csharp
using System;
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = Array.Empty<string>();

    [JsonPropertyName("wids")]
    public string[] Wids { get; set; } = Array.Empty<string>();

    [JsonPropertyName("oid")]
    public string Oid { get; set; }
}
```

Fügen Sie einen Paketverweis auf die Projektdatei der **CLIENT**-App für [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) hinzu.

Fügen Sie die Hilfsprogrammklassen und die Konfiguration des Graph SDK im Abschnitt *Graph SDK* des Artikels <xref:blazor/security/webassembly/graph-api#graph-sdk> hinzu. Geben Sie in der `GraphClientExtensions`-Klasse den Bereich `User.Read` für das Zugriffstoken in der `AuthenticateRequestAsync`-Methode an:

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

Fügen Sie der **CLIENT**-App die folgende benutzerdefinierte Benutzerkontofactory hinzu. Die benutzerdefinierte Benutzerfactory wird verwendet, um Folgendes einzurichten:

* App-Rollenansprüche (`appRole`) (beschrieben im Abschnitt [App-Rollen](#app-roles))
* AAD-Administratorrollenansprüche (`directoryRole`)
* Ein Beispiel für einen Anspruch auf Benutzerprofildaten für die Mobiltelefonnummer des Benutzers (`mobilePhone`)
* AAD-Gruppenansprüche (`directoryGroup`)

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor,
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            foreach (var role in account.Roles)
            {
                userIdentity.AddClaim(new Claim("appRole", role));
            }

            foreach (var wid in account.Wids)
            {
                userIdentity.AddClaim(new Claim("directoryRole", wid));
            }

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);

                var requestMe = graphClient.Me.Request();
                var user = await requestMe.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilePhone",
                        user.MobilePhone));
                }

                var requestMemberOf = graphClient.Users[account.Oid].MemberOf;
                var memberships = await requestMemberOf.Request().GetAsync();

                if (memberships != null)
                {
                    foreach (var entry in memberships)
                    {
                        if (entry.ODataType == "#microsoft.graph.group")
                        {
                            userIdentity.AddClaim(
                                new Claim("directoryGroup", entry.Id));
                        }
                    }
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Der vorangehende Code enthält keine transitiven Mitgliedschaften. Wenn die App direkte und transitive Gruppenmitgliedschaftsansprüche erfordert, ersetzen Sie die `MemberOf`-Eigenschaft (`IUserMemberOfCollectionWithReferencesRequestBuilder`) durch `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).

Der obige Code ignoriert Gruppenmitgliedschaftsansprüche (`groups`), bei denen es sich um AAD-Administratorrollen (Typ `#microsoft.graph.directoryRole`) handelt, weil die von Microsoft Identity Platform 2.0 zurückgegebenen GUID-Werte **Entitäts-IDs** und keine [**Rollenvorlagen-IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids) für AAD-Administratorrollen sind. Entitäts-IDs bleiben in Microsoft Identity Platform 2.0 nicht mandantenübergreifend erhalten und sollten nicht zum Erstellen von Autorisierungsrichtlinien für Benutzer in Apps verwendet werden. Verwenden Sie immer **Rollenvorlagen-IDs** für **von `wids`-Ansprüchen** bereitgestellte AAD-Administratorrollen.

Konfigurieren Sie in `Program.Main` der **CLIENT**-App die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory.

`Program.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState,
    CustomUserAccount>(options =>
{
    ...
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount,
    CustomAccountFactory>();

...

builder.Services.AddGraphClient();
```

## <a name="authorization-configuration"></a>Konfigurieren der Autorisierung

Erstellen Sie in der **CLIENT**-App eine [Richtlinie](xref:security/authorization/policies) für jede [App-Rolle](#app-roles), AAD-Administratorrolle oder Sicherheitsgruppe in `Program.Main`. Im folgenden Beispiel wird eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* erstellt:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Die vollständige Liste der IDs für AAD-Administratorrollen finden Sie in der Azure-Dokumentation unter [Rollenvorlagen-IDs](/azure/active-directory/roles/permissions-reference#role-template-ids). Weitere Informationen zu Autorisierungsrichtlinien finden Sie unter <xref:security/authorization/policies>.

In den folgenden Beispielen verwendet die **CLIENT**-App die oben genannte Richtlinie zum Autorisieren des Benutzers.

Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrator Role
            and can see this content.
        </p>
    </Authorized>
    <NotAuthorized>
        <p>
            The user is NOT in the 'Billing Administrator' role and sees this
            content.
        </p>
    </NotAuthorized>
</AuthorizeView>
```

Durch Verwendung einer [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.

Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic).

`Pages/CheckPolicy.razor`:

```razor
@page "/checkpolicy"
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<h1>Check Policy</h1>

<p>This component checks a policy in code.</p>

<button @onclick="CheckPolicy">Check 'BillingAdministrator' policy</button>

<p>Policy Message: @policyMessage</p>

@code {
    private string policyMessage = "Check hasn't been made yet.";

    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async void CheckPolicy()
    {
        var user = (await authenticationStateTask).User;

        if ((await AuthorizationService.AuthorizeAsync(user, 
            "BillingAdministrator")).Succeeded)
        {
            policyMessage = "Yes! The 'BillingAdministrator' policy is met.";
        }
        else
        {
            policyMessage = "No! 'BillingAdministrator' policy is NOT met.";
        }
    }
}
```

## <a name="authorize-server-apiweb-api-access"></a>Autorisieren des Server-API-/Web-API-Zugriffs

Eine **SERVER**-API-App kann Benutzer mit [Autorisierungsrichtlinien](xref:security/authorization/policies) für Sicherheitsgruppen, AAD-Administratorrollen und App-Rollen für den Zugriff auf sichere API-Endpunkte autorisieren, wenn ein Zugriffstoken `groups`-, `wids`- und `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`-Ansprüche enthält. Das folgende Beispiel erstellt eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* in `Startup.ConfigureServices` unter Verwendung der `wids`-Ansprüche (bekannte IDs/Rollenvorlagen-IDs):

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

Die vollständige Liste der IDs für AAD-Administratorrollen finden Sie in der Azure-Dokumentation unter [Rollenvorlagen-IDs](/azure/active-directory/roles/permissions-reference#role-template-ids). Weitere Informationen zu Autorisierungsrichtlinien finden Sie unter <xref:security/authorization/policies>.

Der Zugriff auf einen Controller in der **SERVER**-App kann auf der Verwendung eines [`[Authorize]`-Attributs](xref:security/authorization/simple) mit dem Namen der Richtlinie basieren (API-Dokumentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).

Das folgende Beispiel beschränkt den Zugriff auf Abrechnungsdaten vom `BillingDataController` auf Azure-Abrechnungsadministratoren mit dem Richtliniennamen `BillingAdministrator`:

```csharp
...
using Microsoft.AspNetCore.Authorization;

[Authorize(Policy = "BillingAdministrator")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

Weitere Informationen finden Sie unter <xref:security/authorization/policies>.

## <a name="app-roles"></a>App-Rollen

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines App-Rollen-Mitgliedschaftsanspruchs finden Sie in der unter [Vorgehensweise: Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

Im folgenden Beispiel wird angenommen, dass die **CLIENT**- und **SERVER**-Apps mit zwei Rollen konfiguriert und die Rollen einem Testbenutzer zugewiesen sind:

* `admin`
* `developer`

> [!NOTE]
> Beim Entwickeln einer gehosteten Blazor WebAssembly-App oder eines Client-Server-Paars aus eigenständigen Apps (einer eigenständigen Blazor WebAssembly-App und einer eigenständigen ASP.NET Core-Server-API-/Web-API-App) muss die Manifesteigenschaft `appRoles` der Registrierungen sowohl des Clients als auch des Servers im Azure-Portal dieselben konfigurierten Rollen enthalten. Nachdem die Rollen im Manifest der Client-App festgelegt wurden, kopieren Sie diese in Gänze in das Manifest der Server-App. Wenn die `appRoles` im Manifest für die Registrierungen der Client- und der Server-App nicht deckungsgleich sind, werden keine Rollenansprüche für authentifizierte Benutzer der Server-API/Web-API erstellt, auch wenn das Zugriffstoken über die richtigen Rollenansprüche verfügt.

> [!NOTE]
> Zwar können Sie ohne Azure AD Premium-Konto Gruppen keine Rollen zuweisen, aber Sie können mit einem Azure-Standardkonto Benutzern Rollen zuweisen und einen Rollenanspruch für Benutzer erhalten. Für die Schritte in diesem Abschnitt ist kein AAD Premium-Konto erforderlich.
>
> Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.

Die im Abschnitt [Benutzerdefiniertes Benutzerkonto](#custom-user-account) gezeigte `CustomAccountFactory` ist so konfiguriert, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert. Fügen Sie die `CustomAccountFactory` in der **CLIENT**-App hinzu, und registrieren Sie sie, wie im Abschnitt [Benutzerdefiniertes Benutzerkonto](#custom-user-account) gezeigt. Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

Geben Sie in `Program.Main` einer **CLIENT**-App den Anspruch namens `appRole` als Rollenanspruch für <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>-Überprüfungen an:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> Wenn Sie lieber den `directoryRoles`-Anspruch (ADD-Administratorrollen) verwenden möchten, weisen Sie `directoryRoles` zu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> zu.

Geben Sie in `Startup.ConfigureServices` einer **SERVER**-App den Anspruch namens `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` als Rollenanspruch für <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>-Überprüfungen an:

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAd", options);
        options.TokenValidationParameters.RoleClaimType = 
            "http://schemas.microsoft.com/ws/2008/06/identity/claims/role";
    },
    options => { Configuration.Bind("AzureAd", options); });
```

> [!NOTE]
> Wenn Sie lieber den `wids`-Anspruch (ADD-Administratorrollen) verwenden möchten, weisen Sie `wids` zu <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> zu.

Die Komponentenautorisierungsansätze sind Stand jetzt funktional. Jeder der Autorisierungsmechanismen in Komponenten der **CLIENT**-App kann die Rolle `admin` verwenden, um Benutzer zu autorisieren:

* [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [Prozedurale Logik](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

Mehrere Rollentests werden unterstützt:

* Legen Sie mithilfe der `AuthorizeView`-Komponente als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* Legen Sie mithilfe der `AuthorizeView`-Komponente als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:

  ```razor
  @code {
      private async Task DoSomething()
      {
          var authState = await AuthenticationStateProvider
              .GetAuthenticationStateAsync();
          var user = authState.User;

          if (user.IsInRole("admin") || user.IsInRole("developer"))
          {
              ...
          }
          else
          {
              ...
          }
      }
  }
  ```

* Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat. Ändern Sie dazu im vorherigen Beispiel das [bedingte OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in ein [bedingtes AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators):

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

Jeder der Autorisierungsmechanismen in Controllern der **SERVER**-App kann die Rolle `admin` verwenden, um Benutzer zu autorisieren:

* [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [Prozedurale Logik](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

Mehrere Rollentests werden unterstützt:

* Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:

  ```csharp
  static readonly string[] scopeRequiredByApi = new string[] { "API.Access" };

  ...

  [HttpGet]
  public IEnumerable<ReturnType> Get()
  {
      HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

      if (User.IsInRole("admin") || User.IsInRole("developer"))
      {
          ...
      }
      else
      {
          ...
      }

      return ...
  }
  ```

* Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat. Ändern Sie dazu im vorherigen Beispiel das [bedingte OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in ein [bedingtes AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators):

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Rollenvorlagen-IDs (Azure-Dokumentation)](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [`groupMembershipClaims`-Attribut (Azure-Dokumentation)](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [How to: Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token (Azure-Dokumentation)](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [Anwendungsrollen (Azure-Dokumentation)](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
