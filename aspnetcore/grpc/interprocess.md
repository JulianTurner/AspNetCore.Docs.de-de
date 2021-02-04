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
ms.openlocfilehash: 8c0f8fb1468e61d5aa2e7f42cb5da33c01819124
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217465"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="97d0d-103">Inter-Process Communication mit gRPC</span><span class="sxs-lookup"><span data-stu-id="97d0d-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="97d0d-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="97d0d-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="97d0d-105">gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet.</span><span class="sxs-lookup"><span data-stu-id="97d0d-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="97d0d-106">TCP wurde für die Kommunikation über ein Netzwerk entwickelt.</span><span class="sxs-lookup"><span data-stu-id="97d0d-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="97d0d-107">[Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist noch effizienter als TCP, wenn sich Client und Dienst auf demselben Computer befinden.</span><span class="sxs-lookup"><span data-stu-id="97d0d-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="97d0d-108">In diesem Artikel wird erläutert, wie gRPC mit benutzerdefinierten Datentransporten in IPC-Szenarios verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="97d0d-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="97d0d-109">Serverkonfiguration</span><span class="sxs-lookup"><span data-stu-id="97d0d-109">Server configuration</span></span>

<span data-ttu-id="97d0d-110">Benutzerdefinierte Datentransporte werden von [Kestrel](xref:fundamentals/servers/kestrel) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="97d0d-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="97d0d-111">Kestrel wird in der Datei *Program.cs* konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="97d0d-111">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="97d0d-112">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="97d0d-112">The preceding example:</span></span>

* <span data-ttu-id="97d0d-113">Der Endpunkt von Kestrel wird in `ConfigureKestrel` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="97d0d-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="97d0d-114"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> wird aufgerufen, um mit dem angegebenen Pfad am [Unix Domain Socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) zu lauschen.</span><span class="sxs-lookup"><span data-stu-id="97d0d-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="97d0d-115">Kestrel weist integrierte Unterstützung für UDS-Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="97d0d-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="97d0d-116">UDS wird für Linux, macOS und [aktuelle Windows-Versionen](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="97d0d-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="97d0d-117">Clientkonfiguration</span><span class="sxs-lookup"><span data-stu-id="97d0d-117">Client configuration</span></span>

<span data-ttu-id="97d0d-118">`GrpcChannel` unterstützt gRPC-Aufrufe für benutzerdefinierte Datentransporte.</span><span class="sxs-lookup"><span data-stu-id="97d0d-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="97d0d-119">Wenn ein Kanal erstellt wird, kann er mit einer `SocketsHttpHandler`-Klasse konfiguriert werden, die über ein benutzerdefiniertes `ConnectCallback`-Objekt verfügt.</span><span class="sxs-lookup"><span data-stu-id="97d0d-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="97d0d-120">Der Rückruf ermöglicht es dem Client, Verbindungen für benutzerdefinierte Datentransporte herzustellen und dann HTTP-Anforderungen über diese Datentransporte zu senden.</span><span class="sxs-lookup"><span data-stu-id="97d0d-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

<span data-ttu-id="97d0d-121">Beispiel für eine UDS-Verbindungsfactory (Unix Domain Socket):</span><span class="sxs-lookup"><span data-stu-id="97d0d-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="97d0d-122">So wird die benutzerdefinierte Verbindungsfactory zum Erstellen eines Kanals verwendet:</span><span class="sxs-lookup"><span data-stu-id="97d0d-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="97d0d-123">Kanäle, die mithilfe des vorangehenden Codes erstellt werden, senden gRPC-Aufrufe über Unix Domain Sockets (UDS).</span><span class="sxs-lookup"><span data-stu-id="97d0d-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="97d0d-124">Unterstützung für andere IPC-Technologien kann mithilfe der Erweiterbarkeit in Kestrel und `SocketsHttpHandler` implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="97d0d-124">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
