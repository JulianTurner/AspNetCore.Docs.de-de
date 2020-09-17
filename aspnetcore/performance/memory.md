---
title: Speicherverwaltung und Muster in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Arbeitsspeicher in ASP.net Core verwaltet wird und wie die Garbage Collector (GC) funktioniert.
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
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
uid: performance/memory
ms.openlocfilehash: 7f1d20687f6dd588e125acf3815815c2bcf0cd04
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722682"
---
# <a name="memory-management-and-garbage-collection-gc-in-aspnet-core"></a><span data-ttu-id="e719c-103">Speicherverwaltung und Garbage Collection (GC) in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e719c-103">Memory management and garbage collection (GC) in ASP.NET Core</span></span>

<span data-ttu-id="e719c-104">Von [Sébastien Ros](https://github.com/sebastienros) und [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e719c-104">By [Sébastien Ros](https://github.com/sebastienros) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="e719c-105">Die Speicherverwaltung ist auch in verwalteten Frameworks wie .net Komplex.</span><span class="sxs-lookup"><span data-stu-id="e719c-105">Memory management is complex, even in a managed framework like .NET.</span></span> <span data-ttu-id="e719c-106">Das analysieren und verstehen von Speicherproblemen kann eine Herausforderung darstellen.</span><span class="sxs-lookup"><span data-stu-id="e719c-106">Analyzing and understanding memory issues can be challenging.</span></span> <span data-ttu-id="e719c-107">Inhalt dieses Artikels</span><span class="sxs-lookup"><span data-stu-id="e719c-107">This article:</span></span>

* <span data-ttu-id="e719c-108">Wurde von vielen *Speicherlecks* und *GC* -Fehlern motiviert.</span><span class="sxs-lookup"><span data-stu-id="e719c-108">Was motivated by many *memory leak* and *GC not working* issues.</span></span> <span data-ttu-id="e719c-109">Die meisten dieser Probleme sind darauf zurückzuführen, dass Sie nicht verstehen, wie die Arbeitsspeicher Nutzung in .net Core funktioniert, oder nicht verstehen, wie Sie gemessen wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-109">Most of these issues were caused by not understanding how memory consumption works in .NET Core, or not understanding how it's measured.</span></span>
* <span data-ttu-id="e719c-110">Veranschaulicht die problematische Speicher Verwendung und schlägt alternative Ansätze vor.</span><span class="sxs-lookup"><span data-stu-id="e719c-110">Demonstrates problematic memory use, and suggests alternative approaches.</span></span>

## <a name="how-garbage-collection-gc-works-in-net-core"></a><span data-ttu-id="e719c-111">Funktionsweise von Garbage Collection (GC) in .net Core</span><span class="sxs-lookup"><span data-stu-id="e719c-111">How garbage collection (GC) works in .NET Core</span></span>

<span data-ttu-id="e719c-112">Der GC ordnet Heap Segmente zu, wobei jedes Segment ein zusammenhängender Speicherbereich ist.</span><span class="sxs-lookup"><span data-stu-id="e719c-112">The GC allocates heap segments where each segment is a contiguous range of memory.</span></span> <span data-ttu-id="e719c-113">Objekte, die im Heap abgelegt werden, werden in einer von drei Generationen kategorisiert: 0, 1 oder 2.</span><span class="sxs-lookup"><span data-stu-id="e719c-113">Objects placed in the heap are categorized into one of 3 generations: 0, 1, or 2.</span></span> <span data-ttu-id="e719c-114">Die Generierung bestimmt die Häufigkeit, mit der die GC versucht, Arbeitsspeicher auf verwalteten Objekten freizugeben, auf die nicht mehr von der APP verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-114">The generation determines the frequency the GC attempts to release memory on managed objects that are no longer referenced by the app.</span></span> <span data-ttu-id="e719c-115">Niedrigere nummerierte Generationen werden häufiger als GC gezählt.</span><span class="sxs-lookup"><span data-stu-id="e719c-115">Lower numbered generations are GC'd more frequently.</span></span>

<span data-ttu-id="e719c-116">Objekte werden basierend auf Ihrer Lebensdauer von einer Generation zu einer anderen verschoben.</span><span class="sxs-lookup"><span data-stu-id="e719c-116">Objects are moved from one generation to another based on their lifetime.</span></span> <span data-ttu-id="e719c-117">Wenn Objekte länger leben, werden Sie in eine höhere Generation verschoben.</span><span class="sxs-lookup"><span data-stu-id="e719c-117">As objects live longer, they are moved into a higher generation.</span></span> <span data-ttu-id="e719c-118">Wie bereits erwähnt, sind höhere Generationen seltener.</span><span class="sxs-lookup"><span data-stu-id="e719c-118">As mentioned previously, higher generations are GC'd less often.</span></span> <span data-ttu-id="e719c-119">Kurzlebige Objekte bleiben immer in Generation 0.</span><span class="sxs-lookup"><span data-stu-id="e719c-119">Short term lived objects always remain in generation 0.</span></span> <span data-ttu-id="e719c-120">Beispielsweise sind Objekte, auf die während der Lebensdauer einer Webanforderung verwiesen wird, kurzlebig.</span><span class="sxs-lookup"><span data-stu-id="e719c-120">For example, objects that are referenced during the life of a web request are short lived.</span></span> <span data-ttu-id="e719c-121">[Singletons](xref:fundamentals/dependency-injection#service-lifetimes) auf Anwendungsebene werden in der Regel zu Generation 2 migriert.</span><span class="sxs-lookup"><span data-stu-id="e719c-121">Application level [singletons](xref:fundamentals/dependency-injection#service-lifetimes) generally migrate to generation 2.</span></span>

<span data-ttu-id="e719c-122">Wenn eine ASP.net Core-App gestartet wird, wird der GC:</span><span class="sxs-lookup"><span data-stu-id="e719c-122">When an ASP.NET Core app starts, the GC:</span></span>

* <span data-ttu-id="e719c-123">Reserviert Speicher für die anfänglichen Heap Segmente.</span><span class="sxs-lookup"><span data-stu-id="e719c-123">Reserves some memory for the initial heap segments.</span></span>
* <span data-ttu-id="e719c-124">Führt einen Commit für einen kleinen Teil des Speichers aus, wenn die Laufzeit geladen wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-124">Commits a small portion of memory when the runtime is loaded.</span></span>

<span data-ttu-id="e719c-125">Die vorhergehenden Speicher Belegungen werden aus Leistungsgründen ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="e719c-125">The preceding memory allocations are done for performance reasons.</span></span> <span data-ttu-id="e719c-126">Der Leistungsvorteil ergibt sich aus Heap Segmenten im zusammenhängenden Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="e719c-126">The performance benefit comes from heap segments in contiguous memory.</span></span>

### <a name="call-gccollect"></a><span data-ttu-id="e719c-127">Ruft GC auf. Samm</span><span class="sxs-lookup"><span data-stu-id="e719c-127">Call GC.Collect</span></span>

<span data-ttu-id="e719c-128">GC wird aufgerufen [. Explizit erfassen](xref:System.GC.Collect*) :</span><span class="sxs-lookup"><span data-stu-id="e719c-128">Calling [GC.Collect](xref:System.GC.Collect*) explicitly:</span></span>

* <span data-ttu-id="e719c-129">Sollte **nicht** von Produktions ASP.net Core-apps ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-129">Should **not** be done by production ASP.NET Core apps.</span></span>
* <span data-ttu-id="e719c-130">Ist nützlich bei der Untersuchung von Speicher Verlusten.</span><span class="sxs-lookup"><span data-stu-id="e719c-130">Is useful when investigating memory leaks.</span></span>
* <span data-ttu-id="e719c-131">Bei der Untersuchung überprüft, ob der GC alle verbleibenden Objekte aus dem Arbeitsspeicher entfernt hat, damit der Arbeitsspeicher gemessen werden kann.</span><span class="sxs-lookup"><span data-stu-id="e719c-131">When investigating, verifies the GC has removed all dangling objects from memory so memory can be measured.</span></span>

## <a name="analyzing-the-memory-usage-of-an-app"></a><span data-ttu-id="e719c-132">Analysieren der Speicherauslastung einer APP</span><span class="sxs-lookup"><span data-stu-id="e719c-132">Analyzing the memory usage of an app</span></span>

<span data-ttu-id="e719c-133">Dedizierte Tools können bei der Analyse der Speicherauslastung helfen:</span><span class="sxs-lookup"><span data-stu-id="e719c-133">Dedicated tools can help analyzing memory usage:</span></span>

- <span data-ttu-id="e719c-134">Zählen von Objekt verweisen</span><span class="sxs-lookup"><span data-stu-id="e719c-134">Counting object references</span></span>
- <span data-ttu-id="e719c-135">Messen der Auswirkung der GC auf die CPU-Auslastung durch den GC</span><span class="sxs-lookup"><span data-stu-id="e719c-135">Measuring how much impact the GC has on CPU usage</span></span>
- <span data-ttu-id="e719c-136">Messen des für jede Generation verwendeten Speicherplatzes</span><span class="sxs-lookup"><span data-stu-id="e719c-136">Measuring memory space used for each generation</span></span>

<span data-ttu-id="e719c-137">Verwenden Sie die folgenden Tools, um die Speicherauslastung zu analysieren:</span><span class="sxs-lookup"><span data-stu-id="e719c-137">Use the following tools to analyze memory usage:</span></span>

* <span data-ttu-id="e719c-138">[dotnet-Trace](/dotnet/core/diagnostics/dotnet-trace): kann auf Produktions Computern verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-138">[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace): Can be  used on production machines.</span></span>
* [<span data-ttu-id="e719c-139">Analysieren der Arbeitsspeicherauslastung ohne Visual Studio-Debugger</span><span class="sxs-lookup"><span data-stu-id="e719c-139">Analyze memory usage without the Visual Studio debugger</span></span>](/visualstudio/profiling/memory-usage-without-debugging2)
* [<span data-ttu-id="e719c-140">Profilerstellung zur Arbeitsspeicherverwendung in Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e719c-140">Profile memory usage in Visual Studio</span></span>](/visualstudio/profiling/memory-usage)

### <a name="detecting-memory-issues"></a><span data-ttu-id="e719c-141">Erkennen von Speicherproblemen</span><span class="sxs-lookup"><span data-stu-id="e719c-141">Detecting memory issues</span></span>

<span data-ttu-id="e719c-142">Der Task-Manager kann verwendet werden, um eine Vorstellung davon zu erhalten, wie viel Arbeitsspeicher eine ASP.net-App verwendet.</span><span class="sxs-lookup"><span data-stu-id="e719c-142">Task Manager can be used to get an idea of how much memory an ASP.NET app is using.</span></span> <span data-ttu-id="e719c-143">Der Arbeitsspeicher Wert des Task-Managers:</span><span class="sxs-lookup"><span data-stu-id="e719c-143">The Task Manager memory value:</span></span>

* <span data-ttu-id="e719c-144">Stellt die Menge an Arbeitsspeicher dar, die vom ASP.NET-Prozess verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-144">Represents the amount of memory that is used by the ASP.NET process.</span></span>
* <span data-ttu-id="e719c-145">Enthält die Living-Objekte der APP und andere Arbeitsspeicherconsumer, z. b. systemeigene Speicherauslastung.</span><span class="sxs-lookup"><span data-stu-id="e719c-145">Includes the app's living objects and other memory consumers such as native memory usage.</span></span>

<span data-ttu-id="e719c-146">Wenn der Arbeitsspeicher Wert des Task-Managers unbegrenzt zunimmt und nie vereinfacht wird, weist die APP einen Speicher Verlust auf.</span><span class="sxs-lookup"><span data-stu-id="e719c-146">If the Task Manager memory value increases indefinitely and never flattens out, the app has a memory leak.</span></span> <span data-ttu-id="e719c-147">In den folgenden Abschnitten werden verschiedene Speicher Verwendungs Muster veranschaulicht und erläutert.</span><span class="sxs-lookup"><span data-stu-id="e719c-147">The following sections demonstrate and explain several memory usage patterns.</span></span>

## <a name="sample-display-memory-usage-app"></a><span data-ttu-id="e719c-148">Beispiel für die Anzeige der Arbeitsspeicher Auslastung</span><span class="sxs-lookup"><span data-stu-id="e719c-148">Sample display memory usage app</span></span>

<span data-ttu-id="e719c-149">Die [Memoryleak-Beispiel-App](https://github.com/sebastienros/memoryleak) ist auf GitHub verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e719c-149">The [MemoryLeak sample app](https://github.com/sebastienros/memoryleak) is available on GitHub.</span></span> <span data-ttu-id="e719c-150">Die memoryleck-App:</span><span class="sxs-lookup"><span data-stu-id="e719c-150">The MemoryLeak app:</span></span>

* <span data-ttu-id="e719c-151">Enthält einen Diagnose Controller, der Echt Zeit Speicher-und GC-Daten für die APP sammelt.</span><span class="sxs-lookup"><span data-stu-id="e719c-151">Includes a diagnostic controller that gathers real-time memory and GC data for the app.</span></span>
* <span data-ttu-id="e719c-152">Verfügt über eine Index Seite, auf der die Speicher-und GC-Daten angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-152">Has an Index page that displays the memory and GC data.</span></span> <span data-ttu-id="e719c-153">Die Index Seite wird jede Sekunde aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="e719c-153">The Index page is refreshed every second.</span></span>
* <span data-ttu-id="e719c-154">Enthält einen API-Controller, der verschiedene Speicher Auslastungs Muster bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e719c-154">Contains an API controller that provides various memory load patterns.</span></span>
* <span data-ttu-id="e719c-155">Ist kein unterstütztes Tool, kann jedoch verwendet werden, um Speicher Auslastungs Muster von ASP.net Core-apps anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="e719c-155">Is not a supported tool, however, it can be used to display memory usage patterns of ASP.NET Core apps.</span></span>

<span data-ttu-id="e719c-156">Führen Sie memoryleck aus.</span><span class="sxs-lookup"><span data-stu-id="e719c-156">Run MemoryLeak.</span></span> <span data-ttu-id="e719c-157">Der zugewiesene Arbeitsspeicher steigt langsam, bis eine GC auftritt.</span><span class="sxs-lookup"><span data-stu-id="e719c-157">Allocated memory slowly increases until a GC occurs.</span></span> <span data-ttu-id="e719c-158">Der Arbeitsspeicher nimmt zu, da das Tool benutzerdefiniertes Objekt zum Erfassen von Daten zuordnet</span><span class="sxs-lookup"><span data-stu-id="e719c-158">Memory increases because the tool allocates custom object to capture data.</span></span> <span data-ttu-id="e719c-159">Die folgende Abbildung zeigt die Index Seite memoryleck, wenn eine Generation 0-GC auftritt.</span><span class="sxs-lookup"><span data-stu-id="e719c-159">The following image shows the MemoryLeak Index page when a Gen 0 GC occurs.</span></span> <span data-ttu-id="e719c-160">Das Diagramm zeigt 0 RPS (Anforderungen pro Sekunde), da keine API-Endpunkte vom API-Controller aufgerufen wurden.</span><span class="sxs-lookup"><span data-stu-id="e719c-160">The chart shows 0 RPS (Requests per second) because no API endpoints from the API controller have been called.</span></span>

![Vorheriges Diagramm](memory/_static/0RPS.png)

<span data-ttu-id="e719c-162">Im Diagramm werden zwei Werte für die Speicherauslastung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e719c-162">The chart displays two values for the memory usage:</span></span>

- <span data-ttu-id="e719c-163">Reserviert: die Menge an Arbeitsspeicher, die von verwalteten Objekten belegt wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-163">Allocated: the amount of memory occupied by managed objects</span></span>
- <span data-ttu-id="e719c-164">[Workingset](/windows/win32/memory/working-set): der Satz von Seiten im virtuellen Adressraum des Prozesses, der sich zurzeit im physischen Speicher befinden.</span><span class="sxs-lookup"><span data-stu-id="e719c-164">[Working set](/windows/win32/memory/working-set): The set of pages in the virtual address space of the process that are currently resident in physical memory.</span></span> <span data-ttu-id="e719c-165">Das angezeigte Workingset ist derselbe Wert, den Task-Manager anzeigt.</span><span class="sxs-lookup"><span data-stu-id="e719c-165">The working set shown is the same value Task Manager displays.</span></span>

### <a name="transient-objects"></a><span data-ttu-id="e719c-166">Vorübergehende Objekte</span><span class="sxs-lookup"><span data-stu-id="e719c-166">Transient objects</span></span>

<span data-ttu-id="e719c-167">Die folgende API erstellt eine 10-KB-Zeichen folgen Instanz und gibt Sie an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="e719c-167">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="e719c-168">Bei jeder Anforderung wird ein neues-Objekt im Arbeitsspeicher zugeordnet und in die Antwort geschrieben.</span><span class="sxs-lookup"><span data-stu-id="e719c-168">On each request, a new object is allocated in memory and written to the response.</span></span> <span data-ttu-id="e719c-169">Zeichen folgen werden als UTF-16-Zeichen in .net gespeichert, sodass jedes Zeichen 2 Bytes im Arbeitsspeicher benötigt.</span><span class="sxs-lookup"><span data-stu-id="e719c-169">Strings are stored as UTF-16 characters in .NET so each character takes 2 bytes in memory.</span></span>

```csharp
[HttpGet("bigstring")]
public ActionResult<string> GetBigString()
{
    return new String('x', 10 * 1024);
}
```

<span data-ttu-id="e719c-170">Das folgende Diagramm wird mit einer relativ kleinen Auslastung in generiert, um anzuzeigen, wie die Speicher Belegungen durch die GC beeinträchtigt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-170">The following graph is generated with a relatively small load in to show how memory allocations are impacted by the GC.</span></span>

![Vorheriges Diagramm](memory/_static/bigstring.png)

<span data-ttu-id="e719c-172">Das vorangehende Diagramm zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e719c-172">The preceding chart shows:</span></span>

* <span data-ttu-id="e719c-173">4K RPS (Anforderungen pro Sekunde).</span><span class="sxs-lookup"><span data-stu-id="e719c-173">4K RPS (Requests per second).</span></span>
* <span data-ttu-id="e719c-174">Garbage Collections der Generation 0 erfolgen ungefähr alle zwei Sekunden.</span><span class="sxs-lookup"><span data-stu-id="e719c-174">Generation 0 GC collections occur about every two seconds.</span></span>
* <span data-ttu-id="e719c-175">Die Workingsets sind konstant bei ungefähr 500 MB.</span><span class="sxs-lookup"><span data-stu-id="e719c-175">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="e719c-176">CPU ist 12%.</span><span class="sxs-lookup"><span data-stu-id="e719c-176">CPU is 12%.</span></span>
* <span data-ttu-id="e719c-177">Die Arbeitsspeicher Nutzung und-Freigabe (über GC) ist stabil.</span><span class="sxs-lookup"><span data-stu-id="e719c-177">The memory consumption and release (through GC) is stable.</span></span>

<span data-ttu-id="e719c-178">Das folgende Diagramm wird mit dem maximalen Durchsatz erstellt, der vom Computer verarbeitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e719c-178">The following chart is taken at the max throughput that can be handled by the machine.</span></span>

![Vorheriges Diagramm](memory/_static/bigstring2.png)

<span data-ttu-id="e719c-180">Das vorangehende Diagramm zeigt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e719c-180">The preceding chart shows:</span></span>

* <span data-ttu-id="e719c-181">22K RPS</span><span class="sxs-lookup"><span data-stu-id="e719c-181">22K RPS</span></span>
* <span data-ttu-id="e719c-182">Garbage Collections der Generation 0 sind mehrmals pro Sekunde aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="e719c-182">Generation 0 GC collections occur several times per second.</span></span>
* <span data-ttu-id="e719c-183">Sammlungen der Generation 1 werden ausgelöst, da die APP bedeutend mehr Arbeitsspeicher pro Sekunde zugewiesen hat.</span><span class="sxs-lookup"><span data-stu-id="e719c-183">Generation 1 collections are triggered because the app allocated significantly more memory per second.</span></span>
* <span data-ttu-id="e719c-184">Die Workingsets sind konstant bei ungefähr 500 MB.</span><span class="sxs-lookup"><span data-stu-id="e719c-184">The working set is constant at approximately 500 MB.</span></span>
* <span data-ttu-id="e719c-185">Die CPU-Auslastung beträgt 33%.</span><span class="sxs-lookup"><span data-stu-id="e719c-185">CPU is 33%.</span></span>
* <span data-ttu-id="e719c-186">Die Arbeitsspeicher Nutzung und-Freigabe (über GC) ist stabil.</span><span class="sxs-lookup"><span data-stu-id="e719c-186">The memory consumption and release (through GC) is stable.</span></span>
* <span data-ttu-id="e719c-187">CPU (33%) ist nicht über ausgelastet, daher kann der Garbage Collection mit einer hohen Anzahl von Zuordnungen mithalten.</span><span class="sxs-lookup"><span data-stu-id="e719c-187">The CPU (33%) is not over-utilized, therefore the garbage collection can keep up with a high number of allocations.</span></span>

### <a name="workstation-gc-vs-server-gc"></a><span data-ttu-id="e719c-188">Arbeitsstation GC im Vergleich zu Server GC</span><span class="sxs-lookup"><span data-stu-id="e719c-188">Workstation GC vs. Server GC</span></span>

<span data-ttu-id="e719c-189">Der .NET Garbage Collector verfügt über zwei verschiedene Modi:</span><span class="sxs-lookup"><span data-stu-id="e719c-189">The .NET Garbage Collector has two different modes:</span></span>

* <span data-ttu-id="e719c-190">**Arbeitsstation GC**: für den Desktop optimiert.</span><span class="sxs-lookup"><span data-stu-id="e719c-190">**Workstation GC**: Optimized for the desktop.</span></span>
* <span data-ttu-id="e719c-191">**Server-GC**</span><span class="sxs-lookup"><span data-stu-id="e719c-191">**Server GC**.</span></span> <span data-ttu-id="e719c-192">Der standardmäßige GC für ASP.net Core-apps.</span><span class="sxs-lookup"><span data-stu-id="e719c-192">The default GC for ASP.NET Core apps.</span></span> <span data-ttu-id="e719c-193">Für den Server optimiert.</span><span class="sxs-lookup"><span data-stu-id="e719c-193">Optimized for the server.</span></span>

<span data-ttu-id="e719c-194">Der GC-Modus kann explizit in der Projektdatei oder in der *runtimeconfig.js* Datei der veröffentlichten App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-194">The GC mode can be set explicitly in the project file or in the *runtimeconfig.json* file of the published app.</span></span> <span data-ttu-id="e719c-195">Das folgende Markup zeigt die Einstellung `ServerGarbageCollection` in der Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="e719c-195">The following markup shows setting `ServerGarbageCollection` in the project file:</span></span>

```xml
<PropertyGroup>
  <ServerGarbageCollection>true</ServerGarbageCollection>
</PropertyGroup>
```

<span data-ttu-id="e719c-196">Zum Ändern der `ServerGarbageCollection` Projektdatei muss die APP neu erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-196">Changing `ServerGarbageCollection` in the project file requires the app to be rebuilt.</span></span>

<span data-ttu-id="e719c-197">**Hinweis:** Server Garbage Collection ist auf Computern mit einem einzelnen Kern **nicht** verfügbar.</span><span class="sxs-lookup"><span data-stu-id="e719c-197">**Note:** Server garbage collection is **not** available on machines with a single core.</span></span> <span data-ttu-id="e719c-198">Weitere Informationen finden Sie unter <xref:System.Runtime.GCSettings.IsServerGC>.</span><span class="sxs-lookup"><span data-stu-id="e719c-198">For more information, see <xref:System.Runtime.GCSettings.IsServerGC>.</span></span>

<span data-ttu-id="e719c-199">In der folgenden Abbildung ist das Arbeitsspeicher Profil unter einem 5K RPS mithilfe der Arbeitsstations-GC dargestellt.</span><span class="sxs-lookup"><span data-stu-id="e719c-199">The following image shows the memory profile under a 5K RPS using the Workstation GC.</span></span>

![Vorheriges Diagramm](memory/_static/workstation.png)

<span data-ttu-id="e719c-201">Die Unterschiede zwischen diesem Diagramm und der Server Version sind von Bedeutung:</span><span class="sxs-lookup"><span data-stu-id="e719c-201">The differences between this chart and the server version are significant:</span></span>

- <span data-ttu-id="e719c-202">Das Workingset sinkt von 500 MB auf 70 MB.</span><span class="sxs-lookup"><span data-stu-id="e719c-202">The working set drops from 500 MB to 70 MB.</span></span>
- <span data-ttu-id="e719c-203">Der GC führt die Garbage Collection der Generation 0 mehrmals pro Sekunde statt alle zwei Sekunden aus.</span><span class="sxs-lookup"><span data-stu-id="e719c-203">The GC does generation 0 collections multiple times per second instead of every two seconds.</span></span>
- <span data-ttu-id="e719c-204">GC fällt von 300 MB auf 10 MB.</span><span class="sxs-lookup"><span data-stu-id="e719c-204">GC drops from 300 MB to 10 MB.</span></span>

<span data-ttu-id="e719c-205">In einer typischen Webserver Umgebung ist die CPU-Auslastung wichtiger als der Arbeitsspeicher, daher ist die Server-GC besser geeignet.</span><span class="sxs-lookup"><span data-stu-id="e719c-205">On a typical web server environment, CPU usage is more important than memory, therefore the Server GC is better.</span></span> <span data-ttu-id="e719c-206">Wenn die Arbeitsspeicher Auslastung hoch ist und die CPU-Auslastung relativ gering ist, kann die GC der Arbeitsstation leistungsfähiger werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-206">If memory utilization is high and CPU usage is relatively low, the Workstation GC might be more performant.</span></span> <span data-ttu-id="e719c-207">Beispielsweise eine hohe Dichte, in der mehrere Web-Apps gehostet werden, bei denen Arbeitsspeicher knapp</span><span class="sxs-lookup"><span data-stu-id="e719c-207">For example, high density hosting several web apps where memory is scarce.</span></span>

<a name="sc"></a>

### <a name="gc-using-docker-and-small-containers"></a><span data-ttu-id="e719c-208">GC mittels Docker und kleiner Containers</span><span class="sxs-lookup"><span data-stu-id="e719c-208">GC using Docker and small containers</span></span>

<span data-ttu-id="e719c-209">Wenn mehrere Container-apps auf einem Computer ausgeführt werden, ist die Arbeitsstations-GC möglicherweise höher als die Server-GC.</span><span class="sxs-lookup"><span data-stu-id="e719c-209">When multiple containerized apps are running on one machine, Workstation GC might be more preformant than Server GC.</span></span> <span data-ttu-id="e719c-210">Weitere Informationen finden Sie unter [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) und [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span><span class="sxs-lookup"><span data-stu-id="e719c-210">For more information, see [Running with Server GC in a Small Container](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-0/) and [Running with Server GC in a Small Container Scenario Part 1 – Hard Limit for the GC Heap](https://devblogs.microsoft.com/dotnet/running-with-server-gc-in-a-small-container-scenario-part-1-hard-limit-for-the-gc-heap/).</span></span>

### <a name="persistent-object-references"></a><span data-ttu-id="e719c-211">Persistente Objekt Verweise</span><span class="sxs-lookup"><span data-stu-id="e719c-211">Persistent object references</span></span>

<span data-ttu-id="e719c-212">Der GC kann keine Objekte freigeben, auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-212">The GC cannot free objects that are referenced.</span></span> <span data-ttu-id="e719c-213">Objekte, auf die verwiesen wird, die jedoch nicht mehr benötigt werden, führen zu einem Speicherplatz.</span><span class="sxs-lookup"><span data-stu-id="e719c-213">Objects that are referenced but no longer needed result in a memory leak.</span></span> <span data-ttu-id="e719c-214">Wenn die APP Objekte häufig anordnet und Sie nicht mehr freigibt, wenn Sie nicht mehr benötigt werden, erhöht sich die Speicherauslastung im Laufe der Zeit.</span><span class="sxs-lookup"><span data-stu-id="e719c-214">If the app frequently allocates objects and fails to free them after they are no longer needed, memory usage will increase over time.</span></span>

<span data-ttu-id="e719c-215">Die folgende API erstellt eine 10-KB-Zeichen folgen Instanz und gibt Sie an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="e719c-215">The following API creates a 10-KB String instance and returns it to the client.</span></span> <span data-ttu-id="e719c-216">Der Unterschied zum vorherigen Beispiel besteht darin, dass auf diese Instanz von einem statischen Member verwiesen wird. Dies bedeutet, dass Sie nie für die Auflistung verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="e719c-216">The difference with the previous example is that this instance is referenced by a static member, which means it's never available for collection.</span></span>

```csharp
private static ConcurrentBag<string> _staticStrings = new ConcurrentBag<string>();

[HttpGet("staticstring")]
public ActionResult<string> GetStaticString()
{
    var bigString = new String('x', 10 * 1024);
    _staticStrings.Add(bigString);
    return bigString;
}
```

<span data-ttu-id="e719c-217">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="e719c-217">The preceding code:</span></span>

* <span data-ttu-id="e719c-218">Ist ein Beispiel für einen typischen Speicherplatz.</span><span class="sxs-lookup"><span data-stu-id="e719c-218">Is an example of a typical memory leak.</span></span>
* <span data-ttu-id="e719c-219">Bei häufigen aufrufen bewirkt, dass der APP-Speicher zunimmt, bis der Prozess mit einer Ausnahme abstürzt `OutOfMemory` .</span><span class="sxs-lookup"><span data-stu-id="e719c-219">With frequent calls, causes app memory to increases until the process crashes with an `OutOfMemory` exception.</span></span>

![Vorheriges Diagramm](memory/_static/eternal.png)

<span data-ttu-id="e719c-221">In der vorangehenden Abbildung:</span><span class="sxs-lookup"><span data-stu-id="e719c-221">In the preceding image:</span></span>

* <span data-ttu-id="e719c-222">Auslastungs Tests der `/api/staticstring` Endpunkt bewirkt eine lineare Zunahme des Speichers.</span><span class="sxs-lookup"><span data-stu-id="e719c-222">Load testing the `/api/staticstring` endpoint causes a linear increase in memory.</span></span>
* <span data-ttu-id="e719c-223">Der GC versucht, Arbeitsspeicher freizugeben, wenn die Arbeitsspeicher Auslastung zunimmt, indem eine Sammlung der Generation 2 aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-223">The GC tries to free memory as the memory pressure grows, by calling a generation 2 collection.</span></span>
* <span data-ttu-id="e719c-224">Der GC kann den kompromittierten Speicher nicht freigeben.</span><span class="sxs-lookup"><span data-stu-id="e719c-224">The GC cannot free the leaked memory.</span></span> <span data-ttu-id="e719c-225">Der zugewiesene und Workingset erhöhen sich mit der Zeit.</span><span class="sxs-lookup"><span data-stu-id="e719c-225">Allocated and working set increase with time.</span></span>

<span data-ttu-id="e719c-226">Einige Szenarien, wie z. b. Caching, erfordern, dass Objekt Verweise aufrechterhalten werden, bis die Arbeitsspeicher Auslastung die Freigabe aufgehoben.</span><span class="sxs-lookup"><span data-stu-id="e719c-226">Some scenarios, such as caching, require object references to be held until memory pressure forces them to be released.</span></span> <span data-ttu-id="e719c-227">Die- <xref:System.WeakReference> Klasse kann für diese Art von Cache Code verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-227">The <xref:System.WeakReference> class can be used for this type of caching code.</span></span> <span data-ttu-id="e719c-228">Ein- `WeakReference` Objekt wird unter Arbeitsspeicher Druck erfasst.</span><span class="sxs-lookup"><span data-stu-id="e719c-228">A `WeakReference` object is collected under memory pressures.</span></span> <span data-ttu-id="e719c-229">Die Standard Implementierung von <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> verwendet `WeakReference` .</span><span class="sxs-lookup"><span data-stu-id="e719c-229">The default implementation of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> uses `WeakReference`.</span></span>

### <a name="native-memory"></a><span data-ttu-id="e719c-230">Nativer Speicher</span><span class="sxs-lookup"><span data-stu-id="e719c-230">Native memory</span></span>

<span data-ttu-id="e719c-231">Einige .net Core-Objekte basieren auf System eigenem Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="e719c-231">Some .NET Core objects rely on native memory.</span></span> <span data-ttu-id="e719c-232">Der Native Arbeitsspeicher kann **nicht** vom GC gesammelt werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-232">Native memory can **not** be collected by the GC.</span></span> <span data-ttu-id="e719c-233">Das .NET-Objekt, das den nativen Speicher verwendet, muss es mit System eigenem Code freigeben</span><span class="sxs-lookup"><span data-stu-id="e719c-233">The .NET object using native memory must free it using native code.</span></span>

<span data-ttu-id="e719c-234">.NET stellt die- <xref:System.IDisposable> Schnittstelle bereit, damit Entwickler systemeigenen Speicher freigeben können.</span><span class="sxs-lookup"><span data-stu-id="e719c-234">.NET provides the <xref:System.IDisposable> interface to let developers release native memory.</span></span> <span data-ttu-id="e719c-235">Auch wenn <xref:System.IDisposable.Dispose*> nicht aufgerufen wird, wird von ordnungsgemäß implementierten Klassen aufgerufen, `Dispose` Wenn der [Finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-235">Even if <xref:System.IDisposable.Dispose*> is not called, correctly implemented classes call `Dispose` when the [finalizer](/dotnet/csharp/programming-guide/classes-and-structs/destructors) runs.</span></span>

<span data-ttu-id="e719c-236">Betrachten Sie folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="e719c-236">Consider the following code:</span></span>

```csharp
[HttpGet("fileprovider")]
public void GetFileProvider()
{
    var fp = new PhysicalFileProvider(TempPath);
    fp.Watch("*.*");
}
```

<span data-ttu-id="e719c-237">[Physicalfileprovider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) ist eine verwaltete Klasse, daher wird jede Instanz am Ende der Anforderung gesammelt.</span><span class="sxs-lookup"><span data-stu-id="e719c-237">[PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider?view=dotnet-plat-ext-3.0) is a managed class, so any instance will be collected at the end of the request.</span></span>

<span data-ttu-id="e719c-238">In der folgenden Abbildung wird das Speicher Profil angezeigt, während die API fortlaufend aufgerufen wird `fileprovider` .</span><span class="sxs-lookup"><span data-stu-id="e719c-238">The following image shows the memory profile while invoking the `fileprovider` API continuously.</span></span>

![Vorheriges Diagramm](memory/_static/fileprovider.png)

<span data-ttu-id="e719c-240">Das vorangehende Diagramm zeigt ein offensichtliches Problem mit der Implementierung dieser Klasse, da die Speicherauslastung weiterhin zunimmt.</span><span class="sxs-lookup"><span data-stu-id="e719c-240">The preceding chart shows an obvious issue with the implementation of this class, as it keeps increasing memory usage.</span></span> <span data-ttu-id="e719c-241">Dies ist ein bekanntes Problem, das in [diesem Problem](https://github.com/dotnet/aspnetcore/issues/3110)nachverfolgt wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-241">This is a known problem that is being tracked in [this issue](https://github.com/dotnet/aspnetcore/issues/3110).</span></span>

<span data-ttu-id="e719c-242">Der gleiche Fehler kann im Benutzercode auftreten, indem eine der folgenden Aktionen durchgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="e719c-242">The same leak could be happen in user code, by one of the following:</span></span>

* <span data-ttu-id="e719c-243">Die Klasse wird nicht ordnungsgemäß freigegeben.</span><span class="sxs-lookup"><span data-stu-id="e719c-243">Not releasing the class correctly.</span></span>
* <span data-ttu-id="e719c-244">Das Aufrufen der- `Dispose` Methode der abhängigen Objekte, die verworfen werden sollen, wird vergessen.</span><span class="sxs-lookup"><span data-stu-id="e719c-244">Forgetting to invoke the `Dispose`method of the dependent objects that should be disposed.</span></span>

### <a name="large-objects-heap"></a><span data-ttu-id="e719c-245">Heap für große Objekte</span><span class="sxs-lookup"><span data-stu-id="e719c-245">Large objects heap</span></span>

<span data-ttu-id="e719c-246">Häufige Speicher Belegungen/freie Zyklen können den Arbeitsspeicher fragmentieren, insbesondere bei der Zuordnung von großen Arbeitsspeicher Blöcken.</span><span class="sxs-lookup"><span data-stu-id="e719c-246">Frequent memory allocation/free cycles can fragment memory, especially when allocating large chunks of memory.</span></span> <span data-ttu-id="e719c-247">Objekte werden in zusammenhängenden Speicherblöcken zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="e719c-247">Objects are allocated in contiguous blocks of memory.</span></span> <span data-ttu-id="e719c-248">Um die Fragmentierung zu verringern, versucht die GC, Sie zu defragmentieren, wenn die GC Speicher freigibt.</span><span class="sxs-lookup"><span data-stu-id="e719c-248">To mitigate fragmentation, when the GC frees memory, it tries to defragment it.</span></span> <span data-ttu-id="e719c-249">Dieser Vorgang wird als " **Komprimierung**" bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="e719c-249">This process is called **compaction**.</span></span> <span data-ttu-id="e719c-250">Die Komprimierung umfasst das Verschieben von Objekten.</span><span class="sxs-lookup"><span data-stu-id="e719c-250">Compaction involves moving objects.</span></span> <span data-ttu-id="e719c-251">Das Verschieben von großen Objekten führt zu einer Leistungs Einbuße.</span><span class="sxs-lookup"><span data-stu-id="e719c-251">Moving large objects imposes a performance penalty.</span></span> <span data-ttu-id="e719c-252">Aus diesem Grund erstellt der GC eine spezielle Speicher Zone für _große_ Objekte ( [Large Object Heap](/dotnet/standard/garbage-collection/large-object-heap) , Loh).</span><span class="sxs-lookup"><span data-stu-id="e719c-252">For this reason the GC creates a special memory zone for _large_ objects, called the [large object heap](/dotnet/standard/garbage-collection/large-object-heap) (LOH).</span></span> <span data-ttu-id="e719c-253">Objekte, die größer als 85.000 Byte sind (etwa 83 KB):</span><span class="sxs-lookup"><span data-stu-id="e719c-253">Objects that are greater than 85,000 bytes (approximately 83 KB) are:</span></span>

* <span data-ttu-id="e719c-254">Auf dem Loh platziert.</span><span class="sxs-lookup"><span data-stu-id="e719c-254">Placed on the LOH.</span></span>
* <span data-ttu-id="e719c-255">Nicht komprimiert.</span><span class="sxs-lookup"><span data-stu-id="e719c-255">Not compacted.</span></span>
* <span data-ttu-id="e719c-256">Wird während der Generation 2-GCS gesammelt.</span><span class="sxs-lookup"><span data-stu-id="e719c-256">Collected during generation 2 GCs.</span></span>

<span data-ttu-id="e719c-257">Wenn der Loh voll ist, löst der GC eine Sammlung der Generation 2 aus.</span><span class="sxs-lookup"><span data-stu-id="e719c-257">When the LOH is full, the GC will trigger a generation 2 collection.</span></span> <span data-ttu-id="e719c-258">Sammlungen der Generation 2:</span><span class="sxs-lookup"><span data-stu-id="e719c-258">Generation 2 collections:</span></span>

* <span data-ttu-id="e719c-259">Sind grundsätzlich langsam.</span><span class="sxs-lookup"><span data-stu-id="e719c-259">Are inherently slow.</span></span>
* <span data-ttu-id="e719c-260">Zusätzlich entstehen die Kosten für das Auslösen einer Sammlung für alle anderen Generationen.</span><span class="sxs-lookup"><span data-stu-id="e719c-260">Additionally incur the cost of triggering a collection on all other generations.</span></span>

<span data-ttu-id="e719c-261">Der folgende Code komprimiert den Loh sofort:</span><span class="sxs-lookup"><span data-stu-id="e719c-261">The following code compacts the LOH immediately:</span></span>

```csharp
GCSettings.LargeObjectHeapCompactionMode = GCLargeObjectHeapCompactionMode.CompactOnce;
GC.Collect();
```

<span data-ttu-id="e719c-262"><xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode>Informationen zur Komprimierung des Loh finden Sie unter.</span><span class="sxs-lookup"><span data-stu-id="e719c-262">See <xref:System.Runtime.GCSettings.LargeObjectHeapCompactionMode> for information on compacting the LOH.</span></span>

<span data-ttu-id="e719c-263">In Containern, die .net Core 3,0 und höher verwenden, wird der Loh automatisch komprimiert.</span><span class="sxs-lookup"><span data-stu-id="e719c-263">In containers using .NET Core 3.0 and later, the LOH is automatically compacted.</span></span>

<span data-ttu-id="e719c-264">Die folgende API veranschaulicht dieses Verhalten:</span><span class="sxs-lookup"><span data-stu-id="e719c-264">The following API that illustrates this behavior:</span></span>

```csharp
[HttpGet("loh/{size=85000}")]
public int GetLOH1(int size)
{
   return new byte[size].Length;
}
```

<span data-ttu-id="e719c-265">Das folgende Diagramm zeigt das Speicher Profil des aufrufenden `/api/loh/84975` Endpunkts unter maximale Auslastung:</span><span class="sxs-lookup"><span data-stu-id="e719c-265">The following chart shows the memory profile of calling the `/api/loh/84975` endpoint, under maximum load:</span></span>

![Vorheriges Diagramm](memory/_static/loh1.png)

<span data-ttu-id="e719c-267">Das folgende Diagramm zeigt das Speicher Profil für den Aufruf des `/api/loh/84976` Endpunkts, wobei *nur noch ein Byte*zugeordnet wird:</span><span class="sxs-lookup"><span data-stu-id="e719c-267">The following chart shows the memory profile of calling the `/api/loh/84976` endpoint, allocating *just one more byte*:</span></span>

![Vorheriges Diagramm](memory/_static/loh2.png)

<span data-ttu-id="e719c-269">Hinweis: die `byte[]` Struktur hat Overhead-bytes.</span><span class="sxs-lookup"><span data-stu-id="e719c-269">Note: The `byte[]` structure has overhead bytes.</span></span> <span data-ttu-id="e719c-270">Aus diesem Grund wird das 85.000-Limit von 84.976 bytes ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="e719c-270">That's why 84,976 bytes triggers the 85,000 limit.</span></span>

<span data-ttu-id="e719c-271">Vergleichen der beiden vorangehenden Diagramme:</span><span class="sxs-lookup"><span data-stu-id="e719c-271">Comparing the two preceding charts:</span></span>

* <span data-ttu-id="e719c-272">Das Workingset ist für beide Szenarien ähnlich, etwa 450 MB.</span><span class="sxs-lookup"><span data-stu-id="e719c-272">The working set is similar for both scenarios, about 450 MB.</span></span>
* <span data-ttu-id="e719c-273">Die unter Loh-Anforderungen (84.975 Bytes) zeigen überwiegend Auflistungen der Generation 0.</span><span class="sxs-lookup"><span data-stu-id="e719c-273">The under LOH requests (84,975 bytes) shows mostly generation 0 collections.</span></span>
* <span data-ttu-id="e719c-274">Die over Loh-Anforderungen generieren Konstante Generation 2-Auflistungen.</span><span class="sxs-lookup"><span data-stu-id="e719c-274">The over LOH requests generate constant generation 2 collections.</span></span> <span data-ttu-id="e719c-275">Sammlungen der Generation 2 sind aufwendig.</span><span class="sxs-lookup"><span data-stu-id="e719c-275">Generation 2 collections are expensive.</span></span> <span data-ttu-id="e719c-276">Es ist mehr CPU erforderlich, und der Durchsatz sinkt fast 50%.</span><span class="sxs-lookup"><span data-stu-id="e719c-276">More CPU is required and throughput drops almost 50%.</span></span>

<span data-ttu-id="e719c-277">Temporäre große Objekte sind besonders problematisch, da Sie Gen2-GCS verursachen.</span><span class="sxs-lookup"><span data-stu-id="e719c-277">Temporary large objects are particularly problematic because they cause gen2 GCs.</span></span>

<span data-ttu-id="e719c-278">Um die maximale Leistung zu erzielen, sollte die Verwendung von großen Objekten minimiert werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-278">For maximum performance, large object use should be minimized.</span></span> <span data-ttu-id="e719c-279">Teilen Sie nach Möglichkeit große Objekte auf.</span><span class="sxs-lookup"><span data-stu-id="e719c-279">If possible, split up large objects.</span></span> <span data-ttu-id="e719c-280">Beispielsweise wird durch die Middleware zum zwischen [Speichern von Antworten](xref:performance/caching/response) in ASP.net Core die Cache Einträge in Blöcke aufgeteilt, die kleiner als 85.000 Bytes sind.</span><span class="sxs-lookup"><span data-stu-id="e719c-280">For example, [Response Caching](xref:performance/caching/response) middleware in ASP.NET Core split the cache entries into blocks less than 85,000 bytes.</span></span>

<span data-ttu-id="e719c-281">Die folgenden Links zeigen die ASP.net Core Methode zum Beibehalten von Objekten unter dem Loh-Grenzwert:</span><span class="sxs-lookup"><span data-stu-id="e719c-281">The following links show the ASP.NET Core approach to keeping objects under the LOH limit:</span></span>

* [<span data-ttu-id="e719c-282">Responsecaching/Streams/streamutilities. cs</span><span class="sxs-lookup"><span data-stu-id="e719c-282">ResponseCaching/Streams/StreamUtilities.cs</span></span>](https://github.com/dotnet/AspNetCore/blob/v3.0.0/src/Middleware/ResponseCaching/src/Streams/StreamUtilities.cs#L16)
* [<span data-ttu-id="e719c-283">Responsecaching/memoryresponsecache. cs</span><span class="sxs-lookup"><span data-stu-id="e719c-283">ResponseCaching/MemoryResponseCache.cs</span></span>](https://github.com/aspnet/ResponseCaching/blob/c1cb7576a0b86e32aec990c22df29c780af29ca5/src/Microsoft.AspNetCore.ResponseCaching/Internal/MemoryResponseCache.cs#L55)

<span data-ttu-id="e719c-284">Weitere Informationen finden Sie unter</span><span class="sxs-lookup"><span data-stu-id="e719c-284">For more information, see:</span></span>

* [<span data-ttu-id="e719c-285">Large Object Heap wurde aufgedeckt.</span><span class="sxs-lookup"><span data-stu-id="e719c-285">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="e719c-286">Großer Objekt Heap</span><span class="sxs-lookup"><span data-stu-id="e719c-286">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)

### <a name="httpclient"></a><span data-ttu-id="e719c-287">HttpClient</span><span class="sxs-lookup"><span data-stu-id="e719c-287">HttpClient</span></span>

<span data-ttu-id="e719c-288">Die falsche Verwendung von <xref:System.Net.Http.HttpClient> kann zu einem Ressourcen Fehler führen.</span><span class="sxs-lookup"><span data-stu-id="e719c-288">Incorrectly using <xref:System.Net.Http.HttpClient> can result in a resource leak.</span></span> <span data-ttu-id="e719c-289">System Ressourcen, z. b. Datenbankverbindungen, Sockets, Datei Handles usw.:</span><span class="sxs-lookup"><span data-stu-id="e719c-289">System resources, such as database connections, sockets, file handles, etc.:</span></span>

* <span data-ttu-id="e719c-290">Sind knapper als der Arbeitsspeicher.</span><span class="sxs-lookup"><span data-stu-id="e719c-290">Are more scarce than memory.</span></span>
* <span data-ttu-id="e719c-291">Sind schwieriger, wenn es zu einem kompromittierten als Arbeitsspeicher</span><span class="sxs-lookup"><span data-stu-id="e719c-291">Are more problematic when leaked than memory.</span></span>

<span data-ttu-id="e719c-292">Erfahrene .NET-Entwickler wissen, dass für Objekte aufgerufen werden muss <xref:System.IDisposable.Dispose*> , die implementieren <xref:System.IDisposable> .</span><span class="sxs-lookup"><span data-stu-id="e719c-292">Experienced .NET developers know to call <xref:System.IDisposable.Dispose*> on objects that implement <xref:System.IDisposable>.</span></span> <span data-ttu-id="e719c-293">Das Verwerfen von Objekten, die implementieren, `IDisposable` führt in der Regel zu einem kompromittierten oder kompromittierten Systemressourcen.</span><span class="sxs-lookup"><span data-stu-id="e719c-293">Not disposing objects that implement `IDisposable` typically results in leaked memory or leaked system resources.</span></span>

<span data-ttu-id="e719c-294">`HttpClient` implementiert `IDisposable` , sollte jedoch bei jedem Aufruf **nicht** verworfen werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-294">`HttpClient` implements `IDisposable`, but should **not** be disposed on every invocation.</span></span> <span data-ttu-id="e719c-295">Stattdessen `HttpClient` sollte wieder verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-295">Rather, `HttpClient` should be reused.</span></span>

<span data-ttu-id="e719c-296">Der folgende Endpunkt erstellt eine neue-Instanz und gibt diese  `HttpClient` bei jeder Anforderung aus:</span><span class="sxs-lookup"><span data-stu-id="e719c-296">The following endpoint creates and disposes a new  `HttpClient` instance on every request:</span></span>

```csharp
[HttpGet("httpclient1")]
public async Task<int> GetHttpClient1(string url)
{
    using (var httpClient = new HttpClient())
    {
        var result = await httpClient.GetAsync(url);
        return (int)result.StatusCode;
    }
}
```

<span data-ttu-id="e719c-297">Unter Last werden die folgenden Fehlermeldungen protokolliert:</span><span class="sxs-lookup"><span data-stu-id="e719c-297">Under load, the following error messages are logged:</span></span>

```
fail: Microsoft.AspNetCore.Server.Kestrel[13]
      Connection id "0HLG70PBE1CR1", Request id "0HLG70PBE1CR1:00000031":
      An unhandled exception was thrown by the application.
System.Net.Http.HttpRequestException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted --->
    System.Net.Sockets.SocketException: Only one usage of each socket address
    (protocol/network address/port) is normally permitted
   at System.Net.Http.ConnectHelper.ConnectAsync(String host, Int32 port,
    CancellationToken cancellationToken)
```

<span data-ttu-id="e719c-298">Obwohl die `HttpClient` Instanzen verworfen werden, benötigt die tatsächliche Netzwerkverbindung eine gewisse Zeit, bis Sie vom Betriebssystem freigegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-298">Even though the `HttpClient` instances are disposed, the actual network connection takes some time to be released by the operating system.</span></span> <span data-ttu-id="e719c-299">Durch das fortlaufende Erstellen neuer Verbindungen erfolgt die Auslastung der _Ports_ .</span><span class="sxs-lookup"><span data-stu-id="e719c-299">By continuously creating new connections, _ports exhaustion_ occurs.</span></span> <span data-ttu-id="e719c-300">Jede Client Verbindung erfordert einen eigenen ClientPort.</span><span class="sxs-lookup"><span data-stu-id="e719c-300">Each client connection requires its own client port.</span></span>

<span data-ttu-id="e719c-301">Eine Möglichkeit, die Port Auslastung zu verhindern, ist die Wiederverwendung derselben `HttpClient` Instanz:</span><span class="sxs-lookup"><span data-stu-id="e719c-301">One way to prevent port exhaustion is to reuse the same `HttpClient` instance:</span></span>

```csharp
private static readonly HttpClient _httpClient = new HttpClient();

[HttpGet("httpclient2")]
public async Task<int> GetHttpClient2(string url)
{
    var result = await _httpClient.GetAsync(url);
    return (int)result.StatusCode;
}
```

<span data-ttu-id="e719c-302">Die- `HttpClient` Instanz wird freigegeben, wenn die APP angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-302">The `HttpClient` instance is released when the app stops.</span></span> <span data-ttu-id="e719c-303">Dieses Beispiel zeigt, dass nicht jede verwerfbare Ressource nach jeder Verwendung verworfen werden sollte.</span><span class="sxs-lookup"><span data-stu-id="e719c-303">This example shows that not every disposable resource should be disposed after each use.</span></span>

<span data-ttu-id="e719c-304">Im folgenden finden Sie eine bessere Möglichkeit zur Handhabung der Lebensdauer einer `HttpClient` Instanz:</span><span class="sxs-lookup"><span data-stu-id="e719c-304">See the following for a better way to handle the lifetime of an `HttpClient` instance:</span></span>

* [<span data-ttu-id="e719c-305">HttpClient und die Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="e719c-305">HttpClient and lifetime management</span></span>](../fundamentals/http-requests.md#httpclient-and-lifetime-management)
* [<span data-ttu-id="e719c-306">HttpClient-Factory-Blog</span><span class="sxs-lookup"><span data-stu-id="e719c-306">HTTPClient factory blog</span></span>](https://devblogs.microsoft.com/aspnet/asp-net-core-2-1-preview1-introducing-httpclient-factory/)
 
### <a name="object-pooling"></a><span data-ttu-id="e719c-307">Objektpooling</span><span class="sxs-lookup"><span data-stu-id="e719c-307">Object pooling</span></span>

<span data-ttu-id="e719c-308">Im vorherigen Beispiel wurde gezeigt, wie die `HttpClient` -Instanz statisch gemacht und von allen Anforderungen wieder verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="e719c-308">The previous example showed how the `HttpClient` instance can be made static and reused by all requests.</span></span> <span data-ttu-id="e719c-309">Durch die Wiederverwendung wird verhindert, dass Ressourcen aussteht.</span><span class="sxs-lookup"><span data-stu-id="e719c-309">Reuse prevents running out of resources.</span></span>

<span data-ttu-id="e719c-310">Objekt Pooling:</span><span class="sxs-lookup"><span data-stu-id="e719c-310">Object pooling:</span></span>

* <span data-ttu-id="e719c-311">Verwendet das Verwendungs Muster.</span><span class="sxs-lookup"><span data-stu-id="e719c-311">Uses the reuse pattern.</span></span>
* <span data-ttu-id="e719c-312">Ist für Objekte konzipiert, die teuer zu erstellen sind.</span><span class="sxs-lookup"><span data-stu-id="e719c-312">Is designed for objects that are expensive to create.</span></span>

<span data-ttu-id="e719c-313">Ein Pool ist eine Sammlung von vorinitialisierten Objekten, die über mehrere Threads reserviert und freigegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="e719c-313">A pool is a collection of pre-initialized objects that can be reserved and released across threads.</span></span> <span data-ttu-id="e719c-314">Pools können Zuordnungs Regeln definieren, z. b. Grenzwerte, vordefinierte Größen oder Wachstumsrate.</span><span class="sxs-lookup"><span data-stu-id="e719c-314">Pools can define allocation rules such as limits, predefined sizes, or growth rate.</span></span>

<span data-ttu-id="e719c-315">Das nuget-Paket [Microsoft. Extensions. Objectpool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) enthält Klassen, die die Verwaltung solcher Pools erleichtern.</span><span class="sxs-lookup"><span data-stu-id="e719c-315">The NuGet package [Microsoft.Extensions.ObjectPool](https://www.nuget.org/packages/Microsoft.Extensions.ObjectPool/) contains classes that help to manage such pools.</span></span>

<span data-ttu-id="e719c-316">Der folgende API-Endpunkt instanziiert einen `byte` Puffer, der bei jeder Anforderung mit Zufallszahlen aufgefüllt wird:</span><span class="sxs-lookup"><span data-stu-id="e719c-316">The following API endpoint instantiates a `byte` buffer that is filled with random numbers on each request:</span></span>

```csharp
        [HttpGet("array/{size}")]
        public byte[] GetArray(int size)
        {
            var random = new Random();
            var array = new byte[size];
            random.NextBytes(array);

            return array;
        }
```

<span data-ttu-id="e719c-317">Im folgenden Diagramm wird der Aufruf der vorangehenden API mit mittlerer Auslastung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e719c-317">The following chart display calling the preceding API with moderate load:</span></span>

![Vorheriges Diagramm](memory/_static/array.png)

<span data-ttu-id="e719c-319">Im vorangehenden Diagramm erfolgen die Sammlungen der Generation 0 ungefähr einmal pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="e719c-319">In the preceding chart, generation 0 collections happen approximately once per second.</span></span>

<span data-ttu-id="e719c-320">Der vorangehende Code kann optimiert werden, indem der `byte` Puffer mithilfe von [arraypool \<T> ](xref:System.Buffers.ArrayPool`1)gebündelt wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-320">The preceding code can be optimized by pooling the `byte` buffer by using [ArrayPool\<T>](xref:System.Buffers.ArrayPool`1).</span></span> <span data-ttu-id="e719c-321">Eine statische-Instanz wird in allen Anforderungen wieder verwendet.</span><span class="sxs-lookup"><span data-stu-id="e719c-321">A static instance is reused across requests.</span></span>

<span data-ttu-id="e719c-322">Unterscheidet sich bei diesem Ansatz, dass ein in einem Pool zusammengefasste Objekt von der API zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="e719c-322">What's different with this approach is that a pooled object is returned from the API.</span></span> <span data-ttu-id="e719c-323">Dies bedeutet Folgendes:</span><span class="sxs-lookup"><span data-stu-id="e719c-323">That means:</span></span>

* <span data-ttu-id="e719c-324">Das-Objekt befindet sich außerhalb des-Steuer Elements, sobald Sie von der-Methode zurückkehren.</span><span class="sxs-lookup"><span data-stu-id="e719c-324">The object is out of your control as soon as you return from the method.</span></span>
* <span data-ttu-id="e719c-325">Das Objekt kann nicht freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e719c-325">You can't release the object.</span></span>

<span data-ttu-id="e719c-326">So richten Sie die Entsorgung des-Objekts ein:</span><span class="sxs-lookup"><span data-stu-id="e719c-326">To set up disposal of the object:</span></span>

* <span data-ttu-id="e719c-327">Kapseln Sie das gepoolte Array in ein verwerfbares Objekt.</span><span class="sxs-lookup"><span data-stu-id="e719c-327">Encapsulate the pooled array in a disposable object.</span></span>
* <span data-ttu-id="e719c-328">Registrieren Sie das in einem Pool zusammengefasste Objekt mit [HttpContext. Response. registerforverwerfen](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span><span class="sxs-lookup"><span data-stu-id="e719c-328">Register the pooled object with [HttpContext.Response.RegisterForDispose](xref:Microsoft.AspNetCore.Http.HttpResponse.RegisterForDispose*).</span></span>

<span data-ttu-id="e719c-329">`RegisterForDispose` übernimmt das Aufrufen von `Dispose` für das Zielobjekt, sodass es nur freigegeben wird, wenn die HTTP-Anforderung beendet ist.</span><span class="sxs-lookup"><span data-stu-id="e719c-329">`RegisterForDispose` will take care of calling `Dispose`on the target object so that it's only released when the HTTP request is complete.</span></span>

```csharp
private static ArrayPool<byte> _arrayPool = ArrayPool<byte>.Create();

private class PooledArray : IDisposable
{
    public byte[] Array { get; private set; }

    public PooledArray(int size)
    {
        Array = _arrayPool.Rent(size);
    }

    public void Dispose()
    {
        _arrayPool.Return(Array);
    }
}

[HttpGet("pooledarray/{size}")]
public byte[] GetPooledArray(int size)
{
    var pooledArray = new PooledArray(size);

    var random = new Random();
    random.NextBytes(pooledArray.Array);

    HttpContext.Response.RegisterForDispose(pooledArray);

    return pooledArray.Array;
}
```

<span data-ttu-id="e719c-330">Das Anwenden derselben Last wie die nicht in einem Pool zusammengefasste Version führt zu folgendem Diagramm:</span><span class="sxs-lookup"><span data-stu-id="e719c-330">Applying the same load as the non-pooled version results in the following chart:</span></span>

![Vorheriges Diagramm](memory/_static/pooledarray.png)

<span data-ttu-id="e719c-332">Der Hauptunterschied besteht darin, dass Bytes zugeordnet werden, was zu einer wesentlich geringeren Auflistung der Generation 0 gehört.</span><span class="sxs-lookup"><span data-stu-id="e719c-332">The main difference is allocated bytes, and as a consequence much fewer generation 0 collections.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e719c-333">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e719c-333">Additional resources</span></span>

* [<span data-ttu-id="e719c-334">Garbage Collection</span><span class="sxs-lookup"><span data-stu-id="e719c-334">Garbage Collection</span></span>](/dotnet/standard/garbage-collection/)
* [<span data-ttu-id="e719c-335">Grundlegendes zu verschiedenen GC-Modi mit neben läufigkeits Schnellansicht</span><span class="sxs-lookup"><span data-stu-id="e719c-335">Understanding different GC modes with Concurrency Visualizer</span></span>](https://blogs.msdn.microsoft.com/seteplia/2017/01/05/understanding-different-gc-modes-with-concurrency-visualizer/)
* [<span data-ttu-id="e719c-336">Large Object Heap wurde aufgedeckt.</span><span class="sxs-lookup"><span data-stu-id="e719c-336">Large Object Heap Uncovered</span></span>](https://devblogs.microsoft.com/dotnet/large-object-heap-uncovered-from-an-old-msdn-article/)
* [<span data-ttu-id="e719c-337">Großer Objekt Heap</span><span class="sxs-lookup"><span data-stu-id="e719c-337">Large object heap</span></span>](/dotnet/standard/garbage-collection/large-object-heap)