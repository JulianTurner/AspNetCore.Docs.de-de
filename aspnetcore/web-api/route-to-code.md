---
title: Grundlegende JSON-APIs mit Route-to-code in ASP.net Core
author: jamesnk
description: Erfahren Sie, wie Sie Route-to-code JSON-Erweiterungs Methoden und verwenden, um Lightweight JSON-Web-APIs zu erstellen.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
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
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 49eaa3ceb47c41226b7a50782436ec270e6e1b7b
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96335594"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="da93e-103">Grundlegende JSON-APIs mit Route-to-code in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="da93e-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="da93e-104">Von [James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="da93e-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="da93e-105">ASP.net Core unterstützt eine Reihe von Methoden zum Erstellen von JSON-Web-APIs:</span><span class="sxs-lookup"><span data-stu-id="da93e-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="da93e-106">[ASP.net Core Web-API](xref:web-api/index) bietet ein umfassendes Framework zum Erstellen von APIs.</span><span class="sxs-lookup"><span data-stu-id="da93e-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="da93e-107">Ein Dienst wird erstellt, indem von geerbt wird <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="da93e-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="da93e-108">Einige Features, die vom Framework bereitgestellt werden, sind Modell Bindung, Validierung, Inhaltsaushandlung, Eingabe-und Ausgabe Formatierung und OpenAPI.</span><span class="sxs-lookup"><span data-stu-id="da93e-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="da93e-109">Route-to-code ist eine Alternative zum ASP.net Core Web-API.</span><span class="sxs-lookup"><span data-stu-id="da93e-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="da93e-110">Route-to-code verbindet [ASP.net Core Routing](xref:fundamentals/routing) direkt mit Ihrem Code.</span><span class="sxs-lookup"><span data-stu-id="da93e-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="da93e-111">Der Code liest aus der Anforderung und schreibt die Antwort.</span><span class="sxs-lookup"><span data-stu-id="da93e-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="da93e-112">Route-to-code verfügt nicht über die erweiterten Features der Web-API, aber es ist auch keine Konfiguration erforderlich, um Sie zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="da93e-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="da93e-113">Route-to-code ist ein guter Ansatz beim Aufbau von kleinen und grundlegenden JSON-Web-APIs.</span><span class="sxs-lookup"><span data-stu-id="da93e-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="da93e-114">Erstellen von JSON-Web-APIs</span><span class="sxs-lookup"><span data-stu-id="da93e-114">Create JSON web APIs</span></span>

<span data-ttu-id="da93e-115">ASP.net Core stellt Hilfsmethoden bereit, die die Erstellung von JSON-Web-APIs vereinfachen:</span><span class="sxs-lookup"><span data-stu-id="da93e-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="da93e-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> überprüft den `Content-Type` Header auf einen JSON-Inhaltstyp.</span><span class="sxs-lookup"><span data-stu-id="da93e-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="da93e-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> liest JSON aus der Anforderung und deserialisiert Sie in den angegebenen Typ.</span><span class="sxs-lookup"><span data-stu-id="da93e-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="da93e-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> schreibt den angegebenen Wert als JSON in den Antworttext und legt den Inhaltstyp der Antwort auf fest `application/json` .</span><span class="sxs-lookup"><span data-stu-id="da93e-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="da93e-119">In *Startup.cs* werden Lightweight, Routen basierte JSON-APIs angegeben.</span><span class="sxs-lookup"><span data-stu-id="da93e-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="da93e-120">Die Route und die API-Logik werden in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> als Teil der Anforderungs Pipeline einer App konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="da93e-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="da93e-121">JSON-Antwortschreiben</span><span class="sxs-lookup"><span data-stu-id="da93e-121">Write JSON response</span></span>

<span data-ttu-id="da93e-122">Beachten Sie den folgenden Code, der eine JSON-API für eine App konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="da93e-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="da93e-123">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="da93e-123">The preceding code:</span></span>

* <span data-ttu-id="da93e-124">Fügt einen HTTP Get-API-Endpunkt mit `/hello/{name:alpha}` als Routen Vorlage hinzu.</span><span class="sxs-lookup"><span data-stu-id="da93e-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="da93e-125">Wenn die Route abgeglichen wird, liest die API den `name` Routen Wert aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="da93e-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="da93e-126">Schreibt einen anonymen Typ als JSON-Antwort mit `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="da93e-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="da93e-127">JSON-Anforderung lesen</span><span class="sxs-lookup"><span data-stu-id="da93e-127">Read JSON request</span></span>

<span data-ttu-id="da93e-128">`HasJsonContentType` und `ReadFromJsonAsync` können verwendet werden, um eine JSON-Antwort in einer Routen basierten JSON-API zu deserialisieren:</span><span class="sxs-lookup"><span data-stu-id="da93e-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="da93e-129">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="da93e-129">The preceding code:</span></span>

* <span data-ttu-id="da93e-130">Fügt einen HTTP Post-API-Endpunkt mit `/weather` als Routen Vorlage hinzu.</span><span class="sxs-lookup"><span data-stu-id="da93e-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="da93e-131">Wenn die Route übereinstimmt, wird `HasJsonContentType` der Inhaltstyp der Anforderung überprüft.</span><span class="sxs-lookup"><span data-stu-id="da93e-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="da93e-132">Ein nicht-JSON-Inhaltstyp gibt einen 415-Statuscode zurück.</span><span class="sxs-lookup"><span data-stu-id="da93e-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="da93e-133">Wenn der Inhaltstyp JSON ist, wird der Anforderungs Inhalt von deserialisiert `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="da93e-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="da93e-134">Konfigurieren der JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="da93e-134">Configure JSON serialization</span></span>

<span data-ttu-id="da93e-135">Es gibt zwei Möglichkeiten zum Anpassen der JSON-Serialisierung:</span><span class="sxs-lookup"><span data-stu-id="da93e-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="da93e-136">Standardserialisierungsoptionen können mit `JsonOptions` in der-Methode konfiguriert werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="da93e-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="da93e-137">`WriteAsJsonAsync` und `ReadFromJsonAsync` verfügen über über Ladungen, die ein- `JsonSerializerOptions` Objekt akzeptieren.</span><span class="sxs-lookup"><span data-stu-id="da93e-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="da93e-138">Dieses `JsonSerializerOptions` Objekt überschreibt die Standardoptionen.</span><span class="sxs-lookup"><span data-stu-id="da93e-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="da93e-139">Authentifizierung und Autorisierung</span><span class="sxs-lookup"><span data-stu-id="da93e-139">Authentication and authorization</span></span>

<span data-ttu-id="da93e-140">Route-to-code unterstützt Authentifizierung und Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="da93e-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="da93e-141">Attribute, wie z. b. `[Authorize]` und `[AllowAnonymous]` , können nicht auf Endpunkten platziert werden, die einem Anforderungs Delegaten zugeordnet sind.</span><span class="sxs-lookup"><span data-stu-id="da93e-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="da93e-142">Stattdessen werden Autorisierungs Metadaten mithilfe der `RequireAuthorization` -und- `AllowAnonymous` Erweiterungs Methoden hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="da93e-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="da93e-143">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="da93e-143">Dependency injection</span></span>

<span data-ttu-id="da93e-144">[Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection) mit einem Konstruktor ist mit nicht möglich Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="da93e-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="da93e-145">Die Web-API erstellt einen Controller für Sie mit Diensten, die in den Konstruktor eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="da93e-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="da93e-146">Ein Typ wird nicht erstellt, wenn ein Endpunkt ausgeführt wird, sodass Dienste manuell aufgelöst werden müssen.</span><span class="sxs-lookup"><span data-stu-id="da93e-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="da93e-147">Routen basierte APIs können <xref:System.IServiceProvider> zum Auflösen von Diensten verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="da93e-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="da93e-148">Vorübergehende und Bereichs bezogene Lebensdauer Dienste wie z `DbContext` . b. müssen aus [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) innerhalb des Anforderungs Delegaten eines Endpunkts aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="da93e-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="da93e-149">Singleton-Lebensdauer Dienste, wie z `ILogger` . b., können von [iendpointroutebuilder. Service Provider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)aufgelöst werden.</span><span class="sxs-lookup"><span data-stu-id="da93e-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="da93e-150">Dienste können außerhalb von Anforderungs Delegaten aufgelöst und von Endpunkten gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="da93e-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="da93e-151">APIs, die di ausgiebig verwenden, sollten einen ASP.net Core App-Typ verwenden, der di unterstützt.</span><span class="sxs-lookup"><span data-stu-id="da93e-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="da93e-152">Beispielsweise ASP.net Core Web-API.</span><span class="sxs-lookup"><span data-stu-id="da93e-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="da93e-153">Die Dienst Injektion mithilfe des Konstruktors eines Controllers ist einfacher als das manuelle Auflösen von Diensten.</span><span class="sxs-lookup"><span data-stu-id="da93e-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="da93e-154">API-Projektstruktur</span><span class="sxs-lookup"><span data-stu-id="da93e-154">API project structure</span></span>

<span data-ttu-id="da93e-155">Routen basierte APIs müssen in *Startup.cs* nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="da93e-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="da93e-156">APIs können in anderen Dateien abgelegt und beim Start mit zugeordnet werden `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="da93e-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="da93e-157">Bei diesem Ansatz wird die Größe der Start Konfigurationsdatei reduziert.</span><span class="sxs-lookup"><span data-stu-id="da93e-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="da93e-158">Beachten Sie die folgende statische `UserApi` Klasse, die eine `Map` Methode definiert.</span><span class="sxs-lookup"><span data-stu-id="da93e-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="da93e-159">Die-Methode ordnet Routen basierte APIs zu.</span><span class="sxs-lookup"><span data-stu-id="da93e-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="da93e-160">In der `Startup.Configure` `Map` -Methode werden die statischen Methoden der-Methode und der anderen Klasse in aufgerufen `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="da93e-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="da93e-161">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="da93e-161">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
