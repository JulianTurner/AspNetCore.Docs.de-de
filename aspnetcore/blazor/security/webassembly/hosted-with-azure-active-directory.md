---
title: Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie eine gehostete ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory sichern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: f0c7723e8de7167b4fe5021ea1ca26e7cf198581
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710567"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>Sichern einer gehosteten ASP.NET Core Blazor WebAssembly-App mit Azure Active Directory

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

In diesem Artikel wird beschrieben, wie Sie eine [gehostete Blazor WebAssembly-App](xref:blazor/hosting-models#blazor-webassembly) erstellen, die [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) für die Authentifizierung verwendet.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Für in Visual Studio erstellte Blazor WebAssembly-Apps, die zur Unterstützung von Konten in einem AAD-Organisationsverzeichnis konfiguriert sind, konfiguriert Visual Studio die Projekte der Projektmappe oder die Registrierungen der Azure-Portal-App bei der Projektgenerierung derzeit nicht richtig. Dieses Problem wird in einem zukünftigen Release von Visual Studio behoben.
>
> Dieser Artikel zeigt, wie die Projektmappe und die Registrierungen für das Azure-App-Portal mit dem .NET-Befehl `dotnet new` der CLI und durch manuelles Erstellen der App-Registrierungen im Azure-Portal erstellt werden können.
>
> Wenn Sie es vorziehen, die Projektmappe und die Azure-App-Registrierungen mit Visual Studio zu erstellen, bevor die IDE aktualisiert wird, lesen Sie **_jeden Abschnitt dieses Artikels_**, und bestätigen oder aktualisieren Sie die Konfigurationen der Apps und die Registrierungen der Apps, nachdem Visual Studio die Projektmappe erstellt hat.

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a>Registrieren von Apps in AAD und Erstellen einer Lösung

### <a name="create-a-tenant"></a>Erstellen eines Mandanten

Befolgen Sie die Anweisungen unter [Schnellstart: Einrichten eines Mandanten](/azure/active-directory/develop/quickstart-create-new-tenant), um einen Mandanten in AAD zu erstellen.

### <a name="register-a-server-api-app"></a>Registrieren einer Server-API-App

Registrieren einer AAD-App für die *Server-API-App*:

1. Klicken Sie unter **Azure Active Directory** > **App-Registrierungen** auf **Neue Registrierung**.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Server AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.
1. Die *Server-API-App* erfordert in diesem Szenario keinen **Umleitungs-URI**. Belassen Sie daher die Dropdownauswahl bei **Web**, und geben Sie keinen Umleitungs-URI ein.
1. Deaktivieren Sie das Kontrollkästchen bei **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich folgende Informationen:

* Anwendungs-ID (Client-ID) der *Server-API-App* (z. B. `41451fa7-82d9-4673-8fa5-69eff5a761fd`)
* Verzeichnis-ID (Mandanten-ID) (z. B. `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)
* Primäre, Herausgeber- oder Mandantendomäne für AAD (z. B. `contoso.onmicrosoft.com`): Die Domäne ist als **Herausgeberdomäne** auf dem Blatt **Branding** des Azure-Portals für die registrierte App verfügbar.

Entfernen Sie unter **API-Berechtigungen** die Berechtigung **Microsoft Graph** > **User.Read**, da die App keine Anmeldung oder Zugriff auf Benutzerprofile benötigt.

Gehen Sie unter **Eine API verfügbar machen** wie folgt vor:

1. Wählen Sie **Bereich hinzufügen**.
1. Wählen Sie **Speichern und fortfahren** aus.
1. Geben Sie einen **Bereichsnamen** an (z. B. `API.Access`).
1. Geben Sie einen **Anzeigenamen der Administratorzustimmung** an (z. B. `Access API`).
1. Geben Sie eine **Beschreibung der Administratorzustimmung** an (z. B. `Allows the app to access server app API endpoints.`).
1. Vergewissern Sie sich, dass **Zustand** auf **Aktiviert** gesetzt ist.
1. Wählen Sie **Bereich hinzufügen** aus.

Notieren Sie sich folgende Informationen:

* App-ID-URI (z. B. `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` oder der von Ihnen angegebene benutzerdefinierte Wert)
* Bereichsname (beispielsweise `API.Access`)

### <a name="register-a-client-app"></a>Registrieren einer Client-App

Registrieren einer AAD-App für die *Client-App*:

::: moniker range=">= aspnetcore-5.0"

1. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.
1. Treffen Sie für **Umleitungs-URI** die Dropdownauswahl **Single-Page-Anwendung (SPA)** , und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).

In **Authentifizierung** > **Plattformkonfigurationen** > **Single-Page-Anwendung (SPA)** :

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Stellen Sie sicher, dass für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token** **nicht** aktiviert sind.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** die Option **Neue Registrierung** aus.
1. Geben Sie einen **Namen** für die App an (z. B. **Blazor Client AAD**).
1. Wählen Sie einen **Unterstützten Kontotyp** aus. Hier können Sie die Option **Nur Konten in diesem Organisationsverzeichnis** (einzelner Mandant) auswählen.
1. Belassen Sie die Dropdownauswahl für **Umleitungs-URI** auf **Web**, und geben Sie den folgenden Umleitungs-URI an: `https://localhost:{PORT}/authentication/login-callback`. Der Standardport für eine App, die auf Kestrel ausgeführt wird, ist 5001. Wenn die App auf einem anderen Kestrel-Port ausgeführt wird, verwenden Sie den Port der App. Für IIS Express finden Sie den zufällig generierten Port für die App in den Eigenschaften der *`Server`* -App im Panel **Debuggen**. Da die App an dieser Stelle noch nicht existiert und der IIS Express-Port unbekannt ist, kehren Sie nach dem Erstellen der App zu diesem Schritt zurück, und aktualisieren Sie den Umleitungs-URI. Im Abschnitt zum [Erstellen der App](#create-the-app) erscheint ein Hinweis, um IIS Express-Benutzer an die Aktualisierung des Umleitungs-URIs zu erinnern.
1. Deaktivieren Sie das Kontrollkästchen **Berechtigungen** > **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen**.
1. Wählen Sie **Registrieren**.

Notieren Sie sich die Anwendungs-ID (Client-ID) der *`Client`* -App (z. B. `4369008b-21fa-427c-abaa-9b53bf58e538`).

In **Authentifizierung** > **Plattformkonfigurationen** > **Web**:

1. Vergewissern Sie sich, dass der **Umleitungs-URI** von `https://localhost:{PORT}/authentication/login-callback` vorhanden ist.
1. Aktivieren Sie für **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.
1. Die verbleibenden Standardwerte für die App müssen für dieses Szenario nicht angepasst werden.
1. Klicken Sie auf die Schaltfläche **Speichern**.

::: moniker-end

Gehen Sie unter **API-Berechtigungen** wie folgt vor:

1. Sorgen Sie dafür, dass die App über die Berechtigung **Microsoft Graph** > **User.Read** verfügt.
1. Klicken Sie auf **Berechtigung hinzufügen** und dann auf **Meine APIs**.
1. Wählen Sie die *Server-API-App* aus der Spalte **Name** aus (z. B. **Blazor Server AAD**).
1. Öffnen Sie die **API**-Liste.
1. Ermöglichen Sie den Zugriff auf die API (z. B. `API.Access`).
1. Wählen Sie **Berechtigungen hinzufügen** aus.
1. Klicken Sie auf die Schaltfläche **Administratoreinwilligung für {MANDANTENNAME} erteilen**. Klicken Sie auf **Ja**, um zu bestätigen.

### <a name="create-the-app"></a>Erstellen der App

Verwenden Sie einen leeren Ordner, ersetzen Sie im folgenden Befehl die Platzhalter durch die zuvor notierten Informationen, und führen Sie den Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| Platzhalter                  | Name im Azure-Portal                                     | Beispiel                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | Anwendungs-ID (Client-ID) für die *`Client`* -App        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | Bereichsname                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | Anwendungs-ID (Client-ID) für die *Server-API-App*      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | Anwendungs-ID-URI&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | Primäre, Herausgeber- oder Mandantendomäne                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | Verzeichnis-ID (Mandant)                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

&dagger;Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu. Wenn Sie den App-ID-URI für den Platzhalter `{SERVER API APP ID URI}` bereitstellen und das Schema `api://` lautet, entfernen Sie das Schema (`api://`) aus dem Argument. Dies wird anhand des Beispielwerts in der obigen Tabelle gezeigt. Wenn der App-ID-URI ein benutzerdefinierter Wert ist oder ein anderes Schema aufweist (z. B. `https://` für eine nicht vertrauenswürdige Herausgeberdomäne wie `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), müssen Sie den Standard-URI für den Geltungsbereich manuell aktualisieren und das Schema `api://` entfernen, nachdem die *`Client`* -App von der Vorlage erstellt wurde. Weitere Informationen finden Sie im Hinweis im Abschnitt [Geltungsbereiche von Zugriffstoken](#access-token-scopes). Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben. Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Der mit der Option `-o|--output` angegebene Ausgabespeicherort erstellt einen Projektordner, sofern kein solcher vorhanden ist, und wird Teil des Namens der App.

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [App-Einstellungen](#app-settings) beschrieben wird.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> Eine Konfigurationsänderung ist möglicherweise erforderlich, wenn Sie einen Azure-Mandanten mit einer nicht überprüften Herausgeberdomäne verwenden, was im Abschnitt [Zugriffstokenbereiche](#access-token-scopes) beschrieben wird.

::: moniker-end

> [!NOTE]
> Im Azure-Portal ist der **Umleitungs-URI** für die Plattformkonfiguration der *`Client`* -App für Apps, die auf dem Kestrel-Server mit Standardeinstellungen ausgeführt werden, für Port 5001 konfiguriert.
>
> Wenn die *`Client`* -App an einem zufälligen IIS Express-Port ausgeführt wird, finden Sie den Port der App in den Eigenschaften der *Server-API-App* im Panel **Debuggen**.
>
> Wenn der Port nicht zuvor mit dem bekannten Port der *`Client`* -App konfiguriert wurde, kehren Sie zur *`Client`* -App-Registrierung im Azure-Portal zurück, und aktualisieren Sie den Umleitungs-URI mit dem korrekten Port.

## <a name="server-app-configuration"></a>*`Server`* -App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **`Server`** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungspaket

::: moniker range=">= aspnetcore-5.0"

Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs mit der Microsoft Identity Platform wird von folgenden Paketen bereitgestellt:

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der App übereinstimmt, im **Versionsverlauf** des Pakets auf NuGet.org ermittelt werden.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Die Unterstützung für die Authentifizierung und Autorisierung von Aufrufen von ASP.NET Core-Web-APIs wird vom Paket [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) bereitgestellt:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) ermittelt werden.

::: moniker-end

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

::: moniker range=">= aspnetcore-5.0"

Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode. Mit der <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>-Methode werden Dienste konfiguriert, um die Web-API mit Microsoft Identity Platform v 2.0 zu schützen. Diese Methode erwartet einen `AzureAd`-Abschnitt in der App-Konfiguration mit den erforderlichen Einstellungen zum Initialisieren der Authentifizierungsoptionen.

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Die `AddAuthentication`-Methode richtet Authentifizierungsdienste innerhalb der App ein und konfiguriert den JWT-Bearerhandler als Standardauthentifizierungsmethode. Die <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>-Methode richtet die spezifischen Parameter im JWT-Bearerhandler ein, die zum Überprüfen der von Azure Active Directory ausgegebenen Token erforderlich sind:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> und <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> stellen Folgendes sicher:

* Die App versucht, Token auf eingehende Anforderungen zu analysieren und zu überprüfen.
* Jede Anforderung, die versucht, ohne die richtigen Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, schlägt fehl.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>User.Identity.Name

Standardmäßig füllt die *`Server`* -App-API `User.Identity.Name` mit dem Wert aus dem `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`-Anspruchstyp (z. B. `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`) auf.

So konfigurieren Sie die App, um den Wert vom `name`-Anspruchstyp zu empfangen:

* Fügen Sie `Startup.cs` einen Namespace für <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> hinzu:

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* Konfigurieren Sie den <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> der <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a>App-Einstellungen

::: moniker range=">= aspnetcore-5.0"

Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/blazor/includes/security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Die Datei `appsettings.json` enthält die Optionen zum Konfigurieren des JWT-Bearerhandlers, der zum Überprüfen von Zugriffstoken verwendet wird:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a>WeatherForecast-Controller

Der WeatherForecast-Controller (*Controllers/WeatherForecastController.cs*) macht eine geschützte API mit auf den Controller angewendetem [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut verfügbar. Es ist **wichtig**, Folgendes zu verstehen:

* Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut in diesem API-Controller ist die einzige Möglichkeit, diese API vor nicht autorisiertem Zugriff zu schützen.
* Das in der Blazor WebAssembly-App verwendete [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut dient nur als Hinweis für die App, dass der Benutzer autorisiert sein sollte, damit die App richtig funktioniert.

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>*`Client`* -App-Konfiguration

*Dieser Abschnitt bezieht sich auf die **`Client`** -App der Lösung.*

### <a name="authentication-package"></a>Authentifizierungspaket

Wenn eine App zum Verwenden von Geschäfts-, Schul- oder Unikonten (`SingleOrg`) erstellt wird, erhält die App automatisch einen Paketverweis für die [Authentifizierungsbibliothek von Microsoft](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)). Das Paket stellt einige Primitive bereit, die der App beim Authentifizieren von Benutzern und beim Abrufen von Token zum Aufrufen geschützter APIs helfen.

Wenn Sie einer App eine Authentifizierung hinzufügen, fügen Sie das Paket manuell der Projektdatei der App hinzu:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Für den Platzhalter `{VERSION}` kann die letzte stabile Version des Pakets, die mit der Version des gemeinsam genutzten Frameworks der Anwendung übereinstimmt, im **Versionsverlauf** des Pakets auf [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) ermittelt werden.

Das [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)-Paket fügt der App das [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)-Paket transitiv hinzu.

### <a name="authentication-service-support"></a>Unterstützung für Authentifizierungsdienste

Es wird Unterstützung für <xref:System.Net.Http.HttpClient>-Instanzen hinzugefügt, die bei Anforderungen an das Serverprojekt Zugriffstoken einschließen.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample.ServerAPI`).

Die Unterstützung für die Authentifizierung von Benutzern wird im Dienstcontainer mit der vom Paket [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) bereitgestellten <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Erweiterungsmethode registriert. Diese Methode richtet die Dienste ein, die erforderlich sind, damit die App mit dem Identitätsanbieter interagiert.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Die <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>-Methode akzeptiert einen Rückruf zum Konfigurieren der für die Authentifizierung der App erforderlichen Parameter. Die zum Konfigurieren der App erforderlichen Werte können von der AAD-Konfiguration im Azure-Portal beim Registrieren der App abgerufen werden.

Die Konfiguration wird durch die Datei `wwwroot/appsettings.json` bereitgestellt:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Beispiel:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Zugriffstokenbereiche

Zu den Standard-Zugriffstokenbereichen zählen die Zugriffstokenbereiche, auf die Folgendes zutrifft:

* standardmäßig in der Anmeldeanforderung enthalten
* zum Bereitstellen eines Zugriffstokens direkt nach der Authentifizierung verwendet

Alle Bereiche müssen gemäß den Azure Active Directory-Regeln zur selben App gehören. Zusätzliche Bereiche können nach Bedarf für zusätzliche API-Apps hinzugefügt werden:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Die Blazor WebAssembly-Vorlage fügt dem App-ID-URI-Argument, das im Befehl `dotnet new` übergeben wird, automatisch ein `api://`-Schema hinzu. Wenn eine App aus der Blazor-Projektvorlage generiert wird, überprüfen Sie, ob der Wert des Bereichs für das Standardzugriffstoken entweder den richtigen URI-Wert für die App-ID verwendet, den Sie im Azure-Portal bereitgestellt haben, oder einen Wert mit **einem** der folgenden Formate:
>
> * Wenn die Domäne des Herausgebers des Verzeichnisses als **vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Überprüfen Sie, ob der Wert ein doppeltes Schema aufweist (`api://api://...`). Wenn ein doppeltes Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.
>
> * Wenn die Domäne des Herausgebers des Verzeichnisses als **nicht vertrauenswürdig** gilt, weist der Bereich des Standardzugriffstoken in der Regel einen Wert ähnlich des folgenden Beispiels auf. Dabei ist `API.Access` der Name des Standardbereichs:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Überprüfen Sie, ob der Wert ein zusätzliches `api://`-Schema aufweist (`api://https://contoso.onmicrosoft.com/...`). Wenn ein zusätzliches `api://`-Schema vorhanden ist, entfernen Sie das erste `api://`-Schema aus dem Wert.
>
> Die Blazor WebAssembly-Vorlage wird möglicherweise in einem zukünftigen Release von ASP.NET Core geändert werden, um diese Szenarios zu beheben. Weitere Informationen finden Sie unter [Doppeltes Schema für den App-ID-URI in Blazor WASM-Vorlage (gehostet, Einzelorganisation) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Geben Sie zusätzliche Bereiche mit `AdditionalScopesToConsent` an:

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

Weitere Informationen finden Sie in den folgenden Abschnitten des Artikels zu *zusätzlichen Szenarios*:

* [Anfordern zusätzlicher Zugriffstoken](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Anfügen von Token an ausgehende Anforderungen](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Anmeldemodus

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a>Imports-Datei

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a>Indexseite

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a>App-Komponente

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin-Komponente

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay-Komponente

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a>Authentication-Komponente

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData-Komponente

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a>Ausführen der App

Führen Sie die App aus dem Serverprojekt aus. Wenn Sie Visual Studio verwenden, haben Sie folgende beiden Möglichkeiten:

* Wählen Sie in der Dropdownliste **Startprojekte** in der Symbolleiste die *Server-API-App* aus, und klicken Sie auf **Ausführen**.
* Wählen Sie das Serverprojekt im **Projektmappen-Explorer** aus, und klicken Sie in der Symbolleiste auf **Ausführen**, oder starten Sie die App über das Menü **Debuggen**.

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/additional-scenarios>
* [Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Dokumentation zu Microsoft Identity Platform](/azure/active-directory/develop/)
* [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](/azure/active-directory/develop/quickstart-register-app)
