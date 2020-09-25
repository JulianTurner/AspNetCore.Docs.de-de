---
title: Blazor-Hostingmodelle in ASP.NET Core
author: guardrex
description: Grundlegendes zu den Blazor WebAssembly- und Blazor Server-Hostingmodellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/11/2020
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
uid: blazor/hosting-models
ms.openlocfilehash: 0830214029d59d0820ed3c5806267c766314c74f
ms.sourcegitcommit: 8fcb08312a59c37e3542e7a67dad25faf5bb8e76
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90009673"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a>Blazor-Hostingmodelle in ASP.NET Core

Von [Daniel Roth](https://github.com/danroth27)

Das Webframework Blazor kann auf Clientseite im Browser in einer auf [WebAssembly](https://webassembly.org/) basierenden .NET-Runtime ( *Blazor WebAssembly* ) oder auf Serverseite in ASP.NET Core ( *Blazor Server* ) ausgeführt werden. Die App und die Komponentenmodelle sind für alle Hostingmodelle *identisch*.

## Blazor WebAssembly

Das wichtigste Hostingmodell für Blazor wird clientseitig im Browser in WebAssembly ausgeführt. Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser heruntergeladen. Die App wird direkt im UI-Thread des Browsers ausgeführt. Die Aktualisierung der Benutzeroberfläche und die Ereignisbehandlung erfolgen im selben Prozess. Die Ressourcen der App werden als statische Dateien auf einem Webserver oder für einen Dienst bereitgestellt, die geeignet sind, statische Inhalte für Clients bereitzustellen. Da die App für die Bereitstellung ohne App mit einem ASP.NET Core-Back-End erstellt wird, wird sie als *eigenständige Blazor WebAssembly-App* bezeichnet.

![Blazor WebAssembly: Die Blazor-App wird in einem Benutzeroberflächenthread im Browser ausgeführt.](hosting-models/_static/blazor-webassembly.png)

Verwenden Sie zum Erstellen einer Blazor-App mithilfe des clientseitigen Hostingmodells die **Blazor WebAssembly-App**-Vorlage ([`dotnet new blazorwasm`](/dotnet/core/tools/dotnet-new)).

Nachdem Sie die **Blazor WebAssembly-App**-Vorlage ausgewählt haben, können Sie die App so konfigurieren, dass sie ein ASP.NET Core-Back-End verwendet. Aktivieren Sie hierzu das Kontrollkästchen **In ASP.NET Core gehostet** ([`dotnet new blazorwasm --hosted`](/dotnet/core/tools/dotnet-new)). Die ASP.NET Core-App stellt Clients die Blazor-App bereit. Eine App mit einem ASP.NET Core-Back-End wird als *gehostete Blazor WebAssembly-App* bezeichnet. Die Blazor WebAssembly-App kann mit dem Server über das Netzwerk mithilfe von API-Aufrufen oder [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>) interagieren.

Das `blazor.webassembly.js`-Skript wird vom Framework und von den Handles bereitgestellt:

* Herunterladen der .NET-Runtime, der App und der App-Abhängigkeiten
* Initialisieren der Runtime, mit der die App ausgeführt wird

Das Blazor WebAssembly-Hostingmodell besitzt folgende Vorteile:

* Es besteht keine serverseitige .NET-Abhängigkeit. Nach dem Herunterladen auf den Client ist die App voll funktionsfähig.
* Die Ressourcen und Funktionen des Clients werden voll genutzt.
* Die Arbeit wird vom Server auf den Client verlagert.
* Es ist kein ASP.NET Core-Webserver zum Hosten der App erforderlich. Serverlose Bereitstellungsszenarios sind möglich (z. B. das Bereitstellen der App aus einem CDN).

Das Blazor WebAssembly-Hostingmodell besitzt folgende Nachteile:

* Die App ist auf die Funktionen des Browsers beschränkt.
* Es ist geeignete Clienthardware und -software erforderlich (z. B. WebAssembly-Unterstützung).
* Die Downloadmenge ist größer und die Ladezeit der Apps länger.
* Die Unterstützung für die .NET-Runtime und -Tools ist weniger ausgereift. Bei der Unterstützung und dem Debuggen in [.NET Standard](/dotnet/standard/net-standard) existieren beispielsweise Einschränkungen.

Das Blazor-Modell für gehostete Apps unterstützt [Docker-Container](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klicken Sie mit der rechten Maustaste auf das Serverprojekt in Visual Studio, und klicken Sie dann auf **Hinzufügen** > **Docker-Unterstützung**.

## Blazor Server

Mit dem Blazor Server-Hostingmodell wird die App über eine ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine [SignalR](xref:signalr/introduction)-Verbindung verarbeitet.

![Der Browser interagiert mit der (in einer ASP.NET Core-App gehosteten) App über eine SignalR-Verbindung auf dem Server.](hosting-models/_static/blazor-server.png)

Verwenden Sie zum Erstellen einer Blazor-App mithilfe des Blazor Server-Hostingmodells die ASP.NET Core- **Blazor Server-App**-Vorlage ([`dotnet new blazorserver`](/dotnet/core/tools/dotnet-new)). Die ASP.NET Core-App hostet die Blazor Server-App und erstellt den SignalR-Endpunkt für Clientverbindungen.

Die ASP.NET Core-App verweist auf die `Startup`-Klasse der App, um folgende Aktionen durchzuführen:

* Hinzufügen serverseitiger Dienste
* Hinzufügen der App zur Pipeline für die Anforderungsverarbeitung

Auf dem Client stellt das `blazor.server.js`-Skript die SignalR-Verbindung mit dem Server her. Das Skript wird in einer eingebetteten Ressource im freigegebenen ASP.NET Core-Framework für die clientseitige App bereitgestellt. Die clientseitige App ist ggf. für das Beibehalten und Wiederherstellen des App-Zustands verantwortlich. 

Das Blazor Server-Hostingmodell besitzt folgende Vorteile:

* Die Downloadgröße ist im Vergleich zu einer Blazor WebAssembly-App deutlich kleiner und die Ladezeit der App wesentlich kürzer.
* Die Serverfunktionen werden in vollem Umfang genutzt, einschließlich aller mit .NET Core kompatiblen APIs.
* Da auf dem Server .NET Core zum Ausführen der App verwendet wird, funktionieren vorhandene .NET-Tools, wie das Debuggen, erwartungsgemäß.
* Thin Clients werden unterstützt. So funktionieren Blazor Server-Apps auch mit Browsern, die WebAssembly nicht unterstützen, sowie auf Geräten mit eingeschränkten Ressourcen.
* Die .NET-/C#-Codebasis der App, einschließlich des Komponentencodes, werden nicht für Clients bereitgestellt.

> [!IMPORTANT]
> Als Reaktion auf die erste Clientanforderung führt die Blazor Server-App ein Prerendering aus, das den Zustand der Benutzeroberfläche auf dem Server festlegt. Versucht der Client, eine SignalR-Verbindung zu erstellen, **muss er mit demselben Server erneut eine Verbindung herstellen**. Blazor Server-Apps mit mehr als einem Back-End-Server sollten für SignalR-Verbindungen *persistente Sitzungen* implementieren. Weitere Informationen finden Sie im Abschnitt [Verbindung mit dem Server](#connection-to-the-server).

Das Blazor Server-Hostingmodell besitzt folgende Nachteile:

* In der Regel ist mit einer höheren Latenzzeit zu rechnen. Bei jeder Benutzerinteraktion fällt ein Netzwerkhop an.
* Es gibt keine Offlineunterstützung. Bei einer Unterbrechung der Clientverbindung funktioniert die App nicht mehr.
* Die Skalierbarkeit ist bei Apps mit vielen Benutzern schwierig. Der Server muss eine Vielzahl von Clientverbindungen verwalten und Clientzustände verarbeiten.
* Zum Bereitstellen der App ist ein ASP.NET Core-Server erforderlich. Serverlose Bereitstellungsszenarios sind nicht möglich (z. B. das Bereitstellen der App aus einem CDN).

Das Blazor Server-App-Modell unterstützt [Docker-Container](/dotnet/standard/microservices-architecture/container-docker-introduction/index). Klicken Sie mit der rechten Maustaste auf das Projekt in Visual Studio, und klicken Sie dann auf **Hinzufügen** > **Docker-Unterstützung**.

### <a name="comparison-to-server-rendered-ui"></a>Vergleich mit einer auf dem Server gerenderten Benutzeroberfläche

Die Unterschiede zwischen Blazor Server-Apps und traditionellen Modellen beim Rendern von Benutzeroberflächen in ASP.NET Core-Apps mit Razor-Ansichten oder Razor Pages zu kennen ist entscheidend. Bei beiden Modellen wird zum Beschreiben von HTML-Inhalt die Razor-Syntax verwendet. Deutliche Unterschiede bestehen hingegen im Rendern von Markup.

Beim Rendern einer Razor-Seite oder -Ansicht gibt jede Razor-Codezeile HTML in Textform aus. Nach dem Rendern verwirft der Server die Instanz der Seite oder Ansicht, einschließlich aller produzierten Zustände. Tritt eine weitere Anforderung für die Seite auf, wenn etwa bei der Serverüberprüfung ein Fehler auftritt und die Zusammenfassung der Überprüfung angezeigt wird, werden folgende Aktionen ausgeführt:

* Die gesamte Seite wird nochmals als HTML-Text gerendert.
* Die Seite wird an den Client gesendet.

Eine Blazor-App besteht aus wiederverwendbaren Benutzeroberflächenelementen, die *Komponenten* genannt werden. Eine Komponente enthält C#-Code, Markup und weitere Komponenten. Wird eine Komponente gerendert, erzeugt Blazor ein Diagramm der enthaltenen Komponenten, das einem HTML- oder XML-Dokumentobjektmodell (DOM) ähnelt. Das Diagramm beinhaltet den in Eigenschaften und Feldern gespeicherten Zustand der Komponenten. Blazor wertet das Komponentendiagramm aus und erstellt eine Binärdarstellung des Markups. Folgende Aktionen können im Binärformat ausgeführt werden:

* Umwandlung in HTML-Text (während des Prerenderings&dagger;)
* Verwendung für eine effiziente Aktualisierung des Markups während des regulären Renderings

&dagger;*Prerendering*: Die angeforderte Razor-Komponente wird auf dem Server in statisches HTML kompiliert und an den Client gesendet. Dort erfolgt das Rendering für den Benutzer. Nach dem Herstellen einer Verbindung zwischen Client und Server werden die vorab gerenderten statischen Elemente durch interaktive Elemente ersetzt. Durch Prerendering reagiert die App schneller auf den Benutzer.

Eine Aktualisierung der Benutzeroberfläche wird in Blazor ausgelöst durch:

* eine Benutzerinteraktion, wie etwa das Auswählen einer Schaltfläche.
* App-Trigger, wie etwa ein Timer.

Das Diagramm wird noch mal gerendert, und für die Benutzeroberfläche wird ein Unterschied *diff* berechnet. Dieser Unterschied stellt die kleinste Menge DOM-Änderungen dar, die zur Aktualisierung der Benutzeroberfläche auf dem Client erforderlich ist. Der Unterschied wird im Binärformat an den Client gesendet und vom Browser angewendet.

Eine Komponente wird verworfen, sobald sie der Benutzer auf dem Client wieder verlässt. Während der Interaktion mit einem Benutzer muss der Zustand der Komponente (Dienste, Ressourcen) im Arbeitsspeicher des Servers gespeichert werden. Da auf dem Server der Zustand vieler Komponenten möglicherweise gleichzeitig verwaltet werden muss, kann die Speicherausschöpfung zu einem Problem werden. Eine Anleitung zum Erstellen einer Blazor Server-App unter optimaler Verwendung des Arbeitsspeichers des Servers finden Sie unter <xref:blazor/security/server/threat-mitigation>.

### <a name="circuits"></a>Leitungen

Eine Blazor Server-App basiert auf [ASP.NET Core SignalR](xref:signalr/introduction). Jeder Client kommuniziert mit dem Server über mindestens eine SignalR-*Verbindung*. Eine solche Verbindung ist eine Blazor-Abstraktion über SignalR-Verbindungen, die temporäre Netzwerkunterbrechungen tolerieren können. Stellt ein Blazor-Client fest, dass die SignalR-Verbindung unterbrochen ist, versucht er über eine neue SignalR-Verbindung, noch mal eine Verbindung mit dem Server herzustellen.

Jedes Browserfenster (Registerkarte des Browsers oder iframe), das über eine Blazor Server-App verbunden ist, verwendet eine SignalR-Verbindung. Darin besteht ein weiterer wesentlicher Unterschied zu typischen, auf dem Server gerenderten Apps. Bei auf dem Server gerenderten Apps bedeutet das gleichzeitige Öffnen einer App in mehreren Browserfenstern in der Regel keine zusätzlichen Ressourcenanforderungen auf dem Server. In einer Blazor Server-App erfordert jedes Browserfenster eine separate Verbindung und separate Instanzen des Komponentenzustands, die vom Server verwaltet werden müssen.

In Blazor gilt das Schließen einer Browserregisterkarte oder das Navigieren zu einer externen URL als *ordnungsgemäße* Beendigung. Bei einer ordnungsgemäßen Beendigung werden die Verbindung und die zugehörigen Ressourcen sofort freigegeben. Die Verbindung zu einem Client kann auch nicht ordnungsgemäß getrennt werden, beispielsweise aufgrund einer Netzwerkunterbrechung. Blazor Server speichert getrennte Verbindungen für die Dauer eines konfigurierbaren Intervalls, damit der Client noch mal eine Verbindung herstellen kann.

In Blazor Server kann mithilfe von Code ein *Verbindungshandler* definiert werden, mit dem Code für Zustandsänderungen einer Benutzerverbindung ausgeführt werden kann. Weitere Informationen finden Sie unter <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.

### <a name="ui-latency"></a>Benutzeroberflächenlatenz

Benutzeroberflächenlatenz bezeichnet die Zeit vom Initiieren einer Aktion bis zur Aktualisierung der Benutzeroberfläche. Je kleiner der Wert für die Benutzeroberflächenlatenz, desto dynamischer fühlt sich eine App für den Benutzer an. Bei einer Blazor Server-App wird jede Aktion an den Server gesendet, verarbeitet und ein Unterschied der Benutzeroberfläche zurückgesendet. Die Benutzeroberflächenlatenz setzt sich folglich zusammen aus der Netzwerklatenz und der Serverlatenz beim Verarbeiten der Aktion.

Bei einer Branchen-App, die ausschließlich in einem privaten Unternehmensnetzwerk verwendet wird, ist der Anteil der Netzwerklatenz an der gesamten Latenzwahrnehmung durch den Benutzer in der Regel vernachlässigbar. Bei einer über das Internet bereitgestellten App kann die Latenz für Benutzer durchaus spürbar sein, insbesondere wenn diese geografisch weit verteilt sind.

Auch die Speicherauslastung kann zur Latenzzeit einer App beitragen. Eine erhöhte Speicherauslastung führt zu einer häufigen Speicherbereinigung oder einer Auslagerung auf die Festplatte. Beide Maßnahmen reduzieren die Leistung einer App und erhöhen folglich deren Benutzeroberflächenlatenz.

Sie sollten Blazor Server-Apps so optimieren, dass die Benutzeroberflächenlatenz durch Reduzieren der Netzwerklatenz und der Speicherauslastung minimiert wird. Informationen zum Messen der Netzwerklatenz finden Sie unter <xref:blazor/host-and-deploy/server#measure-network-latency>. Weitere Informationen zu SignalR und Blazor finden Sie unter:

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a>Verbindung mit dem Server

Blazor Server-Apps erfordern eine aktive SignalR-Verbindung mit dem Server. Wird die Verbindung unterbrochen, versucht die App, noch mal eine Verbindung mit dem Server herzustellen. Solange der Clientzustand im Arbeitsspeicher gespeichert ist, wird die Clientsitzung ohne Verlust des Zustands wiederhergestellt.

Als Reaktion auf die erste Clientanforderung führt die Blazor Server-App ein Prerendering aus, das den Zustand der Benutzeroberfläche auf dem Server festlegt. Versucht der Client, eine SignalR-Verbindung zu erstellen, muss er noch mal mit demselben Server eine Verbindung herstellen. Blazor Server-Apps mit mehr als einem Back-End-Server sollten für SignalR-Verbindungen *persistente Sitzungen* implementieren.

Sie sollten [Azure SignalR Service](/azure/azure-signalr) für Blazor Server-Apps verwenden. Der Dienst ermöglicht das Hochskalieren einer Blazor Server-App auf eine große Anzahl gleichzeitiger SignalR-Verbindungen. Bei Azure SignalR Service werden persistente Sitzungen aktiviert, indem die Option `ServerStickyMode` des Diensts oder dessen Konfigurationswert auf `Required` festgelegt wird. Weitere Informationen finden Sie unter <xref:blazor/host-and-deploy/server#signalr-configuration>.

Bei der Verwendung von IIS sind persistente Sitzungen mit Routing von Anwendungsanforderungen aktiviert. Weitere Informationen finden Sie unter [HTTP-Lastenausgleiche mithilfe von Routing von Anwendungsanforderungen](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
