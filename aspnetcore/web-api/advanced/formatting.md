---
title: Formatieren von Antwortdaten in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Formatieren von Antwortdaten in Web-APIS in ASP.NET Core
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 1/28/2021
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
uid: web-api/advanced/formatting
ms.openlocfilehash: 5d228af00ee34e7f8ca60a5085872fdb93842367
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057498"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="a4f46-103">Formatieren von Antwortdaten in Web-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a4f46-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="a4f46-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a4f46-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a4f46-105">ASP.NET Core MVC unterstützt das Formatieren von Antwortdaten.</span><span class="sxs-lookup"><span data-stu-id="a4f46-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="a4f46-106">Antwortdaten können mithilfe bestimmter Formate oder durch Übernahme des vom Client angeforderten Formats formatiert werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="a4f46-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a4f46-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="a4f46-108">Formatspezifische Aktionsergebnisse</span><span class="sxs-lookup"><span data-stu-id="a4f46-108">Format-specific Action Results</span></span>

<span data-ttu-id="a4f46-109">Einige Aktionsergebnistypen sind für ein bestimmtes Format spezifisch, wie z.B. <xref:Microsoft.AspNetCore.Mvc.JsonResult> und <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="a4f46-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="a4f46-110">Aktionen können Ergebnisse zurückgeben, die in einem bestimmten Format formatiert sind, unabhängig von den Clienteinstellungen.</span><span class="sxs-lookup"><span data-stu-id="a4f46-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="a4f46-111">Mit `JsonResult` beispielsweise werden JSON-formatierte Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="a4f46-112">Mit `ContentResult` oder einer Zeichenfolge werden Zeichenfolgendaten in Nur-Text-Format zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="a4f46-113">Zum Zurückgeben eines bestimmten Typs ist keine Aktion erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a4f46-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="a4f46-114">ASP.NET Core unterstützt jeden Objektrückgabewert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="a4f46-115">Ergebnisse von Aktionen, die Objekte zurückgeben, deren Typ nicht <xref:Microsoft.AspNetCore.Mvc.IActionResult> ist, werden mit der entsprechenden <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter>-Implementierung serialisiert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="a4f46-116">Weitere Informationen finden Sie unter <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="a4f46-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="a4f46-117">Die integrierte Hilfsmethode <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> gibt JSON-formatierte Daten zurück: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="a4f46-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="a4f46-118">Der Beispieldownload gibt die Liste der Autoren zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="a4f46-119">Bei Verwendung der F12-Entwicklertools im Browser oder von [Postman](https://www.getpostman.com/tools) mit dem obigen Code gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a4f46-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="a4f46-120">Der Antwortheader mit **content-type:** `application/json; charset=utf-8` wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="a4f46-121">Die Anforderungsheader werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-121">The request headers are displayed.</span></span> <span data-ttu-id="a4f46-122">Beispiel: Der Header `Accept`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-122">For example, the `Accept` header.</span></span> <span data-ttu-id="a4f46-123">Der `Accept`-Header wird vom vorangehenden Code ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="a4f46-124">Wenn Sie Daten im Textformat zurückgeben möchten, verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ContentResult> und das <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A>-Hilfsprogramm:</span><span class="sxs-lookup"><span data-stu-id="a4f46-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="a4f46-125">Im obigen Code wird `text/plain` als `Content-Type` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="a4f46-126">Das Zurückgeben einer Zeichenfolge liefert `text/plain` als `Content-Type`:</span><span class="sxs-lookup"><span data-stu-id="a4f46-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="a4f46-127">Bei Aktionen mit mehreren Rückgabetypen wird `IActionResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="a4f46-128">Beispiel: Die Rückgabe von verschiedenen HTTP-Statuscodes basierend auf dem Ergebnis der ausgeführten Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="a4f46-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="a4f46-129">Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="a4f46-129">Content negotiation</span></span>

<span data-ttu-id="a4f46-130">Eine Inhaltsaushandlung tritt auf, wenn der Client einen [Accept-Header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) angibt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="a4f46-131">Das von ASP.NET Core verwendete Standardformat ist [JSON](https://json.org/).</span><span class="sxs-lookup"><span data-stu-id="a4f46-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="a4f46-132">Für die Inhaltsaushandlung gilt:</span><span class="sxs-lookup"><span data-stu-id="a4f46-132">Content negotiation is:</span></span>

* <span data-ttu-id="a4f46-133">Sie wird durch <xref:Microsoft.AspNetCore.Mvc.ObjectResult> implementiert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="a4f46-134">Sie ist in die Statuscode-spezifischen Aktionsergebnisse integriert, die von den Hilfsmethoden zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="a4f46-135">Die Hilfsmethoden für Aktionsergebnisse basieren auf `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="a4f46-136">Wenn ein Modelltyp zurückgegeben wird, lautet der Rückgabetyp `ObjectResult`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="a4f46-137">Die folgende Aktionsmethode verwendet die Hilfsmethoden `Ok` und `NotFound`:</span><span class="sxs-lookup"><span data-stu-id="a4f46-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="a4f46-138">Standardmäßig unterstützt ASP.NET Core `application/json`-, `text/json`- und `text/plain`-Medientypen.</span><span class="sxs-lookup"><span data-stu-id="a4f46-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="a4f46-139">Tools wie [Fiddler](https://www.telerik.com/fiddler) oder [Postman](https://www.getpostman.com/tools) können den `Accept`-Anforderungsheader festlegen, um das Rückgabeformat anzugeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="a4f46-140">Wenn der `Accept`-Header einen vom Server unterstützten Typ enthält, wird dieser Typ zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="a4f46-141">Der nächste Abschnitt zeigt, wie zusätzliche Formatierer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="a4f46-142">Controlleraktionen können POCOs (Plain Old CLR Objects) zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="a4f46-143">Wenn ein POCO zurückgegeben wird, erstellt die Runtime automatisch ein `ObjectResult`, das das Objekt umschließt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="a4f46-144">Der Client empfängt das formatierte serialisierte Objekt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="a4f46-145">Wenn das zurückgegebene Objekt `null` ist, wird eine `204 No Content`-Antwort zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="a4f46-146">Zurückgeben eines Objekttyps:</span><span class="sxs-lookup"><span data-stu-id="a4f46-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="a4f46-147">Im oben stehenden Code gibt die Anforderung eines gültigen Autoralias eine `200 OK`-Antwort mit den Daten des Autors zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="a4f46-148">Die Anforderung eines ungültigen Alias gibt eine `204 No Content`-Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="a4f46-149">Der Accept-Header</span><span class="sxs-lookup"><span data-stu-id="a4f46-149">The Accept header</span></span>

<span data-ttu-id="a4f46-150">Eine *Aushandlung* des Inhalts findet statt, wenn in der Anforderung ein `Accept`-Header angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="a4f46-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="a4f46-151">Wenn eine Anforderung einen Accept-Header enthält, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="a4f46-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="a4f46-152">Die Medientypen im Accept-Header werden in der bevorzugten Reihenfolge aufgelistet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="a4f46-153">Es wird versucht, einen Formatierer zu finden, der eine Antwort in einem der angegebenen Formate erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="a4f46-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="a4f46-154">Wenn kein Formatierer gefunden wird, der die Clientanforderung erfüllen kann, führt ASP.NET Core Folgendes aus:</span><span class="sxs-lookup"><span data-stu-id="a4f46-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="a4f46-155">Gibt `406 Not Acceptable` zurück <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> , wenn auf festgelegt ist `true` , oder-</span><span class="sxs-lookup"><span data-stu-id="a4f46-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions.ReturnHttpNotAcceptable?displayProperty=nameWithType> is set to `true`, or -</span></span>
* <span data-ttu-id="a4f46-156">Es wird versucht, den ersten Formatierer zu finden, der eine Antwort erzeugen kann.</span><span class="sxs-lookup"><span data-stu-id="a4f46-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="a4f46-157">Wenn kein Formatierer für das angeforderte Format konfiguriert wurde, wird der erste Formatierer verwendet, der das Objekt formatieren kann.</span><span class="sxs-lookup"><span data-stu-id="a4f46-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="a4f46-158">Wenn in der Anforderung kein `Accept`-Header vorhanden ist, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a4f46-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="a4f46-159">Der erste Formatierer, der das Objekt verarbeiten kann, wird zum Serialisieren der Antwort verwendet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="a4f46-160">Es findet keine Aushandlung statt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="a4f46-161">Der Server bestimmt, welches Format zurückgegeben werden soll.</span><span class="sxs-lookup"><span data-stu-id="a4f46-161">The server is determining what format to return.</span></span>

<span data-ttu-id="a4f46-162">Enthält der Accept-Header `*/*`, wird der Header ignoriert, es sei denn, `RespectBrowserAcceptHeader` ist in <xref:Microsoft.AspNetCore.Mvc.MvcOptions> auf TRUE festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="a4f46-163">Browser und Inhaltsaushandlung</span><span class="sxs-lookup"><span data-stu-id="a4f46-163">Browsers and content negotiation</span></span>

<span data-ttu-id="a4f46-164">Im Gegensatz zu typischen API-Clients, stellen Webbrowser `Accept`-Header bereit.</span><span class="sxs-lookup"><span data-stu-id="a4f46-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="a4f46-165">Webbrowser geben viele Formate an, einschließlich Platzhaltern.</span><span class="sxs-lookup"><span data-stu-id="a4f46-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="a4f46-166">Wenn das Framework erkennt, dass eine Anforderung von einem Browser stammt, wird standardmäßig Folgendes ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="a4f46-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="a4f46-167">Der `Accept`-Header wird ignoriert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="a4f46-168">Der Inhalt wird im JSON-Format zurückgegeben, sofern nicht anders konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="a4f46-169">Dies sorgt bei der Verwendung von APIs für ein konsistenteres browserübergreifendes Benutzererlebnis.</span><span class="sxs-lookup"><span data-stu-id="a4f46-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="a4f46-170">Um eine App so zu konfigurieren, dass Accept-Header in Browsern berücksichtigt werden, legen Sie <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> auf `true` fest:</span><span class="sxs-lookup"><span data-stu-id="a4f46-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="a4f46-171">Konfigurieren von Formatierern</span><span class="sxs-lookup"><span data-stu-id="a4f46-171">Configure formatters</span></span>

<span data-ttu-id="a4f46-172">Apps, die zusätzliche Formate unterstützen müssen, können die geeigneten NuGet-Pakete hinzufügen und die Unterstützung konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="a4f46-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="a4f46-173">Es gibt unterschiedliche Formatierungsprogramme für Ein- und für Ausgaben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="a4f46-174">Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="a4f46-175">Ausgabeformatierer werden zum Formatieren von Antworten verwendet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="a4f46-176">Informationen zum Erstellen eines benutzerdefinierten Formatierers finden Sie unter [Benutzerdefinierte Formatierer](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="a4f46-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="a4f46-177">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="a4f46-177">Add XML format support</span></span>

<span data-ttu-id="a4f46-178">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="a4f46-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="a4f46-179">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="a4f46-180">Bei Verwendung dieses Codes geben Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="a4f46-181">Konfigurieren von System.Text.Jsauf basierten Formatierern</span><span class="sxs-lookup"><span data-stu-id="a4f46-181">Configure System.Text.Json based formatters</span></span>

<span data-ttu-id="a4f46-182">Funktionen für die- `System.Text.Json` basierten Formatierer können mithilfe von konfiguriert werden <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName> .</span><span class="sxs-lookup"><span data-stu-id="a4f46-182">Features for the `System.Text.Json` based formatters can be configured using <xref:Microsoft.AspNetCore.Mvc.JsonOptions.JsonSerializerOptions?displayProperty=fullName>.</span></span> <span data-ttu-id="a4f46-183">Die Standard Formatierung ist "CamelCase".</span><span class="sxs-lookup"><span data-stu-id="a4f46-183">The default formatting is camelCase.</span></span> <span data-ttu-id="a4f46-184">Der folgende markierte Code legt die PascalCase-Formatierung fest:</span><span class="sxs-lookup"><span data-stu-id="a4f46-184">The following highlighted code sets PascalCase formatting:</span></span>

[!code-csharp[](./formatting/5.0samples/WebAPI5PascalCase/Startup.cs?name=snippet&highlight=4-5)]

<span data-ttu-id="a4f46-185">Mit der folgenden Aktionsmethode wird [controllerbase. Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) aufgerufen, um eine Antwort zu erstellen <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> :</span><span class="sxs-lookup"><span data-stu-id="a4f46-185">The following action method calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response:</span></span>

[!code-csharp[](formatting/5.0samples/WebAPI5PascalCase/Controllers/WeatherForecastController.cs?name=snippet&highlight=4)]

<span data-ttu-id="a4f46-186">Mit dem vorangehenden Code:</span><span class="sxs-lookup"><span data-stu-id="a4f46-186">With the preceding code:</span></span>

  * <span data-ttu-id="a4f46-187">`https://localhost:5001/WeatherForecast/temperature` gibt PascalCase zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-187">`https://localhost:5001/WeatherForecast/temperature` returns PascalCase.</span></span>
  * <span data-ttu-id="a4f46-188">`https://localhost:5001/WeatherForecast/error` gibt "CamelCase" zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-188">`https://localhost:5001/WeatherForecast/error` returns camelCase.</span></span> <span data-ttu-id="a4f46-189">Die Fehler Antwort lautet immer "CamelCase", auch wenn die APP das Format auf "PascalCase" festlegt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-189">The error response is always camelCase, even when the app sets the format to PascalCase.</span></span> <span data-ttu-id="a4f46-190">`ProblemDetails` folgt [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), das Kleinbuchstaben angibt</span><span class="sxs-lookup"><span data-stu-id="a4f46-190">`ProblemDetails` follows [RFC 7807](https://tools.ietf.org/html/rfc7807#appendix-A), which specifies lower case</span></span>

<span data-ttu-id="a4f46-191">Mit dem folgenden Code wird PascalCase festgelegt und ein benutzerdefinierter Konverter hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="a4f46-191">The following code sets PascalCase and adds a custom converter:</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="a4f46-192">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-192">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="a4f46-193">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a4f46-193">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="a4f46-194">Hinzufügen von Newtonsoft.Json-basierter Unterstützung für das JSON-Format</span><span class="sxs-lookup"><span data-stu-id="a4f46-194">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="a4f46-195">Vor ASP.NET Core 3.0 wurden standardmäßig die JSON-Formatierer verwendet, die mithilfe des `Newtonsoft.Json`-Pakets implementiert wurden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-195">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="a4f46-196">In ASP.NET Core 3.0 oder höher basieren die Standardformatierer für JSON auf `System.Text.Json`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-196">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="a4f46-197">Unterstützung für `Newtonsoft.Json` basierte Formatierer und Features ist verfügbar, indem [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) Sie das nuget-Paket installieren und in konfigurieren `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="a4f46-197">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="a4f46-198">Im vorangehenden Code werden mit dem-Befehl `AddNewtonsoftJson` die folgenden Features für Web-API, MVC und Pages konfiguriert, die verwendet werden können Razor `Newtonsoft.Json` :</span><span class="sxs-lookup"><span data-stu-id="a4f46-198">In the preceding code, the call to `AddNewtonsoftJson` configures the following Web API, MVC, and Razor Pages features to use `Newtonsoft.Json`:</span></span>

* <span data-ttu-id="a4f46-199">Eingabe-und Ausgabe Formatierer, die JSON lesen und schreiben</span><span class="sxs-lookup"><span data-stu-id="a4f46-199">Input and output formatters that read and write JSON</span></span>
* <xref:Microsoft.AspNetCore.Mvc.JsonResult>
* [<span data-ttu-id="a4f46-200">JSON Patch</span><span class="sxs-lookup"><span data-stu-id="a4f46-200">JSON Patch</span></span>](xref:web-api/jsonpatch)
* <xref:Microsoft.AspNetCore.Mvc.Rendering.IJsonHelper>
* [<span data-ttu-id="a4f46-201">TempData</span><span class="sxs-lookup"><span data-stu-id="a4f46-201">TempData</span></span>](xref:fundamentals/app-state#tempdata)

<span data-ttu-id="a4f46-202">Einige Features funktionieren mit `System.Text.Json`-basierten Formatierern möglicherweise nicht gut und erfordern einen Verweis auf die `Newtonsoft.Json`-basierten Formatierer.</span><span class="sxs-lookup"><span data-stu-id="a4f46-202">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="a4f46-203">Verwenden Sie weiterhin `Newtonsoft.Json`-basierte Formatierer, wenn für die App Folgendes gilt:</span><span class="sxs-lookup"><span data-stu-id="a4f46-203">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="a4f46-204">Sie verwendet `Newtonsoft.Json`-Attribute.</span><span class="sxs-lookup"><span data-stu-id="a4f46-204">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="a4f46-205">Zum Beispiel: `[JsonProperty]` oder `[JsonIgnore]`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-205">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="a4f46-206">Sie passt die Serialisierungseinstellungen an.</span><span class="sxs-lookup"><span data-stu-id="a4f46-206">Customizes the serialization settings.</span></span>
* <span data-ttu-id="a4f46-207">Sie nutzt Features, die von `Newtonsoft.Json` bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-207">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="a4f46-208">`Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-208">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="a4f46-209">Vor ASP.NET Core 3.0 akzeptiert `JsonResult.SerializerSettings` eine Instanz von `JsonSerializerSettings`, die für `Newtonsoft.Json` spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="a4f46-209">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="a4f46-210">die [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>)-Dokumentation generiert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-210">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="a4f46-211">Funktionen für die `Newtonsoft.Json`-basierten Formatierer können mithilfe von `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a4f46-211">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="a4f46-212">Optionen zur Ausgabeserialisierung können aktionsweise mithilfe von `JsonResult` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-212">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="a4f46-213">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a4f46-213">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="a4f46-214">Hinzufügen von Unterstützung für das XML-Format</span><span class="sxs-lookup"><span data-stu-id="a4f46-214">Add XML format support</span></span>

<span data-ttu-id="a4f46-215">Die XML-Formatierung erfordert das NuGet-Paket [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/).</span><span class="sxs-lookup"><span data-stu-id="a4f46-215">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="a4f46-216">XML-Formatierer, die mithilfe von <xref:System.Xml.Serialization.XmlSerializer> implementiert wurden, werden durch Aufruf von <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="a4f46-216">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="a4f46-217">Der oben stehende Code serialisiert Ergebnisse mithilfe von `XmlSerializer`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-217">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="a4f46-218">Bei Verwendung dieses Codes sollten Controllermethoden das geeignete Format basierend auf dem `Accept`-Header der Anforderung zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-218">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="a4f46-219">Angeben eines Formats</span><span class="sxs-lookup"><span data-stu-id="a4f46-219">Specify a format</span></span>

<span data-ttu-id="a4f46-220">Um die Antwortformate einzuschränken, wenden Sie den [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter an.</span><span class="sxs-lookup"><span data-stu-id="a4f46-220">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="a4f46-221">Wie die [](xref:mvc/controllers/filters)meisten Filter `[Produces]` können auch auf Aktion, Controller oder globaler Bereich angewendet werden:</span><span class="sxs-lookup"><span data-stu-id="a4f46-221">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="a4f46-222">Der vorangehende [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) Filter:</span><span class="sxs-lookup"><span data-stu-id="a4f46-222">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="a4f46-223">Er erzwingt die Rückgabe von JSON-formatierten Antworten von allen Aktionen im Controller.</span><span class="sxs-lookup"><span data-stu-id="a4f46-223">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="a4f46-224">Wenn andere Formatierer konfiguriert sind und der Client ein anderes Format angibt, wird JSON zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a4f46-224">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="a4f46-225">Weitere Informationen finden Sie unter [Filter](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="a4f46-225">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="a4f46-226">Formatierer für besondere Fälle</span><span class="sxs-lookup"><span data-stu-id="a4f46-226">Special case formatters</span></span>

<span data-ttu-id="a4f46-227">Einige besondere Fälle werden mithilfe von integrierten Formatierungsprogrammen implementiert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-227">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="a4f46-228">Standardmäßig werden `string`-Rückgabetypen als *text/plain* formatiert (bzw. als *text/html*, wenn sie über den `Accept`-Header angefordert werden).</span><span class="sxs-lookup"><span data-stu-id="a4f46-228">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="a4f46-229">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-229">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="a4f46-230">Formatierer werden in der `ConfigureServices`-Methode entfernt.</span><span class="sxs-lookup"><span data-stu-id="a4f46-230">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="a4f46-231">Aktionen mit einem Modellobjekt-Rückgabetyp geben `null` zurück, wenn der Rückgabewert `204 No Content` lautet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-231">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="a4f46-232">Dieses Verhalten kann durch Entfernen von <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-232">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="a4f46-233">Der folgende Code entfernt `StringOutputFormatter` und `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="a4f46-233">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="a4f46-234">Ohne den `StringOutputFormatter` formatiert der integrierte JSON-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="a4f46-234">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="a4f46-235">Wenn der integrierte JSON-Formatierer entfernt wird und ein XML-Formatierer verfügbar ist, formatiert der XML-Formatierer `string`-Rückgabetypen.</span><span class="sxs-lookup"><span data-stu-id="a4f46-235">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="a4f46-236">Andernfalls geben `string`-Rückgabetypen `406 Not Acceptable` zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-236">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="a4f46-237">Ohne `HttpNoContentOutputFormatter` werden NULL-Objekte mithilfe des konfigurierten Formatierungsprogramms formatiert.</span><span class="sxs-lookup"><span data-stu-id="a4f46-237">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="a4f46-238">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a4f46-238">For example:</span></span>

* <span data-ttu-id="a4f46-239">Der JSON-Formatierer gibt eine Antwort mit dem Text `null` zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-239">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="a4f46-240">Der XML-Formatierer gibt ein leeres XML-Element mit festgelegtem Attribut `xsi:nil="true"` zurück.</span><span class="sxs-lookup"><span data-stu-id="a4f46-240">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="a4f46-241">Zuordnung des Antwortformats durch URLs</span><span class="sxs-lookup"><span data-stu-id="a4f46-241">Response format URL mappings</span></span>

<span data-ttu-id="a4f46-242">Clients können in der URL ein bestimmtes Format anfordern, beispielsweise folgendermaßen:</span><span class="sxs-lookup"><span data-stu-id="a4f46-242">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="a4f46-243">In der Abfragezeichenfolge oder als Teil des Pfads.</span><span class="sxs-lookup"><span data-stu-id="a4f46-243">In the query string or part of the path.</span></span>
* <span data-ttu-id="a4f46-244">Durch Verwendung einer formatspezifischen Dateierweiterung wie „.xml“ oder „.json“.</span><span class="sxs-lookup"><span data-stu-id="a4f46-244">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="a4f46-245">Die Zuordnung des Anforderungspfads sollte in der Route angegeben werden, die die API verwendet.</span><span class="sxs-lookup"><span data-stu-id="a4f46-245">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="a4f46-246">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a4f46-246">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="a4f46-247">Mit der oben genannten Route kann das angeforderte Format als optionale Dateierweiterung angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a4f46-247">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="a4f46-248">Das [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) -Attribut überprüft das vorhanden sein des Format Werts im `RouteData` und ordnet das Antwortformat dem entsprechenden Formatierer zu, wenn die Antwort erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a4f46-248">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="a4f46-249">Route</span><span class="sxs-lookup"><span data-stu-id="a4f46-249">Route</span></span>        |             <span data-ttu-id="a4f46-250">Formatierungsprogramm</span><span class="sxs-lookup"><span data-stu-id="a4f46-250">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="a4f46-251">Standard-Ausgabeformatierungsprogramm</span><span class="sxs-lookup"><span data-stu-id="a4f46-251">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="a4f46-252">JSON-Formatierungsprogramm (falls konfiguriert)</span><span class="sxs-lookup"><span data-stu-id="a4f46-252">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="a4f46-253">XML-Formatierungsprogramm (falls konfiguriert)</span><span class="sxs-lookup"><span data-stu-id="a4f46-253">The XML formatter (if configured)</span></span>  |
