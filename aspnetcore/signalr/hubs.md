---
title: Verwenden von Hubs in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie Hubs in ASP.net Core verwenden SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
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
uid: signalr/hubs
ms.openlocfilehash: 4a31c16eb44e2244574d0df49c30e7a44b2bba6e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050939"
---
# <a name="use-hubs-in-no-locsignalr-for-aspnet-core"></a>Verwenden von Hubs in SignalR für ASP.net Core

Von [Rachel Appel](https://twitter.com/rachelappel) und [Kevin Griffin](https://twitter.com/1kevgriff)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubs/sample/ ) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="what-is-a-no-locsignalr-hub"></a>Was ist ein SignalR Hub?

Die SignalR Hubs-API ermöglicht es Ihnen, Methoden auf verbundenen Clients vom Server aus aufzurufen. Im Servercode definieren Sie Methoden, die vom Client aufgerufen werden. Im Client Code definieren Sie Methoden, die vom Server aufgerufen werden. SignalR kümmert sich um alles hinter den Kulissen, das die Kommunikation zwischen Client und Server und Server-zu-Client-Kommunikation in Echtzeit ermöglicht.

## <a name="configure-no-locsignalr-hubs"></a>SignalRHubs konfigurieren

Die SignalR Middleware erfordert einige Dienste, die durch den Aufruf von konfiguriert werden `services.AddSignalR` .

[!code-csharp[Configure service](hubs/sample/startup.cs?range=38)]

::: moniker range=">= aspnetcore-3.0"

Wenn SignalR Sie einer ASP.net Core-App Funktionen hinzufügen, richten Sie SignalR die Routen ein, indem Sie `endpoint.MapHub` im `Startup.Configure` Rückruf der Methode aufrufen `app.UseEndpoints` .

```csharp
app.UseRouting();
app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/chathub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Wenn SignalR Sie einer ASP.net Core-App Funktionen hinzufügen, richten Sie SignalR die Routen durch Aufrufen von `app.UseSignalR` in der- `Startup.Configure` Methode ein.

[!code-csharp[Configure routes to hubs](hubs/sample/startup.cs?range=57-60)]

::: moniker-end

## <a name="create-and-use-hubs"></a>Erstellen und Verwenden von Hubs

Erstellen Sie einen Hub, indem Sie eine Klasse deklarieren, die von erbt `Hub` , und Ihr öffentliche Methoden hinzufügen. Clients können Methoden aufzurufen, die als definiert sind `public` .

```csharp
public class ChatHub : Hub
{
    public Task SendMessage(string user, string message)
    {
        return Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

Sie können einen Rückgabetyp und Parameter, einschließlich komplexer Typen und Arrays, wie in jeder beliebigen c#-Methode angeben. SignalR verarbeitet die Serialisierung und Deserialisierung komplexer Objekte und Arrays in ihren Parametern und Rückgabe Werten.

> [!NOTE]
> Hubs sind flüchtig:
>
> * Speichern Sie den Zustand nicht in einer Eigenschaft der Hub-Klasse. Jeder Hub-Methodenaufrufe wird auf einer neuen Hub-Instanz ausgeführt.
> * Verwenden Sie `await` , wenn Sie asynchrone Methoden aufrufen, die vom Hub abhängen, der aktiv bleibt. Beispielsweise kann eine Methode wie z. b. `Clients.All.SendAsync(...)` fehlschlagen, wenn Sie ohne aufgerufen wird `await` und die Hub-Methode vor Abschluss abgeschlossen wird `SendAsync` .

## <a name="the-context-object"></a>Das Kontext Objekt

Die- `Hub` Klasse verfügt über eine- `Context` Eigenschaft, die die folgenden Eigenschaften mit Informationen über die Verbindung enthält:

| Eigenschaft | Beschreibung |
| ------ | ----------- |
| `ConnectionId` | Ruft die eindeutige ID für die Verbindung ab, die von zugewiesen wird SignalR . Es gibt eine Verbindungs-ID für jede Verbindung.|
| `UserIdentifier` | Ruft den [Benutzer Bezeichner](xref:signalr/groups)ab. Standardmäßig SignalR verwendet die `ClaimTypes.NameIdentifier` aus der, die `ClaimsPrincipal` der Verbindung zugeordnet ist, als Benutzer Bezeichner. |
| `User` | Ruft den ab, der `ClaimsPrincipal` dem aktuellen Benutzer zugeordnet ist. |
| `Items` | Ruft eine Schlüssel-/Wert-Auflistung ab, die verwendet werden kann, um Daten innerhalb des Gültigkeits Bereichs dieser Verbindung freizugeben. Die Daten können in dieser Sammlung gespeichert werden, und Sie werden für die Verbindung über verschiedene hubmethoden Aufrufe beibehalten. |
| `Features` | Ruft die Auflistung der Funktionen ab, die für die Verbindung verfügbar sind. Diese Sammlung wird in den meisten Szenarien nicht benötigt, sodass Sie noch nicht ausführlich dokumentiert wird. |
| `ConnectionAborted` | Ruft ein ab `CancellationToken` , das benachrichtigt, wenn die Verbindung abgebrochen wird. |

`Hub.Context` enthält auch die folgenden Methoden:

| Methode | Beschreibung |
| ------ | ----------- |
| `GetHttpContext` | Gibt den `HttpContext` für die Verbindung zurück, oder, `null` Wenn die Verbindung nicht mit einer HTTP-Anforderung verknüpft ist. Für http-Verbindungen können Sie diese Methode verwenden, um Informationen wie HTTP-Header und Abfrage Zeichenfolgen zu erhalten. |
| `Abort` | Bricht die Verbindung ab. |

## <a name="the-clients-object"></a>Das Client-Objekt

Die- `Hub` Klasse verfügt über eine- `Clients` Eigenschaft, die die folgenden Eigenschaften für die Kommunikation zwischen Server und Client enthält:

| Eigenschaft | Beschreibung |
| ------ | ----------- |
| `All` | Ruft eine Methode auf allen verbundenen Clients auf. |
| `Caller` | Ruft eine Methode auf dem Client auf, der die Hub-Methode aufgerufen hat. |
| `Others` | Ruft eine Methode auf allen verbundenen Clients mit Ausnahme des Clients auf, der die Methode aufgerufen hat. |

`Hub.Clients` enthält auch die folgenden Methoden:

| Methode | Beschreibung |
| ------ | ----------- |
| `AllExcept` | Ruft eine Methode auf allen verbundenen Clients mit Ausnahme der angegebenen Verbindungen auf. |
| `Client` | Ruft eine Methode auf einem bestimmten verbundenen Client auf. |
| `Clients` | Ruft eine Methode auf bestimmten verbundenen Clients auf. |
| `Group` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe auf.  |
| `GroupExcept` | Ruft eine Methode für alle Verbindungen in der angegebenen Gruppe mit Ausnahme der angegebenen Verbindungen auf. |
| `Groups` | Ruft eine Methode für mehrere Gruppen von Verbindungen auf.  |
| `OthersInGroup` | Ruft eine Methode für eine Gruppe von Verbindungen auf, ausgenommen des Clients, der die Hub-Methode aufgerufen hat.  |
| `User` | Ruft eine Methode für alle Verbindungen auf, die einem bestimmten Benutzer zugeordnet sind. |
| `Users` | Ruft für alle Verbindungen, die den angegebenen Benutzern zugeordnet sind, eine Methode auf. |

Jede Eigenschaft oder Methode in den vorangehenden Tabellen gibt ein-Objekt mit einer- `SendAsync` Methode zurück. Mit der `SendAsync` -Methode können Sie den Namen und die Parameter der aufzurufenden Client Methode angeben.

## <a name="send-messages-to-clients"></a>Senden von Nachrichten an Clients

Um Aufrufe an bestimmte Clients durchführen zu können, verwenden Sie die Eigenschaften des- `Clients` Objekts. Im folgenden Beispiel gibt es drei hubmethoden:

* `SendMessage` sendet mithilfe von eine Nachricht an alle verbundenen Clients `Clients.All` .
* `SendMessageToCaller` sendet mithilfe von eine Nachricht an den Aufrufer zurück `Clients.Caller` .
* `SendMessageToGroups` sendet eine Nachricht an alle Clients in der `SignalR Users` Gruppe.

[!code-csharp[Send messages](hubs/sample/hubs/chathub.cs?name=HubMethods)]

## <a name="strongly-typed-hubs"></a>Stark typisierte Hubs

Ein Nachteil von der Verwendung von `SendAsync` besteht darin, dass eine magische Zeichenfolge verwendet wird, um die aufzurufende Client Methode anzugeben. Dadurch bleibt der Code für Laufzeitfehler geöffnet, wenn der Methodenname falsch geschrieben wurde oder auf dem Client fehlt.

Eine Alternative zur Verwendung von `SendAsync` ist die starke Typverwendung von `Hub` mit <xref:Microsoft.AspNetCore.SignalR.Hub%601> . Im folgenden Beispiel wurden die `ChatHub` Client Methoden in eine Schnittstelle namens extrahiert `IChatClient` .

[!code-csharp[Interface for IChatClient](hubs/sample/hubs/ichatclient.cs?name=snippet_IChatClient)]

Diese Schnittstelle kann verwendet werden, um das vorherige Beispiel zu umgestalten `ChatHub` .

[!code-csharp[Strongly typed ChatHub](hubs/sample/hubs/StronglyTypedChatHub.cs?range=8-18,36)]

`Hub<IChatClient>`Die Verwendung von ermöglicht die Überprüfung der Client Methoden über die Kompilierzeit. Dies verhindert Probleme, die durch die Verwendung von magischen Zeichen folgen verursacht werden, da `Hub<T>` nur den Zugriff auf die in der-Schnittstelle definierten Methoden ermöglicht.

Durch die Verwendung eines stark typisierten wird `Hub<T>` die Möglichkeit zur Verwendung von deaktiviert `SendAsync` . Alle für die Schnittstelle definierten Methoden können weiterhin als asynchron definiert werden. Tatsächlich sollte jede dieser Methoden eine zurückgeben `Task` . Da es sich um eine Schnittstelle handelt, verwenden Sie das `async` Schlüsselwort nicht. Zum Beispiel:

```csharp
public interface IClient
{
    Task ClientMethod();
}
```

> [!NOTE]
> Das `Async` Suffix wird nicht aus dem Methodennamen entfernt. Wenn die Client Methode nicht mit definiert ist `.on('MyMethodAsync')` , sollten Sie nicht `MyMethodAsync` als Namen verwenden.

## <a name="change-the-name-of-a-hub-method"></a>Ändern des Namens einer Hub-Methode

Standardmäßig ist ein serverhub-Methodenname der Name der .NET-Methode. Sie können jedoch das [hubmethodname](xref:Microsoft.AspNetCore.SignalR.HubMethodNameAttribute) -Attribut verwenden, um diesen Standardwert zu ändern und manuell einen Namen für die Methode anzugeben. Der Client sollte beim Aufrufen der-Methode anstelle des .NET-Methoden namens diesen Namen verwenden.

[!code-csharp[HubMethodName attribute](hubs/sample/hubs/chathub.cs?name=HubMethodName&highlight=1)]

## <a name="handle-events-for-a-connection"></a>Behandeln von Ereignissen für eine Verbindung

Die SignalR Hubs-API stellt `OnConnectedAsync` die `OnDisconnectedAsync` virtuellen Methoden und zum Verwalten und Nachverfolgen von Verbindungen bereit. Überschreiben `OnConnectedAsync` Sie die virtuelle Methode, um Aktionen auszuführen, wenn ein Client eine Verbindung mit dem Hub herstellt, z. b. das Hinzufügen zu einer Gruppe.

[!code-csharp[Handle connection](hubs/sample/hubs/chathub.cs?name=OnConnectedAsync)]

Überschreiben `OnDisconnectedAsync` Sie die virtuelle Methode, um Aktionen auszuführen, wenn ein Client die Verbindung trennt. Wenn der Client die Verbindung absichtlich trennt (z. b. durch Aufrufen von `connection.stop()` ), wird der- `exception` Parameter verwendet `null` . Wenn der Client jedoch aufgrund eines Fehlers (z. b. eines Netzwerk Fehlers) getrennt wird, enthält der- `exception` Parameter eine Ausnahme, die den Fehler beschreibt.

[!code-csharp[Handle disconnection](hubs/sample/hubs/chathub.cs?name=OnDisconnectedAsync)]

[!INCLUDE[](~/includes/connectionid-signalr.md)]

## <a name="handle-errors"></a>Behandeln von Fehlern

Ausnahmen, die in ihren hubmethoden ausgelöst werden, werden an den Client gesendet, der die Methode aufgerufen hat. Auf dem JavaScript-Client `invoke` gibt die Methode eine [JavaScript-Zusage](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)zurück. Wenn der Client einen Fehler mit einem Handler empfängt, der mithilfe von verbunden `catch` ist, wird er aufgerufen und als JavaScript- `Error` Objekt übermittelt.

[!code-javascript[Error](hubs/sample/wwwroot/js/chat.js?range=23)]

Wenn Ihr Hub eine Ausnahme auslöst, werden die Verbindungen nicht geschlossen. Standardmäßig wird SignalR eine generische Fehlermeldung an den Client zurückgegeben. Zum Beispiel:

```
Microsoft.AspNetCore.SignalR.HubException: An unexpected error occurred invoking 'MethodName' on the server.
```

Unerwartete Ausnahmen enthalten häufig vertrauliche Informationen, wie z. b. den Namen eines Datenbankservers in einer Ausnahme, die ausgelöst wird, wenn die Datenbankverbindung fehlschlägt. SignalR macht diese detaillierten Fehlermeldungen standardmäßig nicht als Sicherheitsmaßnahme verfügbar. Weitere Informationen dazu, warum Ausnahme Details unterdrückt werden, finden Sie im [Artikel Sicherheitsüberlegungen](xref:signalr/security#exceptions) .

Wenn Sie eine Ausnahme Bedingung haben, die Sie an den Client weiter *geben* möchten, können Sie die- `HubException` Klasse verwenden. Wenn Sie einen `HubException` aus ihrer Hub-Methode auslösen, SignalR **will** sendet die gesamte Nachricht unverändert an den Client.

[!code-csharp[ThrowHubException](hubs/sample/hubs/chathub.cs?name=ThrowHubException&highlight=3)]

> [!NOTE]
> SignalR sendet die- `Message` Eigenschaft der Ausnahme nur an den Client. Die Stapel Überwachung und andere Eigenschaften der Ausnahme sind für den Client nicht verfügbar.

## <a name="related-resources"></a>Verwandte Ressourcen

* [Einführung in ASP.net Core SignalR](xref:signalr/introduction)
* [JavaScript-Client](xref:signalr/javascript-client)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
