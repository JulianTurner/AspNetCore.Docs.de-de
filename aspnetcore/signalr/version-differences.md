---
title: Unterschiede zwischen SignalR und ASP.net Core SignalR
author: bradygaster
description: Unterschiede zwischen SignalR und ASP.net Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
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
uid: signalr/version-differences
ms.openlocfilehash: c37f0a3c528b6285fbf35f41eed781fb76c68de4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854599"
---
# <a name="differences-between-aspnet-no-locsignalr-and-aspnet-core-no-locsignalr"></a>Unterschiede zwischen ASP.net SignalR und ASP.net Core SignalR

ASP.net Core SignalR ist nicht mit Clients oder Servern für ASP.NET kompatibel SignalR . In diesem Artikel werden die Features erläutert, die in ASP.net Core entfernt oder geändert wurden SignalR .

## <a name="how-to-identify-the-no-locsignalr-version"></a>Identifizieren der SignalR Version

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Server-nuget-Paket** | [Microsoft. Aspnet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Keine. Im gemeinsamen [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app) -Framework enthalten. |
| **Client-nuget-Pakete** | [Microsoft. Aspnet. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. Aspnet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. aspnetcore. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **NPM-Paket für JavaScript-Client** | [signalr](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| **Java-Client** | [GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)  | Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Server-App-Typ** | ASP.net (System. Web) oder owin-Self-Host | ASP.NET Core |
| **Unterstützte Serverplattformen** | .NET Framework 4,5 oder höher | .Net Core 3,0 oder höher |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| **Server-nuget-Paket** | [Microsoft. Aspnet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. aspnetcore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.net Core)<br>[Microsoft. aspnetcore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| **Client-nuget-Pakete** | [Microsoft. Aspnet. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. Aspnet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. aspnetcore. SignalR . Ent](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| **NPM-Paket für JavaScript-Client** | [signalr](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| **Java-Client** | [GitHub-Repository](https://github.com/SignalR/java-client) (veraltet)  | Maven-Paket [com. Microsoft. signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| **Server-App-Typ** | ASP.net (System. Web) oder owin-Self-Host | ASP.NET Core |
| **Unterstützte Serverplattformen** | .NET Framework 4,5 oder höher | .NET Framework 4.6.1 oder höher<br>.Net Core 2,1 oder höher |

::: moniker-end

## <a name="feature-differences"></a>Featureunterschiede

### <a name="automatic-reconnects"></a>Automatische erneute Verbindungen

::: moniker range=">= aspnetcore-3.0"

In ASP.net SignalR :

* Standardmäßig versucht, erneut eine Verbindung SignalR mit dem Server herzustellen, wenn die Verbindung getrennt wird. 

In ASP.net Core SignalR :

* Automatische erneute Verbindungen sind sowohl für den [.NET-Client](xref:signalr/dotnet-client#automatically-reconnect) als auch den [JavaScript-Client](xref:signalr/javascript-client#automatically-reconnect)aktiviert:

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Vor ASP.net Core 3,0 SignalR unterstützt keine automatischen erneuten Verbindungen. Wenn der Client getrennt wird, muss der Benutzer explizit eine neue Verbindung starten, um die Verbindung wiederherzustellen. In ASP.net SignalR versucht, erneut eine Verbindung SignalR mit dem Server herzustellen, wenn die Verbindung getrennt wird.

::: moniker-end

### <a name="protocol-support"></a>Protokollunterstützung

ASP.net Core SignalR unterstützt JSON und ein neues binäres Protokoll, das auf [messagepack](xref:signalr/messagepackhubprotocol)basiert. Außerdem können benutzerdefinierte Protokolle erstellt werden.

### <a name="transports"></a>Transportprotokolle

Der unveränderte Frame Transport wird in ASP.net Core nicht unterstützt SignalR .

## <a name="differences-on-the-server"></a>Unterschiede auf dem Server

Die ASP.net Core SignalR serverseitigen Bibliotheken sind in [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)enthalten, das in der **ASP.net Core-Webanwendungs** Vorlage für Razor und MVC-Projekte verwendet wird.

ASP.net Core SignalR ist eine ASP.net Core Middleware. Sie muss konfiguriert werden, indem Sie <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in Aufrufen `Startup.ConfigureServices` .

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Methoden Aufrufes in der- `Startup.Configure` Methode zu.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Zum Konfigurieren des Routings ordnen Sie Routen zu Hubs innerhalb des <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> Methoden Aufrufes in der- `Startup.Configure` Methode zu.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Persistente Sitzungen

Das horizontale Skalierungs Modell für ASP.net SignalR ermöglicht Clients das erneute Verbinden und Senden von Nachrichten an einen beliebigen Server in der Farm. In ASP.net Core SignalR muss der Client für die Dauer der Verbindung mit dem gleichen Server interagieren. Für horizontales Skalieren mit redis bedeutet das, dass persistente Sitzungen erforderlich sind. Für horizontales Skalieren mit dem [Azure- SignalR Dienst](/azure/azure-signalr/)sind keine persistenten Sitzungen erforderlich, da der Dienst Verbindungen mit Clients verarbeitet.

### <a name="single-hub-per-connection"></a>Einzelner Hub pro Verbindung

In ASP.net Core wurde SignalR das Verbindungs Modell vereinfacht. Verbindungen werden direkt mit einem einzelnen Hub hergestellt, anstatt mit einer einzelnen Verbindung, die für die gemeinsame Nutzung des Zugriffs auf mehrere Hubs verwendet wird.

### <a name="streaming"></a>Streaming

ASP.net Core SignalR unterstützt jetzt das [Streamen von Daten](xref:signalr/streaming) vom Hub an den Client.

### <a name="state"></a>State

Die Möglichkeit, den beliebigen Zustand zwischen Clients und dem Hub (häufig genannt `HubState` ) zu übergeben, wurde entfernt sowie Unterstützung für Fortschrittsmeldungen. Zurzeit gibt es keine Entsprechung von Hub-Proxys.

### <a name="persistentconnection-removal"></a>Entfernen von persistentconnection

In ASP.net Core wurde SignalR die [persistentconnection](/previous-versions/aspnet/jj919047(v=vs.118)) -Klasse entfernt.

### <a name="globalhost"></a>Global Host

ASP.net Core ist eine Abhängigkeitsinjektion (di) in das Framework integriert. Dienste können mit di auf den [hubcontext](xref:signalr/hubcontext)zugreifen. Das `GlobalHost` Objekt, das in ASP.NET verwendet wird SignalR , um ein-Objekt zu erhalten, ist `HubContext` nicht in ASP.net Core vorhanden SignalR .

### <a name="hubpipeline"></a>Hubpipeline

ASP.net Core bietet SignalR keine Unterstützung für `HubPipeline` Module.

## <a name="differences-on-the-client"></a>Unterschiede auf dem Client

### <a name="typescript"></a>TypeScript

Der ASP.net Core- SignalR Client wird in [typescript](https://www.typescriptlang.org/)geschrieben. Sie können in JavaScript oder typescript schreiben, wenn Sie den [JavaScript-Client](xref:signalr/javascript-client)verwenden.

### <a name="the-javascript-client-is-hosted-at-npm"></a>Der JavaScript-Client wird auf NPM gehostet.

::: moniker range=">= aspnetcore-3.0"

In ASP.NET-Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen. In den ASP.net Core Versionen enthält das [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM-Paket die JavaScript-Bibliotheken. Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten. Verwenden Sie NPM, um das NPM-Paket abzurufen und zu installieren `@microsoft/signalr` .

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

In ASP.NET-Versionen wurde der JavaScript-Client über ein nuget-Paket in Visual Studio abgerufen. In den ASP.net Core Versionen enthält das [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM-Paket die JavaScript-Bibliotheken. Dieses Paket ist nicht in der **ASP.net Core-Webanwendungs** Vorlage enthalten. Verwenden Sie NPM, um das NPM-Paket abzurufen und zu installieren `@aspnet/signalr` .

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

Die Abhängigkeit von jQuery wurde entfernt, aber Projekte können weiterhin jQuery verwenden.

### <a name="internet-explorer-support"></a>Internet Explorer-Unterstützung

SignalRMicrosoft Internet Explorer wird von ASP.net Core nicht unterstützt, während ASP.net SignalR Microsoft Internet Explorer 8 oder höher unterstützt.
Weitere Informationen finden Sie unter <xref:signalr/supported-platforms#javascript-client>.

### <a name="javascript-client-method-syntax"></a>Syntax der JavaScript-Client Methode

::: moniker range=">= aspnetcore-3.0"

Die JavaScript-Syntax wurde von der ASP.NET-Version von geändert SignalR . Anstatt das-Objekt zu verwenden `$connection` , erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) -API eine Verbindung.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Verwenden Sie die [on](/javascript/api/@microsoft/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Die JavaScript-Syntax wurde von der ASP.NET-Version von geändert SignalR . Anstatt das-Objekt zu verwenden `$connection` , erstellen Sie mithilfe der [hubconnectionbuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) -API eine Verbindung.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Verwenden Sie die [on](/javascript/api/@aspnet/signalr/HubConnection#on) -Methode, um Client Methoden anzugeben, die der Hub abrufen kann.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

Nachdem Sie die Client Methode erstellt haben, starten Sie die Hub-Verbindung. Verketten einer [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) -Methode zum Protokollieren oder behandeln von Fehlern.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Knotenproxys

::: moniker range=">= aspnetcore-3.0"

Hub-Proxys werden nicht mehr automatisch generiert. Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/@microsoft/signalr/hubconnection#invoke) -API übermittelt.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Hub-Proxys werden nicht mehr automatisch generiert. Stattdessen wird der Methodenname als Zeichenfolge an die [Aufruf](/javascript/api/@aspnet/signalr/hubconnection#invoke) -API übermittelt.

::: moniker-end

### <a name="net-and-other-clients"></a>.Net und andere Clients

[Microsoft. aspnetcore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Das nuget-Paket des Clients enthält die .NET-Client Bibliotheken für ASP.net Core SignalR .

Verwenden <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> Sie, um eine Instanz einer Verbindung mit einem Hub zu erstellen und zu erstellen.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Unterschiede bei horizontaler Skalierung

ASP.net SignalR unterstützt SQL Server und redis. ASP.net Core SignalR unterstützt den Azure SignalR -Dienst und redis.

### <a name="aspnet"></a>ASP.NET

* [SignalR horizontales hochskalieren mit Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalR horizontales hochskalieren mit redis](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalR horizontales hochskalieren mit SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Core

* [Azure- SignalR Dienst](/azure/azure-signalr/)
* [Redis-Backplane](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
* [.NET-Client](xref:signalr/dotnet-client)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
