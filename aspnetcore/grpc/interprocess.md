---
title: Inter-Process Communication mit gRPC
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie gRPC für Inter-Process Communication verwenden können.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 08/24/2020
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
uid: grpc/interprocess
ms.openlocfilehash: cfe8c98bb94817035437b2feb127a07bf5cad24b
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945487"
---
# <a name="inter-process-communication-with-grpc"></a>Inter-Process Communication mit gRPC

Von [James Newton-King](https://twitter.com/jamesnk)

gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet. TCP eignet sich ideal für die Kommunikation über ein Netzwerk, [Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist jedoch noch effizienter, wenn sich Client und Dienst auf demselben Computer befinden. In diesem Artikel wird erläutert, wie gRPC mit benutzerdefinierten Datentransporten in IPC-Szenarios verwendet werden kann.

## <a name="server-configuration"></a>Serverkonfiguration

Benutzerdefinierte Datentransporte werden von Kestrel unterstützt. Kestrel wird in der Datei *Program.cs* konfiguriert:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(options =>
            {
                if (File.Exists(SocketPath))
                {
                    File.Delete(SocketPath);
                }
                options.ListenUnixSocket(SocketPath);
            });
        });
```

Für das vorherige Beispiel gilt Folgendes:

* Der Endpunkt von Kestrel wird in `ConfigureKestrel` konfiguriert.
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> wird aufgerufen, um mit dem angegebenen Pfad am [Unix Domain Socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) zu lauschen.

Kestrel weist integrierte Unterstützung für UDS-Endpunkte auf. UDS wird für Linux, macOS und [aktuelle Windows-Versionen](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) unterstützt.

## <a name="client-configuration"></a>Clientkonfiguration

`GrpcChannel` unterstützt gRPC-Aufrufe für benutzerdefinierte Datentransporte. Wenn ein Kanal erstellt wird, kann er mit einer `SocketsHttpHandler`-Klasse konfiguriert werden, die über ein benutzerdefiniertes `ConnectionFactory`-Objekt verfügt. Die Factory ermöglicht es dem Client, Verbindungen für benutzerdefinierte Datentransporte herzustellen und dann HTTP-Anforderungen über diese Datentransporte zu senden.

> [!IMPORTANT]
> `ConnectionFactory` ist eine neue API des Release Candidate 1 von .NET 5.

Beispiel für eine UDS-Verbindungsfactory (Unix Domain Socket):

```csharp
public class UnixDomainSocketConnectionFactory : SocketsConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
        : base(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified)
    {
        _endPoint = endPoint;
    }

    public override ValueTask<Connection> ConnectAsync(EndPoint? endPoint,
        IConnectionProperties? options = null, CancellationToken cancellationToken = default)
    {
        return base.ConnectAsync(_endPoint, options, cancellationToken);
    }
}
```

So wird die benutzerdefinierte Verbindungsfactory zum Erstellen eines Kanals verwendet:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint)
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Kanäle, die mithilfe des vorangehenden Codes erstellt werden, senden gRPC-Aufrufe über Unix Domain Sockets (UDS).
