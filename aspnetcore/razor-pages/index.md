---
title: Einführung in Razor Pages in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Razor Pages in ASP.NET Core codierungsseitige Szenarios einfacher und produktiver gestalten als MVC.
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.date: 02/12/2020
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
uid: razor-pages/index
ms.openlocfilehash: 89e06d00e9312a428c4e164b0dc60527fe12d904
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430873"
---
# <a name="introduction-to-no-locrazor-pages-in-aspnet-core"></a>Einführung in Razor Pages in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)

Razor Pages kann im Vergleich zu Controllern und Ansichten das Programmieren seitenbasierter Anwendungen vereinfachen und die Produktivität erhöhen.

Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Dieses Dokument bietet eine Einführung in Razor Pages. Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial. Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start). Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Erstellen eines Razor Pages-Projekts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie `dotnet new webapp` über die Befehlszeile aus.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages ist in *Startup.cs* aktiviert:

[!code-csharp[](index/3.0sample/RazorPagesIntro/Startup.cs?name=snippet_Startup&highlight=12,36)]

Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a>

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index.cshtml?highlight=1)]

Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird. Der Unterschied besteht in der [`@page`](xref:mvc/views/razor#page)-Anweisung. `@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` wirkt sich auf das Verhalten aller anderen [Razor](xref:mvc/views/razor)-Konstrukte aus. Razor Pages-Dateinamen haben das Suffix *.cshtml*.

Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt. Die Datei *Pages/Index2.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml)]

Das Seitenmodell *Pages/Index2.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *.cs* angefügt wird. Die vorherige Datei mit Razor Razor lautet beispielsweise *Pages/Index2.cshtml*. Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.

Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt. Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:

| Dateiname und Pfad               | Entsprechende URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` oder `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` oder `/Store/Index` |

Notizen:

* Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit RRazor Pages.
* Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.

## <a name="write-a-basic-form"></a>Schreiben eines einfachen Formulars

Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können. Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden. Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:

Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/3.0sample/RazorPagesContacts/Startup.cs#L23-L24) initialisiert.

Für die In-Memory-Datenbank ist das NuGet-Paket `Microsoft.EntityFrameworkCore.InMemory` erforderlich.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Startup.cs?name=snippet)]

Das Datenmodell:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Der db-Kontext:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Data/CustomerDbContext.cs)]

Die Umgebungsdatei *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Das Seitenmodell *Pages/Create.cshtml.cs* :

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_ALL)]

Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.

Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden. Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten. Diese Trennung ermöglicht Folgendes:

* Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)
* [Komponententests](xref:test/razor-pages-tests)

Die Seite verfügt über eine `OnPostAsync`- *Handlermethode* , die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet). Für alle HTTP-Verben können Handlermethoden hinzugefügt werden. Die am häufigsten verwendeten Handler sind:

* `OnGet`, um den für eine Seite erforderlichen Status zu initialisieren. Im vorangehenden Code wird die Razor-Seite *CreateModel.cshtml* durch die `OnGet`-Methode dargestellt.
* `OnPost`, um Formularübermittlungen zu behandeln

Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet. Der vorhergehende Code ist typisch für Razor Pages.

Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:

* Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.
* Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden in Controllern und Razor Pages auf dieselbe Weise eingesetzt. 

Die vorherige `OnPostAsync`-Methode:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync)]

Der grundlegende Ablauf von `OnPostAsync`:

Prüfen auf Validierungsfehler

* Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.
* Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an. denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.

Die Umgebungsdatei *Pages/Create.cshtml* :

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml)]

Der von *Pages/Create.cshtml* gerenderte HTML-Code sieht wie folgt aus:

[!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.html)]

Im vorherigen Code gilt für die Formularübermittlung mittels POST Folgendes:

* Bei gültigen Daten:

  * Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*>-Hilfsmethode auf. `RedirectToPage` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RedirectToPageResult> zurück. `RedirectToPage`:

    * ist ein Aktionsergebnis.
    * ähnelt `RedirectToAction` oder `RedirectToRoute` (wird in Controllern und Ansichten verwendet).
    * ist an Seiten angepasst. Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter. Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).

* Bei Validierungsfehlern, die an den Server übermittelt werden:

  * Die `OnPostAsync`-Handlermethode ruft die <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageBase.Page*>-Hilfsmethode auf. `Page` gibt eine Instanz von <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> zurück. Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben. `PageResult` ist der Standardrückgabetyp für eine Handlermethode. Eine Handlermethode, die `void` zurückgibt, rendert die Seite.
  * Wenn im vorangehenden Beispiel das Formular mithilfe von POST übermittelt und dabei kein Wert angegeben wird, gibt [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) „false“ zurück. In diesem Beispiel werden keine Validierungsfehler auf dem Client angezeigt. Die Verarbeitung von Validierungsfehlern wird weiter unten in diesem Artikel behandelt.

  [!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

* Bei Validierungsfehlern, die durch eine clientseitige Validierung erkannt werden:

  * Die Daten werden **nicht** per POST an den Server gesendet.
  * Die clientseitige Validierung wird weiter unten in diesem Artikel erläutert.

Die Eigenschaft `Customer` verwendet das [`[BindProperty]`](xref:Microsoft.AspNetCore.Mvc.BindPropertyAttribute)-Attribut, um die Modellbindung zu aktivieren:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=15-16)]

`[BindProperty]` sollte **nicht** in Modellen mit Eigenschaften verwendet werden, die vom Client nicht geändert werden dürfen. Weitere Informationen finden Sie unter [Overposting](xref:data/ef-rp/crud#overposting).

Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben. Durch die Bindung an Eigenschaften entfällt das Schreiben von Code, mit dem HTTP-Daten in den Modelltyp konvertiert werden. Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.

[!INCLUDE[](~/includes/bind-get.md)]

Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml?highlight=3,9)]

* Im vorangehenden Code bindet das [Eingabetag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-input-tag-helper) `<input asp-for="Customer.Name" />` das HTML-Element `<input>` an den Modellausdruck `Customer.Name`.
* [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#addtaghelper-makes-tag-helpers-available) stellt Taghilfsprogramme zur Verfügung.

### <a name="the-home-page"></a>Die Homepage

*Index.cshtml* ist die Homepage:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml)]

Die zugeordnete `PageModel`-Klasse ( *Index.cshtml.cs* ):

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet)]

Die Datei *Index.cshtml* enthält das folgende Markup:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=21)]

Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a /a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren. Der Link enthält die Routendaten mit der Kontakt-ID. Beispielsweise `https://localhost:5001/Edit/1`. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.

Die Datei *Index.cshtml* enthält das Markup zum Erstellen der Schaltfläche „delete“ (Löschen) für jeden Kundenkontakt:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml?range=22-23)]

Der gerenderte HTML-Code sieht wie folgt aus:

```html
<button type="submit" formaction="/Customers?id=1&amp;handler=delete">delete</button>
```

Wenn die „delete“-Schaltfläche in HTML gerendert wird, enthält das zugehörige [formaction](https://developer.mozilla.org/docs/Web/HTML/Element/button#attr-formaction)-Element Parameter für Folgendes:

* die Kundenkontakt-ID, die durch das `asp-route-id`-Attribut angegeben wird
* den `handler`, der durch das `asp-page-handler`-Attribut angegeben wird

Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet. Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.

Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten. Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt.

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Index.cshtml.cs?name=snippet2)]

Die `OnPostDeleteAsync`-Methode:

* ruft die `id` der Abfragezeichenfolge ab.
* Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.
* Wenn der Kundenkontakt gefunden wird, wird er entfernt, und die Datenbank wird aktualisiert.
* Ruft <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.RedirectToPage*> auf, um die Stammindexseite (`/Index`) umzuleiten.

### <a name="the-editcshtml-file"></a>Die Datei „Edit.cshtml“

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml?highlight=1)]

Die erste Zeile enthält die `@page "{id:int}"`-Anweisung. Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten. Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

 ```cshtml
@page "{id:int?}"
```

Die Datei *Edit.cshtml.cs* sieht wie folgt aus:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Edit.cshtml.cs?name=snippet)]

## <a name="validation"></a>Validierung

Für Validierungsregeln gilt Folgendes:

* Sie werden deklarativ in der Modellklasse angegeben.
* Sie werden überall in der App erzwungen.

Der Namespace <xref:System.ComponentModel.DataAnnotations> stellt eine Gruppe integrierter Validierungsattribute bereit, die deklarativ auf eine Klasse oder Eigenschaft angewendet werden. „DataAnnotations“ enthält auch Formatierungsattribute wie [`[DataType]`](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute), die bei der Formatierung helfen und keinerlei Validierung bereitstellen.

Sehen Sie sich das `Customer`-Modell an:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Models/Customer.cs)]

Für die folgende Ansichtsdatei *Create.cshtml* gilt Folgendes:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=3,8-9,15-99)]

Der vorangehende Code:

* umfasst jQuery-Skripts einschließlich solcher zur Validierung.
* Verwendet die [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) `<div />` und `<span />`, um Folgendes zu ermöglichen:

  * clientseitiger Validierung.
  * Rendering von Validierungsfehlern.

* wird der folgende HTML-Code generiert:

  [!code-html[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create5.html)]

Wenn Sie das Formular „Create“ (Erstellen) ohne einen Wert für den Namen mit POST übermitteln, wird die Fehlermeldung „The Name field is required.“ (Für das Namensfeld muss ein Wert angegeben werden.) auf dem Formular angezeigt. Wenn JavaScript auf dem Client aktiviert ist, zeigt der Browser den Fehler an, ohne dass Daten per POST an den Server gesendet werden.

Das `[StringLength(10)]`-Attribut generiert `data-val-length-max="10"` für den gerenderten HTML-Code. `data-val-length-max` verhindert, dass im Browser ein Wert eingegeben wird, der die angegebene Maximallänge überschreitet. Wenn ein Tool wie [Fiddler](https://www.telerik.com/fiddler) zum Bearbeiten und erneuten Senden einer POST-Anforderung verwendet wird

* und die Länge des Namens 10 Zeichen überschreitet,
* geschieht Folgendes: Die Fehlermeldung „The field Name must be a string with a maximum length of 10.“ (Das Namensfeld muss eine Zeichenfolge mit maximal 10 Zeichen enthalten.) wird zurückgegeben.

Sehen Sie sich das folgende `Movie`-Modell an:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

Die Validierungsattribute geben das Verhalten an, das für die Modelleigenschaften erzwungen werden soll:

* Die Attribute `Required` und `MinimumLength` geben an, dass eine Eigenschaft einen Wert haben muss. Ein Benutzer kann allerdings ein Leerzeichen eingeben, um diese Anforderung zu erfüllen.
* Das Attribut `RegularExpression` wird verwendet, um einzuschränken, welche Zeichen eingegeben werden dürfen. Für „Genre“ im Code oben gilt Folgendes:

  * Es dürfen nur Buchstaben enthalten sein.
  * Der erste Buchstabe muss ein Großbuchstabe sein. Leerzeichen, Zahlen und Sonderzeichen sind nicht zulässig.

* Für `RegularExpression`-„Rating“ (Bewertung) gilt Folgendes:

  * Das erste Zeichen muss ein Großbuchstabe sein.
  * Sonderzeichen und Zahlen sind als darauffolgende Zeichen zulässig. „PG-13“ ist als Bewertung („Rating“) gültig, nicht jedoch als „Genre“.

* Das Attribut `Range` schränkt einen Wert auf einen bestimmten Bereich ein.
* Mit dem Attribut `StringLength` kann die maximale Länge einer Zeichenfolgeneigenschaft und optional die minimale Länge festlegt werden.
* Werttypen (wie `decimal`, `int`, `float`, `DateTime`) sind grundsätzlich erforderlich und benötigen nicht das Attribut `[Required]`.

Auf der Seite „Create“ (Erstellen) für das `Movie`-Modell werden ungültige Werte und die daraus resultierenden Fehler angezeigt:

![Ansichtsformular „Movie“ mit mehreren clientseitigen jQuery-Validierungsfehlern](~/tutorials/razor-pages/validation/_static/val.png)

Weitere Informationen finden Sie unter:

* [Hinzufügen von Validierungscode zur Movie-App](xref:tutorials/razor-pages/validation)
* [Modellvalidierung in ASP.NET Core](xref:mvc/models/validation)

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback

`HEAD`-Anforderungen ermöglichen das Abrufen der Header für eine bestimmte Ressource. Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.

Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Privacy.cshtml.cs?name=snippet)]

Razor Pages ruft den `OnGet`-Handler auf, wenn kein `OnHead`-Handler definiert ist.

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF und Razor Pages

Razor Pages wird durch [Validierungsmaßnahmen vor XSRF/CSRF-Angriffen](xref:security/anti-request-forgery) geschützt. Das [Formulartag-Hilfsprogramm](xref:mvc/views/working-with-forms#the-form-tag-helper) injiziert Anti-XSRF/CSRF-Token in HTML-Formularelemente.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages

Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine. Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* und *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.

Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.

Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Shared/_Layout2.cshtml?hightlight=12)]

Das [Layout](xref:mvc/views/layout):

* Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.
* Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.
* Der Inhalt der Razor-Seite wird gerendert, wenn `@RenderBody()` aufgerufen wird.

Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).

Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Das Layout befindet sich im Ordner *Pages/Shared*. Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite. Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.

Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.

Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren. *Views/Shared* ist ein MVC-Ansichtsmuster. Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.

Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*. Die Layouts, Vorlagen und Teilansichten, die mit MVC-Controllern und herkömmlichen Razor-Ansichten verwendet werden, *funktionieren problemlos*.

Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` wird weiter unten im Tutorial erläutert. Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.

<a name="namespace"></a>

Die `@namespace`-Anweisung wird wie folgt für eine Seite festgelegt:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Die `@namespace`-Anweisung legt den Namespace für die Seite fest. Die `@model`-Anweisung muss den Namespace nicht enthalten.

Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert. Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.

Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.

`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*

Sehen Sie sich die Ansichtsdatei *Pages/Create.cshtml* an:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create3.cshtml?highlight=2-3)]

Die aktualisierte Ansichtsdatei *Pages/Create.cshtml* mit *_ViewImports.cshtml* und der vorherigen Layoutdatei sieht wie folgt aus:

[!code-cshtml[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create4.cshtml?highlight=2)]

Im vorangehenden Code werden von *_ViewImports.cshtml* der Namespace und die Taghilfsprogramme importiert. Die JavaScript-Dateien werden von der Layoutdatei importiert.

Das [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml* , die die clientseitige Validierung bindet.

Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>URL-Generierung für Seiten

Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:

[!code-csharp[](index/3.0sample/RazorPagesContacts/Pages/Customers/Create.cshtml.cs?name=snippet_PageModel&highlight=28)]

Die App hat die folgende Datei/Ordner-Struktur:

* */Pages*

  * *Index.cshtml*
  * *Privacy.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* führen bei Erfolg eine Umleitung zu *Pages/Customers/Index.cshtml* durch. Die Zeichenfolge `./Index` stellt einen relativen Seitennamen dar, der für den Zugriff auf die vorherige Seite verwendet wird. Sie wird für das Generieren von URIs für die Seite *Pages/Customers/Index.cshtml* verwendet. Zum Beispiel:

* `Url.Page("./Index", ...)`
* `<a asp-page="./Index">Customers Index Page</a>`
* `RedirectToPage("./Index")`

Der absolute Seitenname `/Index` wird zum Generieren der URLs für die Seite *Pages/Index.cshtml* verwendet. Zum Beispiel:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">Home Index Page</a>`
* `RedirectToPage("/Index")`

Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`). Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen. Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.

Die URL-Generierung für Seiten unterstützt relative Namen. In der folgenden Tabelle wird dargestellt, welche Indexseite durch verschiedene `RedirectToPage`-Parameter in *Pages/Customers/Create.cshtml* ausgewählt wird.

| RedirectToPage(x)| Seite |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

<!-- Test via ~/razor-pages/index/3.0sample/RazorPagesContacts/Pages/Customers/Details.cshtml.cs -->

`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*. Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert* , um den Namen der Zielseite zu berechnen.

Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur. Wenn durch relative Namen Seiten in einem Ordner verknüpft werden, hat das folgende Vorteile:

* Relative Links funktionieren weiterhin, wenn ein Ordner umbenannt wird.
* Links funktionieren weiterhin, da sie keinen Ordnernamen enthalten.

Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Weitere Informationen finden Sie unter <xref:mvc/controllers/areas> und <xref:razor-pages/razor-pages-conventions>.

## <a name="viewdata-attribute"></a>Attribut „ViewData“

Daten können mit <xref:Microsoft.AspNetCore.Mvc.ViewDataAttribute> an eine Seite übermittelt werden. Für Eigenschaften mit dem `[ViewData]`-Attribut werden die Werte in <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> gespeichert und daraus geladen.

Im folgenden Beispiel wendet `AboutModel` das `[ViewData]`-Attribut auf die `Title`-Eigenschaft an:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:

```cshtml
<h1>@Model.Title</h1>
```

Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core macht <xref:Microsoft.AspNetCore.Mvc.Controller.TempData> verfügbar. Diese Eigenschaft speichert Daten, bis sie gelesen wurden. Die Methoden <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Keep*> und <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.TempDataDictionary.Peek*> können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen. `TempData` eignet sich für die Umleitung, wenn Daten für mehr als eine Anforderung benötigt werden.

Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.

```csharp
[TempData]
public string Message { get; set; }
```

Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Mehrere Handler pro Seite

Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln. Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`. `asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden. `asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.

Das Seitenmodell:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Der vorherige Code verwendet *benannte Handlermethoden*. Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt. Im vorherigen Beispiel sind OnPost **JoinList** Async und OnPost **JoinListUC** Async die Seitenmethoden. Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Benutzerdefinierte Routen

Verwenden Sie die `@page`-Anweisung für Folgendes:

* Das Angeben einer benutzerdefinierten Route zu einer Seite. Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.
* Das Anfügen von Segmenten an die Standardroute einer Seite. Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.
* Das Anfügen von Parametern an die Standardroute einer Seite. Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.

Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird. `@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.

Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird. Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`. Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.

## <a name="advanced-configuration-and-settings"></a>Erweiterte Konfigurationen und Einstellungen

Die Konfigurationen und Einstellungen in den folgenden Abschnitten sind für die meisten Apps nicht erforderlich.

Verwenden Sie die Überladung <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages%2A>, die <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions> konfiguriert, um die erweiterten Optionen zu konfigurieren:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupRPoptions.cs?name=snippet)]

Verwenden Sie <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions>, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen. Weitere Informationen zu Konventionen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization).

Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Festlegen des Inhaltsstammverzeichnisses für Razor Pages

Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*. Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.WithRazorPagesAtContentRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) (<xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.ContentRootPath>) der App befinden:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesAtContentRoot.cs?name=snippet)]

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages

Fügen Sie <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcCoreBuilderExtensions.WithRazorPagesRoot*> hinzu, um anzugeben, dass sich Ihre Razor-Seiten in einem benutzerdefinierten Stammverzeichnis der App befinden. Geben Sie dabei einen relativen Pfad an:

[!code-csharp[](index/3.0sample/RazorPagesContacts/StartupWithRazorPagesRoot.cs?name=snippet)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.
* [Authorize-Attribut und Razor Pages](xref:security/authorization/simple#aarp)
* [Herunterladen und Anzeigen des Beispielcodes](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/3.0sample)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>
* <xref:blazor/components/prerendering-and-integration>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Ryan Nowak](https://github.com/rynowak)

Razor Pages ist ein neuer Bestandteil von ASP.NET Core MVC, mit dem codierungsseitige Szenarios einfacher und produktiver werden.

Ein Tutorial, in dem der Model-View-Controller-Ansatz verwendet wird, finden Sie unter [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Dieses Dokument bietet eine Einführung in Razor Pages. Es handelt sich nicht um ein Schritt-für-Schritt-Tutorial. Wenn es Ihnen Probleme bereitet, die Ausführungen in einigen Abschnitten nachzuvollziehen, lesen Sie [Erste Schritte mit Razor Pages in ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start). Eine Übersicht über ASP.NET Core finden Sie unter [Einführung in ASP.NET Core](xref:index).

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

<a name="rpvs17"></a>

## <a name="create-a-no-locrazor-pages-project"></a>Erstellen eines Razor Pages-Projekts

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ausführliche Informationen zum Erstellen eines Razor Pages-Projekts finden Sie unter [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start).

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

Führen Sie `dotnet new webapp` über die Befehlszeile aus.

Öffnen Sie die generierte Datei *.csproj* aus Visual Studio für Mac.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie `dotnet new webapp` über die Befehlszeile aus.

---

## <a name="no-locrazor-pages"></a>Razor Pages

Razor Pages ist in *Startup.cs* aktiviert:

[!code-csharp[](index/sample/RazorPagesIntro/Startup.cs?name=snippet_Startup)]

Sehen Sie sich diese einfache Seite an: <a name="OnGet"></a>

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index.cshtml)]

Der vorherige Code ähnelt sehr einer [Razor-Ansichtsdatei](xref:tutorials/first-mvc-app/adding-view), die in einer ASP.NET Core-App mit Controllern und Ansichten verwendet wird. Der Unterschied besteht in der `@page`-Anweisung. `@page` macht die Datei zu einer MVC-Aktion, d.h. dass Anfragen direkt ohne einen Controller verarbeitet werden. `@page` muss die erste Razor-Anweisung auf einer Seite sein. `@page` wirkt sich auf das Verhalten aller anderen Razor-Konstrukte aus.

Eine ähnliche Seite, die die `PageModel`-Klasse verwendet, wird in den folgenden zwei Dateien angezeigt. Die Datei *Pages/Index2.cshtml* :

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Index2.cshtml)]

Das Seitenmodell *Pages/Index2.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesIntro/Pages/Index2.cshtml.cs)]

Die `PageModel`-Klassendatei hat standardmäßig den gleichen Namen wie die Datei mit Razor Pages, nur dass außerdem *.cs* angefügt wird. Die vorherige Datei mit Razor Razor lautet beispielsweise *Pages/Index2.cshtml*. Die Datei mit der `PageModel`-Klasse heißt *Pages/Index2.cshtml.cs*.

Die Zuordnungen von URL-Pfaden zu Seiten werden durch den Speicherort der Seite im Dateisystem bestimmt. Die folgende Tabelle zeigt einen Pfad zu Razor Pages und die entsprechende URL:

| Dateiname und Pfad               | Entsprechende URL |
| ----------------- | ------------ |
| */Pages/Index.cshtml* | `/` oder `/Index` |
| */Pages/Contact.cshtml* | `/Contact` |
| */Pages/Store/Contact.cshtml* | `/Store/Contact` |
| */Pages/Store/Index.cshtml* | `/Store` oder `/Store/Index` |

Notizen:

* Die Runtime sucht standardmäßig im Ordner *Pages* (Seiten) nach Dateien mit RRazor Pages.
* Wenn eine Seite nicht in einer URL enthalten ist, ist `Index` die Standardseite.

## <a name="write-a-basic-form"></a>Schreiben eines einfachen Formulars

Razor Pages ist darauf ausgelegt, allgemeine Muster, die mit Webbrowsern verwendet werden können, beim Erstellen einer App leichter implementieren zu können. Die [Modellbindung](xref:mvc/models/model-binding), [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) und alle HTML-Hilfsprogramme *funktionieren nur* mit den Eigenschaften, die in einer Klasse der Razor Pages definiert wurden. Nehmen wir z.B. eine Seite, die ein allgemeines Kontaktformular für das `Contact`-Modell implementiert:

Für die Beispiele in diesem Dokument wird `DbContext` in der Datei [Startup.cs](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/razor-pages/index/sample/RazorPagesContacts/Startup.cs#L15-L16) initialisiert.

[!code-csharp[](index/sample/RazorPagesContacts/Startup.cs?highlight=15-16)]

Das Datenmodell:

[!code-csharp[](index/sample/RazorPagesContacts/Data/Customer.cs)]

Der db-Kontext:

[!code-csharp[](index/sample/RazorPagesContacts/Data/AppDbContext.cs)]

Die Umgebungsdatei *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml)]

Das Seitenmodell *Pages/Create.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_ALL)]

Die `PageModel` -Klasse heißt standardmäßig `<PageName>Model` und befindet sich im selben Namespace wie die Seite.

Mit der Klasse `PageModel` kann die Logik einer Seite von deren Darstellung getrennt werden. Sie definiert Seitenhandler für Anforderungen, die an die Seite geschickt wurden, und für zum Rendern der Seite verwendete Daten. Diese Trennung ermöglicht Folgendes:

* Verwalten von Seitenabhängigkeiten mithilfe von [Dependency Injection](xref:fundamentals/dependency-injection)
* [Komponententests](xref:test/razor-pages-tests) für Seiten

Die Seite verfügt über eine `OnPostAsync`- *Handlermethode* , die bei `POST`-Anforderungen ausgeführt wird (wenn ein Benutzer das Formular sendet). Sie können Handlermethoden für alle HTTP-Verben hinzufügen. Die am häufigsten verwendeten Handler sind:

* `OnGet`, um den für eine Seite erforderlichen Status zu initialisieren. [OnGet](#OnGet)-Beispiel
* `OnPost`, um Formularübermittlungen zu behandeln

Das Namenssuffix `Async` ist optional. Es wird jedoch standardmäßig häufig für asynchrone Funktionen verwendet. Der vorhergehende Code ist typisch für Razor Pages.

Wenn Sie mit ASP.NET-Apps vertraut sind, die Controller und Ansichten verwenden, werden Ihnen folgende Fakten bekannt vorkommen:

* Der `OnPostAsync`-Code im vorangehenden Beispiel ähnelt dem typischen Controllercode.
* Die meisten primitiven MVC-Typen wie solche für [Modellbindungen](xref:mvc/models/model-binding), [Validierungen](xref:mvc/models/validation), [Validierungen](xref:mvc/models/validation) und Aktionsergebnisse werden gemeinsam verwendet.

Die vorherige `OnPostAsync`-Methode:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync)]

Der grundlegende Ablauf von `OnPostAsync`:

Prüfen auf Validierungsfehler

* Wenn keine Fehler vorliegen, werden die Daten gespeichert und weitergeleitet.
* Wenn es Fehler gibt, zeigen Sie die Seite erneut mit den Validierungsmeldungen an. Die clientseitige Validierung ist identisch mit herkömmlichen ASP.NET Core MVC-Anwendungen, denn Validierungsfehler werden oftmals auf dem Client erkannt und nie an den Server übermittelt.

Wenn die Daten erfolgreich eingegeben wurden, ruft die `OnPostAsync`-Handlermethode die `RedirectToPage`-Hilfsmethode auf, um eine Instanz von `RedirectToPageResult` zurückzugeben. `RedirectToPage` ist ein neues Aktionsergebnis und ähnelt `RedirectToAction` oder `RedirectToRoute`, ist aber für Seiten angepasst. Im vorhergehenden Beispiel leitet es an die Stammindexseite (`/Index`) weiter. Informationen zu `RedirectToPage` finden Sie im Abschnitt [URL-Generierung für Seiten](#url_gen).

Wenn das übermittelte Formular Validierungsfehler enthält (die an den Server übergeben wurden), ruft die `OnPostAsync`-Handlermethode die `Page`-Hilfsmethode auf. `Page` gibt eine Instanz von `PageResult` zurück. Der Vorgang, bei dem `Page` zurückgegeben wird, ähnelt dem Vorgang, bei dem Aktionen im Controller `View` zurückgeben. `PageResult` ist der Standardrückgabetyp für eine Handlermethode. Eine Handlermethode, die `void` zurückgibt, rendert die Seite.

Die Eigenschaft `Customer` verwendet das `[BindProperty]`-Attribut, um die Modellbindung zu aktivieren.

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_PageModel&highlight=10-11)]

Razor Pages binden Eigenschaften standardmäßig nur an Nicht-`GET`-Verben. Durch die Bindung an Eigenschaften können Sie den Umfang von Codes reduzieren, den Sie schreiben müssen. Die Bindung reduziert den Code mithilfe der gleichen Eigenschaft, um Formularfelder (`<input asp-for="Customer.Name">`) zu rendern und die Eingabe zu akzeptieren.

[!INCLUDE[](~/includes/bind-get.md)]

Die Startseite ( *Index.cshtml* ):

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml)]

Die zugeordnete `PageModel`-Klasse ( *Index.cshtml.cs* ):

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs)]

Die Datei *Index.cshtml* enthält das folgende Markup, um einen Bearbeitungslink für jeden Kontakt zu erstellen:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=21)]

Das [Anchor-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `<a asp-page="./Edit" asp-route-id="@contact.Id">Edit</a>` verwendet das `asp-route-{value}`-Attribut, um einen Link zur Bearbeitungsseite zu generieren. Der Link enthält die Routendaten mit der Kontakt-ID. Beispielsweise `https://localhost:5001/Edit/1`. [Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien. Taghilfsprogramme werden durch `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` aktiviert.

Die Datei *Pages/Edit.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Edit.cshtml?highlight=1)]

Die erste Zeile enthält die `@page "{id:int}"`-Anweisung. Die Routingbeschränkung `"{id:int}"` weist die Seite an, die Anforderungen für die Seite zu akzeptieren, die `int`-Routingdaten enthalten. Wenn eine Anforderung an die Seite bestimmte Routingdaten nicht enthält, die in einen `int` konvertiert werden können, gibt die Runtime einen Fehler vom Typ „HTTP 404: Nicht gefunden“ zurück. Um die ID optional zu machen, fügen Sie `?` an die Routeneinschränkung an:

 ```cshtml
@page "{id:int?}"
```

Die Datei *Pages/Edit.cshtml.cs* :

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Edit.cshtml.cs)]

Die Datei *index.cshtml* enthält auch Markup zum Erstellen der Schaltfläche „Löschen“ für jeden benutzerdefinierten Kontakt:

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Index.cshtml?range=22-23)]

Wenn die „Löschen“-Schaltfläche in HTML gerendert wird, enthält ihr `formaction`-Element Parameter für Folgendes:

* Die benutzerdefinierte Kontakt-ID, die vom `asp-route-id`-Attribut angegeben wird
* Der `handler`, der vom `asp-page-handler`-Attribut angegeben wird

Hier sehen Sie ein Beispiel für eine gerenderte „Löschen“-Schaltfläche mit einer benutzerdefinierten Kontakt-ID von `1`:

```html
<button type="submit" formaction="/?id=1&amp;handler=delete">delete</button>
```

Wenn die Schaltfläche ausgewählt wird, wird eine `POST`-Anforderung an den Server gesendet. Durch Konvention wird der Name der Handlermethode auf Grundlage des Werts des `handler`-Parameters gemäß dem Schema `OnPost[handler]Async` ausgewählt.

Da der `handler` in diesem Beispiel `delete` ist, wird die Handlermethode `OnPostDeleteAsync` verwendet, um die `POST`-Anforderung zu verarbeiten. Wenn `asp-page-handler` auf einen anderen Wert (z. B. `remove`) festgelegt wird, wird eine Handlermethode namens `OnPostRemoveAsync` ausgewählt. Im folgenden Code wird der `OnPostDeleteAsync`-Handler definiert:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Index.cshtml.cs?range=26-37)]

Die `OnPostDeleteAsync`-Methode:

* Akzeptiert die `id` der Abfragezeichenfolge. Wenn die Anweisung der Seite *Index.cshtml* die Routingeinschränkung `"{id:int?}"` enthielte, würde `id` aus den Routendaten abgerufen werden. Die Routendaten für `id` werden beispielsweise wie folgt im URI angegeben: `https://localhost:5001/Customers/2`.
* Fragt mit `FindAsync` die Datenbank nach dem Kundenkontakt ab.
* Wenn der Kundenkontakt gefunden wird, wird er aus der Liste der Kundenkontakte entfernt. Die Datenbank wurde aktualisiert.
* Ruft `RedirectToPage` auf, um die Stammindexseite (`/Index`) umzuleiten.

## <a name="mark-page-properties-as-required"></a>Markieren von Eigenschaften als „Required“ (Erforderlich)

Eigenschaften in einem `PageModel` können mit dem [Required](/dotnet/api/system.componentmodel.dataannotations.requiredattribute)-Attribut markiert werden:

[!code-csharp[](index/sample/Create.cshtml.cs?highlight=3,15-16)]

Weitere Informationen finden Sie unter [Modellvalidierung](xref:mvc/models/validation).

## <a name="handle-head-requests-with-an-onget-handler-fallback"></a>Verarbeiten von HEAD-Anforderungen mit einem OnGet-Handlerfallback

`HEAD`-Anforderungen ermöglichen Ihnen das Abrufen des Headers für eine bestimmte Ressource. Im Gegensatz zu `GET`-Anforderungen geben `HEAD`-Anforderungen keinen Antworttext zurück.

Normalerweise wird ein `OnHead`-Handler erstellt und für `HEAD`-Anforderungen aufgerufen: 

```csharp
public void OnHead()
{
    HttpContext.Response.Headers.Add("HandledBy", "Handled by OnHead!");
}
```

In ASP.NET Core 2.1 oder höher greift Razor Pages auf den `OnGet`-Handler zurück, wenn kein `OnHead`-Handler definiert ist. Dieses Verhalten wird durch den Aufruf von [SetCompatibilityVersion](xref:mvc/compatibility-version) in `Startup.ConfigureServices` ermöglicht:

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

Die Standardvorlagen generieren den `SetCompatibilityVersion`-Aufruf in ASP.NET Core 2.1 und 2.2. Tatsächlich legt `SetCompatibilityVersion` die Razor Pages-Option `AllowMappingHeadRequestsToGetHandler` auf `true` fest.

Sie müssen nicht alle Verhalten in `SetCompatibilityVersion` aktivieren, sondern können sich nur *bestimmte* Verhalten aussuchen. Der folgende Code gibt an, dass `HEAD`-Anforderungen dem `OnGet`-Handler zugeordnet werden dürfen:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        options.AllowMappingHeadRequestsToGetHandler = true;
    });
```

<a name="xsrf"></a>

## <a name="xsrfcsrf-and-no-locrazor-pages"></a>XSRF/CSRF und Razor Pages

Sie müssen keinen Code für die [Antifälschungsvalidierung](xref:security/anti-request-forgery) schreiben. Die Generierung und Validierung von Antifälschungstoken ist automatisch in Razor Pages enthalten.

<a name="layout"></a>

## <a name="using-layouts-partials-templates-and-tag-helpers-with-no-locrazor-pages"></a>Verwenden von Layouts, Teilansichten, Vorlagen und Taghilfsprogrammen mit Razor Pages

Razor Pages beinhaltet alle Funktionen der Razor-Anzeige-Engine. Layouts, Teilansichten, Vorlagen, Taghilfsprogramme, *_ViewStart.cshtml* , *_ViewImports.cshtml* funktionieren auf die gleiche Weise wie für herkömmliche Razor-Ansichten.

Strukturieren Sie diese Seite mit einigen dieser praktischen Funktionen.

Fügen Sie der *Pages/Shared/_Layout.cshtml* eine [Layoutseite](xref:mvc/views/layout) hinzu:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_LayoutSimple.cshtml)]

Das [Layout](xref:mvc/views/layout):

* Steuert das Layout der einzelnen Seiten, es sei denn, das Layout wird für eine Seite deaktiviert.
* Importiert HTML-Strukturen, z.B. JavaScript und Stylesheets.

Weitere Informationen finden Sie unter [Layoutseite](xref:mvc/views/layout).

Die Eigenschaft [Layout](xref:mvc/views/layout#specifying-a-layout) wird in *Pages/_ViewStart.cshtml* festgelegt:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewStart.cshtml)]

Das Layout befindet sich im Ordner *Pages/Shared*. Seiten suchen hierarchisch nach anderen Ansichten (Layouts, Vorlagen oder Teilansichten) und beginnen im gleichen Ordner wie die aktuelle Seite. Ein Layout im Ordner *Pages/Shared* kann von jeder Razor-Seite aus unter dem Ordner *Pages* verwendet werden.

Die Layoutdatei sollte im Ordner *Pages/Shared* gespeichert werden.

Wir empfehlen Ihnen, die Layoutdatei **nicht** im Ordner *Views/Shared* (Ansichten/Freigegeben) zu platzieren. *Views/Shared* ist ein MVC-Ansichtsmuster. Razor Pages basieren auf der Ordnerhierarchie, nicht auf Pfadkonventionen.

Die Ansichtensuche in einer Razor Page enthält den Ordner *Pages*. Die Layouts, Vorlagen und Teilansichten, die Sie mit MVC-Controllern und herkömmlichen Razor-Ansichten verwenden, *funktionieren einfach*.

Fügen Sie eine Datei *Pages/_ViewImports.cshtml* hinzu:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml)]

`@namespace` wird weiter unten im Tutorial erläutert. Die `@addTagHelper`-Anweisung bringt die [integrierten Taghilfsprogramme](xref:mvc/views/tag-helpers/builtin-th/Index) zu allen Seiten in der Ordner *Pages*.

<a name="namespace"></a>

Wenn die `@namespace`-Anweisung explizit auf eine Seite angewendet wird:

[!code-cshtml[](index/sample/RazorPagesIntro/Pages/Customers/Namespace2.cshtml?highlight=2)]

Die Anweisung legt den Namespace für die Seite fest. Die `@model`-Anweisung muss den Namespace nicht enthalten.

Wenn sich die `@namespace`-Anweisung in *_ViewImports.cshtml* befindet, stellt der angegebene Namespace das Präfix für den generierten Namespace auf der Seite bereit, die die `@namespace`-Anweisung importiert. Der Rest der generierten Namespaces (der Suffixteil) ist der durch Punkte getrennte relative Pfad zwischen dem Ordner mit *_ViewImports.cshtml* und dem Ordner, der die Seite enthält.

Die `PageModel`-Klasse in *Pages/Customers/Edit.cshtml.cs* legt den Namespace z.B. explizit fest:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/Edit.cshtml.cs?name=snippet_namespace)]

Die Datei *Pages/_ViewImports.cshtml* legt den folgenden Namespace fest:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/_ViewImports.cshtml?highlight=1)]

Der generierte Namespace für die Razor Page *Pages/Customers/Edit.cshtml* ist identisch mit der `PageModel`-Klasse.

`@namespace` *funktioniert auch mit konventionellen Razor-Ansichten.*

Die ursprüngliche Umgebungsdatei *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts/Pages/Create.cshtml?highlight=2)]

Die aktualisierte Umgebungsdatei *Pages/Create.cshtml* :

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/Create.cshtml?highlight=2)]

Das [Razor Pages-Startprojekt](#rpvs17) enthält die Seite *Pages/_ValidationScriptsPartial.cshtml* , die die clientseitige Validierung bindet.

Weitere Informationen zu Teilansichten finden Sie unter <xref:mvc/views/partial>.

<a name="url_gen"></a>

## <a name="url-generation-for-pages"></a>URL-Generierung für Seiten

Die zuvor gezeigte `Create`-Seite verwendet `RedirectToPage`:

[!code-csharp[](index/sample/RazorPagesContacts/Pages/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=10)]

Die App hat die folgende Datei/Ordner-Struktur:

* */Pages*

  * *Index.cshtml*
  * */Customers*

    * *Create.cshtml*
    * *Edit.cshtml*
    * *Index.cshtml*

Die Seiten *Pages/Customers/Create.cshtml* und *Pages/Customers/Edit.cshtml* leiten bei Erfolg an *Pages/Index.cshtml* weiter. Die Zeichenfolge `/Index` ist Teil des URI, der auf die vorhergehende Seite zugreifen soll. Die Zeichenfolge `/Index` kann für das Generieren von URIs für die Seite *Pages/Index.cshtml* verwendet werden. Zum Beispiel:

* `Url.Page("/Index", ...)`
* `<a asp-page="/Index">My Index Page</a>`
* `RedirectToPage("/Index")`

Der Seitenname ist der Pfad zu der Seite vom Stammordner */Pages* (einschließlich eines vorangestellten `/`, z.B. `/Index`). Die oben stehenden Beispiele für eine URL-Generierung bieten erweiterte Optionen und Funktionen, durch die Sie URLs nicht mehr hartcodieren müssen. Bei der URL-Generierung wird [Routing](xref:mvc/controllers/routing) verwendet. Außerdem können damit Parameter generiert und entsprechend der Definition der Route im Zielpfad codiert werden.

Die URL-Generierung für Seiten unterstützt relative Namen. In der folgenden Tabelle wird dargestellt, welche Indexseite für verschiedene `RedirectToPage`-Parameter aus *Pages/Customers/Create.cshtml* ausgewählt wird:

| RedirectToPage(x)| Seite |
| ----------------- | ------------ |
| RedirectToPage("/Index") | *Pages/Index* |
| RedirectToPage("./Index"); | *Pages/Customers/Index* |
| RedirectToPage("../Index") | *Pages/Index* |
| RedirectToPage("Index")  | *Pages/Customers/Index* |

`RedirectToPage("Index")`, `RedirectToPage("./Index")` und `RedirectToPage("../Index")` sind *relative Namen*. Der `RedirectToPage`-Parameter wird mit dem Pfad der aktuellen Seite *kombiniert* , um den Namen der Zielseite zu berechnen.  <!-- Review: Original had The provided string is combined with the page name of the current page to compute the name of the destination page.  page name, not page path -->

Das Verknüpfen relativer Namen eignet sich beim Erstellen von Websites mit einer komplexen Struktur. Wenn Sie relative Namen verwenden, um Seiten in einem Ordner zu verknüpfen, können Sie diesen Ordner umbenennen. Alle Links funktionieren weiterhin, da sie nicht den Namen des Ordners enthalten.

Um auf eine Seite in einem anderen [Bereich](xref:mvc/controllers/areas) umzuleiten, geben Sie den Bereich an:

```csharp
RedirectToPage("/Index", new { area = "Services" });
```

Weitere Informationen finden Sie unter <xref:mvc/controllers/areas>.

## <a name="viewdata-attribute"></a>Attribut „ViewData“

Daten können mit [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute) an eine Seite übergeben werden. Die Werte der Eigenschaften auf Controllern oder Razor Pages-Modellen, die mit dem `[ViewData]`-Attribut versehen sind, werden in [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) gespeichert und daraus geladen.

Im folgenden Beispiel enthält das `AboutModel` eine `Title`-Eigenschaft, die mit `[ViewData]` markiert ist. Die Eigenschaft `Title` wird auf den Titel der Infoseite festgelegt:

```csharp
public class AboutModel : PageModel
{
    [ViewData]
    public string Title { get; } = "About";

    public void OnGet()
    {
    }
}
```

Greifen Sie auf der Infoseite auf die Eigenschaft `Title` als Modelleigenschaft zu:

```cshtml
<h1>@Model.Title</h1>
```

Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

## <a name="tempdata"></a>TempData

ASP.NET Core macht die Eigenschaft [TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Controller_TempData) auf einem [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) verfügbar. Diese Eigenschaft speichert Daten, bis sie gelesen wurden. Die Methoden `Keep` und `Peek` können verwendet werden, um die Daten zu überprüfen, ohne sie zu löschen. `TempData` eignet sich für die Weiterleitung, wenn Daten für mehr als eine Anforderung benötigt werden.

Im folgenden Code wird der Wert von `Message` mit `TempData` festgelegt:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateDot.cshtml.cs?highlight=10-11,25&name=snippet_Temp)]

Das folgende Markup in der Datei *Pages/Customers/Index.cshtml* zeigt den Wert von `Message` mit `TempData` an.

```cshtml
<h3>Msg: @Model.Message</h3>
```

Das Seitenmodell *Pages/Customers/Index.cshtml.cs* wendet das `[TempData]`-Attribut auf die Eigenschaft `Message` an.

```csharp
[TempData]
public string Message { get; set; }
```

Weitere Informationen finden Sie unter [TempData](xref:fundamentals/app-state#tempdata).

<a name="mhpp"></a>

## <a name="multiple-handlers-per-page"></a>Mehrere Handler pro Seite

Die folgende Seite generiert mit dem `asp-page-handler`-Taghilfsprogramm Markup für zwei Handler:

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?highlight=12-13)]

<!-- Review: the FormActionTagHelper applies to all <form /> elements on a Razor page, even when there's no `asp-` attribute   -->

Das Formular im vorherigen Beispiel hat zwei Sendeschaltflächen, und jede verwendet `FormActionTagHelper`, um an eine andere URL zu übermitteln. Das `asp-page-handler`-Attribut ist eine Ergänzung für `asp-page`. `asp-page-handler` generiert URLs, die als Übermittlungsziel jeweils die durch eine Seite festgelegte Handlermethode verwenden. `asp-page` wird nicht angegeben, weil das Beispiel mit der aktuellen Seite verknüpft.

Das Seitenmodell:

[!code-csharp[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml.cs?highlight=20,32)]

Der vorherige Code verwendet *benannte Handlermethoden*. Benannte Handlermethoden werden aus dem Text im Namen nach `On<HTTP Verb>` und vor `Async` (falls vorhanden) erstellt. Im vorherigen Beispiel sind OnPost **JoinList** Async und OnPost **JoinListUC** Async die Seitenmethoden. Wenn Sie *OnPost* und *Async* entfernen, lauten die Handlernamen `JoinList` und `JoinListUC`.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateFATH.cshtml?range=12-13)]

Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH?handler=JoinList`. Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH?handler=JoinListUC`.

## <a name="custom-routes"></a>Benutzerdefinierte Routen

Verwenden Sie die `@page`-Anweisung für Folgendes:

* Das Angeben einer benutzerdefinierten Route zu einer Seite. Die Route zur Seite „Info“ kann mit `@page "/Some/Other/Path"` beispielsweise auf `/Some/Other/Path` festgelegt werden.
* Das Anfügen von Segmenten an die Standardroute einer Seite. Mit `@page "item"` kann beispielsweise ein item-Segment an die Standardroute der Seite angefügt werden.
* Das Anfügen von Parametern an die Standardroute einer Seite. Mit `@page "{id}"` kann beispielsweise ein ID-Parameter (`id`) für eine Seite angefordert werden.

Es wird ein relativer Pfad zum Stamm unterstützt, der durch eine Tilde (`~`) festgelegt wird. `@page "~/Some/Other/Path"` entspricht beispielsweise `@page "/Some/Other/Path"`.

Wenn Sie nicht möchten, dass die Abfragezeichenfolge `?handler=JoinList` in der URL enthalten ist, ändern Sie die Route so, dass der Handlername im Pfadteil der URL eingefügt wird. Sie können die Route anpassen, indem Sie nach der `@page`-Anweisung eine Routenvorlage in doppelten Anführungszeichen hinzufügen.

[!code-cshtml[](index/sample/RazorPagesContacts2/Pages/Customers/CreateRoute.cshtml?highlight=1)]

Mit dem vorherigen Code lautet der URL-Pfad, der an `OnPostJoinListAsync` übermittelt, `https://localhost:5001/Customers/CreateFATH/JoinList`. Der URL-Pfad, der an `OnPostJoinListUCAsync` übermittelt, lautet `https://localhost:5001/Customers/CreateFATH/JoinListUC`.

Das `?` nach `handler` bedeutet, dass der Routenparameter optional ist.

## <a name="configuration-and-settings"></a>Konfiguration und Einstellungen

Um die erweiterten Optionen zu konfigurieren, verwenden Sie die Erweiterungsmethode `AddRazorPagesOptions` auf dem MVC-Generator:

[!code-csharp[](index/sample/RazorPagesContacts/StartupAdvanced.cs?name=snippet_1)]

Derzeit können Sie `RazorPagesOptions` verwenden, um das Stammverzeichnis für Seiten festzulegen oder Anwendungsmodellkonventionen für Seiten hinzuzufügen. Auf diese Weise wird in Zukunft eine höhere Erweiterbarkeit erreicht.

Informationen zum Vorkompilieren von Ansichten finden Sie unter [Razor-Ansichtenkompilierung](xref:mvc/views/view-compilation).

[Laden Sie Beispielcode herunter, oder zeigen Sie ihn an](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/index/sample).

Lesen Sie auch den Artikel [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start), der auf dieser Einführung aufbaut.

### <a name="specify-that-no-locrazor-pages-are-at-the-content-root"></a>Festlegen des Inhaltsstammverzeichnisses für Razor Pages

Standardmäßig lautet das Stammverzeichnis für Razor Pages */Pages*. Fügen Sie [ WithRazorPagesAtContentRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvcbuilderextensions.withrazorpagesatcontentroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass sich Ihre Razor Pages im [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) ([ContentRootPath](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.contentrootpath)) der App befinden:

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesAtContentRoot();
```

### <a name="specify-that-no-locrazor-pages-are-at-a-custom-root-directory"></a>Festlegen eines benutzerdefinierten Stammverzeichnisses für Razor Pages

Fügen Sie [WithRazorPagesRoot](/dotnet/api/microsoft.extensions.dependencyinjection.mvcrazorpagesmvccorebuilderextensions.withrazorpagesroot) zu [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc#Microsoft_Extensions_DependencyInjection_MvcServiceCollectionExtensions_AddMvc_Microsoft_Extensions_DependencyInjection_IServiceCollection_) hinzu, um anzugeben, dass Ihre Razor Pages sich in einem benutzerdefinierten Stammverzeichnis in der App befinden (geben Sie einen relativen Pfad an):

```csharp
services.AddMvc()
    .AddRazorPagesOptions(options =>
    {
        ...
    })
    .WithRazorPagesRoot("/path/to/razor/pages");
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Authorize-Attribut und Razor Pages](xref:security/authorization/simple#aarp)
* <xref:index>
* <xref:mvc/views/razor>
* <xref:mvc/controllers/areas>
* <xref:tutorials/razor-pages/razor-pages-start>
* <xref:security/authorization/razor-pages-authorization>
* <xref:razor-pages/razor-pages-conventions>
* <xref:test/razor-pages-tests>
* <xref:mvc/views/partial>

::: moniker-end
