---
title: Konfigurieren von ASP.NET Core Identity
author: AdrienTorris
description: Verstehen ASP.NET Core Identity Sie die Standardwerte, und erfahren Sie, wie Sie Identity Eigenschaften zur Verwendung benutzerdefinierter Werte konfigurieren.
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
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
uid: security/authentication/identity-configuration
ms.openlocfilehash: b11a2d584b7275a9065c9915021ac945823531f8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051992"
---
# <a name="configure-no-locaspnet-core-identity"></a>Konfigurieren von ASP.NET Core Identity

ASP.NET Core Identity in werden Standardwerte für Einstellungen wie Kenn Wort Richtlinie, Sperrung und cookie Konfiguration verwendet. Diese Einstellungen können in der-Klasse überschrieben werden `Startup` .

## <a name="no-locidentity-options"></a>Identity Optionen

Die [ Identity options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) -Klasse stellt die Optionen dar, die verwendet werden können, um das System zu konfigurieren Identity . `IdentityOptions` muss **nach** dem Aufrufen von oder festgelegt werden `AddIdentity` `AddDefaultIdentity` .

### <a name="claims-no-locidentity"></a>Claims Identity

[ Identity Optionen. Claims Identity ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) gibt die [Anspruchs Identity Optionen](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) mit den Eigenschaften an, die in der folgenden Tabelle aufgeführt sind.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Roleclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | Ruft den für einen Rollen Anspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes. Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [Securitystampclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | Ruft den für den sicherheitstokenanspruch verwendeten Anspruchstyp ab oder legt ihn fest. | `AspNet.Identity.SecurityStamp` |
| [Useridclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | Ruft den für den benutzerbezeichneranspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [Usernameclaimtype](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | Ruft den für den Benutzernamens Anspruch verwendeten Anspruchstyp ab oder legt ihn fest. | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>Sperrung

Die Sperre wird in der [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) -Methode festgelegt:

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

Der vorangehende Code basiert auf der `Login` Identity Vorlage. 

Sperrungs Optionen werden in festgelegt `StartUp.ConfigureServices` :

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

Der vorangehende Code legt die [ Identity Optionen](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den Standardwerten fest.

Bei erfolgreicher Authentifizierung wird die Anzahl fehlerhafter Zugriffsversuche zurückgesetzt, und die Uhr wird zurückgesetzt.

[ Identity Optionen. Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) gibt die [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| ["Zuweisen"](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | Bestimmt, ob ein neuer Benutzer gesperrt werden kann. | `true` |
| [Defaultlockouttimespan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | Die Zeitspanne, für die ein Benutzer gesperrt wird, wenn eine Sperre auftritt. | 5 Minuten |
| [Maxfailedaccessversuchs](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | Die Anzahl der fehlgeschlagenen Zugriffsversuche, bis ein Benutzer gesperrt ist, wenn die Sperre aktiviert ist. | 5 |

### <a name="password"></a>Kennwort

Standardmäßig Identity erfordert, dass Kenn Wörter einen Großbuchstaben, einen Kleinbuchstaben, eine Ziffer und ein nicht alphanumerisches Zeichen enthalten. Kenn Wörter müssen mindestens sechs Zeichen lang sein.

Kenn Wörter werden wie folgt konfiguriert:

* <xref:Microsoft.AspNetCore.Identity.PasswordOptions> in `Startup.ConfigureServices`.
* [ `[StringLength]` Attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) von `Password` Eigenschaften, Identity Wenn [in die APP integriert](xref:security/authentication/scaffold-identity)wird. `InputModel``Password`die Eigenschaften werden in den folgenden Dateien gefunden:
  * `Areas/Identity/Pages/Account/Register.cshtml.cs`
  * `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

[ Identity Optionen. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) gibt die [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) -Eigenschaft mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Requirements-Ziffer](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | Erfordert eine Zahl zwischen 0-9 und dem Kennwort. | `true` |
| [Requirements dlength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | Die Mindestlänge des Kennworts. | 6 |
| [Requirements lowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | Erfordert einen Kleinbuchstaben im Kennwort. | `true` |
| [Anforderungs alphanumerisch](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | Erfordert ein nicht alphanumerisches Zeichen im Kennwort. | `true` |
| [Requirements duniquechars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | Gilt nur für ASP.net Core 2,0 oder höher.<br><br> Erfordert die Anzahl der unterschiedlichen Zeichen im Kennwort. | 1 |
| [Erforderlich (Großbuchstaben)](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | Erfordert ein Großbuchstaben im Kennwort. | `true` |

### <a name="sign-in"></a>Anmeldung

Mit dem folgenden Code werden Einstellungen festgelegt `SignIn` (auf Standardwerte):

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

[ Identity Options. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) gibt die [signinoptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) -Eigenschaft mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| ["Requirements confirmede Mail"](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | Erfordert eine bestätigte e-Mail-Adresse für die Anmeldung. | `false` |
| [Requirements confirmedphonenumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | Erfordert eine bestätigte Telefonnummer, um sich anzumelden. | `false` |

### <a name="tokens"></a>Token

" [ Identity Options. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) " gibt die [tokenoptionen](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) mit den Eigenschaften an, die in der Tabelle angezeigt werden.

| Eigenschaft | Beschreibung |
| -------- | ----------- |
| [Authenticenortokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | Ruft den ab `AuthenticatorTokenProvider` , der zum Überprüfen von zweistufigen Anmeldungen mit einem Authentifikator verwendet wird, oder legt diesen fest. |
| [Changeemailstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | Ruft den `ChangeEmailTokenProvider` zum Generieren von Token ab, die in e-Mail-Bestätigungs-e-Mails verwendet werden. |
| [Changephonenumberumkenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | Ruft den ab `ChangePhoneNumberTokenProvider` , der zum Generieren von Token verwendet wird, die beim Ändern von Telefonnummern verwendet werden |
| [Emailconfirmationstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | Ruft den Tokenanbieter ab, der zum Generieren von Token verwendet wird, die in e-Mail-Konto Bestätigungs |
| [Passwordresetstokenprovider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | Ruft den [iusertwofaktortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) ab, der zum Generieren von Token verwendet wird, die in e-Mails zur Kenn Wort Zurücksetzung verwendet werden |
| [Providermap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | Wird verwendet, um einen [benutzertokenanbieter](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) mit dem Schlüssel zu erstellen, der als Name des Anbieters verwendet wird. |

### <a name="user"></a>Benutzer

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[ Identity Options. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) gibt die [USEROPTIONS](/dotnet/api/microsoft.aspnetcore.identity.useroptions) -Eigenschaft mit den in der Tabelle angezeigten Eigenschaften an.

| Eigenschaft | Beschreibung | Standard |
| -------- | ----------- | :-----: |
| [Zuordnung von "Zuweisung"](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | Zulässige Zeichen im Benutzernamen. | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [Requirements-e-Mail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | Erfordert, dass jeder Benutzer eine eindeutige e-Mail-Adresse hat. | `false` |

### <a name="no-loccookie-settings"></a>Cookie -Einstellungen

Konfigurieren Sie die APP- cookie in `Startup.ConfigureServices` . [Konfigurieren der Anwendung Cookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) muss **nach** dem Aufrufen von `AddIdentity` oder aufgerufen werden `AddDefaultIdentity` .

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

Weitere Informationen finden Sie unter [ Cookie authenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).

## <a name="password-hasher-options"></a>Optionen für Kennwort Hasher

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Ruft Optionen für Kenn Wort Hashwert ab und legt Sie fest.

| Option | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | Der beim hashup neuer Kenn Wörter verwendete Kompatibilitätsmodus. Wird standardmäßig auf <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> festgelegt. Das erste Byte eines Hash Kennworts, das als *Format Marker* bezeichnet wird, gibt die Version des Hash Algorithmus an, mit dem das Kennwort als Hash verwendet wird. Wenn ein Kennwort mit einem Hash überprüft wird, <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> wählt die Methode den richtigen Algorithmus basierend auf dem ersten Byte aus. Ein Client kann authentifiziert werden, unabhängig davon, welche Version des Algorithmus zum Hashen des Kennworts verwendet wurde. Das Festlegen des Kompatibilitätsmodus wirkt sich auf das hashup *neuer Kenn Wörter* aus. |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | Die Anzahl der Iterationen, die beim Hashwert von Kenn Wörtern mithilfe von PBKDF2 verwendet werden. Dieser Wert wird nur verwendet, wenn <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> auf festgelegt ist <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> . Der Wert muss eine positive ganze Zahl sein, und der Standardwert ist `10000` . |

Im folgenden Beispiel <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> wird auf in festgelegt `12000` `Startup.ConfigureServices` :

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Erfordert Global, dass alle Benutzer authentifiziert werden.

[!INCLUDE[](~/includes/requireAuth.md)]