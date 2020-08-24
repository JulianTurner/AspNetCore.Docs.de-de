---
title: Leistungs Diagnose Tools
author: mjrousos
description: Nützliche Tools zur Diagnose von Leistungsproblemen in ASP.net Core apps.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/diagnostic-tools
ms.openlocfilehash: 5f3daaf132b903898e756160a459d4df5f421c11
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746545"
---
# <a name="performance-diagnostic-tools"></a>Leistungs Diagnosetools

Von [Mike Rousos](https://github.com/mjrousos)

In diesem Artikel werden die Tools für die Diagnose von Leistungsproblemen in ASP.net Core aufgeführt.

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio-Diagnosetools

Die in Visual Studio integrierten [Tools zur Profilerstellung und Diagnose](/visualstudio/profiling) sind ein guter Ausgangspunkt für die Untersuchung von Leistungsproblemen. Diese Tools sind leistungsstark und bequem in der Visual Studio-Entwicklungsumgebung zu verwenden. Mit den Tools können Sie die CPU-Auslastung, Speicherauslastung und Leistungs Ereignisse in ASP.net Core-apps analysieren. Wenn Sie integriert sind, ist die Profilerstellung während der Entwicklungszeit einfach.

Weitere Informationen finden Sie in der [Visual Studio-Dokumentation](/visualstudio/profiling/profiling-overview).

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview) bietet detaillierte Leistungsdaten für Ihre APP. Application Insights werden automatisch Daten zu Antwortraten, Fehlerraten, Antwortzeiten von Abhängigkeiten und mehr erfasst. Application Insights unterstützt das Protokollieren von benutzerdefinierten Ereignissen und Metriken für Ihre APP.

Azure-Anwendung Insights bietet mehrere Möglichkeiten, um Einblicke in überwachte apps zu erhalten:

- [Anwendungs](/azure/application-insights/app-insights-app-map) Zuordnung – hilft bei der Identifizierung von Leistungs Engpässen oder bei Ausfall-Hotspots für alle Komponenten verteilter apps.
- [Azure Metrik-Explorer](/azure/azure-monitor/platform/metrics-getting-started) ist eine Komponente des Microsoft Azure-Portal, die das Zeichnen von Diagrammen, das visuelle Korrelieren von Trends und das Untersuchen von Spitzen und Dips in Metrikwerten ermöglicht.
- [Blatt "Leistung" im Application Insights-Portal](/azure/application-insights/app-insights-tutorial-performance):

  - Zeigt Leistungsdetails für verschiedene Vorgänge in der überwachten APP an.
  - Ermöglicht das Drilldown in einen einzelnen Vorgang, um alle Teile/Abhängigkeiten zu überprüfen, die zu einem langen Zeitraum beitragen.
  - Profiler kann von hier aus aufgerufen werden, um Leistungs Überwachungen bei Bedarf zu erfassen.

- [Azure-Anwendung Insights Profiler](/azure/azure-monitor/app/profiler) ermöglicht die reguläre und Bedarfs gesteuerte Profilerstellung von .net-apps.  Azure-Portal zeigt erfasste Leistungs Ablauf Verfolgungen mit Aufruf Listen und aktiven Pfaden an. Die Ablauf Verfolgungs Dateien können auch zur tieferen Analyse mithilfe von perfview heruntergeladen werden.

Application Insights können in einer Vielzahl von Umgebungen verwendet werden:

- Optimiert für die Arbeit in Azure.
- Funktioniert in der Produktions-, Entwicklungs-und Staging-Umgebung.
- Funktioniert lokal in [Visual Studio](/azure/application-insights/app-insights-visual-studio) oder in anderen Host Umgebungen.

Weitere Informationen finden Sie unter [Application Insights für ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).

## <a name="perfview"></a>PerfView

[Perfview](https://github.com/Microsoft/perfview) ist ein Leistungsanalyse Tool, das vom .net-Team speziell für die Diagnose von .net-Leistungsproblemen erstellt wurde. Perfview ermöglicht die Analyse von CPU-Auslastung, Arbeitsspeicher-und GC-Verhalten, Leistungs Ereignissen und der Uhrzeitangabe.

Weitere Informationen zu perfview und den ersten Schritten mit [perfview-Videotutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) oder zum Lesen des Benutzerhandbuchs, das im Tool oder [auf GitHub](https://github.com/Microsoft/perfview)verfügbar ist, finden Sie hier.

## <a name="windows-performance-toolkit"></a>Windows Performance Toolkit

[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) besteht aus zwei Komponenten: Windows Performance Recorder (WPR) und Windows Performance Analyzer (WPA). Die Tools erstellen detaillierte Leistungsprofile von Windows-Betriebssystemen und-apps. WPT bietet umfangreichere Möglichkeiten zur Visualisierung von Daten, aber die Datensammlung ist weniger leistungsfähig als perfview.

## <a name="perfcollect"></a>Perfcollect

Obwohl perfview ein nützliches Tool zur Leistungsanalyse für .net-Szenarien ist, wird es nur unter Windows ausgeführt, sodass Sie es nicht zum Erfassen von Ablauf Verfolgungen aus ASP.net Core Apps verwenden können, die in Linux-Umgebungen ausgeführt werden.

[Perfcollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) ist ein Bash-Skript, das native Linux-Profil Erstellungs Tools ([perf](https://perf.wiki.kernel.org/index.php/Main_Page) und [lttng](https://lttng.org/)) zum Erfassen von Ablauf Verfolgungen für Linux verwendet, die von perfview analysiert werden können. Perfcollect ist nützlich, wenn Leistungsprobleme in Linux-Umgebungen angezeigt werden, in denen perfview nicht direkt verwendet werden kann. Stattdessen kann perfcollect Ablauf Verfolgungen von .net Core-apps erfassen, die dann mithilfe von perfview auf einem Windows-Computer analysiert werden.

Weitere Informationen zur Installation und zu den ersten Schritten mit perfcollect finden Sie [auf GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).

## <a name="other-third-party-performance-tools"></a>Andere Leistungs Tools von Drittanbietern

Im folgenden sind einige Leistungs Tools von Drittanbietern aufgeführt, die bei der Leistungs Untersuchung von .net Core-Anwendungen nützlich sind.

- [MiniProfiler](https://miniprofiler.com/)
- [dotTrace](https://www.jetbrains.com/profiler/) und [dotmemory](https://www.jetbrains.com/dotmemory/) von [jetbrains](https://www.jetbrains.com/)
- [VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) von Intel
