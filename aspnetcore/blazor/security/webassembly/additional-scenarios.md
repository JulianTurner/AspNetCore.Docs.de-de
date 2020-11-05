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
ms.openlocfilehash: 88970b0e53b456467bdc2218a3a6b943bbbf0df5
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234386"
---
# <a name="aspnet-core-no-locblazor-webassembly-additional-security-scenarios"></a>Zusätzliche Sicherheitsszenarios für ASP.NET Core Blazor WebAssembly

Von [Javier Calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Anfügen von Token an ausgehende Anforderungen

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> ist ein <xref:System.Net.Http.DelegatingHandler>, der verwendet wird, um Zugriffstoken an ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen anzufügen. Token werden mithilfe des <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> Diensts abgerufen, der vom Framework registriert wird. Wenn ein Token nicht abgerufen werden kann, wird eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> ausgelöst. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> verfügt über eine <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A>-Methode, die verwendet werden kann, um den Benutzer zum Abrufen eines neuen Token zum Identitätsanbieter zu navigieren.

Zur Vereinfachung stellt das Framework <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> bereit, der mit der Basisadresse der App als autorisierte URL vorkonfiguriert ist. **Zugriffstoken werden nur hinzugefügt, wenn der Anforderungs-URI innerhalb des Basis-URI der APP liegt.** Wenn sich ausgehende Anforderungs-URIs nicht innerhalb des Basis-URI der App befinden, verwenden Sie eine [benutzerdefinierte `AuthorizationMessageHandler`-Klasse ( *empfohlen* )](#custom-authorizationmessagehandler-class), oder [konfigurieren Sie `AuthorizationMessageHandler`](#configure-authorizationmessagehandler).

> [!NOTE]
> Zusätzlich zur Client-App-Konfiguration für den Server-API-Zugriff muss die Server-API auch Cross-Origin-Anforderungen (CORS) zulassen, wenn sich der Client und der Server nicht an derselben Basisadresse befinden. Weitere Informationen zur serverseitigen CORS-Konfiguration finden Sie im Abschnitt [Cross-Origin Resource Sharing (CORS)](#cross-origin-resource-sharing-cors) weiter unten in diesem Artikel.

Siehe folgendes Beispiel:

* <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient%2A> fügt der Dienstesammlung <xref:System.Net.Http.IHttpClientFactory> und verwandte Dienste hinzu und konfiguriert einen benannten <xref:System.Net.Http.HttpClient> (`ServerAPI`). <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> ist die Basisadresse des Ressourcen-URI beim Senden von Anforderungen. <xref:System.Net.Http.IHttpClientFactory> wird vom NuGet-Paket [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http) bereitgestellt.
* <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> ist der <xref:System.Net.Http.DelegatingHandler>, der verwendet wird, um Zugriffstoken an ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen anzufügen. Zugriffstoken werden nur hinzugefügt, wenn der Anforderungs-URI innerhalb des Basis-URI der APP liegt.
* <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A?displayProperty=nameWithType> erstellt und konfiguriert eine <xref:System.Net.Http.HttpClient>-Instanz für ausgehende Anforderungen unter Verwendung der Konfiguration, die dem benannten <xref:System.Net.Http.HttpClient> entspricht (`ServerAPI`).

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

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, befinden sich die Anforderungs-URIs standardmäßig im Basis-URI der App. Daher wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> in einer App zugewiesen, die aus der Projektvorlage generiert wurde.

Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen:

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

### <a name="custom-authorizationmessagehandler-class"></a>Benutzerdefinierte `AuthorizationMessageHandler`-Klasse

*Diese Anleitung in diesem Abschnitt wird für Client-Apps empfohlen, die ausgehende Anforderungen an URIs vornehmen, die sich nicht im Basis-URI der App befinden.*

Im folgenden Beispiel erweitert eine benutzerdefinierte-Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> für die Verwendung als <xref:System.Net.Http.DelegatingHandler> für einen <xref:System.Net.Http.HttpClient>. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguriert diesen Handler, um ausgehende HTTP-Anforderungen mithilfe eines Zugriffstokens zu autorisieren. Das Zugriffstoken wird nur angefügt, wenn mindestens eine der autorisierten URLs eine Basis des Anforderungs-URIs (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) ist.

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

In `Program.Main` (`Program.cs`) ist `CustomAuthorizationMessageHandler` als Dienst mit Gültigkeitsbereich registriert und wird als <xref:System.Net.Http.DelegatingHandler> für ausgehende <xref:System.Net.Http.HttpResponseMessage>-Instanzen konfiguriert, die von einem benannten <xref:System.Net.Http.HttpClient> erstellt werden:

```csharp
builder.Services.AddScoped<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.

Der konfigurierte <xref:System.Net.Http.HttpClient> wird verwendet, um autorisierte Anforderungen mithilfe des [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Musters zu stellen. Wenn der Client mit <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> erstellt wird ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)-Paket), wird <xref:System.Net.Http.HttpClient> für Instanzen bereitgestellt, die Zugriffstoken enthalten, wenn Anforderungen an die Server-API vorgenommen werden. Wenn der Anforderungs-URI wie im folgenden Beispiel (`ExampleAPIMethod`) ein relativer URI ist, wird er mit der <xref:System.Net.Http.HttpClient.BaseAddress> kombiniert, wenn die Client-App die Anforderung vornimmt:

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

### <a name="configure-authorizationmessagehandler"></a>Konfigurieren von `AuthorizationMessageHandler`

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> kann mithilfe der Methode <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> mit autorisierten URLs, Bereichen und einer Rückgabe-URL konfiguriert werden. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> konfiguriert den Handler, um ausgehende HTTP-Anforderungen mithilfe eines Zugriffstokens zu autorisieren. Das Zugriffstoken wird nur angefügt, wenn mindestens eine der autorisierten URLs eine Basis des Anforderungs-URIs (<xref:System.Net.Http.HttpRequestMessage.RequestUri?displayProperty=nameWithType>) ist. Wenn der Anforderungs-URI ein relativer URI ist, wird er mit der <xref:System.Net.Http.HttpClient.BaseAddress> kombiniert.

Im folgenden Beispiel konfiguriert <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> einen <xref:System.Net.Http.HttpClient> in `Program.Main` (`Program.cs`):

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

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> den folgenden Elementen standardmäßig zugewiesen:

* Der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).
* Einer URL des `authorizedUrls`-Arrays.

## <a name="typed-httpclient"></a>Typisierter `HttpClient`

Es kann ein typisierter Client definiert werden, der alle Probleme mit dem HTTP- und Tokenabruf innerhalb einer einzelnen Klasse bearbeitet.

`WeatherForecastClient.cs`:

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

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `using static BlazorSample.Data;`).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.

`FetchData`-Komponente (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Konfigurieren des `HttpClient`-Handlers

Der Handler kann mit dem <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> für ausgehende HTTP-Anforderungen weiter konfiguriert werden.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(
        client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(
        authorizedUrls: new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> den folgenden Elementen standardmäßig zugewiesen:

* Der <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`).
* Einer URL des `authorizedUrls`-Arrays.

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen in einer App mit einem sicheren Standardclient

Wenn die Blazor WebAssembly-App normalerweise einen sicheren Standard-<xref:System.Net.Http.HttpClient> verwendet, kann die App auch nicht authentifizierte oder nicht autorisierte Web-API-Anforderungen stellen, indem sie einen benannten <xref:System.Net.Http.HttpClient> konfiguriert:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri("https://www.example.com/base"));
```

Für eine Blazor-App, die auf der Projektvorlage „Blazor WebAssembly Hosted“ basiert, wird <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`new Uri(builder.HostEnvironment.BaseAddress)`) standardmäßig <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> zugewiesen.

Die obenstehende Registrierung erfolgt zusätzlich zur bereits vorhandenen sicheren Standardregistrierung des <xref:System.Net.Http.HttpClient>.

Eine Komponente erstellt den <xref:System.Net.Http.HttpClient> aus der <xref:System.Net.Http.IHttpClientFactory> ([`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http)-Paket), um nicht authentifizierte oder nicht autorisierte Anforderungen zu stellen:

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
> Der Controller in der Server-API (`WeatherForecastNoAuthenticationController`) für das obenstehende Beispiel ist nicht mit dem Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) gekennzeichnet.

Die Entscheidung, ob ein sicherer Client oder ein unsicherer Client als Standard-<xref:System.Net.Http.HttpClient>-Instanz verwendet werden soll, trifft der Entwickler. Eine Möglichkeit, diese Entscheidung zu treffen, ist die Berücksichtigung der Anzahl der authentifizierten und nicht authentifizierten Endpunkte, die von der App kontaktiert werden. Wenn die Mehrzahl der Anforderungen der App zum Sichern von API-Endpunkten verwendet wird, verwenden Sie die authentifizierte <xref:System.Net.Http.HttpClient>-Instanz als Standard. Registrieren Sie andernfalls die nicht authentifizierte <xref:System.Net.Http.HttpClient>-Instanz als Standard.

Ein alternativer Ansatz für die Verwendung der <xref:System.Net.Http.IHttpClientFactory> ist das Erstellen eines [typisierten Clients](#typed-httpclient) für nicht authentifizierten Zugriff auf anonyme Endpunkte.

## <a name="request-additional-access-tokens"></a>Anfordern zusätzlicher Zugriffstoken

Zugriffstoken können mithilfe von `IAccessTokenProvider.RequestAccessToken` manuell abgerufen werden. Im folgenden Beispiel benötigt eine App einen zusätzlichen Bereich für den standardmäßigen <xref:System.Net.Http.HttpClient>. In dem Beispiel zu Microsoft Authentication Library (MSAL) wird der Bereich mit `MsalProviderOptions` konfiguriert:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 1}");
    options.ProviderOptions.AdditionalScopesToConsent.Add("{CUSTOM SCOPE 2}");
}
```

Bei den Platzhaltern `{CUSTOM SCOPE 1}` und `{CUSTOM SCOPE 2}` im obigen Beispiel handelt es sich um benutzerdefinierte Bereiche.

Die Methode `IAccessTokenProvider.RequestToken` stellt eine Überladung bereit, mit der Apps Zugriffstoken mit vorgegebenen Bereichen bereitstellen kann.

In einer Razor-Komponente:

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

Bei den Platzhaltern `{CUSTOM SCOPE 1}` und `{CUSTOM SCOPE 2}` im obigen Beispiel handelt es sich um benutzerdefinierte Bereiche.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> gibt Folgendes zurück:

* `true` mit dem `token` zur Verwendung
* `false`, wenn das Token nicht abgerufen wird

## <a name="cross-origin-resource-sharing-cors"></a>Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)

Beim Senden von Anmeldeinformationen (Autorisierungscookies/-header) bei CORS-Anforderungen muss der `Authorization`-Header von der CORS-Richtlinie zugelassen werden.

Die folgende Richtlinie enthält die Konfiguration für:

* Anforderungsursprünge (`http://localhost:5000`, `https://localhost:5001`).
* Any-Methode (Verb).
* `Content-Type`- und `Authorization`-Header. Um einen benutzerdefinierten Header (z. B. `x-custom-header`) zuzulassen, listen Sie den Header beim Aufrufen von <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> auf.
* Anmeldeinformationen werden durch clientseitigen JavaScript-Code festgelegt (`credentials`-Eigenschaft auf `include` festgelegt).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Eine gehostete Blazor-Lösung, die auf der Projektvorlage „Blazor Hosted“ basiert, verwendet die gleiche Basisadresse für die Client- und Server-Apps. Die <xref:System.Net.Http.HttpClient.BaseAddress?displayProperty=nameWithType> der Client-App wird standardmäßig auf einen URI `builder.HostEnvironment.BaseAddress` festgelegt. Die CORS-Konfiguration ist in der Standardkonfiguration einer gehosteten App, die aus der Projektvorlage „Blazor Hosted“ erstellt wurde, **nicht** erforderlich. Zusätzliche Client-Apps, die nicht vom Serverprojekt gehostet werden und nicht die Basisadresse der Server-App teilen, **erfordern** CORS-Konfiguration im Serverprojekt.

Weitere Informationen finden Sie unter <xref:security/cors> und unter der HTTP-Anforderungstester-Komponente der Beispiel-App (`Components/HTTPRequestTester.razor`).

## <a name="handle-token-request-errors"></a>Behandeln von Fehlern mit Tokenanforderungen

Wenn eine Single-Page-Anwendung (Single Page Application, SPA) einen Benutzer mithilfe von Open ID Connect (OICD) authentifiziert, wird der Authentifizierungsstatus lokal innerhalb der SPA und im Identity-Anbieter (Identity Provider, IP) in Form eines Sitzungscookies verwaltet, das festgelegt wird, wenn der Benutzer seine Anmeldeinformationen bereitstellt.

Die Token, die der IP für den Benutzer ausgibt, sind in der Regel nur für kurze Zeit (etwa eine Stunde) gültig, sodass die Client-App regelmäßig neue Token abrufen muss. Andernfalls wird der Benutzer nach Ablauf der ausgegebenen Token abgemeldet. In den meisten Fällen sind die OICD-Clients in der Lage, neue Token bereitzustellen, ohne dass sich der Benutzer noch mal authentifizieren muss, weil ein Authentifizierungsstatus oder eine Sitzung im IP gespeichert ist.

Manchmal kann der Client ohne Benutzerinteraktion keine Token abrufen. Dies ist zum Beispiel dann der Fall, wenn sich der Benutzer aus einem beliebigen Grund explizit vom IP abmeldet. Dies passiert, wenn der Benutzer `https://login.microsoftonline.com` aufruft und sich abmeldet. In diesen Szenarios weiß die App nicht sofort, dass sich der Benutzer abgemeldet hat. Alle Token, die im Client enthalten sind, sind dann möglicherweise nicht mehr gültig. Außerdem kann der Client keine neuen Token ohne Benutzerinteraktion bereitstellen, nachdem das aktuelle Token abgelaufen ist.

Diese Szenarios sind nicht spezifisch für die tokenbasierte Authentifizierung. Sie gehören bei SPAs dazu. Wenn eine SPA, die cookies verwendet, eine Server-API aufruft, wird ein Fehler ausgelöst, wenn das Authentifizierungscookie entfernt wird.

Wenn eine App API-Aufrufe für geschützte Ressourcen ausführt, müssen Sie Folgendes beachten:

* Damit ein neues Zugriffstoken zum Aufrufen der API bereitgestellt werden kann, muss der Benutzer sich möglicherweise noch einmal authentifizieren.
* Auch wenn der Client über ein Token verfügt, das anscheinend gültig ist, kann der Serveraufruf fehlschlagen, weil das Token vom Benutzer widerrufen wurde.

Wenn die App ein Token anfordert, gibt es zwei Möglichkeiten:

* Die Anforderung ist erfolgreich, und die App verfügt über ein gültiges Token.
* Die Anforderung schlägt fehl, und die App muss den Benutzer noch einmal authentifizieren, um ein neues Token zu erhalten.

Wenn eine Tokenanforderung fehlschlägt, müssen Sie entscheiden, ob Sie den aktuellen Zustand speichern möchten, bevor Sie eine Umleitung ausführen. Es gibt mehrere Ansätze, die unterschiedlich komplex sind:

* Speichern Sie den aktuellen Seitenzustand im Sitzungsspeicher. Überprüfen Sie während des [`OnInitializedAsync`-Lebenszyklusereignisses ](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), ob der Zustand wiederhergestellt werden kann, bevor Sie fortfahren.
* Fügen Sie einen Abfragezeichenfolgenparameter hinzu, und verwenden Sie diesen, um der App zu signalisieren, dass sie den zuvor gespeicherten Zustand aktualisieren muss.
* Fügen Sie einen Abfragezeichenfolgenparameter mit einem eindeutigen Bezeichner hinzu, um Daten im Sitzungsspeicher zu speichern, ohne Konflikte mit anderen Elementen zu riskieren.

Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Speichern des Zustands vor der Umleitung auf die Anmeldeseite
* Wiederherstellen des vorherigen Zustands nach der Authentifizierung mithilfe des Abfragezeichenfolgenparameters

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

## <a name="save-app-state-before-an-authentication-operation"></a>Speichern des App-Zustands vor einem Authentifizierungsvorgang

Während eines Authentifizierungsvorgangs sollten Sie ggf. den App-Zustand speichern, bevor der Browser an den IP umgeleitet wird. Dies bietet sich zum Beispiel dann an, wenn Sie einen Zustandscontainer verwenden und den Zustand nach erfolgreicher Authentifizierung wiederherstellen möchten. Sie können ein benutzerdefiniertes Zustandsobjekt für die Authentifizierung verwenden, um den App-spezifischen Zustand oder einen Verweis auf diesen zu speichern und diesen Zustand nach erfolgreichem Abschluss des Authentifizierungsvorgangs wiederherzustellen. Im folgenden Beispiel wird dieser Ansatz veranschaulicht.

In der App wird eine Zustandscontainerklasse mit Eigenschaften erstellt, die die Zustandswerte der App enthalten. Im folgenden Beispiel wird der Container verwendet, um den Leistungsindikatorwert der Komponente `Counter` der Standardprojektvorlage (`Pages/Counter.razor`) beizubehalten. Methoden zum (De-)Serialisieren des Containers basierend auf <xref:System.Text.Json>

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

Die Komponente `Counter` verwendet den Zustandscontainer, um den Wert `currentCount` außerhalb der Komponente beizubehalten:

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

Erstellen Sie einen `ApplicationAuthenticationState` aus <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>. Stellen Sie eine `Id`-Eigenschaft bereit, die als Bezeichner für den lokal gespeicherten Zustand fungiert.

`ApplicationAuthenticationState.cs`:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

Die Komponente `Authentication` (`Pages/Authentication.razor`) speichert den Zustand der App mithilfe des lokalen Sitzungsspeichers über die `StateContainer`-(De-)Serialisierungsmethoden `GetStateForLocalStorage` und `SetStateFromLocalStorage` und stellt diesen auch darüber wieder her:

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

In diesem Beispiel wird AAD für die Authentifizierung verwendet. In `Program.Main` (`Program.cs`):

* Der `ApplicationAuthenticationState` wird als `RemoteAuthenticationState`-Typ der Microsoft-Authentifizierungsbibliothek konfiguriert.
* Der Zustandscontainer wird im Dienstcontainer registriert.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Anpassen von App-Routen

Standardmäßig verwendet die Bibliothek [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) die Routen, die in der folgenden Tabelle angezeigt werden, um unterschiedliche Authentifizierungszustände darzustellen.

| Route                            | Zweck |
| -------------------------------- | ------- |
| `authentication/login`           | Löst einen Anmeldevorgang aus |
| `authentication/login-callback`  | Verarbeitet das Ergebnis eines beliebigen Anmeldevorgangs. |
| `authentication/login-failed`    | Zeigt Fehlermeldungen an, wenn der Anmeldevorgang aus einem beliebigen Grund fehlschlägt |
| `authentication/logout`          | Löst einen Abmeldevorgang aus |
| `authentication/logout-callback` | Verarbeitet das Ergebnis eines beliebigen Abmeldevorgangs |
| `authentication/logout-failed`   | Zeigt Fehlermeldungen an, wenn der Abmeldevorgang aus einem beliebigen Grund fehlschlägt |
| `authentication/logged-out`      | Gibt an, dass ein Benutzer sich erfolgreich abgemeldet hat |
| `authentication/profile`         | Löst einen Vorgang aus, um das Benutzerprofil zu bearbeiten |
| `authentication/register`        | Löst einen Vorgang aus, um einen neuen Benutzer zu registrieren |

Die in der obigen Tabelle aufgeführten Routen können über <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> konfiguriert werden. Wenn Sie Optionen zum Bereitstellen benutzerdefinierter Routen festlegen, vergewissern Sie sich, dass die App über eine Route verfügt, die die einzelnen Pfade verarbeitet.

Im folgenden Beispiel haben alle Pfade das Präfix `/security`.

`Authentication`-Komponente (`Pages/Authentication.razor`):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Wenn vollkommen unterschiedliche Pfade verlangt werden, legen Sie die Routen wie zuvor beschrieben fest, und rendern Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> mit einem expliziten Aktionsparameter:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Wenn Sie sich hierfür entscheiden, können Sie die Benutzeroberfläche auf mehrere Seiten aufteilen.

## <a name="customize-the-authentication-user-interface"></a>Anpassen der Benutzeroberfläche für die Authentifizierung

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> enthält Standardbenutzeroberflächenelemente für alle Authentifizierungszustände. Jeder Zustand kann angepasst werden, indem ein benutzerdefiniertes <xref:Microsoft.AspNetCore.Components.RenderFragment>übergeben wird. Wenn Sie den angezeigten Text während des ersten Anmeldeprozesses anpassen möchten, können Sie die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> wie folgt ändern.

`Authentication`-Komponente (`Pages/Authentication.razor`):

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

Die <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> verfügt über ein Fragment, das für die Authentifizierungsrouten in der folgenden Tabelle verwendet werden kann.

| Route                            | Fragment                |
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

## <a name="customize-the-user"></a>Anpassen des Benutzers

Benutzer, die an die App gebunden sind, können angepasst werden.

### <a name="customize-the-user-with-a-payload-claim"></a>Anpassen des Benutzers mit einem Nutzdatenanspruch

Im folgenden Beispiel erhalten die authentifizierten Benutzer der App einen `amr`-Anspruch für jede Authentifizierungsmethode des Benutzers. Der `amr`-Anspruch gibt an, wie der Antragsteller des Tokens in Microsoft Identity Platform v1.0-[Nutzdatenansprüchen](/azure/active-directory/develop/access-tokens#the-amr-claim) authentifiziert wurde. Im Beispiel wird eine benutzerdefinierte Benutzerkontoklasse basierend auf <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>verwendet.

Erstellen Sie eine Klasse, mit der die Klasse <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> erweitert wird: Im folgenden Beispiel wird die `AuthenticationMethod`-Eigenschaft auf das Array von `amr`-JSON-Eigenschaftswerten des Benutzers festgelegt. `AuthenticationMethod` wird bei der Authentifizierung des Benutzers vom Framework automatisch aufgefüllt.

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Erstellen Sie eine Factory, mit der <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> erweitert wird, um aus den in `CustomUserAccount.AuthenticationMethod`gespeicherten Authentifizierungsmethoden des Benutzers Ansprüche zu erstellen:

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

Registrieren Sie die `CustomAccountFactory` für den verwendeten Authentifizierungsanbieter. Sie können die folgenden Registrierungen verwenden:

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

### <a name="aad-security-groups-and-roles-with-a-custom-user-account-class"></a>AAD-Sicherheitsgruppen und -Rollen mit einer benutzerdefinierten Benutzerkontoklasse

Ein weiteres Beispiel für AAD-Sicherheitsgruppen und AAD-Administratorrollen sowie eine benutzerdefinierte Benutzerkontoklasse finden Sie unter <xref:blazor/security/webassembly/aad-groups-roles>.

## <a name="support-prerendering-with-authentication"></a>Unterstützen des Vorabrenderings mit Authentifizierung

Nachdem Sie die Anleitung in einem der Themen zu gehosteten Blazor WebAssembly-Apps befolgt haben, erstellen Sie anhand der folgenden Schritte eine App, die:

* Pfade vorab rendert, für die keine Autorisierung erforderlich ist.
* Keine Pfade vorab rendert, für die eine Autorisierung erforderlich ist.

Schreiben Sie in der `Program`-Klasse der *`Client`* -App (`Program.cs`) allgemeine Dienstregistrierungen in eine separate Methode (z. B. `ConfigureCommonServices`):

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var builder = WebAssemblyHostBuilder.CreateDefault(args);
        builder.RootComponents.Add<App>("app");

        builder.Services.AddScoped(sp => 
            new HttpClient
            {
                BaseAddress = new Uri(builder.HostEnvironment.BaseAddress)
            });

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

Registrieren Sie in `Startup.ConfigureServices` in der Server-App die folgenden zusätzlichen Dienste:

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

Ersetzen Sie in der Methode `Startup.Configure` der Server-App [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) durch [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

Erstellen Sie in der Server-App einen Ordner `Pages`, falls dieser nicht vorhanden ist. Erstellen Sie in der Server-App innerhalb des Ordners `Pages` die Seite `_Host.cshtml`. Fügen Sie den Inhalt der Datei `wwwroot/index.html` der *`Client`* -App in die Datei `Pages/_Host.cshtml` ein. Aktualisieren Sie den Inhalt der Datei:

* Fügen Sie `@page "_Host"` am Anfang der Datei ein.
* Ersetzen Sie das Tag `<app>Loading...</app>` durch Folgendes:

  ```cshtml
  <app>
      @if (!HttpContext.Request.Path.StartsWithSegments("/authentication"))
      {
          <component type="typeof(Wasm.Authentication.Client.App)" 
              render-mode="Static" />
      }
      else
      {
          <text>Loading...</text>
      }
  </app>
  ```
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Optionen für gehostete Apps und dritte Anmeldeanbieter

Wenn eine gehostete Blazor WebAssembly-App mit einem Drittanbieter authentifiziert und autorisiert wird, stehen für die Authentifizierung des Benutzers mehrere Optionen zur Verfügung. Welche Sie auswählen, hängt von Ihrem Szenario ab.

Weitere Informationen finden Sie unter <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Authentifizieren von Benutzern für das ausschließliche Aufrufen geschützter Drittanbieter-APIs

So authentifizieren Sie den Benutzer mit einem clientseitigen OAuth-Flow für den Drittanbieter einer API:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 Szenario:

* Der Server, der die App hostet, spielt keine Rolle.
* APIs auf dem Server können nicht geschützt werden.
* Die App kann nur geschützte APIs von Drittanbietern aufrufen.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Authentifizieren von Benutzern mit einem dritten Anbieter und Aufrufen geschützter APIs auf dem Hostserver und beim Drittanbieter

Konfigurieren Sie Identity mit einem dritten Anmeldeanbieter. Rufen Sie die Tokens ab, die für den Zugriff auf die API des Drittanbieters erforderlich sind, und speichern Sie sie.

Wenn sich ein Benutzer anmeldet, erfasst Identity als Teil des Authentifizierungsprozesses die Token für Zugriff und Aktualisierung. An dieser Stelle gibt es mehrere Ansätze, wie API-Aufrufe für APIs von Drittanbietern ausgeführt werden können.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Verwenden eines Serverzugriffstokens zum Abrufen des Zugriffstokens des Drittanbieters

Verwenden Sie das auf dem Server generierte Zugriffstoken, um das Zugriffstoken des Drittanbieters von einem API-Endpunkt des Servers abzurufen. Verwenden Sie dann das Zugriffstoken des Drittanbieters, um API-Ressourcen von Drittanbietern direkt über Identity auf dem Client abzurufen.

Dieser Ansatz wird nicht empfohlen. Für diesen Ansatz ist es erforderlich, die Drittanbieterzugriffstokens so zu behandeln, als wären sie für einen öffentlichen Client generiert worden. In OAuth-Konzepten ausgedrückt bedeutet dies Folgendes: Die öffentliche App verfügt über keinen geheimen Clientschlüssel, da sie nicht als vertrauenswürdig genug gilt, um Geheimnisse sicher zu speichern. Das Zugriffstoken kann jedoch nur einem vertrauenswürdigen Client übergeben werden. Ein vertrauenswürdiger Client ist ein Client, der über einen geheimen Clientschlüssel verfügt, und es wird davon ausgegangen, dass er Geheimnisse sicher speichern kann.

* Dem Drittanbieterzugriffstoken wird möglicherweise Zugriff auf zusätzliche Bereiche gewährt, um vertrauliche Vorgänge auszuführen. Das basiert auf der Tatsache, dass der Drittanbieter das Token für einen vertrauenswürdigeren Client übergeben hat.
* Ähnlich verhält es sich mit Aktualisierungstokens, die nicht für einen Client übergeben werden sollten, der als nicht vertrauenswürdig gilt. Würden Sie dies tun, erhält der Client uneingeschränkten Zugriff, es sei denn, es gelten anderweitige Einschränkungen.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Durchführen von API-Aufrufen auf dem Client für die Server-API zum Aufrufen von Drittanbieter-APIs

Führen Sie einen API-Aufruf auf dem Client für die Server-API durch. Rufen Sie auf dem Server das Zugriffstoken für die API-Ressource des Drittanbieters ab, und führen Sie danach den Aufruf aus, der erforderlich ist.

Obwohl für diesen Ansatz ein zusätzlicher Netzwerkhop auf dem Server erforderlich ist, um eine Drittanbieter-API aufzurufen, ist es letztendlich ein sichererer Ansatz:

* Der Server kann Aktualisierungstokens speichern und dafür sorgen, dass die App nicht den Zugriff auf Drittanbieterressourcen verliert.
* Die App kann Zugriffstokens auf dem Server nicht unberechtigterweise veröffentlichen, für den Berechtigungen mit noch höherer Vertraulichkeit erforderlich sind.

## <a name="use-openid-connect-oidc-v20-endpoints"></a>Verwenden von OpenID Connect 2.0-Endpunkten (OIDC)

Die Authentifizierungsbibliothek sowie Blazor-Projektvorlagen verwenden OpenID Connect 1.0-Endpunkte (OIDC). Wenn Sie einen Endpunkt der Version 2.0 verwenden möchten, konfigurieren Sie die JWT-Bearer-Option <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType>. Im folgenden Beispiel wird AAD für Version 2.0 konfiguriert, indem ein `v2.0`-Segment an die Eigenschaft <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> angehängt wird:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Alternativ kann die Einstellung in der Datei mit den App-Einstellungen (`appsettings.json`) festgelegt werden:

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Wenn das Anheften eines Segments an die Autorität für den OIDC-Anbieter der App nicht geeignet ist (z. B. bei Nicht-AAD-Anbietern), legen Sie die Eigenschaft <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> direkt fest. Legen Sie die Eigenschaft entweder in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> oder in der Datei mit den App-Einstellungen (`appsettings.json`) mit dem `Authority`-Schlüssel fest.

Die Liste der Ansprüche im ID-Token ändert sich für Endpunkte der Version 2.0. Weitere Informationen finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).

## <a name="configure-and-use-grpc-in-components"></a>Konfigurieren und Verwenden von gRPC in Komponenten

So konfigurieren Sie eine Blazor WebAssembly-App zur Verwendung des [ASP.NET Core gRPC-Frameworks](xref:grpc/index):

* Aktivieren Sie gRPC-Web auf dem Server. Weitere Informationen finden Sie unter <xref:grpc/browser>.
* Registrieren Sie gRPC-Dienste für den Meldungshandler der App. Im folgenden Beispiel wird der Autorisierungsmeldungshandler der App zur Verwendung des [`GreeterClient`-Diensts aus dem gRPC-Tutorial](xref:tutorials/grpc/grpc-start#create-a-grpc-service) (`Program.Main`) konfiguriert:

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

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample`). Platzieren Sie die `.proto`-Datei im `Shared`-Projekt der gehosteten Blazor-Projektmappe.

Eine Komponente in der Client-App kann gRPC-Aufrufe mithilfe des gRPC-Clients (`Pages/Grpc.razor`) ausführen:

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

Der Platzhalter `{APP ASSEMBLY}` ist der Assemblyname der App (z. B. `BlazorSample`). Verwenden Sie Version 2.30.0 oder höher von [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client), um die `Status.DebugException`-Eigenschaft zu verwenden.

Weitere Informationen finden Sie unter <xref:grpc/browser>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/security/webassembly/graph-api>
* [`HttpClient` und `HttpRequestMessage` mit Abruf-API-Anforderungsoptionen](xref:blazor/call-web-api#httpclient-and-httprequestmessage-with-fetch-api-request-options)
