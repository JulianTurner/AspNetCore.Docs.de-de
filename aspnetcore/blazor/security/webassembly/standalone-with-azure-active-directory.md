---
title: Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine eigenständige Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/08/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: b44c5372d694dcc16ff66e24233171e3320d7294
ms.sourcegitcommit: daa9ccf580df531254da9dce8593441ac963c674
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91900900"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>Sichern einer eigenständigen Blazor WebAssembly-App in ASP.NET Core mit Azure Active Directory

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

Für das Erstellen einer [eigenständigen Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly), die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet, gilt Folgendes:

[Erstellen Sie einen AAD-Mandanten und eine Webanwendung:](/azure/active-directory/develop/v2-overview)

Registrieren Sie eine AAD-App im Bereich **Azure Active Directory** > **App-Registrierungen** des Azure-Portals:

::: moniker range=">= aspnetcore-5.0"

1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** auswählen.
1. Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich folgende Informationen:

* Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)
* Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)

In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Standalone AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** auswählen.
1. Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Weiter unten in diesem Artikel erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich folgende Informationen:

* Anwendungs-ID (Client-ID) (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)
* Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)

In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

Erstellen Sie die App in einem leeren Ordner. Ersetzen Sie die Platzhalter im folgenden Befehl durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Platzhalter   | Name im Azure-Portal       | Beispiel                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Anwendungs-ID (Client) | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | Verzeichnis-ID (Mandant)   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

> [!NOTE]
> Im Azure-Portal ist die App-Plattformkonfiguration **Umleitungs-URI** für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.
>
> Wenn die App auf einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der App im Panel **Debuggen**.
>
> Wenn der Port nicht zuvor mit dem bekannten Port der App konfiguriert wurde, kehren Sie zur App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/additional-scopes-standalone-AAD.md)]

::: moniker-end

Nachdem Sie die App erstellt haben, sollten Sie folgende Aktionen durchführen können:

* Anmelden bei der App mit einem AAD-Benutzerkonto
* Anfordern von Zugriffstoken für Microsoft-APIs Weitere Informationen finden Sie unter:
  * [Zugriffstokenbereiche](#access-token-scopes)
  * [Schnellstart: Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.

Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.

## <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert. Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter. Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration beim Registrieren der App abgerufen werden.

Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
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

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:

* [Anfordern zusätzlicher Zugriffstoken](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>Anmeldemodus

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Indexseite

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/additional-scenarios>
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentation zu Microsoft Identity Platform](/azure/active-directory/develop/)
