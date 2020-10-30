---
title: Einführung in ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie, wie die ASP.net Core SignalR Bibliothek das Hinzufügen von Echtzeitfunktionen zu apps vereinfacht.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/27/2019
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
uid: signalr/introduction
ms.openlocfilehash: 1810fef903362addcef4a6c9ec53264604f58d2b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051472"
---
# <a name="introduction-to-aspnet-core-no-locsignalr"></a>Einführung in ASP.NET Core SignalR

## <a name="what-is-no-locsignalr"></a>Was ist SignalR?

ASP.net Core SignalR ist eine Open-Source-Bibliothek, die das Hinzufügen von Echt Zeit Webfunktionen zu apps vereinfacht. Die Echt Zeit webolle ermöglicht es dem serverseitigen Code, Inhalte sofort per Push an Clients zu übersetzen.

Gute Kandidaten für SignalR :

* Apps, für die eine hohe Frequenz von Updates vom Server benötigt wird. Beispiele hierfür sind Apps aus den Bereichen Gaming, soziale Netzwerke, Voting, Auktionen, Karten und GPS.
* Dashboards und Überwachungs-Apps. Beispiele hierfür sind Unternehmensdashboards, Sofortupdates von Verkaufszahlen oder Reisehinweise.
* Apps für die Zusammenarbeit. Whiteboard-Apps und Software für Teambesprechungen sind Beispiele für Apps für die Zusammenarbeit.
* Apps, für die Benachrichtigungen benötigt werden. Soziale Netzwerke, E-Mail, Chat, Games, Reisehinweise und viele andere Arten von Apps nutzen Benachrichtigungen.

SignalR stellt eine API zum Erstellen von [Remote Prozedur aufrufen (RPC)](https://wikipedia.org/wiki/Remote_procedure_call)für Server-zu-Client bereit. Die RPCs wenden JavaScript-Funktionen auf Clients aus dem serverseitigen .net Core-Code an.

Im folgenden sind einige Features von SignalR für ASP.net Core aufgeführt:

* Behandelt die Verbindungs Verwaltung automatisch.
* Sendet Nachrichten gleichzeitig an alle verbundenen Clients. Beispielsweise ein Chatraum.
* Sendet Nachrichten an bestimmte Clients oder Client Gruppen.
* Skaliert, um zunehmenden Datenverkehr zu verarbeiten.

Die Quelle wird in einem [ SignalR Repository auf GitHub](https://github.com/dotnet/AspNetCore/tree/master/src/SignalR)gehostet.

## <a name="transports"></a>Transportprotokolle

SignalR unterstützt die folgenden Verfahren für die Verarbeitung der Echtzeitkommunikation (in der Reihenfolge des ordnungsgemäßen Fallbacks):

* [WebSockets](https://tools.ietf.org/html/rfc7118)
* Ereignisse Server-Sent
* Langer Abruf

SignalR wählt automatisch die beste Transportmethode aus, die sich innerhalb der Funktionen des Servers und des Clients befindet.

## <a name="hubs"></a>Hubs

SignalR verwendet *Hubs* für die Kommunikation zwischen Clients und Servern.

Ein Hub ist eine Pipeline auf hoher Ebene, die es einem Client und einem Server ermöglicht, Methoden untereinander aufzurufen. SignalR verarbeitet die Verteilung über die Computer Grenzen hinweg automatisch, sodass Clients Methoden auf dem Server aufrufen können und umgekehrt. Sie können stark typisierte Parameter an Methoden übergeben, die die Modell Bindung ermöglichen. SignalR stellt zwei integrierte Hub-Protokolle bereit: ein auf JSON basierendes Text Protokoll und ein auf [messagepack](https://msgpack.org/)basierendes binäres Protokoll.  Im Allgemeinen erstellt messagepack kleinere Nachrichten im Vergleich zu JSON. Ältere Browser müssen [XHR Level 2](https://caniuse.com/#feat=xhr2) unterstützen, um Unterstützung für das messagepack-Protokoll bereitzustellen.

Hubs wenden Client seitigen Code an, indem Sie Nachrichten senden, die den Namen und die Parameter der Client seitigen Methode enthalten. Objekte, die als Methoden Parameter gesendet werden, werden mithilfe des konfigurierten Protokolls deserialisiert. Der Client versucht, den Namen einer Methode im Client seitigen Code zuzuordnen. Wenn der Client eine Entsprechung findet, ruft er die-Methode auf und übergibt die deserialisierten Parameterdaten an ihn.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Beginnen Sie mit der SignalR ASP.net Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)
