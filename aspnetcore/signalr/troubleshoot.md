---
title: Problembehandlung bei ASP.net Core SignalR Verbindung
author: bradygaster
description: Problembehandlung bei der ASP.net Core SignalR Verbindung.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/08/2020
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
uid: signalr/troubleshoot
ms.openlocfilehash: f1d9761267d7c6af76c0be6abb238742f40fb016
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059610"
---
# <a name="troubleshoot-connection-errors"></a>Beheben von Verbindungsproblemen

Dieser Abschnitt enthält Hilfe bei Fehlern, die auftreten können, wenn Sie versuchen, eine Verbindung mit einem ASP.net Core SignalR Hub herzustellen.

### <a name="response-code-404"></a>Antwort Code 404

Bei Verwendung von websockets und `skipNegotiation = true`
```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 404
```

* Wenn Sie mehrere Server ohne persistente Sitzungen verwenden, kann die Verbindung auf einem Server gestartet und dann zu einem anderen Server gewechselt werden. Der andere Server kennt die vorherige Verbindung nicht.
* Vergewissern Sie sich, dass der Client eine Verbindung mit dem richtigen Endpunkt herstellt. Beispielsweise wird der Server unter gehostet, `http://127.0.0.1:5000/hub/myHub` und der Client versucht, eine Verbindung mit herzustellen `http://127.0.0.1:5000/myHub` .
* Wenn die Verbindung die ID verwendet und zu lange dauert, bis nach der Aushandlung eine Anforderung an den Server gesendet wird, wird der Server:

  * Löscht die ID.
  * Gibt 404 zurück.

### <a name="response-code-400-or-503"></a>Antwort Code 400 oder 503

Für den folgenden Fehler:

```log
WebSocket connection to 'wss://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 400

Error: Failed to start the connection: Error: There was an error with the transport.
```

Dieser Fehler wird normalerweise dadurch verursacht, dass ein Client nur den websockets-Transport verwendet, aber das websockets-Protokoll auf dem Server nicht aktiviert ist.

### <a name="response-code-307"></a>Antwort Code 307

Bei Verwendung von websockets und `skipNegotiation = true`
```log
WebSocket connection to 'ws://xxx/HubName' failed: Error during WebSocket handshake: Unexpected response code: 307
```

Dieser Fehler kann auch während der Aushandlungs Anforderung auftreten.

Häufige Ursache:
* Die APP ist so konfiguriert, dass Sie HTTPS durch Aufrufen von `UseHttpsRedirection` in erzwingen `Startup` oder HTTPS per URL-Rewrite-Regel erzwingt.

Mögliche Lösung:
* Ändern Sie die URL auf der Clientseite von "http" in "https". `.withUrl("https://xxx/HubName")`

### <a name="response-code-405"></a>Antwort Code 405

HTTP-Statuscode 405-Methode nicht zulässig

* Für die APP ist [cors](xref:signalr/security#cross-origin-resource-sharing) nicht aktiviert

### <a name="response-code-0"></a>Antwort Code 0

HTTP-Statuscode 0 (in der Regel ein [cors](xref:signalr/security#cross-origin-resource-sharing) -Problem), wird kein Statuscode angegeben.

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: CORS header 'Access-Control-Allow-Origin' missing).
```

* Fügen Sie die erwarteten Ursprünge hinzu. `.WithOrigins(...)`

```log
Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at http://localhost:5000/default/negotiate?negotiateVersion=1. (Reason: expected 'true' in CORS header 'Access-Control-Allow-Credentials').
```

* Fügen Sie die `.AllowCredentials()` cors-Richtlinie hinzu. `.AllowAnyOrigin()`Oder `.WithOrigins("*")` mit dieser Option kann nicht verwendet werden.

### <a name="response-code-413"></a>Antwort Code 413

HTTP-Statuscode 413-Nutzlast zu groß

Dies wird häufig durch ein Zugriffs Token verursacht, das über 4 KB ist.

* Wenn Sie den Azure- SignalR Dienst verwenden, verringern Sie die Tokengröße, indem Sie die Ansprüche anpassen, die über den Dienst gesendet werden:
```csharp
.AddAzureSignalR(options =>
{
    options.ClaimsProvider = context => context.User.Claims;
});
```

### <a name="transient-network-failures"></a>Vorübergehende Netzwerkfehler

Vorübergehende Netzwerkfehler können die SignalR Verbindung schließen. Der Server interpretiert die geschlossene Verbindung möglicherweise als eine ordnungsgemäße Client Verbindung. Wenn Sie mehr darüber erfahren möchten, warum ein Client in diesen Fällen getrennt ist, [erfassen Sie Protokolle vom Client und Server](xref:signalr/diagnostics).