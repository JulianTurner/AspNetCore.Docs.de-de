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
ms.openlocfilehash: d47cfba75b640f57cc713049594d4e8acd1fcd0e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280327"
---
# <a name="aspnet-core-blazor-server-additional-security-scenarios"></a><span data-ttu-id="d978e-103">Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="d978e-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

::: moniker range=">= aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="d978e-104">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="d978e-104">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="d978e-105">Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="d978e-105">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="d978e-106">Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App.</span><span class="sxs-lookup"><span data-stu-id="d978e-106">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="d978e-107">Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese.</span><span class="sxs-lookup"><span data-stu-id="d978e-107">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="d978e-108">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d978e-108">For example:</span></span>

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

<span data-ttu-id="d978e-109">Optional werden zusätzliche Bereiche mit `options.Scope.Add("{SCOPE}");` hinzugefügt, wobei der Platzhalter `{SCOPE}` der zusätzliche hinzuzufügende Bereich ist.</span><span class="sxs-lookup"><span data-stu-id="d978e-109">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="d978e-110">Definieren Sie einen **bereichsbezogenen** Tokenanbieterdienst, der innerhalb der Blazor-App verwendet werden kann, um die Token aus der [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d978e-110">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d978e-111">Fügen Sie in `Startup.ConfigureServices` Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="d978e-111">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="d978e-112">Definieren Sie eine Klasse, um den anfänglichen App-Zustand mit den Zugriffs- und Aktualisierungstoken zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="d978e-112">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d978e-113">Erstellen Sie in der Datei `_Host.cshtml` eine Instanz von `InitialApplicationState`, und übergeben Sie diese als Parameter an die App:</span><span class="sxs-lookup"><span data-stu-id="d978e-113">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="d978e-114">Lösen Sie in der `App`-Komponente (`App.razor`) den Dienst auf, und initialisieren Sie diesen mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="d978e-114">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="d978e-115">Fügen Sie der App einen Paketverweis für das NuGet-Paket [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d978e-115">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="d978e-116">Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token für die API-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="d978e-116">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

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

<h2 id="set-the-authentication-scheme"><span data-ttu-id="d978e-117">Festlegen des Authentifizierungsschemas</span><span class="sxs-lookup"><span data-stu-id="d978e-117">Set the authentication scheme</span></span></h2>

<span data-ttu-id="d978e-118">Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d978e-118">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="d978e-119">Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d978e-119">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<h2 id="pass-tokens-to-a-blazor-server-app"><span data-ttu-id="d978e-120">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="d978e-120">Pass tokens to a Blazor Server app</span></span></h2>

<span data-ttu-id="d978e-121">Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="d978e-121">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span>

<span data-ttu-id="d978e-122">Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App.</span><span class="sxs-lookup"><span data-stu-id="d978e-122">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="d978e-123">Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese.</span><span class="sxs-lookup"><span data-stu-id="d978e-123">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="d978e-124">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d978e-124">For example:</span></span>

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

<span data-ttu-id="d978e-125">Optional werden zusätzliche Bereiche mit `options.Scope.Add("{SCOPE}");` hinzugefügt, wobei der Platzhalter `{SCOPE}` der zusätzliche hinzuzufügende Bereich ist.</span><span class="sxs-lookup"><span data-stu-id="d978e-125">Optionally, additional scopes are added with `options.Scope.Add("{SCOPE}");`, where the placeholder `{SCOPE}` is the additional scope to add.</span></span>

<span data-ttu-id="d978e-126">Optional wird die Ressource mit `options.Resource = "{RESOURCE}";` angegeben, wobei der Platzhalter `{RESOURCE}` die Ressource ist.</span><span class="sxs-lookup"><span data-stu-id="d978e-126">Optionally, the resource is specified with `options.Resource = "{RESOURCE}";`, where the placeholder `{RESOURCE}` is the resource.</span></span> <span data-ttu-id="d978e-127">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d978e-127">For example:</span></span>

```csharp
options.Resource = "https://graph.microsoft.com";
```

<span data-ttu-id="d978e-128">Definieren Sie eine Klasse, um den anfänglichen App-Zustand mit den Zugriffs- und Aktualisierungstoken zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="d978e-128">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d978e-129">Definieren Sie einen **bereichsbezogenen** Tokenanbieterdienst, der innerhalb der Blazor-App verwendet werden kann, um die Token aus der [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="d978e-129">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="d978e-130">Fügen Sie in `Startup.ConfigureServices` Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="d978e-130">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="d978e-131">Erstellen Sie in der Datei `_Host.cshtml` eine Instanz von `InitialApplicationState`, und übergeben Sie diese als Parameter an die App:</span><span class="sxs-lookup"><span data-stu-id="d978e-131">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="d978e-132">Lösen Sie in der `App`-Komponente (`App.razor`) den Dienst auf, und initialisieren Sie diesen mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="d978e-132">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="d978e-133">Fügen Sie der App einen Paketverweis für das NuGet-Paket [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.</span><span class="sxs-lookup"><span data-stu-id="d978e-133">Add a package reference to the app for the [`Microsoft.AspNet.WebApi.Client`](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="d978e-134">Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token für die API-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="d978e-134">In the service that makes a secure API request, inject the token provider and retrieve the token for the API request:</span></span>

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

<h2 id="set-the-authentication-scheme"><span data-ttu-id="d978e-135">Festlegen des Authentifizierungsschemas</span><span class="sxs-lookup"><span data-stu-id="d978e-135">Set the authentication scheme</span></span></h2>

<span data-ttu-id="d978e-136">Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d978e-136">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="d978e-137">Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d978e-137">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="d978e-138">Verwenden von OpenID Connect v2.0-Endpunkten (OIDC)</span><span class="sxs-lookup"><span data-stu-id="d978e-138">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="d978e-139">In Versionen von ASP.NET Core vor 5.0 verwenden die Authentifizierungsbibliothek sowie Blazor-Vorlagen die Version 1.0 der OpenID Connect-Endpunkte (OIDC).</span><span class="sxs-lookup"><span data-stu-id="d978e-139">In versions of ASP.NET Core prior to 5.0, the authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="d978e-140">Konfigurieren Sie die Option <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>, um einen v2.0-Endpunkt mit Versionen von ASP.NET Core vor 5.0 zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="d978e-140">To use a v2.0 endpoint with versions of ASP.NET Core prior to 5.0, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="d978e-141">Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="d978e-141">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="d978e-142">Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest.</span><span class="sxs-lookup"><span data-stu-id="d978e-142">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="d978e-143">Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> oder in der Datei mit den App-Einstellungen mit dem <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>-Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="d978e-143">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="d978e-144">Codeänderungen</span><span class="sxs-lookup"><span data-stu-id="d978e-144">Code changes</span></span>

* <span data-ttu-id="d978e-145">Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0.</span><span class="sxs-lookup"><span data-stu-id="d978e-145">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="d978e-146">Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="d978e-146">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="d978e-147">in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="d978e-147">in the Azure documentation.</span></span>
* <span data-ttu-id="d978e-148">Da Ressourcen in Bereichs-URIs für 2.0-Endpunkte angegeben werden, entfernen Sie die Einstellung der <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType>-Eigenschaft in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="d978e-148">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="d978e-149">Weitere Informationen finden Sie in der Azure-Dokumentation unter [Geltungsbereiche im Gegensatz zu Ressourcen](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources).</span><span class="sxs-lookup"><span data-stu-id="d978e-149">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="d978e-150">App-ID-URI</span><span class="sxs-lookup"><span data-stu-id="d978e-150">App ID URI</span></span>

* <span data-ttu-id="d978e-151">Bei der Verwendung von v2.0-Endpunkten definieren APIs einen *`App ID URI`* , der einen eindeutigen Bezeichner für die API darstellen soll.</span><span class="sxs-lookup"><span data-stu-id="d978e-151">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="d978e-152">Alle Bereiche enthalten den App-ID-URI als Präfix, und die v2.0-Endpunkte geben Zugriffstoken mit dem App-ID-URI als Zielgruppe aus.</span><span class="sxs-lookup"><span data-stu-id="d978e-152">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="d978e-153">Bei Verwendung von v2.0-Endpunkten ändert sich die in der Server-API konfigurierte Client-ID von der API-Anwendungs-ID (Client-ID) in den App-ID-URI.</span><span class="sxs-lookup"><span data-stu-id="d978e-153">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="d978e-154">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="d978e-154">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="d978e-155">Sie finden den zu verwendenden App-ID-URI in der Beschreibung zur App-Registrierung des OIDC-Anbieters.</span><span class="sxs-lookup"><span data-stu-id="d978e-155">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>

::: moniker-end
