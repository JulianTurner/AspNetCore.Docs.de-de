---
title: Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie zusätzliche Ansprüche und Token von externen Anbietern einrichten.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
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
uid: security/authentication/social/additional-claims
ms.openlocfilehash: 733afec8d3253ec58a7edf6d7fcf35e303a7fe57
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060325"
---
# <a name="persist-additional-claims-and-tokens-from-external-providers-in-aspnet-core"></a><span data-ttu-id="17627-103">Persistente weitere Ansprüche und Token von externen Anbietern in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="17627-103">Persist additional claims and tokens from external providers in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="17627-104">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="17627-104">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="17627-105">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="17627-105">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="17627-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17627-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="17627-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="17627-107">Prerequisites</span></span>

<span data-ttu-id="17627-108">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="17627-108">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="17627-109">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="17627-109">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="17627-110">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="17627-110">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="17627-111">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="17627-111">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="17627-112">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="17627-112">Set the client ID and client secret</span></span>

<span data-ttu-id="17627-113">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="17627-113">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="17627-114">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="17627-114">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="17627-115">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="17627-115">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="17627-116">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="17627-116">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="17627-117">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-117">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="17627-118">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-118">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="17627-119">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-119">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="17627-120">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-120">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="17627-121">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="17627-121">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="17627-122">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="17627-122">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="17627-123">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="17627-123">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="17627-124">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="17627-124">Establish the authentication scope</span></span>

<span data-ttu-id="17627-125">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie angeben <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="17627-125">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="17627-126">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="17627-126">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="17627-127">Anbieter</span><span class="sxs-lookup"><span data-stu-id="17627-127">Provider</span></span>  | <span data-ttu-id="17627-128">`Scope`</span><span class="sxs-lookup"><span data-stu-id="17627-128">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="17627-129">Facebook</span><span class="sxs-lookup"><span data-stu-id="17627-129">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="17627-130">Google</span><span class="sxs-lookup"><span data-stu-id="17627-130">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="17627-131">Microsoft</span><span class="sxs-lookup"><span data-stu-id="17627-131">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="17627-132">Twitter</span><span class="sxs-lookup"><span data-stu-id="17627-132">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="17627-133">In der Beispiel-APP `userinfo.profile` wird der Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> für aufgerufen wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="17627-133">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="17627-134">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="17627-134">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="17627-135">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="17627-135">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="17627-136">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-136">Map user data keys and create claims</span></span>

<span data-ttu-id="17627-137">Geben Sie in den Optionen des Anbieters <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> in den JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="17627-137">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="17627-138">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="17627-138">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="17627-139">Die Beispiel-App erstellt Gebiets Schema-( `urn:google:locale` ) und Bild ( `urn:google:picture` )-Ansprüche aus den `locale` `picture` Schlüsseln und in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="17627-139">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="17627-140">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` wird ein <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) bei der APP mit signiert <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="17627-140">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="17627-141">Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="17627-141">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="17627-142">In der Beispiel-APP `OnPostConfirmationAsync` richtet ( *Account/externzuweisung. cshtml. cs* ) die locale ( `urn:google:locale` )-und Image ( `urn:google:picture` )-Ansprüche für die angemeldete `ApplicationUser` ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="17627-142">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="17627-143">Standardmäßig werden die Ansprüche eines Benutzers in der Authentifizierung gespeichert :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="17627-143">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="17627-144">Wenn die Authentifizierung :::no-loc(cookie)::: zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="17627-144">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="17627-145">Der Browser erkennt, dass der :::no-loc(cookie)::: Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="17627-145">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="17627-146">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="17627-146">The overall size of the request is too large.</span></span>

<span data-ttu-id="17627-147">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="17627-147">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="17627-148">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="17627-148">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="17627-149">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> für die :::no-loc(Cookie)::: Authentifizierungs Middleware <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> , um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="17627-149">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="17627-150">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="17627-150">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="17627-151">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="17627-151">Save the access token</span></span>

<span data-ttu-id="17627-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung in der gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="17627-152"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="17627-153">`SaveTokens` wird standardmäßig auf festgelegt `false` , um die Größe der abschließenden Authentifizierung zu verringern :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="17627-153">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="17627-154">Die Beispiel-App legt den Wert von `SaveTokens` auf `true` in fest <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="17627-154">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="17627-155">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser` der `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="17627-155">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="17627-156">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="17627-156">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="17627-157">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="17627-157">How to add additional custom tokens</span></span>

<span data-ttu-id="17627-158">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von gespeichert wird `SaveTokens` , fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="17627-158">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/3.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="17627-159">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-159">Creating and adding claims</span></span>

<span data-ttu-id="17627-160">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="17627-160">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="17627-161">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="17627-161">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="17627-162">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von ableiten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und die abstrakte- <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="17627-162">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="17627-163">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="17627-163">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="17627-164">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-164">Removal of claim actions and claims</span></span>

<span data-ttu-id="17627-165">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="17627-165">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="17627-166">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="17627-166">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="17627-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="17627-167"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="17627-168">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="17627-168">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17627-169">Eine ASP.net Core-App kann zusätzliche Ansprüche und Token von externen Authentifizierungs Anbietern (z. b. Facebook, Google, Microsoft und Twitter) einrichten.</span><span class="sxs-lookup"><span data-stu-id="17627-169">An ASP.NET Core app can establish additional claims and tokens from external authentication providers, such as Facebook, Google, Microsoft, and Twitter.</span></span> <span data-ttu-id="17627-170">Jeder Anbieter zeigt verschiedene Informationen über Benutzer auf seiner Plattform an, aber das Muster für das empfangen und Transformieren von Benutzerdaten in zusätzliche Ansprüche ist identisch.</span><span class="sxs-lookup"><span data-stu-id="17627-170">Each provider reveals different information about users on its platform, but the pattern for receiving and transforming user data into additional claims is the same.</span></span>

<span data-ttu-id="17627-171">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="17627-171">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/social/additional-claims/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="17627-172">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="17627-172">Prerequisites</span></span>

<span data-ttu-id="17627-173">Entscheiden Sie, welche externen Authentifizierungs Anbieter in der App unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="17627-173">Decide which external authentication providers to support in the app.</span></span> <span data-ttu-id="17627-174">Registrieren Sie die APP für jeden Anbieter, und erhalten Sie eine Client-ID und einen geheimen Client Schlüssel.</span><span class="sxs-lookup"><span data-stu-id="17627-174">For each provider, register the app and obtain a client ID and client secret.</span></span> <span data-ttu-id="17627-175">Weitere Informationen finden Sie unter <xref:security/authentication/social/index>.</span><span class="sxs-lookup"><span data-stu-id="17627-175">For more information, see <xref:security/authentication/social/index>.</span></span> <span data-ttu-id="17627-176">Die Beispiel-App verwendet den [Google-Authentifizierungs Anbieter](xref:security/authentication/google-logins).</span><span class="sxs-lookup"><span data-stu-id="17627-176">The sample app uses the [Google authentication provider](xref:security/authentication/google-logins).</span></span>

## <a name="set-the-client-id-and-client-secret"></a><span data-ttu-id="17627-177">Festlegen der Client-ID und des geheimen Client Schlüssels</span><span class="sxs-lookup"><span data-stu-id="17627-177">Set the client ID and client secret</span></span>

<span data-ttu-id="17627-178">Der OAuth-Authentifizierungs Anbieter richtet eine Vertrauensstellung mit einer App mithilfe einer Client-ID und eines geheimen Client Schlüssels ein.</span><span class="sxs-lookup"><span data-stu-id="17627-178">The OAuth authentication provider establishes a trust relationship with an app using a client ID and client secret.</span></span> <span data-ttu-id="17627-179">Die Werte für die Client-ID und den geheimen Client Schlüssel werden vom externen Authentifizierungs Anbieter für die App erstellt, wenn die APP beim Anbieter registriert ist.</span><span class="sxs-lookup"><span data-stu-id="17627-179">Client ID and client secret values are created for the app by the external authentication provider when the app is registered with the provider.</span></span> <span data-ttu-id="17627-180">Jeder von der APP verwendete externe Anbieter muss unabhängig von der Client-ID des Anbieters und dem geheimen Client Schlüssel konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="17627-180">Each external provider that the app uses must be configured independently with the provider's client ID and client secret.</span></span> <span data-ttu-id="17627-181">Weitere Informationen finden Sie in den Themen zu externen Authentifizierungs Anbietern, die für Ihr Szenario gelten:</span><span class="sxs-lookup"><span data-stu-id="17627-181">For more information, see the external authentication provider topics that apply to your scenario:</span></span>

* [<span data-ttu-id="17627-182">Facebook-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-182">Facebook authentication</span></span>](xref:security/authentication/facebook-logins)
* [<span data-ttu-id="17627-183">Google-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-183">Google authentication</span></span>](xref:security/authentication/google-logins)
* [<span data-ttu-id="17627-184">Microsoft-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-184">Microsoft authentication</span></span>](xref:security/authentication/microsoft-logins)
* [<span data-ttu-id="17627-185">Twitter-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="17627-185">Twitter authentication</span></span>](xref:security/authentication/twitter-logins)
* [<span data-ttu-id="17627-186">Andere Authentifizierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="17627-186">Other authentication providers</span></span>](xref:security/authentication/otherlogins)
* [<span data-ttu-id="17627-187">OpenIdConnect</span><span class="sxs-lookup"><span data-stu-id="17627-187">OpenIdConnect</span></span>](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)

<span data-ttu-id="17627-188">Die Beispiel-App konfiguriert den Google-Authentifizierungs Anbieter mit einer Client-ID und einem geheimen Client Schlüssel, die von Google bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="17627-188">The sample app configures the Google authentication provider with a client ID and client secret provided by Google:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=4,9)]

## <a name="establish-the-authentication-scope"></a><span data-ttu-id="17627-189">Einrichten des Authentifizierungs Bereichs</span><span class="sxs-lookup"><span data-stu-id="17627-189">Establish the authentication scope</span></span>

<span data-ttu-id="17627-190">Geben Sie die Liste der Berechtigungen an, die vom Anbieter abgerufen werden sollen, indem Sie angeben <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*> .</span><span class="sxs-lookup"><span data-stu-id="17627-190">Specify the list of permissions to retrieve from the provider by specifying the <xref:Microsoft.AspNetCore.Authentication.OAuth.OAuthOptions.Scope*>.</span></span> <span data-ttu-id="17627-191">Authentifizierungs Bereiche für allgemeine externe Anbieter werden in der folgenden Tabelle angezeigt.</span><span class="sxs-lookup"><span data-stu-id="17627-191">Authentication scopes for common external providers appear in the following table.</span></span>

| <span data-ttu-id="17627-192">Anbieter</span><span class="sxs-lookup"><span data-stu-id="17627-192">Provider</span></span>  | <span data-ttu-id="17627-193">`Scope`</span><span class="sxs-lookup"><span data-stu-id="17627-193">Scope</span></span>                                                            |
| --------- | ---------------------------------------------------------------- |
| <span data-ttu-id="17627-194">Facebook</span><span class="sxs-lookup"><span data-stu-id="17627-194">Facebook</span></span>  | `https://www.facebook.com/dialog/oauth`                          |
| <span data-ttu-id="17627-195">Google</span><span class="sxs-lookup"><span data-stu-id="17627-195">Google</span></span>    | `https://www.googleapis.com/auth/userinfo.profile`               |
| <span data-ttu-id="17627-196">Microsoft</span><span class="sxs-lookup"><span data-stu-id="17627-196">Microsoft</span></span> | `https://login.microsoftonline.com/common/oauth2/v2.0/authorize` |
| <span data-ttu-id="17627-197">Twitter</span><span class="sxs-lookup"><span data-stu-id="17627-197">Twitter</span></span>   | `https://api.twitter.com/oauth/authenticate`                     |

<span data-ttu-id="17627-198">In der Beispiel-APP `userinfo.profile` wird der Bereich von Google automatisch durch das Framework hinzugefügt, wenn <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> für aufgerufen wird <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="17627-198">In the sample app, Google's `userinfo.profile` scope is automatically added by the framework when <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> is called on the <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilder>.</span></span> <span data-ttu-id="17627-199">Wenn die APP zusätzliche Bereiche benötigt, fügen Sie Sie den Optionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="17627-199">If the app requires additional scopes, add them to the options.</span></span> <span data-ttu-id="17627-200">Im folgenden Beispiel wird der Google `https://www.googleapis.com/auth/user.birthday.read` -Bereich hinzugefügt, um den Geburtstag eines Benutzers abzurufen:</span><span class="sxs-lookup"><span data-stu-id="17627-200">In the following example, the Google `https://www.googleapis.com/auth/user.birthday.read` scope is added in order to retrieve a user's birthday:</span></span>

```csharp
options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read");
```

## <a name="map-user-data-keys-and-create-claims"></a><span data-ttu-id="17627-201">Zuordnen von Benutzerdaten Schlüsseln und Erstellen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-201">Map user data keys and create claims</span></span>

<span data-ttu-id="17627-202">Geben Sie in den Optionen des Anbieters <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> in den JSON-Benutzerdaten des externen Anbieters einen oder für jeden Schlüssel/Unterschlüssel an, damit die APP-Identität bei der Anmeldung gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="17627-202">In the provider's options, specify a <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonKey*> or <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.MapJsonSubKey*> for each key/subkey in the external provider's JSON user data for the app identity to read on sign in.</span></span> <span data-ttu-id="17627-203">Weitere Informationen zu Anspruchs Typen finden Sie unter <xref:System.Security.Claims.ClaimTypes> .</span><span class="sxs-lookup"><span data-stu-id="17627-203">For more information on claim types, see <xref:System.Security.Claims.ClaimTypes>.</span></span>

<span data-ttu-id="17627-204">Die Beispiel-App erstellt Gebiets Schema-( `urn:google:locale` ) und Bild ( `urn:google:picture` )-Ansprüche aus den `locale` `picture` Schlüsseln und in Google User Data:</span><span class="sxs-lookup"><span data-stu-id="17627-204">The sample app creates locale (`urn:google:locale`) and picture (`urn:google:picture`) claims from the `locale` and `picture` keys in Google user data:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=13-14)]

<span data-ttu-id="17627-205">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync` wird ein <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> ( `ApplicationUser` ) bei der APP mit signiert <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*> .</span><span class="sxs-lookup"><span data-stu-id="17627-205">In `Microsoft.AspNetCore.:::no-loc(Identity):::.UI.Pages.Account.Internal.ExternalLoginModel.OnPostConfirmationAsync`, an <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::User> (`ApplicationUser`) is signed into the app with <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager%601.SignInAsync*>.</span></span> <span data-ttu-id="17627-206">Während des Anmeldevorgangs <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> kann eine `ApplicationUser` Ansprüche für Benutzerdaten speichern, die im verfügbar sind <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*> .</span><span class="sxs-lookup"><span data-stu-id="17627-206">During the sign in process, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.UserManager%601> can store an `ApplicationUser` claims for user data available from the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.Principal*>.</span></span>

<span data-ttu-id="17627-207">In der Beispiel-APP `OnPostConfirmationAsync` richtet ( *Account/externzuweisung. cshtml. cs* ) die locale ( `urn:google:locale` )-und Image ( `urn:google:picture` )-Ansprüche für die angemeldete `ApplicationUser` ein, einschließlich eines Anspruchs für <xref:System.Security.Claims.ClaimTypes.GivenName> :</span><span class="sxs-lookup"><span data-stu-id="17627-207">In the sample app, `OnPostConfirmationAsync` ( *Account/ExternalLogin.cshtml.cs* ) establishes the locale (`urn:google:locale`) and picture (`urn:google:picture`) claims for the signed in `ApplicationUser`, including a claim for <xref:System.Security.Claims.ClaimTypes.GivenName>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=35-51)]

<span data-ttu-id="17627-208">Standardmäßig werden die Ansprüche eines Benutzers in der Authentifizierung gespeichert :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="17627-208">By default, a user's claims are stored in the authentication :::no-loc(cookie):::.</span></span> <span data-ttu-id="17627-209">Wenn die Authentifizierung :::no-loc(cookie)::: zu groß ist, kann dies dazu führen, dass die APP fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="17627-209">If the authentication :::no-loc(cookie)::: is too large, it can cause the app to fail because:</span></span>

* <span data-ttu-id="17627-210">Der Browser erkennt, dass der :::no-loc(cookie)::: Header zu lang ist.</span><span class="sxs-lookup"><span data-stu-id="17627-210">The browser detects that the :::no-loc(cookie)::: header is too long.</span></span>
* <span data-ttu-id="17627-211">Die Gesamtgröße der Anforderung ist zu groß.</span><span class="sxs-lookup"><span data-stu-id="17627-211">The overall size of the request is too large.</span></span>

<span data-ttu-id="17627-212">Wenn eine große Menge an Benutzerdaten für die Verarbeitung von Benutzer Anforderungen erforderlich ist:</span><span class="sxs-lookup"><span data-stu-id="17627-212">If a large amount of user data is required for processing user requests:</span></span>

* <span data-ttu-id="17627-213">Beschränken Sie die Anzahl und die Größe von Benutzer Ansprüchen für die Anforderungs Verarbeitung auf das, was die APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="17627-213">Limit the number and size of user claims for request processing to only what the app requires.</span></span>
* <span data-ttu-id="17627-214">Verwenden Sie eine benutzerdefinierte <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> für die :::no-loc(Cookie)::: Authentifizierungs Middleware <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> , um die Identität über Anforderungen hinweg zu speichern.</span><span class="sxs-lookup"><span data-stu-id="17627-214">Use a custom <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.ITicketStore> for the :::no-loc(Cookie)::: Authentication Middleware's <xref:Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s.:::no-loc(Cookie):::AuthenticationOptions.SessionStore> to store identity across requests.</span></span> <span data-ttu-id="17627-215">Bewahren Sie große Mengen an Identitätsinformationen auf dem Server auf, und senden Sie nur einen kleinen Sitzungs-ID-Schlüssel an den Client.</span><span class="sxs-lookup"><span data-stu-id="17627-215">Preserve large quantities of identity information on the server while only sending a small session identifier key to the client.</span></span>

## <a name="save-the-access-token"></a><span data-ttu-id="17627-216">Speichern des Zugriffs Tokens</span><span class="sxs-lookup"><span data-stu-id="17627-216">Save the access token</span></span>

<span data-ttu-id="17627-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> definiert, ob Zugriffs-und Aktualisierungs Token <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> nach einer erfolgreichen Autorisierung in der gespeichert werden sollen.</span><span class="sxs-lookup"><span data-stu-id="17627-217"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.SaveTokens*> defines whether access and refresh tokens should be stored in the <xref:Microsoft.AspNetCore.Http.Authentication.AuthenticationProperties> after a successful authorization.</span></span> <span data-ttu-id="17627-218">`SaveTokens` wird standardmäßig auf festgelegt `false` , um die Größe der abschließenden Authentifizierung zu verringern :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="17627-218">`SaveTokens` is set to `false` by default to reduce the size of the final authentication :::no-loc(cookie):::.</span></span>

<span data-ttu-id="17627-219">Die Beispiel-App legt den Wert von `SaveTokens` auf `true` in fest <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> :</span><span class="sxs-lookup"><span data-stu-id="17627-219">The sample app sets the value of `SaveTokens` to `true` in <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=15)]

<span data-ttu-id="17627-220">Wenn `OnPostConfirmationAsync` ausgeführt wird, speichern Sie das Zugriffs Token ([externzuweisung-Info. authenticationtokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) vom externen Anbieter in `ApplicationUser` der `AuthenticationProperties` .</span><span class="sxs-lookup"><span data-stu-id="17627-220">When `OnPostConfirmationAsync` executes, store the access token ([ExternalLoginInfo.AuthenticationTokens](xref:Microsoft.AspNetCore.:::no-loc(Identity):::.ExternalLoginInfo.AuthenticationTokens*)) from the external provider in the `ApplicationUser`'s `AuthenticationProperties`.</span></span>

<span data-ttu-id="17627-221">Die Beispiel-App speichert das Zugriffs Token in `OnPostConfirmationAsync` (Neue Benutzerregistrierung) und `OnGetCallbackAsync` (zuvor registrierter Benutzer) in *Account/externzuzugriffgin. cshtml. cs* :</span><span class="sxs-lookup"><span data-stu-id="17627-221">The sample app saves the access token in `OnPostConfirmationAsync` (new user registration) and `OnGetCallbackAsync` (previously registered user) in *Account/ExternalLogin.cshtml.cs* :</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Areas/:::no-loc(Identity):::/Pages/Account/ExternalLogin.cshtml.cs?name=snippet_OnPostConfirmationAsync&highlight=54-56)]

## <a name="how-to-add-additional-custom-tokens"></a><span data-ttu-id="17627-222">Vorgehensweise beim Hinzufügen zusätzlicher benutzerdefinierter Token</span><span class="sxs-lookup"><span data-stu-id="17627-222">How to add additional custom tokens</span></span>

<span data-ttu-id="17627-223">Um zu veranschaulichen, wie Sie ein benutzerdefiniertes Token hinzufügen, das als Teil von gespeichert wird `SaveTokens` , fügt die Beispiel-APP einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> mit dem aktuellen <xref:System.DateTime> für einen [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) von hinzu `TicketCreated` :</span><span class="sxs-lookup"><span data-stu-id="17627-223">To demonstrate how to add a custom token, which is stored as part of `SaveTokens`, the sample app adds an <xref:Microsoft.AspNetCore.Authentication.AuthenticationToken> with the current <xref:System.DateTime> for an [AuthenticationToken.Name](xref:Microsoft.AspNetCore.Authentication.AuthenticationToken.Name*) of `TicketCreated`:</span></span>

[!code-csharp[](additional-claims/samples/2.x/ClaimsSample/Startup.cs?name=snippet_AddGoogle&highlight=17-30)]

## <a name="creating-and-adding-claims"></a><span data-ttu-id="17627-224">Erstellen und Hinzufügen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-224">Creating and adding claims</span></span>

<span data-ttu-id="17627-225">Das Framework bietet allgemeine Aktionen und Erweiterungs Methoden zum Erstellen und Hinzufügen von Ansprüchen zur Auflistung.</span><span class="sxs-lookup"><span data-stu-id="17627-225">The framework provides common actions and extension methods for creating and adding claims to the collection.</span></span> <span data-ttu-id="17627-226">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> und <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span><span class="sxs-lookup"><span data-stu-id="17627-226">For more information, see the <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions> and <xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionUniqueExtensions>.</span></span>

<span data-ttu-id="17627-227">Benutzer können benutzerdefinierte Aktionen definieren, indem Sie von ableiten <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> und die abstrakte- <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> Methode implementieren.</span><span class="sxs-lookup"><span data-stu-id="17627-227">Users can define custom actions by deriving from <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction> and implementing the abstract <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.Run*> method.</span></span>

<span data-ttu-id="17627-228">Weitere Informationen finden Sie unter <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span><span class="sxs-lookup"><span data-stu-id="17627-228">For more information, see <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims>.</span></span>

## <a name="removal-of-claim-actions-and-claims"></a><span data-ttu-id="17627-229">Entfernen von Anspruchs Aktionen und Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="17627-229">Removal of claim actions and claims</span></span>

<span data-ttu-id="17627-230">[Claimaktioncollection. Remove (String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) entfernt alle Anspruchs Aktionen für den angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Auflistung.</span><span class="sxs-lookup"><span data-stu-id="17627-230">[ClaimActionCollection.Remove(String)](xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimActionCollection.Remove*) removes all claim actions for the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the collection.</span></span> <span data-ttu-id="17627-231">[Claimaktioncollectionmapextensions. deleteclaim (claimaktioncollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) löscht einen Anspruch des angegebenen <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> aus der Identität.</span><span class="sxs-lookup"><span data-stu-id="17627-231">[ClaimActionCollectionMapExtensions.DeleteClaim(ClaimActionCollection, String)](xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*) deletes a claim of the given <xref:Microsoft.AspNetCore.Authentication.OAuth.Claims.ClaimAction.ClaimType> from the identity.</span></span> <span data-ttu-id="17627-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> wird hauptsächlich mit [OpenID Connect (oidc)](/azure/active-directory/develop/v2-protocols-oidc) verwendet, um vom Protokoll generierte Ansprüche zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="17627-232"><xref:Microsoft.AspNetCore.Authentication.ClaimActionCollectionMapExtensions.DeleteClaim*> is primarily used with [OpenID Connect (OIDC)](/azure/active-directory/develop/v2-protocols-oidc) to remove protocol-generated claims.</span></span>

## <a name="sample-app-output"></a><span data-ttu-id="17627-233">Beispiel-App-Ausgabe</span><span class="sxs-lookup"><span data-stu-id="17627-233">Sample app output</span></span>

```
User Claims

http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier
    9b342344f-7aab-43c2-1ac1-ba75912ca999
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name
    someone@gmail.com
AspNet.:::no-loc(Identity):::.SecurityStamp
    7D4312MOWRYYBFI1KXRPHGOSTBVWSFDE
http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname
    Judy
urn:google:locale
    en
urn:google:picture
    https://lh4.googleusercontent.com/-XXXXXX/XXXXXX/XXXXXX/XXXXXX/photo.jpg

Authentication Properties

.Token.access_token
    yc23.AlvoZqz56...1lxltXV7D-ZWP9
.Token.token_type
    Bearer
.Token.expires_at
    2019-04-11T22:14:51.0000000+00:00
.Token.TicketCreated
    4/11/2019 9:14:52 PM
.TokenNames
    access_token;token_type;expires_at;TicketCreated
.persistent
.issued
    Thu, 11 Apr 2019 20:51:06 GMT
.expires
    Thu, 25 Apr 2019 20:51:06 GMT

```

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="17627-234">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="17627-234">Additional resources</span></span>

* <span data-ttu-id="17627-235">[dotnet/aspnetcore Engineering socialsample-App](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): die verknüpfte Beispiel-App befindet sich in der Engineering-Verzweigung des [dotnet/aspnetcore-GitHub-](https://github.com/dotnet/AspNetCore) Repository `master` .</span><span class="sxs-lookup"><span data-stu-id="17627-235">[dotnet/AspNetCore engineering SocialSample app](https://github.com/dotnet/AspNetCore/tree/master/src/Security/Authentication/samples/SocialSample): The linked sample app is on the [dotnet/AspNetCore GitHub repo's](https://github.com/dotnet/AspNetCore) `master` engineering branch.</span></span> <span data-ttu-id="17627-236">Die `master` Verzweigung enthält Code, der in der aktiven Entwicklung für die nächste Version von ASP.net Core enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="17627-236">The `master` branch contains code under active development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="17627-237">Um eine Version der Beispiel-App für eine veröffentlichte Version von ASP.net Core anzuzeigen, wählen Sie in der Dropdown Liste **Verzweigung** einen releasebranch aus (z `release/{X.Y}` . b.).</span><span class="sxs-lookup"><span data-stu-id="17627-237">To see a version of the sample app for a released version of ASP.NET Core, use the **Branch** drop down list to select a release branch (for example `release/{X.Y}`).</span></span>
