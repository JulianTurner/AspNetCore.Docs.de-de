---
title: Formatieren von Antwortdaten in Web-APIs in ASP.NET Core
author: ardalis
description: Informationen zum Formatieren von Antwortdaten in Web-APIS in ASP.NET Core
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 89e3e51373db5f7cff974b7a8c69d06bedf856ca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052512"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="6026b-103">Formatieren von Antwortdaten in Web-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6026b-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="6026b-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6026b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6026b-105">ASP.NET Core MVC unterstützt das Formatieren von Antwortdaten.</span><span class="sxs-lookup"><span data-stu-id="6026b-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="6026b-106">Antwortdaten können mithilfe bestimmter Formate oder durch Übernahme des vom Client angeforderten Formats formatiert werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="6026b-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6026b-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="6026b-108">Formatspezifische Aktionsergebnisse</span><span class="sxs-lookup"><span data-stu-id="6026b-108">Format-specific Action Results</span></span>

<span data-ttu-id="6026b-109">Einige Aktionsergebnistypen sind für ein bestimmtes Format spezifisch, wie z.B. <xref:Microsoft.AspNetCore.Mvc.JsonResult> und <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="6026b-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="6026b-110">Aktionen können Ergebnisse zurückgeben, die in einem bestimmten Format formatiert sind, unabhängig von den Clienteinstellungen.</span><span class="sxs-lookup"><span data-stu-id="6026b-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="6026b-111">Mit `JsonResult` beispielsweise werden JSON-formatierte Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="6026b-112">Mit `ContentResult` oder einer Zeichenfolge werden Zeichenfolgendaten in Nur-Text-Format zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="6026b-113">Zum Zurückgeben eines bestimmten Typs ist keine Aktion erforderlich.</span><span class="sxs-lookup"><span data-stu-id="6026b-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="6026b-114">ASP.NET Core unterstützt jeden Objektrückgabewert.</span><span class="sxs-lookup"><span data-stu-id="6026b-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="6026b-115">Ergebnisse von Aktionen, die Objekte zurückgeben, deren Typ nicht <xref:Microsoft.AspNetCore.Mvc.IActionResult> ist, werden mit der entsprechenden <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>-Implementierung serialisiert.</span><span class="sxs-lookup"><span data-stu-id="6026b-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="6026b-116">Weitere Informationen finden Sie unter <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="6026b-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="6026b-117">Die integrierte Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> gibt JSON-formatierte Daten zurück: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="6026b-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="6026b-118">Der Beispieldownload gibt die Liste der Autoren zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="6026b-119">Bei Verwendung der F12-Entwicklertools im Browser oder von [Postman](https://www.getpostman.com/tools) mit dem obigen Code gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6026b-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="6026b-120">Der Antwortheader mit **content-type:** `application/json; charset=utf-8` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6026b-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="6026b-121">Die Anforderungsheader werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="6026b-121">The request headers are displayed.</span></span> <span data-ttu-id="6026b-122">Beispiel: Der Header `Accept`.</span><span class="sxs-lookup"><span data-stu-id="6026b-122">For example, the `Accept` header.</span></span> <span data-ttu-id="6026b-123">Der `Accept`-Header wird vom vorangehenden Code ignoriert.</span><span class="sxs-lookup"><span data-stu-id="6026b-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="6026b-124">Wenn Sie Daten im Textformat zurückgeben möchten, verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ContentResult> und das <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>-Hilfsprogramm:</span><span class="sxs-lookup"><span data-stu-id="6026b-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="6026b-125">Im obigen Code wird `text/plain` als `Content-Type` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="6026b-126">Das Zurückgeben einer Zeichenfolge liefert `text/plain` als `Content-Type`:</span><span class="sxs-lookup"><span data-stu-id="6026b-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="6026b-127">Bei Aktionen mit mehreren Rückgabetypen wird `IActionResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="6026b-128">Beispiel: Die Rückgabe von verschiedenen HTTP-Statuscodes basierend auf dem Ergebnis der ausgeführten Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="6026b-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="6026b-129">Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="6026b-129">Content negotiation</span></span>

<span data-ttu-id="6026b-130">Eine Inhaltsaushandlung tritt auf, wenn der Client einen [Accept-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) angibt.</span><span class="sxs-lookup"><span data-stu-id="6026b-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="6026b-131">Das von ASP.NET Core verwendete Standardformat ist [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="6026b-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="6026b-132">Für die Inhaltsaushandlung gilt:</span><span class="sxs-lookup"><span data-stu-id="6026b-132">Content negotiation is:</span></span>

* <span data-ttu-id="6026b-133">Sie wird durch <xref:Microsoft.AspNetCore.Mvc.ObjectResult> implementiert.</span><span class="sxs-lookup"><span data-stu-id="6026b-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="6026b-134">Sie ist in die Statuscode-spezifischen Aktionsergebnisse integriert, die von den Hilfsmethoden zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="6026b-135">Die Hilfsmethoden für Aktionsergebnisse basieren auf `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="6026b-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="6026b-136">Wenn ein Modelltyp zurückgegeben wird, lautet der Rückgabetyp `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="6026b-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="6026b-137">Die folgende Aktionsmethode verwendet die Hilfsmethoden `Ok` und `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="6026b-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="6026b-138">Standardmäßig unterstützt ASP.NET Core `application/json`-, `text/json`- und `text/plain`-Medientypen.</span><span class="sxs-lookup"><span data-stu-id="6026b-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="6026b-139">Tools wie [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/tools) können den `Accept`-Anforderungsheader festlegen, um das Rückgabeformat anzugeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="6026b-140">Wenn der `Accept`-Header einen vom Server unterstützten Typ enthält, wird dieser Typ zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="6026b-141">Der nächste Abschnitt zeigt, wie zusätzliche Formatierer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="6026b-142">Controlleraktionen können POCOs (Plain Old CLR Objects) zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="6026b-143">Wenn ein POCO zurückgegeben wird, erstellt die Runtime automatisch ein `ObjectResult`, das das Objekt umschließt.</span><span class="sxs-lookup"><span data-stu-id="6026b-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="6026b-144">Der Client empfängt das formatierte serialisierte Objekt.</span><span class="sxs-lookup"><span data-stu-id="6026b-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="6026b-145">Wenn das zurückgegebene Objekt `null` ist, wird eine `204 No Content`-Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="6026b-146">Zurückgeben eines Objekttyps:</span><span class="sxs-lookup"><span data-stu-id="6026b-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="6026b-147">Im oben stehenden Code gibt die Anforderung eines gültigen Autoralias eine `200 OK`-Antwort mit den Daten des Autors zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="6026b-148">Die Anforderung eines ungültigen Alias gibt eine `204 No Content`-Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="6026b-149">Der Accept-Header</span><span class="sxs-lookup"><span data-stu-id="6026b-149">The Accept header</span></span>

<span data-ttu-id="6026b-150">Eine *Aushandlung* des Inhalts findet statt, wenn in der Anforderung ein `Accept`-Header angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="6026b-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="6026b-151">Wenn eine Anforderung einen Accept-Header enthält, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="6026b-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="6026b-152">Die Medientypen im Accept-Header werden in der bevorzugten Reihenfolge aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="6026b-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="6026b-153">Es wird versucht, einen Formatierer zu finden, der eine Antwort in einem der angegebenen Formate erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="6026b-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="6026b-154">Wenn kein Formatierer gefunden wird, der die Clientanforderung erfüllen kann, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="6026b-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="6026b-155">Es wird `406 Not Acceptable` zurückgegeben, wenn <xref:Microsoft.AspNetCore.Mvc.MvcOptions> festgelegt wurde, oder</span><span class="sxs-lookup"><span data-stu-id="6026b-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="6026b-156">Es wird versucht, den ersten Formatierer zu finden, der eine Antwort erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="6026b-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="6026b-157">Wenn kein Formatierer für das angeforderte Format konfiguriert wurde, wird der erste Formatierer verwendet, der das Objekt formatieren kann.</span><span class="sxs-lookup"><span data-stu-id="6026b-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="6026b-158">Wenn in der Anforderung kein `Accept`-Header vorhanden ist, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6026b-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="6026b-159">Der erste Formatierer, der das Objekt verarbeiten kann, wird zum Serialisieren der Antwort verwendet.</span><span class="sxs-lookup"><span data-stu-id="6026b-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="6026b-160">Es findet keine Aushandlung statt.</span><span class="sxs-lookup"><span data-stu-id="6026b-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="6026b-161">Der Server bestimmt, welches Format zurückgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="6026b-161">The server is determining what format to return.</span></span>

<span data-ttu-id="6026b-162">Enthält der Accept-Header `*/*`, wird der Header ignoriert, es sei denn, `RespectBrowserAcceptHeader` ist in <xref:Microsoft.AspNetCore.Mvc.MvcOptions> auf TRUE festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6026b-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="6026b-163">Browser und Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="6026b-163">Browsers and content negotiation</span></span>

<span data-ttu-id="6026b-164">Im Gegensatz zu typischen API-Clients, stellen Webbrowser `Accept`-Header bereit.</span><span class="sxs-lookup"><span data-stu-id="6026b-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="6026b-165">Webbrowser geben viele Formate an, einschließlich Platzhaltern.</span><span class="sxs-lookup"><span data-stu-id="6026b-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="6026b-166">Wenn das Framework erkennt, dass eine Anforderung von einem Browser stammt, wird standardmäßig Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="6026b-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="6026b-167">Der `Accept`-Header wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="6026b-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="6026b-168">Der Inhalt wird im JSON-Format zurückgegeben, sofern nicht anders konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6026b-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="6026b-169">Dies sorgt bei der Verwendung von APIs für ein konsistenteres browserübergreifendes Benutzererlebnis.</span><span class="sxs-lookup"><span data-stu-id="6026b-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="6026b-170">Um eine App so zu konfigurieren, dass Accept-Header in Browsern berücksichtigt werden, legen Sie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="6026b-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="6026b-171">Konfigurieren von Formatierern</span><span class="sxs-lookup"><span data-stu-id="6026b-171">Configure formatters</span></span>

<span data-ttu-id="6026b-172">Apps, die zusätzliche Formate unterstützen müssen, können die geeigneten NuGet-Pakete hinzufügen und die Unterstützung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="6026b-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="6026b-173">Es gibt unterschiedliche Formatierungsprogramme für Ein- und für Ausgaben.</span><span class="sxs-lookup"><span data-stu-id="6026b-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="6026b-174">Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet.</span><span class="sxs-lookup"><span data-stu-id="6026b-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="6026b-175">Ausgabeformatierer werden zum Formatieren von Antworten verwendet.</span><span class="sxs-lookup"><span data-stu-id="6026b-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="6026b-176">Informationen zum Erstellen eines benutzerdefinierten Formatierers finden Sie unter [Benutzerdefinierte Formatierer](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="6026b-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="6026b-177">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="6026b-177">Add XML format support</span></span>

<span data-ttu-id="6026b-178">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="6026b-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="6026b-179">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="6026b-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="6026b-180">Bei Verwendung dieses Codes geben Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="6026b-181">Konfigurieren von System.Text.Json-basierten Formatierern</span><span class="sxs-lookup"><span data-stu-id="6026b-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="6026b-182">Funktionen für die `System.Text.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="6026b-183">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="6026b-184">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6026b-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="6026b-185">Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format</span><span class="sxs-lookup"><span data-stu-id="6026b-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="6026b-186">Vor ASP.NET Core 3.0 wurden standardmäßig die JSON-Formatierer verwendet, die mithilfe des `Newtonsoft.Json`-Pakets implementiert wurden.</span><span class="sxs-lookup"><span data-stu-id="6026b-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="6026b-187">In ASP.NET Core 3.0 oder höher basieren die Standardformatierer für JSON auf `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="6026b-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="6026b-188">Unterstützung für `Newtonsoft.Json` basierte Formatierer und Features ist verfügbar, indem [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) Sie das nuget-Paket installieren und in konfigurieren `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="6026b-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="6026b-189">Im vorangehenden Code werden mit dem-Befehl `AddNewtonsoftJson` die folgenden Features für Web-API, MVC und Pages konfiguriert, die verwendet werden können Razor `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="6026b-189">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="6026b-190">Eingabe-und Ausgabe Formatierer, die JSON lesen und schreiben</span><span class="sxs-lookup"><span data-stu-id="6026b-190">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="6026b-191">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="6026b-191">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="6026b-192">TempData</span><span class="sxs-lookup"><span data-stu-id="6026b-192">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="6026b-193">Einige Features funktionieren mit `System.Text.Json`-basierten Formatierern möglicherweise nicht gut und erfordern einen Verweis auf die `Newtonsoft.Json`-basierten Formatierer.</span><span class="sxs-lookup"><span data-stu-id="6026b-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="6026b-194">Verwenden Sie weiterhin `Newtonsoft.Json`-basierte Formatierer, wenn für die App Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="6026b-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="6026b-195">Sie verwendet `Newtonsoft.Json`-Attribute.</span><span class="sxs-lookup"><span data-stu-id="6026b-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="6026b-196">Zum Beispiel: `[JsonProperty]` oder `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="6026b-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="6026b-197">Sie passt die Serialisierungseinstellungen an.</span><span class="sxs-lookup"><span data-stu-id="6026b-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="6026b-198">Sie nutzt Features, die von `Newtonsoft.Json` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="6026b-199">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="6026b-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="6026b-200">Vor ASP.NET Core 3.0 akzeptiert `JsonResult.SerializerSettings` eine Instanz von `JsonSerializerSettings`, die für `Newtonsoft.Json` spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="6026b-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="6026b-201">die [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>)-Dokumentation generiert.</span><span class="sxs-lookup"><span data-stu-id="6026b-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="6026b-202">Funktionen für die `Newtonsoft.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="6026b-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="6026b-203">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="6026b-204">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6026b-204">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="6026b-205">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="6026b-205">Add XML format support</span></span>

<span data-ttu-id="6026b-206">Die XML-Formatierung erfordert das NuGet-Paket [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="6026b-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="6026b-207">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="6026b-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="6026b-208">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="6026b-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="6026b-209">Bei Verwendung dieses Codes sollten Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="6026b-210">Angeben eines Formats</span><span class="sxs-lookup"><span data-stu-id="6026b-210">Specify a format</span></span>

<span data-ttu-id="6026b-211">Um die Antwortformate einzuschränken, wenden Sie den [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter an.</span><span class="sxs-lookup"><span data-stu-id="6026b-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="6026b-212">Wie die [Filters](xref:mvc/controllers/filters)meisten Filter `[Produces]` können auch auf Aktion, Controller oder globaler Bereich angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="6026b-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="6026b-213">Der vorangehende [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter:</span><span class="sxs-lookup"><span data-stu-id="6026b-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="6026b-214">Er erzwingt die Rückgabe von JSON-formatierten Antworten von allen Aktionen im Controller.</span><span class="sxs-lookup"><span data-stu-id="6026b-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="6026b-215">Wenn andere Formatierer konfiguriert sind und der Client ein anderes Format angibt, wird JSON zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="6026b-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="6026b-216">Weitere Informationen finden Sie unter [Filter](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="6026b-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="6026b-217">Formatierer für besondere Fälle</span><span class="sxs-lookup"><span data-stu-id="6026b-217">Special case formatters</span></span>

<span data-ttu-id="6026b-218">Einige besondere Fälle werden mithilfe von integrierten Formatierungsprogrammen implementiert.</span><span class="sxs-lookup"><span data-stu-id="6026b-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="6026b-219">Standardmäßig werden `string`-Rückgabetypen als *text/plain* formatiert (bzw. als *text/html* , wenn sie über den `Accept`-Header angefordert werden).</span><span class="sxs-lookup"><span data-stu-id="6026b-219">By default, `string` return types are formatted as *text/plain* ( *text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="6026b-220">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="6026b-221">Formatierer werden in der `ConfigureServices`-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="6026b-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="6026b-222">Aktionen mit einem Modellobjekt-Rückgabetyp geben `null` zurück, wenn der Rückgabewert `204 No Content` lautet.</span><span class="sxs-lookup"><span data-stu-id="6026b-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="6026b-223">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="6026b-224">Der folgende Code entfernt `StringOutputFormatter` und `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="6026b-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="6026b-225">Ohne den `StringOutputFormatter` formatiert der integrierte JSON-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="6026b-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="6026b-226">Wenn der integrierte JSON-Formatierer entfernt wird und ein XML-Formatierer verfügbar ist, formatiert der XML-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="6026b-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="6026b-227">Andernfalls geben `string`-Rückgabetypen `406 Not Acceptable` zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="6026b-228">Ohne `HttpNoContentOutputFormatter` werden NULL-Objekte mithilfe des konfigurierten Formatierungsprogramms formatiert.</span><span class="sxs-lookup"><span data-stu-id="6026b-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="6026b-229">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6026b-229">For example:</span></span>

* <span data-ttu-id="6026b-230">Der JSON-Formatierer gibt eine Antwort mit dem Text `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="6026b-231">Der XML-Formatierer gibt ein leeres XML-Element mit festgelegtem Attribut `xsi:nil="true"` zurück.</span><span class="sxs-lookup"><span data-stu-id="6026b-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="6026b-232">Zuordnung des Antwortformats durch URLs</span><span class="sxs-lookup"><span data-stu-id="6026b-232">Response format URL mappings</span></span>

<span data-ttu-id="6026b-233">Clients können in der URL ein bestimmtes Format anfordern, beispielsweise folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="6026b-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="6026b-234">In der Abfragezeichenfolge oder als Teil des Pfads.</span><span class="sxs-lookup"><span data-stu-id="6026b-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="6026b-235">Durch Verwendung einer formatspezifischen Dateierweiterung wie „.xml“ oder „.json“.</span><span class="sxs-lookup"><span data-stu-id="6026b-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="6026b-236">Die Zuordnung des Anforderungspfads sollte in der Route angegeben werden, die die API verwendet.</span><span class="sxs-lookup"><span data-stu-id="6026b-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="6026b-237">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="6026b-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="6026b-238">Mit der oben genannten Route kann das angeforderte Format als optionale Dateierweiterung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6026b-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="6026b-239">Das [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) -Attribut überprüft das vorhanden sein des Format Werts im `RouteData` und ordnet das Antwortformat dem entsprechenden Formatierer zu, wenn die Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="6026b-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="6026b-240">Route</span><span class="sxs-lookup"><span data-stu-id="6026b-240">Route</span></span>        |             <span data-ttu-id="6026b-241">Formatierungsprogramm</span><span class="sxs-lookup"><span data-stu-id="6026b-241">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="6026b-242">Standard-Ausgabeformatierungsprogramm</span><span class="sxs-lookup"><span data-stu-id="6026b-242">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="6026b-243">JSON-Formatierungsprogramm (falls konfiguriert)</span><span class="sxs-lookup"><span data-stu-id="6026b-243">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="6026b-244">XML-Formatierungsprogramm (falls konfiguriert)</span><span class="sxs-lookup"><span data-stu-id="6026b-244">The XML formatter (if configured)</span></span>  |
