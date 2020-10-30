---
title: Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API
author: scottaddie
description: In diesem Artikel erfahren Sie, wie die verschiedenen Rückgabetypen für Controlleraktionsmethoden in einer ASP.NET Core-Web-API verwendet werden.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: a2866970a20785ae8fa306d484972697817b7f92
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058947"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="c8353-103">Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="c8353-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="c8353-104">Von [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="c8353-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="c8353-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c8353-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c8353-106">In ASP.NET Core haben Sie die folgenden Optionen für Rückgabetypen für Web-API-Controlleraktionen:</span><span class="sxs-lookup"><span data-stu-id="c8353-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="c8353-107">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="c8353-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="c8353-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="c8353-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="c8353-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="c8353-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="c8353-110">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="c8353-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="c8353-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="c8353-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="c8353-112">In diesem Artikel wird die Verwendung der einzelnen Rückgabetypen erklärt.</span><span class="sxs-lookup"><span data-stu-id="c8353-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="c8353-113">Spezifischer Typ</span><span class="sxs-lookup"><span data-stu-id="c8353-113">Specific type</span></span>

<span data-ttu-id="c8353-114">Die einfachste Aktion gibt einen primitiven oder komplexen Datentyp zurück (z.B. `string` oder einen benutzerdefinierten Objekttyp).</span><span class="sxs-lookup"><span data-stu-id="c8353-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="c8353-115">Die folgende Aktion gibt eine Auflistung benutzerdefinierter `Product`-Objekte zurück:</span><span class="sxs-lookup"><span data-stu-id="c8353-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="c8353-116">Ohne bekannte Bedingungen, gegen die während der Ausführung einer Aktion Schutzmaßnahmen ergriffen werden müssen, reicht die Rückgabe eines spezifischen Typs möglicherweise aus.</span><span class="sxs-lookup"><span data-stu-id="c8353-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="c8353-117">Die vorherige Aktion akzeptiert keine Parameter, weshalb eine Validierung von Parametereinschränkungen nicht erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="c8353-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="c8353-118">Wenn mehrere Rückgabe Typen möglich sind, wird häufig ein <xref:Microsoft.AspNetCore.Mvc.ActionResult> Rückgabetyp mit dem primitiven oder komplexen Rückgabetyp gemischt.</span><span class="sxs-lookup"><span data-stu-id="c8353-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="c8353-119">Für diese Art von Aktion sind entweder ' [IAction result](#iactionresult-type) ' oder ' [ \<T> Action result](#actionresultt-type) ' erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c8353-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="c8353-120">In diesem Dokument werden mehrere Beispiele mehrerer Rückgabe Typen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8353-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="c8353-121">IEnumerable \<T> oder iasyncenumerable zurückgeben\<T></span><span class="sxs-lookup"><span data-stu-id="c8353-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="c8353-122">Wenn eine Aktion in ASP.NET Core 2.2 und früher <xref:System.Collections.Generic.IEnumerable%601> zurückgibt, führt dies zu einer synchronen Sammlungsiteration durch das Serialisierungsprogramm.</span><span class="sxs-lookup"><span data-stu-id="c8353-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="c8353-123">Das Ergebnis sind die Blockierung von Aufrufen und die potenzielle Außerkraftsetzung des Threadpools.</span><span class="sxs-lookup"><span data-stu-id="c8353-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="c8353-124">Stellen Sie sich zur Veranschaulichung vor, dass der Entity Framework-Core (EF) für die Datenzugriffsanforderungen der Web-API verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="c8353-125">Der Rückgabetyp der folgenden Aktion wird während der Serialisierung synchron aufgezählt:</span><span class="sxs-lookup"><span data-stu-id="c8353-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="c8353-126">Um synchrone Enumeration und blockierendes Warten auf die Datenbank in ASP.NET Core 2.2 und früher zu vermeiden, rufen Sie `ToListAsync` auf:</span><span class="sxs-lookup"><span data-stu-id="c8353-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="c8353-127">In ASP.NET Core 3.0 und höher gilt Folgendes, wenn eine Aktion `IAsyncEnumerable<T>` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="c8353-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="c8353-128">Es kommt nicht mehr zu synchroner Iteration.</span><span class="sxs-lookup"><span data-stu-id="c8353-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="c8353-129">Gleiche Effizienz wie bei Rückgabe von <xref:System.Collections.Generic.IEnumerable%601>.</span><span class="sxs-lookup"><span data-stu-id="c8353-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="c8353-130">ASP.NET Core 3.0 und höher puffert das Ergebnis der folgenden Aktion, bevor es dem Serialisierungsprogramm bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="c8353-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="c8353-131">Sie sollten den Rückgabetyp der Signatur der Aktion als `IAsyncEnumerable<T>` deklarieren, um die asynchrone Iteration sicherzustellen.</span><span class="sxs-lookup"><span data-stu-id="c8353-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="c8353-132">Letztendlich basiert der Iterationsmodus auf dem zugrunde liegenden konkreten Typ, der zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="c8353-133">MVC puffert automatisch jeden konkreten Typ, der `IAsyncEnumerable<T>` implementiert.</span><span class="sxs-lookup"><span data-stu-id="c8353-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="c8353-134">Sehen Sie sich die folgende Aktion an, die den Verkaufspreis enthaltende Produktdatensätze als `IEnumerable<Product>` zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="c8353-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="c8353-135">Die `IAsyncEnumerable<Product>`-Entsprechung der vorherigen Aktion lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="c8353-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="c8353-136">Beide vorangehenden Aktionen führen ab ASP.NET Core 3.0 nicht mehr zu Blockierungen.</span><span class="sxs-lookup"><span data-stu-id="c8353-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="c8353-137">IActionResult-Typ</span><span class="sxs-lookup"><span data-stu-id="c8353-137">IActionResult type</span></span>

<span data-ttu-id="c8353-138">Der Rückgabetyp <xref:Microsoft.AspNetCore.Mvc.IActionResult> eignet sich in Fällen, in denen mehrere `ActionResult`-Rückgabetypen in einer Aktion möglich sind.</span><span class="sxs-lookup"><span data-stu-id="c8353-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="c8353-139">Die `ActionResult`-Typen stellen verschiedene HTTP-Statuscodes dar.</span><span class="sxs-lookup"><span data-stu-id="c8353-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="c8353-140">Alle nicht abstrakten Klassen, die von `ActionResult` abgeleitet werden, qualifizieren sich als gültiger Rückgabetyp.</span><span class="sxs-lookup"><span data-stu-id="c8353-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="c8353-141">Einige allgemeine Rückgabetypen in dieser Kategorie sind <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) und <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="c8353-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="c8353-142">Alternativ können Hilfsmethoden in der <xref:Microsoft.AspNetCore.Mvc.ControllerBase>-Klasse verwendet werden, um `ActionResult`-Typen aus einer Aktion zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="c8353-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="c8353-143">`return BadRequest();` ist beispielsweise eine Kurzform von `return new BadRequestResult();`.</span><span class="sxs-lookup"><span data-stu-id="c8353-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="c8353-144">Da in dieser Art von Aktion mehrere Rückgabe Typen und Pfade vorhanden sind, ist die liberale Verwendung des- [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) Attributs erforderlich.</span><span class="sxs-lookup"><span data-stu-id="c8353-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="c8353-145">Dieses Attribut erzeugt aussagekräftigere Antwortdetails für Web-API-Hilfeseiten, die mit Tools wie [Swagger](xref:tutorials/web-api-help-pages-using-swagger) erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="c8353-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="c8353-146">`[ProducesResponseType]` gibt die bekannten Typen und HTTP-Statuscodes an, die von der Aktion zurückgegeben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c8353-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="c8353-147">Synchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="c8353-147">Synchronous action</span></span>

<span data-ttu-id="c8353-148">Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="c8353-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="c8353-149">In der vorhergehenden Aktion geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c8353-149">In the preceding action:</span></span>

* <span data-ttu-id="c8353-150">Ein 404-Statuscode wird zurückgegeben, wenn das durch `id` dargestellte Produkt im zugrunde liegenden Datenspeicher nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8353-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="c8353-151">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>-Hilfsmethode wird als Kurzform für `return new NotFoundResult();` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c8353-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="c8353-152">Ein 200-Statuscode wird mit dem `Product`-Objekt zurückgegeben, wenn das Produkt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8353-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="c8353-153">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>-Hilfsmethode wird als Kurzform für `return new OkObjectResult(product);` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c8353-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="c8353-154">Asynchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="c8353-154">Asynchronous action</span></span>

<span data-ttu-id="c8353-155">Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="c8353-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="c8353-156">In der vorhergehenden Aktion geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c8353-156">In the preceding action:</span></span>

* <span data-ttu-id="c8353-157">Ein 400-Statuscode wird zurückgegeben, wenn die Produktbeschreibung „XYZ-Widget“ enthält.</span><span class="sxs-lookup"><span data-stu-id="c8353-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="c8353-158">Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>-Hilfsmethode wird als Kurzform für `return new BadRequestResult();` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="c8353-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="c8353-159">Ein 201-Statuscode wird von der Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> generiert, wenn ein Produkt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="c8353-160">Eine Alternative zum Aufrufen von `CreatedAtAction` ist `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span><span class="sxs-lookup"><span data-stu-id="c8353-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="c8353-161">In diesem Codepfad wird das `Product`-Objekt im Antworttext bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8353-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="c8353-162">Ein `Location`-Antwortheader, der die neu erstellte Produkt-URL enthält, wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8353-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="c8353-163">Das folgende Modell gibt beispielsweise an, dass Anforderungen die `Name`- und `Description`-Eigenschaften einbinden müssen.</span><span class="sxs-lookup"><span data-stu-id="c8353-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="c8353-164">Wenn `Name` und `Description` in der Anforderung nicht bereitgestellt werden, tritt bei der Modellvalidierung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c8353-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="c8353-165">Wenn das- [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut in ASP.net Core 2,1 oder höher angewendet wird, führen Modell Validierungs Fehler zu einem 400-Statuscode.</span><span class="sxs-lookup"><span data-stu-id="c8353-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="c8353-166">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="c8353-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="c8353-167">Aktions \<T> Ergebnistyp</span><span class="sxs-lookup"><span data-stu-id="c8353-167">ActionResult\<T> type</span></span>

<span data-ttu-id="c8353-168">In ASP.net Core 2,1 wurde der Rückgabetyp " [aktionresult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) " für Web-API-Controller Aktionen</span><span class="sxs-lookup"><span data-stu-id="c8353-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="c8353-169">Damit wird die Rückgabe eines von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleiteten Typs oder eines [spezifischen Typs](#specific-type) ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="c8353-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="c8353-170">`ActionResult<T>` besitzt gegenüber dem [IActionResult-Typ](#iactionresult-type) die folgenden Vorteile:</span><span class="sxs-lookup"><span data-stu-id="c8353-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="c8353-171">Die [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) -Eigenschaft des Attributs `Type` kann ausgeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="c8353-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="c8353-172">`[ProducesResponseType(200, Type = typeof(Product))]` wird beispielsweise zu `[ProducesResponseType(200)]` vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="c8353-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="c8353-173">Der erwartete Rückgabetyp der Aktion wird stattdessen von `T` in `ActionResult<T>` abgeleitet.</span><span class="sxs-lookup"><span data-stu-id="c8353-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="c8353-174">[Implizite Umwandlungsoperatoren](/dotnet/csharp/language-reference/keywords/implicit) unterstützen die Konvertierung von `T` und `ActionResult` in `ActionResult<T>`.</span><span class="sxs-lookup"><span data-stu-id="c8353-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="c8353-175">`T` wird in <xref:Microsoft.AspNetCore.Mvc.ObjectResult> konvertiert, wodurch `return new ObjectResult(T);` zu `return T;` vereinfacht wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="c8353-176">C# unterstützt keine impliziten Umwandlungsoperatoren in Schnittstellen.</span><span class="sxs-lookup"><span data-stu-id="c8353-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="c8353-177">Daher ist die Konvertierung der Schnittstelle in einen konkreten Typ erforderlich, um `ActionResult<T>` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="c8353-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="c8353-178">Die Verwendung von `IEnumerable` im folgenden Beispiel funktioniert also nicht:</span><span class="sxs-lookup"><span data-stu-id="c8353-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="c8353-179">Eine Möglichkeit zur Korrektur des oben stehenden Codes besteht darin, `_repository.GetProducts().ToList();` zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="c8353-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="c8353-180">Die meisten Aktionen haben einen bestimmten Rückgabetyp.</span><span class="sxs-lookup"><span data-stu-id="c8353-180">Most actions have a specific return type.</span></span> <span data-ttu-id="c8353-181">Während der Ausführung der Aktion können unerwartete Bedingungen auftreten, wodurch der spezifische Typ nicht zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="c8353-182">So kann beispielsweise die Modellvalidierung des Eingabeparameters einer Aktion fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="c8353-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="c8353-183">In diesem Fall wird üblicherweise der entsprechende `ActionResult`-Typ anstatt des spezifischen Typs zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c8353-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="c8353-184">Synchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="c8353-184">Synchronous action</span></span>

<span data-ttu-id="c8353-185">Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="c8353-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="c8353-186">In der vorhergehenden Aktion geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c8353-186">In the preceding action:</span></span>

* <span data-ttu-id="c8353-187">Ein 404-Statuscode wird zurückgegeben, wenn das Produkt in der Datenbank nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8353-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="c8353-188">Ein 200-Statuscode wird mit dem entsprechenden `Product`-Objekt zurückgegeben, wenn das Produkt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c8353-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="c8353-189">Vor ASP.NET Core 2.1 hätte die Zeile `return product;` stattdessen `return Ok(product);` lauten müssen.</span><span class="sxs-lookup"><span data-stu-id="c8353-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="c8353-190">Asynchrone Aktion</span><span class="sxs-lookup"><span data-stu-id="c8353-190">Asynchronous action</span></span>

<span data-ttu-id="c8353-191">Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:</span><span class="sxs-lookup"><span data-stu-id="c8353-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="c8353-192">In der vorhergehenden Aktion geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c8353-192">In the preceding action:</span></span>

* <span data-ttu-id="c8353-193">Ein 400-Statuscode (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) wird durch die ASP.NET Core-Runtime zurückgegeben, wenn:</span><span class="sxs-lookup"><span data-stu-id="c8353-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="c8353-194">Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) -Attribut wurde angewendet, und die Modell Validierung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="c8353-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="c8353-195">Die Produktbeschreibung „XYZ-Widget“ enthält.</span><span class="sxs-lookup"><span data-stu-id="c8353-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="c8353-196">Ein 201-Statuscode wird von der Methode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> generiert, wenn ein Produkt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="c8353-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="c8353-197">In diesem Codepfad wird das `Product`-Objekt im Antworttext bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8353-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="c8353-198">Ein `Location`-Antwortheader, der die neu erstellte Produkt-URL enthält, wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c8353-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c8353-199">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c8353-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
