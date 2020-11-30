---
title: Testen von Controllerlogik in ASP.NET Core
author: ardalis
description: Informationen zum Testen von Controllerlogik in ASP.NET Core mit Moq und xUnit
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 7/22/2020
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
uid: mvc/controllers/testing
ms.openlocfilehash: 348b0fe4da6037933aabdb5b400d36ca073a146a
ms.sourcegitcommit: 43a540e703b9096921de27abc6b66bc0783fe905
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96320095"
---
# <a name="unit-test-controller-logic-in-aspnet-core"></a>Komponententests für die Controllerlogik in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

::: moniker range=">= aspnetcore-3.0"

[Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) beinhalten das Testen einer App-Komponente isoliert von ihrer Infrastruktur und ihren Abhängigkeiten. Bei einem Komponententest der Controllerlogik werden nur die Inhalte einer einzelnen Aktion getestet, nicht das Verhalten ihrer Abhängigkeiten oder des Frameworks selbst.

## <a name="unit-testing-controllers"></a>Komponententests für Controller

Richten Sie Komponententests für Controlleraktionen ein, um sich auf das Verhalten des Controllers zu konzentrieren. Bei einem Komponententest des Controllers werden Szenarien wie [Filter](xref:mvc/controllers/filters), [Routing](xref:fundamentals/routing) und [Modellbindung](xref:mvc/models/model-binding) vermieden. Tests, die die Interaktionen zwischen Komponenten betreffen, die gemeinsam auf eine Anforderung reagieren, werden mithilfe von *Integrationstests* behandelt. Weitere Informationen zu Integrationstests finden Sie unter <xref:test/integration-tests>.

Wenn Sie benutzerdefinierte Filter und Routen schreiben, sollten Sie für diese isoliert einen Komponententest durchführen, der nicht Bestandteil eines Tests für eine bestimmte Controlleraktion ist.

Um mehr über Komponententests für Controller zu erfahren, sehen Sie sich den folgenden Controller in der Beispiel-App an. 

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Der Homecontroller zeigt eine Liste von Brainstormingsitzungen an und ermöglicht das Erstellen neuer Brainstormingsitzungen mit einer POST-Anforderung:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Für den oben aufgeführten Controller gilt Folgendes:

* Er folgt dem [Prinzip der expliziten Abhängigkeiten](/dotnet/architecture/modern-web-apps-azure/architectural-principles#explicit-dependencies).
* Er erwartet, dass [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) eine Instanz von `IBrainstormSessionRepository` bereitstellt.
* Er kann mit einem simulierten `IBrainstormSessionRepository`-Dienst mithilfe eines Pseudoobjektframeworks wie [Moq](https://www.nuget.org/packages/Moq/) getestet werden. Ein *Pseudoobjekt* ist ein künstliches Objekt mit einem vordefiniertem Satz von Eigenschaften und Methodenverhalten, das zum Testen verwendet wird. Weitere Informationen finden Sie unter [Einführung in Integrationstests](xref:test/integration-tests#introduction-to-integration-tests).

Die `HTTP GET Index`-Methode verfügt weder über Schleifen noch über Verzweigungen und ruft nur eine Methode auf. Der Komponententest für diese Aktion:

* Simuliert den `IBrainstormSessionRepository`-Dienst mit der `GetTestSessions`-Methode. `GetTestSessions` erstellt zwei simulierte Brainstormingsitzungen mit Datumsangaben und Sitzungsnamen.
* Führt die `Index`-Methode aus.
* Nimmt Assertionen für das von der Methode zurückgegebene Ergebnis vor:
  * Ein <xref:Microsoft.AspNetCore.Mvc.ViewResult> wird zurückgegeben.
  * Das [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) ist ein `StormSessionViewModel`.
  * Es werden zwei Brainstormingsitzungen in `ViewDataDictionary.Model` gespeichert.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Die `HTTP POST Index`-Methodentests des Homecontrollers bestätigen dies:

* Wenn [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)`false` ist, gibt die Aktionsmethode den Status *400 Bad Request zurück (* <xref:Microsoft.AspNetCore.Mvc.ViewResult> mit den entsprechenden Daten).
* Wenn `ModelState.IsValid``true` ist:
  * Die `Add`-Methode für das Repository wird aufgerufen.
  * Ein <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> wird mit den richtigen Argumenten zurückgegeben.

Ein ungültiger Modellstatus wird getestet, indem mithilfe von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> (wie im ersten Test unten gezeigt) Fehler hinzugefügt werden:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Wenn [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) nicht gültig ist, wird das gleiche `ViewResult` wie für eine GET-Anforderung zurückgegeben. Der Test versucht nicht, ein ungültiges Modell zu übergeben. Das Übergeben eines ungültigen Modells ist kein gültiger Ansatz, da die Modellbindung nicht ausgeführt wird (obwohl ein [Integrationstest](xref:test/integration-tests) Modellbindung verwendet). In diesem Fall wird die Modellbindung nicht getestet. Bei diesen Komponententests wird nur der Code in der Aktionsmethode getestet.

Der zweite Test bestätigt dies, wenn der `ModelState` gültig ist:

* Eine neue `BrainstormSession` wird hinzugefügt (über das Repository).
* Die Methode gibt ein `RedirectToActionResult` mit den erwarteten Eigenschaften zurück.

Simulierte Aufrufe, die nicht aufgerufen werden, werden normalerweise ignoriert. Durch Aufrufen von `Verifiable` am Ende des Einrichtungsaufrufs wird jedoch eine Pseudoüberprüfung im Test ermöglicht. Dies erfolgt mit dem Aufruf von `mockRepo.Verify`. Damit schlägt der Test fehl, wenn die erwartete Methode nicht aufgerufen wurde.

> [!NOTE]
> Mit der in diesem Beispiel verwendeten Moq-Bibliothek können überprüfbare (oder „strikte“) Pseudoobjekte mit nicht überprüfbaren Pseudoobjekten (auch „nicht-strikte“ Pseudoobjekte oder „Stubs“ genannt) kombiniert werden. Weitere Informationen finden Sie unter [Customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior) (Anpassen des Verhaltens von Pseudoobjekten mit Moq).

In der Beispiel-App werden Informationen zu einer bestimmten Brainstormingsitzung mit [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) angezeigt. Der Controller enthält Logik, um ungültige `id` Werte zu behandeln (es gibt im folgenden Beispiel zwei `return`-Szenarien, die diese Fälle abdecken). Die endgültige `return`-Anweisung gibt ein neues `StormSessionViewModel`-Objekt an die Ansicht zurück (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Die Komponententests enthalten einen Test für jedes `return`-Szenario in der `Index`-Aktion des Sitzungscontrollers:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Mit dem Wechsel zum Ideas-Controller stellt die App Funktionalität als Web-API auf der `api/ideas`-Route zur Verfügung:

* Eine Liste von Ideen (`IdeaDTO`), die einer Brainstormingsitzung zugeordnet sind, wird von der `ForSession`-Methode zurückgegeben.
* Die `Create`-Methode fügt einer Sitzung neue Ideen hinzu.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vermeiden Sie die Rückgabe von Geschäftsdomänenentitäten direkt über API-Aufrufe. Für Domänenentitäten gilt Folgendes:

* Sie enthalten häufig mehr Daten als für den Client erforderlich.
* Sie koppeln unnötigerweise das interne Domänenmodell der App mit der öffentlich bereitgestellten API.

Die Zuordnung zwischen Domänenentitäten und den Typen, die an den Client zurückgegeben werden, kann ausgeführt werden:

* Manuell mit einer LINQ-`Select`-Anweisung, wie sie von der Beispiel-App verwendet wird. Weitere Informationen finden Sie unter [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automatisch mit einer Bibliothek, z.B. mit [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Anschließend demonstriert die Beispiel-App Komponententests für die API-Methoden `Create` und `ForSession` des Ideas-Controllers.

Die Beispiel-App enthält zwei `ForSession`-Tests. Der erste Test ermittelt, ob `ForSession` ein <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) für eine ungültige Sitzung zurückgibt:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Die zweite `ForSession`-Test ermittelt, ob `ForSession` eine Liste der Sitzungsideen (`<List<IdeaDTO>>`) für eine gültige Sitzung zurückgibt. Die Tests untersuchen auch die erste Idee, um zu bestätigen, dass ihre `Name`-Eigenschaft richtig ist:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Um das Verhalten der `Create`-Methode zu testen, wenn der `ModelState` ungültig ist, fügt die Beispiel-App dem Controller einen Modellfehler als Teil des Tests hinzu. Versuchen Sie nicht, die Modellüberprüfung oder Modellbindung in Komponententests zu testen. Testen Sie einfach das Verhalten der Aktionsmethode, wenn Sie mit einem ungültigen `ModelState` konfrontiert wird:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Der zweite Test von `Create` hängt davon ab, ob das Repository `null` zurückgibt. Daher wird das Pseudorepository so konfiguriert, dass es `null` zurückgibt. Es ist nicht erforderlich, eine Testdatenbank (im Arbeitsspeicher oder anderweitig) zu erstellen und eine Abfrage zu generieren, die dieses Ergebnis zurückgibt. Der Test kann mit einer einzigen Anweisung ausgeführt werden, wie der Beispielcode veranschaulicht:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Im dritten `Create`-Test (`Create_ReturnsNewlyCreatedIdeaForSession`) wird überprüft, ob die `UpdateAsync`-Methode des Repositorys aufgerufen wird. Das Pseudoobjekt wird mit `Verifiable` aufgerufen. Anschließend wird die `Verify`-Methode des Pseudorepositorys aufgerufen, um zu bestätigen, dass die überprüfbare Methode ausgeführt wurde. Sicherzustellen, dass die Daten von der `UpdateAsync`-Methode gespeichert wurden, gehört nicht zu den Aufgaben des Komponententests. Dies kann mit einem Integrationstest bestätigt werden.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test-Aktions Ergebnis\<T>

In ASP.net Core 2,1 oder höher können Sie mit " [aktionresult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> )" einen Typ zurückgeben, der von abgeleitet wird, `ActionResult` oder einen bestimmten Typ zurückgeben.

Die Beispielanwendung enthält eine Methode, die ein `List<IdeaDTO>` für eine bestimmte Sitzung `id` zurückgibt. Wenn die Sitzung `id` nicht vorhanden ist, gibt der Controller <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> zurück:

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Zwei Tests des `ForSessionActionResult`-Controllers sind in `ApiIdeasControllerTests` vorhanden.

Der erste Test bestätigt, dass der Controller ein `ActionResult`, aber keine nicht vorhandene Liste von Ideen für eine nicht vorhandene Sitzungs-`id` zurückgibt:

* Der Typ von `ActionResult` ist `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> ist ein <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Für eine gültige Sitzungs-`id` bestätigt der zweite Test, dass die Methode Folgendes zurückgibt:

* Ein `ActionResult` mit einem `List<IdeaDTO>`-Typ.
* Das [Aktions Ergebnis \<T> . Der Wert](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) ist ein- `List<IdeaDTO>` Typ.
* Das erste Element in der Liste ist eine gültige Idee, die der in der Pseudositzung gespeicherten Idee entspricht (abgerufen durch den Aufruf von `GetTestSession`).

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Die Beispiel-App enthält auch eine Methode zum Erstellen einer neuen `Idea` für eine bestimmte Sitzung. Der Controller gibt Folgendes zurück:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> für ein ungültiges Modell.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, wenn die Sitzung nicht vorhanden ist.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, wenn die Sitzung mit der neuen Idee aktualisiert wird.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Drei Tests von `CreateActionResult` sind in `ApiIdeasControllerTests` enthalten.

Der erste Test bestätigt, dass eine <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> für ein ungültiges Modell zurückgegeben wird.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Der zweite Test überprüft, ob ein <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>-Element zurückgegeben wird, wenn die Sitzung nicht vorhanden ist.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Für eine gültige Sitzungs-`id` bestätigt der letzte Test Folgendes:

* Die Methode gibt ein `ActionResult` mit einem `BrainstormSession`-Typ zurück.
* Das [Aktions Ergebnis \<T> . Das Ergebnis](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) ist ein <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` ist analog zu einer *201 Created*-Antwort mit einem `Location`-Header.
* Das [Aktions Ergebnis \<T> . Der Wert](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) ist ein- `BrainstormSession` Typ.
* Der Pseudoaufruf zum Aktualisieren der Sitzung (`UpdateAsync(testSession)`) wurde aufgerufen. Der `Verifiable`-Methodenaufruf wird überprüft, indem `mockRepo.Verify()` in den Assertionen ausgeführt wird.
* Zwei `Idea`-Objekte werden für die Sitzung zurückgegeben.
* Das letzte Element (die `Idea`, die durch den Pseudoaufruf `UpdateAsync` hinzugefügt wurde) stimmt mit der `newIdea` überein, die der Sitzung im Test hinzugefügt wurde.

[!code-csharp[](testing/samples/3.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Controller](xref:mvc/controllers/actions) spielen in jeder ASP.NET Core MVC-App eine zentrale Rolle. Daher sollten Sie sich auch darauf verlassen können, dass Controller in Ihrer App wie beabsichtigt funktionieren. Automatisierte Tests können Fehler erkennen, bevor die App in einer Produktionsumgebung bereitgestellt wird.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/testing/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="unit-tests-of-controller-logic"></a>Komponententests der Controllerlogik

[Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) beinhalten das Testen einer App-Komponente isoliert von ihrer Infrastruktur und ihren Abhängigkeiten. Bei einem Komponententest der Controllerlogik werden nur die Inhalte einer einzelnen Aktion getestet, nicht das Verhalten ihrer Abhängigkeiten oder des Frameworks selbst.

Richten Sie Komponententests für Controlleraktionen ein, um sich auf das Verhalten des Controllers zu konzentrieren. Bei einem Komponententest des Controllers werden Szenarien wie [Filter](xref:mvc/controllers/filters), [Routing](xref:fundamentals/routing) und [Modellbindung](xref:mvc/models/model-binding) vermieden. Tests, die die Interaktionen zwischen Komponenten betreffen, die gemeinsam auf eine Anforderung reagieren, werden mithilfe von *Integrationstests* behandelt. Weitere Informationen zu Integrationstests finden Sie unter <xref:test/integration-tests>.

Wenn Sie benutzerdefinierte Filter und Routen schreiben, sollten Sie für diese isoliert einen Komponententest durchführen, der nicht Bestandteil eines Tests für eine bestimmte Controlleraktion ist.

Um mehr über Komponententests für Controller zu erfahren, sehen Sie sich den folgenden Controller in der Beispiel-App an. Der Homecontroller zeigt eine Liste von Brainstormingsitzungen an und ermöglicht das Erstellen neuer Brainstormingsitzungen mit einer POST-Anforderung:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/HomeController.cs?name=snippet_HomeController&highlight=1,5,10,31-32)]

Für den oben aufgeführten Controller gilt Folgendes:

* Er folgt dem [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).
* Er erwartet, dass [Abhängigkeitsinjektion (Dependency Injection, DI)](xref:fundamentals/dependency-injection) eine Instanz von `IBrainstormSessionRepository` bereitstellt.
* Er kann mit einem simulierten `IBrainstormSessionRepository`-Dienst mithilfe eines Pseudoobjektframeworks wie [Moq](https://www.nuget.org/packages/Moq/) getestet werden. Ein *Pseudoobjekt* ist ein künstliches Objekt mit einem vordefiniertem Satz von Eigenschaften und Methodenverhalten, das zum Testen verwendet wird. Weitere Informationen finden Sie unter [Einführung in Integrationstests](xref:test/integration-tests#introduction-to-integration-tests).

Die `HTTP GET Index`-Methode verfügt weder über Schleifen noch über Verzweigungen und ruft nur eine Methode auf. Der Komponententest für diese Aktion:

* Simuliert den `IBrainstormSessionRepository`-Dienst mit der `GetTestSessions`-Methode. `GetTestSessions` erstellt zwei simulierte Brainstormingsitzungen mit Datumsangaben und Sitzungsnamen.
* Führt die `Index`-Methode aus.
* Nimmt Assertionen für das von der Methode zurückgegebene Ergebnis vor:
  * Ein <xref:Microsoft.AspNetCore.Mvc.ViewResult> wird zurückgegeben.
  * Das [ViewDataDictionary.Model](xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary.Model*) ist ein `StormSessionViewModel`.
  * Es werden zwei Brainstormingsitzungen in `ViewDataDictionary.Model` gespeichert.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_Index_ReturnsAViewResult_WithAListOfBrainstormSessions&highlight=14-17)]

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_GetTestSessions)]

Die `HTTP POST Index`-Methodentests des Homecontrollers bestätigen dies:

* Wenn [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid*)`false` ist, gibt die Aktionsmethode den Status *400 Bad Request zurück (* <xref:Microsoft.AspNetCore.Mvc.ViewResult> mit den entsprechenden Daten).
* Wenn `ModelState.IsValid``true` ist:
  * Die `Add`-Methode für das Repository wird aufgerufen.
  * Ein <xref:Microsoft.AspNetCore.Mvc.RedirectToActionResult> wird mit den richtigen Argumenten zurückgegeben.

Ein ungültiger Modellstatus wird getestet, indem mithilfe von <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> (wie im ersten Test unten gezeigt) Fehler hinzugefügt werden:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/HomeControllerTests.cs?name=snippet_ModelState_ValidOrInvalid&highlight=9,16-17,38-41)]

Wenn [ModelState](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) nicht gültig ist, wird das gleiche `ViewResult` wie für eine GET-Anforderung zurückgegeben. Der Test versucht nicht, ein ungültiges Modell zu übergeben. Das Übergeben eines ungültigen Modells ist kein gültiger Ansatz, da die Modellbindung nicht ausgeführt wird (obwohl ein [Integrationstest](xref:test/integration-tests) Modellbindung verwendet). In diesem Fall wird die Modellbindung nicht getestet. Bei diesen Komponententests wird nur der Code in der Aktionsmethode getestet.

Der zweite Test bestätigt dies, wenn der `ModelState` gültig ist:

* Eine neue `BrainstormSession` wird hinzugefügt (über das Repository).
* Die Methode gibt ein `RedirectToActionResult` mit den erwarteten Eigenschaften zurück.

Simulierte Aufrufe, die nicht aufgerufen werden, werden normalerweise ignoriert. Durch Aufrufen von `Verifiable` am Ende des Einrichtungsaufrufs wird jedoch eine Pseudoüberprüfung im Test ermöglicht. Dies erfolgt mit dem Aufruf von `mockRepo.Verify`. Damit schlägt der Test fehl, wenn die erwartete Methode nicht aufgerufen wurde.

> [!NOTE]
> Mit der in diesem Beispiel verwendeten Moq-Bibliothek können überprüfbare (oder „strikte“) Pseudoobjekte mit nicht überprüfbaren Pseudoobjekten (auch „nicht-strikte“ Pseudoobjekte oder „Stubs“ genannt) kombiniert werden. Weitere Informationen finden Sie unter [Customizing Mock behavior with Moq](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior) (Anpassen des Verhaltens von Pseudoobjekten mit Moq).

In der Beispiel-App werden Informationen zu einer bestimmten Brainstormingsitzung mit [SessionController](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs) angezeigt. Der Controller enthält Logik, um ungültige `id` Werte zu behandeln (es gibt im folgenden Beispiel zwei `return`-Szenarien, die diese Fälle abdecken). Die endgültige `return`-Anweisung gibt ein neues `StormSessionViewModel`-Objekt an die Ansicht zurück (*Controllers/SessionController.cs*):

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Controllers/SessionController.cs?name=snippet_SessionController&highlight=12-16,18-22,31)]

Die Komponententests enthalten einen Test für jedes `return`-Szenario in der `Index`-Aktion des Sitzungscontrollers:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/SessionControllerTests.cs?name=snippet_SessionControllerTests&highlight=2,11-14,18,31-32,36,50-55)]

Mit dem Wechsel zum Ideas-Controller stellt die App Funktionalität als Web-API auf der `api/ideas`-Route zur Verfügung:

* Eine Liste von Ideen (`IdeaDTO`), die einer Brainstormingsitzung zugeordnet sind, wird von der `ForSession`-Methode zurückgegeben.
* Die `Create`-Methode fügt einer Sitzung neue Ideen hinzu.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionAndCreate&highlight=1-2,21-22)]

Vermeiden Sie die Rückgabe von Geschäftsdomänenentitäten direkt über API-Aufrufe. Für Domänenentitäten gilt Folgendes:

* Sie enthalten häufig mehr Daten als für den Client erforderlich.
* Sie koppeln unnötigerweise das interne Domänenmodell der App mit der öffentlich bereitgestellten API.

Die Zuordnung zwischen Domänenentitäten und den Typen, die an den Client zurückgegeben werden, kann ausgeführt werden:

* Manuell mit einer LINQ-`Select`-Anweisung, wie sie von der Beispiel-App verwendet wird. Weitere Informationen finden Sie unter [LINQ (Language Integrated Query)](/dotnet/standard/using-linq).
* Automatisch mit einer Bibliothek, z.B. mit [AutoMapper](https://github.com/AutoMapper/AutoMapper).

Anschließend demonstriert die Beispiel-App Komponententests für die API-Methoden `Create` und `ForSession` des Ideas-Controllers.

Die Beispiel-App enthält zwei `ForSession`-Tests. Der erste Test ermittelt, ob `ForSession` ein <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult> (HTTP Not Found) für eine ungültige Sitzung zurückgibt:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests4&highlight=5,7-8,15-16)]

Die zweite `ForSession`-Test ermittelt, ob `ForSession` eine Liste der Sitzungsideen (`<List<IdeaDTO>>`) für eine gültige Sitzung zurückgibt. Die Tests untersuchen auch die erste Idee, um zu bestätigen, dass ihre `Name`-Eigenschaft richtig ist:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests5&highlight=5,7-8,15-18)]

Um das Verhalten der `Create`-Methode zu testen, wenn der `ModelState` ungültig ist, fügt die Beispiel-App dem Controller einen Modellfehler als Teil des Tests hinzu. Versuchen Sie nicht, die Modellüberprüfung oder Modellbindung in Komponententests zu testen. Testen Sie einfach das Verhalten der Aktionsmethode, wenn Sie mit einem ungültigen `ModelState` konfrontiert wird:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests1&highlight=7,13)]

Der zweite Test von `Create` hängt davon ab, ob das Repository `null` zurückgibt. Daher wird das Pseudorepository so konfiguriert, dass es `null` zurückgibt. Es ist nicht erforderlich, eine Testdatenbank (im Arbeitsspeicher oder anderweitig) zu erstellen und eine Abfrage zu generieren, die dieses Ergebnis zurückgibt. Der Test kann mit einer einzigen Anweisung ausgeführt werden, wie der Beispielcode veranschaulicht:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests2&highlight=7-8,15)]

Im dritten `Create`-Test (`Create_ReturnsNewlyCreatedIdeaForSession`) wird überprüft, ob die `UpdateAsync`-Methode des Repositorys aufgerufen wird. Das Pseudoobjekt wird mit `Verifiable` aufgerufen. Anschließend wird die `Verify`-Methode des Pseudorepositorys aufgerufen, um zu bestätigen, dass die überprüfbare Methode ausgeführt wurde. Sicherzustellen, dass die Daten von der `UpdateAsync`-Methode gespeichert wurden, gehört nicht zu den Aufgaben des Komponententests. Dies kann mit einem Integrationstest bestätigt werden.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ApiIdeasControllerTests3&highlight=20-22,28-33)]

## <a name="test-actionresultt"></a>Test-Aktions Ergebnis\<T>

In ASP.net Core 2,1 oder höher können Sie mit " [aktionresult \<T> ](xref:web-api/action-return-types#actionresultt-type) ( <xref:Microsoft.AspNetCore.Mvc.ActionResult%601> )" einen Typ zurückgeben, der von abgeleitet wird, `ActionResult` oder einen bestimmten Typ zurückgeben.

Die Beispielanwendung enthält eine Methode, die ein `List<IdeaDTO>` für eine bestimmte Sitzung `id` zurückgibt. Wenn die Sitzung `id` nicht vorhanden ist, gibt der Controller <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> zurück:

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_ForSessionActionResult&highlight=10,21)]

Zwei Tests des `ForSessionActionResult`-Controllers sind in `ApiIdeasControllerTests` vorhanden.

Der erste Test bestätigt, dass der Controller ein `ActionResult`, aber keine nicht vorhandene Liste von Ideen für eine nicht vorhandene Sitzungs-`id` zurückgibt:

* Der Typ von `ActionResult` ist `ActionResult<List<IdeaDTO>>`.
* <xref:Microsoft.AspNetCore.Mvc.ActionResult`1.Result*> ist ein <xref:Microsoft.AspNetCore.Mvc.NotFoundObjectResult>.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=7,10,13-14)]

Für eine gültige Sitzungs-`id` bestätigt der zweite Test, dass die Methode Folgendes zurückgibt:

* Ein `ActionResult` mit einem `List<IdeaDTO>`-Typ.
* Das [Aktions Ergebnis \<T> . Der Wert](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) ist ein- `List<IdeaDTO>` Typ.
* Das erste Element in der Liste ist eine gültige Idee, die der in der Pseudositzung gespeicherten Idee entspricht (abgerufen durch den Aufruf von `GetTestSession`).

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_ForSessionActionResult_ReturnsIdeasForSession&highlight=7-8,15-18)]

Die Beispiel-App enthält auch eine Methode zum Erstellen einer neuen `Idea` für eine bestimmte Sitzung. Der Controller gibt Folgendes zurück:

* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> für ein ungültiges Modell.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>, wenn die Sitzung nicht vorhanden ist.
* <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>, wenn die Sitzung mit der neuen Idee aktualisiert wird.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/src/TestingControllersSample/Api/IdeasController.cs?name=snippet_CreateActionResult&highlight=9,16,29)]

Drei Tests von `CreateActionResult` sind in `ApiIdeasControllerTests` enthalten.

Der erste Test bestätigt, dass eine <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> für ein ungültiges Modell zurückgegeben wird.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsBadRequest_GivenInvalidModel&highlight=7,13-14)]

Der zweite Test überprüft, ob ein <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>-Element zurückgegeben wird, wenn die Sitzung nicht vorhanden ist.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNotFoundObjectResultForNonexistentSession&highlight=5,15,22-23)]

Für eine gültige Sitzungs-`id` bestätigt der letzte Test Folgendes:

* Die Methode gibt ein `ActionResult` mit einem `BrainstormSession`-Typ zurück.
* Das [Aktions Ergebnis \<T> . Das Ergebnis](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Result*) ist ein <xref:Microsoft.AspNetCore.Mvc.CreatedAtActionResult> . `CreatedAtActionResult` ist analog zu einer *201 Created*-Antwort mit einem `Location`-Header.
* Das [Aktions Ergebnis \<T> . Der Wert](xref:Microsoft.AspNetCore.Mvc.ActionResult%601.Value*) ist ein- `BrainstormSession` Typ.
* Der Pseudoaufruf zum Aktualisieren der Sitzung (`UpdateAsync(testSession)`) wurde aufgerufen. Der `Verifiable`-Methodenaufruf wird überprüft, indem `mockRepo.Verify()` in den Assertionen ausgeführt wird.
* Zwei `Idea`-Objekte werden für die Sitzung zurückgegeben.
* Das letzte Element (die `Idea`, die durch den Pseudoaufruf `UpdateAsync` hinzugefügt wurde) stimmt mit der `newIdea` überein, die der Sitzung im Test hinzugefügt wurde.

[!code-csharp[](testing/samples/2.x/TestingControllersSample/tests/TestingControllersSample.Tests/UnitTests/ApiIdeasControllerTests.cs?name=snippet_CreateActionResult_ReturnsNewlyCreatedIdeaForSession&highlight=20-22,28-34)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/integration-tests>
* [Erstellen und Ausführen von Komponententests mit Visual Studio](/visualstudio/test/unit-test-your-code)
* [Mytesting. aspnetcore. MVC-fließende Test Bibliothek für ASP.net Core MVC](https://github.com/ivaylokenov/MyTested.AspNetCore.Mvc): stark typisierte unittestbibliothek, die eine fließende Oberfläche zum Testen von MVC-und Web-API-apps bereitstellt. (*Wird von Microsoft nicht verwaltet oder unterstützt.* )
* [JustMockLite:](https://github.com/telerik/JustMockLite) Dies ist ein Beispielframework für .NET-Entwickler. (*Wird von Microsoft nicht verwaltet oder unterstützt.* )

