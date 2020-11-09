---
title: 'Verwenden der cookie Authentifizierung ohne ASP.NET Core Identity'
author: rick-anderson
description: 'Erfahren Sie, wie Sie die cookie Authentifizierung ohne verwenden ASP.NET Core Identity .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: 'security/authentication/cookie'
ms.openlocfilehash: 04469e0e75c433b40b364873a7e72e30421936f4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061352"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="5850d-103">Verwenden der cookie Authentifizierung ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5850d-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="5850d-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5850d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5850d-105">ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="5850d-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="5850d-106">Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="5850d-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="5850d-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="5850d-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5850d-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5850d-109">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="5850d-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="5850d-110">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="5850d-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="5850d-111">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="5850d-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="5850d-112">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="5850d-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="5850d-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5850d-113">Configuration</span></span>

<span data-ttu-id="5850d-114">Erstellen Sie in der `Startup.ConfigureServices` -Methode die Authentifizierungs-Middleware-Dienste mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> -Methode und der-Methode <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="5850d-114">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="5850d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5850d-115"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="5850d-116">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="5850d-116">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="5850d-117">Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="5850d-117">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="5850d-118">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="5850d-118">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="5850d-119">Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="5850d-119">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="5850d-120">Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="5850d-120">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="5850d-121">Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="5850d-121">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="5850d-122">Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="5850d-122">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="5850d-123">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="5850d-123">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="5850d-124">In `Startup.Configure` werden `UseAuthentication` und aufgerufen `UseAuthorization` , um die-Eigenschaft festzulegen `HttpContext.User` und die Autorisierungs Middleware für Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="5850d-124">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="5850d-125">Rufen Sie `UseAuthentication` die `UseAuthorization` Methoden und vor, bevor Sie aufrufen `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="5850d-125">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="5850d-126">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5850d-126">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="5850d-127">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="5850d-127">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="5850d-128">Cookie Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="5850d-128">Cookie Policy Middleware</span></span>

<span data-ttu-id="5850d-129">Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen.</span><span class="sxs-lookup"><span data-stu-id="5850d-129">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="5850d-130">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="5850d-130">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="5850d-131">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-131">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="5850d-132">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="5850d-132">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="5850d-133">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="5850d-133">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="5850d-134">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.</span><span class="sxs-lookup"><span data-stu-id="5850d-134">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="5850d-135">Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5850d-135">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="5850d-136">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="5850d-136">MinimumSameSitePolicy</span></span> | <span data-ttu-id="5850d-137">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="5850d-137">Cookie.SameSite</span></span> | <span data-ttu-id="5850d-138">Ergebnis Cookie . SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="5850d-138">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="5850d-139">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-139">SameSiteMode.None</span></span>     | <span data-ttu-id="5850d-140">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-140">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-141">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-141">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-142">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-142">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-143">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-143">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-144">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-144">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-145">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-145">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5850d-146">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-146">SameSiteMode.Lax</span></span>      | <span data-ttu-id="5850d-147">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-147">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-148">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-148">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-149">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-149">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-150">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-150">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-151">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-152">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-152">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5850d-153">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-153">SameSiteMode.Strict</span></span>   | <span data-ttu-id="5850d-154">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-154">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-155">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-155">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-156">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-156">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-157">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-157">SameSiteMode.Strict</span></span><br><span data-ttu-id="5850d-158">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="5850d-159">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-159">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="5850d-160">Erstellen einer Authentifizierung cookie</span><span class="sxs-lookup"><span data-stu-id="5850d-160">Create an authentication cookie</span></span>

<span data-ttu-id="5850d-161">Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="5850d-161">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="5850d-162">Die Benutzerinformationen werden serialisiert und in gespeichert cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-162">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="5850d-163">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="5850d-163">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="5850d-164">`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="5850d-164">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="5850d-165">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-165">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="5850d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> wird standardmäßig nur für einige bestimmte Pfade verwendet, z. b. für den Anmelde Pfad und die Abmelde Pfade.</span><span class="sxs-lookup"><span data-stu-id="5850d-166"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="5850d-167">Weitere Informationen finden Sie in der [ Cookie authenticationhandler-Quelle](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="5850d-167">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="5850d-168">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-168">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="5850d-169">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-169">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="5850d-170">Abmelden</span><span class="sxs-lookup"><span data-stu-id="5850d-170">Sign out</span></span>

<span data-ttu-id="5850d-171">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="5850d-171">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="5850d-172">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-172">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="5850d-173">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-173">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="5850d-174">Wenn der Browser geschlossen cookie wird, werden Sitzungs basierte s (nicht persistente cookie e) automatisch gelöscht, aber keine cookie s, wenn eine einzelne Registerkarte geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-174">When the browser closes it automatically deletes session based cookies (non-persistent cookies), but no cookies are cleared when an individual tab is closed.</span></span> <span data-ttu-id="5850d-175">Der Server wird nicht über Registerkarten-oder Browser schließen-Ereignisse benachrichtigt.</span><span class="sxs-lookup"><span data-stu-id="5850d-175">The server is not notified of tab or browser close events.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="5850d-176">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="5850d-176">React to back-end changes</span></span>

<span data-ttu-id="5850d-177">Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="5850d-177">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="5850d-178">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="5850d-178">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="5850d-179">Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-179">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="5850d-180">Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="5850d-180">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="5850d-181">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-181">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="5850d-182">Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5850d-182">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="5850d-183">Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-183">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="5850d-184">Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="5850d-184">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="5850d-185">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="5850d-185">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="5850d-186">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5850d-186">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="5850d-187">Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:</span><span class="sxs-lookup"><span data-stu-id="5850d-187">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="5850d-188">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="5850d-188">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="5850d-189">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="5850d-189">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="5850d-190">Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="5850d-190">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5850d-191">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="5850d-191">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="5850d-192">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="5850d-192">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="5850d-193">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="5850d-193">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="5850d-194">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5850d-194">The approach described here is triggered on every request.</span></span> <span data-ttu-id="5850d-195">cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="5850d-195">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="5850d-196">Persistente cookie s</span><span class="sxs-lookup"><span data-stu-id="5850d-196">Persistent cookies</span></span>

<span data-ttu-id="5850d-197">Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="5850d-197">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="5850d-198">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-198">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="5850d-199">Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-199">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="5850d-200">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="5850d-200">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="5850d-201">Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="5850d-201">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="5850d-202"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="5850d-202">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="5850d-203">Absoluter cookie Ablauf</span><span class="sxs-lookup"><span data-stu-id="5850d-203">Absolute cookie expiration</span></span>

<span data-ttu-id="5850d-204">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="5850d-204">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="5850d-205">Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-205">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="5850d-206">Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="5850d-206">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="5850d-207">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="5850d-207">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="5850d-208">Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert.</span><span class="sxs-lookup"><span data-stu-id="5850d-208">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="5850d-209">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5850d-209">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5850d-210">ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="5850d-210">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="5850d-211">Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-211">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="5850d-212">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="5850d-212">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="5850d-213">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5850d-213">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5850d-214">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="5850d-214">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="5850d-215">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="5850d-215">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="5850d-216">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="5850d-216">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="5850d-217">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="5850d-217">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="5850d-218">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="5850d-218">Configuration</span></span>

<span data-ttu-id="5850d-219">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für die Datei [Microsoft. aspnetcore. Authentication. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) -Paket.</span><span class="sxs-lookup"><span data-stu-id="5850d-219">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="5850d-220">Erstellen Sie in der `Startup.ConfigureServices` -Methode den Authentifizierungs-Middleware-Dienst mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden und:</span><span class="sxs-lookup"><span data-stu-id="5850d-220">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="5850d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5850d-221"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="5850d-222">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="5850d-222">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="5850d-223">Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="5850d-223">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="5850d-224">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="5850d-224">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="5850d-225">Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="5850d-225">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="5850d-226">Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="5850d-226">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="5850d-227">Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="5850d-227">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="5850d-228">Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="5850d-228">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="5850d-229">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="5850d-229">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="5850d-230">Rufen Sie in der- `Startup.Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="5850d-230">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="5850d-231">Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="5850d-231">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="5850d-232">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="5850d-232">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="5850d-233">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="5850d-233">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="5850d-234">Cookie Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="5850d-234">Cookie Policy Middleware</span></span>

<span data-ttu-id="5850d-235">Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen.</span><span class="sxs-lookup"><span data-stu-id="5850d-235">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="5850d-236">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="5850d-236">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="5850d-237">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-237">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="5850d-238">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="5850d-238">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="5850d-239">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="5850d-239">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="5850d-240">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.</span><span class="sxs-lookup"><span data-stu-id="5850d-240">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="5850d-241">Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="5850d-241">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="5850d-242">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="5850d-242">MinimumSameSitePolicy</span></span> | <span data-ttu-id="5850d-243">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="5850d-243">Cookie.SameSite</span></span> | <span data-ttu-id="5850d-244">Ergebnis Cookie . SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="5850d-244">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="5850d-245">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-245">SameSiteMode.None</span></span>     | <span data-ttu-id="5850d-246">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-246">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-247">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-247">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-248">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-248">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-249">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-249">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-250">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-251">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-251">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5850d-252">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-252">SameSiteMode.Lax</span></span>      | <span data-ttu-id="5850d-253">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-253">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-254">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-254">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-255">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-255">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-256">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-256">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-257">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-258">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-258">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="5850d-259">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-259">SameSiteMode.Strict</span></span>   | <span data-ttu-id="5850d-260">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="5850d-260">SameSiteMode.None</span></span><br><span data-ttu-id="5850d-261">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="5850d-261">SameSiteMode.Lax</span></span><br><span data-ttu-id="5850d-262">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-262">SameSiteMode.Strict</span></span> | <span data-ttu-id="5850d-263">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-263">SameSiteMode.Strict</span></span><br><span data-ttu-id="5850d-264">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-264">SameSiteMode.Strict</span></span><br><span data-ttu-id="5850d-265">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="5850d-265">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="5850d-266">Erstellen einer Authentifizierung cookie</span><span class="sxs-lookup"><span data-stu-id="5850d-266">Create an authentication cookie</span></span>

<span data-ttu-id="5850d-267">Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="5850d-267">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="5850d-268">Die Benutzerinformationen werden serialisiert und in gespeichert cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-268">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="5850d-269">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="5850d-269">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="5850d-270">`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="5850d-270">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="5850d-271">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-271">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="5850d-272">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-272">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="5850d-273">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-273">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="5850d-274">Abmelden</span><span class="sxs-lookup"><span data-stu-id="5850d-274">Sign out</span></span>

<span data-ttu-id="5850d-275">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="5850d-275">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="5850d-276">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-276">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="5850d-277">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="5850d-277">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="5850d-278">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="5850d-278">React to back-end changes</span></span>

<span data-ttu-id="5850d-279">Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="5850d-279">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="5850d-280">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="5850d-280">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="5850d-281">Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-281">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="5850d-282">Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="5850d-282">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="5850d-283">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie .</span><span class="sxs-lookup"><span data-stu-id="5850d-283">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="5850d-284">Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="5850d-284">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="5850d-285">Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-285">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="5850d-286">Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="5850d-286">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="5850d-287">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="5850d-287">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="5850d-288">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5850d-288">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="5850d-289">Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:</span><span class="sxs-lookup"><span data-stu-id="5850d-289">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

```csharp
var claims = new List<Claim>
{
    new Claim(ClaimTypes.Name, user.Email),
    new Claim("LastChanged", {Database Value})
};

var claimsIdentity = new ClaimsIdentity(
    claims, 
    CookieAuthenticationDefaults.AuthenticationScheme);

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme, 
    new ClaimsPrincipal(claimsIdentity));
```

<span data-ttu-id="5850d-290">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="5850d-290">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="5850d-291">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="5850d-291">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

```csharp
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.Cookies;

public class CustomCookieAuthenticationEvents : CookieAuthenticationEvents
{
    private readonly IUserRepository _userRepository;

    public CustomCookieAuthenticationEvents(IUserRepository userRepository)
    {
        // Get the database from registered DI services.
        _userRepository = userRepository;
    }

    public override async Task ValidatePrincipal(CookieValidatePrincipalContext context)
    {
        var userPrincipal = context.Principal;

        // Look for the LastChanged claim.
        var lastChanged = (from c in userPrincipal.Claims
                           where c.Type == "LastChanged"
                           select c.Value).FirstOrDefault();

        if (string.IsNullOrEmpty(lastChanged) ||
            !_userRepository.ValidateLastChanged(lastChanged))
        {
            context.RejectPrincipal();

            await context.HttpContext.SignOutAsync(
                CookieAuthenticationDefaults.AuthenticationScheme);
        }
    }
}
```

<span data-ttu-id="5850d-292">Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="5850d-292">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5850d-293">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="5850d-293">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="5850d-294">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="5850d-294">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="5850d-295">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="5850d-295">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="5850d-296">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="5850d-296">The approach described here is triggered on every request.</span></span> <span data-ttu-id="5850d-297">cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="5850d-297">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="5850d-298">Persistente cookie s</span><span class="sxs-lookup"><span data-stu-id="5850d-298">Persistent cookies</span></span>

<span data-ttu-id="5850d-299">Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="5850d-299">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="5850d-300">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-300">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="5850d-301">Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5850d-301">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="5850d-302">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="5850d-302">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="5850d-303">Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="5850d-303">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="5850d-304"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="5850d-304">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true
    });
```

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="5850d-305">Absoluter cookie Ablauf</span><span class="sxs-lookup"><span data-stu-id="5850d-305">Absolute cookie expiration</span></span>

<span data-ttu-id="5850d-306">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="5850d-306">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="5850d-307">Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="5850d-307">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="5850d-308">Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="5850d-308">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="5850d-309">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="5850d-309">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="5850d-310">Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert.</span><span class="sxs-lookup"><span data-stu-id="5850d-310">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="5850d-311">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="5850d-311">This ignores any sliding expiration settings previously configured.</span></span>

```csharp
// using Microsoft.AspNetCore.Authentication;

await HttpContext.SignInAsync(
    CookieAuthenticationDefaults.AuthenticationScheme,
    new ClaimsPrincipal(claimsIdentity),
    new AuthenticationProperties
    {
        IsPersistent = true,
        ExpiresUtc = DateTime.UtcNow.AddMinutes(20)
    });
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="5850d-312">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5850d-312">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="5850d-313">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="5850d-313">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
