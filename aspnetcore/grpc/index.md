---
title: Einführung in gRPC in .NET
author: juntaoluo
description: Informationen zu gRPC-Diensten mit dem Kestrel-Server und dem ASP.NET Core-Stapel
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 5820049aba90a2fbd06a23756b12ac9656c3b2c4
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057511"
---
# <a name="introduction-to-grpc-on-net"></a><span data-ttu-id="80161-103">Einführung in gRPC in .NET</span><span class="sxs-lookup"><span data-stu-id="80161-103">Introduction to gRPC on .NET</span></span>

<span data-ttu-id="80161-104">Von [John Luo](https://github.com/juntaoluo) und [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="80161-104">By [John Luo](https://github.com/juntaoluo) and [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="80161-105">[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf).</span><span class="sxs-lookup"><span data-stu-id="80161-105">[gRPC](https://grpc.io/docs/guides/) is a language agnostic, high-performance Remote Procedure Call (RPC) framework.</span></span>

<span data-ttu-id="80161-106">Im Folgenden sind die Hauptvorteile von gRPC aufgelistet:</span><span class="sxs-lookup"><span data-stu-id="80161-106">The main benefits of gRPC are:</span></span>
* <span data-ttu-id="80161-107">Ein modernes, hochleistungsfähiges und reduziertes Framework für Remoteprozeduraufrufe</span><span class="sxs-lookup"><span data-stu-id="80161-107">Modern, high-performance, lightweight RPC framework.</span></span>
* <span data-ttu-id="80161-108">Contract First-API-Entwicklung mit standardmäßiger Verwendung von Protokollpuffern, wodurch sprachunabhängige Implementierungen möglich sind</span><span class="sxs-lookup"><span data-stu-id="80161-108">Contract-first API development, using Protocol Buffers by default, allowing for language agnostic implementations.</span></span>
* <span data-ttu-id="80161-109">Für viele Sprachen verfügbare Tools zur Generierung stark typisierter Server und Clients</span><span class="sxs-lookup"><span data-stu-id="80161-109">Tooling available for many languages to generate strongly-typed servers and clients.</span></span>
* <span data-ttu-id="80161-110">Unterstützung von Clients, Servern und bidirektionalen Streamingaufrufen</span><span class="sxs-lookup"><span data-stu-id="80161-110">Supports client, server, and bi-directional streaming calls.</span></span>
* <span data-ttu-id="80161-111">Reduzierte Netzwerkauslastung mit binärer Protobuf-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="80161-111">Reduced network usage with Protobuf binary serialization.</span></span>

<span data-ttu-id="80161-112">gRPC ist für die folgenden Komponenten besonders geeignet:</span><span class="sxs-lookup"><span data-stu-id="80161-112">These benefits make gRPC ideal for:</span></span>
* <span data-ttu-id="80161-113">Einfache Microservices, bei denen Effizienz essentiell ist</span><span class="sxs-lookup"><span data-stu-id="80161-113">Lightweight microservices where efficiency is critical.</span></span>
* <span data-ttu-id="80161-114">Mehrsprachige Systeme, bei denen mehrere Sprachen für die Entwicklung erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="80161-114">Polyglot systems where multiple languages are required for development.</span></span>
* <span data-ttu-id="80161-115">Point-to-Point-Dienste, die in Echtzeit Streaminganforderungen oder -antworten verarbeiten müssen</span><span class="sxs-lookup"><span data-stu-id="80161-115">Point-to-point real-time services that need to handle streaming requests or responses.</span></span>

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a><span data-ttu-id="80161-116">C#-Toolunterstützung für PROTO-Dateien</span><span class="sxs-lookup"><span data-stu-id="80161-116">C# Tooling support for .proto files</span></span>

<span data-ttu-id="80161-117">gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="80161-117">gRPC uses a contract-first approach to API development.</span></span> <span data-ttu-id="80161-118">Dienste und Nachrichten werden in *\*.proto*-Dateien definiert:</span><span class="sxs-lookup"><span data-stu-id="80161-118">Services and messages are defined in *\*.proto* files:</span></span>

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="80161-119">.NET-Typen für Dienste, Clients und Nachrichten werden automatisch generiert, indem *\*.proto*-Dateien in ein Projekt eingeschlossen werden:</span><span class="sxs-lookup"><span data-stu-id="80161-119">.NET types for services, clients and messages are automatically generated by including *\*.proto* files in a project:</span></span>

* <span data-ttu-id="80161-120">Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="80161-120">Add a package reference to [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) package.</span></span>
* <span data-ttu-id="80161-121">Fügen Sie der `<Protobuf>`-Elementgruppe *\*.proto*-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="80161-121">Add *\*.proto* files to the `<Protobuf>` item group.</span></span>

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

<span data-ttu-id="80161-122">Weitere Informationen zur Unterstützung von gRPC-Tools finden Sie unter <xref:grpc/basics>.</span><span class="sxs-lookup"><span data-stu-id="80161-122">For more information on gRPC tooling support, see <xref:grpc/basics>.</span></span>

## <a name="grpc-services-on-aspnet-core"></a><span data-ttu-id="80161-123">gRPC-Dienste in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80161-123">gRPC services on ASP.NET Core</span></span>

<span data-ttu-id="80161-124">gRPC-Dienste können in ASP.NET Core gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="80161-124">gRPC services can be hosted on ASP.NET Core.</span></span> <span data-ttu-id="80161-125">Dienste sind vollständig in häufig verwendete ASP.NET Core-Features wie Protokollierung, Abhängigkeitsinjektion (DI), Authentifizierung und Autorisierung integriert.</span><span class="sxs-lookup"><span data-stu-id="80161-125">Services have full integration with popular ASP.NET Core features such as logging, dependency injection (DI), authentication and authorization.</span></span>

<span data-ttu-id="80161-126">Die Projektvorlage für gRPC-Dienste stellt einen Startdienst bereit:</span><span class="sxs-lookup"><span data-stu-id="80161-126">The gRPC service project template provides a starter service:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

<span data-ttu-id="80161-127">`GreeterService` erbt vom `GreeterBase`-Typ, der aus dem `Greeter`-Dienst in der *\*.proto*-Datei generiert wird.</span><span class="sxs-lookup"><span data-stu-id="80161-127">`GreeterService` inherits from the `GreeterBase` type, which is generated from the `Greeter` service in the *\*.proto* file.</span></span> <span data-ttu-id="80161-128">Der Dienst wird für Clients in *Startup.cs* verfügbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="80161-128">The service is made accessible to clients in *Startup.cs*:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="80161-129">Weitere Informationen zu gRPC-Diensten in ASP.NET Core finden Sie unter <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="80161-129">To learn more about gRPC services on ASP.NET Core, see <xref:grpc/aspnetcore>.</span></span>

## <a name="call-grpc-services-with-a-net-client"></a><span data-ttu-id="80161-130">Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients</span><span class="sxs-lookup"><span data-stu-id="80161-130">Call gRPC services with a .NET client</span></span>

<span data-ttu-id="80161-131">gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto* Dateien generiert werden](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="80161-131">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="80161-132">Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="80161-132">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

<span data-ttu-id="80161-133">Ein gRPC-Client wird mithilfe eines Kanals erstellt, der eine langlebige Verbindung mit einem gRPC-Dienst darstellt.</span><span class="sxs-lookup"><span data-stu-id="80161-133">A gRPC client is created using a channel, which represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="80161-134">Ein Kanal kann mithilfe von `GrpcChannel.ForAddress` erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="80161-134">A channel can be created using `GrpcChannel.ForAddress`.</span></span>

<span data-ttu-id="80161-135">Weitere Informationen zum Erstellen von Clients und zum Aufrufen verschiedener Dienstmethoden finden Sie unter <xref:grpc/client>.</span><span class="sxs-lookup"><span data-stu-id="80161-135">For more information on creating clients, and calling different service methods, see <xref:grpc/client>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="80161-136">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="80161-136">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
