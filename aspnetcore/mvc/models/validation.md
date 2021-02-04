---
title: Modellvalidierung im ASP.NET Core MVC
author: rick-anderson
description: Erfahren Sie mehr über die Modell Validierung in ASP.net Core MVC und Razor Seiten.
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
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
uid: mvc/models/validation
ms.openlocfilehash: d6fa7e4524a8afdc23d4ad46354d9d8b395656a3
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530189"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="78e81-103">Modell Validierung in ASP.net Core MVC und Razor Seiten</span><span class="sxs-lookup"><span data-stu-id="78e81-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="78e81-104">Von [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="78e81-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="78e81-105">In diesem Artikel wird erläutert, wie Benutzereingaben in einer ASP.net Core MVC-oder Pages-APP überprüft werden Razor .</span><span class="sxs-lookup"><span data-stu-id="78e81-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="78e81-106">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="78e81-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="78e81-107">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="78e81-107">Model state</span></span>

<span data-ttu-id="78e81-108">Der Modellstatus stellt Fehler dar, die aus zwei Subsystemen kommen: Modellbindung und Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="78e81-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="78e81-109">Fehler, die von der [Modellbindung](model-binding.md) herrühren, sind im allgemeinen Datenkonvertierungsfehler.</span><span class="sxs-lookup"><span data-stu-id="78e81-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="78e81-110">Beispielsweise wird ein "x" in ein Integerfeld eingegeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="78e81-111">Die Modellvalidierung erfolgt nach der Modellbindung und meldet Fehler, bei denen die Daten nicht den Geschäftsregeln entsprechen.</span><span class="sxs-lookup"><span data-stu-id="78e81-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="78e81-112">Beispielsweise wird eine 0 in einem Feld eingegeben, das eine Bewertung zwischen 1 und 5 erwartet.</span><span class="sxs-lookup"><span data-stu-id="78e81-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="78e81-113">Die Modell Bindung und die Modell Validierung treten vor der Ausführung einer Controller Aktion oder einer Razor pages-Handlermethode auf.</span><span class="sxs-lookup"><span data-stu-id="78e81-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="78e81-114">Bei Web-Apps liegen die Überprüfung von `ModelState.IsValid` und entsprechende Maßnahmen im Verantwortungsbereich der App.</span><span class="sxs-lookup"><span data-stu-id="78e81-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="78e81-115">Web-Apps zeigen die Seite normalerweise mit einer Fehlermeldung erneut an:</span><span class="sxs-lookup"><span data-stu-id="78e81-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="78e81-116">Web-API-Controller müssen `ModelState.IsValid` nicht überprüfen, wenn sie das `[ApiController]`-Attribut haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="78e81-117">In diesem Fall wird eine automatische HTTP 400-Antwort zurückgegeben, die Fehlerdetails beinhaltet, wenn der Modellstatus ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="78e81-118">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="78e81-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="78e81-119">Validierung erneut ausführen</span><span class="sxs-lookup"><span data-stu-id="78e81-119">Rerun validation</span></span>

<span data-ttu-id="78e81-120">Die Validierung erfolgt automatisch, aber ggf. möchten Sie sie manuell wiederholen.</span><span class="sxs-lookup"><span data-stu-id="78e81-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="78e81-121">Eine Szenario wäre, dass Sie einen Wert für eine Eigenschaft berechnen und möchten, dass die Validierung erneut ausgeführt wird, nachdem der berechnete Wert für die Eigenschaft festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="78e81-122">Wenn Sie die Validierung erneut ausführen möchten, rufen Sie wie im Folgenden dargestellt die `TryValidateModel`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="78e81-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="78e81-123">Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="78e81-123">Validation attributes</span></span>

<span data-ttu-id="78e81-124">Mit Validierungsattributen können Sie Validierungsregeln für Modelleigenschaften angeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="78e81-125">Im folgenden Beispiel aus der Beispiel-App sehen Sie eine Modellklasse, die mit Validierungsattributen versehen wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="78e81-126">Das `[ClassicMovie]`-Attribut ist ein benutzerdefiniertes Validierungsattribut; die anderen sind integriert.</span><span class="sxs-lookup"><span data-stu-id="78e81-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="78e81-127">Nicht dargestellt ist `[ClassicMovieWithClientValidator]`.</span><span class="sxs-lookup"><span data-stu-id="78e81-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="78e81-128">`[ClassicMovieWithClientValidator]` zeigt einen alternativen Weg zum Implementieren eines benutzerdefinierten Attributs auf.</span><span class="sxs-lookup"><span data-stu-id="78e81-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="78e81-129">Integrierte Attribute</span><span class="sxs-lookup"><span data-stu-id="78e81-129">Built-in attributes</span></span>

<span data-ttu-id="78e81-130">Im Folgenden sind einige der integrierten Validierungsattribute aufgeführt:</span><span class="sxs-lookup"><span data-stu-id="78e81-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="78e81-131">`[CreditCard]`: Überprüft, ob die Eigenschaft über ein Kreditkartenformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="78e81-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span> <span data-ttu-id="78e81-132">Erfordert [Zusätzliche jQuery-Validierungs Methoden](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span><span class="sxs-lookup"><span data-stu-id="78e81-132">Requires [jQuery Validation Additional Methods](https://cdnjs.cloudflare.com/ajax/libs/jquery-validate/1.19.1/additional-methods.min.js).</span></span>
* <span data-ttu-id="78e81-133">`[Compare]`: Überprüft, ob zwei Eigenschaften in einem Modell stimmen.</span><span class="sxs-lookup"><span data-stu-id="78e81-133">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="78e81-134">`[EmailAddress]`: Überprüft, ob die Eigenschaft ein e-Mail-Format aufweist.</span><span class="sxs-lookup"><span data-stu-id="78e81-134">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="78e81-135">`[Phone]`: Überprüft, ob die Eigenschaft über ein Telefonnummern Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="78e81-135">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="78e81-136">`[Range]`: Überprüft, ob der Eigenschafts Wert innerhalb eines angegebenen Bereichs liegt.</span><span class="sxs-lookup"><span data-stu-id="78e81-136">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="78e81-137">`[RegularExpression]`: Überprüft, ob der Eigenschafts Wert mit einem angegebenen regulären Ausdruck übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="78e81-137">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="78e81-138">`[Required]`: Überprüft, ob das Feld nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-138">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="78e81-139">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [ `[Required]` Attribut](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="78e81-139">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="78e81-140">`[StringLength]`: Überprüft, ob ein Zeichen folgen Eigenschafts Wert eine angegebene Längen Beschränkung nicht überschreitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-140">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="78e81-141">`[Url]`: Überprüft, ob die Eigenschaft ein URL-Format aufweist.</span><span class="sxs-lookup"><span data-stu-id="78e81-141">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="78e81-142">`[Remote]`: Überprüft die Eingabe auf dem Client, indem eine Aktionsmethode auf dem Server aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-142">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="78e81-143">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [ `[Remote]` Attribut](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="78e81-143">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="78e81-144">Im [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)-Namespace finden Sie eine vollständige Liste der Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="78e81-144">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="78e81-145">Fehlermeldungen</span><span class="sxs-lookup"><span data-stu-id="78e81-145">Error messages</span></span>

<span data-ttu-id="78e81-146">Mit Validierungsattributen können Sie die Fehlermeldung angeben, die im Fall einer ungültigen Eingabe angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-146">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="78e81-147">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e81-147">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="78e81-148">Intern rufen die Attribute die Methode `String.Format` mit einem Platzhalter für den Feldnamen und manchmal zusätzliche Platzhalter auf.</span><span class="sxs-lookup"><span data-stu-id="78e81-148">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="78e81-149">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e81-149">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="78e81-150">Bei Anwendung auf eine `Name`-Eigenschaft wäre die Fehlermeldung, die vom vorangehenden Code erstellt wurde, die Folgende: „Name length must be between 6 and 8“ (Die Länge des Namens muss zwischen 6 und 8 Zeichen betragen).</span><span class="sxs-lookup"><span data-stu-id="78e81-150">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="78e81-151">Wenn Sie herausfinden möchten, welche Parameter an die Methode `String.Format` für die Fehlermeldung eines bestimmten Attributs übergeben werden, sehen Sie sich den [DataAnnotations-Quellcode](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations) an.</span><span class="sxs-lookup"><span data-stu-id="78e81-151">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/runtime/tree/master/src/libraries/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="non-nullable-reference-types-and-required-attribute"></a><span data-ttu-id="78e81-152">Verweis Typen und [Required]-Attribut, die keine NULL-Werte zulassen</span><span class="sxs-lookup"><span data-stu-id="78e81-152">Non-nullable reference types and [Required] attribute</span></span>

<span data-ttu-id="78e81-153">Das Validierungssystem behandelt Parameter, die keine NULL-Werte zulassen, oder gebundene Eigenschaften, als hätten Sie ein- `[Required]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-153">The validation system treats non-nullable parameters or bound properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="78e81-154">Durch das [Aktivieren von `Nullable` Kontexten](/dotnet/csharp/nullable-references#nullable-contexts)beginnt MVC implizit mit der Validierung von Eigenschaften oder Parametern, die keine NULL-Werte zulassen, als wäre sie mit dem-Attribut versehen worden `[Required]` .</span><span class="sxs-lookup"><span data-stu-id="78e81-154">By [enabling `Nullable` contexts](/dotnet/csharp/nullable-references#nullable-contexts), MVC implicitly starts validating non-nullable properties or parameters as if they had been attributed with the `[Required]` attribute.</span></span> <span data-ttu-id="78e81-155">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="78e81-155">Consider the following code:</span></span>

```csharp
public class Person
{
    public string Name { get; set; }
}
```

<span data-ttu-id="78e81-156">Wenn die APP mit erstellt wurde `<Nullable>enable</Nullable>` , führt ein fehlender Wert für `Name` in einem JSON-oder Formular-Post zu einem Validierungs Fehler.</span><span class="sxs-lookup"><span data-stu-id="78e81-156">If the app was built with `<Nullable>enable</Nullable>`, a missing value for `Name` in a JSON or form post results in a validation error.</span></span> <span data-ttu-id="78e81-157">Verwenden Sie einen Verweistyp, der NULL-Werte zulässt, damit NULL-Werte oder fehlende Werte für die Eigenschaft angegeben werden können `Name` :</span><span class="sxs-lookup"><span data-stu-id="78e81-157">Use a nullable reference type to allow null or missing values to be specified for the `Name` property:</span></span>

```csharp
public class Person
{
    public string? Name { get; set; }
}
```

<span data-ttu-id="78e81-158">.</span><span class="sxs-lookup"><span data-stu-id="78e81-158">.</span></span> <span data-ttu-id="78e81-159">Dieses Verhalten kann deaktiviert werden, indem <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices` konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-159">This behavior can be disabled by configuring <xref:Microsoft.AspNetCore.Mvc.MvcOptions.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddControllers(options => options.SuppressImplicitRequiredAttributeForNonNullableReferenceTypes = true);
```

### <a name="required-validation-on-the-server"></a><span data-ttu-id="78e81-160">[Required]-Validierung auf dem Server</span><span class="sxs-lookup"><span data-stu-id="78e81-160">[Required] validation on the server</span></span>

<span data-ttu-id="78e81-161">Auf dem Server wird ein erforderlicher Wert als fehlend betrachtet, wenn für eine Eigenschaft NULL festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-161">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="78e81-162">Ein Feld, das keine NULL-Werte zulässt, ist immer gültig, und die Fehlermeldung des `[Required]`-Attributs wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-162">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="78e81-163">Die Modellbindung für eine Eigenschaft, die keine NULL-Werte zulässt, schlägt jedoch möglicherweise fehl, und führt zu einer Fehlermeldung wie `The value '' is invalid` (Der Wert „“ ist ungültig).</span><span class="sxs-lookup"><span data-stu-id="78e81-163">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="78e81-164">Wenn Sie eine benutzerdefinierte Fehlermeldung für die serverseitige Validierung von Typen, die nicht NULL zulassen, angeben möchten, gibt es die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="78e81-164">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="78e81-165">Lassen Sie für das Feld NULL-Werte zu (z. B. `decimal?` statt `decimal`).</span><span class="sxs-lookup"><span data-stu-id="78e81-165">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="78e81-166">[NULL- \<T> Werte](/dotnet/csharp/programming-guide/nullable-types/) zulassen Werttypen werden wie Standardtypen mit NULL-Werten behandelt.</span><span class="sxs-lookup"><span data-stu-id="78e81-166">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="78e81-167">Geben Sie die Standardfehlermeldung an, die von der Modellbindung verwendet werden soll, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-167">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="78e81-168">Weitere Informationen zu Fehlern bei der Modellbindung, für die Sie Standardmeldungen festlegen können, finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="78e81-168">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="78e81-169">[Required]-Validierung auf dem Client</span><span class="sxs-lookup"><span data-stu-id="78e81-169">[Required] validation on the client</span></span>

<span data-ttu-id="78e81-170">Typen und Zeichenfolgen, für die keine NULL-Werte festgelegt werden können, werden auf dem Client anders behandelt wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="78e81-170">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="78e81-171">Auf dem Client:</span><span class="sxs-lookup"><span data-stu-id="78e81-171">On the client:</span></span>

* <span data-ttu-id="78e81-172">Ein Wert gilt nur als vorhanden, wenn eine Eingabe für ihn erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-172">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="78e81-173">Deshalb werden bei der clientseitigen Validierung Typen, für die keine NULL-Werte festgelegt werden können, gleich behandelt wie Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="78e81-173">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="78e81-174">Leerzeichen in einem Zeichenfolgenfeld werden von der jQuery Validation-[Required](https://jqueryvalidation.org/required-method/)-Methode als gültige Eingabe betrachtet.</span><span class="sxs-lookup"><span data-stu-id="78e81-174">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="78e81-175">Die serverseitige Validierung betrachtet ein erforderliches Zeichenfolgenfeld als ungültig, wenn nur Leerzeichen eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="78e81-175">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="78e81-176">Wie bereits gesagt wurde, werden Typen, die keine NULL-Werte zulassen, so behandelt, als hätten sie ein `[Required]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-176">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="78e81-177">Das heißt, die clientseitige Validierung erfolgt, auch wenn Sie das `[Required]`-Attribut nicht anwenden.</span><span class="sxs-lookup"><span data-stu-id="78e81-177">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="78e81-178">Wenn Sie das Attribut jedoch nicht anwenden, erhalten Sie eine Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-178">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="78e81-179">Wenn Sie eine benutzerdefinierte Fehlermeldung angeben möchten, verwenden Sie das Attribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-179">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="78e81-180">[Remote]-Attribut</span><span class="sxs-lookup"><span data-stu-id="78e81-180">[Remote] attribute</span></span>

<span data-ttu-id="78e81-181">Das `[Remote]`-Attribut implementiert die clientseitige Validierung, für die eine Methode auf dem Server aufgerufen werden muss, um zu bestimmen, ob die Feldeingabe gültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-181">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="78e81-182">So muss die App z. B. überprüfen, ob eine Benutzername bereits verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-182">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="78e81-183">So wird die Remotevalidierung implementiert:</span><span class="sxs-lookup"><span data-stu-id="78e81-183">To implement remote validation:</span></span>

1. <span data-ttu-id="78e81-184">Erstellen Sie eine Aktionsmethode für JavaScript, die aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-184">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="78e81-185">Die [Remote](https://jqueryvalidation.org/remote-method/) Methode für die jQuery-Validierung erwartet eine JSON-Antwort:</span><span class="sxs-lookup"><span data-stu-id="78e81-185">The jQuery Validation [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="78e81-186">`true` bedeutet, die Eingabedaten sind gültig.</span><span class="sxs-lookup"><span data-stu-id="78e81-186">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="78e81-187">`false`, `undefined` oder `null` bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-187">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="78e81-188">Verwenden Sie die Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-188">Display the default error message.</span></span>
   * <span data-ttu-id="78e81-189">Jede andere Zeichenfolge bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-189">Any other string means the input is invalid.</span></span> <span data-ttu-id="78e81-190">Verwenden Sie die Zeichenfolge als benutzerdefinierte Fehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-190">Display the string as a custom error message.</span></span>

   <span data-ttu-id="78e81-191">Hier finden Sie ein Beispiel einer Aktionsmethode, die eine benutzerdefinierte Fehlermeldung zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="78e81-191">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="78e81-192">Versehen Sie in der Modellklasse die Eigenschaft mit einem `[Remote]`-Attribut, das auf die Validierungsaktionsmethode zeigt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-192">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="78e81-193">Das `[Remote]`-Attribut ist im `Microsoft.AspNetCore.Mvc`-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="78e81-193">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="78e81-194">Zusätzliche Felder</span><span class="sxs-lookup"><span data-stu-id="78e81-194">Additional fields</span></span>

<span data-ttu-id="78e81-195">Die `AdditionalFields`-Eigenschaft des `[Remote]`-Attributs ermöglicht Ihnen die Validierung von Kombinationen von Feldern für Daten auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="78e81-195">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="78e81-196">Wenn z. B. das `User`-Modell die Eigenschaften `FirstName` und `LastName` hätte, sollten Sie überprüfen, dass kein bereits vorhandener Benutzer diese Namenskombination verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-196">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="78e81-197">Das folgende Beispiel veranschaulicht die Verwendung von `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="78e81-197">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="78e81-198">`AdditionalFields` könnte explizit auf die Zeichenfolgen „FirstName“ und „LastName“ festgelegt werden, aber die Verwendung des [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operators vereinfacht das spätere Refactoring.</span><span class="sxs-lookup"><span data-stu-id="78e81-198">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="78e81-199">Die Aktionsmethode für diese Validierung muss sowohl `firstName`- als auch `lastName`-Argumente akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="78e81-199">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="78e81-200">Wenn der Benutzer einen Vor- oder einen Nachnamen eingibt, führt JavaScript einen Remoteaufruf durch, um zu prüfen, ob dieses Paar Namen bereits verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-200">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="78e81-201">Wenn Sie mindestens zwei weitere Felder überprüfen möchten, geben Sie sie als eine mit Kommas getrennte Liste an.</span><span class="sxs-lookup"><span data-stu-id="78e81-201">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="78e81-202">Wenn Sie z. B. dem Modell eine `MiddleName`-Eigenschaft hinzufügen möchten, legen Sie das `[Remote]`-Attribut wie im folgenden Code veranschaulicht fest:</span><span class="sxs-lookup"><span data-stu-id="78e81-202">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="78e81-203">`AdditionalFields` muss wie alle anderen Attributargumente ein konstanter Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="78e81-203">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="78e81-204">Aus diesem Grund verwenden Sie keine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings) oder rufen <xref:System.String.Join*> auf, um `AdditionalFields` zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-204">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="78e81-205">Alternativen zu integrierten Attributen</span><span class="sxs-lookup"><span data-stu-id="78e81-205">Alternatives to built-in attributes</span></span>

<span data-ttu-id="78e81-206">Wenn Sie eine Validierung benötigen, die nicht von integrierten Attributen bereitgestellt wird, haben Sie die folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="78e81-206">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="78e81-207">[Erstellen Sie benutzerdefinierte Attribute.](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="78e81-207">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="78e81-208">[Implementieren Sie IValidatableObject.](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="78e81-208">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="78e81-209">Benutzerdefinierte Attribute</span><span class="sxs-lookup"><span data-stu-id="78e81-209">Custom attributes</span></span>

<span data-ttu-id="78e81-210">Für Szenarios, die die integrierten Validierungsattribute nicht verarbeiten können, können Sie benutzerdefinierte Validierungsattribute erstellen.</span><span class="sxs-lookup"><span data-stu-id="78e81-210">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="78e81-211">Erstellen Sie eine Klasse, die von <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> erbt, und überschreiben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="78e81-211">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="78e81-212">Die `IsValid`-Methode akzeptiert ein Objekt namens *value*. Dies ist die Eingabe, die überprüft werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-212">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="78e81-213">Ein Überladen akzeptiert auch ein `ValidationContext`-Objekt, das zusätzliche Informationen bereitstellt, z. B. die Modellinstanz, die von der Modellbindung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-213">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="78e81-214">Im folgenden Beispiel wird überprüft, ob das Veröffentlichungsdatum eines Films aus dem Genre *Classic* (Klassiker) zeitlich nicht hinter einer angegebenen Jahreszahl liegt.</span><span class="sxs-lookup"><span data-stu-id="78e81-214">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="78e81-215">Das `[ClassicMovie]`-Attribut:</span><span class="sxs-lookup"><span data-stu-id="78e81-215">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="78e81-216">wird nur auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="78e81-216">Is only run on the server.</span></span>
* <span data-ttu-id="78e81-217">Für Filmklassiker wird das Veröffentlichungsdatum überprüft:</span><span class="sxs-lookup"><span data-stu-id="78e81-217">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="78e81-218">Die obenstehende `movie`-Variable stellt ein `Movie`-Objekt dar, das die Daten der Formularübermittlung enthält.</span><span class="sxs-lookup"><span data-stu-id="78e81-218">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="78e81-219">Schlägt die Validierung fehl, wird die Klasse `ValidationResult` mit einer Fehlermeldung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-219">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="78e81-220">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="78e81-220">IValidatableObject</span></span>

<span data-ttu-id="78e81-221">Im vorherigen Beispiel wurde nur mit `Movie`-Typen gearbeitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-221">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="78e81-222">Eine andere Option zur Validierung auf Klassenebene ist die Implementierung von `IValidatableObject` in der Modellklasse, wie im folgenden Beispiel dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-222">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="78e81-223">Validierung der Knoten auf oberster Ebene</span><span class="sxs-lookup"><span data-stu-id="78e81-223">Top-level node validation</span></span>

<span data-ttu-id="78e81-224">Knoten der obersten Ebene sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="78e81-224">Top-level nodes include:</span></span>

* <span data-ttu-id="78e81-225">Aktionsparameter</span><span class="sxs-lookup"><span data-stu-id="78e81-225">Action parameters</span></span>
* <span data-ttu-id="78e81-226">Controllereigenschaften</span><span class="sxs-lookup"><span data-stu-id="78e81-226">Controller properties</span></span>
* <span data-ttu-id="78e81-227">Seitenhandlerparameter</span><span class="sxs-lookup"><span data-stu-id="78e81-227">Page handler parameters</span></span>
* <span data-ttu-id="78e81-228">Seitenmodelleigenschaften</span><span class="sxs-lookup"><span data-stu-id="78e81-228">Page model properties</span></span>

<span data-ttu-id="78e81-229">An ein Modell gebundene Knoten auf oberster Ebene und Modelleigenschaften werden überprüft.</span><span class="sxs-lookup"><span data-stu-id="78e81-229">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="78e81-230">Im folgenden Beispiel aus der Beispiel-App verwendet die `VerifyPhone`-Methode die <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>-Klasse, um die `phone`-Aktionsparameter zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="78e81-230">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="78e81-231">Knoten auf oberster Ebene können <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> mit Validierungsattributen verwenden.</span><span class="sxs-lookup"><span data-stu-id="78e81-231">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="78e81-232">Im folgenden Beispiel aus der Beispiel-App gibt die `CheckAge`-Methode an, dass der `age`-Parameter aus der Abfragezeichenfolge gebunden sein muss, wenn das Formular übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-232">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="78e81-233">Auf der Seite für die Altersprüfung (*CheckAge.cshtml*) sind zwei Formulare vorhanden.</span><span class="sxs-lookup"><span data-stu-id="78e81-233">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="78e81-234">Das erste Formular übermittelt einen `Age`-Wert von `99` als Abfragezeichenfolgen-Parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="78e81-234">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="78e81-235">Wenn ein ordnungsgemäß formatierter `age`-Parameter aus der Abfragezeichenfolge übermittelt wird, wird das Formular überprüft.</span><span class="sxs-lookup"><span data-stu-id="78e81-235">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="78e81-236">Das zweite Formular auf der Seite für die Altersprüfung übermittelt den `Age`-Wert im Anforderungstext, worauf die Validierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="78e81-236">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="78e81-237">Die Bindung schlägt fehl, da der `age`-Parameter aus einer Abfragezeichenfolge stammen muss.</span><span class="sxs-lookup"><span data-stu-id="78e81-237">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="78e81-238">Maximale Fehleranzahl</span><span class="sxs-lookup"><span data-stu-id="78e81-238">Maximum errors</span></span>

<span data-ttu-id="78e81-239">Die Validierung stoppt, wenn die maximale Anzahl an Fehlern erreicht wird. Diese liegt standardmäßig bei 200.</span><span class="sxs-lookup"><span data-stu-id="78e81-239">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="78e81-240">Sie können diese Zahl mit dem folgenden Code in `Startup.ConfigureServices` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="78e81-240">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="78e81-241">Maximale Rekursion</span><span class="sxs-lookup"><span data-stu-id="78e81-241">Maximum recursion</span></span>

<span data-ttu-id="78e81-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> durchläuft den Objektgraph des Modells, das überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-242"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="78e81-243">Bei Modellen, die umfassend oder unendlich rekursiv sind, führt die Validierung möglicherweise zu einem Stapelüberlauf.</span><span class="sxs-lookup"><span data-stu-id="78e81-243">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="78e81-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) stellt eine Möglichkeit dar, die Validierung frühzeitig zu stoppen, wenn die Besucherrekursion eine konfigurierte Tiefe überschreitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-244">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="78e81-245">Der Standardwert von `MvcOptions.MaxValidationDepth` ist „32“.</span><span class="sxs-lookup"><span data-stu-id="78e81-245">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="78e81-246">Automatischer Kurzschluss</span><span class="sxs-lookup"><span data-stu-id="78e81-246">Automatic short-circuit</span></span>

<span data-ttu-id="78e81-247">Die Validierung wird automatisch kurzgeschlossen (übersprungen), wenn der Modellgraph keine Validierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="78e81-247">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="78e81-248">Objekte, deren Validierung durch die Runtime übersprungen wird, beinhalten Primitivsammlungen (z. B. `byte[]`, `string[]`, `Dictionary<string, string>`) und komplexe Objektgraphen, die keine Validierungssteuerelemente haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-248">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="78e81-249">Validierung deaktivieren</span><span class="sxs-lookup"><span data-stu-id="78e81-249">Disable validation</span></span>

<span data-ttu-id="78e81-250">So deaktivieren Sie die Validierung:</span><span class="sxs-lookup"><span data-stu-id="78e81-250">To disable validation:</span></span>

1. <span data-ttu-id="78e81-251">Erstellen Sie eine Implementierung von `IObjectModelValidator`, die keine Felder als ungültig kennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="78e81-251">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="78e81-252">Fügen Sie `Startup.ConfigureServices` den folgenden Code hinzu, sodass die Standardimplementierung von `IObjectModelValidator` im Abhängigkeitsinjektionscontainer ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-252">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="78e81-253">Möglicherweise werden weiterhin Modellstatusfehler angezeigt, die ihren Ursprung in der Modellbindung haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-253">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="78e81-254">Clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-254">Client-side validation</span></span>

<span data-ttu-id="78e81-255">Die Überprüfung auf Clientseite verhindert die Übermittlung solange, bis das Formular gültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-255">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="78e81-256">Die Schaltfläche „Übermitteln“ führt JavaScript aus, das entweder das Formular übermittelt oder Fehlermeldungen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-256">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="78e81-257">Die clientseitige Validierung umgeht einen unnötigen Roundtrip zum Server, wenn es in einem Formular Eingabefehler gibt.</span><span class="sxs-lookup"><span data-stu-id="78e81-257">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="78e81-258">Die folgenden Skriptverweise in *_Layout.cshtml* und *_ValidationScriptsPartial.cshtml* unterstützen die clientseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="78e81-258">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="78e81-259">Das [unaufdringliche jQuery-Validierungs](https://github.com/aspnet/jquery-validation-unobtrusive) Skript ist eine benutzerdefinierte Front-End-Bibliothek von Microsoft, die auf dem beliebten [jQuery-Validierungs](https://jqueryvalidation.org/) -Plug-in aufbaut</span><span class="sxs-lookup"><span data-stu-id="78e81-259">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validation](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="78e81-260">Ohne jQuery Unobtrusive Validation müssten Sie dieselbe Validierungslogik an zwei unterschiedlichen Stellen codieren: einmal in den Attributen der Validierung auf Serverseite für Modelleigenschaften und einmal in den Skripts auf Clientseite.</span><span class="sxs-lookup"><span data-stu-id="78e81-260">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="78e81-261">Stattdessen verwenden die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und die [HTML-Hilfsprogramme](xref:mvc/views/overview) die Validierungsattribute und Typmetadaten aus den Modelleigenschaften, um HTML 5-Attribute des Typs `data-` in den Formularelementen zu rendern, die validiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="78e81-261">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="78e81-262">die unaufdringliche jquery-Validierung analysiert die `data-` Attribute und übergibt die Logik an die jQuery-Validierung, wodurch die serverseitige Validierungs Logik auf den Client kopiert wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-262">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validation, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="78e81-263">Sie können Validierungsfehler im Client anzeigen, indem Sie die relevanten Taghilfsprogramme wie folgt verwenden:</span><span class="sxs-lookup"><span data-stu-id="78e81-263">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="78e81-264">Die vorangegangenen Taghilfsprogramme rendern die folgende HTML:</span><span class="sxs-lookup"><span data-stu-id="78e81-264">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="78e81-265">Beachten Sie, dass die `data-`-Attribute in der HTML-Ausgabe mit den Validierungsattributen für die `Movie.ReleaseDate`-Eigenschaft übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="78e81-265">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="78e81-266">Das `data-val-required`-Attribut enthält eine Fehlermeldung, die angezeigt wird, wenn der Benutzer das Datumsfeld für die Veröffentlichung nicht ausfüllt.</span><span class="sxs-lookup"><span data-stu-id="78e81-266">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="78e81-267">die unaufdringliche jquery-Validierung übergibt diesen Wert an die Methode "jQuery Validation [Required ()](https://jqueryvalidation.org/required-method/) ", die diese Meldung dann im zugehörigen **\<span>** Element anzeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-267">jQuery Unobtrusive Validation passes this value to the jQuery Validation [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="78e81-268">Die Datentypvalidierung basiert auf dem .NET-Typ einer Eigenschaft, es sei denn, dieser wird von einem `[DataType]`-Attribut überschrieben.</span><span class="sxs-lookup"><span data-stu-id="78e81-268">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="78e81-269">Browser haben ihre eigenen Standardfehlermeldungen, aber das jQuery Validation Unobtrusive Validation-Paket kann diese Meldungen überschreiben.</span><span class="sxs-lookup"><span data-stu-id="78e81-269">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="78e81-270">Mithilfe von `[DataType]`-Attributen und Subklassen wie `[EmailAddress]` können Sie die Fehlermeldung angeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-270">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="78e81-271">Unaufdringliche Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-271">Unobtrusive validation</span></span>

<span data-ttu-id="78e81-272">Informationen zur unaufdringlichen Validierung finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span><span class="sxs-lookup"><span data-stu-id="78e81-272">For information on unobtrusive validation, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="78e81-273">Hinzufügen der Validierung zu dynamischen Formularen</span><span class="sxs-lookup"><span data-stu-id="78e81-273">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="78e81-274">die unaufdringliche jquery-Validierung übergibt beim ersten Laden der Seite Validierungs Logik und Parameter an die jQuery-Validierung.</span><span class="sxs-lookup"><span data-stu-id="78e81-274">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validation when the page first loads.</span></span> <span data-ttu-id="78e81-275">Deshalb funktioniert die Validierung nicht automatisch bei dynamisch generierten Formularen.</span><span class="sxs-lookup"><span data-stu-id="78e81-275">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="78e81-276">Wenn Sie die Validierung aktivieren möchten, müssen Sie jQuery Unobtrusive Validation auffordern, das dynamische Formular direkt nach dem Erstellen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-276">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="78e81-277">Beispielweise wird im nachfolgenden Code dargestellt, wie Sie die Validierung auf Clientseite für ein Formular einrichten können, das über AJAX hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-277">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="78e81-278">Die `$.validator.unobtrusive.parse()`-Methode akzeptiert einen jQuery-Selektor für ihr einziges Argument.</span><span class="sxs-lookup"><span data-stu-id="78e81-278">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="78e81-279">Diese Methode fordert „jQuery Unobtrusive Validation“ auf, die `data-`-Attribute von Formularen in diesem Selektor zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-279">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="78e81-280">Die Werte dieser Attribute werden dann an das jQuery-Validierungs-Plug-in übermittelt.</span><span class="sxs-lookup"><span data-stu-id="78e81-280">The values of those attributes are then passed to the jQuery Validation plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="78e81-281">Hinzufügen der Validierung zu dynamischen Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="78e81-281">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="78e81-282">Die `$.validator.unobtrusive.parse()`-Methode funktioniert für ein gesamtes Formular, nicht für individuelle, dynamisch erstellte Steuerelemente wie `<input>` und `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="78e81-282">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="78e81-283">Wenn das Formular erneut analysiert werden soll, entfernen Sie die Validierungsdaten, die bei der vorherigen Analyse des Formulars hinzugefügt wurden, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-283">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validation
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="78e81-284">Benutzerdefinierte clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-284">Custom client-side validation</span></span>

<span data-ttu-id="78e81-285">Die benutzerdefinierte Client seitige Validierung erfolgt durch das Erstellen von `data-` HTML-Attributen, die mit einem benutzerdefinierten jQuery-Validierungs Adapter funktionieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-285">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validation adapter.</span></span> <span data-ttu-id="78e81-286">Der folgende Beispieladaptercode wurde für die `[ClassicMovie]`- und `[ClassicMovieWithClientValidator]`-Attribute geschrieben, die zuvor in diesem Artikel bereits eingeführt wurden:</span><span class="sxs-lookup"><span data-stu-id="78e81-286">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="78e81-287">Weitere Informationen zum Schreiben von Adaptern finden Sie in der [Dokumentation zur jquery-Validierung](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="78e81-287">For information about how to write adapters, see the [jQuery Validation documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="78e81-288">Die Verwendung eines Adapters für ein bestimmtes Feld wird von `data-`-Attributen ausgelöst, die Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="78e81-288">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="78e81-289">Das Feld wird so gekennzeichnet, dass es validiert wird (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="78e81-289">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="78e81-290">Ein Name für die Validierungsregel und ein Text für die Fehlermeldung werden bestimmt (z. B. `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="78e81-290">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="78e81-291">Zusätzliche Parameter werden bereitgestellt, die vom Validierungssteuerelement benötigt werden (z. B. `data-val-rulename-param1="value"`).</span><span class="sxs-lookup"><span data-stu-id="78e81-291">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="78e81-292">Im folgenden Beispiel sehen Sie die `data-`-Attribute des `ClassicMovie`-Attributs der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="78e81-292">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="78e81-293">Wie bereits gesagt, verwenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und [HTML-Hilfsprogramme](xref:mvc/views/overview) Informationen der Validierungsattribute, um `data-`-Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="78e81-293">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="78e81-294">Es gibt zwei Möglichkeiten, Code zu schreiben, der zur Erstellung von benutzerdefinierten HTML-Attributen des Typs `data-` führt:</span><span class="sxs-lookup"><span data-stu-id="78e81-294">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="78e81-295">Erstellen einer Klasse, die von `AttributeAdapterBase<TAttribute>` ableitet, und einer Klasse, die `IValidationAttributeAdapterProvider` implementiert, und Registrieren Ihres Attributs und des dazugehörigen Adapters in der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="78e81-295">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="78e81-296">Diese Methode folgt dem [Single-Responsibility-Prinzip](https://wikipedia.org/wiki/Single_responsibility_principle), nach dem sich der serverbezogene und der clientbezogene Validierungscode in unterschiedlichen Klassen befinden.</span><span class="sxs-lookup"><span data-stu-id="78e81-296">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="78e81-297">Der Adapter bietet außerdem den Vorteil, dass andere Dienste in der Abhängigkeitsinjektion bei Bedarf zur Verfügung stehen, da der Adapter in der Abhängigkeitsinjektion registriert ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-297">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="78e81-298">Implementieren von `IClientModelValidator` in Ihrer `ValidationAttribute`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="78e81-298">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="78e81-299">Diese Methode ist geeignet, wenn das Attribut für keinerlei serverseitige Validierung zuständig ist und keine Dienste aus der Abhängigkeitsinjektion benötigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-299">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="78e81-300">AttributeAdapter für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-300">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="78e81-301">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-301">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="78e81-302">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="78e81-302">To add client validation by using this method:</span></span>

1. <span data-ttu-id="78e81-303">Erstellen Sie eine Attributadapterklasse für das benutzerdefinierte Validierungsattribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-303">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="78e81-304">Leiten Sie Klasse aus <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>ab.</span><span class="sxs-lookup"><span data-stu-id="78e81-304">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="78e81-305">Erstellen Sie eine `AddValidation`-Methode, die der gerenderten Ausgabe `data-`-Attribute hinzufügt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-305">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="78e81-306">Erstellen Sie eine Adapteranbieterklasse, die <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> implementiert.</span><span class="sxs-lookup"><span data-stu-id="78e81-306">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="78e81-307">Übergeben Sie in der `GetAttributeAdapter`-Methode dem Konstruktor des Adapters das benutzerdefinierte Attribut, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-307">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="78e81-308">Registrieren Sie den Adapteranbieter für die Abhängigkeitsinjektion in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="78e81-308">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="78e81-309">IClientModelValidator für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-309">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="78e81-310">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovieWithClientValidator`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-310">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="78e81-311">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="78e81-311">To add client validation by using this method:</span></span>

* <span data-ttu-id="78e81-312">Implementieren Sie im benutzerdefinierten Validierungsattribut die `IClientModelValidator`-Schnittstelle, und erstellen Sie eine `AddValidation`-Methode.</span><span class="sxs-lookup"><span data-stu-id="78e81-312">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="78e81-313">Fügen Sie in der `AddValidation`-Methode die `data-`-Attribute für die Validierung hinzu, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-313">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="78e81-314">Deaktivieren der clientseitigen Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-314">Disable client-side validation</span></span>

<span data-ttu-id="78e81-315">Der folgende Code deaktiviert die Client Validierung in Razor Seiten:</span><span class="sxs-lookup"><span data-stu-id="78e81-315">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="78e81-316">Weitere Optionen zum Deaktivieren der clientseitigen Validierung:</span><span class="sxs-lookup"><span data-stu-id="78e81-316">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="78e81-317">Kommentieren Sie den Verweis auf `_ValidationScriptsPartial` in allen *CSHTML*-Dateien aus.</span><span class="sxs-lookup"><span data-stu-id="78e81-317">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="78e81-318">Entfernen Sie den Inhalt der *Pages\Shared\_ValidationScriptsPartial.cshtml*-Datei.</span><span class="sxs-lookup"><span data-stu-id="78e81-318">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="78e81-319">Der vorherige Ansatz verhindert nicht die Client seitige Validierung der ASP.NET Core Identity Razor Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="78e81-319">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="78e81-320">Weitere Informationen finden Sie unter <xref:security/authentication/scaffold-identity>.</span><span class="sxs-lookup"><span data-stu-id="78e81-320">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e81-321">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78e81-321">Additional resources</span></span>

* [<span data-ttu-id="78e81-322">System.ComponentModel.DataAnnotations-Namespace</span><span class="sxs-lookup"><span data-stu-id="78e81-322">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="78e81-323">Modell Bindung</span><span class="sxs-lookup"><span data-stu-id="78e81-323">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="78e81-324">In diesem Artikel wird erläutert, wie Benutzereingaben in einer ASP.net Core MVC-oder Pages-APP überprüft werden Razor .</span><span class="sxs-lookup"><span data-stu-id="78e81-324">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="78e81-325">[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="78e81-325">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="78e81-326">Modellstatus</span><span class="sxs-lookup"><span data-stu-id="78e81-326">Model state</span></span>

<span data-ttu-id="78e81-327">Der Modellstatus stellt Fehler dar, die aus zwei Subsystemen kommen: Modellbindung und Modellvalidierung.</span><span class="sxs-lookup"><span data-stu-id="78e81-327">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="78e81-328">Fehler, die Ihren Ursprung bei der [Modellbindung](model-binding.md) haben, sind normalerweise Fehler bei der Datenkonvertierung, z. B. wenn in ein Feld ein „x“ eingegeben wird, das einen Integer erwartet.</span><span class="sxs-lookup"><span data-stu-id="78e81-328">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="78e81-329">Die Modellvalidierung erfolgt nach der Modellbindung und meldet Fehler, wenn die Daten in Konflikt mit den Geschäftsregeln stehen, z. B. wenn in ein Feld eine 0 eingegeben wird, das eine Bewertung zwischen 1 und 5 erwartet.</span><span class="sxs-lookup"><span data-stu-id="78e81-329">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="78e81-330">Die Modell Bindung und-Validierung erfolgt vor der Ausführung einer Controller Aktion oder einer Razor pages-Handlermethode.</span><span class="sxs-lookup"><span data-stu-id="78e81-330">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="78e81-331">Bei Web-Apps liegen die Überprüfung von `ModelState.IsValid` und entsprechende Maßnahmen im Verantwortungsbereich der App.</span><span class="sxs-lookup"><span data-stu-id="78e81-331">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="78e81-332">Web-Apps zeigen die Seite normalerweise mit einer Fehlermeldung erneut an:</span><span class="sxs-lookup"><span data-stu-id="78e81-332">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="78e81-333">Web-API-Controller müssen `ModelState.IsValid` nicht überprüfen, wenn sie das `[ApiController]`-Attribut haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-333">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="78e81-334">In diesem Fall wird eine automatische HTTP 400-Antwort zurückgegeben, die Fehlerdetails beinhaltet, wenn der Modellstatus ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-334">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="78e81-335">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="78e81-335">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="78e81-336">Validierung erneut ausführen</span><span class="sxs-lookup"><span data-stu-id="78e81-336">Rerun validation</span></span>

<span data-ttu-id="78e81-337">Die Validierung erfolgt automatisch, aber ggf. möchten Sie sie manuell wiederholen.</span><span class="sxs-lookup"><span data-stu-id="78e81-337">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="78e81-338">Eine Szenario wäre, dass Sie einen Wert für eine Eigenschaft berechnen und möchten, dass die Validierung erneut ausgeführt wird, nachdem der berechnete Wert für die Eigenschaft festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-338">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="78e81-339">Wenn Sie die Validierung erneut ausführen möchten, rufen Sie wie im Folgenden dargestellt die `TryValidateModel`-Methode auf:</span><span class="sxs-lookup"><span data-stu-id="78e81-339">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="78e81-340">Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="78e81-340">Validation attributes</span></span>

<span data-ttu-id="78e81-341">Mit Validierungsattributen können Sie Validierungsregeln für Modelleigenschaften angeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-341">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="78e81-342">Im folgenden Beispiel aus der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) sehen Sie eine Modellklasse, die mit Validierungsattributen versehen wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-342">The following example from [the sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="78e81-343">Das `[ClassicMovie]`-Attribut ist ein benutzerdefiniertes Validierungsattribut; die anderen sind integriert.</span><span class="sxs-lookup"><span data-stu-id="78e81-343">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="78e81-344">Nicht gezeigt wird hier das Attribut `[ClassicMovie2]`, das eine Alternative darstellt, ein benutzerdefiniertes Attribut zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-344">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="78e81-345">Integrierte Attribute</span><span class="sxs-lookup"><span data-stu-id="78e81-345">Built-in attributes</span></span>

<span data-ttu-id="78e81-346">Zu den integrierten Validierungsattributen gehören:</span><span class="sxs-lookup"><span data-stu-id="78e81-346">Built-in validation attributes include:</span></span>

* <span data-ttu-id="78e81-347">`[CreditCard]`: Überprüft, ob die Eigenschaft über ein Kreditkartenformat verfügt.</span><span class="sxs-lookup"><span data-stu-id="78e81-347">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="78e81-348">`[Compare]`: Überprüft, ob zwei Eigenschaften in einem Modell stimmen.</span><span class="sxs-lookup"><span data-stu-id="78e81-348">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="78e81-349">Beispielsweise verwendet die *Register.cshtml.cs*-Datei `[Compare]`, um zu prüfen, ob die beiden eingegebenen Kennwörter übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="78e81-349">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="78e81-350">[Gerüst Identity ](xref:security/authentication/scaffold-identity) , um den Registrierungscode anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="78e81-350">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="78e81-351">`[EmailAddress]`: Überprüft, ob die Eigenschaft ein e-Mail-Format aufweist.</span><span class="sxs-lookup"><span data-stu-id="78e81-351">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="78e81-352">`[Phone]`: Überprüft, ob die Eigenschaft über ein Telefonnummern Format verfügt.</span><span class="sxs-lookup"><span data-stu-id="78e81-352">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="78e81-353">`[Range]`: Überprüft, ob der Eigenschafts Wert innerhalb eines angegebenen Bereichs liegt.</span><span class="sxs-lookup"><span data-stu-id="78e81-353">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="78e81-354">`[RegularExpression]`: Überprüft, ob der Eigenschafts Wert mit einem angegebenen regulären Ausdruck übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="78e81-354">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="78e81-355">`[Required]`: Überprüft, ob das Feld nicht NULL ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-355">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="78e81-356">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [ `[Required]` Attribut](#required-attribute) .</span><span class="sxs-lookup"><span data-stu-id="78e81-356">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="78e81-357">`[StringLength]`: Überprüft, ob ein Zeichen folgen Eigenschafts Wert eine angegebene Längen Beschränkung nicht überschreitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-357">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="78e81-358">`[Url]`: Überprüft, ob die Eigenschaft ein URL-Format aufweist.</span><span class="sxs-lookup"><span data-stu-id="78e81-358">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="78e81-359">`[Remote]`: Überprüft die Eingabe auf dem Client, indem eine Aktionsmethode auf dem Server aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-359">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="78e81-360">Weitere Informationen zum Verhalten dieses Attributs finden Sie unter [ `[Remote]` Attribut](#remote-attribute) .</span><span class="sxs-lookup"><span data-stu-id="78e81-360">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="78e81-361">Wenn das `[RegularExpression]`-Attribut mit der clientseitigen Validierung verwendet wird, wird der Regex in JavaScript auf dem Client ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="78e81-361">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="78e81-362">Dies bedeutet, dass ein mit [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) übereinstimmendes Verhalten verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-362">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="78e81-363">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/corefx/issues/42487).</span><span class="sxs-lookup"><span data-stu-id="78e81-363">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="78e81-364">Im [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations)-Namespace finden Sie eine vollständige Liste der Validierungsattribute.</span><span class="sxs-lookup"><span data-stu-id="78e81-364">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="78e81-365">Fehlermeldungen</span><span class="sxs-lookup"><span data-stu-id="78e81-365">Error messages</span></span>

<span data-ttu-id="78e81-366">Mit Validierungsattributen können Sie die Fehlermeldung angeben, die im Fall einer ungültigen Eingabe angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-366">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="78e81-367">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e81-367">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="78e81-368">Intern rufen die Attribute die Methode `String.Format` mit einem Platzhalter für den Feldnamen und manchmal zusätzliche Platzhalter auf.</span><span class="sxs-lookup"><span data-stu-id="78e81-368">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="78e81-369">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="78e81-369">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="78e81-370">Bei Anwendung auf eine `Name`-Eigenschaft wäre die Fehlermeldung, die vom vorangehenden Code erstellt wurde, die Folgende: „Name length must be between 6 and 8“ (Die Länge des Namens muss zwischen 6 und 8 Zeichen betragen).</span><span class="sxs-lookup"><span data-stu-id="78e81-370">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="78e81-371">Wenn Sie herausfinden möchten, welche Parameter an die Methode `String.Format` für die Fehlermeldung eines bestimmten Attributs übergeben werden, sehen Sie sich den [DataAnnotations-Quellcode](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations) an.</span><span class="sxs-lookup"><span data-stu-id="78e81-371">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="78e81-372">[Required]-Attribut</span><span class="sxs-lookup"><span data-stu-id="78e81-372">[Required] attribute</span></span>

<span data-ttu-id="78e81-373">Standardmäßig behandelt das Validierungssystem Parameter oder Eigenschaften, die keine NULL-Werte zulassen, wie wenn diese ein `[Required]`-Attribut hätten.</span><span class="sxs-lookup"><span data-stu-id="78e81-373">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="78e81-374">[Werttypen](/dotnet/csharp/language-reference/keywords/value-types) wie `decimal` und `int` lassen keine NULL-Werte zu.</span><span class="sxs-lookup"><span data-stu-id="78e81-374">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="78e81-375">[Required]-Validierung auf dem Server</span><span class="sxs-lookup"><span data-stu-id="78e81-375">[Required] validation on the server</span></span>

<span data-ttu-id="78e81-376">Auf dem Server wird ein erforderlicher Wert als fehlend betrachtet, wenn für eine Eigenschaft NULL festgelegt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-376">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="78e81-377">Ein Feld, das keine NULL-Werte zulässt, ist immer gültig, und die Fehlermeldung des [Required]-Attributs wird nie angezeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-377">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="78e81-378">Die Modellbindung für eine Eigenschaft, die keine NULL-Werte zulässt, schlägt jedoch möglicherweise fehl, und führt zu einer Fehlermeldung wie `The value '' is invalid` (Der Wert „“ ist ungültig).</span><span class="sxs-lookup"><span data-stu-id="78e81-378">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="78e81-379">Wenn Sie eine benutzerdefinierte Fehlermeldung für die serverseitige Validierung von Typen, die nicht NULL zulassen, angeben möchten, gibt es die folgenden Optionen:</span><span class="sxs-lookup"><span data-stu-id="78e81-379">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="78e81-380">Lassen Sie für das Feld NULL-Werte zu (z. B. `decimal?` statt `decimal`).</span><span class="sxs-lookup"><span data-stu-id="78e81-380">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="78e81-381">[NULL- \<T> Werte](/dotnet/csharp/programming-guide/nullable-types/) zulassen Werttypen werden wie Standardtypen mit NULL-Werten behandelt.</span><span class="sxs-lookup"><span data-stu-id="78e81-381">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="78e81-382">Geben Sie die Standardfehlermeldung an, die von der Modellbindung verwendet werden soll, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-382">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="78e81-383">Weitere Informationen zu Fehlern bei der Modellbindung, für die Sie Standardmeldungen festlegen können, finden Sie unter <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span><span class="sxs-lookup"><span data-stu-id="78e81-383">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="78e81-384">[Required]-Validierung auf dem Client</span><span class="sxs-lookup"><span data-stu-id="78e81-384">[Required] validation on the client</span></span>

<span data-ttu-id="78e81-385">Typen und Zeichenfolgen, für die keine NULL-Werte festgelegt werden können, werden auf dem Client anders behandelt wie auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="78e81-385">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="78e81-386">Auf dem Client:</span><span class="sxs-lookup"><span data-stu-id="78e81-386">On the client:</span></span>

* <span data-ttu-id="78e81-387">Ein Wert gilt nur als vorhanden, wenn eine Eingabe für ihn erfolgt ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-387">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="78e81-388">Deshalb werden bei der clientseitigen Validierung Typen, für die keine NULL-Werte festgelegt werden können, gleich behandelt wie Nullable-Typen.</span><span class="sxs-lookup"><span data-stu-id="78e81-388">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="78e81-389">Leerzeichen in einem Zeichenfolgenfeld werden von der jQuery Validation-[Required](https://jqueryvalidation.org/required-method/)-Methode als gültige Eingabe betrachtet.</span><span class="sxs-lookup"><span data-stu-id="78e81-389">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="78e81-390">Die serverseitige Validierung betrachtet ein erforderliches Zeichenfolgenfeld als ungültig, wenn nur Leerzeichen eingegeben werden.</span><span class="sxs-lookup"><span data-stu-id="78e81-390">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="78e81-391">Wie bereits gesagt wurde, werden Typen, die keine NULL-Werte zulassen, so behandelt, als hätten sie ein `[Required]`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-391">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="78e81-392">Das heißt, die clientseitige Validierung erfolgt, auch wenn Sie das `[Required]`-Attribut nicht anwenden.</span><span class="sxs-lookup"><span data-stu-id="78e81-392">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="78e81-393">Wenn Sie das Attribut jedoch nicht anwenden, erhalten Sie eine Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-393">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="78e81-394">Wenn Sie eine benutzerdefinierte Fehlermeldung angeben möchten, verwenden Sie das Attribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-394">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="78e81-395">[Remote]-Attribut</span><span class="sxs-lookup"><span data-stu-id="78e81-395">[Remote] attribute</span></span>

<span data-ttu-id="78e81-396">Das `[Remote]`-Attribut implementiert die clientseitige Validierung, für die eine Methode auf dem Server aufgerufen werden muss, um zu bestimmen, ob die Feldeingabe gültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-396">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="78e81-397">So muss die App z. B. überprüfen, ob eine Benutzername bereits verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-397">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="78e81-398">So wird die Remotevalidierung implementiert:</span><span class="sxs-lookup"><span data-stu-id="78e81-398">To implement remote validation:</span></span>

1. <span data-ttu-id="78e81-399">Erstellen Sie eine Aktionsmethode für JavaScript, die aufgerufen werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-399">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="78e81-400">Die jQuery Validate-[Remote](https://jqueryvalidation.org/remote-method/)-Methode erwartet eine JSON-Antwort:</span><span class="sxs-lookup"><span data-stu-id="78e81-400">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="78e81-401">`"true"` bedeutet, die Eingabedaten sind gültig.</span><span class="sxs-lookup"><span data-stu-id="78e81-401">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="78e81-402">`"false"`, `undefined` oder `null` bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-402">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="78e81-403">Verwenden Sie die Standardfehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-403">Display the default error message.</span></span>
   * <span data-ttu-id="78e81-404">Jede andere Zeichenfolge bedeutet, dass die Eingabe ungültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-404">Any other string means the input is invalid.</span></span> <span data-ttu-id="78e81-405">Verwenden Sie die Zeichenfolge als benutzerdefinierte Fehlermeldung.</span><span class="sxs-lookup"><span data-stu-id="78e81-405">Display the string as a custom error message.</span></span>

   <span data-ttu-id="78e81-406">Hier finden Sie ein Beispiel einer Aktionsmethode, die eine benutzerdefinierte Fehlermeldung zurückgibt:</span><span class="sxs-lookup"><span data-stu-id="78e81-406">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="78e81-407">Versehen Sie in der Modellklasse die Eigenschaft mit einem `[Remote]`-Attribut, das auf die Validierungsaktionsmethode zeigt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-407">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="78e81-408">Das `[Remote]`-Attribut ist im `Microsoft.AspNetCore.Mvc`-Namespace enthalten.</span><span class="sxs-lookup"><span data-stu-id="78e81-408">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="78e81-409">Wenn Sie nicht das Metapaket `Microsoft.AspNetCore.App` oder `Microsoft.AspNetCore.All` verwenden, installieren Sie das NuGet-Paket [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span><span class="sxs-lookup"><span data-stu-id="78e81-409">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="78e81-410">Zusätzliche Felder</span><span class="sxs-lookup"><span data-stu-id="78e81-410">Additional fields</span></span>

<span data-ttu-id="78e81-411">Die `AdditionalFields`-Eigenschaft des `[Remote]`-Attributs ermöglicht Ihnen die Validierung von Kombinationen von Feldern für Daten auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="78e81-411">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="78e81-412">Wenn z. B. das `User`-Modell die Eigenschaften `FirstName` und `LastName` hätte, sollten Sie überprüfen, dass kein bereits vorhandener Benutzer diese Namenskombination verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-412">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="78e81-413">Das folgende Beispiel veranschaulicht die Verwendung von `AdditionalFields`:</span><span class="sxs-lookup"><span data-stu-id="78e81-413">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="78e81-414">`AdditionalFields` könnte explizit auf die Zeichenfolgen `"FirstName"` und `"LastName"` festgelegt werden, aber die Verwendung des [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operators vereinfacht das spätere Refactoring.</span><span class="sxs-lookup"><span data-stu-id="78e81-414">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="78e81-415">Die Aktionsmethode für diese Validierung muss sowohl Vor- als auch Nachnamensargumente akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="78e81-415">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="78e81-416">Wenn der Benutzer einen Vor- oder einen Nachnamen eingibt, führt JavaScript einen Remoteaufruf durch, um zu prüfen, ob dieses Paar Namen bereits verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-416">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="78e81-417">Wenn Sie mindestens zwei weitere Felder überprüfen möchten, geben Sie sie als eine mit Kommas getrennte Liste an.</span><span class="sxs-lookup"><span data-stu-id="78e81-417">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="78e81-418">Wenn Sie z. B. dem Modell eine `MiddleName`-Eigenschaft hinzufügen möchten, legen Sie das `[Remote]`-Attribut wie im folgenden Code veranschaulicht fest:</span><span class="sxs-lookup"><span data-stu-id="78e81-418">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```csharp
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="78e81-419">`AdditionalFields` muss wie alle anderen Attributargumente ein konstanter Ausdruck sein.</span><span class="sxs-lookup"><span data-stu-id="78e81-419">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="78e81-420">Aus diesem Grund verwenden Sie keine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings) oder rufen <xref:System.String.Join*> auf, um `AdditionalFields` zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-420">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="78e81-421">Alternativen zu integrierten Attributen</span><span class="sxs-lookup"><span data-stu-id="78e81-421">Alternatives to built-in attributes</span></span>

<span data-ttu-id="78e81-422">Wenn Sie eine Validierung benötigen, die nicht von integrierten Attributen bereitgestellt wird, haben Sie die folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="78e81-422">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="78e81-423">[Erstellen Sie benutzerdefinierte Attribute.](#custom-attributes)</span><span class="sxs-lookup"><span data-stu-id="78e81-423">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="78e81-424">[Implementieren Sie IValidatableObject.](#ivalidatableobject)</span><span class="sxs-lookup"><span data-stu-id="78e81-424">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="78e81-425">Benutzerdefinierte Attribute</span><span class="sxs-lookup"><span data-stu-id="78e81-425">Custom attributes</span></span>

<span data-ttu-id="78e81-426">Für Szenarios, die die integrierten Validierungsattribute nicht verarbeiten können, können Sie benutzerdefinierte Validierungsattribute erstellen.</span><span class="sxs-lookup"><span data-stu-id="78e81-426">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="78e81-427">Erstellen Sie eine Klasse, die von <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> erbt, und überschreiben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*>-Methode.</span><span class="sxs-lookup"><span data-stu-id="78e81-427">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="78e81-428">Die `IsValid`-Methode akzeptiert ein Objekt namens *value*. Dies ist die Eingabe, die überprüft werden soll.</span><span class="sxs-lookup"><span data-stu-id="78e81-428">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="78e81-429">Ein Überladen akzeptiert auch ein `ValidationContext`-Objekt, das zusätzliche Informationen bereitstellt, z. B. die Modellinstanz, die von der Modellbindung erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-429">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="78e81-430">Im folgenden Beispiel wird überprüft, ob das Veröffentlichungsdatum eines Films aus dem Genre *Classic* (Klassiker) zeitlich nicht hinter einer angegebenen Jahreszahl liegt.</span><span class="sxs-lookup"><span data-stu-id="78e81-430">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="78e81-431">Das `[ClassicMovie2]`-Attribut prüft zuerst das Genre und fährt nur fort, wenn als Genre *Classic* bestimmt werden kann.</span><span class="sxs-lookup"><span data-stu-id="78e81-431">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="78e81-432">Für Filme, die als Klassiker erkannt wurden, prüft es das Veröffentlichungsdatum, um dafür zu sorgen, dass der Film nicht älter ist als eine Jahresgrenze, die an den Attributkonstruktor übergeben wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-432">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="78e81-433">Die obenstehende `movie`-Variable stellt ein `Movie`-Objekt dar, das die Daten der Formularübermittlung enthält.</span><span class="sxs-lookup"><span data-stu-id="78e81-433">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="78e81-434">Die `IsValid`-Methode prüft das Datum und das Genre.</span><span class="sxs-lookup"><span data-stu-id="78e81-434">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="78e81-435">Ist die Validierung erfolgreich, gibt `IsValid` einen `ValidationResult.Success`-Code zurück.</span><span class="sxs-lookup"><span data-stu-id="78e81-435">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="78e81-436">Schlägt die Validierung fehl, wird die Klasse `ValidationResult` mit einer Fehlermeldung zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-436">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="78e81-437">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="78e81-437">IValidatableObject</span></span>

<span data-ttu-id="78e81-438">Im vorherigen Beispiel wurde nur mit `Movie`-Typen gearbeitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-438">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="78e81-439">Eine andere Option zur Validierung auf Klassenebene ist die Implementierung von `IValidatableObject` in der Modellklasse, wie im folgenden Beispiel dargestellt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-439">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="78e81-440">Validierung der Knoten auf oberster Ebene</span><span class="sxs-lookup"><span data-stu-id="78e81-440">Top-level node validation</span></span>

<span data-ttu-id="78e81-441">Knoten der obersten Ebene sind unter anderem:</span><span class="sxs-lookup"><span data-stu-id="78e81-441">Top-level nodes include:</span></span>

* <span data-ttu-id="78e81-442">Aktionsparameter</span><span class="sxs-lookup"><span data-stu-id="78e81-442">Action parameters</span></span>
* <span data-ttu-id="78e81-443">Controllereigenschaften</span><span class="sxs-lookup"><span data-stu-id="78e81-443">Controller properties</span></span>
* <span data-ttu-id="78e81-444">Seitenhandlerparameter</span><span class="sxs-lookup"><span data-stu-id="78e81-444">Page handler parameters</span></span>
* <span data-ttu-id="78e81-445">Seitenmodelleigenschaften</span><span class="sxs-lookup"><span data-stu-id="78e81-445">Page model properties</span></span>

<span data-ttu-id="78e81-446">An ein Modell gebundene Knoten auf oberster Ebene und Modelleigenschaften werden überprüft.</span><span class="sxs-lookup"><span data-stu-id="78e81-446">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="78e81-447">Im folgenden Beispiel aus der Beispiel-App verwendet die `VerifyPhone`-Methode die <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute>-Klasse, um die `phone`-Aktionsparameter zu überprüfen:</span><span class="sxs-lookup"><span data-stu-id="78e81-447">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="78e81-448">Knoten auf oberster Ebene können <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> mit Validierungsattributen verwenden.</span><span class="sxs-lookup"><span data-stu-id="78e81-448">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="78e81-449">Im folgenden Beispiel aus der Beispiel-App gibt die `CheckAge`-Methode an, dass der `age`-Parameter aus der Abfragezeichenfolge gebunden sein muss, wenn das Formular übermittelt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-449">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="78e81-450">Auf der Seite für die Altersprüfung (*CheckAge.cshtml*) sind zwei Formulare vorhanden.</span><span class="sxs-lookup"><span data-stu-id="78e81-450">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="78e81-451">Das erste Formular übermittelt einen `Age`-Wert von `99` als Abfragezeichenfolge: `https://localhost:5001/Users/CheckAge?Age=99`.</span><span class="sxs-lookup"><span data-stu-id="78e81-451">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="78e81-452">Wenn ein ordnungsgemäß formatierter `age`-Parameter aus der Abfragezeichenfolge übermittelt wird, wird das Formular überprüft.</span><span class="sxs-lookup"><span data-stu-id="78e81-452">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="78e81-453">Das zweite Formular auf der Seite für die Altersprüfung übermittelt den `Age`-Wert im Anforderungstext, worauf die Validierung fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="78e81-453">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="78e81-454">Die Bindung schlägt fehl, da der `age`-Parameter aus einer Abfragezeichenfolge stammen muss.</span><span class="sxs-lookup"><span data-stu-id="78e81-454">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="78e81-455">Bei Ausführung mit der Kompatibilitätsversion `CompatibilityVersion.Version_2_1` oder höher, ist die Knotenvalidierung der obersten Eben standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e81-455">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="78e81-456">Andernfalls ist die Knotenvalidierung der obersten Ebene deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="78e81-456">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="78e81-457">Die Standardoption kann überschrieben werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> in (`Startup.ConfigureServices`) festgelegt wird. Sehen Sie sich dazu das folgende Beispiel an:</span><span class="sxs-lookup"><span data-stu-id="78e81-457">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="78e81-458">Maximale Fehleranzahl</span><span class="sxs-lookup"><span data-stu-id="78e81-458">Maximum errors</span></span>

<span data-ttu-id="78e81-459">Die Validierung stoppt, wenn die maximale Anzahl an Fehlern erreicht wird. Diese liegt standardmäßig bei 200.</span><span class="sxs-lookup"><span data-stu-id="78e81-459">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="78e81-460">Sie können diese Zahl mit dem folgenden Code in `Startup.ConfigureServices` konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="78e81-460">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="78e81-461">Maximale Rekursion</span><span class="sxs-lookup"><span data-stu-id="78e81-461">Maximum recursion</span></span>

<span data-ttu-id="78e81-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> durchläuft den Objektgraph des Modells, das überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-462"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="78e81-463">Bei Modellen, die sehr umfassend oder unendlich rekursiv sind, führt die Validierung möglicherweise zu einem Stapelüberlauf.</span><span class="sxs-lookup"><span data-stu-id="78e81-463">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="78e81-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) stellt eine Möglichkeit dar, die Validierung frühzeitig zu stoppen, wenn die Besucherrekursion eine konfigurierte Tiefe überschreitet.</span><span class="sxs-lookup"><span data-stu-id="78e81-464">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="78e81-465">Der Standardwert von `MvcOptions.MaxValidationDepth` ist bei Ausführung mit `CompatibilityVersion.Version_2_2` oder höher „32“.</span><span class="sxs-lookup"><span data-stu-id="78e81-465">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="78e81-466">Bei früheren Versionen ist der Wert NULL, d.h. es gibt keine Tiefeneinschränkung.</span><span class="sxs-lookup"><span data-stu-id="78e81-466">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="78e81-467">Automatischer Kurzschluss</span><span class="sxs-lookup"><span data-stu-id="78e81-467">Automatic short-circuit</span></span>

<span data-ttu-id="78e81-468">Die Validierung wird automatisch kurzgeschlossen (übersprungen), wenn der Modellgraph keine Validierung erfordert.</span><span class="sxs-lookup"><span data-stu-id="78e81-468">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="78e81-469">Objekte, deren Validierung durch die Runtime übersprungen wird, beinhalten Primitivsammlungen (z. B. `byte[]`, `string[]`, `Dictionary<string, string>`) und komplexe Objektgraphen, die keine Validierungssteuerelemente haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-469">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="78e81-470">Validierung deaktivieren</span><span class="sxs-lookup"><span data-stu-id="78e81-470">Disable validation</span></span>

<span data-ttu-id="78e81-471">So deaktivieren Sie die Validierung:</span><span class="sxs-lookup"><span data-stu-id="78e81-471">To disable validation:</span></span>

1. <span data-ttu-id="78e81-472">Erstellen Sie eine Implementierung von `IObjectModelValidator`, die keine Felder als ungültig kennzeichnet.</span><span class="sxs-lookup"><span data-stu-id="78e81-472">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="78e81-473">Fügen Sie `Startup.ConfigureServices` den folgenden Code hinzu, sodass die Standardimplementierung von `IObjectModelValidator` im Abhängigkeitsinjektionscontainer ersetzt wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-473">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="78e81-474">Möglicherweise werden weiterhin Modellstatusfehler angezeigt, die ihren Ursprung in der Modellbindung haben.</span><span class="sxs-lookup"><span data-stu-id="78e81-474">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="78e81-475">Clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-475">Client-side validation</span></span>

<span data-ttu-id="78e81-476">Die Überprüfung auf Clientseite verhindert die Übermittlung solange, bis das Formular gültig ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-476">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="78e81-477">Die Schaltfläche „Übermitteln“ führt JavaScript aus, das entweder das Formular übermittelt oder Fehlermeldungen anzeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-477">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="78e81-478">Die clientseitige Validierung umgeht einen unnötigen Roundtrip zum Server, wenn es in einem Formular Eingabefehler gibt.</span><span class="sxs-lookup"><span data-stu-id="78e81-478">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="78e81-479">Die folgenden Skriptverweise in *_Layout.cshtml* und *_ValidationScriptsPartial.cshtml* unterstützen die clientseitige Validierung:</span><span class="sxs-lookup"><span data-stu-id="78e81-479">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="78e81-480">Bei dem [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive)-Skript handelt es sich um eine benutzerdefinierte Front-End-Bibliothek von Microsoft, die auf dem beliebten [jQuery Validate](https://jqueryvalidation.org/)-Plugin basiert.</span><span class="sxs-lookup"><span data-stu-id="78e81-480">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="78e81-481">Ohne jQuery Unobtrusive Validation müssten Sie dieselbe Validierungslogik an zwei unterschiedlichen Stellen codieren: einmal in den Attributen der Validierung auf Serverseite für Modelleigenschaften und einmal in den Skripts auf Clientseite.</span><span class="sxs-lookup"><span data-stu-id="78e81-481">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="78e81-482">Stattdessen verwenden die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und die [HTML-Hilfsprogramme](xref:mvc/views/overview) die Validierungsattribute und Typmetadaten aus den Modelleigenschaften, um HTML 5-Attribute des Typs `data-` in den Formularelementen zu rendern, die validiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="78e81-482">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="78e81-483">jQuery Unobtrusive Validation analysiert dann diese `data-`-Attribute und übergibt die Logik an jQuery Validate, wodurch die Logik der Validierung auf Serverseite in den Client „kopiert“ wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-483">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="78e81-484">Sie können Validierungsfehler im Client anzeigen, indem Sie die relevanten Taghilfsprogramme wie folgt verwenden:</span><span class="sxs-lookup"><span data-stu-id="78e81-484">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="78e81-485">Die vorangegangenen Taghilfsprogramme rendern die folgende HTML.</span><span class="sxs-lookup"><span data-stu-id="78e81-485">The preceding tag helpers render the following HTML.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="78e81-486">Beachten Sie, dass die `data-`-Attribute in der HTML-Ausgabe mit den Validierungsattributen für die `ReleaseDate`-Eigenschaft übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="78e81-486">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="78e81-487">Das `data-val-required`-Attribut enthält eine Fehlermeldung, die angezeigt wird, wenn der Benutzer das Datumsfeld für die Veröffentlichung nicht ausfüllt.</span><span class="sxs-lookup"><span data-stu-id="78e81-487">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="78e81-488">die unaufdringliche jquery-Validierung übergibt diesen Wert an die jQuery Validate [Required ()](https://jqueryvalidation.org/required-method/) -Methode, die diese Meldung dann im zugehörigen- **\<span>** Element anzeigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-488">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="78e81-489">Die Datentypvalidierung basiert auf dem .NET-Typ einer Eigenschaft, es sei denn, dieser wird von einem `[DataType]`-Attribut überschrieben.</span><span class="sxs-lookup"><span data-stu-id="78e81-489">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="78e81-490">Browser haben ihre eigenen Standardfehlermeldungen, aber das jQuery Validation Unobtrusive Validation-Paket kann diese Meldungen überschreiben.</span><span class="sxs-lookup"><span data-stu-id="78e81-490">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="78e81-491">Mithilfe von `[DataType]`-Attributen und Subklassen wie `[EmailAddress]` können Sie die Fehlermeldung angeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-491">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="78e81-492">Hinzufügen der Validierung zu dynamischen Formularen</span><span class="sxs-lookup"><span data-stu-id="78e81-492">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="78e81-493">jQuery Unobtrusive Validation übergibt die Validierungslogik und -parameter an jQuery Validate, wenn die Seite das erste Mal geladen wird.</span><span class="sxs-lookup"><span data-stu-id="78e81-493">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="78e81-494">Deshalb funktioniert die Validierung nicht automatisch bei dynamisch generierten Formularen.</span><span class="sxs-lookup"><span data-stu-id="78e81-494">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="78e81-495">Wenn Sie die Validierung aktivieren möchten, müssen Sie jQuery Unobtrusive Validation auffordern, das dynamische Formular direkt nach dem Erstellen zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-495">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="78e81-496">Beispielweise wird im nachfolgenden Code dargestellt, wie Sie die Validierung auf Clientseite für ein Formular einrichten können, das über AJAX hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="78e81-496">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="78e81-497">Die `$.validator.unobtrusive.parse()`-Methode akzeptiert einen jQuery-Selektor für ihr einziges Argument.</span><span class="sxs-lookup"><span data-stu-id="78e81-497">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="78e81-498">Diese Methode fordert „jQuery Unobtrusive Validation“ auf, die `data-`-Attribute von Formularen in diesem Selektor zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-498">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="78e81-499">Die Werte diese Attribute werden dann an das jQuery Validate-Plugin übergeben.</span><span class="sxs-lookup"><span data-stu-id="78e81-499">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="78e81-500">Hinzufügen der Validierung zu dynamischen Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="78e81-500">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="78e81-501">Die `$.validator.unobtrusive.parse()`-Methode funktioniert für ein gesamtes Formular, nicht für individuelle, dynamisch erstellte Steuerelemente wie `<input>` und `<select/>`.</span><span class="sxs-lookup"><span data-stu-id="78e81-501">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="78e81-502">Wenn das Formular erneut analysiert werden soll, entfernen Sie die Validierungsdaten, die bei der vorherigen Analyse des Formulars hinzugefügt wurden, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-502">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

```javascript
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="custom-client-side-validation"></a><span data-ttu-id="78e81-503">Benutzerdefinierte clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-503">Custom client-side validation</span></span>

<span data-ttu-id="78e81-504">Die benutzerdefinierte clientseitige Validierung erfolgt über die Generierung von HTML-Attributen des Typs `data-`, die mit einem benutzerdefinierten jQuery Validate-Adapter funktionieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-504">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="78e81-505">Der folgende Beispieladaptercode wurde für die `ClassicMovie`- und `ClassicMovie2`-Attribute geschrieben, die zuvor in diesem Artikel bereits eingeführt wurden:</span><span class="sxs-lookup"><span data-stu-id="78e81-505">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="78e81-506">Informationen zum Schreiben dieser Adapter finden Sie in der [jQuery Validate-Dokumentation](https://jqueryvalidation.org/documentation/).</span><span class="sxs-lookup"><span data-stu-id="78e81-506">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="78e81-507">Die Verwendung eines Adapters für ein bestimmtes Feld wird von `data-`-Attributen ausgelöst, die Folgendes tun:</span><span class="sxs-lookup"><span data-stu-id="78e81-507">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="78e81-508">Das Feld wird so gekennzeichnet, dass es validiert wird (`data-val="true"`).</span><span class="sxs-lookup"><span data-stu-id="78e81-508">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="78e81-509">Ein Name für die Validierungsregel und ein Text für die Fehlermeldung werden bestimmt (z. B. `data-val-rulename="Error message."`).</span><span class="sxs-lookup"><span data-stu-id="78e81-509">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="78e81-510">Zusätzliche Parameter werden bereitgestellt, die vom Validierungssteuerelement benötigt werden (z. B. `data-val-rulename-parm1="value"`).</span><span class="sxs-lookup"><span data-stu-id="78e81-510">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="78e81-511">Im folgenden Beispiel sehen Sie die `data-`-Attribute des `ClassicMovie`-Attributs der Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="78e81-511">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="78e81-512">Wie bereits gesagt, verwenden [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und [HTML-Hilfsprogramme](xref:mvc/views/overview) Informationen der Validierungsattribute, um `data-`-Attribute zu rendern.</span><span class="sxs-lookup"><span data-stu-id="78e81-512">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="78e81-513">Es gibt zwei Möglichkeiten, Code zu schreiben, der zur Erstellung von benutzerdefinierten HTML-Attributen des Typs `data-` führt:</span><span class="sxs-lookup"><span data-stu-id="78e81-513">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="78e81-514">Erstellen einer Klasse, die von `AttributeAdapterBase<TAttribute>` ableitet, und einer Klasse, die `IValidationAttributeAdapterProvider` implementiert, und Registrieren Ihres Attributs und des dazugehörigen Adapters in der Abhängigkeitsinjektion.</span><span class="sxs-lookup"><span data-stu-id="78e81-514">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="78e81-515">Diese Methode folgt dem [Single-Responsibility-Prinzip](https://wikipedia.org/wiki/Single_responsibility_principle), nach dem sich der serverbezogene und der clientbezogene Validierungscode in unterschiedlichen Klassen befinden.</span><span class="sxs-lookup"><span data-stu-id="78e81-515">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="78e81-516">Der Adapter bietet außerdem den Vorteil, dass andere Dienste in der Abhängigkeitsinjektion bei Bedarf zur Verfügung stehen, da der Adapter in der Abhängigkeitsinjektion registriert ist.</span><span class="sxs-lookup"><span data-stu-id="78e81-516">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="78e81-517">Implementieren von `IClientModelValidator` in Ihrer `ValidationAttribute`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="78e81-517">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="78e81-518">Diese Methode ist geeignet, wenn das Attribut für keinerlei serverseitige Validierung zuständig ist und keine Dienste aus der Abhängigkeitsinjektion benötigt.</span><span class="sxs-lookup"><span data-stu-id="78e81-518">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="78e81-519">AttributeAdapter für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-519">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="78e81-520">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-520">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="78e81-521">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="78e81-521">To add client validation by using this method:</span></span>

1. <span data-ttu-id="78e81-522">Erstellen Sie eine Attributadapterklasse für das benutzerdefinierte Validierungsattribut.</span><span class="sxs-lookup"><span data-stu-id="78e81-522">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="78e81-523">Leiten Sie Klasse aus <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>ab.</span><span class="sxs-lookup"><span data-stu-id="78e81-523">Derive the class from <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.AttributeAdapterBase%601>.</span></span> <span data-ttu-id="78e81-524">Erstellen Sie eine `AddValidation`-Methode, die der gerenderten Ausgabe `data-`-Attribute hinzufügt, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-524">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="78e81-525">Erstellen Sie eine Adapteranbieterklasse, die <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> implementiert.</span><span class="sxs-lookup"><span data-stu-id="78e81-525">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="78e81-526">Übergeben Sie in der `GetAttributeAdapter`-Methode dem Konstruktor des Adapters das benutzerdefinierte Attribut, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-526">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="78e81-527">Registrieren Sie den Adapteranbieter für die Abhängigkeitsinjektion in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="78e81-527">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="78e81-528">IClientModelValidator für die clientseitige Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-528">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="78e81-529">Diese Methode, bei der `data-`-Attribute in HTML gerendert werden, wird vom `ClassicMovie2`-Attribut in der Beispiel-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="78e81-529">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="78e81-530">So fügen Sie die Clientvalidierung mithilfe dieser Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="78e81-530">To add client validation by using this method:</span></span>

* <span data-ttu-id="78e81-531">Implementieren Sie im benutzerdefinierten Validierungsattribut die `IClientModelValidator`-Schnittstelle, und erstellen Sie eine `AddValidation`-Methode.</span><span class="sxs-lookup"><span data-stu-id="78e81-531">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="78e81-532">Fügen Sie in der `AddValidation`-Methode die `data-`-Attribute für die Validierung hinzu, wie es im folgenden Beispiel gezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="78e81-532">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="78e81-533">Deaktivieren der clientseitigen Validierung</span><span class="sxs-lookup"><span data-stu-id="78e81-533">Disable client-side validation</span></span>

<span data-ttu-id="78e81-534">Im folgenden Code wird die Clientvalidierung in MVC-Ansichten deaktiviert:</span><span class="sxs-lookup"><span data-stu-id="78e81-534">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="78e81-535">Und in Razor Seiten:</span><span class="sxs-lookup"><span data-stu-id="78e81-535">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="78e81-536">Eine andere Möglichkeit, um die Clientvalidierung zu deaktivieren, ist es, den Verweis auf `_ValidationScriptsPartial` in Ihrer *.cshtml*-Datei auszukommentieren.</span><span class="sxs-lookup"><span data-stu-id="78e81-536">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78e81-537">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="78e81-537">Additional resources</span></span>

* [<span data-ttu-id="78e81-538">System.ComponentModel.DataAnnotations-Namespace</span><span class="sxs-lookup"><span data-stu-id="78e81-538">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="78e81-539">Modell Bindung</span><span class="sxs-lookup"><span data-stu-id="78e81-539">Model Binding</span></span>](model-binding.md)

::: moniker-end
