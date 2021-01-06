---
title: Verwenden von ASP.NET Core mit HTTP/2 in IIS
author: rick-anderson
description: Hier erfahren Sie, wie Sie HTTP/2-Features mit IIS verwenden.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/protocols
ms.openlocfilehash: 4d0dc1239467e92c4127f631709c2ffd6098bfc5
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057413"
---
# <a name="use-aspnet-core-with-http2-on-iis"></a>Verwenden von ASP.NET Core mit HTTP/2 in IIS

Von [Justin Kotalik](https://github.com/jkotalik)

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden IIS-Bereitstellungsszenarien unterstützt:

* Windows Server 2016 oder höher/Windows 10 oder höher
* IIS 10 oder höher
* TLS 1.2-Verbindung oder höher
* Beim [Out-of-Process-Hosting](xref:host-and-deploy/iis/index#out-of-process-hosting-model): Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.

Für eine In-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/2`. Für eine Out-of-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [`HttpRequest.Protocol`](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) den Wert `HTTP/1.1`.

Weitere Informationen zu den In-Process- und Out-of-Process-Hostingmodellen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

HTTP/2 ist standardmäßig für HTTPS-/TLS-Verbindungen aktiviert. Verbindungen führen ein Fallback auf HTTP/1.1 aus, wenn keine HTTP/2-Verbindung hergestellt wurde. Weitere Informationen zur HTTP/2-Konfiguration mit IIS-Bereitstellungen finden Sie unter [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="advanced-http2-features-to-support-grpc"></a>Erweiterte HTTP/2-Features zur Unterstützung von gRPC

Zusätzliche HTTP/2-Features in IIS zur Unterstützung von gRPC, einschließlich der Unterstützung für Antwortnachspanne und das Senden von Frames zum Zurücksetzen

Anforderungen zum Ausführen von gRPC in IIS:

* In-Process-Hosting
* Windows 10, OS Build 20300.1000 oder höher. Erfordert möglicherweise die Verwendung von Windows Insider-Builds
* TLS 1.2-Verbindung oder höher

### <a name="trailers"></a>Trailer

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a>Reset

[!INCLUDE[](~/includes/reset.md)]
