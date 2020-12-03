---
title: Anforderungs- und Antwortvorgänge in ASP.NET Core
author: jkotalik
description: Erfahren Sie, wie Sie in ASP.NET Core den Anforderungstext lesen und den Antworttext schreiben.
monikerRange: '>= aspnetcore-3.0'
ms.author: jukotali
ms.custom: mvc
ms.date: 5/29/2019
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
uid: fundamentals/middleware/request-response
ms.openlocfilehash: 5ad39821778ea58097169def85a940a06f1d036e
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513108"
---
# <a name="request-and-response-operations-in-aspnet-core"></a><span data-ttu-id="b057c-103">Anforderungs- und Antwortvorgänge in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b057c-103">Request and response operations in ASP.NET Core</span></span>

<span data-ttu-id="b057c-104">Von [Justin Kotalik](https://github.com/jkotalik)</span><span class="sxs-lookup"><span data-stu-id="b057c-104">By [Justin Kotalik](https://github.com/jkotalik)</span></span>

<span data-ttu-id="b057c-105">In diesem Artikel wird erläutert, wie Sie den Anforderungstext lesen und den Antworttext schreiben.</span><span class="sxs-lookup"><span data-stu-id="b057c-105">This article explains how to read from the request body and write to the response body.</span></span> <span data-ttu-id="b057c-106">Möglicherweise ist Code für diese Vorgänge erforderlich, wenn Sie Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="b057c-106">Code for these operations might be required when writing middleware.</span></span> <span data-ttu-id="b057c-107">Abgesehen vom Schreiben von Middleware ist benutzerdefinierter Code in der Regel nicht erforderlich, da die Vorgänge von MVC und Razor Pages behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="b057c-107">Outside of writing middleware, custom code isn't generally required because the operations are handled by MVC and Razor Pages.</span></span>

<span data-ttu-id="b057c-108">Es stehen zwei Abstraktionen für die Anforderungs- und Antworttexte zur Verfügung: <xref:System.IO.Stream> und <xref:System.IO.Pipelines.Pipe>.</span><span class="sxs-lookup"><span data-stu-id="b057c-108">There are two abstractions for the request and response bodies: <xref:System.IO.Stream> and <xref:System.IO.Pipelines.Pipe>.</span></span> <span data-ttu-id="b057c-109"><xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> ist eine <xref:System.IO.Stream>-Klasse zum Lesen von Anforderungen, und `HttpRequest.BodyReader` ist eine <xref:System.IO.Pipelines.PipeReader>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="b057c-109">For request reading, <xref:Microsoft.AspNetCore.Http.HttpRequest.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpRequest.BodyReader` is a <xref:System.IO.Pipelines.PipeReader>.</span></span> <span data-ttu-id="b057c-110"><xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> ist eine <xref:System.IO.Stream>-Klasse zum Schreiben von Antworten, und `HttpResponse.BodyWriter` ist eine <xref:System.IO.Pipelines.PipeWriter>-Klasse.</span><span class="sxs-lookup"><span data-stu-id="b057c-110">For response writing, <xref:Microsoft.AspNetCore.Http.HttpResponse.Body?displayProperty=nameWithType> is a <xref:System.IO.Stream>, and `HttpResponse.BodyWriter` is a <xref:System.IO.Pipelines.PipeWriter>.</span></span>

<span data-ttu-id="b057c-111">Anstelle von Datenströmen werden [Pipelines](/dotnet/standard/io/pipelines) empfohlen.</span><span class="sxs-lookup"><span data-stu-id="b057c-111">[Pipelines](/dotnet/standard/io/pipelines) are recommended over streams.</span></span> <span data-ttu-id="b057c-112">Datenströme können bei einigen einfachen Vorgängen einfacher zu verwenden sein, aber Pipelines haben einen Leistungsvorteil und sind in den meisten Szenarien einfacher zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b057c-112">Streams can be easier to use for some simple operations, but pipelines have a performance advantage and are easier to use in most scenarios.</span></span> <span data-ttu-id="b057c-113">ASP.NET Core beginnt, intern Pipelines anstelle von Datenströmen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b057c-113">ASP.NET Core is starting to use pipelines instead of streams internally.</span></span> <span data-ttu-id="b057c-114">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="b057c-114">Examples include:</span></span>

* `FormReader`
* `TextReader`
* `TextWriter`
* `HttpResponse.WriteAsync`

<span data-ttu-id="b057c-115">Datenströme werden jedoch nicht aus dem Framework entfernt.</span><span class="sxs-lookup"><span data-stu-id="b057c-115">Streams aren't being removed from the framework.</span></span> <span data-ttu-id="b057c-116">Datenströme werden weiterhin in .NET verwendet, und für viele Datenstromtypen gibt es keine entsprechenden Pipelines, z. B. `FileStreams` und `ResponseCompression`.</span><span class="sxs-lookup"><span data-stu-id="b057c-116">Streams continue to be used throughout .NET, and many stream types don't have pipe equivalents, such as `FileStreams` and `ResponseCompression`.</span></span>

## <a name="stream-examples"></a><span data-ttu-id="b057c-117">Datenstrombeispiele</span><span class="sxs-lookup"><span data-stu-id="b057c-117">Stream examples</span></span>

<!-- see "fundamentals\middleware\request-response\static\TestPipes.JPG for testing sample -->

<span data-ttu-id="b057c-118">Angenommen, Sie möchten eine Middleware erstellen, die den gesamten Anforderungstext als Liste von Zeichenfolgen liest, die in neue Zeilen umbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="b057c-118">Suppose the goal is to create a middleware that reads the entire request body as a list of strings, splitting on new lines.</span></span> <span data-ttu-id="b057c-119">Eine einfache Datenstromimplementierung könnte wie im folgenden Beispiel aussehen:</span><span class="sxs-lookup"><span data-stu-id="b057c-119">A simple stream implementation might look like the following example:</span></span>

> [!WARNING]
> <span data-ttu-id="b057c-120">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="b057c-120">The following code:</span></span>
> * <span data-ttu-id="b057c-121">Der Code wird verwendet, um die Probleme zu veranschaulichen, bei denen keine Pipe zum Lesen des Anforderungstexts verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="b057c-121">Is used to demonstrate the problems with not using a pipe to read the request body.</span></span>
> * <span data-ttu-id="b057c-122">Der Code ist nicht für die Verwendung in Produktions-Apps vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b057c-122">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStream)]

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="b057c-123">Dieser Code funktioniert, aber es gibt einige Probleme:</span><span class="sxs-lookup"><span data-stu-id="b057c-123">This code works, but there are some issues:</span></span>

* <span data-ttu-id="b057c-124">Vor dem Anfügen an den `StringBuilder` wird im Beispiel eine andere Zeichenfolge erstellt (`encodedString`), die sofort verworfen wird.</span><span class="sxs-lookup"><span data-stu-id="b057c-124">Before appending to the `StringBuilder`, the example creates another string (`encodedString`) that is thrown away immediately.</span></span> <span data-ttu-id="b057c-125">Weil dieser Vorgang für alle Bytes im Datenstrom erfolgt, ist das Ergebnis eine zusätzliche Speicherzuweisung in der Größe des gesamten Anforderungstexts.</span><span class="sxs-lookup"><span data-stu-id="b057c-125">This process occurs for all bytes in the stream, so the result is extra memory allocation the size of the entire request body.</span></span>
* <span data-ttu-id="b057c-126">Im Beispiel wird die gesamte Zeichenfolge vor dem Umbruch in neue Zeilen gelesen.</span><span class="sxs-lookup"><span data-stu-id="b057c-126">The example reads the entire string before splitting on new lines.</span></span> <span data-ttu-id="b057c-127">Es ist viel effizienter, das Bytearray auf neue Zeilen zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="b057c-127">It's more efficient to check for new lines in the byte array.</span></span>

<span data-ttu-id="b057c-128">Es folgt ein Beispiel, in dem einige dieser Probleme behoben werden:</span><span class="sxs-lookup"><span data-stu-id="b057c-128">Here's an example that fixes some of the preceding issues:</span></span>

> [!WARNING]
> <span data-ttu-id="b057c-129">Der folgende Code</span><span class="sxs-lookup"><span data-stu-id="b057c-129">The following code:</span></span>
> * <span data-ttu-id="b057c-130">Der Code wird verwendet, um die Lösungen für einige Probleme im vorangehenden Code zu veranschaulichen, ohne dabei alle Probleme zu lösen.</span><span class="sxs-lookup"><span data-stu-id="b057c-130">Is used to demonstrate the solutions to some problems in the preceding code while not solving all the problems.</span></span>
> * <span data-ttu-id="b057c-131">Der Code ist nicht für die Verwendung in Produktions-Apps vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="b057c-131">Is not intended to be used in production apps.</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringsFromStreamMoreEfficient)]

<span data-ttu-id="b057c-132">Dieses obige Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b057c-132">This preceding example:</span></span>

* <span data-ttu-id="b057c-133">Puffert nicht den gesamten Anforderungstext in einem `StringBuilder`, wenn keine Zeilenvorschubzeichen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="b057c-133">Doesn't buffer the entire request body in a `StringBuilder` unless there aren't any newline characters.</span></span>
* <span data-ttu-id="b057c-134">Ruft nicht `Split` für die Zeichenfolge auf.</span><span class="sxs-lookup"><span data-stu-id="b057c-134">Doesn't call `Split` on the string.</span></span>

<span data-ttu-id="b057c-135">Es gibt jedoch noch ein paar Probleme:</span><span class="sxs-lookup"><span data-stu-id="b057c-135">However, there are still are a few issues:</span></span>

* <span data-ttu-id="b057c-136">Wenn nur wenige neue Zeilenvorschubzeichen vorliegen, wird ein großer Teil des Anforderungstexts in der Zeichenfolge gepuffert.</span><span class="sxs-lookup"><span data-stu-id="b057c-136">If newline characters are sparse, much of the request body is buffered in the string.</span></span>
* <span data-ttu-id="b057c-137">Der Code erstellt weiterhin Zeichenfolgen (`remainingString`) und fügt diese zum Zeichenfolgenpuffer hinzu, was zu einer weiteren Zuteilung führt.</span><span class="sxs-lookup"><span data-stu-id="b057c-137">The code continues to create strings (`remainingString`) and adds them to the string buffer, which results in an extra allocation.</span></span>

<span data-ttu-id="b057c-138">Diese Probleme können behoben werden, aber der Code wird immer komplizierter, und die Verbesserung ist gering.</span><span class="sxs-lookup"><span data-stu-id="b057c-138">These issues are fixable, but the code is becoming progressively more complicated with little improvement.</span></span> <span data-ttu-id="b057c-139">Pipelines bieten eine Möglichkeit, diese Probleme mit minimaler Codekomplexität zu lösen.</span><span class="sxs-lookup"><span data-stu-id="b057c-139">Pipelines provide a way to solve these problems with minimal code complexity.</span></span>

## <a name="pipelines"></a><span data-ttu-id="b057c-140">Pipelines</span><span class="sxs-lookup"><span data-stu-id="b057c-140">Pipelines</span></span>

<span data-ttu-id="b057c-141">Das folgende Beispiel zeigt, wie das gleiche Szenario mit [PipeReader](/dotnet/standard/io/pipelines#pipe) behandelt werden kann:</span><span class="sxs-lookup"><span data-stu-id="b057c-141">The following example shows how the same scenario can be handled using a [PipeReader](/dotnet/standard/io/pipelines#pipe):</span></span>

[!code-csharp[](request-response/samples/3.x/RequestResponseSample/Startup.cs?name=GetListOfStringFromPipe)]

<span data-ttu-id="b057c-142">In diesem Beispiel werden viele Probleme der Datenstromimplementierungen behoben:</span><span class="sxs-lookup"><span data-stu-id="b057c-142">This example fixes many issues that the streams implementations had:</span></span>

* <span data-ttu-id="b057c-143">Ein Zeichenfolgenpuffer ist nicht erforderlich, weil der `PipeReader` Bytes behandelt, die nicht verwendet wurden.</span><span class="sxs-lookup"><span data-stu-id="b057c-143">There's no need for a string buffer because the `PipeReader` handles bytes that haven't been used.</span></span>
* <span data-ttu-id="b057c-144">Codierte Zeichenfolgen werden direkt der Liste der zurückgegebenen Zeichenfolgen hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="b057c-144">Encoded strings are directly added to the list of returned strings.</span></span>
* <span data-ttu-id="b057c-145">Die Erstellung von Zeichenfolgen erfolgt mit Ausnahme des `ToArray`-Aufrufs und des von der Zeichenfolge verwendeten Speichers ohne Zuordnung.</span><span class="sxs-lookup"><span data-stu-id="b057c-145">Other than the `ToArray` call, and the memory used by the string, string creation is allocation free.</span></span>

## <a name="adapters"></a><span data-ttu-id="b057c-146">Adapter</span><span class="sxs-lookup"><span data-stu-id="b057c-146">Adapters</span></span>

<span data-ttu-id="b057c-147">Die Eigenschaften `Body`, `BodyReader` und `BodyWriter` sind für `HttpRequest` und `HttpResponse` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="b057c-147">The `Body`, `BodyReader`, and `BodyWriter` properties are available for `HttpRequest` and `HttpResponse`.</span></span> <span data-ttu-id="b057c-148">Wenn Sie für `Body` einen anderen Datenstrom festlegen, passen neue Adapter die Typen automatisch aneinander an.</span><span class="sxs-lookup"><span data-stu-id="b057c-148">When you set `Body` to a different stream, a new set of adapters automatically adapt each type to the other.</span></span> <span data-ttu-id="b057c-149">Wenn Sie für `HttpRequest.Body` einen neuen Datenstrom festlegen, wird für `HttpRequest.BodyReader` automatisch ein neuer `PipeReader` festgelegt, der `HttpRequest.Body` umschließt.</span><span class="sxs-lookup"><span data-stu-id="b057c-149">If you set `HttpRequest.Body` to a new stream, `HttpRequest.BodyReader` is automatically set to a new `PipeReader` that wraps `HttpRequest.Body`.</span></span>

## <a name="startasync"></a><span data-ttu-id="b057c-150">StartAsync</span><span class="sxs-lookup"><span data-stu-id="b057c-150">StartAsync</span></span>

<span data-ttu-id="b057c-151">`HttpResponse.StartAsync` wird verwendet, um anzugeben, dass Header nicht änderbar sind, und um `OnStarting`-Rückrufe auszuführen.</span><span class="sxs-lookup"><span data-stu-id="b057c-151">`HttpResponse.StartAsync` is used to indicate that headers are unmodifiable and to run `OnStarting` callbacks.</span></span> <span data-ttu-id="b057c-152">Bei der Verwendung von Kestrel als Server garantiert der Aufruf von `StartAsync` vor der Verwendung von `PipeReader`, dass von `GetMemory` zurückgegebener Arbeitsspeicher zu internen <xref:System.IO.Pipelines.Pipe>-Pipeline von Kestrel gehört, anstatt einem externen Puffer.</span><span class="sxs-lookup"><span data-stu-id="b057c-152">When using Kestrel as a server, calling `StartAsync` before using the `PipeReader` guarantees that memory returned by `GetMemory` belongs to Kestrel's internal <xref:System.IO.Pipelines.Pipe> rather than an external buffer.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b057c-153">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b057c-153">Additional resources</span></span>

* [<span data-ttu-id="b057c-154">System.IO.Pipelines in .NET</span><span class="sxs-lookup"><span data-stu-id="b057c-154">System.IO.Pipelines in .NET</span></span>](/dotnet/standard/io/pipelines)
* <xref:fundamentals/middleware/write>

<!-- Test with Postman or other tool. See image in static directory. -->
