---
title: Fehlerbehandlung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Fehlerbehandlung in ASP.NET Core-Apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060468"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="19600-103">Fehlerbehandlung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="19600-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="19600-104">Von [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="19600-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="19600-105">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="19600-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="19600-106">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="19600-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="19600-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="19600-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="19600-108">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).) Die Registerkarte „Netzwerk“ in den F12-Entwicklertools im Browser ist beim Testen der Beispiel-App nützlich.</span><span class="sxs-lookup"><span data-stu-id="19600-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="19600-109">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="19600-109">Developer Exception Page</span></span>

<span data-ttu-id="19600-110">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="19600-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="19600-111">Die ASP.NET Core-Vorlagen generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="19600-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="19600-112">Der vorangehende hervorgehobene Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="19600-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="19600-113">Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> am Anfang der Middlewarepipeline, sodass die in der nachfolgenden Middleware ausgelösten Ausnahmen abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="19600-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="19600-114">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler **nur** dann, wenn die App in der Entwicklungsumgebung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="19600-114">The preceding code enables the Developer Exception Page \* **only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="19600-115">Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="19600-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="19600-116">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="19600-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="19600-117">Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="19600-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="19600-118">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="19600-118">_ Stack trace</span></span>
* <span data-ttu-id="19600-119">Abfragezeichenfolgeparameter, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="19600-119">Query string parameters if any</span></span>
* <span data-ttu-id="19600-120">:::no-loc(Cookie):::s, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="19600-120">:::no-loc(Cookie):::s if any</span></span>
* <span data-ttu-id="19600-121">Header</span><span class="sxs-lookup"><span data-stu-id="19600-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="19600-122">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="19600-122">Exception handler page</span></span>

<span data-ttu-id="19600-123">Um eine benutzerdefinierte Fehlerbehandlungsseite für die [Produktionsumgebung](xref:fundamentals/environments) zu konfigurieren, rufen Sie <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> auf.</span><span class="sxs-lookup"><span data-stu-id="19600-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="19600-124">Die Aufgaben der Middleware zur Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="19600-124">This exception handling middleware:</span></span>

* <span data-ttu-id="19600-125">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="19600-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="19600-126">Führt erneut die Anforderung in einer anderen Pipeline im angegebenen Pfad aus.</span><span class="sxs-lookup"><span data-stu-id="19600-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="19600-127">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="19600-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="19600-128">Der von der Vorlage generierte Code führt die Anforderung erneut mit dem `/Error`-Pfad aus.</span><span class="sxs-lookup"><span data-stu-id="19600-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="19600-129">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="19600-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="19600-130">Die :::no-loc(Razor)::: Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml* ) und <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-130">The :::no-loc(Razor)::: Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="19600-131">Die Projektvorlage einer MVC-App enthält die Aktionsmethode `Error` und die Ansicht „Error“ im Home-Controller.</span><span class="sxs-lookup"><span data-stu-id="19600-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="19600-132">Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="19600-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="19600-133">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Aktionsmethode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="19600-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="19600-134">Lassen Sie den anonymen Zugriff auf die Methode zu, wenn nicht authentifizierten Benutzern die Fehleransicht angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="19600-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="19600-135">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="19600-135">Access the exception</span></span>

<span data-ttu-id="19600-136">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Fehlerhandler zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="19600-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="19600-137">Der folgende Code fügt dem Standard *Pages/Error.cshtml.cs* die von den ASP.NET Core-Vorlagen generierte `ExceptionMessage` hinzu:</span><span class="sxs-lookup"><span data-stu-id="19600-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="19600-138">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="19600-139">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="19600-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="19600-140">So testen Sie die Ausnahme in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="19600-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="19600-141">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="19600-141">Set the environment to production.</span></span>
* <span data-ttu-id="19600-142">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="19600-143">Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.</span><span class="sxs-lookup"><span data-stu-id="19600-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="19600-144">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="19600-144">Exception handler lambda</span></span>

<span data-ttu-id="19600-145">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="19600-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="19600-146">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="19600-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="19600-147">Der folgende Code verwendet einen Lambdaausdruck für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="19600-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="19600-148">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="19600-149">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="19600-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="19600-150">So testen Sie den Lambdaausdruck für die Ausnahmebehandlung in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="19600-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="19600-151">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="19600-151">Set the environment to production.</span></span>
* <span data-ttu-id="19600-152">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="19600-153">Wählen Sie **Trigger an exception** (Ausnahme auslösen) auf der Startseite aus.</span><span class="sxs-lookup"><span data-stu-id="19600-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="19600-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="19600-154">UseStatusCodePages</span></span>

<span data-ttu-id="19600-155">Die ASP.NET Core-App stellt für HTTP-Fehlerstatuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="19600-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="19600-156">Wenn eine App eine HTTP-Fehlerbedingung zwischen 400–499 feststellt, die keinen Text enthält, werden der Statuscode und ein leerer Antworttext zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="19600-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="19600-157">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="19600-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="19600-158">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:</span><span class="sxs-lookup"><span data-stu-id="19600-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="19600-159">Rufen Sie `UseStatusCodePages` vor der Middleware für die Anforderungsverarbeitung auf.</span><span class="sxs-lookup"><span data-stu-id="19600-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="19600-160">Rufen Sie beispielsweise `UseStatusCodePages` vor der Middleware für statische Dateien und der Middleware für Endpunkte auf.</span><span class="sxs-lookup"><span data-stu-id="19600-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="19600-161">Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="19600-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="19600-162">Navigieren Sie zum Beispiel zu `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="19600-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="19600-163">Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="19600-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="19600-164">`UseStatusCodePages` wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="19600-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="19600-165">So testen Sie `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)</span><span class="sxs-lookup"><span data-stu-id="19600-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="19600-166">Legen Sie die Umgebung auf Produktion fest.</span><span class="sxs-lookup"><span data-stu-id="19600-166">Set the environment to production.</span></span>
* <span data-ttu-id="19600-167">Entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="19600-168">Klicken Sie auf die Links auf der Startseite.</span><span class="sxs-lookup"><span data-stu-id="19600-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="19600-169">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="19600-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="19600-170">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="19600-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="19600-171">Im vorangehenden Code ist `{0}` ein Platzhalter für den Fehlercode.</span><span class="sxs-lookup"><span data-stu-id="19600-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="19600-172">`UseStatusCodePages` mit Formatzeichenfolge wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="19600-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="19600-173">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="19600-174">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="19600-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="19600-175">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="19600-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="19600-176">`UseStatusCodePages` mit Lambdaausdruck wird normalerweise nicht in der Produktionsumgebung verwendet, da diese Methode eine Meldung zurückgibt, die für Benutzer nicht nützlich ist.</span><span class="sxs-lookup"><span data-stu-id="19600-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="19600-177">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="19600-178">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="19600-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="19600-179">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="19600-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="19600-180">Sendet den Statuscode [302 Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="19600-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="19600-181">Leitet den Client an den in der URL-Vorlage angegebenen Fehlerbehandlungsendpunkt weiter.</span><span class="sxs-lookup"><span data-stu-id="19600-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="19600-182">Der Fehlerbehandlungsendpunkt zeigt in der Regel Fehlerinformationen an und gibt den HTTP-Code 200 zurück.</span><span class="sxs-lookup"><span data-stu-id="19600-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="19600-183">Die URL-Vorlage kann, wie im vorangehenden Code gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="19600-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="19600-184">Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="19600-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="19600-185">Wenn Sie in der App einen Endpunkt angeben, müssen Sie eine MVC-Ansicht oder :::no-loc(Razor):::-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="19600-185">When specifying an endpoint in the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="19600-186">Ein :::no-loc(Razor)::: Pages-Beispiel finden Sie unter [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="19600-186">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="19600-187">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="19600-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="19600-188">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="19600-189">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="19600-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="19600-190">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="19600-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="19600-191">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="19600-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="19600-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="19600-193">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="19600-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="19600-194">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="19600-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="19600-195">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="19600-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="19600-196">Wenn in der App ein Endpunkt angegeben ist, müssen Sie eine MVC-Ansicht oder :::no-loc(Razor):::-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="19600-196">If an endpoint within the app is specified, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="19600-197">Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="19600-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="19600-198">Ein :::no-loc(Razor)::: Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="19600-198">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="19600-199">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="19600-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="19600-200">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="19600-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="19600-201">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="19600-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="19600-202">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="19600-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="19600-203">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="19600-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="19600-204">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="19600-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a :::no-loc(Razor)::: Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="19600-205">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="19600-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="19600-206">Ein :::no-loc(Razor)::: Pages-Beispiel finden Sie unter [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span><span class="sxs-lookup"><span data-stu-id="19600-206">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="19600-207">Um `UseStatusCodePages` in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) zu testen, entfernen Sie die Kommentare aus `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span><span class="sxs-lookup"><span data-stu-id="19600-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="19600-208">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="19600-208">Disable status code pages</span></span>

<span data-ttu-id="19600-209">Verwenden Sie das Attribut [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="19600-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="19600-210">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von :::no-loc(Razor)::: Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="19600-210">To disable status code pages for specific requests in a :::no-loc(Razor)::: Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="19600-211">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="19600-211">Exception-handling code</span></span>

<span data-ttu-id="19600-212">Code auf Seiten zur Ausnahmebehandlung kann ebenfalls Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="19600-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="19600-213">Produktionsfehlerseiten sollten gründlich getestet werden. Achten Sie darauf, dabei keine eigenen Ausnahmen auszulösen.</span><span class="sxs-lookup"><span data-stu-id="19600-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="19600-214">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="19600-214">Response headers</span></span>

<span data-ttu-id="19600-215">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="19600-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="19600-216">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="19600-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="19600-217">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="19600-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="19600-218">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="19600-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="19600-219">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="19600-219">Server exception handling</span></span>

<span data-ttu-id="19600-220">Neben der Ausnahmebehandlungslogik in einer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="19600-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="19600-221">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort `500 - Internal Server Error` ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="19600-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="19600-222">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="19600-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="19600-223">Anforderungen, die nicht von der App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="19600-224">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="19600-225">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="19600-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="19600-226">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="19600-226">Startup exception handling</span></span>

<span data-ttu-id="19600-227">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="19600-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="19600-228">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="19600-229">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="19600-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="19600-230">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="19600-230">If binding fails:</span></span>

* <span data-ttu-id="19600-231">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="19600-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="19600-232">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="19600-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="19600-233">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="19600-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="19600-234">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="19600-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="19600-235">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="19600-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="19600-236">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="19600-236">Database error page</span></span>

<span data-ttu-id="19600-237">Der Ausnahmefilter der Datenbankentwicklerseite `AddDatabaseDeveloperPageExceptionFilter` erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework Core-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="19600-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="19600-238">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="19600-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="19600-239">Diese Seite ist nur in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="19600-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="19600-240">Der folgende Code wurde von den ASP.NET Core :::no-loc(Razor)::: Pages-Vorlagen generiert, als einzelne Benutzerkonten angegeben wurden:</span><span class="sxs-lookup"><span data-stu-id="19600-240">The following code was generated by the ASP.NET Core :::no-loc(Razor)::: Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="19600-241">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="19600-241">Exception filters</span></span>

<span data-ttu-id="19600-242">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="19600-243">In :::no-loc(Razor)::: Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-243">In :::no-loc(Razor)::: Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="19600-244">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="19600-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="19600-245">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="19600-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="19600-246">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen ausgelöst werden. Sie sind jedoch nicht so flexibel wie die integrierte [Middleware für die Ausnahmebehandlung](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span><span class="sxs-lookup"><span data-stu-id="19600-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="19600-247">Es wird empfohlen, `UseExceptionHandler` zu verwenden, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie auswählen müssen.</span><span class="sxs-lookup"><span data-stu-id="19600-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="19600-248">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="19600-248">Model state errors</span></span>

<span data-ttu-id="19600-249">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="19600-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19600-250">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="19600-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="19600-251">Von [Tom Dykstra](https://github.com/tdykstra/) und [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="19600-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="19600-252">Dieser Artikel beschreibt grundsätzliche Vorgehensweisen zur Behandlung von Fehlern in ASP.NET Core-Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="19600-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="19600-253">Weitere Informationen für Web-Apps finden Sie unter <xref:web-api/handle-errors>.</span><span class="sxs-lookup"><span data-stu-id="19600-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="19600-254">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="19600-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="19600-255">([Informationen zum Herunterladen](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="19600-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="19600-256">Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="19600-256">Developer Exception Page</span></span>

<span data-ttu-id="19600-257">Die *Seite mit Ausnahmen für Entwickler* enthält ausführliche Informationen zu Anforderungsausnahmen.</span><span class="sxs-lookup"><span data-stu-id="19600-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="19600-258">Die ASP.NET Core-Vorlagen generieren den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="19600-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="19600-259">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler, wenn die App in der [Entwicklungsumgebung](xref:fundamentals/environments) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="19600-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="19600-260">Die Vorlagen platzieren <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> vor jeder Middleware, sodass Ausnahmen in der nachfolgenden Middleware abgefangen werden.</span><span class="sxs-lookup"><span data-stu-id="19600-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="19600-261">Der vorangehende Code aktiviert die Seite mit Ausnahmen für Entwickler nur dann, wenn die App in der **Entwicklungsumgebung** ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="19600-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="19600-262">Bei Ausführung der App in der Produktionsumgebung sollten keine detaillierten Informationen zu den Ausnahmen öffentlich angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="19600-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="19600-263">Weitere Informationen zum Konfigurieren der Umgebungen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="19600-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="19600-264">Die Seite mit Ausnahmen für Entwickler enthält die folgenden Informationen zu der Ausnahme und der Anforderung:</span><span class="sxs-lookup"><span data-stu-id="19600-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="19600-265">Stapelüberwachung</span><span class="sxs-lookup"><span data-stu-id="19600-265">Stack trace</span></span>
* <span data-ttu-id="19600-266">Abfragezeichenfolgeparameter, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="19600-266">Query string parameters if any</span></span>
* <span data-ttu-id="19600-267">:::no-loc(Cookie):::s, sofern vorhanden</span><span class="sxs-lookup"><span data-stu-id="19600-267">:::no-loc(Cookie):::s if any</span></span>
* <span data-ttu-id="19600-268">Header</span><span class="sxs-lookup"><span data-stu-id="19600-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="19600-269">Seite „Ausnahmehandler“</span><span class="sxs-lookup"><span data-stu-id="19600-269">Exception handler page</span></span>

<span data-ttu-id="19600-270">Um eine benutzerdefinierte Fehlerbehandlung für die Produktionsumgebung zu konfigurieren, verwenden Sie Middleware zur Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="19600-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="19600-271">Die Middleware:</span><span class="sxs-lookup"><span data-stu-id="19600-271">The middleware:</span></span>

* <span data-ttu-id="19600-272">Dient zum Abfangen und Protokollieren von Ausnahmen.</span><span class="sxs-lookup"><span data-stu-id="19600-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="19600-273">Führt die Anforderung für die angegebene Seite oder den Controller in einer anderen Pipeline erneut aus.</span><span class="sxs-lookup"><span data-stu-id="19600-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="19600-274">Die Anforderung wird nicht erneut ausgeführt, wenn die Antwort gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="19600-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="19600-275">Der von der Vorlage generierte Code führt die Anforderung erneut mit `/Error` aus.</span><span class="sxs-lookup"><span data-stu-id="19600-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="19600-276">Im folgenden Beispiel fügt <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> die Middleware zur Ausnahmebehandlung in Nichtentwicklungsumgebungen hinzu:</span><span class="sxs-lookup"><span data-stu-id="19600-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="19600-277">Die :::no-loc(Razor)::: Pages-App-Vorlage stellt im Ordner *Pages* eine Fehlerseite ( *.cshtml* ) und <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel>-Klasse (`ErrorModel`) bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-277">The :::no-loc(Razor)::: Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="19600-278">Die Projektvorlage einer MVC-App enthält eine Fehleraktionsmethode und die Ansicht „Fehler“ im Home-Controller.</span><span class="sxs-lookup"><span data-stu-id="19600-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="19600-279">Markieren Sie die Aktionsmethode für die Fehlerbehandlung nicht mit HTTP-Methodenattributen wie `HttpGet`.</span><span class="sxs-lookup"><span data-stu-id="19600-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="19600-280">Durch explizite Verben könnte bei einigen Anforderungen verhindert werden, dass diese Methode zum Einsatz kommt.</span><span class="sxs-lookup"><span data-stu-id="19600-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="19600-281">Lassen Sie den anonymen Zugriff auf die Methode zu, wenn nicht authentifizierten Benutzern die Fehleransicht angezeigt werden soll.</span><span class="sxs-lookup"><span data-stu-id="19600-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="19600-282">Zugreifen auf die Ausnahme</span><span class="sxs-lookup"><span data-stu-id="19600-282">Access the exception</span></span>

<span data-ttu-id="19600-283">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>, um auf die Ausnahme oder den Pfad der ursprünglichen Anforderung in einem Controller für die Fehlerbehandlung oder auf einer Seite zuzugreifen:</span><span class="sxs-lookup"><span data-stu-id="19600-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="19600-284">Stellen Sie Clients **keine** vertraulichen Fehlerinformationen bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="19600-285">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="19600-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="19600-286">Legen Sie die Umgebung auf Produktionen fest, und erzwingen Sie eine Ausnahme, um die vorherige Seite zur Ausnahmebehandlung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="19600-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="19600-287">Lambda-Ausdruck für Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="19600-287">Exception handler lambda</span></span>

<span data-ttu-id="19600-288">Eine Alternative zu einer [benutzerdefinierten Ausnahmebehandlungsseite](#exception-handler-page) ist das Angeben eines Lambda-Ausdrucks für <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span><span class="sxs-lookup"><span data-stu-id="19600-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="19600-289">Die Verwendung eines Lambda-Ausdrucks ermöglicht den Zugriff auf den Fehler, bevor die Antwort zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="19600-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="19600-290">Hier ist ein Beispiel der Verwendung eines Lambda-Ausdrucks für die Ausnahmebehandlung:</span><span class="sxs-lookup"><span data-stu-id="19600-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="19600-291">Im vorangehenden Code wird `await context.Response.WriteAsync(new string(' ', 512));` hinzugefügt, sodass der Internet Explorer-Browser die Fehlermeldung anstelle einer IE-Fehlermeldung anzeigt.</span><span class="sxs-lookup"><span data-stu-id="19600-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="19600-292">Weitere Informationen finden Sie in [diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span><span class="sxs-lookup"><span data-stu-id="19600-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="19600-293">Stellen Sie **keine** vertraulichen Fehlerinformationen aus <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> oder <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> für Clients bereit.</span><span class="sxs-lookup"><span data-stu-id="19600-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="19600-294">Die Bereitstellung von Fehlern ist ein Sicherheitsrisiko.</span><span class="sxs-lookup"><span data-stu-id="19600-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="19600-295">Um in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) das Ergebnis des Lambda-Ausdrucks für die Ausnahmebehandlung anzuzeigen, verwenden Sie die Präprozessordirektiven `ProdEnvironment` und `ErrorHandlerLambda`, und wählen Sie auf der Startseite **Ausnahme auslösen** aus.</span><span class="sxs-lookup"><span data-stu-id="19600-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="19600-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="19600-296">UseStatusCodePages</span></span>

<span data-ttu-id="19600-297">Ihre ASP.NET Core-App stellt für HTTP-Statuscodes wie *404 – Nicht gefunden* standardmäßig keine Statuscodeseite zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="19600-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="19600-298">Die App gibt einen Statuscode und einen leeren Antworttext zurück.</span><span class="sxs-lookup"><span data-stu-id="19600-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="19600-299">Verwenden Sie zum Bereitstellen von Statuscodeseiten Middleware für Statuscodeseiten.</span><span class="sxs-lookup"><span data-stu-id="19600-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="19600-300">Die Middleware wird vom Paket [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="19600-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="19600-301">Um für gängige Statuscodes einfache Handler im Textformat zu aktivieren, rufen Sie in der `Startup.Configure`-Methode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> auf:</span><span class="sxs-lookup"><span data-stu-id="19600-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="19600-302">Rufen Sie `UseStatusCodePages` vor Middleware zur Anforderungsverarbeitung auf (z.B. Middleware für statische Dateien und MVC-Middleware).</span><span class="sxs-lookup"><span data-stu-id="19600-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="19600-303">Wenn `UseStatusCodePages` nicht verwendet wird, wird beim Navigieren zu einer URL ohne Endpunkt eine browserabhängige Fehlermeldung zurückgegeben, die angibt, dass der Endpunkt nicht gefunden werden kann.</span><span class="sxs-lookup"><span data-stu-id="19600-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="19600-304">Navigieren Sie zum Beispiel zu `Home/Privacy2`.</span><span class="sxs-lookup"><span data-stu-id="19600-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="19600-305">Wenn `UseStatusCodePages` aufgerufen wird, gibt der Browser Folgendes zurück:</span><span class="sxs-lookup"><span data-stu-id="19600-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="19600-306">UseStatusCodePages mit Formatzeichenfolge</span><span class="sxs-lookup"><span data-stu-id="19600-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="19600-307">Um den Inhaltstyp und Text der Antwort anzupassen, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Inhaltstyp und eine Formatierungszeichenfolge erfordert:</span><span class="sxs-lookup"><span data-stu-id="19600-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="19600-308">UseStatusCodePages mit Lambda-Ausdruck</span><span class="sxs-lookup"><span data-stu-id="19600-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="19600-309">Um benutzerdefinierten Code für die Fehlerbehandlung und das Schreiben von Antworten anzugeben, verwenden Sie die Überladung von <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>, die einen Lambda-Ausdruck verwendet:</span><span class="sxs-lookup"><span data-stu-id="19600-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="19600-310">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="19600-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="19600-311">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>:</span><span class="sxs-lookup"><span data-stu-id="19600-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="19600-312">Sendet den Statuscode *302 Found* (Gefunden) an den Client.</span><span class="sxs-lookup"><span data-stu-id="19600-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="19600-313">Der Client wird an den in der URL-Vorlage angegebenen Standort umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="19600-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="19600-314">Die URL-Vorlage kann, wie im Beispiel gezeigt, einen `{0}`-Platzhalter für den Statuscode enthalten.</span><span class="sxs-lookup"><span data-stu-id="19600-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="19600-315">Wenn die URL-Vorlage mit einer Tilde (`~`) beginnt, wird `~` durch die Angabe für `PathBase` der App ersetzt.</span><span class="sxs-lookup"><span data-stu-id="19600-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="19600-316">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder :::no-loc(Razor):::-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="19600-316">If you point to an endpoint within the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="19600-317">Ein :::no-loc(Razor)::: Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="19600-317">For a :::no-loc(Razor)::: Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="19600-318">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="19600-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="19600-319">Den Client an einen anderen Endpunkt umleiten, in der Regel in Fällen, in denen eine andere App den Fehler verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="19600-320">Für Web-Apps gibt die Adressleiste des Browsers des Clients den umgeleiteten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="19600-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="19600-321">Den ursprünglichen Statuscode mit der anfänglichen Umleitungsantwort nicht beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="19600-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="19600-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="19600-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="19600-323">Die Erweiterungsmethode <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>:</span><span class="sxs-lookup"><span data-stu-id="19600-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="19600-324">Gibt den ursprünglichen Statuscode an den Client zurück.</span><span class="sxs-lookup"><span data-stu-id="19600-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="19600-325">Generiert den Antworttext durch die erneute Ausführung der Anforderungspipeline mithilfe eines alternativen Pfads.</span><span class="sxs-lookup"><span data-stu-id="19600-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="19600-326">Wenn Sie in der App auf einen Endpunkt verweisen, müssen Sie eine MVC-Ansicht oder :::no-loc(Razor):::-Seite für den Endpunkt erstellen.</span><span class="sxs-lookup"><span data-stu-id="19600-326">If you point to an endpoint within the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="19600-327">Stellen Sie sicher, dass `UseStatusCodePagesWithReExecute` vor `UseRouting` platziert wird, damit die Anforderung an die Statusseite umgeleitet werden kann.</span><span class="sxs-lookup"><span data-stu-id="19600-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="19600-328">Ein :::no-loc(Razor)::: Pages-Beispiel finden Sie unter *Pages/StatusCode.cshtml* in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="19600-328">For a :::no-loc(Razor)::: Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="19600-329">Diese Methode wird häufig verwendet, wenn die App Folgendes tun soll:</span><span class="sxs-lookup"><span data-stu-id="19600-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="19600-330">Die Anforderung ohne Umleitung an einen anderen Endpunkt verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="19600-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="19600-331">Für Web-Apps gibt die Adressleiste des Browsers des Clients den ursprünglich angeforderten Endpunkt wieder.</span><span class="sxs-lookup"><span data-stu-id="19600-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="19600-332">Den ursprünglichen Statuscode mit der Antwort beibehalten und zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="19600-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="19600-333">Die Vorlagen für URL und Abfragezeichenfolgen können für den Statuscode einen Platzhalter (`{0}`) enthalten.</span><span class="sxs-lookup"><span data-stu-id="19600-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="19600-334">Die URL-Vorlage muss mit einem Schrägstrich (`/`) beginnen.</span><span class="sxs-lookup"><span data-stu-id="19600-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="19600-335">Wenn Sie im Pfad einen Platzhalter verwenden, vergewissern Sie sich, dass der Endpunkt (Seite oder Controller) das Pfadsegment verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="19600-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="19600-336">Eine :::no-loc(Razor)::: Page für Fehler sollte z. B. den Wert des optionalen Pfadsegments mit der `@page`-Anweisung akzeptieren:</span><span class="sxs-lookup"><span data-stu-id="19600-336">For example, a :::no-loc(Razor)::: Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="19600-337">Der Endpunkt, der den Fehler verarbeitet, kann die ursprüngliche URL abrufen, die den Fehler generiert hat (siehe folgendes Beispiel):</span><span class="sxs-lookup"><span data-stu-id="19600-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="19600-338">Deaktivieren von Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="19600-338">Disable status code pages</span></span>

<span data-ttu-id="19600-339">Verwenden Sie das Attribut [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute), um Statuscodeseiten für einen MVC-Controller oder eine MVC-Aktionsmethode zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="19600-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="19600-340">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>, um Statuscodeseiten für bestimmte Anforderungen in der Handlermethode von :::no-loc(Razor)::: Pages oder in einem MVC-Controller zu deaktivieren:</span><span class="sxs-lookup"><span data-stu-id="19600-340">To disable status code pages for specific requests in a :::no-loc(Razor)::: Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="19600-341">Ausnahmebehandlungscode</span><span class="sxs-lookup"><span data-stu-id="19600-341">Exception-handling code</span></span>

<span data-ttu-id="19600-342">Code auf Seiten zur Ausnahmebehandlung kann Ausnahmen auslösen.</span><span class="sxs-lookup"><span data-stu-id="19600-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="19600-343">Es ist häufig empfehlenswert, wenn Produktionsfehlerseiten nur statische Inhalte aufweisen.</span><span class="sxs-lookup"><span data-stu-id="19600-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="19600-344">Antwortheader</span><span class="sxs-lookup"><span data-stu-id="19600-344">Response headers</span></span>

<span data-ttu-id="19600-345">Nachdem die Header für eine Antwort gesendet wurden:</span><span class="sxs-lookup"><span data-stu-id="19600-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="19600-346">Die App kann den Statuscode der Antwort nicht ändern.</span><span class="sxs-lookup"><span data-stu-id="19600-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="19600-347">Weder Ausnahmeseiten noch Handler können ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="19600-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="19600-348">Die Antwort muss abgeschlossen oder die Verbindung unterbrochen werden.</span><span class="sxs-lookup"><span data-stu-id="19600-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="19600-349">Sichere Ausnahmebehandlung</span><span class="sxs-lookup"><span data-stu-id="19600-349">Server exception handling</span></span>

<span data-ttu-id="19600-350">Neben der Ausnahmebehandlungslogik in Ihrer App kann die [HTTP-Serverimplementierung](xref:fundamentals/servers/index) einige Ausnahmebehandlungen durchführen.</span><span class="sxs-lookup"><span data-stu-id="19600-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="19600-351">Wenn der Server eine Ausnahme erfasst, bevor die Antwortheader gesendet werden, sendet der Server die Antwort *500 – Interner Serverfehler* ohne Antworttext.</span><span class="sxs-lookup"><span data-stu-id="19600-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="19600-352">Wenn der Server eine Ausnahme auffängt, nachdem die Antwortheader gesendet wurden, schließt der Server die Verbindung.</span><span class="sxs-lookup"><span data-stu-id="19600-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="19600-353">Anforderungen, die nicht von Ihrer App verarbeitet werden, werden vom Server verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="19600-354">Jede Ausnahme, die bei der Anforderungsverarbeitung durch den Server auftritt, wird durch die Ausnahmebehandlung des Servers verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="19600-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="19600-355">Die benutzerdefinierten Fehlerseiten der App, Middleware zur Fehlerbehandlung und Filter haben keine Auswirkungen auf dieses Verhalten.</span><span class="sxs-lookup"><span data-stu-id="19600-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="19600-356">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="19600-356">Startup exception handling</span></span>

<span data-ttu-id="19600-357">Nur auf Hostebene können Ausnahmen behandelt werden, die während des Starts einer App auftreten.</span><span class="sxs-lookup"><span data-stu-id="19600-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="19600-358">Der Host kann für das [Erfassen von Startfehlern](xref:fundamentals/host/web-host#capture-startup-errors) und [Erfassen detaillierter Fehler](xref:fundamentals/host/web-host#detailed-errors) konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="19600-359">Die Hostingebene kann nur dann für einen beim Start erfassten Fehler eine Fehlerseite anzeigen, wenn der Fehler nach der Bindung an die Hostadresse bzw. den Port auftritt.</span><span class="sxs-lookup"><span data-stu-id="19600-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="19600-360">Wenn die Bindung misslingt:</span><span class="sxs-lookup"><span data-stu-id="19600-360">If binding fails:</span></span>

* <span data-ttu-id="19600-361">Die Hostebene protokolliert eine kritische Ausnahme.</span><span class="sxs-lookup"><span data-stu-id="19600-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="19600-362">Der DotNet-Prozess stürzt ab.</span><span class="sxs-lookup"><span data-stu-id="19600-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="19600-363">Wenn der HTTP-Server [Kestrel](xref:fundamentals/servers/kestrel) heißt, wird keine Fehlerseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="19600-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="19600-364">Wenn sie auf [IIS](/iis) (oder Azure App Service) oder [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) ausgeführt wird, wird ein *Prozessfehler 502.5* vom [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zurückgegeben, wenn der Prozess nicht starten kann.</span><span class="sxs-lookup"><span data-stu-id="19600-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="19600-365">Weitere Informationen finden Sie unter <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="19600-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="19600-366">Datenbank-Fehlerseite</span><span class="sxs-lookup"><span data-stu-id="19600-366">Database error page</span></span>

<span data-ttu-id="19600-367">Die Middleware „Datenbank-Fehlerseite“ erfasst datenbankbezogene Ausnahmen, die mithilfe von Entity Framework-Migrationen aufgelöst werden können.</span><span class="sxs-lookup"><span data-stu-id="19600-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="19600-368">Wenn diese Ausnahmen auftreten, wird eine HTML-Antwort mit Details zu möglichen Aktionen zum Beheben des Problems generiert.</span><span class="sxs-lookup"><span data-stu-id="19600-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="19600-369">Diese Seite darf nur in der Entwicklungsumgebung aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="19600-370">Aktivieren Sie die Seite, indem Sie `Startup.Configure` Code hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="19600-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="19600-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> benötigt das [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/)-NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="19600-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="19600-372">Ausnahmefilter</span><span class="sxs-lookup"><span data-stu-id="19600-372">Exception filters</span></span>

<span data-ttu-id="19600-373">In MVC-Apps können Ausnahmefilter global oder auf einen Controller oder eine Aktion bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="19600-374">In :::no-loc(Razor)::: Pages-Apps können sie global oder auf ein Seitenmodell bezogen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="19600-374">In :::no-loc(Razor)::: Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="19600-375">Diese Filter verarbeiten jede nicht behandelte Ausnahme, die während der Ausführung eine Controlleraktion oder eines anderen Filters auftritt.</span><span class="sxs-lookup"><span data-stu-id="19600-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="19600-376">Weitere Informationen finden Sie unter <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="19600-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="19600-377">Ausnahmefilter eignen sich zum Auffangen von Ausnahmen, die in MVC-Aktionen auftreten. Sie sind jedoch nicht so flexibel wie Middleware für die Ausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="19600-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="19600-378">Es wird empfohlen, die Middleware zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="19600-378">We recommend using the middleware.</span></span> <span data-ttu-id="19600-379">Verwenden Sie Filter nur dann, wenn Sie für die Fehlerbehandlung auf Grundlage einer ausgewählten MVC-Aktion eine andere Strategie wählen müssen.</span><span class="sxs-lookup"><span data-stu-id="19600-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="19600-380">Modellstatusfehler</span><span class="sxs-lookup"><span data-stu-id="19600-380">Model state errors</span></span>

<span data-ttu-id="19600-381">Informationen zum Behandeln von Modellstatusfehlern finden Sie unter [Modellbindung](xref:mvc/models/model-binding) und [Modellvalidierung](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="19600-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="19600-382">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="19600-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
