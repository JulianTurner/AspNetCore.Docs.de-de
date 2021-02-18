---
title: Verwenden der Graph-API mit der Blazor WebAssembly von ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie die Graph-API mit Blazor-WebAssembly-Apps verwenden.
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
uid: blazor/security/webassembly/graph-api
ms.openlocfilehash: 997d4dec05ddb6b9d0acb5ed36a6510c0836a4fb
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280496"
---
# <a name="use-graph-api-with-aspnet-core-blazor-webassembly"></a><span data-ttu-id="b52a0-103">Verwenden der Graph-API mit der Blazor WebAssembly von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b52a0-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b52a0-104">Die [Microsoft Graph-API](/graph/use-the-api) ist eine RESTful-Web-API, mit der Blazor und andere .NET Framework-Apps auf Ressourcen in Microsoft-Clouddiensten zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="b52a0-104">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="b52a0-105">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b52a0-105">Graph SDK</span></span>

<span data-ttu-id="b52a0-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) sind darauf ausgelegt, einfacher hochwertige, effiziente und robuste Anwendungen, die auf Microsoft Graph zugreifen, erstellen zu können.</span><span class="sxs-lookup"><span data-stu-id="b52a0-106">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="b52a0-107">Die Beispiele in diesem Abschnitt erfordern Paketverweise auf die folgenden Pakete in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App:</span><span class="sxs-lookup"><span data-stu-id="b52a0-107">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="b52a0-108">In den Unterabschnitten dieses Artikels werden die folgenden Hilfsprogrammklassen und Konfigurationen verwendet:</span><span class="sxs-lookup"><span data-stu-id="b52a0-108">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="b52a0-109">Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b52a0-109">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="b52a0-110">Anpassen von Benutzeransprüchen mit dem Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b52a0-110">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="b52a0-111">Nach dem Hinzufügen der Gültigkeitsbereiche der Microsoft Graph-API im AAD-Bereich im Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="b52a0-111">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="b52a0-112">Fügen Sie der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung die `GraphClientExtensions.cs`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="b52a0-112">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="b52a0-113">Stellen Sie die erforderlichen Bereiche für die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>-Eigenschaft der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in der `AuthenticateRequestAsync`-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="b52a0-113">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="b52a0-114">Im folgenden Beispiel wird der `User.Read`-Bereich angegeben, um ihn an die Beispiele in späteren Abschnitten dieses Artikels anzupassen.</span><span class="sxs-lookup"><span data-stu-id="b52a0-114">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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
        public NoOpGraphAuthenticationProvider(IAccessTokenProvider tokenProvider)
        {
            TokenProvider = tokenProvider;
        }

        public IAccessTokenProvider TokenProvider { get; }

        public async Task AuthenticateRequestAsync(HttpRequestMessage request)
        {
            var result = await TokenProvider.RequestAccessToken(
                new AccessTokenRequestOptions()
                {
                    Scopes = new[] { "{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}" }
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
        private readonly HttpClient http;

        public HttpClientHttpProvider(HttpClient http)
        {
            this.http = http;
        }

        public ISerializer Serializer { get; } = new Serializer();

        public TimeSpan OverallTimeout { get; set; } = TimeSpan.FromSeconds(300);

        public void Dispose()
        {
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request)
        {
            return http.SendAsync(request);
        }

        public Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, 
            HttpCompletionOption completionOption, 
            CancellationToken cancellationToken)
        {
            return http.SendAsync(request, completionOption, cancellationToken);
        }
    }
}
```

<span data-ttu-id="b52a0-115">Die Bereichsplatzhalter `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` im vorangehenden Code stellen einen oder mehrere zulässige Bereiche dar.</span><span class="sxs-lookup"><span data-stu-id="b52a0-115">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="b52a0-116">Legen Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels `Scopes` auf ein Zeichenfolgenarray eines Bereichs für `User.Read` fest:</span><span class="sxs-lookup"><span data-stu-id="b52a0-116">For example, set `Scopes` to a string array of one scope for `User.Read` for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="b52a0-117">Fügen Sie in `Program.Main` (`Program.cs`) die Graph-Clientdienste und die Konfiguration mit der `AddGraphClient`-Erweiterungsmethode hinzu:</span><span class="sxs-lookup"><span data-stu-id="b52a0-117">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient("{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}");
```

<span data-ttu-id="b52a0-118">Die Bereichsplatzhalter `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` im vorangehenden Code stellen einen oder mehrere zulässige Bereiche dar.</span><span class="sxs-lookup"><span data-stu-id="b52a0-118">The scope placeholders `"{SCOPE 1}", "{SCOPE 2}", ... "{SCOPE X}"` in the preceding code represent one or more permitted scopes.</span></span> <span data-ttu-id="b52a0-119">Übergeben Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels den Bereich `User.Read` an `AddGraphClient`:</span><span class="sxs-lookup"><span data-stu-id="b52a0-119">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="b52a0-120">Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b52a0-120">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="b52a0-121">In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="b52a0-121">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="b52a0-122">In der folgenden `GraphExample`-Komponente wird ein injizierter `GraphServiceClient` verwendet, um die Daten zum AAD-Profil des Benutzers abzurufen und seine Mobiltelefonnummer anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="b52a0-122">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="b52a0-123">Anpassen von Benutzeransprüchen mit dem Graph SDK</span><span class="sxs-lookup"><span data-stu-id="b52a0-123">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="b52a0-124">In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="b52a0-124">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="b52a0-125">Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer eines Benutzers aus der Mobiltelefonnummer in seinem AAD-Benutzerprofil.</span><span class="sxs-lookup"><span data-stu-id="b52a0-125">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="b52a0-126">Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="b52a0-126">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="b52a0-127">In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar.</span><span class="sxs-lookup"><span data-stu-id="b52a0-127">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="b52a0-128">Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.</span><span class="sxs-lookup"><span data-stu-id="b52a0-128">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="b52a0-129">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b52a0-129">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="b52a0-130">Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:</span><span class="sxs-lookup"><span data-stu-id="b52a0-130">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="b52a0-131">Benannter Client mit Graph-API</span><span class="sxs-lookup"><span data-stu-id="b52a0-131">Named client with Graph API</span></span>

<span data-ttu-id="b52a0-132">In den Beispielen in diesem Abschnitt wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API verwendet, um die Mobiltelefonnummer eines Benutzers zum Verarbeiten eines Anrufs abzurufen.</span><span class="sxs-lookup"><span data-stu-id="b52a0-132">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="b52a0-133">Die Beispiele in diesem Abschnitt erfordern einen Paketverweis auf [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App.</span><span class="sxs-lookup"><span data-stu-id="b52a0-133">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="b52a0-134">Erstellen Sie die folgende Klasse und Projektkonfiguration für die Arbeit mit der Graph-API.</span><span class="sxs-lookup"><span data-stu-id="b52a0-134">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="b52a0-135">In den nächsten Unterabschnitten dieses Artikels werden die folgenden Klassen und Konfigurationen verwendet:</span><span class="sxs-lookup"><span data-stu-id="b52a0-135">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="b52a0-136">Aufrufen der Graph-API aus einer Komponente</span><span class="sxs-lookup"><span data-stu-id="b52a0-136">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="b52a0-137">Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client</span><span class="sxs-lookup"><span data-stu-id="b52a0-137">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="b52a0-138">Nachdem Sie die Bereiche der Microsoft Graph-API im AAD-Bereich des Azure-Portals hinzugefügt haben, stellen Sie dem für die App konfigurierten Handler die erforderlichen Bereiche für die Graph-API bereit.</span><span class="sxs-lookup"><span data-stu-id="b52a0-138">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="b52a0-139">Im folgenden Beispiel wird der Handler für den Bereich `User.Read` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="b52a0-139">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="b52a0-140">Sie können noch weitere Bereiche hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b52a0-140">Additional scopes can be added.</span></span>

<span data-ttu-id="b52a0-141">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="b52a0-141">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="b52a0-142">Konfigurieren Sie in `Program.Main` (`Program.cs`) den benannten <xref:System.Net.Http.HttpClient> für die Graph-API:</span><span class="sxs-lookup"><span data-stu-id="b52a0-142">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="b52a0-143">Aufrufen der Graph-API aus einer Komponente</span><span class="sxs-lookup"><span data-stu-id="b52a0-143">Call Graph API from a component</span></span>

<span data-ttu-id="b52a0-144">In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b52a0-144">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="b52a0-145">In einer Razor-Komponente:</span><span class="sxs-lookup"><span data-stu-id="b52a0-145">In a Razor component:</span></span>

* <span data-ttu-id="b52a0-146">Erstellen Sie einen <xref:System.Net.Http.HttpClient> für die Graph-API, und geben Sie eine Anforderung für die Benutzerprofilprofildaten aus.</span><span class="sxs-lookup"><span data-stu-id="b52a0-146">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="b52a0-147">Die `UserInfo.cs`-Klasse legt die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b52a0-147">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="b52a0-148">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="b52a0-148">`Pages/CallUser.razor`:</span></span>

```razor
@page "/CallUser"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject ILogger<CallUser> Logger

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
                // Use userInfo.MobilePhone and callInfo.Message to make a call

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

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="b52a0-149">Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client</span><span class="sxs-lookup"><span data-stu-id="b52a0-149">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="b52a0-150">In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b52a0-150">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="b52a0-151">Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer des Benutzers aus den Daten zur Mobiltelefonnummer in seinem AAD-Benutzerprofil.</span><span class="sxs-lookup"><span data-stu-id="b52a0-151">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="b52a0-152">Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="b52a0-152">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="b52a0-153">Fügen Sie der App eine `UserInfo.cs`-Klasse hinzu, und legen Sie die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="b52a0-153">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="b52a0-154">In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar.</span><span class="sxs-lookup"><span data-stu-id="b52a0-154">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="b52a0-155">Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.</span><span class="sxs-lookup"><span data-stu-id="b52a0-155">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="b52a0-156">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="b52a0-156">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="b52a0-157">Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:</span><span class="sxs-lookup"><span data-stu-id="b52a0-157">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="b52a0-158">Das obige Beispiel gilt für eine App, die die AAD-Authentifizierung mit MSAL verwendet.</span><span class="sxs-lookup"><span data-stu-id="b52a0-158">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="b52a0-159">Ähnliche Muster gibt es für die OIDC- und die API-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="b52a0-159">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="b52a0-160">Weitere Informationen finden Sie in den Beispielen unter [Anpassen des Benutzers mit einem Nutzdatenanspruch](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).</span><span class="sxs-lookup"><span data-stu-id="b52a0-160">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
