---
title: Testen von gRPC-Diensten mit gRPCurl in ASP.NET Core
author: jamesnk
description: Hier erfahren Sie, wie sie Dienste mit gRPC-Tools testen. gRPCurl ist ein Befehlszeilentool für die Interaktion mit gRPC-Diensten. gRPCui ist eine interaktive Webbenutzeroberfläche.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/test-tools
ms.openlocfilehash: d8d12c34a54b278e0b116f964e120047b1d2d5d1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058791"
---
# <a name="test-grpc-services-with-grpcurl-in-aspnet-core"></a><span data-ttu-id="3fd9b-105">Testen von gRPC-Diensten mit gRPCurl in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fd9b-105">Test gRPC services with gRPCurl in ASP.NET Core</span></span>

<span data-ttu-id="3fd9b-106">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="3fd9b-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="3fd9b-107">Für gRPC sind Tools verfügbar, mit deren Hilfe Entwickler Dienste testen können, ohne Client-Apps zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-107">Tooling is available for gRPC that allows developers to test services without building client apps:</span></span>

* <span data-ttu-id="3fd9b-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) ist ein Befehlszeilentool, das die Interaktion mit gRPC-Diensten ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span>
* <span data-ttu-id="3fd9b-109">[gRPCui](https://github.com/fullstorydev/grpcui) baut auf gRPCurl auf und fügt eine interaktive Webbenutzeroberfläche für gRPC hinzu, ähnlich wie Tools wie Postman und die Swagger-Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-109">[gRPCui](https://github.com/fullstorydev/grpcui) builds on top of gRPCurl and adds an interactive web UI for gRPC, similar to tools such as Postman and Swagger UI.</span></span>

<span data-ttu-id="3fd9b-110">In diesem Artikel wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-110">This article discusses how to:</span></span>

* <span data-ttu-id="3fd9b-111">Herunterladen und Installieren von gRPCurl und gRPCui</span><span class="sxs-lookup"><span data-stu-id="3fd9b-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="3fd9b-112">Einrichten von gRPC-Reflexion mit einer gRPC ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-112">Set up gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="3fd9b-113">Ermitteln und Testen von gRPC-Diensten mit `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="3fd9b-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="3fd9b-114">Interagieren mit gRPC-Diensten über einen Browser mithilfe von `grpcui`</span><span class="sxs-lookup"><span data-stu-id="3fd9b-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="3fd9b-115">Informationen zu gRPCurl</span><span class="sxs-lookup"><span data-stu-id="3fd9b-115">About gRPCurl</span></span>

<span data-ttu-id="3fd9b-116">gRPCurl ist ein von der gRPC-Community erstelltes Befehlszeilentool.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="3fd9b-117">Es bietet folgende Features:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-117">Its features include:</span></span>

* <span data-ttu-id="3fd9b-118">Aufrufen von gRPC-Diensten wie Streamingdienste</span><span class="sxs-lookup"><span data-stu-id="3fd9b-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="3fd9b-119">Dienstermittlung mithilfe der [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)</span><span class="sxs-lookup"><span data-stu-id="3fd9b-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="3fd9b-120">Auflisten und Beschreiben von gRPC-Diensten</span><span class="sxs-lookup"><span data-stu-id="3fd9b-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="3fd9b-121">Kann mit sicheren Servern (TLS-Servern) und unsicheren (unverschlüsselten) Servern verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="3fd9b-122">Informationen zum Herunterladen und Installieren von `grpcurl` finden Sie auf der [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="3fd9b-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

![gRPCurl-Befehlszeile](~/grpc/test-tools/static/grpcurl.png)

## <a name="set-up-grpc-reflection"></a><span data-ttu-id="3fd9b-124">Einrichten von gRPC-Reflexion</span><span class="sxs-lookup"><span data-stu-id="3fd9b-124">Set up gRPC reflection</span></span>

<span data-ttu-id="3fd9b-125">`grpcurl` muss den Protobuf-Vertrag für Dienste kennen, bevor diese aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-125">`grpcurl` must know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="3fd9b-126">Hierfür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-126">There are two ways to do this:</span></span>

* <span data-ttu-id="3fd9b-127">Richten Sie [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) auf dem Server ein.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-127">Set up [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md) on the server.</span></span> <span data-ttu-id="3fd9b-128">gRPCurl ermittelt automatisch Dienstverträge.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-128">gRPCurl automatically discovers service contracts.</span></span>
* <span data-ttu-id="3fd9b-129">Geben Sie `.proto`-Dateien in Befehlszeilenargumenten für gRPCurl an.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-129">Specify `.proto` files in command-line arguments to gRPCurl.</span></span>

<span data-ttu-id="3fd9b-130">Es ist einfacher, gRPCurl mit gRPC-Reflexion zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-130">It's easier to use gRPCurl with gRPC reflection.</span></span> <span data-ttu-id="3fd9b-131">gRPC-Reflexion fügt der App einen neuen gRPC-Dienst hinzu, der von Clients zum Ermitteln von Diensten aufgerufen werden kann.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-131">gRPC reflection adds a new gRPC service to the app that clients can call to discover services.</span></span>

<span data-ttu-id="3fd9b-132">gRPC ASP.NET Core bietet mit dem [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)-Pakte integrierte Unterstützung für gRPC-Reflexion.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-132">gRPC ASP.NET Core has built-in support for gRPC reflection with the [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="3fd9b-133">So konfigurieren Sie die Reflexion in einer App:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-133">To configure reflection in an app:</span></span>

* <span data-ttu-id="3fd9b-134">Fügen Sie einen `Grpc.AspNetCore.Server.Reflection`-Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-134">Add a `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="3fd9b-135">Registrieren Sie die Reflexion in `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-135">Register reflection in `Startup.cs`:</span></span>
  * <span data-ttu-id="3fd9b-136">`AddGrpcReflection` zum Registrieren von Diensten, die die Reflexion ermöglichen</span><span class="sxs-lookup"><span data-stu-id="3fd9b-136">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="3fd9b-137">`MapGrpcReflectionService` zum Hinzufügen eines Dienstendpunkts der Reflexion.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-137">`MapGrpcReflectionService` to add a reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,15-18)]

<span data-ttu-id="3fd9b-138">Wenn gRPC-Reflexion eingerichtet ist:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-138">When gRPC reflection is set up:</span></span>

* <span data-ttu-id="3fd9b-139">Der Server-App wird ein gRPC-Reflexionsdienst hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-139">A gRPC reflection service is added to the server app.</span></span>
* <span data-ttu-id="3fd9b-140">Client-Apps, die gRPC-Reflexion unterstützen, können den Reflexionsdienst zum Ermitteln von vom Server gehosteten Diensten aufrufen.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-140">Client apps that support gRPC reflection can call the reflection service to discover services hosted by the server.</span></span>
* <span data-ttu-id="3fd9b-141">gRPC-Dienste werden weiterhin vom Client aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-141">gRPC services are still called from the client.</span></span> <span data-ttu-id="3fd9b-142">Reflexion ermöglicht nur Dienstermittlung und umgeht nicht die serverseitige Sicherheit.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-142">Reflection only enables service discovery and doesn't bypass server-side security.</span></span> <span data-ttu-id="3fd9b-143">Endpunkte, die durch [Authentifizierung und Autorisierung](xref:grpc/authn-and-authz) geschützt sind, erfordern für einen erfolgreichen Aufruf, dass der Aufrufer Anmeldeinformationen für den Endpunkt übergibt.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-143">Endpoints protected by [authentication and authorization](xref:grpc/authn-and-authz) require the caller to pass credentials for the endpoint to be called successfully.</span></span>

## <a name="use-grpcurl"></a><span data-ttu-id="3fd9b-144">Verwenden Sie `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="3fd9b-144">Use `grpcurl`</span></span>

<span data-ttu-id="3fd9b-145">Das Argument `-help` erläutert `grpcurl`-Befehlszeilenoptionen:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-145">The `-help` argument explains `grpcurl` command-line options:</span></span>

```console
$ grpcurl -help
```

### <a name="discover-services"></a><span data-ttu-id="3fd9b-146">Ermitteln von Diensten</span><span class="sxs-lookup"><span data-stu-id="3fd9b-146">Discover services</span></span>

<span data-ttu-id="3fd9b-147">Verwenden Sie das Verb `describe`, um die vom Server definierten Dienste anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-147">Use the `describe` verb to view the services defined by the server:</span></span>

```console
$ grpcurl localhost:5001 describe
greet.Greeter is a service:
service Greeter {
  rpc SayHello ( .greet.HelloRequest ) returns ( .greet.HelloReply );
  rpc SayHellos ( .greet.HelloRequest ) returns ( stream .greet.HelloReply );
}
grpc.reflection.v1alpha.ServerReflection is a service:
service ServerReflection {
  rpc ServerReflectionInfo ( stream .grpc.reflection.v1alpha.ServerReflectionRequest ) returns ( stream .grpc.reflection.v1alpha.ServerReflectionResponse );
}
```

<span data-ttu-id="3fd9b-148">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-148">The preceding example:</span></span>

* <span data-ttu-id="3fd9b-149">Führt das Verb `describe` auf Server `localhost:5001` aus.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-149">Runs the `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="3fd9b-150">Druckt von der gRPC-Reflexion zurückgegebene Dienste und Methoden.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-150">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="3fd9b-151">`Greeter` ist ein von der App implementierter Dienst.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-151">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="3fd9b-152">`ServerReflection` ist der vom `Grpc.AspNetCore.Server.Reflection`-Paket hinzugefügter Dienst.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-152">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="3fd9b-153">Kombinieren Sie `describe` mit einem Dienst-, Methoden- oder Meldungsnamen, um die entsprechenden Details anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-153">Combine `describe` with a service, method, or message name to view its detail:</span></span>

```powershell
$ grpcurl localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="3fd9b-154">Aufrufen von gRPC-Diensten</span><span class="sxs-lookup"><span data-stu-id="3fd9b-154">Call gRPC services</span></span>

<span data-ttu-id="3fd9b-155">Rufen Sie einen gRPC-Dienst auf, indem Sie einen Dienst- oder Methodennamen zusammen mit einem JSON-Argument angeben, das die Anforderungsnachricht darstellt.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-155">Call a gRPC service by specifying a service and method name along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="3fd9b-156">Der JSON-Code wird in Protobuf konvertiert und an den Dienst gesendet.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-156">The JSON is converted into Protobuf and sent to the service.</span></span>

```console
$ grpcurl -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="3fd9b-157">Im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-157">In the preceding example:</span></span>

* <span data-ttu-id="3fd9b-158">Das Argument `-d` gibt eine Anforderungsnachricht mit JSON an.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-158">The `-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="3fd9b-159">Dieses Argument muss vor der Serveradresse und dem Methodennamen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-159">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="3fd9b-160">Ruft die `SayHello`-Methode für den `greeter.Greeter`-Dienst auf.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-160">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="3fd9b-161">Druckt die Antwortnachricht als JSON-Code.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-161">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="3fd9b-162">Informationen zu gRPCui</span><span class="sxs-lookup"><span data-stu-id="3fd9b-162">About gRPCui</span></span>

<span data-ttu-id="3fd9b-163">gRPCui ist eine interaktive Webbenutzeroberfläche für gRPC.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-163">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="3fd9b-164">Sie basiert auf gRPCurl und stellt eine grafische Benutzeroberfläche zum Ermitteln und Testen von gRPC-Diensten bereit – vergleichbar mit HTTP-Tools wie Postman oder die Swagger-Benutzeroberfläche.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-164">It builds on top of gRPCurl and offers a GUI for discovering and testing gRPC services, similar to HTTP tools such as Postman or Swagger UI.</span></span>

<span data-ttu-id="3fd9b-165">Informationen zum Herunterladen und Installieren von `grpcui` finden Sie auf der [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="3fd9b-165">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="3fd9b-166">Verwenden von `grpcui`</span><span class="sxs-lookup"><span data-stu-id="3fd9b-166">Using `grpcui`</span></span>

<span data-ttu-id="3fd9b-167">Führen Sie `grpcui` mit der Serveradresse aus, mit der als Argument interagiert wird:</span><span class="sxs-lookup"><span data-stu-id="3fd9b-167">Run `grpcui` with the server address to interact with as an argument:</span></span>

```powershell
$ grpcui localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="3fd9b-168">Das Tool ruft in der interaktiven Webbenutzeroberfläche ein Browserfenster auf.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-168">The tool launches a browser window with the interactive web UI.</span></span> <span data-ttu-id="3fd9b-169">gRPC-Dienste werden mithilfe der gRPC-Reflexion automatisch ermittelt.</span><span class="sxs-lookup"><span data-stu-id="3fd9b-169">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui-Webbenutzeroberfläche](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="3fd9b-171">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="3fd9b-171">Additional resources</span></span>

* [<span data-ttu-id="3fd9b-172">gRPCurl-GitHub-Startseite</span><span class="sxs-lookup"><span data-stu-id="3fd9b-172">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="3fd9b-173">gRPCui-GitHub-Startseite</span><span class="sxs-lookup"><span data-stu-id="3fd9b-173">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [`Grpc.AspNetCore.Server.Reflection`](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
