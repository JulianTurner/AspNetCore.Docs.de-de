---
title: Neuerungen in ASP.NET Core 5.0
author: rick-anderson
description: Informationen zu den neuen Features in ASP.NET Core 5.0.
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2020
no-loc:
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
- Kestrel
uid: aspnetcore-5.0
ms.openlocfilehash: e25549d557dd971d0f2f4d67a182574f07138acb
ms.sourcegitcommit: 1be547564381873fe9e84812df8d2088514c622a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94508122"
---
# <a name="whats-new-in-aspnet-core-50"></a><span data-ttu-id="9219f-103">Neuerungen in ASP.NET Core 5.0</span><span class="sxs-lookup"><span data-stu-id="9219f-103">What's new in ASP.NET Core 5.0</span></span>

<span data-ttu-id="9219f-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 5.0 aufgezeigt und Links zur relevanten Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9219f-104">This article highlights the most significant changes in ASP.NET Core 5.0 with links to relevant documentation.</span></span>

## <a name="aspnet-core-mvc-and-no-locrazor-improvements"></a><span data-ttu-id="9219f-105">ASP.NET Core MVC- und Razor-Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="9219f-105">ASP.NET Core MVC and Razor improvements</span></span>

### <a name="model-binding-datetime-as-utc"></a><span data-ttu-id="9219f-106">Modellbindung von DateTime als UTC</span><span class="sxs-lookup"><span data-stu-id="9219f-106">Model binding DateTime as UTC</span></span>

<span data-ttu-id="9219f-107">Die Modellbindung unterstützt jetzt die Bindung von UTC-Zeitzeichenfolgen an `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="9219f-107">Model binding now supports binding UTC time strings to `DateTime`.</span></span> <span data-ttu-id="9219f-108">Wenn die Anforderung eine UTC-Zeitzeichenfolge enthält, bindet die Modellbindung Sie an ein UTC-`DateTime`-Element.</span><span class="sxs-lookup"><span data-stu-id="9219f-108">If the request contains a UTC time string, model binding binds it to a UTC `DateTime`.</span></span> <span data-ttu-id="9219f-109">Beispielsweise wird die folgende Zeitzeichenfolge an das UTC-`DateTime`-Element gebunden: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span><span class="sxs-lookup"><span data-stu-id="9219f-109">For example, the following time string is bound the UTC `DateTime`: `https://example.com/mycontroller/myaction?time=2019-06-14T02%3A30%3A04.0576719Z`</span></span>

### <a name="model-binding-and-validation-with-c-9-record-types"></a><span data-ttu-id="9219f-110">Modellbindung und Validierung mit Datensatztypen in C# 9.0</span><span class="sxs-lookup"><span data-stu-id="9219f-110">Model binding and validation with C# 9 record types</span></span>

<span data-ttu-id="9219f-111">[Datensatztypen in C# 9.0](/dotnet/csharp/whats-new/csharp-9#record-types) können mit der Modellbindung in einem Model View Controller (MVC) oder einer Razor-Seite verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-111">[C# 9 record types](/dotnet/csharp/whats-new/csharp-9#record-types) can be used with model binding in an MVC controller or a Razor Page.</span></span> <span data-ttu-id="9219f-112">Datensatztypen lassen sich gut zur Modellierung von Daten verwenden, die über das Netzwerk übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-112">Record types are a good way to model data being transmitted over the network.</span></span>

<span data-ttu-id="9219f-113">Beispielsweise wird im folgenden `PersonController`-Element der `Person`-Datensatztyp mit Modellbindung und Formularvalidierung verwendet:</span><span class="sxs-lookup"><span data-stu-id="9219f-113">For example, the following `PersonController` uses the `Person` record type with model binding and form validation:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
          // ...
   }
}
```

<span data-ttu-id="9219f-114">Die Datei `Person/Index.cshtml` enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9219f-114">The `Person/Index.cshtml` file:</span></span>

```cshtml
@model Person

Name: <input asp-for="Model.Name" />
<span asp-validation-for="Model.Name" />

Age: <input asp-for="Model.Age" />
<span asp-validation-for="Model.Age" />
```

### <a name="improvements-to-dynamicroutevaluetransformer"></a><span data-ttu-id="9219f-115">Verbesserungen an DynamicRouteValueTransformer</span><span class="sxs-lookup"><span data-stu-id="9219f-115">Improvements to DynamicRouteValueTransformer</span></span>

<span data-ttu-id="9219f-116">Mit ASP.NET Core 3.1 wurde <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> als Methode zur Verwendung eines benutzerdefinierten Endpunkts für die dynamische Auswahl einer MVC-Aktion oder einer Razor-Seite eingeführt.</span><span class="sxs-lookup"><span data-stu-id="9219f-116">ASP.NET Core 3.1 introduced <xref:Microsoft.AspNetCore.Mvc.Routing.DynamicRouteValueTransformer> as a way to use custom endpoint to dynamically select an MVC controller action or a Razor page.</span></span> <span data-ttu-id="9219f-117">ASP.NET Core 5.0-Anwendungen können den Status an ein `DynamicRouteValueTransformer`-Element übergeben und die ausgewählten Endpunkte filtern.</span><span class="sxs-lookup"><span data-stu-id="9219f-117">ASP.NET Core 5.0 apps can pass state to a `DynamicRouteValueTransformer` and filter the set of endpoints chosen.</span></span>

### <a name="miscellaneous"></a><span data-ttu-id="9219f-118">Sonstiges</span><span class="sxs-lookup"><span data-stu-id="9219f-118">Miscellaneous</span></span>

* <span data-ttu-id="9219f-119">Das [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attribut kann auf Eigenschaften eines Razor-Seitenmodells angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-119">The [[Compare]](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute can be applied to properties on a Razor Page model.</span></span>
* <span data-ttu-id="9219f-120">Parameter und Eigenschaften, die vom Text gebunden werden, gelten standardmäßig als erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9219f-120">Parameters and properties bound from the body are considered required by default.</span></span> <!-- Review: How is this different from 3.1
The validation system in .NET Core 3.0 and later treats non-nullable parameters or bound properties as if they had a [Required] attribute.
see https://docs.microsoft.com/aspnet/core/mvc/models/validation?view=aspnetcore-3.1   
-->

## <a name="web-api"></a><span data-ttu-id="9219f-121">Web-API</span><span class="sxs-lookup"><span data-stu-id="9219f-121">Web API</span></span>

### <a name="openapi-specification-on-by-default"></a><span data-ttu-id="9219f-122">OpenAPI-Spezifikation standardmäßig aktiviert</span><span class="sxs-lookup"><span data-stu-id="9219f-122">OpenAPI Specification on by default</span></span>

<span data-ttu-id="9219f-123">[Die OpenAPI-Spezifikation](http://spec.openapis.org/oas/v3.0.3) ist ein Branchenstandard zum Beschreiben von HTTP-APIs und deren Integration in komplexe Geschäftsprozesse oder Drittanbieter.</span><span class="sxs-lookup"><span data-stu-id="9219f-123">[OpenAPI Specification](http://spec.openapis.org/oas/v3.0.3) is an industry standard for describing HTTP APIs and integrating them into complex business processes or with third parties.</span></span> <span data-ttu-id="9219f-124">OpenAPI wird von allen Cloudanbietern und zahlreichen API-Registrierungen weitgehend unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-124">OpenAPI is widely supported by all cloud providers and many API registries.</span></span> <span data-ttu-id="9219f-125">Apps, die OpenAPI-Dokumente aus Web-APIs ausgeben, haben eine Vielzahl neuer Möglichkeiten, mit denen diese APIs verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="9219f-125">Apps that emit OpenAPI documents from web APIs have a variety of new opportunities in which those APIs can be used.</span></span> <span data-ttu-id="9219f-126">In Zusammenarbeit mit den Betreuern des Open Source-Projekts [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) enthält die ASP.NET Core-API-Vorlage eine NuGet-Abhängigkeit von [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span><span class="sxs-lookup"><span data-stu-id="9219f-126">In partnership with the maintainers of the open-source project [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/), the ASP.NET Core API template contains a NuGet dependency on [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore).</span></span> <span data-ttu-id="9219f-127">Swashbuckle ist ein gängiges Open-Source-NuGet-Paket, das OpenAPI-Dokumente dynamisch ausgibt.</span><span class="sxs-lookup"><span data-stu-id="9219f-127">Swashbuckle is a popular open-source NuGet package that emits OpenAPI documents dynamically.</span></span> <span data-ttu-id="9219f-128">Swashbuckle führt dies durch das Introspektieren über die API-Controller und das Erstellen des OpenAPI-Dokuments zur Laufzeit oder zur Buildzeit mithilfe der Swashbuckle-CLI aus.</span><span class="sxs-lookup"><span data-stu-id="9219f-128">Swashbuckle does this by introspecting over the API controllers and generating the OpenAPI document at run-time, or at build time using the Swashbuckle CLI.</span></span>

<span data-ttu-id="9219f-129">In ASP.NET Core 5.0 wird die OpenAPI-Unterstützung von den Web-API-Vorlagen standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="9219f-129">In ASP.NET Core 5.0, the web API templates enable the OpenAPI support by default.</span></span> <span data-ttu-id="9219f-130">So deaktivieren Sie OpenAPI:</span><span class="sxs-lookup"><span data-stu-id="9219f-130">To disable OpenAPI:</span></span>

* <span data-ttu-id="9219f-131">Über die Befehlszeile:</span><span class="sxs-lookup"><span data-stu-id="9219f-131">From the command line:</span></span>

    ```dotnetcli
    dotnet new webapi --no-openapi true
    ```
* <span data-ttu-id="9219f-132">Über Visual Studio: Deaktivieren Sie die Option **Support für OpenAPI aktivieren**.</span><span class="sxs-lookup"><span data-stu-id="9219f-132">From Visual Studio: Uncheck **Enable OpenAPI support**.</span></span>

<span data-ttu-id="9219f-133">Alle *.csproj*-Dateien, die für Web-API-Projekte erstellt werden, enthalten den Verweis auf das [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="9219f-133">All *.csproj* files created for web API projects contain the [Swashbuckle.AspNetCore](https://www.nuget.org/packages/Swashbuckle.AspNetCore/) NuGet package reference.</span></span>

```xml
<ItemGroup>
    <PackageReference Include="Swashbuckle.AspNetCore" Version="5.5.1" />
</ItemGroup>
```

<span data-ttu-id="9219f-134">Der von der Vorlage generierte Code enthält Code in `Startup.ConfigureServices`, der die Erstellung des OpenAPI-Dokuments aktiviert:</span><span class="sxs-lookup"><span data-stu-id="9219f-134">The template generated code contains code in `Startup.ConfigureServices` that activates OpenAPI document generation:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet)]

<span data-ttu-id="9219f-135">Die `Startup.Configure`-Methode fügt die Swashbuckle-Middleware hinzu, die Folgendes ermöglicht:</span><span class="sxs-lookup"><span data-stu-id="9219f-135">The `Startup.Configure` method adds the Swashbuckle middleware, which enables the:</span></span>

* <span data-ttu-id="9219f-136">Dokumenterstellungsprozess</span><span class="sxs-lookup"><span data-stu-id="9219f-136">Document generation process.</span></span>
* <span data-ttu-id="9219f-137">Die Seite für die Swagger-Benutzeroberfläche ist standardmäßig im Entwicklungsmodus.</span><span class="sxs-lookup"><span data-stu-id="9219f-137">Swagger UI page by default in development mode.</span></span>

<span data-ttu-id="9219f-138">Der von der Vorlage generierte Code wird nicht versehentlich die Beschreibung der API bei der Veröffentlichung in der Produktion verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="9219f-138">The template generated code won't accidentally expose the API's description when publishing to production.</span></span>

[!code-csharp[](~/release-notes/sample/StartupSwagger.cs?name=snippet2)]

#### <a name="azure-api-management-import"></a><span data-ttu-id="9219f-139">Azure API Management-Import</span><span class="sxs-lookup"><span data-stu-id="9219f-139">Azure API Management Import</span></span>

<span data-ttu-id="9219f-140">Wenn ASP.NET Core API-Projekte OpenAPI aktivieren, bietet die Veröffentlichung mit Visual Studio 2019 Version 16.8 und höher automatisch einen zusätzlichen Schritt bei der Veröffentlichung.</span><span class="sxs-lookup"><span data-stu-id="9219f-140">When ASP.NET Core API projects enable OpenAPI, the Visual Studio 2019 version 16.8 and later publishing automatically offer an additional step in the publishing flow.</span></span> <span data-ttu-id="9219f-141">Entwickler, die [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) verwenden, haben die Möglichkeit, die APIs während des Veröffentlichungsflusses automatisch in Azure API Management zu importieren:</span><span class="sxs-lookup"><span data-stu-id="9219f-141">Developers who use [Azure API Management](xref:tutorials/publish-to-azure-api-management-using-vs) have an opportunity to automatically import the APIs into Azure API Management during the publish flow:</span></span>

![Azure API Management-Import im Vergleich zur Veröffentlichung](~/release-notes/static/publish-to-apim.png)

### <a name="better-launch-experience-for-web-api-projects"></a><span data-ttu-id="9219f-143">Bessere Startbedingungen für Web-API-Projekte</span><span class="sxs-lookup"><span data-stu-id="9219f-143">Better launch experience for web API projects</span></span>

<span data-ttu-id="9219f-144">Wenn OpenAPI standardmäßig aktiviert ist, werden die Startbedingungen für die App (F5) für Web-API-Entwickler erheblich verbessert.</span><span class="sxs-lookup"><span data-stu-id="9219f-144">With OpenAPI enabled by default, the app launching experience (F5) for web API developers significantly improves.</span></span> <span data-ttu-id="9219f-145">In ASP.NET Core 5.0 ist die Web-API-Vorlage zum Laden der Seite für die Swagger-Benutzeroberfläche vorkonfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9219f-145">With ASP.NET Core 5.0, the web API template comes pre-configured to load up the Swagger UI page.</span></span> <span data-ttu-id="9219f-146">Auf der Seite für die Swagger-Benutzeroberfläche finden Sie die Dokumentation, die für die veröffentlichte API hinzugefügt wurde. Zudem können Sie von dort aus auch APIs mit einem einzigen Mausklick testen.</span><span class="sxs-lookup"><span data-stu-id="9219f-146">The Swagger UI page provides both the documentation added for the published API, and enables testing the APIs with a single click.</span></span>

![Swagger/index.html-Sicht](~/release-notes/static/swagger-ui-page-rc1.png)

## Blazor

### <a name="performance-improvements"></a><span data-ttu-id="9219f-148">Leistungsverbesserungen</span><span class="sxs-lookup"><span data-stu-id="9219f-148">Performance improvements</span></span>

<span data-ttu-id="9219f-149">Für .NET 5 wurden erhebliche Verbesserungen der Blazor WebAssembly-Laufzeitleistung mit besonderem Schwerpunkt auf komplexem Benutzeroberflächenrendering und JSON-Serialisierung vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="9219f-149">For .NET 5, we made significant improvements to Blazor WebAssembly runtime performance with a specific focus on complex UI rendering and JSON serialization.</span></span> <span data-ttu-id="9219f-150">In unseren Leistungstests ist Blazor WebAssembly in .NET 5 in den meisten Szenarios zwei- bis dreimal schneller.</span><span class="sxs-lookup"><span data-stu-id="9219f-150">In our performance tests, Blazor WebAssembly in .NET 5 is two to three times faster for most scenarios.</span></span> <span data-ttu-id="9219f-151">Weitere Informationen finden Sie im [ASP.NET-Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements) (ASP.NET Core-Updates in .NET 5 Release Candidate).</span><span class="sxs-lookup"><span data-stu-id="9219f-151">For more information, see [ASP.NET Blog: ASP.NET Core updates in .NET 5 Release Candidate 1](https://devblogs.microsoft.com/aspnet/asp-net-core-updates-in-net-5-release-candidate-1/#blazor-webassembly-performance-improvements).</span></span>

### <a name="css-isolation"></a><span data-ttu-id="9219f-152">CSS-Isolation</span><span class="sxs-lookup"><span data-stu-id="9219f-152">CSS isolation</span></span>

<span data-ttu-id="9219f-153">Blazor unterstützt jetzt das Definieren von CSS-Formaten, die auf eine bestimmte Komponente zugeschnitten sind.</span><span class="sxs-lookup"><span data-stu-id="9219f-153">Blazor now supports defining CSS styles that are scoped to a given component.</span></span> <span data-ttu-id="9219f-154">Komponentenspezifische CSS-Formate erleichtern die Auseinandersetzung mit den Formaten in einer Anwendung und die Vermeidung unbeabsichtigter Begleiterscheinungen von globalen Formaten.</span><span class="sxs-lookup"><span data-stu-id="9219f-154">Component-specific CSS styles make it easier to reason about the styles in an app and to avoid unintentional side effects of global styles.</span></span> <span data-ttu-id="9219f-155">Weitere Informationen finden Sie unter <xref:blazor/components/css-isolation>.</span><span class="sxs-lookup"><span data-stu-id="9219f-155">For more information, see <xref:blazor/components/css-isolation>.</span></span>

### <a name="new-inputfile-component"></a><span data-ttu-id="9219f-156">Neue `InputFile`-Komponente</span><span class="sxs-lookup"><span data-stu-id="9219f-156">New `InputFile` component</span></span>

<span data-ttu-id="9219f-157">Die `InputFile`-Komponente ermöglicht das Lesen einer oder mehrerer Dateien, die von einem Benutzer für den Upload ausgewählt wurden.</span><span class="sxs-lookup"><span data-stu-id="9219f-157">The `InputFile` component allows reading one or more files selected by a user for upload.</span></span> <span data-ttu-id="9219f-158">Weitere Informationen finden Sie unter <xref:blazor/file-uploads>.</span><span class="sxs-lookup"><span data-stu-id="9219f-158">For more information, see <xref:blazor/file-uploads>.</span></span>

### <a name="new-inputradio-and-inputradiogroup-components"></a><span data-ttu-id="9219f-159">Neue `InputRadio`- und `InputRadioGroup`-Komponenten</span><span class="sxs-lookup"><span data-stu-id="9219f-159">New `InputRadio` and `InputRadioGroup` components</span></span>

<span data-ttu-id="9219f-160">Blazor verfügt über integrierte `InputRadio`- und `InputRadioGroup`-Komponenten, die die Datenbindung an Optionsfeldgruppen mit integrierter Validierung vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="9219f-160">Blazor has built-in `InputRadio` and `InputRadioGroup` components that simplify data binding to radio button groups with integrated validation.</span></span> <span data-ttu-id="9219f-161">Weitere Informationen finden Sie unter <xref:blazor/forms-validation>.</span><span class="sxs-lookup"><span data-stu-id="9219f-161">For more information, see <xref:blazor/forms-validation>.</span></span>

### <a name="component-virtualization"></a><span data-ttu-id="9219f-162">Komponentenvirtualisierung</span><span class="sxs-lookup"><span data-stu-id="9219f-162">Component virtualization</span></span>

<span data-ttu-id="9219f-163">Verbessern Sie die wahrgenommene Leistung von Komponentenrendering mithilfe der integrierten Virtualisierungsunterstützung des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="9219f-163">Improve the perceived performance of component rendering using the Blazor framework's built-in virtualization support.</span></span> <span data-ttu-id="9219f-164">Weitere Informationen finden Sie unter <xref:blazor/forms-validation#radio-buttons>.</span><span class="sxs-lookup"><span data-stu-id="9219f-164">For more information, see <xref:blazor/forms-validation#radio-buttons>.</span></span>

### <a name="ontoggle-event-support"></a><span data-ttu-id="9219f-165">Unterstützung von `ontoggle`-Ereignissen</span><span class="sxs-lookup"><span data-stu-id="9219f-165">`ontoggle` event support</span></span>

<span data-ttu-id="9219f-166">Blazor-Ereignisse unterstützen jetzt das `ontoggle`-DOM-Ereignis.</span><span class="sxs-lookup"><span data-stu-id="9219f-166">Blazor events now support the `ontoggle` DOM event.</span></span> <span data-ttu-id="9219f-167">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#event-argument-types>.</span><span class="sxs-lookup"><span data-stu-id="9219f-167">For more information, see <xref:blazor/components/event-handling#event-argument-types>.</span></span>

### <a name="set-ui-focus-in-no-locblazor-apps"></a><span data-ttu-id="9219f-168">Festlegen des Fokus auf die Benutzeroberfläche in Blazor-Apps</span><span class="sxs-lookup"><span data-stu-id="9219f-168">Set UI focus in Blazor apps</span></span>

<span data-ttu-id="9219f-169">Verwenden Sie die `FocusAsync`-Hilfsmethode für Elementverweise, um den Benutzeroberflächenfokus auf dieses Element festzulegen.</span><span class="sxs-lookup"><span data-stu-id="9219f-169">Use the `FocusAsync` convenience method on element references to set the UI focus to that element.</span></span> <span data-ttu-id="9219f-170">Weitere Informationen finden Sie unter <xref:blazor/components/event-handling#focus-an-element>.</span><span class="sxs-lookup"><span data-stu-id="9219f-170">For more information, see <xref:blazor/components/event-handling#focus-an-element>.</span></span>

### <a name="custom-validation-class-attributes"></a><span data-ttu-id="9219f-171">Benutzerdefinierte Validierungsklassenattribute</span><span class="sxs-lookup"><span data-stu-id="9219f-171">Custom validation class attributes</span></span>

<span data-ttu-id="9219f-172">Benutzerdefinierte Validierungsklassennamen sind nützlich, wenn eine Integration in CSS-Frameworks wie Bootstrap erfolgt.</span><span class="sxs-lookup"><span data-stu-id="9219f-172">Custom validation class names are useful when integrating with CSS frameworks, such as Bootstrap.</span></span> <span data-ttu-id="9219f-173">Weitere Informationen finden Sie unter <xref:blazor/forms-validation#custom-validation-class-attributes>.</span><span class="sxs-lookup"><span data-stu-id="9219f-173">For more information, see <xref:blazor/forms-validation#custom-validation-class-attributes>.</span></span>

### <a name="iasyncdisposable-support"></a><span data-ttu-id="9219f-174">IAsyncDisposable-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="9219f-174">IAsyncDisposable support</span></span>

<span data-ttu-id="9219f-175">Blazor-Komponenten unterstützen jetzt die <xref:System.IAsyncDisposable>-Schnittstelle für das asynchrone Release zugeordneter Ressourcen.</span><span class="sxs-lookup"><span data-stu-id="9219f-175">Blazor components now support the <xref:System.IAsyncDisposable> interface for the asynchronous release of allocated resources.</span></span>

### <a name="javascript-isolation-and-object-references"></a><span data-ttu-id="9219f-176">-JavaScript-Isolation und Objektverweise</span><span class="sxs-lookup"><span data-stu-id="9219f-176">JavaScript isolation and object references</span></span>

<span data-ttu-id="9219f-177">Blazor aktiviert JavaScript-Isolierung in [JavaScript-Standardmodulen](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span><span class="sxs-lookup"><span data-stu-id="9219f-177">Blazor enables JavaScript isolation in standard [JavaScript modules](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Modules).</span></span> <span data-ttu-id="9219f-178">Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span><span class="sxs-lookup"><span data-stu-id="9219f-178">For more information, see <xref:blazor/call-javascript-from-dotnet#blazor-javascript-isolation-and-object-references>.</span></span>

### <a name="form-components-support-display-name"></a><span data-ttu-id="9219f-179">Formularkomponenten unterstützen Anzeigenamen</span><span class="sxs-lookup"><span data-stu-id="9219f-179">Form components support display name</span></span>

<span data-ttu-id="9219f-180">Die folgenden integrierten Komponenten unterstützen Anzeigenamen mit dem `DisplayName`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="9219f-180">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* `InputDate`
* `InputNumber`
* `InputSelect`

<span data-ttu-id="9219f-181">Weitere Informationen finden Sie unter <xref:blazor/forms-validation#display-name-support>.</span><span class="sxs-lookup"><span data-stu-id="9219f-181">For more information, see <xref:blazor/forms-validation#display-name-support>.</span></span>

### <a name="catch-all-route-parameters"></a><span data-ttu-id="9219f-182">Catch-All-Routenparameter</span><span class="sxs-lookup"><span data-stu-id="9219f-182">Catch-all route parameters</span></span>

<span data-ttu-id="9219f-183">Catch-All-Routenparameter, die Pfade über mehrere Ordnergrenzen hinweg erfassen, werden in Komponenten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-183">Catch-all route parameters, which capture paths across multiple folder boundaries, are supported in components.</span></span> <span data-ttu-id="9219f-184">Weitere Informationen finden Sie unter <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span><span class="sxs-lookup"><span data-stu-id="9219f-184">For more information, see <xref:blazor/fundamentals/routing#catch-all-route-parameters>.</span></span>

### <a name="debugging-improvements"></a><span data-ttu-id="9219f-185">Verbesserungen beim Debugging</span><span class="sxs-lookup"><span data-stu-id="9219f-185">Debugging improvements</span></span>

<span data-ttu-id="9219f-186">Das Debuggen von Blazor WebAssembly-Apps wurde in ASP.NET Core 5.0 verbessert.</span><span class="sxs-lookup"><span data-stu-id="9219f-186">Debugging Blazor WebAssembly apps is improved in ASP.NET Core 5.0.</span></span> <span data-ttu-id="9219f-187">Außerdem wird das Debuggen jetzt für Visual Studio für Mac unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-187">Additionally, debugging is now supported on Visual Studio for Mac.</span></span> <span data-ttu-id="9219f-188">Weitere Informationen finden Sie unter <xref:blazor/debug>.</span><span class="sxs-lookup"><span data-stu-id="9219f-188">For more information, see <xref:blazor/debug>.</span></span>

### <a name="microsoft-no-locidentity-v20-and-msal-v20"></a><span data-ttu-id="9219f-189">Microsoft Identity v2.0 und MSAL v2.0</span><span class="sxs-lookup"><span data-stu-id="9219f-189">Microsoft Identity v2.0 and MSAL v2.0</span></span>

<span data-ttu-id="9219f-190">Blazor-Sicherheit verwendet jetzt Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) und [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) und MSAL v2.0.</span><span class="sxs-lookup"><span data-stu-id="9219f-190">Blazor security now uses Microsoft Identity v2.0 ([`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web) and [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)) and MSAL v2.0.</span></span> <span data-ttu-id="9219f-191">Weitere Informationen finden Sie in den Themen unter [Blazor Sicherheit und Identity Knoten](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="9219f-191">For more information, see the topics in the [Blazor Security and Identity node](xref:blazor/security/index).</span></span>

### <a name="protected-browser-storage-for-no-locblazor-server"></a><span data-ttu-id="9219f-192">Geschützter Browserspeicher für Blazor Server</span><span class="sxs-lookup"><span data-stu-id="9219f-192">Protected Browser Storage for Blazor Server</span></span>

<span data-ttu-id="9219f-193">Für Blazor Server-Apps gibt es jetzt eine integrierte Unterstützung zum Speichern des App-Status im Browser, der mit ASP.NET Core-Datenschutzfunktionen vor unbefugtem Zugriff geschützt wird.</span><span class="sxs-lookup"><span data-stu-id="9219f-193">Blazor Server apps can now use built-in support for storing app state in the browser that has been protected from tampering using ASP.NET Core data protection.</span></span> <span data-ttu-id="9219f-194">Daten können entweder im lokalen Browserspeicher oder im Sitzungsspeicher gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-194">Data can be stored in either local browser storage or session storage.</span></span> <span data-ttu-id="9219f-195">Weitere Informationen finden Sie unter <xref:blazor/state-management>.</span><span class="sxs-lookup"><span data-stu-id="9219f-195">For more information, see <xref:blazor/state-management>.</span></span>

### <a name="no-locblazor-webassembly-prerendering"></a><span data-ttu-id="9219f-196">Blazor WebAssembly-Prerendering</span><span class="sxs-lookup"><span data-stu-id="9219f-196">Blazor WebAssembly prerendering</span></span>

<span data-ttu-id="9219f-197">Die Komponentenintegration wurde für verschiedene Hostingmodelle verbessert, und Blazor WebAssembly-Apps können jetzt die Ausgabe auf dem Server vorab rendern.</span><span class="sxs-lookup"><span data-stu-id="9219f-197">Component integration is improved across hosting models, and Blazor WebAssembly apps can now prerender output on the server.</span></span> <!-- UNCOMMENT AFTER https://github.com/dotnet/AspNetCore.Docs/pull/19887 MERGES: For more information, see <xref:blazor/components/integrate-components-into-razor-pages-and-mvc-apps> and <xref:mvc/views/tag-helpers/builtin-th/component-tag-helper>. -->

### <a name="trimminglinking-improvements"></a><span data-ttu-id="9219f-198">Verbesserungen beim Kürzen/Verknüpfen</span><span class="sxs-lookup"><span data-stu-id="9219f-198">Trimming/linking improvements</span></span>

<span data-ttu-id="9219f-199">Blazor WebAssembly führt die IL-Kürzung/Verknüpfung (Intermediate Language) während eines Builds durch, um nicht benötigte Zwischensprache aus den Ausgabeassemblys der Anwendung zu kürzen.</span><span class="sxs-lookup"><span data-stu-id="9219f-199">Blazor WebAssembly performs Intermediate Language (IL) trimming/linking during a build to trim unnecessary IL from the app's output assemblies.</span></span> <span data-ttu-id="9219f-200">Ab dem Release von ASP.NET Core 5.0 führt Blazor WebAssembly eine verbesserte Kürzung mit zusätzlichen Konfigurationsoptionen durch.</span><span class="sxs-lookup"><span data-stu-id="9219f-200">With the release of ASP.NET Core 5.0, Blazor WebAssembly performs improved trimming with additional configuration options.</span></span> <span data-ttu-id="9219f-201">Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/configure-trimmer> und [Kürzungsoptionen](/dotnet/core/deploying/trimming-options).</span><span class="sxs-lookup"><span data-stu-id="9219f-201">For more information, see <xref:blazor/host-and-deploy/configure-trimmer> and [Trimming options](/dotnet/core/deploying/trimming-options).</span></span>

### <a name="browser-compatibility-analyzer"></a><span data-ttu-id="9219f-202">Browserkompatibilitäts-Analysetool</span><span class="sxs-lookup"><span data-stu-id="9219f-202">Browser compatibility analyzer</span></span>

<span data-ttu-id="9219f-203">Blazor WebAssembly-Apps zielen auf die vollständige .NET-API-Oberfläche ab, aber aufgrund von Browsersandboxeinschränkungen werden nicht alle .NET-APIs in WebAssembly unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-203">Blazor WebAssembly apps target the full .NET API surface area, but not all .NET APIs are supported on WebAssembly due to browser sandbox constraints.</span></span> <span data-ttu-id="9219f-204">Nicht unterstützte APIs lösen <xref:System.PlatformNotSupportedException> bei der Ausführung in Webassembly aus.</span><span class="sxs-lookup"><span data-stu-id="9219f-204">Unsupported APIs throw <xref:System.PlatformNotSupportedException> when running on WebAssembly.</span></span> <span data-ttu-id="9219f-205">Ein Plattformkompatibilitäts-Analysetool warnt den Entwickler, wenn die API APIs verwendet, die nicht von den Zielplattformen der App unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-205">A platform compatibility analyzer warns the developer when the app uses APIs that aren't supported by the app's target platforms.</span></span> <span data-ttu-id="9219f-206">Weitere Informationen finden Sie unter <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span><span class="sxs-lookup"><span data-stu-id="9219f-206">For more information, see <xref:blazor/components/class-libraries#browser-compatibility-analyzer-for-blazor-webassembly>.</span></span>

### <a name="lazy-load-assemblies"></a><span data-ttu-id="9219f-207">Lazy Loading-Assemblys</span><span class="sxs-lookup"><span data-stu-id="9219f-207">Lazy load assemblies</span></span>

<span data-ttu-id="9219f-208">Die Startleistung einer Blazor WebAssembly-App kann verbessert werden, indem das Laden einiger Anwendungsassemblys verzögert wird, bis sie erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="9219f-208">Blazor WebAssembly app startup performance can be improved by deferring the loading of some application assemblies until they're required.</span></span> <span data-ttu-id="9219f-209">Weitere Informationen finden Sie unter <xref:blazor/webassembly-lazy-load-assemblies>.</span><span class="sxs-lookup"><span data-stu-id="9219f-209">For more information, see <xref:blazor/webassembly-lazy-load-assemblies>.</span></span>

### <a name="updated-globalization-support"></a><span data-ttu-id="9219f-210">Aktualisierte Globalisierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="9219f-210">Updated globalization support</span></span>

<span data-ttu-id="9219f-211">Die Globalisierungsunterstützung ist für Blazor WebAssembly basierend auf den internationalen Komponenten für Unicode (ICU) verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9219f-211">Globalization support is available for Blazor WebAssembly based on International Components for Unicode (ICU).</span></span> <span data-ttu-id="9219f-212">Weitere Informationen finden Sie unter <xref:blazor/globalization-localization>.</span><span class="sxs-lookup"><span data-stu-id="9219f-212">For more information, see <xref:blazor/globalization-localization>.</span></span>

## <a name="grpc"></a><span data-ttu-id="9219f-213">gRPC</span><span class="sxs-lookup"><span data-stu-id="9219f-213">gRPC</span></span>

<span data-ttu-id="9219f-214">In [gRPC](https://grpc.io/) wurden viele Leistungsverbesserungen vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="9219f-214">Many preformance improvements have been made in [gRPC](https://grpc.io/).</span></span> <span data-ttu-id="9219f-215">Weitere Informationen finden Sie unter [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/) (gPRC-Leistungsverbesserungen in .NET 5).</span><span class="sxs-lookup"><span data-stu-id="9219f-215">For more information, see [gRPC performance improvements in .NET 5](https://devblogs.microsoft.com/aspnet/grpc-performance-improvements-in-net-5/).</span></span>

<span data-ttu-id="9219f-216">Weitere Informationen zu gRPC finden Sie unter <xref:grpc/index>.</span><span class="sxs-lookup"><span data-stu-id="9219f-216">For more gRPC information, see <xref:grpc/index>.</span></span>

## SignalR

### <a name="no-locsignalr-hub-filters"></a><span data-ttu-id="9219f-217">SignalR-Hubfilter</span><span class="sxs-lookup"><span data-stu-id="9219f-217">SignalR Hub filters</span></span>

<span data-ttu-id="9219f-218">SignalR Hubfilter, die in ASP.NET als Hubpipelines bezeichnet werdenSignalR, sind ein Feature, das die Ausführung von Code vor und nach dem Aufruf von Hubmethoden ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="9219f-218">SignalR Hub filters, called Hub pipelines in ASP.NET SignalR, is a feature that allows code to run before and after Hub methods are called.</span></span> <span data-ttu-id="9219f-219">Das Ausführen von Code vor und nach dem Aufruf von Hubmethoden ist vergleichbar damit, wie mithilfe von Middleware Code vor und nach einer HTTP-Anforderung ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="9219f-219">Running code before and after Hub methods are called is similar to how middleware has the ability to run code before and after an HTTP request.</span></span> <span data-ttu-id="9219f-220">Zu den allgemeinen Verwendungsmöglichkeiten zählen die Protokollierung, die Fehlerbehandlung und die Argumentvalidierung.</span><span class="sxs-lookup"><span data-stu-id="9219f-220">Common uses include logging, error handling, and argument validation.</span></span>

<span data-ttu-id="9219f-221">Weitere Informationen finden Sie unter [Use hub filters in ASP.NET CoreSignalR](xref:signalr/hub-filters) (Verwenden von Hubfiltern in ASP.NET Core).</span><span class="sxs-lookup"><span data-stu-id="9219f-221">For more information, see [Use hub filters in ASP.NET Core SignalR](xref:signalr/hub-filters).</span></span>

### <a name="no-locsignalr-parallel-hub-invocations"></a><span data-ttu-id="9219f-222">Parallele SignalR-Hubaufrufe</span><span class="sxs-lookup"><span data-stu-id="9219f-222">SignalR parallel hub invocations</span></span>

<span data-ttu-id="9219f-223">ASP.NET Core SignalR ist nun in der Lage, parallele Hubaufrufe zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="9219f-223">ASP.NET Core SignalR is now capable of handling parallel hub invocations.</span></span> <span data-ttu-id="9219f-224">Das Standardverhalten kann so geändert werden, dass Clients gleichzeitig mehr als eine Hubmethode aufrufen:</span><span class="sxs-lookup"><span data-stu-id="9219f-224">The default behavior can be changed to allow clients to invoke more than one hub method at a time:</span></span>

[!code-csharp[](~/release-notes/sample/StartupSignalRhubs.cs?name=snippet)]

### <a name="added-messagepack-support-in-no-locsignalr-java-client"></a><span data-ttu-id="9219f-225">Hinzugefügte MessagePack-Unterstützung im SignalR-Java-Client</span><span class="sxs-lookup"><span data-stu-id="9219f-225">Added Messagepack support in SignalR Java client</span></span>

<span data-ttu-id="9219f-226">Ein neues Paket, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), bietet MessagePack-Unterstützung für den SignalR-Java-Client.</span><span class="sxs-lookup"><span data-stu-id="9219f-226">A new package, [com.microsoft.signalr.messagepack](https://mvnrepository.com/artifact/com.microsoft.signalr.messagepack), adds MessagePack support to the SignalR Java client.</span></span> <span data-ttu-id="9219f-227">Um das MessagePack-Hubprotokoll zu verwenden, fügen Sie `.withHubProtocol(new MessagePackHubProtocol())` zum Verbindungs-Generator hinzu:</span><span class="sxs-lookup"><span data-stu-id="9219f-227">To use the MessagePack hub protocol, add `.withHubProtocol(new MessagePackHubProtocol())` to the connection builder:</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create(
                           "http://localhost:53353/MyHub")
               .withHubProtocol(new MessagePackHubProtocol())
               .build();
```

<!--
See [Update SignalR code](xref:migration/31-to-50#signalr) for migration instructions.
-->

## Kestrel

* <span data-ttu-id="9219f-228">Erneut ladbare Endpunkte über Konfiguration: Kestrel kann Konfigurationsänderungen erkennen, die an [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) übergeben werden, und die Bindung von vorhandenen Endpunkten lösen und an neue Endpunkte binden, ohne dass ein Neustart der Anwendung erforderlich ist, wenn der Parameter `reloadOnChange` auf `true` gesetzt ist.</span><span class="sxs-lookup"><span data-stu-id="9219f-228">Reloadable endpoints via configuration: Kestrel can detect changes to configuration passed to [KestrelServerOptions.Configure](xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Configure%2A) and unbind from existing endpoints and bind to new endpoints without requiring an application restart when the `reloadOnChange` parameter is `true`.</span></span> <span data-ttu-id="9219f-229">Standardmäßig wird bei Verwendung von <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> oder <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A> Kestrel mit aktiviertem `reloadOnChange`-Element an den Konfigurationsunterabschnitt ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) gebunden.</span><span class="sxs-lookup"><span data-stu-id="9219f-229">By default when using <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults%2A> or <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>, Kestrel binds to the ["Kestrel"](https://github.com/dotnet/aspnetcore/blob/7e9e03b70124784b1de5564c573bd65cdaccbfcc/src/DefaultBuilder/src/WebHost.cs#L226) configuration subsection with `reloadOnChange` enabled.</span></span> <span data-ttu-id="9219f-230">Apps müssen `reloadOnChange: true` übergeben, wenn `KestrelServerOptions.Configure` manuell aufgerufen wird, um erneut ladbare Endpunkte abzurufen.</span><span class="sxs-lookup"><span data-stu-id="9219f-230">Apps must pass `reloadOnChange: true` when calling `KestrelServerOptions.Configure` manually to get reloadable endpoints.</span></span>
* <span data-ttu-id="9219f-231">Verbesserungen bei HTTP/2-Antwortheadern.</span><span class="sxs-lookup"><span data-stu-id="9219f-231">HTTP/2 response headers improvements.</span></span> <span data-ttu-id="9219f-232">Weitere Informationen finden Sie im nächsten Abschnitt [Leistungsverbesserungen](#performance-improvements).</span><span class="sxs-lookup"><span data-stu-id="9219f-232">For more information, see [Performance improvements](#performance-improvements) in the next section.</span></span>
* <span data-ttu-id="9219f-233">Unterstützung für zusätzliche Endpunkttypen im Socketstransport: Als Ergänzung der neuen API, die in <xref:System.Net.Sockets?displayProperty=nameWithType> eingeführt wurde, ermöglicht der Socketsstandardtransport in [Kestrel](xref:fundamentals/servers/kestrel) die Bindung sowohl an vorhandene Dateihandles als auch an Unix-Domänensockets.</span><span class="sxs-lookup"><span data-stu-id="9219f-233">Support for additional endpoints types in the sockets transport: Adding to the new API introduced in <xref:System.Net.Sockets?displayProperty=nameWithType>, the sockets default transport in [Kestrel](xref:fundamentals/servers/kestrel) allows binding to both existing file handles and Unix domain sockets.</span></span> <span data-ttu-id="9219f-234">Die Unterstützung für die Bindung an vorhandene Dateihandles ermöglicht die Verwendung der vorhandenen `Systemd`-Integration, ohne dass der `libuv`-Transport erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="9219f-234">Support for binding to existing file handles enables using the existing `Systemd` integration without requiring the `libuv` transport.</span></span>
* <span data-ttu-id="9219f-235">Decodierung von benutzerdefiniertem Header in Kestrel: Mithilfe von Apps kann festgelegt werden, welche Codierung (<xref:System.Text.Encoding>) verwendet werden soll, um eingehende Header anhand des Headernamens zu interpretieren, anstatt standardmäßig UTF-8 zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9219f-235">Custom header decoding in Kestrel: Apps can specify which <xref:System.Text.Encoding> to use to interpret incoming headers based on the header name instead of defaulting to UTF-8.</span></span> <span data-ttu-id="9219f-236">Legen Sie den Schalter </span><span class="sxs-lookup"><span data-stu-id="9219f-236">Set the</span></span> <!--<xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector> --> <span data-ttu-id="9219f-237">Eigenschaft `Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` zum Festlegen der zu verwendenden Codierung:</span><span class="sxs-lookup"><span data-stu-id="9219f-237">`Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions.RequestHeaderEncodingSelector` property to specify which encoding to use:</span></span>
 
  ```csharp
  public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.RequestHeaderEncodingSelector = encoding =>
                {
                      return encoding switch
                      {
                          "Host" => System.Text.Encoding.Latin1,
                          _ => System.Text.Encoding.UTF8,
                      };
                };
            });
            webBuilder.UseStartup<Startup>();
        });
  ```

### <a name="no-lockestrel-endpoint-specific-options-via-configuration"></a><span data-ttu-id="9219f-238">Für den Kestrel-Endpunkt spezifische Optionen über die Konfiguration</span><span class="sxs-lookup"><span data-stu-id="9219f-238">Kestrel endpoint-specific options via configuration</span></span>

<span data-ttu-id="9219f-239">Ab sofort wird das Konfigurieren der endpunktspezifischen Optionen für Kestrel über die [Konfiguration](xref:fundamentals/configuration/index) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-239">Support has been added for configuring Kestrel’s endpoint-specific options via [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="9219f-240">Die endpunktspezifischen Konfigurationen umfassen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9219f-240">The endpoint-specific configurations includes the:</span></span>

* <span data-ttu-id="9219f-241">Verwendete HTTP-Protokolle</span><span class="sxs-lookup"><span data-stu-id="9219f-241">HTTP protocols used</span></span>
* <span data-ttu-id="9219f-242">Verwendete TLS-Protokolle</span><span class="sxs-lookup"><span data-stu-id="9219f-242">TLS protocols used</span></span>
* <span data-ttu-id="9219f-243">Ausgewähltes Zertifikat</span><span class="sxs-lookup"><span data-stu-id="9219f-243">Certificate selected</span></span>
* <span data-ttu-id="9219f-244">Clientzertifikatmodus</span><span class="sxs-lookup"><span data-stu-id="9219f-244">Cient certificate mode</span></span>

<span data-ttu-id="9219f-245">Die Konfiguration ermöglicht die Angabe, welches Zertifikat auf Grundlage des angegebenen Servernamens ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="9219f-245">Configuration allows specifying which certificate is selected based on the specified server name.</span></span> <span data-ttu-id="9219f-246">Der Servername ist Teil der SNI-Erweiterung (Servernamensanzeige) des TLS-Protokolls, wie vom Client angegeben.</span><span class="sxs-lookup"><span data-stu-id="9219f-246">The server name is part of the Server Name Indication (SNI) extension to the TLS protocol as indicated by the client.</span></span> <span data-ttu-id="9219f-247">Die Konfiguration von Kestrel unterstützt auch ein Platzhalterpräfix im Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="9219f-247">Kestrel's configuration also supports a wildcard prefix in the host name.</span></span>

<span data-ttu-id="9219f-248">Im folgenden Beispiel wird gezeigt, wie eine endpunktspezifische Option mithilfe einer Konfigurationsdatei angegeben wird:</span><span class="sxs-lookup"><span data-stu-id="9219f-248">The following example shows how to specify endpoint-specific using a configuration file:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "EndpointName": {
        "Url": "https://*",
        "Sni": {
          "a.example.org": {
            "Protocols": "Http1AndHttp2",
            "SslProtocols": [ "Tls11", "Tls12"],
            "Certificate": {
              "Path": "testCert.pfx",
              "Password": "testPassword"
            },
            "ClientCertificateMode" : "NoCertificate"
          },
          "*.example.org": {
            "Certificate": {
              "Path": "testCert2.pfx",
              "Password": "testPassword"
            }
          },
          "*": {
            // At least one sub-property needs to exist per
            // SNI section or it cannot be discovered via
            // IConfiguration
            "Protocols": "Http1",
          }
        }
      }
    }
  }
}
```

<span data-ttu-id="9219f-249">Servernamensanzeige (SNI) ist eine TLS-Erweiterung zum Einbinden einer virtuellen Domäne als Teil der SSL-Aushandlung.</span><span class="sxs-lookup"><span data-stu-id="9219f-249">Server Name Indication (SNI) is a TLS extension to include a virtual domain as a part of SSL negotiation.</span></span> <span data-ttu-id="9219f-250">Dies bedeutet, dass der Name der virtuellen Domäne oder eines Hosts zur Identifizierung des Netzwerkendpunkts verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="9219f-250">What this effectively means is that the virtual domain name, or a hostname, can be used to identify the network end point.</span></span>

## <a name="performance-improvements"></a><span data-ttu-id="9219f-251">Leistungsverbesserungen</span><span class="sxs-lookup"><span data-stu-id="9219f-251">Performance improvements</span></span>

### <a name="http2"></a><span data-ttu-id="9219f-252">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="9219f-252">HTTP/2</span></span>

* <span data-ttu-id="9219f-253">Erhebliche Reduzierung der Zuweisungen im HTTP/2-Codepfad.</span><span class="sxs-lookup"><span data-stu-id="9219f-253">Significant reductions in allocations in the HTTP/2 code path.</span></span>
* <span data-ttu-id="9219f-254">Unterstützung für [dynamische HPack-Komprimierung](https://tools.ietf.org/html/rfc7541) von HTTP/2-Antwortheadern in [Kestrel](xref:fundamentals/servers/kestrel).</span><span class="sxs-lookup"><span data-stu-id="9219f-254">Support for [HPack dynamic compression](https://tools.ietf.org/html/rfc7541) of HTTP/2 response headers in [Kestrel](xref:fundamentals/servers/kestrel).</span></span> <span data-ttu-id="9219f-255">Weitere Informationen finden Sie unter [Größe der Headertabelle](xref:fundamentals/servers/kestrel#header-table-size) und [HPACK: The Silent Killer (Feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/) (HPACK: Der lautlose Killer (Feature) von HTTP/2).</span><span class="sxs-lookup"><span data-stu-id="9219f-255">For more information, see [Header table size](xref:fundamentals/servers/kestrel#header-table-size) and [HPACK: the silent killer (feature) of HTTP/2](https://blog.cloudflare.com/hpack-the-silent-killer-feature-of-http-2/).</span></span>
* <span data-ttu-id="9219f-256">Senden von HTTP/2-PING-Frames: HTTP/2 verfügt über einen Mechanismus zum Senden von PING-Frames, um sicherzustellen, dass eine Verbindung im Leerlauf noch funktionsfähig ist.</span><span class="sxs-lookup"><span data-stu-id="9219f-256">Sending HTTP/2 PING frames: HTTP/2 has a mechanism for sending PING frames to ensure an idle connection is still functional.</span></span> <span data-ttu-id="9219f-257">Das Sicherstellen einer funktionsfähigen Verbindung ist besonders nützlich, wenn mit lange dauernden Streams gearbeitet wird, die oft im Leerlauf sind und nur sporadisch Aktivität aufweisen, z. B. gRPC-Streams.</span><span class="sxs-lookup"><span data-stu-id="9219f-257">Ensuring a viable connection is especially useful when working with long-lived streams that are often idle but only intermittently see activity, for example, gRPC streams.</span></span> <span data-ttu-id="9219f-258">In [Kestrel](xref:fundamentals/servers/kestrel) können Apps periodische PING-Frames senden, indem sie Grenzwerte für <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions> festlegen:</span><span class="sxs-lookup"><span data-stu-id="9219f-258">Apps can send periodic PING frames in [Kestrel](xref:fundamentals/servers/kestrel) by setting limits on <xref:Microsoft.AspNetCore.Server.Kestrel.KestrelServerOptions>:</span></span>

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                options.Limits.Http2.KeepAlivePingInterval =
                                               TimeSpan.FromSeconds(10);
                options.Limits.Http2.KeepAlivePingTimeout =
                                               TimeSpan.FromSeconds(1);
            });
            webBuilder.UseStartup<Startup>();
        });
   ```
   <!-- review: KeepAlivePingInterval not found in RC1. Try testing with RC1. See https://github.com/dotnet/aspnetcore/pull/22565/files see C:/Users/riande/source/repos/WebApplication128/WebApplication128 -->

### <a name="containers"></a><span data-ttu-id="9219f-259">Container</span><span class="sxs-lookup"><span data-stu-id="9219f-259">Containers</span></span>

<span data-ttu-id="9219f-260">Vor .NET 5.0 war es zum Erstellen und Veröffentlichen einer *Dockerfile* für eine ASP.NET Core-Anwendung erforderlich, das gesamte .NET Core SDK und das ASP.NET Core-Image zu pullen.</span><span class="sxs-lookup"><span data-stu-id="9219f-260">Prior to .NET 5.0, building and publishing a *Dockerfile* for an ASP.NET Core app required pulling the entire .NET Core SDK and the ASP.NET Core image.</span></span> <span data-ttu-id="9219f-261">Mit dieser Version wird das Pullen der SDK-Imagebytes reduziert, und die für das ASP.NET Core-Image gepullten Bytes werden größtenteils entfernt.</span><span class="sxs-lookup"><span data-stu-id="9219f-261">With this release, pulling the SDK images bytes is reduced and the bytes pulled for the ASP.NET Core image is largely eliminated.</span></span> <span data-ttu-id="9219f-262">Weitere Informationen finden Sie in [diesem GitHub-Issue-Kommentar](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span><span class="sxs-lookup"><span data-stu-id="9219f-262">For more information, see [this GitHub issue comment](https://github.com/dotnet/dotnet-docker/issues/1814#issuecomment-625294750).</span></span>

## <a name="authentication-and-authorization"></a><span data-ttu-id="9219f-263">Authentifizierung und Autorisierung</span><span class="sxs-lookup"><span data-stu-id="9219f-263">Authentication and authorization</span></span>

### <a name="azure-active-directory-authentication-with-microsoftno-locidentityweb"></a><span data-ttu-id="9219f-264">Azure Active Directory-Authentifizierung bei Microsoft.Identity.Web</span><span class="sxs-lookup"><span data-stu-id="9219f-264">Azure Active Directory authentication with Microsoft.Identity.Web</span></span>

<span data-ttu-id="9219f-265">Die ASP.NET Core-Projektvorlagen lassen sich jetzt in <xref:Microsoft.Identity.Web?displayProperty=fullName> integrieren, um die Authentifizierung mit [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) abzuwickeln (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="9219f-265">The ASP.NET Core project templates now integrate with <xref:Microsoft.Identity.Web?displayProperty=fullName> to handle authentication with [Azure Activity Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD).</span></span> <span data-ttu-id="9219f-266">Das [Microsoft.Identity.Web-Paket](https://www.nuget.org/packages/Microsoft.Identity.Web/) bietet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="9219f-266">The [Microsoft.Identity.Web package](https://www.nuget.org/packages/Microsoft.Identity.Web/) provides:</span></span>

* <span data-ttu-id="9219f-267">Eine bessere Authentifizierung über Azure AD.</span><span class="sxs-lookup"><span data-stu-id="9219f-267">A better experience for authentication through Azure AD.</span></span>
* <span data-ttu-id="9219f-268">Eine einfachere Möglichkeit, auf Azure-Ressourcen im Namen Ihrer Benutzer zuzugreifen, einschließlich [Microsoft Graph](/graph/overview).</span><span class="sxs-lookup"><span data-stu-id="9219f-268">An easier way to access Azure resources on behalf of your users, including [Microsoft Graph](/graph/overview).</span></span> <span data-ttu-id="9219f-269">Weitere Informationen finden Sie im [Microsoft.Identity.Web-Beispiel](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), das mit einer einfachen Anmeldung beginnt und sich über Mehrinstanzenfähigkeit, die Verwendung von Azure-APIs, die Verwendung von Microsoft Graph und den Schutz Ihrer eigenen APIs erstreckt.</span><span class="sxs-lookup"><span data-stu-id="9219f-269">See the [Microsoft.Identity.Web sample](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2), which starts with a basic login and advances through multi-tenancy, using Azure APIs, using Microsoft Graph, and protecting your own APIs.</span></span> <span data-ttu-id="9219f-270">`Microsoft.Identity.Web` ist neben .NET 5 verfügbar.</span><span class="sxs-lookup"><span data-stu-id="9219f-270">`Microsoft.Identity.Web` is available alongside .NET 5.</span></span>

### <a name="allow-anonymous-access-to-an-endpoint"></a><span data-ttu-id="9219f-271">Zulassen des anonymen Zugriffs auf einen Endpunkt</span><span class="sxs-lookup"><span data-stu-id="9219f-271">Allow anonymous access to an endpoint</span></span>

<span data-ttu-id="9219f-272">Die `AllowAnonymous`-Erweiterungsmethode ermöglicht den anonymen Zugriff auf einen Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="9219f-272">The `AllowAnonymous` extension method allows anonymous access to an endpoint:</span></span>

[!code-csharp[](~/release-notes/sample/StartupAnonEndpoint.cs?name=snippet)]

### <a name="custom-handling-of-authorization-failures"></a><span data-ttu-id="9219f-273">Benutzerdefinierte Behandlung von Autorisierungsfehlern</span><span class="sxs-lookup"><span data-stu-id="9219f-273">Custom handling of authorization failures</span></span>

<span data-ttu-id="9219f-274">Die benutzerdefinierte Behandlung von Autorisierungsfehlern ist mit der neuen [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs)-Schnittstelle, die von der [Middleware](xref:fundamentals/middleware/index) zur [Autorisierung](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) aufgerufen wird, jetzt einfacher.</span><span class="sxs-lookup"><span data-stu-id="9219f-274">Custom handling of authorization failures is now easier with the new [IAuthorizationMiddlewareResultHandler](https://github.com/dotnet/aspnetcore/blob/v5.0.0-rc.1.20451.17/src/Security/Authorization/Policy/src/IAuthorizationMiddlewareResultHandler.cs) interface that is invoked by the [authorization](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A) [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="9219f-275">Die Standardimplementierung bleibt die gleiche, aber ein benutzerdefinierter Handler kann in Dependency Injection registriert werden. So werden benutzerdefinierte HTTP-Antworten zugelassen, die sich danach richten, warum die Autorisierung fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="9219f-275">The default implementation remains the same, but a custom handler can be registered in [Dependency injection, which allows custom HTTP responses based on why authorization failed.</span></span> <span data-ttu-id="9219f-276">Weitere Informationen finden Sie in [diesem Beispiel](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs), das die Verwendung von `IAuthorizationMiddlewareResultHandler` veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="9219f-276">See [this sample](https://github.com/dotnet/aspnetcore/blob/master/src/Security/samples/CustomAuthorizationFailureResponse/Authorization/SampleAuthorizationMiddlewareResultHandler.cs) that demonstrates usage of the `IAuthorizationMiddlewareResultHandler`.</span></span>

### <a name="authorization-when-using-endpoint-routing"></a><span data-ttu-id="9219f-277">Autorisierung bei Verwendung des Endpunktroutings</span><span class="sxs-lookup"><span data-stu-id="9219f-277">Authorization when using endpoint routing</span></span>

<span data-ttu-id="9219f-278">Die Autorisierung bei Verwendung des Endpunktroutings empfängt jetzt das `HttpContext`-Element statt der Endpunktinstanz.</span><span class="sxs-lookup"><span data-stu-id="9219f-278">Authorization when using endpoint routing now receives the `HttpContext` rather than the endpoint instance.</span></span> <span data-ttu-id="9219f-279">Dadurch kann die Autorisierungsmiddleware auf die `RouteData`-Eigenschaft und andere Eigenschaften von `HttpContext` zugreifen, auf die über die <xref:Microsoft.AspNetCore.Http.Endpoint>-Klasse nicht zugegriffen werden konnte.</span><span class="sxs-lookup"><span data-stu-id="9219f-279">This allows the authorization middleware to access the `RouteData` and other properties of the `HttpContext` that were not accessible though the <xref:Microsoft.AspNetCore.Http.Endpoint> class.</span></span> <span data-ttu-id="9219f-280">Der Endpunkt kann mit [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A) aus dem Kontext abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-280">The endpoint can be fetched from the context using [context.GetEndpoint](xref:Microsoft.AspNetCore.Http.EndpointHttpContextExtensions.GetEndpoint%2A).</span></span>

### <a name="role-based-access-control-with-kerberos-authentication-and-ldap-on-linux"></a><span data-ttu-id="9219f-281">Rollenbasierte Zugriffssteuerung mit Kerberos-Authentifizierung und LDAP unter Linux</span><span class="sxs-lookup"><span data-stu-id="9219f-281">Role-based access control with Kerberos authentication and LDAP on Linux</span></span>

<span data-ttu-id="9219f-282">Weitere Informationen finden Sie unter [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac) (Kerberos-Authentifizierung und rollenbasierte Zugriffssteuerung (RBAC)).</span><span class="sxs-lookup"><span data-stu-id="9219f-282">See [Kerberos authentication and role-based access control (RBAC)](xref:security/authentication/windowsauth#rbac)</span></span>

## <a name="api-improvements"></a><span data-ttu-id="9219f-283">API-Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="9219f-283">API improvements</span></span>

### <a name="json-extension-methods-for-httprequest-and-httpresponse"></a><span data-ttu-id="9219f-284">JSON-Erweiterungsmethoden für HttpRequest und HttpResponse</span><span class="sxs-lookup"><span data-stu-id="9219f-284">JSON extension methods for HttpRequest and HttpResponse</span></span>

<span data-ttu-id="9219f-285">JSON-Daten können mit den neuen Erweiterungsmethoden <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> und `WriteAsJsonAsync` aus einem `HttpRequest`- und `HttpResponse`-Element gelesen und geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-285">JSON data can be read and written to from an `HttpRequest` and `HttpResponse` using the new <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> and `WriteAsJsonAsync` extension methods.</span></span> <span data-ttu-id="9219f-286">Diese Erweiterungsmethoden verarbeiten die JSON-Daten mithilfe des Serialisierungsmoduls [System.Text.Json](xref:System.Text.Json).</span><span class="sxs-lookup"><span data-stu-id="9219f-286">These extension methods use the [System.Text.Json](xref:System.Text.Json) serializer to handle the JSON data.</span></span> <span data-ttu-id="9219f-287">Mit der neuen `HasJsonContentType`-Erweiterungsmethode lässt sich auch überprüfen, ob eine Anforderung einen JSON-Inhaltstyp aufweist.</span><span class="sxs-lookup"><span data-stu-id="9219f-287">The new `HasJsonContentType` extension method can also check if a request has a JSON content type.</span></span>

<span data-ttu-id="9219f-288">Die JSON-Erweiterungsmethoden können mit [Endpunktrouting](xref:fundamentals/routing) kombiniert werden, um JSON-APIs in einem Programmierstil zu erstellen, der als \***Route-zu-Code** _ bezeichnet wird.</span><span class="sxs-lookup"><span data-stu-id="9219f-288">The JSON extension methods can be combined with [endpoint routing](xref:fundamentals/routing) to create JSON APIs in a style of programming we call \***route to code** _.</span></span> <span data-ttu-id="9219f-289">Diese neue Option bietet Entwicklern die Möglichkeit, grundlegende JSON-APIs auf einfache Art und Weise zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="9219f-289">It is a new option for developers who want to create basic JSON APIs in a lightweight way.</span></span> <span data-ttu-id="9219f-290">Beispielsweise kann eine Web-App, die nur über wenige Endpunkte verfügt, anstatt der vollständigen Funktionalität von ASP.NET Core-MVC auch Route-zu-Code nutzen:</span><span class="sxs-lookup"><span data-stu-id="9219f-290">For example, a web app that has only a handful of endpoints might choose to use route to code rather than the full functionality of ASP.NET Core MVC:</span></span>

```csharp
endpoints.MapGet("/weather/{city:alpha}", async context =>
{
    var city = (string)context.Request.RouteValues["city"];
    var weather = GetFromDatabase(city);

    await context.Response.WriteAsJsonAsync(weather);
});
```

<span data-ttu-id="9219f-291">Weitere Informationen zu den neuen JSON-Erweiterungsmethoden und zu Route-zu-Code finden Sie in dieser [.NET-Show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span><span class="sxs-lookup"><span data-stu-id="9219f-291">For more information on the new JSON extension methods and route to code, see [this .NET show](https://channel9.msdn.com/Shows/On-NET/ASPNET-Core-Series-Route-to-Code).</span></span>

### <a name="systemdiagnosticsactivity"></a><span data-ttu-id="9219f-292">System.Diagnostics.Activity</span><span class="sxs-lookup"><span data-stu-id="9219f-292">System.Diagnostics.Activity</span></span>

<span data-ttu-id="9219f-293">Als Standardformat für <xref:System.Diagnostics.Activity?displayProperty=fullName> wird jetzt standardmäßig das W3C-Format verwendet.</span><span class="sxs-lookup"><span data-stu-id="9219f-293">The default format for <xref:System.Diagnostics.Activity?displayProperty=fullName> now defaults to the W3C format.</span></span> <span data-ttu-id="9219f-294">Dadurch wird die Unterstützung für verteilte Ablaufverfolgung in ASP.NET Core standardmäßig mit mehr Frameworks interoperabel.</span><span class="sxs-lookup"><span data-stu-id="9219f-294">This makes distributed tracing support in ASP.NET Core interoperable with more frameworks by default.</span></span>

### <a name="frombodyattribute"></a><span data-ttu-id="9219f-295">FromBodyAttribute</span><span class="sxs-lookup"><span data-stu-id="9219f-295">FromBodyAttribute</span></span>

<span data-ttu-id="9219f-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> unterstützt nun die Konfiguration einer Option, mit der diese Parameter oder Eigenschaften als optional betrachtet werden können:</span><span class="sxs-lookup"><span data-stu-id="9219f-296"><xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute> now supports configuring an option that allows these parameters or properties to be considered optional:</span></span>

```csharp
public IActionResult Post([FromBody(EmptyBodyBehavior = EmptyBodyBehavior.Allow)]
                          MyModel model)
{
    ...
}
```

## <a name="miscellaneous-improvements"></a><span data-ttu-id="9219f-297">Sonstige Verbesserungen</span><span class="sxs-lookup"><span data-stu-id="9219f-297">Miscellaneous improvements</span></span>

<span data-ttu-id="9219f-298">Ab sofort werden auf ASP.NET-Core-Assemblys [Nullable-Anmerkungen](/dotnet/csharp/nullable-references#attributes-describe-apis) angewendet.</span><span class="sxs-lookup"><span data-stu-id="9219f-298">We’ve started applying [nullable annotations](/dotnet/csharp/nullable-references#attributes-describe-apis) to ASP.NET Core assemblies.</span></span> <span data-ttu-id="9219f-299">Außerdem ist vorgesehen, den größten Teil der allgemeinen öffentlichen API-Oberfläche des .NET 5-Frameworks mit Anmerkungen zu versehen.</span><span class="sxs-lookup"><span data-stu-id="9219f-299">We plan to annotate most of the common public API surface of the .NET 5 framework.</span></span> <!-- Review: what's the impact of this? How does it work? Need more info.  Check the link I added -->

### <a name="control-startup-class-activation"></a><span data-ttu-id="9219f-300">Steuerung der Startklassenaktivierung</span><span class="sxs-lookup"><span data-stu-id="9219f-300">Control Startup class activation</span></span>

<span data-ttu-id="9219f-301">Es wurde eine zusätzliche <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A>-Überladung hinzugefügt, mit der eine App eine Factorymethode zum Steuern der `Startup`-Klassenaktivierung bereitstellen kann.</span><span class="sxs-lookup"><span data-stu-id="9219f-301">An additional <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseStartup%2A> overload has been added that lets an app provide a factory method for controlling `Startup` class activation.</span></span> <span data-ttu-id="9219f-302">Das Steuern der `Startup`-Klassenaktivierung ist nützlich, um zusätzliche Parameter an `Startup` zu übergeben, die zusammen mit dem Host initialisiert werden:</span><span class="sxs-lookup"><span data-stu-id="9219f-302">Controlling `Startup` class activation is useful to pass additional parameters to `Startup` that are initialized along with the host:</span></span>

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var logger = CreateLogger();
        var host = Host.CreateDefaultBuilder()
            .ConfigureWebHost(builder =>
            {
                builder.UseStartup(context => new Startup(logger));
            })
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="auto-refresh-with-dotnet-watch"></a><span data-ttu-id="9219f-303">Automatische Aktualisierung mit „dotnet watch“</span><span class="sxs-lookup"><span data-stu-id="9219f-303">Auto refresh with dotnet watch</span></span>

<span data-ttu-id="9219f-304">In .NET 5 wird beim Ausführen von [dotnet watch](xref:tutorials/dotnet-watch) in einem ASP.NET Core-Projekt der Standardbrowser gestartet und automatisch aktualisiert, wenn Änderungen am Code vorgenommen werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-304">In .NET 5, running [dotnet watch](xref:tutorials/dotnet-watch) on an ASP.NET Core project both launches the default browser and auto refreshes the browser as changes are made to the code.</span></span> <span data-ttu-id="9219f-305">Dies bedeutet, Sie haben folgende Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="9219f-305">This means you can:</span></span>

<span data-ttu-id="9219f-306">_ Öffnen Sie ein ASP.NET Core-Projekt in einem Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="9219f-306">_ Open an ASP.NET Core project in a text editor.</span></span>
* <span data-ttu-id="9219f-307">Führen Sie `dotnet watch` aus.</span><span class="sxs-lookup"><span data-stu-id="9219f-307">Run `dotnet watch`.</span></span>
* <span data-ttu-id="9219f-308">Konzentrieren Sie sich auf die Codeänderungen, während das Tool das Neuerstellen, Neustarten und erneute Laden der App übernimmt.</span><span class="sxs-lookup"><span data-stu-id="9219f-308">Focus on the code changes while the tooling handles rebuilding, restarting, and reloading the app.</span></span>

<span data-ttu-id="9219f-309">Die Funktion zur automatischen Aktualisierung soll in Zukunft in Visual Studio eingeführt werden.</span><span class="sxs-lookup"><span data-stu-id="9219f-309">We hope to bring the auto refresh functionality to Visual Studio in the future.</span></span>

### <a name="console-logger-formatter"></a><span data-ttu-id="9219f-310">Formatierer für die Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="9219f-310">Console Logger Formatter</span></span>

<span data-ttu-id="9219f-311">Es wurden Verbesserungen an der Konsolenprotokollierung in der `Microsoft.Extensions.Logging`-Bibliothek vorgenommen.</span><span class="sxs-lookup"><span data-stu-id="9219f-311">Improvements have been made to the console log provider in the `Microsoft.Extensions.Logging` library.</span></span> <span data-ttu-id="9219f-312">Entwickler können nun ein benutzerdefiniertes `ConsoleFormatter`-Programm implementieren, um Formatierungen und Farbgebungen der Konsolenausgabe vollständig zu steuern.</span><span class="sxs-lookup"><span data-stu-id="9219f-312">Developers can now implement a custom `ConsoleFormatter` to exercise complete control over formatting and colorization of the console output.</span></span> <span data-ttu-id="9219f-313">Die Formatierungs-APIs ermöglichen eine umfangreiche Formatierung, indem eine Teilmenge der VT-100-Escapesequenzen implementiert wird.</span><span class="sxs-lookup"><span data-stu-id="9219f-313">The formatter APIs allow for rich formatting by implementing a subset of the VT-100 escape sequences.</span></span> <span data-ttu-id="9219f-314">VT-100 wird von den meisten modernen Terminals unterstützt.</span><span class="sxs-lookup"><span data-stu-id="9219f-314">VT-100 is supported by most modern terminals.</span></span> <span data-ttu-id="9219f-315">Die Konsolenprotokollierung kann Escapesequenzen auf nicht unterstützten Terminals analysieren, sodass Entwickler einen einzelnen Formatierer für alle Terminals erstellen können.</span><span class="sxs-lookup"><span data-stu-id="9219f-315">The console logger can parse out escape sequences on unsupported terminals allowing developers to author a single formatter for all terminals.</span></span>

### <a name="json-console-logger"></a><span data-ttu-id="9219f-316">JSON-Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="9219f-316">JSON Console Logger</span></span>

<span data-ttu-id="9219f-317">Zusätzlich zur Unterstützung von benutzerdefinierten Formatierern gibt es nun auch einen integrierten JSON-Formatierer, der strukturierte JSON-Protokolle an die Konsole ausgibt.</span><span class="sxs-lookup"><span data-stu-id="9219f-317">In addition to support for custom formatters, we’ve also added a built-in JSON formatter that emits structured JSON logs to the console.</span></span> <span data-ttu-id="9219f-318">Der folgende Code zeigt, wie Sie von der Standardprotokollierung zu JSON wechseln:</span><span class="sxs-lookup"><span data-stu-id="9219f-318">The following code shows how to switch from the default logger to JSON:</span></span>

[!code-csharp[](~/release-notes/sample/ProgramJsonLog.cs?name=snippet)]

<span data-ttu-id="9219f-319">An die Konsole ausgegebene Protokollmeldungen sind JSON-formatiert:</span><span class="sxs-lookup"><span data-stu-id="9219f-319">Log messages emitted to the console are JSON formatted:</span></span>

```json
{
  "EventId": 0,
  "LogLevel": "Information",
  "Category": "Microsoft.Hosting.Lifetime",
  "Message": "Now listening on: https://localhost:5001",
  "State": {
    "Message": "Now listening on: https://localhost:5001",
    "address": "https://localhost:5001",
    "{OriginalFormat}": "Now listening on: {address}"
  }
}
```
