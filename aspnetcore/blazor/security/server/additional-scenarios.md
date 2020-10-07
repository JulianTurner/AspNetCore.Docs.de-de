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
ms.openlocfilehash: 9b3698489300e45cf77c3d51611ff44e2f4e16a5
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393664"
---
# <a name="aspnet-core-no-locblazor-server-additional-security-scenarios"></a><span data-ttu-id="72c9b-103">Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor Server</span><span class="sxs-lookup"><span data-stu-id="72c9b-103">ASP.NET Core Blazor Server additional security scenarios</span></span>

<span data-ttu-id="72c9b-104">Von [Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="72c9b-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="pass-tokens-to-a-no-locblazor-server-app"></a><span data-ttu-id="72c9b-105">Übergeben von Token an eine Blazor Server-App</span><span class="sxs-lookup"><span data-stu-id="72c9b-105">Pass tokens to a Blazor Server app</span></span>

<span data-ttu-id="72c9b-106">Token, die außerhalb der Razor-Komponenten in einer Blazor Server-App verfügbar sind, können mit dem in diesem Abschnitt beschriebenen Ansatz an Komponenten übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="72c9b-106">Tokens available outside of the Razor components in a Blazor Server app can be passed to components with the approach described in this section.</span></span> <span data-ttu-id="72c9b-107">Beispielcode, einschließlich eines umfassenden `Startup.ConfigureServices`-Beispiels, finden Sie unter [Übergeben von Token an eine serverseitige Blazor-Anwendung](https://github.com/javiercn/blazor-server-aad-sample).</span><span class="sxs-lookup"><span data-stu-id="72c9b-107">For sample code, including a complete `Startup.ConfigureServices` example, see the [Passing tokens to a server-side Blazor application](https://github.com/javiercn/blazor-server-aad-sample).</span></span>

<span data-ttu-id="72c9b-108">Authentifizieren Sie die Blazor Server-App genauso wie eine reguläre Razor Pages- oder MVC-App.</span><span class="sxs-lookup"><span data-stu-id="72c9b-108">Authenticate the Blazor Server app as you would with a regular Razor Pages or MVC app.</span></span> <span data-ttu-id="72c9b-109">Stellen Sie die Token für das Authentifizierungscookie bereit, und speichern Sie diese.</span><span class="sxs-lookup"><span data-stu-id="72c9b-109">Provision and save the tokens to the authentication cookie.</span></span> <span data-ttu-id="72c9b-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="72c9b-110">For example:</span></span>

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

<span data-ttu-id="72c9b-111">Definieren Sie eine Klasse, um den anfänglichen App-Zustand mit den Zugriffs- und Aktualisierungstoken zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="72c9b-111">Define a class to pass in the initial app state with the access and refresh tokens:</span></span>

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="72c9b-112">Definieren Sie einen **bereichsbezogenen** Tokenanbieterdienst, der innerhalb der Blazor-App verwendet werden kann, um die Token aus der [Abhängigkeitsinjektion](xref:blazor/fundamentals/dependency-injection) aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="72c9b-112">Define a **scoped** token provider service that can be used within the Blazor app to resolve the tokens from [dependency injection (DI)](xref:blazor/fundamentals/dependency-injection):</span></span>

```csharp
public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

<span data-ttu-id="72c9b-113">Fügen Sie in `Startup.ConfigureServices` Dienste für Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="72c9b-113">In `Startup.ConfigureServices`, add services for:</span></span>

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

<span data-ttu-id="72c9b-114">Erstellen Sie in der Datei `_Host.cshtml` eine Instanz von `InitialApplicationState`, und übergeben Sie diese als Parameter an die App:</span><span class="sxs-lookup"><span data-stu-id="72c9b-114">In the `_Host.cshtml` file, create and instance of `InitialApplicationState` and pass it as a parameter to the app:</span></span>

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

<span data-ttu-id="72c9b-115">Lösen Sie in der `App`-Komponente (`App.razor`) den Dienst auf, und initialisieren Sie diesen mit den Daten aus dem Parameter:</span><span class="sxs-lookup"><span data-stu-id="72c9b-115">In the `App` component (`App.razor`), resolve the service and initialize it with the data from the parameter:</span></span>

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

<span data-ttu-id="72c9b-116">Fügen Sie der App einen Paketverweis für das NuGet-Paket [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) hinzu.</span><span class="sxs-lookup"><span data-stu-id="72c9b-116">Add a package reference to the app for the [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client) NuGet package.</span></span>

<span data-ttu-id="72c9b-117">Fügen Sie den Tokenanbieter in den Dienst ein, der eine sichere API-Anforderung stellt, und rufen Sie das Token ab, um die API aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="72c9b-117">In the service that makes a secure API request, inject the token provider and retrieve the token to call the API:</span></span>

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

## <a name="set-the-authentication-scheme"></a><span data-ttu-id="72c9b-118">Festlegen des Authentifizierungsschemas</span><span class="sxs-lookup"><span data-stu-id="72c9b-118">Set the authentication scheme</span></span>

<span data-ttu-id="72c9b-119">Für eine App, die mehr als eine Authentifizierungsmiddleware verwendet und daher über mehr als ein Authentifizierungsschema verfügt, kann das von Blazor verwendete Schema explizit in der Endpunktkonfiguration von `Startup.Configure` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="72c9b-119">For an app that uses more than one Authentication Middleware and thus has more than one authentication scheme, the scheme that Blazor uses can be explicitly set in the endpoint configuration of `Startup.Configure`.</span></span> <span data-ttu-id="72c9b-120">Im folgenden Beispiel wird das Azure Active Directory-Schema festgelegt:</span><span class="sxs-lookup"><span data-stu-id="72c9b-120">The following example sets the Azure Active Directory scheme:</span></span>

```csharp
endpoints.MapBlazorHub().RequireAuthorization(
    new AuthorizeAttribute 
    {
        AuthenticationSchemes = AzureADDefaults.AuthenticationScheme
    });
```

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="72c9b-121">Verwenden von OpenID Connect v2.0-Endpunkten (OIDC)</span><span class="sxs-lookup"><span data-stu-id="72c9b-121">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="72c9b-122">Die Authentifizierungsbibliothek sowie Blazor-Vorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC).</span><span class="sxs-lookup"><span data-stu-id="72c9b-122">The authentication library and Blazor templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="72c9b-123">Zum Verwenden eines 2.0-Endpunkts konfigurieren Sie die Option <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> unter <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="72c9b-123">To use a v2.0 endpoint, configure the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority?displayProperty=nameWithType> option in the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    }
```

<span data-ttu-id="72c9b-124">Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="72c9b-124">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="72c9b-125">Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest.</span><span class="sxs-lookup"><span data-stu-id="72c9b-125">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="72c9b-126">Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> oder in der Datei mit den App-Einstellungen mit dem <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority>-Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="72c9b-126">Either set the property in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions> or in the app settings file with the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> key.</span></span>

### <a name="code-changes"></a><span data-ttu-id="72c9b-127">Codeänderungen</span><span class="sxs-lookup"><span data-stu-id="72c9b-127">Code changes</span></span>

* <span data-ttu-id="72c9b-128">Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0.</span><span class="sxs-lookup"><span data-stu-id="72c9b-128">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="72c9b-129">Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span><span class="sxs-lookup"><span data-stu-id="72c9b-129">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)</span></span> <span data-ttu-id="72c9b-130">in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="72c9b-130">in the Azure documentation.</span></span>
* <span data-ttu-id="72c9b-131">Da Ressourcen in Bereichs-URIs für 2.0-Endpunkte angegeben werden, entfernen Sie die Einstellung der <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType>-Eigenschaft in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span><span class="sxs-lookup"><span data-stu-id="72c9b-131">Since resources are specified in scope URIs for v2.0 endpoints, remove the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Resource?displayProperty=nameWithType> property setting in <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions>:</span></span>

  ```csharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options => 
      {
          ...
          options.Resource = "...";    // REMOVE THIS LINE
          ...
      }
  ```

  <span data-ttu-id="72c9b-132">Weitere Informationen finden Sie in der Azure-Dokumentation unter [Geltungsbereiche im Gegensatz zu Ressourcen](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources).</span><span class="sxs-lookup"><span data-stu-id="72c9b-132">For more information, see [Scopes, not resources](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources) in the Azure documentation.</span></span>

### <a name="app-id-uri"></a><span data-ttu-id="72c9b-133">App-ID-URI</span><span class="sxs-lookup"><span data-stu-id="72c9b-133">App ID URI</span></span>

* <span data-ttu-id="72c9b-134">Bei der Verwendung von v2.0-Endpunkten definieren APIs einen *`App ID URI`* , der einen eindeutigen Bezeichner für die API darstellen soll.</span><span class="sxs-lookup"><span data-stu-id="72c9b-134">When using v2.0 endpoints, APIs define an *`App ID URI`*, which is meant to represent a unique identifier for the API.</span></span>
* <span data-ttu-id="72c9b-135">Alle Bereiche enthalten den App-ID-URI als Präfix, und die v2.0-Endpunkte geben Zugriffstoken mit dem App-ID-URI als Zielgruppe aus.</span><span class="sxs-lookup"><span data-stu-id="72c9b-135">All scopes include the App ID URI as a prefix, and v2.0 endpoints emit access tokens with the App ID URI as the audience.</span></span>
* <span data-ttu-id="72c9b-136">Bei Verwendung von v2.0-Endpunkten ändert sich die in der Server-API konfigurierte Client-ID von der API-Anwendungs-ID (Client-ID) in den App-ID-URI.</span><span class="sxs-lookup"><span data-stu-id="72c9b-136">When using V2.0 endpoints, the client ID configured in the Server API changes from the API Application ID (Client ID) to the App ID URI.</span></span>

<span data-ttu-id="72c9b-137">`appsettings.json`:</span><span class="sxs-lookup"><span data-stu-id="72c9b-137">`appsettings.json`:</span></span>

```json
{
  "AzureAd": {
    ...
    "ClientId": "https://{TENANT}.onmicrosoft.com/{APP NAME}"
    ...
  }
}
```

<span data-ttu-id="72c9b-138">Sie finden den zu verwendenden App-ID-URI in der Beschreibung zur App-Registrierung des OIDC-Anbieters.</span><span class="sxs-lookup"><span data-stu-id="72c9b-138">You can find the App ID URI to use in the OIDC provider app registration description.</span></span>
