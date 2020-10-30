---
title: 'Authentifizierung und Autorisierung in ASP.net Core :::no-loc(SignalR):::'
author: bradygaster
description: 'Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core verwenden :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/authn-and-authz
ms.openlocfilehash: 0e220d72fe9ef4ada402b449ef20e31324f7bcd2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060117"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="3b68f-103">Authentifizierung und Autorisierung in ASP.net Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="3b68f-103">Authentication and authorization in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="3b68f-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="3b68f-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="3b68f-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="3b68f-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a><span data-ttu-id="3b68f-106">Authentifizieren von Benutzern, die eine Verbindung mit einem :::no-loc(SignalR)::: Hub herstellen</span><span class="sxs-lookup"><span data-stu-id="3b68f-106">Authenticate users connecting to a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="3b68f-107">:::no-loc(SignalR)::: kann mit ASP.net Core- [Authentifizierung](xref:security/authentication/identity) verwendet werden, um jedem Verbindung einen Benutzer zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-107">:::no-loc(SignalR)::: can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each connection.</span></span> <span data-ttu-id="3b68f-108">In einem Hub kann über die [hubconnectioncontext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) -Eigenschaft auf Authentifizierungsdaten zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-108">In a hub, authentication data can be accessed from the [HubConnectionContext.User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) property.</span></span> <span data-ttu-id="3b68f-109">Die Authentifizierung ermöglicht dem Hub das Abrufen von Methoden für alle Verbindungen, die einem Benutzer zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="3b68f-109">Authentication allows the hub to call methods on all connections associated with a user.</span></span> <span data-ttu-id="3b68f-110">Weitere Informationen finden Sie unter [Verwalten von Benutzern und Gruppen :::no-loc(SignalR)::: in ](xref:signalr/groups).</span><span class="sxs-lookup"><span data-stu-id="3b68f-110">For more information, see [Manage users and groups in :::no-loc(SignalR):::](xref:signalr/groups).</span></span> <span data-ttu-id="3b68f-111">Mehrere Verbindungen können einem einzelnen Benutzer zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-111">Multiple connections may be associated with a single user.</span></span>

<span data-ttu-id="3b68f-112">Im folgenden finden Sie ein Beispiel für `Startup.Configure` die Verwendung von :::no-loc(SignalR)::: und ASP.net Core-Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="3b68f-112">The following is an example of `Startup.Configure` which uses :::no-loc(SignalR)::: and ASP.NET Core authentication:</span></span>

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

    app.Use:::no-loc(SignalR):::(hubs =>
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
> <span data-ttu-id="3b68f-113">Die Reihenfolge, in der Sie die :::no-loc(SignalR)::: und ASP.net Core Authentifizierungs Middleware registriert, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="3b68f-113">The order in which you register the :::no-loc(SignalR)::: and ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="3b68f-114">Ruft immer `UseAuthentication` vor `Use:::no-loc(SignalR):::` auf, sodass :::no-loc(SignalR)::: über einen Benutzer auf dem verfügt `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="3b68f-114">Always call `UseAuthentication` before `Use:::no-loc(SignalR):::` so that :::no-loc(SignalR)::: has a user on the `HttpContext`.</span></span>

::: moniker-end

### <a name="no-loccookie-authentication"></a><span data-ttu-id="3b68f-115">Authentifizierung per :::no-loc(Cookie):::</span><span class="sxs-lookup"><span data-stu-id="3b68f-115">:::no-loc(Cookie)::: authentication</span></span>

<span data-ttu-id="3b68f-116">In einer browserbasierten APP :::no-loc(cookie)::: ermöglicht die Authentifizierung, dass Ihre vorhandenen Benutzer Anmelde Informationen automatisch an Verbindungen weitergeleitet werden :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="3b68f-116">In a browser-based app, :::no-loc(cookie)::: authentication allows your existing user credentials to automatically flow to :::no-loc(SignalR)::: connections.</span></span> <span data-ttu-id="3b68f-117">Wenn Sie den Browser Client verwenden, ist keine zusätzliche Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="3b68f-117">When using the browser client, no additional configuration is needed.</span></span> <span data-ttu-id="3b68f-118">Wenn der Benutzer bei ihrer App angemeldet ist, :::no-loc(SignalR)::: erbt die Verbindung diese Authentifizierung automatisch.</span><span class="sxs-lookup"><span data-stu-id="3b68f-118">If the user is logged in to your app, the :::no-loc(SignalR)::: connection automatically inherits this authentication.</span></span>

<span data-ttu-id="3b68f-119">:::no-loc(Cookie):::s sind eine browserspezifische Methode zum Senden von Zugriffs Token, die von nicht-Browser Clients jedoch gesendet werden können.</span><span class="sxs-lookup"><span data-stu-id="3b68f-119">:::no-loc(Cookie):::s are a browser-specific way to send access tokens, but non-browser clients can send them.</span></span> <span data-ttu-id="3b68f-120">Wenn Sie den [.NET-Client](xref:signalr/dotnet-client)verwenden, kann die- `:::no-loc(Cookie):::s` Eigenschaft im-Befehl konfiguriert werden, `.WithUrl` um einen bereitzustellen :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="3b68f-120">When using the [.NET Client](xref:signalr/dotnet-client), the `:::no-loc(Cookie):::s` property can be configured in the `.WithUrl` call to provide a :::no-loc(cookie):::.</span></span> <span data-ttu-id="3b68f-121">Allerdings :::no-loc(cookie)::: erfordert die Verwendung der-Authentifizierung über den .NET-Client, dass die APP eine API zum Austauschen von Authentifizierungsdaten für einen bereitstellt :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="3b68f-121">However, using :::no-loc(cookie)::: authentication from the .NET client requires the app to provide an API to exchange authentication data for a :::no-loc(cookie):::.</span></span>

### <a name="bearer-token-authentication"></a><span data-ttu-id="3b68f-122">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-122">Bearer token authentication</span></span>

<span data-ttu-id="3b68f-123">Der Client kann ein Zugriffs Token bereitstellen, anstatt zu verwenden :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="3b68f-123">The client can provide an access token instead of using a :::no-loc(cookie):::.</span></span> <span data-ttu-id="3b68f-124">Der Server überprüft das Token und verwendet es zum Identifizieren des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="3b68f-124">The server validates the token and uses it to identify the user.</span></span> <span data-ttu-id="3b68f-125">Diese Überprüfung erfolgt nur, wenn die Verbindung hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3b68f-125">This validation is done only when the connection is established.</span></span> <span data-ttu-id="3b68f-126">Während der Lebensdauer der Verbindung wird der Server nicht automatisch neu validiert, um die tokensperrung zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-126">During the life of the connection, the server doesn't automatically revalidate to check for token revocation.</span></span>

<span data-ttu-id="3b68f-127">Im JavaScript-Client kann das Token mithilfe der [accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) -Option bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-127">In the JavaScript client, the token can be provided using the [accessTokenFactory](xref:signalr/configuration#configure-bearer-authentication) option.</span></span>

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

<span data-ttu-id="3b68f-128">Im .NET-Client gibt es eine ähnliche [accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) -Eigenschaft, die verwendet werden kann, um das Token zu konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="3b68f-128">In the .NET client, there's a similar [AccessTokenProvider](xref:signalr/configuration#configure-bearer-authentication) property that can be used to configure the token:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="3b68f-129">Die von Ihnen bereitgestellte zugriffstokenfunktion wird vor **jeder** http-Anforderung von aufgerufen :::no-loc(SignalR)::: .</span><span class="sxs-lookup"><span data-stu-id="3b68f-129">The access token function you provide is called before **every** HTTP request made by :::no-loc(SignalR):::.</span></span> <span data-ttu-id="3b68f-130">Wenn Sie das Token erneuern müssen, um die Verbindung aktiv zu halten (da es während der Verbindung ablaufen kann), verwenden Sie dies innerhalb dieser Funktion, und geben Sie das aktualisierte Token zurück.</span><span class="sxs-lookup"><span data-stu-id="3b68f-130">If you need to renew the token in order to keep the connection active (because it may expire during the connection), do so from within this function and return the updated token.</span></span>

<span data-ttu-id="3b68f-131">In Standard-Web-APIs werden bearertoken in einem HTTP-Header gesendet.</span><span class="sxs-lookup"><span data-stu-id="3b68f-131">In standard web APIs, bearer tokens are sent in an HTTP header.</span></span> <span data-ttu-id="3b68f-132">:::no-loc(SignalR):::Kann diese Header jedoch nicht in Browsern festlegen, wenn einige Transporte verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-132">However, :::no-loc(SignalR)::: is unable to set these headers in browsers when using some transports.</span></span> <span data-ttu-id="3b68f-133">Wenn websockets und Server-Sent Ereignisse verwendet werden, wird das Token als Abfrage Zeichenfolgen-Parameter übertragen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-133">When using WebSockets and Server-Sent Events, the token is transmitted as a query string parameter.</span></span> 

#### <a name="built-in-jwt-authentication"></a><span data-ttu-id="3b68f-134">Integrierte JWT-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-134">Built-in JWT authentication</span></span>

<span data-ttu-id="3b68f-135">Auf dem Server wird die bearertokenauthentifizierung mithilfe der [JWT-bearermiddleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="3b68f-135">On the server, bearer token authentication is configured using the [JWT Bearer middleware](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A):</span></span>

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> <span data-ttu-id="3b68f-136">Die Abfrage Zeichenfolge wird in Browsern verwendet, wenn eine Verbindung mit websockets hergestellt und Ereignisse aufgrund von Einschränkungen der Browser-API Server-Sent werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-136">The query string is used on browsers when connecting with WebSockets and Server-Sent Events due to browser API limitations.</span></span> <span data-ttu-id="3b68f-137">Bei Verwendung von HTTPS werden Abfrage Zeichen folgen Werte durch die TLS-Verbindung gesichert.</span><span class="sxs-lookup"><span data-stu-id="3b68f-137">When using HTTPS, query string values are secured by the TLS connection.</span></span> <span data-ttu-id="3b68f-138">Viele Server protokollieren jedoch Abfrage Zeichen folgen Werte.</span><span class="sxs-lookup"><span data-stu-id="3b68f-138">However, many servers log query string values.</span></span> <span data-ttu-id="3b68f-139">Weitere Informationen finden Sie unter [Sicherheitsüberlegungen in :::no-loc(SignalR)::: ASP.net Core ](xref:signalr/security).</span><span class="sxs-lookup"><span data-stu-id="3b68f-139">For more information, see [Security considerations in ASP.NET Core :::no-loc(SignalR):::](xref:signalr/security).</span></span> <span data-ttu-id="3b68f-140">:::no-loc(SignalR)::: verwendet Header zum Übertragen von Token in Umgebungen, die diese unterstützen (z. b. die .net-und Java-Clients).</span><span class="sxs-lookup"><span data-stu-id="3b68f-140">:::no-loc(SignalR)::: uses headers to transmit tokens in environments which support them (such as the .NET and Java clients).</span></span>

#### <a name="no-locidentity-server-jwt-authentication"></a><span data-ttu-id="3b68f-141">:::no-loc(Identity)::: Server-JWT-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-141">:::no-loc(Identity)::: Server JWT authentication</span></span>

<span data-ttu-id="3b68f-142">Wenn Sie :::no-loc(Identity)::: Server verwenden, fügen Sie <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> dem Projekt einen Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="3b68f-142">When using :::no-loc(Identity)::: Server, add a <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> service to the project:</span></span>

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

<span data-ttu-id="3b68f-143">Registrieren Sie den Dienst in, `Startup.ConfigureServices` nachdem Sie Dienste für <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> die Authentifizierung () und den Authentifizierungs Handler für :::no-loc(Identity)::: Server () hinzugefügt haben <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A> :</span><span class="sxs-lookup"><span data-stu-id="3b68f-143">Register the service in `Startup.ConfigureServices` after adding services for authentication (<xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A>) and the authentication handler for :::no-loc(Identity)::: Server (<xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.Add:::no-loc(Identity):::ServerJwt%2A>):</span></span>

```csharp
services.AddAuthentication()
    .Add:::no-loc(Identity):::ServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a><span data-ttu-id="3b68f-144">:::no-loc(Cookie):::e im Vergleich zu Träger Token</span><span class="sxs-lookup"><span data-stu-id="3b68f-144">:::no-loc(Cookie):::s vs. bearer tokens</span></span> 

<span data-ttu-id="3b68f-145">:::no-loc(Cookie):::e sind für Browser spezifisch.</span><span class="sxs-lookup"><span data-stu-id="3b68f-145">:::no-loc(Cookie):::s are specific to browsers.</span></span> <span data-ttu-id="3b68f-146">Das Senden von anderen Arten von Clients erhöht die Komplexität im Vergleich zum Senden von bearertoken.</span><span class="sxs-lookup"><span data-stu-id="3b68f-146">Sending them from other kinds of clients adds complexity compared to sending bearer tokens.</span></span> <span data-ttu-id="3b68f-147">Daher wird :::no-loc(cookie)::: die Authentifizierung nicht empfohlen, es sei denn, die APP muss nur Benutzer über den Browser Client authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="3b68f-147">Consequently, :::no-loc(cookie)::: authentication isn't recommended unless the app only needs to authenticate users from the browser client.</span></span> <span data-ttu-id="3b68f-148">Die bearertokenauthentifizierung ist die empfohlene Vorgehensweise, wenn andere Clients als der Browser Client verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-148">Bearer token authentication is the recommended approach when using clients other than the browser client.</span></span>

### <a name="windows-authentication"></a><span data-ttu-id="3b68f-149">Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-149">Windows authentication</span></span>

<span data-ttu-id="3b68f-150">Wenn die [Windows-Authentifizierung](xref:security/authentication/windowsauth) in ihrer App konfiguriert ist, :::no-loc(SignalR)::: kann diese Identität zum Sichern von Hubs verwenden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-150">If [Windows authentication](xref:security/authentication/windowsauth) is configured in your app, :::no-loc(SignalR)::: can use that identity to secure hubs.</span></span> <span data-ttu-id="3b68f-151">Zum Senden von Nachrichten an einzelne Benutzer müssen Sie jedoch einen benutzerdefinierten Benutzer-ID-Anbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-151">However, to send messages to individual users, you need to add a custom User ID provider.</span></span> <span data-ttu-id="3b68f-152">Das Windows-Authentifizierungssystem stellt den Anspruch "namensbezeichneranspruch" nicht bereit.</span><span class="sxs-lookup"><span data-stu-id="3b68f-152">The Windows authentication system doesn't provide the "Name Identifier" claim.</span></span> <span data-ttu-id="3b68f-153">:::no-loc(SignalR)::: verwendet den-Anspruch, um den Benutzernamen zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="3b68f-153">:::no-loc(SignalR)::: uses the claim to determine the user name.</span></span>

<span data-ttu-id="3b68f-154">Fügen Sie eine neue Klasse hinzu, die implementiert `IUserIdProvider` und einen der Ansprüche des Benutzers abruft, der als Bezeichner verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="3b68f-154">Add a new class that implements `IUserIdProvider` and retrieve one of the claims from the user to use as the identifier.</span></span> <span data-ttu-id="3b68f-155">Wenn Sie z. b. den Anspruch "Name" (den Windows-Benutzernamen im Formular `[Domain]\[Username]` ) verwenden möchten, erstellen Sie die folgende Klasse:</span><span class="sxs-lookup"><span data-stu-id="3b68f-155">For example, to use the "Name" claim (which is the Windows username in the form `[Domain]\[Username]`), create the following class:</span></span>

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

<span data-ttu-id="3b68f-156">Anstelle `ClaimTypes.Name` von können Sie einen beliebigen Wert aus dem `User` (z. b. den Windows-SID-Bezeichner usw.) verwenden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-156">Rather than `ClaimTypes.Name`, you can use any value from the `User` (such as the Windows SID identifier, and so on).</span></span>

> [!NOTE]
> <span data-ttu-id="3b68f-157">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="3b68f-157">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="3b68f-158">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-158">Otherwise, a message intended for one user could end up going to a different user.</span></span>

<span data-ttu-id="3b68f-159">Registrieren Sie diese Komponente in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="3b68f-159">Register this component in your `Startup.ConfigureServices` method.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.Add:::no-loc(SignalR):::();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

<span data-ttu-id="3b68f-160">Im .NET-Client muss die Windows-Authentifizierung aktiviert werden, indem die [usedefault-Anmelde](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) Informationen-Eigenschaft festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="3b68f-160">In the .NET Client, Windows Authentication must be enabled by setting the [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) property:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

<span data-ttu-id="3b68f-161">Die Windows-Authentifizierung wird in Internet Explorer und Microsoft Edge unterstützt, aber nicht in allen Browsern.</span><span class="sxs-lookup"><span data-stu-id="3b68f-161">Windows authentication is supported in Internet Explorer and Microsoft Edge, but not in all browsers.</span></span> <span data-ttu-id="3b68f-162">Beispielsweise kann in Chrome und Safari der Versuch, Windows-Authentifizierung und websockets zu verwenden, nicht ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-162">For example, in Chrome and Safari, attempting to use Windows authentication and WebSockets fails.</span></span> <span data-ttu-id="3b68f-163">Wenn die Windows-Authentifizierung fehlschlägt, versucht der Client, auf andere Transporte zurückzugreifen, die möglicherweise funktionieren.</span><span class="sxs-lookup"><span data-stu-id="3b68f-163">When Windows authentication fails, the client attempts to fall back to other transports which might work.</span></span>

### <a name="use-claims-to-customize-identity-handling"></a><span data-ttu-id="3b68f-164">Verwenden von Ansprüchen zum Anpassen der Identitäts Behandlung</span><span class="sxs-lookup"><span data-stu-id="3b68f-164">Use claims to customize identity handling</span></span>

<span data-ttu-id="3b68f-165">Eine APP, die Benutzer authentifiziert, kann :::no-loc(SignalR)::: Benutzer-IDs von Benutzer Ansprüchen ableiten.</span><span class="sxs-lookup"><span data-stu-id="3b68f-165">An app that authenticates users can derive :::no-loc(SignalR)::: user IDs from user claims.</span></span> <span data-ttu-id="3b68f-166">Um anzugeben :::no-loc(SignalR)::: , wie Benutzer-IDs von erstellt werden, implementieren `IUserIdProvider` und registrieren Sie die Implementierung.</span><span class="sxs-lookup"><span data-stu-id="3b68f-166">To specify how :::no-loc(SignalR)::: creates user IDs, implement `IUserIdProvider` and register the implementation.</span></span>

<span data-ttu-id="3b68f-167">Der Beispielcode veranschaulicht, wie Sie Ansprüche verwenden, um die e-Mail-Adresse des Benutzers als identifizierende Eigenschaft auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-167">The sample code demonstrates how you would use claims to select the user's email address as the identifying property.</span></span> 

> [!NOTE]
> <span data-ttu-id="3b68f-168">Der von Ihnen gewählte Wert muss für alle Benutzer in Ihrem System eindeutig sein.</span><span class="sxs-lookup"><span data-stu-id="3b68f-168">The value you choose must be unique among all the users in your system.</span></span> <span data-ttu-id="3b68f-169">Andernfalls könnte eine Nachricht, die für einen Benutzer vorgesehen ist, an einen anderen Benutzer weiter gehen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-169">Otherwise, a message intended for one user could end up going to a different user.</span></span>

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

<span data-ttu-id="3b68f-170">Die Kontoregistrierung fügt der ASP.net-Identitätsdatenbank einen Anspruch mit dem Typ hinzu `ClaimsTypes.Email` .</span><span class="sxs-lookup"><span data-stu-id="3b68f-170">The account registration adds a claim with type `ClaimsTypes.Email` to the ASP.NET identity database.</span></span>

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

<span data-ttu-id="3b68f-171">Registrieren Sie diese Komponente in Ihrer `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3b68f-171">Register this component in your `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a><span data-ttu-id="3b68f-172">Autorisieren von Benutzern für den Zugriff auf Hubs und hubmethoden</span><span class="sxs-lookup"><span data-stu-id="3b68f-172">Authorize users to access hubs and hub methods</span></span>

<span data-ttu-id="3b68f-173">Standardmäßig können alle Methoden in einem Hub von einem nicht authentifizierten Benutzer aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-173">By default, all methods in a hub can be called by an unauthenticated user.</span></span> <span data-ttu-id="3b68f-174">Um eine Authentifizierung anzufordern, wenden Sie das [Autorisierungs](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Attribut auf den Hub an:</span><span class="sxs-lookup"><span data-stu-id="3b68f-174">To require authentication, apply the [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) attribute to the hub:</span></span>

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

<span data-ttu-id="3b68f-175">Sie können die Konstruktorargumente und -eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die bestimmten [Autorisierungsrichtlinien](xref:security/authorization/policies) entsprechen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-175">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="3b68f-176">Wenn Sie beispielsweise über eine benutzerdefinierte Autorisierungs Richtlinie namens verfügen, `MyAuthorizationPolicy` können Sie sicherstellen, dass nur Benutzer, die dieser Richtlinie entsprechen, mithilfe des folgenden Codes auf den Hub zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="3b68f-176">For example, if you have a custom authorization policy called `MyAuthorizationPolicy` you can ensure that only users matching that policy can access the hub using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

<span data-ttu-id="3b68f-177">Einzelne hubmethoden können auch das- `[Authorize]` Attribut anwenden.</span><span class="sxs-lookup"><span data-stu-id="3b68f-177">Individual hub methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="3b68f-178">Wenn der aktuelle Benutzer nicht mit der auf die Methode angewendeten Richtlinie identisch ist, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="3b68f-178">If the current user doesn't match the policy applied to the method, an error is returned to the caller:</span></span>

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

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a><span data-ttu-id="3b68f-179">Verwenden von Autorisierungs Handlern zum Anpassen der Hub-Methoden Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-179">Use authorization handlers to customize hub method authorization</span></span>

<span data-ttu-id="3b68f-180">:::no-loc(SignalR)::: bietet Autorisierungs Handlern eine benutzerdefinierte Ressource, wenn eine Hub-Methode eine Autorisierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="3b68f-180">:::no-loc(SignalR)::: provides a custom resource to authorization handlers when a hub method requires authorization.</span></span> <span data-ttu-id="3b68f-181">Die Ressource ist eine Instanz von `HubInvocationContext`.</span><span class="sxs-lookup"><span data-stu-id="3b68f-181">The resource is an instance of `HubInvocationContext`.</span></span> <span data-ttu-id="3b68f-182">Der `HubInvocationContext` enthält `HubCallerContext` , den Namen der aufgerufenen Hub-Methode und die Argumente für die Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="3b68f-182">The `HubInvocationContext` includes the `HubCallerContext`, the name of the hub method being invoked, and the arguments to the hub method.</span></span>

<span data-ttu-id="3b68f-183">Sehen Sie sich das Beispiel für einen Chatraum an, der die Anmeldung mehrerer Organisationen über Azure Active Directory ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="3b68f-183">Consider the example of a chat room allowing multiple organization sign-in via Azure Active Directory.</span></span> <span data-ttu-id="3b68f-184">Jeder Benutzer mit einem Microsoft-Konto kann sich anmelden, um sich anzumelden, aber nur Mitglieder der besitzenden Organisation sollten in der Lage sein, Benutzer zu sperren oder die Chat Verläufe der Benutzer anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-184">Anyone with a Microsoft account can sign in to chat, but only members of the owning organization should be able to ban users or view users' chat histories.</span></span> <span data-ttu-id="3b68f-185">Außerdem möchten wir möglicherweise bestimmte Funktionen von bestimmten Benutzern einschränken.</span><span class="sxs-lookup"><span data-stu-id="3b68f-185">Furthermore, we might want to restrict certain functionality from certain users.</span></span> <span data-ttu-id="3b68f-186">Wenn Sie die aktualisierten Features in ASP.net Core 3,0 verwenden, ist dies durchaus möglich.</span><span class="sxs-lookup"><span data-stu-id="3b68f-186">Using the updated features in ASP.NET Core 3.0, this is entirely possible.</span></span> <span data-ttu-id="3b68f-187">Beachten Sie, dass der `DomainRestrictedRequirement` als Benutzer definiert fungiert `IAuthorizationRequirement` .</span><span class="sxs-lookup"><span data-stu-id="3b68f-187">Note how the `DomainRestrictedRequirement` serves as a custom `IAuthorizationRequirement`.</span></span> <span data-ttu-id="3b68f-188">Nachdem der `HubInvocationContext` Ressourcen Parameter übergeben wurde, kann die interne Logik den Kontext untersuchen, in dem der Hub aufgerufen wird, und Entscheidungen treffen, die es dem Benutzer ermöglichen, individuelle hubmethoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-188">Now that the `HubInvocationContext` resource parameter is being passed in, the internal logic can inspect the context in which the Hub is being called and make decisions on allowing the user to execute individual Hub methods.</span></span>

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
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.:::no-loc(Identity):::.Name) && 
            context.User.:::no-loc(Identity):::.Name.EndsWith("@microsoft.com"))
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

<span data-ttu-id="3b68f-189">`Startup.ConfigureServices`Fügen Sie in die neue Richtlinie hinzu, und stellen Sie die benutzerdefinierte `DomainRestrictedRequirement` Anforderung als Parameter bereit, um die `DomainRestricted` Richtlinie zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-189">In `Startup.ConfigureServices`, add the new policy, providing the custom `DomainRestrictedRequirement` requirement as a parameter to create the `DomainRestricted` policy.</span></span>

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

<span data-ttu-id="3b68f-190">Im vorherigen Beispiel `DomainRestrictedRequirement` ist die-Klasse ein und eine `IAuthorizationRequirement` eigene `AuthorizationHandler` für diese Anforderung.</span><span class="sxs-lookup"><span data-stu-id="3b68f-190">In the preceding example, the `DomainRestrictedRequirement` class is both an `IAuthorizationRequirement` and its own `AuthorizationHandler` for that requirement.</span></span> <span data-ttu-id="3b68f-191">Es ist akzeptabel, diese beiden Komponenten in separate Klassen aufzuteilen, um Probleme zu trennen.</span><span class="sxs-lookup"><span data-stu-id="3b68f-191">It's acceptable to split these two components into separate classes to separate concerns.</span></span> <span data-ttu-id="3b68f-192">Ein Vorteil des Beispiel Ansatzes besteht darin, dass es nicht erforderlich ist, den `AuthorizationHandler` während des Starts einzufügen, da die Anforderung und der Handler gleich sind.</span><span class="sxs-lookup"><span data-stu-id="3b68f-192">A benefit of the example's approach is there's no need to inject the `AuthorizationHandler` during startup, as the requirement and the handler are the same thing.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3b68f-193">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3b68f-193">Additional resources</span></span>

* [<span data-ttu-id="3b68f-194">Bearertokenauthentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="3b68f-194">Bearer Token Authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="3b68f-195">Ressourcenbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="3b68f-195">Resource-based Authorization</span></span>](xref:security/authorization/resourcebased)
