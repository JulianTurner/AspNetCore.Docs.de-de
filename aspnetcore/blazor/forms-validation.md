---
title: Blazor-Formulare und -Validierung in ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Formular- und Feldvalidierungsszenarios in Blazor verwendet werden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/18/2020
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
uid: blazor/forms-validation
ms.openlocfilehash: b485a62c61d404a91134f49cf2a49134ec9f5123
ms.sourcegitcommit: 8ed9a413bdc2d665ad11add8828898d726ccb106
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280386"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a><span data-ttu-id="5eb2b-103">Blazor-Formulare und -Validierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5eb2b-103">ASP.NET Core Blazor forms and validation</span></span>

<span data-ttu-id="5eb2b-104">Von [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5eb2b-104">By [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/), and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="5eb2b-105">Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-105">Forms and validation are supported in Blazor using [data annotations](xref:mvc/models/validation).</span></span>

<span data-ttu-id="5eb2b-106">Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-106">The following `ExampleModel` type defines validation logic using data annotations:</span></span>

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

<span data-ttu-id="5eb2b-107">Ein Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Komponente definiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-107">A form is defined using the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> component.</span></span> <span data-ttu-id="5eb2b-108">Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-108">The following form demonstrates typical elements, components, and Razor code:</span></span>

```razor
<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <InputText id="name" @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="5eb2b-109">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-109">In the preceding example:</span></span>

* <span data-ttu-id="5eb2b-110">Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-110">The form validates user input in the `name` field using the validation defined in the `ExampleModel` type.</span></span> <span data-ttu-id="5eb2b-111">Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-111">The model is created in the component's `@code` block and held in a private field (`exampleModel`).</span></span> <span data-ttu-id="5eb2b-112">Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-112">The field is assigned to the `Model` attribute of the `<EditForm>` element.</span></span>
* <span data-ttu-id="5eb2b-113">Der `@bind-Value` der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente bindet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-113">The <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `@bind-Value` binds:</span></span>
  * <span data-ttu-id="5eb2b-114">Die Modelleigenschaft (`exampleModel.Name`) an die `Value`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-114">The model property (`exampleModel.Name`) to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `Value` property.</span></span> <span data-ttu-id="5eb2b-115">Weitere Informationen zur Eigenschaftenbindung finden Sie unter <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-115">For more information on property binding, see <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.</span></span>
  * <span data-ttu-id="5eb2b-116">Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-116">A change event delegate to the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component's `ValueChanged` property.</span></span>
* <span data-ttu-id="5eb2b-117">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-[Validierungskomponente](#validator-components) fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-117">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> [validator component](#validator-components) attaches validation support using data annotations.</span></span>
* <span data-ttu-id="5eb2b-118">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst Validierungsnachrichten zusammen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-118">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes validation messages.</span></span>
* <span data-ttu-id="5eb2b-119">`HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-119">`HandleValidSubmit` is triggered when the form successfully submits (passes validation).</span></span>

## <a name="built-in-forms-components"></a><span data-ttu-id="5eb2b-120">Integrierte Formularkomponenten</span><span class="sxs-lookup"><span data-stu-id="5eb2b-120">Built-in forms components</span></span>

<span data-ttu-id="5eb2b-121">Einige integrierte Komponenten sind verfügbar, um Benutzereingaben zu empfangen und zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-121">A set of built-in components are available to receive and validate user input.</span></span> <span data-ttu-id="5eb2b-122">Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-122">Inputs are validated when they're changed and when a form is submitted.</span></span> <span data-ttu-id="5eb2b-123">In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-123">Available input components are shown in the following table.</span></span>

::: moniker range=">= aspnetcore-5.0"

| <span data-ttu-id="5eb2b-124">Eingabekomponenten</span><span class="sxs-lookup"><span data-stu-id="5eb2b-124">Input component</span></span> | <span data-ttu-id="5eb2b-125">Wird gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="5eb2b-125">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| [`InputRadio`](#radio-buttons) | `<input type="radio">` |
| [`InputRadioGroup`](#radio-buttons) | `<input type="radio">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

::: moniker range="< aspnetcore-5.0"

| <span data-ttu-id="5eb2b-126">Eingabekomponenten</span><span class="sxs-lookup"><span data-stu-id="5eb2b-126">Input component</span></span> | <span data-ttu-id="5eb2b-127">Wird gerendert als&hellip;</span><span class="sxs-lookup"><span data-stu-id="5eb2b-127">Rendered as&hellip;</span></span> |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |

::: moniker-end

<span data-ttu-id="5eb2b-128">Alle Eingabekomponenten einschließlich <xref:Microsoft.AspNetCore.Components.Forms.EditForm> unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-128">All of the input components, including <xref:Microsoft.AspNetCore.Components.Forms.EditForm>, support arbitrary attributes.</span></span> <span data-ttu-id="5eb2b-129">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-129">Any attribute that doesn't match a component parameter is added to the rendered HTML element.</span></span>

<span data-ttu-id="5eb2b-130">Eingabekomponenten weisen ein Standardverhalten für Validierungen auf, wenn ein Feld geändert wird, einschließlich der Aktualisierung der CSS-Klasse des Felds, um den Feldzustand darzustellen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-130">Input components provide default behavior for validating when a field is changed, including updating the field CSS class to reflect the field state.</span></span> <span data-ttu-id="5eb2b-131">Einige Komponenten beinhalten hilfreiche Parsinglogik.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-131">Some components include useful parsing logic.</span></span> <span data-ttu-id="5eb2b-132"><xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> und <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß, indem nicht parsbare Werte als Validierungsfehler registriert werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-132">For example, <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> and <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> handle unparseable values gracefully by registering unparseable values as validation errors.</span></span> <span data-ttu-id="5eb2b-133">Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-133">Types that can accept null values also support nullability of the target field (for example, `int?`).</span></span>

<span data-ttu-id="5eb2b-134">Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-134">The following `Starship` type defines validation logic using a larger set of properties and data annotations than the earlier `ExampleModel`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    [Required]
    [StringLength(16, ErrorMessage = "Identifier too long (16 character limit).")]
    public string Identifier { get; set; }

    public string Description { get; set; }

    [Required]
    public string Classification { get; set; }

    [Range(1, 100000, ErrorMessage = "Accommodation invalid (1-100000).")]
    public int MaximumAccommodation { get; set; }

    [Required]
    [Range(typeof(bool), "true", "true", 
        ErrorMessage = "This form disallows unapproved ships.")]
    public bool IsValidatedDesign { get; set; }

    [Required]
    public DateTime ProductionDate { get; set; }
}
```

<span data-ttu-id="5eb2b-135">Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-135">In the preceding example, `Description` is optional because no data annotations are present.</span></span>

<span data-ttu-id="5eb2b-136">Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-136">The following form validates user input using the validation defined in the `Starship` model:</span></span>

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" />
        </label>
    </p>

    <button type="submit">Submit</button>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>, 
        &copy;1966-2019 CBS Studios, Inc. and 
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        ...
    }
}
```

<span data-ttu-id="5eb2b-137"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> erstellt <xref:Microsoft.AspNetCore.Components.Forms.EditContext> als [kaskadierenden Wert](xref:blazor/components/cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-137">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> creates an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> as a [cascading value](xref:blazor/components/cascading-values-and-parameters) that tracks metadata about the edit process, including which fields have been modified and the current validation messages.</span></span>

<span data-ttu-id="5eb2b-138">Weisen Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm> **entweder** einen <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **oder** ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> zu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-138">Assign **either** an <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **or** an <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> to an <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span> <span data-ttu-id="5eb2b-139">Die Zuweisung beider Elemente wird nicht unterstützt und generiert einen **Laufzeitfehler**.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-139">Assignment of both isn't supported and generates a **runtime error**.</span></span>

<span data-ttu-id="5eb2b-140"><xref:Microsoft.AspNetCore.Components.Forms.EditForm> stellt für gültige und ungültige Formularübermittlungen geeignete Ereignisse zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-140">The <xref:Microsoft.AspNetCore.Components.Forms.EditForm> provides convenient events for valid and invalid form submission:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

<span data-ttu-id="5eb2b-141">Verwenden Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, um mithilfe von benutzerdefiniertem Code Validierung auszulösen und Feldwerte zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-141">Use <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> to use custom code to trigger validation and check field values.</span></span>

<span data-ttu-id="5eb2b-142">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-142">In the following example:</span></span>

* <span data-ttu-id="5eb2b-143">Die `HandleSubmit`-Methode wird ausgeführt, wenn die Schaltfläche **`Submit`** ausgewählt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-143">The `HandleSubmit` method executes when the **`Submit`** button is selected.</span></span>
* <span data-ttu-id="5eb2b-144">Das Formular wird durch Aufrufen von <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> überprüft.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-144">The form is validated by calling <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="5eb2b-145">Abhängig vom Validierungsergebnis wird zusätzlicher Code ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-145">Additional code is executed depending on the validation result.</span></span> <span data-ttu-id="5eb2b-146">Platzieren Sie Geschäftslogik in der Methode, die <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> zugewiesen ist.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-146">Place business logic in the method assigned to <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>.</span></span>

```razor
<EditForm EditContext="@editContext" OnSubmit="HandleSubmit">

    ...

    <button type="submit">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
    }

    private async Task HandleSubmit()
    {
        var isValid = editContext.Validate();

        if (isValid)
        {
            ...
        }
        else
        {
            ...
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="5eb2b-147">Die Framework-API ist nicht dafür zuständig, Validierungsmeldungen direkt aus einem <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu löschen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-147">Framework API doesn't exist to clear validation messages directly from an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="5eb2b-148">Daher empfiehlt es sich nicht allgemein, einem neuen <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in einem Formular Validierungsmeldungen hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-148">Therefore, we don't generally recommend adding validation messages to a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in a form.</span></span> <span data-ttu-id="5eb2b-149">Verwenden Sie zum Verwalten von Validierungsmeldungen eine [Validierungskomponente](#validator-components) mit dem [Validierungscode für Geschäftslogik](#business-logic-validation), wie in diesem Artikel beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-149">To manage validation messages, use a [validator component](#validator-components) with your [business logic validation code](#business-logic-validation), as described in this article.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="display-name-support"></a><span data-ttu-id="5eb2b-150">Unterstützung für Anzeigenamen</span><span class="sxs-lookup"><span data-stu-id="5eb2b-150">Display name support</span></span>

<span data-ttu-id="5eb2b-151">*Dieser Abschnitt gilt ab dem Release Candidate 1 (RC1) für .NET 5, der Mitte September veröffentlicht wird.*</span><span class="sxs-lookup"><span data-stu-id="5eb2b-151">*This section applies to .NET 5 Release Candidate 1 (RC1) or later, which will be released in mid-September.*</span></span>

<span data-ttu-id="5eb2b-152">Die folgenden integrierten Komponenten unterstützen Anzeigenamen mit dem `DisplayName`-Parameter:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-152">The following built-in components support display names with the `DisplayName` parameter:</span></span>

* <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601>
* <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601>

<span data-ttu-id="5eb2b-153">Für das folgende `InputDate`-Komponentenbeispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-153">In the following `InputDate` component example:</span></span>

* <span data-ttu-id="5eb2b-154">Der Anzeigename (`DisplayName`) wird auf `birthday` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-154">The display name (`DisplayName`) is set to `birthday`.</span></span>
* <span data-ttu-id="5eb2b-155">Die Komponente ist an die `BirthDate`-Eigenschaft als `DateTime`-Typ gebunden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-155">The component is bound to the `BirthDate` property as a `DateTime` type.</span></span>

```razor
<InputDate @bind-Value="@BirthDate" DisplayName="birthday" />

@code {
    public DateTime BirthDate { get; set; }
}
```

<span data-ttu-id="5eb2b-156">Wenn der Benutzer keinen Datumswert angibt, wird der Validierungsfehler folgendermaßen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-156">If the user doesn't provide a date value, the validation error appears as:</span></span>

```
The birthday must be a date.
```

::: moniker-end

## <a name="validator-components"></a><span data-ttu-id="5eb2b-157">Validierungssteuerelementkomponente</span><span class="sxs-lookup"><span data-stu-id="5eb2b-157">Validator components</span></span>

<span data-ttu-id="5eb2b-158">Validierungssteuerelementkomponenten unterstützen Formularvalidierung durch die Verwaltung eines <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> für den <xref:Microsoft.AspNetCore.Components.Forms.EditContext> eines Formulars.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-158">Validator components support form validation by managing a <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> for a form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span>

<span data-ttu-id="5eb2b-159">Das Blazor-Framework stellt die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente zum Anfügen von Validierungsunterstützung zu Formularen auf der Grundlage von [Validierungsattributen (Datenanmerkungen)](xref:mvc/models/validation#validation-attributes) zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-159">The Blazor framework provides the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component to attach validation support to forms based on [validation attributes (data annotations)](xref:mvc/models/validation#validation-attributes).</span></span> <span data-ttu-id="5eb2b-160">Erstellen Sie benutzerdefinierte Validierungssteuerelementkomponenten, um Validierungsmeldungen für verschiedene Formulare auf derselben Seite oder in demselben Formular in verschiedenen Schritten der Formularverarbeitung zu verarbeiten, z. B. clientseitige Validierung, gefolgt von serverseitiger Validierung.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-160">Create custom validator components to process validation messages for different forms on the same page or the same form at different steps of form processing, for example client-side validation followed by server-side validation.</span></span> <span data-ttu-id="5eb2b-161">Das in diesem Abschnitt gezeigte Beispiel für eine Validierungssteuerelementkomponente (`CustomValidator`) wird in den folgenden Abschnitten dieses Artikels verwendet:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-161">The validator component example shown in this section, `CustomValidator`, is used in the following sections of this article:</span></span>

* [<span data-ttu-id="5eb2b-162">Geschäftslogikvalidierung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-162">Business logic validation</span></span>](#business-logic-validation)
* [<span data-ttu-id="5eb2b-163">Servervalidierung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-163">Server validation</span></span>](#server-validation)

> [!NOTE]
> <span data-ttu-id="5eb2b-164">In vielen Fällen können benutzerdefinierte Datenanmerkungs-Validierungsattribute anstelle von benutzerdefinierten Validierungssteuerelementkomponenten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-164">Custom data annotation validation attributes can be used instead of custom validator components in many cases.</span></span> <span data-ttu-id="5eb2b-165">Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-165">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="5eb2b-166">Bei Verwendung mit serverseitiger Validierung müssen alle benutzerdefinierten Attribute, die auf das Modell angewendet werden, auf dem Server ausführbar sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-166">When used with server-side validation, any custom attributes applied to the model must be executable on the server.</span></span> <span data-ttu-id="5eb2b-167">Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-167">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

<span data-ttu-id="5eb2b-168">Erstellen Sie eine Validierungssteuerelementkomponente aus <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-168">Create a validator component from <xref:Microsoft.AspNetCore.Components.ComponentBase>:</span></span>

* <span data-ttu-id="5eb2b-169">Der <xref:Microsoft.AspNetCore.Components.Forms.EditContext> des Formulars ist ein [kaskadierenden Parameter](xref:blazor/components/cascading-values-and-parameters) der Komponente.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-169">The form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> is a [cascading parameter](xref:blazor/components/cascading-values-and-parameters) of the component.</span></span>
* <span data-ttu-id="5eb2b-170">Wenn die Validierungssteuerelementkomponente initialisiert wird, wird ein neuer <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> erstellt, um eine aktuelle Liste von Formularfehlern zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-170">When the validator component is initialized, a new <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> is created to maintain a current list of form errors.</span></span>
* <span data-ttu-id="5eb2b-171">Der Nachrichtenspeicher empfängt Fehler, wenn Entwicklercode in der Komponente des Formulars die `DisplayErrors`-Methode aufruft.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-171">The message store receives errors when developer code in the form's component calls the `DisplayErrors` method.</span></span> <span data-ttu-id="5eb2b-172">Die Fehler werden in einem [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)-Element an die `DisplayErrors`-Methode übergeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-172">The errors are passed to the `DisplayErrors` method in a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2).</span></span> <span data-ttu-id="5eb2b-173">Im Wörterbuch ist der Schlüssel der Name des Formularfelds, das mindestens einen Fehler aufweist.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-173">In the dictionary, the key is the name of the form field that has one or more errors.</span></span> <span data-ttu-id="5eb2b-174">Der Wert ist die Fehlerliste.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-174">The value is the error list.</span></span>
* <span data-ttu-id="5eb2b-175">Meldungen werden gelöscht, wenn eines der folgenden Ereignisse aufgetreten ist:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-175">Messages are cleared when any of the following have occurred:</span></span>
  * <span data-ttu-id="5eb2b-176">Validierung wird für den <xref:Microsoft.AspNetCore.Components.Forms.EditContext> angefordert, wenn das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>-Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-176">Validation is requested on the <xref:Microsoft.AspNetCore.Components.Forms.EditContext> when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested> event is raised.</span></span> <span data-ttu-id="5eb2b-177">Alle Fehler werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-177">All of the errors are cleared.</span></span>
  * <span data-ttu-id="5eb2b-178">Ein Feld ändert sich im Formular, wenn das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Ereignis ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-178">A field changes in the form when the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> event is raised.</span></span> <span data-ttu-id="5eb2b-179">Nur die Fehler für das Feld werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-179">Only the errors for the field are cleared.</span></span>
  * <span data-ttu-id="5eb2b-180">Die `ClearErrors`-Methode wird vom Entwicklercode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-180">The `ClearErrors` method is called by developer code.</span></span> <span data-ttu-id="5eb2b-181">Alle Fehler werden gelöscht.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-181">All of the errors are cleared.</span></span>

```csharp
using System;
using System.Collections.Generic;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Forms;

namespace BlazorSample.Client
{
    public class CustomValidator : ComponentBase
    {
        private ValidationMessageStore messageStore;

        [CascadingParameter]
        private EditContext CurrentEditContext { get; set; }

        protected override void OnInitialized()
        {
            if (CurrentEditContext == null)
            {
                throw new InvalidOperationException(
                    $"{nameof(CustomValidator)} requires a cascading " +
                    $"parameter of type {nameof(EditContext)}. " +
                    $"For example, you can use {nameof(CustomValidator)} " +
                    $"inside an {nameof(EditForm)}.");
            }

            messageStore = new ValidationMessageStore(CurrentEditContext);

            CurrentEditContext.OnValidationRequested += (s, e) => 
                messageStore.Clear();
            CurrentEditContext.OnFieldChanged += (s, e) => 
                messageStore.Clear(e.FieldIdentifier);
        }

        public void DisplayErrors(Dictionary<string, List<string>> errors)
        {
            foreach (var err in errors)
            {
                messageStore.Add(CurrentEditContext.Field(err.Key), err.Value);
            }

            CurrentEditContext.NotifyValidationStateChanged();
        }

        public void ClearErrors()
        {
            messageStore.Clear();
            CurrentEditContext.NotifyValidationStateChanged();
        }
    }
}
```

## <a name="business-logic-validation"></a><span data-ttu-id="5eb2b-182">Geschäftslogikvalidierung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-182">Business logic validation</span></span>

<span data-ttu-id="5eb2b-183">Die Validierung der Geschäftslogik kann mit einer [Validierungssteuerelementkomponente](#validator-components) ausgeführt werden, die Formularfehler in einem Wörterbuch empfängt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-183">Business logic validation can be accomplished with a [validator component](#validator-components) that receives form errors in a dictionary.</span></span>

<span data-ttu-id="5eb2b-184">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-184">In the following example:</span></span>

* <span data-ttu-id="5eb2b-185">Die `CustomValidator`-Komponente aus dem Abschnitt [Validierungssteuerelementkomponenten](#validator-components) dieses Artikels wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-185">The `CustomValidator` component from the [Validator components](#validator-components) section of this article is used.</span></span>
* <span data-ttu-id="5eb2b-186">Die Validierung erfordert einen Wert für die Beschreibung des Schiffs (`Description`), wenn der Benutzer die `Defense`-Schiffsklassifizierung (`Classification`) auswählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-186">The validation requires a value for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="5eb2b-187">Wenn Validierungsmeldungen in der Komponente festgelegt werden, werden sie dem <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> des Validierungssteuerelements hinzugefügt und in <xref:Microsoft.AspNetCore.Components.Forms.EditForm> angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-187">When validation messages are set in the component, they're added to the validator's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> and shown in the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>:</span></span>

```csharp
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    ...

</EditForm>

@code {
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private void HandleValidSubmit()
    {
        customValidator.ClearErrors();

        var errors = new Dictionary<string, List<string>>();

        if (starship.Classification == "Defense" &&
                string.IsNullOrEmpty(starship.Description))
        {
            errors.Add(nameof(starship.Description),
                new List<string>() { "For a 'Defense' ship classification, " +
                "'Description' is required." });
        }

        if (errors.Count() > 0)
        {
            customValidator.DisplayErrors(errors);
        }
        else
        {
            // Process the form
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="5eb2b-188">Als Alternative zur Verwendung von [Validierungkomponenten](#validator-components) können Datenanmerkungs-Validierungsattribute verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-188">As an alternative to using [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="5eb2b-189">Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-189">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="5eb2b-190">Bei Verwendung mit serverseitiger Validierung müssen die Attribute auf dem Server ausführbar sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-190">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="5eb2b-191">Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-191">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

## <a name="server-validation"></a><span data-ttu-id="5eb2b-192">Servervalidierung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-192">Server validation</span></span>

<span data-ttu-id="5eb2b-193">Servervalidierung kann mit einer Server-[Validierungssteuerelementkomponente](#validator-components) erfolgen:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-193">Server validation can be accomplished with a server [validator component](#validator-components):</span></span>

* <span data-ttu-id="5eb2b-194">Verarbeiten Sie clientseitige Validierung im Formular mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-194">Process client-side validation in the form with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="5eb2b-195">Wenn das Formular clientseitige Validierung besteht (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> wird aufgerufen), senden Sie das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> zur Formularverarbeitung an eine Back-End-Server-API.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-195">When the form passes client-side validation (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> is called), send the <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> to a backend server API for form processing.</span></span>
* <span data-ttu-id="5eb2b-196">Verarbeiten Sie die Modellvalidierung auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-196">Process model validation on the server.</span></span>
* <span data-ttu-id="5eb2b-197">Die Server-API umfasst sowohl die integrierte Framework-Datenanmerkungsvalidierung als auch benutzerdefinierte Validierungslogik, die vom Entwickler bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-197">The server API includes both the built-in framework data annotations validation and custom validation logic supplied by the developer.</span></span> <span data-ttu-id="5eb2b-198">Wenn die Validierung auf dem Server bestanden wird, verarbeiten Sie das Formular, und senden Sie einen Erfolgsstatuscode zurück (*200 - OK*).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-198">If validation passes on the server, process the form and send back a success status code (*200 - OK*).</span></span> <span data-ttu-id="5eb2b-199">Wenn bei der Validierung ein Fehler auftritt, werden ein Fehlerstatuscode (*400 - Bad Request* (Ungültige Anforderung)) und die Feldvalidierungsfehler zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-199">If validation fails, return a failure status code (*400 - Bad Request*) and the field validation errors.</span></span>
* <span data-ttu-id="5eb2b-200">Deaktivieren Sie entweder das Formular bei Erfolg, oder zeigen Sie die Fehler an.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-200">Either disable the form on success or display the errors.</span></span>

<span data-ttu-id="5eb2b-201">Das folgende Beispiel beruht auf Folgendem:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-201">The following example is based on:</span></span>

* <span data-ttu-id="5eb2b-202">Einer gehosteten Blazor-Lösung, die von der [gehosteten Blazor-Projektvorlage](xref:blazor/hosting-models#blazor-webassembly) erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-202">A hosted Blazor solution created by the [Blazor Hosted project template](xref:blazor/hosting-models#blazor-webassembly).</span></span> <span data-ttu-id="5eb2b-203">Das Beispiel kann mit allen sicheren gehosteten Blazor-Lösungen verwendet werden, die in der [Dokumentation zu Sicherheit und Identity](xref:blazor/security/webassembly/index#implementation-guidance) beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-203">The example can be used with any of the secure hosted Blazor solutions described in the [Security and Identity documentation](xref:blazor/security/webassembly/index#implementation-guidance).</span></span>
* <span data-ttu-id="5eb2b-204">Dem Formular *Starfleet Starship Database* aus dem vorherigen Abschnitt [Integrierte Formularkomponenten](#built-in-forms-components).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-204">The *Starfleet Starship Database* form example in the preceding [Built-in forms components](#built-in-forms-components) section.</span></span>
* <span data-ttu-id="5eb2b-205">Der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente des Blazor-Frameworks.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-205">The Blazor framework's <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span>
* <span data-ttu-id="5eb2b-206">Der `CustomValidator`-Komponente, die im Abschnitt [Validierungssteuerelementkomponenten](#validator-components) gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-206">The `CustomValidator` component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="5eb2b-207">Im folgenden Beispiel überprüft die Server-API, ob ein Wert für die Beschreibung des Schiffs (`Description`) bereitgestellt wird, wenn der Benutzer die `Defense`-Schiffsklassifizierung (`Classification`) auswählt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-207">In the following example, the server API validates that a value is provided for the ship's description (`Description`) if the user selects the `Defense` ship classification (`Classification`).</span></span>

<span data-ttu-id="5eb2b-208">Platzieren Sie das `Starship`-Modell im `Shared`-Projekt der Lösung, sodass sowohl die Client- als auch die Server-App das Modell verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-208">Place the `Starship` model into the solution's `Shared` project so that both the client and server apps can use the model.</span></span> <span data-ttu-id="5eb2b-209">Da für das Modell Datenanmerkungen erforderlich sind, fügen Sie der Projektdatei des `Shared`-Projekts einen Paketverweis für [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) hinzu:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-209">Since the model requires data annotations, add a package reference for [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) to the `Shared` project's project file:</span></span>

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

<span data-ttu-id="5eb2b-210">Um die neueste Nicht-Vorschauversion des Pakets zu ermitteln, ziehen Sie den **Versionsverlauf** des Pakets unter [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations) zu Rate.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-210">To determine the latest non-preview version of the package, see the package **Version History** at [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations).</span></span>

<span data-ttu-id="5eb2b-211">Fügen Sie im Server-API-Projekt einen Controller hinzu, um die Anforderungen für die Validierung des Schiffs (`Controllers/StarshipValidation.cs`) zu verarbeiten und bei Validierungsfehlern Meldungen zurückzugeben:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-211">In the server API project, add a controller to process starship validation requests (`Controllers/StarshipValidation.cs`) and return failed validation messages:</span></span>

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using BlazorSample.Shared;

namespace BlazorSample.Server.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class StarshipValidationController : ControllerBase
    {
        private readonly ILogger<StarshipValidationController> logger;

        public StarshipValidationController(
            ILogger<StarshipValidationController> logger)
        {
            this.logger = logger;
        }

        [HttpPost]
        public async Task<IActionResult> Post(Starship starship)
        {
            try
            {
                if (starship.Classification == "Defense" && 
                    string.IsNullOrEmpty(starship.Description))
                {
                    ModelState.AddModelError(nameof(starship.Description),
                        "For a 'Defense' ship " +
                        "classification, 'Description' is required.");
                }
                else
                {
                    // Process the form asynchronously
                    // async ...

                    return Ok(ModelState);
                }
            }
            catch (Exception ex)
            {
                logger.LogError("Validation Error: {MESSAGE}", ex.Message);
            }

            return BadRequest(ModelState);
        }
    }
}
```

<span data-ttu-id="5eb2b-212">Wenn ein Validierungsfehler der Modellbindung auf dem Server auftritt, gibt ein [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normalerweise eine [Standardantwort „Ungültige Anforderung“](xref:web-api/index#default-badrequest-response) mit <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-212">When a model binding validation error occurs on the server, an [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normally returns a [default bad request response](xref:web-api/index#default-badrequest-response) with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="5eb2b-213">Die Antwort enthält mehr Daten als nur die Validierungsfehler (wie im folgenden Beispiel gezeigt), wenn alle Felder des *Starfleet Starship Database*-Formulars nicht übermittelt wurden und die Validierung des Formulars fehlschlägt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-213">The response contains more data than just the validation errors, as shown in the following example when all of the fields of the *Starfleet Starship Database* form aren't submitted and the form fails validation:</span></span>

```json
{
  "title": "One or more validation errors occurred.",
  "status": 400,
  "errors": {
    "Identifier": ["The Identifier field is required."],
    "Classification": ["The Classification field is required."],
    "IsValidatedDesign": ["This form disallows unapproved ships."],
    "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
  }
}
```

<span data-ttu-id="5eb2b-214">Wenn die Server-API die vorherige JSON-Standardantwort zurückgibt, ist es möglich, dass der Client die Antwort analysiert, um die untergeordneten Elemente des `errors`-Knotens abzurufen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-214">If the server API returns the preceding default JSON response, it's possible for the client to parse the response to obtain the children of the `errors` node.</span></span> <span data-ttu-id="5eb2b-215">Es ist jedoch unpraktisch, die Datei zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-215">However, it's inconvenient to parse the file.</span></span> <span data-ttu-id="5eb2b-216">Zum Analysieren des JSON-Codes ist nach dem Aufrufen von <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> zusätzlicher Code erforderlich, um eine [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) von Fehlern für die Verarbeitung von Formularvalidierungsfehlern zu generierren.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-216">Parsing the JSON requires additional code after calling <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> in order to produce a [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) of errors for forms validation error processing.</span></span> <span data-ttu-id="5eb2b-217">Im Idealfall sollte die Server-API nur die Validierungsfehler zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-217">Ideally, the server API should only return the validation errors:</span></span>

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

<span data-ttu-id="5eb2b-218">Um die Antwort der Server-API so zu ändern, dass nur die Validierungsfehler zurückgegeben werden, ändern Sie den Delegaten, der für Aktionen aufgerufen wird, die mit <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices` kommentiert sind.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-218">To modify the server API's response to make it only return the validation errors, change the delegate that's invoked on actions that are annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="5eb2b-219">Geben Sie für den API-Endpunkt (`/StarshipValidation`) ein <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> mit <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> zurück.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-219">For the API endpoint (`/StarshipValidation`), return a <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> with the <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>.</span></span> <span data-ttu-id="5eb2b-220">Behalten Sie für alle anderen API-Endpunkte das Standardverhalten bei, indem Sie das Objektergebnis mit neuen <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurückgeben:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-220">For any other API endpoints, preserve the default behavior by returning the object result with a new <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>:</span></span>

```csharp
using Microsoft.AspNetCore.Mvc;

...

services.AddControllersWithViews()
    .ConfigureApiBehaviorOptions(options =>
    {
        options.InvalidModelStateResponseFactory = context =>
        {
            if (context.HttpContext.Request.Path == "/StarshipValidation")
            {
                return new BadRequestObjectResult(context.ModelState);
            }
            else
            {
                return new BadRequestObjectResult(
                    new ValidationProblemDetails(context.ModelState));
            }
        };
    });
```

<span data-ttu-id="5eb2b-221">Weitere Informationen finden Sie unter <xref:web-api/handle-errors#validation-failure-error-response>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-221">For more information, see <xref:web-api/handle-errors#validation-failure-error-response>.</span></span>

<span data-ttu-id="5eb2b-222">Fügen Sie im Clientprojekt die im Abschnitt [Validierungssteuerelementkomponenten](#validator-components) gezeigte Validierungssteuerelementkomponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-222">In the client project, add the validator component shown in the [Validator components](#validator-components) section.</span></span>

<span data-ttu-id="5eb2b-223">Im Clientprojekt wird das *Starfleet Starship Database*-Formular aktualisiert, um Servervalidierungsfehler mithilfe der `CustomValidator`-Komponente anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-223">In the client project, the *Starfleet Starship Database* form is updated to show server validation errors with help of the `CustomValidator` component.</span></span> <span data-ttu-id="5eb2b-224">Wenn die Server-API Validierungsmeldungen zurückgibt, werden diese dem <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> der `CustomValidator`-Komponente hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-224">When the server API returns validation messages, they're added to the `CustomValidator` component's <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore>.</span></span> <span data-ttu-id="5eb2b-225">Die Fehler sind im <xref:Microsoft.AspNetCore.Components.Forms.EditContext> des Formulars zur Anzeige durch die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> des Formulars verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-225">The errors are available in the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> for display by the form's <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>:</span></span>

```csharp
@page "/FormValidation"
@using System.Net
@using System.Net.Http.Json
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@using BlazorSample.Shared
@attribute [Authorize]
@inject HttpClient Http
@inject ILogger<FormValidation> Logger

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <CustomValidator @ref="customValidator" />
    <ValidationSummary />

    <p>
        <label>
            Identifier:
            <InputText @bind-Value="starship.Identifier" disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Description (optional):
            <InputTextArea @bind-Value="starship.Description" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Primary Classification:
            <InputSelect @bind-Value="starship.Classification" disabled="@disabled">
                <option value="">Select classification ...</option>
                <option value="Exploration">Exploration</option>
                <option value="Diplomacy">Diplomacy</option>
                <option value="Defense">Defense</option>
            </InputSelect>
        </label>
    </p>
    <p>
        <label>
            Maximum Accommodation:
            <InputNumber @bind-Value="starship.MaximumAccommodation" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Engineering Approval:
            <InputCheckbox @bind-Value="starship.IsValidatedDesign" 
                disabled="@disabled" />
        </label>
    </p>
    <p>
        <label>
            Production Date:
            <InputDate @bind-Value="starship.ProductionDate" disabled="@disabled" />
        </label>
    </p>

    <button type="submit" disabled="@disabled">Submit</button>

    <p style="@messageStyles">
        @message
    </p>

    <p>
        <a href="http://www.startrek.com/">Star Trek</a>,
        &copy;1966-2019 CBS Studios, Inc. and
        <a href="https://www.paramount.com">Paramount Pictures</a>
    </p>
</EditForm>

@code {
    private bool disabled;
    private string message;
    private string messageStyles = "visibility:hidden";
    private CustomValidator customValidator;
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };

    private async Task HandleValidSubmit(EditContext editContext)
    {
        customValidator.ClearErrors();

        try
        {
            var response = await Http.PostAsJsonAsync<Starship>(
                "StarshipValidation", (Starship)editContext.Model);

            var errors = await response.Content
                .ReadFromJsonAsync<Dictionary<string, List<string>>>();

            if (response.StatusCode == HttpStatusCode.BadRequest && 
                errors.Count() > 0)
            {
                customValidator.DisplayErrors(errors);
            }
            else if (!response.IsSuccessStatusCode)
            {
                throw new HttpRequestException(
                    $"Validation failed. Status Code: {response.StatusCode}");
            }
            else
            {
                disabled = true;
                messageStyles = "color:green";
                message = "The form has been processed.";
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            ex.Redirect();
        }
        catch (Exception ex)
        {
            Logger.LogError("Form processing error: {MESSAGE}", ex.Message);
            disabled = true;
            messageStyles = "color:red";
            message = "There was an error processing the form.";
        }
    }
}
```

> [!NOTE]
> <span data-ttu-id="5eb2b-226">Als Alternative zu [Validierungkomponenten](#validator-components) können Datenanmerkungs-Validierungsattribute verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-226">As an alternative to [validation components](#validator-components), data annotation validation attributes can be used.</span></span> <span data-ttu-id="5eb2b-227">Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-227">Custom attributes applied to the form's model activate with the use of the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="5eb2b-228">Bei Verwendung mit serverseitiger Validierung müssen die Attribute auf dem Server ausführbar sein.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-228">When used with server-side validation, the attributes must be executable on the server.</span></span> <span data-ttu-id="5eb2b-229">Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-229">For more information, see <xref:mvc/models/validation#alternatives-to-built-in-attributes>.</span></span>

> [!NOTE]
> <span data-ttu-id="5eb2b-230">Der serverseitige Überprüfungsansatz in diesem Abschnitt eignet sich für jedes der Beispiele für gehostete Blazor WebAssembly-Lösungen in diesem Dokumentationssatz:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-230">The server-side validation approach in this section is suitable for any of the Blazor WebAssembly hosted solution examples in this documentation set:</span></span>
>
> * [<span data-ttu-id="5eb2b-231">Azure Active Directory (AAD)</span><span class="sxs-lookup"><span data-stu-id="5eb2b-231">Azure Active Directory (AAD)</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [<span data-ttu-id="5eb2b-232">Azure Active Directory (AAD) B2C</span><span class="sxs-lookup"><span data-stu-id="5eb2b-232">Azure Active Directory (AAD) B2C</span></span>](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [<span data-ttu-id="5eb2b-233">Identity Server</span><span class="sxs-lookup"><span data-stu-id="5eb2b-233">Identity Server</span></span>](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a><span data-ttu-id="5eb2b-234">InputText basierend auf dem Eingabeereignis</span><span class="sxs-lookup"><span data-stu-id="5eb2b-234">InputText based on the input event</span></span>

<span data-ttu-id="5eb2b-235">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-235">Use the <xref:Microsoft.AspNetCore.Components.Forms.InputText> component to create a custom component that uses the `input` event instead of the `change` event.</span></span>

<span data-ttu-id="5eb2b-236">Im folgenden Beispiel erbt die `CustomInputText`-Komponente die `InputText`-Komponente des Frameworks und legt die Ereignisbindung (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) auf das `oninput`-Ereignis fest.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-236">In the following example, the `CustomInputText` component inherits the framework's `InputText` component and sets the event binding (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) to the `oninput` event.</span></span>

<span data-ttu-id="5eb2b-237">`Shared/CustomInputText.razor`:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-237">`Shared/CustomInputText.razor`:</span></span>

```razor
@inherits InputText

<input 
    @attributes="AdditionalAttributes" 
    class="@CssClass" 
    value="@CurrentValue"
    @oninput="EventCallback.Factory.CreateBinder<string>(
         this, __value => CurrentValueAsString = __value, 
         CurrentValueAsString)" />
```

<span data-ttu-id="5eb2b-238">Die `CustomInputText`-Komponente kann überall dort verwendet werden, wo <xref:Microsoft.AspNetCore.Components.Forms.InputText> verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-238">The `CustomInputText` component can be used anywhere <xref:Microsoft.AspNetCore.Components.Forms.InputText> is used:</span></span>

<span data-ttu-id="5eb2b-239">`Pages/TestForm.razor`:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-239">`Pages/TestForm.razor`:</span></span>

```razor
@page "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    <CustomInputText @bind-Value="exampleModel.Name" />

    <button type="submit">Submit</button>
</EditForm>

<p>
    CurrentValue: @exampleModel.Name
</p>

@code {
    private ExampleModel exampleModel = new ExampleModel();

    private void HandleValidSubmit()
    {
        ...
    }

    public class ExampleModel
    {
        [Required]
        [StringLength(10, ErrorMessage = "Name is too long.")]
        public string Name { get; set; }
    }
}
```

## <a name="radio-buttons"></a><span data-ttu-id="5eb2b-240">Optionsfelder</span><span class="sxs-lookup"><span data-stu-id="5eb2b-240">Radio buttons</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5eb2b-241">Verwenden Sie `InputRadio`-Komponenten mit der `InputRadioGroup`-Komponente, um eine Optionsfeldgruppe zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-241">Use `InputRadio` components with the `InputRadioGroup` component to create a radio button group.</span></span> <span data-ttu-id="5eb2b-242">Im folgenden Beispiel werden dem im Abschnitt [Integrierte Formularkomponenten](#built-in-forms-components) beschriebenem `Starship`-Modell Eigenschaften hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-242">In the following example, properties are added to the `Starship` model described in the [Built-in forms components](#built-in-forms-components) section:</span></span>

```csharp
[Required]
[Range(typeof(Manufacturer), nameof(Manufacturer.SpaceX), 
    nameof(Manufacturer.VirginGalactic), ErrorMessage = "Pick a manufacturer.")]
public Manufacturer Manufacturer { get; set; } = Manufacturer.Unknown;

[Required, EnumDataType(typeof(Color))]
public Color? Color { get; set; } = null;

[Required, EnumDataType(typeof(Engine))]
public Engine? Engine { get; set; } = null;
```

<span data-ttu-id="5eb2b-243">Fügen Sie der App die folgende `enums`-Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-243">Add the following `enums` to the app.</span></span> <span data-ttu-id="5eb2b-244">Erstellen Sie eine neue Datei, in der `enums` gespeichert werden kann, oder fügen Sie der `Starship.cs`-Datei `enums` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-244">Create a new file to hold the `enums` or add the `enums` to the `Starship.cs` file.</span></span> <span data-ttu-id="5eb2b-245">Sorgen Sie dafür, dass das `Starship`-Modell und das *Starfleet Starship Database*-Formular auf `enums` zugreifen können:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-245">Make the `enums` accessible to the `Starship` model and the *Starfleet Starship Database* form:</span></span>

```csharp
public enum Manufacturer { SpaceX, NASA, ULA, Virgin, Unknown }
public enum Color { ImperialRed, SpacecruiserGreen, StarshipBlue, VoyagerOrange }
public enum Engine { Ion, Plasma, Fusion, Warp }
```

<span data-ttu-id="5eb2b-246">Aktualisieren Sie das im Abschnitt [Integrierte Formularkomponenten](#built-in-forms-components) beschriebene *Starfleet Starship Database*-Formular.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-246">Update the *Starfleet Starship Database* form described in the [Built-in forms components](#built-in-forms-components) section.</span></span> <span data-ttu-id="5eb2b-247">Fügen Sie die Komponenten hinzu, damit Folgendes erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-247">Add the components to produce:</span></span>

* <span data-ttu-id="5eb2b-248">Eine Optionsfeldgruppe für den Schiffshersteller.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-248">A radio button group for the ship manufacturer.</span></span>
* <span data-ttu-id="5eb2b-249">Eine geschachtelte Optionsfeldgruppe für die Farbe und den Motor des Schiffs.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-249">A nested radio button group for ship color and engine.</span></span>

```razor
<p>
    <InputRadioGroup @bind-Value="starship.Manufacturer">
        Manufacturer:
        <br>
        @foreach (var manufacturer in (Manufacturer[])Enum
            .GetValues(typeof(Manufacturer)))
        {
            <InputRadio Value="manufacturer" />
            @manufacturer
            <br>
        }
    </InputRadioGroup>
</p>

<p>
    Pick one color and one engine:
    <InputRadioGroup Name="engine" @bind-Value="starship.Engine">
        <InputRadioGroup Name="color" @bind-Value="starship.Color">
            <InputRadio Name="color" Value="Color.ImperialRed" />Imperial Red<br>
            <InputRadio Name="engine" Value="Engine.Ion" />Ion<br>
            <InputRadio Name="color" Value="Color.SpacecruiserGreen" />
                Spacecruiser Green<br>
            <InputRadio Name="engine" Value="Engine.Plasma" />Plasma<br>
            <InputRadio Name="color" Value="Color.StarshipBlue" />Starship Blue<br>
            <InputRadio Name="engine" Value="Engine.Fusion" />Fusion<br>
            <InputRadio Name="color" Value="Color.VoyagerOrange" />
                Voyager Orange<br>
            <InputRadio Name="engine" Value="Engine.Warp" />Warp<br>
        </InputRadioGroup>
    </InputRadioGroup>
</p>
```

> [!NOTE]
> <span data-ttu-id="5eb2b-250">Wenn `Name` ausgelassen wird, werden die `InputRadio`-Komponenten nach dem direkten Vorgänger gruppiert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-250">If `Name` is omitted, `InputRadio` components are grouped by their most recent ancestor.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5eb2b-251">Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-251">When working with radio buttons in a form, data binding is handled differently than other elements because radio buttons are evaluated as a group.</span></span> <span data-ttu-id="5eb2b-252">Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-252">The value of each radio button is fixed, but the value of the radio button group is the value of the selected radio button.</span></span> <span data-ttu-id="5eb2b-253">Das folgende Beispiel veranschaulicht die Vorgehensweise:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-253">The following example shows how to:</span></span>

* <span data-ttu-id="5eb2b-254">Verarbeiten von Datenbindungen für eine Optionsfeldgruppe</span><span class="sxs-lookup"><span data-stu-id="5eb2b-254">Handle data binding for a radio button group.</span></span>
* <span data-ttu-id="5eb2b-255">Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente</span><span class="sxs-lookup"><span data-stu-id="5eb2b-255">Support validation using a custom `InputRadio` component.</span></span>

```razor
@using System.Globalization
@typeparam TValue
@inherits InputBase<TValue>

<input @attributes="AdditionalAttributes" type="radio" value="@SelectedValue" 
       checked="@(SelectedValue.Equals(Value))" @onchange="OnChange" />

@code {
    [Parameter]
    public TValue SelectedValue { get; set; }

    private void OnChange(ChangeEventArgs args)
    {
        CurrentValueAsString = args.Value.ToString();
    }

    protected override bool TryParseValueFromString(string value, 
        out TValue result, out string errorMessage)
    {
        var success = BindConverter.TryConvertTo<TValue>(
            value, CultureInfo.CurrentCulture, out var parsedValue);
        if (success)
        {
            result = parsedValue;
            errorMessage = null;

            return true;
        }
        else
        {
            result = default;
            errorMessage = $"{FieldIdentifier.FieldName} field isn't valid.";

            return false;
        }
    }
}
```

<span data-ttu-id="5eb2b-256">Die folgende <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-256">The following <xref:Microsoft.AspNetCore.Components.Forms.EditForm> uses the preceding `InputRadio` component to obtain and validate a rating from the user:</span></span>

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary />

    @for (int i = 1; i <= 5; i++)
    {
        <label>
            <InputRadio name="rate" SelectedValue="i" @bind-Value="model.Rating" />
            @i
        </label>
    }

    <button type="submit">Submit</button>
</EditForm>

<p>You chose: @model.Rating</p>

@code {
    private Model model = new Model();

    private void HandleValidSubmit()
    {
        ...
    }

    public class Model
    {
        [Range(1, 5)]
        public int Rating { get; set; }
    }
}
```

::: moniker-end

## <a name="binding-select-element-options-to-c-object-null-values"></a><span data-ttu-id="5eb2b-257">Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten</span><span class="sxs-lookup"><span data-stu-id="5eb2b-257">Binding `<select>` element options to C# object `null` values</span></span>

<span data-ttu-id="5eb2b-258">Es gibt aus folgenden Gründen keine vernünftige Möglichkeit, den Optionswert eines `<select>`-Elements als `null`-Wert eines C#-Objekts darzustellen:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-258">There's no sensible way to represent a `<select>` element option value as a C# object `null` value, because:</span></span>

* <span data-ttu-id="5eb2b-259">HTML-Attribute können keine `null`-Werte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-259">HTML attributes can't have `null` values.</span></span> <span data-ttu-id="5eb2b-260">`null` entspricht in HTML am ehesten dem Nichtvorhandensein des `value`-HTML-Attributs im `<option>`-Element.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-260">The closest equivalent to `null` in HTML is absence of the HTML `value` attribute from the `<option>` element.</span></span>
* <span data-ttu-id="5eb2b-261">Wenn Sie ein `<option>`-Element ohne `value`-Attribut auswählen, behandelt der Browser den Wert als *Textinhalt* dieses `<option>`-Elements.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-261">When selecting an `<option>` with no `value` attribute, the browser treats the value as the *text content* of that `<option>`'s element.</span></span>

<span data-ttu-id="5eb2b-262">Das Blazor-Framework versucht nicht, das Standardverhalten zu unterdrücken, weil dies Folgendes beinhalten würde:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-262">The Blazor framework doesn't attempt to suppress the default behavior because it would involve:</span></span>

* <span data-ttu-id="5eb2b-263">Erstellen einer Kette von Problemumgehungen für Sonderfälle im Framework.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-263">Creating a chain of special-case workarounds in the framework.</span></span>
* <span data-ttu-id="5eb2b-264">Breaking Changes am aktuellen Frameworkverhalten.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-264">Breaking changes to current framework behavior.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="5eb2b-265">Das plausibelste Äquivalent zu `null` in HTML ist eine `value` mit einer *leeren Zeichenfolge*.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-265">The most plausible `null` equivalent in HTML is an *empty string* `value`.</span></span> <span data-ttu-id="5eb2b-266">Das Blazor-Framework verarbeitet `null` als Konvertierung einer leeren Zeichenfolge für eine bidirektionale Bindung an einen `<select>`-Wert.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-266">The Blazor framework handles `null` to empty string conversions for two-way binding to a `<select>`'s value.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="5eb2b-267">Beim Versuch einer bidirektionalen Bindung an einen `<select>`-Wert verarbeitet das Blazor-Framework `null` nicht automatisch als Konvertierung einer leeren Zeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-267">The Blazor framework doesn't automatically handle `null` to empty string conversions when attempting two-way binding to a `<select>`'s value.</span></span> <span data-ttu-id="5eb2b-268">Weitere Informationen finden Sie unter [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221) (Korrigieren der Bindung von <select> an einen NULL-Wert [dotnet/aspnetcore #23221]).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-268">For more information, see [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221).</span></span>

::: moniker-end

## <a name="validation-support"></a><span data-ttu-id="5eb2b-269">Validierungsunterstützung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-269">Validation support</span></span>

<span data-ttu-id="5eb2b-270">Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse an.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-270">The <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attaches validation support using data annotations to the cascaded <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="5eb2b-271">Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-271">Enabling support for validation using data annotations requires this explicit gesture.</span></span> <span data-ttu-id="5eb2b-272">Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> durch eine benutzerdefinierte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-272">To use a different validation system than data annotations, replace the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> with a custom implementation.</span></span> <span data-ttu-id="5eb2b-273">Die ASP.NET Core-Implementierung können Sie sich in der Verweisquelle ansehen: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-273">The ASP.NET Core implementation is available for inspection in the reference source: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs).</span></span> <span data-ttu-id="5eb2b-274">Die vorangehenden Links zur Verweisquelle stellen Code aus dem `master`-Branch des Repositorys bereit, der die aktuelle Entwicklung der Produkteinheit für das nächste Release von ASP.net Core darstellt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-274">The preceding links to reference source provide code from the repository's `master` branch, which represents the product unit's current development for the next release of ASP.NET Core.</span></span> <span data-ttu-id="5eb2b-275">Um den Branch für ein anderes Release auszuwählen, verwenden Sie die GitHub-Branchauswahl (beispielsweise `release/3.1`).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-275">To select the branch for a different release, use the GitHub branch selector (for example `release/3.1`).</span></span>

<span data-ttu-id="5eb2b-276">Blazor führt zwei Validierungstypen aus:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-276">Blazor performs two types of validation:</span></span>

* <span data-ttu-id="5eb2b-277">Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-277">*Field validation* is performed when the user tabs out of a field.</span></span> <span data-ttu-id="5eb2b-278">Während der Feldvalidierung ordnet die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-278">During field validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component associates all reported validation results with the field.</span></span>
* <span data-ttu-id="5eb2b-279">*Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-279">*Model validation* is performed when the user submits the form.</span></span> <span data-ttu-id="5eb2b-280">Während der Modellvalidierung versucht die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-280">During model validation, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component attempts to determine the field based on the member name that the validation result reports.</span></span> <span data-ttu-id="5eb2b-281">Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-281">Validation results that aren't associated with an individual member are associated with the model rather than a field.</span></span>

### <a name="validation-summary-and-validation-message-components"></a><span data-ttu-id="5eb2b-282">Komponenten der Validierungszusammenfassung und der Validierungsnachricht</span><span class="sxs-lookup"><span data-stu-id="5eb2b-282">Validation Summary and Validation Message components</span></span>

<span data-ttu-id="5eb2b-283">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span><span class="sxs-lookup"><span data-stu-id="5eb2b-283">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component summarizes all validation messages, which is similar to the [Validation Summary Tag Helper](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):</span></span>

```razor
<ValidationSummary />
```

<span data-ttu-id="5eb2b-284">Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-284">Output validation messages for a specific model with the `Model` parameter:</span></span>
  
```razor
<ValidationSummary Model="@starship" />
```

<span data-ttu-id="5eb2b-285">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span><span class="sxs-lookup"><span data-stu-id="5eb2b-285">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> component displays validation messages for a specific field, which is similar to the [Validation Message Tag Helper](xref:mvc/views/working-with-forms#the-validation-message-tag-helper).</span></span> <span data-ttu-id="5eb2b-286">Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-286">Specify the field for validation with the `For` attribute and a lambda expression naming the model property:</span></span>

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

<span data-ttu-id="5eb2b-287">Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>- und <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponenten unterstützen arbiträre Attribute.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-287">The <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601> and <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> components support arbitrary attributes.</span></span> <span data-ttu-id="5eb2b-288">Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-288">Any attribute that doesn't match a component parameter is added to the generated `<div>` or `<ul>` element.</span></span>

<span data-ttu-id="5eb2b-289">Steuern Sie den Stil von Validierungsmeldungen im Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-289">Control the style of validation messages in the app's stylesheet (`wwwroot/css/app.css` or `wwwroot/css/site.css`).</span></span> <span data-ttu-id="5eb2b-290">Die `validation-message`-Standardklasse legt die Textfarbe von Validierungsmeldungen auf Rot fest:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-290">The default `validation-message` class sets the text color of validation messages to red:</span></span>

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a><span data-ttu-id="5eb2b-291">Benutzerdefinierte Validierungsattribute</span><span class="sxs-lookup"><span data-stu-id="5eb2b-291">Custom validation attributes</span></span>

<span data-ttu-id="5eb2b-292">Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-292">To ensure that a validation result is correctly associated with a field when using a [custom validation attribute](xref:mvc/models/validation#custom-attributes), pass the validation context's <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName> when creating the <xref:System.ComponentModel.DataAnnotations.ValidationResult>:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

private class CustomValidator : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, 
        ValidationContext validationContext)
    {
        ...

        return new ValidationResult("Validation message to user.",
            new[] { validationContext.MemberName });
    }
}
```

> [!NOTE]
> <span data-ttu-id="5eb2b-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> ist `null`.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-293"><xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> is `null`.</span></span> <span data-ttu-id="5eb2b-294">Das Einfügen von Diensten zur Validierung in der `IsValid`-Methode wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-294">Injecting services for validation in the `IsValid` method isn't supported.</span></span>

### <a name="no-locblazor-data-annotations-validation-package"></a><span data-ttu-id="5eb2b-295">Validierungspakete für Datenanmerkungen in Blazor</span><span class="sxs-lookup"><span data-stu-id="5eb2b-295">Blazor data annotations validation package</span></span>

<span data-ttu-id="5eb2b-296">[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ist ein Paket, das Lücken bei der Validierung mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente schließt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-296">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) is a package that fills validation experience gaps using the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component.</span></span> <span data-ttu-id="5eb2b-297">Das Paket ist aktuell *experimentell*.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-297">The package is currently *experimental*.</span></span>

> [!NOTE]
> <span data-ttu-id="5eb2b-298">Das [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)-Paket enthält die neueste Version des *Release Candidate* auf [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Verwenden Sie zu diesem Zeitpunkt weiterhin das *experimentelle* Release Candidate-Paket.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-298">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package has a latest version of *release candidate* at [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Continue to use the *experimental* release candidate package at this time.</span></span> <span data-ttu-id="5eb2b-299">Die Assembly des Pakets wird möglicherweise in einer zukünftigen Version entweder in das Framework oder die Laufzeit verschoben.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-299">The package's assembly might be moved to either the framework or the runtime in a future release.</span></span> <span data-ttu-id="5eb2b-300">Weitere Aktualisierungen finden Sie im [GitHub-Repository „Announcements“](https://github.com/aspnet/Announcements) (Ankündigungen), im [dotnet/aspnetcore-Repositoryvon GitHub ](https://github.com/dotnet/aspnetcore) oder in diesem Themenabschnitt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-300">Watch the [Announcements GitHub repository](https://github.com/aspnet/Announcements), the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore), or this topic section for further updates.</span></span>

### <a name="compareproperty-attribute"></a><span data-ttu-id="5eb2b-301">[CompareProperty]-Attribut</span><span class="sxs-lookup"><span data-stu-id="5eb2b-301">[CompareProperty] attribute</span></span>

<span data-ttu-id="5eb2b-302"><xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-302">The <xref:System.ComponentModel.DataAnnotations.CompareAttribute> doesn't work well with the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> component because it doesn't associate the validation result with a specific member.</span></span> <span data-ttu-id="5eb2b-303">Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-303">This can result in inconsistent behavior between field-level validation and when the entire model is validated on a submit.</span></span> <span data-ttu-id="5eb2b-304">Mit dem *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wird das zusätzliche Validierungsattribut `ComparePropertyAttribute` eingeführt, das diese Einschränkungen umgeht.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-304">The [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) *experimental* package introduces an additional validation attribute, `ComparePropertyAttribute`, that works around these limitations.</span></span> <span data-ttu-id="5eb2b-305">In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attributs.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-305">In a Blazor app, `[CompareProperty]` is a direct replacement for the [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute) attribute.</span></span>

### <a name="nested-models-collection-types-and-complex-types"></a><span data-ttu-id="5eb2b-306">Verschachtelte Modelle, Sammlungstypen und komplexe Typen</span><span class="sxs-lookup"><span data-stu-id="5eb2b-306">Nested models, collection types, and complex types</span></span>

<span data-ttu-id="5eb2b-307">Blazor bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-307">Blazor provides support for validating form input using data annotations with the built-in <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>.</span></span> <span data-ttu-id="5eb2b-308"><xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-308">However, the <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> only validates top-level properties of the model bound to the form that aren't collection- or complex-type properties.</span></span>

<span data-ttu-id="5eb2b-309">Verwenden Sie den `ObjectGraphDataAnnotationsValidator`, der im *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellt wird, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexer Eigenschaften:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-309">To validate the bound model's entire object graph, including collection- and complex-type properties, use the `ObjectGraphDataAnnotationsValidator` provided by the *experimental* [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) package:</span></span>

```razor
<EditForm Model="@model" OnValidSubmit="@HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

<span data-ttu-id="5eb2b-310">Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-310">Annotate model properties with `[ValidateComplexType]`.</span></span> <span data-ttu-id="5eb2b-311">In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-311">In the following model classes, the `ShipDescription` class contains additional data annotations to validate when the model is bound to the form:</span></span>

<span data-ttu-id="5eb2b-312">`Starship.cs`:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-312">`Starship.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; } = 
        new ShipDescription();

    ...
}
```

<span data-ttu-id="5eb2b-313">`ShipDescription.cs`:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-313">`ShipDescription.cs`:</span></span>

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class ShipDescription
{
    [Required]
    [StringLength(40, ErrorMessage = "Description too long (40 char).")]
    public string ShortDescription { get; set; }
    
    [Required]
    [StringLength(240, ErrorMessage = "Description too long (240 char).")]
    public string LongDescription { get; set; }
}
```

### <a name="enable-the-submit-button-based-on-form-validation"></a><span data-ttu-id="5eb2b-314">Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-314">Enable the submit button based on form validation</span></span>

<span data-ttu-id="5eb2b-315">So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-315">To enable and disable the submit button based on form validation:</span></span>

* <span data-ttu-id="5eb2b-316">Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-316">Use the form's <xref:Microsoft.AspNetCore.Components.Forms.EditContext> to assign the model when the component is initialized.</span></span>
* <span data-ttu-id="5eb2b-317">Validieren Sie das Formular im <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-317">Validate the form in the context's <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged> callback to enable and disable the submit button.</span></span>
* <span data-ttu-id="5eb2b-318">Heben Sie die Einbindung des Ereignishandlers in der `Dispose`-Methode auf.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-318">Unhook the event handler in the `Dispose` method.</span></span> <span data-ttu-id="5eb2b-319">Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-319">For more information, see <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.</span></span>

> [!NOTE]
> <span data-ttu-id="5eb2b-320">Wenn Sie einen <xref:Microsoft.AspNetCore.Components.Forms.EditContext> verwenden, weisen Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm> nicht außerdem ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> zu.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-320">When using an <xref:Microsoft.AspNetCore.Components.Forms.EditContext>, don't also assign a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the <xref:Microsoft.AspNetCore.Components.Forms.EditForm>.</span></span>

```razor
@implements IDisposable

<EditForm EditContext="@editContext">
    <DataAnnotationsValidator />
    <ValidationSummary />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private Starship starship = new Starship() { ProductionDate = DateTime.UtcNow };
    private bool formInvalid = true;
    private EditContext editContext;

    protected override void OnInitialized()
    {
        editContext = new EditContext(starship);
        editContext.OnFieldChanged += HandleFieldChanged;
    }

    private void HandleFieldChanged(object sender, FieldChangedEventArgs e)
    {
        formInvalid = !editContext.Validate();
        StateHasChanged();
    }

    public void Dispose()
    {
        editContext.OnFieldChanged -= HandleFieldChanged;
    }
}
```

<span data-ttu-id="5eb2b-321">Im vorherigen Beispiel ist `formInvalid` in folgenden Fällen auf `false` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-321">In the preceding example, set `formInvalid` to `false` if:</span></span>

* <span data-ttu-id="5eb2b-322">Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-322">The form is preloaded with valid default values.</span></span>
* <span data-ttu-id="5eb2b-323">Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-323">You want the submit button enabled when the form loads.</span></span>

<span data-ttu-id="5eb2b-324">Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-324">A side effect of the preceding approach is that a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component is populated with invalid fields after the user interacts with any one field.</span></span> <span data-ttu-id="5eb2b-325">Dieses Szenario lässt sich folgendermaßen auflösen:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-325">This scenario can be addressed in either of the following ways:</span></span>

* <span data-ttu-id="5eb2b-326">Verwenden Sie keine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente des Formulars.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-326">Don't use a <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component on the form.</span></span>
* <span data-ttu-id="5eb2b-327">Lassen Sie die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-327">Make the <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> component visible when the submit button is selected (for example, in a `HandleValidSubmit` method).</span></span>

```razor
<EditForm EditContext="@editContext" OnValidSubmit="@HandleValidSubmit">
    <DataAnnotationsValidator />
    <ValidationSummary style="@displaySummary" />

    ...

    <button type="submit" disabled="@formInvalid">Submit</button>
</EditForm>

@code {
    private string displaySummary = "display:none";

    ...

    private void HandleValidSubmit()
    {
        displaySummary = "display:block";
    }
}
```

## <a name="troubleshoot"></a><span data-ttu-id="5eb2b-328">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="5eb2b-328">Troubleshoot</span></span>

> <span data-ttu-id="5eb2b-329">InvalidOperationException: EditForm erfordert einen Model-Parameter oder einen EditContext-Parameter, aber nicht beides.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-329">InvalidOperationException: EditForm requires a Model parameter, or an EditContext parameter, but not both.</span></span>

<span data-ttu-id="5eb2b-330">Vergewissern Sie sich, dass <xref:Microsoft.AspNetCore.Components.Forms.EditForm> über ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>- **oder** über ein <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Element verfügt.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-330">Confirm that the <xref:Microsoft.AspNetCore.Components.Forms.EditForm> has a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> **or** <xref:Microsoft.AspNetCore.Components.Forms.EditContext>.</span></span> <span data-ttu-id="5eb2b-331">Verwenden Sie nicht beides für dasselbe Formular.</span><span class="sxs-lookup"><span data-stu-id="5eb2b-331">Don't use both for the same form.</span></span>

<span data-ttu-id="5eb2b-332">Wenn Sie dem Formular ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>-Element zuweisen, vergewissern Sie sich, dass der Modelltyp instanziiert wird, wie im folgenden Beispiel gezeigt:</span><span class="sxs-lookup"><span data-stu-id="5eb2b-332">When assigning a <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> to the form, confirm that the model type is instantiated, as the following example shows:</span></span>

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
