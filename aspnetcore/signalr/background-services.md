---
title: Host ASP.net Core SignalR in Hintergrund Diensten
author: bradygaster
description: Erfahren Sie, wie Sie Nachrichten SignalR von .net Core-backgroundservice-Klassen an Clients senden.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/background-services
ms.openlocfilehash: 4b877c64a881fec15ac9e9bd74ffdde0b5fa60f9
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530176"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Host ASP.net Core SignalR in Hintergrund Diensten

Von [Brady Gastern](https://twitter.com/bradygaster)

Dieser Artikel enthält Anleitungen für:

* Hosten SignalR von Hubs mit einem Hintergrund Arbeitsprozess, der mit ASP.net Core gehostet wird.
* Senden von Nachrichten an verbundene Clients aus einem .net Core- [backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService).

::: moniker range=">= aspnetcore-3.0"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

::: moniker-end

## <a name="enable-signalr-in-startup"></a>SignalRBeim Start aktivieren

::: moniker range=">= aspnetcore-3.0"

Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der- `Startup.ConfigureServices` Methode fügt der Aufruf von `services.AddSignalR` die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di) hinzu, um zu unterstützen SignalR . In `Startup.Configure` wird die- `MapHub` Methode im Rückruf aufgerufen `UseEndpoints` , um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

Das Hosting SignalR von ASP.net Core Hubs im Kontext eines hintergrundworkerprozesses ist identisch mit dem Hosting eines Hubs in einer ASP.net Core Web-App. In der- `Startup.ConfigureServices` Methode fügt der Aufruf von `services.AddSignalR` die erforderlichen Dienste der Ebene der ASP.net Core Abhängigkeitsinjektion (di) hinzu, um zu unterstützen SignalR . In `Startup.Configure` wird die- `UseSignalR` Methode aufgerufen, um die Hub-Endpunkte in der ASP.net Core Anforderungs Pipeline zu verbinden.

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

Im vorherigen Beispiel implementiert die- `ClockHub` Klasse die- `Hub<T>` Klasse, um einen stark typisierten Hub zu erstellen. Der wurde `ClockHub` in der-Klasse so konfiguriert, dass er `Startup` auf Anforderungen am Endpunkt antwortet `/hubs/clock` .

Weitere Informationen zu stark typisierten Hubs finden Sie unter [Verwenden von Hubs in SignalR für ASP.net Core](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Diese Funktionalität ist nicht auf die [Hub \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) -Klasse beschränkt. Jede Klasse, die von [Hub](xref:Microsoft.AspNetCore.SignalR.Hub)erbt, z. [b. dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), funktioniert.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

Die vom stark typisierten verwendete Schnittstelle `ClockHub` ist die- `IClock` Schnittstelle.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a>Einen SignalR Hub über einen Hintergrunddienst aufzurufen

Während des Starts `Worker` wird die Klasse, a `BackgroundService` , mithilfe von aktiviert `AddHostedService` .

```csharp
services.AddHostedService<Worker>();
```

Da SignalR auch in der Phase aktiviert ist `Startup` , in der jeder Hub an einen einzelnen Endpunkt in der HTTP-Anforderungs Pipeline ASP.net Core angefügt ist, wird jeder Hub durch ein `IHubContext<T>` auf dem Server dargestellt. Mithilfe der di-Features von ASP.net Core können andere Klassen, die von der hostingschicht instanziiert werden (z. b. `BackgroundService` Klassen, MVC-Controller Klassen oder Razor Seiten Modelle), Verweise auf serverseitige Hubs erhalten, indem Sie Instanzen von `IHubContext<ClockHub, IClock>` während der Konstruktion akzeptieren.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

Wenn die `ExecuteAsync` -Methode im Hintergrunddienst iterativ aufgerufen wird, werden das aktuelle Datum und die aktuelle Uhrzeit des Servers über das an die verbundenen Clients gesendet `ClockHub` .

## <a name="react-to-signalr-events-with-background-services"></a>Reagieren auf SignalR Ereignisse mit Hintergrund Diensten

Wie eine einseitige APP, die den JavaScript-Client für verwendet SignalR , oder eine .net-Desktop-App, die verwendet <xref:signalr/dotnet-client> , kann eine- `BackgroundService` oder- `IHostedService` Implementierung auch verwendet werden, um eine Verbindung mit SignalR Hubs herzustellen und auf Ereignisse zu reagieren.

Die `ClockHubClient` -Klasse implementiert die `IClock` -Schnittstelle und die- `IHostedService` Schnittstelle. Auf diese Weise kann Sie während `Startup` der kontinuierlichen Durchführung von aktiviert werden und auf Hub-Ereignisse vom Server reagieren.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

Während der Initialisierung wird `ClockHubClient` von eine Instanz von erstellt `HubConnection` und die `IClock.ShowTime` -Methode als Handler für das-Ereignis des Hubs aktiviert `ShowTime` .

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

In der- `IHostedService.StartAsync` Implementierung `HubConnection` wird der asynchron gestartet.

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Während der- `IHostedService.StopAsync` Methode `HubConnection` wird der asynchron freigegeben.

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

In der- `IHostedService.StartAsync` Implementierung `HubConnection` wird der asynchron gestartet.

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

Während der- `IHostedService.StopAsync` Methode `HubConnection` wird der asynchron freigegeben.

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hub](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
* [Stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs)
