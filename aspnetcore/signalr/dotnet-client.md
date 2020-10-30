---
title: ASP.net Core SignalR .NET-Client
author: bradygaster
description: Informationen zum ASP.net Core SignalR .NET-Client
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
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
uid: signalr/dotnet-client
ms.openlocfilehash: 7849322e50aeceda0850d94f9c9bcda79a6051c8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061222"
---
# <a name="aspnet-core-no-locsignalr-net-client"></a>ASP.net Core SignalR .NET-Client

Mit der ASP.net Core SignalR .NET-Client Bibliothek können Sie über SignalR .net-apps mit Hubs kommunizieren.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Codebeispiel in diesem Artikel ist eine WPF-App, die den ASP.net Core SignalR .NET-Client verwendet.

## <a name="install-the-no-locsignalr-net-client-package"></a>Installieren des SignalR .NET-Client Pakets

[Microsoft. aspnetcore. SignalR . Das Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) Paket ist für .NET-Clients erforderlich, um eine Verbindung mit SignalR Hubs herzustellen.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl im Fenster **Paket-Manager-Konsole** aus:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Um die Client Bibliothek zu installieren, führen Sie den folgenden Befehl in einer Befehlsshell aus:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Erstellen Sie einen, und rufen Sie auf, um eine Verbindung herzustellen `HubConnectionBuilder` `Build` . Die Hub-URL, das Protokoll, der Transporttyp, die Protokollebene, Header und andere Optionen können beim Herstellen einer Verbindung konfiguriert werden. Konfigurieren Sie alle erforderlichen Optionen, indem Sie eine der `HubConnectionBuilder` Methoden in einfügen `Build` . Starten Sie die Verbindung mit `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Handle für verlorene Verbindung

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Automatisch wiederherstellen der Verbindung

Der <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> kann so konfiguriert werden, dass mithilfe der-Methode in automatisch erneut eine Verbindung hergestellt wird `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Ohne Parameter `WithAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.

Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand, und das- `Reconnecting` Ereignis wird ausgelöst.  Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren. Nicht interaktive Apps können Nachrichten in die Warteschlange einreihen oder löschen.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst das- `Reconnected` Ereignis aus. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde, und alle Nachrichten in der Warteschlange aus der Warteschlange entfernt

Da die Verbindung mit dem Server vollständig neu ist, `ConnectionId` wird den Ereignis Handlern eine neue bereitgestellt `Reconnected` .

> [!WARNING]
> Der `Reconnected` -Parameter des Ereignis Handlers ist `connectionId` NULL, wenn der `HubConnection` zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()` konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:

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

Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Status, und das-Ereignis wird ausgelöst <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> . Auf diese Weise können Sie versuchen, die Verbindung manuell neu zu starten oder den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `WithAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.

Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch der Verbindung angehalten wird. In der Standardkonfiguration gibt es einen weiteren erneuten Verbindungsversuch in weiteren 30 Sekunden.

Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `WithAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `NextRetryDelay` .

`NextRetryDelay` nimmt ein einzelnes Argument mit dem Typ an `RetryContext` . `RetryContext`Verfügt über drei Eigenschaften: `PreviousRetryCount` , `ElapsedTime` und `RetryReason` , wobei es sich um ein `long` -, ein `TimeSpan` -und ein-Wert handelt `Exception` . Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `PreviousRetryCount` als auch `ElapsedTime` 0 (null) und die Ausnahme, die bewirkt hat, `RetryReason` dass die Verbindung unterbrochen wurde. Nach jedem fehlgeschlagenen Wiederholungsversuch `PreviousRetryCount` wird um eins erhöht, `ElapsedTime` wird aktualisiert, um die Zeitspanne für eine erneute Verbindungs Herstellung widerzuspiegeln, und die Ausnahme, die bewirkt hat, `RetryReason` dass der letzte Wiederholungsversuch fehlgeschlagen ist.

`NextRetryDelay` muss entweder einen TimeSpan-Wert zurückgeben, der die Zeit angibt, die gewartet werden soll, bevor der nächste Verbindungsversuch wiederholt `null` wird `HubConnection` .

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

Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.

::: moniker-end

### <a name="manually-reconnect"></a>Verbindung manuell wiederherstellen

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> Vor 3,0 wird die Verbindung des .NET-Clients für SignalR nicht automatisch wieder hergestellt. Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.

::: moniker-end

Verwenden Sie das- <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> Ereignis, um auf eine verlorene Verbindung zu reagieren. Beispielsweise möchten Sie möglicherweise die erneute Verbindung automatisieren.

Das-Ereignis erfordert einen Delegaten, der `Closed` einen zurückgibt, mit dem asynchroner `Task` Code ohne Verwendung von ausgeführt werden kann `async void` . Um die Delegatsignatur in einem Ereignishandler zu erfüllen, der `Closed` synchron ausgeführt wird, geben Sie Folgendes zurück `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Der Hauptgrund für die Async-Unterstützung ist, dass Sie die Verbindung neu starten können. Das Starten einer Verbindung ist eine asynchrone Aktion.

In einem `Closed` Handler, der die Verbindung neu startet, sollten Sie auf eine zufällige Verzögerung warten, um das Überladen des Servers zu verhindern, wie im folgenden Beispiel gezeigt:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>Hub-Methoden vom Client abrufen

`InvokeAsync` Ruft Methoden für den Hub auf. Übergeben Sie den Namen der Hub-Methode und alle in der Hub-Methode definierten Argumente an `InvokeAsync` . SignalR ist asynchron, verwenden Sie also `async` und, `await` Wenn Sie die Aufrufe ausführen.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

Die `InvokeAsync` Methode gibt einen zurück `Task` , der abgeschlossen wird, wenn die Server Methode zurückgibt. Der Rückgabewert wird ggf. als Ergebnis von bereitgestellt `Task` . Alle Ausnahmen, die von der-Methode auf dem Server ausgelöst werden, führen zu einem Fehler `Task` . Verwenden `await` Sie die-Syntax, um auf den Abschluss der Server Methode und die `try...catch` Syntax zur Fehlerbehandlung zu warten.

Die `SendAsync` Methode gibt einen zurück, der abgeschlossen wird, `Task` Wenn die Nachricht an den Server gesendet wurde. Es wird kein Rückgabewert bereitgestellt, da dieser `Task` nicht wartet, bis die Server Methode abgeschlossen ist. Alle Ausnahmen, die beim Senden der Nachricht auf dem Client ausgelöst werden, führen zu einem Fehler `Task` . Verwenden `await` `try...catch` Sie die Syntax und, um Sende Fehler zu behandeln.

> [!NOTE]
> Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird. Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

## <a name="call-client-methods-from-hub"></a>Client Methoden aus Hub abrufen

Definieren von Methoden, die der Hub mithilfe von `connection.On` nach dem Aufbau aufruft, jedoch vor dem Starten der Verbindung.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Der vorangehende Code in wird ausgeführt, `connection.On` Wenn der serverseitige Code ihn mit der- `SendAsync` Methode aufruft.

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Behandeln Sie Fehler mit einer try-catch-Anweisung. Überprüfen `Exception` Sie das Objekt, um die ordnungsgemäße Aktion zu bestimmen, die nach einem Fehler ausgeführt werden soll.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Azure- SignalR Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)
