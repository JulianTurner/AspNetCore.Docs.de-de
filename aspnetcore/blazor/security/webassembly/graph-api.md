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
ms.openlocfilehash: 6464b80d52837e7fe35efe5daac2193b77e21c84
ms.sourcegitcommit: e087b6a38e3d38625ebb567a973e75b4d79547b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637651"
---
# <a name="use-graph-api-with-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="2b2fa-103">Verwenden der Graph-API mit der Blazor WebAssembly von ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b2fa-103">Use Graph API with ASP.NET Core Blazor WebAssembly</span></span>

<span data-ttu-id="2b2fa-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b2fa-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2b2fa-105">Die [Microsoft Graph-API](/graph/use-the-api) ist eine RESTful-Web-API, mit der Blazor und andere .NET Framework-Apps auf Ressourcen in Microsoft-Clouddiensten zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-105">[Microsoft Graph API](/graph/use-the-api) is a RESTful web API that enables Blazor and other .NET Framework apps to access Microsoft Cloud service resources.</span></span>

## <a name="graph-sdk"></a><span data-ttu-id="2b2fa-106">Graph SDK</span><span class="sxs-lookup"><span data-stu-id="2b2fa-106">Graph SDK</span></span>

<span data-ttu-id="2b2fa-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) sind darauf ausgelegt, einfacher hochwertige, effiziente und robuste Anwendungen, die auf Microsoft Graph zugreifen, erstellen zu können.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-107">[Microsoft Graph SDKs](/graph/sdks/sdks-overview) are designed to simplify building high-quality, efficient, and resilient applications that access Microsoft Graph.</span></span>

<span data-ttu-id="2b2fa-108">Die Beispiele in diesem Abschnitt erfordern Paketverweise auf die folgenden Pakete in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-108">The examples in this section require package references for the following packages in the project file of the standalone or *`Client`* app's project file:</span></span>

* [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)
* [`Microsoft.Graph`](https://www.nuget.org/packages/Microsoft.Graph)

<span data-ttu-id="2b2fa-109">In den Unterabschnitten dieses Artikels werden die folgenden Hilfsprogrammklassen und Konfigurationen verwendet:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-109">The following utility classes and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="2b2fa-110">Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="2b2fa-110">Call Graph API from a component using the Graph SDK</span></span>](#call-graph-api-from-a-component-using-the-graph-sdk)
* [<span data-ttu-id="2b2fa-111">Anpassen von Benutzeransprüchen mit dem Graph SDK</span><span class="sxs-lookup"><span data-stu-id="2b2fa-111">Customize user claims with the Graph SDK</span></span>](#customize-user-claims-with-the-graph-sdk)

<span data-ttu-id="2b2fa-112">Nach dem Hinzufügen der Gültigkeitsbereiche der Microsoft Graph-API im AAD-Bereich im Azure-Portal:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-112">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal:</span></span>

* <span data-ttu-id="2b2fa-113">Fügen Sie der eigenständigen App oder der *`Client`* -App einer gehosteten Blazor-Lösung die `GraphClientExtensions.cs`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-113">Add the following `GraphClientExtensions.cs` class to the standalone app or *`Client`* app of a hosted Blazor solution.</span></span>
* <span data-ttu-id="2b2fa-114">Stellen Sie die erforderlichen Bereiche für die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes>-Eigenschaft der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in der `AuthenticateRequestAsync`-Methode bereit.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-114">Provide the required scopes to the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions.Scopes> property of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenRequestOptions> in the `AuthenticateRequestAsync` method.</span></span> <span data-ttu-id="2b2fa-115">Im folgenden Beispiel wird der `User.Read`-Bereich angegeben, um ihn an die Beispiele in späteren Abschnitten dieses Artikels anzupassen.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-115">In the following example, the `User.Read` scope is specified to match the examples in later sections of this article.</span></span>

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

<span data-ttu-id="2b2fa-116">Der Platzhalter `{STRING ARRAY OF SCOPES}` im vorangehenden Code ist ein Zeichenfolgenarray der zulässigen Bereiche.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-116">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="2b2fa-117">Legen Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels `Scopes` auf den Bereich `User.Read` fest:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-117">For example, set `Scopes` to the `User.Read` scope for the examples in the following sections of this article:</span></span>

```csharp
Scopes = new[] { "https://graph.microsoft.com/User.Read" }
```

<span data-ttu-id="2b2fa-118">Fügen Sie in `Program.Main` (`Program.cs`) die Graph-Clientdienste und die Konfiguration mit der `AddGraphClient`-Erweiterungsmethode hinzu:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-118">In `Program.Main` (`Program.cs`), add the Graph client services and configuration with the `AddGraphClient` extension method:</span></span>

```csharp
builder.Services.AddGraphClient({STRING ARRAY OF SCOPES});
```

<span data-ttu-id="2b2fa-119">Der Platzhalter `{STRING ARRAY OF SCOPES}` im vorangehenden Code ist ein Zeichenfolgenarray der zulässigen Bereiche.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-119">The placeholder `{STRING ARRAY OF SCOPES}` in the preceding code is a string array of the permitted scopes.</span></span> <span data-ttu-id="2b2fa-120">Übergeben Sie beispielsweise für die Beispiele in den folgenden Abschnitten dieses Artikels den Bereich `User.Read` an `AddGraphClient`:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-120">For example, pass the `User.Read` scope to `AddGraphClient` for the examples in the following sections of this article:</span></span>

```csharp
builder.Services.AddGraphClient("https://graph.microsoft.com/User.Read");
```

### <a name="call-graph-api-from-a-component-using-the-graph-sdk"></a><span data-ttu-id="2b2fa-121">Aufrufen der Graph-API aus einer Komponente mithilfe des Graph SDK</span><span class="sxs-lookup"><span data-stu-id="2b2fa-121">Call Graph API from a component using the Graph SDK</span></span>

<span data-ttu-id="2b2fa-122">In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-122">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span> <span data-ttu-id="2b2fa-123">In der folgenden `GraphExample`-Komponente wird ein injizierter `GraphServiceClient` verwendet, um die Daten zum AAD-Profil des Benutzers abzurufen und seine Mobiltelefonnummer anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-123">The following `GraphExample` component uses an injected `GraphServiceClient` to obtain the user's AAD profile data and display their mobile phone number:</span></span>

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

### <a name="customize-user-claims-with-the-graph-sdk"></a><span data-ttu-id="2b2fa-124">Anpassen von Benutzeransprüchen mit dem Graph SDK</span><span class="sxs-lookup"><span data-stu-id="2b2fa-124">Customize user claims with the Graph SDK</span></span>

<span data-ttu-id="2b2fa-125">In diesem Abschnitt werden die [Hilfsprogrammklassen (`GraphClientExtensions.cs`)](#graph-sdk) verwendet, die weiter oben in diesem Artikel beschrieben wurden.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-125">This section uses the [utility classes (`GraphClientExtensions.cs`)](#graph-sdk) described earlier in this article.</span></span>

<span data-ttu-id="2b2fa-126">Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer eines Benutzers aus der Mobiltelefonnummer in seinem AAD-Benutzerprofil.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-126">In the following example, the app creates a mobile phone number claim for a user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="2b2fa-127">Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-127">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="2b2fa-128">In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-128">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="2b2fa-129">Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-129">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="2b2fa-130">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-130">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="2b2fa-131">Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-131">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

## <a name="named-client-with-graph-api"></a><span data-ttu-id="2b2fa-132">Benannter Client mit Graph-API</span><span class="sxs-lookup"><span data-stu-id="2b2fa-132">Named client with Graph API</span></span>

<span data-ttu-id="2b2fa-133">In den Beispielen in diesem Abschnitt wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API verwendet, um die Mobiltelefonnummer eines Benutzers zum Verarbeiten eines Anrufs abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-133">The examples in this section use a named <xref:System.Net.Http.HttpClient> for Graph API to obtain a user's mobile phone number to process a call.</span></span>

<span data-ttu-id="2b2fa-134">Die Beispiele in diesem Abschnitt erfordern einen Paketverweis auf [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in der Projektdatei der eigenständigen App oder der Projektdatei der *`Client`* -App.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-134">The examples in this section require a package reference for [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) in the project file of the standalone or *`Client`* app's project file.</span></span>

<span data-ttu-id="2b2fa-135">Erstellen Sie die folgende Klasse und Projektkonfiguration für die Arbeit mit der Graph-API.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-135">Create the following class and project configuration for working with Graph API.</span></span> <span data-ttu-id="2b2fa-136">In den nächsten Unterabschnitten dieses Artikels werden die folgenden Klassen und Konfigurationen verwendet:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-136">The following class and configuration are used in each of the following subsections of this article:</span></span>

* [<span data-ttu-id="2b2fa-137">Aufrufen der Graph-API aus einer Komponente</span><span class="sxs-lookup"><span data-stu-id="2b2fa-137">Call Graph API from a component</span></span>](#call-graph-api-from-a-component)
* [<span data-ttu-id="2b2fa-138">Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client</span><span class="sxs-lookup"><span data-stu-id="2b2fa-138">Customize user claims with Graph API and a named client</span></span>](#customize-user-claims-with-graph-api-and-a-named-client)

<span data-ttu-id="2b2fa-139">Nachdem Sie die Bereiche der Microsoft Graph-API im AAD-Bereich des Azure-Portals hinzugefügt haben, stellen Sie dem für die App konfigurierten Handler die erforderlichen Bereiche für die Graph-API bereit.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-139">After adding the Microsoft Graph API scopes in the AAD area of the Azure portal, provide the required scopes to the app's configured handler for Graph API.</span></span> <span data-ttu-id="2b2fa-140">Im folgenden Beispiel wird der Handler für den Bereich `User.Read` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-140">The following example configures the handler for the `User.Read` scope.</span></span> <span data-ttu-id="2b2fa-141">Sie können noch weitere Bereiche hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-141">Additional scopes can be added.</span></span>

<span data-ttu-id="2b2fa-142">`GraphAuthorizationMessageHandler.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-142">`GraphAuthorizationMessageHandler.cs`:</span></span>

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

<span data-ttu-id="2b2fa-143">Konfigurieren Sie in `Program.Main` (`Program.cs`) den benannten <xref:System.Net.Http.HttpClient> für die Graph-API:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-143">In `Program.Main` (`Program.cs`), configure the named <xref:System.Net.Http.HttpClient> for Graph API:</span></span>

```csharp
builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();
```

### <a name="call-graph-api-from-a-component"></a><span data-ttu-id="2b2fa-144">Aufrufen der Graph-API aus einer Komponente</span><span class="sxs-lookup"><span data-stu-id="2b2fa-144">Call Graph API from a component</span></span>

<span data-ttu-id="2b2fa-145">In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-145">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="2b2fa-146">In einer Razor-Komponente:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-146">In a Razor component:</span></span>

* <span data-ttu-id="2b2fa-147">Erstellen Sie einen <xref:System.Net.Http.HttpClient> für die Graph-API, und geben Sie eine Anforderung für die Benutzerprofilprofildaten aus.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-147">Create an <xref:System.Net.Http.HttpClient> for Graph API and issue a request for the user's profile data.</span></span>
* <span data-ttu-id="2b2fa-148">Die `UserInfo.cs`-Klasse legt die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-148">The `UserInfo.cs` class designates the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties.</span></span>

<span data-ttu-id="2b2fa-149">`Pages/CallUser.razor`:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-149">`Pages/CallUser.razor`:</span></span>

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
> <span data-ttu-id="2b2fa-150">Im obigen Beispiel implementiert der Entwickler den benutzerdefinierten `ICallProcessor` (`CallProcessor`), um automatisierte Anrufe zunächst in die Warteschlange zu stellen und dann zu platzieren.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-150">In the preceding example, the developer implements the custom `ICallProcessor` (`CallProcessor`) to queue and then place automated calls.</span></span>

### <a name="customize-user-claims-with-graph-api-and-a-named-client"></a><span data-ttu-id="2b2fa-151">Anpassen von Benutzeransprüchen mit der Graph-API und einem benannten Client</span><span class="sxs-lookup"><span data-stu-id="2b2fa-151">Customize user claims with Graph API and a named client</span></span>

<span data-ttu-id="2b2fa-152">In diesem Abschnitt werden der [Meldungshandler für die Graph-Autorisierung (`GraphAuthorizationMessageHandler.cs`) und die Ergänzungen an `Program.Main` in der App](#named-client-with-graph-api) verwendet, die weiter oben in diesem Artikel beschrieben wurden. Damit wird ein benannter <xref:System.Net.Http.HttpClient> für die Graph-API bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-152">This section uses the [Graph Authorization Message Handler (`GraphAuthorizationMessageHandler.cs`) and `Program.Main` additions to the app](#named-client-with-graph-api) described earlier in this article, which provides a named <xref:System.Net.Http.HttpClient> for Graph API.</span></span>

<span data-ttu-id="2b2fa-153">Im folgenden Beispiel erstellt die App einen Anspruch für die Mobiltelefonnummer des Benutzers aus den Daten zur Mobiltelefonnummer in seinem AAD-Benutzerprofil.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-153">In the following example, the app creates a mobile phone number claim for the user from their AAD user profile's mobile phone number.</span></span> <span data-ttu-id="2b2fa-154">Für die App muss der Graph-API-Bereich `User.Read` in AAD konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-154">The app must have the `User.Read` Graph API scope configured in AAD.</span></span>

<span data-ttu-id="2b2fa-155">Fügen Sie der App eine `UserInfo.cs`-Klasse hinzu, und legen Sie die erforderlichen Benutzerprofileigenschaften mit dem <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute>-Attribut und dem JSON-Namen fest, der von AAD für diese Eigenschaften verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-155">Add a `UserInfo.cs` class to the app and designate the required user profile properties with the <xref:System.Text.Json.Serialization.JsonPropertyNameAttribute> attribute and the JSON name used by AAD for those properties:</span></span>

```csharp
using System.Text.Json.Serialization;

public class UserInfo
{
    [JsonPropertyName("mobilePhone")]
    public string MobilePhone { get; set; }
}
```

<span data-ttu-id="2b2fa-156">In der folgenden benutzerdefinierten Benutzerkontofactory, stellt das <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> des Frameworks das Konto des Benutzers dar.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-156">In the following custom user account factory, the framework's <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> represents the user's account.</span></span> <span data-ttu-id="2b2fa-157">Wenn die App eine benutzerdefinierte Benutzerkontoklasse benötigt, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-157">If the app requires a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code.</span></span>

<span data-ttu-id="2b2fa-158">`CustomAccountFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-158">`CustomAccountFactory.cs`:</span></span>

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

<span data-ttu-id="2b2fa-159">Konfigurieren Sie in `Program.Main` (`Program.cs`) die MSAL-Authentifizierung für die Verwendung der benutzerdefinierten Benutzerkontofactory: Wenn die App eine benutzerdefinierte Benutzerkontoklasse verwendet, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird, tauschen Sie die benutzerdefinierte Benutzerkontoklasse im folgenden Code gegen <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> aus:</span><span class="sxs-lookup"><span data-stu-id="2b2fa-159">In `Program.Main` (`Program.cs`), configure the MSAL authentication to use the custom user account factory: If the app uses a custom user account class that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>, swap the custom user account class for <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> in the following code:</span></span>

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

<span data-ttu-id="2b2fa-160">Das obige Beispiel gilt für eine App, die die AAD-Authentifizierung mit MSAL verwendet.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-160">The preceding example is for an app that uses AAD authentication with MSAL.</span></span> <span data-ttu-id="2b2fa-161">Ähnliche Muster gibt es für die OIDC- und die API-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="2b2fa-161">Similar patterns exist for OIDC and API authentication.</span></span> <span data-ttu-id="2b2fa-162">Weitere Informationen finden Sie in den Beispielen unter [Anpassen des Benutzers mit einem Nutzdatenanspruch](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim).</span><span class="sxs-lookup"><span data-stu-id="2b2fa-162">For more information, see the examples in [Customize the user with a payload claim](xref:blazor/security/webassembly/additional-scenarios#customize-the-user-with-a-payload-claim) section.</span></span>
