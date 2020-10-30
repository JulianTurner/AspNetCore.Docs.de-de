---
title: Taghilfsprogrammkomponenten in ASP.NET Core
author: scottaddie
description: Informationen zu Taghilfsprogrammkomponenten und deren Verwendung in ASP.NET Core.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
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
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: 15bddd8ce18546bef7ee7e6ec2e32e369d0858a3
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060559"
---
# <a name="tag-helper-components-in-aspnet-core"></a>Taghilfsprogrammkomponenten in ASP.NET Core

Von [Scott Addie](https://twitter.com/Scott_Addie) und [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

Eine Taghilfsprogrammkomponente ist ein Taghilfsprogramm, mit dem Sie HTML-Elemente aus serverseitigem Code bedingt ändern oder hinzufügen können. Dieses Feature ist in ASP.NET Core 2.0 oder höher verfügbar.

ASP.NET Core enthält zwei integrierte Taghilfsprogrammkomponenten: `head` und `body`. Sie befinden sich im <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> -Namespace und können sowohl in MVC als auch auf Razor Seiten verwendet werden. Taghilfsprogrammkomponenten benötigen keine Registrierung bei der App in *_ViewImports.cshtml* .

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="use-cases"></a>Anwendungsfälle

Zwei gängige Anwendungsfälle für Taghilfsprogrammkomponenten sind:

1. [Einfügen eines `<link>` in `<head>`.](#inject-into-html-head-element)
1. [Einfügen eines `<script>` in `<body>`.](#inject-into-html-body-element)

In den folgenden Abschnitten werden diese Anwendungsfälle beschrieben.

### <a name="inject-into-html-head-element"></a>Einfügen in HTML-head-Element

Im HTML-`<head>`-Element werden CSS-Dateien häufig mit dem HTML-`<link>`-Element importiert. Der folgende Code fügt ein `<link>`-Element in das `<head>`-Element mit der `head`-Taghilfsprogrammkomponente ein:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

Im obigen Code:

* `AddressStyleTagHelperComponent` implementiert <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>. Die Abstraktion:
  * Ermöglicht die Initialisierung der Klasse mit einem <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext>.
  * Ermöglicht die Verwendung von Taghilfsprogrammkomponenten zum Hinzufügen oder Ändern von HTML-Elementen.
* Die <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*>-Eigenschaft definiert die Reihenfolge, in der die Komponenten gerendert werden. `Order` ist erforderlich, wenn mehrere Verwendungsmöglichkeiten für Taghilfsprogrammkomponenten in einer App vorhanden sind.
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> vergleicht den <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*>-Eigenschaftswert des Ausführungskontexts mit `head`. Wenn der Vergleich zu TRUE ausgewertet wird, wird der Inhalt des `_style`-Felds in das HTML`<head>`-Element eingefügt.

### <a name="inject-into-html-body-element"></a>Einfügen in HTML-body-Element

Die `body`-Taghilfsprogrammkomponente kann ein `<script>`-Element in das `<body>`-Element einfügen. Im folgenden Code wird diese Technik veranschaulicht:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

Eine separate HTML-Datei wird zum Speichern des `<script>`-Elements verwendet. Durch die HTML-Datei wird der Code übersichtlicher und besser verwaltbar. Der vorangehende Code liest den Inhalt von *TagHelpers/Templates/AddressToolTipScript.html* und fügt diesen mit der Ausgabe des Taghilfsprogramms an. Die Datei *AddressToolTipScript.html* enthält das folgende Markup:

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

Der vorangehende Code bindet ein [Bootstrap-QuickInfo-Widget](https://getbootstrap.com/docs/3.3/javascript/#tooltips) an ein beliebiges `<address>`-Element, das ein `printable`-Attribut enthält. Der Effekt wird sichtbar, wenn ein Mauszeiger über das Element bewegt wird.

## <a name="register-a-component"></a>Registrieren einer Komponente

Eine Taghilfsprogrammkomponente muss der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt werden. Es gibt drei Möglichkeiten, der Sammlung Taghilfsprogrammkomponenten hinzuzufügen:

* [Registrierung über Dienstcontainer](#registration-via-services-container)
* [Registrierung über die Razor Datei](#registration-via-razor-file)
* [Registrierung über Seitenmodell oder Controller](#registration-via-page-model-or-controller)

### <a name="registration-via-services-container"></a>Registrierung über Dienstcontainer

Wenn die Klasse der Taghilfsprogrammkomponente nicht mit <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> verwaltet wird, muss sie mit dem [Abhängigkeitsinjektionssystem (Dependency Injection, DI)](xref:fundamentals/dependency-injection) registriert werden. Der folgende `Startup.ConfigureServices`-Code registriert die `AddressStyleTagHelperComponent`- und `AddressScriptTagHelperComponent`- -Klassen mit einer [vorübergehenden Lebensdauer](xref:fundamentals/dependency-injection#lifetime-and-registration-options):

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-no-locrazor-file"></a>Registrierung über die Razor Datei

Wenn die taghilfskomponente nicht bei di registriert ist, kann Sie Razor auf einer Seiten Seite oder in einer MVC-Ansicht registriert werden. Diese Technik wird zum Steuern des injizierten Markups und der Reihenfolge der Komponenten Ausführung aus einer Razor Datei verwendet.

`ITagHelperComponentManager` dient zum Hinzufügen von Taghilfsprogrammkomponenten oder zum Entfernen aus der App. Im folgenden Code wird diese Technik mit `AddressTagHelperComponent` veranschaulicht:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

Im obigen Code:

* Die `@inject`-Direktive stellt eine Instanz von `ITagHelperComponentManager` zur Verfügung. Die-Instanz wird einer Variablen mit `manager` dem Namen für Access Downstream in der Razor Datei zugewiesen.
* Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.

`AddressTagHelperComponent` wird geändert, um einen Konstruktor zu berücksichtigen, der die `markup`- und `order`-Parameter annimmt:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

Der bereitgestellte `markup`-Parameter wird in `ProcessAsync` wie folgt verwendet:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>Registrierung über Seitenmodell oder Controller

Wenn die taghilfskomponente nicht bei di registriert ist, kann Sie über ein Razor Seiten Modell oder einen MVC-Controller registriert werden. Dieses Verfahren ist nützlich, um c#-Logik von Dateien zu trennen Razor .

Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen. Die Taghilfsprogrammkomponente wird der Sammlung „Taghilfsprogrammkomponenten“ der Instanz hinzugefügt. Das folgende Razor Seiten Modell veranschaulicht diese Vorgehensweise mit `AddressTagHelperComponent` :

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

Im obigen Code:

* Die Konstruktorinjektion wird verwendet, um auf eine Instanz von `ITagHelperComponentManager` zuzugreifen.
* Eine Instanz von `AddressTagHelperComponent` wird der Sammlung „Taghilfsprogrammkomponenten“ der App hinzugefügt.

## <a name="create-a-component"></a>Erstellen einer Komponente

So erstellen Sie eine benutzerdefinierte Taghilfsprogrammkomponente:

* Erstellen Sie eine öffentliche Klasse, die von <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> ableitet ist.
* Wenden [`[HtmlTargetElement]`](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) Sie ein Attribut auf die Klasse an. Geben Sie den Namen des HTML-Zielelements an.
* *Optional* : wenden [`[EditorBrowsable(EditorBrowsableState.Never)]`](xref:System.ComponentModel.EditorBrowsableAttribute) Sie ein Attribut auf die Klasse an, um die Anzeige des Typs in IntelliSense zu unterdrücken.

Der folgende Code erstellt eine benutzerdefinierte Taghilfsprogrammkomponente, das als Ziel das `<address>`-HTML-Element aufweist:

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

Verwenden Sie die benutzerdefinierte `address`-Taghilfsprogrammkomponente zum Einfügen von HTML-Markup wie hier gezeigt:

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open(" +
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

Die obige `ProcessAsync`-Methode injiziert den für <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> bereitgestellten HTML-Code in das entsprechende `<address>`-Element. Die Einfügung tritt auf, wenn:

* Der `TagName`-Eigenschaftswert des Ausführungskontexts `address` entspricht.
* Das entsprechende `<address>`-Element über ein `printable`-Attribut verfügt.

Beispielsweise wird die `if`-Anweisung bei der Verarbeitung des folgenden `<address>`-Elements zu TRUE ausgewertet:

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
