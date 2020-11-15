---
title: Verwenden von Hub-Filtern in ASP.net Core SignalR
author: brecon
description: Erfahren Sie, wie Sie in ASP.net Core Hub-Filter verwenden SignalR .
monikerRange: '>= aspnetcore-5.0'
ms.author: brecon
ms.custom: mvc
ms.date: 05/22/2020
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
uid: signalr/hub-filters
ms.openlocfilehash: 9b131d8ec13204525f39263afaf506e336373a7c
ms.sourcegitcommit: 827e8be18cebbcc09b467c089e17fa6f5e430cb2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94634573"
---
# <a name="use-hub-filters-in-aspnet-core-no-locsignalr"></a>Verwenden von Hub-Filtern in ASP.net Core SignalR

Hub-Filter:

* Sind in ASP.net Core 5,0 oder höher verfügbar.
* Ermöglicht die Ausführung von Logik vor und nach dem Aufrufen von hubmethoden durch Clients.

Dieser Artikel enthält Anleitungen zum Schreiben und Verwenden von Hub-filtern.

## <a name="configure-hub-filters"></a>Konfigurieren von Hub-Filtern

Hub-Filter können global oder pro Hub-Typ angewendet werden. Die Reihenfolge, in der Filter hinzugefügt werden, ist die Reihenfolge, in der die Filter ausgeführt werden. Globale Hub-Filter werden vor lokalen Hub-Filtern ausgeführt.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(options =>
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

Ein Hub-Filter kann mit einer der folgenden Methoden hinzugefügt werden:

* Fügen Sie einen Filter nach dem konkreten Typ hinzu:

    ```csharp
    hubOptions.AddFilter<TFilter>();
    ```

    Diese wird aus der Abhängigkeitsinjektion (di) oder dem aktivierten Typ aufgelöst.

* Filter nach Lauf Zeittyp hinzufügen:

    ```csharp
    hubOptions.AddFilter(typeof(TFilter));
    ```

    Diese wird von di oder aktiviertem Typ aufgelöst.

* Filter nach Instanz hinzufügen:

    ```csharp
    hubOptions.AddFilter(new MyFilter());
    ```

    Diese Instanz wird wie eine Singleton-Instanz verwendet. Für alle hubmethoden Aufrufe wird dieselbe Instanz verwendet.

Hubfilter werden pro hubaufruf erstellt und verworfen. Wenn Sie den globalen Zustand im Filter oder in keinem Zustand speichern möchten, fügen Sie den Hub-Filtertyp di als Singleton hinzu, um die Leistung zu verbessern. Alternativ dazu können Sie den Filter auch als-Instanz hinzufügen.

## <a name="create-hub-filters"></a>Erstellen von Hub-Filtern

Erstellen Sie einen Filter, indem Sie eine Klasse deklarieren, die von erbt `IHubFilter` , und die-Methode hinzufügen `InvokeMethodAsync` . Außerdem gibt es `OnConnectedAsync` und `OnDisconnectedAsync` , die optional implementiert werden können, um die `OnConnectedAsync` - `OnDisconnectedAsync` bzw.-hubmethoden zu umschließen.

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
            Console.WriteLine($"Exception calling '{invocationContext.HubMethodName}': {ex}");
            throw;
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

Filter sind der Middleware sehr ähnlich. Die- `next` Methode ruft den nächsten Filter auf. Der endgültige Filter Ruft die Hub-Methode auf. Filter können das Ergebnis auch `next` nach dem Aufruf der Hub-Methode aus der Warte-und der Ausführung der Logik speichern, bevor das Ergebnis von zurückgegeben wird `next` .

Um einen hubmethoden Aufruf in einem Filter zu überspringen, lösen Sie eine Ausnahme vom Typ aus, statt aufzurufenden `HubException` `next` . Der Client erhält eine Fehlermeldung, wenn ein Ergebnis erwartet wird.

## <a name="use-hub-filters"></a>Verwenden von Hub-Filtern

Wenn Sie die Filter Logik schreiben, versuchen Sie, Sie generisch zu machen, indem Sie Attribute für hubmethoden verwenden, anstatt die Namen der hubmethoden zu überprüfen.

Stellen Sie sich einen Filter vor, bei dem ein Hub-Methoden Argument auf verbotene Ausdrücke überprüft und alle gefundenen Ausdrücke ersetzt werden `***` .
Nehmen Sie für dieses Beispiel an, dass eine `LanguageFilterAttribute` Klasse definiert ist. Die-Klasse verfügt über eine Eigenschaft namens `FilterArgument` , die festgelegt werden kann, wenn das-Attribut verwendet wird.

1. Platzieren Sie das-Attribut für die Hub-Methode, die über ein zu bereinigtes Zeichen folgen Argument verfügt:

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

1. Definieren Sie einen Hub-Filter, um das Attribut zu überprüfen und verbotene Ausdrücke in einem Hub-Methoden Argument durch zu ersetzen `**_` :

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

1. Registrieren Sie den Hub-Filter in der- `Startup.ConfigureServices` Methode. Um zu vermeiden, dass die Liste der gesperrten Ausdrücke für jeden Aufruf erneut initialisiert wird, wird der hubfilter als Singleton registriert:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR(hubOptions =>
        {
            hubOptions.AddFilter<LanguageFilter>();
        });
    
        services.AddSingleton<LanguageFilter>();
    }
    ```

## <a name="the-hubinvocationcontext-object"></a>Das hubinvocationcontext-Objekt

`HubInvocationContext`Enthält Informationen für den aktuellen hubmethoden Aufruf.

| Eigenschaft | BESCHREIBUNG | type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Enthält Informationen über die Verbindung. | `HubCallerContext` |
| `Hub` | Die Instanz des Hubs, der für diesen hubmethoden Aufruf verwendet wird. | `Hub` |
| `HubMethodName` | Der Name der aufgerufenen Hub-Methode. | `string` |
| `HubMethodArguments` | Die Liste der Argumente, die an die Hub-Methode übermittelt werden. | `IReadOnlyList<string>` |
| `ServiceProvider` | Der Bereichs bezogene Dienstanbieter für diesen hubmethoden Aufruf. | `IServiceProvider` |
| `HubMethod` | Die Informationen zur Hub-Methode. | `MethodInfo` |

## <a name="the-hublifetimecontext-object"></a>Das hublifetimecontext-Objekt

Der `HubLifetimeContext` enthält Informationen für die `OnConnectedAsync` `OnDisconnectedAsync` hubmethoden und.

| Eigenschaft | BESCHREIBUNG | type |
| ------ | ------ | ----------- |
| `Context ` | `HubCallerContext`Enthält Informationen über die Verbindung. | `HubCallerContext` |
| `Hub` | Die Instanz des Hubs, der für diesen hubmethoden Aufruf verwendet wird. | `Hub` |
| `ServiceProvider` | Der Bereichs bezogene Dienstanbieter für diesen hubmethoden Aufruf. | `IServiceProvider` |

## <a name="authorization-and-filters"></a>Autorisierung und Filter

[Autorisieren von Attributen für hubmethoden](xref:signalr/authn-and-authz#use-authorization-handlers-to-customize-hub-method-authorization) , die vor hubfiltern ausgeführt werden.
