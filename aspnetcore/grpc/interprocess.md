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
# <a name="inter-process-communication-with-grpc"></a><span data-ttu-id="3f967-103">Inter-Process Communication mit gRPC</span><span class="sxs-lookup"><span data-stu-id="3f967-103">Inter-process communication with gRPC</span></span>

<span data-ttu-id="3f967-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3f967-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3f967-105">gRPC-Aufrufe zwischen einem Client und einem Dienst werden in der Regel über TCP-Sockets gesendet.</span><span class="sxs-lookup"><span data-stu-id="3f967-105">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="3f967-106">TCP eignet sich ideal für die Kommunikation über ein Netzwerk, [Inter-Process Communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) ist jedoch noch effizienter, wenn sich Client und Dienst auf demselben Computer befinden.</span><span class="sxs-lookup"><span data-stu-id="3f967-106">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span> <span data-ttu-id="3f967-107">In diesem Artikel wird erläutert, wie gRPC mit benutzerdefinierten Datentransporten in IPC-Szenarios verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="3f967-107">This document explains how to use gRPC with custom transports in IPC scenarios.</span></span>

## <a name="server-configuration"></a><span data-ttu-id="3f967-108">Serverkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3f967-108">Server configuration</span></span>

<span data-ttu-id="3f967-109">Benutzerdefinierte Datentransporte werden von Kestrel unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3f967-109">Custom transports are supported by Kestrel.</span></span> <span data-ttu-id="3f967-110">Kestrel wird in der Datei *Program.cs* konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="3f967-110">Kestrel is configured in *Program.cs*:</span></span>

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

<span data-ttu-id="3f967-111">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3f967-111">The preceding example:</span></span>

* <span data-ttu-id="3f967-112">Der Endpunkt von Kestrel wird in `ConfigureKestrel` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="3f967-112">Configures Kestrel's endpoints in `ConfigureKestrel`.</span></span>
* <span data-ttu-id="3f967-113"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> wird aufgerufen, um mit dem angegebenen Pfad am [Unix Domain Socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) zu lauschen.</span><span class="sxs-lookup"><span data-stu-id="3f967-113">Calls <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> to listen to a [Unix domain socket (UDS)](https://en.wikipedia.org/wiki/Unix_domain_socket) with the specified path.</span></span>

<span data-ttu-id="3f967-114">Kestrel weist integrierte Unterstützung für UDS-Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="3f967-114">Kestrel has built-in support for UDS endpoints.</span></span> <span data-ttu-id="3f967-115">UDS wird für Linux, macOS und [aktuelle Windows-Versionen](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3f967-115">UDS are supported on Linux, macOS and [modern versions of Windows](https://devblogs.microsoft.com/commandline/af_unix-comes-to-windows/).</span></span>

## <a name="client-configuration"></a><span data-ttu-id="3f967-116">Clientkonfiguration</span><span class="sxs-lookup"><span data-stu-id="3f967-116">Client configuration</span></span>

<span data-ttu-id="3f967-117">`GrpcChannel` unterstützt gRPC-Aufrufe für benutzerdefinierte Datentransporte.</span><span class="sxs-lookup"><span data-stu-id="3f967-117">`GrpcChannel` supports making gRPC calls over custom transports.</span></span> <span data-ttu-id="3f967-118">Wenn ein Kanal erstellt wird, kann er mit einer `SocketsHttpHandler`-Klasse konfiguriert werden, die über ein benutzerdefiniertes `ConnectionFactory`-Objekt verfügt.</span><span class="sxs-lookup"><span data-stu-id="3f967-118">When a channel is created, it can be configured with a `SocketsHttpHandler` that has a custom `ConnectionFactory`.</span></span> <span data-ttu-id="3f967-119">Die Factory ermöglicht es dem Client, Verbindungen für benutzerdefinierte Datentransporte herzustellen und dann HTTP-Anforderungen über diese Datentransporte zu senden.</span><span class="sxs-lookup"><span data-stu-id="3f967-119">The factory allows the client to make connections over custom transports and then send HTTP requests over that transport.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3f967-120">`ConnectionFactory` ist eine neue API des Release Candidate 1 von .NET 5.</span><span class="sxs-lookup"><span data-stu-id="3f967-120">`ConnectionFactory` is a new API in .NET 5 release candidate 1.</span></span>

<span data-ttu-id="3f967-121">Beispiel für eine UDS-Verbindungsfactory (Unix Domain Socket):</span><span class="sxs-lookup"><span data-stu-id="3f967-121">Unix domain sockets connection factory example:</span></span>

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

<span data-ttu-id="3f967-122">So wird die benutzerdefinierte Verbindungsfactory zum Erstellen eines Kanals verwendet:</span><span class="sxs-lookup"><span data-stu-id="3f967-122">Using the custom connection factory to create a channel:</span></span>

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

<span data-ttu-id="3f967-123">Kanäle, die mithilfe des vorangehenden Codes erstellt werden, senden gRPC-Aufrufe über Unix Domain Sockets (UDS).</span><span class="sxs-lookup"><span data-stu-id="3f967-123">Channels created using the preceding code send gRPC calls over Unix domain sockets.</span></span>
