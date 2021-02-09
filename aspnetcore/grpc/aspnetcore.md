---
title: gRPC-Dienste mit ASP.NET Core
author: juntaoluo
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von gRPC-Diensten mit ASP.NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 01/29/2021
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
uid: grpc/aspnetcore
ms.openlocfilehash: f17ba247747f906cf026fc0f7bc04d51f4c8cb2a
ms.sourcegitcommit: e311cfb77f26a0a23681019bd334929d1aaeda20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99530202"
---
# <a name="grpc-services-with-aspnet-core"></a>gRPC-Dienste mit ASP.NET Core

In diesem Dokument wird gezeigt, wie Sie mit gRPC-Diensten unter Verwendung von ASP.NET Core die ersten Schritte unternehmen können.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="prerequisites"></a>Voraussetzungen

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a>Erste Schritte mit dem gRPC-Dienst in ASP.NET Core

[Zeigen Sie Beispielcode an, oder laden Sie diesen herunter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Ausführliche Informationen zum Erstellen eines gRPC-Projekts finden Sie unter [Erste Schritte mit gRPC-Diensten](xref:tutorials/grpc/grpc-start).

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

Führen Sie `dotnet new grpc -o GrpcGreeter` über die Befehlszeile aus.

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a>Hinzufügen von gRPC-Diensten zu einer ASP.NET Core-App

gRPC erfordert das Paket [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore).

### <a name="configure-grpc"></a>Konfigurieren von gRPC

In *Startup.cs*:

* gRPC wird mit der Methode `AddGrpc` aktiviert.
* Jeder gRPC-Dienst wird der Routingpipeline durch die `MapGrpcService`-Methode hinzugefügt.

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

ASP.NET Core-Middleware und -Features teilen sich die Routingpipeline, daher kann eine App so konfiguriert werden, dass sie zusätzliche Anforderungshandler bedient. Die zusätzlichen Anforderungshandler wie MVC-Controller arbeiten parallel zu den konfigurierten gRPC-Diensten.

## <a name="server-options"></a>Serveroptionen

gRPC-Dienste können von allen integrierten ASP.NET Core-Servern gehostet werden.

> [!div class="checklist"]
>
> * Kestrel
> * TestServer
> * IIS&dagger;
> * HTTP.sys&Dagger;

&dagger;IIS erfordert .NET 5 und Windows 10 Build 20241 oder höher.

&Dagger;HTTP.sys erfordert .NET 5 und Windows 10 Build 19529 oder höher.

Weitere Informationen zum Auswählen des richtigen Servers für eine ASP.NET Core-App finden Sie unter <xref:fundamentals/servers/index>.

::: moniker range=">= aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) ist ein plattformübergreifender Webserver für ASP.NET Core. Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige der erweiterten Features in HTTP.sys wie Portfreigabe.

Kestrel gRPC-Endpunkte:

* Erfordert HTTP/2.
* Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.

### <a name="http2"></a>HTTP/2

gRPC erfordert HTTP/2. gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.

Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel/http2) auf den meisten modernen Betriebssystemen. Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.

### <a name="tls"></a>TLS

Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden. In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist. Es ist keine Konfiguration erforderlich. Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.

In der Produktion muss TLS explizit konfiguriert sein. Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Protokollaushandlung

TLS wird für mehr als nur die Sicherung der Kommunikation verwendet. Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt. Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.

Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden. Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt. Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.

Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel/endpoints).

> [!NOTE]
> macOS unterstützt ASP.NET Core gRPC mit TLS nicht. Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

## <a name="iis"></a>IIS

[IIS (Internet Information Services, Internetinformationsdienste)](xref:host-and-deploy/iis/index) stellen einen flexiblen, sicheren und verwaltbaren Webserver für das Hosten von Web-Apps (einschließlich ASP.NET Core) dar. .NET 5 und Windows 10 Build 20241 oder höher sind erforderlich, um gRPC-Dienste mit IIS zu hosten.

IIS muss für die Verwendung von TLS und HTTP/2 konfiguriert sein. Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/protocols>.

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) ist ein Webserver für ASP.NET Core, der nur unter Windows ausgeführt werden kann. .NET 5 und Windows 10 Build 20241 oder höher sind erforderlich, um gRPC-Dienste mit HTTP.sys zu hosten.

HTTP.sys muss für die Verwendung von TLS und HTTP/2 konfiguriert sein. Weitere Informationen finden Sie unter [HTTP/2-Unterstützung](xref:fundamentals/servers/httpsys#http2-support).

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## <a name="kestrel"></a>Kestrel

[Kestrel](xref:fundamentals/servers/kestrel) ist ein plattformübergreifender Webserver für ASP.NET Core. Kestrel bietet die beste Leistung und Arbeitsspeicherauslastung, verfügt jedoch nicht über einige der erweiterten Features in HTTP.sys wie Portfreigabe.

Kestrel gRPC-Endpunkte:

* Erfordert HTTP/2.
* Sollte mit [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert werden.

### <a name="http2"></a>HTTP/2

gRPC erfordert HTTP/2. gRPC für ASP.NET Core überprüft [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol%2A) zu `HTTP/2`.

Kestrel [unterstützt HTTP/2](xref:fundamentals/servers/kestrel#http2-support) auf den meisten modernen Betriebssystemen. Kestrel Endpunkte sind standardmäßig für die Unterstützung von HTTP/1.1- und HTTP/2-Verbindungen konfiguriert.

### <a name="tls"></a>TLS

Die für gRPC verwendeten Kestrel-Endpunkte sollten mit TLS gesichert werden. In der Entwicklung wird ein mit TLS gesicherter Endpunkt automatisch bei `https://localhost:5001` erstellt, wenn das ASP.NET Core-Entwicklungszertifikat vorhanden ist. Es ist keine Konfiguration erforderlich. Ein `https`-Präfix prüft, ob der Kestrel-Endpunkt TLS verwendet.

In der Produktion muss TLS explizit konfiguriert sein. Im folgenden *appsettings.json* -Beispiel wird ein mit TLS gesicherter HTTP/2-Endpunkt bereitgestellt:

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

Alternativ können die Kestrel-Endpunkte in *Program.cs* konfiguriert werden:

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

### <a name="protocol-negotiation"></a>Protokollaushandlung

TLS wird für mehr als nur die Sicherung der Kommunikation verwendet. Der TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)-Handshake wird zur Aushandlung des Verbindungsprotokolls zwischen dem Client und dem Server verwendet, wenn ein Endpunkt mehrere Protokolle unterstützt. Diese Aushandlung bestimmt, ob die Verbindung HTTP/1.1 oder HTTP/2 verwendet.

Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden. Ein Endpunkt mit mehreren Protokollen (z. B. `HttpProtocols.Http1AndHttp2`) kann ohne TLS nicht verwendet werden, da keine Aushandlung erfolgt. Für alle Verbindungen zum ungesicherten Endpunkt wird standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.

Weitere Informationen zum Aktivieren von HTTP/2 und TLS mit Kestrel finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!NOTE]
> macOS unterstützt ASP.NET Core gRPC mit TLS nicht. Zum erfolgreichen Ausführen von gRPC-Diensten unter macOS ist eine zusätzliche Konfiguration erforderlich. Weitere Informationen finden Sie unter [ASP.NET Core gRPC-App kann unter macOS nicht gestartet werden](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).

::: moniker-end

## <a name="integration-with-aspnet-core-apis"></a>Integration mit ASP.NET Core-APIs

gRPC-Dienste haben vollen Zugriff auf die ASP.NET Core-Features wie [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) und [ Protokollierung](xref:fundamentals/logging/index). So kann die Dienstimplementierung z. B. einen Protokollierungsdienst aus dem Container der Abhängigkeitsinjektion über den Konstruktor auflösen:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

Standardmäßig kann die gRPC-Dienstimplementierung andere Abhängigkeitsinjektionsdienste mit beliebiger Lebensdauer (Singleton, Bereichsbezogen oder Vorübergehend) auflösen.

### <a name="resolve-httpcontext-in-grpc-methods"></a>Auflösen von HttpContext in gRPC-Methoden

Die gRPC-API bietet Zugriff auf einige HTTP/2-Nachrichtendaten, z. B. Methode, Host, Header und Nachspanne. Der Zugriff erfolgt über das Argument `ServerCallContext`, das an jede gRPC-Methode übergeben wird:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

`ServerCallContext` bietet nicht in allen ASP.NET-APIs vollen Zugriff auf `HttpContext`. Die `GetHttpContext`-Erweiterungsmethode bietet vollen Zugriff auf `HttpContext`, das die zugrunde liegende HTTP/2-Nachricht in ASP.NET-APIs darstellt:

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]


## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
