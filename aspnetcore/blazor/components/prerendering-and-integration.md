---
title: Vorabrendern und Integrieren von ASP.NET Core Razor-Komponenten
author: guardrex
description: Informieren Sie sich über Razor-Komponentenintegrationsszenarien für Blazor-Apps, einschließlich des Vorabrenderns von Razor-Komponenten auf dem Server.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
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
uid: blazor/components/prerendering-and-integration
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 3402117334548f9d90880d4f536e8baa288e7bc9
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506980"
---
# <a name="prerender-and-integrate-aspnet-core-no-locrazor-components"></a>Vorabrendern und Integrieren von ASP.NET Core Razor-Komponenten

Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)

::: zone pivot="webassembly"

::: moniker range=">= aspnetcore-5.0"

Razor-Komponenten können in Razor Pages- und MVC-Apps integriert werden, die in einer Blazor WebAssembly-Lösung gehostet werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.

## <a name="configuration"></a>Konfiguration

So richten Sie das Vorabrendern für eine Blazor WebAssembly-App ein

1. Hosten Sie die Blazor WebAssembly-App in einer ASP.NET Core-App. Eine eigenständige Blazor WebAssembly-App kann einer ASP.NET Core-Lösung hinzugefügt werden, oder Sie können eine gehostete Blazor WebAssembly-App verwenden, die anhand der Blazor-Projektvorlage „Hosted“ erstellt wird.

1. Entfernen Sie die standardmäßige statische Datei `wwwroot/index.html` aus dem Blazor WebAssembly-Clientprojekt.

1. Löschen Sie im Clientprojekt die folgende Zeile in `Program.Main`:

   ```csharp
   builder.RootComponents.Add<App>("#app");
   ```

1. Fügen Sie dem Serverprojekt die Datei `Pages/_Host.cshtml` hinzu. Sie können die Datei `_Host.cshtml` aus einer App abrufen, die anhand der Vorlage Blazor Server mit dem Befehl `dotnet new blazorserver -o BlazorServer` in einer Befehlsshell erstellt wurde. Nachdem Sie die Datei `Pages/_Host.cshtml` in der Server-App der gehosteten Blazor WebAssembly-Lösung abgelegt haben, nehmen Sie die folgenden Änderungen an der Datei vor:

   * Legen Sie den Namespace auf den Ordner `Pages` der Server-App fest (z. B. `@namespace BlazorHosted.Server.Pages`).
   * Legen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung für das Clientprojekt fest (z. B. `@using BlazorHosted.Client`).
   * Aktualisieren Sie die Stylesheetlinks so, dass sie auf das Stylesheet der WebAssembly-App zeigen. Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:

     ```cshtml
     <link href="css/app.css" rel="stylesheet" />
     <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
     ```

   * Aktualisieren Sie den `render-mode` des [Hilfsprogramm für Komponententags](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) so, dass die Stammkomponente `App` gerendert wird:

     ```cshtml
     <component type="typeof(App)" render-mode="WebAssemblyPrerendered" />
     ```

   * Aktualisieren Sie die Quelle des Blazor-Skripts so, dass das clientseitige Blazor WebAssembly-Skript verwendet wird:

     ```cshtml
     <script src="_framework/blazor.webassembly.js"></script>
     ```

1. In `Startup.Configure` (`Startup.cs`) des Serverprojekts:

   * Rufen Sie `UseDeveloperExceptionPage` für den App-Generator in der Entwicklungsumgebung auf.
   * Rufen Sie `UseBlazorFrameworkFiles` für den App-Generator auf.
   * Ändern Sie das Fallback von der Seite `index.html` (`endpoints.MapFallbackToFile("index.html");`) in die Seite `_Host.cshtml`.

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       if (env.IsDevelopment())
       {
           app.UseDeveloperExceptionPage();
           app.UseWebAssemblyDebugging();
       }
       else
       {
           app.UseExceptionHandler("/Error");
           app.UseHsts();
       }

       app.UseHttpsRedirection();
       app.UseBlazorFrameworkFiles();
       app.UseStaticFiles();

       app.UseRouting();

       app.UseEndpoints(endpoints =>
       {
           endpoints.MapRazorPages();
           endpoints.MapControllers();
           endpoints.MapFallbackToPage("/_Host");
       });
   }
   ```

## <a name="render-components-in-a-page-or-view-with-the-component-tag-helper"></a>Rendern von Komponenten auf einer Seite oder in einer Ansicht mit dem Hilfsprogramm für Komponententags

Das Hilfsprogramm für Komponententags unterstützt zwei Rendermodi zum Rendern einer Komponente aus einer Blazor WebAssembly-App auf einer Seite oder in einer Ansicht:

* `WebAssembly`: Rendert einen Marker für eine Blazor WebAssembly-App zur Einbindung einer interaktiven Komponente, wenn diese im Browser geladen wird. Die Komponente wird nicht vorab gerendert. Diese Option erleichtert das Rendern verschiedener Blazor WebAssembly-Komponenten auf verschiedenen Seiten.
* `WebAssemblyPrerendered`: Rendert die Komponente vorab in statischen HTML-Code und enthält einen Marker für eine Blazor WebAssembly-App zur späteren Verwendung, um die Komponente nach Laden in den Browser interaktiv zu gestalten.

Im folgenden Razor Pages-Beispiel wird die Komponente `Counter` auf einer Seite gerendert. Um die Komponente interaktiv zu gestalten, ist das Blazor WebAssembly-Skript im [Renderabschnitt](xref:mvc/views/layout#sections) der Seite enthalten. Um die Verwendung des vollständigen Namespaces für die Komponente `Counter` mit dem Hilfsprogramm für Komponententags (`{APP ASSEMBLY}.Pages.Counter`) zu vermeiden, fügen Sie eine [`@using`](xref:mvc/views/razor#using)-Anweisung dem Namespace `Pages` der Client-App hinzu. Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:

```cshtml
...
@using BlazorHosted.Client.Pages

<h1>@ViewData["Title"]</h1>

<component type="typeof(Counter)" render-mode="WebAssemblyPrerendered" />

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

<xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode> konfiguriert folgende Einstellungen für die Komponente:

* Ob die Komponente zuvor für die Seite gerendert wird
* Ob die Komponente als statische HTML auf der Seite gerendert wird oder ob sie die nötigen Informationen für das Bootstrapping einer Blazor-App über den Benutzer-Agent enthält.

Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

Das vorherige Beispiel erfordert, dass das Layout (`_Layout.cshtml`) der Server-App innerhalb des schließenden `</body>`-Tags einen [Renderabschnitt](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) für das Skript enthält:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.

Wenn die App auch Komponenten mit den Stilen in der Blazor WebAssembly-App formatieren soll, binden Sie die Stile der Anwendung in die Datei `_Layout.cshtml` ein. Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

## <a name="render-components-in-a-page-or-view-with-a-css-selector"></a>Rendern von Komponenten auf einer Seite oder in einer Ansicht mit dem CSS-Selektor

Fügen Sie dem *Client*-Projekt in `Program.Main` (`Program.cs`) Stammkomponenten hinzu. Im folgenden Beispiel wird die Komponente `Counter` als Stammkomponente mit einem CSS-Selektor deklariert, der das Element mit der `id` auswählt, die mit `my-counter` übereinstimmt. Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:

```csharp
using BlazorHosted.Client.Pages;

...

builder.RootComponents.Add<Counter>("#my-counter");
```

Im folgenden Razor Pages-Beispiel wird die Komponente `Counter` auf einer Seite gerendert. Um die Komponente interaktiv zu gestalten, ist das Blazor WebAssembly-Skript im [Renderabschnitt](xref:mvc/views/layout#sections) der Seite enthalten:

```cshtml
...

<h1>@ViewData["Title"]</h1>

<div id="my-counter">Loading...</div>

@section Scripts {
    <script src="_framework/blazor.webassembly.js"></script>
}
```

Das vorherige Beispiel erfordert, dass das Layout (`_Layout.cshtml`) der Server-App innerhalb des schließenden `</body>`-Tags einen [Renderabschnitt](xref:mvc/views/layout#sections) (<xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.RenderSection%2A>) für das Skript enthält:

```cshtml
    ...

    @RenderSection("Scripts", required: false)
</body>
```

Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.

Wenn die App auch Komponenten mit den Stilen in der Blazor WebAssembly-App formatieren soll, binden Sie die Stile der Anwendung in die Datei `_Layout.cshtml` ein. Im folgenden Beispiel ist der Namespace der Client-App `BlazorHosted.Client`:

```cshtml
<head>
    ...

    <link href="css/app.css" rel="stylesheet" />
    <link href="BlazorHosted.Client.styles.css" rel="stylesheet" />
</head>
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Das Integrieren von Razor-Komponenten in Razor Pages- und MVC-Apps in einer gehosteten Blazor WebAssembly-Lösung wird in ASP.NET Core ab .NET 5 unterstützt. Wählen Sie eine .NET 5- oder neuere Version dieses Artikels aus.

::: moniker-end

::: zone-end

::: zone pivot="server"

Razor-Komponenten können in einer Blazor Server-App in Razor Pages- und MVC-Apps integriert werden. Wenn die Seite oder Ansicht gerendert wird, können Komponenten gleichzeitig vorab gerendert werden.

Nachdem Sie [die App konfiguriert haben](#configuration), befolgen Sie die Anleitungen in den folgenden Abschnitten abhängig von den Anforderungen der App:

* Routingfähige Komponenten: Die folgenden Abschnitte beziehen sich auf Komponenten, die über Benutzeranforderungen direkt routingfähig sind. Befolgen Sie diese Anleitung, wenn Besucher in der Lage sein sollen, in ihrem Browser eine HTTP-Anforderung für eine Komponente mit einer [`@page`](xref:mvc/views/razor#page)-Direktive zu erstellen.
  * [Verwenden routingfähiger Komponenten in einer Razor Pages-App](#use-routable-components-in-a-razor-pages-app)
  * [Verwenden routingfähiger Komponenten in einer MVC-App](#use-routable-components-in-an-mvc-app)
* [Rendern von Komponenten einer Seite oder Ansicht:](#render-components-from-a-page-or-view) Dieser Abschnitt bezieht sich auf Komponenten, die nicht über Benutzeranforderungen direkt routingfähig sind. Befolgen Sie diese Anleitung, wenn die App Komponenten mit dem [Taghilfsprogramm für Komponenten](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper) in vorhandene Seiten und Ansichten einbettet.

## <a name="configuration"></a>Konfiguration

Eine vorhandene Razor Pages- oder MVC-App kann Razor-Komponenten in Seiten und Ansichten integrieren:

1. In der Layoutdatei der App (`_Layout.cshtml`):

   * Fügen Sie das folgende `<base>`-Tag zum `<head>`-Element hinzu:

     ```html
     <base href="~/" />
     ```

     Der `href`-Wert (der *App-Basispfad*) im obigen Beispiel setzt voraus, dass die App sich im URL-Stammpfad (`/`) befindet. Wenn es sich bei der App um eine untergeordnete Anwendung handelt, befolgen Sie die Anweisungen im Abschnitt *App-Basispfad* des <xref:blazor/host-and-deploy/index#app-base-path>-Artikels.

     Die Datei `_Layout.cshtml` befindet sich bei Razor-Pages-Apps im Ordner `Pages/Shared` und bei MVC-Apps im Ordner `Views/Shared`.

   * Fügen Sie ein `<script>`-Tag für das `blazor.server.js`-Skript unmittelbar vor dem Renderabschnitt `Scripts` hinzu:

     ```html
         ...
         <script src="_framework/blazor.server.js"></script>

         @await RenderSectionAsync("Scripts", required: false)
     </body>
     ```

     Das Framework fügt das Skript `blazor.server.js` zur App hinzu. Das Skript muss nicht manuell zur App hinzugefügt werden.

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

1. Befolgen Sie die Anweisungen im Abschnitt [Configuration](#configuration).

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

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

1. Befolgen Sie die Anweisungen im Abschnitt [Configuration](#configuration).

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

   [!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

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

   Weitere Informationen zum Hilfsprogramm für Komponententags, einschließlich Übergabe von Parametern und Konfiguration von <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode>, finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>.

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
* Wenn die Komponenten nicht in einem Ordner namens `Components` enthalten sind, ändern Sie `Components` in den Namen des Ordners, in dem sich die Komponenten befinden.

```cshtml
@using MyAppNamespace.Components
```

Die Datei `_ViewImports.cshtml` befindet sich im Ordner `Pages` einer Razor-Pages-App oder im Ordner `Views` einer MVC-App.

Weitere Informationen finden Sie unter <xref:blazor/components/index#namespaces>.

::: zone-end
