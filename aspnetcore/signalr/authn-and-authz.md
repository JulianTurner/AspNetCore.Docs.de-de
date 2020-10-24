---
title: Authentifizierung und Autorisierung in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core verwenden SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: e16efa59a82d0f3cb1a2272ae0c07654ebec6a51
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491560"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>Authentifizierung und Autorisierung in ASP.net Core SignalR

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Authentifizieren von Benutzern, die eine Verbindung mit einem SignalR Hub herstellen

SignalR kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem Verbindung einen Benutzer zuzuordnen. In einem Hub kann über die [hubconnectioncontext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) -Eigenschaft auf Authentifizierungsdaten zugegriffen werden. Die Authentifizierung ermöglicht dem Hub das Abrufen von Methoden für alle Verbindungen, die einem Benutzer zugeordnet sind. Weitere Informationen finden Sie unter [Verwalten von Benutzern und Gruppen SignalR in ](xref:signalr/groups). Mehrere Verbindungen können einem einzelnen Benutzer zugeordnet werden.

Im folgenden finden Sie ein Beispiel für `Startup.Configure` die Verwendung von SignalR und ASP.net Core-Authentifizierung:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> Die Reihenfolge, in der Sie die SignalR und ASP.net Core Authentifizierungs Middleware registriert, ist wichtig. Ruft immer `UseAuthentication` vor `UseSignalR` auf, sodass SignalR über einen Benutzer auf dem verfügt `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>Authentifizierung per Cookie

In einer browserbasierten APP cookie ermöglicht die Authentifizierung, dass Ihre vorhandenen Benutzer Anmelde Informationen automatisch an Verbindungen weitergeleitet werden SignalR . Wenn Sie den Browser Client verwenden, ist keine zusätzliche Konfiguration erforderlich. Wenn der Benutzer bei ihrer App angemeldet ist, SignalR erbt die Verbindung diese Authentifizierung automatisch.

Cookies sind eine browserspezifische Methode zum Senden von Zugriffs Token, die von nicht-Browser Clients jedoch gesendet werden können. Wenn Sie den [.NET-Client](xref:signalr/dotnet-client)verwenden, kann die- `Cookies` Eigenschaft im-Befehl konfiguriert werden, `.WithUrl` um einen bereitzustellen cookie . Allerdings cookie erfordert die Verwendung der-Authentifizierung über den .NET-Client, dass die APP eine API zum Austauschen von Authentifizierungsdaten für einen bereitstellt cookie .

### <a name="bearer-token-authentication"></a>Bearertokenauthentifizierung

Der Client kann ein Zugriffs Token bereitstellen, anstatt zu verwenden cookie . Der Server überprüft das Token und verwendet es zum Identifizieren des Benutzers. Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird. Während der Lebensdauer der Verbindung wird der Server nicht automatisch neu validiert, um die tokensperrung zu überprüfen.

Im JavaScript-Client kann das Token mithilfe der [accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) -Option bereitgestellt werden.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

Im .NET-Client gibt es eine ähnliche [accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die verwendet werden kann, um das Token zu konfigurieren:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Die von Ihnen bereitgestellte zugriffstokenfunktion wird vor **jeder** http-Anforderung von aufgerufen SignalR . Wenn Sie das Token erneuern müssen, um die Verbindung aktiv zu halten (da es während der Verbindung ablaufen kann), verwenden Sie dies innerhalb dieser Funktion, und geben Sie das aktualisierte Token zurück.

In Standard-Web-APIs werden bearertoken in einem HTTP-Header gesendet. SignalRKann diese Header jedoch nicht in Browsern festlegen, wenn einige Transporte verwendet werden. Wenn websockets und Server-Sent Ereignisse verwendet werden, wird das Token als Abfrage Zeichenfolgen-Parameter übertragen. 

#### <a name="built-in-jwt-authentication"></a>Integrierte JWT-Authentifizierung

Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT-bearermiddleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)konfiguriert:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Die Abfrage Zeichenfolge wird in Browsern verwendet, wenn eine Verbindung mit websockets hergestellt und Ereignisse aufgrund von Einschränkungen der Browser-API Server-Sent werden. Bei Verwendung von HTTPS werden Abfrage Zeichen folgen Werte durch die TLS-Verbindung gesichert. Viele Server protokollieren jedoch Abfrage Zeichen folgen Werte. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in SignalR ASP.net Core ](xref:signalr/security). SignalR verwendet Header zum Übertragen von Token in Umgebungen, die diese unterstützen (z. b. die .net-und Java-Clients).

#### <a name="no-locidentity-server-jwt-authentication"></a>Identity Server-JWT-Authentifizierung

Wenn Sie Identity Server verwenden, fügen Sie <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> dem Projekt einen Dienst hinzu:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

Registrieren Sie den Dienst in, `Startup.ConfigureServices` nachdem Sie Dienste für <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> die Authentifizierung () und den Authentifizierungs Handler für Identity Server () hinzugefügt haben <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> :

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookiee im Vergleich zu Träger Token 

Cookiee sind für Browser spezifisch. Das Senden von anderen Arten von Clients erhöht die Komplexität im Vergleich zum Senden von bearertoken. Daher wird cookie die Authentifizierung nicht empfohlen, es sei denn, die APP muss nur Benutzer über den Browser Client authentifizieren. Die bearertokenauthentifizierung ist die empfohlene Vorgehensweise, wenn andere Clients als der Browser Client verwendet werden.

### <a name="windows-authentication"></a>Windows-Authentifizierung

Wenn die [Windows-Authentifizierung](xref:security/authentication/windowsauth) in ihrer App konfiguriert ist, SignalR kann diese Identität zum Sichern von Hubs verwenden. Zum Senden von Nachrichten an einzelne Benutzer müssen Sie jedoch einen benutzerdefinierten Benutzer-ID-Anbieter hinzufügen. Das Windows-Authentifizierungssystem stellt den Anspruch "namensbezeichneranspruch" nicht bereit. SignalR verwendet den-Anspruch, um den Benutzernamen zu ermitteln.

Fügen Sie eine neue Klasse hinzu, die implementiert `IUserIdProvider` und einen der Ansprüche des Benutzers abruft, der als Bezeichner verwendet werden soll. Wenn Sie z. b. den Anspruch "Name" (den Windows-Benutzernamen im Formular `[Domain]\[Username]` ) verwenden möchten, erstellen Sie die folgende Klasse:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Anstelle `ClaimTypes.Name` von können Sie einen beliebigen Wert aus dem `User` (z. b. den Windows-SID-Bezeichner usw.) verwenden.

> [!NOTE]
> Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein. Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.

Registrieren Sie diese Komponente in der- `Startup.ConfigureServices` Methode.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

Im .NET-Client muss die Windows-Authentifizierung aktiviert werden, indem die [usedefault-Anmelde](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Informationen-Eigenschaft festgelegt wird:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Die Windows-Authentifizierung wird in Internet Explorer und Microsoft Edge unterstützt, aber nicht in allen Browsern. Beispielsweise kann in Chrome und Safari der Versuch, Windows-Authentifizierung und websockets zu verwenden, nicht ausgeführt werden. Wenn die Windows-Authentifizierung fehlschlägt, versucht der Client, auf andere Transporte zurückzugreifen, die möglicherweise funktionieren.

### <a name="use-claims-to-customize-identity-handling"></a>Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung

Eine APP, die Benutzer authentifiziert, kann SignalR Benutzer-IDs von Benutzer Ansprüchen ableiten. Um anzugeben SignalR , wie Benutzer-IDs von erstellt werden, implementieren `IUserIdProvider` und registrieren Sie die Implementierung.

Der Beispielcode veranschaulicht, wie Sie Ansprüche verwenden, um die e-Mail-Adresse des Benutzers als identifizierende Eigenschaft auszuwählen. 

> [!NOTE]
> Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein. Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Die Kontoregistrierung fügt der ASP.net-Identitätsdatenbank einen Anspruch mit dem Typ hinzu `ClaimsTypes.Email` .

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Registrieren Sie diese Komponente in Ihrer `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Autorisieren von Benutzern für den Zugriff auf Hubs und hubmethoden

Standardmäßig können alle Methoden in einem Hub von einem nicht authentifizierten Benutzer aufgerufen werden. Um eine Authentifizierung anzufordern, wenden Sie das [Autorisierungs](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut auf den Hub an:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

Sie können die Konstruktorargumente und -eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die bestimmten [Autorisierungsrichtlinien](xref:security/authorization/policies) entsprechen. Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie namens verfügen, `MyAuthorizationPolicy` können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Hub zugreifen können:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Einzelne hubmethoden können auch das- `[Authorize]` Attribut anwenden. Wenn der aktuelle Benutzer nicht mit der auf die Methode angewendeten Richtlinie identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Verwenden von Autorisierungs Handlern zum Anpassen der Hub-Methoden Autorisierung

SignalR bietet Autorisierungs Handlern eine benutzerdefinierte Ressource, wenn eine Hub-Methode eine Autorisierung erfordert. Die Ressource ist eine Instanz von `HubInvocationContext`. Der `HubInvocationContext` enthält `HubCallerContext` , den Namen der aufgerufenen Hub-Methode und die Argumente für die Hub-Methode.

Sehen Sie sich das Beispiel für einen Chatraum an, der die Anmeldung mehrerer Organisationen über Azure Active Directory ermöglicht. Jeder Benutzer mit einem Microsoft-Konto kann sich anmelden, um sich anzumelden, aber nur Mitglieder der besitzenden Organisation sollten in der Lage sein, Benutzer zu sperren oder die Chat Verläufe der Benutzer anzuzeigen. Außerdem möchten wir möglicherweise bestimmte Funktionen von bestimmten Benutzern einschränken. Wenn Sie die aktualisierten Features in ASP.net Core 3,0 verwenden, ist dies durchaus möglich. Beachten Sie, dass der `DomainRestrictedRequirement` als Benutzer definiert fungiert `IAuthorizationRequirement` . Nachdem der `HubInvocationContext` Ressourcen Parameter übergeben wurde, kann die interne Logik den Kontext untersuchen, in dem der Hub aufgerufen wird, und Entscheidungen treffen, die es dem Benutzer ermöglichen, individuelle hubmethoden auszuführen.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

`Startup.ConfigureServices`Fügen Sie in die neue Richtlinie hinzu, und stellen Sie die benutzerdefinierte `DomainRestrictedRequirement` Anforderung als Parameter bereit, um die `DomainRestricted` Richtlinie zu erstellen.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

Im vorherigen Beispiel `DomainRestrictedRequirement` ist die-Klasse ein und eine `IAuthorizationRequirement` eigene `AuthorizationHandler` für diese Anforderung. Es ist akzeptabel, diese beiden Komponenten in separate Klassen aufzuteilen, um Probleme zu trennen. Ein Vorteil des Beispiel Ansatzes besteht darin, dass es nicht erforderlich ist, den `AuthorizationHandler` während des Starts einzufügen, da die Anforderung und der Handler gleich sind.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bearertokenauthentifizierung in ASP.net Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Ressourcenbasierte Autorisierung](xref:security/authorization/resourcebased)
