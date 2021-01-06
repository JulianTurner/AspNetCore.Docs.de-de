---
title: Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie stark typisierte Middleware mit einer factorybezogenen Aktivierung und einem Drittanbietercontainer in ASP.NET Core verwenden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
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
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/04/2021
ms.locfileid: "93057764"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="18d2a-103">Middlewareaktivierung mit einem Drittanbietercontainer in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="18d2a-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="18d2a-104">In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18d2a-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="18d2a-105">Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="18d2a-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="18d2a-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18d2a-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="18d2a-107">Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar.</span><span class="sxs-lookup"><span data-stu-id="18d2a-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="18d2a-108">Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="18d2a-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="18d2a-109">Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="18d2a-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="18d2a-110">Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="18d2a-111">Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken.</span><span class="sxs-lookup"><span data-stu-id="18d2a-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="18d2a-112">Die Verwendung von Simple Injector ist nicht verpflichtend.</span><span class="sxs-lookup"><span data-stu-id="18d2a-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="18d2a-113">Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="18d2a-114">Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="18d2a-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="18d2a-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="18d2a-115">IMiddlewareFactory</span></span>

<span data-ttu-id="18d2a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="18d2a-117">In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="18d2a-118">Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="18d2a-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="18d2a-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="18d2a-119">IMiddleware</span></span>

<span data-ttu-id="18d2a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="18d2a-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="18d2a-121">Middleware, die von einer `IMiddlewareFactory`-Implementierung (*Middleware/SimpleInjectorActivatedMiddleware.cs*) aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="18d2a-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="18d2a-122">Eine Erweiterung (*Middleware/MiddlewareExtensions.cs*) wird für die Middleware erstellt:</span><span class="sxs-lookup"><span data-stu-id="18d2a-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="18d2a-123">`Startup.ConfigureServices` muss mehrere Tasks durchführen:</span><span class="sxs-lookup"><span data-stu-id="18d2a-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="18d2a-124">Richten Sie den Simple Injector-Container ein.</span><span class="sxs-lookup"><span data-stu-id="18d2a-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="18d2a-125">Registrieren Sie die Factory und die Middleware.</span><span class="sxs-lookup"><span data-stu-id="18d2a-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="18d2a-126">Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="18d2a-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="18d2a-127">Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="18d2a-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="18d2a-128">In diesem Artikel wird veranschaulicht, wie <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> und <xref:Microsoft.AspNetCore.Http.IMiddleware> als Erweiterungspunkt für die Aktivierung von [Middleware](xref:fundamentals/middleware/index) mit einem Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="18d2a-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="18d2a-129">Einführende Informationen zu `IMiddlewareFactory` und `IMiddleware` finden Sie unter <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="18d2a-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="18d2a-130">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="18d2a-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="18d2a-131">Die Beispiel-App stellt die Aktivierung von Middleware durch eine `IMiddlewareFactory`-Implementierung (`SimpleInjectorMiddlewareFactory`) dar.</span><span class="sxs-lookup"><span data-stu-id="18d2a-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="18d2a-132">Das Beispiel verwendet den Dependency Injection-Container [Simple Injector](https://simpleinjector.org).</span><span class="sxs-lookup"><span data-stu-id="18d2a-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="18d2a-133">Die Middlewareimplementierungen in diesem Beispiel erfassen den Wert, der von einem Parameter für Abfragezeichenfolgen (`key`) angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="18d2a-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="18d2a-134">Die Middleware verwendet einen eingefügten Datenbankkontext (einen bereichsbezogenen Dienst), um den Abfragezeichenwert in einer speicherinternen Datenbank zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="18d2a-135">Die Beispiel-App verwendet [Simple Injector](https://github.com/simpleinjector/SimpleInjector) ausschließlich zu Demonstrationszwecken.</span><span class="sxs-lookup"><span data-stu-id="18d2a-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="18d2a-136">Die Verwendung von Simple Injector ist nicht verpflichtend.</span><span class="sxs-lookup"><span data-stu-id="18d2a-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="18d2a-137">Die Ansätze für die Aktivierung von Middleware, die in der Dokumentation zu Simple Injector und in GitHub-Problemen beschrieben werden, werden von den Besitzern von Simple Injector empfohlen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="18d2a-138">Weitere Informationen finden Sie in der [Dokumentation zu Simple Injector](https://simpleinjector.readthedocs.io/en/latest/index.html) und im [GitHub-Repository für Simple Injector](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="18d2a-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="18d2a-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="18d2a-139">IMiddlewareFactory</span></span>

<span data-ttu-id="18d2a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> stellt Methoden zur Verfügung, um die Middleware zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="18d2a-141">In der Beispiel-App wird eine Middlewarefactory implementiert, um eine `SimpleInjectorActivatedMiddleware`-Instanz zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="18d2a-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="18d2a-142">Die Middlewarefactory verwendet den Simple Injector-Container, um die Middleware aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="18d2a-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="18d2a-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="18d2a-143">IMiddleware</span></span>

<span data-ttu-id="18d2a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> definiert Middleware für die Anforderungspipeline der App.</span><span class="sxs-lookup"><span data-stu-id="18d2a-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="18d2a-145">Middleware, die von einer `IMiddlewareFactory`-Implementierung (*Middleware/SimpleInjectorActivatedMiddleware.cs*) aktiviert wurde:</span><span class="sxs-lookup"><span data-stu-id="18d2a-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="18d2a-146">Eine Erweiterung (*Middleware/MiddlewareExtensions.cs*) wird für die Middleware erstellt:</span><span class="sxs-lookup"><span data-stu-id="18d2a-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="18d2a-147">`Startup.ConfigureServices` muss mehrere Tasks durchführen:</span><span class="sxs-lookup"><span data-stu-id="18d2a-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="18d2a-148">Richten Sie den Simple Injector-Container ein.</span><span class="sxs-lookup"><span data-stu-id="18d2a-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="18d2a-149">Registrieren Sie die Factory und die Middleware.</span><span class="sxs-lookup"><span data-stu-id="18d2a-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="18d2a-150">Stellen Sie den Datenbankkontext der App über den Simple Injector-Container zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="18d2a-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="18d2a-151">Die Middlewares wird in der Anforderungsverarbeitungspipeline in `Startup.Configure` registriert:</span><span class="sxs-lookup"><span data-stu-id="18d2a-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="18d2a-152">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="18d2a-152">Additional resources</span></span>

* [<span data-ttu-id="18d2a-153">Middleware</span><span class="sxs-lookup"><span data-stu-id="18d2a-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="18d2a-154">Factorybezogene Middlewareaktivierung</span><span class="sxs-lookup"><span data-stu-id="18d2a-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="18d2a-155">GitHub-Repository für Simple Injector</span><span class="sxs-lookup"><span data-stu-id="18d2a-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="18d2a-156">Dokumentation zu Simple Injector</span><span class="sxs-lookup"><span data-stu-id="18d2a-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
