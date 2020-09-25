---
title: Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server für zusätzliche Sicherheitsszenarios konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/04/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: c8bce9572f0c21fdbd7ed585772c2b7965f40598
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90592942"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server

Von [Javier Calvarro Nelson](https://github.com/javiercn)

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a>Übergeben von Token an eine Blazor Server-App

Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden. Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices`-Beispiels, finden Sie unter [Übergeben von Token an eine serverseitige Blazor-Anwendung](https://github.com/javiercn/blazor-server-aad-sample).

Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App. Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese. Zum Beispiel:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Definieren Sie eine Klasse, um den anfänglichen App-Zustand mit den Zugriffs- und Aktualisierungstoken zu übergeben:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definieren Sie einen **bereichsbezogenen** Tokenanbieterdienst, der innerhalb der Blazor-App verwendet werden kann, um die Token aus der [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) aufzulösen:

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Fügen Sie in `Startup.ConfigureServices` Dienste für Folgendes hinzu:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Erstellen Sie in der Datei `_Host.cshtml` eine Instanz von `InitialApplicationState`, und übergeben Sie diese als Parameter an die App:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

Lösen Sie in der `App`-Komponente (`App.razor`) den Dienst auf, und initialisieren Sie diesen mit den Daten aus dem Parameter:

```razor
@inject TokenProvider TokenProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokenProvider.AccessToken = InitialState.AccessToken;
        TokenProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Fügen Sie der App einen Paketverweis für das NuGet-Paket [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.

Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token ab, um die API aufzurufen:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly TokenProvider store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="set-the-authentication-scheme"></a>Festlegen des Authentifizierungsschemas

Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden. Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Verwenden von OpenID Connect v2.0-Endpunkten (OIDC)

Die Authentifizierungsbibliothek sowie Blazor-Vorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC). Zum Verwenden eines 2.0-Endpunkts konfigurieren Sie die Option <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> unter <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest. Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> oder in der Datei mit den App-Einstellungen mit dem <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>-Schlüssel fest.

### <a name="code-changes"></a>Codeänderungen

* Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0. Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison) in der Azure-Dokumentation.
* Da Ressourcen in Bereichs-URIs für 2.0-Endpunkte angegeben werden, entfernen Sie die Einstellung der <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType>-Eigenschaft in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
      ```

  For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.

### App ID URI

* When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.
* All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.
* When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.

`appsettings.json`:

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

Sie finden den zu verwendenden App-ID-URI in der Beschreibung zur App-Registrierung des OIDC-Anbieters.
