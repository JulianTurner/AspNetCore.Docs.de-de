---
title: Taghilfsprogramme in Formularen in ASP.NET Core
author: rick-anderson
description: Beschreibt die integrierten Taghilfsprogramme, die mit Formularen verwendet werden.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: mvc/views/working-with-forms
ms.openlocfilehash: a568ad481eb09587e2ddce8e84d0ac3eff01e990
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060897"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a>Taghilfsprogramme in Formularen in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) und [Jerrie Pelser](https://github.com/jerriep)

In diesem Dokument wird das Arbeiten mit Formularen und mit den HTML-Elementen, die häufig in Formularen verwendet werden, veranschaulicht. Das HTML-Element [Form](https://www.w3.org/TR/html401/interact/forms.html) stellt den primären Mechanismus bereit, den Web-Apps zum Bereitstellen von Daten an den Server verwenden. In diesem Dokument werden überwiegend [Taghilfsprogramme](tag-helpers/intro.md) beschrieben sowie deren Einsatzmöglichkeiten zum produktiven Erstellen von robusten HTML-Formularen. Es wird empfohlen, [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](tag-helpers/intro.md) vor diesem Dokument zu lesen.

Häufig stellen HTML-Hilfsprogramme eine Alternative zu einem bestimmten Taghilfsprogramm dar. Allerdings ersetzen Taghilfsprogramme HTML-Hilfsprogramme nicht, und es gibt nicht für jedes HTML-Hilfsprogramm ein Taghilfsprogramm. Wenn es ein alternatives HTML-Hilfsprogramm gibt, wird dies erwähnt.

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a>Das Taghilfsprogramm für Formulare

Das taghilfsprogramm für [Formulare](https://www.w3.org/TR/html401/interact/forms.html) :

* Generiert den HTML- [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` Attribut Wert für eine MVC-Controller Aktion oder benannte Route.

* Generiert ein ausgeblendetes [Token für die Anforderungsüberprüfung](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages), um (bei Verwendung mit dem `[ValidateAntiForgeryToken]`-Attribut in der nachfolgenden HTML-Aktionsmethode) websiteübergreifende Anforderungsfälschung zu verhindern.

* Stellt das `asp-route-<Parameter Name>`-Attribut bereit, bei dem `<Parameter Name>` zu den Routenwerten hinzugefügt wird. Die `routeValues`-Parameter von `Html.BeginForm` und `Html.BeginRouteForm` bieten eine ähnliche Funktionalität.

* Verfügt über die alternativen HTML-Hilfsprogramme `Html.BeginForm` und `Html.BeginRouteForm`

Beispiel:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

Das zuvor erwähnte Taghilfsprogramm für Formulare generiert folgenden HTML-Code:

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

Die MVC-Runtime generiert den `action`-Attributwert aus den Attributen `asp-controller` und `asp-action` des Taghilfsprogramms für Formulare. Das Taghilfsprogramm für Formulare generiert ebenfalls ein ausgeblendetes [Token für die Anforderungsüberprüfung](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages), um (bei Verwendung mit dem `[ValidateAntiForgeryToken]`-Attribut in der nachfolgenden HTML-Aktionsmethode) websiteübergreifende Anforderungsfälschung zu verhindern. Es ist schwierig, ein reines HTML-Formular vor websiteübergreifender Anforderungsfälschung zu schützen. Das Taghilfsprogramm für Formulare stellt diesen Dienst für Sie bereit.

### <a name="using-a-named-route"></a>Verwenden einer benannte Route

Das `asp-route`-Attribut für Taghilfsprogramme kann ebenfalls ein Markup für das HTML-Attribut `action` generieren. Eine App mit einer [Route](../../fundamentals/routing.md) namens `register` kann folgendes Markup für die Registrierungsseite verwenden:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

Viele der Ansichten im Ordner *Views/Account* (dieser wird generiert, wenn Sie eine neue Web-App mit *einzelnen Benutzerkonten* erstellen) enthalten das Attribut [asp-route-returnurl](xref:mvc/views/working-with-forms):

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
>Durch die integrierten Vorlagen wird `returnUrl` nur automatisch aufgefüllt, wenn Sie versuchen, auf eine autorisierte Ressource zuzugreifen, aber nicht authentifiziert oder autorisiert sind. Wenn Sie versuchen, einen nicht autorisierten Zugriff durchzuführen, leitet die Sicherheitsmiddleware Sie zur Anmeldeseite weiter, bei der `returnUrl` festgelegt ist.

## <a name="the-form-action-tag-helper"></a>Das Hilfsprogramm für Formularaktionen

Das Hilfsprogramm für Formularaktionen generiert das `formaction`-Attribut auf dem generierten `<button ...>`- oder `<input type="image" ...>`-Tag. Das `formaction`-Attribut legt fest, wohin ein Formular seine Daten übermittelt. Es bindet an [\<input>](https://www.w3.org/wiki/HTML/Elements/input) Elemente des Typs `image` und von [\<button>](https://www.w3.org/wiki/HTML/Elements/button) Elementen. Das Hilfsprogramm für Formularaktionen ermöglicht die Verwendung von mehreren [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-`-Attributen, die steuern, welcher `formaction`-Link für das entsprechende Element generiert wird.

Folgende [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)-Attribute werden zum Steuern des Werts von `formaction` unterstützt:

|attribute|Beschreibung|
|---|---|
|[asp-controller](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|Der Name des Controllers.|
|[asp-action](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|Der Name der Aktionsmethode.|
|[asp-area](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|Der Name des Bereichs.|
|[asp-page](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|Der Name der Razor Seite.|
|[asp-page-handler](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|Der Name des Razor Seiten Handlers.|
|[asp-route](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|Der Name der Route.|
|[asp-route-{value}](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|Ein einzelner URL-Routenwert. Beispiel: `asp-route-id="1234"`.|
|[asp-all-route-data](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|Alle Routenwerte.|
|[asp-fragment](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|Das URL-Fragment.|

### <a name="submit-to-controller-example"></a>Beispiel des Übermittelns an den Controller

Das folgende Markup sendet das Formular bei Auswahl der Eingabe oder der Schaltfläche an die `Index`-Aktion von `HomeController`:

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

Das bisherige Markup generiert folgenden HTML-Code:

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a>Beispiel des Übermittelns an die Seite

Das folgende Markup übergibt das Formular an die `About` Razor Seite:

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

Das bisherige Markup generiert folgenden HTML-Code:

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a>Beispiel des Übermittelns an die Route

Betrachten Sie den `/Home/Test`-Endpunkt:

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

Das folgende Markup sendet das Formular an den `/Home/Test`-Endpunkt.

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

Das bisherige Markup generiert folgenden HTML-Code:

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a>Das Taghilfsprogramm für die Eingabe

Das taghilfsprogramm für die Eingabe bindet ein HTML- [\<input>](https://www.w3.org/wiki/HTML/Elements/input) Element an einen Modell Ausdruck in der Razor-Ansicht.

Syntax:

```cshtml
<input asp-for="<Expression Name>">
```

Das Taghilfsprogramm für die Eingabe:

* Generiert die HTML-Attribute `id` und `name` für den Ausdrucksnamen, der im `asp-for`-Attribut angegeben ist. `asp-for="Property1.Property2"` entspricht `m => m.Property1.Property2`. Der Name des Ausdrucks wird für den `asp-for`-Attributwert verwendet. Weitere Informationen finden Sie im Abschnitt [Ausdrucksnamen](#expression-names).

* Legt den HTML-Attributwert `type` basierend auf dem Modelltyp und den Attributen für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) fest, die auf die Modelleigenschaft angewendet werden

* Überschreibt den HTML-Attributwert `type` nicht, wenn ein solcher festgelegt ist

* Generiert [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)-Validierungsattribute aus den Attributen für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), die auf Modelleigenschaften angewendet werden

* Verfügt über eine Überschneidung der HTML-Hilfsprogrammfeatures mit `Html.TextBoxFor` und `Html.EditorFor` Weitere Informationen finden Sie im Abschnitt **Alternative HTML-Hilfsprogramme für Taghilfsprogramme für die Eingabe** .

* Stellt die starke Typisierung bereit Wenn der Name der Eigenschaft geändert wird und Sie das Taghilfsprogramm nicht aktualisieren, wird Ihnen eine Fehlermeldung ähnlich der Folgenden angezeigt:

  ```
  An error occurred during the compilation of a resource required to process
  this request. Please review the following specific error details and modify
  your source code appropriately.

  Type expected
   'RegisterViewModel' does not contain a definition for 'Email' and no
   extension method 'Email' accepting a first argument of type 'RegisterViewModel'
   could be found (are you missing a using directive or an assembly reference?)
  ```

Das `Input`-Taghilfsprogramm legt das HTML-Attribut `type` basierend auf dem .NET-Typ fest. In der folgenden Tabelle werden einige (jedoch nicht alle) allgemeine .NET-Typen sowie generierte HTML-Typen aufgeführt.

|.NET-Typ|Eingabetyp|
|---|---|
|Bool|type="checkbox"|
|String|type="text"|
|Datetime|type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)|
|Byte|type="number"|
|Int|type="number"|
|Single, Double|type="number"|

In der folgenden Tabelle werden einige allgemeine Attribute für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aufgeführt, die das Taghilfsprogramm für die Eingabe bestimmten Eingabetypen zuordnet (nicht jedes Validierungsattribut wird aufgeführt):

|attribute|Eingabetyp|
|---|---|
|[EmailAddress]|type="email"|
|[Url]|type="url"|
|[HiddenInput]|type="hidden"|
|[Phone]|type="tel"|
|[DataType(DataType.Password)]|type="password"|
|[DataType(DataType.Date)]|type="date"|
|[DataType(DataType.Time)]|type="time"|

Beispiel:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

Der oben stehende Code generiert folgenden HTML-Code:

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

Die Datenanmerkungen, die auf die `Email`- und `Password`-Eigenschaft angewendet werden, generieren Metadaten im Modell. Das Taghilfsprogramm für die Eingabe nutzt die Metadaten des Modells und erzeugt [HTML5-](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*`-Attribute (siehe [Modellvalidierung](../models/validation.md)). Diese Attribute beschreiben das Validierungssteuerelement, das den Eingabefeldern angefügt werden soll. Dadurch wird die unaufdringliche Validierung für HTML5 und [jQuery](https://jquery.com/) bereitgestellt. Die unaufdringlichen Attribute weisen das Format `data-val-rule="Error Message"` auf, wobei Rule der Name der Validierungs Regel ist (z `data-val-required` . b., `data-val-email` , `data-val-maxlength` usw.). Wenn eine Fehlermeldung im-Attribut bereitgestellt wird, wird Sie als Wert für das- `data-val-rule` Attribut angezeigt. Es gibt ebenfalls Attribute im Format `data-val-ruleName-argumentName="argumentValue"`, die zusätzliche Details zur Regel bereitstellen, z.B. `data-val-maxlength-max="1024"`.

### <a name="html-helper-alternatives-to-input-tag-helper"></a>Alternative HTML-Hilfsprogramme zum Taghilfsprogramm für die Eingabe

`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` und `Html.EditorFor` verfügen über sich überschneidende Features mit dem Taghilfsprogramm für die Eingabe. Das Taghilfsprogramm für die Eingabe legt das `type`-Attribut automatisch fest, `Html.TextBox` und `Html.TextBoxFor` jedoch nicht. `Html.Editor` und `Html.EditorFor` verarbeiten Auflistungen, komplexe Objekte und Vorlagen, das Taghilfsprogramm für die Eingabe jedoch nicht. Das Taghilfsprogramm für die Eingabe, `Html.EditorFor` und `Html.TextBoxFor` sind stark typisiert (sie verwenden Lambdaausdrücke), `Html.TextBox` und `Html.Editor` jedoch nicht (sie verwenden Ausdrucksnamen).

### <a name="htmlattributes"></a>HtmlAttributes

`@Html.Editor()` und `@Html.EditorFor()` verwenden einen speziellen `ViewDataDictionary`-Eintrag namens `htmlAttributes`, wenn die Standardvorlagen ausgeführt werden. Dieses Verhalten kann optional mithilfe des `additionalViewData`-Parameters erweitert werden. Der Schlüssel „htmlAttributes“ berücksichtigt die Groß-/Kleinschreibung nicht. Der Schlüssel „htmlAttributes“ wird ähnlich wie das `htmlAttributes`-Objekt behandelt, das an Hilfsprogramme für die Eingabe wie `@Html.TextBox()` übergeben wird.

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a>Ausdrucksnamen

Der `asp-for`-Attributwert stellt eine `ModelExpression`-Klasse sowie die rechte Seite eines Lambdaausdrucks dar. Somit wird `asp-for="Property1"` im generierten Code zu `m => m.Property1`. Dadurch benötigen Sie das Präfix `Model` nicht. Sie können das \@-Zeichen verwenden, um einen Inlineausdruck zu starten und diesen vor `m.` zu verschieben:

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

Folgendes wird generiert:

```html
<input type="text" id="joe" name="joe" value="Joe">
```

Mit den Auflistungseigenschaften generiert `asp-for="CollectionProperty[23].Member"` den gleichen Namen wie `asp-for="CollectionProperty[i].Member"`, wenn `i` den Wert `23` besitzt.

Wenn ASP.NET Core MVC den Wert von `ModelExpression` berechnet, werden verschiedene Quellen untersucht, unter anderem `ModelState`. Gehen Sie von `<input type="text" asp-for="@Name">` aus. Das berechnete `value`-Attribut entspricht dem ersten der folgenden Werte, der nicht NULL ist:

* `ModelState`-Eintrag mit dem Schlüssel „Name“
* Ergebnis des Ausdrucks `Model.Name`

### <a name="navigating-child-properties"></a>Navigieren zu untergeordneten Eigenschaften

Sie können ebenfalls mithilfe des Eigenschaftenpfads des Ansichtsmodells zu untergeordneten Eigenschaften navigieren. Betrachten Sie eine komplexere Modellklasse, die eine untergeordnete `Address`-Eigenschaft enthält.

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

In der Ansicht wird eine Bindung an `Address.AddressLine1` erstellt:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

Folgender HTML-Code wird für `Address.AddressLine1` generiert:

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a>Ausdrucksnamen und Auflistungen

Beispiel für ein Modell, das ein Array von `Colors` enthält:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

Die Aktionsmethode:

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

Im folgenden wird Razor gezeigt, wie Sie auf ein bestimmtes `Color` Element zugreifen:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

Die Vorlage *Views/Shared/EditorTemplates/String.cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

Beispiel mithilfe von `List<T>`:

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

Im folgenden wird Razor gezeigt, wie Sie eine Auflistung durchlaufen:

[!code-cshtml[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

Die Vorlage *Views/Shared/EditorTemplates/ToDoItem.cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

Wenn der Wert in einem Kontext wie `asp-for` oder `Html.DisplayFor` ausgeführt wird, sollte nach Möglichkeit `foreach` verwendet werden. Allgemein ist `for` besser als `foreach` (sofern das Szenario dies zulässt), weil kein Enumerator zugewiesen werden muss. Die Auswertung eines Indexers in einem LINQ-Ausdruck kann jedoch teuer sein und sollte auf ein Mindestmaß reduziert werden.

&nbsp;

>[!NOTE]
>Der oben stehende kommentierte Beispielcode veranschaulicht das Ersetzen eines Lambdaausdrucks mit dem `@`-Operator, um auf jedes `ToDoItem`-Objekt in der Liste zuzugreifen.

## <a name="the-textarea-tag-helper"></a>Das Taghilfsprogramm für Textbereiche

Das `Textarea Tag Helper`-Taghilfsprogramm ähnelt dem Taghilfsprogramm für die Eingabe.

* Generiert das `id` - `name` Attribut und das-Attribut sowie die Daten Validierungs Attribute aus dem Modell für ein- [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) Element.

* Stellt die starke Typisierung bereit

* Alternatives HTML-Hilfsprogramm: `Html.TextAreaFor`

Beispiel:

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

Folgender HTML-Code wird generiert:

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a>Das Taghilfsprogramm für Bezeichnungen

* Generiert die Beschriftungs Beschriftung und das `for` Attribut [\<label>](https://www.w3.org/wiki/HTML/Elements/label) für ein-Element für einen Ausdrucks Namen.

* Alternatives HTML-Hilfsprogramm: `Html.LabelFor`

`Label Tag Helper` bietet folgende Vorteile gegenüber einem reinen HTML-Bezeichnungselement:

* Sie erhalten den aussagekräftigen Bezeichnungswert aus dem `Display`-Attribut. Der gewünschte Anzeigename kann sich mit der Zeit ändern, und die Kombination des `Display`-Attributs und des Taghilfsprogramms für Bezeichnungen wendet `Display` überall dort an, wo es verwendet wird.

* Weniger Markup im Quellcode

* Starke Typisierung mit der Modelleigenschaft

Beispiel:

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

Folgender HTML-Code wird für das `<label>`-Element generiert:

```html
<label for="Email">Email Address</label>
```

Das Taghilfsprogramm für Bezeichnungen hat den `for`-Attributwert „Email“ generiert, bei dem es sich um die ID handelt, die dem `<input>`-Element zugeordnet wird. Die Taghilfsprogramme generieren konsistente `id`- und `for`-Elemente, sodass diese ordnungsgemäß zugeordnet werden können. Die Beschriftung in diesem Beispiel ergibt sich aus dem `Display`-Attribut. Wenn das Modell kein `Display`-Attribut enthalten hat, würde die Beschriftung dem Eigenschaftsname des Ausdrucks entsprechen.

## <a name="the-validation-tag-helpers"></a>Die Taghilfsprogramme für die Validierung

Es gibt zwei Taghilfsprogramme für die Validierung. `Validation Message Tag Helper` (zeigt eine Validierungsmeldung für eine einzelne Eigenschaft im Modell an) und `Validation Summary Tag Helper` (zeigt eine Zusammenfassung der Validierungsfehler an). `Input Tag Helper` fügt clientseitige HTML5-Validierungsattribute zu Eingabeelementen hinzu, die auf den Attributen für die Datenanmerkung Ihrer Modellklassen basieren. Die Validierung wird ebenfalls auf dem Server ausgeführt. Das Taghilfsprogramm für die Validierung zeigt diese Fehlermeldungen an, wenn ein Validierungsfehler auftritt.

### <a name="the-validation-message-tag-helper"></a>Das Taghilfsprogramm für Validierungsmeldungen

* Fügt das [HTML5-](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"`-Attribut zum [span](https://developer.mozilla.org/docs/Web/HTML/Element/span)-Element hinzu, wodurch die Validierungsfehlermeldung an das Eingabefeld der angegebenen Modelleigenschaft angefügt wird. Wenn ein clientseitiger Validierungsfehler auftritt, zeigt [jQuery](https://jquery.com/) die Fehlermeldung im `<span>`-Element an.

* Die Validierung wird ebenfalls auf dem Server ausgeführt. JavaScript ist auf Clients möglicherweise deaktiviert, und einige Validierungen können nur auf Serverseite ausgeführt werden.

* Alternatives HTML-Hilfsprogramm: `Html.ValidationMessageFor`

`Validation Message Tag Helper` wird mit dem `asp-validation-for`-Attribut eines [span](https://developer.mozilla.org/docs/Web/HTML/Element/span)-HTML-Elements verwendet.

```cshtml
<span asp-validation-for="Email"></span>
```

Das Taghilfsprogramm für Validierungsmeldungen generiert folgenden HTML-Code:

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

Im Allgemeinen verwenden Sie `Validation Message Tag Helper` nach einem `Input`-Taghilfsprogramm für die gleiche Eigenschaft. Dadurch werden alle Validierungsfehlermeldungen in der Nähe der Eingabe angezeigt, die den Fehler verursacht hat.

> [!NOTE]
> Für die clientseitige Validierung müssen Sie über eine Ansicht mit den richtigen JavaScript- und [jQuery](https://jquery.com/)-Skriptverweisen verfügen. Weitere Informationen finden Sie unter [Modell Validierung](../models/validation.md) .

Wenn ein serverseitiger Validierungsfehler auftritt (wenn Sie z.B. eine benutzerdefinierte serverseitige Validierung durchführen oder die clientseitige Validierung deaktiviert ist), legt MVC diese Fehlermeldung als Text des `<span>`-Elements fest.

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a>Das Taghilfsprogramm für Validierungszusammenfassungen

* Zielt `<div>`-Elemente mit dem `asp-validation-summary`-Attribut an

* Alternatives HTML-Hilfsprogramm: `@Html.ValidationSummary`

`Validation Summary Tag Helper` wird verwendet, um eine Zusammenfassung der Validierungsmeldungen anzuzeigen. Der `asp-validation-summary`-Attributwert kann Folgendem entsprechen:

|asp-validation-summary|Validierungsmeldungen werden angezeigt|
|--- |--- |
|ValidationSummary.All|Eigenschaften- und Modellebene|
|ValidationSummary.ModelOnly|Modell|
|ValidationSummary.None|Keine|

### <a name="sample"></a>Beispiel

Im folgenden Beispiel enthält das Datenmodell `DataAnnotation`-Attribute, wodurch Validierungsfehlermeldungen im `<input>`-Element generiert werden.  Das Taghilfsprogramm für die Validierung zeigt diese Fehlermeldung an, wenn ein Validierungsfehler auftritt:

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

Der generierte HTML-Code (wenn das Modell gültig ist):

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a>Das Taghilfsprogramm für die Auswahl

* Generiert das [select](https://www.w3.org/wiki/HTML/Elements/select)-Element und die zugehörigen [option](https://www.w3.org/wiki/HTML/Elements/option)-Elemente für die Eigenschaften des Modells.

* Verfügt über die alternativen HTML-Hilfsprogramme `Html.DropDownListFor` und `Html.ListBoxFor`

Das `Select Tag Helper` `asp-for` gibt den Namen der Modelleigenschaft für das [select](https://www.w3.org/wiki/HTML/Elements/select)-Element an, und `asp-items` legt die [option](https://www.w3.org/wiki/HTML/Elements/option)-Elemente fest.  Zum Beispiel:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

Beispiel:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

Die `Index`-Methode initialisiert `CountryViewModel`, legt das ausgewählte Land fest und übergibt dieses an die `Index`-Ansicht.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

Die HTTP-POST-Methode `Index` zeigt die Auswahl an:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

Die Ansicht `Index`:

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

Diese generiert folgenden HTML-Code (wenn „CA“ ausgewählt ist):

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> Es wird nicht empfohlen, `ViewBag` oder `ViewData` mit dem Taghilfsprogramm für die Auswahl zu verwenden. Ein Ansichtsmodell kann MVC-Metadaten stabiler bereitstellen und ist in der Regel weniger problematisch.

Beim `asp-for`-Attributwert handelt es sich um einen Sonderfall, bei dem kein `Model`-Präfix erforderlich ist. Für andere Attribute von Taghilfsprogrammen (z.B. `asp-items`) ist dies jedoch erforderlich.

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a>Enumerationsbindung

Häufig ist es sinnvoll, `<select>` mit einer `enum`-Eigenschaft zu verwenden und das `SelectListItem`-Element aus den `enum`-Werten zu generieren.

Beispiel:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

Die `GetEnumSelectList`-Methode generiert ein `SelectList`-Objekt für eine Enumeration.

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

Sie können die Enumeratorliste mit dem `Display`-Attribut markieren, um eine umfangreichere Benutzeroberfläche zu erhalten:

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

Folgender HTML-Code wird generiert:

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a>Optionsgruppe

Das HTML-  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) Element wird generiert, wenn das Ansichts Modell mindestens ein- `SelectListGroup` Objekt enthält.

`CountryViewModelGroup` teilt die `SelectListItem`-Elemente den Gruppen „North America“ (Nordamerika) und „Europe“ (Europa) zu:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

Die beiden Gruppen werden im Folgenden dargestellt:

![Beispiel für Optionsgruppen](working-with-forms/_static/grp.png)

Der generierte HTML-Code:

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a>Mehrfachauswahl

Das Taghilfsprogramm für die Auswahl generiert automatisch das Attribut [multiple = "multiple"](https://w3c.github.io/html-reference/select.html), wenn die Eigenschaft, die im `asp-for`-Attribut angegeben wird, eine `IEnumerable`-Schnittstelle ist. Betrachten Sie beispielsweise folgendes Modell:

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

Mit folgender Ansicht

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

wird der folgende HTML-Code generiert:

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a>Keine Auswahl

Wenn Sie die Option „not specified“ auf mehreren Seiten verwenden, können Sie eine Vorlage erstellen, um Wiederholungen aus dem HTML-Code zu entfernen:

[!code-cshtml[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

Die Vorlage *Views/Shared/EditorTemplates/CountryViewModel.cshtml* :

[!code-cshtml[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

Das Hinzufügen von HTML- [\<option>](https://www.w3.org/wiki/HTML/Elements/option) Elementen ist nicht auf den Fall *ohne Auswahl* beschränkt. Durch die folgende Ansichts- und Aktionsmethode wird beispielsweise HTML-Code generiert, der dem oben stehenden Code ähnelt:

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-cshtml[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

Das richtige `<option>`-Element (enthält das `selected="selected"`-Attribut) wird abhängig vom aktuellen `Country`-Wert ausgewählt.

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/tag-helpers/intro>
* [HTML-Formularelement](https://www.w3.org/TR/html401/interact/forms.html)
* [Token für die Anforderungsüberprüfung](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [IAttributeAdapter-Schnittstelle](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [Codeauschnitte für dieses Dokument](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
