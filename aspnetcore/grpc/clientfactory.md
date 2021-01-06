---
title: gRPC-Integration von Clientfactory in .NET Core
author: jamesnk
description: Hier erfahren Sie, wie Sie gRPC-Clients mithilfe der Clientfactory erstellen.
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060949"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC-Integration von Clientfactory in .NET Core

Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Möglichkeit, gRPC-Clients zu erstellen. Diese Möglichkeit kann als Alternative zum [Konfigurieren eigenständiger gRPC-Clientinstanzen](xref:grpc/client) verwendet werden. Die Factoryintegration ist im NuGet-Paket [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) verfügbar.

Die Factory bietet die folgenden Vorteile:

* Ein zentraler Ort für das Konfigurieren logischer gRPC-Clientinstanzen
* Optionen für die Verwaltung der Lebensdauer des zugrunde liegenden `HttpClientMessageHandler`
* Automatische Weitergabe von Stichtagen und Absagen in einem ASP.NET Core-gRPC-Dienst

## <a name="register-grpc-clients"></a>Registrierung von gRPC-Clients

Zum Registrieren eines gRPC-Clients kann die generische Erweiterungsmethode `AddGrpcClient` innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse des gRPC-Clients und die Dienstadresse angibt:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

Der gRPC-Clienttyp wird als vorübergehender Typ mit einer Abhängigkeitsinjektion (Dependency Injection, DI) registriert. Der Client kann nun injiziert und direkt in von der DI erstellten Typen genutzt werden. Bei ASP.NET Core-MVC-Controllern, SignalR-Hubs und gRPC-Diensten handelt es sich um Orte, an denen gRPC-Clients direkt injiziert werden können:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Konfigurieren von HttpClient

`HttpClientFactory` erstellt die `HttpClient`-Klasse, die vom gRPC-Client verwendet wird. `HttpClientFactory`-Standardmethoden können verwendet werden ,um Middleware für ausgehende Anforderungen hinzuzufügen oder den zugrunde liegenden `HttpClientHandler` der `HttpClient`-Klasse zu konfigurieren:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Weitere Informationen erhalten Sie unter [Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Konfigurieren von Kanal und Interceptoren

Für Folgendes sind gRPC-spezifische Methoden verfügbar:

* Konfigurieren des einem gRPC-Client zugrunde liegenden Kanals
* Hinzufügen von `Interceptor`-Instanzen, die der Client für gRPC-Aufrufe verwendet

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Weitergabe von Stichtagen und Absagen

gRPC-Clients, die von der Factory in einem gRPC-Dienst erstellt wurden, können mit `EnableCallContextPropagation()` so konfiguriert werden, dass sie die Tokens für Stichtage und Absagen automatisch an untergeordnete Aufrufe weitergeben. Die Erweiterungsmethode `EnableCallContextPropagation()` ist im NuGet-Paket [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) verfügbar.

Die Weitergabe von Aufrufkontext funktioniert, in dem die Tokens für Stichtage und Absagen aus dem aktuellen gRPC-Anforderungskontext gelesen und automatisch an ausgehende Aufrufe weitergegeben werden, die vom gRPC-Client ausgeführt werden. Die Weitergabe von Aufrufkontext ist eine sehr gute Möglichkeit, sicherzustellen, dass komplexe und verschachtelte gRPC-Szenarios immer den Stichtag und die Absage weitergeben.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

`EnableCallContextPropagation` löst standardmäßig einen Fehler aus, wenn der Client außerhalb von gRPC-Aufrufen verwendet wird. Der Fehler ist dazu konzipiert, Sie darüber zu informieren, dass kein Aufrufkontext zum Übertragen vorhanden ist. Wenn Sie den Client außerhalb eines Aufrufkontexts verwenden möchten, unterdrücken Sie den Fehler, wenn der Client mit `SuppressContextNotFoundErrors` konfiguriert ist:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Weitere Informationen zu Stichtagen und der RPC-Absage finden Sie unter [RPC-Lebenszyklus](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
