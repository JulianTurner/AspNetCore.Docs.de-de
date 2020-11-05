---
title: ASP.NET Core-Blazor-Vorlagen
author: guardrex
description: Hier erfahren Sie mehr über ASP.NET Core-Vorlagen für Blazor-Apps und die Blazor-Projektstruktur.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/04/2020
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
uid: blazor/templates
ms.openlocfilehash: eef381367d7aa59dcc430c529746088d4488e700
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054930"
---
# <a name="aspnet-core-no-locblazor-templates"></a>ASP.NET Core-Blazor-Vorlagen

Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)

Das Blazor-Framework bietet Vorlagen zum Entwickeln von Apps für die einzelnen Blazor-Hostingmodelle:

* Blazor WebAssembly (`blazorwasm`)
* Blazor Server (`blazorserver`)

Weitere Informationen zur Blazor-Hostingmodellen finden Sie unter <xref:blazor/hosting-models>.

Vorlagenoptionen sind verfügbar, indem Sie die `--help`-Option an den CLI-Befehl [`dotnet new`](/dotnet/core/tools/dotnet-new) übergeben:

```dotnetcli
dotnet new blazorwasm --help
dotnet new blazorserver --help
```

## <a name="no-locblazor-project-structure"></a>Blazor-Projektstruktur

Die folgenden Dateien und Ordner bilden eine Blazor-App, die aus einer Blazor-Projektvorlage generiert wird:

* `Program.cs`: Der Einstiegspunkt der App, von dem aus Folgendes eingerichtet wird:

  * ASP.NET Core-[Host](xref:fundamentals/host/generic-host) (Blazor Server)
  * WebAssembly-Host (Blazor WebAssembly): Der Code in dieser Datei ist für Apps eindeutig, die aus der Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt wurden.
    * Die `App`-Komponente ist die Stammkomponente der App. Die `App`-Komponente wird als das `app`-DOM-Element (`<app>...</app>`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("app")`) angegeben.
    * [Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

* `Startup.cs` (Blazor Server): Diese Datei enthält die Startlogik der App. Die `Startup`-Klasse definiert zwei Methoden:

  * `ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App. In Blazor Server-Apps werden Dienste durch Aufrufe von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird dem Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.
  * `Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten. Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (`Pages/_Host.cshtml`) einzurichten und die Navigation zu aktivieren.

* `wwwroot/index.html` (Blazor WebAssembly): Die Stammseite der App, die als HTML-Seite implementiert ist:
  * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird. Die Komponente wird an der Position des `app`-DOM-Elements (`<app>...</app>`) gerendert.
  * Die `_framework/blazor.webassembly.js`-JavaScript-Datei wird geladen:
    * Diese lädt die .NET-Runtime, die App und der App-Abhängigkeiten herunter.
    * Sie initialisiert die Runtime, damit die App ausgeführt werden kann.

* `App.razor`: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet. Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

* `Pages`-Ordner: Dieser Ordner enthält die routingfähigen Komponenten/Seiten (`.razor`), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor Server-App. Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben. Die Vorlage besteht aus den folgenden Teilen:
  * `_Host.cshtml` (Blazor Server): Die Stammseite der App, die als Razor-Seite implementiert ist:
    * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
    * Die `_framework/blazor.server.js`-JavaScript-Datei wird geladen, die die SignalR-Echtzeitverbindung zwischen dem Browser und dem Server einrichtet.
    * Die Hostseite gibt an, wo die `App`-Stammkomponente (`App.razor`) gerendert wird.
  * `Counter` (`Pages/Counter.razor`): Implementiert die Zählerseite.
  * `Error` (`Error.razor`, nur Blazor Server-App): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.
  * `FetchData` (`Pages/FetchData.razor`): Implementiert die Seite zum Abrufen von Daten.
  * `Index` (`Pages/Index.razor`): Implementiert die Homepage.
  
* `Properties/launchSettings.json`: Enthält die [Konfiguration der Entwicklungsumgebung](xref:fundamentals/environments#development-and-launchsettingsjson).

* `Shared`-Ordner: Dieser Ordner enthält weitere Benutzeroberflächenkomponenten (`.razor`), die von der App verwendet werden:
  * `MainLayout` (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.
  * `NavMenu` (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-component) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.

* `_Imports.razor`: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (`.razor`) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.

* `Data`-Ordner (Blazor Server): Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellen.

* `wwwroot`: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.

* `appsettings.json`: Enthält [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration) für die App. In einer Blazor WebAssembly-App befindet sich die Datei mit den App-Einstellungen im Ordner `wwwroot`. In einer Blazor Server-App befindet sich die Datei mit den App-Einstellungen im Projektstamm.
