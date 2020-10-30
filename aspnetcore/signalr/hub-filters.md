---
title: 'Verwenden von Hub-Filtern in ASP.net Core :::no-loc(SignalR):::'
author: brecon
description: 'Erfahren Sie, wie Sie in ASP.net Core Hub-Filter verwenden :::no-loc(SignalR)::: .'
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/hub-filters
ms.openlocfilehash: 5a4cb5122080b72875ac11cf2e682162d017d7b9
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052720"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="5b996-103">Verwenden von Hub-Filtern in ASP.net Core :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="5b996-103">Use hub filters in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="5b996-104">Hub-Filter:</span><span class="sxs-lookup"><span data-stu-id="5b996-104">Hub filters:</span></span>

* <span data-ttu-id="5b996-105">Sind in ASP.net Core 5,0 oder höher verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5b996-105">Are available in ASP.NET Core 5.0 or later.</span></span>
* <span data-ttu-id="5b996-106">Ermöglicht die Ausführung von Logik vor und nach dem Aufrufen von hubmethoden durch Clients.</span><span class="sxs-lookup"><span data-stu-id="5b996-106">Allow logic to run before and after hub methods are invoked by clients.</span></span>

<span data-ttu-id="5b996-107">Dieser Artikel enthält Anleitungen zum Schreiben und Verwenden von Hub-filtern.</span><span class="sxs-lookup"><span data-stu-id="5b996-107">This article provides guidance for writing and using hub filters.</span></span>

## <a name="configure-hub-filters"></a><span data-ttu-id="5b996-108">Konfigurieren von Hub-Filtern</span><span class="sxs-lookup"><span data-stu-id="5b996-108">Configure hub filters</span></span>

<span data-ttu-id="5b996-109">Hub-Filter können global oder pro Hub-Typ angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="5b996-109">Hub filters can be applied globally or per hub type.</span></span> <span data-ttu-id="5b996-110">Die Reihenfolge, in der Filter hinzugefügt werden, ist die Reihenfolge, in der die Filter ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5b996-110">The order in which filters are added is the order in which the filters run.</span></span> <span data-ttu-id="5b996-111">Globale Hub-Filter werden vor lokalen Hub-Filtern ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5b996-111">Global hub filters run before local hub filters.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(SignalR):::(options =>
    {
        // Global filters will run first
        options.AddFilter<CustomFilter>();
    }).AddHubOptions<ChatHub>(options =>
    {
        // Local filters will run second
        options.AddFilter<CustomFilter2>();
    });
}
```

<span data-ttu-id="5b996-112">Ein Hub-Filter kann mit einer der folgenden Methoden hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="5b996-112">A hub filter can be added in one of the following ways:</span></span>

* <span data-ttu-id="5b996-113">Fügen Sie einen Filter nach dem konkreten Typ hinzu:</span><span class="sxs-lookup"><span data-stu-id="5b996-113">Add a filter by concrete type:</span></span>

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    <span data-ttu-id="5b996-114">Diese wird aus der Abhängigkeitsinjektion (di) oder dem aktivierten Typ aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="5b996-114">This will be resolved from dependency injection (DI) or type activated.</span></span>

* <span data-ttu-id="5b996-115">Filter nach Lauf Zeittyp hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5b996-115">Add a filter by runtime type:</span></span>

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    <span data-ttu-id="5b996-116">Diese wird von di oder aktiviertem Typ aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="5b996-116">This will be resolved from DI or type activated.</span></span>

* <span data-ttu-id="5b996-117">Filter nach Instanz hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5b996-117">Add a filter by instance:</span></span>

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    <span data-ttu-id="5b996-118">Diese Instanz wird wie eine Singleton-Instanz verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b996-118">This instance will be used like a singleton.</span></span> <span data-ttu-id="5b996-119">Für alle hubmethoden Aufrufe wird dieselbe Instanz verwendet.</span><span class="sxs-lookup"><span data-stu-id="5b996-119">All hub method invocations will use the same instance.</span></span>

<span data-ttu-id="5b996-120">Hubfilter werden pro hubaufruf erstellt und verworfen.</span><span class="sxs-lookup"><span data-stu-id="5b996-120">Hub filters are created and disposed per hub invocation.</span></span> <span data-ttu-id="5b996-121">Wenn Sie den globalen Zustand im Filter oder in keinem Zustand speichern möchten, fügen Sie den Hub-Filtertyp di als Singleton hinzu, um die Leistung zu verbessern.</span><span class="sxs-lookup"><span data-stu-id="5b996-121">If you want to store global state in the filter, or no state, add the hub filter type to DI as a singleton for better performance.</span></span> <span data-ttu-id="5b996-122">Alternativ dazu können Sie den Filter auch als-Instanz hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="5b996-122">Alternatively, add the filter as an instance if you can.</span></span>

## <a name="create-hub-filters"></a><span data-ttu-id="5b996-123">Erstellen von Hub-Filtern</span><span class="sxs-lookup"><span data-stu-id="5b996-123">Create hub filters</span></span>

<span data-ttu-id="5b996-124">Erstellen Sie einen Filter, indem Sie eine Klasse deklarieren, die von erbt `IHubFilter` , und die-Methode hinzufügen `InvokeMethodAsync` .</span><span class="sxs-lookup"><span data-stu-id="5b996-124">Create a filter by declaring a class that inherits from `IHubFilter`, and add the `InvokeMethodAsync` method.</span></span> <span data-ttu-id="5b996-125">Außerdem gibt es `OnConnectedAsync` und `OnDisconnectedAsync` , die optional implementiert werden können, um die `OnConnectedAsync` - `OnDisconnectedAsync` bzw.-hubmethoden zu umschließen.</span><span class="sxs-lookup"><span data-stu-id="5b996-125">There is also `OnConnectedAsync` and `OnDisconnectedAsync` that can optionally be implemented to wrap the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods respectively.</span></span>

```csharp
public class CustomFilter : IHubFilter
{
    public async ValueTask<object> InvokeMethodAsync(
        HubInvocationContext invocationContext, Func<HubInvocationContext, ValueTask<object>> next)
    {
        Console.WriteLine($"Calling hub method '{invocationContext.HubMethodName}'");
        try
        {
            return await next(invocationContext);
        }
        catch (Exception ex)
        {
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}'");
            throw ex;
        }
    }

    // Optional method
    public Task OnConnectedAsync(HubLifetimeContext context, Func<HubLifetimeContext, Task> next)
    {
        return next(context);
    }

    // Optional method
    public Task OnDisconnectedAsync(
        HubLifetimeContext context, Exception exception, Func<HubLifetimeContext, Exception, Task> next)
    {
        return next(context, exception);
    }
}
```

<span data-ttu-id="5b996-126">Filter sind der Middleware sehr ähnlich.</span><span class="sxs-lookup"><span data-stu-id="5b996-126">Filters are very similar to middleware.</span></span> <span data-ttu-id="5b996-127">Die- `next` Methode ruft den nächsten Filter auf.</span><span class="sxs-lookup"><span data-stu-id="5b996-127">The `next` method invokes the next filter.</span></span> <span data-ttu-id="5b996-128">Der endgültige Filter Ruft die Hub-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="5b996-128">The final filter will invoke the hub method.</span></span> <span data-ttu-id="5b996-129">Filter können das Ergebnis auch `next` nach dem Aufruf der Hub-Methode aus der Warte-und der Ausführung der Logik speichern, bevor das Ergebnis von zurückgegeben wird `next` .</span><span class="sxs-lookup"><span data-stu-id="5b996-129">Filters can also store the result from awaiting `next` and run logic after the hub method has been called before returning the result from `next`.</span></span>

<span data-ttu-id="5b996-130">Um einen hubmethoden Aufruf in einem Filter zu überspringen, lösen Sie eine Ausnahme vom Typ aus, statt aufzurufenden `HubException` `next` .</span><span class="sxs-lookup"><span data-stu-id="5b996-130">To skip a hub method invocation in a filter, throw an exception of type `HubException` instead of calling `next`.</span></span> <span data-ttu-id="5b996-131">Der Client erhält eine Fehlermeldung, wenn ein Ergebnis erwartet wird.</span><span class="sxs-lookup"><span data-stu-id="5b996-131">The client will receive an error if it was expecting a result.</span></span>

## <a name="use-hub-filters"></a><span data-ttu-id="5b996-132">Verwenden von Hub-Filtern</span><span class="sxs-lookup"><span data-stu-id="5b996-132">Use hub filters</span></span>

<span data-ttu-id="5b996-133">Wenn Sie die Filter Logik schreiben, versuchen Sie, Sie generisch zu machen, indem Sie Attribute für hubmethoden verwenden, anstatt die Namen der hubmethoden zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5b996-133">When writing the filter logic, try to make it generic by using attributes on hub methods instead of checking for hub method names.</span></span>

<span data-ttu-id="5b996-134">Stellen Sie sich einen Filter vor, bei dem ein Hub-Methoden Argument auf verbotene Ausdrücke überprüft und alle gefundenen Ausdrücke ersetzt werden `***` .</span><span class="sxs-lookup"><span data-stu-id="5b996-134">Consider a filter that will check a hub method argument for banned phrases and replace any phrases it finds with `***`.</span></span>
<span data-ttu-id="5b996-135">Nehmen Sie für dieses Beispiel an, dass eine `LanguageFilterAttribute` Klasse definiert ist.</span><span class="sxs-lookup"><span data-stu-id="5b996-135">For this example, assume a `LanguageFilterAttribute` class is defined.</span></span> <span data-ttu-id="5b996-136">Die-Klasse verfügt über eine Eigenschaft namens `FilterArgument` , die festgelegt werden kann, wenn das-Attribut verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5b996-136">The class has a property named `FilterArgument` that can be set when using the attribute.</span></span>

1. <span data-ttu-id="5b996-137">Platzieren Sie das-Attribut für die Hub-Methode, die über ein zu bereinigtes Zeichen folgen Argument verfügt:</span><span class="sxs-lookup"><span data-stu-id="5b996-137">Place the attribute on the hub method that has a string argument to be cleaned:</span></span>

    ```csharp
    public class ChatHub
    {
        [LanguageFilter(filterArgument: 0)]
        public async Task SendMessage(string message, string username)
        {
            await Clients.All.SendAsync("SendMessage", $"{username} says: {message}");
        }
    }
    ```

1. <span data-ttu-id="5b996-138">Definieren Sie einen Hub-Filter, um das Attribut zu überprüfen und verbotene Ausdrücke in einem Hub-Methoden Argument durch zu ersetzen `**_` :</span><span class="sxs-lookup"><span data-stu-id="5b996-138">Define a hub filter to check for the attribute and replace banned phrases in a hub method argument with `**_`:</span></span>

    ```csharp
    public class LanguageFilter : IHubFilter
    {
        // populated from a file or inline
        private List<string> bannedPhrases = new List<string> { "async void", ".Result" };

        public async ValueTask<object> InvokeMethodAsync(HubInvocationContext invocationContext, 
            Func<HubInvocationContext, ValueTask<object>> next)
        {
            var languageFilter = (LanguageFilterAttribute)Attribute.GetCustomAttribute(
                invocationContext.HubMethod, typeof(LanguageFilterAttribute));
            if (languageFilter != null &&
                invocationContext.HubMethodArguments.Count > languageFilter.FilterArgument &&
                invocationContext.HubMethodArguments[languageFilter.FilterArgument] is string str)
            {
                foreach (var bannedPhrase in bannedPhrases)
                {
                    str = str.Replace(bannedPhrase, "_**");
                }

                arguments = invocationContext.HubMethodArguments.ToArray();
                arguments[languageFilter.FilterArgument] = str;
                invocationContext = new HubInvocationContext(invocationContext.Context,
                    invocationContext.ServiceProvider,
                    invocationContext.Hub,
                    invocationContext.HubMethod,
                    arguments);
            }

            return await next(invocationContext);
        }
    }
    ```

1. <span data-ttu-id="5b996-139">Registrieren Sie den Hub-Filter in der- `Startup.ConfigureServices` Methode.</span><span class="sxs-lookup"><span data-stu-id="5b996-139">Register the hub filter in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5b996-140">Um zu vermeiden, dass die Liste der gesperrten Ausdrücke für jeden Aufruf erneut initialisiert wird, wird der hubfilter als Singleton registriert:</span><span class="sxs-lookup"><span data-stu-id="5b996-140">To avoid reinitializing the banned phrases list for every invocation, the hub filter is registered as a singleton:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Add:::no-loc(SignalR):::(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a><span data-ttu-id="5b996-141">Das hubinvocationcontext-Objekt</span><span class="sxs-lookup"><span data-stu-id="5b996-141">The HubInvocationContext object</span></span>

<span data-ttu-id="5b996-142">`HubInvocationContext`Enthält Informationen für den aktuellen hubmethoden Aufruf.</span><span class="sxs-lookup"><span data-stu-id="5b996-142">The `HubInvocationContext` contains information for the current hub method invocation.</span></span>

| <span data-ttu-id="5b996-143">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="5b996-143">Property</span></span> | <span data-ttu-id="5b996-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5b996-144">Description</span></span> | <span data-ttu-id="5b996-145">Typ</span><span class="sxs-lookup"><span data-stu-id="5b996-145">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="5b996-146">`HubCallerContext`Enthält Informationen über die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="5b996-146">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="5b996-147">Die Instanz des Hubs, der für diesen hubmethoden Aufruf verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5b996-147">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `HubMethodName` | <span data-ttu-id="5b996-148">Der Name der aufgerufenen Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="5b996-148">The name of the hub method being invoked.</span></span> | `string` |
| `HubMethodArguments` | <span data-ttu-id="5b996-149">Die Liste der Argumente, die an die Hub-Methode übermittelt werden.</span><span class="sxs-lookup"><span data-stu-id="5b996-149">The list of arguments being passed to the hub method.</span></span> | `IReadOnlyList<string>` |
| `ServiceProvider` | <span data-ttu-id="5b996-150">Der Bereichs bezogene Dienstanbieter für diesen hubmethoden Aufruf.</span><span class="sxs-lookup"><span data-stu-id="5b996-150">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |
| `HubMethod` | <span data-ttu-id="5b996-151">Die Informationen zur Hub-Methode.</span><span class="sxs-lookup"><span data-stu-id="5b996-151">The hub method information.</span></span> | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a><span data-ttu-id="5b996-152">Das hublifetimecontext-Objekt</span><span class="sxs-lookup"><span data-stu-id="5b996-152">The HubLifetimeContext object</span></span>

<span data-ttu-id="5b996-153">Der `HubLifetimeContext` enthält Informationen für die `OnConnectedAsync` `OnDisconnectedAsync` hubmethoden und.</span><span class="sxs-lookup"><span data-stu-id="5b996-153">The `HubLifetimeContext` contains information for the `OnConnectedAsync` and `OnDisconnectedAsync` hub methods.</span></span>

| <span data-ttu-id="5b996-154">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="5b996-154">Property</span></span> | <span data-ttu-id="5b996-155">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="5b996-155">Description</span></span> | <span data-ttu-id="5b996-156">Typ</span><span class="sxs-lookup"><span data-stu-id="5b996-156">Type</span></span> |
| ------ | ------ | ----------- |
| `Context ` | <span data-ttu-id="5b996-157">`HubCallerContext`Enthält Informationen über die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="5b996-157">The `HubCallerContext` contains information about the connection.</span></span> | `HubCallerContext` |
| `Hub` | <span data-ttu-id="5b996-158">Die Instanz des Hubs, der für diesen hubmethoden Aufruf verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5b996-158">The instance of the Hub being used for this hub method invocation.</span></span> | `Hub` |
| `ServiceProvider` | <span data-ttu-id="5b996-159">Der Bereichs bezogene Dienstanbieter für diesen hubmethoden Aufruf.</span><span class="sxs-lookup"><span data-stu-id="5b996-159">The scoped service provider for this hub method invocation.</span></span> | `IServiceProvider` |

## <a name="authorization-and-filters"></a><span data-ttu-id="5b996-160">Autorisierung und Filter</span><span class="sxs-lookup"><span data-stu-id="5b996-160">Authorization and filters</span></span>

<span data-ttu-id="5b996-161">[Autorisieren von Attributen für hubmethoden](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) , die vor hubfiltern ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5b996-161">[Authorize attributes on hub methods](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) run before hub filters.</span></span>
