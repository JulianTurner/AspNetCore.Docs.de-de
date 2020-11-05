---
title: Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server
author: guardrex
description: Erfahren Sie, wie Sie Blazor Server für zusätzliche Sicherheitsszenarios konfigurieren.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/06/2020
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
uid: blazor/security/server/additional-scenarios
ms.openlocfilehash: 56b226f8e4a10aa996b0344f10c76dad2ae32b51
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234434"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a>Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server

Von [Javier Calvarro Nelson](https://github.com/javiercn)

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Übergeben von Token an eine Blazor Server-App</h2>

Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden.

Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App. Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese. Zum Beispiel:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Optional werden zusätzliche Bereiche mit `options.Scope.Add("{SCOPE}");` hinzugefügt, wobei der Platzhalter `{SCOPE}` der zusätzliche hinzuzufügende Bereich ist.

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

Definieren Sie eine Klasse, um den anfänglichen App-Zustand mit den Zugriffs- und Aktualisierungstoken zu übergeben:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
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

<component type="typeof(App)" param-InitialState="tokens" 
    render-mode="ServerPrerendered" />
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

Fügen Sie der App einen Paketverweis für das NuGet-Paket [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.

Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token für die API-Anforderung ab:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Festlegen des Authentifizierungsschemas</h2>

Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden. Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app">Übergeben von Token an eine Blazor Server-App</h2>

Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden.

Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App. Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese. Zum Beispiel:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.IdentityModel.Protocols.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = OpenIdConnectResponseType.Code;
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
});
```

Optional werden zusätzliche Bereiche mit `options.Scope.Add("{SCOPE}");` hinzugefügt, wobei der Platzhalter `{SCOPE}` der zusätzliche hinzuzufügende Bereich ist.

Optional wird die Ressource mit `options.Resource = "{RESOURCE}";` angegeben, wobei der Platzhalter `{RESOURCE}` die Ressource ist. Zum Beispiel:

```csharp
options.Resource = "https://graph.microsoft.com";
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

Fügen Sie der App einen Paketverweis für das NuGet-Paket [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.

Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token für die API-Anforderung ab:

```csharp
using System;
using System.Net.Http;
using System.Threading.Tasks;

public class WeatherForecastService
{
    private readonly HttpClient http;
    private readonly TokenProvider tokenProvider;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        http = clientFactory.CreateClient();
        this.tokenProvider = tokenProvider;
    }

    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var token = tokenProvider.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await http.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

<h2 id="set-the-authentication-scheme">Festlegen des Authentifizierungsschemas</h2>

Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden. Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Verwenden von OpenID Connect v2.0-Endpunkten (OIDC)

In Versionen von ASP.NET Core vor 5.0 verwenden die Authentifizierungsbibliothek sowie Blazor-Vorlagen die Version 1.0 der OpenID Connect-Endpunkte (OIDC). Konfigurieren Sie die Option <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, um einen v2.0-Endpunkt mit Versionen von ASP.NET Core vor 5.0 zu verwenden:

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

  Weitere Informationen finden Sie in der Azure-Dokumentation unter [Geltungsbereiche im Gegensatz zu Ressourcen](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources).

### <a name="app-id-uri"></a>App-ID-URI

* Bei der Verwendung von v2.0-Endpunkten definieren APIs einen *`App ID URI`* , der einen eindeutigen Bezeichner für die API darstellen soll.
* Alle Bereiche enthalten den App-ID-URI als Präfix, und die v2.0-Endpunkte geben Zugriffstoken mit dem App-ID-URI als Zielgruppe aus.
* Bei Verwendung von v2.0-Endpunkten ändert sich die in der Server-API konfigurierte Client-ID von der API-Anwendungs-ID (Client-ID) in den App-ID-URI.

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

::: moniker-end
