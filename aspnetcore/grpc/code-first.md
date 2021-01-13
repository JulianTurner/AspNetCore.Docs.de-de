---
title: Code-First-gRPC-Dienste und -Clients mit .NET
author: jamesnk
description: Hier erlernen Sie die grundlegenden Konzepte beim Schreiben von Code-First-gRPC mit .NET.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/04/2021
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
uid: grpc/code-first
ms.openlocfilehash: 6856770a57d900a4953dad294236cb4d08479d9d
ms.sourcegitcommit: 53e01d6e9b70a18a05618f0011cf115a16633c21
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97880618"
---
# <a name="code-first-grpc-services-and-clients-with-net"></a><span data-ttu-id="a5d30-103">Code-First-gRPC-Dienste und -Clients mit .NET</span><span class="sxs-lookup"><span data-stu-id="a5d30-103">Code-first gRPC services and clients with .NET</span></span>

<span data-ttu-id="a5d30-104">Von [James Newton-King](https://twitter.com/jamesnk) und [Marc Gravell](https://twitter.com/marcgravell)</span><span class="sxs-lookup"><span data-stu-id="a5d30-104">By [James Newton-King](https://twitter.com/jamesnk) and [Marc Gravell](https://twitter.com/marcgravell)</span></span>

<span data-ttu-id="a5d30-105">Code-First-gRPC verwendet .NET-Typen, um Dienst- und Nachrichtenverträge zu definieren.</span><span class="sxs-lookup"><span data-stu-id="a5d30-105">Code-first gRPC uses .NET types to define service and message contracts.</span></span>

<span data-ttu-id="a5d30-106">Code-First ist eine gute Wahl, wenn ein gesamtes System .NET verwendet:</span><span class="sxs-lookup"><span data-stu-id="a5d30-106">Code-first is a good choice when an entire system uses .NET:</span></span>

* <span data-ttu-id="a5d30-107">.NET-Dienst- und -Datenvertragstypen können von .NET-Server und -Clients gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="a5d30-107">.NET service and data contract types can be shared between the .NET server and clients.</span></span>
* <span data-ttu-id="a5d30-108">Verhindert, dass Verträge in `.proto`-Dateien und Codegenerierung definiert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="a5d30-108">Avoids the need to define contracts in `.proto` files and code generation.</span></span>

<span data-ttu-id="a5d30-109">Code-First sollte nicht in mehrsprachigen Systemen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a5d30-109">Code-first isn't recommended in polyglot systems with multiple languages.</span></span> <span data-ttu-id="a5d30-110">.NET-Dienst- und -Datenvertragstypen können nicht mit Nicht-.NET-Plattformen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a5d30-110">.NET service and data contract types can't be used with non-.NET platforms.</span></span> <span data-ttu-id="a5d30-111">Um einen mit Code-First geschriebenen gRPC-Dienst aufzurufen, müssen andere Plattformen einen `.proto`-Vertrag erstellen, der mit dem Dienst übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="a5d30-111">To call a gRPC service written using code-first, other platforms must create a `.proto` contract that matches the service.</span></span>

## <a name="protobuf-netgrpc"></a><span data-ttu-id="a5d30-112">protobuf-net.Grpc</span><span class="sxs-lookup"><span data-stu-id="a5d30-112">protobuf-net.Grpc</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a5d30-113">Hilfe zu protobuf-net.Grpc finden Sie auf der [protobuf-net.Grpc-Website](https://protobuf-net.github.io/protobuf-net.Grpc/), oder erstellen Sie ein Problem im [protobuf-net.Grpc-GitHub-Repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span><span class="sxs-lookup"><span data-stu-id="a5d30-113">For help with protobuf-net.Grpc, visit the [protobuf-net.Grpc website](https://protobuf-net.github.io/protobuf-net.Grpc/) or create an issue on the [protobuf-net.Grpc GitHub repository](https://github.com/protobuf-net/protobuf-net.Grpc).</span></span>

<span data-ttu-id="a5d30-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) ist ein Communityprojekt und wird von Microsoft nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a5d30-114">[protobuf-net.Grpc](https://protobuf-net.github.io/protobuf-net.Grpc/) is a community project and isn't supported by Microsoft.</span></span> <span data-ttu-id="a5d30-115">Die Code-First-Unterstützung wird `Grpc.AspNetCore` und `Grpc.Net.Client` hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a5d30-115">It adds code-first support to `Grpc.AspNetCore` and `Grpc.Net.Client`.</span></span> <span data-ttu-id="a5d30-116">Es werden mit Attributen versehene .NET-Typen verwendet, um die gRPC-Dienste und -Nachrichten einer App zu definieren.</span><span class="sxs-lookup"><span data-stu-id="a5d30-116">It uses .NET types annotated with attributes to define an app's gRPC services and messages.</span></span>

<span data-ttu-id="a5d30-117">Der erste Schritt beim Erstellen eines Code-First-gRPC-Diensts ist die Definition des Codevertrags:</span><span class="sxs-lookup"><span data-stu-id="a5d30-117">The first step to creating a code-first gRPC service is defining the code contract:</span></span>

* <span data-ttu-id="a5d30-118">Erstellen Sie ein neues Projekt, das von Server und Client gemeinsam genutzt wird.</span><span class="sxs-lookup"><span data-stu-id="a5d30-118">Create a new project that will be shared by the server and client.</span></span>
* <span data-ttu-id="a5d30-119">Fügen Sie einen [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc)-Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-119">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="a5d30-120">Erstellen Sie Dienst- und Datenvertragstypen.</span><span class="sxs-lookup"><span data-stu-id="a5d30-120">Create service and data contract types.</span></span>

[!code-csharp[](code-first/Contracts.cs)]

<span data-ttu-id="a5d30-121">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="a5d30-121">The preceding code:</span></span>

* <span data-ttu-id="a5d30-122">Definiert `HelloRequest`- und `HelloReply`-Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="a5d30-122">Defines `HelloRequest` and `HelloReply` messages.</span></span>
* <span data-ttu-id="a5d30-123">Definiert die `IGreeterService`-Vertragsschnittstelle mit der unären `SayHelloAsync`-gRPC-Methode.</span><span class="sxs-lookup"><span data-stu-id="a5d30-123">Defines the `IGreeterService` contract interface with the unary `SayHelloAsync` gRPC method.</span></span>

<span data-ttu-id="a5d30-124">Der Dienstvertrag wird auf dem Server implementiert und vom Client aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a5d30-124">The service contract is implemented on the server and called from the client.</span></span> <span data-ttu-id="a5d30-125">Auf Dienstschnittstellen definierte Methoden müssen mit bestimmten Signaturen identisch sein, in Abhängigkeit davon, ob sie unär, Serverstreaming, Clientstreaming oder bidirektionales Streaming sind.</span><span class="sxs-lookup"><span data-stu-id="a5d30-125">Methods defined on service interfaces must match certain signatures depending on whether they're unary, server streaming, client streaming, or bidirectional streaming.</span></span>

<span data-ttu-id="a5d30-126">Weitere Informationen zum Definieren von Dienstverträgen finden Sie in der [Dokumentation zu den ersten Schritten mit protobuf-net.GrpC](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span><span class="sxs-lookup"><span data-stu-id="a5d30-126">For more information on defining service contracts, see the [protobuf-net.Grpc getting started documentation](https://protobuf-net.github.io/protobuf-net.Grpc/gettingstarted).</span></span>

## <a name="create-a-code-first-grpc-service"></a><span data-ttu-id="a5d30-127">Erstellen eines Code-First-gRPC-Diensts</span><span class="sxs-lookup"><span data-stu-id="a5d30-127">Create a code-first gRPC service</span></span>

<span data-ttu-id="a5d30-128">So fügen Sie einen gRPC-Code-First-Dienst einer ASP.NET Core-App hinzu:</span><span class="sxs-lookup"><span data-stu-id="a5d30-128">To add gRPC code-first service to an ASP.NET Core app:</span></span>

* <span data-ttu-id="a5d30-129">Fügen Sie einen [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore)-Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-129">Add a [protobuf-net.Grpc.AspNetCore](https://www.nuget.org/packages/protobuf-net.Grpc.AspNetCore) package reference.</span></span>
* <span data-ttu-id="a5d30-130">Fügen Sie dem freigegebenen Code-Vertragsprojekt einen Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-130">Add a reference to the shared code-contract project.</span></span>

<span data-ttu-id="a5d30-131">Erstellen Sie eine neue `GreeterService.cs`-Datei, und implementieren Sie die `IGreeterService`-Dienstschnittstelle:</span><span class="sxs-lookup"><span data-stu-id="a5d30-131">Create a new `GreeterService.cs` file and implement the `IGreeterService` service interface:</span></span>

[!code-csharp[](code-first/GreeterService.cs?highlight=1)]

<span data-ttu-id="a5d30-132">Aktualisieren Sie die Datei `Startup.cs`:</span><span class="sxs-lookup"><span data-stu-id="a5d30-132">Update the `Startup.cs` file:</span></span>

[!code-csharp[](code-first/Startup.cs?highlight=3,17)]

<span data-ttu-id="a5d30-133">Im obigen Code:</span><span class="sxs-lookup"><span data-stu-id="a5d30-133">In the preceding code:</span></span>

* <span data-ttu-id="a5d30-134">`AddCodeFirstGrpc` registriert Dienste, die Code-First aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a5d30-134">`AddCodeFirstGrpc` registers services that enable code-first.</span></span>
* <span data-ttu-id="a5d30-135">`MapGrpcService<GreeterService>` fügt den Code-First-Dienstendpunkt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-135">`MapGrpcService<GreeterService>` adds the code-first service endpoint.</span></span>

<span data-ttu-id="a5d30-136">Mit Code-First und `.proto`-Dateien implementierte gRPC-Dienste können in der gleichen App nebeneinander vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="a5d30-136">gRPC services implemented with code-first and `.proto` files can co-exist in the same app.</span></span> <span data-ttu-id="a5d30-137">Für alle gRPC-Dienste wird die [gRPC-Dienstkonfiguration](xref:grpc/configuration#configure-services-options) verwendet.</span><span class="sxs-lookup"><span data-stu-id="a5d30-137">All gRPC services use [gRPC service configuration](xref:grpc/configuration#configure-services-options).</span></span>

## <a name="create-a-code-first-grpc-client"></a><span data-ttu-id="a5d30-138">Erstellen eines Code-First-gRPC-Clients</span><span class="sxs-lookup"><span data-stu-id="a5d30-138">Create a code-first gRPC client</span></span>

<span data-ttu-id="a5d30-139">Ein Code-First-gRPC-Client verwendet den Dienstvertrag, um gRPC-Dienste aufzurufen.</span><span class="sxs-lookup"><span data-stu-id="a5d30-139">A code-first gRPC client uses the service contract to call gRPC services.</span></span> <span data-ttu-id="a5d30-140">So rufen Sie einen gRPC-Dienst mit einem Code-First-Client auf:</span><span class="sxs-lookup"><span data-stu-id="a5d30-140">To call a gRPC service using a code-first client:</span></span>

* <span data-ttu-id="a5d30-141">Fügen Sie einen [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc)-Paketverweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-141">Add a [protobuf-net.Grpc](https://www.nuget.org/packages/protobuf-net.Grpc) package reference.</span></span>
* <span data-ttu-id="a5d30-142">Fügen Sie dem freigegebenen Code-Vertragsprojekt einen Verweis hinzu.</span><span class="sxs-lookup"><span data-stu-id="a5d30-142">Add a reference to the shared code-contract project.</span></span>

[!code-csharp[](code-first/Program.cs?highlight=2,4-5)]

<span data-ttu-id="a5d30-143">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="a5d30-143">The preceding code:</span></span>

* <span data-ttu-id="a5d30-144">Erstellt einen gRPC-Kanal.</span><span class="sxs-lookup"><span data-stu-id="a5d30-144">Creates a gRPC channel.</span></span>
* <span data-ttu-id="a5d30-145">Erstellt einen Code-First-Client aus dem Kanal mit der `CreateGrpcService<IGreeterService>`-Erweiterungsmethode.</span><span class="sxs-lookup"><span data-stu-id="a5d30-145">Creates a code-first client from the channel with the `CreateGrpcService<IGreeterService>` extension method.</span></span>
* <span data-ttu-id="a5d30-146">Ruft den gRPC-Dienst mit `SayHelloAsync` auf.</span><span class="sxs-lookup"><span data-stu-id="a5d30-146">Calls the gRPC service with `SayHelloAsync`.</span></span>

<span data-ttu-id="a5d30-147">Ein Code-First-gRPC-Client wird aus einem Kanal erstellt.</span><span class="sxs-lookup"><span data-stu-id="a5d30-147">A code-first gRPC client is created from a channel.</span></span> <span data-ttu-id="a5d30-148">Genau wie ein regulärer Client verwendet ein Code-First-Client seine [Kanalkonfiguration](xref:grpc/configuration#configure-client-options).</span><span class="sxs-lookup"><span data-stu-id="a5d30-148">Just like a regular client, a code-first client uses its [channel configuration](xref:grpc/configuration#configure-client-options).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a5d30-149">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a5d30-149">Additional resources</span></span>

* [<span data-ttu-id="a5d30-150">protobuf-net.Grpc-Website</span><span class="sxs-lookup"><span data-stu-id="a5d30-150">protobuf-net.Grpc website</span></span>](https://protobuf-net.github.io/protobuf-net.Grpc/)
* [<span data-ttu-id="a5d30-151">protobuf-net.GrpC-GitHub-Repository</span><span class="sxs-lookup"><span data-stu-id="a5d30-151">protobuf-net.Grpc GitHub repository</span></span>](https://github.com/protobuf-net/protobuf-net.Grpc)
