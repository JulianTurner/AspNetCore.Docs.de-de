---
title: Ansichten in ASP.NET Core MVC
author: ardalis
description: Informationen zur Verarbeitung der Darstellung von App-Daten und zur Benutzerinteraktion in den Ansichten von ASP.NET Core MVC
ms.author: riande
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
uid: mvc/views/overview
ms.openlocfilehash: 373b17377740441d3859e3b7d942017a22bc7a68
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060624"
---
# <a name="views-in-aspnet-core-mvc"></a>Ansichten in ASP.NET Core MVC

Von [Steve Smith](https://ardalis.com/)

In diesem Artikel werden die Ansichten erläutert, die in ASP.NET Core MVC-Anwendungen verwendet werden. Informationen zu Razor Seiten finden Sie unter [Einführung in Razor Seiten](xref:razor-pages/index).

Im Muster Model-View-Controller (MVC) verarbeitet die *Ansicht* die Darstellung der App-Daten und der Benutzerinteraktion. Eine Sicht ist eine HTML-Vorlage mit eingebettetem [ Razor Markup](xref:mvc/views/razor). Razor Markup ist ein Code, der mit HTML-Markup interagiert, um eine Webseite zu entwickeln, die an den Client gesendet wird.

In ASP.net Core MVC sind Sichten *cshtml* -Dateien, die die [Programmiersprache c#](/dotnet/csharp/) im Razor Markup verwenden. In der Regel werden Ansichtsdateien in Ordnern gruppiert, die für jeden [Controller](xref:mvc/controllers/actions) der App benannt werden. Die Ordner werden in *Ansichten* -Ordnern im Stammverzeichnis der App gespeichert:

![Ansichtenordner werden im Projektmappen-Explorer von Visual Studio zusammen mit dem Basisordner geöffnet, um die Dateien „About.cshtml“, „Contact.cshtml“ und „Index.cshtml“ angezeigt werden.](overview/_static/views_solution_explorer.png)

Der *Basis* -Controller wird im *Ansichten* -Ordner von einem *Basis* -Ordner dargestellt. Der *Basis* -Ordner enthält Ansichten für die (Homepage-)Webseiten *Hilfe* , *Kontakt* und *Index* . Wenn ein Benutzer eine dieser drei Webseiten aufruft, legen Controlleraktionen im *Basis* -Controller fest, welche der drei Ansichten verwendet werden, um Webseiten zu erstellen und diese an den Benutzer zurückzugeben.

Verwenden Sie [Layouts](xref:mvc/views/layout), damit die Abschnitte der Webseiten konsistent angezeigt werden und die Wiederholung von Code vermieden wird. Layouts bestehen in der Regel aus der Kopfzeile, der Navigation, Menüelementen und der Fußzeile. Die Kopf- und die Fußzeile enthalten in der Regel Markupbausteine für viele Metadatenelemente sowie Links zu Skripts und Stilelemente. Mithilfe von Layouts können Sie die Markupbausteine in Ihren Ansichten vermeiden.

Über [Teilansichten](xref:mvc/views/partial) wird weniger Code dupliziert, indem wiederverwendbare Bestandteile der Ansichten verwaltet werden. Sie sind z.B. nützlich für Autorenbiografien auf einem Blog, die in verschiedenen Ansichten angezeigt werden. Bei einer Autorenbiografie handelt es sich um gewöhnlichen Inhalt von Ansichten. Sie erfordern keinen Code, der ausgeführt werden muss, um den Inhalt für die Webseite herzustellen. Die Ansicht kann auf die Inhalte der Autorenbiografie über eine Modellbindung zugreifen. Daher sollten idealerweise Teilansichten für diese Art von Inhalt verwendet werden.

Über [Ansichtskomponenten](xref:mvc/views/view-components) können Sie genauso wie über Teilansichten Wiederholungen von Code reduzieren. Sie sind besonders geeignet für Inhalt von Ansichten, für den Code auf dem Server ausgeführt werden muss, um die Webseite zu rendern. Ansichtskomponenten sind nützlich, wenn der gerenderte Inhalt Datenbankinformationen erfordert, z.B. bei einem Warenkorb auf einer Website. Ansichtskomponenten sind nicht auf Modellbindungen beschränkt, um Webseitenausgaben herzustellen.

## <a name="benefits-of-using-views"></a>Vorteile der Verwendung von Ansichten

Über Ansichten kann in einer MVC-App eine [Trennung von Belangen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) erfolgen, indem das Markup der Benutzeroberfläche von anderen Bestandteilen der App getrennt wird. Das SoC-Design macht Ihre App modularer. Dies hat folgende Vorteile:

* Die App kann einfacher verwaltet werden, da sie besser organisiert ist. Ansichten werden in der Regel nach App-Features gruppiert. Dadurch können Sie zugehörige Ansichten leichter finden, wenn Sie an einem Feature arbeiten.
* Die Bestandteile der App sind lose miteinander verknüpft. Sie können die Ansichten der App getrennt von der Geschäftslogik und den Datenzugriffskomponenten erstellen und aktualisieren. Sie können die Ansichten der App verändern, müssen dafür aber nicht unbedingt andere Bestandteile der App aktualisieren.
* Sie können die Bestandteile der Benutzeroberfläche der App leichter testen, da es sich bei den Ansichten um voneinander getrennte Einheiten handelt.
* Durch die verbesserte Organisation sinkt die Wahrscheinlichkeit, dass Sie versehentlich Code für die Benutzeroberfläche wiederholt schreiben.

## <a name="creating-a-view"></a>Erstellen einer Ansicht

Ansichten, die nur für bestimmte Controller erstellt werden, werden im Ordner *Views/[NamedesControllers]* erstellt. Ansichten, die von mehreren Controllern verwendet werden können, werden im Ordner *Views/Shared* gespeichert. Wenn Sie eine Ansicht erstellen möchten, fügen Sie eine neue Datei hinzu, und geben Sie ihr denselben Namen wie der zugehörigen Controlleraktion mit der Erweiterung *.cshtml* . Wenn Sie eine Ansicht erstellen möchten, die mit der Aktion *Hilfe* im *Basis* -Controller übereinstimmt, erstellen Sie eine *About.cshtml* -Datei im Ordner *Views/Home* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

*Razor* Markup beginnt mit dem `@` Symbol. Führen Sie c#-Anweisungen aus, indem Sie c#-Code in [ Razor Code Blöcken](xref:mvc/views/razor#razor-code-blocks) platzieren, die von geschweiften Klammern () deaktiviert werden `{ ... }` . Weitere Informationen finden Sie im obenstehenden Beispiel für die Zuweisung von „Hilfe“ zu `ViewData["Title"]`. Sie können Werte innerhalb von HTML-Code angeben, indem Sie auf den Wert mit dem Symbol `@` verweisen. Weitere Informationen finden Sie in den Inhalten der obenstehenden Elemente `<h2>` und `<h3>`.

Der obenstehende Inhalt der Ansicht ist nur ein Teil der gesamten Webseite, die für den Benutzer gerendert wird. Der Rest des Seitenlayouts und andere häufig auftretende Aspekte werden in anderen Ansichtsdateien angegeben. Weitere Informationen dazu finden Sie im Artikel [Layout](xref:mvc/views/layout).

## <a name="how-controllers-specify-views"></a>Angeben von Ansichten durch Controller

Ansichten werden in der Regel von Aktionen als [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult) zurückgegeben. Dabei handelt es sich um eine Art [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult). Über Ihre Aktionsmethode kann eine `ViewResult`-Klasse direkt erstellt und zurückgegeben werden. Diese Methode wird allerdings selten genutzt. Da die meisten Controller von [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller) erben, können Sie einfach die `View`-Hilfsprogrammmethode verwenden, um `ViewResult` zurückzugeben:

*HomeController.cs*

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

Wenn diese Aktion zurückgegeben wird, wird die im letzten Abschnitt angezeigte *About.cshtml* -Ansicht als die folgende Webseite gerendert:

![Hilfe-Seite in Edge gerendert](overview/_static/about-page.png)

Für die `View`-Hilfsprogrammmethode gibt es einige Überladungen. Sie können bei Bedarf folgende Elemente angeben:

* Eine explizite Ansicht, die zurückgegeben werden soll:

  ```csharp
  return View("Orders");
  ```

* Ein [Modell](xref:mvc/models/model-binding), das an die Ansicht übergeben werden soll:

  ```csharp
  return View(Orders);
  ```

* Sowohl eine Ansicht als auch ein Modell:

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a>Ansichtsermittlung

Wenn eine Aktion eine Ansicht zurückgibt, wird ein Prozess namens *Ansichtsermittlung* ausgelöst. Über diesen Prozess wird auf Grundlage des Ansichtsnamens festgelegt, welche Ansichtsdatei verwendet wird. 

Standardmäßig gibt die `View`-Methode (`return View();`) eine Ansicht zurück, die denselben Namen wie die Aktionsmethode besitzt, über die diese aufgerufen wird. Beispielsweise wird der Name der " *about* "- `ActionResult` Methode des Controllers verwendet, um nach einer Ansichts Datei mit dem Namen " *about. cshtml* " zu suchen. Zuerst sucht die Runtime im Ordner *Views/[NamedesControllers]* nach der Ansicht. Wenn keine passende Ansicht gefunden wird, wird im Ordner *Freigegeben* nach der Ansicht gesucht.

Es macht keinen Unterschied, ob Sie implizit `ViewResult` mit `return View();` zurückgeben, oder den Ansichtsnamen mit `return View("<ViewName>");` explizit an die `View`-Methode übergeben. In beiden Fällen sucht die Ansichtsermittlung nach einer passenden Ansichtsdatei in diesem Ordner:

   1. *Views/ \[ ControllerName]/ \[ viewName]. cshtml*
   1. *Views/Shared/\[Ansichtsname].cshtml*

Anstelle eines Ansichtsnamens kann auch ein Pfad zu einer Ansichtsdatei verwendet werden. Wenn Sie einen absoluten Pfad im Stammverzeichnis der App verwenden (der mit „/“ oder „~/“ beginnt), muss die Erweiterung *.cshtml* angegeben sein:

```csharp
return View("Views/Home/About.cshtml");
```

Sie können auch einen relativen Pfad verwenden, um Ansichten in verschiedenen Verzeichnissen ohne die Erweiterung *.cshtml* anzugeben. In der `HomeController`-Klasse können Sie die Ansicht *Index* in Ihrer *Verwaltungs* -Ansicht mit einem relativen Pfad angeben:

```csharp
return View("../Manage/Index");
```

Genauso können Sie auch das aktuelle controllerspezifische Verzeichnis mit dem Präfix „./“ angeben:

```csharp
return View("./About");
```

[Teilansichten](xref:mvc/views/partial) und [Ansichtskomponenten](xref:mvc/views/view-components) verwenden ähnliche (aber nicht identische) Ermittlungsmechanismen.

Sie können die Standardkonvention zum Suchen von Ansichten in der App anpassen, indem Sie eine benutzerdefinierte [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander)-Schnittstelle verwenden.

Die Ansichtsermittlung ist davon abhängig, ob Ansichtsdateien über Dateinamen gefunden werden. Wenn das zugrunde liegende Dateisystem die Groß-/Kleinschreibung beachtet, wird wahrscheinlich auch bei den Ansichtsnamen zwischen Groß- und Kleinschreibung unterschieden. Damit die Kompatibilität für alle Betriebssysteme eingehalten werden kann, müssen Sie Groß-/Kleinbuchstaben zwischen dem Controller und den Aktionsnamen sowie den zugeordneten Ansichtsordnern und Dateinamen aufeinander abstimmten. Wenn ein Fehler ausgelöst wird und eine Ansichtsdatei nicht gefunden werden kann, während Sie mit einem Dateisystem arbeiten, das zwischen Groß- und Kleinbuchstaben unterscheidet, bestätigen Sie, dass die Groß-/Kleinschreibung der gesuchten Ansichtsdatei mit dem tatsächlichen Dateinamen der Ansicht übereinstimmt.

Halten Sie sich an die bewährten Methoden zum Organisieren der Dateistruktur Ihrer Ansichten, um die Beziehungen zwischen Controllern, Aktionen und Ansichten auf die Verwaltbarkeit und Klarheit auszurichten.

## <a name="passing-data-to-views"></a>Übergeben von Daten an Ansichten

Übergeben Sie Daten über verschiedene Ansätze an Ansichten:

* Stark typisierte Daten: viewmodel
* Schwach typisierte Daten
  * `ViewData` (`ViewDataAttribute`)
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a>Stark typisierte Daten (viewmodel)

Wenn Sie Stabilität wünschen, sollten Sie einen [Modell](xref:mvc/models/model-binding)-Typen in der Ansicht angeben. Das Modell wird häufig als *Ansichtsmodell* bezeichnet. Übergeben Sie eine Instanz des Ansichtsmodelltyps an die Ansicht aus der Aktion.

Wenn Sie ein Ansichtsmodell verwenden, um eine Ansicht zu übergeben, kann die Ansicht die Vorteile der *starken* Typüberprüfung nutzen. Man spricht von *starker Typisierung* (oder *stark typisiert* als Adjektiv), wenn es für jede Variable und jede Konstante einen explizit definierten Typ gibt, z.B. `string`, `int` oder `DateTime`. Die Gültigkeit der in der Ansicht verwendeten Typen wird zur Kompilierzeit überprüft.

[Visual Studio](https://visualstudio.microsoft.com) und [Visual Studio Code](https://code.visualstudio.com/) erstellen Listen von stark typisierten Klassenmembern mithilfe des Features [IntelliSense](/visualstudio/ide/using-intellisense). Wenn Sie die Eigenschaften eines Ansichtsmodell anzeigen wollen, geben Sie den Namen der Variablen für dieses an, und setzen Sie einen Punkt (`.`). Dadurch können Sie Code schneller schreiben, und Sie machen weniger Fehler.

Geben Sie mithilfe der `@model`-Anweisung ein Modell an. Verwenden Sie das Modell mit `@Model`:

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Der Controller übergibt das Modell als Parameter, um es für die Ansicht bereitzustellen:

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

Es gibt keine Einschränkungen der Modelltypen, die in einer Ansicht enthalten sein sollten. Es wird empfohlen, Plain Old CLR Object-Ansichtsmodelle (POCO) mit wenigen oder gar keinen definierten Methoden zu verwenden. In der Regel werden Ansichtsmodellklassen weder im Ordner *Modelle* noch in einem separaten *Ansichtsmodelle* -Ordner im Stammverzeichnis der App gespeichert. Bei dem im zuvor genannten Beispiel verwendeten *Adresse* -Ansichtsmodell handelt es sich um ein POCO-Ansichtsmodell, das in einer Datei mit dem Namen *Address.cs* gespeichert ist:

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

Es ist möglich, für die Ansichtsmodelltypen und Unternehmensmodelltypen dieselben Klassen zu verwenden. Wenn Sie allerdings unterschiedliche Modelle verwenden, können Ihre Ansichten unabhängig von der Geschäftslogik und den Bestandteilen zum Datenzugriff Ihrer App variieren. Wenn Sie Modelle und Ansichtsmodelle trennen, wird mehr Sicherheit geboten, wenn Modelle die [Modellbindung](xref:mvc/models/model-binding) und die [Validierung](xref:mvc/models/validation) von Daten verwenden, die vom Benutzer an die App gesendet werden.

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a>Schwach typisierte Daten (ViewData, ViewData-Attribut und ViewBag)

`ViewBag`*nicht verfügbar in Razor Seiten.*

Ansichten haben nicht nur Zugriff auf stark typisierte Datensammlungen, sondern auch auf *schwach typisierte* (auch als *lose typisiert* bezeichnet). Im Gegensatz zu starken Typen werden bei *schwachen Typen* (oder *losen Typen* ) nicht explizit die Datentypen deklariert, die Sie verwenden. Sie können die Sammlung schwach typisierter Daten verwenden, um kleinere Datenmengen an Controller und Ansichten zu übergeben oder sie ihnen zu entnehmen.

| Übergeben von Daten zwischen...                        | Beispiel                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| einem Controller und einer Ansicht                             | Auffüllen einer Dropdownliste mit Daten                                          |
| einer Ansicht und einer [Layoutansicht](xref:mvc/views/layout)   | Festlegen des **\<title>** Element Inhalts in der Layoutansicht aus einer Ansichts Datei.  |
| einer [Teilansicht](xref:mvc/views/partial) und einer Ansicht | Ein Widget, das Daten anzeigt, die der Webseite zugrunde liegen, die der Benutzer angefordert hat.      |

Auf diese Sammlung kann entweder über die Eigenschaft `ViewData` oder über die Eigenschaft `ViewBag` auf Controller und Ansichten verwiesen werden. Die `ViewData`-Eigenschaft ist ein Wörterbuch, das aus schwach typisierten Objekten besteht. Bei der `ViewBag`-Eigenschaft handelt es sich um einen Wrapper um `ViewData`, der dynamische Eigenschaften für die zugrunde liegende `ViewData`-Sammlung bereitstellt. Hinweis: bei schlüssellookups wird zwischen Groß-und Kleinschreibung unterbunden `ViewData` `ViewBag` .

`ViewData` und `ViewBag` werden zur Laufzeit dynamisch aufgelöst. Da in diesen Elementen keine Typüberprüfung zur Kompilierzeit enthalten sind, sind beide in der Regel fehleranfälliger als Ansichtsmodelle. Aus diesem Grund verwenden Entwickler `ViewData` und `ViewBag` selten oder nie.

<a name="VD"></a>

**ViewData**

`ViewData` ist ein [viewdatadictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) -Objekt, auf das über Schlüssel zugegriffen wird `string` . Zeichenfolgendaten können gespeichert und ohne Umwandlung direkt verwendet werden. Trotzdem müssen Sie für die Extraktion andere `ViewData`-Objektwerte in bestimmte Typen umwandeln. Sie können `ViewData` verwenden, um Daten von Controllern an Ansichten und innerhalb von Ansichten zu übergeben, einschließlich [Teilansichten](xref:mvc/views/partial) und [Layouts](xref:mvc/views/layout).

Im Folgenden finden Sie ein Beispiel, über das Werte für eine Begrüßung und eine Anrede mithilfe von `ViewData` in einer Aktion festgelegt werden:

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

Arbeiten Sie mit den Daten in einer Ansicht:

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

**Attribut „ViewData“**

Ein anderer Ansatz zur Verwendung von [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) ist das [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)-Attribut. Für Eigenschaften auf Controllern oder Razor Seiten Modellen, die mit dem- `[ViewData]` Attribut gekennzeichnet sind, werden die Werte gespeichert und aus dem Wörterbuch geladen.

Im folgenden Beispiel enthält der Home-Controller eine `Title`-Eigenschaft, die mit `[ViewData]` markiert ist. Die `About`-Methode legt den Titel der Infoansicht fest:

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

Im Layout wird der Titel aus dem ViewData-Wörterbuch gelesen:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

**ViewBag**

`ViewBag`*nicht verfügbar in Razor Seiten.*

`ViewBag` ein [dynamicviewdata](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) -Objekt, das dynamischen Zugriff auf die in gespeicherten Objekte bereitstellt `ViewData` . Es ist angenehmer, mit `ViewBag` zu arbeiten, da keine Umwandlung erforderlich ist. Im Folgenden finden Sie ein Beispiel, in dem dargestellt wird, wie Sie mit `ViewBag` das gleiche Ergebnis wie mit `ViewData` erzielen:

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

**Gleichzeitiges Verwenden von „ViewData“ and „ViewBag“**

`ViewBag`*nicht verfügbar in Razor Seiten.*

Da `ViewData` und `ViewBag` beide auf dieselbe zugrunde liegende `ViewData`-Sammlung verweisen, können Sie sowohl `ViewData` als auch `ViewBag` verwenden, und zwischen beiden Elementen wechseln, wenn Sie Werte schreiben und lesen.

Legen Sie oben in einer *About.cshtml* -Ansicht mit `ViewBag` den Titel und mit `ViewData` die Beschreibung fest:

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

Lesen Sie die Eigenschaften, aber verwenden Sie `ViewData` und `ViewBag` in umgekehrter Reihenfolge. Rufen Sie in der *_Layout.cshtml* -Datei mithilfe von `ViewData` den Titel und mithilfe von `ViewBag` die Beschreibung ab:

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

Bedenken Sie, dass für Zeichenfolgen mit `ViewData` keine Umwandlung erforderlich ist. `@ViewData["Title"]` kann ohne Umwandlung verwendet werden.

Sie können `ViewData` und `ViewBag` gleichzeitig verwenden und zwischen dem Lesen und Schreiben der Eigenschaften abwechseln. Das folgende Markup wird gerendert:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

**Zusammenfassung der Unterschiede zwischen „ViewData“ und „ViewBag“**

 `ViewBag` ist auf den Seiten nicht verfügbar Razor .

* `ViewData`
  * Wird von [viewdatadictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)abgeleitet und verfügt daher über Wörterbuch Eigenschaften, die nützlich sein können, z `ContainsKey` `Add` . b.,, `Remove` und `Clear` .
  * Schlüssel sind Zeichenfolgen im Wörterbuch. Daher sind Leerzeichen erlaubt. Beispiel: `ViewData["Some Key With Whitespace"]`
  * Jeder Typ, der von `string` abweicht, muss in der Ansicht umgewandelt werden, sodass `ViewData` verwendet werden kann.
* `ViewBag`
  * Wird von [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) abgeleitet. Daher können dynamische Eigenschaften über die Punktnotation (`@ViewBag.SomeKey = <value or object>`) erstellt werden, und es ist keine Umwandlung erforderlich. Über die Syntax von `ViewBag` können Controller und Ansichten schneller hinzugefügt werden.
  * Es ist einfacher, nach NULL-Werten zu suchen. Beispiel: `@ViewBag.Person?.Name`

**Empfohlene Verwendung von „ViewData“ und „ViewBag“**

`ViewData` und `ViewBag` sind beides gültige Ansätze, wenn Sie kleinere Datenmengen zwischen Controllern und Ansichten übergeben möchten. Sie können beliebig entscheiden, welche Methode Sie verwenden möchten. Sie können zwar `ViewData`- und `ViewBag`-Objekte miteinander vermischen, jedoch ist es einfacher, den Code zu lesen und zu verwalten, wenn nur ein Ansatz verwendet wird. Beide Ansätze werden zur Laufzeit dynamisch aufgelöst, wodurch häufig Laufzeitfehler entstehen. Einige Entwicklerteams vermeiden diese Ansätze daher.

### <a name="dynamic-views"></a>Dynamische Ansichten

Ansichten, die einen Modelltyp nicht mit `@model` deklarieren, an die aber eine Modellinstanz übergeben wird (z.B. `return View(Address);`), können dynamisch auf die Eigenschaften der Instanz verweisen:

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

Dieses Feature bietet zwar Flexibilität, jedoch weder Kompilierungsschutz noch IntelliSense. Wenn die Eigenschaft nicht vorhanden ist, löst die Webseitengenerierung zur Laufzeit einen Fehler aus.

## <a name="more-view-features"></a>Weitere Ansichtsfeatures

Mithilfe von [Taghilfsprogrammen](xref:mvc/views/tag-helpers/intro) können Sie einfach serverseitiges Verhalten zu vorhandenen HTML-Tags hinzufügen. Wenn Sie Taghilfsprogramme verwenden, müssen Sie keinen benutzerdefinierten Code oder Hilfsprogramme in Ihren Ansichten schreiben. Taghilfsprogramme werden als Attribute für HTML-Elemente angewendet und werden von Editors ignoriert, da sie diese nicht verarbeiten können. Dadurch können Sie Ansichtsmarkups in verschiedenen Tools bearbeiten und rendern.

Sie können benutzerdefinierte HTML-Markups über verschiedene integrierte HTML-Hilfsprogramme generieren. Eine komplexere Logik von Benutzeroberflächen kann über [Ansichtskomponenten](xref:mvc/views/view-components) verarbeitet werden. Ansichtskomponenten stellen dieselben SoC-Designs bereit, die Controller und Ansichten bereitstellen. Wenn diese verwendet werden, werden keine Aktionen und Ansichten benötigt, die Daten verarbeiten, die von häufig verwendeten Benutzeroberflächenelementen verwendet werden.

Wie viele andere Aspekte von ASP.NET Core unterstützen Ansichten [Dependency Injection](xref:fundamentals/dependency-injection) und lassen zu, dass Dienste [in Ansichten eingefügt werden](xref:mvc/views/dependency-injection).
