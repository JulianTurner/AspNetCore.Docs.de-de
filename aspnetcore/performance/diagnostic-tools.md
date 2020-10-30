---
title: Leistungs Diagnose Tools
author: mjrousos
description: Nützliche Tools zur Diagnose von Leistungsproblemen in ASP.net Core apps.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: performance/diagnostic-tools
ms.openlocfilehash: 71386de232265840f9b825bd33d23bb1d218efc6
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060858"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="041ff-103">Leistungs Diagnosetools</span><span class="sxs-lookup"><span data-stu-id="041ff-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="041ff-104">Von [Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="041ff-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="041ff-105">In diesem Artikel werden die Tools für die Diagnose von Leistungsproblemen in ASP.net Core aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="041ff-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="041ff-106">Visual Studio-Diagnosetools</span><span class="sxs-lookup"><span data-stu-id="041ff-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="041ff-107">Die in Visual Studio integrierten [Tools zur Profilerstellung und Diagnose](/visualstudio/profiling) sind ein guter Ausgangspunkt für die Untersuchung von Leistungsproblemen.</span><span class="sxs-lookup"><span data-stu-id="041ff-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="041ff-108">Diese Tools sind leistungsstark und bequem in der Visual Studio-Entwicklungsumgebung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="041ff-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="041ff-109">Mit den Tools können Sie die CPU-Auslastung, Speicherauslastung und Leistungs Ereignisse in ASP.net Core-apps analysieren.</span><span class="sxs-lookup"><span data-stu-id="041ff-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="041ff-110">Wenn Sie integriert sind, ist die Profilerstellung während der Entwicklungszeit einfach.</span><span class="sxs-lookup"><span data-stu-id="041ff-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="041ff-111">Weitere Informationen finden Sie in der [Visual Studio-Dokumentation](/visualstudio/profiling/profiling-overview).</span><span class="sxs-lookup"><span data-stu-id="041ff-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="041ff-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="041ff-112">Application Insights</span></span>

<span data-ttu-id="041ff-113">[Application Insights](/azure/application-insights/app-insights-overview) bietet detaillierte Leistungsdaten für Ihre APP.</span><span class="sxs-lookup"><span data-stu-id="041ff-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="041ff-114">Application Insights werden automatisch Daten zu Antwortraten, Fehlerraten, Antwortzeiten von Abhängigkeiten und mehr erfasst.</span><span class="sxs-lookup"><span data-stu-id="041ff-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="041ff-115">Application Insights unterstützt das Protokollieren von benutzerdefinierten Ereignissen und Metriken für Ihre APP.</span><span class="sxs-lookup"><span data-stu-id="041ff-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="041ff-116">Azure-Anwendung Insights bietet mehrere Möglichkeiten, um Einblicke in überwachte apps zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="041ff-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="041ff-117">[Anwendungs](/azure/application-insights/app-insights-app-map) Zuordnung – hilft bei der Identifizierung von Leistungs Engpässen oder bei Ausfall-Hotspots für alle Komponenten verteilter apps.</span><span class="sxs-lookup"><span data-stu-id="041ff-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="041ff-118">[Azure Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started) ist eine Komponente des Microsoft Azure-Portal, die das Zeichnen von Diagrammen, das visuelle Korrelieren von Trends und das Untersuchen von Spitzen und Dips in Metrikwerten ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="041ff-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="041ff-119">[Blatt "Leistung" im Application Insights-Portal](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="041ff-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="041ff-120">Zeigt Leistungsdetails für verschiedene Vorgänge in der überwachten APP an.</span><span class="sxs-lookup"><span data-stu-id="041ff-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="041ff-121">Ermöglicht das Drilldown in einen einzelnen Vorgang, um alle Teile/Abhängigkeiten zu überprüfen, die zu einem langen Zeitraum beitragen.</span><span class="sxs-lookup"><span data-stu-id="041ff-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="041ff-122">Profiler kann von hier aus aufgerufen werden, um Leistungs Überwachungen bei Bedarf zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="041ff-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="041ff-123">[Azure-Anwendung Insights Profiler](/azure/azure-monitor/app/profiler) ermöglicht die reguläre und Bedarfs gesteuerte Profilerstellung von .net-apps.</span><span class="sxs-lookup"><span data-stu-id="041ff-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="041ff-124">Azure-Portal zeigt erfasste Leistungs Ablauf Verfolgungen mit Aufruf Listen und aktiven Pfaden an.</span><span class="sxs-lookup"><span data-stu-id="041ff-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="041ff-125">Die Ablauf Verfolgungs Dateien können auch zur tieferen Analyse mithilfe von perfview heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="041ff-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="041ff-126">Application Insights können in einer Vielzahl von Umgebungen verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="041ff-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="041ff-127">Optimiert für die Arbeit in Azure.</span><span class="sxs-lookup"><span data-stu-id="041ff-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="041ff-128">Funktioniert in der Produktions-, Entwicklungs-und Staging-Umgebung.</span><span class="sxs-lookup"><span data-stu-id="041ff-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="041ff-129">Funktioniert lokal in [Visual Studio](/azure/application-insights/app-insights-visual-studio) oder in anderen Host Umgebungen.</span><span class="sxs-lookup"><span data-stu-id="041ff-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="041ff-130">Weitere Informationen finden Sie unter [Application Insights für ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="041ff-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="041ff-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="041ff-131">PerfView</span></span>

<span data-ttu-id="041ff-132">[Perfview](https://github.com/Microsoft/perfview) ist ein Leistungsanalyse Tool, das vom .net-Team speziell für die Diagnose von .net-Leistungsproblemen erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="041ff-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="041ff-133">Perfview ermöglicht die Analyse von CPU-Auslastung, Arbeitsspeicher-und GC-Verhalten, Leistungs Ereignissen und der Uhrzeitangabe.</span><span class="sxs-lookup"><span data-stu-id="041ff-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="041ff-134">Weitere Informationen zu perfview und den ersten Schritten mit [perfview-Videotutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) oder zum Lesen des Benutzerhandbuchs, das im Tool oder [auf GitHub](https://github.com/Microsoft/perfview)verfügbar ist, finden Sie hier.</span><span class="sxs-lookup"><span data-stu-id="041ff-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="041ff-135">Windows Performance Toolkit</span><span class="sxs-lookup"><span data-stu-id="041ff-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="041ff-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) besteht aus zwei Komponenten: Windows Performance Recorder (WPR) und Windows Performance Analyzer (WPA).</span><span class="sxs-lookup"><span data-stu-id="041ff-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="041ff-137">Die Tools erstellen detaillierte Leistungsprofile von Windows-Betriebssystemen und-apps.</span><span class="sxs-lookup"><span data-stu-id="041ff-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="041ff-138">WPT bietet umfangreichere Möglichkeiten zur Visualisierung von Daten, aber die Datensammlung ist weniger leistungsfähig als perfview.</span><span class="sxs-lookup"><span data-stu-id="041ff-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="041ff-139">Perfcollect</span><span class="sxs-lookup"><span data-stu-id="041ff-139">PerfCollect</span></span>

<span data-ttu-id="041ff-140">Obwohl perfview ein nützliches Tool zur Leistungsanalyse für .net-Szenarien ist, wird es nur unter Windows ausgeführt, sodass Sie es nicht zum Erfassen von Ablauf Verfolgungen aus ASP.net Core Apps verwenden können, die in Linux-Umgebungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="041ff-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="041ff-141">[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) ist ein Bash-Skript, das native Linux-Profil Erstellungs Tools ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) und [lttng](https://lttng.org/)) zum Erfassen von Ablauf Verfolgungen für Linux verwendet, die von perfview analysiert werden können.</span><span class="sxs-lookup"><span data-stu-id="041ff-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="041ff-142">Perfcollect ist nützlich, wenn Leistungsprobleme in Linux-Umgebungen angezeigt werden, in denen perfview nicht direkt verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="041ff-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="041ff-143">Stattdessen kann perfcollect Ablauf Verfolgungen von .net Core-apps erfassen, die dann mithilfe von perfview auf einem Windows-Computer analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="041ff-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="041ff-144">Weitere Informationen zur Installation und zu den ersten Schritten mit perfcollect finden Sie [auf GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span><span class="sxs-lookup"><span data-stu-id="041ff-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="041ff-145">Andere Leistungs Tools von Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="041ff-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="041ff-146">Im folgenden sind einige Leistungs Tools von Drittanbietern aufgeführt, die bei der Leistungs Untersuchung von .net Core-Anwendungen nützlich sind.</span><span class="sxs-lookup"><span data-stu-id="041ff-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="041ff-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="041ff-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="041ff-148">[dotTrace](https://www.jetbrains.com/profiler/) und [dotmemory](https://www.jetbrains.com/dotmemory/) von [jetbrains](https://www.jetbrains.com/)</span><span class="sxs-lookup"><span data-stu-id="041ff-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="041ff-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) von Intel</span><span class="sxs-lookup"><span data-stu-id="041ff-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
