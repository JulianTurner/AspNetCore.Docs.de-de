---
title: Verwenden der Graph-API mit der Blazor WebAssembly von ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie die Graph-API mit Blazor-WebAssembly-Apps verwenden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 3c77a8b39562c0145d1441cfdfe1dcf57aa3a613
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92692069"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a>Verwenden der Graph-API mit der Blazor WebAssembly von ASP.NET Core

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-5.0"

Die [Microsoft Graph-API](/graph/use-the-api) ist eine RESTful-Web-API, mit der Blazor und andere .NET Framework-Apps auf Ressourcen in Microsoft-Clouddiensten zugreifen können.

## <a name="graph-sdk"></a>Graph SDK

[Microsoft Graph SDKs](/graph/sdks/sdks-overview) sind darauf ausgelegt, einfacher hochwertige, effiziente und robuste Anwendungen, die auf Microsoft Graph zugreifen, erstellen zu können.

Die Beispiele in diesem Abschnitt erfordern Paketverweise auf die folgenden Pakete in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App:

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

In den Unterabschnitten dieses Artikels werden die folgenden Hilfsprogrammklassen und Konfigurationen verwendet:

* [Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK](#call-graph-api-from-a-component-using-the-graph-sdk)
* [Anpassen von Benutzeransprüchen mit dem Graph SDK](#customize-user-claims-with-the-graph-sdk)

Nach dem Hinzufügen der Gültigkeitsbereiche der Microsoft Graph-API im AAD-Bereich im Azure-Portal:

* Fügen Sie der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung die `GraphClientExtensions.cs`-Klasse hinzu.
* Stellen Sie die erforderlichen Bereiche für die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>-Eigenschaft der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in der `AuthenticateRequestAsync`-Methode bereit. Im folgenden Beispiel wird der `User.Read`-Bereich angegeben, um ihn an die Beispiele in späteren Abschnitten dieses Artikels anzupassen.

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Authentication.WebAssembly.Msal.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Graph;

internal static class GraphClientExtensions
{
    public static IServiceCollection AddGraphClient(
        this IServiceCollection services, params string[] scopes)
    {
        services.Configure<RemoteAuthenticationOptions<MsalProviderOptions>>(
            options =>
            {
                foreach (var scope in scopes)
                {
                    options.ProviderOptions.AdditionalScopesToConsent.Add(scope);
                }
            });

        services.AddScoped<IAuthenticationProvider, 
            NoOpGraphAuthenticationProvider>();
        services.AddScoped<IHttpProvider, HttpClientHttpProvider>(sp => 
            new HttpClientHttpProvider(new HttpClient()));
        services.AddScoped(sp =>
        {
            return new GraphServiceClient(
                sp.GetRequiredService<IAuthenticationProvider>(),
                sp.GetRequiredService<IHttpProvider>());
        });

        return services;
    }

    private class NoOpGraphAuthenticationProvider : IAuthenticationProvider
    {
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider provider)
        {
            Provider = provider;
        }

        public IAccessTokenProvider Provider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await Provider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = {STRING ARRAY OF SCOPES}
                });

            if (result.TryGetToken(out var token))
            {
                request.Headers.Authorization ??= new AuthenticationHeaderValue(
                    "Bearer", token.Value);
            }
        }
    }

    private class HttpClientHttpProvider : IHttpProvider
    {
        private readonly HttpClient client;

        public HttpClientHttpProvider(HttpClient client)
        {
            this.client = client;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return client.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return client.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

Der Platzhalter `{STRING ARRAY OF SCOPES}` im vorangehenden Code ist ein Zeichenfolgenarray der zulässigen Bereiche. Legen Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels `Scopes` auf den Bereich `User.Read` fest:

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

Fügen Sie in `Program.Main` (`Program.cs`) die Graph-Clientdienste und die Konfiguration mit der `AddGraphClient`-Erweiterungsmethode hinzu:

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

Der Platzhalter `{STRING ARRAY OF SCOPES}` im vorangehenden Code ist ein Zeichenfolgenarray der zulässigen Bereiche. Übergeben Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels den Bereich `User.Read` an `AddGraphClient`:

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a>Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK

In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden. In der folgenden `GraphExample`-Komponente wird ein injizierter `GraphServiceClient` verwendet, um die Daten zum AAD-Profil des Benutzers abzurufen und seine Mobiltelefonnummer anzuzeigen:

```razor
@page "/GraphExample"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.Graph
@attribute [Authorize]
@inject GraphServiceClient GraphClient

<h3>Graph Client Example</h3>

@if (user != null)
{
    <p>Mobile Phone: @user.MobilePhone</p>
}

@code {
    private User user;

    protected async override Task OnInitializedAsync()
    {
        var request = GraphClient.Me.Request();
        user = await request.GetAsync();
    }
}
```

### <a name="customize-user-claims-with-the-graph-sdk"></a>Anpassen von Benutzeransprüchen mit dem Graph SDK

In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden.

Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer eines Benutzers aus der Mobiltelefonnummer in seinem AAD-Benutzerprofil. Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.

In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar. Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.

`CustomAccountFactory.cs`:

```csharp
using System;
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Logging;
using Microsoft.Graph;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IServiceProvider serviceProvider;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IServiceProvider serviceProvider,
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.serviceProvider = serviceProvider;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var graphClient = ActivatorUtilities
                    .CreateInstance<GraphServiceClient>(serviceProvider);
                var request = graphClient.Me.Request();
                var user = await request.GetAsync();

                if (user != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        user.MobilePhone));
                }
            }
            catch (ServiceException exception)
            {
                logger.LogError("Graph API service failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.Extensions.Configuration;

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
        options.ProviderOptions.DefaultAccessTokenScopes.Add(
            "https://graph.microsoft.com/User.Read");
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

::: moniker-end

## <a name="named-client-with-graph-api"></a>Benannter Client mit Graph-API

In den Beispielen in diesem Abschnitt wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API verwendet, um die Mobiltelefonnummer eines Benutzers zum Verarbeiten eines Anrufs abzurufen.

Die Beispiele in diesem Abschnitt erfordern einen Paketverweis auf [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App.

Erstellen Sie die folgende Klasse und Projektkonfiguration für die Arbeit mit der Graph-API. In den nächsten Unterabschnitten dieses Artikels werden die folgenden Klassen und Konfigurationen verwendet:

* [Aufrufen der Graph-API aus einer Komponente](#call-graph-api-from-a-component)
* [Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client](#customize-user-claims-with-graph-api-and-a-named-client)

Nachdem Sie die Bereiche der Microsoft Graph-API im AAD-Bereich des Azure-Portals hinzugefügt haben, stellen Sie dem für die App konfigurierten Handler die erforderlichen Bereiche für die Graph-API bereit. Im folgenden Beispiel wird der Handler für den Bereich `User.Read` konfiguriert. Sie können noch weitere Bereiche hinzufügen.

`GraphAuthorizationMessageHandler.cs`:

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read" });
    }
}
```

Konfigurieren Sie in `Program.Main` (`Program.cs`) den benannten <xref:System.Net.Http.HttpClient> für die Graph-API:

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a>Aufrufen der Graph-API aus einer Komponente

In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.

In einer Razor-Komponente:

* Erstellen Sie einen <xref:System.Net.Http.HttpClient> für die Graph-API, und geben Sie eine Anforderung für die Benutzerprofilprofildaten aus.
* Die `UserInfo.cs`-Klasse legt die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird.

`Pages/CallUser.razor`:

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger
@inject ICallProcessor CallProcessor

<h3>Call User</h3>

<EditForm Model="@callInfo" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Message:
            <InputTextArea @bind-Value="callInfo.Message" />
        </label>
    </p>

    <button type="submit">Place call</button>

    <p>
        @formStatus
    </p>
</EditForm>

@code {
    private string formStatus;
    private CallInfo callInfo = new CallInfo();

    private async Task HandleValidSubmit()
    {
        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                Scopes = new[] { "https://graph.microsoft.com/User.Read" }
            });

        if (tokenResult.TryGetToken(out var token))
        {
            var client = ClientFactory.CreateClient("GraphAPI");

            var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

            if (userInfo != null)
            {
                CallProcessor.Send(userInfo.MobilePhone, callInfo.Message);

                formStatus = "Form successfully processed.";
                Logger.LogInformation(
                    $"Form successfully processed at {DateTime.UtcNow}. " +
                    $"Mobile Phone: {userInfo.MobilePhone}");
            }
        }
        else
        {
            formStatus = "There was a problem processing the form.";
            Logger.LogError("Token failure");
        }
    }

    private class CallInfo
    {
        [Required]
        [StringLength(1000, ErrorMessage = "Message too long (1,000 char limit)")]
        public string Message { get; set; }
    }

    private class UserInfo
    {
        [JsonPropertyName("mobilePhone")]
        public string MobilePhone { get; set; }
    }
}
```

> [!NOTE]
> Im obigen Beispiel implementiert der Entwickler den benutzerdefinierten `ICallProcessor` (`CallProcessor`), um automatisierte Anrufe zunächst in die Warteschlange zu stellen und dann zu platzieren.

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a>Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client

In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.

Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer des Benutzers aus den Daten zur Mobiltelefonnummer in seinem AAD-Benutzerprofil. Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.

Fügen Sie der App eine `UserInfo.cs`-Klasse hinzu, und legen Sie die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird:

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar. Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.

`CustomAccountFactory.cs`:

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;
using Microsoft.Extensions.Logging;

public class CustomAccountFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    private readonly ILogger<CustomAccountFactory> logger;
    private readonly IHttpClientFactory clientFactory;

    public CustomAccountFactory(IAccessTokenProviderAccessor accessor, 
        IHttpClientFactory clientFactory, 
        ILogger<CustomAccountFactory> logger)
        : base(accessor)
    {
        this.clientFactory = clientFactory;
        this.logger = logger;
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            var userIdentity = (ClaimsIdentity)initialUser.Identity;

            try
            {
                var client = clientFactory.CreateClient("GraphAPI");

                var userInfo = await client.GetFromJsonAsync<UserInfo>("v1.0/me");

                if (userInfo != null)
                {
                    userIdentity.AddClaim(new Claim("mobilephone", 
                        userInfo.MobilePhone));
                }
            }
            catch (AccessTokenNotAvailableException exception)
            {
                logger.LogError("Graph API access token failure: {Message}",
                    exception.Message);
            }
        }

        return initialUser;
    }
}
```

Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
    RemoteUserAccount>(options =>
    {
        builder.Configuration.Bind("AzureAd", 
            options.ProviderOptions.Authentication);
    })
    .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, RemoteUserAccount, 
        CustomAccountFactory>();
```

Das obige Beispiel gilt für eine App, die die AAD-Authentifizierung mit MSAL verwendet. Ähnliche Muster gibt es für die OIDC- und die API-Authentifizierung. Weitere Informationen finden Sie in den Beispielen unter [Anpassen des Benutzers mit einem Nutzdatenanspruch](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).
