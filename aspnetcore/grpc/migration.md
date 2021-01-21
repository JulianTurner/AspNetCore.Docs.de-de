---
title: Migrieren von gRPC-Diensten von C-core zu ASP.NET Core
author: juntaoluo
description: Erfahren Sie, wie Sie eine vorhandene C-core-basierte gRPC-App so verschieben, dass sie auf einem ASP.NET Core-Stapel ausgeführt wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 27c53dd4b41d6c99e45fccb5af79bab1ed5dc1b9
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253149"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a>Migrieren von gRPC-Diensten von C-core zu ASP.NET Core

Von [John Luo](https://github.com/juntaoluo)

Aufgrund der Implementierung des zugrundeliegenden Stapels funktionieren nicht alle Features in [C-core-basierten gRPC-Apps](https://grpc.io/blog/grpc-stacks) und ASP.NET Core-basierten Apps gleich. In diesem Artikel wird auf die Hauptunterschiede zwischen den beiden Stapeln in Bezug auf die Migration eingegangen.

## <a name="grpc-service-implementation-lifetime"></a>Lebensdauer der gRPC-Dienstimplementierung

Im ASP.NET Core-Stapel werden gRPC-Dienste standardmäßig mit [bereichsbezogener Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) erstellt. Im Gegensatz hierzu ist gRPC-C-core standardmäßig an einen Dienst mit [Singletonlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) gebunden.

Die bereichsbezogene Lebensdauer ermöglicht es der Dienstimplementierung, andere Dienste mit bereichsbezogener Lebensdauer aufzulösen. Bei einer bereichsbezogenen Lebensdauer kann beispielsweise auch `DbContext` durch Konstruktorinjektion aus dem Abhängigkeitsinjektionscontainer aufgelöst werden. Wenn bereichsbezogene Lebensdauer verwendet wird:

* wird für jede Anforderung eine neue Instanz der Dienstimplementierung konstruiert.
* ist es nicht möglich, Zustände zwischen Anforderungen über Instanzmember für den Implementierungstyp freizugeben.
* wird erwartet, dass freigegebene Zustände in einem Singletondienst im Abhängigkeitsinjektionscontainer gespeichert werden. Die gespeicherten freigegebenen Zustände werden im Konstruktor der gRPC-Dienstimplementierung aufgelöst.

Weitere Informationen zur Lebensdauer von Diensten finden Sie unter <xref:fundamentals/dependency-injection#service-lifetimes>.

### <a name="add-a-singleton-service"></a>Hinzufügen eines Singletondiensts

Die Übertragung einer gRPC-C-core-Implementierung in ASP.NET Core kann vereinfacht werden, indem die Lebensdauer der Dienstimplementierung von bereichsbezogen in Singleton geändert wird. Hierbei wird eine Instanz der Dienstimplementierung zum Abhängigkeitsinjektionscontainer hinzugefügt:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

Eine Dienstimplementierung mit Singletonlebensdauer kann allerdings bereichsbezogene Dienste nicht mehr durch Konstruktorinjektion auflösen.

## <a name="configure-grpc-services-options"></a>Konfigurieren von Optionen für gRPC-Dienste

In C-core-basierten Apps werden Einstellungen wie `grpc.max_receive_message_length` und `grpc.max_send_message_length` mit `ChannelOption` beim [Konstruieren der Serverinstanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__) konfiguriert.

In ASP.NET Core stellt gRPC die Konfiguration über den Typ `GrpcServiceOptions` bereit. Beispielsweise kann die Maximalgröße für eingehende Nachrichten für einen gRPC-Dienst über `AddGrpc` konfiguriert werden. Im folgenden Beispiel wird der Standardwert für `MaxReceiveMessageSize` von 4 MB in 16 MB geändert:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

Weitere Informationen zur Konfiguration finden Sie unter <xref:grpc/configuration>.

## <a name="logging"></a>Protokollierung

Bei C-core-basierten Apps wird mit `GrpcEnvironment` [die Protokollierung für Debuggingzwecke konfiguriert](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_). Im ASP.NET Core-Stapel wird diese Funktion über die [Protokollierungs-API](xref:fundamentals/logging/index) bereitgestellt. Beispielsweise kann Protokollierung über Konstruktorinjektion zum gRPC-Dienst hinzugefügt werden:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a>HTTPS

::: moniker range=">= aspnetcore-5.0"
Bei C-core-basierten Apps wird HTTPS über die [Eigenschaft Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports) konfiguriert. Ein ähnliches Konzept wird beim Konfigurieren von Servern in ASP.NET Core verwendet. Kestrel verwendet beispielsweise die [Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints) für diese Funktion.
::: moniker-end

::: moniker range="< aspnetcore-5.0"
Bei C-core-basierten Apps wird HTTPS über die [Eigenschaft Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports) konfiguriert. Ein ähnliches Konzept wird beim Konfigurieren von Servern in ASP.NET Core verwendet. Kestrel verwendet beispielsweise die [Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktion.
::: moniker-end

## <a name="grpc-interceptors-vs-middleware"></a>Gegenüberstellung gRPC-Interceptors und Middleware

ASP.NET Core-[Middleware](xref:fundamentals/middleware/index) bietet ähnliche Funktionen wie Interceptors in C-core-basierten gRPC-Apps. ASP.NET Core-Middleware und Interceptors sind konzeptionell ähnlich. Beide:

* werden dafür verwendet, eine Pipeline zu konstruieren, die gRPC-Anforderungen verarbeitet.
* ermöglichen das Ausführen von Arbeiten vor oder nach der nächsten Komponente in der Pipeline.
* ermöglichen den Zugriff auf `HttpContext`:
  * In der Middleware ist `HttpContext` ein Parameter.
  * In Interceptors kann auf `HttpContext` durch den Parameter `ServerCallContext` mit der Erweiterungsmethode `ServerCallContext.GetHttpContext` zugegriffen werden. Beachten Sie, dass dieses Feature spezifisch für Interceptors ist, die in ASP.NET Core ausgeführt werden.

Unterschiede zwischen gRPC-Interceptors und ASP.NET Core-Middleware:

* Interceptors:
  * arbeiten auf der gRPC-Abstraktionsebene mit [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).
  * stellen Zugriff bereit auf:
    * die deserialisierte, beim Aufruf gesendete Nachricht.
    * die beim Aufruf zurückgegebene Nachricht, bevor diese serialisiert wird.
  * können von gRPC-Diensten ausgelöste Ausnahmen abfangen und verarbeiten.
* Middleware:
  * wird vor gRPC-Interceptors ausgeführt.
  * arbeitet mit den zugrundeliegenden HTTP/2-Nachrichten.
  * kann nur auf Bytes aus den Anforderungs- und Antwortdatenströmen zugreifen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
