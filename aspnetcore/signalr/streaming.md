---
title: 'Streaming in ASP.net Core verwenden :::no-loc(SignalR):::'
author: bradygaster
description: Erfahren Sie, wie Sie Daten zwischen dem Client und dem Server streamen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
no-loc:
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690631"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="f8c33-103">Streaming in ASP.net Core verwenden :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="f8c33-103">Use streaming in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="f8c33-104">Von [Brennan](https://github.com/BrennanConroy) "a"</span><span class="sxs-lookup"><span data-stu-id="f8c33-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8c33-105">ASP.net Core :::no-loc(SignalR)::: unterstützt das Streaming vom Client zum Server und vom Server zum Client.</span><span class="sxs-lookup"><span data-stu-id="f8c33-105">ASP.NET Core :::no-loc(SignalR)::: supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="f8c33-106">Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="f8c33-107">Beim Streaming wird jedes Fragment an den Client oder Server gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8c33-108">ASP.net Core :::no-loc(SignalR)::: unterstützt das Streamen von Rückgabe Werten von Server Methoden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-108">ASP.NET Core :::no-loc(SignalR)::: supports streaming return values of server methods.</span></span> <span data-ttu-id="f8c33-109">Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="f8c33-110">Wenn ein Rückgabewert an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="f8c33-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f8c33-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="f8c33-112">Einrichten eines Hubs für das Streaming</span><span class="sxs-lookup"><span data-stu-id="f8c33-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8c33-113">Eine Hub-Methode wird automatisch zu einer streaminghub-Methode, wenn Sie <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , oder zurückgibt `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f8c33-114">Eine Hub-Methode wird automatisch zu einer streaminghub-Methode, wenn Sie einen <xref:System.Threading.Channels.ChannelReader%601> oder ein zurückgibt `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="f8c33-115">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8c33-116">Streaming Hub-Methoden können `IAsyncEnumerable<T>` zusätzlich zu zurückgeben `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="f8c33-117">Die einfachste Möglichkeit zur Rückgabe `IAsyncEnumerable<T>` besteht darin, dass Sie die Hub-Methode zu einer asynchronen Iteratormethode wird, wie im folgenden Beispiel veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="f8c33-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="f8c33-118">Asynchrone Hub-Iteratormethoden können einen `CancellationToken` Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert.</span><span class="sxs-lookup"><span data-stu-id="f8c33-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="f8c33-119">Asynchrone Iteratormethoden vermeiden häufige Probleme, die mit Kanälen auftreten, wie z. b. das `ChannelReader` frühzeitige zurückgeben oder Beenden der Methode ohne das Abschließen von <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="f8c33-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="f8c33-120">Das folgende Beispiel zeigt die Grundlagen des Streamings von Daten an den Client mithilfe von Kanälen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="f8c33-121">Jedes Mal, wenn ein Objekt in das geschrieben wird <xref:System.Threading.Channels.ChannelWriter%601> , wird das Objekt sofort an den Client gesendet.</span><span class="sxs-lookup"><span data-stu-id="f8c33-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="f8c33-122">Am Ende `ChannelWriter` ist abgeschlossen, um dem Client mitzuteilen, dass der Stream geschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="f8c33-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="f8c33-123">Schreiben Sie in `ChannelWriter<T>` einem Hintergrund Thread in den, und geben Sie `ChannelReader` so bald wie möglich zurück.</span><span class="sxs-lookup"><span data-stu-id="f8c33-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="f8c33-124">Andere hubaufrufe werden blockiert, bis eine `ChannelReader` zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="f8c33-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="f8c33-125">Packen Sie die Logik in einer- [ `try ... catch` Anweisung](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="f8c33-125">Wrap logic in a [`try ... catch` statement](/dotnet/csharp/language-reference/keywords/try-catch).</span></span> <span data-ttu-id="f8c33-126">Vervollständigen Sie `Channel` in einem- [ `finally` Block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span><span class="sxs-lookup"><span data-stu-id="f8c33-126">Complete the `Channel` in a [`finally` block](/dotnet/csharp/language-reference/keywords/try-catch-finally).</span></span> <span data-ttu-id="f8c33-127">Wenn Sie einen Fehler weitergeben möchten, erfassen Sie ihn innerhalb des `catch` Blocks, und schreiben Sie ihn in den- `finally` Block.</span><span class="sxs-lookup"><span data-stu-id="f8c33-127">If you want to flow an error, capture it inside the `catch` block and write it in the `finally` block.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f8c33-128">Server-zu-Client-streaminghubmethoden können einen `CancellationToken` Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert.</span><span class="sxs-lookup"><span data-stu-id="f8c33-128">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="f8c33-129">Verwenden Sie dieses Token, um den Server Vorgang zu beenden und alle Ressourcen freizugeben, wenn der Client die Verbindung vor dem Ende des Streams trennt.</span><span class="sxs-lookup"><span data-stu-id="f8c33-129">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="f8c33-130">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="f8c33-130">Client-to-server streaming</span></span>

<span data-ttu-id="f8c33-131">Eine Hub-Methode wird automatisch zu einer Client-zu-Server-streaminghub Methode, wenn Sie ein oder mehrere Objekte des Typs <xref:System.Threading.Channels.ChannelReader%601> oder akzeptiert <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="f8c33-131">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="f8c33-132">Das folgende Beispiel zeigt die Grundlagen zum Lesen von Streamingdaten, die vom Client gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-132">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="f8c33-133">Wenn der Client in den schreibt <xref:System.Threading.Channels.ChannelWriter%601> , werden die Daten `ChannelReader` auf dem Server, von dem die Hub-Methode liest, in den geschrieben.</span><span class="sxs-lookup"><span data-stu-id="f8c33-133">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="f8c33-134">Eine <xref:System.Collections.Generic.IAsyncEnumerable%601> Version der-Methode folgt.</span><span class="sxs-lookup"><span data-stu-id="f8c33-134">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a><span data-ttu-id="f8c33-135">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-135">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="f8c33-136">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-136">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f8c33-137">Die `StreamAsync` -Methode und die- `StreamAsChannelAsync` Methode in `HubConnection` werden zum Aufrufen von Server-zu-Client-Streamingmethoden verwendet.</span><span class="sxs-lookup"><span data-stu-id="f8c33-137">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="f8c33-138">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsync` oder `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-138">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="f8c33-139">Der generische Parameter für `StreamAsync<T>` und `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-139">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="f8c33-140">Ein Objekt vom Typ `IAsyncEnumerable<T>` oder `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="f8c33-140">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="f8c33-141">Ein `StreamAsync` Beispiel, das zurückgibt `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="f8c33-141">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

<span data-ttu-id="f8c33-142">Ein entsprechendes `StreamAsChannelAsync` Beispiel, das zurückgibt `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="f8c33-142">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="f8c33-143">Die `StreamAsChannelAsync` -Methode für `HubConnection` wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-143">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="f8c33-144">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-144">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="f8c33-145">Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-145">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="f8c33-146">Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="f8c33-146">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f8c33-147">Die `StreamAsChannelAsync` -Methode für `HubConnection` wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-147">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="f8c33-148">Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-148">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="f8c33-149">Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-149">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="f8c33-150">Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.</span><span class="sxs-lookup"><span data-stu-id="f8c33-150">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="f8c33-151">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="f8c33-151">Client-to-server streaming</span></span>

<span data-ttu-id="f8c33-152">Es gibt zwei Möglichkeiten, eine Client-zu-Server-streaminghub Methode aus dem .NET-Client aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-152">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="f8c33-153">Sie können entweder ein `IAsyncEnumerable<T>` oder ein `ChannelReader` als Argument an `SendAsync` , `InvokeAsync` oder übergeben `StreamAsChannelAsync` , abhängig von der aufgerufenen Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="f8c33-153">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="f8c33-154">Wenn Daten in das- `IAsyncEnumerable` Objekt oder das- `ChannelWriter` Objekt geschrieben werden, empfängt die Hub-Methode auf dem Server ein neues Element mit den Daten vom Client.</span><span class="sxs-lookup"><span data-stu-id="f8c33-154">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="f8c33-155">Wenn ein `IAsyncEnumerable` Objekt verwendet wird, endet der Stream, nachdem die Methode, die streamelemente zurückgibt, beendet wird.</span><span class="sxs-lookup"><span data-stu-id="f8c33-155">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

<span data-ttu-id="f8c33-156">Wenn Sie einen verwenden `ChannelWriter` , vervollständigen Sie den Kanal mit folgenden Aktionen `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="f8c33-156">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="f8c33-157">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-157">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="f8c33-158">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-158">Server-to-client streaming</span></span>

<span data-ttu-id="f8c33-159">JavaScript-Clients wenden Server-zu-Client-Streamingmethoden auf Hubs mit an `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-159">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="f8c33-160">Die- `stream` Methode akzeptiert zwei Argumente:</span><span class="sxs-lookup"><span data-stu-id="f8c33-160">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="f8c33-161">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="f8c33-161">The name of the hub method.</span></span> <span data-ttu-id="f8c33-162">Im folgenden Beispiel lautet der Name der Hub-Methode `Counter` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-162">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="f8c33-163">In der Hub-Methode definierte Argumente.</span><span class="sxs-lookup"><span data-stu-id="f8c33-163">Arguments defined in the hub method.</span></span> <span data-ttu-id="f8c33-164">Im folgenden Beispiel sind die Argumente eine Anzahl für die Anzahl der zu empfangenden Datenstrom Elemente und die Verzögerung zwischen streamingelementen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-164">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="f8c33-165">`connection.stream` Gibt einen zurück `IStreamResult` , der eine- `subscribe` Methode enthält.</span><span class="sxs-lookup"><span data-stu-id="f8c33-165">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="f8c33-166">Übergeben `IStreamSubscriber` Sie einen an, `subscribe` und legen Sie die `next` `error` Rückrufe, und fest `complete` , um Benachrichtigungen vom Aufruf zu empfangen `stream` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-166">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="f8c33-167">Um den Stream vom Client zu beenden, müssen Sie die- `dispose` Methode für das-Objekt abrufen, das `ISubscription` von der-Methode zurückgegeben wird `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-167">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="f8c33-168">Wenn Sie diese Methode aufrufen, wird der- `CancellationToken` Parameter der Hub-Methode abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-168">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="f8c33-169">Um den Stream vom Client zu beenden, müssen Sie die- `dispose` Methode für das-Objekt abrufen, das `ISubscription` von der-Methode zurückgegeben wird `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-169">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="f8c33-170">Client-zu-Server-Streaming</span><span class="sxs-lookup"><span data-stu-id="f8c33-170">Client-to-server streaming</span></span>

<span data-ttu-id="f8c33-171">Von JavaScript-Clients werden Client-zu-Server-Streamingmethoden für Hubs aufgerufen, indem ein `Subject` als Argument an `send` , `invoke` oder übergeben `stream` wird, abhängig von der aufgerufenen hubmethode.</span><span class="sxs-lookup"><span data-stu-id="f8c33-171">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="f8c33-172">`Subject`Ist eine Klasse, die wie eine aussieht `Subject` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-172">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="f8c33-173">Beispielsweise können Sie in rxjs die Klasse " [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) " aus dieser Bibliothek verwenden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-173">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="f8c33-174">Wenn `subject.next(item)` Sie mit einem Element aufrufen, wird das Element in den Stream geschrieben, und die Hub-Methode empfängt das Element auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="f8c33-174">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="f8c33-175">Um den Stream zu beenden, wird aufgerufen `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="f8c33-175">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="f8c33-176">Java-Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-176">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="f8c33-177">Streaming zwischen Server und Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-177">Server-to-client streaming</span></span>

<span data-ttu-id="f8c33-178">Der :::no-loc(SignalR)::: Java-Client verwendet die- `stream` Methode, um Streamingmethoden aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="f8c33-178">The :::no-loc(SignalR)::: Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="f8c33-179">`stream` Es werden drei oder mehr Argumente akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="f8c33-179">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="f8c33-180">Der erwartete Typ der streamelemente.</span><span class="sxs-lookup"><span data-stu-id="f8c33-180">The expected type of the stream items.</span></span>
* <span data-ttu-id="f8c33-181">Der Name der Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="f8c33-181">The name of the hub method.</span></span>
* <span data-ttu-id="f8c33-182">In der Hub-Methode definierte Argumente.</span><span class="sxs-lookup"><span data-stu-id="f8c33-182">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="f8c33-183">Die- `stream` Methode für `HubConnection` gibt einen Observable-Wert des Datenstrom-Elementtyps zurück.</span><span class="sxs-lookup"><span data-stu-id="f8c33-183">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="f8c33-184">Die-Methode des Observable-Typs `subscribe` ist `onNext` , wo `onError` und `onCompleted` Handler definiert werden.</span><span class="sxs-lookup"><span data-stu-id="f8c33-184">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f8c33-185">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f8c33-185">Additional resources</span></span>

* [<span data-ttu-id="f8c33-186">Hubs</span><span class="sxs-lookup"><span data-stu-id="f8c33-186">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="f8c33-187">.NET-Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-187">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="f8c33-188">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="f8c33-188">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="f8c33-189">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="f8c33-189">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
