---
title: Dependency Injection in Ansichten in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr über die Unterstützung von Dependency Injection in Ansichten in ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
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
uid: mvc/views/dependency-injection
ms.openlocfilehash: a596d05e3d1d73d4faa2f84d97a40e4a2c1234e4
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059285"
---
# <a name="dependency-injection-into-views-in-aspnet-core"></a><span data-ttu-id="175b9-103">Dependency Injection in Ansichten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="175b9-103">Dependency injection into views in ASP.NET Core</span></span>

<span data-ttu-id="175b9-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="175b9-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="175b9-105">ASP.NET Core unterstützt [Dependency Injection](xref:fundamentals/dependency-injection) in Ansichten.</span><span class="sxs-lookup"><span data-stu-id="175b9-105">ASP.NET Core supports [dependency injection](xref:fundamentals/dependency-injection) into views.</span></span> <span data-ttu-id="175b9-106">Dies kann besonders für ansichtsspezifische Dienste nützlich sein – z.B. für die Lokalisierung oder für Daten, die nur zum Auffüllen von Ansichtselementen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="175b9-106">This can be useful for view-specific services, such as localization or data required only for populating view elements.</span></span> <span data-ttu-id="175b9-107">Sie sollten versuchen, das Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) zwischen Controllern und Ansichten beizubehalten.</span><span class="sxs-lookup"><span data-stu-id="175b9-107">You should try to maintain [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) between your controllers and views.</span></span> <span data-ttu-id="175b9-108">Die meisten Daten, die in Ihren Ansichten angezeigt werden, sollten vom Controller übergeben worden sein.</span><span class="sxs-lookup"><span data-stu-id="175b9-108">Most of the data your views display should be passed in from the controller.</span></span>

<span data-ttu-id="175b9-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="175b9-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="configuration-injection"></a><span data-ttu-id="175b9-110">Konfigurationsinjektion</span><span class="sxs-lookup"><span data-stu-id="175b9-110">Configuration injection</span></span>

<span data-ttu-id="175b9-111">*:::no-loc(appsettings.json):::* Werte können direkt in eine Ansicht eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="175b9-111">*:::no-loc(appsettings.json):::* values can be injected directly into a view.</span></span>

<span data-ttu-id="175b9-112">Beispiel für eine- *:::no-loc(appsettings.json):::* Datei:</span><span class="sxs-lookup"><span data-stu-id="175b9-112">Example of an *:::no-loc(appsettings.json):::* file:</span></span>

```json
{
   "root": {
      "parent": {
         "child": "myvalue"
      }
   }
}
```

<span data-ttu-id="175b9-113">Die Syntax für `@inject`: `@inject <type> <name>`</span><span class="sxs-lookup"><span data-stu-id="175b9-113">The syntax for `@inject`: `@inject <type> <name>`</span></span>

<span data-ttu-id="175b9-114">Ein Beispiel, das `@inject` verwendet:</span><span class="sxs-lookup"><span data-stu-id="175b9-114">An example using `@inject`:</span></span>

```csharp
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
@{
   string myValue = Configuration["root:parent:child"];
   ...
}
```

## <a name="service-injection"></a><span data-ttu-id="175b9-115">Dienstinjektion</span><span class="sxs-lookup"><span data-stu-id="175b9-115">Service injection</span></span>

<span data-ttu-id="175b9-116">Ein Dienst kann mithilfe der `@inject`-Anweisung in eine Ansicht eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="175b9-116">A service can be injected into a view using the `@inject` directive.</span></span> <span data-ttu-id="175b9-117">`@inject` fügt der Ansicht eine Eigenschaft hinzu und füllt diese Eigenschaft mittels Dependency Injection auf.</span><span class="sxs-lookup"><span data-stu-id="175b9-117">You can think of `@inject` as adding a property to the view, and populating the property using DI.</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/ToDo/Index.cshtml?highlight=4,5,15,16,17)]

<span data-ttu-id="175b9-118">In dieser Ansicht wird neben einer Zusammenfassung von allgemeinen Statistiken eine Liste von `ToDoItem`-Instanzen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="175b9-118">This view displays a list of `ToDoItem` instances, along with a summary showing overall statistics.</span></span> <span data-ttu-id="175b9-119">Die Zusammenfassung wird über den eingefügten `StatisticsService`-Dienst aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="175b9-119">The summary is populated from the injected `StatisticsService`.</span></span> <span data-ttu-id="175b9-120">Dieser Dienst ist für Dependency Injection in `ConfigureServices` in *Startup.cs* registriert:</span><span class="sxs-lookup"><span data-stu-id="175b9-120">This service is registered for dependency injection in `ConfigureServices` in *Startup.cs* :</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Startup.cs?highlight=6,7&range=15-22)]

<span data-ttu-id="175b9-121">Die `StatisticsService` führt Berechnungen für die `ToDoItem`-Instanzen durch, auf die er über ein Repository zugreift:</span><span class="sxs-lookup"><span data-stu-id="175b9-121">The `StatisticsService` performs some calculations on the set of `ToDoItem` instances, which it accesses via a repository:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/StatisticsService.cs?highlight=15,20,25)]

<span data-ttu-id="175b9-122">Das Beispielrepository verwendet eine speicherinterne Auflistung.</span><span class="sxs-lookup"><span data-stu-id="175b9-122">The sample repository uses an in-memory collection.</span></span> <span data-ttu-id="175b9-123">Die obenstehend dargestellte Implementierung (die auf allen Daten im Arbeitsspeicher ausgeführt wird) wird nicht für große Datasets empfohlen, auf die über eine Remoteverbindung zugegriffen wird.</span><span class="sxs-lookup"><span data-stu-id="175b9-123">The implementation shown above (which operates on all of the data in memory) isn't recommended for large, remotely accessed data sets.</span></span>

<span data-ttu-id="175b9-124">Das Beispiel stellt Daten aus dem Modell, die an die Ansicht gebunden sind, und den in die Ansicht eingefügten Dienst dar:</span><span class="sxs-lookup"><span data-stu-id="175b9-124">The sample displays data from the model bound to the view and the service injected into the view:</span></span>

![Die „Zu erledigen“-Ansicht, die die Gesamtelemente, fertiggestellte Elemente, durchschnittliche Priorität und eine Liste mit Aufgaben enthält, deren Prioritätsstufen und boolesche Werte auf die Fertigstellung hindeuten.](dependency-injection/_static/screenshot.png)

## <a name="populating-lookup-data"></a><span data-ttu-id="175b9-126">Auffüllen von Nachschlagedaten</span><span class="sxs-lookup"><span data-stu-id="175b9-126">Populating Lookup Data</span></span>

<span data-ttu-id="175b9-127">Die Ansichtsinjektion kann nützlich sein, wenn Sie Optionen in Benutzeroberflächenelemente wie Dropdownlisten einfügen möchten.</span><span class="sxs-lookup"><span data-stu-id="175b9-127">View injection can be useful to populate options in UI elements, such as dropdown lists.</span></span> <span data-ttu-id="175b9-128">Verwenden Sie ggf. ein Benutzerprofilformular, das Optionen zum Festlegen des Geschlechts, des Staats und anderer Vorlieben beinhaltet.</span><span class="sxs-lookup"><span data-stu-id="175b9-128">Consider a user profile form that includes options for specifying gender, state, and other preferences.</span></span> <span data-ttu-id="175b9-129">Wenn Sie ein solches Formular über einen Standard-MVC-Ansatz rendern, muss der Controller Datenzugriffsdienste für all diese Optionen anfordern, und dann ein Modell oder `ViewBag` mit den Optionen auffüllen, die gebunden werden sollen.</span><span class="sxs-lookup"><span data-stu-id="175b9-129">Rendering such a form using a standard MVC approach would require the controller to request data access services for each of these sets of options, and then populate a model or `ViewBag` with each set of options to be bound.</span></span>

<span data-ttu-id="175b9-130">Über einen alternativen Ansatz werden Dienste direkt in die Ansicht eingefügt, um die Optionen abzurufen.</span><span class="sxs-lookup"><span data-stu-id="175b9-130">An alternative approach injects services directly into the view to obtain the options.</span></span> <span data-ttu-id="175b9-131">Dadurch ist weniger Code für den Controller erforderlich, denn die Konstruktionsebene dieses Ansichtselements wird in die Ansicht verschoben.</span><span class="sxs-lookup"><span data-stu-id="175b9-131">This minimizes the amount of code required by the controller, moving this view element construction logic into the view itself.</span></span> <span data-ttu-id="175b9-132">Die Controlleraktion, die das Formular zur Profilbearbeitung anzeigen soll, muss das Formular nur an die Profilinstanz übergeben:</span><span class="sxs-lookup"><span data-stu-id="175b9-132">The controller action to display a profile editing form only needs to pass the form the profile instance:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Controllers/ProfileController.cs?highlight=9,19)]

<span data-ttu-id="175b9-133">Das HTML-Formular, das verwendet wird, um diese Vorlieben zu aktualisieren, umfasst Dropdownlisten für drei Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="175b9-133">The HTML form used to update these preferences includes dropdown lists for three of the properties:</span></span>

![Die „Profil aktualisieren“-Ansicht mit einem Formular, in das der Name, das Geschlecht, der Staat und die Lieblingsfarbe eingetragen werden kann.](dependency-injection/_static/updateprofile.png)

<span data-ttu-id="175b9-135">Die Listen werden über einen Dienst aufgefüllt, der in die Ansicht eingefügt wurde:</span><span class="sxs-lookup"><span data-stu-id="175b9-135">These lists are populated by a service that has been injected into the view:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Profile/Index.cshtml?highlight=4,16,17,21,22,26,27)]

<span data-ttu-id="175b9-136">`ProfileOptionsService` ist ein Dienst auf Benutzeroberflächenebene, der nur die für das Formular benötigten Daten einfügt:</span><span class="sxs-lookup"><span data-stu-id="175b9-136">The `ProfileOptionsService` is a UI-level service designed to provide just the data needed for this form:</span></span>

[!code-csharp[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Model/Services/ProfileOptionsService.cs?highlight=7,13,24)]

> [!IMPORTANT]
> <span data-ttu-id="175b9-137">Denken Sie daran, Typen zu registrieren, die Sie über Dependency Injection in `Startup.ConfigureServices` anfordern.</span><span class="sxs-lookup"><span data-stu-id="175b9-137">Don't forget to register types you request through dependency injection in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="175b9-138">Ein nicht registrierter Typ löst eine Ausnahme zur Laufzeit aus, weil der Dienstanbieter intern über [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice) abgefragt wird.</span><span class="sxs-lookup"><span data-stu-id="175b9-138">An unregistered type throws an exception at runtime because the service provider is internally queried via [GetRequiredService](/dotnet/api/microsoft.extensions.dependencyinjection.serviceproviderserviceextensions.getrequiredservice).</span></span>

## <a name="overriding-services"></a><span data-ttu-id="175b9-139">Überschreiben von Diensten</span><span class="sxs-lookup"><span data-stu-id="175b9-139">Overriding Services</span></span>

<span data-ttu-id="175b9-140">Sie können über diese Technik nicht nur neue Dienste einfügen, sondern auch zuvor auf einer Seite eingefügte Dienste überschreiben.</span><span class="sxs-lookup"><span data-stu-id="175b9-140">In addition to injecting new services, this technique can also be used to override previously injected services on a page.</span></span> <span data-ttu-id="175b9-141">In der nachfolgenden Abbildung werden alle Felder angezeigt, die auf der Seite verfügbar sind, die im ersten Beispiel verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="175b9-141">The figure below shows all of the fields available on the page used in the first example:</span></span>

![Kontextmenü von IntelliSense, in dem das @-Symbol eingegeben wurde und „HTML“, „Komponente“, „StatsService“ und „URL-Felder“ angezeigt werden](dependency-injection/_static/razor-fields.png)

<span data-ttu-id="175b9-143">Die Standardfelder umfassen also `Html`, `Component` und `Url` sowie den `StatsService`-Dienst, den Sie eingefügt haben.</span><span class="sxs-lookup"><span data-stu-id="175b9-143">As you can see, the default fields include `Html`, `Component`, and `Url` (as well as the `StatsService` that we injected).</span></span> <span data-ttu-id="175b9-144">Wenn Sie beispielsweise die Standard-HTML-Hilfsprogramme durch ihre eigenen ersetzen möchten, können Sie dafür ganz einfach `@inject` verwenden:</span><span class="sxs-lookup"><span data-stu-id="175b9-144">If for instance you wanted to replace the default HTML Helpers with your own, you could easily do so using `@inject`:</span></span>

[!code-cshtml[](../../mvc/views/dependency-injection/sample/src/ViewInjectSample/Views/Helper/Index.cshtml?highlight=3,11)]

<span data-ttu-id="175b9-145">Wenn Sie vorhandene Dienste erweitern möchten, können Sie diese Technik verwenden und aus der vorhanden Implementierung erben bzw. diese mit Ihrer eigenen umschließen.</span><span class="sxs-lookup"><span data-stu-id="175b9-145">If you want to extend existing services, you can simply use this technique while inheriting from or wrapping the existing implementation with your own.</span></span>

## <a name="see-also"></a><span data-ttu-id="175b9-146">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="175b9-146">See Also</span></span>

* <span data-ttu-id="175b9-147">Blog von Simon Timms: [Getting Lookup Data Into Your View (Übertragen von Nachschlagedaten in Ihre Ansicht)](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span><span class="sxs-lookup"><span data-stu-id="175b9-147">Simon Timms Blog: [Getting Lookup Data Into Your View](https://blog.simontimms.com/2015/06/09/getting-lookup-data-into-you-view/)</span></span>
