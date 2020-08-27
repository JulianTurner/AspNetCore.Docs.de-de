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
ms.openlocfilehash: 24ba49828db08fdd67723c81ac0c8d9981ab3404
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945414"
---
# <a name="use-no-loccookie-authentication-without-no-locaspnet-core-identity"></a>Verwenden der cookie Authentifizierung ohne ASP.NET Core Identity

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen. Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden. Weitere Informationen finden Sie unter <xref:security/authentication/identity>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert. Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden. Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert. In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.

## <a name="configuration"></a>Konfiguration

Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für die Datei [Microsoft. aspnetcore. Authentication. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) -Paket.

Erstellen Sie in der `Startup.ConfigureServices` -Methode die Authentifizierungs-Middleware-Dienste mit der <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> -Methode und der-Methode <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten. Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt. Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.

Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app. Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` . Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

In `Startup.Configure` werden `UseAuthentication` und aufgerufen `UseAuthorization` , um die-Eigenschaft festzulegen `HttpContext.User` und die Autorisierungs Middleware für Anforderungen auszuführen. Rufen Sie `UseAuthentication` die `UseAuthorization` Methoden und vor, bevor Sie aufrufen `UseEndpoints` :

[!code-csharp[](cookie/samples/3.x/CookieSample/Startup.cs?name=snippet2)]

Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.

Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Richtlinien Middleware

Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen. Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.

Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen. Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` . Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.

| Minimumsamesitepolicy | Cookie. SameSite | Ergebnis Cookie . SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| Samesitemode. None     | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict |
| Samesitemode. Lax      | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. Lax<br>Samesitemode. Lax<br>Samesitemode. Strict |
| Samesitemode. Strict   | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. Strict<br>Samesitemode. Strict<br>Samesitemode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Erstellen einer Authentifizierung cookie

Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> . Die Benutzerinformationen werden serialisiert und in gespeichert cookie . 

Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu. Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.RedirectUri> wird standardmäßig nur für einige bestimmte Pfade verwendet, z. b. für den Anmelde Pfad und die Abmelde Pfade. Weitere Informationen finden Sie in der [ Cookie authenticationhandler-Quelle](https://github.com/dotnet/aspnetcore/blob/f2e6e6ff334176540ef0b3291122e359c2106d1a/src/Security/Authentication/Cookies/src/CookieAuthenticationHandler.cs#L334).

ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet. Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/3.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird. Andernfalls wird das Standardschema verwendet.

Wenn der Browser geschlossen cookie wird, werden Sitzungs basierte s (nicht persistente cookie e) automatisch gelöscht, aber keine cookie s, wenn eine einzelne Registerkarte geschlossen wird. Der Server wird nicht über Registerkarten-oder Browser schließen-Ereignisse benachrichtigt.

## <a name="react-to-back-end-changes"></a>Reagieren auf Back-End-Änderungen

Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität. Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:

* Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .
* Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.

Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie . Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.

Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird. Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist. In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert. Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.

Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:

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

Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :

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

Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode. Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt. Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .

> [!WARNING]
> Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst. cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.

## <a name="persistent-no-loccookies"></a>Persistente cookie s

Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll. Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden. 

Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird. Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt. Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>Absoluter cookie Ablauf

Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden. Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält. Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.

Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert. Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.

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

ASP.NET Core Identity ist ein vollständiger Authentifizierungs Anbieter mit vollem Funktionsumfang zum Erstellen und warten von Anmeldungen. Allerdings cookie kann ein-basierter Authentifizierungs Anbieter ohne ASP.NET Core Identity verwendet werden. Weitere Informationen finden Sie unter <xref:security/authentication/identity>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/cookie/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zu Demonstrationszwecken in der Beispiel-APP ist das Benutzerkonto für den hypothetischen Benutzer Maria Rodriguez in der APP hart codiert. Verwenden Sie die **e-Mail-** Adresse `maria.rodriguez@contoso.com` und jedes Kennwort, um den Benutzer anzumelden. Der Benutzer wird in der `AuthenticateUser` -Methode in der Datei *pages/Account/Login. cshtml. cs* authentifiziert. In einem realen Beispiel wird der Benutzer für eine-Datenbank authentifiziert.

## <a name="configuration"></a>Konfiguration

Wenn die APP das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)nicht verwendet, erstellen Sie einen Paket Verweis in der Projektdatei für die Datei [Microsoft. aspnetcore. Authentication. Cookie s](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Cookies/) -Paket.

Erstellen Sie in der `Startup.ConfigureServices` -Methode den Authentifizierungs-Middleware-Dienst mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden und:

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme> wird an `AddAuthentication` das Standard Authentifizierungsschema für die APP festgelegt. `AuthenticationScheme` ist nützlich, wenn mehrere Instanzen der Authentifizierung vorhanden sind cookie und Sie die [Autorisierung mit einem bestimmten Schema durch](xref:security/authorization/limitingidentitybyscheme)laufen möchten. Durch Festlegen `AuthenticationScheme` von auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) wird der Wert " Cookie s" für das Schema bereitstellt. Sie können einen beliebigen Zeichen folgen Wert angeben, der das Schema unterscheidet.

Das Authentifizierungsschema der APP unterscheidet sich vom cookie Authentifizierungsschema der app. Wenn kein cookie Authentifizierungsschema für bereitgestellt <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> wird, verwendet es `CookieAuthenticationDefaults.AuthenticationScheme` (" Cookie s").

Die cookie -Eigenschaft der Authentifizierung <xref:Microsoft.AspNetCore.Http.CookieBuilder.IsEssential> ist standardmäßig auf festgelegt `true` . Authentifizierungs- cookie e sind zulässig, wenn ein Website Besucher der Datensammlung nicht zugestimmt hat. Weitere Informationen finden Sie unter <xref:security/gdpr#essential-cookies>.

Rufen Sie in der- `Startup.Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt. Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :

[!code-csharp[](cookie/samples/2.x/CookieSample/Startup.cs?name=snippet2)]

Die- <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationOptions> Klasse wird verwendet, um die Authentifizierungs Anbieter Optionen zu konfigurieren.

Legen Sie `CookieAuthenticationOptions` in der Dienst Konfiguration für die Authentifizierung in der- `Startup.ConfigureServices` Methode fest:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        ...
    });
```

## <a name="no-loccookie-policy-middleware"></a>Cookie Richtlinien Middleware

Die [ Cookie Richtlinien Middleware](xref:Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware) ermöglicht cookie Richtlinien Funktionen. Beim Hinzufügen der Middleware zur APP-Verarbeitungs Pipeline ist die Reihenfolge vertraulich, dass &mdash; Sie nur die in der Pipeline registrierten Downstreamkomponenten betrifft.

```csharp
app.UseCookiePolicy(cookiePolicyOptions);
```

Verwenden <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions> Sie für die Cookie Richtlinien Middleware, um globale Merkmale der Verarbeitung zu steuern, cookie und schließen Sie cookie Verarbeitungs Handler ein, wenn e angefügt cookie oder gelöscht werden.

Der Standard <xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy> Wert ist `SameSiteMode.Lax` , um die OAuth2-Authentifizierung zuzulassen. Um eine Richtlinie für denselben Standort von streng zu erzwingen `SameSiteMode.Strict` , legen Sie den fest `MinimumSameSitePolicy` . Obwohl diese Einstellung OAuth2 und andere Ursprungs übergreifende Authentifizierungs Schemas unterbricht, erhöht Sie die Sicherheitsstufe cookie für andere Typen von apps, die sich nicht auf die Verarbeitung von Ursprungs übergreifenden Anforderungen stützen.

```csharp
var cookiePolicyOptions = new CookiePolicyOptions
{
    MinimumSameSitePolicy = SameSiteMode.Strict,
};
```

Die Cookie Einstellung der Richtlinien Middleware für `MinimumSameSitePolicy` kann die Einstellung von `Cookie.SameSite` in den `CookieAuthenticationOptions` Einstellungen entsprechend der folgenden Matrix beeinflussen.

| Minimumsamesitepolicy | Cookie. SameSite | Ergebnis Cookie . SameSite-Einstellung |
| --------------------- | --------------- | --------------------------------- |
| Samesitemode. None     | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict |
| Samesitemode. Lax      | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. Lax<br>Samesitemode. Lax<br>Samesitemode. Strict |
| Samesitemode. Strict   | Samesitemode. None<br>Samesitemode. Lax<br>Samesitemode. Strict | Samesitemode. Strict<br>Samesitemode. Strict<br>Samesitemode. Strict |

## <a name="create-an-authentication-no-loccookie"></a>Erstellen einer Authentifizierung cookie

Erstellen Sie eine, um eine zu erstellen cookie , die Benutzerinformationen enthält <xref:System.Security.Claims.ClaimsPrincipal> . Die Benutzerinformationen werden serialisiert und in gespeichert cookie . 

Erstellen <xref:System.Security.Claims.ClaimsIdentity> Sie mit allen erforderlichen <xref:System.Security.Claims.Claim> s, und rufen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*> Sie auf, um den Benutzer anzumelden:

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet1)]

`SignInAsync` erstellt eine verschlüsselte cookie und fügt Sie der aktuellen Antwort hinzu. Wenn `AuthenticationScheme` nicht angegeben ist, wird das Standardschema verwendet.

ASP.net Core- [Datenschutz](xref:security/data-protection/using-data-protection) System wird für die Verschlüsselung verwendet. Konfigurieren Sie für eine APP, die auf mehreren Computern gehostet wird, den Lastenausgleich über apps oder eine Webfarm, den [Datenschutz](xref:security/data-protection/configuration/overview) so, dass derselbe Schlüsselbund und der APP-Bezeichner verwendet werden.

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und dessen zu löschen, wenden Sie Folgendes an cookie <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*> :

[!code-csharp[](cookie/samples/2.x/CookieSample/Pages/Account/Login.cshtml.cs?name=snippet2)]

Wenn `CookieAuthenticationDefaults.AuthenticationScheme` (oder " Cookie s") nicht als Schema verwendet wird (z. b. "Configuration Manager" Cookie ), geben Sie das Schema an, das beim Konfigurieren des Authentifizierungs Anbieters verwendet wird. Andernfalls wird das Standardschema verwendet.

## <a name="react-to-back-end-changes"></a>Reagieren auf Back-End-Änderungen

Nachdem ein cookie erstellt wurde, cookie ist die einzige Quelle der Identität. Wenn ein Benutzerkonto in Back-End-Systemen deaktiviert ist:

* Das cookie Authentifizierungssystem der APP verarbeitet weiterhin Anforderungen auf der Grundlage der Authentifizierung cookie .
* Der Benutzer bleibt bei der App angemeldet, solange die Authentifizierung cookie gültig ist.

Das <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents.ValidatePrincipal*> -Ereignis kann verwendet werden, um die Validierung der Identität abzufangen und zu überschreiben cookie . Das Überprüfen von cookie bei jeder Anforderung verringert das Risiko, dass widerrufene Benutzer auf die App zugreifen.

Ein Ansatz zur cookie Validierung basiert auf der Verfolgung, wann die Benutzerdatenbank geändert wird. Wenn die Datenbank nicht geändert wurde, seit Sie cookie ausgestellt wurde, ist es nicht erforderlich, den Benutzer erneut zu authentifizieren, wenn der Benutzer cookie noch gültig ist. In der Beispiel-APP wird die Datenbank in implementiert `IUserRepository` und speichert einen `LastChanged` Wert. Wenn ein Benutzer in der Datenbank aktualisiert wird, `LastChanged` wird der Wert auf die aktuelle Zeit festgelegt.

Um eine für ungültig zu erklären cookie , wenn sich die Datenbank auf der Grundlage des- `LastChanged` Werts ändert, erstellen Sie cookie mit einem Anspruch, der `LastChanged` den aktuellen `LastChanged` Wert aus der Datenbank enthält:

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

Um eine außer Kraft setzung für das-Ereignis zu implementieren `ValidatePrincipal` , schreiben Sie eine-Methode mit der folgenden Signatur in eine Klasse, die von abgeleitet ist <xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationEvents> :

```csharp
ValidatePrincipal(CookieValidatePrincipalContext)
```

Im folgenden finden Sie eine Beispiel Implementierung von `CookieAuthenticationEvents` :

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

Registrieren Sie die Ereignis Instanz während cookie der Dienst Registrierung in der- `Startup.ConfigureServices` Methode. Stellen Sie eine Bereichs bezogene [Dienst Registrierung](xref:fundamentals/dependency-injection#service-lifetimes) für die `CustomCookieAuthenticationEvents` Klasse bereit:

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.EventsType = typeof(CustomCookieAuthenticationEvents);
    });

services.AddScoped<CustomCookieAuthenticationEvents>();
```

Stellen Sie sich eine Situation vor, in der der Name des Benutzers &mdash; eine Entscheidung aktualisiert, die die Sicherheit in keiner Weise beeinträchtigt. Wenn Sie den Benutzer Prinzipal nicht dedestruktiv aktualisieren möchten, müssen Sie aufrufen `context.ReplacePrincipal` und die- `context.ShouldRenew` Eigenschaft auf festlegen `true` .

> [!WARNING]
> Der hier beschriebene Ansatz wird bei jeder Anforderung ausgelöst. cookieDas Überprüfen der Authentifizierungs-e für alle Benutzer bei jeder Anforderung kann zu einer hohen Leistungs Einbuße für die APP führen.

## <a name="persistent-no-loccookies"></a>Persistente cookie s

Es kann sein, dass die cookie über Browsersitzungen hinweg beibehalten werden soll. Diese Persistenz sollte nur mit der expliziten Zustimmung des Benutzers mit dem Kontrollkästchen "merken" bei der Anmeldung oder einem ähnlichen Mechanismus aktiviert werden. 

Mit dem folgenden Code Ausschnitt werden eine Identität und eine entsprechende Identität erstellt cookie , die bei Browser Abschlüssen nicht angezeigt wird. Alle zuvor konfigurierten gleitenden Ablauf Einstellungen werden berücksichtigt. Wenn das cookie abläuft, während der Browser geschlossen wird, löscht der Browser den, cookie nachdem er neu gestartet wurde.

<xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.IsPersistent>Auf `true` in festlegen <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties> :

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

## <a name="absolute-no-loccookie-expiration"></a>Absoluter cookie Ablauf

Eine absolute Ablaufzeit kann mit festgelegt werden <xref:Microsoft.AspNetCore.Authentication.AuthenticationProperties.ExpiresUtc> . Zum Erstellen eines persistenten cookie `IsPersistent` muss ebenfalls festgelegt werden. Andernfalls wird das cookie mit einer Sitzungs basierten Lebensdauer erstellt und kann entweder vor oder nach dem Authentifizierungs Ticket ablaufen, das es enthält. Wenn `ExpiresUtc` festgelegt ist, wird der Wert der- <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.ExpireTimeSpan> Option von <xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions> , sofern festgelegt, überschrieben.

Der folgende Code Ausschnitt erstellt eine Identität und entsprechende cookie , die 20 Minuten dauert. Dadurch werden alle zuvor konfigurierten gleitenden Ablauf Einstellungen ignoriert.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/limitingidentitybyscheme>
* <xref:security/authorization/claims>
* [Richtlinien basierte Rollen Überprüfungen](xref:security/authorization/roles#policy-based-role-checks)
* <xref:host-and-deploy/web-farm>
