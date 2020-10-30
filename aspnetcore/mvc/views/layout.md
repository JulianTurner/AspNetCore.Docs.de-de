---
title: Layout in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie man gängige Layouts verwendet, Anweisungen von mehreren Ansichten gemeinsam nutzen lässt und Programmcode vor dem Rendern der Ansichten in einer ASP.NET Core-App ausführt.
ms.author: riande
ms.date: 07/30/2019
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
uid: mvc/views/layout
ms.openlocfilehash: 502df268e7f5f33acfffccd5ec0bd65267fa12da
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060975"
---
# <a name="layout-in-aspnet-core"></a>Layout in ASP.NET Core

Von [Steve Smith](https://ardalis.com/) und [Dave Brock](https://twitter.com/daveabrock)

Seiten und Ansichten beinhalten häufig sowohl visuelle als auch programmgesteuerte Elemente. Dieser Artikel demonstriert Folgendes:

* Verwendung von allgemeinen Layouts
* Freigeben von Anweisungen
* Führen Sie den allgemeinen Code aus, bevor Sie Seiten oder Ansichten rendern.

In diesem Dokument werden die Layouts für die beiden unterschiedlichen Ansätze zum ASP.net Core MVC erläutert: Razor Seiten und Controller mit Ansichten. In diesem Thema sind die Unterschiede minimal:

* Razor Seiten befinden sich im Ordner *pages* .
* Controller mit Ansichten verwenden einen Ordner namens *Views* für Ansichten.

## <a name="what-is-a-layout"></a>Was ist ein Layout?

Die meisten Web-Apps haben ein gebräuchliches Layout, das dem Benutzer beim Navigieren auf den Seiten ein konsistentes Verhalten bietet. Das Layout enthält normalerweise allgemeine Benutzeroberflächenelemente wie App-Header, Navigations- oder Menüelemente sowie eine Fußzeile.

![Beispiel für ein Seitenlayout](layout/_static/page-layout.png)

Oft verwendete HTML-Strukturen wie Skripts und Stylesheets werden auch häufig von vielen Seiten in einer App genutzt. Alle diese freigegebenen Elemente können in einer *Layoutdatei* definiert werden, auf die von jeder in der APP verwendeten Ansicht verwiesen werden kann. Layouts verringern Codeduplikate in Ansichten.

Gemäß Konvention ist *_Layout.cshtml* das Standardlayout für eine ASP.NET Core-App. Für neue ASP.NET Core-Projekte, die mit den Vorlagen erstellt wurden, sind folgende Layoutdateien vorhanden:

* Razor Seiten: *pages/Shared/_Layout. cshtml*

  ![Ordner „Pages“ im Projektmappen-Explorer](layout/_static/rp-web-project-views.png)

* Controller mit Ansichten: *Views/Shared/_Layout.cshtml*

  ![Ordner „Views“ im Projektmappen-Explorer](layout/_static/mvc-web-project-views.png)

Das Layout definiert eine übergeordnete Vorlage für die Ansichten einer App. Für Apps ist kein Layout erforderlich. Sie können allerdings mehrere Layouts mit unterschiedlichen Ansichten aufweisen.

Der folgende Code zeigt die Layoutdatei für eine Vorlage, die mit dem Projekt mit einem Controller und Ansichten erstellt wurde:

[!code-cshtml[](~/common/samples/WebApplication1/Views/Shared/_Layout.cshtml?highlight=44,72)]

## <a name="specifying-a-layout"></a>Festlegen eines Layouts

Razor Sichten verfügen über eine- `Layout` Eigenschaft. Durch Festlegen dieser Eigenschaft wird das Layout der jeweiligen Ansicht bestimmt:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml?highlight=2)]

Das angegebene Layout kann einen vollständigen Pfad (z.B. */Pages/Shared/_Layout.cshtml* oder */Views/Shared/_Layout.cshtml* ) oder einen Teil des Namens (Beispiel: `_Layout`) aufweisen. Wenn ein partieller Name angegeben wird, Razor sucht die Ansichts-Engine mithilfe Ihres Standard Ermittlungs Prozesses nach der Layoutdatei. Der Ordner, in dem sich die die Handlermethode (oder der Controller) befindet, wird zuerst durchsucht und danach der Ordner *Shared* . Dieser Ermittlungsprozess ist identisch mit dem Prozess zum Auffinden von [Teilansichten](xref:mvc/views/partial#partial-view-discovery).

Standardmäßig muss jedes Layout `RenderBody` aufrufen. Wo immer der Aufruf von `RenderBody` platziert ist, wird der Inhalt der Ansicht gerendert.

<a name="layout-sections-label"></a>
<!-- https://stackoverflow.com/questions/23327578 -->
### <a name="sections"></a>Abschnitte

Optional kann ein Layout auf mindestens einen *Abschnitt* verweisen, indem es `RenderSection` aufruft. Abschnitte geben an, wo bestimmte Seitenelemente platziert werden sollen. Jeder Aufruf von `RenderSection` kann angeben, ob dieser Abschnitt erforderlich ist:

```html
<script type="text/javascript" src="~/scripts/global.js"></script>

@RenderSection("Scripts", required: false)
```

Wenn ein erforderlicher Bereich nicht gefunden werden kann, wird eine Ausnahme ausgelöst. Einzelne Ansichten geben den Inhalt an, der in einem Abschnitt mithilfe der Syntax gerendert werden soll `@section` Razor . Wenn eine Seite oder eine Ansicht einen Abschnitt definiert, muss dieser auch gerendert werden. Andernfalls tritt ein Fehler auf.

Eine Beispiel `@section` Definition in der Razor Seitenansicht:

```html
@section Scripts {
     <script type="text/javascript" src="~/scripts/main.js"></script>
}
```

Im vorangehenden Code wird *scripts/main.js* zum Abschnitt `scripts` auf einer Seite oder in einer Ansicht hinzugefügt. Andere Seiten oder Ansichten in der gleichen App benötigen dieses Skript möglicherweise nicht und definieren einen Abschnitt zu Skripts.

In der folgenden Markupdatei wird die Datei *_ValidationScriptsPartial.cshtml* mit dem [Partial-Taghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) gerendert:

```html
@section Scripts {
    <partial name="_ValidationScriptsPartial" />
}
```

Das vorangehende Markup wurde durch [ Identity Gerüstbau ](xref:security/authentication/scaffold-identity)generiert.

Die Abschnitte, die auf einer Seite oder in einer Ansicht definiert wurden, stehen nur auf deren Layoutseite zur Verfügung. Teilansichten, Ansichtskomponenten und andere Teile eines Ansichtssystems können nicht auf sie verweisen.

### <a name="ignoring-sections"></a>Ignorieren von Abschnitten

Standardmäßig müssen der Text und die Abschnitte einer Inhaltsseite alle von der Layoutseite gerendert werden. Die Razor Ansichts-Engine erzwingt dies durch nachverfolgen, ob der Text und jeder Abschnitt gerendert wurden.

Rufen Sie die Methoden `IgnoreBody` und `IgnoreSection` auf, um die Ansichtsengine anzuweisen, den Text oder die Abschnitte zu ignorieren.

Der Text und jeder Abschnitt einer Razor Seite müssen entweder gerendert oder ignoriert werden.

<a name="viewimports"></a>

## <a name="importing-shared-directives"></a>Importieren gemeinsam verwendeter Anweisungen

Sichten und Seiten können Razor -Direktiven verwenden, um Namespaces zu importieren und [Abhängigkeitsinjektion](dependency-injection.md)zu verwenden. Anweisungen, die von mehreren Ansichten gemeinsam verwendet werden, können in einer gemeinsam verwendeten Datei namens *_ViewImports.cshtml* angegeben werden. Die `_ViewImports`-Datei unterstützt die folgenden Anweisungen:

* `@addTagHelper`
* `@removeTagHelper`
* `@tagHelperPrefix`
* `@using`
* `@model`
* `@inherits`
* `@inject`

Die Datei unterstützt keine anderen Razor Features, wie z. b. Funktionen und Abschnitts Definitionen.

Eine `_ViewImports.cshtml`-Beispieldatei:

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewImports.cshtml)]

Die Datei *_ViewImports.cshtml* für eine ASP.NET Core MVC-App befindet sich normalerweise im Ordner *Pages* (oder *Views* ). Eine Datei *_ViewImports.cshtml* kann auch in einen anderen Ordner verschoben werden. In diesem Fall wird sie nur auf die Seiten oder Ansichten in diesem Ordner und in dessen Unterordnern angewendet. `_ViewImports`-Dateien werden beginnend ab der Stammebene verarbeitet und dann einzeln für jeden Ordner bis zum Speicherort der Seite oder der Ansicht selbst. Die `_ViewImports`-Einstellungen auf der Stammebene werden möglicherweise auf Ordnerebene außer Kraft gesetzt.

Nehmen Sie beispielsweise Folgendes an:

* Die Datei *_ViewImports.cshtml* auf der Stammebene enthält `@model MyModel1` und `@addTagHelper *, MyTagHelper1`.
* Die Datei *_ViewImports.cshtml* in einem Unterordner enthält `@model MyModel2` und `@addTagHelper *, MyTagHelper2`.

Seiten und Ansichten im Unterordner haben Zugriff auf beide Taghilfsprogramme und das Modell `MyModel2`.

Wenn sich mehrere Dateien namens *_ViewImports.cshtml* in der Hierarchie befinden, umfasst das kombinierte Verhalten der Anweisungen Folgendes:

* `@addTagHelper`, `@removeTagHelper`: werden nach der Reihe ausgeführt
* `@tagHelperPrefix`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@model`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@inherits`: dasjenige, das der Ansicht am Nächsten ist, setzt die anderen außer Kraft
* `@using`: alle einbezogen; Duplikate werden ignoriert
* `@inject`: dasjenige, das der Ansicht am Nächsten ist, setzt für jede Eigenschaft alle anderen mit dem gleichen Namen außer Kraft

<a name="viewstart"></a>

## <a name="running-code-before-each-view"></a>Ausführen von Code vor jeder Ansicht

Code, der ausgeführt werden muss, bevor die einzelnen Ansichten oder Seiten in die Datei *_ViewStart.cshtml* platziert werden. Gemäß der Konvention befindet sich die Datei *_ViewStart.cshtml* im Ordner *Seiten* (oder *Ansichten* ). Die in *_ViewStart.cshtml* aufgelisteten Anweisungen werden vor jeder vollständigen Ansicht (also keine Layouts und keine Teilansichten) ausgeführt. *_ViewStart.cshtml* ist genauso wie [ViewImports.cshtml](xref:mvc/views/layout#viewimports) hierarchisch. Wenn eine Datei namens *_ViewStart.cshtml* im Ordner „Ansichten“ oder „Seiten“, der mit einem Controller verknüpft ist, definiert wird, wird sie nach derjenigen ausgeführt, die im Stamm des Ordners *Seiten* (oder *Ansichten* ) definiert wurde (falls vorhanden).

Ein Beispiel für die Datei *_ViewStart.cshtml* :

[!code-cshtml[](../../common/samples/WebApplication1/Views/_ViewStart.cshtml)]

Die oben stehende Datei gibt an, dass alle Ansichten das Layout *_Layout.cshtml* verwenden.

*_ViewStart.cshtml* und *_ViewImports.cshtml* werden in der Regel **nicht** in den Ordner */Pages/Shared* (oder */Views/Shared* ) platziert. Die Versionen dieser Dateien auf Anwendungsebene sollten direkt in den Ordner */Pages* (oder */Views* ) platziert werden.
