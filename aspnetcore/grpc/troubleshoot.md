---
title: Problembehandlung für gRPC in .NET Core
author: jamesnk
description: Beheben Sie Fehler bei der Verwendung von gRPC in .NET Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/09/2020
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
uid: grpc/troubleshoot
ms.openlocfilehash: 61d4d2204886f26b4ff55bc876825012809f1dfa
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253097"
---
# <a name="troubleshoot-grpc-on-net-core"></a>Problembehandlung für gRPC in .NET Core

Von [James Newton-King](https://twitter.com/jamesnk)

In diesem Dokument werden häufig auftretende Probleme bei der Entwicklung von gRPC-Apps in .NET behandelt.

## <a name="mismatch-between-client-and-service-ssltls-configuration"></a>Nichtübereinstimmung der SSL-/TLS-Konfiguration von Client und Dienst

In der gRPC-Vorlage und den gRPC-Beispielen werden gRPC-Dienste standardmäßig durch [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) gesichert. gRPC-Clients müssen eine sichere Verbindung verwenden, um gesicherte gRPC-Dienste aufzurufen zu können.

Sie können in den Protokollen, die beim Starten der App geschrieben werden, überprüfen, ob ein ASP.NET Core-gRPC-Dienst TLS verwendet. Der Dienst lauscht in diesem Fall auf einen HTTPS-Endpunkt:

```
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: https://localhost:5001
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Development
```

Der .NET Core-Client muss in der Serveradresse `https` verwenden, um Aufrufe über eine sichere Verbindung auszuführen:

```csharp
static async Task Main(string[] args)
{
    // The port number(5001) must match the port of the gRPC server.
    var channel = GrpcChannel.ForAddress("https://localhost:5001");
    var client = new Greet.GreeterClient(channel);
}
```

Alle gRPC-Clientimplementierungen unterstützen TLS. Bei gRPC-Clients mit anderen Sprachen ist üblicherweise ein mit `SslCredentials` konfigurierter Kanal erforderlich. `SslCredentials` gibt das Zertifikat an, das der Client verwendet, und muss anstelle von unsicheren Anmeldeinformationen verwendet werden. Beispiele für das Konfigurieren der verschiedenen gRPC-Clientimplementierungen für die Verwendung von TLS finden Sie unter [gRPC-Authentifizierung](https://www.grpc.io/docs/guides/auth/).

## <a name="call-a-grpc-service-with-an-untrustedinvalid-certificate"></a>Aufrufen eines gRPC-Diensts mit einem nicht vertrauenswürdigen/ungültigen Zertifikat

Der .NET-gRPC-Client erfordert, dass der Dienst über ein vertrauenswürdiges Zertifikat verfügt. Die folgende Fehlermeldung wird zurückgegeben, wenn ein gRPC-Dienst ohne vertrauenswürdiges Zertifikat aufgerufen wird:

> Ausnahmefehler. System.Net.Http.HttpRequestException: The SSL connection could not be established, see inner exception.
> ---> System.Security.Authentication.AuthenticationException: The remote certificate is invalid according to the validation procedure. (Ausnahmefehler. System.Net.Http.HttpRequestException: Es konnte keine SSL-Verbindung hergestellt werden, siehe interne Ausnahme. ---> System.Security.Authentication.AuthenticationException: Das Remotezertifikat ist laut Validierungsverfahren ungültig.)

Dieser Fehler wird möglicherweise angezeigt, wenn Sie Ihre App lokal testen und das ASP.NET Core-HTTPS-Entwicklungszertifikat nicht vertrauenswürdig ist. Anweisungen zum Beheben dieses Problems finden Sie unter [Einstufen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig unter Windows und macOS](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).

Wenn Sie einen gRPC-Dienst auf einem anderen Computer aufrufen und das Zertifikat nicht als vertrauenswürdig einstufen können, kann der gRPC-Client so konfiguriert werden, dass er das ungültige Zertifikat ignoriert. Im folgenden Code wird [HttpClientHandler.ServerCertificateCustomValidationCallback](/dotnet/api/system.net.http.httpclienthandler.servercertificatecustomvalidationcallback) verwendet, um Aufrufe ohne vertrauenswürdiges Zertifikat zuzulassen:

```csharp
var httpHandler = new HttpClientHandler();
// Return `true` to allow certificates that are untrusted/invalid
httpHandler.ServerCertificateCustomValidationCallback = 
    HttpClientHandler.DangerousAcceptAnyServerCertificateValidator;

var channel = GrpcChannel.ForAddress("https://localhost:5001",
    new GrpcChannelOptions { HttpHandler = httpHandler });
var client = new Greet.GreeterClient(channel);
```

> [!WARNING]
> Nicht vertrauenswürdige Zertifikate sollten nur während der App-Entwicklung verwendet werden. Produktions-Apps sollten stets gültige Zertifikate verwenden.

## <a name="call-insecure-grpc-services-with-net-core-client"></a>Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client

Wenn eine App .NET Core 3.x verwendet, sind weitere Konfigurationen erforderlich, um unsichere gRPC-Dienste mit dem .NET Core-Client aufrufen zu können. Der gRPC-Client muss für den Parameter `System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport` `true` festlegen und in der Serveradresse `http` verwenden:

```csharp
// This switch must be set before creating the GrpcChannel/HttpClient.
AppContext.SetSwitch(
    "System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);

// The port number(5000) must match the port of the gRPC server.
var channel = GrpcChannel.ForAddress("http://localhost:5000");
var client = new Greet.GreeterClient(channel);
```

Bei .NET 5-Apps ist keine zusätzliche Konfiguration erforderlich. Damit jedoch unsichere gRPC-Dienste aufgerufen werden können, muss mindestens Version 2.32.0 von `Grpc.Net.Client` verwendet werden.

## <a name="unable-to-start-aspnet-core-grpc-app-on-macos"></a>ASP.NET Core-gRPC-App kann unter macOS nicht gestartet werden

Kestrel unterstützt HTTP/2 mit TLS unter macOS und älteren Windows-Versionen wie Windows 7 nicht. In der ASP.NET Core-gRPC-Vorlage und den ASP.NET Core-gRPC-Beispielen wird standardmäßig TLS verwendet. Wenn Sie versuchen, den gRPC-Server zu starten, wird folgende Fehlermeldung angezeigt:

> Unable to bind to https://localhost:5001 on the IPv4 loopback interface: 'HTTP/2 over TLS is not supported on macOS due to missing ALPN support.'. (Eine Bindung an „https://localhost:5001“ auf der IPv4-Loopback-Schnittstelle kann nicht erfolgen: „HTTP/2 über TLS wird unter macOS aufgrund von fehlender ALPN-Unterstützung nicht unterstützt.“)

Konfigurieren Sie Kestrel und den gRPC-Client so, dass HTTP/2 *ohne* TLS verwendet wird, um dieses Problem zu umgehen. Dies sollten Sie nur während der Entwicklung tun. Wenn TLS nicht verwendet wird, führt dies dazu, dass gRPC-Nachrichten unverschlüsselt gesendet werden.

Kestrel muss einen HTTP/2-Endpunkt ohne TLS in *Program.cs* konfigurieren:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // Setup a HTTP/2 endpoint without TLS.
                options.ListenLocalhost(5000, o => o.Protocols = 
                    HttpProtocols.Http2);
            });
            webBuilder.UseStartup<Startup>();
        });
```

::: moniker range=">= aspnetcore-5.0"
Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel/endpoints#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden. `HttpProtocols.Http1AndHttp2` kann nicht verwendet werden, da zum Aushandeln von HTTP/2 TLS erforderlich ist. Ohne TLS wird für alle Verbindungen zu diesem Endpunkt standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.
::: moniker-end

::: moniker range="< aspnetcore-5.0"
Wenn ein HTTP/2-Endpunkt ohne TLS konfiguriert wird, muss bei diesem Endpunkt für [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) `HttpProtocols.Http2` festgelegt werden. `HttpProtocols.Http1AndHttp2` kann nicht verwendet werden, da zum Aushandeln von HTTP/2 TLS erforderlich ist. Ohne TLS wird für alle Verbindungen zu diesem Endpunkt standardmäßig HTTP/1.1 festgelegt, und gRPC-Aufrufe schlagen fehl.
::: moniker-end

Der gRPC-Client muss ebenfalls so konfiguriert sein, dass er kein TLS verwendet. Weitere Informationen finden Sie im Abschnitt [Aufrufen unsicherer gRPC-Dienste mit dem .NET Core-Client](#call-insecure-grpc-services-with-net-core-client).

> [!WARNING]
> HTTP/2 ohne TLS sollte nur während der App-Entwicklung verwendet werden. Produktions-Apps sollten stets Transportsicherheit verwenden. Weitere Informationen finden Sie unter [Sicherheitsüberlegungen zu gRPC für ASP.NET Core](xref:grpc/security#transport-security).

## <a name="grpc-c-assets-are-not-code-generated-from-proto-files"></a>Kein Generieren von Code aus PROTO-Dateien bei gRPC-C#-Objekten

Für das Generieren von gRPC-Code für konkrete Clients und Dienstbasisklassen muss in einem Projekt auf Protobuf-Dateien und -Tools verwiesen werden. Folgendes muss enthalten sein:

* *.proto*-Dateien, die Sie in der Elementgruppe `<Protobuf>` verwenden wollen. Auf [importierte *.proto*-Dateien](https://developers.google.com/protocol-buffers/docs/proto3#importing-definitions) muss im Projekt verwiesen werden.
* Paketverweis auf das gRPC-Toolpaket [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)

Weitere Informationen zum Generieren von gRPC-C#-Objekten finden Sie unter <xref:grpc/basics>.

Bei einer ASP.NET Core-Web-App, die gRPC-Dienste hostet, muss nur die Dienstbasisklasse generiert werden:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
</ItemGroup>
```

Bei einer gRPC-Client-App, die gRPC-Aufrufe ausführt, muss nur der konkrete Client generiert werden:

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" GrpcServices="Client" />
</ItemGroup>
```

## <a name="wpf-projects-unable-to-generate-grpc-c-assets-from-proto-files"></a>Generieren von gRPC-C#-Objekten aus PROTO-Dateien in WPF-Projekten nicht möglich

Es gibt ein [bekanntes Problem](https://github.com/dotnet/wpf/issues/810) bei WPF-Projekten, das verhindert, dass das Generieren von gRPC-Code richtig funktioniert. Bei der Verwendung aller gRPC-Typen, die in einem WPF-Projekt durch Verweise auf `Grpc.Tools` und *.proto*-Dateien generiert werden, treten Kompilierungsfehler auf:

> Fehler CS0246: Der Typ- oder Namespacename „MyGrpcServices“ wurde nicht gefunden (möglicherweise fehlt eine using-Direktive oder ein Assemblyverweis).

So umgehen Sie dieses Problem:

1. Erstellen Sie ein neues .NET Core-Klassenbibliotheksprojekt.
2. Fügen Sie im neuen Projekt Verweise hinzu, um das [Generieren von C#-Code aus *\*.proto*-Dateien](xref:grpc/basics#generated-c-assets) zu ermöglichen:
    * Fügen Sie dem [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/)-Paket einen Paketverweis hinzu.
    * Fügen Sie der `<Protobuf>`-Elementgruppe *\*.proto*-Dateien hinzu.
3. Fügen Sie in der WPF-Anwendung einen Verweis auf das neue Projekt hinzu.

Die WPF-Anwendung kann die in gRPC generierten Typen aus dem neuen Klassenbibliotheksprojekt verwenden.

[!INCLUDE[](~/includes/gRPCazure.md)]
