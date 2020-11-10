---
title: Übersicht über ASP.NET Core MVC
author: ardalis
description: Informationen zu ASP.NET Core MVC als umfangreiches Framework zum Erstellen von Web-Apps und APIs mithilfe des Model-View-Controller-Entwurfsmusters
ms.author: riande
ms.date: 02/12/2020
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
uid: mvc/overview
ms.openlocfilehash: 4f0e98057c03769a672ef350cc3ec724a4f7e721
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431056"
---
# <a name="overview-of-aspnet-core-mvc"></a>Übersicht über ASP.NET Core MVC

Von [Steve Smith](https://ardalis.com/)

ASP.NET Core MVC ist ein umfangreiches Framework zum Erstellen von Web-Apps und APIs mithilfe des Model-View-Controller-Entwurfsmusters.

## <a name="what-is-the-mvc-pattern"></a>Was ist das MVC-Muster?

Das Architekturmuster Model-View-Controller (MVC) unterteilt eine Anwendung in drei Hauptkomponentengruppen: Modelle (Models), Ansichten (Views) und Controller (Controllers). Dieses Muster erleichtert die [Trennung von Belangen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns). Mit diesem Muster werden Benutzeranforderungen an einen Controller weitergeleitet. Der Controller arbeitet mit dem Modell, um Benutzeraktionen auszuführen und/oder Ergebnisse von Abfragen abzurufen. Der Controller wählt die Ansicht, die dem Benutzer angezeigt wird, und stellt sämtliche erforderliche Modelldaten dafür bereit.

Die folgende Abbildung zeigt die drei Hauptkomponenten und deren Beziehungen untereinander:

![MVC-Muster](overview/_static/mvc.png)

Diese Abgrenzung der Aufgaben erleichtert die Skalierung Ihrer Anwendung hinsichtlich der Komplexität, da es einfacher ist, ein Element zu codieren, zu debuggen und zu testen (das Modell, die Ansicht oder den Controller), das nur eine einzige Aufgabe hat. Deutlich schwieriger ist es, Code zu aktualisieren, zu testen und zu debuggen, der Abhängigkeiten in zwei oder drei dieser Bereiche aufweist. Benutzeroberflächenlogik ändert sich beispielsweise häufiger als Geschäftslogik. Werden Präsentationscode und Geschäftslogik in einem einzigen Objekt kombiniert, muss ein Objekt mit Geschäftslogik jedes Mal geändert werden, wenn die Benutzeroberfläche geändert wird. Dies führt häufig zu Fehlermeldungen. Außerdem muss die Geschäftslogik nach jeder kleinen Änderung der Benutzeroberfläche erneut getestet werden.

> [!NOTE]
> Sowohl die Ansicht als auch der Controller sind abhängig vom Modell. Das Modell ist jedoch weder von der Ansicht noch vom Controller abhängig. Hierin besteht einer der Hauptvorteile der Trennung. Aufgrund dieser Trennung kann das Modell unabhängig von der visuellen Darstellung erstellt und getestet werden.

### <a name="model-responsibilities"></a>Aufgaben des Modells

Das Modell stellt in einer MVC-Anwendung den Status der Anwendung und sämtlicher Vorgänge oder Geschäftslogik dar, die von ihm ausgeführt werden sollen. Im Modell sollte Geschäftslogik zusammen mit der gesamten Implementierungslogik gekapselt werden, um den Status der Anwendung beizubehalten. Stark typisierte Ansichten verwenden in der Regel ViewModel-Typen, die die Daten für diese Ansicht enthalten. Der Controller erstellt und füllt diese ViewModel-Instanzen aus dem Modell.

### <a name="view-responsibilities"></a>Aufgaben der Ansicht

Ansichten dienen der Darstellung von Inhalt über die Benutzeroberfläche. Sie verwenden die [ Razor Ansichts-Engine](#razor-view-engine) zum Einbetten von .NET-Code in HTML-Markup. Ansichten sollten so wenig Logik wie möglich enthalten. Die enthaltene Logik sollte sich zudem auf die Darstellung von Inhalt beziehen. Sollten Sie dennoch viel Logik in Ansichtsdateien ausführen müssen, um Daten aus einem komplexen Modell anzuzeigen, ist es sinnvoll, eine [Ansichtskomponente](views/view-components.md), ViewModel oder eine Ansichtsvorlage zu verwenden, um die Ansicht zu vereinfachen.

### <a name="controller-responsibilities"></a>Aufgaben des Controllers

Controller sind Komponenten, die Benutzerinteraktionen verarbeiten, mit dem Modell arbeiten und letztlich eine Ansicht auswählen, die gerendert werden soll. In einer MVC-Anwendung zeigt die Ansicht nur Informationen an. Benutzereingaben und -interaktionen werden vom Controller verarbeitet und beantwortet. Im MVC-Muster stellt der Controller den Einstiegspunkt dar. Er ist verantwortlich für die Auswahl des Modells, mit dem gearbeitet wird, sowie der Ansicht, die gerendert wird (daher kommt auch sein Name: Der Controller kontrolliert, wie die App auf eine bestimmte Anforderung reagiert).

> [!NOTE]
> Controller sollten nicht durch zu viele Aufgaben übermäßig kompliziert gemacht werden. Um zu verhindern, dass die Controllerlogik zu komplex wird, verlagern Sie die Geschäftslogik aus dem Controller in das Domänenmodell.

>[!TIP]
> Wenn Sie feststellen, dass Ihre Controlleraktionen häufig die gleiche Art von Aktionen ausführen, verschieben diese häufig ausgeführten Aktionen in [Filter](#filters).

## <a name="what-is-aspnet-core-mvc"></a>Was ist ASP.NET Core MVC?

Das ASP.NET Core MVC-Framework ist ein einfaches, äußerst testfähiges Open-Source-Präsentationsframework, das für die Verwendung mit ASP.NET Core optimiert wurde.

ASP.NET Core MVC stellt eine auf Mustern basierte Methode zum Entwickeln dynamischer Websites dar, die eine saubere Trennung von Belangen ermöglicht. Es ermöglicht Ihnen die vollständige Kontrolle über das Markup, unterstützt eine TDD-freundliche Entwicklung und verwendet die aktuellsten Webstandards.

## <a name="features"></a>Funktionen

Zu ASP.NET Core MVC gehören folgende Elemente:

* [Routing](#routing)
* [Modell Bindung](#model-binding)
* [Modell Validierung](#model-validation)
* [Dependency Injection](../fundamentals/dependency-injection.md)
* [Filter](#filters)
* [Bereiche](#areas)
* [Web-APIs](#web-apis)
* [Prüfbarkeit](#testability)
* [Razor Anzeige-Engine](#razor-view-engine)
* [Stark typisierte Ansichten](#strongly-typed-views)
* [Taghilfsprogramme](#tag-helpers)
* [Komponenten anzeigen](#view-components)

### <a name="routing"></a>Routing

ASP.NET Core MVC baut auf dem [Routing von ASP.NET Core](../fundamentals/routing.md) auf, einer leistungsstarken URL-Zuordnungskomponente, mit der Sie Anwendungen mit verständlichen und suchbaren URLs erstellen können. Damit können Sie die URL-Benennungsmuster für Ihre Anwendung definieren, die sich für die Suchmaschinenoptimierung (SEO) und die Linkgenerierung eignen, ohne dass die Organisation der Dateien auf Ihrem Webserver berücksichtigt werden muss. Sie können die Routen mithilfe einer geeigneten Syntax für Routenvorlagen definieren, die Routenwerteinschränkungen, Standardwerte sowie optionale Werte unterstützt.

Mit dem *auf Konventionen basierten Routing* können Sie global definieren, welche URL-Formate Ihre Anwendung akzeptiert, und wie diese Formate einer bestimmten Aktionsmethode auf dem angegebenen Controller zugeordnet werden. Wird eine eingehende Anforderung empfangen, analysiert die Routingengine die URL und vergleicht sie mit einer der definierten URL-Formate. Anschließend wird die zugeordnete Aktionsmethode des Controllers aufgerufen.

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

*Attributrouting* ermöglicht Ihnen die Angabe von Routinginformationen, indem die Controller und Aktionen mit Attributen versehen werden, die die Routen für Ihre Anwendung definieren. Das bedeutet, dass Ihre Routendefinitionen neben den Controller und die Aktion platziert werden, denen sie zugeordnet sind.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
    [HttpGet("{id}")]
    public IActionResult GetProduct(int id)
    {
      ...
    }
}
```

### <a name="model-binding"></a>Modellbindung

Die [Modellbindung](models/model-binding.md) von ASP.NET Core MVC konvertiert Clientanforderungsdaten (Formularwerte, Routendaten, Abfragezeichenfolgen-Parameter, HTTP-Header) in Objekte, die der Controller verarbeiten kann. Dadurch muss Ihre Controllerlogik nicht mehr die eingehenden Anforderungsdaten ermitteln. Die Daten sind bereits als Parameter für die Aktionsmethoden vorhanden.

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a>Modellvalidierung

ASP.NET Core MVC unterstützt die [Validierung](models/validation.md), indem das Modellobjekt mit Validierungsattributen für Datenanmerkungen versehen wird. Die Validierungsattribute werden auf der Clientseite überprüft, bevor Werte auf dem Server bereitgestellt werden. Auf der Serverseite werden sie überprüft, bevor die Controlleraktion aufgerufen wird.

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

Eine Controlleraktion:

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

Das Framework verarbeitet Validierungsanforderungsdaten sowohl auf dem Client als auch auf dem Server. Für Modelltypen angegebene Validierungslogik wird den gerenderten Ansichten als unaufdringliche Anmerkungen hinzugefügt und im Browser mit [jQuery Validation](https://jqueryvalidation.org/) erzwungen.

### <a name="dependency-injection"></a>Dependency Injection

ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](../fundamentals/dependency-injection.md). In ASP.NET Core MVC können [Controller](controllers/dependency-injection.md) benötigte Dienste über ihre Konstruktoren anfordern. So wird das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgt.

Mit der `@inject`-Anweisung kann Ihre App [Dependency Injection auch in Ansichtsdateien](views/dependency-injection.md) verwenden:

```cshtml
@inject SomeService ServiceName

<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a>Filter

Mit [Filtern](controllers/filters.md) können Entwickler übergreifende Belange kapseln, wie etwa die Ausnahmebehandlung oder die Autorisierung. Filter ermöglichen das Ausführen benutzerdefinierter Vor- und Nachverarbeitungslogik für Aktionsmethoden. Sie können so konfiguriert werden, dass sie für eine angegebene Anforderung an bestimmten Stellen in der Ausführungspipeline ausgeführt werden. Filter können als Attribute auf Controller oder Aktionen angewendet werden (oder sie werden global ausgeführt). Das Framework enthält mehrere Filter (wie z.B. `Authorize`). `[Authorize]` ist das Attribut, das zum Erstellen von MVC-Autorisierungsfiltern verwendet wird.

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a>Bereiche

[Bereiche](controllers/areas.md) bieten eine Möglichkeit, eine große ASP.net Core MVC-Web-App in kleinere funktionale Gruppierungen zu partitionieren. Ein Bereich ist eine MVC-Struktur innerhalb einer Anwendung. In einem MVC-Projekt sind logische Komponenten wie Modell, Controller und Ansicht in verschiedenen Ordnern gespeichert. MVC nutzt Namenskonventionen zum Erstellen einer Beziehung zwischen diesen Komponenten. Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen. Beispielsweise eine e-Commerce-App mit mehreren Geschäftseinheiten, z. b. Checkout, Abrechnung und Suche usw. Jede dieser Einheiten verfügt über eigene logische Komponenten Sichten, Controller und Modelle.

### <a name="web-apis"></a>Web-APIs

Als umfangreiche Plattform zum Erstellen von Websites verfügt ASP.NET Core MVC außerdem über umfassende Unterstützung für das Erstellen von Web-APIs. Sie können Dienste für eine breit gefächerte Palette von Clients erstellen, darunter auch Browser und mobile Geräte.

Das Framework beinhaltet Unterstützung für die Aushandlung von HTTP-Inhalt mit integrierter Unterstützung zum [Formatieren von Daten](xref:web-api/advanced/formatting) als JSON oder XML. Sie können [benutzerdefinierte Formatierungsprogramme](xref:web-api/advanced/custom-formatters) schreiben, um Unterstützung für Ihre eigenen Formate hinzuzufügen.

Außerdem können Sie mit der Linkgenerierung Unterstützung für Hypermedia aktivieren. Aktivieren Sie auf einfache Weise Unterstützung für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/), damit Ihre Web-APIs für mehrere Webanwendungen freigegeben werden.

### <a name="testability"></a>Prüfbarkeit

Durch die Verwendung von Schnittstellen und der Abhängigkeitsinjektion eignet sich das Framework besonders gut für Komponententests. Es enthält Features (wie etwa einen TestHost- und InMemory-Anbieter für Entity Framework), mit denen auch [Integrationstests](xref:test/integration-tests) schnell und einfach durchgeführt werden können. Weitere Informationen hierzu finden Sie unter [Testen der Controllerlogik](controllers/testing.md).

### <a name="no-locrazor-view-engine"></a>Razor Anzeige-Engine

[ASP.net Core MVC-Ansichten](views/overview.md) verwenden Sie die [ Razor Ansichts-Engine](views/razor.md) zum renderingansichten. Razor ist eine kompakte, ausdrucksstarke und fließende Vorlagen Markup Sprache zum Definieren von Sichten mithilfe von eingebettetem c#-Code. Razor wird verwendet, um Webinhalte auf dem Server dynamisch zu generieren. Sie können Servercode sauber mit Inhalt und Code der Clientseite kombinieren.

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

Mithilfe der Razor Ansichts-Engine können Sie [Layouts](views/layout.md), [Teilansichten](views/partial.md) und austauschbare Abschnitte definieren.

### <a name="strongly-typed-views"></a>Stark typisierte Ansichten

Razor Sichten in MVC können basierend auf dem Modell stark typisiert werden. Controller können ein stark typisiertes Modell an die Ansichten übergeben, um Typüberprüfung und IntelliSense-Unterstützung für Ihre Ansichten zu aktivieren.

Die folgende Ansicht rendert beispielweise ein Modell vom Typ `IEnumerable<Product>`:

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a>Taghilfsprogramme

[Taghilfsprogramme](views/tag-helpers/intro.md) ermöglichen es dem serverseitigen Code, an der Erstellung und dem Rendern von HTML-Elementen in Razor Dateien teilzunehmen. Mit Taghilfsprogrammen können Sie benutzerdefinierte Tags anpassen (z.B. `<environment>`) oder das Verhalten von vorhandenen Tags ändern (z.B. `<label>`). Taghilfsprogramme sind an bestimmte Elemente basierend auf dem Elementnamen und dessen Attributen gebunden. Sie bieten gleichzeitig die Vorteile des serverseitigen Renderns sowie der HTML-Bearbeitungsmöglichkeiten.

Für häufige Aufgaben wie das Erstellen von Formularen und Links sowie das Laden von Objekten gibt es zahlreiche integrierte Taghilfsprogramme. Weitere Taghilfsprogramme sind in öffentlichen GitHub-Repositorys und als NuGet-Pakete verfügbar. Taghilfsprogramme werden in C# erstellt und sind für HTML-Elemente basierend auf dem Elementnamen, dem Attributnamen oder dem übergeordneten Tag konzipiert. Der integrierte LinkTagHelper kann z.B. verwendet werden, um einen Link zur `Login`-Aktion des `AccountsController` zu erstellen:

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

Mit `EnvironmentTagHelper` können Sie Ihren Ansichten verschiedene Skripts hinzufügen (z.B. minimiert oder RAW), basierend auf der Laufzeitumgebung, wie Entwicklung, Staging oder Produktion:

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

Taghilfsprogramme bieten eine HTML-freundliche Entwicklungsumgebung und eine umfangreiche IntelliSense-Umgebung zum Erstellen von HTML und Razor Markup. Die meisten integrierten Taghilfsprogramme sind für vorhandene HTML-Elemente konzipiert und stellen serverseitige Attribute für die jeweiligen Elemente bereit.

### <a name="view-components"></a>Ansichtskomponenten

Mit [Ansichtskomponenten](views/view-components.md) können Sie Renderinglogik packen und in der gesamten Anwendung wiederverwenden. Ansichtskomponenten ähneln [Teilansichten](views/partial.md), enthalten jedoch zugeordnete Logik.

## <a name="compatibility-version"></a>Kompatibilitätsversion

Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen.

Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Mytesting. aspnetcore. MVC-fließende Test Bibliothek für ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): stark typisierte unittestbibliothek, die eine fließende Oberfläche zum Testen von MVC-und Web-API-apps bereitstellt. ( *Wird von Microsoft nicht verwaltet oder unterstützt.* )
* <xref:blazor/components/prerendering-and-integration>
