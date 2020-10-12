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
ms.openlocfilehash: 7a0c606d82dd625c179ec89e22b9313dfa5d18b4
ms.sourcegitcommit: c026bf76a0e14a5ee68983519a63574c674e9ff7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91636776"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a>AAD-Gruppen (Azure Active Directory), Administratorrollen und benutzerdefinierte Rollen

Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)

Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:

* Benutzerdefinierte Gruppen
  * Sicherheit
  * Microsoft 365
  * Verteilung
* Rollen
  * AAD-Administratorrollen
  * Benutzerdefinierte Rollen

Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:

* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a>Microsoft Graph-API-Berechtigung

Für alle App-Benutzer, die über mehr als fünf AAD-Administratorrollen und Mitgliedschaften in Sicherheitsgruppen verfügen, ist ein Aufruf der [Microsoft Graph-API](/graph/use-the-api) erforderlich.

Um Graph-API Aufrufe zuzulassen, erteilen Sie der eigenständigen oder Client-App einer gehosteten Blazor-Lösung eine der folgenden [Graph-API-Berechtigungen](/graph/permissions-reference) im Azure-Portal:

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

`Directory.Read.All` ist die Berechtigung mit den geringsten Rechten und die Berechtigung, die für das in diesem Artikel beschriebene Beispiel verwendet wird.

## <a name="user-defined-groups-and-administrator-roles"></a>Benutzerdefinierte Gruppen und Administratorrollen

Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln. Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und AAD-Administratorrollen zu.

* [Rollen auf Grundlage von Azure AD-Sicherheitsgruppen](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [`groupMembershipClaims`-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der AAD-Rolle *Abrechnungsadministrator* zugewiesen ist.

Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an. Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.

Wenn die Anzahl der zugewiesenen AAD-Administratorrollen und benutzerdefinierten Gruppen fünf überschreitet, sendet AAD einen `hasgroups`-Anspruch mit dem Wert `true`, statt einen `groups`-Anspruch zu senden. Jede App, deren Benutzern ggf. mehr als fünf Rollen und Gruppen zugewiesen sind, muss einen separaten Graph-API-Aufruf ausführen, um die Rollen und Gruppen eines Benutzers abzurufen. Die in diesem Artikel bereitgestellte Beispielimplementierung behandelt dieses Szenario. Weitere Informationen finden Sie in den Informationen zu `groups`- und `hasgroups`-Ansprüchen im Artikel [Microsoft Identity Platform-Zugriffstoken: Nutzlastansprüche](/azure/active-directory/develop/access-tokens#payload-claims).

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

Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`. Im folgenden Beispiel wird eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* erstellt:

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Objekt-IDs für AAD-Administratorrollen](#aad-administrator-role-object-ids).

In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a>Autorisieren des Server-API-Zugriffs für benutzerdefinierte Gruppen und Administratorrollen

Die Server-API kann Benutzer in der clientseitigen WebAssembly-App nicht nur für den Zugriff auf Seiten und Ressourcen, sondern auch für den Zugriff auf sichere API-Endpunkte autorisieren. Nachdem die *Server*-App das Zugriffstoken des Benutzers überprüft hat, geschieht Folgendes:

* Die App verwendet den unveränderlichen [Objektbezeichneranspruch (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) des Benutzers aus dem JWT (`id_token`), um ein Zugriffstoken für die Graph-API abzurufen.
* Durch einen Graph-API-Aufruf werden die Mitgliedschaften des Benutzers in benutzerdefinierten Azure-Sicherheitsgruppen und Administratorrollen abgerufen.
* `group`-Ansprüche werden anhand von Mitgliedschaften festgelegt.
* Mithilfe von [Autorisierungsrichtlinien](xref:security/authorization/policies) kann der Benutzerzugriff auf Server-API-Endpunkte eingeschränkt werden.

> [!NOTE]
> Das Autorisieren von Benutzern auf der Grundlage ihrer [benutzerdefinierten AAD-Rollen](#user-defined-roles) wird in dieser Anleitung zurzeit noch nicht behandelt.

### <a name="packages"></a>Pakete

Fügen Sie der *Server*-App Paketverweise für folgende Pakete hinzu:

* [Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)
* [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)

### <a name="azure-configuration"></a>Azure-Konfiguration

* Vergewissern Sie sich, dass der *Server*-App-Registrierung API-Zugriff auf die Graph-API-Berechtigung für `Directory.Read.All` erteilt wird. Dabei handelt es sich um die Zugriffsebene mit den geringsten Rechten für Sicherheitsgruppen. Stellen Sie nach dem Zuweisen der Berechtigung sicher, dass die Administratoreinwilligung auf die Berechtigung angewendet wird.
* Weisen Sie der *Server*-App ein neues Clientgeheimnis zu. Notieren Sie sich das Geheimnis für die Konfiguration der App im Abschnitt [App-Einstellungen](#app-settings).

### <a name="app-settings"></a>App-Einstellungen

Erstellen Sie in der Datei mit den App-Einstellungen (`appsettings.json` oder `appsettings.Production.json`) einen `ClientSecret`-Eintrag mit dem Clientgeheimnis der *Server*-App aus dem Azure-Portal:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

Beispiel:

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

### <a name="authorization-policies"></a>Mit auf Namespace-Ebene konfigurierten Autorisierungsrichtlinien

Erstellen Sie [Autorisierungsrichtlinien](xref:security/authorization/policies) für AAD-Sicherheitsgruppen und AAD-Administratorrollen in den `Startup.ConfigureServices` der *Server*-App (`Startup.cs`) auf der Grundlage von Gruppenobjekt-IDs und [Objekt-IDs für AAD-Administratorrollen](#aad-administrator-role-object-ids).

Beispielsweise weist eine Richtlinie der Azure-Rolle „Abrechnungsadministrator“ die folgende Konfiguration auf:

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

Weitere Informationen finden Sie unter <xref:security/authorization/policies>.

### <a name="controller-access"></a>Controllerzugriff

Setzen Sie Richtlinien auf den Controllern der *Server*-App durch.

Im folgenden Beispiel wird der Zugriff auf Abrechnungsdaten vom `BillingDataController` gemäß Konfiguration im Abschnitt [Autorisierungsrichtlinien](#authorization-policies) auf Azure-Abrechnungsadministratoren mit dem Richtliniennamen `BillingAdmin` beschränkt:

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

### <a name="service-configuration"></a>Dienstkonfiguration

Fügen Sie in der Methode `Startup.ConfigureServices` der *Server*-App Logik hinzu, um die Graph-API aufzurufen und Benutzer-`group`-Ansprüche für die Sicherheitsgruppen und -rollen des Benutzers festzulegen.

> [!NOTE]
> Im Beispielcode in diesem Abschnitt wird die Active Directory-Authentifizierungsbibliothek (ADAL) verwendet, die auf Microsoft Identity Platform v1.0 basiert. Dieses Thema wird für Identity v2.0 für .NET 5 aktualisiert. Verfolgen Sie den Fortschritt dieser Arbeit unter [[RC1] Microsoft Identity Platform 2.0 für Blazor (dotnet/AspNetCore.Docs #19503)](https://github.com/dotnet/AspNetCore.Docs/issues/19503).

Für den Code in der `Startup`-Klasse der *Server*-App sind zusätzliche Namespaces erforderlich. Die folgenden `using`-Anweisungen enthalten die erforderlichen Namespaces für den in diesem Abschnitt folgenden Code:

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Linq;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.AzureAD.UI;
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Graph;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.IdentityModel.Logging;
```

Beim Konfigurieren von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:

* Schließen Sie optional die Verarbeitung für <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> ein. Beispielsweise kann die App fehlerhafte Authentifizierungen protokollieren.
* Erstellen Sie in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> einen Graph-API-Aufruf, um die Gruppen und Rollen des Benutzers abzurufen.

> [!WARNING]
> <xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> stellt in Protokollierungsmeldungen personenbezogene Informationen (PII) bereit. Aktivieren Sie PII nur für das Debuggen mit Testbenutzerkonten.

In `Startup.ConfigureServices`:

```csharp
#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));

services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
{
    options.Events = new JwtBearerEvents()
    {
        OnAuthenticationFailed = context =>
        {
            // Optional: Log the exception

#if DEBUG
            Console.WriteLine($"OnAuthenticationFailed: {context.Exception}");
#endif

            return Task.FromResult(0);
        },
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;
            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var authContext = new AuthenticationContext(
                    Configuration["AzureAd:Instance"] +
                    Configuration["AzureAd:TenantId"]);
                AuthenticationResult authResult = null;

                try
                {
                    authResult = await authContext.AcquireTokenSilentAsync(
                        "https://graph.microsoft.com", 
                        Configuration["AzureAd:ClientId"]);
                }
                catch (AdalException adalException)
                {
                    if (adalException.ErrorCode == 
                        AdalError.FailedToAcquireTokenSilently || 
                        adalException.ErrorCode == 
                        AdalError.UserInteractionRequired)
                    {
                        var userAssertion = new UserAssertion(accessToken.RawData,
                            "urn:ietf:params:oauth:grant-type:jwt-bearer", oid);
                        var clientCredential = new ClientCredential(
                            Configuration["AzureAd:ClientId"],
                            Configuration["AzureAd:ClientSecret"]);
                        authResult = await authContext.AcquireTokenAsync(
                            "https://graph.microsoft.com", clientCredential, 
                            userAssertion);
                    }
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", 
                                authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                        .Request().GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error

#if DEBUG
                    Console.WriteLine(
                        "OnTokenValidated: Service Exception: " +
                        $"{serviceException.Message}");
#endif
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }
            else
            {
                // Optional: Log missing OID claim

#if DEBUG
                Console.WriteLine($"OnTokenValidated: OID missing: " +
                    $"{accessToken.RawData}");
#endif
            }

            await Task.FromResult(0);
        }
    };
});
```

Im vorherigen Beispiel:

* Zuerst wird versucht, den automatischen Tokenabruf (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) auszuführen, weil das Zugriffstoken möglicherweise bereits im ADAL-Tokencache gespeichert wurde. Es ist schneller, das Token aus dem Cache abzurufen, als ein neues Token anzufordern.
* Wenn das Zugriffstoken nicht aus dem Cache abgerufen wird (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> oder <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> wird ausgelöst), wird eine Benutzerassertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) mit der Clientanmeldeinformation (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) ausgeführt, um das Token im Namen des Benutzers (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>) abzurufen. Anschließend kann der `Microsoft.Graph.GraphServiceClient` das Token für den Graph-API-Aufruf verwenden. Das Token wird im ADAL-Tokencache abgelegt. Bei zukünftigen Graph-API-Aufrufen für denselben Benutzer wird das Token automatisch mit <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> aus dem Cache abgerufen.

Der Code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> erhält keine transitiven Mitgliedschaften. So ändern Sie den Code, um direkte und transitive Mitgliedschaften zu erhalten:

* Für die Codezeile:

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  Ersetzen Sie die vorherige Zeile durch:

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* Für die Codezeile:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  Ersetzen Sie die vorherige Zeile durch:

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

Beim Erstellen von Ansprüchen unterscheidet der Code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nicht zwischen AAD-Sicherheitsgruppen und AAD-Administratorrollen. Damit die App zwischen Gruppen und Rollen unterscheidet, überprüfen Sie beim Durchlaufen der Gruppen und Rollen den `entry.ODataType`. Um separate Sicherheitsgruppen- und Rollenansprüche zu erstellen, verwenden Sie in etwa folgenden Code:

```csharp
foreach (var entry in groupsAndAzureRoles)
{
    if (entry.ODataType == "#microsoft.graph.group")
    {
        userIdentity.AddClaim(new Claim("group", entry.Id));
    }
    else
    {
        // entry.ODataType == "#microsoft.graph.directoryRole"
        userIdentity.AddClaim(new Claim("role", entry.Id));
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

Die im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert. Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles) beschrieben in der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung hinzu, und registrieren Sie sie dort. Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.

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

## <a name="aad-administrator-role-object-ids"></a>Objekt-IDs für AAD-Administratorrollen

Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet. Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren. Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles).

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
