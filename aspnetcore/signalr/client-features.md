---
title: ASP.net Core SignalR Clients
author: bradygaster
description: Erfahren Sie, welche Funktionen von den verschiedenen ASP.net Core Clients unterstützt werden SignalR .
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
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
uid: signalr/client-features
ms.openlocfilehash: bb8352b11e78582c4a32b67da7dcb701d020b0d9
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2020
ms.locfileid: "90721709"
---
# <a name="aspnet-core-no-locsignalr-clients"></a>ASP.net Core SignalR Clients

## <a name="versioning-support-and-compatibility"></a>Versionsverwaltung, Unterstützung und Kompatibilität

Die SignalR Clients werden zusammen mit den Serverkomponenten ausgeliefert und sind mit einer Versions Angabe versehen. Jeder unterstützte Client kann sicher eine Verbindung mit jedem unterstützten Server herstellen, und alle Kompatibilitätsprobleme werden als Fehler behandelt, die korrigiert werden müssen. SignalR Clients werden in demselben Support Lebenszyklus wie der Rest von .net Core unterstützt. Weitere Informationen finden Sie in [der .net Core-Unterstützungs Richtlinie](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .

Viele Features erfordern einen kompatiblen Client **und** Server. Im folgenden finden Sie eine Tabelle, in der die Mindestversionen für verschiedene Features angezeigt werden.

Die 1. x-Versionen von entsprechen SignalR den .net Core-Versionen 2,1 und 2,2 und weisen die gleiche Lebensdauer auf. Bei Version 3. x und höher entspricht die SignalR Version exakt dem Rest von .net und hat denselben Support Lebenszyklus.

| SignalR-Version | .NET Core-Version | Supportebene | Ende des Supports |
| - | - | - | - |
| 1.0. x | 2.1.x | Langfristige Unterstützung | 21. August 2021 |
| 1.1. x | 2.2. x | Ende der Lebensdauer | 23. Dezember 2019 |
| 3. x oder höher | *identisch mit SignalR Version* | Weitere Informationen finden Sie in der [.net Core-Unterstützungs Richtlinie](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) . |

**Hinweis:** In ASP.net Core 3,0 wurde der JavaScript- *moved* Client in das `@microsoft/signalr` NPM-Paket verschoben.

## <a name="feature-distribution"></a>Funktionsverteilung

In der folgenden Tabelle sind die Features und die Unterstützung für Clients aufgeführt, die Echtzeitunterstützung anbieten. Für jede Funktion wird die *Mindestversion* aufgelistet, die dieses Feature unterstützt. Wenn keine Version aufgeführt ist, wird die Funktion nicht unterstützt.

| Funktion | Server | .NET-Client | JavaScript-Client | Java-Client |
| ---- | :-: | :-: | :-: | :-: |
| Azure- SignalR Dienst Unterstützung |2.1.0|1.0.0|1.0.0|1.0.0|
| [Streaming zwischen Server und Client](xref:signalr/streaming)          |2.1.0|1.0.0|1.0.0|1.0.0|
| [Client-zu-Server-Streaming](xref:signalr/streaming)          |3.0.0|3.0.0|3.0.0|3.0.0|
| Automatische erneute Verbindung ([.net](./dotnet-client.md?tabs=visual-studio&view=aspnetcore-3.0#handle-lost-connection), [JavaScript](./javascript-client.md?view=aspnetcore-3.0#reconnect-clients))          |3.0.0|3.0.0|3.0.0|❌|
| Websockets-Transport |2.1.0|1.0.0|1.0.0|1.0.0|
| Transport von Server gesendeten Ereignissen |2.1.0|1.0.0|1.0.0|❌|
| Langer Abruf Transport |2.1.0|1.0.0|1.0.0|3.0.0|
| JSON Hub-Protokoll |2.1.0|1.0.0|1.0.0|1.0.0|
| Hub-Protokoll „MessagePack“ |2.1.0|1.0.0|1.0.0|❌|

Die Unterstützung für die Aktivierung zusätzlicher Client Features wird in [unserer Problem](https://github.com/dotnet/AspNetCore/issues)Verfolgung nachverfolgt.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Beginnen Sie mit der SignalR ASP.net Core](xref:tutorials/signalr)
* [Unterstützte Plattformen](xref:signalr/supported-platforms)
* [Hubs](xref:signalr/hubs)
* [JavaScript-Client](xref:signalr/javascript-client)