---
title: Vergleich von gRPC-Diensten mit HTTP-APIs
author: jamesnk
description: Erfahren Sie, wie gRPC im Vergleich zu HTTP-APIs abschneidet und wie die empfohlenen Szenarien aussehen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
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
uid: grpc/comparison
ms.openlocfilehash: 1ec553d54a9cad170cb322bc186bb67ac8bbded4
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658728"
---
# <a name="compare-grpc-services-with-http-apis"></a>Vergleich von gRPC-Diensten mit HTTP-APIs

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Artikel wird der Vergleich von [gRPC-Diensten](https://grpc.io/docs/guides/) mit HTTP-APIs mit JSON (einschließlich ASP.NET Core-[Web-APIs](xref:web-api/index)) erläutert. Die Technologie, mit der eine API für Ihre App bereitgestellt wird, ist eine wichtige Wahl, und gRPC bietet im Vergleich zu HTTP-APIs besondere Vorteile. In diesem Artikel werden die Stärken und Schwächen von gRPC erläutert und Szenarien empfohlen, in denen gRPC gegenüber anderen Technologien bevorzugt zu verwenden ist.

## <a name="high-level-comparison"></a>Allgemeiner Vergleich

Die folgende Tabelle bietet einen allgemeinen Vergleich der Features von gRPC und HTTP-APIs mit JSON.

| Feature          | gRPC                                               | HTTP-APIs mit JSON           |
| ---------------- | -------------------------------------------------- | ----------------------------- |
| Vertrag         | Erforderlich ( *.proto*)                                | Optional (OpenAPI)            |
| Protokoll         | HTTP/2                                             | HTTP                          |
| Payload          | [Protobuf (klein, binär)](#performance)           | JSON (groß, für Menschen lesbar)  |
| Präskriptivität | [Strikte Spezifikation](#strict-specification)      | Locker. Jedes HTTP ist gültig.     |
| Streaming        | [Client, Server, bidirektional](#streaming)       | Client, Server                |
| Browserunterstützung  | [Nein (erfordert grpc-web)](#limited-browser-support) | Ja                           |
| Sicherheit         | Transport (TLS)                                    | Transport (TLS)               |
| Clientcodegenerierung | [Ja](#code-generation)                      | OpenAPI + Tools von Drittanbietern |

## <a name="grpc-strengths"></a>gRPC-Stärken

### <a name="performance"></a>Leistung

gRPC-Nachrichten werden mit [Protobuf](https://developers.google.com/protocol-buffers/docs/overview) serialisiert, einem effizienten binären Nachrichtenformat. Protobuf wird sehr schnell auf dem Server und dem Client serialisiert. Die Protobuf-Serialisierung führt zu kleinen Nachrichtennutzlasten, was in Szenarien mit begrenzter Bandbreite wichtig ist, z. B. bei mobilen Apps.

gRPC wurde für HTTP/2 konzipiert, eine umfassende Überarbeitung von HTTP, die erhebliche Leistungsvorteile gegenüber HTTP 1.x bietet:

* Binäre Rahmen und Komprimierung. Das HTTP/2-Protokoll ist sowohl beim Senden als auch beim Empfangen kompakt und effizient.
* Multiplexing mehrerer HTTP/2-Aufrufe über eine einzelne TCP-Verbindung. Durch Multiplexing werden [Head-of-Line-Blockierungen](https://en.wikipedia.org/wiki/Head-of-line_blocking) beseitigt.

HTTP/2 ist nicht exklusiv für GrpC. Viele Anforderungstypen, einschließlich HTTP-APIs mit JSON, können HTTP/2 verwenden und von dessen Leistungsverbesserungen profitieren.

### <a name="code-generation"></a>Codeerzeugung

Alle gRPC-Frameworks bieten erstklassige Unterstützung für die Codegenerierung. Eine grundlegende Datei für die gRPC-Entwicklung ist die [`.proto`-Datei](https://developers.google.com/protocol-buffers/docs/proto3), die den Vertrag der gRPC-Dienste und -Nachrichten definiert. Aus dieser Datei generieren die gRPC-Frameworks eine Dienstbasisklasse, Nachrichten und einen vollständigen Client.

Durch die Freigabe der *.proto*-Datei zwischen dem Server und dem Client können Nachrichten und Clientcode von Ende zu Ende generiert werden. Die Codegenerierung des Clients verhindert die Duplizierung von Nachrichten auf dem Client und dem Server und erstellt einen stark typisierten Client für Sie. Wenn Sie keinen Client schreiben müssen, sparen Sie bei Anwendungen mit vielen Diensten erheblich an Entwicklungszeit.

### <a name="strict-specification"></a>Strikte Spezifikation

Eine formale Spezifikation für die HTTP-API mit JSON existiert nicht. Entwickler diskutieren über das beste Format für URLs, HTTP-Verben und Antwortcodes.

Die [gRPC-Spezifikation](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-HTTP2.md) gibt vor, welchem Format ein gRPC-Dienst folgen muss. gRPC macht Diskussionen überflüssig und spart Entwicklern Zeit, da gRPC über Plattformen und Implementierungen hinweg konsistent ist.

### <a name="streaming"></a>Streaming

HTTP/2 bietet eine Grundlage für langlebige Echtzeitkommunikationsdatenströme. gRPC bietet erstklassige Unterstützung für das Streaming über HTTP/2.

Ein gRPC-Dienst unterstützt alle Streamingkombinationen:

* Unär (kein Streaming)
* Streaming vom Server zum Client
* Streaming vom Client zum Server
* Bidirektionales Streaming

### <a name="deadlinetimeouts-and-cancellation"></a>Stichtag/Zeitlimits und Abbruch

Mit gRPC können Clients angeben, wie lange sie bereit sind, auf den RPC-Abschluss zu warten. Der [Stichtag](https://grpc.io/blog/deadlines) wird an den Server gesendet, und der Server kann entscheiden, welche Aktion bei einer Überschreitung der Frist durchgeführt werden soll. Der Server kann z. B. in Bearbeitung befindliche gRPC-/HTTP-/Datenbankanforderungen bei einem Timeout abbrechen.

Die Weitergabe des Stichtags und des Abbruchs durch untergeordnete gRPC-Aufrufe hilft bei der Durchsetzung der Ressourcennutzungslimits.

## <a name="grpc-recommended-scenarios"></a>Für gRPC empfohlene Szenarien

gRPC ist für die folgenden Szenarien besonders geeignet:

* **Microservices:** gRPC ist für kurze Wartezeiten und Kommunikation mit hohem Durchsatz konzipiert. gRPC eignet sich hervorragend für einfache Microservices, bei denen die Effizienz entscheidend ist.
* **Punkt-zu-Punkt-Echtzeitkommunikation:** gRPC weist eine ausgezeichnete Unterstützung für bidirektionales Streaming auf. gRPC-Dienste können Nachrichten in Echtzeit und ohne Abrufen bereitstellen.
* **Mehrsprachige Umgebungen:** gRPC-Tools unterstützen alle gängigen Entwicklungssprachen, was gRPC zu einer guten Wahl für mehrsprachige Umgebungen macht.
* **Umgebungen mit Netzwerkbeschränkungen:** gRPC-Nachrichten werden mit Protobuf serialisiert, einem einfachen Nachrichtenformat. Eine gRPC-Nachricht ist immer kleiner als eine entsprechende JSON-Nachricht.
* **Inter-Process Communication (IPC)** : IPC-Transporte (z. B. UNIX-Domänensockets und Named Pipes) können mit gRPC für die Kommunikation zwischen Apps auf demselben Computer verwendet werden. Weitere Informationen finden Sie unter <xref:grpc/interprocess>.

## <a name="grpc-weaknesses"></a>Schwächen von gRPC

### <a name="limited-browser-support"></a>Eingeschränkte Browserunterstützung

Es ist heute unmöglich, einen gRPC-Dienst direkt von einem Browser aus aufzurufen. gRPC nutzt in hohem Maße HTTP/2-Features, und kein Browser bietet das für die Unterstützung eines gRPC-Clients erforderliche Maß an Kontrolle über Webanforderungen. Browser gestatten es einem Aufrufer z. B. nicht, die Verwendung von HTTP/2 zu erfordern oder den Zugriff auf zugrunde liegende HTTP/2-Frames zu ermöglichen.

Es gibt zwei gängige Ansätze, um gRPC in Browser-Apps zu integrieren:

* [gRPC-Web](https://grpc.io/docs/tutorials/basic/web.html) ist eine zusätzliche Technologie vom gRPC-Team, die gRPC-Unterstützung im Browser bietet. Mit gRPC-Web können Browser-Apps von der Hochleistung und geringen Netzwerkauslastung von gRPC profitieren. Nicht alle Features von gRPC werden von gRPC-Web unterstützt. Client- und bidirektionales Streaming wird nicht unterstützt, und es gibt nur begrenzte Unterstützung für Serverstreaming.

  .NET Core unterstützt gRPC-Web. Weitere Informationen finden Sie unter <xref:grpc/browser>.

* REST-JSON-Web-APIs können automatisch aus gRPC-Diensten erstellt werden, indem die *PROTO*-Datei mit [HTTP-Metadaten](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) kommentiert wird. Dadurch kann eine App sowohl gRPC- als auch JSON-Web-APIs unterstützen, ohne den Aufwand für die Entwicklung separater Dienste für beides zu verdoppeln.

  .NET Core bietet experimentelle Unterstützung für das Erstellen von JSON-Web-APIs aus gRPC-Diensten. Weitere Informationen finden Sie unter <xref:grpc/httpapi>.

### <a name="not-human-readable"></a>Nicht für Menschen lesbar

HTTP-API-Anforderungen werden als Text gesendet und können von Menschen gelesen und erstellt werden.

gRPC-Nachrichten werden standardmäßig mit Protobuf codiert. Obwohl Protobuf effizient zu senden und zu empfangen ist, ist sein binäres Format nicht für Menschen lesbar. Protobuf benötigt die in der *.proto*-Datei angegebene Schnittstellenbeschreibung der Nachricht, um sie ordnungsgemäß zu deserialisieren. Zusätzliche Tools sind erforderlich, um die Protobuf-Nutzlasten im Netzwerk zu analysieren und Anforderungen manuell zusammenzustellen.

Es gibt Features wie [Serverreflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) und das [gRPC-Befehlszeilentool](https://github.com/grpc/grpc/blob/master/doc/command_line_tool.md), die bei binären Protobuf-Nachrichten helfen. Außerdem unterstützen Protobuf-Nachrichten die [Konvertierung zu und von JSON](https://developers.google.com/protocol-buffers/docs/proto3#json). Die integrierte JSON-Konvertierung bietet eine effiziente Möglichkeit, Protobuf-Nachrichten beim Debuggen in und aus der für Menschen lesbaren Form zu konvertieren.

## <a name="alternative-framework-scenarios"></a>Alternative Frameworkszenarien

In den folgenden Szenarien werden andere Frameworks als gRPC empfohlen:

* **Über Browser zugängliche APIs:** gRPC wird im Browser nicht vollständig unterstützt. gRPC-Web kann eine Browserunterstützung bieten, hat aber Einschränkungen und führt einen Serverproxy ein.
* **Übertragung von Echtzeitkommunikation:** gRPC unterstützt Echtzeitkommunikation per Streaming, das Konzept der Übertragung einer Nachricht an registrierte Verbindungen existiert allerdings nicht. In einem Chatraum-Szenario, in dem z. B. neue Chatnachrichten an alle Clients im Chatraum gesendet werden sollen, ist jeder gRPC-Aufruf erforderlich, um neue Chatnachrichten einzeln an den Client zu senden. [SignalR](xref:signalr/introduction) ist ein hilfreiches Framework für dieses Szenario. SignalR weist das Konzept der dauerhaften Verbindungen und der integrierten Unterstützung für das Senden von Nachrichten auf.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
