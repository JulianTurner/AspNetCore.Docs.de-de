---
title: Erstellen von Taghilfsprogrammen in ASP.NET Core
author: rick-anderson
description: Informationen zum Erstellen von Taghilfsprogrammen in ASP.NET Core
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
uid: mvc/views/tag-helpers/authoring
ms.openlocfilehash: 306416db3d9ae0219f859c3cf459eb08a5b778cf
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060923"
---
# <a name="author-tag-helpers-in-aspnet-core"></a>Erstellen von Taghilfsprogrammen in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/authoring/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="get-started-with-tag-helpers"></a>Erste Schritte mit Taghilfsprogrammen

Dieses Tutorial soll als Einführung in das Programmieren von Taghilfsprogrammen dienen. Im Artikel [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](intro.md) werden die Vorteile von Taghilfsprogrammen beschrieben.

Bei einem Taghilfsprogramm handelt es sich um eine Klasse, die die `ITagHelper`-Schnittstelle implementiert. Wenn Sie hingegen ein Taghilfsprogramm erstellen, stellen Sie in der Regel eine Ableitung von `TagHelper` her, wodurch Sie auf die `Process`-Methode zugreifen können.

1. Erstellen Sie ein neues ASP.NET Core-Projekt mit dem Namen **AuthoringTagHelpers** . Für dieses Projekt benötigen Sie keine Authentifizierung.

1. Erstellen Sie einen Ordner mit dem Namen *TagHelpers* , in dem die Taghilfsprogramme gespeichert werden sollen. Dieser *TagHelpers* Ordner ist *nicht* zwingend erforderlich, allerdings handelt es sich dabei um eine praktische Konvention. Im Folgenden werden Beispiele zum Schreiben einfacher Taghilfsprogramme beschrieben.

## <a name="a-minimal-tag-helper"></a>Ein Taghilfsprogramm mit den mindestens erforderlichen Elementen

In diesem Beispiel wird erläutert, wie Sie ein Taghilfsprogramm schreiben, das ein Update für ein E-Mail-Tag ausführt. Zum Beispiel:

```html
<email>Support</email>
```

Der Server verwendet das Taghilfsprogramm für E-Mails, um dieses Markup in Folgendes zu konvertieren:

```html
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

Dabei handelt es sich um ein Anchor-Tag, das daraus einen E-Mail-Link erstellt. Dieses Hilfsprogramm kann sich als nützlich erweisen, wenn Sie eine Blog-Engine schreiben und eine E-Mail an die Marketingabteilung, den Support oder andere Kontakte senden möchten, die alle derselben Domäne angehören.

1. Fügen Sie dem Ordner *TagHelpers* folgende `EmailTagHelper`-Klasse hinzu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1EmailTagHelperCopy.cs)]

   * Taghilfsprogramme verwenden Namenskonventionen, die auf die Elemente des Stammklassennamens ausgerichtet sind. Das Element *TagHelper* ist in dem Klassennamen allerdings nicht enthalten. In diesem Beispiel lautet der Stammname von **EmailTagHelper***email* , damit das `<email>`-Tag als Ziel verwendet wird. Diese Namenskonvention sollte für die meisten Taghilfsprogramme funktionieren. Nachfolgend finden Sie eine Erläuterung dazu, wie Sie diese außer Kraft setzen.

   * Die `EmailTagHelper`-Klasse wird von `TagHelper` abgeleitet. Über die `TagHelper`-Klasse werden Methoden und Eigenschaften für das Schreiben von Taghilfsprogrammen bereitgestellt.

   * Die überschriebene `Process`-Methode steuert die Funktionen des Taghilfsprogramms bei der Ausführung. Außerdem stellt die `TagHelper`-Klasse eine asynchrone Version (`ProcessAsync`) mit denselben Parametern bereit.

   * Der Kontextparameter von `Process` (und `ProcessAsync`) enthält Informationen, die der Ausführung des aktuellen HTML-Tags zugewiesen werden.

   * Der Ausgabeparameter von `Process` (und `ProcessAsync`) enthält ein zustandsbehaftetes HTML-Element, das für die Originalquelle steht, die zum Generieren eines HTML-Tags und von Inhalten verwendet wird.

   * Der Klassenname in diesem Beispiel enthält ein **TagHelper** -Suffix, das *nicht* erforderlich ist, aber als bewährter Bestandteil angesehen wird. Sie können die Klasse wie folgt deklarieren:

   ```csharp
   public class Email : TagHelper
   ```

1. `EmailTagHelper` Razor Fügen Sie der `addTagHelper` *views/_ViewImports. cshtml-* Datei die-Direktive hinzu, um die Klasse für alle Sichten verfügbar zu machen:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImportsCopyEmail.cshtml?highlight=2,3)]

   Im obenstehenden Code wird die Platzhaltersyntax verwendet, um alle Taghilfsprogramme anzugeben, die in der Assembly verfügbar sein sollen. In der ersten Zeichenfolge nach `@addTagHelper` wird angegeben, welches Taghilfsprogramm geladen werden soll (Verwenden Sie für alle Taghilfsprogramme „*“), und die zweite Zeichenfolge „AuthoringTagHelpers“ gibt die Assembly an, in der sich das Taghilfsprogramm befindet. Beachten Sie außerdem, dass die zweite Zeile die ASP.net Core MVC-taghilfsprogramme mithilfe der Platzhalter Syntax bringt (diese Hilfsprogramme werden unter [Einführung in taghilfsprogramme](intro.md)erläutert). Dies ist die- `@addTagHelper` Direktive, die das taghilfsprogramm für die Ansicht verfügbar macht Razor . Stattdessen können Sie auch wie folgt den vollqualifizierten Namen eines Taghilfsprogramms eingeben:

```csharp
@using AuthoringTagHelpers
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
@addTagHelper AuthoringTagHelpers.TagHelpers.EmailTagHelper, AuthoringTagHelpers
```

<!--
the following snippet uses TagHelpers3 and should use TagHelpers (not the 3)
    [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/_ViewImports.cshtml?highlight=3&range=1-3)]
-->

Wenn Sie einer Ansicht über einen vollqualifizierten Namen ein Taghilfsprogramm hinzufügen möchten, müssen Sie zunächst den Namen (`AuthoringTagHelpers.TagHelpers.EmailTagHelper`) und dann den **Assemblynamen** ( *AuthoringTagHelpers* , nicht notwendigerweise `namespace`) hinzufügen. Die meisten Entwickler verwenden am liebsten die Platzhaltersyntax. Unter [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](intro.md) finden Sie mehr Details zum Hinzufügen und Entfernen von Taghilfsprogrammen, zur Hierarchie und zur Platzhaltersyntax.

1. Aktualisieren Sie das Markup mit den folgenden Änderungen in der Datei *Views/Home/Contact.cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Führen Sie die App aus, und verwenden Sie einen beliebigen Browser, um die HTML-Quelle abzurufen, damit Sie überprüfen können, ob die E-Mail-Tags durch Anchor-Markups wie `<a>Support</a>` ersetzt wurden. *Support* und *Marketing* werden als Links gerendert. Allerdings besitzen sie kein `href`-Attribut, das sie funktionsfähig macht. Dies soll im nächsten Abschnitt behoben werden.

## <a name="setattribute-and-setcontent"></a>„SetAttribute“ und „SetContent“

In diesem Abschnitt wird erläutert, wie Sie ein Update für `EmailTagHelper` ausführen, sodass ein gültiges Anchor-Tag für die E-Mail erstellt wird. Wir aktualisieren Sie, um Informationen aus einer Razor Ansicht (in Form eines- `mail-to` Attributs) zu übernehmen und diese beim Erstellen des Ankers zu verwenden.

Führen Sie über folgenden Code ein Update für die `EmailTagHelper`-Klasse aus:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?range=6-22)]

* Namen von Klassen und Eigenschaften für Taghilfsprogramme, die in Pascal-Schreibweise angegeben sind, werden in [Kebab-Schreibweise](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101) übersetzt. Wenn Sie daher das `MailTo`-Attribut verwenden möchten, müssen Sie das entsprechende `<email mail-to="value"/>`-Äquivalent auswählen.

* Mit der letzten Zeile wird der Inhalt dieses Taghilfsprogramms fertig gestellt, das alle mindestens erforderlichen Elemente enthält.

* In der markierten Zeile wird die Syntax zum Hinzufügen von Attributen dargestellt:

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailTo.cs?highlight=6&range=14-21)]

Dieser Ansatz funktioniert für das Attribut „href“, wenn es zu diesem Zeitpunkt noch nicht in der Attributsammlung enthalten ist. Außerdem können Sie die `output.Attributes.Add`-Methode verwenden, um ein Attribut des Taghilfsprogramms am Ende der Tagattributsammlung hinzuzufügen.

1. Aktualisieren Sie das Markup mit den folgenden Änderungen in der Datei *Views/Home/Contact.cshtml* :

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/ContactCopy.cshtml?highlight=15,16)]

1. Führen Sie die App aus, und überprüfen Sie, ob die richtigen Links generiert werden.

<a name="self-closing"></a>

   > [!NOTE]
   > Wenn das E-Mail-Tag selbstschließend sein soll (`<email mail-to="Rick" />`), ist auch die endgültige Ausgabe selbstschließend. Um die Funktion zum Schreiben des Tags nur mit einem Starttag (`<email mail-to="Rick">`) zu aktivieren, müssen Sie die Klasse wie folgt markieren:
   >
   > [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelperMailVoid.cs?highlight=1&range=6-10)]

   Bei einem selbstschließenden E-Mail-Taghilfsprogramm sieht die Ausgabe wie folgt aus `<a href="mailto:Rick@contoso.com" />`. Bei selbstschließenden Anchor-Tags handelt es sich nicht um gültige HTML-Elemente. Daher sollten Sie diese nicht erstellen. Erstellen Sie stattdessen ein selbstschließendes Taghilfsprogramm. Taghilfsprogramme legen den Typ der `TagMode`-Eigenschaft fest, nachdem ein Tag gelesen wurde.

### <a name="processasync"></a>ProcessAsync

In diesem Abschnitt wird beschrieben, wie Sie ein asynchrones E-Mail-Hilfsprogramm schreiben.

1. Ersetzen Sie die Klasse `EmailTagHelper` durch den folgenden Code:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/EmailTagHelper.cs?range=6-17)]

   **Hinweise:**

   * Diese Version verwendet die asynchrone `ProcessAsync`-Methode. Die asynchrone `GetChildContentAsync`-Methode gibt `Task` mit `TagHelperContent` zurück.

   * Verwenden Sie den Parameter `output`, um Inhalte des HTML-Elements abzurufen.

1. Nehmen Sie folgende Änderung an der *Views/Home/Contact.cshtml* -Datei vor, damit das Taghilfsprogramm die Ziel-E-Mail abrufen kann.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=15,16&range=1-17)]

1. Führen Sie die App aus, und überprüfen Sie, ob gültige E-Mail-Links generiert werden.

### <a name="removeall-precontentsethtmlcontent-and-postcontentsethtmlcontent"></a>„RemoveAll“, „PreContent.SetHtmlContent“ und „PostContent.SetHtmlContent“

1. Fügen Sie dem Ordner *TagHelpers* folgende `BoldTagHelper`-Klasse hinzu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/BoldTagHelper.cs)]

   * Das `[HtmlTargetElement]`-Attribut übergibt einen Attributparameter, der angibt, dass diesem ein HTML-Element mit dem HTML-Attribut „bold“ zugeordnet werden kann. Anschließend wird die Überschreibungsmethode `Process` in der Klasse ausgeführt. In diesem Beispiel entfernt die Methode `Process` das Attribut „bold“ und umschließt das enthaltene Markup mit `<strong></strong>`.

   * Da der bestehende Taginhalt nicht ersetzt werden soll, müssen Sie das Starttag `<strong>` über die Methode `PreContent.SetHtmlContent` und das Endtag `</strong>` über die Methode `PostContent.SetHtmlContent` schreiben.

1. Verändern Sie die Ansicht *About.cshtml* , damit diese den Attributwert `bold` enthält. Der fertige Code wird nachfolgend dargestellt.

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutBoldOnly.cshtml?highlight=7)]

1. Führen Sie die App aus. Sie können einen beliebigen Browser verwenden, um die Quelle zu untersuchen und das Markup zu überprüfen.

   Das oben genannte `[HtmlTargetElement]`-Attribut gilt nur für HTML-Markups mit dem Attributnamen „bold“. Das `<bold>`-Element wurde vom Taghilfsprogramm nicht verändert.

1. Kommentieren Sie die Attributzeile `[HtmlTargetElement]` aus, damit standardmäßig `<bold>`-Tags angezielt werden, also HTML-Markups im Format `<bold>`. Beachten Sie, dass die Standardnamenskonvention dem Klassennamen **Bold** TagHelper den `<bold>`-Tags entspricht.

1. Führen Sie die App aus, und überprüfen Sie, ob das `<bold>`-Tag vom Taghilfsprogramm verarbeitet wird.

Wenn Sie eine Klasse durch mehrere `[HtmlTargetElement]`-Attribute ergänzen, wird ein logischer ODER-Operator der Ziele erstellt. Wenn Sie z.B. den nachfolgenden Code verwenden, wird ein „bold“-Tag oder ein „bold“-Attribut zugeordnet.

[!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zBoldTagHelperCopy.cs?highlight=1,2&range=5-15)]

Wenn mehrere Attribute derselben Anweisung hinzugefügt werden, behandelt die Runtime diese als logisches UND. Beispielsweise muss im nachfolgenden Code ein HTML-Element den Namen „bold“ mit einem Attribut namens „bold“ (`<bold bold />`) enthalten sein, damit dieses zugeordnet werden kann.

```csharp
[HtmlTargetElement("bold", Attributes = "bold")]
   ```

Außerdem können Sie `[HtmlTargetElement]` verwenden, um den Namen des angezielten Elements zu ändern. Wenn Sie z.B. möchten, dass `BoldTagHelper``<MyBold>`-Tags anzielt, müssen Sie die folgenden Attribute verwenden:

```csharp
[HtmlTargetElement("MyBold")]
```

## <a name="pass-a-model-to-a-tag-helper"></a>Übergeben eines Modells an das Taghilfsprogramm

1. Fügen Sie einen *Models* -Ordner hinzu.

1. Fügen Sie dem Ordner *Models* die folgende `WebsiteContext`-Klasse hinzu:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Models/WebsiteContext.cs)]

1. Fügen Sie dem Ordner *TagHelpers* folgende `WebsiteInformationTagHelper`-Klasse hinzu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/WebsiteInformationTagHelper.cs)]

   * Wie obenstehend erwähnt, übersetzen Taghilfsprogramme C#-Klassennamen und -Eigenschaften in der Pascal-Schreibweise für Taghilfsprogramme in [Kebab-Schreibweise](https://wiki.c2.com/?KebabCase). Um in zu verwenden `WebsiteInformationTagHelper` Razor , schreiben Sie daher `<website-information />` .

   * Das Zielelement wird mit dem `[HtmlTargetElement]`-Attribut nicht explizit identifiziert, sodass standardmäßig `website-information` angezielt wird. Wenn Sie das folgende Attribut angewendet haben (beachten Sie, dass es sich hierbei nicht um Kebab Case handelt, sondern das Attribut dem Klassennamen zugeordnet wird):

   ```csharp
   [HtmlTargetElement("WebsiteInformation")]
   ```

   Das Tag `<website-information />` in Kebab-Schreibweise wird dann nicht zugeordnet. Wenn Sie das `[HtmlTargetElement]`-Attribut verwenden, müssen Sie wie nachfolgend dargestellt Kebab Case verwenden:

   ```csharp
   [HtmlTargetElement("Website-Information")]
   ```

   * Selbstschließende Elemente haben keinen Inhalt. In diesem Beispiel verwendet das Razor Markup ein selbstschließendes Tag, aber das taghilfsprogramm erstellt ein [Abschnitts](https://www.w3.org/TR/html5/sections.html#the-section-element) Element (das nicht selbst geschlossen wird und Sie Inhalt innerhalb des `section` Elements schreiben). Aus diesem Grund müssen Sie `TagMode` auf `StartTagAndEndTag` festlegen, um Ausgaben zu schreiben. Stattdessen können Sie auch die Zeileneinstellung `TagMode` auskommentieren und Markups mit einem Endtag schreiben. (Ein Beispielmarkup wird in einem nachfolgenden Abschnitt dieses Tutorials zur Verfügung gestellt.)

   * Das Dollarzeichen (`$`) in der folgenden Zeile verwendet eine [interpolierte Zeichenfolge](/dotnet/csharp/language-reference/keywords/interpolated-strings):

   ```cshtml
   $@"<ul><li><strong>Version:</strong> {Info.Version}</li>
   ```

1. Fügen Sie der *About.cshtml* -Ansicht das folgende Markup hinzu. Das markierte Markup zeigt die Websiteinformationen an.

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?highlight=1,4-8, 18-999)]

   > [!NOTE]
   > Im Razor unten gezeigten Markup:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/About.cshtml?range=18-18)]
   >
   > Razor weiß, `info` dass es sich bei dem Attribut um eine Klasse, nicht um eine Zeichenfolge handelt und Sie c#-Code schreiben möchten. Alle Attribute von Taghilfsprogrammen, die keine Zeichenfolgen darstellen, sollten ohne das Zeichen `@` geschrieben werden.

1. Führen Sie die App aus, und navigieren Sie zur Ansicht „Info“, um die Websiteinformationen zu sehen.

   > [!NOTE]
   > Sie können das folgende Markup mit dem Endtag verwenden und die Zeile mit `TagMode.StartTagAndEndTag` aus dem Taghilfsprogramm entfernen:
   >
   > [!code-html[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/AboutNotSelfClosing.cshtml?range=20-21)]

## <a name="condition-tag-helper"></a>Taghilfsprogramm für Bedingungen

Das Taghilfsprogramm für Bedingungen rendert Ausgaben, wenn ein TRUE-Wert zurückgegeben wird.

1. Fügen Sie dem Ordner *TagHelpers* folgende `ConditionTagHelper`-Klasse hinzu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/ConditionTagHelper.cs)]

1. Ersetzen Sie die Inhalte der Datei *Views/HelloWorld/Index.cshtml* durch das folgende Markup:

   [!code-cshtml[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Index.cshtml)]

1. Ersetzen Sie die `Index`-Methode im `Home`-Controller durch den folgenden Code:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Controllers/HomeController.cs?range=9-18)]

1. Führen Sie die App aus, und navigieren Sie zur Homepage. Das Markup im bedingten `div`-Element wird nicht gerendert. Fügen Sie die Abfragezeichenfolge `?approved=true` an die URL an (z.B. `http://localhost:1235/Home/Index?approved=true`). `approved` ist auf TRUE festgelegt, und das bedingte Markup wird angezeigt.

> [!NOTE]
> Verwenden Sie den [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operator, um das anzuzielende Attribut wie beim „bold“-Taghilfsprogramm anzugeben, anstatt eine Zeichenfolge anzugeben:
>
> [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/zConditionTagHelperCopy.cs?highlight=1,2,5&range=5-18)]
>
> Der [nameof](/dotnet/csharp/language-reference/keywords/nameof)-Operator schützt den Code, wenn dieser umgestaltet wird, also wenn z.B. der Name in `RedCondition` geändert wird.

### <a name="avoid-tag-helper-conflicts"></a>Vermeiden von Konflikten mit dem Taghilfsprogramm

In diesem Abschnitt wird erläutert, wie Sie zwei Taghilfsprogramme für automatische Verlinkungen schreiben. Das erste ersetzt Markups mit einer URL, die mit HTTP beginnt, durch ein HTML-Anchor-Tag mit derselben URL, sodass ein Link zur der URL zurückgegeben wird. Das zweite führt dieselbe Aktion aus, allerdings für URLs, die mit WWW beginnen.

Da dieses beiden Hilfsprogramme sehr ähnlich funktionieren und Sie sie möglicherweise in der Zukunft umgestalten möchten, werden diese in derselben Datei gespeichert.

1. Fügen Sie dem Ordner *TagHelpers* folgende `AutoLinkerHttpTagHelper`-Klasse hinzu.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=7-19)]

   >[!NOTE]
   >Die `AutoLinkerHttpTagHelper`-Klasse zielt `p`-Elemente an und verwendet [RegEx](/dotnet/standard/base-types/regular-expression-language-quick-reference), um den Anchor zu erstellen.

1. Fügen Sie das folgende Markup an das Ende der Datei *Views/Home/Contact.cshtml* an:

   [!code-cshtml[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/Views/Home/Contact.cshtml?highlight=19)]

1. Führen Sie die App aus, und überprüfen Sie, ob das Taghilfsprogramm den Anchor richtig rendert.

1. Führen Sie ein Update für die `AutoLinker`-Klasse aus, um `AutoLinkerWwwTagHelper` einzufügen, wodurch WWW-Text in ein Anchor-Tag konvertiert wird, das ebenfalls den ursprünglichen WWW-Text enthält. Der aktualisierte Code wird im folgenden Beispiel markiert:

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?highlight=15-34&range=7-34)]

1. Führen Sie die App aus. Beachten Sie, dass der WWW-Text als Link gerendert wird, der HTTP-Text hingegen nicht. Wenn Sie in beide Klassen einen Haltepunkt einfügen, wird die HTTP-Hilfsprogrammklasse zuerst ausgeführt. Das Problem dabei ist, dass die Ausgabe des Taghilfsprogramms zwischengespeichert wird, und wenn das WWW-Hilfsprogramm ausgeführt wird, wird die zwischengespeicherte Ausgabe des HTTP-Hilfsprogramms überschrieben. Nachfolgend wird erläutert, wie Sie die Reihenfolge kontrollieren, in der Taghilfsprogramme ausgeführt werden. Codefehler werden wie folgt behoben:

   [!code-csharp[](../../../mvc/views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10,21,22,26&range=8-37)]

   > [!NOTE]
   > In der ersten Version der Taghilfsprogramme für automatische Verlinkungen konnten Sie den Inhalt des Ziels über folgenden Code abrufen:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinker.cs?range=12)]
   >
   > Das bedeutet, Sie rufen `GetChildContentAsync` über das `TagHelperOutput`-Objekt ab, das in der `ProcessAsync`-Methode übergeben wurde. Wie zuvor bereits erwähnt, gewinnt das letzte Taghilfsprogramm, das ausgeführt wird, da die Ausgabe zwischengespeichert wird. Sie haben dieses Problem mithilfe des folgenden Codes behoben:
   >
   > [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?range=34-35)]
   >
   > Der obenstehende Code prüft, ob der Inhalt verändert wurde. Wenn das der Fall ist, ruft er den Inhalt vom Ausgabepuffer ab.

1. Führen Sie die App aus, und überprüfen Sie, ob die beiden Links wie gewünscht funktionieren. Dabei kann es oft sein, dass es scheint, als würde das Taghilfsprogramm für die automatische Verlinkung einwandfrei funktionieren, obwohl ein verstecktes Problem besteht. Wenn das WWW-Taghilfsprogramm zuerst ausgeführt wird, werden die WWW-Links fehlerhaft erstellt. Aktualisieren Sie den Code, indem Sie die Überladung `Order` hinzufügen, um die Reihenfolge zu kontrollieren, in der das Tag ausgeführt wird. Die Eigenschaft `Order` bestimmt die Ausführungsreihenfolge, die im Zusammenhang mit anderen Taghilfsprogrammen steht, die auf dasselbe Element ausgerichtet sind. Der Wert der Standardreihenfolge ist 0 (null), und Instanzen mit niedrigeren Werten werden zuerst ausgeführt.

   [!code-csharp[](authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z2AutoLinkerCopy.cs?highlight=5,6,7,8&range=8-15)]

   Mit dem vorhergehenden Code wird sichergestellt, dass das HTTP-Taghilfsprogramm vor dem WWW-Taghilfsprogramm ausgeführt wird. Ändern Sie `Order` in `MaxValue`, und überprüfen Sie, ob das für das WWW-Tag generierte Markup falsch ist.

## <a name="inspect-and-retrieve-child-content"></a>Überprüfen und Abrufen von untergeordnetem Inhalt

Die Taghilfsprogramme stellen mehrere Eigenschaften zum Abrufen von Inhalt zur Verfügung.

* Das Ergebnis von `GetChildContentAsync` kann an `output.Content` angefügt werden.
* Sie können das Ergebnis von `GetChildContentAsync` mit `GetContent` prüfen.
* Wenn Sie `output.Content` ändern, wird der TagHelper-Text nicht ausgeführt oder gerendert, solange Sie nicht wie im Beispiel zur automatischen Verlinkung `GetChildContentAsync` aufrufen:

[!code-csharp[](../../views/tag-helpers/authoring/sample/AuthoringTagHelpers/src/AuthoringTagHelpers/TagHelpers/z1AutoLinkerCopy.cs?highlight=5,6,10&range=8-21)]

* Bei mehreren Aufrufen von `GetChildContentAsync` wird derselbe Wert zurückgegeben, und der `TagHelper`-Text wird nicht erneut ausgeführt, solange Sie keinen FALSE-Parameter übergeben, der darauf hindeutet, dass die zwischengespeicherten Ergebnisse nicht verwendet werden sollen.

## <a name="load-minified-partial-view-taghelper"></a>TagHelper für das Laden minimierter Teilansichten

In Produktionsumgebungen lässt sich die Leistung steigern, indem minimierte Teilansichten geladen werden. Um von minimierten Teilansichten in der Produktion zu profitieren, gehen Sie folgendermaßen vor:

* Erstellen Sie einen Präbuildprozess, der Teilansichten minimiert, und richten Sie den Prozess ein.
* Verwenden Sie den folgenden Code, um minimierte Teilansichten in Nicht-Entwicklungsumgebungen zu laden.

[!code-csharp[](authoring/sample/AuthoringTagHelpers/src/MinifiedVersionTagHelper.cs?name=snippet)]
