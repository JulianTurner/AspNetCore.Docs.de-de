---
title: Authentifizierung mit Facebook, Google und externem Anbieter ohne ASP.NET Core Identity
author: rick-anderson
description: Eine Erläuterung der Verwendung von Facebook, Google, Twitter usw. Kontobenutzer Authentifizierung ohne ASP.NET Core Identity .
ms.author: riande
ms.date: 12/10/2019
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
uid: security/authentication/social/social-without-identity
ms.openlocfilehash: cd7545a3ddaccedfa64ef5e9d5458c21c651257a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060286"
---
# <a name="use-social-sign-in-provider-authentication-without-no-locaspnet-core-identity"></a>Verwenden Sie die Authentifizierung für den sozialen Anmelde Anbieter ohne ASP.NET Core Identity

Von [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<xref:security/authentication/social/index> Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.NET Core Identity als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** verwendet wird ASP.NET Core Identity . Dies ist nützlich für apps, die nicht alle Features von benötigen ASP.NET Core Identity , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> <xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*> Methoden, und <xref:Microsoft.Extensions.DependencyInjection.GoogleExtensions.AddGoogle*> :

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet1)]

Der-Befehl ruft <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> die der APP auf <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme> . Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn Sie die APP `DefaultScheme` auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") festlegen, wird die APP so konfiguriert, Cookie dass Sie s als Standardschema für diese Erweiterungs Methoden verwendet. Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` . `DefaultChallengeScheme` überschreibt `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .

`Startup.Configure`Rufen Sie in `UseAuthentication` und zwischen dem Aufruf `UseAuthorization` von und auf `UseRouting` `UseEndpoints` . Dadurch wird die `HttpContext.User` -Eigenschaft festgelegt und die Autorisierungs Middleware für Anforderungen ausgeführt:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Startup.cs?name=snippet2&highlight=3-4)]

Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts). Weitere Informationen zur cookie Authentifizierung finden Sie unter <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und dessen zu löschen, müssen Sie cookie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/3.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt. Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<xref:security/authentication/social/index> Beschreibt, wie Sie es Benutzern ermöglichen können, sich mithilfe von OAuth 2,0 mit Anmelde Informationen von externen Authentifizierungs Anbietern anzumelden. Die in diesem Thema beschriebene Vorgehensweise umfasst ASP.NET Core Identity als Authentifizierungs Anbieter.

In diesem Beispiel wird veranschaulicht, wie ein externer Authentifizierungs Anbieter **ohne** verwendet wird ASP.NET Core Identity . Dies ist nützlich für apps, die nicht alle Features von benötigen ASP.NET Core Identity , aber trotzdem eine Integration in einen vertrauenswürdigen externen Authentifizierungs Anbieter erfordern.

In diesem Beispiel wird die [Google-Authentifizierung](xref:security/authentication/google-logins) zum Authentifizieren von Benutzern verwendet. Die Verwendung der Google-Authentifizierung verlagert viele der Komplexitäten bei der Verwaltung des Anmeldevorgangs an Google. Informationen zur Integration in einen anderen externen Authentifizierungs Anbieter finden Sie in den folgenden Themen:

* [Facebook-Authentifizierung](xref:security/authentication/facebook-logins)
* [Microsoft-Authentifizierung](xref:security/authentication/microsoft-logins)
* [Twitter-Authentifizierung](xref:security/authentication/twitter-logins)
* [Andere Anbieter](xref:security/authentication/otherlogins)

## <a name="configuration"></a>Konfiguration

Konfigurieren Sie in der `ConfigureServices` -Methode die Authentifizierungs Schemas der APP mit den `AddAuthentication` `AddCookie` Methoden, und `AddGoogle` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet1)]

Durch den [addauthentication](/dotnet/api/microsoft.extensions.dependencyinjection.authenticationservicecollectionextensions.addauthentication#Microsoft_Extensions_DependencyInjection_AuthenticationServiceCollectionExtensions_AddAuthentication_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_AuthenticationOptions__) -Rückruf wird das [defaultscheme](xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultScheme)der APP festgelegt. Das `DefaultScheme` ist das Standardschema, das von den folgenden `HttpContext` Authentifizierungs Erweiterungs Methoden verwendet wird:

* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.AuthenticateAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ChallengeAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.ForbidAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignInAsync*>
* <xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*>

Wenn Sie die APP `DefaultScheme` auf [ Cookie authenticationdefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationDefaults.AuthenticationScheme) (" Cookie s") festlegen, wird die APP so konfiguriert, Cookie dass Sie s als Standardschema für diese Erweiterungs Methoden verwendet. Wenn Sie die APP <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions.DefaultChallengeScheme> auf [googledefaults. authenticationScheme](xref:Microsoft.AspNetCore.Authentication.Google.GoogleDefaults.AuthenticationScheme) ("Google") festlegen, wird die APP für die Verwendung von Google als Standardschema für Aufrufe von konfiguriert `ChallengeAsync` . `DefaultChallengeScheme` überschreibt `DefaultScheme` . <xref:Microsoft.AspNetCore.Authentication.AuthenticationOptions>Weitere Eigenschaften, die bei Festlegung überschreiben, finden Sie unter `DefaultScheme` .

Rufen Sie in der- `Configure` Methode die- `UseAuthentication` Methode auf, um die Authentifizierungs Middleware aufzurufen, die die- `HttpContext.User` Eigenschaft festlegt. Rufen Sie die-Methode auf, `UseAuthentication` bevor Sie `UseMvcWithDefaultRoute` oder aufrufen `UseMvc` :

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Startup.cs?name=snippet2)]

Weitere Informationen zu Authentifizierungs Schemas finden Sie unter [Authentifizierungs Konzepte](xref:security/authentication/index#authentication-concepts). Weitere Informationen zur cookie Authentifizierung finden Sie unter <xref:security/authentication/cookie> .

## <a name="apply-authorization"></a>Autorisierung anwenden

Testen Sie die Authentifizierungs Konfiguration der APP, indem `AuthorizeAttribute` Sie das-Attribut auf einen Controller, eine Aktion oder eine Seite anwenden. Der folgende Code schränkt den Zugriff auf die *Datenschutz* Seite auf authentifizierte Benutzer ein:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Privacy.cshtml.cs?name=snippet&highlight=1)]

## <a name="sign-out"></a>Abmelden

Um den aktuellen Benutzer zu abmelden und dessen zu löschen, müssen Sie cookie [signoutasync](xref:Microsoft.AspNetCore.Authentication.AuthenticationHttpContextExtensions.SignOutAsync*)aufrufen. Der folgende Code fügt `Logout` der *Index* Seite einen Seiten Handler hinzu:

[!code-csharp[](social-without-identity/samples_snapshot/2.x/Pages/Index.cshtml.cs?name=snippet&highlight=3-7)]

Beachten Sie, dass der-aufrufswert `SignOutAsync` kein Authentifizierungsschema angibt. Die APP `DefaultScheme` von `CookieAuthenticationDefaults.AuthenticationScheme` wird als Fallback verwendet.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/simple>
* <xref:security/authentication/social/additional-claims>

::: moniker-end
