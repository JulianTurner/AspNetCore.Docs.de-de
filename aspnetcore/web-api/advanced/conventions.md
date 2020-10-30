---
title: Verwenden von Web-API-Konventionen
author: pranavkm
description: Informationen zu Web-API-Konventionen in ASP.NET Core
monikerRange: '>= aspnetcore-2.2'
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
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
uid: web-api/advanced/conventions
ms.openlocfilehash: 0c5ea8ba69e4c6287afce1771ac9cee65bb188a8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052538"
---
# <a name="use-web-api-conventions"></a><span data-ttu-id="66598-103">Verwenden von Web-API-Konventionen</span><span class="sxs-lookup"><span data-stu-id="66598-103">Use web API conventions</span></span>

<span data-ttu-id="66598-104">Von [Pranav Krishnamoorthy](https://github.com/pranavkm) und [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="66598-104">By [Pranav Krishnamoorthy](https://github.com/pranavkm) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="66598-105">ASP.NET Core 2.2 und höher umfasst eine Möglichkeit, allgemeine [API-Dokumentation](xref:tutorials/web-api-help-pages-using-swagger) zu extrahieren und diese auf mehrere Aktionen, Controller sowie alle Controller in einer Assembly anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="66598-105">ASP.NET Core 2.2 and later includes a way to extract common [API documentation](xref:tutorials/web-api-help-pages-using-swagger) and apply it to multiple actions, controllers, or all controllers within an assembly.</span></span> <span data-ttu-id="66598-106">Web-API-Konventionen sind ein Ersatz für das Dekorieren einzelner Aktionen mit [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) .</span><span class="sxs-lookup"><span data-stu-id="66598-106">Web API conventions are a substitute for decorating individual actions with [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute).</span></span>

<span data-ttu-id="66598-107">Eine Konvention ermöglicht Ihnen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="66598-107">A convention allows you to:</span></span>

* <span data-ttu-id="66598-108">Definieren der gebräuchlichsten Rückgabetypen und Statuscodes, die von einem bestimmten Typ von Aktion zurückgegeben werden</span><span class="sxs-lookup"><span data-stu-id="66598-108">Define the most common return types and status codes returned from a specific type of action.</span></span>
* <span data-ttu-id="66598-109">Erkennen von Aktionen, die vom definierten Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="66598-109">Identify actions that deviate from the defined standard.</span></span>

<span data-ttu-id="66598-110">ASP.NET Core MVC 2.2 und höher umfasst eine Reihe von Standardkonventionen in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span><span class="sxs-lookup"><span data-stu-id="66598-110">ASP.NET Core MVC 2.2 and later includes a set of default conventions in <xref:Microsoft.AspNetCore.Mvc.DefaultApiConventions?displayProperty=fullName>.</span></span> <span data-ttu-id="66598-111">Die Konventionen basieren auf dem Controller ( *ValuesController.cs* ), der in der ASP.NET Core-Projektvorlage **API** bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="66598-111">The conventions are based on the controller ( *ValuesController.cs* ) provided in the ASP.NET Core **API** project template.</span></span> <span data-ttu-id="66598-112">Wenn Ihre Aktionen den Mustern in der Vorlage entsprechen, sollten die Standardkonventionen für Sie ausreichend sein.</span><span class="sxs-lookup"><span data-stu-id="66598-112">If your actions follow the patterns in the template, you should be successful using the default conventions.</span></span> <span data-ttu-id="66598-113">Wenn die Standardkonventionen Ihre Anforderungen nicht erfüllen, lesen Sie [Erstellen von Web-API-Konventionen](#create-web-api-conventions).</span><span class="sxs-lookup"><span data-stu-id="66598-113">If the default conventions don't meet your needs, see [Create web API conventions](#create-web-api-conventions).</span></span>

<span data-ttu-id="66598-114"><xref:Microsoft.AspNetCore.Mvc.ApiExplorer> erkennt zur Laufzeit Konventionen.</span><span class="sxs-lookup"><span data-stu-id="66598-114">At runtime, <xref:Microsoft.AspNetCore.Mvc.ApiExplorer> understands conventions.</span></span> <span data-ttu-id="66598-115">`ApiExplorer` ist die Abstraktion von MVC, über die mit [OpenAPI](https://www.openapis.org/)-Dokumentgeneratoren kommuniziert wird (OpenAPI ist auch als Swagger bekannt).</span><span class="sxs-lookup"><span data-stu-id="66598-115">`ApiExplorer` is MVC's abstraction to communicate with [OpenAPI](https://www.openapis.org/) (also known as Swagger) document generators.</span></span> <span data-ttu-id="66598-116">Die Attribute aus der angewendeten Konvention werden einer Aktion zugeordnet und zur OpenAPI-Dokumentation der Aktion hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="66598-116">Attributes from the applied convention are associated with an action and are included in the action's OpenAPI documentation.</span></span> <span data-ttu-id="66598-117">Auch [API-Analysetools](xref:web-api/advanced/analyzers) erkennen Konventionen.</span><span class="sxs-lookup"><span data-stu-id="66598-117">[API analyzers](xref:web-api/advanced/analyzers) also understand conventions.</span></span> <span data-ttu-id="66598-118">Wenn Ihre Aktion keiner Konvention folgt (also z. B. einen Statuscode zurückgibt, der nicht durch die angewendete Konvention dokumentiert ist), werden Sie mit einer Warnung aufgefordert, den Statuscode zu dokumentieren.</span><span class="sxs-lookup"><span data-stu-id="66598-118">If your action is unconventional (for example, it returns a status code that isn't documented by the applied convention), a warning encourages you to document the status code.</span></span>

<span data-ttu-id="66598-119">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="66598-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/conventions/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="apply-web-api-conventions"></a><span data-ttu-id="66598-120">Anwenden von Web-API-Konventionen</span><span class="sxs-lookup"><span data-stu-id="66598-120">Apply web API conventions</span></span>

<span data-ttu-id="66598-121">Konventionen werden nicht miteinander kombiniert. Jede Aktion kann genau einer Konvention zugeordnet werden.</span><span class="sxs-lookup"><span data-stu-id="66598-121">Conventions don't compose; each action may be associated with exactly one convention.</span></span> <span data-ttu-id="66598-122">Speziellere Konventionen haben Vorrang vor weniger speziellen Konventionen.</span><span class="sxs-lookup"><span data-stu-id="66598-122">More specific conventions take precedence over less specific conventions.</span></span> <span data-ttu-id="66598-123">Die Auswahl ist nicht deterministisch, wenn mindestens zwei Konventionen mit gleicher Priorität für eine Aktion gelten.</span><span class="sxs-lookup"><span data-stu-id="66598-123">The selection is non-deterministic when two or more conventions of the same priority apply to an action.</span></span> <span data-ttu-id="66598-124">Sie können eine der folgenden Optionen auswählen, um angefangen bei der genausten Konvention bis hin zur ungenausten eine Konvention auf eine Aktion anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="66598-124">The following options exist to apply a convention to an action, from the most specific to the least specific:</span></span>

1. <span data-ttu-id="66598-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute`&mdash;Gilt für einzelne Aktionen und gibt den Typ der Konvention und der Konvention an, die angewendet wird.</span><span class="sxs-lookup"><span data-stu-id="66598-125">`Microsoft.AspNetCore.Mvc.ApiConventionMethodAttribute` &mdash; Applies to individual actions and specifies the convention type and the convention method that applies.</span></span>

    <span data-ttu-id="66598-126">Im folgenden Beispiel wird die `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`-Konventionsmethode des Standardkonventionstyps auf die `Update`-Aktion angewendet:</span><span class="sxs-lookup"><span data-stu-id="66598-126">In the following example, the default convention type's `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method is applied to the `Update` action:</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionMethod&highlight=3)]

    <span data-ttu-id="66598-127">Die `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put`-Konventionsmethode wendet die folgenden Attribute auf die Aktion an:</span><span class="sxs-lookup"><span data-stu-id="66598-127">The `Microsoft.AspNetCore.Mvc.DefaultApiConventions.Put` convention method applies the following attributes to the action:</span></span>

    ```csharp
    [ProducesDefaultResponseType]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    ```

    <span data-ttu-id="66598-128">Weitere Informationen zu `[ProducesDefaultResponseType]` finden Sie unter [Standardantwort](https://swagger.io/docs/specification/describing-responses/#default).</span><span class="sxs-lookup"><span data-stu-id="66598-128">For more information on `[ProducesDefaultResponseType]`, see [Default Response](https://swagger.io/docs/specification/describing-responses/#default).</span></span>

1. <span data-ttu-id="66598-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, auf einen Controller angewendet &mdash; Wendet den angegebenen Konventionstyp auf alle Controlleraktionen an.</span><span class="sxs-lookup"><span data-stu-id="66598-129">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to a controller &mdash; Applies the specified convention type to all actions on the controller.</span></span> <span data-ttu-id="66598-130">Eine Konventionsmethode ist mit Hinweisen markiert, mit denen die Aktionen bestimmt werden, für die die Konventionsmethode gilt.</span><span class="sxs-lookup"><span data-stu-id="66598-130">A convention method is marked with hints that determine the actions to which the convention method applies.</span></span> <span data-ttu-id="66598-131">Weitere Informationen zu Hinweisen finden Sie unter [Erstellen von Web-API-Konventionen](#create-web-api-conventions)).</span><span class="sxs-lookup"><span data-stu-id="66598-131">For more information on hints, see [Create web API conventions](#create-web-api-conventions)).</span></span>

    <span data-ttu-id="66598-132">Im folgenden Beispiel werden alle standardmäßigen Konventionen auf alle Aktionen in *ContactsConventionController* angewendet:</span><span class="sxs-lookup"><span data-stu-id="66598-132">In the following example, the default set of conventions is applied to all actions in *ContactsConventionController* :</span></span>

    [!code-csharp[](conventions/sample/Controllers/ContactsConventionController.cs?name=snippet_ApiConventionTypeAttribute&highlight=2)]

1. <span data-ttu-id="66598-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute`, auf eine Assembly angewendet &mdash; Wendet den angegebenen Konventionstyp auf alle Controller in der aktuellen Assembly an.</span><span class="sxs-lookup"><span data-stu-id="66598-133">`Microsoft.AspNetCore.Mvc.ApiConventionTypeAttribute` applied to an assembly &mdash; Applies the specified convention type to all controllers in the current assembly.</span></span> <span data-ttu-id="66598-134">Es empfiehlt sich, Attribute auf Assemblyebene in der *Startup.cs* -Datei anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="66598-134">As a recommendation, apply assembly-level attributes in the *Startup.cs* file.</span></span>

    <span data-ttu-id="66598-135">Im folgenden Beispiel werden alle standardmäßigen Konventionen auf alle Controller in der Assembly angewendet:</span><span class="sxs-lookup"><span data-stu-id="66598-135">In the following example, the default set of conventions is applied to all controllers in the assembly:</span></span>

    [!code-csharp[](conventions/sample/Startup.cs?name=snippet_ApiConventionTypeAttribute&highlight=1)]

## <a name="create-web-api-conventions"></a><span data-ttu-id="66598-136">Erstellen von Web-API-Konventionen</span><span class="sxs-lookup"><span data-stu-id="66598-136">Create web API conventions</span></span>

<span data-ttu-id="66598-137">Wenn die Standard-API-Konventionen Ihre Anforderungen nicht erfüllen, erstellen Sie eigene Konventionen.</span><span class="sxs-lookup"><span data-stu-id="66598-137">If the default API conventions don't meet your needs, create your own conventions.</span></span> <span data-ttu-id="66598-138">Eine Konvention ist:</span><span class="sxs-lookup"><span data-stu-id="66598-138">A convention is:</span></span>

* <span data-ttu-id="66598-139">Ein statischer Typ mit Methoden.</span><span class="sxs-lookup"><span data-stu-id="66598-139">A static type with methods.</span></span>
* <span data-ttu-id="66598-140">Geeignet, [Antworttypen](#response-types) und [Benennungsanforderungen](#naming-requirements) für Aktionen zu definieren.</span><span class="sxs-lookup"><span data-stu-id="66598-140">Capable of defining [response types](#response-types) and [naming requirements](#naming-requirements) on actions.</span></span>

### <a name="response-types"></a><span data-ttu-id="66598-141">Antworttypen</span><span class="sxs-lookup"><span data-stu-id="66598-141">Response types</span></span>

<span data-ttu-id="66598-142">Diese Methoden werden mit den Attributen `[ProducesResponseType]` oder `[ProducesDefaultResponseType]` versehen.</span><span class="sxs-lookup"><span data-stu-id="66598-142">These methods are annotated with `[ProducesResponseType]` or `[ProducesDefaultResponseType]` attributes.</span></span> <span data-ttu-id="66598-143">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66598-143">For example:</span></span>

```csharp
public static class MyAppConventions
{
    [ProducesResponseType(StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public static void Find(int id)
    {
    }
}
```

<span data-ttu-id="66598-144">Sind keine spezielleren Metadatenattribute vorhanden, erzwingt ein Anwenden dieser Konvention auf eine Assembly, dass:</span><span class="sxs-lookup"><span data-stu-id="66598-144">If more specific metadata attributes are absent, applying this convention to an assembly enforces that:</span></span>

* <span data-ttu-id="66598-145">Die Konventionsmethode für jede Aktion namens `Find` gilt.</span><span class="sxs-lookup"><span data-stu-id="66598-145">The convention method applies to any action named `Find`.</span></span>
* <span data-ttu-id="66598-146">Ein Parameter namens `id` in der `Find`-Aktion vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="66598-146">A parameter named `id` is present on the `Find` action.</span></span>

### <a name="naming-requirements"></a><span data-ttu-id="66598-147">Benennungsanforderungen</span><span class="sxs-lookup"><span data-stu-id="66598-147">Naming requirements</span></span>

<span data-ttu-id="66598-148">Die Attribute `[ApiConventionNameMatch]` und `[ApiConventionTypeMatch]` können auf die Konventionsmethode angewendet werden, die die Aktionen bestimmt, für die sie gelten.</span><span class="sxs-lookup"><span data-stu-id="66598-148">The `[ApiConventionNameMatch]` and `[ApiConventionTypeMatch]` attributes can be applied to the convention method that determines the actions to which they apply.</span></span> <span data-ttu-id="66598-149">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66598-149">For example:</span></span>

```csharp
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(StatusCodes.Status404NotFound)]
[ApiConventionNameMatch(ApiConventionNameMatchBehavior.Prefix)]
public static void Find(
    [ApiConventionNameMatch(ApiConventionNameMatchBehavior.Suffix)]
    int id)
{ }
```

<span data-ttu-id="66598-150">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="66598-150">In the preceding example:</span></span>

* <span data-ttu-id="66598-151">Die auf die Methode angewendete Option `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` gibt an, dass die Konvention für jede Aktion gilt, die das Präfix „Find“ hat.</span><span class="sxs-lookup"><span data-stu-id="66598-151">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Prefix` option applied to the method indicates that the convention matches any action prefixed with "Find".</span></span> <span data-ttu-id="66598-152">Beispiele für übereinstimmende Aktionen sind `Find`, `FindPet` und `FindById`.</span><span class="sxs-lookup"><span data-stu-id="66598-152">Examples of matching actions include `Find`, `FindPet`, and `FindById`.</span></span>
* <span data-ttu-id="66598-153">Die auf den Parameter angewendete Option `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` gibt an, dass die Konvention für jede Methode gilt, die genau einen Parameter hat, der mit dem Suffix „id“ endet.</span><span class="sxs-lookup"><span data-stu-id="66598-153">The `Microsoft.AspNetCore.Mvc.ApiExplorer.ApiConventionNameMatchBehavior.Suffix` applied to the parameter indicates that the convention matches methods with exactly one parameter ending in the suffix identifier.</span></span> <span data-ttu-id="66598-154">Beispiele sind Parameter wie `id` und `petId`.</span><span class="sxs-lookup"><span data-stu-id="66598-154">Examples include parameters such as `id` or `petId`.</span></span> <span data-ttu-id="66598-155">Vergleichbar kann `ApiConventionTypeMatch` auf Typen angewendet, um den Parametertyp einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="66598-155">`ApiConventionTypeMatch` can be similarly applied to types to constrain the parameter type.</span></span> <span data-ttu-id="66598-156">Ein `params[]`-Argument gibt verbleibende Parameter an, die nicht explizit abgeglichen werden müssen.</span><span class="sxs-lookup"><span data-stu-id="66598-156">A `params[]` argument indicates remaining parameters that don't need to be explicitly matched.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="66598-157">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="66598-157">Additional resources</span></span>

* <xref:web-api/advanced/analyzers>
* <xref:tutorials/web-api-help-pages-using-swagger>
