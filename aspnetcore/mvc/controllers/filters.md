---
title: Filter in ASP.NET Core
author: Rick-Anderson
description: Erfahren Sie, wie Filter funktionieren und wie Sie sie in ASP.NET Core verwenden.
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: 79457d55e0dcda342bc0017bb386c23525666657
ms.sourcegitcommit: 04ad9cd26fcaa8bd11e261d3661f375f5f343cdc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100107193"
---
# <a name="filters-in-aspnet-core"></a>Filter in ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.

Integrierte Filter sind für folgende Aufgaben zuständig:

* Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)
* Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)

Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden. Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.  Mit Filtern lässt sich die Duplizierung von Code vermeiden. Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.

Dieses Dokument gilt für Razor Seiten, API-Controller und Controller mit Ansichten. Filter funktionieren nicht direkt mit- [ Razor Komponenten](xref:blazor/components/index). Ein Filter kann nur indirekt Einfluss auf eine Komponente haben, wenn Folgendes gilt:

* Die Komponente ist in eine Seite oder Ansicht eingebettet.
* Die Seite oder der Controller/die Ansicht verwendet den Filter.

[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Die Funktionsweise von Filtern

Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird. Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der Aktionsaufrufpipeline verarbeitet. Die Verarbeitung von Anforderungen verläuft weiterhin durch Aktionsauswahl, Routingmiddleware und verschiedenen weiteren Middlewares, bevor eine Antwort an den Client gesendet wird.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtertypen

Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:

* [Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist. Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.

* [Ressourcen Filter](#resource-filters):

  * Werden nach der Autorisierung ausgeführt.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> führt Code vor dem Rest der Filterpipeline aus. Beispielsweise führt `OnResourceExecuting` Code vor der Modellbindung aus.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> führt Code aus, nachdem der Rest der Pipeline abgeschlossen wurde.

* [Aktionsfilter](#action-filters):

  * Sie führen Code unmittelbar vor und nach dem Aufruf einer Aktionsmethode aus.
  * Sie können die an eine Aktion übergebenen Argumente ändern.
  * Sie können das von der Aktion zurückgegebene Ergebnis ändern.
  * Werden in Seiten **nicht** unterstützt Razor .

* [Ausnahmefilter](#exception-filters) wenden globale Richtlinien auf unbehandelte Ausnahmen an, die auftreten, bevor etwas in den Antworttext geschrieben wurde.

* [Ergebnisfilter](#result-filters) führen Code unmittelbar vor und nach der Ausführung von Aktionsergebnissen aus. Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde. Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.

Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet. Beim Verlassen der Pipeline wird die Anforderung nur von Ergebnisfiltern und Ressourcenfiltern verarbeitet, bevor sie an den Client gesendet wird.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementierung

Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.

Synchrone Filter führen Code vor und nach der jeweiligen Pipelinephase aus. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird. <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> wird nach der Rückgabe der Aktionsmethode aufgerufen.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Im vorangehenden Code ist [mydebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) eine Utility-Funktion im [Beispiel Download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).

Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode. Zum Beispiel <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.

### <a name="multiple-filter-stages"></a>Mehrere Filterphasen

Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden. Beispielsweise implementiert die <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>-Klasse:

* Synchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>
* Asynchron: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide. Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf. Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf. Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen. Wenn abstrakte Klassen wie verwendet <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> werden, müssen nur die synchronen Methoden oder die asynchronen Methoden für jeden Filtertyp überschrieben werden.

### <a name="built-in-filter-attributes"></a>Integrierte Filterattribute

ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können. Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren. Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

Verwenden Sie ein Tool wie die [Browser Entwicklertools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) , um die Header zu untersuchen. Unter **Antwortheader** wird `author: Rick Anderson` angezeigt.

Mit dem folgenden Code wird ein `ActionFilterAttribute`-Element implementiert, das diese Aktionen ausführt:

* Liest den Titel und den Namen aus dem Konfigurationssystem. Im Gegensatz zum vorherigen Beispiel erfordert der folgende Code nicht, dass dem Code Filterparameter hinzugefügt werden.
* Fügt dem Antwortheader den Titel und den Namen hinzu.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

Die Konfigurationsoptionen werden vom [Konfigurationssystem](xref:fundamentals/configuration/index) mithilfe der [Optionsmuster](xref:fundamentals/configuration/options) bereitgestellt. Beispielsweise aus der *appsettings.json* Datei:

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

In `StartUp.ConfigureServices`:

* Die `PositionOptions`-Klasse wird dem Dienstcontainer mit dem Konfigurationsbereich `"Position"` hinzugefügt.
* `MyActionFilterAttribute` wird dem Dienstcontainer hinzugefügt.

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

Der folgende Code zeigt die `PositionOptions`-Klasse:

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

Der folgende Code wendet das `MyActionFilterAttribute` auf die Methode `Index2` an:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

Unter den **Antwort Headern** `author: Rick Anderson` `Editor: Joe Smith` wird und angezeigt, wenn der `Sample/Index2` Endpunkt aufgerufen wird.

Der folgende Code wendet `MyActionFilterAttribute` und `AddHeaderAttribute` auf die Seite an Razor :

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

Filter können nicht auf Razor seitenhandlermethoden angewendet werden. Sie können entweder auf das Razor Seiten Modell oder Global angewendet werden.

Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.

Filterattribute:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Filterbereiche und Reihenfolge der Ausführung

Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:

* Verwenden eines Attributs für eine Controller-Aktion. Filter Attribute können nicht auf Razor Seiten Handlermethoden angewendet werden.
* Verwenden eines Attributs auf einem Controller oder einer Razor Seite.
* Global für alle Controller, Aktionen und Razor Seiten, wie im folgenden Code gezeigt:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a>Standardreihenfolge der Ausführung

Wenn es mehrere Filter für eine bestimmte Stufe der Pipeline gibt, bestimmt der Bereich die Standardreihenfolge der Filterausführung.  Globale Filter umfassen Klassenfilter, welche hingegen Methodenfilter umfassen.

Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt. Filterreihenfolge:

* Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll
  * Der *vor* -Code von Controller-und Razor Seiten filtern.
    * Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll
    * Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll
  * Der *after* -Code von Controller-und Razor Seiten filtern.
* Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll
  
Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.

| Sequenz | Filterbereich | Filtermethode |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controller oder Razor Seite| `OnActionExecuting` |
| 3 | Methode | `OnActionExecuting` |
| 4 | Methode | `OnActionExecuted` |
| 5 | Controller oder Razor Seite | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

### <a name="controller-level-filters"></a>Filter auf Controllerebene

Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Diese Methoden führen Folgendes aus:

* Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.
* `OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.
* `OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.
* `OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen. Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.

Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.

Die `TestController`:

* Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.
* Überschreibt `OnActionExecuting` und `OnActionExecuted`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Filter auf Controllerebene legen die Eigenschaft [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) auf `int.MinValue` fest. Für Filter auf Controllerebene kann die Ausführung nach Filtern, die auf Methoden angewendet werden **nicht** festgelegt werden. Die Reihenfolge wird im nächsten Abschnitt erläutert.

Informationen zu Razor Seiten finden Sie unter [Implementieren von Seiten Razor Filtern durch Überschreiben von Filter Methoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Überschreiben der Standardreihenfolge

Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden. `IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat. Für einen Filter mit geringerem `Order`-Wert gilt:

* Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.
* Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.

Die Eigenschaft `Order` wird mit einem Konstruktorparameter festgelegt:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

Sehen Sie sich die zwei Aktionsfilter im folgenden Controller an:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

In `StartUp.ConfigureServices` wird ein globaler Filter hinzugefügt:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

Die 3 Filter werden in der folgenden Reihenfolge ausgeführt:

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich. Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst. Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest. Wie zuvor erwähnt, legen Filter auf Controllerebene die [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17)-Eigenschaft auf `int.MinValue` fest. Bei integrierten Filtern bestimmt der Umfang die Reihenfolge, es sei denn, `Order` ist auf einen Wert ungleich null festgelegt.

Im vorstehenden Code hat `MySampleActionFilter` globalen Umfang und wird daher vor `MyAction2FilterAttribute` ausgeführt, der Controllerumfang aufweist. Um `MyAction2FilterAttribute` zuerst ausführen zu lassen, legen Sie die Reihenfolge auf `int.MinValue` fest:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

Um zuerst den globalen Filter `MySampleActionFilter` ausführen zu lassen, legen Sie `Order` auf `int.MinValue` fest:

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a>Abbrechen und Kurzschließen

Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist. Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel. Die `ShortCircuitingResourceFilter`:

* Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.
* Der Filter unterbricht alle verbleibenden Pipelineschritte.

Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt. Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird. Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a>Dependency Injection

Filter können nach Typ oder Instanz hinzugefügt werden. Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet. Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert. Ein typaktivierter Filter bedeutet Folgendes:

* Für jede Anforderung wird eine Instanz erstellt.
* Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.

Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten. Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:

* Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden. 
* Dies ist eine Einschränkung der Funktionsweise von Attributen.

Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert

Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:

DI bietet Protokollierungen. Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden. Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind. Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:

* Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.
* Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden. Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert. Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.

Der folgende Code zeigt den `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:

* Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird. Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:

  * Eine einzelne Instanz des Filters wird erstellt.
  * Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.

* Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar. `CreateInstance` lädt den angegebenen Typ aus DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst. Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.

Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:

* Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.  Ihre Abhängigkeiten werden vom DI-Container erfüllt.
* `TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.

Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:
* Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde. Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.

* Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Autorisierungsfilter

Für Autorisierungsfilter gilt Folgendes:

* Sie werden als erste Filter in der Filterpipeline ausgeführt.
* Sie steuern den Zugriff auf Aktionsmethoden.
* Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.

Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework. Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben. Für den integrierten Autorisierungsfilter gilt:

* Er ruft das Autorisierungssystem auf.
* Er autorisiert keine Anforderungen.

Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:

* Die Ausnahme wird nicht behandelt.
* Ausnahmefilter behandeln die Ausnahme nicht.

Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.

Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Ressourcenfilter

Für Ressourcenfilter gilt:

* Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* Die Ausführung umschließt den größten Teil der Filterpipeline.
* Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.

Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen. Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.

Beispiele für Ressourcenfilter:

* Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Verhindert, dass die Modellbindung auf die Formulardaten zugreift.
  * Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.

## <a name="action-filters"></a>Aktionsfilter

Aktionsfilter gelten **nicht** für Razor Seiten. Razor -Seiten unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).

Für Aktionsfilter gilt:

* Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.

Der folgende Code zeigt einen Beispielaktionsfilter:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.
* <xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.

Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:

* Die Ausführung nachfolgender Filter wird verhindert.
* Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat. Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:

  * Die Ausnahme wird effektiv behandelt.
  * `Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.

Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:

* Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.
* Gibt `ActionExecutedContext` zurück.

Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.

Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.

Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:

* Überprüfen des Modellzustands.
* Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> Controller, die mit dem-Attribut kommentiert werden, überprüfen `[ApiController]` automatisch den Modell Zustand und geben eine 400-Antwort zurück. Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).

Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:

* Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat. Wenn für `Exception` NULL festgelegt wird,

  * werden Ausnahmen effektiv behandelt,
  * und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Ausnahmefilter

Für Ausnahmefilter gilt Folgendes:

* Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.
* Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.

Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Mit dem folgenden Code wird der Ausnahmefilter getestet:

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

Für Ausnahmefilter gilt Folgendes:

* Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.
* Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Behandeln von nicht behandelten Ausnahmen, die bei der Razor Erstellung von Seiten oder Controllern, [Modell Bindungen](xref:mvc/models/model-binding), Aktions filtern oder Aktionsmethoden auftreten.
* Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.

Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden. Dadurch wird die Weitergabe der Ausnahme beendet. Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln. Nur ein Aktionsfilter kann das.

Für Ausnahmefilter gilt Folgendes:

* Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.
* Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.

Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden. Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird. Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML. Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.

## <a name="result-filters"></a>Ergebnisfilter

Für Ergebnisfilter gilt:

* Implementieren einer Schnittstelle:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter und IAsyncResultFilter

Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab. Eine Aktion, die eine Ansicht zurückgibt, beinhaltet die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult>. Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch. Erfahren Sie mehr über [Aktions Ergebnisse](xref:mvc/controllers/actions).

Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert. Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:

* Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.
* Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.

Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen. Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird. Das Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` bewirkt Folgendes:

* Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.
* Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.

Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet. Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht geändert werden.

`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.

`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat. Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme und verhindert, dass die Ausnahme zu einem späteren Zeitpunkt in der Pipeline ein weiteres Mal ausgelöst wird. Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben. Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.

Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus. Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann. Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter

Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird. Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:

* Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken
* Ausnahmefilter

Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden. Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln. Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen. Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.

`IFilterFactory.IsReusable`:

* Ein Hinweis von der Factory, dass die von der Factory erstellte Filter Instanz außerhalb des Anforderungs Bereichs wieder verwendet werden kann, in dem Sie erstellt wurde.
* Sollte ***nicht*** mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als Singleton abhängig ist.

Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:

* Eine einzelne Instanz des Filters wird erstellt.
* Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.

> [!WARNING] 
> Nur so konfigurieren, dass <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> zurückgegeben `true` wird, wenn die Quelle der Filter eindeutig ist, die Filter Zustands loser sind und die Filter sicher für mehrere HTTP-Anforderungen verwendet werden können. Geben Sie beispielsweise keine Filter von di zurück, die als Bereichs bezogen oder vorübergehend registriert sind, wenn `IFilterFactory.IsReusable` zurückgibt `true` .

Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Der Filter wird im folgenden Code angewendet:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

Testen Sie den vorangehenden Code durch Ausführen des [Downloadbeispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):

* Rufen Sie die F12-Entwicklungstools auf.
* Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.

Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:

* **Autor:**`Rick Anderson`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **intern:**`My header`

Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>In einem Attribut implementierte IFilterFactory

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:

* Sie erfordern kein Übergeben von Parametern.
* Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar. `CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Verwenden von Middleware in der Filterpipeline

Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind. Filter unterscheiden sich jedoch insofern von Middleware, dass sie Teil der Runtime sind, was bedeutet, dass sie Zugriff auf Kontext und Konstrukte haben.

Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll. Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.

## <a name="next-actions"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Filter Methoden für Razor Seiten](xref:razor-pages/filter).
* Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Von [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)

*Filter* ermöglichen es in ASP.NET Core, Code vor oder nach bestimmten Phasen der Anforderungsverarbeitungspipeline auszuführen.

Integrierte Filter sind für folgende Aufgaben zuständig:

* Autorisierung (der Zugriff auf Ressourcen, für die ein Benutzer nicht autorisiert ist, wird verhindert)
* Zwischenspeicherung von Antworten (die Anforderungspipeline wird unterbrochen, damit eine zwischengespeicherte Antwort zurückgegeben wird)

Durch die Erstellung benutzerdefinierter Filter kann mit aktionsübergreifenden Problemen umgegangen werden. Zu solchen übergreifenden Problemen gehören beispielsweise Fehlerbehandlung, Caching, Konfiguration, Autorisierung und Protokollierung.  Mit Filtern lässt sich die Duplizierung von Code vermeiden. Sie können zum Beispiel die Fehlerbehandlung in einem Ausnahmefilter konsolidieren.

Dieses Dokument gilt für Razor Seiten, API-Controller und Controller mit Ansichten.

[Zeigen Sie ein Beispiel an, oder laden Sie es herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

## <a name="how-filters-work"></a>Die Funktionsweise von Filtern

Filter werden in der *ASP.NET Core-Aktionsaufrufpipeline* ausgeführt, die manchmal auch als *Filterpipeline* bezeichnet wird.  Die Filterpipeline wird ausgeführt, nachdem ASP.NET Core die auszuführende Aktion ausgewählt hat.

![Die Anforderung wird von weiterer Middleware, Routingmiddleware, Aktionsauswahl und der ASP.NET Core-Aktionsaufrufpipeline verarbeitet. Die Verarbeitung von Anforderungen verläuft weiterhin durch Aktionsauswahl, Routingmiddleware und verschiedenen weiteren Middlewares, bevor eine Antwort an den Client gesendet wird.](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a>Filtertypen

Jeder Filtertyp wird in einer anderen Phase der Filterpipeline ausgeführt:

* [Autorisierungsfilter](#authorization-filters) werden zuerst ausgeführt und dienen dazu, festzustellen, ob der Benutzer für die Anforderung berechtigt ist. Autorisierungsfilter schließen die Pipeline kurz, wenn die Anforderung nicht autorisiert ist.

* [Ressourcen Filter](#resource-filters):

  * Werden nach der Autorisierung ausgeführt.  
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> kann Code vor dem Rest der Filterpipeline ausführen. Beispielsweise kann `OnResourceExecuting` Code vor der Modellbindung ausführen.
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> kann Code nach Abschluss der restlichen Pipeline ausführen.

* [Aktionsfilter](#action-filters) können, vor und nach dem Aufruf einer individuelle Aktionsmethode, Code ausführen. Sie können zur Bearbeitung der Argumente, die an eine Aktion übermittelt werden, und des Ergebnisses, das von der Aktion zurückgegeben wird, verwendet werden. Aktionsfilter werden in Seiten **nicht** unterstützt Razor .

* [Ausnahmefilter](#exception-filters) werden dazu verwendet, globale Richtlinien auf unbehandelte Ausnahmen anzuwenden, die auftreten, bevor etwas in den Antworttext geschrieben wurde.

* [Ergebnisfilter](#result-filters) können, vor und nach dem Ergebnis einer ausgeführten individuellen Aktion, Code ausführen. Sie werden nur ausgeführt, wenn die Aktionsmethode erfolgreich ausgeführt wurde. Ergebnisfilter sind hilfreich für Logik, die die Ausführung von Ansichten oder Formatierern umfasst.

Das folgende Diagramm veranschaulicht, wie die Filtertypen mit der Filterpipeline interagieren.

![Die Anforderung wird von Autorisierungsfilter, Ressourcenfilter, Modellbindung, Aktionsfilter, Aktionsausführung sowie Aktionsergebniskonvertierung, Ausnahmefilter, Ergebnisfilter und Ergebnisausführung verarbeitet. Beim Verlassen der Pipeline wird die Anforderung nur von Ergebnisfiltern und Ressourcenfiltern verarbeitet, bevor sie an den Client gesendet wird.](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a>Implementierung

Filter unterstützen sowohl synchrone als auch asynchrone Implementierungen über verschiedene Schnittstellendefinitionen.

Synchrone Filter können Code vor (`On-Stage-Executing`) und nach (`On-Stage-Executed`) der jeweiligen Pipelinephase ausführen. `OnActionExecuting` beispielsweise wird aufgerufen, bevor die Aktionsmethode aufgerufen wird. `OnActionExecuted` wird nach der Rückgabe der Aktionsmethode aufgerufen.

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

Asynchrone Filter definieren eine `On-Stage-ExecutionAsync`-Methode:

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

Im oben stehenden Code weist der `SampleAsyncActionFilter` einen <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) auf, der die Aktionsmethode ausführt.  Jede der `On-Stage-ExecutionAsync`-Methoden akzeptiert einen `FilterType-ExecutionDelegate`, der die Pipelinephase des Filters ausführt.

### <a name="multiple-filter-stages"></a>Mehrere Filterphasen

Schnittstellen für mehrere Filterphasen können in einer einzigen Klasse implementiert werden. Beispielsweise implementiert die Klasse <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>`IActionFilter`, `IResultFilter`, und deren asynchrone Gegenstücke.

Implementieren Sie **entweder** die synchrone oder asynchrone Version einer Filterschnittstelle, aber **nicht** beide. Die Runtime prüft zuerst, ob der Filter die asynchrone Schnittstelle implementiert, und wenn dies der Fall ist, ruft die Runtime diese Schnittstelle auf. Wenn dies nicht der Fall ist, ruft es die Methode(n) der synchronen Schnittstelle auf. Wenn die asynchrone und die synchrone Schnittstelle in einer einzigen Klasse implementiert sind, wird nur die asynchrone Methode aufgerufen. Bei Verwendung von abstrakten Klassen wie <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> überschreiben Sie nur die synchronen Methoden oder die asynchrone Methode für jeden Filtertyp.

### <a name="built-in-filter-attributes"></a>Integrierte Filterattribute

ASP.NET Core enthält integrierte attributbasierte Filter, die als Unterklasse erstellt und angepasst werden können. Zum Beispiel fügt der folgende Ergebnisfilter der Antwort einen Header hinzu:

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

Wie im obigen Beispiel gezeigt, können Filter mithilfe von Attributen Argumente akzeptieren. Wenden Sie das `AddHeaderAttribute` auf einen Controller oder eine Aktionsmethode an, und geben Sie den Namen und den Wert des HTTP-Headers an:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

Einige der Filterschnittstellen besitzen entsprechende Attribute, die als Basisklassen für benutzerdefinierte Implementierungen verwendet werden können.

Filterattribute:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a>Filterbereiche und Reihenfolge der Ausführung

Der Pipeline kann in einem von drei *Bereichen* ein Filter hinzugefügt werden:

* Verwenden eines Attributs in einer Aktion
* Verwenden eines Attributs in einem Controller
* Global für alle Controller und Aktionen, wie in folgendem Code gezeigt:

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

Der oben stehende Code fügt mithilfe der Sammlung [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) global drei Filter hinzu.

### <a name="default-order-of-execution"></a>Standardreihenfolge der Ausführung

Wenn mehrere Filter *des gleichen Typs* vorhanden sind, bestimmt der Umfang die Standardreihenfolge ihrer Ausführung.  Globale Filter umschließen Klassenfilter. Klassenfilter umschließen Methodenfilter.

Als Ergebnis der Filterschachtelung wird der *nach* einer Pipelinephase auszuführende Code in umgekehrter Reihenfolge zum Code *vor* einer Pipelinephase ausgeführt. Filterreihenfolge:

* Der Code von globalen Filtern, der *vor* einer Pipelinephase ausgeführt werden soll
  * Der Code von Controllerfiltern, der *vor* einer Pipelinephase ausgeführt werden soll
    * Der Code von Aktionsmethodenfiltern, der *vor* einer Pipelinephase ausgeführt werden soll
    * Der Code von Aktionsmethodenfiltern, der *nach* einer Pipelinephase ausgeführt werden soll
  * Der Code von Controllerfiltern, der *nach* einer Pipelinephase ausgeführt werden soll
* Der Code von globalen Filtern, der *nach* einer Pipelinephase ausgeführt werden soll
  
Das folgende Beispiel veranschaulicht die Reihenfolge, in der Filtermethoden für synchrone Aktionsfilter aufgerufen werden.

| Sequenz | Filterbereich | Filtermethode |
|:--------:|:------------:|:-------------:|
| 1 | Global | `OnActionExecuting` |
| 2 | Controller | `OnActionExecuting` |
| 3 | Methode | `OnActionExecuting` |
| 4 | Methode | `OnActionExecuted` |
| 5 | Controller | `OnActionExecuted` |
| 6 | Global | `OnActionExecuted` |

Diese Sequenz veranschaulicht Folgendes:

* Der Methodenfilter ist innerhalb des Controllerfilters geschachtelt.
* Der Controllerfilter ist innerhalb des globalen Filters geschachtelt.

### <a name="controller-and-razor-page-level-filters"></a>Filter auf Controller-und Razor Seitenebene

Jeder Controller, der von der Basisklasse <xref:Microsoft.AspNetCore.Mvc.Controller> erbt, enthält die Methoden [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*), [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) und [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted`. Diese Methoden führen Folgendes aus:

* Die Filter, die für eine bestimmte Aktion ausgeführt werden, werden umschlossen.
* `OnActionExecuting` wird vor allen Filtern der Aktion aufgerufen.
* `OnActionExecuted` wird nach allen Filtern der Aktion aufgerufen.
* `OnActionExecutionAsync` wird vor allen Filtern der Aktion aufgerufen. Code im Filter nach `next` wird nach der Aktionsmethode ausgeführt.

Im Downloadbeispiel wird `MySampleActionFilter` global beim Start angewendet.

Die `TestController`:

* Wendet das `SampleActionFilterAttribute` (`[SampleActionFilter]`) auf die Aktion `FilterTest2` an.
* Überschreibt `OnActionExecuting` und `OnActionExecuted`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

Durch Navigieren zu `https://localhost:5001/Test/FilterTest2` wird der folgende Code ausgeführt:

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

Informationen zu Razor Seiten finden Sie unter [Implementieren von Seiten Razor Filtern durch Überschreiben von Filter Methoden](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).

### <a name="overriding-the-default-order"></a>Überschreiben der Standardreihenfolge

Die Standardreihenfolge der Ausführung kann durch Implementieren von <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> überschrieben werden. `IOrderedFilter` macht die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> verfügbar, die bei der Bestimmung der Ausführungsreihenfolge Vorrang vor dem Bereich hat. Für einen Filter mit geringerem `Order`-Wert gilt:

* Führt den Code, der *vor* einer Pipelinephase ausgeführt werden soll, vor dem Code eines Filters mit einem höheren Wert für `Order` aus.
* Führt den Code, der *nach* einer Pipelinephase ausgeführt werden soll, nach dem Code eines Filters mit einem höheren Wert für `Order` aus.

Die Eigenschaft `Order` kann mit einem Konstruktorparameter festgelegt werden:

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

Sehen Sie sich die drei Aktionsfilter an, die im vorherigen Beispiel gezeigt werden. Wenn die `Order`-Eigenschaft des Controllers und der globalen Filter auf 1 bzw. 2 festgelegt ist, wird die Reihenfolge der Ausführung umgekehrt.

| Sequenz | Filterbereich | `Order` -Eigenschaft | Filtermethode |
|:--------:|:------------:|:-----------------:|:-------------:|
| 1 | Methode | 0 | `OnActionExecuting` |
| 2 | Controller | 1  | `OnActionExecuting` |
| 3 | Global | 2  | `OnActionExecuting` |
| 4 | Global | 2  | `OnActionExecuted` |
| 5 | Controller | 1  | `OnActionExecuted` |
| 6 | Methode | 0  | `OnActionExecuted` |

Die Eigenschaft `Order` hat bei der Bestimmung der Reihenfolge, in der Filter ausgeführt werden, Vorrang vor dem Bereich. Filter werden erst nach der Reihenfolge sortiert, und dann werden Gleichstände über den Bereich aufgelöst. Alle integrierten Filter implementieren `IOrderedFilter` und legen den Standartwert von `Order` auf 0 fest. Bei integrierten Filtern bestimmt der Bereich die Reihenfolge, falls `Order` nicht auf einen Wert ungleich 0 festgelegt ist.

## <a name="cancellation-and-short-circuiting"></a>Abbrechen und Kurzschließen

Die Filterpipeline kann kurzgeschlossen werden, indem die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> auf den Parameter <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> festgelegt wird, der in der Filtermethode angegeben ist. Zum Beispiel verhindert der folgende Ressourcenfilter, dass der Rest der Pipeline ausgeführt wird:

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

Im folgenden Code verwenden sowohl der Filter `ShortCircuitingResourceFilter` und der Filter `AddHeader` die Aktionsmethode `SomeResource` als Ziel. Die `ShortCircuitingResourceFilter`:

* Der Filter wird zuerst ausgeführt, da es sich um einen Ressourcenfilter handelt und `AddHeader` ein Aktionsfilter ist.
* Der Filter unterbricht alle verbleibenden Pipelineschritte.

Der `AddHeader`-Filter wird daher nie für die `SomeResource`-Aktion ausgeführt. Dasselbe Verhalten würde auch dann auftreten, wenn beide Filter auf Aktionsmethodenebene angewendet würden, wenn `ShortCircuitingResourceFilter` zuerst ausgeführt wird. Der `ShortCircuitingResourceFilter` wird aufgrund seines Filtertyps oder durch die explizite Verwendung der `Order`-Eigenschaft zuerst ausgeführt.

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a>Dependency Injection

Filter können nach Typ oder Instanz hinzugefügt werden. Wenn eine Instanz hinzugefügt wird, wird diese Instanz für jede Anforderung verwendet. Wenn ein Typ hinzugefügt wird, ist der Filter typaktiviert. Ein typaktivierter Filter bedeutet Folgendes:

* Für jede Anforderung wird eine Instanz erstellt.
* Alle Konstruktorabhängigkeiten werden durch [Dependency Injection](xref:fundamentals/dependency-injection) (DI) aufgefüllt.

Filter, die als Attribute implementiert und Controllerklassen oder Aktionsmethoden direkt hinzugefügt werden, können keine Konstruktorabhängigkeiten von [Dependency Injection](xref:fundamentals/dependency-injection) (DI) erhalten. Konstruktorabhängigkeiten können aus folgenden Gründen von DI nicht bereitgestellt werden:

* Für Attribute müssen Konstruktorparameter bereitgestellt werden, wenn sie angewendet werden. 
* Dies ist eine Einschränkung der Funktionsweise von Attributen.

Die folgenden Filter unterstützen von DI bereitgestellte Konstruktorabhängigkeiten:

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>, im Attribut implementiert

Die oben genannten Filter können auf einen Controller oder eine Aktionsmethode angewendet werden:

DI bietet Protokollierungen. Vermeiden Sie es jedoch, Filter nur zum Zweck der Protokollierung zu erstellen und zu verwenden. Die integrierte [Frameworkprotokollierung](xref:fundamentals/logging/index) bietet in der Regel alle Elemente, die für die Protokollierung erforderlich sind. Wenn Protokollierung zu Filtern hinzugefügt wird, gilt Folgendes:

* Die Protokollierung sollte mit Schwerpunkt auf geschäftlichen Aspekten oder verhaltensspezifisch für den Filter erfolgen.
* Aktionen oder andere Frameworkereignisse sollten **nicht** protokolliert werden. Die integrierten Filter protokollieren Aktionen und Frameworkereignisse.

### <a name="servicefilterattribute"></a>ServiceFilterAttribute

Die Typen der Dienstfilterimplementierung werden in `ConfigureServices` registriert. Ein <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> ruft eine Instanz des Filter von DI ab.

Der folgende Code zeigt den `AddHeaderResultServiceFilter`:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Im folgenden Code wird `AddHeaderResultServiceFilter` zum DI-Container hinzugefügt:

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

Im folgenden Code ruft das `ServiceFilter`-Attribut eine Instanz des `AddHeaderResultServiceFilter`-Filters aus DI ab:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

Beim Verwenden von `ServiceFilterAttribute` wird [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) festgelegt:

* Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs, in dem sie erstellt wurde, wiederverwendet wird. Die ASP.NET Core-Runtime bietet keine Garantie für Folgendes:

  * Eine einzelne Instanz des Filters wird erstellt.
  * Der Filter wird nicht zu einem späteren Zeitpunkt vom DI-Container erneut angefordert.

* Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar. `CreateInstance` lädt den angegebenen Typ aus DI.

### <a name="typefilterattribute"></a>TypeFilterAttribute

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> ähnelt <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>. Der zugehörige Typ wird allerdings nicht direkt vom DI-Container aufgelöst. Stattdessen wird der Typ mithilfe von <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> instanziiert.

Da `TypeFilterAttribute`-Typen nicht direkt vom DI-Container aufgelöst werden, gilt Folgendes:

* Typen, auf die mithilfe von `TypeFilterAttribute` verwiesen wird, müssen nicht beim DI-Container registriert werden.  Ihre Abhängigkeiten werden vom DI-Container erfüllt.
* `TypeFilterAttribute` kann optional Konstruktorargumente für den Typ akzeptieren.

Beim Verwenden von `TypeFilterAttribute` wird [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) festgelegt:
* Gibt einen Hinweis darauf, dass die Filterinstanz *möglicherweise* außerhalb des Anforderungsbereichs wiederverwendet wird, in dem sie erstellt wurde. Die ASP.NET Core-Runtime bietet keine Garantie dafür, dass eine einzelne Instanz des Filters erstellt wird.

* Sollte nicht mit einem Filter verwendet werden, der von Diensten mit einer anderen Lebensdauer als der eines Singletons abhängig ist.

Das folgende Beispiel zeigt, wie Sie Argumente durch Verwendung von `TypeFilterAttribute` an einen Typ übergeben:

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a>Autorisierungsfilter

Für Autorisierungsfilter gilt Folgendes:

* Sie werden als erste Filter in der Filterpipeline ausgeführt.
* Sie steuern den Zugriff auf Aktionsmethoden.
* Sie verfügen nur über eine Methode, die vor der Aktion ausgeführt wird, nicht jedoch über eine Methode, die nach der Aktion ausgeführt wird.

Benutzerdefinierte Autorisierungsfilter erfordern ein benutzerdefiniertes Autorisierungsframework. Statt einen benutzerdefinierten Filter zu schreiben, sollten Sie die Autorisierungsrichtlinien konfigurieren oder eine benutzerdefinierte Autorisierungsrichtlinie schreiben. Für den integrierten Autorisierungsfilter gilt:

* Er ruft das Autorisierungssystem auf.
* Er autorisiert keine Anforderungen.

Lösen Sie **keine** Ausnahmen in Autorisierungsfiltern aus:

* Die Ausnahme wird nicht behandelt.
* Ausnahmefilter behandeln die Ausnahme nicht.

Beim Auftreten einer Ausnahme in einem Autorisierungsfilter sollten Sie eine Abfrage erwägen.

Weitere Informationen finden Sie unter [Autorisierung](xref:security/authorization/introduction).

## <a name="resource-filters"></a>Ressourcenfilter

Für Ressourcenfilter gilt:

* Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter>.
* Die Ausführung umschließt den größten Teil der Filterpipeline.
* Nur [Autorisierungsfilter](#authorization-filters) werden vor Ressourcenfiltern ausgeführt.

Ressourcenfilter sind hilfreich, um den größten Teil der Pipeline kurzzuschließen. Ein Cachingfilter kann beispielsweise bei einem Cachetreffer den Rest der Pipeline überspringen.

Beispiele für Ressourcenfilter:

* Der oben gezeigte [Ressourcenfilter zum Kurzschließen](#short-circuiting-resource-filter).
* [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):

  * Verhindert, dass die Modellbindung auf die Formulardaten zugreift.
  * Wird bei großen Dateiuploads verwendet, um zu verhindern, dass die Formulardaten in den Arbeitsspeicher eingelesen werden.

## <a name="action-filters"></a>Aktionsfilter

> [!IMPORTANT]
> Aktionsfilter gelten **nicht** für Razor Seiten. Razor -Seiten unterstützen <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> . Weitere Informationen finden Sie unter [Filtermethoden für Razor Pages](xref:razor-pages/filter).

Für Aktionsfilter gilt:

* Sie implementieren entweder die Schnittstelle <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter>.
* Ihre Ausführung umfasst die Ausführung von Aktionsmethoden.

Der folgende Code zeigt einen Beispielaktionsfilter:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> bietet folgende Eigenschaften:

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> ermöglicht das Lesen der Eingaben für eine Aktionsmethode.
* <xref:Microsoft.AspNetCore.Mvc.Controller> ermöglicht das Ändern der Controllerinstanz.
* <xref:System.Web.Mvc.ActionExecutingContext.Result>: Die Festlegung von `Result` schließt die Ausführung der Aktionsmethode und der nachfolgenden Aktionsfilter kurz.

Durch Auslösen einer Ausnahme in einer Aktionsmethode geschieht Folgendes:

* Die Ausführung nachfolgender Filter wird verhindert.
* Im Gegensatz zum Festlegen von `Result` wird die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.

<xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> bietet `Controller` und `Result` sowie die folgenden Eigenschaften:

* <xref:System.Web.Mvc.ActionExecutedContext.Canceled> ist TRUE, wenn die Aktionsausführung durch einen anderen Filter kurzgeschlossen wurde.
* <xref:System.Web.Mvc.ActionExecutedContext.Exception> ist ungleich NULL, wenn die Aktion oder ein zuvor ausgeführter Aktionsfilter eine Ausnahme ausgelöst hat. Durch Festlegen dieser Eigenschaft auf NULL geschieht Folgendes:

  * Die Ausnahme wird effektiv behandelt.
  * `Result` wird so ausgeführt, als wäre das Ergebnis von der Aktionsmethode zurückgegeben worden.

Bei einem `IAsyncActionFilter` hat der Aufruf von <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> folgende Auswirkungen:

* Alle nachfolgenden Aktionsfilter und die Aktionsmethode werden ausgeführt.
* Gibt `ActionExecutedContext` zurück.

Weisen Sie einer Ergebnisinstanz <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> zu, und rufen Sie nicht `next` (den `ActionExecutionDelegate`) auf, um die Pipeline kurzzuschließen.

Das Framework stellt ein abstraktes <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> bereit, das als Unterklasse verwendet werden kann.

Der Aktionsfilter `OnActionExecuting` kann für Folgendes verwendet werden:

* Überprüfen des Modellzustands.
* Zurückgeben eines Fehlers, wenn der Zustand ungültig ist.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

Die Methode `OnActionExecuted` wird nach der Aktionsmethode ausgeführt. Es gilt Folgendes:

* Die Methode kann die Ergebnisse der Aktion durch die Eigenschaft <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> sehen und ändern.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> wird auf TRUE festgelegt, wenn die Ausführung der Aktion durch einen anderen Filter kurzgeschlossen wurde.
* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> wird auf einen Wert ungleich NULL festgelegt, wenn die Aktion oder ein nachfolgender Aktionsfilter eine Ausnahme ausgelöst hat. Wenn für `Exception` NULL festgelegt wird,

  * werden Ausnahmen effektiv behandelt,
  * und `ActionExecutedContext.Result` wird so ausgeführt, als würde die Eigenschaft normal von der Aktionsmethode zurückgegeben werden.

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a>Ausnahmefilter

Für Ausnahmefilter gilt Folgendes:

* Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>. 
* Sie können verwendet werden, um gängige Fehlerbehandlungsrichtlinien zu implementieren.

Der folgende Beispielausnahmefilter verwendet eine benutzerdefinierte Fehleransicht, um Details zu Ausnahmen anzuzeigen, die auftreten, wenn die App sich in der Entwicklung befindet:

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

Für Ausnahmefilter gilt Folgendes:

* Sie verfügen nicht über vorangehende oder darauffolgende Ereignisse.
* Sie implementieren <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.
* Behandeln von nicht behandelten Ausnahmen, die bei der Razor Erstellung von Seiten oder Controllern, [Modell Bindungen](xref:mvc/models/model-binding), Aktions filtern oder Aktionsmethoden auftreten.
* Sie erfassen **keine** Ausnahmen, die in Ressourcenfiltern, Ergebnisfiltern oder bei der Ausführung von MVC-Ergebnissen auftreten.

Um eine Ausnahme zu behandeln, muss die Eigenschaft <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> auf `true` festgelegt oder eine Antwort geschrieben werden. Dadurch wird die Weitergabe der Ausnahme beendet. Ein Ausnahmefilter kann eine Ausnahme nicht in ein „erfolgreiches Ergebnis“ umwandeln. Nur ein Aktionsfilter kann das.

Für Ausnahmefilter gilt Folgendes:

* Sie eignen sich für das Abfangen von Ausnahmen, die in Aktionen auftreten.
* Sie sind im Vergleich zu Middleware für die Fehlerbehandlung weniger flexibel.

Sie sollten bei der Ausnahmebehandlung vorzugsweise Middleware verwenden. Verwenden Sie Ausnahmefilter nur, wenn sich die Fehlerbehandlung *unterscheidet*, je nachdem, welche Aktionsmethode aufgerufen wird. Zum Beispiel verfügt eine App möglicherweise über Aktionsmethoden für beide API-Endpunkte und für Ansichten bzw. HTML. Die API-Endpunkte können Fehlerinformationen als JSON zurückgeben, während ansichtsbasierte Aktionen eine Fehlerseite als HTML zurückgeben können.

## <a name="result-filters"></a>Ergebnisfilter

Für Ergebnisfilter gilt:

* Implementieren einer Schnittstelle:
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>
  * <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> oder <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter>
* Ihre Ausführung umfasst die Ausführung von Aktionsergebnissen.

### <a name="iresultfilter-and-iasyncresultfilter"></a>IResultFilter und IAsyncResultFilter

Der folgende Code zeigt einen Ergebnisfilter, der einen HTTP-Header hinzufügt:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

Die Art des Ergebnisses, das ausgeführt wird, hängt von der jeweiligen Aktion ab. Eine Aktion, die eine Ansicht zurückgibt, würde die gesamte Razor-Verarbeitung als Teil der Ausführung von <xref:Microsoft.AspNetCore.Mvc.ViewResult> beinhalten. Eine API-Methode führt möglicherweise eine Art Serialisierung als Teil der Ausführung des Ergebnisses durch. Erfahren Sie mehr über [Aktions Ergebnisse](xref:mvc/controllers/actions).

Ergebnisfilter werden nur ausgeführt, wenn eine Aktion oder ein Aktionsfilter ein Aktionsergebnis liefert. Ergebnisfilter werden in folgenden Fällen nicht ausgeführt:

* Ein Autorisierungs- oder Ressourcenfilter schließt die Pipeline kurz.
* Ein Ausnahmefilter behandelt eine Ausnahme durch Erzeugen eines Aktionsergebnisses.

Die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> kann die Ausführung des Aktionsergebnisses und der nachfolgenden Ergebnisfilter durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> auf `true` kurzschließen. Schreiben Sie beim Kurzschließen in das Antwortobjekt, um zu verhindern, dass eine leere Antwort generiert wird. Beim Auslösen einer Ausnahme in `IResultFilter.OnResultExecuting` geschieht Folgendes:

* Die Ausführung des Aktionsergebnisses und der nachfolgenden Filter wird verhindert.
* Die ausgelöste Ausnahme wird nicht als erfolgreiches Ergebnis, sondern als Fehler behandelt.

Wenn die <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName>-Methode ausgeführt wird, wurde die Antwort wahrscheinlich bereits an den Client gesendet. Wenn die Antwort bereits an den Client gesendet wurde, kann sie nicht mehr geändert werden.

`ResultExecutedContext.Canceled` wird auf `true` festgelegt, wenn die Ausführung des Aktionsergebnisses durch einen anderen Filter kurzgeschlossen wurde.

`ResultExecutedContext.Exception` wird auf einen Wert ungleich NULL festgelegt, wenn das Aktionsergebnis oder ein nachfolgender Ergebnisfilter eine Ausnahme ausgelöst hat. Das Festlegen von `Exception` auf NULL behandelt eine Ausnahme effektiv und verhindert, dass die Ausnahme später in der Pipeline erneut von ASP.NET Core ausgelöst wird. Beim Behandeln einer Ausnahme in einem Ergebnisfilter gibt es keine zuverlässige Möglichkeit, Daten in eine Antwort zu schreiben. Wenn ein Aktionsergebnis eine Ausnahme auslöst und die Header bereits an den Client übergeben wurden, gibt es keinen zuverlässigen Mechanismus, einen Fehlercode zu senden.

Bei einem <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter> führt ein Aufruf von `await next` im <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> alle nachfolgenden Ergebnisfilter und das Aktionsergebnis aus. Zum Kurzschließen legen Sie [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) auf `true` fest und rufen den `ResultExecutionDelegate` nicht auf:

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

Das Framework stellt ein abstraktes `ResultFilterAttribute` bereit, das als Unterklasse verwendet werden kann. Die weiter oben gezeigte Klasse [AddHeaderAttribute](#add-header-attribute) ist ein Beispiel für ein Ergebnisfilterattribut.

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a>IAlwaysRunResultFilter und IAsyncAlwaysRunResultFilter

Die Schnittstellen <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> und <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> deklarieren eine <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter>-Implementierung, die für alle Aktionsergebnisse ausgeführt wird. Dies schließt Aktionsergebnisse ein, die von folgenden Filtern generiert werden:

* Autorisierungs- und Ressourcenfilter, die einen Kurzschluss bewirken
* Ausnahmefilter

Beispielsweise wird der folgende Filter immer ausgeführt und legt ein Aktionsergebnis (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) mit dem Statuscode *422 – Einheit kann nicht bearbeitet werden* fest, wenn bei der Inhaltsaushandlung ein Fehler auftritt:

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a>IFilterFactory

<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>. Deshalb kann eine `IFilterFactory`-Instanz an einer beliebigen Stelle in der Filterpipeline als `IFilterMetadata`-Instanz verwendet werden. Wenn die Runtime den Aufruf des Filters vorbereitet, versucht sie, ihn in eine `IFilterFactory` umzuwandeln. Wenn diese Umwandlung gelingt, wird die Methode <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> aufgerufen, um die Instanz `IFilterMetadata` für den Aufruf zu erstellen. Da die exakte Filterpipeline beim Start der Anwendung nicht explizit festgelegt werden muss, wird dadurch ein sehr flexibles Design ermöglicht.

Als weiteres Verfahren zum Erstellen von Filtern kann `IFilterFactory` mithilfe von benutzerdefinierten Attributimplementierungen implementiert werden:

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

Der oben stehende Code kann durch Ausführen des [Downloadbeispiels](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) getestet werden:

* Rufen Sie die F12-Entwicklungstools auf.
* Navigieren Sie zu `https://localhost:5001/Sample/HeaderWithFactory`.

Die F12-Entwicklungstools zeigen folgende Antwortheader an, die vom Beispielcode hinzugefügt wurden:

* **Autor:**`Joe Smith`
* **globaladdheader:** `Result filter added to MvcOptions.Filters`
* **intern:**`My header`

Der oben stehende Code erstellt den Antwortheader „**internal:** `My header`“.

### <a name="ifilterfactory-implemented-on-an-attribute"></a>In einem Attribut implementierte IFilterFactory

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

Filter, die `IFilterFactory` implementieren, eignen sich in folgenden Fällen:

* Sie erfordern kein Übergeben von Parametern.
* Sie verfügen über Konstruktorabhängigkeiten, die durch DI erfüllt werden müssen.

<xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implementiert <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>. `IFilterFactory` macht die <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*>-Methode zum Erstellen einer <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>-Instanz verfügbar. `CreateInstance` lädt den angegebenen Typ aus dem Dienstecontainer (DI).

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

Der folgende Code zeigt drei Ansätze zum Anwenden von `[SampleActionFilter]`:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

Im oben stehenden Code ist das Ergänzen der Methode um `[SampleActionFilter]` der bevorzugte Ansatz zum Anwenden von `SampleActionFilter`.

## <a name="using-middleware-in-the-filter-pipeline"></a>Verwenden von Middleware in der Filterpipeline

Ressourcenfilter funktionieren wie [Middleware](xref:fundamentals/middleware/index), sie umschließen alle Ausführungen, die später in der Pipeline enthalten sind. Filter unterscheiden sich jedoch in der Hinsicht von Middleware, dass sie Teil der ASP.NET Core-Runtime sind, was bedeutet, dass sie Zugriff auf ASP.NET Core-Kontext und -Konstrukte haben.

Um Middleware als Filter zu verwenden, erstellen Sie einen Typ mit einer `Configure`-Methode, die die Middleware festlegt, die in die Filterpipeline eingefügt werden soll. Das folgende Beispiel verwendet die Lokalisierungsmiddleware, um die aktuelle Kultur einer Anforderung einzurichten:

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

Verwenden Sie das <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> zum Ausführen der Middleware:

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

Middlewarefilter werden zum selben Zeitpunkt in der Filterpipeline wie Ressourcenfilter, vor der Modellbindung und nach dem Rest der Pipeline ausgeführt.

## <a name="next-actions"></a>Nächste Schritte

* Weitere Informationen finden Sie unter [Filter Methoden für Razor Seiten](xref:razor-pages/filter).
* Um Filter näher kennenzulernen, [laden Sie das GitHub-Beispiel herunter, und testen und bearbeiten Sie es](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).

::: moniker-end
