---
title: Inter-Process Communication mit gRPC
author: jamesnk
description: In diesem Artikel erfahren Sie, wie Sie gRPC für Inter-Process Communication verwenden können.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.date: 09/16/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/interprocess
ms.openlocfilehash: d806a340d8540fce8af6ccc6ff68325e4b733922
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059883"
---
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="a0e5e-103">Inter-Process Communication mit gRPC</span><span class="sxs-lookup"><span data-stu-id="a0e5e-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="a0e5e-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="a0e5e-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="a0e5e-105">gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="a0e5e-106">TCP wurde für die Kommunikation über ein Netzwerk entwickelt.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-106">TCP was designed for communicating across a network.</span></span> <span data-ttu-id="a0e5e-107">[Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist noch effizienter als TCP, wenn sich Client und Dienst auf demselben Computer befinden.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-107">[Inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient than TCP when the client and service are on the same machine.</span></span> <span data-ttu-id="a0e5e-108">In diesem Artikel wird erläutert, wie gRPC mit benutzerdefinierten Datentransporten in IPC-Szenarios verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-108">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="a0e5e-109">Serverkonfiguration</span><span class="sxs-lookup"><span data-stu-id="a0e5e-109">Server configuration</span></span>

<span data-ttu-id="a0e5e-110">Benutzerdefinierte Datentransporte werden von [Kestrel](xref:fundamentals/servers/kestrel) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-110">Custom transports are supported by [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="a0e5e-111">Kestrel wird in der Datei *Program.cs* konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="a0e5e-111">Kestrel is configured in *Program.cs* :</span></span>

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

<span data-ttu-id="a0e5e-112">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a0e5e-112">The preceding example:</span></span>

* <span data-ttu-id="a0e5e-113">Der Endpunkt von Kestrel wird in `ConfigureKestrel` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-113">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="a0e5e-114"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> wird aufgerufen, um mit dem angegebenen Pfad am [Unix Domain Socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) zu lauschen.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-114">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="a0e5e-115">Kestrel weist integrierte Unterstützung für UDS-Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-115">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="a0e5e-116">UDS wird für Linux, macOS und [aktuelle Windows-Versionen](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-116">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="a0e5e-117">Clientkonfiguration</span><span class="sxs-lookup"><span data-stu-id="a0e5e-117">Client configuration</span></span>

<span data-ttu-id="a0e5e-118">`GrpcChannel` unterstützt gRPC-Aufrufe für benutzerdefinierte Datentransporte.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-118">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="a0e5e-119">Wenn ein Kanal erstellt wird, kann er mit einer `SocketsHttpHandler`-Klasse konfiguriert werden, die über ein benutzerdefiniertes `ConnectCallback`-Objekt verfügt.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-119">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectCallback`.</span></span> <span data-ttu-id="a0e5e-120">Der Rückruf ermöglicht es dem Client, Verbindungen für benutzerdefinierte Datentransporte herzustellen und dann HTTP-Anforderungen über diese Datentransporte zu senden.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-120">The callback allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a0e5e-121">`SocketsHttpHandler.ConnectCallback` ist eine neue API des Release Candidate 2 von .NET 5.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-121">`SocketsHttpHandler.ConnectCallback` is a new API in .NET 5 release candidate 2.</span></span>

<span data-ttu-id="a0e5e-122">Beispiel für eine UDS-Verbindungsfactory (Unix Domain Socket):</span><span class="sxs-lookup"><span data-stu-id="a0e5e-122">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="a0e5e-123">So wird die benutzerdefinierte Verbindungsfactory zum Erstellen eines Kanals verwendet:</span><span class="sxs-lookup"><span data-stu-id="a0e5e-123">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="a0e5e-124">Kanäle, die mithilfe des vorangehenden Codes erstellt werden, senden gRPC-Aufrufe über Unix Domain Sockets (UDS).</span><span class="sxs-lookup"><span data-stu-id="a0e5e-124">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span> <span data-ttu-id="a0e5e-125">Unterstützung für andere IPC-Technologien kann mithilfe der Erweiterbarkeit in Kestrel und `SocketsHttpHandler` implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="a0e5e-125">Support for other IPC technologies can be implemented using the extensibility in Kestrel and `SocketsHttpHandler`.</span></span>
