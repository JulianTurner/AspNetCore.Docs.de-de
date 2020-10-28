---
title: Streaming in ASP.net Core verwenden SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Daten zwischen dem Client und dem Server streamen.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690631"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>Streaming in ASP.net Core verwenden SignalR

Von [Brennan](https://github.com/BrennanConroy) "a"

::: moniker range=">= aspnetcore-3.0"

ASP.net Core SignalR unterstützt das Streaming vom Client zum Server und vom Server zum Client. Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen. Beim Streaming wird jedes Fragment an den Client oder Server gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.net Core SignalR unterstützt das Streamen von Rückgabe Werten von Server Methoden. Dies ist nützlich für Szenarien, in denen Daten Fragmente im Laufe der Zeit eintreffen. Wenn ein Rückgabewert an den Client gestreamt wird, wird jedes Fragment an den Client gesendet, sobald es verfügbar wird, anstatt darauf zu warten, dass alle Daten verfügbar werden.

::: moniker-end

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Einrichten eines Hubs für das Streaming

::: moniker range=">= aspnetcore-3.0"

Eine Hub-Methode wird automatisch zu einer streaminghub-Methode, wenn Sie <xref:System.Collections.Generic.IAsyncEnumerable`1> , <xref:System.Threading.Channels.ChannelReader%601> , oder zurückgibt `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Eine Hub-Methode wird automatisch zu einer streaminghub-Methode, wenn Sie einen <xref:System.Threading.Channels.ChannelReader%601> oder ein zurückgibt `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Streaming zwischen Server und Client

::: moniker range=">= aspnetcore-3.0"

Streaming Hub-Methoden können `IAsyncEnumerable<T>` zusätzlich zu zurückgeben `ChannelReader<T>` . Die einfachste Möglichkeit zur Rückgabe `IAsyncEnumerable<T>` besteht darin, dass Sie die Hub-Methode zu einer asynchronen Iteratormethode wird, wie im folgenden Beispiel veranschaulicht. Asynchrone Hub-Iteratormethoden können einen `CancellationToken` Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert. Asynchrone Iteratormethoden vermeiden häufige Probleme, die mit Kanälen auftreten, wie z. b. das `ChannelReader` frühzeitige zurückgeben oder Beenden der Methode ohne das Abschließen von <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Das folgende Beispiel zeigt die Grundlagen des Streamings von Daten an den Client mithilfe von Kanälen. Jedes Mal, wenn ein Objekt in das geschrieben wird <xref:System.Threading.Channels.ChannelWriter%601> , wird das Objekt sofort an den Client gesendet. Am Ende `ChannelWriter` ist abgeschlossen, um dem Client mitzuteilen, dass der Stream geschlossen ist.

> [!NOTE]
> Schreiben Sie in `ChannelWriter<T>` einem Hintergrund Thread in den, und geben Sie `ChannelReader` so bald wie möglich zurück. Andere hubaufrufe werden blockiert, bis eine `ChannelReader` zurückgegeben wird.
>
> Packen Sie die Logik in einer- [ `try ... catch` Anweisung](/dotnet/csharp/language-reference/keywords/try-catch). Vervollständigen Sie `Channel` in einem- [ `finally` Block](/dotnet/csharp/language-reference/keywords/try-catch-finally). Wenn Sie einen Fehler weitergeben möchten, erfassen Sie ihn innerhalb des `catch` Blocks, und schreiben Sie ihn in den- `finally` Block.

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

Server-zu-Client-streaminghubmethoden können einen `CancellationToken` Parameter akzeptieren, der ausgelöst wird, wenn der Client den Datenstrom abonniert. Verwenden Sie dieses Token, um den Server Vorgang zu beenden und alle Ressourcen freizugeben, wenn der Client die Verbindung vor dem Ende des Streams trennt.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Client-zu-Server-Streaming

Eine Hub-Methode wird automatisch zu einer Client-zu-Server-streaminghub Methode, wenn Sie ein oder mehrere Objekte des Typs <xref:System.Threading.Channels.ChannelReader%601> oder akzeptiert <xref:System.Collections.Generic.IAsyncEnumerable%601> . Das folgende Beispiel zeigt die Grundlagen zum Lesen von Streamingdaten, die vom Client gesendet werden. Wenn der Client in den schreibt <xref:System.Threading.Channels.ChannelWriter%601> , werden die Daten `ChannelReader` auf dem Server, von dem die Hub-Methode liest, in den geschrieben.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

Eine <xref:System.Collections.Generic.IAsyncEnumerable%601> Version der-Methode folgt.

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

## <a name="net-client"></a>.NET-Client

### <a name="server-to-client-streaming"></a>Streaming zwischen Server und Client


::: moniker range=">= aspnetcore-3.0"

Die `StreamAsync` -Methode und die- `StreamAsChannelAsync` Methode in `HubConnection` werden zum Aufrufen von Server-zu-Client-Streamingmethoden verwendet. Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsync` oder `StreamAsChannelAsync` . Der generische Parameter für `StreamAsync<T>` und `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden. Ein Objekt vom Typ `IAsyncEnumerable<T>` oder `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.

Ein `StreamAsync` Beispiel, das zurückgibt `IAsyncEnumerable<int>` :

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

Ein entsprechendes `StreamAsChannelAsync` Beispiel, das zurückgibt `ChannelReader<int>` :

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

Die `StreamAsChannelAsync` -Methode für `HubConnection` wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen. Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsChannelAsync` . Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden. Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.

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

Die `StreamAsChannelAsync` -Methode für `HubConnection` wird verwendet, um eine Server-zu-Client-Streamingmethode aufzurufen. Übergeben Sie den in der Hub-Methode definierten Namen und die Argumente der Hub-Methode an `StreamAsChannelAsync` . Der generische Parameter für `StreamAsChannelAsync<T>` gibt den Typ von Objekten an, die von der Streamingmethode zurückgegeben werden. Ein `ChannelReader<T>` wird vom Datenstrom Aufruf zurückgegeben und stellt den Datenstrom auf dem Client dar.

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

### <a name="client-to-server-streaming"></a>Client-zu-Server-Streaming

Es gibt zwei Möglichkeiten, eine Client-zu-Server-streaminghub Methode aus dem .NET-Client aufzurufen. Sie können entweder ein `IAsyncEnumerable<T>` oder ein `ChannelReader` als Argument an `SendAsync` , `InvokeAsync` oder übergeben `StreamAsChannelAsync` , abhängig von der aufgerufenen Hub-Methode.

Wenn Daten in das- `IAsyncEnumerable` Objekt oder das- `ChannelWriter` Objekt geschrieben werden, empfängt die Hub-Methode auf dem Server ein neues Element mit den Daten vom Client.

Wenn ein `IAsyncEnumerable` Objekt verwendet wird, endet der Stream, nachdem die Methode, die streamelemente zurückgibt, beendet wird.

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

Wenn Sie einen verwenden `ChannelWriter` , vervollständigen Sie den Kanal mit folgenden Aktionen `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript-Client

### <a name="server-to-client-streaming"></a>Streaming zwischen Server und Client

JavaScript-Clients wenden Server-zu-Client-Streamingmethoden auf Hubs mit an `connection.stream` . Die- `stream` Methode akzeptiert zwei Argumente:

* Der Name der Hub-Methode. Im folgenden Beispiel lautet der Name der Hub-Methode `Counter` .
* In der Hub-Methode definierte Argumente. Im folgenden Beispiel sind die Argumente eine Anzahl für die Anzahl der zu empfangenden Datenstrom Elemente und die Verzögerung zwischen streamingelementen.

`connection.stream` Gibt einen zurück `IStreamResult` , der eine- `subscribe` Methode enthält. Übergeben `IStreamSubscriber` Sie einen an, `subscribe` und legen Sie die `next` `error` Rückrufe, und fest `complete` , um Benachrichtigungen vom Aufruf zu empfangen `stream` .

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Um den Stream vom Client zu beenden, müssen Sie die- `dispose` Methode für das-Objekt abrufen, das `ISubscription` von der-Methode zurückgegeben wird `subscribe` . Wenn Sie diese Methode aufrufen, wird der- `CancellationToken` Parameter der Hub-Methode abgebrochen.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Um den Stream vom Client zu beenden, müssen Sie die- `dispose` Methode für das-Objekt abrufen, das `ISubscription` von der-Methode zurückgegeben wird `subscribe` .

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>Client-zu-Server-Streaming

Von JavaScript-Clients werden Client-zu-Server-Streamingmethoden für Hubs aufgerufen, indem ein `Subject` als Argument an `send` , `invoke` oder übergeben `stream` wird, abhängig von der aufgerufenen hubmethode. `Subject`Ist eine Klasse, die wie eine aussieht `Subject` . Beispielsweise können Sie in rxjs die Klasse " [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) " aus dieser Bibliothek verwenden.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

Wenn `subject.next(item)` Sie mit einem Element aufrufen, wird das Element in den Stream geschrieben, und die Hub-Methode empfängt das Element auf dem Server.

Um den Stream zu beenden, wird aufgerufen `subject.complete()` .

## <a name="java-client"></a>Java-Client

### <a name="server-to-client-streaming"></a>Streaming zwischen Server und Client

Der SignalR Java-Client verwendet die- `stream` Methode, um Streamingmethoden aufzurufen. `stream` Es werden drei oder mehr Argumente akzeptiert:

* Der erwartete Typ der streamelemente.
* Der Name der Hub-Methode.
* In der Hub-Methode definierte Argumente.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

Die- `stream` Methode für `HubConnection` gibt einen Observable-Wert des Datenstrom-Elementtyps zurück. Die-Methode des Observable-Typs `subscribe` ist `onNext` , wo `onError` und `onCompleted` Handler definiert werden.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
