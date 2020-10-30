---
title: Verarbeiten von Anforderungen mit Controllern in ASP.NET Core MVC
author: ardalis
description: ''
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
uid: mvc/controllers/actions
ms.openlocfilehash: a9319e74d0213b178c2a71be69a0332270d9446c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061456"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a>Verarbeiten von Anforderungen mit Controllern in ASP.NET Core MVC

Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://github.com/scottaddie)

Controller, Aktionen und Folgen von Aktionen sind ein wesentlicher Bestandteil der App-Entwicklung mit ASP.NET Core MVC.

## <a name="what-is-a-controller"></a>Was ist ein Controller?

Ein Controller wird verwendet, um mehrere Aktionen zu definieren und zu gruppieren. Eine Aktion (oder eine *Aktionsmethode* ) ist eine Methode in einem Controller, der Abfragen behandelt. Controller gruppieren ähnliche Aktionen auf logische Weise. Diese Aktionsaggregation ermöglicht das Anwenden gemeinsamer Regeln, wie z.B. für das Routing, Caching und die Autorisierung. Anforderungen werden Aktionen über [Routing](xref:mvc/controllers/routing) zugeordnet.

Per Konvention trifft Folgendes auf Controllerklassen zu:

* Sie befinden sich im Ordner *Controllers* auf Stammebene des Projekts.
* Stellen Sie sicher, dass Ihre Klasse von der Klasse `Microsoft.AspNetCore.Mvc.Controller` erbt.

Ein Controller ist eine Klasse, die instanziiert werden kann und auf die mindestens eine der folgenden Bedingungen zutrifft:

* An den Klassennamen ist `Controller` angehängt.
* Die Klasse erbt von einer Klasse, an deren Namen `Controller` angehängt ist.
* Das `[Controller]`-Attribut wird auf die Klasse angewendet.

Die Controllerklasse darf kein verknüpftes `[NonController]`-Attribut aufweisen.

Controller sollten dem [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) folgen. Zum Umsetzen dieses Prinzips gibt es mehrere Herangehensweisen. Wenn mehrere Controlleraktionen den gleichen Dienst erfordern, ziehen Sie [Constructor Injection](xref:mvc/controllers/dependency-injection#constructor-injection) zum Anfordern dieser Abhängigkeiten in Erwägung. Wenn der Dienst nur von einer einzigen Aktionsmethode benötigt wird, ist [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) möglicherweise nützlich.

Im Muster **M** odel- **V** iew- **C** ontroller ist ein Controller für die erste Verarbeitung der Anforderung und die Instanziierung des Modells zuständig. Für gewöhnlich sollten Unternehmensentscheidungen innerhalb des Modell getroffen werden.

Der Controller nimmt das Ergebnis der Verarbeitung des Modells (falls es ein Ergebnis gibt) an und gibt entweder die geeignete Ansicht mit den verknüpften Ansichtsdaten oder das Ergebnis des API-Aufrufs zurück. In den folgenden Artikeln erfahren Sie mehr: [Übersicht über ASP.NET Core MVC](xref:mvc/overview) und [Erste Schritte mit ASP.NET Core MVC und Visual Studio](xref:tutorials/first-mvc-app/start-mvc).

Der Controller ist eine Abstraktion *auf Ebene der Benutzeroberfläche* . Er ist dafür zuständig, die Gültigkeit von Anfragedaten zu gewährleisten und die zurückzugebende Ansicht auszuwählen. In gut gestalteten Apps schließt er nicht direkt Datenzugriff oder Geschäftslogik ein. Stattdessen delegiert der Controller an Dienste, die an diesen Stellen zuständig sind.

## <a name="defining-actions"></a>Definieren von Aktionen

Öffentliche Methoden in Controllern sind Aktionen, mit Ausnahme von Methoden mit `[NonAction]`-Attributen. Parameter in Aktionen werden an Anforderungsdaten gebunden und mit [Modellbindungen](xref:mvc/models/model-binding) überprüft. Die Modellüberprüfung erfolgt für alle an Modelle gebundene Elemente. Der `ModelState.IsValid`-Eigenschaftenwert gibt an, ob die Modellbindung und -überprüfung erfolgreich war.

Aktionsmethoden sollten Logik zum Zuordnen einer Anforderung zu einem Unternehmen beinhalten. Unternehmen sollten normalerweise durch einen Dienst dargestellt werden, auf die der Controller über [Dependency Injection](xref:mvc/controllers/dependency-injection) zugreift. Anschließend ordnen Aktionen das Ergebnis der Unternehmensaktion einem Anwendungszustand zu.

Aktionen können ein beliebiges Element zurückgeben. Häufig geben Sie jedoch eine Instanz von `IActionResult` (oder für async-Methoden eine Instanz von `Task<IActionResult>`) zurück, die eine Antwort erzeugt. Die Aktionsmethode ist dafür zuständig, *die Art der Antwort auszuwählen* . Das Aktionsergebnis *antwortet* .

### <a name="controller-helper-methods"></a>Hilfsmethoden von Controllern

Controller erben üblicherweise von [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), auch wenn dies nicht erforderlich ist. Das Ableiten von `Controller` ermöglicht den Zugriff auf drei verschiedene Arten von Hilfsmethoden:

#### <a name="1-methods-resulting-in-an-empty-response-body"></a>1. Methoden, die zu einem leeren Antworttext führen

Es ist kein `Content-Type`-HTTP-Antwortheader beinhaltet, da der Antworttext keinen Inhalt aufweist, der beschrieben werden könnte.

Innerhalb dieser Kategorie gibt es wiederum zwei Ergebnistypen: Redirect (Umleiten) oder HTTP-Statuscode.

* **HTTP-Statuscode**

    Dieser Typ gibt einen HTTP-Statuscode zurück. Beispiele für Hilfsmethoden dieses Typs sind `BadRequest`, `NotFound` und `Ok`. `return BadRequest();` erzeugt beispielsweise bei der Ausführung den Statuscode 400. Wenn Methoden wie `BadRequest`, `NotFound` und `Ok` überladen werden, gelten sie nicht länger als HTTP-Statuscode-Antwortdienste, da eine Inhaltsaushandlung stattfindet.

* **Umleiten**

    Dieser Typ gibt eine Umleitung an eine Aktion oder ein Ziel zurück (unter Verwendung von `Redirect`, `LocalRedirect`, `RedirectToAction` oder `RedirectToRoute`). `return RedirectToAction("Complete", new {id = 123});` leitet beispielsweise an `Complete` um und übergibt ein anonymes Objekt.

    Der Ergebnistyp „Redirect“ unterscheidet sich vom Typ „HTTP-Statuscode“ zunächst dahingehend, dass er über einen `Location`-HTTP-Antwortheader verfügt.

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a>2. Methoden, die zu einem nicht leeren Antworttext mit einem vordefinierten Inhaltstyp führen

Die meisten Hilfsmethoden in dieser Kategorie beinhalten eine `ContentType`-Eigenschaft, mit der Sie den Antwortheader `Content-Type` festlegen können, um den Antworttext zu beschreiben.

Innerhalb dieser Kategorie gibt es wiederum zwei Ergebnistypen: [View](xref:mvc/views/overview) (Ansicht) und [Formatted Response](xref:web-api/advanced/formatting) (Formatierte Antwort).

* **Anzeigen**

    Dieser Typ gibt eine Ansicht zurück, die ein Modell zum Rendern von HTML verwendet. `return View(customer);` übergibt beispielsweise zur Datenbindung ein Modell an die Ansicht.

* **Formatierte Antwort**

    Dieser Typ gibt eine Datei im JSON-Format oder in einem ähnlichen Datenaustauschformat zurück, um ein Objekt auf eine bestimmte Weise darzustellen. `return Json(customer);` serialisiert beispielsweise das bereitgestellte Objekt im JSON-Format.
    
    Weitere gängige Methoden dieses Typs sind `File` und `PhysicalFile`. `return PhysicalFile(customerFilePath, "text/xml");` gibt beispielsweise [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult) zurück.

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a>3. Methoden, die zu einem nicht leeren Antworttext in einem Inhaltstyp, der mit dem Client ausgehandelt wurde, formatieren

Diese Kategorie wird auch als **Inhaltsaushandlung** bezeichnet. Die [Inhaltsaushandlung](xref:web-api/advanced/formatting#content-negotiation) wird dann angewendet, wenn eine Aktion einen [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult)-Typ oder ein anderes Objekt, das keine [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult)-Implementierung ist, zurückgibt. Eine Aktion, die eine Implementierung zurückgibt, die nicht `IActionResult` ist (z.B. `object`), gibt auch eine formatierte Antwort zurück.

Weitere Hilfsmethoden dieses Typs sind `BadRequest`, `CreatedAtRoute` und `Ok`. `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);` und `return Ok(value);` sind Beispiele für diese Methoden. Beachten Sie, dass `BadRequest` und `Ok` Inhaltsaushandlungen nur dann durchführen, wenn ein Wert an sie übergeben wird. Wenn kein Wert übergeben wird, fungieren Sie als Ergebnistypen vom Typ HTTP-Statuscode. Andererseits führt die `CreatedAtRoute`-Methode immer Inhaltsaushandlungen durch, da ihre Überladungen alle das Übergeben eines Werts erfordern.

### <a name="cross-cutting-concerns"></a>Übergreifende Belange

Verschiedene Anwendungen haben häufig übereinstimmende Teile in Ihrem Workflow. Beispiel dafür sind Anwendungen, die eine Authentifizierung erfordern, um auf den Einkaufswagen zugreifen zu können, oder Anwendungen, die Daten auf einigen Seiten zwischenspeichern. Verwenden Sie einen *Filter* , um Logik vor oder nach einer Aktionsmethode durchzuführen. Das Verwenden von [Filtern](xref:mvc/controllers/filters) bei übergreifenden Belangen kann Duplikate minimieren.

Die meisten Filterattribute, wie z.B. `[Authorize]`, können auf Ebene des Controllers oder der Aktion mit der gewünschten Detailgenauigkeit angewendet werden.

Die Fehlerbehandlung und das Zwischenspeichern von Antworten sind häufig übergreifende Belange:
* [Fehlerbehandlung](xref:mvc/controllers/filters#exception-filters)
* [Zwischenspeichern von Antworten](xref:performance/caching/response)

Viele übergreifende Belange können mit Filtern oder mit benutzerdefinierter [Middleware](xref:fundamentals/middleware/index) behandelt werden.
