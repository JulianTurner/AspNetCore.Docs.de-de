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
ms.openlocfilehash: 50d019897310f9edd560069137ab73b39d780be8
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626232"
---
# <a name="azure-ad-groups-administrative-roles-and-user-defined-roles"></a><span data-ttu-id="efb94-103">Gruppen, Administratorrollen und benutzerdefinierte Rollen aus Azure AD</span><span class="sxs-lookup"><span data-stu-id="efb94-103">Azure AD Groups, Administrative Roles, and user-defined roles</span></span>

<span data-ttu-id="efb94-104">Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="efb94-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="efb94-105">Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:</span><span class="sxs-lookup"><span data-stu-id="efb94-105">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="efb94-106">Benutzerdefinierte Gruppen</span><span class="sxs-lookup"><span data-stu-id="efb94-106">User-defined groups</span></span>
  * <span data-ttu-id="efb94-107">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="efb94-107">Security</span></span>
  * <span data-ttu-id="efb94-108">O365</span><span class="sxs-lookup"><span data-stu-id="efb94-108">O365</span></span>
  * <span data-ttu-id="efb94-109">Verteilung</span><span class="sxs-lookup"><span data-stu-id="efb94-109">Distribution</span></span>
* <span data-ttu-id="efb94-110">Rollen</span><span class="sxs-lookup"><span data-stu-id="efb94-110">Roles</span></span>
  * <span data-ttu-id="efb94-111">Integrierte Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="efb94-111">Built-in Administrative Roles</span></span>
  * <span data-ttu-id="efb94-112">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="efb94-112">User-defined roles</span></span>

<span data-ttu-id="efb94-113">Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:</span><span class="sxs-lookup"><span data-stu-id="efb94-113">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="efb94-114">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="efb94-114">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="efb94-115">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="efb94-115">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="efb94-116">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="efb94-116">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="microsoft-graph-api-permission"></a><span data-ttu-id="efb94-117">Microsoft Graph-API-Berechtigung</span><span class="sxs-lookup"><span data-stu-id="efb94-117">Microsoft Graph API permission</span></span>

<span data-ttu-id="efb94-118">Für alle App-Benutzer, die über mehr als fünf integrierte AAD-Administratorrollen und Mitgliedschaften in Sicherheitsgruppen verfügen, ist ein Aufruf der [Microsoft Graph-API](/graph/use-the-api) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="efb94-118">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five built-in AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="efb94-119">Um Graph-API Aufrufe zuzulassen, erteilen Sie der eigenständigen oder Client-App einer gehosteten Blazor-Lösung eine der folgenden [Graph-API-Berechtigungen](/graph/permissions-reference) im Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="efb94-119">To permit Graph API calls, give the standalone or client app of a hosted Blazor solution any of the following [Graph API permissions](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="efb94-120">`Directory.Read.All` ist die Berechtigung mit den geringsten Rechten und die Berechtigung, die für das in diesem Artikel beschriebene Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="efb94-120">`Directory.Read.All` is the least-privileged permission and is the permission used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-built-in-administrative-roles"></a><span data-ttu-id="efb94-121">Benutzerdefinierte Gruppen und integrierte Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="efb94-121">User-defined groups and built-in Administrative Roles</span></span>

<span data-ttu-id="efb94-122">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln.</span><span class="sxs-lookup"><span data-stu-id="efb94-122">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="efb94-123">Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und integrierten AAD-Administratorrollen zu.</span><span class="sxs-lookup"><span data-stu-id="efb94-123">Assign users to user-defined AAD groups and built-in Administrative Roles.</span></span>

* [<span data-ttu-id="efb94-124">Rollen auf Grundlage von Azure AD-Sicherheitsgruppen</span><span class="sxs-lookup"><span data-stu-id="efb94-124">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="efb94-125">`groupMembershipClaims`-Attribut</span><span class="sxs-lookup"><span data-stu-id="efb94-125">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="efb94-126">In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der in AAD integrierten Rolle *Abrechnungsadministrator* zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="efb94-126">The following examples assume that a user is assigned to the AAD built-in *Billing Administrator* role.</span></span>

<span data-ttu-id="efb94-127">Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="efb94-127">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="efb94-128">Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="efb94-128">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="efb94-129">Wenn die Anzahl der zugewiesenen integrierten Azure-Administratorrollen und benutzerdefinierten Gruppen fünf überschreitet, sendet AAD einen `hasgroups` Anspruch mit einem `true`-Wert, anstatt einen `groups`-Anspruch zu senden.</span><span class="sxs-lookup"><span data-stu-id="efb94-129">When the number of assigned built-in Azure Administrative Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="efb94-130">Jede App, deren Benutzern ggf. mehr als fünf Rollen und Gruppen zugewiesen sind, muss einen separaten Graph-API-Aufruf ausführen, um die Rollen und Gruppen eines Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="efb94-130">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="efb94-131">Die in diesem Artikel bereitgestellte Beispielimplementierung behandelt dieses Szenario.</span><span class="sxs-lookup"><span data-stu-id="efb94-131">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="efb94-132">Weitere Informationen finden Sie in den Informationen zu `groups`- und `hasgroups`-Ansprüchen im Artikel [Microsoft Identity Platform-Zugriffstoken: Nutzlastansprüche](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="efb94-132">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="efb94-133">Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> um Arrayeigenschaften für Gruppen und Rollen.</span><span class="sxs-lookup"><span data-stu-id="efb94-133">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="efb94-134">Weisen Sie jeder Eigenschaft ein leeres Array zu, damit die Überprüfung auf `null` nicht erforderlich ist, wenn diese Eigenschaften später in `foreach`-Schleifen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="efb94-134">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="efb94-135">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="efb94-135">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="efb94-136">Erstellen Sie in der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="efb94-136">In the standalone app or the client app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="efb94-137">Verwenden Sie den richtigen Gültigkeitsbereich (Berechtigung) für Graph-API-Aufrufe, die Rollen- und Gruppeninformationen abrufen.</span><span class="sxs-lookup"><span data-stu-id="efb94-137">Use the correct scope (permission) for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="efb94-138">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="efb94-138">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="efb94-139">Fügen Sie in `Program.Main` (`Program.cs`) den <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Implementierungsdienst hinzu, und fügen Sie einen benannten <xref:System.Net.Http.HttpClient> zum Durchführen von Graph-API-Anforderungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="efb94-139">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="efb94-140">Im folgenden Beispiel wird dieser Client als `GraphAPI` bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="efb94-140">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="efb94-141">Erstellen Sie AAD-Verzeichnisobjektklassen, um die OData-Rollen (Open Data Protocol) und -Gruppen von einem Graph-API-Aufruf zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="efb94-141">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="efb94-142">Die OData-Daten werden im JSON-Format empfangen, und ein Aufruf von <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> füllt eine Instanz der `DirectoryObjects`-Klasse mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="efb94-142">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="efb94-143">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="efb94-143">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="efb94-144">Erstellen Sie eine benutzerdefinierte Benutzerfactory zum Verarbeiten von Rollen- und Gruppenansprüchen.</span><span class="sxs-lookup"><span data-stu-id="efb94-144">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="efb94-145">In der folgenden Beispielimplementierung wird auch das `roles`-Anspruchsarray verwendet, das im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="efb94-145">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="efb94-146">Wenn der `hasgroups`-Anspruch vorhanden ist, wird der benannte <xref:System.Net.Http.HttpClient> verwendet, um eine autorisierte Anforderung der Graph-API vorzunehmen, um die Rollen und Gruppen des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="efb94-146">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="efb94-147">Diese Implementierung verwendet den Microsoft Identity Platform v1.0-Endpunkt `https://graph.microsoft.com/v1.0/me/memberOf` ([API-Dokumentation](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="efb94-147">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span> <span data-ttu-id="efb94-148">Die Anleitungen in diesem Thema werden für Identity v2.0 aktualisiert, wenn die MSAL-Pakete für v2.0 aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="efb94-148">The guidance in this topic will be updated for Identity v2.0 when the MSAL packages are upgraded for v2.0.</span></span>

<span data-ttu-id="efb94-149">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="efb94-149">`CustomAccountFactory.cs`:</span></span>

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
    private readonly ILogger<CustomUserFactory> _logger;
    private readonly IHttpClientFactory _clientFactory;

    public CustomUserFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomUserFactory> logger)
        : base(accessor)
    {
        _clientFactory = clientFactory;
        _logger = logger;
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
                    var client = _clientFactory.CreateClient("GraphAPI");

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
                        _logger.LogError("Graph API request failure: {REASON}", 
                            response.ReasonPhrase);
                    }
                }
                catch (AccessTokenNotAvailableException exception)
                {
                    _logger.LogError("Graph API access token failure: {MESSAGE}", 
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

<span data-ttu-id="efb94-150">Für das Entfernen des ursprünglichen `groups`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="efb94-150">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="efb94-151">Die Vorgehensweise in diesem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="efb94-151">The approach in this example:</span></span>
>
> * <span data-ttu-id="efb94-152">Fügt eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse zum Anfügen von Zugriffstoken an ausgehende Anforderungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="efb94-152">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="efb94-153">Fügt einen benannten <xref:System.Net.Http.HttpClient> hinzu, um Web-API-Anforderungen an einen sicheren, externen Web-API-Endpunkt vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="efb94-153">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="efb94-154">Verwendet den benannten <xref:System.Net.Http.HttpClient>, um autorisierte Anforderungen vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="efb94-154">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="efb94-155">Allgemeine Erläuterungen zu diesem Ansatz finden Sie im Artikel <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="efb94-155">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="efb94-156">Registrieren Sie die Factory in `Program.Main` (`Program.cs`) der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung.</span><span class="sxs-lookup"><span data-stu-id="efb94-156">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or client app of a hosted Blazor solution.</span></span> <span data-ttu-id="efb94-157">Stimmen Sie dem `Directory.Read.All`-Berechtigungsbereich als zusätzlichen Bereich für die App zu:</span><span class="sxs-lookup"><span data-stu-id="efb94-157">Consent to the `Directory.Read.All` permission scope as an additional scope for the app:</span></span>

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

<span data-ttu-id="efb94-158">Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="efb94-158">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="efb94-159">Im folgenden Beispiel wird eine Richtlinie für die in AAD integrierte Rolle *Abrechnungsadministrator* erstellt:</span><span class="sxs-lookup"><span data-stu-id="efb94-159">The following example creates a policy for the AAD built-in *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="efb94-160">Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Gruppen-IDs für AAD-Administratorrollen](#aad-adminstrative-role-group-ids).</span><span class="sxs-lookup"><span data-stu-id="efb94-160">For the complete list of AAD role Object IDs, see the [AAD Adminstrative Role Group IDs](#aad-adminstrative-role-group-ids) section.</span></span>

<span data-ttu-id="efb94-161">In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="efb94-161">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="efb94-162">Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="efb94-162">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="efb94-163">Durch Verwendung der [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:</span><span class="sxs-lookup"><span data-stu-id="efb94-163">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="efb94-164">Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="efb94-164">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="efb94-165">Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="efb94-165">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="efb94-166">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="efb94-166">User-defined roles</span></span>

<span data-ttu-id="efb94-167">In AAD registrierte Apps können auch für die Verwendung von benutzerdefinierten Rollen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="efb94-167">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="efb94-168">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `roles`-Mitgliedschaftsanspruchs finden Sie in der Azure-Dokumentation unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).</span><span class="sxs-lookup"><span data-stu-id="efb94-168">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="efb94-169">Im folgenden Beispiel wird davon ausgegangen, dass eine App mit zwei Rollen konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="efb94-169">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="efb94-170">Zwar können Sie Rollen nur mit einem Azure AD Premium-Konto Sicherheitsgruppen zuweisen, allerdings können Sie mit einem Azure-Standardkonto Rollen Benutzer zuweisen und einen `roles`-Anspruch für Benutzer erhalten.</span><span class="sxs-lookup"><span data-stu-id="efb94-170">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="efb94-171">Für die Schritte in diesem Abschnitt ist kein Azure AD Premium-Konto erforderlich.</span><span class="sxs-lookup"><span data-stu-id="efb94-171">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="efb94-172">Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="efb94-172">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="efb94-173">Der von AAD gesendete einzelne `roles`-Anspruch zeigt die benutzerdefinierten Rollen jeweils als `value` für `appRoles` in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="efb94-173">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="efb94-174">Die App muss das JSON-Array mit Rollen in einzelne `role`-Ansprüche konvertieren.</span><span class="sxs-lookup"><span data-stu-id="efb94-174">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="efb94-175">Die im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert.</span><span class="sxs-lookup"><span data-stu-id="efb94-175">The `CustomUserFactory` shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="efb94-176">Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles) beschrieben in der eigenständigen App oder der Client-App einer gehosteten Blazor-Lösung hinzu, und registrieren Sie sie dort.</span><span class="sxs-lookup"><span data-stu-id="efb94-176">Add and register the `CustomUserFactory` in the standalone app or client app of a hosted Blazor solution as shown in the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span> <span data-ttu-id="efb94-177">Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="efb94-177">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="efb94-178">Geben Sie in `Program.Main` der eigenständigen oder Client-App einer gehosteten Blazor-Lösung den Anspruch mit dem Namen „`role`“ als Rollenanspruch an:</span><span class="sxs-lookup"><span data-stu-id="efb94-178">In `Program.Main` of the standalone app or client app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="efb94-179">Die Komponentenautorisierungsansätze sind Stand jetzt funktional.</span><span class="sxs-lookup"><span data-stu-id="efb94-179">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="efb94-180">Jeder der Autorisierungsmechanismen bei den Komponenten kann die Rolle `admin` verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="efb94-180">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="efb94-181">[`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="efb94-181">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="efb94-182">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="efb94-182">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="efb94-183">[Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="efb94-183">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="efb94-184">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="efb94-184">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-adminstrative-role-group-ids"></a><span data-ttu-id="efb94-185">Gruppen-IDs für AAD-Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="efb94-185">AAD Adminstrative Role Group IDs</span></span>

<span data-ttu-id="efb94-186">Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet.</span><span class="sxs-lookup"><span data-stu-id="efb94-186">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="efb94-187">Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="efb94-187">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="efb94-188">Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und integrierte Administratorrollen](#user-defined-groups-and-built-in-administrative-roles).</span><span class="sxs-lookup"><span data-stu-id="efb94-188">For more information, see the [User-defined groups and AAD built-in Administrative Roles](#user-defined-groups-and-built-in-administrative-roles) section.</span></span>

<span data-ttu-id="efb94-189">AAD-Administratorrolle</span><span class="sxs-lookup"><span data-stu-id="efb94-189">AAD Administrative Role</span></span> | <span data-ttu-id="efb94-190">ObjectID</span><span class="sxs-lookup"><span data-stu-id="efb94-190">Object ID</span></span>
--- | ---
<span data-ttu-id="efb94-191">Anwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-191">Application administrator</span></span> | <span data-ttu-id="efb94-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="efb94-192">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="efb94-193">Anwendungsentwickler</span><span class="sxs-lookup"><span data-stu-id="efb94-193">Application developer</span></span> | <span data-ttu-id="efb94-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="efb94-194">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="efb94-195">Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-195">Authentication administrator</span></span> | <span data-ttu-id="efb94-196">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="efb94-196">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="efb94-197">Azure DevOps-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-197">Azure DevOps administrator</span></span> | <span data-ttu-id="efb94-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="efb94-198">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="efb94-199">Azure Information Protection-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-199">Azure Information Protection administrator</span></span> | <span data-ttu-id="efb94-200">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="efb94-200">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="efb94-201">B2C-IEF-Schlüsselsatzadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-201">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="efb94-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="efb94-202">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="efb94-203">B2C-IEF-Richtlinienadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-203">B2C IEF Policy administrator</span></span> | <span data-ttu-id="efb94-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="efb94-204">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="efb94-205">B2C-Benutzerflowadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-205">B2C user flow administrator</span></span> | <span data-ttu-id="efb94-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="efb94-206">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="efb94-207">B2C-Administrator für Benutzerflowattribute</span><span class="sxs-lookup"><span data-stu-id="efb94-207">B2C user flow attribute administrator</span></span> | <span data-ttu-id="efb94-208">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="efb94-208">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="efb94-209">Rechnungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-209">Billing administrator</span></span> | <span data-ttu-id="efb94-210">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="efb94-210">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="efb94-211">Cloudanwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-211">Cloud application administrator</span></span> | <span data-ttu-id="efb94-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="efb94-212">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="efb94-213">Cloudgeräteadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-213">Cloud device administrator</span></span> | <span data-ttu-id="efb94-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="efb94-214">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="efb94-215">Complianceadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-215">Compliance administrator</span></span> | <span data-ttu-id="efb94-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="efb94-216">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="efb94-217">Compliancedatenadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-217">Compliance data administrator</span></span> | <span data-ttu-id="efb94-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="efb94-218">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="efb94-219">Administrator für den bedingten Zugriff</span><span class="sxs-lookup"><span data-stu-id="efb94-219">Conditional Access administrator</span></span> | <span data-ttu-id="efb94-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="efb94-220">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="efb94-221">Genehmigende Person für den LockBox-Kundenzugriff</span><span class="sxs-lookup"><span data-stu-id="efb94-221">Customer LockBox access approver</span></span> | <span data-ttu-id="efb94-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="efb94-222">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="efb94-223">Desktop Analytics-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-223">Desktop Analytics administrator</span></span> | <span data-ttu-id="efb94-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="efb94-224">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="efb94-225">Rolle „Verzeichnis lesen“</span><span class="sxs-lookup"><span data-stu-id="efb94-225">Directory readers</span></span> | <span data-ttu-id="efb94-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="efb94-226">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="efb94-227">Dynamics 365-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-227">Dynamics 365 administrator</span></span> | <span data-ttu-id="efb94-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="efb94-228">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="efb94-229">Exchange-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-229">Exchange administrator</span></span> | <span data-ttu-id="efb94-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="efb94-230">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="efb94-231">Externer Identitätsanbieteradministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-231">External Identity Provider administrator</span></span> | <span data-ttu-id="efb94-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="efb94-232">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="efb94-233">Globaler Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-233">Global administrator</span></span> | <span data-ttu-id="efb94-234">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="efb94-234">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="efb94-235">Globaler Leser</span><span class="sxs-lookup"><span data-stu-id="efb94-235">Global reader</span></span> | <span data-ttu-id="efb94-236">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="efb94-236">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="efb94-237">Gruppenadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-237">Groups administrator</span></span> | <span data-ttu-id="efb94-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="efb94-238">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="efb94-239">Gasteinladender</span><span class="sxs-lookup"><span data-stu-id="efb94-239">Guest inviter</span></span> | <span data-ttu-id="efb94-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="efb94-240">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="efb94-241">Helpdesk-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-241">Helpdesk administrator</span></span> | <span data-ttu-id="efb94-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="efb94-242">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="efb94-243">Intune-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-243">Intune administrator</span></span> | <span data-ttu-id="efb94-244">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="efb94-244">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="efb94-245">Kaizala-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-245">Kaizala administrator</span></span> | <span data-ttu-id="efb94-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="efb94-246">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="efb94-247">Lizenzadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-247">License administrator</span></span> | <span data-ttu-id="efb94-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="efb94-248">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="efb94-249">Nachrichtencenter-Datenschutzleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="efb94-249">Message center privacy reader</span></span> | <span data-ttu-id="efb94-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="efb94-250">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="efb94-251">Nachrichtencenter-Leser</span><span class="sxs-lookup"><span data-stu-id="efb94-251">Message center reader</span></span> | <span data-ttu-id="efb94-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="efb94-252">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="efb94-253">Office-Apps-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-253">Office apps administrator</span></span> | <span data-ttu-id="efb94-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="efb94-254">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="efb94-255">Kennwortadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-255">Password administrator</span></span> | <span data-ttu-id="efb94-256">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="efb94-256">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="efb94-257">Power BI-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-257">Power BI administrator</span></span> | <span data-ttu-id="efb94-258">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="efb94-258">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="efb94-259">Power Platform-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-259">Power platform administrator</span></span> | <span data-ttu-id="efb94-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="efb94-260">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="efb94-261">Privilegierter Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-261">Privileged authentication administrator</span></span> | <span data-ttu-id="efb94-262">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="efb94-262">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="efb94-263">Administrator für privilegierte Rollen</span><span class="sxs-lookup"><span data-stu-id="efb94-263">Privileged role administrator</span></span> | <span data-ttu-id="efb94-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="efb94-264">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="efb94-265">Berichtsleser</span><span class="sxs-lookup"><span data-stu-id="efb94-265">Reports reader</span></span> | <span data-ttu-id="efb94-266">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="efb94-266">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="efb94-267">Suchadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-267">Search administrator</span></span> | <span data-ttu-id="efb94-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="efb94-268">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="efb94-269">Such-Editor</span><span class="sxs-lookup"><span data-stu-id="efb94-269">Search editor</span></span> | <span data-ttu-id="efb94-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="efb94-270">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="efb94-271">Sicherheitsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-271">Security administrator</span></span> | <span data-ttu-id="efb94-272">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="efb94-272">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="efb94-273">Sicherheitsoperator</span><span class="sxs-lookup"><span data-stu-id="efb94-273">Security operator</span></span> | <span data-ttu-id="efb94-274">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="efb94-274">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="efb94-275">Sicherheitsleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="efb94-275">Security reader</span></span> | <span data-ttu-id="efb94-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="efb94-276">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="efb94-277">Dienstunterstützungsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-277">Service support administrator</span></span> | <span data-ttu-id="efb94-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="efb94-278">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="efb94-279">SharePoint-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-279">SharePoint administrator</span></span> | <span data-ttu-id="efb94-280">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="efb94-280">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="efb94-281">Skype for Business-Administrator</span><span class="sxs-lookup"><span data-stu-id="efb94-281">Skype for Business administrator</span></span> | <span data-ttu-id="efb94-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="efb94-282">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="efb94-283">Teams-Kommunikationsadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-283">Teams Communications Administrator</span></span> | <span data-ttu-id="efb94-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="efb94-284">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="efb94-285">Teams-Kommunikationssupporttechniker</span><span class="sxs-lookup"><span data-stu-id="efb94-285">Teams Communications Support Engineer</span></span> | <span data-ttu-id="efb94-286">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="efb94-286">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="efb94-287">Supportfachmann für die Teams-Kommunikation</span><span class="sxs-lookup"><span data-stu-id="efb94-287">Teams Communications Specialist</span></span> | <span data-ttu-id="efb94-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="efb94-288">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="efb94-289">Teams-Dienstadministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-289">Teams Service Administrator</span></span> | <span data-ttu-id="efb94-290">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="efb94-290">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="efb94-291">Benutzeradministrator</span><span class="sxs-lookup"><span data-stu-id="efb94-291">User administrator</span></span> | <span data-ttu-id="efb94-292">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="efb94-292">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="efb94-293">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="efb94-293">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
