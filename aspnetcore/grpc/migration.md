---
title: Migrieren von gRPC-Diensten von C-core zu ASP.NET Core
author: juntaoluo
description: Erfahren Sie, wie Sie eine vorhandene C-core-basierte gRPC-App so verschieben, dass sie auf einem ASP.NET Core-Stapel ausgeführt wird.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/25/2019
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
uid: grpc/migration
ms.openlocfilehash: 1a230e470fa666b2aa6761b4d5dabd09264d2aae
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059831"
---
# <a name="migrating-grpc-services-from-c-core-to-aspnet-core"></a><span data-ttu-id="15a10-103">Migrieren von gRPC-Diensten von C-core zu ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="15a10-103">Migrating gRPC services from C-core to ASP.NET Core</span></span>

<span data-ttu-id="15a10-104">Von [John Luo](https://github.com/juntaoluo)</span><span class="sxs-lookup"><span data-stu-id="15a10-104">By [John Luo](https://github.com/juntaoluo)</span></span>

<span data-ttu-id="15a10-105">Aufgrund der Implementierung des zugrundeliegenden Stapels funktionieren nicht alle Features in [C-core-basierten gRPC-Apps](https://grpc.io/blog/grpc-stacks) und ASP.NET Core-basierten Apps gleich.</span><span class="sxs-lookup"><span data-stu-id="15a10-105">Due to the implementation of the underlying stack, not all features work in the same way between [C-core-based gRPC](https://grpc.io/blog/grpc-stacks) apps and ASP.NET Core-based apps.</span></span> <span data-ttu-id="15a10-106">In diesem Artikel wird auf die Hauptunterschiede zwischen den beiden Stapeln in Bezug auf die Migration eingegangen.</span><span class="sxs-lookup"><span data-stu-id="15a10-106">This document highlights the key differences for migrating between the two stacks.</span></span>

## <a name="grpc-service-implementation-lifetime"></a><span data-ttu-id="15a10-107">Lebensdauer der gRPC-Dienstimplementierung</span><span class="sxs-lookup"><span data-stu-id="15a10-107">gRPC service implementation lifetime</span></span>

<span data-ttu-id="15a10-108">Im ASP.NET Core-Stapel werden gRPC-Dienste standardmäßig mit [bereichsbezogener Lebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) erstellt.</span><span class="sxs-lookup"><span data-stu-id="15a10-108">In the ASP.NET Core stack, gRPC services, by default, are created with a [scoped lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="15a10-109">Im Gegensatz hierzu ist gRPC-C-core standardmäßig an einen Dienst mit [Singletonlebensdauer](xref:fundamentals/dependency-injection#service-lifetimes) gebunden.</span><span class="sxs-lookup"><span data-stu-id="15a10-109">In contrast, gRPC C-core by default binds to a service with a [singleton lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="15a10-110">Die bereichsbezogene Lebensdauer ermöglicht es der Dienstimplementierung, andere Dienste mit bereichsbezogener Lebensdauer aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="15a10-110">A scoped lifetime allows the service implementation to resolve other services with scoped lifetimes.</span></span> <span data-ttu-id="15a10-111">Bei einer bereichsbezogenen Lebensdauer kann beispielsweise auch `DbContext` durch Konstruktorinjektion aus dem Abhängigkeitsinjektionscontainer aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="15a10-111">For example, a scoped lifetime can also resolve `DbContext` from the DI container through constructor injection.</span></span> <span data-ttu-id="15a10-112">Wenn bereichsbezogene Lebensdauer verwendet wird:</span><span class="sxs-lookup"><span data-stu-id="15a10-112">Using scoped lifetime:</span></span>

* <span data-ttu-id="15a10-113">wird für jede Anforderung eine neue Instanz der Dienstimplementierung konstruiert.</span><span class="sxs-lookup"><span data-stu-id="15a10-113">A new instance of the service implementation is constructed for each request.</span></span>
* <span data-ttu-id="15a10-114">ist es nicht möglich, Zustände zwischen Anforderungen über Instanzmember für den Implementierungstyp freizugeben.</span><span class="sxs-lookup"><span data-stu-id="15a10-114">It isn't possible to share state between requests via instance members on the implementation type.</span></span>
* <span data-ttu-id="15a10-115">wird erwartet, dass freigegebene Zustände in einem Singletondienst im Abhängigkeitsinjektionscontainer gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="15a10-115">The expectation is to store shared states in a singleton service in the DI container.</span></span> <span data-ttu-id="15a10-116">Die gespeicherten freigegebenen Zustände werden im Konstruktor der gRPC-Dienstimplementierung aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="15a10-116">The stored shared states are resolved in the constructor of the gRPC service implementation.</span></span>

<span data-ttu-id="15a10-117">Weitere Informationen zur Lebensdauer von Diensten finden Sie unter <xref:fundamentals/dependency-injection#service-lifetimes>.</span><span class="sxs-lookup"><span data-stu-id="15a10-117">For more information on service lifetimes, see <xref:fundamentals/dependency-injection#service-lifetimes>.</span></span>

### <a name="add-a-singleton-service"></a><span data-ttu-id="15a10-118">Hinzufügen eines Singletondiensts</span><span class="sxs-lookup"><span data-stu-id="15a10-118">Add a singleton service</span></span>

<span data-ttu-id="15a10-119">Die Übertragung einer gRPC-C-core-Implementierung in ASP.NET Core kann vereinfacht werden, indem die Lebensdauer der Dienstimplementierung von bereichsbezogen in Singleton geändert wird.</span><span class="sxs-lookup"><span data-stu-id="15a10-119">To facilitate the transition from a gRPC C-core implementation to ASP.NET Core, it's possible to change the service lifetime of the service implementation from scoped to singleton.</span></span> <span data-ttu-id="15a10-120">Hierbei wird eine Instanz der Dienstimplementierung zum Abhängigkeitsinjektionscontainer hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="15a10-120">This involves adding an instance of the service implementation to the DI container:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc();
    services.AddSingleton(new GreeterService());
}
```

<span data-ttu-id="15a10-121">Eine Dienstimplementierung mit Singletonlebensdauer kann allerdings bereichsbezogene Dienste nicht mehr durch Konstruktorinjektion auflösen.</span><span class="sxs-lookup"><span data-stu-id="15a10-121">However, a service implementation with a singleton lifetime is no longer able to resolve scoped services through constructor injection.</span></span>

## <a name="configure-grpc-services-options"></a><span data-ttu-id="15a10-122">Konfigurieren von Optionen für gRPC-Dienste</span><span class="sxs-lookup"><span data-stu-id="15a10-122">Configure gRPC services options</span></span>

<span data-ttu-id="15a10-123">In C-core-basierten Apps werden Einstellungen wie `grpc.max_receive_message_length` und `grpc.max_send_message_length` mit `ChannelOption` beim [Konstruieren der Serverinstanz](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="15a10-123">In C-core-based apps, settings such as `grpc.max_receive_message_length` and `grpc.max_send_message_length` are configured with `ChannelOption` when [constructing the Server instance](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server__ctor_System_Collections_Generic_IEnumerable_Grpc_Core_ChannelOption__).</span></span>

<span data-ttu-id="15a10-124">In ASP.NET Core stellt gRPC die Konfiguration über den Typ `GrpcServiceOptions` bereit.</span><span class="sxs-lookup"><span data-stu-id="15a10-124">In ASP.NET Core, gRPC provides configuration through the `GrpcServiceOptions` type.</span></span> <span data-ttu-id="15a10-125">Beispielsweise kann die Maximalgröße für eingehende Nachrichten für einen gRPC-Dienst über `AddGrpc` konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="15a10-125">For example, a gRPC service's the maximum incoming message size can be configured via `AddGrpc`.</span></span> <span data-ttu-id="15a10-126">Im folgenden Beispiel wird der Standardwert für `MaxReceiveMessageSize` von 4 MB in 16 MB geändert:</span><span class="sxs-lookup"><span data-stu-id="15a10-126">The following example changes the default `MaxReceiveMessageSize` of 4 MB to 16 MB:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.MaxReceiveMessageSize = 16 * 1024 * 1024; // 16 MB
    });
}
```

<span data-ttu-id="15a10-127">Weitere Informationen zur Konfiguration finden Sie unter <xref:grpc/configuration>.</span><span class="sxs-lookup"><span data-stu-id="15a10-127">For more information on configuration, see <xref:grpc/configuration>.</span></span>

## <a name="logging"></a><span data-ttu-id="15a10-128">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="15a10-128">Logging</span></span>

<span data-ttu-id="15a10-129">Bei C-core-basierten Apps wird mit `GrpcEnvironment` [die Protokollierung für Debuggingzwecke konfiguriert](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_).</span><span class="sxs-lookup"><span data-stu-id="15a10-129">C-core-based apps rely on the `GrpcEnvironment` to [configure the logger](https://grpc.io/grpc/csharp/api/Grpc.Core.GrpcEnvironment.html?q=size#Grpc_Core_GrpcEnvironment_SetLogger_Grpc_Core_Logging_ILogger_) for debugging purposes.</span></span> <span data-ttu-id="15a10-130">Im ASP.NET Core-Stapel wird diese Funktion über die [Protokollierungs-API](xref:fundamentals/logging/index) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="15a10-130">The ASP.NET Core stack provides this functionality through the [Logging API](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="15a10-131">Beispielsweise kann Protokollierung über Konstruktorinjektion zum gRPC-Dienst hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="15a10-131">For example, a logger can be added to the gRPC service via constructor injection:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

## <a name="https"></a><span data-ttu-id="15a10-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="15a10-132">HTTPS</span></span>

<span data-ttu-id="15a10-133">Bei C-core-basierten Apps wird HTTPS über die [Eigenschaft Server.Ports](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports) konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="15a10-133">C-core-based apps configure HTTPS through the [Server.Ports property](https://grpc.io/grpc/csharp/api/Grpc.Core.Server.html#Grpc_Core_Server_Ports).</span></span> <span data-ttu-id="15a10-134">Ein ähnliches Konzept wird beim Konfigurieren von Servern in ASP.NET Core verwendet.</span><span class="sxs-lookup"><span data-stu-id="15a10-134">A similar concept is used to configure servers in ASP.NET Core.</span></span> <span data-ttu-id="15a10-135">Kestrel verwendet beispielsweise die [Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) für diese Funktion.</span><span class="sxs-lookup"><span data-stu-id="15a10-135">For example, Kestrel uses [endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) for this functionality.</span></span>

## <a name="grpc-interceptors-vs-middleware"></a><span data-ttu-id="15a10-136">Gegenüberstellung gRPC-Interceptors und Middleware</span><span class="sxs-lookup"><span data-stu-id="15a10-136">gRPC Interceptors vs Middleware</span></span>

<span data-ttu-id="15a10-137">ASP.NET Core-[Middleware](xref:fundamentals/middleware/index) bietet ähnliche Funktionen wie Interceptors in C-core-basierten gRPC-Apps.</span><span class="sxs-lookup"><span data-stu-id="15a10-137">ASP.NET Core [middleware](xref:fundamentals/middleware/index) offers similar functionalities compared to interceptors in C-core-based gRPC apps.</span></span> <span data-ttu-id="15a10-138">ASP.NET Core-Middleware und Interceptors sind konzeptionell ähnlich.</span><span class="sxs-lookup"><span data-stu-id="15a10-138">ASP.NET Core middleware and interceptors are conceptually similar.</span></span> <span data-ttu-id="15a10-139">Beide:</span><span class="sxs-lookup"><span data-stu-id="15a10-139">Both:</span></span>

* <span data-ttu-id="15a10-140">werden dafür verwendet, eine Pipeline zu konstruieren, die gRPC-Anforderungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="15a10-140">Are used to construct a pipeline that handles a gRPC request.</span></span>
* <span data-ttu-id="15a10-141">ermöglichen das Ausführen von Arbeiten vor oder nach der nächsten Komponente in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="15a10-141">Allow work to be performed before or after the next component in the pipeline.</span></span>
* <span data-ttu-id="15a10-142">ermöglichen den Zugriff auf `HttpContext`:</span><span class="sxs-lookup"><span data-stu-id="15a10-142">Provide access to `HttpContext`:</span></span>
  * <span data-ttu-id="15a10-143">In der Middleware ist `HttpContext` ein Parameter.</span><span class="sxs-lookup"><span data-stu-id="15a10-143">In middleware the `HttpContext` is a parameter.</span></span>
  * <span data-ttu-id="15a10-144">In Interceptors kann auf `HttpContext` durch den Parameter `ServerCallContext` mit der Erweiterungsmethode `ServerCallContext.GetHttpContext` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="15a10-144">In interceptors the `HttpContext` can be accessed using the `ServerCallContext` parameter with the `ServerCallContext.GetHttpContext` extension method.</span></span> <span data-ttu-id="15a10-145">Beachten Sie, dass dieses Feature spezifisch für Interceptors ist, die in ASP.NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="15a10-145">Note that this feature is specific to interceptors running in ASP.NET Core.</span></span>

<span data-ttu-id="15a10-146">Unterschiede zwischen gRPC-Interceptors und ASP.NET Core-Middleware:</span><span class="sxs-lookup"><span data-stu-id="15a10-146">gRPC Interceptor differences from ASP.NET Core Middleware:</span></span>

* <span data-ttu-id="15a10-147">Interceptors:</span><span class="sxs-lookup"><span data-stu-id="15a10-147">Interceptors:</span></span>
  * <span data-ttu-id="15a10-148">arbeiten auf der gRPC-Abstraktionsebene mit [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span><span class="sxs-lookup"><span data-stu-id="15a10-148">Operate on the gRPC layer of abstraction using the [ServerCallContext](https://grpc.io/grpc/csharp/api/Grpc.Core.ServerCallContext.html).</span></span>
  * <span data-ttu-id="15a10-149">stellen Zugriff bereit auf:</span><span class="sxs-lookup"><span data-stu-id="15a10-149">Provide access to:</span></span>
    * <span data-ttu-id="15a10-150">die deserialisierte, beim Aufruf gesendete Nachricht.</span><span class="sxs-lookup"><span data-stu-id="15a10-150">The deserialized message sent to a call.</span></span>
    * <span data-ttu-id="15a10-151">die beim Aufruf zurückgegebene Nachricht, bevor diese serialisiert wird.</span><span class="sxs-lookup"><span data-stu-id="15a10-151">The message being returned from the call before it is serialized.</span></span>
  * <span data-ttu-id="15a10-152">können von gRPC-Diensten ausgelöste Ausnahmen abfangen und verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="15a10-152">Can catch and handle exceptions thrown from gRPC services.</span></span>
* <span data-ttu-id="15a10-153">Middleware:</span><span class="sxs-lookup"><span data-stu-id="15a10-153">Middleware:</span></span>
  * <span data-ttu-id="15a10-154">wird vor gRPC-Interceptors ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="15a10-154">Runs before gRPC interceptors.</span></span>
  * <span data-ttu-id="15a10-155">arbeitet mit den zugrundeliegenden HTTP/2-Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="15a10-155">Operates on the underlying HTTP/2 messages.</span></span>
  * <span data-ttu-id="15a10-156">kann nur auf Bytes aus den Anforderungs- und Antwortdatenströmen zugreifen.</span><span class="sxs-lookup"><span data-stu-id="15a10-156">Can only access bytes from the request and response streams.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="15a10-157">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="15a10-157">Additional resources</span></span>

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:tutorials/grpc/grpc-start>
