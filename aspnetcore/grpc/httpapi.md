---
title: Erstellen von JSON-Web-APIs über gRPC
author: jamesnk
description: Hier erfahren Sie, wie sie JSON-HTTP-APIs für gRPC-Dienste erstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 64d18114e2fe9ee10edb902a98a281c3cd9f3393
ms.sourcegitcommit: aa85f2911792a1e4783bcabf0da3b3e7e218f63a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95417577"
---
# <a name="create-json-web-apis-from-grpc"></a>Erstellen von JSON-Web-APIs über gRPC

Von [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API ist ein experimentelles Projekt, kein veröffentlichtes Produkt. Wir möchten Folgendes:
>
> * Testen, ob unser Ansatz zum Erstellen von JSON-Web-APIs für gRPC-Dienste funktioniert
> * Feedback, ob der Ansatz für .NET-Entwickler nützlich ist
>
> Hinterlassen Sie [Feedback](https://github.com/grpc/grpc-dotnet/issues/167), damit wir sicherstellen können, dass Entwickler unser Projekt gern und produktiv nutzen.

gRPC ist eine moderne Kommunikationsmethode zwischen Apps. gRPC verwendet HTTP/2, Streaming, Protobuf und Nachrichtenverträge, um leistungsstarke Echtzeitdienste zu erstellen.

Eine Einschränkung von gRPC ist jedoch, dass nicht jede Plattform kompatibel ist. Browser unterstützen HTTP/2 nicht vollständig, sodass REST und JSON die primären Methoden sind, um Daten in Browser-Apps zu übertragen. Trotz der Vorteile von gRPC sind REST und JSON für moderne Apps wichtig. Wenn Sie gRPC- **und** JSON-Web-APIs entwickeln, steigt der Aufwand für die App-Entwicklung.

In diesem Dokument wird erläutert, wie JSON-Web-APIs mithilfe von gRPC-Diensten erstellt werden.

## <a name="grpc-http-api"></a>gRPC HTTP API

gRPC HTTP API ist eine experimentelle Erweiterung für ASP.NET Core, die RESTful-JSON-APIs für gRPC-Dienste erstellt. Nach der Konfiguration ermöglicht gRPC HTTP API es Apps, gRPC-Dienste mit gängigen HTTP-Konzepten aufzurufen:

HTTP-Verben
* URL-Parameterbindung
* JSON-Anforderungen und -Antworten

gRPC kann weiterhin zum Aufrufen von Diensten verwendet werden.

### <a name="usage"></a>Verwendung

1. Fügen Sie [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi) einen Paketverweis hinzu.
1. Registrieren Sie Dienste mit `AddGrpcHttpApi` in *Startup.cs*.
1. Fügen Sie die Dateien [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) und [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) zu Ihrem Projekt hinzu.
1. Kommentieren Sie die gRPC-Methoden in Ihren *PROTO*-Dateien mit HTTP-Bindungen und -Routen:

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Die gRPC-Methode `SayHello` kann jetzt als gRPC, Protobuf und HTTP-API aufgerufen werden:

* Anforderung: `HTTP/1.1 GET /v1/greeter/world`
* Antwort: `{ "message": "Hello world" }`

Aus den Serverprotokollen geht hervor, dass der HTTP-Aufruf von einem gRPC-Dienst ausgeführt wird. gRPC HTTP API ordnet die eingehende HTTP-Anforderung einer gRPC-Nachricht zu und konvertiert die Antwortnachricht anschließend in JSON.

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

Das ist ein einfaches Beispiel. Weitere Anpassungsoptionen finden Sie unter [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API und gRPC-Web im Vergleich

gRPC HTTP API und gRPC-Web ermöglichen es, gRPC-Dienste über einen Browser aufzurufen. Die Vorgehensweise unterscheidet sich jedoch:

* Mit gRPC-Web können Browser-Apps gRPC-Dienste mithilfe des gRPC-Web-Client und Protobuf über den Browser aufrufen. gRPC-Web setzt voraus, dass die Browser-App einen gRPC-Client generiert, und hat den Vorteil, dass kleine, schnelle Protobuf-Nachrichten gesendet werden.
* gRPC HTTP API ermöglicht es Browser-Apps, gRPC-Dienste so aufzurufen, als wären es RESTful-APIs mit JSON. Die Browser-App muss keinen gRPC-Client generieren und benötigt keine Informationen zu gRPC.

Für gRPC HTTP API wird kein Client generiert. Der vorherige `Greeter`-Dienst kann mithilfe von JavaScript-APIs für den Browser aufgerufen werden:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Experimenteller Status

gRPC HTTP API ist ein Experiment. Es ist nicht fertiggestellt und wird nicht unterstützt. Diese Technologie und die Möglichkeiten, die sie App-Entwicklern bietet, um schnell und gleichzeitig gRPC- und JSON-Dienste zu erstellen, sind für uns interessant. Es kann jedoch nicht garantiert werden, dass gRPC HTTP API fertiggestellt wird.

Das Interesse der Entwickler an gRPC HTTP API soll gemessen werden. [Senden Sie uns Feedback](https://github.com/grpc/grpc-dotnet/issues/167), wenn gRPC HTTP API für Sie interessant ist.

## <a name="grpc-gateway"></a>grpc-gateway

[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) ist eine weitere Technologie zum Erstellen von RESTful-JSON-APIs über gRPC-Dienste. Es werden die gleichen *PROTO*-Anmerkungen verwendet, um HTTP-Konzepte gRPC-Diensten zuzuordnen.

Der größte Unterschied zwischen grpc-gateway und gRPC HTTP API besteht darin, dass grpc-gateway die Codegenerierung zum Erstellen eines Reverseproxyservers verwendet. Der Reverseproxy übersetzt RESTful-Aufrufe in gRPC und sendet diese dann an den gRPC-Dienst.

Weitere Informationen zur Installation und Verwendung von grpc-gateway finden Sie in der [INFODATEI zu grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Dokumentation zu google.api.HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
