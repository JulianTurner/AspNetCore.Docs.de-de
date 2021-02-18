---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine eigenständige Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: dd32db8eaac70cfb3dfb3872c43c28aed6a87657
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280907"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory B2C

In diesem Artikel wird beschrieben, wie Sie eine [eigenständige Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) für die Authentifizierung verwendet.

Erstellen Sie einen Mandanten, oder identifizieren Sie einen vorhandenen B2C-Mandanten für die App, die im Azure-Portal verwendet werden soll. Befolgen Sie hierzu die Anweisungen im Tutorial [Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant). Kehren Sie direkt nach dem Erstellen oder Identifizieren eines zu verwendenden Mandanten zu diesem Artikel zurück.

Notieren Sie sich folgende Informationen:

* AAD B2C-Instanz (z. B. `https://contoso.b2clogin.com/`, wobei der nachgestellte Schrägstrich enthalten ist): Die Instanz ist das Schema und der Host einer Azure-B2B-App-Registrierung, die Sie im Fenster **Endpunkte** auf der Seite **App-Registrierungen** im Azure-Portal finden.
* Primäre, Herausgeber- oder Mandantendomäne für AAD B2C (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.

Registrieren einer AAD B2C-App:

::: moniker range=">= aspnetcore-5.0"

1. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).
1. Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**
1. Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.
1. Wählen Sie **Registrieren**.

Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`).

In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD B2C**).
1. Wählen Sie für **Unterstützte Kontotypen** die Option für mehrere Mandanten aus: **Konten in einem beliebigen Organisationsverzeichnis oder ein beliebiger Identitätsanbieter Wählen Sie diese Option aus, um Benutzer bei Azure AD B2C zu authentifizieren.**
1. Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Vergewissern Sie sich, dass die Option **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist.
1. Wählen Sie **Registrieren**.

Notieren Sie sich die Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`).

In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

Gehen Sie unter **Startseite** > **Azure AD B2C** > **Benutzerflows** wie folgt vor:

[Erstellen Sie einen Benutzerflow für die Registrierung und Anmeldung](/azure/active-directory-b2c/tutorial-create-user-flows)

Wählen Sie mindestens das Benutzerattribut **Anwendungsansprüche** > **Anzeigename** aus, um `context.User.Identity.Name` in der `LoginDisplay`-Komponente (`Shared/LoginDisplay.razor`) auszufüllen.

Notieren Sie sich den Namen des für die App erstellten Benutzerflows für die Registrierung und Anmeldung (z. B. `B2C_1_signupsignin`).

Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Platzhalter                   | Name im Azure-Portal               | Beispiel                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Instanz                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | Anwendungs-ID (Client)         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Benutzerflow für die Registrierung oder Anmeldung       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Primäre, Herausgeber- oder Mandantendomäne | `contoso.onmicrosoft.com`              |

Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

> [!NOTE]
> Im Azure-Portal ist die App-Plattformkonfiguration **Umleitungs-URI** für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.
>
> Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.
>
> Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:

* Anmelden bei der App mit einem AAD-Benutzerkonto
* Anfordern von Zugriffstoken für Microsoft-APIs Weitere Informationen finden Sie unter:
  * [Zugriffstokenbereiche](#access-token-scopes)
  * [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App zum Verwenden eines B2C-Einzelkontos (`IndividualB2C`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.

Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert. Diese Methode richtet alle Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter. Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.

Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Beispiel:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>Zugriffstokenbereiche

Die Blazor WebAssembly-Vorlage konfiguriert die App nicht automatisch so, dass diese ein Zugriffstoken für eine sichere API anfordert. Zum Bereitstellen eines Zugriffstokens als Teil des Anmeldeflows fügen Sie den Bereich den Standard-Zugriffstokenbereichen von <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> hinzu:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:

* [Anfordern zusätzlicher Zugriffstoken](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>Anmeldemodus

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/additional-scenarios>
* [Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](/azure/active-directory-b2c/tutorial-create-tenant)
* [Tutorial: Registrieren Sie eine Anwendung in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications)
* [Dokumentation zu Microsoft Identity Platform](/azure/active-directory/develop/)
