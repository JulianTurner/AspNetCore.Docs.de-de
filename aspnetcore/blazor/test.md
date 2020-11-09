---
title: 'Testen von Komponenten in ASP.NET Core Blazor'
author: guardrex
description: Erfahren Sie, wie Sie Komponenten in Blazor-Apps testen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/test
ms.openlocfilehash: cd4aee66fd6df6cc0ce520d8ca66e0a2cf130eff
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93054865"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a><span data-ttu-id="2673e-103">Testen von Komponenten in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="2673e-103">Test components in ASP.NET Core Blazor</span></span>

[<span data-ttu-id="2673e-104">Egil Hansen</span><span class="sxs-lookup"><span data-stu-id="2673e-104">Egil Hansen</span></span>](https://egilhansen.com/)

<span data-ttu-id="2673e-105">Testen ist ein wichtiger Aspekt beim Erstellen stabiler und verwaltbarer Software.</span><span class="sxs-lookup"><span data-stu-id="2673e-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="2673e-106">Um eine Blazor-Komponente zu testen, muss die *zu testende Komponente* (Component Under Test, CUT):</span><span class="sxs-lookup"><span data-stu-id="2673e-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="2673e-107">Mit relevanten Eingaben für den Test gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="2673e-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="2673e-108">Sie unterliegt abhängig vom Typ des ausgeführten Tests möglicherweise Interaktion oder Änderung.</span><span class="sxs-lookup"><span data-stu-id="2673e-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="2673e-109">Beispielsweise können Ereignishandler ausgelöst werden, z. B. ein `onclick`-Ereignis für eine Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="2673e-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="2673e-110">Auf erwartete Werte überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="2673e-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="2673e-111">Testansätze</span><span class="sxs-lookup"><span data-stu-id="2673e-111">Test approaches</span></span>

<span data-ttu-id="2673e-112">Zwei gängige Vorgehensweisen zum Testen von Blazor-Komponenten sind End-to-End-Tests (E2E) und Komponententests:</span><span class="sxs-lookup"><span data-stu-id="2673e-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="2673e-113">**Komponententests** : [Komponententests](/dotnet/core/testing/) mit einer Komponententestbibliothek geschrieben, die Folgendes bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="2673e-113">**Unit testing** : [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="2673e-114">Komponentenrendering.</span><span class="sxs-lookup"><span data-stu-id="2673e-114">Component rendering.</span></span>
  * <span data-ttu-id="2673e-115">Überprüfung der Komponentenausgabe und des -status.</span><span class="sxs-lookup"><span data-stu-id="2673e-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="2673e-116">Auslösen von Ereignishandlern und Lebenszyklusmethoden.</span><span class="sxs-lookup"><span data-stu-id="2673e-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="2673e-117">Assertionen, dass das Komponentenverhalten ordnungsgemäß ist.</span><span class="sxs-lookup"><span data-stu-id="2673e-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="2673e-118">[bUnit](https://github.com/egil/bUnit) ist ein Beispiel für eine Bibliothek, die Razor-Komponententests in ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="2673e-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="2673e-119">**E2E-Tests** : Ein Test Runner führt eine Blazor-App mit der zu testenden Komponente aus und automatisiert eine Browserinstanz.</span><span class="sxs-lookup"><span data-stu-id="2673e-119">**E2E testing** : A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="2673e-120">Das Testtool überprüft und interagiert mit der zu testenden Komponente über den Browser.</span><span class="sxs-lookup"><span data-stu-id="2673e-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="2673e-121">[Selenium](https://github.com/SeleniumHQ/selenium) ist ein Beispiel für ein E2E-Testframework, das mit Blazor-Apps verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="2673e-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="2673e-122">Bei Komponententests ist nur die Blazor-Komponente (Razor/C#) betroffen.</span><span class="sxs-lookup"><span data-stu-id="2673e-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="2673e-123">Externe Abhängigkeiten (z. B. Dienste und JS-Interop) müssen simuliert werden.</span><span class="sxs-lookup"><span data-stu-id="2673e-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="2673e-124">Bei E2E-Tests sind die Blazor-Komponente und die gesamte zusätzliche Infrastruktur Bestandteil des Tests, einschließlich CSS, JS und DOM sowie Browser-APIs.</span><span class="sxs-lookup"><span data-stu-id="2673e-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="2673e-125">Der *Testbereich* beschreibt, wie umfangreiche Tests sind.</span><span class="sxs-lookup"><span data-stu-id="2673e-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="2673e-126">Der Testbereich besitzt normalerweise Einfluss auf die Geschwindigkeit der Tests.</span><span class="sxs-lookup"><span data-stu-id="2673e-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="2673e-127">Komponententests werden für eine Teilmenge der Subsysteme der App und in der Regel innerhalb von Millisekunden ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="2673e-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="2673e-128">E2E-Tests, bei denen eine größere Gruppe der Subsysteme der App getestet wird, können einige Sekunden in Anspruch nehmen.</span><span class="sxs-lookup"><span data-stu-id="2673e-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="2673e-129">Komponententests bieten auch Zugriff auf die Instanz der zu testenden Komponente, sodass der interne Zustand der Komponente untersucht und überprüft werden kann.</span><span class="sxs-lookup"><span data-stu-id="2673e-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="2673e-130">Dies ist in E2E-Tests normalerweise nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="2673e-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="2673e-131">Im Hinblick auf die Umgebung der Komponente müssen E2E-Tests sicherstellen, dass der erwartete Umgebungszustand erreicht wurde, bevor die Überprüfung gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="2673e-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="2673e-132">Andernfalls ist das Ergebnis nicht vorhersagbar.</span><span class="sxs-lookup"><span data-stu-id="2673e-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="2673e-133">Bei Komponententests sind das Rendern der zu testenden Komponente und der Lebenszyklus des Tests besser integriert, was die Teststabilität verbessert.</span><span class="sxs-lookup"><span data-stu-id="2673e-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="2673e-134">E2E-Tests umfasst das Starten mehrerer Prozesse, Netzwerk- und Datenträger-E/A-Vorgänge sowie andere Subsystemaktivitäten, die häufig zu schlechter Testzuverlässigkeit führen.</span><span class="sxs-lookup"><span data-stu-id="2673e-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="2673e-135">Komponententests werden in der Regel von diesen Arten von Problemen isoliert.</span><span class="sxs-lookup"><span data-stu-id="2673e-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="2673e-136">In der folgenden Tabelle werden die Unterschiede zwischen den beiden Testansätzen zusammengefasst.</span><span class="sxs-lookup"><span data-stu-id="2673e-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="2673e-137">Funktion</span><span class="sxs-lookup"><span data-stu-id="2673e-137">Capability</span></span>                       | <span data-ttu-id="2673e-138">Komponententest</span><span class="sxs-lookup"><span data-stu-id="2673e-138">Unit testing</span></span>                     | <span data-ttu-id="2673e-139">E2E-Tests</span><span class="sxs-lookup"><span data-stu-id="2673e-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="2673e-140">Testbereich</span><span class="sxs-lookup"><span data-stu-id="2673e-140">Test scope</span></span>                       | <span data-ttu-id="2673e-141">Nur Blazor-Komponente (Razor/C#)</span><span class="sxs-lookup"><span data-stu-id="2673e-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="2673e-142">Blazor-Komponente (Razor/C#) mit CSS/JS</span><span class="sxs-lookup"><span data-stu-id="2673e-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="2673e-143">Testausführungszeit</span><span class="sxs-lookup"><span data-stu-id="2673e-143">Test execution time</span></span>              | <span data-ttu-id="2673e-144">Millisekunden</span><span class="sxs-lookup"><span data-stu-id="2673e-144">Milliseconds</span></span>                     | <span data-ttu-id="2673e-145">Sekunden</span><span class="sxs-lookup"><span data-stu-id="2673e-145">Seconds</span></span>                                 |
| <span data-ttu-id="2673e-146">Zugriff auf die Komponenteninstanz</span><span class="sxs-lookup"><span data-stu-id="2673e-146">Access to the component instance</span></span> | <span data-ttu-id="2673e-147">Ja</span><span class="sxs-lookup"><span data-stu-id="2673e-147">Yes</span></span>                              | <span data-ttu-id="2673e-148">Nein</span><span class="sxs-lookup"><span data-stu-id="2673e-148">No</span></span>                                      |
| <span data-ttu-id="2673e-149">Sensibel für die Umgebung</span><span class="sxs-lookup"><span data-stu-id="2673e-149">Sensitive to the environment</span></span>     | <span data-ttu-id="2673e-150">Nein</span><span class="sxs-lookup"><span data-stu-id="2673e-150">No</span></span>                               | <span data-ttu-id="2673e-151">Ja</span><span class="sxs-lookup"><span data-stu-id="2673e-151">Yes</span></span>                                     |
| <span data-ttu-id="2673e-152">Zuverlässigkeit</span><span class="sxs-lookup"><span data-stu-id="2673e-152">Reliability</span></span>                      | <span data-ttu-id="2673e-153">Zuverlässiger</span><span class="sxs-lookup"><span data-stu-id="2673e-153">More reliable</span></span>                    | <span data-ttu-id="2673e-154">Weniger zuverlässig</span><span class="sxs-lookup"><span data-stu-id="2673e-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="2673e-155">Auswählen des am besten geeigneten Testansatzes</span><span class="sxs-lookup"><span data-stu-id="2673e-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="2673e-156">Beachten Sie das Szenario, wenn Sie den Typ der auszuführenden Tests auswählen.</span><span class="sxs-lookup"><span data-stu-id="2673e-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="2673e-157">Einige Überlegungen werden in der folgenden Tabelle beschrieben.</span><span class="sxs-lookup"><span data-stu-id="2673e-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="2673e-158">Szenario</span><span class="sxs-lookup"><span data-stu-id="2673e-158">Scenario</span></span> | <span data-ttu-id="2673e-159">Vorgeschlagener Ansatz</span><span class="sxs-lookup"><span data-stu-id="2673e-159">Suggested approach</span></span> | <span data-ttu-id="2673e-160">Bemerkungen</span><span class="sxs-lookup"><span data-stu-id="2673e-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="2673e-161">Komponente ohne JS-Interop-Logik</span><span class="sxs-lookup"><span data-stu-id="2673e-161">Component without JS interop logic</span></span> | <span data-ttu-id="2673e-162">Komponententest</span><span class="sxs-lookup"><span data-stu-id="2673e-162">Unit testing</span></span> | <span data-ttu-id="2673e-163">Wenn in einer Blazor-Komponente keine Abhängigkeit von JS-Interop vorhanden ist, kann die Komponente ohne Zugriff auf JS oder die DOM-API getestet werden.</span><span class="sxs-lookup"><span data-stu-id="2673e-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="2673e-164">In diesem Szenario gibt es keine Nachteile bei der Auswahl von Komponententests.</span><span class="sxs-lookup"><span data-stu-id="2673e-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="2673e-165">Komponente mit einfacher JS-Interop-Logik</span><span class="sxs-lookup"><span data-stu-id="2673e-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="2673e-166">Komponententest</span><span class="sxs-lookup"><span data-stu-id="2673e-166">Unit testing</span></span> | <span data-ttu-id="2673e-167">Es ist üblich, dass Komponenten das DOM abfragen oder Animationen über JS-Interop auslösen.</span><span class="sxs-lookup"><span data-stu-id="2673e-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="2673e-168">Komponententests werden in diesem Szenario normalerweise bevorzugt, da es einfach ist, die JS-Interaktion über die <xref:Microsoft.JSInterop.IJSRuntime>-Schnittstelle zu simulieren.</span><span class="sxs-lookup"><span data-stu-id="2673e-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="2673e-169">Komponente, die von komplexem JS-Code abhängt</span><span class="sxs-lookup"><span data-stu-id="2673e-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="2673e-170">Komponententests und separate JS-Tests</span><span class="sxs-lookup"><span data-stu-id="2673e-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="2673e-171">Wenn eine Komponente JS-Interop verwendet, um eine große oder komplexe JS-Bibliothek aufzurufen, die Interaktion zwischen der Blazor Komponente und der JS-Bibliothek jedoch einfach ist, dann ist es wahrscheinlich die beste Vorgehensweise, die Komponente und die JS-Bibliothek oder den Code als zwei separate Teile zu behandeln und jeden Teil einzeln zu testen.</span><span class="sxs-lookup"><span data-stu-id="2673e-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="2673e-172">Testen Sie die Blazor-Komponente mit einer Komponententestbibliothek, und testen Sie JS mit einer JS-Testbibliothek.</span><span class="sxs-lookup"><span data-stu-id="2673e-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="2673e-173">Komponente mit Logik, die von der JS-Bearbeitung des Browser-DOM abhängt</span><span class="sxs-lookup"><span data-stu-id="2673e-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="2673e-174">E2E-Tests</span><span class="sxs-lookup"><span data-stu-id="2673e-174">E2E testing</span></span> | <span data-ttu-id="2673e-175">Wenn die Funktionalität einer Komponente von JS und der Bearbeitung des DOM abhängt, überprüfen Sie sowohl das JS als auch den Blazor-Code in einem E2E-Test.</span><span class="sxs-lookup"><span data-stu-id="2673e-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="2673e-176">Dies ist der Ansatz, den die Blazor-Frameworkentwickler mit der Browserrenderinglogik von Blazor verfolgt haben, bei der C#- und JS-Code eng miteinander gekoppelt sind.</span><span class="sxs-lookup"><span data-stu-id="2673e-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="2673e-177">Der C#- und der JS-Code müssen zusammenarbeiten, um Blazor-Komponenten in einem Browser ordnungsgemäß zu rendern.</span><span class="sxs-lookup"><span data-stu-id="2673e-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="2673e-178">Komponente, die von einer Drittanbieter-Komponentenbibliothek mit schwer zu simulierenden Abhängigkeiten abhängig ist</span><span class="sxs-lookup"><span data-stu-id="2673e-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="2673e-179">E2E-Tests</span><span class="sxs-lookup"><span data-stu-id="2673e-179">E2E testing</span></span> | <span data-ttu-id="2673e-180">Wenn die Funktionalität einer Komponente von einer Drittanbieter-Komponentenbibliothek abhängig ist, die schwer zu simulierende Abhängigkeiten aufweist (z. B. JS-Interop), sind E2E-Tests möglicherweise die einzige Option zum Testen der Komponente.</span><span class="sxs-lookup"><span data-stu-id="2673e-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="2673e-181">Testen von Komponenten mit bUnit</span><span class="sxs-lookup"><span data-stu-id="2673e-181">Test components with bUnit</span></span>

<span data-ttu-id="2673e-182">Es gibt kein offizielles Microsoft-Testframework für Blazor, aber das durch die Community betriebene Projekt [bUnit](https://github.com/egil/bUnit) stellt eine bequeme Möglichkeit für Komponententests von Blazor-Komponenten dar.</span><span class="sxs-lookup"><span data-stu-id="2673e-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="2673e-183">bUnit ist eine Testbibliothek von Drittanbietern und wird von Microsoft weder unterstützt noch verwaltet.</span><span class="sxs-lookup"><span data-stu-id="2673e-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="2673e-184">bUnit funktioniert mit allgemeinen Testframeworks, z. B. [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/) und [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="2673e-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="2673e-185">Diese Testframeworks lassen bUnit-Tests wie herkömmliche Komponententests aussehen und sich auch so anfühlen.</span><span class="sxs-lookup"><span data-stu-id="2673e-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="2673e-186">In ein Framework für allgemeine Tests integrierte bUnit-Tests werden normalerweise folgendermaßen ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="2673e-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="2673e-187">[Mit Test Explorer von Visual Studio](/visualstudio/test/run-unit-tests-with-test-explorer).</span><span class="sxs-lookup"><span data-stu-id="2673e-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="2673e-188">Mit dem CLI-Befehl [`dotnet test`](/dotnet/core/tools/dotnet-test) in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="2673e-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="2673e-189">Mit einer automatisierten DevOps-Testpipeline.</span><span class="sxs-lookup"><span data-stu-id="2673e-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="2673e-190">Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.</span><span class="sxs-lookup"><span data-stu-id="2673e-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="2673e-191">Anleitungen finden Sie in der Dokumentation des Testframeworks.</span><span class="sxs-lookup"><span data-stu-id="2673e-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="2673e-192">Im folgenden Beispiel wird die Struktur eines bUnit-Tests für die `Counter`-Komponente in einer App basierend auf einer Blazor-Projektvorlage veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="2673e-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="2673e-193">Die `Counter`-Komponente zeigt einen Zähler an und erhöht ihn basierend auf dem Benutzer, der eine Schaltfläche auf der Seite auswählt:</span><span class="sxs-lookup"><span data-stu-id="2673e-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

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

<span data-ttu-id="2673e-194">Der folgende bUnit-Test bestätigt, dass der Zähler der zu testenden Komponente ordnungsgemäß erhöht wird, wenn die Schaltfläche ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="2673e-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="2673e-195">Die folgenden Aktionen finden in jedem Schritt des Tests statt:</span><span class="sxs-lookup"><span data-stu-id="2673e-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="2673e-196">*Arrange* (Anordnen): Die `Counter`-Komponente wird mit `TestContext` von bUnit gerendert.</span><span class="sxs-lookup"><span data-stu-id="2673e-196">*Arrange* : The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="2673e-197">Das Absatzelement (`<p>`) der zu testenden Komponente wird gefunden und `paraElm` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="2673e-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="2673e-198">*Act* (Aktion ausführen): Das Element der Schaltfläche (`<button>`) wird gesucht und durch Aufrufen von `Click` ausgewählt, wodurch der Zähler erhöht und der Inhalt des Absatztags (`<p>`) aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="2673e-198">*Act* : The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="2673e-199">Der Text Inhalt des Absatzelements wird durch Aufrufen von `TextContent` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="2673e-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="2673e-200">*Assert* (Bestätigen): `MarkupMatches` wird für den Textinhalt aufgerufen, um zu bestätigen, dass er mit der erwarteten Zeichenfolge übereinstimmt, die `Current count: 1` lautet.</span><span class="sxs-lookup"><span data-stu-id="2673e-200">*Assert* : `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="2673e-201">Die Assert-Methode `MarkupMatches` unterscheidet sich von einer regulären Zeichenfolgenvergleichsassertion (z. B. `Assert.Equal("Current count: 1", paraElmText);`). `MarkupMatches` führt einen semantischen Vergleich der Eingaben und des erwarteten HTML-Markups durch.</span><span class="sxs-lookup"><span data-stu-id="2673e-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="2673e-202">Ein semantischer Vergleich ist sich der HTML-Semantik bewusst, d. h. Elemente wie unwichtige Leerzeichen werden ignoriert.</span><span class="sxs-lookup"><span data-stu-id="2673e-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="2673e-203">Dies führt zu stabileren Tests.</span><span class="sxs-lookup"><span data-stu-id="2673e-203">This results in more stable tests.</span></span> <span data-ttu-id="2673e-204">Weitere Informationen finden Sie unter [Anpassen des semantischen HTML-Vergleichs](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span><span class="sxs-lookup"><span data-stu-id="2673e-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2673e-205">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2673e-205">Additional resources</span></span>

* <span data-ttu-id="2673e-206">[Erste Schritte mit bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit-Anweisungen enthalten Anleitungen zum Erstellen eines Testprojekts, zum Referenzieren von Testframeworkpaketen und zum Erstellen und Ausführen von Tests.</span><span class="sxs-lookup"><span data-stu-id="2673e-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
