---
title: ASP.net Core SignalR JavaScript-Client
author: bradygaster
description: Übersicht über ASP.net Core SignalR JavaScript-Client.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 04/08/2020
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
uid: signalr/javascript-client
ms.openlocfilehash: be271272c952487fccc5136307c84fdf49391848
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690646"
---
# <a name="aspnet-core-no-locsignalr-javascript-client"></a>ASP.net Core SignalR JavaScript-Client

::: moniker range=">= aspnetcore-3.0"

Von [Rachel Appel](https://twitter.com/rachelappel)

Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-client-package"></a>Installieren des SignalR Client Pakets

Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt. In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.

### <a name="install-with-npm"></a>Installieren mit NPM

Führen Sie in Visual Studio die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus. Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal** aus.

```bash
npm init -y
npm install @microsoft/signalr
```

NPM installiert den Paket Inhalt im Ordner *" \\ @microsoft\signalr\dist\browser node_modules* ". Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* . Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".

Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element. Beispiel:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Verwenden eines Content Delivery Network (CDN)

Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek. Beispiel:

[!code-html[](javascript-client/samples/3.x/SignalRChat/Pages/Index.cshtml?name=snippet_CDN)]

Die Client Bibliothek ist unter den folgenden CDNs verfügbar:

* [cdnjs verfügbar](https://cdnjs.com/libraries/microsoft-signalr)
* [jsdelivr](https://www.jsdelivr.com/package/npm/@microsoft/signalr)
* [unpkg](https://unpkg.com/@microsoft/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>Installieren mit Libman

[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren. Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu. Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Mit dem folgenden Code wird eine Verbindung erstellt und gestartet. Der Name des Hubs wird nicht beachtet.

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=3-6,29-43)]

### <a name="cross-origin-connections"></a>Ursprungs übergreifende Verbindungen

Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite. Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.

Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert. Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse:

[!code-csharp[](javascript-client/samples/3.x/SignalRChat/Startup.cs?highlight=16-23,40)]

## <a name="call-hub-methods-from-the-client"></a>Callhub-Methoden vom Client

JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40microsoft/signalr/hubconnection#invoke-string--any---) von [hubconnection](/javascript/api/%40microsoft/signalr/hubconnection)auf. Die- `invoke` Methode akzeptiert Folgendes:

* Der Name der Hub-Methode.
* Alle Argumente, die in der Hub-Methode definiert sind.

Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` . Das zweite und dritte Argument, das an `invoke` das-Argument und das-Argument der Hub-Methode übermittelt wird `user` `message` :

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=2)]

> [!NOTE]
> Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird. Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück. Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt. Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.

JavaScript-Clients können auch öffentliche Methoden auf Hubs über die [Send](/javascript/api/%40microsoft/signalr/hubconnection#send-string--any---) -Methode von aufzurufen `HubConnection` . Anders als bei der- `invoke` Methode wartet die- `send` Methode nicht auf eine Antwort vom Server. Die- `send` Methode gibt ein JavaScript zurück `Promise` . Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde. Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden `async` `await` Sie und oder die `Promise` - `then` Methode und die- `catch` Methode, um diese Fälle zu behandeln.

> [!NOTE]
> Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat. Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.

## <a name="call-client-methods-from-the-hub"></a>Client Methoden aus dem Hub abrufen

Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40microsoft/signalr/hubconnection#on-string---args--any-------void-) -Methode von eine Methode `HubConnection` .

* Der Name der JavaScript-Client Methode.
* Argumente, die der Hub an die Methode übergibt.

Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` . Die Argument Namen lauten `user` und `message` :

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_ReceiveMessage)]

Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft:

[!code-csharp[Call client-side](javascript-client/samples/3.x/SignalRChat/Hubs/ChatHub.cs?name=snippet_SendMessage)]

SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .

> [!NOTE]
> Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` . Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Verwenden `try` `catch` Sie und mit `async` und `await` oder der `Promise` -Methode von `catch` , um Client seitige Fehler zu behandeln. Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Invoke&highlight=1,3-5)]

Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird. Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert. Folgende Protokoll Ebenen sind verfügbar:

* `signalR.LogLevel.Error`: Fehlermeldungen. Protokolliert `Error` nur Meldungen.
* `signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern. Protokolle `Warning` und `Error` Nachrichten.
* `signalR.LogLevel.Information`: Status Meldungen ohne Fehler. Protokolliert `Information` die `Warning` Meldungen, und `Error` .
* `signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen. Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.

Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren. Nachrichten werden in der Browser Konsole protokolliert:

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?name=snippet_Connection&highlight=3)]

## <a name="reconnect-clients"></a>Clients erneut verbinden

### <a name="automatically-reconnect"></a>Automatisch wiederherstellen der Verbindung

Der JavaScript-Client für SignalR kann so konfiguriert werden, dass er mithilfe der- `withAutomaticReconnect` Methode auf [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder)automatisch erneut eine Verbindung herstellt. Standardmäßig wird die Verbindung nicht automatisch wieder hergestellt.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

Ohne Parameter `withAutomaticReconnect()` konfiguriert den Client so, dass 0, 2, 10 und 30 Sekunden gewartet wird, bevor versucht wird, jeden erneuten Verbindungsversuch durchzusetzen, der nach vier fehlgeschlagenen Versuchen angehalten wird.

Vor dem Starten von Wiederholungs versuchen wechselt der `HubConnection` in den `HubConnectionState.Reconnecting` -Zustand und löst seine Rückrufe `onreconnecting` aus, anstatt in den `Disconnected` -Zustand zu wechseln und seine Rückrufe wie eine auszulösen, `onclose` ohne dass die `HubConnection` Automatische Verbindungs Wiederherstellung konfiguriert wurde. Dies bietet die Möglichkeit, die Benutzer zu warnen, dass die Verbindung unterbrochen wurde, und Benutzeroberflächen Elemente zu deaktivieren.

```javascript
connection.onreconnecting(error => {
    console.assert(connection.state === signalR.HubConnectionState.Reconnecting);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection lost due to error "${error}". Reconnecting.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Wenn der Client innerhalb der ersten vier Versuche erfolgreich erneut eine Verbindung herstellt, wechselt `HubConnection` zurück in den `Connected` -Zustand und löst seine `onreconnected` Rückrufe aus. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung wieder hergestellt wurde.

Da die Verbindung mit dem Server vollständig neu ist, `connectionId` wird dem Rückruf ein neuer bereitgestellt `onreconnected` .

> [!WARNING]
> Der `onreconnected` -Parameter des Rückrufs ist nicht `connectionId` definiert `HubConnection` , wenn der zum über [springen der Aushandlung](xref:signalr/configuration#configure-client-options)konfiguriert wurde.

```javascript
connection.onreconnected(connectionId => {
    console.assert(connection.state === signalR.HubConnectionState.Connected);

    document.getElementById("messageInput").disabled = false;

    const li = document.createElement("li");
    li.textContent = `Connection reestablished. Connected with connectionId "${connectionId}".`;
    document.getElementById("messagesList").appendChild(li);
});
```

`withAutomaticReconnect()` konfiguriert nicht `HubConnection` , um anfängliche Start Fehler zu wiederholen, sodass Start Fehler manuell behandelt werden müssen:

```javascript
async function start() {
    try {
        await connection.start();
        console.assert(connection.state === signalR.HubConnectionState.Connected);
        console.log("SignalR Connected.");
    } catch (err) {
        console.assert(connection.state === signalR.HubConnectionState.Disconnected);
        console.log(err);
        setTimeout(() => start(), 5000);
    }
};
```

Wenn der Client nicht innerhalb der ersten vier Versuche erneut eine Verbindung herstellt, wechselt `HubConnection` in den `Disconnected` -Zustand und auslöst seine [OnClose](/javascript/api/%40aspnet/signalr/hubconnection#onclose) -Rückrufe. Dies bietet die Möglichkeit, den Benutzern mitzuteilen, dass die Verbindung dauerhaft verloren gegangen ist, und es wird empfohlen, die Seite zu aktualisieren:

```javascript
connection.onclose(error => {
    console.assert(connection.state === signalR.HubConnectionState.Disconnected);

    document.getElementById("messageInput").disabled = true;

    const li = document.createElement("li");
    li.textContent = `Connection closed due to error "${error}". Try refreshing this page to restart the connection.`;
    document.getElementById("messagesList").appendChild(li);
});
```

Um eine benutzerdefinierte Anzahl von Wiederholungs versuchen zu konfigurieren, bevor die Verbindung getrennt wird, oder um die zeitliche Verbindung wiederherzustellen, `withAutomaticReconnect` akzeptiert ein Array von Zahlen, das die Verzögerung in Millisekunden angibt, die gewartet werden soll, bevor die einzelnen Wiederholungs Versuche gestartet werden.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect([0, 0, 10000])
    .build();

    // .withAutomaticReconnect([0, 2000, 10000, 30000]) yields the default behavior
```

Im vorangehenden Beispiel wird der so konfiguriert `HubConnection` , dass nach dem Verlust der Verbindung erneut versucht wird, die Verbindung herzustellen. Dies gilt auch für die Standardkonfiguration.

Wenn der erste Wiederholungsversuch fehlschlägt, wird der zweite Wiederholungsversuch auch sofort gestartet, anstatt zwei Sekunden zu warten, wie in der Standardkonfiguration.

Wenn der zweite Wiederholungsversuch fehlschlägt, wird der dritte Versuch der erneuten Verbindungs Herstellung in 10 Sekunden gestartet. Dies entspricht der Standardkonfiguration.

Das benutzerdefinierte Verhalten weicht dann erneut vom Standardverhalten ab, indem nach dem dritten Wiederholungsversuch beendet wird, anstatt einen weiteren Wiederholungsversuch in weiteren 30 Sekunden auszuführen, wie in der Standardkonfiguration.

Wenn Sie eine noch größere Kontrolle über die zeitliche Steuerung und die Anzahl der automatischen Wiederholungs Versuche wünschen, `withAutomaticReconnect` akzeptiert ein Objekt, das die- `IRetryPolicy` Schnittstelle implementiert, die über eine einzige Methode mit dem Namen verfügt `nextRetryDelayInMilliseconds` .

`nextRetryDelayInMilliseconds` nimmt ein einzelnes Argument mit dem Typ an `RetryContext` . `RetryContext`Verfügt über drei Eigenschaften: `previousRetryCount` , `elapsedMilliseconds` und `retryReason` die sind `number` , und `number` `Error` . Vor dem ersten Versuch, die Verbindung wiederherzustellen, sind sowohl `previousRetryCount` als auch `elapsedMilliseconds` NULL, und der Fehler, der bewirkt hat, `retryReason` dass die Verbindung unterbrochen wurde. Nach jedem fehlgeschlagenen Wiederholungsversuch `previousRetryCount` wird der Wert um eins erhöht `elapsedMilliseconds` . der Wert wird aktualisiert, um die Zeitspanne in Millisekunden wiederherzustellen, die bis zum letzten Verbindungsversuch aufgewendet wurde. Dies ist der Fehler, der `retryReason` zum Fehlschlagen des letzten Wiederholungs Versuchs geführt hat.

`nextRetryDelayInMilliseconds` muss entweder eine Zahl zurückgeben, die die Anzahl der Millisekunden angibt, die vor dem nächsten erneuten Verbindungsversuch gewartet werden soll, oder, `null` Wenn die `HubConnection` Verbindung nicht mehr herstellen soll.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect({
        nextRetryDelayInMilliseconds: retryContext => {
            if (retryContext.elapsedMilliseconds < 60000) {
                // If we've been reconnecting for less than 60 seconds so far,
                // wait between 0 and 10 seconds before the next reconnect attempt.
                return Math.random() * 10000;
            } else {
                // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
                return null;
            }
        }
    })
    .build();
```

Alternativ dazu können Sie auch Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt, wie in [Manuelles Wiederherstellen der Verbindung](#manually-reconnect)gezeigt.

### <a name="manually-reconnect"></a>Verbindung manuell wiederherstellen

Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:

1. Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.
1. Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .

[!code-javascript[](javascript-client/samples/3.x/SignalRChat/wwwroot/chat.js?range=30-40)]

Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest&preserve-view=true )
* [JavaScript-Tutorial](xref:tutorials/signalr)
* [WebPack und typescript-Tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Cross-Origin-Anforderungen (cors)](xref:security/cors)
* [Azure- SignalR Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)
* [Beheben von Verbindungsproblemen](xref:signalr/troubleshoot)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rachel Appel](https://twitter.com/rachelappel)

Die ASP.net Core SignalR JavaScript-Client Bibliothek ermöglicht es Entwicklern, serverseitigen Hub-Code aufzurufen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/javascript-client/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="install-the-no-locsignalr-client-package"></a>Installieren des SignalR Client Pakets

Die SignalR JavaScript-Client Bibliothek wird als [NPM](https://www.npmjs.com/) -Paket bereitgestellt. In den folgenden Abschnitten werden verschiedene Möglichkeiten zum Installieren der-Client Bibliothek erläutert.

### <a name="install-with-npm"></a>Installieren mit NPM

Wenn Sie Visual Studio verwenden, führen Sie die folgenden Befehle über die **Paket-Manager-Konsole** im Stamm Ordner aus. Führen Sie für Visual Studio Code die folgenden Befehle über das **integrierte Terminal** aus.

```bash
npm init -y
npm install @aspnet/signalr
```

NPM installiert den Paket Inhalt im Ordner *" \\ @aspnet\signalr\dist\browser node_modules* ". Erstellen Sie einen neuen Ordner mit dem Namen *signalr* unter dem Ordner *wwwroot \\ lib* . Kopieren Sie die *signalr.js* Datei in den Ordner " *wwwroot\lib\signalr* ".

Verweisen Sie SignalR auf den JavaScript-Client im- `<script>` Element. Beispiel:

```html
<script src="~/lib/signalr/signalr.js"></script>
```

### <a name="use-a-content-delivery-network-cdn"></a>Verwenden eines Content Delivery Network (CDN)

Wenn Sie die Client Bibliothek ohne die erforderliche Komponente für NPM verwenden möchten, verweisen Sie auf eine CDN-gehostete Kopie der Client Bibliothek. Beispiel:

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/microsoft-signalr/3.1.3/signalr.min.js"></script>
```

Die Client Bibliothek ist unter den folgenden CDNs verfügbar:

* [cdnjs verfügbar](https://cdnjs.com/libraries/aspnet-signalr)
* [jsdelivr](https://www.jsdelivr.com/package/npm/@aspnet/signalr)
* [unpkg](https://unpkg.com/@aspnet/signalr@next/dist/browser/signalr.min.js)

### <a name="install-with-libman"></a>Installieren mit Libman

[Libman](xref:client-side/libman/index) kann verwendet werden, um bestimmte Client Bibliotheksdateien aus der CDN-gehosteten Client Bibliothek zu installieren. Fügen Sie z. b. nur die minierte JavaScript-Datei zum Projekt hinzu. Weitere Informationen zu diesem Ansatz finden [Sie unter Hinzufügen der SignalR Client Bibliothek](xref:tutorials/signalr#add-the-signalr-client-library).

## <a name="connect-to-a-hub"></a>Herstellen einer Verbindung mit einem Hub

Mit dem folgenden Code wird eine Verbindung erstellt und gestartet. Der Name des Hubs wird nicht beachtet.

[!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-13,28-51)]

### <a name="cross-origin-connections"></a>Ursprungs übergreifende Verbindungen

Normalerweise laden Browser Verbindungen aus derselben Domäne wie die angeforderte Seite. Es gibt jedoch Situationen, in denen eine Verbindung mit einer anderen Domäne erforderlich ist.

Um zu verhindern, dass ein böswilliger Standort vertrauliche Daten von einem anderen Standort liest, sind [Ursprungs übergreifende Verbindungen](xref:security/cors) standardmäßig deaktiviert. Um eine Ursprungs übergreifende Anforderung zuzulassen, aktivieren Sie Sie in der- `Startup` Klasse.

[!code-csharp[Cross-origin connections](javascript-client/samples/2.x/SignalRChat/Startup.cs?highlight=29-35,56)]

## <a name="call-hub-methods-from-client"></a>Hub-Methoden vom Client abrufen

JavaScript-Clients rufen öffentliche Methoden für Hubs über die [Aufruf Methode](/javascript/api/%40aspnet/signalr/hubconnection#invoke) von [hubconnection](/javascript/api/%40aspnet/signalr/hubconnection)auf. Die- `invoke` Methode akzeptiert zwei Argumente:

* Der Name der Hub-Methode. Im folgenden Beispiel ist der Methodenname auf dem Hub `SendMessage` .
* Alle Argumente, die in der Hub-Methode definiert sind. Im folgenden Beispiel lautet der Argument Name `message` . Im Beispielcode wird eine Pfeil Funktions Syntax verwendet, die in den aktuellen Versionen aller wichtigen Browser mit Ausnahme von Internet Explorer unterstützt wird.

  [!code-javascript[Call hub methods](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=24)]

> [!NOTE]
> Das Aufrufen von Hub-Methoden von einem Client wird nur unterstützt, wenn der Azure- SignalR Dienst im *Standard* Modus verwendet wird. Weitere Informationen finden Sie unter [häufig gestellte Fragen (Azure-signalr-GitHub-Repository)](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose).

Die- `invoke` Methode gibt eine JavaScript- [Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)zurück. Die `Promise` wird mit dem Rückgabewert (sofern vorhanden) aufgelöst, wenn die-Methode auf dem Server zurückgibt. Wenn die-Methode auf dem Server einen Fehler auslöst, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.

Die- `send` Methode gibt ein JavaScript zurück `Promise` . Der `Promise` wird aufgelöst, wenn die Nachricht an den Server gesendet wurde. Wenn beim Senden der Nachricht ein Fehler auftritt, `Promise` wird mit der Fehlermeldung zurückgewiesen. Verwenden `then` Sie die- `catch` Methode und die-Methode für den `Promise` selbst, um diese Fälle (oder `await` Syntax) zu verarbeiten.

> [!NOTE]
> Die Verwendung von `send` wartet nicht, bis der Server die Nachricht empfangen hat. Folglich ist es nicht möglich, Daten oder Fehler vom Server zurückzugeben.

## <a name="call-client-methods-from-hub"></a>Client Methoden aus Hub abrufen

Um Nachrichten vom Hub zu empfangen, definieren Sie mithilfe der [on](/javascript/api/%40aspnet/signalr/hubconnection#on) -Methode von eine Methode `HubConnection` .

* Der Name der JavaScript-Client Methode. Im folgenden Beispiel lautet der Methodenname `ReceiveMessage` .
* Argumente, die der Hub an die Methode übergibt. Im folgenden Beispiel ist der Argument Wert `message` .

[!code-javascript[Receive calls from hub](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=14-19)]

Der vorangehende Code in wird ausgeführt, `connection.on` Wenn der serverseitige Code ihn mit der- <xref:Microsoft.AspNetCore.SignalR.ClientProxyExtensions.SendAsync%2A> Methode aufruft.

[!code-csharp[Call client-side](javascript-client/samples/2.x/SignalRChat/hubs/chathub.cs?range=8-11)]

SignalR bestimmt, welche Client Methode aufgerufen werden soll, indem der Methodenname und die in und definierten Argumente abgeglichen werden `SendAsync` `connection.on` .

> [!NOTE]
> Es wird empfohlen, die [Start](/javascript/api/%40aspnet/signalr/hubconnection#start) -Methode für den After-Befehl aufzurufen `HubConnection` `on` . Dadurch wird sichergestellt, dass ihre Handler registriert werden, bevor Nachrichten empfangen werden.

## <a name="error-handling-and-logging"></a>Fehlerbehandlung und Protokollierung

Verketten Sie eine `catch` Methode an das Ende der `start` Methode, um Client seitige Fehler zu behandeln. Verwenden `console.error` Sie, um Fehler an die Konsole des Browsers auszugeben.

[!code-javascript[Error handling](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=50)]

Richten Sie die Client seitige Protokoll Ablauf Verfolgung ein, indem Sie eine Protokollierung und einen Ereignistyp an die Protokollierung übergeben, wenn die Verbindung hergestellt wird. Nachrichten werden mit der angegebenen Protokollebene und höher protokolliert. Folgende Protokoll Ebenen sind verfügbar:

* `signalR.LogLevel.Error`: Fehlermeldungen. Protokolliert `Error` nur Meldungen.
* `signalR.LogLevel.Warning`: Warnmeldungen zu möglichen Fehlern. Protokolle `Warning` und `Error` Nachrichten.
* `signalR.LogLevel.Information`: Status Meldungen ohne Fehler. Protokolliert `Information` die `Warning` Meldungen, und `Error` .
* `signalR.LogLevel.Trace`: Ablauf Verfolgungs Meldungen. Protokolliert alles, einschließlich Daten, die zwischen dem Hub und dem Client transportiert werden.

Verwenden Sie die Methode configure [relogging](/javascript/api/%40aspnet/signalr/hubconnectionbuilder#configurelogging) für [hubconnectionbuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) , um die Protokollebene zu konfigurieren. Nachrichten werden in der Browser Konsole protokolliert.

[!code-javascript[Logging levels](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=9-12)]

## <a name="reconnect-clients"></a>Clients erneut verbinden

### <a name="manually-reconnect"></a>Verbindung manuell wiederherstellen

> [!WARNING]
> Vor 3,0 wird vom JavaScript-Client für SignalR nicht automatisch eine erneute Verbindung hergestellt. Sie müssen Code schreiben, mit dem der Client manuell erneut eine Verbindung herstellt.

Der folgende Code veranschaulicht einen typischen manuellen Verbindungs Ansatz:

1. Eine Funktion (in diesem Fall die `start` Funktion) wird erstellt, um die Verbindung zu starten.
1. Die `start` -Funktion wird im-Ereignishandler der Verbindung aufgerufen `onclose` .

[!code-javascript[Reconnect the JavaScript client](javascript-client/samples/2.x/SignalRChat/wwwroot/js/chat.js?range=28-40)]

Eine reale Implementierung würde einen exponentiellen Backoff verwenden oder eine angegebene Anzahl von Wiederholungen wiederholen, bevor Sie zurückgegeben wird.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [JavaScript-API-Referenz](/javascript/api/?view=signalr-js-latest)
* [JavaScript-Tutorial](xref:tutorials/signalr)
* [WebPack und typescript-Tutorial](xref:tutorials/signalr-typescript-webpack)
* [Hubs](xref:signalr/hubs)
* [.NET-Client](xref:signalr/dotnet-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Cross-Origin-Anforderungen (cors)](xref:security/cors)
* [Azure- SignalR Dienst Server lose Dokumentation](/azure/azure-signalr/signalr-concept-serverless-development-config)

::: moniker-end
