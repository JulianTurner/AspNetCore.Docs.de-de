---
title: ASP.NET Core Blazor WebAssembly mit Azure Active Directory-Gruppen und -Rollen
author: guardrex
description: Erfahren Sie, wie Sie Blazor WebAssembly für die Verwendung von Azure Active Directory-Gruppen und -Rollen konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/aad-groups-roles
ms.openlocfilehash: 96a7dde9a5a756e40125ffda4c54fbf24fdc616a
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058258"
---
# <a name="azure-active-directory-aad-groups-administrator-roles-and-user-defined-roles"></a><span data-ttu-id="1d2b9-103">AAD-Gruppen (Azure Active Directory), Administratorrollen und benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-103">Azure Active Directory (AAD) groups, Administrator Roles, and user-defined roles</span></span>

<span data-ttu-id="1d2b9-104">Von [Luke Latham](https://github.com/guardrex) und [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-104">By [Luke Latham](https://github.com/guardrex) and [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

> [!NOTE]
> <span data-ttu-id="1d2b9-105">Dieser Artikel bezieht sich auf Blazor ASP.NET Core-Apps der Version 3.1 mit Microsoft Identity 1.0 und wird in Kürze mit Identity 2.0 auf Version 5.0 aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-105">This article applies to Blazor ASP.NET Core apps version 3.1 with Microsoft Identity 1.0 and will be updated to 5.0 with Identity 2.0 shortly.</span></span> <span data-ttu-id="1d2b9-106">Weitere Informationen finden Sie im folgenden GitHub-Issue und Pull Request.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-106">For more information, see the following GitHub issue and pull request.</span></span> <span data-ttu-id="1d2b9-107">Auf der Registerkarte **Files changed** (Geänderte Dateien) des Pull Requests finden Sie den Entwurfstext und Beispiele für die Updates für diesen Artikel.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-107">The the **Files changed** tab of the pull request contains the draft text and examples for the updates to the article.</span></span> <span data-ttu-id="1d2b9-108">Nach einer Überprüfung und finalen Updates wird der Pull Request mit der veröffentlichten Dokumentation zusammengeführt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-108">After review and final updates, the pull request will be merged into the live documentation set.</span></span>
>
> * <span data-ttu-id="1d2b9-109">Problem: [Blazor WASM mit AAD-Gruppen und -Rollen (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-109">Issue: [Blazor WASM with AAD groups and roles (dotnet/AspNetCore.Docs #17683)](https://github.com/dotnet/AspNetCore.Docs/issues/17683)</span></span>
> * <span data-ttu-id="1d2b9-110">Pull Request: [Blazor Version 5.0 des Artikels für AAD-Gruppen und -Rollen (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-110">Pull Request: [Blazor AAD groups and roles topic 5.0 (dotnet/AspNetCore.Docs #20856)](https://github.com/dotnet/AspNetCore.Docs/pull/20856)</span></span>

<span data-ttu-id="1d2b9-111">Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-111">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1d2b9-112">Benutzerdefinierte Gruppen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-112">User-defined groups</span></span>
  * <span data-ttu-id="1d2b9-113">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="1d2b9-113">Security</span></span>
  * <span data-ttu-id="1d2b9-114">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="1d2b9-114">Microsoft 365</span></span>
  * <span data-ttu-id="1d2b9-115">Verteilung</span><span class="sxs-lookup"><span data-stu-id="1d2b9-115">Distribution</span></span>
* <span data-ttu-id="1d2b9-116">Rollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-116">Roles</span></span>
  * <span data-ttu-id="1d2b9-117">AAD-Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-117">AAD Administrator Roles</span></span>
  * <span data-ttu-id="1d2b9-118">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-118">User-defined roles</span></span>

<span data-ttu-id="1d2b9-119">Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-119">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="1d2b9-120">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="1d2b9-120">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="1d2b9-121">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1d2b9-121">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="1d2b9-122">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="1d2b9-122">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

## <a name="scopes"></a><span data-ttu-id="1d2b9-123">Bereiche</span><span class="sxs-lookup"><span data-stu-id="1d2b9-123">Scopes</span></span>

<span data-ttu-id="1d2b9-124">Für alle App-Benutzer, die über mehr als fünf AAD-Administratorrollen und Mitgliedschaften in Sicherheitsgruppen verfügen, ist ein Aufruf der [Microsoft Graph-API](/graph/use-the-api) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-124">A [Microsoft Graph API](/graph/use-the-api) call is required for any app user with more than five AAD Administrator role and security group memberships.</span></span>

<span data-ttu-id="1d2b9-125">Um Graph-API-Aufrufe zuzulassen, erteilen Sie der eigenständigen oder *`Client`* -App einer gehosteten Blazor-Lösung beliebige der folgenden [Graph-API-Berechtigungen (Bereiche)](/graph/permissions-reference) im Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-125">To permit Graph API calls, give the standalone or *`Client`* app of a hosted Blazor solution any of the following [Graph API permissions (scopes)](/graph/permissions-reference) in the Azure portal:</span></span>

* `Directory.Read.All`
* `Directory.ReadWrite.All`
* `Directory.AccessAsUser.All`

<span data-ttu-id="1d2b9-126">`Directory.Read.All` ist der Bereich mit den geringsten Rechten, der auch für das in diesem Artikel beschriebene Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-126">`Directory.Read.All` is the least-privileged scope and is the scope used for the example described in this article.</span></span>

## <a name="user-defined-groups-and-administrator-roles"></a><span data-ttu-id="1d2b9-127">Benutzerdefinierte Gruppen und Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-127">User-defined groups and Administrator Roles</span></span>

<span data-ttu-id="1d2b9-128">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `groups`-Mitgliedschaftsanspruchs finden Sie in den folgenden Abschnitten in Azure-Artikeln.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-128">To configure the app in the Azure portal to provide a `groups` membership claim, see the following Azure articles.</span></span> <span data-ttu-id="1d2b9-129">Weisen Sie Benutzer benutzerdefinierten AAD-Gruppen und AAD-Administratorrollen zu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-129">Assign users to user-defined AAD groups and AAD Administrator Roles.</span></span>

* [<span data-ttu-id="1d2b9-130">Rollen auf Grundlage von Azure AD-Sicherheitsgruppen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-130">Roles using Azure AD security groups</span></span>](/azure/architecture/multitenant-identity/app-roles#roles-using-azure-ad-security-groups)
* [<span data-ttu-id="1d2b9-131">`groupMembershipClaims`-Attribut</span><span class="sxs-lookup"><span data-stu-id="1d2b9-131">`groupMembershipClaims` attribute</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)

<span data-ttu-id="1d2b9-132">In den folgenden Beispielen wird davon ausgegangen, dass ein Benutzer der AAD-Rolle *Abrechnungsadministrator* zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-132">The following examples assume that a user is assigned to the AAD *Billing Administrator* role.</span></span>

<span data-ttu-id="1d2b9-133">Der von AAD gesendete einzelne `groups`-Anspruch zeigt die Gruppen und Rollen des Benutzers als Objekt-IDs (GUIDs) in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-133">The single `groups` claim sent by AAD presents the user's groups and roles as Object IDs (GUIDs) in a JSON array.</span></span> <span data-ttu-id="1d2b9-134">Die App muss das JSON-Array mit Gruppen und Rollen in einzelne `group`-Ansprüche konvertieren, für die sie [Richtlinien](xref:security/authorization/policies) erstellen kann.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-134">The app must convert the JSON array of groups and roles into individual `group` claims that the app can build [policies](xref:security/authorization/policies) against.</span></span>

<span data-ttu-id="1d2b9-135">Wenn die Anzahl der zugewiesenen AAD-Administratorrollen und benutzerdefinierten Gruppen fünf überschreitet, sendet AAD einen `hasgroups`-Anspruch mit dem Wert `true`, statt einen `groups`-Anspruch zu senden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-135">When the number of assigned AAD Administrator Roles and user-defined groups exceeds five, AAD sends a `hasgroups` claim with a `true` value instead of sending a `groups` claim.</span></span> <span data-ttu-id="1d2b9-136">Jede App, deren Benutzern ggf. mehr als fünf Rollen und Gruppen zugewiesen sind, muss einen separaten Graph-API-Aufruf ausführen, um die Rollen und Gruppen eines Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-136">Any app that may have more than five roles and groups assigned to its users must make a separate Graph API call to obtain a user's roles and groups.</span></span> <span data-ttu-id="1d2b9-137">Die in diesem Artikel bereitgestellte Beispielimplementierung behandelt dieses Szenario.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-137">The example implementation provided in this article addresses this scenario.</span></span> <span data-ttu-id="1d2b9-138">Weitere Informationen finden Sie in den Informationen zu `groups`- und `hasgroups`-Ansprüchen im Artikel [Microsoft Identity Platform-Zugriffstoken: Nutzlastansprüche](/azure/active-directory/develop/access-tokens#payload-claims).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-138">For more information, see the `groups` and `hasgroups` claims information in [Microsoft identity platform access tokens: Payload claims](/azure/active-directory/develop/access-tokens#payload-claims) article.</span></span>

<span data-ttu-id="1d2b9-139">Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> um Arrayeigenschaften für Gruppen und Rollen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-139">Extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include array properties for groups and roles.</span></span> <span data-ttu-id="1d2b9-140">Weisen Sie jeder Eigenschaft ein leeres Array zu, damit die Überprüfung auf `null` nicht erforderlich ist, wenn diese Eigenschaften später in `foreach`-Schleifen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-140">Assign an empty array to each property so that checking for `null` isn't required when these properties are used in `foreach` loops later.</span></span>

<span data-ttu-id="1d2b9-141">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-141">`CustomUserAccount.cs`:</span></span>

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

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1d2b9-142">Befolgen Sie **eine** der folgenden Vorgehensweisen, um Ansprüche für AAD-Gruppen und -Rollen zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-142">Use **either** of the following approaches to create claims for AAD groups and roles:</span></span>

* [<span data-ttu-id="1d2b9-143">Verwenden des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="1d2b9-143">Use the Graph SDK</span></span>](#use-the-graph-sdk)
* [<span data-ttu-id="1d2b9-144">Verwenden eines benannten `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="1d2b9-144">Use a named `HttpClient`</span></span>](#use-a-named-httpclient)

### <a name="use-the-graph-sdk"></a><span data-ttu-id="1d2b9-145">Verwenden des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="1d2b9-145">Use the Graph SDK</span></span>

<span data-ttu-id="1d2b9-146">Fügen Sie der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung einen Paketverweis für [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-146">Add a package reference to the standalone app or *`Client`* app of a hosted Blazor solution for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="1d2b9-147">Fügen Sie die Hilfsprogrammklassen und die Konfiguration des Graph SDK im Abschnitt *Graph SDK* des Artikels <xref:blazor/security/webassembly/graph-api#graph-sdk> hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-147">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span>

<span data-ttu-id="1d2b9-148">Fügen Sie der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung (`CustomAccountFactory.cs`) die folgende benutzerdefinierte Benutzerkontofactory hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-148">Add the following custom user account factory to the standalone appo or *`Client`* app of a hosted Blazor solution (`CustomAccountFactory.cs`).</span></span> <span data-ttu-id="1d2b9-149">Die benutzerdefinierte Benutzerfactory wird zum Verarbeiten von Rollen- und Gruppenansprüchen verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-149">The custom user factory is used to process roles and groups claims.</span></span> <span data-ttu-id="1d2b9-150">Das `roles`-Anspruchsarray wird im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) behandelt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-150">The `roles` claim array is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="1d2b9-151">Wenn der `hasgroups`-Anspruch vorhanden ist, wird über das Graph SDK eine autorisierte Anforderung an die Graph-API ausgeführt, um die Rollen und Gruppen des Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-151">If the `hasgroups` claim is present, the Graph SDK is used to make an authorized request to Graph API to obtain the user's roles and groups:</span></span>

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
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
                userIdentity.AddClaim(new Claim("role", role));
            }

            if (userIdentity.HasClaim(c => c.Type == "hasgroups"))
            {
                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    var graphClient = ActivatorUtilities
                        .CreateInstance<GraphServiceClient>(serviceProvider);
                    var oid = userIdentity.Claims.FirstOrDefault(x => x.Type == "oid")?
                        .Value;

                    if (!string.IsNullOrEmpty(oid))
                    {
                        groupsAndAzureRoles = await graphClient.Users[oid].MemberOf
                            .Request().GetAsync();
                    }
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the error
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }

                var claim = userIdentity.Claims.FirstOrDefault(
                    c => c.Type == "hasgroups");

                userIdentity.RemoveClaim(claim);
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

<span data-ttu-id="1d2b9-152">Der vorangehende Code enthält keine transitiven Mitgliedschaften.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-152">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="1d2b9-153">Wenn die App direkte und transitive Gruppenmitgliedschaftsansprüche erfordert:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-153">If the app requires direct and transitive group membership claims:</span></span>

* <span data-ttu-id="1d2b9-154">Ändern Sie den Typ `IUserMemberOfCollectionWithReferencesPage` für `groupsAndAzureRoles` in `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-154">Change the `IUserMemberOfCollectionWithReferencesPage` type for `groupsAndAzureRoles` to `IUserTransitiveMemberOfCollectionWithReferencesPage`.</span></span>
* <span data-ttu-id="1d2b9-155">Ersetzen Sie die `MemberOf`-Eigenschaft durch `TransitiveMemberOf`, wenn Sie die Gruppen und Rollen des Benutzers anfordern.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-155">When requesting the user's groups and roles, replace the `MemberOf` property with `TransitiveMemberOf`.</span></span>

<span data-ttu-id="1d2b9-156">Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-156">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

### <a name="use-a-named-httpclient"></a><span data-ttu-id="1d2b9-157">Verwenden eines benannten `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="1d2b9-157">Use a named `HttpClient`</span></span>

::: moniker-end

<span data-ttu-id="1d2b9-158">Erstellen Sie in der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-158">In the standalone app or the *`Client`* app of a hosted Blazor solution, create a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class.</span></span> <span data-ttu-id="1d2b9-159">Verwenden Sie den richtigen Bereich für Graph-API-Aufrufe, mit denen Rollen- und Gruppeninformationen abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-159">Use the correct scope for Graph API calls that obtain role and group information.</span></span>

<span data-ttu-id="1d2b9-160">`GraphAPIAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-160">`GraphAPIAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="1d2b9-161">Fügen Sie in `Program.Main` (`Program.cs`) den <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Implementierungsdienst hinzu, und fügen Sie einen benannten <xref:System.Net.Http.HttpClient> zum Durchführen von Graph-API-Anforderungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-161">In `Program.Main` (`Program.cs`), add the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> implementation service and add a named <xref:System.Net.Http.HttpClient> for making Graph API requests.</span></span> <span data-ttu-id="1d2b9-162">Im folgenden Beispiel wird dieser Client als `GraphAPI` bezeichnet:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-162">The following example names the client `GraphAPI`:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

<span data-ttu-id="1d2b9-163">Erstellen Sie AAD-Verzeichnisobjektklassen, um die OData-Rollen (Open Data Protocol) und -Gruppen von einem Graph-API-Aufruf zu empfangen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-163">Create AAD directory objects classes to receive the Open Data Protocol (OData) roles and groups from a Graph API call.</span></span> <span data-ttu-id="1d2b9-164">Die OData-Daten werden im JSON-Format empfangen, und ein Aufruf von <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> füllt eine Instanz der `DirectoryObjects`-Klasse mit Daten auf.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-164">The OData arrives in JSON format, and a call to <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> populates an instance of the `DirectoryObjects` class.</span></span>

<span data-ttu-id="1d2b9-165">`DirectoryObjects.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-165">`DirectoryObjects.cs`:</span></span>

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

<span data-ttu-id="1d2b9-166">Erstellen Sie eine benutzerdefinierte Benutzerfactory zum Verarbeiten von Rollen- und Gruppenansprüchen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-166">Create a custom user factory to process roles and groups claims.</span></span> <span data-ttu-id="1d2b9-167">In der folgenden Beispielimplementierung wird auch das `roles`-Anspruchsarray verwendet, das im Abschnitt [Benutzerdefinierte Rollen](#user-defined-roles) beschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-167">The following example implementation also handles the `roles` claim array, which is covered in the [User-defined roles](#user-defined-roles) section.</span></span> <span data-ttu-id="1d2b9-168">Wenn der `hasgroups`-Anspruch vorhanden ist, wird der benannte <xref:System.Net.Http.HttpClient> verwendet, um eine autorisierte Anforderung der Graph-API vorzunehmen, um die Rollen und Gruppen des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-168">If the `hasgroups` claim is present, the named <xref:System.Net.Http.HttpClient> is used to make an authorized request to Graph API to obtain the user's roles and groups.</span></span> <span data-ttu-id="1d2b9-169">Diese Implementierung verwendet den Microsoft Identity Platform v1.0-Endpunkt `https://graph.microsoft.com/v1.0/me/memberOf` ([API-Dokumentation](/graph/api/user-list-memberof)).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-169">This implementation uses the Microsoft Identity Platform v1.0 endpoint `https://graph.microsoft.com/v1.0/me/memberOf` ([API documentation](/graph/api/user-list-memberof)).</span></span>

<span data-ttu-id="1d2b9-170">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-170">`CustomAccountFactory.cs`:</span></span>

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
                    logger.LogError("Graph API access token failure: {Message}", 
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

<span data-ttu-id="1d2b9-171">Für das Entfernen des ursprünglichen `groups`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-171">There's no need to provide code to remove the original `groups` claim, if present, because it's automatically removed by the framework.</span></span>

> [!NOTE]
> <span data-ttu-id="1d2b9-172">Die Vorgehensweise in diesem Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-172">The approach in this example:</span></span>
>
> * <span data-ttu-id="1d2b9-173">Fügt eine benutzerdefinierte <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>-Klasse zum Anfügen von Zugriffstoken an ausgehende Anforderungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-173">Adds a custom <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> class to attach access tokens to outgoing requests.</span></span>
> * <span data-ttu-id="1d2b9-174">Fügt einen benannten <xref:System.Net.Http.HttpClient> hinzu, um Web-API-Anforderungen an einen sicheren, externen Web-API-Endpunkt vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-174">Adds a named <xref:System.Net.Http.HttpClient> for making web API requests to a secure, external web API endpoint.</span></span>
> * <span data-ttu-id="1d2b9-175">Verwendet den benannten <xref:System.Net.Http.HttpClient>, um autorisierte Anforderungen vorzunehmen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-175">Uses the named <xref:System.Net.Http.HttpClient> to make authorized requests.</span></span>
>
> <span data-ttu-id="1d2b9-176">Allgemeine Erläuterungen zu diesem Ansatz finden Sie im Artikel <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class>.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-176">General coverage for this approach is found in the <xref:blazor/security/webassembly/additional-scenarios#custom-authorizationmessagehandler-class> article.</span></span>

<span data-ttu-id="1d2b9-177">Registrieren Sie die Factory in `Program.Main` (`Program.cs`) der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-177">Register the factory in `Program.Main` (`Program.cs`) of the standalone app or *`Client`* app of a hosted Blazor solution.</span></span> <span data-ttu-id="1d2b9-178">Geben Sie Ihre Einwilligung für den `Directory.Read.All`-Bereich als zusätzlichen Bereich für die App:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-178">Consent to the `Directory.Read.All` scope as an additional scope for the app:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

...

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

## <a name="authorization-configuration"></a><span data-ttu-id="1d2b9-179">Konfigurieren der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="1d2b9-179">Authorization configuration</span></span>

<span data-ttu-id="1d2b9-180">Erstellen Sie für jede Gruppe oder Rolle eine [Richtlinie](xref:security/authorization/policies) in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-180">Create a [policy](xref:security/authorization/policies) for each group or role in `Program.Main`.</span></span> <span data-ttu-id="1d2b9-181">Im folgenden Beispiel wird eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* erstellt:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-181">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1d2b9-182">Eine vollständige Liste der Objekt-IDs für die einzelnen AAD-Rollen finden Sie im Abschnitt [Objekt-IDs für AAD-Administratorrollen](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-182">For the complete list of AAD role Object IDs, see the [AAD Administrator Role Object IDs](#aad-administrator-role-object-ids) section.</span></span>

<span data-ttu-id="1d2b9-183">In den folgenden Beispielen verwendet die App die obige Richtlinie für die Autorisierung des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-183">In the following examples, the app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="1d2b9-184">Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-184">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="1d2b9-185">Durch Verwendung der [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-185">Access to an entire component can be based on the policy using [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="1d2b9-186">Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-186">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="1d2b9-187">Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic):</span><span class="sxs-lookup"><span data-stu-id="1d2b9-187">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic):</span></span>

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

## <a name="authorize-server-api-access-for-user-defined-groups-and-administrator-roles"></a><span data-ttu-id="1d2b9-188">Autorisieren des Server-API-Zugriffs für benutzerdefinierte Gruppen und Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-188">Authorize server API access for user-defined groups and Administrator Roles</span></span>

<span data-ttu-id="1d2b9-189">Die Server-API kann Benutzer in der clientseitigen WebAssembly-App nicht nur für den Zugriff auf Seiten und Ressourcen, sondern auch für den Zugriff auf sichere API-Endpunkte autorisieren.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-189">In addition to authorizing users in the client-side WebAssembly app to access pages and resources, the server API can authorize users for access to secure API endpoints.</span></span> <span data-ttu-id="1d2b9-190">Nachdem die *Server*-App das Zugriffstoken des Benutzers überprüft hat, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-190">After the *Server* app validates the user's access token:</span></span>

* <span data-ttu-id="1d2b9-191">Die Server-API-App verwendet den unveränderlichen [Objektbezeichneranspruch (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) des Benutzers aus dessen Zugriffstoken, um ein Zugriffstoken für die Graph-API abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-191">The server API app uses the user's immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) from their access token to obtain an access token for Graph API.</span></span>
* <span data-ttu-id="1d2b9-192">Durch einen Graph-API-Aufruf von [`memberOf`](/graph/api/user-list-memberof) für den Benutzer werden dessen Mitgliedschaften in benutzerdefinierten Azure-Sicherheitsgruppen und Administratorrollen abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-192">A Graph API call obtains the user's Azure user-defined security group and Administrator Role memberships by calling [`memberOf`](/graph/api/user-list-memberof) on the user.</span></span>
* <span data-ttu-id="1d2b9-193">`group`-Ansprüche werden anhand von Mitgliedschaften festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-193">Memberships are used to establish `group` claims.</span></span>
* <span data-ttu-id="1d2b9-194">Mithilfe von [Autorisierungsrichtlinien](xref:security/authorization/policies) kann der Benutzerzugriff auf Server-API-Endpunkte für die gesamte App eingeschränkt werden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-194">[Authorization policies](xref:security/authorization/policies) can be used to limit user access to server API endpoints throughout the app.</span></span>

> [!NOTE]
> <span data-ttu-id="1d2b9-195">Das Autorisieren von Benutzern auf der Grundlage ihrer [benutzerdefinierten AAD-Rollen](#user-defined-roles) wird in dieser Anleitung zurzeit noch nicht behandelt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-195">This guidance doesn't currently include authorizing users on the basis of their [AAD user-defined roles](#user-defined-roles).</span></span>

<span data-ttu-id="1d2b9-196">Mithilfe der Anleitung in diesem Abschnitt wird die Server-API-App als [*Daemon-App*](/azure/active-directory/develop/scenario-daemon-overview) für den Microsoft Graph-API-Aufruf konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-196">The guidance in this section configures the server API app as a [*daemon app*](/azure/active-directory/develop/scenario-daemon-overview) for the Microsoft Graph API call.</span></span> <span data-ttu-id="1d2b9-197">Folgendes wird damit **nicht** erreicht:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-197">This approach does **not**:</span></span>

* <span data-ttu-id="1d2b9-198">Erzwingen des `access_as_user`-Bereichs</span><span class="sxs-lookup"><span data-stu-id="1d2b9-198">Require the the `access_as_user` scope.</span></span>
* <span data-ttu-id="1d2b9-199">Zugreifen auf die Graph-API im Namen des Benutzers/Clients, der die API-Anforderung ausführt</span><span class="sxs-lookup"><span data-stu-id="1d2b9-199">Access Graph API on behalf of the user/client making the API request.</span></span>

<span data-ttu-id="1d2b9-200">Für den Graph-API-Aufruf durch die Server-API-App ist nur der Graph-API Bereich **Anwendung** für `Directory.Read.All` für die Server-API-App im Azure-Portal erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-200">The call to Graph API by the server API app only requires a server API app **Application** Graph API scope for `Directory.Read.All` in the Azure portal.</span></span> <span data-ttu-id="1d2b9-201">Diese Vorgehensweise verhindert jeglichen Zugriff der Client-App auf Verzeichnisdaten, der von der Server-API nicht explizit zugelassen wird.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-201">This approach absolutely prevents the client app from accessing directory data that the server API doesn't explicitly permit.</span></span> <span data-ttu-id="1d2b9-202">Die Client-App kann nur auf die Controllerendpunkte der Server-API-App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-202">The client app is only able to access the server API app's controller endpoints.</span></span>

### <a name="azure-configuration"></a><span data-ttu-id="1d2b9-203">Azure-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="1d2b9-203">Azure configuration</span></span>

* <span data-ttu-id="1d2b9-204">Vergewissern Sie sich, dass die *Server*-App-Registrierung den Graph-API-Bereich **Anwendung** (nicht **Delegiert**) für `Directory.Read.All` erhält. Dabei handelt es sich um die Zugriffsebene mit den geringsten Rechten für Sicherheitsgruppen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-204">Confirm that the *Server* app registration is given **Application** (not **Delegated**) Graph API scope for `Directory.Read.All`, which is the least-privileged access level for security groups.</span></span> <span data-ttu-id="1d2b9-205">Stellen Sie nach dem Zuweisen des Bereichs sicher, dass die Administratoreinwilligung auf den Bereich angewandt wird.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-205">Confirm that admin consent is applied to the scope after making the scope assignment.</span></span>
* <span data-ttu-id="1d2b9-206">Weisen Sie der *Server*-App ein neues Clientgeheimnis zu.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-206">Assign a new client secret to the *Server* app.</span></span> <span data-ttu-id="1d2b9-207">Notieren Sie sich das Geheimnis für die Konfiguration der App im Abschnitt [App-Einstellungen](#app-settings).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-207">Note the secret for the app's configuration in the [App settings](#app-settings) section.</span></span>

### <a name="app-settings"></a><span data-ttu-id="1d2b9-208">App-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-208">App settings</span></span>

<span data-ttu-id="1d2b9-209">Erstellen Sie in der Datei mit den App-Einstellungen (`appsettings.json` oder `appsettings.Production.json`) einen `ClientSecret`-Eintrag mit dem Clientgeheimnis der *Server*-App aus dem Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-209">In the app settings file (`appsettings.json` or `appsettings.Production.json`), create a `ClientSecret` entry with the *Server* app's client secret from the Azure portal:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "XXXXXXXXXXXX.onmicrosoft.com",
  "TenantId": "{GUID}",
  "ClientId": "{GUID}",
  "ClientSecret": "{CLIENT SECRET}"
},
```

<span data-ttu-id="1d2b9-210">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-210">For example:</span></span>

```json
"AzureAd": {
  "Instance": "https://login.microsoftonline.com/",
  "Domain": "contoso.onmicrosoft.com",
  "TenantId": "34bf0ec1-7aeb-4b5d-ba42-82b059b3abe8",
  "ClientId": "05d198e0-38c6-4efc-a67c-8ee87ed9bd3d",
  "ClientSecret": "54uE~9a.-wW91fe8cRR25ag~-I5gEq_92~"
},
```

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="1d2b9-211">Wenn die Herausgeberdomäne des Mandanten nicht überprüft wird, wird als Server-API-Bereich für den Benutzer-/Clientzugriff ein `https://`-URI verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-211">If the tenant publisher domain isn't verified, the server API scope for user/client access uses an `https://`-based URI.</span></span> <span data-ttu-id="1d2b9-212">In diesem Szenario ist für die Server-API-App die Konfiguration einer `Audience` in der Datei `appsettings.json` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-212">In this scenario, the server API app requires `Audience` configuration in the `appsettings.json` file.</span></span> <span data-ttu-id="1d2b9-213">In der folgenden Konfiguration umfasst das Ende des `Audience`-Werts **nicht** den Standardbereich `/{DEFAULT SCOPE}` (wobei der Platzhalter `{DEFAULT SCOPE}` den Standardbereich darstellt):</span><span class="sxs-lookup"><span data-stu-id="1d2b9-213">In the following configuration, the end of the `Audience` value does **not** include the default scope `/{DEFAULT SCOPE}`, where the placeholder `{DEFAULT SCOPE}` is the default scope:</span></span>
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://{TENANT}.onmicrosoft.com/{SERVER API APP CLIENT ID OR CUSTOM VALUE}"
>   }
> }
>
> In the preceding configuration, the placeholder `{TENANT}` is the app's tenant, and the placeholder `{SERVER API APP CLIENT ID OR CUSTOM VALUE}` is the server API app's `ClientId` or custom value provided in the Azure portal's app registration.
>
> Example:
>
> ```json
> {
>   "AzureAd": {
>     ...
>
>     "Audience": "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd"
>   }
> }
> ```
>
> <span data-ttu-id="1d2b9-214">In der vorherigen Beispielkonfiguration:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-214">In the preceding example configuration:</span></span>
>
> * <span data-ttu-id="1d2b9-215">Die Mandantendomäne ist `contoso.onmicrosoft.com`.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-215">The tenant domain is `contoso.onmicrosoft.com`.</span></span>
> * <span data-ttu-id="1d2b9-216">Die `ClientId` der Server-API-App lautet `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-216">The server API app `ClientId` is `41451fa7-82d9-4673-8fa5-69eff5a761fd`.</span></span>
>
> > [!NOTE]
> > <span data-ttu-id="1d2b9-217">Das explizite Konfigurieren einer `Audience` ist für Apps mit einer verifizierten Herausgeberdomäne **nicht** erforderlich, wenn deren API-Bereich auf `api://` basiert.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-217">Configuring an `Audience` explicitly usually is **not** required for app's with a verified publisher domain that has an `api://`-based API scope.</span></span>
>
> <span data-ttu-id="1d2b9-218">Weitere Informationen finden Sie unter <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-218">For more information, see <xref:blazor/security/webassembly/hosted-with-azure-active-directory#app-settings>.</span></span>

::: moniker-end

### <a name="authorization-policies"></a><span data-ttu-id="1d2b9-219">Mit auf Namespace-Ebene konfigurierten Autorisierungsrichtlinien</span><span class="sxs-lookup"><span data-stu-id="1d2b9-219">Authorization policies</span></span>

<span data-ttu-id="1d2b9-220">Erstellen Sie [Autorisierungsrichtlinien](xref:security/authorization/policies) für AAD-Sicherheitsgruppen und AAD-Administratorrollen in den `Startup.ConfigureServices` der *Server*-App (`Startup.cs`) auf der Grundlage von Gruppenobjekt-IDs und [Objekt-IDs für AAD-Administratorrollen](#aad-administrator-role-object-ids).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-220">Create [authorization policies](xref:security/authorization/policies) for AAD security groups and AAD Administrator Roles in the *Server* app's `Startup.ConfigureServices` (`Startup.cs`) based on group object IDs and [AAD Administrator Role object IDs](#aad-administrator-role-object-ids).</span></span>

<span data-ttu-id="1d2b9-221">Beispielsweise weist eine Richtlinie der Azure-Rolle „Abrechnungsadministrator“ die folgende Konfiguration auf:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-221">For example, an Azure Billing Administrator Role policy has the following configuration:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdmin", policy => 
        policy.RequireClaim("group", "69ff516a-b57d-4697-a429-9de4af7b5609"));
});
```

<span data-ttu-id="1d2b9-222">Weitere Informationen finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-222">For more information, see <xref:security/authorization/policies>.</span></span>

### <a name="controller-access"></a><span data-ttu-id="1d2b9-223">Controllerzugriff</span><span class="sxs-lookup"><span data-stu-id="1d2b9-223">Controller access</span></span>

<span data-ttu-id="1d2b9-224">Setzen Sie Richtlinien auf den Controllern der *Server*-App durch.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-224">Require policies on the *Server* app's controllers.</span></span>

<span data-ttu-id="1d2b9-225">Im folgenden Beispiel wird der Zugriff auf Abrechnungsdaten vom `BillingDataController` gemäß Konfiguration im Abschnitt [Autorisierungsrichtlinien](#authorization-policies) auf Azure-Abrechnungsadministratoren mit dem Richtliniennamen `BillingAdmin` beschränkt:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-225">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdmin`, as configured in the [Authorization policies](#authorization-policies) section:</span></span>

```csharp
[Authorize(Policy = "BillingAdmin")]
[ApiController]
[Route("[controller]")]
public class BillingDataController : ControllerBase
{
    ...
}
```

::: moniker range=">= aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="1d2b9-226">Pakete</span><span class="sxs-lookup"><span data-stu-id="1d2b9-226">Packages</span></span>

<span data-ttu-id="1d2b9-227">Fügen Sie der *Server*-App Paketverweise für folgende Pakete hinzu:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-227">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="1d2b9-228">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="1d2b9-228">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="1d2b9-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-229">[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)</span></span>

### <a name="services"></a><span data-ttu-id="1d2b9-230">Dienste</span><span class="sxs-lookup"><span data-stu-id="1d2b9-230">Services</span></span>

<span data-ttu-id="1d2b9-231">In der `Startup.ConfigureServices`-Methode der *Server*-App sind für den Code in der `Startup`-Klasse der *Server*-App zusätzliche Namespaces erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-231">In the *Server* app's `Startup.ConfigureServices` method, additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="1d2b9-232">Fügen Sie `Startup.cs` die folgenden Namespaces hinzu:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-232">Add the following namespaces to `Startup.cs`:</span></span>

```csharp
using System;
using System.IdentityModel.Tokens.Jwt;
using System.Net.Http.Headers;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.Graph;
using Microsoft.Identity.Client;
using Microsoft.IdentityModel.Logging;
```

<span data-ttu-id="1d2b9-233">Beim Konfigurieren von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-233">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="1d2b9-234">Schließen Sie optional die Verarbeitung für <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> ein.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-234">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1d2b9-235">Beispielsweise kann die App fehlerhafte Authentifizierungen protokollieren.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-235">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="1d2b9-236">Erstellen Sie in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> einen Graph-API-Aufruf, um die Gruppen und Rollen des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-236">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="1d2b9-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> stellt in Protokollierungsmeldungen personenbezogene Informationen (PII) bereit.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-237"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="1d2b9-238">Aktivieren Sie PII nur für das Debuggen mit Testbenutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-238">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="1d2b9-239">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-239">In `Startup.ConfigureServices`:</span></span>

```csharp
JwtSecurityTokenHandler.DefaultMapInboundClaims = false;

#if DEBUG
IdentityModelEventSource.ShowPII = true;
#endif

var scopes = new string[] { "https://graph.microsoft.com/.default" };

var app = ConfidentialClientApplicationBuilder.Create(Configuration["AzureAd:ClientId"])
   .WithClientSecret(Configuration["AzureAd:ClientSecret"])
   .WithAuthority(new Uri(Configuration["AzureAd:Instance"] + Configuration["AzureAd:Domain"]))
   .Build();

services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(options =>
{
    Configuration.Bind("AzureAd", options);

    options.Events = new JwtBearerEvents()
    {
        OnTokenValidated = async context =>
        {
            var accessToken = context.SecurityToken as JwtSecurityToken;

            var oid = accessToken.Claims.FirstOrDefault(x => x.Type == "oid")?
                .Value;

            if (!string.IsNullOrEmpty(oid))
            {
                var userIdentity = (ClaimsIdentity)context.Principal.Identity;

                AuthenticationResult authResult = null;

                try
                {
                    authResult = await app.AcquireTokenForClient(scopes)
                        .ExecuteAsync();
                }
                catch (MsalUiRequiredException ex)
                {
                    // Optional: Log the exception
                }
                catch (MsalServiceException ex)
                {
                    // Optional: Log the exception
                }

                var graphClient = new GraphServiceClient(
                    new DelegateAuthenticationProvider(async requestMessage => {
                        requestMessage.Headers.Authorization =
                            new AuthenticationHeaderValue("Bearer", authResult.AccessToken);

                        await Task.CompletedTask;
                    }));

                IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = 
                    null;

                try
                {
                    groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request()
                        .GetAsync();
                }
                catch (ServiceException serviceException)
                {
                    // Optional: Log the exception
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
}, 
options =>
{
    Configuration.Bind("AzureAd", options);
});
```

<span data-ttu-id="1d2b9-240">Im obigen Code ist die Behandlung der folgenden Tokenfehler optional:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-240">In the preceding code, handling the following token errors is optional:</span></span>

* <span data-ttu-id="1d2b9-241">`MsalUiRequiredException`: Die App verfügt nicht über ausreichende Berechtigungen (Bereiche).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-241">`MsalUiRequiredException`: The app doesn't have sufficient permissions (scopes).</span></span>
  * <span data-ttu-id="1d2b9-242">Überprüfen Sie, ob die Bereiche der Server-API-App im Azure-Portal die Berechtigung **Anwendung** für `Directory.Read.All` enthalten.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-242">Determine if the server API app scopes in the Azure portal include an **Application** permission for `Directory.Read.All`.</span></span>
  * <span data-ttu-id="1d2b9-243">Vergewissern Sie sich, dass der Mandantenadministrator der App Berechtigungen erteilt hat.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-243">Confirm that the tenant administrator has granted permissions to the app.</span></span>
* <span data-ttu-id="1d2b9-244">`MsalServiceException` (`AADSTS70011`): Überprüfen Sie, ob der Bereich `https://graph.microsoft.com/.default` ist.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-244">`MsalServiceException` (`AADSTS70011`): Confirm that the scope is `https://graph.microsoft.com/.default`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

### <a name="packages"></a><span data-ttu-id="1d2b9-245">Pakete</span><span class="sxs-lookup"><span data-stu-id="1d2b9-245">Packages</span></span>

<span data-ttu-id="1d2b9-246">Fügen Sie der *Server*-App Paketverweise für folgende Pakete hinzu:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-246">Add package references to the *Server* app for the following packages:</span></span>

* [<span data-ttu-id="1d2b9-247">Microsoft.Graph</span><span class="sxs-lookup"><span data-stu-id="1d2b9-247">Microsoft.Graph</span></span>](https://www.nuget.org/packages/Microsoft.Graph)
* <span data-ttu-id="1d2b9-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-248">[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages?q=Microsoft.IdentityModel.Clients.ActiveDirectory)</span></span>

### <a name="service-configuration"></a><span data-ttu-id="1d2b9-249">Dienstkonfiguration</span><span class="sxs-lookup"><span data-stu-id="1d2b9-249">Service configuration</span></span>

<span data-ttu-id="1d2b9-250">Fügen Sie in der Methode `Startup.ConfigureServices` der *Server*-App Logik hinzu, um die Graph-API aufzurufen und Benutzer-`group`-Ansprüche für die Sicherheitsgruppen und -rollen des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-250">In the *Server* app's `Startup.ConfigureServices` method add logic to make the Graph API call and establish user `group` claims for the user's security groups and roles.</span></span>

> [!NOTE]
> <span data-ttu-id="1d2b9-251">Im Beispielcode in diesem Abschnitt wird die Active Directory-Authentifizierungsbibliothek (ADAL) verwendet, die auf Microsoft Identity Platform v1.0 basiert.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-251">The example code in this section uses the Active Directory Authentication Library (ADAL), which is based on Microsoft Identity Platform v1.0.</span></span>

<span data-ttu-id="1d2b9-252">Für den Code in der `Startup`-Klasse der *Server*-App sind zusätzliche Namespaces erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-252">Additional namespaces are required for the code in the `Startup` class of the *Server* app.</span></span> <span data-ttu-id="1d2b9-253">Die folgenden `using`-Anweisungen enthalten die erforderlichen Namespaces für den in diesem Abschnitt folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-253">The following set of `using` statements includes the required namespaces for the code that follows in this section:</span></span>

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

<span data-ttu-id="1d2b9-254">Beim Konfigurieren von <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-254">When configuring <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents>:</span></span>

* <span data-ttu-id="1d2b9-255">Schließen Sie optional die Verarbeitung für <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType> ein.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-255">Optionally include processing for <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnAuthenticationFailed?displayProperty=nameWithType>.</span></span> <span data-ttu-id="1d2b9-256">Beispielsweise kann die App fehlerhafte Authentifizierungen protokollieren.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-256">For example, the app can log failed authentication.</span></span>
* <span data-ttu-id="1d2b9-257">Erstellen Sie in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType> einen Graph-API-Aufruf, um die Gruppen und Rollen des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-257">In <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated?displayProperty=nameWithType>, make a Graph API call to obtain the user's groups and roles.</span></span>

> [!WARNING]
> <span data-ttu-id="1d2b9-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> stellt in Protokollierungsmeldungen personenbezogene Informationen (PII) bereit.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-258"><xref:Microsoft.IdentityModel.Logging.IdentityModelEventSource.ShowPII?displayProperty=nameWithType> provides Personally Identifiable Information (PII) in logging messages.</span></span> <span data-ttu-id="1d2b9-259">Aktivieren Sie PII nur für das Debuggen mit Testbenutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-259">Only activate PII for debugging with test user accounts.</span></span>

<span data-ttu-id="1d2b9-260">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-260">In `Startup.ConfigureServices`:</span></span>

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
                }

                if (groupsAndAzureRoles != null)
                {
                    foreach (var entry in groupsAndAzureRoles)
                    {
                        userIdentity.AddClaim(new Claim("group", entry.Id));
                    }
                }
            }

            await Task.FromResult(0);
        }
    };
});
```

<span data-ttu-id="1d2b9-261">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-261">In the preceding example:</span></span>

* <span data-ttu-id="1d2b9-262">Zuerst wird versucht, den automatischen Tokenabruf (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) auszuführen, weil das Zugriffstoken möglicherweise bereits im ADAL-Tokencache gespeichert wurde.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-262">Silent token acquisition (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>) is attempted first because the access token may have already been stored in the ADAL token cache.</span></span> <span data-ttu-id="1d2b9-263">Es ist schneller, das Token aus dem Cache abzurufen, als ein neues Token anzufordern.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-263">It's faster to obtain the token from cache than to request a new token.</span></span>
* <span data-ttu-id="1d2b9-264">Wenn das Zugriffstoken nicht aus dem Cache abgerufen wird (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> oder <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> wird ausgelöst), wird eine Benutzerassertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) mit der Clientanmeldeinformation (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) ausgeführt, um das Token im Namen des Benutzers (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>) abzurufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-264">If the access token isn't acquired from cache (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.FailedToAcquireTokenSilently?displayProperty=nameWithType> or <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AdalError.UserInteractionRequired?displayProperty=nameWithType> is thrown), a user assertion (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.UserAssertion>) is made with the client credential (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential>) to obtain the token on behalf of the user (<xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenAsync%2A>).</span></span> <span data-ttu-id="1d2b9-265">Anschließend kann der `Microsoft.Graph.GraphServiceClient` das Token für den Graph-API-Aufruf verwenden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-265">Next, the `Microsoft.Graph.GraphServiceClient` can proceed to use the token to make the Graph API call.</span></span> <span data-ttu-id="1d2b9-266">Das Token wird im ADAL-Tokencache abgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-266">The token is placed into the ADAL token cache.</span></span> <span data-ttu-id="1d2b9-267">Bei zukünftigen Graph-API-Aufrufen für denselben Benutzer wird das Token automatisch mit <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A> aus dem Cache abgerufen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-267">For future Graph API calls for the same user, the token is acquired from cache silently with <xref:Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext.AcquireTokenSilentAsync%2A>.</span></span>

::: moniker-end

<span data-ttu-id="1d2b9-268">Der Code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> erhält keine transitiven Mitgliedschaften.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-268">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't obtain transitive memberships.</span></span> <span data-ttu-id="1d2b9-269">So ändern Sie den Code, um direkte und transitive Mitgliedschaften zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-269">To change the code to obtain direct and transitive memberships:</span></span>

* <span data-ttu-id="1d2b9-270">Für die Codezeile:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-270">For the code line:</span></span>

  ```csharp
  IUserMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

  <span data-ttu-id="1d2b9-271">Ersetzen Sie die vorherige Zeile durch:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-271">Replace the preceding line with:</span></span>

  ```csharp
  IUserTransitiveMemberOfCollectionWithReferencesPage groupsAndAzureRoles = null;
  ```

* <span data-ttu-id="1d2b9-272">Für die Codezeile:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-272">For the code line:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].MemberOf.Request().GetAsync();
  ```

  <span data-ttu-id="1d2b9-273">Ersetzen Sie die vorherige Zeile durch:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-273">Replace the preceding line with:</span></span>

  ```csharp
  groupsAndAzureRoles = await graphClient.Users[oid].TransitiveMemberOf.Request()
      .GetAsync();
  ```

<span data-ttu-id="1d2b9-274">Beim Erstellen von Ansprüchen unterscheidet der Code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> nicht zwischen AAD-Sicherheitsgruppen und AAD-Administratorrollen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-274">The code in <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerEvents.OnTokenValidated> doesn't distinguish between AAD security groups and AAD Administrator Roles when creating claims.</span></span> <span data-ttu-id="1d2b9-275">Damit die App zwischen Gruppen und Rollen unterscheidet, überprüfen Sie beim Durchlaufen der Gruppen und Rollen den `entry.ODataType`.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-275">For the app to distinguish between groups and roles, check the `entry.ODataType` when iterating through the groups and roles.</span></span> <span data-ttu-id="1d2b9-276">Um separate Sicherheitsgruppen- und Rollenansprüche zu erstellen, verwenden Sie in etwa folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-276">To create separate security group and role claims, use code similar to the following:</span></span>

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

## <a name="user-defined-roles"></a><span data-ttu-id="1d2b9-277">Benutzerdefinierte Rollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-277">User-defined roles</span></span>

<span data-ttu-id="1d2b9-278">In AAD registrierte Apps können auch für die Verwendung von benutzerdefinierten Rollen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-278">An AAD-registered app can also be configured to use user-defined roles.</span></span>

<span data-ttu-id="1d2b9-279">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines `roles`-Mitgliedschaftsanspruchs finden Sie in der Azure-Dokumentation unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-279">To configure the app in the Azure portal to provide a `roles` membership claim, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="1d2b9-280">Im folgenden Beispiel wird davon ausgegangen, dass eine App mit zwei Rollen konfiguriert wurde:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-280">The following example assumes that an app is configured with two roles:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="1d2b9-281">Zwar können Sie Rollen nur mit einem Azure AD Premium-Konto Sicherheitsgruppen zuweisen, allerdings können Sie mit einem Azure-Standardkonto Rollen Benutzer zuweisen und einen `roles`-Anspruch für Benutzer erhalten.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-281">Although you can't assign roles to security groups without an Azure AD Premium account, you can assign users to roles and receive a `roles` claim for users with a standard Azure account.</span></span> <span data-ttu-id="1d2b9-282">Für die Schritte in diesem Abschnitt ist kein Azure AD Premium-Konto erforderlich.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-282">The guidance in this section doesn't require an Azure AD Premium account.</span></span>
>
> <span data-ttu-id="1d2b9-283">Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-283">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="1d2b9-284">Der von AAD gesendete einzelne `roles`-Anspruch zeigt die benutzerdefinierten Rollen jeweils als `value` für `appRoles` in einem JSON-Array an.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-284">The single `roles` claim sent by AAD presents the user-defined roles as the `appRoles`'s `value`s in a JSON array.</span></span> <span data-ttu-id="1d2b9-285">Die App muss das JSON-Array mit Rollen in einzelne `role`-Ansprüche konvertieren.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-285">The app must convert the JSON array of roles into individual `role` claims.</span></span>

<span data-ttu-id="1d2b9-286">Die im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles) gezeigte `CustomUserFactory` ist so eingerichtet, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-286">The `CustomUserFactory` shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="1d2b9-287">Fügen Sie die `CustomUserFactory` wie im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles) beschrieben in der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung hinzu, und registrieren Sie sie dort.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-287">Add and register the `CustomUserFactory` in the standalone app or *`Client`* app of a hosted Blazor solution as shown in the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span> <span data-ttu-id="1d2b9-288">Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-288">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="1d2b9-289">Geben Sie in `Program.Main` der eigenständigen oder *`Client`* -App einer gehosteten Blazor-Lösung den Anspruch mit dem Namen „`role`“ als Rollenanspruch an:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-289">In `Program.Main` of the standalone app or *`Client`* app of a hosted Blazor solution, specify the claim named "`role`" as the role claim:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "role";
});
```

<span data-ttu-id="1d2b9-290">Die Komponentenautorisierungsansätze sind Stand jetzt funktional.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-290">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="1d2b9-291">Jeder der Autorisierungsmechanismen bei den Komponenten kann die Rolle `admin` verwenden, um den Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-291">Any of the authorization mechanisms in components can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="1d2b9-292">[`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) (Beispiel: `<AuthorizeView Roles="admin">`)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-292">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="1d2b9-293">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Beispiel: `@attribute [Authorize(Roles = "admin")]`)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-293">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="1d2b9-294">[Prozedurale Logik](xref:blazor/security/index#procedural-logic) (Beispiel: `if (user.IsInRole("admin")) { ... }`)</span><span class="sxs-lookup"><span data-stu-id="1d2b9-294">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="1d2b9-295">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="1d2b9-295">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

## <a name="aad-administrator-role-object-ids"></a><span data-ttu-id="1d2b9-296">Objekt-IDs für AAD-Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-296">AAD Administrator Role Object IDs</span></span>

<span data-ttu-id="1d2b9-297">Die in der folgenden Tabelle aufgeführten Objekt-IDs werden zum Erstellen von [Richtlinien](xref:security/authorization/policies) für `group`-Ansprüche verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-297">The Object IDs presented in the following table are used to create [policies](xref:security/authorization/policies) for `group` claims.</span></span> <span data-ttu-id="1d2b9-298">Richtlinien ermöglichen es einer App, Benutzer für verschiedene Aktivitäten in einer App zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="1d2b9-298">Policies permit an app to authorize users for various activities in an app.</span></span> <span data-ttu-id="1d2b9-299">Weitere Informationen finden Sie im Abschnitt [Benutzerdefinierte Gruppen und AAD-Administratorrollen](#user-defined-groups-and-administrator-roles).</span><span class="sxs-lookup"><span data-stu-id="1d2b9-299">For more information, see the [User-defined groups and AAD Administrator Roles](#user-defined-groups-and-administrator-roles) section.</span></span>

<span data-ttu-id="1d2b9-300">AAD-Administratorrolle</span><span class="sxs-lookup"><span data-stu-id="1d2b9-300">AAD Administrator Role</span></span> | <span data-ttu-id="1d2b9-301">ObjectID</span><span class="sxs-lookup"><span data-stu-id="1d2b9-301">Object ID</span></span>
--- | ---
<span data-ttu-id="1d2b9-302">Anwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-302">Application administrator</span></span> | <span data-ttu-id="1d2b9-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span><span class="sxs-lookup"><span data-stu-id="1d2b9-303">fa11557b-4f15-4ddd-85d5-313c7cd74047</span></span>
<span data-ttu-id="1d2b9-304">Anwendungsentwickler</span><span class="sxs-lookup"><span data-stu-id="1d2b9-304">Application developer</span></span> | <span data-ttu-id="1d2b9-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span><span class="sxs-lookup"><span data-stu-id="1d2b9-305">68adcbb8-9504-44f6-89f2-5cd48dc74a2c</span></span>
<span data-ttu-id="1d2b9-306">Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-306">Authentication administrator</span></span> | <span data-ttu-id="1d2b9-307">02d110a1-96b1-419e-af87-746461b60ed7</span><span class="sxs-lookup"><span data-stu-id="1d2b9-307">02d110a1-96b1-419e-af87-746461b60ed7</span></span>
<span data-ttu-id="1d2b9-308">Azure DevOps-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-308">Azure DevOps administrator</span></span> | <span data-ttu-id="1d2b9-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span><span class="sxs-lookup"><span data-stu-id="1d2b9-309">a5311ace-ca41-44cd-b833-8d22caa0b34f</span></span>
<span data-ttu-id="1d2b9-310">Azure Information Protection-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-310">Azure Information Protection administrator</span></span> | <span data-ttu-id="1d2b9-311">18632dce-f9b5-4f01-abb5-37051f06860e</span><span class="sxs-lookup"><span data-stu-id="1d2b9-311">18632dce-f9b5-4f01-abb5-37051f06860e</span></span>
<span data-ttu-id="1d2b9-312">B2C-IEF-Schlüsselsatzadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-312">B2C IEF Keyset administrator</span></span> | <span data-ttu-id="1d2b9-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span><span class="sxs-lookup"><span data-stu-id="1d2b9-313">0c2e87e5-94f9-4adb-ae8c-bcafe11bd368</span></span>
<span data-ttu-id="1d2b9-314">B2C-IEF-Richtlinienadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-314">B2C IEF Policy administrator</span></span> | <span data-ttu-id="1d2b9-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span><span class="sxs-lookup"><span data-stu-id="1d2b9-315">bfcab36c-10c6-4b13-b63c-4d8b62c0c44e</span></span>
<span data-ttu-id="1d2b9-316">B2C-Benutzerflowadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-316">B2C user flow administrator</span></span> | <span data-ttu-id="1d2b9-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span><span class="sxs-lookup"><span data-stu-id="1d2b9-317">baa531b7-8cf0-44ad-8f98-eded88dae827</span></span>
<span data-ttu-id="1d2b9-318">B2C-Administrator für Benutzerflowattribute</span><span class="sxs-lookup"><span data-stu-id="1d2b9-318">B2C user flow attribute administrator</span></span> | <span data-ttu-id="1d2b9-319">dd0baca0-a535-48c1-b871-8431abe16452</span><span class="sxs-lookup"><span data-stu-id="1d2b9-319">dd0baca0-a535-48c1-b871-8431abe16452</span></span>
<span data-ttu-id="1d2b9-320">Rechnungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-320">Billing administrator</span></span> | <span data-ttu-id="1d2b9-321">69ff516a-b57d-4697-a429-9de4af7b5609</span><span class="sxs-lookup"><span data-stu-id="1d2b9-321">69ff516a-b57d-4697-a429-9de4af7b5609</span></span>
<span data-ttu-id="1d2b9-322">Cloudanwendungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-322">Cloud application administrator</span></span> | <span data-ttu-id="1d2b9-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span><span class="sxs-lookup"><span data-stu-id="1d2b9-323">250b5fe3-b553-458d-9a53-b782c13c34bf</span></span>
<span data-ttu-id="1d2b9-324">Cloudgeräteadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-324">Cloud device administrator</span></span> | <span data-ttu-id="1d2b9-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span><span class="sxs-lookup"><span data-stu-id="1d2b9-325">26cd4b44-2636-4ddb-bdfa-27feae66f86d</span></span>
<span data-ttu-id="1d2b9-326">Complianceadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-326">Compliance administrator</span></span> | <span data-ttu-id="1d2b9-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span><span class="sxs-lookup"><span data-stu-id="1d2b9-327">9d6e1dd0-c9f8-45f8-b558-b134f700116c</span></span>
<span data-ttu-id="1d2b9-328">Compliancedatenadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-328">Compliance data administrator</span></span> | <span data-ttu-id="1d2b9-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span><span class="sxs-lookup"><span data-stu-id="1d2b9-329">4c0ca3a2-231e-416c-9411-4abe57d5cb9d</span></span>
<span data-ttu-id="1d2b9-330">Administrator für den bedingten Zugriff</span><span class="sxs-lookup"><span data-stu-id="1d2b9-330">Conditional Access administrator</span></span> | <span data-ttu-id="1d2b9-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span><span class="sxs-lookup"><span data-stu-id="1d2b9-331">8f71a611-137d-49af-87ad-e97f1fd5da76</span></span>
<span data-ttu-id="1d2b9-332">Genehmigende Person für den LockBox-Kundenzugriff</span><span class="sxs-lookup"><span data-stu-id="1d2b9-332">Customer LockBox access approver</span></span> | <span data-ttu-id="1d2b9-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span><span class="sxs-lookup"><span data-stu-id="1d2b9-333">c18d54a8-b13e-4954-a1a4-7deaf2e4f184</span></span>
<span data-ttu-id="1d2b9-334">Desktop Analytics-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-334">Desktop Analytics administrator</span></span> | <span data-ttu-id="1d2b9-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span><span class="sxs-lookup"><span data-stu-id="1d2b9-335">c62c4ac5-e4c6-4096-8a2f-1ee3cbaaae15</span></span>
<span data-ttu-id="1d2b9-336">Rolle „Verzeichnis lesen“</span><span class="sxs-lookup"><span data-stu-id="1d2b9-336">Directory readers</span></span> | <span data-ttu-id="1d2b9-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span><span class="sxs-lookup"><span data-stu-id="1d2b9-337">e1fc84a6-7762-4b9b-8e29-518b4adbc23b</span></span>
<span data-ttu-id="1d2b9-338">Dynamics 365-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-338">Dynamics 365 administrator</span></span> | <span data-ttu-id="1d2b9-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span><span class="sxs-lookup"><span data-stu-id="1d2b9-339">f20a9cfa-9fdf-49a8-a977-1afe446a1d6e</span></span>
<span data-ttu-id="1d2b9-340">Exchange-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-340">Exchange administrator</span></span> | <span data-ttu-id="1d2b9-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span><span class="sxs-lookup"><span data-stu-id="1d2b9-341">b2ec2cc0-d5c9-4864-ad9b-38dd9dba2652</span></span>
<span data-ttu-id="1d2b9-342">Externer Identitätsanbieteradministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-342">External Identity Provider administrator</span></span> | <span data-ttu-id="1d2b9-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span><span class="sxs-lookup"><span data-stu-id="1d2b9-343">febfaeb4-e478-407a-b4b3-f4d9716618a2</span></span>
<span data-ttu-id="1d2b9-344">Globaler Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-344">Global administrator</span></span> | <span data-ttu-id="1d2b9-345">a45ba61b-44db-462c-924b-3b2719152588</span><span class="sxs-lookup"><span data-stu-id="1d2b9-345">a45ba61b-44db-462c-924b-3b2719152588</span></span>
<span data-ttu-id="1d2b9-346">Globaler Leser</span><span class="sxs-lookup"><span data-stu-id="1d2b9-346">Global reader</span></span> | <span data-ttu-id="1d2b9-347">f6903b21-6aba-4124-b44c-76671796b9d5</span><span class="sxs-lookup"><span data-stu-id="1d2b9-347">f6903b21-6aba-4124-b44c-76671796b9d5</span></span>
<span data-ttu-id="1d2b9-348">Gruppenadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-348">Groups administrator</span></span> | <span data-ttu-id="1d2b9-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span><span class="sxs-lookup"><span data-stu-id="1d2b9-349">158b3e5a-d89d-460b-92b5-3b34985f0197</span></span>
<span data-ttu-id="1d2b9-350">Gasteinladender</span><span class="sxs-lookup"><span data-stu-id="1d2b9-350">Guest inviter</span></span> | <span data-ttu-id="1d2b9-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span><span class="sxs-lookup"><span data-stu-id="1d2b9-351">4c730a1d-cc22-44af-8f9f-4eec635c7502</span></span>
<span data-ttu-id="1d2b9-352">Helpdesk-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-352">Helpdesk administrator</span></span> | <span data-ttu-id="1d2b9-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span><span class="sxs-lookup"><span data-stu-id="1d2b9-353">108678c8-6628-44e1-8d01-caf598a6a5f5</span></span>
<span data-ttu-id="1d2b9-354">Intune-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-354">Intune administrator</span></span> | <span data-ttu-id="1d2b9-355">79950741-23fa-4189-b2cb-46640601c497</span><span class="sxs-lookup"><span data-stu-id="1d2b9-355">79950741-23fa-4189-b2cb-46640601c497</span></span>
<span data-ttu-id="1d2b9-356">Kaizala-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-356">Kaizala administrator</span></span> | <span data-ttu-id="1d2b9-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span><span class="sxs-lookup"><span data-stu-id="1d2b9-357">d6322af2-48e7-42e0-8c68-0bbe31af3412</span></span>
<span data-ttu-id="1d2b9-358">Lizenzadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-358">License administrator</span></span> | <span data-ttu-id="1d2b9-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span><span class="sxs-lookup"><span data-stu-id="1d2b9-359">3355458a-e423-44bf-8b98-4ac5e572cea5</span></span>
<span data-ttu-id="1d2b9-360">Nachrichtencenter-Datenschutzleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="1d2b9-360">Message center privacy reader</span></span> | <span data-ttu-id="1d2b9-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span><span class="sxs-lookup"><span data-stu-id="1d2b9-361">6395db95-9fb8-42b9-b1ed-30a2405eee6f</span></span>
<span data-ttu-id="1d2b9-362">Nachrichtencenter-Leser</span><span class="sxs-lookup"><span data-stu-id="1d2b9-362">Message center reader</span></span> | <span data-ttu-id="1d2b9-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span><span class="sxs-lookup"><span data-stu-id="1d2b9-363">fd5d37b8-4e24-434b-9e63-70ed3b759a16</span></span>
<span data-ttu-id="1d2b9-364">Office-Apps-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-364">Office apps administrator</span></span> | <span data-ttu-id="1d2b9-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span><span class="sxs-lookup"><span data-stu-id="1d2b9-365">5f3870cd-b042-4f93-86d7-c9d77c664dc7</span></span>
<span data-ttu-id="1d2b9-366">Kennwortadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-366">Password administrator</span></span> | <span data-ttu-id="1d2b9-367">466e48b7-5d66-4ae5-8911-1a118de74941</span><span class="sxs-lookup"><span data-stu-id="1d2b9-367">466e48b7-5d66-4ae5-8911-1a118de74941</span></span>
<span data-ttu-id="1d2b9-368">Power BI-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-368">Power BI administrator</span></span> | <span data-ttu-id="1d2b9-369">984e83b8-8337-4255-91a1-acb663175ab4</span><span class="sxs-lookup"><span data-stu-id="1d2b9-369">984e83b8-8337-4255-91a1-acb663175ab4</span></span>
<span data-ttu-id="1d2b9-370">Power Platform-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-370">Power platform administrator</span></span> | <span data-ttu-id="1d2b9-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span><span class="sxs-lookup"><span data-stu-id="1d2b9-371">76d6f95e-9a15-4d7d-8d21-00de00faf9fd</span></span>
<span data-ttu-id="1d2b9-372">Privilegierter Authentifizierungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-372">Privileged authentication administrator</span></span> | <span data-ttu-id="1d2b9-373">0829f731-b46d-419f-9742-aeb122367d11</span><span class="sxs-lookup"><span data-stu-id="1d2b9-373">0829f731-b46d-419f-9742-aeb122367d11</span></span>
<span data-ttu-id="1d2b9-374">Administrator für privilegierte Rollen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-374">Privileged role administrator</span></span> | <span data-ttu-id="1d2b9-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span><span class="sxs-lookup"><span data-stu-id="1d2b9-375">f20a725a-d1c8-4107-83ea-1171c97d00c7</span></span>
<span data-ttu-id="1d2b9-376">Berichtsleser</span><span class="sxs-lookup"><span data-stu-id="1d2b9-376">Reports reader</span></span> | <span data-ttu-id="1d2b9-377">54635450-e8ed-4f2d-9632-07db2517b4de</span><span class="sxs-lookup"><span data-stu-id="1d2b9-377">54635450-e8ed-4f2d-9632-07db2517b4de</span></span>
<span data-ttu-id="1d2b9-378">Suchadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-378">Search administrator</span></span> | <span data-ttu-id="1d2b9-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span><span class="sxs-lookup"><span data-stu-id="1d2b9-379">c770a2f1-c9ba-4e60-9176-9f52b1eb1a31</span></span>
<span data-ttu-id="1d2b9-380">Such-Editor</span><span class="sxs-lookup"><span data-stu-id="1d2b9-380">Search editor</span></span> | <span data-ttu-id="1d2b9-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span><span class="sxs-lookup"><span data-stu-id="1d2b9-381">6a6858c6-5f0d-44ac-87c7-0190fbedd271</span></span>
<span data-ttu-id="1d2b9-382">Sicherheitsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-382">Security administrator</span></span> | <span data-ttu-id="1d2b9-383">20fa50e3-6531-44d8-bd39-b251420568ad</span><span class="sxs-lookup"><span data-stu-id="1d2b9-383">20fa50e3-6531-44d8-bd39-b251420568ad</span></span>
<span data-ttu-id="1d2b9-384">Sicherheitsoperator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-384">Security operator</span></span> | <span data-ttu-id="1d2b9-385">43aae017-8e51-4188-91ab-e6debd572800</span><span class="sxs-lookup"><span data-stu-id="1d2b9-385">43aae017-8e51-4188-91ab-e6debd572800</span></span>
<span data-ttu-id="1d2b9-386">Sicherheitsleseberechtigter</span><span class="sxs-lookup"><span data-stu-id="1d2b9-386">Security reader</span></span> | <span data-ttu-id="1d2b9-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span><span class="sxs-lookup"><span data-stu-id="1d2b9-387">45035cd3-fd97-4250-8197-3a53d3562d9b</span></span>
<span data-ttu-id="1d2b9-388">Dienstunterstützungsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-388">Service support administrator</span></span> | <span data-ttu-id="1d2b9-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span><span class="sxs-lookup"><span data-stu-id="1d2b9-389">2c92cf45-c914-48f8-9bf9-fc14b28818ab</span></span>
<span data-ttu-id="1d2b9-390">SharePoint-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-390">SharePoint administrator</span></span> | <span data-ttu-id="1d2b9-391">e1c32229-875e-461d-ae24-3cb99116e86c</span><span class="sxs-lookup"><span data-stu-id="1d2b9-391">e1c32229-875e-461d-ae24-3cb99116e86c</span></span>
<span data-ttu-id="1d2b9-392">Skype for Business-Administrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-392">Skype for Business administrator</span></span> | <span data-ttu-id="1d2b9-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span><span class="sxs-lookup"><span data-stu-id="1d2b9-393">0a8cee12-e21d-43ef-abd9-f1ea85710e30</span></span>
<span data-ttu-id="1d2b9-394">Teams-Kommunikationsadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-394">Teams Communications Administrator</span></span> | <span data-ttu-id="1d2b9-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span><span class="sxs-lookup"><span data-stu-id="1d2b9-395">2393e455-6e13-4743-9f52-63fcec2b6a9c</span></span>
<span data-ttu-id="1d2b9-396">Teams-Kommunikationssupporttechniker</span><span class="sxs-lookup"><span data-stu-id="1d2b9-396">Teams Communications Support Engineer</span></span> | <span data-ttu-id="1d2b9-397">802dd94e-d717-46f6-af98-b9167071e9fc</span><span class="sxs-lookup"><span data-stu-id="1d2b9-397">802dd94e-d717-46f6-af98-b9167071e9fc</span></span>
<span data-ttu-id="1d2b9-398">Supportfachmann für die Teams-Kommunikation</span><span class="sxs-lookup"><span data-stu-id="1d2b9-398">Teams Communications Specialist</span></span> | <span data-ttu-id="1d2b9-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span><span class="sxs-lookup"><span data-stu-id="1d2b9-399">ef547281-cf46-4cc6-bcaa-f5eac3f030c9</span></span>
<span data-ttu-id="1d2b9-400">Teams-Dienstadministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-400">Teams Service Administrator</span></span> | <span data-ttu-id="1d2b9-401">8846a0be-197b-443a-b13c-11192691fa24</span><span class="sxs-lookup"><span data-stu-id="1d2b9-401">8846a0be-197b-443a-b13c-11192691fa24</span></span>
<span data-ttu-id="1d2b9-402">Benutzeradministrator</span><span class="sxs-lookup"><span data-stu-id="1d2b9-402">User administrator</span></span> | <span data-ttu-id="1d2b9-403">1f6eed58-7dd3-460b-a298-666f975427a1</span><span class="sxs-lookup"><span data-stu-id="1d2b9-403">1f6eed58-7dd3-460b-a298-666f975427a1</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d2b9-404">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d2b9-404">Additional resources</span></span>

* <xref:security/authorization/claims>
* <xref:blazor/security/index>
