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
# <a name="azure-active-directory-aad-groups-administrator-roles-and-app-roles"></a><span data-ttu-id="6ece9-103">Gruppen, Administratorrollen und App-Rollen in Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="6ece9-103">Azure Active Directory (AAD) groups, Administrator Roles, and App Roles</span></span>

<span data-ttu-id="6ece9-104">Azure Active Directory (AAD) bietet mehrere Autorisierungsansätze, die mit ASP.NET Core Identity kombiniert werden können:</span><span class="sxs-lookup"><span data-stu-id="6ece9-104">Azure Active Directory (AAD) provides several authorization approaches that can be combined with ASP.NET Core Identity:</span></span>

* <span data-ttu-id="6ece9-105">Gruppen</span><span class="sxs-lookup"><span data-stu-id="6ece9-105">Groups</span></span>
  * <span data-ttu-id="6ece9-106">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="6ece9-106">Security</span></span>
  * <span data-ttu-id="6ece9-107">Microsoft 365</span><span class="sxs-lookup"><span data-stu-id="6ece9-107">Microsoft 365</span></span>
  * <span data-ttu-id="6ece9-108">Verteilung</span><span class="sxs-lookup"><span data-stu-id="6ece9-108">Distribution</span></span>
* <span data-ttu-id="6ece9-109">Rollen</span><span class="sxs-lookup"><span data-stu-id="6ece9-109">Roles</span></span>
  * <span data-ttu-id="6ece9-110">AAD-Administratorrollen</span><span class="sxs-lookup"><span data-stu-id="6ece9-110">AAD Administrator Roles</span></span>
  * <span data-ttu-id="6ece9-111">App-Rollen</span><span class="sxs-lookup"><span data-stu-id="6ece9-111">App Roles</span></span>

<span data-ttu-id="6ece9-112">Die Informationen in diesem Artikel beziehen sich auf die in den folgenden Artikeln beschriebenen Bereitstellungsszenarios für Blazor WebAssembly-AAD:</span><span class="sxs-lookup"><span data-stu-id="6ece9-112">The guidance in this article applies to the Blazor WebAssembly AAD deployment scenarios described in the following topics:</span></span>

* [<span data-ttu-id="6ece9-113">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Microsoft-Konten</span><span class="sxs-lookup"><span data-stu-id="6ece9-113">Standalone with Microsoft Accounts</span></span>](xref:blazor/security/webassembly/standalone-with-microsoft-accounts)
* [<span data-ttu-id="6ece9-114">Sichern einer eigenständigen ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6ece9-114">Standalone with AAD</span></span>](xref:blazor/security/webassembly/standalone-with-azure-active-directory)
* [<span data-ttu-id="6ece9-115">Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="6ece9-115">Hosted with AAD</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)

<span data-ttu-id="6ece9-116">Dieser Artikel enthält Anleitungen für Client- und Server-Apps:</span><span class="sxs-lookup"><span data-stu-id="6ece9-116">The article's guidance provides instructions for client and server apps:</span></span>

* <span data-ttu-id="6ece9-117">**CLIENT**: Eigenständig Blazor WebAssembly-Apps oder die *`Client`* -App einer gehosteten Blazor-Lösung.</span><span class="sxs-lookup"><span data-stu-id="6ece9-117">**CLIENT**: Standalone Blazor WebAssembly apps or the *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="6ece9-118">**SERVER**: Eigenständige ASP.NET Core-Server-API-/Web API-Apps oder die *`Server`* -App einer gehosteten Blazor-Lösung.</span><span class="sxs-lookup"><span data-stu-id="6ece9-118">**SERVER**: Standalone ASP.NET Core server API/web API apps or the *`Server`* app of a hosted Blazor solution.</span></span>

## <a name="scopes"></a><span data-ttu-id="6ece9-119">Bereiche</span><span class="sxs-lookup"><span data-stu-id="6ece9-119">Scopes</span></span>

<span data-ttu-id="6ece9-120">Damit die [Microsoft Graph-API](/graph/use-the-api) Daten zu Benutzerprofil, Rollenzuweisung und Gruppenmitgliedschaft abrufen kann, ist der **CLIENT** mit (`https://graph.microsoft.com/User.Read`) [Graph-API-Berechtigung (Bereich)](/graph/permissions-reference) im Azure-Portal konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6ece9-120">To permit [Microsoft Graph API](/graph/use-the-api) calls for user profile, role assignment, and group membership data, the **CLIENT** is configured with (`https://graph.microsoft.com/User.Read`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="6ece9-121">Einer **SERVER**-App, die die Graph-API aufruft, um Rollen- und Gruppenmitgliedschaftsdaten zu erhalten, wird die `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph-API-Berechtigung (Bereich)](/graph/permissions-reference) im Azure-Portal gewährt.</span><span class="sxs-lookup"><span data-stu-id="6ece9-121">A **SERVER** app that calls Graph API for role and group membership data is provided `GroupMember.Read.All` (`https://graph.microsoft.com/GroupMember.Read.All`) [Graph API permission (scope)](/graph/permissions-reference) in the Azure portal.</span></span>

<span data-ttu-id="6ece9-122">Diese Bereiche werden zusätzlich zu den in AAD-Bereitstellungsszenarien erforderlichen Bereichen benötigt, die in den im ersten Abschnitt dieses Artikels aufgeführten Themen beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="6ece9-122">These scopes are required in addition to the scopes required in AAD deployment scenarios described by the topics listed in the first section of this article.</span></span>

> [!NOTE]
> <span data-ttu-id="6ece9-123">Die Wörter „Berechtigung“ und „Bereich“ werden im Azure-Portal und in verschiedenen Dokumentationsmaterialien von Microsoft und externen Anbietern austauschbar verwendet.</span><span class="sxs-lookup"><span data-stu-id="6ece9-123">The words "permission" and "scope" are used interchangeably in the Azure portal and in various Microsoft and external documentation sets.</span></span> <span data-ttu-id="6ece9-124">Im vorliegenden Artikel wird „Bereich“ durchgehend für die Berechtigungen verwendet, die einer App im Azure-Portal zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="6ece9-124">This article uses the word "scope" throughout for the permissions assigned to an app in the Azure portal.</span></span>

## <a name="group-membership-claims-attribute"></a><span data-ttu-id="6ece9-125">groupMembershipClaims-Attribut</span><span class="sxs-lookup"><span data-stu-id="6ece9-125">Group Membership Claims attribute</span></span>

<span data-ttu-id="6ece9-126">Legen Sie im Azure-Portal im App-Manifest für **CLIENT**- und **SERVER**-Apps das [`groupMembershipClaims`-Attribut](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) auf `All` fest.</span><span class="sxs-lookup"><span data-stu-id="6ece9-126">In the app's manifest in the the Azure portal for **CLIENT** and **SERVER** apps, set the [`groupMembershipClaims` attribute](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute) to `All`.</span></span> <span data-ttu-id="6ece9-127">Der Wert `All` führt dazu, dass sämtliche Sicherheitsgruppen, Verteilergruppe und Rollen abgerufen werden, in denen der angemeldete Benutzer Mitglied ist.</span><span class="sxs-lookup"><span data-stu-id="6ece9-127">A value of `All` results in obtaining all of the security groups, distribution groups, and roles that the signed-in user is a member of.</span></span>

1. <span data-ttu-id="6ece9-128">Öffnen Sie die Azure-Portal-Registrierung der App.</span><span class="sxs-lookup"><span data-stu-id="6ece9-128">Open the app's Azure portal registration.</span></span>
1. <span data-ttu-id="6ece9-129">Wählen Sie in der Randleiste **Verwalten** > **Manifest** aus.</span><span class="sxs-lookup"><span data-stu-id="6ece9-129">Select **Manage** > **Manifest** in the sidebar.</span></span>
1. <span data-ttu-id="6ece9-130">Suchen Sie das `groupMembershipClaims`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="6ece9-130">Find the `groupMembershipClaims` attribute.</span></span>
1. <span data-ttu-id="6ece9-131">Legen Sie den Wert auf `All` fest.</span><span class="sxs-lookup"><span data-stu-id="6ece9-131">Set the value to `All`.</span></span>
1. <span data-ttu-id="6ece9-132">Wählen Sie die Schaltfläche **Speichern** aus.</span><span class="sxs-lookup"><span data-stu-id="6ece9-132">Select the **Save** button.</span></span>

```json
"groupMembershipClaims": "All",
```

## <a name="custom-user-account"></a><span data-ttu-id="6ece9-133">Benutzerdefiniertes Benutzerkonto</span><span class="sxs-lookup"><span data-stu-id="6ece9-133">Custom user account</span></span>

<span data-ttu-id="6ece9-134">Weisen Sie Benutzer im Azure-Portal zu AAD-Sicherheitsgruppen und AAD-Administratorrollen zu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-134">Assign users to AAD security groups and AAD Administrator Roles in the Azure portal.</span></span>

<span data-ttu-id="6ece9-135">Für die Beispiele in diesem Artikel gilt:</span><span class="sxs-lookup"><span data-stu-id="6ece9-135">The examples in this article:</span></span>

* <span data-ttu-id="6ece9-136">Es wird angenommen, dass einem Benutzer im AAD-Mandanten im Azure-Portal die AAD-Rolle *Abrechnungsadministrator* zugewiesen ist, damit dieser Benutzer für den Zugriff auf die Zugriffsserver-API-Daten autorisiert ist.</span><span class="sxs-lookup"><span data-stu-id="6ece9-136">Assume that a user is assigned to the AAD *Billing Administrator* role in the Azure portal AAD tenant for authorization to access server API data.</span></span>
* <span data-ttu-id="6ece9-137">Verwenden Sie [Autorisierungsrichtlinien](xref:security/authorization/policies), um den Zugriff innerhalb der **CLIENT**- und **SERVER**-Apps zu steuern.</span><span class="sxs-lookup"><span data-stu-id="6ece9-137">Use [authorization policies](xref:security/authorization/policies) to control access within the **CLIENT** and **SERVER** apps.</span></span>

<span data-ttu-id="6ece9-138">Erweitern Sie <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in der **CLIENT**-App, um Eigenschaften für Folgendes einzuschließen:</span><span class="sxs-lookup"><span data-stu-id="6ece9-138">In the **CLIENT** app, extend <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> to include properties for:</span></span>

* <span data-ttu-id="6ece9-139">`Roles`: Array aus AAD-App-Rollen (beschrieben im Abschnitt [App-Rollen](#app-roles))</span><span class="sxs-lookup"><span data-stu-id="6ece9-139">`Roles`: AAD App Roles array (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="6ece9-140">`Wids`: AAD-Administratorrollen in [`wids`-Ansprüchen (Ansprüche bekannter IDs)](/azure/active-directory/develop/access-tokens#payload-claims)</span><span class="sxs-lookup"><span data-stu-id="6ece9-140">`Wids`: AAD Administrator Roles in [well-known IDs claims (`wids`)](/azure/active-directory/develop/access-tokens#payload-claims)</span></span>
* <span data-ttu-id="6ece9-141">`Oid`: Unveränderlicher [`oid`-Anspruch (Objektbezeichneranspruch)](/azure/active-directory/develop/id-tokens#payload-claims) (identifiziert einen Benutzer mandantenintern und mandantenübergreifend eindeutig)</span><span class="sxs-lookup"><span data-stu-id="6ece9-141">`Oid`: Immutable [object identifier claim (`oid`)](/azure/active-directory/develop/id-tokens#payload-claims) (uniquely identifies a user within and across tenants)</span></span>

<span data-ttu-id="6ece9-142">Weisen Sie jeder Arrayeigenschaft ein leeres Array zu, damit die Überprüfung auf `null` nicht erforderlich ist, wenn diese Eigenschaften in `foreach`-Schleifen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ece9-142">Assign an empty array to each array property so that checking for `null` isn't required when these properties are used in `foreach` loops.</span></span>

<span data-ttu-id="6ece9-143">`CustomUserAccount.cs`:</span><span class="sxs-lookup"><span data-stu-id="6ece9-143">`CustomUserAccount.cs`:</span></span>

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

<span data-ttu-id="6ece9-144">Fügen Sie einen Paketverweis auf die Projektdatei der **CLIENT**-App für [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph) hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-144">Add a package reference to the **CLIENT** app's project file for [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph).</span></span>

<span data-ttu-id="6ece9-145">Fügen Sie die Hilfsprogrammklassen und die Konfiguration des Graph SDK im Abschnitt *Graph SDK* des Artikels <xref:blazor/security/webassembly/graph-api#graph-sdk> hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-145">Add the Graph SDK utility classes and configuration in the *Graph SDK* section of the <xref:blazor/security/webassembly/graph-api#graph-sdk> article.</span></span> <span data-ttu-id="6ece9-146">Geben Sie in der `GraphClientExtensions`-Klasse den Bereich `User.Read` für das Zugriffstoken in der `AuthenticateRequestAsync`-Methode an:</span><span class="sxs-lookup"><span data-stu-id="6ece9-146">In the `GraphClientExtensions` class, specify the `User.Read` scope for the access token in the `AuthenticateRequestAsync` method:</span></span>

```csharp
var result = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions()
    {
        Scopes = new[] { "https://graph.microsoft.com/User.Read" }
    });
```

<span data-ttu-id="6ece9-147">Fügen Sie der **CLIENT**-App die folgende benutzerdefinierte Benutzerkontofactory hinzu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-147">Add the following custom user account factory to the **CLIENT** app.</span></span> <span data-ttu-id="6ece9-148">Die benutzerdefinierte Benutzerfactory wird verwendet, um Folgendes einzurichten:</span><span class="sxs-lookup"><span data-stu-id="6ece9-148">The custom user factory is used to establish:</span></span>

* <span data-ttu-id="6ece9-149">App-Rollenansprüche (`appRole`) (beschrieben im Abschnitt [App-Rollen](#app-roles))</span><span class="sxs-lookup"><span data-stu-id="6ece9-149">App Role claims (`appRole`) (covered in the [App Roles](#app-roles) section)</span></span>
* <span data-ttu-id="6ece9-150">AAD-Administratorrollenansprüche (`directoryRole`)</span><span class="sxs-lookup"><span data-stu-id="6ece9-150">AAD Administrator Role claims (`directoryRole`)</span></span>
* <span data-ttu-id="6ece9-151">Ein Beispiel für einen Anspruch auf Benutzerprofildaten für die Mobiltelefonnummer des Benutzers (`mobilePhone`)</span><span class="sxs-lookup"><span data-stu-id="6ece9-151">An example user profile data claim for the user's mobile phone number (`mobilePhone`)</span></span>
* <span data-ttu-id="6ece9-152">AAD-Gruppenansprüche (`directoryGroup`)</span><span class="sxs-lookup"><span data-stu-id="6ece9-152">AAD Group claims (`directoryGroup`)</span></span>

<span data-ttu-id="6ece9-153">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="6ece9-153">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="6ece9-154">Der vorangehende Code enthält keine transitiven Mitgliedschaften.</span><span class="sxs-lookup"><span data-stu-id="6ece9-154">The preceding code doesn't include transitive memberships.</span></span> <span data-ttu-id="6ece9-155">Wenn die App direkte und transitive Gruppenmitgliedschaftsansprüche erfordert, ersetzen Sie die `MemberOf`-Eigenschaft (`IUserMemberOfCollectionWithReferencesRequestBuilder`) durch `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span><span class="sxs-lookup"><span data-stu-id="6ece9-155">If the app requires direct and transitive group membership claims, replace the `MemberOf` property (`IUserMemberOfCollectionWithReferencesRequestBuilder`) with `TransitiveMemberOf` (`IUserTransitiveMemberOfCollectionWithReferencesRequestBuilder`).</span></span>

<span data-ttu-id="6ece9-156">Der obige Code ignoriert Gruppenmitgliedschaftsansprüche (`groups`), bei denen es sich um AAD-Administratorrollen (Typ `#microsoft.graph.directoryRole`) handelt, weil die von Microsoft Identity Platform 2.0 zurückgegebenen GUID-Werte **Entitäts-IDs** und keine [**Rollenvorlagen-IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids) für AAD-Administratorrollen sind.</span><span class="sxs-lookup"><span data-stu-id="6ece9-156">The preceding code ignores group membership claims (`groups`) that are AAD Administrator Roles (`#microsoft.graph.directoryRole` type) because the GUID values returned by the Microsoft Identity Platform 2.0 are AAD Administrator Role **entity IDs** and not [**Role Template IDs**](/azure/active-directory/roles/permissions-reference#role-template-ids).</span></span> <span data-ttu-id="6ece9-157">Entitäts-IDs bleiben in Microsoft Identity Platform 2.0 nicht mandantenübergreifend erhalten und sollten nicht zum Erstellen von Autorisierungsrichtlinien für Benutzer in Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="6ece9-157">Entity IDs aren't stable across tenants in Microsoft Identity Platform 2.0 and shouldn't be used to create authorization policies for users in apps.</span></span> <span data-ttu-id="6ece9-158">Verwenden Sie immer **Rollenvorlagen-IDs** für **von `wids`-Ansprüchen** bereitgestellte AAD-Administratorrollen.</span><span class="sxs-lookup"><span data-stu-id="6ece9-158">Always use **Role Template IDs** for AAD Administrator Roles **provided by `wids` claims**.</span></span>

<span data-ttu-id="6ece9-159">Konfigurieren Sie in `Program.Main` der **CLIENT**-App die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory.</span><span class="sxs-lookup"><span data-stu-id="6ece9-159">In `Program.Main` of the **CLIENT** app, configure the MSAL authentication to use the custom user account factory.</span></span>

<span data-ttu-id="6ece9-160">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="6ece9-160">`Program.cs`:</span></span>

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

## <a name="authorization-configuration"></a><span data-ttu-id="6ece9-161">Konfigurieren der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="6ece9-161">Authorization configuration</span></span>

<span data-ttu-id="6ece9-162">Erstellen Sie in der **CLIENT**-App eine [Richtlinie](xref:security/authorization/policies) für jede [App-Rolle](#app-roles), AAD-Administratorrolle oder Sicherheitsgruppe in `Program.Main`.</span><span class="sxs-lookup"><span data-stu-id="6ece9-162">In the **CLIENT** app, create a [policy](xref:security/authorization/policies) for each [App Role](#app-roles), AAD Administrator Role, or security group in `Program.Main`.</span></span> <span data-ttu-id="6ece9-163">Im folgenden Beispiel wird eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* erstellt:</span><span class="sxs-lookup"><span data-stu-id="6ece9-163">The following example creates a policy for the AAD *Billing Administrator* role:</span></span>

```csharp
builder.Services.AddAuthorizationCore(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("directoryRole", 
            "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="6ece9-164">Die vollständige Liste der IDs für AAD-Administratorrollen finden Sie in der Azure-Dokumentation unter [Rollenvorlagen-IDs](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="6ece9-164">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="6ece9-165">Weitere Informationen zu Autorisierungsrichtlinien finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6ece9-165">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6ece9-166">In den folgenden Beispielen verwendet die **CLIENT**-App die oben genannte Richtlinie zum Autorisieren des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="6ece9-166">In the following examples, the **CLIENT** app uses the preceding policy to authorize the user.</span></span>

<span data-ttu-id="6ece9-167">Die [`AuthorizeView`-Komponente](xref:blazor/security/index#authorizeview-component) verwendet die Richtlinie:</span><span class="sxs-lookup"><span data-stu-id="6ece9-167">The [`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) works with the policy:</span></span>

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

<span data-ttu-id="6ece9-168">Durch Verwendung einer [`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) kann mithilfe der Richtlinie der Zugriff auf eine gesamte Komponente geregelt werden:</span><span class="sxs-lookup"><span data-stu-id="6ece9-168">Access to an entire component can be based on the policy using an [`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>):</span></span>

```razor
@page "/"
@using Microsoft.AspNetCore.Authorization
@attribute [Authorize(Policy = "BillingAdministrator")]
```

<span data-ttu-id="6ece9-169">Wenn der Benutzer nicht angemeldet ist, wird er auf die AAD-Anmeldeseite umgeleitet und nach der Anmeldung zurück zur entsprechenden Komponente.</span><span class="sxs-lookup"><span data-stu-id="6ece9-169">If the user isn't logged in, they're redirected to the AAD sign-in page and then back to the component after they sign in.</span></span>

<span data-ttu-id="6ece9-170">Eine Richtlinienüberprüfung kann auch [in Code mit prozeduraler Logik durchgeführt werden](xref:blazor/security/index#procedural-logic).</span><span class="sxs-lookup"><span data-stu-id="6ece9-170">A policy check can also be [performed in code with procedural logic](xref:blazor/security/index#procedural-logic).</span></span>

<span data-ttu-id="6ece9-171">`Pages/CheckPolicy.razor`:</span><span class="sxs-lookup"><span data-stu-id="6ece9-171">`Pages/CheckPolicy.razor`:</span></span>

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

## <a name="authorize-server-apiweb-api-access"></a><span data-ttu-id="6ece9-172">Autorisieren des Server-API-/Web-API-Zugriffs</span><span class="sxs-lookup"><span data-stu-id="6ece9-172">Authorize server API/web API access</span></span>

<span data-ttu-id="6ece9-173">Eine **SERVER**-API-App kann Benutzer mit [Autorisierungsrichtlinien](xref:security/authorization/policies) für Sicherheitsgruppen, AAD-Administratorrollen und App-Rollen für den Zugriff auf sichere API-Endpunkte autorisieren, wenn ein Zugriffstoken `groups`-, `wids`- und `http://schemas.microsoft.com/ws/2008/06/identity/claims/role`-Ansprüche enthält.</span><span class="sxs-lookup"><span data-stu-id="6ece9-173">A **SERVER** API app can authorize users to access secure API endpoints with [authorization policies](xref:security/authorization/policies) for security groups, AAD Administrator Roles, and App Roles when an access token contains `groups`, `wids`, and `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` claims.</span></span> <span data-ttu-id="6ece9-174">Das folgende Beispiel erstellt eine Richtlinie für die AAD-Rolle *Abrechnungsadministrator* in `Startup.ConfigureServices` unter Verwendung der `wids`-Ansprüche (bekannte IDs/Rollenvorlagen-IDs):</span><span class="sxs-lookup"><span data-stu-id="6ece9-174">The following example creates a policy for the AAD *Billing Administrator* role in `Startup.ConfigureServices` using the `wids` (well-known IDs/Role Template IDs) claims:</span></span>

```csharp
services.AddAuthorization(options =>
{
    options.AddPolicy("BillingAdministrator", policy => 
        policy.RequireClaim("wids", "b0f54661-2d74-4c50-afa3-1ec803f12efe"));
});
```

<span data-ttu-id="6ece9-175">Die vollständige Liste der IDs für AAD-Administratorrollen finden Sie in der Azure-Dokumentation unter [Rollenvorlagen-IDs](/azure/active-directory/roles/permissions-reference#role-template-ids).</span><span class="sxs-lookup"><span data-stu-id="6ece9-175">For the complete list of IDs for AAD Administrator Roles, see [Role template IDs](/azure/active-directory/roles/permissions-reference#role-template-ids) in the Azure documentation.</span></span> <span data-ttu-id="6ece9-176">Weitere Informationen zu Autorisierungsrichtlinien finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6ece9-176">For more information on authorization policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="6ece9-177">Der Zugriff auf einen Controller in der **SERVER**-App kann auf der Verwendung eines [`[Authorize]`-Attributs](xref:security/authorization/simple) mit dem Namen der Richtlinie basieren (API-Dokumentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span><span class="sxs-lookup"><span data-stu-id="6ece9-177">Access to a controller in the **SERVER** app can be based on using an [`[Authorize]` attribute](xref:security/authorization/simple) with the name of the policy (API documentation: <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>).</span></span>

<span data-ttu-id="6ece9-178">Das folgende Beispiel beschränkt den Zugriff auf Abrechnungsdaten vom `BillingDataController` auf Azure-Abrechnungsadministratoren mit dem Richtliniennamen `BillingAdministrator`:</span><span class="sxs-lookup"><span data-stu-id="6ece9-178">The following example limits access to billing data from the `BillingDataController` to Azure Billing Administrators with a policy name of `BillingAdministrator`:</span></span>

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

<span data-ttu-id="6ece9-179">Weitere Informationen finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="6ece9-179">For more information, see <xref:security/authorization/policies>.</span></span>

## <a name="app-roles"></a><span data-ttu-id="6ece9-180">App-Rollen</span><span class="sxs-lookup"><span data-stu-id="6ece9-180">App Roles</span></span>

<span data-ttu-id="6ece9-181">Informationen zur Konfiguration der App im Azure-Portal für die Bereitstellung eines App-Rollen-Mitgliedschaftsanspruchs finden Sie in der unter [Vorgehensweise: Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).</span><span class="sxs-lookup"><span data-stu-id="6ece9-181">To configure the app in the Azure portal to provide App Roles membership claims, see [How to: Add app roles in your application and receive them in the token](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) in the Azure documentation.</span></span>

<span data-ttu-id="6ece9-182">Im folgenden Beispiel wird angenommen, dass die **CLIENT**- und **SERVER**-Apps mit zwei Rollen konfiguriert und die Rollen einem Testbenutzer zugewiesen sind:</span><span class="sxs-lookup"><span data-stu-id="6ece9-182">The following example assumes that the **CLIENT** and **SERVER** apps are configured with two roles, and the roles are assigned to a test user:</span></span>

* `admin`
* `developer`

> [!NOTE]
> <span data-ttu-id="6ece9-183">Beim Entwickeln einer gehosteten Blazor WebAssembly-App oder eines Client-Server-Paars aus eigenständigen Apps (einer eigenständigen Blazor WebAssembly-App und einer eigenständigen ASP.NET Core-Server-API-/Web-API-App) muss die Manifesteigenschaft `appRoles` der Registrierungen sowohl des Clients als auch des Servers im Azure-Portal dieselben konfigurierten Rollen enthalten.</span><span class="sxs-lookup"><span data-stu-id="6ece9-183">When developing a hosted Blazor WebAssembly app or a client-server pair of standalone apps (a standalone Blazor WebAssembly app and a standalone ASP.NET Core server API/web API app), the `appRoles` manifest property of both the client and the server Azure portal app registrations must include the same configured roles.</span></span> <span data-ttu-id="6ece9-184">Nachdem die Rollen im Manifest der Client-App festgelegt wurden, kopieren Sie diese in Gänze in das Manifest der Server-App.</span><span class="sxs-lookup"><span data-stu-id="6ece9-184">After establishing the roles in the client app's manifest, copy them in their entirety to the server app's manifest.</span></span> <span data-ttu-id="6ece9-185">Wenn die `appRoles` im Manifest für die Registrierungen der Client- und der Server-App nicht deckungsgleich sind, werden keine Rollenansprüche für authentifizierte Benutzer der Server-API/Web-API erstellt, auch wenn das Zugriffstoken über die richtigen Rollenansprüche verfügt.</span><span class="sxs-lookup"><span data-stu-id="6ece9-185">If you don't mirror the manifest `appRoles` between the client and server app registrations, role claims aren't established for authenticated users of the server API/web API, even if their access token has the correct roles claims.</span></span>

> [!NOTE]
> <span data-ttu-id="6ece9-186">Zwar können Sie ohne Azure AD Premium-Konto Gruppen keine Rollen zuweisen, aber Sie können mit einem Azure-Standardkonto Benutzern Rollen zuweisen und einen Rollenanspruch für Benutzer erhalten.</span><span class="sxs-lookup"><span data-stu-id="6ece9-186">Although you can't assign roles to groups without an Azure AD Premium account, you can assign roles to users and receive a roles claim for users with a standard Azure account.</span></span> <span data-ttu-id="6ece9-187">Für die Schritte in diesem Abschnitt ist kein AAD Premium-Konto erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6ece9-187">The guidance in this section doesn't require an AAD Premium account.</span></span>
>
> <span data-ttu-id="6ece9-188">Mehrere Rollen werden im Azure-Portal durch **_separates Hinzufügen eines Benutzers_** für jede weitere Rollenzuweisung zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="6ece9-188">Multiple roles are assigned in the Azure portal by **_re-adding a user_** for each additional role assignment.</span></span>

<span data-ttu-id="6ece9-189">Die im Abschnitt [Benutzerdefiniertes Benutzerkonto](#custom-user-account) gezeigte `CustomAccountFactory` ist so konfiguriert, dass sie auf einen `roles`-Anspruch mit einem JSON-Arraywert reagiert.</span><span class="sxs-lookup"><span data-stu-id="6ece9-189">The `CustomAccountFactory` shown in the [Custom user account](#custom-user-account) section is set up to act on a `roles` claim with a JSON array value.</span></span> <span data-ttu-id="6ece9-190">Fügen Sie die `CustomAccountFactory` in der **CLIENT**-App hinzu, und registrieren Sie sie, wie im Abschnitt [Benutzerdefiniertes Benutzerkonto](#custom-user-account) gezeigt.</span><span class="sxs-lookup"><span data-stu-id="6ece9-190">Add and register the `CustomAccountFactory` in the **CLIENT** app as shown in the [Custom user account](#custom-user-account) section.</span></span> <span data-ttu-id="6ece9-191">Für das Entfernen des ursprünglichen `roles`-Anspruchs muss kein Code bereitgestellt werden, da das Framework diesen automatisch entfernt.</span><span class="sxs-lookup"><span data-stu-id="6ece9-191">There's no need to provide code to remove the original `roles` claim because it's automatically removed by the framework.</span></span>

<span data-ttu-id="6ece9-192">Geben Sie in `Program.Main` einer **CLIENT**-App den Anspruch namens `appRole` als Rollenanspruch für <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>-Überprüfungen an:</span><span class="sxs-lookup"><span data-stu-id="6ece9-192">In `Program.Main` of a **CLIENT** app, specify the claim named "`appRole`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.UserOptions.RoleClaim = "appRole";
});
```

> [!NOTE]
> <span data-ttu-id="6ece9-193">Wenn Sie lieber den `directoryRoles`-Anspruch (ADD-Administratorrollen) verwenden möchten, weisen Sie `directoryRoles` zu <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType> zu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-193">If you prefer to use the `directoryRoles` claim (ADD Administrator Roles), assign "`directoryRoles`" to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationUserOptions.RoleClaim?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="6ece9-194">Geben Sie in `Startup.ConfigureServices` einer **SERVER**-App den Anspruch namens `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` als Rollenanspruch für <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType>-Überprüfungen an:</span><span class="sxs-lookup"><span data-stu-id="6ece9-194">In `Startup.ConfigureServices` of a **SERVER** app, specify the claim named "`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`" as the role claim for <xref:System.Security.Claims.ClaimsPrincipal.IsInRole%2A?displayProperty=nameWithType> checks:</span></span>

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
> <span data-ttu-id="6ece9-195">Wenn Sie lieber den `wids`-Anspruch (ADD-Administratorrollen) verwenden möchten, weisen Sie `wids` zu <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType> zu.</span><span class="sxs-lookup"><span data-stu-id="6ece9-195">If you prefer to use the `wids` claim (ADD Administrator Roles), assign "`wids`" to the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.RoleClaimType?displayProperty=nameWithType>.</span></span>

<span data-ttu-id="6ece9-196">Die Komponentenautorisierungsansätze sind Stand jetzt funktional.</span><span class="sxs-lookup"><span data-stu-id="6ece9-196">Component authorization approaches are functional at this point.</span></span> <span data-ttu-id="6ece9-197">Jeder der Autorisierungsmechanismen in Komponenten der **CLIENT**-App kann die Rolle `admin` verwenden, um Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="6ece9-197">Any of the authorization mechanisms in components of the **CLIENT** app can use the `admin` role to authorize the user:</span></span>

* [<span data-ttu-id="6ece9-198">`AuthorizeView`-Komponente</span><span class="sxs-lookup"><span data-stu-id="6ece9-198">`AuthorizeView` component</span></span>](xref:blazor/security/index#authorizeview-component)

  ```razor
  <AuthorizeView Roles="admin">
  ```

* <span data-ttu-id="6ece9-199">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="6ece9-199">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="6ece9-200">Prozedurale Logik</span><span class="sxs-lookup"><span data-stu-id="6ece9-200">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
  var user = authState.User;

  if (user.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="6ece9-201">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ece9-201">Multiple role tests are supported:</span></span>

* <span data-ttu-id="6ece9-202">Legen Sie mithilfe der `AuthorizeView`-Komponente als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-202">Require that the user be in **either** the `admin` **or** `developer` role with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin, developer">
      ...
  </AuthorizeView>
  ```

* <span data-ttu-id="6ece9-203">Legen Sie mithilfe der `AuthorizeView`-Komponente als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-203">Require that the user be in **both** the `admin` **and** `developer` roles with the `AuthorizeView` component:</span></span>

  ```razor
  <AuthorizeView Roles="admin">
      <AuthorizeView Roles="developer">
          ...
      </AuthorizeView>
  </AuthorizeView>
  ```

* <span data-ttu-id="6ece9-204">Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-204">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="6ece9-205">Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-205">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```razor
  @attribute [Authorize(Roles = "admin")]
  @attribute [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="6ece9-206">Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-206">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="6ece9-207">Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat. Ändern Sie dazu im vorherigen Beispiel das [bedingte OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in ein [bedingtes AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators):</span><span class="sxs-lookup"><span data-stu-id="6ece9-207">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  ```

<span data-ttu-id="6ece9-208">Jeder der Autorisierungsmechanismen in Controllern der **SERVER**-App kann die Rolle `admin` verwenden, um Benutzer zu autorisieren:</span><span class="sxs-lookup"><span data-stu-id="6ece9-208">Any of the authorization mechanisms in controllers of the **SERVER** app can use the `admin` role to authorize the user:</span></span>

* <span data-ttu-id="6ece9-209">[`[Authorize]`-Attributanweisung](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span><span class="sxs-lookup"><span data-stu-id="6ece9-209">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>)</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  ```

* [<span data-ttu-id="6ece9-210">Prozedurale Logik</span><span class="sxs-lookup"><span data-stu-id="6ece9-210">Procedural logic</span></span>](xref:blazor/security/index#procedural-logic)

  ```csharp
  if (User.IsInRole("admin")) { ... }
  ```

<span data-ttu-id="6ece9-211">Mehrere Rollentests werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6ece9-211">Multiple role tests are supported:</span></span>

* <span data-ttu-id="6ece9-212">Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-212">Require that the user be in **either** the `admin` **or** `developer` role with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin, developer")]
  ```

* <span data-ttu-id="6ece9-213">Legen Sie mithilfe des `[Authorize]`-Attributs als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-213">Require that the user be in **both** the `admin` **and** `developer` roles with the `[Authorize]` attribute:</span></span>

  ```csharp
  [Authorize(Roles = "admin")]
  [Authorize(Roles = "developer")]
  ```

* <span data-ttu-id="6ece9-214">Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **entweder** die Rolle `admin` **oder** die Rolle `developer` innehat:</span><span class="sxs-lookup"><span data-stu-id="6ece9-214">Require that the user be in **either** the `admin` **or** `developer` role with procedural code:</span></span>

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

* <span data-ttu-id="6ece9-215">Legen Sie per prozeduralem Code als erforderlich fest, dass der Benutzer **sowohl** die Rolle `admin` **als auch** die Rolle `developer` innehat. Ändern Sie dazu im vorherigen Beispiel das [bedingte OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in ein [bedingtes AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators):</span><span class="sxs-lookup"><span data-stu-id="6ece9-215">Require that the user be in **both** the `admin` **and** `developer` roles with procedural code by changing the [conditional OR (`||`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) to a [conditional AND (`&&`)](/dotnet/csharp/language-reference/operators/boolean-logical-operators) in the preceding example:</span></span>

  ```csharp
  if (User.IsInRole("admin") && User.IsInRole("developer"))
  ```

## <a name="additional-resources"></a><span data-ttu-id="6ece9-216">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6ece9-216">Additional resources</span></span>

* [<span data-ttu-id="6ece9-217">Rollenvorlagen-IDs (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="6ece9-217">Role template IDs (Azure documentation)</span></span>](/azure/active-directory/roles/permissions-reference#role-template-ids)
* [<span data-ttu-id="6ece9-218">`groupMembershipClaims`-Attribut (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="6ece9-218">`groupMembershipClaims` attribute (Azure documentation)</span></span>](/azure/active-directory/develop/reference-app-manifest#groupmembershipclaims-attribute)
* [<span data-ttu-id="6ece9-219">How to: Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="6ece9-219">How to: Add app roles in your application and receive them in the token (Azure documentation)</span></span>](/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)
* [<span data-ttu-id="6ece9-220">Anwendungsrollen (Azure-Dokumentation)</span><span class="sxs-lookup"><span data-stu-id="6ece9-220">Application roles (Azure documentation)</span></span>](/azure/architecture/multitenant-identity/app-roles)
* <xref:security/authorization/claims>
* <xref:security/authorization/roles>
* <xref:blazor/security/index>
