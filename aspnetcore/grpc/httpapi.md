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
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="3dbb3-103">Erstellen von JSON-Web-APIs über gRPC</span><span class="sxs-lookup"><span data-stu-id="3dbb3-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="3dbb3-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3dbb3-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3dbb3-105">gRPC HTTP API ist ein experimentelles Projekt, kein veröffentlichtes Produkt.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="3dbb3-106">Wir möchten Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-106">We want to:</span></span>
>
> * <span data-ttu-id="3dbb3-107">Testen, ob unser Ansatz zum Erstellen von JSON-Web-APIs für gRPC-Dienste funktioniert</span><span class="sxs-lookup"><span data-stu-id="3dbb3-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="3dbb3-108">Feedback, ob der Ansatz für .NET-Entwickler nützlich ist</span><span class="sxs-lookup"><span data-stu-id="3dbb3-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="3dbb3-109">Hinterlassen Sie [Feedback](https://github.com/grpc/grpc-dotnet/issues/167), damit wir sicherstellen können, dass Entwickler unser Projekt gern und produktiv nutzen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="3dbb3-110">gRPC ist eine moderne Kommunikationsmethode zwischen Apps.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="3dbb3-111">gRPC verwendet HTTP/2, Streaming, Protobuf und Nachrichtenverträge, um leistungsstarke Echtzeitdienste zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="3dbb3-112">Eine Einschränkung von gRPC ist jedoch, dass nicht jede Plattform kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="3dbb3-113">Browser unterstützen HTTP/2 nicht vollständig, sodass REST und JSON die primären Methoden sind, um Daten in Browser-Apps zu übertragen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="3dbb3-114">Trotz der Vorteile von gRPC sind REST und JSON für moderne Apps wichtig.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="3dbb3-115">Wenn Sie gRPC- **und** JSON-Web-APIs entwickeln, steigt der Aufwand für die App-Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="3dbb3-116">In diesem Dokument wird erläutert, wie JSON-Web-APIs mithilfe von gRPC-Diensten erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="3dbb3-117">gRPC HTTP API</span><span class="sxs-lookup"><span data-stu-id="3dbb3-117">gRPC HTTP API</span></span>

<span data-ttu-id="3dbb3-118">gRPC HTTP API ist eine experimentelle Erweiterung für ASP.NET Core, die RESTful-JSON-APIs für gRPC-Dienste erstellt.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="3dbb3-119">Nach der Konfiguration ermöglicht gRPC HTTP API es Apps, gRPC-Dienste mit gängigen HTTP-Konzepten aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="3dbb3-120">HTTP-Verben</span><span class="sxs-lookup"><span data-stu-id="3dbb3-120">_ HTTP verbs</span></span>
* <span data-ttu-id="3dbb3-121">URL-Parameterbindung</span><span class="sxs-lookup"><span data-stu-id="3dbb3-121">URL parameter binding</span></span>
* <span data-ttu-id="3dbb3-122">JSON-Anforderungen und -Antworten</span><span class="sxs-lookup"><span data-stu-id="3dbb3-122">JSON requests/responses</span></span>

<span data-ttu-id="3dbb3-123">gRPC kann weiterhin zum Aufrufen von Diensten verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="3dbb3-124">Verwendung</span><span class="sxs-lookup"><span data-stu-id="3dbb3-124">Usage</span></span>

1. <span data-ttu-id="3dbb3-125">Fügen Sie [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi) einen Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="3dbb3-126">Registrieren Sie Dienste mit `AddGrpcHttpApi` in *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="3dbb3-127">Fügen Sie die Dateien [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) und [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="3dbb3-128">Kommentieren Sie die gRPC-Methoden in Ihren *PROTO*-Dateien mit HTTP-Bindungen und -Routen:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

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

<span data-ttu-id="3dbb3-129">Die gRPC-Methode `SayHello` kann jetzt als gRPC, Protobuf und HTTP-API aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="3dbb3-130">Anforderung: `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="3dbb3-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="3dbb3-131">Antwort: `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="3dbb3-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="3dbb3-132">Aus den Serverprotokollen geht hervor, dass der HTTP-Aufruf von einem gRPC-Dienst ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="3dbb3-133">gRPC HTTP API ordnet die eingehende HTTP-Anforderung einer gRPC-Nachricht zu und konvertiert die Antwortnachricht anschließend in JSON.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

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

<span data-ttu-id="3dbb3-134">Das ist ein einfaches Beispiel.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-134">This is a basic example.</span></span> <span data-ttu-id="3dbb3-135">Weitere Anpassungsoptionen finden Sie unter [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule).</span><span class="sxs-lookup"><span data-stu-id="3dbb3-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="3dbb3-136">gRPC HTTP API und gRPC-Web im Vergleich</span><span class="sxs-lookup"><span data-stu-id="3dbb3-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="3dbb3-137">gRPC HTTP API und gRPC-Web ermöglichen es, gRPC-Dienste über einen Browser aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="3dbb3-138">Die Vorgehensweise unterscheidet sich jedoch:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="3dbb3-139">Mit gRPC-Web können Browser-Apps gRPC-Dienste mithilfe des gRPC-Web-Client und Protobuf über den Browser aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="3dbb3-140">gRPC-Web setzt voraus, dass die Browser-App einen gRPC-Client generiert, und hat den Vorteil, dass kleine, schnelle Protobuf-Nachrichten gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="3dbb3-141">gRPC HTTP API ermöglicht es Browser-Apps, gRPC-Dienste so aufzurufen, als wären es RESTful-APIs mit JSON.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="3dbb3-142">Die Browser-App muss keinen gRPC-Client generieren und benötigt keine Informationen zu gRPC.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="3dbb3-143">Für gRPC HTTP API wird kein Client generiert.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="3dbb3-144">Der vorherige `Greeter`-Dienst kann mithilfe von JavaScript-APIs für den Browser aufgerufen werden:</span><span class="sxs-lookup"><span data-stu-id="3dbb3-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="3dbb3-145">Experimenteller Status</span><span class="sxs-lookup"><span data-stu-id="3dbb3-145">Experimental status</span></span>

<span data-ttu-id="3dbb3-146">gRPC HTTP API ist ein Experiment.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="3dbb3-147">Es ist nicht fertiggestellt und wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="3dbb3-148">Diese Technologie und die Möglichkeiten, die sie App-Entwicklern bietet, um schnell und gleichzeitig gRPC- und JSON-Dienste zu erstellen, sind für uns interessant.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="3dbb3-149">Es kann jedoch nicht garantiert werden, dass gRPC HTTP API fertiggestellt wird.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="3dbb3-150">Das Interesse der Entwickler an gRPC HTTP API soll gemessen werden.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="3dbb3-151">[Senden Sie uns Feedback](https://github.com/grpc/grpc-dotnet/issues/167), wenn gRPC HTTP API für Sie interessant ist.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="3dbb3-152">grpc-gateway</span><span class="sxs-lookup"><span data-stu-id="3dbb3-152">grpc-gateway</span></span>

<span data-ttu-id="3dbb3-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) ist eine weitere Technologie zum Erstellen von RESTful-JSON-APIs über gRPC-Dienste.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="3dbb3-154">Es werden die gleichen *PROTO*-Anmerkungen verwendet, um HTTP-Konzepte gRPC-Diensten zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="3dbb3-155">Der größte Unterschied zwischen grpc-gateway und gRPC HTTP API besteht darin, dass grpc-gateway die Codegenerierung zum Erstellen eines Reverseproxyservers verwendet.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="3dbb3-156">Der Reverseproxy übersetzt RESTful-Aufrufe in gRPC und sendet diese dann an den gRPC-Dienst.</span><span class="sxs-lookup"><span data-stu-id="3dbb3-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="3dbb3-157">Weitere Informationen zur Installation und Verwendung von grpc-gateway finden Sie in der [INFODATEI zu grpc-gateway](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="3dbb3-157">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3dbb3-158">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3dbb3-158">Additional resources</span></span>

* [<span data-ttu-id="3dbb3-159">Dokumentation zu google.api.HttpRule</span><span class="sxs-lookup"><span data-stu-id="3dbb3-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
