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
ms.openlocfilehash: f8b31c617ef71003042d31690de49d48946ac3d5
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710645"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a>Authentifizierung und Autorisierung in ASP.NET Core Blazor

Von [Steve Sanderson](https://github.com/SteveSandersonMS) und [Luke Latham](https://github.com/guardrex)

ASP.NET Core unterstützt die Konfiguration und Verwaltung der Sicherheit in Blazor-Apps.

Es gibt jeweils unterschiedliche Sicherheitsszenarien für Blazor Server- und Blazor WebAssembly-Apps. Da Blazor Server-Apps auf dem Server ausgeführt werden, kann durch Autorisierungsprüfungen Folgendes bestimmt werden:

* Die UI-Optionen, die einem Benutzer angezeigt werden (z.B., welche Menüeinträge für einen Benutzer verfügbar sind).
* Zugriffsregeln für Bereiche und Komponenten der App.

Blazor WebAssembly-Apps werden auf dem Client ausgeführt. Die Autorisierung wird *nur* verwendet, um zu bestimmen, welche UI-Optionen angezeigt werden. Da clientseitige Prüfungen von einem Benutzer geändert oder umgangen werden können, kann eine Blazor WebAssembly-App keine Autorisierungszugriffsregeln durchsetzen.

[Autorisierungskonventionen von Razor Pages](xref:security/authorization/razor-pages-authorization) gelten nicht für routingfähige Razor-Komponenten. Wenn eine nicht routingfähige Razor-Komponente [auf einer Seite eingebettet](xref:blazor/components/prerendering-and-integration) wird, wirken sich die Autorisierungskonventionen der Seite indirekt auf die Razor-Komponenten sowie auf den Rest des Seiteninhalts aus.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> und <xref:Microsoft.AspNetCore.Identity.UserManager%601> werden in Razor-Komponenten nicht unterstützt.

## <a name="authentication"></a>Authentifizierung

Blazor verwendet die vorhandenen ASP.NET Core-Authentifizierungsmechanismen, um die Identität des Benutzers festzustellen. Der genaue Mechanismus hängt davon ab, wie die Blazor-App gehostet wird – über Blazor WebAssembly oder Blazor Server.

### <a name="no-locblazor-webassembly-authentication"></a>Authentifizierung per Blazor WebAssembly

In den Blazor WebAssembly-Apps können Authentifizierungsprüfungen umgangen werden, da der gesamte clientseitige Code von Benutzern geändert werden kann. Dasselbe gilt für alle clientseitigen App-Technologien, einschließlich JavaScript SPA-Frameworks oder native Apps für jedes Betriebssystem.

Fügen Sie Folgendes hinzu:

* Einen Paketverweis für [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) zur Projektdatei der App.
* Den Namespace `Microsoft.AspNetCore.Components.Authorization` zur `_Imports.razor`-Datei der App.

Die Verwendung eines integrierten oder benutzerdefinierten <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>-Diensts für die Verarbeitung der Authentifizierung wird in den folgenden Abschnitten behandelt.

Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:blazor/security/webassembly/index>.

### <a name="no-locblazor-server-authentication"></a>Authentifizierung per Blazor Server

Blazor Server-Apps funktionieren über eine Echtzeitverbindung, die mit SignalR erstellt wurde. Die [Authentifizierung in SignalR-basierten Apps wird verarbeitet](xref:signalr/authn-and-authz), wenn die Verbindung hergestellt wird. Die Authentifizierung kann auf einem cookie oder einem anderen Bearertoken basieren.

Der integrierte Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> für Blazor Server-Apps ruft die Daten zum Authentifizierungsstatus von der ASP. NET Core-Eigenschaft `HttpContext.User` ab. Auf diese Weise lässt sich der Authentifizierungsstatus in bestehende Authentifizierungsmechanismen von ASP.NET Core integrieren.

Weitere Informationen zur Erstellung von Apps und Konfigurationen finden Sie unter <xref:blazor/security/server/index>.

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider-Dienst

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ist der zugrunde liegende Dienst, der von der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>- und der <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState>-Komponente verwendet wird, um den Authentifizierungsstatus abzurufen.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> wird in der Regel nicht direkt verwendet. Verwenden Sie die später in diesem Artikel beschriebenen Ansätze [`AuthorizeView`-Komponente](#authorizeview-component) oder [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter). Der Hauptnachteil bei der direkten Verwendung von <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ist, dass die Komponente nicht automatisch benachrichtigt wird, wenn sich die zugrunde liegenden Daten des Authentifizierungsstatus ändern.

Der Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> kann die <xref:System.Security.Claims.ClaimsPrincipal>-Daten des aktuellen Benutzers bereitstellen, wie im folgenden Beispiel gezeigt:

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

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat und da der Benutzer ein <xref:System.Security.Claims.ClaimsPrincipal> ist, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Weitere Informationen zur Dependency Injection (DI) und den Diensten finden Sie unter <xref:blazor/fundamentals/dependency-injection> und <xref:fundamentals/dependency-injection>.

## <a name="implement-a-custom-authenticationstateprovider"></a>Implementieren eines benutzerdefinierten AuthenticationStateProvider

Wenn die App einen benutzerdefinierten Anbieter erfordert, implementieren Sie <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>, und überschreiben Sie `GetAuthenticationStateAsync`:

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

In einer Blazor WebAssembly-App ist der Dienst `CustomAuthStateProvider` in `Main` von `Program.cs` registriert:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

In einer Blazor Server-App ist der `CustomAuthStateProvider`-Dienst in `Startup.ConfigureServices` registriert:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bei der Verwendung von `CustomAuthStateProvider` im vorherigen Beispiel werden alle Benutzer mit dem Benutzernamen `mrfibuli` authentifiziert.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Verfügbar machen des Authentifizierungsstatus als kaskadierender Parameter

Wenn für die prozedurale Logik Authentifizierungsstatusdaten erforderlich sind, z. B. bei der Ausführung einer vom Benutzer ausgelösten Aktion, können Sie die Authentifizierungsstatusdaten abrufen, indem Sie einen kaskadierenden Parameter vom Typ `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` definieren:

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

Wenn `user.Identity.IsAuthenticated` den Wert `true` hat, können Ansprüche aufgezählt und die Mitgliedschaft in Rollen ausgewertet werden.

Richten Sie den kaskadierenden Parameter `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` mit den Komponenten <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> und <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> in der `App`-Komponente (`App.razor`) ein:

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

Fügen Sie in einer Blazor WebAssembly-App Dienste für Optionen und für die Autorisierung in `Program.Main` hinzu:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

In einer Blazor Server-App sind Dienste für Optionen und die Autorisierung bereits vorhanden. Es ist also keine weitere Aktion erforderlich.

## <a name="authorization"></a>Autorisierung

Nachdem ein Benutzer authentifiziert wurde, werden *Autorisierungsregeln* angewendet, um zu steuern, welche Funktionen der Benutzer ausführen kann.

In der Regel wird der Zugriff in Abhängigkeit von folgenden Punkten gewährt oder verweigert:

* Ein Benutzer ist authentifiziert (angemeldet).
* Ein Benutzer hat eine *Rolle*.
* Ein Benutzer hat einen *Anspruch*.
* Ein *Richtlinie* wird erfüllt.

Jedes dieser Konzepte entspricht dem einer ASP.NET Core MVC- oder Razor Pages-App. Weitere Informationen zu ASP.NET Core-Sicherheit finden Sie im Artikel unter [ASP.NET Core-Sicherheit und -Identität](xref:security/index).

## <a name="authorizeview-component"></a>AuthorizeView-Komponente

Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente zeigt selektiv den Inhalt der Benutzeroberfläche an, abhängig davon, ob der Benutzer dazu berechtigt ist. Dieser Ansatz ist nützlich, wenn Sie nur Daten für den Benutzer *anzeigen* müssen und nicht die Identität des Benutzers in der prozeduralen Logik verwenden müssen.

Die Komponente macht eine Variable `context` vom Typ <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> verfügbar, mit der Sie auf Informationen über den angemeldeten Benutzer zugreifen können:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Sie können auch andere Inhalte zur Anzeige bereitstellen, wenn der Benutzer nicht autorisiert ist:

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

Der Inhalt der Tags `<Authorized>` und `<NotAuthorized>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.

Ein Standardereignishandler für ein autorisiertes Element, zum Beispiel die `SecureMethod`-Methode für das `<button>`-Element im vorangegangenen Beispiel, kann nur von einem autorisierten Benutzer aufgerufen werden.

Autorisierungsbedingungen, wie Rollen oder Richtlinien, die Benutzeroberflächenoptionen oder den Zugriff steuern, werden im Abschnitt [Autorisierung](#authorization) behandelt.

Wenn keine Autorisierungsbedingungen angegeben sind, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> eine Standardrichtlinie und behandelt:

* Authentifizierte (angemeldete) Benutzer als autorisiert.
* Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.

Die Komponente <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> kann in der Komponente `NavMenu` (`Shared/NavMenu.razor`) verwendet werden, um ein Listenelement (`<li>...</li>`) für eine [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) anzuzeigen. Beachten Sie jedoch, dass bei diesem Ansatz nur das Listenelement aus der gerenderten Ausgabe entfernt wird. Benutzer werden nicht daran gehindert, zur Komponente zu navigieren.

### <a name="role-based-and-policy-based-authorization"></a>Rollenbasierte und richtlinienbasierte Autorisierung

Die <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente unterstützt die *rollenbasierte* oder die *richtlinienbasierte* Autorisierung.

Verwenden Sie für die rollenbasierte Autorisierung den <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles>-Parameter:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Weitere Informationen finden Sie unter <xref:security/authorization/roles>.

Verwenden Sie für die richtlinienbasierte Autorisierung den <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy>-Parameter:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Die anspruchsbasierte Autorisierung ist ein Sonderfall der richtlinienbasierten Autorisierung. Beispielsweise können Sie eine Richtlinie definieren, die vom Benutzer einen bestimmten Anspruch erfordert. Weitere Informationen finden Sie unter <xref:security/authorization/policies>.

Diese APIs können entweder in Blazor Server- oder in Blazor WebAssembly-Apps verwendet werden.

Wenn weder <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> noch <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> angegeben wird, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> die Standardrichtlinie.

### <a name="content-displayed-during-asynchronous-authentication"></a>Während der asynchronen Authentifizierung angezeigter Inhalt

Bei Blazor kann der Authentifizierungsstatus *asynchron* bestimmt werden. Das primäre Szenario für diesen Ansatz sind Blazor WebAssembly-Apps, die eine Anforderung zur Authentifizierung an einen externen Endpunkt stellen.

Während der Authentifizierung zeigt <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> standardmäßig keine Inhalte an. Wenden Sie das `<Authorizing>`-Tag an, um während der Authentifizierung Inhalte anzuzeigen:

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

Dieser Ansatz gilt in der Regel nicht für Blazor Server-Apps. Blazor Server-Apps kennen den Authentifizierungsstatus, sobald der Status hergestellt ist. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing>-Inhalte können zwar in der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>-Komponente einer Blazor Server-App bereitgestellt werden, aber der Inhalt wird nie angezeigt.

## <a name="authorize-attribute"></a>[Authorize]-Attribut

Das Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) kann in Razor-Komponenten verwendet werden:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Verwenden Sie nur [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) für `@page`-Komponenten, die über den Blazor-Router erreicht werden. Die Autorisierung wird nur als Aspekt des Routings und *nicht* für untergeordnete Komponenten durchgeführt, die innerhalb einer Seite gerendert werden. Um die Anzeige von bestimmten Teilen innerhalb einer Seite zu autorisieren, verwenden Sie stattdessen <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>.

Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut unterstützt auch die rollenbasierte oder die richtlinienbasierte Autorisierung. Verwenden Sie für die rollenbasierte Autorisierung den <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>-Parameter:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

Verwenden Sie für die richtlinienbasierte Autorisierung den <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy>-Parameter:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Wenn weder <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> noch <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> angegeben wird, verwendet [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) die Standardrichtlinie und behandelt:

* Authentifizierte (angemeldete) Benutzer als autorisiert.
* Nicht authentifizierte (abgemeldete) Benutzer als nicht autorisiert.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Anpassen von unautorisierten Inhalten mit der Router-Komponente

Gemeinsam mit der <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView>-Komponente ermöglicht die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente der App, benutzerdefinierten Inhalt anzugeben, wenn:

* Inhalt nicht gefunden wird.
* Der Benutzer eine [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Bedingung nicht erfüllt, die für die Komponente angewendet wird. Das [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)-Attribut wird im Abschnitt [`[Authorize]`-Attribut](#authorize-attribute) behandelt.
* Die Asynchrone Authentifizierung ausgeführt wird.

In der Standardprojektvorlage für Blazor Server zeigt die Komponente `App` (`App.razor`), wie benutzerdefinierte Inhalte festgelegt werden können:

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
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
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

Der Inhalt der Tags `<NotFound>`, `<NotAuthorized>` und `<Authorizing>` kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten.

Wenn das Tag `<NotAuthorized>` nicht angegeben ist, verwendet <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> die folgende alternative Meldung:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Benachrichtigung über Änderungen des Authentifizierungsstatus

Wenn die App feststellt, dass sich die zugrunde liegenden Daten des Authentifizierungsstatus geändert haben (z. B. weil sich ein Benutzer abgemeldet hat oder ein anderer Benutzer seine Rollen geändert hat), kann ein [benutzerdefinierter `AuthenticationStateProvider`](#implement-a-custom-authenticationstateprovider) optional die Methode <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> in der Basisklasse <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> aufrufen. Dadurch werden die Verbraucher der Authentifizierungsstatusdaten (z. B. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>) informiert, dass der Rendervorgang mit den neuen Daten erneut durchgeführt werden muss.

## <a name="procedural-logic"></a>Prozedurale Logik

Wenn die App zur Überprüfung von Autorisierungsregeln im Rahmen der prozeduralen Logik benötigt wird, verwenden Sie einen kaskadierten Parameter vom Typ `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>`, um das <xref:System.Security.Claims.ClaimsPrincipal> des Benutzers abzurufen. `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` kann zum Auswerten von Richtlinien mit anderen Diensten kombiniert werden, wie z. B. `IAuthorizationService`.

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
> Fügen Sie in einer Komponente einer <xref:Microsoft.AspNetCore.Components.Authorization>-App die Namespaces Blazor WebAssembly und <xref:Microsoft.AspNetCore.Authorization> hinzu:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Sie können diese Namespaces global bereitstellen, indem Sie sie der Datei `_Imports.razor` der App hinzufügen.

## <a name="troubleshoot-errors"></a>Fehlerbehandlung

Häufige Fehler:

* **Die Autorisierung erfordert einen kaskadierenden Parameter vom Typ `Task\<AuthenticationState>`. Verwenden Sie `CascadingAuthenticationState`, um diesen bereitzustellen.**

* **Für `authenticationStateTask` wird ein `null`** -Wert empfangen.

Wahrscheinlich wurde das Projekt nicht mit einer Blazor Server-Vorlage mit aktivierter Authentifizierung erstellt. Umschließen Sie einen Teil der Benutzeroberflächenstruktur wie folgt mit `<CascadingAuthenticationState>`, z. B. in der Komponente `App` (`App.razor`):

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

<xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> stellt den kaskadierenden Parameter `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>` zur Verfügung, den er wiederum vom zugrunde liegenden DI-Dienst <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> empfängt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Erstellen einer benutzerdefinierten Version der Authentication.MSAL-JavaScript-Bibliothek](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Nützliche Ressourcen für Blazor: Authentifizierung](https://github.com/AdrienTorris/awesome-blazor#authentication) – Beispiellinks für die Community
