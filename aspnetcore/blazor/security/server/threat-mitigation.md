---
title: Leitfaden zur Bedrohungsabwehr für Blazor Server in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie Sicherheitsbedrohungen für Blazor Server-Apps vermeiden.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/09/2020
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
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 0e8b26110a970526b5f6306da236a92f52e64604
ms.sourcegitcommit: fe5a287fa6b9477b130aa39728f82cdad57611ee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430953"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a>Leitfaden zur Bedrohungsabwehr für Blazor Server in ASP.NET Core

Von [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server-Server-Apps basieren auf einem *zustandsbehafteten* Datenverarbeitungsmodell, in dem die Beziehung zwischen Server und Client sehr langlebig ist. Der persistente Zustand wird durch einen [Circuit](xref:blazor/state-management) aufrechterhalten, der mehrere potenziell langlebige Verbindungen umfassen kann.

Wenn ein Benutzer eine Blazor Server-Website besucht, erstellt der Server einen Circuit im Arbeitsspeicher des Servers. Der Circuit meldet dem Browser, welche Inhalte dieser rendern und wie dieser auf Ereignisse antworten soll, z. B. wenn der Benutzer in der Benutzeroberfläche auf eine Schaltfläche klickt. Für die Ausführung dieser Aktionen ruft ein Circuit JavaScript-Funktionen in den Browser- und .NET-Methoden des Benutzers auf dem Server auf. Diese bidirektionale JavaScript-basierte Interaktion wird als [JavaScript-Interop (JS-Interop)](xref:blazor/call-javascript-from-dotnet) bezeichnet.

Da JS-Interop eine Internetverbindung erfordert und der Client einen Remotebrowser verwendet, betreffen die meisten Web-App-Sicherheitsprobleme alle Blazor Server-Apps. In diesem Artikel werden häufige Bedrohungen für Blazor Server-Apps beschrieben. Außerdem enthält er Anleitungen zur Bedrohungsminderung, die auf internetseitige Apps ausgerichtet sind.

In eingeschränkten Umgebungen wie Unternehmensnetzwerken oder Intranets:

* treffen einige der Hinweise aus diesem Artikel nicht zu
* rechnen sich die Implementierungskosten für einige der Hinweise aus diesem Artikel nicht, da das Sicherheitsrisiko in eingeschränkten Umgebungen gering ist.

## <a name="no-locblazor-and-shared-state"></a>Blazor und freigegebener Zustand

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a>Ressourcenerschöpfung

Ressourcenerschöpfung kann auftreten, wenn ein Client mit dem Server interagiert und eine exzessive Ressourcenverbrauch auf dem Server verursacht. Übermäßiger Ressourcenverbrauch beeinträchtigt maßgeblich die folgenden Komponenten:

* [CPU](#cpu)
* [Arbeitsspeicher](#memory)
* [Clientverbindungen](#client-connections)

Das Ziel von Denial-of-Service-Angriffen (DoS) ist üblicherweise, die Ressourcen einer App oder eines Servers zu erschöpfen. Ressourcenerschöpfung ist jedoch nicht notwendigerweise das Ergebnis eines Angriffs auf das System. Endliche Ressourcen können z. B. aufgrund einer hohen Benutzernachfrage aufgebraucht werden. Auf DoS-Angriffe wird weiter unten im Abschnitt [Denial-of-Service-Angriffe (DoS)](#denial-of-service-dos-attacks) eingegangen.

Blazor-externe Ressourcen wie Datenbanken und Dateihandles (für den Lese- und Schreibvorgänge in Dateien) können ebenso erschöpft werden. Weitere Informationen finden Sie unter <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

CPU-Erschöpfung kann auftreten, wenn mindestens ein Client den Server zu CPU-intensiven Prozessen zwingt.

Ein Beispiel wäre eine Blazor Server-App, die eine *Fibonacci-Zahl* berechnet. Fibonacci-Zahlen sind die Bestandteile einer Fibonnacci-Reihe, in der jede Zahl die Summe der beiden vorherigen Zahlen darstellt. Der für die Lösung erforderliche Arbeitsaufwand hängt von der Länge der Reihe und der Größe des ersten Wertes ab. Wenn die App die Clientanforderung nicht einschränkt, dominieren die CPU-intensiven Berechnungen unter Umständen die CPU-Zeit und schmälern die Leistung von anderen Tasks. Exzessiver Ressourcenverbrauch ist ein Sicherheitsproblem, das sich auf die Verfügbarkeit auswirkt.

CPU-Erschöpfung ist ein Problem, das alle öffentlich zugänglichen Apps betrifft. In regulären Web-Apps wird für Anforderungen und Verbindungen als Schutzvorkehrung ein Timeoutzeitraum festgelegt. In Blazor Server-Apps sind diese Schutzmaßnahmen jedoch nicht vorgesehen. Blazor Server-Apps müssen die richtigen Überprüfungen und Grenzwerte enthalten, bevor potenziell CPU-intensive Prozesse ausgeführt werden.

### <a name="memory"></a>Arbeitsspeicher

Arbeitsspeichererschöpfung kann auftreten, wenn mindestens ein Client den Server zwingt, große Teile des Arbeitsspeichers zu belegen.

Ein Beispiel wäre eine serverseitige Blazor-App mit einer Komponente, die eine Elementliste akzeptiert und anzeigt. Wenn die Anzahl der zulässigen Elemente oder die Anzahl der an den Client gerenderten Elemente nicht durch die Blazor-App eingeschränkt wird, dominieren die arbeitsspeicherintensiven Verarbeitungs- und Renderingprozesse unter Umständen den Arbeitsspeicher des Servers und ziehen die Serverleistung in Mitleidenschaft. Der Server kann infolge abstürzen oder so langsam werden, dass es scheint, als sei er abgestürzt.

Stellen Sie sich das folgende Szenario vor, in dem eine Elementliste gewartet und angezeigt wird, die mit einer potenziellen Arbeitsspeichererschöpfung auf dem Server zusammenhängt:

* Die Elemente in einer `List<MyItem>`-Eigenschaft oder einem solchen Feld nutzen den Arbeitsspeicher des Servers. Wenn die App zulässt, dass die Elementliste uneingeschränkt wächst, besteht die Gefahr, dass der Arbeitsspeicher des Servers irgendwann vollständig belegt ist. Wenn der Arbeitsspeicher erschöpft ist, wird die aktuelle Sitzung beendet (stürzt ab), und alle gleichzeitigen Sitzungen in dieser Serverinstanz empfangen eine Ausnahme mit einem Hinweis auf unzureichenden Arbeitsspeicher. Damit dieses Szenario nicht auftritt, muss die App eine Datenstruktur verwenden, die eine Elementobergrenze für gleichzeitige Benutzer durchsetzt.
* Wenn beim Rendering kein Paginierungsschema verwendet wird, belegt der Server zusätzlichen Arbeitsspeicher für Objekte, die nicht in der Benutzeroberfläche angezeigt werden. Ohne eine Obergrenze für die Anzahl der Elemente kann der verfügbare Arbeitsspeicher durch die Nachfrage erschöpft werden. Dieses Szenario lässt sich mit einem der folgenden Ansätze verhindern:
  * Verwenden von paginierten Listen beim Rendering
  * Anzeigen der ersten 100 bis 1.000 Elemente und Durchsetzen, dass Benutzer Suchkriterien eingeben, um andere Elemente als die angezeigten zu finden
  * In einem fortgeschrittenen Renderingszenario können Sie Listen oder Raster implementieren, die *Virtualisierung* unterstützen. Wenn Virtualisierung eingesetzt wird, rendern Listen nur eine Teilmenge der Elemente, die für den Benutzer aktuell sichtbar sind. Interagiert der Benutzer in der Benutzeroberfläche mit der Scrollleiste, rendert die Komponente nur diejenigen Elemente, die angezeigt werden sollen. Aktuell nicht angezeigte Elemente werden idealerweise im sekundären Speicher aufbewahrt. Sie können auch im Speicher abgelegt werden, was jedoch weniger empfehlenswert ist.

Blazor Server-Apps bieten ein ähnliches Programmiermodell wie andere Benutzeroberflächenframeworks für zustandsbehaftete Apps wie WPF, Windows Forms oder Blazor WebAssembly. Der Hauptunterschied besteht darin, dass der von der App benutzte Arbeitsspeicher in vielen Benutzeroberflächenframeworks dem Client gehört und sich die Auswirkungen daher auf den einzelnen Client beschränken. Eine Blazor WebAssembly-App wird z. B. vollständig auf dem Client ausgeführt und verwendet ausschließlich die Arbeitsspeicherressourcen des Clients. Im Blazor Server-Szenario gehört der von der App verbrauchte Arbeitsspeicher dem Server und wird von den Clients auf der Serverinstanz gemeinsam genutzt.

Der serverseitige Arbeitsspeicherbedarf muss bei allen Blazor Server-Apps bedacht werden. Die meisten Web-Apps sind jedoch zustandslos, und der für die Verarbeitung einer Anforderung belegte Arbeitsspeicher wird wieder freigegeben, nachdem die Antwort zurückgegeben wurde. Es ist allgemein empfehlenswert, es Clients nicht zu erlauben, den Arbeitsspeicher uneingeschränkt zu belegen, so wie bei anderen serverseitigen Apps mit dauerhaften Clientverbindungen auch. Der von einer Blazor Server-App verbrauchte Arbeitsspeicher bleibt auch nach der Verarbeitung einer einzelnen Anforderung belegt.

> [!NOTE]
> Während der Entwicklung kann mithilfe eines Profilers oder einer Überwachung der Arbeitsspeicherbedarf von Clients bewertet werden. Ein Profiler oder eine Überwachung erfassen jedoch nicht den von einem bestimmten Client belegten Arbeitsspeicher. Wenn Sie den Arbeitsspeicherverbrauch eines bestimmten Clients während der Entwicklung bestimmen möchten, müssen Sie eine Sicherungskopie erfassen und den Arbeitsspeicherbedarf aller Objekte untersuchen, die sich im Circuit eines Benutzers befinden.

### <a name="client-connections"></a>Clientverbindungen

Verbindungserschöpfung kann auftreten, wenn mindestens ein Client zu viele gleichzeitige Verbindungen zum Server geöffnet hat und so andere Clients daran hindert, neue Verbindungen herzustellen.

Blazor-Clients stellen eine Verbindung pro Sitzung her und halten diese so lange offen, wie auch das Browserfenster geöffnet ist. Die Anforderung an den Server, alle Verbindungen aufrechtzuerhalten, ist nicht charakteristisch für Blazor-Apps. Da die Verbindungen dauerhaft und Blazor Server-Apps zustandsbehaftet sind, stellt Verbindungserschöpfung ein größeres Risiko für die Verfügbarkeit der App dar.

In Blazor Server-Apps wird die Anzahl der Verbindungen pro Benutzer nicht automatisch beschränkt. Wenn die App eine Verbindungsobergrenze erfordert, sollten Sie mindestens eine der folgenden Maßnahmen ergreifen:

* Erzwingen der Authentifizierung, wodurch nicht autorisierten Benutzern automatisch die Möglichkeit genommen wird, eine Verbindung zur App herzustellen. Damit dieses Szenario eintritt, müssen Sie verhindern, dass Benutzer willkürlich neue Benutzer bereitstellen.
* Beschränken der pro Benutzer zulässigen Anzahl von Verbindungen. Verbindungen lassen sich mithilfe der folgenden Maßnahmen beschränken. Wägen Sie sorgfältig ab, bevor Sie legitimen Benutzern Zugriff auf die App erteilen (z. B., wenn eine Verbindungsobergrenze für die IP-Adresse eines Clients festgelegt wird).
  * Auf App-Ebene:
    * Erweiterung des Endpunktroutings
    * Durchsetzen der Authentifizierung bei der Verbindungsherstellung zur App und Nachverfolgen der aktiven Sitzungen pro Benutzer
    * Ablehnen neuer Sitzungen, wenn die Obergrenze erreicht wird
    * Proxy-WebSocket-Verbindungen zu einer App mithilfe eines Proxys wie [Azure SignalR Service](/azure/azure-signalr/signalr-overview). Dieser Dienst bündelt Client-zu-App-Verbindungen. Auf diese Weise kann eine App mehr Verbindungen akzeptieren, als ein einzelner Client herstellen kann. Dies verhindert, dass ein Client alle Verbindungen zum Server belegt.
  * Auf Serverebene: Verwenden eines der App vorgeschalteten Proxys/Gateways. Mit [Azure Front Door](/azure/frontdoor/front-door-overview) können Sie beispielsweise das globale Routing des Webdatenverkehrs zur App definieren, verwalten und überwachen. Es funktioniert, wenn Blazor Server-Apps für lange Abrufintervalle konfiguriert ist.
  
    > [!NOTE]
    > Obwohl lange Abrufintervalle für Blazor Server-Apps unterstützt werden, ist [WebSockets das empfohlene Transportprotokoll](xref:blazor/host-and-deploy/server#azure-signalr-service). [Azure Front Door](/azure/frontdoor/front-door-overview) unterstützt WebSockets derzeit nicht, aber die Unterstützung von WebSockets wird für eine zukünftige Version des Diensts erwogen.

## <a name="denial-of-service-dos-attacks"></a>Denial-of-Service-Angriffe (DoS)

Bei DoS-Angriffen verursacht ein Client die Erschöpfung von mindestens einer Serverressource, sodass die App nicht mehr verfügbar ist. Blazor Server-Apps enthalten einige Standardgrenzwerte und nutzen andere Grenzwerte aus ASP.NET Core und SignalR, um sich gegen DoS-Angriffe auf <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>-Objekte zu schützen.

| Blazor Server-App-Grenze | Beschreibung | Standard |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | Maximale Anzahl der getrennten Circuits, die sich gleichzeitig im Arbeitsspeicher eines bestimmten Servers befinden | 100 |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | Maximale Zeitspanne, die ein getrennter Circuit im Arbeitsspeicher aufbewahrt wird, bevor er entfernt wird | 3 Minuten |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | Maximale Zeitspanne, die der Server wartet, bevor der Aufruf einer asynchronen JavaScript-Funktion durch ein Timeout beendet wird | 1 Minute |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | Maximale Anzahl nicht unbestätigter Renderbatches, die der Server zu einem bestimmten Zeitpunkt pro Circuit im Arbeitsspeicher aufbewahrt, um eine stabile Neuverbindung zu ermöglichen. Wenn die Obergrenze erreicht wird, generiert der Server keine neuen Renderbatches mehr, bis mindestens ein Batch vom Client bestätigt wurde. | 10 |

Mit <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions> können Sie die maximale Größe einer einzelnen eingehenden Hubnachricht festlegen.

| Grenzwert für SignalR und ASP.NET Core | Beschreibung | Standard |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.SignalR.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | Nachrichtengröße für eine einzelne Nachricht | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interaktionen mit dem Browser (Client)

Ein Client interagiert über die von JS-Interop gesendeten Ereignisse und Meldungen zur Renderingfertigstellung mit dem Server. Die Kommunikation von JS-Interop verläuft bidirektional zwischen JavaScript und .NET:

* Browserereignisse werden asynchron vom Client an den Server gesendet.
* Der Server antwortet asynchron und rendert die Benutzeroberfläche nach Bedarf neu.

### <a name="javascript-functions-invoked-from-net"></a>Über .NET aufgerufene JavaScript-Funktionen

Für .NET-Methoden, die JavaScript aufrufen:

* Für alle Aufrufe ist ein Timeout festgelegt. Dies bedeutet, dass Aufrufe fehlschlagen, sobald der Wert überschritten wird, und <xref:System.OperationCanceledException> an den Aufrufer zurückgegeben wird.
  * Das Standardtimeout für Aufrufe (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) liegt bei einer Minute. Weitere Informationen dazu, wie Sie das Timeout konfigurieren, finden Sie unter <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.
  * Ein Abbruchtoken kann bereitgestellt werden, um den Abbruch aufrufsweise zu steuern. Verwenden Sie nach Möglichkeit das Standardtimeout für Clientaufrufe. Ein Zeitintervall sollte nur festgelegt werden, wenn ein Abbruchtoken bereitgestellt wird.
* Das Ergebnis eines JavaScript-Aufrufs ist nicht vertrauenswürdig. Die Blazor-App-Client, der im Browser ausgeführt wird, sucht nach der aufzurufenden JavaScript-Funktion. Die Funktion wird aufgerufen, und es wird entweder das Ergebnis oder ein Fehler zurückgegeben. Ein böswilliger Client kann Folgendes versuchen:
  * Verursachen eines Problems in der App, indem von der JavaScript-Funktion ein Fehler zurückgegeben wird
  * Einführen eines unbeabsichtigten Verhaltens auf dem Server, indem ein unerwartetes Ergebnis von der JavaScript-Funktion zurückgegeben wird

Treffen Sie die folgenden Vorkehrungen, um sich gegen die zuvor genannten Szenarios zu schützen:

* Umschließen Sie Aufrufe von JS-Interop in [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisungen, um während der Aufrufe gegebenenfalls Fehler abzufangen. Weitere Informationen finden Sie unter <xref:blazor/fundamentals/handle-errors#javascript-interop>.
* Überprüfen Sie die Daten, die bei JS-Interop-Aufrufen zurückgegeben werden, einschließlich der Fehlermeldungen, bevor Sie etwas unternehmen.

### <a name="net-methods-invoked-from-the-browser"></a>Über den Browser aufgerufene .NET-Methoden

Vertrauen Sie keinen von JavaScript aus aufgerufenen .NET-Methoden. Wenn eine .NET-Methode für JavaScript verfügbar gemacht wird, sollten Sie sich ansehen, wie die .NET-Methode aufgerufen wird:

* Behandeln Sie jede .NET-Methode, die für JavaScript verfügbar gemacht wird, wie einen öffentlichen App-Endpunkt.
  * Überprüfen Sie die Eingabe.
    * Stellen Sie sicher, dass die Werte innerhalb der erwarteten Bereiche liegen.
    * Stellen Sie sicher, dass der Benutzer dazu berechtigt ist, die angeforderte Aktion auszuführen.
  * Belegen Sie durch den Aufruf der .NET-Methode nicht übermäßig viele Ressourcen. Führen Sie zum Beispiel Überprüfungen aus, und legen Sie Obergrenzen für die CPU- und Arbeitsspeicherauslastung fest.
  * Beachten Sie, dass statische Methoden und Instanzmethoden für JavaScript-Clients verfügbar gemacht werden können. Zustände sollten nicht sitzungsübergreifend verwendet werden, es sei denn, das Design gibt dies vor, und es sind angemessene Beschränkungen vorgesehen.
    * Für Instanzmethoden, die über `DotNetReference`-Objekte verfügbar gemacht werden, die ursprünglich per Dependency Injection (DI) erstellt wurden, sollten die Objekte als bereichsbezogen registriert werden. Dies gilt für alle DI-Dienste, die die Blazor Server-App nutzt.
    * Bei statischen Methoden sollten Sie vermeiden, einen Zustand herzustellen, der sich auf den Client festlegen lässt. Die einzige Ausnahme stellen Apps dar, in denen Zustände absichtlich für alle Benutzer auf einer Serverinstanz verwendet.
  * Von Benutzern bereitgestellte Daten sollten nicht als Parameter für JavaScript-Aufrufe genutzt werden. Wenn das Übergeben von Daten in Parametern absolut notwendig ist, sollten Sie sicherstellen, dass der JavaScript-Code bei der Datenübergabe keine Gelegenheit für [Cross-Site-Scripting (XSS)](#cross-site-scripting-xss) bietet. Schreiben Sie beispielsweise keine von Benutzern bereitgestellten Daten in das Dokumentobjektmodell (DOM), indem Sie die Eigenschaft `innerHTML` eines Elements festlegen. Außerdem können Sie mit [Content Security Policy](https://developer.mozilla.org/docs/Web/HTTP/CSP) (zu Deutsch: „Richtlinie für Inhaltssicherheit“) ggf. `eval` und andere unsichere primitive JavaScript-Datentypen deaktivieren.
* Implementieren Sie keine benutzerdefinierte Sendung von .NET-Aufrufen zusätzlich zur Sendungsimplementierung des Frameworks. Das Verfügbarmachen von .NET-Methoden für den Browser ist ein fortgeschrittenes Szenario, das sich nicht als allgemeine Blazor-Entwicklungstechnik empfiehlt.

### <a name="events"></a>Ereignisse

Ereignisse stellen einen Einstiegspunkt in eine Blazor Server-App dar. Für das Ereignishandling in Blazor Server-Apps gelten dieselben Regeln wie für das Schützen von Endpunkten in Web-Apps. Ein böswilliger Client kann beliebige Daten als Nutzlast für ein Ereignis senden.

Zum Beispiel:

* Ein Änderungsereignis für `<select>` könnte einen Wert senden, der nicht innerhalb der Optionen liegt, die die App dem Client präsentiert hat.
* `<input>` könnte beliebige Textdaten an den Server senden und dabei die clientseitige Überprüfung umgehen.

Die App muss die Daten für alle Ereignisse überprüfen, die die App verarbeitet. Die [Formularkomponenten](xref:blazor/forms-validation) des Blazor-Frameworks führen grundlegende Überprüfungen aus. Wenn die App benutzerdefinierte Formularkomponenten verwendet, müssen Benutzer Code schreiben, damit die Ereignisdaten richtig ausgewertet werden.

Blazor Server-Ereignisse sind asynchron, sodass mehrere Ereignisse an den Server gesendet werden können, bevor die App darauf reagieren und ein neues Rendering produzieren kann. Dies birgt einige Sicherheitsrisiken, die bedacht werden sollten. Clientaktionen in der App müssen innerhalb des Ereignishandlers beschränkt werden und dürfen nicht vom aktuell gerenderten Anzeigezustand abhängen.

Stellen Sie sich eine Zählerkomponente vor, mit der ein Benutzer einen Zähler höchstens dreimal erhöhen können soll. Die Schaltfläche zum Hochsetzen des Zählers basiert bedingungsweise auf dem Wert von `count`:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Ein Client kann mindestens ein Inkrementierungsereignis senden, bevor das Framework ein neues Rendering dieser Komponente generiert. Dies führt dazu, dass `count` *mehr als dreimal* vom Benutzer inkrementiert werden kann, weil die Schaltfläche nicht schnell genug aus der Benutzeroberfläche entfernt wird. Die richtige Vorgehensweise für das Durchsetzen der Obergrenze von drei `count`-Inkrementen wird im folgenden Beispiel veranschaulicht:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Indem die Überprüfung `if (count < 3) { ... }` innerhalb des Handlers hinzugefügt wird, wird die Entscheidung über das Inkrementieren von `count` anhand des aktuellen App-Zustands getroffen. Die Entscheidung basiert nicht wie im vorherigen Beispiel auf dem Zustand der Benutzeroberfläche, der vorübergehen veraltet sein kann.

### <a name="guard-against-multiple-dispatches"></a>Wächter gegen mehrere Sendungen

Wenn ein Ereignisrückruf einen zeitintensiven Vorgang wie das Abrufen von Daten aus einem externen Dienst oder einer Datenbank asynchron aufruft, sollten Sie möglicherweise einen Wächter einsetzen. Der Wächter kann verhindern, dass Benutzer mehrere Vorgänge an die Warteschlange senden, während der Vorgang bearbeitet und visuelles Feedback geliefert wird. Der folgende Komponentencode legt `isLoading` auf `true` fest, während `GetForecastAsync` Daten vom Server abruft. Während `isLoading` `true` ist, ist die Schaltfläche in der Benutzeroberfläche deaktiviert:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Das im vorherigen Beispiel gezeigte Wächtermuster funktioniert, wenn der Hintergrundvorgang asynchron mit dem `async`-`await`-Muster ausgeführt wird.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Frühes Abbrechen und Vermeiden der Verwendung nach der Entfernung (use-after-dispose)

Zusätzlich zu einem Wächter (Abschnitt [Wächter gegen mehrere Sendungen](#guard-against-multiple-dispatches)) sollten Sie auch ein Abbruchtoken (<xref:System.Threading.CancellationToken>) verwenden, um zeitintensive Vorgänge nach dem Entfernen der Komponente abbrechen zu können. Dieser Ansatz bietet den zusätzlichen Vorteil, dass eine Verwendung nach der Entfernung (*use-after-dispose*) in Komponenten vermieden wird:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Vermeiden von großen Datenmengen produzierenden Ereignissen

Einige DOM-Ereignisse wie `oninput` oder `onscroll` können eine große Menge von Daten generieren. Diese Ereignisse sollten nicht in Blazor-Server-Apps verwendet werden.

## <a name="additional-security-guidance"></a>Zusätzlicher Sicherheitsleitfaden

Der Leitfaden zur Sicherung von ASP.NET Core-Apps gilt für Blazor Server-Apps und enthält die folgenden Abschnitte:

* [Protokollierung und vertrauliche Daten](#logging-and-sensitive-data)
* [Schützen von Informationen während der Übertragung mit HTTPS](#protect-information-in-transit-with-https)
* [Cross-Site-Scripting (XSS)](#cross-site-scripting-xss)
* [Ursprungsübergreifender Schutz](#cross-origin-protection)
* [Clickjacking](#click-jacking)
* [Open Redirects](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Protokollierung und vertrauliche Daten

JS-Interop-Interaktionen zwischen dem Client und dem Server werden in den Serverprotokollen mit <xref:Microsoft.Extensions.Logging.ILogger>-Instanzen erfasst. In Blazor werden keine vertraulichen Informationen wie tatsächliche Ereignisse oder JS-Interop-Ein- und -Ausgaben protokolliert.

Wenn auf dem Server ein Fehler auftritt, benachrichtigt das Framework den Client und beendet die Sitzung. Der Client empfängt automatisch eine Standardfehlermeldung, die in den Entwicklertools des Browsers aufgerufen werden kann.

Die clientseitige Fehlermeldung enthält weder den Aufrufstapel noch Details zur Fehlerursache, die Serverprotokolle hingegen schon. Zu Entwicklungszwecken können vertrauliche Fehlerinformationen für den Client verfügbar gemacht werden, indem Sie detaillierte Fehlermeldungen aktivieren.

In JavaScript werden detaillierte Fehlermeldungen wie folgt aktiviert:

* <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.
* Der Konfigurationsschlüssel `DetailedErrors` muss auf `true` festgelegt werden. Diese Einstellung können Sie in der Einstellungsdatei der App (`appsettings.json`) vornehmen. Der Schlüssel lässt sich darüber hinaus mithilfe der Umgebungsvariablen `ASPNETCORE_DETAILEDERRORS` auf den Wert `true` festlegen.

> [!WARNING]
> Das Verfügbarmachen von Fehlerinformationen für internetseitige Clients ist ein Sicherheitsrisiko, dass immer vermieden werden sollte.

### <a name="protect-information-in-transit-with-https"></a>Schützen von Informationen während der Übertragung mit HTTPS

Blazor Server stellt die Kommunikation zwischen Client und Server mithilfe von SignalR bereit. Normalerweise verwendet Blazor Server den Transport, der von SignalR ausgehandelt wird. Dabei handelt es sich in der Regel um WebSockets.

Blazor Server bietet keine Garantie für die Integrität und Vertraulichkeit der Daten, die zwischen Server und Client gesendet werden. Verwenden Sie daher immer HTTPS.

### <a name="cross-site-scripting-xss"></a>Cross-Site-Scripting (XSS)

XSS ermöglicht es einem nicht autorisierten Benutzer, eine beliebige Logik im Kontext des Browsers auszuführen. Eine kompromittierte App kann beliebigen Code auf dem Client ausführen. Das Sicherheitsrisiko kann ausgenutzt werden, um eine Reihe schädlicher Aktionen für den Server auszuführen:

* Senden gefälschter/ungültiger Ereignisse an den Server
* Senden von fehlgeschlagenen/ungültigen Meldungen zur Renderingfertigstellung
* Vermeiden des Sendens von Meldungen zur Renderingfertigstellung
* Senden von Interop-Aufrufen aus JavaScript an .NET
* Ändern der Antwort von Interop-Aufrufen aus .NET an JavaScript
* Vermeiden des Sendens von .NET-Ergebnissen an JS-Interop

Das Blazor Server-Framework bietet die folgenden Maßnahmen zum Schutz vor einigen der zuvor genannten Bedrohungen:

* Es werden keine neuen Benutzeroberflächenupdates generiert, wenn der Client die Renderbatches nicht bestätigt. Dies wird mit <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType> konfiguriert.
* Für alle JavaScript-Aufrufe von .NET aus tritt nach einer Minute ein Timeout auf, ohne dass der Client eine Antwort sendet. Dies wird mit <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType> konfiguriert.
* Während JS-Interop wird eine grundlegende Überprüfung für alle Eingaben ausgeführt, die aus dem Browser stammen:
  * .NET-Verweise sind gültig und weisen den von der .NET-Methode erwarteten Typ auf.
  * Die Daten sind nicht falsch formatiert.
  * Die Nutzlast enthält die korrekte Anzahl von Argumenten für die Methode.
  * Die Argumente oder das Ergebnis lässt sich korrekt deserialisieren, bevor die Methode aufgerufen wird.
* Eine grundlegende Überprüfung wird für alle Eingaben ausgeführt, die aus dem Browser aus gesendeten Ereignissen stammen:
  * Das Ereignis weist einen gültigen Typ auf.
  * Die Daten für das Ereignis lassen sich deserialisieren.
  * Es gibt einen Ereignishandler, der dem Ereignis zugeordnet ist.

Zusätzlich zu den vom Framework implementierten Schutzmaßnahmen muss die App vom Entwickler so programmiert werden, dass sie gegen Bedrohungen geschützt ist und die angemessenen Aktionen ausführen kann:

* Beim Ereignishandling müssen Daten immer überprüft werden.
* Werden ungültige Daten empfangen, müssen die angemessenen Aktionen ausgeführt werden:
  * Die Daten und die Rückgabe müssen ignoriert werden. Dies ermöglicht es der App, weiterhin Anforderungen zu verarbeiten.
  * Wenn die App eine ungültige Eingabe erkennt, die nicht von einem legitimen Client stammen kann, wird eine Ausnahme zurückgegeben. Aufgrund der Ausnahme werden sowohl der Circuit als auch die Sitzung beendet.
* Vertrauen Sie nicht der Fehlermeldung, die in den Fertigstellungsmeldungen zum Batchrendering in den Protokollen enthalten ist. Sie stammt vom Client und ist allgemein nicht vertrauenswürdig, da der Client möglicherweise kompromittiert ist.
* Vertrauen Sie nicht der Eingabe für JS-Interop-Aufrufe zwischen JavaScript und .NET-Methoden (beide Richtungen).
* Die App ist dafür zuständig, dass die Gültigkeit der Argumente und Ergebnisse überprüft wird, selbst wenn die Argumente oder Ergebnisse korrekt deserialisiert wurden.

Das Risiko von XSS besteht nur, wenn die App Benutzereingaben in die gerenderte Seite einbindet. Blazor Server-Komponenten führen einen Schritt zur Kompilierzeit aus, bei dem Markup in einer `.razor`-Datei in eine prozedurale C#-Logik transformiert wird. Zur Laufzeit erstellt die C#-Logik eine *Renderingstruktur*, die die Elemente, den Text und die untergeordneten Komponenten beschreibt. Diese wird mithilfe mehrerer JavaScript-Anweisungen auf das DOM des Browsers angewendet (oder im Falle von Prerendering in HTML serialisiert):

* Benutzereingaben, die über eine normale Razor-Syntax gerendert werden (z. B. `@someStringValue`) stellen kein XSS-Risiko dar, weil die Razor-Syntax mithilfe von Befehlen, die nur Text schreiben können, dem DOM hinzugefügt wird. Selbst wenn der Wert HTML-Markup enthält, wird er als statischer Text angezeigt. Während des Prerenderings wird die Ausgabe HTML-codiert, wodurch der Inhalt ebenfalls als statischer Text angezeigt wird.
* Skripttags sind nicht zulässig und sollten in der Komponentenrenderingstruktur der App nicht verwendet werden. Wenn Sie ein Skripttag das Markup einer Komponente aufnehmen, wird ein Kompilierzeitfehler zurückgegeben.
* Komponentenersteller können auch ohne Razor Komponenten in C# erstellen. Der Komponentenersteller ist dafür verantwortlich, das beim Ausgeben der Ausgabe die richtigen APIs benutzt werden. Es muss beispielsweise `builder.AddContent(0, someUserSuppliedString)` und *nicht* `builder.AddMarkupContent(0, someUserSuppliedString)` verwendet werden, da letztere API das Risiko von XSS birgt.

Zum Schutz vor XSS-Angriffen können Sie auch Maßnahmen zur Risikominderung ergreifen, z. B. [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) konfigurieren.

Weitere Informationen finden Sie unter <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Ursprungsübergreifender Schutz

Bei ursprungsübergreifenden Angriffen führt ein Client, der einen anderen Ursprung hat, eine Aktion für den Server aus. Bei der böswilligen Aktion handelt es sich üblicherweise um eine GET-Anforderung oder eine Art von POST-Methode (Cross-Site Request Forgery, CSRF). Es ist jedoch ebenso möglich, dass eine böswillige WebSocket-Verbindung geöffnet wird. Blazor Server-Apps bieten [dieselben Garantien wie jede andere SignalR-App, die das angebotene Hubprotokoll verwendet](xref:signalr/security):

* Es besteht ursprungsübergreifender Zugriff auf Blazor Server-Apps, es sei denn, dies wird durch zusätzliche Schutzmaßnahmen verhindert. Wenn Sie den ursprungsübergreifenden Zugriff deaktivieren möchten, müssen Sie entweder Cross-Origin Resource Sharing (CORS) im Endpunkt deaktivieren, indem Sie die CORS-Middleware der Pipeline und das <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute>-Objekt den Metadaten des Blazor-Endpunkts hinzufügen oder die zulässigen Ursprünge eingrenzen, indem Sie [SignalR für Cross-Origin Resource Sharing konfigurieren](xref:signalr/security#cross-origin-resource-sharing).
* Wenn CORS aktiviert ist, sind je nach CORS-Konfiguration unter Umständen weitere Schritte erforderlich, um die App zu schützen. Ist CORS global aktiviert, lässt sich der Mechanismus für den Blazor Server-Hub deaktivieren, indem die <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute>-Metadaten den Endpunktmetadaten hinzugefügt werden, nachdem <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.MapBlazorHub%2A> im Endpunktroutengenerator aufgerufen wurde.

Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Clickjacking

Beim Clickjacking wird eine Website als `<iframe>` innerhalb einer Website gerendert, die einen anderen Ursprung hat. So soll der Benutzer überlistet und dazu gebracht werden, Aktionen auf der angegriffenen Website auszuführen.

Mithilfe von [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) und dem Header `X-Frame-Options` können Sie eine App davor schützen, innerhalb eines Inlineframes (`<iframe>`) gerendert zu werden. Weitere Informationen finden Sie unter [MDN Web Docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Open Redirects

Wenn eine Blazor Server-App-Sitzung beginnt, überprüft der Server grundlegend die URLs, die beim Sitzungsstart gesendet wurden. Das Framework überprüft, ob es sich bei der Basis-URL um eine übergeordnete URL der aktuellen handelt, bevor der Circuit hergestellt wird. Weitere Überprüfungen führt das Framework nicht aus.

Wenn ein Benutzer auf dem Client auf einen Link klickt, wird die Link-URL an den Server gesendet. Dieser entscheidet wiederum, welche Aktion anschließend ausgeführt wird. Die App kann beispielsweise eine clientseitige Navigation ausführen oder den Browser anweisen, einen neuen Inhalt aufzurufen.

Komponenten können außerdem mithilfe von <xref:Microsoft.AspNetCore.Components.NavigationManager> Navigationsanforderungen programmgesteuert auslösen. In einem solchen Szenario kann die App eine clientseitige Navigation ausführen oder den Browser anweisen, einen neuen Inhalt aufzurufen.

Für Komponenten gilt Folgendes:

* Benutzereingaben dürfen nicht in Argumenten von Navigationsaufrufen verwendet werden.
* Argumente müssen überprüft werden, um sicherzustellen, dass das Ziel von der App erlaubt wird.

Andernfalls kann ein böswilliger Benutzer den Browser zwingen, eine vom Angreifer kontrollierte Website aufzurufen. In diesem Szenario überlistet der Angreifer die App, sodass diese eine Benutzereingabe im Rahmen eines Aufrufs der <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>-Methode verwendet.

Diese Empfehlung gilt auch, wenn Links als App-Inhalt gerendert werden:

* Wenn möglich, sollten Sie sich immer für relative Links entscheiden.
* Überprüfen Sie, ob die Ziele von absoluten Links gültig sind, bevor Sie die Links in eine Seite einbetten.

Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

## <a name="security-checklist"></a>Checkliste für die Sicherheit

Die folgende Liste von Sicherheitsmaßnahmen ist nicht abschließend:

* Überprüfen Sie die Argumente von Ereignissen.
* Überprüfen Sie die Eingaben und Ergebnisse aus JS-Interop-Aufrufen.
* Vermeiden Sie Benutzereingaben in JS-Interop-Aufrufen von .NET aus (oder überprüfen Sie diese vorher).
* Verhindern Sie, dass der Client unbegrenzt Arbeitsspeicher belegen kann.
  * Daten innerhalb der Komponente
  * `DotNetObject`-Verweise, die an den Client zurückgegeben werden
* Schützen Sie sich vor mehreren Sendungen.
* Brechen Sie zeitintensive Vorgängen ab, wenn die Komponente entfernt wird.
* Vermeiden Sie Ereignisse, die große Datenmengen produzieren.
* Benutzereingaben sollten nie für <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType>-Aufrufe verwendet werden, und überprüfen Sie Benutzereingaben für URLs zuerst auf zulässige Ursprünge, wenn dies unvermeidlich ist.
* Treffen Sie keine Autorisierungsentscheidungen auf Grundlage des Zustands der Benutzeroberfläche, sondern nur anhand des Komponentenzustands.
* Verwenden Sie ggf. [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP), um Apps vor XSS-Angriffen zu schützen.
* Verwenden Sie ggf. CSP und [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options), um Apps vor Clickjacking zu schützen.
* Stellen Sie sicher, dass die CORS-Einstellungen angemessen sind, wenn Sie CORS aktivieren, oder deaktivieren Sie CORS explizit für Blazor-Apps.
* Führen Sie Tests durch, um sicherzustellen, dass die serverseitigen Grenzwerte für die Blazor-App eine akzeptable Nutzungsqualität gewährleisten und keine nicht akzeptablen Risiken bergen.
