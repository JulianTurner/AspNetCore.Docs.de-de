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
ms.openlocfilehash: cb2855f0293a6bc800bb5758cd1a8400d4434a24
ms.sourcegitcommit: a71bb61f7add06acb949c9258fe506914dfe0c08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855460"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="5448a-103">Erstellen von JSON-Web-APIs über gRPC</span><span class="sxs-lookup"><span data-stu-id="5448a-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="5448a-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5448a-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5448a-105">gRPC HTTP API ist ein experimentelles Projekt, kein veröffentlichtes Produkt.</span><span class="sxs-lookup"><span data-stu-id="5448a-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="5448a-106">Wir möchten Folgendes:</span><span class="sxs-lookup"><span data-stu-id="5448a-106">We want to:</span></span>
>
> * <span data-ttu-id="5448a-107">Testen, ob unser Ansatz zum Erstellen von JSON-Web-APIs für gRPC-Dienste funktioniert</span><span class="sxs-lookup"><span data-stu-id="5448a-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="5448a-108">Feedback, ob der Ansatz für .NET-Entwickler nützlich ist</span><span class="sxs-lookup"><span data-stu-id="5448a-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="5448a-109">Hinterlassen Sie [Feedback](https://github.com/grpc/grpc-dotnet/issues/167), damit wir sicherstellen können, dass Entwickler unser Projekt gern und produktiv nutzen.</span><span class="sxs-lookup"><span data-stu-id="5448a-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="5448a-110">gRPC ist eine moderne Kommunikationsmethode zwischen Apps.</span><span class="sxs-lookup"><span data-stu-id="5448a-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="5448a-111">gRPC verwendet HTTP/2, Streaming, Protobuf und Nachrichtenverträge, um leistungsstarke Echtzeitdienste zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="5448a-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="5448a-112">Eine Einschränkung von gRPC ist jedoch, dass nicht jede Plattform kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="5448a-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="5448a-113">Browser unterstützen HTTP/2 nicht vollständig, sodass REST und JSON die primären Methoden sind, um Daten in Browser-Apps zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="5448a-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="5448a-114">Trotz der Vorteile von gRPC sind REST und JSON für moderne Apps wichtig.</span><span class="sxs-lookup"><span data-stu-id="5448a-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="5448a-115">Wenn Sie gRPC- **und** JSON-Web-APIs entwickeln, steigt der Aufwand für die App-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="5448a-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="5448a-116">In diesem Dokument wird erläutert, wie JSON-Web-APIs mithilfe von gRPC-Diensten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="5448a-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="5448a-117">gRPC HTTP API</span></span>

<span data-ttu-id="5448a-118">gRPC HTTP API ist eine experimentelle Erweiterung für ASP.NET Core, die RESTful-JSON-APIs für gRPC-Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="5448a-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="5448a-119">Nach der Konfiguration ermöglicht gRPC HTTP API es Apps, gRPC-Dienste mit gängigen HTTP-Konzepten aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="5448a-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="5448a-120">HTTP-Verben</span><span class="sxs-lookup"><span data-stu-id="5448a-120">_ HTTP verbs</span></span>
* <span data-ttu-id="5448a-121">URL-Parameterbindung</span><span class="sxs-lookup"><span data-stu-id="5448a-121">URL parameter binding</span></span>
* <span data-ttu-id="5448a-122">JSON-Anforderungen und -Antworten</span><span class="sxs-lookup"><span data-stu-id="5448a-122">JSON requests/responses</span></span>

<span data-ttu-id="5448a-123">gRPC kann weiterhin zum Aufrufen von Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="5448a-124">Verwendung</span><span class="sxs-lookup"><span data-stu-id="5448a-124">Usage</span></span>

1. <span data-ttu-id="5448a-125">Fügen Sie [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="5448a-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="5448a-126">Registrieren Sie Dienste mit `AddGrpcHttpApi` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5448a-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="5448a-127">Fügen Sie die Dateien [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) und [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="5448a-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="5448a-128">Kommentieren Sie die gRPC-Methoden in Ihren *PROTO*-Dateien mit HTTP-Bindungen und -Routen:</span><span class="sxs-lookup"><span data-stu-id="5448a-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

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

<span data-ttu-id="5448a-129">Die gRPC-Methode `SayHello` kann jetzt als gRPC, Protobuf und HTTP-API aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="5448a-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="5448a-130">Anforderung: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="5448a-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="5448a-131">Antwort: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="5448a-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="5448a-132">Aus den Serverprotokollen geht hervor, dass der HTTP-Aufruf von einem gRPC-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="5448a-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="5448a-133">gRPC HTTP API ordnet die eingehende HTTP-Anforderung einer gRPC-Nachricht zu und konvertiert die Antwortnachricht anschließend in JSON.</span><span class="sxs-lookup"><span data-stu-id="5448a-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

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

<span data-ttu-id="5448a-134">Das ist ein einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="5448a-134">This is a basic example.</span></span> <span data-ttu-id="5448a-135">Weitere Anpassungsoptionen finden Sie unter [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span><span class="sxs-lookup"><span data-stu-id="5448a-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="5448a-136">Aktivieren der Unterstützung für Swagger/OpenAPI</span><span class="sxs-lookup"><span data-stu-id="5448a-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="5448a-137">Bei Swagger (OpenAPI) handelt es sich um eine sprachunabhängige Spezifikation für das Beschreiben von REST-APIs.</span><span class="sxs-lookup"><span data-stu-id="5448a-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="5448a-138">Die gRPC-HTTP-API lässt sich in [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) integrieren, um einen Swagger-Endpunkt für RESTful-gRPC-Dienste zu generieren.</span><span class="sxs-lookup"><span data-stu-id="5448a-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="5448a-139">Dann kann der Swagger-Endpunkt mit der [Swagger-Benutzeroberfläche](https://swagger.io/swagger-ui/) und anderen Tools verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="5448a-140">So aktivieren Sie Swagger mit der gRPC-HTTP-API:</span><span class="sxs-lookup"><span data-stu-id="5448a-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="5448a-141">Fügen Sie [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="5448a-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="5448a-142">Konfigurieren Sie Swashbuckle in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="5448a-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="5448a-143">Die `AddGrpcSwagger`-Methode konfiguriert Swashbuckle so, dass gRPC-HTTP-API-Endpunkte eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="5448a-144">Um zu überprüfen, ob Swashbuckle Swagger für die RESTful-gRPC-Dienste generiert, starten Sie die App und navigieren zur Seite der Swagger-Benutzeroberfläche:</span><span class="sxs-lookup"><span data-stu-id="5448a-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![Swagger-Benutzeroberfläche](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="5448a-146">gRPC HTTP API und gRPC-Web im Vergleich</span><span class="sxs-lookup"><span data-stu-id="5448a-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="5448a-147">gRPC HTTP API und gRPC-Web ermöglichen es, gRPC-Dienste über einen Browser aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="5448a-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="5448a-148">Die Vorgehensweise unterscheidet sich jedoch:</span><span class="sxs-lookup"><span data-stu-id="5448a-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="5448a-149">Mit gRPC-Web können Browser-Apps gRPC-Dienste mithilfe des gRPC-Web-Client und Protobuf über den Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="5448a-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="5448a-150">gRPC-Web setzt voraus, dass die Browser-App einen gRPC-Client generiert, und hat den Vorteil, dass kleine, schnelle Protobuf-Nachrichten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="5448a-151">gRPC HTTP API ermöglicht es Browser-Apps, gRPC-Dienste so aufzurufen, als wären es RESTful-APIs mit JSON.</span><span class="sxs-lookup"><span data-stu-id="5448a-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="5448a-152">Die Browser-App muss keinen gRPC-Client generieren und benötigt keine Informationen zu gRPC.</span><span class="sxs-lookup"><span data-stu-id="5448a-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="5448a-153">Für gRPC HTTP API wird kein Client generiert.</span><span class="sxs-lookup"><span data-stu-id="5448a-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="5448a-154">Der vorherige `Greeter`-Dienst kann mithilfe von JavaScript-APIs für den Browser aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="5448a-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="5448a-155">Experimenteller Status</span><span class="sxs-lookup"><span data-stu-id="5448a-155">Experimental status</span></span>

<span data-ttu-id="5448a-156">gRPC HTTP API ist ein Experiment.</span><span class="sxs-lookup"><span data-stu-id="5448a-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="5448a-157">Es ist nicht fertiggestellt und wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="5448a-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="5448a-158">Diese Technologie und die Möglichkeiten, die sie App-Entwicklern bietet, um schnell und gleichzeitig gRPC- und JSON-Dienste zu erstellen, sind für uns interessant.</span><span class="sxs-lookup"><span data-stu-id="5448a-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="5448a-159">Es kann jedoch nicht garantiert werden, dass gRPC HTTP API fertiggestellt wird.</span><span class="sxs-lookup"><span data-stu-id="5448a-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="5448a-160">Das Interesse der Entwickler an gRPC HTTP API soll gemessen werden.</span><span class="sxs-lookup"><span data-stu-id="5448a-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="5448a-161">[Senden Sie uns Feedback](https://github.com/grpc/grpc-dotnet/issues/167), wenn gRPC HTTP API für Sie interessant ist.</span><span class="sxs-lookup"><span data-stu-id="5448a-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="5448a-162">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="5448a-162">grpc-gateway</span></span>

<span data-ttu-id="5448a-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) ist eine weitere Technologie zum Erstellen von RESTful-JSON-APIs über gRPC-Dienste.</span><span class="sxs-lookup"><span data-stu-id="5448a-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="5448a-164">Es werden die gleichen *PROTO*-Anmerkungen verwendet, um HTTP-Konzepte gRPC-Diensten zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="5448a-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="5448a-165">Der größte Unterschied zwischen grpc-gateway und gRPC HTTP API besteht darin, dass grpc-gateway die Codegenerierung zum Erstellen eines Reverseproxyservers verwendet.</span><span class="sxs-lookup"><span data-stu-id="5448a-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="5448a-166">Der Reverseproxy übersetzt RESTful-Aufrufe in gRPC und sendet diese dann an den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="5448a-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="5448a-167">Weitere Informationen zur Installation und Verwendung von grpc-gateway finden Sie in der [INFODATEI zu grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="5448a-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5448a-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5448a-168">Additional resources</span></span>

* [<span data-ttu-id="5448a-169">Dokumentation zu google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="5448a-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
