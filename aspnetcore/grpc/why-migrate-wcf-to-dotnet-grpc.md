---
title: Gründe für die Migration von WCF zu ASP.NET Core gRPC
author: markrendle
description: Dieser Artikel enthält eine Zusammenfassung der Gründe, warum ASP.NET Core gRPC für WCF-Entwickler (Windows Communication Foundation) geeignet ist, die zu modernen Architekturen und Plattformen migrieren möchten.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058687"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a>gRPC für WCF-Entwickler (Windows Communication Foundation)

Dieser Artikel enthält eine Zusammenfassung der Gründe, warum ASP.NET Core gRPC für WCF-Entwickler (Windows Communication Foundation) geeignet ist, die zu modernen Architekturen und Plattformen migrieren möchten.

## <a name="comparison-to-wcf"></a>Vergleich mit WCF

Implementierung und Ansatz für gRPC sind zwar unterschiedlich, die Entwicklung und Nutzung von Diensten mit gRPC sollte für WCF-Entwickler jedoch intuitiv sein. Bei WCF und gRPC handelt es sich um RPC-Frameworks (Remote Procedure Call, Remoteprozeduraufruf) mit denselben Zielen:

* Ermöglichen einer Codierung als würden sich Client und Server auf derselben Plattform befinden
* Bereitstellen einer vereinfachten portierbaren Netzwerk-API

Beide Plattformen haben die Anforderung, eine Schnittstelle zu deklarieren und zu implementieren, wobei jedoch der Prozess zum Deklarieren der Schnittstelle unterschiedlich ist. Die zahlreichen Arten von RPC-Aufrufen, die von gRPC unterstützt werden, entsprechen den für WCF-Dienste verfügbaren Bindungen. Weitere Informationen und Beispiele finden Sie unter [Migrieren einer WCF-Lösung zu gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).

## <a name="benefits-of-grpc"></a>Vorteile von gRPC

gRPC bietet aus folgenden Gründen ein besseres Framework als andere Ansätze.

### <a name="performance"></a>Leistung

gRPC verwendet HTTP/2. Im Vergleich zu HTTP/1.1:

* Ist HTTP/2 ein kleineres, schnelleres binäres Protokoll.
* Lässt sich HTTP/2 von Computern effizienter analysieren.
* Unterstützt HTTP/2 das Multiplexing von Anforderungen über eine einzelne Verbindung. Dank Multiplexing können mehrere Anforderungen über eine Verbindung gesendet werden, ohne dass Anforderungen gegenseitig blockiert werden. Bei HTTP/1.1 wird die Blockierung als HOL-Blockierung (Head-of-Line) bezeichnet.

gRPC verwendet Protobuf. Hierbei handelt es sich um ein effizientes Binärformat zum Serialisieren von Meldungen. Protobuf-Meldungen:
* Lassen sich schnell Serialisieren und Deserialisieren.
* Verwenden weniger Bandbreite als textbasierte Formate. 

gRPC ist eine gute Lösung für mobile Geräte und Netzwerke mit Bandbreiteneinschränkungen.

### <a name="interoperability"></a>Interoperabilität

gRPC-Tools und -Bibliotheken gibt es für alle wichtigen Programmiersprachen und Plattformen wie .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby und PHP. Dank dem binären Übertragungsformat Protobuf und der effizienten Codegenerierung für die einzelnen Plattformen können Entwickler plattformübergreifende, leistungsfähige Apps erstellen.

### <a name="usability-and-productivity"></a>Nutzbarkeit und Produktivität

gRPC ist eine umfassende RPC-Lösung. Sie funktioniert konsistent über mehrere Sprachen und Plattformen hinweg. Zudem bietet sie auch hervorragende Tools, bei denen ein Großteil der Codebausteine automatisch generiert wird. Wie bei WCF werden bei gRPC Meldungen und ein stark typisierter Client automatisch generiert. Somit bleibt Entwicklern mehr Zeit, sich auf die Geschäftslogik zu konzentrieren.

### <a name="streaming"></a>Streaming

gRPC arbeitet mit einem rein bidirektionalen Streaming, das den Vollduplex-Diensten von WCF ähnliche Funktionen bietet. gRPC-Streaming kann über reguläre Internetverbindungen, Lastenausgleichsmodule und Dienstnetze betrieben werden.

### <a name="deadlines-timeouts-and-cancellation"></a>Fristen, Zeitlimits und Abbruch

Mit gRPC können Clients eine maximale Zeit für die Beendigung eines RPC angeben. Wenn die angegebene Frist überschritten wird, kann der Server den Vorgang unabhängig vom Client abbrechen. Fristen und Abbrüche können durch nachfolgende gRPC-Aufrufe weitergegeben werden, um für Ressourcen Nutzungslimits zu erzwingen. Clients können Vorgänge beenden, wenn eine Frist überschritten wird, oder ggf. auch früher. So können Clients beispielsweise Vorgänge aufgrund einer Benutzerinteraktion abbrechen.

### <a name="security"></a>Sicherheit

gRPC kann TLS und HTTP/2 verwenden, um eine verschlüsselte End-to-End-Verbindung zwischen dem Client und dem Server bereitzustellen. Durch die Unterstützung der Clientzertifikatsauthentifizierung wird die Sicherheit und Vertrauensstellung zwischen Client und Server verbessert.

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a>gRPC als Migrationspfad für WCF zu .NET Core und .NET 5

.NET Core und .NET 5 kennzeichnen insofern einen Wechsel, als dass Microsoft Remote-Kommunikationslösungen für Entwickler bereitstellt, die Dienste auf verschiedenen Plattformen bereitstellen möchten. .NET Core und .NET 5 unterstützen das [Aufrufen von WCF-Diensten](/dotnet/core/additional-tools/wcf-web-service-reference-guide), bieten aber keine serverseitige Unterstützung für das Hosting von WCF.

Es gibt zwei empfohlene Pfade für die Modernisierung von WCF-Apps:

* gRPC basiert auf modernen Technologien und wird in der Entwicklercommunity für RPC-Apps bevorzugt verwendet. Ab .NET Core 3.0 verfügen moderne .NET-Plattformen über eine ausgezeichnete Unterstützung für gRPC. Wenn Sie WCF-Dienste migrieren, sodass gRPC verwendet werden kann, können Sie die RPC-Features, die Leistung und die Interoperabilität bereitstellen, die in modernen Apps erforderlich sind.

* [CoreWCF](https://github.com/CoreWCF/CoreWCF) ist eine Communityinitiative, mit der .NET Core und .NET 5 die Unterstützung zum Hosten von WCF-Diensten erhalten sollen. Ein Vorschauversion ist verfügbar, und das Projekt ist in Kürze für die Produktion bereit. CoreWCF unterstützt nur einen Teil der Features von WCF, und bei .NET Framework-Apps, die zur Verwendung dieser Features migriert werden, muss der Code geändert und getestet werden. CoreWCF ist eine gute Wahl, wenn eine App die Kompatibilität mit vorhandenen Clients wahren muss, die WCF-Dienste aufrufen.

## <a name="get-started"></a>Erste Schritte

Eine ausführliche Anleitung zum Erstellen von gRPC-Diensten in ASP.NET Core für WCF-Entwickler finden Sie unter [ASP.NET Core gRPC für WCF-Entwickler](/dotnet/architecture/grpc-for-wcf-developers).
