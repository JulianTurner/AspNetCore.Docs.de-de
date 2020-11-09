---
title: Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen und Verwenden von benutzerdefinierten Formatierern für Web-APIs in ASP.NET Core.
ms.author: riande
ms.date: 06/25/2020
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
uid: web-api/advanced/custom-formatters
ms.openlocfilehash: e4d73fdc0db3faeace5d68b3d71718315e68cae3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058921"
---
# <a name="custom-formatters-in-aspnet-core-web-api"></a><span data-ttu-id="35f7e-103">Benutzerdefinierte Formatierer in Web-APIs in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35f7e-103">Custom formatters in ASP.NET Core Web API</span></span>

<span data-ttu-id="35f7e-104">Von [Kirk Larkin](https://twitter.com/serpent5) und [Tom Dykstra](https://github.com/tdykstra).</span><span class="sxs-lookup"><span data-stu-id="35f7e-104">By [Kirk Larkin](https://twitter.com/serpent5) and [Tom Dykstra](https://github.com/tdykstra).</span></span>

<span data-ttu-id="35f7e-105">ASP.NET Core MVC unterstützt den Datenaustausch in Web-APIs mithilfe von Eingabe- und Ausgabeformatierern.</span><span class="sxs-lookup"><span data-stu-id="35f7e-105">ASP.NET Core MVC supports data exchange in Web APIs using input and output formatters.</span></span> <span data-ttu-id="35f7e-106">Eingabeformatierer werden von der [Modellbindung](xref:mvc/models/model-binding) verwendet.</span><span class="sxs-lookup"><span data-stu-id="35f7e-106">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="35f7e-107">Ausgabe Formatierer werden verwendet, um [Antworten zu formatieren](xref:web-api/advanced/formatting).</span><span class="sxs-lookup"><span data-stu-id="35f7e-107">Output formatters are used to [format responses](xref:web-api/advanced/formatting).</span></span>

<span data-ttu-id="35f7e-108">Das Framework stellt integrierte Eingabe- und Ausgabeformatierer für JSON und XML bereit.</span><span class="sxs-lookup"><span data-stu-id="35f7e-108">The framework provides built-in input and output formatters for JSON and XML.</span></span> <span data-ttu-id="35f7e-109">Es stellt einen integrierten Ausgabeformatierer für Nur-Text bereit, jedoch keinen Eingabeformatierer.</span><span class="sxs-lookup"><span data-stu-id="35f7e-109">It provides a built-in output formatter for plain text, but doesn't provide an input formatter for plain text.</span></span>

<span data-ttu-id="35f7e-110">In diesem Artikel wird dargestellt, wie Sie die Unterstützung von zusätzlichen Formaten hinzufügen, indem Sie benutzerdefinierte Formatierer erstellen.</span><span class="sxs-lookup"><span data-stu-id="35f7e-110">This article shows how to add support for additional formats by creating custom formatters.</span></span> <span data-ttu-id="35f7e-111">Ein Beispiel für einen benutzerdefinierten Formatierer für die nur-Text-Eingabe finden Sie unter [textplaininputformatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="35f7e-111">For an example of a custom plain text input formatter, see [TextPlainInputFormatter](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.Formatters/TextPlainInputFormatter.cs) on GitHub.</span></span>

<span data-ttu-id="35f7e-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="35f7e-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-custom-formatters"></a><span data-ttu-id="35f7e-113">Empfohlene Verwendung von benutzerdefinierten Formatierern</span><span class="sxs-lookup"><span data-stu-id="35f7e-113">When to use custom formatters</span></span>

<span data-ttu-id="35f7e-114">Verwenden Sie einen benutzerdefinierten Formatierer, um Unterstützung für einen Inhaltstyp hinzuzufügen, der nicht von den integrierten Formatierern verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="35f7e-114">Use a custom formatter to add support for a content type that isn't handled by the built-in formatters.</span></span>

## <a name="overview-of-how-to-use-a-custom-formatter"></a><span data-ttu-id="35f7e-115">Übersicht über die Verwendung des Formatierers</span><span class="sxs-lookup"><span data-stu-id="35f7e-115">Overview of how to use a custom formatter</span></span>

<span data-ttu-id="35f7e-116">So erstellen Sie einen benutzerdefinierten Formatierer:</span><span class="sxs-lookup"><span data-stu-id="35f7e-116">To create a custom formatter:</span></span>

* <span data-ttu-id="35f7e-117">Zum Serialisieren von Daten, die an den Client gesendet werden, erstellen Sie eine Ausgabe formatterklasse.</span><span class="sxs-lookup"><span data-stu-id="35f7e-117">For serializing data sent to the client, create an output formatter class.</span></span>
* <span data-ttu-id="35f7e-118">Zum Deserialisieren von Daten, die vom Client empfangen werden, erstellen Sie eine eingabeformatterklasse.</span><span class="sxs-lookup"><span data-stu-id="35f7e-118">For deserializing data received from the client, create an input formatter class.</span></span>
* <span data-ttu-id="35f7e-119">Fügen Sie den-und-Auflistungen in Instanzen von formatiererklassen hinzu `InputFormatters` `OutputFormatters` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="35f7e-119">Add instances of formatter classes to the `InputFormatters` and `OutputFormatters` collections in <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

## <a name="how-to-create-a-custom-formatter-class"></a><span data-ttu-id="35f7e-120">Erstellen einer benutzerdefinierten Formatiererklasse</span><span class="sxs-lookup"><span data-stu-id="35f7e-120">How to create a custom formatter class</span></span>

<span data-ttu-id="35f7e-121">Erstellen eines Formatierers:</span><span class="sxs-lookup"><span data-stu-id="35f7e-121">To create a formatter:</span></span>

* <span data-ttu-id="35f7e-122">Leiten Sie die Klasse von der entsprechenden Basisklasse ab.</span><span class="sxs-lookup"><span data-stu-id="35f7e-122">Derive the class from the appropriate base class.</span></span> <span data-ttu-id="35f7e-123">Die Beispiel-APP wird von <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> und abgeleitet <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="35f7e-123">The sample app derives from <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> and <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter>.</span></span>
* <span data-ttu-id="35f7e-124">Geben Sie gültige Medientypen und Codierungen im Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="35f7e-124">Specify valid media types and encodings in the constructor.</span></span>
* <span data-ttu-id="35f7e-125">Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A>- und <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A>-Methoden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-125">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.CanReadType%2A> and <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter.CanWriteType%2A> methods.</span></span>
* <span data-ttu-id="35f7e-126">Überschreiben Sie die <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A>- und `WriteResponseBodyAsync`-Methoden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-126">Override the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter.ReadRequestBodyAsync%2A> and `WriteResponseBodyAsync` methods.</span></span>

<span data-ttu-id="35f7e-127">Der folgende Code zeigt die- `VcardOutputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="35f7e-127">The following code shows the `VcardOutputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_Class)]
  
### <a name="derive-from-the-appropriate-base-class"></a><span data-ttu-id="35f7e-128">Ableiten von der entsprechenden Basisklasse</span><span class="sxs-lookup"><span data-stu-id="35f7e-128">Derive from the appropriate base class</span></span>

<span data-ttu-id="35f7e-129">Leiten Sie für Text Medientypen (z. b. vCard) von der- <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> oder- <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> Basisklasse ab.</span><span class="sxs-lookup"><span data-stu-id="35f7e-129">For text media types (for example, vCard), derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.TextInputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.TextOutputFormatter> base class.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ClassDeclaration)]

<span data-ttu-id="35f7e-130">Leiten Sie für binäre Typen von der- <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> oder- <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> Basisklasse ab.</span><span class="sxs-lookup"><span data-stu-id="35f7e-130">For binary types, derive from the <xref:Microsoft.AspNetCore.Mvc.Formatters.InputFormatter> or <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatter> base class.</span></span>

### <a name="specify-valid-media-types-and-encodings"></a><span data-ttu-id="35f7e-131">Angeben von gültigen Medientypen und Codierungen</span><span class="sxs-lookup"><span data-stu-id="35f7e-131">Specify valid media types and encodings</span></span>

<span data-ttu-id="35f7e-132">Geben Sie im Konstruktor gültige Medientypen und Codierungen an, indem Sie `SupportedMediaTypes`- und `SupportedEncodings`-Sammlungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="35f7e-132">In the constructor, specify valid media types and encodings by adding to the `SupportedMediaTypes` and `SupportedEncodings` collections.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_ctor)]

<span data-ttu-id="35f7e-133">Eine Formatiererklasse kann **keine** Konstruktorinjektion für ihre Abhängigkeiten verwenden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-133">A formatter class can **not** use constructor injection for its dependencies.</span></span> <span data-ttu-id="35f7e-134">`ILogger<VcardOutputFormatter>`Kann z. b. nicht als Parameter zum-Konstruktor hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-134">For example, `ILogger<VcardOutputFormatter>` cannot be added as a parameter to the constructor.</span></span> <span data-ttu-id="35f7e-135">Verwenden Sie zum Zugreifen auf Dienste das Kontext Objekt, das an die-Methoden übermittelt wird.</span><span class="sxs-lookup"><span data-stu-id="35f7e-135">To access services, use the context object that gets passed in to the methods.</span></span> <span data-ttu-id="35f7e-136">Ein Codebeispiel in diesem Artikel und das [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) zeigen, wie Sie dies tun.</span><span class="sxs-lookup"><span data-stu-id="35f7e-136">A code example in this article and the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples) show how to do this.</span></span>

### <a name="override-canreadtype-and-canwritetype"></a><span data-ttu-id="35f7e-137">Überschreiben von "canlestype" und "CanWrite Type"</span><span class="sxs-lookup"><span data-stu-id="35f7e-137">Override CanReadType and CanWriteType</span></span>

<span data-ttu-id="35f7e-138">Geben Sie den zu deserialisierenden oder zu serialisierenden Typ durch Überschreiben der- `CanReadType` Methode oder der- `CanWriteType` Methode an</span><span class="sxs-lookup"><span data-stu-id="35f7e-138">Specify the type to deserialize into or serialize from by overriding the `CanReadType` or `CanWriteType` methods.</span></span> <span data-ttu-id="35f7e-139">Beispielsweise das Erstellen von vCard-Text von einem `Contact` Typ und umgekehrt.</span><span class="sxs-lookup"><span data-stu-id="35f7e-139">For example, creating vCard text from a `Contact` type and vice versa.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_CanWriteType)]

#### <a name="the-canwriteresult-method"></a><span data-ttu-id="35f7e-140">Die CanWriteResult-Methode</span><span class="sxs-lookup"><span data-stu-id="35f7e-140">The CanWriteResult method</span></span>

<span data-ttu-id="35f7e-141">In einigen Szenarien `CanWriteResult` muss anstelle von überschrieben werden `CanWriteType` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-141">In some scenarios, `CanWriteResult` must be overridden rather than `CanWriteType`.</span></span> <span data-ttu-id="35f7e-142">Verwenden Sie `CanWriteResult`, wenn alle der folgenden Bedingungen zutreffen:</span><span class="sxs-lookup"><span data-stu-id="35f7e-142">Use `CanWriteResult` if the following conditions are true:</span></span>

* <span data-ttu-id="35f7e-143">Die Aktionsmethode gibt eine Modell Klasse zurück.</span><span class="sxs-lookup"><span data-stu-id="35f7e-143">The action method returns a model class.</span></span>
* <span data-ttu-id="35f7e-144">Es gibt abgeleitete Klassen, die möglicherweise zur Laufzeit zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-144">There are derived classes which might be returned at runtime.</span></span>
* <span data-ttu-id="35f7e-145">Die von der Aktion zurückgegebene abgeleitete Klasse muss zur Laufzeit bekannt sein.</span><span class="sxs-lookup"><span data-stu-id="35f7e-145">The derived class returned by the action must be known at runtime.</span></span>

<span data-ttu-id="35f7e-146">Angenommen, die Aktionsmethode lautet wie folgt:</span><span class="sxs-lookup"><span data-stu-id="35f7e-146">For example, suppose the action method:</span></span>

* <span data-ttu-id="35f7e-147">Signature gibt einen `Person` Typ zurück.</span><span class="sxs-lookup"><span data-stu-id="35f7e-147">Signature returns a `Person` type.</span></span>
* <span data-ttu-id="35f7e-148">Kann einen- `Student` oder-Typ zurückgeben `Instructor` , der von abgeleitet wird `Person` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-148">Can return a `Student` or `Instructor` type that derives from `Person`.</span></span> 

<span data-ttu-id="35f7e-149">Damit der Formatierer nur- `Student` Objekte behandelt, überprüfen Sie den Typ von <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> im Kontext Objekt, das für die-Methode bereitgestellt wird `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-149">For the formatter to handle only `Student` objects, check the type of <xref:Microsoft.AspNetCore.Mvc.Formatters.OutputFormatterCanWriteContext.Object> in the context object provided to the `CanWriteResult` method.</span></span> <span data-ttu-id="35f7e-150">Wenn die Aktionsmethode Folgendes zurückgibt `IActionResult` :</span><span class="sxs-lookup"><span data-stu-id="35f7e-150">When the action method returns `IActionResult`:</span></span>

* <span data-ttu-id="35f7e-151">Es ist nicht erforderlich, zu verwenden `CanWriteResult` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-151">It's not necessary to use `CanWriteResult`.</span></span>
* <span data-ttu-id="35f7e-152">Die- `CanWriteType` Methode empfängt den Lauf Zeittyp.</span><span class="sxs-lookup"><span data-stu-id="35f7e-152">The `CanWriteType` method receives the runtime type.</span></span>

<a id="read-write"></a>

### <a name="override-readrequestbodyasync-and-writeresponsebodyasync"></a><span data-ttu-id="35f7e-153">Überschreiben von "Read requestbodyasync" und "schreiteresponsebodyasync"</span><span class="sxs-lookup"><span data-stu-id="35f7e-153">Override ReadRequestBodyAsync and WriteResponseBodyAsync</span></span>

<span data-ttu-id="35f7e-154">Die Deserialisierung oder Serialisierung wird in `ReadRequestBodyAsync` oder ausgeführt `WriteResponseBodyAsync` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-154">Deserialization or serialization is performed in `ReadRequestBodyAsync` or `WriteResponseBodyAsync`.</span></span> <span data-ttu-id="35f7e-155">Im folgenden Beispiel wird gezeigt, wie Sie Dienste aus dem Container für die Abhängigkeitsinjektion erhalten.</span><span class="sxs-lookup"><span data-stu-id="35f7e-155">The following example shows how to get services from the dependency injection container.</span></span> <span data-ttu-id="35f7e-156">Dienste können nicht aus Konstruktorparametern abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="35f7e-156">Services can't be obtained from constructor parameters.</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardOutputFormatter.cs?name=snippet_WriteResponseBodyAsync)]

## <a name="how-to-configure-mvc-to-use-a-custom-formatter"></a><span data-ttu-id="35f7e-157">Konfigurieren von MVC zum Verwenden eines benutzerdefinierten Formatierers</span><span class="sxs-lookup"><span data-stu-id="35f7e-157">How to configure MVC to use a custom formatter</span></span>

<span data-ttu-id="35f7e-158">Wenn Sie einen benutzerdefinierten Formatierer verwenden, fügen Sie der `InputFormatters`- oder `OutputFormatters`-Sammlung eine Instanz der Formatiererklasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="35f7e-158">To use a custom formatter, add an instance of the formatter class to the `InputFormatters` or `OutputFormatters` collection.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](custom-formatters/samples/2.x/CustomFormattersSample/Startup.cs?name=mvcoptions&highlight=3-4)]

::: moniker-end

<span data-ttu-id="35f7e-159">Formatierer werden in der Reihenfolge überprüft, in der Sie sie einfügen.</span><span class="sxs-lookup"><span data-stu-id="35f7e-159">Formatters are evaluated in the order you insert them.</span></span> <span data-ttu-id="35f7e-160">Der erste hat Vorrang.</span><span class="sxs-lookup"><span data-stu-id="35f7e-160">The first one takes precedence.</span></span>

## <a name="the-complete-vcardinputformatter-class"></a><span data-ttu-id="35f7e-161">Die komplette `VcardInputFormatter` Klasse</span><span class="sxs-lookup"><span data-stu-id="35f7e-161">The complete `VcardInputFormatter` class</span></span>

<span data-ttu-id="35f7e-162">Der folgende Code zeigt die- `VcardInputFormatter` Klasse aus dem [Beispiel](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span><span class="sxs-lookup"><span data-stu-id="35f7e-162">The following code shows the `VcardInputFormatter` class from the [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples):</span></span>

[!code-csharp[](custom-formatters/samples/3.x/CustomFormattersSample/Formatters/VcardInputFormatter.cs?name=snippet_Class)]

## <a name="test-the-app"></a><span data-ttu-id="35f7e-163">Testen der App</span><span class="sxs-lookup"><span data-stu-id="35f7e-163">Test the app</span></span>

<span data-ttu-id="35f7e-164">[Führen Sie die Beispiel-App für diesen Artikel aus](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), der grundlegende vCard-Eingabe-und-Ausgabe Formatierer implementiert.</span><span class="sxs-lookup"><span data-stu-id="35f7e-164">[Run the sample app for this article](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/custom-formatters/samples), which implements basic vCard input and output formatters.</span></span> <span data-ttu-id="35f7e-165">Die APP liest und schreibt vCards ähnlich der folgenden:</span><span class="sxs-lookup"><span data-stu-id="35f7e-165">The app reads and writes vCards similar to the following:</span></span>

```
BEGIN:VCARD
VERSION:2.1
N:Davolio;Nancy
FN:Nancy Davolio
END:VCARD
```

<span data-ttu-id="35f7e-166">Um die vcardausgabe anzuzeigen, führen Sie die APP aus, und senden Sie eine GET-Anforderung mit dem Accept-Header `text/vcard` an `https://localhost:5001/api/contacts` .</span><span class="sxs-lookup"><span data-stu-id="35f7e-166">To see vCard output, run the app and send a Get request with Accept header `text/vcard` to `https://localhost:5001/api/contacts`.</span></span>

<span data-ttu-id="35f7e-167">So fügen Sie der Auflistung von Kontakten im Arbeitsspeicher eine vCard hinzu:</span><span class="sxs-lookup"><span data-stu-id="35f7e-167">To add a vCard to the in-memory collection of contacts:</span></span>

* <span data-ttu-id="35f7e-168">Senden Sie eine `Post` Anforderung an `/api/contacts` mit einem Tool wie postman.</span><span class="sxs-lookup"><span data-stu-id="35f7e-168">Send a `Post` request to `/api/contacts` with a tool like Postman.</span></span>
* <span data-ttu-id="35f7e-169">Legen Sie den Header `Content-Type` auf `text/vcard` fest.</span><span class="sxs-lookup"><span data-stu-id="35f7e-169">Set the `Content-Type` header to `text/vcard`.</span></span>
* <span data-ttu-id="35f7e-170">Legen Sie `vCard` Text im Textkörper fest, wie im vorherigen Beispiel dargestellt.</span><span class="sxs-lookup"><span data-stu-id="35f7e-170">Set `vCard` text in the body, formatted like the preceding example.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="35f7e-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="35f7e-171">Additional resources</span></span>

* <xref:web-api/advanced/formatting>
* <xref:grpc/dotnet-grpc>
