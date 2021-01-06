---
title: Sicherheitsaspekte in gRPC für ASP.NET Core
author: jamesnk
description: Informationen zu Sicherheitsaspekten für gRPC für ASP.NET Core
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
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
uid: grpc/security
ms.openlocfilehash: a7a595a71f988377bf25c500f04da2add3d85aef
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93058830"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Sicherheitsaspekte in gRPC für ASP.NET Core

Von [James Newton-King](https://twitter.com/jamesnk)

Dieser Artikel enthält Informationen zum Sichern von gRPC mit .NET Core

## <a name="transport-security"></a>Transportsicherheit

GRPC-Nachrichten werden über HTTP/2 gesendet und empfangen. Es wird Folgendes empfohlen:

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) wird verwendet, um Nachrichten in gRPC-Produktions-Apps zu sichern.
* GRPC-Dienste dürfen nur über gesicherte Ports lauschen und reagieren.

TLS ist in Kestrel konfiguriert. Weitere Informationen zur Kestrel-Endpunktkonfiguration finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Ausnahmen

Ausnahmemeldungen werden generell als vertrauliche Daten angesehen, die nicht für einen Client offengelegt werden dürfen. Standardmäßig sendet gRPC keine Details einer von einem gRPC-Dienst ausgelösten Ausnahme an den Client. Stattdessen erhält der Client eine generische Nachricht, die angibt, dass ein Fehler aufgetreten ist. Ausnahmemeldungen, die an den Client übermittelt werden, können mithilfe von [EnableDetailedErrors](xref:grpc/configuration#configure-services-options) überschrieben werden (zum Beispiel während der Entwicklung oder eines Tests). Ausnahmemeldungen dürfen dem Client in Produktions-Apps nicht offengelegt werden.

## <a name="message-size-limits"></a>Beschränkungen der Nachrichtengröße

Eingehende Nachrichten an gRPC-Clients und -Dienste werden in den Speicher geladen. Die Größenbeschränkung für Nachrichten sind Mechanismen, um zu verhindern, dass gRPC übermäßig viele Ressourcen beansprucht.

GRPC verwendet Größenbeschränkungen pro Nachricht, um eingehende und ausgehende Nachrichten zu verwalten. Standardmäßig beschränkt gRPC die eingehenden Nachrichten auf 4 MB. Für ausgehende Nachrichten gibt es keine Beschränkung.

Auf dem Server können gRPC-Nachrichtengrenzwerte für alle Dienste in einer App mit `AddGrpc` konfiguriert werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 1 * 1024 * 1024; // 1 MB
        options.MaxSendMessageSize = 1 * 1024 * 1024; // 1 MB
    });
}
```

Beschränkungen können auch für einen einzelnen Dienst mit `AddServiceOptions<TService>` konfiguriert werden. Weitere Informationen zur Konfiguration von Beschränkungen der Nachrichtengröße finden Sie unter [gRPC-Konfiguration](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Clientzertifikatüberprüfung

[Clientzertifikate](https://tools.ietf.org/html/rfc5246#section-7.4.4) werden zunächst überprüft, wenn die Verbindung hergestellt wird. Standardmäßig führt Kestrel keine zusätzliche Überprüfung des Clientzertifikats einer Verbindung durch.

Es wird empfohlen, dass gRPC-Dienste von Clientzertifikaten über das Paket [Microsoft.AspNetCore.Authentication.Certificate](xref:security/authentication/certauth) gesichert werden. Die Authentifizierung der ASP.NET Core-Zertifizierung führt zusätzliche Überprüfungen auf einem Clientzertifikat aus, einschließlich der folgenden:

* Zertifikat verfügt über gültige erweiterte Schlüsselverwendung (EKU)
* Befindet sich innerhalb des Gültigkeitszeitraums
* Zertifikatswiderruf wird überprüft
