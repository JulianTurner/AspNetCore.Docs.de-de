---
title: Sicherheitsüberlegungen in ASP.net Core SignalR
author: bradygaster
description: Erfahren Sie, wie Sie die Authentifizierung und Autorisierung in ASP.net Core verwenden SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
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
uid: signalr/security
ms.openlocfilehash: 5ecbf07b1527e9c68443870f7fce77adc29a5416
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050835"
---
# <a name="security-considerations-in-aspnet-core-no-locsignalr"></a>Sicherheitsüberlegungen in ASP.net Core SignalR

Von [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Informationen zum Sichern von SignalR .

## <a name="cross-origin-resource-sharing"></a>Cross-Origin Resource Sharing

[Cross-Origin Resource Sharing (cors)](https://www.w3.org/TR/cors/) kann verwendet werden, um Ursprungs übergreifende SignalR Verbindungen im Browser zuzulassen. Wenn JavaScript-Code in einer anderen Domäne als der APP gehostet wird SignalR , muss [cors-Middleware](xref:security/cors) aktiviert werden, damit JavaScript eine Verbindung mit der App herstellen kann SignalR . Lassen Sie Ursprungs übergreifende Anforderungen nur von vertrauenswürdigen Domänen zu. Zum Beispiel:

* Ihre Website wird gehostet auf `http://www.example.com`
* Ihre SignalR App wird gehostet auf `http://signalr.example.com`

Cors sollte in der APP so konfiguriert werden SignalR , dass Sie nur den Ursprung zulässt `www.example.com` .

Weitere Informationen zum Konfigurieren von cors finden Sie unter [Aktivieren von Cross-Origin-Anforderungen (cors)](xref:security/cors). SignalR**erfordert** die folgenden cors-Richtlinien:

* Hiermit werden bestimmte erwartete Ursprünge zugelassen. Es ist möglich, einen beliebigen Ursprung zuzulassen, aber er ist **nicht** sicher oder wird empfohlen.
* HTTP `GET` -Methoden und `POST` müssen zulässig sein.
* Anmelde Informationen müssen zulässig sein, damit cookie -basierte persistente Sitzungen ordnungsgemäß funktionieren. Sie müssen auch dann aktiviert werden, wenn die Authentifizierung nicht verwendet wird.

::: moniker range=">= aspnetcore-5.0"

In 5,0 haben wir jedoch eine Option im typescript-Client bereitgestellt, um keine Anmelde Informationen zu verwenden.
Die Option, Anmelde Informationen nicht zu verwenden, sollte nur verwendet werden, wenn Sie 100% wissen, dass Anmelde Informationen wie z Cookie . b. in Ihrer APP nicht benötigt werden ( cookie e werden von Azure App Service verwendet, wenn mehrere Server für persistente Sitzungen verwendet werden).

::: moniker-end

Mit der folgenden cors-Richtlinie kann beispielsweise ein auf SignalR gehosteter Browser Client auf `https://example.com` die SignalR auf gehostete App zugreifen `https://signalr.example.com` :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // ... other middleware ...

    // Make sure the CORS middleware is ahead of SignalR.
    app.UseCors(builder =>
    {
        builder.WithOrigins("https://example.com")
            .AllowAnyHeader()
            .WithMethods("GET", "POST")
            .AllowCredentials();
    });

    // ... other middleware ...
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub");
    });

    // ... other middleware ...
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Main](security/sample/Startup.cs?name=snippet1)]

::: moniker-end

## <a name="websocket-origin-restriction"></a>WebSocket-Ursprungs Einschränkung

::: moniker range=">= aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Informationen zur Ursprungs Einschränkung für websockets finden Sie unter [websockets Origin-Einschränkung](xref:fundamentals/websockets#websocket-origin-restriction).

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Der von CORS erzeugte Schutz gilt nicht für WebSockets. Für Browser gilt Folgendes **nicht** :

* Ausführen von CORS-Preflightanforderungen
* Berücksichtigen der Einschränkungen, die in den `Access-Control`-Headern bei der Erstellung von WebSocket-Anforderungen angegeben sind

Allerdings senden Browser den `Origin`-Header, wenn die WebSocket-Anforderungen ausgegeben werden. Anwendungen sollten zur Überprüfung dieser Header konfiguriert werden, um sicherzustellen, dass nur WebSockets von den erwarteten Ursprüngen zulässig sind.

In ASP.net Core 2,1 und höher kann die Header Validierung mithilfe einer benutzerdefinierten Middleware **vor und der `UseSignalR` Authentifizierungs Middleware** in erreicht werden `Configure` :

[!code-csharp[Main](security/sample/Startup.cs?name=snippet2)]

> [!NOTE]
> Der `Origin`-Header wird vom Client gesteuert und kann wie der `Referer`-Header überlistet werden. Diese Header sollten **nicht** als Authentifizierungsmechanismus verwendet werden.

::: moniker-end

## <a name="connectionid"></a>ConnectionId

Das verfügbar machen `ConnectionId` von kann zu böswilligem Identitätswechsel führen, wenn die SignalR Server-oder Client Version ASP.net Core 2,2 oder früher ist. Wenn die SignalR Server-und die Client Version ASP.net Core 3,0 oder höher sind, `ConnectionToken` muss anstelle des `ConnectionId` geheim gehalten werden. Der `ConnectionToken` wird absichtlich nicht in einer API verfügbar gemacht.  Es kann schwierig sein, sicherzustellen, dass ältere SignalR Clients keine Verbindung mit dem Server herstellen. selbst wenn Ihre SignalR Server Version ASP.net Core 3,0 oder höher ist, sollte der nicht verfügbar gemacht `ConnectionId` werden.

## <a name="access-token-logging"></a>Protokollierung von Zugriffs Token

Wenn websockets oder Server-Sent Ereignisse verwendet werden, sendet der Browser Client das Zugriffs Token in der Abfrage Zeichenfolge. Der Empfang des Zugriffs Tokens über eine Abfrage Zeichenfolge ist im Allgemeinen sicher, wenn der Standard Header verwendet wird `Authorization` . Verwenden Sie immer HTTPS, um eine sichere End-to-End-Verbindung zwischen dem Client und dem Server sicherzustellen. Viele Webserver protokollieren die URL für jede Anforderung, einschließlich der Abfrage Zeichenfolge. Durch das Protokollieren der URLs kann das Zugriffs Token protokolliert werden. ASP.net Core protokolliert standardmäßig die URL für jede Anforderung, die die Abfrage Zeichenfolge enthält. Zum Beispiel:

```
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/chathub?access_token=1234
```

Wenn Sie Bedenken haben, dass Sie diese Daten mit ihren Serverprotokollen protokollieren möchten, können Sie diese Protokollierung vollständig deaktivieren, indem Sie die Protokollierung `Microsoft.AspNetCore.Hosting` auf die `Warning` Ebene oder höher konfigurieren (diese Nachrichten werden auf der `Info` Ebene geschrieben). Weitere Informationen finden Sie unter [Protokollfilterung](xref:fundamentals/logging/index#log-filtering) . Wenn Sie trotzdem bestimmte Anforderungs Informationen protokollieren möchten, können Sie [eine Middleware schreiben](xref:fundamentals/middleware/write) , um die benötigten Daten zu protokollieren und den `access_token` Wert der Abfrage Zeichenfolge (sofern vorhanden) herauszufiltern.

## <a name="exceptions"></a>Ausnahmen

Ausnahmemeldungen werden generell als vertrauliche Daten angesehen, die nicht für einen Client offengelegt werden dürfen. Standardmäßig SignalR sendet nicht die Details einer Ausnahme, die von einer Hub-Methode ausgelöst wird, an den Client. Stattdessen erhält der Client eine generische Nachricht, die angibt, dass ein Fehler aufgetreten ist. Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit [enabledetailederrors](xref:signalr/configuration#configure-server-options). Ausnahme Meldungen sollten in Produktions-apps nicht für den Client verfügbar gemacht werden.

## <a name="buffer-management"></a>Pufferverwaltung

SignalR verwendet Verbindungs Puffer für eingehende und ausgehende Nachrichten. Standardmäßig SignalR beschränkt diese Puffer auf 32 KB. Die größte Nachricht, die ein Client oder Server senden kann, ist 32 KB. Der maximale Arbeitsspeicher, der von einer Verbindung für Nachrichten belegt wird, beträgt 32 KB. Wenn die Nachrichten immer kleiner als 32 KB sind, können Sie den Grenzwert verringern:

* Verhindert, dass ein Client eine größere Nachricht senden kann.
* Der Server muss niemals große Puffer zuordnen, um Nachrichten zu akzeptieren.

Wenn die Nachrichten größer als 32 KB sind, können Sie den Grenzwert erhöhen. Das Erhöhen dieses Limits bedeutet Folgendes:

* Der-Client kann bewirken, dass der Server große Speicherpuffer zuweist.
* Die Server Zuordnung großer Puffer verringert möglicherweise die Anzahl der gleichzeitigen Verbindungen.

Es gibt Grenzwerte für eingehende und ausgehende Nachrichten. beide können für das [httpconnectiondispatcheroptions](xref:signalr/configuration#configure-server-options) -Objekt konfiguriert werden, das in konfiguriert ist `MapHub` :

* `ApplicationMaxBufferSize` stellt die maximale Anzahl von Bytes vom Client dar, die der Server puffert. Wenn der Client versucht, eine Nachricht zu senden, die über diesen Grenzwert hinausgeht, wird die Verbindung möglicherweise geschlossen.
* `TransportMaxBufferSize` stellt die maximale Anzahl von Bytes dar, die vom Server gesendet werden können. Wenn der Server versucht, eine Nachricht (einschließlich Rückgabewerte von hubmethoden) zu senden, die größer als dieser Grenzwert ist, wird eine Ausnahme ausgelöst.

Durch Festlegen des Limits auf wird `0` der Grenzwert deaktiviert. Durch das Entfernen des Limits kann ein Client eine beliebige Größe einer Nachricht senden. Böswillige Clients, die große Nachrichten senden, können dazu führen, dass mehr Speicherplatz zugewiesen Durch eine übermäßige Speicherauslastung kann die Anzahl gleichzeitiger Verbindungen erheblich reduziert werden.
