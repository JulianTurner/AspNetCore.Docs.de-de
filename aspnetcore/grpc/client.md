---
title: Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients
author: jamesnk
description: Hier erfahren Sie, wie Sie gRPC-Dienste mit dem .NET-gRPC-Client aufrufen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/client
ms.openlocfilehash: 9322020083ce25b00b2979633ae8a692cfd4da4a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060962"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="5d9a9-103">Aufrufen von gRPC-Diensten mithilfe eines .NET-Clients</span><span class="sxs-lookup"><span data-stu-id="5d9a9-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="5d9a9-104">Im NuGet-Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) ist eine .NET-gRPC-Clientbibliothek verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="5d9a9-105">Im vorliegenden Dokument wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-105">This document explains how to:</span></span>

* <span data-ttu-id="5d9a9-106">Konfigurieren eines gRPC-Clients, um gRPC-Dienste aufzurufen</span><span class="sxs-lookup"><span data-stu-id="5d9a9-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="5d9a9-107">Durchführen von gRPC-Aufrufe von unären, Serverstreaming- und Clientstreamingmethoden sowie von Methoden für bidirektionales Streaming.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="5d9a9-108">Konfigurieren eines gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="5d9a9-108">Configure gRPC client</span></span>

<span data-ttu-id="5d9a9-109">gRPC-Clients sind konkrete Clienttypen, [die aus *\*.proto* Dateien generiert werden](xref:grpc/basics#generated-c-assets).</span><span class="sxs-lookup"><span data-stu-id="5d9a9-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="5d9a9-110">Der konkrete gRPC-Client verfügt über Methoden, die in den gRPC-Dienst in der *\*.proto* -Datei übersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="5d9a9-111">Beispielsweise generiert ein Dienst namens `Greeter` einen `GreeterClient`-Typ mit Methoden zum Aufrufen des Diensts.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="5d9a9-112">Ein gRPC-Client wird aus einem Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="5d9a9-113">Starten Sie das Erstellen eines Kanals mithilfe von `GrpcChannel.ForAddress`, und verwenden Sie dann den Kanal, um einen gRPC-Client zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="5d9a9-114">Ein Kanal steht für eine langlebige Verbindung zu einem gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="5d9a9-115">Wenn ein Kanal erstellt wird, wird er mit Optionen zum Aufrufen eines Diensts konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="5d9a9-116">Die für Aufrufe verwendete Klasse `HttpClient`, die maximale Größe für gesendete und empfange Nachrichten sowie die Protokollierung können in der Klasse `GrpcChannelOptions` angegeben und mit `GrpcChannel.ForAddress` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="5d9a9-117">Eine vollständige Liste der Optionen finden Sie unter [Konfigurieren von Clientoptionen](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="5d9a9-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="5d9a9-118">Konfigurieren von TLS</span><span class="sxs-lookup"><span data-stu-id="5d9a9-118">Configure TLS</span></span>

<span data-ttu-id="5d9a9-119">Die Sicherheit auf Verbindungsebene von gRPC-Client und dem aufgerufenen Dienst muss übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="5d9a9-120">Beim Erstellen des gRPC-Kanals wird für den gRPC-Client die Verwendung von Transport Layer Security (TLS) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="5d9a9-121">Ein gRPC-Client löst einen Fehler aus, wenn beim Aufrufen eines Diensts die Sicherheit auf Verbindungsebene von Kanal und nicht Dienst nicht übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="5d9a9-122">Stellen Sie beim Konfigurieren eines gRPC-Kanals für die Verwendung von TLS sicher, dass die Serveradresse mit `https` beginnt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="5d9a9-123">`GrpcChannel.ForAddress("https://localhost:5001")` verwendet beispielsweise das HTTPS-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="5d9a9-124">Der gRPC-Kanal handelt automatisch eine mit TLS gesicherte Verbindung aus und verwendet für gRPC-Aufrufe eine sichere Verbindung.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="5d9a9-125">gRPC unterstützt die Authentifizierung mit Clientzertifikaten über TLS.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="5d9a9-126">Informationen zum Konfigurieren von Clientzertifikaten mit einem gRPC-Kanal finden Sie unter <xref:grpc/authn-and-authz#client-certificate-authentication>.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="5d9a9-127">Stellen Sie zum Aufrufen von unsicheren gRPC-Diensten sicher, dass die Serveradresse mit `http` beginnt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="5d9a9-128">`GrpcChannel.ForAddress("http://localhost:5000")` verwendet beispielsweise das HTTP-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="5d9a9-129">Wenn Sie [unsichere gRPC-Dienste mit dem .NET-Client aufrufen](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client) möchten, sind ab .NET Core 3.1 weitere Konfigurationen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="5d9a9-130">Clientleistung</span><span class="sxs-lookup"><span data-stu-id="5d9a9-130">Client performance</span></span>

<span data-ttu-id="5d9a9-131">Kanal- und Clientleistung und -nutzung:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="5d9a9-132">Das Erstellen eines Kanals kann ein kostspieliger Vorgang sein.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="5d9a9-133">Wenn ein Kanal für gRPC-Aufrufe wiederverwendet wird, stehen Leistungsvorteile zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="5d9a9-134">gRPC-Clients werden mit Kanälen erstellt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="5d9a9-135">Bei gRPC-Clients handelt es sich um Lightweightobjekte. Sie müssen nicht zwischengespeichert oder wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="5d9a9-136">Aus einem Kanal können mehrere gRPC-Clients erstellt werden, einschließlich verschiedener Clienttypen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="5d9a9-137">Ein Kanal und Clients, die aus dem Kanal erstellt wurden, können sicher von mehreren Threads verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="5d9a9-138">Clients, die aus dem Kanal erstellt wurden, können mehrere gleichzeitige Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="5d9a9-139">`GrpcChannel.ForAddress` ist nicht die einzige Option für das Erstellen eines gRPC-Clients.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="5d9a9-140">Wenn Sie gRPC-Dienste in einer ASP.NET Core-App aufrufen, sollten Sie die [Integration der gRPC-Clientfactory](xref:grpc/clientfactory) in Erwägung ziehen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="5d9a9-141">Die gRPC-Integration mit `HttpClientFactory` bietet eine zentralisierte Alternative zur Erstellung von gRPC-Clients.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="5d9a9-142">Das Aufrufen von gRPC über HTTP/2 mit `Grpc.Net.Client` wird in Xamarin aktuell nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="5d9a9-143">Die HTTP/2-Unterstützung soll jedoch in einem zukünftigen Release von Xamarin optimiert werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="5d9a9-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) und [gRPC-Web](xref:grpc/browser) sind funktionsfähige Alternativen, die aktuell verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="5d9a9-145">Durchführen von gRPC-Aufrufen</span><span class="sxs-lookup"><span data-stu-id="5d9a9-145">Make gRPC calls</span></span>

<span data-ttu-id="5d9a9-146">Ein gRPC-Aufruf wird initiiert, indem eine Methode auf dem Client aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="5d9a9-147">Der gRPC-Client verarbeitet die Nachrichtenserialisierung und leitet den gRPC-Aufruf an den entsprechenden Dienst weiter.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="5d9a9-148">Bei gRPC stehen verschiedene Methodentypen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-148">gRPC has different types of methods.</span></span> <span data-ttu-id="5d9a9-149">Wie der Client verwendet wird, um einen gRPC-Aufruf durchzuführen, hängt vom Typ der aufgerufenen Methode ab.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="5d9a9-150">Es gibt die folgenden gRPC-Methodentypen:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-150">The gRPC method types are:</span></span>

* <span data-ttu-id="5d9a9-151">Unär</span><span class="sxs-lookup"><span data-stu-id="5d9a9-151">Unary</span></span>
* <span data-ttu-id="5d9a9-152">Serverstreaming</span><span class="sxs-lookup"><span data-stu-id="5d9a9-152">Server streaming</span></span>
* <span data-ttu-id="5d9a9-153">Clientstreaming</span><span class="sxs-lookup"><span data-stu-id="5d9a9-153">Client streaming</span></span>
* <span data-ttu-id="5d9a9-154">Bidirektionales Streaming</span><span class="sxs-lookup"><span data-stu-id="5d9a9-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="5d9a9-155">Unärer Aufruf</span><span class="sxs-lookup"><span data-stu-id="5d9a9-155">Unary call</span></span>

<span data-ttu-id="5d9a9-156">Ein unärer Aufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="5d9a9-157">Sobald der Dienst abgeschlossen ist, wird eine Antwortnachricht zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="5d9a9-158">Alle unären Dienstmethoden in der Datei *\*.proto* führen zu zwei .NET-Methoden auf dem konkreten gRPC-Clienttyp, um die Methode aufzurufen: eine asynchrone Methode und eine blockierende Methode.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="5d9a9-159">Bei `GreeterClient` gibt es beispielsweise zwei Möglichkeiten, `SayHello` aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="5d9a9-160">`GreeterClient.SayHelloAsync` ruft den `Greeter.SayHello`-Dienst asynchron auf und</span><span class="sxs-lookup"><span data-stu-id="5d9a9-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="5d9a9-161">kann erwartet werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-161">Can be awaited.</span></span>
* <span data-ttu-id="5d9a9-162">`GreeterClient.SayHello` ruft den `Greeter.SayHello`-Dienst auf und blockiert, bis der Vorgang abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="5d9a9-163">Verwenden Sie diese Möglichkeit nicht für asynchronen Code.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="5d9a9-164">Serverstreamingaufruf</span><span class="sxs-lookup"><span data-stu-id="5d9a9-164">Server streaming call</span></span>

<span data-ttu-id="5d9a9-165">Ein Serverstreamingaufruf beginnt damit, dass der Client eine Anforderungsnachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="5d9a9-166">`ResponseStream.MoveNext()` liest Nachrichten, die vom Dienst gestreamt werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="5d9a9-167">Der Serverstreamingaufruf ist abgeschlossen, wenn `ResponseStream.MoveNext()` `false` zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="5d9a9-168">Wenn Sie C# 8 oder höher verwenden, kann die `await foreach`-Syntax verwendet werden, um Nachrichten zu lesen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="5d9a9-169">Die `IAsyncStreamReader<T>.ReadAllAsync()`-Erweiterungsmethode liest alle Nachrichten aus dem Antwortstream:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="5d9a9-170">Clientstreamingaufruf</span><span class="sxs-lookup"><span data-stu-id="5d9a9-170">Client streaming call</span></span>

<span data-ttu-id="5d9a9-171">Ein Clientstreamingaufruf beginnt, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="5d9a9-172">Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="5d9a9-173">Wenn der Client das Senden von Nachrichten abgeschlossen hat, sollte `RequestStream.CompleteAsync()` aufgerufen werden, damit der Dienst benachrichtigt wird.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="5d9a9-174">Der Aufruf ist abgeschlossen, wenn der Dienst eine Antwortnachricht zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-174">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="5d9a9-175">Aufruf von bidirektionalem Streaming</span><span class="sxs-lookup"><span data-stu-id="5d9a9-175">Bi-directional streaming call</span></span>

<span data-ttu-id="5d9a9-176">Ein Aufruf für bidirektionales Streaming beginnt, *ohne* dass der Client eine Nachricht sendet.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="5d9a9-177">Der Client kann Nachrichten mit `RequestStream.WriteAsync` senden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="5d9a9-178">Auf vom Dienst gestreamte Nachrichten kann mit `ResponseStream.MoveNext()` oder `ResponseStream.ReadAllAsync()` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="5d9a9-179">Der Aufruf für bidirektionales Streaming ist abgeschlossen, wenn `ResponseStream` keine weiteren Nachrichten mehr hat.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

Console.WriteLine("Starting background task to receive messages");
var readTask = Task.Run(async () =>
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine(response.Message);
        // Echo messages sent to the service
    }
});

Console.WriteLine("Starting to send messages");
Console.WriteLine("Type a message to echo then press enter.");
while (true)
{
    var result = Console.ReadLine();
    if (string.IsNullOrEmpty(result))
    {
        break;
    }

    await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
}

Console.WriteLine("Disconnecting");
await call.RequestStream.CompleteAsync();
await readTask;
```

<span data-ttu-id="5d9a9-180">Um die beste Leistung zu erzielen und unnötige Fehler beim Client und beim Dienst zu vermeiden, versuchen Sie, bidirektionale Streamingaufrufe ordnungsgemäß abzuschließen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="5d9a9-181">Ein bidirektionaler Aufruf wird ordnungsgemäß abgeschlossen, wenn der Server das Lesen des Anforderungsdatenstroms und der Client das Lesen des Antwortdatenstroms beendet hat.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="5d9a9-182">Der vorherige Beispielaufruf ist ein Beispiel für einen bidirektionalen Aufruf, der ordnungsgemäß beendet wird.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="5d9a9-183">Im Aufruf führt der Client die folgenden Aktionen aus:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-183">In the call, the client:</span></span>

1. <span data-ttu-id="5d9a9-184">Er startet einen neuen bidirektionalen Streamingaufruf, indem `EchoClient.Echo` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="5d9a9-185">Er erstellt einen Hintergrundtask zum Lesen von Nachrichten aus dem Dienst mit `ResponseStream.ReadAllAsync()`.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="5d9a9-186">Er sendet Nachrichten mit `RequestStream.WriteAsync` an den Server.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="5d9a9-187">Er benachrichtigt den Server, dass das Senden von Nachrichten mit `RequestStream.CompleteAsync()` abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="5d9a9-188">Er wartet, bis der Hintergrundtask alle eingehenden Nachrichten gelesen hat.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="5d9a9-189">Während des Aufrufs von bidirektionalem Streaming können sich Client und Dienst jederzeit gegenseitig Nachrichten senden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="5d9a9-190">Die beste Clientlogik für die Interaktion mit einem bidirektionalem Aufruf variiert je nach Dienstlogik.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="5d9a9-191">Zugreifen auf gRPC-Nachspanne</span><span class="sxs-lookup"><span data-stu-id="5d9a9-191">Access gRPC trailers</span></span>

<span data-ttu-id="5d9a9-192">gRPC-Aufrufe können gRPC-Nachspanne zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="5d9a9-193">gRPC-Nachspanne werden verwendet, um Name/Wert-Metadaten zu einem Aufruf bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="5d9a9-194">Nachspanne stellen eine ähnliche Funktionalität wie HTTP-Header bereit, werden aber am Ende des Aufrufs empfangen.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="5d9a9-195">Auf gRPC-Nachspanne kann über `GetTrailers()` zugegriffen werden, die eine Sammlung von Metadaten zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="5d9a9-196">Nachspanne werden nach Abschluss der Antwort zurückgegeben, weshalb Sie erst alle Antwortnachrichten abwarten müssen, bevor Sie auf die Nachspanne zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="5d9a9-197">Unäre und Clientstreamingaufrufe müssen auf `ResponseAsync` warten, bevor sie `GetTrailers()` aufrufen können:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="5d9a9-198">Server- und bidirektionale Streamingaufrufe müssen zuerst vollständig den Antwortdatenstrom abwarten, bevor sie `GetTrailers()` aufrufen können:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="5d9a9-199">Auf gRPC-Nachspanne kann auch über `RpcException` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="5d9a9-200">Ein Dienst kann Nachspanne eventuell zusammen mit einem gRPC-Status von „nicht in Ordnung“ zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="5d9a9-201">In dieser Situation werden die Nachspanne vom gRPC-Client aus der ausgelösten Ausnahme abgerufen:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="configure-deadline"></a><span data-ttu-id="5d9a9-202">Konfigurieren von Fristen</span><span class="sxs-lookup"><span data-stu-id="5d9a9-202">Configure deadline</span></span>

<span data-ttu-id="5d9a9-203">Das Konfigurieren einer gRPC-Aufruffrist wird empfohlen, da sie eine Obergrenze für den Zeitraum vorgibt, in dem ein Aufruf ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="5d9a9-204">Dadurch wird verhindert, dass fehlerhafte Dienste unbegrenzt lange ausgeführt werden und die Serverressourcen belasten.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="5d9a9-205">Fristen stellen einen wichtigen Aspekt bei der Erstellung zuverlässiger Apps dar.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="5d9a9-206">Konfigurieren Sie `CallOptions.Deadline`, um eine Frist für einen gRPC-Aufruf festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5d9a9-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="5d9a9-207">Weitere Informationen finden Sie unter <xref:grpc/deadlines-cancellation#deadlines>.</span><span class="sxs-lookup"><span data-stu-id="5d9a9-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5d9a9-208">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5d9a9-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
