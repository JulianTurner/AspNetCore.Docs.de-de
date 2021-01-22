---
title: Unterstützte Plattformen für ASP.NET Core SignalR
author: bradygaster
description: Erfahren Sie mehr über die unterstützten Plattformen für ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
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
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689226"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a>Unterstützte Plattformen für ASP.NET Core SignalR

## <a name="server-system-requirements"></a>Systemanforderungen an Server

SignalR für ASP.net Core unterstützt jede Server Plattform, die von ASP.net Core unterstützt wird.

## <a name="javascript-client"></a>JavaScript-Client

Der [JavaScript-Client](xref:signalr/javascript-client) wird auf nodejs 8 und höheren Versionen und den folgenden Browsern ausgeführt:

| Browser                          | Version         |
| -------------------------------- | --------------- |
| Apple Safari, einschließlich iOS      | Aktuell&dagger; |
| Google Chrome, einschließlich Android | Aktuell&dagger; |
| Microsoft Edge                   | Aktuell&dagger; |
| Mozilla Firefox                  | Aktuell&dagger; |

&dagger;*Aktuell* bezieht sich auf die neueste Version des Browsers.

Internet Explorer und andere ältere Browser werden vom JavaScript-Client nicht unterstützt. Der Client hat möglicherweise ein unerwartetes Verhalten und Fehler in nicht unterstützten Browsern.

## <a name="net-client"></a>.NET-Client

Der [.NET-Client](xref:signalr/dotnet-client) wird auf jeder von ASP.net Core unterstützten Plattform ausgeführt. [Xamarin- SignalR Entwickler können](https://github.com/aspnet/Announcements/issues/305) z. b. zum Entwickeln von Android-Apps mit xamarin. Android 8.4.0.1 und höher und IOS-Apps verwenden, die xamarin. IOS 11.14.0.4 und höher verwenden.

Wenn der Server IIS ausführt, erfordert der websockets-Transport IIS 8,0 oder höher unter Windows Server 2012 oder höher. Andere Transporte werden auf allen Plattformen unterstützt.

## <a name="java-client"></a>Java-Client

Der [Java-Client](xref:signalr/java-client) unterstützt Java 8 und höhere Versionen.

## <a name="unsupported-clients"></a>Nicht unterstützte Clients

Die folgenden Clients sind verfügbar, aber experimentell oder inoffiziell. Sie werden derzeit nicht unterstützt und sind möglicherweise nie.

* [C++-Client](https://github.com/aspnet/SignalR-Client-Cpp)

* [SWIFT-Client](https://github.com/moozzyk/SignalR-Client-Swift)
