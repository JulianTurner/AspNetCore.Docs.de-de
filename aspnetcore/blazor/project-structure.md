---
title: Blazor-Projektstruktur in ASP.NET Core
author: guardrex
description: Hier erfahren Sie mehr über die Blazor-App-Projektstruktur in ASP.NET Core.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/19/2021
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
uid: blazor/project-structure
ms.openlocfilehash: 94b5a3d8c0f5b94ecac32e6fc5f94efeb8337f37
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280350"
---
# <a name="aspnet-core-blazor-project-structure"></a>Blazor-Projektstruktur in ASP.NET Core

In diesem Artikel werden die Dateien und Ordner beschrieben, aus denen eine Blazor-App besteht, die aus den Blazor-Projektvorlagen generiert wurde.

## Blazor WebAssembly

Die Blazor WebAssembly-Vorlage (`blazorwasm`) erstellt die anfänglichen Dateien und die Verzeichnisstruktur für eine Blazor WebAssembly-App. Die App wird mit Demonstrationscode für eine `FetchData`-Komponente, die Daten aus der statischen Ressource `weather.json` lädt, und für die Benutzerinteraktion mit einer `Counter`-Komponente aufgefüllt.

* `Pages`-Ordner: Enthält die routingfähigen Komponenten/Seiten (`.razor`), aus denen die Blazor-App besteht. Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben. Die Vorlage besteht aus den folgenden Komponenten:
  * `Counter`-Komponente (`Counter.razor`): Implementiert die Zählerseite.
  * `FetchData`-Komponente (`FetchData.razor`): Implementiert die Seite zum Abrufen von Daten.
  * `Index`-Komponente (`Index.razor`): Implementiert die Homepage.
  
* `Properties/launchSettings.json`: Enthält die [Konfiguration der Entwicklungsumgebung](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared`-Ordner: Enthält die folgenden freigegebenen Komponenten und Stylesheets:
  * `MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.
  * `MainLayout.razor.css`: Stylesheet für das Hauptlayout der App.
  * `NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.
  * `NavMenu.razor.css`: Stylesheet für das Navigationsmenü der App.
  * `SurveyPrompt`-Komponente (`SurveyPrompt.razor`): Blazor-Umfragekomponente.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared`-Ordner: Enthält die folgenden freigegebenen Komponenten:
  * `MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.
  * `NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.
  * `SurveyPrompt`-Komponente (`SurveyPrompt.razor`): Blazor-Umfragekomponente.
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

* `wwwroot`: Das [Webstammverzeichnis](xref:fundamentals/index#web-root) für die App, das die öffentlichen statischen Ressourcen der App enthält, einschließlich `appsettings.json` und Dateien mit den Umgebungseinstellungen der App für [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration). Die Webseite `index.html` ist die Stammseite der App, die als HTML-Seite implementiert ist:
  * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird. Die Komponente wird an der Position des `div`-DOM-Elements mit einem `id`-Objekt von `app` (`<div id="app">Loading...</div>`) gerendert.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `wwwroot`: Das [Webstammverzeichnis](xref:fundamentals/index#web-root) für die App, das die öffentlichen statischen Ressourcen der App enthält, einschließlich `appsettings.json` und Dateien mit den Umgebungseinstellungen der App für [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration). Die Webseite `index.html` ist die Stammseite der App, die als HTML-Seite implementiert ist:
  * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
  * Die Seite gibt an, wo die `App`-Stammkomponente gerendert wird. Die Komponente wird an der Position des `app`-DOM-Elements (`<app>Loading...</app>`) gerendert.

::: moniker-end

> [!NOTE]
> JavaScript-Dateien (JS), die der Datei `wwwroot/index.html` hinzugefügt werden, müssen vor dem schließenden `</body>`-Tag eingefügt werden. Die Reihenfolge, in der benutzerdefinierter JS-Code aus JS-Dateien geladen wird, ist in einigen Szenarien wichtig. Stellen Sie beispielsweise sicher, dass JS-Dateien mit Interopmethoden vor JS-Dateien des Blazor-Frameworks eingeschlossen werden.

* `_Imports.razor`: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (`.razor`) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.

* `App.razor`: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet. Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

::: moniker range=">= aspnetcore-5.0"

* `Program.cs`: Der Einstiegspunkt der App, der den WebAssembly-Host einrichtet:
  
  * Die `App`-Komponente ist die Stammkomponente der App. Die `App`-Komponente wird als das `div`-DOM-Element mit einem `id`-Objekt von `app` (`<div id="app">Loading...</div>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("#app")`) angegeben.
  * [Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Program.cs`: Der Einstiegspunkt der App, der den WebAssembly-Host einrichtet:

  * Die `App`-Komponente ist die Stammkomponente der App. Die `App`-Komponente wird als das `app`-DOM-Element (`<app>Loading...</app>` in `wwwroot/index.html`) für die Stammkomponentensammlung (`builder.RootComponents.Add<App>("app")`) angegeben.
  * [Dienste](xref:blazor/fundamentals/dependency-injection) werden hinzugefügt und konfiguriert (z. B. `builder.Services.AddSingleton<IMyDependency, MyDependency>()`).

::: moniker-end

## Blazor Server

Die Blazor Server-Vorlage (`blazorserver`) erstellt die anfänglichen Dateien und die Verzeichnisstruktur für eine Blazor Server-App. Die App wird mit Demonstrationscode für eine `FetchData`-Komponente, die Daten aus dem registrierten Dienst `WeatherForecastService` lädt, und für die Benutzerinteraktion mit einer `Counter`-Komponente aufgefüllt.

* `Data`-Ordner: Dieser Ordner enthält die `WeatherForecast`-Klasse und Implementierung von `WeatherForecastService`, die Beispielwetterdaten für die `FetchData`-Komponente der App bereitstellt.

* `Pages`-Ordner: Dieser Ordner enthält die routingfähigen Komponenten/Seiten (`.razor`), aus denen die Blazor-App besteht, sowie die Razor-Stammseite einer Blazor Server-App. Die Route für jede Seite wird mithilfe der [`@page`](xref:mvc/views/razor#page)-Anweisung angegeben. Die Vorlage besteht aus den folgenden Teilen:
  * `_Host.cshtml`: Die Stammseite der App, die als Razor-Seite implementiert ist:
    * Wenn eine Seite der App zum ersten Mal angefordert wird, wird diese Seite gerendert und in der Antwort zurückgegeben.
    * Die Hostseite gibt an, wo die `App`-Stammkomponente (`App.razor`) gerendert wird.
  * `Counter`-Komponente (`Counter.razor`): Implementiert die Zählerseite.
  * `Error`-Komponente (`Error.razor`): Wird gerendert, wenn in der App eine nicht behandelte Ausnahme auftritt.
  * `FetchData`-Komponente (`FetchData.razor`): Implementiert die Seite zum Abrufen von Daten.
  * `Index`-Komponente (`Index.razor`): Implementiert die Homepage.

> [!NOTE]
> JavaScript-Dateien (JS), die der Datei `Pages/_Host.cshtml` hinzugefügt werden, müssen vor dem schließenden `</body>`-Tag eingefügt werden. Die Reihenfolge, in der benutzerdefinierter JS-Code aus JS-Dateien geladen wird, ist in einigen Szenarien wichtig. Stellen Sie beispielsweise sicher, dass JS-Dateien mit Interopmethoden vor JS-Dateien des Blazor-Frameworks eingeschlossen werden.

* `Properties/launchSettings.json`: Enthält die [Konfiguration der Entwicklungsumgebung](xref:fundamentals/environments#development-and-launchsettingsjson).

::: moniker range=">= aspnetcore-5.0"

* `Shared`-Ordner: Enthält die folgenden freigegebenen Komponenten und Stylesheets:
  * `MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.
  * `MainLayout.razor.css`: Stylesheet für das Hauptlayout der App.
  * `NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.
  * `NavMenu.razor.css`: Stylesheet für das Navigationsmenü der App.
  * `SurveyPrompt`-Komponente (`SurveyPrompt.razor`): Blazor-Umfragekomponente.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* `Shared`-Ordner: Enthält die folgenden freigegebenen Komponenten:
  * `MainLayout`-Komponente (`MainLayout.razor`): Die [Layoutkomponente](xref:blazor/layouts) der App.
  * `NavMenu`-Komponente (`NavMenu.razor`): Diese Komponente implementiert die Navigation in der Seitenleiste. Sie schließt die [`NavLink`-Komponente](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) (<xref:Microsoft.AspNetCore.Components.Routing.NavLink>) ein, die Navigationslinks zu anderen Razor-Komponenten rendert. Die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente gibt automatisch einen ausgewählten Zustand an, wenn die Komponente geladen wird, sodass der Benutzer nachvollziehen kann, welche Komponente derzeit angezeigt wird.
  * `SurveyPrompt`-Komponente (`SurveyPrompt.razor`): Blazor-Umfragekomponente.
  
::: moniker-end

* `wwwroot`: Der Ordner [Web Root](xref:fundamentals/index#web-root) für die App, der die öffentlichen statischen Ressourcen der App enthält.

* `_Imports.razor`: Enthält gängige Razor-Anweisungen, die in die Komponenten der App (`.razor`) eingefügt werden sollen, z. B. [`@using`](xref:mvc/views/razor#using)-Anweisungen für Namespaces.

* `App.razor`: Hierbei handelt es sich um die Stammkomponente der App, die das clientseitige Routing mithilfe der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente einrichtet. Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente fängt die Browsernavigation ab und rendert die Seite, die der angeforderten Adresse entspricht.

* `appsettings.json` und Dateien mit den Umgebungseinstellungen der App: Geben Sie [Konfigurationseinstellungen](xref:blazor/fundamentals/configuration) für die App an.

* `Program.cs`: Der Einstiegspunkt der App, der den ASP.NET Core-[Host](xref:fundamentals/host/generic-host) einrichtet.

* `Startup.cs`: Diese Datei enthält die Startlogik der App. Die `Startup`-Klasse definiert zwei Methoden:

  * `ConfigureServices`: Diese Methode konfiguriert die [DI-Dienste (Dependency Injection)](xref:fundamentals/dependency-injection) der App. Dienste werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.ComponentServiceCollectionExtensions.AddServerSideBlazor%2A> hinzugefügt, und `WeatherForecastService` wird dem Dienstcontainer hinzugefügt, damit die `FetchData`-Beispielkomponente darauf zugreifen kann.
  * `Configure`: Konfiguriert die Pipeline für die Anforderungsverarbeitung der App:
    * <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> wird aufgerufen, um einen Endpunkt für die Echtzeitverbindung mit dem Browser einzurichten. Die Verbindung wird mit [SignalR](xref:signalr/introduction) hergestellt. Dabei handelt es sich um ein Framework zum Hinzufügen von Echtzeitwebfunktionen zu Apps.
    * [`MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage*) wird aufgerufen, um die Stammseite der App (`Pages/_Host.cshtml`) einzurichten und die Navigation zu aktivieren.
