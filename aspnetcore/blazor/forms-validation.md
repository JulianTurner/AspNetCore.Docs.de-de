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
ms.openlocfilehash: 4690c279c24ef23806a6e72aece5f7cd821752bc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88628325"
---
# <a name="aspnet-core-no-locblazor-forms-and-validation"></a>Blazor-Formulare und -Validierung in ASP.NET Core

Von [Daniel Roth](https://github.com/danroth27), [Rémi Bourgarel](https://remibou.github.io/) und [Luke Latham](https://github.com/guardrex)

Formulare und Validierungen werden in Blazor mithilfe von [Datenanmerkungen](xref:mvc/models/validation) unterstützt.

Der folgende `ExampleModel`-Typ definiert die Validierungslogik mithilfe von Datenanmerkungen:

```csharp
using System.ComponentModel.DataAnnotations;

public class ExampleModel
{
    [Required]
    [StringLength(10, ErrorMessage = "Name is too long.")]
    public string Name { get; set; }
}
```

Ein Formular wird mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Komponente definiert. Im folgenden Formular sehen Sie typische Elemente, Komponenten und typischen Razor-Code:

```razor
<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
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

Im vorherigen Beispiel:

* Das Formular validiert Benutzereingaben im `name`-Feld mithilfe der im `ExampleModel`-Typ definierten Validierung. Das Modell wird im `@code`-Block der Komponente erstellt und in einem privaten Feld (`exampleModel`) gespeichert. Das Feld wird dem `Model`-Attribut des `<EditForm>`-Elements zugewiesen.
* Der `@bind-Value` der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente bindet Folgendes:
  * Die Modelleigenschaft (`exampleModel.Name`) an die `Value`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente. Weitere Informationen zur Eigenschaftenbindung finden Sie unter <xref:blazor/components/data-binding#parent-to-child-binding-with-component-parameters>.
  * Den Delegat eines Änderungsereignisses an die `ValueChanged`-Eigenschaft der <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente.
* Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-[Validierungskomponente](#validator-components) fügt Validierungsunterstützung mithilfe von Datenanmerkungen hinzu.
* Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst Validierungsnachrichten zusammen.
* `HandleValidSubmit` wird ausgelöst, wenn das Formular erfolgreich gesendet wird (die Validierung erfolgreich besteht).

## <a name="built-in-forms-components"></a>Integrierte Formularkomponenten

Einige integrierte Eingabekomponenten sind verfügbar, um Benutzereingaben zu empfangen und zu validieren. Eingaben werden validiert, wenn sie geändert werden und wenn ein Formular gesendet wird. In der folgenden Tabelle finden Sie verfügbare Eingabekomponenten.

| Eingabekomponenten | Wird gerendert als&hellip; |
| --------------- | ------------------- |
| <xref:Microsoft.AspNetCore.Components.Forms.InputText> | `<input>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputTextArea> | `<textarea>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputSelect%601> | `<select>` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> | `<input type="number">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputCheckbox> | `<input type="checkbox">` |
| <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> | `<input type="date">` |

Alle Eingabekomponenten einschließlich <xref:Microsoft.AspNetCore.Components.Forms.EditForm> unterstützen arbiträre Attribute. Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten HTML-Element hinzugefügt.

Eingabekomponenten stellen Standardverhalten für das Validieren bei Bearbeitung zur Verfügung und ändern ihre CSS-Klasse, um den Feldzustand darzustellen. Einige Komponenten beinhalten hilfreiche Parsinglogik. <xref:Microsoft.AspNetCore.Components.Forms.InputDate%601> und <xref:Microsoft.AspNetCore.Components.Forms.InputNumber%601> verarbeiten beispielsweise nicht parsbare Werte ordnungsgemäß. Typen, die NULL-Werte akzeptieren, unterstützen auch die NULL-Zulässigkeit des Zielfelds (z. B. `int?`).

Der folgende `Starship`-Typ definiert eine Validierungslogik mithilfe einer größeren Anzahl Eigenschaften und Datenanmerkungen wie die frühere `ExampleModel`-Klasse:

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

Im obigen Beispiel ist `Description` optional, da keine Datenanmerkungen vorhanden sind.

Das folgende Formular validiert Benutzereingaben mithilfe der im `Starship`-Modell definierten Validierung:

```razor
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
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

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> erstellt <xref:Microsoft.AspNetCore.Components.Forms.EditContext> als [kaskadierenden Wert](xref:blazor/components/cascading-values-and-parameters), der Metadaten zum Bearbeitungsprozess erfasst, einschließlich der Felder, die geändert wurden und den aktuellen Validierungsnachrichten.

Weisen Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm> **entweder** einen <xref:Microsoft.AspNetCore.Components.Forms.EditContext> **oder** ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model?displayProperty=nameWithType> zu. Die Zuweisung beider Elemente wird nicht unterstützt und generiert einen **Laufzeitfehler**.

<xref:Microsoft.AspNetCore.Components.Forms.EditForm> stellt für gültige und ungültige Formularübermittlungen geeignete Ereignisse zur Verfügung:

* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit>
* <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnInvalidSubmit>

Verwenden Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit>, um mithilfe von benutzerdefiniertem Code Validierung auszulösen und Feldwerte zu überprüfen.

Im folgenden Beispiel:

* Die `HandleSubmit`-Methode wird ausgeführt, wenn die Schaltfläche **`Submit`** ausgewählt wird.
* Das Formular wird durch Aufrufen von <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Validate%2A?displayProperty=nameWithType> überprüft.
* Abhängig vom Validierungsergebnis wird zusätzlicher Code ausgeführt. Platzieren Sie Geschäftslogik in der Methode, die <xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnSubmit> zugewiesen ist.

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
> Die Framework-API ist nicht dafür zuständig, Validierungsmeldungen direkt aus einem <xref:Microsoft.AspNetCore.Components.Forms.EditContext> zu löschen. Daher empfiehlt es sich nicht allgemein, einem neuen <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> in einem Formular Validierungsmeldungen hinzuzufügen. Verwenden Sie zum Verwalten von Validierungsmeldungen eine [Validierungskomponente](#validator-components) mit dem [Validierungscode für Geschäftslogik](#business-logic-validation), wie in diesem Artikel beschrieben.

## <a name="validator-components"></a>Validierungssteuerelementkomponente

Validierungssteuerelementkomponenten unterstützen Formularvalidierung durch die Verwaltung eines <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> für den <xref:Microsoft.AspNetCore.Components.Forms.EditContext> eines Formulars.

Das Blazor-Framework stellt die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente zum Anfügen von Validierungsunterstützung zu Formularen auf der Grundlage von [Validierungsattributen (Datenanmerkungen)](xref:mvc/models/validation#validation-attributes) zur Verfügung. Erstellen Sie benutzerdefinierte Validierungssteuerelementkomponenten, um Validierungsmeldungen für verschiedene Formulare auf derselben Seite oder in demselben Formular in verschiedenen Schritten der Formularverarbeitung zu verarbeiten, z. B. clientseitige Validierung, gefolgt von serverseitiger Validierung. Das in diesem Abschnitt gezeigte Beispiel für eine Validierungssteuerelementkomponente (`CustomValidator`) wird in den folgenden Abschnitten dieses Artikels verwendet:

* [Geschäftslogikvalidierung](#business-logic-validation)
* [Servervalidierung](#server-validation)

> [!NOTE]
> In vielen Fällen können benutzerdefinierte Datenanmerkungs-Validierungsattribute anstelle von benutzerdefinierten Validierungssteuerelementkomponenten verwendet werden. Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert. Bei Verwendung mit serverseitiger Validierung müssen alle benutzerdefinierten Attribute, die auf das Modell angewendet werden, auf dem Server ausführbar sein. Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

Erstellen Sie eine Validierungssteuerelementkomponente aus <xref:Microsoft.AspNetCore.Components.ComponentBase>:

* Der <xref:Microsoft.AspNetCore.Components.Forms.EditContext> des Formulars ist ein [kaskadierenden Parameter](xref:blazor/components/cascading-values-and-parameters) der Komponente.
* Wenn die Validierungssteuerelementkomponente initialisiert wird, wird ein neuer <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> erstellt, um eine aktuelle Liste von Formularfehlern zu verwalten.
* Der Nachrichtenspeicher empfängt Fehler, wenn Entwicklercode in der Komponente des Formulars die `DisplayErrors`-Methode aufruft. Die Fehler werden in einem [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2)-Element an die `DisplayErrors`-Methode übergeben. Im Wörterbuch ist der Schlüssel der Name des Formularfelds, das mindestens einen Fehler aufweist. Der Wert ist die Fehlerliste.
* Meldungen werden gelöscht, wenn eines der folgenden Ereignisse aufgetreten ist:
  * Validierung wird für den <xref:Microsoft.AspNetCore.Components.Forms.EditContext> angefordert, wenn das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnValidationRequested>-Ereignis ausgelöst wird. Alle Fehler werden gelöscht.
  * Ein Feld ändert sich im Formular, wenn das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Ereignis ausgelöst wird. Nur die Fehler für das Feld werden gelöscht.
  * Die `ClearErrors`-Methode wird vom Entwicklercode aufgerufen. Alle Fehler werden gelöscht.

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

## <a name="business-logic-validation"></a>Geschäftslogikvalidierung

Die Validierung der Geschäftslogik kann mit einer [Validierungssteuerelementkomponente](#validator-components) ausgeführt werden, die Formularfehler in einem Wörterbuch empfängt.

Im folgenden Beispiel:

* Die `CustomValidator`-Komponente aus dem Abschnitt [Validierungssteuerelementkomponenten](#validator-components) dieses Artikels wird verwendet.
* Die Validierung erfordert einen Wert für die Beschreibung des Schiffs (`Description`), wenn der Benutzer die `Defense`-Schiffsklassifizierung (`Classification`) auswählt.

Wenn Validierungsmeldungen in der Komponente festgelegt werden, werden sie dem <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> des Validierungssteuerelements hinzugefügt und in <xref:Microsoft.AspNetCore.Components.Forms.EditForm> angezeigt:

```csharp
@page "/FormsValidation"

<h1>Starfleet Starship Database</h1>

<h2>New Ship Entry Form</h2>

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
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
> Als Alternative zur Verwendung von [Validierungkomponenten](#validator-components) können Datenanmerkungs-Validierungsattribute verwendet werden. Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert. Bei Verwendung mit serverseitiger Validierung müssen die Attribute auf dem Server ausführbar sein. Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

## <a name="server-validation"></a>Servervalidierung

Servervalidierung kann mit einer Server-[Validierungssteuerelementkomponente](#validator-components) erfolgen:

* Verarbeiten Sie clientseitige Validierung im Formular mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente.
* Wenn das Formular clientseitige Validierung besteht (<xref:Microsoft.AspNetCore.Components.Forms.EditForm.OnValidSubmit> wird aufgerufen), senden Sie das <xref:Microsoft.AspNetCore.Components.Forms.EditContext.Model?displayProperty=nameWithType> zur Formularverarbeitung an eine Back-End-Server-API.
* Verarbeiten Sie die Modellvalidierung auf dem Server.
* Die Server-API umfasst sowohl die integrierte Framework-Datenanmerkungsvalidierung als auch benutzerdefinierte Validierungslogik, die vom Entwickler bereitgestellt wird. Wenn die Validierung auf dem Server bestanden wird, verarbeiten Sie das Formular, und senden Sie einen Erfolgsstatuscode zurück (*200 - OK*). Wenn bei der Validierung ein Fehler auftritt, werden ein Fehlerstatuscode (*400 - Bad Request* (Ungültige Anforderung)) und die Feldvalidierungsfehler zurückgegeben.
* Deaktivieren Sie entweder das Formular bei Erfolg, oder zeigen Sie die Fehler an.

Das folgende Beispiel beruht auf Folgendem:

* Einer gehosteten Blazor-Lösung, die von der [gehosteten Blazor-Projektvorlage](xref:blazor/hosting-models#blazor-webassembly) erstellt wurde. Das Beispiel kann mit allen sicheren gehosteten Blazor-Lösungen verwendet werden, die in der [Dokumentation zu Sicherheit und Identity](xref:blazor/security/webassembly/index#implementation-guidance) beschrieben werden.
* Dem Formular *Starfleet Starship Database* aus dem vorherigen Abschnitt [Integrierte Formularkomponenten](#built-in-forms-components).
* Der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente des Blazor-Frameworks.
* Der `CustomValidator`-Komponente, die im Abschnitt [Validierungssteuerelementkomponenten](#validator-components) gezeigt wird.

Im folgenden Beispiel überprüft die Server-API, ob ein Wert für die Beschreibung des Schiffs (`Description`) bereitgestellt wird, wenn der Benutzer die `Defense`-Schiffsklassifizierung (`Classification`) auswählt.

Platzieren Sie das `Starship`-Modell im `Shared`-Projekt der Lösung, sodass sowohl die Client- als auch die Server-App das Modell verwenden kann. Da für das Modell Datenanmerkungen erforderlich sind, fügen Sie der Projektdatei des `Shared`-Projekts einen Paketverweis für [`System.ComponentModel.Annotations`](https://www.nuget.org/packages/System.ComponentModel.Annotations) hinzu:

```xml
<ItemGroup>
  <PackageReference Include="System.ComponentModel.Annotations" Version="{VERSION}" />
</ItemGroup>
```

Um die neueste Nicht-Vorschauversion des Pakets zu ermitteln, ziehen Sie den **Versionsverlauf** des Pakets unter [NuGet.org](https://www.nuget.org/packages/System.ComponentModel.Annotations) zu Rate.

Fügen Sie im Server-API-Projekt einen Controller hinzu, um die Anforderungen für die Validierung des Schiffs (`Controllers/StarshipValidation.cs`) zu verarbeiten und bei Validierungsfehlern Meldungen zurückzugeben:

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

Wenn ein Validierungsfehler der Modellbindung auf dem Server auftritt, gibt ein [`ApiController`](xref:web-api/index) (<xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>) normalerweise eine [Standardantwort „Ungültige Anforderung“](xref:web-api/index#default-badrequest-response) mit <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurück. Die Antwort enthält mehr Daten als nur die Validierungsfehler (wie im folgenden Beispiel gezeigt), wenn alle Felder des *Starfleet Starship Database*-Formulars nicht übermittelt wurden und die Validierung des Formulars fehlschlägt:

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

Wenn die Server-API die vorherige JSON-Standardantwort zurückgibt, ist es möglich, dass der Client die Antwort analysiert, um die untergeordneten Elemente des `errors`-Knotens abzurufen. Es ist jedoch unpraktisch, die Datei zu analysieren. Zum Analysieren des JSON-Codes ist nach dem Aufrufen von <xref:System.Net.Http.Json.HttpContentJsonExtensions.ReadFromJsonAsync%2A> zusätzlicher Code erforderlich, um eine [`Dictionary<string, List<string>>`](xref:System.Collections.Generic.Dictionary`2) von Fehlern für die Verarbeitung von Formularvalidierungsfehlern zu generierren. Im Idealfall sollte die Server-API nur die Validierungsfehler zurückgeben:

```json
{
  "Identifier": ["The Identifier field is required."],
  "Classification": ["The Classification field is required."],
  "IsValidatedDesign": ["This form disallows unapproved ships."],
  "MaximumAccommodation": ["Accommodation invalid (1-100000)."]
}
```

Um die Antwort der Server-API so zu ändern, dass nur die Validierungsfehler zurückgegeben werden, ändern Sie den Delegaten, der für Aktionen aufgerufen wird, die mit <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> in `Startup.ConfigureServices` kommentiert sind. Geben Sie für den API-Endpunkt (`/StarshipValidation`) ein <xref:Microsoft.AspNetCore.Mvc.BadRequestObjectResult> mit <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary> zurück. Behalten Sie für alle anderen API-Endpunkte das Standardverhalten bei, indem Sie das Objektergebnis mit neuen <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> zurückgeben:

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

Weitere Informationen finden Sie unter <xref:web-api/handle-errors#validation-failure-error-response>.

Fügen Sie im Clientprojekt die im Abschnitt [Validierungssteuerelementkomponenten](#validator-components) gezeigte Validierungssteuerelementkomponente hinzu.

Im Clientprojekt wird das *Starfleet Starship Database*-Formular aktualisiert, um Servervalidierungsfehler mithilfe der `CustomValidator`-Komponente anzuzeigen. Wenn die Server-API Validierungsmeldungen zurückgibt, werden diese dem <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessageStore> der `CustomValidator`-Komponente hinzugefügt. Die Fehler sind im <xref:Microsoft.AspNetCore.Components.Forms.EditContext> des Formulars zur Anzeige durch die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary> des Formulars verfügbar:

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

<EditForm Model="@starship" OnValidSubmit="HandleValidSubmit">
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
> Als Alternative zu [Validierungkomponenten](#validator-components) können Datenanmerkungs-Validierungsattribute verwendet werden. Benutzerdefinierte Attribute, die auf das Modell des Formulars angewendet werden, werden mit der Verwendung der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente aktiviert. Bei Verwendung mit serverseitiger Validierung müssen die Attribute auf dem Server ausführbar sein. Weitere Informationen finden Sie unter <xref:mvc/models/validation#alternatives-to-built-in-attributes>.

> [!NOTE]
> Der serverseitige Überprüfungsansatz in diesem Abschnitt eignet sich für jedes der Beispiele für gehostete Blazor WebAssembly-Lösungen in diesem Dokumentationssatz:
>
> * [Azure Active Directory (AAD)](xref:blazor/security/webassembly/hosted-with-azure-active-directory)
> * [Azure Active Directory (AAD) B2C](xref:blazor/security/webassembly/hosted-with-azure-active-directory-b2c)
> * [Identity Server](xref:blazor/security/webassembly/hosted-with-identity-server)

## <a name="inputtext-based-on-the-input-event"></a>InputText basierend auf dem Eingabeereignis

Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.InputText>-Komponente, um eine benutzerdefinierte Komponente zu erstellen, die das `input`-Ereignis anstelle des `change`-Ereignisses verwendet.

Im folgenden Beispiel erbt die `CustomInputText`-Komponente die `InputText`-Komponente des Frameworks und legt die Ereignisbindung (<xref:Microsoft.AspNetCore.Components.EventCallbackFactoryBinderExtensions.CreateBinder%2A>) auf das `oninput`-Ereignis fest.

`Shared/CustomInputText.razor`:

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

Die `CustomInputText`-Komponente kann überall dort verwendet werden, wo <xref:Microsoft.AspNetCore.Components.Forms.InputText> verwendet wird:

`Pages/TestForm.razor`:

```razor
@page  "/testform"
@using System.ComponentModel.DataAnnotations;

<EditForm Model="@exampleModel" OnValidSubmit="HandleValidSubmit">
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

## <a name="radio-buttons"></a>Optionsfelder

Beim Verwenden von Optionsfeldern in einem Formular werden Datenbindungen nicht wie andere Elemente verarbeitet, da Optionsfelder als Gruppe ausgewertet werden. Der Wert der einzelnen Optionsfelder ist fest. Der Wert der Optionsfeldgruppe ist jedoch der Wert des ausgewählten Optionsfelds. Das folgende Beispiel veranschaulicht die Vorgehensweise:

* Verarbeiten von Datenbindungen für eine Optionsfeldgruppe
* Unterstützen der Validierung mithilfe einer benutzerdefinierten `InputRadio`-Komponente

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

Die folgende <xref:Microsoft.AspNetCore.Components.Forms.EditForm>-Klasse verwendet die vorangehende `InputRadio`-Komponente, um eine Bewertung vom Benutzer zu erhalten und sie zu bewerten:

```razor
@page "/RadioButtonExample"
@using System.ComponentModel.DataAnnotations

<h1>Radio Button Group Test</h1>

<EditForm Model="model" OnValidSubmit="HandleValidSubmit">
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

## <a name="binding-select-element-options-to-c-object-null-values"></a>Binden von `<select>`-Elementoptionen an `null`-Werte von C#-Objekten

Es gibt aus folgenden Gründen keine vernünftige Möglichkeit, den Optionswert eines `<select>`-Elements als `null`-Wert eines C#-Objekts darzustellen:

* HTML-Attribute können keine `null`-Werte aufweisen. `null` entspricht in HTML am ehesten dem Nichtvorhandensein des `value`-HTML-Attributs im `<option>`-Element.
* Wenn Sie ein `<option>`-Element ohne `value`-Attribut auswählen, behandelt der Browser den Wert als *Textinhalt* dieses `<option>`-Elements.

Das Blazor-Framework versucht nicht, das Standardverhalten zu unterdrücken, weil dies Folgendes beinhalten würde:

* Erstellen einer Kette von Problemumgehungen für Sonderfälle im Framework.
* Breaking Changes am aktuellen Frameworkverhalten.

::: moniker range=">= aspnetcore-5.0"

Das plausibelste Äquivalent zu `null` in HTML ist eine `value` mit einer *leeren Zeichenfolge*. Das Blazor-Framework verarbeitet `null` als Konvertierung einer leeren Zeichenfolge für eine bidirektionale Bindung an einen `<select>`-Wert.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

Beim Versuch einer bidirektionalen Bindung an einen `<select>`-Wert verarbeitet das Blazor-Framework `null` nicht automatisch als Konvertierung einer leeren Zeichenfolge. Weitere Informationen finden Sie unter [Fix binding `<select>` to a null value (dotnet/aspnetcore #23221)](https://github.com/dotnet/aspnetcore/pull/23221) (Korrigieren der Bindung von <select> an einen NULL-Wert [dotnet/aspnetcore #23221]).

::: moniker-end

## <a name="validation-support"></a>Validierungsunterstützung

Die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente fügt Validierungsunterstützung mithilfe von Datenanmerkungen an die kaskadierte <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse an. Für das Aktivieren der Validierungsunterstützung mithilfe von Datenanmerkungen ist eine explizite Geste erforderlich. Wenn Sie ein anderes Validierungssystem als Datenanmerkungen verwenden möchten, ersetzen Sie <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> durch eine benutzerdefinierte Implementierung. Die ASP.NET Core-Implementierung können Sie sich in der Verweisquelle ansehen: [`DataAnnotationsValidator`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/DataAnnotationsValidator.cs)/[`AddDataAnnotationsValidation`](https://github.com/dotnet/AspNetCore/blob/master/src/Components/Forms/src/EditContextDataAnnotationsExtensions.cs). Die vorangehenden Links zur Verweisquelle stellen Code aus dem `master`-Branch des Repositorys bereit, der die aktuelle Entwicklung der Produkteinheit für das nächste Release von ASP.net Core darstellt. Um den Branch für ein anderes Release auszuwählen, verwenden Sie die GitHub-Branchauswahl (beispielsweise `release/3.1`).

Blazor führt zwei Validierungstypen aus:

* Die *Feldvalidierung* wird ausgeführt, wenn der Benutzer auf eine Stelle außerhalb des Felds tippt. Während der Feldvalidierung ordnet die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente alle gemeldeten Validierungsergebnisse dem Feld zu.
* *Modellvalidierung* wird ausgeführt, wenn der Benutzer ein Formular sendet. Während der Modellvalidierung versucht die <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, das Feld basierend auf dem Namen des Members zu ermitteln, das vom Validierungsergebnis gemeldet wird. Validierungsergebnisse, die keinem einzelnen Member zugeordnet werden, werden dem Modell und keinem Feld zugeordnet.

### <a name="validation-summary-and-validation-message-components"></a>Komponenten der Validierungszusammenfassung und der Validierungsnachricht

Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente fasst alle Validierungsnachrichten zusammen. Das ähnelt dem [Taghilfsprogramm für Validierungszusammenfassungen](xref:mvc/views/working-with-forms#the-validation-summary-tag-helper):

```razor
<ValidationSummary />
```

Validierungsmeldungen für ein bestimmtes Modell werden mit dem `Model`-Parameter ausgegeben:
  
```razor
<ValidationSummary Model="@starship" />
```

Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>-Komponente zeigt Validierungsnachrichten für ein bestimmtes Feld an. Das ähnelt dem [Taghilfsprogramm für Validierungsmeldungen](xref:mvc/views/working-with-forms#the-validation-message-tag-helper). Das Validierungsfeld wird mit dem `For`-Attribut und einem Lambdaausdruck angegeben, der die Modelleigenschaft benennt:

```razor
<ValidationMessage For="@(() => starship.MaximumAccommodation)" />
```

Die <xref:Microsoft.AspNetCore.Components.Forms.ValidationMessage%601>- und <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponenten unterstützen arbiträre Attribute. Attribute, die nicht mit einem Komponentenparameter übereinstimmen, werden dem gerenderten `<div>`- oder `<ul>`-Element hinzugefügt.

Steuern Sie den Stil von Validierungsmeldungen im Stylesheet (`wwwroot/css/app.css` oder `wwwroot/css/site.css`) der App. Die `validation-message`-Standardklasse legt die Textfarbe von Validierungsmeldungen auf Rot fest:

```css
.validation-message {
    color: red;
}
```

### <a name="custom-validation-attributes"></a>Benutzerdefinierte Validierungsattribute

Übergeben Sie die <xref:System.ComponentModel.DataAnnotations.ValidationContext.MemberName>-Eigenschaft des Validierungskontexts bei der Erstellung der <xref:System.ComponentModel.DataAnnotations.ValidationResult>-Klasse, um sicherzustellen, dass ein Validierungsergebnis korrekt einem Feld zugeordnet wird, wenn ein [benutzerdefiniertes Validierungsattribut](xref:mvc/models/validation#custom-attributes) verwendet wird:

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
> <xref:System.ComponentModel.DataAnnotations.ValidationContext.GetService%2A?displayProperty=nameWithType> ist `null`. Das Einfügen von Diensten zur Validierung in der `IsValid`-Methode wird nicht unterstützt.

### <a name="no-locblazor-data-annotations-validation-package"></a>Validierungspakete für Datenanmerkungen in Blazor

[`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) ist ein Paket, das Lücken bei der Validierung mithilfe der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente schließt. Das Paket ist aktuell *experimentell*.

> [!NOTE]
> Das [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation)-Paket enthält die neueste Version des *Release Candidate* auf [Nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation). Verwenden Sie zu diesem Zeitpunkt weiterhin das *experimentelle* Release Candidate-Paket. Die Assembly des Pakets wird möglicherweise in einer zukünftigen Version entweder in das Framework oder die Laufzeit verschoben. Weitere Aktualisierungen finden Sie im [GitHub-Repository „Announcements“](https://github.com/aspnet/Announcements) (Ankündigungen), im [dotnet/aspnetcore-Repositoryvon GitHub ](https://github.com/dotnet/aspnetcore) oder in diesem Themenabschnitt.

### <a name="compareproperty-attribute"></a>[CompareProperty]-Attribut

<xref:System.ComponentModel.DataAnnotations.CompareAttribute> funktioniert nicht gut mit der <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>-Komponente, da hier das Validierungsergebnis nicht einem bestimmten Member zugeordnet wird. Das führt zu einem nicht konsistenten Verhalten zwischen Validierung auf Feldebene und der Validierung des gesamten Modells bei Sendevorgängen. Mit dem *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) wird das zusätzliche Validierungsattribut `ComparePropertyAttribute` eingeführt, das diese Einschränkungen umgeht. In einer Blazor-App handelt es sich bei `[CompareProperty]` um eine direkte Ersetzung des [`[Compare]`](xref:System.ComponentModel.DataAnnotations.CompareAttribute)-Attributs.

### <a name="nested-models-collection-types-and-complex-types"></a>Verschachtelte Modelle, Sammlungstypen und komplexe Typen

Blazor bietet Unterstützung für die Validierung von Formulareingaben mithilfe von Datenanmerkungen mit dem integrierten <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator>. <xref:Microsoft.AspNetCore.Components.Forms.DataAnnotationsValidator> validiert jedoch nur Eigenschaften des Modells auf oberster Ebene, die an das Formular gebunden sind, bei denen es sich aber um keine Sammlungs- oder komplexen Eigenschaften handelt.

Verwenden Sie den `ObjectGraphDataAnnotationsValidator`, der im *experimentellen* Paket [`Microsoft.AspNetCore.Components.DataAnnotations.Validation`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.DataAnnotations.Validation) bereitgestellt wird, um den gesamten Objektgraph des gebundenen Modells zu validieren, einschließlich Sammlungseigenschaften und komplexer Eigenschaften:

```razor
<EditForm Model="@model" OnValidSubmit="HandleValidSubmit">
    <ObjectGraphDataAnnotationsValidator />
    ...
</EditForm>
```

Fügen Sie Modelleigenschaften Anmerkungen mit `[ValidateComplexType]` hinzu. In den folgenden Modellklassen enthält die `ShipDescription`-Klasse zusätzliche Datenanmerkungen für die Validierung, wenn das Modell an das Formular gebunden ist:

`Starship.cs`:

```csharp
using System;
using System.ComponentModel.DataAnnotations;

public class Starship
{
    ...

    [ValidateComplexType]
    public ShipDescription ShipDescription { get; set; }

    ...
}
```

`ShipDescription.cs`:

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

### <a name="enable-the-submit-button-based-on-form-validation"></a>Aktivieren der Schaltfläche zum Senden basierend auf der Formularvalidierung

So aktivieren oder deaktivieren Sie die Schaltfläche zum Senden basierend auf der Formularvalidierung:

* Verwenden Sie die <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Klasse des Formulars, um das Modell zuzuweisen, wenn die Komponente initialisiert wird.
* Validieren Sie das Formular im <xref:Microsoft.AspNetCore.Components.Forms.EditContext.OnFieldChanged>-Rückruf des Kontexts, um die Schaltfläche zum Senden zu aktivieren und zu deaktivieren.
* Heben Sie die Einbindung des Ereignishandlers in der `Dispose`-Methode auf. Weitere Informationen finden Sie unter <xref:blazor/components/lifecycle#component-disposal-with-idisposable>.

> [!NOTE]
> Wenn Sie einen <xref:Microsoft.AspNetCore.Components.Forms.EditContext> verwenden, weisen Sie <xref:Microsoft.AspNetCore.Components.Forms.EditForm> nicht außerdem ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model> zu.

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

Im vorherigen Beispiel ist `formInvalid` in folgenden Fällen auf `false` festgelegt:

* Das Formular ist bereits mit gültigen Standardwerten ausgefüllt.
* Sie möchten die Schaltfläche zum Senden aktivieren, wenn das Formular geladen wird.

Eine Nebenwirkung des vorangehenden Ansatzes ist, dass eine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente mit ungültigen Feldern aufgefüllt wird, nachdem der Benutzer mit einem beliebigen Feld interagiert hat. Dieses Szenario lässt sich folgendermaßen auflösen:

* Verwenden Sie keine <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente des Formulars.
* Lassen Sie die <xref:Microsoft.AspNetCore.Components.Forms.ValidationSummary>-Komponente einblenden, wenn auf die Schaltfläche zum Senden geklickt wird, z. B. in einer `HandleValidSubmit`-Methode.

```razor
<EditForm EditContext="@editContext" OnValidSubmit="HandleValidSubmit">
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

## <a name="troubleshoot"></a>Problembehandlung

> InvalidOperationException: EditForm erfordert einen Model-Parameter oder einen EditContext-Parameter, aber nicht beides.

Vergewissern Sie sich, dass <xref:Microsoft.AspNetCore.Components.Forms.EditForm> über ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>- **oder** über ein <xref:Microsoft.AspNetCore.Components.Forms.EditContext>-Element verfügt. Verwenden Sie nicht beides für dasselbe Formular.

Wenn Sie dem Formular ein <xref:Microsoft.AspNetCore.Components.Forms.EditForm.Model>-Element zuweisen, vergewissern Sie sich, dass der Modelltyp instanziiert wird, wie im folgenden Beispiel gezeigt:

```csharp
private ExampleModel exampleModel = new ExampleModel();
```
