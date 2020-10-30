---
title: Hilfsprogramm für Teiltags in ASP.NET Core
author: scottaddie
description: Lernen Sie das ASP.NET Core-Hilfsprogramm für Teiltags und die Rolle seiner Attribute beim Rendern einer Teilansicht kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 04/06/2019
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
uid: mvc/views/tag-helpers/builtin-th/partial-tag-helper
ms.openlocfilehash: 124f23caa4a757f63a80dfea627304204ba2cdca
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061430"
---
# <a name="partial-tag-helper-in-aspnet-core"></a>Hilfsprogramm für Teiltags in ASP.NET Core

Von [Scott Addie](https://github.com/scottaddie)

Eine Übersicht der Taghilfsprogramme finden Sie unter <xref:mvc/views/tag-helpers/intro>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/built-in/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="overview"></a>Überblick

Das Hilfsprogramm für partielle Tags wird zum Rendern einer [Teilansicht](xref:mvc/views/partial) in Razor Seiten und MVC-Apps verwendet. Bedenken Sie dabei Folgendes:

* Das Programm erfordert ASP.NET Core 2.1 oder höher.
* Es stellt eine Alternative zur [Syntax des HTML-Hilfsprogramms](xref:mvc/views/partial#reference-a-partial-view) dar.
* Es rendert die Teilansicht asynchron.

Folgende zählen zu den Optionen des HTML-Hilfsprogramms für das Rendern einer Teilansicht:

* [`@await Html.PartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partialasync)
* [`@await Html.RenderPartialAsync`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartialasync)
* [`@Html.Partial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.partial)
* [`@Html.RenderPartial`](/dotnet/api/microsoft.aspnetcore.mvc.rendering.htmlhelperpartialextensions.renderpartial)

Das Modell *Product* wird in den Beispielen in diesem Dokument verwendet:

[!code-csharp[](samples/TagHelpersBuiltIn/Models/Product.cs)]

Eine Auflistung der im Hilfsprogramm für Teiltags enthaltenen Attribute folgt.

## <a name="name"></a>name

Das `name`-Attribut ist erforderlich. Es gibt den Namen oder den Pfad der Teilansicht an, die gerendert werden soll. Wenn der Name einer Teilansicht bereitgestellt wird, wird der Prozess [Ansichtsermittlung](xref:mvc/views/overview#view-discovery) initiiert. Dieser Prozess wird umgangen, wenn ein expliziter Pfad bereitgestellt wird. Eine Übersicht über alle verfügbaren `name`-Werte finden Sie unter [Ermitteln von Teilansichten](xref:mvc/views/partial#partial-view-discovery).

Folgendes Markup verwendet einen expliziten Pfad, der angibt, dass *_ProductPartial.cshtml* aus dem Ordner *Shared* (Freigegeben) geladen werden soll. Wenn Sie das Attribut [for](#for) verwenden, wird an Modell zur Bindung an die Teilansicht übergeben.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Name)]

## <a name="for"></a>für

Das `for`-Attribut weist ein [ModelExpression](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.modelexpression)-Element zu, das für das aktuelle Modell ausgewertet werden soll. Ein `ModelExpression`-Element leitet die `@Model.`-Syntax ab. `for="Product"` kann beispielsweise anstelle von `for="@Model.Product"` verwendet werden. Dieses Standardverhalten für die Ableitung kann überschrieben werden, indem Sie das `@`-Symbol zum Definieren eines Inlineausdrucks verwenden.

Folgendes Markup lädt *_ProductPartial.cshtml* :

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_For)]

Die Teilansicht ist an die `Product`-Eigenschaft des zugehörigen Seitenmodells gebunden:

[!code-csharp[](samples/TagHelpersBuiltIn/Pages/Product.cshtml.cs?highlight=8)]

## <a name="model"></a>model

Das `model`-Attribut weist eine Modellinstanz zu, die an die Teilansicht übergeben werden soll. Das `model`-Attribut kann nicht mit dem [for](#for)-Attribut verwendet werden.

Im folgenden Markup wird ein neues `Product`-Objekt instanziiert und an das `model`-Attribut zur Bindung übergeben:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_Model)]

## <a name="view-data"></a>view-data

Das `view-data`-Attribut weist ein [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)-Element zu, das an die Teilansicht übergeben werden soll. Folgendes Markup stellt die gesamte ViewData-Auflistung für die Teilansicht zur Verfügung:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Product.cshtml?name=snippet_ViewData&highlight=5-)]

Im vorangehenden Code wird der Schlüsselwert `IsNumberReadOnly` auf `true` festgelegt und zur ViewData-Auflistung hinzugefügt. Folglich wird `ViewData["IsNumberReadOnly"]` innerhalb der folgenden Teilansicht zur Verfügung gestellt:

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Shared/_ProductViewDataPartial.cshtml?highlight=5)]

In diesem Beispiel bestimmt der Wert von `ViewData["IsNumberReadOnly"]`, ob das Feld *Number* (Anzahl) schreibgeschützt sein soll.

## <a name="migrate-from-an-html-helper"></a>Migrieren von einem HTML-Hilfsprogramm

Sehen Sie sich das folgende Beispiel für das Hilfsprogramm für asynchrone HTML an. Eine Sammlung von Produkten wird durchlaufen und dargestellt. Laut dem ersten Parameter der `PartialAsync`-Methode wird die Teilansicht *_ProductPartial.cshtml* geladen. Eine Instanz des `Product`-Modells wird für die Bindung an die Teilansicht übergeben.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_HtmlHelper&highlight=3)]

Das folgende Teiltaghilfsprogramm erreicht dasselbe asynchrone Renderingverhalten wie das `PartialAsync`-HTML-Hilfsprogramm. Dem `model`-Attribut wird eine `Product`-Modellinstanz zum Binden an die Teilansicht zugewiesen.

[!code-cshtml[](samples/TagHelpersBuiltIn/Pages/Products.cshtml?name=snippet_TagHelper&highlight=3)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/partial>
* <xref:mvc/views/overview#weakly-typed-data-viewdata-viewdata-attribute-and-viewbag>
