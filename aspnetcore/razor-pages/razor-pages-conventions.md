---
title: 'Razor Pages: Routen- und App-Konventionen in ASP.NET Core'
author: rick-anderson
description: Erfahren Sie, wie Konventionen für Routen- und App-Modellanbieter Sie beim Steuern von Seitenrouting, Ermittlung und Verarbeitung unterstützen können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 2947bf0b697ca01f17d260b9f31aa3cc79d457b6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059870"
---
# <a name="no-locrazor-pages-route-and-app-conventions-in-aspnet-core"></a>Razor Pages: Routen- und App-Konventionen in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Erfahren Sie, wie Sie in Apps für Razor Pages mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Um eine Seitenroute anzugeben oder einer Route Segmente oder Parameter hinzuzufügen, verwenden Sie die Anweisung `@page` der Seite. Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routensegmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: Reservierte Routingnamen](xref:mvc/controllers/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Razor Pages-Konventionen werden mithilfe einer <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>-Überladung konfiguriert, die <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> in `Startup.ConfigureServices` konfiguriert. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add( ... );
        options.Conventions.AddFolderRouteModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageRouteModelConvention(
            "/About", model => { ... });
        options.Conventions.AddPageRoute(
            "/Contact", "TheContactPage/{text?}");
        options.Conventions.AddFolderApplicationModelConvention(
            "/OtherPages", model => { ... });
        options.Conventions.AddPageApplicationModelConvention(
            "/About", model => { ... });
        options.Conventions.ConfigureFilter(model => { ... });
        options.Conventions.ConfigureFilter( ... );
    });
}
```

## <a name="route-order"></a>Routenreihenfolge

Routen geben eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung an (Routenabgleich).

| Auftrag            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird vor anderen Routen verarbeitet. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird für die Verarbeitung der Route `Order` standardmäßig auf 0 (null) gesetzt. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge für die Route an. |

Die Routenverarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen dieselbe `Order` aufweisen, wird zuerst die spezifischste Route abgeglichen, danach folgen die weniger spezifischen Routen.
* Wenn Routen mit derselben `Order` und derselben Anzahl Parameter mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> hinzugefügt wurden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Verarbeitungsreihenfolge für Routen. Im Allgemeinen wird beim Routing die richtige Route mittels URL-Zuordnung ausgewählt. Wenn Sie zum ordnungsgemäßen Weiterleiten von Anforderungen für die Route `Order`-Eigenschaften festlegen müssen, ist das Routingschema der App für Clients wahrscheinlich irreführend und in Bezug auf die Verwaltung störanfälliger. Versuchen Sie daher, das Routingschema der App zu vereinfachen. Bei der Beispiel-App ist eine explizite Verarbeitungsreihenfolge für Routen erforderlich, um mit einer einzigen App verschiedene Routingszenarios veranschaulichen zu können. In Produktions-Apps sollten Sie jedoch nach Möglichkeit eine Festlegung von `Order` für Routen vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Reihenfolge von Routen in den MVC-Themen finden Sie unter [Routing zu Controlleraktionen: Ordnen der Attributrouten](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modellkonventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die auf Razor Pages anwendbar sind.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routenmodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenroutenmodellen eingesetzt werden.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Routenabgleichsverhalten in der Beispiel-App sichergestellt:

* Eine Routenvorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Standardreihenfolge der Route für die Kontaktseite lautet `null` (`Order = 0`). Somit wird sie vor der Routenvorlage `{globalTemplate?}` abgeglichen.
* Eine Routenvorlage für `{aboutTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine Routenvorlage für `{otherPagesTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameter (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Razor Pages-Optionen wie das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> werden hinzugefügt, wenn Razor Pages zur Dienstsammlung in `Startup.ConfigureServices` hinzugefügt wird. In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer App-Modellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenanwendungsmodellen eingesetzt werden.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer Handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenhandlermodellen eingesetzt werden.

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardanbieter für Routenmodelle, der von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> abgeleitet wird, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenrouten bereitzustellen.

### <a name="folder-route-model-convention"></a>Ordnerroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für alle Seiten im angegebenen Ordner aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameterwert (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seitenroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn die Seite „Info“ mit einem Routenparameterwert unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden eines Parametertransformators zum Anpassen von Seitenrouten

Von ASP.NET Core generierte Seitenrouten können mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Mit der Seitenroutenmodellkonvention `PageRouteTransformerConvention` wird ein Parametertransformator auf die Ordner- und Dateinamenssegmente von automatisch generierten Seitenrouten in einer App angewendet. Für die Razor Pages-Datei unter */Pages/SubscriptionManagement/ViewAll.cshtml* wird die Route beispielsweise von `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all` umgeschrieben.

Mit `PageRouteTransformerConvention` werden nur die automatisch generierten Segmente einer Seitenroute transformiert, die aus dem Ordner- und Dateinamen von Razor Pages stammen. Mit der `@page`-Anweisung hinzugefügte Routensegmente werden nicht transformiert. Auch durch <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> hinzugefügte Routen werden mit dieser Konvention nicht transformiert.

Die `PageRouteTransformerConvention` wird als Option in `Startup.ConfigureServices` registriert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages(options =>
    {
        options.Conventions.Add(
            new PageRouteTransformerConvention(
                new SlugifyParameterTransformer()));
    });
}
```

[!code-csharp[](~/mvc/controllers/routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

[!INCLUDE[](~/includes/regex.md)]

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Konfigurieren Sie mithilfe von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> eine Route zu einer Seite am angegebenen Seitenpfad. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seitenmodelle, der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> implementiert, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenmodellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispielanwendung eine `AddHeaderAttribute`-Klasse, also ein <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, das einen Antwortheader verwendet:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten im angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

Der angegebene Filter, der angewendet werden soll, wird mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Wenn der Pfad `OtherPages/Page2` nicht enthält, hat der `EmptyFilter`, wie vorgesehen, keine Auswirkung, da Aktionsfilter von Razor Pages ignoriert werden.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

Mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> wird die angegebene Zuordnungsinstanz so konfiguriert, dass sie [Filter](xref:mvc/controllers/filters) auf alle Razor Pages anwendet.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da Razor Pages Handlermethoden verwenden. Darüber hinaus stehen die folgenden MVC-Filtertypen zur Verfügung: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seitenfilter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der auf Razor Pages anwendbar ist. Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Erfahren Sie, wie Sie in Apps für Razor Pages mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Um eine Seitenroute anzugeben oder einer Route Segmente oder Parameter hinzuzufügen, verwenden Sie die Anweisung `@page` der Seite. Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routensegmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: Reservierte Routingnamen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Konventionen für Razor Pages werden <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> mithilfe der Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> unter der Dienstklasse in der Klasse `Startup` hinzugefügt. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Routenreihenfolge

Routen geben eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung an (Routenabgleich).

| Auftrag            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird vor anderen Routen verarbeitet. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird für die Verarbeitung der Route `Order` standardmäßig auf 0 (null) gesetzt. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge für die Route an. |

Die Routenverarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen dieselbe `Order` aufweisen, wird zuerst die spezifischste Route abgeglichen, danach folgen die weniger spezifischen Routen.
* Wenn Routen mit derselben `Order` und derselben Anzahl Parameter mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> hinzugefügt wurden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Verarbeitungsreihenfolge für Routen. Im Allgemeinen wird beim Routing die richtige Route mittels URL-Zuordnung ausgewählt. Wenn Sie zum ordnungsgemäßen Weiterleiten von Anforderungen für die Route `Order`-Eigenschaften festlegen müssen, ist das Routingschema der App für Clients wahrscheinlich irreführend und in Bezug auf die Verwaltung störanfälliger. Versuchen Sie daher, das Routingschema der App zu vereinfachen. Bei der Beispiel-App ist eine explizite Verarbeitungsreihenfolge für Routen erforderlich, um mit einer einzigen App verschiedene Routingszenarios veranschaulichen zu können. In Produktions-Apps sollten Sie jedoch nach Möglichkeit eine Festlegung von `Order` für Routen vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Reihenfolge von Routen in den MVC-Themen finden Sie unter [Routing zu Controlleraktionen: Ordnen der Attributrouten](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modellkonventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die auf Razor Pages anwendbar sind.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routenmodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenroutenmodellen eingesetzt werden.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Routenabgleichsverhalten in der Beispiel-App sichergestellt:

* Eine Routenvorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Standardreihenfolge der Route für die Kontaktseite lautet `null` (`Order = 0`). Somit wird sie vor der Routenvorlage `{globalTemplate?}` abgeglichen.
* Eine Routenvorlage für `{aboutTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine Routenvorlage für `{otherPagesTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameter (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Optionen für Razor Pages, z. B. das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn MVC der Dienstauflistung in `Startup.ConfigureServices` hinzugefügt wird. In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer App-Modellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenanwendungsmodellen eingesetzt werden.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer Handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenhandlermodellen eingesetzt werden.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardanbieter für Routenmodelle, der von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> abgeleitet wird, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenrouten bereitzustellen.

### <a name="folder-route-model-convention"></a>Ordnerroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für alle Seiten im angegebenen Ordner aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameterwert (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seitenroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn die Seite „Info“ mit einem Routenparameterwert unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>Verwenden eines Parametertransformators zum Anpassen von Seitenrouten

Von ASP.NET Core generierte Seitenrouten können mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Mit der Seitenroutenmodellkonvention `PageRouteTransformerConvention` wird ein Parametertransformator auf die Ordner- und Dateinamenssegmente von automatisch generierten Seitenrouten in einer App angewendet. Für die Razor Pages-Datei unter */Pages/SubscriptionManagement/ViewAll.cshtml* wird die Route beispielsweise von `/SubscriptionManagement/ViewAll` in `/subscription-management/view-all` umgeschrieben.

Mit `PageRouteTransformerConvention` werden nur die automatisch generierten Segmente einer Seitenroute transformiert, die aus dem Ordner- und Dateinamen von Razor Pages stammen. Mit der `@page`-Anweisung hinzugefügte Routensegmente werden nicht transformiert. Auch durch <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> hinzugefügte Routen werden mit dieser Konvention nicht transformiert.

Die `PageRouteTransformerConvention` wird als Option in `Startup.ConfigureServices` registriert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add(
                new PageRouteTransformerConvention(
                    new SlugifyParameterTransformer()));
        });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Konfigurieren Sie mithilfe von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> eine Route zu einer Seite am angegebenen Seitenpfad. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seitenmodelle, der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> implementiert, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenmodellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispielanwendung eine `AddHeaderAttribute`-Klasse, also ein <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, das einen Antwortheader verwendet:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten im angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

Der angegebene Filter, der angewendet werden soll, wird mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Wenn der Pfad `OtherPages/Page2` nicht enthält, hat der `EmptyFilter`, wie vorgesehen, keine Auswirkung, da Aktionsfilter von Razor Pages ignoriert werden.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

Mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> wird die angegebene Zuordnungsinstanz so konfiguriert, dass sie [Filter](xref:mvc/controllers/filters) auf alle Razor Pages anwendet.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da Razor Pages Handlermethoden verwenden. Darüber hinaus stehen die folgenden MVC-Filtertypen zur Verfügung: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seitenfilter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der auf Razor Pages anwendbar ist. Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Erfahren Sie, wie Sie in Apps für Razor Pages mithilfe von [Konventionen für Seitenrouten und App-Modellanbieter](xref:mvc/controllers/application-model#conventions) das Seitenrouting, die Ermittlung und die Verarbeitung steuern können.

Wenn Sie für einzelne Seiten benutzerdefinierte Seitenrouten konfigurieren müssen, sollten Sie das Routing zu den Seiten mithilfe der [AddPageRoute-Konvention](#configure-a-page-route) konfigurieren, die weiter unten in diesem Artikel beschrieben wird.

Um eine Seitenroute anzugeben oder einer Route Segmente oder Parameter hinzuzufügen, verwenden Sie die Anweisung `@page` der Seite. Weitere Informationen finden Sie unter [Benutzerdefinierte Routen](xref:razor-pages/index#custom-routes).

Es gibt reservierte Wörter, die nicht als Routensegmente oder Parameternamen verwendet werden können. Weitere Informationen finden Sie unter [Routing: Reservierte Routingnamen](xref:fundamentals/routing#reserved-routing-names).

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

| Szenario | Dieses Beispiel veranschaulicht Folgendes: |
| -------- | --------------------------- |
| [Model conventions (Modellkonventionen)](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | Das Hinzufügen einer Routenvorlage und eines Headers zu den Seiten einer App |
| [Konventionen für Seitenroutenaktionen](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | Das Hinzufügen einer Routenvorlage zu Seiten in einem Ordner und zu einer Einzelseite |
| [Konventionen für Seitenmodellaktionen](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter (Filterklasse, Lambdaausdruck oder Filterzuordnungsinstanz)</li></ul> | Das Hinzufügen eines Headers zu Seiten in einem Ordner, das Hinzufügen eines Headers zu einer einzelnen Seite und das Konfigurieren einer [Filter-Factoy](xref:mvc/controllers/filters#ifilterfactory) zum Hinzufügen eines Headers zu den Seiten einer App. |

Konventionen für Razor Pages werden <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> mithilfe der Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> unter der Dienstklasse in der Klasse `Startup` hinzugefügt. Die folgenden Beispiele der Konvention werden später in diesem Thema erläutert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>Routenreihenfolge

Routen geben eine <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> für die Verarbeitung an (Routenabgleich).

| Auftrag            | Verhalten |
| :--------------: | -------- |
| -1               | Die Route wird vor anderen Routen verarbeitet. |
| 0                | Die Reihenfolge wurde nicht angegeben (Standardwert). Wenn Sie `Order` (`Order = null`) nicht zuweisen, wird für die Verarbeitung der Route `Order` standardmäßig auf 0 (null) gesetzt. |
| 1, 2, &hellip; n | Gibt die Verarbeitungsreihenfolge für die Route an. |

Die Routenverarbeitung wird gemäß der Konvention eingerichtet:

* Routen werden in sequenzieller Reihenfolge verarbeitet (-1, 0, 1, 2, &hellip; n).
* Wenn Routen dieselbe `Order` aufweisen, wird zuerst die spezifischste Route abgeglichen, danach folgen die weniger spezifischen Routen.
* Wenn Routen mit derselben `Order` und derselben Anzahl Parameter mit einer Anforderungs-URL übereinstimmen, werden Routen in der Reihenfolge verarbeitet, in der sie der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection> hinzugefügt wurden.

Vermeiden Sie nach Möglichkeit die Abhängigkeit von einer festgelegten Verarbeitungsreihenfolge für Routen. Im Allgemeinen wird beim Routing die richtige Route mittels URL-Zuordnung ausgewählt. Wenn Sie zum ordnungsgemäßen Weiterleiten von Anforderungen für die Route `Order`-Eigenschaften festlegen müssen, ist das Routingschema der App für Clients wahrscheinlich irreführend und in Bezug auf die Verwaltung störanfälliger. Versuchen Sie daher, das Routingschema der App zu vereinfachen. Bei der Beispiel-App ist eine explizite Verarbeitungsreihenfolge für Routen erforderlich, um mit einer einzigen App verschiedene Routingszenarios veranschaulichen zu können. In Produktions-Apps sollten Sie jedoch nach Möglichkeit eine Festlegung von `Order` für Routen vermeiden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Reihenfolge von Routen in den MVC-Themen finden Sie unter [Routing zu Controlleraktionen: Ordnen der Attributrouten](xref:mvc/controllers/routing#ordering-attribute-routes).

## <a name="model-conventions"></a>Modellkonventionen

Fügen Sie einen Delegaten für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> hinzu, um [Modellkonventionen](xref:mvc/controllers/application-model#conventions) hinzuzufügen, die auf Razor Pages anwendbar sind.

### <a name="add-a-route-model-convention-to-all-pages"></a>Hinzufügen einer Routenmodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenroutenmodellen eingesetzt werden.

Die Beispielanwendung fügt dann zu allen Seiten der App eine `{globalTemplate?}`-Routenvorlage hinzu:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `1` festgelegt. Dadurch wird das folgende Routenabgleichsverhalten in der Beispiel-App sichergestellt:

* Eine Routenvorlage für `TheContactPage/{text?}` wird später in diesem Thema hinzugefügt. Die Standardreihenfolge der Route für die Kontaktseite lautet `null` (`Order = 0`). Somit wird sie vor der Routenvorlage `{globalTemplate?}` abgeglichen.
* Eine Routenvorlage für `{aboutTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{aboutTemplate?}` erhält den `Order` von `2`. Wenn die Seite „Info“ unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.
* Eine Routenvorlage für `{otherPagesTemplate?}` wird später in diesem Thema hinzugefügt. Die Vorlage `{otherPagesTemplate?}` erhält den `Order` von `2`. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameter (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Optionen für Razor Pages, z. B. das Hinzufügen von <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>, werden hinzugefügt, wenn MVC der Dienstauflistung in `Startup.ConfigureServices` hinzugefügt wird. In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/) finden Sie ein Beispiel hierfür.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit einem Routensegment von GlobalRouteValue angefordert. Die gerenderte Seite zeigt, dass der Datenwert für die Route in der OnGet-Methode der Seite erfasst wird.](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>Hinzufügen einer App-Modellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenanwendungsmodellen eingesetzt werden.

Die Beispielanwendung enthält eine `AddHeaderAttribute`-Klasse, damit das Verwenden dieser und anderer Konventionen weiter unten in diesem Thema gezeigt werden kann. Der Klassenkonstruktor akzeptiert eine `name`-Zeichenfolge und ein `values`-Zeichenfolgenarray. Diese Werte werden in seiner `OnResultExecuting`-Methode verwendet, um einen Antwortheader einzurichten. Die Klasse wird im Abschnitt [Seitenmodellaktionskonventionen](#page-model-action-conventions) weiter unten in diesem Artikel erläutert.

Die Beispielanwendung verwendet die Klasse `AddHeaderAttribute`, um den Header `GlobalHeader` zu allen Seiten der Anwendung hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen an, dass „GlobalHeader“ hinzugefügt wurde.](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>Hinzufügen einer Handlermodellkonvention zu allen Seiten

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> zum Erstellen und Hinzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention> zur Auflistung der Instanzen von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>, die während der Erstellung von Seitenhandlermodellen eingesetzt werden.

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>Konventionen für Seitenroutenaktionen

Der Standardanbieter für Routenmodelle, der von <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> abgeleitet wird, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenrouten bereitzustellen.

### <a name="folder-route-model-convention"></a>Ordnerroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für alle Seiten im angegebenen Ordner aufruft.

Die Beispielanwendung verwendet <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>, um eine `{otherPagesTemplate?}`-Routenvorlage zu den Seiten im Ordner *OtherPages* hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn eine Seite im Ordner *Pages/OtherPages* mit einem Routenparameterwert (z. B. `/OtherPages/Page1/RouteDataValue`) angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["otherPagesTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` an, und prüfen Sie das Ergebnis:

![Seite1 aus dem Ordner „OtherPages“ wird mit einem Routensegment von GlobalRouteValue und OtherPagesRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>Seitenroutenmodellkonvention

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> für die Seite mit dem angegebenen Namen aufruft.

Die Beispielanwendung verwendet `AddPageRouteModelConvention`, um eine `{aboutTemplate?}`-Routenvorlage zu der Seite „Info“ hinzuzufügen:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

Die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> ist für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> auf `2` festgelegt. Dadurch wird sichergestellt, dass die Vorlage für `{globalTemplate?}`, die weiter oben in diesem Thema für `1` festgelegt wurde, in Bezug auf die erste Position für einen Routendatenwert vorgezogen wird, wenn nur ein einziger Routenwert angegeben wurde. Wenn die Seite „Info“ mit einem Routenparameterwert unter `/About/RouteDataValue` angefordert wird, wird „RouteDataValue“ in die Vorlage `RouteData.Values["globalTemplate"]` geladen (`Order = 1`) und nicht in `RouteData.Values["aboutTemplate"]` (`Order = 2`), da die Eigenschaft `Order` auf null festgelegt wurde.

Legen Sie die `Order` möglichst nicht fest, sodass `Order = 0` gilt. Verlassen Sie sich bei der Auswahl der richtigen Route auf die Routenplanung.

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About/GlobalRouteValue/AboutRouteValue` an, und prüfen Sie das Ergebnis:

![Die Seite „Info“ wird mit Routensegmenten für GlobalRouteValue und AboutRouteValue angefordert. In der gerenderten Seite wird gezeigt, dass die Datenwerte für die Route in der OnGet-Methode der Seite erfasst werden.](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>Konfigurieren einer Seitenroute

Konfigurieren Sie mithilfe von <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> eine Route zu einer Seite am angegebenen Seitenpfad. Generierte Links, die auf die Seite verweisen, verwenden die von Ihnen angegebene Route. `AddPageRoute` verwendet die `AddPageRouteModelConvention` zum Aufbauen der Route.

Die Beispielanwendung erstellt für *Contact.cshtml* eine Route zur Seite `/TheContactPage`:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

Die Seite „Kontakt“ kann auch unter `/Contact` über ihre Standardroute erreicht werden.

Die benutzerdefinierte Route der Beispielanwendung, die zur Seite „Kontakt“ führt, ermöglicht das Verwenden eines optionalen `text`-Routensegments (`{text?}`). Die Seite enthält dieses optionale Segment auch in ihrer `@page`-Anweisung, für den Fall, dass der Besucher über die `/Contact`-Route auf die Seite zugreift:

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

Beachten Sie, dass die URL, die für den Link **Kontakt** in der gerenderten Seite generiert wurde, die aktualisierte Route widerspiegelt:

![Kontaktlink der Beispielanwendung in der Navigationsleiste](razor-pages-conventions/_static/contact-link.png)

![Beim Überprüfen des Kontaktlinks in der gerenderten HTML zeigt sich, dass das HREF-Attribut auf „/TheContactPage“ festgelegt wurde.](razor-pages-conventions/_static/contact-link-source.png)

Sie können die Kontaktseite entweder über deren übliche Route, `/Contact`, oder über die benutzerdefinierte Route, `/TheContactPage`, besuchen. Wenn Sie ein zusätzliches `text`-Routensegment bereitstellen, wird dieses HTML-codierte Segment auf der Seite angezeigt:

![Beispiel für das Bereitstellen eines optionalen „Text“-Routensegments mit „TextValue“ in der URL in der Ansicht des Microsoft Edge-Browsers Die gerenderte Seite zeigt den Segmentwert „Text“ an.](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>Konventionen für Seitenmodellaktionen

Der Standardanbieter für Seitenmodelle, der <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> implementiert, ruft Konventionen auf, die entwickelt wurden, um Erweiterungspunkte zum Konfigurieren von Seitenmodellen bereitzustellen. Diese Konventionen sind beim Erstellen und Ändern von Seitenermittlungs- und Verarbeitungsszenarios hilfreich.

In den Beispielen in diesem Abschnitt verwendet die Beispielanwendung eine `AddHeaderAttribute`-Klasse, also ein <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>, das einen Antwortheader verwendet:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

Das Beispiel veranschaulicht mithilfe von Konventionen, wie das Attribut auf alle Seiten in einem Ordner und auf eine Einzelseite angewendet werden kann.

**Ordner-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>-Instanzen für alle Seiten im angegebenen Ordner aufruft.

Im Beispiel wird die Verwendung der `AddFolderApplicationModelConvention` durch Hinzufügen eines Headers, `OtherPagesHeader`, zu den Seiten im Ordner *OtherPages* der Anwendung veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

Fordern Sie die Seite „Seite1“ der Beispielanwendung unter `localhost:5000/OtherPages/Page1` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „OtherPages/Seite1“ zeigt, dass der OtherPagesHeader hinzugefügt wurde.](razor-pages-conventions/_static/page1-otherpages-header.png)

**Seiten-App-Modellkonvention**

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> zum Erstellen und Hinzuzufügen einer <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>, die eine Aktion für das <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> für die Seite mit dem angegebenen Namen aufruft.

Im Beispiel wird das Verwenden von `AddPageApplicationModelConvention` durch Hinzufügen eines Headers, `AboutHeader`, auf der Seite „Info“ veranschaulicht:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Antwortheader der Seite „Info“ zeigen, dass AboutHeader hinzugefügt wurde.](razor-pages-conventions/_static/about-page-about-header.png)

**Konfigurieren eines Filters**

Der angegebene Filter, der angewendet werden soll, wird mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> konfiguriert. Sie können Filterklassen selbst implementieren. In der Beispielanwendung wird jedoch gezeigt, wie Sie einen Filter in einen Lambdaausdruck implementieren. Dieser wird dann im Hintergrund als Zuordnungsinstanz implementiert, die einen Filter zurückgibt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

Das Seiten-App-Modell wird verwendet, um den relativen Pfad für Segmente zu überprüfen, die zur Seite „Seite2“ im Ordner *OtherPages* führen. Wenn die Bedingung erfüllt ist, wird ein Header hinzugefügt. Wenn dies nicht der Fall ist, wird der `EmptyFilter` angewendet.

`EmptyFilter` ist ein [Aktionsfilter](xref:mvc/controllers/filters#action-filters). Wenn der Pfad `OtherPages/Page2` nicht enthält, hat der `EmptyFilter`, wie vorgesehen, keine Auswirkung, da Aktionsfilter von Razor Pages ignoriert werden.

Fordern Sie die Seite „Seite2“ der Beispielanwendung unter `localhost:5000/OtherPages/Page2` an, und prüfen Sie die Header, um das Ergebnis zu sehen:

![Der Header „OtherPagesPage2Header“ wird zur Antwort für „Seite2“ hinzugefügt.](razor-pages-conventions/_static/page2-filter-header.png)

**Konfigurieren einer Filterzuordnungsinstanz**

Mit <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> wird die angegebene Zuordnungsinstanz so konfiguriert, dass sie [Filter](xref:mvc/controllers/filters) auf alle Razor Pages anwendet.

Die Beispielanwendung bietet Ihnen die Möglichkeit des beispielhaften Verwendens einer [Filterzuordnungsinstanz](xref:mvc/controllers/filters#ifilterfactory) durch Hinzufügen des Headers `FilterFactoryHeader` zu den Seiten der Anwendung, der zwei Werte besitzt:

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs* :

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

Fordern Sie die Seite „Info“ der Beispielanwendung unter `localhost:5000/About` an, und überprüfen Sie die Header, um das Ergebnis zu sehen:

![Die Antwortheader der Seite „Info“ zeigen, dass zwei Header des Typs „FilterFactoryHeader“ hinzugefügt wurden.](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>Die MVC-Filter und der Seitenfilter (IPageFilter)

MVC-[Aktionsfilter](xref:mvc/controllers/filters#action-filters) werden von Razor Pages ignoriert, da Razor Pages Handlermethoden verwenden. Darüber hinaus stehen die folgenden MVC-Filtertypen zur Verfügung: [Autorisierung](xref:mvc/controllers/filters#authorization-filters), [Ausnahme](xref:mvc/controllers/filters#exception-filters), [Ressource](xref:mvc/controllers/filters#resource-filters) und [Ergebnis](xref:mvc/controllers/filters#result-filters). Weitere Informationen finden Sie im Thema [Filter](xref:mvc/controllers/filters).

Der Seitenfilter (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) ist ein Filter, der auf Razor Pages anwendbar ist. Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
