---
title: Bewährte Methoden für die ASP.net Core Leistung
author: mjrousos
description: Tipps zum Verbessern der Leistung in ASP.net Core-apps und vermeiden von häufigen Leistungsproblemen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/06/2020
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
uid: performance/performance-best-practices
ms.openlocfilehash: 01575ec87d2d346da7367523ca5e257d53de4983
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722617"
---
# <a name="aspnet-core-performance-best-practices"></a>Bewährte Methoden für die ASP.net Core Leistung

Von [Mike Rousos](https://github.com/mjrousos)

Dieser Artikel enthält Richtlinien für bewährte Methoden für die Leistung mit ASP.net Core.

## <a name="cache-aggressively"></a>Zwischenspeichern

Caching wird in mehreren Teilen dieses Dokuments erläutert. Weitere Informationen finden Sie unter <xref:performance/caching/response>.

## <a name="understand-hot-code-paths"></a>Verstehen von Hot-Codepfade

In diesem Dokument wird ein *Hot-Codepfad* als Codepfad definiert, der häufig aufgerufen wird und in dem ein Großteil der Ausführungszeit auftritt. Hot-Codepfade schränken die horizontale Skalierung und Leistung der APP ein und werden in verschiedenen Teilen dieses Dokuments erläutert.

## <a name="avoid-blocking-calls"></a>Vermeiden von blockierenden aufrufen

ASP.net Core-apps sollten so entworfen werden, dass viele Anforderungen gleichzeitig verarbeitet werden. Asynchrone APIs ermöglichen es einem kleinen Thread Pool, Tausende gleichzeitiger Anforderungen zu verarbeiten, indem nicht auf blockierende Aufrufe gewartet wird. Anstatt auf den Abschluss einer synchronen Aufgabe mit langer Laufzeit zu warten, kann der Thread an einer anderen Anforderung arbeiten.

Ein häufiges Leistungsproblem bei ASP.net Core-Apps ist das Blockieren von aufrufen, die asynchron sein könnten. Viele synchrone blockierende Aufrufe führen zu einem [Thread Pool-Hunger](/archive/blogs/vancem/diagnosing-net-core-threadpool-starvation-with-perfview-why-my-service-is-not-saturating-all-cores-or-seems-to-stall) und zu beeinträchtigten Reaktionszeiten.

**Nicht**:

* Blockieren Sie die asynchrone Ausführung durch Aufrufen von " [Task. Wait](/dotnet/api/system.threading.tasks.task.wait) " oder " [Task. Result](/dotnet/api/system.threading.tasks.task-1.result)".
* Abrufen von Sperren in gemeinsamen Codepfade. ASP.net Core-apps sind am leistungsfähigsten, wenn Sie entwickelt werden, um Code parallel auszuführen.
* " [Task. Run](/dotnet/api/system.threading.tasks.task.run) " aufzurufen und sofort darauf warten. ASP.net Core bereits app-Code in normalen Thread Pool-Threads ausführt, führt das Aufrufen von "Task. Run" nur zu einer zusätzlichen unnötigen Thread Pool-Zeitplanung. Auch wenn der geplante Code einen Thread blockieren würde, wird dies von Task. Run nicht verhindert.

**Do**Ausführen:

* Asynchrone [Hot-Codepfade](#understand-hot-code-paths) .
* Aufrufen von Datenzugriff, e/a und langfristigen Operations-APIs, wenn eine asynchrone API verfügbar ist. Verwenden Sie " [Task. Run](/dotnet/api/system.threading.tasks.task.run) " **nicht** , um eine synchrone API asynchron zu machen.
* Aktionen für Controller/ Razor Seite asynchron durchführen. Die gesamte-aufrufsstapel ist asynchron, um von [Async/](/dotnet/csharp/programming-guide/concepts/async/) Erwartungs Mustern zu profitieren.

Ein Profiler, z. b. [perfview](https://github.com/Microsoft/perfview), kann verwendet werden, um häufig dem [Thread Pool](/windows/desktop/procthread/thread-pools)hinzugefügte Threads zu suchen. Das `Microsoft-Windows-DotNETRuntime/ThreadPoolWorkerThread/Start` Ereignis gibt einen Thread an, der dem Thread Pool hinzugefügt wurde. <!--  For more information, see [async guidance docs](TBD-Link_To_Davifowl_Doc)  -->

## <a name="return-ienumerablet-or-iasyncenumerablet"></a>IEnumerable \<T> oder iasyncenumerable zurückgeben\<T>

`IEnumerable<T>`Die Rückgabe von einer Aktion führt zu einer synchronen Auflistungs Iterationen durch das Serialisierungsprogramm. Das Ergebnis sind die Blockierung von Aufrufen und die potenzielle Außerkraftsetzung des Threadpools. Um eine synchrone Enumeration zu vermeiden, verwenden Sie `ToListAsync` vor dem Zurückgeben der Enumerable.

Ab ASP.net Core 3,0 `IAsyncEnumerable<T>` kann als Alternative zu verwendet werden, `IEnumerable<T>` die asynchron auflistet. Weitere Informationen finden Sie unter [Rückgabe Typen von Controller Aktionen](xref:web-api/action-return-types#return-ienumerablet-or-iasyncenumerablet).

## <a name="minimize-large-object-allocations"></a>Minimieren von großen Objekt Zuordnungen

Der [.net Core-Garbage Collector](/dotnet/standard/garbage-collection/) verwaltet die Zuweisung und Freigabe von Arbeitsspeicher automatisch in ASP.net Core apps. Das automatische Garbage Collection bedeutet im Allgemeinen, dass Entwickler sich nicht darum kümmern müssen, wie oder wann Speicher freigegeben wird. Das Bereinigen von Objekten, auf die nicht verwiesen wird, benötigt jedoch CPU-Zeit, sodass Entwickler die Zuweisung von Objekten in [Hot-Codepfade](#understand-hot-code-paths) Die Garbage Collection ist für große Objekte besonders aufwendig (> 85 KB). Große Objekte werden auf dem [großen Objekt Heap](/dotnet/standard/garbage-collection/large-object-heap) gespeichert und erfordern eine vollständige (Generation 2) Garbage Collection zum Bereinigen. Im Gegensatz zu den Sammlungen der Generation 0 und der Generation 1 erfordert eine Sammlung der Generation 2 eine vorübergehende Unterbrechung der APP Häufige Zuordnungen und Aufhebung der Zuordnung von großen Objekten können zu inkonsistenter Leistung führen.

Empfehlungen:

* Sollten **Sie** große Objekte zwischenspeichern, die häufig verwendet werden. Das Zwischenspeichern von großen Objekten verhindert teure Zuweisungen.
* Verwenden **Sie einen** [arraypool \<T> ](/dotnet/api/system.buffers.arraypool-1) , um große Arrays zu speichern.
* Weisen **Sie nicht** viele kurzlebige große Objekte in den [Pfaden für heiße Codes](#understand-hot-code-paths)zu.

Arbeitsspeicher Probleme, wie z. b. die vorstehenden, können diagnostiziert werden, indem Garbage Collection (GC)-Statistiken in [perfview](https://github.com/Microsoft/perfview) überprüft und Folgendes überprüft wird:

* Pausenzeit für Garbage Collection.
* Der Prozentsatz der Prozessorzeit, der in Garbage Collection aufgewendet wird.
* Wie viele Garbage Collections sind die Generation 0, 1 und 2.

Weitere Informationen finden Sie unter [Garbage Collection und Leistung](/dotnet/standard/garbage-collection/performance).

## <a name="optimize-data-access-and-io"></a>Optimieren des Datenzugriffs und der e/a-Vorgänge

Interaktionen mit einem Datenspeicher und anderen Remote Diensten sind häufig die langsamsten Teile einer ASP.net Core-app. Das effiziente lesen und Schreiben von Daten ist wichtig für eine gute Leistung.

Empfehlungen:

* **Ruft alle** Datenzugriffs-APIs asynchron auf.
* Rufen **Sie nicht** mehr Daten ab, als erforderlich sind. Schreiben Sie Abfragen, um nur die Daten zurückzugeben, die für die aktuelle HTTP-Anforderung erforderlich sind.
* In **Erwägung gezogen** , dass Daten, auf die häufig zugegriffen wird, von einer Datenbank oder einem Remote Dienst abgerufen werden, wenn etwas veraltete Daten zulässig sind. Verwenden Sie je nach Szenario einen [MemoryCache](xref:performance/caching/memory) oder einen [distributedcache](xref:performance/caching/distributed). Weitere Informationen finden Sie unter <xref:performance/caching/response>.
* Minimieren **Sie** die Netzwerkroundtrips. Das Ziel besteht darin, die erforderlichen Daten in einem einzigen Aufruf anstelle mehrerer Aufrufe abzurufen.
* **Verwenden** Sie [keine nach Verfolgungs Abfragen](/ef/core/querying/tracking#no-tracking-queries) in Entity Framework Core, wenn Sie schreibgeschützte Zwecke auf Daten zugreifen. EF Core können die Ergebnisse von Abfragen ohne Nachverfolgung effizienter zurückgeben.
* Filtern und Aggregieren **Sie** LINQ-Abfragen (z. b. mit `.Where` `.Select` `.Sum` den Anweisungen, oder), damit die Filterung von der Datenbank ausgeführt wird.
* Beachten **Sie** , dass EF Core einige Abfrage Operatoren auf dem Client auflöst, was zu einer ineffizienten Abfrage Ausführung führen kann. Weitere Informationen finden Sie unter [Leistungsprobleme bei der Client Evaluierung](/ef/core/querying/client-eval#client-evaluation-performance-issues).
* Verwenden **Sie keine** Projektions Abfragen für Auflistungen, die zum Ausführen von "N + 1"-SQL-Abfragen führen können. Weitere Informationen finden Sie unter [Optimierung korrelierter Unterabfragen](/ef/core/what-is-new/ef-core-2.1#optimization-of-correlated-subqueries).

Unter [EF High Performance](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries) finden Sie Ansätze, die die Leistung in hochskalierbaren apps verbessern können:

* [DbContext-Pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)
* [Explizit kompilierte Abfragen](/ef/core/what-is-new/ef-core-2.0#explicitly-compiled-queries)

Es wird empfohlen, die Auswirkungen der vorangehenden Hochleistungs Ansätze zu messen, bevor Sie einen Commit für die Codebasis ausführen. Die zusätzliche Komplexität kompilierter Abfragen kann die Leistungsverbesserung nicht rechtfertigen.

Abfrage Probleme können erkannt werden, indem die Zeit für den Zugriff auf Daten mit [Application Insights](/azure/application-insights/app-insights-overview) oder mit Profil Erstellungs Tools überprüft wird. Die meisten Datenbanken stellen auch Statistiken für häufig ausgeführte Abfragen zur Verfügung.

## <a name="pool-http-connections-with-httpclientfactory"></a>Pool-http-Verbindungen mit httpclientfactory

Obwohl [HttpClient](/dotnet/api/system.net.http.httpclient) die `IDisposable` -Schnittstelle implementiert, ist Sie für die Wiederverwendung vorgesehen. Geschlossene `HttpClient` Instanzen lassen für kurze Zeit Sockets im `TIME_WAIT` Zustand geöffnet. Wenn ein Codepfad, der Objekte erstellt und freigibt `HttpClient` , häufig verwendet wird, kann die APP verfügbare Sockets ausschöpfen. [Httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) wurde in ASP.net Core 2,1 als Lösung für dieses Problem eingeführt. Er verarbeitet das Pooling von http-Verbindungen, um Leistung und Zuverlässigkeit zu optimieren.

Empfehlungen:

* Erstellen und Löschen **Sie** Instanzen nicht `HttpClient` direkt.
* **Do** Verwenden Sie [httpclientfactory](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests) zum Abrufen von `HttpClient` Instanzen. Weitere Informationen finden Sie unter [Verwenden von HttpClientFactory zum Implementieren robuster HTTP-Anforderungen](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

## <a name="keep-common-code-paths-fast"></a>Häufige Codepfade schnell halten

Sie möchten, dass der gesamte Code schnell ist. Häufig aufgerufene Codepfade sind die wichtigsten, die optimiert werden können. Dazu gehören:

* Middlewarekomponenten in der Anforderungs Verarbeitungs Pipeline der APP, insbesondere Middleware, die früh in der Pipeline ausgeführt wird. Diese Komponenten haben große Auswirkungen auf die Leistung.
* Code, der für jede Anforderung oder mehrmals pro Anforderung ausgeführt wird. Beispielsweise benutzerdefinierte Protokollierung, Autorisierungs Handler oder Initialisierung vorübergehender Dienste.

Empfehlungen:

* Verwenden **Sie keine** benutzerdefinierten middlewarekomponenten mit Aufgaben mit langer Ausführungszeit.
* Verwenden **Sie** Leistungsprofil Erstellungs Tools, wie z. b. [Visual Studio Diagnosetools](/visualstudio/profiling/profiling-feature-tour) oder [perfview](https://github.com/Microsoft/perfview)), um [heiße Codepfade](#understand-hot-code-paths)zu identifizieren.

## <a name="complete-long-running-tasks-outside-of-http-requests"></a>Ausführen von Aufgaben mit langer Ausführungszeit außerhalb von HTTP-Anforderungen

Die meisten Anforderungen an eine ASP.net Core-App können von einem Controller oder Seiten Modell verarbeitet werden, das die erforderlichen Dienste aufruft und eine HTTP-Antwort zurückgibt. Bei einigen Anforderungen, die Aufgaben mit langer Ausführungszeit betreffen, ist es besser, den gesamten Anforderungs Antwortprozess asynchron auszuführen.

Empfehlungen:

* Warten **Sie nicht** , bis lange Ausführungsaufgaben im Rahmen der normalen Verarbeitung von HTTP-Anforderungen ausgeführt werden.
* **Sie** sollten Anforderungen mit langer Ausführungszeit mit [Hintergrund Diensten](xref:fundamentals/host/hosted-services) oder außerhalb des Prozesses mit einer [Azure-Funktion](/azure/azure-functions/)verarbeiten. Das Abschließen von Arbeitsprozessen außerhalb des Prozesses ist besonders für CPU-intensive Aufgaben vorteilhaft.
* Verwenden **Sie** Echt Zeit Kommunikationsoptionen, wie z [SignalR](xref:signalr/introduction) . b., um asynchron mit Clients zu kommunizieren.

## <a name="minify-client-assets"></a>Minimieren von Client Ressourcen

ASP.net Core-apps mit komplexen Front-Ends bedienen häufig viele JavaScript-, CSS-oder Image-Dateien. Die Leistung der anfänglichen Ladeanforderungen kann wie folgt verbessert werden:

* Bündelung, bei der mehrere Dateien zu einer kombiniert werden.
* Minimieren: Dadurch wird die Größe der Dateien reduziert, indem Leerzeichen und Kommentare entfernt werden.

Empfehlungen:

* Verwenden **Sie** ASP.net Core die [integrierte Unterstützung](xref:client-side/bundling-and-minification) für das bündeln und minimieren von Client Ressourcen.
* **Verwenden Sie** andere Tools von Drittanbietern, z. b. [WebPack](https://webpack.js.org/), bei der komplexen Verwaltung von Client Assets.

## <a name="compress-responses"></a>Antworten komprimieren

 Die Reduzierung der Antwort Größe erhöht in der Regel die Reaktionsfähigkeit einer APP, oft erheblich. Eine Möglichkeit, die Nutz Last Größen zu reduzieren, ist das Komprimieren der Antworten einer App. Weitere Informationen finden Sie unter [Antwort Komprimierung](xref:performance/response-compression).

## <a name="use-the-latest-aspnet-core-release"></a>Neueste ASP.net Core Release verwenden

Jede neue Version von ASP.net Core umfasst Leistungsverbesserungen. Optimierungen in .net Core und ASP.net Core bedeuten, dass neuere Versionen in der Regel ältere Versionen übersteigen. .Net Core 2,1 hat z. b. Unterstützung für kompilierte reguläre Ausdrücke hinzugefügt und von der [Spanne \<T> ](/archive/msdn-magazine/2018/january/csharp-all-about-span-exploring-a-new-net-mainstay)profitiert. ASP.net Core 2,2 hat Unterstützung für http/2 hinzugefügt. [ASP.net Core 3,0 bietet viele Verbesserungen](xref:aspnetcore-3.0) , die die Speicherauslastung reduzieren und den Durchsatz verbessern. Wenn Leistung eine Priorität ist, sollten Sie ein Upgrade auf die aktuelle Version von ASP.net Core durchführen.

## <a name="minimize-exceptions"></a>Minimieren von Ausnahmen

Ausnahmen sollten selten vorkommen. Das Auslösen und Abfangen von Ausnahmen ist relativ zu anderen Code Fluss Mustern langsam. Aus diesem Grund sollten Ausnahmen nicht zum Steuern des normalen Programmflusses verwendet werden.

Empfehlungen:

* Verwenden Sie das Auslösen oder Abfangen von Ausnahmen **nicht** als Mittel des normalen Programmflusses, insbesondere in aktiven [Codepfade](#understand-hot-code-paths).
* Fügen **Sie** Logik in die APP ein, um Bedingungen zu erkennen und zu behandeln, die eine Ausnahme auslösen würden.
* Lösen Sie Ausnahmen für ungewöhnliche oder unerwartete Bedingungen aus, oder fangen **Sie Sie** ab.

App-Diagnosetools, wie z. b. Application Insights, können helfen, allgemeine Ausnahmen in einer APP zu identifizieren, die sich auf die Leistung auswirken können

## <a name="performance-and-reliability"></a>Leistung und Zuverlässigkeit

In den folgenden Abschnitten finden Sie Leistungs Tipps und bekannte Zuverlässigkeitsprobleme und-Lösungen.

## <a name="avoid-synchronous-read-or-write-on-httprequesthttpresponse-body"></a>Vermeiden Sie synchronen Lese-oder Schreibzugriff auf den HttpRequest/HttpResponse-Text

Alle e/a-Vorgänge in ASP.net Core sind asynchron. Server implementieren die `Stream` -Schnittstelle, die sowohl synchrone als auch asynchrone über Ladungen aufweist. Die asynchronen sollten bevorzugt werden, um zu verhindern, dass Threads im Thread Pool blockiert werden. Blockierende Threads können zu einem Thread Pool-Hunger führen.

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel wird verwendet <xref:System.IO.StreamReader.ReadToEnd*> . Der aktuelle Thread wird blockiert, um auf das Ergebnis zu warten. Dies ist ein Beispiel für die [Synchronisierung über Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
).

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet1)]

Im vorangehenden Code `Get` Liest synchron den gesamten HTTP-Anforderungs Text in den Arbeitsspeicher. Wenn der Client langsam hochgeladen wird, erfolgt die Synchronisierung der APP über Async. Die APP führt eine Synchronisierung über Async aus, da Kestrel **keine** synchronen Lesevorgänge unterstützt.

**Führen Sie Folgendes aus:** Im folgenden Beispiel <xref:System.IO.StreamReader.ReadToEndAsync*> wird der Thread beim Lesen verwendet und nicht blockiert.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet2)]

Der vorangehende Code liest den gesamten HTTP-Anforderungs Text asynchron in den Arbeitsspeicher.

> [!WARNING]
> Wenn die Anforderung groß ist, kann das Lesen des gesamten HTTP-Anforderungs Texts in den Arbeitsspeicher zu einer OOM-Bedingung (Out of Memory) führen. OOM kann zu einem Denial-of-Service-Ergebnis führen.  Weitere Informationen finden Sie unter [vermeiden des Lesens großer Anforderungs Texte oder Antwort Texte](#arlb) in den Arbeitsspeicher in diesem Dokument.

**Führen Sie Folgendes aus:** Das folgende Beispiel ist vollständig asynchron und verwendet einen nicht gepufferten Anforderungs Text:

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MyFirstController.cs?name=snippet3)]

Der vorangehende Code deserialisiert den Anforderungs Text asynchron in ein c#-Objekt.

## <a name="prefer-readformasync-over-requestform"></a>"Read Form Async" über "Request. Form" bevorzugen

Verwenden Sie `HttpContext.Request.ReadFormAsync` anstelle von `HttpContext.Request.Form`.
`HttpContext.Request.Form` kann nur mit den folgenden Bedingungen sicher gelesen werden:

* Das Formular wurde durch einen-Rückruf gelesen `ReadFormAsync` , und
* Der zwischengespeicherte Formular Wert wird gelesen mithilfe von. `HttpContext.Request.Form`

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel wird verwendet `HttpContext.Request.Form` .  `HttpContext.Request.Form` verwendet [Sync über Async](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md#warning-sync-over-async
) und kann zu einem Thread Pool-Hunger führen.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet1)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird verwendet `HttpContext.Request.ReadFormAsync` , um den Formular Text asynchron zu lesen.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/MySecondController.cs?name=snippet2)]

<a name="arlb"></a>

## <a name="avoid-reading-large-request-bodies-or-response-bodies-into-memory"></a>Vermeiden Sie, große Anforderungs Texte oder Antwort Texte in den Speicher zu lesen.

In .net endet jede Objekt Zuordnung, die größer als 85 KB ist, im großen Objekt Heap ([Loh](https://blogs.msdn.microsoft.com/maoni/2006/04/19/large-object-heap/)). Große Objekte sind auf zwei Arten kostspielig:

* Die Zuordnungs Kosten sind hoch, da der Arbeitsspeicher für ein neu zugeordneter großes Objekt gelöscht werden muss. Die CLR garantiert, dass der Arbeitsspeicher für alle neu zugeordneten Objekte gelöscht wird.
* Loh wird mit dem restlichen Heap gesammelt. Loh erfordert eine vollständige [Garbage Collection](/dotnet/standard/garbage-collection/fundamentals) -oder [Gen2](/dotnet/standard/garbage-collection/fundamentals#generations)-Auflistung.

In diesem [Blogbeitrag](https://adamsitnik.com/Array-Pool/#the-problem) wird das Problem kurz beschrieben:

> Wenn ein großes Objekt zugewiesen wird, wird es als Objekt der Generation 2 markiert. Nicht Gen 0 wie bei kleinen Objekten. Wenn nicht genügend Arbeitsspeicher in Loh vorhanden ist, bereinigt GC den gesamten verwalteten Heap, nicht nur Loh. Die Generation 0, Gen 1 und Gen 2 einschließlich Loh werden bereinigt. Dies wird als voll Garbage Collection bezeichnet und ist der zeitraubende Garbage Collection. Für viele Anwendungen ist dies akzeptabel. Aber definitiv nicht für Hochleistungs-Webserver, bei denen einige große Speicherpuffer benötigt werden, um eine durchschnittliche Webanforderung zu verarbeiten (Lesen Sie aus einem Socket, dekomprimieren und JSON-decodieren &).

Das naive Speichern eines großen Anforderungs-oder Antwort Texts in einem einzelnen-oder-Text `byte[]` `string` :

* Kann dazu führen, dass der Platz in Loh schnell nicht mehr genügend Speicherplatz hat.
* Kann aufgrund vollständiger GCS-Ausführung zu Leistungsproblemen bei der APP führen.

## <a name="working-with-a-synchronous-data-processing-api"></a>Arbeiten mit einer synchronen Datenverarbeitungs-API

Wenn Sie ein Serialisierungsprogramm/Deserialisierungsprogramm verwenden, das nur synchrone Lese-und Schreibvorgänge unterstützt (z. b.  [JSON.net](https://www.newtonsoft.com/json/help/html/Introduction.htm)):

* Puffert die Daten asynchron in den Arbeitsspeicher, bevor Sie an das Serialisierungsprogramm/Deserialisierungsprogramm übergeben werden.

> [!WARNING]
> Wenn die Anforderung groß ist, kann dies zu einer OOM-Bedingung (Out of Memory) führen. OOM kann zu einem Denial-of-Service-Ergebnis führen.  Weitere Informationen finden Sie unter [vermeiden des Lesens großer Anforderungs Texte oder Antwort Texte](#arlb) in den Arbeitsspeicher in diesem Dokument.

ASP.net Core 3,0 verwendet <xref:System.Text.Json> standardmäßig für die JSON-Serialisierung. <xref:System.Text.Json>:

* Lese- und Schreibvorgänge in JSON erfolgen asynchron.
* Der Code ist für UTF-8-Text optimiert.
* In der Regel lässt sich eine höhere Leistung als mit `Newtonsoft.Json` erzielen.

## <a name="do-not-store-ihttpcontextaccessorhttpcontext-in-a-field"></a>Speichern Sie ihttpcontextaccessor. HttpContext nicht in einem Feld.

Der [ihttpcontextaccessor. HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext) gibt den `HttpContext` der aktiven Anforderung zurück, wenn der Zugriff über den Anforderungs Thread erfolgt. Der `IHttpContextAccessor.HttpContext` darf **nicht** in einem Feld oder einer Variablen gespeichert werden.

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel wird der `HttpContext` in einem Feld gespeichert, und anschließend wird versucht, ihn später zu verwenden.

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet1)]

Der vorangehende Code erfasst häufig einen NULL-Wert oder einen falschen Wert `HttpContext` im Konstruktor.

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird Folgendes veranschaulicht:

* Speichert den <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> in einem-Feld.
* Verwendet das `HttpContext` Feld zur richtigen Zeit und überprüft, ob `null` .

[!code-csharp[](performance-best-practices/samples/3.0/MyType.cs?name=snippet2)]

## <a name="do-not-access-httpcontext-from-multiple-threads"></a>Nicht auf "HttpContext" aus mehreren Threads zugreifen

`HttpContext` ist *nicht* Thread sicher. Der `HttpContext` parallele Zugriff von mehreren Threads kann zu nicht definiertem Verhalten wie z. b. hängen, abstürzen und Daten Beschädigung führen.

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel werden drei parallele Anforderungen erstellt, und der eingehende Anforderungs Pfad wird vor und nach der ausgehenden HTTP-Anforderung protokolliert. Der Zugriff auf den Anforderungs Pfad erfolgt über mehrere Threads, die möglicherweise parallel erfolgen.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet1&highlight=25,28)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel werden alle Daten aus der eingehenden Anforderung kopiert, bevor die drei parallelen Anforderungen vorgenommen werden.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncFirstController.cs?name=snippet2&highlight=6,8,22,28)]

## <a name="do-not-use-the-httpcontext-after-the-request-is-complete"></a>Verwenden Sie HttpContext nicht, wenn die Anforderung erfüllt ist.

`HttpContext` ist nur gültig, solange eine aktive HTTP-Anforderung in der ASP.net Core Pipeline vorhanden ist. Die gesamte ASP.net Core Pipeline ist eine asynchrone Kette von Delegaten, die jede Anforderung ausführt. Wenn die `Task` von dieser Kette zurückgegebene abgeschlossen wird, wird der wieder verwendet `HttpContext` .

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel `async void` wird verwendet, wodurch die HTTP-Anforderung durchgeführt wird, wenn die erste `await` erreicht wird:

* Dies ist in ASP.net Core-apps **immer** eine bewährte Vorgehensweise.
* Greift auf den zu, `HttpResponse` nachdem die HTTP-Anforderung beendet wurde.
* Stürzt den Prozess ab.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncBadVoidController.cs?name=snippet1)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird ein-Wert `Task` an das Framework zurückgegeben, sodass die HTTP-Anforderung erst abgeschlossen wird, wenn die Aktion abgeschlossen ist.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/AsyncSecondController.cs?name=snippet1)]

## <a name="do-not-capture-the-httpcontext-in-background-threads"></a>HttpContext nicht in Hintergrundthreads erfassen

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel wird gezeigt, dass das Aufzeichnen `HttpContext` von aus der- `Controller` Eigenschaft erfolgt. Dies ist eine bewährte Vorgehensweise, da das Arbeits Element folgende Aktionen ausführen könnte:

* Außerhalb des Anforderungs Bereichs ausführen.
* Versuchen Sie, den Fehler zu lesen `HttpContext` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet1)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird Folgendes veranschaulicht:

* Kopiert die Daten, die in der Hintergrundaufgabe während der Anforderung erforderlich sind.
* Verweist auf nichts vom Controller.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetFirstController.cs?name=snippet2)]

Hintergrundaufgaben sollten als gehostete Dienste implementiert werden. Weitere Informationen finden Sie unter [Hintergrundaufgaben mit gehosteten Diensten](xref:fundamentals/host/hosted-services).

## <a name="do-not-capture-services-injected-into-the-controllers-on-background-threads"></a>Dienste, die in die Controller in Hintergrundthreads eingefügt werden, nicht erfassen

Gehen Sie nicht wie folgt vor **:** Im folgenden Beispiel wird gezeigt, dass das Aufzeichnen `DbContext` von aus dem `Controller` Action-Parameter erfolgt. Dies ist eine bewährte Vorgehensweise.  Das Arbeits Element kann außerhalb des Anforderungs Bereichs ausgeführt werden. Der bezieht sich `ContosoDbContext` auf die Anforderung, was zu einer führt `ObjectDisposedException` .

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet1)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird Folgendes veranschaulicht:

* Fügt ein ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> , um einen Bereich im Hintergrund Arbeits Element zu erstellen. `IServiceScopeFactory` ist ein Singleton.
* Erstellt einen neuen Abhängigkeits einschleusungs Bereich im Hintergrund Thread.
* Verweist auf nichts vom Controller.
* Erfasst nicht `ContosoDbContext` aus der eingehenden Anforderung.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2)]

Der folgende markierte Code:

* Erstellt einen Gültigkeitsbereich für die Lebensdauer des Hintergrund Vorgangs und löst Dienste davon auf.
* Verwendet `ContosoDbContext` aus dem richtigen Bereich.

[!code-csharp[](performance-best-practices/samples/3.0/Controllers/FireAndForgetSecondController.cs?name=snippet2&highlight=9-16)]

## <a name="do-not-modify-the-status-code-or-headers-after-the-response-body-has-started"></a>Ändern Sie den Statuscode oder die Header nicht, nachdem der Antworttext gestartet wurde.

Der HTTP-Antworttext wird von ASP.net Core nicht gepuffert. Wenn die Antwort zum ersten Mal geschrieben wird:

* Die Header werden zusammen mit diesem Textteil an den Client gesendet.
* Es ist nicht mehr möglich, Antwortheader zu ändern.

Gehen Sie nicht wie folgt vor **:** Der folgende Code versucht, Antwortheader hinzuzufügen, nachdem die Antwort bereits gestartet wurde:

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet1)]

Im vorangehenden Code löst `context.Response.Headers["test"] = "test value";` eine Ausnahme aus, wenn in `next()` die Antwort geschrieben hat.

**Führen Sie Folgendes aus:** Im folgenden Beispiel wird überprüft, ob die HTTP-Antwort vor dem Ändern der Header gestartet wurde.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet2)]

**Führen Sie Folgendes aus:** Im folgenden Beispiel `HttpResponse.OnStarting` wird verwendet, um die Header festzulegen, bevor die Antwortheader an den Client geleert werden.

Wenn Sie überprüfen, ob die Antwort nicht gestartet wurde, können Sie einen Rückruf registrieren, der unmittelbar vor dem Schreiben von Antwort Headern aufgerufen wird. Es wird überprüft, ob die Antwort nicht gestartet wurde:

* Bietet die Möglichkeit, Header Just-in-Time anzufügen oder zu überschreiben.
* Erfordert keine Kenntnis der nächsten Middleware in der Pipeline.

[!code-csharp[](performance-best-practices/samples/3.0/Startup22.cs?name=snippet3)]

## <a name="do-not-call-next-if-you-have-already-started-writing-to-the-response-body"></a>"Next ()" nicht aufzurufen, wenn Sie bereits mit dem Schreiben in den Antworttext begonnen haben

Komponenten erwarten nur, dass Sie aufgerufen werden, wenn Sie die Antwort verarbeiten und bearbeiten können.

## <a name="use-in-process-hosting-with-iis"></a>Verwenden des in-Process-Hosting mit IIS

Beim Einsatz von In-Process-Hosting wird eine ASP.NET Core-App im gleichen Prozess wie ihr IIS-Arbeitsprozess ausgeführt. Das in-Process-Hosting bietet eine verbesserte Leistung gegenüber dem Out-of-Process-Hosting, da Anforderungen nicht über den Loopback Adapter über einen Proxy gesendet werden. Der Loopback Adapter ist eine Netzwerkschnittstelle, die ausgehenden Netzwerk Datenverkehr zurück an denselben Computer zurückgibt. IIS erledigt das Prozessmanagement mit dem [Windows-Prozessaktivierungsdienst (Process Activation Service, WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).

-Projekte werden standardmäßig in ASP.net Core 3,0 und höher auf das in-Process-Hostingmodell eingestellt.

Weitere Informationen finden Sie unter [Hosten von ASP.net Core unter Windows mit IIS](xref:host-and-deploy/iis/index)