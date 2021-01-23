---
title: Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API
author: scottaddie
description: In diesem Artikel erfahren Sie, wie die verschiedenen Rückgabetypen für Controlleraktionsmethoden in einer ASP.NET Core-Web-API verwendet werden.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: 62c8227ca770a3a9adbe780685b140bc0e86841e
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710697"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>Rückgabetypen für Controlleraktionen in der ASP.NET Core-Web-API

Von [Scott Addie](https://github.com/scottaddie)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

In ASP.NET Core haben Sie die folgenden Optionen für Rückgabetypen für Web-API-Controlleraktionen:

::: moniker range=">= aspnetcore-2.1"

* [Spezifischer Typ](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Spezifischer Typ](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

In diesem Artikel wird die Verwendung der einzelnen Rückgabetypen erklärt.

## <a name="specific-type"></a>Spezifischer Typ

Die einfachste Aktion gibt einen primitiven oder komplexen Datentyp zurück (z.B. `string` oder einen benutzerdefinierten Objekttyp). Die folgende Aktion gibt eine Auflistung benutzerdefinierter `Product`-Objekte zurück:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Ohne bekannte Bedingungen, gegen die während der Ausführung einer Aktion Schutzmaßnahmen ergriffen werden müssen, reicht die Rückgabe eines spezifischen Typs möglicherweise aus. Die vorherige Aktion akzeptiert keine Parameter, weshalb eine Validierung von Parametereinschränkungen nicht erforderlich ist.

Wenn mehrere Rückgabe Typen möglich sind, wird häufig ein <xref:Microsoft.AspNetCore.Mvc.ActionResult> Rückgabetyp mit dem primitiven oder komplexen Rückgabetyp gemischt. Für diese Art von Aktion sind entweder ' [IAction result](#iactionresult-type) ' oder ' [ \<T> Action result](#actionresultt-type) ' erforderlich. In diesem Dokument werden mehrere Beispiele mehrerer Rückgabe Typen bereitgestellt.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>IEnumerable \<T> oder iasyncenumerable zurückgeben\<T>

Wenn eine Aktion in ASP.NET Core 2.2 und früher <xref:System.Collections.Generic.IEnumerable%601> zurückgibt, führt dies zu einer synchronen Sammlungsiteration durch das Serialisierungsprogramm. Das Ergebnis sind die Blockierung von Aufrufen und die potenzielle Außerkraftsetzung des Threadpools. Stellen Sie sich zur Veranschaulichung vor, dass der Entity Framework-Core (EF) für die Datenzugriffsanforderungen der Web-API verwendet wird. Der Rückgabetyp der folgenden Aktion wird während der Serialisierung synchron aufgezählt:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Um synchrone Enumeration und blockierendes Warten auf die Datenbank in ASP.NET Core 2.2 und früher zu vermeiden, rufen Sie `ToListAsync` auf:

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

In ASP.NET Core 3.0 und höher gilt Folgendes, wenn eine Aktion `IAsyncEnumerable<T>` zurückgibt:

* Es kommt nicht mehr zu synchroner Iteration.
* Gleiche Effizienz wie bei Rückgabe von <xref:System.Collections.Generic.IEnumerable%601>.

ASP.NET Core 3.0 und höher puffert das Ergebnis der folgenden Aktion, bevor es dem Serialisierungsprogramm bereitgestellt wird:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Sie sollten den Rückgabetyp der Signatur der Aktion als `IAsyncEnumerable<T>` deklarieren, um die asynchrone Iteration sicherzustellen. Letztendlich basiert der Iterationsmodus auf dem zugrunde liegenden konkreten Typ, der zurückgegeben wird. MVC puffert automatisch jeden konkreten Typ, der `IAsyncEnumerable<T>` implementiert.

Sehen Sie sich die folgende Aktion an, die den Verkaufspreis enthaltende Produktdatensätze als `IEnumerable<Product>` zurückgibt:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

Die `IAsyncEnumerable<Product>`-Entsprechung der vorherigen Aktion lautet wie folgt:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.31/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Beide vorangehenden Aktionen führen ab ASP.NET Core 3.0 nicht mehr zu Blockierungen.

## <a name="iactionresult-type"></a>IActionResult-Typ

Der Rückgabetyp <xref:Microsoft.AspNetCore.Mvc.IActionResult> eignet sich in Fällen, in denen mehrere `ActionResult`-Rückgabetypen in einer Aktion möglich sind. Die `ActionResult`-Typen stellen verschiedene HTTP-Statuscodes dar. Alle nicht abstrakten Klassen, die von `ActionResult` abgeleitet werden, qualifizieren sich als gültiger Rückgabetyp. Einige allgemeine Rückgabetypen in dieser Kategorie sind <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) und <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternativ können Hilfsmethoden in der <xref:Microsoft.AspNetCore.Mvc.ControllerBase>-Klasse verwendet werden, um `ActionResult`-Typen aus einer Aktion zurückzugeben. `return BadRequest();` ist beispielsweise eine Kurzform von `return new BadRequestResult();`.

Da in dieser Art von Aktion mehrere Rückgabe Typen und Pfade vorhanden sind, ist die liberale Verwendung des- [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) Attributs erforderlich. Dieses Attribut erzeugt aussagekräftigere Antwortdetails für Web-API-Hilfeseiten, die mit Tools wie [Swagger](xref:tutorials/web-api-help-pages-using-swagger) erstellt wurden. `[ProducesResponseType]` gibt die bekannten Typen und HTTP-Statuscodes an, die von der Aktion zurückgegeben werden sollen.

### <a name="synchronous-action"></a>Synchrone Aktion

Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

In der vorhergehenden Aktion geschieht Folgendes:

* Ein 404-Statuscode wird zurückgegeben, wenn das durch `id` dargestellte Produkt im zugrunde liegenden Datenspeicher nicht vorhanden ist. Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>-Hilfsmethode wird als Kurzform für `return new NotFoundResult();` aufgerufen.
* Ein 200-Statuscode wird mit dem `Product`-Objekt zurückgegeben, wenn das Produkt vorhanden ist. Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>-Hilfsmethode wird als Kurzform für `return new OkObjectResult(product);` aufgerufen.

### <a name="asynchronous-action"></a>Asynchrone Aktion

Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

In der vorhergehenden Aktion geschieht Folgendes:

* Ein 400-Statuscode wird zurückgegeben, wenn die Produktbeschreibung „XYZ-Widget“ enthält. Die <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>-Hilfsmethode wird als Kurzform für `return new BadRequestResult();` aufgerufen.
* Ein 201-Statuscode wird von der Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> generiert, wenn ein Produkt erstellt wird. Eine Alternative zum Aufrufen von `CreatedAtAction` ist `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`. In diesem Codepfad wird das `Product`-Objekt im Antworttext bereitgestellt. Ein `Location`-Antwortheader, der die neu erstellte Produkt-URL enthält, wird bereitgestellt.

Das folgende Modell gibt beispielsweise an, dass Anforderungen die `Name`- und `Description`-Eigenschaften einbinden müssen. Wenn `Name` und `Description` in der Anforderung nicht bereitgestellt werden, tritt bei der Modellvalidierung ein Fehler auf.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

Wenn das- [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) Attribut in ASP.net Core 2,1 oder höher angewendet wird, führen Modell Validierungs Fehler zu einem 400-Statuscode. Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>Aktions \<T> Ergebnistyp

In ASP.net Core 2,1 wurde der Rückgabetyp " [aktionresult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) " für Web-API-Controller Aktionen Damit wird die Rückgabe eines von <xref:Microsoft.AspNetCore.Mvc.ActionResult> abgeleiteten Typs oder eines [spezifischen Typs](#specific-type) ermöglicht. `ActionResult<T>` besitzt gegenüber dem [IActionResult-Typ](#iactionresult-type) die folgenden Vorteile:

* Die [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) -Eigenschaft des Attributs `Type` kann ausgeschlossen werden. `[ProducesResponseType(200, Type = typeof(Product))]` wird beispielsweise zu `[ProducesResponseType(200)]` vereinfacht. Der erwartete Rückgabetyp der Aktion wird stattdessen von `T` in `ActionResult<T>` abgeleitet.
* [Implizite Umwandlungsoperatoren](/dotnet/csharp/language-reference/keywords/implicit) unterstützen die Konvertierung von `T` und `ActionResult` in `ActionResult<T>`. `T` wird in <xref:Microsoft.AspNetCore.Mvc.ObjectResult> konvertiert, wodurch `return new ObjectResult(T);` zu `return T;` vereinfacht wird.

C# unterstützt keine impliziten Umwandlungsoperatoren in Schnittstellen. Daher ist die Konvertierung der Schnittstelle in einen konkreten Typ erforderlich, um `ActionResult<T>` verwenden zu können. Die Verwendung von `IEnumerable` im folgenden Beispiel funktioniert also nicht:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Eine Möglichkeit zur Korrektur des oben stehenden Codes besteht darin, `_repository.GetProducts().ToList();` zurückzugeben.

Die meisten Aktionen haben einen bestimmten Rückgabetyp. Während der Ausführung der Aktion können unerwartete Bedingungen auftreten, wodurch der spezifische Typ nicht zurückgegeben wird. So kann beispielsweise die Modellvalidierung des Eingabeparameters einer Aktion fehlschlagen. In diesem Fall wird üblicherweise der entsprechende `ActionResult`-Typ anstatt des spezifischen Typs zurückgegeben.

### <a name="synchronous-action"></a>Synchrone Aktion

Bei der folgenden synchronen Aktion gibt es zwei mögliche Rückgabetypen:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

In der vorhergehenden Aktion geschieht Folgendes:

* Ein 404-Statuscode wird zurückgegeben, wenn das Produkt in der Datenbank nicht vorhanden ist.
* Ein 200-Statuscode wird mit dem entsprechenden `Product`-Objekt zurückgegeben, wenn das Produkt vorhanden ist. Vor ASP.NET Core 2.1 hätte die Zeile `return product;` stattdessen `return Ok(product);` lauten müssen.

### <a name="asynchronous-action"></a>Asynchrone Aktion

Bei der folgenden asynchronen Aktion gibt es zwei mögliche Rückgabetypen:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

In der vorhergehenden Aktion geschieht Folgendes:

* Ein 400-Statuscode (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) wird durch die ASP.NET Core-Runtime zurückgegeben, wenn:
  * Das [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) -Attribut wurde angewendet, und die Modell Validierung schlägt fehl.
  * Die Produktbeschreibung „XYZ-Widget“ enthält.
* Ein 201-Statuscode wird von der Methode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> generiert, wenn ein Produkt erstellt wird. In diesem Codepfad wird das `Product`-Objekt im Antworttext bereitgestellt. Ein `Location`-Antwortheader, der die neu erstellte Produkt-URL enthält, wird bereitgestellt.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
