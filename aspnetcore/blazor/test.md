---
title: Testen von Komponenten in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Komponenten in Blazor-Apps testen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
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
uid: blazor/test
ms.openlocfilehash: 8a6fa8f25c8209584488fb2578c70e884877d666
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625868"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>Testen von Komponenten in ASP.NET Core Blazor

[Egil Hansen](https://egilhansen.com/)

Testen ist ein wichtiger Aspekt beim Erstellen stabiler und verwaltbarer Software.

Um eine Blazor-Komponente zu testen, muss die *zu testende Komponente* (Component Under Test, CUT):

* Mit relevanten Eingaben für den Test gerendert werden.
* Sie unterliegt abhängig vom Typ des ausgeführten Tests möglicherweise Interaktion oder Änderung. Beispielsweise können Ereignishandler ausgelöst werden, z. B. ein `onclick`-Ereignis für eine Schaltfläche.
* Auf erwartete Werte überprüft werden.

## <a name="test-approaches"></a>Testansätze

Zwei gängige Vorgehensweisen zum Testen von Blazor-Komponenten sind End-to-End-Tests (E2E) und Komponententests:

* **Komponententests**: [Komponententests](/dotnet/core/testing/) mit einer Komponententestbibliothek geschrieben, die Folgendes bereitstellt:
  * Komponentenrendering.
  * Überprüfung der Komponentenausgabe und des -status.
  * Auslösen von Ereignishandlern und Lebenszyklusmethoden.
  * Assertionen, dass das Komponentenverhalten ordnungsgemäß ist.

  [bUnit](https://github.com/egil/bUnit) ist ein Beispiel für eine Bibliothek, die Razor-Komponententests in ermöglicht.

* **E2E-Tests**: Ein Test Runner führt eine Blazor-App mit der zu testenden Komponente aus und automatisiert eine Browserinstanz. Das Testtool überprüft und interagiert mit der zu testenden Komponente über den Browser. [Selenium](https://github.com/SeleniumHQ/selenium) ist ein Beispiel für ein E2E-Testframework, das mit Blazor-Apps verwendet werden kann.

Bei Komponententests ist nur die Blazor-Komponente (Razor/C#) betroffen. Externe Abhängigkeiten (z. B. Dienste und JS-Interop) müssen simuliert werden. Bei E2E-Tests sind die Blazor-Komponente und die gesamte zusätzliche Infrastruktur Bestandteil des Tests, einschließlich CSS, JS und DOM sowie Browser-APIs.

Der *Testbereich* beschreibt, wie umfangreiche Tests sind. Der Testbereich besitzt normalerweise Einfluss auf die Geschwindigkeit der Tests. Komponententests werden für eine Teilmenge der Subsysteme der App und in der Regel innerhalb von Millisekunden ausgeführt. E2E-Tests, bei denen eine größere Gruppe der Subsysteme der App getestet wird, können einige Sekunden in Anspruch nehmen. 

Komponententests bieten auch Zugriff auf die Instanz der zu testenden Komponente, sodass der interne Zustand der Komponente untersucht und überprüft werden kann. Dies ist in E2E-Tests normalerweise nicht möglich.

Im Hinblick auf die Umgebung der Komponente müssen E2E-Tests sicherstellen, dass der erwartete Umgebungszustand erreicht wurde, bevor die Überprüfung gestartet wird. Andernfalls ist das Ergebnis nicht vorhersagbar. Bei Komponententests sind das Rendern der zu testenden Komponente und der Lebenszyklus des Tests besser integriert, was die Teststabilität verbessert.

E2E-Tests umfasst das Starten mehrerer Prozesse, Netzwerk- und Datenträger-E/A-Vorgänge sowie andere Subsystemaktivitäten, die häufig zu schlechter Testzuverlässigkeit führen. Komponententests werden in der Regel von diesen Arten von Problemen isoliert.

In der folgenden Tabelle werden die Unterschiede zwischen den beiden Testansätzen zusammengefasst.

| Funktion                       | Komponententest                     | E2E-Tests                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| Testbereich                       | Nur Blazor-Komponente (Razor/C#) | Blazor-Komponente (Razor/C#) mit CSS/JS |
| Testausführungszeit              | Millisekunden                     | Sekunden                                 |
| Zugriff auf die Komponenteninstanz | Ja                              | Nein                                      |
| Sensibel für die Umgebung     | Nein                               | Ja                                     |
| Zuverlässigkeit                      | Zuverlässiger                    | Weniger zuverlässig                           |

## <a name="choose-the-most-appropriate-test-approach"></a>Auswählen des am besten geeigneten Testansatzes

Beachten Sie das Szenario, wenn Sie den Typ der auszuführenden Tests auswählen. Einige Überlegungen werden in der folgenden Tabelle beschrieben.

| Szenario | Vorgeschlagener Ansatz | Bemerkungen |
| -------- | ------------------ | ------- |
| Komponente ohne JS-Interop-Logik | Komponententest | Wenn in einer Blazor-Komponente keine Abhängigkeit von JS-Interop vorhanden ist, kann die Komponente ohne Zugriff auf JS oder die DOM-API getestet werden. In diesem Szenario gibt es keine Nachteile bei der Auswahl von Komponententests. |
| Komponente mit einfacher JS-Interop-Logik | Komponententest | Es ist üblich, dass Komponenten das DOM abfragen oder Animationen über JS-Interop auslösen. Komponententests werden in diesem Szenario normalerweise bevorzugt, da es einfach ist, die JS-Interaktion über die <xref:Microsoft.JSInterop.IJSRuntime>-Schnittstelle zu simulieren. |
| Komponente, die von komplexem JS-Code abhängt | Komponententests und separate JS-Tests | Wenn eine Komponente JS-Interop verwendet, um eine große oder komplexe JS-Bibliothek aufzurufen, die Interaktion zwischen der Blazor Komponente und der JS-Bibliothek jedoch einfach ist, dann ist es wahrscheinlich die beste Vorgehensweise, die Komponente und die JS-Bibliothek oder den Code als zwei separate Teile zu behandeln und jeden Teil einzeln zu testen. Testen Sie die Blazor-Komponente mit einer Komponententestbibliothek, und testen Sie JS mit einer JS-Testbibliothek. |
| Komponente mit Logik, die von der JS-Bearbeitung des Browser-DOM abhängt | E2E-Tests | Wenn die Funktionalität einer Komponente von JS und der Bearbeitung des DOM abhängt, überprüfen Sie sowohl das JS als auch den Blazor-Code in einem E2E-Test. Dies ist der Ansatz, den die Blazor-Frameworkentwickler mit der Browserrenderinglogik von Blazor verfolgt haben, bei der C#- und JS-Code eng miteinander gekoppelt sind. Der C#- und der JS-Code müssen zusammenarbeiten, um Blazor-Komponenten in einem Browser ordnungsgemäß zu rendern.
| Komponente, die von einer Drittanbieter-Komponentenbibliothek mit schwer zu simulierenden Abhängigkeiten abhängig ist | E2E-Tests | Wenn die Funktionalität einer Komponente von einer Drittanbieter-Komponentenbibliothek abhängig ist, die schwer zu simulierende Abhängigkeiten aufweist (z. B. JS-Interop), sind E2E-Tests möglicherweise die einzige Option zum Testen der Komponente. |

## <a name="test-components-with-bunit"></a>Testen von Komponenten mit bUnit

Es gibt kein offizielles Microsoft-Testframework für Blazor, aber das durch die Community betriebene Projekt [bUnit](https://github.com/egil/bUnit) stellt eine bequeme Möglichkeit für Komponententests von Blazor-Komponenten dar.

> [!NOTE]
> bUnit ist eine Testbibliothek von Drittanbietern und wird von Microsoft weder unterstützt noch verwaltet.

bUnit funktioniert mit allgemeinen Testframeworks, z. B. [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/) und [xUnit](https://xunit.github.io/). Diese Testframeworks lassen bUnit-Tests wie herkömmliche Komponententests aussehen und sich auch so anfühlen. In ein Framework für allgemeine Tests integrierte bUnit-Tests werden normalerweise folgendermaßen ausgeführt:

* [Mit Test Explorer von Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).
* Mit dem CLI-Befehl [`dotnet test`](/dotnet/core/tools/dotnet-test) in einer Befehlsshell.
* Mit einer automatisierten DevOps-Testpipeline.

> [!NOTE]
> Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch. Anleitungen finden Sie in der Dokumentation des Testframeworks.

Im folgenden Beispiel wird die Struktur eines bUnit-Tests für die `Counter`-Komponente in einer App basierend auf einer Blazor-Projektvorlage veranschaulicht. Die `Counter`-Komponente zeigt einen Zähler an und erhöht ihn basierend auf dem Benutzer, der eine Schaltfläche auf der Seite auswählt:

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

Der folgende bUnit-Test bestätigt, dass der Zähler der zu testenden Komponente ordnungsgemäß erhöht wird, wenn die Schaltfläche ausgewählt wird:

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var cxt = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

Die folgenden Aktionen finden in jedem Schritt des Tests statt:

* *Arrange* (Anordnen): Die `Counter`-Komponente wird mit `TestContext` von bUnit gerendert. Das Absatzelement (`<p>`) der zu testenden Komponente wird gefunden und `paraElm` zugewiesen.

* *Act* (Aktion ausführen): Das Element der Schaltfläche (`<button>`) wird gesucht und durch Aufrufen von `Click` ausgewählt, wodurch der Zähler erhöht und der Inhalt des Absatztags (`<p>`) aktualisiert wird. Der Text Inhalt des Absatzelements wird durch Aufrufen von `TextContent` abgerufen.

* *Assert* (Bestätigen): `MarkupMatches` wird für den Textinhalt aufgerufen, um zu bestätigen, dass er mit der erwarteten Zeichenfolge übereinstimmt, die `Current count: 1` lautet.

> [!NOTE]
> Die Assert-Methode `MarkupMatches` unterscheidet sich von einer regulären Zeichenfolgenvergleichsassertion (z. B. `Assert.Equal("Current count: 1", paraElmText);`). `MarkupMatches` führt einen semantischen Vergleich der Eingaben und des erwarteten HTML-Markups durch. Ein semantischer Vergleich ist sich der HTML-Semantik bewusst, d. h. Elemente wie unwichtige Leerzeichen werden ignoriert. Dies führt zu stabileren Tests. Weitere Informationen finden Sie unter [Anpassen des semantischen HTML-Vergleichs](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erste Schritte mit bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit-Anweisungen enthalten Anleitungen zum Erstellen eines Testprojekts, zum Referenzieren von Testframeworkpaketen und zum Erstellen und Ausführen von Tests.
