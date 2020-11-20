---
title: Modellbindung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie die Modellbindung in ASP.NET Core funktioniert und wie Sie ihr Verhalten anpassen.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 4de34a75da932b41190caa8434ac5be8cc0710fd
ms.sourcegitcommit: 8363e44f630fcc6433ccd2a85f7aa9567cd274ed
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981933"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="eb660-103">Modellbindung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="eb660-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eb660-104">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="eb660-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="eb660-105">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb660-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="eb660-106">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="eb660-106">What is Model binding</span></span>

<span data-ttu-id="eb660-107">Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="eb660-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="eb660-108">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eb660-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="eb660-109">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="eb660-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="eb660-110">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="eb660-110">Model binding automates this process.</span></span> <span data-ttu-id="eb660-111">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="eb660-111">The model binding system:</span></span>

* <span data-ttu-id="eb660-112">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="eb660-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="eb660-113">Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="eb660-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="eb660-114">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="eb660-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="eb660-115">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="eb660-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="eb660-116">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eb660-116">Example</span></span>

<span data-ttu-id="eb660-117">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="eb660-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="eb660-118">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="eb660-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="eb660-119">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="eb660-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="eb660-120">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="eb660-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="eb660-121">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="eb660-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="eb660-122">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="eb660-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="eb660-123">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="eb660-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="eb660-124">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="eb660-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="eb660-125">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="eb660-126">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="eb660-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="eb660-127">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="eb660-128">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="eb660-129">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="eb660-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="eb660-130">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb660-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="eb660-131">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="eb660-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="eb660-132">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="eb660-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="eb660-133">Ziele</span><span class="sxs-lookup"><span data-stu-id="eb660-133">Targets</span></span>

<span data-ttu-id="eb660-134">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="eb660-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="eb660-135">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="eb660-136">Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="eb660-137">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="eb660-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="eb660-138">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-138">[BindProperty] attribute</span></span>

<span data-ttu-id="eb660-139">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="eb660-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="eb660-140">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-140">[BindProperties] attribute</span></span>

<span data-ttu-id="eb660-141">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="eb660-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="eb660-142">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="eb660-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="eb660-143">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eb660-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="eb660-144">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="eb660-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="eb660-145">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="eb660-146">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="eb660-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="eb660-147">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="eb660-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="eb660-148">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="eb660-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="eb660-149">Quellen</span><span class="sxs-lookup"><span data-stu-id="eb660-149">Sources</span></span>

<span data-ttu-id="eb660-150">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="eb660-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="eb660-151">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="eb660-151">Form fields</span></span>
1. <span data-ttu-id="eb660-152">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="eb660-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="eb660-153">Routendaten</span><span class="sxs-lookup"><span data-stu-id="eb660-153">Route data</span></span>
1. <span data-ttu-id="eb660-154">Abfragezeichenfolge-Parameter</span><span class="sxs-lookup"><span data-stu-id="eb660-154">Query string parameters</span></span>
1. <span data-ttu-id="eb660-155">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="eb660-155">Uploaded files</span></span>

<span data-ttu-id="eb660-156">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="eb660-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="eb660-157">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="eb660-157">There are a few exceptions:</span></span>

* <span data-ttu-id="eb660-158">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="eb660-159">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="eb660-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="eb660-160">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="eb660-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="eb660-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ruft Werte aus der Abfrage Zeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="eb660-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="eb660-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ruft Werte aus den Feldern für gepostete Formulare ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="eb660-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ruft Werte aus dem Anforderungs Text ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="eb660-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="eb660-166">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="eb660-166">These attributes:</span></span>

* <span data-ttu-id="eb660-167">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="eb660-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="eb660-168">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="eb660-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="eb660-169">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="eb660-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="eb660-170">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="eb660-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="eb660-171">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-171">[FromBody] attribute</span></span>

<span data-ttu-id="eb660-172">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="eb660-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="eb660-173">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="eb660-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="eb660-174">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="eb660-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="eb660-175">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="eb660-176">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="eb660-177">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="eb660-178">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-178">In the preceding example:</span></span>

* <span data-ttu-id="eb660-179">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="eb660-180">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="eb660-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="eb660-181">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="eb660-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="eb660-182">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="eb660-183">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="eb660-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="eb660-184">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="eb660-185">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="eb660-185">Additional sources</span></span>

<span data-ttu-id="eb660-186">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="eb660-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="eb660-187">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="eb660-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="eb660-188">Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand.</span><span class="sxs-lookup"><span data-stu-id="eb660-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="eb660-189">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="eb660-189">To get data from a new source:</span></span>

* <span data-ttu-id="eb660-190">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="eb660-191">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="eb660-192">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eb660-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="eb660-193">Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie .</span><span class="sxs-lookup"><span data-stu-id="eb660-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="eb660-194">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb660-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="eb660-195">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="eb660-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="eb660-196">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="eb660-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="eb660-197">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb660-197">No source for a model property</span></span>

<span data-ttu-id="eb660-198">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="eb660-199">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb660-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="eb660-200">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="eb660-201">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="eb660-202">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="eb660-203">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="eb660-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="eb660-204">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="eb660-205">Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="eb660-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="eb660-206">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="eb660-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="eb660-207">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="eb660-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="eb660-208">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="eb660-208">Type conversion errors</span></span>

<span data-ttu-id="eb660-209">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb660-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="eb660-210">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="eb660-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="eb660-211">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="eb660-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="eb660-212">Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="eb660-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="eb660-213">Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor .</span><span class="sxs-lookup"><span data-stu-id="eb660-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="eb660-214">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="eb660-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="eb660-215">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="eb660-216">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="eb660-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="eb660-217">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="eb660-218">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="eb660-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="eb660-219">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="eb660-220">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="eb660-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="eb660-221">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="eb660-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="eb660-222">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="eb660-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="eb660-223">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="eb660-223">Simple types</span></span>

<span data-ttu-id="eb660-224">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="eb660-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="eb660-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="eb660-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="eb660-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="eb660-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="eb660-227">Char</span><span class="sxs-lookup"><span data-stu-id="eb660-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="eb660-228">DateTime</span><span class="sxs-lookup"><span data-stu-id="eb660-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="eb660-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="eb660-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="eb660-230">Decimal</span><span class="sxs-lookup"><span data-stu-id="eb660-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="eb660-231">Double</span><span class="sxs-lookup"><span data-stu-id="eb660-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="eb660-232">Enum</span><span class="sxs-lookup"><span data-stu-id="eb660-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="eb660-233">GUID</span><span class="sxs-lookup"><span data-stu-id="eb660-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="eb660-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb660-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="eb660-235">Single</span><span class="sxs-lookup"><span data-stu-id="eb660-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="eb660-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="eb660-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="eb660-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb660-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="eb660-238">URI</span><span class="sxs-lookup"><span data-stu-id="eb660-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="eb660-239">Version</span><span class="sxs-lookup"><span data-stu-id="eb660-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="eb660-240">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="eb660-240">Complex types</span></span>

<span data-ttu-id="eb660-241">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="eb660-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="eb660-242">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="eb660-243">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="eb660-244">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="eb660-245">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="eb660-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="eb660-246">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb660-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="eb660-247">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="eb660-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="eb660-248">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="eb660-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="eb660-249">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="eb660-249">Prefix = parameter name</span></span>

<span data-ttu-id="eb660-250">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="eb660-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="eb660-251">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="eb660-252">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="eb660-253">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb660-253">Prefix = property name</span></span>

<span data-ttu-id="eb660-254">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="eb660-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="eb660-255">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb660-256">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="eb660-257">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="eb660-257">Custom prefix</span></span>

<span data-ttu-id="eb660-258">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="eb660-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="eb660-259">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb660-260">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="eb660-261">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="eb660-261">Attributes for complex type targets</span></span>

<span data-ttu-id="eb660-262">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="eb660-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="eb660-263">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="eb660-264">Sie haben \***nicht** _ Auswirkungen auf Eingabe Formatierungen, die gepostete JSON-und XML-Anforderungs Texte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="eb660-264">They do \***not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="eb660-265">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="eb660-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="eb660-266">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-266">[Bind] attribute</span></span>

<span data-ttu-id="eb660-267">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="eb660-268">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="eb660-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="eb660-269">`[Bind]` wirkt sich _*_nicht_*_ auf Eingabe Formatierer aus.</span><span class="sxs-lookup"><span data-stu-id="eb660-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="eb660-270">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="eb660-271">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="eb660-272">Das- `[Bind]` Attribut kann verwendet werden, um in _create \*-Szenarios vor overposting zu schützen.</span><span class="sxs-lookup"><span data-stu-id="eb660-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="eb660-273">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="eb660-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="eb660-274">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="eb660-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="eb660-275">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="eb660-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="eb660-276">[Modelbinder]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="eb660-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> kann auf Typen, Eigenschaften oder Parameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="eb660-278">Es ermöglicht die Angabe des Typs des Modell Binders, der zum Binden der spezifischen Instanz oder des Typs verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="eb660-279">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="eb660-280">Das `[ModelBinder]` -Attribut kann auch verwendet werden, um den Namen einer Eigenschaft oder eines Parameters zu ändern, wenn er Modell gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="eb660-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="eb660-281">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-281">[BindRequired] attribute</span></span>

<span data-ttu-id="eb660-282">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb660-283">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="eb660-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="eb660-284">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="eb660-285">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb660-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="eb660-286">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-286">[BindNever] attribute</span></span>

<span data-ttu-id="eb660-287">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb660-288">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="eb660-289">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="eb660-290">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="eb660-290">Collections</span></span>

<span data-ttu-id="eb660-291">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb660-292">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb660-293">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-293">For example:</span></span>

* <span data-ttu-id="eb660-294">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="eb660-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="eb660-295">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="eb660-295">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="eb660-296">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eb660-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="eb660-297">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="eb660-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb660-298">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="eb660-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="eb660-299">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="eb660-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="eb660-300">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="eb660-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="eb660-301">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="eb660-302">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="eb660-303">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="eb660-303">Dictionaries</span></span>

<span data-ttu-id="eb660-304">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb660-305">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb660-306">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-306">For example:</span></span>

* <span data-ttu-id="eb660-307">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="eb660-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="eb660-308">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="eb660-308">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="eb660-309">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="eb660-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb660-310">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="eb660-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="eb660-311">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="eb660-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="eb660-312">Konstruktorbindungs-und Daten Satz Typen</span><span class="sxs-lookup"><span data-stu-id="eb660-312">Constructor binding and record types</span></span>

<span data-ttu-id="eb660-313">Die Modell Bindung erfordert, dass komplexe Typen über einen Parameter losen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="eb660-314">Sowohl `System.Text.Json` als auch `Newtonsoft.Json` basierte Eingabe Formatierer unterstützen die Deserialisierung von Klassen, die über keinen Parameter losen Konstruktor verfügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="eb660-315">C# 9 führt Daten Satz Typen ein, die eine hervorragend Möglichkeit darstellen, Daten über das Netzwerk zu übermitteln.</span><span class="sxs-lookup"><span data-stu-id="eb660-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="eb660-316">ASP.net Core fügt Unterstützung für die Modell Bindung und die Validierung von Daten Satz Typen mit einem einzelnen Konstruktor hinzu:</span><span class="sxs-lookup"><span data-stu-id="eb660-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="eb660-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="eb660-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="eb660-318">Beim Überprüfen von Daten Satz Typen sucht die Common Language Runtime anstelle von Eigenschaften nach Validierungs Metadaten speziell für Parameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="eb660-319">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="eb660-320">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="eb660-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="eb660-321">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="eb660-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="eb660-322">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="eb660-323">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="eb660-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="eb660-324">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="eb660-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="eb660-325">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="eb660-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="eb660-326">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="eb660-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="eb660-327">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="eb660-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="eb660-328">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="eb660-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="eb660-329">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="eb660-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="eb660-330">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="eb660-330">Special data types</span></span>

<span data-ttu-id="eb660-331">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="eb660-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="eb660-332">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="eb660-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="eb660-333">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="eb660-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="eb660-334">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eb660-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="eb660-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="eb660-335">CancellationToken</span></span>

<span data-ttu-id="eb660-336">Aktionen können optional einen `CancellationToken` als Parameter binden.</span><span class="sxs-lookup"><span data-stu-id="eb660-336">Actions can optionally bind a `CancellationToken` as a parameter.</span></span> <span data-ttu-id="eb660-337">Dies bindet <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> , wenn die Verbindung, die der HTTP-Anforderung zugrunde liegt, abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-337">This binds <xref:Microsoft.AspNetCore.Http.HttpContext.RequestAborted> that signals when the connection underlying the HTTP request is aborted.</span></span> <span data-ttu-id="eb660-338">Aktionen können diesen Parameter verwenden, um asynchrone Vorgänge mit langer Laufzeit abzubrechen, die als Teil der Controller Aktionen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-338">Actions can use this parameter to cancel long running async operations that are executed as part of the controller actions.</span></span>

### <a name="formcollection"></a><span data-ttu-id="eb660-339">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="eb660-339">FormCollection</span></span>

<span data-ttu-id="eb660-340">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="eb660-340">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="eb660-341">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="eb660-341">Input formatters</span></span>

<span data-ttu-id="eb660-342">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="eb660-342">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="eb660-343">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="eb660-343">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="eb660-344">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="eb660-344">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="eb660-345">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-345">You can add other formatters for other content types.</span></span>

<span data-ttu-id="eb660-346">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="eb660-346">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="eb660-347">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="eb660-347">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="eb660-348">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="eb660-348">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="eb660-349">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="eb660-349">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="eb660-350">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="eb660-350">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="eb660-351">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="eb660-351">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="eb660-352">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="eb660-352">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="eb660-353">Anpassen der Modellbindung mit Eingabeformatierern</span><span class="sxs-lookup"><span data-stu-id="eb660-353">Customize model binding with input formatters</span></span>

<span data-ttu-id="eb660-354">Ein Eingabeformatierer ist in vollem Umfang für das Lesen von Daten aus dem Anforderungstext verantwortlich.</span><span class="sxs-lookup"><span data-stu-id="eb660-354">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="eb660-355">Um diesen Prozess anzupassen, konfigurieren Sie die APIs, die vom Eingabeformatierer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-355">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="eb660-356">In diesem Abschnitt wird beschrieben, wie Sie den auf `System.Text.Json` basierenden Eingabeformatierer so anpassen, dass er einen benutzerdefinierten Typ mit dem Namen `ObjectId` versteht.</span><span class="sxs-lookup"><span data-stu-id="eb660-356">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="eb660-357">Betrachten Sie das folgende Modell, das eine benutzerdefinierte `ObjectId`-Eigenschaft mit dem Namen `Id` enthält:</span><span class="sxs-lookup"><span data-stu-id="eb660-357">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="eb660-358">Um den Modellbindungsprozess bei der Verwendung von `System.Text.Json`anzupassen, erstellen Sie eine aus <xref:System.Text.Json.Serialization.JsonConverter%601> abgeleitete Klasse:</span><span class="sxs-lookup"><span data-stu-id="eb660-358">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="eb660-359">Um einen benutzerdefinierten Konverter zu verwenden, wenden Sie das <xref:System.Text.Json.Serialization.JsonConverterAttribute>-Attribut auf den Typ an.</span><span class="sxs-lookup"><span data-stu-id="eb660-359">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="eb660-360">Im folgenden Beispiel wird der Typ `ObjectId` mit `ObjectIdConverter` als seinem benutzerdefinierten Konverter konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="eb660-360">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="eb660-361">Weitere Informationen finden Sie unter [Vorgehensweise: Schreiben benutzerdefinierter Konverter](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="eb660-361">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="eb660-362">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-362">Exclude specified types from model binding</span></span>

<span data-ttu-id="eb660-363">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="eb660-363">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="eb660-364">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-364">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="eb660-365">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="eb660-365">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="eb660-366">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb660-366">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb660-367">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="eb660-367">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="eb660-368">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb660-368">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb660-369">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="eb660-369">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="eb660-370">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="eb660-370">Custom model binders</span></span>

<span data-ttu-id="eb660-371">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="eb660-371">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="eb660-372">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb660-372">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="eb660-373">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-373">Manual model binding</span></span> 

<span data-ttu-id="eb660-374">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-374">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="eb660-375">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-375">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="eb660-376">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="eb660-376">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="eb660-377">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="eb660-377">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="eb660-378">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-378">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="eb660-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> verwendet Wertanbieter, um Daten aus dem Formulartext, der Abfragezeichenfolge und den Routendaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="eb660-379"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="eb660-380">`TryUpdateModelAsync` wird normalerweise so verwendet:</span><span class="sxs-lookup"><span data-stu-id="eb660-380">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="eb660-381">Wird mit Razor Seiten und MVC-Apps verwendet, die Controller und Ansichten verwenden, um eine Übertragung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="eb660-381">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="eb660-382">Nicht zusammen mit einer Web-API, es sei denn, sie wird von Formulardaten, Abfragezeichenfolgen und Routendaten konsumiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-382">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="eb660-383">Web-API-Endpunkte, die JSON nutzen, verwenden [Eingabeformatierer](#input-formatters), um den Anforderungstext in ein-Objekt zu deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="eb660-383">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="eb660-384">Weitere Informationen finden Sie unter [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="eb660-384">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="eb660-385">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-385">[FromServices] attribute</span></span>

<span data-ttu-id="eb660-386">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="eb660-386">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="eb660-387">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="eb660-387">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="eb660-388">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-388">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eb660-389">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-389">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb660-390">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb660-390">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eb660-391">In diesem Artikel wird erläutert, was Modellbindung ist, wie sie funktioniert, und wie Sie ihr Verhalten anpassen können.</span><span class="sxs-lookup"><span data-stu-id="eb660-391">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="eb660-392">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="eb660-392">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="eb660-393">Was ist Modellbindung?</span><span class="sxs-lookup"><span data-stu-id="eb660-393">What is Model binding</span></span>

<span data-ttu-id="eb660-394">Controller und Razor Seiten arbeiten mit Daten, die aus HTTP-Anforderungen stammen.</span><span class="sxs-lookup"><span data-stu-id="eb660-394">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="eb660-395">Routendaten können beispielsweise einen Datensatzschlüssel enthalten, und bereitgestellte Formularfelder können Werte für die Eigenschaften des Modells bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eb660-395">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="eb660-396">Das Schreiben von Code zum Abrufen jedes dieser Werte und deren Konvertierung aus Zeichenfolgen in .NET-Datentypen wäre mühsam und fehleranfällig.</span><span class="sxs-lookup"><span data-stu-id="eb660-396">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="eb660-397">Modellbindung automatisiert diesen Vorgang.</span><span class="sxs-lookup"><span data-stu-id="eb660-397">Model binding automates this process.</span></span> <span data-ttu-id="eb660-398">Das Modellbindungssystem:</span><span class="sxs-lookup"><span data-stu-id="eb660-398">The model binding system:</span></span>

* <span data-ttu-id="eb660-399">Ruft Daten aus verschiedenen Quellen ab, z. B. Routendaten, Formularfelder und Abfragezeichenfolgen.</span><span class="sxs-lookup"><span data-stu-id="eb660-399">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="eb660-400">Stellt die Daten für Controller und Razor Seiten in Methoden Parametern und öffentlichen Eigenschaften bereit.</span><span class="sxs-lookup"><span data-stu-id="eb660-400">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="eb660-401">Konvertiert Zeichenfolgendaten in .NET-Typen.</span><span class="sxs-lookup"><span data-stu-id="eb660-401">Converts string data to .NET types.</span></span>
* <span data-ttu-id="eb660-402">Aktualisiert Eigenschaften komplexer Typen.</span><span class="sxs-lookup"><span data-stu-id="eb660-402">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="eb660-403">Beispiel</span><span class="sxs-lookup"><span data-stu-id="eb660-403">Example</span></span>

<span data-ttu-id="eb660-404">Angenommen Sie haben die folgende Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="eb660-404">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="eb660-405">Und die App empfängt eine Anforderung mit dieser URL:</span><span class="sxs-lookup"><span data-stu-id="eb660-405">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="eb660-406">Die Modellbindung durchläuft die folgenden Schritte, nachdem das Routingsystem die Aktionsmethode ausgewählt hat:</span><span class="sxs-lookup"><span data-stu-id="eb660-406">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="eb660-407">Findet den ersten Parameters von `GetByID`, eine ganze Zahl namens `id`.</span><span class="sxs-lookup"><span data-stu-id="eb660-407">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="eb660-408">Durchsucht die verfügbaren Quellen in der HTTP-Anforderung und findet `id` = „2“ in den Routendaten.</span><span class="sxs-lookup"><span data-stu-id="eb660-408">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="eb660-409">Konvertiert der Zeichenfolge „2“ in die ganze Zahl 2.</span><span class="sxs-lookup"><span data-stu-id="eb660-409">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="eb660-410">Findet den nächsten Parameter von `GetByID`, einen booleschen Wert namens `dogsOnly`.</span><span class="sxs-lookup"><span data-stu-id="eb660-410">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="eb660-411">Durchsucht die Quellen und findet „DogsOnly=True“ in der Abfragezeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="eb660-411">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="eb660-412">Beim Abgleich von Namen wird die Groß- und Kleinschreibung nicht berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-412">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="eb660-413">Konvertiert die Zeichenfolge „true“ in den booleschen Wert `true`.</span><span class="sxs-lookup"><span data-stu-id="eb660-413">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="eb660-414">Das Framework ruft dann die `GetById`-Methode auf, und übergibt dabei als Eingabe „2“ für den `id`-Parameter und `true` für den `dogsOnly`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-414">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="eb660-415">Im vorherigen Beispiel sind die Ziele der Modellbindung Methodenparameter, die einfache Typen sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-415">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="eb660-416">Ziele können aber auch die Eigenschaften eines komplexen Typs sein.</span><span class="sxs-lookup"><span data-stu-id="eb660-416">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="eb660-417">Nachdem jede Eigenschaft erfolgreich gebunden wurde, erfolgt die [Modellvalidierung](xref:mvc/models/validation) für diese Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb660-417">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="eb660-418">Der Datensatz darüber, welche Daten an das Modell gebunden sind, sowie mit allen Bindungs- oder Validierungsfehlern wird in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) oder [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="eb660-418">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="eb660-419">Um herauszufinden, ob dieser Vorgang erfolgreich war, überprüft die App das Flag [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid).</span><span class="sxs-lookup"><span data-stu-id="eb660-419">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="eb660-420">Ziele</span><span class="sxs-lookup"><span data-stu-id="eb660-420">Targets</span></span>

<span data-ttu-id="eb660-421">Die Modellbindung versucht, Werte für die folgenden Arten von Zielen zu finden:</span><span class="sxs-lookup"><span data-stu-id="eb660-421">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="eb660-422">Parameter der Controlleraktionsmethode, zu der eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-422">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="eb660-423">Parameter der Razor pages-Handlermethode, an die eine Anforderung weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-423">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="eb660-424">Öffentliche Eigenschaften eines Controllers oder einer `PageModel`-Klasse, falls durch Attribute angegeben.</span><span class="sxs-lookup"><span data-stu-id="eb660-424">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="eb660-425">[BindProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-425">[BindProperty] attribute</span></span>

<span data-ttu-id="eb660-426">Kann auf eine öffentliche Eigenschaft eines Controllers oder einer `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, diese Eigenschaft als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="eb660-426">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="eb660-427">[BindProperties]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-427">[BindProperties] attribute</span></span>

<span data-ttu-id="eb660-428">Verfügbar in ASP.NET Core 2.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="eb660-428">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="eb660-429">Kann auf einen Controller oder eine `PageModel`-Klasse angewendet werden, um die Modellbindung anzuweisen, alle öffentlichen Eigenschaften dieser Klasse als Ziel zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="eb660-429">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="eb660-430">Modellbindung für HTTP-GET-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="eb660-430">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="eb660-431">Standardmäßig sind Eigenschaften für HTTP GET-Anforderungen nicht gebunden.</span><span class="sxs-lookup"><span data-stu-id="eb660-431">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="eb660-432">In der Regel ist alles, was Sie für eine GET-Anforderung benötigen, ein Datensatz-ID-Parameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-432">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="eb660-433">Die Datensatz-ID wird verwendet, um das Element in der Datenbank zu suchen.</span><span class="sxs-lookup"><span data-stu-id="eb660-433">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="eb660-434">Daher besteht keine Notwendigkeit, eine Eigenschaft zu binden, die eine Instanz des Modells enthält.</span><span class="sxs-lookup"><span data-stu-id="eb660-434">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="eb660-435">In Szenarien, in denen Sie Eigenschaften an Daten aus GET-Anforderungen binden möchten, legen Sie die Eigenschaft `SupportsGet` auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="eb660-435">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="eb660-436">Quellen</span><span class="sxs-lookup"><span data-stu-id="eb660-436">Sources</span></span>

<span data-ttu-id="eb660-437">Standardmäßig ruft die Modellbindung Daten in Form von Schlüssel-Wert-Paaren aus den folgenden Quellen in einer HTTP-Anforderung ab:</span><span class="sxs-lookup"><span data-stu-id="eb660-437">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="eb660-438">Formularfelder</span><span class="sxs-lookup"><span data-stu-id="eb660-438">Form fields</span></span>
1. <span data-ttu-id="eb660-439">Der Anforderungstext (für [Controller mit dem [ApiController]-Attribut](xref:web-api/index#binding-source-parameter-inference))</span><span class="sxs-lookup"><span data-stu-id="eb660-439">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="eb660-440">Routendaten</span><span class="sxs-lookup"><span data-stu-id="eb660-440">Route data</span></span>
1. <span data-ttu-id="eb660-441">Abfragezeichenfolge-Parameter</span><span class="sxs-lookup"><span data-stu-id="eb660-441">Query string parameters</span></span>
1. <span data-ttu-id="eb660-442">Hochgeladene Dateien</span><span class="sxs-lookup"><span data-stu-id="eb660-442">Uploaded files</span></span>

<span data-ttu-id="eb660-443">Für jeden Zielparameter oder jede Zieleigenschaft werden die Quellen nach der oben aufgeführten Reihenfolge überprüft.</span><span class="sxs-lookup"><span data-stu-id="eb660-443">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="eb660-444">Es gibt ein paar Ausnahmen:</span><span class="sxs-lookup"><span data-stu-id="eb660-444">There are a few exceptions:</span></span>

* <span data-ttu-id="eb660-445">Routendaten und Abfragezeichenfolgenwerte werden nur für einfache Typen verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-445">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="eb660-446">Hochgeladene Dateien werden nur an Zieltypen gebunden, die `IFormFile` oder `IEnumerable<IFormFile>` implementieren.</span><span class="sxs-lookup"><span data-stu-id="eb660-446">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="eb660-447">Wenn die Standardquelle nicht richtig ist, verwenden Sie eines der folgenden Attribute zum Festlegen der Quelle:</span><span class="sxs-lookup"><span data-stu-id="eb660-447">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="eb660-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Ruft Werte aus der Abfrage Zeichenfolge ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-448">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="eb660-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Ruft Werte aus Routendaten ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-449">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="eb660-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Ruft Werte aus den Feldern für gepostete Formulare ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-450">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="eb660-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -Ruft Werte aus dem Anforderungs Text ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-451">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="eb660-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -Ruft Werte aus HTTP-Headern ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-452">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="eb660-453">Diese Attribute:</span><span class="sxs-lookup"><span data-stu-id="eb660-453">These attributes:</span></span>

* <span data-ttu-id="eb660-454">Werden Modelleigenschaften einzeln hinzugefügt (nicht zur Modellklasse), wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="eb660-454">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="eb660-455">Akzeptieren optional einen Modellnamenswert im Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="eb660-455">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="eb660-456">Diese Option wird für den Fall bereitgestellt, dass der Eigenschaftenname nicht mit dem Wert in der Anforderung übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="eb660-456">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="eb660-457">Beispielsweise könnte der Wert in der Anforderung ein Header mit einem Bindestrich in seinem Namen sein, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="eb660-457">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="eb660-458">[FromBody]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-458">[FromBody] attribute</span></span>

<span data-ttu-id="eb660-459">Wenden Sie das `[FromBody]`-Attribut auf einen Parameter an, um dessen Eigenschaften über den Text einer HTTP-Anforderung aufzufüllen.</span><span class="sxs-lookup"><span data-stu-id="eb660-459">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="eb660-460">Die ASP.NET Core-Runtime delegiert die Verantwortung, für das Lesen des Texts an einen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="eb660-460">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="eb660-461">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="eb660-461">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="eb660-462">Wenn `[FromBody]` auf einen komplexen Typparameter angewendet wird, werden alle Bindungsquellenattribute ignoriert, die auf die Eigenschaften angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-462">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="eb660-463">Die folgende `Create`-Aktion legt beispielsweise fest, dass der `pet`-Parameter mithilfe des Texts aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-463">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="eb660-464">Die `Pet`-Klasse legt fest, dass ihre `Breed`-Eigenschaft mithilfe eines Abfragezeichenfolgenparameters aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-464">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="eb660-465">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-465">In the preceding example:</span></span>

* <span data-ttu-id="eb660-466">Das `[FromQuery]`-Attribut wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-466">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="eb660-467">Die `Breed`-Eigenschaft wird nicht mithilfe eines Abfragezeichenfolgenparameters aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="eb660-467">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="eb660-468">Eingabeformatierer lesen nur den Text und verstehen Bindungsquellenattribute nicht.</span><span class="sxs-lookup"><span data-stu-id="eb660-468">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="eb660-469">Wenn ein geeigneter Wert im Text gefunden wird, wird dieser Wert zum Auffüllen der `Breed`-Eigenschaft verwendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-469">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="eb660-470">Wenden Sie `[FromBody]` auf nicht mehr als einen Parameter pro Aktionsmethode an.</span><span class="sxs-lookup"><span data-stu-id="eb660-470">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="eb660-471">Sobald der Anforderungsdatenstrom von einem Eingabeformatierer gelesen wurde, ist er nicht mehr verfügbar, um für die Bindung anderer `[FromBody]`-Parameter nochmal gelesen zu werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-471">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="eb660-472">Zusätzliche Quellen</span><span class="sxs-lookup"><span data-stu-id="eb660-472">Additional sources</span></span>

<span data-ttu-id="eb660-473">Quelldaten werden dem Modellbindungssystem durch *Wertanbieter* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="eb660-473">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="eb660-474">Sie können benutzerdefinierte Wertanbieter schreiben und registrieren, die Daten für die Modellbindung aus anderen Quellen abrufen.</span><span class="sxs-lookup"><span data-stu-id="eb660-474">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="eb660-475">Angenommen, Sie möchten Daten aus cookie s oder dem Sitzungszustand.</span><span class="sxs-lookup"><span data-stu-id="eb660-475">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="eb660-476">So rufen Sie Daten aus einer neuen Quelle ab</span><span class="sxs-lookup"><span data-stu-id="eb660-476">To get data from a new source:</span></span>

* <span data-ttu-id="eb660-477">Erstellen Sie eine Klasse, die das `IValueProvider` implementiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-477">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="eb660-478">Erstellen Sie eine Klasse, die das `IValueProviderFactory` implementiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-478">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="eb660-479">Registrieren Sie die Factoryklasse in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="eb660-479">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="eb660-480">Die Beispiel-app enthält einen [Wert Anbieter](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) und ein [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) -Beispiel, mit dem Werte von s abgerufen werden cookie .</span><span class="sxs-lookup"><span data-stu-id="eb660-480">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="eb660-481">Dies ist der Registrierungscode in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="eb660-481">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="eb660-482">Der angezeigte Code fügt den benutzerdefinierten Wertanbieter hinter allen integrierten Wertanbietern ein.</span><span class="sxs-lookup"><span data-stu-id="eb660-482">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="eb660-483">Damit er der erste in der Liste wird, rufen Sie `Insert(0, new CookieValueProviderFactory())` anstelle von `Add` auf.</span><span class="sxs-lookup"><span data-stu-id="eb660-483">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="eb660-484">Keine Quelle für eine Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb660-484">No source for a model property</span></span>

<span data-ttu-id="eb660-485">Standardmäßig wird kein Modellzustandsfehler erstellt, wenn kein Wert für eine Modelleigenschaft gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-485">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="eb660-486">Die Eigenschaft wird auf „null“ oder einen Standardwert festgelegt:</span><span class="sxs-lookup"><span data-stu-id="eb660-486">The property is set to null or a default value:</span></span>

* <span data-ttu-id="eb660-487">Einfache Nullable-Typen werden auf `null` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-487">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="eb660-488">Nicht-Nullable-Werttypen werden auf `default(T)` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-488">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="eb660-489">Beispiel: Ein Parameter `int id` wird auf „0“ festgelegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-489">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="eb660-490">Für komplexe Typen erstellt die Modellbindung eine Instanz, indem der Standardkonstruktor verwendet wird, ohne Eigenschaften festzulegen.</span><span class="sxs-lookup"><span data-stu-id="eb660-490">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="eb660-491">Arrays werden auf `Array.Empty<T>()` festgelegt, mit der Ausnahme, dass `byte[]`-Arrays auf `null` festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-491">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="eb660-492">Wenn der Modell Zustand für eine Modell Eigenschaft ungültig gemacht werden soll, wenn nichts gefunden wird, verwenden Sie das- [`[BindRequired]`](#bindrequired-attribute) Attribut.</span><span class="sxs-lookup"><span data-stu-id="eb660-492">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="eb660-493">Beachten Sie, dass dieses `[BindRequired]`-Verhalten für die Modellbindung von Daten aus bereitgestellten Formulardaten gilt, nicht für JSON- oder XML-Daten in einem Anforderungstext.</span><span class="sxs-lookup"><span data-stu-id="eb660-493">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="eb660-494">Anforderungstextdaten werden von [Eingabeformatierern](#input-formatters) verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="eb660-494">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="eb660-495">Typkonvertierungsfehler</span><span class="sxs-lookup"><span data-stu-id="eb660-495">Type conversion errors</span></span>

<span data-ttu-id="eb660-496">Wenn eine Quelle gefunden wird, aber nicht in den Zieltyp konvertiert werden kann, wird der Modellzustand als „ungültig“ gekennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="eb660-496">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="eb660-497">Der Zielparameter oder die Zieleigenschaft wird auf „null“ oder einen Standardwert festgelegt, wie bereits im vorherigen Abschnitt erwähnt.</span><span class="sxs-lookup"><span data-stu-id="eb660-497">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="eb660-498">In einem API-Controller, der über das `[ApiController]`-Attribut verfügt, führt ein ungültiger Modellzustand zu einer automatischen „HTTP 400“-Antwort.</span><span class="sxs-lookup"><span data-stu-id="eb660-498">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="eb660-499">Zeigen Sie auf einer Razor Seite die Seite erneut mit einer Fehlermeldung an:</span><span class="sxs-lookup"><span data-stu-id="eb660-499">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="eb660-500">Bei der Client seitigen Validierung werden die meisten ungültigen Daten abgefangen, die andernfalls an ein Seiten Formular übermittelt werden Razor .</span><span class="sxs-lookup"><span data-stu-id="eb660-500">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="eb660-501">Diese Validierung erschwert es, den voranstehenden, hervorgehobenen Code auszulösen.</span><span class="sxs-lookup"><span data-stu-id="eb660-501">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="eb660-502">Die Beispiel-App umfasst eine Schaltfläche **Submit with Invalid Date** (Mit ungültigem Datum absenden), die ungültige Daten in das Feld **Hire Date** (Einstellungsdatum) einfügt und das Formular absendet.</span><span class="sxs-lookup"><span data-stu-id="eb660-502">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="eb660-503">Diese Schaltfläche zeigt, wie der Code zum erneuten Anzeigen der Seite funktioniert, wenn Datenkonvertierungsfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="eb660-503">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="eb660-504">Wenn die Seite von dem vorangehenden Code erneut angezeigt wird, wird die ungültige Eingabe nicht im Formularfeld angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-504">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="eb660-505">Dies liegt daran, dass die Modelleigenschaft auf „null“ oder einen Standardwert festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="eb660-505">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="eb660-506">Die ungültige Eingabe wird jedoch in einer Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eb660-506">The invalid input does appear in an error message.</span></span> <span data-ttu-id="eb660-507">Wenn Sie aber die ungültigen Daten im Formularfeld erneut anzeigen möchten, sollten Sie aus der Modelleigenschaft eine Zeichenfolge machen und die Datenkonvertierung manuell ausführen.</span><span class="sxs-lookup"><span data-stu-id="eb660-507">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="eb660-508">Dieselbe Strategie empfiehlt sich, wenn Sie nicht möchten, dass Typkonvertierungsfehler zu Modellzustandsfehlern führen.</span><span class="sxs-lookup"><span data-stu-id="eb660-508">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="eb660-509">In diesem Fall machen Sie aus der Modelleigenschaft eine Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="eb660-509">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="eb660-510">Einfache Typen</span><span class="sxs-lookup"><span data-stu-id="eb660-510">Simple types</span></span>

<span data-ttu-id="eb660-511">Die einfachen Typen, in die die Modellbindung Quellzeichenfolgen konvertieren kann, sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="eb660-511">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="eb660-512">Boolean</span><span class="sxs-lookup"><span data-stu-id="eb660-512">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="eb660-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="eb660-513">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="eb660-514">Char</span><span class="sxs-lookup"><span data-stu-id="eb660-514">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="eb660-515">DateTime</span><span class="sxs-lookup"><span data-stu-id="eb660-515">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="eb660-516">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="eb660-516">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="eb660-517">Decimal</span><span class="sxs-lookup"><span data-stu-id="eb660-517">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="eb660-518">Double</span><span class="sxs-lookup"><span data-stu-id="eb660-518">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="eb660-519">Enum</span><span class="sxs-lookup"><span data-stu-id="eb660-519">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="eb660-520">GUID</span><span class="sxs-lookup"><span data-stu-id="eb660-520">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="eb660-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb660-521">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="eb660-522">Single</span><span class="sxs-lookup"><span data-stu-id="eb660-522">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="eb660-523">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="eb660-523">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="eb660-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="eb660-524">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="eb660-525">URI</span><span class="sxs-lookup"><span data-stu-id="eb660-525">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="eb660-526">Version</span><span class="sxs-lookup"><span data-stu-id="eb660-526">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="eb660-527">Komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="eb660-527">Complex types</span></span>

<span data-ttu-id="eb660-528">Ein komplexer Typ muss einen öffentlichen Standardkonstruktor und öffentliche schreibbare Eigenschaften besitzen, die gebunden werden können.</span><span class="sxs-lookup"><span data-stu-id="eb660-528">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="eb660-529">Wenn die Modellbindung erfolgt, wird die Klasse mit dem öffentlichen Standardkonstruktor instanziiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-529">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="eb660-530">Für jede Eigenschaft des komplexen Typs durchsucht die Modellbindung die Quellen für das Namensmuster *prefix.property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-530">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="eb660-531">Wenn nichts gefunden wird, sucht sie nur nach *property_name* ohne das Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-531">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="eb660-532">Beim Binden an einen Parameter ist das Präfix der Name des Parameters.</span><span class="sxs-lookup"><span data-stu-id="eb660-532">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="eb660-533">Beim Binden an eine öffentliche Eigenschaft `PageModel` ist das Präfix der Name der öffentlichen Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="eb660-533">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="eb660-534">Einige Attribute besitzen eine Eigenschaft `Prefix`, die es Ihnen gestattet, die Standardverwendung des Parameter- oder Eigenschaftennamens außer Kraft zu setzen.</span><span class="sxs-lookup"><span data-stu-id="eb660-534">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="eb660-535">Nehmen Sie beispielsweise an, der komplexe Typ ist die folgende `Instructor`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="eb660-535">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="eb660-536">Präfix = Parametername</span><span class="sxs-lookup"><span data-stu-id="eb660-536">Prefix = parameter name</span></span>

<span data-ttu-id="eb660-537">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist:</span><span class="sxs-lookup"><span data-stu-id="eb660-537">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="eb660-538">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `instructorToUpdate.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-538">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="eb660-539">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-539">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="eb660-540">Präfix = Name der Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="eb660-540">Prefix = property name</span></span>

<span data-ttu-id="eb660-541">Wenn das zu bindende Modell eine Eigenschaft des Controllers oder der `PageModel`-Klasse namens `Instructor` ist:</span><span class="sxs-lookup"><span data-stu-id="eb660-541">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="eb660-542">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-542">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb660-543">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-543">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="eb660-544">Benutzerdefiniertes Präfix</span><span class="sxs-lookup"><span data-stu-id="eb660-544">Custom prefix</span></span>

<span data-ttu-id="eb660-545">Wenn das zu bindende Modell ein Parameter namens `instructorToUpdate` ist, und ein `Bind`-Attribut `Instructor` als Präfix angibt:</span><span class="sxs-lookup"><span data-stu-id="eb660-545">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="eb660-546">Die Modellbindung beginnt, indem sie die Quellen nach dem Schlüssel `Instructor.ID` durchsucht.</span><span class="sxs-lookup"><span data-stu-id="eb660-546">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="eb660-547">Wenn dieser nicht gefunden wird, sucht sie nach `ID` ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-547">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="eb660-548">Attribute für Ziele komplexen Typs</span><span class="sxs-lookup"><span data-stu-id="eb660-548">Attributes for complex type targets</span></span>

<span data-ttu-id="eb660-549">Mehrere integrierte Attribute stehen für die Kontrolle der Modellbindung komplexer Typen zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="eb660-549">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="eb660-550">Diese Attribute wirken sich auf die Modellbindung aus, wenn bereitgestellte Formulardaten die Quelle der Wert sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-550">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="eb660-551">Sie haben keine Auswirkungen auf Eingabeformatierer, die bereitgestellte JSON- und XML-Anforderungstexte verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="eb660-551">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="eb660-552">Eingabeformatierer werden [später in diesem Artikel](#input-formatters) erklärt.</span><span class="sxs-lookup"><span data-stu-id="eb660-552">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="eb660-553">Lesen Sie auch die Diskussion des `[Required]`-Attributs in der [Modellvalidierung](xref:mvc/models/validation#required-attribute).</span><span class="sxs-lookup"><span data-stu-id="eb660-553">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="eb660-554">[BindRequired]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-554">[BindRequired] attribute</span></span>

<span data-ttu-id="eb660-555">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-555">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb660-556">Bewirkt, dass die Modellbindung einen Modellzustandsfehler hinzufügt, wenn die Bindung für die Eigenschaft eines Modells nicht erfolgen kann.</span><span class="sxs-lookup"><span data-stu-id="eb660-556">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="eb660-557">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-557">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="eb660-558">[BindNever]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-558">[BindNever] attribute</span></span>

<span data-ttu-id="eb660-559">Kann nur auf Modelleigenschaften angewendet werden, nicht auf Methodenparameter.</span><span class="sxs-lookup"><span data-stu-id="eb660-559">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="eb660-560">Verhindert, dass die Modellbindung die Eigenschaft eines Modells festlegt.</span><span class="sxs-lookup"><span data-stu-id="eb660-560">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="eb660-561">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-561">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="eb660-562">[Bind]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-562">[Bind] attribute</span></span>

<span data-ttu-id="eb660-563">Kann auf eine Klasse oder einen Methodenparameter angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-563">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="eb660-564">Gibt an, welche Eigenschaften eines Modells in die Modellbindung aufgenommen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="eb660-564">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="eb660-565">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn ein Ereignishandler oder eine Aktionsmethode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-565">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="eb660-566">Im folgenden Beispiel werden nur die angegebenen Eigenschaften des `Instructor`-Modells gebunden, wenn die `OnPost`-Methode aufgerufen wird:</span><span class="sxs-lookup"><span data-stu-id="eb660-566">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="eb660-567">Das `[Bind]`-Attribut kann zum Schutz vor Overposting in *Erstellungs* szenarien (create) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-567">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="eb660-568">Es funktioniert nicht gut in Bearbeitungsszenarien (edit), weil ausgeschlossene Eigenschaften auf „null“ oder einen Standardwert festgelegt werden, anstatt unverändert zu bleiben.</span><span class="sxs-lookup"><span data-stu-id="eb660-568">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="eb660-569">Zum Schutz vor Overposting werden Ansichtsmodelle empfohlen, anstelle des `[Bind]`-Attributs.</span><span class="sxs-lookup"><span data-stu-id="eb660-569">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="eb660-570">Weitere Informationen finden Sie unter [Sicherheitshinweis zum Overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="eb660-570">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="eb660-571">Auflistungen</span><span class="sxs-lookup"><span data-stu-id="eb660-571">Collections</span></span>

<span data-ttu-id="eb660-572">Bei Zielen, die Sammlungen einfacher Typen sind, sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-572">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb660-573">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-573">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb660-574">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-574">For example:</span></span>

* <span data-ttu-id="eb660-575">Angenommen, der zu bindende Parameter ist ein Array namens `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="eb660-575">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="eb660-576">Formular- oder Abfragezeichenfolgendaten können eins der folgenden Formate haben:</span><span class="sxs-lookup"><span data-stu-id="eb660-576">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="eb660-577">Das folgende Format wird nur für Formulardaten unterstützt:</span><span class="sxs-lookup"><span data-stu-id="eb660-577">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="eb660-578">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Array von zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="eb660-578">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb660-579">selectedCourses[0]=1050</span><span class="sxs-lookup"><span data-stu-id="eb660-579">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="eb660-580">selectedCourses[1]=2000</span><span class="sxs-lookup"><span data-stu-id="eb660-580">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="eb660-581">Datenformate, die Indexnummern verwenden(... [0]... [1] ...), müssen sicherstellen, dass sie fortlaufend nummeriert sind, beginnend mit 0 (null).</span><span class="sxs-lookup"><span data-stu-id="eb660-581">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="eb660-582">Treten bei der Indexnummerierung Lücken auf, werden alle Elemente, die auf die Lücke folgen, ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-582">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="eb660-583">Wenn die Indizes beispielsweise 0 und 2 anstelle von 0 und 1 sind, wird beispielsweise das zweite Element ignoriert.</span><span class="sxs-lookup"><span data-stu-id="eb660-583">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="eb660-584">Wörterbücher</span><span class="sxs-lookup"><span data-stu-id="eb660-584">Dictionaries</span></span>

<span data-ttu-id="eb660-585">Bei `Dictionary`-Zielen sucht die Modellbindung nach Übereinstimmungen mit *parameter_name* oder *property_name*.</span><span class="sxs-lookup"><span data-stu-id="eb660-585">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="eb660-586">Wird keine Übereinstimmung gefunden, sucht sie nach einem der unterstützten Formate ohne Präfix.</span><span class="sxs-lookup"><span data-stu-id="eb660-586">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="eb660-587">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-587">For example:</span></span>

* <span data-ttu-id="eb660-588">Angenommen, der Zielparameter ist eine `Dictionary<int, string>` mit dem Namen `selectedCourses`:</span><span class="sxs-lookup"><span data-stu-id="eb660-588">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="eb660-589">Die bereitgestellten Formular- oder Abfragezeichenfolgendaten können wie eins der folgenden Beispiele aussehen:</span><span class="sxs-lookup"><span data-stu-id="eb660-589">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="eb660-590">Bei allen der vorangehenden Beispielformate übergibt die Modellbindung ein Wörterbuch aus zwei Elementen an den `selectedCourses`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="eb660-590">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="eb660-591">selectedCourses["1050"]="Chemistry"</span><span class="sxs-lookup"><span data-stu-id="eb660-591">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="eb660-592">selectedCourses["2000"]="Economics"</span><span class="sxs-lookup"><span data-stu-id="eb660-592">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="eb660-593">Globalisierungsverhalten der Routendaten und Abfragezeichenfolgen für die Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-593">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="eb660-594">Der ASP.NET Core-Routenwertanbieter und der Abfragezeichenfolgenwert-Anbieter:</span><span class="sxs-lookup"><span data-stu-id="eb660-594">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="eb660-595">behandeln Werte als invariante Kulturen.</span><span class="sxs-lookup"><span data-stu-id="eb660-595">Treat values as invariant culture.</span></span>
* <span data-ttu-id="eb660-596">erwarten, dass URLs kulturinvariant sind.</span><span class="sxs-lookup"><span data-stu-id="eb660-596">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="eb660-597">Im Gegensatz dazu durchlaufen Werte, die aus Formulardaten stammen, eine kulturabhängige Konvertierung.</span><span class="sxs-lookup"><span data-stu-id="eb660-597">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="eb660-598">Dies ist beabsichtigt, damit URLs zwischen Gebietsschemas freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="eb660-598">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="eb660-599">So lassen Sie den ASP.NET Core-Routenwertanbieter und den Abfragezeichenfolgenwert-Anbieter eine kulturabhängige Konvertierung durchlaufen:</span><span class="sxs-lookup"><span data-stu-id="eb660-599">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="eb660-600">Sie erben von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory>.</span><span class="sxs-lookup"><span data-stu-id="eb660-600">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="eb660-601">Kopieren Sie den Code aus [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) oder [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs).</span><span class="sxs-lookup"><span data-stu-id="eb660-601">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="eb660-602">Ersetzen Sie den an den Wertanbieterkonstruktor übergebenen [Culture-Wert](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) durch [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture).</span><span class="sxs-lookup"><span data-stu-id="eb660-602">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="eb660-603">Ersetzen Sie die Standardwertanbieter-Zuordnungsinstanz in den MVC-Optionen durch Ihre neue:</span><span class="sxs-lookup"><span data-stu-id="eb660-603">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="eb660-604">Spezielle Datentypen</span><span class="sxs-lookup"><span data-stu-id="eb660-604">Special data types</span></span>

<span data-ttu-id="eb660-605">Es gibt einige spezielle Datentypen, die die Modellbindung verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="eb660-605">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="eb660-606">„IFormFile“ und „IFormFileCollection“</span><span class="sxs-lookup"><span data-stu-id="eb660-606">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="eb660-607">Eine in der HTTP-Anforderung enthaltene, hochgeladenen Datei.</span><span class="sxs-lookup"><span data-stu-id="eb660-607">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="eb660-608">Außerdem wird `IEnumerable<IFormFile>` für mehrere Dateien unterstützt.</span><span class="sxs-lookup"><span data-stu-id="eb660-608">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="eb660-609">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="eb660-609">CancellationToken</span></span>

<span data-ttu-id="eb660-610">Wird verwendet, um die Aktivität in asynchronen Controllern zu beenden.</span><span class="sxs-lookup"><span data-stu-id="eb660-610">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="eb660-611">„FormCollection“</span><span class="sxs-lookup"><span data-stu-id="eb660-611">FormCollection</span></span>

<span data-ttu-id="eb660-612">Wird verwendet, um alle Werte aus bereitgestellten Formulardaten abzurufen.</span><span class="sxs-lookup"><span data-stu-id="eb660-612">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="eb660-613">Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="eb660-613">Input formatters</span></span>

<span data-ttu-id="eb660-614">Daten im Anforderungstext können im JSON-, XML- oder einem anderen Format sein.</span><span class="sxs-lookup"><span data-stu-id="eb660-614">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="eb660-615">Um diese Daten zu analysieren, verwendet die Modellbindung einen *Eingabeformatierer*, der für die Verarbeitung eines bestimmten Inhaltstyps konfiguriert ist.</span><span class="sxs-lookup"><span data-stu-id="eb660-615">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="eb660-616">ASP.NET Core enthält standardmäßig JSON-basierte Eingabeformatierer für die Verarbeitung von JSON-Daten.</span><span class="sxs-lookup"><span data-stu-id="eb660-616">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="eb660-617">Sie können andere Formatierer für andere Inhaltstypen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-617">You can add other formatters for other content types.</span></span>

<span data-ttu-id="eb660-618">ASP.NET Core wählt Eingabeformatierer auf Grundlage des [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute)-Attributs aus.</span><span class="sxs-lookup"><span data-stu-id="eb660-618">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="eb660-619">Wenn kein Attribut vorhanden ist, verwendet es den [Content-Type-Header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span><span class="sxs-lookup"><span data-stu-id="eb660-619">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="eb660-620">So verwenden Sie die integrierte XML-Eingabeformatierer</span><span class="sxs-lookup"><span data-stu-id="eb660-620">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="eb660-621">Installieren Sie das NuGet-Paket `Microsoft.AspNetCore.Mvc.Formatters.Xml`.</span><span class="sxs-lookup"><span data-stu-id="eb660-621">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="eb660-622">In `Startup.ConfigureServices`, rufen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> oder <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> auf.</span><span class="sxs-lookup"><span data-stu-id="eb660-622">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="eb660-623">Wenden Sie das `Consumes`-Attribut auf Controllerklassen oder Aktionsmethoden an, die XML im Anforderungstext erwarten sollten.</span><span class="sxs-lookup"><span data-stu-id="eb660-623">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="eb660-624">Weitere Informationen finden Sie unter [Einführung der XML-Serialisierung](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="eb660-624">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="eb660-625">Ausschließen angegebener Typen aus der Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-625">Exclude specified types from model binding</span></span>

<span data-ttu-id="eb660-626">Das Verhalten der Modellbindung und des Validierungssystems wird von der Klasse [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata) gesteuert.</span><span class="sxs-lookup"><span data-stu-id="eb660-626">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="eb660-627">Sie können `ModelMetadata` anpassen, indem Sie [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders) einen Detailanbieter hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eb660-627">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="eb660-628">Integrierte Detailanbieter sind verfügbar, um die Modellbindung oder Validierung für angegebene Typen zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="eb660-628">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="eb660-629">Um die Modellbindung für alle Modelle eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb660-629">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb660-630">Beispielsweise können Sie die Modellbindung für alle Modelle vom Typ `System.Version` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="eb660-630">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="eb660-631">Um die Validierung für Eigenschaften eines angegebenen Typs zu deaktivieren, fügen Sie in `Startup.ConfigureServices` einen <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> hinzu.</span><span class="sxs-lookup"><span data-stu-id="eb660-631">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="eb660-632">Beispielsweise können Sie die Überprüfung von Eigenschaften vom Typ `System.Guid` wie folgt deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="eb660-632">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="eb660-633">Benutzerdefinierte Modellbindungen</span><span class="sxs-lookup"><span data-stu-id="eb660-633">Custom model binders</span></span>

<span data-ttu-id="eb660-634">Sie können die Modellbindung erweitern, indem Sie eine benutzerdefinierte Modellbindung schreiben und das `[ModelBinder]`-Attribut verwenden, um diese für ein bestimmtes Ziel auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="eb660-634">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="eb660-635">Erfahren Sie mehr über die [benutzerdefinierte Modellbindung](xref:mvc/advanced/custom-model-binding).</span><span class="sxs-lookup"><span data-stu-id="eb660-635">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="eb660-636">Manuelle Modellbindung</span><span class="sxs-lookup"><span data-stu-id="eb660-636">Manual model binding</span></span>

<span data-ttu-id="eb660-637">Die Modellbindung kann mithilfe der <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>-Methode manuell aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="eb660-637">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="eb660-638">Die Methode ist für die beiden Klassen `ControllerBase` und `PageModel` definiert.</span><span class="sxs-lookup"><span data-stu-id="eb660-638">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="eb660-639">Mithilfe von Methodenüberladungen können Sie das Präfix und den Wertanbieter festlegen, die verwendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="eb660-639">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="eb660-640">Die Methode gibt `false` zurück, wenn die Modellbindung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="eb660-640">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="eb660-641">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eb660-641">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="eb660-642">[FromServices]-Attribut</span><span class="sxs-lookup"><span data-stu-id="eb660-642">[FromServices] attribute</span></span>

<span data-ttu-id="eb660-643">Der Name dieses Attributs folgt dem Muster von Modellbindungsattributen, die eine Datenquelle angeben.</span><span class="sxs-lookup"><span data-stu-id="eb660-643">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="eb660-644">Es ist aber nicht zum Binden von Daten aus einem Wertanbieter gedacht.</span><span class="sxs-lookup"><span data-stu-id="eb660-644">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="eb660-645">Es ruft eine Instanz eines Typs aus dem [Dependency Injection](xref:fundamentals/dependency-injection)-Container (Abhängigkeitsinjektion) ab.</span><span class="sxs-lookup"><span data-stu-id="eb660-645">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="eb660-646">Sein Zweck besteht darin, eine Alternative zur „Constructor Injection“ (Konstruktorinjektion) bereitzustellen, wenn Sie einen Dienst nur dann benötigen, wenn eine bestimmte Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="eb660-646">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eb660-647">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eb660-647">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
