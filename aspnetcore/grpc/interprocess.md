---
title: Inter-Process Communication mit gRPC
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie gRPC für Inter-Process Communication verwenden können.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
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
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93059883"
---
# <a name="inter-process-communication-with-grpc"></a>Inter-Process Communication mit gRPC

Von [James Newton-King](https://twitter.com/jamesnk)

gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet. TCP wurde für die Kommunikation über ein Netzwerk entwickelt. [Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist noch effizienter als TCP, wenn sich Client und Dienst auf demselben Computer befinden. In diesem Artikel wird erläutert, wie gRPC mit benutzerdefinierten Datentransporten in IPC-Szenarios verwendet werden kann.

## <a name="server-configuration"></a>Serverkonfiguration

Benutzerdefinierte Datentransporte werden von [Kestrel](xref:fundamentals/servers/kestrel) unterstützt. Kestrel wird in der Datei *Program.cs* konfiguriert:

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
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> wird aufgerufen, um mit dem angegebenen Pfad am [Unix Domain Socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) zu lauschen.

Kestrel weist integrierte Unterstützung für UDS-Endpunkte auf. UDS wird für Linux, macOS und [aktuelle Windows-Versionen](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) unterstützt.

## <a name="client-configuration"></a>Clientkonfiguration

`GrpcChannel` unterstützt gRPC-Aufrufe für benutzerdefinierte Datentransporte. Wenn ein Kanal erstellt wird, kann er mit einer `SocketsHttpHandler`-Klasse konfiguriert werden, die über ein benutzerdefiniertes `ConnectCallback`-Objekt verfügt. Der Rückruf ermöglicht es dem Client, Verbindungen für benutzerdefinierte Datentransporte herzustellen und dann HTTP-Anforderungen über diese Datentransporte zu senden.

> [!IMPORTANT]
> `SocketsHttpHandler.ConnectCallback` ist eine neue API des Release Candidate 2 von .NET 5.

Beispiel für eine UDS-Verbindungsfactory (Unix Domain Socket):

```csharp
public class UnixDomainSocketConnectionFactory
{
    private readonly EndPoint _endPoint;

    public UnixDomainSocketConnectionFactory(EndPoint endPoint)
    {
        _endPoint = endPoint;
    }

    public async ValueTask<Stream> ConnectAsync(SocketsHttpConnectionContext _,
        CancellationToken cancellationToken = default)
    {
        var socket = new Socket(AddressFamily.Unix, SocketType.Stream, ProtocolType.Unspecified);

        try
        {
            await socket.ConnectAsync(_endPoint, cancellationToken).ConfigureAwait(false);
            return new NetworkStream(socket, true);
        }
        catch
        {
            socket.Dispose();
            throw;
        }
    }
}
```

So wird die benutzerdefinierte Verbindungsfactory zum Erstellen eines Kanals verwendet:

```csharp
public static readonly string SocketPath = Path.Combine(Path.GetTempPath(), "socket.tmp");

public static GrpcChannel CreateChannel()
{
    var udsEndPoint = new UnixDomainSocketEndPoint(SocketPath);
    var connectionFactory = new UnixDomainSocketConnectionFactory(udsEndPoint);
    var socketsHttpHandler = new SocketsHttpHandler
    {
        ConnectCallback = connectionFactory.ConnectAsync
    };

    return GrpcChannel.ForAddress("http://localhost", new GrpcChannelOptions
    {
        HttpHandler = socketsHttpHandler
    });
}
```

Kanäle, die mithilfe des vorangehenden Codes erstellt werden, senden gRPC-Aufrufe über Unix Domain Sockets (UDS). Unterstützung für andere IPC-Technologien kann mithilfe der Erweiterbarkeit in Kestrel und `SocketsHttpHandler` implementiert werden.
