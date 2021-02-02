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
# <a name="introduction-to-grpc-on-net"></a>Einführung in gRPC in .NET

Von [John Luo](https://github.com/juntaoluo) und [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) ist ein sprachunabhängiges RPC-Framework mit hoher Leistung (Remote Procedure Call, Remoteprozeduraufruf).

Im Folgenden sind die Hauptvorteile von gRPC aufgelistet:
* Ein modernes, hochleistungsfähiges und reduziertes Framework für Remoteprozeduraufrufe
* Contract First-API-Entwicklung mit standardmäßiger Verwendung von Protokollpuffern, wodurch sprachunabhängige Implementierungen möglich sind
* Für viele Sprachen verfügbare Tools zur Generierung stark typisierter Server und Clients
* Unterstützung von Clients, Servern und bidirektionalen Streamingaufrufen
* Reduzierte Netzwerkauslastung mit binärer Protobuf-Serialisierung

gRPC ist für die folgenden Komponenten besonders geeignet:
* Einfache Microservices, bei denen Effizienz essentiell ist
* Mehrsprachige Systeme, bei denen mehrere Sprachen für die Entwicklung erforderlich sind
* Point-to-Point-Dienste, die in Echtzeit Streaminganforderungen oder -antworten verarbeiten müssen

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>C#-Toolunterstützung für PROTO-Dateien

gRPC verwendet einen Vertrag zuerst-Ansatz für die API-Entwicklung. Dienste und Nachrichten werden in *\*.proto*-Dateien definiert:

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

.NET-Typen für Dienste, Clients und Nachrichten werden automatisch generiert, indem *\*.proto*-Dateien in ein Projekt eingeschlossen werden:

* Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.
* Fügen Sie der `<Protobuf>`-Elementgruppe *\*.proto*-Dateien hinzu.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

Weitere Informationen zur Unterstützung von gRPC-Tools finden Sie unter <xref:grpc/basics>.

## <a name="grpc-services-on-aspnet-core"></a>gRPC-Dienste in ASP.NET Core

gRPC-Dienste können in ASP.NET Core gehostet werden. Dienste sind vollständig in häufig verwendete ASP.NET Core-Features wie Protokollierung, Abhängigkeitsinjektion (DI), Authentifizierung und Autorisierung integriert.

Die Projektvorlage für gRPC-Dienste stellt einen Startdienst bereit:

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

`GreeterService` erbt vom `GreeterBase`-Typ, der aus dem `Greeter`-Dienst in der *\*.proto*-Datei generiert wird. Der Dienst wird für Clients in *Startup.cs* verfügbar gemacht:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Weitere Informationen zu gRPC-Diensten in ASP.NET Core finden Sie unter <xref:grpc/aspnetcore>.

## <a name="call-grpc-services-with-a-net-client"></a>Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients

gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto* Dateien generiert werden](xref:grpc/basics#generated-c-assets). Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto*-Datei übersetzt werden.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Ein gRPC-Client wird mithilfe eines Kanals erstellt, der eine langlebige Verbindung mit einem gRPC-Dienst darstellt. Ein Kanal kann mithilfe von `GrpcChannel.ForAddress` erstellt werden.

Weitere Informationen zum Erstellen von Clients und zum Aufrufen verschiedener Dienstmethoden finden Sie unter <xref:grpc/client>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
