---
title: SignalR Hubcontext
author: bradygaster
description: Erfahren Sie, wie Sie den ASP.net Core SignalR hubcontext-Dienst verwenden, um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.
monikerRange: '>= aspnetcore-2.1'
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
uid: signalr/hubcontext
ms.openlocfilehash: 91d02ea9e15a2c3910c3b10159bf5b1523c8e271
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058180"
---
# <a name="send-messages-from-outside-a-hub"></a>Senden von Nachrichten von außerhalb eines Hubs

Von [Mikael Mengistu](https://twitter.com/MikaelM_12)

Der SignalR Hub ist die Kern Abstraktion für das Senden von Nachrichten an Clients, die mit dem Server verbunden sind SignalR . Es ist auch möglich, Nachrichten von anderen Stellen in Ihrer APP mit dem `IHubContext` Dienst zu senden. In diesem Artikel wird erläutert, wie Sie auf einen zugreifen SignalR `IHubContext` , um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)

## <a name="get-an-instance-of-ihubcontext"></a>Eine Instanz von "ihubcontext" erhalten

In ASP.net Core SignalR können Sie über Abhängigkeitsinjektion auf eine Instanz von zugreifen `IHubContext` . Sie können eine Instanz von `IHubContext` in einen Controller, eine Middleware oder einen anderen di-Dienst einfügen. Verwenden Sie die-Instanz, um Nachrichten an Clients zu senden.

> [!NOTE]
> Dies unterscheidet sich von ASP.NET 4. x SignalR , bei dem globalhost verwendet wurde, um den Zugriff auf zu ermöglichen `IHubContext` . ASP.net Core verfügt über ein Framework für die Abhängigkeitsinjektion, das den Bedarf an diesem globalen Singleton entfällt.

### <a name="inject-an-instance-of-ihubcontext-in-a-controller"></a>Einfügen einer Instanz von ihubcontext in einen Controller

Sie können eine Instanz von `IHubContext` in einen Controller einfügen, indem Sie Sie dem Konstruktor hinzufügen:

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

Nun können Sie mit dem Zugriff auf eine Instanz von `IHubContext` hubmethoden aufrufen, als ob Sie sich im Hub selbst befinden würden.

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-ihubcontext-in-middleware"></a>Eine Instanz von "ihubcontext" in der Middleware erhalten

Greifen Sie `IHubContext` in der middlewarepipeline wie folgt auf zu:

```csharp
app.Use(async (context, next) =>
{
    var hubContext = context.RequestServices
                            .GetRequiredService<IHubContext<ChatHub>>();
    //...
    
    if (next != null)
    {
        await next.Invoke();
    }
});
```

> [!NOTE]
> Wenn hubmethoden von außerhalb der Klasse aufgerufen werden `Hub` , gibt es keinen Aufrufer, der dem Aufruf zugeordnet ist. Daher gibt es keinen Zugriff auf die `ConnectionId` Eigenschaften, `Caller` und `Others` .

### <a name="get-an-instance-of-ihubcontext-from-ihost"></a>Eine Instanz von "ihubcontext" von IHost erhalten

Der Zugriff `IHubContext` auf ein vom Webhost aus ist für die Integration in Bereiche außerhalb von ASP.net Core hilfreich, z. b. durch die Verwendung von Abhängigkeits Injection-Frameworks von Drittanbietern

```csharp
    public class Program
    {
        public static void Main(string[] args)
        {
            var host = CreateHostBuilder(args).Build();
            var hubContext = host.Services.GetService(typeof(IHubContext<ChatHub>));
            host.Run();
        }

        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
                .ConfigureWebHostDefaults(webBuilder => {
                    webBuilder.UseStartup<Startup>();
                });
    }
```

### <a name="inject-a-strongly-typed-hubcontext"></a>Einfügen eines stark typisierten hubcontext

Um einen stark typisierten hubcontext einzufügen, stellen Sie sicher, dass Ihr Hub von erbt `Hub<T>` . Fügen Sie Sie mithilfe der- `IHubContext<THub, T>` Schnittstelle anstelle von ein `IHubContext<THub>` .

```csharp
public class ChatController : Controller
{
    public IHubContext<ChatHub, IChatClient> _strongChatHubContext { get; }

    public ChatController(IHubContext<ChatHub, IChatClient> chatHubContext)
    {
        _strongChatHubContext = chatHubContext;
    }

    public async Task SendMessage(string user, string message)
    {
        await _strongChatHubContext.Clients.All.ReceiveMessage(user, message);
    }
}
```

Weitere Informationen finden Sie unter [stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs) .

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [Hubs](xref:signalr/hubs)
* [Veröffentlichen in Azure](xref:signalr/publish-to-azure-web-app)
