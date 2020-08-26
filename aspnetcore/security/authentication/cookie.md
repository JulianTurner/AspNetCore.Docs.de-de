---
title: Verwenden der cookie Authentifizierung ohne ASP.NET Core Identity
author: rick-anderson
description: Erfahren Sie, wie Sie die cookie Authentifizierung ohne verwenden ASP.NET Core Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/11/2020
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
uid: security/authentication/cookie
ms.openlocfilehash: 04d2f0d289e2c9ec13aeb880df47240bec19d3ec
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876762"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a><span data-ttu-id="91b23-103">Verwenden der cookie Authentifizierung ohne ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="91b23-103">Use cookie authentication without ASP.NET Core Identity</span></span>

<span data-ttu-id="91b23-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="91b23-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="91b23-105">ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="91b23-105">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="91b23-106">Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-106">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="91b23-107">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="91b23-107">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="91b23-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91b23-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="91b23-109">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="91b23-109">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="91b23-110">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="91b23-110">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="91b23-111">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="91b23-111">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="91b23-112">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="91b23-112">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="91b23-113">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="91b23-113">Configuration</span></span>

<span data-ttu-id="91b23-114">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für die Datei [Microsoft. aspnetcore. Authentication. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) -Paket.</span><span class="sxs-lookup"><span data-stu-id="91b23-114">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="91b23-115">Erstellen Sie in der `Startup.ConfigureServices` -Methode die Authentifizierungs-Middleware-Dienste mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> -Methode und der-Methode <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :</span><span class="sxs-lookup"><span data-stu-id="91b23-115">In the `Startup.ConfigureServices` method, create the Authentication Middleware services with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="91b23-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="91b23-116"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="91b23-117">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="91b23-117">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="91b23-118">Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="91b23-118">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="91b23-119">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="91b23-119">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="91b23-120">Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="91b23-120">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="91b23-121">Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="91b23-121">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="91b23-122">Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="91b23-122">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="91b23-123">Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="91b23-123">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="91b23-124">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="91b23-124">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="91b23-125">In `Startup.Configure` werden `UseAuthentication` und aufgerufen `UseAuthorization` , um die-Eigenschaft festzulegen `HttpContext.User` und die Autorisierungs Middleware für Anforderungen auszuführen.</span><span class="sxs-lookup"><span data-stu-id="91b23-125">In `Startup.Configure`, call `UseAuthentication` and `UseAuthorization` to set the `HttpContext.User` property and run Authorization Middleware for requests.</span></span> <span data-ttu-id="91b23-126">Rufen Sie `UseAuthentication` die `UseAuthorization` Methoden und vor, bevor Sie aufrufen `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="91b23-126">Call the `UseAuthentication` and `UseAuthorization` methods before calling `UseEndpoints`:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="91b23-127">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="91b23-127">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="91b23-128">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="91b23-128">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="91b23-129">Cookie Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="91b23-129">Cookie Policy Middleware</span></span>

<span data-ttu-id="91b23-130">Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen.</span><span class="sxs-lookup"><span data-stu-id="91b23-130">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="91b23-131">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="91b23-131">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="91b23-132">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-132">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="91b23-133">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="91b23-133">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="91b23-134">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="91b23-134">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="91b23-135">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.</span><span class="sxs-lookup"><span data-stu-id="91b23-135">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="91b23-136">Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="91b23-136">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="91b23-137">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="91b23-137">MinimumSameSitePolicy</span></span> | <span data-ttu-id="91b23-138">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="91b23-138">Cookie.SameSite</span></span> | <span data-ttu-id="91b23-139">Ergebnis Cookie . SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="91b23-139">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="91b23-140">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-140">SameSiteMode.None</span></span>     | <span data-ttu-id="91b23-141">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-141">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-142">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-142">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-143">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-143">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-144">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-144">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-145">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-145">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-146">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-146">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="91b23-147">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-147">SameSiteMode.Lax</span></span>      | <span data-ttu-id="91b23-148">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-148">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-149">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-149">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-150">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-150">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-151">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-151">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-152">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-152">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-153">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-153">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="91b23-154">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-154">SameSiteMode.Strict</span></span>   | <span data-ttu-id="91b23-155">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-155">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-156">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-156">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-157">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-157">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-158">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-158">SameSiteMode.Strict</span></span><br><span data-ttu-id="91b23-159">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-159">SameSiteMode.Strict</span></span><br><span data-ttu-id="91b23-160">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-160">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="91b23-161">Erstellen einer Authentifizierung cookie</span><span class="sxs-lookup"><span data-stu-id="91b23-161">Create an authentication cookie</span></span>

<span data-ttu-id="91b23-162">Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="91b23-162">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="91b23-163">Die Benutzerinformationen werden serialisiert und in gespeichert cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-163">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="91b23-164">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="91b23-164">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="91b23-165">`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="91b23-165">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="91b23-166">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-166">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="91b23-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> wird standardmäßig nur für einige bestimmte Pfade verwendet, z. b. für den Anmelde Pfad und die Abmelde Pfade.</span><span class="sxs-lookup"><span data-stu-id="91b23-167"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> is only used on a few specific paths by default, for example, the login path and logout paths.</span></span> <span data-ttu-id="91b23-168">Weitere Informationen finden Sie in der [ Cookie authenticationhandler-Quelle](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span><span class="sxs-lookup"><span data-stu-id="91b23-168">For more information see the [CookieAuthenticationHandler source](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).</span></span>

<span data-ttu-id="91b23-169">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-169">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="91b23-170">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-170">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="91b23-171">Abmelden</span><span class="sxs-lookup"><span data-stu-id="91b23-171">Sign out</span></span>

<span data-ttu-id="91b23-172">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="91b23-172">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="91b23-173">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-173">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="91b23-174">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-174">Otherwise, the default scheme is used.</span></span>

<span data-ttu-id="91b23-175">Der Server hat keine Kontrolle über den Browser des Clients.</span><span class="sxs-lookup"><span data-stu-id="91b23-175">The server has no control of the clients browser.</span></span> <span data-ttu-id="91b23-176">Wenn der Benutzer den Browser oder die Registerkarte schließt, kann der Server den Benutzer nicht abmelden.</span><span class="sxs-lookup"><span data-stu-id="91b23-176">If the user closes the browser or tab, the server cannot sign out the user.</span></span> <span data-ttu-id="91b23-177">Zum Implementieren der Abmeldung des Benutzers, wenn der Browser geschlossen wird, müssen Sie diesen mit JavaScript erkennen.</span><span class="sxs-lookup"><span data-stu-id="91b23-177">To implement signing out the user when the browser is closed, you must detect that with JavaScript.</span></span> <span data-ttu-id="91b23-178">Suchen Sie nach "Gewusst wie: Erkennen des Browser Fensters Registerkarte Close-Ereignis?".</span><span class="sxs-lookup"><span data-stu-id="91b23-178">Search for "How to Detect Browser Window Tab Close Event?".</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="91b23-179">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="91b23-179">React to back-end changes</span></span>

<span data-ttu-id="91b23-180">Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="91b23-180">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="91b23-181">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="91b23-181">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="91b23-182">Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-182">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="91b23-183">Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="91b23-183">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="91b23-184">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-184">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="91b23-185">Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="91b23-185">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="91b23-186">Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-186">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="91b23-187">Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="91b23-187">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="91b23-188">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="91b23-188">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="91b23-189">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="91b23-189">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="91b23-190">Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:</span><span class="sxs-lookup"><span data-stu-id="91b23-190">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="91b23-191">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="91b23-191">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="91b23-192">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="91b23-192">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="91b23-193">Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="91b23-193">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="91b23-194">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="91b23-194">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="91b23-195">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="91b23-195">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="91b23-196">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="91b23-196">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="91b23-197">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="91b23-197">The approach described here is triggered on every request.</span></span> <span data-ttu-id="91b23-198">cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="91b23-198">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="91b23-199">Persistente cookie s</span><span class="sxs-lookup"><span data-stu-id="91b23-199">Persistent cookies</span></span>

<span data-ttu-id="91b23-200">Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="91b23-200">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="91b23-201">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-201">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="91b23-202">Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-202">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="91b23-203">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="91b23-203">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="91b23-204">Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="91b23-204">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="91b23-205"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="91b23-205">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="91b23-206">Absoluter cookie Ablauf</span><span class="sxs-lookup"><span data-stu-id="91b23-206">Absolute cookie expiration</span></span>

<span data-ttu-id="91b23-207">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="91b23-207">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="91b23-208">Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-208">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="91b23-209">Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="91b23-209">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="91b23-210">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="91b23-210">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="91b23-211">Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert.</span><span class="sxs-lookup"><span data-stu-id="91b23-211">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="91b23-212">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="91b23-212">This ignores any sliding expiration settings previously configured.</span></span>

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

<span data-ttu-id="91b23-213">ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen.</span><span class="sxs-lookup"><span data-stu-id="91b23-213">ASP.NET Core Identity is a complete, full-featured authentication provider for creating and maintaining logins.</span></span> <span data-ttu-id="91b23-214">Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-214">However, a cookie-based authentication provider without ASP.NET Core Identity can be used.</span></span> <span data-ttu-id="91b23-215">Weitere Informationen finden Sie unter <xref:security/authentication/identity>.</span><span class="sxs-lookup"><span data-stu-id="91b23-215">For more information, see <xref:security/authentication/identity>.</span></span>

<span data-ttu-id="91b23-216">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="91b23-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="91b23-217">Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert.</span><span class="sxs-lookup"><span data-stu-id="91b23-217">For demonstration purposes in the sample app, the user account for the hypothetical user, Maria Rodriguez, is hardcoded into the app.</span></span> <span data-ttu-id="91b23-218">Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden.</span><span class="sxs-lookup"><span data-stu-id="91b23-218">Use the **Email** address `maria.rodriguez@contoso.com` and any password to sign in the user.</span></span> <span data-ttu-id="91b23-219">Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="91b23-219">The user is authenticated in the `AuthenticateUser` method in the *Pages/Account/Login.cshtml.cs* file.</span></span> <span data-ttu-id="91b23-220">In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="91b23-220">In a real-world example, the user would be authenticated against a database.</span></span>

## <a name="configuration"></a><span data-ttu-id="91b23-221">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="91b23-221">Configuration</span></span>

<span data-ttu-id="91b23-222">Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für die Datei [Microsoft. aspnetcore. Authentication. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) -Paket.</span><span class="sxs-lookup"><span data-stu-id="91b23-222">If the app doesn't use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), create a package reference in the project file for the [Microsoft.AspNetCore.Authentication.Cookies](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) package.</span></span>

<span data-ttu-id="91b23-223">Erstellen Sie in der `Startup.ConfigureServices` -Methode den Authentifizierungs-Middleware-Dienst mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden und:</span><span class="sxs-lookup"><span data-stu-id="91b23-223">In the `Startup.ConfigureServices` method, create the Authentication Middleware service with the <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> and <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> methods:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<span data-ttu-id="91b23-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="91b23-224"><xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> passed to `AddAuthentication` sets the default authentication scheme for the app.</span></span> <span data-ttu-id="91b23-225">`AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten.</span><span class="sxs-lookup"><span data-stu-id="91b23-225">`AuthenticationScheme` is useful when there are multiple instances of cookie authentication and you want to [authorize with a specific scheme](xref:security/authorization/limitingidentitybyscheme).</span></span> <span data-ttu-id="91b23-226">Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="91b23-226">Setting the `AuthenticationScheme` to [CookieAuthenticationDefaults.AuthenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) provides a value of "Cookies" for the scheme.</span></span> <span data-ttu-id="91b23-227">Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="91b23-227">You can supply any string value that distinguishes the scheme.</span></span>

<span data-ttu-id="91b23-228">Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app.</span><span class="sxs-lookup"><span data-stu-id="91b23-228">The app's authentication scheme is different from the app's cookie authentication scheme.</span></span> <span data-ttu-id="91b23-229">Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").</span><span class="sxs-lookup"><span data-stu-id="91b23-229">When a cookie authentication scheme isn't provided to <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*>, it uses `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="91b23-230">Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` .</span><span class="sxs-lookup"><span data-stu-id="91b23-230">The authentication cookie's <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> property is set to `true` by default.</span></span> <span data-ttu-id="91b23-231">Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat.</span><span class="sxs-lookup"><span data-stu-id="91b23-231">Authentication cookies are allowed when a site visitor hasn't consented to data collection.</span></span> <span data-ttu-id="91b23-232">Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.</span><span class="sxs-lookup"><span data-stu-id="91b23-232">For more information, see <xref:security/gdpr#essential-cookies>.</span></span>

<span data-ttu-id="91b23-233">Rufen Sie in der- `Startup.Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt.</span><span class="sxs-lookup"><span data-stu-id="91b23-233">In the `Startup.Configure` method, call the `UseAuthentication` method to invoke the Authentication Middleware that sets the `HttpContext.User` property.</span></span> <span data-ttu-id="91b23-234">Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :</span><span class="sxs-lookup"><span data-stu-id="91b23-234">Call the `UseAuthentication` method before calling `UseMvcWithDefaultRoute` or `UseMvc`:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

<span data-ttu-id="91b23-235">Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="91b23-235">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> class is used to configure the authentication provider options.</span></span>

<span data-ttu-id="91b23-236">Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:</span><span class="sxs-lookup"><span data-stu-id="91b23-236">Set `CookieAuthenticationOptions` in the service configuration for authentication in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a><span data-ttu-id="91b23-237">Cookie Richtlinien Middleware</span><span class="sxs-lookup"><span data-stu-id="91b23-237">Cookie Policy Middleware</span></span>

<span data-ttu-id="91b23-238">Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen.</span><span class="sxs-lookup"><span data-stu-id="91b23-238">[Cookie Policy Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) enables cookie policy capabilities.</span></span> <span data-ttu-id="91b23-239">Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.</span><span class="sxs-lookup"><span data-stu-id="91b23-239">Adding the middleware to the app processing pipeline is order sensitive&mdash;it only affects downstream components registered in the pipeline.</span></span>

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

<span data-ttu-id="91b23-240">Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-240">Use <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> provided to the Cookie Policy Middleware to control global characteristics of cookie processing and hook into cookie processing handlers when cookies are appended or deleted.</span></span>

<span data-ttu-id="91b23-241">Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="91b23-241">The default <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> value is `SameSiteMode.Lax` to permit OAuth2 authentication.</span></span> <span data-ttu-id="91b23-242">Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` .</span><span class="sxs-lookup"><span data-stu-id="91b23-242">To strictly enforce a same-site policy of `SameSiteMode.Strict`, set the `MinimumSameSitePolicy`.</span></span> <span data-ttu-id="91b23-243">Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.</span><span class="sxs-lookup"><span data-stu-id="91b23-243">Although this setting breaks OAuth2 and other cross-origin authentication schemes, it elevates the level of cookie security for other types of apps that don't rely on cross-origin request processing.</span></span>

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

<span data-ttu-id="91b23-244">Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.</span><span class="sxs-lookup"><span data-stu-id="91b23-244">The Cookie Policy Middleware setting for `MinimumSameSitePolicy` can affect the setting of `Cookie.SameSite` in `CookieAuthenticationOptions` settings according to the matrix below.</span></span>

| <span data-ttu-id="91b23-245">Minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="91b23-245">MinimumSameSitePolicy</span></span> | <span data-ttu-id="91b23-246">Cookie. SameSite</span><span class="sxs-lookup"><span data-stu-id="91b23-246">Cookie.SameSite</span></span> | <span data-ttu-id="91b23-247">Ergebnis Cookie . SameSite-Einstellung</span><span class="sxs-lookup"><span data-stu-id="91b23-247">Resultant Cookie.SameSite setting</span></span> |
| --------------------- | --------------- | --------------------------------- |
| <span data-ttu-id="91b23-248">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-248">SameSiteMode.None</span></span>     | <span data-ttu-id="91b23-249">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-249">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-250">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-250">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-251">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-251">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-252">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-252">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-253">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-253">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-254">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-254">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="91b23-255">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-255">SameSiteMode.Lax</span></span>      | <span data-ttu-id="91b23-256">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-256">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-257">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-257">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-258">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-258">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-259">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-259">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-260">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-260">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-261">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-261">SameSiteMode.Strict</span></span> |
| <span data-ttu-id="91b23-262">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-262">SameSiteMode.Strict</span></span>   | <span data-ttu-id="91b23-263">Samesitemode. None</span><span class="sxs-lookup"><span data-stu-id="91b23-263">SameSiteMode.None</span></span><br><span data-ttu-id="91b23-264">Samesitemode. Lax</span><span class="sxs-lookup"><span data-stu-id="91b23-264">SameSiteMode.Lax</span></span><br><span data-ttu-id="91b23-265">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-265">SameSiteMode.Strict</span></span> | <span data-ttu-id="91b23-266">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-266">SameSiteMode.Strict</span></span><br><span data-ttu-id="91b23-267">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-267">SameSiteMode.Strict</span></span><br><span data-ttu-id="91b23-268">Samesitemode. Strict</span><span class="sxs-lookup"><span data-stu-id="91b23-268">SameSiteMode.Strict</span></span> |

## <a name="create-an-authentication-no-loccookie"></a><span data-ttu-id="91b23-269">Erstellen einer Authentifizierung cookie</span><span class="sxs-lookup"><span data-stu-id="91b23-269">Create an authentication cookie</span></span>

<span data-ttu-id="91b23-270">Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> .</span><span class="sxs-lookup"><span data-stu-id="91b23-270">To create a cookie holding user information, construct a <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="91b23-271">Die Benutzerinformationen werden serialisiert und in gespeichert cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-271">The user information is serialized and stored in the cookie.</span></span> 

<span data-ttu-id="91b23-272">Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:</span><span class="sxs-lookup"><span data-stu-id="91b23-272">Create a <xref:System.Security.Claims.ClaimsIdentity> with any required <xref:System.Security.Claims.Claim>s and call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> to sign in the user:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

<span data-ttu-id="91b23-273">`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu.</span><span class="sxs-lookup"><span data-stu-id="91b23-273">`SignInAsync` creates an encrypted cookie and adds it to the current response.</span></span> <span data-ttu-id="91b23-274">Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-274">If `AuthenticationScheme` isn't specified, the default scheme is used.</span></span>

<span data-ttu-id="91b23-275">ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-275">ASP.NET Core's [Data Protection](xref:security/data-protection/using-data-protection) system is used for encryption.</span></span> <span data-ttu-id="91b23-276">Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-276">For an app hosted on multiple machines, load balancing across apps, or using a web farm, [configure data protection](xref:security/data-protection/configuration/overview) to use the same key ring and app identifier.</span></span>

## <a name="sign-out"></a><span data-ttu-id="91b23-277">Abmelden</span><span class="sxs-lookup"><span data-stu-id="91b23-277">Sign out</span></span>

<span data-ttu-id="91b23-278">Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :</span><span class="sxs-lookup"><span data-stu-id="91b23-278">To sign out the current user and delete their cookie, call <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>:</span></span>

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

<span data-ttu-id="91b23-279">Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-279">If `CookieAuthenticationDefaults.AuthenticationScheme` (or "Cookies") isn't used as the scheme (for example, "ContosoCookie"), supply the scheme used when configuring the authentication provider.</span></span> <span data-ttu-id="91b23-280">Andernfalls wird das Standardschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="91b23-280">Otherwise, the default scheme is used.</span></span>

## <a name="react-to-back-end-changes"></a><span data-ttu-id="91b23-281">Reagieren auf Back-End-Änderungen</span><span class="sxs-lookup"><span data-stu-id="91b23-281">React to back-end changes</span></span>

<span data-ttu-id="91b23-282">Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität.</span><span class="sxs-lookup"><span data-stu-id="91b23-282">Once a cookie is created, the cookie is the single source of identity.</span></span> <span data-ttu-id="91b23-283">Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:</span><span class="sxs-lookup"><span data-stu-id="91b23-283">If a user account is disabled in back-end systems:</span></span>

* <span data-ttu-id="91b23-284">Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-284">The app's cookie authentication system continues to process requests based on the authentication cookie.</span></span>
* <span data-ttu-id="91b23-285">Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.</span><span class="sxs-lookup"><span data-stu-id="91b23-285">The user remains signed into the app as long as the authentication cookie is valid.</span></span>

<span data-ttu-id="91b23-286">Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie .</span><span class="sxs-lookup"><span data-stu-id="91b23-286">The <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> event can be used to intercept and override validation of the cookie identity.</span></span> <span data-ttu-id="91b23-287">Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.</span><span class="sxs-lookup"><span data-stu-id="91b23-287">Validating the cookie on every request mitigates the risk of revoked users accessing the app.</span></span>

<span data-ttu-id="91b23-288">Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-288">One approach to cookie validation is based on keeping track of when the user database changes.</span></span> <span data-ttu-id="91b23-289">Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist.</span><span class="sxs-lookup"><span data-stu-id="91b23-289">If the database hasn't been changed since the user's cookie was issued, there's no need to re-authenticate the user if their cookie is still valid.</span></span> <span data-ttu-id="91b23-290">In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert.</span><span class="sxs-lookup"><span data-stu-id="91b23-290">In the sample app, the database is implemented in `IUserRepository` and stores a `LastChanged` value.</span></span> <span data-ttu-id="91b23-291">Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="91b23-291">When a user is updated in the database, the `LastChanged` value is set to the current time.</span></span>

<span data-ttu-id="91b23-292">Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:</span><span class="sxs-lookup"><span data-stu-id="91b23-292">In order to invalidate a cookie when the database changes based on the `LastChanged` value, create the cookie with a `LastChanged` claim containing the current `LastChanged` value from the database:</span></span>

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

<span data-ttu-id="91b23-293">Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :</span><span class="sxs-lookup"><span data-stu-id="91b23-293">To implement an override for the `ValidatePrincipal` event, write a method with the following signature in a class that derives from <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents>:</span></span>

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

<span data-ttu-id="91b23-294">Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :</span><span class="sxs-lookup"><span data-stu-id="91b23-294">The following is an example implementation of `CookieAuthenticationEvents`:</span></span>

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

<span data-ttu-id="91b23-295">Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="91b23-295">Register the events instance during cookie service registration in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="91b23-296">Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:</span><span class="sxs-lookup"><span data-stu-id="91b23-296">Provide a [scoped service registration](xref:fundamentals/dependency-injection#service-lifetimes) for your `CustomCookieAuthenticationEvents` class:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

<span data-ttu-id="91b23-297">Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt.</span><span class="sxs-lookup"><span data-stu-id="91b23-297">Consider a situation in which the user's name is updated&mdash;a decision that doesn't affect security in any way.</span></span> <span data-ttu-id="91b23-298">Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .</span><span class="sxs-lookup"><span data-stu-id="91b23-298">If you want to non-destructively update the user principal, call `context.ReplacePrincipal` and set the `context.ShouldRenew` property to `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="91b23-299">Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="91b23-299">The approach described here is triggered on every request.</span></span> <span data-ttu-id="91b23-300">cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.</span><span class="sxs-lookup"><span data-stu-id="91b23-300">Validating authentication cookies for all users on every request can result in a large performance penalty for the app.</span></span>

## <a name="persistent-no-loccookies"></a><span data-ttu-id="91b23-301">Persistente cookie s</span><span class="sxs-lookup"><span data-stu-id="91b23-301">Persistent cookies</span></span>

<span data-ttu-id="91b23-302">Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll.</span><span class="sxs-lookup"><span data-stu-id="91b23-302">You may want the cookie to persist across browser sessions.</span></span> <span data-ttu-id="91b23-303">Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-303">This persistence should only be enabled with explicit user consent with a "Remember Me" check box on sign in or a similar mechanism.</span></span> 

<span data-ttu-id="91b23-304">Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="91b23-304">The following code snippet creates an identity and corresponding cookie that survives through browser closures.</span></span> <span data-ttu-id="91b23-305">Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="91b23-305">Any sliding expiration settings previously configured are honored.</span></span> <span data-ttu-id="91b23-306">Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="91b23-306">If the cookie expires while the browser is closed, the browser clears the cookie once it's restarted.</span></span>

<span data-ttu-id="91b23-307"><xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :</span><span class="sxs-lookup"><span data-stu-id="91b23-307">Set <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent> to `true` in <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties>:</span></span>

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

## <a name="absolute-no-loccookie-expiration"></a><span data-ttu-id="91b23-308">Absoluter cookie Ablauf</span><span class="sxs-lookup"><span data-stu-id="91b23-308">Absolute cookie expiration</span></span>

<span data-ttu-id="91b23-309">Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> .</span><span class="sxs-lookup"><span data-stu-id="91b23-309">An absolute expiration time can be set with <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc>.</span></span> <span data-ttu-id="91b23-310">Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="91b23-310">To create a persistent cookie, `IsPersistent` must also be set.</span></span> <span data-ttu-id="91b23-311">Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält.</span><span class="sxs-lookup"><span data-stu-id="91b23-311">Otherwise, the cookie is created with a session-based lifetime and could expire either before or after the authentication ticket that it holds.</span></span> <span data-ttu-id="91b23-312">Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.</span><span class="sxs-lookup"><span data-stu-id="91b23-312">When `ExpiresUtc` is set, it overrides the value of the <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> option of <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions>, if set.</span></span>

<span data-ttu-id="91b23-313">Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert.</span><span class="sxs-lookup"><span data-stu-id="91b23-313">The following code snippet creates an identity and corresponding cookie that lasts for 20 minutes.</span></span> <span data-ttu-id="91b23-314">Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.</span><span class="sxs-lookup"><span data-stu-id="91b23-314">This ignores any sliding expiration settings previously configured.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="91b23-315">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="91b23-315">Additional resources</span></span>

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [<span data-ttu-id="91b23-316">Richtlinien basierte Rollen Überprüfungen</span><span class="sxs-lookup"><span data-stu-id="91b23-316">Policy-based role checks</span></span>](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
