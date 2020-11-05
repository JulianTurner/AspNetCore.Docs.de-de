---
title: Schreiben von benutzerdefinierter ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie, wie benutzerdefinierte ASP.NET Core-Middleware geschrieben wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/18/2020
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
uid: fundamentals/middleware/write
ms.openlocfilehash: 5f33691cbcc00f407fff907ca62547fd80f2aa3c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057465"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="2be44-103">Schreiben von benutzerdefinierter ASP.NET Core-Middleware</span><span class="sxs-lookup"><span data-stu-id="2be44-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="2be44-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2be44-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2be44-105">Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2be44-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="2be44-106">ASP.NET Core stellt in umfangreichem Maß integrierte Middlewarekomponenten zur Verfügung. In manchen Szenarios möchten Sie aber vielleicht selbst eine benutzerdefinierte Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="2be44-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

> [!NOTE]
> <span data-ttu-id="2be44-107">In diesem Thema wird beschrieben, wie Sie *auf Konventionen basierende* Middleware schreiben.</span><span class="sxs-lookup"><span data-stu-id="2be44-107">This topic describes how to write *convention-based* middleware.</span></span> <span data-ttu-id="2be44-108">Informationen zu einem Ansatz, bei dem starke Typisierung und anforderungsbasierte Aktivierung verwendet werden, finden Sie unter <xref:fundamentals/middleware/extensibility>.</span><span class="sxs-lookup"><span data-stu-id="2be44-108">For an approach that uses strong typing and per-request activation, see <xref:fundamentals/middleware/extensibility>.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="2be44-109">Middlewareklasse</span><span class="sxs-lookup"><span data-stu-id="2be44-109">Middleware class</span></span>

<span data-ttu-id="2be44-110">Für gewöhnlich ist Middleware in einer Klasse gekapselt und wird mit einer Erweiterungsmethode verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="2be44-110">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="2be44-111">Sehen Sie sich folgende Middleware an, die die Kultur der aktuellen Anforderung über eine Abfragezeichenfolge festlegt:</span><span class="sxs-lookup"><span data-stu-id="2be44-111">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="2be44-112">Im vorhergehenden Beispielcode wird die Erstellung einer Middlewarekomponente veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="2be44-112">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="2be44-113">Informationen zur Unterstützung der integrierten Lokalisierung für ASP.NET Core finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="2be44-113">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="2be44-114">Testen Sie die Middleware, indem Sie die Kultur übergeben.</span><span class="sxs-lookup"><span data-stu-id="2be44-114">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="2be44-115">Fordern Sie beispielsweise `https://localhost:5001/?culture=no` an.</span><span class="sxs-lookup"><span data-stu-id="2be44-115">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="2be44-116">Im folgenden Code wird der Middlewaredelegat in eine Klasse verschoben:</span><span class="sxs-lookup"><span data-stu-id="2be44-116">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="2be44-117">Die Middlewareklasse muss Folgendes enthalten:</span><span class="sxs-lookup"><span data-stu-id="2be44-117">The middleware class must include:</span></span>

* <span data-ttu-id="2be44-118">Einen öffentlichen Konstruktor mit einem Parameter des Typs <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span><span class="sxs-lookup"><span data-stu-id="2be44-118">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="2be44-119">Eine öffentliche Methode mit dem Namen `Invoke` oder `InvokeAsync`.</span><span class="sxs-lookup"><span data-stu-id="2be44-119">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="2be44-120">Diese Methode muss Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2be44-120">This method must:</span></span>
  * <span data-ttu-id="2be44-121">Eine `Task` zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="2be44-121">Return a `Task`.</span></span>
  * <span data-ttu-id="2be44-122">Einen ersten Parameter des Typs <xref:Microsoft.AspNetCore.Http.HttpContext> akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="2be44-122">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="2be44-123">Zusätzliche Parameter für den Konstruktor und `Invoke`/`InvokeAsync` werden mittels [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="2be44-123">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="2be44-124">Middlewareabhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="2be44-124">Middleware dependencies</span></span>

<span data-ttu-id="2be44-125">Middleware sollte das [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) befolgen, indem sie ihre Abhängigkeiten in ihrem Konstruktor verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="2be44-125">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="2be44-126">Middleware wird einmal während der *Anwendungslebensdauer* erstellt.</span><span class="sxs-lookup"><span data-stu-id="2be44-126">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="2be44-127">Lesen Sie den Abschnitt [Voranforderungsbasierte Middlewareabhängigkeiten](#per-request-middleware-dependencies), wenn Sie Dienste für Middleware innerhalb einer Anforderung gemeinsam verwenden müssen.</span><span class="sxs-lookup"><span data-stu-id="2be44-127">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="2be44-128">Middlewarekomponenten können Ihre Abhängigkeiten über [Dependency Injection (DI)](xref:fundamentals/dependency-injection) mit Konstruktorparametern auflösen.</span><span class="sxs-lookup"><span data-stu-id="2be44-128">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="2be44-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) kann auch direkt zusätzliche Parameter annehmen.</span><span class="sxs-lookup"><span data-stu-id="2be44-129">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="2be44-130">Voranforderungsbasierte Middlewareabhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="2be44-130">Per-request middleware dependencies</span></span>

<span data-ttu-id="2be44-131">Weil Middleware zum Zeitpunkt des Anwendungsstarts erstellt wird (und nicht voranforderungsbasiert), werden *bereichsbezogene* Lebensdauerdienste von Middlewarekonstruktoren in den einzelnen Anforderungen nicht gemeinsam mit anderen Typen mit Dependency Injection verwendet.</span><span class="sxs-lookup"><span data-stu-id="2be44-131">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="2be44-132">Wenn Sie einen *bereichsbezogenen* Dienst sowohl in Ihrer Middleware als auch in anderen Typen verwenden müssen, fügen Sie diese Dienste zur Signatur der `Invoke`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="2be44-132">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="2be44-133">Die `Invoke`-Methode kann zusätzliche Parameter akzeptieren, die durch DI aufgefüllt werden:</span><span class="sxs-lookup"><span data-stu-id="2be44-133">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

<span data-ttu-id="2be44-134">Die [Lebensdauer und Registrierungsoptionen](xref:fundamentals/dependency-injection#lifetime-and-registration-options) enthalten ein vollständiges Beispiel der Middleware mit Diensten mit *bereichsbezogener* Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="2be44-134">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* lifetime services.</span></span>

## <a name="middleware-extension-method"></a><span data-ttu-id="2be44-135">Erweiterungsmethode für die Middleware</span><span class="sxs-lookup"><span data-stu-id="2be44-135">Middleware extension method</span></span>

<span data-ttu-id="2be44-136">Die folgende Erweiterungsmethode stellt die Middleware über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="2be44-136">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="2be44-137">Der folgende Code ruft die Methode von `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="2be44-137">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="2be44-138">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2be44-138">Additional resources</span></span>

* <span data-ttu-id="2be44-139">Die [Lebensdauer und Registrierungsoptionen](xref:fundamentals/dependency-injection#lifetime-and-registration-options) enthalten ein vollständiges Beispiel der Middleware mit Diensten mit *bereichsbezogener* und *vorübergehender* Lebensdauer sowie *Singletonlebensdauer*.</span><span class="sxs-lookup"><span data-stu-id="2be44-139">[Lifetime and registration options](xref:fundamentals/dependency-injection#lifetime-and-registration-options) contains a complete sample of middleware with *scoped* , *transient* , and *singleton* lifetime services.</span></span>
* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
