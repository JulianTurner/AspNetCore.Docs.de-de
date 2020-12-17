---
title: Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly
author: guardrex
description: Erfahren Sie, wie Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly-Apps ausgeführt wird.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/09/2020
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
uid: blazor/webassembly-lazy-load-assemblies
ms.openlocfilehash: 6e7fa6e231e97793fbf7e1ac1d208bf3013c6fce
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506570"
---
# <a name="lazy-load-assemblies-in-aspnet-core-no-locblazor-webassembly"></a>Lazy Loading von Assemblys in ASP.NET Core Blazor WebAssembly

Von [Safia Abdalla](https://safia.rocks) und [Luke Latham](https://github.com/guardrex)

Die Startleistung einer Blazor WebAssembly-App kann verbessert werden, indem das Laden einiger Anwendungsassemblys verzögert wird, bis sie erforderlich sind. Dies wird als *Lazy Loading* bezeichnet. Beispielsweise können Assemblys, die nur zum Rendern einer einzelnen Komponente verwendet werden, so eingerichtet werden, dass Sie nur geladen werden, wenn der Benutzer zu dieser Komponente navigiert. Nach dem Laden werden die Assemblys clientseitig zwischengespeichert und sind für alle zukünftigen Navigationsvorgänge verfügbar.

Mit der Lazy Loading-Funktion von Blazor können Sie App-Assemblys für Lazy Loading markieren. Die Assemblys werden dann während der Laufzeit geladen, wenn der Benutzer zu einer bestimmten Route navigiert. Die Funktion besteht aus Änderungen an der Projektdatei und Änderungen am Router der Anwendung.

> [!NOTE]
> Blazor Server-Apps profitieren nicht von Lazy Loading, da Assemblys in einer Blazor Server-App nicht auf den Client heruntergeladen werden.

## <a name="project-file"></a>Projektdatei

Markieren Sie Assemblys in der Projektdatei der App (`.csproj`) mithilfe des `BlazorWebAssemblyLazyLoad`-Elements für Lazy Loading. Verwenden Sie den Assemblynamen mit der Erweiterung `.dll`. Das Blazor Framework verhindert, dass die durch diese Elementgruppe angegebenen Assemblys beim App-Start geladen werden. Im folgenden Beispiel wird eine große benutzerdefinierte Assembly (`GrantImaharaRobotControls.dll`) für Lazy Loading markiert. Wenn eine Assembly, die für Lazy Loading markiert ist, Abhängigkeiten aufweist, muss sie auch in der Projektdatei für Lazy Loading markiert werden.

```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="GrantImaharaRobotControls.dll" />
</ItemGroup>
```

## <a name="router-component"></a>`Router`-Komponente

Die `Router`-Komponente von Blazor bestimmt, welche Assemblys Blazor nach routingfähigen Komponenten durchsucht. Die `Router`-Komponente ist auch dafür verantwortlich, die Komponente für die Route zu rendern, in der der Benutzer navigiert. Die `Router`-Komponente unterstützt ein `OnNavigateAsync`-Feature, das in Verbindung mit Lazy Loading verwendet werden kann.

In der `Router`-Komponente der App (`App.razor`):

* Fügen Sie einen `OnNavigateAsync`-Rückruf hinzu. Der `OnNavigateAsync`-Handler wird aufgerufen, wenn der Benutzer:
  * Zum ersten Mal eine Route besucht, indem er direkt in seinem Browser zu ihr navigiert.
  * Zu einer neuen Route mithilfe eines Links oder eines <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>-Aufrufs navigiert.
* Wenn für Lazy Loading markierte Assemblys routingfähige Komponenten enthalten, fügen Sie der Komponente eine [Liste](xref:System.Collections.Generic.List%601)\<<xref:System.Reflection.Assembly>> (z. B. mit dem Namen `lazyLoadedAssemblies`) hinzu. Die Assemblys werden an die <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Sammlung zurückgegeben, wenn die Assemblys routingfähige Komponenten enthalten. Das Framework durchsucht die Assemblys nach Routen und aktualisiert die Routensammlung, wenn neue Routen gefunden werden.

```razor
@using System.Reflection

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Wenn der `OnNavigateAsync`-Rückruf einen Ausnahmefehler auslöst, wird die [Fehlerbenutzeroberfläche von Blazor](xref:blazor/fundamentals/handle-errors#detailed-errors-during-development) aufgerufen.

### <a name="assembly-load-logic-in-onnavigateasync"></a>Assemblyladelogik in `OnNavigateAsync`

`OnNavigateAsync` verfügt über einen `NavigationContext`-Parameter, der Informationen zum aktuellen asynchronen Navigationsereignis bereitstellt, einschließlich des Zielpfads (`Path`) und des Abbruchtokens (`CancellationToken`):

* Die `Path`-Eigenschaft ist der Zielpfad des Benutzers relativ zum Basispfad der App, z. B. `/robot`.
* Das `CancellationToken` kann verwendet werden, um den Abbruch des asynchronen Tasks zu beobachten. `OnNavigateAsync` bricht den aktuell ausgeführten Navigationstask automatisch ab, wenn der Benutzer zu einer anderen Seite navigiert.

Implementieren Sie in `OnNavigateAsync` Logik, um die zu ladenden Assemblys zu bestimmen. Folgende Optionen sind verfügbar:

* Bedingte Überprüfungen innerhalb der `OnNavigateAsync`-Methode.
* Eine Nachschlagetabelle, in der Routen Assemblynamen zugeordnet werden, die entweder in die Komponente eingefügt oder innerhalb des [`@code`](xref:mvc/views/razor#code)-Blocks implementiert werden.

`LazyAssemblyLoader` ist ein vom Framework bereitgestellter Singleton-Dienst zum Laden von Assemblys. Fügen Sie `LazyAssemblyLoader` in die `Router`-Komponente ein:

```razor
...
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

...
```

`LazyAssemblyLoader` stellt die `LoadAssembliesAsync`-Methode bereit, für die Folgendes gilt:

* Sie verwendet JS Interop zum Abrufen von Assemblys über einen Netzwerkaufruf.
* Lädt Assemblys in die Laufzeit, die für WebAssembly im Browser ausgeführt werden.

Die Lazy Loading-Implementierung des Frameworks unterstützt Lazy Loading mit Prerendering in einer gehosteten Blazor-Lösung. Während des PreRenderings werden alle Assemblys (einschließlich der für Lazy Loading markierten Assemblys) als geladen angenommen. Registrieren Sie `LazyAssemblyLoader` manuell in der `Startup.ConfigureServices`-Methode des *Serverprojekts* (`Startup.cs`):

```csharp
services.AddScoped<LazyAssemblyLoader>();
```

### <a name="user-interaction-with-navigating-content"></a>Benutzerinteraktion mit `<Navigating>`-Inhalt

Während des Ladens von Assemblys, was mehrere Sekunden dauern kann, kann die `Router`-Komponente dem Benutzer anzeigen, dass ein Seitenübergang stattfindet:

* Fügen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung für den <xref:Microsoft.AspNetCore.Components.Routing?displayProperty=fullName>-Namespace hinzu.
* Fügen Sie der Komponente ein `<Navigating>`-Tag mit Markup für die Anzeige während Seitenübergangsereignissen hinzu.

```razor
...
@using Microsoft.AspNetCore.Components.Routing
...

<Router ...>
    <Navigating>
        <div style="...">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
</Router>

...
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

### <a name="handle-cancellations-in-onnavigateasync"></a>Verarbeiten von Abbrüchen in `OnNavigateAsync`

Das `NavigationContext`-Objekt, das an den `OnNavigateAsync`-Rückruf übergeben wird, enthält ein `CancellationToken`, das beim Auftreten eines neuen Navigationsereignisses festgelegt wird. Der `OnNavigateAsync`-Rückruf muss ausgelöst werden, wenn dieses Abbruchtoken festgelegt wird, um zu vermeiden, dass der `OnNavigateAsync`-Rückruf für eine veraltete Navigation weiterhin ausgeführt wird.

Wenn ein Benutzer zu Route A und dann sofort zu Route B navigiert, sollte die App den `OnNavigateAsync`-Rückruf für Route A nicht weiterhin ausführen:

```razor
@inject HttpClient Http
@inject ProductCatalog Products

<Router AppAssembly="@typeof(Program).Assembly" 
    OnNavigateAsync="@OnNavigateAsync">
    ...
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context)
    {
        if (context.Path == "/about") 
        {
            var stats = new Stats = { Page = "/about" };
            await Http.PostAsJsonAsync("api/visited", stats, context.CancellationToken);
        }
        else if (context.Path == "/store")
        {
            var productIds = [345, 789, 135, 689];

            foreach (var productId in productIds) 
            {
                context.CancellationToken.ThrowIfCancellationRequested();
                Products.Prefetch(productId);
            }
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

> [!NOTE]
> Wenn der Rückruf nicht ausgelöst wird, wenn das Abbruchtoken in `NavigationContext` abgebrochen wird, kann dies zu unbeabsichtigtem Verhalten führen, z. B. zum Rendern einer Komponente aus einer vorherigen Navigation.

### <a name="onnavigateasync-events-and-renamed-assembly-files"></a>`OnNavigateAsync`-Ereignisse und umbenannte Assemblydateien

Das Ressourcenladeprogramm benötigt die Assemblynamen, die in der `blazor.boot.json`-Datei definiert sind. Wenn [Assemblys umbenannt werden](xref:blazor/host-and-deploy/webassembly#change-the-filename-extension-of-dll-files), stimmen die Assemblynamen, die in `OnNavigateAsync`-Methoden verwendet werden, und die Assemblynamen in der `blazor.boot.json`-Datei nicht mehr überein.

So beheben Sie dies:

* Überprüfen Sie, ob die App in der Produktionsumgebung ausgeführt wird, wenn Sie bestimmen, welche Assemblynamen verwendet werden sollen.
* Speichern Sie die umbenannten Assemblynamen in einer eigenen Datei, und lesen Sie aus dieser Datei, um zu bestimmen, welche Assemblynamen in der `LazyLoadAssemblyService`- und der `OnNavigateAsync`-Methode verwendet werden sollen.

### <a name="complete-example"></a>Vollständiges Beispiel

Die folgende vollständige `Router`-Komponente veranschaulicht das Laden der `GrantImaharaRobotControls.dll`-Assembly, wenn der Benutzer zu `/robot` navigiert. Während Seitenübergängen wird dem Benutzer eine formatierte Meldung angezeigt.

```razor
@using System.Reflection
@using Microsoft.AspNetCore.Components.Routing
@using Microsoft.AspNetCore.Components.WebAssembly.Services
@inject LazyAssemblyLoader assemblyLoader

<Router AppAssembly="@typeof(Program).Assembly" 
    AdditionalAssemblies="@lazyLoadedAssemblies" OnNavigateAsync="@OnNavigateAsync">
    <Navigating>
        <div style="padding:20px;background-color:blue;color:white">
            <p>Loading the requested page&hellip;</p>
        </div>
    </Navigating>
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <LayoutView Layout="@typeof(MainLayout)">
            <p>Sorry, there's nothing at this address.</p>
        </LayoutView>
    </NotFound>
</Router>

@code {
    private List<Assembly> lazyLoadedAssemblies = new List<Assembly>();

    private async Task OnNavigateAsync(NavigationContext args)
    {
        try
        {
            if (args.Path.EndsWith("/robot"))
            {
                var assemblies = await assemblyLoader.LoadAssembliesAsync(
                    new List<string>() { "GrantImaharaRobotControls.dll" });
                lazyLoadedAssemblies.AddRange(assemblies);
            }
        }
        catch (Exception ex)
        {
            ...
        }
    }
}
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

## <a name="troubleshoot"></a>Problembehandlung

* Wenn unerwartetes Rendering auftritt (wenn z. B. eine Komponente aus einer vorherigen Navigation gerendert wird), vergewissern Sie sich, dass der Code ausgelöst wird, wenn das Abbruchtoken festgelegt wird.
* Wenn Assemblys beim Starten der Anwendung weiterhin geladen werden, überprüfen Sie, ob die Assembly in der Projektdatei für Lazy Loading markiert ist.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:blazor/webassembly-performance-best-practices>
