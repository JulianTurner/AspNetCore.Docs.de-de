---
title: Verwenden von gRPC in Browser-Apps
author: jamesnk
description: Erfahren Sie, wie Sie gRPC-Dienste für ASP.NET Core so konfigurieren, dass sie mit gRPC-Web von Browser-Apps aus aufgerufen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 5c9501b3e7cbdcbb02e3d78d67185a0a75ccba7c
ms.sourcegitcommit: c9b03d8a6a4dcc59e4aacb30a691f349235a74c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379405"
---
# <a name="use-grpc-in-browser-apps"></a>Verwenden von gRPC in Browser-Apps

Von [James Newton-King](https://twitter.com/jamesnk)

 Erfahren Sie, wie Sie einen vorhandenen gRPC-Dienst für ASP.NET Core so konfigurieren, dass er mit dem [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md)-Protokoll von Browser-Apps aus aufgerufen werden können. gRPC-Web erlaubt JavaScript- und Blazor-Apps im Browser, gRPC-Dienste aufzurufen. Es ist nicht möglich, einen HTTP/2-gRPC-Dienst von einer browserbasierten App aus aufzurufen. gRPC-Dienste, die in ASP.NET Core gehostet werden, können so konfiguriert werden, dass sie neben HTTP/2 gRPC auch gRPC-Web unterstützen.


Anweisungen zum Hinzufügen eines gRPC-Diensts zu einer vorhandenen ASP.NET Core-App finden Sie unter [Hinzufügen von gRPC-Diensten zu einer ASP.NET Core-App](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).

Anweisungen zum Erstellen eines gRPC-Projekts finden Sie unter <xref:tutorials/grpc/grpc-start>.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web in ASP.NET Core im Vergleich zu Envoy

Es gibt zwei Möglichkeiten zum Hinzufügen von gRPC-Web zu einer ASP.NET Core-App:

* Unterstützung von gRPC-Web parallel zu gRPC HTTP/2 in ASP.NET Core. Diese Option verwendet die mit dem `Grpc.AspNetCore.Web`-Paket bereitgestellte Middleware.
* Verwenden Sie die gRPC-Webunterstützung des [Envoy Proxys](https://www.envoyproxy.io/), um gRPC-Web in gRPC HTTP/2 zu übersetzen. Der übersetzte Aufruf wird dann an die ASP.NET Core-App weitergeleitet.

Beide Ansätze haben Vor- und Nachteile. Wenn die Umgebung einer App bereits Envoy als Proxy verwendet, kann es sinnvoll sein, Envoy auch zur Bereitstellung von gRPC-Webunterstützung zu verwenden. Als einfache Lösung für gRPC-Web, die nur ASP.NET Core erfordert, ist `Grpc.AspNetCore.Web` eine gute Wahl.

## <a name="configure-grpc-web-in-aspnet-core"></a>Konfigurieren von gRPC-Web in ASP.NET Core

gRPC-Dienste, die in ASP.NET Core gehostet werden, können so konfiguriert werden, dass sie neben HTTP/2 gRPC auch gRPC-Web unterstützen. Für gRPC-Web sind keine Änderungen der Dienste erforderlich. Die einzige Änderung ist die Startkonfiguration.

So aktivieren Sie gRPC-Web mit einem ASP.NET Core gRPC-Dienst

* Fügen Sie einen Verweis auf das Paket [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) hinzu.
* Konfigurieren Sie die App zur Verwendung von gRPC-Web, indem Sie `UseGrpcWeb` und `EnableGrpcWeb` zu *Startup.cs* hinzufügen:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Der vorangehende Code:

* Fügt die gRPC-Web-Middleware, `UseGrpcWeb`, nach dem Routing und vor den Endpunkten hinzu.
* Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die gRPC-Web mit `EnableGrpcWeb` unterstützt. 

Alternativ kann die gRPC-Web-Middleware so konfiguriert werden, dass alle Dienste standardmäßig gRPC-Web unterstützen und `EnableGrpcWeb` nicht erforderlich ist. Geben Sie `new GrpcWebOptions { DefaultEnabled = true }` an, wenn die Middleware hinzugefügt wird.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> Es gibt ein bekanntes Problem, das dazu führen kann, dass gRPC-Web fehlschlägt, wenn es [von Htpp.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x gehostet wird.
>
> Eine Problemumgehung, um gRPC-Web auf Http.sys betreiben zu können, finden Sie [hier](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).

### <a name="grpc-web-and-cors"></a>gRPC-Web und CORS

Die Browsersicherheit verhindert, dass eine Webseite Anforderungen an eine andere Domäne als diejenige stellt, die die Webseite versorgt hat. Diese Einschränkung gilt für das Durchführen von gRPC-Webaufrufen mit Browser-Apps. So wird z. B. eine Browser-App, die von `https://www.contoso.com` versorgt wird, daran gehindert, gRPC-Web-Dienste aufzurufen, die auf `https://services.contoso.com` gehostet werden. Die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross Origin Resource Sharing, CORS) kann verwendet werden, um diese Einschränkung zu lockern.

Damit eine Browser-App die Möglichkeit hat, gRPC-Web-Aufrufe ursprungsübergreifend zu tätigen, richten Sie [CORS in ASP.NET Core](xref:security/cors) ein. Verwenden Sie die integrierte CORS-Unterstützung, und machen Sie gRPC-spezifische Header mit <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> verfügbar.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Der vorangehende Code:

* Ruft `AddCors` zum Hinzufügen von CORS-Diensten auf und konfiguriert eine CORS-Richtlinie, die gRPC-spezifische Header verfügbar macht.
* Ruft `UseCors` zum Hinzufügen der CORS-Middleware nach dem Routing und vor den Endpunkten auf.
* Gibt die Methode `endpoints.MapGrpcService<GreeterService>()` an, die CORS mit `RequiresCors` unterstützt.

### <a name="grpc-web-and-streaming"></a>gRPC-Web und Streaming

Herkömmliches gRPC über HTTP/2 unterstützt das Streamen in alle Richtungen. gRPC-Web bietet eingeschränkte Unterstützung für Streaming:

* gRPC-Web-Browserclients unterstützen das Aufrufen von Clientstreaming- und bidirektionalen Streamingmethoden nicht.
* ASP.NET Core-gRPC-Dienste, die in Azure App Service und IIS gehostet werden, unterstützen das bidirektionale Streaming nicht.

Bei der Verwendung von gRPC-Web wird nur der Einsatz von unären Methoden und Serverstreamingmethoden empfohlen.

## <a name="call-grpc-web-from-the-browser"></a>Aufrufen von gRPC-Web über den Browser

Browser-Apps können gRPC-Web verwenden, um gRPC-Dienste aufzurufen. Es gibt einige Anforderungen und Einschränkungen beim Aufrufen von gRPC-Diensten mit gRPC-Web aus dem Browser heraus:

* Der Server muss für die Unterstützung von gRPC-Web konfiguriert worden sein.
* Clientstreaming und bidirektionale Streamingaufrufe werden nicht unterstützt. Das Serverstreaming wird unterstützt.
* Das Aufrufen von gRPC-Diensten in einer anderen Domäne erfordert die Konfiguration von [CORS](xref:security/cors) auf dem Server.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web-Client

Es gibt einen JavaScript gRPC-Web-Client. Anweisungen zur Verwendung von gRPC-Web aus JavaScript heraus finden Sie unter [Schreiben von JavaScript-Clientcode mit gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>Konfigurieren von gRPC-Web mit dem .NET gRPC-Client

Der .NET gRPC-Client kann für gRPC-Web-Aufrufe konfiguriert werden. Dies ist nützlich für [Blazor WebAssembly](xref:blazor/index#blazor-webassembly)-Apps, die im Browser gehostet werden und dieselben HTTP-Einschränkungen des JavaScript-Codes aufweisen. Das Aufrufen von gRPC-Web mit einem .NET-Client ist [dasselbe wie das Aufrufen von HTTP/2 gRPC](xref:grpc/client). Die einzige Änderung besteht darin, wie der Kanal erstellt wird.

So verwenden Sie gRPC-Web

* Fügen Sie einen Verweis auf das Paket [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) hinzu.
* Stellen Sie sicher, dass der Verweis auf das Paket [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) 2.29.0 oder höher ist.
* Konfigurieren Sie den Kanal für die Verwendung von `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Der vorangehende Code:

* Konfiguriert einen Kanal zur Verwendung von gRPC-Web.
* Erstellt einen Client und tätigt einen Aufruf über den Kanal.

`GrpcWebHandler` hat die folgenden Konfigurationsoptionen:

* **InnerHandler**: Der zugrundeliegende <xref:System.Net.Http.HttpMessageHandler>, der die gRPC HTTP-Anforderung stellt, z. B. `HttpClientHandler`.
* **GrpcWebMode**: Ein Enumerationstyp, der angibt, ob die gRPC-HTTP-Anforderung `Content-Type` `application/grpc-web` oder `application/grpc-web-text` ist.
    * `GrpcWebMode.GrpcWeb` konfiguriert Inhalte, die ohne Codierung gesendet werden. Standardwert.
    * `GrpcWebMode.GrpcWebText` konfiguriert den Inhalt so, dass er Base64-codiert ist. Erforderlich für Serverstreamingaufrufe in Browsern.
* **HttpVersion**: Die `Version` des HTTP-Protokolls, die verwendet wird, um [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) für die zugrunde liegende gRPC-HTTP-Anforderung festzulegen. gRPC-Web erfordert keine bestimmte Version und setzt die Standardeinstellung nicht außer Kraft, sofern nicht anders angegeben.

> [!IMPORTANT]
> Generierte gRPC-Clients verfügen über synchrone und asynchrone Methoden für den Aufruf unärer Methoden. Beispiel: `SayHello` ist synchron und `SayHelloAsync` ist asynchron. Der Aufruf einer synchronen Methode in einer Blazor WebAssembly-App führt dazu, dass die App nicht mehr reagiert. Asynchrone Methoden müssen in Blazor WebAssembly immer verwendet werden.

### <a name="use-grpc-client-factory-with-grpc-web"></a>Verwenden der gRPC-Clientfactory mit gRPC-Web

Ein mit gRPC-Web kompatibler .NET-Client kann mithilfe der gRPC-Integration mit [HttpClientFactory](xref:System.Net.Http.IHttpClientFactory) erstellt werden.

So verwenden Sie gRPC-Web mit der Clientfactory:

* Fügen Sie der Projektdatei Paketverweise für folgende Pakete hinzu:
  * [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web)
  * [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory)
* Registrieren Sie einen gRPC-Client mit Dependency Injection (DI), indem Sie die generische Erweiterungsmethode `AddGrpcClient` verwenden. In einer Blazor WebAssembly-App werden Dienste mit DI in `Program.cs` registriert.
* Konfigurieren Sie `GrpcWebHandler` mithilfe der Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler%2A>.

```csharp
builder.Services
    .AddGrpcClient<Greet.GreeterClient>((services, options) =>
    {
        options.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(
        () => new GrpcWebHandler(GrpcWebMode.GrpcWebText, new HttpClientHandler()));
```

Weitere Informationen finden Sie unter <xref:grpc/clientfactory>.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [gRPC für GitHub-Projekt für Webclients](https://github.com/grpc/grpc-web)
* <xref:security/cors>
* <xref:grpc/httpapi>
