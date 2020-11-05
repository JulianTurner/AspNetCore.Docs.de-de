---
title: Gründe für die Migration von WCF zu ASP.NET Core gRPC
author: markrendle
description: Dieser Artikel enthält eine Zusammenfassung der Gründe, warum ASP.NET Core gRPC für WCF-Entwickler (Windows Communication Foundation) geeignet ist, die zu modernen Architekturen und Plattformen migrieren möchten.
monikerRange: '>= aspnetcore-3.0'
ms.author: wpickett
ms.date: 09/02/2020
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
uid: grpc/wcf
ms.openlocfilehash: 26629b4aa5510f4ef5f53f57b64e45f6c32d4014
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058687"
---
# <a name="grpc-for-windows-communication-foundation-wcf-developers"></a><span data-ttu-id="40c37-103">gRPC für WCF-Entwickler (Windows Communication Foundation)</span><span class="sxs-lookup"><span data-stu-id="40c37-103">gRPC for Windows Communication Foundation (WCF) developers</span></span>

<span data-ttu-id="40c37-104">Dieser Artikel enthält eine Zusammenfassung der Gründe, warum ASP.NET Core gRPC für WCF-Entwickler (Windows Communication Foundation) geeignet ist, die zu modernen Architekturen und Plattformen migrieren möchten.</span><span class="sxs-lookup"><span data-stu-id="40c37-104">This article provides a summary of why ASP.NET Core gRPC is a good fit for Windows Communication Foundation (WCF) developers who want to migrate to modern architectures and platforms.</span></span>

## <a name="comparison-to-wcf"></a><span data-ttu-id="40c37-105">Vergleich mit WCF</span><span class="sxs-lookup"><span data-stu-id="40c37-105">Comparison to WCF</span></span>

<span data-ttu-id="40c37-106">Implementierung und Ansatz für gRPC sind zwar unterschiedlich, die Entwicklung und Nutzung von Diensten mit gRPC sollte für WCF-Entwickler jedoch intuitiv sein.</span><span class="sxs-lookup"><span data-stu-id="40c37-106">Although the implementation and approach are different for gRPC, the experience of developing and consuming services with gRPC should be intuitive for WCF developers.</span></span> <span data-ttu-id="40c37-107">Bei WCF und gRPC handelt es sich um RPC-Frameworks (Remote Procedure Call, Remoteprozeduraufruf) mit denselben Zielen:</span><span class="sxs-lookup"><span data-stu-id="40c37-107">WCF and gRPC are RPC (remote procedure call) frameworks with the same goals:</span></span>

* <span data-ttu-id="40c37-108">Ermöglichen einer Codierung als würden sich Client und Server auf derselben Plattform befinden</span><span class="sxs-lookup"><span data-stu-id="40c37-108">Make it possible to code as though the client and server are on the same platform.</span></span>
* <span data-ttu-id="40c37-109">Bereitstellen einer vereinfachten portierbaren Netzwerk-API</span><span class="sxs-lookup"><span data-stu-id="40c37-109">Provide a simplified portable networking API.</span></span>

<span data-ttu-id="40c37-110">Beide Plattformen haben die Anforderung, eine Schnittstelle zu deklarieren und zu implementieren, wobei jedoch der Prozess zum Deklarieren der Schnittstelle unterschiedlich ist.</span><span class="sxs-lookup"><span data-stu-id="40c37-110">Both platforms share the requirement of declaring and implementing an interface, although the process for declaring the interface is different.</span></span> <span data-ttu-id="40c37-111">Die zahlreichen Arten von RPC-Aufrufen, die von gRPC unterstützt werden, entsprechen den für WCF-Dienste verfügbaren Bindungen.</span><span class="sxs-lookup"><span data-stu-id="40c37-111">The many types of RPC calls that gRPC supports map well to the bindings available to WCF services.</span></span> <span data-ttu-id="40c37-112">Weitere Informationen und Beispiele finden Sie unter [Migrieren einer WCF-Lösung zu gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span><span class="sxs-lookup"><span data-stu-id="40c37-112">For more information and examples, see [Migrate a WCF solution to gRPC](/dotnet/architecture/grpc-for-wcf-developers/migrate-wcf-to-grpc).</span></span>

## <a name="benefits-of-grpc"></a><span data-ttu-id="40c37-113">Vorteile von gRPC</span><span class="sxs-lookup"><span data-stu-id="40c37-113">Benefits of gRPC</span></span>

<span data-ttu-id="40c37-114">gRPC bietet aus folgenden Gründen ein besseres Framework als andere Ansätze.</span><span class="sxs-lookup"><span data-stu-id="40c37-114">gRPC provides a better framework than other approaches for the following reasons.</span></span>

### <a name="performance"></a><span data-ttu-id="40c37-115">Leistung</span><span class="sxs-lookup"><span data-stu-id="40c37-115">Performance</span></span>

<span data-ttu-id="40c37-116">gRPC verwendet HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="40c37-116">gRPC uses HTTP/2.</span></span> <span data-ttu-id="40c37-117">Im Vergleich zu HTTP/1.1:</span><span class="sxs-lookup"><span data-stu-id="40c37-117">In contrast to HTTP/1.1, HTTP/2:</span></span>

* <span data-ttu-id="40c37-118">Ist HTTP/2 ein kleineres, schnelleres binäres Protokoll.</span><span class="sxs-lookup"><span data-stu-id="40c37-118">Is a smaller, faster binary protocol.</span></span>
* <span data-ttu-id="40c37-119">Lässt sich HTTP/2 von Computern effizienter analysieren.</span><span class="sxs-lookup"><span data-stu-id="40c37-119">Is more efficient for computers to parse.</span></span>
* <span data-ttu-id="40c37-120">Unterstützt HTTP/2 das Multiplexing von Anforderungen über eine einzelne Verbindung.</span><span class="sxs-lookup"><span data-stu-id="40c37-120">Supports multiplexing requests over a single connection.</span></span> <span data-ttu-id="40c37-121">Dank Multiplexing können mehrere Anforderungen über eine Verbindung gesendet werden, ohne dass Anforderungen gegenseitig blockiert werden.</span><span class="sxs-lookup"><span data-stu-id="40c37-121">Multiplexing enables multiple requests to be sent over one connection without requests blocking each other.</span></span> <span data-ttu-id="40c37-122">Bei HTTP/1.1 wird die Blockierung als HOL-Blockierung (Head-of-Line) bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="40c37-122">In HTTP/1.1, the blocking is known as "head-of-line (HOL) blocking."</span></span>

<span data-ttu-id="40c37-123">gRPC verwendet Protobuf. Hierbei handelt es sich um ein effizientes Binärformat zum Serialisieren von Meldungen.</span><span class="sxs-lookup"><span data-stu-id="40c37-123">gRPC uses Protobuf, an efficient binary format, to serialize messages.</span></span> <span data-ttu-id="40c37-124">Protobuf-Meldungen:</span><span class="sxs-lookup"><span data-stu-id="40c37-124">Protobuf messages are:</span></span>
* <span data-ttu-id="40c37-125">Lassen sich schnell Serialisieren und Deserialisieren.</span><span class="sxs-lookup"><span data-stu-id="40c37-125">Fast to serialize and deserialize.</span></span>
* <span data-ttu-id="40c37-126">Verwenden weniger Bandbreite als textbasierte Formate.</span><span class="sxs-lookup"><span data-stu-id="40c37-126">Use less bandwidth than text-based formats.</span></span> 

<span data-ttu-id="40c37-127">gRPC ist eine gute Lösung für mobile Geräte und Netzwerke mit Bandbreiteneinschränkungen.</span><span class="sxs-lookup"><span data-stu-id="40c37-127">gRPC is a good solution for mobile devices and networks with bandwidth restrictions.</span></span>

### <a name="interoperability"></a><span data-ttu-id="40c37-128">Interoperabilität</span><span class="sxs-lookup"><span data-stu-id="40c37-128">Interoperability</span></span>

<span data-ttu-id="40c37-129">gRPC-Tools und -Bibliotheken gibt es für alle wichtigen Programmiersprachen und Plattformen wie .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby und PHP.</span><span class="sxs-lookup"><span data-stu-id="40c37-129">There are gRPC tools and libraries for all major programming languages and platforms, including .NET, Java, Python, Go, C++, Node.js, Swift, Dart, Ruby, and PHP.</span></span> <span data-ttu-id="40c37-130">Dank dem binären Übertragungsformat Protobuf und der effizienten Codegenerierung für die einzelnen Plattformen können Entwickler plattformübergreifende, leistungsfähige Apps erstellen.</span><span class="sxs-lookup"><span data-stu-id="40c37-130">Thanks to the Protobuf binary wire format and the efficient code generation for each platform, developers can build cross-platform, performant apps.</span></span>

### <a name="usability-and-productivity"></a><span data-ttu-id="40c37-131">Nutzbarkeit und Produktivität</span><span class="sxs-lookup"><span data-stu-id="40c37-131">Usability and productivity</span></span>

<span data-ttu-id="40c37-132">gRPC ist eine umfassende RPC-Lösung.</span><span class="sxs-lookup"><span data-stu-id="40c37-132">gRPC is a comprehensive RPC solution.</span></span> <span data-ttu-id="40c37-133">Sie funktioniert konsistent über mehrere Sprachen und Plattformen hinweg.</span><span class="sxs-lookup"><span data-stu-id="40c37-133">It works consistently across multiple languages and platforms.</span></span> <span data-ttu-id="40c37-134">Zudem bietet sie auch hervorragende Tools, bei denen ein Großteil der Codebausteine automatisch generiert wird.</span><span class="sxs-lookup"><span data-stu-id="40c37-134">It also provides excellent tooling, with much of the boilerplate code automatically generated.</span></span> <span data-ttu-id="40c37-135">Wie bei WCF werden bei gRPC Meldungen und ein stark typisierter Client automatisch generiert.</span><span class="sxs-lookup"><span data-stu-id="40c37-135">Like WCF, gRPC automatically generates messages and a strongly typed client.</span></span> <span data-ttu-id="40c37-136">Somit bleibt Entwicklern mehr Zeit, sich auf die Geschäftslogik zu konzentrieren.</span><span class="sxs-lookup"><span data-stu-id="40c37-136">Developer time is freed up to focus on business logic.</span></span>

### <a name="streaming"></a><span data-ttu-id="40c37-137">Streaming</span><span class="sxs-lookup"><span data-stu-id="40c37-137">Streaming</span></span>

<span data-ttu-id="40c37-138">gRPC arbeitet mit einem rein bidirektionalen Streaming, das den Vollduplex-Diensten von WCF ähnliche Funktionen bietet.</span><span class="sxs-lookup"><span data-stu-id="40c37-138">gRPC has full bidirectional streaming, which provides similar functionality to WCF's full duplex services.</span></span> <span data-ttu-id="40c37-139">gRPC-Streaming kann über reguläre Internetverbindungen, Lastenausgleichsmodule und Dienstnetze betrieben werden.</span><span class="sxs-lookup"><span data-stu-id="40c37-139">gRPC streaming can operate over regular internet connections, load balancers, and service meshes.</span></span>

### <a name="deadlines-timeouts-and-cancellation"></a><span data-ttu-id="40c37-140">Fristen, Zeitlimits und Abbruch</span><span class="sxs-lookup"><span data-stu-id="40c37-140">Deadlines, timeouts, and cancellation</span></span>

<span data-ttu-id="40c37-141">Mit gRPC können Clients eine maximale Zeit für die Beendigung eines RPC angeben.</span><span class="sxs-lookup"><span data-stu-id="40c37-141">gRPC allows clients to specify a maximum time for an RPC to finish.</span></span> <span data-ttu-id="40c37-142">Wenn die angegebene Frist überschritten wird, kann der Server den Vorgang unabhängig vom Client abbrechen.</span><span class="sxs-lookup"><span data-stu-id="40c37-142">If the specified deadline is exceeded, the server can cancel the operation independently of the client.</span></span> <span data-ttu-id="40c37-143">Fristen und Abbrüche können durch nachfolgende gRPC-Aufrufe weitergegeben werden, um für Ressourcen Nutzungslimits zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="40c37-143">Deadlines and cancellations can be propagated through subsequent gRPC calls to help enforce resource usage limits.</span></span> <span data-ttu-id="40c37-144">Clients können Vorgänge beenden, wenn eine Frist überschritten wird, oder ggf. auch früher.</span><span class="sxs-lookup"><span data-stu-id="40c37-144">Clients can stop operations when a deadline is exceeded, or earlier if necessary.</span></span> <span data-ttu-id="40c37-145">So können Clients beispielsweise Vorgänge aufgrund einer Benutzerinteraktion abbrechen.</span><span class="sxs-lookup"><span data-stu-id="40c37-145">For example, clients can stop operations because of a user interaction.</span></span>

### <a name="security"></a><span data-ttu-id="40c37-146">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="40c37-146">Security</span></span>

<span data-ttu-id="40c37-147">gRPC kann TLS und HTTP/2 verwenden, um eine verschlüsselte End-to-End-Verbindung zwischen dem Client und dem Server bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="40c37-147">gRPC can use TLS and HTTP/2 to provide an end-to-end encrypted connection between the client and the server.</span></span> <span data-ttu-id="40c37-148">Durch die Unterstützung der Clientzertifikatsauthentifizierung wird die Sicherheit und Vertrauensstellung zwischen Client und Server verbessert.</span><span class="sxs-lookup"><span data-stu-id="40c37-148">Support for client certificate authentication further increases security and trust between client and server.</span></span>

## <a name="grpc-as-a-migration-path-for-wcf-to-net-core-and-net-5"></a><span data-ttu-id="40c37-149">gRPC als Migrationspfad für WCF zu .NET Core und .NET 5</span><span class="sxs-lookup"><span data-stu-id="40c37-149">gRPC as a migration path for WCF to .NET Core and .NET 5</span></span>

<span data-ttu-id="40c37-150">.NET Core und .NET 5 kennzeichnen insofern einen Wechsel, als dass Microsoft Remote-Kommunikationslösungen für Entwickler bereitstellt, die Dienste auf verschiedenen Plattformen bereitstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="40c37-150">.NET Core and .NET 5 marks a shift in the way that Microsoft delivers remote communication solutions to developers who want to deliver services across a range of platforms.</span></span> <span data-ttu-id="40c37-151">.NET Core und .NET 5 unterstützen das [Aufrufen von WCF-Diensten](/dotnet/core/additional-tools/wcf-web-service-reference-guide), bieten aber keine serverseitige Unterstützung für das Hosting von WCF.</span><span class="sxs-lookup"><span data-stu-id="40c37-151">.NET Core and .NET 5 support [calling WCF services](/dotnet/core/additional-tools/wcf-web-service-reference-guide), but won't offer server-side support for hosting WCF.</span></span>

<span data-ttu-id="40c37-152">Es gibt zwei empfohlene Pfade für die Modernisierung von WCF-Apps:</span><span class="sxs-lookup"><span data-stu-id="40c37-152">There are two recommended paths for modernizing WCF apps:</span></span>

* <span data-ttu-id="40c37-153">gRPC basiert auf modernen Technologien und wird in der Entwicklercommunity für RPC-Apps bevorzugt verwendet.</span><span class="sxs-lookup"><span data-stu-id="40c37-153">gRPC is built on modern technologies and has emerged as the most popular choice across the developer community for RPC apps.</span></span> <span data-ttu-id="40c37-154">Ab .NET Core 3.0 verfügen moderne .NET-Plattformen über eine ausgezeichnete Unterstützung für gRPC.</span><span class="sxs-lookup"><span data-stu-id="40c37-154">Starting with .NET Core 3.0, modern .NET platforms have excellent support for gRPC.</span></span> <span data-ttu-id="40c37-155">Wenn Sie WCF-Dienste migrieren, sodass gRPC verwendet werden kann, können Sie die RPC-Features, die Leistung und die Interoperabilität bereitstellen, die in modernen Apps erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="40c37-155">Migrating WCF services to use gRPC helps provide the RPC features, performance, an interoperability needed in modern apps.</span></span>

* <span data-ttu-id="40c37-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) ist eine Communityinitiative, mit der .NET Core und .NET 5 die Unterstützung zum Hosten von WCF-Diensten erhalten sollen.</span><span class="sxs-lookup"><span data-stu-id="40c37-156">[CoreWCF](https://github.com/CoreWCF/CoreWCF) is a community effort to bring support for hosting WCF services to .NET Core and .NET 5.</span></span> <span data-ttu-id="40c37-157">Ein Vorschauversion ist verfügbar, und das Projekt ist in Kürze für die Produktion bereit.</span><span class="sxs-lookup"><span data-stu-id="40c37-157">A preview release is available and the project is working towards being production ready.</span></span> <span data-ttu-id="40c37-158">CoreWCF unterstützt nur einen Teil der Features von WCF, und bei .NET Framework-Apps, die zur Verwendung dieser Features migriert werden, muss der Code geändert und getestet werden.</span><span class="sxs-lookup"><span data-stu-id="40c37-158">CoreWCF only supports a subset of WCF's features, and .NET Framework apps that migrate to use it will need code changes and testing to be successful.</span></span> <span data-ttu-id="40c37-159">CoreWCF ist eine gute Wahl, wenn eine App die Kompatibilität mit vorhandenen Clients wahren muss, die WCF-Dienste aufrufen.</span><span class="sxs-lookup"><span data-stu-id="40c37-159">CoreWCF is a good choice if an app has to maintain compatibility with existing clients that call WCF services.</span></span>

## <a name="get-started"></a><span data-ttu-id="40c37-160">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="40c37-160">Get started</span></span>

<span data-ttu-id="40c37-161">Eine ausführliche Anleitung zum Erstellen von gRPC-Diensten in ASP.NET Core für WCF-Entwickler finden Sie unter [ASP.NET Core gRPC für WCF-Entwickler](/dotnet/architecture/grpc-for-wcf-developers).</span><span class="sxs-lookup"><span data-stu-id="40c37-161">For detailed guidance on building gRPC services in ASP.NET Core for WCF developers, see [ASP.NET Core gRPC for WCF Developers](/dotnet/architecture/grpc-for-wcf-developers)</span></span>
