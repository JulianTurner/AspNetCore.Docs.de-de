---
title: 'ASP.net Core :::no-loc(SignalR)::: .NET-Client'
author: bradygaster
description: 'Informationen zum ASP.net Core :::no-loc(SignalR)::: .NET-Client'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: 7849322e50aeceda0850d94f9c9bcda79a6051c8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061222"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a><span data-ttu-id="d7e67-103">ASP.net Core :::no-loc(SignalR)::: .NET-Client</span><span class="sxs-lookup"><span data-stu-id="d7e67-103">ASP.NET Core :::no-loc(SignalR)::: .NET Client</span></span>

<span data-ttu-id="d7e67-104">Mit der ASP.net Core :::no-loc(SignalR)::: .NET-Client Bibliothek können Sie über :::no-loc(SignalR)::: .net-apps mit Hubs kommunizieren.</span><span class="sxs-lookup"><span data-stu-id="d7e67-104">The ASP.NET Core :::no-loc(SignalR)::: .NET client library lets you communicate with :::no-loc(SignalR)::: hubs from .NET apps.</span></span>

<span data-ttu-id="d7e67-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d7e67-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="d7e67-106">Das Codebeispiel in diesem Artikel ist eine WPF-App, die den ASP.net Core :::no-loc(SignalR)::: .NET-Client verwendet.</span><span class="sxs-lookup"><span data-stu-id="d7e67-106">The code sample in this article is a WPF app that uses the ASP.NET Core :::no-loc(SignalR)::: .NET client.</span></span>

## <a name="install-the-no-locsignalr-net-client-package"></a><span data-ttu-id="d7e67-107">Installieren des :::no-loc(SignalR)::: .NET-Client Pakets</span><span class="sxs-lookup"><span data-stu-id="d7e67-107">Install the :::no-loc(SignalR)::: .NET client package</span></span>

<span data-ttu-id="d7e67-108">[Microsoft. aspnetcore. :::no-loc(SignalR)::: . Das Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) Paket ist für .NET-Clients erforderlich, um eine Verbindung mit :::no-loc(SignalR)::: Hubs herzustellen.</span><span class="sxs-lookup"><span data-stu-id="d7e67-108">The [Microsoft.AspNetCore.:::no-loc(SignalR):::.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package is required for .NET clients to connect to :::no-loc(SignalR)::: hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d7e67-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d7e67-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d7e67-110">Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl im Fenster **Paket-Manager-Konsole** aus:</span><span class="sxs-lookup"><span data-stu-id="d7e67-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d7e67-111">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="d7e67-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d7e67-112">Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="d7e67-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="d7e67-113">Herstellen einer Verbindung mit einem Hub</span><span class="sxs-lookup"><span data-stu-id="d7e67-113">Connect to a hub</span></span>

<span data-ttu-id="d7e67-114">Erstellen Sie einen, und rufen Sie auf, um eine Verbindung herzustellen `HubConnectionBuilder` `Build` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="d7e67-115">Die Hub-URL, das Protokoll, der Transporttyp, die Protokollebene, Header und andere Optionen können beim Herstellen einer Verbindung konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="d7e67-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="d7e67-116">Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden in einfügen `Build` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="d7e67-117">Starten Sie die Verbindung mit `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="d7e67-118">Handle für verlorene Verbindung</span><span class="sxs-lookup"><span data-stu-id="d7e67-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="d7e67-119">Automatisch wiederherstellen der Verbindung</span><span class="sxs-lookup"><span data-stu-id="d7e67-119">Automatically reconnect</span></span>

<span data-ttu-id="d7e67-120">Der <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection> kann so konfiguriert werden, dass mithilfe der-Methode in automatisch erneut eine Verbindung hergestellt wird `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d7e67-120">The <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="d7e67-121">Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="d7e67-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="d7e67-122">Ohne Parameter `WithAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="d7e67-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="d7e67-123">Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand, und das- `Reconnecting` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="d7e67-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="d7e67-124">Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="d7e67-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="d7e67-125">Nicht interaktive Apps können Nachrichten in die Warteschlange einreihen oder löschen.</span><span class="sxs-lookup"><span data-stu-id="d7e67-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="d7e67-126">Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst das- `Reconnected` Ereignis aus.</span><span class="sxs-lookup"><span data-stu-id="d7e67-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="d7e67-127">Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde, und alle Nachrichten in der Warteschlange aus der Warteschlange entfernt</span><span class="sxs-lookup"><span data-stu-id="d7e67-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="d7e67-128">Da die Verbindung mit dem Server vollständig neu ist, `ConnectionId` wird den Ereignis Handlern eine neue bereitgestellt `Reconnected` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="d7e67-129">Der `Reconnected` -Parameter des Ereignis Handlers ist `connectionId` NULL, wenn der `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="d7e67-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="d7e67-130">`WithAutomaticReconnect()` konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:</span><span class="sxs-lookup"><span data-stu-id="d7e67-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="d7e67-131">Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Status, und das-Ereignis wird ausgelöst <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection.Closed> .</span><span class="sxs-lookup"><span data-stu-id="d7e67-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="d7e67-132">Auf diese Weise können Sie versuchen, die Verbindung manuell neu zu starten oder den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist.</span><span class="sxs-lookup"><span data-stu-id="d7e67-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="d7e67-133">Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `WithAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="d7e67-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="d7e67-134">Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen.</span><span class="sxs-lookup"><span data-stu-id="d7e67-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="d7e67-135">Dies gilt auch für die Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d7e67-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="d7e67-136">Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d7e67-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="d7e67-137">Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="d7e67-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="d7e67-138">Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch der Verbindung angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="d7e67-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="d7e67-139">In der Standardkonfiguration gibt es einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="d7e67-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="d7e67-140">Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `WithAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="d7e67-141">`NextRetryDelay` nimmt ein einzelnes Argument mit dem Typ an `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="d7e67-142">`RetryContext`Verfügt über drei Eigenschaften: `PreviousRetryCount` , `ElapsedTime` und `RetryReason` , wobei es sich um ein `long` -, ein `TimeSpan` -und ein-Wert handelt `Exception` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="d7e67-143">Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `PreviousRetryCount` als auch `ElapsedTime` 0 (null) und die Ausnahme, die bewirkt hat, `RetryReason` dass die Verbindung unterbrochen wurde.</span><span class="sxs-lookup"><span data-stu-id="d7e67-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="d7e67-144">Nach jedem fehlgeschlagenen Wiederholungsversuch `PreviousRetryCount` wird um eins erhöht, `ElapsedTime` wird aktualisiert, um die Zeitspanne für eine erneute Verbindungs Herstellung widerzuspiegeln, und die Ausnahme, die bewirkt hat, `RetryReason` dass der letzte Wiederholungsversuch fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="d7e67-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="d7e67-145">`NextRetryDelay` muss entweder einen TimeSpan-Wert zurückgeben, der die Zeit angibt, die gewartet werden soll, bevor der nächste Verbindungsversuch wiederholt `null` wird `HubConnection` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="d7e67-146">Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.</span><span class="sxs-lookup"><span data-stu-id="d7e67-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="d7e67-147">Verbindung manuell wiederherstellen</span><span class="sxs-lookup"><span data-stu-id="d7e67-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="d7e67-148">Vor 3,0 wird die Verbindung des .NET-Clients für :::no-loc(SignalR)::: nicht automatisch wieder hergestellt.</span><span class="sxs-lookup"><span data-stu-id="d7e67-148">Prior to 3.0, the .NET client for :::no-loc(SignalR)::: doesn't automatically reconnect.</span></span> <span data-ttu-id="d7e67-149">Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.</span><span class="sxs-lookup"><span data-stu-id="d7e67-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="d7e67-150">Verwenden Sie das- <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection.Closed> Ereignis, um auf eine verlorene Verbindung zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="d7e67-150">Use the <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="d7e67-151">Beispielsweise möchten Sie möglicherweise die erneute Verbindung automatisieren.</span><span class="sxs-lookup"><span data-stu-id="d7e67-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="d7e67-152">Das-Ereignis erfordert einen Delegaten, der `Closed` einen zurückgibt, mit dem asynchroner `Task` Code ohne Verwendung von ausgeführt werden kann `async void` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="d7e67-153">Um die Delegatsignatur in einem Ereignishandler zu erfüllen, der `Closed` synchron ausgeführt wird, geben Sie Folgendes zurück `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="d7e67-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="d7e67-154">Der Hauptgrund für die Async-Unterstützung ist, dass Sie die Verbindung neu starten können.</span><span class="sxs-lookup"><span data-stu-id="d7e67-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="d7e67-155">Das Starten einer Verbindung ist eine asynchrone Aktion.</span><span class="sxs-lookup"><span data-stu-id="d7e67-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="d7e67-156">In einem `Closed` Handler, der die Verbindung neu startet, sollten Sie auf eine zufällige Verzögerung warten, um das Überladen des Servers zu verhindern, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="d7e67-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d7e67-157">Hub-Methoden vom Client abrufen</span><span class="sxs-lookup"><span data-stu-id="d7e67-157">Call hub methods from client</span></span>

<span data-ttu-id="d7e67-158">`InvokeAsync` Ruft Methoden für den Hub auf.</span><span class="sxs-lookup"><span data-stu-id="d7e67-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="d7e67-159">Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="d7e67-160">:::no-loc(SignalR)::: ist asynchron, verwenden Sie also `async` und, `await` Wenn Sie die Aufrufe ausführen.</span><span class="sxs-lookup"><span data-stu-id="d7e67-160">:::no-loc(SignalR)::: is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="d7e67-161">Die `InvokeAsync` Methode gibt einen zurück `Task` , der abgeschlossen wird, wenn die Server Methode zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="d7e67-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="d7e67-162">Der Rückgabewert wird ggf. als Ergebnis von bereitgestellt `Task` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="d7e67-163">Alle Ausnahmen, die von der-Methode auf dem Server ausgelöst werden, führen zu einem Fehler `Task` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="d7e67-164">Verwenden `await` Sie die-Syntax, um auf den Abschluss der Server Methode und die `try...catch` Syntax zur Fehlerbehandlung zu warten.</span><span class="sxs-lookup"><span data-stu-id="d7e67-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="d7e67-165">Die `SendAsync` Methode gibt einen zurück, der abgeschlossen wird, `Task` Wenn die Nachricht an den Server gesendet wurde.</span><span class="sxs-lookup"><span data-stu-id="d7e67-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="d7e67-166">Es wird kein Rückgabewert bereitgestellt, da dieser `Task` nicht wartet, bis die Server Methode abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="d7e67-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="d7e67-167">Alle Ausnahmen, die beim Senden der Nachricht auf dem Client ausgelöst werden, führen zu einem Fehler `Task` .</span><span class="sxs-lookup"><span data-stu-id="d7e67-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="d7e67-168">Verwenden `await` `try...catch` Sie die Syntax und, um Sende Fehler zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="d7e67-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="d7e67-169">Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- :::no-loc(SignalR)::: Dienst im *Standard* Modus verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d7e67-169">Calling hub methods from a client is only supported when using the Azure :::no-loc(SignalR)::: Service in *Default* mode.</span></span> <span data-ttu-id="d7e67-170">Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span><span class="sxs-lookup"><span data-stu-id="d7e67-170">For more information, see [Frequently Asked Questions (azure-signalr GitHub repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d7e67-171">Client Methoden aus Hub abrufen</span><span class="sxs-lookup"><span data-stu-id="d7e67-171">Call client methods from hub</span></span>

<span data-ttu-id="d7e67-172">Definieren von Methoden, die der Hub mithilfe von `connection.On` nach dem Aufbau aufruft, jedoch vor dem Starten der Verbindung.</span><span class="sxs-lookup"><span data-stu-id="d7e67-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="d7e67-173">Der vorangehende Code in wird ausgeführt, `connection.On` Wenn der serverseitige Code ihn mit der- `SendAsync` Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="d7e67-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="d7e67-174">Fehlerbehandlung und Protokollierung</span><span class="sxs-lookup"><span data-stu-id="d7e67-174">Error handling and logging</span></span>

<span data-ttu-id="d7e67-175">Behandeln Sie Fehler mit einer try-catch-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="d7e67-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="d7e67-176">Überprüfen `Exception` Sie das Objekt, um die ordnungsgemäße Aktion zu bestimmen, die nach einem Fehler ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="d7e67-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="d7e67-177">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d7e67-177">Additional resources</span></span>

* [<span data-ttu-id="d7e67-178">Hubs</span><span class="sxs-lookup"><span data-stu-id="d7e67-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="d7e67-179">JavaScript-Client</span><span class="sxs-lookup"><span data-stu-id="d7e67-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="d7e67-180">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="d7e67-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="d7e67-181">Azure- :::no-loc(SignalR)::: Dienst Server lose Dokumentation</span><span class="sxs-lookup"><span data-stu-id="d7e67-181">Azure :::no-loc(SignalR)::: Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
