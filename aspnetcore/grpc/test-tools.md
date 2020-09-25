---
title: Testen von Diensten mit gRPC-Tools
author: jamesnk
description: Hier erfahren Sie, wie sie Dienste mit gRPC-Tools testen. gRPCurl ist ein Befehlszeilentool für die Interaktion mit gRPC-Diensten. gRPCui ist eine interaktive Webbenutzeroberfläche.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/09/2020
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
uid: grpc/test-tools
ms.openlocfilehash: ba51d9b5db2e9fbc7583856d79ab8658eff9b586
ms.sourcegitcommit: a07f83b00db11f32313045b3492e5d1ff83c4437
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2020
ms.locfileid: "90594366"
---
# <a name="test-services-with-grpc-tools"></a><span data-ttu-id="513c6-105">Testen von Diensten mit gRPC-Tools</span><span class="sxs-lookup"><span data-stu-id="513c6-105">Test services with gRPC tools</span></span>

<span data-ttu-id="513c6-106">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="513c6-106">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="513c6-107">Für gRPC gibt es Tools, mit deren Hilfe Entwickler Dienste testen können, ohne Client-Apps zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="513c6-107">Tooling is available for gRPC that allows developers to test services without building client apps.</span></span> <span data-ttu-id="513c6-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) ist ein Befehlszeilentool, das die Interaktion mit gRPC-Diensten ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="513c6-108">[gRPCurl](https://github.com/fullstorydev/grpcurl) is a command-line tool that provides interaction with gRPC services.</span></span> <span data-ttu-id="513c6-109">[gRPCui](https://github.com/fullstorydev/grpcui) stellt eine interaktive Webbenutzeroberfläche für gRPC bereit.</span><span class="sxs-lookup"><span data-stu-id="513c6-109">[gRPCui](https://github.com/fullstorydev/grpcui) adds an interactive web UI for gRPC.</span></span>

<span data-ttu-id="513c6-110">In diesem Artikel wird Folgendes erläutert:</span><span class="sxs-lookup"><span data-stu-id="513c6-110">This article discusses how to:</span></span>

* <span data-ttu-id="513c6-111">Herunterladen und Installieren von gRPCurl und gRPCui</span><span class="sxs-lookup"><span data-stu-id="513c6-111">Download and install gRPCurl and gRPCui.</span></span>
* <span data-ttu-id="513c6-112">Einrichten einer gRPC-Reflexion mit einer gRPC ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="513c6-112">Setup gRPC reflection with a gRPC ASP.NET Core app.</span></span>
* <span data-ttu-id="513c6-113">Ermitteln und Testen von gRPC-Diensten mit `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="513c6-113">Discover and test gRPC services with `grpcurl`.</span></span>
* <span data-ttu-id="513c6-114">Interagieren mit gRPC-Diensten über einen Browser mithilfe von `grpcui`</span><span class="sxs-lookup"><span data-stu-id="513c6-114">Interact with gRPC services via a browser using `grpcui`.</span></span>

## <a name="about-grpcurl"></a><span data-ttu-id="513c6-115">Informationen zu gRPCurl</span><span class="sxs-lookup"><span data-stu-id="513c6-115">About gRPCurl</span></span>

<span data-ttu-id="513c6-116">gRPCurl ist ein von der gRPC-Community erstelltes Befehlszeilentool.</span><span class="sxs-lookup"><span data-stu-id="513c6-116">gRPCurl is a command-line tool created by the gRPC community.</span></span> <span data-ttu-id="513c6-117">Es bietet folgende Features:</span><span class="sxs-lookup"><span data-stu-id="513c6-117">Its features include:</span></span>

* <span data-ttu-id="513c6-118">Aufrufen von gRPC-Diensten wie Streamingdienste</span><span class="sxs-lookup"><span data-stu-id="513c6-118">Calling gRPC services, including streaming services.</span></span>
* <span data-ttu-id="513c6-119">Dienstermittlung mithilfe der [gRPC-Reflexion](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md)</span><span class="sxs-lookup"><span data-stu-id="513c6-119">Service discovery using [gRPC reflection](https://github.com/grpc/grpc/blob/master/doc/server-reflection.md).</span></span>
* <span data-ttu-id="513c6-120">Auflisten und Beschreiben von gRPC-Diensten</span><span class="sxs-lookup"><span data-stu-id="513c6-120">Listing and describing gRPC services.</span></span>
* <span data-ttu-id="513c6-121">Kann mit sicheren Servern (TLS-Servern) und unsicheren (unverschlüsselten) Servern verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="513c6-121">Works with secure (TLS) and insecure (plain-text) servers.</span></span>

<span data-ttu-id="513c6-122">Informationen zum Herunterladen und Installieren von `grpcurl` finden Sie auf der [gRPCurl-GitHub-Startseite](https://github.com/fullstorydev/grpcurl#installation).</span><span class="sxs-lookup"><span data-stu-id="513c6-122">For information about downloading and installing `grpcurl`, see the [gRPCurl GitHub homepage](https://github.com/fullstorydev/grpcurl#installation).</span></span>

## <a name="setup-grpc-reflection"></a><span data-ttu-id="513c6-123">Einrichten einer gRPC-Reflexion</span><span class="sxs-lookup"><span data-stu-id="513c6-123">Setup gRPC reflection</span></span>

<span data-ttu-id="513c6-124">`grpcurl` muss den Protobuf-Vertrag für Dienste kennen, bevor diese aufgerufen werden können.</span><span class="sxs-lookup"><span data-stu-id="513c6-124">`grpcurl` needs to know the Protobuf contract of services before it can call them.</span></span> <span data-ttu-id="513c6-125">Hierfür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="513c6-125">There are two ways to do this:</span></span>

* <span data-ttu-id="513c6-126">Verwenden der gRPC-Reflexion zur Ermittlung von Dienstverträgen</span><span class="sxs-lookup"><span data-stu-id="513c6-126">Use gRPC reflection to discover service contracts.</span></span>
* <span data-ttu-id="513c6-127">Angeben von *PROTO*-Dateien in Befehlszeilenargumenten</span><span class="sxs-lookup"><span data-stu-id="513c6-127">Specify *.proto* files in command-line arguments.</span></span>

<span data-ttu-id="513c6-128">Es ist einfacher, gRPCurl mit gRPC-Reflexion und Dienstermittlung zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="513c6-128">It's easier to use gRPCurl with gRPC reflection and service discovery.</span></span> <span data-ttu-id="513c6-129">gRPC ASP.NET Core bietet mit dem [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)-Pakte integrierte Unterstützung für gRPC-Reflexion.</span><span class="sxs-lookup"><span data-stu-id="513c6-129">gRPC ASP.NET Core has built-in support for gRPC reflection with the [Grpc.AspNetCore.Server.Reflection](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection) package.</span></span> <span data-ttu-id="513c6-130">So konfigurieren Sie die Reflexion in einer App:</span><span class="sxs-lookup"><span data-stu-id="513c6-130">To configure reflection in an app:</span></span>

* <span data-ttu-id="513c6-131">Fügen Sie einen `Grpc.AspNetCore.Server.Reflection`-Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="513c6-131">Add `Grpc.AspNetCore.Server.Reflection` package reference.</span></span>
* <span data-ttu-id="513c6-132">Registrieren Sie die Reflexion in *Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="513c6-132">Register reflection in *Startup.cs*:</span></span>
  * <span data-ttu-id="513c6-133">`AddGrpcReflection` zum Registrieren von Diensten, die die Reflexion ermöglichen</span><span class="sxs-lookup"><span data-stu-id="513c6-133">`AddGrpcReflection` to register services that enable reflection.</span></span>
  * <span data-ttu-id="513c6-134">`MapGrpcReflectionService` zum Hinzufügen des Dienstendpunkts der Reflexion</span><span class="sxs-lookup"><span data-stu-id="513c6-134">`MapGrpcReflectionService` to add reflection service endpoint.</span></span>

[!code-csharp[](~/grpc/test-tools/Startup.cs?name=snippet_1&highlight=4,14)]

## <a name="use-grpcurl"></a><span data-ttu-id="513c6-135">Verwenden Sie `grpcurl`</span><span class="sxs-lookup"><span data-stu-id="513c6-135">Use `grpcurl`</span></span>

<span data-ttu-id="513c6-136">Das Argument `-help` erläutert `grpcurl`-Befehlszeilenoptionen:</span><span class="sxs-lookup"><span data-stu-id="513c6-136">The `-help` argument explains `grpcurl` command-line options:</span></span>

```powershell
> grpcurl.exe -help
```

### <a name="discover-services"></a><span data-ttu-id="513c6-137">Ermitteln von Diensten</span><span class="sxs-lookup"><span data-stu-id="513c6-137">Discover services</span></span>

<span data-ttu-id="513c6-138">Verwenden Sie das Verb `describe`, um die vom Server definierten Dienste anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="513c6-138">Use the `describe` verb to view the services defined by the server:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe
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

<span data-ttu-id="513c6-139">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="513c6-139">The preceding example:</span></span>

* <span data-ttu-id="513c6-140">Führt das Verb `describe` auf Server `localhost:5001` aus.</span><span class="sxs-lookup"><span data-stu-id="513c6-140">Runs `describe` verb on server `localhost:5001`.</span></span>
* <span data-ttu-id="513c6-141">Druckt von der gRPC-Reflexion zurückgegebene Dienste und Methoden.</span><span class="sxs-lookup"><span data-stu-id="513c6-141">Prints services and methods returned by gRPC reflection.</span></span>
  * <span data-ttu-id="513c6-142">`Greeter` ist ein von der App implementierter Dienst.</span><span class="sxs-lookup"><span data-stu-id="513c6-142">`Greeter` is a service implemented by the app.</span></span>
  * <span data-ttu-id="513c6-143">`ServerReflection` ist der vom `Grpc.AspNetCore.Server.Reflection`-Paket hinzugefügter Dienst.</span><span class="sxs-lookup"><span data-stu-id="513c6-143">`ServerReflection` is the service added by the `Grpc.AspNetCore.Server.Reflection` package.</span></span>

<span data-ttu-id="513c6-144">Kombinieren Sie `describe` mit einem Dienst-, Methoden- oder Meldungsnamen, um die entsprechenden Details anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="513c6-144">Combine `describe` with a service, method or message name to view its detail:</span></span>

```powershell
> grpcurl.exe localhost:5001 describe greet.HelloRequest
greet.HelloRequest is a message:
message HelloRequest {
  string name = 1;
}
```

### <a name="call-grpc-services"></a><span data-ttu-id="513c6-145">Aufrufen von gRPC-Diensten</span><span class="sxs-lookup"><span data-stu-id="513c6-145">Call gRPC services</span></span>

<span data-ttu-id="513c6-146">Rufen Sie einen gRPC-Dienst auf, indem Sie einen Dienst- oder Methodennamen zusammen mit einem JSON-Argument angeben, das die Anforderungsnachricht darstellt.</span><span class="sxs-lookup"><span data-stu-id="513c6-146">Call a gRPC service by specifying a service and method name, along with a JSON argument that represents the request message.</span></span> <span data-ttu-id="513c6-147">Der JSON-Code wird in Protobuf konvertiert und an den Dienst gesendet.</span><span class="sxs-lookup"><span data-stu-id="513c6-147">The JSON is converted into Protobuf and sent to the service.</span></span>

```powershell
> grpcurl.exe -d '{ \"name\": \"World\" }' localhost:5001 greet.Greeter/SayHello
{
  "message": "Hello World"
}
```

<span data-ttu-id="513c6-148">Für das vorherige Beispiel gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="513c6-148">The preceding example:</span></span>

* <span data-ttu-id="513c6-149">Das Argument `-d` gibt eine Anforderungsnachricht mit JSON an.</span><span class="sxs-lookup"><span data-stu-id="513c6-149">`-d` argument specifies a request message with JSON.</span></span> <span data-ttu-id="513c6-150">Dieses Argument muss vor der Serveradresse und dem Methodennamen angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="513c6-150">This argument must come before the server address and method name.</span></span>
* <span data-ttu-id="513c6-151">Ruft die `SayHello`-Methode für den `greeter.Greeter`-Dienst auf.</span><span class="sxs-lookup"><span data-stu-id="513c6-151">Calls the `SayHello` method on the `greeter.Greeter` service.</span></span>
* <span data-ttu-id="513c6-152">Druckt die Antwortnachricht als JSON-Code.</span><span class="sxs-lookup"><span data-stu-id="513c6-152">Prints the response message as JSON.</span></span>

## <a name="about-grpcui"></a><span data-ttu-id="513c6-153">Informationen zu gRPCui</span><span class="sxs-lookup"><span data-stu-id="513c6-153">About gRPCui</span></span>

<span data-ttu-id="513c6-154">gRPCui ist eine interaktive Webbenutzeroberfläche für gRPC.</span><span class="sxs-lookup"><span data-stu-id="513c6-154">gRPCui is an interactive web UI for gRPC.</span></span> <span data-ttu-id="513c6-155">Es basiert auf gRPCurl und stellt eine grafische Benutzeroberfläche zum Ermitteln und Testen von gRPC-Diensten bereit – vergleichbar mit Postman bei HTTP-Tools.</span><span class="sxs-lookup"><span data-stu-id="513c6-155">It builds on top of gRPCurl, and offers a GUI for discovering and testing gRPC services, similar to HTTP tools like Postman.</span></span>

<span data-ttu-id="513c6-156">Informationen zum Herunterladen und Installieren von `grpcui` finden Sie auf der [gRPCui-GitHub-Startseite](https://github.com/fullstorydev/grpcui#installation).</span><span class="sxs-lookup"><span data-stu-id="513c6-156">For information about downloading and installing `grpcui`, see the [gRPCui GitHub homepage](https://github.com/fullstorydev/grpcui#installation).</span></span>

## <a name="using-grpcui"></a><span data-ttu-id="513c6-157">Verwenden von `grpcui`</span><span class="sxs-lookup"><span data-stu-id="513c6-157">Using `grpcui`</span></span>

<span data-ttu-id="513c6-158">Führen Sie `grpcui` aus, wobei mit der Serveradresse als Argument interagiert wird.</span><span class="sxs-lookup"><span data-stu-id="513c6-158">Run `grpcui` with the server address to interact with as an argument.</span></span>

```powershell
> grpcui.exe localhost:5001
gRPC Web UI available at http://127.0.0.1:55038/
```

<span data-ttu-id="513c6-159">Das Tool ruft in der interaktiven Webbenutzeroberfläche ein Browserfenster auf.</span><span class="sxs-lookup"><span data-stu-id="513c6-159">The tool will launch a browser window with the interactive web UI.</span></span> <span data-ttu-id="513c6-160">gRPC-Dienste werden mithilfe der gRPC-Reflexion automatisch ermittelt.</span><span class="sxs-lookup"><span data-stu-id="513c6-160">gRPC services are automatically discovered using gRPC reflection.</span></span>

![gRPCui-Webbenutzeroberfläche](~/grpc/test-tools/static/grpcui.png)

## <a name="additional-resources"></a><span data-ttu-id="513c6-162">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="513c6-162">Additional resources</span></span>

* [<span data-ttu-id="513c6-163">gRPCurl-GitHub-Startseite</span><span class="sxs-lookup"><span data-stu-id="513c6-163">gRPCurl GitHub homepage</span></span>](https://github.com/fullstorydev/grpcurl)
* [<span data-ttu-id="513c6-164">gRPCui-GitHub-Startseite</span><span class="sxs-lookup"><span data-stu-id="513c6-164">gRPCui GitHub homepage</span></span>](https://github.com/fullstorydev/grpcui)
* [<span data-ttu-id="513c6-165">Grpc.AspNetCore.Server.Reflection</span><span class="sxs-lookup"><span data-stu-id="513c6-165">Grpc.AspNetCore.Server.Reflection</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server.Reflection)
