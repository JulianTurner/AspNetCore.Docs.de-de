---
title: Authentifizierung und Autorisierung in gRPC für ASP.NET Core
author: jamesnk
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in gRPC für ASP.NET Core durchführen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/authn-and-authz
ms.openlocfilehash: 833114a12c8c1ac67097b3592cf410d7a69bb628
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "94673977"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a><span data-ttu-id="d03fa-103">Authentifizierung und Autorisierung in gRPC für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d03fa-103">Authentication and authorization in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="d03fa-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="d03fa-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="d03fa-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d03fa-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="authenticate-users-calling-a-grpc-service"></a><span data-ttu-id="d03fa-106">Authentifizieren von Benutzern, die einen gRPC-Dienst aufrufen</span><span class="sxs-lookup"><span data-stu-id="d03fa-106">Authenticate users calling a gRPC service</span></span>

<span data-ttu-id="d03fa-107">gRPC kann mit der [ASP.NET Core-Authentifizierung](xref:security/authentication/identity) verwendet werden, um einen Benutzer mit jedem Aufruf zu verknüpfen.</span><span class="sxs-lookup"><span data-stu-id="d03fa-107">gRPC can be used with [ASP.NET Core authentication](xref:security/authentication/identity) to associate a user with each call.</span></span>

<span data-ttu-id="d03fa-108">Nachfolgend finden Sie ein Beispiel für `Startup.Configure`, das gRPC- und ASP.NET Core-Authentifizierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="d03fa-108">The following is an example of `Startup.Configure` which uses gRPC and ASP.NET Core authentication:</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();
    
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGrpcService<GreeterService>();
    });
}
```

> [!NOTE]
> <span data-ttu-id="d03fa-109">Die Reihenfolge, in der Sie die ASP.NET Core-Middleware für die Authentifizierung registrieren, ist wichtig.</span><span class="sxs-lookup"><span data-stu-id="d03fa-109">The order in which you register the ASP.NET Core authentication middleware matters.</span></span> <span data-ttu-id="d03fa-110">Rufen Sie `UseAuthentication` und `UseAuthorization` immer nach `UseRouting` und vor `UseEndpoints` auf.</span><span class="sxs-lookup"><span data-stu-id="d03fa-110">Always call `UseAuthentication` and `UseAuthorization` after `UseRouting` and before `UseEndpoints`.</span></span>

<span data-ttu-id="d03fa-111">Der Authentifizierungsmechanismus, den Ihre App während eines Aufrufs verwendet, muss konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-111">The authentication mechanism your app uses during a call needs to be configured.</span></span> <span data-ttu-id="d03fa-112">Die Authentifizierungskonfiguration wird in `Startup.ConfigureServices` hinzugefügt und wird je nach dem von Ihrer App verwendeten Authentifizierungsmechanismus unterschiedlich sein.</span><span class="sxs-lookup"><span data-stu-id="d03fa-112">Authentication configuration is added in `Startup.ConfigureServices` and will be different depending upon the authentication mechanism your app uses.</span></span> <span data-ttu-id="d03fa-113">Beispiele für die Sicherung von ASP.NET Core-Apps finden Sie unter [Authentifizierungsbeispiele](xref:security/authentication/samples).</span><span class="sxs-lookup"><span data-stu-id="d03fa-113">For examples of how to secure ASP.NET Core apps, see [Authentication samples](xref:security/authentication/samples).</span></span>

<span data-ttu-id="d03fa-114">Nachdem die Authentifizierung eingerichtet wurde, kann auf den Benutzer in einer Methode des gRPC-Diensts über `ServerCallContext` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-114">Once authentication has been setup, the user can be accessed in a gRPC service methods via the `ServerCallContext`.</span></span>

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a><span data-ttu-id="d03fa-115">Bearertokenauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="d03fa-115">Bearer token authentication</span></span>

<span data-ttu-id="d03fa-116">Der Client kann ein Zugriffstoken zur Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="d03fa-116">The client can provide an access token for authentication.</span></span> <span data-ttu-id="d03fa-117">Der Server überprüft das Token und verwendet es zum Identifizieren des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="d03fa-117">The server validates the token and uses it to identify the user.</span></span>

<span data-ttu-id="d03fa-118">Auf dem Server wird die Bearertokenauthentifizierung unter Verwendung der [JWT Bearer-Middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="d03fa-118">On the server, bearer token authentication is configured using the [JWT Bearer middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer).</span></span>

<span data-ttu-id="d03fa-119">Im gRPC-Client von .NET kann das Token unter Verwendung der `Metadata`-Sammlung mit Aufrufen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-119">In the .NET gRPC client, the token can be sent with calls by using the `Metadata` collection.</span></span> <span data-ttu-id="d03fa-120">Einträge in der `Metadata`-Sammlung werden mit einem gRPC-Aufruf als HTTP-Header gesendet:</span><span class="sxs-lookup"><span data-stu-id="d03fa-120">Entries in the `Metadata` collection are sent with a gRPC call as HTTP headers:</span></span>

```csharp
public bool DoAuthenticatedCall(
    Ticketer.TicketerClient client, string token)
{
    var headers = new Metadata();
    headers.Add("Authorization", $"Bearer {token}");

    var request = new BuyTicketsRequest { Count = 1 };
    var response = await client.BuyTicketsAsync(request, headers);

    return response.Success;
}
```

<span data-ttu-id="d03fa-121">Das Konfigurieren von `ChannelCredentials` für einen Kanal ist eine alternative Möglichkeit, das Token mit gRPC-Aufrufen an den Dienst zu senden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-121">Configuring `ChannelCredentials` on a channel is an alternative way to send the token to the service with gRPC calls.</span></span> <span data-ttu-id="d03fa-122">`ChannelCredentials` kann `CallCredentials` enthalten, die eine Möglichkeit zum automatischen Festlegen von `Metadata` bieten.</span><span class="sxs-lookup"><span data-stu-id="d03fa-122">A `ChannelCredentials` can include `CallCredentials`, which provide a way to automatically set `Metadata`.</span></span>

<span data-ttu-id="d03fa-123">`CallCredentials` wird bei jedem gRPC-Aufruf ausgeführt, wodurch vermieden wird, dass an mehreren Stellen Code geschrieben werden muss, um das Token selbst zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="d03fa-123">`CallCredentials` is run each time a gRPC call is made, which avoids the need to write code in multiple places to pass the token yourself.</span></span> <span data-ttu-id="d03fa-124">Beachten Sie, dass `CallCredentials` nur angewendet werden, wenn der Kanal mit TLS gesichert ist.</span><span class="sxs-lookup"><span data-stu-id="d03fa-124">Note that `CallCredentials` are only applied if the channel is secured with TLS.</span></span> <span data-ttu-id="d03fa-125">`CallCredentials` werden nicht auf unsichere Kanäle ohne TLS angewendet.</span><span class="sxs-lookup"><span data-stu-id="d03fa-125">`CallCredentials` aren't applied on unsecured non-TLS channels.</span></span>

<span data-ttu-id="d03fa-126">Die Anmeldeinformationen im folgenden Beispiel konfigurieren den Kanal derart, dass das Token bei jedem gRPC-Aufruf gesendet wird:</span><span class="sxs-lookup"><span data-stu-id="d03fa-126">The credential in the following example configures the channel to send the token with every gRPC call:</span></span>

```csharp
private static GrpcChannel CreateAuthenticatedChannel(string address)
{
    var credentials = CallCredentials.FromInterceptor((context, metadata) =>
    {
        if (!string.IsNullOrEmpty(_token))
        {
            metadata.Add("Authorization", $"Bearer {_token}");
        }
        return Task.CompletedTask;
    });

    // SslCredentials is used here because this channel is using TLS.
    // CallCredentials can't be used with ChannelCredentials.Insecure on non-TLS channels.
    var channel = GrpcChannel.ForAddress(address, new GrpcChannelOptions
    {
        Credentials = ChannelCredentials.Create(new SslCredentials(), credentials)
    });
    return channel;
}
```

### <a name="client-certificate-authentication"></a><span data-ttu-id="d03fa-127">Clientzertifikatsauthentifizierung</span><span class="sxs-lookup"><span data-stu-id="d03fa-127">Client certificate authentication</span></span>

<span data-ttu-id="d03fa-128">Ein Client könnte alternativ ein Clientzertifikat zur Authentifizierung bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="d03fa-128">A client could alternatively provide a client certificate for authentication.</span></span> <span data-ttu-id="d03fa-129">Die [Zertifikatsauthentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf der TLS-Ebene, lange bevor sie überhaupt zum ASP.NET Core gelangt.</span><span class="sxs-lookup"><span data-stu-id="d03fa-129">[Certificate authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="d03fa-130">Wenn die Anforderung in ASP.NET Core eingeht, können Sie mit dem [Clientzertifikat-Authentifizierungspaket](xref:security/authentication/certauth) das Zertifikat in ein `ClaimsPrincipal` auflösen.</span><span class="sxs-lookup"><span data-stu-id="d03fa-130">When the request enters ASP.NET Core, the [client certificate authentication package](xref:security/authentication/certauth) allows you to resolve the certificate to a `ClaimsPrincipal`.</span></span>

> [!NOTE]
> <span data-ttu-id="d03fa-131">Der Server muss so konfiguriert werden, dass er Clientzertifikate akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="d03fa-131">Configure the server to accept client certificates.</span></span> <span data-ttu-id="d03fa-132">Informationen zum Akzeptieren von Clientzertifikaten in Kestrel, IIS und Azure finden Sie unter <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span><span class="sxs-lookup"><span data-stu-id="d03fa-132">For information on accepting client certificates in Kestrel, IIS, and Azure, see <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.</span></span>

<span data-ttu-id="d03fa-133">Im .NET gRPC-Client wird das Clientzertifikat zu `HttpClientHandler` hinzugefügt, das dann zur Erstellung des gRPC-Clients verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="d03fa-133">In the .NET gRPC client, the client certificate is added to `HttpClientHandler` that is then used to create the gRPC client:</span></span>

```csharp
public Ticketer.TicketerClient CreateClientWithCert(
    string baseAddress,
    X509Certificate2 certificate)
{
    // Add client cert to the handler
    var handler = new HttpClientHandler();
    handler.ClientCertificates.Add(certificate);

    // Create the gRPC channel
    var channel = GrpcChannel.ForAddress(baseAddress, new GrpcChannelOptions
    {
        HttpHandler = handler
    });

    return new Ticketer.TicketerClient(channel);
}
```

### <a name="other-authentication-mechanisms"></a><span data-ttu-id="d03fa-134">Andere Authentifizierungsmechanismen</span><span class="sxs-lookup"><span data-stu-id="d03fa-134">Other authentication mechanisms</span></span>

<span data-ttu-id="d03fa-135">Viele von ASP.NET Core unterstützte Authentifizierungsmechanismen arbeiten mit gRPC:</span><span class="sxs-lookup"><span data-stu-id="d03fa-135">Many ASP.NET Core supported authentication mechanisms work with gRPC:</span></span>

* <span data-ttu-id="d03fa-136">Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="d03fa-136">Azure Active Directory</span></span>
* <span data-ttu-id="d03fa-137">Clientzertifikat</span><span class="sxs-lookup"><span data-stu-id="d03fa-137">Client Certificate</span></span>
* <span data-ttu-id="d03fa-138">IdentityServer</span><span class="sxs-lookup"><span data-stu-id="d03fa-138">IdentityServer</span></span>
* <span data-ttu-id="d03fa-139">JWT-Token</span><span class="sxs-lookup"><span data-stu-id="d03fa-139">JWT Token</span></span>
* <span data-ttu-id="d03fa-140">OAuth 2.0</span><span class="sxs-lookup"><span data-stu-id="d03fa-140">OAuth 2.0</span></span>
* <span data-ttu-id="d03fa-141">OpenID Connect</span><span class="sxs-lookup"><span data-stu-id="d03fa-141">OpenID Connect</span></span>
* <span data-ttu-id="d03fa-142">WS-Federation</span><span class="sxs-lookup"><span data-stu-id="d03fa-142">WS-Federation</span></span>

<span data-ttu-id="d03fa-143">Weitere Informationen zum Konfigurieren der Authentifizierung auf dem Server finden Sie unter [ASP.NET Core-Authentifizierung](xref:security/authentication/identity).</span><span class="sxs-lookup"><span data-stu-id="d03fa-143">For more information on configuring authentication on the server, see [ASP.NET Core authentication](xref:security/authentication/identity).</span></span>

<span data-ttu-id="d03fa-144">Die Konfiguration des gRPC-Clients für die Verwendung der Authentifizierung hängt von dem von Ihnen verwendeten Authentifizierungsmechanismus ab.</span><span class="sxs-lookup"><span data-stu-id="d03fa-144">Configuring the gRPC client to use authentication will depend on the authentication mechanism you are using.</span></span> <span data-ttu-id="d03fa-145">Die vorherigen Beispiele für Bearertoken und Clientzertifikate zeigen eine Reihe von Möglichkeiten, wie der gRPC-Client so konfiguriert werden kann, dass er Authentifizierungsmetadaten mit gRPC-Aufrufen sendet:</span><span class="sxs-lookup"><span data-stu-id="d03fa-145">The previous bearer token and client certificate examples show a couple of ways the gRPC client can be configured to send authentication metadata with gRPC calls:</span></span>

* <span data-ttu-id="d03fa-146">Stark typisierte gRPC-Clients verwenden intern `HttpClient`.</span><span class="sxs-lookup"><span data-stu-id="d03fa-146">Strongly typed gRPC clients use `HttpClient` internally.</span></span> <span data-ttu-id="d03fa-147">Die Authentifizierung kann für [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) oder durch Hinzufügen von benutzerdefinierten [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler)-Instanzen zu `HttpClient` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-147">Authentication can be configured on [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler), or by adding custom [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler) instances to the `HttpClient`.</span></span>
* <span data-ttu-id="d03fa-148">Jeder gRPC-Aufruf hat ein optionales `CallOptions`-Argument.</span><span class="sxs-lookup"><span data-stu-id="d03fa-148">Each gRPC call has an optional `CallOptions` argument.</span></span> <span data-ttu-id="d03fa-149">Benutzerdefinierte Header können über die Headersammlung der Option gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-149">Custom headers can be sent using the option's headers collection.</span></span>

> [!NOTE]
> <span data-ttu-id="d03fa-150">Die Windows-Authentifizierung (NTLM/Kerberos/Negotiate) kann nicht mit gRPC verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-150">Windows Authentication (NTLM/Kerberos/Negotiate) can't be used with gRPC.</span></span> <span data-ttu-id="d03fa-151">gRPC erfordert HTTP/2, und HTTP/2 unterstützt keine Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="d03fa-151">gRPC requires HTTP/2, and HTTP/2 doesn't support Windows Authentication.</span></span>

## <a name="authorize-users-to-access-services-and-service-methods"></a><span data-ttu-id="d03fa-152">Autorisieren von Benutzern zum Zugriff auf Dienste und Dienstmethoden</span><span class="sxs-lookup"><span data-stu-id="d03fa-152">Authorize users to access services and service methods</span></span>

<span data-ttu-id="d03fa-153">Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-153">By default, all methods in a service can be called by unauthenticated users.</span></span> <span data-ttu-id="d03fa-154">Wenden Sie das Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) auf den Dienst an, um eine Authentifizierung zu erfordern:</span><span class="sxs-lookup"><span data-stu-id="d03fa-154">To require authentication, apply the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute to the service:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="d03fa-155">Sie können die Konstruktorargumente und -eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die bestimmten [Autorisierungsrichtlinien](xref:security/authorization/policies) entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d03fa-155">You can use the constructor arguments and properties of the `[Authorize]` attribute to restrict access to only users matching specific [authorization policies](xref:security/authorization/policies).</span></span> <span data-ttu-id="d03fa-156">Wenn Sie z. B. eine benutzerdefinierte Autorisierungsrichtlinie namens `MyAuthorizationPolicy` besitzen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, mit dem folgenden Code auf den Dienst zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="d03fa-156">For example, if you have a custom authorization policy called `MyAuthorizationPolicy`, ensure that only users matching that policy can access the service using the following code:</span></span>

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

<span data-ttu-id="d03fa-157">Auf einzelne Dienstmethoden kann auch das Attribut `[Authorize]` angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="d03fa-157">Individual service methods can have the `[Authorize]` attribute applied as well.</span></span> <span data-ttu-id="d03fa-158">Wenn der aktuelle Benutzer nicht mit den Richtlinien übereinstimmt, die **sowohl** auf die Methode als auch auf die Klasse angewendet werden, wird ein Fehler an den Aufrufer zurückgegeben:</span><span class="sxs-lookup"><span data-stu-id="d03fa-158">If the current user doesn't match the policies applied to **both** the method and the class, an error is returned to the caller:</span></span>

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
    public override Task<AvailableTicketsResponse> GetAvailableTickets(
        Empty request, ServerCallContext context)
    {
        // ... buy tickets for the current user ...
    }

    [Authorize("Administrators")]
    public override Task<BuyTicketsResponse> RefundTickets(
        BuyTicketsRequest request, ServerCallContext context)
    {
        // ... refund tickets (something only Administrators can do) ..
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="d03fa-159">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d03fa-159">Additional resources</span></span>

* [<span data-ttu-id="d03fa-160">Bearertokenauthentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d03fa-160">Bearer Token authentication in ASP.NET Core</span></span>](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [<span data-ttu-id="d03fa-161">Konfigurieren der Clientzertifikatsauthentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d03fa-161">Configure Client Certificate authentication in ASP.NET Core</span></span>](xref:security/authentication/certauth)
