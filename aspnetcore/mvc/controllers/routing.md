---
title: Routing zu Controlleraktionen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie ASP.NET Core MVC Routingmiddleware verwendet, um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen.
ms.author: riande
ms.date: 3/25/2020
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
uid: mvc/controllers/routing
ms.openlocfilehash: a163c87fdb9a02c1b074ab32c19c11932c66cfd4
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854534"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>Routing zu Controlleraktionen in ASP.NET Core

Von [Ryan Nowak](https://github.com/rynowak), [Kirk Larkin](https://twitter.com/serpent5) und [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core Controller verwenden die Routing [Middleware](xref:fundamentals/middleware/index) , um die URLs eingehender Anforderungen abzugleichen und Sie [Aktionen](#action)zuzuordnen.  Routen Vorlagen:

* Werden in Startcode oder Attributen definiert.
* Beschreiben Sie, wie URL-Pfade mit [Aktionen](#action)abgeglichen werden.
* Werden verwendet, um URLs für Verknüpfungen zu generieren. Die generierten Verknüpfungen werden in der Regel in Antworten zurückgegeben.

Aktionen werden entweder [konventionell](#cr) oder [Attribut](#ar)weitergeleitet. Durch das Platzieren einer Route auf dem Controller oder der [Aktion](#action) wird das Attribut geroutet. Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).

Dieses Dokument:

* Erläutert die Interaktionen zwischen MVC und Routing:
  * Verwendung von Routing Features durch typische MVC-apps
  * Umfasst beides:
    * [Konventionell Routing](#cr) , das in der Regel für Controller und Ansichten verwendet wird.
    * *Attribut Routing* , das mit Rest-APIs verwendet wird. Wenn Sie hauptsächlich an der Weiterleitung von Rest-APIs interessiert sind, springen Sie zum Abschnitt [Attribut Routing für Rest-APIs](#ar) .
  * Weitere Informationen finden Sie unter [Routing](xref:fundamentals/routing) für erweiterte Routing Details.
* Bezieht sich auf das Standard Routing System, das in ASP.net Core 3,0 hinzugefügt wurde, dem sogenannten Endpunkt Routing. Es ist möglich, Controller mit der vorherigen Version des Routings zu Kompatibilitätszwecken zu verwenden. Anweisungen hierzu finden Sie im [2.2-3.0-Migrations Handbuch](xref:migration/22-to-30) . Das Referenzmaterial zum Legacy Routing System finden Sie in der [Version 2,2 dieses Dokuments](xref:mvc/controllers/routing?view=aspnetcore-2.2) .

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>Einrichten der herkömmlichen Route

`Startup.Configure` in der Regel ist Code ähnlich dem folgenden, wenn [herkömmliches Routing](#crd)verwendet wird:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

Innerhalb des <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> Aufrufes wird zum Erstellen einer einzelnen Route verwendet. Die einzelne Route heißt `default` Route. Die meisten apps mit Controllern und Ansichten verwenden eine Routen Vorlage ähnlich der `default` Route. Rest-APIs sollten [Attribut Routing](#ar)verwenden.

Die Routen Vorlage `"{controller=Home}/{action=Index}/{id?}"` :

* Entspricht einem URL-Pfad wie `/Products/Details/5`
* Extrahiert die Routen Werte, `{ controller = Products, action = Details, id = 5 }` indem der Pfad mit einem Token versehen wird. Die Extraktion von Routen Werten führt zu einer Abstimmung, wenn die APP einen Controller mit dem Namen `ProductsController` und eine `Details` Aktion aufweist:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5` Modell bindet den Wert von `id = 5` , um den- `id` Parameter auf festzulegen `5` . Weitere Informationen finden Sie unter [Modell Bindung](xref:mvc/models/model-binding) .
* `{controller=Home}` definiert `Home` als Standard `controller` .
* `{action=Index}` definiert `Index` als Standard `action` .
*  Das `?` Zeichen in `{id?}` definiert `id` als optional.
  * Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt. Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).
* Entspricht dem URL-Pfad `/` .
* Erzeugt die Routen Werte `{ controller = Home, action = Index }` .

Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keinen Wert, da kein entsprechendes Segment im URL-Pfad vorhanden ist. `/` stimmt nur überein, wenn eine `HomeController` and-Aktion vorhanden ist `Index` :

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mithilfe der vorangehenden Controller Definition und Routen Vorlage `HomeController.Index` wird die Aktion für die folgenden URL-Pfade ausgeführt:

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

Der URL `/` -Pfad verwendet die Routen Vorlagen `Home` -Standard Controller und- `Index` Aktion. Der URL-Pfad `/Home` verwendet die Standardaktion der Routen Vorlage `Index` .

Mit der Hilfsmethode <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A>:

```csharp
endpoints.MapDefaultControllerRoute();
```

Arbeits

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

> [!IMPORTANT]
> Das Routing wird mithilfe der <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> Middleware und konfiguriert. So verwenden Sie Controller:
>
> * Ruft <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> in `UseEndpoints` auf, um [Attribut](#ar) Routing Controller zuzuordnen.
> * <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A>Wird oder aufgerufen <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> , um sowohl [konventionell geroutete](#cr) Controller als auch Attribut Routing Controller [zuzuordnen](#ar) .

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>Herkömmliches Routing

Herkömmliches Routing wird mit Controllern und Ansichten verwendet. Die `default`-Route:

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

ist ein Beispiel für *herkömmliches Routing*. Es wird als *herkömmliches Routing* bezeichnet, da es eine *Konvention* für URL-Pfade festlegt:

* Das erste Pfad Segment, `{controller=Home}` , wird dem Controller Namen zugeordnet.
* Das zweite Segment, `{action=Index}` , wird dem [Aktions](#action) Namen zugeordnet.
* Das dritte Segment `{id?}` wird für eine optionale verwendet `id` . Die `?` in `{id?}` macht Sie optional. `id` wird verwendet, um einer Modell Entität zuzuordnen.

Verwenden Sie diese `default` Route, den URL-Pfad:

* `/Products/List` wird der- `ProductsController.List` Aktion zugeordnet.
* `/Blog/Article/17` wird zugeordnet, und in der `BlogController.Article` Regel bindet Model den- `id` Parameter an 17.

Diese Zuordnung:

* Basiert **nur** auf den Controller-und [Aktions](#action) Namen.
* Basiert nicht auf Namespaces, Quelldatei Speicherorten oder Methoden Parametern.

Die Verwendung von herkömmlichem Routing mit der Standardroute ermöglicht das Erstellen der APP, ohne für jede Aktion ein neues URL-Muster zu erstellen. Für eine APP mit [CRUD-](https://wikipedia.org/wiki/Create,_read,_update_and_delete) Stil Aktionen mit Konsistenz für die URLs über Controller hinweg:

* Erleichtert das Vereinfachen des Codes.
* Macht die Benutzeroberfläche berechenbarer.

> [!WARNING]
> Der `id` im vorangehenden Code wird von der Routen Vorlage als optional definiert. Aktionen können ausgeführt werden, ohne dass die optionale ID als Teil der URL bereitgestellt wird. Im Allgemeinen gilt, wenn in `id` der URL weggelassen wird:
>
> * `id` wird `0` von der Modell Bindung auf festgelegt.
> * Es wurde keine Entität in der Daten Bank Übereinstimmung gefunden `id == 0` .
>
> Das [Attribut Routing](#ar) ermöglicht eine differenzierte Steuerung, um die erforderliche ID für einige Aktionen und nicht für andere zu erstellen. Gemäß der Konvention enthält die Dokumentation optionale Parameter, z `id` . b., wenn Sie wahrscheinlich in der richtigen Verwendung angezeigt werden.

Für die meisten Apps sollte eine grundlegendes und beschreibendes Routingschema ausgewählt werden, um lesbare und aussagekräftige URLs zu erhalten. Für die konventionelle Standardroute `{controller=Home}/{action=Index}/{id?}` gilt:

* Sie unterstützt ein grundlegendes und beschreibendes Routingschema.
* Sie stellt einen nützlichen Startpunkt für benutzeroberflächenbasierte Apps dar.
* Ist die einzige Routen Vorlage, die für viele Web-UI-apps benötigt wird. Bei größeren Webanwendungs-Apps ist eine andere Route, die [Bereiche](#areas) verwendet, häufig alle erforderlich.

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> und <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute%2A> :

* Weisen Sie Ihren Endpunkten automatisch einen **Bestell** Wert basierend auf der Reihenfolge zu, in der Sie aufgerufen werden.

Endpunktrouting in ASP.NET Core 3.0 und höher:

* Weist kein Konzept für Routen auf.
* Bietet keine Bestell Garantien für die Ausführung der Erweiterbarkeit. alle Endpunkte werden gleichzeitig verarbeitet.

Wenn Sie die [Protokollierung](xref:fundamentals/logging/index) aktivieren, erfahren Sie, wie die integrierten Routingimplementierungen (z.B. <xref:Microsoft.AspNetCore.Routing.Route>) Zuordnungen für Anforderungen ermitteln.

Das [Attribut Routing](#ar) wird weiter unten in diesem Dokument erläutert.

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>Mehrere herkömmliche Routen

Mehrere [konventionelle Routen](#cr) können in hinzugefügt werden `UseEndpoints` , indem weitere Aufrufe von und hinzugefügt werden <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> . Dies ermöglicht das Definieren mehrerer Konventionen oder das Hinzufügen herkömmlicher Routen, die einer bestimmten [Aktion](#action)zugeordnet sind, wie z. b.:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

Die `blog` Route im vorangehenden Code ist eine **dedizierte konventionelle Route**. Dies wird als dedizierte konventionelle Route bezeichnet:

* Es verwendet [herkömmliches Routing](#cr).
* Es ist für eine bestimmte [Aktion](#action)vorgesehen.

Da `controller` und `action` nicht in der Routen Vorlage `"blog/{*article}"` als Parameter angezeigt werden:

* Sie können nur die Standardwerte aufweisen `{ controller = "Blog", action = "Article" }` .
* Diese Route wird immer der Aktion zugeordnet `BlogController.Article` .

`/Blog`, `/Blog/Article` und `/Blog/{any-string}` sind die einzigen URL-Pfade, die der Blog Route entsprechen.

Für das vorherige Beispiel gilt Folgendes:

* `blog` Route hat eine höhere Priorität als die Route, `default` da Sie zuerst hinzugefügt wird.
* Ist ein Beispiel für das Routing im [Slug](https://developer.mozilla.org/docs/Glossary/Slug) -Stil, bei dem es typisch ist, einen Artikelnamen als Teil der URL zu haben.

> [!WARNING]
> In ASP.net Core 3,0 und höher ist das Routing nicht:
> * Definieren Sie ein Konzept, das als *Route* bezeichnet wird. `UseRouting` fügt der Middlewarepipeline einen Routenabgleich hinzu. Die `UseRouting` Middleware prüft den Satz von Endpunkten, die in der APP definiert sind, und wählt basierend auf der Anforderung die beste Endpunkt Übereinstimmung aus.
> * Stellen Sie Garantien für die Ausführungsreihenfolge der Erweiterbarkeit wie <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> oder bereit <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> .
>
>Referenzmaterial zum Routing finden Sie unter [Routing](xref:fundamentals/routing) .

<a name="cro"></a>

### <a name="conventional-routing-order"></a>Konventionelle Routing Reihenfolge

Herkömmliches Routing stimmt nur mit einer Kombination aus Aktion und Controller überein, die von der APP definiert werden. Dies dient zur Vereinfachung der Fälle, in denen herkömmliche Routen sich überlappen.
Durch das Hinzufügen von Routen mithilfe von <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute%2A> , <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute%2A> und wird <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> ihren Endpunkten automatisch ein Bestellwert entsprechend der Reihenfolge zugewiesen, in der Sie aufgerufen werden. Übereinstimmungen aus einer zuvor angezeigten Route haben eine höhere Priorität. Beim herkömmlichen Routing ist die Reihenfolge wichtig. Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind. [Dedizierte herkömmliche Routen](#dcr) mit "Catch-All"-Routen Parametern wie `{*article}` können eine Route zu [gierig](xref:fundamentals/routing#greedy)machen, was bedeutet, dass Sie mit den URLs übereinstimmt, die Sie mit anderen Routen vergleichen wollten. Platzieren Sie die gierigen Routen später in der Routing Tabelle, um gierige Übereinstimmungen zu vermeiden.

[!INCLUDE[](~/includes/catchall.md)]

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>Auflösen mehrdeutiger Aktionen

Wenn zwei Endpunkte über das Routing abgleichen, muss das Routing einen der folgenden Schritte ausführen:

* Wählen Sie den besten Kandidaten aus.
* Löst eine Ausnahme aus.

Beispiel:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

Der vorangehende Controller definiert zwei Aktionen, die mit identisch sind:

* Der URL-Pfad. `/Products33/Edit/17`
* Weiterleiten von Daten `{ controller = Products33, action = Edit, id = 17 }` .

Dies ist ein typisches Muster für MVC-Controller:

* `Edit(int)` zeigt ein Formular zum Bearbeiten eines Produkts an.
* `Edit(int, Product)` verarbeitet das gepostete Formular.

So beheben Sie die korrekte Route:

* `Edit(int, Product)` wird ausgewählt, wenn die Anforderung ein HTTP ist `POST` .
* `Edit(int)` wird ausgewählt, wenn das [http-Verb](#verb) etwas anderes ist. `Edit(int)` wird im Allgemeinen über aufgerufen `GET` .

Die <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , `[HttpPost]` , wird für das Routing bereitgestellt, sodass Sie basierend auf der HTTP-Methode der Anforderung ausgewählt werden kann. Der `HttpPostAttribute` ist `Edit(int, Product)` besser geeignet als `Edit(int)` .

Es ist wichtig, die Rolle von Attributen wie zu verstehen `HttpPostAttribute` . Ähnliche Attribute werden für andere [HTTP-Verben](#verb)definiert. Bei [herkömmlichem Routing](#cr)wird bei Aktionen häufig derselbe Aktionsname verwendet, wenn Sie Teil eines anshow Formulars sind, Formular Workflow senden. Informationen hierzu finden Sie beispielsweise [unter Überprüfen der beiden Bearbeitungs Aktionsmethoden](xref:tutorials/first-mvc-app/controller-methods-views#get-post).

Wenn das Routing keinen besten Kandidaten auswählen kann, <xref:System.Reflection.AmbiguousMatchException> wird eine ausgelöst, die mehrere übereinstimmende Endpunkte auflistet.

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>Herkömmliche Routennamen

Die Zeichen  `"blog"` `"default"` folgen und in den folgenden Beispielen sind konventionelle Routennamen:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Mit den Routennamen wird der Route ein logischer Name zugewiesen. Die benannte Route kann für die URL-Generierung verwendet werden. Die Verwendung einer benannten Route vereinfacht die URL-Erstellung, wenn die Reihenfolge der Routen die URL-Generierung erschweren könnte. Routennamen müssen eine eindeutige Anwendungs weite sein.

Routennamen:

* Hat keine Auswirkung auf die URL-Übereinstimmung oder-Verarbeitung von Anforderungen.
* Werden nur für die URL-Generierung verwendet.

Das Routing Namenskonzept wird als " [iendpointnamemetadata](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)" dargestellt. Die Begriffe **Routen Name** und **Endpunkt Name**:

* Sind austauschbar.
* Welche Informationen in der Dokumentation und im Code verwendet werden, hängt von der API ab, die beschrieben wird.

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>Attribut Routing für Rest-APIs

Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch [HTTP-Verben](#verb)dargestellt werden.

Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet. Der folgende `StartUp.Configure` Code ist für eine Rest-API typisch und wird im nächsten Beispiel verwendet:

[!code-csharp[](routing/samples/3.x/main/StartupAPI.cs?name=snippet)]

Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers%2A> wird innerhalb von aufgerufen, `UseEndpoints` um Attribut Routing Controller zuzuordnen.

Im folgenden Beispiel:

* Die vorangehende `Configure` Methode wird verwendet.
* `HomeController` entspricht einem Satz von URLs, die mit der herkömmlichen Standardroute `{controller=Home}/{action=Index}/{id?}` übereinstimmen.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

Die `HomeController.Index` Aktion wird für alle URL-Pfade `/` , `/Home` , oder ausgeführt `/Home/Index` `/Home/Index/3` .

In diesem Beispiel wird ein wichtiger Programmier Unterschied zwischen Attribut Routing und [herkömmlichem Routing](#cr)hervorgehoben. Das Attribut Routing erfordert mehr Eingaben, um eine Route anzugeben. Bei der herkömmlichen Standardroute werden Routen genauer behandelt. Das Attribut Routing ermöglicht und erfordert jedoch eine genaue Kontrolle darüber, welche Routen Vorlagen auf die einzelnen [Aktionen](#action)angewendet werden.

Beim Attribut Routing spielen die Controller-und Aktions Namen keinen Teil, in dem die Aktion abgeglichen wird, es sei denn, es wird eine [Tokenersetzung](#routing-token-replacement-templates-ref-label) verwendet. Das folgende Beispiel entspricht den gleichen URLs wie im vorherigen Beispiel:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Der folgende Code verwendet die Tokenersetzung für `action` und `controller` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

Der folgende Code gilt `[Route("[controller]/[action]")]` für den Controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

Im vorangehenden Code müssen die `Index` Methoden Vorlagen `/` oder `~/` den Routen Vorlagen vorangestellt werden. Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden.

Weitere Informationen zur Routen Vorlagen Auswahl finden Sie unter [Routen Vorlagen Rangfolge](xref:fundamentals/routing#rtp) .

## <a name="reserved-routing-names"></a>Reservierte Routingnamen

Die folgenden Schlüsselwörter sind reservierte Routen Parameternamen bei der Verwendung von Controllern oder Razor Seiten:

* `action`
* `area`
* `controller`
* `handler`
* `page`

Die Verwendung `page` von als Routen Parameter mit Attribut Routing ist ein häufiger Fehler. Dies führt zu inkonsistentem und verwirdem Verhalten bei der URL-Generierung.

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

Die speziellen Parameternamen werden von der URL-Generierung verwendet, um zu bestimmen, ob ein URL-Generierungs Vorgang auf eine Razor Seite oder auf einen Controller verweist.

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP-Verb Vorlagen

ASP.net Core verfügt über die folgenden HTTP-Verb Vorlagen:

* [HttpGet](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [HttpPost](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [HttpPut](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [HttpDelete](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[Httphead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[Httppatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>Routenvorlagen

ASP.net Core verfügt über die folgenden Routen Vorlagen:

* Alle [http-Verb Vorlagen](#verb) sind Routen Vorlagen.
* [Seeweg](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>Attribut Routing mit HTTP-Verb Attributen

Beachten Sie den folgenden Controller:

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

Im obigen Code:

* Jede Aktion enthält das- `[HttpGet]` Attribut, das die Übereinstimmung von HTTP-GET-Anforderungen einschränkt.
* Die `GetProduct` Aktion enthält die `"{id}"` Vorlage und `id` wird daher an die `"api/[controller]"` Vorlage auf dem Controller angehängt. Die Methoden Vorlage ist `"api/[controller]/"{id}""` . Daher entspricht diese Aktion nur Get-Anforderungen für das Formular `/api/test2/xyz` , `/api/test2/123` , `/api/test2/{any string}` usw.
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* Die `GetIntProduct` Aktion enthält die `"int/{id:int}")` Vorlage. Der `:int` Teil der Vorlage schränkt die `id` Routen Werte auf Zeichen folgen ein, die in eine ganze Zahl konvertiert werden können. Eine GET-Anforderung für `/api/test2/int/abc` :
  * Entspricht dieser Aktion nicht.
  * Gibt den Fehler " [404 nicht gefunden](https://developer.mozilla.org/docs/Web/HTTP/Status/404) " zurück.
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* Die `GetInt2Product` Aktion enthält `{id}` in der Vorlage, schränkt jedoch nicht `id` auf Werte ein, die in eine ganze Zahl konvertiert werden können. Eine GET-Anforderung für `/api/test2/int2/abc` :
  * Entspricht dieser Route.
  * Die Modell Bindung kann nicht `abc` in eine ganze Zahl konvertiert werden. Der- `id` Parameter der-Methode ist Integer.
  * Gibt eine ungültige [400-Anforderung](https://developer.mozilla.org/docs/Web/HTTP/Status/400) zurück, da die Modell Bindung nicht in eine ganze Zahl konvertiert werden konnte `abc` .
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

Beim Attribut Routing können <xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute> Attribute wie <xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> , <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute> und verwendet werden <xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute> . Alle HTTP- [Verb](#verb) Attribute akzeptieren eine Routen Vorlage. Das folgende Beispiel zeigt zwei Aktionen, die derselben Routen Vorlage entsprechen:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

Verwenden des URL-Pfads `/products3` :

* Die `MyProductsController.ListProducts` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `GET` .
* Die `MyProductsController.CreateProduct` Aktion wird ausgeführt, wenn das [http-Verb](#verb) den Wert hat `POST` .

Beim Aufbau einer Rest-API müssen Sie `[Route(...)]` nur selten eine Aktionsmethode verwenden, da die Aktion alle HTTP-Methoden akzeptiert. Es ist besser, das spezifischere [http-Verb-Attribut](#verb) zu verwenden, um genau zu erfahren, was Ihre API unterstützt. REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.

Rest-APIs sollten das Attribut Routing verwenden, um die Funktionalität der App als Satz von Ressourcen zu modellieren, in denen Vorgänge durch HTTP-Verben dargestellt werden. Dies bedeutet, dass viele Vorgänge, z. b. Get und Post, für dieselbe logische Ressource dieselbe URL verwenden. Das Attributrouting bietet eine Ebene der Steuerung, die für einen sorgfältigen Entwurf des öffentlichen Endpunktlayouts einer API erforderlich ist.

Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen. Im folgenden Beispiel `id` ist als Teil des URL-Pfads erforderlich:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Die `Products2ApiController.GetProduct(int)` Aktion:

* Wird mit URL-Pfad wie `/products2/3`
* Wird nicht mit dem URL-Pfad ausgeführt `/products2` .

Das [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)-Attribut ermöglicht es einer Aktion, die unterstützten Anforderungsinhaltstypen einzuschränken. Weitere Informationen finden Sie [unter Definieren unterstützter Anforderungs Inhaltstypen mit dem Attribut "Verbrauchs](xref:web-api/index#consumes)".

 Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Weitere Informationen zu `[ApiController]` finden Sie unter [apicontroller-Attribut](xref:web-api/index##apicontroller-attribute).

## <a name="route-name"></a>Routenname

Im folgenden Code wird ein Routenname von `Products_List` definiert:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren. Routennamen:

* Hat keine Auswirkung auf das URL-Vergleichs Verhalten des Routings.
* Werden nur für die URL-Generierung verwendet.

Routennamen müssen anwendungsweit eindeutig sein.

Vergleichen Sie den vorangehenden Code mit der herkömmlichen Standardroute, die den `id` Parameter als optional definiert ( `{id?}` ). Die Möglichkeit, APIs genau anzugeben, bietet Vorteile, z. b. das zulassen `/products` und verteilen `/products/5` an verschiedene Aktionen.

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>Kombinieren von Attribut Routen

Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert. Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt. Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

Im vorherigen Beispiel:

* Der URL-Pfad `/products` kann Stimmen. `ProductsApi.ListProducts`
* Der URL-Pfad kann abgeglichen werden `/products/5` `ProductsApi.GetProduct(int)` .

Beide Aktionen entsprechen nur http, `GET` da Sie mit dem-Attribut markiert sind `[HttpGet]` .

Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden. Das folgende Beispiel entspricht einem Satz von URL-Pfaden, die der Standardroute ähneln.

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

In der folgenden Tabelle werden die `[Route]` Attribute im vorangehenden Code erläutert:

| attribute               | Kombiniert mit `[Route("Home")]` | Definiert die Routen Vorlage. |
| ----------------- | ------------ | --------- |
| `[Route("")]` | Ja | `"Home"` |
| `[Route("Index")]` | Ja | `"Home/Index"` |
| `[Route("/")]` | **Nein** | `""` |
| `[Route("About")]` | Ja | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>Attribut Routen Reihenfolge

Routing erstellt eine Struktur und gleicht alle Endpunkte gleichzeitig ab:

* Die Routen Einträge Verhalten sich so, als ob Sie in einer idealen Reihenfolge platziert werden.
* Die spezifischsten Routen können vor den allgemeineren Routen ausgeführt werden.

Beispielsweise ist eine Attribut Route wie `blog/search/{topic}` spezifischer als eine Attribut Route wie `blog/{*article}` . Die `blog/search/{topic}` Route hat standardmäßig eine höhere Priorität, da Sie spezifischer ist. Mit [herkömmlichem Routing](#cr)ist der Entwickler für das Platzieren von Routen in der gewünschten Reihenfolge verantwortlich.

Attribut Routen können mithilfe der-Eigenschaft eine Bestellung konfigurieren <xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order> . Alle vom Framework bereitgestellten [Routen Attribute](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) umfassen `Order` . Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet. Die Standardreihenfolge ist `0`. Das Festlegen einer Route mithilfe von wird `Order = -1` vor Routen ausgeführt, die keine Reihenfolge festlegen. Das Festlegen einer Route mithilfe von wird `Order = 1` nach der Standardrouten Reihenfolge ausgeführt.

**Vermeiden** Sie die Abhängigkeit von `Order` . Wenn für den URL-Speicher einer APP explizite Auftrags Werte erforderlich sind, um ordnungsgemäß weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend. Im Allgemeinen wählt das Attribut Routing die richtige Route mit URL-Übereinstimmung aus. Wenn die für die URL-Generierung verwendete Standard Reihenfolge nicht funktioniert, ist die Verwendung eines Routen namens als außer Kraft Setzung in der Regel einfacher als das Anwenden der `Order` Eigenschaft.

Beachten Sie die beiden folgenden Controller, die beide den Routen Abgleich definieren `/home` :

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

Wenn `/home` Sie mit dem vorangehenden Code anfordern, wird eine Ausnahme ausgelöst, die der folgenden ähnelt:

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

Durch das Hinzufügen `Order` eines der Routen Attribute wird die Mehrdeutigkeit aufgelöst:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

Mit dem vorangehenden Code `/home` führt den `HomeController.Index` Endpunkt aus. Um die `MyDemoController.MyIndex` anzufordern, fordern Sie an `/home/MyIndex` . **Hinweis**:

* Der vorangehende Code ist ein Beispiel oder ein schlechtes Routing Design. Es wurde verwendet, um die-Eigenschaft zu veranschaulichen `Order` .
* Die- `Order` Eigenschaft löst nur die Mehrdeutigkeit auf, diese Vorlage kann nicht abgeglichen werden. Es wäre besser, die Vorlage zu entfernen `[Route("Home")]` .

Siehe [ Razor Seiten Routen und App-Konventionen: Routen Reihenfolge](xref:razor-pages/razor-pages-conventions#route-order) für Informationen zur Routen Reihenfolge mit Razor Seiten.

In einigen Fällen wird ein HTTP 500-Fehler mit mehrdeutigen Routen zurückgegeben. Verwenden Sie die [Protokollierung](xref:fundamentals/logging/index) , um anzuzeigen, welche Endpunkte `AmbiguousMatchException` den verursacht haben

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Ersetzung von Token in Routen Vorlagen [Controller], [Aktion], [Bereich]

Zur einfacheren Unterstützung unterstützen Attribut Routen die *Tokenersetzung* durch Einschließen eines Tokens in eckige Klammern ( `[` , `]` ). Die Token `[action]` , `[area]` und `[controller]` werden durch die Werte für Aktionsname, Bereichs Name und Controller Name aus der Aktion ersetzt, in der die Route definiert ist:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

Im obigen Code:

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * Match `/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * Match `/Products0/Edit/{id}`

Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf. Das vorherige Beispiel verhält sich wie der folgende Code:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

Attributrouten können auch mit Vererbung kombiniert werden. Dies ist in Kombination mit der Tokenersetzung stark. Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`generiert für jede Aktion einen eindeutigen Routennamen:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden.
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 generiert für jede Aktion einen eindeutigen Routennamen.

Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung

Die Tokenersetzung kann mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer` parametertransformator den `SubscriptionManagement` Routen Wert in `subscription-management` :

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

Die <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> ist eine Anwendungsmodellkonvention, die Folgendes ausführt:

* Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.
* Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

Die vorangehende `ListAll` Methode stimmt überein `/subscription-management/list-all` .

Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

Die Definition von Slug finden Sie unter [MDN-Webdokumentation zu Slug](https://developer.mozilla.org/docs/Glossary/Slug) .

[!INCLUDE[](~/includes/regex.md)]
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>Mehrere Attribut Routen

Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen. Dies wird am häufigsten beim Imitieren des Verhaltens der herkömmlichen Standardroute verwendet. Im folgenden Beispiel wird dies gezeigt:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

Wenn Sie mehrere Routen Attribute auf dem Controller platzieren, bedeutet dies, dass jede mit den Routen Attributen der Aktionsmethoden kombiniert wird:

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

Alle [http-Verb](#verb) Routen Einschränkungen implementieren `IActionConstraint` .

Wenn mehrere Routen Attribute, die implementieren, <xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint> in eine Aktion eingefügt werden:

* Jede Aktions Einschränkung kombiniert mit der auf den Controller angewendeten Routen Vorlage.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

Die Verwendung mehrerer Routen für Aktionen mag hilfreich und leistungsstark sein. es ist besser, den URL-Bereich ihrer App als "Basic" und "klar" festzuhalten. Verwenden Sie mehrere Routen für Aktionen **nur** bei Bedarf, z. b. zur Unterstützung vorhandener Clients.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen

Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

Im vorangehenden Code wird `[HttpPost("product/{id:int}")]` eine Routen Einschränkung angewendet. Die `ProductsController.ShowProduct` Aktion wird nur durch URL-Pfade wie abgeglichen `/product/3` . Der Weiterleitungs Vorlagen Teil `{id:int}` schränkt dieses Segment auf ganze Zahlen ein.

Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Benutzerdefinierte Routen Attribute mit iroutetemplateprovider

Alle [Routen Attribute](#rt) implementieren <xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider> . Die ASP.net Core Laufzeit:

* Sucht nach Attributen in Controller Klassen und Aktionsmethoden, wenn die APP gestartet wird.
* Verwendet die Attribute, die implementieren, `IRouteTemplateProvider` um den anfänglichen Satz von Routen zu erstellen.

Implementieren `IRouteTemplateProvider` Sie, um benutzerdefinierte Routen Attribute zu definieren. Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

Die vorherige `Get` Methode gibt zurück `Order = 2, Template = api/MyTestApi` .

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>Verwenden des Anwendungs Modells zum Anpassen von Attribut Routen

Das Anwendungsmodell:

* Ist ein Objektmodell, das beim Start erstellt wird.
* Enthält alle Metadaten, die von ASP.net Core verwendet werden, um die Aktionen in einer APP weiterzuleiten und auszuführen.

Das Anwendungsmodell umfasst alle Daten, die von Routen Attributen gesammelt werden. Die Daten aus Routen Attributen werden von der- `IRouteTemplateProvider` Implementierung bereitgestellt. Geregelt

* Kann geschrieben werden, um das Anwendungsmodell so zu ändern, dass das Routing Verhalten angepasst wird.
* Werden beim APP-Start gelesen.

Dieser Abschnitt zeigt ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungs Modells. Der folgende Code bewirkt, dass Routen ungefähr mit der Ordnerstruktur des Projekts übereinstimmen.

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

Der folgende Code verhindert, `namespace` dass die Konvention auf Controller angewendet wird, die Attribut weitergeleitet werden:

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

Der folgende Controller verwendet beispielsweise nicht `NamespaceRoutingConvention` :

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

Die `NamespaceRoutingConvention.Apply`-Methode:

* Führt keine Aktion aus, wenn der Controller Attribut weitergeleitet wird.
* Legt die Controller Vorlage auf Grundlage von fest `namespace` , wobei die Basis `namespace` entfernt wurde.

`NamespaceRoutingConvention`Kann in angewendet werden `Startup.ConfigureServices` :

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

Sehen Sie sich beispielsweise den folgenden Controller an:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

Im obigen Code:

* Die Basis `namespace` ist `My.Application` .
* Der vollständige Name des vorangehenden Controllers ist `My.Application.Admin.Controllers.UsersController` .
* Der `NamespaceRoutingConvention` legt die Controller Vorlage auf fest `Admin/Controllers/Users/[action]/{id?` .

`NamespaceRoutingConvention`Kann auch als Attribut auf einem Controller angewendet werden:

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Gemischtes Routing: Attributrouting vs. herkömmliches Routing

ASP.net Core-Apps können die Verwendung von herkömmlichem Routing und Attribut Routing vermischen. In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden. Alle **Routen Attribute** auf dem Controller machen **alle** Aktionen im Controller Attribut weiter.

Bei Attribut Routing und herkömmlichem Routing wird dieselbe Routing-Engine verwendet.

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL-Generierung und Ambient-Werte

Apps können Routing-URL-Generierungs Features verwenden, um URL-Links zu Aktionen zu generieren. Durch das Erstellen von URLs werden hart codierte URLs eliminiert, sodass der Code stabiler und verwallegbar wird. Dieser Abschnitt konzentriert sich auf die Funktionen der URL-Generierung, die von MVC bereitgestellt werden Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Die <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> Schnittstelle ist das zugrunde liegende Element der Infrastruktur zwischen MVC und dem Routing für die URL-Generierung. Eine Instanz von `IUrlHelper` ist über die- `Url` Eigenschaft in Controllern, Ansichten und Ansichts Komponenten verfügbar.

Im folgenden Beispiel wird die- `IUrlHelper` Schnittstelle mithilfe der- `Controller.Url` Eigenschaft verwendet, um eine URL zu einer anderen Aktion zu generieren.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Wenn die APP die herkömmliche Standardroute verwendet, ist der Wert der `url` Variablen die URL-Pfad Zeichenfolge `/UrlGeneration/Destination` . Dieser URL-Pfad wird durch das Routing erstellt, indem Folgendes kombiniert wird:

* Die Routen Werte aus der aktuellen Anforderung, die als **Umgebungs Werte** bezeichnet werden.
* Die Werte, die an weitergegeben werden, `Url.Action` und ersetzen diese Werte in der Routen Vorlage:

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt. Ein Routen Parameter, der keinen Wert hat, kann folgende Aktionen ausführen:

* Verwenden Sie ggf. einen Standardwert.
* Sie sollten übersprungen werden, wenn Sie optional ist. Beispielsweise die `id` aus der Routen Vorlage `{controller}/{action}/{id?}` .

Die URL-Generierung schlägt fehl, wenn für einen erforderlichen Routen Parameter kein entsprechender Wert vorhanden ist. Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.

Im vorherigen Beispiel von wird von `Url.Action` [herkömmlichem Routing](#cr)ausgegangen. Die URL-Generierung funktioniert ähnlich wie das [Attribut Routing](#ar), obwohl sich die Konzepte unterscheiden. Mit herkömmlichem Routing:

* Die Routen Werte werden zum Erweitern einer Vorlage verwendet.
* Die Routen Werte für `controller` und werden `action` in der Regel in dieser Vorlage angezeigt. Dies funktioniert, da die mit dem Routing übereinstimmenden URLs einer Konvention entsprechen.

Im folgenden Beispiel wird das Attribut Routing verwendet:

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

Durch die `Source` Aktion im vorangehenden Code wird generiert `custom/url/to/destination` .

<xref:Microsoft.AspNetCore.Routing.LinkGenerator> wurde in ASP.net Core 3,0 als Alternative zu hinzugefügt `IUrlHelper` . `LinkGenerator` bietet ähnliche, aber flexiblere Funktionen. Jede Methode `IUrlHelper` von verfügt auch über eine entsprechende Familie von-Methoden `LinkGenerator` .

### <a name="generating-urls-by-action-name"></a>Generieren von URLs nach Aktionsnamen

" [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)", " [Linkgenerator. getpathbyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)" und alle zugehörigen über Ladungen sind so konzipiert, dass der Ziel Endpunkt durch Angabe eines Controller namens und Aktions namens generiert wird.

Bei Verwendung `Url.Action` von werden die aktuellen Routen Werte für `controller` und `action` von der Laufzeit bereitgestellt:

* Der Wert von `controller` und `action` ist Teil der [Ambient-Werte](#ambient) und-Werte. Die `Url.Action` -Methode verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.

Beim Routing werden die Werte in Ambient-Werten verwendet, um Informationen einzugeben, die beim Erstellen einer URL nicht bereitgestellt wurden. Stellen Sie sich eine Route wie `{a}/{b}/{c}/{d}` mit Ambient-Werten vor `{ a = Alice, b = Bob, c = Carol, d = David }` :

* Das Routing verfügt über genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren.
* Das Routing verfügt über genügend Informationen, da alle Routen Parameter über einen Wert verfügen.

Wenn der Wert `{ d = Donovan }` hinzugefügt wird:

* Der Wert `{ d = David }` wird ignoriert.
* Der generierte URL-Pfad ist `Alice/Bob/Carol/Donovan` .

**Warnung**: URL-Pfade sind hierarchisch. Im vorherigen Beispiel, wenn der Wert `{ c = Cheryl }` hinzugefügt wird:

* Beide Werte `{ c = Carol, d = David }` werden ignoriert.
* Für ist kein Wert mehr vorhanden, `d` und die URL-Generierung schlägt fehl.
* Die gewünschten Werte von `c` und `d` müssen angegeben werden, um eine URL zu generieren.  

Möglicherweise erwarten Sie dieses Problem mit der Standardroute `{controller}/{action}/{id?}` . Dieses Problem tritt in der Praxis selten auf, da `Url.Action` explizit einen `controller` -Wert und einen- `action` Wert angibt.

Mehrere über Ladungen von [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) nehmen ein Routen Werte Objekt an, um Werte für andere Routen Parameter als und bereitzustellen `controller` `action` . Das Routen Werte Objekt wird häufig mit verwendet `id` . Beispiel: `Url.Action("Buy", "Products", new { id = 17 })`. Das Routen Werte Objekt:

* Gemäß der Konvention ist in der Regel ein Objekt des anonymen Typs.
* Kann ein `IDictionary<>` oder ein [poco](https://wikipedia.org/wiki/Plain_old_CLR_object)sein).

Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

Der vorangehende Code generiert `/Products/Buy/17?color=red` .

Mit dem folgenden Code wird eine absolute URL generiert:

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

Um einen absolute URL zu erstellen, verwenden Sie eine der folgenden Aktionen:

* Eine Überladung, die eine akzeptiert `protocol` . Beispielsweise der vorangehende Code.
* [Linkgenerator. geturibyaction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*), wodurch standardmäßig absolute URIs generiert werden.

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>Generieren von URLs nach Route

Der vorangehende Code hat das Erzeugen einer URL durch Übergeben des Controllers und des Aktions namens veranschaulicht. `IUrlHelper` stellt auch die [URL. RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*) -Familie der Methoden bereit. Diese Methoden ähneln [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*), Sie kopieren jedoch nicht die aktuellen Werte von `action` und `controller` in die Routen Werte. Die häufigste Verwendung von `Url.RouteUrl` :

* Gibt einen Routennamen zum Generieren der URL an.
* In der Regel wird kein Controller-oder Aktionsname angegeben.

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

Die folgende Razor Datei generiert einen HTML-Link zum `Destination_Route` :

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-no-locrazor"></a>Generieren von URLs in HTML und Razor

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper> stellt die <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper> Methoden [HTML. BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*) und [HTML. Action Link](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) zum Generieren von `<form>` - `<a>` bzw.-Elementen bereit. Diese Methoden verwenden die [URL. Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) -Methode, um eine URL zu generieren, und Sie akzeptieren ähnliche Argumente. Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.

Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`. Beide implementieren mit `IUrlHelper`. Weitere Informationen finden Sie [unter taghilfsprogramme in Formularen](xref:mvc/views/working-with-forms) .

In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>URL-Generierung in Aktions Ergebnissen

In den vorangehenden Beispielen wurde die Verwendung von `IUrlHelper` in einem Controller gezeigt. Die häufigste Verwendung eines Controllers ist das Generieren einer URL als Teil eines Aktions Ergebnisses.

Die Basisklassen <xref:Microsoft.AspNetCore.Mvc.ControllerBase> und <xref:Microsoft.AspNetCore.Mvc.Controller> stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen. Eine typische Verwendung besteht darin, nach Annahme der Benutzereingabe eine Umleitung durchzusetzen:

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

Die Aktions Ergebnisse Factorymethoden wie <xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction%2A> und <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction%2A> folgen einem ähnlichen Muster wie die Methoden in `IUrlHelper` .

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Sonderfall für dedizierte herkömmliche Routen

[Herkömmliches Routing](#cr) kann eine besondere Art von Routen Definition verwenden, die als [dedizierte konventionelle Route](#dcr)bezeichnet wird. Im folgenden Beispiel ist die Route mit dem Namen `blog` eine dedizierte konventionelle Route:

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

Wenn Sie die vorangehenden Routen Definitionen verwenden, `Url.Action("Index", "Home")` generiert den URL-Pfad `/` mithilfe der `default` Route, aber warum? Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.

[Dedizierte herkömmliche Routen](#dcr) basieren auf einem besonderen Verhalten von Standardwerten, die über keinen entsprechenden Routen Parameter verfügen, der verhindert, dass die Route mit der URL-Generierung zu [gierig](xref:fundamentals/routing#greedy) wird. In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet. Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen. Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht stimmen `{ controller = Blog, action = Article }` . Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Bereiche

[Bereiche](xref:mvc/controllers/areas) sind ein MVC-Feature, das verwendet wird, um verwandte Funktionen in einer Gruppe als separate zu organisieren:

* Routing Namespace für Controller Aktionen.
* Ordnerstruktur für Ansichten.

Die Verwendung von Bereichen ermöglicht es einer APP, mehrere Controller mit dem gleichen Namen zu verwenden, solange Sie über unterschiedliche Bereiche verfügen. Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`. In diesem Abschnitt wird erläutert, wie das Routing mit Bereichen interagiert. Ausführliche Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](xref:mvc/controllers/areas) .

Im folgenden Beispiel wird MVC so konfiguriert, dass die herkömmliche Standardroute und eine `area` Route für einen mit dem Namen verwendet werden `area` `Blog` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Im vorangehenden Code <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute%2A> wird aufgerufen, um zu erstellen `"blog_route"` . Der zweite Parameter, `"Blog"` , ist der Bereichs Name.

Beim Abgleichen eines URL-Pfads wie `/Manage/Users/AddUser` `"blog_route"` generiert die Route die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` . Der `area` Routen Wert wird durch einen Standardwert für erstellt `area` . Die von erstellte Route `MapAreaControllerRoute` entspricht Folgendem:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route. Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.

Beim herkömmlichen Routing ist die Reihenfolge wichtig. Im Allgemeinen sollten Routen mit Bereichen früher platziert werden, da Sie spezifischer als Routen ohne Bereich sind.

Im vorherigen Beispiel entsprechen die Routen Werte `{ area = Blog, controller = Users, action = AddUser }` der folgenden Aktion:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

Das [[Area]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) -Attribut bezeichnet einen Controller als Teil eines Bereichs. Dieser Controller befindet sich im `Blog` Bereich. Controller ohne ein `[Area]` -Attribut sind keine Elemente eines Bereichs und Stimmen **nicht** mit einem Wert identisch, wenn der `area` Routen Wert durch Routing bereitgestellt wird. Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

Der Namespace der einzelnen Controller wird hier aus Gründen der Vollständigkeit angezeigt. Wenn die vorangehenden Controller denselben Namespace verwenden, wird ein Compilerfehler generiert. Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.

Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird. Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.

<a name="aa"></a>

Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.

Wenn eine Aktion in einem Bereich ausgeführt wird, ist der Routen Wert für `area` als [Ambient-Wert](#ambient) für das Routing zur URL-Generierung verfügbar. Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

Der folgende Code generiert eine URL zu `/Zebra/Users/AddUser` :

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>Aktions Definition

Öffentliche Methoden auf einem Controller, außer denen mit dem [NonAction](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute) -Attribut, sind Aktionen.

## <a name="sample-code"></a>Beispielcode

* [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]
* [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC verwendet [Routing-Middleware](xref:fundamentals/middleware/index), um die URLs der eingehenden Anforderungen abzugleichen und sie Aktionen zuzuordnen. Routen werden im Startcode oder in Attributen definiert und beschreiben, wie URL-Pfade Aktionen zugeordnet werden sollen. Sie werden auch dazu verwendet, um URLs (für Links) zu generieren, die in Antworten gesendet werden.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Weitere Informationen finden Sie im Abschnitt [Gemischtes Routing](#routing-mixed-ref-label).

In diesem Artikel werden die Schnittstellen zwischen MVC und Routing und die Art und Weise erläutert, wie normale MVC-Apps Routingfeatures verwenden. Informationen zum erweiterten Routing finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

## <a name="setting-up-routing-middleware"></a>Einrichten der Routing-Middleware

Ihre *Configure*-Methode kann Code wie diesen enthalten:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Innerhalb des Aufrufs von `UseMvc` wird mit `MapRoute` eine einzelne Route erstellt, die wir `default`-Route nennen. Die meisten MVC-Apps verwenden eine Route mit einer Vorlage, die der `default`-Route ähnelt.

TDie Routenvorlage `"{controller=Home}/{action=Index}/{id?}"` kann einem URL-Pfad wie `/Products/Details/5` abgleichen und extrahiert die Routenwerte `{ controller = Products, action = Details, id = 5 }`, indem ein Token für den Pfad erstellt wird. MVC versucht, einen Controller namens `ProductsController` zu suchen und die Aktion `Details` auszuführen:

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

In diesem Beispiel hat die Modellbindung den `id`-Parameter mit dem Wert `id = 5` beim Aufrufen dieser Aktion auf `5` festgelegt. Weitere Informationen finden Sie unter [Modellbindung](../models/model-binding.md).

Verwenden der `default`-Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Die Routenvorlage:

* `{controller=Home}` definiert `Home` als Standard-`controller`.

* `{action=Index}` definiert `Index` als Standard-`action`.

* `{id?}` definiert `id` als optional.

Standardmäßige und optionale Routenparameter müssen nicht im URL-Pfad vorhanden sein, damit es eine Übereinstimmung gibt. Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

`"{controller=Home}/{action=Index}/{id?}"` gleicht den URL-Pfad `/` ab und erzeugt die Routenwerte `{ controller = Home, action = Index }`. Die Werte für `controller` und `action` verwenden die Standardwerte. `id` erzeugt keine Werte, da kein entsprechendes Segment im URL-Pfad vorhanden ist. MVC nutzt diese Routenwerte, um die `HomeController`- und `Index`-Aktion auszuwählen:

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

Mit dieser Controllerdefinition und dieser Routenvorlage wird die `HomeController.Index`-Aktion für jeden der folgenden URL-Pfade ausgeführt:

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

Mit der Hilfsmethode `UseMvcWithDefaultRoute`:

```csharp
app.UseMvcWithDefaultRoute();
```

kann Folgendes ersetzt werden:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` und `UseMvcWithDefaultRoute` fügen der Middleware-Pipeline eine Instanz von `RouterMiddleware` hinzu. MVC interagiert nicht direkt mit der Middleware und verarbeitet Anforderungen mithilfe von Routing. MVC ist über eine Instanz von `MvcRouteHandler` mit den Routen verbunden. Der Code in `UseMvc` ähnelt Folgendem:

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` definiert keine Routen direkt, sondern fügt der Routenauflistung einen Platzhalter für die `attribute`-Route hinzu. Die Überladung `UseMvc(Action<IRouteBuilder>)` ermöglicht es Ihnen, Ihre eigenen Routen hinzuzufügen und unterstützt darüber hinaus das Routingattribut.  `UseMvc` und alle Varianten fügen einen Platzhalter für die Attributroute hinzu. Attributrouting ist immer verfügbar, unabhängig von der Konfiguration von `UseMvc`. `UseMvcWithDefaultRoute` definiert eine Standardroute und unterstützt Attributrouting. Der Abschnitt [Attributrouting](#attribute-routing-ref-label) enthält weitere Informationen zu dem Thema.

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>Herkömmliches Routing

Die `default`-Route:

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

Der vorangehende Code ist ein Beispiel für ein herkömmliches Routing. Dieser Stil wird als herkömmliches Routing bezeichnet, da er eine *Konvention* für URL-Pfade festlegt:

* Das erste Pfad Segment wird dem Controller Namen zugeordnet.
* Die zweite wird dem Aktions Namen zugeordnet.
* Das dritte Segment wird für eine optionale verwendet `id` . `id` wird einer Modell Entität zugeordnet.

Mit dieser `default`-Route wir der URL-Pfad `/Products/List` der `ProductsController.List`-Aktion und `/Blog/Article/17``BlogController.Article` zugeordnet. Diese Zuordnung basiert **ausschließlich** auf den Controller- und Aktionsnamen und nicht auf Namespaces, Speicherorten für Quelldateien oder Methodenparametern.

> [!TIP]
> Die Kombination aus herkömmlichem Routing und Standardrouting ermöglicht es Ihnen, Anwendungen schnell zu erstellen, ohne für jede definierte Aktion ein neues URL-Muster entwerfen zu müssen. Bei Anwendungen mit Aktionen im CRUD-Stil können einheitliche URLs auf allen Controllern dabei helfen, den Code zu vereinfachen und die UI vorhersehbarer zu machen.

> [!WARNING]
> `id` wird von der Routenvorlage als optional definiert, was bedeutet, dass Ihre Aktionen ausgeführt werden, ohne dass die ID als Teil der URL bereitgestellt wird. Wenn `id` in der URL ausgelassen wird, wird es üblicherweise von der Modellbindung auf `0` festgelegt, und daher wird in der Datenbank keine Entität gefunden, die `id == 0` entspricht. Mit dem Attributrouting können Sie präzise steuern, für welche Aktionen die ID erforderlich ist und für welche nicht. Gemäß der Konvention enthält die Dokumentation optionale Parameter wie `id`, wenn sie wahrscheinlich in der richtigen Verwendung angezeigt werden.

## <a name="multiple-routes"></a>Mehrere Routen

Sie können mehrere Routen innerhalb von `UseMvc` hinzufügen, indem Sie weitere Aufrufe von `MapRoute` hinzufügen. So können Sie mehrere Konventionen definieren oder herkömmlichen Routen hinzufügen, die einer bestimmten Aktion zugeordnet sind, z.B.:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Die `blog`-Route hier ist eine *dedizierte herkömmliche Route*, d.h., dass das herkömmliche Routingsystem verwendet wird, aber einer bestimmten Aktion zugeordnet ist. Da `controller` und `action` nicht als Parameter in der Routenvorlage vorkommen, können sie nur die Standardwerte haben. Daher wird diese Route immer der Aktion `BlogController.Article` zugeordnet.

Die Routen in der Routenauflistung sind geordnet und werden in der Reihenfolge verarbeitet, in der sie hinzugefügt wurden. In diesem Beispiel wird daher die Route `blog` vor der Route `default` überprüft.

> [!NOTE]
> *Dedizierte herkömmliche Routen* verwenden häufig **catch-all-** Routen Parameter wie `{*article}` , um den verbleibenden Teil des URL-Pfads zu erfassen. Dies kann eine Route „zu gierig“ machen, d.h., dass sie alle URLs abgleicht, die eigentlich von anderen Routen abgeglichen werden sollten. Fügen Sie die „gierigen“ Routen weiter unten in die Routingtabelle ein, um dieses Problem zu beheben.

### <a name="fallback"></a>Fallback

Im Rahmen der Anforderungsverarbeitung überprüft MVC, ob mit den Routenwerten ein Controller und eine Aktion in Ihrer Anwendung gefunden werden können. Falls die Routenwerte mit keiner Aktion übereinstimmen, gilt die Route nicht als Übereinstimmung, und die nächste Route wird überprüft. Dabei spricht man von einem *Fallback*. Dieser Vorgang soll Szenarios vereinfachen, bei denen sich herkömmliche Routen überschneiden.

### <a name="disambiguating-actions"></a>Aktionen eindeutig zuordnen

Wenn zwei Aktionen beim Routing übereinstimmen, muss MVC beide analysieren und die beste auswählen oder eine Ausnahme auslösen. Beispiel:

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

Dieser Controller definiert zwei Aktionen, die mit dem URL-Pfad `/Products/Edit/17` übereinstimmen und die Daten `{ controller = Products, action = Edit, id = 17 }` weiterleiten. Dies ist ein typisches Muster für MVC-Controller, in dem `Edit(int)` ein Formular zum Bearbeiten eines Produkts anzeigt und `Edit(int, Product)` das bereitgestellte Formular verarbeitet. Um dies zu ermöglichen, muss MVC `Edit(int, Product)` auswählen, wenn die Anforderung HTTP-`POST` ist, und `Edit(int)`, wenn das HTTP-Verb ein anderes ist.

`HttpPostAttribute` (`[HttpPost]`) ist eine Implementierung von `IActionConstraint`, bei der die Aktion nur ausgewählt werden darf, wenn das HTTP-Verb `POST` ist. Aufgrund des Vorhandenseins von `IActionConstraint` ist `Edit(int, Product)` die „bessere“ Übereinstimmung als `Edit(int)`, sodass `Edit(int, Product)` zuerst überprüft wird.

Benutzerdefinierte `IActionConstraint`-Implementierungen müssen Sie nur in speziellen Szenarios schreiben. Es ist jedoch wichtig, die Rolle von Attributen wie `HttpPostAttribute` zu kennen, denn ähnliche Attribute sind auch für andere HTTP-Verben definiert. Beim herkömmlichen Routing nutzen Aktionen oft denselben Aktionsnamen, wenn sie Teil eines `show form -> submit form`-Workflows sind. Die Vorteile dessen werden deutlicher, wenn Sie den Abschnitt [Verstehen von IActionConstraint](#understanding-iactionconstraint) gelesen haben.

Wenn mehrere Routen übereinstimmen und MVC die „beste“ nicht bestimmen kann, löst es eine `AmbiguousActionException` aus.

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>Routennamen

Die Zeichenfolgen `"blog"` und `"default"` in den folgenden Beispielen sind Routennamen:

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Der Routenname gibt der Route einen logischen Namen, damit die benannte Route bei der URL-Generierung verwendet werden kann. Dadurch wird die URL-Erstellung erheblich vereinfacht, obwohl die Reihenfolge der Routen die URL-Generierung verkomplizieren sollte. Routennamen müssen anwendungsweit eindeutig sein.

Routennamen haben keine Auswirkung auf die URL-Zuordnung oder die Verarbeitung von Anforderungen. Sie dienen nur der URL-Generierung. Unter [Routing](xref:fundamentals/routing) finden Sie weitere ausführliche Informationen zur URL-Generierung, einschließlich der Generierung in MVC-spezifischen Hilfsprogrammen.

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>Attributrouting

Beim Attributrouting werden Aktionen mithilfe von Attributen direkt Routenvorlagen zugeordnet. Im folgenden Beispiel wird `app.UseMvc();` in der `Configure`-Methode verwendet, und es wird keine Route übergeben. `HomeController` entspricht URLs, die denen ähneln, denen die Standardroute `{controller=Home}/{action=Index}/{id?}` entsprechen würde:

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

Die `HomeController.Index()`-Aktion wird für die URL-Pfade `/`, `/Home` und `/Home/Index` jeweils ausgeführt.

> [!NOTE]
> In diesem Beispiel wird ein wichtiger Unterschied beim Programmieren zwischen dem Attributrouting und dem herkömmlichen Routing hervorgehoben. Attributrouting erfordert mehr Eingabe, um eine Route anzugeben. Die herkömmliche Standardroute verarbeitet auch kürzere Routen. Attributrouting ermöglicht jedoch (und erfordert auch) die präzise Kontrolle der Routenvorlagen, die für die einzelnen Aktionen gelten.

Beim Attributrouting haben der Controller- und der Aktionsname **keine** Auswirkung darauf, welche Aktion ausgewählt wird. In diesem Beispiel werden dieselben URLs wie im vorherigen Beispiel abgeglichen.

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> Die oben genannten Routenvorlagen definieren keine Routenparameter für `action`, `area` und `controller`. Diese Routenparameter sind in Attributrouten nicht zulässig. Da die Routenvorlage bereits einer Aktion zugeordnet ist, wäre es nicht sinnvoll, den Aktionsnamen aus der URL zu analysieren.

## <a name="attribute-routing-with-httpverb-attributes"></a>Attributrouting mit Http[Verb]-Attributen

Beim Attributrouting können auch `Http[Verb]`-Attribute wie `HttpPostAttribute` verwendet werden. Alle diese Attribute können eine Routenvorlage akzeptieren. In diesem Beispiel werden zwei Aktionen gezeigt, die derselben Routenvorlage zugeordnet sind:

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

Für einen URL-Pfad wie `/products` wird die Aktion `ProductsApi.ListProducts` ausgeführt, wenn das HTTP-Verb `GET` ist, und `ProductsApi.CreateProduct` wird ausgeführt, wenn das HTTP-Verb `POST` entspricht. Attributrouting gleicht zuerst die URL gegen die Routenvorlagen ab, die von den Routenattributen definiert werden. Sobald eine Routenvorlage übereinstimmt, werden `IActionConstraint`-Einschränkungen angewendet, um zu bestimmen, welche Aktionen ausgeführt werden können.

> [!TIP]
> Beim Erstellen einer REST-API wird `[Route(...)]` selten für eine Aktionsmethode verwendet, die Aktion alle HTTP-Methoden akzeptiert. Es ist besser, das spezifischere `Http*Verb*Attributes` zu nutzen, um präzise anzugeben, was Ihre API unterstützt. REST-API-Clients sollten wissen, welche Pfade und HTTP-Verben bestimmten logischen Operationen entsprechen.

Da eine Attributroute für eine bestimmte Aktion gilt, ist es einfach, Parameter als Teil der Routenvorlagendefinition erforderlich festzulegen. In diesem Beispiel ist `id` als Teil des URL-Pfads erforderlich.

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Die `ProductsApi.GetProduct(int)`-Aktion wird für einen URL-Pfad wie `/products/3` ausgeführt, jedoch nicht für einen URL-Pfad wie `/products`. Eine vollständige Beschreibung und Routenvorlagen und dazugehörige Optionen finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

## <a name="route-name"></a>Routenname

Im folgenden Code wird ein *Routenname* von `Products_List` definiert:

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

Routennamen können verwendet werden, um basierend auf einer bestimmten Route eine URL zu generieren. Routennamen haben keine Auswirkung auf das URL-Zuordnungsverhalten des Routings und dienen nur zur URL-Generierung. Routennamen müssen anwendungsweit eindeutig sein.

> [!NOTE]
> Vergleichen Sie dies mit der herkömmlichen *Standardroute*, die den `id`-Parameter als optional definiert (`{id?}`). APIs präzise angeben zu können, hat Vorteile, z.B. können `/products` und `/products/5` an unterschiedliche Aktionen gesendet werden.

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>Kombinieren von Routen

Um Attributrouting weniger repetitiv zu gestalten, werden Routenattribute auf dem Controller mit Routenattributen auf den einzelnen Aktionen kombiniert. Alle auf dem Controller definierten Routenvorlagen werden den Routenvorlagen auf den Aktionen vorangestellt. Wenn Routenattribute auf dem Controller platziert werden, verwenden **alle** Aktionen im Controller Attributrouting.

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

In diesem Beispiel kann der URL-Pfad `/products``ProductsApi.ListProducts` und der URL-Pfad `/products/5``ProductsApi.GetProduct(int)` zugeordnet werden. Beide Aktionen gleichen nur „HTTP `GET`“ ab, weil sie mit `HttpGetAttribute` markiert sind.

Routenvorlagen, die auf eine Aktion angewendet werden, die mit einem `/` oder `~/` beginnen, können nicht mit Routenvorlagen kombiniert werden, die auf den Controller angewendet werden. In diesem Beispiel werden mehrere URL-Pfade zugeordnet, die der *Standardroute* ähneln.

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>Ordnen der Attributrouten

Im Gegensatz zu herkömmlichen Routen, die in einer definierten Reihenfolge ausgeführt werden, erstellt das Attribut Routing eine Struktur und gleicht alle Routen gleichzeitig ab. Der Vorgang wird also ausgeführt, als ob die Routeneinträge in der idealen Reihenfolge platziert worden wären: Die spezifischsten Routen können also vor den allgemeineren ausgeführt werden.

Eine Route wie `blog/search/{topic}` ist z.B. spezifischer als eine Route wie `blog/{*article}`. Logisch gesehen wird die Route `blog/search/{topic}` standardmäßig zuerst ausgeführt, da dies die einzig sinnvolle Reihenfolge ist. Beim herkömmlichen Routing ist der Entwickler verantwortlich dafür, die Routen in die gewünschte Reihenfolge zu bringen.

Attributrouten können mithilfe der `Order`-Eigenschaft aller vom Framework bereitgestellten Routenattribute eine Reihenfolge konfigurieren. Routen werden entsprechend einer aufsteigenden Reihenfolge der `Order`-Eigenschaft verarbeitet. Die Standardreihenfolge ist `0`. Wird für eine Route `Order = -1` festgelegt, wird sie vor denjenigen Routen ausgeführt, für die keine Reihenfolge festgelegt wurde. Wird für eine Route `Order = 1` festgelegt, wird sie ausgeführt, nachdem die Standardroutenreihenfolge ausgeführt wurde.

> [!TIP]
> Vermeiden Sie eine Abhängigkeit von `Order`. Wenn der URL-Raum explizite Reihenfolgenwerte erfordert, um korrekt weiterzuleiten, ist es wahrscheinlich auch für Clients verwirrend. Beim Attributrouting wird im Allgemeinen mithilfe der URL-Zuordnung die richtige Route ausgewählt. Wenn die für die URL-Generierung verwendete Standardreihenfolge nicht funktioniert, ist es meist einfacher, Routennamen als Außerkraftsetzung zu verwenden, statt die `Order`-Eigenschaft anzuwenden.

Razor Pages-Routing und MVC Controller-Routing verwenden eine gemeinsame Implementierung. Informationen zur Routen Reihenfolge in den Razor Seiten Themen finden Sie unter [ Razor pages Route und App Conventions: Route Order](xref:razor-pages/razor-pages-conventions#route-order).

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>Ersetzen von Token in Routenvorlagen ([controller], [action] [area])

Zur einfacheren Unterstützung unterstützen Attribut Routen die *Tokenersetzung* durch Einschließen eines Tokens in eckige Klammern ( `[` , `]` ). Die Token `[action]`, `[area]` und `[controller]` werden durch die Werte der Aktionsnamen, den Namen des Bereichs und des Controllers der Aktion ersetzt, in dem die Route definiert ist. In dem folgenden Beispiel entsprechen die Aktionen wie in den Kommentaren beschrieben URL-Pfaden:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

Die Tokenersetzung tritt im letzten Schritt der Erstellung von Attributrouten auf. Der folgende Code verhält sich genauso wie der aus dem obigen Beispiel:

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

Attributrouten können auch mit Vererbung kombiniert werden. Dies ist besonders effektiv in Kombination mit der Tokenersetzung.

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

Tokenersetzung gilt auch für Routennamen, die durch Attributrouten definiert werden. `[Route("[controller]/[action]", Name="[controller]_[action]")]` generiert für jede Aktion einen eindeutigen Routennamen.

Damit das Trennzeichen `[` oder `]` der Tokenersetzungs-Literalzeichenfolge bei einem Abgleich gefunden wird, muss es doppelt vorhanden sein (`[[` oder `]]`), was einem Escapezeichen entspricht.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>Verwenden eines Parametertransformators zum Anpassen der Tokenersetzung

Die Tokenersetzung kann mit einem Parametertransformator angepasst werden. Ein Parametertransformator implementiert `IOutboundParameterTransformer` und wandelt den Wert der Parameter um. Beispielsweise ändert ein benutzerdefinierter `SlugifyParameterTransformer`-Parametertransformator den Routenwert `SubscriptionManagement` in `subscription-management`.

Die `RouteTokenTransformerConvention` ist eine Anwendungsmodellkonvention, die Folgendes ausführt:

* Wendet einen angegebenen Parametertransformator auf alle Attributrouten in der App an.
* Passt die Tokenwerte für Attributrouten bei ihrer Ersetzung an.

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

Die `RouteTokenTransformerConvention` wird als Option in `ConfigureServices` registriert.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
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

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>Mehrere Routen

Attributrouting unterstützt das Definieren mehrerer Routen, die zu derselben Aktion führen. Dies wird am häufigsten beim Imitieren des Verhaltens der *herkömmlichen Standardroute* verwendet. Im folgenden Beispiel wird dies gezeigt:

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

Wenn mehrere Routenattribute auf dem Controller platziert werden, wird jedes mit jedem der Routenattribute auf den Aktionsmethoden kombiniert.

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

Werden mehrere Routenattribute (die `IActionConstraint` implementieren) auf einer Aktion platziert, wird jede Aktionseinschränkung mit der Routenvorlage aus dem Attribut kombiniert, das sie definiert.

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> Obwohl das Verwenden mehrerer Routen für Aktionen sehr hilfreich scheint, ist es empfehlenswerter, den URL-Raum der Anwendung einfach und gut definiert zu halten. Verwenden Sie nur mehrere Routen für Aktionen, wenn dies notwendig ist, z.B. um vorhandene Clients zu unterstützen.

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>Angeben von optionalen Attributroutenparametern, Standardwerten und Einschränkungen

Attributrouten unterstützen dieselbe Inline-Syntax wie herkömmliche Routen, um optionale Parameter, Standardwerte und Einschränkungen anzugeben.

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

Eine ausführliche Beschreibung der Syntax der Routenvorlage finden Sie unter [Routenvorlagenreferenz](xref:fundamentals/routing#route-template-reference).

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>Benutzerdefinierte Routenattribute mithilfe von `IRouteTemplateProvider`

Alle im Framework bereitgestellten Routenattribute (`[Route(...)]`, `[HttpGet(...)]` usw.) implementieren die `IRouteTemplateProvider`-Schnittstelle. MVC sucht in Controllerklassen und Aktionsmethoden nach Attributen, wenn die Anwendung gestartet wird, und verwendet diejenigen, die `IRouteTemplateProvider` implementieren, um die anfänglichen Routen zu erstellen.

Sie können `IRouteTemplateProvider` implementieren, um eigene Routenattribute zu definieren. Jeder `IRouteTemplateProvider` lässt Sie eine einzelne Route mit einer benutzerdefinierten Routenvorlage, Reihenfolge und einem benutzerdefinierten Namen definieren:

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

Das Attribut aus dem obigen Beispiel legt `Template` automatisch auf `"api/[controller]"` fest, wenn `[MyApiController]` angewendet wird.

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>Anpassen von Attributrouten mithilfe des Anwendungsmodells

Das *Anwendungsmodell* ist ein Objektmodell, das beim Starten aus allen Metadaten von MVC erstellt wird, um Ihre Aktionen weiterzuleiten und auszuführen. Das *Anwendungsmodell* enthält alle Daten, die aus Routenattributen (über `IRouteTemplateProvider`) erfasst wurden. Sie können *Konventionen* schreiben, damit das Anwendungsmodell beim Startzeitpunkt das Routingverhalten anpasst. Dieser Abschnitt enthält ein einfaches Beispiel für das Anpassen des Routings mithilfe des Anwendungsmodells.

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>Gemischtes Routing: Attributrouting vs. herkömmliches Routing

MVC-Anwendungen können herkömmliches Routing und Attributrouting kombinieren. In der Regel werden herkömmliche Routen für Controller verwendet, die für Browser-HTML-Seiten gedacht sind, und das Attributrouting für Controller für REST-APIs.

Aktionen werden entweder herkömmlich oder über Attribute zugeordnet, d.h., dass eine Route auf dem Controller oder der Aktion platziert wird. Aktionen, die Attributrouten definieren, können nicht über die herkömmliche Routen und umgekehrt erreicht werden. Wenn nur **ein** Routenattribut auf dem Controller platziert wird, werden alle Aktionen im Controller über Attribute zugeordnet.

> [!NOTE]
> Die beiden Routingmethoden unterscheiden sich in dem Prozess, der angewendet wird, nachdem eine URL einer Routenvorlage zugeordnet wurde. Beim herkömmlichen Routing dienen die Routenwerte aus der Zuordnung dazu, aus einer Nachschlagetabelle aller herkömmlich zugeordneten Aktionen die Aktion und den Controller auszuwählen. Beim Attributrouting ist jede Vorlage bereits einer Aktion zugeordnet, und keine weitere Suche ist erforderlich.

## <a name="complex-segments"></a>Komplexe Segmente

Komplexe Segmente (z.B. `[Route("/dog{token}cat")]`) werden von rechts nach links auf eine nicht gierige Weise durch entsprechende Literale verarbeitet. Eine entsprechende Beschreibung finden Sie im [Quellcode](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296). Weitere Informationen finden Sie unter [diesem Problem](https://github.com/dotnet/AspNetCore.Docs/issues/8197).

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL-Generierung

MVC-Anwendungen können Routingfeatures zur URL-Generierung verwenden, um URL-Links zu Aktionen zu generieren. Durch das Generieren von URLs müssen URLs nicht mehr hartcodiert werden, sodass der Code robuster und leichter verwaltbar wird. Dieser Abschnitt konzentriert sich auf die von MVC bereitgestellte URL-Generierung und befasst sich nur kurz mit der Funktionsweise. Eine detaillierte Beschreibung der URL-Generierung finden Sie unter [Routing in ASP.NET Core](xref:fundamentals/routing).

Die `IUrlHelper`-Schnittstelle ist die zugrunde liegende Infrastruktur zwischen MVC und dem Routing zur URL-Generierung. Eine Instanz von `IUrlHelper` ist über die `Url`-Eigenschaft in Controllern, Ansichten und Ansichtskomponenten verfügbar.

In diesem Beispiel wird die `IUrlHelper`-Schnittstelle von der `Controller.Url`-Eigenschaft dazu verwendet, eine URL in einer anderen Aktion zu generieren.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

Wenn die Anwendung die herkömmliche Standardroute verwendet, ist der Wert der `url`-Variable die URL-Pfadzeichenfolge `/UrlGeneration/Destination`. Dieser URL-Pfad wird vom Routing erstellt, indem Routenwerte aus der aktuellen Anforderung (Umgebungswerte) mit den an `Url.Action` übergebenen Werten kombiniert und anschließend in die Routenvorlage eingefügt werden:

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

Der Wert eines jeden Routenparameters wird in der Routenvorlage durch die entsprechenden Namen mit den Werten und Umgebungswerten ersetzt. Ein Routenparameter ohne Wert kann einen Standardwert verwenden, wenn er über einen verfügt, oder übersprungen werden, wenn er optional ist (wie im Fall von `id` in diesem Beispiel). Die URL-Generierung schlägt fehl, wenn ein erforderlicher Routenparameter keinen entsprechenden Wert besitzt. Wenn die URL-Generierung für eine Route fehlschlägt, wird die nächste Route ausprobiert, bis alle Routen getestet wurden oder eine Übereinstimmung gefunden wurde.

In dem Beispiel mit `Url.Action` oben wird von herkömmlichem Routing ausgegangen. Die URL-Generierung funktioniert ähnlich wie das Attributrouting, obwohl sich die Konzepte unterscheiden. Bei herkömmlichem Routing wird mit den Routenwerten eine Vorlage erweitert, und die Routenwerte für `controller` und `action` kommen in der Regel in dieser Vorlage vor. Das funktioniert, weil sich die mit dem Routing zugeordneten URLs an eine *Konvention* halten. Beim Attributrouting dürfen die Routenwerte für `controller` und `action` nicht in der Vorlage vorkommen. Sie werden stattdessen verwendet, um nachzuschlagen, welche Vorlage verwendet werden soll.

In diesem Beispiel wird das Attributrouting verwendet:

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC erstellt eine Nachschlagetabelle aller über Attribute zugeordneten Aktionen und ordnet die `controller`- und `action`-Werte zu, um die Routenvorlage auszuwählen, die für die URL-Generierung verwendet werden soll. Im obigen Beispiel wird `custom/url/to/destination` generiert.

### <a name="generating-urls-by-action-name"></a>Generieren von URLs nach Aktionsnamen

`Url.Action` (`IUrlHelper` . `Action`) und alle zugehörigen Überladungen bauen alle auf der Idee auf, dass Sie angeben, was Sie verknüpfen möchten, indem Sie einen Controllernamen und einen Aktionsnamen angeben.

> [!NOTE]
> Bei Verwendung von `Url.Action` werden die aktuellen Routenwerte für `controller` und `action` für Sie festgelegt. Die Werte von `controller` und `action` bestehen sowohl aus *Umgebungswerten* **als auch aus** *Werten*. Die Methode `Url.Action` verwendet immer die aktuellen Werte von `action` und `controller` und generiert einen URL-Pfad, der zur aktuellen Aktion weiterleitet.

Beim Routing wird versucht, mit den Werten in den Umgebungswerten Informationen auszufüllen, die Sie beim Generieren einer URL nicht bereitgestellt haben. Mit Routen wie `{a}/{b}/{c}/{d}` und Umgebungswerten wie `{ a = Alice, b = Bob, c = Carol, d = David }` hat das Routing genügend Informationen, um eine URL ohne zusätzliche Werte zu generieren, da alle Routenparameter einen Wert aufweisen. Wenn Sie den Wert `{ d = Donovan }` hinzufügen, wird der Wert `{ d = David }` ignoriert, und der generierte URL-Pfad wäre `Alice/Bob/Carol/Donovan`.

> [!WARNING]
> URL-Pfade sind hierarchisch. Wenn Sie im obigen Beispiel den Wert `{ c = Cheryl }` hinzufügen, werden die beiden Werte `{ c = Carol, d = David }` ignoriert. In diesem Fall haben wir keinen Wert für `d` mehr, und die URL-Generierung schlägt fehl. Sie müssten dann den gewünschten Wert von `c` und `d` angeben.  Man könnte annehmen, dass dieses Problem bei der Standardroute auftritt (`{controller}/{action}/{id?}`). Tatsächlich passiert es in der Praxis jedoch selten, das `Url.Action` immer explizit einen `controller`- und `action`-Wert angibt.

Längere Überladungen von `Url.Action` akzeptieren auch ein zusätzliches *route values*-Objekt, um andere Werte für Routenparameter als `controller` und `action` bereitzustellen. Es wird in der Regel mit `id` verwendet, z.B. in `Url.Action("Buy", "Products", new { id = 17 })`. Gemäß der Konvention ist das *Routenwerte*-Objekt eines des anonymen Typs, es kann aber auch ein `IDictionary<>`-Objekt oder ein *Plain Old .NET Object* sein. Alle zusätzlichen Routenwerte, die keinen Routenparametern zugeordnet sind, werden in der Abfragezeichenfolge platziert.

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> Um eine absolute URL zu erstellen, verwenden Sie eine Überladung, die eine `protocol` akzeptiert: `Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>Generieren von URLs nach Routen

Im obigen Code wurde das Generieren einer URL durch das Übergeben des Controller- und Aktionsnamens veranschaulicht. `IUrlHelper` stellt außerdem die `Url.RouteUrl`-Methodenfamilie bereit. Diese Methoden ähneln `Url.Action`, kopieren jedoch nicht die aktuellen Werte `action` und `controller` in die Routenwerte. Oftmals wird damit ein Routenname angegeben, um mit einer bestimmten Route die URL zu generieren, in der Regel *ohne* Angabe eines Controller- oder Aktionsnamens.

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>Generieren von URLs in HTML

`IHtmlHelper` stellt die `HtmlHelper`-Methoden `Html.BeginForm` und `Html.ActionLink` bereit, um jeweils `<form>`- und `<a>`-Elemente zu generieren. Diese Methoden verwenden die `Url.Action`-Methode, um eine URL zu generieren, und akzeptieren ähnliche Argumente. Die `Url.RouteUrl`-Begleiter für `HtmlHelper` sind `Html.BeginRouteForm` und `Html.RouteLink`, die ähnliche Funktionen aufweisen.

Taghilfsprogramme generieren URLs mit den Taghilfsprogrammen `form` und `<a>`. Beide implementieren mit `IUrlHelper`. Weitere Informationen finden Sie unter [Einführung in die Verwendung von Taghilfsprogrammen in Formularen in ASP.NET Core](../views/working-with-forms.md).

In Ansichten ist `IUrlHelper` über die `Url`-Eigenschaft für jede Ad-hoc-URL-Generierung verfügbar, die keine der oben genannten ist.

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>Generieren von URLs in Aktionsergebnissen

In den obigen Beispielen wurde gezeigt, wie `IUrlHelper` in einem Controller verwendet wird und dass es üblicherweise dazu dient, eine URL im Rahmen eines Aktionsergebnisses zu generieren.

Die Basisklassen `ControllerBase` und `Controller` stellen Hilfsmethoden für Aktionsergebnisse bereit, die auf eine andere Aktionen verweisen. Eine typische Verwendung besteht darin, nach dem Akzeptieren einer Benutzereingabe weiterzuleiten.

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

Die Factorymethoden der Aktionsergebnisse folgen einem ähnlichen Muster wie die Methoden für `IUrlHelper`.

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>Sonderfall für dedizierte herkömmliche Routen

Beim herkömmlichen Routing können Sie eine bestimmte Art von Routendefinition verwenden, die als *dedizierte herkömmliche Route* bezeichnet wird. Die Route namens `blog` im folgenden Beispiel ist eine dedizierte herkömmliche Route.

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

Wenn Sie diese Routendefinitionen verwenden, generiert `Url.Action("Index", "Home")` den URL-Pfad `/` mit der `default`-Route. Die Frage ist nur warum? Man könnte meinen, dass die Routenwerte `{ controller = Home, action = Index }` ausreichen würden, um eine URL mithilfe von `blog` zu generieren, und das Ergebnis wäre `/blog?action=Index&controller=Home`.

Dedizierte herkömmliche Routen nutzen ein spezielles Verhalten von Standardwerten, die keinen entsprechenden Routenparameter besitzen, der verhindert, dass die Route bei der URL-Generierung „zu gierig“ wird. In diesem Fall sind die Standardwerte `{ controller = Blog, action = Article }`, und weder `controller` noch `action` werden als Routenparameter verwendet. Wenn das Routing die URL-Generierung ausführt, müssen die angegebenen Werte mit den Standardwerten übereinstimmen. Die URL-Generierung mithilfe von `blog` schlägt fehl, da die Werte `{ controller = Home, action = Index }` nicht mit `{ controller = Blog, action = Article }` übereinstimmen. Routing greift dann wieder auf `default` zurück, was erfolgreich ausgeführt wird.

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>Bereiche

[Bereiche](areas.md) sind ein MVC-Feature, mit dem verwandte Funktionen in einer Gruppe als separater Routing-Namespace (für Controlleraktionen) und als Ordnerstruktur (für Ansichten) organisiert werden können. Mithilfe von Bereichen kann eine Anwendung mehrere Controller mit demselben Namen haben – solange sie verschiedene *Bereiche* haben. Mithilfe von Bereichen wird außerdem eine Hierarchie erstellt, damit das Routing durch Hinzufügen eines anderen Routenparameters ausgeführt werden kann: `area` zu `controller` und `action`. In diesem Abschnitt wird erläutert, wie Routing mit Bereichen interagiert. Weitere Informationen zur Verwendung von Bereichen mit Ansichten finden Sie unter [Bereiche](areas.md).

Im folgenden Beispiel wird MVC konfiguriert, sodass es die standardmäßige herkömmliche Route verwendet und eine *Bereichsroute* für einen Bereich namens `Blog`:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

Beim Abgleich eines URL-Pfads wie `/Manage/Users/AddUser` erzeugt die erste Route die Routenwerte `{ area = Blog, controller = Users, action = AddUser }`. Der `area`-Routenwert wird von einem Standardwert für `area` erzeugt. Die von `MapAreaRoute` erstellte Route entspricht in der Tat der folgenden:

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` erstellt mit einem Standardwert und einer Einschränkung für `area` sowie mit dem bereitgestellten Bereichsnamen (in diesem Fall `Blog`) eine Route. Der Standardwert stellt sicher, dass die Route immer `{ area = Blog, ... }` erzeugt, die Einschränkung erfordert den Wert `{ area = Blog, ... }` für URL-Generierung.

> [!TIP]
> Beim herkömmlichen Routing ist die Reihenfolge wichtig. Routen mit Bereichen werden im Allgemeinen früher in der Routentabelle aufgeführt als die spezifischeren Routen ohne Bereich.

Die Routenwerte im oben genannten Beispiel werden der folgenden Aktion zugeordnet:

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` kennzeichnet einen Controller als Teil eines Bereichs, z.B. des Bereichs `Blog`. Controller ohne `[Area]`-Attribut gehören demnach zu keinem Bereich und stimmen **nicht** überein, wenn die `area`-Route wird vom Routing bereitgestellt wird. Im folgenden Beispiel kann nur der erste aufgelistete Controller die Routenwerte `{ area = Blog, controller = Users, action = AddUser }` abgleichen.

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> Aus Gründen der Vollständigkeit wird hier der Namespace jedes Controllers dargestellt. Andernfalls gäbe es einen Namenskonflikt zwischen den Controllern, und ein Compilerfehler würde generiert. Klassennamespaces haben keine Auswirkungen auf das MVC-Routing.

Die ersten beiden Controller gehören zu Bereichen und werden können nur abgleichen, wenn ihr jeweiliger Bereichsname vom `area`-Routenwert bereitgestellt wird. Der dritte Controller gehört keinem Bereich an und kann nur abgleichen, wenn vom Routing kein Wert für `area` bereitgestellt wird.

> [!NOTE]
> Im Hinblick auf das Erkennen *keines Werts* hat die Abwesenheit des `area`-Werts dieselben Auswirkungen, wie wenn der Wert für `area` 0 (null) oder eine leere Zeichenfolge wäre.

Beim Ausführen einer Aktion innerhalb eines Bereichs ist der Routenwert für `area` als *Umgebungswert* für das Routing verfügbar und kann für die URL-Generierung verwendet werden. Das bedeutet, dass Bereiche bei der URL-Generierung wie im folgenden Beispiel dargestellt standardmäßig *beständig* sind.
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>Verstehen von IActionConstraint

> [!NOTE]
> Dieser Abschnitt enthält Details zu Framework-Mechanismen und dazu, wie eine auszuführende Aktion in MVC auswählt wird. Eine typische Anwendung benötigt kein benutzerdefiniertes `IActionConstraint`.

Sie haben `IActionConstraint` wahrscheinlich bereits verwendet, auch wenn Sie mit der Oberfläche noch nicht vertraut sind. Das `[HttpGet]`-Attribut und ähnliche `[Http-VERB]`-Attribute implementieren `IActionConstraint`, um die Ausführung einer Aktionsmethode einzuschränken.

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

Unter Annahme der herkömmlichen Standardroute würde der URL-Pfad `/Products/Edit` die Werte `{ controller = Products, action = Edit }` erzeugen, die **beiden** hier gezeigten Aktionen entsprechen. In `IActionConstraint`-Terminologie würde man sagen, dass es sich bei beiden genannten Aktionen um Kandidaten handelt, da beide den Routendaten entsprechen.

Wenn `HttpGetAttribute` ausgeführt wird, wird angegeben, dass *Edit()* mit *GET* übereinstimmt und mit keinem anderen HTTP-Verb. Für die `Edit(...)`-Aktion wurde keine Einschränkungen definiert, und daher stimmt sie mit allen HTTP-Verben überein. Wenn wir von `POST` ausgehen, stimmt nur `Edit(...)` überein. Für `GET` können jedoch beide Aktionen übereinstimmen. Eine Aktion mit `IActionConstraint` wird einer Aktion ohne jedoch immer *vorgezogen*. Da `Edit()` über `[HttpGet]` verfügt, gilt die Methode als spezifischer und wird ausgewählt, wenn beide Aktionen das Verb erkennen.

Im Prinzip ist `IActionConstraint` eine Form der *Überladung*. Anstatt jedoch Methoden mit demselben Namen zu überladen, führt es zu einer Überladung zwischen Aktionen, die dieselbe URL erkennen. Beim Attributrouting wird auch `IActionConstraint` verwendet. Aus diesem Grund kann es zu Aktionen aus verschiedenen Controllern führen, die beide als Kandidaten gelten.

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>Implementieren von IActionConstraint

`IActionConstraint` lässt sich am einfachsten erstellen, indem Sie eine abgeleitete Klasse von `System.Attribute` erstellen und sie auf Ihren Aktionen und Controllern platzieren. MVC erkennt automatisch jedes `IActionConstraint`, das als Attribut verwendet wird. Sie können mithilfe des Anwendungsmodells Einschränkungen anwenden. Dies ist wahrscheinlich die flexibelste Herangehensweise, da Sie über Metaprogrammierung festlegen können, wie die Einschränkungen angewendet werden.

Im folgenden Beispiel wählt eine Einschränkung eine Aktion aus, die auf einem *Ländercode* aus den Routendaten basiert. [Das vollständige Beispiel finden Sie auf GitHub.](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

Sie sind für die Implementierung der `Accept`-Methode und die Festlegung einer Reihenfolge (Order) verantwortlich, in der die Einschränkung ausgeführt wird. In diesem Fall gibt die `Accept`-Methode `true` zurück, um zu kennzeichnen, dass die Aktion eine Übereinstimmung ist, wenn die `country`-Routenwerte übereinstimmen. Dies unterscheidet sich von `RouteValueAttribute` dahingehend, dass ein Fallback auf eine Aktion ohne Attribute zulässig ist. In dem Beispiel wird gezeigt, dass wenn Sie eine `en-US`-Aktion definieren, ein Ländercode wie `fr-FR` auf einen generischeren Controller zurückgreift, für den `[CountrySpecific(...)]` nicht angewendet wird.

Die `Order`-Eigenschaft entscheidet, zu welcher *Phase* die Einschränkung gehört. Aktionseinschränkungen werden auf Grundlage von `Order` in Gruppen ausgeführt. Beispiel: Alle vom Framework bereitgestellten HTTP-Methodenattribute verwenden denselben `Order`-Wert, sodass sie in derselben Phase ausgeführt werden können. Es kann so viele Phasen geben, wie zur Implementierung der gewünschten Richtlinien notwendig.

> [!TIP]
> Bei der Entscheidung über einen Wert für `Order` sollten Sie berücksichtigen, ob die Einschränkung vor HTTP-Methoden angewendet werden soll oder nicht. Niedrigere Zahlen werden zuerst ausgeführt.

::: moniker-end
