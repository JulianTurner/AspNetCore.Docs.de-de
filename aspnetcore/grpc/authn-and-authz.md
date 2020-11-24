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
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94673977"
---
# <a name="authentication-and-authorization-in-grpc-for-aspnet-core"></a>Authentifizierung und Autorisierung in gRPC für ASP.NET Core

Von [James Newton-King](https://twitter.com/jamesnk)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/grpc/authn-and-authz/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-calling-a-grpc-service"></a>Authentifizieren von Benutzern, die einen gRPC-Dienst aufrufen

gRPC kann mit der [ASP.NET Core-Authentifizierung](xref:security/authentication/identity) verwendet werden, um einen Benutzer mit jedem Aufruf zu verknüpfen.

Nachfolgend finden Sie ein Beispiel für `Startup.Configure`, das gRPC- und ASP.NET Core-Authentifizierung verwendet:

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
> Die Reihenfolge, in der Sie die ASP.NET Core-Middleware für die Authentifizierung registrieren, ist wichtig. Rufen Sie `UseAuthentication` und `UseAuthorization` immer nach `UseRouting` und vor `UseEndpoints` auf.

Der Authentifizierungsmechanismus, den Ihre App während eines Aufrufs verwendet, muss konfiguriert werden. Die Authentifizierungskonfiguration wird in `Startup.ConfigureServices` hinzugefügt und wird je nach dem von Ihrer App verwendeten Authentifizierungsmechanismus unterschiedlich sein. Beispiele für die Sicherung von ASP.NET Core-Apps finden Sie unter [Authentifizierungsbeispiele](xref:security/authentication/samples).

Nachdem die Authentifizierung eingerichtet wurde, kann auf den Benutzer in einer Methode des gRPC-Diensts über `ServerCallContext` zugegriffen werden.

```csharp
public override Task<BuyTicketsResponse> BuyTickets(
    BuyTicketsRequest request, ServerCallContext context)
{
    var user = context.GetHttpContext().User;

    // ... access data from ClaimsPrincipal ...
}

```

### <a name="bearer-token-authentication"></a>Bearertokenauthentifizierung

Der Client kann ein Zugriffstoken zur Authentifizierung bereitstellen. Der Server überprüft das Token und verwendet es zum Identifizieren des Benutzers.

Auf dem Server wird die Bearertokenauthentifizierung unter Verwendung der [JWT Bearer-Middleware](/dotnet/api/microsoft.extensions.dependencyinjection.jwtbearerextensions.addjwtbearer) konfiguriert.

Im gRPC-Client von .NET kann das Token unter Verwendung der `Metadata`-Sammlung mit Aufrufen gesendet werden. Einträge in der `Metadata`-Sammlung werden mit einem gRPC-Aufruf als HTTP-Header gesendet:

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

Das Konfigurieren von `ChannelCredentials` für einen Kanal ist eine alternative Möglichkeit, das Token mit gRPC-Aufrufen an den Dienst zu senden. `ChannelCredentials` kann `CallCredentials` enthalten, die eine Möglichkeit zum automatischen Festlegen von `Metadata` bieten.

`CallCredentials` wird bei jedem gRPC-Aufruf ausgeführt, wodurch vermieden wird, dass an mehreren Stellen Code geschrieben werden muss, um das Token selbst zu übergeben. Beachten Sie, dass `CallCredentials` nur angewendet werden, wenn der Kanal mit TLS gesichert ist. `CallCredentials` werden nicht auf unsichere Kanäle ohne TLS angewendet.

Die Anmeldeinformationen im folgenden Beispiel konfigurieren den Kanal derart, dass das Token bei jedem gRPC-Aufruf gesendet wird:

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

### <a name="client-certificate-authentication"></a>Clientzertifikatsauthentifizierung

Ein Client könnte alternativ ein Clientzertifikat zur Authentifizierung bereitstellen. Die [Zertifikatsauthentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) erfolgt auf der TLS-Ebene, lange bevor sie überhaupt zum ASP.NET Core gelangt. Wenn die Anforderung in ASP.NET Core eingeht, können Sie mit dem [Clientzertifikat-Authentifizierungspaket](xref:security/authentication/certauth) das Zertifikat in ein `ClaimsPrincipal` auflösen.

> [!NOTE]
> Der Server muss so konfiguriert werden, dass er Clientzertifikate akzeptiert. Informationen zum Akzeptieren von Clientzertifikaten in Kestrel, IIS und Azure finden Sie unter <xref:security/authentication/certauth#configure-your-server-to-require-certificates>.

Im .NET gRPC-Client wird das Clientzertifikat zu `HttpClientHandler` hinzugefügt, das dann zur Erstellung des gRPC-Clients verwendet wird:

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

### <a name="other-authentication-mechanisms"></a>Andere Authentifizierungsmechanismen

Viele von ASP.NET Core unterstützte Authentifizierungsmechanismen arbeiten mit gRPC:

* Azure Active Directory
* Clientzertifikat
* IdentityServer
* JWT-Token
* OAuth 2.0
* OpenID Connect
* WS-Federation

Weitere Informationen zum Konfigurieren der Authentifizierung auf dem Server finden Sie unter [ASP.NET Core-Authentifizierung](xref:security/authentication/identity).

Die Konfiguration des gRPC-Clients für die Verwendung der Authentifizierung hängt von dem von Ihnen verwendeten Authentifizierungsmechanismus ab. Die vorherigen Beispiele für Bearertoken und Clientzertifikate zeigen eine Reihe von Möglichkeiten, wie der gRPC-Client so konfiguriert werden kann, dass er Authentifizierungsmetadaten mit gRPC-Aufrufen sendet:

* Stark typisierte gRPC-Clients verwenden intern `HttpClient`. Die Authentifizierung kann für [HttpClientHandler](/dotnet/api/system.net.http.httpclienthandler) oder durch Hinzufügen von benutzerdefinierten [HttpMessageHandler](/dotnet/api/system.net.http.httpmessagehandler)-Instanzen zu `HttpClient` konfiguriert werden.
* Jeder gRPC-Aufruf hat ein optionales `CallOptions`-Argument. Benutzerdefinierte Header können über die Headersammlung der Option gesendet werden.

> [!NOTE]
> Die Windows-Authentifizierung (NTLM/Kerberos/Negotiate) kann nicht mit gRPC verwendet werden. gRPC erfordert HTTP/2, und HTTP/2 unterstützt keine Windows-Authentifizierung.

## <a name="authorize-users-to-access-services-and-service-methods"></a>Autorisieren von Benutzern zum Zugriff auf Dienste und Dienstmethoden

Standardmäßig können alle Methoden in einem Dienst von nicht authentifizierten Benutzern aufgerufen werden. Wenden Sie das Attribut [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) auf den Dienst an, um eine Authentifizierung zu erfordern:

```csharp
[Authorize]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Sie können die Konstruktorargumente und -eigenschaften des `[Authorize]`-Attributs verwenden, um den Zugriff auf Benutzer zu beschränken, die bestimmten [Autorisierungsrichtlinien](xref:security/authorization/policies) entsprechen. Wenn Sie z. B. eine benutzerdefinierte Autorisierungsrichtlinie namens `MyAuthorizationPolicy` besitzen, stellen Sie sicher, dass nur Benutzer, die dieser Richtlinie entsprechen, mit dem folgenden Code auf den Dienst zugreifen können:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class TicketerService : Ticketer.TicketerBase
{
}
```

Auf einzelne Dienstmethoden kann auch das Attribut `[Authorize]` angewendet werden. Wenn der aktuelle Benutzer nicht mit den Richtlinien übereinstimmt, die **sowohl** auf die Methode als auch auf die Klasse angewendet werden, wird ein Fehler an den Aufrufer zurückgegeben:

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Bearertokenauthentifizierung in ASP.NET Core](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Konfigurieren der Clientzertifikatsauthentifizierung in ASP.NET Core](xref:security/authentication/certauth)
