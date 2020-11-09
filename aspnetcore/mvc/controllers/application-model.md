---
title: Arbeiten mit dem Anwendungsmodell in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie Sie das Anwendungsmodell lesen und bearbeiten, um das Verhalten von MVC-Elementen in ASP.NET Core zu ändern.
ms.author: riande
ms.date: 12/05/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/controllers/application-model
ms.openlocfilehash: a7a654eb43c0dbf375af911d8d5353ac4c04a825
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060936"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="49705-103">Arbeiten mit dem Anwendungsmodell in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="49705-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="49705-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="49705-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="49705-105">ASP.NET Core MVC definiert ein *Anwendungsmodell* , in dem die Komponenten einer MVC-App dargestellt werden.</span><span class="sxs-lookup"><span data-stu-id="49705-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="49705-106">Sie können dieses Modell lesen und bearbeiten, um die Verhaltensweise von MVC-Elementen zu ändern.</span><span class="sxs-lookup"><span data-stu-id="49705-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="49705-107">MVC folgt standardmäßig bestimmten Konventionen, um zu ermitteln, welche Klassen als Controller betrachtet werden, welche Methoden in diesen Klassen Aktionen darstellen und wie sich Parameter und das Routing verhalten.</span><span class="sxs-lookup"><span data-stu-id="49705-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="49705-108">Sie können dieses Verhalten an die Anforderungen Ihrer App anpassen, indem Sie Ihre eigenen Konventionen schaffen und diese global oder in Form von Attributen anwenden.</span><span class="sxs-lookup"><span data-stu-id="49705-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="49705-109">Modelle und Anbieter</span><span class="sxs-lookup"><span data-stu-id="49705-109">Models and Providers</span></span>

<span data-ttu-id="49705-110">Das ASP.NET Core-MVC-Anwendungsmodell enthält abstrakte Schnittstellen und konkrete Implementierungsklassen, die eine MVC-Anwendung beschreiben.</span><span class="sxs-lookup"><span data-stu-id="49705-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="49705-111">Dieses Modell resultiert aus der MVC-Ermittlung von Controllern, Aktionen, Aktionsparametern, Routen und Filtern der App gemäß den Standardkonventionen.</span><span class="sxs-lookup"><span data-stu-id="49705-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="49705-112">Wenn Sie mit dem Anwendungsmodell arbeiten, können Sie Ihre App so ändern, dass sie verschiedenen Konventionen des MVC-Standardverhaltens folgt.</span><span class="sxs-lookup"><span data-stu-id="49705-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="49705-113">Die Parameter, Namen, Routen und Filter werden alle als Konfigurationsdaten für Aktionen und Controller verwendet.</span><span class="sxs-lookup"><span data-stu-id="49705-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="49705-114">Das ASP.NET Core-MVC-Anwendungsmodell weist folgende Struktur auf:</span><span class="sxs-lookup"><span data-stu-id="49705-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="49705-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="49705-115">ApplicationModel</span></span>
  * <span data-ttu-id="49705-116">Controller (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="49705-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="49705-117">Aktionen (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="49705-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="49705-118">Parameter (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="49705-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="49705-119">Jede Modellebenen kann auf eine allgemeine `Properties`-Sammlung zugreifen. Zudem ist auf niedrigeren Ebenen der Zugriff auf Eigenschaftswerte sowie die Überschreibung von Eigenschaftswerten möglich, die auf höheren Ebenen in der Hierarchie festgelegt wurden.</span><span class="sxs-lookup"><span data-stu-id="49705-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="49705-120">Die Eigenschaften werden bei der Erstellung der Aktionen in der Datei `ActionDescriptor.Properties` permanent gespeichert.</span><span class="sxs-lookup"><span data-stu-id="49705-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="49705-121">Wenn eine Anforderung dann verarbeitet wird, kann über `ActionContext.ActionDescriptor.Properties` auf sämtliche Eigenschaften zugegriffen werden, die über eine Konvention hinzugefügt oder geändert wurden.</span><span class="sxs-lookup"><span data-stu-id="49705-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="49705-122">Die Verwendung von Eigenschaften stellt eine gute Möglichkeit zur Konfiguration Ihrer Filter, Modellbindungen usw. auf Grundlage einer Aktion dar.</span><span class="sxs-lookup"><span data-stu-id="49705-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="49705-123">Nachdem die App gestartet wurde, ist die `ActionDescriptor.Properties`-Sammlung nicht threadsicher (bei Schreibvorgängen).</span><span class="sxs-lookup"><span data-stu-id="49705-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="49705-124">Konventionen stellen die beste Möglichkeit zum sicheren Hinzufügen von Daten zu dieser Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="49705-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="49705-125">IApplicationModelProvider</span><span class="sxs-lookup"><span data-stu-id="49705-125">IApplicationModelProvider</span></span>

<span data-ttu-id="49705-126">ASP.NET Core MVC lädt das Anwendungsmodell über ein durch die Schnittstelle [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) definiertes Anbietermuster.</span><span class="sxs-lookup"><span data-stu-id="49705-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="49705-127">Dieser Abschnitt enthält einige interne Implementierungsdetails zur Funktionsweise dieses Anbieters.</span><span class="sxs-lookup"><span data-stu-id="49705-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="49705-128">Dies ist allerdings ein Thema für Fortgeschrittene. In den meisten Apps, die das Anwendungsmodell nutzen, sollte mit Konventionen gearbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="49705-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="49705-129">Implementierungen der Schnittstelle `IApplicationModelProvider` umschließen sich gegenseitig. Dabei wird bei den einzelnen Implementierungen die Methode `OnProvidersExecuting` in aufsteigender Reihenfolge basierend auf der zugehörigen Eigenschaft `Order` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="49705-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="49705-130">Anschließend wird die Methode `OnProvidersExecuted` in umgekehrter Reihenfolge aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="49705-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="49705-131">Das Framework definiert verschiedene Anbieter:</span><span class="sxs-lookup"><span data-stu-id="49705-131">The framework defines several providers:</span></span>

<span data-ttu-id="49705-132">Erst (`Order=-1000`):</span><span class="sxs-lookup"><span data-stu-id="49705-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="49705-133">Then (`Order=-990`):</span><span class="sxs-lookup"><span data-stu-id="49705-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="49705-134">Die Reihenfolge, in der zwei Anbieter mit dem gleichen Wert für `Order` aufgerufen werden, ist nicht definiert und sollte daher nicht als zuverlässig betrachtet werden.</span><span class="sxs-lookup"><span data-stu-id="49705-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="49705-135">`IApplicationModelProvider` ist ein erweitertes, auszuweitendes Konzept für Autoren von Frameworks.</span><span class="sxs-lookup"><span data-stu-id="49705-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="49705-136">Im Allgemeinen sollten in Apps Konventionen und in Frameworks Anbieter verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="49705-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="49705-137">Der Hauptunterschied besteht darin, dass Anbieter immer vor Konventionen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="49705-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="49705-138">Das Konzept `DefaultApplicationModelProvider` etabliert viele der von ASP.NET Core MVC verwendeten Standardverhaltensweisen.</span><span class="sxs-lookup"><span data-stu-id="49705-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="49705-139">Zu den Zuständigkeiten des Konzepts zählen die folgenden:</span><span class="sxs-lookup"><span data-stu-id="49705-139">Its responsibilities include:</span></span>

* <span data-ttu-id="49705-140">Hinzufügen globaler Filter zum Kontext</span><span class="sxs-lookup"><span data-stu-id="49705-140">Adding global filters to the context</span></span>
* <span data-ttu-id="49705-141">Hinzufügen von Controllern zum Kontext</span><span class="sxs-lookup"><span data-stu-id="49705-141">Adding controllers to the context</span></span>
* <span data-ttu-id="49705-142">Hinzufügen öffentlicher Controllermethoden in Form von Aktionen</span><span class="sxs-lookup"><span data-stu-id="49705-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="49705-143">Hinzufügen von Aktionsmethodenparametern zum Kontext</span><span class="sxs-lookup"><span data-stu-id="49705-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="49705-144">Anwenden einer Route und anderer Attribute</span><span class="sxs-lookup"><span data-stu-id="49705-144">Applying route and other attributes</span></span>

<span data-ttu-id="49705-145">Einige integrierte Verhaltensweisen werden vom `DefaultApplicationModelProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="49705-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="49705-146">Dieser Anbieter ist für das Erstellen von zuständig [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) , das wiederum auf [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) -,-und-Instanzen verweist [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) .</span><span class="sxs-lookup"><span data-stu-id="49705-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="49705-147">Die `DefaultApplicationModelProvider`-Klasse stellt ein Detail zur Implementierung des internen Frameworks dar und wird sich in Zukunft ändern.</span><span class="sxs-lookup"><span data-stu-id="49705-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="49705-148">`AuthorizationApplicationModelProvider` ist für die Anwendung des Verhaltens zuständig, das den Attributen `AuthorizeFilter` und `AllowAnonymousFilter` zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="49705-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="49705-149">[Weitere Informationen zu diesen Attributen](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="49705-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="49705-150">`CorsApplicationModelProvider` implementiert Verhalten, das dem `IEnableCorsAttribute` und `IDisableCorsAttribute` sowie dem `DisableCorsAuthorizationFilter` zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="49705-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="49705-151">[Weitere Informationen zu CORS](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="49705-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="49705-152">Konventionen</span><span class="sxs-lookup"><span data-stu-id="49705-152">Conventions</span></span>

<span data-ttu-id="49705-153">Das Anwendungsmodell definiert Konventionsabstraktionen, die eine einfachere Möglichkeit zur Anpassung des Verhaltens der Modelle bieten, wodurch auf eine Überschreibung des gesamten Modells oder Anbieters verzichtet werden kann.</span><span class="sxs-lookup"><span data-stu-id="49705-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="49705-154">Diese Abstraktionen werden als Methode für eine Änderung des Verhaltens Ihrer App empfohlen.</span><span class="sxs-lookup"><span data-stu-id="49705-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="49705-155">Mithilfe von Konventionen können Sie Code schreiben, der Anpassungen dynamisch anwendet.</span><span class="sxs-lookup"><span data-stu-id="49705-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="49705-156">Während [Filter](xref:mvc/controllers/filters) eine Möglichkeit zum Ändern des Frameworkverhaltens bieten, können Sie mithilfe von Anpassungen steuern, wie die einzelnen Komponenten der App zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="49705-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="49705-157">Folgende Konventionen sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="49705-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="49705-158">Konventionen werden angewendet, indem Sie den MVC-Optionen hinzugefügt werden, oder indem Sie `Attribute` s implementieren und auf Controller, Aktionen oder Aktionsparameter anwenden (ähnlich wie [`Filters`](xref:mvc/controllers/filters) ).</span><span class="sxs-lookup"><span data-stu-id="49705-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="49705-159">Im Gegenteil zu Filtern werden Konventionen nur beim Starten der App ausgeführt, nicht im Rahmen einzelner Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="49705-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="49705-160">Beispiel: Ändern von ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="49705-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="49705-161">Die folgende Konvention wird zum Hinzufügen einer Eigenschaft zum Anwendungsmodell verwendet.</span><span class="sxs-lookup"><span data-stu-id="49705-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="49705-162">Konventionen von Anwendungsmodellen werden als Optionen angewendet, wenn MVC in `ConfigureServices` zu `Startup` hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="49705-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="49705-163">Auf Eigenschaften kann innerhalb von Controlleraktionen über die `ActionDescriptor`-Eigenschaftensammlung zugegriffen werden:</span><span class="sxs-lookup"><span data-stu-id="49705-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="49705-164">Beispiel: Ändern der ControllerModel-Beschreibung</span><span class="sxs-lookup"><span data-stu-id="49705-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="49705-165">Wie im vorherigen Beispiel kann auch das Controllermodell so geändert werden, dass es benutzerdefinierte Eigenschaften enthält.</span><span class="sxs-lookup"><span data-stu-id="49705-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="49705-166">Diese Eigenschaften überschreiben vorhandene Eigenschaften mit dem gleichen Namen, die im Anwendungsmodell angegeben sind.</span><span class="sxs-lookup"><span data-stu-id="49705-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="49705-167">Das folgende Konventionsattribut fügt eine Beschreibung auf Controllerebene hinzu:</span><span class="sxs-lookup"><span data-stu-id="49705-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="49705-168">Diese Konvention wird als Attribut für einen Controller angewendet.</span><span class="sxs-lookup"><span data-stu-id="49705-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="49705-169">Auf die Eigenschaft „Beschreibung“ wird auf die gleiche Weise wie in vorherigen Beispielen zugegriffen.</span><span class="sxs-lookup"><span data-stu-id="49705-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="49705-170">Beispiel: Ändern der ActionModel-Beschreibung</span><span class="sxs-lookup"><span data-stu-id="49705-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="49705-171">Auf einzelne Aktionen kann eine separate Attributkonvention angewendet werden, mit der das bereits auf Anwendungs- oder Controllerebene angewendete Verhalten überschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="49705-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="49705-172">Eine Anwendung dieser Konvention auf eine Aktion innerhalb des Controllers aus dem vorherigen Beispiel veranschaulicht, wie die Konvention auf Controllerebene überschrieben wird:</span><span class="sxs-lookup"><span data-stu-id="49705-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="49705-173">Beispiel: Ändern von ParameterModel</span><span class="sxs-lookup"><span data-stu-id="49705-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="49705-174">Die folgende Konvention kann auf Aktionsparameter zur Änderung ihrer `BindingInfo` angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="49705-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="49705-175">Für folgende Konvention ist erforderlich, dass es sich bei dem Parameter um einen Routenparameter handelt; andere mögliche Bindungsquellen (z.B. Abfragezeichenfolgewerte) werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="49705-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="49705-176">Das Attribut kann möglicherweise auf sämtliche Aktionsparameter angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="49705-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="49705-177">Beispiel: Ändern des ActionModel-Namens</span><span class="sxs-lookup"><span data-stu-id="49705-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="49705-178">Folgende Konvention ändert das `ActionModel`, um den *Namen* der Aktion zu aktualisieren, auf die sie angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="49705-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="49705-179">Der neue Name wird als Parameter für das Attribut bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="49705-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="49705-180">Er wird beim Routing verwendet und beeinflusst die Route, die zum Erreichen dieser Aktionsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="49705-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="49705-181">Dieses Attribut wird auf eine Aktionsmethode im `HomeController` angewendet:</span><span class="sxs-lookup"><span data-stu-id="49705-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="49705-182">Obwohl der Methodenname `SomeName` lautet, überschreibt das Attribut die MVC-Konvention für die Verwendung des Methodennamens und ersetzt den Aktionsnamen durch `MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="49705-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="49705-183">Daher lautet die Route zum Erreichen dieser Aktion `/Home/MyCoolAction`.</span><span class="sxs-lookup"><span data-stu-id="49705-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="49705-184">Dieses Beispiel ist im Wesentlichen mit der Verwendung des integrierten Attributs [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) identisch.</span><span class="sxs-lookup"><span data-stu-id="49705-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="49705-185">Beispiel: Benutzerdefinierte Routingkonvention</span><span class="sxs-lookup"><span data-stu-id="49705-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="49705-186">Sie können die Funktionsweise von Routing mithilfe einer `IApplicationModelConvention` anpassen.</span><span class="sxs-lookup"><span data-stu-id="49705-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="49705-187">Die folgende Konvention bezieht beispielsweise Namespaces von Controllern in ihre Routen ein und ersetzt dabei in der Route `.` im Namespace durch `/`:</span><span class="sxs-lookup"><span data-stu-id="49705-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="49705-188">Die Konvention wird als Option unter „Start“ hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="49705-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="49705-189">Sie können Konventionen zu Ihrer [Middleware](xref:fundamentals/middleware/index) hinzufügen, indem Sie über `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));` auf `MvcOptions` zugreifen.</span><span class="sxs-lookup"><span data-stu-id="49705-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="49705-190">Im nachfolgenden Beispiel wird diese Konvention auf Routen angewendet, die kein Attributrouting verwenden, bei dem der Name des Controllers „Namespace“ enthält.</span><span class="sxs-lookup"><span data-stu-id="49705-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="49705-191">Der folgende Controller veranschaulicht diese Konvention:</span><span class="sxs-lookup"><span data-stu-id="49705-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="49705-192">Verwendung eines Anwendungsmodells in WebApiCompatShim</span><span class="sxs-lookup"><span data-stu-id="49705-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="49705-193">ASP.NET Core MVC verwendet andere Konventionen aus ASP.NET-Web-API 2.</span><span class="sxs-lookup"><span data-stu-id="49705-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="49705-194">Mithilfe von benutzerdefinierten Konventionen können Sie das Verhalten einer ASP.NET Core-MVC-App so ändern, dass es mit dem einer Web-API-App konsistent ist.</span><span class="sxs-lookup"><span data-stu-id="49705-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="49705-195">Microsoft liefert die [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) speziell für diesen Zweck aus.</span><span class="sxs-lookup"><span data-stu-id="49705-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="49705-196">Hier finden Sie weitere Informationen zum [Migrieren der ASP.NET-Web-API](xref:migration/webapi).</span><span class="sxs-lookup"><span data-stu-id="49705-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="49705-197">Für die Verwendung der Web-API-Kompatibilitätsshim müssen Sie das Paket zu Ihrem Projekt und anschließend die Konventionen zu MVC hinzufügen, indem Sie `AddWebApiConventions`unter `Startup` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="49705-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="49705-198">Die von der Shim bereitgestellten Konventionen gelten nur für Teile der App, auf die bestimmte Attribute angewendet worden sind.</span><span class="sxs-lookup"><span data-stu-id="49705-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="49705-199">Die folgenden vier Attribute steuern, bei welchen Controllern die zugehörigen Konventionen durch die Konventionen der Shim geändert werden sollten:</span><span class="sxs-lookup"><span data-stu-id="49705-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="49705-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="49705-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="49705-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="49705-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="49705-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="49705-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="49705-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="49705-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="49705-204">Aktionskonventionen</span><span class="sxs-lookup"><span data-stu-id="49705-204">Action Conventions</span></span>

<span data-ttu-id="49705-205">Das `UseWebApiActionConventionsAttribute` wird für die Zuordnung der HTTP-Methode zu Aktionen basierend auf ihrem Namen verwendet (beispielsweise würde `Get` der Aktion `HttpGet` zugeordnet).</span><span class="sxs-lookup"><span data-stu-id="49705-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="49705-206">Es gilt nur für Aktionen, die kein Attributrouting verwenden.</span><span class="sxs-lookup"><span data-stu-id="49705-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="49705-207">Überladen</span><span class="sxs-lookup"><span data-stu-id="49705-207">Overloading</span></span>

<span data-ttu-id="49705-208">Das `UseWebApiOverloadingAttribute` wird für die Anwendung der Konvention `WebApiOverloadingApplicationModelConvention` verwendet.</span><span class="sxs-lookup"><span data-stu-id="49705-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="49705-209">Diese Konvention fügt eine `OverloadActionConstraint` zum Aktionsauswahlprozess hinzu, wodurch mögliche Aktionen auf die Aktionen begrenzt werden, bei denen die Anforderung alle nicht optionalen Parameter erfüllt.</span><span class="sxs-lookup"><span data-stu-id="49705-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="49705-210">Parameterkonventionen</span><span class="sxs-lookup"><span data-stu-id="49705-210">Parameter Conventions</span></span>

<span data-ttu-id="49705-211">Das `UseWebApiParameterConventionsAttribute` wird für die Anwendung der Aktionskonvention `WebApiParameterConventionsApplicationModelConvention` verwendet.</span><span class="sxs-lookup"><span data-stu-id="49705-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="49705-212">Diese Konvention gibt an, dass einfache Typen, die als Aktionsparameter verwendet werden, standardmäßig vom URI abhängen, während komplexe Typen vom Anforderungstext abhängen.</span><span class="sxs-lookup"><span data-stu-id="49705-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="49705-213">Routen</span><span class="sxs-lookup"><span data-stu-id="49705-213">Routes</span></span>

<span data-ttu-id="49705-214">Das `UseWebApiRoutesAttribute` steuert, ob die Controllerkonvention `WebApiApplicationModelConvention` angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="49705-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="49705-215">Bei Aktivierung wird diese Konvention verwendet, um Unterstützung für [Bereiche](xref:mvc/controllers/areas) zur Route hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="49705-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="49705-216">Neben einer Reihe von Konventionen enthält das Kompatibilitätspaket eine `System.Web.Http.ApiController`-Basisklasse, welche die von der Web-API bereitgestellte Klasse ersetzt.</span><span class="sxs-lookup"><span data-stu-id="49705-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="49705-217">Dadurch funktionieren Ihre Controller, die für die Web-API geschrieben wurden und von dem zugehörigen `ApiController` übernommen werden, bei der Ausführung im ASP.NET Core-MVC so, wie sie entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="49705-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="49705-218">Alle oben aufgeführten `UseWebApi*`-Attribute werden auf die Basiscontrollerklasse angewendet.</span><span class="sxs-lookup"><span data-stu-id="49705-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="49705-219">Der `ApiController` macht Eigenschaften, Methoden und Ergebnistypen verfügbar, die mit denen in der Web-API kompatibel sind.</span><span class="sxs-lookup"><span data-stu-id="49705-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="49705-220">Verwenden von ApiExplorer zum Dokumentieren Ihrer App</span><span class="sxs-lookup"><span data-stu-id="49705-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="49705-221">Das Anwendungsmodell macht eine [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) -Eigenschaft auf jeder Ebene verfügbar, die zum Durchlaufen der Struktur der APP verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="49705-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="49705-222">Diese kann zum [Generieren von Hilfeseiten für Ihre Web-APIs mithilfe von Tools wie Swagger](xref:tutorials/web-api-help-pages-using-swagger) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="49705-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="49705-223">Die Eigenschaft `ApiExplorer` macht eine Eigenschaft vom Typ `IsVisible` verfügbar, die festgelegt werden kann, um anzugeben, welche Teile des Modells Ihrer App verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="49705-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="49705-224">Sie können diese Einstellung mithilfe einer Konvention konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="49705-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="49705-225">Mit diesem Ansatz (und ggf. zusätzlichen Konventionen) können Sie die API-Sichtbarkeit auf einer beliebigen Ebene innerhalb Ihrer App aktivieren oder deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="49705-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
