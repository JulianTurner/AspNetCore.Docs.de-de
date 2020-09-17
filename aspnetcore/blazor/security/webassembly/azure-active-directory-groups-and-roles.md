---
title: ASP.NET Core Blazor WebAssembly mit Azure Active Directory-Gruppen und -Rollen
author: guardrex
description: Erfahren Sie, wie Sie Blazor WebAssembly für die Verwendung von Azure Active Directory-Gruppen und -Rollen konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 07/28/2020
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 81114768a3600544dda46efbc886e2f56932aba7
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592928"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a>Gruppen, Administratorrollen und benutzerdefinierte Rollen aus Azure AD

Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:

* Benutzerdefinierte Gruppen
  * Sicherheit
  * Microsoft 365
  * Verteilung
* Rollen
  * Integrierte Administratorrollen
  * Benutzerdefinierte Rollen

Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:

* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph-API-Berechtigung

Für alle App-Benutzer, die über mehr als fünf integrierte AAD-Administratorrollen und Mitgliedschaften in Sicherheitsgruppen verfügen, ist ein Aufruf der [Microsoft Graph-API](/graph/use-the-api) erforderlich.

Um Graph-API Aufrufe zuzulassen, erteilen Sie der eigenständigen oder Client-App einer gehosteten Blazor-Lösung eine der folgenden [Graph-API-Berechtigungen](/graph/permissions-reference) im Azure-Portal:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` ist die Berechtigung mit den geringsten Rechten und die Berechtigung, die für das in diesem Artikel beschriebene Beispiel verwendet wird.

## <a name="user-defined-groups-and-built-in-administrative-roles"></a>Benutzerdefinierte Gruppen und integrierte Administratorrollen

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln. Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und integrierten AAD-Administratorrollen zu.

* [Rollen auf Grundlage von Azure AD-Sicherheitsgruppen](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims`-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der in AAD integrierten Rolle *Abrechnungsadministrator* zugewiesen ist.

Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an. Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.

Wenn die Anzahl der zugewiesenen integrierten Azure-Administratorrollen und benutzerdefinierten Gruppen fünf überschreitet, sendet AAD einen `hasgroups` Anspruch mit einem `true`-Wert, anstatt einen `groups`-Anspruch zu senden. Jede App, deren Benutzern ggf. mehr als fünf Rollen und Gruppen zugewiesen sind, muss einen separaten Graph-API-Aufruf ausführen, um die Rollen und Gruppen eines Benutzers abzurufen. Die in diesem Artikel bereitgestellte Beispielimplementierung behandelt dieses Szenario. Weitere Informationen finden Sie in den Informationen zu `groups`- und `hasgroups`-Ansprüchen im Artikel [Microsoft Identity Platform-Zugriffstoken: Nutzlastansprüche](/azure/active-directory/develop/access-tokens#payload-claims).

Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> um Arrayeigenschaften für Gruppen und Rollen. Weisen Sie jeder Eigenschaft ein leeres Array zu, damit die Überprüfung auf `null` nicht erforderlich ist, wenn diese Eigenschaften später in `foreach`-Schleifen verwendet werden.

`CustomUserAccount.cs`:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("groups")]
    public string[] Groups { get; set; } = new string[] { };

    [JsonPropertyName("roles")]
    public string[] Roles { get; set; } = new string[] { };
}
```

Erstellen Sie in der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse. Verwenden Sie den richtigen Gültigkeitsbereich (Berechtigung) für Graph-API-Aufrufe, die Rollen- und Gruppeninformationen abrufen.

`GraphAPIAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/Directory.Read.All" });
    }
}
```

Fügen Sie in `Program.Main` (`Program.cs`) den <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Implementierungsdienst hinzu, und fügen Sie einen benannten <xref:System.Net.Http.HttpClient> zum Durchführen von Graph-API-Anforderungen hinzu. Im folgenden Beispiel wird dieser Client als `GraphAPI` bezeichnet:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

Erstellen Sie AAD-Verzeichnisobjektklassen, um die OData-Rollen (Open Data Protocol) und -Gruppen von einem Graph-API-Aufruf zu empfangen. Die OData-Daten werden im JSON-Format empfangen, und ein Aufruf von <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> füllt eine Instanz der `DirectoryObjects`-Klasse mit Daten auf.

`DirectoryObjects.cs`:

```csharp
using System.Collections.Generic;
using System.Text.Json.Serialization;

public class DirectoryObjects
{
    [JsonPropertyName("@odata.context")]
    public string Context { get; set; }

    [JsonPropertyName("value")]
    public List<Value> Values { get; set; }
}

public class Value
{
    [JsonPropertyName("@odata.type")]
    public string Type { get; set; }

    [JsonPropertyName("id")]
    public string Id { get; set; }
}
```

Erstellen Sie eine benutzerdefinierte Benutzerfactory zum Verarbeiten von Rollen- und Gruppenansprüchen. In der folgenden Beispielimplementierung wird auch das `roles`-Anspruchsarray verwendet, das im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) beschrieben wird. Wenn der `hasgroups`-Anspruch vorhanden ist, wird der benannte <xref:System.Net.Http.HttpClient> verwendet, um eine autorisierte Anforderung der Graph-API vorzunehmen, um die Rollen und Gruppen des Benutzers abzurufen. Diese Implementierung verwendet den Microsoft Identity Platform v1.0-Endpunkt `https://graph.microsoft.com/v1.0/me/memberOf` ([API-Dokumentation](/graph/api/user-list-memberof)). Die Anleitungen in diesem Thema werden für Identity v2.0 aktualisiert, wenn die MSAL-Pakete für v2.0 aktualisiert werden.

`CustomAccountFactory.cs`:

```csharp
using System.Linq;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    private readonly ILogger<CustomUserFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                try
                {
                    var client = clientFactory.CreateClient("GraphAPI");

                    var response = await client.GetAsync("v1.0/me/memberOf");

                    if (response.IsSuccessStatusCode)
                    {
                        var userObjects = await response.Content
                            .ReadFromJsonAsync<DirectoryObjects>();

                        foreach (var obj in userObjects?.Values)
                        {
                            userIdentity.AddClaim(new Claim("group", obj.Id));
                        }

                        var claim = userIdentity.Claims.FirstOrDefault(
                            c => c.Type == "hasgroups");

                        userIdentity.RemoveClaim(claim);
                    }
                    else
                    {
                        logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    logger.LogError("Graph API access token failure: {MESSAGE}", 
                        exception.Message);
                }
            }
            else
            {
                foreach (var group in account.Groups)
                {
                    userIdentity.AddClaim(new Claim("group", group));
                }
            }
        }

        return initialUser;
    }
}
```

Für das Entfernen des ursprünglichen `groups`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

> [!NOTE]
> Die Vorgehensweise in diesem Beispiel:
>
> * Fügt eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse zum Anfügen von Zugriffstoken an ausgehende Anforderungen hinzu.
> * Fügt einen benannten <xref:System.Net.Http.HttpClient> hinzu, um Web-API-Anforderungen an einen sicheren, externen Web-API-Endpunkt vorzunehmen.
> * Verwendet den benannten <xref:System.Net.Http.HttpClient>, um autorisierte Anforderungen vorzunehmen.
>
> Allgemeine Erläuterungen zu diesem Ansatz finden Sie im Artikel <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.

Registrieren Sie die Factory in `Program.Main` (`Program.cs`) der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung. Stimmen Sie dem `Directory.Read.All`-Berechtigungsbereich als zusätzlichen Bereich für die App zu:

```csharp
builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    CustomUserAccount>(options =>
{
    builder.Configuration.Bind("AzureAd", 
        options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("...");

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Directory.Read.All");
})
.AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, CustomUserAccount, 
    CustomUserFactory>();
```

Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`. Im folgenden Beispiel wird eine Richtlinie für die in AAD integrierte Rolle *Abrechnungsadministrator* erstellt:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Gruppen-IDs für AAD-Administratorrollen](#aad-adminstrative-role-group-ids).

In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.

Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:

```razor
<AuthorizeView Policy="BillingAdministrator">
    <Authorized>
        <p>
            The user is in the 'Billing Administrator' AAD Administrative Role
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

Durch Verwendung der [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.

Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic):

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

## <a name="user-defined-roles"></a>Benutzerdefinierte Rollen

In AAD registrierte Apps können auch für die Verwendung von benutzerdefinierten Rollen konfiguriert werden.

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `roles`-Mitgliedschaftsanspruchs finden Sie in der Azure-Dokumentation unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

Im folgenden Beispiel wird davon ausgegangen, dass eine App mit zwei Rollen konfiguriert wurde:

* `admin`
* `developer`

> [!NOTE]
> Zwar können Sie Rollen nur mit einem Azure AD Premium-Konto Sicherheitsgruppen zuweisen, allerdings können Sie mit einem Azure-Standardkonto Rollen Benutzer zuweisen und einen `roles`-Anspruch für Benutzer erhalten. Für die Schritte in diesem Abschnitt ist kein Azure AD Premium-Konto erforderlich.
>
> Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.

Der von AAD gesendete einzelne `roles`-Anspruch zeigt die benutzerdefinierten Rollen jeweils als `value` für `appRoles` in einem JSON-Array an. Die App muss das JSON-Array mit Rollen in einzelne `role`-Ansprüche konvertieren.

Die im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert. Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben in der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung hinzu, und registrieren Sie sie dort. Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

Geben Sie in `Program.Main` der eigenständigen oder Client-App einer gehosteten Blazor-Lösung den Anspruch mit dem Namen „`role`“ als Rollenanspruch an:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

Die Komponentenautorisierungsansätze sind Stand jetzt funktional. Jeder der Autorisierungsmechanismen bei den Komponenten kann die Rolle `admin` verwenden, um den Benutzer zu autorisieren:

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

## <a name="aad-adminstrative-role-group-ids"></a>Gruppen-IDs für AAD-Administratorrollen

Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet. Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren. Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles).

AAD-Administratorrolle | ObjectID
--- | ---
Anwendungsadministrator | fa11557b-4f15-4ddd-85d5-313c7cd74047
Anwendungsentwickler | 68adcbb8-9504-44f6-89f2-5cd48dc74a2c
Authentifizierungsadministrator | 02d110a1-96b1-419e-af87-746461b60ed7
Azure DevOps-Administrator | a5311ace-ca41-44cd-b833-8d22caa0b34f
Azure Information Protection-Administrator | 18632dce-f9b5-4f01-abb5-37051f06860e
B2C-IEF-Schlüsselsatzadministrator | 0c2e87e5-94f9-4adb-ae8c-bcafe11bd368
B2C-IEF-Richtlinienadministrator | bfcab36c-10c6-4b13-b63c-4d8b62c0c44e
B2C-Benutzerflowadministrator | baa531b7-8cf0-44ad-8f98-eded88dae827
B2C-Administrator für Benutzerflowattribute | dd0baca0-a535-48c1-b871-8431abe16452
Rechnungsadministrator | 69ff516a-b57d-4697-a429-9de4af7b5609
Cloudanwendungsadministrator | 250b5fe3-b553-458d-9a53-b782c13c34bf
Cloudgeräteadministrator | 26cd4b44-2636-4ddb-bdfa-27feae66f86d
Complianceadministrator | 9d6e1dd0-c9f8-45f8-b558-b134f700116c
Compliancedatenadministrator | 4c0ca3a2-231e-416c-9411-4abe57d5cb9d
Administrator für den bedingten Zugriff | 8f71a611-137d-49af-87ad-e97f1fd5da76
Genehmigende Person für den LockBox-Kundenzugriff | c18d54a8-b13e-4954-a1a4-7deaf2e4f184
Desktop Analytics-Administrator | c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15
Rolle „Verzeichnis lesen“ | e1fc84a6-7762-4b9b-8e29-518b4adbc23b
Dynamics 365-Administrator | f20a9cfa-9fdf-49a8-a977-1afe446a1d6e
Exchange-Administrator | b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652
Externer Identitätsanbieteradministrator | febfaeb4-e478-407a-b4b3-f4d9716618a2
Globaler Administrator | a45ba61b-44db-462c-924b-3b2719152588
Globaler Leser | f6903b21-6aba-4124-b44c-76671796b9d5
Gruppenadministrator | 158b3e5a-d89d-460b-92b5-3b34985f0197
Gasteinladender | 4c730a1d-cc22-44af-8f9f-4eec635c7502
Helpdesk-Administrator | 108678c8-6628-44e1-8d01-caf598a6a5f5
Intune-Administrator | 79950741-23fa-4189-b2cb-46640601c497
Kaizala-Administrator | d6322af2-48e7-42e0-8c68-0bbe31af3412
Lizenzadministrator | 3355458a-e423-44bf-8b98-4ac5e572cea5
Nachrichtencenter-Datenschutzleseberechtigter | 6395db95-9fb8-42b9-b1ed-30a2405eee6f
Nachrichtencenter-Leser | fd5d37b8-4e24-434b-9e63-70ed3b759a16
Office-Apps-Administrator | 5f3870cd-b042-4f93-86d7-c9d77c664dc7
Kennwortadministrator | 466e48b7-5d66-4ae5-8911-1a118de74941
Power BI-Administrator | 984e83b8-8337-4255-91a1-acb663175ab4
Power Platform-Administrator | 76d6f95e-9a15-4d7d-8d21-00de00faf9fd
Privilegierter Authentifizierungsadministrator | 0829f731-b46d-419f-9742-aeb122367d11
Administrator für privilegierte Rollen | f20a725a-d1c8-4107-83ea-1171c97d00c7
Berichtsleser | 54635450-e8ed-4f2d-9632-07db2517b4de
Suchadministrator | c770a2f1-c9ba-4e60-9176-9f52b1eb1a31
Such-Editor | 6a6858c6-5f0d-44ac-87c7-0190fbedd271
Sicherheitsadministrator | 20fa50e3-6531-44d8-bd39-b251420568ad
Sicherheitsoperator | 43aae017-8e51-4188-91ab-e6debd572800
Sicherheitsleseberechtigter | 45035cd3-fd97-4250-8197-3a53d3562d9b
Dienstunterstützungsadministrator | 2c92cf45-c914-48f8-9bf9-fc14b28818ab
SharePoint-Administrator | e1c32229-875e-461d-ae24-3cb99116e86c
Skype for Business-Administrator | 0a8cee12-e21d-43ef-abd9-f1ea85710e30
Teams-Kommunikationsadministrator | 2393e455-6e13-4743-9f52-63fcec2b6a9c
Teams-Kommunikationssupporttechniker | 802dd94e-d717-46f6-af98-b9167071e9fc
Supportfachmann für die Teams-Kommunikation | ef547281-cf46-4cc6-bcaa-f5eac3f030c9
Teams-Dienstadministrator | 8846a0be-197b-443a-b13c-11192691fa24
Benutzeradministrator | 1f6eed58-7dd3-460b-a298-666f975427a1

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
