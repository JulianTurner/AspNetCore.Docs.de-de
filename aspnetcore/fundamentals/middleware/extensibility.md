---
title: Factorybezogene Middlewareaktivierung in ASP.NET Core
author: rick-anderson
description: Informationen zur Verwendung von stark typisierter Middleware mit einer factorybezogenen Aktivierungsimplementierung von ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057491"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="aa823-103">Factorybezogene Middlewareaktivierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa823-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="aa823-104">Bei <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> handelt es sich um einen Erweiterungspunkt für die [Middleware](xref:fundamentals/middleware/index)-Aktivierung.</span><span class="sxs-lookup"><span data-stu-id="aa823-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="aa823-105">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> überprüft, ob der registrierte Typ einer Middleware <xref:Microsoft.AspNetCore.Http.IMiddleware> implementiert.</span><span class="sxs-lookup"><span data-stu-id="aa823-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="aa823-106">Falls dies der Fall ist, wird die im Container registrierte <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>-Instanz im Container verwendet, um die <xref:Microsoft.AspNetCore.Http.IMiddleware>-Implementierung aufzulösen, anstatt die konventionsbasierte Middlewareaktivierungslogik zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa823-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="aa823-107">Die Middleware wird als [bereichsbezogener oder vorübergehender Dienst](xref:fundamentals/dependency-injection#service-lifetimes) im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="aa823-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="aa823-108">Vorteile:</span><span class="sxs-lookup"><span data-stu-id="aa823-108">Benefits:</span></span>

* <span data-ttu-id="aa823-109">Aktivierung pro Clientanforderung (Injektion von bereichsbezogenen Diensten)</span><span class="sxs-lookup"><span data-stu-id="aa823-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="aa823-110">Starke Typisierung der Middleware</span><span class="sxs-lookup"><span data-stu-id="aa823-110">Strong typing of middleware</span></span>

<span data-ttu-id="aa823-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> wird pro Clientanforderung (-verbindung) aktiviert, sodass bereichsbezogene Dienste in den Konstruktor der Middleware eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="aa823-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="aa823-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa823-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="aa823-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="aa823-113">IMiddleware</span></span>

<span data-ttu-id="aa823-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="aa823-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="aa823-115">Die [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*)-Methode verarbeitet Anforderungen und gibt einen <xref:System.Threading.Tasks.Task> zurück, der für die Ausführung der Middleware steht.</span><span class="sxs-lookup"><span data-stu-id="aa823-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="aa823-116">Über Konventionen aktivierte Middleware:</span><span class="sxs-lookup"><span data-stu-id="aa823-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="aa823-117">Über <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> aktivierte Middleware:</span><span class="sxs-lookup"><span data-stu-id="aa823-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="aa823-118">Für Middlewares werden Erweiterungen erstellt:</span><span class="sxs-lookup"><span data-stu-id="aa823-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="aa823-119">Es ist nicht möglich, Objekte mit <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> an eine Middleware zu übergeben, die über eine Factory aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="aa823-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="aa823-120">Die Middleware, die über eine Factory aktiviert wurde, wird dem integrierten Container in `Startup.ConfigureServices` hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa823-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="aa823-121">Beide Middlewares werden in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="aa823-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="aa823-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="aa823-122">IMiddlewareFactory</span></span>

<span data-ttu-id="aa823-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa823-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="aa823-124">Die Implementierung der Middlewarefactory wird im Container als bereichsbezogener Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="aa823-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="aa823-125">Die Standardimplementierung von <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, befindet sich im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="aa823-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="aa823-126">Bei <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> handelt es sich um einen Erweiterungspunkt für die [Middleware](xref:fundamentals/middleware/index)-Aktivierung.</span><span class="sxs-lookup"><span data-stu-id="aa823-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="aa823-127">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> überprüft, ob der registrierte Typ einer Middleware <xref:Microsoft.AspNetCore.Http.IMiddleware> implementiert.</span><span class="sxs-lookup"><span data-stu-id="aa823-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="aa823-128">Falls dies der Fall ist, wird die im Container registrierte <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>-Instanz im Container verwendet, um die <xref:Microsoft.AspNetCore.Http.IMiddleware>-Implementierung aufzulösen, anstatt die konventionsbasierte Middlewareaktivierungslogik zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa823-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="aa823-129">Die Middleware wird als [bereichsbezogener oder vorübergehender Dienst](xref:fundamentals/dependency-injection#service-lifetimes) im Dienstcontainer der App registriert.</span><span class="sxs-lookup"><span data-stu-id="aa823-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="aa823-130">Vorteile:</span><span class="sxs-lookup"><span data-stu-id="aa823-130">Benefits:</span></span>

* <span data-ttu-id="aa823-131">Aktivierung pro Clientanforderung (Injektion von bereichsbezogenen Diensten)</span><span class="sxs-lookup"><span data-stu-id="aa823-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="aa823-132">Starke Typisierung der Middleware</span><span class="sxs-lookup"><span data-stu-id="aa823-132">Strong typing of middleware</span></span>

<span data-ttu-id="aa823-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> wird pro Clientanforderung (-verbindung) aktiviert, sodass bereichsbezogene Dienste in den Konstruktor der Middleware eingefügt werden können.</span><span class="sxs-lookup"><span data-stu-id="aa823-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="aa823-134">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa823-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="aa823-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="aa823-135">IMiddleware</span></span>

<span data-ttu-id="aa823-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="aa823-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="aa823-137">Die [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*)-Methode verarbeitet Anforderungen und gibt einen <xref:System.Threading.Tasks.Task> zurück, der für die Ausführung der Middleware steht.</span><span class="sxs-lookup"><span data-stu-id="aa823-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="aa823-138">Über Konventionen aktivierte Middleware:</span><span class="sxs-lookup"><span data-stu-id="aa823-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="aa823-139">Über <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> aktivierte Middleware:</span><span class="sxs-lookup"><span data-stu-id="aa823-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="aa823-140">Für Middlewares werden Erweiterungen erstellt:</span><span class="sxs-lookup"><span data-stu-id="aa823-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="aa823-141">Es ist nicht möglich, Objekte mit <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> an eine Middleware zu übergeben, die über eine Factory aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="aa823-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="aa823-142">Die Middleware, die über eine Factory aktiviert wurde, wird dem integrierten Container in `Startup.ConfigureServices` hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="aa823-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="aa823-143">Beide Middlewares werden in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="aa823-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="aa823-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="aa823-144">IMiddlewareFactory</span></span>

<span data-ttu-id="aa823-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="aa823-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="aa823-146">Die Implementierung der Middlewarefactory wird im Container als bereichsbezogener Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="aa823-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="aa823-147">Die Standardimplementierung von <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, befindet sich im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/).</span><span class="sxs-lookup"><span data-stu-id="aa823-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="aa823-148">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="aa823-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
