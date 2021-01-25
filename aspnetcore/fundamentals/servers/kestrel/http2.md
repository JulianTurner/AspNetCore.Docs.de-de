---
title: Verwenden von HTTP/2 mit dem ASP.NET Core-Kestrel-Webserver
author: rick-anderson
description: In diesem Artikel erhalten Sie Informationen zur Verwendung von HTTP/2 mit Kestrel, dem plattformübergreifenden Webserver für ASP.NET Core.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/http2
ms.openlocfilehash: 431459bb6ece1d054146558ef865e44845a22686
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253847"
---
# <a name="use-http2-with-the-aspnet-core-kestrel-web-server"></a>Verwenden von HTTP/2 mit dem ASP.NET Core-Kestrel-Webserver

[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps verfügbar, wenn die folgenden Basisanforderungen erfüllt sind:

* Betriebssystem&dagger;
  * Windows Server 2016/Windows 10 oder höher&Dagger;
  * Linux mit OpenSSL 1.0.2 oder höher (z.B. Ubuntu 16.04 oder höher)
* Zielframework: .NET Core 2.2 oder höher
* [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)
* TLS 1.2-Verbindung oder höher

&dagger;HTTP/2 wird unter macOS in einem zukünftigen Release unterstützt.
&Dagger;Kestrel bietet eingeschränkte Unterstützung für HTTP/2 unter Windows Server 2012 R2 und Windows 8.1. Die Unterstützung ist eingeschränkt, weil die Liste der unterstützten TLS-Verschlüsselungssammlungen unter diesen Betriebssystemen begrenzt ist. Zum Sichern von TLS-Verbindungen ist möglicherweise ein durch einen Elliptic Curve Digital Signature Algorithm (ECDSA) generiertes Zertifikat erforderlich.

Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A)`HTTP/2`.

Ab .NET Core 3.0 ist HTTP/2 standardmäßig aktiviert. Weitere Informationen zur Konfiguration finden Sie in den Abschnitten [HTTP/2-Einschränkungen](xref:fundamentals/servers/kestrel/options#http2-limits) und [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols).

## <a name="advanced-http2-features"></a>Erweiterte HTTP/2-Features

Hierbei handelt es sich um weitere HTTP/2-Features in Kestrel zur Unterstützung von gRPC, einschließlich der Unterstützung für Antwortnachspanne und das Senden von Frames zum Zurücksetzen.

### <a name="trailers"></a>Trailer

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
