---
title: JsonPatch in ASP.NET Core-Web-API
author: rick-anderson
description: Erfahren Sie, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: da507974b88c21de22e2c7a56950943207565138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060546"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="ce954-103">JsonPatch in ASP.NET Core-Web-API</span><span class="sxs-lookup"><span data-stu-id="ce954-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="ce954-104">Von [Tom Dykstra](https://github.com/tdykstra) und [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="ce954-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce954-105">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="ce954-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="ce954-106">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="ce954-106">Package installation</span></span>

<span data-ttu-id="ce954-107">Führen Sie die folgenden Schritte aus, um die JSON-Patchunterstützung in der APP zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="ce954-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="ce954-108">Installieren Sie das [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) nuget-Paket.</span><span class="sxs-lookup"><span data-stu-id="ce954-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="ce954-109">Aktualisieren Sie die- `Startup.ConfigureServices` Methode des Projekts, um aufzurufen <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="ce954-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="ce954-110">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="ce954-111">`AddNewtonsoftJson` ist mit den folgenden MVC-Dienstregistrierungsmethoden kompatibel:</span><span class="sxs-lookup"><span data-stu-id="ce954-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.Add:::no-loc(Razor):::Pages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="ce954-112">JSON Patch, addnewtonweichjson und System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="ce954-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="ce954-113">`AddNewtonsoftJson` ersetzt die `System.Text.Json` -basierten Eingabe-und Ausgabe Formatierer, die zum Formatieren des **gesamten** JSON-Inhalts verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ce954-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="ce954-114">Um Unterstützung für JSON-Patch mithilfe von hinzuzufügen und `Newtonsoft.Json` die anderen Formatierer unverändert zu lassen, aktualisieren Sie die-Methode des Projekts `Startup.ConfigureServices` wie folgt:</span><span class="sxs-lookup"><span data-stu-id="ce954-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="ce954-115">Der vorangehende Code erfordert das `Microsoft.AspNetCore.Mvc.NewtonsoftJson` -Paket und die folgenden- `using` Anweisungen:</span><span class="sxs-lookup"><span data-stu-id="ce954-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="ce954-116">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="ce954-116">PATCH HTTP request method</span></span>

<span data-ttu-id="ce954-117">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ce954-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="ce954-118">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="ce954-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="ce954-119">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="ce954-119">JSON Patch</span></span>

<span data-ttu-id="ce954-120">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce954-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="ce954-121">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen* .</span><span class="sxs-lookup"><span data-stu-id="ce954-121">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="ce954-122">Jeder Vorgang identifiziert eine bestimmte Art von Änderung.</span><span class="sxs-lookup"><span data-stu-id="ce954-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="ce954-123">Beispiele für solche Änderungen sind das Hinzufügen eines Array Elements oder das Ersetzen eines Eigenschafts Werts.</span><span class="sxs-lookup"><span data-stu-id="ce954-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="ce954-124">Die folgenden JSON-Dokumente stellen z. b. eine Ressource, ein JSON-Patch-Dokument für die Ressource und das Ergebnis der Anwendung der patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="ce954-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="ce954-125">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="ce954-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="ce954-126">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="ce954-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="ce954-127">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="ce954-127">In the preceding JSON:</span></span>

* <span data-ttu-id="ce954-128">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="ce954-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="ce954-129">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="ce954-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="ce954-130">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="ce954-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="ce954-131">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="ce954-131">Resource after patch</span></span>

<span data-ttu-id="ce954-132">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="ce954-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="ce954-133">Die Änderungen, die durch Anwenden eines JSON-Patch-Dokuments auf eine Ressource vorgenommen werden, sind atomarisch.</span><span class="sxs-lookup"><span data-stu-id="ce954-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="ce954-134">Wenn ein Vorgang in der Liste fehlschlägt, wird kein Vorgang in der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="ce954-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="ce954-135">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="ce954-135">Path syntax</span></span>

<span data-ttu-id="ce954-136">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="ce954-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="ce954-137">Beispiel: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="ce954-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="ce954-138">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ce954-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="ce954-139">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="ce954-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="ce954-140">Bis `add` zum Ende eines Arrays verwenden Sie einen Bindestrich ( `-` ) anstelle einer Indexnummer: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="ce954-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="ce954-141">Operations</span><span class="sxs-lookup"><span data-stu-id="ce954-141">Operations</span></span>

<span data-ttu-id="ce954-142">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="ce954-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="ce954-143">Vorgang</span><span class="sxs-lookup"><span data-stu-id="ce954-143">Operation</span></span>  | <span data-ttu-id="ce954-144">Hinweise</span><span class="sxs-lookup"><span data-stu-id="ce954-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="ce954-145">Hinzufügen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="ce954-145">Add a property or array element.</span></span> <span data-ttu-id="ce954-146">Für vorhandene Eigenschaft: set value.</span><span class="sxs-lookup"><span data-stu-id="ce954-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="ce954-147">Entfernen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="ce954-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="ce954-148">Identisch mit `remove`, gefolgt von `add` an gleicher Stelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="ce954-149">Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="ce954-150">Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="ce954-151">Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.</span><span class="sxs-lookup"><span data-stu-id="ce954-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="ce954-152">JSON-Patch in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="ce954-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="ce954-153">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ce954-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="ce954-154">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="ce954-154">Action method code</span></span>

<span data-ttu-id="ce954-155">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="ce954-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="ce954-156">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce954-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="ce954-157">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="ce954-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="ce954-158">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="ce954-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="ce954-159">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="ce954-160">Dieser Code aus der Beispiel-APP funktioniert mit dem folgenden `Customer` Modell:</span><span class="sxs-lookup"><span data-stu-id="ce954-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="ce954-161">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ce954-161">The sample action method:</span></span>

* <span data-ttu-id="ce954-162">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="ce954-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="ce954-163">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="ce954-163">Applies the patch.</span></span>
* <span data-ttu-id="ce954-164">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="ce954-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="ce954-165">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ce954-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="ce954-166">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="ce954-166">Model state</span></span>

<span data-ttu-id="ce954-167">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ce954-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="ce954-168">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="ce954-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="ce954-169">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="ce954-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="ce954-170">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="ce954-170">Dynamic objects</span></span>

<span data-ttu-id="ce954-171">Im folgenden Aktionsmethoden Beispiel wird gezeigt, wie ein Patch auf ein dynamisches Objekt angewendet wird:</span><span class="sxs-lookup"><span data-stu-id="ce954-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="ce954-172">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="ce954-172">The add operation</span></span>

* <span data-ttu-id="ce954-173">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="ce954-174">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="ce954-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="ce954-175">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="ce954-176">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce954-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="ce954-177">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="ce954-178">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce954-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="ce954-179">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="ce954-179">The remove operation</span></span>

* <span data-ttu-id="ce954-180">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="ce954-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="ce954-181">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="ce954-182">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce954-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="ce954-183">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="ce954-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="ce954-184">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="ce954-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="ce954-185">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="ce954-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="ce954-186">Im folgenden Beispiel-Patch-Dokument wird `CustomerName` auf NULL festgelegt und löscht Folgendes `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="ce954-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="ce954-187">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="ce954-187">The replace operation</span></span>

<span data-ttu-id="ce954-188">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="ce954-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="ce954-189">Im folgenden Beispiel-Patch-Dokument wird der Wert von festgelegt `CustomerName` und `Orders[0]` durch ein neues- `Order` Objekt ersetzt:</span><span class="sxs-lookup"><span data-stu-id="ce954-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="ce954-190">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="ce954-190">The move operation</span></span>

* <span data-ttu-id="ce954-191">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="ce954-192">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="ce954-193">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="ce954-194">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="ce954-195">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="ce954-196">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-196">The following sample patch document:</span></span>

* <span data-ttu-id="ce954-197">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="ce954-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="ce954-198">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="ce954-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="ce954-199">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="ce954-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="ce954-200">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="ce954-200">The copy operation</span></span>

<span data-ttu-id="ce954-201">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="ce954-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="ce954-202">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-202">The following sample patch document:</span></span>

* <span data-ttu-id="ce954-203">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="ce954-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="ce954-204">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="ce954-205">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="ce954-205">The test operation</span></span>

<span data-ttu-id="ce954-206">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="ce954-207">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="ce954-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="ce954-208">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ce954-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="ce954-209">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="ce954-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="ce954-210">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="ce954-210">Get the code</span></span>

<span data-ttu-id="ce954-211">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)</span><span class="sxs-lookup"><span data-stu-id="ce954-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="ce954-212">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce954-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ce954-213">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="ce954-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="ce954-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="ce954-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="ce954-215">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="ce954-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="ce954-216">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="ce954-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="ce954-217">Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce954-218">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ce954-218">Additional resources</span></span>

* [<span data-ttu-id="ce954-219">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="ce954-220">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="ce954-221">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="ce954-222">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="ce954-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="ce954-223">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="ce954-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="ce954-224">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="ce954-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce954-225">In diesem Artikel wird erläutert, wie JSON Patch-Anforderungen in einer ASP.NET Core-Web-API behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="ce954-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="ce954-226">PATCH HTTP-Anforderungsmethode</span><span class="sxs-lookup"><span data-stu-id="ce954-226">PATCH HTTP request method</span></span>

<span data-ttu-id="ce954-227">Die Methoden PUT und [PATCH](https://tools.ietf.org/html/rfc5789) werden verwendet, um eine vorhandene Ressource zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ce954-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="ce954-228">Der Unterschied zwischen den beiden Methoden besteht darin, dass PUT die gesamte Ressource ersetzt, während PATCH nur die Änderungen angibt.</span><span class="sxs-lookup"><span data-stu-id="ce954-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="ce954-229">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="ce954-229">JSON Patch</span></span>

<span data-ttu-id="ce954-230">Mit dem [JSON Patch](https://tools.ietf.org/html/rfc6902)-Format geben Sie an, dass Updates auf eine Ressource angewendet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ce954-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="ce954-231">Ein JSON Patch-Dokument verfügt über ein Array von *Vorgängen* .</span><span class="sxs-lookup"><span data-stu-id="ce954-231">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="ce954-232">Jeder Vorgang identifiziert eine bestimmte Art von Änderung, z.B. das Hinzufügen eines Arrayelements oder das Ersetzen eines Eigenschaftswerts.</span><span class="sxs-lookup"><span data-stu-id="ce954-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="ce954-233">Die folgenden JSON-Dokumente stellen beispielsweise eine Ressource, ein JSON Patch-Dokument für die Ressource und das Ergebnis der Anwendung der Patchvorgänge dar.</span><span class="sxs-lookup"><span data-stu-id="ce954-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="ce954-234">Ressourcenbeispiel</span><span class="sxs-lookup"><span data-stu-id="ce954-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="ce954-235">JSON Patch-Beispiel</span><span class="sxs-lookup"><span data-stu-id="ce954-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="ce954-236">Für den oben stehenden JSON-Code gilt:</span><span class="sxs-lookup"><span data-stu-id="ce954-236">In the preceding JSON:</span></span>

* <span data-ttu-id="ce954-237">Die `op`-Eigenschaft gibt den Typ des Vorgangs an.</span><span class="sxs-lookup"><span data-stu-id="ce954-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="ce954-238">Die `path`-Eigenschaft gibt das zu aktualisierende Element an.</span><span class="sxs-lookup"><span data-stu-id="ce954-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="ce954-239">Die `value`-Eigenschaft stellt den neuen Wert bereit.</span><span class="sxs-lookup"><span data-stu-id="ce954-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="ce954-240">Ressource nach dem Patch</span><span class="sxs-lookup"><span data-stu-id="ce954-240">Resource after patch</span></span>

<span data-ttu-id="ce954-241">So sieht die Ressource nach der Anwendung des voranstehenden JSON Patch-Dokuments aus:</span><span class="sxs-lookup"><span data-stu-id="ce954-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="ce954-242">Die Änderungen, die durch Anwenden eines JSON Patch-Dokuments auf eine Ressource vorgenommen werden, sind unteilbar: Wenn ein Vorgang aus der Liste fehlschlägt, wird kein Vorgang aus der Liste angewendet.</span><span class="sxs-lookup"><span data-stu-id="ce954-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="ce954-243">Pfadsyntax</span><span class="sxs-lookup"><span data-stu-id="ce954-243">Path syntax</span></span>

<span data-ttu-id="ce954-244">Die [path](https://tools.ietf.org/html/rfc6901)-Eigenschaft eines Vorgangsobjekts weist Schrägstriche zwischen Ebenen auf.</span><span class="sxs-lookup"><span data-stu-id="ce954-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="ce954-245">Beispiel: `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="ce954-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="ce954-246">Nullbasierte Indizes werden verwendet, um Arrayelemente anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ce954-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="ce954-247">Das erste Element des `addresses`-Arrays wäre bei `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="ce954-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="ce954-248">Zum `add` ans Ende eines Arrays verwenden Sie einen Bindestrich (-) anstelle einer Indexnummer: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="ce954-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="ce954-249">Operations</span><span class="sxs-lookup"><span data-stu-id="ce954-249">Operations</span></span>

<span data-ttu-id="ce954-250">Die folgende Tabelle zeigt unterstützt Vorgänge gemäß der [JSON Patch-Spezifikation](https://tools.ietf.org/html/rfc6902):</span><span class="sxs-lookup"><span data-stu-id="ce954-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="ce954-251">Vorgang</span><span class="sxs-lookup"><span data-stu-id="ce954-251">Operation</span></span>  | <span data-ttu-id="ce954-252">Hinweise</span><span class="sxs-lookup"><span data-stu-id="ce954-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="ce954-253">Hinzufügen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="ce954-253">Add a property or array element.</span></span> <span data-ttu-id="ce954-254">Für vorhandene Eigenschaft: set value.</span><span class="sxs-lookup"><span data-stu-id="ce954-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="ce954-255">Entfernen einer Eigenschaft oder eines Arrayelements.</span><span class="sxs-lookup"><span data-stu-id="ce954-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="ce954-256">Identisch mit `remove`, gefolgt von `add` an gleicher Stelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="ce954-257">Identisch mit `remove` aus der Quelle, gefolgt von `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="ce954-258">Identisch mit `add` zum Ziel unter Verwendung des Werts aus der Quelle.</span><span class="sxs-lookup"><span data-stu-id="ce954-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="ce954-259">Gibt Statuscode für Erfolg zurück, wenn der Wert von `path` = bereitgestellter `value`.</span><span class="sxs-lookup"><span data-stu-id="ce954-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="ce954-260">JsonPatch in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ce954-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="ce954-261">Die ASP.NET Core-Implementierung von JSON Patch wird im [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/)-NuGet-Paket bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="ce954-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="ce954-262">Das Paket ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="ce954-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="ce954-263">Aktionsmethodencode</span><span class="sxs-lookup"><span data-stu-id="ce954-263">Action method code</span></span>

<span data-ttu-id="ce954-264">Eine Aktionsmethode für JSON Patch in einem API-Controller:</span><span class="sxs-lookup"><span data-stu-id="ce954-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="ce954-265">Ist versehen mit dem `HttpPatch`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="ce954-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="ce954-266">Akzeptiert eine `JsonPatchDocument<T>`-Klasse, in der Regel mit `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="ce954-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="ce954-267">Ruft `ApplyTo` für das Patch-Dokument auf, um die Änderungen anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="ce954-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="ce954-268">Hier sehen Sie ein Beispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="ce954-269">Dieser Code aus der Beispiel-App funktioniert mit den folgenden `Customer`-Modell.</span><span class="sxs-lookup"><span data-stu-id="ce954-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="ce954-270">Die Beispielaktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="ce954-270">The sample action method:</span></span>

* <span data-ttu-id="ce954-271">Erstellt ein Objekt vom Typ `Customer`.</span><span class="sxs-lookup"><span data-stu-id="ce954-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="ce954-272">Wendet den Patch an.</span><span class="sxs-lookup"><span data-stu-id="ce954-272">Applies the patch.</span></span>
* <span data-ttu-id="ce954-273">Gibt das Ergebnis wird im Textkörper der Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="ce954-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="ce954-274">In einer realen App würde der Code die Daten aus einem Speicher wie z.B. einer Datenbank abrufen und die Datenbank nach dem Anwenden des Patchs aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="ce954-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="ce954-275">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="ce954-275">Model state</span></span>

<span data-ttu-id="ce954-276">Das voranstehende Aktionsmethodenbeispiel ruft eine Überladung von `ApplyTo` auf, die den Modellstatus als einen seiner Parameter akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="ce954-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="ce954-277">Mit dieser Option können Sie Fehlermeldungen in Antworten abrufen.</span><span class="sxs-lookup"><span data-stu-id="ce954-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="ce954-278">Das folgende Beispiel zeigt den Textkörper einer „400 (Ungültige Anforderung)-Antwort für einen `test` Vorgang:</span><span class="sxs-lookup"><span data-stu-id="ce954-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="ce954-279">Dynamische Objekte</span><span class="sxs-lookup"><span data-stu-id="ce954-279">Dynamic objects</span></span>

<span data-ttu-id="ce954-280">Das folgende Aktionsmethodenbeispiel veranschaulicht das Anwenden ein Patchs auf ein dynamisches Objekt.</span><span class="sxs-lookup"><span data-stu-id="ce954-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="ce954-281">Add-Vorgang (Hinzufügen)</span><span class="sxs-lookup"><span data-stu-id="ce954-281">The add operation</span></span>

* <span data-ttu-id="ce954-282">Wenn `path` auf ein Arrayelement verweist: Fügt ein neues Element vor dem von `path` angegebenen Element ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="ce954-283">Wenn `path` auf eine Eigenschaft verweist: Legt den Eigenschaftswert fest.</span><span class="sxs-lookup"><span data-stu-id="ce954-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="ce954-284">Wenn `path` auf einen nicht vorhandenen Speicherort verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="ce954-285">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Fügt eine Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce954-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="ce954-286">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="ce954-287">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und fügt ein `Order`-Objekt am Ende des `Orders`-Arrays hinzu.</span><span class="sxs-lookup"><span data-stu-id="ce954-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="ce954-288">Remove-Vorgang (Entfernen)</span><span class="sxs-lookup"><span data-stu-id="ce954-288">The remove operation</span></span>

* <span data-ttu-id="ce954-289">Wenn `path` auf ein Arrayelement verweist: Entfernt das Element.</span><span class="sxs-lookup"><span data-stu-id="ce954-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="ce954-290">Wenn `path` auf eine Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="ce954-291">Wenn die Ressource, auf die der Patch angewendet werden soll, ein dynamisches Objekt ist: Entfernt die Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="ce954-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="ce954-292">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist:</span><span class="sxs-lookup"><span data-stu-id="ce954-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="ce954-293">Wenn die Eigenschaft NULL-Werte zulässt: auf Null festlegen.</span><span class="sxs-lookup"><span data-stu-id="ce954-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="ce954-294">Wenn die Eigenschaft keine NULL-Werte zulässt: auf `default<T>` festlegen.</span><span class="sxs-lookup"><span data-stu-id="ce954-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="ce954-295">Im folgenden Beispiel legt das Patch-Dokument `CustomerName` auf Null fest und löscht `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="ce954-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="ce954-296">Replace-Vorgang (Ersetzen)</span><span class="sxs-lookup"><span data-stu-id="ce954-296">The replace operation</span></span>

<span data-ttu-id="ce954-297">Dieser Vorgang ist funktionell identisch mit einem `remove`, gefolgt von einem `add`.</span><span class="sxs-lookup"><span data-stu-id="ce954-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="ce954-298">Das folgende Patch-Dokumentbeispiel legt den Wert von `CustomerName` fest und ersetzt `Orders[0]` durch ein neues `Order`-Objekt.</span><span class="sxs-lookup"><span data-stu-id="ce954-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="ce954-299">Move-Vorgang (Verschieben)</span><span class="sxs-lookup"><span data-stu-id="ce954-299">The move operation</span></span>

* <span data-ttu-id="ce954-300">Wenn `path` auf ein Arrayelement verweist: Kopiert das `from`-Element an den Speicherort des `path`-Elements und führt dann einen `remove`-Vorgang für das `from`-Element aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="ce954-301">Wenn `path` auf eine Eigenschaft verweist: Kopiert den Wert der `from`-Eigenschaft in die `path`-Eigenschaft, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="ce954-302">Wenn `path` auf eine nicht vorhandene Eigenschaft verweist:</span><span class="sxs-lookup"><span data-stu-id="ce954-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="ce954-303">Wenn die Ressource, auf die der Patch angewendet werden soll, ein statisches Objekt ist: Die Anforderung schlägt fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="ce954-304">Wenn es sich bei der Ressource, die gepatcht werden soll, um ein dynamisches Objekt handelt: Kopiert die `from`-Eigenschaft in den von `path` angegebenen Speicherort, und führt dann einen `remove`-Vorgang für die `from`-Eigenschaft aus.</span><span class="sxs-lookup"><span data-stu-id="ce954-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="ce954-305">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-305">The following sample patch document:</span></span>

* <span data-ttu-id="ce954-306">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="ce954-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="ce954-307">Legt `Orders[0].OrderName` auf Null fest.</span><span class="sxs-lookup"><span data-stu-id="ce954-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="ce954-308">Verschiebt `Orders[1]` vor `Orders[0]`.</span><span class="sxs-lookup"><span data-stu-id="ce954-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="ce954-309">Copy-Vorgang (Kopieren)</span><span class="sxs-lookup"><span data-stu-id="ce954-309">The copy operation</span></span>

<span data-ttu-id="ce954-310">Dieser Vorgang ist funktionell identisch mit einem `move`-Vorgang ohne den abschließenden `remove`-Schritt.</span><span class="sxs-lookup"><span data-stu-id="ce954-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="ce954-311">Das folgende Patch-Dokumentbeispiel:</span><span class="sxs-lookup"><span data-stu-id="ce954-311">The following sample patch document:</span></span>

* <span data-ttu-id="ce954-312">Kopiert den Wert der `Orders[0].OrderName` nach `CustomerName`.</span><span class="sxs-lookup"><span data-stu-id="ce954-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="ce954-313">Fügt eine Kopie von `Orders[1]` vor `Orders[0]` ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="ce954-314">Test-Vorgang (Testen)</span><span class="sxs-lookup"><span data-stu-id="ce954-314">The test operation</span></span>

<span data-ttu-id="ce954-315">Wenn der Wert an dem von `path` angegebenen Speicherort sich von dem in `value` bereitgestellten Wert unterscheidet, schlägt die Anforderung fehl.</span><span class="sxs-lookup"><span data-stu-id="ce954-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="ce954-316">In diesem Fall schlägt die gesamte PATCH-Anforderung fehl, selbst wenn alle anderen Vorgänge im Patch-Dokument ansonsten erfolgreich ausgeführt werden könnten.</span><span class="sxs-lookup"><span data-stu-id="ce954-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="ce954-317">Der `test`-Vorgang wird häufig verwendet, um ein Update zu verhindern, wenn ein Parallelitätskonflikt vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ce954-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="ce954-318">Das folgende Patch-Dokumentbeispiel hat keine Auswirkungen, wenn der Anfangswert von `CustomerName` „John“ ist, da der Test fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="ce954-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="ce954-319">Abrufen des Codes</span><span class="sxs-lookup"><span data-stu-id="ce954-319">Get the code</span></span>

<span data-ttu-id="ce954-320">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="ce954-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="ce954-321">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ce954-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="ce954-322">Um das Beispiel zu testen, führen Sie die App aus, und senden Sie HTTP-Anforderungen mit den folgenden Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="ce954-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="ce954-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="ce954-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="ce954-324">HTTP-Methode: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="ce954-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="ce954-325">Header: `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="ce954-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="ce954-326">Text: Kopieren Sie eine der JSON-patchdokumentbeispiele, und fügen Sie Sie aus dem *JSON* -Projektordner ein.</span><span class="sxs-lookup"><span data-stu-id="ce954-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ce954-327">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ce954-327">Additional resources</span></span>

* [<span data-ttu-id="ce954-328">IETF RFC 5789 PATCH-Methodenspezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="ce954-329">IETF RFC 6902 JSON Patch-Spezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="ce954-330">IETF RFC 6901 JSON Patch-Pfadformatspezifikation</span><span class="sxs-lookup"><span data-stu-id="ce954-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="ce954-331">[JSON Patch-Dokumentation](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="ce954-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="ce954-332">Enthält Links zu Ressourcen zum Erstellen von JSON Patch-Dokumenten.</span><span class="sxs-lookup"><span data-stu-id="ce954-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="ce954-333">ASP.NET Core JSON Patch-Quellcode</span><span class="sxs-lookup"><span data-stu-id="ce954-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
