---
title: Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps
author: guardrex
description: In diesem Artikel lernen Sie Datenbindungsszenarios für Komponenten und DOM-Elemente in Blazor-Apps kennen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/25/2020
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
uid: blazor/components/integrate-components-into-razor-pages-and-mvc-apps
ms.openlocfilehash: e56a08be082cef4ba3a0a58fdfa9d3800d244f75
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056256"
---
# <a name="integrate-aspnet-core-no-locrazor-components-into-no-locrazor-pages-and-mvc-apps"></a>Integrieren von ASP.NET Core Razor-Komponenten in Razor Pages- und MVC-Apps

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.

Nachdem Sie [die App vorbereitet haben](#prepare-the-app), verwenden Sie die Anleitungen in den folgenden Abschnitten, abhängig von den Anforderungen der App:

* Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind. Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.
  * [Verwenden routingfähiger Komponenten in einer Razor Pages-App](#use-routable-components-in-a-razor-pages-app)
  * [Verwenden routingfähiger Komponenten in einer MVC-App](#use-routable-components-in-an-mvc-app)
* [Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind. Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.

## <a name="prepare-the-app"></a>Vorbereiten der App

Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

1. In der Layoutdatei der App (`_Layout.cshtml`):

   * Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:

     ```html
     <base href="~/" />
     ```

     Der `href`-Wert (der *App-Basispfad* ) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:blazor/host-and-deploy/index#app-base-path>-Artikels.

     Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner *Pages/Shared* und bei MVC-Apps im Ordner *Views/Shared*.

   * Fügen Sie direkt vor dem schließenden `</body>`-Tag ein `<script>`-Tag für das Skript *blazor.server.js* ein:

     ```html
     <script src="_framework/blazor.server.js"></script>
     ```

     Das Framework fügt das Skript *blazor.server.js* zur App hinzu. Das Skript muss nicht manuell zur App hinzugefügt werden.

1. Fügen Sie im Stammordner des Projekts eine `_Imports.razor`-Datei mit dem folgenden Inhalt ein (ändern Sie den letzten Namespace `MyAppNamespace` in den Namespace der App):

   ```razor
   @using System.Net.Http
   @using Microsoft.AspNetCore.Authorization
   @using Microsoft.AspNetCore.Components.Authorization
   @using Microsoft.AspNetCore.Components.Forms
   @using Microsoft.AspNetCore.Components.Routing
   @using Microsoft.AspNetCore.Components.Web
   @using Microsoft.JSInterop
   @using MyAppNamespace
   ```

1. Registrieren Sie in `Startup.ConfigureServices` den Blazor Server-Dienst:

   ```csharp
   services.AddServerSideBlazor();
   ```

1. Fügen Sie in `Startup.Configure` den Blazor Hub-Endpunkt zu `app.UseEndpoints` hinzu:

   ```csharp
   endpoints.MapBlazorHub();
   ```

1. Integrieren Sie Komponenten in eine beliebige Seite oder Ansicht. Weitere Informationen finden Sie im Abschnitt [Rendern von Komponenten einer Seite oder Ansicht](#render-components-from-a-page-or-view).

## <a name="use-routable-components-in-a-no-locrazor-pages-app"></a>Verwenden routingfähiger Komponenten in einer Razor Pages-App

*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*

So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in Razor Pages-Apps ein:

1. Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).

1. Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Fügen Sie dem Ordner `Pages` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:

   ```cshtml
   @page "/blazor"
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.

   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:

   * Ob die Komponente zuvor für die Seite gerendert wird
   * Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.

   | Rendermodus | Beschreibung |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der `App`-Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die `App`-Komponente in statischem HTML. |

   Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Fügen Sie eine Route mit niedriger Priorität für die Seite `_Host.cshtml` zur Endpunktkonfiguration in `Startup.Configure` hinzu:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToPage("/_Host");
   });
   ```

1. Fügen Sie routingfähige Komponenten zur App hinzu. Zum Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).

## <a name="use-routable-components-in-an-mvc-app"></a>Verwenden routingfähiger Komponenten in einer MVC-App

*In diesem Abschnitt wird das Hinzufügen von Komponenten behandelt, die über Benutzeranforderungen direkt routingfähig sind.*

So richten Sie die Unterstützung von routingfähigen Razor-Komponenten in MVC-Apps ein:

1. Befolgen Sie die Anleitungen im Abschnitt [Vorbereiten der App](#prepare-the-app).

1. Fügen Sie eine `App.razor`-Datei mit dem folgenden Inhalt zum Projektstamm hinzu:

   ```razor
   @using Microsoft.AspNetCore.Components.Routing

   <Router AppAssembly="@typeof(Program).Assembly">
       <Found Context="routeData">
           <RouteView RouteData="routeData" />
       </Found>
       <NotFound>
           <h1>Page not found</h1>
           <p>Sorry, but there's nothing here!</p>
       </NotFound>
   </Router>
   ```

1. Fügen Sie dem Ordner `Views/Home` eine `_Host.cshtml`-Datei mit dem folgenden Inhalt hinzu:

   ```cshtml
   @{
       Layout = "_Layout";
   }

   <app>
       <component type="typeof(App)" render-mode="ServerPrerendered" />
   </app>
   ```

   Komponenten verwenden die gemeinsam verwendete Datei `_Layout.cshtml` für ihr Layout.
   
   <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die `App`-Komponente:

   * Ob die Komponente zuvor für die Seite gerendert wird
   * Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.

   | Rendermodus | Beschreibung |
   | ----------- | ----------- |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.ServerPrerendered> | Rendert die `App`-Komponente in statisches HTML und fügt einen Marker für eine Blazor Server-App hinzu. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server> | Rendert einen Marker für eine Blazor Server-App. Die Ausgabe der `App`-Komponente ist nicht enthalten. Wenn der Benutzer-Agent gestartet wird, wird der Marker zum Bootstrapping einer Blazor-App verwendet. |
   | <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Static> | Rendert die `App`-Komponente in statischem HTML. |

   Weitere Informationen zum Taghilfsprogramm für Komponenten finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

1. Fügen Sie eine Aktion zum Home-Controller hinzu:

   ```csharp
   public IActionResult Blazor()
   {
      return View("_Host");
   }
   ```

1. Fügen Sie eine Route mit niedriger Priorität für die Controlleraktion hinzu, die die Ansicht `_Host.cshtml` an die Endpunktkonfiguration in `Startup.Configure` zurückgibt:

   ```csharp
   app.UseEndpoints(endpoints =>
   {
       ...

       endpoints.MapFallbackToController("Blazor", "Home");
   });
   ```

1. Erstellen Sie einen `Pages`-Ordner, und fügen Sie routingfähige Komponenten zur App hinzu. Zum Beispiel:

   ```razor
   @page "/counter"

   <h1>Counter</h1>

   ...
   ```

Weitere Informationen zu Namespaces finden Sie im Abschnitt [Komponentennamespaces](#component-namespaces).

## <a name="render-components-from-a-page-or-view"></a>Rendern von Komponenten einer Seite oder Ansicht

*In diesem Abschnitt wird das Hinzufügen von Komponenten zu Seiten oder Ansichten behandelt, wenn die Komponenten nicht direkt über Benutzeranforderungen routingfähig sind.*

Verwenden Sie das [Komponententaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) zum Rendern einer Komponente einer Seite oder Ansicht.

### <a name="render-stateful-interactive-components"></a>Rendern zustandsbehafteter interaktiver Komponenten

Zustandsbehaftete interaktive Komponenten können einer Razor-Seite oder -Ansicht hinzugefügt werden.

Wenn die Seite oder Ansicht gerendert wird:

* Die Komponente wird mit der Seite oder Ansicht vorab gerendert.
* Der anfängliche Komponentenzustand, der zum Rendern vorab genutzt wurde, geht verloren.
* Der neue Komponentenzustand wird erstellt, wenn die SignalR-Verbindung hergestellt wird.

Die folgende Razor-Seite rendert eine `Counter`-Komponente:

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

### <a name="render-noninteractive-components"></a>Rendern nicht interaktiver Komponenten

Auf der folgenden Razor-Seite wird die `Counter`-Komponente statisch mit einem Anfangswert gerendert, der mit einem Formular angegeben wird. Da die Komponente statisch gerendert wird, ist die Komponente nicht interaktiv:

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@functions {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

## <a name="component-namespaces"></a>Komponentennamespaces

Wenn Sie einen benutzerdefinierten Ordner für die Komponenten der App verwenden, fügen Sie den Namespace des Ordners zur Seite/Ansicht oder zur Datei `_ViewImports.cshtml` hinzu. Im folgenden Beispiel:

* Ändern Sie `MyAppNamespace` in den Namespace der App.
* Wenn die Komponenten nicht in einem Ordner namens *Components* enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.

```cshtml
@using MyAppNamespace.Components
```

Die Datei `_ViewImports.cshtml` befindet sich im Ordner `Pages` einer Razor-Pages-App oder im Ordner `Views` einer MVC-App.

Weitere Informationen finden Sie unter <xref:blazor/components/index#namespaces>.
