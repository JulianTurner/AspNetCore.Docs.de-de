---
title: 'SignalR Hubcontext'
author: bradygaster
description: 'Erfahren Sie, wie Sie den ASP.net Core SignalR hubcontext-Dienst verwenden, um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.'
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
- 'IHubContext'
uid: signalr/hubcontext
ms.openlocfilehash: 0b1940dc85634051e8a566c6859f51c130b69269
ms.sourcegitcommit: 1b7f2e1aabf43fa93b920cad36515d7336bfc2df
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93066732"
---
# <a name="send-messages-from-outside-a-hub"></a><span data-ttu-id="a1044-103">Senden von Nachrichten von außerhalb eines Hubs</span><span class="sxs-lookup"><span data-stu-id="a1044-103">Send messages from outside a hub</span></span>

<span data-ttu-id="a1044-104">Von [Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="a1044-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="a1044-105">Der SignalR Hub ist die Kern Abstraktion für das Senden von Nachrichten an Clients, die mit dem Server verbunden sind SignalR .</span><span class="sxs-lookup"><span data-stu-id="a1044-105">The SignalR hub is the core abstraction for sending messages to clients connected to the SignalR server.</span></span> <span data-ttu-id="a1044-106">Es ist auch möglich, Nachrichten von anderen Stellen in Ihrer APP mit dem `IHubContext` Dienst zu senden.</span><span class="sxs-lookup"><span data-stu-id="a1044-106">It's also possible to send messages from other places in your app using the `IHubContext` service.</span></span> <span data-ttu-id="a1044-107">In diesem Artikel wird erläutert, wie Sie auf einen zugreifen SignalR `IHubContext` , um Benachrichtigungen von außerhalb eines Hubs an Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="a1044-107">This article explains how to access a SignalR `IHubContext` to send notifications to clients from outside a hub.</span></span>

<span data-ttu-id="a1044-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(Vorgehensweise zum Herunterladen)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="a1044-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/hubcontext/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="get-an-instance-of-no-locihubcontext"></a><span data-ttu-id="a1044-109">Eine Instanz von erhalten IHubContext</span><span class="sxs-lookup"><span data-stu-id="a1044-109">Get an instance of IHubContext</span></span>

<span data-ttu-id="a1044-110">In ASP.net Core SignalR können Sie über Abhängigkeitsinjektion auf eine Instanz von zugreifen `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="a1044-110">In ASP.NET Core SignalR, you can access an instance of `IHubContext` via dependency injection.</span></span> <span data-ttu-id="a1044-111">Sie können eine Instanz von `IHubContext` in einen Controller, eine Middleware oder einen anderen di-Dienst einfügen.</span><span class="sxs-lookup"><span data-stu-id="a1044-111">You can inject an instance of `IHubContext` into a controller, middleware, or other DI service.</span></span> <span data-ttu-id="a1044-112">Verwenden Sie die-Instanz, um Nachrichten an Clients zu senden.</span><span class="sxs-lookup"><span data-stu-id="a1044-112">Use the instance to send messages to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a1044-113">Dies unterscheidet sich von ASP.NET 4. x SignalR , bei dem globalhost verwendet wurde, um den Zugriff auf zu ermöglichen `IHubContext` .</span><span class="sxs-lookup"><span data-stu-id="a1044-113">This differs from ASP.NET 4.x SignalR which used GlobalHost to provide access to the `IHubContext`.</span></span> <span data-ttu-id="a1044-114">ASP.net Core verfügt über ein Framework für die Abhängigkeitsinjektion, das den Bedarf an diesem globalen Singleton entfällt.</span><span class="sxs-lookup"><span data-stu-id="a1044-114">ASP.NET Core has a dependency injection framework that removes the need for this global singleton.</span></span>

### <a name="inject-an-instance-of-no-locihubcontext-in-a-controller"></a><span data-ttu-id="a1044-115">Einfügen einer Instanz von IHubContext in einen Controller</span><span class="sxs-lookup"><span data-stu-id="a1044-115">Inject an instance of IHubContext in a controller</span></span>

<span data-ttu-id="a1044-116">Sie können eine Instanz von `IHubContext` in einen Controller einfügen, indem Sie Sie dem Konstruktor hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="a1044-116">You can inject an instance of `IHubContext` into a controller by adding it to your constructor:</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=12-19,57)]

<span data-ttu-id="a1044-117">Nun können Sie mit dem Zugriff auf eine Instanz von `IHubContext` hubmethoden aufrufen, als ob Sie sich im Hub selbst befinden würden.</span><span class="sxs-lookup"><span data-stu-id="a1044-117">Now, with access to an instance of `IHubContext`, you can call hub methods as if you were in the hub itself.</span></span>

[!code-csharp[IHubContext](hubcontext/sample/Controllers/HomeController.cs?range=21-25)]

### <a name="get-an-instance-of-no-locihubcontext-in-middleware"></a><span data-ttu-id="a1044-118">Eine Instanz von IHubContext in Middleware erhalten</span><span class="sxs-lookup"><span data-stu-id="a1044-118">Get an instance of IHubContext in middleware</span></span>

<span data-ttu-id="a1044-119">Greifen Sie `IHubContext` in der middlewarepipeline wie folgt auf zu:</span><span class="sxs-lookup"><span data-stu-id="a1044-119">Access the `IHubContext` within the middleware pipeline like so:</span></span>

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
> <span data-ttu-id="a1044-120">Wenn hubmethoden von außerhalb der Klasse aufgerufen werden `Hub` , gibt es keinen Aufrufer, der dem Aufruf zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="a1044-120">When hub methods are called from outside of the `Hub` class, there's no caller associated with the invocation.</span></span> <span data-ttu-id="a1044-121">Daher gibt es keinen Zugriff auf die `ConnectionId` Eigenschaften, `Caller` und `Others` .</span><span class="sxs-lookup"><span data-stu-id="a1044-121">Therefore, there's no access to the `ConnectionId`, `Caller`, and `Others` properties.</span></span>

### <a name="get-an-instance-of-no-locihubcontext-from-ihost"></a><span data-ttu-id="a1044-122">Eine Instanz von IHubContext von IHost erhalten</span><span class="sxs-lookup"><span data-stu-id="a1044-122">Get an instance of IHubContext from IHost</span></span>

<span data-ttu-id="a1044-123">Der Zugriff `IHubContext` auf ein vom Webhost aus ist für die Integration in Bereiche außerhalb von ASP.net Core hilfreich, z. b. durch die Verwendung von Abhängigkeits Injection-Frameworks von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="a1044-123">Accessing an `IHubContext` from the web host is useful for integrating with areas outside of ASP.NET Core, for example, using third-party dependency injection frameworks:</span></span>

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

### <a name="inject-a-strongly-typed-hubcontext"></a><span data-ttu-id="a1044-124">Einfügen eines stark typisierten hubcontext</span><span class="sxs-lookup"><span data-stu-id="a1044-124">Inject a strongly-typed HubContext</span></span>

<span data-ttu-id="a1044-125">Um einen stark typisierten hubcontext einzufügen, stellen Sie sicher, dass Ihr Hub von erbt `Hub<T>` .</span><span class="sxs-lookup"><span data-stu-id="a1044-125">To inject a strongly-typed HubContext, ensure your Hub inherits from `Hub<T>`.</span></span> <span data-ttu-id="a1044-126">Fügen Sie Sie mithilfe der- `IHubContext<THub, T>` Schnittstelle anstelle von ein `IHubContext<THub>` .</span><span class="sxs-lookup"><span data-stu-id="a1044-126">Inject it using the `IHubContext<THub, T>` interface rather than `IHubContext<THub>`.</span></span>

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

<span data-ttu-id="a1044-127">Weitere Informationen finden Sie unter [stark typisierte Hubs](xref:signalr/hubs#strongly-typed-hubs) .</span><span class="sxs-lookup"><span data-stu-id="a1044-127">See [Strongly typed hubs](xref:signalr/hubs#strongly-typed-hubs) for more information.</span></span>

## <a name="related-resources"></a><span data-ttu-id="a1044-128">Verwandte Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a1044-128">Related resources</span></span>

* [<span data-ttu-id="a1044-129">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="a1044-129">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="a1044-130">Hubs</span><span class="sxs-lookup"><span data-stu-id="a1044-130">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="a1044-131">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="a1044-131">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
