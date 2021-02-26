---
title: Open Web Interface for .NET (OWIN) mit ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core die Open Web Interface for .NET (OWIN) unterstützt, die das Entkoppeln von Web-Apps von Webservern ermöglicht.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 2/8/2021
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
uid: fundamentals/owin
ms.openlocfilehash: c44803c6f67d8e03759a01f7aa71d82088be9a11
ms.sourcegitcommit: ef8d8c79993a6608bf597ad036edcf30b231843f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975273"
---
# <a name="open-web-interface-for-net-owin-with-aspnet-core"></a>Open Web Interface for .NET (OWIN) mit ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core:

* unterstützt Open Web Interface for .NET (OWIN)
* enthält mit .NET Core kompatiblen Ersatz für `Microsoft.Owin.*`-Bibliotheken ([Katana](/aspnet/aspnet/overview/owin-and-katana/))

Mit OWIN können Web-Apps von Webservern entkoppelt werden. OWIN legt eine Standardmöglichkeit des Einsatzes von Middleware in einer Pipeline zum Verarbeiten von Anforderungen und den entsprechenden Antworten fest. ASP.NET Core-Anwendungen und -Middleware können mit auf OWIN basierten Anwendungen, Servern und OWIN basierter Middleware zusammenarbeiten.

OWIN bietet eine Entkopplungsebene, die das gemeinsame Verwenden zweier Frameworks mit unterschiedlichen Objektmodellen zulässt. Das `Microsoft.AspNetCore.Owin`-Paket bietet zwei Adapterimplementierungen:

* ASP.NET Core auf OWIN 
* OWIN auf ASP.NET Core

So kann ASP.NET Core auf einem mit OWIN kompatiblen Server bzw. Host gehostet werden. Alternativ können andere mit OWIN kompatible Komponenten über ASP.NET Core ausgeführt werden.

> [!NOTE]
> Das Verwenden dieser Adapter führt zu Leistungseinbußen. Apps, die nur ASP.NET Core-Komponenten verwenden, sollten keine `Microsoft.AspNetCore.Owin`-Pakete oder Adapter verwenden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/owin/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="running-owin-middleware-in-the-aspnet-core-pipeline"></a>Ausführen von OWIN-Middleware in der ASP.NET Core-Pipeline

Die OWIN-Unterstützung von ASP.NET Core wird im Rahmen des `Microsoft.AspNetCore.Owin`-Pakets bereitgestellt. Sie können OWIN-Unterstützung in Ihrem Projekt ermöglichen, indem Sie dieses Paket installieren.

OWIN-Middleware stimmt mit der [OWIN-Spezifikation](https://owin.org/spec/spec/owin-1.0.0.html) überein, die eine `Func<IDictionary<string, object>, Task>`-Schnittstelle und das Festlegen spezifischer Schlüssel erfordert (wie z.B. `owin.ResponseBody`). Die folgende einfache OWIN-Middleware zeigt „Hello World“ (Hallo Welt) an:

```csharp
public Task OwinHello(IDictionary<string, object> environment)
{
    string responseText = "Hello World via OWIN";
    byte[] responseBytes = Encoding.UTF8.GetBytes(responseText);

    // OWIN Environment Keys: https://owin.org/spec/spec/owin-1.0.0.html
    var responseStream = (Stream)environment["owin.ResponseBody"];
    var responseHeaders = (IDictionary<string, string[]>)environment["owin.ResponseHeaders"];

    responseHeaders["Content-Length"] = new string[] { responseBytes.Length.ToString(CultureInfo.InvariantCulture) };
    responseHeaders["Content-Type"] = new string[] { "text/plain" };

    return responseStream.WriteAsync(responseBytes, 0, responseBytes.Length);
}
```

Die Beispielsignatur gibt ein `Task`-Objekt zurück und akzeptiert ein `IDictionary<string, object>`, wie OWIN es erfordert.

Der folgende Code veranschaulicht, wie Sie die `OwinHello`-Middleware (siehe oben) der ASP.NET Core-Pipeline mit der `UseOwin`-Erweiterungsmethode hinzufügen.

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseOwin(pipeline =>
    {
        pipeline(next => OwinHello);
    });
}
```

Sie können andere Aktionen konfigurieren, die in der OWIN-Pipeline durchgeführt werden sollen.

> [!NOTE]
> Antwortheader sollten nur vor dem ersten Schreiben in den Antwortstream modifiziert werden.

> [!NOTE]
> Mehrere Aufrufe von `UseOwin` werden aus Leistungsgründen nicht empfohlen. OWIN-Komponenten funktionieren am besten, wenn sie gruppiert werden.

```csharp
app.UseOwin(pipeline =>
{
    pipeline(next =>
    {
        return async environment =>
        {
            // Do something before.
            await next(environment);
            // Do something after.
        };
    });
});
```

<a name="hosting-on-owin"></a>

## <a name="run-aspnet-core-on-an-owin-based-server-and-use-its-websockets-support"></a>Ausführen von ASP.NET Core auf einem auf OWIN basierten Server und Nutzen der WebSockets-Unterstützung

Der Zugriff auf Features wie WebSockets ist ein weiteres Beispiel dafür, wie ASP.NET Core Features von auf OWIN basierten Servern nutzen kann. Der .NET OWIN-Webserver, der im vorherigen Beispiel verwendet wurde, verfügt über integrierte Unterstützung für WebSockets. Dies kann von einer ASP.NET Core-Anwendung genutzt werden. Das unten stehende Beispiel zeigt eine einfache Web-App, die WebSockets unterstützt und alles zurück gibt, was über WebSockets an den Server gesendet wurde.

```csharp
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.Use(async (context, next) =>
        {
            if (context.WebSockets.IsWebSocketRequest)
            {
                WebSocket webSocket = await context.WebSockets.AcceptWebSocketAsync();
                await EchoWebSocket(webSocket);
            }
            else
            {
                await next();
            }
        });

        app.Run(context =>
        {
            return context.Response.WriteAsync("Hello World");
        });
    }

    private async Task EchoWebSocket(WebSocket webSocket)
    {
        byte[] buffer = new byte[1024];
        WebSocketReceiveResult received = await webSocket.ReceiveAsync(
            new ArraySegment<byte>(buffer), CancellationToken.None);

        while (!webSocket.CloseStatus.HasValue)
        {
            // Echo anything we receive
            await webSocket.SendAsync(new ArraySegment<byte>(buffer, 0, received.Count), 
                received.MessageType, received.EndOfMessage, CancellationToken.None);

            received = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), 
                CancellationToken.None);
        }

        await webSocket.CloseAsync(webSocket.CloseStatus.Value, 
            webSocket.CloseStatusDescription, CancellationToken.None);
    }
}
```

## <a name="owin-environment"></a>OWIN-Umgebung

Sie können mit `HttpContext` eine OWIN-Umgebung erstellen.

```csharp

   var environment = new OwinEnvironment(HttpContext);
   var features = new OwinFeatureCollection(environment);
   ```

## <a name="owin-keys"></a>OWIN-Schlüssel

OWIN ist von einem `IDictionary<string,object>`-Objekt abhängig, um Informationen innerhalb eines Austauschs einer HTTP-Anforderung und einer Antwort weiterzugeben. ASP.NET Core implementiert die unten aufgelisteten Schlüssel. Weitere Informationen finden Sie in den [Hauptspezifikationen und Erweiterungen](https://owin.org/#spec) und in den [OWIN Key Guidelines and Common Keys (Wichtigste Richtlinien und gängige Schlüsse von OWIN)](https://owin.org/spec/spec/CommonKeys.html).

### <a name="request-data-owin-v100"></a>Anforderungsdaten (OWIN v1.0.0)

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| owin.RequestScheme | `String` |  |
| owin.RequestMethod  | `String` | |    
| owin.RequestPathBase  | `String` | |    
| owin.RequestPath | `String` | |     
| owin.RequestQueryString  | `String` | |    
| owin.RequestProtocol  | `String` | |    
| owin.RequestHeaders | `IDictionary<string,string[]>`  | |
| owin.RequestBody | `Stream`  | |

### <a name="request-data-owin-v110"></a>Anforderungsdaten (OWIN v1.1.0)

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| owin.RequestId | `String` | Optional |

### <a name="response-data-owin-v100"></a>Antwortdaten (OWIN v1.0.0)

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| owin.ResponseStatusCode | `int` | Optional |
| owin.ResponseReasonPhrase | `String` | Optional |
| owin.ResponseHeaders | `IDictionary<string,string[]>`  | |
| owin.ResponseBody | `Stream`  | |

### <a name="other-data-owin-v100"></a>Andere Daten (OWIN v1.0.0)

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| owin.CallCancelled | `CancellationToken` |  |
| owin.Version  | `String` | |   

### <a name="common-keys"></a>Gängige Schlüssel

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| ssl.ClientCertificate | `X509Certificate` |  |
| ssl.LoadClientCertAsync  | `Func<Task>` | |    
| server.RemoteIpAddress  | `String` | |    
| server.RemotePort | `String` | |     
| server.LocalIpAddress  | `String` | |    
| server.LocalPort  | `String` | |    
| server.IsLocal  | `bool` | |    
| server.OnSendingHeaders  | `Action<Action<object>,object>` | |

### <a name="sendfiles-v030"></a>SendFiles v0.3.0

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| sendfile.SendAsync | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) | Pro Anforderung |

### <a name="opaque-v030"></a>Opaque v0.3.0

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| opaque.Version | `String` |  |
| opaque.Upgrade | `OpaqueUpgrade` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| opaque.Stream | `Stream` |  |
| opaque.CallCancelled | `CancellationToken` |  |

### <a name="websocket-v030"></a>WebSocket v0.3.0

| Key               | Wert (Typ) | Beschreibung |
| ----------------- | ------------ | ----------- |
| websocket.Version | `String` |  |
| websocket.Accept | `WebSocketAccept` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm) |
| websocket.AcceptAlt |  | n/v |
| websocket.SubProtocol | `String` | Siehe [RFC6455 Abschnitt 4.2.2](https://tools.ietf.org/html/rfc6455#section-4.2.2), Schritt 5.5 |
| websocket.SendAsync | `WebSocketSendAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.ReceiveAsync | `WebSocketReceiveAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CloseAsync | `WebSocketCloseAsync` | Siehe [Delegatsignatur](https://owin.org/spec/extensions/owin-SendFile-Extension-v0.3.0.htm)  |
| websocket.CallCancelled | `CancellationToken` |  |
| websocket.ClientCloseStatus | `int` | Optional |
| websocket.ClientCloseDescription | `String` | Optional |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Middleware](xref:fundamentals/middleware/index)
* [Server](xref:fundamentals/servers/index)
