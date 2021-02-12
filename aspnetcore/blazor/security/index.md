---
title: Authentifizierung und Autorisierung in ASP.NET Core Blazor
author: guardrex
description: Lernen Sie die Szenarien für die Authentifizierung und Autorisierung in Blazor kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: c786c00892772f9f0ce80c903bde495d4f2523f2
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106738"
---
# <a name="aspnet-core-blazor-authentication-and-authorization"></a><span data-ttu-id="2acde-103">Authentifizierung und Autorisierung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2acde-103">ASP.NET Core Blazor authentication and authorization</span></span>

<span data-ttu-id="2acde-104">Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2acde-104">By [Steve Sanderson](https://github.com/SteveSandersonMS) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2acde-105">ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="2acde-105">ASP.NET Core supports the configuration and management of security in Blazor apps.</span></span>

<span data-ttu-id="2acde-106">Es gibt jeweils unterschiedliche Sicherheitsszenarien für Blazor Server- und Blazor WebAssembly-Apps.</span><span class="sxs-lookup"><span data-stu-id="2acde-106">Security scenarios differ between Blazor Server and Blazor WebAssembly apps.</span></span> <span data-ttu-id="2acde-107">Da Blazor Server-Apps auf dem Server ausgeführt werden, kann durch Autorisierungsprüfungen Folgendes bestimmt werden:</span><span class="sxs-lookup"><span data-stu-id="2acde-107">Because Blazor Server apps run on the server, authorization checks are able to determine:</span></span>

* <span data-ttu-id="2acde-108">Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).</span><span class="sxs-lookup"><span data-stu-id="2acde-108">The UI options presented to a user (for example, which menu entries are available to a user).</span></span>
* <span data-ttu-id="2acde-109">Zugriffsregeln für Bereiche und Komponenten der App.</span><span class="sxs-lookup"><span data-stu-id="2acde-109">Access rules for areas of the app and components.</span></span>

<span data-ttu-id="2acde-110">Blazor WebAssembly-Apps werden auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2acde-110">Blazor WebAssembly apps run on the client.</span></span> <span data-ttu-id="2acde-111">Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-111">Authorization is *only* used to determine which UI options to show.</span></span> <span data-ttu-id="2acde-112">Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine Blazor WebAssembly-App keine Autorisierungszugriffsregeln durchsetzen.</span><span class="sxs-lookup"><span data-stu-id="2acde-112">Since client-side checks can be modified or bypassed by a user, a Blazor WebAssembly app can't enforce authorization access rules.</span></span>

<span data-ttu-id="2acde-113">[Autorisierungskonventionen von Razor Pages](xref:security/authorization/razor-pages-authorization) gelten nicht für routingfähige Razor-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2acde-113">[Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization) don't apply to routable Razor components.</span></span> <span data-ttu-id="2acde-114">Wenn eine nicht routingfähige Razor-Komponente [auf einer Seite eingebettet](xref:blazor/components/prerendering-and-integration) wird, wirken sich die Autorisierungskonventionen der Seite indirekt auf die Razor-Komponenten sowie auf den Rest des Seiteninhalts aus.</span><span class="sxs-lookup"><span data-stu-id="2acde-114">If a non-routable Razor component is [embedded in a page](xref:blazor/components/prerendering-and-integration), the page's authorization conventions indirectly affect the Razor component along with the rest of the page's content.</span></span>

> [!NOTE]
> <span data-ttu-id="2acde-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> und <xref:Microsoft.AspNetCore.Identity.UserManager%601> werden in Razor-Komponenten nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="2acde-115"><xref:Microsoft.AspNetCore.Identity.SignInManager%601> and <xref:Microsoft.AspNetCore.Identity.UserManager%601> aren't supported in Razor components.</span></span>

## <a name="authentication"></a><span data-ttu-id="2acde-116">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="2acde-116">Authentication</span></span>

<span data-ttu-id="2acde-117">Blazor verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen.</span><span class="sxs-lookup"><span data-stu-id="2acde-117">Blazor uses the existing ASP.NET Core authentication mechanisms to establish the user's identity.</span></span> <span data-ttu-id="2acde-118">Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird – über Blazor WebAssembly oder Blazor Server.</span><span class="sxs-lookup"><span data-stu-id="2acde-118">The exact mechanism depends on how the Blazor app is hosted, Blazor WebAssembly or Blazor Server.</span></span>

### <a name="blazor-webassembly-authentication"></a><span data-ttu-id="2acde-119">Authentifizierung per Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2acde-119">Blazor WebAssembly authentication</span></span>

<span data-ttu-id="2acde-120">In den Blazor WebAssembly-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann.</span><span class="sxs-lookup"><span data-stu-id="2acde-120">In Blazor WebAssembly apps, authentication checks can be bypassed because all client-side code can be modified by users.</span></span> <span data-ttu-id="2acde-121">Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.</span><span class="sxs-lookup"><span data-stu-id="2acde-121">The same is true for all client-side app technologies, including JavaScript SPA frameworks or native apps for any operating system.</span></span>

<span data-ttu-id="2acde-122">Fügen Sie Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="2acde-122">Add the following:</span></span>

* <span data-ttu-id="2acde-123">Einen Paketverweis für [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) zur Projektdatei der App.</span><span class="sxs-lookup"><span data-stu-id="2acde-123">A package reference for [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) to the app's project file.</span></span>
* <span data-ttu-id="2acde-124">Den Namespace `Microsoft.AspNetCore.Components.Authorization` zur `_Imports.razor`-Datei der App.</span><span class="sxs-lookup"><span data-stu-id="2acde-124">The `Microsoft.AspNetCore.Components.Authorization` namespace to the app's `_Imports.razor` file.</span></span>

<span data-ttu-id="2acde-125">Die Verwendung eines integrierten oder benutzerdefinierten <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>-Diensts für die Verarbeitung der Authentifizierung wird in den folgenden Abschnitten behandelt.</span><span class="sxs-lookup"><span data-stu-id="2acde-125">To handle authentication, use of a built-in or custom <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service is covered in the following sections.</span></span>

<span data-ttu-id="2acde-126">Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:blazor/security/webassembly/index>.</span><span class="sxs-lookup"><span data-stu-id="2acde-126">For more information on creating apps and configuration, see <xref:blazor/security/webassembly/index>.</span></span>

### <a name="blazor-server-authentication"></a><span data-ttu-id="2acde-127">Authentifizierung per Blazor Server</span><span class="sxs-lookup"><span data-stu-id="2acde-127">Blazor Server authentication</span></span>

<span data-ttu-id="2acde-128">Blazor Server-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="2acde-128">Blazor Server apps operate over a real-time connection that's created using SignalR.</span></span> <span data-ttu-id="2acde-129">Die [Authentifizierung in SignalR-basierten Apps wird verarbeitet](xref:signalr/authn-and-authz), wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2acde-129">[Authentication in SignalR-based apps](xref:signalr/authn-and-authz) is handled when the connection is established.</span></span> <span data-ttu-id="2acde-130">Die Authentifizierung kann auf einem cookie oder einem anderen Bearertoken basieren.</span><span class="sxs-lookup"><span data-stu-id="2acde-130">Authentication can be based on a cookie or some other bearer token.</span></span>

<span data-ttu-id="2acde-131">Der integrierte Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> für Blazor Server-Apps ruft die Daten zum Authentifizierungsstatus von der ASP. NET Core-Eigenschaft `HttpContext.User` ab.</span><span class="sxs-lookup"><span data-stu-id="2acde-131">The built-in <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service for Blazor Server apps obtains authentication state data from ASP.NET Core's `HttpContext.User`.</span></span> <span data-ttu-id="2acde-132">Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende Authentifizierungsmechanismen von ASP.NET Core integrieren.</span><span class="sxs-lookup"><span data-stu-id="2acde-132">This is how authentication state integrates with existing ASP.NET Core authentication mechanisms.</span></span>

<span data-ttu-id="2acde-133">Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:blazor/security/server/index>.</span><span class="sxs-lookup"><span data-stu-id="2acde-133">For more information on creating apps and configuration, see <xref:blazor/security/server/index>.</span></span>

## <a name="authenticationstateprovider-service"></a><span data-ttu-id="2acde-134">AuthenticationStateProvider-Dienst</span><span class="sxs-lookup"><span data-stu-id="2acde-134">AuthenticationStateProvider service</span></span>

<span data-ttu-id="2acde-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ist der zugrunde liegende Dienst, der von der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>- und der <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2acde-135"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> is the underlying service used by the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> component to get the authentication state.</span></span>

<span data-ttu-id="2acde-136"><xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> wird in der Regel nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="2acde-136">You don't typically use <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly.</span></span> <span data-ttu-id="2acde-137">Verwenden Sie die später in diesem Artikel beschriebenen Ansätze [`AuthorizeView`-Komponente](#authorizeview-component) oder [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter).</span><span class="sxs-lookup"><span data-stu-id="2acde-137">Use the [`AuthorizeView` component](#authorizeview-component) or [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) approaches described later in this article.</span></span> <span data-ttu-id="2acde-138">Der Hauptnachteil bei der direkten Verwendung von <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.</span><span class="sxs-lookup"><span data-stu-id="2acde-138">The main drawback to using <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> directly is that the component isn't notified automatically if the underlying authentication state data changes.</span></span>

<span data-ttu-id="2acde-139">Der Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="2acde-139">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> service can provide the current user's <xref:System.Security.Claims.ClaimsPrincipal> data, as shown in the following example:</span></span>

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="2acde-140">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-140">If `user.Identity.IsAuthenticated` is `true` and because the user is a <xref:System.Security.Claims.ClaimsPrincipal>, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2acde-141">Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/fundamentals/dependency-injection> und <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="2acde-141">For more information on dependency injection (DI) and services, see <xref:blazor/fundamentals/dependency-injection> and <xref:fundamentals/dependency-injection>.</span></span>

## <a name="implement-a-custom-authenticationstateprovider"></a><span data-ttu-id="2acde-142">Implementieren eines benutzerdefinierten AuthenticationStateProvider</span><span class="sxs-lookup"><span data-stu-id="2acde-142">Implement a custom AuthenticationStateProvider</span></span>

<span data-ttu-id="2acde-143">Wenn die App einen benutzerdefinierten Anbieter erfordert, implementieren Sie <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>, und überschreiben Sie `GetAuthenticationStateAsync`:</span><span class="sxs-lookup"><span data-stu-id="2acde-143">If the app requires a custom provider, implement <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> and override `GetAuthenticationStateAsync`:</span></span>

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

<span data-ttu-id="2acde-144">In einer Blazor WebAssembly-App ist der Dienst `CustomAuthStateProvider` in `Main` von `Program.cs` registriert:</span><span class="sxs-lookup"><span data-stu-id="2acde-144">In a Blazor WebAssembly app, the `CustomAuthStateProvider` service is registered in `Main` of `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="2acde-145">In einer Blazor Server-App ist der `CustomAuthStateProvider`-Dienst in `Startup.ConfigureServices` registriert:</span><span class="sxs-lookup"><span data-stu-id="2acde-145">In a Blazor Server app, the `CustomAuthStateProvider` service is registered in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

<span data-ttu-id="2acde-146">Bei der Verwendung von `CustomAuthStateProvider` im vorherigen Beispiel werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.</span><span class="sxs-lookup"><span data-stu-id="2acde-146">Using the `CustomAuthStateProvider` in the preceding example, all users are authenticated with the username `mrfibuli`.</span></span>

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a><span data-ttu-id="2acde-147">Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter</span><span class="sxs-lookup"><span data-stu-id="2acde-147">Expose the authentication state as a cascading parameter</span></span>

<span data-ttu-id="2acde-148">Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` definieren:</span><span class="sxs-lookup"><span data-stu-id="2acde-148">If authentication state data is required for procedural logic, such as when performing an action triggered by the user, obtain the authentication state data by defining a cascading parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`:</span></span>

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

<span data-ttu-id="2acde-149">Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-149">If `user.Identity.IsAuthenticated` is `true`, claims can be enumerated and membership in roles evaluated.</span></span>

<span data-ttu-id="2acde-150">Richten Sie den kaskadierenden Parameter `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` mit den Komponenten <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> und <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> in der `App`-Komponente (`App.razor`) ein:</span><span class="sxs-lookup"><span data-stu-id="2acde-150">Set up the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter using the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> and <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> components in the `App` component (`App.razor`):</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2acde-151">Fügen Sie in einer Blazor WebAssembly-App Dienste für Optionen und für die Autorisierung in `Program.Main` hinzu:</span><span class="sxs-lookup"><span data-stu-id="2acde-151">In a Blazor WebAssembly App, add services for options and authorization to `Program.Main`:</span></span>

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

<span data-ttu-id="2acde-152">In einer Blazor Server-App sind Dienste für Optionen und die Autorisierung bereits vorhanden. Es ist also keine weitere Aktion erforderlich.</span><span class="sxs-lookup"><span data-stu-id="2acde-152">In a Blazor Server app, services for options and authorization are already present, so no further action is required.</span></span>

## <a name="authorization"></a><span data-ttu-id="2acde-153">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="2acde-153">Authorization</span></span>

<span data-ttu-id="2acde-154">Nachdem ein Benutzer authentifiziert wurde, werden *Autorisierungsregeln* angewendet, um zu steuern, welche Funktionen der Benutzer ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="2acde-154">After a user is authenticated, *authorization* rules are applied to control what the user can do.</span></span>

<span data-ttu-id="2acde-155">In der Regel wird der Zugriff in Abhängigkeit von folgenden Punkten gewährt oder verweigert:</span><span class="sxs-lookup"><span data-stu-id="2acde-155">Access is typically granted or denied based on whether:</span></span>

* <span data-ttu-id="2acde-156">Ein Benutzer ist authentifiziert (angemeldet).</span><span class="sxs-lookup"><span data-stu-id="2acde-156">A user is authenticated (signed in).</span></span>
* <span data-ttu-id="2acde-157">Ein Benutzer hat eine *Rolle*.</span><span class="sxs-lookup"><span data-stu-id="2acde-157">A user is in a *role*.</span></span>
* <span data-ttu-id="2acde-158">Ein Benutzer hat einen *Anspruch*.</span><span class="sxs-lookup"><span data-stu-id="2acde-158">A user has a *claim*.</span></span>
* <span data-ttu-id="2acde-159">Ein *Richtlinie* wird erfüllt.</span><span class="sxs-lookup"><span data-stu-id="2acde-159">A *policy* is satisfied.</span></span>

<span data-ttu-id="2acde-160">Jedes dieser Konzepte entspricht dem einer ASP.NET Core MVC- oder Razor Pages-App.</span><span class="sxs-lookup"><span data-stu-id="2acde-160">Each of these concepts is the same as in an ASP.NET Core MVC or Razor Pages app.</span></span> <span data-ttu-id="2acde-161">Weitere Informationen zu ASP.NET Core-Sicherheit finden Sie im Artikel unter [ASP.NET Core-Sicherheit und -Identität](xref:security/index).</span><span class="sxs-lookup"><span data-stu-id="2acde-161">For more information on ASP.NET Core security, see the articles under [ASP.NET Core Security and Identity](xref:security/index).</span></span>

## <a name="authorizeview-component"></a><span data-ttu-id="2acde-162">AuthorizeView-Komponente</span><span class="sxs-lookup"><span data-stu-id="2acde-162">AuthorizeView component</span></span>

<span data-ttu-id="2acde-163">Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente zeigt selektiv den Inhalt der Benutzeroberfläche an, abhängig davon, ob der Benutzer dazu berechtigt ist.</span><span class="sxs-lookup"><span data-stu-id="2acde-163">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component selectively displays UI content depending on whether the user is authorized.</span></span> <span data-ttu-id="2acde-164">Dieser Ansatz ist nützlich, wenn Sie nur Daten für den Benutzer *anzeigen* müssen und nicht die Identität des Benutzers in der prozeduralen Logik verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="2acde-164">This approach is useful when you only need to *display* data for the user and don't need to use the user's identity in procedural logic.</span></span>

<span data-ttu-id="2acde-165">Die Komponente macht eine Variable `context` vom Typ <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> verfügbar, mit der Sie auf Informationen über den angemeldeten Benutzer zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="2acde-165">The component exposes a `context` variable of type <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>, which you can use to access information about the signed-in user:</span></span>

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

<span data-ttu-id="2acde-166">Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht autorisiert ist:</span><span class="sxs-lookup"><span data-stu-id="2acde-166">You can also supply different content for display if the user isn't authorized:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

<span data-ttu-id="2acde-167">Der Inhalt der Tags `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2acde-167">The content of `<Authorized>` and `<NotAuthorized>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2acde-168">Ein Standardereignishandler für ein autorisiertes Element, zum Beispiel die `SecureMethod`-Methode für das `<button>`-Element im vorangegangenen Beispiel, kann nur von einem autorisierten Benutzer aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-168">A default event handler for an authorized element, such as the `SecureMethod` method for the `<button>` element in the preceding example, can only be invoked by an authorized user.</span></span>

<span data-ttu-id="2acde-169">Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.</span><span class="sxs-lookup"><span data-stu-id="2acde-169">Authorization conditions, such as roles or policies that control UI options or access, are covered in the [Authorization](#authorization) section.</span></span>

<span data-ttu-id="2acde-170">Wenn keine Autorisierungsbedingungen angegeben sind, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> eine Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="2acde-170">If authorization conditions aren't specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses a default policy and treats:</span></span>

* <span data-ttu-id="2acde-171">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="2acde-171">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2acde-172">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="2acde-172">Unauthenticated (signed-out) users as unauthorized.</span></span>

<span data-ttu-id="2acde-173">Die Komponente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> kann in der Komponente `NavMenu` (`Shared/NavMenu.razor`) verwendet werden, um ein Listenelement (`<li>...</li>`) für eine [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) anzuzeigen. Beachten Sie jedoch, dass bei diesem Ansatz nur das Listenelement aus der gerenderten Ausgabe entfernt wird.</span><span class="sxs-lookup"><span data-stu-id="2acde-173">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component can be used in the `NavMenu` component (`Shared/NavMenu.razor`) to display a list item (`<li>...</li>`) for a [`NavLink` component](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>), but note that this approach only removes the list item from the rendered output.</span></span> <span data-ttu-id="2acde-174">Benutzer werden nicht daran gehindert, zur Komponente zu navigieren.</span><span class="sxs-lookup"><span data-stu-id="2acde-174">It doesn't prevent the user from navigating to the component.</span></span>

### <a name="role-based-and-policy-based-authorization"></a><span data-ttu-id="2acde-175">Rollenbasierte und richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="2acde-175">Role-based and policy-based authorization</span></span>

<span data-ttu-id="2acde-176">Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="2acde-176">The <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component supports *role-based* or *policy-based* authorization.</span></span>

<span data-ttu-id="2acde-177">Verwenden Sie für die rollenbasierte Autorisierung den <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2acde-177">For role-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parameter:</span></span>

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

<span data-ttu-id="2acde-178">Weitere Informationen finden Sie unter <xref:security/authorization/roles>.</span><span class="sxs-lookup"><span data-stu-id="2acde-178">For more information, see <xref:security/authorization/roles>.</span></span>

<span data-ttu-id="2acde-179">Verwenden Sie für die richtlinienbasierte Autorisierung den <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2acde-179">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parameter:</span></span>

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

<span data-ttu-id="2acde-180">Die anspruchsbasierte Autorisierung ist ein Sonderfall der richtlinienbasierten Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="2acde-180">Claims-based authorization is a special case of policy-based authorization.</span></span> <span data-ttu-id="2acde-181">Beispielsweise können Sie eine Richtlinie definieren, die vom Benutzer einen bestimmten Anspruch erfordert.</span><span class="sxs-lookup"><span data-stu-id="2acde-181">For example, you can define a policy that requires users to have a certain claim.</span></span> <span data-ttu-id="2acde-182">Weitere Informationen finden Sie unter <xref:security/authorization/policies>.</span><span class="sxs-lookup"><span data-stu-id="2acde-182">For more information, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="2acde-183">Diese APIs können entweder in Blazor Server- oder in Blazor WebAssembly-Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-183">These APIs can be used in either Blazor Server or Blazor WebAssembly apps.</span></span>

<span data-ttu-id="2acde-184">Wenn weder <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> noch <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> angegeben wird, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> die Standardrichtlinie.</span><span class="sxs-lookup"><span data-stu-id="2acde-184">If neither <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> nor <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> is specified, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> uses the default policy.</span></span>

### <a name="content-displayed-during-asynchronous-authentication"></a><span data-ttu-id="2acde-185">Während der asynchronen Authentifizierung angezeigter Inhalt</span><span class="sxs-lookup"><span data-stu-id="2acde-185">Content displayed during asynchronous authentication</span></span>

<span data-ttu-id="2acde-186">Bei Blazor kann der Authentifizierungsstatus *asynchron* bestimmt werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-186">Blazor allows for authentication state to be determined *asynchronously*.</span></span> <span data-ttu-id="2acde-187">Das primäre Szenario für diesen Ansatz sind Blazor WebAssembly-Apps, die eine Anforderung zur Authentifizierung an einen externen Endpunkt stellen.</span><span class="sxs-lookup"><span data-stu-id="2acde-187">The primary scenario for this approach is in Blazor WebAssembly apps that make a request to an external endpoint for authentication.</span></span>

<span data-ttu-id="2acde-188">Während der Authentifizierung zeigt <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> standardmäßig keine Inhalte an.</span><span class="sxs-lookup"><span data-stu-id="2acde-188">While authentication is in progress, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> displays no content by default.</span></span> <span data-ttu-id="2acde-189">Wenden Sie das `<Authorizing>`-Tag an, um während der Authentifizierung Inhalte anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="2acde-189">To display content while authentication occurs, use the `<Authorizing>` tag:</span></span>

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

<span data-ttu-id="2acde-190">Dieser Ansatz gilt in der Regel nicht für Blazor Server-Apps.</span><span class="sxs-lookup"><span data-stu-id="2acde-190">This approach isn't normally applicable to Blazor Server apps.</span></span> <span data-ttu-id="2acde-191">Blazor Server-Apps kennen den Authentifizierungsstatus, sobald der Status hergestellt ist.</span><span class="sxs-lookup"><span data-stu-id="2acde-191">Blazor Server apps know the authentication state as soon as the state is established.</span></span> <span data-ttu-id="2acde-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>-Inhalte können zwar in der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente einer Blazor Server-App bereitgestellt werden, aber der Inhalt wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2acde-192"><xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> content can be provided in a Blazor Server app's <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> component, but the content is never displayed.</span></span>

## <a name="authorize-attribute"></a><span data-ttu-id="2acde-193">[Authorize]-Attribut</span><span class="sxs-lookup"><span data-stu-id="2acde-193">[Authorize] attribute</span></span>

<span data-ttu-id="2acde-194">Das [`[Authorize]`-Attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) kann in Razor-Komponenten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="2acde-194">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) can be used in Razor components:</span></span>

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> <span data-ttu-id="2acde-195">Verwenden Sie nur [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) für `@page`-Komponenten, die über den Blazor-Router erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-195">Only use [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) on `@page` components reached via the Blazor Router.</span></span> <span data-ttu-id="2acde-196">Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="2acde-196">Authorization is only performed as an aspect of routing and *not* for child components rendered within a page.</span></span> <span data-ttu-id="2acde-197">Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.</span><span class="sxs-lookup"><span data-stu-id="2acde-197">To authorize the display of specific parts within a page, use <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> instead.</span></span>

<span data-ttu-id="2acde-198">Das [`[Authorize]`-Attribut](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="2acde-198">The [`[Authorize]` attribute](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) also supports role-based or policy-based authorization.</span></span> <span data-ttu-id="2acde-199">Verwenden Sie für die rollenbasierte Autorisierung den <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2acde-199">For role-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

<span data-ttu-id="2acde-200">Verwenden Sie für die richtlinienbasierte Autorisierung den <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>-Parameter:</span><span class="sxs-lookup"><span data-stu-id="2acde-200">For policy-based authorization, use the <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parameter:</span></span>

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<span data-ttu-id="2acde-201">Wenn weder <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> noch <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> angegeben wird, verwendet [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) die Standardrichtlinie und behandelt:</span><span class="sxs-lookup"><span data-stu-id="2acde-201">If neither <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> nor <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> is specified, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) uses the default policy, which by default is to treat:</span></span>

* <span data-ttu-id="2acde-202">Authentifizierte (angemeldete) Benutzer als autorisiert.</span><span class="sxs-lookup"><span data-stu-id="2acde-202">Authenticated (signed-in) users as authorized.</span></span>
* <span data-ttu-id="2acde-203">Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.</span><span class="sxs-lookup"><span data-stu-id="2acde-203">Unauthenticated (signed-out) users as unauthorized.</span></span>

## <a name="customize-unauthorized-content-with-the-router-component"></a><span data-ttu-id="2acde-204">Anpassen von unautorisierten Inhalten mit der Router-Komponente</span><span class="sxs-lookup"><span data-stu-id="2acde-204">Customize unauthorized content with the Router component</span></span>

<span data-ttu-id="2acde-205">Gemeinsam mit der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>-Komponente ermöglicht die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente der App, benutzerdefinierten Inhalt anzugeben, wenn:</span><span class="sxs-lookup"><span data-stu-id="2acde-205">The <xref:Microsoft.AspNetCore.Components.Routing.Router> component, in conjunction with the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> component, allows the app to specify custom content if:</span></span>

* <span data-ttu-id="2acde-206">Der Benutzer eine [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Bedingung nicht erfüllt, die für die Komponente angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="2acde-206">The user fails an [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) condition applied to the component.</span></span> <span data-ttu-id="2acde-207">Das Markup des [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType)-Elements wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2acde-207">The markup of the [`<NotAuthorized>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.NotAuthorized?displayProperty=nameWithType) element is displayed.</span></span> <span data-ttu-id="2acde-208">Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut wird im Abschnitt [`[Authorize]`-Attribut](#authorize-attribute) behandelt.</span><span class="sxs-lookup"><span data-stu-id="2acde-208">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is covered in the [`[Authorize]` attribute](#authorize-attribute) section.</span></span>
* <span data-ttu-id="2acde-209">Asynchrone Autorisierung wird ausgeführt. Dies bedeutet in der Regel, dass der Benutzer authentifiziert wird.</span><span class="sxs-lookup"><span data-stu-id="2acde-209">Asynchronous authorization is in progress, which usually means that the process of authenticating the user is in progress.</span></span> <span data-ttu-id="2acde-210">Das Markup des [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType)-Elements wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2acde-210">The markup of the [`<Authorizing>`](xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView.Authorizing?displayProperty=nameWithType) element is displayed.</span></span>
* <span data-ttu-id="2acde-211">Inhalt nicht gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="2acde-211">Content isn't found.</span></span> <span data-ttu-id="2acde-212">Das Markup des [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType)-Elements wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="2acde-212">The markup of the [`<NotFound>`](xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound?displayProperty=nameWithType) element is displayed.</span></span>

<span data-ttu-id="2acde-213">In der Standardprojektvorlage für Blazor Server zeigt die Komponente `App` (`App.razor`), wie benutzerdefinierte Inhalte festgelegt werden können:</span><span class="sxs-lookup"><span data-stu-id="2acde-213">In the default Blazor Server project template, the `App` component (`App.razor`) demonstrates how to set custom content:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authorization in progress</h1>
                    <p>Only visible while authorization is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2acde-214">Der Inhalt der Tags `<NotFound>`, `<NotAuthorized>` und `<Authorizing>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.</span><span class="sxs-lookup"><span data-stu-id="2acde-214">The content of `<NotFound>`, `<NotAuthorized>`, and `<Authorizing>` tags can include arbitrary items, such as other interactive components.</span></span>

<span data-ttu-id="2acde-215">Wenn das Tag `<NotAuthorized>` nicht angegeben ist, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> die folgende alternative Meldung:</span><span class="sxs-lookup"><span data-stu-id="2acde-215">If the `<NotAuthorized>` tag isn't specified, the <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> uses the following fallback message:</span></span>

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a><span data-ttu-id="2acde-216">Benachrichtigung über Änderungen des Authentifizierungsstatus</span><span class="sxs-lookup"><span data-stu-id="2acde-216">Notification about authentication state changes</span></span>

<span data-ttu-id="2acde-217">Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z. B. weil sich ein Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein [benutzerdefinierter `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) optional die Methode <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> in der Basisklasse <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> aufrufen.</span><span class="sxs-lookup"><span data-stu-id="2acde-217">If the app determines that the underlying authentication state data has changed (for example, because the user signed out or another user has changed their roles), a [custom `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) can optionally invoke the method <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> on the <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> base class.</span></span> <span data-ttu-id="2acde-218">Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="2acde-218">This notifies consumers of the authentication state data (for example, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) to rerender using the new data.</span></span>

## <a name="procedural-logic"></a><span data-ttu-id="2acde-219">Prozedurale Logik</span><span class="sxs-lookup"><span data-stu-id="2acde-219">Procedural logic</span></span>

<span data-ttu-id="2acde-220">Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2acde-220">If the app is required to check authorization rules as part of procedural logic, use a cascaded parameter of type `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` to obtain the user's <xref:System.Security.Claims.ClaimsPrincipal>.</span></span> <span data-ttu-id="2acde-221">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z. B. `IAuthorizationService`.</span><span class="sxs-lookup"><span data-stu-id="2acde-221">`Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` can be combined with other services, such as `IAuthorizationService`, to evaluate policies.</span></span>

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="2acde-222">Fügen Sie in einer Komponente einer <xref:Microsoft.AspNetCore.Components.Authorization>-App die Namespaces Blazor WebAssembly und <xref:Microsoft.AspNetCore.Authorization> hinzu:</span><span class="sxs-lookup"><span data-stu-id="2acde-222">In a Blazor WebAssembly app component, add the <xref:Microsoft.AspNetCore.Authorization> and <xref:Microsoft.AspNetCore.Components.Authorization> namespaces:</span></span>
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> <span data-ttu-id="2acde-223">Sie können diese Namespaces global bereitstellen, indem Sie sie der Datei `_Imports.razor` der App hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="2acde-223">These namespaces can be provided globally by adding them to the app's `_Imports.razor` file.</span></span>

## <a name="troubleshoot-errors"></a><span data-ttu-id="2acde-224">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="2acde-224">Troubleshoot errors</span></span>

<span data-ttu-id="2acde-225">Häufige Fehler:</span><span class="sxs-lookup"><span data-stu-id="2acde-225">Common errors:</span></span>

* <span data-ttu-id="2acde-226">**Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ `Task\<AuthenticationState>`. Verwenden Sie `CascadingAuthenticationState`, um diesen bereitzustellen.**</span><span class="sxs-lookup"><span data-stu-id="2acde-226">**Authorization requires a cascading parameter of type `Task\<AuthenticationState>`. Consider using `CascadingAuthenticationState` to supply this.**</span></span>

* <span data-ttu-id="2acde-227">**Für `authenticationStateTask` wird ein `null`** -Wert empfangen.</span><span class="sxs-lookup"><span data-stu-id="2acde-227">**`null` value is received for `authenticationStateTask`**</span></span>

<span data-ttu-id="2acde-228">Wahrscheinlich wurde das Projekt nicht mit einer Blazor Server-Vorlage mit aktivierter Authentifizierung erstellt.</span><span class="sxs-lookup"><span data-stu-id="2acde-228">It's likely that the project wasn't created using a Blazor Server template with authentication enabled.</span></span> <span data-ttu-id="2acde-229">Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z. B. in der Komponente `App` (`App.razor`):</span><span class="sxs-lookup"><span data-stu-id="2acde-229">Wrap a `<CascadingAuthenticationState>` around some part of the UI tree, for example in the `App` component (`App.razor`) as follows:</span></span>

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<span data-ttu-id="2acde-230"><xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> stellt den kaskadierenden Parameter `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` zur Verfügung, den er wiederum vom zugrunde liegenden DI-Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> empfängt.</span><span class="sxs-lookup"><span data-stu-id="2acde-230">The <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> supplies the `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` cascading parameter, which in turn it receives from the underlying <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> DI service.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2acde-231">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2acde-231">Additional resources</span></span>

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [<span data-ttu-id="2acde-232">Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="2acde-232">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* <span data-ttu-id="2acde-233">[Nützliche Ressourcen für Blazor: Authentifizierung](https://github.com/AdrienTorris/awesome-blazor#authentication) – Beispiellinks für die Community</span><span class="sxs-lookup"><span data-stu-id="2acde-233">[Awesome Blazor: Authentication](https://github.com/AdrienTorris/awesome-blazor#authentication) community sample links</span></span>
