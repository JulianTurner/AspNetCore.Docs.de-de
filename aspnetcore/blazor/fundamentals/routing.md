---
title: ASP.NET Core Blazor-Routing
author: guardrex
description: Erfahren Sie, wie Sie das Anforderungsrouting in Apps verwalten und wie Sie die NavLink-Komponente in Blazor-Apps zur Navigation verwenden können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2020
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
uid: blazor/fundamentals/routing
ms.openlocfilehash: 74886eae7431045e56de535b5221040bd56cdc2d
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751567"
---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor-Routing

Von [Luke Latham](https://github.com/guardrex)

In diesem Artikel erfahren Sie, wie Sie das Anforderungsrouting verwalten und die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verwenden können, um Navigationslinks in Blazor-Apps zu erstellen.

## <a name="route-templates"></a>Routenvorlagen

Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht das Routing an Razor-Komponenten in einer Blazor-App. Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente wird in der `App`-Komponente von Blazor-Apps verwendet.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App1.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App1.razor)]

::: moniker-end

Wenn eine Razor-Komponente (`.razor`) mit einer [`@page`-Anweisung](xref:mvc/views/razor#page) kompiliert wird, wird für die generierte Komponentenklasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Objekt bereitgestellt, das die Routenvorlage der Komponente angibt.

Wenn die App gestartet wird, wird die Assembly gescannt, die als `AppAssembly`-Objekt des Routers angegeben wurde, um Routeninformationen für die Komponenten der App zu erfassen, die über ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Objekt verfügen.

Zur Laufzeit führt die <xref:Microsoft.AspNetCore.Components.RouteView>-Komponente Folgendes aus:

* Sie empfängt das <xref:Microsoft.AspNetCore.Components.RouteData>-Objekt vom <xref:Microsoft.AspNetCore.Components.Routing.Router>-Objekt zusammen mit den Routenparametern.
* Sie rendert die angegebene Komponente mit deren [Layout](xref:blazor/layouts), einschließlich aller weiteren geschachtelten Layouts.

Optional können Sie einen <xref:Microsoft.AspNetCore.Components.RouteView.DefaultLayout>-Parameter mit einer Layoutklasse für Komponenten angeben, die kein Layout mit der [`@layout`-Anweisung](xref:blazor/layouts#specify-a-layout-in-a-component) festlegen. Die Blazor-Projektvorlagen des Frameworks geben die `MainLayout`-Komponente (`Shared/MainLayout.razor`) als Standardlayout der App an. Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.

Komponenten unterstützen mehrere Routenvorlagen mithilfe mehrerer [`@page`-Anweisungen](xref:mvc/views/razor#page). Die folgende Beispielkomponente lädt Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`.

`Pages/BlazorRoute.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/BlazorRoute.razor?highlight=1-2)]

::: moniker-end

> [!IMPORTANT]
> Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer `wwwroot/index.html`-Datei (Blazor WebAssembly) oder `Pages/_Host.cshtml`-Datei (Blazor Server) mit dem im `href`-Attribut angegebenen App-Basispfad enthalten. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde

Die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.

In der `App`-Komponente können Sie benutzerdefinierten Inhalt in der <xref:Microsoft.AspNetCore.Components.Routing.Router>-Vorlage der <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Komponente festlegen.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App2.razor?highlight=5-8)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App2.razor?highlight=5-8)]

::: moniker-end

Beliebige Elemente werden als Inhalt der `<NotFound>`-Tags unterstützt, z. B. andere interaktive Komponenten. Informationen zum Anwenden eines Standardlayouts auf <xref:Microsoft.AspNetCore.Components.Routing.Router.NotFound>-Inhalte finden Sie unter <xref:blazor/layouts#default-layout>.

## <a name="route-to-components-from-multiple-assemblies"></a>Weiterleiten an Komponenten aus mehreren Assemblys

Verwenden Sie den <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Parameter, um zusätzliche Assemblys anzugeben, die die <xref:Microsoft.AspNetCore.Components.Routing.Router>-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll. Zusätzliche Assemblys werden neben der für `AppAssembly` angegebenen Assembly gescannt. Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer [Komponentenklassenbibliothek](xref:blazor/components/class-libraries) definiert ist, auf die verwiesen wird. Im folgenden <xref:Microsoft.AspNetCore.Components.Routing.Router.AdditionalAssemblies>-Beispiel wird Routing für `Component1` unterstützt.

`App.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/App3.razor)]

::: moniker-end

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/App3.razor)]

::: moniker-end

## <a name="route-parameters"></a>Routenparameter

Der Router verwendet Routenparameter, um die entsprechenden [Komponentenparameter](xref:blazor/components/index#component-parameters) mit demselben Namen aufzufüllen. Bei den Routenparameternamen muss die Groß- und Kleinschreibung nicht berücksichtigt werden. Im folgenden Beispiel weist der Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu. Wenn eine Anforderung für `/RouteParameter/amazing` erfolgt, wird der Inhalt der `<h1>`-Tags als `Blazor is amazing!` gerendert.

`Pages/RouteParameter.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter1.razor?highlight=1)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

Optionale Parameter werden unterstützt. Im folgenden Beispiel weist der optionale Parameter `text` den Wert des Routensegments der Eigenschaft `Text` der Komponente zu. Wenn das Segment nicht vorhanden ist, wird der Wert von `Text` auf `fantastic` festgelegt.

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/5.x/RouteParameter2.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Optionale Parameter werden nicht unterstützt. Im folgenden Beispiel werden zwei [`@page`-Anweisungen](xref:mvc/views/razor#page) angewendet. Die erste Anweisung ermöglicht die Navigation zur Komponente ohne einen Parameter. Die zweite Direktive weist der `Text`-Eigenschaft der Komponente den Wert für die `{text}`-Routenparameter zu.

`Pages/RouteParameter.razor`:

[!code-razor[](routing/samples_snapshot/3.x/RouteParameter2.razor?highlight=2)]

::: moniker-end

Verwenden Sie [`OnParametersSet`](xref:blazor/components/lifecycle#after-parameters-are-set) anstelle von [`OnInitialized`](xref:blazor/components/lifecycle#component-initialization-methods), um die App-Navigation zur gleichen Komponente mit einem anderen optionalen Parameterwert zuzulassen. Verwenden Sie auf der Grundlage des vorangehenden Beispiels `OnParametersSet`, wenn der Benutzer in der Lage sein soll, von `/RouteParameter` zu `/RouteParameter/amazing` oder von `/RouteParameter/amazing` zu `/RouteParameter` zu navigieren:

```csharp
protected override void OnParametersSet()
{
    Text = Text ?? "fantastic";
}
```

## <a name="route-constraints"></a>Routeneinschränkungen

Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.

Im folgenden Beispiel stimmt die Route zur `User`-Komponente nur überein, wenn:

* ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.
* das `Id`-Segment ein Integer (`int`) ist.

`Pages/User.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/User.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/User.razor?highlight=1)]

::: moniker-end

Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar. Informationen zu den Routeneinschränkungen der invarianten Kultur finden Sie in der Warnung unter der Tabelle.

| Constraint | Beispiel           | Beispiele für Übereinstimmungen                                                                  | Invariante<br>Kultur<br>Übereinstimmend |
| ---------- | ----------------- | -------------------------------------------------------------------------------- | :------------------------------: |
| `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nein                               |
| `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ja                              |
| `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ja                              |
| `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ja                              |
| `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ja                              |
| `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nein                               |
| `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ja                              |
| `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ja                              |

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder <xref:System.DateTime>), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.

## <a name="routing-with-urls-that-contain-dots"></a>Routing mit URLs, die Punkte enthalten

Wenn das letzte Segment einer Anforderungs-URL einen Punkt (`.`) enthält, wird bei gehosteten Blazor WebAssembly- und Blazor Server-Apps von der serverseitigen Standardvorlage für Routen davon ausgegangen, dass eine Datei angefordert wird. Beispielsweise wird die URL `https://localhost.com:5001/example/some.thing` vom Router als Anforderung für eine Datei mit dem Namen `some.thing` interpretiert. Ohne zusätzliche Konfiguration gibt eine App die Antwort *404 – Nicht gefunden* zurück, wenn `some.thing` an eine Komponente mit einer [`@page`-Anweisung](xref:mvc/views/razor#page) weiterleiten sollte und `some.thing` ein Routenparameterwert ist. Wenn eine Route mit mindestens einem Parameter verwendet werden soll, der einen Punkt enthält, muss die Route von der App mit einer benutzerdefinierten Vorlage konfiguriert werden.

Sehen Sie sich z. B. die folgende `Example`-Komponente an, die einen Routenparameter aus dem letzten Segment der URL empfangen kann.

`Pages/Example.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Example.razor?highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Example.razor?highlight=2)]

::: moniker-end

Wenn Sie möchten, dass die *`Server`* -App einer gehosteten Blazor WebAssembly-Lösung die Anforderung mit einem Punkt im `param`-Routenparameter weiterleiten kann, fügen Sie eine Ausweich-Dateiroutenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` befindet.

`Startup.cs`:

```csharp
endpoints.MapFallbackToFile("/example/{param?}", "index.html");
```

Fügen Sie eine Ausweichdatei-Routenvorlage hinzu, wobei sich der optionale Parameter in `Startup.Configure` befindet, um eine Blazor Server-App so zu konfigurieren, dass die Anforderung mit einem Punkt im `param`-Routenparameter weiterleitet wird.

`Startup.cs`:

```csharp
endpoints.MapFallbackToPage("/example/{param?}", "/_Host");
```

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="catch-all-route-parameters"></a>Catch-All-Routenparameter

::: moniker range=">= aspnetcore-5.0"

Catch-All-Routenparameter, die Pfade über mehrere Ordnergrenzen hinweg erfassen, werden in Komponenten unterstützt.

Für Catch-All-Routenparameter gilt:

* Sie müssen so benannt werden, dass sie dem Routensegmentnamen entsprechen. Die Groß-/Kleinschreibung muss bei der Benennung nicht beachtet werden.
* Ein `string`-Typ. Im Framework steht keine automatische Übertragung zur Verfügung.
* Am Ende der URL.

`Pages/CatchAll.razor`:

[!code-razor[](routing/samples_snapshot/5.x/CatchAll.razor)]

Bei der URL `/catch-all/this/is/a/test` mit der Routenvorlage `/catch-all/{*pageRoute}` wird der Wert für `PageRoute` auf `this/is/a/test` festgelegt.

Schrägstriche und Segmente des erfassten Pfads werden decodiert. Bei der Routenvorlage `/catch-all/{*pageRoute}` hält die URL `/catch-all/this/is/a%2Ftest%2A` `this/is/a/test*` an.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Catch-All-Routenparameter werden ab ASP.NET Core 5.0 unterstützt. Weitere Informationen finden Sie im Artikel zur 5.0-Version.

::: moniker-end

## <a name="uri-and-navigation-state-helpers"></a>Hilfsprogramme für URI und Navigationszustand

Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um URIs und die Navigation im C#-Code zu verwalten. <xref:Microsoft.AspNetCore.Components.NavigationManager> stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.

| Member | Beschreibung |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri> | Ruft den aktuellen absoluten URI ab. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten. In der Regel entspricht <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> dem `href`-Attribut im `<base>`-Element des Dokuments in `wwwroot/index.html` (Blazor WebAssembly) oder `Pages/_Host.cshtml` (Blazor Server). |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> | Navigiert zum angegebenen URI. Bei `forceLoad` lautet der Wert `true`:<ul><li>Clientseitiges Routing wird umgangen.</li><li>Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</li></ul> |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged> | Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager.ToAbsoluteUri%2A> | Konvertiert einen relativen URI in einen absoluten URI. |
| <span style="word-break:normal;word-wrap:normal"><xref:Microsoft.AspNetCore.Components.NavigationManager.ToBaseRelativePath%2A></span> | Wenn ein Basis-URI (z. B. ein URI, der zuvor von <xref:Microsoft.AspNetCore.Components.NavigationManager.BaseUri> zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert. |

Für das <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged>-Ereignis bietet <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> die folgenden Informationen zu Navigationsereignissen:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location>: Die URL des neuen Speicherorts.
* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted>: Wenn der Wert `true` ist, hat Blazor die Navigation vom Browser abgefangen. Wenn der Wert `false` ist, hat <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> bewirkt, dass die Navigation erfolgt ist.

Die folgende Komponente führt folgende Aktionen aus:

* Sie navigiert zur `Counter`-Komponente der App (`Pages/Counter.razor`), wenn die Schaltfläche mit <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A> geklickt wird.
* Sie reagiert auf das „Location Changed“-Ereignis, indem sie <xref:Microsoft.AspNetCore.Components.NavigationManager.LocationChanged?displayProperty=nameWithType> abonniert.
  * Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird. Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.
  * Die Protokollierungsimplementierung protokolliert die folgenden Informationen, wenn die Schaltfläche geklickt wird:

    > `BlazorSample.Pages.Navigate: Information: URL of new location: https://localhost:5001/counter`

`Pages/Navigate.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/Navigate.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/Navigate.razor)]

::: moniker-end

Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

## <a name="query-string-and-parse-parameters"></a>Abfragezeichenfolgen und Analysieren von Parametern

Die Abfragezeichenfolge einer Anforderung wird von der <xref:Microsoft.AspNetCore.Components.NavigationManager.Uri?displayProperty=nameWithType>-Eigenschaft abgerufen:

```razor
@inject NavigationManager NavigationManager

...

var query = new Uri(NavigationManager.Uri).Query;
```

So analysieren Sie die Parameter einer Abfragezeichenfolge:

* Eine App kann die <xref:Microsoft.AspNetCore.WebUtilities>-API verwenden. Wenn die API für die App nicht verfügbar ist, fügen Sie in der Projektdatei der App einen Paketverweis für [Microsoft.AspNetCore.WebUtilities](https://www.nuget.org/packages/Microsoft.AspNetCore.WebUtilities) hinzu.
* Nach dem Analysieren der Abfragezeichenfolge mit <xref:Microsoft.AspNetCore.WebUtilities.QueryHelpers.ParseQuery%2A?displayProperty=nameWithType> rufen Sie den Wert ab.

Im folgenden `ParseQueryString`-Komponentenbeispiel wird ein Parameterschlüssel einer Abfragezeichenfolge mit dem Namen `ship` analysiert. Das Schlüssel-Wert-Paar für die URL-Abfragezeichenfolge `?ship=Tardis` erfasst z. B. den Wert `Tardis` in `queryValue`. Navigieren Sie im folgenden Beispiel mit der URL `https://localhost:5001/parse-query-string?ship=Tardis` zur App.

`Pages/ParseQueryString.razor`:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/ParseQueryString.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/ParseQueryString.razor)]

::: moniker-end

## <a name="navlink-and-navmenu-components"></a>`NavLink`- und `NavMenu`-Komponenten

Verwenden Sie bei der Erstellung von Navigationslinks eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`). Eine <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt. Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist. Optional können Sie <xref:Microsoft.AspNetCore.Components.Routing.NavLink.ActiveClass?displayProperty=nameWithType> einen CSS-Klassennamen zuweisen, um eine benutzerdefinierte CSS-Klasse auf den gerenderten Link anzuwenden, wenn die aktuelle Route mit `href` übereinstimmt.

Die folgende `NavMenu`-Komponente erstellt eine [`Bootstrap`](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponenten verwendet werden:

::: moniker range=">= aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/5.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

::: moniker-end

> [!NOTE]
> Die `NavMenu`-Komponente (`NavMenu.razor`) wird im Ordner `Shared` einer App bereitgestellt, die aus den Blazor-Projektvorlagen generiert wurde.

Es gibt zwei <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch>-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:

* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.All?displayProperty=nameWithType>: <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.
* <xref:Microsoft.AspNetCore.Components.Routing.NavLinkMatch.Prefix?displayProperty=nameWithType> (*Standardwert*): <xref:Microsoft.AspNetCore.Components.Routing.NavLink> ist aktiv, wenn ein Präfix mit der aktuellen URL übereinstimmt.

Im vorherigen Beispiel stimmt die Startseite <xref:Microsoft.AspNetCore.Components.Routing.NavLink> `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`). Die zweite <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `component`-Präfix aufruft (z. B. `https://localhost:5001/component` und `https://localhost:5001/component/another-segment`).

Zusätzliche <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponentenattribute werden an das gerenderte Ankertag weitergegeben. Im folgenden Beispiel schließt die <xref:Microsoft.AspNetCore.Components.Routing.NavLink>-Komponente das `target`-Attribut ein:

```razor
<NavLink href="example-page" target="_blank">Example page</NavLink>
```

Das folgende HTML-Markup wird gerendert:

```html
<a href="example-page" target="_blank">Example page</a>
```

> [!WARNING]
> Aufgrund der Art und Weise, in der Blazor untergeordneten Inhalt rendert, erfordert das Rendern von `NavLink`-Komponenten in einer `for`-Schleife eine lokale Indexvariable, wenn die inkrementierende Schleifenvariable im Inhalt der untergeordneten Komponente (`NavLink`) verwendet wird:
>
> ```razor
> @for (int c = 0; c < 10; c++)
> {
>     var current = c;
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @current
>         </NavLink>
>     </li>
> }
> ```
>
> Die Verwendung einer Indexvariable in diesem Szenario ist eine Anforderung für **jede** untergeordnete Komponente, die eine Schleifenvariable im [untergeordneten Inhalt](xref:blazor/components/index#child-content) verwendet, nicht nur für die `NavLink`-Komponente.
>
> Alternativ dazu können Sie eine `foreach`-Schleife mit <xref:System.Linq.Enumerable.Range%2A?displayProperty=nameWithType> verwenden:
>
> ```razor
> @foreach(var c in Enumerable.Range(0,10))
> {
>     <li ...>
>         <NavLink ... href="@c">
>             <span ...></span> @c
>         </NavLink>
>     </li>
> }
> ```

## <a name="aspnet-core-endpoint-routing-integration"></a>Integration von ASP.NET Core-Endpunktrouting

*Dieser Abschnitt gilt nur für Blazor Server-Apps.*

Blazor Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert. Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> in `Startup.Configure` akzeptiert.

`Startup.cs`:

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/5.x/Startup.cs?highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

::: moniker-end

Die typische Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert. Gemäß der Konvention wird die *Hostseite* in der Regel im Ordner `Pages` der App mit `_Host.cshtml` benannt.

Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet. Die Ausweichroute wird verwendet, wenn andere Routen nicht passen. Dadurch kann die App andere Controller und Seiten verwenden, ohne das Komponentenrouting in der Blazor Server-App zu beeinträchtigen.

Informationen zum Konfigurieren von <xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A> für das Serverhosting an einer Nicht-Stamm-URL finden Sie unter <xref:blazor/host-and-deploy/index#app-base-path>.
