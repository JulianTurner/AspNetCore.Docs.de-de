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
ms.openlocfilehash: 642a7b26a3ba0a79a35437a5c19ee0be63a367af
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057316"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="77cfb-103">Übersicht über ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="77cfb-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="77cfb-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="77cfb-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="77cfb-105">ASP.NET Core MVC ist ein umfangreiches Framework zum Erstellen von Web-Apps und APIs mithilfe des Model-View-Controller-Entwurfsmusters.</span><span class="sxs-lookup"><span data-stu-id="77cfb-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="77cfb-106">Was ist das MVC-Muster?</span><span class="sxs-lookup"><span data-stu-id="77cfb-106">What is the MVC pattern?</span></span>

<span data-ttu-id="77cfb-107">Das Architekturmuster Model-View-Controller (MVC) unterteilt eine Anwendung in drei Hauptkomponentengruppen: Modelle (Models), Ansichten (Views) und Controller (Controllers).</span><span class="sxs-lookup"><span data-stu-id="77cfb-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="77cfb-108">Dieses Muster erleichtert die [Trennung von Belangen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span><span class="sxs-lookup"><span data-stu-id="77cfb-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="77cfb-109">Mit diesem Muster werden Benutzeranforderungen an einen Controller weitergeleitet. Der Controller arbeitet mit dem Modell, um Benutzeraktionen auszuführen und/oder Ergebnisse von Abfragen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="77cfb-110">Der Controller wählt die Ansicht, die dem Benutzer angezeigt wird, und stellt sämtliche erforderliche Modelldaten dafür bereit.</span><span class="sxs-lookup"><span data-stu-id="77cfb-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="77cfb-111">Die folgende Abbildung zeigt die drei Hauptkomponenten und deren Beziehungen untereinander:</span><span class="sxs-lookup"><span data-stu-id="77cfb-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC-Muster](overview/_static/mvc.png)

<span data-ttu-id="77cfb-113">Diese Abgrenzung der Aufgaben erleichtert die Skalierung Ihrer Anwendung hinsichtlich der Komplexität, da es einfacher ist, ein Element zu codieren, zu debuggen und zu testen (das Modell, die Ansicht oder den Controller), das nur eine einzige Aufgabe hat.</span><span class="sxs-lookup"><span data-stu-id="77cfb-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="77cfb-114">Deutlich schwieriger ist es, Code zu aktualisieren, zu testen und zu debuggen, der Abhängigkeiten in zwei oder drei dieser Bereiche aufweist.</span><span class="sxs-lookup"><span data-stu-id="77cfb-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="77cfb-115">Benutzeroberflächenlogik ändert sich beispielsweise häufiger als Geschäftslogik.</span><span class="sxs-lookup"><span data-stu-id="77cfb-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="77cfb-116">Werden Präsentationscode und Geschäftslogik in einem einzigen Objekt kombiniert, muss ein Objekt mit Geschäftslogik jedes Mal geändert werden, wenn die Benutzeroberfläche geändert wird.</span><span class="sxs-lookup"><span data-stu-id="77cfb-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="77cfb-117">Dies führt häufig zu Fehlermeldungen. Außerdem muss die Geschäftslogik nach jeder kleinen Änderung der Benutzeroberfläche erneut getestet werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="77cfb-118">Sowohl die Ansicht als auch der Controller sind abhängig vom Modell.</span><span class="sxs-lookup"><span data-stu-id="77cfb-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="77cfb-119">Das Modell ist jedoch weder von der Ansicht noch vom Controller abhängig.</span><span class="sxs-lookup"><span data-stu-id="77cfb-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="77cfb-120">Hierin besteht einer der Hauptvorteile der Trennung.</span><span class="sxs-lookup"><span data-stu-id="77cfb-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="77cfb-121">Aufgrund dieser Trennung kann das Modell unabhängig von der visuellen Darstellung erstellt und getestet werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="77cfb-122">Aufgaben des Modells</span><span class="sxs-lookup"><span data-stu-id="77cfb-122">Model Responsibilities</span></span>

<span data-ttu-id="77cfb-123">Das Modell stellt in einer MVC-Anwendung den Status der Anwendung und sämtlicher Vorgänge oder Geschäftslogik dar, die von ihm ausgeführt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="77cfb-124">Im Modell sollte Geschäftslogik zusammen mit der gesamten Implementierungslogik gekapselt werden, um den Status der Anwendung beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="77cfb-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="77cfb-125">Stark typisierte Ansichten verwenden in der Regel ViewModel-Typen, die die Daten für diese Ansicht enthalten.</span><span class="sxs-lookup"><span data-stu-id="77cfb-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="77cfb-126">Der Controller erstellt und füllt diese ViewModel-Instanzen aus dem Modell.</span><span class="sxs-lookup"><span data-stu-id="77cfb-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="77cfb-127">Aufgaben der Ansicht</span><span class="sxs-lookup"><span data-stu-id="77cfb-127">View Responsibilities</span></span>

<span data-ttu-id="77cfb-128">Ansichten dienen der Darstellung von Inhalt über die Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="77cfb-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="77cfb-129">Sie verwenden die [ Razor Ansichts-Engine](#razor-view-engine) zum Einbetten von .NET-Code in HTML-Markup.</span><span class="sxs-lookup"><span data-stu-id="77cfb-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="77cfb-130">Ansichten sollten so wenig Logik wie möglich enthalten. Die enthaltene Logik sollte sich zudem auf die Darstellung von Inhalt beziehen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="77cfb-131">Sollten Sie dennoch viel Logik in Ansichtsdateien ausführen müssen, um Daten aus einem komplexen Modell anzuzeigen, ist es sinnvoll, eine [Ansichtskomponente](views/view-components.md), ViewModel oder eine Ansichtsvorlage zu verwenden, um die Ansicht zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="77cfb-132">Aufgaben des Controllers</span><span class="sxs-lookup"><span data-stu-id="77cfb-132">Controller Responsibilities</span></span>

<span data-ttu-id="77cfb-133">Controller sind Komponenten, die Benutzerinteraktionen verarbeiten, mit dem Modell arbeiten und letztlich eine Ansicht auswählen, die gerendert werden soll.</span><span class="sxs-lookup"><span data-stu-id="77cfb-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="77cfb-134">In einer MVC-Anwendung zeigt die Ansicht nur Informationen an. Benutzereingaben und -interaktionen werden vom Controller verarbeitet und beantwortet.</span><span class="sxs-lookup"><span data-stu-id="77cfb-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="77cfb-135">Im MVC-Muster stellt der Controller den Einstiegspunkt dar. Er ist verantwortlich für die Auswahl des Modells, mit dem gearbeitet wird, sowie der Ansicht, die gerendert wird (daher kommt auch sein Name: Der Controller kontrolliert, wie die App auf eine bestimmte Anforderung reagiert).</span><span class="sxs-lookup"><span data-stu-id="77cfb-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="77cfb-136">Controller sollten nicht durch zu viele Aufgaben übermäßig kompliziert gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="77cfb-137">Um zu verhindern, dass die Controllerlogik zu komplex wird, verlagern Sie die Geschäftslogik aus dem Controller in das Domänenmodell.</span><span class="sxs-lookup"><span data-stu-id="77cfb-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="77cfb-138">Wenn Sie feststellen, dass Ihre Controlleraktionen häufig die gleiche Art von Aktionen ausführen, verschieben diese häufig ausgeführten Aktionen in [Filter](#filters).</span><span class="sxs-lookup"><span data-stu-id="77cfb-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="77cfb-139">Was ist ASP.NET Core MVC?</span><span class="sxs-lookup"><span data-stu-id="77cfb-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="77cfb-140">Das ASP.NET Core MVC-Framework ist ein einfaches, äußerst testfähiges Open-Source-Präsentationsframework, das für die Verwendung mit ASP.NET Core optimiert wurde.</span><span class="sxs-lookup"><span data-stu-id="77cfb-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="77cfb-141">ASP.NET Core MVC stellt eine auf Mustern basierte Methode zum Entwickeln dynamischer Websites dar, die eine saubere Trennung von Belangen ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="77cfb-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="77cfb-142">Es ermöglicht Ihnen die vollständige Kontrolle über das Markup, unterstützt eine TDD-freundliche Entwicklung und verwendet die aktuellsten Webstandards.</span><span class="sxs-lookup"><span data-stu-id="77cfb-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="77cfb-143">Features</span><span class="sxs-lookup"><span data-stu-id="77cfb-143">Features</span></span>

<span data-ttu-id="77cfb-144">Zu ASP.NET Core MVC gehören folgende Elemente:</span><span class="sxs-lookup"><span data-stu-id="77cfb-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="77cfb-145">Routing</span><span class="sxs-lookup"><span data-stu-id="77cfb-145">Routing</span></span>](#routing)
* [<span data-ttu-id="77cfb-146">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="77cfb-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="77cfb-147">Modellvalidierung</span><span class="sxs-lookup"><span data-stu-id="77cfb-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="77cfb-148">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="77cfb-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="77cfb-149">Filter</span><span class="sxs-lookup"><span data-stu-id="77cfb-149">Filters</span></span>](#filters)
* [<span data-ttu-id="77cfb-150">Bereiche</span><span class="sxs-lookup"><span data-stu-id="77cfb-150">Areas</span></span>](#areas)
* [<span data-ttu-id="77cfb-151">Web-APIs</span><span class="sxs-lookup"><span data-stu-id="77cfb-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="77cfb-152">Prüfbarkeit</span><span class="sxs-lookup"><span data-stu-id="77cfb-152">Testability</span></span>](#testability)
* [<span data-ttu-id="77cfb-153">Razor Anzeige-Engine</span><span class="sxs-lookup"><span data-stu-id="77cfb-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="77cfb-154">Stark typisierte Ansichten</span><span class="sxs-lookup"><span data-stu-id="77cfb-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="77cfb-155">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="77cfb-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="77cfb-156">Komponenten anzeigen</span><span class="sxs-lookup"><span data-stu-id="77cfb-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="77cfb-157">Routing</span><span class="sxs-lookup"><span data-stu-id="77cfb-157">Routing</span></span>

<span data-ttu-id="77cfb-158">ASP.NET Core MVC baut auf dem [Routing von ASP.NET Core](../fundamentals/routing.md) auf, einer leistungsstarken URL-Zuordnungskomponente, mit der Sie Anwendungen mit verständlichen und suchbaren URLs erstellen können.</span><span class="sxs-lookup"><span data-stu-id="77cfb-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="77cfb-159">Damit können Sie die URL-Benennungsmuster für Ihre Anwendung definieren, die sich für die Suchmaschinenoptimierung (SEO) und die Linkgenerierung eignen, ohne dass die Organisation der Dateien auf Ihrem Webserver berücksichtigt werden muss.</span><span class="sxs-lookup"><span data-stu-id="77cfb-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="77cfb-160">Sie können die Routen mithilfe einer geeigneten Syntax für Routenvorlagen definieren, die Routenwerteinschränkungen, Standardwerte sowie optionale Werte unterstützt.</span><span class="sxs-lookup"><span data-stu-id="77cfb-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="77cfb-161">Das *auf der Konvention basierende Routing* ermöglicht es Ihnen, die URL-Formate global zu definieren, die von der Anwendung akzeptiert werden, und zu erfahren, wie die einzelnen Formate einer bestimmten Aktionsmethode auf einem bestimmten Controller zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on a given controller.</span></span> <span data-ttu-id="77cfb-162">Wird eine eingehende Anforderung empfangen, analysiert die Routingengine die URL und vergleicht sie mit einer der definierten URL-Formate. Anschließend wird die zugeordnete Aktionsmethode des Controllers aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="77cfb-163">*Attributrouting* ermöglicht Ihnen die Angabe von Routinginformationen, indem die Controller und Aktionen mit Attributen versehen werden, die die Routen für Ihre Anwendung definieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="77cfb-164">Das bedeutet, dass Ihre Routendefinitionen neben den Controller und die Aktion platziert werden, denen sie zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="77cfb-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

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

### <a name="model-binding"></a><span data-ttu-id="77cfb-165">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="77cfb-165">Model binding</span></span>

<span data-ttu-id="77cfb-166">Die [Modellbindung](models/model-binding.md) von ASP.NET Core MVC konvertiert Clientanforderungsdaten (Formularwerte, Routendaten, Abfragezeichenfolgen-Parameter, HTTP-Header) in Objekte, die der Controller verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="77cfb-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="77cfb-167">Dadurch muss Ihre Controllerlogik nicht mehr die eingehenden Anforderungsdaten ermitteln. Die Daten sind bereits als Parameter für die Aktionsmethoden vorhanden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
```

### <a name="model-validation"></a><span data-ttu-id="77cfb-168">Modellvalidierung</span><span class="sxs-lookup"><span data-stu-id="77cfb-168">Model validation</span></span>

<span data-ttu-id="77cfb-169">ASP.NET Core MVC unterstützt die [Validierung](models/validation.md), indem das Modellobjekt mit Validierungsattributen für Datenanmerkungen versehen wird.</span><span class="sxs-lookup"><span data-stu-id="77cfb-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="77cfb-170">Die Validierungsattribute werden auf der Clientseite überprüft, bevor Werte auf dem Server bereitgestellt werden. Auf der Serverseite werden sie überprüft, bevor die Controlleraktion aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="77cfb-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

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

<span data-ttu-id="77cfb-171">Eine Controlleraktion:</span><span class="sxs-lookup"><span data-stu-id="77cfb-171">A controller action:</span></span>

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

<span data-ttu-id="77cfb-172">Das Framework verarbeitet Validierungsanforderungsdaten sowohl auf dem Client als auch auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="77cfb-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="77cfb-173">Für Modelltypen angegebene Validierungslogik wird den gerenderten Ansichten als unaufdringliche Anmerkungen hinzugefügt und im Browser mit [jQuery Validation](https://jqueryvalidation.org/) erzwungen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="77cfb-174">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="77cfb-174">Dependency injection</span></span>

<span data-ttu-id="77cfb-175">ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="77cfb-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="77cfb-176">In ASP.NET Core MVC können [Controller](controllers/dependency-injection.md) benötigte Dienste über ihre Konstruktoren anfordern. So wird das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgt.</span><span class="sxs-lookup"><span data-stu-id="77cfb-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="77cfb-177">Mit der `@inject`-Anweisung kann Ihre App [Dependency Injection auch in Ansichtsdateien](views/dependency-injection.md) verwenden:</span><span class="sxs-lookup"><span data-stu-id="77cfb-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

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

### <a name="filters"></a><span data-ttu-id="77cfb-178">Filter</span><span class="sxs-lookup"><span data-stu-id="77cfb-178">Filters</span></span>

<span data-ttu-id="77cfb-179">Mit [Filtern](controllers/filters.md) können Entwickler übergreifende Belange kapseln, wie etwa die Ausnahmebehandlung oder die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="77cfb-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="77cfb-180">Filter ermöglichen das Ausführen benutzerdefinierter Vor- und Nachverarbeitungslogik für Aktionsmethoden. Sie können so konfiguriert werden, dass sie für eine angegebene Anforderung an bestimmten Stellen in der Ausführungspipeline ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="77cfb-181">Filter können als Attribute auf Controller oder Aktionen angewendet werden (oder sie werden global ausgeführt).</span><span class="sxs-lookup"><span data-stu-id="77cfb-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="77cfb-182">Das Framework enthält mehrere Filter (wie z.B. `Authorize`).</span><span class="sxs-lookup"><span data-stu-id="77cfb-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="77cfb-183">`[Authorize]` ist das Attribut, das zum Erstellen von MVC-Autorisierungsfiltern verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="77cfb-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="77cfb-184">Bereiche</span><span class="sxs-lookup"><span data-stu-id="77cfb-184">Areas</span></span>

<span data-ttu-id="77cfb-185">[Bereiche](controllers/areas.md) bieten eine Möglichkeit, eine große ASP.net Core MVC-Web-App in kleinere funktionale Gruppierungen zu partitionieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="77cfb-186">Ein Bereich ist eine MVC-Struktur innerhalb einer Anwendung.</span><span class="sxs-lookup"><span data-stu-id="77cfb-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="77cfb-187">In einem MVC-Projekt sind logische Komponenten wie Modell, Controller und Ansicht in verschiedenen Ordnern gespeichert. MVC nutzt Namenskonventionen zum Erstellen einer Beziehung zwischen diesen Komponenten.</span><span class="sxs-lookup"><span data-stu-id="77cfb-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="77cfb-188">Bei einer großen App kann es von Vorteil sein, die App in mehrere Bereiche mit hoher Funktionalität aufzuteilen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="77cfb-189">Beispielsweise eine e-Commerce-App mit mehreren Geschäftseinheiten, z. b. Checkout, Abrechnung und Suche usw. Jede dieser Einheiten verfügt über eigene logische Komponenten Sichten, Controller und Modelle.</span><span class="sxs-lookup"><span data-stu-id="77cfb-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="77cfb-190">Web-APIs</span><span class="sxs-lookup"><span data-stu-id="77cfb-190">Web APIs</span></span>

<span data-ttu-id="77cfb-191">Als umfangreiche Plattform zum Erstellen von Websites verfügt ASP.NET Core MVC außerdem über umfassende Unterstützung für das Erstellen von Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="77cfb-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="77cfb-192">Sie können Dienste für eine breit gefächerte Palette von Clients erstellen, darunter auch Browser und mobile Geräte.</span><span class="sxs-lookup"><span data-stu-id="77cfb-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="77cfb-193">Das Framework beinhaltet Unterstützung für die Aushandlung von HTTP-Inhalt mit integrierter Unterstützung zum [Formatieren von Daten](xref:web-api/advanced/formatting) als JSON oder XML.</span><span class="sxs-lookup"><span data-stu-id="77cfb-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="77cfb-194">Sie können [benutzerdefinierte Formatierungsprogramme](xref:web-api/advanced/custom-formatters) schreiben, um Unterstützung für Ihre eigenen Formate hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="77cfb-195">Außerdem können Sie mit der Linkgenerierung Unterstützung für Hypermedia aktivieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="77cfb-196">Aktivieren Sie auf einfache Weise Unterstützung für die [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://www.w3.org/TR/cors/), damit Ihre Web-APIs für mehrere Webanwendungen freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-196">Easily enable support for [cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="77cfb-197">Prüfbarkeit</span><span class="sxs-lookup"><span data-stu-id="77cfb-197">Testability</span></span>

<span data-ttu-id="77cfb-198">Durch die Verwendung von Schnittstellen und der Abhängigkeitsinjektion eignet sich das Framework besonders gut für Komponententests. Es enthält Features (wie etwa einen TestHost- und InMemory-Anbieter für Entity Framework), mit denen auch [Integrationstests](xref:test/integration-tests) schnell und einfach durchgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="77cfb-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="77cfb-199">Weitere Informationen hierzu finden Sie unter [Testen der Controllerlogik](controllers/testing.md).</span><span class="sxs-lookup"><span data-stu-id="77cfb-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="no-locrazor-view-engine"></a><span data-ttu-id="77cfb-200">Razor Anzeige-Engine</span><span class="sxs-lookup"><span data-stu-id="77cfb-200">Razor view engine</span></span>

<span data-ttu-id="77cfb-201">[ASP.net Core MVC-Ansichten](views/overview.md) verwenden Sie die [ Razor Ansichts-Engine](views/razor.md) zum renderingansichten.</span><span class="sxs-lookup"><span data-stu-id="77cfb-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="77cfb-202">Razor ist eine kompakte, ausdrucksstarke und fließende Vorlagen Markup Sprache zum Definieren von Sichten mithilfe von eingebettetem c#-Code.</span><span class="sxs-lookup"><span data-stu-id="77cfb-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="77cfb-203">Razor wird verwendet, um Webinhalte auf dem Server dynamisch zu generieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="77cfb-204">Sie können Servercode sauber mit Inhalt und Code der Clientseite kombinieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-204">You can cleanly mix server code with client side content and code.</span></span>

```cshtml
<ul>
    @for (int i = 0; i < 5; i++) {
        <li>List item @i</li>
    }
</ul>
```

<span data-ttu-id="77cfb-205">Mithilfe der Razor Ansichts-Engine können Sie [Layouts](views/layout.md), [Teilansichten](views/partial.md) und austauschbare Abschnitte definieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="77cfb-206">Stark typisierte Ansichten</span><span class="sxs-lookup"><span data-stu-id="77cfb-206">Strongly typed views</span></span>

<span data-ttu-id="77cfb-207">Razor Sichten in MVC können basierend auf dem Modell stark typisiert werden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="77cfb-208">Controller können ein stark typisiertes Modell an die Ansichten übergeben, um Typüberprüfung und IntelliSense-Unterstützung für Ihre Ansichten zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="77cfb-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="77cfb-209">Die folgende Ansicht rendert beispielweise ein Modell vom Typ `IEnumerable<Product>`:</span><span class="sxs-lookup"><span data-stu-id="77cfb-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="77cfb-210">Taghilfsprogramme</span><span class="sxs-lookup"><span data-stu-id="77cfb-210">Tag Helpers</span></span>

<span data-ttu-id="77cfb-211">[Taghilfsprogramme](views/tag-helpers/intro.md) ermöglichen es dem serverseitigen Code, an der Erstellung und dem Rendern von HTML-Elementen in Razor Dateien teilzunehmen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="77cfb-212">Mit Taghilfsprogrammen können Sie benutzerdefinierte Tags anpassen (z.B. `<environment>`) oder das Verhalten von vorhandenen Tags ändern (z.B. `<label>`).</span><span class="sxs-lookup"><span data-stu-id="77cfb-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="77cfb-213">Taghilfsprogramme sind an bestimmte Elemente basierend auf dem Elementnamen und dessen Attributen gebunden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="77cfb-214">Sie bieten gleichzeitig die Vorteile des serverseitigen Renderns sowie der HTML-Bearbeitungsmöglichkeiten.</span><span class="sxs-lookup"><span data-stu-id="77cfb-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="77cfb-215">Für häufige Aufgaben wie das Erstellen von Formularen und Links sowie das Laden von Objekten gibt es zahlreiche integrierte Taghilfsprogramme. Weitere Taghilfsprogramme sind in öffentlichen GitHub-Repositorys und als NuGet-Pakete verfügbar.</span><span class="sxs-lookup"><span data-stu-id="77cfb-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="77cfb-216">Taghilfsprogramme werden in C# erstellt und sind für HTML-Elemente basierend auf dem Elementnamen, dem Attributnamen oder dem übergeordneten Tag konzipiert.</span><span class="sxs-lookup"><span data-stu-id="77cfb-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="77cfb-217">Der integrierte LinkTagHelper kann z.B. verwendet werden, um einen Link zur `Login`-Aktion des `AccountsController` zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="77cfb-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="77cfb-218">Mit `EnvironmentTagHelper` können Sie Ihren Ansichten verschiedene Skripts hinzufügen (z.B. minimiert oder RAW), basierend auf der Laufzeitumgebung, wie Entwicklung, Staging oder Produktion:</span><span class="sxs-lookup"><span data-stu-id="77cfb-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

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

<span data-ttu-id="77cfb-219">Taghilfsprogramme bieten eine HTML-freundliche Entwicklungsumgebung und eine umfangreiche IntelliSense-Umgebung zum Erstellen von HTML und Razor Markup.</span><span class="sxs-lookup"><span data-stu-id="77cfb-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="77cfb-220">Die meisten integrierten Taghilfsprogramme sind für vorhandene HTML-Elemente konzipiert und stellen serverseitige Attribute für die jeweiligen Elemente bereit.</span><span class="sxs-lookup"><span data-stu-id="77cfb-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="77cfb-221">Ansichtskomponenten</span><span class="sxs-lookup"><span data-stu-id="77cfb-221">View Components</span></span>

<span data-ttu-id="77cfb-222">Mit [Ansichtskomponenten](views/view-components.md) können Sie Renderinglogik packen und in der gesamten Anwendung wiederverwenden.</span><span class="sxs-lookup"><span data-stu-id="77cfb-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="77cfb-223">Ansichtskomponenten ähneln [Teilansichten](views/partial.md), enthalten jedoch zugeordnete Logik.</span><span class="sxs-lookup"><span data-stu-id="77cfb-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="77cfb-224">Kompatibilitätsversion</span><span class="sxs-lookup"><span data-stu-id="77cfb-224">Compatibility version</span></span>

<span data-ttu-id="77cfb-225">Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen.</span><span class="sxs-lookup"><span data-stu-id="77cfb-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="77cfb-226">Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="77cfb-226">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77cfb-227">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="77cfb-227">Additional resources</span></span>

* <span data-ttu-id="77cfb-228">[Mytesting. aspnetcore. MVC-fließende Test Bibliothek für ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): stark typisierte unittestbibliothek, die eine fließende Oberfläche zum Testen von MVC-und Web-API-apps bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="77cfb-228">[MyTested.AspNetCore.Mvc - Fluent Testing Library for ASP.NET Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): Strongly-typed unit testing library, providing a fluent interface for testing MVC and web API apps.</span></span> <span data-ttu-id="77cfb-229">(*Wird von Microsoft nicht verwaltet oder unterstützt.* )</span><span class="sxs-lookup"><span data-stu-id="77cfb-229">(*Not maintained or supported by Microsoft.*)</span></span>
* <xref:blazor/components/prerendering-and-integration>
