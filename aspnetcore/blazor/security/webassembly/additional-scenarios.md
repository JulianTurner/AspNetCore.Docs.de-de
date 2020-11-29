---
title: Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Sie Blazor WebAssembly für zusätzliche Sicherheitsszenarios konfigurieren.
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
uid: blazor/security/webassembly/additional-scenarios
ms.openlocfilehash: bb502533bca24e82792db8814b75b16407f20339
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870385"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="3289d-103">Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3289d-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="3289d-104">Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="3289d-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="3289d-105">Anfügen von Token an ausgehende Anforderungen</span><span class="sxs-lookup"><span data-stu-id="3289d-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="3289d-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> ist ein <xref:System.Net.Http.DelegatingHandler>, der verwendet wird, um Zugriffstoken an ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="3289d-106"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> is a <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="3289d-107">Token werden mithilfe des <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Diensts abgerufen, der vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="3289d-107">Tokens are acquired using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service, which is registered by the framework.</span></span> <span data-ttu-id="3289d-108">Wenn ein Token nicht abgerufen werden kann, wird eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="3289d-108">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="3289d-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> verfügt über eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>-Methode, die verwendet werden kann, um den Benutzer zum Abrufen eines neuen Token zum Identitätsanbieter zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="3289d-109"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span>

<span data-ttu-id="3289d-110">Zur Vereinfachung stellt das Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> bereit, der mit der Basisadresse der App als autorisierte URL vorkonfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-110">For convenience, the framework provides the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> preconfigured with the app's base address as an authorized URL.</span></span> <span data-ttu-id="3289d-111">**Zugriffstoken werden nur hinzugefügt, wenn der Anforderungs-URI innerhalb des Basis-URI der APP liegt.**</span><span class="sxs-lookup"><span data-stu-id="3289d-111">**Access tokens are only added when the request URI is within the app's base URI.**</span></span> <span data-ttu-id="3289d-112">Wenn sich ausgehende Anforderungs-URIs nicht innerhalb des Basis-URI der App befinden, verwenden Sie eine [benutzerdefinierte `AuthorizationMessageHandler`-Klasse (*empfohlen*)](#custom-authorizationmessagehandler-class), oder [konfigurieren Sie `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span><span class="sxs-lookup"><span data-stu-id="3289d-112">When outgoing request URIs aren't within the app's base URI, use a [custom `AuthorizationMessageHandler` class (*recommended*)](#custom-authorizationmessagehandler-class) or [configure the `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).</span></span>

> [!NOTE]
> <span data-ttu-id="3289d-113">Zusätzlich zur Client-App-Konfiguration für den Server-API-Zugriff muss die Server-API auch Cross-Origin-Anforderungen (CORS) zulassen, wenn sich der Client und der Server nicht an derselben Basisadresse befinden.</span><span class="sxs-lookup"><span data-stu-id="3289d-113">In addition to the client app configuration for server API access, the server API must also allow cross-origin requests (CORS) when the client and the server don't reside at the same base address.</span></span> <span data-ttu-id="3289d-114">Weitere Informationen zur serverseitigen CORS-Konfiguration finden Sie im Abschnitt [Cross-Origin Resource Sharing (CORS)](#cross-origin-resource-sharing-cors) weiter unten in diesem Artikel.</span><span class="sxs-lookup"><span data-stu-id="3289d-114">For more information on server-side CORS configuration, see the [Cross-origin resource sharing (CORS)](#cross-origin-resource-sharing-cors) section later in this article.</span></span>

<span data-ttu-id="3289d-115">Siehe folgendes Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3289d-115">In the following example:</span></span>

* <span data-ttu-id="3289d-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> fügt der Dienstesammlung <xref:System.Net.Http.IHttpClientFactory> und verwandte Dienste hinzu und konfiguriert einen benannten <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="3289d-116"><xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> adds <xref:System.Net.Http.IHttpClientFactory> and related services to the service collection and configures a named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span> <span data-ttu-id="3289d-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ist die Basisadresse des Ressourcen-URI beim Senden von Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="3289d-117"><xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is the base address of the resource URI when sending requests.</span></span> <span data-ttu-id="3289d-118"><xref:System.Net.Http.IHttpClientFactory> wird vom NuGet-Paket [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="3289d-118"><xref:System.Net.Http.IHttpClientFactory> is provided by the [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) NuGet package.</span></span>
* <span data-ttu-id="3289d-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ist der <xref:System.Net.Http.DelegatingHandler>, der verwendet wird, um Zugriffstoken an ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen anzufügen.</span><span class="sxs-lookup"><span data-stu-id="3289d-119"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is the <xref:System.Net.Http.DelegatingHandler> used to attach access tokens to outgoing <xref:System.Net.Http.HttpResponseMessage> instances.</span></span> <span data-ttu-id="3289d-120">Zugriffstoken werden nur hinzugefügt, wenn der Anforderungs-URI innerhalb des Basis-URI der APP liegt.</span><span class="sxs-lookup"><span data-stu-id="3289d-120">Access tokens are only added when the request URI is within the app's base URI.</span></span>
* <span data-ttu-id="3289d-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> erstellt und konfiguriert eine <xref:System.Net.Http.HttpClient>-Instanz für ausgehende Anforderungen unter Verwendung der Konfiguration, die dem benannten <xref:System.Net.Http.HttpClient> entspricht (`ServerAPI`).</span><span class="sxs-lookup"><span data-stu-id="3289d-121"><xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> creates and configures an <xref:System.Net.Http.HttpClient> instance for outgoing requests using the configuration that corresponds to the named <xref:System.Net.Http.HttpClient> (`ServerAPI`).</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("ServerAPI", 
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("ServerAPI"));
```

<span data-ttu-id="3289d-122">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, befinden sich die Anforderungs-URIs standardmäßig im Basis-URI der App.</span><span class="sxs-lookup"><span data-stu-id="3289d-122">For a Blazor app based on the Blazor WebAssembly Hosted project template, request URIs are within the app's base URI by default.</span></span> <span data-ttu-id="3289d-123">Daher wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in einer App zugewiesen, die aus der Projektvorlage generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="3289d-123">Therefore, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in an app generated from the project template.</span></span>

<span data-ttu-id="3289d-124">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen:</span><span class="sxs-lookup"><span data-stu-id="3289d-124">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Http

...

protected override async Task OnInitializedAsync()
{
    private ExampleType[] examples;

    try
    {
        examples = 
            await Http.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");

        ...
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

### <a name="custom-authorizationmessagehandler-class"></a><span data-ttu-id="3289d-125">Benutzerdefinierte `AuthorizationMessageHandler`-Klasse</span><span class="sxs-lookup"><span data-stu-id="3289d-125">Custom `AuthorizationMessageHandler` class</span></span>

<span data-ttu-id="3289d-126">*Diese Anleitung in diesem Abschnitt wird für Client-Apps empfohlen, die ausgehende Anforderungen an URIs vornehmen, die sich nicht im Basis-URI der App befinden.*</span><span class="sxs-lookup"><span data-stu-id="3289d-126">*This guidance in this section is recommended for client apps that make outgoing requests to URIs that aren't within the app's base URI.*</span></span>

<span data-ttu-id="3289d-127">Im folgenden Beispiel erweitert eine benutzerdefinierte-Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> für die Verwendung als <xref:System.Net.Http.DelegatingHandler> für einen <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3289d-127">In the following example, a custom class extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> for use as the <xref:System.Net.Http.DelegatingHandler> for an <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="3289d-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguriert diesen Handler, um ausgehende HTTP-Anforderungen mithilfe eines Zugriffstokens zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="3289d-128"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures this handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="3289d-129">Das Zugriffstoken wird nur angefügt, wenn mindestens eine der autorisierten URLs eine Basis des Anforderungs-URIs (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-129">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span>

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public CustomAuthorizationMessageHandler(IAccessTokenProvider provider, 
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://www.example.com/base" },
            scopes: new[] { "example.read", "example.write" });
    }
}
```

<span data-ttu-id="3289d-130">In `Program.Main` (`Program.cs`) ist `CustomAuthorizationMessageHandler` als Dienst mit Gültigkeitsbereich registriert und wird als <xref:System.Net.Http.DelegatingHandler> für ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen konfiguriert, die von einem benannten <xref:System.Net.Http.HttpClient> erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="3289d-130">In `Program.Main` (`Program.cs`), `CustomAuthorizationMessageHandler` is registered as a scoped service and is configured as the <xref:System.Net.Http.DelegatingHandler> for outgoing <xref:System.Net.Http.HttpResponseMessage> instances made by a named <xref:System.Net.Http.HttpClient>:</span></span>

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

<span data-ttu-id="3289d-131">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="3289d-131">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3289d-132">Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen.</span><span class="sxs-lookup"><span data-stu-id="3289d-132">The configured <xref:System.Net.Http.HttpClient> is used to make authorized requests using the [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span> <span data-ttu-id="3289d-133">Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> erstellt wird ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)-Paket), wird <xref:System.Net.Http.HttpClient> für Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an die Server-API vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-133">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package), the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server API.</span></span> <span data-ttu-id="3289d-134">Wenn der Anforderungs-URI wie im folgenden Beispiel (`ExampleAPIMethod`) ein relativer URI ist, wird er mit der <xref:System.Net.Http.HttpClient.BaseAddress> kombiniert, wenn die Client-App die Anforderung vornimmt:</span><span class="sxs-lookup"><span data-stu-id="3289d-134">If the request URI is a relative URI, as it is in the following example (`ExampleAPIMethod`), it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress> when the client app makes the request:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private ExampleType[] examples;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            var client = ClientFactory.CreateClient("ServerAPI");

            examples = 
                await client.GetFromJsonAsync<ExampleType[]>("ExampleAPIMethod");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```

### <a name="configure-authorizationmessagehandler"></a><span data-ttu-id="3289d-135">Konfigurieren von `AuthorizationMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="3289d-135">Configure `AuthorizationMessageHandler`</span></span>

<span data-ttu-id="3289d-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mithilfe der Methode <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> mit autorisierten URLs, Bereichen und einer Rückgabe-URL konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-136"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with authorized URLs, scopes, and a return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span> <span data-ttu-id="3289d-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguriert den Handler, um ausgehende HTTP-Anforderungen mithilfe eines Zugriffstokens zu autorisieren.</span><span class="sxs-lookup"><span data-stu-id="3289d-137"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> configures the handler to authorize outbound HTTP requests using an access token.</span></span> <span data-ttu-id="3289d-138">Das Zugriffstoken wird nur angefügt, wenn mindestens eine der autorisierten URLs eine Basis des Anforderungs-URIs (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-138">The access token is only attached if at least one of the authorized URLs is a base of the request URI (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>).</span></span> <span data-ttu-id="3289d-139">Wenn der Anforderungs-URI ein relativer URI ist, wird er mit der <xref:System.Net.Http.HttpClient.BaseAddress> kombiniert.</span><span class="sxs-lookup"><span data-stu-id="3289d-139">If the request URI is a relative URI, it's combined with the <xref:System.Net.Http.HttpClient.BaseAddress>.</span></span>

<span data-ttu-id="3289d-140">Im folgenden Beispiel konfiguriert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> einen <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-140">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddScoped(sp => new HttpClient(
    sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new[] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }))
    {
        BaseAddress = new Uri("https://www.example.com/base")
    });
```

<span data-ttu-id="3289d-141">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> den folgenden Elementen standardmäßig zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="3289d-141">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="3289d-142">Der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="3289d-142">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="3289d-143">Einer URL des `authorizedUrls`-Arrays.</span><span class="sxs-lookup"><span data-stu-id="3289d-143">A URL of the `authorizedUrls` array.</span></span>

## <a name="typed-httpclient"></a><span data-ttu-id="3289d-144">Typisierter `HttpClient`</span><span class="sxs-lookup"><span data-stu-id="3289d-144">Typed `HttpClient`</span></span>

<span data-ttu-id="3289d-145">Es kann ein typisierter Client definiert werden, der alle Probleme mit dem HTTP- und Tokenabruf innerhalb einer einzelnen Klasse bearbeitet.</span><span class="sxs-lookup"><span data-stu-id="3289d-145">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="3289d-146">`WeatherForecastClient.cs`:</span><span class="sxs-lookup"><span data-stu-id="3289d-146">`WeatherForecastClient.cs`:</span></span>

```csharp
using System.Net.Http;
using System.Net.Http.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using static {APP ASSEMBLY}.Data;

public class WeatherForecastClient
{
    private readonly HttpClient http;
 
    public WeatherForecastClient(HttpClient http)
    {
        this.http = http;
    }
 
    public async Task<WeatherForecast[]> GetForecastAsync()
    {
        var forecasts = new WeatherForecast[0];

        try
        {
            forecasts = await http.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

<span data-ttu-id="3289d-147">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `using static BlazorSample.Data;`).</span><span class="sxs-lookup"><span data-stu-id="3289d-147">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `using static BlazorSample.Data;`).</span></span>

<span data-ttu-id="3289d-148">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-148">`Program.Main` (`Program.cs`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="3289d-149">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="3289d-149">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3289d-150">`FetchData`-Komponente (`Pages/FetchData.razor`):</span><span class="sxs-lookup"><span data-stu-id="3289d-150">`FetchData` component (`Pages/FetchData.razor`):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="3289d-151">Konfigurieren des `HttpClient`-Handlers</span><span class="sxs-lookup"><span data-stu-id="3289d-151">Configure the `HttpClient` handler</span></span>

<span data-ttu-id="3289d-152">Der Handler kann mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> für ausgehende HTTP-Anforderungen weiter konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-152">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="3289d-153">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-153">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

<span data-ttu-id="3289d-154">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> den folgenden Elementen standardmäßig zugewiesen:</span><span class="sxs-lookup"><span data-stu-id="3289d-154">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> is assigned to the following by default:</span></span>

* <span data-ttu-id="3289d-155">Der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span><span class="sxs-lookup"><span data-stu-id="3289d-155">The <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).</span></span>
* <span data-ttu-id="3289d-156">Einer URL des `authorizedUrls`-Arrays.</span><span class="sxs-lookup"><span data-stu-id="3289d-156">A URL of the `authorizedUrls` array.</span></span>

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="3289d-157">Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient</span><span class="sxs-lookup"><span data-stu-id="3289d-157">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="3289d-158">Wenn die Blazor WebAssembly-App normalerweise einen sicheren Standard-<xref:System.Net.Http.HttpClient> verwendet, kann die App auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen stellen, indem sie einen benannten <xref:System.Net.Http.HttpClient> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="3289d-158">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="3289d-159">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-159">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

<span data-ttu-id="3289d-160">Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="3289d-160">For a Blazor app based on the Blazor WebAssembly Hosted project template, <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) is assigned to the <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> by default.</span></span>

<span data-ttu-id="3289d-161">Die obenstehende Registrierung erfolgt zusätzlich zur bereits vorhandenen sicheren Standardregistrierung des <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3289d-161">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="3289d-162">Eine Komponente erstellt den <xref:System.Net.Http.HttpClient> aus der <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)-Paket), um nicht authentifizierte oder nicht autorisierte Anforderungen zu stellen:</span><span class="sxs-lookup"><span data-stu-id="3289d-162">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) package) to make unauthenticated or unauthorized requests:</span></span>

```razor
@inject IHttpClientFactory ClientFactory

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var client = ClientFactory.CreateClient("ServerAPI.NoAuthenticationClient");

        forecasts = await client.GetFromJsonAsync<WeatherForecast[]>(
            "WeatherForecastNoAuthentication");
    }
}
```

> [!NOTE]
> <span data-ttu-id="3289d-163">Der Controller in der Server-API (`WeatherForecastNoAuthenticationController`) für das obenstehende Beispiel ist nicht mit dem Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="3289d-163">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

<span data-ttu-id="3289d-164">Die Entscheidung, ob ein sicherer Client oder ein unsicherer Client als Standard-<xref:System.Net.Http.HttpClient>-Instanz verwendet werden soll, trifft der Entwickler.</span><span class="sxs-lookup"><span data-stu-id="3289d-164">The decision whether to use a secure client or an insecure client as the default <xref:System.Net.Http.HttpClient> instance is up to the developer.</span></span> <span data-ttu-id="3289d-165">Eine Möglichkeit, diese Entscheidung zu treffen, ist die Berücksichtigung der Anzahl der authentifizierten und nicht authentifizierten Endpunkte, die von der App kontaktiert werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-165">One way to make this decision is to consider the number of authenticated versus unauthenticated endpoints that the app contacts.</span></span> <span data-ttu-id="3289d-166">Wenn die Mehrzahl der Anforderungen der App zum Sichern von API-Endpunkten verwendet wird, verwenden Sie die authentifizierte <xref:System.Net.Http.HttpClient>-Instanz als Standard.</span><span class="sxs-lookup"><span data-stu-id="3289d-166">If the majority of the app's requests are to secure API endpoints, use the authenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span> <span data-ttu-id="3289d-167">Registrieren Sie andernfalls die nicht authentifizierte <xref:System.Net.Http.HttpClient>-Instanz als Standard.</span><span class="sxs-lookup"><span data-stu-id="3289d-167">Otherwise, register the unauthenticated <xref:System.Net.Http.HttpClient> instance as the default.</span></span>

<span data-ttu-id="3289d-168">Ein alternativer Ansatz für die Verwendung der <xref:System.Net.Http.IHttpClientFactory> ist das Erstellen eines [typisierten Clients](#typed-httpclient) für nicht authentifizierten Zugriff auf anonyme Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="3289d-168">An alternative approach to using the <xref:System.Net.Http.IHttpClientFactory> is to create a [typed client](#typed-httpclient) for unauthenticated access to anonymous endpoints.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="3289d-169">Anfordern zusätzlicher Zugriffstoken</span><span class="sxs-lookup"><span data-stu-id="3289d-169">Request additional access tokens</span></span>

<span data-ttu-id="3289d-170">Zugriffstoken können mithilfe von `IAccessTokenProvider.RequestAccessToken` manuell abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-170">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span> <span data-ttu-id="3289d-171">Im folgenden Beispiel benötigt eine App einen zusätzlichen Bereich für den standardmäßigen <xref:System.Net.Http.HttpClient>.</span><span class="sxs-lookup"><span data-stu-id="3289d-171">In the following example, an additional scope is required by an app for the default <xref:System.Net.Http.HttpClient>.</span></span> <span data-ttu-id="3289d-172">In dem Beispiel zu Microsoft Authentication Library (MSAL) wird der Bereich mit `MsalProviderOptions` konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="3289d-172">The Microsoft Authentication Library (MSAL) example configures the scope with `MsalProviderOptions`:</span></span>

<span data-ttu-id="3289d-173">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-173">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

<span data-ttu-id="3289d-174">Bei den Platzhaltern `{CUSTOM SCOPE 1}` und `{CUSTOM SCOPE 2}` im obigen Beispiel handelt es sich um benutzerdefinierte Bereiche.</span><span class="sxs-lookup"><span data-stu-id="3289d-174">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="3289d-175">Die Methode `IAccessTokenProvider.RequestToken` stellt eine Überladung bereit, mit der Apps Zugriffstoken mit vorgegebenen Bereichen bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="3289d-175">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="3289d-176">In einer Razor-Komponente:</span><span class="sxs-lookup"><span data-stu-id="3289d-176">In a Razor component:</span></span>

```razor
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider

...

var tokenResult = await TokenProvider.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "{CUSTOM SCOPE 1}", "{CUSTOM SCOPE 2}" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

<span data-ttu-id="3289d-177">Bei den Platzhaltern `{CUSTOM SCOPE 1}` und `{CUSTOM SCOPE 2}` im obigen Beispiel handelt es sich um benutzerdefinierte Bereiche.</span><span class="sxs-lookup"><span data-stu-id="3289d-177">The `{CUSTOM SCOPE 1}` and `{CUSTOM SCOPE 2}` placeholders in the preceding example are custom scopes.</span></span>

<span data-ttu-id="3289d-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> gibt Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="3289d-178"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="3289d-179">`true` mit dem `token` zur Verwendung</span><span class="sxs-lookup"><span data-stu-id="3289d-179">`true` with the `token` for use.</span></span>
* <span data-ttu-id="3289d-180">`false`, wenn das Token nicht abgerufen wird</span><span class="sxs-lookup"><span data-stu-id="3289d-180">`false` if the token isn't retrieved.</span></span>

## <a name="cross-origin-resource-sharing-cors"></a><span data-ttu-id="3289d-181">Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)</span><span class="sxs-lookup"><span data-stu-id="3289d-181">Cross-origin resource sharing (CORS)</span></span>

<span data-ttu-id="3289d-182">Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-182">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="3289d-183">Die folgende Richtlinie enthält die Konfiguration für:</span><span class="sxs-lookup"><span data-stu-id="3289d-183">The following policy includes configuration for:</span></span>

* <span data-ttu-id="3289d-184">Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).</span><span class="sxs-lookup"><span data-stu-id="3289d-184">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="3289d-185">Any-Methode (Verb).</span><span class="sxs-lookup"><span data-stu-id="3289d-185">Any method (verb).</span></span>
* <span data-ttu-id="3289d-186">`Content-Type`- und `Authorization`-Header.</span><span class="sxs-lookup"><span data-stu-id="3289d-186">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="3289d-187">Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.</span><span class="sxs-lookup"><span data-stu-id="3289d-187">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="3289d-188">Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="3289d-188">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="3289d-189">Eine gehostete Blazor-Lösung, die auf der Projektvorlage „Blazor Hosted“ basiert, verwendet die gleiche Basisadresse für die Client- und Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="3289d-189">A hosted Blazor solution based on the Blazor Hosted project template uses the same base address for the client and server apps.</span></span> <span data-ttu-id="3289d-190">Die <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> der Client-App wird standardmäßig auf einen URI `builder.HostEnvironment.BaseAddress` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="3289d-190">The client app's <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> is set to a URI of `builder.HostEnvironment.BaseAddress` by default.</span></span> <span data-ttu-id="3289d-191">Die CORS-Konfiguration ist in der Standardkonfiguration einer gehosteten App, die aus der Projektvorlage „Blazor Hosted“ erstellt wurde, **nicht** erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3289d-191">CORS configuration is **not** required in the default configuration of a hosted app created from the Blazor Hosted project template.</span></span> <span data-ttu-id="3289d-192">Zusätzliche Client-Apps, die nicht vom Serverprojekt gehostet werden und nicht die Basisadresse der Server-App teilen, **erfordern** CORS-Konfiguration im Serverprojekt.</span><span class="sxs-lookup"><span data-stu-id="3289d-192">Additional client apps that aren't hosted by the server project and don't share the server app's base address **do** require CORS configuration in the server project.</span></span>

<span data-ttu-id="3289d-193">Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (`Components/HTTPRequestTester.razor`).</span><span class="sxs-lookup"><span data-stu-id="3289d-193">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (`Components/HTTPRequestTester.razor`).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="3289d-194">Behandeln von Fehlern mit Tokenanforderungen</span><span class="sxs-lookup"><span data-stu-id="3289d-194">Handle token request errors</span></span>

<span data-ttu-id="3289d-195">Wenn eine Single-Page-Anwendung (Single Page Application, SPA) einen Benutzer mithilfe von Open ID Connect (OICD) authentifiziert, wird der Authentifizierungsstatus lokal innerhalb der SPA und im Identity-Anbieter (Identity Provider, IP) in Form eines Sitzungscookies verwaltet, das festgelegt wird, wenn der Benutzer seine Anmeldeinformationen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="3289d-195">When a Single Page Application (SPA) authenticates a user using OpenID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="3289d-196">Die Token, die der IP für den Benutzer ausgibt, sind in der Regel nur für kurze Zeit (etwa eine Stunde) gültig, sodass die Client-App regelmäßig neue Token abrufen muss.</span><span class="sxs-lookup"><span data-stu-id="3289d-196">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="3289d-197">Andernfalls wird der Benutzer nach Ablauf der ausgegebenen Token abgemeldet.</span><span class="sxs-lookup"><span data-stu-id="3289d-197">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="3289d-198">In den meisten Fällen sind die OICD-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer noch mal authentifizieren muss, weil ein Authentifizierungsstatus oder eine Sitzung im IP gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-198">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="3289d-199">Manchmal kann der Client ohne Benutzerinteraktion keine Token abrufen. Dies ist zum Beispiel dann der Fall, wenn sich der Benutzer aus einem beliebigen Grund explizit vom IP abmeldet.</span><span class="sxs-lookup"><span data-stu-id="3289d-199">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="3289d-200">Dies passiert, wenn der Benutzer `https://login.microsoftonline.com` aufruft und sich abmeldet. In diesen Szenarios weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Alle Token, die im Client enthalten sind, sind dann möglicherweise nicht mehr gültig.</span><span class="sxs-lookup"><span data-stu-id="3289d-200">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="3289d-201">Außerdem kann der Client keine neuen Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-201">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="3289d-202">Diese Szenarios sind nicht spezifisch für die tokenbasierte Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="3289d-202">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="3289d-203">Sie gehören bei SPAs dazu.</span><span class="sxs-lookup"><span data-stu-id="3289d-203">They are part of the nature of SPAs.</span></span> <span data-ttu-id="3289d-204">Wenn eine SPA, die cookies verwendet, eine Server-API aufruft, wird ein Fehler ausgelöst, wenn das Authentifizierungscookie entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="3289d-204">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="3289d-205">Wenn eine App API-Aufrufe für geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:</span><span class="sxs-lookup"><span data-stu-id="3289d-205">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="3289d-206">Damit ein neues Zugriffstoken zum Aufrufen der API bereitgestellt werden kann, muss der Benutzer sich möglicherweise noch einmal authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="3289d-206">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="3289d-207">Auch wenn der Client über ein Token verfügt, das anscheinend gültig ist, kann der Serveraufruf fehlschlagen, weil das Token vom Benutzer widerrufen wurde.</span><span class="sxs-lookup"><span data-stu-id="3289d-207">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="3289d-208">Wenn die App ein Token anfordert, gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="3289d-208">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="3289d-209">Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.</span><span class="sxs-lookup"><span data-stu-id="3289d-209">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="3289d-210">Die Anforderung schlägt fehl, und die App muss den Benutzer noch einmal authentifizieren, um ein neues Token zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="3289d-210">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="3289d-211">Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen.</span><span class="sxs-lookup"><span data-stu-id="3289d-211">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="3289d-212">Es gibt mehrere Ansätze, die unterschiedlich komplex sind:</span><span class="sxs-lookup"><span data-stu-id="3289d-212">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="3289d-213">Speichern Sie den aktuellen Seitenzustand im Sitzungsspeicher.</span><span class="sxs-lookup"><span data-stu-id="3289d-213">Store the current page state in session storage.</span></span> <span data-ttu-id="3289d-214">Überprüfen Sie während des [`OnInitializedAsync`-Lebenszyklusereignisses ](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), ob der Zustand wiederhergestellt werden kann, bevor Sie fortfahren.</span><span class="sxs-lookup"><span data-stu-id="3289d-214">During the [`OnInitializedAsync` lifecycle event](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="3289d-215">Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand aktualisieren muss.</span><span class="sxs-lookup"><span data-stu-id="3289d-215">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="3289d-216">Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.</span><span class="sxs-lookup"><span data-stu-id="3289d-216">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="3289d-217">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="3289d-217">The following example shows how to:</span></span>

* <span data-ttu-id="3289d-218">Speichern des Zustands vor der Umleitung auf die Anmeldeseite</span><span class="sxs-lookup"><span data-stu-id="3289d-218">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="3289d-219">Wiederherstellen des vorherigen Zustands nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters</span><span class="sxs-lookup"><span data-stu-id="3289d-219">Recover the previous state afterward authentication using the query string parameter.</span></span>

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider TokenProvider
...

<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var http = new HttpClient();
        http.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await TokenProvider.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            http.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await http.PostAsJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="3289d-220">Speichern des App-Zustands vor einem Authentifizierungsvorgang</span><span class="sxs-lookup"><span data-stu-id="3289d-220">Save app state before an authentication operation</span></span>

<span data-ttu-id="3289d-221">Während eines Authentifizierungsvorgangs sollten Sie ggf. den App-Zustand speichern, bevor der Browser an den IP umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="3289d-221">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="3289d-222">Dies bietet sich zum Beispiel dann an, wenn Sie einen Zustandscontainer verwenden und den Zustand nach erfolgreicher Authentifizierung wiederherstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="3289d-222">This can be the case when you're using a state container and want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="3289d-223">Sie können ein benutzerdefiniertes Zustandsobjekt für die Authentifizierung verwenden, um den App-spezifischen Zustand oder einen Verweis auf diesen zu speichern und diesen Zustand nach erfolgreichem Abschluss des Authentifizierungsvorgangs wiederherzustellen.</span><span class="sxs-lookup"><span data-stu-id="3289d-223">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state after the authentication operation successfully completes.</span></span> <span data-ttu-id="3289d-224">Im folgenden Beispiel wird dieser Ansatz veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="3289d-224">The following example demonstrates the approach.</span></span>

<span data-ttu-id="3289d-225">In der App wird eine Zustandscontainerklasse mit Eigenschaften erstellt, die die Zustandswerte der App enthalten.</span><span class="sxs-lookup"><span data-stu-id="3289d-225">A state container class is created in the app with properties to hold the app's state values.</span></span> <span data-ttu-id="3289d-226">Im folgenden Beispiel wird der Container verwendet, um den Leistungsindikatorwert der Komponente `Counter` der Standardprojektvorlage (`Pages/Counter.razor`) beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="3289d-226">In the following example, the container is used to maintain the counter value of the default project template's `Counter` component (`Pages/Counter.razor`).</span></span> <span data-ttu-id="3289d-227">Methoden zum (De-)Serialisieren des Containers basierend auf <xref:System.Text.Json></span><span class="sxs-lookup"><span data-stu-id="3289d-227">Methods for serializing and deserializing the container are based on <xref:System.Text.Json>.</span></span>

```csharp
using System.Text.Json;

public class StateContainer
{
    public int CounterValue { get; set; }

    public string GetStateForLocalStorage()
    {
        return JsonSerializer.Serialize(this);
    }

    public void SetStateFromLocalStorage(string locallyStoredState)
    {
        var deserializedState = 
            JsonSerializer.Deserialize<StateContainer>(locallyStoredState);

        CounterValue = deserializedState.CounterValue;
    }
}
```

<span data-ttu-id="3289d-228">Die Komponente `Counter` verwendet den Zustandscontainer, um den Wert `currentCount` außerhalb der Komponente beizubehalten:</span><span class="sxs-lookup"><span data-stu-id="3289d-228">The `Counter` component uses the state container to maintain the `currentCount` value outside of the component:</span></span>

```razor
@page "/counter"
@inject StateContainer State

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    protected override void OnInitialized()
    {
        if (State.CounterValue > 0)
        {
            currentCount = State.CounterValue;
        }
    }

    private void IncrementCount()
    {
        currentCount++;
        State.CounterValue = currentCount;
    }
}
```

<span data-ttu-id="3289d-229">Erstellen Sie einen `ApplicationAuthenticationState` aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span><span class="sxs-lookup"><span data-stu-id="3289d-229">Create an `ApplicationAuthenticationState` from <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>.</span></span> <span data-ttu-id="3289d-230">Stellen Sie eine `Id`-Eigenschaft bereit, die als Bezeichner für den lokal gespeicherten Zustand fungiert.</span><span class="sxs-lookup"><span data-stu-id="3289d-230">Provide an `Id` property, which serves as an identifier for the locally-stored state.</span></span>

<span data-ttu-id="3289d-231">`ApplicationAuthenticationState.cs`:</span><span class="sxs-lookup"><span data-stu-id="3289d-231">`ApplicationAuthenticationState.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

<span data-ttu-id="3289d-232">Die Komponente `Authentication` (`Pages/Authentication.razor`) speichert den Zustand der App mithilfe des lokalen Sitzungsspeichers über die `StateContainer`-(De-)Serialisierungsmethoden `GetStateForLocalStorage` und `SetStateFromLocalStorage` und stellt diesen auch darüber wieder her:</span><span class="sxs-lookup"><span data-stu-id="3289d-232">The `Authentication` component (`Pages/Authentication.razor`) saves and restores the app's state using local session storage with the `StateContainer` serialization and deserialization methods, `GetStateForLocalStorage` and `SetStateFromLocalStorage`:</span></span>

```razor
@page "/authentication/{action}"
@inject IJSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action"
                             TAuthenticationState="ApplicationAuthenticationState"
                             AuthenticationState="AuthenticationState"
                             OnLogInSucceeded="RestoreState"
                             OnLogOutSucceeded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public ApplicationAuthenticationState AuthenticationState { get; set; } =
        new ApplicationAuthenticationState();

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn,
            Action) ||
            RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogOut,
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();

            await JS.InvokeVoidAsync("sessionStorage.setItem",
                AuthenticationState.Id, State.GetStateForLocalStorage());
        }
    }

    private async Task RestoreState(ApplicationAuthenticationState state)
    {
        if (state.Id != null)
        {
            var locallyStoredState = await JS.InvokeAsync<string>(
                "sessionStorage.getItem", state.Id);

            if (locallyStoredState != null)
            {
                State.SetStateFromLocalStorage(locallyStoredState);
                await JS.InvokeVoidAsync("sessionStorage.removeItem", state.Id);
            }
        }
    }
}
```

<span data-ttu-id="3289d-233">In diesem Beispiel wird AAD für die Authentifizierung verwendet.</span><span class="sxs-lookup"><span data-stu-id="3289d-233">This example uses Azure Active Directory (AAD) for authentication.</span></span> <span data-ttu-id="3289d-234">In `Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-234">In `Program.Main` (`Program.cs`):</span></span>

* <span data-ttu-id="3289d-235">Der `ApplicationAuthenticationState` wird als `RemoteAuthenticationState`-Typ der Microsoft-Authentifizierungsbibliothek konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3289d-235">The `ApplicationAuthenticationState` is configured as the Microsoft Autentication Library (MSAL) `RemoteAuthenticationState` type.</span></span>
* <span data-ttu-id="3289d-236">Der Zustandscontainer wird im Dienstcontainer registriert.</span><span class="sxs-lookup"><span data-stu-id="3289d-236">The state container is registered in the service container.</span></span>

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a><span data-ttu-id="3289d-237">Anpassen von App-Routen</span><span class="sxs-lookup"><span data-stu-id="3289d-237">Customize app routes</span></span>

<span data-ttu-id="3289d-238">Standardmäßig verwendet die Bibliothek [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) die Routen, die in der folgenden Tabelle angezeigt werden, um unterschiedliche Authentifizierungszustände darzustellen.</span><span class="sxs-lookup"><span data-stu-id="3289d-238">By default, the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="3289d-239">Route</span><span class="sxs-lookup"><span data-stu-id="3289d-239">Route</span></span>                            | <span data-ttu-id="3289d-240">Zweck</span><span class="sxs-lookup"><span data-stu-id="3289d-240">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="3289d-241">Löst einen Anmeldevorgang aus</span><span class="sxs-lookup"><span data-stu-id="3289d-241">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="3289d-242">Verarbeitet das Ergebnis eines beliebigen Anmeldevorgangs.</span><span class="sxs-lookup"><span data-stu-id="3289d-242">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="3289d-243">Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus einem beliebigen Grund fehlschlägt</span><span class="sxs-lookup"><span data-stu-id="3289d-243">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="3289d-244">Löst einen Abmeldevorgang aus</span><span class="sxs-lookup"><span data-stu-id="3289d-244">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="3289d-245">Verarbeitet das Ergebnis eines beliebigen Abmeldevorgangs</span><span class="sxs-lookup"><span data-stu-id="3289d-245">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="3289d-246">Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus einem beliebigen Grund fehlschlägt</span><span class="sxs-lookup"><span data-stu-id="3289d-246">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="3289d-247">Gibt an, dass ein Benutzer sich erfolgreich abgemeldet hat</span><span class="sxs-lookup"><span data-stu-id="3289d-247">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="3289d-248">Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten</span><span class="sxs-lookup"><span data-stu-id="3289d-248">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="3289d-249">Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren</span><span class="sxs-lookup"><span data-stu-id="3289d-249">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="3289d-250">Die in der obigen Tabelle aufgeführten Routen können über <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-250">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3289d-251">Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die App über eine Route verfügt, die die einzelnen Pfade verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="3289d-251">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="3289d-252">Im folgenden Beispiel haben alle Pfade das Präfix `/security`.</span><span class="sxs-lookup"><span data-stu-id="3289d-252">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="3289d-253">`Authentication`-Komponente (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="3289d-253">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3289d-254">`Program.Main` (`Program.cs`):</span><span class="sxs-lookup"><span data-stu-id="3289d-254">`Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

<span data-ttu-id="3289d-255">Wenn vollkommen unterschiedliche Pfade verlangt werden, legen Sie die Routen wie zuvor beschrieben fest, und rendern Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:</span><span class="sxs-lookup"><span data-stu-id="3289d-255">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="3289d-256">Wenn Sie sich hierfür entscheiden, können Sie die Benutzeroberfläche auf mehrere Seiten aufteilen.</span><span class="sxs-lookup"><span data-stu-id="3289d-256">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="3289d-257">Anpassen der Benutzeroberfläche für die Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="3289d-257">Customize the authentication user interface</span></span>

<span data-ttu-id="3289d-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> enthält Standardbenutzeroberflächenelemente für alle Authentifizierungszustände.</span><span class="sxs-lookup"><span data-stu-id="3289d-258"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="3289d-259">Jeder Zustand kann angepasst werden, indem ein benutzerdefiniertes <xref:Microsoft.AspNetCore.Components.RenderFragment>übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="3289d-259">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="3289d-260">Wenn Sie den angezeigten Text während des ersten Anmeldeprozesses anpassen möchten, können Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.</span><span class="sxs-lookup"><span data-stu-id="3289d-260">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="3289d-261">`Authentication`-Komponente (`Pages/Authentication.razor`):</span><span class="sxs-lookup"><span data-stu-id="3289d-261">`Authentication` component (`Pages/Authentication.razor`):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="3289d-262">Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das für die Authentifizierungsrouten in der folgenden Tabelle verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3289d-262">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="3289d-263">Route</span><span class="sxs-lookup"><span data-stu-id="3289d-263">Route</span></span>                            | <span data-ttu-id="3289d-264">Fragment</span><span class="sxs-lookup"><span data-stu-id="3289d-264">Fragment</span></span>                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |

## <a name="customize-the-user"></a><span data-ttu-id="3289d-265">Anpassen des Benutzers</span><span class="sxs-lookup"><span data-stu-id="3289d-265">Customize the user</span></span>

<span data-ttu-id="3289d-266">Benutzer, die an die App gebunden sind, können angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-266">Users bound to the app can be customized.</span></span>

### <a name="customize-the-user-with-a-payload-claim"></a><span data-ttu-id="3289d-267">Anpassen des Benutzers mit einem Nutzdatenanspruch</span><span class="sxs-lookup"><span data-stu-id="3289d-267">Customize the user with a payload claim</span></span>

<span data-ttu-id="3289d-268">Im folgenden Beispiel erhalten die authentifizierten Benutzer der App einen `amr`-Anspruch für jede Authentifizierungsmethode des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="3289d-268">In the following example, the app's authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span> <span data-ttu-id="3289d-269">Der `amr`-Anspruch gibt an, wie der Antragsteller des Tokens in Microsoft Identity Platform v1.0-[Nutzdatenansprüchen](/azure/active-directory/develop/access-tokens#the-amr-claim) authentifiziert wurde.</span><span class="sxs-lookup"><span data-stu-id="3289d-269">The `amr` claim identifies how the subject of the token was authenticated in Microsoft Identity Platform v1.0 [payload claims](/azure/active-directory/develop/access-tokens#the-amr-claim).</span></span> <span data-ttu-id="3289d-270">Im Beispiel wird eine benutzerdefinierte Benutzerkontoklasse basierend auf <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>verwendet.</span><span class="sxs-lookup"><span data-stu-id="3289d-270">The example uses a custom user account class based on <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>.</span></span>

<span data-ttu-id="3289d-271">Erstellen Sie eine Klasse, mit der die Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird:</span><span class="sxs-lookup"><span data-stu-id="3289d-271">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class.</span></span> <span data-ttu-id="3289d-272">Im folgenden Beispiel wird die `AuthenticationMethod`-Eigenschaft auf das Array von `amr`-JSON-Eigenschaftswerten des Benutzers festgelegt.</span><span class="sxs-lookup"><span data-stu-id="3289d-272">The following example sets the `AuthenticationMethod` property to the user's array of `amr` JSON property values.</span></span> <span data-ttu-id="3289d-273">`AuthenticationMethod` wird bei der Authentifizierung des Benutzers vom Framework automatisch aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="3289d-273">`AuthenticationMethod` is populated automatically by the framework when the user is authenticated.</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="3289d-274">Erstellen Sie eine Factory, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> erweitert wird, um aus den in `CustomUserAccount.AuthenticationMethod`gespeicherten Authentifizierungsmethoden des Benutzers Ansprüche zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="3289d-274">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> to create claims from the user's authentication methods stored in `CustomUserAccount.AuthenticationMethod`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomAccountFactory 
    : AccountClaimsPrincipalFactory<CustomUserAccount>
{
    public CustomAccountFactory(NavigationManager navigationManager, 
        IAccessTokenProviderAccessor accessor) : base(accessor)
    {
    }
  
    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        CustomUserAccount account, RemoteAuthenticationUserOptions options)
    {
        var initialUser = await base.CreateUserAsync(account, options);

        if (initialUser.Identity.IsAuthenticated)
        {
            foreach (var value in account.AuthenticationMethod)
            {
                ((ClaimsIdentity)initialUser.Identity)
                    .AddClaim(new Claim("amr", value));
            }
        }

        return initialUser;
    }
}
```

<span data-ttu-id="3289d-275">Registrieren Sie die `CustomAccountFactory` für den verwendeten Authentifizierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="3289d-275">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="3289d-276">Sie können die folgenden Registrierungen verwenden:</span><span class="sxs-lookup"><span data-stu-id="3289d-276">Any of the following registrations are valid:</span></span>

* <span data-ttu-id="3289d-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3289d-277"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddOidcAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

* <span data-ttu-id="3289d-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="3289d-278"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddMsalAuthentication<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```
  
* <span data-ttu-id="3289d-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="3289d-279"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

  ...

  builder.Services.AddApiAuthorization<RemoteAuthenticationState, 
      CustomUserAccount>(options =>
      {
          ...
      })
      .AddAccountClaimsPrincipalFactory<RemoteAuthenticationState, 
          CustomUserAccount, CustomAccountFactory>();
  ```

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a><span data-ttu-id="3289d-280">AAD-Sicherheitsgruppen und -Rollen mit einer benutzerdefinierten Benutzerkontoklasse</span><span class="sxs-lookup"><span data-stu-id="3289d-280">AAD security groups and roles with a custom user account class</span></span>

<span data-ttu-id="3289d-281">Ein weiteres Beispiel für AAD-Sicherheitsgruppen und AAD-Administratorrollen sowie eine benutzerdefinierte Benutzerkontoklasse finden Sie unter <xref:blazor/security/webassembly/aad-groups-roles>.</span><span class="sxs-lookup"><span data-stu-id="3289d-281">For an additional example that works with AAD security groups and AAD Administrator Roles and a custom user account class, see <xref:blazor/security/webassembly/aad-groups-roles>.</span></span>

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="3289d-282">Unterstützen des Vorabrenderings mit Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="3289d-282">Support prerendering with authentication</span></span>

<span data-ttu-id="3289d-283">Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:</span><span class="sxs-lookup"><span data-stu-id="3289d-283">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="3289d-284">Pfade vorab rendert, für die keine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-284">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="3289d-285">Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-285">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="3289d-286">Schreiben Sie in der `Program`-Klasse der *`Client`* -App (`Program.cs`) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="3289d-286">In the *`Client`* app's `Program` class (`Program.cs`), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add...;

        builder.Services.AddScoped( ... );

        services.Add...;

        ConfigureCommonServices(builder.Services);

        await builder.Build().RunAsync();
    }

    public static void ConfigureCommonServices(IServiceCollection services)
    {
        // Common service registrations
    }
}
```

<span data-ttu-id="3289d-287">Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:</span><span class="sxs-lookup"><span data-stu-id="3289d-287">In the server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Server;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddRazorPages();
    services.AddScoped<AuthenticationStateProvider, 
        ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

<span data-ttu-id="3289d-288">Ersetzen Sie in der Methode `Startup.Configure` der Server-App [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) durch [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="3289d-288">In the server app's `Startup.Configure` method, replace [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="3289d-289">Erstellen Sie in der Server-App einen Ordner `Pages`, falls dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-289">In the server app, create a `Pages` folder if it doesn't exist.</span></span> <span data-ttu-id="3289d-290">Erstellen Sie in der Server-App innerhalb des Ordners `Pages` die Seite `_Host.cshtml`.</span><span class="sxs-lookup"><span data-stu-id="3289d-290">Create a `_Host.cshtml` page inside the server app's `Pages` folder.</span></span> <span data-ttu-id="3289d-291">Fügen Sie den Inhalt der Datei `wwwroot/index.html` der *`Client`* -App in die Datei `Pages/_Host.cshtml` ein.</span><span class="sxs-lookup"><span data-stu-id="3289d-291">Paste the contents from the *`Client`* app's `wwwroot/index.html` file into the `Pages/_Host.cshtml` file.</span></span> <span data-ttu-id="3289d-292">Aktualisieren Sie den Inhalt der Datei:</span><span class="sxs-lookup"><span data-stu-id="3289d-292">Update the file's contents:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="3289d-293">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="3289d-293">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="3289d-294">Ersetzen Sie das Tag `<div id="app">Loading...</div>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3289d-294">Replace the `<div id="app">Loading...</div>` tag with the following:</span></span>

  ```cshtml
  <div id="app">
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </div>
  ```
  
  <span data-ttu-id="3289d-295">Im Beispiel oben ist der Platzhalter `{CLIENT APP ASSEMBLY NAME}` der Assemblyname der Client-App (z. B. `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="3289d-295">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="3289d-296">Fügen Sie `@page "_Host"` am Anfang der Datei ein.</span><span class="sxs-lookup"><span data-stu-id="3289d-296">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="3289d-297">Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3289d-297">Replace the `<app>Loading...</app>` tag with the following:</span></span>

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof({CLIENT APP ASSEMBLY NAME}.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
  <span data-ttu-id="3289d-298">Im Beispiel oben ist der Platzhalter `{CLIENT APP ASSEMBLY NAME}` der Assemblyname der Client-App (z. B. `BlazorSample.Client`).</span><span class="sxs-lookup"><span data-stu-id="3289d-298">In the preceding example, the placeholder `{CLIENT APP ASSEMBLY NAME}` is the client app's assembly name (for example `BlazorSample.Client`).</span></span>

::: moniker-end
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="3289d-299">Optionen für gehostete Apps und dritte Anmeldeanbieter</span><span class="sxs-lookup"><span data-stu-id="3289d-299">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="3289d-300">Wenn eine gehostete Blazor WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="3289d-300">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="3289d-301">Welche Sie auswählen, hängt von Ihrem Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="3289d-301">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="3289d-302">Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="3289d-302">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="3289d-303">Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="3289d-303">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="3289d-304">So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:</span><span class="sxs-lookup"><span data-stu-id="3289d-304">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="3289d-305">Szenario:</span><span class="sxs-lookup"><span data-stu-id="3289d-305">In this scenario:</span></span>

* <span data-ttu-id="3289d-306">Der Server, der die App hostet, spielt keine Rolle.</span><span class="sxs-lookup"><span data-stu-id="3289d-306">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="3289d-307">APIs auf dem Server können nicht geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-307">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="3289d-308">Die App kann nur geschützte APIs von Drittanbietern aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3289d-308">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="3289d-309">Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter</span><span class="sxs-lookup"><span data-stu-id="3289d-309">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="3289d-310">Konfigurieren Sie Identity mit einem dritten Anmeldeanbieter.</span><span class="sxs-lookup"><span data-stu-id="3289d-310">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="3289d-311">Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.</span><span class="sxs-lookup"><span data-stu-id="3289d-311">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="3289d-312">Wenn sich ein Benutzer anmeldet, erfasst Identity als Teil des Authentifizierungsprozesses die Token für Zugriff und Aktualisierung.</span><span class="sxs-lookup"><span data-stu-id="3289d-312">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="3289d-313">An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="3289d-313">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="3289d-314">Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters</span><span class="sxs-lookup"><span data-stu-id="3289d-314">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="3289d-315">Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3289d-315">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="3289d-316">Verwenden Sie dann das Zugriffstoken des Drittanbieters, um API-Ressourcen von Drittanbietern direkt über Identity auf dem Client abzurufen.</span><span class="sxs-lookup"><span data-stu-id="3289d-316">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="3289d-317">Dieser Ansatz wird nicht empfohlen.</span><span class="sxs-lookup"><span data-stu-id="3289d-317">We don't recommend this approach.</span></span> <span data-ttu-id="3289d-318">Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden.</span><span class="sxs-lookup"><span data-stu-id="3289d-318">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="3289d-319">In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="3289d-319">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="3289d-320">Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.</span><span class="sxs-lookup"><span data-stu-id="3289d-320">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="3289d-321">Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.</span><span class="sxs-lookup"><span data-stu-id="3289d-321">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="3289d-322">Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.</span><span class="sxs-lookup"><span data-stu-id="3289d-322">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="3289d-323">Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs</span><span class="sxs-lookup"><span data-stu-id="3289d-323">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="3289d-324">Führen Sie einen API-Aufruf auf dem Client für die Server-API durch.</span><span class="sxs-lookup"><span data-stu-id="3289d-324">Make an API call from the client to the server API.</span></span> <span data-ttu-id="3289d-325">Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="3289d-325">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="3289d-326">Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:</span><span class="sxs-lookup"><span data-stu-id="3289d-326">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="3289d-327">Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.</span><span class="sxs-lookup"><span data-stu-id="3289d-327">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="3289d-328">Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="3289d-328">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-openid-connect-oidc-v20-endpoints"></a><span data-ttu-id="3289d-329">Verwenden von OpenID Connect 2.0-Endpunkten (OIDC)</span><span class="sxs-lookup"><span data-stu-id="3289d-329">Use OpenID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="3289d-330">Die Authentifizierungsbibliothek sowie Blazor-Projektvorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC).</span><span class="sxs-lookup"><span data-stu-id="3289d-330">The authentication library and Blazor project templates use OpenID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="3289d-331">Wenn Sie einen Endpunkt der Version 2.0 verwenden möchten, konfigurieren Sie die JWT-Bearer-Option <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="3289d-331">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="3289d-332">Im folgenden Beispiel wird AAD für Version 2.0 konfiguriert, indem ein `v2.0`-Segment an die Eigenschaft <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> angehängt wird:</span><span class="sxs-lookup"><span data-stu-id="3289d-332">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="3289d-333">Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="3289d-333">Alternatively, the setting can be made in the app settings (`appsettings.json`) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="3289d-334">Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest.</span><span class="sxs-lookup"><span data-stu-id="3289d-334">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="3289d-335">Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der Datei mit den App-Einstellungen (`appsettings.json`) mit dem `Authority`-Schlüssel fest.</span><span class="sxs-lookup"><span data-stu-id="3289d-335">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (`appsettings.json`) with the `Authority` key.</span></span>

<span data-ttu-id="3289d-336">Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0.</span><span class="sxs-lookup"><span data-stu-id="3289d-336">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="3289d-337">Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="3289d-337">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>

## <a name="configure-and-use-grpc-in-components"></a><span data-ttu-id="3289d-338">Konfigurieren und Verwenden von gRPC in Komponenten</span><span class="sxs-lookup"><span data-stu-id="3289d-338">Configure and use gRPC in components</span></span>

<span data-ttu-id="3289d-339">So konfigurieren Sie eine Blazor WebAssembly-App zur Verwendung des [ASP.NET Core gRPC-Frameworks](xref:grpc/index):</span><span class="sxs-lookup"><span data-stu-id="3289d-339">To configure a Blazor WebAssembly app to use the [ASP.NET Core gRPC framework](xref:grpc/index):</span></span>

* <span data-ttu-id="3289d-340">Aktivieren Sie gRPC-Web auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="3289d-340">Enable gRPC-Web on the server.</span></span> <span data-ttu-id="3289d-341">Weitere Informationen finden Sie unter <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="3289d-341">For more information, see <xref:grpc/browser>.</span></span>
* <span data-ttu-id="3289d-342">Registrieren Sie gRPC-Dienste für den Meldungshandler der App.</span><span class="sxs-lookup"><span data-stu-id="3289d-342">Register gRPC services for the app's message handler.</span></span> <span data-ttu-id="3289d-343">Im folgenden Beispiel wird der Autorisierungsmeldungshandler der App zur Verwendung des [`GreeterClient`-Diensts aus dem gRPC-Tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`) konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="3289d-343">The following example configures the app's authorization message handler to use the [`GreeterClient` service from the gRPC tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Grpc.Net.Client;
using Grpc.Net.Client.Web;
using {APP ASSEMBLY}.Shared;

...

builder.Services.AddScoped(sp =>
{
    var baseAddressMessageHandler = 
        sp.GetRequiredService<BaseAddressAuthorizationMessageHandler>();
    baseAddressMessageHandler.InnerHandler = new HttpClientHandler();
    var grpcWebHandler = 
        new GrpcWebHandler(GrpcWebMode.GrpcWeb, baseAddressMessageHandler);
    var channel = GrpcChannel.ForAddress(builder.HostEnvironment.BaseAddress, 
        new GrpcChannelOptions { HttpHandler = grpcWebHandler });

    return new Greeter.GreeterClient(channel);
});
```

<span data-ttu-id="3289d-344">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="3289d-344">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="3289d-345">Platzieren Sie die `.proto`-Datei im `Shared`-Projekt der gehosteten Blazor-Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="3289d-345">Place the `.proto` file in the `Shared` project of the hosted Blazor solution.</span></span>

<span data-ttu-id="3289d-346">Eine Komponente in der Client-App kann gRPC-Aufrufe mithilfe des gRPC-Clients (`Pages/Grpc.razor`) ausführen:</span><span class="sxs-lookup"><span data-stu-id="3289d-346">A component in the client app can make gRPC calls using the gRPC client (`Pages/Grpc.razor`):</span></span>

```razor
@page "/grpc"
@attribute [Authorize]
@using Microsoft.AspNetCore.Authorization
@using {APP ASSEMBLY}.Shared
@inject Greeter.GreeterClient GreeterClient

<h1>Invoke gRPC service</h1>

<p>
    <input @bind="name" placeholder="Type your name" />
    <button @onclick="GetGreeting" class="btn btn-primary">Call gRPC service</button>
</p>

Server response: <strong>@serverResponse</strong>

@code {
    private string name = "Bert";
    private string serverResponse;

    private async Task GetGreeting()
    {
        try
        {
            var request = new HelloRequest { Name = name };
            var reply = await GreeterClient.SayHelloAsync(request);
            serverResponse = reply.Message;
        }
        catch (Grpc.Core.RpcException ex)
            when (ex.Status.DebugException is 
                AccessTokenNotAvailableException tokenEx)
        {
            tokenEx.Redirect();
        }
    }
}
```

<span data-ttu-id="3289d-347">Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="3289d-347">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample`).</span></span> <span data-ttu-id="3289d-348">Verwenden Sie Version 2.30.0 oder höher von [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), um die `Status.DebugException`-Eigenschaft zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3289d-348">To use the `Status.DebugException` property, use [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) version 2.30.0 or later.</span></span>

<span data-ttu-id="3289d-349">Weitere Informationen finden Sie unter <xref:grpc/browser>.</span><span class="sxs-lookup"><span data-stu-id="3289d-349">For more information, see <xref:grpc/browser>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3289d-350">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3289d-350">Additional resources</span></span>

* <xref:blazor/security/webassembly/graph-api>
* [<span data-ttu-id="3289d-351">`HttpClient` und `HttpRequestMessage` mit Abruf-API-Anforderungsoptionen</span><span class="sxs-lookup"><span data-stu-id="3289d-351">`HttpClient` and `HttpRequestMessage` with Fetch API request options</span></span>](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
